---
layout: post
title: "yum 配置本地yum源"
categories:
- linus
---

#### 参考资料 ####
[http://www.fmeng.me/linus/2014/06/07/install-software-on-centos.html](http://www.fmeng.me/linus/2014/06/07/install-software-on-centos.html)<br/>
[http://blog.csdn.net/fmeng23/article/details/23879509](http://blog.csdn.net/fmeng23/article/details/23879509)

1. 挂载光盘

		mkdir /mnt/cdrom0 && mount -t ios9660 /dev/sd0 /mnt/cdrom0

1. 备份所有的yum源
		
		#备份yum源
		mv CentOS-Base.repo CentOS-Base.repo.bak
		mv CentOS-Sources.repo CentOS-Sources.repo.bak
		mv CentOS-Vault.repo CentOS-Vault.repo.bak
		#新建repo文件
		touch CentOS-Sources.repo 


2. 修改CentOS-Sources.repo文件
		
		#仓库的名字
		[base-source] 
		#仓库的描述
		name=CentOS-$releasever - Base Sources
		#安装镜像的根目录
		baseurl=file:///path/to/centos_ios
		gpgcheck=0
		enabled=1

3. 清除缓存

		yum clean all

## 建议 ##

&emsp;&emsp;这种方式配置的yum源只能使用本地的仓库安装rpm的包。目前阶段没有找到，使用网络和本地源共存的方法。这种更新rpm包的方法，适用于1.有大量的rpm包安装（这些安装包在安装光盘里可以找到）2.不能连接网络，使用`rpm -ihv`安装要解决包之间的依赖关系。