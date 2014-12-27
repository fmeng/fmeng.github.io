---
layout: post
title: "变态的bash shell"
categories:
- linus
---
1. <a href="#links">连接</a>
2. <a href="#1">随机生成10位密码</a>

<a name="links"></a>
[http://commandlinefu.cn/](http://commandlinefu.cn/)

------
<a name="1"></a>

1. 随机生成10位密码

		tr -dc "A-Za-z0-9-\+=\!@#\$\^\&*" < /dev/urandom | head -c 10 | xargs

		#从/dev/urandom中读取随机字符
		#剔除不含有"A-Za-z0-9-\+=\!@#\$\^\&*"的字符
		#取头部的10个字符，head -c 10
		#添加换行符 xargs
2. 打开目录下的所有文件

		vim $(find . ! -path \*.svn\* -type f -iname \*foo\*)
		
		#注意这个用法 vim $(find *****)
3. 打包tar.gz文件
		
		tar -cvf - path | gzip > foo.tar.gz
		

4. tar实现递归复制

		tar -cvf - /home | tar -xvf -