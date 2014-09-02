---
layout: post
title: "MapReduce"
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
