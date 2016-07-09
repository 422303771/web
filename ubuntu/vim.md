# vim 使用指南

## vim 插件管理器

### Vundle [git仓库](https://github.com/VundleVim/Vundle.vim)

安装文档有中文指南。

#### 第一步

	$ git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim

#### 第二步

对`.vimrc`文件进行设置。
	
`begin()`和`end()`之间的为可以添加的插件。

插件添加支持多种格式。

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
	
#### 第三步 

在vim中输入`:PluginInstall`来安装插件。

## vim 常用插件指南

### Command-t 

插件[地址](https://github.com/wincent/command-t)

插件需要安装`ruby`，安装完成后，进入目录`~/.vim/bundle/command-t/ruby/command-t`

执行下方命令：

	ruby extconf.rb
	make

之后进入Vim，输入命令`:CommandT`查看效果。

如果提示vim不支持`ruby`，则安装`vim-nox`

	$ sudo apt-get install vim-nox

下图，为正常运行图

![](https://raw.githubusercontent.com/wincent/command-t/media/command-t.gif)


**简要使用说明**

`tab`为选择切换到文件选择。方向键用了选择文件。`enter`确认选择。

