---
layout: post
title: "在vbox中安装archlinux"
description: ""
category: 
tags: [vbox, archlinux]
---

{% include JB/setup %}

- [1. 废话](#1-废话)
- [2. 安装vbox](#2-安装vbox)
- [3. 安装arch](#3-安装arch)
  - [3.1 下载镜像](#31-下载镜像)
  - [3.2 分配虚拟电脑](#32-分配虚拟电脑)
  - [3.3 挂载安装](#33-挂载安装)
     - [3.3.1 键盘布局](#331-键盘布局)
     - [3.3.2 分区](#332-分区)
     - [3.3.3 格式化](#333-格式化)
     - [3.3.4 网络](#334-网络)
     - [3.3.5 挂载分区](#335-挂载分区)
     - [3.3.6 安装基本系统](#336-安装基本系统)
     - [3.3.7 配置系统](#337-配置系统)
     - [3.3.8 安装Syslinux](#338-安装syslinux)
- [4. 必要包安装](#4-必要包安装)
  - [4.1 vbox相关](#41-vbox相关)
  - [4.2 openbox](#42-openbox)
  - [4.3 fcitx](#43-fcitx)

---



#1. 废话
- 最近发现脑容量有点不够用，于是准备用 __jekyll+github+vim+markdown__的方式写（做）博（笔）客（记）。
- 公司发的电脑预装win7系统，各种不习惯，而且cygwin带各种坑。本着不作死就不会死的心态，准备搞个 __vbox+archlinux__。

---

#2. 安装vbox
- 在[VirtualBox官网](https://www.virtualbox.org/wiki/Downloads)下载最新(坑)版本的安装包。

- 装Vbox就遇到坑了，你敢信？刚开始下载VirtualBox 4.3.14 for Windows hosts，安装完打开提示:
>Failed to install NtCreateSection monitor:e9 bb eb ....
>(rc=-8)

- 不知道是不是bug，我的系统是win7 64bit，认栽= =
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

- __useradd__创建用户，安装配置sudo，最后 __reboot__

---

#4. 必要包安装

##4.1 vbox相关

- 先安装 __virtualbox-guest-iso__和 __virtualbox-guest-utils__两个包再载入模块:

  ```bash
# sudo pacman -S virtualbox-guest-iso virtualbox-guest-utils
# modprobe -a  vboxguest vboxsf vboxvideo
```

- 这里有个坑，加载模块时有可能提示 __Module not found__。开机提示：
>[FAILED] Failed to start Load Kernel Modules.

- 运行：

  ```bash
# systemctl --failed
```

- 排查原因发现是因为 __linux-headers__包的问题，重新安装，再用 __dkms install__安装vbox相关模块。在 __/etc/modules-load.d/__中创建 __virtualbox.conf__加入以下几行：
>vboxguest  
>vboxsf  
>vboxvideo  

- 安装 __vboxguest-hook__并把 __vboxguest__加入到 __/etc/mkinitcpio.conf__HOOKS数组中，再执行：

  ```bash
# mkinitcpio -p linux
```

- 以创建初始RAM disk。这样每次更新内核，都会重新编译vbox客户机模块。最后用以下命令开启共享服务：

  ```bash
# VBoxClient-all &
```

- 在vbox的共享文件夹创建一个永久的文件夹vbox-share（指向win7中某个文件夹），然后用以下命令挂载，便可以实现共享：

  ```bash
# sudo mkdir /mnt/vbox-share
# sudo mount -t vboxsf vbox-share /mnt/vbox-share
```

##4.2 openbox

- 以前用过KDE，GNOME，awesome。还没用过openbox， xfce。由于openbox比xfce更轻量，因此选择试试openbox。
- 安装openbox, slim，执行以下命令拷贝配置文件和启动silm的服务：

  ```bash
# mkdir -p ~/.config/openbox
# sudo cp /etc/xdg/openbox/{rc.xml,menu.xml,autostart,environment} ~/.config/openbox
# sudo systemctl enable slim.service
```

- 在 __~/.xinitrc__中写入 __exec openbox-session__。这样便可以开机启动openbox。
- 安装obconf可以方便地配置主题，安装feh设置wallpaper，将以下命令加入到 __~/.config/openbox/autostart__以永久设置wallpaper：

  ```bash
feh --bg-scale /path/to/image.file &
```

##4.3 fcitx

- 安装fcitx-im,fcitx-configtool,fcitx-table-extra，设置环境变量，在~/.xinitrc中加入：
>export GTK_IM_MODULE=xim  
>export QT_IM_MODULE=xim  
>export XMODIFIERS="@im=fcitx"  

- 启动fcitx-configtool设置输入法以及快捷键。
- 注意： export环境变量应该放在exec执行openbox之前，否则openbox没有export环境变量。如果fcitx还是无法启动，执行fcitx-diagnose排查错误。
