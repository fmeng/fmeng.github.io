---
layout: post
title: "关于网络的一些事……"
categories:
- other
---

##实验室网络配置##

1. 分用和复用。什么是？为什么？解决了什么问题？
2. 网关和代理。如何理解？作用？
3. 真Ip，假Ip，虚拟ip，共有Ip，私有Ip……？
4. 有公网Ip就一定能，搭建自己的服务器吗？
5. 花生壳的实现原理

## Http协议 ##
1. 从输入一个url到返回一个网页经历了什么？
2. Http1.0为什么没有Host首部？
3. 如何解决虚拟主机的问题？
4. 并发连接和持续连接
5. 持续连接带来的问题(哑代理、盲中继)，如何解决？
6. 会话机制。为什么需要会话？如何控制会话？
7. cookie的分类：会话cookie，持久cookie


----

**实验室网络配置**<br/>
![skylab](/img/skylab.jpg)<br/>
由于时间原因，没有具体配置网络参数。所以连线节点处显示红色。<br/>
**学校网络简单拓扑图**<br/>
![school](/img/school.jpg)<br/>

**自己电脑的主机ip地址显示**<br/>
![ip](/img/ip.jpg)<br/>
![ip](/img/ipconfig.jpg)<br/>
![ip](/img/windows_ip_configure.jpg)<br/>

**浏览器访问流程**<br/>
![访问流程](/img/vist_web.png)<br/>
![访问流程](/img/http_message.jpg)<br/>
![访问流程](/img/tcp_before_http.jpg)<br/>

**会话控制**<br/>

![会话控制](/img/tcp_before_http.jpg)<br/>
![会话控制](/img/session_chosing.jpg)<br/>

----

网关：协议的转换<br/>
代理：协议的转发<br/>

私有IP地址范围：<br/>
A: 10.0.0.0~10.255.255.255 即10.0.0.0/8<br/>
B:172.16.0.0~172.31.255.255即172.16.0.0/12<br/>
C:192.168.0.0~192.168.255.255 即192.168.0.0/16<br/>

测试工具：
ping， nmap， nc(netcat)，ifconfig traceroute tcpdump wireshark fiddler2<br/>
简单介绍一下实现原理，用途方法。

解释为什么即使有公网ip也被别人访问。<br/>

1. 基本原理：主动拦截了ip报文。
2. 实现方式：在网关制定过滤规则。

----

#### 访问流程： ####
**持续连接：**<br/>
输入Url --> 查找IP地址 --> 发送get请求报文 --> 接受get相应报文 --> 请求到服务器Html文件 --> 在解释文本过程中找到其他资源 --> 继续请求,不切断TCP连接 --> 网页涉及的所有资源请求完毕 --> 呈现网页<br/>

要解决，盲中继、哑代理问题。<br/>
`connetciong:keep-alive`（http1.0） --> `proxy-connection:keep-alive`(http1.0+)--> 默认是持续连接，使用`connection:close`关闭连接。(http1.1)<br/>

**并发连接:**<br/>
输入Url --> 查找IP地址 --> 发送get请求报文 --> 接受get相应报文 --> 请求到服务器Html文件 --> 在解释文本过程中找到其他资源 --> 继续请求，找到所有资源连接，并行发送HTTP报文 --> 网页涉及的所有资源请求完毕（可以再请求过程中就解释网页呈现部分效果） --> 呈现网页<br/>

要解决的问题：
1. 多的Tcp握手报文和释放连接的报文，消耗带宽。
2. 建立和释放连接也会消耗很多主机资源。
3. tcp的流量控制盒拥塞控制，使报文的发送速率成曲线增长。

**会话控制**<br/>
**session是基于cookie的。**<br/>

1. 使用url重写实现会话控制
2. 使用cookie实现会话


&emsp;&emsp;cookie机制，不属于Http的规范。在http1.0的时候是没有，cookie控制的。只能使用一个胖胖的url来携带会话信息。
