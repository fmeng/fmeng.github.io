---
layout: post
title: "Myeclipse 10.X安装 properties editor插件"
categories:
- other
---

[http://www.itnose.net/detail/6118116.html](http://www.itnose.net/detail/6118116.html)<br/>
[http://sourceforge.jp/projects/propedit/](http://sourceforge.jp/projects/propedit/)
#### <a href="#1">步骤1--安装</a> ####
1. <a href="#1">在先安装</a>
2. <a href="#12">下载安装</a>
#### <a href="#2">步骤2--配置</a> ####
### <a name="1"></a>下载安装 ###
1. 第一种方法（要翻墙）
	-->Help<br />
	-->Install on site...(等待时间较长)<br />
	-->Work with(填写表单内容)<br /> 
	Properties Editor - http://propedit.sourceforge.jp/eclipse/updates/<br/>
	-->**choose**<br/>
	Properties Editor
	--> Finish <br/>
	-->Restart Myeclipse

2. 第二中方法
<a name="12"></a>
	-->下载<br/>
	链接：[http://pan.baidu.com/s/1ezpHs](http://pan.baidu.com/s/1ezpHs) 密码：pg7s<br/>
	-->右键打开Myeclipse的安装目录<br/>
	-->mkdir dropins<br />
	-->copy features and plugins unzip by download file to "path-to-dropins"
	-->Restart Myeclipse 
###<a name="1"></a> 设置默认文件 ###

-->Windows<br />
-->Perferences<br />
-->General<br />
-->Editor<br />
-->File Associations<br />
-->**Choose**<br />
*.properties<br />
-->**Choose**<br />
PropertiesEditor(default)<br />
-->Ok<br />