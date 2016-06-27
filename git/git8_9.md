# 8. 自定义 Git

介绍一些配置与钩子机制

## 8.1 配置 Git

我们已经知道使用`git config `可以配置Git。

首先要做的是设置名字与电子邮件地址。

	$ git config --global user.name "[名字]"
	$ git config --global user.email [邮件地址]

Git的配置文件分为三个层级，低级的设置会覆盖高级的。

1. 全系统设置，位于`/etc/gitconfig`文件，该文件含有系统里每位用户及他们所有仓库的配置。使用`$ git config --system`读写文件。

2. 全局设置，位于每个用户的`~/.gitconfig`文件。可以使用`$ git config --global -e`读写文件。

3. 本地设置，位于工作目录下`.git/config`,只对当前的项目有效。

只要合乎语法，可以使用任意编辑工具。

### 8.1.1 客户端基本配置

Git能识别配置为两大类：客户端和服务器。其中大部分属于客户端配置。

如果想要得到当前Git版本的选项列表，可以运行

	$ man git-config

* `core.editor`设置编辑器

	默认情况下，Git会调用环境变量（`$VISUAL`或`$EDITOR`）设置的文本编辑器。如果不设置会调用vi来创建和编辑提交和标签信息。
	
	使用下方命令来设置编辑器
	
		$ git config --global core.editor emacs
	
	现在Git都会调用Emacs编辑信息。
	
* `commit.template`设置默认提交信息

	如果把设定指定为系统上的某个文件的路径，提交信息为文件中的内容。

	**例子：**
	
	假设创建了一个叫`~/.gitmessage.txt`的模板，内容类似下方：
	
		subject line
		
		what happened
		
		[ticket: X]
	
	要让Git把它作为`$ git commit`时的默认信息，设置命令如下：
		
		$ git config --global commit.template ~/.gitmessage.txt
		$ git commit
	
	当你提交时，编辑器中就会显示如下的提交信息：
	
		subject line
		
		what happened
		
		[ticket: X]
		# Please enter the commit message for your changes. Lines starting
		# with '#' will be ignored, and an empty message aborts the commit.
		# On branch master
		# Changes to be committed:
		#   (use "git reset HEAD <file>..." to unstage)
		#
		# modified:   lib/test.rb
		#
		~
		~
		".git/COMMIT_EDITMSG" 14L, 297C
	
	如果要求提交格式，可以在系统创建一个文件，并设置为默认Git的提交模板。

* `core.pager` 设定分页器，

	默认是`less`，设置为空字符，关闭该选项。
	
		$ git config --global core.pager ''
	
	这样不管命令输出是多少，Git都会在一页显示内容。

* `user.signingkey` 设置署名。

	如果要创建包含签署的标签，把GPG签名设置为配置项会更好。
	
		$ git config --global user.signingkey <gpg-key-id>
	
	随后，运行`git tag`命令时，即可直接签署标签：
	
		$ git tag -s <tag-name>

* `core.excludesfile`设置类似全局有效`.gitignore`。

	**例子:**
	
	创建一个`~/.gitignore_global`文件。忽略文件`.DS_Store`和以`~`结尾文件。
	
		*~
		.DS_Store
	
	随后运行：
	
		$ git config --global core.excludesfile ~/.gitignore_global
		
	完成设置。
	
* `help.autocorrect`允许模糊命令
	
	默认情况下是关闭的。
	
	使用命令：
	
		$ git config --global help.autocorrect [时间]
	
	[时间]实际的考虑时间是设置的十分之一。
	
### 8.1.2 Git 中的着色

Git支持对终端内容着色。

* `color.ui`

	Git会自动着色大部分输入内容，如果不喜欢可以关掉。
	
	关闭终端颜色输出命令：
	
		$ git config --global color.ui false
	
	默认值是`auto`，它会着色直接输出到终端的内容。当有特殊指定是无效。
	
	有可以设置为`always`,来忽略自定制的设置。

* `color.*` 设置命令对应的着色。

它们都能被设置为`true`、`false`、`always`。

	color.branch
	color.diff
	color.interactive
	color.status

以上配置都有子选项，它们可以覆盖父设置，达到输出各个部分着色。

**例子：**

为了让diff输出信息以蓝色前景、黑色背景和粗体显示，可以运行

	$ git config --global color.diff.meta "blue black bold"

可以设置的颜色有：`normal`、`black`、`red`、`green`、`yellow`、`blue`、`magenta`、`cyan`、`white`。

可以设置的字体有：`bold`（粗体）、`dim`（斜体）、`ul`（下滑线）、`blink`（交换前景色）、`reverse`（背景色）。

### 8.1.3 外部的合并与比较工具

Perforce 工具的设置与介绍，Perforce是图像化的处理工具。

Perforce[下载地址](https://www.perforce.com/)

[文档说明地址](https://git-scm.com/book/zh/v2/%E8%87%AA%E5%AE%9A%E4%B9%89-Git-%E9%85%8D%E7%BD%AE-Git#外部的合并与比较工具)

### 8.1.4 格式化与多余的空白字符

* `core.autocrlf`
	
	CRLF问题：问题产生的原因是window使用回车（CR）和换行（LF）两个字符来结束一行，而Mac和Linux只用换行（LF）一个字符。
	
	虽然这是小问题，但是它会扰乱跨平台协作，许多Window上的编辑器会悄悄把行尾的换行字符转成回车和换行。或者在用户按下Enter时，插入回车和换行两个字符。
	
	Git可以在提交时，自动把回车和换行转变成换行，而在检出代码时把换行转换成回车和换行。
	
	可以使用`core.autocrlf`来打开此功能。
	
	如果在window系统上，把它设置成`true`,这样在检出代码时，换行会被转换成回车和换行。
	
		$ git config --global core.autocrlf true
	
	如果使用的是Linux或Mac，不需要Git在检出文件时自动转换。然而当一个以回车加换行作为结束符的文件不小心被引入时，你一定想让Git修正。你可以把`core.autocrlf`设置成`input`来告诉Git在提交时把回车和换行转换成换行，检出时不转换：
	
		$ git config --global core.autocrlf input
	
	以上方法在window上检出文件会保留回车和换行，而在Mac和Linux上会保留换行。
	
	如果是window程序员，且正在开发仅运行在window上的项目，可以设置`false`取消此功能，
	
		$ git config --global core.autocrlf false
	
* `core.whitespace` 设定Git对空白的处理。

	Git 中预先设置了一些选项来探测和修正多余空白字符问题。它提供了六种处理多余空白字符的选项。
	
	其中三个默认开启，另外三个默认关闭，不过可在自由的设置它们。
	
	默认被打开的三个选项是
	
	1. `blank-at-eol`,查找行尾的空格。
	
	2. `blank-at-eof`,盯住文件底部的空行。
	
	3. `space-before-tab`,警惕行头tag前面的空行。
	
	默认被关闭的三个选项是
	
	1. `indent-with-non-tab`,揪出以空格而非tab开头的行（可以用`tabwidth`选项控制它）
	
	2. `tab-in-indent`，监视在行头表示缩进的tab
	
	3. `cr-at-eol`,告诉Git忽略行尾的回车
	
	**例子：**
	
	例如，你想要打开除`cr-at-eol`之外的所有选项
	
		$ git config --global core.whitespace \ trailing-space,space-before-tab,indent-with-non-tab
	
	当使用`git apply`打补丁时你也会从中受益。
	
	下方命令为处理补丁时发出警告:
	
		$ git apply --whitespace=warn <patch> 
	
	下方命令为在Git打补丁前自动修正此问题：
	
		$ git apply --whitespace=fix <patch>
	
	如果提交了有空白问题的文件，但还没推送到上游，你让Git在重写补丁时自动修正它们。
	
		$ git rebase --whitespace=fix
	
### 8.1.5 服务器端配置

Git服务器的配置项并不多，但有一些选项值得一看。

* `receive.fsckObjects`

	检测文件的有效性以及SHA-1验证是否保持一致。
	
	默认为关闭，因为速度很慢。
	
		$ git config --system receive.fsckObjects true
	
	现在，Git会在每次推送前检查库的完整性，确保没有被问题客户端引入破坏性数据。
	
* `receive.denyNonFastForwards`

	当确认要更新远程分支时，可以使用`-f`强制更新。
	
	如有要禁止强制更新推送，使用下方命令
	
		$ git config --system receive.denyNonFastForwards true


* `receive.denyDeletes`

	有一些方法可以绕过`denyNonFastForwards`策略。
	
	其中一种是先删除某个分支，在连同新的引用一起推送回该分支。
	
	设置`receive.denyDeletes`可以把漏洞补上
	
		$ git config --system receive.denyDeletes true

	   

----

## 8.2 Git 属性

对一些项目进行特定的设置，可以在目录下`.gitattributes`文件进行设置（通常在项目的根目录）。如果不想让设置文件与其它文件一同提交，可以在`.git/info/attributes`文件中设置。

### 8.2.1 二进制文件

你可以用Git属性让Git知道哪些是二进制文件，并设置如何处理这些文件。

* 识别二进制文件

	例如Mac平台上的Xcode项目包含一个`.pbxproj`结尾的文件，它通常是一个记录项目构建的JSON数据集，由IDE写入磁盘。希望将它当成二进制文件。
	
	要让Git把所有`pbxproj`文件当成二进制文件，在`.gitattributes`文件中如下设置
	
		*.pbxproj binary

现在使用`git show`或`git diff`时，Git不会比较或打印该文件的变化。
	
* 比较二进制文件

也可以使用Git属性来有效的比较两个二进制文件。秘诀在于，告诉Git怎么把二进制文件转化为文本格式，从而能够使用普通的diff方式进行对比。

最让人头疼的问题之一：对Microsoft Word文档进行版本控制。

**例子：**

**Windows环境下未测试**

默认情况下，只能得到下方的输出结果：

	$ git diff
	diff --git a/chapter1.docx b/chapter1.docx
	index 88839c4..4afcb7c 100644
	Binary files a/chapter1.docx and b/chapter1.docx differ

设置Git属性可以在很好的解决此问题，在`.gitattributes`文件中添加

	*.docx diff=word

这是告诉Git当你尝试查看`.docx`模式的文件都应该使用`word`过滤器。

对Git进行设置，令其能够借助docx2txt程序将Wrod文档转为可读文本文件，这样就可以正常的比较了。

首先安装`docx2txt`，下载[地址](http://docx2txt.sourceforge.net )最新日期为2014年，版本为v1.4。下载后按照`INSTALL`文件，把它放到可执行的路径下。接下来，还需要写一个脚本把输出结果包装成Git支持的格式。在路径下创建一个`docx2txt`文件，添加这些下方内容。

	#!/bin/bash
	docx2txt.pl $1 -

使用`chmod a+x`给这个文件加上可执行权限（linux下）。最后配置Git来使用脚本。

	$ git config diff.word.textconv docx2txt

现在Git就能将Word文件转换成文本文件了。

**关于图片的比较**

`exigtool`下载[地址](http://www.sno.phy.queensu.ca/~phil/exiftool/),目前还在更新。

还可以比较图像文件。其中一个办法是，在比较时对图像文件运用一个过滤器，提炼出EXIF信息。

需要下载`exiftool`,它可以将图像转换为元数据的文本信息，这样能以文本形式显示发送过的变动：
	
	$ echo '*.png diff=exif' >> .gitattributes
	$ git config diff.exif.textconv exiftool
	

### 8.2.2 关键字展开

### 8.2.3 导出版本库

### 8.2.4 合并策略

## 8.3 Git 钩子

### 8.3.1 安装一个钩子

### 8.3.2 客户端钩子

* 提交工作流钩子

* 电子邮件工作流钩子

* 其它客户端钩子

### 8.3.3 服务器端钩子

## 8.4 使用强制策略的一个例子

### 8.4.1 服务器端钩子

* 指定特殊的提交信息格式

* 指定基于用户的访问权限控制列表（ACL）系统

* 测试一下

### 8.4.2 客户端钩子

# 9. Git 与其他系统

使用到的几率不大，如果需要

阅读用户[文档](https://git-scm.com/book/zh/v2/Git-%E4%B8%8E%E5%85%B6%E4%BB%96%E7%B3%BB%E7%BB%9F-%E4%BD%9C%E4%B8%BA%E5%AE%A2%E6%88%B7%E7%AB%AF%E7%9A%84-Git)