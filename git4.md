
## 4.服务器上的Git

git 可以使用四种协议来传输资料：本地协议，HTTP协议，SSH协议，Git协议。

### 4.1 协议
#### 4.1.1 本地协议

远程版本库就硬盘内的另一个目录。

>
>`$ git clone /opt/git/project.git`  
>`$ git clone file:///opt/git/project.git`
>
>如果指明`file：//`，Git会触发网络传输，通常传输效率比较低。  
>使用普通路径，速度更快。
>
>要增加一个本地版本库到现有的 Git 项目，可以执行如下的命令
>
>`$ git remote add local_proj /opt/git/project.git`
>
**优点：**  

  设置简单，设置共享文件即可。
  
**缺点：**  

不利于网络访问。  

通过NFS访问速度比SSH访问慢。


#### 4.1.2 HTTP协议

智能（Smart） HTTP 协议

“智能” HTTP 协议的运行方式和 SSH 及 Git 协议类似，只是运行在标准的 HTTP/S 端口上并且可以使用各种 HTTP 验证机制，这意味着使用起来会比 SSH 协议简单的多，比如可以使用 HTTP 协议的用户名／密码的基础授权，免去设置 SSH 公钥。  

哑（Dumb） HTTP 协议

如果服务器没有提供智能 HTTP 协议的服务，Git 客户端会尝试使用更简单的“哑” HTTP 协议。 哑 HTTP 协议里 web 服务器仅把裸版本库当作普通文件来对待，提供文件服务。 哑 HTTP 协议的优美之处在于设置起来简单。 基本上，只需要把一个裸版本库放在 HTTP 跟目录，设置一个叫做 post-update 的挂钩就可以了（见 Git 钩子）。 此时，只要能访问 web 服务器上你的版本库，就可以克隆你的版本库。 下面是设置从 HTTP 访问版本库的方法

	$ cd /var/www/htdocs/
	$ git clone --bare /path/to/git_project gitproject.git
	$ cd gitproject.git
	$ mv hooks/post-update.sample hooks/post-update
	$ chmod a+x hooks/post-update	

这样就可以了。 Git 自带的 post-update 挂钩会默认执行合适的命令（git update-server-info），来确保通过 HTTP 的获取和克隆操作正常工作。 这条命令会在你通过 SSH 向版本库推送之后被执行；然后别人就可以通过类似下面的命令来克隆

	$ git clone https://example.com/gitproject.git

**优点：**

不同的访问方式只需要一个 URL 以及服务器只在需要授权时提示输入授权信息，这两个简便性让终端用户使用 Git 变得非常简单。

HTTP/S 协议被广泛使用，一般的企业防火墙都会允许这些端口的数据通过。

**缺点：**

在一些服务器上，架设 HTTP/S 协议的服务端会比 SSH 协议的棘手一些。 除了这一点，用其他协议提供 Git 服务与 “智能” HTTP 协议相比就几乎没有优势了。

#### 4.1.3 SSH协议

架设 Git 服务器时常用 SSH 协议作为传输协议。 因为大多数环境下已经支持通过 SSH 访问   

通过 SSH 协议克隆版本库，你可以指定一个 ssh:// 的 URL  

	$ git clone ssh://user@server/project.git

或者使用一个简短的 scp 式的写法 

	$ git clone user@server:project.git

**优势：**

用 SSH 协议的优势有很多。 首先，SSH 架设相对简单 —— SSH 守护进程很常见，多数管理员都有使用经验，并且多数操作系统都包含了它及相关的管理工具。 其次，通过 SSH 访问是安全的 —— 所有传输数据都要经过授权和加密。 最后，与 HTTP/S 协议、Git 协议及本地协议一样，SSH 协议很高效，在传输前也会尽量压缩数据。

**缺点**

SSH 协议的缺点在于你不能通过他实现匿名访问。 即便只要读取数据，使用者也要有通过 SSH 访问你的主机的权限，这使得 SSH 协议不利于开源的项目。 如果你只在公司网络使用，SSH 协议可能是你唯一要用到的协议。 如果你要同时提供匿名只读访问和 SSH 协议，那么你除了为自己推送架设 SSH 服务以外，还得架设一个可以让其他人访问的服务。

#### 4.1.4 Git协议

接下来是 Git 协议。 这是包含在 Git 里的一个特殊的守护进程；它监听在一个特定的端口（9418），类似于 SSH 服务，但是访问无需任何授权。 要让版本库支持 Git 协议，需要先创建一个 git-daemon-export-ok 文件 —— 它是 Git 协议守护进程为这个版本库提供服务的必要条件 —— 但是除此之外没有任何安全措施。 要么谁都可以克隆这个版本库，要么谁也不能。 这意味这，通常不能通过 Git 协议推送。 由于没有授权机制，一旦你开放推送操作，意味着网络上知道这个项目 URL 的人都可以向项目推送数据。 不用说，极少会有人这么做。

**优点：**

目前，Git 协议是 Git 使用的网络传输协议里最快的。 如果你的项目有很大的访问量，或者你的项目很庞大并且不需要为写进行用户授权，架设 Git 守护进程来提供服务是不错的选择。 它使用与 SSH 相同的数据传输机制，但是省去了加密和授权的开销。

**缺点：**

Git 协议缺点是缺乏授权机制。 把 Git 协议作为访问项目版本库的唯一手段是不可取的。 一般的做法里，会同时提供 SSH 或者 HTTPS 协议的访问服务，只让少数几个开发者有推送（写）权限，其他人通过 git:// 访问只有读权限。 Git 协议也许也是最难架设的。 它要求有自己的守护进程，这就要配置 xinetd 或者其他的程序，这些工作并不简单。 它还要求防火墙开放 9418 端口，但是企业防火墙一般不会开放这个非标准端口。 而大型的企业防火墙通常会封锁这个端口。

### 4.2 在服务器上搭建Git

在开始架设 Git 服务器前，需要把现有仓库导出为裸仓库——即一个不包含当前工作目录的仓库。 这通常是很简单的。 为了通过克隆你的仓库来创建一个新的裸仓库，你需要在克隆命令后加上 --bare选项 按照惯例，裸仓库目录名以 .git 结尾，就像这样

	$ git clone --bare my_project my_project.git
	Cloning into bare repository 'my_project.git'...
	done.

现在，你的 my_project.git 目录中应该有 Git 目录的副本了。

整体上效果大致相当于

	$ cp -Rf my_project/.git my_project.git

虽然在配置文件中有若干不同，但是对于你的目的来说，这两种方式都是一样的。 它只取出 Git 仓库自身，不要工作目录，然后特别为它单独创建一个目录。

#### 4.2.1 把裸仓库放到服务器上

既然你有了裸仓库的副本，剩下要做的就是把裸仓库放到服务器上并设置你的协议。 假设一个域名为 `git.example.com` 的服务器已经架设好，并可以通过 SSH 连接，你想把所有的 Git 仓库放在 `/opt/git` 目录下。 假设服务器上存在` /opt/git/` 目录，你可以通过以下命令复制你的裸仓库来创建一个新仓库

	$ scp -r my_project.git user@git.example.com:/opt/git

此时，其他通过 SSH 连接这台服务器并对 /opt/git 目录拥有可读权限的使用者，通过运行以下命令就可以克隆你的仓库。

	$ git clone user@git.example.com:/opt/git/my_project.git

如果一个用户，通过使用 SSH 连接到一个服务器，并且其对 `/opt/git/my_project.git` 目录拥有可写权限，那么他将自动拥有推送权限。

如果到该项目目录中运行 `git init `命令，并加上 `--shared` 选项，那么 Git 会自动修改该仓库目录的组权限为可写。

	$ ssh user@git.example.com
	$ cd /opt/git/my_project.git
	$ git init --bare --shared

由此可见，根据现有的 Git 仓库创建一个裸仓库，然后把它放上你和协作者都有 SSH 访问权的服务器是多么容易。 现在你们已经准备好在同一项目上展开合作了。

值得注意的是，这的确是架设一个几个人拥有连接权的 Git 服务的全部——只要在服务器上加入可以用 SSH 登录的帐号，然后把裸仓库放在大家都有读写权限的地方。 你已经准备好了一切，无需更多。

下面的几节中，你会了解如何扩展到更复杂的设定。 这些内容包含如何避免为每一个用户建立一个账户，给仓库添加公共读取权限，架设网页界面等等。 然而，请记住这一点，如果只是和几个人在一个私有项目上合作的话，仅仅 是一个 SSH 服务器和裸仓库就足够了。

#### 4.2.2 小型安装

SSH 连接
如果你有一台所有开发者都可以用 SSH 连接的服务器，架设你的第一个仓库就十分简单了，因为你几乎什么都不用做（正如我们上一节所说的）。 如果你想在你的仓库上设置更复杂的访问控制权限，只要使用服务器操作系统的普通的文件系统权限就行了。

如果需要团队里的每个人都对仓库有写权限，又不能给每个人在服务器上建立账户，那么提供 SSH 连接就是唯一的选择了。 我们假设用来共享仓库的服务器已经安装了 SSH 服务，而且你通过它访问服务器。

有几个方法可以使你给团队每个成员提供访问权。 第一个就是给团队里的每个人创建账号，这种方法很直接但也很麻烦。 或许你不会想要为每个人运行一次 `adduser` 并且设置临时密码。

第二个办法是在主机上建立一个 git 账户，让每个需要写权限的人发送一个 SSH 公钥，然后将其加入 git 账户的 `~/.ssh/authorized_keys` 文件。 这样一来，所有人都将通过 git 账户访问主机。 这一点也不会影响提交的数据——访问主机用的身份不会影响提交对象的提交者信息。

另一个办法是让 SSH 服务器通过某个 LDAP 服务，或者其他已经设定好的集中授权机制，来进行授权。 只要每个用户可以获得主机的 shell 访问权限，任何 SSH 授权机制你都可视为是有效的。

### 4.3 生成SSH公钥

 默认情况下，用户的 SSH 密钥存储在其 ~/.ssh 目录下。 进入该目录并列出其中内容，你便可以快速确认自己是否已拥有密钥

	$ cd ~/.ssh
	$ ls
	authorized_keys2  id_dsa       known_hosts
	config            id_dsa.pub

我们需要寻找一对以 id_dsa 或 id_rsa 命名的文件，其中一个带有 .pub 扩展名。 .pub 文件是你的公钥，另一个则是私钥。 如果找不到这样的文件（或者根本没有 .ssh 目录），你可以通过运行 ssh-keygen 程序来创建它们。在 Linux/Mac 系统中，ssh-keygen 随 SSH 软件包提供；在 Windows 上，该程序包含于 MSysGit 软件包中

	$ ssh-keygen
	Generating public/private rsa key pair.
	Enter file in which to save the key (/home/schacon/.ssh/id_rsa):
	Created directory '/home/schacon/.ssh'.
	Enter passphrase (empty for no passphrase):
	Enter same passphrase again:
	Your identification has been saved in /home/schacon/.ssh/id_rsa.
	Your public key has been saved in /home/schacon/.ssh/id_rsa.pub.
	The key fingerprint is:
	d0:82:24:8e:d7:f1:bb:9b:33:53:96:93:49:da:9b:e3 schacon@mylaptop.local

首先 ssh-keygen 会确认密钥的存储位置（默认是 .ssh/id_rsa），然后它会要求你输入两次密钥口令。如果你不想在使用密钥时输入口令，将其留空即可。

现在，进行了上述操作的用户需要将各自的公钥发送给任意一个 Git 服务器管理员（假设服务器正在使用基于公钥的 SSH 验证设置）。 他们所要做的就是复制各自的 .pub 文件内容，并将其通过邮件发送。 公钥看起来是这样的：

	$ cat ~/.ssh/id_rsa.pub
	ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAklOUpkDHrfHY17SbrmTIpNLTGK9Tjom/BWDSU
	GPl+nafzlHDTYW7hdI4yZ5ew18JH4JW9jbhUFrviQzM7xlELEVf4h9lFX5QVkbPppSwg0cda3
	Pbv7kOdJ/MTyBlWXFCR+HAo3FXRitBqxiX1nKhXpHAZsMciLq8V6RjsNAQwdsdMFvSlVK/7XA
	t3FaoJoAsncM1Q9x5+3V0Ww68/eIFmb1zuUFljQJKprrX88XypNDvjYNby6vw/Pb0rwert/En
	mZ+AW4OZPnTPI89ZPmVMLuayrD2cE86Z/il8b+gw3r3+1nKatmIkjn2so1d01QraTlMqVSsbx
	NrRFi9wrf+M7Q== schacon@mylaptop.local

### 4.4 配置服务器

我们来看看如何配置服务器端的 SSH 访问。 本例中，我们将使用 authorized_keys 方法来对用户进行认证。 同时我们假设你使用的操作系统是标准的 Linux 发行版，比如 Ubuntu。 首先，创建一个操作系统用户 git，并为其建立一个 .ssh 目录

	$ sudo adduser git
	$ su git
	$ cd
	$ mkdir .ssh && chmod 700 .ssh
	$ touch .ssh/authorized_keys && chmod 600 .ssh/authorized_keys

接着，我们需要为系统用户 git 的 authorized_keys 文件添加一些开发者 SSH 公钥。 假设我们已经获得了若干受信任的公钥，并将它们保存在临时文件中。 与前文类似，这些公钥看起来是这样的：

	$ cat /tmp/id_rsa.john.pub
	ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCB007n/ww+ouN4gSLKssMxXnBOvf9LGt4L
	ojG6rs6hPB09j9R/T17/x4lhJA0F3FR1rP6kYBRsWj2aThGw6HXLm9/5zytK6Ztg3RPKK+4k
	Yjh6541NYsnEAZuXz0jTTyAUfrtU3Z5E003C4oxOj6H0rfIF1kKI9MAQLMdpGW1GYEIgS9Ez
	Sdfd8AcCIicTDWbqLAcU4UpkaX8KyGlLwsNuuGztobF8m72ALC/nLF6JLtPofwFBlgc+myiv
	O7TCUSBdLQlgMVOFq1I2uPWQOkOWQAHukEOmfjy2jctxSDBQ220ymjaNsHT4kgtZg2AYYgPq
	dAv8JggJICUvax2T9va5 gsg-keypair

将这些公钥加入系统用户 git 的 .ssh 目录下 authorized_keys 文件的末尾：

	$ cat /tmp/id_rsa.john.pub >> ~/.ssh/authorized_keys
	$ cat /tmp/id_rsa.josie.pub >> ~/.ssh/authorized_keys
	$ cat /tmp/id_rsa.jessica.pub >> ~/.ssh/authorized_keys

现在我们来为开发者新建一个空仓库。可以借助带 --bare 选项的 git init 命令来做到这一点，该命令在初始化仓库时不会创建工作目录：

	$ cd /opt/git
	$ mkdir project.git
	$ cd project.git
	$ git init --bare
	Initialized empty Git repository in /opt/git/project.git/

接着，John、Josie 或者 Jessica 中的任意一人可以将他们项目的最初版本推送到这个仓库中，他只需将此仓库设置为项目的远程仓库并向其推送分支。 请注意，每添加一个新项目，都需要有人登录服务器取得 shell，并创建一个裸仓库。 我们假定这个设置了 git 用户和 Git 仓库的服务器使用 gitserver 作为主机名。 同时，假设该服务器运行在内网，并且你已在 DNS 配置中将 gitserver 指向此服务器。那么我们可以运行如下命令（假定 myproject 是已有项目且其中已包含文件）

	# on John's computer
	$ cd myproject
	$ git init
	$ git add .
	$ git commit -m 'initial commit'
	$ git remote add origin git@gitserver:/opt/git/project.git
	$ git push origin master

此时，其他开发者可以克隆此仓库，并推回各自的改动，步骤很简单：

	$ git clone git@gitserver:/opt/git/project.git
	$ cd project
	$ vim README
	$ git commit -am 'fix for the README file'
	$ git push origin master

通过这种方法，你可以快速搭建一个具有读写权限、面向多个开发者的 Git 服务器。

需要注意的是，目前所有（获得授权的）开发者用户都能以系统用户 git 的身份登录服务器从而获得一个普通 shell。 如果你想对此加以限制，则需要修改 passwd 文件中（git 用户所对应）的 shell 值。

借助一个名为 git-shell 的受限 shell 工具，你可以方便地将用户 git 的活动限制在与 Git 相关的范围内。该工具随 Git 软件包一同提供。 如果将 git-shell 设置为用户 git 的登录 shell（login shell），那么用户 git 便不能获得此服务器的普通 shell 访问权限。 若要使用 git-shell，需要用它替换掉 bash 或 csh，使其成为系统用户的登录 shell。 为进行上述操作，首先你必须确保 git-shell 已存在于 /etc/shells 文件中：

	$ cat /etc/shells   # see if `git-shell` is already in there.  If not...
	$ which git-shell   # make sure git-shell is installed on your system.
	$ sudo vim /etc/shells  # and add the path to git-shell from last command

现在你可以使用 chsh <username> 命令修改任一系统用户的 shell：

	$ sudo chsh git  # and enter the path to git-shell, usually: /usr/bin/git-shell

这样，用户 git 就只能利用 SSH 连接对 Git 仓库进行推送和拉取操作，而不能登录机器并取得普通 shell。 如果试图登录，你会发现尝试被拒绝，像这样：

	$ ssh git@gitserver
	fatal: Interactive git shell is not enabled.
	hint: ~/git-shell-commands should exist and have read and execute access.
	Connection to gitserver closed.

现在，网络相关的 Git 命令依然能够正常工作，但是开发者用户已经无法得到一个普通 shell 了。 正如输出信息所提示的，你也可以在 git 用户的家目录下建立一个目录，来对 git-shell 命令进行一定程度的自定义。 比如，你可以限制掉某些本应被服务器接受的 Git 命令，或者对刚才的 SSH 拒绝登录信息进行自定义，这样，当有开发者用户以类似方式尝试登录时，便会看到你的信息。 要了解更多有关自定义 shell 的信息，请运行 git help shell

---

### 4.5 Git守护进程

详细信息阅读[文档](https://git-scm.com/book/zh/v2/%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git-Git-%E5%AE%88%E6%8A%A4%E8%BF%9B%E7%A8%8B)

git协议并不常用

---

### 4.6 Smart HTTP


详细信息阅读[文档](https://git-scm.com/book/zh/v2/%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git-Smart-HTTP)

不常用

---

### 4.7 GitWeb

如果你对项目有读写权限或只读权限，你可能需要建立起一个基于网页的简易查看器。 Git 提供了一个叫做 GitWeb 的 CGI 脚本来做这项工作。

> **GitWeb 的网页用户界面**
>
>![](https://git-scm.com/book/en/v2/book/04-git-server/images/git-instaweb.png)
>
>如果你想要查看 GitWeb 如何展示你的项目，并且在服务器上安装了轻量级网络服务器比如 `lighttpd` 或 `webrick`， Git 提供了一个命令来让你启动一个临时的服务器。 在 Linux 系统的电脑上，`lighttpd` 通常已经安装了，所以你只需要在项目目录里执行 `git instaweb` 命令即可。 如果你使用 Mac 系统， Mac OS X Leopard 系统已经预安装了 Ruby，所以 `webrick` 或许是你最好的选择。 如果不想使用 lighttpd 启动 `instaweb` 命令，你需要在执行时加入 `--httpd` 参数
>
>		$ git instaweb --httpd=webrick
>		[2009-02-21 10:02:21] INFO  WEBrick 1.3.1
>		2009-02-21 10:02:21] INFO  ruby 1.8.6 (2008-03-03) [universal-darwin9.0]
>
>这个命令启动了一个监听 1234 端口的 HTTP 服务器，并且自动打开了浏览器。 这对你来说十分方便。 当你已经完成了工作并想关闭这个服务器，你可以执行同一个命令，并加上 `--stop` 选项.   
> 
>`$ git instaweb --httpd=webrick --stop`
>

#

>如果你现在想为你的团队或你托管的开源项目持续的运行这个页面，你需要通过普通的 Web 服务器来设置 CGI 脚本。 一些 Linux 发行版的软件库有 gitweb 包，可以通过 apt 或 yum 来安装，你可以先试试。 接下来我们来快速的了解一下如何手动安装 GitWeb。 首先，你需要获得 Git 的源代码，它包含了 GitWeb ，并可以生成自定义的 CGI 脚本
>
>		$ git clone git://git.kernel.org/pub/scm/git/git.git
>		$ cd git/
>		$ make GITWEB_PROJECTROOT="/opt/git" prefix=/usr gitweb
>  		  SUBDIR gitweb
>   	  SUBDIR ../
>		make[2]: `GIT-VERSION-FILE' is up to date.
>   	  GEN gitweb.cgi
> 		  GEN static/gitweb.js
>		$ sudo cp -Rf gitweb /var/www/
>
>需要注意的是，你需要在命令中指定` GITWEB_PROJECTROOT `变量来让程序知道你的 Git 版本库的位置。 现在，你需要在 Apache 中使用这个 CGI 脚本，你需要为此添加一个虚拟主机
>
>		<VirtualHost *:80>
>  			  ServerName gitserver
>  			  DocumentRoot /var/www/gitweb
>   			 <Directory /var/www/gitweb>
>   			     Options ExecCGI +FollowSymLinks +SymLinksIfOwnerMatch
>   			     AllowOverride All
>   			     order allow,deny
>  			    	 Allow from all
>  				     AddHandler cgi-script cgi
>  			 	     DirectoryIndex gitweb.cgi
> 		     </Directory>
>		</VirtualHost>
>
>再次提醒，GitWeb 可以通过任何一个支持 CGI 或 Perl 的网络服务器架设；如果你需要的话，架设起来应该不会很困难。 现在，你可以访问 `http://gitserver/` 在线查看你的版本库
>
>DNS 已经设置的情况下。
>


----


### 4.8 GitLab

详细内容见[文档](https://git-scm.com/book/zh/v2/%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git-GitLab)

### 4.9 第三方托管的选择

详细内容见[文档](https://git-scm.com/book/zh/v2/%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git-%E7%AC%AC%E4%B8%89%E6%96%B9%E6%89%98%E7%AE%A1%E7%9A%84%E9%80%89%E6%8B%A9)