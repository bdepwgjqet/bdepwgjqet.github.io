[Velocity](http://velocity.apache.org/)是Apache的开源的基于Java的语言模板引擎。它允许用 __模板语言__引用java代码定义的对象。

关于Velocity模板语言(VTL)：
- 使用$字符开始的references（变量、属性、方法）用于得到什么；使用#字符开始的directives用于做些什么（例如执行命令）。
- 注释：单行使用##，多行使用 __#* code you want to be .. *#__。
- VTL中的属性可以作为VTL方法的缩写。例如：$a.getA()与$a.A有相同的效果。
- 语法：
  - 表达式
     - 访问javaBeans : $someBean或${someBean}
     - 读Properties : $bean.name或${bean.name}，这会访问$bean.getName()或者$bean.get("name")方法。
     - 写Properties : #set(${bean.name} = "value")，这会访问baen.setName(value)方法。
	 - 赋值 : #set($A = $B + $C)
  - 条件和循环
     - #foreach($a in $b)
	   do sth
	   #end
	 - #if($a==0)
	   doa
	   #else
	   dob
       #end
  - 反斜杠使用，例\$a
  - and(&&) or(||) not(!) 同C++
  - Velocity 允许 include 本地文件，只能在TEMPLATE_ROOT目录下。
  - Velocity 可以 parse 本地vm文件，例#parse("a.vm")
  - #stop 可以停止执行模板引擎并返回，用于debug
  - 可以定义重用的VTL，如下代码：
    #macro ( d )
    <tr><td></td></tr>
    #end
	调用方法：#d()
  	
- Velocity demo：http://www.yanyulin.info/pages/2014/03/velocity_disabuse_1.html http://m.poorren.com/apache-velocity-java/
