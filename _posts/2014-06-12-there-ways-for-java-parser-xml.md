---
layout: post
title: "JAVA解析XML的三种方式"
categories:
- java
---

 >要操作的xml文件。<br/>
 

<code>
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<exam>
	<student examid="222" idcard="111">
		<name>张三</name>
		<location>沈阳</location>
		<grade>95</grade>
	</student>
</exam>
</code>

1. ##DOM解析

	&emsp;&emsp;**简单介绍。**是W3C组织推荐的解析XMl文档的方式。会把xml文档全部读入内存，根据所有xml的结点建立对象，组织对象之间的关系，比较吃消内存空间。适合，增、删、改，不适合查找。

	&emsp;&emsp;**解析代码。**<br/>
	使用的开发工具包。 javax.xml、org.w3c.dom、org.xml.sax。用解析器解析xml文档产生document对象。所以代码首先要分为两步，1.产生一个解析器。2.产生一个document对象。3.操作document对象。

	

	&emsp;&emsp;**注意细节。**要把改变后的Document写回文件。

2. ##SAX解析
	
	&emsp;&emsp;**简单介绍。**不是官方标准。但事实上是XMl社区公认的标准，几乎所有的XML解析器都支持它。Sax解析采用的是事件处理的方式。不会占用太多的内存。

	&emsp;&emsp;**解析代码。**

	&emsp;&emsp;**注意细节。**在读取器读取文件之前把内容处理器写好。

3. ##DOM4J解析

	&emsp;&emsp;**简单介绍。**

	&emsp;&emsp;**解析代码。**

	&emsp;&emsp;**注意细节。**

##Xpath介绍。

## 总结 ##

Java中的3中解析Xml的API。