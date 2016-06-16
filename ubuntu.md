# ubuntu 安装与设置
## 下载镜像包
[Ubuntu下载地址](http://www.ubuntu.com/download/desktop)
## VMware与实体机安装
* 实体机需要使用[*win32 disk imager*](https://sourceforge.net/projects/win32diskimager/files/Archive/)写入U盘
* 基本安装   

>	设定用户名与系统时间  
>	分区：*一般* 一个~/分区，一个swap交互分区。 
>
>|username|password|  
>|:------:|:------:|  
>|new     |0       |  
>|root    |0       |



## 安装之后



>### 安装vmware tools *VWmare虚假机需要安装*  
>
>`$ cd vmware-tools-distrib`  
>`$ sudo ./vmware-install.pl ` 	
>
>删除	  
>
>	`$ sudo ./vmware-tools-distrib/bin/vmware-uninstall-tools.pl`  

***********

>### 安装shadowsocks  
>
>`$ sudo add-apt-repository ppa:hzwhuang/ss-qt5`  
>`$ sudo apt-get update`  
>`$ sudo apt-get install shadowsocks-qt5`  

********

>### 安装privoxy //代理工具  
>`$ sudo apt-get install privoxy`  

># 

>>配置文件地址/etc/privoxy/config   
>>
>>在4.1 listen-address 改端口  
>>`Listen-addres localhost:8118`  
>>
>>在5.2加  
>>`forward-socks5 / 127.0.0.1:7070 .`  

>#   

>>重启privoxy   
>>`$ sudo /etc/init.d/privoxy restart`  

>#   

>>设置终端环境  
>>`$ export http_proxy="127.0.0.1:8118"`	
>>`$ export https_proxy="127.0.0.1:8118"`  

># 

>>在/etc/rc.local添加开机启动sud  
>>`$ sudo /etc/init.d/privoxy start`  

>#  

>>在/etc/profile加   
>>`export http_proxy="127.0.0.1:8118"`  
>>`export https_proxy="127.0.0.1:8118"`  

----

>### 安装google拼音  
>
>`$ sudo add-apt-repository ppa:fcitx-team/nightly`  
>`$ sudo apt-get update`  
>`$ sudo apt-get install fcitx fcitx-googlepinyin`  

****

>### 安装 unity-tweak-tool  
>
>`$ sudo add-apt-repository ppa:freyja-dev/unity-tweak-tool-daily`  
>`$ sudo apt-get update`  
>`$ sudo apt-get install unity-tweak-tool`  

****

>### 删除火狐  
>  
> ``$ sudo apt-get remove firefox``  

****

>###  删除libreoffice  
> 
> `$ sudo apt-get remove --purge libreoffice*`   
> `$ sudo apt-get clean`  
> `$ sudo apt-get autoremove`  

****

>###  安装新力得  
>
>`$ sudo apt-get install synaptic`   

****

>### 安装gdebi  
>
> `$ sudo apt-get install gdebi`  

*****

>### 安装chromium  
>
>`$ sudo apt-get install chromium-browser`  

****

>### 安装sublime text 3  
>  
>`$ sudo add-apt-repository ppa:webupd8team/sublime-text-3`  
>`$ sudo apt-get update`  
>`$ sudo apt-get install sublime-text-installer`  

****
		

> ### 安装与配置zsh与oh-my-zsh  

># 

>> 1\. ubuntu中默认安装了哪些shell   
>>`$ cat /etc/shells # /etc/shells: valid login shells/bin/sh/bin/dash/bin/bash/bin/rbash`  
>>（有sh、dash、bash和rbash）  

># 

>> 2\. 当前正在运行的是那个版本的shell   
>>`$ echo $SHELL/bin/bash`   

># 

>> 3\. 正式安装zsh、git和wget   
>>获取并自动按照oh-my-zsh：    
>>`$ sudo apt-get install zsh git wget`    
>>`$ wget --no-check-certificate https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh`    

>#  

>> 4\. 替换bash为zsh：  
>>`$ chsh -s /bin/zsh`  

># 

>> 5\. 最后重启：  
>>`$ sudo reboot`   

># 

>[参考文档](http://www.jianshu.com/p/546effd99c35)

****

>### 安装ravefinity风格  
>
>`$ sudo add-apt-repository ppa:ravefinity-project/ppa`   
>`$ sudo apt-get update`    
>`$ sudo apt-get install vibrancy-colors`     
>`$ sudo apt-get install ambiance-flat-colors radiance-flat-colors`    

*****

>### 安装my weather  
>
>>`$ sudo add-apt-repository ppa:atareao/atareao`   
>>`$ sudo apt-get update`   
>>`$ sudo apt-get install my-weather-indicator`   

># 

>>删除   
>>
>>`$ sudo apt-get remove my-weather-indicator`   
>>`$ sudo apt-get install ppa-purge` // *源管理器*   
>>`$ sudo ppa-purge ppa:atareao/atareao`  

*****

>### 安装网易云音乐  
>去[官方网站](http://music.163.com/#/download)下载  

*****

>### 安装dropbox  
>去[官方网站](https://www.dropbox.com/install?os=lnx)下载  

># 

>>或者 源代码安装  

># 

>>1\. 下载源代码  
>>[下载文件](https://www.dropbox.com/download?dl=packages/nautilus-dropbox-1.4.0.tar.bz2)  

># 

>>2\. 提取 tarball   
>>`tar xjf ./nautilus-dropbox-1.4.0.tar.bz2`  

># 

>>3\. 编译  
>>`cd ./nautilus-dropbox-1.4.0; ./configure; make; make install;`  

*****

### 安装爱壁纸HD 

官方[下载](https://www.lovebizhi.com/linux.html)

需要额外的包python-support_1.0.15_all.deb [地址](https://launchpad.net/ubuntu/xenial/amd64/python-support/1.0.15)



----



### 安装Netgare WNDA3100v2 USB

1. `$ lsusb` 显示设备列表。`$ iwconfig`不能找到网卡。

2. `$ sudo apt-get install ndisgtk` 安装ndisgtk管理驱动工具。

3. `$ sudo ndiswrapper -i bcmn43xx64.inf` 安装bcm4323驱动。

4. `$ ndiswrapper -l`查看驱动是否安装成功。

5. 加载驱动模块

		$ sudo depmod -a
		$ sudo modprobe ndiswrapper

----



### 安装配置JDK与Android Studio

JDK [下载](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
[参考视频](https://www.youtube.com/watch?v=NZB3Iy7Lve4)

Android Studio [下载](https://developer.android.com/studio/index.html)
[参考视频](https://www.youtube.com/watch?v=qfinKxwYYZs)

当前下载的版本,安装与配置以当前版本为基准。要使用Android Studio首先要安装JDK。

|软件|版本|
|:----:|:----:|
|JDK|1.8.0_91|
|Android Studio|143.2915827|

不知道有没有安装JDK，可以在终端查询

	$ javac -version

1. 安装JDK
	
	解压下载包，到根目录`~/`方便以后操作。
	
	在`/usr/lib`中创建`java`文件夹使用命令。
		
		$ cd /usr/lib/ 
		$ sudo mkdir java 
	
	随后退回到根目录，移动`jdk1.8.0_91`到`/usr/lib/java/`。
	
		$ cd ~ 
		$ sudo mv jdk1.8.0_91/ /usr/lib/java 
	
	之后设置JDK。
	
		$ sudo update-alternatives --install "/usr/bin/java" "java" "/usr/lib/java/jdk1.8.0_91/bin/java" 1
		$ sudo update-alternatives --install "/usr/bin/javac" "javac" "/usr/lib/java/jdk1.8.0_91/bin/javac" 1
		$ sudo update-alternatives --install "/usr/bin/javaws" "javaws" "/usr/lib/java/jdk1.8.0_91/bin/javaws" 1
	
	最后配置本机环境。在`~/.bashrc`文件中。
	
		$ sudo subl ~/.bashrc 
	
	`~/.bashrc`要写入的内容
	
		export JAVA_HOME=/usr/lib/java/jdk1.8.0_91
		set PATH="$PATH:$JAVA_HOME/bin"
		export PATH
	
	再次运行输入`javac -version`可看到版本号如下。
		
		java version "1.8.0_91"
		Java(TM) SE Runtime Environment (build 1.8.0_91-b14)
		Java HotSpot(TM) 64-Bit Server VM (build 25.91-b14, mixed mode)

	*传说中快捷方法(未测试)*
	
		$ sudo add-apt-repository ppa:webupd8team/java
		$ sudo apt-get update
		$ sudo apt-get install oracle-java8-installer


2. 安装 Android Studio
	
	下载Android Studio后解压到`/home`，进入`/home/new/android-studio/bin/`目录
	
		$ cd /home/new/android-studio/bin/
		
	对`studio.sh`文件提权后，运行。
	
		$ sudo chmod 777 -R studio.sh
		$ ./studio.sh
	
	弹出窗口，询问导入配置，还是全新用户。选择全新。随后选择内容，安装。

	当出现`unable to run mksdcard sdk tool`时，安装32位包

	$ sudo apt-get install lib32z1 lib32ncurses5 lib32bz2-1.0 lib32stdc++6

	*`lib32bz2-1.0`包没有找到，有手动安装[地址](http://packages.ubuntu.com/trusty/lib32bz2-1.0)*


----



