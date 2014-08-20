
> - [1. 引言](#1. 引言)
- [2. 环境相关](#2. 环境相关)
  - [2.1 开发环境](#2.1 开发环境)
  - [2.2 Aone相关](#2.2 Aone相关)

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
