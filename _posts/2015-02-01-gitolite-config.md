---
layout: post
title: "Gitolite服务器搭建"
categories:
- linus
---

1. links：<br/>	
	- [https://github.com/sitaramc/gitolite/wiki](https://github.com/sitaramc/gitolite/wiki)
	- [https://github.com/sitaramc/gitolite](https://github.com/sitaramc/gitolite)**<这个参考很重要！！！！>**
	- [http://www.ossxp.com/doc/git/gitolite.html](http://www.ossxp.com/doc/git/gitolite.html)
	- [链接：http://pan.baidu.com/s/10c1ci](链接：http://pan.baidu.com/s/10c1ci) 密码：gywd
2. steps to install
3. adding users and repos


----
## steps to install ##
1. **First, prepare the ssh key:**
	1. login to "git" on the server
	2. make sure ~/.ssh/authorized_keys is empty or non-existent
	3. make sure your ssh public key from your workstation has been copied as $HOME/YourName.pub
2. Next, install gitolite by running these commands:
	
		git clone git://github.com/sitaramc/gitolite
		mkdir -p $HOME/bin
		gitolite/install -to $HOME/bin
3. Finally, setup gitolite with yourself as the administrator:
		
		#如果下面这行命令无法执行，请配置环境变量
		gitolite setup -pk YourName.pub
If the last command doesn't run perhaps "bin" in not in your "PATH". You can either add it, or just run:

		$HOME/bin/gitolite setup -pk YourName.pub

## adding users and repos ##

	git clone git@host:gitolite-admin

配置文件`conf/gitolite.conf`

	repo foo
	    RW+         =   alice
	    RW          =   bob
	    R           =   carol
上传配置文件

	git add conf
	git add keydir
	git commit -m "added foo, gave access to alice, bob, carol"
	git push **
提示：关于同一台电脑配置多个账号登陆，请参照 [Git添加多个秘钥](http://www.fmeng.me/linus/2015/01/26/mutil-git-ssh-key.html)

## 总结： ##
&emsp;&emsp;更行比较快的，较新的技术，我们**一定要首先参照官方文档**。虽然，有时候很多文档是英文的，但是我们也可以少走很多弯路！
