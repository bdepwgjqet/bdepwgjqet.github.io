引入tikz包 :

```latex
\usepackage{tikz}
```

引入需要调用的扩展 : 

```latex
\usetikzlibrary{arrows,automata}
```

通过如下方式设置背景颜色:

```latex
\definecolor{shadecolor}{rgb}{0.95,0.95,0.95}
```

绘图环境设置

```latex
	\begin{tikzpicture}[shorten >=1pt,node distance=2cm,>=stealth',auto,every state/.style={thin,fill=blue!10}]
```

- node distance 设置点间距离

绘制结点的一种方法:

```latex
\node[state][label=above:$\{标注名\}$]            (结点key(可中文))                [right/below of = 节点key]                  {$结点显示名称$} 
\node[state]            (root)                                    {$root$};
\node[state]            (1)                  [right of = root]    {$1,0,0$};
```

绘制边:
```latex
            (起始节点key)   edge    node{连边标注名称} (终节点key)
\path[->]	(root)	edge	node{T}	(1)
                edge	node{A}	(7)
                edge[loop above] node{Not in \{T,A\}} (root)
        (1)		edge	node{M}	(2)
                edge	node{E}	(5)
        (2)		edge	node{A}	(3)	
        (3)		edge	node{L}	(4)
        (4)		edge	node{L}	(10)
        (5)		edge	node{A}	(6)
        (7)		edge	node{T}	(8)
        (8)		edge	node{M}	(9);
```

绘制虚线的方式


```latex
\draw	[dash pattern=on 2pt off 3pt on 4pt off 4pt,->]	(1) to [out=-135,in=-45]	(root);
```

