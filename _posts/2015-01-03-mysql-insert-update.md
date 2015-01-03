---
layout: post
title: "mysql插入、更新数据冲突"
categories:
- other
---

[http://stackoverflow.com/questions/14383503/on-duplicate-key-update-same-as-insert](http://stackoverflow.com/questions/14383503/on-duplicate-key-update-same-as-insert)

[http://dev.mysql.com/doc/refman/5.5/en/insert-on-duplicate.html](http://dev.mysql.com/doc/refman/5.5/en/insert-on-duplicate.html)

需求： 在**插入**一条记录到数据库中时，避免该记录已经存在从而造成的**主键、索引冲突**错误。<br/>
解决方法：<br/>

1. 先判断要插入索引的主键是否在数据库中存在，如果存在，就使用update语句。如果不存在，就是用add语句。
2. `on duplicate key update`判断使用更新语句，还是插入语句。（这个语句只有在mysql数据库中存在，**不是标准sql语句**）

		#在没有主键冲突的情况下，使用这个插入语句
		INSERT INTO table (id,a,b,c,d,e,f,g) VALUES (1,2,3,4,5,6,7,8)
		
		#在有主键、索引冲突是情况下，使用以下这两种方法
		INSERT INTO table (id,a,b,c,d,e,f,g) VALUES (1,2,3,4,5,6,7,8) 
		ON DUPLICATE KEY UPDATE a=2,b=3,c=4,d=5,e=6,f=7,g=8

		INSERT INTO table (id,a,b,c,d,e,f,g) VALUES (1,2,3,4,5,6,7,8) 
		ON DUPLICATE KEY UPDATE a=VALUES(a),b=VALUES(b),c=VALUES(c),d=VALUES(d),e=VALUES(e),f=VALUES(f),g=VALUES(g)


#### 总结： ####

&emsp;&emsp;国内的有些博客，只会copy，都没有经过自己的思考。为自己搜索答案，增加的不少困难。以后，搜索比较偏的信息，要首选google。