<!--toc-->

- [jekyll](#jekyll)
	- [1\. 安装jekyll必要组件](#1-安装jekyll必要组件)
	- [1.1 安装jekyll](#11-安装jekyll)
	- [1.2 jekyll使用](#12-jekyll使用)
		- [1.3 增加分页](#13-增加分页)
	- [2\. kramdown 语法说明](#2-kramdown-语法说明)
		- [2.1 TOC连接](#21-toc连接)
		- [2.2 删除线](#22-删除线)
		- [2.3 数学公式](#23-数学公式)
	- [3\. disqus](#3-disqus)

<!-- tocstop -->

----

# jekyll

[jekyll官方地址](https://jekyllrb.com/)

[jekyll中文](http://jekyllcn.com/)

## 1\. 安装jekyll必要组件

下文是在ubuntu上安装与设置

```
$ sudo apt-get install ruby ruby-dev make gcc nodejs
```

## 1.1 安装jekyll

```
$ sudo gem install jekyll
```

之后查看jekyll版本

```
$ jekyll -v
```

## 1.2 jekyll使用

首先要设置`_config.yml`文件

随后build为site。

```
$ jekyll build
```

build后网站文件在`_site`

使用`jekyll server`运行测试。

```
$ jekyll server
```

浏览器输入`localhost:4000`可以查看网站运行情况。

`_post`文件下位文章列表，可以将文章放入后build。

_注意：文件的头部要使用下方格式_

```
---
layout: post
title: Blogging Like a Hacker
---
```

### 1.3 增加分页

[参考文档](http://jekyllcn.com/docs/pagination/)

在`_config.yml`中设置分页

```
paginate: 7
paginate_path: "/page/:num"
gems: [jekyll-paginate]
```

在`_layouts/default.html`中添加分页代码

```html

<!-- 遍历分页后的文章 -->
    {% for post in paginator.posts %}
        <span class="post-meta">{{ post.date | date: "%b %-d, %Y " }}</span>
        <h2><a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></h2>
     {% endfor %}
<!-- fix page1 链接 -->
    {% if paginator.total_pages > 1 %}
        <div class="pagination">
            {% if paginator.previous_page %}
                    <a href="{{ paginator.previous_page_path | prepend: site.baseurl | replace: '//', '/' }}">« Prev</a>
                {% else %}
                    <span>« Prev</span>        
            {% endif %}

            {% for page in (1..paginator.total_pages) %}
                {% if page == paginator.page %}
                    <em>{{ page }}</em>
                {% elsif page == 1 %}
                    <a href="{{ site.baseurl }}">{{ page }}</a>
                {% else %}
                    <a href="{{ site.paginate_path | prepend: site.baseurl | replace: '//', '/' | replace: ':num', page }}">{{ page }}</a>
            {% endif %}
            {% endfor %}

            {% if paginator.next_page %}
                    <a href="{{ paginator.next_page_path | prepend: site.baseurl | replace: '//', '/' }}">Next »</a>
                {% else %}
                    <span>Next »</span>
            {% endif %}

            </div>
    {% endif %}
```

--------------------------------------------------------------------------------

## 2\. kramdown 语法说明

### 2.1 TOC连接

```
* TOC
{：toc}
```

生成目录带自动锚点

### 2.2 删除线

```
~~删除线~~
```

### 2.3 数学公式

需要增加`js`用于公式解析

```
<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
```

## 3\. disqus

添加`disqus`代码到`_layouts/post.html`
