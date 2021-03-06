---
layout: post
title: "Java Web 基础"
categories:
- java
---
1. [java_ ee_sdk-7u2](http://www.oracle.com/technetwork/java/javaee/downloads/java-ee-sdk-7-downloads-1956236.html) &emsp;&emsp;<font color=red>**API文档在SDK中**</font>
2. [java-ee-sdk-7u2-api online](http://docs.oracle.com/javaee/7/api/)


1. 创建web应用的3三种方式(context就是web应用)
	1. 修改`path2Tomcat/conf/server.xml`文件，需要重启、被所有web应用共享
	2. 修改`path2Tomcat/conf/context.xml`文件，被所有web应用共享
	3. 添加一个xml文件到`conf/Catalina/localhost`,不需要重启，只所属当前web应用使用
		1. 文件名位虚拟目录 `conf/Catalina/localhostfmeng#index.xml --> http://host/fmeng/index`
		2. 缺省web应用`conf/Catalina/localhostfmeng#index.xml --> http://host`
	3. tomcat自动管理`path2Tomcat/webapps`，映射成虚拟目录

----
`path2Tomcat/conf/server.xml`

		<?xml version='1.0' encoding='ISO-8859-1'?>
		<!--server.xml-->
		<Server port="8005" shutdown="SHUTDOWN">
		  <GlobalNamingResources>
		    <Resource name="UserDatabase" auth="Container"
		              type="org.apache.catalina.UserDatabase"
		              description="User database that can be updated and saved"
		              factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
		              pathname="conf/tomcat-users.xml" />
		  </GlobalNamingResources>
		  <Service name="Catalina">
		    <Connector port="8080" protocol="HTTP/1.1"
		               connectionTimeout="20000"
		               redirectPort="8443" />
		    <Engine name="Catalina" defaultHost="localhost">
		      <Realm className="org.apache.catalina.realm.LockOutRealm">
		        <Realm className="org.apache.catalina.realm.UserDatabaseRealm"
		               resourceName="UserDatabase"/>
		      </Realm>
		      <Host name="localhost"  appBase="webapps"
		            unpackWARs="true" autoDeploy="true">
		        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
		               prefix="localhost_access_log." suffix=".txt"
		               pattern="%h %l %u %t &quot;%r&quot; %s %b" />
				 <!--创建一个web应用-->
			   	<Context path="/itcast" docBase="c:\news" />
				<!--创建一个缺省web应用-->
				<Context path="" docBase="c:\news" />
		      </Host>
		    </Engine>
		  </Service>
		</Server>


----
`path2Tomcat/conf/context.xml`

	<?xml version='1.0' encoding='ISO-8859-1'?>
	<Context>
	    <WatchedResource>WEB-INF/web.xml</WatchedResource>
	</Context>

----
`conf/Catalina/localhost`

	<Context docBase="c:\news" />

----
2. web应用的组织结构<br/>
![](/img/web11.jpg)
3. 配置虚拟主机

`path2Tomcat/conf/server.xml`
	
	<Host name="fmeng.me"  appBase="webapps"
	            unpackWARs="true" autoDeploy="true">
	        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
	               prefix="localhost_access_log." suffix=".txt"
	               pattern="%h %l %u %t &quot;%r&quot; %s %b" />
		   <!--创建一个web应用-->
		   <Context path="/itcast" docBase="c:\news" />
	</Host>

3. servlet 开发

`WEB-INF/web.xml`配置servlet

	<servlet>
		<servlet-name>servletDemo</servlet-name>
		<servlet-class></servlet-class>
	</servlet>
	<servlet-mapping>
		<servlet-name>servletDemo</servlet-name>
		<url-pattern>/servletDemo</url-pattern>
		<!--在启动的时候被创建
		<load-on-startup>1</load-on-startup>
		-->
	</servlet-mapping>
4. ServletConfig

		<!--在服务器启动的时候，服务器读取配置文件web.xml创建ServletConfig对象。-->
		<servlet>
			<servlet-name>servletDemo</servlet-name>
			<servlet-class></servlet-class>
			<init-param>
				<param-name>field</param-name>
				<param-value>value</param-value>
			</init-param>
		</servlet>
5. ServletContext <font color=red>(当前web应用对象,Context域对象)</font>

	1. `ServletConfig.getServletContext();` 得到ServletContext；
	2. `this.getServletContext();`得到ServletContext；
	3. 获得转发转发对象：
	
			RequestDispatcher rd = this.getServletContext().getRequestDispatcher("1.jsp");
			rd.forward(request,response);
6. Request和Response<br/>
	客户端跳转：`Response.setRedirect("test.jsp");`<br/>
	服务端跳转：`request.getRequestDispatcher("/message.jsp").forward(request, response);`


-----
总结：**先学习sevlet再学习jsp技术。为了开发方便会引入其他技术。**
