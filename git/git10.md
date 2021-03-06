<!--toc-->

- [10\. Git 内部原理](#10-git-内部原理)
	- [10.1 底层命令和高层命令](#101-底层命令和高层命令)
	- [10.2 Git 对象](#102-git-对象)
		- [10.2.1 树对象](#1021-树对象)
		- [10.2.2 提交对象](#1022-提交对象)
		- [10.2.3 对象存储](#1023-对象存储)
	- [10.3 Git 引用](#103-git-引用)
		- [10.3.1 HEAD 引用](#1031-head-引用)
		- [10.3.2 标签引用](#1032-标签引用)
		- [10.3.3 远程引用](#1033-远程引用)
	- [10.4 包文件](#104-包文件)
	- [10.5 引用规格](#105-引用规格)
		- [10.5.1 引用规格推送](#1051-引用规格推送)
		- [10.5.2 删除引用](#1052-删除引用)
	- [10.6 传输协议](#106-传输协议)
		- [10.6.1 哑协议](#1061-哑协议)
		- [10.6.2 智能协议](#1062-智能协议)
	- [10.7 维护与数据恢复](#107-维护与数据恢复)
		- [10.7.1 维护](#1071-维护)
		- [10.7.2 数据恢复](#1072-数据恢复)
		- [10.7.3 移除对象](#1073-移除对象)
	- [10.8 环境变量](#108-环境变量)
		- [10.8.1 全局行为](#1081-全局行为)
		- [10.8.2 版本库位置](#1082-版本库位置)
		- [10.8.3 路径规则](#1083-路径规则)
		- [10.8.4 提交](#1084-提交)
		- [10.8.5 网络](#1085-网络)
		- [10.8.6 比较和合并](#1086-比较和合并)
		- [10.8.7 调试](#1087-调试)
		- [10.8.8 其它](#1088-其它)

<!-- tocstop -->

--------------------------------------------------------------------------------

# 10\. Git 内部原理

介绍Git内部工作原理和实现方式。

## 10.1 底层命令和高层命令

通过`checkout`、`branch`、`remote`等30个此类命令玩转Git。

Git命令设计成能以UNIX命令行的风格连接在一起。也可以由脚本调用，来完成工作。这部分命令一般称为`底层`命令，而那些更友好的命令被称为`高层`命令。

前九章探讨的高层命令，本章将面对底层命令。底层命令可以了解Git内部工作机制，也有助于说明Git如何完成工作，以及它为何如运作。多数底层命令并不面向最终用户，它们更适合作为新命令和自定义脚本的组成部分。

当在一个新目录或已有目录执行`git init`时，Git会创建一个`.git`目录。这个目录包含了几乎所有Git存储和操作的对象。如若想备份或复制一个版本库，只需把目录拷贝到另一处即可。

本章探讨的内容，均在`.git`目录内,该目录的结构如下：

```
$ ls -F1
HEAD
config*
description
hooks/
info/
objects/
refs/
```

上方目录为一个通过`git init`创建的全新仓库。

说明:

名称          | 类型 | 说明
:---------- | :: | :-----------------------------------
description | 文件 | 仅供GitWeb程序使用。
config      | 文件 | 项目特有的配置选项。
info        | 目录 | 全局性排除，用来放置不希望记录在`.gitignore`文件中忽略文件。
hooks       | 目录 | 包含客户端或服务端的钩子脚本。
HEAD        | 文件 | 指示目前被检出的分支
index       | 文件 | （未创建）暂存区信息
objects     | 目录 | 存储所有的数据内容
refs        | 目录 | 存储指向数据（分支）的提交对象的指针

--------------------------------------------------------------------------------

## 10.2 Git 对象

Git是一个内容寻址文件系统。

这意味着，Git的核心部分是一个简单的键值对数据库。你可以向该数据库插入任意内容，它会返回一个键值，通过该键值可以在任意时刻再次检索该内容。

可以通过底层命令`hash-object`来演示上述效果----该命令可将任意数据保存于`.git`目录，并返回相应的键值。

首先，我们需要初始化一个新的Git版本库，并确认`objects`目录为空：

```
$ git init test
Initialized empty Git repository in /tmp/test/.git/
$ cd test
$ find .git/objects
.git/objects
.git/objects/info
.git/objects/pack
$ find .git/objects -type f
```

可以看到Git对`objects`目录进行了初始化，并创建了`pack`和`info`子目录，但均为空。接着，往Git数据库存入一些文本：

```
$ echo 'test content' | git hash-object -w --stdin
d670460b4b4aece5915caf5c68d12f560a9fe3e4
```

`-w`选项指示`hash-object`命令存储数据对象，若不指定此选项，则该命令仅返回对应的键值。

`--stdin`选项则指示该命令从标准输入读取内容，若不指定，则在命令尾部给出待存储文件的路径。

该命令输入一个40字符的校验，这是一个SHA-1哈希值，一个将待存储的数据外加一个头部信息一起做SHA-1校验运算而产生的校验。

现在可以查看Git是如何存储数据的：

```
$ find .git/objects -type f
.git/objects/d6/70460b4b4aece5915caf5c68d12f560a9fe3e4
```

可以在`objects`目录下看到一个文件。这就是开始时Git存储内容的方式。

一个文件对应一条内容，以该内容加上特定头部信息的SHA-1为文件命名。

校验的前两个字符用于命名子目录，余下的38字符则用作文件名。

可以通过`cat-file`命令从Git取回数据。加`-p`选项可以自动判断内容的类型，并显示格式化的内容。

```
$ git cat-file -p d670460b4b4aece5915caf5c68d12f560a9fe3e4
test content
```

至此，掌握了向Git中存入内容，以及如何将它们取出。

同样可以将这些操作应用于文件中的内容。

例如，对一个文件进行版本控制。首先，创建一个新文件，将其内容存入数据库：

```
$ echo 'version 1' > test.txt
$ git hash-object -w test.txt
83baae61804e65cc73a7201a7252750c76066a30
```

接着，向文件里写入新内容，并再次将其存入数据库：

```
$ echo 'version 2' > test.txt
$ git hash-object -w test.txt
1f7a7a472abf3dd9643fd615f6da379c4acb3e3a
```

数据库记录下了该文件的两个不同版本，当然之前我们存入的第一条内容也还在：

```
$ find .git/objects -type f
.git/objects/1f/7a7a472abf3dd9643fd615f6da379c4acb3e3a
.git/objects/83/baae61804e65cc73a7201a7252750c76066a30
.git/objects/d6/70460b4b4aece5915caf5c68d12f560a9fe3e4
```

现在可以把文件内容恢复到第一个版本：

```
$ git cat-file -p 83baae61804e65cc73a7201a7252750c76066a30 > test.txt
$ cat test.txt
version 1
```

或第二个版本：

```
$ git cat-file -p 1f7a7a472abf3dd9643fd615f6da379c4acb3e3a > test.txt
$ cat test.txt
version 2
```

然而，记住文件的每一个版本所对应的 SHA-1 值并不现实；另一个问题是，在这个（简单的版本控制）系统中，文件名并没有被保存----我们仅保存了文件的内容。 上述类型的对象我们称之为数据对象（blob object）。 利用 `cat-file -t` 命令，可以让 Git 告诉我们其内部存储的任何对象类型，只要给定该对象的 SHA-1 值：

```
$ git cat-file -t 1f7a7a472abf3dd9643fd615f6da379c4acb3e3a
blob
```

### 10.2.1 树对象

树对象，它解决文件名保存的问题，也允许将多个文件组织到一起。（实际上是一个文件夹。）

Git以一种类似于UNIX文件系统的方式存储内容，但作了些简化。

所有内容均以树对象和数据对象的形式存储，其中树对象对应了UNIX中的目录项，数据对象大致上对应了inodes或文件内容。

一个树对象包含一条或多条树对象记录，每条记录含有一个指向数据对象或者子树对象的SHA-1指针，以及相应的模式、类型、文件名信息。

例如，某项目当前对应的最新树对象可能是：

```
$ git cat-file -p master^{tree}
100644 blob a906cb2a4a904a152e80877d4088654daad0c859      README
100644 blob 8f94139338f9404f26296befa88755fc2598c289      Rakefile
040000 tree 99f1a6d12cb4b6f19c8655fca46c3ecf317074e0      lib
```

`master^{tree}`语法表示`master`分支上最新的提交所指向的树对象。

_注意：lib子目录,并不是一个数据对象，而是一个指针，其指向的是另一个树对象：_

```
$ git cat-file -p 99f1a6d12cb4b6f19c8655fca46c3ecf317074e0
100644 blob 47c6340d6459e05787f644c2447d2595f5d3a54b      simplegit.rb
```

简单的说，Git内部存储的数据有点像下图：

![](https://git-scm.com/book/en/v2/book/10-git-internals/images/data-model-1.png)

创建自己的树对象，通常，Git根据某一时刻暂存区，所表示的状态创建并记录一个对应的树对象，如此重复便可以依次记录一系列的树对象。

因此，为创建一个树对象，首先需要通过暂存一些文件来创建一个暂存区。

可以使用`update-index`为一个单独文件创建一个暂存区。把`test.txt`文件的首个版本加入新的暂存区。

`update-index`命令必须加`--add`选项，因为文件并不在暂存区中。还有`--cacheinfo`选项，因为要添加的文件位于Git数据库中，而不是当前目录下。同时，需要指定文件模式、SHA-1 与 文件名：

```
$ git update-index --add --cacheinfo 100644 \
  83baae61804e65cc73a7201a7252750c76066a30 test.txt
```

指定的文件模式为`100644`，表明这是一个普通文件。`100755`,表示一个可执行文件。`120000`,表示一个符合链接。

现在，可以通过`write-tree`命令将暂存区内容写入一个树对象，此处无需`-w`选项。

`-w`选项，如果某个树对象此前并不存在，当调用`write-tree`命令时，它会根据当前暂存区状态自动创建一个新的树对象：

```
$ git write-tree
d8329fc1cc938780ffdd9f94e0d364e0ea74f579
$ git cat-file -p d8329fc1cc938780ffdd9f94e0d364e0ea74f579
100644 blob 83baae61804e65cc73a7201a7252750c76066a30      test.txt
```

不妨验证一下它确实是一个树对象：

```
$ git cat-file -t d8329fc1cc938780ffdd9f94e0d364e0ea74f579
tree
```

接着我们来创建一个新的树对象，它包括`test.txt`文件的第二个版本，以及一个新的文件：

```
$ echo 'new file' > new.txt
$ git update-index test.txt
$ git update-index --add new.txt
```

暂存区现在包含了`test.txt`文件的新版本，和一个新文件`new.txt`。记录下这个目录树，然后观察它的结构：

```
$ git write-tree
0155eb4229851634a0f03eb265b69f5a2d56f341
$ git cat-file -p 0155eb4229851634a0f03eb265b69f5a2d56f341
100644 blob fa49b077972391ad58037050f2a75f74e3671e92      new.txt
100644 blob 1f7a7a472abf3dd9643fd615f6da379c4acb3e3a      test.txt
```

新的树对象包含两条文件记录，同时`test.txt`的SHA-1值（`1f7a7a`）是先前值的`第二版`。

将第一个树对象加入第二个树对象，使其成为新的树对象的一个子目录。

通过调用`read-tree`命令，可以把树对象读入暂存区。

例子中，通过对`read-tree`指定`--prefix`选项，将一个已有的树对象作为子树读入暂存区：

```
$ git read-tree --prefix=bak d8329fc1cc938780ffdd9f94e0d364e0ea74f579
$ git write-tree
3c4e9cd789d88d8d89c1073707c3585e41b0e614
$ git cat-file -p 3c4e9cd789d88d8d89c1073707c3585e41b0e614
040000 tree d8329fc1cc938780ffdd9f94e0d364e0ea74f579      bak
100644 blob fa49b077972391ad58037050f2a75f74e3671e92      new.txt
100644 blob 1f7a7a472abf3dd9643fd615f6da379c4acb3e3a      test.txt
```

如果基于这个新的树对象创建一个工作目录，你会发现工作目录的根目录包含两个文件以及一个名为`bak`的子目录，该子目录包含`test.txt`文件的第一个版本。

可以认为Git内部存储着用于表示上述结构的数据：

![](https://git-scm.com/book/en/v2/book/10-git-internals/images/data-model-2.png)

### 10.2.2 提交对象

现在有三个树对象，分别代表了我们想要跟踪的不同项目快照。然而问题是，想重新使用这些快照，必须知道所有三个SHA-1值，并且，你也不知道谁保存了这些快照，在什么时刻保存的，以及为什么保存这些快照。以上这些，正是提交对象能为保存的基本信息。

可以通过调用`commit-tree`命令创建一个提交对象，需要指定一个树对象的SHA-1值，以及该提交的父提交对象。

我们从之前创建的第一个树对象开始：

```
$ echo 'first commit' | git commit-tree d8329f
fdf4fc3344e67ab068f836878b6c4951e3b15f3dw
```

现在可以通过`cat-file`命令查看新提交对象：

```
$ git cat-file -p fdf4fc3
tree d8329fc1cc938780ffdd9f94e0d364e0ea74f579
author Scott Chacon <schacon@gmail.com> 1243040974 -0700
committer Scott Chacon <schacon@gmail.com> 1243040974 -0700

first commit
```

提交对象的格式很简单：先指定一个顶层树对象，代表当前项目快照，然后是作者/提交者信息（`user.name`和`user.email`配置来设定，外加一个时间戳），留空一行，最后是提交注释。

接着，我们创建另外两个提交对象，它们分别引用各自的上一个提交：

```
$ echo 'second commit' | git commit-tree 0155eb -p fdf4fc3
cac0cab538b970a37ea1e769cbbde608743bc96d
$ echo 'third commit'  | git commit-tree 3c4e9c -p cac0cab
1a410efbd13591db07496601ebc7a059dd55cfe9
```

这三个提交对象分别指向之前创建的三个树对象快照中的一个。

现在，如果对最后一个提交的SHA-1值运行`git log`命令，会发现，可以查看到提交历史了：

```
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
```

上方例子中，在不使用任何上层命令，仅凭几个底层操作便完成了一个Git提交历史的创建。这就是每次我们运行`git add`和`git commit`命令时，Git所做的工作。

将被改写的文件保存为数据对象，更新暂存区，记录树对象，最后创建一个指明了顶层树对象和父提交的提交对象。

这三种主要的Git对象，数据对象、树对象、提交对象，最初均以单独文件的形式保存在`.git/objects`目录下。

下面列出了目前示例目录内的所有对象，辅以各自所保存内容的注释：

```
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
```

如果跟踪所有的内部指针，将得到一个类似下方的对象关系图：

![](https://git-scm.com/book/en/v2/book/10-git-internals/images/data-model-3.png)

### 10.2.3 对象存储

在存储内容时，会有一个头部信息一并被保存。

现在看看Git是如何存储对象的。

通过在Ruby中交互演示，将看到数据对象`what is up ,doc`是如何存储的。

通过`irb`命令启动Ruby交互模式：

```
$ irb
>> content = "what is up, doc?"
=> "what is up, doc?"
```

Git以对象类型作为开头来构造一个头部信息，这里是`blob`字符串。接着Git会添加一个空格，随后是数据内容的长度，最后是一个空字节：

```
>> header = "blob #{content.length}\0"
=> "blob 16\u0000"
```

Git会将上述信息和原始数据拼接起来，并计算出这条新内容的SHA-1校验。

在Ruby中可以先通过`require`命令导入SHA-1 digest 库，然后对目标字符串调用`Digest::SHA1.hexdigest()`

```
>> store = header + content
=> "blob 16\u0000what is up, doc?"
>> require 'digest/sha1'
=> true
>> sha1 = Digest::SHA1.hexdigest(store)
=> "bd9dbf5aae1a3862dd1526723246b20206e5fc37"
```

Git会通过`zlib`压缩这条新内容。在Ruby中可以借助`zlib`库做到这一点。

先导入相应的库，然后对目标内容调用`Zlib::Deflate.deflate()`

```
>> require 'zlib'
=> true
>> zlib_content = Zlib::Deflate.deflate(store)
=> "x\x9CK\xCA\xC9OR04c(\xCFH,Q\xC8,V(-\xD0QH\xC9O\xB6\a\x00_\x1C\a\x9D"
```

最后，需要将这条经由`zlib`压缩的内容写入磁盘上的某个对象。要先确定待写入对象的路径（SHA-1值得前两个字符作为子目录名称，后38个字符作为子目录内文件的名字）。

如果该子目录不存在，可以通过Ruby中的`FileUtils.mkdir_p()`函数来创建它。

接着，通过`File.open()`打开这个文件。

最后，对上一步中得到的文件调用`write()`函数，以向目标文件写入之前那条`zlib`压缩过的内容：

```
>> path = '.git/objects/' + sha1[0,2] + '/' + sha1[2,38]
=> ".git/objects/bd/9dbf5aae1a3862dd1526723246b20206e5fc37"
>> require 'fileutils'
=> true
>> FileUtils.mkdir_p(File.dirname(path))
=> ".git/objects/bd"
>> File.open(path, 'w') { |f| f.write zlib_content }
=> 32
```

这样就创建了一个有效的Git数据对象。所有的Git对象均以这种方式存储，区别仅在于类型识别。

另外两种对象类型的头部信息以字符串`commit`或`tree`开头，而不是`blob`。

**注册：虽然数据对象的内容几乎可以是任何东西，但提交对象和树对象的内容却有各自固定的格式。**

--------------------------------------------------------------------------------

## 10.3 Git 引用

借助`git log 1a410e`这样的命令来浏览完整的提交历史，但为了能遍历那段历史从而找到所有相关对象，还是要记住`1a410e`是最后一个提交。我们需要一个文件来保存SHA-1值，并给文件起一个简单的名字，然后用这个名字指针来替代原始的SHA-1值。

在Git里，这样的文件被称为`引用（references，或缩写为refs）`，你可以在`.git/refs`目录下找到这类含有SHA-1值的文件。

在目前的项目中，这个目录没有包含任何文件，但它包含了一个简单的目录结构:

```
$ find .git/refs
.git/refs
.git/refs/heads
.git/refs/tags
$ find .git/refs -type f
```

若要创建一个新引用来帮助记忆最新提交所在的位置，只需要简单地做如下操作：

```
$ echo "1a410efbd13591db07496601ebc7a059dd55cfe9" > .git/refs/heads/master
```

现在，你可以在Git命令中使用刚创新的引用来代替SHA-1值了：

```
$ git log --pretty=oneline  master
1a410efbd13591db07496601ebc7a059dd55cfe9 third commit
cac0cab538b970a37ea1e769cbbde608743bc96d second commit
fdf4fc3344e67ab068f836878b6c4951e3b15f3d first commit
```

Git中不提倡直接编辑引用文件。如果想更新某个引用，Git提供了一个更加安全的命令`update-ref`来完成此事：

```
$ git update-ref refs/heads/master 1a410efbd13591db07496601ebc7a059dd55cfe9
```

这基本就是Git分支的本质：一个指向某一系列提交之首的指针或引用。若想在第二个提交上创建一个分支，可以这么做：

```
$ git update-ref refs/heads/test cac0ca
```

这个分支将只包含从第二个提交开始往前追溯记录：

```
$ git log --pretty=oneline test
cac0cab538b970a37ea1e769cbbde608743bc96d second commit
fdf4fc3344e67ab068f836878b6c4951e3b15f3d first commit
```

至此，我们的Git数据库从概念上看起来像这样：

![](https://git-scm.com/book/en/v2/book/10-git-internals/images/data-model-4.png)

### 10.3.1 HEAD 引用

现在的问题是，当你执行`git branch [分支名]`时，Git使用`HEAD文件`知道最新提交的SHA-1值。

HEAD文件是一个符号引用，指向目前所在的分支。

符合引用，它并不像普通引用那样包含一个SHA-1值，它是一个指向其他引用的指针。

查看`HEAD`文件的内容,一般如下

```
$ cat .git/HEAD
ref: refs/heads/master
```

如果执行`git checkout test`,Git会像这样更新`HEAD文件`:

```
$ cat .git/HEAD
ref: refs/heads/test
```

当执行`git commit`时，该命令会创建一个提交对象，并用HEAD文件中的引用指向SHA-1值，设置其父提交字段。

你也可以手动编辑该文件，可以安全的命令来代替手动修改`symbolic-ref`。可以用此命令查看HEAD引用对应的值：

```
$ git symbolic-ref HEAD
refs/heads/master
```

同样可以设置HEAD引用值：

```
$ git symbolic-ref HEAD refs/heads/test
$ cat .git/HEAD
ref: refs/heads/test
```

但是不能把符合引用设置为一个不符合引用格式的值：

```
$ git symbolic-ref HEAD test
fatal: Refusing to point HEAD outside of refs/
```

### 10.3.2 标签引用

Git中三种主要的对象类型已经介绍过了，还有第四种。标签对象（tag object）非常类似于一个提交对象。

它包含一个标签创建者信息、一个日期、一段注释信息，以及一个指针。

主要的区别在于，标签对象通常指向一个提交对象，而不是一个树对象。

它是一个不移动的分支引用，永远指向同一个提交对象，只不过给提交对象加一个更友好的名字罢了。

下方命令可以创建一个轻量标签：

```
$ git update-ref refs/tags/v1.0 cac0cab538b970a37ea1e769cbbde608743bc96d
```

这就是轻量标签的全部内容，一个固定的引用。

附注标签则更复杂一些，要创建一个附注标签，Git会创建一个标签对象，并记录一个引用来指向该标签对象，而不是直接指向提交对象。

可以通过创建一个附注标签来验证这个过程(`-a`选项指定了要创建的是一个附注标签)：

```
$ git tag -a v1.1 1a410efbd13591db07496601ebc7a059dd55cfe9 -m 'test tag'
```

通过下方命令查找SHA-1值：

```
$ cat .git/refs/tags/v1.1
9585191f37f7b0fb9444f35a9bf50de191beadc2
```

现在对SHA-1值运行`cat-file`命令：

```
$ git cat-file -p 9585191f37f7b0fb9444f35a9bf50de191beadc2
object 1a410efbd13591db07496601ebc7a059dd55cfe9
type commit
tag v1.1
tagger Scott Chacon <schacon@gmail.com> Sat May 23 16:48:58 2009 -0700

test tag
```

可以看到，object条目指向打了标签的那个提交对象的SHA-1值。另外要注意，标签对象并不是必须指向某个提交对象；你可以对任意类型的Git对象打标签。

在Git源码中，项目维护者将他们的GPG公钥添加为一个数据对象，然后对这个对象打了标签。

可以克隆一个Git版本库，然后通过执行下面命令查看：

```
$ git cat-file blob junio-gpg-pub
```

### 10.3.3 远程引用

第三种引用类型是远程引用，如果添加了一个远程版本库并对其执行过推送操作，Git会记录下最近一次推送操作时每一个分支所对应的值，并保存在`refs/remotes`目录下。

**例子：**

你可以添加一个叫做`origin`的远程版本库，然后把`master`分支推送上去：

```
$ git remote add origin git@github.com:schacon/simplegit-progit.git
$ git push origin master
Counting objects: 11, done.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (7/7), 716 bytes, done.
Total 7 (delta 2), reused 4 (delta 1)
To git@github.com:schacon/simplegit-progit.git
  a11bef0..ca82a6d  master -> master
```

此时，如果查看`refs/remotes/origin/master`文件，可以发现`origin`远程版本库的master分支对应的SHA-1值，就是最近一次与服务器通信时本地`master`分支的SHA-1值：

```
$ cat .git/refs/remotes/origin/master
ca82a6dff817ec66f44342007202690a93763949
```

远程引用与分支（位于`refs/heads`目录下的引用）之间最主要的区别在于，远程引用是只读的。虽然可以`git checkout`到某个远程引用，但是Git并不会将HEAD引用指向远程引用。因此，你永远不能通过`commit`命令来更新远程引用。git将这些远程引进作为记录远程服务器上各分支最后已知位置状态的书签来管理。

--------------------------------------------------------------------------------

## 10.4 包文件

重新回到示例Git版本库的对象数据库。

到目前为止，可以看到有11个对象。

对象类型 | 对象数量
:--: | :--:
数据对象 |  4
树对象  |  3
提交对象 |  3
标签对象 |  1
     |
 总计  |  11

使用命令查询结果：

```
$ find .git/objects -type f
.git/objects/01/55eb4229851634a0f03eb265b69f5a2d56f341 # tree 2
.git/objects/1a/410efbd13591db07496601ebc7a059dd55cfe9 # commit 3
.git/objects/1f/7a7a472abf3dd9643fd615f6da379c4acb3e3a # test.txt v2
.git/objects/3c/4e9cd789d88d8d89c1073707c3585e41b0e614 # tree 3
.git/objects/83/baae61804e65cc73a7201a7252750c76066a30 # test.txt v1
.git/objects/95/85191f37f7b0fb9444f35a9bf50de191beadc2 # tag
.git/objects/ca/c0cab538b970a37ea1e769cbbde608743bc96d # commit 2
.git/objects/d6/70460b4b4aece5915caf5c68d12f560a9fe3e4 # 'test content'
.git/objects/d8/329fc1cc938780ffdd9f94e0d364e0ea74f579 # tree 1
.git/objects/fa/49b077972391ad58037050f2a75f74e3671e92 # new.txt
.git/objects/fd/f4fc3344e67ab068f836878b6c4951e3b15f3d # commit 1
```

Git使用Zlib压缩文件，而且目标并没有存储太多东西，上述文件一共占用了925字节。

接下来，将会指引你添加一些大文件到版本库中。

为了便于展示，将添加一个大约22KB的`repo.rb`文件。

```
$ curl https://raw.githubusercontent.com/mojombo/grit/master/lib/grit/repo.rb > repo.rb
$ git add repo.rb
$ git commit -m 'added repo.rb'
[master 484a592] added repo.rb
 3 files changed, 709 insertions(+), 2 deletions(-)
 delete mode 100644 bak/test.txt
 create mode 100644 repo.rb
 rewrite test.txt (100%)
```

如果查看生成的树对象，可以看到`repo.rb`文件对应`数据对象`的SHA-1值：

```
$ git cat-file -p master^{tree}
100644 blob fa49b077972391ad58037050f2a75f74e3671e92      new.txt
100644 blob 033b4468fa6b2a9547a70d88d1bbe8bf3f9ed0d5      repo.rb
100644 blob e3f094f522629ae358806b17daf78246c27c007b      test.txt
```

接下来，可以使用`git cat-file`命令查看这个对象有多大：

```
$ git cat-file -s 033b4468fa6b2a9547a70d88d1bbe8bf3f9ed0d5
22044
```

现在，修改这个文件，然后看看会发生什么：

```
$ echo '# testing' >> repo.rb
$ git commit -am 'modified repo a bit'
[master 2431da6] modified repo.rb a bit
 1 file changed, 1 insertion(+)
```

查看这个提交生成的树对象：

```
$ git cat-file -p master^{tree}
100644 blob fa49b077972391ad58037050f2a75f74e3671e92      new.txt
100644 blob b042a60ef7dff760008df33cee372b945b6e884e      repo.rb
100644 blob e3f094f522629ae358806b17daf78246c27c007b      test.txt
```

`repo.rb`对应一个与之前完全不同的数据对象，这说明，虽然你只是在一个400行的文件后加一行新内容，Git也会用一个全新的对象来存储新的文件内容：

```
$ git cat-file -s b042a60ef7dff760008df33cee372b945b6e884e
22054
```

硬盘上现在有两个几乎完全相同，大小均为22KB的对象。如果Git只完整保存其中一个，再保存另一个对象与之前版本的差异内容，会更好。

实际上Git可以那样做。Git最初向硬盘中存储对象时所使用的格式被称为`松散(loose)`对象格式，但是，Git会时不时地将这些对象打包成一个称为`包文件（packfile）`的二进制文件，以节省空间和提高效率。

当版本库中有太多的松散对象，或者手动执行`git gc`命令，或者向远程服务器执行推送时，Git都会这样做。

要看打包过程，你可以手动执行`git gc`命令让Git对对象进行打包：

```
$ git gc
Counting objects: 18, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (14/14), done.
Writing objects: 100% (18/18), done.
Total 18 (delta 3), reused 0 (delta 0)
```

这个时候再查看`objects`目录，你会发现大部分的对象都不见了，与同时出现了一对新文件：

```
$ find .git/objects -type f
.git/objects/bd/9dbf5aae1a3862dd1526723246b20206e5fc37
.git/objects/d6/70460b4b4aece5915caf5c68d12f560a9fe3e4
.git/objects/info/packs
.git/objects/pack/pack-978e03944f5c581011e6998cd0e9e30000905586.idx
.git/objects/pack/pack-978e03944f5c581011e6998cd0e9e30000905586.pack
```

仍保留着的几个对象是未被任何提交记录引用的数据对象，这里为之前创建的`what is up，doc?`和`test content`这两个示例数据对象。

因为它们从没被添加至任何提交记录中，所以Git认为他们是`摇摆(dangling)`的，不会将他们打包进新生成的包文件中。

剩下的文件是新创建的包文件和一个索引。包文件包含刚从文件系统中移除的所有对象的内容。

索引文件包含了包文件的偏移信息，我们通过索引文件就可以快速定位任意一个指定对象。

有趣的是，运行`git gc`命令前硬盘上的文件大约22KB，打包后大约7KB。

Git打包时，会查找命名及大小相近的文件，并只保存文件的差异内容。

查看包文件，观察它是如何节省空间的。`git verify-pack`这个命令可以查看已打包的内容：

```
$ git verify-pack -v .git/objects/pack/pack-978e03944f5c581011e6998cd0e9e30000905586.idx
2431da676938450a4d72e260db3bf7b0f587bbc1 commit 223 155 12
69bcdaff5328278ab1c0812ce0e07fa7d26a96d7 commit 214 152 167
80d02664cb23ed55b226516648c7ad5d0a3deb90 commit 214 145 319
43168a18b7613d1281e5560855a83eb8fde3d687 commit 213 146 464
092917823486a802e94d727c820a9024e14a1fc2 commit 214 146 610
702470739ce72005e2edff522fde85d52a65df9b commit 165 118 756
d368d0ac0678cbe6cce505be58126d3526706e54 tag    130 122 874
fe879577cb8cffcdf25441725141e310dd7d239b tree   136 136 996
d8329fc1cc938780ffdd9f94e0d364e0ea74f579 tree   36 46 1132
deef2e1b793907545e50a2ea2ddb5ba6c58c4506 tree   136 136 1178
d982c7cb2c2a972ee391a85da481fc1f9127a01d tree   6 17 1314 1 \
  deef2e1b793907545e50a2ea2ddb5ba6c58c4506
3c4e9cd789d88d8d89c1073707c3585e41b0e614 tree   8 19 1331 1 \
  deef2e1b793907545e50a2ea2ddb5ba6c58c4506
0155eb4229851634a0f03eb265b69f5a2d56f341 tree   71 76 1350
83baae61804e65cc73a7201a7252750c76066a30 blob   10 19 1426
fa49b077972391ad58037050f2a75f74e3671e92 blob   9 18 1445
b042a60ef7dff760008df33cee372b945b6e884e blob   22054 5799 1463
033b4468fa6b2a9547a70d88d1bbe8bf3f9ed0d5 blob   9 20 7262 1 \
  b042a60ef7dff760008df33cee372b945b6e884e
1f7a7a472abf3dd9643fd615f6da379c4acb3e3a blob   10 19 7282
non delta: 15 objects
chain length = 1: 3 objects
.git/objects/pack/pack-978e03944f5c581011e6998cd0e9e30000905586.pack: ok
```

`033b4`这个数据对象引用了数据对象`b042a`，是`repo.rb`文件的第二个版本。

第三列显示的是各个对象在包文件中的大小，可以看到`b042a`占用了22KB的空间，而`033b4`仅占用9B。

第二个版本完整保存了文件内容，而原始版本反而是用差异方式保存的，这是因为大部分情况下需要快速访问文件的最新版本。

你可以随时打包，Git时常会自动对仓库进行重新打包以节省空间。当然也可以随时手动`git gc`命令来执行。

--------------------------------------------------------------------------------

## 10.5 引用规格

远程分支到本地引用的映射方式可以更复杂。假设你添加了一个远程版本库：

```
$ git remote add origin https://github.com/schacon/simplegit-progit
```

上述命令会在`.git/config`文件中添加一个小节，其中指定远程 版本库的名字(`origin`默认名字)、URL和一个用于获取操作的引用规格（refspec）：

```
[remote "origin"]
    url = https://github.com/schacon/simplegit-progit
    fetch = +refs/heads/*:refs/remotes/origin/*
```

引用规格的由一个可选的`+`号和紧随其后的`<src>:<dst>`组成。

`<src>`是一个模式，代表远程版本库中的引用。

`<dst>`是那些远程引用在本地所对应的位置。

`+`号告诉Git即使在不能快进的情况下也要(强制)更新引用。

默认情况下，引用规格由`git remote add`命令自动生成，Git获取服务器中`refs/heads/`下面的所有引用，并将它写入到本地的`refs/remotes/origin/`中，所有，如果服务器上有一个`master`分支，我们可以在本地通过下面的方式来访问该分支上的提交记录：

```
$ git log origin/master
$ git log remotes/origin/master
$ git log refs/remotes/origin/master
```

上面的三个命令作用相同分，因为Git会把它们都扩展成`refs/remotes/origin/master`。

如果想让Git每次只拉取远程的`master`分支，而不是所有分支，可以把（引用规格的）获取那一行修改为：

```
fetch = +refs/heads/master:refs/remotes/origin/master
```

这只是对远程版本库的`git fetch`操作的默认引进规格。如果有某些只希望被执行一次的操作，也可以在命令行指定引用规格。若要将远程的`master`分支拉到本地的`origin/mymaster`分支，可以运行：

```
$ git fetch origin master:refs/remotes/origin/mymaster
```

也可以指定多个引用规格，在命令行中，你可以按照下方方式拉取多个分支：

```
$ git fetch origin master:refs/remotes/origin/mymaster \
     topic:refs/remotes/origin/topic
From git@github.com:schacon/simplegit
 ! [rejected]        master     -> origin/mymaster  (non fast forward)
 * [new branch]      topic      -> origin/topic
```

在个例子中，对`master`分支的拉取操作被拒绝，因为它不是一个可以快进的引用。我们可以通过在引用规格前指定`+`号来覆盖该规则。

你也可以在配置文件中指定多个用于获取的引用规格。如果想在每次获取时都包含`master`和`experiment`分支，添加如下两行：

```
[remote "origin"]
    url = https://github.com/schacon/simplegit-progit
    fetch = +refs/heads/master:refs/remotes/origin/master
    fetch = +refs/heads/experiment:refs/remotes/origin/experiment
```

不能再模式中使用部分符号，下方为不合法的引用规格：

```
fetch = +refs/heads/qa*:refs/remotes/origin/qa*
```

但是可以使用命名空间或目录来达到类似目的。假设你有一个QA团队，他们推送了一系列分支，同时你只想要获取`master`和QA团队的所有分支，而不关心其他任何分支，那么可以使用如下配置：

```
[remote "origin"]
    url = https://github.com/schacon/simplegit-progit
    fetch = +refs/heads/master:refs/remotes/origin/master
    fetch = +refs/heads/qa/*:refs/remotes/origin/qa/*
```

如果项目的工作流很复杂，有QA团队推送分支、开发人员推送分支、集成团队推送并且在远程分支上展开协作，你就可以像这样为这些分支创建各自的命名空间。

### 10.5.1 引用规格推送

上面的方式很好，但是需要QA团队将他们的分支放入远程仓库的`qa/`命名空间。

QA团队先要使用下方命令将他们的`master`分支推送到远程服务器的`qa/master`分支上，

```
$ git push origin master:refs/heads/qa/master
```

如果想要Git每次推送时都这样做，就要在配置文件中添加一条`push`值

```
[remote "origin"]
    url = https://github.com/schacon/simplegit-progit
    fetch = +refs/heads/*:refs/remotes/origin/*
    push = refs/heads/master:refs/heads/qa/master
```

这样就可以让`git push origin`默认把本地`master`分支推送到远程`qa/master`分支。

### 10.5.2 删除引用

可以使用下方命令，将引用规格从远程服务器上删除引用：

```
$ git push origin :topic
```

因为引用规格是`<src>:<dst>`,所以上述命令把`<src>`留空，意味着把远程版本库的`topic`分支定义为空值，也就是删除它。

--------------------------------------------------------------------------------

## 10.6 传输协议

Git可以通过两种主要的方式在版本库之间传输数据。一种是`哑(dumb)协议`，一种是`智能(smart)`协议。

### 10.6.1 哑协议

如果正在架设一个基于HTTP协议的只读版本版，一般这种情况下使用的就是哑协议。

被称为`哑协议`，是因为在传输过程中，服务端不需要有针对Git特有的代码，抓取过程是一系列HTTP的`GET`请求，这种情况下，客户端可以推断出服务端Git仓库的布局。

_注意：现在很少使用哑协议了，使用哑协议的版本库很难保证安全性和私有化，所以大多数Git服务器宿主都会拒绝使用它。一般情况下都建议使用智能协议。_

通过`simplegit`版本库开看`http-fetch`的过程：

```
$ git clone http://server/simplegit-progit.git
```

它做的第一件事就是拉取`info/refs`文件。这个文件是通过`update-server-info`命令生成的，这也解释了在使用HTTP传输时，必须把它设置为`post-receive`钩子的原因：

```
=> GET info/refs
ca82a6dff817ec66f44342007202690a93763949     refs/heads/master
```

现在，得到一个远程引用和SHA-1值得列表。接下来，要确定HEAD引用是什么，这样就知道在完成后应该被检出的工作目录的内容：

```
=> GET HEAD
ref: refs/heads/master
```

在完成抓取后，需要检出`master`分支。这时，就可以可以遍历处理了。

因为你从`info/refs`文件中所提提取的`ca82a6`提交对象开始，所以首先要获取它：

```
=> GET objects/ca/82a6dff817ec66f44342007202690a93763949
(179 bytes of binary data)
```

取回了一个对象，这是一个在服务端以松散格式保存的对象，是你通过使用静态HTTP GET请求获取的。

你可以使用zlib解压缩它，去除头部，查看提交记录的内容：

```
$ git cat-file -p ca82a6dff817ec66f44342007202690a93763949
tree cfda3bf379e4f8dba8717dee55aab78aef7f4daf
parent 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
author Scott Chacon <schacon@gmail.com> 1205815931 -0700
committer Scott Chacon <schacon@gmail.com> 1240030591 -0700

changed the version number
```

接下来，还要在获取两个对象，一个是树对象`cfda3b`，它包含有刚刚获取的提交对象所指向的内容，另一个是它的父提交`085bb3`

```
=> GET objects/08/5bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
(179 bytes of data)
```

这样就取得了你的下一个提交对象。再抓取树对象：

```
=> GET objects/cf/da3bf379e4f8dba8717dee55aab78aef7f4daf
(404 - Not Found)
```

这个树对象在服务端并不以松散格式对象存在，所有得到一个404响应，表示在HTTP服务端没有找到该对象。

有几个可以得原因。 第一，这个对象可能在替代版本库里面。第二在包文件里面。

Git会首先检查所有列出的替代版本库：

```
=> GET objects/info/http-alternates
(empty file)
```

如果返回了一个包含替代版本库URL的列表，那么Git就会去那些地址检查格式对象和文件。这是一种节省磁盘的好方法。

但是，这个例子中，没有列出可用的替代版本库。所以你所需要的对象肯定在某个包文件中。

要检查服务端有哪些可用的包文件，你需要获取`objects/info/packs`文件，这里面有一个包文件列表（它也是通过执行`update-server-info`所生成的）：

```
=> GET objects/info/packs
P pack-816a9b2334da9953e530f27bcac22082a9f5b835.pack
```

服务器上只有一个包文件，需要的对象就在里面。但是要先检查它的索引文件以确认。

即使服务端有多个包文件，这也很有用，这样你就知道所需要的对象是在哪一个包文件里面：

```
=> GET objects/pack/pack-816a9b2334da9953e530f27bcac22082a9f5b835.idx
(4k of binary data)
```

现在你有这个包文件的索引，你可以查看你要的对象是否在里面。

因为索引文件列出了这个包文件所包含的所有对象的SHA-1值，和该对象存在于包文件中的偏移量。

你的对象就在f这里，接下来就是获取整个包文件：

```
=> GET objects/pack/pack-816a9b2334da9953e530f27bcac22082a9f5b835.pack
(13k of binary data)
```

现在你也有了树对象，你可以继续在浏览提交记录。它们全部都在刚下载的包文件里面，所以不需求更多的下载了。

Git会将开始时下载的HEAD引用指向`master`分支的工作目录。

### 10.6.2 智能协议

哑协议虽然简单但效率略低，而且不能从客户端向服务端发送数据。

智能协议是更常用的传送数据的方法，但它需要在服务端运行一个进程，它可以读取本地数据，理解客户端有什么和需要什么，并为它生成合适的包文件。

总共有两组进程用于传输数据，它们分别负责上传和下载数据。

- 上传数据

  为了上传数据到服务端，Git使用`send-pack`和`receive-pack`进程。运行在客户端上的`send-pack`进程连接到服务端`receive-pack`进程。

  - SSH

    举例来说，在项目中使用命令`git push origin master`时，`origin`是由基于SSH协议的URL所定义的。

    Git会运行`send-pack`进程，它会通过SSH连接你的服务器。它会尝试通过SSH在服务端执行命令，如下：

    ```
      $ ssh -x git@server "git-receive-pack 'simplegit-progit.git'"
      00a5ca82a6dff817ec66f4437202690a93763949 refs/heads/master report-status \
          delete-refs side-band-64k quiet ofs-delta \
          agent=git/2:2.1.1+github-607-gfba4028 delete-refs
      0000
    ```

    `git-receive-pack`命令会立即为它的每一个引用发送一行响应，在这个例子，就只有`master`分支和它的SHA-1值。第一行响应中也包含了一个服务器能力的列表（这里是`report-status`、`delete-refs`和一些其它的，包括客户端的识别码。）

    `0000`表示服务端己完成了发送引用列表。

    现在Git知道了服务器的状态，`send-pack`进程会判断哪些提交记录是本地拥有的，而服务端没有的。

    `send-pack`会告知`receive-pack`这次推送将会更新各个引用。

    **例子：**

    如果你正在更新`master`分支，并且增加`experiment`分支，这个`send-pack`的响应将会是这样：

    ```
      0076ca82a6dff817ec66f44342007202690a93763949 15027957951b64cf874c3557a0f3547bd83b3ff6 \
          refs/heads/master report-status
      006c0000000000000000000000000000000000000000 cdfdb42577e2506715f8cfeacdbabc092bf63e8d \
          refs/heads/experiment
      0000
    ```

    Git会为每一个将要更新的引用发送一行数据，包含该行长度，旧SHA-1值，新SHA-1值和将要更新的引用。

    这里全为`0`的SHA-1值表示之前没有过这个引用。因为正要添加新的`experiment`应用，删除引用时，将会看到相反的情况，右边的SHA-1值全为`0`。

    接下来，客户端会发送一个包文件，它包含了所以服务端还没有的对象。最后，服务端会以成功或失败响应：

    ```
      000eunpack ok
    ```

```
* HTTP(S)

    HTTPS与HTTP相比，除了在`握手`过程略有不同外，其他基本相似。连接是从下面这个请求开始的：

        => GET http://server/simplegit-progit.git/info/refs?service=git-receive-pack
        001f# service=git-receive-pack
        00ab6c5f0e45abd7832bf23074a333f739977c9e8188 refs/heads/master report-status \
            delete-refs side-band-64k quiet ofs-delta \
            agent=git/2:2.1.1~vmg-bitmaps-bugaloo-608-g116744e
        0000

    这完成了客户端和服务器的第一次数据交换，接下来客户端发起另一个请求，这次是一个`POST`请求，这个请求中包含了`git-upload-pack`提供的数据。

        => POST http://server/simplegit-progit.git/git-receive-pack

    这个`POST`请求的内容是`send-pack`的输出和相应的包文件。服务端在收到请求后相应地作出成功或者失败的HTTP响应。
```

- 下载数据

  当下载数据时，`fetch-pack`和`upload-pack`进程就起作用了。客户端启动`fetch-pack`进程，连接到远程的`upload-pack`进程，以协商后续传输的数据。

  - SSH

    如果通过SSH使用抓取功能，`fetch-pack`会像下方一样运行：

    ```
      $ ssh -x git@server "git-upload-pack 'simplegit-progit.git'"
    ```

    在`fetch-pack`连接后，`upload-pack`会返回类似下面的内容：

    ```
      00dfca82a6dff817ec66f44342007202690a93763949 HEAD multi_ack thin-pack \
          side-band side-band-64k ofs-delta shallow no-progress include-tag \
          multi_ack_detailed symref=HEAD:refs/heads/master \
          agent=git/2:2.1.1+github-607-gfba4028
      003fe2409a098dc3e53539a9028a94b6224db9d6a6b6 refs/heads/master
      0000
    ```

    这与`receive-pack`的响应类似，但是这里所包含的能力是不同的。而且它还包含HEAD引用所指向内容（`symref=HEAD:refs/heads/master`）,这样如果客户端执行的是克隆，Git就会知道要检出什么。

    这时，`fetch-pack`进程查看它自己所拥有的对象，并响应`want`和它需要的对象的SHA-1值。它还会发送`have`和所有它已拥有的对象的SHA-1值。在列表的最后，它还会发送`done`以通知`upload-pack`进程可以开始发送它所需对象的包文件：

    ```
      003cwant ca82a6dff817ec66f44342007202690a93763949 ofs-delta
      0032have 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
      0009done
      0000
    ```

```
* HTTP(S)

    抓取操作的握手需要两个HTTP请求。第一个是向和哑协议中相同的端点发送`GET`请求：

        => GET $GIT_URL/info/refs?service=git-upload-pack
        001e# service=git-upload-pack
        00e7ca82a6dff817ec66f44342007202690a93763949 HEAD multi_ack thin-pack \
            side-band side-band-64k ofs-delta shallow no-progress include-tag \
            multi_ack_detailed no-done symref=HEAD:refs/heads/master \
            agent=git/2:2.1.1+github-607-gfba4028
        003fca82a6dff817ec66f44342007202690a93763949 refs/heads/master
        0000

    这和通过SSH使用`git-upload-pack`是非常相似的，但是第二个数据交换则是一个单独的请求：

        => POST $GIT_URL/git-upload-pack HTTP/1.0
        0032want 0a53e9ddeaddad63ad106860237bbf53411d11a7
        0032have 441b40d833fdfa93eb2908e52742248faf0ee993
        0000

    这个输出格式还是和前面一样的。这个请求的响应包含了所需要的包文件，并指明成功或失败。
```

--------------------------------------------------------------------------------

## 10.7 维护与数据恢复

有时，需要对仓库进行清理。使它的结构变得更紧凑，或者对导入的仓库进行清理，或者是恢复丢失的内容。

### 10.7.1 维护

Git会不定时地自动运行`auto gc`命令。大多数时候，这个命令并不会产生效果。然而，如果有太多的松散对象或者太多包文件，Git会运行一个完整的`git gc`命令。

`gc`代表垃圾回收，这个命令会做下面的事情：收集所有松散对象并将它们放置到包文件中，将多个包文件合并为一个大的包文件，移除与任何提交都不相关的陈旧对象。

也可以手动执行自动垃圾回收：

```
$ git gc --auto
```

这个命令通过并不会产生效果。大约需要7000个以上的松散对象或超过50个的包文件才能让Git启动一次真正的`gc`命令。

可以通过修改`gc.auto`与`gc.autopacklimit`的设置来改动这些数值。

`gc`将会做的另一件事是打包你的引用到一个单独的文件。假设你的仓库包含以下分支与标签：

```
    $ find .git/refs -type f
    .git/refs/heads/experiment
    .git/refs/heads/master
    .git/refs/tags/v1.0
    .git/refs/tags/v1.1
```

如果执行了`git gc`命令，`refs`目录中将不会再有这些文件。为了保证效率Git会将它们移动到名为`.git/packed-refs`的文件中，就像下方这样：

```
$ cat .git/packed-refs
# pack-refs with: peeled fully-peeled
cac0cab538b970a37ea1e769cbbde608743bc96d refs/heads/experiment
ab1afef80fac8e34258ff41fc1b867c702daa24b refs/heads/master
cac0cab538b970a37ea1e769cbbde608743bc96d refs/tags/v1.0
9585191f37f7b0fb9444f35a9bf50de191beadc2 refs/tags/v1.1
^1a410efbd13591db07496601ebc7a059dd55cfe9
```

如果更新了引用，Git并不会修改这个文件，而是向`refs/heads`创建一个新的文件。为了获得指定引用的正确SHA-1值，Git会首先在`refs`目录中查找引用，然后再到`packed-refs`文件中查找。所以，如果你在`refs`目录中找不到一个引用，那么它或许在`packed-refs`文件中。

注意这个文件的最后一行，它会以`^`开头，这个符号表示它上一行的标签是附注标签，那一行是附注标签指向的那个提交。

### 10.7.2 数据恢复

在使用Git的时候，你可能会意外丢失一次提交。通常是因为你强制删除了正在工作的分支，但是最后却发现你还需要这个分支。也可能是硬重置了一次分支，放弃了你想要的提交。如果上述情况已经发生，该如何找回你的提交？

下方例子将硬重置测试仓库中的master分支到一个旧提交，以恢复丢失的提交。

首先，让看看仓库现在在什么地方：

```
$ git log --pretty=oneline
ab1afef80fac8e34258ff41fc1b867c702daa24b modified repo a bit
484a59275031909e19aadb7c92262719cfcdf19a added repo.rb
1a410efbd13591db07496601ebc7a059dd55cfe9 third commit
cac0cab538b970a37ea1e769cbbde608743bc96d second commit
fdf4fc3344e67ab068f836878b6c4951e3b15f3d first commit
```

现在，我们将`master`分支硬重置到第三次提交：

```
$ git reset --hard 1a410efbd13591db07496601ebc7a059dd55cfe9
HEAD is now at 1a410ef third commit
$ git log --pretty=oneline
1a410efbd13591db07496601ebc7a059dd55cfe9 third commit
cac0cab538b970a37ea1e769cbbde608743bc96d second commit
fdf4fc3344e67ab068f836878b6c4951e3b15f3d first commit
```

现在顶部的两个提交已经失去了，没有分支指向这些提交。你需要找出最后一次提交的SHA-1然后增加一个指向它的分支。

方法是找到最后一次提交的SHA-1值。可以使用`git reflog`命令。

当你正在工作时，Git会默默记录每一次你改变HEAD时的值。每一次提交或改变分支，引用日志都会被更新。引用日志也可以通过`git update-ref`命令更新。

可以随时通过`git reflog`命令来了解你曾经做过什么：

```
$ git reflog
1a410ef HEAD@{0}: reset: moving to 1a410ef
ab1afef HEAD@{1}: commit: modified repo.rb a bit
484a592 HEAD@{2}: commit: added repo.rb
```

这里可以看到已经检出的两次提交，然而并没有足够多的信息。为了信息更多的信息，可以使用`git log -g`，这个命令会以标准日志的格式输出引用日志。

```
$ git log -g
commit 1a410efbd13591db07496601ebc7a059dd55cfe9
Reflog: HEAD@{0} (Scott Chacon <schacon@gmail.com>)
Reflog message: updating HEAD
Author: Scott Chacon <schacon@gmail.com>
Date:   Fri May 22 18:22:37 2009 -0700

        third commit

commit ab1afef80fac8e34258ff41fc1b867c702daa24b
Reflog: HEAD@{1} (Scott Chacon <schacon@gmail.com>)
Reflog message: updating HEAD
Author: Scott Chacon <schacon@gmail.com>
Date:   Fri May 22 18:15:24 2009 -0700

       modified repo.rb a bit
```

看起来下面的就是丢失的提交，可以通过创建一个新的分支指向这个提交来恢复它。

例如，你可以创建一个名为`recover-branch`的分支指向这个提交：

```
$ git branch recover-branch ab1afef
$ git log --pretty=oneline recover-branch
ab1afef80fac8e34258ff41fc1b867c702daa24b modified repo a bit
484a59275031909e19aadb7c92262719cfcdf19a added repo.rb
1a410efbd13591db07496601ebc7a059dd55cfe9 third commit
cac0cab538b970a37ea1e769cbbde608743bc96d second commit
fdf4fc3344e67ab068f836878b6c4951e3b15f3d first commit
```

现在`recover-branch`分支是`master`分支曾经指向的地方。提交记录成功找回。

接下来，假设你丢失的提交因为某些原因不在引用日志中，通过移除`recover-branch`分支并删除引用日志来模拟这种情况。

现在前两次提交又不被任何分支指向了：

```
$ git branch -D recover-branch
$ rm -Rf .git/logs/
```

由于引用日志数据存放在`.git/logs/`目录中，现在你已经没有引用日志了。这时该如何恢复那次提交？

使用`git fsck`实用工具，将会检查数据库的完整性。如果使用`--full`参数，它会显示出所有没有被其他对象指向的对象：

$ git fsck --full Checking object directories: 100% (256/256), done. Checking objects: 100% (18/18), done. dangling blob d670460b4b4aece5915caf5c68d12f560a9fe3e4 dangling commit ab1afef80fac8e34258ff41fc1b867c702daa24b dangling tree aea790b9a58f6cf6f2804eeac9f0abbe9631e4c9 dangling blob 7108f7ecb345ee9d0084193f147cdad4d2998293

现在添加一个分支并指向提交，这样数据就找回来了。

### 10.7.3 移除对象

刚有人先仓库添加一个很大的文件，而后虽然删除了，但是Git的提交记录还在。别人克隆是还是要强制下载这个大文件。

那么现在需要找到并移除这些大文件。

_注意：这个操作对提交历史的修改是破环性的。_

它会从必须修改或移除一个大文件引用最早的树对象开始重写每一次提交。

如果在导入仓库后，在别人开始基于这些提交工作前执行这个操作，那么将不会有问题。否则，你必须通知所有的贡献者他们需要将他们的成果变基到你的新提交上。

为了演示，我们将添加一个大文件到测试仓库中，并在下一次提交中删除它，之后我们需要找到它，并将它从仓库中永久删除。

首先，添加一个大文件到仓库中：

```
$ curl https://www.kernel.org/pub/software/scm/git/git-2.1.0.tar.gz > git.tgz
$ git add git.tgz
$ git commit -m 'add git tarball'
[master 7b30847] add git tarball
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 git.tgz
```

现在移除它：

$ git rm git.tgz rm 'git.tgz' $ git commit -m 'oops - removed large tarball' [master dadf725] oops - removed large tarball 1 file changed, 0 insertions(+), 0 deletions(-) delete mode 100644 git.tgz

之后使用`git gc`清理一下。

```
$ git gc
Counting objects: 17, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (13/13), done.
Writing objects: 100% (17/17), done.
Total 17 (delta 1), reused 10 (delta 0)
```

现在执行`$ git count-objects -v`来查看下占用空间的大小：

```
$ git count-objects -v
count: 7
size: 32
in-pack: 17
packs: 1
size-pack: 4868
prune-packable: 0
garbage: 0
size-garbage: 0
```

`size-pack`是包文件的大小，计量单位是KB。现在彻底的移除这个文件。

首先必须找到它，没错假装不知道。

查看包文件中内容可以使用`$ git verify-pack -v`然后对输出内容的第三列（文件大小）进行排序，找出大文件。

```
$ git verify-pack -v .git/objects/pack/pack-29…69.idx \
  | sort -k 3 -n \
  | tail -3
dadf7258d699da2c8d89b09ef6670edb7d5f91b4 commit 229 159 12
033b4468fa6b2a9547a70d88d1bbe8bf3f9ed0d5 blob   22044 5792 4977696
82c99a3e86bb1267b236a4b6eff7868d97489af1 blob   4975916 4976258 1438
```

这时可以看到这个大对象出现在结果的最底部。

为了找出具体是哪个文件，可以使用`rev-list`命令，加上`--objects`参数，它会列出所有提交的SHA-1，数据对象的SHA-1和它们相关联的文件路径。

```
$ git rev-list --objects --all | grep 82c99a3
82c99a3e86bb1267b236a4b6eff7868d97489af1 git.tgz
```

现在，只需要从过去所有的树中移除这个文件。使用下方命令可以查看哪些提交对这个文件产生改动：

```
$ git log --oneline --branches -- git.tgz
dadf725 oops - removed large tarball
7b30847 add git tarball
```

现在，必须重写`7b30847`提交之后的所有提交来从Git历史中完成移除这个文件。为了执行这个操作，要使用`filter-branch`命令。

```
$ git filter-branch --index-filter \
  'git rm --ignore-unmatch --cached git.tgz' -- 7b30847^..
Rewrite 7b30847d080183a1ab7d18fb202473b3096e9f34 (1/2)rm 'git.tgz'
Rewrite dadf7258d699da2c8d89b09ef6670edb7d5f91b4 (2/2)
Ref 'refs/heads/master' was rewritten
```

`--index-filter`选项类似于`--tree-filter`选择，不过这个选项并不会让命令修改在硬盘上检出的文件，而只是修改在暂存区或索引中的文件。

同时必须使用`git rm --cached`命令来移除文件，而不是`rm file`命令。因为需要从索引中移除，而不是磁盘中。

`--ignore-unmatch`选择告诉命令，如果尝试删除的模式不存在时，不提示错误。

最后使用`filter-branch`选项来重写自`7b30847`提交以来的历史，也就是这个问题产生的地方。

历史中将不再包含对那个文件的引用。不过，引用日志和`.git/refs/original`通过`filter-branch`选择添加的新引用中还存有对这个文件的引用，所以必须移除它们然后重新打包数据库。

在重新打包前需要移除任何包含指向那些旧提交的指针文件：

```
$ rm -Rf .git/refs/original
$ rm -Rf .git/logs/
$ git gc
Counting objects: 15, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (11/11), done.
Writing objects: 100% (15/15), done.
Total 15 (delta 1), reused 12 (delta 0)
```

现在看看省了多少空间。

```
$ git count-objects -v
count: 11
size: 4904
in-pack: 15
packs: 1
size-pack: 8
prune-packable: 0
garbage: 0
size-garbage: 0
```

包的大小降到了8KB，可以从size的值看出，这个大文件还在松散对象中，并没有消失。但是它不会在推送或克隆中出现，这才是最重要的。

如果真的想要删除它，可以通过`git prune --expire`选项来完全地移除那个对象：

```
$ git prune --expire now
$ git count-objects -v
count: 0
size: 0
in-pack: 15
packs: 1
size-pack: 8
prune-packable: 0
garbage: 0
size-garbage: 0
```

--------------------------------------------------------------------------------

## 10.8 环境变量

Git总是在一个`bash shell`中运行，并借助一些shell环境变量来决定他的运行方式。

知道它们是什么以及它们如何让Git按照你希望的方式去运行会很有用。

这里只说明最常用的部分。

### 10.8.1 全局行为

**`GIT_EXEC_PATH`**

决定Git到哪里找它的子程序（`git commit`、`git diff`等等）。可以使用`git --exec-path`来查看当前设置。

**`HOME`**

通常不会修改，这是Git查找全局配置文件的地方。

如果要制作携带版Git，在Git的shell配置中覆盖`HOME`设置。

**`PREFIX`**

类似`HOME`,如果设置**`GIT_CONFIG_NOSYSTEM`**，就禁用系统级别的配置文件。在系统配置响应了命令，而你又无权限修改的时候很有用。

**`GIT_PAGER`**

控制在命令行上显示多页输出的程序。如果没有设置就用`PAGER`。

**`GIT_EDITOR`**

当用户需要编辑一些文本时，Git会启动编辑器。如果没有设置就用`EDITOR`。

### 10.8.2 版本库位置

**`GIT_DIR`**

`.git`目录的位置，如果没事设置。Git会逐层向上查找`.git`目录，直到`~`或`/`。

**`GIT_CEILING_DIRECTORIES`**

控制查找`.git`目录的行为。如果你访问加载很慢的目录，会想让Git早点停止尝试。

**`GIT_WORK_TREE`**

是非空版本库的工作目录的根路径。如果没有指定，就用`$GIT_DIR`的父目录。

**`GIT_INDEX_FILE`**

是索引文件的路径。（非空版本库有）

**`GIT_OBJECT_DIRECTORY`**

用来指定`.git/objects`目录的位置。

**`GIT_ALTERNATE_OBJECT_DIRECTORIES`**

一个冒号分割列表，告诉Git到哪里去找不在`GIT_OBJECT_DIRECTORY`目录中的对象，如果有很多项目有相同内容的大文件，可以用来避免存储过多的备份。

### 10.8.3 路径规则

`pathspec`指在Git中如何指定路径，包括通用符号的使用。

**`GIT_GLOB_PATHSPECS`**&**`GIT_NOGLOB_PATHSPECS`**

控制通用符号在路径中的默认行为。

如果 GIT_GLOB_PATHSPECS 设置为 1, 通配符表现为通配符（这是默认设置）; 如果 GIT_NOGLOB_PATHSPECS 设置为 1,通配符仅匹配字面。意思是_ .c 只会匹配 文件名是 "_.c" 的文件， 而不是以 .c 结尾的文件。 你可以在各个路径规格中用 :(glob) 或 :(literal) 开头来覆盖这个配置，如 :(glob)*.c 。

**`GIT_LITERAL_PATHSPECS`**

禁用上面的两种行为；通配符将不能用，前缀覆盖也不能用。

**`GIT_ICASE_PATHSPECS`**

让所有的路径规格忽略大小写。

### 10.8.4 提交

`git commit`用这些环境变量作主要的信息源。

**`GIT_AUTHOR_NAME`**

是 "author" 字段的可读的名字。

**`GIT_AUTHOR_EMAIL`**

是 "author" 字段的邮件。

**`GIT_AUTHOR_DATE`**

是 "author" 字段的时间戳。

**`GIT_COMMITTER_NAME`**

是 "committer" 字段的可读的名字。

**`GIT_COMMITTER_EMALL`**

是 "committer" 字段的邮件。

**`GIT_COMMITTER_DATE`**

是 "committer" 字段的时间戳。

如果`user.email`没有配置，就会用`EMAIL`指定邮件地址。如果也没有设置，Git继续回退使用系统用户和主机名。

### 10.8.5 网络

**`GIT_CURL_VERBOSE`**

告诉 Git 显示所有由那个库产生的消息。

**`GIT_SSL_NO_VERIFY`**

告诉 Git 不用验证 SSL 证书。 这在有些时候是需要的， 例如你用一个自己签名的证书通过 HTTPS 来提供 Git 服务， 或者你正在搭建 Git 服务器，还没有安装完全的证书。

**`GIT_HTTP_LOW_SPEED_LIMIT`**

低于设置网速

**`GIT_HTTP_LOW_SPEED_TIME`**

接上，持续多少秒后，中断连接。

这些值会覆盖 http.lowSpeedLimit 和 http.lowSpeedTime 配置的值。

**`GIT_HTTP_USER_AGENT`**

设置 Git 在通过 HTTP 通讯时用到的 user-agent。 默认值类似于 git/2.0.0 。

### 10.8.6 比较和合并

**`GIT_DIFF_OPTS`**

有效值仅支持 -u

<n> 或 --unified=<n>，用来控制在 git diff 命令中显示的内容行数。</n></n>

**`GIT_EXTERNAL_DIFF`**

用来覆盖 diff.external 配置的值。 如果设置了这个值， 当执行Git git diff 时，Git 会调用该程序。

**`GIT_DIFF_PATH_COUNTER`**&**`GIT_DIFF_PATH_TOTAL`**

对于 GIT_EXTERNAL_DIFF 或 diff.external 指定的程序有用。 前者表示在一系列文件中哪个是被比较的（从 1 开始），后者表示每批文件的总数。

**`GIT_MERGE_VERBOSITY`**

控制递归合并策略的输出。 允许的值有下面这些：

- 0 什么都不输出，除了可能会有一个错误信息。

- 1 只显示冲突。

- 2 还显示文件改变。

- 3 显示因为没有改变被跳过的文件。

- 4 显示处理的所有路径。

- 5 显示详细的调试信息。

默认值是 2.

### 10.8.7 调试

打开Git内置的跟踪信息。

变量可用值如下：

- "true", "1", 或 "2" – 跟踪类别写到标准错误输出.

- 以 / 开头的绝对路径 – 跟踪输出会被写到那个文件。

**`GIT_TRACE`**

控制常规跟踪，它并不适用于特殊情况。 它跟踪的范围包括别名的展开和其他子程序的委托。

```
$ GIT_TRACE=true git lga
20:12:49.877982 git.c:554               trace: exec: 'git-lga'
20:12:49.878369 run-command.c:341       trace: run_command: 'git-lga'
20:12:49.879529 git.c:282               trace: alias expansion: lga => 'log' '--graph' '--pretty=oneline' '--abbrev-commit' '--decorate' '--all'
20:12:49.879885 git.c:349               trace: built-in: git 'log' '--graph' '--pretty=oneline' '--abbrev-commit' '--decorate' '--all'
20:12:49.899217 run-command.c:341       trace: run_command: 'less'
20:12:49.899675 run-command.c:192       trace: exec: 'less'
```

**`GIT_TRACE_PACK_ACCESS`**

控制访问打包文件的跟踪信息 第一个字段是被访问的打包文件，第二个是文件的偏移量：

```
$ GIT_TRACE_PACK_ACCESS=true git status
20:10:12.081397 sha1_file.c:2088        .git/objects/pack/pack-c3fa...291e.pack 12
20:10:12.081886 sha1_file.c:2088        .git/objects/pack/pack-c3fa...291e.pack 34662
20:10:12.082115 sha1_file.c:2088        .git/objects/pack/pack-c3fa...291e.pack 35175
# […]
20:10:12.087398 sha1_file.c:2088        .git/objects/pack/pack-e80e...e3d2.pack 56914983
20:10:12.087419 sha1_file.c:2088        .git/objects/pack/pack-e80e...e3d2.pack 14303666
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
```

**`GIT_TRACE_PACKET`**

打开网络操作包级别的跟踪信息

```
$ GIT_TRACE_PACKET=true git ls-remote origin
20:15:14.867043 pkt-line.c:46           packet:          git< # service=git-upload-pack
20:15:14.867071 pkt-line.c:46           packet:          git< 0000
20:15:14.867079 pkt-line.c:46           packet:          git< 97b8860c071898d9e162678ea1035a8ced2f8b1f HEAD\0multi_ack thin-pack side-band side-band-64k ofs-delta shallow no-progress include-tag multi_ack_detailed no-done symref=HEAD:refs/heads/master agent=git/2.0.4
20:15:14.867088 pkt-line.c:46           packet:          git< 0f20ae29889d61f2e93ae00fd34f1cdb53285702 refs/heads/ab/add-interactive-show-diff-func-name
20:15:14.867094 pkt-line.c:46           packet:          git< 36dc827bc9d17f80ed4f326de21247a5d1341fbc refs/heads/ah/doc-gitk-config
# […]
```

**`GIT_TRACE_PERFORMANCE`**

控制性能数据的日志打印。 输出显示了每个 Git 命令调用花费的时间。

```
$ GIT_TRACE_PERFORMANCE=true git gc
20:18:19.499676 trace.c:414             performance: 0.374835000 s: git command: 'git' 'pack-refs' '--all' '--prune'
20:18:19.845585 trace.c:414             performance: 0.343020000 s: git command: 'git' 'reflog' 'expire' '--all'
Counting objects: 170994, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (43413/43413), done.
Writing objects: 100% (170994/170994), done.
Total 170994 (delta 126176), reused 170524 (delta 125706)
20:18:23.567927 trace.c:414             performance: 3.715349000 s: git command: 'git' 'pack-objects' '--keep-true-parents' '--honor-pack-keep' '--non-empty' '--all' '--reflog' '--unpack-unreachable=2.weeks.ago' '--local' '--delta-base-offset' '.git/objects/pack/.tmp-49190-pack'
20:18:23.584728 trace.c:414             performance: 0.000910000 s: git command: 'git' 'prune-packed'
20:18:23.605218 trace.c:414             performance: 0.017972000 s: git command: 'git' 'update-server-info'
20:18:23.606342 trace.c:414             performance: 3.756312000 s: git command: 'git' 'repack' '-d' '-l' '-A' '--unpack-unreachable=2.weeks.ago'
Checking connectivity: 170994, done.
20:18:25.225424 trace.c:414             performance: 1.616423000 s: git command: 'git' 'prune' '--expire' '2.weeks.ago'
20:18:25.232403 trace.c:414             performance: 0.001051000 s: git command: 'git' 'rerere' 'gc'
20:18:25.233159 trace.c:414             performance: 6.112217000 s: git command: 'git' 'gc'
```

**`GIT_TRACE_SETUP`**

显示 Git 发现的关于版本库和交互环境的信息

```
$ GIT_TRACE_SETUP=true git status
20:19:47.086765 trace.c:315             setup: git_dir: .git
20:19:47.087184 trace.c:316             setup: worktree: /Users/ben/src/git
20:19:47.087191 trace.c:317             setup: cwd: /Users/ben/src/git
20:19:47.087194 trace.c:318             setup: prefix: (null)
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
```

### 10.8.8 其它

**`GIT_SSH`**

Git 连接 SSH 主机时会用指定的程序代替 `ssh` 。 它会被用 `$GIT_SSH [username@]host [-p <port>] <command>` 的命令方式调用。 这不是配置定制 `ssh` 调用方式的最简单的方法; 它不支持额外的命令行参数， 所以你必须写一个封装脚本然后让 `GIT_SSH` 指向它。 可能用 `~/.ssh/config` 会更简单。

**`GIT_ASKPASS`**

覆盖了 `core.askpass` 配置。 这是 Git 需要向用户请求验证时用到的程序，它接受一个文本提示作为命令行参数，并在 `stdout` 中返回应答。

**`GIT_NAMESPACE`**

控制有命令空间的引用的访问，与 `--namespace` 标志是相同的. 这主要在服务器端有用， 如果你想在一个版本库中存储单个版本库的多个 `fork`, 只要保持引用是隔离的就可以。

**`GIT_FLUSH`**

强制 Git 在向标准输出增量写入时使用没有缓存的 I/O。 设置为 1 让 Git 刷新更多， 设置为 0 则使所有的输出被缓存。 默认值（若此变量未设置）是根据活动和输出模式的不同选择合适的缓存方案。

**`GIT_REFLOG_ACTION`**

让你可以指定描述性的文字写到 reflog 中。 这有个例子：

```
$ GIT_REFLOG_ACTION="my action" git commit --allow-empty -m 'my message'
[master 9e3d55a] my message
$ git reflog -1
9e3d55a HEAD@{0}: my action: my message
```

--------------------------------------------------------------------------------
