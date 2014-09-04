---
layout: post
title: "普通配置DBCP"
categories:
- java
---

#### 导入的jar包 ####
[jar包下载：](#)<br/>
[http://pan.baidu.com/s/1kTHrtav](http://pan.baidu.com/s/1kTHrtav)<br/>
	commons-dbcp2-2.0.0.jar<br/>
	commons-pool.jar<br/>
	commons.collections-3.2.1.jar<br/>
	mysql-connector-java-5.1.28-bin.jar<br/>

#### 使用的配置文件 ####

	driverClassName=com.mysql.jdbc.Driver
	url=jdbc:mysql://127.0.0.1:3306/test
	userName=root
	password=mengmeng
	
	#初始化连接
	initialSize=10
	
	#最大连接
	maxActive=50
	
	#最大空闲连接
	maxIdle=20
	
	#最小空闲连接
	minIdle=5
	
	#超时等待时间 单位为毫秒
	maxWait=60000
	
	#在连接数据库过程中，使用的参数
	connectionProperties=useUnicode=true;characterEncoding=gbk
	
	#是否自动提交
	defaultAutocommit=true
	
	#所有的连接是否是只读的
	defaultReadOnly=
	
	#数据库的隔离级别
	defaultTransactionIsoLation=READ_UNCOMMITTED

#### Java代码 ####

	private static DataSource myDataSource = null;
	static {
		try {
			Class.forName("com.mysql.jdbc.Driver");
			Properties properties = new Properties();
			InputStream is = JdbcUtils.class.getClassLoader().getResourceAsStream("dbcp.properties");
			properties.load(is);
			myDataSource = BasicDataSourceFactory.createDataSource(properties);
			if(null != is){
				is.close();
			}
		} catch (Exception e) {
			throw new ExceptionInInitializerError(e);
		}
	}

	public static Connection getConnection() throws SQLException {
		return myDataSource.getConnection();
	}

#### DBCP的原理--自己实现数据源 ####

	public class MyDataSource {
		private static String url = "jdbc:myql://127.0.0.1:3306/jdbc";
		private static String user = "root";
		private static String password = "";
		private static int initCount = 5;
		private static int maxCount = 10;
		private static int currentCount = 0;
		private LinkedList<Connection> connectionsPool = new LinkedList<>();
		
		public MyDataSource(){
			for(int i=0; i<5; i++){
				try {
					this.connectionsPool.addLast(this.createConnection());
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					throw new ExceptionInInitializerError(e);
				}
			}
		}
		
		public Connection getConnection() throws SQLException{
			synchronized (connectionsPool) {
				if(this.connectionsPool.size() > 0){
					return this.connectionsPool.removeFirst();
				}
				if(this.currentCount < this.maxCount){
					this.currentCount ++;
					return this.createConnection();
				}
				throw new SQLException("已没有连接可以使用！");
			}
		}
		
		public void free(Connection conn){
			this.connectionsPool.addLast(conn);
		}
	
		private Connection createConnection() throws SQLException {
			return DriverManager.getConnection(url, user, password);
		}
	}

&emsp;&emsp;为了防止连接被其他人误关闭，例如使用`c.close()`方法,而不是使用`MyDataSource.free()`方法，造成数据员中的可用连接越来越少。需要重写Connection的`close()`方法。

	public class MyConnection implements Connection{
	
		private java.sql.Connection realConnection ;
		MyConnection(java.sql.Connection Connection){
			this.realConnection = Connection;
		}

		/**
		*重写connection的close方法，不是直接关闭连接，
		*而是把它们重新方法数据源中。
		*/
		@Override
		public void close() throws SQLException {
			MyDataSource.free(connection);
		}

		@Override
		public <T> T unwrap(Class<T> iface) throws SQLException {
			return this.realConnection.unwrap(iface);
		}
		@Override
		public boolean isWrapperFor(Class<?> iface) throws SQLException {
			return this.realConnection.isWrapperFor(iface);
		}

		/**
		*实现所有没有实现的方法
		*/
	}
















