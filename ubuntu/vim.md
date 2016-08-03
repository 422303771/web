<!--toc-->

- [vim 使用指南](#vim-使用指南)
	- [1.vim 插件管理器](#1vim-插件管理器)
		- [1\. 1 Vundle git仓库](#1-1-vundle-git仓库httpsgithubcomvundlevimvundlevim)
			- [1.2 第一步](#12-第一步)
			- [1.3 第二步](#13-第二步)
			- [1.4 第三步](#14-第三步)
	- [2.vim 常用插件指南](#2vim-常用插件指南)
		- [2.1 Command-t](#21-command-t)
		- [2.2 vim中编写markdown与预览](#22-vim中编写markdown与预览)
		- [2.3 nerdTree](#23-nerdtree)
			- [NERDTRee config NERDTree 设置](#nerdtree-config-nerdtree-设置)

<!-- tocstop -->

----

# vim 使用指南

## 1.vim 插件管理器

### 1\. 1 Vundle [git仓库](https://github.com/VundleVim/Vundle.vim)

安装文档有中文指南。

#### 1.2 第一步

```
$ git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
```

#### 1.3 第二步

对`.vimrc`文件进行设置。

`begin()`和`end()`之间的为可以添加的插件。

插件添加支持多种格式。

```
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()

Plugin 'VundleVim/Vundle.vim'
Plugin 'tpope/vim-fugitive'
Plugin 'L9'
Plugin 'wincent/command-t'
Plugin 'godlygeek/tabular'
Plugin 'plasticboy/vim-markdown'
Plugin 'suan/vim-instant-markdown'

call vundle#end()            
filetype plugin indent on
```

#### 1.4 第三步

在vim中输入`:PluginInstall`来安装插件。

--------------------------------------------------------------------------------

## 2.vim 常用插件指南

### 2.1 Command-t

插件[地址](https://github.com/wincent/command-t)

插件需要安装`ruby`，安装完成后，进入目录`~/.vim/bundle/command-t/ruby/command-t`

执行下方命令：

```
ruby extconf.rb
make
```

之后进入Vim，输入命令`:CommandT`查看效果。

如果提示vim不支持`ruby`，则安装`vim-nox`

```
$ sudo apt-get install vim-nox
```

下图，为正常运行图

![](https://raw.githubusercontent.com/wincent/command-t/media/command-t.gif)

**简要使用说明**

`tab`为选择切换到文件选择。方向键用了选择文件。`enter`确认选择。

--------------------------------------------------------------------------------

### 2.2 vim中编写markdown与预览

需要安装的插件有三个。

```
Plugin 'godlygeek/tabular'
Plugin 'plasticboy/vim-markdown'
Plugin 'suan/vim-instant-markdown'
```

其中`vim-instant-markdown`需要安装`nodejs`、`npm`、`xdg-utils`、`nodejs-legacy`。

安装`instant-markdown-d`

```
$ sudo npm -g install instant-markdown-d
```

其他需要组件：

```
$ sudo apt-get install nodejs
$ sudo apt-get install npm
$ sudo apt-get install xdg-utils
$ sudo apt-get install nodejs-legacy
```

实现的效果为`浏览器预览`与`markdown语法高亮`

安装完成后效果如下图：

![](https://dl.dropboxusercontent.com/u/28956267/instant-markdown-demo.gif)

--------------------------------------------------------------------------------

### 2.3 nerdTree

参考[地址](https://github.com/scrooloose/nerdtree)

[nerdtree-git-plugin](https://github.com/Xuyuanp/nerdtree-git-plugin) git语法提示

[vim-nerdtree-tabs](https://github.com/jistr/vim-nerdtree-tabs) NERDTree tag控制

```
Plugin 'scrooloose/nerdTree'
Plugin 'Xuyuanp/nerdtree-git-plugin'
Plugin 'jistr/vim-nerdtree-tabs'
```

`vim`中输入`PluginInstall`，输入`NERDTree`检查是否运行

在`.vimrc`中添加设置

#### NERDTRee config NERDTree 设置

当指定文件时，自动随VIM启动。

```
autocmd vimenter * NERDTree
```

未指定文件时，加载NERDTree

```
autocmd StdinReadPre * let s:std_in=1
autocmd VimEnter * if argc() == 0 && !exists("s:std_in") | NERDTree | endif
```

隐藏与显示快捷键[Ctrl+n]

```
map <C-n> :NERDTreeToggle<CR>
```

左侧只有一个窗口时，随Vim一起关闭

```
autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTree") && b:NERDTree.isTabTree()) | q | endif
```

--------------------------------------------------------------------------------
