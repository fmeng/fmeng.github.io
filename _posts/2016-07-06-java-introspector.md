---
layout: post
title: "内省（Introspector）"
categories:
- java
---

1. <font color=red>使用反射技术，专门用来操作Bean的属性。
3. 因为每次都会用到固定的反射技术，所以就把经常使用到的反射技术包装起来成为内省。</font>
4. 涉及到的类：Introspector、Method、Field、Constructor

-----
构架一个JavaBean

	package com.fmeng.reflect.test;
	
	public class PersonBean {
		private String name;
		private String password;
		private int age;
	
		public String getName() {
			return name;
		}
	
		public void setName(String name) {
			this.name = name;
		}
	
		public String getPassword() {
			return password;
		}
	
		public void setPassword(String password) {
			this.password = password;
		}
	
		public int getAge() {
			return age;
		}
	
		public void setAge(int age) {
			this.age = age;
		}
	}

测试内省introspector

	package com.fmeng.reflect.test;
	
	import java.beans.BeanInfo;
	import java.beans.IntrospectionException;
	import java.beans.Introspector;
	import java.beans.PropertyDescriptor;
	import java.lang.reflect.InvocationTargetException;
	import java.lang.reflect.Method;
	
	public class Intro {
		public static void main(String[] args) throws IntrospectionException,
				ClassNotFoundException, IllegalAccessException,
				IllegalArgumentException, InvocationTargetException {
			System.out.println("--------------------------");
			// 1. 得到Bean的所有属性
			Class<?> clazz = Class.forName("com.fmeng.reflect.test.PersonBean");
			// 通过内省类，得到Bean的所有信息对象,去除父类的属性
			BeanInfo beanInfo = Introspector.getBeanInfo(clazz, Object.class);
			// 得到属性描述器
			PropertyDescriptor[] propertyDescriptors = beanInfo
					.getPropertyDescriptors();
			for (PropertyDescriptor pd : propertyDescriptors) {
				System.out.println(pd.getName());
			}
			System.out.println("---------------------------");
			// 2. 使用方法，操作Bean的age属性
			PersonBean personBean = new PersonBean();
			PropertyDescriptor pd = new PropertyDescriptor("age", clazz);
			Method setMethod = pd.getWriteMethod();
			setMethod.invoke(personBean, 1);
			Method getMethod = pd.getReadMethod();
			getMethod.invoke(personBean);
			System.out.println(personBean.getAge());
	
		}
	}

输出结果：

	--------------------------
	age
	name
	password
	---------------------------
	1

-----
总结：<font color=red>**内省其实就是提供了基于反射的，操作对象的方法。**</font>这里的操作对象主要包括：构造函数、类的属性、类的方法。





