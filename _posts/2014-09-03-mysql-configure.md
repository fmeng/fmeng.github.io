---
layout: post
title: "Mysql安装问题"
categories:
- other
---

&emsp;&emsp;Myql在windows上的安装方式有两种。1. 使用msi文件Next，Next，Finish安装。
2. 使用压缩包安装。

---
&emsp;&emsp;最近在安装MySQL过程中出现了，一系列的问题。使用第一种方式安装，mysql一直无法启动，报了各种错。大体有，注册表问题（window是坑爹大作），服务配置问题。就是我以前安装过Mysql，由于没有删除干净，所以再次安装就出现了问题。

&emsp;&emsp;在window上安装软件，很难避免注册表问题。以后如果安装开元软件，有压缩包软件，直接拿过来修改配置文件，然后使用，不要为了临时的方便，带来一些不必要的麻烦。

## Mysql安装过程 ##

1. [Mysql下载](#)<br/>
[http://dev.mysql.com/downloads/mysql/](http://dev.mysql.com/downloads/mysql/)
2. [Mysql安装](#)<br/>

#### 解压到指定目录 ####

![解压到指定目录](/img/zip_mysql.jpg)

#### 从my-default.ini生成配置文件 ####
`my.ini`文件。


	[mysql]  
	#设置mysql客户端的字符集  
	default-character-set = utf8
	
	[mysqld]
	
	#绑定IPv4和3306端口
	bind-address = 0.0.0.0
	port = 3306
	
	# 设置mysql的安装目录
	basedir=c:/mysql-5.6.20-winx64
	
	# 设置mysql数据库的数据的存放目录
	datadir=c:/mysql-5.6.20-winx64/data
	
	# 允许最大连接数
	max_connections=200
	
	explicit_defaults_for_timestamp=true 
	
	#设置服务器段的字符集 
	character_set_server = utf8  

#### Mysql配置流程 ####
&emsp;&emsp;**以下都是在命令行窗口下进行的。**

1.Mysql注册服务<br/>

`mysqld --install mysql-5.6.20-winx64 --defaults-file=C:\mysql-5.6.20-winx64\my.ini`<br/>
"mysql-5.6.20-winx64"是指定服务的名字，自己可以选一个舒服的名字。

>如果此时“出现Install/Remove of the Service Denied!”的错误，说明cmd不是以管理员身份运行<br/>
>删除服务 sc delete mysql5  

2.启动和停止服务

启动服务：`net start mysql-5.6.20-winx64`<br/>
停止服务：`net stop mysql-5.6.20-winx64 `<br/>
ps:和linux上服务启动方式不一样。

3.登录MySQL服务器

`mysql -h hostname -u username -p`<br/>
`mysql -hhostname -uusername -p` <br/>

4.修改root密码

	mysql> update mysql.user set password="root" where User="root";  
	mysql> flush privileges;

#### 总结： ####

&emsp;&emsp;一开始，解决Mysql安装问题的时候，把思想囿于使用Next，Next,Finsh的方式，没少折腾了。没有注意微软的坑爹注册表，不是一时半会能解决的，后来才转变思想使用zip安装。

&emsp;&emsp;以后再windows上安装开元软件，能不使用Next，Next，Finish安装，就不使用。避免以后造成不必要的麻烦。
   