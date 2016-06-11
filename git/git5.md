# 5. 分布式 Git

## 5.1 分布式工作流程

### 5.1.1 集中式工作流

![](https://git-scm.com/book/en/v2/book/05-distributed-git/images/centralized_workflow.png)

> *说明：两个开发者从中心仓库克隆代码下来，同时作了一些修改，那么只有第一个开发者可以顺利地把数据推送回共享服务器。 第二个开发者在推送修改之前，必须先将第一个人的工作合并进来，这样才不会覆盖第一个人的修改。*

### 5.1.2 集成管理者工作流

![](https://git-scm.com/book/en/v2/book/05-distributed-git/images/integration-manager.png)

> *说明：每个开发者拥有自己仓库的写权限和其他所有人仓库的读权限。 这种情形下通常会有个代表“官方”项目的权威的仓库。 要为这个项目做贡献，你需要从该项目克隆出一个自己的公开仓库，然后将自己的修改推送上去。 接着你可以请求官方仓库的维护者拉取更新合并到主项目。 维护者可以将你的仓库作为远程仓库添加进来，在本地测试你的变更，将其合并入他们的分支并推送回官方仓库*
>
>>1. 项目维护者推送到主仓库。
>>2. 贡献者克隆此仓库，做出修改。
>>3. 贡献者将数据推送到自己的公开仓库。
>>4. 贡献者给维护者发送邮件，请求拉取自己的更新。
>>5. 维护者在自己本地的仓库中，将贡献者的仓库加为远程仓库并合并修改。
>>6. 维护者将合并后的修改推送到主仓库。



### 5.1.3 司令官与副官工作流

![](https://git-scm.com/book/en/v2/book/05-distributed-git/images/benevolent-dictator.png)

>*说明：一般拥有数百位协作开发者的超大型项目才会用到这样的工作方式，例如著名的 Linux 内核项目。 被称为副官（lieutenant）的各个集成管理者分别负责集成项目中的特定部分。 所有这些副官头上还有一位称为司令官（dictator）的总集成管理者负责统筹。 司令官维护的仓库作为参考仓库，为所有协作者提供他们需要拉取的项目代码。*
> 
>>1. 普通开发者在自己的特性分支上工作，并根据 master 分支进行变基。 这里是司令官的master分支。
>>2. 副官将普通开发者的特性分支合并到自己的 master 分支中。
>>3. 司令官将所有副官的 master 分支并入自己的 master 分支中。
>>4. 司令官将集成后的 master 分支推送到参考仓库中，以便所有其他开发者以此为基础进行变基。
>
>

---

## 5.2 向一个项目贡献 

> 决定贡献的因素  
> 1. 活跃贡献者的数量
> 2. 项目的使用流程
> 3. 用户的提交权限

### 5.2.1 提交准则

>**提交信息的快速说明** 在 Git 源代码中的 `Documentation/SubmittingPatches` 文件中阅读 [地址](https://github.com/git/git/blob/master/Documentation/SubmittingPatches)
>
>在提交前，运行 git diff --check，它将会找到可能的空白错误并将它们为你列出来。 
> 
>接着，让每一个提交独立，每一个问题独立。
>
>最后，提交信息要简介。
>

### 5.2.2 私有小型团队

>这里的`私有`指的是闭源。
>
>通常在一个特性分支工作一会儿，当它准备好整合时合并回你的 master 分支。 当想要共享工作时，将其合并回你自己的 master 分支，如果有改动的话然后抓取并合并 origin/master，最终推送到服务器上的 master 分支。
>
>**通常顺序像这样:**
>
>![](https://git-scm.com/book/en/v2/book/05-distributed-git/images/small-team-flow.png)
>

### 5.2.3 私有管理团队

>
>许多团队切换到 Git 是因为这一允许多个团队并行工作、并在之后合并不同工作的能力。 团队中更小一些的子小组可以通过远程分支协作而不必影响或妨碍整个团队的能力是 Git 的一个巨大优势。 在这儿看到的工作流程顺序类似这样:
>
>![](https://git-scm.com/book/en/v2/book/05-distributed-git/images/managed-team-flow.png)
>

### 5.2.4 派生的公开项目

详细内容查看[文档](https://git-scm.com/book/zh/v2/%E5%88%86%E5%B8%83%E5%BC%8F-Git-%E5%90%91%E4%B8%80%E4%B8%AA%E9%A1%B9%E7%9B%AE%E8%B4%A1%E7%8C%AE)

### 5.2.5 通过邮件的公开项目

许多项目建立了接受补丁的流程 - 需要检查每一个项目的特定规则，因为它们之间有区别。 因为有几个历史悠久的、大型的项目会通过一个开发者的邮件列表接受补丁，现在我们将会通过一个例子来演示。

工作流程与之前的用例是类似的 - 你为工作的每一个补丁序列创建特性分支。 区别是如何提交它们到项目中。 生成每一个提交序列的电子邮件版本然后邮寄它们到开发者邮件列表，而不是派生项目然后推送到你自己的可写版本。  

	$ git checkout -b topicA
	# (work)
	$ git commit
	# (work)
	$ git commit

现在有两个提交要发送到邮件列表。 使用 `git format-patch` 来生成可以邮寄到列表的` mbox` 格式的文件 - 它将每一个提交转换为一封电子邮件，提交信息的第一行作为主题，剩余信息与提交引入的补丁作为正文。 它有一个好处是是使用 `format-patch `生成的一封电子邮件应用的提交正确地保留了所有的提交信息。

	$ git format-patch -M origin/master
	0001-add-limit-to-log-function.patch
	0002-changed-log-output-to-30-from-25.patch

`format-patch` 命令打印出它创建的补丁文件名字。 `-M` 开关告诉 Git 查找重命名。 文件最后看起来像这样：

	$ cat 0001-add-limit-to-log-function.patch
	From 330090432754092d704da8e76ca5c05c198e71a8 Mon Sep 17 00:00:00 2001
	From: Jessica Smith <jessica@example.com>
	Date: Sun, 6 Apr 2008 10:17:23 -0700
	Subject: [PATCH 1/2] add limit to log function

	Limit log functionality to the first 20

	---
 	lib/simplegit.rb |    2 +-
 	1 files changed, 1 insertions(+), 1 deletions(-)

	diff --git a/lib/simplegit.rb b/lib/simplegit.rb
	index 76f47bc..f9815f1 100644
	--- a/lib/simplegit.rb
	+++ b/lib/simplegit.rb
	@@ -14,7 +14,7 @@ class SimpleGit
   		end

   		def log(treeish = 'master')
	-    command("git log #{treeish}")
	+    command("git log -n 20 #{treeish}")
   		end

   		def ls_tree(treeish = 'master')
	--
	2.1.0

也可以编辑这些补丁文件为邮件列表添加更多不想要在提交信息中显示出来的信息。 如果在 --- 行与补丁开头（`diff --git` 行）之间添加文本，那么开发者就可以阅读它；但是应用补丁时会排除它。

为了将其邮寄到邮件列表，你既可以将文件粘贴进电子邮件客户端，也可以通过命令行程序发送它。 粘贴文本经常会发生格式化问题，特别是那些不会合适地保留换行符与其他空白的 “更聪明的” 客户端。 幸运的是，Git 提供了一个工具帮助你通过 IMAP 发送正确格式化的补丁，这可能对你更容易些。 我们将会演示如何通过 Gmail 发送一个补丁，它正好是我们所知最好的邮件代理；可以在之前提到的 Git 源代码中的 `Documentation/SubmittingPatches` 文件的最下面了解一系列邮件程序的详细指令。

首先，需要在 ~/.gitconfig 文件中设置 imap 区块。 可以通过一系列的 `git config` 命令来分别设置每一个值，或者手动添加它们，不管怎样最后配置文件应该看起来像这样：

	[imap]
	  folder = "[Gmail]/Drafts"
	  host = imaps://imap.gmail.com
	  user = user@gmail.com
	  pass = p4ssw0rd
 	  port = 993
	  sslverify = false

如果 IMAP 服务器不使用 SSL，最后两行可能没有必要，host 的值会是 `imap://` 而不是` imaps://`。 当那些设置完成后，可以使用 `git imap-send` 将补丁序列放在特定 IMAP 服务器的 Drafts 文件夹中：

	$ cat *.patch |git imap-send
	Resolving imap.gmail.com... ok
	Connecting to [74.125.142.109]:993... ok
	Logging in...
	sending 2 messages
	100% (2/2) done

在这个时候，你应该能够到 Drafts 文件夹中，修改收件人字段为想要发送补丁的邮件列表，可能需要抄送给维护者或负责那个部分的人，然后发送。

你也可以通过一个 SMTP 服务器发送补丁。 同之前一样，你可以通过一系列的 git config 命令来分别设置选项，或者你可以手动地将它们添加到你的 ~/.gitconfig 文件的 sendmail 区块：

	[sendemail]
  		smtpencryption = tls
  		smtpserver = smtp.gmail.com
 		smtpuser = user@gmail.com
 		smtpserverport = 587

当这完成后，你可以使用 `git send-email` 发送你的补丁：

	$ git send-email *.patch
	0001-added-limit-to-log-function.patch
	0002-changed-log-output-to-30-from-25.patch
	Who should the emails appear to be from? [Jessica Smith <jessica@example.com>]
	Emails will be sent from: Jessica Smith <jessica@example.com>
	Who should the emails be sent to? jessica@example.com
	Message-ID to be used as In-Reply-To for the first email? y

然后，对于正在发送的每一个补丁，Git 会吐出这样的一串日志信息：

	(mbox) Adding cc: Jessica Smith <jessica@example.com> from
	  \line 'From: Jessica Smith <jessica@example.com>'
	OK. Log says:
	Sendmail: /usr/sbin/sendmail -i jessica@example.com
	From: Jessica Smith <jessica@example.com>
	To: jessica@example.com
	Subject: [PATCH 1/2] added limit to log function
	Date: Sat, 30 May 2009 13:29:15 -0700
	Message-Id: <1243715356-61726-1-git-send-email-jessica@example.com>
	X-Mailer: git-send-email 1.6.2.rc1.20.g8c5b.dirty
	In-Reply-To: <y>
	References: <y>
	
	Result: OK


---

## 5.3 维护项目

### 5.3.1 在特性分支中工作

如果你想向项目中整合一些新东西，最好将这些尝试局限在特性分支——一种通常用来尝试新东西的临时分支中。   

为特性分支创建一个简单的名字，方便找回。也可以添加贡献者名字的缩写。

>
>`$ git branch [sc/ruby_client master]`  [sc/ruby_client master]为分支名称。
>
>`$ git checkout -b sc/ruby_client master` 或者切到新分支上。
>

### 5.3.2 应用来自邮件的补丁

如果你通过电子邮件收到了一个需要整合进入项目的补丁，你需要将其应用到特性分支中进行评估。 有两种应用该种补丁的方法：使用 `git apply`，或者使用 `git am`。

**使用 apply 命令应用补丁**
>
>如果你收到了一个使用 git diff 或 Unix diff 命令（不推荐使用这种方式，具体见下一节）创建的补丁，可以使用 git apply 命令来应用。 假设你将补丁保存在了 /tmp/patch-ruby-client.patch 中，可以这样应用补丁：
>
>		$ git apply /tmp/patch-ruby-client.patch  

>这会修改工作目录中的文件。 它与运行 patch -p1 命令来应用补丁几乎是等效的，但是这种方式更加严格，相对于 patch 来说，它能够接受的模糊匹配更少。 它也能够处理 git diff 格式文件所描述的文件添加、删除和重命名操作，而 patch 则不会。 最后，git apply 命令采用了一种“全部应用，否则就全部撤销（apply all or abort all）”的模型，即补丁只有全部内容都被应用和完全不被应用两个状态，而 patch 可能会导致补丁文件被部分应用，最后使你的工作目录保持在一个比较奇怪的状态。 总体来看，git apply 命令要比 patch 谨慎得多。 并且，它不会为你创建提交——在运行之后，你需要手动暂存并提交补丁所引入的更改。
>
>在实际应用补丁前，你还可以使用 git apply 来检查补丁是否可以顺利应用——即对补丁运行 git apply --check 命令：
>
>		$ git apply --check 0001-seeing-if-this-helps-the-gem.patch
>		error: patch failed: ticgit.gemspec:1
>		error: ticgit.gemspec: patch does not apply
>
>如果没有产生输出，则该补丁可以顺利应用。 如果检查失败了，该命令还会以一个非零的状态退出，所以需要时你也可以在脚本中使用它。
>
>

**使用 am 命令应用补丁**

>如果补丁的贡献者也是一个 Git 用户，并且其能熟练使用 format-patch 命令来生成补丁，这样的话你的工作会变得更加轻松，因为这种补丁中包含了作者信息和提交信息供你参考。 如果可能的话，请鼓励贡献者使用 format-patch 而不是 diff 来为你生成补丁。 而只有对老式的补丁，你才必须使用 git apply 命令。

>要应用一个由 format-patch 命令生成的补丁，你应该使用 git am 命令。 从技术的角度看，git am 是为了读取 mbox 文件而构建的，mbox 是一种用来在单个文本文件中存储一个或多个电子邮件消息的简单纯文本格式。 其大致格式如下所示：
>
>		From 330090432754092d704da8e76ca5c05c198e71a8 Mon Sep 17 00:00:00 2001
>		From: Jessica Smith <jessica@example.com>
>		Date: Sun, 6 Apr 2008 10:17:23 -0700
>		Subject: [PATCH 1/2] add limit to log function
>
>		Limit log functionality to the first 20
>
>这其实就是你前面看到的 `format-patch` 命令输出的开始几行。 而同时它也是有效的 mbox 电子邮件格式。 如果有人使用 `git send-email` 命令将补丁以电子邮件的形式发送给你，你便可以将它下载为 mbox 格式的文件，之后将 `git am` 命令指向该文件，它会应用其中包含的所有补丁。 如果你所使用的邮件客户端能够同时将多封邮件保存为 mbox 格式的文件，你甚至能够将一系列补丁打包为单个 mbox 文件，并利用 `git am` 命令将它们一次性全部应用。

>然而，如果贡献者将 `format-patch` 生成的补丁文件上传到类似 Request Ticket 的任务处理系统，你可以先将其保存到本地，之后通过 git am 来应用补丁：
>
>		$ git am 0001-limit-log-function.patch
>		Applying: add limit to log function
>
>你会看到补丁被顺利地应用，并且为你自动创建了一个新的提交。 其中的作者信息来自于电子邮件头部的 From 和 Date 字段，提交消息则取自 Subject 和邮件正文中补丁之前的内容。 比如，应用上面那个 mbox 示例后生成的提交是这样的：
>
>		$ git log --pretty=fuller -1
>		commit 6c5e70b984a60b3cecd395edd5b48a7575bf58e0
>		Author:     Jessica Smith <jessica@example.com>
>		AuthorDate: Sun Apr 6 10:17:23 2008 -0700
>		Commit:     Scott Chacon <schacon@gmail.com>
>		CommitDate: Thu Apr 9 09:19:06 2009 -0700
>
>   		add limit to log function
>
>   		Limit log functionality to the first 20
>
>其中 Commit 信息表示的是应用补丁的人和应用补丁的时间。 Author 信息则表示补丁的原作者和原本的创建时间。

>但是，有时候无法顺利地应用补丁。 这也许是因为你的主分支和创建补丁的分支相差较多，也有可能是因为这个补丁依赖于其他你尚未应用的补丁。 这种情况下，`git am` 进程将会报错并且询问你要做什么：
>
>		$ git am 0001-seeing-if-this-helps-the-gem.patch
>		Applying: seeing if this helps the gem
>		error: patch failed: ticgit.gemspec:1
>		error: ticgit.gemspec: patch does not apply
>		Patch failed at 0001.
>		When you have resolved this problem run "git am --resolved".
>		If you would prefer to skip this patch, instead run "git am --skip".
>		To restore the original branch and stop patching run "git am --abort".
>
>该命令将会在所有出现问题的文件内加入冲突标记，就和发生冲突的合并或变基操作一样。 而你解决问题的手段很大程度上也是一样的——即手动编辑那些文件来解决冲突，暂存新的文件，之后运行 git am --resolved 继续应用下一个补丁：
>
>		$ (fix the file)
>		$ git add ticgit.gemspec
>		$ git am --resolved
>		Applying: seeing if this helps the gem
>
>如果你希望 Git 能够尝试以更加智能的方式解决冲突，你可以对其传递 `-3` 选项来使 Git 尝试进行三方合并。 该选项默认并没有打开，因为如果用于创建补丁的提交并不在你的版本库内的话，这样做是没有用处的。 而如果你确实有那个提交的话——比如补丁是基于某个公共提交的——那么通常 `-3` 选项对于应用有冲突的补丁是更加明智的选择。
>
>		$ git am -3 0001-seeing-if-this-helps-the-gem.patch
>		Applying: seeing if this helps the gem
>		error: patch failed: ticgit.gemspec:1
>		error: ticgit.gemspec: patch does not apply
>		Using index info to reconstruct a base tree...
>		Falling back to patching base and 3-way merge...
>		No changes -- Patch already applied.
>
>比如上面这种情况，我在之前已经应用过同样的补丁。 如果没有 -3 选项的话，这看起来就像是存在一个冲突。

>如果你正在利用一个 mbox 文件应用多个补丁，也可以在交互模式下运行 am 命令，这样在每个补丁之前，它会停住询问你是否要应用该补丁：
>
>		$ git am -3 -i mbox
>		Commit Body is:
>		-------------------------
>		seeing if this helps the gem
>		--------------------------
>		Apply? [y]es/[n]o/[e]dit/[v]iew patch/[a]ccept all
>
>这在你保存的补丁较多时很好用，因为你可以在应用之前查看忘掉内容的补丁，并且跳过已经应用过的补丁。

>当与你的特性相关的所有补丁都被应用并提交到分支中之后，你就可以选择是否以及如何将其整合到更长期的分支中去了。
>
>
>
>
>


### 5.3.3 检出远程分支

### 5.3.4 确定引入了哪些东西

### 5.3.5 将贡献的工作整合进来

#### 5.3.5.1 大项目合并工作流

#### 5.3.5.2 变基与拣选工作流

### 5.3.6 为发布打标签

### 5.3.7 生成一个构建号

### 5.3.8 准备一次发布

### 5.3.9 制作提交简报


----

# 6. GitHub

## 6.1 账户的创建和配置


----

## 6.2 对项目做出贡献


----

## 6.3 维护项目


----

## 6.4 管理组织


-----

## 6.5 脚本 GitHub