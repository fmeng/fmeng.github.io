---
layout: post
title: "Wmware Workstation 10.0 网络设置"
categories:
- other
---

## 安装 ##

Wmware Workstation 10.0 （中文）下载连接：<br/>
链接：[http://pan.baidu.com/s/1kT3fWdL](http://pan.baidu.com/s/1kT3fWdL) 密码：z65f<br/>
序列号1：JZ6WK-4529P-HZAA1-9RAG6-33JNR<br/>
序列号2：5F4EV-4Z0DP-XZHN9-0L95H-02V17<br/>
**如果安装,请使用工具清理干净卸载残留，重新安装。卸载工具需要google下载！**

## 网卡设置 ##

&emsp;&emsp;关于网卡的设置，实在是有点乱。自己电脑实体机的网卡、虚拟出来的网卡vmnet0、vmnet1、vmnet8、再加上虚拟机系统的网卡实在是让人头疼。在这里，我觉得有必要先纠正一下叫法。其实只有自己电脑的实体网卡才算的上真正的网卡，其他的都不能称之为完整意义的网卡，应该就它们**网络接口**。你应该清楚**连接网络的实体应该是网络接口，而不是网卡。**在linux上面一张网卡是可以虚拟出来两个以上的网络接口的，两个网络接口是可以配置两个ip的。

&emsp;&emsp;选择不同的虚拟网卡（也就是网络接口）就是让vmware workstation 这款软件在为虚拟提供网络的时候选择的网络连接方式。vmware workstation 选择不同网络接口就是选择不同的网络连接方式（连接方式包括桥接，nat，host=only）
### 1.Bridge(桥接模式) ###

![](/img/bridge.gif)

&emsp;&emsp;通过上层拓扑（可以是路由器，也可能是交换机，不是宿主主机）提供ip连接到网络，和真实的主机连接到网络中没有区别。有没有dhcp服务主要依赖于上层网络拓扑是否提供该服务。

&emsp;&emsp;**在桥接模式下，虚拟主机和实体机一样使用自己的网络接口 vmnet0接入网络。**vmnet0网卡只是起一个桥接的作用，只是作为一个虚拟网络和真实网络进行数据转换的桥梁。接入方式是依赖于独立于宿主主机的网络拓扑的。

### 2.Nat模式 ###

![](/img/nat.gif)

&emsp;&emsp;宿主主机通过vmnet 8网络接口虚拟一个支持DHCP服务的三层设备（可以是路由器或者三层及交换机），由这个虚拟设备设备提供vmware workstation 这款软件的接入网络。

&emsp;&emsp;由于是wmnet8接口虚拟的网络接口提供给虚拟机ip地址，虚拟机所有的数据包发送都要通过**wmnet8的网关转发**。

### 3.Host-only ###

![](/img/host-only.gif)

&emsp;&emsp;该网络连接模式和nat连接模式相似。只是虚拟出来的网络接口没有接入互联网。能提供DHCP服务为各个虚拟机分配ip地址，但是**各个虚拟机不能接入互联网，只能在vmnet1这个虚拟接口上进行数据转换。**

## 总结 ##

&emsp;&emsp;一开始理解基本的概念错误，走了不少弯路。自己电脑实体机的网卡、虚拟出来的网卡vmnet0、vmnet1、vmnet8、再加上虚拟机系统的网卡实在是让人头疼。网卡（实体网卡）和接口（虚拟网络接口）的概念没有分清楚。还要明白，**vmnet0、vmnet1、vmnet8宿主机和VMware Workstation 的关系**。以后，问题出来了要从基本概念理解，才能找到问题的根本原因。