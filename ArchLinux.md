<!--toc-->

- [Arch Linux 安装指南](#arch-linux-安装指南)
	- [1\. 基本系统安装](#1-基本系统安装)
		- [1.1\. Arch Linux镜像下载](#11-arch-linux镜像下载httpswwwarchlinuxorgdownload)
		- [1.2\. 安装](#12-安装)
			- [1.2.1\. VWmare](#121-vwmare)
			- [基本安装设定](#基本安装设定)
			- [开始安装](#开始安装)
			- [1.2.2\. 实体机](#122-实体机)
			- [1.2.3\. 网站参考](#123-网站参考)
	- [2\. 桌面化安装](#2-桌面化安装)
	- [3\. 常用软件补全](#3-常用软件补全)
	- [4\. 美化](#4-美化)
	- [5\. 开发环境搭建](#5-开发环境搭建)

<!-- tocstop -->

----

# Arch Linux 安装指南

## 1\. 基本系统安装

### 1.1\. Arch Linux镜像[下载](https://www.archlinux.org/download/)

### 1.2\. 安装

#### 1.2.1\. VWmare

#### 基本安装设定

- 设定任意 Linux 64位虚拟机
- 内存2G，硬盘20G
- 挂载镜像光盘

#### 开始安装

#### 1.2.2\. 实体机

```
# loadkeys us                                // 设置键盘
# cfdisk                                       // 创建硬盘分区
# mkfs.ext4 /dev/sda1                        // 格式化分区
# mkswap /dev/sda2 && swapon /dev/sda2       // 挂载swap
# mount /dev/sda1 /mnt                       // 挂载 分区
# vi /etc/pacman.d/mirrorlist                // 添加软件源
# pacman -Syy                                // 更新软件源
# pacstrap /mnt base base-devel net-tools    // 安装基础包
# genfstab -U -p /mnt >> /mnt/etc/fstab      // 自动生成引导文件
# arch-chroot /mnt                           // 切入硬盘root
# vi /etc/locale.gen && locale-gen              // 设置字符
# echo LANG="zh_CN.UTF-8" >> /etc/locale.conf   // 设置语言
# echo KEYMAP=us >> /etc/vconsole.conf          // 设置键盘
# ln -s /usr/share/zoneinfo/Asia/Chongqing /etc/localtime  // 设置本地时间
# echo vm >> /etc/hostname                                 // 设置主机名称
# mkinitcpio -p linux                                         // 根据配置生成镜像
# passwd                                                   // 修改root密码
# pacman -S grub-bios                                      // 安装grub引导
# grub-install /dev/sda                                    // 安装到硬盘
# grub-mkconfig -o /boot/grub/grub.cfg                     // 自动设置grub
# exit                                                     // 退出root用户
# umount /mnt                                              // 取消挂载硬盘
# reboot                                                   // 重启
```

备注：

```
# mkdir /mnt/home && mount /dev/sda9 /mnt/home     //创建/home 并挂载硬盘。
```

在创建硬盘分区后执行，如果有需要。

要添加的软件源： 参考官方 最新[地址](https://wiki.archlinux.org/index.php/Mirrors#China)

```
http://mirror.bit.edu.cn/archlinux/ - Beijing Institute of Technology
http://mirrors.aliyun.com/archlinux/ - Alibaba

http://mirrors.sohu.com/archlinux/
http://mirrors.yun-idc.com/archlinux/
```

以上基本安装完成。

#### 1.2.3\. 网站参考

[地址1](https://bbs.archlinuxcn.org/viewtopic.php?id=1901)

[地址2](https://bbs.archlinuxcn.org/viewtopic.php?id=1037)

自动化安装设置工具[Archlinux U Install](https://github.com/helmuthdu/aui)

[自动显示修复](http://www.ituring.com.cn/article/120762)

## 2\. 桌面化安装

## 3\. 常用软件补全

## 4\. 美化

## 5\. 开发环境搭建
