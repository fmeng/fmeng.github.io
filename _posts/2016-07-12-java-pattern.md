---
layout: post
title: "Java 23种设计模式"
categories:
- java
---
学习视频和代码：<br/>&emsp;&emsp;&emsp;链接：[http://pan.baidu.com/s/1dF4sFwX](http://pan.baidu.com/s/1dF4sFwX) &emsp;密码：3dxr<br/>
**分析方法（包括但不限于）： <br/>**<font color=red>

1. 特点
2. 应用场景
3. 实现形式
4. 性能标准
5. 代码实现

</font>

1. <a href="1">**创建型模式**</a>
	1. <a href="#11">单例模式</a>
	2. <a href="#12">工厂模式</a>
	4. <a href="#14">建造者模式</a>
	5. <a href="#15">原型模式</a>
2. **构造型模式**
	1. <a href="#21">适配器模式</a>
	2. <a href="#22">桥接模式</a>
	3. <a href="#23">装饰模式</a>
	4. <a href="#24">组合模式</a>
	5. <a href="#25">外观模式</a>
	6. <a href="#26">享元模式</a>
	7. <a href="#27">代理模式</a>
3. **行为型模式**
	1. <a href="#31">模版方法模式</a>
	2. <a href="#32">命令模式</a>
	3. <a href="#33">迭代器模式</a>
	4. <a href="#34">观察者模式</a>
	5. <a href="#35">中介者模式</a>
	6. <a href="#36">备忘录模式</a>
	7. <a href="#37">解析器模式</a>
	8. <a href="#38">状态模式</a>
	9. <a href="#39">策略模式</a>
	10. <a href="#310">职责链模式</a>
	11. <a href="#311">访问者模式</a>
	
## <a name="1">1.创建型模式</a> ##


1. <a href="#11">单例模式</a><br/>保证一个类只有一个实例，并且提供一个访问该实例的全局访问点。
2. <a href="#12">工厂模式</a>
	1. 简单工厂模式
	   <br/>用来生产同一等级结构中的任意产品。（赠加新产品，需要修改已有代码）
	2. 工厂方法模式
	   <br/>用来生产同一等级结构中的固定产品。（支持增加任意产品）
	3. 抽象工厂模式
	   <br/>用来生产不同产品族的全部产品。（对与增加新的产品，无能为力，支持增加产品族）
4. <a href="#14">建造者模式</a>
    <br/>分离了对象子组件的单独构造器（由Builder来负责）和装配（由Director负责）。从而可以构造出复杂对象。
5. <a href="#15">原型模式</a>
   <br/>通过new产生一个对象需要非常繁琐的数据准备或访问权限，则可以使用原型模式。

### <a name="1">1.1单例模式</a> ###

1. **特点**
	1. 由于单例模式只生成一个实例，减少了系统性能开销。当一个对象的产生需要比较多的资源时（读取配置文件，产生其他依赖对象），则可通过在应用启动时产生一个单例对象，然后永久贮存在内存的方式解决。**多次创建，系统开销大，系统内部需要多次使用。**
	2. 单例模式可以在系统**设置全局访问点，优化资源访问**。（例如，可以设计一个单例类，负责所有数据表的映射处理）
2. **应用场景**
	1. 项目中读取配置文件
	2. 数据库连接池，因为数据库连接是一种数据库资源
	3. Spring中，每个Bean默认是单例化的，这样便于Spring容器管理
	4. SpringMVC框架中，控制器对象也是单例
2. **实现形式**<font color=red>（**保证一个类只有一个实例，并且提供一个访问该实例的全局访问点**）</font>
	1. 饿汉式
	2. 懒汉式
	3. 双重检测锁
	4. 静态内部类
	5. 枚举类<br/>	
5. **性能标准**
	1. **延时加载（在调用getInstance时实例化对象，还是在类加载的时实例化对象）**
	2. 线程安全（多个线程并发调用了getInstance方法）<br/>
<font color=red>**写完代码务必检查 `static` `final` `synchronized` `public` `private`关键字的使用**</font><br/>
6. **代码实现**
	1. **饿汉式**

			public class SingletonDemo1 {
				
				//类初始化时，立即加载这个对象（没有延时加载的优势）。加载类时，天然的是线程安全的！
				private static SingletonDemo1 instance = new SingletonDemo1();  
				
				private SingletonDemo1(){
				}
				
				//方法没有同步，调用效率高！
				public static SingletonDemo1  getInstance(){
					return instance;
				}
				
			}
	1. **懒汉式**
	
			public class SingletonDemo2 {
				
				//类初始化时，不初始化这个对象（延时加载，真正用的时候再创建）。
				private static SingletonDemo2 instance;  
				
				private SingletonDemo2(){ //私有化构造器
				}
				
				//方法同步，调用效率低！
				public static  synchronized SingletonDemo2  getInstance(){
					if(instance==null){
						instance = new SingletonDemo2();
					}
					return instance;
				}
				
			}
	1. **双重检测锁1**
	
			public class SingletonDemo5 {
				private static SingletonDemo5 instance;
			
				private SingletonDemo5() {
				}
			
				public static SingletonDemo5 getInstance() {
					if (instance == null) {
						synchronized (SingletonDemo5.class) {
							if (instance == null) {
								instance = new SingletonDemo5();
							}
						}
					}
					return instance;
				}
			}
	
	1. **双重检测锁2**（已解决jvm无序写入问题）

			public class SingletonDemo3 {
			
				private static SingletonDemo3 instance = null;
			
				public static SingletonDemo3 getInstance() {
					if (instance == null) {
						SingletonDemo3 sc;
						synchronized (SingletonDemo3.class) {
							sc = instance;
							if (sc == null) {
								synchronized (SingletonDemo3.class) {
									if (sc == null) {
										sc = new SingletonDemo3();
									}
								}
								instance = sc;
							}
						}
					}
					return instance;
				}
			
				private SingletonDemo3() {
			
				}
			
			}
	2. **静态内部类**

			public class SingletonDemo4 {
			
				private static class SingletonClassInstance {
					private static final SingletonDemo4 instance = new SingletonDemo4();
				}
			
				private SingletonDemo4() {
				}
			
				// 方法没有同步，调用效率高！
				public static SingletonDemo4 getInstance() {
					return SingletonClassInstance.instance;
				}
			
			}
	1. **枚举类**

			public enum SingletonDemo5 {
			
				// 这个枚举元素，本身就是单例对象！
				INSTANCE;
			
				// 添加自己需要的操作！
				public void singletonOperation() {
				}
			
			}

总结：核心思想，**保证一个类只有一个实例，并且提供一个访问该实例的全局访问点。**在编码过程中注意检查关键子的书写。在实践中，使用单例模式开发，注意应用场景。

---
### <a name="12">1.2工厂模式</a> ###
<font color=red><b>根据设计理论建议：工厂方法模式。但实际上我们一般都用简单工厂模式。</b></font>

1. **面向对象设计的基本原则**
	1. OCP（Open-Closed Principle，开闭原则）:一个软件的实体应该对扩展开放，对修改关闭
	2. DIP（Dependence Inversion Principle，依赖倒转原则）：针对接口编程，不要针对实现编程
	3. LoD（Low of Demeter，迪米特法则）：只与你直接的朋友同行，而避免和陌生人通信
2. **应用场景**
	1. JDK中的Calendar中的getInstance方法
	2. JDBC中Connection对象的获取
	3. Spring中IOC容器创建、管理Bean对象
	4. XML解析时的DocumentBuilderFactory创建解析器对象
	5. 反射中的Class对象的newInstance()
3. **实现形式**<br/><font color=red><b>实例化对象，用工厂方法代替new；将选择实现类、创建对象统一管理和控制。从而将调用者跟我们的实现类解耦。</b></font>
	1. **简单工厂模式**<br/>
	   用来产生同一等级结构中的任意产品。（**增加新的产品，需要修改已有代码**）
	2. **工厂方法模式**<br/>
	   用来生产同一等级结构中的固定产品。（**支持增加任意产品**）
	3. **抽象工厂模式**
		用来生产不同产品族的全部产品。（**对于增减新产品无能为力，支持增加产品簇**）
5. **代码实现**
	1. **简单工厂模式**
	<br/>![](/img/pattern11.png)
	<br/>实现代码1
	
			public class CarFactory {
			
				public static Car createCar(String type) {
					if ("奥迪".equals(type)) {
						return new Audi();
					} else if ("比亚迪".equals(type)) {
						return new Byd();
					// 增加产品要修改该处代码
					} else {
						return null;
					}
				}
			
			}

		<br/>实现代码2

			public class CarFactory2 {
			
				public static Car createAudi() {
					return new Audi();
				}
			
				public static Car createByd() {
					return new Byd();
				}
				// 增加产品需要在该处增加代码
			
			}
	2. **工厂方法模式**
	<br/><font color=red><b>工厂方法模式避免简单工厂模式不满足完全OCP。简单工厂模式只有一个工厂类，而工厂方法模式有一组实现相同接口的工厂类。</b></font>
	<br/>![](/img/pattern.png)

			public class Byd implements Car {
			
				@Override
				public void run() {
					System.out.println("比亚迪再跑！");
				}
			
			}

		 &emsp;

			public class BydFactory implements CarFactory {
			
				@Override
				public Car createCar() {
					return new Byd();
				}
			
			}
	3. **抽象工厂模式**
	<br/><font color=red><b>不可增加产品，可以增加产品族</b></font>
	<br/>![](/img/pattern.png)

总结：三种工厂模式，分别对应不同的项目形式，我们一般使用简单工厂模式创建对象。

----
### <a name="14">1.4建造者模式</a> ###

1. 特点
	1. **分离对象子组建的单独构造器（由Builder来负责）和装配（由Director负责）。**从而可以构造出复杂的对象。这个模式用于：某个对象构造过程复杂的情况下使用。
	2. **实现了构造和装配的解耦。**不同的构建器，相同的装配，也可以做出不同的对象；相同的构建器，不同的装配顺序也可以做出不同的对象。也就是实现了构建算法，装配算法的解耦，实现了更好的复用。
2. 应用场景<br/>我们需要构建一个复杂的产品，装配这个产品需要很多步骤。<font color=red>**这种构造模式，适用于对象的子件很多，构造过程很复杂的对象。**</font>
	1. SpringBuilder类的append方法
	2. SQL中的PreparedStatement
	3. JDOM中的，DomBuilder，SAXBuilder
3. 实现形式
<br/>![](/img/pattern14.png)

总结：一种设计模式往往解决一类问题。熟练掌握、运用设计模式的的方法就是，了解设计模式应用的场景，适用解决那些问题。

-----

### <a name="1.5">1.5原型模式(prototype,克隆模式)</a> ###

1. **特点**<br/>短时间大量创建对象时，原型模式比普通的new方式获得对象要快
2. **应用场景**<br/>
	<font color=red>原型模式很少单独出现，一般是和工厂方法一起出现，通过clone的方法创建一个对象，然后由工厂方法提供给调用者。</font>
	<br/>Spring中的Bean的创建实际上就两种：单例模式和原型模式。（当然，原型模式需要和工厂模式搭配起来）
3. **实现形式**
	1. Cloneable接口和clone方法
	2. 对象的序列化和反序列化
4. 对象复制成员变量的程度
	1. **浅克隆**
		<br/>克隆得到对象的所有变量都含有原来对象相同的值，所有变量的引用仍然指向原来的对象的变量。<font color=red>只复制了引用，没有复制堆中的数据。</font>
	2. **深克隆**
	    <br/>克隆得到对象的所有变量都不一定含有原来对象相同的值，所有变量的引用仍然指向原来的对象的变量。<font color=red>创建了新引用，重新创建了堆中的数据。</font>
5. 代码实现

	1. 浅克隆<br/>
	Sheep.java

			public class Sheep implements Cloneable{
				private String sname;
				private Date birthday;
				// 实现Cloneable接口，才能重写Object类的clone方法
				protected Object clone() throws CloneNotSupportedException {
					// 有多态，父类引用指向子类对象，调用重写方法
					Object obj = super.clone();  //直接调用object对象的clone()方法！
					return obj;
				}
			}
	client.java&emsp;
	
			public class Client {
				public static void main(String[] args) throws Exception {
					Date date = new Date(12312321331L);
					Sheep s1 = new Sheep("少利",date);
					Sheep s2 = (Sheep) s1.clone();
				}
			}

	1. 深克隆<br/>Sheep.java

			public class Sheep implements Cloneable{
				private String sname;
				private Date birthday;
				// 实现Cloneable接口，才能重写Object类的clone方法
				protected Object clone() throws CloneNotSupportedException {
					Object obj = super.clone(); 
					Sheep2 s = (Sheep2) obj;
					// 把属性也进行克隆！
					s.birthday = (Date) this.birthday.clone(); 
					return obj;
				}
			}
	1.	序列化和反序列化<br/>Sheep.java

			// 类必须实现Serializable接口才能被序列化！
			public class Sheep implements Serializable{
				private String sname;
				private Date birthday;
			}
client.java

			public class Client3 {
				// 使用序列化和反序列化实现深复制
				public static void main(String[] args) throws Exception {
					Date date = new Date(12312321331L);
					Sheep s1 = new Sheep("多利", date);
					// 通过ObjectOutputStream从jvm中得到被序列化的对象
					ByteArrayOutputStream bos = new ByteArrayOutputStream();
					ObjectOutputStream oos = new ObjectOutputStream(bos);
					oos.writeObject(s1);
					byte[] bytes = bos.toByteArray();
					// 通过得到的序列化的对象的字节数数组构建对象
					ByteArrayInputStream bis = new ByteArrayInputStream(bytes);
					ObjectInputStream ois = new ObjectInputStream(bis);
					Sheep s2 = (Sheep) ois.readObject();
				}
			}

总结： clone和反序列化提供了除了new之外的构建对象的方法。clone是通过底层C++实现的，可以直接把内存中的数据做一个复制，效率较高。在创建构造比较繁琐的类的对象时，可以使用clone和反序列化。