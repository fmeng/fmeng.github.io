---
layout: post
title: "微信公众平台开发的3个难点"
categories:
- java
---

## Session处理 ##
<p>&emsp;&emsp;据我所知，微信公众平台对回话处理支持不够完善。本人认为，主要是微信服务器要在中间环节维持回话，这样就会吃消很多资源，再加上很多用户访问，这种现象就更加明显了。所以，微信服务器公众平台开发文档上，明确声明会主动维持会话5秒。所以，微信公众平台对传统的http的会话处理并不是很完善。</p>

这里是我用wireshark抓的报文。微信服务器返回的头信息有限，没有维持会话的cookie信息或者sessionId。[http://pan.baidu.com/s/1dDxRAJv](http://pan.baidu.com/s/1dDxRAJv)
<p>&emsp;&emsp;这是3种方法来解决微信服务器不支持持久会话的问题。</p>

- 重写Tomcat的源码，只需要修改session的getId()和setId()方法。这里的getId得到的是微信服务器反馈信息的fromUserName，setId设置的也是fromUserName。这样就能巧妙的解决微信服务器对长会话支持不好的问题。本人还是感觉这种方法效率最高，和Tomcat联系紧密。但是修改Tomcat源码也不是一朝半夕能解决的。现在实现技术上有困难。

- 把用户上次访问服务器的信息存入数据库，供下次访问。从而解决上下文的信息交换。但是缺点也是可见的。效率不会很高，还要设计一个系统的数据库来组织上下文数据。

- 使用`System.Web.SessionState.HttpSessionState`设计自己的Session管理规则。这里有实现方法，[点击这里](http://www.cnblogs.com/yank/p/3476874.html)。

## 主动推送 ##

- 使用微信开放的接口，一天只能推送一次。
- 编程实现，使用HttpClient模拟登陆微信公众平台实现回复。[点击这里](http://www.jb51.net/article/47739.htm)。但是现在加上了24小时内回复的限制，无法推送。也就是在你发信息以前的24小时之内，用户没有主动给你发信息。即使你使用微信公众平台网页版管理回复信息，微信服务器是不予转发的。（可能是考虑到，频繁的广告推送，用户体验不好。）

## 智能回复 -Reboot##

想找一个自动回复的API。Lucence + Ik分词器 + Luke有待研究。话说这套东西没有一年很难研究好。<br/>

解决方法：[图灵机器人](http://www.tuling123.com/openapi/)

