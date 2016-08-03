<!--toc-->

- [8\. 自定义 Git](#8-自定义-git)
	- [8.1 配置 Git](#81-配置-git)
		- [8.1.1 客户端基本配置](#811-客户端基本配置)
		- [8.1.2 Git 中的着色](#812-git-中的着色)
		- [8.1.3 外部的合并与比较工具](#813-外部的合并与比较工具)
		- [8.1.4 格式化与多余的空白字符](#814-格式化与多余的空白字符)
		- [8.1.5 服务器端配置](#815-服务器端配置)
	- [8.2 Git 属性](#82-git-属性)
		- [8.2.1 二进制文件](#821-二进制文件)
		- [8.2.2 关键字展开](#822-关键字展开)
		- [8.2.3 导出版本库](#823-导出版本库)
		- [8.2.4 合并策略](#824-合并策略)
	- [8.3 Git 钩子](#83-git-钩子)
		- [8.3.1 安装一个钩子](#831-安装一个钩子)
		- [8.3.2 客户端钩子](#832-客户端钩子)
		- [8.3.3 服务器端钩子](#833-服务器端钩子)
	- [8.4 使用强制策略的一个例子](#84-使用强制策略的一个例子)
		- [8.4.1 服务器端钩子](#841-服务器端钩子)
		- [8.4.2 客户端钩子](#842-客户端钩子)
- [9\. Git 与其他系统](#9-git-与其他系统)

<!-- tocstop -->

----

# 8\. 自定义 Git

介绍一些配置与钩子机制

## 8.1 配置 Git

我们已经知道使用`git config`可以配置Git。

首先要做的是设置名字与电子邮件地址。

```
$ git config --global user.name "[名字]"
$ git config --global user.email [邮件地址]
```

Git的配置文件分为三个层级，低级的设置会覆盖高级的。

1. 全系统设置，位于`/etc/gitconfig`文件，该文件含有系统里每位用户及他们所有仓库的配置。使用`$ git config --system`读写文件。

2. 全局设置，位于每个用户的`~/.gitconfig`文件。可以使用`$ git config --global -e`读写文件。

3. 本地设置，位于工作目录下`.git/config`,只对当前的项目有效。

只要合乎语法，可以使用任意编辑工具。

### 8.1.1 客户端基本配置

Git能识别配置为两大类：客户端和服务器。其中大部分属于客户端配置。

如果想要得到当前Git版本的选项列表，可以运行

```
$ man git-config
```

- `core.editor`设置编辑器

  默认情况下，Git会调用环境变量（`$VISUAL`或`$EDITOR`）设置的文本编辑器。如果不设置会调用vi来创建和编辑提交和标签信息。

  使用下方命令来设置编辑器

  ```
    $ git config --global core.editor emacs
  ```

  现在Git都会调用Emacs编辑信息。

- `commit.template`设置默认提交信息

  如果把设定指定为系统上的某个文件的路径，提交信息为文件中的内容。

  **例子：**

  假设创建了一个叫`~/.gitmessage.txt`的模板，内容类似下方：

  ```
    subject line

    what happened

    [ticket: X]
  ```

  要让Git把它作为`$ git commit`时的默认信息，设置命令如下：

  ```
    $ git config --global commit.template ~/.gitmessage.txt
    $ git commit
  ```

  当你提交时，编辑器中就会显示如下的提交信息：

  ```
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
  ```

  如果要求提交格式，可以在系统创建一个文件，并设置为默认Git的提交模板。

- `core.pager` 设定分页器，

  默认是`less`，设置为空字符，关闭该选项。

  ```
    $ git config --global core.pager ''
  ```

  这样不管命令输出是多少，Git都会在一页显示内容。

- `user.signingkey` 设置署名。

  如果要创建包含签署的标签，把GPG签名设置为配置项会更好。

  ```
    $ git config --global user.signingkey <gpg-key-id>
  ```

  随后，运行`git tag`命令时，即可直接签署标签：

  ```
    $ git tag -s <tag-name>
  ```

- `core.excludesfile`设置类似全局有效`.gitignore`。

  **例子:**

  创建一个`~/.gitignore_global`文件。忽略文件`.DS_Store`和以`~`结尾文件。

  ```
    *~
    .DS_Store
  ```

  随后运行：

  ```
    $ git config --global core.excludesfile ~/.gitignore_global
  ```

  完成设置。

- `help.autocorrect`允许模糊命令

  默认情况下是关闭的。

  使用命令：

  ```
    $ git config --global help.autocorrect [时间]
  ```

  [时间]实际的考虑时间是设置的十分之一。

### 8.1.2 Git 中的着色

Git支持对终端内容着色。

- `color.ui`

  Git会自动着色大部分输入内容，如果不喜欢可以关掉。

  关闭终端颜色输出命令：

  ```
    $ git config --global color.ui false
  ```

  默认值是`auto`，它会着色直接输出到终端的内容。当有特殊指定是无效。

  有可以设置为`always`,来忽略自定制的设置。

- `color.*` 设置命令对应的着色。

它们都能被设置为`true`、`false`、`always`。

```
color.branch
color.diff
color.interactive
color.status
```

以上配置都有子选项，它们可以覆盖父设置，达到输出各个部分着色。

**例子：**

为了让diff输出信息以蓝色前景、黑色背景和粗体显示，可以运行

```
$ git config --global color.diff.meta "blue black bold"
```

可以设置的颜色有：`normal`、`black`、`red`、`green`、`yellow`、`blue`、`magenta`、`cyan`、`white`。

可以设置的字体有：`bold`（粗体）、`dim`（斜体）、`ul`（下滑线）、`blink`（交换前景色）、`reverse`（背景色）。

### 8.1.3 外部的合并与比较工具

Perforce 工具的设置与介绍，Perforce是图像化的处理工具。

Perforce[下载地址](https://www.perforce.com/)

[文档说明地址](https://git-scm.com/book/zh/v2/%E8%87%AA%E5%AE%9A%E4%B9%89-Git-%E9%85%8D%E7%BD%AE-Git#外部的合并与比较工具)

### 8.1.4 格式化与多余的空白字符

- `core.autocrlf`

  CRLF问题：问题产生的原因是window使用回车（CR）和换行（LF）两个字符来结束一行，而Mac和Linux只用换行（LF）一个字符。

  虽然这是小问题，但是它会扰乱跨平台协作，许多Window上的编辑器会悄悄把行尾的换行字符转成回车和换行。或者在用户按下Enter时，插入回车和换行两个字符。

  Git可以在提交时，自动把回车和换行转变成换行，而在检出代码时把换行转换成回车和换行。

  可以使用`core.autocrlf`来打开此功能。

  如果在window系统上，把它设置成`true`,这样在检出代码时，换行会被转换成回车和换行。

  ```
    $ git config --global core.autocrlf true
  ```

  如果使用的是Linux或Mac，不需要Git在检出文件时自动转换。然而当一个以回车加换行作为结束符的文件不小心被引入时，你一定想让Git修正。你可以把`core.autocrlf`设置成`input`来告诉Git在提交时把回车和换行转换成换行，检出时不转换：

  ```
    $ git config --global core.autocrlf input
  ```

  以上方法在window上检出文件会保留回车和换行，而在Mac和Linux上会保留换行。

  如果是window程序员，且正在开发仅运行在window上的项目，可以设置`false`取消此功能，

  ```
    $ git config --global core.autocrlf false
  ```

- `core.whitespace` 设定Git对空白的处理。

  Git 中预先设置了一些选项来探测和修正多余空白字符问题。它提供了六种处理多余空白字符的选项。

  其中三个默认开启，另外三个默认关闭，不过可在自由的设置它们。

  默认被打开的三个选项是

  1. `blank-at-eol`,查找行尾的空格。

  2. `blank-at-eof`,盯住文件底部的空行。

  3. `space-before-tab`,警惕行头tag前面的空行。

    默认被关闭的三个选项是

  4. `indent-with-non-tab`,揪出以空格而非tab开头的行（可以用`tabwidth`选项控制它）

  5. `tab-in-indent`，监视在行头表示缩进的tab

  6. `cr-at-eol`,告诉Git忽略行尾的回车

    **例子：**

    例如，你想要打开除`cr-at-eol`之外的所有选项

    $ git config --global core.whitespace \ trailing-space,space-before-tab,indent-with-non-tab

    当使用`git apply`打补丁时你也会从中受益。

    下方命令为处理补丁时发出警告:

    $ git apply --whitespace=warn

    <patch>
    </patch>

    下方命令为在Git打补丁前自动修正此问题：

    $ git apply --whitespace=fix

    <patch>
    </patch>

    如果提交了有空白问题的文件，但还没推送到上游，你让Git在重写补丁时自动修正它们。

    $ git rebase --whitespace=fix

### 8.1.5 服务器端配置

Git服务器的配置项并不多，但有一些选项值得一看。

- `receive.fsckObjects`

  检测文件的有效性以及SHA-1验证是否保持一致。

  默认为关闭，因为速度很慢。

  ```
    $ git config --system receive.fsckObjects true
  ```

  现在，Git会在每次推送前检查库的完整性，确保没有被问题客户端引入破坏性数据。

- `receive.denyNonFastForwards`

  当确认要更新远程分支时，可以使用`-f`强制更新。

  如有要禁止强制更新推送，使用下方命令

  ```
    $ git config --system receive.denyNonFastForwards true
  ```

- `receive.denyDeletes`

  有一些方法可以绕过`denyNonFastForwards`策略。

  其中一种是先删除某个分支，在连同新的引用一起推送回该分支。

  设置`receive.denyDeletes`可以把漏洞补上

  ```
    $ git config --system receive.denyDeletes true
  ```

--------------------------------------------------------------------------------

## 8.2 Git 属性

对一些项目进行特定的设置，可以在目录下`.gitattributes`文件进行设置（通常在项目的根目录）。如果不想让设置文件与其它文件一同提交，可以在`.git/info/attributes`文件中设置。

### 8.2.1 二进制文件

你可以用Git属性让Git知道哪些是二进制文件，并设置如何处理这些文件。

- 识别二进制文件

  例如Mac平台上的Xcode项目包含一个`.pbxproj`结尾的文件，它通常是一个记录项目构建的JSON数据集，由IDE写入磁盘。希望将它当成二进制文件。

  要让Git把所有`pbxproj`文件当成二进制文件，在`.gitattributes`文件中如下设置

  ```
    *.pbxproj binary
  ```

现在使用`git show`或`git diff`时，Git不会比较或打印该文件的变化。

- 比较二进制文件

也可以使用Git属性来有效的比较两个二进制文件。秘诀在于，告诉Git怎么把二进制文件转化为文本格式，从而能够使用普通的diff方式进行对比。

最让人头疼的问题之一：对Microsoft Word文档进行版本控制。

**例子：**

**Windows环境下未测试**

默认情况下，只能得到下方的输出结果：

```
$ git diff
diff --git a/chapter1.docx b/chapter1.docx
index 88839c4..4afcb7c 100644
Binary files a/chapter1.docx and b/chapter1.docx differ
```

设置Git属性可以在很好的解决此问题，在`.gitattributes`文件中添加

```
*.docx diff=word
```

这是告诉Git当你尝试查看`.docx`模式的文件都应该使用`word`过滤器。

对Git进行设置，令其能够借助docx2txt程序将Wrod文档转为可读文本文件，这样就可以正常的比较了。

首先安装`docx2txt`，下载[地址](http://docx2txt.sourceforge.net)最新日期为2014年，版本为v1.4。下载后按照`INSTALL`文件，把它放到可执行的路径下。接下来，还需要写一个脚本把输出结果包装成Git支持的格式。在路径下创建一个`docx2txt`文件，添加这些下方内容。

```
#!/bin/bash
docx2txt.pl $1 -
```

使用`chmod a+x`给这个文件加上可执行权限（linux下）。最后配置Git来使用脚本。

```
$ git config diff.word.textconv docx2txt
```

现在Git就能将Word文件转换成文本文件了。

**关于图片的比较**

`exigtool`下载[地址](http://www.sno.phy.queensu.ca/~phil/exiftool/),目前还在更新。

还可以比较图像文件。其中一个办法是，在比较时对图像文件运用一个过滤器，提炼出EXIF信息。

需要下载`exiftool`,它可以将图像转换为元数据的文本信息，这样能以文本形式显示发送过的变动：

```
$ echo '*.png diff=exif' >> .gitattributes
$ git config diff.exif.textconv exiftool
```

### 8.2.2 关键字展开

在Git中，关键字展开有一个主要问题，无法利用它往文件中加入其关联提交的相关信息。

Git属性提供了两种方法来达成关键字展开的目的。

**方法一**

把文件所对应数据对象的SHA-1校验，自动注入到文件中的`$Id$`字段。如果在一个或者多个文件上设置，下次当你检出相关分支时，Git会用相应数据对象SHA-1值替换对应字段。

```
$ echo '*.txt ident' >> .gitattributes
$ echo '$Id$' > test.txt
```

当下次检出文件时，Git将注入数据对象的SHA-1校验

```
$ rm test.txt
$ git checkout -- test.txt
$ cat test.txt
$Id: 42812b7653c7b88933f8a9d6cad0ca16714b9bb3 $
```

上述方法的用途比较有限。

**方法二**

自己编写过滤器来实现文件提交或检出时关键字替换。

一个过滤器由`clean`和`smudge`两个子过滤器组成。在`.gitattributes`文件中，你能对特定的路径设置一个过滤器，然后设置文件检出前的处理脚本(`smudge`)和文件暂存前的处理脚本（`clean`）。

下图是`smudge`的处理过程

![](https://git-scm.com/book/en/v2/book/08-customizing-git/images/smudge.png)

下图是`clean`的处理过程

![](https://git-scm.com/book/en/v2/book/08-customizing-git/images/clean.png)

**例子：**

在提交前，用`indent`程序过滤所有C源码。可以在`.gitattributes`文件中对filter属性设置`indent`过滤器来过滤`*.c`文件。

```
*.c filter=indent
```

然后，通过下方配置，让Git知道`indent`过滤器在`smudge`和`clean`时分别该做什么:

```
$ git config --global filter.indent.clean indent
$ git config --global filter.indent.smudge cat
```

在这个例子中，当暂存`*.c`文件时，`indent`程序会先被触发。在把它们检出回硬盘时，`cat`程序会先被触发。`cat`在这里没有作用，它仅把输入的数据重新输出。

还有一个使用Ruby的例子，详细的查看[官方文档](https://git-scm.com/book/zh/v2/%E8%87%AA%E5%AE%9A%E4%B9%89-Git-Git-%E5%B1%9E%E6%80%A7#filters_a)

### 8.2.3 导出版本库

Git属性在导出项目归档时也能发挥作用。

`export-ignore`

当归档时，可以设置Git不导出某些文件和目录。如果不想在归档中包含某个子目录或文件，但想把他们纳入项目的版本管理中，可以在`export-ignore`属性中指定。

**例子：**

假如在`test/`子目录下有一些测试文件，不希望它们被包含在项目导出的压缩包中。

在Git属性文件中加：

```
test/ export-ignore
```

当运行`git archive`时，`test`不会再压缩文件中。

`export-subst`

在导出文件进行部署时，将`git log`格式化，并将关键字展开。

**例子：**

想在项目中包含一个叫做`LAST_COMMIT`的文件，并在运行`git archive`时自动注入最新的体提交数据，应该这样设置：

```
$ echo 'Last commit date: $Format:%cd by %aN$' > LAST_COMMIT
$ echo "LAST_COMMIT export-subst" >> .gitattributes
$ git add LAST_COMMIT .gitattributes
$ git commit -am 'adding LAST_COMMIT file for archives'
```

运行`git archive`后，内容会被替换成下方的样子：

```
$ git archive HEAD | tar xCf ../deployment-testing -
$ cat ../deployment-testing/LAST_COMMIT
Last commit date: Tue Apr 21 08:38:48 2009 -0700 by Scott Chacon
```

还可以用提交信息或者git注解进行替换，并且`git log`还能做字词包装：

```
$ echo '$Format:Last commit: %h by %aN at %cd%n%+w(76,6,9)%B$' > LAST_COMMIT
$ git commit -am 'export-subst 使用 git log 的自定义格式化工具

git archive 直接使用 git log 的 `pretty=format:`
处理器，并在输出中移除两侧的 `$Format:` 和 `$`
标记。
'
$ git archive @ | tar xfO - LAST_COMMIT
Last commit: 312ccc8 by Jim Hill at Fri May 8 09:14:04 2015 -0700
       export-subst 使用 git log 的自定义格式化工具

         git archive 直接使用 git log 的 `pretty=format:` 处理器，并
         在输出中移除两侧的 `$Format:` 和 `$` 标记。
```

### 8.2.4 合并策略

通过Git属性，能对项目中特定文件指定不同的合并策略。

一个有用的选项，告诉Git当特定文件发送冲突时不要合并它们，而是直接使用本地内容。

**例子：**

假设你有一个数据库设置文件 `database.xml`，在两个分支中它是不同的，而你想合并另一个分支到你的分支上，又不想弄乱该数据库文件。 你可以设置属性如下：

```
database.xml merge=ours
```

然后定义一个虚拟的合并策略，叫做 `ours`：

```
$ git config --global merge.ours.driver true
```

如果你合并了另一个分支，`database.xml` 文件不会有合并冲突，相反会显示如下信息：

```
$ git merge topic
Auto-merging database.xml
Merge made by recursive.
```

`database.xml` 就保持了主干分支中的原始版本。

--------------------------------------------------------------------------------

## 8.3 Git 钩子

Git能在特定动作发生时触发自定义脚本。有两组这样的钩子：客户端和服务器。

客户端钩子由提交或合并等操作调用，而服务端钩子用于接收被推送提交等联网操作。

### 8.3.1 安装一个钩子

钩子都存储在Git目录下的`hooks`子目录中。即项目的`.git/hooks`。当使用`git init`初始化一个新版本时，Git默认会在这个目录中放置一些脚本。

脚本除了本身可以被调用外，它们还透露了被触发时转入的参数。

把一个一个正确命名而且可执行的文件放入Git目录下`hooks`子目录中，即可激活钩子脚本。这样，脚本就能被Git调用。

### 8.3.2 客户端钩子

客户端钩子分为很多种。下面把它们分为：提交工作流钩子，电子邮件工作流钩子和其他钩子。

_注意：克隆某个版本库时，客户端钩子并不一起复制。如果需要这些钩子，可以使用强制策略，在服务器端实现这一功能。_

- 提交工作流钩子

4个涉及提交的钩子

1. `pre-commit`在输入提交信息前运行。

  用于检查提交的快照，检查是否有所遗漏，确保测试运行，以及核查代码。如果该钩子以非零值退出，Git将放弃提交。

  不过可以使用`git commit --no-verify`来绕过这个环节。

2. `prepare-commit-msg`在启动提交信息编辑器之前，默认信息被创建之后运行。

  多用与插入提交信息。

3. `commit-msg`接受一个参数，参数存在当前提交信息的临时文件的路径。

  当提交为非零时，Git将放弃提交。

4. `post-commit`在整个提交过程完成后运行。

  它不接收任何参数，可以很容易的运行`git log -1 HEAD`来获得最后一次提交信息。

5. 电子邮件工作流钩子

  用于接收由`git format-patch`产生的补丁。

  要设置的钩子有三个，它们都由`git am`调用。

  第一个钩子是`applypatch-msg`

  它接收单个参数，包含请求合并信息的临时文件的名字。如果返回值非零，Git将放弃该补丁。

  第二个钩子是`pre-applypatch`

  它运行在应用补丁之后，产生提交之前，可以在提交前检查快照。

  第三个钩子是`post-applypatch`

  运行于提交产生之后，把结果通知给一个小组或所拉取的补丁的作者。但不能停止打补丁的过程。

- 其它客户端钩子

`pre-rebase`

运行于变基之前，以非零值退出可以终止变基的过程。可以使用钩子来禁止对已经推送的提交变基。

`post-rewrite`

钩子被那些会替换提交记录的命令调用，例如：`git commit --amend` 和`git rebase`。它唯一的参数是触发重写发的命令名，同时从标准输入中接受一系列重写的提交记录。

`post-checkout`

在`git checkout`运行后，会被调用。可以根据你项目环境用它调整你的工作目录。

`post-merge`

在`git merge`成功后，被调用。可以用它恢复Git无法跟踪的工作区数据，比如权限数据。

`pre-push`

在`git push`运行期间，更新远程引用但没有传送对象时被调用。它接受远程分支的名字和位置作为参数，同时从标准输入中读取一系列待更新的引用。

`pre-auto-gc`

在`git gc --auto`进行垃圾回收前调用，可以用它来提醒现在要回收垃圾了。

### 8.3.3 服务器端钩子

作为系统管理员，你还可以使用若干服务器端钩子对项目强制执行各种类型的策略。

这些钩子的脚本在推送服务器之前和之后运行。

`pre-receive`

处理来自客户端推送时，最先被调用的是`pre-receive`。它从标准输入获取一系列被推送的引用。如果以非零值退出，所有的推送内容都不会被接受。

`update`

它会为每一个准备更新的分支各运行一次。

假如推动者同时向多个分支推送内容，`pre-receive`只运行一次，`update`则会为每一个被推送的分支运行一次。

`post-receive`

挂钩在整个过程完结以后运行，可以用来更新其他系统服务或者通知用户。

--------------------------------------------------------------------------------

## 8.4 使用强制策略的一个例子

建立一个Git工作流程：检查提交信息的格式，并指定只能由特定用户修改项目中特定子目录。

编写一个客户端脚本提示开发人员他们的推送是否会被拒绝，以及一个服务器端脚本来实际执行这些策略。

例子中脚本是用Ruby编写的。

所有Git自带的钩子脚本都是使用`Perl`或`Bash`写的。

### 8.4.1 服务器端钩子

所有服务器端的工作都将在`hooks`目录下的`update`脚本中完成。

`update`脚本会为每一个提交的分支各运行一次，它接受三个参数。

1. 被推送的引用的名字

2. 推送前分支的修订版本

3. 用户准备推送的修订版本

如果使用SSH推送，还要知道此次推动的用户信息。

如果允许所有操作都通过公钥授权的账号，必须要通过一个shell包装脚本依据公钥来判断用户的身份，并相应的设置环境变量来表示用户的身份。

下方就是假设`$USER`环境变量里存储了当前连接用户的身份，你的update脚本首先搜集一切需要的信息：

```
#!/usr/bin/env ruby

$refname = ARGV[0]
$oldrev  = ARGV[1]
$newrev  = ARGV[2]
$user    = ENV['USER']

puts "Enforcing Policies..."
puts "(#{$refname}) (#{$oldrev[0,6]}) (#{$newrev[0,6]})"
```

- 指定特殊的提交信息格式

第一项任务是每一条提交信息都必须遵循某种特殊的格式。

假如每一个信息必须包含一条`ref:1234`的字符串，要逐一检查每一条推送上来的提交内容，看看提交信息是否包含这么一个字符串，然后，如果某个提交里不包含字符串，以非零返回，退出并拒绝推送。

把`$newrev`和`$oldrev`变量的值传给`git rev-list`的Git底层命令，你可以获取所有提交SHA-1值。`git rev-list`只输出SHA-1值，没有其他信息。

**例子：**

```
$ git rev-list 538c33..d14fc7
d14fc7c847ab946ec39590d87783c69b031bdfb7
9f585da4401b0a3999e84113824d15245c13f0be
234071a1be950e2a8d078e6141f5cd20c1e61ad3
dfa04c9ef3d5197182f13fb5b9b1fb7717d2222a
17716ec0f1ff5c77eff40b7fe912f9f6cfd0e475
```

下一步实现从每一个提交中提取出提交信息。使用另一个`git cat-file`的底层命令来获取原始的提交数据。

```
$ git cat-file commit ca82a6
tree cfda3bf379e4f8dba8717dee55aab78aef7f4daf
parent 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
author Scott Chacon <schacon@gmail.com> 1205815931 -0700
committer Scott Chacon <schacon@gmail.com> 1240030591 -0700

changed the version number
```

通过 SHA-1 值获得提交中的提交信息的一个简单办法是找到提交的第一个空行，然后取从它往后的所有内容。 可以使用 Unix 系统的 `sed` 命令来实现该效果：

```
$ git cat-file commit ca82a6 | sed '1,/^$/d'
changed the version number
```

下面写判处脚本，返回非零值，整个脚本如下：

```
$regex = /\[ref: (\d+)\]/

# 指定自定义的提交信息格式
def check_message_format
  missed_revs = `git rev-list #{$oldrev}..#{$newrev}`.split("\n")
  missed_revs.each do |rev|
    message = `git cat-file commit #{rev} | sed '1,/^$/d'`
    if !$regex.match(message)
      puts "[POLICY] Your message is not formatted correctly"
      exit 1
    end
  end
end
check_message_format
```

这样不符合指定规则的提交都会遭到拒绝。

- 指定基于用户的访问权限控制列表（ACL）系统

假定你需要添加一个使用访问权限控制列表的机制，来指定哪些用户对项目的哪些部分有推送权限。

为了实现这一点，要把相关的规则写入位于服务器原始Git仓库的acl中，还需要让`update`钩子检阅规则，审视推送的提交内容中被修改的所有文件，然后决定用户是否对所有这些文件都有权限。

先从写一个ACL文件开始，下方是ACL文件的设置方法。

第一项，内容是`avail`或者`unavail`。

第二项，用逗号分隔适用该规则的用户列表。

第三项，适用该规则的路径。

第四项，使用`|`隔开，各命令。

ACL文件设置如下：

```
avail|nickh,pjhyett,defunkt,tpw
avail|usinclair,cdickens,ebronte|doc
avail|schacon|lib
avail|schacon|tests
```

下面把这些数据读入你要用到的数据结构里。暂时只实现`avail`规则。

下方是生成一个关联数组的方法，它的键是用户名，值是一个由该用户有写入权限的所有目录组成的数据：

```
def get_acl_access_data(acl_file)
  # 读取ACL数据
  acl_file = File.read(acl_file).split("\n").reject { |line| line == '' }
  access = {}
  acl_file.each do |line|
    avail, users, path = line.split('|')
    next unless avail == 'avail'
    users.split(',').each do |user|
      access[user] ||= []
      access[user] << path
    end
  end
  access
end
```

对刚刚设置的ACL规则文件使用，这个`get_acl_access_date`方法返回的数据结构如下：

```
{"defunkt"=>[nil],
 "tpw"=>[nil],
 "nickh"=>[nil],
 "pjhyett"=>[nil],
 "schacon"=>["lib", "tests"],
 "cdickens"=>["doc"],
 "usinclair"=>["doc"],
 "ebronte"=>["doc"]}
```

接下来，需要找出提交都修改了哪些路径，从而才能保证推送者对所有这些路径都有权限。

使用`git log`的`--name-only`选项，可以很容易的找出一次提交里修改的文件：

```
$ git log -1 --name-only --pretty=format:'' 9f585d

README
lib/test.rb
```

使用`get_acl_access_data`返回的ACL结构来一一核对每次提交修改的文件列表，就能找出该用户是否有权限推送所有的提交内容：

```
# 仅允许特定用户修改项目中的特定子目录
def check_directory_perms
  access = get_acl_access_data('acl')

  # 检查是否有人在向他没有权限的地方推送内容
  new_commits = `git rev-list #{$oldrev}..#{$newrev}`.split("\n")
  new_commits.each do |rev|
    files_modified = `git log -1 --name-only --pretty=format:'' #{rev}`.split("\n")
    files_modified.each do |path|
      next if path.size == 0
      has_file_access = false
      access[$user].each do |access_path|
        if !access_path  # 用户拥有完全访问权限
           || (path.start_with? access_path) # 或者对此路径有访问权限
          has_file_access = true
        end
      end
      if !has_file_access
        puts "[POLICY] You do not have access to push to #{path}"
        exit 1
      end
    end
  end
end

check_directory_perms
```

通过`git rev-list`获取推送到服务器的所有提交。接着，对于每一个提交，找出它修改的文件，然后，确保推送者具有这些文件的推送权限。

- 测试一下

把上面的代码放到`.git/hooks/update`文件里，运行`chmod u+x .git/hooks/update`,然后推送一个不符合格式的提交，你会得到以下的提示：

```
$ git push -f origin master
Counting objects: 5, done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 323 bytes, done.
Total 3 (delta 1), reused 0 (delta 0)
Unpacking objects: 100% (3/3), done.
Enforcing Policies...
(refs/heads/master) (8338c5) (c5b616)
[POLICY] Your message is not formatted correctly
error: hooks/update exited with error code 1
error: hook declined to update refs/heads/master
To git@gitserver:project.git
 ! [remote rejected] master -> master (hook declined)
error: failed to push some refs to 'git@gitserver:project.git'
```

这有几个信息。

首先可以看到钩子运行的起点。

```
Enforcing Policies...
(refs/heads/master) (fb8c72) (c56860)
```

这是`update`脚本开头输出到标准输出的。

下一个值得注意的部分是错误信息。

```
[POLICY] Your message is not formatted correctly
error: hooks/update exited with error code 1
error: hook declined to update refs/heads/master
```

第一行是我们脚本输出的，剩下两行是Git在告诉我们`update`脚本退出时返回了非零值而推送被拒绝。最后

```
To git@gitserver:project.git
 ! [remote rejected] master -> master (hook declined)
error: failed to push some refs to 'git@gitserver:project.git'
```

每个被钩子拒绝的推送都收到一个`remote rejected`信息，它告诉你是钩子无法成功运行导致推送的拒绝。

而后，只要`update`脚本存在并且可执行，我们的版本中永远都不会包含不符合格式的提交信息。

### 8.4.2 客户端钩子

在服务端推送时被拒绝是非常不愉快的，可以给客户端一些钩子，在他们出错时给出警告。

所以必须通过其他途径把这些钩子分发到用户的`.git/hooks`目录并设置为可执行文件。虽然你可以在相同或单独项目里加入并分发钩子，但是Git不会自动设置它。

首先，在每次提交前核查提交信息，这样才能确保服务器不会因为不合条件的提交信息而拒绝更改。

为了这个目的，你可以增加`commit-msg`钩子。如果使用钩子来读取作为第一个参数传递的提交信息，然后与规定的格式作比较，你就可以使Git在提交信息不对的情况下拒绝提交。

```
#!/usr/bin/env ruby
message_file = ARGV[0]
message = File.read(message_file)

$regex = /\[ref: (\d+)\]/

if !$regex.match(message)
  puts "[POLICY] Your message is not formatted correctly"
  exit 1
end
```

脚本位于正确的位置`.git/hooks/commit-msg`并可执行时，提交信息的格式又是不正确的，你会看到：

```
$ git commit -am 'test'
[POLICY] Your message is not formatted correctly
```

在这个示例中，提交没有成功。然而提交注释信息符合要求，Git会允许提交：

```
$ git commit -am 'test [ref: 132]'
[master e05c914] test [ref: 132]
 1 file changed, 1 insertions(+), 0 deletions(-)
```

接下来要保证没有修改到ACL允许范围之外的文件。假设你的`.git`目录下有前面使用过的ACL文件，那么下方的`pre-commit`脚本将把里面的规定执行起来：

```
#!/usr/bin/env ruby

$user    = ENV['USER']

# [ 插入上文中的 get_acl_access_data 方法 ]

# 仅允许特定用户修改项目中的特定子目录
def check_directory_perms
  access = get_acl_access_data('.git/acl')

  files_modified = `git diff-index --cached --name-only HEAD`.split("\n")
  files_modified.each do |path|
    next if path.size == 0
    has_file_access = false
    access[$user].each do |access_path|
    if !access_path || (path.index(access_path) == 0)
      has_file_access = true
    end
    if !has_file_access
      puts "[POLICY] You do not have access to push to #{path}"
      exit 1
    end
  end
end

check_directory_perms
```

这和服务器的脚本几乎一样，除了两个重要区别。

第一，ACL文件的位置不同，因为这个脚本在当前工作目录运行，而非`.git`目录。

第二，获取被修改文件列表的方式。在服务器时使用查看提交记录的方式。而本地还没有提交，所以这个列表只能从暂存区获取。

_注意：假定的是本地用户和推送到远程服务器端的相同。如果不同，则需要手动设置一下`$USER`变量。_

还有确保推送内容中不包含非快进的引用。出现一个不是快进的引用有两种情况，一种是在某个已经推送过的提交上作变基，一种是从本地推送一个错误的分支到远程分支上。

假定为了执行这个策略，已经在服务器上配置好了`receive.denyDeletes`和`receive.denyNonFastForwards`,而唯一需要避免的是某个已经推送过提交作变基。

下方是用来检查这个问题的`pre-rebase`脚本示例。它获取所有待重写的提交列表，然后检查它们是否存在于远程引用中。一旦发现其中一个提交是某个远程引用的，它就终止此次变基：

```
#!/usr/bin/env ruby

base_branch = ARGV[0]
if ARGV[1]
  topic_branch = ARGV[1]
else
  topic_branch = "HEAD"
end

target_shas = `git rev-list #{base_branch}..#{topic_branch}`.split("\n")
remote_refs = `git branch -r`.split("\n").map { |r| r.strip }

target_shas.each do |sha|
  remote_refs.each do |remote_ref|
    shas_pushed = `git rev-list ^#{sha}^@ refs/remotes/#{remote_ref}`
    if shas_pushed.split("\n").include?(sha)
      puts "[POLICY] Commit #{sha} has already been pushed to #{remote_ref}"
      exit 1
    end
  end
end
```

`SHA^@` 会被解析成该提交的所有父提交。该命令会列出远程分支最新的提交中可到达的。

这个解决方案主要问题是`它可能很慢，而且常常没有必要`。只有不使用`-f`强制推送，服务器就会自动给出警告并且拒绝接受推送。

--------------------------------------------------------------------------------

# 9\. Git 与其他系统

使用到的几率不大，如果需要

阅读用户[文档](https://git-scm.com/book/zh/v2/Git-%E4%B8%8E%E5%85%B6%E4%BB%96%E7%B3%BB%E7%BB%9F-%E4%BD%9C%E4%B8%BA%E5%AE%A2%E6%88%B7%E7%AB%AF%E7%9A%84-Git)
