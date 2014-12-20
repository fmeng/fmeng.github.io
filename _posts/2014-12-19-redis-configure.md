---
layout: post
title: "Redis高级应用"
categories:
- linus
---
1. <a href="#self">安全性</a>
2. <a href="#copy">主从复制</a>
3. <a href="#event">事务处理</a>
4. <a href="#sync">持久化机制</a>
5. <a href="#sub">发布订阅消息</a>
6. <a href="#vm">虚拟内存的使用</a>
7. <a href="#sumup">总结</a>

<a name="self"></a>
## 安全性 ##
&emsp;&emsp;**因为Redis的处理速度非常快。所以，我们要设置一个比较长、比较复杂的认证密码。**

	#1. 设置配置文件
	cat "requirepass fmeng" >> /path/to/redis.conf
	#2.启动redis-cli时，通过验证
	redis-cli -a fmeng
	#或者进入redis-cli客户端
	auth fmeng

<a name="copy"></a>
## 主从复制 ##

&emsp;&emsp;实现多个slave server和master server实现数据同步。**只需要在从服务上的配置文件中修改相关的参数即可。**
	
1. master配置
		
		#为了安全，我们要为master配置验证密码
		echo "requirepass fmeng" >> /path/to/redis.conf
2. slave配置

		#指定master主机的ip和端口
		echo "slaveof ip 6379" >> /path/to/redis.conf

<a name="event"></a>
## 事务处理 ##
&emsp;&emsp;Redis的事务处理的支持不是很完善。**不能回滚，事务处理中的某个操作。**

	#事务开始
	multi
	set name fmeng
	set age 10
	#1.执行事务
	exec
	#2.取消事务

#### 在事务处理过程中，同步问题 --乐观锁####
&emsp;&emsp;因为redis支持多个连接同时连接数据库，这样就会引来数据的同步问题。在master和slave主机信息不同步的情况下，如何解决数据同步问题？---乐观锁。
**乐观锁会记录当前数据库中的数据版本，如果在同步过程中，当前要提价的数据小于要提交的的远程数据，是不能提交的。**

	#在事务执行之前，检测当前要操作的key的版本
	watch name
	#事务开始
	multi
	set name fmeng
	set age 10
	#1.执行事务
	exec
	#在exec后，先进行判断。
**判断name这个key在修改过程中，是否有其他线程（会话、连接）对name的值进行更改。如果有更改，就会执行失败，**  `set age 10`  **也会执行失败。**通过加锁的形式可以解决，redis事务回滚的不足。

----

1. 可以使用watch监视多个key的变化。
2. **watch监视的key，是对本次连接的、本次事务有效。(有些人理解错了)**
3. **只能在`multi`代码执行的前面执行`unwatch`才有效。在multi和exec之间不生效！**
4. `discard`可以再`multi`之后，`exec`之前清除事务。**如果之前设置了watch，不会对下次的事务有影响！**
5. **watch只能绑定在本链接中的，挨着它最近的事务。无论事务执行成功与否，watch都不会对下次事务产生影响。**

<a name="sync"></a>
## 持久化机制 ##
>redis的数据存在内存中是不安全的。我们可以采取一种机制，把内存中的数据同步到硬盘中。这就是redis的持久化机制。

1. snapshotting(快照)（默认方式）--更新文件的版本

	数据库文件自动保存到`/path/to/redis/bin/dump.rdb`文件中。dump.rdb是以二进制的形式保存的。配置数据同步的周期。配置文件`redis.conf`<br/>

		#在900秒内，如果有至少1个key被修改，就自动同步数据到文件中
		save 900 1
		#在90秒内，如果有至少10个key被修改，就自动同步数据到文件中
		save 90  10
		#2个配置是可以并存的
	优缺点：很有可能在900这个时间间隔中，丢失数据。如果时间设置太短，硬盘读写数据就会太快，对硬盘有损害，性能低下。同时，在写入硬盘过程中，由于读写速度过快，操作系统也会采用缓冲写入的方式，这样一来又会引来并发访问的同步问题。
2. append-only file (aof 缩写) ---保存执行的命令
	
	redis会把执行的命令保存到`/redis/bin/appendonly.aof`文件中。下次redis启动时，读取指令，恢复数据。配置`redis.conf`
		
		#使用aof的形式同步数据
		appendonly yes

		#1.收到命令就立即写入磁盘
		appendfsync always

		#2.每秒中，往磁盘中写一次
		appendfsync everysec

		#3.依赖os，在i/o空闲的时候写入
		appendfsync no
	
	优缺点：这种配置内存中数据的同步化方式，虽然灵活，但是命令都是以明文的形式保存到appendonly.aof文件中的，有安全性忧患。
<a name="sub"></a>
## 发布订阅消息 ##
>发布订阅（publish/subscribe）是一种消息通讯模式，主要的目的是消除发布者和订阅者之间的耦合。redis可以作为一个pub/sub的sever，在订阅者和发布者起到一个消息路由的作用。**发布者设置channle(频道)让订阅的接受信息，订阅者则可以监听发布者提供的频道的状态。**

1. subscribe(订阅者)--session1
		
		#该session监听channle1，看是否有信息通过这个频道发送
		subscribe channle1

	**显示这个session通过channle1接受到的信息：**

		127.0.0.1:6379> subscribe channle1
		Reading messages... (press Ctrl-C to quit)
		1) "subscribe"
		2) "channle1"
		3) (integer) 1
		1) "message"
		2) "channle1"
		3) "fmeng"
	**可以设置多个session，每个session可以监听多个channle。**
2. publish（发布者）--session2
		
		#通过channle把信息发送出去
		publish channle1 fmeng

#### 其他命令 ####

1. `pusubscribe`

		通过匹配模式，取消订阅信息
		h?llo subscribes to hello, hallo and hxllo
		h*llo subscribes to hllo and heeeello
		h[ae]llo subscribes to hello and hallo, but not hillo
2. `psubscribe`

		通过匹配模式，订阅channle信息
		匹配模式，同上
3. `unsubscribe`(取消订阅)

		#1.参数为空,取消这个会话的所有订阅信息
		unsubscribe 
		#2.有参数,取消channle1的订阅信息
		unsubscribe channle1

通过(pub/sub),可以实现简单的聊天系统。
<a name="vm"></a>
## 虚拟内存的使用 ##

目的：节省内存、提高性能。<br/>
把那些不是经常使用的数据放到硬盘上。但是redis的虚拟内存是基于OS上的文件的，不像linux把数据存放到交换分区。

配置`redis.conf`

	vm-enabled yes         #开启vm功能
	vm-swap-file /tmp/redis.swap
	vm-max-memory 1000000  #1G内存
	vm-page-size 32        #每个页面的32个字节
	vm-page 134217728      #最多使用多少个页面
	vm-max-thread 4        #用于执行硬盘换入的线程数量


<a name="sumup"></a>
## 总结 ##

&emsp;&emsp;redis的高级性能，部分功能还不是很成熟。例如，redis的事务支持，不能自动回滚。同时，我觉的redis在安全方面做的还不够，这样就依赖其他防火墙设置必要的安全措施。


