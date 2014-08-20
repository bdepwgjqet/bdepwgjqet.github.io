
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

新建日常（项目）：

申请变更&分支：

Check out 分支代码：

```bash
~ $ svn co [分支地址]
```

进入项目目录，生成项目文件：

```bash
~ $ mvn eclipse:clean
~ $ mvn eclipse:eclipse -DdownloadSources=true
```

在Eclipse中导入项目，完成编码，Check in代码：

```bash
~ $ svn up
~ $ svn commit -m "[注释]"
```

在Aone上创建测试环境，进行测试：

完成测试后，提交集成：

进入我的集成，在日常环境上布署，让测试的同学进行测试：

日常环境没问题后，在预发环境进行布署：

进入正式环境，发布。

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

```XML
<server>                                  
	<id>snapshots</id>                  
	<username>snapshotsAdmin</username>          
	<password>123456</password>
</server>                            
<server> 
	<id>releases</id> 
	<username>admin</username>
	<password>taobao123456789</password>
</server>
```

配置镜像库：

```XML
<mirror>
	<id>tbmirror</id>
	<mirrorOf>central</mirrorOf>
	<name>taobao mirror</name>
	<url>http://mvnrepo.taobao.ali.com/mvn/repository</url>
</mirror>
<mirror>
	<id>tbmirror-snapshots</id>
	<mirrorOf>snapshots</mirrorOf>
	<name>taobao mirror snapshots</name>
	<url>http://mvnrepo.taobao.ali.com/mvn/repository</url>
</mirror>
```

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


