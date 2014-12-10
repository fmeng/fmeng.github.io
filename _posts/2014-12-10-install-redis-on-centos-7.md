---
layout: post
title: "Redis install on CentOS_7.0"
categories:
- linus
---

参考资料：<br/>

1. [http://www.redis.cn/](http://www.redis.cn/)
2. [http://redisbook.com/index.html](http://redisbook.com/index.html)
3. [http://redisbook.readthedocs.org/en/latest/](http://redisbook.readthedocs.org/en/latest/)
4. [http://keenwon.com/1335.html](http://keenwon.com/1335.html)

配置环境：<br/>

1. Window7\_64 , Wmware workstation 11
2. bringe net connection
3. redis\_2.8.17
4. CentOS\_7.0\_x86\_64

#### 安装步骤 ####

1. 下载解压安装文件

		su -
		cd /tmp
		wget http://download.redis.io/releases/redis-2.8.17.tar.gz
		tar -zxvf redis-2.8.17.tar.gz
		makdir /usr/local/redis_2.8.17 && cp -r redis-2.8.17/* /usr/local/redis_2.8.17

2. 编译

		cd /usr/local/redis_2.8.17
		make && make install

3. 配置`redis.conf`

		cd /usr/local/redis_2.8.17
		#备份redis.conf
		mv redis.conf redis.conf.bak
		#去除配置文件中的注释和空白行
		cat redis.conf.bak | grep -v "^#" | grep -v "$^" >> redis.conf
修改下面几行<br/>

		daemonize yes
		loglevel notice
		logfile /var/log/redis.log
		dir ./

4. 设置系统的`overcommit_memory`

		echo "vm.overcommit_memory=1" >> /etc/sysctl.conf
		sysctl vm.overcommit_memory=1

5. 添加启动脚本，设置服务，自动启动

		touch /etc/init.d/redis
		chmod +x /etc/init.d/redis
		chkconfig --add redis
		chkconfig redis on
脚本内容：<br/>

		#!/bin/sh
		#
		# redis        Startup script for Redis Server
		#
		# chkconfig: - 90 10
		# description: Redis is an open source, advanced key-value store. 
		#
		# processname: redis-server
		# config: /etc/redis.conf
		# pidfile: /var/run/redis.pid
		 
		REDISPORT=6379
		EXEC=/usr/local/bin/redis-server
		REDIS_CLI=/usr/local/bin/redis-cli
		 
		PIDFILE=/var/run/redis.pid
		CONF="/usr/src/redis-2.8.13/redis.conf"
		 
		case "$1" in
		    start)
		        if [ -f $PIDFILE ]
		        then
		                echo "$PIDFILE exists, process is already running or crashed"
		        else
		                echo "Starting Redis server..."
		                $EXEC $CONF
		        fi
		        if [ "$?"="0" ] 
		        then 
		              echo "Redis is running..."
		        fi 
		        ;;
		    stop)
		        if [ ! -f $PIDFILE ]
		        then
		                echo "$PIDFILE does not exist, process is not running"
		        else
		                PID=$(cat $PIDFILE)
		                echo "Stopping ..."
		                $REDIS_CLI -p $REDISPORT SHUTDOWN
		                while [ -x ${PIDFILE} ]
		               do
		                    echo "Waiting for Redis to shutdown ..."
		                    sleep 1
		                done
		                echo "Redis stopped"
		        fi
		        ;;
		   restart|force-reload)
		        ${0} stop
		        ${0} start
		        ;;
		  *)
		    echo "Usage: /etc/init.d/redis {start|stop|restart|force-reload}" >&2
		        exit 1
		esac


6. 运行、测试服务	

		service redis start
		netstat -anop | grep redis-server
显示结果：<br/>

		tcp        0      0 0.0.0.0:6379            0.0.0.0:*               LISTEN      2529/redis-server *  off (0.00/0/0)
		tcp6       0      0 :::6379                 :::*                    LISTEN      2529/redis-server *  off (0.00/0/0)
		
7. 配置、快捷启动redis-cli

		echo "alias redis-cli=/usr/local/redis_2.8.17/src/redis-cli" >> ~/.bashrc
		source ~/.bashrc
8. 测试

		redis-cli
![](/img/redis_2014_success.jpg)

## 总结： ##

&emsp;&emsp;这次实在见证了shell脚本的威力。学习脚本还要从点滴做起！

	