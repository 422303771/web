# git使用说明 

[git官方文档](https://git-scm.com/book/en/v2)

git GUI乱码修正  
>`$ git config --global gui.encoding utf-8`  

参考文档[地址](https://segmentfault.com/a/1190000000578037)

## 1. git安装与设定  

>安装好git后，需要对个git进行设定。  

使用git自带工具 **git config** 设定外观与行为。  

### 1.1. 设定用户名与电子邮箱地址
>`$ git config --global user.name "John Doe"`  
>`$ git config --global user.email johndoe@example.com`

**注意：  
`--global`是全系统变量。  
如果不想使用，可以在项目目录下，不加`--globar`使用。  
用户名与电子邮件只对当前项目有限。**  

### 1.2. 设定git的文本编辑器   

>`$ git config --global core.editor emacs`  

### 1.3. 检查配置信息  

使用命令
>`git config --list`  

如果检查特定的设置，输入设置名称。
  
例如：   

>		$ git config user.name
>		John Doe

### 1.4.帮助

git获取帮助方法，<verb>为命令名。  

>		$ git help <verb>
>		$ git <verb> --help
>		$ man git-<verb>


----

## 2. Git基础

### 2.1 初始化

创建git管理项目，目录下输入:

>		$ git init 

从其他地方克隆,[url]为项目的网络地址,[name]为项目本地化后的名字：

>		$ git clone [url]
>		$ git clone [url] [name]


---

### 2.2 更新到仓库

#### 2.2.1 查看当前文件状态

>		$ git status

`Untracked files`未添加文件。

跟踪新文件,[new]为文件名。当[new]为目录地址时，暂存目录下全部文件。

>		$ git add [new]

`Changes to be committed`当前文件处于暂存状态。  

`Changes not staged for commit `当前文件发生变化，且没有放入缓存区。  
  
运行`git add`后如果再次修改文件，需要再次运行`git add`

>		$ git status -s
>		 M README
>		MM Rakefile
>		A  lib/git.rb
>		M  lib/simplegit.rb
>		?? LICENSE.txt

**说明：**

* 新添加的未跟踪文件前面有 ?? 标记  
* 新添加到暂存区中的文件前面有 A 标记  
* 修改过的文件前面有 M 标记  

	* 出现在右边的 M 表示该文件被修改了但是还没放入暂存区
	* 出现在靠左边的 M 表示该文件被修改了并放入了暂存区

例如，上面的状态报告显示：

	README 文件在工作区被修改了但是还没有将修改后的文件放入暂存区。

	lib/simplegit.rb 文件被修改了并将修改后的文件放入了暂存区。 

	Rakefile 在工作区被修改并提交到暂存区后又在工作区中被修改了，所以在暂存区和工作区都有该文件被修改了的记录。




#### 2.2.2 忽略文件

创建一个文件`.gitignore`，文件用于记录要忽略的文件。

例子：
	

> 		# no .a files  //禁止.a文件
> 		*.a
> 
>		# but do track lib.a, even though you're ignoring .a files above    //加（！）的为例外。
>		!lib.a
>
>		# only ignore the TODO file in the current directory, not subdir/TODO   //忽略当前目录的TODO文件
>		/TODO
>
>		# ignore all files in the build/ directory		   	//忽略buidl文件夹下的全部文件
>		build/
>
>		# ignore doc/notes.txt, but not doc/server/arch.txt  //忽略文件夹下的全部.txt,子目录下不受影响
>		doc/*.txt
>
>		# ignore all .pdf files in the doc/ directory		//忽略文件夹下全部.pdf文件，子目录下就受影响
>		doc/**/*.pdf
>
>		*.[oa]     //全部的.o或.a结尾的文件

# 
**其他：**

		星号（*）匹配零个或多个任意字符；

		[abc] 匹配任何一个列在方括号中的字符（这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）；

		问号（?）只匹配一个任意字符；	

		如果在方括号中使用短划线（-）分隔两个字符，表示所有在这两个字符范围内的都可以匹配（比如 [0-9] 表示匹配所有 0 到 9 的数字）。 

		使用两个星号（*) 表示匹配任意中间目录，比如a/**/z 可以匹配 a/z, a/b/z 或 a/b/c/z等。

#### 2.2.3 查看已暂存和未暂存的修改

查看命令`$ git diff` 区别于`$ git status`  

`$ git diff`可以查看详细的文档变化，仅能查看到没有添加到缓存之前的（git add 前）。  

`$ git diff`直接输入查询到的是 **修改之后还没有暂存**。  

若要查看已暂存的将要添加到下次提交里的内容，可以说使用`git diff --cached`与`git diff --staged`。  

两个命令的效果相同。   

> `$ git statu` 查看暂存前后的两个版本  
> `$ git diif` 查看暂存前后的变化  
> `$ git diff --cached` 查看查看已经暂存起来的变化

#### 2.2.4 提交更新 

使用命令`$ git commit`  

要提交的是已经`$ git add`过的文件。

每次准备提交前，先用 `$ git status `看下，是不是都已暂存起来了， 然后再运行提交命令 `$ git commit`

> `$ git commit` 弹出默认文本编辑器，用来显示文件信息。  
> 退出编辑器时，Git 会丢掉注释行，用你输入提交附带信息生成一次提交。  

也可以使用`$ git commit -m "[注释]" `，在[注释]中添加内容。

**例子：**

		$ git commit -m "Story 182: Fix benchmarks for speed"  
		[master 463dc4f] Story 182: Fix benchmarks for speed  
 		2 files changed, 2 insertions(+)  
 		create mode 100644 README  

[master 463dc4f] 为文件保存的分支，与SHA-1的校验码。

#### 2.2.5 跳过使用暂存区域

不使用`$ git add`,而直接提交，在`$ git commit `后加`-a`。需要先使用`$ git status`跟踪 
> `$ git status`  
> `$ git commit -a -m "[注释]"`

#### 2.2.6 移除文件
1. 彻底删除文件（包括版本库与本地文件）  
> 先使用`rm [文件]`删除文件  
> 再运行`git rm [文件]`记录删除文件操作。  
> `-f`为强制删除，用于删除修改后没有提交到暂存区的文件。
	
2. 仅从git中删除（保留本地文件。例如没有用的编辑文件）
>  `$ git rm --cached [文件名]`  
>  `git rm log/\*.log`  
>
> 注意到星号 * 之前的反斜杠 \， 因为 Git 有它自己的文件模式扩展匹配方式，所以我们 shell 来帮忙展开。 此命令删除 log/ 目录下扩展名为 .log 的所有文件。
>
> `$ git rm \*~`  
> 该命令为删除以 ~ 结尾的所有文件。

#### 2.2.7 移动文件（从命名文件）

> `$ git mv [原文件名] [新文件名]`  
> **例如：**
>
>		$ git mv README.md README  
>		$ git status  
>		On branch master  
>		Changes to be committed:  
> 			(use "git reset HEAD <file>..." to unstage)  
>
>    	renamed:    README.md -> README  
> 上方为重命名操作  


---

### 2.3 查看提交历史

#### 2.3.1 查看提交历史

使用命令`$ git log`

**例子**
>`$ git log -p -2`  
>`-p`用来显示每次提交的内容差异  
>`-2`用来仅显示最近两次提交  
>`--stat`用来显示每次提交的简略信息。每条信息下方有简略的终结。 
> 
>`--pretty`用来指定不同的显示格式  
>>通常使用`git log --pretty=oneline`  
>>`git log --pretty=short`  
>>`git log --pretty=full`  
>>`git log --pretty=fuller`
>>
>>`=format`最有用  
>>如`$ git log --pretty=format:"%h - %an, %ar : %s"`    
>>
>>|命令|说明|  
>>|:----:|:----:|  
>>|%H|提交对象（commit）的完整哈希字串|    
>>|%h|提交对象的简短哈希字串|  
>>|%T|树对象（tree）的完整哈希字串|  
>>|%t|树对象的简短哈希字串|  
>>|%P|父对象（parent）的完整哈希字串|  
>>|%p|父对象的简短哈希字串|  
>>|%an|作者（author）的名字|  
>>|%ae|作者的电子邮件地址|  
>>|%ad|作者修订日期（可以用 --date= 选项定制格式）|  
>>|%ar|作者修订日期，按多久以前的方式显示|  
>>|%cn|提交者(committer)的名字|  
>>|%ce|提交者的电子邮件地址|  
>>|%cd|提交日期|  
>>|%cr|提交日期，按多久以前的方式显示|  
>>|%s|提交说明|  

>>作者指的是实际作出修改的人，提交者指的是最后将此工作成果提交到仓库的人。  
>
>`--graph`与`git log --pretty=format`一同使用时，有ASCII字符串来形象地展示分支、合并历史。  

----
> 关于一些`git log`的参数说明  
>
>|参数|说明|  
>|:----:|:----:|  
>|-p|按补丁格式显示每个更新之间的差异。|  
>|--stat|显示每次更新的文件修改统计信息。|  
>|--shortstat|只显示 --stat 中最后的行数修改添加移除统计。|  
>|--name-only|仅在提交信息后显示已修改的文件清单。|  
>|--name-status|显示新增、修改、删除的文件清单。|  
>|--abbrev-commit|仅显示 SHA-1 的前几个字符，而非所有的 40 个字符。|  
>|--relative-date|使用较短的相对时间显示（比如，“2 weeks ago”）。|  
>|--graph|显示 ASCII 图形表示的分支合并历史。|  
>|--pretty|使用其他格式显示历史提交信息。可用的选项包括 oneline，short，full，fuller 和 format（后跟指定格式）。|  

  
#### 2.3.2 限制输出长度

>`git log --since=2.weeks`显示2周内的提交。  
>`$ git log -S[function_name]`[ ]中为要查找的特定内容，如函数名。 
>
>|参数|说明|  
>|:----:|:----:|  
>|-(n)|仅显示最近的 n 条提交|  
>|--since, --after|仅显示指定时间之后的提交。|  
>|--until, --before|仅显示指定时间之前的提交。|  
>|--author|仅显示指定作者相关的提交。|  
>|--committer|仅显示指定提交者相关的提交。|  
>|--grep|仅显示含指定关键字的提交|  
>|-S|仅显示添加或移除了某个关键字的提交|  

---

### 2.4 撤消操作

> `$ git commit --amend` 提交遗漏的文件  
>
> **例子：**
>
>		$ git commit -m 'initial commit'  
>		$ git add forgotten_file  
>		$ git commit --amend  
> *说明：最终只有一个提交 - 第二次提交将代替第一次提交的结果。*

#### 2.4.1 取消暂存的文件

> `$ git reset HEAD [文件名]` 取消暂存的文件  
>
> **例子：**
>
>		$ git add *
>		$ git status
>		On branch master
>		Changes to be committed:
>  			(use "git reset HEAD <file>..." to unstage)
>
>   		 renamed:    README.md -> README
>   		 modified:   CONTRIBUTING.md
>
> *说明：不小心将文件添加多了，要将多余文件移除暂存区*	
>
>		$ git reset HEAD CONTRIBUTING.md  
>		Unstaged changes after reset:  
>		M	CONTRIBUTING.md
>		$ git status
>		On branch master
>		Changes to be committed:
>		  (use "git reset HEAD <file>..." to unstage)
>
>	  		  renamed:    README.md -> README
>
>		Changes not staged for commit:
>		  (use "git add <file>..." to update what will be committed)
>		  (use "git checkout -- <file>..." to discard changes in working directory)
>
>	    modified:   CONTRIBUTING.md
> *说明：已经将多余文件移除暂存区*

#### 2.4.2 撤消对文件的修改

> `$ git checkout -- [文件名]` 撤销到上次提交。  

---

### 2.5 远程仓库的使用

#### 2.5.1 查看远程仓库

> `$ git remote `命令，列出远程服务器的简写。  
>
> `$ git remote -v` 列出远程仓库的简写与对应的URL地址。


#### 2.5.2 添加远程仓库

> ` git remote add [shortname] [url]`[shortname] 为仓库名简写 ，[url]为仓库地址。  
>
> `$ git fetch [仓库简写]`拉取仓库信息  

#### 2.5.3 从远程仓库中抓取与拉取

> `$ git fetch [仓库简写]`访问远程仓库，从中拉取所有还没有的数据  
>
> 如果使用`clone`命令克隆一个仓库，命令会自动添加远程仓库并默认仓库简写为`origin`。  
> `git fetch origin` 会抓取克隆（或上一次抓取）后新推送的所有工作。  
> `git fetch origin` 不会合并或修改当前工作，需要手动合并。
>
> `git pull` 命令来自动的抓取然后合并远程分支到当前分支。  
> *注意：需要一个分支设置为跟踪一个远程分支*

#### 2.5.4 推送到远程仓库

> `git push [仓库简写] [分支名称]` 推送分支
>
> 推送时要确保分支为最新的，之后才可以推送

#### 2.5.5 查看远程仓库

> `$ git remote show [仓库简写]`  命令会列出远程仓库的 URL 与跟踪分支的信息。

#### 2.5.6 远程仓库的移除与重命名

> `git remote rename [旧名字] [新名字]` 重命名远程仓库
>
> `git remote rm [仓库简写]`删除远程仓库  

---

### 2.6 打标签 

#### 2.6.1 列出标签

> `$ git tag` 列出标签
> `$ git tag -l 'v1.8.5*'`显示特定版本的标签

#### 2.6.2 创建标签
Git 使用两种主要类型的标签：轻量标签（lightweight）与附注标签（annotated）  

> **附注标签**  
>
> `$ git tag -a v1.4 -m 'my version 1.4'`  
>
> 在运行`git tag`时加`-a`
>
> `-m`指定一条储存在标签中的信息
>
> 使用 `git show` 命令可以看到标签信息与对应的提交信息
>
>**例子：**
>
>		$ git tag -a v1.4 -m 'my version 1.4'
>		$ git tag
>		v0.1
>		v1.3
>		v1.4
>
> 创建带信息的标签  
>
>		$ git show v1.4
>		tag v1.4
>		Tagger: Ben Straub <ben@straub.cc>
>		Date:   Sat May 3 20:19:12 2014 -0700
>
>		my version 1.4
>
>		commit ca82a6dff817ec66f44342007202690a93763949
>		Author: Scott Chacon <schacon@gee-mail.com>
>		Date:   Mon Mar 17 21:52:11 2008 -0700
>
>    		changed the version number
>
> 查看带信息的标签

---

> **轻量标签**
>
> `$ git tag [v1.4-lw]`  [ ]中为轻量化标签,不储存额外的信息  
>
> `$ git show [v1.4-lw]` 显示[ ]标签信息
> 

---

#### 2.6.3 后期打标签

> 先查看记录   
>
> `$ git log --pretty=oneline`
> 
> `$ git tag -a [v1.2] [9fceb02]` 打标签时，加入部分校验码。[ ]中为标签与校验码。

#### 2.6.4 共享标签

> 需要使用命令推送  
>
> `$ git push [origin] [v1.5]`  [v1.5]中为标签, [origin]为仓库简写
>
> 如果推送多个
> `$ git push [origin] --tags` [origin]为仓库简写

#### 2.6.4 检出标签

> 使工作目录与仓库中特定的标签版本完全一样  
>  
> `$ git checkout -b [version2] [v2.0.0]` [version2]为分支名称， [v2.0.0]为标签名称。  
>
> *如果在这之后又进行了一次提交，version2 分支会因为改动向前移动了，那么 version2 分支就会和 v2.0.0 标签稍微有些不同，这时就应该当心了。*

---
