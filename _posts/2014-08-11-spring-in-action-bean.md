---
layout: post
title: "Spring in Action 笔记（一、bean相关）"
description: ""
category: 
tags: []
---
{% include JB/setup %}

Spring的主要特性：依赖注入（DI）和面向切面编程（AOP）。
- 依赖注入：通过依赖注入可以将紧密耦合的代码转换成松散耦合的代码，解决紧密耦合的代码难以理解，测试困难等问题。
- 面向切面编程：[...]  

Spring容器之Bean工厂：
- 由org.springframework.beans.factory.BeanFactory接口定义。
- Bean 是一种特殊的Java类，Bean的属性由方法定义。用set开始的方法可以写属性，用get开始的方法可以读属性。set方法没有返回类型，只能有一个参数，参数的数据类型必须和属性的数据类型一致。get方法对应类型并且没有参数。

Spring 配置（可以使用XML或Java注解的方式）：
- XML方式：在XML文件中<beans>标签内放置所有Spring配置信息，包括<bean>声明（beans不是唯一命名空间）。
- Java注解的方式：

Spring中装配bean的例子：
- Spring中通过\<bean\>创建一个对象。如下：

  ```XML
	<bean id="beanid" class="*.Oneclass" >
```  

  beanid实际上会这样被创建：

  ```java
new *.Oneclass();
```  

- 用XmlBeanFactory，ClassPathXmlApplicationContext，FileSystemXmlApplicationContext，XmlWebApplicationContext容器装配，例如： 
  先定义接口：

  ```java
package com.springExample;
public interface Actioner {
	void action() throws ActionException;
}
```  

  声名一个Bean：

{% highlight java linenos %}
package com.springExample;

public class Man implements Actioner {

	private int cnt = 2;
	private String name;
	private List<String> friendsList;
	private Map<String,String> friendToTime;
	private Set<String> friendsSet;
	private boolean isHealthy;

	public Man() {}

	public Man(int cnt, List<String> friendsList, Map<String,String> friendToTime, Set<String> friendsSet boolean isHealthy) {
		this.cnt = cnt;
		this.friendsList = friendsList;
		this.friendToTime = friendToTime;
		this.friendsSet = friendsSet;
		this.isHealthy = isHealthy;
	}

	public void action() throws ActionException {
		System.out.println("do "+cnt+" action");
	}

}
{% endhighlight %}

  XML配置：

  ```XML
<bean id="Mike" class="com.springExample.Man" />
```

  用ClassPathXmlApplicationContext加载Spring上下文：

```java
ApplicationContext ctx = new ClassPathXmlApplicationContext(com/springExample/spring-Example.xml);
Actioner actioner = (Actioner) ctx.getBean("Mike");
actioner.action();
```  

  执行后会显示：do 2 action.
  
- 关于依赖注入：

 - 1.通过构造函数注入,用<constructor-arg>标签。
上例Mike的其它信息可以用<constructor-arg>通过以下方式注入（该标签可以用ref引用其它bean）：

{% highlight XML linenos %}
<bean id="Mike" class="com.springExample.Man">  
   <constructor-arg value="12" index="0" >  
   </constructor-arg>  

   <constructor-arg value="Mike Tom" index="1" >  
   </constructor-arg>  
	 
   <constructor-arg index="2">  
	 <list>  
		<value>Maydy</value>  
		<value>Tony</value>  
	 </list>  
   </constructor-arg>  

   <constructor-arg index="3">  
	   <map>  
		  <entry key="Maydy" value="20010801"></entry>  
		  <entry key="Tony" value="20020901"></entry>  
	   </map>  
   </constructor-arg>  
	 
   <constructor-arg index="4">  
	  <set>  
		 <value>Maydy</value>  
		 <value>Tony</value>  
	  </set>  
   </constructor-arg>  
  
   <constructor-arg index="5" value="1">  
   </constructor-arg>  
</bean>  
{% endhighlight %}

 - 2.用setter方法注入，用<property>标签（可以用<ref bean>引用其它bean的值），例如下:

{% highlight XML linenos %}
<bean id="Mike" class="com.springExample.Man">  
	<property name = "cnt">  
		<value>12</value>  
	</property>  
  
	<property name = "name">  
		<value>Mike Tom</value>  
	</property>

	<property name = "friendsList">
		<list>
			<value>Maydy<value>
			<value>Tony<value>
		</list>
	</property>

	<property name = "friendsToTime">
		<map>
			<entry key = "Maydy">
				<value>20080801</value>
			</entry>
		</map>
		<map>
			<entry key = "Tony">
				<value>20080901</value>
			</entry>
		</map>
	</property>

	<property name = "friendsSet">
		<set>
			<value>Maydy<value>
			<value>Tony<value>
		</set>
	</property>

	<property name = "isHealthy">
		<value>1</value>
	</property>
</bean>
{% endhighlight %}
