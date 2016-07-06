---
layout: post
title: "Java 反射技术"
categories:
- java
---

[http://www.cnblogs.com/myadmin/p/5282628.html](http://www.cnblogs.com/myadmin/p/5282628.html)<br/>
重点理解了类的加载机制，理解起来就会容易。<br/>[JAVA类加载机制（一）](http://www.fmeng.me/java/2016/07/05/java-class-loader-system-1.html)&emsp;&emsp;[JAVA类加载机制（二）](http://www.fmeng.me/java/2016/07/05/java-class-loader-system-2.html)

1. **什么是反射？**一个类由多个组成部分，例如：成员变量、方法、构造方法等。反射就是<font color = red>**加载类，并解剖出类的各个组成部分**。</font>

2. **什么时候用到反射？** 反射<font color = red>**（一种特殊解剖形式）**</font>是用来做框架的。框架提供的创建某个类对象的入口，只是某个“com.fmeng.test”字符串。我们要<b>通过这个字符串创建对象，就要使用到反射技术。</b>

3. 通过发射调用一个数组参数的方法，要注意在数组前面加上（Object）来确定一个对象。

-------
	package com.fmeng.reflect.test;
	
	import java.lang.reflect.Constructor;
	import java.lang.reflect.Field;
	import java.lang.reflect.InvocationTargetException;
	import java.lang.reflect.Method;
	import java.util.ArrayList;
	import java.util.List;
	
	class Person {
		private String name = "Person Class";
		public static int i = 0;
	
		public Person() {
			System.out.println("person");
		}
	
		public Person(String name) {
			System.out.println("person");
		}
	
		public Person(String name, int password) {
			System.out.println("person");
		}
	
		public Person(int i) {
			System.out.println("Person" + i);
		}
	
		private Person(List list) {
			System.out.println("Person Constructor, Paramenter List");
		}
	
		public String getName() {
			return this.name;
		}
	
		public String run(String[] str) {
			return "run String";
		}
	}
	
	public class Demo1 {
	
		public static void main(String[] args) throws ClassNotFoundException,
				NoSuchMethodException, SecurityException, InstantiationException,
				IllegalAccessException, IllegalArgumentException,
				InvocationTargetException, NoSuchFieldException {
			// 1. 通过反射类的构造函数，实例化一个对象
			Class<?> clazz = Class.forName("com.fmeng.reflect.test.Person");
			Constructor<?> c = clazz.getConstructor(int.class);
			Person person = (Person) c.newInstance(1);
			System.out.println(person.getName());
			// 2. 通过私有构造方法，暴力反射一个类，构建对象
			System.out.println("------------------------");
			Class<?> clazz2 = Class.forName("com.fmeng.reflect.test.Person");
			Constructor<?> c2 = clazz.getDeclaredConstructor(List.class);
			c2.setAccessible(true);
			Person p = (Person) c2.newInstance(new ArrayList());
			System.out.println(p.getName());
			// 3. 通过反射，访问对象里的属性
			System.out.println("------------------------");
			Person pp = new Person();
			pp.i = 100;
			Class<?> clazz3 = Class.forName("com.fmeng.reflect.test.Person");
			Field filed = clazz3.getField("i");
			// 得到属性的类型和数值
			Class<?> type = filed.getType();
			Object o = filed.get(p);
			if (int.class.equals(type)) {
				Integer in = (Integer) o;
				int i = in.intValue();
				System.out.println("Person paramenter i = " + i);
			}
			// 4. 通过反射访问里面的函数
			Person pp2 = new Person();
			Class<?> clazz4 = Class.forName("com.fmeng.reflect.test.Person");
			Method m = clazz4.getMethod("run", String[].class);
			Object oo = m.invoke(pp2, (Object) new String[] { "1" });
			System.out.println((String) oo);
		}
	}

总结：发射技术的理解 ，是基于类的加载机制的。`ClassLoader`、`Class`行程概念，更容易理解。另外，类的私有方法和属性不可对外访问，可以用<font color=red>**反射技术实现访问私有方法和属性，使用`Constructor.setAccessible(true);`做到。**</font>

