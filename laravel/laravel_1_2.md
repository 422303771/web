<!-- toc -->

- [laravel 5 学习指南](#laravel-5-学习指南)
	- [1.部署Laravel Homestead](#1部署laravel-homestead)
		- [1.1\. 必要软件安装](#11-必要软件安装)
		- [1.2.下载Laravel Homestead虚拟机文件](#12下载laravel-homestead虚拟机文件)
		- [1.3\. 安装Laravel Homestead](#13-安装laravel-homestead)
		- [1.4\. 设置Laravel Homestead](#14-设置laravel-homestead)
		- [1.5.运行Laravel Homestead](#15运行laravel-homestead)
		- [1.6.安装laravel到目录](#16安装laravel到目录)
		- [1.7\. 关机](#17-关机)
	- [2\. 正常安装Laravel](#2-正常安装laravel)
	- [安装nginx + php7 + mysql + redis](#安装nginx-php7-mysql-redis)
		- [2.1 安装nginx](#21-安装nginx)
		- [2.2 安装php7.0-fpm](#22-安装php70-fpm)
		- [2.3 安装MySql](#23-安装mysql)
		- [2.4 安装composer](#24-安装composer)
		- [2.5 安装laravel 5](#25-安装laravel-5)
			- [2.5.1 方法1](#251-方法1)
			- [2.5.2 方法2](#252-方法2)
			- [2.5.3 权限](#253-权限)

<!-- tocstop -->

 --------------------------------------------------------------------------------

# laravel 5 学习指南

## 1.部署Laravel Homestead

### 1.1\. 必要软件安装

软件         | 下载地址
:--------- | :--------------------------------
vagrant    | [地址](https://www.vagrantup.com/)
virtualbox | [地址](https://www.virtualbox.org/)
git        | [地址](https://git-scm.com/)

composer 在虚拟机中有提供，但为了能正常安装laravel需要自备**梯子**

Laravel 官方指南[地址](https://laravel.com/docs/5.2/homestead)

### 1.2.下载Laravel Homestead虚拟机文件

Git Bash中输入命令

```
$ vagrant box add laravel/homestead        //安装Laravel Homestead
```

随后会要求选择使用的虚拟机。virtualbox为1 ，Vmware 为2 。

_注意：如果使用VMware需要购买vagrant的插件。_

确认后，开始下载。

_注意：需要梯子_

### 1.3\. 安装Laravel Homestead

下载完成后，需要使用git，克隆Laravel Homestead的安装设置文件。

切换到想要建立项目的目录，使用克隆命令。

```
$ git clone https://github.com/laravel/homestead.git Homestead
```

### 1.4\. 设置Laravel Homestead

首先需要有SSH密钥，如果没有使用下方命令创建

```
$ ssh-keygen
```

克隆完整后，载入Laravel Homestead设置文件。

```
$ bash init.sh   //载入 Laravel Homestead 设置文件
```

之后设置文件映射，

`C:\Windows\System32\drivers\etc\hosts` 负责DNS的文件

`C:\Users\Keleven\.homestead\Homestead.yaml` 负责Laravel Homestead设置的文件

在`Homestead.yaml`修改

```
ip: "192.168.20.10"        //映射的ip地址
memory: 2048            //虚拟机内存大小
cpus: 1                    //虚拟机CUP数量
provider: virtualbox    //虚拟机供应商

authorize: ~/.ssh/id_rsa.pub   //SSH 公钥地址

keys:                            
    - ~/.ssh/id_rsa                //SSH 私钥地址

folders:                            //项目的主文件夹
    - map: J:\laravel\web  # pc          //PC端 文件地址
      to: /home/vagrant/webCode  #vbox    //Vbox端 地址

sites:
    - map: web.app                               //网站名称
      to: /home/vagrant/webCode/web/public       //laravel 入口
    - map: back.app                                 //网站名称
      to: /home/vagrant/webCode/back/public      //laravel 入口
databases:                                       // web数据库名称
    - webdb
```

在`hosts`中添加

```
192.168.20.10 web.app
192.168.20.10 back.app
```

_注意：IP：地址用没有使用的IP段_

### 1.5.运行Laravel Homestead

在克隆的的Homestead目录下。启动虚拟机。

**不要再Vbox中启动，不要再Vbox中启动，不要再Vbox中启动。**

```
$ vagrant up
```

加载完成会就可以使用了，使用下方命令与虚拟机建立SSH链接。

```
$ vagrant ssh
```

进去虚拟机的第一件事，解除Composer的xdebug模式，否则严重影响下载包的速度。

解除方法：

找到`/etc/php/7.0/mods-available/`下的`xdebug.ini`打开后加注释。

```
$ sudo nano /etc/php/7.0/mods-available/xdebug.ini  // 使用nano编辑xdebug.ini文件
```

在`zend_extension=xdebug.so`前方加`;`

对`sudo nano /etc/php/5.6/mods-available/`下的`xdebug.ini`进行同样操作。

```
sudo nano /etc/php/5.6/mods-available/xdebug.ini
```

### 1.6.安装laravel到目录

安装目录在`Homestead.yaml`设置过，本地目录与虚拟机的目录是共享的。

即安装完成后，可以直接修改本地目录中的文件。

使用Git Bash进入项目目录。使用`composer`安装`laravel`

```
$ composer create-project --prefer-dist laravel/laravel [blog]
```

[blog]可以是任意的项目名称。

_注意：项目名称要与`Homestead.yaml`中设置对应_

安装完成后，在浏览器中输入web.app，就可以看到laravel在正常运行了。

### 1.7\. 关机

先使用`exit`推出SSH链接。

在关闭虚拟机:

```
$ vagrant halt
```

--------------------------------------------------------------------------------

## 2\. 正常安装Laravel

在ubuntu desktop环境下安装。

## 安装nginx + php7 + mysql + redis

--------------------------------------------------------------------------------

### 2.1 安装nginx

```
$ sudo apt-get update
$ sudo apt-get install nginx
```

显示防护墙列表

```
$ sudo ufw app list
```

结果如下

```
可用应用程序：
  CUPS
  Nginx Full
  Nginx HTTP
  Nginx HTTPS
```

- Nginx Full: `This profile opens both port 80 (normal, unencrypted web traffic) and port 443 (TLS/SSL encrypted traffic)`
- Nginx HTTP: `This profile opens only port 80 (normal, unencrypted web traffic)`
- Nginx HTTPS: `This profile opens only port 443 (TLS/SSL encrypted traffic)`

设置防火墙规则

```
$ sudo ufw allow 'Nginx Full'
```

查看防火墙状态

```
$ sudo ufw status
```

网站目录在`var/www/html`

根据情况修改`/etc/nginx/nginx.conf`中的`keepalive_timeout`值。

--------------------------------------------------------------------------------

### 2.2 安装php7.0-fpm

[参考地址](https://www.howtoforge.com/tutorial/installing-nginx-with-php7-fpm-and-mysql-on-ubuntu-16.04-lts-lemp/)

```
$ sudo apt-get install php7.0-fpm
```

现在还不能访问`.php`文件，要进行如下设置：

```
$ sudo nano /etc/nginx/sites-available/default
```

- `server_name _`服务器名，可以不填。
- `root /var/www/html` 网站目录地址。可以手动修改。

  增加`index.php`

- `location ~ \.php$`php支持，需要解除注释。

- `include snippets/fastcgi-php.conf;`取消注释

-  `fastcgi_pass 127.0.0.1:9000;`与 `fastcgi_pass unix:/run/php/php7.0-fpm.sock;`选一个否则冲突。

之后重新读取`nginx`设置，

```
$ sudo service nginx reload
```

修改`/etc/php/7.0/fpm/php.ini`

```
$ sudo nano /etc/php/7.0/fpm/php.ini
```

设置`cgi.fix_pathinfo=1`

重启`php7.0-fpm`服务

```
$ sudo service php7.0-fpm reload
```

网站目录下建立任意`.php`文件测试。

文件内容如下

```php
    <?php
    phpinfo();
    ?>
```

随后输入`127.0.0.1/[文件名].php`

通过命令定位php7.0的其他包：

```
$ sudo apt-cache search php7.0
```

选择安装的如下：

```
$ sudo apt-get install php7.0-cli php7.0-common php7.0 php7.0-mysql php7.0-curl php7.0-gd php7.0-bz2 php7.0-mcrypt php7.0-zip php7.0-mbstring php7.0-xml
```

_其他让php7.0使用TCP连接_

修改文件`/etc/php/7.0/fpm/pool.d/www.conf`与`/etc/nginx/sites-available/default`

`/etc/php/7.0/fpm/pool.d/www.conf`文件

```
;listen = /var/run/php5-fpm.sock       //注释掉
listen = 127.0.0.1:9000                    // 增加
```

`/etc/nginx/sites-available/default`文件

```
[...]
        location ~ \.php$ {
 include snippets/fastcgi-php.conf;

 # With php7.0-cgi alone:
 fastcgi_pass 127.0.0.1:9000;
 # With php7.0-fpm:
 # fastcgi_pass unix:/run/php/php7.0-fpm.sock;
 }
[...]
```

重新读取`nginx`

```
service nginx reload
```

--------------------------------------------------------------------------------

### 2.3 安装MySql

```
$ sudo apt-get install mysql-server-5.7 php7.0-mysql
```

--------------------------------------------------------------------------------

### 2.4 安装composer

[下载地址](https://getcomposer.org/download/)

随后会得到一个`composer.phar`文件，可以直接放入任意目录，终端输入`composer`使用。

当然为了方便可以设置为全局可以用。

```
$ sudo mv composer.phar /usr/local/bin/composer
$ sudo chmod +x /usr/local/bin/composer
```

--------------------------------------------------------------------------------

### 2.5 安装laravel 5

[文档地址](https://laravel.com/docs/5.2)

#### 2.5.1 方法1

```
$ composer create-project --prefer-dist laravel/laravel [blog]
```

[blog]可以为项目名

#### 2.5.2 方法2

需要使用`bash`。`zsh`测试无效

```
$ composer global require "laravel/installer"

$ export PATH="~/.config/composer/vendor/bin:$PATH"
```

进入想要创建项目的目录

```
$ laravel new [blog]
```

[blog]可以为项目名

#### 2.5.3 权限

在Ubuntu版本下，需要设置目录权限。

项目目录下的`storage`

```
chmod -R 777 storage
```

`bootstrap`目录下的`cache`
```
chmod -R 777 cache
```

_注意：需要梯子！需要梯子！需要梯子！_
