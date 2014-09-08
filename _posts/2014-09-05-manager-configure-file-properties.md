---
layout: post
title: "Manager Configure File --Properties"
categories:
- java
---

## 要求 ##

1. **在修改配置文件以后最好重启服务**
1. 制作一个工具类统一读取配置文件的数据
2. 项目的所有配置文件，要放到类加载的根目录
3. 在类初始化以前，配置文件的累要先被加载进来
4. 不能多次读取配置文件，文件只能被加载一次
5. 使用相对路径，用类加载器找到配置文件所在的目录
6. 网properties中添加数据时：先判断properties文件是否存在，在写入文件，最后写入map
7. **因为properties文件的更改会引来多线程问题，在setProperty()上加锁**

## 使用方法 ##
		
	public class PropertyUtil {
		private static Map<String, Properties> fileNameAndProperties = new HashMap<>();
		private static final String propertyFilePath;
		static {
			// 通过类加载器得到路径,在windows下面路径中有空格就要替换掉
			propertyFilePath = PropertyUtil.class.getClassLoader().getResource("").getPath().replaceAll("%20", " ");
			//1.找到类加载目录下的所有配置文件
			File path = new File(propertyFilePath);
			File [] configFiles = path.listFiles((new FilenameFilter() {
				@Override
				public boolean accept(File dir, String name) {
					if((name != null) && (name.endsWith(".properties"))){
						return true;
					}else{
						return false;
					}
				}
			}));
			
			//发文件名和配置文件放到map里面去
			for(int i=0; i<configFiles.length; i++){
				String fileName = configFiles[i].getName();
				Properties properties = path2Propertis(configFiles[i]);
				fileNameAndProperties.put(fileName, properties);
			}
		}
	
		public static String getPropertyParam(String filename, String proterty){
			if(!fileNameAndProperties.containsKey(filename)){
					throw new RuntimeException("没有找到该配置文件！请检查文件名是否错误，或者类加载目录下是否有该配置文件！");
			}else{
				String result =  fileNameAndProperties.get(filename).getProperty(proterty);
				if(result == null){
					//可能是用户在类加载后修改了配置文件,在次查看文件内容是否变化
					Properties properties = getPropertiesParamAfaterClassLoader(filename);
					result = properties.getProperty(proterty);
					if(result != null){
						//说明配置文件在类加载后，修改过，要重新加载
						fileNameAndProperties.put(filename, properties);
					}
				}
				return result;
			}
		}
						
		public static Properties getPropertie(String filename){
			if(!fileNameAndProperties.containsKey(filename)){
				throw new RuntimeException("没有找到该配置文件！请检查文件名是否错误，或者类加载目录下是否有该配置文件！");
			}else{
				return fileNameAndProperties.get(filename);
			}
		}
						//避免多线程带来的问题，要给方法加锁
		public static synchronized void setProperties(String fileName, String attrName, String attr){
			Properties prop = fileNameAndProperties.get(fileName);
			if(prop == null){
				throw new RuntimeException("没有找到文件"+fileName);
			}else{
				String path = getPropertyfilepath() + fileName;
				//设置属性
				prop.put(attrName, attr);
				//写入文件
				FileOutputStream fos = null;
				try {
					fos = new FileOutputStream(path);
					prop.store(fos, "");
					fos.flush();
				} catch (Exception e) {
					throw new RuntimeException(e);
				}finally{
					if(null != fos){
						try {
							fos.close();
						} catch (IOException e) {
							throw new RuntimeException(e);
						}
					}
				}
				//更行map中属性
				fileNameAndProperties.put(fileName, prop);
			}
		}
				//避免多线程带来的问题，要给方法加锁
		public static synchronized void setProperties(String fileName, Properties property){
			Properties prop = fileNameAndProperties.get(fileName);
			if(prop == null){
				throw new RuntimeException("没有找到文件"+fileName);
			}else{
				String path = getPropertyfilepath() + fileName;
				//写入文件
				FileOutputStream fos = null;
				try {
					fos = new FileOutputStream(path);
					prop.store(fos, "");
					fos.flush();
				} catch (Exception e) {
					throw new RuntimeException(e);
				}finally{
					if(null != fos){
						try {
							fos.close();
						} catch (IOException e) {
							throw new RuntimeException(e);
						}
					}
				}
				//更新map
				fileNameAndProperties.put(fileName, property);
			}
		}
		
		//不通过map得到Properties
		private static Properties getPropertiesParamAfaterClassLoader(String fileName){
			String configureFilePath = propertyFilePath;
			String url = configureFilePath+fileName;
			Properties properties = path2Propertis(url);
			return properties;
		}
		
		private static Properties path2Propertis(String path){
			Properties properties = null;
			// 通过文件流的形式读取文件
			FileInputStream in = null;
			try {
				in = new FileInputStream(path);
				properties = new Properties();
				properties.load(in);
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
			return properties;
		}
		
		private static Properties path2Propertis(File file){
			Properties properties = null;
			// 通过文件流的形式读取文件
			FileInputStream in = null;
			try {
				in = new FileInputStream(file);
				properties = new Properties();
				properties.load(in);
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
			return properties;
		}
	
		public static String getPropertyfilepath() {
			return propertyFilePath;
		}
	
		public static Map<String, Properties> getFileNameAndProperties() {
			return fileNameAndProperties;
		}
		
		
	}
