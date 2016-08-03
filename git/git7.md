<!--toc-->

- [7\. Git 工具](#7-git-工具)
	- [7.1 选择修订版本](#71-选择修订版本)
		- [7.1.1 单个修订版本](#711-单个修订版本)
		- [7.1.2 简短的 SHA-1](#712-简短的-sha-1)
		- [7.1.3 分支引用](#713-分支引用)
		- [7.1.4 引用日志](#714-引用日志)
		- [7.1.5 祖先引用](#715-祖先引用)
		- [7.1.6 提交区间](#716-提交区间)
	- [7.2 交互式暂存](#72-交互式暂存)
		- [7.2.1 暂存与取消暂存文件](#721-暂存与取消暂存文件)
		- [7.2.2 暂存补丁](#722-暂存补丁)
	- [7.3 储藏与清理](#73-储藏与清理)
		- [7.3.1 储藏工作](#731-储藏工作)
		- [7.3.2 创造性的储藏](#732-创造性的储藏)
		- [7.3.3 从储藏创建一个分支](#733-从储藏创建一个分支)
		- [7.3.4 清理工作目录](#734-清理工作目录)
	- [7.4 签署工作](#74-签署工作)
		- [7.4.1 GPG 介绍](#741-gpg-介绍)
		- [7.4.2 签署标签](#742-签署标签)
		- [7.4.3 验证标签](#743-验证标签)
		- [7.4.4 签署提交](#744-签署提交)
		- [7.4.5 每个人必须签署](#745-每个人必须签署)
	- [7.5 搜索](#75-搜索)
		- [7.5.1 Git Grep](#751-git-grep)
		- [7.5.2 Git 日志搜索](#752-git-日志搜索)
	- [7.6 重写历史](#76-重写历史)
		- [7.6.1 修改最后一次提交](#761-修改最后一次提交)
		- [7.6.2 修改多个提交信息](#762-修改多个提交信息)
		- [7.6.3 重新排序提交](#763-重新排序提交)
		- [7.6.4 压缩提交](#764-压缩提交)
		- [7.6.5 拆分提交](#765-拆分提交)
		- [7.6.6 核武器级选项：filter-branch](#766-核武器级选项filter-branch)
	- [7.7 重置揭密](#77-重置揭密)
		- [7.7.1 三棵树](#771-三棵树)
		- [7.7.2 工作流程](#772-工作流程)
		- [7.7.3 重置的作用](#773-重置的作用)
		- [7.7.4 通过路径来重置](#774-通过路径来重置)
		- [7.7.5 压缩](#775-压缩)
		- [7.7.6 检出](#776-检出)
	- [7.8 高级合并](#78-高级合并)
		- [7.8.1 合并冲突](#781-合并冲突)
		- [7.8.2 撤消合并](#782-撤消合并)
		- [7.8.3 其他类型的合并](#783-其他类型的合并)
	- [7.9 Rerere](#79-rerere)
	- [7.10 使用 Git 调试](#710-使用-git-调试)
		- [7.10.1 文件标注](#7101-文件标注)
		- [7.10.2 二分查找](#7102-二分查找)
	- [7.11 子模块](#711-子模块)
		- [7.11.1 开始使用子模块](#7111-开始使用子模块)
		- [7.11.2 克隆含有子模块的项目](#7112-克隆含有子模块的项目)
		- [7.11.3 在包含子模块的项目上工作](#7113-在包含子模块的项目上工作)
		- [7.11.4 子模块技巧](#7114-子模块技巧)
		- [7.11.5 子模块的问题](#7115-子模块的问题)
	- [7.12 打包](#712-打包)
	- [7.13 替换](#713-替换)
	- [7.14 凭证存储](#714-凭证存储)
		- [7.14.1 底层实现](#7141-底层实现)
		- [7.14.2 自定义凭证缓存](#7142-自定义凭证缓存)

<!-- tocstop -->

----


# 7\. Git 工具

Git技能点进级

## 7.1 选择修订版本

Git允许通过几种方法来指明提交

### 7.1.1 单个修订版本

可以通过Git给出的SHA-1值来获取一次提交。当然有其他的方法。

### 7.1.2 简短的 SHA-1

Git 只提交几个SHA-1开头编码就可以指定文件，编码要大于4位。

**例如：**

```
$ git show 1c002dd4b536e7479fe34593e72e6c6c1819e53b
$ git show 1c002dd4b536e7479f
$ git show 1c002d
```

上方3条命令，都显示相同的内容。最后一条使用起来更加的方便。

在使用`git log`后加上`--abbrev-commit`，输出结果就显示短SHA-1码，默认为7位，有时会自动增加字符数。

**例子：**

```
$ git log --abbrev-commit --pretty=oneline
ca82a6d changed the version number
085bb3b removed unnecessary test code
a11bef0 first commit
```

在`$ git log --abbrev-commit --pretty=oneline`加上 `-[数字]`，则显示最后几次的提交。

SHA-1发生冲突的可能性，非常小。

### 7.1.3 分支引用

指明提交的最直接方法为指向分支。使用`git show`加SHA-1码，或者分支名。两者的作用是一样的。

```
$ git show ca82a6dff817ec66f44342007202690a93763949
$ git show topic1
```

上面的命令结果是一样的。

`rev-parse`命令可以查询到分支的SHA-1。

**例子：**

```
$ git rev-parse topic1
ca82a6dff817ec66f44342007202690a93763949
```

### 7.1.4 引用日志

Git后台保持一个引用日志，记录HEAD和分支指向历史。

使用`git reflog`查看引用日志，后方加`@{n}`显示前5次的提交，`{n}`为数字。`{yesterday}`为显示昨天的提交。

当HEAD的位置变化时，Git就会记录。

```
$ git show HEAD@{5}
$ git show master@{yesterday}
```

_引用日志记录的仅仅是本地操作的内容_

### 7.1.5 祖先引用

祖先引用是另一种指明一个提到方式。

使用`git show HEAD~`查看上一次提交。`HEAD`可以换为第[n]提交的SHA-1码。

当使用SHA-1码加[数字]时，使用`git show [d921970]^[2]`

在`~`后添加数字，代表第[n]次前的提交。`$ git show HEAD~[3]`

### 7.1.6 提交区间

- 双点

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/double-dot.png)

想要查看 experiment分支中有哪些提交没有合并到master分支中。

可以使用`master..experiment`。

**例子**

```
$ git log master..experiment
D
C
```

想要查看远端分支

```
git log origin/master..HEAD
```

- 多点

查看`refA`或`refB`，但不被`refC`包含。下方的两个命令效果相同。

```
$ git log refA refB ^refC  
$ git log refA refB --not refC
```

- 三点

查看被两者中的一个包含，但不是两者同时包含。

```
$ git log master...experiment
F
E
D
C
```

使用`$ git log --left-right master...experiment`能显示提交处于哪一侧分支。

```
$ git log --left-right master...experiment
< F
< E
> D
> C
```

--------------------------------------------------------------------------------

## 7.2 交互式暂存

运行`git add`时，使用`-i`或者`--interactive`,进入交互式终端模式。

```
$ git add -i
           staged     unstaged path
  1:    unchanged        +0/-1 TODO
  2:    unchanged        +1/-1 index.html
  3:    unchanged        +5/-1 lib/simplegit.rb

*** Commands ***
  1: status     2: update      3: revert     4: add untracked
  5: patch      6: diff        7: quit       8: help
What now>
```

### 7.2.1 暂存与取消暂存文件

在 `What now>` 键入`2`或`u`，脚本将提升想要暂存那个文件。

```
What now> 2
           staged     unstaged path
  1:    unchanged        +0/-1 TODO
  2:    unchanged        +1/-1 index.html
  3:    unchanged        +5/-1 lib/simplegit.rb
Update>>
```

要暂存TODO与index.html文件，可以输入数字：

```
Update>> 1,2
           staged     unstaged path
* 1:    unchanged        +0/-1 TODO
* 2:    unchanged        +1/-1 index.html
  3:    unchanged        +5/-1 lib/simplegit.rb
Update>>
```

有`*`的文件将会被暂存。如果在`update>>`后直接按回车，Git会暂存之前选择的文件：

```
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
```

TODO与index.html文件已经被暂存，simplegit，rb文件还未被暂存。要取消时TODO，使用`3`或`r`：

```
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
```

查看状态，已经取消暂存TODO文件：

```
*** Commands ***
  1: status     2: update      3: revert     4: add untracked
  5: patch      6: diff        7: quit       8: help
What now> 1
           staged     unstaged path
  1:    unchanged        +0/-1 TODO
  2:        +1/-1      nothing index.html
  3:    unchanged        +5/-1 lib/simplegit.rb
```

想要查看暂存区内容的区别，可以使用`6`或`d`。会显示暂存列表，可以从中选择想要查看的文件，与`git diff --cached`非常相似。

```
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
```

### 7.2.2 暂存补丁

git可以暂存文件的特定部分。simplegit.rb 文件中做了两处修改，但只想要暂存其中的一个而不是另一个。

在交互模式下，输入`5`或`p`，git会询问要暂存那些文件，然后，对已选择文件的每一个部分，它都会一个个地显示文件区别并询问你是否想要暂存它们：

```
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
```

这时有很多选项。 输入 ? 显示所有可以使用的命令列表：

```
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
```

通常情况下可以输入 `y` 或 `n` 来选择是否要暂存每一个区块，当然，暂存特定文件中的所有部分或为之后的选择跳过一个区块也是非常有用的。 如果你只暂存文件的一部分，状态输出可能会像下面这样：

```
What now> 1
           staged     unstaged path
  1:    unchanged        +0/-1 TODO
  2:        +1/-1      nothing index.html
  3:        +1/-1        +4/-0 lib/simplegit.rb
```

最后执行`git commit`保存修改。

使用`git add -p`或`git add --patch`也能启动脚本。

_测试时，似乎是有 bug_

--------------------------------------------------------------------------------

## 7.3 储藏与清理

储蓄当前工作，而非提交，方便随时回来。

使用`git stash`命令。

### 7.3.1 储藏工作

当想要切换分支，但是还不想提交之前的改动，运行`git stash` 或 `git stash save`，可以看到改动：

```
$ git stash
Saved working directory and index state \
  "WIP on master: 049d078 added the index file"
HEAD is now at 049d078 added the index file
(To restore them type "git stash apply")
```

在运行`git status`时可以看到工作目录是干净的，这时可以切换分支了。

要查看储存的东西，可以使用`git stach list`:

```
$ git stash list
stash@{0}: WIP on master: 049d078 added the index file
stash@{1}: WIP on master: c264051 Revert "added file_size"
stash@{2}: WIP on master: 21d80a5 added number to log
```

当想要重新工作时，运行`git stach apply` 或者 `git stash apply stash@{2}`运行之前的储存。如果不指定`stash@{2}`,{2}可以为任意存储编号，git默认指定为最新的储存。

当储存的文件被修改，但是因为储存(stash)前没有暂存(add)，想要重新暂存（add）。要使用`--index`选项运行`git stash apply`命令。

**注意：需要被更改过的文件暂存过（add），使用`$ git stash apply --index`将储存变为修改过的样子，并提交到暂存(add)**

```
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
```

使用`git stash drop stash@{0}`删除储存，可以指定储存位置。

使用`git stash pop`回到文件储存时的状态(最新的储存)，并从储存中删除。

### 7.3.2 创造性的储藏

- `$ git stash --keep-index`

  说明：不储存通过`git add`已经暂存的东西。

  ```
    $ git status -s
    M  index.html
     M lib/simplegit.rb

    $ git stash --keep-index
    Saved working directory and index state WIP on master: 1b65b17 added the index file
    HEAD is now at 1b65b17 added the index file

    $ git status -s
    M  index.html
  ```

- `$ git stash -u`与`$ git stash --include-untracked`

  说明：储存全部没有添加过的文件。`git add`过的文件。基本上是储存全部未提交的文件。

  ```
    $ git status -s
    M  index.html
     M lib/simplegit.rb
    ?? new-file.txt

    $ git stash -u
    Saved working directory and index state WIP on master: 1b65b17 added the index file
    HEAD is now at 1b65b17 added the index file
  ```

- `$ git stash --patch`

  说明：不储藏所有修改过的任何文件，但是会交互式地提示哪些改动想要储藏、哪些改动需要保存在工作目录中。

### 7.3.3 从储藏创建一个分支

将储存新建为一个分支。当修改储存的文件，在读取储存时，会出现错误。这时可以使用`$ git stash branch [新分支名]`，将储存的内容放到新分支上，随后自动删除储存。

**例子：**

```
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
```

### 7.3.4 清理工作目录

`git clean` 用来移除未被跟踪的文件

另一个更安全的方法使用`git stash --all`移除全部，并存储起来。

使用`git clean -f -d`移除工作目录中的未跟踪文件以及空的子目录。`-f`是强制的意思。

使用`$ git clean -d -n`,将说明要移除什么。

```
$ git clean -d -n
Would remove test.o
Would remove tmp/
```

默认`git clean`只移除没有忽略的未跟踪文件。在`.gitiignore`文件设置的被忽略文件，不会被删除。

`$ git clean -n -d -x`移除文件包括忽略的文件。

`-i`以交换模式运行`$ git clean`。

--------------------------------------------------------------------------------

## 7.4 签署工作

检查文件是否来源可信

### 7.4.1 GPG 介绍

开始签名之前，需要先配置GPG并安装个人密钥。

如果没有就新创建一个

`$ gpg --gen-key` 需要输入名字，电子邮件，注释，其他信息可以选择默认。

使用`$ gpg --list-keys`来查生成的密钥。

```
$ gpg --list-keys
/c/Users/k/.gnupg/pubring.gpg
-----------------------------
pub   2048R/DCBD0501 2016-06-13
uid                  username (comment) <gmail@gmail.com>
sub   2048R/AE07F126 2016-06-13
```

当拥有一个可以签署的私钥，通过下方命令来签署。

`git config --global user.signingkey [DCBD0501]` [DCBD0501]为私钥编号。

现在Git使用密钥来签署标签与提交。

使用`$ gpg --armor --export [DCBD0501]`来查询密钥。

从`-----BEGIN PGP PUBLIC KEY BLOCK-----`到 `-----END PGP PUBLIC KEY BLOCK-----`结束。添加到项目标记每个人的提交吧。

### 7.4.2 签署标签

签署命令时，使用`-S` 代替`-a`。

_$ git tag -a -m "[注释]"命令为标签加注释。_

> **例子：**

> ```
>    $ git tag -s v1.5 -m 'my signed 1.5 tag'

>    You need a passphrase to unlock the secret key for
>    user: "Ben Straub <ben@straub.cc>"
>    2048-bit RSA key, ID 800430EB, created 2014-05-04
> ```

运行`$ git show [v1.5]`可以查看到GPG签名。[v1.5]为标签号。

> **例子：**

> ```
>    $ git show v1.5
>    tag v1.5
>    Tagger: Ben Straub <ben@straub.cc>
>    Date:   Sat May 3 20:29:41 2014 -0700

>    my signed 1.5 tag
>    -----BEGIN PGP SIGNATURE-----
>    Version: GnuPG v1

>    iQEcBAABAgAGBQJTZbQlAAoJEF0+sviABDDrZbQH/09PfE51KPVPlanr6q1v4/Ut
>    LQxfojUWiLQdg2ESJItkcuweYg+kc3HCyFejeDIBw9dpXt00rY26p05qrpnG+85b
>    hM1/PswpPLuBSr+oCIDj5GMC2r2iEKsfv2fJbNW8iWAXVLoWZRF8B0MfqX/YTMbm
>    ecorc4iXzQu7tupRihslbNkfvfciMnSDeSvzCpWAHl7h8Wj6hhqePmLm9lAYqnKp
>    8S5B/1SSQuEAjRZgI4IexpZoeKGVDptPHxLLS38fozsyi0QyDyzEgJxcJQVMXxVi
>    RUysgqjcpT8+iQM1PblGfHR4XAhuOqN5Fx06PSaFZhqvWFezJ28/CLyX5q+oIVk=
>    =EFTF
>    -----END PGP SIGNATURE-----

>    commit ca82a6dff817ec66f44342007202690a93763949
>    Author: Scott Chacon <schacon@gee-mail.com>
>    Date:   Mon Mar 17 21:52:11 2008 -0700

>        changed the version number
> ```

### 7.4.3 验证标签

使用`git tag -v [标签名]`验证使用GPG的签名。

> **例子：**

> ```
>    $ git tag -v v1.4.2.1
>    object 883653babd8ee7ea23e6a5c392bb739348b1eb61
>    type commit
>    tag v1.4.2.1
>    tagger Junio C Hamano <junkio@cox.net> 1158138501 -0700

>    GIT 1.4.2.1

>    Minor fixes since 1.4.2, including git-mv and git-http with alternates.
>    gpg: Signature made Wed Sep 13 02:08:25 2006 PDT using DSA key ID F3119B9A
>    gpg: Good signature from "Junio C Hamano <junkio@cox.net>"
>    gpg:                 aka "[jpeg image of size 1513]"
>    Primary key fingerprint: 3565 2A26 2040 E066 C9A7  4A7D C0C6 D9A4 F311 9B9A
> ```

如果没有署名者的公钥，你会得到报错。

> ```
>    gpg: Signature made Wed Sep 13 02:08:25 2006 PDT using DSA key ID F3119B9A
>    gpg: Can't check signature: public key not found
>    error: could not verify the tag 'v1.4.2.1'
> ```

### 7.4.4 签署提交

在`$ git commit -m "注释"`时加上`-S`，使用带签名的提交。

> **例子：**

> ```
>    $ git commit -a -S -m 'signed commit'

>    You need a passphrase to unlock the secret key for
>    user: "Scott Chacon (Git signing key) <schacon@gmail.com>"
>    2048-bit RSA key, ID 0A46826A, created 2014-06-04

>    [master 5c3386c] signed commit
>     4 files changed, 4 insertions(+), 24 deletions(-)
>      rewrite Rakefile (100%)
>     create mode 100644 lib/git.rb
> ```

使用`$ git log`时，加上`--show-signature`来查看签名。

> **例子：**

> ```
>    $ git log --show-signature -1
>    commit 5c3386cf54bba0a33a32da706aa52bc0155503c2
>    gpg: Signature made Wed Jun  4 19:49:17 2014 PDT using RSA key ID 0A46826A
>    gpg: Good signature from "Scott Chacon (Git signing key) <schacon@gmail.com>"
>    Author: Scott Chacon <schacon@gmail.com>
>    Date:   Wed Jun 4 19:49:17 2014 -0700

>         signed commit
> ```

也可以配置`git log`时，加入`%G？`格式化输出。

> **例子：**

> ```
>    $ git log --pretty="format:%h %G? %aN  %s"

>    5c3386c G Scott Chacon  signed commit
>    ca82a6d N Scott Chacon  changed the version number
>    085bb3b N Scott Chacon  removed unnecessary test code
>    a11bef0 N Scott Chacon  first commit
> ```

在`git merge`与`git pull`中，可以使用`--verify-signatures`来检查并拒绝没有可信GPG的提交。

合并一个未签名提交的分支时，合并不会生效

`$ git merge --verify-signatures non-verify`。

> **例子：**

> ```
>    $ git merge --verify-signatures non-verify
>    fatal: Commit ab06180 does not have a GPG signature.
> ```

如果合并的分支，有有效的GPG签名时，合并命令会提示GPG签名已经检查过了，然后继续。

> **例子：**

> ```
>    $ git merge --verify-signatures signed-branch
>    Commit 13ad65e has a good GPG signature by Scott Chacon (Git signing key) <schacon@gmail.com>
>    Updating 5c3386c..13ad65e
>    Fast-forward
>     README | 2 ++
>     1 file changed, 2 insertions(+)
> ```

还可以在`git merge`命令后加`-S`来签署合并。

> **例子：**

> ```
>    $ git merge --verify-signatures -S  signed-branch
>    Commit 13ad65e has a good GPG signature by Scott Chacon (Git signing key) <schacon@gmail.com>

>    You need a passphrase to unlock the secret key for
>    user: "Scott Chacon (Git signing key) <schacon@gmail.com>"
>    2048-bit RSA key, ID 0A46826A, created 2014-06-04

>    Merge made by the 'recursive' strategy.
>     README | 2 ++
>     1 file changed, 2 insertions(+)
> ```

### 7.4.5 每个人必须签署

确定要使用签名时，团队的每个人都要建立GPG签名，不然会很麻烦。

--------------------------------------------------------------------------------

## 7.5 搜索

介绍用于查找函数或定义的两种工具。

### 7.5.1 Git Grep

使用`git grep -n [关键词]`搜索目录中的关键词，并显示行号。`-n`表示输出行号。

> **例子：**

> ```
>    $ git grep -n git
>    EADME.md:1:# github使用测试
>    README.md:6:- [ ] git 文档笔记 进行中
>    README.md:7:- [ ] git markdown 风格制作中
>    README.md:9:- [ ] 如何将github日记用，研究中
>    git/git1_2.md:1:# git使用说明
>    git/git1_2.md:3:[git官方文档](https://git-scm.com/book/en/v2)
>    git/git1_2.md:5:git GUI乱码修正
> ```

如果使用`$ git grep --count [关键词]`仅显示那些文件包含关键词，每个文件有多少个匹配。

**例子：**

> ```
>    $ git grep --count git
>    README.md:4
>    git/git1_2.md:116
>    git/git3.md:119
>    git/git4.md:81
>    git/git5_6.md:107
>    git/git7.md:120
>    git/git8_9.md:1
>    git/github_more_key.md:12
>    ubuntu.md:3
> ```

如果想看匹配的行是属于哪一个函数，使用`git grep -p [gmtime_r] [*.c]`[gmtime_r]为关键词，[*.c]为所属文件。

多个词搜索使用`$ git grep -e ['#define'] --and -e [SORT_DIRENT]`,['#define'] 与[SORT_DIRENT]为搜索词。

### 7.5.2 Git 日志搜索

想要知道什么时候，添加或者引入，使用`git log -S[关键词] --oneline`

> **例子：**

> ```
>    $ git log -Sbranch --oneline
>    01dc744 GPG test
>    f2bddb1 7.3阅读完成
>    59d652f 7.3.1
> ```

- 行日志搜索

`$ git log -L :[关键词]:[文件名]`，展示代码中的一行或者一个函数的历史。

关于命令的表达方法：下方的两种方法完全一样。

```
$ git log -L :git_deflate_bound:zlib.c

$ git log -L '/unsigned long git_deflate_bound/',/^}/:zlib.c
```

--------------------------------------------------------------------------------

## 7.6 重写历史

修改提交历史如愿的展示。

### 7.6.1 修改最后一次提交

通常进行的是修改提交信息，或者修改你添加，修改和移除文件的暂存(add)

`$ git commit --amend`修改最后一次的提交信息。进入文本编辑器修改。

对于忘记添加文件，或移除文件，先运行`git add`或`git rm`，在运行`git commit --amend`。

### 7.6.2 修改多个提交信息

使用命令`$ git rebase -i [HEAD~3]`[HEAD~3]修改前多少次的提交。<br>
之后在文本模式下输入要修改的注释，顺序为倒叙。<br>
_注意：前多少次到最新的注释全部会显示。_

随后确定要的修改提交注释，将要修改的注释前，`pick` 改为`edit`，

> **例如：**

> ```
>    edit f7f3f6d changed my name a bit
>    pick 310154e updated README formatting and added blame
>    pick a5f4a0d added cat-file
> ```

在退出编辑文档。

输入`git commit --amend` 修改刚刚确定需改的提交注释。

最后 `$ git rebase --continue` 执行变基。

如果修改失败了可以使用`rm -fr "[项目目录下]/.git/rebase-merge"`清除失败记录。

### 7.6.3 重新排序提交

使用 `$ git rebase -i [HEAD^2]` [HEAD^2]为后退步数。

```
pick f7f3f6d changed my name a bit
pick 310154e updated README formatting and added blame
pick a5f4a0d added cat-file
```

将要删除行直接删除，如下。

```
pick 310154e updated README formatting and added blame
pick f7f3f6d changed my name a bit
```

### 7.6.4 压缩提交

将多个提交压缩为一个提交。

使用 `$ git rebase -i [HEAD^2]` [HEAD^2]为后退步数。

在文本文档中将`pick`改为`squash`。

说明：如果要将三个提价压缩为一个，见下面的例子。对于多条提交同样适用。`pick`为最后的提交。

```
pick f7f3f6d changed my name a bit
squash 310154e updated README formatting and added blame
squash a5f4a0d added cat-file
```

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

- 从每一个提交移除一个文件

  从整个提交历史中移除[文件]，命令如下：

  `$ git filter-branch --tree-filter 'rm -f [文件名]' HEAD`

  `--tree-filter`在检出项目的，每一个提交后，运行指定的命令，然后重新提交结果。

  为了`filter-branch`在所有分支上运行，加`--all`

- 使一个子目录做为新的根目录

  使用`$ git filter-branch --subdirectory-filter trunk HEAD`

  Git会自动移除所有不影响子目录的提交。

- 全局修改邮箱地址

  忘记设置名字与邮箱地址，或者修改邮箱。

  ```
    $ git filter-branch --commit-filter '
        if [ "$GIT_AUTHOR_EMAIL" = "schacon@localhost" ];
        then
                GIT_AUTHOR_NAME="Scott Chacon";
                GIT_AUTHOR_EMAIL="schacon@example.com";
                git commit-tree "$@";
        else
                git commit-tree "$@";
        fi' HEAD
  ```

  命令会修改每一个提交SHA-1校验。

--------------------------------------------------------------------------------

## 7.7 重置揭密

介绍`reset`与`checkout`

### 7.7.1 三棵树

树                 | 用途
:---------------- | :--------------
HEAD              | 上次提交的快照，下次提交的起点
Index             | 预提交的快照(`add`命令)
Working Directory | 沙盒

_注意："树"指的是"文件的集合"_

- HEAD

  HEAD是当前分支的指针，总是指向分支上最后一次提交。

  `$ git cat-file -p HEAD`查看实际的目录列表。

  `$ git ls-tree -r HEAD`当前全部文件的SHA-1校验

_注意：`cat-file`与`ls-tree`是底层命令。_

- 索引

  索引，将文件提交到暂存区。

- 工作目录

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

用图的方式说明`reset`的意义。下方例子继承上边的。说明`$ git reset HEAD~`的实际运作过程。 开始第三次修改并保存。

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/reset-start.png)

- 第 1 步：移动 HEAD

  `reset`先移动HEAD指向的分支，使用`reset --soft`分支仅仅停在上一个位置。

  ![](https://git-scm.com/book/en/v2/book/07-git-tools/images/reset-soft.png)

  使用命令`$ git reset HEAD~`,之后再使用`git commit`。效果与使用`git commit --amend`一致。

- 第 2 步：更新索引（--mixed）

  现在使用`git status`查看状态，可以看到HEAD与索引目录的区别，之后使用`reset`将快照[提交]指向HEAD的新位置。

  ![](https://git-scm.com/book/en/v2/book/07-git-tools/images/reset-mixed.png)

  如果增加`--mixed`选择，`reset`会停下来。命令为

  ```
    $ git reset --mixed HEAD~
  ```

  图上做了什么：撤销上一次提交，同时取消暂存。

- 第 3 步：更新工作目录（--hard）

  最后`reset`让文件变回上次提交。

  ![](https://git-scm.com/book/en/v2/book/07-git-tools/images/reset-hard.png)

  _注意：`--hard`选项不能撤销。_

- 回顾

  `reset`会以特定的顺序重写三棵树，可以使用特定参数使选项停止。

  1. 移动HEAD分支的指向。使用命令`$ git reset --soft HEAD~`
  2. 使索引看起来像HEAD。使用命令`$ git reset --mixed HEAD~`
  3. 使文件看起来像索引。使用命令`$ git reset --hard HEAD~`

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

- 不带路径

  `git checkout [branch]`与`git reset --hard [branch]`非常相似

  **二者的不同点**

  1. `git checkout`对工作目录是安全的，`checkout`会先试着合并一下。而`reset`会直接替换所有的东西。

  2. 更新HEAD的方式不同，`reset`会移动HEAD分支的指向，而`checkout`只会移动HEAD本身到另一分支。

    **例子：**

    假设有`master`和`develop`分支，它们分别指向不同提交，现在HEAD指向`develop`。

    运行`git reset master`,那么`develop`会和`master`指向同一个提交。

    如果运行的是`git checkout master`，那么`develop`不会移动，HEAD自身会移动。

    下图解释的非常清楚。

    ![](https://git-scm.com/book/en/v2/book/07-git-tools/images/reset-checkout.png)

- 带路径

  运行`checkout`的另一种方式是指定文件路径。

  使用的效果与`$ git reset [branch] file`一致。

--------------------------------------------------------------------------------

## 7.8 高级合并

用来说明合并过程中可能遇到的问题，以及问题的解决方法。

### 7.8.1 合并冲突

在合并分支前，先清理工作目录，确保没有尚未储存的文件。如果有先保存到一个临时的分支，或者提交到当前分支。

- 中断一次合并

  `git merge --abort`

  `git merge --abort`选项会尝试恢复到合并前状态。

  _注意：当目录中有没有储存，没提交的文件时，命令不能工作_

- 忽略空白

  当更改制表符时，常见的错误。Unix换到DOS时，等等类似情况。

  忽略文件中的空白，而后合并。

  1. `-Xignore-all-space`忽略任意数量的已有空白的修改
  2. `-Xignore-space-change`忽略所有空白修改

    **例子**

    ```
    $ git merge -Xignore-space-change whitespace
    Auto-merging hello.rb
    Merge made by the 'recursive' strategy.
    hello.rb | 2 +-
    1 file changed, 1 insertion(+), 1 deletion(-)
    ```

- 手动文件再合并

  先将冲突文件提取为备份，使用`stages`控制提取是的文本。

  |stages|说明| |:----:|:----| |stages 1|文件的共同祖先版本| |stages 2|你的版本| |stages 3|来自MERGE_HEAD的版本，将要合并的版本|

  **例子：**

  ```
    $ git show :1:hello.rb > hello.common.rb
    $ git show :2:hello.rb > hello.ours.rb
    $ git show :3:hello.rb > hello.theirs.rb
  ```

  也可以使用`ls-files -u`来得到文件的SHA-1值。

  **效果如下**

  ```
    $ git ls-files -u
    100755 ac51efdc3df4f4fd328d1a02ad05331d8e2c9111 1    hello.rb
    100755 36c06c8752c78d2aff89571132f3bf7841a7b5c3 2    hello.rb
    100755 e85207e04dfdd5eb0a1e9febbc67fd837c44a1cd 3    hello.rb
  ```

```
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
```

- 检出冲突

  **例子：**

  ```
    $ git log --graph --oneline --decorate --all
    * f1270f7 (HEAD, master) update README
    * 9af9d3b add a README
    * 694971d update phrase to hola world
    | * e3eb223 (mundo) add more tests
    | * 7cff591 add testing script
    | * c3ffff1 changed text to hello mundo
    |/
    * b7dcc89 initial hello world code
  ```

  现在有只在 master 分支上的三次单独提交，还有其他三次提交在 mundo 分支上。 如果我们尝试将 mundo 分支合并入 master 分支，我们得到一个冲突。

  ```
    $ git merge mundo
    Auto-merging hello.rb
    CONFLICT (content): Merge conflict in hello.rb
    Automatic merge failed; fix conflicts and then commit the result.
  ```

  关于冲突的显示方法`$ git checkout --conflict [文件]`，检出文件并替换合并冲突标记。

  `-- conflict`参数有`diff3`或`merge`。使用`diff3`时，增加`base`版本

  **例子：**

  ```
    $ git checkout --conflict=diff3 hello.rb
  ```

  返回结果如下：

  ```
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
  ```

如果喜欢这种格式可以使用`merge.conflictstyle`设置`diff3`为默认选项。

```
$ git config --global merge.conflictstyle diff3
```

还可以使用`--ours`和`--theirs`,选择留下一边，删除另一边。

- 合并日志

  使用`git log`解决合并冲突。

  **例子：**

  ```
    $ git log --oneline --left-right HEAD...MERGE_HEAD
    < f1270f7 update README
    < 9af9d3b add a README
    < 694971d update phrase to hola world
    > e3eb223 add more tests
    > 7cff591 add testing script
    > c3ffff1 changed text to hello mundo
  ```

  使用`--merge`选项，会只显示任何一个触发合并冲突的提交。

  ```
    $ git log --oneline --left-right --merge
    < 694971d update phrase to hola world
    > c3ffff1 changed text to hello mundo
  ```

  如果在运行命令是加`-p`选项，会看到所有冲突文件的区别，很有用。

- 组合式差异格式

  当合并冲突时，直接运行`git diff`会显示一个独特的输出格式。这种格式叫作`组合式差异`

  **例子：**

  ```
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
  ```

  可以在后，使用`git log`来获取相同信息，增加`-p`与`--cc`参数。

  **例子：**

  ```
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
  ```

### 7.8.2 撤消合并

假设现在在一个特性分支上工作，不小心将其合并到 master 中，现在提交历史看起来是这样

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/undomerge-start.png)

有两种方法用来撤销合并`修复引用`与`还原提交`

- 修复引用

  运行`git reset --hard HEAD~`，要在没有提交之前，提交到服务器后，在执行会带来不必要的麻烦。

  它会重新提交历史，提交后不要使用，提交后不要使用，提交后不要使用。

  **在 git reset --hard HEAD~ 之后的历史**

  ![](https://git-scm.com/book/en/v2/book/07-git-tools/images/undomerge-reset.png)

- 还原提交

  还原上一次的提交`$ git revert -m [1] HEAD`[1]为还原的步数。

  使用命令后效果如下图。要撤销C4的合并修改，同时保存C6的内容。使新生成的^m内容完全与C6一致。

  ![](https://git-scm.com/book/en/v2/book/07-git-tools/images/undomerge-revert.png)

  这时如果将`topic`合并到`master`中，git会报错。因为已经合并过到M了。

  如果再次在`topic`分支进行修改，随后合并到`master`，那么C3，C4就没有合并到分支上，从而出现错误。

  说明图如下，

  ![](https://git-scm.com/book/en/v2/book/07-git-tools/images/undomerge-revert2.png)

  这时可以撤销到M，更新到^^M,分支，M与^^M的内容相同，随后合并修改后的C7。

  ```
    $ git revert HEAD^   //撤销两次。
  ```

  ![](https://git-scm.com/book/en/v2/book/07-git-tools/images/undomerge-revert3.png)

  这样合并的结果就包含了，`topic`的修改。

### 7.8.3 其他类型的合并

- 我们的或他们的偏好

  默认情况下，git在合并产生冲突时，标记错误，并要求用户手动修改。

  当在合并时使用`-Xours`或`-Xtheirs`，可以自动的忽略一个而选择另一个。

  `-Xours`选择保存之前的提交。

  `-Xtheirs`选择保存之后的提交。

  **例子：**

  ```
    $ git merge -Xours mundo
  ```

- 子树合并

  当有两个项目的时候，其中一个映射到另一个子目录，合并时执行一个子树合并，git通常会自动计算之后正确合并。

  先拉取另一分支到目录，切入本地分支与拉取得分支，会发现两者的目录不同。

  假设本地分支为master，拉取的分支为rack_branch。

  先切回master，随后执行`$ git read-tree --prefix=rack/ -u rack_branck`在master的目录下建立rack文件夹，并包含rack的文件。

  当rack项目更新是，依然可以切到rack_brank分支进行拉取更新。

  将rack拉取的更新合并到master，使用命令：

  ```
    $ git checkout master
    $ git merge --squash -s recursive -Xsubtree=rack rack_branch
  ```

  _注意：_

  当要查看rack子目录与rack_branch分支的差异时，使用`$ git diff-tree`而不是`$ git diff`。

  ```
    $ git diff-tree -p rack_branch
  ```

  或者，将子目录与最后一次抓取的master分支进行比较。

  ```
    $ git diff-tree -p rack_remote/master
  ```

--------------------------------------------------------------------------------

## 7.9 Rerere

说明：它允许你让Git记住解决一个冲突的方法，下一次有相同冲突时，Git可以为你自动解决。

`$ git rerere`是一个隐藏的功能。

开启功能是，需要使用命令设置：

```
$ git config --global rerere.enabled ture
```

也可以通过 **创建.git/rr-cache目录**来开启功能，但是使用起来没有命令方便，命令是全局控制的。

**例子:**

当分支中的文件产生可能的冲突时。

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/rerere1.png)

当合并两个分支到一起时，将得到一个合并冲突

```
$ git merge i18n-world
Auto-merging hello.rb
CONFLICT (content): Merge conflict in hello.rb
Recorded preimage for 'hello.rb'
Automatic merge failed; fix conflicts and then commit the result.
```

_注意：Recorded preimage for 'hello.rb'_ 其中的'hello.rb'可能是任意的冲突文件。

之后使用`$ git status`不会有任何的冲突提示，但是可以使用`$ git rerere status`查看。

使用`$ git rerere diff`将会显示解决方案的当前状态，即开始解决前与解决后的样子。

**例子：**

```
$ git rerere diff
--- a/hello.rb
+++ b/hello.rb
@@ -1,11 +1,11 @@
 #! /usr/bin/env ruby

 def hello
-<<<<<<<
-  puts 'hello mundo'
-=======
+<<<<<<< HEAD
   puts 'hola world'
->>>>>>>
+=======
+  puts 'hello mundo'
+>>>>>>> i18n-world
 end
```

还可以使用`$ git ls-files -u`查看冲突文件之前，左右两边的情况。

**例子：**

```
$ git ls-files -u
100644 39804c942a9c1f2c03dc7c5ebcd7f3e3a6b97519 1    hello.rb
100644 a440db6e8d1fd76ad438a49025a9ad9ce746f581 2    hello.rb
100644 54336ba847c3758ab604876419607e9443848474 3    hello.rb
```

当合并的结果并不满意时，就需要手动修改。之后执行`$ git add [文件]`与`$ git commit`

没有手动修改的结果：

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/rerere2.png)

现在撤销合并，将它变基到master分支。使用命令`$ git reset --hard HEAD^`回到上次的状态。

随后切到`i18-world`分支，执行变基。

```
$ git checkout i18n-world
Switched to branch 'i18n-world'

$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: i18n one word
Using index info to reconstruct a base tree...
Falling back to patching base and 3-way merge...
Auto-merging hello.rb
CONFLICT (content): Merge conflict in hello.rb
Resolved 'hello.rb' using previous resolution.
Failed to merge in the changes.
Patch failed at 0001 i18n one word
```

当看到`Resolved 'hello.rb' using previous resolution.`这行时，说明合并冲突已经自动解决了。

再次查看文件时，可以看到里面没有合并冲突的标记。使用`$ cat [文件]`查看

使用`$ git diff`也可以看出它是如何自动解决的。

```
$ git diff
diff --cc hello.rb
index a440db6,54336ba..0000000
--- a/hello.rb
+++ b/hello.rb
@@@ -1,7 -1,7 +1,7 @@@
  #! /usr/bin/env ruby

  def hello
-   puts 'hola world'
 -  puts 'hello mundo'
++  puts 'hola mundo'
  end
```

**下方是参考图片**

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/rerere3.png)

还可以通过`checkout`命令重新恢复到冲突的文件状态。

```
$ git checkout --conflict=merge hello.rb
$ cat hello.rb
#! /usr/bin/env ruby

def hello
<<<<<<< ours
  puts 'hola world'
=======
  puts 'hello mundo'
>>>>>>> theirs
end
```

随后运行`$ git rerere`解决。

```
$ git rerere
Resolved 'hello.rb' using previous resolution.
$ cat hello.rb
#! /usr/bin/env ruby

def hello
  puts 'hola mundo'
end
```

通过`rerere`解决文件冲突后，添加并继续变基完成。

```
$ git add hello.rb
$ git rebase --continue
Applying: i18n one word
```

如果做了很多次重新合并，或者想要一个特性分支始终与你的 master 分支保持最新但却不想要一大堆合并，或者经常变基，打开 rerere 功能可以帮助你的生活变得更美好。

--------------------------------------------------------------------------------

## 7.10 使用 Git 调试

用于跟踪bug或者找错误。

### 7.10.1 文件标注

`$ git blame` 标注指定文件，查看每一行的最后修改时间，以及被谁修改的。`-L`指定输出范围。

**例子：**

```
$ git blame -L 12,22 simplegit.rb
^4832fe2 (Scott Chacon  2008-03-15 10:31:28 -0700 12)  def show(tree = 'master')
^4832fe2 (Scott Chacon  2008-03-15 10:31:28 -0700 13)   command("git show #{tree}")
^4832fe2 (Scott Chacon  2008-03-15 10:31:28 -0700 14)  end
^4832fe2 (Scott Chacon  2008-03-15 10:31:28 -0700 15)
9f6560e4 (Scott Chacon  2008-03-17 21:52:20 -0700 16)  def log(tree = 'master')
79eaf55d (Scott Chacon  2008-04-06 10:15:08 -0700 17)   command("git log #{tree}")
9f6560e4 (Scott Chacon  2008-03-17 21:52:20 -0700 18)  end
9f6560e4 (Scott Chacon  2008-03-17 21:52:20 -0700 19)
42cf2861 (Magnus Chacon 2008-04-13 10:45:01 -0700 20)  def blame(path)
42cf2861 (Magnus Chacon 2008-04-13 10:45:01 -0700 21)   command("git blame #{path}")
42cf2861 (Magnus Chacon 2008-04-13 10:45:01 -0700 22)  end
```

例子说明：第一项为SHA-1值，第二项是用户名称，第三项是修改时间，最后是文件行的内容。

_注意：^4832fe2为第一次提交的行_

`-C`选项，查看代码来源。

**例子：**

将GITServerHandler.m文件拆分为数个文件，其中一个是GITPackUpload.m

使用`-C`参数，可以看到代码的原始出处。

```
$ git blame -C -L 141,153 GITPackUpload.m
f344f58d GITServerHandler.m (Scott 2009-01-04 141)
f344f58d GITServerHandler.m (Scott 2009-01-04 142) - (void) gatherObjectShasFromC
f344f58d GITServerHandler.m (Scott 2009-01-04 143) {
70befddd GITServerHandler.m (Scott 2009-03-22 144)         //NSLog(@"GATHER COMMI
ad11ac80 GITPackUpload.m    (Scott 2009-03-24 145)
ad11ac80 GITPackUpload.m    (Scott 2009-03-24 146)         NSString *parentSha;
ad11ac80 GITPackUpload.m    (Scott 2009-03-24 147)         GITCommit *commit = [g
ad11ac80 GITPackUpload.m    (Scott 2009-03-24 148)
ad11ac80 GITPackUpload.m    (Scott 2009-03-24 149)         //NSLog(@"GATHER COMMI
ad11ac80 GITPackUpload.m    (Scott 2009-03-24 150)
56ef2caf GITServerHandler.m (Scott 2009-01-05 151)         if(commit) {
56ef2caf GITServerHandler.m (Scott 2009-01-05 152)                 [refDict setOb
56ef2caf GITServerHandler.m (Scott 2009-01-05 153)
```

### 7.10.2 二分查找

用于查找未知文件错误，首先使用`$ git bisect start`来启动，最后执行`$ git bisect bad`来告诉系统，你所在的提交是有问题的，最后使用`$ git bisect good [好的]`

```
$ git bisect start
$ git bisect bad
$ git bisect good v1.0
Bisecting: 6 revisions left to test after this
[ecb6e1bc347ccecc5f9350d878ce677feb13d3b2] error handling on repo
```

Git在你标记为正常的提交和当前的错误版本之间有12次左右的提交，git会检出中的提交。

现在可以执行测试，如果问题还存在，说明问题在前六次的提交，如果没有问题，说明问题在后6次的提交。

如果没有问题，通过`$ git bisect good`来告诉Git，继续寻找。

```
$ git bisect good
Bisecting: 3 revisions left to test after this
[b047b02ea83310a70fd603dc8cd7a6cd13d15c04] secure this thing
```

现在你在另一个提交上，这个提交是刚刚那个测试通过的提交和有问题提交的中点。再一次执行测试，发现这个提交是有问题的，可以通过`$ git bisect bad`告诉Git

```
$ git bisect bad
Bisecting: 1 revisions left to test after this
[f71ce38690acf49c1f3c9bea38e09d82a5ce6014] drop exceptions table
```

现在git拥有的信息可以确定引入问题的位置，它会告诉你第一个提交的SHA-1 值并显示一些提交说明。方便找出问题的根源。

```
$ git bisect good
b047b02ea83310a70fd603dc8cd7a6cd13d15c04 is first bad commit
commit b047b02ea83310a70fd603dc8cd7a6cd13d15c04
Author: PJ Hyett <pjhyett@example.com>
Date:   Tue Jan 27 14:48:32 2009 -0800

    secure this thing

:040000 040000 40ee3e7821b895e52c1695092db9bdc4c61d1730
f24d3c6ebcfc639b1a3814550e62d60b8e68a8e4 M  config
```

随后使用`$ git bisect reset`重置HEAD到最开始位置。

可以写一个脚本，来处理`git bisect`方法见[文档](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E4%BD%BF%E7%94%A8-Git-%E8%B0%83%E8%AF%95#二分查找)

## 7.11 子模块

当项目中，需要包含并使用另一个项目，也许是第三方库。或者独立开发的，用于多个父项目的库。

现在，想要把他们当做两个独立的项目，同时又想在一个项目中使用另一个。

**例子：**

假设你正在开发一个网站然后创建了 Atom 订阅。 你决定使用一个库，而不是写自己的 Atom 生成代码。 你可能不得不通过 CPAN 安装或 Ruby gem 来包含共享库中的代码，或者将源代码直接拷贝到自己的项目中。 如果将这个库包含进来，那么无论用何种方式都很难定制它，部署则更加困难，因为你必须确保每一个客户端都包含该库。 如果将代码复制到自己的项目中，那么你做的任何自定义修改都会使合并上游的改动变得困难。

Git通过子模块来解决这个问题，子模块允许你将一个Git仓库作为另一个Git仓库的子目录，它能让你将另一个仓库克隆到自己的项目中，同时还保持提交的独立。

### 7.11.1 开始使用子模块

**例子：**

我们将要演示如何在一个被分成一个主项目与几个子项目上开发。

我们首先将一个已存在的Git仓库添加为正在工作的仓库的子模块，你可以通过`git submodule add [仓库地址]`来添加子模块。

例子中将添加一个名为`DbConnector`的库

```
$ git submodule add https://github.com/chaconinc/DbConnector
Cloning into 'DbConnector'...
remote: Counting objects: 11, done.
remote: Compressing objects: 100% (10/10), done.
remote: Total 11 (delta 0), reused 11 (delta 0)
Unpacking objects: 100% (11/11), done.
Checking connectivity... done.
```

默认情况下，子模块会将子项目放到一个与仓库同名的目录中，本例中是`DbConnector`,如果想要放到其他地方，那么可以在命令结尾添加一个不同的路径。

这时运行`git status`,可以看到新的文件以及`.gitmodules`文件。

```
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   .gitmodules
    new file:   DbConnector
```

`.gitmodules`文件，用来保存项目URL与已经拉取的本地目录之间的映射。查看一下文件的内容。

```
$ cat .gitmodules
[submodule "DbConnector"]
    path = DbConnector
    url = https://github.com/chaconinc/DbConnector
```

如果有多个子模块，该文件就会有多条记录。

_注意：`.gitmodules`文件同样受版本控制，它会和该项目的其他部分一同被拉取推送。克隆项目的人就知道要去哪找子模块了。_

`$ git status`显示的另外一个输出是文件夹的记录，如果运行`git diff`，会看到类似的下面的信息：

```
$ git diff --cached DbConnector
diff --git a/DbConnector b/DbConnector
new file mode 160000
index 0000000..c3f01dc
--- /dev/null
+++ b/DbConnector
@@ -0,0 +1 @@
+Subproject commit c3f01dc8862123d317dd46284b05b6892c7b29bc
```

虽然`DbConnector`是工作目录的一个子目录，但是Git还是会将它视作一个子模块。当你不在子模块的目录中时，Git不会跟踪它的内容，而是将它看作一个特殊提交。

如果想要更漂亮的输出，可以给`git diff`加`--submodule`选项。

```
$ git diff --cached --submodule
diff --git a/.gitmodules b/.gitmodules
new file mode 100644
index 0000000..71fc376
--- /dev/null
+++ b/.gitmodules
@@ -0,0 +1,3 @@
+[submodule "DbConnector"]
+       path = DbConnector
+       url = https://github.com/chaconinc/DbConnector
Submodule DbConnector 0000000...c3f01dc (new submodule)
```

当你提交时，会看到下方的信息

```
$ git commit -am 'added DbConnector module'
[master fb9093c] added DbConnector module
 2 files changed, 4 insertions(+)
 create mode 100644 .gitmodules
 create mode 160000 DbConnector
```

_注意：`DbConnector`记录的160000模式，这是一种特殊模式，它意味着你将一次提交作为一项目录记录，而非将它记录成一个子目录或者一个文件。_

### 7.11.2 克隆含有子模块的项目

当克隆一个含有子模块的项目时，默认会包含子模块目录，但其中还没有任何文件。

**例子:**

```
$ git clone https://github.com/chaconinc/MainProject
Cloning into 'MainProject'...
remote: Counting objects: 14, done.
remote: Compressing objects: 100% (13/13), done.
remote: Total 14 (delta 1), reused 13 (delta 0)
Unpacking objects: 100% (14/14), done.
Checking connectivity... done.
$ cd MainProject
$ ls -la
total 16
drwxr-xr-x   9 schacon  staff  306 Sep 17 15:21 .
drwxr-xr-x   7 schacon  staff  238 Sep 17 15:21 ..
drwxr-xr-x  13 schacon  staff  442 Sep 17 15:21 .git
-rw-r--r--   1 schacon  staff   92 Sep 17 15:21 .gitmodules
drwxr-xr-x   2 schacon  staff   68 Sep 17 15:21 DbConnector
-rw-r--r--   1 schacon  staff  756 Sep 17 15:21 Makefile
drwxr-xr-x   3 schacon  staff  102 Sep 17 15:21 includes
drwxr-xr-x   4 schacon  staff  136 Sep 17 15:21 scripts
drwxr-xr-x   4 schacon  staff  136 Sep 17 15:21 src
$ cd DbConnector/
$ ls
$
```

其中有`DbConnector`目录，但是空的。必须运行两个命令：`$ git submodule init`用来初始化本地配置文件。而`$ git submodule update`则从该项目中抓取所有数据，并检出父项目中列出的合适的提交。

```
$ git submodule init
Submodule 'DbConnector' (https://github.com/chaconinc/DbConnector) registered for path 'DbConnector'
$ git submodule update
Cloning into 'DbConnector'...
remote: Counting objects: 11, done.
remote: Compressing objects: 100% (10/10), done.
remote: Total 11 (delta 0), reused 11 (delta 0)
Unpacking objects: 100% (11/11), done.
Checking connectivity... done.
Submodule path 'DbConnector': checked out 'c3f01dc8862123d317dd46284b05b6892c7b29bc'
```

现在`DbConnector`子目录的状态与提交时相同了。

**快捷方式：**

如果给`$ git clone`命令加`--recursive`选项，它就会自动的初始化并更新仓库中的每一个子模块。

```
$ git clone --recursive [url]    //[url]指链接地址。
```

**例子:**

```
$ git clone --recursive https://github.com/chaconinc/MainProject
Cloning into 'MainProject'...
remote: Counting objects: 14, done.
remote: Compressing objects: 100% (13/13), done.
remote: Total 14 (delta 1), reused 13 (delta 0)
Unpacking objects: 100% (14/14), done.
Checking connectivity... done.
Submodule 'DbConnector' (https://github.com/chaconinc/DbConnector) registered for path 'DbConnector'
Cloning into 'DbConnector'...
remote: Counting objects: 11, done.
remote: Compressing objects: 100% (10/10), done.
remote: Total 11 (delta 0), reused 11 (delta 0)
Unpacking objects: 100% (11/11), done.
Checking connectivity... done.
Submodule path 'DbConnector': checked out 'c3f01dc8862123d317dd46284b05b6892c7b29bc'
```

### 7.11.3 在包含子模块的项目上工作

同时与主项目和子项目上的队员协作。

- 拉取上游修改

  下面的例子中，将使用最简单的模型，只使用子项目并不时地更新，也不对子项目进行任何修改。

  如果要查看子模块中的新工作，可以进入到目录中运行`$ git fetch`与`git merge`,合并上游分支来更新本地代码。

  ```
    $ git fetch
    From https://github.com/chaconinc/DbConnector
       c3f01dc..d0354fc  master     -> origin/master
    $ git merge origin/master
    Updating c3f01dc..d0354fc
    Fast-forward
     scripts/connect.sh | 1 +
     src/db.c           | 1 +
     2 files changed, 2 insertions(+)
  ```

如果现在返回主项目并运行`$ git diff --submodule`,就会看到子模块被更新，同时获得了一个包含新添加提交的列表。如果你不想每次运行`$ git diff`时都输入`--submodule`,那么可以将`diff.submodule`设置为`log`来将其作为默认行为。

**例子：**

```
$ git config --global diff.submodule log
$ git diff
Submodule DbConnector c3f01dc..d0354fc:
  > more efficient db routine
  > better connection routine
```

如果现在提交，那么子模块锁定为其他人更新时的新代码。

如果不想手动抓取与合并，那么还有更容易的方式。运作`$ git submodule update --remote [子模块名]`,Git将会进入子模块然后抓取更新。

```
$ git submodule update --remote DbConnector
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 4 (delta 2), reused 4 (delta 2)
Unpacking objects: 100% (4/4), done.
From https://github.com/chaconinc/DbConnector
   3f19983..d0354fc  master     -> origin/master
Submodule path 'DbConnector': checked out 'd0354fc054692d3906c85c3af05ddce39a1c0644'
```

此命令默认会假定你要更新子模块仓库的`master`分支。也可以设置为其它分支。

例如，你要DbConnector子模块跟踪仓库的`stable`分支，那么可以在`.gitmodules`文件中设置（这种方法使其他人也可以跟踪），也可以在`.git/config`文件中设置。

在`.gitmodules`中设置方法

```
$ git config -f .gitmodules submodule.DbConnector.branch stable
```

之后可以直接使用`$ git submodule update --remote`更新子模块。

如果不用`-f .gitmodules`选择，那么它只会为你修改。（其他人不能跟踪）

这时运行`$ git status`,Git会显示子模块中有新提交。

```
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

  modified:   .gitmodules
  modified:   DbConnector (new commits)

no changes added to commit (use "git add" and/or "git commit -a")
```

如果你设置了配置选项`status.submodulesummary`,Git也会显示子模块的更改摘要：

```
$ git config status.submodulesummary 1

$ git status
On branch master
Your branch is up-to-date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   .gitmodules
    modified:   DbConnector (new commits)

Submodules changed but not updated:

* DbConnector c3f01dc...c87d55d (4):
  > catch non-null terminated lines
```

这时如果运行`$ git diff`,可以看到修改了`.gitmodules`文件，同时还有几个已拉取的提交，需要提交到子模块上。

```
$ git diff
diff --git a/.gitmodules b/.gitmodules
index 6fc0b3d..fd1cc29 100644
--- a/.gitmodules
+++ b/.gitmodules
@@ -1,3 +1,4 @@
 [submodule "DbConnector"]
        path = DbConnector
        url = https://github.com/chaconinc/DbConnector
+       branch = stable
 Submodule DbConnector c3f01dc..c87d55d:
  > catch non-null terminated lines
  > more robust error handling
  > more efficient db routine
  > better connection routine
```

运行`$ git log -p --submodule`查看子模块中的提交日记。

当运行`$ git submodule update --remote`时，Git会尝试更所有子模块，所以如果有很多子模块的话，你可以传递想要更新的子模块的名字。

```
$ git submodule update --remote [子模块]
```

- 在子模块上工作

  当你想在子模块编写代码时，同时可能还想在主项目上编写代码。

  下面的例子将说明如何在子模块与主项目中同时做修改，以及如何同时提交与发布那些修改。

  默认情况下，Git不会记录子模块仓库的修改。

  为使Git能够保存子模块的修改，需要要做两件事儿。

  1. 进入每个子模块并检出其相应的工作分支分，

  2. 如果做了修改，需要告诉Git它该做什么，然后运行`$ git submodule update --remote`来从上游拉取新工作。随后可以合并到本地工作中，也可以尝试将你的工作变基到新的更改上。

    **下方是操作示例：**

    首先，进入子模块然后检出一个分支。

    $ git checkout stable Switched to branch 'stable'

    然后用`merge`选项，为了手动指定它，只需要给`update`添加`--merge`选项即可。这时我们将会看到服务器上的这个子模块有一个改动并且它被合并了进来。

    $ git submodule update --remote --merge remote: Counting objects: 4, done. remote: Compressing objects: 100% (2/2), done. remote: Total 4 (delta 2), reused 4 (delta 2) Unpacking objects: 100% (4/4), done. From <https://github.com/chaconinc/DbConnector>

    ```
    c87d55d..92c7337  stable     -> origin/stable
    ```

    Updating c87d55d..92c7337 Fast-forward src/main.c | 1 + 1 file changed, 1 insertion(+) Submodule path 'DbConnector': merged in '92c7337b30ef9e0893e758dac2459d07362ab5ea'

    如果进入DbConnector目录，可以发现新的改动已经合并入本地`stable`分支。

    现在让我们看看当我们对库做一些本地的改动，而同时其他人推送另外一个修改到上游时会发生什么。

    $ cd DbConnector/ $ vim src/db.c $ git commit -am 'unicode support' [stable f906e16] unicode support 1 file changed, 1 insertion(+)

    如果我们现在更新子模块，就会看到当我们在本地做了更改时上游也有一个改动，我们需要将它并入本地。（并入的是上游改动，而非本地。）

    $ git submodule update --remote --rebase First, rewinding head to replay your work on top of it... Applying: unicode support Submodule path 'DbConnector': rebased into '5d60ef9bbebf5a0c1c1050f242ceeb54ad58da94'

    如果你忘记`--rebase`或`--merge`,Git会将子模块更新为服务器上的状态。并将项目重置为一个游离的HEAD状态。

    真的忘了加`--rebase`或`--merge`也没有问题，重新检出分支，之后手动合并或变基就可以了。

    如果没有提交子模块的改动，那么运行一个子模块更新也不会出现问题，此时Git会只抓取更新并不会覆盖子模块目录中未保存的工作。

    $ git submodule update --remote remote: Counting objects: 4, done. remote: Compressing objects: 100% (3/3), done. remote: Total 4 (delta 0), reused 4 (delta 0) Unpacking objects: 100% (4/4), done. From <https://github.com/chaconinc/DbConnector>

    ```
    5d60ef9..c75e92a  stable     -> origin/stable
    ```

    error: Your local changes to the following files would be overwritten by checkout:

    ```
    scripts/setup.sh
    ```

    Please, commit your changes or stash them before you can switch branches. Aborting Unable to checkout 'c75e92a2b3855c9e5b66f915308390d9db204aca' in submodule path 'DbConnector'

    如果做了一些与上游改动冲突的改动，当运行更新时Git会让你知道。

    $ git submodule update --remote --merge Auto-merging scripts/setup.sh CONFLICT (content): Merge conflict in scripts/setup.sh Recorded preimage for 'scripts/setup.sh' Automatic merge failed; fix conflicts and then commit the result. Unable to merge 'c75e92a2b3855c9e5b66f915308390d9db204aca' in submodule path 'DbConnector'

- 发布子模块改动

  现在我们的子模块目录中有一些改动。其中一些是我们通过更新从上游引入的（服务器拉取的），而另一些是本地生成的，由于我们还没有推送它们，对任何其他人都不可用。

  **例子:**

  ```
    $ git diff
    Submodule DbConnector c87d55d..82d2ad3:
      > Merge from origin/stable
      > updated setup script
      > unicode support
      > remove unnecessary method
      > add new option for conn pooling
  ```

  如果我们在主项目中提交但并不推送子模块上的改动，其他尝试检出我们修改的人会遇到麻烦，因为他们无法得到依赖的子模块改动。那些改动还只存在于我们本地的分支中。

  为了不发生上述的情况，要让Git在推送到主项目前检查所有子模块是否已经推送（推送到服务器）。

  **下方命令为自动子模块是否提交，防止错误。**

  `git push`命令可以设置`--recurse-submodules`参数`check`或`on-demand`。

  如果任何提交的子模块改动没有推送那么`check`选项会直接使`push`失败

  `$ git push --recurse-submodules=check`

  ```
    $ git push --recurse-submodules=check
    The following submodule paths contain changes that can
    not be found on any remote:
      DbConnector

    Please try

        git push --recurse-submodules=on-demand

    or cd to the path and use

        git push

    to push them to a remote.
  ```

  当然`$ git push --recurse-submodules=check`也会给我们一些建议。

  最简单的方法是进入每一个子模块中，然后手动推送到远程创库，确保本地修改能别其他人获得，之后再推送。

  另一种选项是使用`on-demand`值，它会尝试为你自动推送子模块的修改。

  ```
    $ git push --recurse-submodules=on-demand
    Pushing submodule 'DbConnector'
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 917 bytes | 0 bytes/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    To https://github.com/chaconinc/DbConnector
       c75e92a..82d2ad3  stable -> stable
    Counting objects: 2, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (2/2), 266 bytes | 0 bytes/s, done.
    Total 2 (delta 1), reused 0 (delta 0)
    To https://github.com/chaconinc/MainProject
       3d6d338..9a377d1  master -> master
  ```

  使用`$ git push --recurse-submodules=on-demand`时如果子模块推动失败了，那么主项目也会失败。

- 合并子模块改动

  如果你与其他人同时改动了一个子模块，那么可能会遇上一些问题。

  如果子模块的历史已经分叉，并在父项目中分别提交到分叉的分支上，那么你需要做一些工作来修复它。

  为了解决这个问题，先运行`git diff`查看。

  ```
    $ git diff
    diff --cc DbConnector
    index eb41d76,c771610..0000000
    --- a/DbConnector
    +++ b/DbConnector
  ```

  `eb41d76`是子模块中共有的提交，而`c771610`是上游拥有的提交。

  如果进入子模块中，HEAD应该在`eb41d76`上，如果不在，手动处理。

  所有先进入子模块目录，基于`git diff`的第二个SHA创建一个分支，然后手动合并。

  ```
    $ cd DbConnector                //进入子模块目录

    $ git rev-parse HEAD                     //查看HEAD的SHA值
    eb41d764bccf88be77aced643c13a7fa86714135

    $ git branch try-merge c771610            //从 c771610 创建分支
    (DbConnector)

    $ git merge try-merge                    //合并到try-merge分支。
    Auto-merging src/main.c
    CONFLICT (content): Merge conflict in src/main.c
    Recorded preimage for 'src/main.c'
    Automatic merge failed; fix conflicts and then commit the result.
  ```

  这里得到一个合并冲突，所以如果想解决并提交它，需要通过结果来更新主项目。

  ```
    $ vim src/main.c                 // 手动处理冲突
    $ git add src/main.c                    //添加到暂存区
    $ git commit -am 'merged our changes'    //提交
    Recorded resolution for 'src/main.c'.
    [master 9fd905e] merged our changes

    $ cd ..             //回到主目录
    $ git diff             //    再次检查SHA-1值
    diff --cc DbConnector
    index eb41d76,c771610..0000000
    --- a/DbConnector
    +++ b/DbConnector
    @@@ -1,1 -1,1 +1,1 @@@
    - Subproject commit eb41d764bccf88be77aced643c13a7fa86714135
     -Subproject commit c77161012afbbe1f58b5053316ead08f4b7e6d1d
    ++Subproject commit 9fd905e5d7f45a0d4cbc43d1ee550f16a30e825a
    $ git add DbConnector            //解决子模块冲突的记录

    $ git commit -m "Merge Tom's Changes"        //提交合并
    [master 10d2c60] Merge Tom's Changes
  ```

```
如果Git目录中有同样的一个合并提交，它的历史包含两边的提交，Git会建议你将它作为一个可行的解决方案。

如果有解决方案，你会看到类似下面的信息

    $ git merge origin/master
    warning: Failed to merge submodule DbConnector (not fast-forward)
    Found a possible merge resolution for the submodule:
     9fd905e5d7f45a0d4cbc43d1ee550f16a30e825a: > merged our changes
    If this is correct simply add it to the index for example
    by using:

      git update-index --cacheinfo 160000 9fd905e5d7f45a0d4cbc43d1ee550f16a30e825a "DbConnector"

    which will accept this suggestion.
    Auto-merging DbConnector
    CONFLICT (submodule): Merge conflict in DbConnector
    Automatic merge failed; fix conflicts and then commit the result.

Git会建议你更新索引，就像运行了`git add`那样，这样会清除冲突，随后提交。

不过下方是另外一种解决方案。

进入子模块，查看差异，快进的这次提交。

    $ cd DbConnector/
    $ git merge 9fd905e
    Updating eb41d76..9fd905e
    Fast-forward

    $ cd ..
    $ git add DbConnector
    $ git commit -am 'Fast forwarded to a common submodule child'

这个方法可以检验工作是否有效，以及确保子模块目录中有代码。
```

### 7.11.4 子模块技巧

你可以做几件事情来让使用子模块工作轻松一点。

- 子模块遍历

  有一个`foreach`子模块命令，它能在每一个子模块中运行任意命令。如果项目中包含了大量子模块，这会非常有用。

  **例子：**

  假设我们想要开始开发一项新功能或者修改一些错误，并且需要在几个子模块内工作。我们可以轻松地保存所有子模块的工作进度。

  使用命令`$ git submodule foreach 'git stash'`

  ```
    $ git submodule foreach 'git stash'
    Entering 'CryptoLibrary'
    No local changes to save
    Entering 'DbConnector'
    Saved working directory and index state WIP on stable: 82d2ad3 Merge from origin/stable
    HEAD is now at 82d2ad3 Merge from origin/stable
  ```

  然后我们可以创建一个新分支，并将所有子模块都切换过去。

  使用命令`$ git submodule foreach 'git checkout -b featureA'`

  ```
    $ git submodule foreach 'git checkout -b featureA'
    Entering 'CryptoLibrary'
    Switched to a new branch 'featureA'
    Entering 'DbConnector'
    Switched to a new branch 'featureA'
  ```

  能够生成一个主项目与所有子项目的改动统一时非常有用的。

  下方命令用来显示主项目与子项目的差异。

  ```
    $ git diff; git submodule foreach 'git diff'
  ```

- 有用的别名

  设置别名，方便日常使用

  ```
    $ git config alias.sdiff '!'"git diff && git submodule foreach 'git diff'"    //显示修改
    $ git config alias.spush 'push --recurse-submodules=on-demand'                    //子模块拉取
    $ git config alias.supdate 'submodule update --remote --merge'                    //子模块合并
  ```

### 7.11.5 子模块的问题

其他的一些小问题

**例子：**

假如在有子模块的项目中切换分支可能会有问题。如果你创建一个新分支，在其中添加一个子模块，之后切换到没有子模块的分支上，你依然会有一个没有跟踪的子模块目录。

```
$ git checkout -b add-crypto
Switched to a new branch 'add-crypto'

$ git submodule add https://github.com/chaconinc/CryptoLibrary
Cloning into 'CryptoLibrary'...
...

$ git commit -am 'adding crypto library'
[add-crypto 4445836] adding crypto library
 2 files changed, 4 insertions(+)
 create mode 160000 CryptoLibrary

$ git checkout master
warning: unable to rmdir CryptoLibrary: Directory not empty
Switched to branch 'master'
Your branch is up-to-date with 'origin/master'.

$ git status
On branch master
Your branch is up-to-date with 'origin/master'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)

    CryptoLibrary/

nothing added to commit but untracked files present (use "git add" to track)
```

移除文件并不困难。但是当切换回包含子模块的分支时，需要使用`$ submodule update --init`来重新创建和填充。

```
$ git clean -fdx
Removing CryptoLibrary/

$ git checkout add-crypto
Switched to branch 'add-crypto'

$ ls CryptoLibrary/

$ git submodule update --init
Submodule path 'CryptoLibrary': checked out 'b8dda6aa182ea4464f3f3264b11e0268545172af'

$ ls CryptoLibrary/
Makefile    includes    scripts        src
```

当想要将正在使用的子目录变为子模块是时。要特别注意，如果删除子目录然后运行`submodule add`,Gith会报错。

```
$ rm -Rf CryptoLibrary/
$ git submodule add https://github.com/chaconinc/CryptoLibrary
'CryptoLibrary' already exists in the index
```

必须要先取消暂存`CryptoLibrary`目录。然后才可以添加子模块。

```
$ git rm -r CryptoLibrary
$ git submodule add https://github.com/chaconinc/CryptoLibrary
Cloning into 'CryptoLibrary'...
remote: Counting objects: 11, done.
remote: Compressing objects: 100% (10/10), done.
remote: Total 11 (delta 0), reused 11 (delta 0)
Unpacking objects: 100% (11/11), done.
Checking connectivity... done.
```

**另一个例子：**

当你在分支中做了一些工作。现在要切换回的分支中，修改过的文件还在子目录中，而不是子模块中时，会报错。

```
$ git checkout master
error: The following untracked working tree files would be overwritten by checkout:
  CryptoLibrary/Makefile
  CryptoLibrary/includes/crypto.h
  ...
Please move or remove them before you can switch branches.
Aborting
```

这时可以使用`check -f`来强制切换，但是要小心，如果其中有未保存的修改，这个命令会把它们覆盖掉。

```
$ git checkout -f master
warning: unable to rmdir CryptoLibrary: Directory not empty
Switched to branch 'master'
```

当你切换回来之后，因为一些原因得到了一个空的`CryptoLibrary`目录，并且`git submodule update`也无法修复它。

你需要进入到子模块中运行`git checkout .`来找回所有的文件，也可以通过`submodule foreach`脚本来为多个子模块运行。

_注意：近来子模块将所有Git数据保存在顶级项目的`.git`目录中。删除一个子模块并不会丢失任何提交或分支。_

--------------------------------------------------------------------------------

## 7.12 打包

使用次数上并不常用，功能上十分有用。

将要提交的数据打包，打包的内容为一个二进制文件。

随后可以将文件通过邮件或者闪存传给其他人，随后解包到其他的仓库中。

**例子：**

假设你有一个包含两个提交的仓库：

```
$ git log
commit 9a466c572fe88b195efd356c3f2bbeccdb504102
Author: Scott Chacon <schacon@gmail.com>
Date:   Wed Mar 10 07:34:10 2010 -0800

    second commit

commit b1ec3248f39900d2a406049d762aa68e9641be25
Author: Scott Chacon <schacon@gmail.com>
Date:   Wed Mar 10 07:34:01 2010 -0800

    first commit
```

如果你想把这个仓库发送给其他人，但你没有其他仓库的权限，或者不想建立一个仓库。

这时你就可以用`git bundle create`命令来打包。

命令解释`$ git bundle create [文件名] HEAD master` 包括为设定的文件名，文件中包含`master`分支所需的数据。

```
$ git bundle create repo.bundle HEAD master
Counting objects: 6, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (6/6), 441 bytes, done.
Total 6 (delta 0), reused 0 (delta 0)
```

使用`bundle`命令时，需要列出所有希望打包的引用或者提交的区间。如果希望仓库可以被克隆，应该增加一个HEAD引用。

当别人传给你一个`repo.bundle`文件并希望你在这个项目上工作，你可以从这个二进制文件中克隆出一个目录，就像从一个URL克隆一样。

```
$ git clone repo.bundle repo
Initialized empty Git repository in /private/tmp/bundle/repo/.git/
$ cd repo
$ git log --oneline
9a466c5 second commit
b1ec324 first commit
```

如果你在打包时没有包含HEAD引用，你还需要在命令后指定一个`-b master`或者其他被引入的分支，否则Git不知道应该检出哪一个分支。

现在假设你提交了3个修订，并要用邮件或U盘，将新的提交放在一个包里传回去。

```
$ git log --oneline
71b84da last commit - second repo
c99cf5b fourth commit - second repo
7011d3d third commit - second repo
9a466c5 second commit
b1ec324 first commit
```

首先要确认我们要打包的提交区间，与网络传输不同（网络传输会自动计算。）。

当然可是打包整个仓库，但最好仅仅打包变更的部分。

为了仅打包变更的部分，我们需要计算差别。

有很多种方法去指明提交区间，`origin/master..master`或`master ^origin/master`之类的方法。

可以使用`$ git log --oneline master ^origin/master`来测试。

```
$ git log --oneline master ^origin/master
71b84da last commit - second repo
c99cf5b fourth commit - second repo
7011d3d third commit - second repo
```

这样就找出了要打包的列表。可以使用`$ git bundle create`命令，加上[文件名]，与要打包的提交区间。

```
$ git bundle create commits.bundle master ^9a466c5
Counting objects: 11, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (9/9), 775 bytes, done.
Total 9 (delta 0), reused 0 (delta 0)
```

当其他人拿到时，可以使用`bundle verify`命令检查是否为一个合法的Git包，是否有共同的祖先来导入。

```
$ git bundle verify ../[commits.bundle]
The bundle contains 1 ref
71b84daaf49abed142a373b6e5c59a22dc6560dc refs/heads/master
The bundle requires these 1 ref
9a466c572fe88b195efd356c3f2bbeccdb504102 second commit
../commits.bundle is okay
```

如果在打包时，仅仅打包了两个提交，检查时会报错

```
$ git bundle verify ../commits-bad.bundle
error: Repository lacks these prerequisite commits:
error: 7011d3d8fc200abe0ad561c011c3852a4b7bbe95 third commit - second repo
```

如果想看看包中可以导入的分支使用下方的命令：

```
$ git bundle list-heads ../[commits.bundle]
```

[commits.bundle]可以是任意的导入包

现在要从包中取出`master`分支到仓库中的`other-master`分支。

```
$ git fetch ../commits.bundle master:other-master
From ../commits.bundle
 * [new branch]      master     -> other-master
```

最后可以看到`other-master`分支，以及自己的`master`分支。

下方代码为查看的结果。

```
$ git log --oneline --decorate --graph --all
* 8255d41 (HEAD, master) third commit - first repo
| * 71b84da (other-master) last commit - second repo
| * c99cf5b fourth commit - second repo
| * 7011d3d third commit - second repo
|/
* 9a466c5 second commit
* b1ec324 first commit
```

--------------------------------------------------------------------------------

## 7.13 替换

Git对象是不可改变的，但是它提供一种方法，用其他对象假装替换数据库中的Git对象。

`replace`命令可以让你在Git中指定一个对象，并可以声 明`每次遇到这个Git对象时，假装它是其他的东西。`

**例子：**

你有一个大型的代码历史并想把自己的仓库分成一个短历史和一个更大更长久的历史，短历史供新的开发者使用，长历史给喜欢挖掘数据的人使用。

你可以通过用新仓库的提交链接老仓库中的提交。

这里有一个有5次提交的仓库

```
$ git log --oneline
ef989d8 fifth commit
c6e1e95 fourth commit
9c68fdc third commit
945704c second commit
c1822cf first commit
```

我们想将其拆分为两个仓库，第一次到第四次提交作为一个历史版本，第四、五次提交作为第二个历史版本。

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/replace1.png)

创建一个历史版本很容易，只将一个历史中分支推送到一个新的远程仓库的master分支。

使用命令`$ git branch history [c6e1e95]`[c6e1e95]为分支创建处的SHA-1值。

```
$ git branch history c6e1e95
$ git log --oneline --decorate
ef989d8 (HEAD, master) fifth commit
c6e1e95 (history) fourth commit
9c68fdc third commit
945704c second commit
c1822cf first commit
```

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/replace2.png)

现在我们可以把这个新的`history`分支推送到我们新仓库的`master`分支

```
$ git remote add project-history https://github.com/schacon/project-history
$ git push project-history history:master
Counting objects: 12, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (12/12), 907 bytes, done.
Total 12 (delta 0), reused 0 (delta 0)
Unpacking objects: 100% (12/12), done.
To git@github.com:schacon/project-history.git
 * [new branch]      history -> master
```

这样历史版本就发布了。难一点的部分是删除我们最近的历史让它变得更小。我们需要一个重叠以便用一个相等的提交来替换另一个提交。这样一来，我们将截断到第四、五个提交。

下方代码为显示当前提交。

```
$ git log --oneline --decorate
ef989d8 (HEAD, master) fifth commit
c6e1e95 (history) fourth commit
9c68fdc third commit
945704c second commit
c1822cf first commit
```

现在的情况下，创建一个能够扩展历史的基础提交是很有用的。其他开发者想要修改第一次提交或者其他操作时就知道要做些什么了，因此，我们要用命令创建一个最初的提交，然后将第四、五次提交变基到它上面。

我们需要选择一个点去拆分，第三个提交是一个很好的选择。使用`commit-tree`命令来创建基础提交，这样我们就有了一个树，并返回一个全新的，无父节点的SHA提交对象。

```
$ echo 'get history from blah blah blah' | git commit-tree 9c68fdc^{tree}
622e88e9cbfbacfb75b5279245b9fb38dfea10cf
```

`echo`是说明，后方是创建。

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/replace3.png)

现在就有了一个基本提交了，我们可以通过`git rebase --onto`命令将剩余的历史变基到基础提交上。

`--onto`参数是`commit-tree`命令返回的SHA值，变基点会为第三次提交。

```
$ git rebase --onto 622e88 9c68fdc
First, rewinding head to replay your work on top of it...
Applying: fourth commit
Applying: fifth commit
```

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/replace4.png)

现在可以将新历史推送到新项目中，当有人克隆这个仓库时，他们只能看到最近两次提交以及一个包含说明的基础提交。

假如我们是一个想要获得整个历史的开发者。在克隆这个阶段后的仓库后，想要获得历史数据，就需要添加第二个远程的历史版本并拉取到本地。

```
$ git clone https://github.com/schacon/project
$ cd project

$ git log --oneline master
e146b5f fifth commit
81a708d fourth commit
622e88e get history from blah blah blah

$ git remote add project-history https://github.com/schacon/project-history
$ git fetch project-history
From https://github.com/schacon/project-history
 * [new branch]      master     -> project-history/master
```

现在其他人在`master`分支中有最近的提交，在`project-history/master`分支中有过去的提交。

下方代码为查看提交信息：

```
$ git log --oneline master
e146b5f fifth commit
81a708d fourth commit
622e88e get history from blah blah blah

$ git log --oneline project-history/master
c6e1e95 fourth commit
9c68fdc third commit
945704c second commit
c1822cf first commit
```

为了合并两个分支，可以使用`git replace`命令加上想要替换的提交信息。

例子中，可以用`master`分支中的第四次提交(81a708d)替换为`project-history/master`分支中的第四个提交。

```
$ git replace 81a708d c6e1e95
```

再次查看`master`分支中的历史信息，显示如下：

```
$ git log --oneline master
e146b5f fifth commit
81a708d fourth commit
9c68fdc third commit
945704c second commit
c1822cf first commit
```

![](https://git-scm.com/book/en/v2/book/07-git-tools/images/replace5.png)

有趣的是，即使使用了`c6e1e95`替换数据，它的SHA-1显示为`81a708d`,即使你运行了`cat-file`命令，它仍会显示替换的数据。

`81a708d` 真正的父提交是 `622e882` ，而非呈现的 `9c68fdce` 提交

## 7.14 凭证存储

如果你使用的是SSH方式连接服务器，并设置了一个没有口令的密钥，这样可以不输出用户名和密码的情况下安全地传输数据。而使用HTTP是不可能的。

Git有一个凭证系统来处理一些登陆验证。使用HTTP通信时。

- 默认为缓存密码。每次连接都会询问用户名与密码

- `cache`模式。将密码放入内存一段时间。密码永远不会被存储在硬盘中，并且15分钟后从内存中清除。

- `store`模式。将密码以明文的形式存储到硬盘，并且永不过期。 缺点就是密码用明文的方式存放在home的目录下。

- 如果使用的是Mac，Git还有一种`osxkeychain`模式，它会将密码缓存到系统用户的钥匙串中。这种方法将密码存放在硬盘中，永不过期，但是被加密的。

- 如果使用的是window，可以安装一个叫做`winstore`的辅助工具。与`osxkeychain`十分类似。

设置方法：

```
$ git config --global credential.helper cache
```

`store`模式可以接受`--file [目录]`，可以自定义存放密码的文件目录。默认是`~/.git-credentials`.

`cache`模式可以使用`--timeout [秒数]`可以设置后台密码的保存时间。默认是900秒。

下方代码是自定义路径的例子：

```
$ git config --global credential.helper store --file ~/.my-credentials
```

Git允许配置多个辅助工具。当查找特定服务器时，Git会按顺序查询，并在找到有效用户与密码时停止。

如果在U盘上有一个密码文件，又希望U盘拔出时使用内存缓存来保护用户名和密码。要设置`.gitconfig`文件。

下方是配置

```
[credential]
    helper = store --file /mnt/thumbdrive/.git-credentials
    helper = cache --timeout 30000
```

### 7.14.1 底层实现

Git密码辅助工作的命令是`$ git credential`,这个命令接收一个参数，并通过标准输入获取更多参数。

**例子：**

假设已经配置好一个凭证辅助工具，这个辅助工具保存了 mygithost 的凭证信息。 下面是一个使用 "fill" 命令的会话，当 Git 尝试寻找一个服务器的凭证时就会被调用。

```
$ git credential fill   //这是开始交互的命令
protocol=https             //Git-credential 接下来会等待标准输入。 我们提供我们所知道的信息：协议和主机名。
host=mygithost
                        //一个空行代表输入已经完成，凭证系统应该输出它所知道的信息。
protocol=https             //接下来由 Git-credential 接管，并且将找到的信息打印到标准输出。
host=mygithost
username=bob
password=s3cre7

$ git credential fill         //如果没有找到对应的凭证，Git 会询问用户的用户名和密码，我们将这些信息输入到在标准输出的地方（这个例子中是同一个控制台）。
protocol=https
host=unknownhost

Username for 'https://unknownhost': bob
Password for 'https://bob@unknownhost':
protocol=https
host=unknownhost
username=bob
password=s3cre7
```

密码系统实际调用的程序和Git本身是分开的，具体是哪一个以及如何调用与`credential.helper`配置的值有关。

配置值                                 | 行为
:---------------------------------- | :--------------------------------
foo                                 | 执行git-credential-foo
foo -a --opt=bcd                    | 执行git-credential-foo -a --opt=bcd
/absolute/path/foo -xyz             | 执行 /absolute/path/foo -xyz
!f() { echo "password=s3cre7"; }; f | ! 后面的代码会在shell执行

可以配置它们来接受命令行参数。

通常格式是`git-credential-foo [args] [action]`输入/输出协议和`git-credential`一样。

- get 是请求输入一对用户名和密码

- store 是请求保存一个凭证到辅助工具的内存

- erase 会将给定的证书从辅助工具内存中清除。

**例子：**

```
$ git credential-store --file ~/git.store store         //告诉 git-credential-store 去保存凭证，当访问https://mygithost时使用用户名bob，密码s3cre7。
protocol=https
host=mygithost
username=bob
password=s3cre7
$ git credential-store --file ~/git.store get   //取出刚刚保存的用户名与密码。
protocol=https
host=mygithost

username=bob
password=s3cre7
```

`osxkeychain` 和 `winstore`会使用原生格式，而`cache`使用内存格式。

### 7.14.2 自定义凭证缓存

参看[文档](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E5%87%AD%E8%AF%81%E5%AD%98%E5%82%A8#自定义凭证缓存)
