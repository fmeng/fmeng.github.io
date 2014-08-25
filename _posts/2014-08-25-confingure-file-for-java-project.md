---
layout: post
title: "如何从Java项目配置文件中读取数据"
categories:
- java
---
1. 把配置文件加载数据的代码放到静态代码块
1. 设计好从Java中读取文件的工具类
2. 通过类加载器读取配置文件的路径
3. 通过文件流的形式读取文件

----
	package shenshi.library.utils;
	
	import java.io.File;
	import java.io.FileInputStream;
	import java.io.IOException;
	import java.util.Properties;
	
	
	public class ConfigureProperties {
		private static Properties weixinConfigure = null;
		static {
			// 通过类加载器得到路径
			String configureFilePath = ConfigureProperties.class.getClassLoader()
					.getResource("lib.properties").getPath();
			configureFilePath = configureFilePath.replaceAll("%20", " ");
			// 通过文件流的形式读取文件
			FileInputStream in = null;
			try {
				in = new FileInputStream(new File(configureFilePath));
				weixinConfigure = new Properties();
				weixinConfigure.load(in);
				
			} catch (Exception e) {
				throw new ExceptionInInitializerError(e);
			}finally{
				if (null != null) {
					try {
						in.close();
					} catch (IOException e) {
						throw new ExceptionInInitializerError(e);
					}
				}
			}
		}
	
		public static String getProperty(String proterty){
			return weixinConfigure.getProperty(proterty);
		}
	}
