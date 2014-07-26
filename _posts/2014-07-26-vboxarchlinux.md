---
layout: post
title: "在vbox中安装archlinux"
description: ""
category: 
tags: [vbox, archlinux]
---
{% include JB/setup %}


#1. 废话
- 最近发现脑容量有点不够用，于是准备用 __jekyll+github+vim+markdown__的方式写（做）博（笔）客（记）。
- 公司发的电脑预装win7系统，各种不习惯，而且cygwin带各种坑。本着不作死就不会死的心态，准备装个 __vbox+archlinux__

---

#2. 安装vbox
- 在[VirtualBox官网](https://www.virtualbox.org/wiki/Downloads)下载最新(坑)版本的安装包。

- 装Vbox就遇到坑了，你敢信？刚开始下载VirtualBox 4.3.14 for Windows hosts，安装完打开提示:
>Failed to install NtCreateSection monitor:e9 bb eb ....
>(rc=-8)

- 不知道是不是bug，我的系统是win7 64bit，认裁= =
- 试了几种版本后发现改成4.3.12版本才是正确的姿势。

---

#3. 安装arch
##3.1 下载镜像
- 在[Archlinux官网](https://www.archlinux.org/download/)下载最新镜像。

##3.2 分配虚拟电脑
- 在Vbox新建一个虚拟电脑，分配空间等等，公司配的电脑是128G ssd，这里给分配了8G。

##3.3 挂载安装
###3.3.1 键盘布局
- 默认就是us(美式键盘)，可以不用改。如果要更改可以使用命令:

  ```bash
# loadkeys layout
```

- 把layout换成键盘布局，如fr,uk等。

---
###3.3.2 分区
- 用gdisk工具分区，根目录3G，home目录5G：
  ```bash
# gdisk disk-device
```

###3.3.3 格式化
- 用以下命令格式化分区:
  ```bash
# mkfs -t ext4 /dev/partition
```

###3.3.4 网络
- 使用vbox的NAT模式，因此网格不用考虑。

###3.3.5 挂载分区
- 先将根目录挂载:
  ```bash
# mount /dev/sda1 /mnt
```
- 再挂hmoe目录:
  ```bash
# mkdir /mnt/home
# mount /dev/sda2 /mnt/home
```
- 如果有boot是单独分区也要挂载。

###3.3.6 安装基本系统
- 先修改 __etc/pacman.d/mirrorlist__选择一个较快的源，推荐：
> Server = http://lug.mtu.edu/archlinux/$repo/os/$arch  
> Server = http://mirror.umd.edu/archlinux/$repo/os/$arch  
> Server = http://mirrors.liquidweb.com/archlinux/$repo/os/$arch  
> Server = http://cosmos.cites.illinois.edu/pub/archlinux/$repo/os/$arch  

- 用pacstrap安装:
  ```bash
# pacstrap /mnt base
```

###3.3.7 配置系统 
- 用下面命令生成fstab：
  ```bash
# genfstab -p /mnt >> /mnt/etc/fstab
```

- chroot到新系统中：
  ```bash
# arch-chroot /mnt
```

- 在 __etc/hostname__中添加主机名
- 建立时区：
  ```bash
# ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

- 取消 __etc/locale.gen__中 __zh_CN.UTF-8 UTF-8__的注释，执行 __locale-gen__生成lcoale信息
- 在 __etc/locale.conf__中设置语言，例如：
  ```bash
LANG=en_US.UTF-8
```

- 执行 __passwd__设置密码

###3.3.8 安装Syslinux
- 刚开始安装Grub，装完后重启发现没网，不知道是哪坑了，于是换Syslinux。
- 由于是GPT，先安装gptfdisk
  ```bash
# pacman -S gptfdisk
```

- 再安装syslinux:
  ```bash
# pacman -S syslinux
# syslinux-install_update -iam
```

- 编缉 __boot/syslinux/syslinux.cfg__将 __/__指向正确根分区：
  ```bash
# ...
# APPEND root=/dev/sda1 rw
```

- __reboot__

#4. 

##4.1 vbox相关

##4.2 openbox

##4.3 fcitx

---