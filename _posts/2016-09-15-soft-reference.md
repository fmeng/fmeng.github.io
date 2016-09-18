---
layout: post
title: "Java 如何有效地避免OOM：善于利用软引用和弱引用"
categories:
- java
---
### 学习资料 ###
1. [http://www.cnblogs.com/dolphin0520/p/3784171.html](http://www.cnblogs.com/dolphin0520/p/3784171.html) 
2. [这个博客质量不怎么样，只看虚引用部分就行了](http://droidyue.com/blog/2014/10/12/understanding-weakreference-in-java/)
3. [https://www.iflym.com/index.php/java-programe/201407140001.html](https://www.iflym.com/index.php/java-programe/201407140001.html)
4. [https://www.iflym.com/index.php/code/201609050002.html](https://www.iflym.com/index.php/code/201609050002.html)
4. [https://docs.oracle.com/javase/8/docs/api/java/util/WeakHashMap.html](https://docs.oracle.com/javase/8/docs/api/java/util/WeakHashMap.html)
2. [《深入理解Java虚拟机》 ](http://pan.baidu.com/s/1c2A3XOO)&emsp;&emsp;密码：a2tr

-----

![](/img/java-reference.png)

### <a>一、理解</a> ###
1. 引用（Reference），描述了引用和对象之间的关系（引用-->对象），一个对象可对应多个、多种引用
2. <font color="red">gc根据引用和对象的关系和JVM内存的使用情况，回收对象；对象回收了，引用也会非回收</font>
3. 不同种类的引用，描述了，gc回收该引用不同的回收时机
4. 引用可以是，静态属性、对象属性、局部变量
5. <font color="red">重点理解。引用的种类、内存使用情况、回收时机</font>


### <a>二、引用的分类</a> ###

#### <a>1. 强引用（StrongReference） </a>####

1. 回收时机，<font color="red">不会被回收</font>。无论内存是否够用、无论系统是否调用了gc
2. 示例<br/>
	`Object object = new Object();`<br/>
3. 应用，编程中经常使用
	
#### <a>2. 软引用（SoftReference）</a> ####

1. 回收时机，<font color="red">jvm调用了gc，同时现在内存不足</font>
2. 示例<br/>
	 `SoftReference<String> sr = new SoftReference<String>(new String("hello")); `
3. `SoftReference`对象中的get方法<br/>
	在SoftReference类中，有三个方法，两个构造方法和一个<font color="red">get方法</font>（WekReference类似）
	get方法用来获取与软引用关联的对象的弱引用，如果该<font color="red">对象被回收了，则返回null</font>
3. 应用<br/><font color="red">适合用来实现缓存：比如网页缓存、图片缓存等</font>

		// 缓存图片
		private Map<String, SoftReference<Bitmap>> imageCache = new HashMap<String, SoftReference<Bitmap>>();
		public void addBitmapToCache(String path) {
		        // 强引用的Bitmap对象
		        Bitmap bitmap = BitmapFactory.decodeFile(path);
		        // 软引用的Bitmap对象
		        SoftReference<Bitmap> softBitmap = new SoftReference<Bitmap>(bitmap);
		        // 添加该对象到Map中使其缓存
		        imageCache.put(path, softBitmap);
		    }
		 public Bitmap getBitmapByPath(String path) {
		        // 从缓存中取软引用的Bitmap对象
		        SoftReference<Bitmap> softBitmap = imageCache.get(path);
		        // 判断是否存在软引用
		        if (softBitmap == null) {
		            return null;
		        }
		        // 取出Bitmap对象，如果由于内存不足Bitmap被回收，将取得空
		        Bitmap bitmap = softBitmap.get();
		        return bitmap;
		    }

#### <a>3. 弱引用（WeakReference）</a> ####

1. 回收时机，会被回收。<font color="red">jvm调用了gc，无论内存是否充足，都会被回收。</font>
2. 示例<br/>
	` WeakReference<String> sr = new WeakReference<String>(new String("hello"));`<br/>
	`System.gc();`<br/>
	`System.out.println(sr.get()); // 输出结果为null`
3. 应用<br/><font color="red">弱引用可以和一个引用队列（ReferenceQueue）联合使用</font>，如果弱引用所引用的对象被JVM回收，这个软引用就会被加入到与之关联的引用队列中。
	
### <a>4. 虚引用（PhantomReference）</a> ###
1. 回收时机，<font color="red">不影响对象的生命周期</font>，在任何时候都可能被垃圾回收器回收。
2. 说明<br/>
	<font color=red>不可以通过get方法来得到其指向的对象。它的唯一作用就是当其指向的对象被回收之后，自己被加入到引用队列，用作记录该引用指向的对象已被销毁。</font>
2. 示例<br/>
	`ReferenceQueue<String> queue = new ReferenceQueue<String>();`<br/>
	`PhantomReference<String> pr = new PhantomReference<String>(new String("hello"), queue);`<br/>
	<font color="red">`System.out.println(pr.get()); // 在回收虚引用之前，调用他的方法`</font>
3. 应用<br/>
	<font color="red">虚引用必须和引用队列关联使用。</font><br/>
	程序可以通过判断引用队列中是否已经加入了虚引用，来了解被引用的对象是否将要被垃圾回收。<br/>
	如果程序发现某个虚引用已经被加入到引用队列，那么就可以在所引用的对象的内存被回收之前采取必要的行动</font>

### <a>三、总结</a> ###

<table class="meng">
  <tbody>
    <tr>
      <td valign="top" width="25%"><p><span style="font-size: 14px;"><strong>级别</strong></span></p></td>
      <td valign="top" width="25%"><p><span style="font-size: 14px;"><strong>什么时候被垃圾回收</strong></span></p></td>
      <td valign="top" width="25%"><p><span style="font-size: 14px;"><strong>用途</strong></span></p></td>
      <td valign="top" width="25%"><p><span style="font-size: 14px;"><strong>生存时间</strong></span></p></td>
    </tr>
    <tr>
      <td valign="top" width="25%"><p><span style="font-size: 14px;">强引用</span></p></td>
      <td valign="top" width="25%"><p><span style="font-size: 14px;">从来不会</span></p></td>
      <td valign="top" width="25%"><p><span style="font-size: 14px;">对象的一般状态</span></p></td>
      <td valign="top" width="25%"><p><span style="font-size: 14px;">JVM停止运行时终止</span></p></td>
    </tr>
    <tr>
      <td valign="top" width="25%"><p><span style="font-size: 14px; color: #ff0000;">软引用</span></p></td>
      <td valign="top" width="25%"><p><span style="font-size: 14px;">在内存不足时</span></p></td>
      <td valign="top" width="25%"><p><span style="font-size: 14px;">对象简单？缓存</span></p></td>
      <td valign="top" width="25%"><p><span style="font-size: 14px;">内存不足时终止</span></p></td>
    </tr>
    <tr>
      <td valign="top" width="25%"><p><span style="font-size: 14px; color: #ff0000;">弱引用</span></p></td>
      <td valign="top" width="25%"><p><span style="font-size: 14px;">在垃圾回收时</span></p></td>
      <td valign="top" width="25%"><p><span style="font-size: 14px;">对象缓存</span></p></td>
      <td valign="top" width="25%"><p><span style="font-size: 14px;">gc运行后终止</span></p></td>
    </tr>
    <tr>
      <td valign="top" width="25%"><p><span style="font-size: 14px; color: #ff0000;">虚引用</span></p></td>
      <td valign="top" width="25%"><p><span style="font-size: 14px;">Unknown</span></p></td>
      <td valign="top" width="25%"><p><span style="font-size: 14px;">Unknown</span></p></td>
      <td valign="top" width="25%"><p><span style="font-size: 14px;">Unknown</span></p>
        <div><span style="font-size: 14px;">&nbsp;</span></div></td>
    </tr>
  </tbody>
</table>