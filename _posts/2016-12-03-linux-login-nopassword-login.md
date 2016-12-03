---
layout: post
title: "linux 免密码登录"
categories:
- linus
---

----------

	# 1. 生成秘钥
	ssh-keygen -t rsa
	
	# 2. 登录远程服务器
	ssh root@192.197.47.55 mkdir -p .ssh
	
	# 3. 把本地秘钥复制的远程服务器
	cat .ssh/id_rsa.pub | ssh root@192.197.47.55 'cat >> .ssh/authorized_keys'
	
	# 4. 修改远程秘钥的访问权限
	ssh root@192.197.47.55 "chmod 700 .ssh; chmod 640 .ssh/authorized_keys"
	
	# 5. 测试无密码远程登录
	ssh root@192.197.47.55

