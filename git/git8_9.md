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

### 8.1.3 外部的合并与比较工具

### 8.1.4 格式化与多余的空白字符

### 8.1.5 服务器端配置

## 8.2 Git 属性

### 8.2.1 二进制文件

* 识别二进制文件

* 比较二进制文件

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