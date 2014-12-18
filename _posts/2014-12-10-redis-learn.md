---
layout: post
title: "Redis学习笔记"
categories:
- linus
---

redis:

1. [http://www.redis.cn/](http://www.redis.cn/)
3. [http://redisbook.readthedocs.org/en/latest/](http://redisbook.readthedocs.org/en/latest/)
4. 链接：[http://pan.baidu.com/s/1qW0U4PY](http://pan.baidu.com/s/1qW0U4PY) 密码：7zyr

jedis:

2. [http://tool.oschina.net/apidocs/apidoc?api=jedis-2.1.0](http://tool.oschina.net/apidocs/apidoc?api=jedis-2.1.0)
4. [http://flyingsnail.blog.51cto.com/5341669/1371650](http://flyingsnail.blog.51cto.com/5341669/1371650)
5. [http://www.cnblogs.com/liuling/p/2014-4-19-04.html](http://www.cnblogs.com/liuling/p/2014-4-19-04.html)

## 简介： ##
&emsp;&emsp;Redis是一个开源的，先进的key-value存储。它通常被称为**数据结构服务器**，因为键可以包含**字符串**、**哈希**、**链表****、集合、****有序集合**。

## 应用： ##
1. 取最新的N个数据的操作
2. 排行榜操作，去TOPN
3. 设定过期时间的应用
4. 计数器应用
5. Uniq操作，获取某段时间所有数据的拍重值
6. 实时系统、反垃圾系统
7. pub/sub构建实时操作系统
8. 构建队列系统
6. 缓存


## Redis配置参数详解 ##

[http://www.cnblogs.com/wenanry/archive/2012/02/26/2368398.html](http://www.cnblogs.com/wenanry/archive/2012/02/26/2368398.html)
## 数据类型 ##

## 1.String ##

> string 类型的变量是二进制安全的，也就是可以存放二进制数据。

---
1. set

		set key fmeng
2. setex

		setex color 10 red
		#把颜色的值设置为red，在数据库中存储10秒	
3. setnx

		setnx name fmeng
		#如果name这个值已经设置了，就不设置。反之，就设置。
4. setrange

		set email fmeng123@fmeng.com
		setrange email 9 gmail.com
		get email
		"fmeng123@gmail.com"
		#修改字符串的指定字符

-----

1. mset

		mset key1 value1 key2 value2
		#设置多个key
3. msetnx
		
		msetnx key1 value1 key2 value2
		#设置过个key，如果原来的值存在就不会覆盖，原来的值。
		#如果有一个值是原来存在的，其他值也不会设置成功

----

1. getset

		getset key1 valuess
		#先通过get得到要设置的key，如果拿不到就直接返回nil
		#如果拿到就尽心set设置

-----

3. getrange

		set name fmeng
		getranget name 0 1
		"fm"
		#截取指定长度的字符
1. append

		set name fmeng
		append name 123@gmail.com
		get name
		fmeng123@gmail.com
		#在字符串的后面添加字符
2. incr

		set int 1
		incr int
		get int
		2
		#先判断这个key是不是整形数字，如果是整形就自增1
		#否则，就返回错误	
3. incrby

		set int 1 
		incrby int 10
		get int 
		11
		#指定自增整数的大小，可以为负值
4. strlen
		
		set name femng
		strlen name
		5
		#计算指定字符的长度

## 2.Hashs ##

> Redis的hash是一个String类型的field和value的映射表。它的添加和删除操作都是O(1) 。
hashs特别适用于存储对象。对象的每个属性存储成单个String。将一个对象存储在一个hash类型中会占用比较少的内存，并且方便存取整个对象。

-----
1. hset
	
		hset myhash fmeng meng
		hkeys myhash
		#得到myhash的key为fmeng
		hvals myhash
		#得到myhash的value为meng
2. hsetnx

		hsetnx myhash1 fmeng meng
		hgetall myhash1 
		1.fmeng
		2.meng #用hgetall既可以获得key也可以获得value
		#先判断这个hash的key是否存在，如果存在就返回空
		#如果不存在就执行

----

1. hmset
		
		hmset myhash3 name fmeng color red	
		#设置myhash3的两个key分别为name，value为fmeng	
		#key为color，value为red
	
2. hmsetnx
		
		hmset myhash3 name fmeng color red	
		#设置myhash3的两个key分别为name，value为fmeng	
		#key为color，value为red

		#先判断myhash3中是否有连个value的key，如果存在就放回为空
		#反之，在设置相应的key

----
1. hincrby

		hincrby  hash  key 5
		#把hash中key自增5
2. hexists

		hexists hash name
		#判断hash中的name的key是否存在	
3. hlen

		hlen hash
		#判断hash中有几个变量
4. hdel

		hdel hash name
		#删除hash中的name字段
5. hkeys
		
		hkeys hash 
		#列出hash中的所有key
6. hvals

		hvals hash
		#列出hash中的所有value
7. hgetall
		
		hgetall hash
		#hash中的key和value会一同列出来


## 3.List ##

>list是一个链表结构，主要功能push、pop、获取一个范围内的所有值等等。**操作中的key理解为链表的名字**redis的list其实就是一个双向链表。我们可以push、pop操作list的头部和尾部，实现数据结构上的栈和队列。

1. lpush

		lpush list1 one
		lpush list1 two
		lrange list1 0 -1 
		#从头部插入数据
		#索引的数据是：头部为0，往下递增。
2. lrange

		lrange list 0 -1
		#列出在0 -1 范围内的所有数据
1. rpush

		rpush list meng
		lrange list 0 -1
		1) "two"
		2) "one"
		3) "meng"
		#说明rpush是从尾部插入数据
2. linsert

		#在“meng”的前面添加一个test元素
		linsert list before meng test
3. lset

		lset list2 0 tt
		#把从头部开始的第一个元素的值设为tt
4. lrem

		lrem list2 2 tt
		#从头部开始删除2个、value为tt的元素
		#如果不足2个，则删除一个
		
		lrem list2 0 tt
		#删除所有的tt元素
		
		lrem list2 -2 tt
		#从尾部开始删除元素，知道删除2个tt元素
5. ltrim

		lrange list 0 -1
		1) "two"
		2) "one"
		3) "meng"
		ltrim list 1 2
		#会删除two元素
		#索引是从0，到下面递增
6. lpop

		lpop list
		#从头部删除一个元素，并返回一个结果
7. rpop

		rpop list
		#从尾部删除一个元素，并返回一个结果
8. rpoplpush

		rpoplpush list4 list5
		#从list4的尾部弹出一个元素，push到list5的头部
		#从list4尾部删除，从list5头部添加
9. lindex

		lindex list4 1
		#展示list4中索引为1的元素value值
10. llen
		
		llen list4
		#显示list4的长度


----


## 4.sets ##

> **sets是集合，没有顺序，不允许有重复值**，它是String类型的无序集合。sets是通过hash table实现，添加、删除、查找的复杂度都是o（1）。对sets的操作有：并集、交集、差集。


1. sadd

		sadd myset fmeng
		#向集合myset中添加fmeng
2. smembers

		smembers myset
		#展示myset中所有的元素
3. srem
	
		srem myset fmeng
		#从有序集合myset中删除元素fmeng
4. spop

		spop myset fmeng
		#从myset中删除一个元素，放回删除的元素
		#pop只能是随机的，因为sets是没有顺序的

----
		smembers myset5
		1) "1"
		2) "2"
		3) "3"

		smembers myset6
		1) "2"
		2) "3"
		3) "4"

----
5. sdiff
		
		#取两个集合的不同
		sdiff myset5 myset6
		#显示结果
		1	
6. sdiffstore

		sdiffstore myset5 myset6 myset7
		#把sdiff的结果1存入到myset7中
7. sinter

		#取2个集合的交集
		sinter myset5 myset6
		#显示结果
		2
		3
8. sunion

		#2个集合的并集
		sunion myset5 myset6
		#显示结果
		1
		2
		3
		4
9. smove

		smove myset5 myset6 1
		#把元素1从myset5中、移动到myset6中
		smembers myset6
		1
		2 
		3
		4
10. scard

		#查看集合的元素个数		
		scard myset5
		2
11. sismember

		sismember myset5 4
		#判断4是否为myset5的元素
12. srandmember
		
		#随机显示一个集合中的元素
		srandmember myset5

----

		
## 5. zsets ##

>sorte sets是sets的一个升级版本，它在set的基础上增加了**顺序的属性**。这个属性，在添加、修改的时候可以指定。每次指定这个顺序属性后，zsets会自动调整集合的顺序。可以把zsets理解为两列的mysql表，一列为顺序、一列为value。


1. zadd

		#添加一个元素，指定顺序
		zadd myzset 1 fmeng
		zadd myzset 0 mengmeng
		
2. zrange

		zrange myzset 0 -1
		#按序列好从小到大输出元素的值
		#左边的序列号，是使用zrange是格式化作用。
		#和真正的数据结构没有关系
3. zrem
		
		zrem myzset fmeng
		#删除value，fmeng
4. zincrby

		zincrby myzset 2 fmeng
		#把fmeng这个值的顺序自增2
		#1.判断该集合中是否存在fmeng，如果不存在就添加fmeng，顺序设为2
		#2.如果zset中有该元素，就把这个元素的顺序自增2
5. zrank

		zrank myzset fmeng
		#1.如果feng不存在该集合，就返回nil
		#2.如果存在，先从按顺序**从小到大**排序，返回fmeng的顺序值（这个值是减1的）
6. zrevrank

		zrank myzset fmeng
		#1.如果feng不存在该集合，就返回nil
		#2.如果存在，先从按顺序**从大到小**排序，返回fmeng的顺序值（这个值是减1的）
7. zrevrange

		zrevrange myzset 2 3
		#先按循序**从大到小**排序。返回顺序为2--3之间的元素
8. zcount

		zcount myzset 2 10
		#返回score在2--10之间的个数
		#因为，这个区间的数可能不是连续的
9. zcard

		zcard myzset 
		#返回myzset中的key的个数
10. zremrangebyrank

	[http://www.redis.cn/commands/zremrangebyrank.html](http://www.redis.cn/commands/zremrangebyrank.html)
		
11. zremrangebyscore

	[http://www.redis.cn/commands/zrangebyscore.html](http://www.redis.cn/commands/zrangebyscore.html)

-----

## 键值相关命令 ##

1. keys 
	
		keys *
		#显示当前库中的所有key
2. exists

		exists name
		#判断那么这个key是否存在
3. del

		del name
		#删除这个key
4. expire

		expire name 10
		#设置name的过期时间为10秒
4. ttl

		ttl name
		#显示还有多长时间过期
5. select

		select 0-15
		#默认为0，选择不同的数据库
6. move

		move key 1
		#把key移动到数据库1
7. persist
	
		presist key
		#移除过期时间
8. randomkey

		randomkey
		#随机返回一个key
9. rename

		rename old_name new_name
		#重命名某个key
10. type

		type some_key
		#判断某个key的类型


-----

## 服务器相关命令 ##

1. dbsize

		dbsize 
		#返回当前数据库中的key数目
2. config
		
		config get *
		#得到redis.conf配置文件中的所有配置信息
3. flushdb

		flushdb
		#删除当前库中的所有key
4. flushall
		
		flushall
		#删除所有库中的所有key



