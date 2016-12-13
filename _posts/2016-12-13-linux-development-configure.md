---
layout: post
title: "服务器web环境搭建"
categories:
- linus
---

### 系统环境 ###
   ` Linux iZ2ze3jgdl6vry49yfx6f8Z 2.6.32-573.22.1.el6.x86_64 `<br/>
   `#1 SMP Wed Mar 23 03:35:39 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux`

### 配置 ###
1. 相关下载链接: https://pan.baidu.com/s/1mi2QMNm 密码: j3w1
   1. maven-3.0.5.tar.gz
      <br/>版本下载：https://maven.apache.org/docs/history.html
   2. jdk-7u79-linux-x64.tar.gz 
      <br/>版本下载：
      <br/>http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
      <br/>http://www.oracle.com/technetwork/cn/java/javase/downloads/jdk7-downloads-1880260.html
   3. apache-tomcat-8.5.6.tar.gz 
       <br/> 版本下载；http://tomcat.apache.org/
   4. m2.20161213.tar.gz

2. `/etc/profile`<br/>
            export JAVA_HOME=/usr/local/jdk7/jdk1.7.0_79
            export JRE_HOME=/usr/local/jdk7/jdk1.7.0_79/jre
            export PATH=$PATH:/usr/local/jdk7/jdk1.7.0_79/bin
            export CLASSPATH=./:/usr/local/jdk7/jdk1.7.0_79/lib:/usr/local/jdk7/jdk1.7.0_79/jre/lib
            export MAVEN_HOME=/usr/local/maven/maven-3.0.5
            export M2_HOME=/usr/local/maven/maven-3.0.5
            export PATH=${MAVEN_HOME}/bin:${PATH}
            export M2=/usr/local/maven/maven-3.0.5/bin
3. jdk1.7.0_79
            [root@host jdk1.7.0_79]# pwd
            /usr/local/jdk7/jdk1.7.0_79
            [root@host jdk1.7.0_79]# ls -al
            drwxr-xr-x 2 uucp  143     4096 4月  11 2015 bin
            -r--r--r-- 1 uucp  143     3339 4月  11 2015 COPYRIGHT
            drwxr-xr-x 4 uucp  143     4096 4月  11 2015 db
            drwxr-xr-x 3 uucp  143     4096 4月  11 2015 include
            drwxr-xr-x 5 uucp  143     4096 4月  11 2015 jre
            drwxr-xr-x 5 uucp  143     4096 4月  11 2015 lib

4. /etc/init.d/tomcat(以服务的形式启动tomcat时使用)
            #!/bin/bash
            # tomcat 开机自动启动
            # chkconfig: 2345 10 90  
            # description: Starts and Stops the Tomcat daemon. 

            ##################路径改变，需要修改######################
            JAVA_HOME=/usr/local/jdk7/jdk1.7.0_79
            export JAVA_HOME                                                         PATH=$JAVA_HOME/bin:$PATH
            export PATH 

            ##################路径改变,需要修改########################
            CATALINA_HOME=/usr/local/tomcat8/apache-tomcat-8.5.6
            case $1 in
            start)
            sh $CATALINA_HOME/bin/startup.sh
            ;;
            stop)
            sh $CATALINA_HOME/bin/shutdown.sh
            ;;
            restart)
            sh $CATALINA_HOME/bin/shutdown.sh
            sh $CATALINA_HOME/bin/startup.sh
            ;;
            esac
            exit 0
5.  tomcat 安装路径
            [root@host apache-tomcat-8.5.6]# pwd
            /usr/local/tomcat8/apache-tomcat-8.5.6
            [root@host apache-tomcat-8.5.6]# ls -l
            drwxr-x--- 2 root root  4096 12月  5 21:12 bin
            drwx------ 3 root root  4096 12月  5 21:20 conf
            drwxr-x--- 2 root root  4096 12月  5 21:12 lib
            -rw-r----- 1 root root 57092 10月  7 04:18 LICENSE
            drwxr-x--- 2 root root  4096 12月 13 00:26 logs
