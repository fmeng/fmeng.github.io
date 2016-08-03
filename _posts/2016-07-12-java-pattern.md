---
layout: post
title: "Java 23种设计模式"
categories:
- java
---

学习视频和代码：<br/>&emsp;&emsp;&emsp;链接：[http://pan.baidu.com/s/1dF4sFwX](http://pan.baidu.com/s/1dF4sFwX) &emsp;密码：3dxr<br/>

1. <a href="#1">**创建型模式**</a>
	1. <a href="#11">单例模式</a>
	2. <a href="#12">工厂模式</a>
	4. <a href="#13">建造者模式</a>
	5. <a href="#14">原型模式</a>
2. <a href="#2">**构造型模式**</a>
	1. <a href="#21">适配器模式</a>
	2. <a href="#22">代理模式</a>
	2. <a href="#23">桥接模式</a>
	3. <a href="#24">组合模式</a>
	4. <a href="#25">装饰模式</a>
	5. <a href="#26">外观模式</a>
	6. <a href="#27">享元模式</a>
3. **行为型模式**
	1. <a href="#31">职责链模式</a>
	2. <a href="#32">迭代器模式</a>
	3. <a href="#33">中介者模式</a>
	4. <a href="#34">观察者模式</a>
	5. <a href="#35">中介者模式</a>
	6. <a href="#36">备忘录模式</a>
	7. <a href="#37">解析器模式</a>
	8. <a href="#38">状态模式</a>
	9. <a href="#39">策略模式</a>
	10. <a href="#310">职责链模式</a>
	11. <a href="#311">模版方法模式</a>
	
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

### <a name="11">1.1单例模式</a> ###

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
	<br/>![](/img/pattern12.png)

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
	<br/>![](/img/pattern13.png)

总结：三种工厂模式，分别对应不同的项目形式，我们一般使用简单工厂模式创建对象。

----
### <a name="13">1.3建造者模式</a> ###

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

### <a name="14">1.4原型模式(prototype,克隆模式)</a> ###

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

----
## <a name="2">2.构造型模式</a> ##

1. <a href="#21">适配器模式</a><br/>是原本由于接口不兼容不能在一起工作的类可以一起工作
2. <a href="#22">代理模式</a><br/>为真实对象提供一个代理，从而控制对真实对象的访问
2. <a href="#23">桥接模式</a><br/>处理多层继承结构，处理多纬度变化的场景，将各个纬度设计成独立的继承结构，使各个纬度可以独立的扩展在抽象层建立关联
3. <a href="#24">组合模式</a><br/>将对象组合成树状结构以表示部分和整体的层次结构，使得客户可以统一的调用子叶对象和容器对象
4. <a href="#25">装饰模式</a><br/>动态地为一个对象添加额外的功能，比继承灵活
5. <a href="#26">外观模式</a><br/>为子系统提供统一的调用接口，使得子系统更加容易使用
6. <a href="#27">享元模式</a><br/>运用共享技术有效的实现管理大量细粒度对象，节省内存，提高效率

### <a name="21">2.1适配器模式</a> ###

1. **特点** 
	<br/> 
	**目的：将一个类的接口转换成客户希望的另外一个接口。** Adapter模式使得原本由于接口不兼容而不能在一起工作的那些类可以在一起工作了。
<br/><font color=red>图很重要！**适配器模式在程序上的实现是，一个适配两个对象Adapter类**</font>
<br/>![](/img/pattern15.png)<br/>
3. **模式中的角色**
	1. **目标接口（Target）**：客户所期待的接口。目标可以是具体的或抽象的类，也可以是接口
	2. **需要适配的类（Adaptee）**：需要适配的类或适配者类
	3. <font color=red>**适配器（Adapter）**</font>：通过包装一个需要适配的对象，把原接口转换成目标接口
2. **应用场景**
	1. 旧系统的升级、改造
	2. java.io.InputStreamReader(InputStream)
	3. java.io.OutputStreamReader(OutputStream)
5. **代码实现**
<br/>Adapter.java<font color=red>（通过继承`Adaptee`类的方式，实现和`Adaptee`的连接）</font>

		public class Adapter extends Adaptee implements Target {
			
			@Override
			public void handleReq() {
				super.request();
			}
		}
<br/>Adaper2.java<font color=red>（通过持有`Adaptee`类构建对象引用的方式，实现和`Adaptee`的连接）</font>

		public class Adapter2 implements Target {
		
			private Adaptee adaptee;
		
			@Override
			public void handleReq() {
				adaptee.request();
			}
		
			public Adapter2(Adaptee adaptee) {
				super();
				this.adaptee = adaptee;
			}
		
		}
client.java

		public static void main(String[] args) {
			Client  c = new Client();
			// 构建要适配的对象
			Adaptee a = new Adaptee();		
			// Target t = new Adapter();
			// 1. 要构建的对象放到适配器中 2. 使目标接口持有构建好的适配器对象的引用
			Target t = new Adapter2(a);
			t.handleReq();		
		}



总结：**接口就是一种标准。**要制作一种适配器连接两个类或接口，继承接口、实现类就可以了。也可以在适配器中持有类构建对象的引用。

-----
### <a name="22">2.2代理模式</a> ###

1. **特点**
	<br/><font color=red>通过代理，控制对对象的访问！可以详细控制访问<font color=green>某个（某类）</font>对象的方法，在调用这个方法前做前置处理，调用这个方法后做后置处理。（既：AOP的微观实现！）</font>
	<br/>AOP(Aspect Oriented Programming，面向切面的编程)的核心实现机制！
<br/>![](/img/pattern15.png)<br/>
2. **模式中的角色**
	1. **抽象角色**（抽象出来的歌手）<br/>定义
	2. 
	3. 
	4. 角色和真实角色的公共对外方法
	2. **真实角色**（周杰伦）
		1. **关注真正的业务逻辑**
		2. 实现抽象角色，定义真实角色所要实现的业务逻辑，共代理角色调用
	3. **代理角色**（经纪人）
		1. **将统一的流程控制放到代理角色中处理**
		2. 实现抽象角色，是真实角色的代理，通过真实角色的业务逻辑方法来实现抽象方法，并可以附加自己的操作
3. **应用场景**
	1. 安全代理：屏蔽对真实角色的直接访问
	2. 远程代理：通过代理类处理远程方法调用（RMI）
	3. 延迟加载：先加载轻量级的代理对象，真正需要时再加载真实对象
4. **分类**
	1. **静态代理**（静态定义代理类，自己定义）
	2. **动态代理**（动态生成代理类，系统提供接口）
		1. **JDK自带的动态代理**
		2. javaassist字节码操作库实现
		3. CGIB
		4. ASM（底层使用指令，可维护性差）
5. **代码实现**
	1. **静态代理**
		<font color=red><br/>ProxyStar持有RealStar的引用，实现两个类之间的联系 </font>
<br/>Star.java（接口）
	
			// 真实角色和代理角色都要实现这个接口
			public interface Star {
				// 面谈
				void confer();
				// 签合同
				void signContract();
				// 订票
				void bookTicket();
				// 唱歌
				void sing();
				// 收钱
				void collectMoney();
			}
RealStar.java(真实角色)

			//代理角色和真实角色实现相同的接口
			public class RealStar implements Star {
				@Override
				public void bookTicket() {
					System.out.println("RealStar.bookTicket()");
				}
				@Override
				public void collectMoney() {
					System.out.println("RealStar.collectMoney()");
				}
				@Override
				public void confer() {
					System.out.println("RealStar.confer()");
				}
				@Override
				public void signContract() {
					System.out.println("RealStar.signContract()");
				}
				@Override
				public void sing() {
					System.out.println("RealStar(周杰伦本人).sing()");
				}	
			}
ProxyStar.java（代理角色）

			// 代理角色和真实角色实现相同的接口
			public class ProxyStar implements Star {
				// 持有真实角色的引用
				private Star star;
				public ProxyStar(Star star) {
					super();
					this.star = star;
				}
				@Override
				public void bookTicket() {
					System.out.println("ProxyStar.bookTicket()");
				}
				@Override
				public void collectMoney() {
					System.out.println("ProxyStar.collectMoney()");
				}
				@Override
				public void confer() {
					System.out.println("ProxyStar.confer()");
				}
				@Override
				public void signContract() {
					System.out.println("ProxyStar.signContract()");
				}
				@Override
				public void sing() {
					star.sing();
				}
			}
Client.java

			public class Client {
				public static void main(String[] args) {
					Star real = new RealStar();
					Star proxy = new ProxyStar(real);
					// 由代理角色实现 （经济人）
					proxy.confer();
					proxy.signContract();
					proxy.bookTicket();
					proxy.collectMoney();
					// 由真实角色实现（周杰伦）
					proxy.sing();
					
				}
			}

	2. **动态代理**
		<font color=red><br/>使用`class StarHandler implements InvocationHandler`实现`RealStar`和`ProxyStar`之间的联系。使用JVM自带的`Proxy.newProxyInstance()`构建一个代理对象（要使用 StarHandler）</font>
<br/><br/>Star.java（接口）
	
			// 真实角色和代理角色都要实现这个接口
			public interface Star {
				// 面谈
				void confer();
				// 签合同
				void signContract();
				// 订票
				void bookTicket();
				// 唱歌
				void sing();
				// 收钱
				void collectMoney();
			}

          RealStar.java(真实角色)

			//代理角色和真实角色实现相同的接口
			public class RealStar implements Star {
				@Override
				public void bookTicket() {
					System.out.println("RealStar.bookTicket()");
				}
				@Override
				public void collectMoney() {
					System.out.println("RealStar.collectMoney()");
				}
				@Override
				public void confer() {
					System.out.println("RealStar.confer()");
				}
				@Override
				public void signContract() {
					System.out.println("RealStar.signContract()");
				}
				@Override
				public void sing() {
					System.out.println("RealStar(周杰伦本人).sing()");
				}	
			}
<br/>StarHandler.java

			public class StarHandler implements InvocationHandler {
				private Star realStar;
				// 把代理要连接的RealStart放进来
				public StarHandler(Star realStar) {
					super();
					this.realStar = realStar;
				}
				/*
				 * InvocationHandler接口中invoke方法的功能
				 * 1. 调用proxy的所有方法
				 * 2. proxy的所有方法在调用的时候会执行 invoke方法
				 * 3. 在invoke调用过程做修改，使其调用RealStart的方法
				 */
				@Override
				public Object invoke(Object proxy, Method method, Object[] args)
						throws Throwable {
					Object object = null;
					if(method.getName().equals("sing")){
						// 调用RealStart的sing方法，不调用proxy的sing方法
						// 如果调用的方法有返回值，返回Object
						object = method.invoke(realStar, args);
					}
					return object;
				}
			}
Client.java

			public static void main(String[] args) {
				Star realStar = new RealStar();
				// handler用来建立真实对象和代理对象之间的联系
				StarHandler handler = new StarHandler(realStar);
				// 通过系统自带的Proxy获得一个代理对象
				Star proxy = (Star) Proxy.newProxyInstance(ClassLoader.getSystemClassLoader(), 
						new Class[]{Star.class}, handler);
				// 这里调用的是真实角色的sing方法
				proxy.sing();		
			}

总结：**代理。真实对象和代理对象要实现同一接口，代理对象要和真实对象建立联系（静态代理，通过proxy持有真实对象的引用；动态代理，通过实现InvocationHandler接口的类）。**

静态代理。需要自己构建代理对象，只有真实对象的引用。这样就能通过代理对象隐藏真是对象的很多功能细节，把真实对象需要开放的功能提供给代理以供调用者使用。

动态代理。使用`class StarHandler implements InvocationHandler`实现`RealStar`和`ProxyStar`之间的联系。使用JVM自带的`Proxy.newProxyInstance()`构建一个代理对象（要使用到 StarHandler 构建Proxy对象）。Proxy内部通过反射提供了调用代理方法的入口，这样我们就可以把修改原来Proxy内方法执行的内容。

------
### <a name="23">2.3桥接模式</a> ###
1. **特点**
	1. **桥接模式可以代替多层继承方案**。多层继承违背了单一继承原则，服用性较差，类的个数页非常多。桥接模式可以极大的减少子类的个数，从而降低管理和维护的成本。
	2. 桥接模式极大的提高了系统的可扩展性，在两个变化纬度中任意扩展一个纬度，都不需要修改原有的系统，**符合开闭原则**。
	3. 桥接模式适用于。**处理多层继承结构，处理多维度变化的场景，将各个纬度设计成独立的继承结构**，使各个纬度可以和独立的扩展在抽象层建立关联。
2. **案例分析**
<br/>继承模式下的类结构模式，太繁琐
<br/>![](/img/pattern21.jpg)

2. **应用场景**
	1. JDBC驱动程序
	2. 银行日志管理（多纬度）
		1. 格式分类：操作日志、交易日志、异常日志
		2. 距离分类：本地记录日志、异地记录日志
	3. 人力资源系统中的奖金计算模块（多纬度）
		1. 奖金分类：个人奖金、团体奖金、激励奖金
		2. 部门分类：人事部门、销售福门、研发部门
3. **实现形式**（<font color=red>**一个主要实现的类，持有不同纬度接口的引用**</font>）
<br/>![](/img/pattern22.png)
5. **代码实现**
<br/>Computer2.java

		public class Computer2 {
			protected Brand brand;
			public Computer2(Brand b) {
				this.brand = b;
			}	
			public void sale(){
				brand.sale();
			}	
		}
		
		class Desktop2 extends Computer2 {
		
			public Desktop2(Brand b) {
				super(b);
			}
			
			@Override
			public void sale() {
				super.sale();
				System.out.println("销售台式机");
			}
		}
		
		class Laptop2 extends Computer2 {
			
			public Laptop2(Brand b) {
				super(b);
			}
			
			@Override
			public void sale() {
				super.sale();
				System.out.println("销售笔记本");
			}
		}
Brand.java

		public interface Brand {
			void sale();
		}
		
		class Lenovo implements Brand {
			@Override
			public void sale() {
				System.out.println("销售联想电脑");
			}	
		}
		
		class Dell implements Brand {
			@Override
			public void sale() {
				System.out.println("销售Dell电脑");
			}	
		}
		
		class Shenzhou implements Brand {	
			@Override
			public void sale() {
				System.out.println("销售神舟电脑");
			}	
		}

总结：重要的理解，在多维度变化的场景下，使用桥接模式可以代替多层继承方案。**实现形式是重点！**

----


### <a name="24">2.4组合模式</a> ###

1. **特点**
	1. <font color=red>组合模式和类的组合不同</font>
	2. <font color=red>组合模式适用于“树”型结构</font>
3. **组织结构**
    1. 抽象构件角色（component），定义了子叶和容器的共同特点
	2. 子叶构件角色（leaf），无子节点
	3. 容器构件角色（composite），有容器特征，可以包含子节点<br/>
	
	![](/img/pattern241.png)
2. **应用场景**
	1. 操作系统中的资源管理器
	2. GUI中的容器层次图
	3. XML文件解析
	4. OA系统中，组织结构的处理
	5. Junit单元测试框架
		> 底层设计就是典型的组合模式，TestCase（子叶）， TestUnit（容器），Test接口（抽象） 
3. **流程分析**
	1. 组合模式为了处理**树型结构**提供了完美的解决方案，描述了如何将容器和子叶进行递归组合，使得用户在使用时可以**一致性的对待容器和子叶**。
	2. 当容器对象的指定方法被调用时，将遍历整个树型结构，寻找也包含这个方法的成员，并调用执行。其中，使用递归调用的机制对真个结构进行处理。
5. **代码实现**<br/>
AbstractFile.java（各个组建）

		//抽象构建
		public interface AbstractFile {
			void killVirus(); // 杀毒
		}
		
		class ImageFile implements AbstractFile {
			private String name;
		
			public ImageFile(String name) {
				super();
				this.name = name;
			}
		
			@Override
			public void killVirus() {
				System.out.println("---图像文件：" + name + ",进行查杀！");
			}
		
		}
		
		class TextFile implements AbstractFile {
			private String name;
		
			public TextFile(String name) {
				super();
				this.name = name;
			}
		
			@Override
			public void killVirus() {
				System.out.println("---文本文件：" + name + ",进行查杀！");
			}
		}
		
		class VideoFile implements AbstractFile {
			private String name;
		
			public VideoFile(String name) {
				super();
				this.name = name;
			}
		
			@Override
			public void killVirus() {
				System.out.println("---视频文件：" + name + ",进行查杀！");
			}
		}
		
		class Folder implements AbstractFile {
			private String name;
			// 定义容器，用来存放本容器构建下的子节点
			private List<AbstractFile> list = new ArrayList<AbstractFile>();
		
			public Folder(String name) {
				super();
				this.name = name;
			}
		
			public void add(AbstractFile file) {
				list.add(file);
			}
		
			public void remove(AbstractFile file) {
				list.remove(file);
			}
		
			public AbstractFile getChild(int index) {
				return list.get(index);
			}
		
			@Override
			public void killVirus() {
				System.out.println("---文件夹：" + name + ",进行查杀");
		
				for (AbstractFile file : list) {
					file.killVirus();
				}
		
			}
		
		}

	client.java


		public static void main(String[] args) {
			// 子叶构件
			AbstractFile f2,f3,f4,f5;
			// 容器构件
			Folder f1 = new Folder("我的收藏");
			f2 = new ImageFile("老高的大头像.jpg");
			f3 = new TextFile("Hello.txt");
			f1.add(f2);
			f1.add(f3);	
			Folder f11 = new Folder("电影");
			f4 = new VideoFile("笑傲江湖.avi");
			f5 = new VideoFile("神雕侠侣.avi");
			f11.add(f4);
			f11.add(f5);
			f1.add(f11);
			f2.killVirus();	
			f1.killVirus();
		}

**总结：组合结构和类的组合不同，组合结构适用于树形结构。**


-----
### <a name="25">2.5装饰模式</a> ###

1. **特点**<font color=red>
	1. **动态**的为一个对象**增加新功能**
	2. 装饰模式是一种用于代替继承的技术，无须通过继承增加子类就能扩展对象的新功能。使用对象的关联关系代替继承关系，避免了类型体系的快速膨胀。</font>
2. **应用场景**
	2. Swing包中图形界面构件的功能
	3. Servlet API中提供了一个request对象的Decorater设计模式的默认实现类HttpServletRequestWrapper，HttpServletRequestWrapper类增强了request的功能。
	4. Structs2中，request，response，session对象的处理
	1. IO的输入输出流的设计 
		1. 抽象构件角色 Componet<br/>`inputStream`、`outputStream`、`Reader`、`Writer`
		2. 具体构件角色 Concrete Componet<br/>`FileInputStram`、`FileOutputStream`
		3. 装饰角色 Decorator<br/>`FilterInputStream`、`FilterOutputStream`
		4. 具体装饰角色 ConcreteDecorator<br/>`BufferedInputStream`、`BufferedOutputStream`
3. **实现形式**
	1. 抽象构件角色 Componet<br/>真实对象和装饰对象有相同的接口。这样，客户端对象就能够以与真实对象相同的方式同装饰对象进行交货
	2. 具体构件角色 Concrete Componet（真实对象）<br/>IO流中的FileInputStream、FileOutputStream
	3. 装饰角色 Decorator<br/>持有一个抽象构件的引用。装饰对象接收所有客户端的请求，并把这些请求转发给真实对象。这样，就能在真实对象调用前后增加新功能。
	4. 具体装饰角色 ConcreteDecorator<br/>真实对象装饰后的角色<br/>
	![](/img/pattern251.png)
4. <font color=red>**重点**</font>
	4. **总结**
		1. 装饰模式（Decorator）也叫包装模式（**Wrapper**）
		2. 装饰模式降低了系统的耦合度，可以动态的添加删除对象的职责，并使需要构件的具体构件类和具体装饰类可以独立变化，以便增加新的具体构件类和具体装饰类
	1. **优点**
		1. 扩展对象功能，比继承灵活，不会导致类的个数急剧增加
		2. 可以对一个对象进行多次装饰，创造出不同行为的组合，得到功能更加强大的对象
		3. 具体构建类和具体装饰类可以独立变化，用户可以根据自己的需要，自己增加新的具体构件子类和具体装饰类
	2. **缺点**
		1. 产生很对小对象。大量小对象占据内存，一定程度上影响性能
		2. 装饰模式易于出错，调试排查比较麻烦
	3. **装饰模式和桥接模式的区别**
		<br/>两个模式都是为了解决对子类对象问题，<font color=red>**但是他们的诱因不一样**。桥接模式是对象自身现有机制沿多个纬度变化，是既有部分不稳定。装饰模式是为了增加新的功能。</font> 
	
5. **代码实现**

		public interface ICar {
			void move();
		}
		
		//ConcreteComponent 具体构件角色(真实对象)
		class Car implements ICar {
			@Override
			public void move() {
				System.out.println("陆地上跑！");
			}
		}
		
		//Decorator装饰角色
		class SuperCar implements ICar {
			protected ICar car;
			public SuperCar(ICar car) {
				super();
				this.car = car;
			}
		
			@Override
			public void move() {
				car.move();
			}
		}
		
		//ConcreteDecorator具体装饰角色
		class FlyCar extends SuperCar {
		
			public FlyCar(ICar car) {
				super(car);
			}
			
			public void fly(){
				System.out.println("天上飞！");
			}
		
			@Override
			public void move() {
				super.move();
				fly();
			}
			
		}
		
		//ConcreteDecorator具体装饰角色
		class WaterCar extends SuperCar {
			
			public WaterCar(ICar car) {
				super(car);
			}
			
			public void swim(){
				System.out.println("水上游！");
			}
			
			@Override
			public void move() {
				super.move();
				swim();
			}
			
		}
		
		//ConcreteDecorator具体装饰角色
		class AICar extends SuperCar {
			
			public AICar(ICar car) {
				super(car);
			}
			
			public void autoMove(){
				System.out.println("自动跑！");
			}
			
			@Override
			public void move() {
				super.move();
				autoMove();
			}
			
		}
client.java

		public class Client {
			public static void main(String[] args) {
				Car car  = new Car();
				car.move();
				
				System.out.println("增加新的功能，飞行----------");
				FlyCar flycar = new FlyCar(car);
				flycar.move();
				
				System.out.println("增加新的功能，水里游---------");
				WaterCar  waterCar = new WaterCar(car);
				waterCar.move();
				
				System.out.println("增加两个新的功能，飞行，水里游-------");
				WaterCar waterCar2 = new WaterCar(new FlyCar(car));
				waterCar2.move();
				
			}
		}

总结：装饰器模式**动态**的为一个对象**增加新功能**，区别与桥接模式在指定的纬度为对象增加新功能。

-----

### <a name="26">2.6外观模式</a> ###

1. **特点**
	1. 迪米特法则（最少知识原则）<br/>一个软件实体应当尽可能少的与其他实体发生相互作用。
	2. 为一个子系统提供统一的入口。封装子系统的复杂性，便于客户端调用。
2. **应用场景**
	1. 使用频率高。那里都会用到。各种技术和框架中，都有外观模式的使用。如：<br/>JDBC封装后的，Commons提供的DBUtils类，Hibernate提供的工具类	，SpringJDBC工具类等。
5. **实现**
<br/>![](/img/pattern261.png)

总结：**把复杂的业务逻辑隐藏起来，只对外提供一个服务的入口。**封装子系统的复杂性，便于客户端调用。

----
### <a name="27">2.7享元模式</a> ###
1. **特点**
	1. 场景
		<br/>如果有很多个完全相同或者相似的对象，我们可以**通过享元模式来节省内存**。
	2. 核心
		1. <font color=red>享元模式以共享的方式高效的支持大量细粒度对象的重用</font>
		2. <font color=red>享元模式能做到共享的关键是区分了内部状态和外部状态</font>
	1. 优点
		1. 极大的减少了内存中对象的数量
		2. 相同或相似对象内存中只存一份，极大的节约了资源，提高了系统的性能
		3. 外部状态相对独立，不影响内部状态
	4. 缺点
		1. 模式较复杂，使程序逻辑复杂化
		2. 为了节省内存，共享了内部状态，分离出外部状态，而读取外部状态使运行时间变长。用时间换空间。
2. **应用场景**
	1. 享元模式由于其共享的特性，可以在任何池中操作，比如：线程池、数据库连接池
	2. Spring类的设计也是享元模式
3. **实现形式**
	1. FlyweightFactory 享元工厂类<br/>创建并管理享元对象，享元池一般设计成键值对
	2. Flyweight<br/>通常是一个接口或抽象类，声明公共方法，这些方法可以向外界提供对象的内部状态，设置外部状态。
	3. concreteFlyWeight具体享元类<br/> 为内部状态提供成员变量进行存储
	4. UnshareConcreteFlyWeight非共享享元类<br/>不能被共享的子类设计成非共享享元类<br/>
![](/img/pattern271.png)
5. **代码实现**
<br/>Coordinate.java（外部状态`UnSharedConcreteFlyWeight`）

		// 外部状态UnSharedConcreteFlyWeight
		public class Coordinate {
			private int x,y;
		
			public Coordinate(int x, int y) {
				super();
				this.x = x;
				this.y = y;
			}
		
			public int getX() {
				return x;
			}
		
			public void setX(int x) {
				this.x = x;
			}
		
			public int getY() {
				return y;
			}
		
			public void setY(int y) {
				this.y = y;
			}
			
		}

	ChessFlyWeight.java

		// 享元类
		public interface ChessFlyWeight {
			void setColor(String c);
			String getColor();
			void display(Coordinate c);
		}
		
		
		class ConcreteChess implements ChessFlyWeight {
		
			private String color;
			
			public ConcreteChess(String color) {
				super();
				this.color = color;
			}
		
			@Override
			public void display(Coordinate c) {
				System.out.println("棋子颜色："+color);
				System.out.println("棋子位置："+c.getX()+"----"+c.getY());
			}
		
			@Override
			public String getColor() {
				return color;
			}
		
			@Override
			public void setColor(String c) {
				this.color = c;
			}
			
		}
ChessFlyWeightFactory.java

		// 享元工厂
		public class ChessFlyWeightFactory {
			//享元池
			private static Map<String,ChessFlyWeight> map = new HashMap<String, ChessFlyWeight>();
			
			public static ChessFlyWeight  getChess(String color){
				
				if(map.get(color)!=null){
					return map.get(color);
				}else{
					ChessFlyWeight cfw = new ConcreteChess(color);
					map.put(color, cfw);
					return cfw;
				}
			}
		}

总结：<font color=red>享元模式以共享的方式高效的支持大量细粒度对象的重用；享元模式能做到共享的关键是区分了内部状态和外部状态；</font>享元模式的实现过程比较复杂，应该主要学习其应用场景，使用的时候在过来学习。

-----
### <a name="31">3.1职责链模式</a> ###

1. **定义**<br/><font color=red>将能够处理同一类请求的对象连城一条链，所提交的请求沿着链传递，链上的对象逐个判断是否有能力处理该请求，如果能处理则处理，如果不能则传递给链上的下一个对象。</font>
2. **应用场景**
 	1. java中异常机制就是一种责任链模式。一个try可以对应多个catch，当一个catch不匹配类型，则自动调到下一个catch。
	2. Servlet开发中，过滤器的链式处理
	3. Structs2中拦截器的调用也是典型的责任链模式
4. **UML关系图解**<br/>![](/img/pattern311.png)
5. **代码实现**
<br/>Leader.java **抽象类**

		public abstract class Leader {
			protected String name;
			protected Leader nextLeader; //责任链上的后继对象
			
			public Leader(String name) {
				super();
				this.name = name;
			}
			//设定责任链上的后继对象
			public void setNextLeader(Leader nextLeader) {
				this.nextLeader = nextLeader;
			}
			public abstract void handleRequest(LeaveRequest request);
		}

	LeaveRequest.java **请假条**

		public class LeaveRequest {
			private String empName;
			private int leaveDays;
			private String reason;
			
			public LeaveRequest(String empName, int leaveDays, String reason) {
				super();
				this.empName = empName;
				this.leaveDays = leaveDays;
				this.reason = reason;
			}
			public String getEmpName() {
				return empName;
			}
			public void setEmpName(String empName) {
				this.empName = empName;
			}
			public int getLeaveDays() {
				return leaveDays;
			}
			public void setLeaveDays(int leaveDays) {
				this.leaveDays = leaveDays;
			}
			public String getReason() {
				return reason;
			}
			public void setReason(String reason) {
				this.reason = reason;
			} 
		}
Director.java 主任

		public class Director extends Leader {
		
			public Director(String name) {
				super(name);
			}
		
			@Override
			public void handleRequest(LeaveRequest request) {
				if(request.getLeaveDays()<3){
					System.out.println("员工："+request.getEmpName()+"请假，天数："+request.getLeaveDays()+",理由："+request.getReason());
					System.out.println("主任："+this.name+",审批通过！");
				}else{
					if(this.nextLeader!=null){
						this.nextLeader.handleRequest(request);
					}
				}
			}
		
		}
	Manager.java 经理

		public class Manager extends Leader {
		
			public Manager(String name) {
				super(name);
			}
		
			@Override
			public void handleRequest(LeaveRequest request) {
				if(request.getLeaveDays()<10){
					System.out.println("员工："+request.getEmpName()+"请假，天数："+request.getLeaveDays()+",理由："+request.getReason());
					System.out.println("经理："+this.name+",审批通过！");
				}else{
					if(this.nextLeader!=null){
						this.nextLeader.handleRequest(request);
					}
				}
			}
		
		}
	GeneralManager.java  总经理
		
		public class GeneralManager extends Leader {
		
			public GeneralManager(String name) {
				super(name);
			}
		
			@Override
			public void handleRequest(LeaveRequest request) {
				if(request.getLeaveDays()<30){
					System.out.println("员工："+request.getEmpName()+"请假，天数："+request.getLeaveDays()+",理由："+request.getReason());
					System.out.println("总经理："+this.name+",审批通过！");
				}else{
					System.out.println("莫非"+request.getEmpName()+"想辞职，居然请假"+request.getLeaveDays()+"天！");
				}
			}
		
		}

	Client.java

		public class Client {
			public static void main(String[] args) {
				Leader a = new Director("张三");
				Leader b = new Manager("李四");
				Leader c = new GeneralManager("王五");
				//组织责任链对象的关系
				a.setNextLeader(b);
				b.setNextLeader(c);
				
				//开始请假操作
				LeaveRequest req1 = new LeaveRequest("TOM", 15, "回英国老家探亲！");
				a.handleRequest(req1);
				
			}
		}

总结：<font color=red>职责链模式将能够处理同一类请求的对象连城一条链，所提交的请求沿着链传递。这样把原先可以通过`if else`实现的功能抽离出来，减少了类之间的耦合。</font>

------
### <a name="32">3.2迭代器模式</a> ###

1. **场景**
	1. 提供了一种可以遍历聚合对象的方式。又称为：游标cursor模式
	2. 聚合对象：存储数据
	3. 迭代器：遍历数据
2. **应用场景**
	1. JDK内置的迭代器（List/Set）
3. **UML结构图**<br/>![](/img/pattern321.jpg)

5. **代码实现**（已经简化类图结构）<br/>
	MyIterator.java
	
		// 自定义的迭代器接口
		public interface MyIterator {
			void first();	//将游标指向第一个元素
			void next();	//将游标指向下一个元素
			boolean hasNext();//判断是否存在下一个元素
			
			boolean isFirst();
			boolean isLast();
			
			Object getCurrentObj();  //获取当前游标指向的对象
		}
	ConcreteMyAggregate.java

		// 自定义的聚合类
		public class ConcreteMyAggregate {
			private List<Object> list = new ArrayList<Object>();
		
			public void addObject(Object obj){
				this.list.add(obj);
			}
			public void removeObject(Object obj){
				this.list.remove(obj);
			}
		
			public List<Object> getList() {
				return list;
			}
		
			public void setList(List<Object> list) {
				this.list = list;
			}
			
			//获得迭代器
			public MyIterator  createIterator(){
				return new ConcreteIterator();
			}
			
			//使用内部类定义迭代器，可以直接使用外部类的属性
			private class ConcreteIterator implements MyIterator {
		
				private int cursor;  //定义游标用于记录遍历时的位置
				
				@Override
				public void first() {
					cursor = 0;
				}
		
				@Override
				public Object getCurrentObj() {
					return list.get(cursor);
				}
		
				@Override
				public boolean hasNext() {
					if(cursor<list.size()){
						return true;
					}
					return false;
				}
		
				@Override
				public boolean isFirst() {
					return cursor==0?true:false;
				}
		
				@Override
				public boolean isLast() {
					return cursor==(list.size()-1)?true:false;
				}
		
				@Override
				public void next() {
					if(cursor<list.size()){
						cursor++;
					}
				}
				
			}	
			
		}
Client.java

		public static void main(String[] args) {
				ConcreteMyAggregate cma = new ConcreteMyAggregate();
				cma.addObject("aa");
				cma.addObject("bb");
				cma.addObject("cc");
				
				MyIterator iter = cma.createIterator();
				while(iter.hasNext()){
					System.out.println(iter.getCurrentObj());
					iter.next();
				}
				
			}
		}

总结：**迭代器模式提供了一种跟便捷的遍历聚合对象的方式。**


------

### <a name="33">3.3中介者模式</a> ###
1. **特点**
	1. 如果一个系统中对象之间的联系呈现为网状结构，对象之间存在大量的多对多关系，将导致关系及其复杂，这些对象称为“**同事对象**”
	2. 我们可以引入一个“**中介者对象**”，使各个同事对象只跟中介者对象打交道，将复杂的网络结构化解为星状结构
3. **本质**<br/>解耦多个同事对象之间的交互关系。每个对象都持有中介者对象的引用，只跟中介者对象打交道。我们通过中介者对象统一管理这些交互关系。
2. **应用场景**
	1. MVC模式（其中C，控制器就是一个中介者对象。M和V都和它打交道）
	2. 窗口游戏程序。窗口软件开发中窗口对象也是一个中介者对象
	3. 图形界面开发GUI中，多个组件之间的交互，可以通过引入一个中介者对象来解决，可以是整体的窗口对象或者DOM对象
	4. `Java.lang.reflect.Method#invoke()`
3. **实现形式**<br />![](/img/pattern331.png)
5. **代码实现**
<br/>Department.java

		//同事类的接口
		public interface Department {
			void selfAction(); //做本部门的事情
			void outAction();  //向总经理发出申请
		}
Finacial.java（财务部）

		public class Finacial implements Department {
		
			private Mediator m;  //持有中介者(总经理)的引用
			
			public Finacial(Mediator m) {
				super();
				this.m = m;
				m.register("finacial", this);
			}
		
			@Override
			public void outAction() {
				System.out.println("汇报工作！没钱了，钱太多了！怎么花?");
			}
		
			@Override
			public void selfAction() {
				System.out.println("数钱！");
			}
		
		}
Market.java（市场部）

		public class Market implements Department {
		
			private Mediator m;  //持有中介者(总经理)的引用
			
			public Market(Mediator m) {
				super();
				this.m = m;
				m.register("market", this);
			}
		
			@Override
			public void outAction() {
				System.out.println("汇报工作！项目承接的进度，需要资金支持！");
				
				m.command("finacial");
				
			}
		
			@Override
			public void selfAction() {
				System.out.println("跑去接项目！");
			}
		
		}
Development.java（研发部）

		public class Development implements Department {
		
			private Mediator m;  //持有中介者(总经理)的引用
			
			public Development(Mediator m) {
				super();
				this.m = m;
				m.register("development", this);
			}
		
			@Override
			public void outAction() {
				System.out.println("汇报工作！没钱了，需要资金支持！");
			}
		
			@Override
			public void selfAction() {
				System.out.println("专心科研，开发项目！");
			}
		
		}
Mediator.java (中介者抽象接口)

		public interface Mediator {
			
			void register(String dname,Department d);
			
			void command(String dname);
			
		}
President.java (中介者，总经理)

		public class President implements Mediator {
			
			private Map<String,Department> map = new HashMap<String , Department>();
			
			@Override
			public void command(String dname) {
				map.get(dname).selfAction();
			}
		
			@Override
			public void register(String dname, Department d) {
				map.put(dname, d);
			}
		
		}
client.java

		public static void main(String[] args) {
			Mediator m = new President();
			
			Market   market = new Market(m);
			Development devp = new Development(m);
			Finacial f = new Finacial(m);
			
			market.selfAction();
			market.outAction();
			
		}

<font color=red>总结：解耦多个同事对象之间的交互关系。每个对象都持有中介者对象的引用，只跟中介者对象打交道。我们通过中介者对象统一管理这些交互关系。</font>


----
