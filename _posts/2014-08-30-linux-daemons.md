---
layout: post
title: "Linux系统服务--Daemons"
categories:
- linus
---

#### daemon和service的关系 ####

&emsp;&emsp;**daemon是service的实现形式。service更注重表示程序运行所产生的功能，daemon更注重程序是如何运行的。一般的命名是xx服务，xxd的daemon。service：往往提供一些系统或者网络功能。**

<table class="meng">
<tr><td>daemon的分类</td><td>常驻内存</td><td>受其他进程管理</td><td>安全管理机制</td><td>响应速度</td><td>举例</td></tr>
<tr><td>stand_alone</td><td>是</td><td>否</td><td>无</td><td>快</td><td>httpd&emsp;vsftpd</td></tr>
<tr><td>super daemon</td><td>否</td><td>受inetd或者xinetd管理</td><td>有</td><td>慢</td><td>telnet</td></tr>
</table>

**daemon在什么时候执行？**<br/>
signal-control,由客户端的请求激活。<br/>
interal-control,系统自己每隔一段时间就去检测配置文件，然后自己判断是否执行。例如，atd和crond每分钟检测一下配置文件。

#### 一些常见服务的解释文档 ####

`/etc/services`一些预定的规范，一般不要修改。 <br/>
 http &emsp; 80/tcp&emsp;www&emsp;www-http &emsp;# WorldWideWeb HTTP

#### daemon的启动和配置 ####
&emsp;&emsp;启动脚本要完成那些工作？<br/>
环境的检测、配置文件的分析、PID文件的放置、lock一些资源文件等。
<table class="meng">
<tr><td>描述</td><td>文件存放位置</td><td>举例</td></tr>
<tr><td>启动脚本存放处</td><td>/etc/init.d/*&emsp;链接<br/>实际目录/etc/rc.d/init.d/*</td><td>halt&emsp;sshd&emsp;iptables&emsp;atd</td></tr>
<tr><td>系统初始化配置文件</td><td>/etc/sysconfig/*</td><td>selinux&emsp;rsyslog&emsp;i18n</td></tr>
<tr><td>super&emsp;daemon配置文件</td><td>主进程：/etc/xinetd.conf<br/>从进程：/etc/xinetd.d/*</td><td></td></tr>
</table>