# 10. Git 内部原理

介绍Git内部工作原理和实现方式。

## 10.1 底层命令和高层命令

通过`checkout`、`branch`、`remote`等30个此类命令玩转Git。

Git命令设计成能以UNIX命令行的风格连接在一起。也可以由脚本调用，来完成工作。这部分命令一般称为`底层`命令，而那些更友好的命令被称为`高层`命令。

前九章探讨的高层命令，本章将面对底层命令。底层命令可以了解Git内部工作机制，也有助于说明Git如何完成工作，以及它为何如运作。多数底层命令并不面向最终用户，它们更适合作为新命令和自定义脚本的组成部分。

当在一个新目录或已有目录执行`git init`时，Git会创建一个`.git`目录。这个目录包含了几乎所有Git存储和操作的对象。如若想备份或复制一个版本库，只需把目录拷贝到另一处即可。

本章探讨的内容，均在`.git`目录内,该目录的结构如下：

	$ ls -F1
	HEAD
	config*
	description
	hooks/
	info/
	objects/
	refs/

上方目录为一个通过`git init`创建的全新仓库。

说明:

|名称|类型|说明|
|:----:|:----:|:----|
|description|文件|仅供GitWeb程序使用。|
|config|文件|项目特有的配置选项。|
|info|目录|全局性排除，用来放置不希望记录在`.gitignore`文件中忽略文件。|
|hooks|目录|包含客户端或服务端的钩子脚本。|
|HEAD|文件|指示目前被检出的分支|
|index|文件|（未创建）暂存区信息|
|objects|目录|存储所有的数据内容|
|refs|目录|存储指向数据（分支）的提交对象的指针|
	

-----

## 10.2 Git 对象

Git是一个内容寻址文件系统。

这意味着，Git的核心部分是一个简单的键值对数据库。你可以向该数据库插入任意内容，它会返回一个键值，通过该键值可以在任意时刻再次检索该内容。

可以通过底层命令`hash-object`来演示上述效果——该命令可将任意数据保存于`.git`目录，并返回相应的键值。

首先，我们需要初始化一个新的Git版本库，并确认`objects`目录为空：
	
	$ git init test
	Initialized empty Git repository in /tmp/test/.git/
	$ cd test
	$ find .git/objects
	.git/objects
	.git/objects/info
	.git/objects/pack
	$ find .git/objects -type f

可以看到Git对`objects`目录进行了初始化，并创建了`pack`和`info`子目录，但均为空。接着，往Git数据库存入一些文本：
	
	$ echo 'test content' | git hash-object -w --stdin
	d670460b4b4aece5915caf5c68d12f560a9fe3e4

`-w`选项指示`hash-object`命令存储数据对象，若不指定此选项，则该命令仅返回对应的键值。

`--stdin`选项则指示该命令从标准输入读取内容，若不指定，则在命令尾部给出待存储文件的路径。

该命令输入一个40字符的校验，这是一个SHA-1哈希值，一个将待存储的数据外加一个头部信息一起做SHA-1校验运算而产生的校验。

现在可以查看Git是如何存储数据的：

	$ find .git/objects -type f
	.git/objects/d6/70460b4b4aece5915caf5c68d12f560a9fe3e4

可以在`objects`目录下看到一个文件。这就是开始时Git存储内容的方式。

一个文件对应一条内容，以该内容加上特定头部信息的SHA-1为文件命名。

校验的前两个字符用于命名子目录，余下的38字符则用作文件名。

可以通过`cat-file`命令从Git取回数据。加`-p`选项可以自动判断内容的类型，并显示格式化的内容。
	
	$ git cat-file -p d670460b4b4aece5915caf5c68d12f560a9fe3e4
	test content
	
至此，掌握了向Git中存入内容，以及如何将它们取出。

同样可以将这些操作应用于文件中的内容。

例如，对一个文件进行版本控制。首先，创建一个新文件，将其内容存入数据库：

	$ echo 'version 1' > test.txt
	$ git hash-object -w test.txt
	83baae61804e65cc73a7201a7252750c76066a30

接着，向文件里写入新内容，并再次将其存入数据库：
	
	$ echo 'version 2' > test.txt
	$ git hash-object -w test.txt
	1f7a7a472abf3dd9643fd615f6da379c4acb3e3a

数据库记录下了该文件的两个不同版本，当然之前我们存入的第一条内容也还在：

	$ find .git/objects -type f
	.git/objects/1f/7a7a472abf3dd9643fd615f6da379c4acb3e3a
	.git/objects/83/baae61804e65cc73a7201a7252750c76066a30
	.git/objects/d6/70460b4b4aece5915caf5c68d12f560a9fe3e4

现在可以把文件内容恢复到第一个版本：

	$ git cat-file -p 83baae61804e65cc73a7201a7252750c76066a30 > test.txt
	$ cat test.txt
	version 1

或第二个版本：

	$ git cat-file -p 1f7a7a472abf3dd9643fd615f6da379c4acb3e3a > test.txt
	$ cat test.txt
	version 2
	
然而，记住文件的每一个版本所对应的 SHA-1 值并不现实；另一个问题是，在这个（简单的版本控制）系统中，文件名并没有被保存——我们仅保存了文件的内容。 上述类型的对象我们称之为数据对象（blob object）。 利用 `cat-file -t` 命令，可以让 Git 告诉我们其内部存储的任何对象类型，只要给定该对象的 SHA-1 值：

	$ git cat-file -t 1f7a7a472abf3dd9643fd615f6da379c4acb3e3a
	blob
	
### 10.2.1 树对象

树对象，它解决文件名保存的问题，也允许将多个文件组织到一起。（实际上是一个文件夹。）

Git以一种类似于UNIX文件系统的方式存储内容，但作了些简化。

所有内容均以树对象和数据对象的形式存储，其中树对象对应了UNIX中的目录项，数据对象大致上对应了inodes或文件内容。

一个树对象包含一条或多条树对象记录，每条记录含有一个指向数据对象或者子树对象的SHA-1指针，以及相应的模式、类型、文件名信息。

例如，某项目当前对应的最新树对象可能是：

	$ git cat-file -p master^{tree}
	100644 blob a906cb2a4a904a152e80877d4088654daad0c859      README
	100644 blob 8f94139338f9404f26296befa88755fc2598c289      Rakefile
	040000 tree 99f1a6d12cb4b6f19c8655fca46c3ecf317074e0      lib

`master^{tree}`语法表示`master`分支上最新的提交所指向的树对象。

*注意：lib子目录,并不是一个数据对象，而是一个指针，其指向的是另一个树对象：*

	$ git cat-file -p 99f1a6d12cb4b6f19c8655fca46c3ecf317074e0
	100644 blob 47c6340d6459e05787f644c2447d2595f5d3a54b      simplegit.rb
	
简单的说，Git内部存储的数据有点像下图：

![](https://git-scm.com/book/en/v2/book/10-git-internals/images/data-model-1.png)

创建自己的树对象，通常，Git根据某一时刻暂存区，所表示的状态创建并记录一个对应的树对象，如此重复便可以依次记录一系列的树对象。

因此，为创建一个树对象，首先需要通过暂存一些文件来创建一个暂存区。

可以使用`update-index`为一个单独文件创建一个暂存区。把`test.txt`文件的首个版本加入新的暂存区。

`update-index`命令必须加`--add`选项，因为文件并不在暂存区中。还有`--cacheinfo`选项，因为要添加的文件位于Git数据库中，而不是当前目录下。同时，需要指定文件模式、SHA-1 与 文件名：

	$ git update-index --add --cacheinfo 100644 \
	  83baae61804e65cc73a7201a7252750c76066a30 test.txt

指定的文件模式为`100644`，表明这是一个普通文件。`100755`,表示一个可执行文件。`120000`,表示一个符合链接。

现在，可以通过`write-tree`命令将暂存区内容写入一个树对象，此处无需`-w`选项。

`-w`选项，如果某个树对象此前并不存在，当调用`write-tree`命令时，它会根据当前暂存区状态自动创建一个新的树对象：
	
	$ git write-tree
	d8329fc1cc938780ffdd9f94e0d364e0ea74f579
	$ git cat-file -p d8329fc1cc938780ffdd9f94e0d364e0ea74f579
	100644 blob 83baae61804e65cc73a7201a7252750c76066a30      test.txt

不妨验证一下它确实是一个树对象：

	$ git cat-file -t d8329fc1cc938780ffdd9f94e0d364e0ea74f579
	tree
	
接着我们来创建一个新的树对象，它包括`test.txt`文件的第二个版本，以及一个新的文件：

	$ echo 'new file' > new.txt
	$ git update-index test.txt
	$ git update-index --add new.txt

暂存区现在包含了`test.txt`文件的新版本，和一个新文件`new.txt`。记录下这个目录树，然后观察它的结构：

	$ git write-tree
	0155eb4229851634a0f03eb265b69f5a2d56f341
	$ git cat-file -p 0155eb4229851634a0f03eb265b69f5a2d56f341
	100644 blob fa49b077972391ad58037050f2a75f74e3671e92      new.txt
	100644 blob 1f7a7a472abf3dd9643fd615f6da379c4acb3e3a      test.txt

新的树对象包含两条文件记录，同时`test.txt`的SHA-1值（`1f7a7a`）是先前值的`第二版`。

将第一个树对象加入第二个树对象，使其成为新的树对象的一个子目录。

通过调用`read-tree`命令，可以把树对象读入暂存区。

例子中，通过对`read-tree`指定`--prefix`选项，将一个已有的树对象作为子树读入暂存区：

	$ git read-tree --prefix=bak d8329fc1cc938780ffdd9f94e0d364e0ea74f579
	$ git write-tree
	3c4e9cd789d88d8d89c1073707c3585e41b0e614
	$ git cat-file -p 3c4e9cd789d88d8d89c1073707c3585e41b0e614
	040000 tree d8329fc1cc938780ffdd9f94e0d364e0ea74f579      bak
	100644 blob fa49b077972391ad58037050f2a75f74e3671e92      new.txt
	100644 blob 1f7a7a472abf3dd9643fd615f6da379c4acb3e3a      test.txt
	
如果基于这个新的树对象创建一个工作目录，你会发现工作目录的根目录包含两个文件以及一个名为`bak`的子目录，该子目录包含`test.txt`文件的第一个版本。

可以认为Git内部存储着用于表示上述结构的数据：

![](https://git-scm.com/book/en/v2/book/10-git-internals/images/data-model-2.png)

### 10.2.2 提交对象

现在有三个树对象，分别代表了我们想要跟踪的不同项目快照。然而问题是，想重新使用这些快照，必须知道所有三个SHA-1值，并且，你也不知道谁保存了这些快照，在什么时刻保存的，以及为什么保存这些快照。以上这些，正是提交对象能为保存的基本信息。

可以通过调用`commit-tree`命令创建一个提交对象，需要指定一个树对象的SHA-1值，以及该提交的父提交对象。

我们从之前创建的第一个树对象开始：
	
	$ echo 'first commit' | git commit-tree d8329f
	fdf4fc3344e67ab068f836878b6c4951e3b15f3dw

现在可以通过`cat-file`命令查看新提交对象：

	$ git cat-file -p fdf4fc3
	tree d8329fc1cc938780ffdd9f94e0d364e0ea74f579
	author Scott Chacon <schacon@gmail.com> 1243040974 -0700
	committer Scott Chacon <schacon@gmail.com> 1243040974 -0700
	
	first commit

提交对象的格式很简单：先指定一个顶层树对象，代表当前项目快照，然后是作者/提交者信息（`user.name`和`user.email`配置来设定，外加一个时间戳），留空一行，最后是提交注释。

接着，我们创建另外两个提交对象，它们分别引用各自的上一个提交：

	$ echo 'second commit' | git commit-tree 0155eb -p fdf4fc3
	cac0cab538b970a37ea1e769cbbde608743bc96d
	$ echo 'third commit'  | git commit-tree 3c4e9c -p cac0cab
	1a410efbd13591db07496601ebc7a059dd55cfe9

这三个提交对象分别指向之前创建的三个树对象快照中的一个。

现在，如果对最后一个提交的SHA-1值运行`git log`命令，会发现，可以查看到提交历史了：

	$ git log --stat 1a410e
	commit 1a410efbd13591db07496601ebc7a059dd55cfe9
	Author: Scott Chacon <schacon@gmail.com>
	Date:   Fri May 22 18:15:24 2009 -0700
	
		third commit
	
	 bak/test.txt | 1 +
	 1 file changed, 1 insertion(+)
	
	commit cac0cab538b970a37ea1e769cbbde608743bc96d
	Author: Scott Chacon <schacon@gmail.com>
	Date:   Fri May 22 18:14:29 2009 -0700
	
		second commit
	
	 new.txt  | 1 +
	 test.txt | 2 +-
	 2 files changed, 2 insertions(+), 1 deletion(-)
	
	commit fdf4fc3344e67ab068f836878b6c4951e3b15f3d
	Author: Scott Chacon <schacon@gmail.com>
	Date:   Fri May 22 18:09:34 2009 -0700
	
	    first commit
	
	 test.txt | 1 +
	 1 file changed, 1 insertion(+)

上方例子中，在不使用任何上层命令，仅凭几个底层操作便完成了一个Git提交历史的创建。这就是每次我们运行`git add`和`git commit`命令时，Git所做的工作。

将被改写的文件保存为数据对象，更新暂存区，记录树对象，最后创建一个指明了顶层树对象和父提交的提交对象。

这三种主要的Git对象，数据对象、树对象、提交对象，最初均以单独文件的形式保存在`.git/objects`目录下。

下面列出了目前示例目录内的所有对象，辅以各自所保存内容的注释：

	$ find .git/objects -type f
	.git/objects/01/55eb4229851634a0f03eb265b69f5a2d56f341 # tree 2
	.git/objects/1a/410efbd13591db07496601ebc7a059dd55cfe9 # commit 3
	.git/objects/1f/7a7a472abf3dd9643fd615f6da379c4acb3e3a # test.txt v2
	.git/objects/3c/4e9cd789d88d8d89c1073707c3585e41b0e614 # tree 3
	.git/objects/83/baae61804e65cc73a7201a7252750c76066a30 # test.txt v1
	.git/objects/ca/c0cab538b970a37ea1e769cbbde608743bc96d # commit 2
	.git/objects/d6/70460b4b4aece5915caf5c68d12f560a9fe3e4 # 'test content'
	.git/objects/d8/329fc1cc938780ffdd9f94e0d364e0ea74f579 # tree 1
	.git/objects/fa/49b077972391ad58037050f2a75f74e3671e92 # new.txt
	.git/objects/fd/f4fc3344e67ab068f836878b6c4951e3b15f3d # commit 1

如果跟踪所有的内部指针，将得到一个类似下方的对象关系图：

![](https://git-scm.com/book/en/v2/book/10-git-internals/images/data-model-3.png)


### 10.2.3 对象存储

在存储内容时，会有一个头部信息一并被保存。

现在看看Git是如何存储对象的。

通过在Ruby中交互演示，将看到数据对象`what is up ,doc`是如何存储的。

通过`irb`命令启动Ruby交互模式：

	$ irb
	>> content = "what is up, doc?"
	=> "what is up, doc?"

Git以对象类型作为开头来构造一个头部信息，这里是`blob`字符串。接着Git会添加一个空格，随后是数据内容的长度，最后是一个空字节：

	>> header = "blob #{content.length}\0"
	=> "blob 16\u0000"

Git会将上述信息和原始数据拼接起来，并计算出这条新内容的SHA-1校验。

在Ruby中可以先通过`require`命令导入SHA-1 digest 库，然后对目标字符串调用`Digest::SHA1.hexdigest()`

	>> store = header + content
	=> "blob 16\u0000what is up, doc?"
	>> require 'digest/sha1'
	=> true
	>> sha1 = Digest::SHA1.hexdigest(store)
	=> "bd9dbf5aae1a3862dd1526723246b20206e5fc37"
	
Git会通过`zlib`压缩这条新内容。在Ruby中可以借助`zlib`库做到这一点。

先导入相应的库，然后对目标内容调用`Zlib::Deflate.deflate()`
	
	>> require 'zlib'
	=> true
	>> zlib_content = Zlib::Deflate.deflate(store)
	=> "x\x9CK\xCA\xC9OR04c(\xCFH,Q\xC8,V(-\xD0QH\xC9O\xB6\a\x00_\x1C\a\x9D"

最后，需要将这条经由`zlib`压缩的内容写入磁盘上的某个对象。要先确定待写入对象的路径（SHA-1值得前两个字符作为子目录名称，后38个字符作为子目录内文件的名字）。

如果该子目录不存在，可以通过Ruby中的`FileUtils.mkdir_p() `函数来创建它。

接着，通过`File.open()`打开这个文件。

最后，对上一步中得到的文件调用`write()`函数，以向目标文件写入之前那条`zlib`压缩过的内容：
	
	>> path = '.git/objects/' + sha1[0,2] + '/' + sha1[2,38]
	=> ".git/objects/bd/9dbf5aae1a3862dd1526723246b20206e5fc37"
	>> require 'fileutils'
	=> true
	>> FileUtils.mkdir_p(File.dirname(path))
	=> ".git/objects/bd"
	>> File.open(path, 'w') { |f| f.write zlib_content }
	=> 32

这样就创建了一个有效的Git数据对象。所有的Git对象均以这种方式存储，区别仅在于类型识别。

另外两种对象类型的头部信息以字符串`commit`或`tree`开头，而不是`blob`。

**注册：虽然数据对象的内容几乎可以是任何东西，但提交对象和树对象的内容却有各自固定的格式。**

------

## 10.3 Git 引用

借助`git log 1a410e`这样的命令来浏览完整的提交历史，但为了能遍历那段历史从而找到所有相关对象，还是要记住`1a410e`是最后一个提交。我们需要一个文件来保存SHA-1值，并给文件起一个简单的名字，然后用这个名字指针来替代原始的SHA-1值。

在Git里，这样的文件被称为`引用（references，或缩写为refs）`，你可以在`.git/refs`目录下找到这类含有SHA-1值的文件。

在目前的项目中，这个目录没有包含任何文件，但它包含了一个简单的目录结构:

	$ find .git/refs
	.git/refs
	.git/refs/heads
	.git/refs/tags
	$ find .git/refs -type f

若要创建一个新引用来帮助记忆最新提交所在的位置，只需要简单地做如下操作：

	$ echo "1a410efbd13591db07496601ebc7a059dd55cfe9" > .git/refs/heads/master

现在，你可以在Git命令中使用刚创新的引用来代替SHA-1值了：

	$ git log --pretty=oneline  master
	1a410efbd13591db07496601ebc7a059dd55cfe9 third commit
	cac0cab538b970a37ea1e769cbbde608743bc96d second commit
	fdf4fc3344e67ab068f836878b6c4951e3b15f3d first commit

Git中不提倡直接编辑引用文件。如果想更新某个引用，Git提供了一个更加安全的命令`update-ref`来完成此事：

	$ git update-ref refs/heads/master 1a410efbd13591db07496601ebc7a059dd55cfe9

这基本就是Git分支的本质：一个指向某一系列提交之首的指针或引用。若想在第二个提交上创建一个分支，可以这么做：

	$ git update-ref refs/heads/test cac0ca

这个分支将只包含从第二个提交开始往前追溯记录：
	
	$ git log --pretty=oneline test
	cac0cab538b970a37ea1e769cbbde608743bc96d second commit
	fdf4fc3344e67ab068f836878b6c4951e3b15f3d first commit
	
至此，我们的Git数据库从概念上看起来像这样：

![](https://git-scm.com/book/en/v2/book/10-git-internals/images/data-model-4.png)


[TOC]


### 10.3.1 HEAD 引用

### 10.3.2 标签引用

### 10.3.3 远程引用

## 10.4 包文件

## 10.5 引用规格

### 10.5.1 引用规格推送

### 10.5.2 删除引用

## 10.6 传输协议

### 10.6.1 哑协议

### 10.6.2 智能协议

* 上传数据

	* SSH

	* HTTP(S)

* 下载数据

	* SSH

	* HTTP(S)

## 10.7 维护与数据恢复

### 10.7.1 维护

### 10.7.2 数据恢复

### 10.7.3 移除对象

## 10.8 环境变量

### 10.8.1 全局行为

### 10.8.2 版本库位置

### 10.8.3 路径规则

### 10.8.4 提交

### 10.8.5 网络

### 10.8.6 调试

### 10.8.7 其它