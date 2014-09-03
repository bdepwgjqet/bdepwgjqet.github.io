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

- 在函数的调用处按<C-]>光标会自动跳到函数的定义处

- 在函数的定义处按<C-T>光标会跳回被调用的地方

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

```baslet 
g:winManagerWindowLayout='FileExplorer|TagList'
nmap wm :WMToggle<cr>
```

在vim中按wm可调出netrw窗口和TagList窗口

