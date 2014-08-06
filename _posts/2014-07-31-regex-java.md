---
layout: post
title: "正则匹配--java"
categories:
- java
---

## 四个字符 ##
1. []里面只能表示一个字符。
2. ()代表是一个分组
3. {}代表[]出现的次数
4. ，代表分组之间的界限

## 关于转义字符 --使用预先编译##

1. \\\d转义普通字符数字
2. \\\\\转义\\
3. 转义字符列表[http://www.cnblogs.com/lixin890808/p/3375678.html](http://www.cnblogs.com/lixin890808/p/3375678.html)
<br/>
[http://blog.csdn.net/risingwonderland/article/details/23794897](http://blog.csdn.net/risingwonderland/article/details/23794897)

## 代码示例 ##

匹配数字


	String str = "The population of 2984444215 is growing";
	Pattern p = Pattern.compile("\\d+");
	Matcher m = p.matcher(str);
	if (m.find()) {
		String result = m.group();
		System.out.println("result is " + result);
	}


得到各个分组
	
	//由","实现分组
	String str = "2012-8-17,9:30-11:30"; 
	Pattern p = Pattern.compile("(\\d{4}-\\d{1,2}-\\d{1,2}),(\\d{1,2}:\\d{1,2}-\\d{1,2}:\\d{1,2})");
	if (m.find()) {
		//最小是1，从1开始
		String s = m.group(1);
		System.out.println(s);
	}

把cat换成dog
	
	 String str = "one cat two cats in the yard";
	 Pattern p = Pattern.compile("cat");
	 Matcher m = p.matcher(str);
		//一定要设置缓冲字符用来多次改变字符串
	 StringBuffer sb = new StringBuffer();
	 while (m.find()) {
		 /*1.把str中的cat字符替换成dog
		   2.把替换后的字符串赋值给缓冲字符
			*/
	     m.appendReplacement(sb, "dog");
	 }
	     //添加最后没有被缓冲流捕获的部分
	 m.appendTail(sb);
	 System.out.println(sb.toString());



特殊匹配

	public void test5(){
		String url = "http://210.30.208.140/(1dk2zq55uad53euafs4nxxjy)/default2.aspx";
		Pattern p = Pattern.compile("(http://210.30.208.140/\\()(.{24})(\\)/default2.aspx)");
		 Matcher m = p.matcher(url);
		 if(m.find()){
			 String s = m.group(1);
			 System.out.println(s);
		 }
	}
	

匹配/

	String s = "\\";
	boolean b = s.matches("\\\\");
	System.out.println(b);
