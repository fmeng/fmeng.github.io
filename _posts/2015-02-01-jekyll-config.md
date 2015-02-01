---
layout: post
title: "Jekyll环境搭建"
categories:
- linus
---
1. 搭建环境centos 7.0<br/>
[http://jekyllrb.com/docs/installation/](http://jekyllrb.com/docs/installation/)

2. 安装ruby环境

	yum install -y ruby rubygems ruby-devle
3. Gem修改国内的源
	
		gem sources --remove https://rubygems.org/
		gem sources -l
		gem sources -a https://ruby.taobao.org

3. 安装nodejs

		#Run as root on RHEL, CentOS or Fedora:
		curl -sL https://rpm.nodesource.com/setup | bash -
		yum install -y nodejs
4. 安装jekyll

		#安装过程没有显示过程，比较缓慢，耐心等待。
		gem install jekyll
5. jekyll安装成功<br/>
![](/img/jekyll.jpg)

6. 启动项目
		
		#指定端口
		jekyll server -P 4000

7. 修改防火墙
		

## 注意： ##
&emsp;&emsp;在修改gitolite的配置文件的时候，只能通过git先把repo中的配置文件clone下来在进行修改，push。 可以通过 `git clone git@127.0.0.1:jinantth.github.io`进行clone。但是这里虽然都是在同一个主机clone文件，也要添加用户到gitolite中。

**添加用户到gitolite**<br/>
1. 修改 `config/gitolite.conf`
2. 添加公钥到 `keydir`