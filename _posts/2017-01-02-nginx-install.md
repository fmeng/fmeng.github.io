---
layout: post
title: "ngix配置"
categories:
- linus
---

### 需要模块 ###
1. nginx-1.6.1.tar.gz
2. openssl-fips-2.0.5.tar.gz
3. pcre-8.21.tar.gz&emsp;&emsp;正则表达式函数库
4. zlib-1.2.7.tar.gz&emsp;&emsp;压缩函式库
5. rarlinux-4.0.1.tar.gz&emsp;&emsp;RAR压缩工具<br/>
   下载连接：<a href="https://pan.baidu.com/s/1eRO2AEu">https://pan.baidu.com/s/1eRO2AEu</a>

### 安装 ####
1. rarlinux
   		tar -zxvf rarlinux-4.0.1.tar.gz
   		cp -f rar/rar_static /usr/local/bin/rar
   		# rar解压
   		rar -x fileName.rar
2. zlib
		tar zxvf zlib-1.2.8.tar.gz
		cd zlib-1.2.8
		./configure --prefix=/usr/local/zlib
		make && make install
3. pcre
		
4. openssl-fips
5. nginx

