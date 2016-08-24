<!--toc-->

- [Ubuntu Server 16.04 LTS](#ubuntu-server-1604-lts)
	- [更新：](#更新)
	- [安装Gitlab](#安装gitlab)
	- [安装laravel](#安装laravel)

<!-- tocstop -->

----

# Ubuntu Server 16.04 LTS

## 更新：

```
$ sudo apt-get update   //更新软件源
$ sudo apt-get upgrade  //升级软件
$ sudo apt-get dist-upgrade  //升级系统
```

对于出现：

```
x packages can be updated.
y updates are security updates.
```

只是每日提示日记。如果想要移除，修改`/etc/update-motd.d/90-updates-available`文件。

## 安装Gitlab

私有git仓库工具，可以设置在任何主机上，主机限定linux。

[官方地址](https://about.gitlab.com/)

社区版，可以建立最多十个项目。

安装以社区版为例。

_注意：需要梯子_

1. 安装与配置必要依赖

  ```
   sudo apt-get install curl openssh-server ca-certificates postfix
  ```

2. 安装Gitlab源与软件包

  ```
   curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
   sudo apt-get install gitlab-ce
  ```

3. 设置并启动Gitlab

  ```
   sudo gitlab-ctl reconfigure
  ```

默认登陆的用户名是root，密码在第一次登陆时设置。

## 安装laravel
