---
layout: post
title: "HttpClient Post文本文件乱码问题"
categories:
- java
---
![](/img/http_client.jpg)

----

	public static boolean sendCustomMessage(String fromUserName, String message, String charSet) throws ParseException, IOException{
			String accessToken = PropertyUtil.getPropertyParam("weixin.properties", "accessToken");
			String url = PropertyUtil.getPropertyParam("weixin.properties", "customUrl").replace("ACCESS_TOKEN", accessToken);
			HttpPost httpPost = new HttpPost(url);
			/**
			 * 这种方法构造实体，无论如何调试也不能对String编码
			 */
			//构造实体
			//EntityBuilder b = EntityBuilder.create();
			//b.setText(new String(message.getBytes("GB2312"),"UTF-8"));
			//HttpEntity entity = b.build();
			/**
			 * 构建StringEntity设置编码解决问题
			 */
			StringEntity entity = new StringEntity(message, "UTF-8");
			httpPost.setHeader("Content-Type", "application/x-www-form-urlencoded;charset=UTF-8");
			httpPost.setEntity(entity);
			//设置client
			//httpPost.setConfig(RequestConfig)
			CloseableHttpClient httpClient = HttpClient.createHttpsClient();
			String responseMessage = ResponseHttpEntity.getJson(httpClient, httpPost, charSet);
			//关闭资源
			httpClient.close();
			//转换成Json对象
			JSONObject jsonObject = JSONObject.fromObject(responseMessage);
			
			if(jsonObject.getInt("errcode") == 0){
				return true;
			}else{
				return false;
			}
		}

----
![](/img/http_entity.jpg)

## 总结： ##

&emsp;&emsp;不能太依赖google和百度。没有发HttpClient这套API吃透。还要熟悉这套API。
再次，寻找解决方法的思路错了。以后应该，首先学会看管方的api。
