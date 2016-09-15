---
layout: post
title: "深入理解java动态代理"
categories:
- java
---
### <a>资料：</a> ###

1. [设计模式，静态代理和动态代理](http://www.fmeng.me/java/2016/06/12/java-pattern.html#22)
1. [http://www.codekk.com/blogs/detail/54cfab086c4761e5001b253d](http://www.codekk.com/blogs/detail/54cfab086c4761e5001b253d)
2. [https://www.ibm.com/developerworks/cn/java/j-lo-proxy1/](https://www.ibm.com/developerworks/cn/java/j-lo-proxy1/)
3. [https://www.ibm.com/developerworks/cn/java/j-lo-proxy2/](https://www.ibm.com/developerworks/cn/java/j-lo-proxy2/)
4. [《设计模式：Java语言中的应用》](http://pan.baidu.com/s/1gfuOLYr) &emsp;&emsp;密码：imup
5. [《深入Java虚拟机》](http://pan.baidu.com/s/1gfG3275) &emsp;&emsp;密码：hm8w

------
基本概念

1. 需求。不希望或是不能直接访问对象 A，而是通过访问一个中介对象 B，由 B 去访问 A 达成目的
2. 优点
	1. 隐藏委托类的实现
	2. 解耦（不改变委托类代码情况下做一些额外处理，比如添加初始判断及其他公共操作）
3. 分类
	1. 静态代理（代理类在程序运行前已经存在的代理方式称为静态代理）
	2. 动态代理（代理类在程序运行前不存在、运行时由程序动态生成的代理方式称为动态代理）
3. <font color="red">实现InvocationHandler接口的类，可以持有真实对象target的引用，也可以不持有target的引用</font>

	1.`Client.java`

	 	// create proxy instance
	    TimingInvocationHandler timingInvocationHandler = new 
		TimingInvocationHandler(new OperateImpl());
		Operate operate = (Operate)Proxy.newProxyInstance(Operate.class.getClassLoader(), 
			new Class[] {Operate.class},
		    timingInvocationHandler);
	2.`timingInvocationHandler.java`

		public class TimingInvocationHandler implements InvocationHandler {
			
		    private Object target;
			// 无参、默认构造器
		    public TimingInvocationHandler() {}
			// 委托类对象，通过构造函数传过来
		    public TimingInvocationHandler(Object target) {
		        this.target = target;
		    }
		
		    @Override
		    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
				// 循环遍历，proxy的所有方法，return为方法的返回值
		        long start = System.currentTimeMillis();
		        Object obj = method.invoke(target, args);
		        System.out.println(method.getName() + " cost time is:" + (System.currentTimeMillis() - start));
		        return obj;
		    }
		}
	3.Operate（真实对象实现的接口）

		public interface Operate {
		    public void operateMethod1();
		    public void operateMethod2();
		    public void operateMethod3();
		}

	4.OperateImpl（真实对象的实现类）

		public class OperateImpl implements Operate {
		
		    @Override
		    public void operateMethod1() {
		        System.out.println("Invoke operateMethod1");
		        sleep(110);
		    }
		
		    @Override
		    public void operateMethod2() {
		        System.out.println("Invoke operateMethod2");
		        sleep(120);
		    }
		
		    @Override
		    public void operateMethod3() {
		        System.out.println("Invoke operateMethod3");
		        sleep(130);
		    }
		
		    private static void sleep(long millSeconds) {
		        try {
		            Thread.sleep(millSeconds);
		        } catch (InterruptedException e) {
		            e.printStackTrace();
		        }
		    }
		}
5.解析`Proxy.newProxyInstance(...)`<br/>
`public static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h);`
	1. loader表示类加载器
	2. interfaces表示委托类的接口（生成代理类时需要实现这些接口）
	3. h是InvocationHandler实现类对象（负责连接代理类和委托类的中间类）<br/>
如上的动态代理实现实际是双层的静态代理，开发者提供了委托类 B，程序动态生成了代理类 A。开发者还需要提供一个实现了InvocationHandler的子类 C，子类 C 连接代理类 A 和委托类 B，它是代理类 A 的委托类，委托类 B 的代理类。用户直接调用代理类 A 的对象，A 将调用转发给委托类 C，委托类 C 再将调用转发给它的委托类 B。

#### 3. 动态代理原理 ####
<a>参照，</a>[公共技术点之 Java 动态代理 3.2](http://www.codekk.com/blogs/detail/54cfab086c4761e5001b253d)


