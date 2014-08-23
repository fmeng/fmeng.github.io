---
layout: post
title: "HTTPS复习"
categories:
- other
---


	public class HttpClientUtil {  
	    public static void main(String[] args)throws Exception{  
	        //String requestUrl = "http://127.0.0.1:8088/test/web/userac";  
	        String requestUrl = "https://127.0.0.1:8443/test/web/userac";  
	        System.out.println(sendSSLRequest(requestUrl));  
	    }  
	      
	    /** 
	     * 发送HTTPS请求 
	     * @param requestUrl 请求的地址 
	     * @return 响应内容 
	     */  
	    @SuppressWarnings("finally")  
	    public static String sendSSLRequest(String requestUrl){  
	        long responseLength = 0;       //响应长度  
	        String responseContent = null; //响应内容  
	        HttpClient httpClient = new DefaultHttpClient(); //创建默认的httpClient实例  
	        try {  
	            KeyStore trustStore = KeyStore.getInstance(KeyStore.getDefaultType());  
	            FileInputStream fis = new FileInputStream(new File("F:\\Tool\\IDE\\Jadyer_SSL_20120508.keystore"));  
	            try {  
	                trustStore.load(fis, "hongyu75".toCharArray()); //加载KeyStore  
	            } catch (NoSuchAlgorithmException e) {  
	                e.printStackTrace();  
	            } catch (CertificateException e) {  
	                e.printStackTrace();  
	            } catch (IOException e) {  
	                e.printStackTrace();  
	            } finally {  
	                try {  
	                    fis.close();  
	                } catch (IOException e) {  
	                    e.printStackTrace();  
	                }  
	            }  
	            SSLSocketFactory socketFactory = new SSLSocketFactory(trustStore);   //创建Socket工厂,将trustStore注入  
	            Scheme sch = new Scheme("https", 8443, socketFactory);               //创建Scheme  
	            httpClient.getConnectionManager().getSchemeRegistry().register(sch); //注册Scheme  
	            HttpGet httpGet = new HttpGet(requestUrl);           //创建HttpGet  
	            HttpResponse response = httpClient.execute(httpGet); //执行GET请求  
	            HttpEntity entity = response.getEntity();            //获取响应实体  
	            if (null != entity) {  
	                responseLength = entity.getContentLength();  
	                responseContent = EntityUtils.toString(entity, "UTF-8");  
	                EntityUtils.consume(entity); //Consume response content  
	            }  
	            System.out.println("请求地址: " + httpGet.getURI());  
	            System.out.println("响应状态: " + response.getStatusLine());  
	            System.out.println("响应长度: " + responseLength);  
	            System.out.println("响应内容: " + responseContent);  
	        } catch (KeyManagementException e) {  
	            e.printStackTrace();  
	        } catch (UnrecoverableKeyException e) {  
	            e.printStackTrace();  
	        } catch (KeyStoreException e) {  
	            e.printStackTrace();  
	        } catch (FileNotFoundException e) {  
	            e.printStackTrace();  
	        } catch (NoSuchAlgorithmException e) {  
	            e.printStackTrace();  
	        } catch (ClientProtocolException e) {  
	            e.printStackTrace();  
	        } catch (ParseException e) {  
	            e.printStackTrace();  
	        } catch (IOException e) {  
	            e.printStackTrace();  
	        } finally {  
	            httpClient.getConnectionManager().shutdown(); //关闭连接,释放资源  
	            return responseContent;  
	        }  
	    }  
	}  