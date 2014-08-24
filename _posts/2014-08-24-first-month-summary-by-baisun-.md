---
layout: post
title: "第一个月月报 白隼"
description: ""
category: 
tags: []
---
{% include JB/setup %}


> - [1. 引言](#1. 引言)
- [2. 环境相关](#2. 环境相关)
  - [2.1 开发环境](#2.1 开发环境)
  - [2.2 Aone相关](#2.2 Aone相关)
  - [2.3 log查看](#2.3 log查看)
- [3. 技术相关](#3. 技术相关)
  - [3.1 Maven](#3.1 Maven)
     - [3.1.1 Maven安装](#3.1.1 Maven安装)
     - [3.1.2 Maven配置](#3.1.2 Maven配置)
  - [3.2 Velocity](#3.2 Velocity)
  - [3.3 Spring](#3.3 Spring)
     - [3.3.1 Bean相关](#3.3.1 Bean相关)
     - [3.3.2 AOP相关](#3.3.2 AOP相关)
     - [3.3.3 数据库相关](#3.3.3 数据库相关)
  - [3.4 Webx](#3.4 Webx)
     - [3.4.1 Webx目录结构](#3.4.1 Webx目录结构)
     - [3.4.2 Webx执行流程](#3.4.2 Webx执行流程)
  - [3.5 HSF](#3.4 HSF)
     - [3.5.1 如何对外发布](#3.5.1 如何对外发布)
     - [3.5.2 如何调用HSF服务](#3.5.2 如何调用HSF服务)
  - [3.6 Tair](#3.6 Tair)
  - [3.7 iBatis](#3.7 iBatis)

---

<h1 id="月报-白隼" align="center">入职一个月月报-白隼</h1>

<h2 id="1. 引言">1. 引言</h2>

  入职已经近一个月，从入职前对Java的Web开发基本未了解过，到现在了解Spring架构的一部分知识，熟悉日常开发，项目上线，从中学到了不少东西，包括淘系的一些技术以及中间件。通过以月报的形式归纳总结，加深理解。同时给以后入职的同学一些参考。如有理解不到位或者不正确的地方，多谢指正。


<h2 id="2. 环境相关">2. 环境相关</h2>

<h3 id="2.1 开发环境">2.1 开发环境</h3>

必须工具：

- 项目管理工具 ： Maven （2.2.1以上版本）

- 版本控制工具 ： TortoiseSVN

- IDE ： Eclipse 

- JDK 1.6

- 切换hosts ： SwitchHosts （项目的测试、日常、线上绑的地址不同，需要切换）

- 安全终端模拟软件：Xshell （或者SecureCRT,putty） 

其它工具：

- 模拟linux终端：Cygwin （如果你也习惯用linux下的命令，里面的软件包比较旧，会有些坑，比如用Gem安装Ruby的相关包时，常用的都还正常，Cygwin有自己的包管理工具apt-cyg）

- Eclipse插件 ： Veloeclipse （Eclipse下velocity的语法高亮）

- Eclipse插件 ： Vrapper （Eclipse中嵌入Vim的功能）

- 编辑器 ： Vim for windows （如果你也是Vim党）

- 数据库管理工具 ： Navicat


<h3 id="2.2 Aone相关">2.2 Aone相关</h3>

项目的创建、测试、发布都是在[Aone](http://aone.alibaba-inc.com/aone2/myaone)上进行。

打开以下地址申请SVN权限：

> http://aone.alibaba-inc.com/aone2/scm/svnRightApplyNew/0

申请完找师兄审批。

在Aone上可以新建日常或者新建项目，新建日常主要是解决一些日常的小需求。新建项目主要是进行项目开发。

从新建日常（项目）到发布的流程大致如下（IDE用Eclipse）：

- 新建日常（项目）.

- 申请变更&分支.

- Check out 分支代码：

  ```bash
~ $ svn co [分支地址]
```

- 进入项目目录，生成项目文件:

  ```bash
~ $ mvn eclipse:clean
~ $ mvn eclipse:eclipse -DdownloadSources=true
```

- 在Eclipse中导入项目，完成编码，Check in代码：

  ```bash
~ $ svn up
~ $ svn commit -m "[注释]"
```

- 在Aone上创建测试环境，进行测试.

- 完成测试后，提交集成.

- 进入我的集成，在日常环境上布署，让测试的同学进行测试.

- 日常环境没问题后，在预发环境进行布署.

- 进入正式环境，发布。

<h3 id="2.3 log查看">2.3 log查看</h3>

申请对应机器(测试，日常，预发，线上)帐号：

> http://aops.alibaba-inc.com/workflow/account/new/ 

远程到服务器上：

- 测试，日常机器：用Xshell（SecureCRT,putty）远程到服务器上。

- 预发，线上机器：先远程到 __跳板机__（login1.cm3.alibaba.org），再远程到线上机器。

日志打在 __/home/admin/[应用名]/logs/__目录下。

<h2 id="3. 技术相关">3. 技术相关</h2>


<h3 id="3.1 Maven">3.1 Maven</h3>

Apache Maven，是一个 __软件项目管理__及 __自动构建__工具，可以方便的编译代码、进行依赖管理、管理二进制库。它能帮助我们标准化构建过程。在Maven之前，十个项目可能有十种构建方式；有了Maven之后，所有项目的构建命令都是简单一致的，这极大地避免了不必要的学习成本，而且有利于促进项目团队的标准化。Maven还为全世界的Java开发者提供了一个免费的中央仓库，在其中几乎可以找到任何的流行开源类库。通过一些Maven的衍生工具（如Nexus），还能对其进行快速地搜索。只要定位了坐标，Maven就能够帮我们自动下载，省去了手工劳动。

使用Maven只需要配置POM以及简单的几行命令，便可以实现项目自动构建，效率远远高于传统的手工构建工作。

对Maven项目管理的理解：Maven的思想类似于Linux下的包管理工具，前者是将开发所需要的lib库统一由服务器管理，后者是由服务器统一管理应用软件。通过Maven既定的规则，可以准确地找到所需的依赖类库，能很好地解决版本问题。


<h4 id="3.1.1 Maven安装">3.1.1 Maven安装</h4>

__Windows环境下的安装__

需要先安装JDK（1.4以上），下载 Maven：

>http://maven.apache.org/download.html 

下载完后解压，配置环境变量：

- M2_HOME ： 解压后Maven的根目录。

- PATH中加入%M2_HOME%/bin 

终端下输入 __mnv -v__查看Maven的版本以确认是否安装成功。

Maven的目录结构：

```bash
./bin
|-- m2.conf
|-- mvn
|-- mvn.bat
|-- mvnDebug
|-- mvnDebug.bat
`-- mvnyjp
./boot
`-- plexus-classworlds-2.5.1.jar
./conf
|-- logging
|   `-- simplelogger.properties
|-- settings.xml
`-- settings.xml.bak
./lib
|-- ext
|   `-- README.txt
`-- wagon-provider-api-2.6.jar
./LICENSE [error opening dir]
./NOTICE [error opening dir]
./README.txt [error opening dir]
```

- Bin中主要是mvn运行时的脚本。

- Boot中只有一个文件，是一个类加载器框架，Maven用这个框架加载自己的类库 

- Conf目录下主要是Settings.xml用于配置Maven。

- Lib下包含了所有的Maven运行时所需的类库（以上目录中没有列出全部类库）。

<h4 id="3.1.2 Maven配置">3.1.2 Maven配置</h4>

__配置Settings.xml__

找师兄要配置好的Settings.xml作为参考。

配置本地Repository位置：

```XML
<localRepository>D:/Maven_Repository</localRepository>
```

Repository位置修改过也需要在Eclipse中配置：

配置server：


配置镜像库：


配置Profile和激活的Profile.

__配置pom.xml__

POM(Project Object Model,项目对象模型) 定义了项目的的基本信息，用于描述项目如何构建，声明项目依赖，等等.就像Make的Makefile一样. 

基本的一些配置元素:

- project : pom.xml的根元素,主要声明一些POM相关的命名空间及xsd元素.

- groupId : 对于淘宝的项目来说，groupId 通常是产品线的代号，如
com.taobao.uic，com.taobao.shopcenter 等，注意groupId 一定要保护产品线的信息，不要设置为com.taobao，这不便于划分项目。

- artifactId : 这个是Maven项目中模块的名称标识，对于artifactId来说，通常需要包含产品线的名称，因为最终的jar名称通常为artifactId+version+”.jar”，如果artifactId名称非常普通，那么会导致jar文件名称冲突。这里还有一个问题就是目录名称和artifactId 是否要一样？实际的开发中，不需要一样。artifactId 包括产品线的前缀，如uic-client，如果uic-client属于项目中的一个模块，那么我们只需 要将目录名称设置为client，而不是uic-client，因为目录是在项目这个环境中，不需要添加前缀，加啦反而给理解造成困难。

- version : 项目的版本号,一般由三部分组成,如1.1.0.

- packaging : 打包类型，通常为pom，jar，war和ear，pom打包类型表示抽象概念，没有实际的产出，如项目的根pom都是pom打包类型。jar和war是我们常用的，分别是普通的jar应用和web app应用。ear打包类型表示企业应用，如包含多个jar，多个war等。在淘宝正常都是pom，jar和war，ear我们很少涉及。

- name和description表示项目的名称和描述，这里的名称要和artifactId区分开。

- url : 项目的介绍.

- scm : svn的信息，设置一下项目的svn url

- developers : 主要是项目的成员信息.

__POM的依赖管理配置:__

项目需要依赖第三方包,需要使用dependency元素配置pom.

例如:

```XML
<dependencies>
	<dependency>
		<groupId>junit</groupId>
		<artifactId>junit</artifactId> 
		<version>4.0</version> 
		<scope>test</scope> 
	</dependency>
	...
<dependencies>
```

其中scope是指所依赖的开发包生效的范围，如编译期，测试期和运行期:

- compile：这个是缺省的生效范围。compile scope会在编译期和执行器的同时生效。

- provided：编译期生效，包括test，但是在执行期不会生效，会有容器或者jdk提供。 

- runtime：执行期生效，编译期不需要。

- test：和compile类似，但是只针对测试代码有效。

- system：和provided类似，但是provided scope依赖还是要从maven仓库中找的，而system scope不是从库中查找，需要开发人员指定.

参考自:

> http://maven.apache.org/maven-model/maven.html

<h3 id="3.2 Velocity">3.2 Velocity</h3>

[Velocity](http://velocity.apache.org/)是Apache的开源的基于Java的语言模板引擎。它允许用 __模板语言__引用java代码定义的对象。

关于Velocity模板语言(VTL)：

- 使用$字符开始的references（变量、属性、方法）用于得到什么；使用#字符开始的directives用于做些什么（例如执行命令）。

- 注释：单行使用##，多行使用 __#* code you want to be .. *#__。

- VTL中的属性可以作为VTL方法的缩写。例如：$a.getA()与$a.A有相同的效果。

__语法：__

表达式:

- 访问javaBeans : $someBean或${someBean}

- 读Properties : $bean.name或${bean.name}，这会访问$bean.getName()或者$bean.get("name")方法。

- 写Properties : #set(${bean.name} = "value")，这会访问baen.setName(value)方法。

- 赋值 : #set($A = $B + $C)

条件和循环:

```velocity
#foreach($a in $b)
	do sth
#end

#if($a==0)
	doa
#else
	dob
#end
```

velocity一些用法:

- 用$!的方式强制把不存在的变量显示为空白,例如$!valuename

- 输出特殊字符用反斜杠，例\$a

- and(&&) or(||) not(!) 同C++

- Velocity 允许 include 本地文件，只能在TEMPLATE_ROOT目录下。

- Velocity 可以 parse 本地vm文件，例#parse("a.vm")

- \#stop 可以停止执行模板引擎并返回，用于debug

定义重用的VTL，如下代码：

```velocity
#macro ( d )
<tr><td></td></tr>
#end
```

调用方法：

```velocity
#d()
```
  	
Velocity demo：

> http://www.yanyulin.info/pages/2014/03/velocity_disabuse_1.html 

> http://m.poorren.com/apache-velocity-java/

<h3 id="3.3 Spring">3.3 Spring</h3>

<h4 id="3.3.1 Bean相关">3.3.1 Bean相关</h4>

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


<h4 id="3.3.2 AOP相关">3.3.2 AOP相关</h4>

相关定义：

 - __面向切面编程（AOP）__：为了解决横切关注点（分布于应用中多处的功能）与它们所影响的对象之间的解耦，将横切关注点与业务逻辑相分离。

 - __切面：__横切关注点被模块化为特殊的类，是通知和切点的结合。

 - __通知：__切面的工作被称为通知。

 - __连接点：__连接点是在应用执行过程中能够插入切面的一个点。这个点可以是调用方法时、抛出异常时、甚至是修改一个字段时。

 - __切点：__切面不需要通知应用的所有连接点，切点有助于缩小切面所通知连接点的范围。

 - __引入：__引入允许我们向现有的类添加新方法或属性。

 - __织入：__织入是将切面应用到目标对象来创建新的代理对象的过程。


__声明切面的方法：__

有如下Audience类：

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

{% highlight XML linenos %}
<!-- 顶层aop配置元素 -->
<aop:config>

	<!-- 定义切面 -->
	<aop:aspect ref="audience">
		
		<!-- 定义一个命名切点,消除冗余切点定义 -->
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

__声明环绕通知__、 __为通知传递参数__以及 __通过切面引入新功能__的肉容见__Spring In Action 3__的P96-P101 。

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


<h4 id="3.3.3 数据库相关">3.3.3 数据库相关</h4>

__数据库相关__

Spring 通过 __数据访问对象(DAO, Data access object)提供数据读取和写入到数据库中.DAO 以接口的方式发布功能,应用程序的其它部分通过接口进行访问.

Spring 将数据访问过程中固定的和可变的部分明确划分为两个不同的类:

- 模板 : 管理过程中固定的部分(事务控制,管理资源,处理异常).

- 回调 : 处理自定义的数据访问代码(创建语句,绑定参数,整理结果).

__配置数据源__

数据源 : 存储所有建立数据库连接信息.

连接池 : 数据库连接池负责分配、管理和释放数据库连接，它允许应用程序重复使用一个现有的数据库连接，而再不是重新建立一个. 

Spring 提供了在Spring上下文中配置数据源Bean的多种方式

- 通过JDBC驱动程序定义的数据源

- 通过JNDI查找的数据源

- 连接池的数据源

我接触的项目中通过使用DBCP在Spring中配置数据源连接池, DBCP包含了多个提供连接池功能的数据源,其中BasicDataSource是最常用的,因为它易于在Spring中配置.

可以这样配置一个简单的BasicDataSource

```XML
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">

	<!-- 指定JBDC驱动类的全限定类名 -->
	<property name="driverClassName" value="org.hsqldb.jdbcDriver" />

	<!-- 设置数据库的JDBC URL -->
	<property name="url" value="jdbc:hsqldb:hsql://localhost/appname/appname" />

	<!-- 用户名密码 -->
	<property name="username" value="uname" />
	<property name="password" value="passwd" />

	<!-- 启动时创建的连接数量 -->
	<property name="initialSize" value="5" />

	<!-- 同一时间可从池中分配的最多连接数 0表示无限制 -->
	<property name="maxActive" value="10" />
</bean>
```

使用传统的JDBC进行数据库操作完成简单的任务就需要大量的代码, 这些代码很多是重复的, 通过使用JDBC模板可以简化JDBC代码. Spring 为JDBC提供了3个模板类:

- JdbcTemplate : 最基本的Spring JDBC模板.

- NamedParameterJdbcTemplate : 使用该模板类执行查询时,可以将查询值以命名参数的形式绑定到SQL中

- SimpleJdbcTemplate : 利用java5的特性简化JDBC模板使用.

Spring 2.5中NamedParameterJdbcTemplate所具备的命名参数功能被合并到SimpleJdbcTemplate中,所以选择SimpleJdbcTemplate.

设置DataSource : 

```XML
<bean id="jdbcTemplate"
	class="org.springframework.jdbc.core.simple.SimpleJdbcTemplate">
	<constructor-arg ref="dataSource" />
```

这样便能让SimpleJdbcTemplate正常工作.

将jdbcTemplate装配到DAO中并使用它访问数据库 :

```java
public class JdbcAppDAO implements appDAO {

	private SimpleJdbcTemplate jdbcTemplate;

	public void setJdbcTemplate(SimpleJdbcTemplate jdbcTemplate) {
		this.jdbcTemplate = jdbcTemplate;
	}
}
```

装配JdbcSpitterDAO的jcbdTemplate属性:

```XML
<bean id="appDAO"
	class="org.tec.app.JdbcAppDAO">
	<property name="jdbcTemplate ref="jdbcTemplate" />
</bean>
```

一个数据库update实现(使用命名参数)可以是这样:

```java
public addUser(User user) {
	Map<String, Object> params = new HashMap<String, Object>();
	params.put("username", user.getUsername());
	params.put("password", user.getPassword());
	params.put("email", user.getEmail());

	jdbcTemplate.update(SQL_INSERT_USER, params);
}
```

一个数据库query实现可以是这样:

```java
public User getUserById(long id) {
	return jdbcTemplate.queryForObject(
		SQL_SELECT_USER_BY_ID,
		new ParameterizedRowMapper<User>() {
			public User mapRow(ResultSet rs, int rowNum) throws SQLException {
				User user = new user();
				user.setId(rs.getLong(1));
				user.setUsername(rs.getString(2));
				user.setPassword(rs.getString(3));
				user.setEmail(rs.getString(4));
				return user;
			}
		},
		id
	);
}
```

<h3 id="3.4 Webx">3.4 Webx</h3>

Webx是一套基于Java Servlet API的通用Web框架。在Ali内部被广泛使用。

Webx框架可以划分成三个大层次:

- SpringExt：基于Spring，提供扩展组件的能力。它是整个框架的基础。

- Webx Framework：基于Servlet API，提供基础的服务，例如：初始化Spring、初始化日志、接收请求、错误处理、开发模式等。Webx Framework只和servlet及spring相关 —— 它不关心Web框架中常见的一些服务，例如Action处理、表单处理、模板渲染等。因此，事实上，你可以用Webx Framework来创建多种风格的Web框架。

- Webx Turbine：基于Webx Framework，实现具体的网页功能，例如：Action处理、表单处理、模板渲染等。

<h4 id="3.4.1 Webx目录结构">3.4.1 Webx目录结构</h4>

一个简单的Webx项目的目录结构:

```bash
./pom.xml
./src
`-- main
    |-- java
    |   `-- com
    |       `-- alibaba
    |           `-- webx
    |               `-- tutorial1
    |                   `-- app1
    |                       |-- module
    |                       |   |-- action
    |                       |   |   `-- RegisterAction.java
    |                       |   `-- screen
    |                       |       |-- form
    |                       |       |   `-- Welcome.java
    |                       |       |-- list
    |                       |       |   `-- Default.java
    |                       |       |-- multievent
    |                       |       |   |-- SayHello1.java
    |                       |       |   `-- SayHello2.java
    |                       |       `-- simple
    |                       |           |-- Count.java
    |                       |           |-- Download.java
    |                       |           |-- SayHi.java
    |                       |           `-- SayHiImage.java
    |                       `-- Visitor.java
    `-- webapp
        |-- app1
        |   `-- templates
        |       |-- layout
        |       |   `-- default.vm
        |       `-- screen
        |           |-- form
        |           |   |-- register.vm
        |           |   `-- welcome.vm
        |           |-- index.vm
        |           `-- list
        |               |-- asHtml.vm
        |               |-- asJson.vm
        |               `-- asXml.vm
        |-- common
        |   `-- templates
        |       |-- layout
        |       |   `-- default.vm
        |       |-- macros.vm
        |       `-- screen
        |           `-- error.vm
        `-- WEB-INF
            |-- app1
            |   `-- form.xml
            |-- common
            |   |-- pipeline.xml
            |   |-- pipeline-exception.xml
            |   |-- resources.xml
            |   |-- uris.xml
            |   |-- webx-component.xml
            |   `-- webx-component-and-root.xml
            |-- logback.xml
            |-- web.xml
            |-- webx.xml
            `-- webx-app1.xml
```

src/main目录下:

- java目录下主要放置基本编程模块.

- webapp目录下主要放置配置文件和模板文件.

webapp目录下:

- templates中主要放置模板文件
  - layout放置整个页面的布局的模板文件
  - control放置一些公用的小页面,比如页头页尾
  - screen放置主体的内容

- WEB-INF放置所有的配置文件
  - webx.xml : 配置webx所用到的所有srevices
  - pipeline.xml : webx框架的总控文件
  - log4j.xml : 日志的配置文件

java目录下主要是Modules编程模块:

- Screen目录下 : 处理页面显示逻辑的mdoule

- Control目录下 : 和Screen类似,可以被别的screen或loyaut引用

- Action目录下 : 处理表单提交的mudole

<h4 id="3.4.2 Webx执行流程">3.4.2 Webx执行流程</h4>

__访问一个页面webx的执行流程__

例如当访问show.htm面页时:

1. webx会在相同目录下查找show.vm模板.

2. 在对应目录下查找screen类show(Default,TemplateScreen),如果找不到则查找括号中的screen类.

3. 执行screen类中的execute方法,并渲染screen模板.

__表单验证的流程__

在vm模板中有如下配置:

```velocity
<form action="action.htm" method="post">
	<input type="hidden" name="event_submit_do_upload" value="anything" />
    <input type="hidden" name="action" value="resign/upload_action" />
</form>
```

这样每次提交表单后,都会调用UploadAction.doUpload方法, 再访问action.htm.

webx框架的详细介绍 :

> http://openwebx.org/docs/Webx3_Guide_Book.html#webx.form


<h3 id="3.5 HSF">3.5 HSF</h3>

HSF全称为High-Speed Service Framework，旨在为淘宝的应用提供一个分布式的服务框架，HSF从分布式应用层面以及统一的发布/调用方式层面为大家提供支持，从而可以很容易的开发分布式的应用以及提供或使用公用功能模块，而不用考虑分布式领域中的各种细节技术，例如远程通讯、性能损耗、调用的透明化、同步/异步调用方式的实现等等问题。HSF通过ConfigServer联接Consumer和Provider,将Provider提供的Spring Bean供Consumer调用.

HSF从1.4.2以后支持在Jboss或Tomcat中部署运行.

Jboss 环境:

- 下载淘宝官方认可的JBoss 4.2.2；

- 下载HSF V1.4.8发行版；

- 下载完毕后解压放入jboss 4.2.2的server/default/deploy下,这样Jboss会在启动时自动部署HSF服务。

下载地址:

> http://hsf.taobao.net/hsfversion/

<h4 id="3.5.1 如何对外发布">3.5.1 如何对外发布</h4>

需求 : 已有一个名称为HelloWorld的Spring Bean，此Bean实现的接口为com.taobao.hsf.test.HelloWorld，现需让其他功能模块能远程调用此Spring Bean.

首先增加一个Spring Bean XML :

{% highlight XML linenos %}
<beans>

 <bean class="com.taobao.hsf.app.spring.util.HSFSpringProviderBean">

   <!--  serviceInterface 必须配置 [String]，为服务对外提供的接口 -->
   <property name="serviceInterface">
     <value>com.taobao.hsf.test.HelloWorld</value>
   </property>

   <!--   target 必须配置 [ref]，为需要发布为HSF服务的spring bean id -->
   <property name="target">
     <ref bean="HelloWorld"/>
   </property>

   <!--    serviceVersion 可选配置 [String]，含义为服务的版本，默认为1.0.0 -->
   <property name="serviceVersion">
     <value>1.1.0</value>不要有空白字符/回车换行
   </property>

   <!--    serviceName 推荐配置 [String]，含义为服务的名称，便于管理，默认为null -->
   <property name="serviceName">
     <value>HelloWorldService</value>
   </property>

   <!--    serviceDesc 可选配置 [String]，含义为服务的描述信息，便于管理，默认为null -->
   <property name="serviceDesc">
     <value>HelloWorldService providered by HSF</value>
   </property>

   <!--    serviceGroup 可选配置 [String]，含义为服务所属的组别，相当于此机器所在的VIP，默认为HSF -->
   <property name="serviceGroup">
      <value>HSF</value>
   </property>

   <!--    supportAsynCall 可选配置 [true|false]，含义为标识此服务是否支持持久可靠的异步调用
       默认值为false，也就是不支持持久可靠的异步调用，但仍然支持非持久可靠的oneway、future以及callback方式的异步调用 -->
   <property name="supportAsynCall">
     <value>false</value>
   </property>

   <!--    clientTimeout 可选配置 [int]，含义为客户端调用此服务时的超时时间，单位为ms，默认为3000ms -->
   <property name="clientTimeout">
     <value>3000</value>
   </property>

   <!--    clientIdleTimeout 可选配置 [int]，含义为客户端连接空闲的超时时间，单位为s，默认为600 -->
   <property name="clientIdleTimeout">
     <value>60</value>
   </property>

   <!--    serializeType 可选配置 [String(hessian|java)]，含义为序列化类型，默认为hessian -->
   <property name="serializeType">
     <value>java</value>
   </property>

   <!--    methodToInjectConsumerIp 可选配置，含义为注入调用端IP的方法，这样业务服务也可以得知是哪个IP在调用，该方法的参数必须为String -->
   <property name="methodToInjectConsumerIp">
     <value>setConsumerIP</value>
   </property>

   <!--    methodSpecials为可选配置，含义为为方法单独配置超时时间，这样接口中的方法可以采用不同的超时时间 -->
   <property name="methodSpecials">
     <list>
       <bean class="com.taobao.hsf.model.metadata.MethodSpecial">
          <property name="methodName" value="sum" />
          <property name="clientTimeout" value="10000" />
       </bean>
     </list>
   </property>
 </bean>
{% endhighlight %}

将此XML加入到Spring加载的applicationContext文件中.

按项目正常方式打包部署,启动Jboss,此HelloWorld就可被远程以HSF服务的方式调用了.

<h4 id="3.5.2 如何调用HSF服务">3.5.2 如何调用HSF服务</h4>

需求 : 在Spring中远程调用一个接口为com.taobao.hsf.test.HelloWorld，版本为1.1.0的服务，需调用此服务的Spring bean名称定为CallHelloWorld.

首先增加一个Spring Bean XML:

{% highlight XML linenos %}
<beans>

 <bean id="CallHelloWorld" class="com.taobao.hsf.app.spring.util.HSFSpringConsumerBean">

   <!--  interfaceName 必须配置 [String]，为需要调用的服务的接口 -->
   <property name="interfaceName">
     <value>com.taobao.hsf.test.HelloWorld</value>
   </property>

   <!--  version 可选配置 [String]，含义为需要调用的服务的版本，默认为1.0.0 -->
   <property name="version">
     <value>1.1.0</value>
   </property>

   <!--  group 可选配置 [String]，含义为需要调用的服务所在的组，默认为HSF -->
   <property name="group">
     <value>HSF</value>
   </property>

   <!--  methodSpecials 可选配置，含义为为方法单独配置超时时间，这样接口中的方法可以采用不同的超时时间 -->
   <property name="methodSpecials">
     <list>
       <bean class="com.taobao.hsf.model.metadata.MethodSpecial">
         <property name="methodName" value="sum" />
         <property name="clientTimeout" value="10000" />
       </bean>
     </list>
   </property>

     <!-- target为可选配置[String]，含义为需调用的服务的地址和端口
     主要用于单元测试环境和-Dhsf.run.mode=0的开发环境中，在运行环境下，此属性将无效，而是采用配置中心推送回来的目标服务地址信息 -->
   <property name="target">
       <value>10.1.6.57:12200?_TIMEOUT=1000</value>
   </property>

	<!-- asyncallMethods为可选配置[List]，含义为调用此服务时需要采用异步调用的方法名列表以及异步调用的方式 默认为空集合，即所有方法都采用同步调用.  格式为： name:方法名;type:异步调用类型; -->

   <property name="asyncallMethods">
     <list>
       <value>name:save</value>
     </list>
   </property>

     <!-- 设置回调的处理器，此处理器不用实现HSF的任何接口，方法遵循以下约定即可
     public void ${name}_callback(Object invokeContext, Object appResponse, Throwable t)，其中${name}即为发起调用的远程服务的方法名 -->
   <property name="callbackHandler" ref="PlugServiceCallbackHandler" />

   <!-- 设置传递上下文的方式为接口方式，即在发起调用时先调用接口上的setInvokeContext方法传入上下文对象 -->
   <property name="interfaceMethodToAttachContext" value="setInvokeContext" />

   <!-- 设置传递上下文的方式为ThreadLocal对象方式，即在发起调用时通过一个ThreadLocal对象来设置上下文对象，接口方式和ThreadLocal对象方式选其一即可 -->
   <property name="invokeContextThreadLocal" ref="invokeContextThreadLocal" /> 
 </bean>

</beans> 
{% endhighlight %}


参考:

> http://baike.corp.taobao.com/index.php/HSF 

<h3 id="3.6 Tair">3.6 Tair</h3>

tair 是淘宝自己开发的一个分布式 key/value 存储引擎. tair 分为持久化和非持久化两种使用方式.

- 非持久化的 tair : 可以看成是一个分布式缓存.

- 持久化的 tair : 将数据存放于磁盘中.

__tair 的总体结构__

tair由一个中心控制节点(config sever)和一系列的服务节点(data sever)组成.

- config server : 负责管理所有的data server, 维护data server的状态信息. 它是控制点, 而且是单点, 采用一主一备的形式来保证其可靠性.

- data server 对外提供各种数据服务, 并以心跳的形式将自身状况汇报给config server. 所有的 data server 地位都是等价的. 

__算法__

tair的分布采用一致性哈希算法, 对于所有的key, 分到Q个桶中, 桶是负载均衡和数据迁移的基本单位. config server 根据一定的策略把每个桶指派到不同的data server上.

单调性保证 : 一致性哈希算法同时将上述 __key__和 __桶__用哈希函数哈希,这样key和桶都位于哈希环上的某一个位置. 假设有Q个桶, 则哈希环被分成Q段, 只要将每个桶之前的一段key交给这个桶. 当我们增加一个桶时, 只会影响增加桶后面一个桶的key分配.其它桶不并会受到影响.

均衡性 : 通过引入虚拟节点的方式,对每一个桶计算多个Hash值,选取这些Hash值中比较均匀分布的值作为虚拟节点,这样每个节点处理的Key便会相对比较均衡.

__存储引擎__

非持久化

- mdb : 类似memcached, 支持key/value及分级key缓存, 支持使用share memory

- rdb : 支持key/value以及复杂数据结构缓存.

持久化

- ldb : 使用google开源leveldb, 支持key/value以及分级key存储,数据排序

<h3 id="3.7 iBatis">3.7 iBatis</h3>

iBatis是一个基于SQL映射支持Java和.NET的持久层框架,现已被Google托管,改名为MyBatis.

iBatis提供的持久层框架包括SQL Maps和DAO.

使用SQL Map能够大大减少访问关系数据库的代码.SQL Map使用简单的XML配置文件将Java Bean映射成SQL语句.

SQL Map使用XML配置文件统一配置不同的属性,包括DataSource的详细信息.一个配置文件例子:

```XML
<sqlMapConfig>
	<properties resource="examples/sqlmap/maps/SqlMapConfigExample.properties " />

	<settings
		cacheModelsEnabled="true"
		enhancementEnabled="true"
		lazyLoadingEnabled="true"
		maxRequests="32"
		maxSessions="10"
		maxTransactions="5"
		useStatementNamespaces="false"
	/>

	<typeAlias alias="order" type="testdomain.Order"/>

	<transactionManager type="JDBC" >
		<dataSource type="SIMPLE">
			<property name="JDBC.Driver" value="${driver}"/>
			<property name="JDBC.ConnectionURL" value="${url}"/>
			<property name="JDBC.Username" value="${username}"/>
			<property name="JDBC.Password" value="${password}"/>
			<property name="JDBC.DefaultAutoCommit" value="true" />
			<property name="Pool.MaximumActiveConnections" value="10"/>
			<property name="Pool.MaximumIdleConnections" value="5"/>
			<property name="Pool.MaximumCheckoutTime" value="120000"/>
			<property name="Pool.TimeToWait" value="500"/>
			<property name="Pool.PingQuery" value="select 1 from ACCOUNT"/>
			<property name="Pool.PingEnabled" value="false"/>
			<property name="Pool.PingConnectionsOlderThan" value="1"/>
			<property name="Pool.PingConnectionsNotUsedFor" value="1"/>
		</dataSource>
	</transactionManager>

	<sqlMap resource="examples/sqlmap/maps/Person.xml" />

</sqlMapConfig>
```

各元素说明:

- properties元素 : 用于在配置文件中使用标准的Java属性文件（name＝value）。这样做后，在属性文件中定义的属性可以作为变量在SQL Map配置文件及其包含的所有SQL Map映射文件中引用。例如，如果属性文件中包含属性： __driver=org.hsqldb.jdbcDriver__则SQL Map配置文件及其每个映射文件都可以使用占位符${driver}来代表值org.hsqldb.jdbcDriver.

- setting 元素 : 用于配置和优化SqlMapClient实例的各选项. 

- typeAlias : 为一个通常较长的、全限定类名指定一个较短的别名. 

- transactionManager : 为SQL Map配置事务管理服务.

- dataSource : transactionManager的一部分,为SQL Map数据源设置了一系列参数. 

- sqlMap元素 : 用于包括SQL Map映射文件和其他的SQL Map配置文件. 

__SQL Map XML 映射文件__

SQL Map的核心概念是Mapped Statement。Mapped Statement可以使用任意的SQL语句，并拥有Parameter Map（输入）和Result Map（输出）。如果是简单情况，Mapped Statement可以使用Java类来作为parameter和result。Mapped Statement也可以使用缓存模型，在内存中缓存常用的数据。

一个SQL Map XML映射文件可以包含任意多个Mapped Statement，Parameter Map和Result Map。

Mapped Statement的结构如下所示：

```XML
<statement id=”statementName”
	[parameterClass=”some.class.Name”]
	[resultClass=”some.class.Name”]
	[parameterMap=”nameOfParameterMap”]
	[resultMap=”nameOfResultMap”]
	[cacheModel=”nameOfCache”]
>
	select * from PRODUCT where PRD_ID = [?|#propertyName#]
	order by [$simpleDynamic$]
</statement>
```

statement类型:

- insert : 对应数据库的 insert 操作，该操作返回本次操作插入记录的主键值。

- update : 对应数据库的 update 操作，该操作返回被更新的记录个数。

- delete : 对应数据库的 delete 操作，该操作返回被删除的记录个数。

- select : 对应数据库的 select 操作，该操作返回特定的 POJO 或 对象。

- procedure : 对应数据库存储过程。

- statement : 类型最为通用，可以代替以上所有的类型。但由于缺乏操作直观性故不推荐。

属性:

- __parameterClass__属性的值是Java类的全限定名（即包括类的包名）。parameterClass属性是可选的，但强烈建议使用。它的目的是限制输入参数的类型为指定的Java类，并优化框架的性能。如果您使用parameterMap，则没有必要使用parameterClass属性。例如，如果要只允许Java类examples.domain.Product作为输入参数，可以这样：

  ```XML
<statement id=”statementName” parameterClass=” examples.domain.Product”>
insert into PRODUCT values (#id#, #description#, #price#)
</statement>
```

- 属性 __parameterMap__的值等于一个预先定义的<parameterMap>元素的名称.比较少用到.

- resultClass属性的值是Java类的全限定名（即包括类的包名）。resultClass属性可以让你指定一个Java类，根据ResultSetMetaData将其自动映射到JDBC的ResultSet。只要是Java Bean的属性名称和ResultSet的列名匹配，属性自动赋值给列值。这使得查询mapped statement变得很短。例如: 

  ```XML
<statement id="getPerson" parameterClass=”int” resultClass="examples.domain.Person">
	SELECT 
		PER_ID as id,
		PER_FIRST_NAME as firstName,
		PER_LAST_NAME as lastName,
		PER_BIRTH_DATE as birthDate,
		PER_WEIGHT_KG as weightInKilograms,
		PER_HEIGHT_M as heightInMeters
	FROM 
		PERSON
	WHERE 
		PER_ID = #value#
</statement>
```

- resultMap是最常用和最重要的属性。ResultMap属性的值等于预先定义的resultMap元素的name属性值.

  ResultMap的结构:

  ```XML
<resultMap id=”resultMapName” class=”some.domain.Class” [extends=”parent-resultMap”]>
	<result property=”propertyName” column=”COLUMN_NAME”
		[columnIndex=”1”] [javaType=”int”] [jdbcType=”NUMERIC”]
		[nullValue=”-999999”] [select=”someOtherStatement”]
	/>
	<result ……/>
	<result ……/>
	<result ……/>
</resultMap>
```

  例子:

  ```XML
<resultMap id=”get-product-result” class=”com.ibatis.example.Product”>
	<result property=”id” column=”PRD_ID”/>
	<result property=”description” column=”PRD_DESCRIPTION”/>
</resultMap>

<statement id=”getProduct” resultMap=”get-product-result”>
	select * from PRODUCT
</statement>
```

  这样查询语句得到的ResultSet被映射成Product对象. 

__动态Mapped Statement__

SQL Map API使用和mapped statement非常相似的结构,可以避免一系列if-else条件语句和一连串字符串连接.

例子:

```XML
<select id="dynamicGetAccountList"
	cacheModel="account-cache"
	resultMap="account-result" >

	select * from ACCOUNT

	<isGreaterThan prepend="and" property="id" compareValue="0">
		where ACC_ID = #id#
	</isGreaterThan>

	order by ACC_LAST_NAME
</select>
```

元素说明:

- prepend : 可被覆盖的SQL语句组成部分，添加在语句的前面（可选）

- property : 被比较的属性（必选）

- compareProperty : 另一个用于和前者比较的属性（必选或选择compareValue）

- compareValue : 用于比较的值（必选或选择compareProperty） 

- \<isEqual\> 比较属性值和静态值或另一个属性值是否相等。

- \<isNotEqual\> 比较属性值和静态值或另一个属性值是否不相等。

- \<isGreaterThan\> 比较属性值是否大于静态值或另一个属性值。

- \<isGreaterEqual\> 比较属性值是否大于等于静态值或另一个属性值。

- \<isLessThan\> 比较属性值是否小于静态值或另一个属性值。

- \<isLessEqual\> 比较属性值是否小于等于静态值或另一个属性值。

- \<isPropertyAvailable\> 检查是否存在该属性（存在parameter bean的属性）。

- \<isNotPropertyAvailable\> 检查是否不存在该属性（不存在parameter bean的属性）。

- \<isNull\> 检查属性是否为null。对于int,long,double这种非Object的默认不为null.

- \<isNotNull\> 检查属性是否不为null。

- \<isEmpty\> 检查Collection.size()的值，属性的String或String.valueOf()值,是否为null或空（“”或size() \< 1）。

- \<isNotEmpty\> 检查Collection.size()的值，属性的String或String.valueOf()值,是否不为null或不为空（“”或size() \> 0）。

