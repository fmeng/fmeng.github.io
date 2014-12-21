---
layout: post
title: "mysql用户权限"
categories:
- other
---
很长时间被mysql的权限问题困扰，于是就下一下工夫把知识学透吧。


##相关的系统配置表
1. mysql.user
2. mysql.db
3. mysql.host
4. mysql.table_priv
5. mysql.column_priv

##用户权限的等级
1. 全局控制权限
2. db和host共同控制链接后的权限
3. 表权限
4. 列权限
5. 特殊权限

##mysql权限控制的过程
1. 能不能链接？  
&emsp;&emsp;当client要链接mysql的server的时候，无论是远程链接还是本地链接。首先要检测是否有链接数据库的权利，首先，比对user+password是否合法。其次，比较用户在不同的主机登录时也会有不同的全局操作权限。这里应该注意，在user，db，host中都有对用户操作数据库，表的权限。他们的区别和联系何在？<br/>
&emsp;&emsp;user,证明的是全局变量，权限等级最高。db和host共同作用、控制对数据库和用户登录主机的认证权限。这里，host和db是一起作用的。user和他们的区别在于，后者可以确定到一个数据库。
2. 链接后有那些权限？  
&emsp;&emsp;当用户建立链接后，对数据库进行操作。就会在原来的全局权限的基础上进一步进行检测。这里检测的两个表是：mysql.tabel_priv,mysql_column_priv。

##操作命令
1. 创建一个超级用户  
<code>
grant all privilege on *.* to fmeng@'localhost" identifide by "my_password" with grant option 
</code>
2. 创建一个程序用户  
<code>
grant usage , select , insert , update , delete , show view , create temporary tables , execute on 'mydb'.* to fmeng@'localhost' identifide by "my_password"
</code>
3. 创建一个普通用户  
<code>
grant usage , select on 'mydb'.* to public @'201.12.135.21' indentifide by "my_other password"
</code>
4. 其他命令  
&emsp;&emsp;刷新权限
&emsp;&emsp;
<code>
flush privileges
</code> <br/>
&emsp;&emsp;查看权限
&emsp;&emsp;<code>
show grants for 'fmeng'@115.23.78.09
</code><br/>
&emsp;&emsp;回收权限
&emsp;&emsp;<code>
revoke delete on mydb.* from 'fmeng'@117.0.01
</code><br/>
&emsp;&emsp;删除用户
&emsp;&emsp;<code>
drop user 'fmeng'@localhost
</code>

##总结
&emsp;&emsp;在设置用户权限的时候，要综合考虑。从内到外设计。用于自己使用的数据库和文档数据库不符合，在学西数据库时，应该是以实践为主。



