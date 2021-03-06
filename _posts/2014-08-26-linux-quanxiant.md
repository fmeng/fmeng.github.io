---
layout: post
title: "linux权限总结"
categories:
- linus
---
写在前面的话：<br/>
如果你真的喜欢linux，我希望你能放下windows的包袱，细心的体味这个绝佳的艺术品，并且尝试着把自己的思想向“Everything is a file”靠拢。
>"Everything is a file" describes one of the defining features of Unix, and its derivatives — that a wide range of input/output resources such as documents, directories, hard-drives, modems, keyboards, printers and even some inter-process and network communications are simple streams of bytes exposed through the filesystem name space.

## 让人头疼的RWX ##

&emsp;&emsp;起初，我对可读、可写、可执行的权限控制并不是十分透彻。以至于，后来闹了不少笑话。例如，自己的文本文件，是三个权限都有，就是无法进入目录对自己的文件操作。“一切皆文件”的思想是linux系统的精华，我觉得在理解文件权限的时候，尽可能的想这个思想靠拢，理解起来更容易。理解RWX的权限，分两部分：文件的RWX，目录（一种特殊的文件）的RWX。


#### 文件的RWX ####
1. R，文件的可读权限，比价容易理解。说的是文件是否可以被相应的软件打开，从而被识别出来。例如，文本文件用vim打开，图片文件被相应的图片读取软件打开。**cat命令，vim命令。**
2. W，文件的可写权限，说的是文件的内容是否可以被更改。也就是使用相应的编辑机修改文件。请注意，这里的W权限是不能修改文件的存在性状态的。意思就是，文件被删除了 ，在目录里面添加一个文件，是不受W权限控制的。**vim的编辑命令**
3. X，可执行权限。可以简单理解为该文件是否可以支配操作系统的资源。分两种情况，第一种是二进制文件。这种文件可以直接和系统交互，是个名副其实是可执行。另一种情况就是，解释性执行的脚本，例如shell script，这种脚本在执行的过程中，是同过shell解释执行的，本人认为不算实际意义上的可执行文件。但是，在脚本里面条加上`#!/bin/bash`的语句，系统就可以为之找到适合的解释器，执行脚本。**"/usr/bin/bash&emsp;shell_script.sh"**

#### 目录的RWX ####
&emsp;&emsp;目录也是一个文件。不要把目录理解成一个盛放文件或者目录的容器，这是我们抽象文件组织的底层结构的一种理解模型。其实，目录在底层是通过文件的形式组织文件中的数据的。

1. R,**目录的可读权限就是，目录文本的内容是否可以被读取，文件的内容则是其目录下的文件**也是就是说，这个权限控制的目录的内容是否可以被展示出来。
2. W,可写，当然是是否可以修改文本的内容了。但是，**这里的文本内容是目录下文件的索引，修改文件的索引，可以修改文件的名字，添加一个文件**。要知道在linux中组织文件的形式和windows是不同的。**在linux中，文件名和文件时分开的，文件名存储在上级目录中。** 对应命令，**mv&emsp;mkdir&emsp;touch file&emsp;rm**
3. X，目录的可执行权限，和文件的可执行权限完全是两码事。因为，目录的既不是二进制文件也不是脚本，怎么能被“执行”呢？这里目录的可执行权限，说的是是否可以从目录上一级的结构切换到下一级的结构。**cd**


#### 事实证明一切--做一下测试吧 ####

<table class="meng">
<tr><td>&emsp;目录&emsp;</td><td>&emsp;文件&emsp;</td><td>表现状态</td></tr>
<tr><td>_ _ X</td><td>* * * </td><td>虽然有X，但是没有R，W，只能自由切换目录使用cd命令</td></tr>
<tr><td>_ W _</td><td>* * * </td><td>没有X的权限，不能进入目录，不能在目录创建文件不能ls</td></tr>
<tr><td>R _ _</td><td>* * * </td><td>没有X的权限，不能再目录创建文件，能ls但是只能查看文件名，不能查看文件权限</td></tr>
<tr><td>_ W X</td><td>* * * </td><td>可以进入目录，也可创建文件，不能ls</td></tr>
<tr><td>R _ X</td><td>* * * </td><td>可以进入目录，可以ls，不可以创建文件</td></tr>
<tr><td>R W _</td><td>* * * </td><td>只能ls查看文件名，不能查看文件的权限</td></tr>
<tr><td>R W X</td><td>* * * </td><td>^.^ 这个就不用说了</td></tr>
</table>

#### 总结： ####

1. **先看有没有X的权限，可执行的权限直接控制W的权限。如果没有X的权限，W的权限是不起作用的。**
2. **如果内有X的权限，ls只能查看文件中的文件名，不能查看目录中文件的权限。**
3. **一定要分清目录的RWX和文件的RWX的区别。**


## 不被关注的SUID,SGID,SBIT ##

&emsp;&emsp;3个特殊权限一直被我忽略。但是同时也是很重要的。SUID和SGID解决的是具有可执行权限的二进制文件的一种权限控制。SBIG是对目录文件中各个文件所有者的一种权限控制。**SUID为4，SGID为2，SBIT为1**

#### SUID和SGID ####

&emsp;&emsp;`ls -l /etc/passwd`**我们可以查看到，只有该文件的所有者ROOT用户才有可写的权限。其他用户是没有可写的权限的，也就是在这种情况下，只有ROOT才可以修改该文件，从而修改用户的密码。但是，事实并非如此，别忘了普通即使没有ROOT权限，也是可以修改自己的密码的。这就是SUID和SGUID搞得鬼了。**

&emsp;&emsp;**其实，我们可以明朗一下程序的执行过程：操作者（用户，用户组）、操作（可执行的文件）、被操作对象（一般是文件）。**

**我们通过RWX的权限控制，把操作者和被操作对象联系在一起，其中忽略了操作的过程。但是，这个过程也是可以被我们很好的利用起来，来解决linux中的权限问题。所谓的操作也就是一些可执行的程序（二进制、脚本），我们通过对这些操作进行一些权限控制。但是，对操作的权限控制，如果涉及到脚本的话，就比较困难。因为，脚本是被其他的解释器解释运行的，在所有的解释器中都植入一个权限控制机制，实在太难了。所以，我觉的这也是SUID和SGID只对二进制文件有效的原因。**

**适用该权限的条件：**

1. 执行者要对二进制的文件有可执行X的权限
2. SUID，SGID只对二进制文件有效，对脚本无效
3. 本权限只有在二进制文件执行的过程中有效
4. 执行者的身份权限变成被操作用户所有者的权限。

&emsp;&emsp;对于第四条的理解，普通用户要修改密码时，发现自己在`/etc/passwd`文件上没有可写的权限，但是发现自己的操作`/usr/bin/chpasswd`有SUID的权限，于是执行者在操作文件上的权限就变成了，文件所有这的ROOT。

**命令**

ls -l /usr/bin/locate
## 做好内部安全SELinux ##

同时也引起了不少麻烦。例如，即使我给tomcat的工作目录赋予合适的权限（RWX），但是tomcat在执行

## 网络安全配置 ##

#### Net Filter ####

&emsp;&emsp;主要是对数据包的首部进行过滤。<br/>
&emsp;&emsp;过滤的数据包有：mac、ip、tcp、udp、icmp等。主要过滤OSI的2、3、4层。<br/>


#### Tcp Wrappers ####

&emsp;&emsp;**tcp wrappers通过客户端想要的程序的程序的文件名，分析客户端的ip，看看是否需要放行。**

1. 分析什么样的程序--两种程序

	1. 有super daemon（xinetd）所管理的服务。

		查看由super deamon的服务 `ls /etc/xinetd.d` 或者 开启服务 `chkconfig xinetd on` 查看服务 `chkconfig --list`<br/>
		`yum search xinetd`<br/>
		安装xinetd服务 `yum install xinetd`
		
	2. 支持libwrap.so模块的服务。	

2. 如何分析ip

#### Iptables ####