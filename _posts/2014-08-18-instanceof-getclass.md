---
layout: post
title: "getClass和instanceof"
categories:
- java
## 比较getclass和instanceof，==和equals ##

* instanceof 
 1. 用于比较有继承关系的类
 2. 前者(已经实现的对象)，是不是后者(类)的子类

* getclass
	1. getclass得到的是实现该示例的类，有引用和堆中数据。
	2. ==只能比较引用是同一类型的数据，即使存在继承关系，也是不能比较(无法变异通过)
	3. equals比较的是堆中数据。对数据类型没有特殊要求
****
		class Father{
			private String f = "father";
		}
		
		class Brother extends Father{
			private String b = "brother";
		}
		
		class Sister extends Father{
			private String s = "sister";
		}
		
		public class Test {
			public static void main(String [] args){
				Father father = new Father();
				Brother brother = new Brother();
				Sister sister = new Sister();
		System.out.println(father instanceof Father);
		System.out.println(father instanceof Brother);//false
		System.out.println(father instanceof Sister);//false
		System.out.println(brother instanceof Brother);
		System.out.println(brother instanceof Father);
		//System.out.println(brother instanceof Sister);
		/**
		 * instanceof 
		 * 1.用于比较有继承关系的类
		 * 2.前者(已经实现的对象)，是不是后者(类)的子类
		 */
		System.out.println(father.getClass() == brother.getClass());
		System.out.println(father.getClass().equals(brother.getClass()));
		System.out.println(brother.getClass() == father.getClass());
		System.out.println(brother.getClass().equals(father.getClass()));
		//System.out.println(brother.getClass() == sister.getClass());
		System.out.println(brother.getClass().equals(sister.getClass()));
		//System.out.println(sister.getClass() == Father.class);
		System.out.println(sister.getClass().equals(Father.class));
		/**
		 * 1.getclass得到的是实现该示例的类，有引用和堆中数据。
		 * 2.==只能比较引用是同一类型的数据，即使存在继承关系，也是不能比较(无法变异通过)
		 * 3.equals比较的是堆中数据。对数据类型没有特殊要求
		 */
			}
		}