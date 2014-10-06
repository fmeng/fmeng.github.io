---
layout: post
title: "网络复习--Cisco路由器启动"
categories:
- other
---

&emsp;&emsp;先说几句废话。对于这种偏逻辑的东西，我还是建议把逻辑给形象化。用图片的形式展示出来，既有利于分析流程，更有利于复习。虽然，画图会费些功夫，但是绝对是划得来的。

[OSI模型](#tcp_ip)<br/>
[lan物理层的连接](#lan)<br/>
[以太网介质比较](#media)<br/>
[路由器的连接接口_1](#1)<br/>
[路由器的连接接口_2](#2)<br/>
[路由器的连接接口_3](#3)<br/>
[思科路由器硬件和启动流程--思维导图](#setup)<br/>
[路由器的启动流程--流程图](#line_map)<br/>
[路由器配置信息的来源](config_info)<br/>
[NVROM中的16个字节](#nvrom)<br/>
<a name="tcp_ip" id="tcp_ip" href="/img/tcp_ip.jpg">![](/img/tcp_ip.jpg)</a>
<a name="lan" id="lan" href="/img/1_lan物理层的连接.jpg">![](/img/1_lan物理层的连接.jpg)</a>
<a name="media" id="media" href="/img/2_以太网介质比较.jpg">![](/img/2_以太网介质比较.jpg)</a>
<a name="1" id="1" href="/img/路由器的连接接口_1.jpg">![](/img/路由器的连接接口_1.jpg)</a>
<a name="2" id="2" href="/img/路由器的连接接口_2.jpg">![](/img/路由器的连接接口_2.jpg)</a>
<a name="3" id="3" href="/img/路由器的连接接口_3.jpg">![](/img/路由器的连接接口_3.jpg)</a>
<a name="setup" id="setup" href="/img/思科路由器硬件和启动流程.png">![](/img/思科路由器硬件和启动流程.png)</a>
<a name="line_map" id="line_map" href="/img/路由器的启动流程.jpg">![](/img/路由器的启动流程.jpg)</a>
<a name="config_info" id="config_info" href="/img/路由器配置信息的来源.jpg">![](/img/路由器配置信息的来源.jpg)</a>
<a name="nvrom" id="nvrom" href="/img/nvrom中的16个字节.jpg">![](/img/nvrom中的16个字节.jpg)</a>