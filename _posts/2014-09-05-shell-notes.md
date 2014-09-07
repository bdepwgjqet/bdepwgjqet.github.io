---
layout: post
title: "shell 笔记持续更新"
description: ""
category: 
tags: []
---
{% include JB/setup %}

__多行注释 :__

```bash
:<<BLOCK'
....注释内容
'BLOCK
```

shell本身并不自带注释多行的命令,以上命令把输入重定义到前面的命令，但是:是空命令，所以就相当于注释。

__比较运算符 :__

|运算符          |描述                              |示例                                |
|----------------|:---------------------------------|:-----------------------------------|
|文件比较运算符  |                                  |                                    |
|-e filename     |如果 filename 存在，则为真        |[ -e /var/log/syslog ]              |
|-d filename     |如果 filename 为目录，则为真	    |[ -d /tmp/mydir ]                   |
|-f filename     |如果 filename 为常规文件，则为真	|[ -f /usr/bin/grep ]                |
|-L filename     |如果 filename 为符号链接，则为真	|[ -L /usr/bin/grep ]                |
|-r filename     |如果 filename 可读，则为真	    |[ -r /var/log/syslog ]              |
|-w filename     |如果 filename 可写，则为真	    |[ -w /var/mytmp.txt ]               |
|-x filename     |如果 filename 可执行，则为真	    |[ -L /usr/bin/grep ]                |
|f1 -nt f2	     |如果 f1 比 f2 新，则为真	        |[ /tmp/services -nt /etc/services ] |
|f1 -ot f2	     |如果 f1 比 f2 旧，则为真	        |[ /boot/test -ot /tmp/test ]        |
|字符串比较运算符|                                  |                                    |
|-z string	     |如果 string 长度为零，则为真	    |[ -z "$var" ]                       |
|-n string	     |如果 string 长度非零，则为真	    |[ -n "$var" ]                       |
|s1 = s2	     |如果 s1 与 s2 相同，则为真	    |[ "$str" = "tmp str hi" ]           |
|s1 != s2	     |如果 s1 与 s2 不同，则为真	    |[ "$str" != "tmp str hi" ]          |
|算术比较运算符  |                                  |                                    |
|n1 -eq n2       |等于                              |[ 3 -eq $num ]                      |
|n1 -ne n2       |不等于                            |[ 3 -ne $num ]                      |
|n1 -lt n2       |小于                              |[ 3 -lt $num ]                      |
|n1 -le n2       |小于或等于                        |[ 3 -le $num ]                      |
|n1 -gt n2       |大于                              |[ 3 -gt $num ]                      |
|n1 -ge n2       |大于或等于                        |[ 3 -ge $num ]                      |

__获取脚本参数$i__

假设i=1, shell中没有提供类似$($i)这样的表达式取得参数$1, 如果你知道有这样的表达式, 发送到我的邮件

> bdepwgjqet@gmail.com

万分感谢!

可以通过将脚本参数先存入数组, 再从数组中取值的方式解决:

```bash
NUM=$#

for (( i=1; i<=$NUM; i++ )); do
	param[$i]=$1
	shift
done

pos=1
pos_val=${param[$pos]}
```

