# git使用说明 

[git官方文档](https://git-scm.com/book/en/v2)

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

## 2. Git基础

### 2.1 初始化

创建git管理项目，目录下输入:

>		$ git init 

从其他地方克隆,[url]为项目的网络地址,[name]为项目本地化后的名字：

>		$ git clone [url]
>		$ git clone [url] [name]

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