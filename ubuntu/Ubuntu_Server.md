# Ubuntu Server 16.04 LTS

## 更新：

	$ sudo apt-get update   //更新软件源
	$ sudo apt-get upgrade  //升级软件
	$ sudo apt-get dist-upgrade  //升级系统
	
对于出现：
		   
	x packages can be updated.
    y updates are security updates.

只是每日提示日记。如果想要移除，修改`/etc/update-motd.d/90-updates-available`文件。