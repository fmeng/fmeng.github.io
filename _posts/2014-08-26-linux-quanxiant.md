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
<tr><td>目录</td><td>文件</td><td>表现状态</td></tr>
</table>
## 不为人知的SGU ##

## 做好内部安全SELinux ##

## 网络安全配置 ##