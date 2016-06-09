# ubuntu 安装与设置
## 下载镜像包
[Ubuntu下载地址](http://www.ubuntu.com/download/desktop)
## VMware与实体机安装
* 实体机需要使用[*win32 disk imager*](https://sourceforge.net/projects/win32diskimager/files/Archive/)
写入U盘
* 基本安装  
>设定用户名与系统时间  
>分区：*一般* 一个~/分区，一个swap交互分区。  
|username|password|  
|***|***|  
|new     |0       |  
|root    |0       |


## 安装之后



>安装vmware tools *VWmare虚假机需要安装*  
>
>`$ cd vmware-tools-distrib`  
>`$ sudo ./vmware-install.pl ` 	
>
>删除	  
>
>	`$ sudo ./vmware-tools-distrib/bin/vmware-uninstall-tools.pl`  
\
***********
>安装shadowsocks  
>
>`$ sudo add-apt-repository ppa:hzwhuang/ss-qt5`  
>`$ sudo apt-get update`  
>`$ sudo apt-get install shadowsocks-qt5`  
\
********
>安装privoxy //代理工具  
>`$ sudo apt-get install privoxy`  
> 
>>配置文件地址/etc/privoxy/config   
>>
>>在4.1 listen-address 改端口  
>>`Listen-addres localhost:8118`  
>>
>>在5.2加  
>>`forward-socks5 / 127.0.0.1:7070 .`  
>
>>重启privoxy   
>>`$ sudo /etc/init.d/privoxy restart`  
>
>>设置终端环境  
>>`$ export http_proxy="127.0.0.1:8118"`	
>>`$ export https_proxy="127.0.0.1:8118"`  

>>在/etc/rc.local添加开机启动sud  
>>`$ sudo /etc/init.d/privoxy start`  
> 
>>在/etc/profile加   
>>`export http_proxy="127.0.0.1:8118"`  
>>`export https_proxy="127.0.0.1:8118"`  
*****
>安装google拼音  
>
>`$ sudo add-apt-repository ppa:fcitx-team/nightly`  
>`$ sudo apt-get update`  
>`$ sudo apt-get install fcitx fcitx-googlepinyin`  
****
>安装 unity-tweak-tool  
>
>`$ sudo apt-get install unity-tweal-tool`  
****
>删除火狐  
>  
> ``$ sudo apt-get remove firefox``  
****
> 删除libreoffice  
> 
> `$ sudo apt-get remove --purge libreoffice*`   
> `$ sudo apt-get clean`  
> `$ sudo apt-get autoremove`  

****

> 安装新力得  
>
>`$ sudo apt-get install synaptic`   

>安装gdebi  
>
> `$ sudo apt-get install gdebi`  

>安装chromium  
>
>`$ sudo apt-get install chromium-browser`  

>安装sublime text 3  
>  
>`$ sudo add-apt-repository ppa:webupd8team/sublime-text-3`  
>`$ sudo apt-get update`  
>`$ sudo apt-get install sublime-text-installer`  

		
> 安装与配置zsh与oh-my-zsh  
>  
>>1. ubuntu中默认安装了哪些shell   
>>  
>>`$ cat /etc/shells # /etc/shells: valid login shells/bin/sh/bin/dash/bin/bash/bin/rbash`  
>>
>>（有sh、dash、bash和rbash）  
>	
>>2.当前正在运行的是那个版本的shell   
>> 
>>`$ echo $SHELL/bin/bash`   
>
>>3.正式安装zsh、git和wget   
>>获取并自动按照oh-my-zsh：    
>>
>>`$ sudo apt-get install zsh git wget`    
>>`$ wget --no-check-certificate https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh`    
>
>>4.替换bash为zsh：  
>>`$ chsh -s /bin/zsh`  
>
>>5.最后重启：  
>>  
>>`$ sudo reboot`   
>
>[参考文档](http://www.jianshu.com/p/546effd99c35)


>安装ravefinity风格  
>
>`$ sudo add-apt-repository ppa:ravefinity-project/ppa`   
>`$ sudo apt-get update`    
>`$ sudo apt-get install vibrancy-colors`     
>`$ sudo apt-get install ambiance-flat-colors radiance-flat-colors`    

>安装my weather  
>
>>`$ sudo add-apt-repository ppa:atareao/atareao`   
>>`$ sudo apt-get update`   
>>`$ sudo apt-get install my-weather-indicator`   
>
>>删除   
>>
>>`$ sudo apt-get remove my-weather-indicator`   
>>`$ sudo apt-get install ppa-purge` // *源管理器*   
>>`$ sudo ppa-purge ppa:atareao/atareao`  


>安装网易云音乐  
>去[官方网站](http://music.163.com/#/download)下载  

>安装dropbox  
>去[官方网站](https://www.dropbox.com/install?os=lnx)下载  
>
>>或者 源代码安装  
>
>>1. 下载源代码  
>>[下载文件](https://www.dropbox.com/download?dl=packages/nautilus-dropbox-1.4.0.tar.bz2)  
>
>>2. 提取 tarball   
>>`tar xjf ./nautilus-dropbox-1.4.0.tar.bz2`  
>
>>3. 编译  
>>`cd ./nautilus-dropbox-1.4.0; ./configure; make; make install;`  

