---
layout: post
title: "Java Enum 类构建"
categories:
- java
---

[http://yedward.net/?id=398&utm_source=tuicool&utm_medium=referral](http://yedward.net/?id=398&utm_source=tuicool&utm_medium=referral)<br/>
[]()
#### <a href="#1">1.什么枚举，为什么要用到枚举?</a> ####
#### <a href="#2">2.Java使用枚举的语法</a> ####
#### <a href="#3">3.Enum在编码中的主要事项</a> ####

### <a name="1"></a>1.什么是枚举，为什么要用到枚举? ###

1. `public static fianl mounth;`(相当于静态常量)
2. 所要表达的量是可以被列举的，只初始化一次且不可改变
3. java 1.5前用静态常量表示枚举（其被列举的数量是不能控制的！），1.5以后使用枚举类型表示
### <a name="2"></a>2.Java使用枚举的语法 ###
	enum Mounth {
		// 1.相当于，Mounth类继承了enum类。使用继承enum类的Mounth类实例化对象
		Jan(1), Feb(2), Mar(3), Apr(4), May(5), Jun(6), Jul(7), Aug(8), Sep(9), Oct(
				10), Nov(11), Dec(12);
		private int num;
	
		// 2.父类的构造方法被重写，只能使用这个构造方法构建对象，必须传参数！
		// 3.重写的构造函数必须为private，因为enum类中为private，该类继承了enum类
		// 构造器只能私有private，绝对不允许有public构造器,保证枚举类不可在类外实例化
		private Mounth(int num) {
			this.num = num;
		}
	
		public int getNum() {
			return this.num;
		}
	}
	
	public class Demo {
		public static void main(String[] args) {
			// 4.Feb为Mounth的静态常量
			Mounth e = Mounth.Feb;
			int i = e.getNum();
			System.out.println(i);
		}
	}

--------
含有抽象方法的枚举类型：

	public class EnumDemo {
		public static void main(String[] args) {
			// 通过Grade枚举类，得到Grade实例化的枚举对象A
			Grade grade = Grade.A;
			// 调用grade对象实现的抽象函数getLocaeValve();
			String locaeValue = grade.getLocalValue();
			String score = grade.getScore();
			System.out.print("Grade.A:\nlocaeValue:" + locaeValue + "\nscore:"
					+ score);
		}
	
	}
	
	enum Grade {
		/*
		 * 抽象函数的解决的问题：构建的枚举对象A、B、C、D、E要对外提供统一功能的方法。因为优、良、差是和成绩相关的，成绩"100-90"、"89-80"
		 * 等是在构造函数的时候设定的，这时就可以设定成绩的等级了。所以使用抽象函数解决构建的位置对象要实现的方法。
		 */A("100-90") {
			public String getLocalValue() {
				return "优";
			}
		},
		B("89-80") {
			public String getLocalValue() {
				return "良";
			}
		},
		C("79-70") {
			public String getLocalValue() {
				return "一般";
			}
		},
		D("69-60") {
			public String getLocalValue() {
				return "差";
			}
		},
		E("59-0") {
			public String getLocalValue() {
				return "不及格";
			}
		};
		private String score;
	
		private Grade(String score) {
			this.score = score;
		}
	
		// 函数设置成public、abstract。因为需要子类如实现该方法，所以要使用public或者protect！！！
		protected abstract String getLocalValue();
	
		public String getScore() {
			return this.score;
		}
	}
### <a name="3"></a>3.Enum在编码中的主要事项 ###

1. 枚举类的实现意义，应用场景要清晰
2. 枚举类实现接口和枚举类中的抽象方法的区别。（接口在类内具体实现方法，抽象函数在实例化是实现）
3. 始终把实现的枚举类想象成继承了Enum类的类，这样便于思考问题。例如，`protected abstract String getLocalValue();`是`protected`,因为Grade类继承了Enum类，父类的私有类在子类中是不可见的。protected和public在子类中是可见的。
4. 子类继承父类后，重写了父类的构造方法，默认的构造方法`Grade()`无法使用了。