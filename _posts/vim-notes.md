---
layout: post
title: "Vim notes"
description: ""
category: 
tags: []
---
{% include JB/setup %}

__ctags :__

安装ctags

到scr目录执行 :

```bash
~ $ ctags -R
```

生成tags文件

用vim编辑文件,在vim中运行:

```bash
:set tags=/parth/to/your/tags
```

将tags文件加入到vim中

这样

- 在函数的调用处按\<C-\]\>光标会自动跳到函数的定义处

- 在函数的定义处按\<C-F\>光标会跳回被调用的地方

__TagList :__

安装TagList

在vimrc中添加:

```bash
let Tlist_Show_One_File=1
let Tlist_Exit_OnlyWindow=1
```

vim中执行

```bash
:Tlist
```
打开TagList窗口

__WinManager :__

安装WinManager

在vimrc中添加:

```bash
g:winManagerWindowLayout='FileExplorer|TagList'
nmap wm :WMToggle<cr>
```

在vim中按wm可调出netrw窗口和TagList窗口

__cscope :__

安装cscope

```bash
~ $ sudo pacman -S cscope
```

在vimrc中添加

```bash
:set cscopequickfix=s-,c-,d-,i-,t-,e-
```

设定使用quickfix窗口来显示cscope结果

先在项目目录下执行:

```bash
~ $ cscope -Rbq
```

这样会生成三个cscope库文件cscope.in.out, cscope.out, cscope.po.out.

在vim中执行:

```bash
:cs add /home/bdep__/code/acmt/cscope.out /home/bdep__/code/acmt
```

将cscope文件导入到vim中

可以使用:

```bash
:cs find g fun
```

查找fun的定义

```bash
:cs find c fun
```

查找fun被调用的地方

可以使用

```bash
:cw
```

打开QuickFix窗口,查看所有被调用的地方

__VisualMark :__

vim中的书签

安装VisualMark

使用mm命令把某行一标记为书签.

多个标签用F2 和 shift+F2切换.

__智能补全__

装好ctags, 生成tags, 导入tags

在vimrc中加入:

```bash
filetype plugin indent on
set completeopt=longest,menu
```

使用"Ctrl+X Ctrl+O"可以显示所有匹配标签

- Ctrl+P : 向前切换成员

- Ctrl+N : 向后切换成员

- Ctrl+E : 表示退出下拉窗口,并退回到原来录入的文字

- Ctrl+Y : 表示退出下拉窗口,并接受当前选项

__vim 命令__

让 vim 执行一个外部命令{cmd},例如:

```bash
:w !{cmd}
```

让vim调用root权限保存文件:

```bash
:w !sudo tee %
```

vim先调用外部sudo命令, 然后把当前缓冲区的内容从stdin传入。

- tee 是一个把stdin保存到文件的小工具。

- % 是vim当中一个只读寄存器的名字，总保存着当前编辑文件的文件路径。
