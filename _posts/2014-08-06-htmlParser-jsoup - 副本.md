---
layout: post
title: "解析网页内容--li"
categories:
- java
---

## 已经从网站抓取网页 ##

	<li class="book_list_info">
	   <h3>
		   <span>中文图书</span>
		   <a href="item.php?marc_no=0000548314" >1.Linux系统应用基础教程第2版</a>
		   TP316.85/90
	   </h3>
	   <p> 
		   <span>馆藏复本：2 <br>可借复本：2</span>
		   张小进编著<br />
	       机械工业出版社2014<br/>
	       <img/>
		   <a>馆藏<s></a>
	   </p>
	</li>


## 分析如下 ##

		1.用htmlparser接得到li标签的内容(因为我已经封装成工具类了)<br/>
		(下面的关于html的解析都用jsoup)
					1.得到h3
								1.得到<span>
								2.得到<a>
								3.正则匹配 “TP316.85/90”
					2.得到p
								1.得到<span>
										1.正则匹配两个数字
								2.正则匹配第一个<br/>
								3.正则匹配第二个<br/>

## 做个标记明天再战 ##

&emsp;&emsp;单个标签,例如&lt;br/&gt;,暂时没有找到比较好的解决方法，现在只能用正则匹配了。