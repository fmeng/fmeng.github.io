---
layout: post
title: "Linux 复习"
categories:
- linus
---
1. [http://linux.51yip.com/](http://linux.51yip.com/)&emsp;&emsp;<font color=red>**linux手册中文（包含其他中文手册！！！）**</font>
2. [http://man.linuxde.net/](http://man.linuxde.net/)
3. [鸟哥的私房菜 Oline](http://cn.linux.vbird.org/)
4. [鸟哥的私房菜 CHM](http://pan.baidu.com/s/1hs4TXeG)&emsp;&emsp;iu9n
5. [鸟哥的私房菜 PDF](http://pan.baidu.com/s/1nv0afqD)&emsp;&emsp;csqm

---
1. `cp -R .fmeng.txt /home/fmeng`
1. find
2. tar
	1. gz<br/>压缩：`tar -zcvf fileName.tar.gz dirName`<br/>解压：`tar -xzvf fileName.tar.gz `
	2. bz2<br/>压缩：`tar -jcvf fileName.tar.bz2 dirName`<br/>
	解压：`tar -jxvf fileName.tar.bz2`
3. yum、rpm
	1. rpm
		1. 安装：`rpm -ivh --test packge.rpm`(只测试不安装)
		2. 卸载：`rpm -e sudo`
		3. 升级：`rpm -Uvh packge.rpm `
		4. 文件隶属的rpm包：`rpm -qf /etc/services`
		5. 查询软件包的信息：`rpm -qi packge.rpm`(已安装) <br/>`rpm -qip pcakge.rpm`(还未安装)
		6. 查询安装包安装了那些文件在系统中：`rpm -ql packge.rpm`(显示文件的绝对路径)<br/>
		`rpm -qlp packge.rpm`(还未安装)
		6. 查询软件包的帮助文档：`rpm -qd package.rpm`
		7. 查询软件包的配置文件：`rpm -qc package.rpm`
		8. 解压所有文件到当前目录：<br/>
		`rpm2cpio coreutils-8.4-43.el6.x86_64 | cpio -idv`<br/>解压指定文件到当期目录：<br/>
`rpm2cpio coreutils-8.4-43.el6.x86_64 | cpio -idv ./bin/ls`
	4. yum
		1. `yum -y install` 
		2. `yum update all`
		3. `yum list | grep sudo`
		4. `yum info sudo`
		5. `yum remove`
	6. 源代码包安装
		1. `tar -xzvf proftpd-1.3.3.tar.gz` (解压)
		2. `cd proftpd-1.3.3`
		3. `./configure --prefix=/usr/local/proftpd`(配置)
		4. `make`
		5. `make install`
	3. 添加用户的配置文件
		1. `/etc/passwd` 、`/etc/shadow`、 `/etc/group`、 `/etc/gshadow`<font color=red>
		2. 用户默认配置文件：<br/>`/etc/login.defs` 、`/etc/default/useradd`
		3. 新用户信息文件(放到用户家目录)：<br/>`/etc/skel`</font>
		4. 登陆信息：`/etc/motd`
	5. <font color=red>setUID=4、**setGID=2**、r=1、w=2、x=4</font>
		1. 当一个<font color=red>可执行程序</font>具有setUID权限，用户执行这个程序时，将以<font color=red>程序所有者的身份执行</font>。（例如：一个可执行程序文件passwd，具有S权限,可执行的该程序的用户是root用户。因为，该passwd具有S权限，<font color=red>普通用户在执行这个程序的时候，可以先变成root用户执行passwd。只限于可执行程序！</font>）
		2. 查找所有setUID程序：`find / -perm -4000 -o -perm -2000`
		3. 粘着位=1。<font color=red>在同一文件夹下，设置其他人对所有者文件的权限，通过设置他们文件共属文件夹的权限粘着位。</font>
		4. useradd、usermod(修改用户参数一样）<br/>
			![](/img/linux101.jpg)
		5. gpasswd<br/>![](/img/linux102.jpg)

----
总结：容易忘记的东西，及时做笔记，查阅方便。

