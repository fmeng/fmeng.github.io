---
layout: post
title: "趴取图书馆数据“乱码”解决方法 "
categories:
- java
---

## 寻找解决方法 ##
&emsp;&emsp;NCF不是一种编码。原理很简单，就是把除了 ISO-8859-1 编码中前128个字符以外的所有其他的编码都用 NCR(Numeric character reference) 来表示。比如“汉字”这两个字，如果我们写成“&#27721;&#23383;”这种形式，那么它在任意字符集下都可以正确显示。<br/>
[http://blog.sina.com.cn/s/blog_56da5aa0010007g0.html](http://blog.sina.com.cn/s/blog_56da5aa0010007g0.html)

	&emsp;&#x0069;&it;&#x0075;&#x0078;&#x7cfb;&#x7edf;&#x5e94;&#x7528;
1. 注意 m.find()和m.matches()的区别。
	m.find(),编译后的字符串可以使字符串的一个子串。m.matches()要匹配所有的字符串。必要时要使用,m.reset();重置里面的表识器。 在编译之前可以指定，编译前的规则。`Pattern p = Pattern.compile("",Pattern.CASE_INSENSITIVE);`忽略大小写。
3. 替换字符串中的所有子串。

		Pattern p = Pattern.compile("孩子",Pattern.CASE_INSENSITIVE);
		String s = "我们都是好孩子,你是好孩子吗？";
		Matcher m = p.matcher(s);
		String str = null;
		if(m.find()){
			str = m.replaceAll("学生");
		}
		System.out.println(str);

4. 可以使用URLDecoder，URLEncoder对url编码。（可以指定编码）

	不能去除转义字符&amp;it,标签&lt;br/&gt;。只能对字符串进行机械的转码。
5. 使用commons-lang包中的StringEscapeUtils.unescapeHtml4()工具对`&#x0065;&#x0076;&#x0065;`之类的字符进行解码。

	不能自动去除&lt;br/&gt;无法解析`&emsp;&ensp;&nbsp;`等。会出现？号乱码。
	同时也会抛出异常。
6. 使用doc.body().text()。得到解析后的字符串。

	可以自动去除&lt;br/&gt;标签。解释大部分转义标签。但是无法解析&emsp;&ensp;&nbsp;。


## 解决方法 ##

1. 先使用正则匹配替换`&emsp;&ensp;&nbsp;`等不能解释的字符。
2. 使用doc.body().text()既可以去除&lt;br/&gt;又可以解析NFC。

代码实现如下。


		Pattern p = Pattern.compile("&emsp;",Pattern.CASE_INSENSITIVE);
		String s = "&emsp;&#x0069;&lt;&#x0075;&#x0078;&#x7cfb;&#x7edf;&#x5e94;&#x7528;";
		Matcher m = p.matcher(s);
		String str = null;
		if(m.find()){
			str = m.replaceAll(" ");
		}
		Document d = Jsoup.parse(str);
		String ok = d.body().text();
		System.out.println(ok);