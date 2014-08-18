---
layout: post
title: "Spring in Action 笔记（二、AOP相关）"
description: ""
category: 
tags: []
---
{% include JB/setup %}

相关定义：
 - __面向切面编程（AOP）__：为了解决横切关注点（分布于应用中多处的功能）与它们所影响的对象之间的解耦，将横切关注点与业务逻辑相分离。

 - __切面：__横切关注点被模块化为特殊的类，是通知和切点的结合。

 - __通知：__切面的工作被称为通知。

 - __连接点：__连接点是在应用执行过程中能够插入切面的一个点。这个点可以是调用方法时、抛出异常时、甚至是修改一个字段时。

 - __切点：__切面不需要通知应用的所有连接点，切点有助于缩小切面所通知连接点的范围。

 - __引入：__引入允许我们向现有的类添加新方法或属性。

 - __织入：__织入是将切面应用到目标对象来创建新的代理对象的过程。


__声明切面的方法：__

有一个类：

{% highlight Java linenos %}
package com.springinaction.springdo1;

public class Audience {

	//表演前
	public void takeSeats() {
		;
	}
	
	//表演前
	public void turnOffCellPhones() {
		;
	}

	//表演后
	public void applaud() {
		;
	}

	//表演失败后
	public void demandRefund() {
		;
	}
}
{% endhighlight %}

把它装配成一个Bean：

```XML
<bean id="audience" class="com.springaction.springdo1.Audience" />
```

通过如下方式把Bean变成一个切面：

{% highlight Java linenos %}
<!-- 顶层aop配置元素 -->
<aop:config>

	<!-- 定义切面 -->
	<aop:aspect ref="audience">
		
		<!-- 定义一个命名切点，消除冗余切点定义 -->
		<aop:pointcut id="performance" expression="execution(* com.springinaction.springdo1.Peformer.perform(..))" />

		<!-- 定义AOP前置通知 -->
		<aop:before pointcut-ref="performance" method="takeSeats" />
		<aop:before pointcut="performance" method="turnOffCellPhones" />

		<!-- 定义AOP after-throwing通知 -->
		<aop:after-returning pointcut-ref="performance" method="applaud" />

		<!-- 定义AOP after-throwing通知 -->
		<aop:after-throwing pointcut-ref="performance" method="demandRefund" />
	</aop:aspect>

</aop:config>
{% endhighlight %}

execution()用于编写切点，上例中*号表示不关心方法的返回值，(..)标识切点选择任意的perform()方法，无论该方法的入参是什么。

__声明环绕通知__及 __为通知传递参数__及 __通过切面引入新功能__见Spring In Action 3 P96-P101 。

__通过注解标注一个切面的例子：__

{% highlight Java linenos %}
package com.springinaction.springdo1;

@Aspect
public class Audience {

	//定义切点
	@Pointcut("execution(* com.springinaction.springido1.Performer.perform(..))")
	public void performance() {
	}

	//表演前
	@Before("performance()")
	public void takeSeats() {
		;
	}
	
	//表演前
	@Before("performance()")
	public void turnOffCellPhones() {
		;
	}

	//表演后
	@AfterReturning("performance()")
	public void applaud() {
		;
	}

	//表演失败后
	@AfterThrowing("performance()")
	public void demandRefund() {
		;
	}
}
{% endhighlight %}
