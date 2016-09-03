<!--toc-->

- [建立多用户的SSH key](#建立多用户的ssh-key)
	- [1. 生成并添加第一个SSH key](#1-生成并添加第一个ssh-key)
	- [2. 生成并添加第二个ssh key](#2-生成并添加第二个ssh-key)
	- [3. 在.ssh/下创建config文件](#3-在ssh下创建config文件)
	- [4. 测试链接](#4-测试链接)
	- [5. 对于已经创建或已经clone到本地的](#5-对于已经创建或已经clone到本地的)
		- [5.1. 打开`.git/config`](#51-打开gitconfig)
		- [5.2. clone仓库时对应配置host对应的账户](#52-clone仓库时对应配置host对应的账户)

<!-- tocstop -->

----

# 建立多用户的SSH key

## 1. 生成并添加第一个SSH key

```
$ ssh-keygen -t rsa -C "youremail@xxx.com"
```

## 2. 生成并添加第二个ssh key

```
$ ssh-keygen -t rsa -C "youremail@xxx.com"
```

再`Enter file in which ti save the key`输入新名字

## 3. 在.ssh/下创建config文件

```
Host github.com
HostName github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa
Host my.github.com
HostName github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/my
```

_Host 的地址可以随意设定，自己能区分就可以_

## 4. 测试链接

```
$ ssh -T github.com
```

  当返回`HI username`时为设置成功。

## 5. 对于已经创建或已经clone到本地的

### 5.1. 打开`.git/config`

```
#更改[remote "origin"]项中的url中的
#my.github.com 对应上面配置的host
[remote "origin"]
url = git@my.github.com:itmyline/blog.git
```

或者在Git Bash中提交的时候修改remote

```
$ git remote rm origin
$ git remote add origin git@my.github.com:itmyline/blog.git
```

### 5.2. clone仓库时对应配置host对应的账户

```
#my.github.com对应一个账号
git clone git@my.github.com:username/repo.git
```
