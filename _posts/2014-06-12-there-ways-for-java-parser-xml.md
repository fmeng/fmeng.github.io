---
layout: post
title: "JAVA解析XML的三种方式"
categories:
- java
---



1. ##DOM解析

	&emsp;&emsp;**简单介绍。**是W3C组织推荐的解析XMl文档的方式。会把xml文档全部读入内存，根据所有xml的结点建立对象，组织对象之间的关系，比较吃消内存空间。适合，增、删、改，不适合查找。

	&emsp;&emsp;**解析代码。**<br/>
	使用的开发工具包。 javax.xml、org.w3c.dom、org.xml.sax。用解析器解析xml文档产生document对象。所以代码首先要分为两步，1.产生一个解析器。2.产生一个document对象。3.操作document对象。

 >要操作的xml文件。<br/>
 

	<?xml version="1.0" encoding="UTF-8" standalone="no"?>
	<exam>
	<student examid="222" idcard="111">
		<name>张三</name>
		<location>沈阳</location>
		<grade>95</grade>
	</student>
	</exam>
操作xml的代码
		
			import java.io.FileNotFoundException;
			import java.io.FileOutputStream;
			import java.io.IOException;
			import javax.xml.parsers.DocumentBuilder;
			import javax.xml.parsers.DocumentBuilderFactory;
			import javax.xml.parsers.ParserConfigurationException;
			import javax.xml.transform.Transformer;
			import javax.xml.transform.TransformerConfigurationException;
			import javax.xml.transform.TransformerException;
			import javax.xml.transform.TransformerFactory;
			import javax.xml.transform.dom.DOMSource;
			import javax.xml.transform.stream.StreamResult;
			import org.w3c.dom.Document;
			import org.w3c.dom.Element;
			import org.w3c.dom.NodeList;
			import org.xml.sax.SAXException;
			
			import com.sun.org.apache.xalan.internal.xsltc.cmdline.Transform;
			
			
			public class TestDom {

			private DocumentBuilderFactory factory = null;
			private DocumentBuilder builder = null;
			private Document document= null;
			public static void main(String[] args) throws Exception{
			  new TestDom().start();

			}
			
			public void start() throws ParserConfigurationException, SAXException, IOException, TransformerException{
				//1.创建工厂
				factory = DocumentBuilderFactory.newInstance();
				//2.得到document解析器
				builder = factory.newDocumentBuilder();
				//3.解析xml文档，得到代表文档的document
				document = builder.parse("src/Students.xml");
				this.add();
			}
			
			public void add() throws FileNotFoundException, TransformerException{
				
				//创建节点
				Element s = document.createElement("student");
				s.setTextContent("test");
				
				//把创建的节点挂到原始节点上
				Element e = (Element) document.getElementsByTagName("exam").item(0);
				e.appendChild(s);
				
				//更新后的内存写入到xml文档
				TransformerFactory tff = TransformerFactory.newInstance();
				Transformer tf = tff.newTransformer();
				tf.transform(new DOMSource(document), new StreamResult(new FileOutputStream("src/Students.xml")));
			}
		
		}


	

&emsp;&emsp;**注意细节。**要把改变后的Document写回文件。

2. ##SAX解析
	
	&emsp;&emsp;**简单介绍。**不是官方标准。但事实上是XMl社区公认的标准，几乎所有的XML解析器都支持它。Sax解析采用的是事件处理的方式。不会占用太多的内存。

	&emsp;&emsp;**解析代码。**


**book.xml**
****
		<?xml version="1.0" encoding="UTF-8" ?>
		<书架>
			<书>
				<书名>Java就业培训教程</书名>
				<作者>张孝祥</作者>
				<售价>109元</售价>
			</书>
			<书>
				<书名>JavaScript网页开发</书名>
				<作者>黎活明</作者>
				<售价>28。0元</售价>
			</书>
		</书架>
**Run.java**
****

		package fmeng.github.test;
		
		import java.util.List;
		
		import javax.xml.parsers.SAXParser;
		import javax.xml.parsers.SAXParserFactory;
		
		import org.junit.Test;
		import org.xml.sax.XMLReader;
		
		import fmeng.github.book.Book;
		
		
		
		public class Run {
			SAXParserFactory factory = null;
			SAXParser parser = null;
			XMLReader reader = null;
			
			@Test
			public void initReader() throws Exception{
				//创建一个工厂
				factory = SAXParserFactory.newInstance();
				//创建一个解析器
				parser = factory.newSAXParser();
				//创建一个读取器
				reader = parser.getXMLReader();
				//设置内容处理器
				BeanListHeadler listHeadler = new BeanListHeadler();
				reader.setContentHandler(listHeadler);
				//读取Xml内容
				reader.parse("src/book.xml");
				List<Book> l = listHeadler.getBooks();
				System.out.println(l.isEmpty());
			}
		}
**BeanListHeadler.java**
		package fmeng.github.test;
		
		import java.util.ArrayList;
		import java.util.List;
		
		import org.xml.sax.Attributes;
		import org.xml.sax.SAXException;
		import org.xml.sax.helpers.DefaultHandler;
		
		import fmeng.github.book.Book;
		
		public class BeanListHeadler extends DefaultHandler{
			
			private List<Book> list = new ArrayList<>();
			private String currentTag = null;
			private Book book = null;
			/**
			 * 注意这个解释器的使用方法。
			 * 1.设置list和book对象为全局变量。
			 * 2.equals方法的空指针异常要避免
			 * 3.注意程序执行的过程，细节
			 * 4.在endElement方法中要注意。book和currentTag置空
			 * 5.在每个函数，start和end接受的是tag，首先要处理的是currentTag=qName；
			 */
			
			@Override
			public void startElement(String uri, String localName, String qName,
					Attributes attributes) throws SAXException {
				this.currentTag = qName;
				if("书".equals(currentTag)){
					book = new Book();
				}	
			}
		
			@Override
			public void characters(char[] ch, int start, int length)
					throws SAXException {
				if("书名".equals(currentTag)){
					String name = new String (ch,start,length);
					this.book.setName(name);
				}else if("作者".equals(currentTag)){
					String author = new String (ch,start,length);
					this.book.setAthor(author);
				}else if("售价".equals(currentTag)){
					String price = new String(ch,start,length);
					this.book.setPrice(price);
				}
			}
			
			@Override
			public void endElement(String uri, String localName, String qName)
					throws SAXException {
				currentTag = qName;
				if("书".equals(currentTag)){
					list.add(book);
					book = null;
				}
				currentTag = null;
			}
			
			public List<Book> getBooks(){
				return list;
			}
			
		}


&emsp;&emsp;**注意细节。**在读取器读取文件之前把内容处理器写好。

3. ##DOM4J解析

	&emsp;&emsp;**简单介绍。**
	实开发中使用这套API，简单方便，高效。
	[http://www.dom4j.org/](http://www.dom4j.org/ "dom4j")<br/>
	
	jar包下载 [http://sourceforge.net/projects/dom4j/](http://sourceforge.net/projects/dom4j/)

	里面有解析，说明文档，直接使用example就能看懂。

	&emsp;&emsp;**易出错的地方**
1. 要得到里层的东西，要从外面一层一层往里找。
2. XMLWriter writer = new XMLWriter(new FileWriter("path/to/name.xml"));会出现乱码问题。分析原因。filewriter的码表是本机码表。因为是字符流，所以不能指定码表。outputStreamwriter是字节流，可以指定码表。
3. xmlwriter在读xml文档的时候在默认情况下会使用UTF-8读取文档。可以指定格式化输出器格式化读入的xml文档。
4. 两个问题。xml读数据的时候采用哪个码表。文件流输出的时候采用哪个码表。

	&emsp;&emsp;**注意细节。**
使用下载的dom4j.jar包，当解析出现问题时，就要读入额外的jar包。（在lib文件夹中）

##Xpath介绍。
官方文档 
[http://zvon.org/xxl/XPathTutorial/General_chi/examples.html](http://zvon.org/xxl/XPathTutorial/General_chi/examples.html)

## 总结 ##

链接：[http://pan.baidu.com/s/1c0laBp2](http://pan.baidu.com/s/1c0laBp2) 密码：ggia