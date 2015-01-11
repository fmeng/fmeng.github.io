---
layout: post
title: "家用路由器设置"
categories:
- other
---
1. <a href="#links">前言</a>
2. <a href="#gainian">基本概念</a>
3. <a href="#5">五种工作模式</a>
	1. <a href="#ap">AP(Access point)模式</a>
	2. <a href="#router">路由模式(Router)</a>
	3. <a href="#repeater">中继模式(repeater)</a>
	4. <a href="#bridge">桥接模式(Bridge)</a>
	5. <a href="#client">客户端模式(Client)</a>
4. <a href="#2">2个路由器互联</a>
	1. <a href="#wan">wan口链接</a>
	2. <a href="#lan">lan口链接</a>

<a name="links"></a>
## 前言 ##
>说明：本文，会从网络的基本原理入手。只建议有一定**网络基础**的同学阅读。

[http://www.today-wx.com/wireless/102.html](http://www.today-wx.com/wireless/102.html)<br/>
[http://service.tp-link.com.cn/detail_article_304.html](http://service.tp-link.com.cn/detail_article_304.html)<br/>
我在网上随便搜了一些有关家用路由器设置到文章。因为作者们的水平各不相同，有的从简单的配置使用入手，有的简单的讲解一些原理，有的原理讲的比较模糊。所以，我打算从网路原理的角度逐步深入家用路由器。好了，在开始之前，我首先要讲几个基本的网路术语。因为，这些术语被滥用的太多了，以至于我们在理解的时候出现了很多偏差。

<a name="gainian"></a>
## 基本概念 ##
2. hub (集线器)，swith（交换机）<br/>
	是一个二层设备。不能阻隔网络（swith的vlan不在讨论的范围内)，没有路由的功能，只能拓宽网络的。**所有链接在二层设备上的工作站都属于同一个网络**。
1. **ap （wireless access point，access point，访问接入点 ……）**<br/>
	是一种接入网络的方法，通过无线接入网络。
3. bridge (桥接)<br/>
	就是通过网桥来实现网络的互联。网桥可以看作一个两口的hub。
4. router （路由）<br/>
	阻隔网络。(内容太多，自悟吧！)
5. **repeater（中继）**<br/>
	这个中继和wlan链接的FR（frame repeater，帧中继）可不是一回事。**这里的中继只起到拓展、加强无线信号的作用。**
6. adapter （网卡，网络适配器）
	分为无线网卡，有限网卡。网卡不同接入网路的方式也不同。
7. internet iP address （pppoe，ADSL拨号)<br/>
	由运营商分配ip地址、子网掩码、网关、dns等，一般需要pppoe验证。
8. static ip （静态ip）
	wan不提供dhcp服务，自己配置ip地址、子网掩码、网关、dns等。ip地址不能冲突。
9. router iP （动态ip）<br/>
	由wan提供dhcp服务，自动分配ip地址、子网掩码、网关、dns等。
10. mac clone （mac地址克隆）<br/>
	有些运营商只允许一个工作站接入，或者指定的工作站接入。要“欺骗”他们就用到这个功能了。
11. **dhcp server （dhcp服务）**<br/>
	由dhcp-server为dhcp-client分配ip地址等参数。
12. ssid （Service Set Identifier，服务集标识）<br/>
	唯一标识一个网络。就是你为自己的路由器取得名字。我们通常所说的禁用ssid，其实是禁用ssid广播。**一般，如果要接入网络的设备比较少，同时为了安全起见，我们会禁用ssid。**
13. **nat (Network Address Translation，网络地址转换)**
	工作在网络层。nat的实现方式有3种：Static Nat（静态转换），Dynamic Nat（动态转换），PAT（Port address Translation,端口多路复用）。**家用路由器使用的是PAT**。

<a name="5"></a>
## 五种工作模式 ##

<a name="ap"></a>
1. **AP(Access point)模式**<br/>
![](/img/access-mode.png)<br/>
- 实现原理：hub+ap，工作在数据链路层，不具有nat功能。<br/>
- 应用场景：要求，wan能提供多个可用ip。例如，在实验室我们可以通过交换机获得多个ip，减小路由器的负载（工作在二层的路由器比工作在三层的路由器轻松的多），我们可以采用这种接入方式。<br/>
- 其他：要接入的设备过多，wan无法满足需求的情况。不适合adsl拨号上网。

<a name="router"></a>
2. **路由模式(Router)**
	![](/img/router-mode.png)<br/>
	- 实现原理：nat+ap，工作在网络层，这里的nat采用的是端口多路复用。<BR/>
	- 应用场景：wan只能提供一个可用ip，lan内有多个设备要接入网络。<BR/>
	- 其他：如果lan内接入的设备过多，路由器负载过重，就会断网、掉线。（实验室，掉线大多是这种原因造成的）

<a name="repeater"></a>
3. **中继模式(repeater)**
	![](/img/repeater-mode.png)<br/>
	- 实现原理：(bridge)通过网桥把两个路由器连接起来。**主路由器和从路由器都提供ap（网络接入）的功能。**能转发三层的数据帧（自己推测），**运行在三层**。<BR/>
	- 应用场景：拓宽网络，网络同属与一个局域网。<BR/>
	- 其他：如果考虑接入方便，就使用这种方式。<br/>

<a name="bridge"></a>
4. **桥接模式(Bridge)**
	![](/img/bridge-mode.png)<br/>
	- 实现原理：(bridge)通过网桥把两个路由器连接起来。**从路由器不提供ap的功能。**，只能转发二层的数据帧（自己推测）**运行在二层**。<br/>
	- 应用场景：拓宽网络，网络同属与一个局域网。<br/>
	- 其他：如果要考虑性能，就使用这种方式。毕竟转发二层的帧比三层的ip报文效率要高。

<a name="client"></a>
5. **客户端模式(Client)**
	![](/img/client-mode.png)<br/>
	- 实现原理：adapter，把路由器用作工作站的无线网卡。<br/>
	- 应用场景：如果主机没有无线网卡，可以通过有限网卡链接路由器，通过路由器的无线功能接入网络。<br/>
	- 其他：现在的应用场景不多。电脑一般都集成无线网卡。

<a name="2"></a>
## **2个路由器互联** ##

要实现连个路由器的互联，一般有两种方法。定义：A路由器（已经接入wan，主路由器），B路由器（通过A路由器接入网路，从路由器）。<br/>
补充知识：直连线和交叉线<br/>
[http://liucw.blog.51cto.com/6751239/1172153](http://liucw.blog.51cto.com/6751239/1172153)
![](/img/lianxian.jpg)<br/>
你看了这张图是不是很疑惑啊。路由器和pc之间的连线分明是直连线。这里为什么是交叉线呢？先说明，我们平时使用的家用路由器和真正的商用路由器是有所不同的。**如果是商用路由器，就要使用交叉线链接pc和路由器。如果使用家用路由器就要使用直连线链接pc和路由器。是因为，家里路由器的生产厂商为了使用方便，把家用路由器和pc的连线做成了直连线。同时，很多家用路由器也是可以通过交叉线和pc相连的。**

<a name="wan"></a>
1. 使用B路由器的wan口链接A路由器。
![](/img/wan-router.jpg)
<br/><br/>
<a name="lan"></a>
1. 使用B路由器的lan口链接A路由器。
![](/img/lan-router.jpg)