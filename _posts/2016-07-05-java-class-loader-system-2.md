---
layout: post
title: "JAVA类加载机制（二）"
categories:
- java
---

[http://my.oschina.net/everyDay111/blog/508180](http://my.oschina.net/everyDay111/blog/508180)<br/>
视频资料：[链接：http://pan.baidu.com/s/1nv2abB7](链接：http://pan.baidu.com/s/1nv2abB7)&emsp;密码：g0gr<br/>
推荐书籍：[深入java虚拟机(iteye社区).pdf](http://pan.baidu.com/s/1pLgrsAv)&emsp;&emsp;密码：4n8l<br/>
推荐书籍：[《深入理解Java虚拟机：JVM高级特性与最佳实践》(第二版)](https://book.douban.com/subject/24722612/)<br/>&emsp;&emsp;&emsp;&emsp;&emsp;[下载PDF](http://pan.baidu.com/s/1c2kb4pq) &emsp;密码：cfyf

------
#### 概念解析： ####

1.	**父委托机制。**加载器之间的“父子”关系指的是<font color=red>**包装关系，而不是类之间的继承关系。**</font>
2.	**定义类加载器**：若有一个类成功的加载了Sample类**（真正加载了该Class）。**
3.	**初始类加载器**：所有能能成功返回Class对象引用的类加载器<font color=red>**（拥有加载该Class的能力）。**</font>
4.	**命名空间**：命名空间由该加载器及所有父加载器所加载的类组成。<br/>
命名空间包括，类在堆区和方法区的存储。如下图：
![](/img/java-jvm24.jpg)
![](/img/java-jvm21.jpg)
5.	**运行时包**。由统一加载器加载的属于相同包的类组成的包。<font color=red>
	1. **在同一个包内**
	2. **类加载器相同** </font>
6.	**不同类加载器的命名空间关系**
![](/img/java-jvm22.jpg)
7.	**类的卸载**<br/>
	Java自带的类加载器（Bootstrap、Extension、System）加载的类不可卸载。用户自定义的加载器加载的类可卸载。
8.	**所有的Java类都有一个静态属性class。**
![](/img/java-jvm23.jpg)

-----
总结：理解ClassLoader、Class之间的关系。**方法区具体存放了类里面所有的变量和方法实现，堆区把方法区的数据组织在一起存放在堆中。方法区存放了类对外的统一方法，这是实现反射机制的关键。**具体理解下图：<br/>
![](/img/java-jvm25.jpg)












