---
layout: post
title: "Vim notes"
description: ""
category: 
tags: []
---
{% include JB/setup %}

__before read__
Try to use :help first

__plugin management__
vundle

__useful command__

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

__ranges__

$	last line	:$s/old/new/g

.	current line	:.w single.txt

%	all lines (same as 1,$)	:%s/old/new/g

__filter__

!{motion}{filter}

eg:

- :%!python -m json.tool, use external __python -m json.tool__ filter all lines(%)

__register__

" + register name

eg:

- "+y, copy to system copy cache, + means system copy cache register
- "+p, paste
- "% only read, use to store file name

__motion__

:help motion.txt

__exe__

:help exe
