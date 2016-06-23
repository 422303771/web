# laravel 5 学习指南

## 1.部署Laravel Homestead

### 1.1. 必要软件安装

|软件|下载地址|
|:----:|:----:|
|vagrant|[地址](https://www.vagrantup.com/)|
|virtualbox|[地址](https://www.virtualbox.org/)|
|git|[地址](https://git-scm.com/)|

composer 在虚拟机中有提供，但为了能正常安装laravel需要自备**梯子**

Laravel 官方指南[地址](https://laravel.com/docs/5.2/homestead)

### 1.2.下载Laravel Homestead虚拟机文件

Git Bash中输入命令

	$ vagrant box add laravel/homestead		//安装Laravel Homestead

随后会要求选择使用的虚拟机。virtualbox为1 ，Vmware 为2 。

*注意：如果使用VMware需要购买vagrant的插件。*

确认后，开始下载。

*注意：需要梯子*

### 1.3. 安装Laravel Homestead

下载完成后，需要使用git，克隆Laravel Homestead的安装设置文件。

切换到想要建立项目的目录，使用克隆命令。

	$ git clone https://github.com/laravel/homestead.git Homestead


### 1.4. 设置Laravel Homestead

首先需要有SSH密钥，如果没有使用下方命令创建

	$ ssh-keygen

克隆完整后，载入Laravel Homestead设置文件。

	$ bash init.sh   //载入 Laravel Homestead 设置文件

之后设置文件映射，

`C:\Windows\System32\drivers\etc\hosts`  负责DNS的文件

`C:\Users\Keleven\.homestead\Homestead.yaml`   负责Laravel Homestead设置的文件

在`Homestead.yaml`修改

	ip: "192.168.20.10"		//映射的ip地址
	memory: 2048			//虚拟机内存大小
	cpus: 1					//虚拟机CUP数量
	provider: virtualbox	//虚拟机供应商
	
	authorize: ~/.ssh/id_rsa.pub   //SSH 公钥地址
	
	keys:							
	    - ~/.ssh/id_rsa				//SSH 私钥地址
	
	folders:							//项目的主文件夹
	    - map: J:\laravel\web  # pc          //PC端 文件地址
	      to: /home/vagrant/webCode  #vbox    //Vbox端 地址 
	
	sites:
	    - map: web.app                               //网站名称
	      to: /home/vagrant/webCode/web/public       //laravel 入口
	    - map: back.app								 //网站名称
	      to: /home/vagrant/webCode/back/public      //laravel 入口
	databases:                                       // web数据库名称
	    - webdb

在`hosts`中添加

	192.168.20.10 web.app
	192.168.20.10 back.app

*注意：IP：地址用没有使用的IP段*

### 1.5.运行Laravel Homestead

在克隆的的Homestead目录下。启动虚拟机。

**不要再Vbox中启动，不要再Vbox中启动，不要再Vbox中启动。**

	$ vagrant up 

加载完成会就可以使用了，使用下方命令与虚拟机建立SSH链接。

	$ vagrant ssh

进去虚拟机的第一件事，解除Composer的xdebug模式，否则严重影响下载包的速度。

解除方法：

找到`/etc/php/7.0/mods-available/`下的`xdebug.ini`打开后加注释。

	$ sudo nano /etc/php/7.0/mods-available/xdebug.ini  // 使用nano编辑xdebug.ini文件

在`zend_extension=xdebug.so`前方加`;`

对`sudo nano /etc/php/5.6/mods-available/`下的`xdebug.ini`进行同样操作。

	sudo nano /etc/php/5.6/mods-available/xdebug.ini

### 1.6.安装laravel到目录

安装目录在`Homestead.yaml`设置过，本地目录与虚拟机的目录是共享的。

即安装完成后，可以直接修改本地目录中的文件。

使用Git Bash进入项目目录。使用`composer`安装`laravel`

	$ composer create-project --prefer-dist laravel/laravel [blog]

[blog]可以是任意的项目名称。

*注意：项目名称要与`Homestead.yaml`中设置对应*

安装完成后，在浏览器中输入web.app，就可以看到laravel在正常运行了。


### 1.7. 关机

先使用`exit`推出SSH链接。

在关闭虚拟机:

	$ vagrant halt


## 2. 正常安装Laravel

在ubuntu desktop环境下安装。