---
layout: post
title: "设计计划任务--定时更新 access token"
categories:
- java
---
1. access token写入到`weixin.properties`文件。
2. 使用PropertyUtil写入，更新文件和map。
3. **设定时间略小于120分钟，避免其他线程拿到的access token过时，设置为113分钟。**
4. **工具类`PropertyUtil.setProperties()`已经加锁。**
5. 解决http请求延时，导致获取acces token不是最新的。
6. 解决一次请求可能返回为空的情况

`web.xml`&emsp;&emsp;tomcat启动，自动配置access token

	 <servlet>
	    <servlet-name>get-weixin-access-token-timer</servlet-name>
	    <servlet-class>com.hellopipi.servlet.InitAccessToken</servlet-class>
	    <load-on-startup>2</load-on-startup>
	  </servlet>

`InitAccessToken.java`&emsp;&emsp;设计servlet

	public class InitAccessToken extends HttpServlet {
	private static final long serialVersionUID = -4484737443065428164L;
	
	Timer getAccessTokenTimer = null;
	/**
	 * 使用destroy防止，从新加载timer
	 */
	@Override
	public void destroy() {
		getAccessTokenTimer.cancel();
	}

	@Override
	public void init() throws ServletException {
		class Task extends TimerTask {
			@Override
			public void run() {
				// 超过113分钟，更新accessToken
				String accessToken = null;
				while(accessToken == null){
					accessToken = getToken(PropertyUtil.getPropertyParam(
							"weixin.properties", "appid"),
							PropertyUtil.getPropertyParam("weixin.properties",
									"appsecret"));
				}
				PropertyUtil.setProperties("weixin.properties", "accessToken",
						accessToken);
			}

		}
		getAccessTokenTimer = new Timer();
		/**
		 * 这里把时间设置为113分钟。避免其他线程拿到过时的accessToken
		 */
		getAccessTokenTimer.schedule(new Task(), 0, 113 * 60 * 1000);
		
		//为了解决获取access token 的延时问题开一个线程输出access token
		class printAccessTokenThread extends Thread  
		{  
		    @SuppressWarnings("static-access")
			public void run() 
		    {  
		    	try {
		    		//让他睡6秒，等等计划任务执行完在输出
					this.sleep(6*1000);
				} catch (InterruptedException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
		    	System.out.println("已经获得accessToken:");
				System.out.println(PropertyUtil.getPropertyParam(
						"weixin.properties", "accessToken"));
		    }  
		} 
		new printAccessTokenThread().start();
	}

	private String getToken(String appid, String appsecret) {
		String tokenUrl = PropertyUtil.getPropertyParam("weixin.properties",
				"tokenUrl");
		String requestUrl = tokenUrl.replace("APPID", appid).replace(
				"APPSECRET", appsecret);
		JSONObject jsonObject = CommonUtil
				.httpsRequest(requestUrl, "GET", null);
		String token = null;
		if (null != jsonObject) {
			token = jsonObject.getString("access_token");
		}
		return token;
	}