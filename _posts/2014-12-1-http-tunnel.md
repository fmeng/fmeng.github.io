---
layout: post
title: "Http Tunnel穿透学校防火墙"
categories:
- linus
---

## 原理 ##
[http://stackoverflow.com/questions/22340966/use-httptunnel-htc-hts-to-connect-ssh-server-inside-firewall](http://stackoverflow.com/questions/22340966/use-httptunnel-htc-hts-to-connect-ssh-server-inside-firewall)<br/>
![](/img/http-tunnel.jpg)

#### Http Tunnel ####

官网<br/>
[http://www.nocrew.org/software/httptunnel.html](http://www.nocrew.org/software/httptunnel.html)<br/>
说明<br/>
[http://www.csparks.com/FirewallTunneling/](http://www.csparks.com/FirewallTunneling/)<br/>

## 服务器端配置 ##


## 客户端配置 ##

1. Corkscrew配置<br/>
**这个是客户端的协议转换软件，在下面的配置中没有用到**<br/>
[http://www.linuxhowtos.org/Security/sshproxy.htm](http://www.linuxhowtos.org/Security/sshproxy.htm)<br/>
[http://www.mtu.net/~engstrom/ssh-proxy.php](http://www.mtu.net/~engstrom/ssh-proxy.php)

2.

	htc -F 10022 www.yourserver.com:443
	ssh -R 10080:goodstuff:80 -p 10022 localhost 


## 结果 ##

&emsp;&emsp;在部分网段的内网能访问（没配置前不能访问），但是在外网（北京）还是不能访问。问了一下**安全方面的高手**才知道。要想穿透学校的防火强不是很容易的。学校的防火墙会主动的检测客户端的浏览器端口号，如果发现不是常用的端口，就会拦截。同时在发送https协议的时候也是会携带httptunnel的信息的。防火前也能检测到。**个人猜测，是防火墙断掉了SSL或者TSL。**
