---
layout: post
title: "JSON和对象之间的相互装换"
categories:
- java
---
json-lib和其依赖的jar包：
[http://pan.baidu.com/s/1hqf5Oaw](http://pan.baidu.com/s/1hqf5Oaw) <br/>

commons-beanutils-1.7.0.jar<br/>
commons-collections-3.2.1jar<br/>
commons-lang-2.3.jar<br/>
commons-logging-1.2.jar<br/>
ezmorph-1.0.6.jar<br/>
json-lib-2.2.3-jdk13.jar<br/>

#### 代码分析 ####

![Button对象](/img/menu_uml.jpg)

#### 内存分析 ####

![object](/img/object_menu.jpg)

#### 效果展示 ####

![object](/img/weixin_menu.jpg)

#### 转换流程 ####

![object](/img/string_json_my_object.jpg)

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
	//然后根据JsonObject实际包含的对象不同做不同的处理

#### 代码实现 ####

`book.java`
	
	package com.hellopipi.test;
	
	public class Book {
		private String name = "book name";
	
		// 一定要有空参数的构造方法
		public Book() {
		}
	
		public Book(String name) {
			this.name = name;
		}
	
		public String getName() {
			return name;
		}
	
		public void setName(String name) {
			this.name = name;
		}	
	}

`User.java`

	package com.hellopipi.test;
	
	import java.util.ArrayList;
	import java.util.HashMap;
	import java.util.List;
	import java.util.Map;
	
	public class User {
		private String name = "fmeng";
		private String id = "123456";
		private Book book = new Book();
		private Book[] bookArray = new Book[] { new Book("arrayBook1"),
				new Book("arrayBook2") };
		private List<Book> bookList = new ArrayList<>();
		private Map<String, Book> bookMap = new HashMap<String, Book>();
	
		// 一定要有空参数的构造方法
		public User() {
			this.bookList.add(new Book("listBook1"));
			this.bookList.add(new Book("listBook2"));
			this.bookMap.put("one", new Book("mapBook1"));
			this.bookMap.put("two", new Book("mapBook2"));
		}
	
		public String getName() {
			return name;
		}
	
		public void setName(String name) {
			this.name = name;
		}
	
		public String getId() {
			return id;
		}
	
		public void setId(String id) {
			this.id = id;
		}
	
		public Book getBook() {
			return book;
		}
	
		public void setBook(Book book) {
			this.book = book;
		}
	
		public Book[] getBookArray() {
			return bookArray;
		}
	
		public void setBookArray(Book[] bookArray) {
			this.bookArray = bookArray;
		}
	
		public List<Book> getBookList() {
			return bookList;
		}
	
		public void setBookList(List<Book> bookList) {
			this.bookList = bookList;
		}
	
		public Map<String, Book> getBookMap() {
			return bookMap;
		}
	
		public void setBookMap(Map<String, Book> bookMap) {
			this.bookMap = bookMap;
		}
		
		
	}

`main.java`

	package com.hellopipi.test;
	import java.util.Iterator;
	import java.util.LinkedList;
	import java.util.List;
	import net.sf.json.JSONArray;
	import net.sf.json.JSONObject;
	public class TestJson_ {
	
		@SuppressWarnings("deprecation")
		public static void main(String[] args) throws InstantiationException, IllegalAccessException, NoSuchMethodException, SecurityException {
	/**
	 * 从bean到String,从String到bean
	 */
			// 1.从bean对象到String
			User user = new User();
			JSONObject jsonObject = JSONObject.fromObject(user);
			String userString = jsonObject.toString();
	System.out.println(userString);
			// 1.从String到bean对象	
			jsonObject = JSONObject.fromObject(userString);
			user = (User) JSONObject.toBean(jsonObject, User.class);
	System.out.println(user);
	
	/**
	 * 从数组到String,从String到数组
	 */
		   //2. 从数组到String
			boolean [] boolArray = new boolean [] {true, false, true};
			JSONArray jsonArray = JSONArray.fromObject(boolArray);
			String s = jsonArray.toString();
	System.out.println(s);
			//2.从String到数组
			jsonArray = JSONArray.fromObject(s);
			@SuppressWarnings("unchecked")
			Iterator<Object> i = jsonArray.iterator();
			boolean [] array = null;
			List<Boolean> bList = new LinkedList<>();
			while((i != null) && (i.hasNext())){
				Object object = i.next();
				Boolean b = (Boolean)object;
				bList.add(b);
			}
			int length = bList.size();
			array = new boolean [length];
			for(int j=0; j<length; j++){
				array[j] = bList.get(j);
			}
	System.out.println(array);
	
	/**
	 * 从list到String，从String到list
	 */
		//list --> array
		List<String> list = new LinkedList<>();
		list.add("t1");
		list.add("t2");
		list.add("t3");
		JSONArray jsonArray2 = JSONArray.fromObject(list);
		String sss = jsonArray2.toString();
	System.out.println(sss);
		//array --list
		JSONArray jsonArray3 = JSONArray.fromObject(sss);
		@SuppressWarnings("unchecked")
		List<String> aa = JSONArray.toList(jsonArray3, String.class);
	System.out.println(aa);
	
	/**
	 * 要注意使用JSONObject和JSONArray的静态方法
	 */
		}
	}

#### 总结： ####
 **在使用对象装换成Json格式的字符串时，Json转化机制会使用，构建对象的类的名字，变量的名字作为Json字符串结构的组成部分。为了适应Json格式的字符串在传输过程中的规范，有时候我们要有意改变对象或者属性的命名。**