# Unibeast (6.2)


Unibeast不能正常读取镜像的处理办法，镜像为官方APP Store下载。Unibeast需要使用mac os系统。

1.  Open Terminal  
2. Type: cd Desktop (or Applications)  
3. Type: mkdir -p "_MASReceipt"  
4. Type: echo -e '\000com.apple.InstallAssistant.ElCapitan' > _MASReceipt/receipt  
5. A new folder should appear on your desktop (or Applications) called "_MASReceipt" and inside will be a file named "receipt"  
6. Go to Install OS X El Capitan app, right click and select "Show Package Contents" open Contents and delete the folder named "_MASReceipt"  
7. Copy the new "_MASReceipt" you created inside Contents folder of Install OS X El Capitan app  
8. Open Unibeast and you will see the magic!  

使用Unibeast在EGVA X58 sli le 上安装mac os 10.11.5，基本成功。  

创新声卡驱动无解。安装时要设置SMbios，系统版本设置为mac3.1。Nvidia显卡关闭驱动，boot后加`nv_disable=1`暂时关闭显卡驱动。

因为主板不支持EFI，所以设置文件位于硬盘目录下而非EFI分区。