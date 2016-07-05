---
layout: post
title: "JAVA类加载机制"
categories:
- java
---

[http://my.oschina.net/everyDay111/blog/508180](http://my.oschina.net/everyDay111/blog/508180)<br/>
视频资料：[链接：http://pan.baidu.com/s/1nv2abB7](链接：http://pan.baidu.com/s/1nv2abB7)&emsp;密码：g0gr<br/>
推荐书籍：[深入java虚拟机(iteye社区).pdf](http://pan.baidu.com/s/1pLgrsAv)&emsp;&emsp;密码：4n8l<br/>
推荐书籍：[《深入理解Java虚拟机：JVM高级特性与最佳实践》(第二版)](https://book.douban.com/subject/24722612/)<br/>&emsp;&emsp;&emsp;&emsp;&emsp;[下载PDF](http://pan.baidu.com/s/1c2kb4pq) &emsp;密码：cfyf

------

### 一、	下几种情况JVM结束生命周期： ###

1.	执行了System.exit();
2.	程序正常结束
3.	程序再运行过程中遇到Exception或Error
4.	操作系统错误
### 二、	类的加载、连接（验证、准备、解析）、初始化 ###
连接就是将已经读入到内存的类的二进制数据合并到虚拟机的运行时环境中去。

1. 加载。
	1.1	类的加载指的是将类的.class文件中的二进制数据读入到内存中，将其放在运行时数据区的方法区内（先），然后在堆区创建一个java.lang.Class对象（后），用来封装类在方法区内的数据结构
	1.2	类的加载的最终产品是位于堆区中的Class对象。Class对象封装了类在方法区内的数据结构，并且向Java程序员提供了访问方法区内的数据结构的接口
	1.3	加载.class文件的方式
		1.3.1	从本地系统中直接加载
		1.3.2	通过网络下载.class文件
		1.3.3	从zip，jar等归档文件中加载.class文件
		1.3.4	从专有数据库中提取.class文件
		1.3.5	将Java源文件动态编译为.class文件
	1.4	两种类加载器
		1.4.1	Java虚拟机自带的加载器
			1.4.1.1	根类加载器（Bootstrap）（C++代码实现）
			1.4.1.2	扩展类加载器（Extension）（Java代码实现）
			1.4.1.3	系统类加载器（System、应用加载器）（Java代码实现）
		1.4.2	用户自定义的类加载器
			1.4.2.1	java.lang.ClassLoader的子类
			1.4.2.2	用户可以定制类的加载方式
	1.5	类加载器的加载时机
		1.5.1	JVM规范，ClassLoader可以预料某个类将要使用时，提前加载。如遇到class文件缺失或存在错误，程序首次主动使用该类时才报告LinkageError错误。如果这个类一直没有被程序主动使用，那么类加载器就不会报告错误。（发现错误和报告错误的是两个时机）
2	验证。确保被加载类的正确性
3	准备。为类的静态变量分配内存，并初始化为默认值
4	解析。把类中的符号引用（java）转换成直接引用（c）
5	初始化。为类的静态变量赋予用户定义的初始值





