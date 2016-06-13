# 7. Git 工具

Git技能点进级

## 7.1 选择修订版本

Git允许通过几种方法来指明提交

### 7.1.1 单个修订版本

可以通过Git给出的SHA-1值来获取一次提交。当然有其他的方法。

### 7.1.2 简短的 SHA-1

Git 只提交几个SHA-1开头编码就可以指定文件，编码要大于4位。

**例如：**

	$ git show 1c002dd4b536e7479fe34593e72e6c6c1819e53b
	$ git show 1c002dd4b536e7479f
	$ git show 1c002d

上方3条命令，都显示相同的内容。最后一条使用起来更加的方便。

在使用`git log`后加上`--abbrev-commit`，输出结果就显示短SHA-1码，默认为7位，有时会自动增加字符数。

**例子：**

	$ git log --abbrev-commit --pretty=oneline
	ca82a6d changed the version number
	085bb3b removed unnecessary test code
	a11bef0 first commit
	
在`$ git log --abbrev-commit --pretty=oneline`加上 `-[数字]`，则显示最后几次的提交。

SHA-1发生冲突的可能性，非常小。

### 7.1.3 分支引用

指明提交的最直接方法为指向分支。使用`git show `加SHA-1码，或者分支名。两者的作用是一样的。

	
	$ git show ca82a6dff817ec66f44342007202690a93763949
	$ git show topic1

上面的命令结果是一样的。

`rev-parse`命令可以查询到分支的SHA-1。

**例子：**
	
	$ git rev-parse topic1
	ca82a6dff817ec66f44342007202690a93763949


### 7.1.4 引用日志

Git后台保持一个引用日志，记录HEAD和分支指向历史。

使用`git reflog`查看引用日志，后方加`@{n}`显示前5次的提交，`{n}`为数字。`{yesterday}`为显示昨天的提交。  

当HEAD的位置变化时，Git就会记录。

	$ git show HEAD@{5}
	$ git show master@{yesterday}

*引用日志记录的仅仅是本地操作的内容*
	
### 7.1.5 祖先引用

祖先引用是另一种指明一个提到方式。

使用`git show HEAD~`查看上一次提交。`HEAD`可以换为第[n]提交的SHA-1码。

当使用SHA-1码加[数字]时，使用`git show [d921970]^[2]`

在`~ `后添加数字，代表第[n]次前的提交。`$ git show HEAD~[3]`

### 7.1.6 提交区间

* 双点

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/double-dot.png)

想要查看 experiment分支中有哪些提交没有合并到master分支中。

可以使用`master..experiment`。

**例子**

	$ git log master..experiment
	D
	C

想要查看远端分支

	git log origin/master..HEAD

* 多点

查看`refA`或`refB`，但不被`refC`包含。下方的两个命令效果相同。

	$ git log refA refB ^refC  
	$ git log refA refB --not refC
	
* 三点

查看被两者中的一个包含，但不是两者同时包含。

	$ git log master...experiment
	F
	E
	D
	C

使用`$ git log --left-right master...experiment`能显示提交处于哪一侧分支。

	$ git log --left-right master...experiment
	< F
	< E
	> D
	> C


----

		
## 7.2 交互式暂存

运行`git add`时，使用`-i`或者`--interactive`,进入交互式终端模式。

	$ git add -i
	           staged     unstaged path
	  1:    unchanged        +0/-1 TODO
	  2:    unchanged        +1/-1 index.html
	  3:    unchanged        +5/-1 lib/simplegit.rb
	
	*** Commands ***
	  1: status     2: update      3: revert     4: add untracked
	  5: patch      6: diff        7: quit       8: help
	What now>

### 7.2.1 暂存与取消暂存文件

在 ` What now> ` 键入`2`或`u`，脚本将提升想要暂存那个文件。

	What now> 2
	           staged     unstaged path
	  1:    unchanged        +0/-1 TODO
	  2:    unchanged        +1/-1 index.html
	  3:    unchanged        +5/-1 lib/simplegit.rb
	Update>>

要暂存TODO与index.html文件，可以输入数字：

	Update>> 1,2
	           staged     unstaged path
	* 1:    unchanged        +0/-1 TODO
	* 2:    unchanged        +1/-1 index.html
	  3:    unchanged        +5/-1 lib/simplegit.rb
	Update>>
	
有`*`的文件将会被暂存。如果在`update>>`后直接按回车，Git会暂存之前选择的文件：

	Update>>
	updated 2 paths
	
	*** Commands ***
	  1: status     2: update      3: revert     4: add untracked
	  5: patch      6: diff        7: quit       8: help
	What now> 1
	           staged     unstaged path
	  1:        +0/-1      nothing TODO
	  2:        +1/-1      nothing index.html
	  3:    unchanged        +5/-1 lib/simplegit.rb

TODO与index.html文件已经被暂存，simplegit，rb文件还未被暂存。要取消时TODO，使用`3`或`r`：

	*** Commands ***
	  1: status     2: update      3: revert     4: add untracked
	  5: patch      6: diff        7: quit       8: help
	What now> 3
	           staged     unstaged path
	  1:        +0/-1      nothing TODO
	  2:        +1/-1      nothing index.html
	  3:    unchanged        +5/-1 lib/simplegit.rb
	Revert>> 1
	           staged     unstaged path
	* 1:        +0/-1      nothing TODO
	  2:        +1/-1      nothing index.html
	  3:    unchanged        +5/-1 lib/simplegit.rb
	Revert>> [enter]
	reverted one path

查看状态，已经取消暂存TODO文件：
	
	*** Commands ***
	  1: status     2: update      3: revert     4: add untracked
	  5: patch      6: diff        7: quit       8: help
	What now> 1
	           staged     unstaged path
	  1:    unchanged        +0/-1 TODO
	  2:        +1/-1      nothing index.html
	  3:    unchanged        +5/-1 lib/simplegit.rb
		
想要查看暂存区内容的区别，可以使用`6`或`d`。会显示暂存列表，可以从中选择想要查看的文件，与`git diff --cached`非常相似。

	*** Commands ***
	  1: status     2: update      3: revert     4: add untracked
	  5: patch      6: diff        7: quit       8: help
	What now> 6
	           staged     unstaged path
	  1:        +1/-1      nothing index.html
	Review diff>> 1
	diff --git a/index.html b/index.html
	index 4d07108..4335f49 100644
	--- a/index.html
	+++ b/index.html
	@@ -16,7 +16,7 @@ Date Finder
	
	 <p id="out">...</p>
	
	-<div id="footer">contact : support@github.com</div>
	+<div id="footer">contact : email.support@github.com</div>
	
	 <script type="text/javascript">
	
### 7.2.2 暂存补丁

git可以暂存文件的特定部分。simplegit.rb 文件中做了两处修改，但只想要暂存其中的一个而不是另一个。

在交互模式下，输入`5`或`p`，git会询问要暂存那些文件，然后，对已选择文件的每一个部分，它都会一个个地显示文件区别并询问你是否想要暂存它们：

	diff --git a/lib/simplegit.rb b/lib/simplegit.rb
	index dd5ecc4..57399e0 100644
	--- a/lib/simplegit.rb
	+++ b/lib/simplegit.rb
	@@ -22,7 +22,7 @@ class SimpleGit
	   end
	
	   def log(treeish = 'master')
	-    command("git log -n 25 #{treeish}")
	+    command("git log -n 30 #{treeish}")
	   end
	
	   def blame(path)
	Stage this hunk [y,n,a,d,/,j,J,g,e,?]?

这时有很多选项。 输入 ? 显示所有可以使用的命令列表：

	Stage this hunk [y,n,a,d,/,j,J,g,e,?]? ?
	y - stage this hunk
	n - do not stage this hunk
	a - stage this and all the remaining hunks in the file
	d - do not stage this hunk nor any of the remaining hunks in the file
	g - select a hunk to go to
	/ - search for a hunk matching the given regex
	j - leave this hunk undecided, see next undecided hunk
	J - leave this hunk undecided, see next hunk
	k - leave this hunk undecided, see previous undecided hunk
	K - leave this hunk undecided, see previous hunk
	s - split the current hunk into smaller hunks
	e - manually edit the current hunk
	? - print help

通常情况下可以输入 `y` 或 `n` 来选择是否要暂存每一个区块，当然，暂存特定文件中的所有部分或为之后的选择跳过一个区块也是非常有用的。 如果你只暂存文件的一部分，状态输出可能会像下面这样：

	What now> 1
	           staged     unstaged path
	  1:    unchanged        +0/-1 TODO
	  2:        +1/-1      nothing index.html
	  3:        +1/-1        +4/-0 lib/simplegit.rb

最后执行`git commit`保存修改。

使用`git add -p`或`git add --patch`也能启动脚本。

*测试时，似乎是有 bug*


----

## 7.3 储藏与清理

储蓄当前工作，而非提交，方便随时回来。

使用`git stash `命令。

### 7.3.1 储藏工作

当想要切换分支，但是还不想提交之前的改动，运行`git stash` 或 `git stash save`，可以看到改动：

	$ git stash
	Saved working directory and index state \
	  "WIP on master: 049d078 added the index file"
	HEAD is now at 049d078 added the index file
	(To restore them type "git stash apply")
	
在运行`git status`时可以看到工作目录是干净的，这时可以切换分支了。

要查看储存的东西，可以使用`git stach list`:
	
	$ git stash list
	stash@{0}: WIP on master: 049d078 added the index file
	stash@{1}: WIP on master: c264051 Revert "added file_size"
	stash@{2}: WIP on master: 21d80a5 added number to log

当想要重新工作时，运行`git stach apply` 或者 `git stash apply stash@{2}`运行之前的储存。如果不指定`stash@{2}`,{2}可以为任意存储编号，git默认指定为最新的储存。

当储存的文件被修改，但是因为储存(stash)前没有暂存(add)，想要重新暂存（add）。要使用`--index`选项运行`git stash apply`命令。

**注意：需要被更改过的文件暂存过（add），使用`$ git stash apply --index`将储存变为修改过的样子，并提交到暂存(add)**


	$ git stash apply --index
	# On branch master
	# Changes to be committed:
	#   (use "git reset HEAD <file>..." to unstage)
	#
	#      modified:   index.html
	#
	# Changed but not updated:
	#   (use "git add <file>..." to update what will be committed)
	#
	#      modified:   lib/simplegit.rb
	
使用`git stash drop stash@{0}`删除储存，可以指定储存位置。

使用`git stash pop`回到文件储存时的状态(最新的储存)，并从储存中删除。


### 7.3.2 创造性的储藏

* `$ git stash --keep-index`

	说明：不储存通过`git add`已经暂存的东西。
	
		$ git status -s
		M  index.html
		 M lib/simplegit.rb
		
		$ git stash --keep-index
		Saved working directory and index state WIP on master: 1b65b17 added the index file
		HEAD is now at 1b65b17 added the index file
		
		$ git status -s
		M  index.html
		
* `$ git stash -u`与`$ git stash --include-untracked`

	说明：储存全部没有添加过的文件。`git add`过的文件。基本上是储存全部未提交的文件。
		
		$ git status -s
		M  index.html
		 M lib/simplegit.rb
		?? new-file.txt
		
		$ git stash -u
		Saved working directory and index state WIP on master: 1b65b17 added the index file
		HEAD is now at 1b65b17 added the index file
		
* `$ git stash --patch`

	说明：不储藏所有修改过的任何文件，但是会交互式地提示哪些改动想要储藏、哪些改动需要保存在工作目录中。


### 7.3.3 从储藏创建一个分支

将储存新建为一个分支。当修改储存的文件，在读取储存时，会出现错误。这时可以使用`$ git stash branch [新分支名]`，将储存的内容放到新分支上，随后自动删除储存。

**例子：**

	$ git stash branch testchanges
	Switched to a new branch "testchanges"
	# On branch testchanges
	# Changes to be committed:
	#   (use "git reset HEAD <file>..." to unstage)
	#
	#      modified:   index.html
	#
	# Changed but not updated:
	#   (use "git add <file>..." to update what will be committed)
	#
	#      modified:   lib/simplegit.rb
	#
	Dropped refs/stash@{0} (f0dfc4d5dc332d1cee34a634182e168c4efc3359)
	
### 7.3.4 清理工作目录

`git clean ` 用来移除未被跟踪的文件

另一个更安全的方法使用`git stash --all`移除全部，并存储起来。

使用`git clean -f -d`移除工作目录中的未跟踪文件以及空的子目录。`-f`是强制的意思。

使用`$ git clean -d -n`,将说明要移除什么。

	$ git clean -d -n
	Would remove test.o
	Would remove tmp/

默认`git clean`只移除没有忽略的未跟踪文件。在`.gitiignore`文件设置的被忽略文件，不会被删除。

`$ git clean -n -d -x`移除文件包括忽略的文件。

`-i`以交换模式运行`$ git clean`。


---


## 7.4 签署工作

检查文件是否可信

### 7.4.1 GPG 介绍

### 7.4.2 签署标签

### 7.4.3 验证标签

### 7.4.4 签署提交

### 7.4.5 每个人必须签署

## 7.5 搜索

### 7.5.1 Git Grep

### 7.5.2 Git 日志搜索 

* 行日志搜索

## 7.6 重写历史

### 7.6.1 修改最后一次提交

### 7.6.2 修改多个提交信息

### 7.6.3 重新排序提交

### 7.6.4 压缩提交

### 7.6.5 拆分提交

### 7.6.6 核武器级选项：filter-branch

* 从每一个提交移除一个文件
* 使一个子目录做为新的根目录
* 全局修改邮箱地址

## 7.7 重置揭密

### 7.7.1 三棵树

* HEAD

* 索引

* 工作目录

### 7.7.2 工作流程

### 7.7.3 重置的作用

* 第 1 步：移动 HEAD

* 第 2 步：更新索引（--mixed）

* 第 3 步：更新工作目录（--hard）

* 回顾

### 7.7.4 通过路径来重置

### 7.7.5 压缩

### 7.7.6 检出

* 不带路径

* 带路径

## 7.8 高级合并

### 7.8.1 合并冲突

* 中断一次合并

* 忽略空白

* 手动文件再合并

* 检出冲突

* 合并日志

* 组合式差异格式

### 7.8.2 撤消合并

* 修复引用

* 还原提交

### 7.8.3 其他类型的合并

* 我们的或他们的偏好

* 子树合并

## 7.9 Rerere

## 7.10 使用 Git 调试

### 7.10.1 文件标注

### 7.10.2 二分查找

## 7.11 子模块

### 7.11.1 开始使用子模块

### 7.11.2 克隆含有子模块的项目

### 7.11.3 在包含子模块的项目上工作

* 拉取上游修改

* 在子模块上工作

* 发布子模块改动

* 合并子模块改动

### 7.11.4 子模块技巧

* 子模块遍历

* 有用的别名

### 7.11.5 子模块的问题

## 7.12 打包

## 7.13 替换

## 7.14 凭证存储

### 7.14.1 底层实现

### 7.14.2 自定义凭证缓存