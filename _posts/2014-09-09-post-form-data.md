---
layout: post
title: "如何使用HttpClient提交富媒体文件？"
categories:
- java
---

[参考文章：](#)<br/>
[http://www.baeldung.com/httpclient-multipart-upload](http://www.baeldung.com/httpclient-multipart-upload)<br/>
[http://hc.apache.org/httpcomponents-client-ga/tutorial/html/fundamentals.html#d5e186](http://hc.apache.org/httpcomponents-client-ga/tutorial/html/fundamentals.html#d5e186)<br/>
[http://blog.csdn.net/weijonathan/article/details/9328509](http://blog.csdn.net/weijonathan/article/details/9328509)<br/>

1. 使用test1()，要导入指定的File类。<br/>
2. 使用test2()，只能提交文本文件类型<br/>
3. 使用test3()，以流的方式，提交数据<br/>
4. 使用test4()，字节数组的方式提交数据<br/>

**注意点：**<br/>
`builder.addBinaryBody("media", bytes, ContentType.DEFAULT_BINARY,"2.jpg");`<br/>
**显示为：**<br/>

![](/img/form_post_data.jpg)

	public class TestMedia {
		
		/**
		 * 说明，测试三和测试四，测试通过。能提交图片文件
		 * 测试一，好像要导入指定的File类
		 * 测试二，提交文本文件
		 */
		private static String textFileName = PropertyUtil.getPropertyfilepath() + "2.jpg";
		private static CloseableHttpClient client = HttpClient.createLibCient();
		private static String url = "http://file.api.weixin.qq.com/cgi-bin/media/upload?access_token="
		+"EOAC4WPAV_hyS-Gvsv17xGFIl6rCCTK3NI--d6_cGiyQXEEUtXZ7dH_kU8O68IJQTBDtA-wgdivXO4kRqVwyyw"
				+"&type=image";
		public static void main(String[] args) throws Exception {
			//test1();
			//test2();
			//test3();
			test4();
			
		}
	
		// Uploading a Form with Two Text Parts and a File
		/**
		 * 测试没有通过，貌似要动如别的包。File不是j2se的File
		 * @throws Exception
		 */
		private static void test1() throws Exception {
			File file = new File(textFileName);
			HttpPost post = new HttpPost(url);
			FileBody fileBody = new FileBody(file);
			//
			MultipartEntityBuilder builder = MultipartEntityBuilder.create();
			builder.setMode(HttpMultipartMode.BROWSER_COMPATIBLE);
			builder.addPart("upfile", fileBody);
			HttpEntity entity = builder.build();
			//
			post.setEntity(entity);
			post.setHeader("Content-Type", "image/jpeg");
			HttpResponse response = client.execute(post);
			String s = ResponseHttpEntity.getPageString(response.getEntity(),"UTF-8");
			System.out.println(s);
		}
	
		// Uploading Text and a Text File Part
		private static void test2() throws Exception {
			HttpPost post = new HttpPost(url);
			File file = new File(textFileName);
			String message = "This is a multipart post";
			MultipartEntityBuilder builder = MultipartEntityBuilder.create();
			builder.setMode(HttpMultipartMode.BROWSER_COMPATIBLE);
			builder.addBinaryBody("upfile", file, ContentType.DEFAULT_BINARY,
					textFileName);
			builder.addTextBody("text", message, ContentType.DEFAULT_BINARY);
			//
			HttpEntity entity = builder.build();
			post.setEntity(entity);
			HttpResponse response = client.execute(post);
			String s = ResponseHttpEntity.getPageString(response.getEntity(),"UTF-8");
			System.out.println(s);
		}
	
		// Uploading a Zip File, an Image File and a Text Part
		public static void test3() throws Exception {
			HttpPost post = new HttpPost(url);
			File file = new File(textFileName);
			MultipartEntityBuilder builder = MultipartEntityBuilder.create();
			builder.setMode(HttpMultipartMode.BROWSER_COMPATIBLE);
			builder.addBinaryBody("media", file, ContentType.DEFAULT_BINARY,
					"2.jpg");
			//
			HttpEntity entity = builder.build();
			post.setEntity(entity);
			HttpResponse response = client.execute(post);
			String s = ResponseHttpEntity.getPageString(response.getEntity(),"UTF-8","");
			System.out.println(s);
		}
	
		// Uploading a Byte Array and Text
		public static void test4() throws Exception {
			HttpPost post = new HttpPost(url);
			File file = new File(textFileName);
			FileInputStream fis = new FileInputStream(file);
			int i = fis.available();
			byte [] bytes = new byte [i];
			fis.read(bytes);
			//
			MultipartEntityBuilder builder = MultipartEntityBuilder.create();
			builder.setMode(HttpMultipartMode.BROWSER_COMPATIBLE);
			builder.addBinaryBody("media", bytes, ContentType.DEFAULT_BINARY,
					"2.jpg");
			//
			HttpEntity entity = builder.build();
			post.setEntity(entity);
			HttpResponse response = client.execute(post);
			String s = ResponseHttpEntity.getPageString(response.getEntity(),"UTF-8","");
			System.out.println(s);
		}
	
	}

