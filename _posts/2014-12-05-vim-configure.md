---
layout: post
title: "vim 配置"
categories:
- linus
---
#### 环境 ####
**CentOS\_x86\_64<br/>**
**Vim\_7.2.411** <br/>
参考:<br/>
[http://stackoverflow.com/questions/3173963/useful-vim-plugins-for-web-development-and-design-php-html-css-javascript](http://stackoverflow.com/questions/3173963/useful-vim-plugins-for-web-development-and-design-php-html-css-javascript)<br/>

[http://spf13.com/post/the-15-best-vim-plugins](http://spf13.com/post/the-15-best-vim-plugins)

## 准备 ##
&emsp;&emsp;**在安装之前，一定要卸载原有的vim安装最新版本的vim！！！**<br/>
下载<br/>
[http://www.vim.org/download.php](http://www.vim.org/download.php)<br/>
1.卸载<br/>

	#列出所有vim相关的包
	rpm -qa | grep vim
	#卸载相关的包
	rpm -ev vim-enhanced
	rpm -ev vim-common
	#卸载软件包，解决依赖关系
	rpm -ev --nodeps vim-minimal

2.安装<br/>
出现编译错误,缺少iconv的相关libvim<br/>
[http://www.letuknowit.com/post/28.html](http://www.letuknowit.com/post/28.html)

	checking size of off_t... configure: error: in `/home/fmeng/vim74/src':
	configure: error: cannot compute sizeof (off_t)
	See `config.log' for more details.

解决方法<br/>

	echo "/usr/local/lib" >> /etc/ld.so.conf
	/sbin/ldconfig

#### ~/.vim的目录结构 ####

	[fmeng@sky]~/.vim% pwd
	/home/fmeng/.vim
	[fmeng@sky]~/.vim% tree
	.
	├── autoload
	│   ├── ctrlp
	│   │   ├── bookmarkdir.vim
	│   │   ├── buffertag.vim
	│   │   ├── changes.vim
	│   │   ├── dir.vim
	│   │   ├── line.vim
	│   │   ├── mixed.vim
	│   │   ├── mrufiles.vim
	│   │   ├── quickfix.vim
	│   │   ├── rtscript.vim
	│   │   ├── tag.vim
	│   │   ├── undo.vim
	│   │   └── utils.vim
	│   └── ctrlp.vim
	├── colors
	├── doc
	│   ├── ctrlp.txt
	│   └── NERD_tree.txt
	├── plugin
	│   ├── ctrlp.vim
	│   └── NERD_tree.vim
	├── readme_ctrlp.md
	└── syntax
	    └── nerdtree.vim

#### 1. ctrlp ####

[http://www.vim.org/scripts/script.php?script_id=3736](http://www.vim.org/scripts/script.php?script_id=3736)<br/>
帮助文档<br/>
[http://blog.codepiano.com/2013/12/10/ctrlp-chinese-doc/](http://blog.codepiano.com/2013/12/10/ctrlp-chinese-doc/)

	#穿件vim配置文件存放的目录
	mkdir ~/.vim
	#解压文件
	unzip ctrlp.zip -d ctrlp
	#把所有的文件cp到vim响应的目录

使用：`ctrl + p` 使用，`ctrl + c` 退出

#### 2. nerdtree ####

[http://www.vim.org/scripts/script.php?script_id=1658](http://www.vim.org/scripts/script.php?script_id=1658)<br/>
帮助文档<br/>
[http://segmentfault.com/blog/kaizhuqin/1190000000668624](http://segmentfault.com/blog/kaizhuqin/1190000000668624)<br/>
使用方法: `F3 `调出nerdtree

#### 3. StupidEasyMotion ####

[https://github.com/joequery/Stupid-EasyMotion](https://github.com/joequery/Stupid-EasyMotion)<br/>
使用方法： <br/>
`<Leader><Leader>w `- make every word a target<br/>
`<Leader><Leader>fx `- make every character x in the line a target

#### 4. surround ####

[http://www.vim.org/scripts/script.php?script_id=1697](http://www.vim.org/scripts/script.php?script_id=1697)
jj
### 5.配置~/.vimrc ###

	"设置每行计数
	set nu
	"设置特殊语法高亮
	syntax on
	
	"设置自动换行缩进
	set cindent
	set ts=4
	set expandtab
	
	"设置颜色主题
	colorscheme desert
	
	"设置Easymotion快捷键
	"map <C-O> <Leader><Leader>w 
	"map <C-E> <Leader><Leader>f 
	
	"设置NERDTree快捷键
	map <F2> :NERDTreeToggle<CR>
	map <F3> :NERDTreeFind<CR>
	
	"设置搜索时忽略大小写
	set ignorecase
	"设置状态行下划线
	set ruler
	"设置磁动保存
	set autowrite
	
	"解决vim退出时延问题
	set ttimeout
	set ttimeoutlen=100
	set shell=bash\ -i
	
	"解决backspace失灵问题
	set nocompatible
	set backspace=indent,eo1,start


