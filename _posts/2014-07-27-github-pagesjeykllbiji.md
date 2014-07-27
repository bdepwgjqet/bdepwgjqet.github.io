---
layout: post
title: "用Github Pages和Jeykll管理博(bi)客(ji)"
description: ""
category: 
tags: [Github, Jekyll, Markdown, Vim, Pygments, Markdown]
---
{% include JB/setup %}


>- [1. Github Pages](#1. Github Pages)
- [2. Jekyll](#2. Jekyll)
- [3. Jekyll-Bootstrap](#3. Jekyll-Bootstrap)
- [4. Markdown and Vim](#4. Markdown and Vim)
  - [4.1 Markdown](#4.1 Markdown)
  - [4.2 Vim](#4.2 Vim)
- [5. Pygments](#5. Pygments)
- [6. 域名绑定](#6. 域名绑定)

---


<h1 id="1. Github Pages">1. Github Pages</h1>

[Github](https://github.com/)能很好地管理项目代码，它提供的[Github Pages](https://pages.github.com/)能很好地管理项目文档，同时可以用于写(zuo)博(bi)客(ji)。

Github Pages使用[Jekyll](http://jekyllrb.com/)模板系梳，只能静态页面发布，配置简单，是一个轻量级的系统，十分适合写博客。

---

<h1 id="2. Jekyll">2. Jekyll</h1>

[Jekyll](http://jekyllrb.com/)是基于Ruby Gem（对Ruby组件打包的Ruby打包系统）的解析引擎，能够把样板、liquid语言、markdown转换成静态网页。

可以用以下命令快速生成一个简单的网站模板：

```bash
~ $ gem install jekyll
~ $ jekyll new my-awesome-site
~ $ cd my-awesome-site
~ $ jekyll serve 
```

以上命令有可能提示无法链接，原因是Rubygems的官网被Wall(XX)了，可以通过以下命令使用淘宝的源：

```bash
~ $ gem source -r http://rubygems.org
~ $ gem source -a http://ruby.taobao.org
~ $ gem source -u
```

访问本地4000端口可以访问刚创建的模板。基本的结构如下：

{% highlight bash %}
.
├── about.md
├── _config.yml
├── css
│   └── main.css
├── feed.xml
├── _includes
│   ├── footer.html
│   ├── header.html
│   └── head.html
├── index.html
├── _layouts
│   ├── default.html
│   ├── page.html
│   └── post.html
└── _posts
    └── 2014-07-27-welcome-to-jekyll.markdown
{% endhighlight %}

简要说明:

- **_config.yml**：配置文件，用来配置基本的信息（例如所用的标记语言）以及定义效果。
- **_includes**：用来存放需要被反复调用的文件，可以通过 **\{\% include file.html \%\}**来调用flie.html文件。
- **_layouts**：主要用于存放模板文件。需要用 **YAML front matter**定义，可以通过 **\{\{ content \}\}**标记把数据插入到模板中。
- **_posts**：存放博客的文章，文件命名必须是year-month-day-article-title.mk这样。
- **site**用来存放最终生成的文档。

可以用如下的命令创建文章：

```bash
~ $ rake post title="your title"
```

---

<h1 id="3. Jekyll-Bootstrap">3. Jekyll-Bootstrap</h1>

[Jekyll-Bootstrap](http://jekyllbootstrap.com/)是Jekyll的一种主题，它使用twitter的[Bootstrap](http://getbootstrap.com/2.3.2/)作为前端CSS框架。

可以用以下命令安装并同步到Github主分支：

```bash
~ $ git clone https://github.com/plusjade/jekyll-bootstrap.git USERNAME.github.com
~ $ git remote set origin git@github.com:USERNAME/USERNAME.github.com.git
~ $ git push origin master
```

其主要目录结构和Jekyll大致相同。

---

<h1 id="4. Markdown and Vim">4. Markdown and Vim</h1>

<h2 id="4.1 Markdown">4.1 Markdown</h2>

Markdown 是一种轻量标记语言，写出来的文本能方便地转成HTML(或XHTML)。

Markdown 的语法说明可以参照：[这里](http://wowubuntu.com/markdown/)。

- Markdown页内跳转的实现方法：按如下方式先定义一个锚(id)。

  ```html
<span id="target">Hello World</span>
```

  在需要跳转的地方用以下方式生成链接：

  ```html
[XXXX](#target)
```

- 生成Markdown中的目录( __TOC__) : 可以用以下命令安装一个用js写的工具 __DocToc__。

  ```bash
~ $ npm install -g doctoc
```

  使用方法：

  ```bash
~ $ doctoc [file]
```

<h2 id="4.2 Vim">4.2 Vim</h2>

Vim 是编缉器之神，安装Vim markdown插件支持Markdown的语法高亮。

在[这里](http://www.vim.org/scripts/script.php?script_id=2882)下载安装包并解压。与Vim其它安装插件相同，只需将syntax和ftdetect文件夹中的 __mkd.vim__拷贝到  __~/.vim/__目录下的snytax和ftdetect文件夹中并重启Vim即可生效。

---

<h1 id="5. Pygments">5. Pygments</h1>

[Pygments](http://pygments.org/) 是一个代码语法高亮插件，它支持Jekyll模板，目前支持100多种语言的高亮。

在archlinux中可以用以下命令安装：

```bash
~ $ sudo pacman -S python2-pygments
~ $ gem install pygments.rb
```

在Jekyll的配置文件_config.yml中设置打开pgyments：

{% highlight bash %}
pygments: true
markdown: redcarpet
redcarpet:
 extensions: ["no_intra_emphasis", "fenced_code_blocks", "autolink",     "strikethrough", "superscript", "with_toc_data"]
{% endhighlight %}

用以下命令生成css文件：

```bash
~ $ pygmentize -S emacs -f html > your/path/pygments.css
```

emacs指的是样式名，可以在[这里](pygments.org/demo/)查看样式名的种类，以及pgyments支持的语言的种类。

在default.html中引用pygments的css文件：

```html
<link href="{{ ASSET_PATH }}/css/pygments.css" rel="stylesheet">
```

通过以下方式实现代码高亮：

{% highlight bash linenos %}
{% highlight java linenos %}
/* hello world demo */
public class HelloWorld {
    public static void main(String args[]) {
        System.out.println("Hello World!");
	}
}
{% endhighlight %}
{% endhighlight %}

或者这样：

{% highlight bash linenos %}
```java
/* hello world demo */
public class HelloWorld {
    public static void main(String args[]) {
        System.out.println("Hello World!");
	}
}
```
{% endhighlight %}


效果：

{% highlight java linenos %}
/* hello world demo */
public class HelloWorld {
    public static void main(String args[]) {
        System.out.println("Hello World!");
	}
}
{% endhighlight %}

以上 __\{\% highlight java linenos \%\}__中java表示语言，linenos表示代码框中显示行号。使用default样式生成的css不支持linenos。

---

<h1 id="6. 域名绑定">6. 域名绑定</h1>

Github 绑定域名的方法可以参考[Github官方说明](https://help.github.com/articles/setting-up-a-custom-domain-with-github-pages)。
对于一级域名：
- 在name.github.io项目根目录创建CNAME文件，在其第一行写入域名（例：name.com）。
- 在域名运营商进行A解析。由于国外DNS易被（XX），可以使用dnspod.cn进行DNS解析（Github需要将域名解析为192.30.252.153,192.30.252.154，解析完后可以用以下dig命令验证），然后更改运营商处 __Nameservers__为F1G1NS1.DNSPOD.NET和F1G1NS2.DNSPOD.NET。
  ```bash
~ $ dig your-site-name.com +nostats +nocomments +nocmd
~ $ dig your-github-name.github.io +nostats +nocomments +nocmd
```
  两次询问DNS结果应当一至。
- 等待解析成功。

---

