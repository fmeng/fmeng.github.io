---
layout: post
title: "JsonObject --> String(使用的是反射机制)"
categories:
- java
---

#### 需求： ####
****
1. 把下面这张图片要发送的信息封装成对象
2. 通JsonObject转换成字符串
3. 这里text类的名字故意小写的，避免对象转换成Json字符串时，类名不一致


![](/img/custom_message.jpg)

#### 实现： ####

	package com.hellopipi.bean.custom.message;
	
	@SuppressWarnings("unused")
	public class TextMessage {
		//msgtype不用改变，这里就直接给定值了
		private String touser = null;
	    private String msgtype = "text";
	    private text text = null;
	    //设置内部类---->不用外部访问的类设置成内部类
	    private class text{
	    	private String content = null;
			public text(String content){
				this.content = content;
			}
	    	
	    }
	    //构造器私有化————>为了工厂模式
		private TextMessage (){}
		//设置工厂模式的好处，省去了很多set、get方法
		public static TextMessage getTextMessage(String touser, String content ){
			TextMessage message = new TextMessage();
			message.touser = touser;
			TextMessage.text text = message.new text(content);
			message.text = text;
			return message;
		}
	
	}

#### 总结 ####

类很少被其他了引用时，设成内部的。学者使用工厂模式的设计思想写代码。哎，有点为这几行代码折服了。“每一行注释都是有故事的，每一段代码都是有感情的。”fighting......

#### 事实总是事与愿违 ####

1. 因为json-lib这套api是通过反射机制找到方法名->属性名然后在进行转换的。
2. 实际上，是通过JSONUtils中`public static String quote( String string ){}`转换的。
3. 所以是对要转换的对象又要求。

#### 修改后的代码不忍直视了 ####

---
	package com.hellopipi.bean.custom.message;
	
	public class TextMessage {
	
		private String touser = null;
		private String msgtype = "text";
		private text text = null;
	
		/**
		 * 构造犯法要公开
		 * @author fmeng
		 *
		 */
		// 设置内部类
		public class text {
	
			private String content = null;
	
			public text(String content) {
				this.content = content;
			}
		/**
		 * 添加get方法，不用添加set方法，object-->String的时候
		 * @return
		 */
			public String getContent() {
				return content;
			}
	
		}
		/**
		 * 构造方法要公开
		 */
		public TextMessage() {
		}
	
		public static TextMessage getTextMessage(String touser, String content) {
			TextMessage message = new TextMessage();
			message.touser = touser;
			TextMessage.text text = message.new text(content);
			message.text = text;
			return message;
		}
		
		/**
		 * 添加get方法，不用添加set方法，object-->String的时候
		 * @return
		 */
		public String getTouser() {
			return touser;
		}
	
		public String getMsgtype() {
			return msgtype;
		}
	
		public text getText() {
			return text;
		}
	
	}
