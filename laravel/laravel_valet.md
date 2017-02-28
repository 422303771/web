
<!-- toc orderedList:0 depthFrom:1 depthTo:6 -->

* [Laravel Valet](#laravel-valet)
  * [Laravel Valet on Ubuntu](#laravel-valet-on-ubuntu)
    * [安装需求包](#安装需求包)
    * [安装php7.1](#安装php71)
    * [安装Composer](#安装composer)
    * [安装Valet](#安装valet)
    * [安装Mysql5.7](#安装mysql57)
    * [laravel](#laravel)
    * [使用phpmyadmin 连接 本地Mysql](#使用phpmyadmin-连接-本地mysql)
    * [portainer](#portainer)

<!-- tocstop -->


----

# Laravel Valet
简化的laravel开发平台。`Valet`比`Homestead`要好用的多。

`Valet`支持`Mac os`或者`Ubuntu`系统。
## Laravel Valet on Ubuntu

### 安装需求包
 ```
$ sudo apt-get install libnss3-tools jq xsel
 ```
### 安装php7.1
 ```
$ sudo add-apt-repository ppa:ondrej/php
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo apt-get install php7.1-cli php7.1-common php7.1-curl php7.1-json php7.1-mbstring php7.1-mcrypt php7.1-opcache php7.1-readline php7.1-xml php7.1-zip php7.1-mysql php7.1
 ```
### 安装Composer
```
$ php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('SHA384', 'composer-setup.php') === '55d6ead61b29c7bdee5cccfb50076874187bd9f21f65d8991d46ec5cc90518f447387fb9f76ebae1fbbacf329e583e30') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
$ mv composer.phar /usr/local/bin/composer
```

升级`global`包
```
composer global update
```

### 安装Valet
> 注意：默认安装`php7.1`时，会自动安装`apache2`。
> `apache2`与`nginx`冲突，需要卸载`apache2`。

```
$ composer global require cpriego/valet-linux
```
在`~/.zshrc`添加
```
export PATH="$PATH:$HOME/.config/composer/vendor/bin"
```
或使用下方命令
```
echo 'export PATH="$PATH:$HOME/.config/composer/vendor/bin"' >> ~/.zshrc
```
之后运行安装valet
```
$ valet install
```
使用下面命令关联项目目录
```
$ valet park
```
使用下方命令将域名改为`.app`
```
$ valet domain app
```

### 安装Mysql5.7
安装Mysql5.7
```
$ sudo apt-get install mysql-server-5.7
```
设置Mysql5.7
```
$ sudo mysql_secure_installation
```
设置Mysql5.7开机启动
```
$ systemctl status mysql.service
```
创建新用户
用户名为`new`，密码为`New@123456`
```
$ sudo mysql -u root -p
Enter password:
mysql> CREATE USER 'new'@'localhost' IDENTIFIED BY 'New@123456';
mysql> GRANT ALL PRIVILEGES ON *.* TO 'new'@'localhost';
```
新建数据库
数据库名`blog`
```
mysql> CREATE DATABASE blog\G
```

### laravel

```
composer global require "laravel/installer"
```

### 使用phpmyadmin 连接 本地Mysql
>使用`docker`镜像包，需要注意`socket`通道挂载。

`docker-compose.yml`文件
```
version: '2'

services:
  phpmyadmin:
    restart: always
    image: phpmyadmin/phpmyadmin
    container_name: phpmyadmin
    # hostname: phpmyadmin
    # domainname: example.com
    ports:
      - 8080:80
    environment:
      - PMA_HOSTS=localhost
      # - PMA_VERBOSES=production-db1,production-db2,dev-db1,dev-db2
      - PMA_USER=new
      - PMA_PASSWORD=New@123456
      # - PMA_ABSOLUTE_URI=http://example.com/phpmyadmin/
    volumes:
      - /var/run/mysqld/mysqld.sock:/run/mysqld/mysqld.sock
```

### portainer

>UI方式管理`docker`容器与镜像

```
docker run --rm -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock portainer/portainer
```
![sp170228_004013](https://ooo.0o0.ooo/2017/02/28/58b4567bcb5c9.png)
<!-- ![sp170228_004013](/assets/sp170228_004013.png) -->
