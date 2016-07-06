---
layout: post
title: "BeanUtils-把其他对象转换成Bean"
categories:
- java
---

相关Jar下载：[http://pan.baidu.com/s/1gfDbHbP](http://pan.baidu.com/s/1gfDbHbP)&emsp;&emsp;密码：ahzs
<font color=red><b>

1. BeanUtils方便把其他的数据整合方式快速的转换成对象。
2. 当一个对象中的变量数量未知时，要访问所有的变量可以使用内省。
</b></font>
---
		package com.fmeng.reflect.test;
		
		import java.util.Date;
		
		public class PersonBean {
			private String name;
			private String password;
			private int age;
			private Date birthday;
		
			public Date getBirthday() {
				return birthday;
			}
		
			public void setBirthday(Date birthday) {
				this.birthday = birthday;
			}
		
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


----

	package com.fmeng.reflect.test;
	
	import java.beans.BeanInfo;
	import java.beans.IntrospectionException;
	import java.beans.Introspector;
	import java.beans.PropertyDescriptor;
	import java.lang.reflect.InvocationTargetException;
	import java.lang.reflect.Method;
	import java.text.ParseException;
	import java.text.SimpleDateFormat;
	import java.util.Date;
	
	import org.apache.commons.beanutils.BeanUtils;
	import org.apache.commons.beanutils.ConvertUtils;
	import org.apache.commons.beanutils.Converter;
	
	public class BeanU {
	
		public static void main(String[] args) throws IllegalAccessException,
				InvocationTargetException, IntrospectionException {
			// BeanUtil 也有已经写好的Converter
			ConvertUtils.register(new Converter() {
	
				@Override
				public <T> T convert(Class<T> type, Object value) {
					//判断value是合法的非空字符串（判断数据的合法性）
					if (value != null && value instanceof String
							&& (!"".equals(String.valueOf(value).trim()))) {
						SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
						try {
							return (T)sdf.parse((String)value);
						} catch (ParseException e) {
							throw new RuntimeException(e);
						}
					}
					return null;
				}
			}, Date.class);
			
			// 假如从浏览器端传来如下参数：
			PersonBean pb = new PersonBean();
			String name = "fmeng";
			String password = "pwd";
			String age = "39";
			String birthday = "1990-09-16";
			BeanUtils.setProperty(pb, "name", name);
			BeanUtils.setProperty(pb, "password", password);
			BeanUtils.setProperty(pb, "age", age);
			// BeanUtils只支持8中类型的数据类型转换，为了正确转换城日期类型，要注册转换器转换日期类型
			BeanUtils.setProperty(pb, "birthday", birthday);
			BeanInfo bi = Introspector.getBeanInfo(PersonBean.class, Object.class);
			PropertyDescriptor [] pds = bi.getPropertyDescriptors();
			for(PropertyDescriptor pd : pds){
				Method method = pd.getReadMethod();
				System.out.println(pd.getName() + ":" + method.invoke(pb).toString());
			}

		}
	
	}

总结：**第三方jar往往提供了解决某类问题的方案。**学习使用一个Jar包，要明白这个包是用来干什么的，什么时候使用比较方便。同时这些第三方Jar包往往封装了一系列的工具，以供使用。**当遇到棘手的问题时，先尝试寻找Jar中是否有解决该问题的办法。**




