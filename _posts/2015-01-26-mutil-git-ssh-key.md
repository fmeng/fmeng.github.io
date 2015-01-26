---
layout: post
title: "Git添加多个秘钥"
categories:
- linus
---
链接：<br/>
	[http://www.webmaster.me/uncategorized/add-multiple-ssh-keys-on-github.html](http://www.webmaster.me/uncategorized/add-multiple-ssh-keys-on-github.html)

1. 创建 `id\_rsa2.pub` 和 `id\_rsa2`

		ssh-keygen -t rsa -C 'jinantth@163.com'
		#这里输入一个新的ssh key文件名
		#~/.ssh/id_rsa2有时候不能识别，要使用：/c/fmeng/.ssh/id_rsa2
		Enter file in which to save the key (~/.ssh/id_rsa): ~/.ssh/id_rsa2

2. 打开新生成的`~/.ssh/id_rsa2.pub`文件，将里面的内容添加到GitHub后台。 <br/>

	![](/img/add_key_to_github.jpg)
3. 打开`~/.ssh/config`文件（没有则创建），添加一个Host。
		
		#git@github.com:jinantth/jinantth.github.io.git
		#"git@github.com:"替换为指定内容github2
		Host github2
		HostName github.com
		User git
		IdentityFile ~/.ssh/id_rsa2
4. 克隆远端项目，并修改origin
	
		#克隆远端项目
		git clone git@github.com:jinantth/jinantth.github.io.git
		#修改remote值
		git remote set-url origin github2:jinantth/jinantth.github.io.git
5. 测试

## 总结 ##
&emsp;&emsp;理解ssh验证的概念。`~/.ssh/config`的文件**用来在本地配置和远端对应的项目名称，在该文件中可以指定认证远端用户的私钥。**