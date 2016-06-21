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

检查文件是否来源可信

### 7.4.1 GPG 介绍

开始签名之前，需要先配置GPG并安装个人密钥。

如果没有就新创建一个

`$ gpg --gen-key` 需要输入名字，电子邮件，注释，其他信息可以选择默认。

使用`$ gpg --list-keys`来查生成的密钥。

	$ gpg --list-keys
	/c/Users/k/.gnupg/pubring.gpg
	-----------------------------
	pub   2048R/DCBD0501 2016-06-13
	uid                  username (comment) <gmail@gmail.com>
	sub   2048R/AE07F126 2016-06-13

当拥有一个可以签署的私钥，通过下方命令来签署。

`git config --global user.signingkey [DCBD0501]` [DCBD0501]为私钥编号。

现在Git使用密钥来签署标签与提交。

使用`$ gpg --armor --export [DCBD0501]`来查询密钥。

从`-----BEGIN PGP PUBLIC KEY BLOCK-----`到   `-----END PGP PUBLIC KEY BLOCK-----`结束。添加到项目标记每个人的提交吧。

### 7.4.2 签署标签

签署命令时，使用`-S ` 代替`-a`。

*$ git tag -a -m "[注释]"命令为标签加注释。*

>**例子：**
>
>		$ git tag -s v1.5 -m 'my signed 1.5 tag'
>	
>		You need a passphrase to unlock the secret key for
>		user: "Ben Straub <ben@straub.cc>"
>		2048-bit RSA key, ID 800430EB, created 2014-05-04

运行`$ git show [v1.5]`可以查看到GPG签名。[v1.5]为标签号。
>	**例子：**
>		
>		$ git show v1.5
>		tag v1.5
>		Tagger: Ben Straub <ben@straub.cc>
>		Date:   Sat May 3 20:29:41 2014 -0700
>		
>		my signed 1.5 tag
>		-----BEGIN PGP SIGNATURE-----
>		Version: GnuPG v1
>		
>		iQEcBAABAgAGBQJTZbQlAAoJEF0+sviABDDrZbQH/09PfE51KPVPlanr6q1v4/Ut
>		LQxfojUWiLQdg2ESJItkcuweYg+kc3HCyFejeDIBw9dpXt00rY26p05qrpnG+85b
>		hM1/PswpPLuBSr+oCIDj5GMC2r2iEKsfv2fJbNW8iWAXVLoWZRF8B0MfqX/YTMbm
>		ecorc4iXzQu7tupRihslbNkfvfciMnSDeSvzCpWAHl7h8Wj6hhqePmLm9lAYqnKp
>		8S5B/1SSQuEAjRZgI4IexpZoeKGVDptPHxLLS38fozsyi0QyDyzEgJxcJQVMXxVi
>		RUysgqjcpT8+iQM1PblGfHR4XAhuOqN5Fx06PSaFZhqvWFezJ28/CLyX5q+oIVk=
>		=EFTF
>		-----END PGP SIGNATURE-----
>		
>		commit ca82a6dff817ec66f44342007202690a93763949
>		Author: Scott Chacon <schacon@gee-mail.com>
>		Date:   Mon Mar 17 21:52:11 2008 -0700
>		
>		    changed the version number
	
### 7.4.3 验证标签

使用`git tag -v [标签名]`验证使用GPG的签名。

> **例子：**
>
>		$ git tag -v v1.4.2.1
>		object 883653babd8ee7ea23e6a5c392bb739348b1eb61
>		type commit
>		tag v1.4.2.1
>		tagger Junio C Hamano <junkio@cox.net> 1158138501 -0700
>	
>		GIT 1.4.2.1
>	
>		Minor fixes since 1.4.2, including git-mv and git-http with alternates.
>		gpg: Signature made Wed Sep 13 02:08:25 2006 PDT using DSA key ID F3119B9A
>		gpg: Good signature from "Junio C Hamano <junkio@cox.net>"
>		gpg:                 aka "[jpeg image of size 1513]"
>		Primary key fingerprint: 3565 2A26 2040 E066 C9A7  4A7D C0C6 D9A4 F311 9B9A

如果没有署名者的公钥，你会得到报错。

>		gpg: Signature made Wed Sep 13 02:08:25 2006 PDT using DSA key ID F3119B9A
>		gpg: Can't check signature: public key not found
>		error: could not verify the tag 'v1.4.2.1'
	
### 7.4.4 签署提交

在`$ git commit -m "注释"`时加上`-S`，使用带签名的提交。

> **例子：**
>
>		$ git commit -a -S -m 'signed commit'
>		
>		You need a passphrase to unlock the secret key for
>		user: "Scott Chacon (Git signing key) <schacon@gmail.com>"
>		2048-bit RSA key, ID 0A46826A, created 2014-06-04
>	
>		[master 5c3386c] signed commit
>		 4 files changed, 4 insertions(+), 24 deletions(-)
> 		 rewrite Rakefile (100%)
>		 create mode 100644 lib/git.rb

使用`$ git log`时，加上`--show-signature`来查看签名。

>**例子：**
>
>		$ git log --show-signature -1
>		commit 5c3386cf54bba0a33a32da706aa52bc0155503c2
>		gpg: Signature made Wed Jun  4 19:49:17 2014 PDT using RSA key ID 0A46826A
>		gpg: Good signature from "Scott Chacon (Git signing key) <schacon@gmail.com>"
>		Author: Scott Chacon <schacon@gmail.com>
>		Date:   Wed Jun 4 19:49:17 2014 -0700
> 
>	         signed commit

也可以配置`git log`时，加入`%G？`格式化输出。

> **例子：**
>
>		$ git log --pretty="format:%h %G? %aN  %s"
>	
>		5c3386c G Scott Chacon  signed commit
>		ca82a6d N Scott Chacon  changed the version number
>		085bb3b N Scott Chacon  removed unnecessary test code
>		a11bef0 N Scott Chacon  first commit
	

在`git merge`与`git pull`中，可以使用`--verify-signatures`来检查并拒绝没有可信GPG的提交。

合并一个未签名提交的分支时，合并不会生效

`$ git merge --verify-signatures non-verify`。

>**例子：**
>    
>		$ git merge --verify-signatures non-verify
>		fatal: Commit ab06180 does not have a GPG signature.

如果合并的分支，有有效的GPG签名时，合并命令会提示GPG签名已经检查过了，然后继续。

>**例子：**
>
>		$ git merge --verify-signatures signed-branch
>		Commit 13ad65e has a good GPG signature by Scott Chacon (Git signing key) <schacon@gmail.com>
>		Updating 5c3386c..13ad65e
>		Fast-forward
>		 README | 2 ++
>		 1 file changed, 2 insertions(+)

还可以在`git merge`命令后加`-S`来签署合并。

>**例子：**
>
>		$ git merge --verify-signatures -S  signed-branch
>		Commit 13ad65e has a good GPG signature by Scott Chacon (Git signing key) <schacon@gmail.com>
>		
>		You need a passphrase to unlock the secret key for
>		user: "Scott Chacon (Git signing key) <schacon@gmail.com>"
>		2048-bit RSA key, ID 0A46826A, created 2014-06-04
>		
>		Merge made by the 'recursive' strategy.
>		 README | 2 ++
>		 1 file changed, 2 insertions(+)



### 7.4.5 每个人必须签署

确定要使用签名时，团队的每个人都要建立GPG签名，不然会很麻烦。


-----


## 7.5 搜索

介绍用于查找函数或定义的两种工具。

### 7.5.1 Git Grep

使用`git grep -n [关键词]`搜索目录中的关键词，并显示行号。`-n`表示输出行号。

>**例子：**
>
>		$ git grep -n git
>		EADME.md:1:# github使用测试
>		README.md:6:- [ ] git 文档笔记 进行中
>		README.md:7:- [ ] git markdown 风格制作中
>		README.md:9:- [ ] 如何将github日记用，研究中
>		git/git1_2.md:1:# git使用说明
>		git/git1_2.md:3:[git官方文档](https://git-scm.com/book/en/v2)
>		git/git1_2.md:5:git GUI乱码修正

如果使用`$ git grep --count [关键词]`仅显示那些文件包含关键词，每个文件有多少个匹配。

**例子：**

>		$ git grep --count git
>		README.md:4
>		git/git1_2.md:116
>		git/git3.md:119
>		git/git4.md:81
>		git/git5_6.md:107
>		git/git7.md:120
>		git/git8_9.md:1
>		git/github_more_key.md:12
>		ubuntu.md:3
		
如果想看匹配的行是属于哪一个函数，使用`git grep -p [gmtime_r] [*.c]`[gmtime_r]为关键词，[*.c]为所属文件。

多个词搜索使用`$ git grep -e ['#define'] --and -e [SORT_DIRENT]`,['#define'] 与[SORT_DIRENT]为搜索词。

### 7.5.2 Git 日志搜索 

想要知道什么时候，添加或者引入，使用`git log -S[关键词] --oneline`

> **例子：**
>
>		$ git log -Sbranch --oneline
>		01dc744 GPG test
>		f2bddb1 7.3阅读完成
>		59d652f 7.3.1



* 行日志搜索

`$ git log -L :[关键词]:[文件名]`，展示代码中的一行或者一个函数的历史。

关于命令的表达方法：下方的两种方法完全一样。

	$ git log -L :git_deflate_bound:zlib.c
	
	$ git log -L '/unsigned long git_deflate_bound/',/^}/:zlib.c
	

-----


## 7.6 重写历史

修改提交历史如愿的展示。

### 7.6.1 修改最后一次提交

通常进行的是修改提交信息，或者修改你添加，修改和移除文件的暂存(add)

`$ git commit --amend`修改最后一次的提交信息。进入文本编辑器修改。

对于忘记添加文件，或移除文件，先运行`git add `或`git rm`，在运行`git commit --amend`。


### 7.6.2 修改多个提交信息

使用命令`$ git rebase -i [HEAD~3]`[HEAD~3]修改前多少次的提交。  
之后在文本模式下输入要修改的注释，顺序为倒叙。  
*注意：前多少次到最新的注释全部会显示。*

随后确定要的修改提交注释，将要修改的注释前，`pick` 改为`edit`，

>**例如：**
>
>		edit f7f3f6d changed my name a bit
>		pick 310154e updated README formatting and added blame
>		pick a5f4a0d added cat-file

在退出编辑文档。

输入`git commit --amend` 修改刚刚确定需改的提交注释。

最后 `$ git rebase --continue` 执行变基。 

如果修改失败了可以使用`rm -fr "[项目目录下]/.git/rebase-merge"`清除失败记录。


### 7.6.3 重新排序提交


使用 `$ git rebase -i [HEAD^2]` [HEAD^2]为后退步数。

	pick f7f3f6d changed my name a bit
	pick 310154e updated README formatting and added blame
	pick a5f4a0d added cat-file

将要删除行直接删除，如下。

	pick 310154e updated README formatting and added blame
	pick f7f3f6d changed my name a bit

### 7.6.4 压缩提交

将多个提交压缩为一个提交。

使用 `$ git rebase -i [HEAD^2]` [HEAD^2]为后退步数。

在文本文档中将`pick`改为`squash`。

说明：如果要将三个提价压缩为一个，见下面的例子。对于多条提交同样适用。`pick`为最后的提交。

	pick f7f3f6d changed my name a bit
	squash 310154e updated README formatting and added blame
	squash a5f4a0d added cat-file

保存后，三次提交压缩为一次提交。

### 7.6.5 拆分提交

使用 `$ git rebase -i [HEAD^2]` [HEAD^2]为后退步数。

将要拆分的提交设置为`edit`

在`git commit --amend`出现后，

运行`$ git reset HEAD^`,  

随后暂存与提交被重置的文件,

最后运行`$ git rebase --continue`结束。

### 7.6.6 核武器级选项：filter-branch

`$ git filter-branch`可以大量的改变文件。

* 从每一个提交移除一个文件

	从整个提交历史中移除[文件]，命令如下：
	
	`$ git filter-branch --tree-filter 'rm -f [文件名]' HEAD`

	`--tree-filter`在检出项目的，每一个提交后，运行指定的命令，然后重新提交结果。

	为了`filter-branch`在所有分支上运行，加`--all`


* 使一个子目录做为新的根目录

	使用`$ git filter-branch --subdirectory-filter trunk HEAD`

	Git会自动移除所有不影响子目录的提交。

* 全局修改邮箱地址

	忘记设置名字与邮箱地址，或者修改邮箱。  

		$ git filter-branch --commit-filter '
	        if [ "$GIT_AUTHOR_EMAIL" = "schacon@localhost" ];
	        then
	                GIT_AUTHOR_NAME="Scott Chacon";
	                GIT_AUTHOR_EMAIL="schacon@example.com";
	                git commit-tree "$@";
	        else
	                git commit-tree "$@";
	        fi' HEAD
		
	命令会修改每一个提交SHA-1校验。

-----

## 7.7 重置揭密

 介绍`reset`与`checkout`

### 7.7.1 三棵树

|树|用途|
|:----:|:----|
|HEAD|上次提交的快照，下次提交的起点|
|Index|预提交的快照(`add`命令)|
|Working Directory|沙盒|

*注意：“树”指的是“文件的集合”*

* HEAD
	
	HEAD是当前分支的指针，总是指向分支上最后一次提交。
	
	`$ git cat-file -p HEAD`查看实际的目录列表。
	
	`$ git ls-tree -r HEAD`当前全部文件的SHA-1校验
	
*注意：`cat-file`与`ls-tree`是底层命令。*

* 索引

	索引，将文件提交到暂存区。

* 工作目录

	项目的工作目录，在没有暂存与提交前，进行文件修改操作。

### 7.7.2 工作流程

Git 主要是通过操纵三棵树，来以连续的状态记录项目的快照。

参考图，如下：

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/reset-workflow.png)

下方以图片的形式详细说明Git的工作过程。

首先使用`git init`创建一个Git仓库，其中HEAD指向不明，因为什么都没有创建。

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/reset-ex1.png)

当是用`git add`后

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/reset-ex2.png)

当使用`git commit`后

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/reset-ex3.png)

当修改文件时，

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/reset-ex4.png)

再次使用`git add`时

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/reset-ex5.png)

`git commit`提交后HEAD移动


![](https://git-scm.com/book/en/v2/book/07-git-tools/images/reset-ex6.png)


### 7.7.3 重置的作用

用图的方式说明`reset`的意义。下方例子继承上边的。说明`$ git reset HEAD~`的实际运作过程。
开始第三次修改并保存。

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/reset-start.png)

* 第 1 步：移动 HEAD
	
	`reset`先移动HEAD指向的分支，使用`reset --soft`分支仅仅停在上一个位置。
	
	![](https://git-scm.com/book/en/v2/book/07-git-tools/images/reset-soft.png)
	
	使用命令`$ git reset HEAD~`,之后再使用`git commit`。效果与使用`git commit --amend`一致。

* 第 2 步：更新索引（--mixed）

	现在使用`git status`查看状态，可以看到HEAD与索引目录的区别，之后使用`reset`将快照[提交]指向HEAD的新位置。
	
	![](https://git-scm.com/book/en/v2/book/07-git-tools/images/reset-mixed.png)

	如果增加`--mixed`选择，`reset`会停下来。命令为

		$ git reset --mixed HEAD~

	图上做了什么：撤销上一次提交，同时取消暂存。

* 第 3 步：更新工作目录（--hard）

	最后`reset`让文件变回上次提交。

	![](https://git-scm.com/book/en/v2/book/07-git-tools/images/reset-hard.png)

	*注意：`--hard`选项不能撤销。*


* 回顾

	`reset`会以特定的顺序重写三棵树，可以使用特定参数使选项停止。
	
	1.	移动HEAD分支的指向。使用命令`$ git reset --soft HEAD~`  
	2.  使索引看起来像HEAD。使用命令`$ git reset --mixed HEAD~`  
	3.  使文件看起来像索引。使用命令`$ git reset --hard HEAD~`

### 7.7.4 通过路径来重置

通过指定路径或文件来重置，依然使用`reset`命令。

比如运行`$ git reset file.txt`实际上是`$ git reset --mixed HEAD file.txt`的简写。

### 7.7.5 压缩

假设你有一个项目，第一次提交中有一个文件，第二次提交增加了一个新的文件并修改了第一个文件，第三次提交再次修改了第一个文件。 由于第二次提交是一个未完成的工作，因此你想要压缩它。

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/reset-squash-r1.png)

运行`$ git reset --soft HEAD~2`,将HEAD分支移动到一个旧的提交上。

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/reset-squash-r2.png)

之后使用`$ git commit`重新提交。

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/reset-squash-r3.png)

### 7.7.6 检出

说明`reset`与`checkout`的区别。

* 不带路径

	`git checkout [branch]`与`git reset --hard [branch]`非常相似
	
	**二者的不同点**
	
	1.	`git checkout`对工作目录是安全的，`checkout`会先试着合并一下。而`reset`会直接替换所有的东西。
	
	2.  更新HEAD的方式不同，`reset`会移动HEAD分支的指向，而`checkout`只会移动HEAD本身到另一分支。

	**例子：**

	假设有`master`和`develop`分支，它们分别指向不同提交，现在HEAD指向`develop`。  
	
	运行`git reset master`,那么`develop`会和`master`指向同一个提交。  

	如果运行的是`git checkout master`，那么`develop`不会移动，HEAD自身会移动。

	下图解释的非常清楚。
	
	![](https://git-scm.com/book/en/v2/book/07-git-tools/images/reset-checkout.png)

* 带路径

	运行`checkout`的另一种方式是指定文件路径。  

	使用的效果与`$ git reset [branch] file`一致。


----


## 7.8 高级合并

用来说明合并过程中可能遇到的问题，以及问题的解决方法。

### 7.8.1 合并冲突

在合并分支前，先清理工作目录，确保没有尚未储存的文件。如果有先保存到一个临时的分支，或者提交到当前分支。

* 中断一次合并
	
	`git merge --abort`

	`git merge --abort`选项会尝试恢复到合并前状态。
	
	*注意：当目录中有没有储存，没提交的文件时，命令不能工作*

* 忽略空白

	当更改制表符时，常见的错误。Unix换到DOS时，等等类似情况。

	忽略文件中的空白，而后合并。
	1. `-Xignore-all-space`忽略任意数量的已有空白的修改
	2. `-Xignore-space-change`忽略所有空白修改

		**例子**	

			$ git merge -Xignore-space-change whitespace
			Auto-merging hello.rb
			Merge made by the 'recursive' strategy.
			 hello.rb | 2 +-
			 1 file changed, 1 insertion(+), 1 deletion(-)			
			
* 手动文件再合并

	先将冲突文件提取为备份，使用`stages`控制提取是的文本。

	|stages|说明|
	|:----:|:----|
	|stages 1|文件的共同祖先版本|
	|stages 2|你的版本|
	|stages 3|来自MERGE_HEAD的版本，将要合并的版本|

	**例子：**

		$ git show :1:hello.rb > hello.common.rb
		$ git show :2:hello.rb > hello.ours.rb
		$ git show :3:hello.rb > hello.theirs.rb

	也可以使用`ls-files -u`来得到文件的SHA-1值。

	**效果如下**

		$ git ls-files -u
		100755 ac51efdc3df4f4fd328d1a02ad05331d8e2c9111 1	hello.rb
		100755 36c06c8752c78d2aff89571132f3bf7841a7b5c3 2	hello.rb
		100755 e85207e04dfdd5eb0a1e9febbc67fd837c44a1cd 3	hello.rb


	手动修改文件后，使用`git merge-file`来合并分离开的文件。

	`$ dos2unix [文件]`将指定文件的制表符，从DOS改为unix。

	**例子：**

		$ dos2unix hello.theirs.rb
		dos2unix: converting file hello.theirs.rb to Unix format ...
		
		$ git merge-file -p \
		    hello.ours.rb hello.common.rb hello.theirs.rb > hello.rb
		
		$ git diff -b
		diff --cc hello.rb
		index 36c06c8,e85207e..0000000
		--- a/hello.rb
		+++ b/hello.rb
		@@@ -1,8 -1,7 +1,8 @@@
		  #! /usr/bin/env ruby
		
		 +# prints out a greeting
		  def hello
		-   puts 'hello world'
		+   puts 'hello mundo'
		  end
		
		  hello()
		
	最后使用`git clean`命令来清理手动产出的文件。

	**例子：**

		$ git clean -f
		Removing hello.common.rb
		Removing hello.ours.rb
		Removing hello.theirs.rb
			
* 检出冲突

	**例子：**
		
		$ git log --graph --oneline --decorate --all
		* f1270f7 (HEAD, master) update README
		* 9af9d3b add a README
		* 694971d update phrase to hola world
		| * e3eb223 (mundo) add more tests
		| * 7cff591 add testing script
		| * c3ffff1 changed text to hello mundo
		|/
		* b7dcc89 initial hello world code

	现在有只在 master 分支上的三次单独提交，还有其他三次提交在 mundo 分支上。 如果我们尝试将 mundo 分支合并入 master 分支，我们得到一个冲突。
	
		$ git merge mundo
		Auto-merging hello.rb
		CONFLICT (content): Merge conflict in hello.rb
		Automatic merge failed; fix conflicts and then commit the result.

	关于冲突的显示方法`$ git checkout --conflict [文件]`，检出文件并替换合并冲突标记。
	
	`-- conflict`参数有`diff3`或`merge`。使用`diff3`时，增加`base`版本

	**例子：**

		$ git checkout --conflict=diff3 hello.rb
		
	返回结果如下：

		#! /usr/bin/env ruby
		
		def hello
		<<<<<<< ours
		  puts 'hola world'
		||||||| base
		  puts 'hello world'
		=======
		  puts 'hello mundo'
		>>>>>>> theirs
		end
		
		hello()

如果喜欢这种格式可以使用`merge.conflictstyle`设置`diff3`为默认选项。

	$ git config --global merge.conflictstyle diff3

还可以使用`--ours`和`--theirs`,选择留下一边，删除另一边。

* 合并日志
	
	使用`git log`解决合并冲突。
	
	**例子：**
	
		$ git log --oneline --left-right HEAD...MERGE_HEAD
		< f1270f7 update README
		< 9af9d3b add a README
		< 694971d update phrase to hola world
		> e3eb223 add more tests
		> 7cff591 add testing script
		> c3ffff1 changed text to hello mundo
	
	使用`--merge`选项，会只显示任何一个触发合并冲突的提交。

		$ git log --oneline --left-right --merge
		< 694971d update phrase to hola world
		> c3ffff1 changed text to hello mundo

	如果在运行命令是加`-p`选项，会看到所有冲突文件的区别，很有用。	

* 组合式差异格式

	当合并冲突时，直接运行`git diff`会显示一个独特的输出格式。这种格式叫作`组合式差异`
	
	**例子：**

		$ git diff
		diff --cc hello.rb
		index 0399cd5,59727f0..0000000
		--- a/hello.rb
		+++ b/hello.rb
		@@@ -1,7 -1,7 +1,11 @@@
		  #! /usr/bin/env ruby
		
		  def hello
		++<<<<<<< HEAD
		 +  puts 'hola world'
		++=======
		+   puts 'hello mundo'
		++>>>>>>> mundo
		  end
		
		  hello()	

	可以在后，使用`git log`来获取相同信息，增加`-p`与`--cc`参数。

	**例子：**
		
		$ git log --cc -p -1
		commit 14f41939956d80b9e17bb8721354c33f8d5b5a79
		Merge: f1270f7 e3eb223
		Author: Scott Chacon <schacon@gmail.com>
		Date:   Fri Sep 19 18:14:49 2014 +0200
		
		    Merge branch 'mundo'
		
		    Conflicts:
		        hello.rb
		
		diff --cc hello.rb
		index 0399cd5,59727f0..e1d0799
		--- a/hello.rb
		+++ b/hello.rb
		@@@ -1,7 -1,7 +1,7 @@@
		  #! /usr/bin/env ruby
		
		  def hello
		-   puts 'hola world'
		 -  puts 'hello mundo'
		++  puts 'hola mundo'
		  end
		
		  hello()	
	

### 7.8.2 撤消合并

假设现在在一个特性分支上工作，不小心将其合并到 master 中，现在提交历史看起来是这样  

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/undomerge-start.png)

有两种方法用来撤销合并`修复引用`与`还原提交`

* 修复引用
   
	运行`git reset --hard HEAD~`，要在没有提交之前，提交到服务器后，在执行会带来不必要的麻烦。
	
	它会重新提交历史，提交后不要使用，提交后不要使用，提交后不要使用。

	**在 git reset --hard HEAD~ 之后的历史**

	![](https://git-scm.com/book/en/v2/book/07-git-tools/images/undomerge-reset.png)

* 还原提交

	还原上一次的提交`$ git revert -m [1] HEAD`[1]为还原的步数。

	使用命令后效果如下图。要撤销C4的合并修改，同时保存C6的内容。使新生成的^m内容完全与C6一致。

	![](https://git-scm.com/book/en/v2/book/07-git-tools/images/undomerge-revert.png)

	这时如果将`topic`合并到`master`中，git会报错。因为已经合并过到M了。

	如果再次在`topic`分支进行修改，随后合并到`master`，那么C3，C4就没有合并到分支上，从而出现错误。
	
	说明图如下，

	![](https://git-scm.com/book/en/v2/book/07-git-tools/images/undomerge-revert2.png)

	这时可以撤销到M，更新到^^M,分支，M与^^M的内容相同，随后合并修改后的C7。

		$ git revert HEAD^   //撤销两次。

	![](https://git-scm.com/book/en/v2/book/07-git-tools/images/undomerge-revert3.png)
	
	这样合并的结果就包含了，`topic`的修改。

### 7.8.3 其他类型的合并

* 我们的或他们的偏好

	默认情况下，git在合并产生冲突时，标记错误，并要求用户手动修改。

	当在合并时使用`-Xours`或`-Xtheirs`，可以自动的忽略一个而选择另一个。

	`-Xours`选择保存之前的提交。

	`-Xtheirs`选择保存之后的提交。

	**例子：**

		$ git merge -Xours mundo


* 子树合并

	当有两个项目的时候，其中一个映射到另一个子目录，合并时执行一个子树合并，git通常会自动计算之后正确合并。

	先拉取另一分支到目录，切入本地分支与拉取得分支，会发现两者的目录不同。

	假设本地分支为master，拉取的分支为rack_branch。

	先切回master，随后执行`$ git read-tree --prefix=rack/ -u rack_branck`在master的目录下建立rack文件夹，并包含rack的文件。

	当rack项目更新是，依然可以切到rack_brank分支进行拉取更新。

	将rack拉取的更新合并到master，使用命令：

		$ git checkout master
		$ git merge --squash -s recursive -Xsubtree=rack rack_branch

	*注意：*

	当要查看rack子目录与rack_branch分支的差异时，使用`$ git diff-tree`而不是`$ git diff`。

		$ git diff-tree -p rack_branch

	或者，将子目录与最后一次抓取的master分支进行比较。

		$ git diff-tree -p rack_remote/master


----


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