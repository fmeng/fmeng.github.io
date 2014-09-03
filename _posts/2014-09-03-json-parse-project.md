---
layout: post
title: "JSON和对象之间的相互装换"
categories:
- java
---
json-lib和其依赖的jar包：
[http://pan.baidu.com/s/1dDy6Eo5](http://pan.baidu.com/s/1dDy6Eo5 "Json-lib-jar-download")  <br/>

commons-beanutils-1.7.0.jar<br/>
commons-collections-3.2.1jar<br/>
commons-lang-2.5.jar<br/>
commons-logging-1.1.1.jar<br/>
ezmorph-1.0.3.jar<br/>
json-lib-2.2.2-jdk15.jar<br/>

#### 代码分析 ####

![Button对象](/img/menu_uml.jpg)

#### 内存分析 ####

![object](/img/object_menu.jpg)

#### 效果展示 ####

![object](/img/weixin_menu.jpg)

#### 实现代码： ####

	private static Menu getMenu() {
			/**
			 * 左边按钮
			 */
			ClickButton btn11 = new ClickButton();
			btn11.setName("登陆图书馆");
			btn11.setType("click");
			btn11.setKey("C11_LIB");
			
			ClickButton btn12 = new ClickButton();
			btn12.setName("教务系统");
			btn12.setType("click");
			btn12.setKey("C12_CHOSING");
			
			ComplexButton btn1 = new ComplexButton();
			btn1.setName("沈师服务");
			btn1.setSub_button(new Button [] {btn11, btn12});
			
			/**
			 * 中间按钮
			 */
			ViewButton btn21 = new ViewButton();
			btn21.setName("图书馆认证");
			btn21.setType("view");
			btn21.setUrl("http://www.baidu.com");
			
			ViewButton btn22 = new ViewButton();
			btn22.setName("教学平台");
			btn22.setType("view");
			btn22.setUrl("http://www.qq.com");
			
			ComplexButton btn2 = new ComplexButton();
			btn2.setName("其他服务");
			btn2.setSub_button(new Button [] {btn21, btn22});
			/** 
			 * 右边按钮
			 */
			ClickButton btn31 = new ClickButton();
			btn31.setName("Hello World");
			btn31.setType("click");
			btn31.setKey("C31_HELLO_WORLD");
			
			ClickButton btn32 = new ClickButton();
			btn32.setName("关于皮皮");
			btn32.setType("click");
			btn32.setKey("V32_ABOUT_PIPI");
			
			ComplexButton btn3 = new ComplexButton();
			btn3.setName("关于我们");
			btn3.setSub_button(new Button [] {btn31, btn32});
			
			/**
			 * 创建菜单
			 */
			Menu menu = new Menu();
			menu.setButton(new Button [] {btn1, btn2, btn3});
			
			return menu;
		}

#### JSON字符串 ####

	{
	    "button": [
	        {
	            "name": "沈师服务", 
	            "sub_button": [
	                {
	                    "key": "C11_LIB", 
	                    "name": "登陆图书馆", 
	                    "type": "click"
	                }, 
	                {
	                    "key": "C12_CHOSING", 
	                    "name": "教务系统", 
	                    "type": "click"
	                }
	            ]
	        }, 
	        {
	            "name": "其他服务", 
	            "sub_button": [
	                {
	                    "name": "图书馆认证", 
	                    "type": "view", 
	                    "url": "http://www.baidu.com"
	                }, 
	                {
	                    "name": "教学平台", 
	                    "type": "view", 
	                    "url": "http://www.qq.com"
	                }
	            ]
	        }, 
	        {
	            "name": "关于我们", 
	            "sub_button": [
	                {
	                    "key": "C31_HELLO_WORLD", 
	                    "name": "Hello World", 
	                    "type": "click"
	                }, 
	                {
	                    "key": "V32_ABOUT_PIPI", 
	                    "name": "关于皮皮", 
	                    "type": "click"
	                }
	            ]
	        }
	    ]
	}

#### 核心代码 ####

	//将菜单对象转换成字符串
	String jsonMenu = JSONObject.fromObject(menu).toString();
	//将字符串转换成JsonObject
	jsonObject = JSONObject.fromObject(string);

#### 总结： ####
 **在使用对象装换成Json格式的字符串时，Json转化机制会使用，构建对象的类的名字，变量的名字作为Json字符串结构的组成部分。为了适应Json格式的字符串在传输过程中的规范，有时候我们要有意改变对象或者属性的命名。**