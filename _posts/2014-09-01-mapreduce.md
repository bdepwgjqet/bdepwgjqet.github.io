---
layout: post
title: "MapReduce笔记"
description: ""
category: 
tags: []
---
{% include JB/setup %}

MapReduce大致流程 :

- 对输入数据源进行切片

- master调度worker执行map任务(一个worker负责一部分切片)

  - worker读取输入源片段

  - worker执行map任务,将任务输出保存在本地

- master调度worker执行reduce任务

  - reduce worker读取map任务的输出作为reduce输入 

  - reduce worker执行map任务,将任务输出保存到HDFS

Mapper源码如下:

{% highlight java linenos %}
public class Mapper<KEYIN, VALUEIN, KEYOUT, VALUEOUT> {

    /**
     * Called once at the beginning of the task.
     */
    protected void setup(Context context
    				   ) throws IOException, InterruptedException {
    // NOTHING
    }
         
    /**
     * Called once for each key/value pair in the input split. Most applications
     * should override this, but the default is the identity function.
     */
    @SuppressWarnings("unchecked")
    protected void map(KEYIN key, VALUEIN value, 
    				   Context context) throws IOException, InterruptedException {
      context.write((KEYOUT) key, (VALUEOUT) value);
    }

    /**
     * Called once at the end of the task.
     */
    protected void cleanup(Context context
    					   ) throws IOException, InterruptedException {
      // NOTHING
    }

    /**
     * Expert users can override this method for more complete control over the
     * execution of the Mapper.
     * @param context
     * @throws IOException
     */
    public void run(Context context) throws IOException, InterruptedException {
      setup(context);
      while (context.nextKeyValue()) {
    	map(context.getCurrentKey(), context.getCurrentValue(), context);
      }
      cleanup(context);
    }
} 
{% endhighlight %}

从源可以看出Mapper有setup(), map(), cleanup(), run()四个方法.

- setup 主要进行一些map前的准备工作

- map 承担主要的处理工作,输入为key/value,上下文context

- cleanup 主要是收尾工作,如关闭文件

- run 方法提供了setup-map-cleanup的执行模板

Map在输出时 :

- 将key/value/partitioner写入到内存中

- 当内存中的数据达到一定阀值时spill到磁盘, 在spill前会先进行排序.

- 排序的规则是先进行Partitioner的排序,再进行key的字典序排序,默认是快排.

- 生成多个sipll文件时,会进行归并成一个大文件,是一个归并排序的过程.


Reducer源码如下:

{% highlight java linenos %}
public class Reducer<KEYIN,VALUEIN,KEYOUT,VALUEOUT> {

    /**
     * Called once at the start of the task.
     */
    protected void setup(Context context
                         ) throws IOException, InterruptedException {
      // NOTHING
    }

    /**
     * This method is called once for each key. Most applications will define
     * their reduce class by overriding this method. The default implementation
     * is an identity function.
     */
    @SuppressWarnings("unchecked")
    protected void reduce(KEYIN key, Iterable<VALUEIN> values, Context context
                          ) throws IOException, InterruptedException {
        for(VALUEIN value: values) {
            context.write((KEYOUT) key, (VALUEOUT) value);
        }
    }

    /**
     * Called once at the end of the task.
     */
    protected void cleanup(Context context
                           ) throws IOException, InterruptedException {
      // NOTHING
    }

    /**
     * Advanced application writers can use the 
     * {@link #run(org.apache.hadoop.mapreduce.Reducer.Context)} method to
     * control how the reduce task works.
     */
    public void run(Context context) throws IOException, InterruptedException {
        setup(context);
        while (context.nextKey()) {
            reduce(context.getCurrentKey(), context.getValues(), context);
        }
        cleanup(context);
    }
}
{% endhighlight %}

从源可以看出Reducer也有setup(), reduce(), cleanup(), run()四个方法.

- setup 主要进行一些reduce前的准备工作

- reduce 承担主要的处理工作,输入为key/(value集合),上下文context

- cleanup 主要是收尾工作,如关闭文件

- run 方法提供了setup-reduce-cleanup的执行模板

Reduce在输入时的shuffle过程:

1. 拷贝 : ReduceTask有一个线程和TaskTracker联系，之后TaskTracker和JobTracker联系，获取MapTask完成事件, ReduceTask会创建和MapTask数目相等的拷贝线程，用于拷贝MapTask的输出数据,当有新的MapTask完成事件时,Reduce拷贝线程从指定的机器上通过http的形式进行拷贝.当数据拷贝的时候，分两种情况，当数据量小的时候就会写入内存当中，当数据量大的时候就会写入磁盘当中，这些工作分别由两个线程完成.

2. 归并 : 所有的数据都来自不同的机器，所以有多个文件，这些文件需要归并成一个文件，在拷贝文件的时候就会进行归并动作

Reduce输出过程 :

- 根据客户端设置的OutputFormat中getRecordWriter()方法得到RecordWriter

- 通过OutputFormat和RecordWriter将结果输出到临时文件中

- 和TaskTracker进行通信，TaskTracker和JobTracker进行通信，然后JobTracker返回commit的指令，Reduce进行commit，将临时结果文件重命名成最终的文件

- kill掉其他的TaskAttempt

一个MapReduce例子:

需求: 

- 输入 : 输入文件有两个字段, 第一个字段由多个词组组成,词之间用ASCII码0x02分割,第二个字段是一个时间戳,字段之间用ASCII码0x01分割. 输入给定一个整型参数,表示小时数.

- 输出 : 统计时间戳在\{当前系统时间-输入小时数\}的时间戳之后的词出现的词频数, 并且按词频倒序输出.

例如(假设现在系统时间戳为4600):

- 输入 :

> nice perfect nice za za za 1001
> nice good 999

参数为1(小时)

- 输出 :

> za 3
> nice 2
> perfect 1

大致方法 : MapReduce的排序都是在Map端进行,因此可以起两个Job, 第一个Job进行词频统记,第二个Job以第一个Job的输出作为输入, 第二个Job在Map端进行key/value反转,这时会以value进行排序(重写comparator比较器),第二个Job在Reduce端再进行key/value反转,就能保证最后以value倒序输出.


词频统计Map:

{% highlight java linenos %}
public class CountMap extends Mapper<LongWritable, Text, Text, IntWritable> {
	private final static IntWritable one = new IntWritable(1);
	private Text word = new Text();

	@Override
	public void map(LongWritable key, Text value, Context context)
			throws IOException, InterruptedException {

		String[] sline = value.toString().split(String.valueOf((char) 1));
		String[] tline = sline[0].split(String.valueOf((char) 2));

		try {
			Date currentDate = new Date(System.currentTimeMillis());
			long curDate = currentDate.getTime();
			long timeStamp = Long.parseLong(sline[sline.length - 1]);

			Configuration conf = context.getConfiguration();
			long preTimeTag = Long.parseLong(conf.get("preTimeTag"));
			long preTime = preTimeTag * 3600 * 1000;

			if (timeStamp >= curDate - preTime) {
				for (int i = 0; i < tline.length; i++) {
					word.set(tline[i]);
					context.write(word, one);
				}
			}
		} catch (Exception e) {
			System.err.println("CountMap err");
		}

	}
}
{% endhighlight %}

词频统计Reduce:

{% highlight java linenos %}
public class CountReduce extends Reducer<Text, IntWritable, Text, IntWritable> {

	@Override
	public void reduce(Text key, Iterable<IntWritable> values, Context context)
			throws IOException, InterruptedException {
		int sum = 0;
		for (IntWritable val : values) {
			sum += val.get();
		}
		context.write(key, new IntWritable(sum));
	}
}
{% endhighlight %}

反转value/key Map:

{% highlight java linenos %}
public class SortMap extends Mapper<LongWritable, Text, IntWritable, Text> {
	IntWritable mapKey = new IntWritable();
	private Text mapValue = new Text();

	@Override
	public void map(LongWritable key, Text value, Context context)
			throws IOException, InterruptedException {

		String[] sline = value.toString().split(String.valueOf('\t'));

		try {
			mapKey.set(Integer.parseInt(sline[1]));
			mapValue.set(sline[0]);
			context.write(mapKey, mapValue);

		} catch (Exception e) {
			System.err.println("SortMap err");
		}
	}
}
{% endhighlight %}


反转value/key Reduce:

{% highlight java linenos %}
public class SortReduce extends Reducer<IntWritable, Text, Text, IntWritable> {

	@Override
	public void reduce(IntWritable key, Iterable<Text> values, Context context)
			throws IOException, InterruptedException {
		for (Text text : values) {
			context.write(text, key);
		}
	}
}
{% endhighlight %}

比较器实现:

{% highlight java linenos %}
public class DescendingIntComparable extends WritableComparator {

	public DescendingIntComparable() {
		super(IntWritable.class);
	}

	@SuppressWarnings("rawtypes")
	@Override
	public int compare(WritableComparable a, WritableComparable b) {
		return super.compare(a, b) * (-1);
	}

	@Override
	public int compare(byte[] b1, int s1, int l1, byte[] b2, int s2, int l2) {

		Integer v1 = ByteBuffer.wrap(b1, s1, l1).getInt();
		Integer v2 = ByteBuffer.wrap(b2, s2, l2).getInt();

		return v1.compareTo(v2) * (-1);
	}

}
{% endhighlight %}

最后的主程序:
{% highlight java linenos %}
public class WordCount {

	public static int run(String[] args) throws Exception {
		Configuration conf = new Configuration();
		conf.set("preTimeTag", args[2]);

		Path tmpDir = new Path(new String("wordcount-tmpdir-"
				+ System.currentTimeMillis()));

		Job job = new Job(conf, "word count");

		job.setOutputKeyClass(Text.class);
		job.setOutputValueClass(IntWritable.class);

		job.setMapperClass(CountMap.class);
		job.setReducerClass(CountReduce.class);

		job.setInputFormatClass(TextInputFormat.class);
		job.setOutputFormatClass(TextOutputFormat.class);

		FileInputFormat.addInputPath(job, new Path(args[0]));
		FileOutputFormat.setOutputPath(job, tmpDir);

		job.waitForCompletion(true);

		Job sortJob = new Job(conf, "count sort");

		sortJob.setOutputKeyClass(IntWritable.class);
		sortJob.setOutputValueClass(Text.class);

		sortJob.setMapperClass(SortMap.class);
		sortJob.setReducerClass(SortReduce.class);

		sortJob.setInputFormatClass(TextInputFormat.class);
		sortJob.setOutputFormatClass(TextOutputFormat.class);

		sortJob.setSortComparatorClass(DescendingIntComparable.class);

		FileInputFormat.addInputPath(sortJob, tmpDir);
		FileOutputFormat.setOutputPath(sortJob, new Path(args[1]));

		sortJob.waitForCompletion(true);

		return 0;
	}

	public static void main(String[] args) throws Exception {
		if (args.length != 3) {
			System.err.println("err Parameters : input output prehours");
			System.exit(-1);
		}

		int exitRes = run(args);
		System.exit(exitRes);
	}
}
{% endhighlight %}
