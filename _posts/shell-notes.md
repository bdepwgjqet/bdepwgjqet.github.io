---
layout: post
title: "Shell notes"
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

__crontab中的输出配置__

crontab中经常配置脚本输出为:

```bash
> /dev/null 2>&1
```

- 前半段表示重定向到空设备文件

- 系统默认0是stdin标准输入文件,1是stdout标准输出,2是标准错误输出

- 后半段表示2的输出重定向到1

- 整句就是将标准输出和标准错误输出都重定向到空设备文件

__点命令__

source(点命令)可以把文件中的命令在当前终端顺序执行

- 用于重新执行刚修改的初始化文件

- 执行cd到某个目录的shell脚本, 若不使用source命令直接执行脚本, 这样执行终端会产生一个子shell, 子shell执行完脚本, 子shell中切换了目录, 子shell执行完后变量和操作都会被全部回收, 终端上的路径就不会被改变.使用source命令可以达到这一目的.


__grep__

grep的格式如下:

```bash
grep [OPTIONS] PATTERN [FILE...]
```

OPTION中常用的:

- -r(recursive) : 递归查找子目录(基本shell命令通用)

- -v : 不匹配的

- -n : 显示查找东西所在的行号

- --exclude : 排除查找的文件

- -i : 忽略大小写

- -An : 显示后(after) n行

- -Bn : 显示前(before) n行

一个粟子:

```bash
grep -rn -B1 -A1 --exclude=*.{iml,ipr,iws} "\-SNAPSHOT" ./
```

__PS1环境变量__

PS1是用于设置命令提示符的环境变量

- \d : 表示日期

- \t : 表示时间 HH:mm:ss

- \u : 当前用户名称

- \h : 主机名称中第一个'.'之前的部分

- \H : 完整主机名称

我的一个配置:

```bash
PS1="\[$(tput bold)\]\n\[\033[1;37m\]\342\224\214[$(if [[ ${EUID} == 0 ]]; then echo '\[\033[01;31m\]\h'; else echo '\[$(tput setaf 1)\]\u\[$(tput setaf 7)\]@\[$(tput setaf 4)\]\h'; fi)\[\033[1;37m\]]\342\224\200(\$(if [[ \$? == 0 ]]; then echo \"\[\033[01;32m\]\342\234\223\"; else echo \"\[\033[01;31m\]\342\234\227\"; fi)\[\033[1;37m\])\342\224\200[\[$(tput setaf 6)\]\t \d\[\033[1;37m\]]\342\224\200[\[\033[1;32m\]\$(ls -1 | wc -l | sed 's: ::g') files, \$(ls -lah | grep -m 1 total | sed 's/total //')b\[\033[1;37m\]]\[\033[1;37m\]\n\342\224\224\342\224\200(\[\033[1;32m\]\w\[\033[1;37m\])\342\224\200> \[\033[0m\]"
```

一个可以生成部分功能PS1的网站:

> https://www.kirsle.net/wizards/ps1.html 
