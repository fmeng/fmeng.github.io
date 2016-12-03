---
layout: post
title: "CentOS 7 网路配置，修改静态IP，更换主机名"
categories:
- linus
---

----------
1. 配置一些全局的网络参数<br/>
   `/etc/sysconfig/network` 

        # Created by fmeng
        # 配置可供选择的网关
        GATEWAY=192.168.22.1
        # DNS=8.8.8.8
2. 修改主机名<br/>

   	hostnamectl set-hostname hostname --static

3. 配置网路接口参数 <br/>
   `/etc/sysconfig/network-scripts/ifcfg-eno16777736`

        TYPE="Ethernet"
        # 配置静态ip #########
        BOOTPROTO="static"
        IPADDR=192.168.22.143
        NETMASK=255.255.255.0
        NM_CONTROLLED="no"
        #####################
        GATEWAY=192.168.22.1
        DEFROUTE="yes"
        IPV4_FAILURE_FATAL="no"
        IPV6INIT="yes"
        IPV6_AUTOCONF="yes"
        IPV6_DEFROUTE="yes"
        IPV6_FAILURE_FATAL="no"
        NAME="eno16777736"
        UUID="494c27b0-e2ff-4e83-bef5-b0be16cc90b1"
        DEVICE="eno16777736" 
        # 开机自动启动 #########
        ONBOOT="yes"
        ######################
        PEERDNS="yes"
        PEERROUTES="yes"
        IPV6_PEERDNS="yes"
        IPV6_PEERROUTES="yes"
        IPV6_PRIVACY="no"
