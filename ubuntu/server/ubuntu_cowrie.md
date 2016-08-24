<!--toc-->

- [cowrie SSH防护](#cowrie-ssh防护)
	- [获取方法](#获取方法)
	- [0. 安装前设置](#0-安装前设置)
		- [0.1 新建用户](#01-新建用户)
		- [0.2 安装python包](#02-安装python包)
		- [0.3 安装 Virtual(可选)](#03-安装-virtual可选)
	- [1. 安装](#1-安装)
		- [1.1 从github克隆](#11-从github克隆)
		- [1.2 复制cowrie](#12-复制cowrie)
		- [1.3 设置端口转发](#13-设置端口转发)
		- [1.4 启用cowrie](#14-启用cowrie)
	- [2. 在`virtual`中使用](#2-在virtual中使用)
		- [2.1 创建virtual](#21-创建virtual)
		- [2.2 启动](#22-启动)
	- [与MySQL结合](#与mysql结合)

<!-- tocstop -->


---

# cowrie SSH防护

## 获取方法

[github](https://github.com/micheloosterhof/cowrie)

[说明文档](http://www.micheloosterhof.com/cowrie/)

[中文参考](http://www.freebuf.com/articles/network/112065.html)

## 0. 安装前设置

### 0.1 新建用户

| 用户 | 密码  |
|--|--|
| cowrie | 0 |

```
sudo adduser cowrie
```

### 0.2 安装python包

```
sudo apt-get install python-twisted python-crypto python-cryptography python-pyasn1 python-gmpy2 python-mysqldb python-openssl python-zope.interface
```
### 0.3 安装 Virtual(可选)

```
sudo apt-get install virtualenv libmpfr-dev
```

---

## 1. 安装

### 1.1 从github克隆

```git
git clone https://github.com/micheloosterhof/cowrie.git
cd cowrie
```

### 1.2 复制cowrie

```
cp cowrie.cfg.dist cowrie.cfg
```

### 1.3 设置端口转发

```
sudo iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 2222
```

### 1.4 启用cowrie

```
./start.sh
```

----

## 2. 在`virtual`中使用

###  2.1 创建virtual


创建`virtual environment`

```
virtualenv cowrie-env
```

激活`virtual`并安装包

```
source cowrie-env/bin/activate
pip install -r requirements.txt
```

退出`virtual`

```
deactivate
```

### 2.2 启动

```
./start.sh cowrie-env
```

## 与MySQL结合

将日记内容写入到MySQL文件，之后g使用laravel获取，在后台对网站安全进行管理。

----
