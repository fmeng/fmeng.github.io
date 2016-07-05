---
layout: post
title: "JAVA类加载机制（一）"
categories:
- java
---

[http://my.oschina.net/everyDay111/blog/508180](http://my.oschina.net/everyDay111/blog/508180)<br/>
视频资料：[链接：http://pan.baidu.com/s/1nv2abB7](链接：http://pan.baidu.com/s/1nv2abB7)&emsp;密码：g0gr<br/>
推荐书籍：[深入java虚拟机(iteye社区).pdf](http://pan.baidu.com/s/1pLgrsAv)&emsp;&emsp;密码：4n8l<br/>
推荐书籍：[《深入理解Java虚拟机：JVM高级特性与最佳实践》(第二版)](https://book.douban.com/subject/24722612/)<br/>&emsp;&emsp;&emsp;&emsp;&emsp;[下载PDF](http://pan.baidu.com/s/1c2kb4pq) &emsp;密码：cfyf

------

### 一、	下几种情况JVM结束生命周期： ###

1.	执行了System.exit();
2.	程序正常结束
3.	程序再运行过程中遇到Exception或Error
4.	操作系统错误

<br/>
![](/img/java-jvm1.jpg)<br/>
-----
![](/img/java-jvm2.jpg)<br/>
### 二、	类的加载、连接（验证、准备、解析）、初始化 ##

1. **加载。**
	1. 类的加载指的是将类的.class文件中的二进制数据读入到内存中，将其放在运行时数据区的方法区内<font color=red>**（先）**</font>，然后在堆区创建一个java.lang.Class对象<font color=red>**（后）**</font>，用来封装类在方法区内的数据结构
	2. <font color=red>**类的加载的最终产品是位于堆区中的Class对象。Class对象封装了类在方法区内的数据结构，并且向Java程序员提供了访问方法区内的数据结构的接口**</font>
	3. 加载.class文件的方式
		1. 从本地系统中直接加载
		2. 通过网络下载.class文件
		2. 从zip，jar等归档文件中加载.class文件
		4. 从专有数据库中提取.class文件
		5. 将Java源文件动态编译为.class文件
	4. 两种类加载器
		1. Java虚拟机自带的加载器
			1. 根类加载器（Bootstrap）<font color=red>（C++代码实现 getClassLoder() == null）</font>
			2. 扩展类加载器（Extension）（Java代码实现）
			3. 系统类加载器（System、应用加载器）（Java代码实现）
		2. 用户自定义的类加载器
			1. java.lang.ClassLoader的子类
			2. 用户可以定制类的加载方式
	5. 类加载器的加载时机
		1. JVM规范，ClassLoader可以预料某个类将要使用时，提前加载。如遇到class文件缺失或存在错误，程序首次主动使用该类时才报告LinkageError错误。如果这个类一直没有被程序主动使用，那么类加载器就不会报告错误。<font color=red>**（发现错误和报告错误的是两个时机）**</font>
		
>类被加载后，就进入连接阶段。连接就是将已经读入到内存的类的二进制<font color=red>**数据合并到虚拟机的运行时环境中去**</font>。

2. **验证。**确保被加载类的正确性<br/>
因为，可能被手动修改class文件。编译的时候要验证，这时也要验证。
<br/>![](/img/java-jvm3.jpg)<br/>
3. **准备。**为类的静态变量分配内存，并初始化为默认值
<br/><font color=red>**为变量分配内存，附默认值。**</font>
4. **解析。**<font color=red>**把类中的符号引用（java）转换成直接引用（c）**</FONT>
5. **初始化。**<font color=red>**为类的静态变量赋予用户定义的初始值**</FONT><br/>
只有当程序访问的静态变量或静态方法确实在当前类或当前接口中定义时，才可以认为是对类或接口的主动使用<br/>
**调用ClassLoader类的loadClass方法加载一个类，并不是对类的主动使用，不会导致类的初始化**
<br/>![](/img/java-jvm4.jpg)<br/>
### 三、Java程序对类的使用方式分为两种 ###
1. 主动使用<font color=red>**（6钟）**</font><font color=red>
	1. 创建类的实例
	1. 访问某个类或接口的静态变量，或者对静态变量赋值（调用编译时候的类的常量，不会导致被初始化）
	1. 调用类的静态方法
	1. 反射（Class.ForName(“com.fmeng.Test”)）
	1. 初始化一个类的子类
	1. Java虚拟机在启动时被标明为启动类的类（含有Main函数）
	</font>
2. 被动使用（除了主动使用，其他使用类的方式）

-----
#### 示例一、主要验证，在6种情况之一时类被初始化。 ####

1.&emsp;创建类的实例<BR/>
2.&emsp;Java虚拟机在启动时被标明为启动类的类（含有Main函数）
&emsp;

	package me.fmeng.jvm.test;
	/*
	 * 1. 类在初始化过程中，静态变量的值受赋默认初始值、类的初始化赋值2个因素影响
	 * 2. Class文件中含有一系列的操作码，它是由单字节指令组成的序列，每一个操作码后都	  有一个或多个操作数。
	 * 	  所以，静态变量也要按顺序初始化赋值 ，自上到下，先变量后静态代码块。
	 */
	class Singleton {
		/*
		 * 本例测试测过为：
		 * counter1: 1
		 * counter2: 0
		 * private static Singleton singleton = new Singleton();
		 * 分析结果，把改行放到下面，测试结果为：
		 * counter1: 1
		 * counter2: 0
		 */
		// 2. 类的准备，赋默认初始值 null     // 5. 类的初始化赋值，调用构造函数 counter1=1, counter2=1
		private static Singleton singleton = new Singleton();
		// 3. 类的准备，赋予默认初始值 0	      // 6. 类的初始化赋值，不用赋值
		public static int counter1; 
		// 4. 类的准备，赋予默认初始值 0		  // 7. 类的初始化赋值，counter2=0
		public static int counter2 = 0;
		// private static Singleton singleton = new Singleton();
	
		private Singleton() {
			counter1++;
			counter2++;
		}
		
		public static Singleton getSingleTon() {
			return singleton;
		}
	}
	
	public class Demo1 {
		public static void main(String[] args) {
			// 1. 调用了类的静态方法-->初始化类    		// 8. 初始化类构建对象
			Singleton singleton = Singleton.getSingleTon();
			// 通过实例对象调用静态属性、方法与通过类调用静态属性、方法相同。
			System.out.println("counter1: " + singleton.counter1);
			System.out.println("counter2: " + singleton.counter2);
		}
	}



运行结果：

	counter1: 1
	counter2: 0
把`private static Singleton singleton = new Singleton();`放到注释的地方、运行结果：

	counter1: 1
	counter2: 1

---------
#### 示例二、访问某个类或接口的静态变量，或者对静态变量赋值（调用编译时候的类的常量，不会导致被初始化） ####
	package me.fmeng.jvm.test;
	
	import java.util.Random;
	/*
	 * 1.2	访问某个类或接口的静态变量，或者对静态变量赋值（调用编译时候的类的常量，不会导致被初始化）
	 */
	class FinalTest1 {
		public static final int x = 6 / 2;
		static {
			System.out.println("FinalTest1 static block");
		}
	}
	
	class FinalTest2 {
		public static final int x = new Random().nextInt(100);
		static {
			System.out.println("FinalTest2 static block");
		}
	}
	
	public class Demo2 {
		public static void main(String[] args) {
			// 调用编译时候的类的常量，不会导致类被初始化
			System.out.println(FinalTest1.x);
			System.out.println("--------------------");
			// 只有在运行时才能确定x的值，类会初始化
			System.out.println(FinalTest2.x);
		}
	
	}



运行结果：

	3
	--------------------
	FinalTest2 static block
	53

----

#### 示例三、主要验证，在6种情况之一时类被初始化。 <font color=red>”1.5	初始化一个类的子类”</font>####

	package me.fmeng.jvm.test;
	
	class Parent {
		static int a = 3;
		static {
			System.out.println("Parent static block");
		}
	}
	
	class Child extends Parent{
		static int b = 4;
		static {
			System.out.println("Child static block");
		}
	}
	
	public class Demo3 {
		static {
			System.out.println("Demo3 static block");
		}
	
		public static void main(String[] args) {
			// 因为a变量是属于parent，所以是对父类的主动使用！
			System.out.println(Child.a);
			System.out.println("-----------------");
			System.out.println(Child.b);
		}
	
	}
输出结果：

	3
	--------------------
	FinalTest2 static block
	53

---

#### 示例三、主要验证，在6种情况之一时类被初始化。 <font color=red>”1.4 反射（Class.ForName(“com.fmeng.Test”)）”。调用ClassLoader的loadClass方法，不是对一个类的主动使用，不会初始化这个类</font>####

	package me.fmeng.jvm.test;
	
	class CL {
		static {
			System.out.println("Class CL");
		}
	}
	
	public class Demo4 {
		public static void main(String[] args) throws ClassNotFoundException {
			// 获得系统类加载器
			ClassLoader classLoader = ClassLoader.getSystemClassLoader();
			// 调用ClassLoader的loadClass方法，不是对一个类的主动使用，不会初始化这个类
			Class<?> clazz = classLoader.loadClass("me.fmeng.jvm.test.CL");
			System.out.println("-------------------");
			// 1.4	反射（Class.ForName(“com.fmeng.Test”)）
			// 是对一个类的主动使用！
			clazz = Class.forName("me.fmeng.jvm.test.CL");
	
		}
	
	}

输出结果：

	-------------------
	Class CL

-------

总结：解析JVM运行机制，从底层认识Class的加载过程，更方便分析问题。以后，**当遇到一个难以理解的感念的时候，要多尝试找到关联知识点**，很有可能是其他知识点不牢固导致概念理解困难！本次，就是在深入理解反射机制的时候，学习类的加载机制。









