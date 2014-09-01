---
layout: post
title: "MapReduce"
description: ""
category: 
tags: []
---
{% include JB/setup %}

MapReduce大致流程 :

1. 对输入数据源进行切片

2. master调度worker执行map任务(一个worker负责一部分切片)

  - worker读取输入源片段

  - worker执行map任务,将任务输出保存在本地

3. master调度worker执行reduce任务

  - reduce worker读取map任务的输出作为reduce输入 

  - reduce worker执行map任务,将任务输出保存到HDFS

Map源码如下:

{% highlight java linenos %}
public static class Map extends Mapper<LongWritable, Text, Text, IntWritable> {
    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

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

- map 承担主要的处理工作

- cleanup 主要是收尾工作,如关闭文件

- run 方法提供了setup-map-cleanup的执行模板

Map在输出时 :

- 将key/value/partitioner写入到内存中

- 当内存中的数据达到一定阀值时spill到磁盘, 在spill前会先进行排序.

- 排序的规则是先进行Partitioner的排序,再进行key的字典序排序,默认是快排.

- 生成多个sipll文件时,会进行归并成一个大文件,是一个归并排序的过程.

