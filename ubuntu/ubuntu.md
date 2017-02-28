<!--toc-->

* [ubuntu 安装与设置](#ubuntu-安装与设置)
	* [下载镜像包](#下载镜像包)
	* [VMware与实体机安装](#vmware与实体机安装)
	* [安装之后](#安装之后)
		* [安装vmware tools _VWmare虚假机需要安装_](#安装vmware-tools-_vwmare虚假机需要安装_)
		* [安装shadowsocks](#安装shadowsocks)
		* [安装privoxy //代理工具](#安装privoxy-代理工具)
		* [安装google拼音](#安装google拼音)
		* [安装 unity-tweak-tool](#安装-unity-tweak-tool)
		* [删除火狐](#删除火狐)
		* [删除libreoffice](#删除libreoffice)
		* [安装新力得](#安装新力得)
		* [安装gdebi](#安装gdebi)
		* [安装chromium](#安装chromium)
		* [安装sublime text 3](#安装sublime-text-3)
		* [安装与配置zsh与oh-my-zsh](#安装与配置zsh与oh-my-zsh)
		* [设置oh-my-zsh](#设置oh-my-zsh)
		* [安装powerline](#安装powerline)
			* [powerline 与 zsh](#powerline-与-zsh)
			* [powerline 与 vim](#powerline-与-vim)
			* [powerline 与 tmux](#powerline-与-tmux)
		* [安装ravefinity风格](#安装ravefinity风格)
		* [安装my weather](#安装my-weather)
		* [安装网易云音乐](#安装网易云音乐)
		* [安装dropbox](#安装dropbox)
		* [安装爱壁纸HD](#安装爱壁纸hd)
		* [安装Numix Circle 图标与主题](#安装numix-circle-图标与主题)
		* [安装inkscape](#安装inkscape)
		* [安装conky](#安装conky)
		* [安装JQ、curl](#安装jq-curl)
		* [安装conky插件](#安装conky插件)
		* [安装plank](#安装plank)
		* [安装Netgare WNDA3100v2 USB](#安装netgare-wnda3100v2-usb)
		* [安装配置JDK与Android Studio](#安装配置jdk与android-studio)
			* [安装JDK](#安装jdk)
			* [安装 Android Studio](#安装-android-studio)
		* [安装kicad](#安装kicad)
		* [安装 terminix](#安装-terminix)
		* [安装Gitg](#安装gitg)
		* [安装atom](#安装atom)

<!-- tocstop -->

----

# ubuntu 安装与设置

## 下载镜像包

[Ubuntu下载地址](http://www.ubuntu.com/download/desktop)

## VMware与实体机安装

- 实体机需要使用[_win32 disk imager_](https://sourceforge.net/projects/win32diskimager/files/Archive/)写入U盘
- 基本安装

> 设定用户名与系统时间<br>
> 分区：_一般_ 一个~/分区，一个swap交互分区。

> username | password
> :------: | :------:
>   new    |    0
>   root   |    0

## 安装之后

> ### 安装vmware tools _VWmare虚假机需要安装_

> `$ cd vmware-tools-distrib`<br>
> `$ sudo ./vmware-install.pl`

> 删除

> `$ sudo ./vmware-tools-distrib/bin/vmware-uninstall-tools.pl`

--------------------------------------------------------------------------------

> ### 安装shadowsocks

> `$ sudo add-apt-repository ppa:hzwhuang/ss-qt5`<br>
> `$ sudo apt-get update`<br>
> `$ sudo apt-get install shadowsocks-qt5`

--------------------------------------------------------------------------------

> ### 安装privoxy //代理工具

> `$ sudo apt-get install privoxy`

> #

> > 配置文件地址/etc/privoxy/config

> > 在4.1 listen-address 改端口<br>
> > `Listen-addres localhost:8118`

> > 在5.2加<br>
> > `forward-socks5 / 127.0.0.1:7070 .`

> #

> > 重启privoxy<br>
> > `$ sudo /etc/init.d/privoxy restart`

> #

> > 设置终端环境<br>
> > `$ export http_proxy="127.0.0.1:8118"` `$ export https_proxy="127.0.0.1:8118"`

> #

> > 在/etc/rc.local添加开机启动sud<br>
> > `$ sudo /etc/init.d/privoxy start`

> #

> > 在/etc/profile加<br>
> > `export http_proxy="127.0.0.1:8118"`<br>
> > `export https_proxy="127.0.0.1:8118"`

--------------------------------------------------------------------------------

> ### 安装google拼音

> `$ sudo add-apt-repository ppa:fcitx-team/nightly`<br>
> `$ sudo apt-get update`<br>
> `$ sudo apt-get install fcitx fcitx-googlepinyin`

--------------------------------------------------------------------------------

> ### 安装 unity-tweak-tool

> `$ sudo add-apt-repository ppa:freyja-dev/unity-tweak-tool-daily`<br>
> `$ sudo apt-get update`<br>
> `$ sudo apt-get install unity-tweak-tool`

--------------------------------------------------------------------------------

> ### 删除火狐

> `$ sudo apt-get remove firefox`

--------------------------------------------------------------------------------

> ### 删除libreoffice

> `$ sudo apt-get remove --purge libreoffice*`<br>
> `$ sudo apt-get clean`<br>
> `$ sudo apt-get autoremove`

--------------------------------------------------------------------------------

> ### 安装新力得

> `$ sudo apt-get install synaptic`

--------------------------------------------------------------------------------

> ### 安装gdebi

> `$ sudo apt-get install gdebi`

--------------------------------------------------------------------------------

> ### 安装chromium

> `$ sudo apt-get install chromium-browser`

--------------------------------------------------------------------------------

> ### 安装sublime text 3

> `$ sudo add-apt-repository ppa:webupd8team/sublime-text-3`<br>
> `$ sudo apt-get update`<br>
> `$ sudo apt-get install sublime-text-installer`

--------------------------------------------------------------------------------

> ### 安装与配置zsh与oh-my-zsh

> #

> > 1\. ubuntu中默认安装了哪些shell<br>
> > `$ cat /etc/shells # /etc/shells: valid login shells/bin/sh/bin/dash/bin/bash/bin/rbash`<br>
> > （有sh、dash、bash和rbash）

> #

> > 2\. 当前正在运行的是那个版本的shell<br>
> > `$ echo $SHELL/bin/bash`

> #

> > 3\. 正式安装zsh、git和wget<br>
> > 获取并自动按照oh-my-zsh：<br>
> > `$ sudo apt-get install zsh git wget`<br>
> > `$ wget --no-check-certificate https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh`

> #

> > 4\. 替换bash为zsh：<br>
> > `$ chsh -s /bin/zsh`

> #

> > 5\. 最后重启：<br>
> > `$ sudo reboot`

> #

> [参考文档](http://www.jianshu.com/p/546effd99c35)<br>
> [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)

安装完成后的默认效果

--------------------------------------------------------------------------------

### 设置oh-my-zsh

`~/.zshrc`为设置文件。

- Plugins

  默认插件`plugins=(git)`

  更多插件在`~/.oh-my-zsh/plugins`

  添加方法如`plugins=(git github ubuntu)`

  插件的使用方法在`plugins/插件名/README.md`

  下方表格为`ubuntu`插件

缩写   | 实际命令
:--- | :-----------------------------------------------
acs  | apt-cache search
afs  | apt-file search --regexp
ags  | apt-get source                                   |
acp  | apt-cache policy
afu  | sudo apt-file update
ppap | sudo ppa-purge
ag   | sudo apt-get                                     |
aga  | sudo apt-get autoclean
agar | sudo apt-get autoremove
agb  | sudo apt-get build-dep
agc  | sudo apt-get clean                               |
agd  | sudo apt-get dselect-upgrade
agi  | sudo apt-get install                             |
agp  | sudo apt-get purge                               |
agr  | sudo apt-get remove                              |
agu  | sudo apt-get update                              |
agud | sudo apt-get update && sudo apt-get dist-upgrade
agug | sudo apt-get upgrade
aguu | sudo apt-get update && sudo apt-get upgrade      |
agar | sudo apt-get autoremove

- THEME

  默认风格为`robbyrussell`

  修改`ZSH_THEME="agnoster"`，比较有趣的一些，[更多风格](https://wiki.github.com/robbyrussell/oh-my-zsh/themes)

  设置完成后的效果

  ![](https://raw.githubusercontent.com/422303771/web/master/ubuntu/img/zsh1.png)

  _注意使用`agnoster`风格时，要做如下设置放置乱码。_

  使用下方命令测试

  ```
    $ echo "\ue0b0 \u00b1 \ue0a0 \u27a6 \u2718 \u26a1 \u2699"
  ```

  - 如果有乱码需要安装字体补丁包(一般都会乱码)

    ```
      $ git clone https://github.com/powerline/fonts.git
      $ cd fonts
      $ ./install.sh
    ```

  - 之后要设置终端字体，不然依旧是乱码。

    打开终端→编辑→配置文件首选项→常规→自定义字体。

    字体要选择一个`powerline`修正的字体。字号随意。

  - 如果感觉`用户名机器名称`的显示方式太长可以修改`~/.oh-my-zsh/themes/agnoster.zsh-theme`文件。

    找到`### Prompt components`将`@%m`去掉后保存。

### 安装powerline

最方便的方法是使用`pip`安装。[文档地址](https://powerline.readthedocs.io/en/latest/)

安装`pip`

```
$ sudo apt-get install python-pip
$ reboot //pip安装包的位置正确
$ pip install powerline-status
$ pip show powerline-status  //查看包信息
```

![](https://raw.githubusercontent.com/422303771/web/master/ubuntu/img/zsh2.png)

文档中的`{repository_root}`就是`Location`指示的`/usr/local/lib/python2.7/dist-packages`

#### powerline 与 zsh

设置`~/.zshrc`文件

添加

```
. /usr/local/lib/python2.7/dist-packages/powerline/bindings/zsh/powerline.zsh
```

保存后，重新打开终端。

效果如下图：

![](https://raw.githubusercontent.com/422303771/web/master/ubuntu/img/zsh3.png)

#### powerline 与 vim

安装vim

```
$ sudo apt-get install vim
```

在根目录下新建`.vimrc`文件，添加下方代码。

```
set rtp+=/usr/local/lib/python2.7/dist-packages/powerline/bindings/vim
set laststatus=2
set t_Co=256
```

保存后，终端输入`vim`,查看效果。

```
$ vim
```

运行效果如下

![](https://raw.githubusercontent.com/422303771/web/master/ubuntu/img/zsh4.png)

#### powerline 与 tmux

安装tmux

```
$ sudo apt-get install tmux
```

根目录下新建`.tmux.conf`文件，添加下方代码。

```
source "/usr/local/lib/python2.7/dist-packages/powerline/bindings/tmux/powerline.conf"
set-option -g default-terminal "screen-256color"
set-option -g mouse on
```

保存并重启。运用`$ tmux`查看效果。

![](https://raw.githubusercontent.com/422303771/web/master/ubuntu/img/zsh5.png)

**更新：2016年7月13日18:33:13**

为了方便管理为`.vimrc`、`.zshrc`、`.conkyrc`、`.tmux.conf`建立软连接

```
ln -s ~/set/.conkyrc ~/.conyrc
ln -s ~/set/.tmux.conf ~/.tmux.conf
ln -s ~/set/.vimrc ~/.vimrc
ln -s ~/set/.zshrc ~/.zshrc
```

--------------------------------------------------------------------------------

> ### 安装ravefinity风格

> `$ sudo add-apt-repository ppa:ravefinity-project/ppa`<br>
> `$ sudo apt-get update`<br>
> `$ sudo apt-get install vibrancy-colors`<br>
> `$ sudo apt-get install ambiance-flat-colors radiance-flat-colors`

--------------------------------------------------------------------------------

> ### 安装my weather

> > `$ sudo add-apt-repository ppa:atareao/atareao`<br>
> > `$ sudo apt-get update`<br>
> > `$ sudo apt-get install my-weather-indicator`

> #

> > 删除

> > `$ sudo apt-get remove my-weather-indicator`<br>
> > `$ sudo apt-get install ppa-purge` // _源管理器_<br>
> > `$ sudo ppa-purge ppa:atareao/atareao`

--------------------------------------------------------------------------------

> ### 安装网易云音乐

> 去[官方网站](http://music.163.com/#/download)下载

--------------------------------------------------------------------------------

> ### 安装dropbox

> 去[官方网站](https://www.dropbox.com/install?os=lnx)下载

> #

> > 或者 源代码安装

> #

> > 1\. 下载源代码<br>
> > [下载文件](https://www.dropbox.com/download?dl=packages/nautilus-dropbox-1.4.0.tar.bz2)

> #

> > 2\. 提取 tarball<br>
> > `tar xjf ./nautilus-dropbox-1.4.0.tar.bz2`

> #

> > 3\. 编译<br>
> > `cd ./nautilus-dropbox-1.4.0; ./configure; make; make install;`

--------------------------------------------------------------------------------

### 安装爱壁纸HD

官方[下载](https://www.lovebizhi.com/linux.html)

需要额外的包python-support_1.0.15_all.deb [地址](https://launchpad.net/ubuntu/xenial/amd64/python-support/1.0.15)

--------------------------------------------------------------------------------

### 安装Numix Circle 图标与主题

```
$ sudo add-apt-repository ppa:numix/ppa
$ sudo apt-get update
$ sudo apt-get install numix-gtk-theme numix-icon-theme-circle
```

图标使用效果

![](https://raw.githubusercontent.com/422303771/web/master/ubuntu/img/Numix1.png)

--------------------------------------------------------------------------------

### 安装inkscape

用于将SVG图片转换为PNG图片

```
$ sudo apt-get install inkscape
```

### 安装conky

```
$ sudo apt-get install conky-all
```

### 安装JQ、curl

```
$ sudo apt-get install jq

$ sudo apt-get install curl

$ sudo apt-get install font-manager   //字体管理工具
```

### 安装conky插件

- 需要有OpenWeatherMap的API KEY

- 需要用到的字体[Poiret One](https://www.google.com/fonts/specimen/Poiret+One)

- [参考文档](https://github.com/zagortenay333/conky-Vision),替换图标颜色后不能正常加载。

安装后效果

![](https://raw.githubusercontent.com/422303771/web/master/ubuntu/img/conky1.png)

编辑`.conkyrc`文件

`template6`为OpenWeatherMap API KEY

`template7`为城市编号 [城市编号查找地址](http://openweathermap.org/help/city_list.txt)

`template8`为温度显示单位。`metric`为摄氏温度。

`template9`为显示语言图中为`en_US.UTF8`,中文可以设置为`zh_CN.UTF-8`

--------------------------------------------------------------------------------

### 安装plank

```
$ sudo apt-get install plank
```

--------------------------------------------------------------------------------

### 安装Netgare WNDA3100v2 USB

1. `$ lsusb` 显示设备列表。`$ iwconfig`不能找到网卡。

2. `$ sudo apt-get install ndisgtk` 安装ndisgtk管理驱动工具。

3. `$ sudo ndiswrapper -i bcmn43xx64.inf` 安装bcm4323驱动。

4. `$ ndiswrapper -l`查看驱动是否安装成功。

5. 加载驱动模块

  ```
  $ sudo depmod -a
  $ sudo modprobe ndiswrapper
  ```

--------------------------------------------------------------------------------

### 安装配置JDK与Android Studio

JDK [下载](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) [参考视频](https://www.youtube.com/watch?v=NZB3Iy7Lve4)

Android Studio [下载](https://developer.android.com/studio/index.html) [参考视频](https://www.youtube.com/watch?v=qfinKxwYYZs)

当前下载的版本,安装与配置以当前版本为基准。要使用Android Studio首先要安装JDK。

```
  软件       |     版本
```

:------------: | :---------: JDK | 1.8.0_91 Android Studio | 143.2915827

不知道有没有安装JDK，可以在终端查询

```
$ java -version
```

#### 安装JDK

解压下载包，到根目录`~/`方便以后操作。

在`/usr/lib`中创建`java`文件夹使用命令。

```
$ cd /usr/lib/
$ sudo mkdir java
```

随后退回到根目录，移动`jdk1.8.0_91`到`/usr/lib/java/`。

```
$ cd ~
$ sudo mv jdk1.8.0_91/ /usr/lib/java
```

之后设置JDK。

```
$ sudo update-alternatives --install "/usr/bin/java" "java" "/usr/lib/java/jdk1.8.0_91/bin/java" 1
$ sudo update-alternatives --install "/usr/bin/javac" "javac" "/usr/lib/java/jdk1.8.0_91/bin/javac" 1
$ sudo update-alternatives --install "/usr/bin/javaws" "javaws" "/usr/lib/java/jdk1.8.0_91/bin/javaws" 1
```

最后配置本机环境。在`~/.bashrc`文件中。

```
$ sudo subl ~/.bashrc
```

`~/.bashrc`要写入的内容

```
export JAVA_HOME=/usr/lib/java/jdk1.8.0_91
set PATH="$PATH:$JAVA_HOME/bin"
export PATH
```

再次输入`$ java -version`可看到版本号如下。

```
$ java -version
java version "1.8.0_91"
Java(TM) SE Runtime Environment (build 1.8.0_91-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.91-b14, mixed mode)
```

_传说中快捷方法，自动安装。_

```
$ sudo add-apt-repository ppa:webupd8team/java
$ sudo apt-get update
$ sudo apt-get install oracle-java8-installer
```

#### 安装 Android Studio

下载Android Studio后解压到`/home`，进入`/home/new/android-studio/bin/`目录

```
$ cd /home/new/android-studio/bin/
```

对`studio.sh`文件提权后，运行。

```
$ sudo chmod 777 -R studio.sh
$ ./studio.sh
```

弹出窗口，询问导入配置，还是全新用户。选择全新。随后选择内容，安装。

当出现`unable to run mksdcard sdk tool`时，安装32位包

```
$ sudo apt-get install lib32z1 lib32ncurses5 lib32bz2-1.0 lib32stdc++6
```

_`lib32bz2-1.0`包没有找到，有手动安装[地址](http://packages.ubuntu.com/trusty/lib32bz2-1.0)_

--------------------------------------------------------------------------------

### 安装kicad

```
$ sudo add-apt-repository --yes ppa:js-reynaud/kicad-4
$ sudo apt-get update
$ sudo apt-get install kicad kicad-locale-zh
```

--------------------------------------------------------------------------------

### 安装 terminix

[地址](https://github.com/gnunn1/terminix)

```
$ sudo add-apt-repository ppa:webupd8team/terminix
$ sudo apt-get update
$ sudo apt-get install terminix
```
* 设置

在`~.zshrc`中添加
```
if [[ $TERMINIX_ID ]]; then
        source /etc/profile.d/vte.sh
fi
```
在`ubuntu`中需要链接`vte.sh`文件
```
sudo ln -s /etc/profile.d/vte-2.91.sh /etc/profile.d/vte.sh
```


替换原有终端
```
$ sudo update-alternatives --config x-terminal-emulator
```

--------------------------------------------------------------------------------

### 安装Gitg

```
$ sudo apt-get install gitg
```

--------------------------------------------------------------------------------

### 安装atom

```
$ sudo add-apt-repository ppa:webupd8team/atom
$ sudo apt-get update
$ sudo apt-get install atom
```

--------------------------------------------------------------------------------
