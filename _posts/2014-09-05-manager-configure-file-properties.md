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
		
		public class ConfigureProperties {
			private static Map<String, Properties> fileNameAndProperties = new HashMap<>();
			static {
				// 通过类加载器得到路径
				String configureFilePath = ConfigureProperties.class.getClassLoader().getResource("").getPath();
				//在windows下面路径中有空格就要替换掉
				configureFilePath = configureFilePath.replaceAll("%20", " ");
				//1.找到类加载目录下的所有配置文件
				File path = new File(configureFilePath);
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
				
				//把文件名和配置文件放到map里面去
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
					//可能是用户在类加载后修改了配置文件,在次查看文件内容是否变化
					if(result == null){
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
			
			//不通过map得到Properties
			private static Properties getPropertiesParamAfaterClassLoader(String fileName){
				String configureFilePath = getClassLoaderPath();
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
			
			private static String getClassLoaderPath(){
				// 通过类加载器得到路径
				String configureFilePath = ConfigureProperties.class.getClassLoader().getResource("").getPath();
				//在windows下面路径中有空格就要替换掉
				configureFilePath = configureFilePath.replaceAll("%20", " ");
				return configureFilePath;
			}
		}
