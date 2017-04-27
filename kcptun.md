## kcptun 访问加速器

在服务器上使用`kcptun`时，需要使用`socks5`，进行代理

[一键部署包](https://github.com/clangcn/kcp-server)

使用
![snipaste_20170419_200547](/assets/snipaste_20170419_200547.png)

```
==============================================
Your Server IP: 133.130.90.70
   Server Port: 45678
    Server Key: 3OXVKHGDPRclaSS4
    crypt mode: aes
     fast mode: fast2
   compression: enable
           MTU: 1350
        sndwnd: 1024
        rcvwnd: 1024
==============================================
```

随后在 路由或本地设置。

## 部署原因
conoha 貌似对TCP 下载有一定限制，于是使用kcptun进行加速。测试后效果比较理想。

直接访问web没有ss 的效果好，主要用来下载被墙的包

```
/etc/init.d/kcp-server
```
