---
layout: post
title: "Linux NTP配置"
categories:
- linus
---

[http://blog.chinaunix.net/uid-20672257-id-3013282.html](http://blog.chinaunix.net/uid-20672257-id-3013282.html)

[http://www.path8.net/tn/archives/6002](http://www.path8.net/tn/archives/6002)

时间服务器

[http://www.douban.com/note/171309770/](http://www.douban.com/note/171309770/)

遇到错误：no server suitable for synchronization found
http://blog.sina.com.cn/s/blog_3f1a25310100qn84.html

#### 时间： ####
1. 软件时间，linux系统的时间，从1970.1.1开始记录的时间参数
2. 硬件时间，bios里记录是时间
3. 网络时间，从NTP服务器获得的时间

#### 使用命令： ####
1. date
2. hwclock
3. ntpdate

#### 参数信息 ####

210.72.145.44 &emsp;为中国国家授时中心服务器地址

**警告：**<br/>
&emsp;&emsp;但是学校外的路由器把NTP配置的端口屏蔽了。
只能自己手动设置时间了。等到服务器接入外网，在多调整吧。

## 关于NTP ##







