---
layout: post
title: "dom4j乱码和在WEB-INF下面的包"
categories:
- java
---

## not class find ##
分析，这个异常是编译时异常。理论上，Eclipse会自动提示有错。
但是，没有发现错误。
<br/>
问题解决,原来Eclipse还没有那么智能。把reference的包自动放到WEB-INF/lib中

## 乱码 ##

在做微信公众平台时，dom4j解析xml文件，出现异常。我初步判断可能是xml格式不完整的问题。但是，测试没有通过。<br/>
分别用两种方式改变String编码，还是没有解决问题。


	import java.io.ByteArrayInputStream;
	import java.io.UnsupportedEncodingException;
	import java.net.URLEncoder;
	import java.nio.charset.Charset;
	import java.util.List;
	
	import org.dom4j.Document;
	import org.dom4j.DocumentException;
	import org.dom4j.Element;
	import org.dom4j.io.SAXReader;
	
	
	public class Test1 {
	
		private static String s = "<xml><ToUserName><![CDATA[gh_499588f706cb]]></ToUserName><FromUserName><![CDATA[oGTKKuK-vjm-tV79T45M2e-x_vbU]]></FromUserName><CreateTime>1408429680</CreateTime><MsgType><![CDATA[text]]></MsgType><Content><![CDATA[fsdfdsf]]></Content><MsgId>6049159414516866928</MsgId></xml>";
		public static void main(String[] args) throws UnsupportedEncodingException {
			String ss = new String(s.getBytes(Charset.defaultCharset()),"UTF-8");
			String sss=URLEncoder.encode(s, "utf-8");
			SAXReader reader = new SAXReader();
			try {
				/**
				*这里把字符串改为 s,ss,ss都无法通过
				*/
				Document document = reader.read(sss);
				Element root = document.getRootElement();
				List<Element> elementList = root.elements();
				for (Element e : elementList) {
					System.out.println("名字"+e.getName());
					System.out.println("值"+e.getTextTrim());
				}
			} catch (DocumentException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
	
	}


#### 异常信息 ####
	org.dom4j.DocumentException: no protocol:

	Nested exception: 
	java.net.MalformedURLException: no protocol:

	Nested exception: java.net.MalformedURLException: no protocol: 


#### 最后解决方法 ####

`document = saxReader.read(new ByteArrayInputStream(str.getBytes("UTF-8"))); `	<br/>
原因，就是saxReader.read()在读取字符串是只能以固定的编码方式读取字符串（具体是那种，我没有测试出来）。所以无论我用`String ss = new String(s.getBytes(Charset.defaultCharset()),"UTF-8");`这种方式如何更换编码。都没有起作用。
