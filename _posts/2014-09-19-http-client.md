---
layout: post
title: "HttpCliet 总结"
categories:
- java
---

#### 资料： ####
学习的HttpClient是httpcomponents-client-4.3.4，因为HttpClient不同版本之间更新较大，在这里声明一下。

[API](#)<br>
&emsp;&emsp;[http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/)<br/>
[JAR](#)<br/>
&emsp;&emsp;[https://hc.apache.org/downloads.cgi](https://hc.apache.org/downloads.cgi)<br/>
[Tutorial](#)<br/>
&emsp;&emsp;[http://hc.apache.org/httpcomponents-client-ga/tutorial/html/](http://hc.apache.org/httpcomponents-client-ga/tutorial/html/)<br/>
[Blogs](#)<br/>
&emsp;&emsp;[Demo for httpClient](http://www.baeldung.com/httpclient-guide)<br/>
&emsp;&emsp;[Chinese Tutorial](http://www.cnblogs.com/loveyakamoz/category/311258.html)<br/>
&emsp;&emsp;[http://www.yeetrack.com/?p=773](http://www.yeetrack.com/?p=773)<br/>
&emsp;&emsp;[Upload and download file](http://www.cnblogs.com/Scott007/p/3817285.html)<br/>
&emsp;&emsp;[MyBlog](#)<br/>
&emsp;&emsp;&emsp;&emsp;[HttpClient提交富媒体文件]([http://www.fmeng.name/java/2014/09/09/post-form-data.html)<br/>
&emsp;&emsp;&emsp;&emsp;[HttpClient Post文本文件乱码问题](http://www.fmeng.name/java/2014/09/18/encoder-httpClient-post.html)

[MyFiles](#)<br/>
&emsp;&emsp;里面有相关的Jar和Src和Tutorial&emsp;&emsp;[Click Here To Download](http://pan.baidu.com/s/1jJXRw)

## 把HTTPClient做成工厂 ##

	public class HttpClient {
		// 把httpclient做成工厂
		private HttpClient() {
		}
	
		public static CloseableHttpClient createChosingClient() {
			LaxRedirectStrategy redirectStrategy = new LaxRedirectStrategy();
	
			CloseableHttpClient httpclient = HttpClients.custom()
					.setRedirectStrategy(redirectStrategy)
					.setUserAgent("Mozilla/5.0 Firefox/26.0").build();
			return httpclient;
		}
	
		public static CloseableHttpClient createLibCient() {
			// 重定向控制
			LaxRedirectStrategy redirectStrategy = new LaxRedirectStrategy();
			// cookie控制
			RequestConfig globalConfig = RequestConfig.custom()
					.setCookieSpec(CookieSpecs.BEST_MATCH).build();
			// 设置httpclient
			CloseableHttpClient httpclient = HttpClients.custom()
					.setRedirectStrategy(redirectStrategy)
					.setDefaultRequestConfig(globalConfig)
					.setUserAgent("Mozilla/5.0 Firefox/26.0").build();
			return httpclient;
		}
	
		public static CloseableHttpClient createLibCient(Cookie cookie) {
			// 重定向控制
			LaxRedirectStrategy redirectStrategy = new LaxRedirectStrategy();
			// cookie控制
			RequestConfig globalConfig = RequestConfig.custom()
					.setCookieSpec(CookieSpecs.BEST_MATCH).build();
			CookieStore cookieStore = new BasicCookieStore();
			cookieStore.addCookie(cookie);
			// 设置HttpClient
			CloseableHttpClient httpclient = HttpClients.custom()
					.setRedirectStrategy(redirectStrategy)
					.setDefaultRequestConfig(globalConfig)
					.setUserAgent("Mozilla/5.0 Firefox/26.0")
					.setDefaultCookieStore(cookieStore).build();
			return httpclient;
		}
	
		public static CloseableHttpClient createCient() {
			// 重定向控制
			LaxRedirectStrategy redirectStrategy = new LaxRedirectStrategy();
			// cookie控制
	
			RequestConfig globalConfig = RequestConfig.custom()
					.setCookieSpec(CookieSpecs.BEST_MATCH).build();
			// 设置httpclient
			CloseableHttpClient httpclient = HttpClients.custom()
					.setRedirectStrategy(redirectStrategy)
					.setDefaultRequestConfig(globalConfig)
					.setUserAgent("Mozilla/5.0 Firefox/26.0").build();
			return httpclient;
		}
	
		public static CloseableHttpClient createHttpsClient(){
			// SSL配置
			SSLContextBuilder builder = new SSLContextBuilder();
			SSLConnectionSocketFactory sslsf;
			try {
				builder.loadTrustMaterial(null, new TrustSelfSignedStrategy());
				sslsf = new SSLConnectionSocketFactory(
						builder.build());
			} catch (KeyManagementException | NoSuchAlgorithmException
					| KeyStoreException e) {
				throw new RuntimeException(e);
			}
			// 重定向控制
			LaxRedirectStrategy redirectStrategy = new LaxRedirectStrategy();
			// cookie控制
	
			RequestConfig globalConfig = RequestConfig.custom()
					.setCookieSpec(CookieSpecs.BEST_MATCH)
					.build();
			// 设置httpclient
			CloseableHttpClient httpclient = HttpClients.custom()
					.setRedirectStrategy(redirectStrategy)
					.setDefaultRequestConfig(globalConfig)
					.setSSLSocketFactory(sslsf)
					.setUserAgent("Mozilla/5.0 Firefox/26.0").build();
			return httpclient;
		}
	
	}


## 封装HttpClient执行过程，只提取需要的放回结果 ##


	public class ResponseHttpEntity {
		private ResponseHttpEntity() {
		}
	
		// 由一个reponse得到一个网页
		public static String getPageString(HttpEntity entity) {
			String page = null;
			try {
				if (entity != null && isHtml(entity)) {
					page = EntityUtils.toString(entity);
				}
			} catch (Exception e) {
				throw new RuntimeException(e);
			}
			return page;
		}
	
		// 有一个httpclient和一个get方法得到一个StringPage
		public static String getPageString(CloseableHttpClient client,
				HttpGet httpget) {
			CloseableHttpResponse response = null;
			HttpEntity entity = null;
			try {
				response = client.execute(httpget);
				if (response != null) {
					entity = response.getEntity();
					if (entity != null && ResponseHttpEntity.isHtml(entity)) {
						String s = EntityUtils.toString(entity);
						return s;
					}
				}
			} catch (Exception e) {
				throw new RuntimeException(e);
			}finally {
				if (response != null) {
					try {
						response.close();
					} catch (IOException e) {
						throw new RuntimeException(e);
					}
				}
			}
			return null;
		}
	
		public static String getPageString(CloseableHttpClient client,
				HttpPost httppst) {
			CloseableHttpResponse response = null;
			HttpEntity entity = null;
			try {
				response = client.execute(httppst);
				if (response != null) {
					entity = response.getEntity();
					if (entity != null && ResponseHttpEntity.isHtml(entity)) {
						String s = EntityUtils.toString(entity);
						return s;
					}
				}
			} catch (Exception e) {
				throw new RuntimeException(e);
			} finally {
				if (response != null) {
					try {
						response.close();
					} catch (IOException e) {
						throw new RuntimeException(e);
					}
				}
			}
			return null;
		}
	
		// 由一个reponse得到一个网页，指定字符集
		public static String getPageString(HttpEntity entity, String charSet) {
			String page = null;
			try {
				if (entity != null && isHtml(entity))
					page = EntityUtils.toString(entity, charSet);
			} catch (Exception e) {
				throw new RuntimeException(e);
			}
			return page;
		}
		// 由一个reponse得到一个网页，指定字符集
			public static String getString(HttpEntity entity, String charSet) {
				String page = null;
				try {
					if (entity != null)
						page = EntityUtils.toString(entity, charSet);
				} catch (Exception e) {
					throw new RuntimeException(e);
				}
				return page;
			}
	
			public static byte[] getByteArray(HttpEntity entity) {
				byte[] b = null;
				if (entity != null && (isImage(entity) != null)) {
					try {
						b = EntityUtils.toByteArray(entity);
					} catch (IOException e) {
						throw new RuntimeException(e);
					}
				}
				return b;
			}
			
		// 有一个httpclient和一个get方法得到一个StringPage
		public static String getPageString(CloseableHttpClient client,
				HttpGet httpget, String charSet) {
			CloseableHttpResponse response = null;
			HttpEntity entity = null;
			try {
				response = client.execute(httpget);
				if (response != null) {
					entity = response.getEntity();
					if (entity != null && ResponseHttpEntity.isHtml(entity)) {
						String s = EntityUtils.toString(entity, charSet);
						return s;
					}
				}
			} catch (Exception e) {
				throw new RuntimeException(e);
			}  finally {
				if (response != null) {
					try {
						response.close();
					} catch (IOException e) {
						throw new RuntimeException(e);
					}
				}
			}
			return null;
		}
	
		
		public static String getPageString(CloseableHttpClient client,
				HttpGet httpget, HttpClientContext context, String charSet) {
			CloseableHttpResponse response = null;
			HttpEntity entity = null;
			try {
				response = client.execute(httpget, context);
				if (response != null) {
					entity = response.getEntity();
					if (entity != null && ResponseHttpEntity.isHtml(entity)) {
						String s = EntityUtils.toString(entity, charSet);
						return s;
					}
				}
			} catch (Exception e) {
				throw new RuntimeException(e);
			} finally {
				if (response != null) {
					try {
						response.close();
					} catch (IOException e) {
						throw new RuntimeException(e);
					}
				}
			}
			return null;
		}
		
		public static HttpClientContext getContext(CloseableHttpClient client,
				HttpGet httpget, HttpClientContext context) {
			try {
				client.execute(httpget, context);
			} catch (Exception e) {
				throw new RuntimeException(e);
			}
			return context;
		}
		
		public static HttpClientContext getContext(CloseableHttpClient client,
				HttpGet httpget) {
			HttpClientContext context = new HttpClientContext();
			try {
				client.execute(httpget, context);
			} catch (Exception e) {
				throw new RuntimeException(e);
			}
			return context;
		}
	
		public static String getPageString(CloseableHttpClient client,
				HttpPost httpPost, String charSet) {
			CloseableHttpResponse response = null;
			HttpEntity entity = null;
			try {
				response = client.execute(httpPost);
				if (response != null) {
					entity = response.getEntity();
					if (entity != null && ResponseHttpEntity.isHtml(entity)) {
						String s = EntityUtils.toString(entity, charSet);
						return s;
					}
				}
			} catch (Exception e) {
				throw new RuntimeException(e);
			}finally {
				if (response != null) {
					try {
						response.close();
					} catch (IOException e) {
						throw new RuntimeException(e);
					}
				}
			}
			return null;
		}
		
		
		public static String getJson(CloseableHttpClient client,
				HttpPost httpPost, String charSet) {
			CloseableHttpResponse response = null;
			HttpEntity entity = null;
			try {
				response = client.execute(httpPost);
				if (response != null) {
					entity = response.getEntity();
					if (entity != null) {
						String s = EntityUtils.toString(entity, charSet);
						return s;
					}
				}
			} catch (Exception e) {
				throw new RuntimeException(e);
			}finally {
				if (response != null) {
					try {
						response.close();
					} catch (IOException e) {
						throw new RuntimeException(e);
					}
				}
			}
			return null;
		}
		
		public static String getJson(CloseableHttpClient client,
				HttpGet httpGet, String charSet) {
			CloseableHttpResponse response = null;
			HttpEntity entity = null;
			try {
				response = client.execute(httpGet);
				if (response != null) {
					entity = response.getEntity();
					if (entity != null) {
						String s = EntityUtils.toString(entity, charSet);
						return s;
					}
				}
			} catch (Exception e) {
				throw new RuntimeException(e);
			}finally {
				if (response != null) {
					try {
						response.close();
					} catch (IOException e) {
						throw new RuntimeException(e);
					}
				}
			}
			return null;
		}
	
		
		public static String getPageString(CloseableHttpClient client,
				HttpPost httpPost, HttpClientContext context, String charSet) {
			CloseableHttpResponse response = null;
			HttpEntity entity = null;
			try {
				response = client.execute(httpPost,context);
				if (response != null) {
					entity = response.getEntity();
					if (entity != null && ResponseHttpEntity.isHtml(entity)) {
						String s = EntityUtils.toString(entity, charSet);
						return s;
					}
				}
			} catch (Exception e) {
				throw new RuntimeException(e);
			} finally {
				if (response != null) {
					try {
						response.close();
					} catch (IOException e) {
						throw new RuntimeException(e);
					}
				}
			}
			return null;
		}
		
		public static HttpClientContext getContext(CloseableHttpClient client,
				HttpPost httpPost, HttpClientContext context) {
				try {
					client.execute(httpPost,context);
				} catch (Exception e) {
					throw new RuntimeException(e);
				}
				return context;
		}
		
		public static HttpClientContext getContext(CloseableHttpClient client, HttpPost httpPost) {
				HttpClientContext context = new HttpClientContext();
				try {
					client.execute(httpPost,context);
				} catch (Exception e) {
					throw new RuntimeException(e);
				}
				return context;
		}
	
		public static boolean isHtml(HttpEntity entity) {
			Pattern p = Pattern.compile("^text/html.*");
			if (entity != null && entity.getContentType().getName() != null) {
				String s = entity.getContentType().getValue();
				Matcher m = p.matcher(s);
				if (m.find()) {
					return true;
				}
			}
			return false;
		}
	
		public static String isImage(HttpEntity entity) {
			Pattern p1 = Pattern.compile("(^image/)(.*)(;.*)",Pattern.CASE_INSENSITIVE);
			Pattern p2 = Pattern.compile("(^image/)(.*)",Pattern.CASE_INSENSITIVE);
			if (entity != null && entity.getContentType().getName() != null) {
				String s = entity.getContentType().getValue();
				Matcher m = p1.matcher(s);
				if(m.matches()){
					return m.group(2).toLowerCase();
				}
				m = p2.matcher(s);
				if(m.matches()){
					return m.group(2).toLowerCase();
				}
			}
			return null;
		}
	}

## 注意： ##
&emsp;&emsp;CloseableHttpCliet,CloseableHttpResponse都是以资源的形式存在的。一定要记得关闭，尽量完的打开资源，尽量早的关闭。




