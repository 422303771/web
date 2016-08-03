<!--toc-->

- [git说明分支](#git说明分支)
	- [3\. Git分支](#3-git分支)
		- [3.1 分支简介](#31-分支简介)
			- [3.1.1 分支创建](#311-分支创建)
			- [3.1.2 分支切换](#312-分支切换)
		- [3.2 分支的新建与合并](#32-分支的新建与合并)
			- [3.2.1 新建分支](#321-新建分支)
			- [3.2.2 分支的合并](#322-分支的合并)
			- [3.2.3 遇到冲突时的分支合并](#323-遇到冲突时的分支合并)
		- [3.3 分支管理](#33-分支管理)
		- [3.4 分支开发工作流](#34-分支开发工作流)
			- [3.4.1 长期分支](#341-长期分支)
			- [3.4.2 特性分支](#342-特性分支)
		- [3.5 远程分支](#35-远程分支)
			- [3.5.1 推送](#351-推送)
			- [3.5.2 跟踪分支](#352-跟踪分支)
			- [3.5.3 拉取](#353-拉取)
			- [3.5.4 删除远程分支](#354-删除远程分支)
		- [3.6 变基](#36-变基)
			- [3.6.1 变基的基本操作](#361-变基的基本操作)
			- [3.6.2 变基的风险](#362-变基的风险)

<!-- tocstop -->

-----

# git说明分支

## 3\. Git分支

### 3.1 分支简介

#### 3.1.1 分支创建

> `$ git branch [testing]` [testing]为新分支名称。

> 例如下图

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/two-branches.png)

> git如何知道当前在那个分支上，见下图

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/head-to-master.png)

> 使用`$ git log --oneline --decorate` 查看分支当前所指对象啊。

#### 3.1.2 分支切换

> `$ git checkout [testing]` [testing] 为分支名。

> 效果如下图

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/head-to-testing.png)

> 使用`$ git commit -a -m 'made a change'`后，HEAD随分支移动，如下图

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/advance-testing.png)

> 使用`$ git checkout master`时，切回master分支。如下图

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/checkout-master.png)

> _注意：分支切换会改变目录中的文件_

> 再次更改后`$ git commit -a -m 'made other changes'` ,分支变化如下

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/advance-master.png)

> _述两次改动针对的是不同分支：你可以在不同分支间不断地来回切换和工作，并在时机成熟时将它们合并起来。 而所有这些工作，你需要的命令只有 branch、checkout 和 commit。_

> 运行`$ git log --oneline --decorate --graph --all`

> 可以查看到提交历史、各个分支的指向以及项目的分支分叉情况。

> **例如：**

> ```
>    $ git log --oneline --decorate --graph --all
>    * c2b9e (HEAD, master) made other changes
>    | * 87ab2 (testing) made a change
>    |/
>    * f30ab add feature #32 - ability to add new formats to the
>    * 34ac2 fixed bug #1328 - stack overflow under certain conditions
>    * 98ca9 initial commit of my project
> ```

--------------------------------------------------------------------------------

### 3.2 分支的新建与合并

#### 3.2.1 新建分支

> 假设项目需要一些提交 ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/basic-branching-1.png)

> 新建一个分支并同时切换到那个分支上

> 运行`-b`参数的`git checkout`命令，如下

> `$ git checkout -b [iss53]` [iss53]可以是任意的分支名。

> 上方命令是下方命令的简写

> `$ git branch iss53`<br>
> `$ git checkout iss53`

> 结果如下

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/basic-branching-2.png)

> 执行`$ git commit -a -m 'added a new footer [issue 53]'`完成更改后

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/basic-branching-3.png)

> 现在需要修改一个更紧急的问题，而忽略暂时忽略iss53，切回到master分钟后重新开始。

> `$ git checkout master`

> 随后新建一个分支`hotfix`, 并切入。

> `$ git checkout -b hotfix`

> 修改完成后提交`$ git commit -a -m 'fixed the broken email address'`。

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/basic-branching-4.png)

> 修改正确后，将`hotfix` 合并到部署线上，可以使用`git merge`

> ```
>    $ git checkout master
>    $ git merge hotfix
>    Updating f42c576..3a0874c
>    Fast-forward
>         index.html | 2 ++
>        1 file changed, 2 insertions(+)
> ```

> "快进（fast-forward）",如果一个分支到达另一个分支，Git 在合并两者时，将指针向前推进（指针右移）。如下图情况

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/basic-branching-5.png)

> 现在回到之前的工作，应该先删除`hotfix`,再开始。使用带 -d 选项的 git branch 命令来删除分支。

> `$ git branch -d hotfix`

> 现在切回之前的iss53继续

> `$ git checkout iss53`,<br>
> `$ git commit -a -m 'finished the new footer [issue 53]'`

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/basic-branching-6.png)

--------------------------------------------------------------------------------

#### 3.2.2 分支的合并

> 当已经修正了iss53后，将工作并入master分支。需要要将iss53分支合并到master。

> 同样使用`git merge`

> ```
>    $ git checkout master
>    Switched to branch 'master'
>    $ git merge iss53
>    Merge made by the 'recursive' strategy.
>    index.html |    1 +
>    1 file changed, 1 insertion(+)
> ```

> git的做法如下图,这个过程叫做`一次合并提交`

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/basic-merging-1.png) ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/basic-merging-2.png)

> 随后删除iss53分支<br>
> `$ git branch -d iss53`

--------------------------------------------------------------------------------

#### 3.2.3 遇到冲突时的分支合并

> 当合并iss53时，产生合并冲突。

> 使用`$ git status`命令来查看那些因包含合并冲突而处于未合并（unmerged）状态的文件。

> ```
>    $ git status
>    On branch master
>    You have unmerged paths.
>         (fix conflicts and run "git commit")

>    Unmerged paths:
>          (use "git add <file>..." to mark resolution)

>            both modified:      index.html

>    no changes added to commit (use "git add" and/or "git commit -a")
> ```

> 打开并修改冲突文件

> **例子：**

> ```
>    <<<<<<< HEAD:index.html
>    <div id="footer">contact : email.support@github.com</div>
>    =======
>    <div id="footer">
>     please contact us at support@github.com
>    </div>
>    >>>>>>> iss53:index.html
> ```

> **说明：`HEAD`所指的的版本（也就是master分支的位置）在======之前，而后面为iss53分支。**

> 手动处理冲突后，使用`git add`

> 可以是使用`git mergetool`命令进行图形化处理。完成时后退出时，git会询问合并是否成功。

> 可以运行`git status`查看合并冲突是否解决。

> 如果对修改满意，使用`git commit`来完成合并提交。

--------------------------------------------------------------------------------

### 3.3 分支管理

> `git branch` 命令不只是可以创建与删除分支

> 加任何参数运行,得到当前所有分支列表。

> **例子：**

> ```
>    $ git branch
>       iss53
>    * master
>      testing
> ```

> _注意：`_`为HEAD所指向的分支。*

> 如果需要查看每一个分支的最后一次提交，可以运行 `git branch -v` 命令。

> **例子：**

> ```
>    $ git branch -v
>       iss53   93b412c fix javascript issue
>    * master  7a98805 Merge branch 'iss53'
>        testing 782fd34 add scott to the author list in the readmes
> ```

>

> `--merged`与 `--no-merged`这两个有用的选项可以过滤这个列表中已经合并或尚未合并到当前分支的分支

> ```
>    $ git branch --merged
>       iss53
>    * master
> ```

> _列表中分支名字前没有_ 号的分支通常可以使用 `git branch -d`删除掉。*

> 查看所有包含未合并工作的分支，可以运行 git branch --no-merged

> ```
>    $ git branch --no-merged
>        testing
> ```

> _注意：当没有合并分支使用`git branch -d`删除时会失败，这时可以使用`-D`来强行删除。_

--------------------------------------------------------------------------------

### 3.4 分支开发工作流

#### 3.4.1 长期分支

> 刚开发到一个比较稳定的阶段，稳定版本被合并到master。

> 渐进稳定分支的线性图

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/lr-branches-1.png)

> 与上图的显示方式相同。

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/lr-branches-2.png)

#### 3.4.2 特性分支

>

> 特性分支是一种短期分支。

> 拥有多个特性分支的提交历史

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/topic-branches-1.png)

> 假设两件事情：你决定使用第二个方案来解决那个问题，即使用在 iss91v2 分支中方案；另外，你将 dumbidea 分支拿给你的同事看过之后，结果发现这是个惊人之举。 这时你可以抛弃 iss91 分支（即丢弃 C5 和 C6 提交），然后把另外两个分支合并入主干分支。 最终你的提交历史看起来像下面这个样子

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/topic-branches-2.png)

> _上图的分支全部合并到master分支。_

--------------------------------------------------------------------------------

### 3.5 远程分支

首先远程仓库名字 "origin" 与分支名字 "master" 一样，在 Git 中并没有任何特别的含义一样。同时 "master" 是当你运行`git init` 时默认的起始分支名字，原因仅仅是它的广泛使用，"origin" 是当你运行 git clone 时默认的远程仓库名字。 如果你运行`git clone -o booyah`，那么你默认的远程分支名字将会是 `booyah/master`。 >

> **例子：**

> 假设你的网络里有一个在 `git.ourcompany.com` 的 Git 服务器。 如果你从这里克隆，Git 的`clone` 命令会为你自动将其命名为 origin，拉取它的所有数据，创建一个指向它的 `master`分支的指针，并且在本地将其命名为 `origin/master`。 Git 也会给你一个与 origin 的 master 分支在指向同一个地方的本地 `master` 分支，这样你就有工作的基础。

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/remote-branches-1.png)

> 如果你在本地的 `master`分支做了一些工作，然而在同一时间，其他人推送提交到`git.ourcompany.com` 并更新了它的 `master` 分支，那么你的提交历史将向不同的方向前进。 也许，只要你不与 origin 服务器连接，你的 `origin/master`指针就不会移动。

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/remote-branches-2.png)

> 如果要同步你的工作，运行 `git fetch origin`命令。 这个命令查找 "origin" 是哪一个服务器（在本例中，它是 git.ourcompany.com），从中抓取本地没有的数据，并且更新本地数据库，移动 `origin/master` 指针指向新的、更新后的位置。

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/remote-branches-3.png)

> 为了演示有多个远程仓库与远程分支的情况，我们假定你有另一个内部 Git 服务器，仅用于你的 sprint 小组的开发工作。 这个服务器位于 `git.team1.ourcompany.com`。 你可以运行`git remote add` 命令添加一个新的远程仓库引用到当前的项目，这个命令我们会在 Git 基础 中详细说明。 将这个远程仓库命名为 `teamone`，将其作为整个 URL 的缩写。

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/remote-branches-4.png)

> 现在，可以运行 `git fetch teamone`来抓取远程仓库 `teamone` 有而本地没有的数据。 因为那台服务器上现有的数据是 `origin`服务器上的一个子集，所以 Git 并不会抓取数据而是会设置远程跟踪分支 `teamone/master`指向`teamone` 的`master`分支。

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/remote-branches-5.png)

#### 3.5.1 推送

> `$ git push [origin] [serverfix]` [origin]为远程仓库名称，[serverfix]为分支名称。

> **使用指南：如何避免每次输入密码**

> 如果你正在使用 HTTPS URL 来推送，Git 服务器会询问用户名与密码。 默认情况下它会在终端中提示服务器是否允许你进行推送。 如果不想在每一次推送时都输入用户名与密码，你可以设置一个 "credential cache"。 最简单的方式就是将其保存在内存中几分钟，可以简单地运行`git config --global credential.helper cache` 来设置它。

> 下一次其他协作者从服务器上抓取数据时，他们会在本地生成一个远程分支 origin/serverfix，指向服务器的 serverfix 分支的引用。 命令`$ git fetch [origin]` [origin]为远程仓库名称.

> 当抓取到新的远程跟踪分支时,不会有一个新的 serverfix 分支 - 只有一个不可以修改的 `origin/serverfix` 指针

> 可以运行 `git merge origin/serverfix` 将这些工作合并到当前所在的分支。 如果想要在自己的 `serverfix` 分支上工作，可以将其建立在远程跟踪分支之上

> ```
>    $ git checkout -b serverfix origin/serverfix
>    Branch serverfix set up to track remote branch serverfix from origin.
>    Switched to a new branch 'serverfix'
> ```

> 这会给你一个用于工作的本地分支，并且起点位于 `origin/serverfix`。

--------------------------------------------------------------------------------

#### 3.5.2 跟踪分支

从一个远程跟踪分支检出一个本地分支会自动创建一个叫做 "跟踪分支"（有时候也叫做 "上游分支"）<br>
>

> 当克隆一个仓库时,它通常会自动地创建一个跟踪 `origin/master` 的 `master` 分支。

> 运行`git checkout -b [branch] [remotename]/[branch]`<br>
> 可以使用`git checkout --track [remotename]/[branch]`替代上方命令

> 如果想要将本地分支与远程分支设置为不同名字，你可以轻松地增加一个不同名字的本地分支的上一个命令。

> `$ git checkout -b sf origin/serverfix`

> 现在，本地分支 `sf` 会自动从 `origin/serverfix` 拉取。

> 设置已有的本地分支跟踪一个刚刚拉取下来的远程分支，或者想要修改正在跟踪的上游分支，你可以在任意时间使用 `-u` 或 `--set-upstream-to` 选项运行 `git branch` 来显式地设置

> `$ git branch -u origin/serverfix`

> 如果想要查看设置的所有跟踪分支，可以使用 `git branch` 的 `-vv` 选项。 这会将所有的本地分支列出来并且包含更多的信息，如每一个分支正在跟踪哪个远程分支与本地分支是否是领先、落后或是都有

> ```
>    $ git branch -vv
>      iss53     7e424c3 [origin/iss53: ahead 2] forgot the brackets
>      master     1ae2a45 [origin/master] deploying index fix
>    * serverfix  f8674d9 [teamone/server-fix-good: ahead 3, behind 1] this should do it
>      testing    5ea463a trying something new
> ```

> _说明：<br>
> 这里可以看到 iss53 分支正在跟踪 origin/iss53 并且 "ahead" 是 2，意味着本地有两个提交还没有推送到服务器上。<br>
> 也能看到 master 分支正在跟踪 origin/master 分支并且是最新的。<br>
> 接下来可以看到 serverfix 分支正在跟踪 teamone 服务器上的 server-fix-good 分支并且领先 2 落后 1，意味着服务器上有一次提交还没有合并入同时本地有三次提交还没有推送。<br>
> 最后看到 testing 分支并没有跟踪任何远程分支。_

--------------------------------------------------------------------------------

#### 3.5.3 拉取

当 `git fetch` 命令从服务器上抓取本地没有的数据时，它并不会修改工作目录中的内容。 它只会获取数据然后让你自己合并。 然而，有一个命令叫作 `git pull`在大多数情况下它的含义是一个 `git fetch` 紧接着一个`git merge`命令。 如果有一个像之前章节中演示的设置好的跟踪分支，不管它是显式地设置还是通过 `clone` 或 `checkout`命令为你创建的，`git pull`都会查找当前分支所跟踪的服务器与分支，从服务器上抓取数据然后尝试合并入那个远程分支。

由于 `git pull` 的魔法经常令人困惑所以通常单独显式地使用 `fetch` 与 `merge`命令会更好一些。

--------------------------------------------------------------------------------

#### 3.5.4 删除远程分支

假设你已经通过远程分支做完所有的工作了 - 也就是说你和你的协作者已经完成了一个特性并且将其合并到了远程仓库的 master 分支（或任何其他稳定代码分支）。 可以运行带有 --delete 选项的 git push 命令来删除一个远程分支。如果想要从服务器上删除 serverfix 分支，运行下面的命令：

> ```
>        $ git push origin --delete serverfix
>        To https://github.com/schacon/simplegit
>         - [deleted]         serverfix
> ```

--------------------------------------------------------------------------------

### 3.6 变基

在 Git 中整合来自不同分支的修改主要有两种方法：`merge`以及 `rebase`。

#### 3.6.1 变基的基本操作

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/basic-rebase-2.png)

> 上图使用`merge`命令进行合并。

> 还有另一种方法：提取在 C4 中引入的补丁和修改，然后在 C3 的基础上再应用一次。 在 Git 中，这种操作就叫做`变基`。

> 使用`rebase`命令将提交到某一分支上的所有修改都移至另一分支上，就好像"重新播放"一样。

> **例子：**

> ```
>    $ git checkout experiment
>    $ git rebase master
>    First, rewinding head to replay your work on top of it...
>    Applying: added staged command
> ```

> _说明：它的原理是首先找到这两个分支（即当前分支 experiment、变基操作的目标基底分支 master）的最近共同祖先 C2，然后对比当前分支相对于该祖先的历次提交，提取相应的修改并存为临时文件，然后将当前分支指向目标基底 C3, 最后以此将之前另存为临时文件的修改依序应用。_

> 操作原理图，如下。**将 C4 中的修改变基到 C3。**

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/basic-rebase-3.png)

> 之后回到`master`分支，进行一次快进合并。

> ```
>    $ git checkout master
>    $ git merge experiment
> ```

> 结果如下图。**master 分支的快进合并**

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/basic-rebase-4.png)

> **这两种整合方法的最终结果没有任何区别，但是变基使得提交历史更加整洁。**

> **一个更有趣的例子：**

> 你创建了一个特性分支 server，为服务端添加了一些功能，提交了 C3 和 C4。 然后从 C3 上创建了特性分支 client，为客户端添加了一些功能，提交了 C8 和 C9。 最后，你回到 server 分支，又提交了 C10。

> **从一个特性分支里再分出一个特性分支的提交历史**

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/interesting-rebase-1.png)

> 假设你希望将 client 中的修改合并到主分支并发布，但暂时并不想合并 server 中的修改，因为它们还需要经过更全面的测试。 这时，你就可以使用`git rebase`命令的 `--onto` 选项，选中在 client 分支里但不在 server 分支里的修改（即 C8 和 C9），将它们在 master 分支上重演.

> `$ git rebase --onto master server client`

> 以上命令的意思是："取出 `client` 分支，找出处于 `client` 分支和 `server` 分支的共同祖先之后的修改，然后把它们在 `master` 分支上重演一遍"。 这理解起来有一点复杂，不过效果非常酷。

> **截取特性分支上的另一个特性分支，然后变基到其他分支**

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/interesting-rebase-2.png)

> 现在可以快进合并 master 分支了

> ```
>    $ git checkout master
>    $ git merge client
> ```

> **快进合并 master 分支，使之包含来自 client 分支的修改.**

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/interesting-rebase-3.png)

> 接下来你决定将 server 分支中的修改也整合进来。 使用`git rebase [basebranch] [topicbranch]`命令可以直接将特性分支（即本例中的 server）变基到目标分支（即 master）上。这样做能省去你先切换到 server 分支，再对其执行变基命令的多个步骤。

> [basebranch]为要基变的分支，[topicbranch]为特性分支。

> `$ git rebase master server`

> **server 中的代码被"续"到了 master 后面。**

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/interesting-rebase-4.png)

> 然后就可以快进合并主分支 master 了

> ```
>    $ git checkout master
>    $ git merge server
> ```

> 至此，client 和 server 分支中的修改都已经整合到主分支里去了，你可以删除这两个分支

> ```
>    $ git branch -d client
>    $ git branch -d server
> ```

> ![](https://git-scm.com/book/en/v2/book/03-git-branching/images/interesting-rebase-5.png)

--------------------------------------------------------------------------------

#### 3.6.2 变基的风险

呃，奇妙的变基也并非完美无缺，要用它得遵守一条准则：

> **不要对在你的仓库外有副本的分支执行变基。**

> **不要对本地仓库之外的仓库执行变基，尤其是推送到服务器后。**

> 详细内容见[文档](https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%8F%98%E5%9F%BA)
