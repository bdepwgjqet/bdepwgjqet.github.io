---
layout: post
title: "虚拟机中搭建Git服务器Gitosis"
description: ""
category: 
tags: []
---
{% include JB/setup %}

# 虚拟机中搭建Git服务器Gitosis

Git是目前非常流行的分布式版本控制系统, 目前比较火的[Github](https://github.com/)就是一个基于Git的代码管理平台, Github提供免费托管公共仓库的功能, 而私密仓库的功能需要收费. 于是就折腾了一下在虚拟机中搭建了Git-Gitosis用来管理个人代码.

Git支持四种协议来传输数据 : SSH协议, Git协议, HTTP协议, 本地传输. 我选择了用SSH协议.

__安装openssh__

```bash
# pacman -S openssh
```

若修改端口的话(默认是22)在__/etc/ssh/sshd_config__ 中修改Port

在宿主机中ssh测试看能否正常登录服务器.(需要先将虚拟机的网络设置成桥接或Host only, 这样宿主机才能访问虚拟机)

__安装Gitosis__

archlinux在AUR中有Gitosis

```bash
~$ yaourt gitosis
```

安装完成后先在当前用户下生成ssh公钥,利用该公钥初始化gitosis

```bash
ssh-keygen -t rsa
cp ~/.ssh/id_rsa.pub /tmp/id_rsa.pub
```

创建一个git用户用于git服务

```bash
~$ sudo useradd -m git
~$ sudo passwd git
~$ su git
```

切换到git用户后初始化gitosis

```bash
~$ gitosis-init < /tmp/id_rsa.pub
```

这样在git的home目录下会有如下结构:

```bash
├── gitosis
│   └── projects.list
└── repositories
    └── gitosis-admin.git
        ├── branches
        ├── config
        ├── description
        ├── gitosis.conf
        ├── gitosis-export
        │   └── keydir
        ├── HEAD
        ├── hooks
        ├── index
        ├── info
        │   ├── exclude
        │   └── refs
        ├── objects
        └── refs
            ├── heads
            │   └── master
            └── tags
```

- repositories 是存放公共库的目录

- keydir中存放不同用户的公钥文件

切换回非git用户, 将git用户下的gitosis-admin.git clone到一个位置,用于管理项目,项目对应的成员,公钥文件等.

```bash
~$ git clone ssh://git@localhost:8090/gitosis-admin.git
```

由于是本机,故用localhost(假设ssh端口被设成8090)

会有如下结构

```bash
.
├── gitosis.conf
└── keydir
    └── bdep__@wFalcon.pub
```

gitosis.conf中内容如下

```bash
[gitosis]

[group gitosis-admin]
members = bdep__@wFalcon
writable = gitosis-admin
```

如果要增加一个项目,例如是test-example,则在该文件中加入如下内容

```bash
[group test]
members = bdep__@wFalcon bdepwgjqet@Arch
writable = test-example
```

其中bdep__@wFalcon bdepwgjqet@Arch对该项目都有读写权限

修改完后, 提交到服务器

```bash
~$ git add ./*
~$ git commit -m "add project and user"
~$ git push origin master
```

这样服务器就有了新项目的信息, __但还不会生成新项目的文件.__

这时用虚拟机中的bdep__@wFalcon新建该项目,并push到服务器

```bash
~$ mkdir test-example
~$ git add ./*
~$ git commit -m "first commit"
~$ git push origin master
```

此时在git用户目录下的git仓库repositories中就会出现test-example.git项目了.

如何实现宿主机对虚拟机中git服务的访问呢?

假设之前test-example中允许读写的用户bdepwgjqet@Arch是宿主机中的一个用户

这时要先在宿主机中用bdepwgjqet@Arch用户生成一个rsa公钥(上文中的id_rsa.pub), 将该公钥中的内容保存到虚拟机中gitosis-admin.git的keydir下, 变成如下

```bash
.
├── gitosis.conf
└── keydir
    ├── bdep__@wFalcon.pub
    └── bdepwgjqet@Arch.pub
```

然后将该变更push到服务器

```bash
~$ git add ./*
~$ git commit -m "add rsa pub key"
~$ git push origin master
```

这时候在宿主机就可以访问虚拟机中的git服务了(假设ssh端口是8090, 虚拟机桥接ip为192.168.1.124)

```bash
~$ git clone ssh://git@192.168.1.124:8090/test-example.git
```


