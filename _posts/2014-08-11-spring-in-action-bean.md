---
layout: post
title: "Spring in Action 笔记（一、Bean相关）"
description: ""
category: 
tags: []
---
{% include JB/setup %}

Spring的主要特性：依赖注入（DI）和面向切面编程（AOP）。  

- 依赖注入：通过依赖注入可以将紧密耦合的代码转换成松散耦合的代码，解决紧密耦合的代码难以理解，测试困难等问题，有助于应用对象之间的解耦。  

- 面向切面编程（AOP）__：为了解决横切关注点与它们所影响的对象之间的解耦，将横切关注点与业务逻辑相分离。  

Spring容器之Bean工厂：  

- 由org.springframework.beans.factory.BeanFactory接口定义。

- Bean 是一种特殊的Java类，Bean的属性由方法定义。用set开始的方法可以写属性，用get开始的方法可以读属性。set方法没有返回类型，只能有一个参数，参数的数据类型必须和属性的数据类型一致。get方法对应类型并且没有参数。

Spring 配置（可以使用XML或Java注解的方式）：

- XML方式：在XML文件中<beans>标签内放置所有Spring配置信息，包括<bean>声明（beans不是唯一命名空间）。

- Java注解的方式：

Spring中装配bean的例子：

Spring中通过\<bean\>创建一个对象。如下：

```XML
<bean id="beanid" class="*.Oneclass" >
```  

beanid实际上会这样被创建：

```java
new *.Oneclass();
```  

用XmlBeanFactory，ClassPathXmlApplicationContext，FileSystemXmlApplicationContext，XmlWebApplicationContext容器装配，例如： 
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
  
关于依赖注入：

1.通过构造函数注入,用<constructor-arg>标签。
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

2.用setter方法注入，用<property>标签（可以用<ref bean>引用其它bean的值），例如下:

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


__最小化装配__

目的：为了简化XML的配置

方式：

- 自动装配(autowiring)，减少甚至消除<property>和<constructor-arg>元素，让spring自动识别如何装配Bean的依赖关系。
- 自动检测(autodiscovery)，让Spring自动识别哪些类需要被装配成Bean，减少<bean>元素的使用。

自动装配Bean属性的方式：

- byName：把与Bean的属性具有相同名字（或ID)的其他Bean自动装配到该Bean的对应属性中。若没有名字相匹配的Bean，则不装配。
- byType：把与Bean的属性具有相同类型的其它Bean自动装配到该Bean的对应属性中。若没有名字相匹配的Bean，则不装配。
- constructor：把与Bean的构造器入参具有相同类型的其他Bean自动装配到Bean构造器的对应入参中。
- autodetect：先尝试用cnostructor自动装配，若失败，再用ByType。

__byName自动装配的例子：__

假设有一个id为A的Bean显式装配是这样的：

```XML
<bean id="A" class="com.i.j.k">
	<property name="X" value="V">
	<property name="B" ref="C">
</bean>
```

id为B的Bean已经被定义：

```XML
<bean id="B" class="com.u.v.w"/>
```

那么利用byName方式A可以这样装配：

```XML
<bean id="A" class="com.i.j.k" autowire="byName">
	<property name="X" value="V">
</bean>
```

byType自动装配的例子类似byName，只不过byType是寻找有相同属性类型的Bean，要求只能有一个相匹配，如果找到多个，可以使用bean的pirmary属性，会优先选择pirmary属性为ture的Bean，否则抛出异常（primary属性默认设置是ture）。

__constructor自动装配例子：__

要装配这样一个Bean:

```XML
<bean id="A" class="com.x.y.z" autowire="constructor"/>
```

Spring会审视z的构造器，并尝试在Spring配置中寻找匹配z中某一个构造器的所有入参的Bean。如果有多个，处理方式与byType相同。

默认自动装配：在\<beans\>标签中设置default-autowire属性。

```XML
<beans default-autowire="constructor">
</beans>
```

__混合装配__： 可以混合使用自动装配和显式装配，这样可以避免自动装配中找到多个抛出异常的情况。
- PS：不能混合使用constructor自动装配和\<constructor-arg\>元素，这是因为使用constructor时必须让Spring自动装配构造器的所有入参。

__注解装配：__

使用\<context:annotation-config\>元素开启注解装配：

```XML
<beans default-autowire="constructor">
<context:annotation-config \>
</beans>
```

方式:

- Spring自带的@Atutowired注解
- JSR-330的@Inject注解
- JSR-250的@Resource注解

__@autowired方式例子：__

直接标注属性：

```Java
@Autowired
private ClassA classA;
</beans>
```

或者标注构造器：

```Java
@Autowired
public setClassA(ClassA classA) {
	this.classA = classA;
}
</beans>
```

这样子Spring都会用byType方法进行自动装配。

__避免装配失败抛出异常__：

- 可以使用@Autowired(required=false)，这样装配失败，该元素会被设为null。
- 可以使用@Qualifier("beanid")指定id=beanid的Bean进行装配。（可以创建自定义的限定器，如下会把B装配给c）

  ```Java
@Qualifier
public interface A {
}

@A
public class B implements C {
}

@Autowired
@A
private C c;
```

__@Inject方式：__与Autowired一样，@Inject没有required属性。

__@Resource方式：__作用相当于@Autowired，@Autowired按byType自动注入，而@Resource默认按byName自动注入（推荐用）。


__用java代替XML配置：__

首先用少量的XML配置启用Java配置：


```XML
<beans>
	<context:component-scan 
		base-package="com.a.b.c" />
</beans>
```

这会让Spring在com.a.b.c包内晒找使用@Configuration注解所标注的所有类。

使用@Configuration注解的Java类，代替\<beans\>元素

{% highlight Java linenos %}
package com.a.b.c;

import org.springframework.context.annotation.Configuration;

@Configuration

public class c {
	@Bean
	public BeanA beanA() {
		return new ClassA();
	}

	//注入的方式
	@Bean 
	public BeanA beanA100() {
		return new classA(100);
	}
}
{% endhighlight %}

这样会创建并反回一个ClassA的实例对象，声明为一个id为beanA的classA Bean。


