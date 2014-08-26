---
layout: post
title: "JDBC常见问题"
categories:
- java
---
#### 最终要把driver放到drivers的一个驱动列表list中 ####
#### resultset的索引号是从1开始的 ####
1. 第一种，可读性好，`new com.mysql.jdbc.Driver()`会注册一次驱动,调用registerDriver时注册一次。对mysql的包有依赖，而不是使用字符串，包不在就不能通过编译。
2. 第二中，有两个参数，不适合properties文件的编辑。


#### 要尽量晚的获取连接，尽量早的释放连接。 ####


----

	public static void main(String [] args) throws Exception{
			//1.注册驱动
			//DriverManager.registerDriver(new com.mysql.jdbc.Driver());
			//System.setProperty("jdbc.divers", "com.mysql.jdbc.Driver");
			Class.forName("com.mysql.jdbc.Driver");
			//2.创建连接
			Connection conn = DriverManager.getConnection(
					"jdbc:mysql://127.0.0.1:3306/hellopipi","root","mengmeng");
			//3.创建语句
			Statement st = conn.createStatement();
			//4.执行语句
			ResultSet rs = st.executeQuery("select * from user");
			//5.处理结果
			while(rs.next()){
				for(int i=1; i<4; i++){
					System.out.println(rs.getObject(i));
				}
			}
			//6.释放资源
			rs.close();
			st.close();
			conn.close();
		}

## 使用静态类封装数据库连接的类 ##

	public class JdbcUtils {
		private JdbcUtils() {
		};
	
		private static String url = "jdbc:mysql://127.0.0.1:3306/hellopipi";
		private static String user = "root";
		private static String password = "mengmeng";
		static {
			try {
				Class.forName("com.mysql.jdbc.Driver");
			} catch (ClassNotFoundException e) {
				throw new ExceptionInInitializerError(e);
			}
		}
	
		public static Connection getConnection() throws SQLException {
			return DriverManager.getConnection(url, user, password);
		}
	
		public static void free(ResultSet rs, Statement st, Connection conn) {
			try {
				if (rs != null) {
					rs.close();
				}
			}catch(SQLException e){
				e.printStackTrace();
			} finally {
				try {
					if (st != null) {
						st.close();
					}
				}catch(SQLException e){
					e.printStackTrace();
				} finally {
					if (conn != null) {
						try {
							conn.close();
						} catch (SQLException e) {
							e.printStackTrace();
						}
					}
				}
			}
	
		}
	}

## 使用单例模式 ##

1. 单例模式懒加载，或者延迟加载，实例化类。
2. 在实例化类过程中为了避免多次构造对象要上锁
3. 只是在对象构造的过程中上锁
4. 对第一次创建对象上锁



---
	public class JdbcUtilsSingle {
		private JdbcUtilsSingle() {
		};
	
		private static JdbcUtilsSingle instance = null;
		private static String url = "jdbc:mysql://127.0.0.1:3306/hellopipi";
		private static String user = "root";
		private static String password = "mengmeng";
		static {
			try {
				Class.forName("com.mysql.jdbc.Driver");
			} catch (ClassNotFoundException e) {
				throw new ExceptionInInitializerError(e);
			}
		}
		
		public static JdbcUtilsSingle getInstance(){
			//延时加载
			if(instance == null){
				synchronized (JdbcUtilsSingle.class) {
					if(instance == null){
						instance = new JdbcUtilsSingle();
					}
				}
			}
			return instance;
		}
		public static Connection getConnection() throws SQLException {
			return DriverManager.getConnection(url, user, password);
		}
	
		public static void free(ResultSet rs, Statement st, Connection conn) {
			try {
				if (rs != null) {
					rs.close();
				}
			}catch(SQLException e){
				e.printStackTrace();
			} finally {
				try {
					if (st != null) {
						st.close();
					}
				}catch(SQLException e){
					e.printStackTrace();
				} finally {
					if (conn != null) {
						try {
							conn.close();
						} catch (SQLException e) {
							e.printStackTrace();
						}
					}
				}
			}
	
		}
	}

## 使用`java.sql.PreparedStatement`防止sql注入##

1. 问号代替要操作的参数
2. `pst.setString(1, "fmeng111")`替代数据
3. `rs = pst.executeQuery()`执行的结果不要放去sql。默认是追加载编译的sql语句之后


---

	private static void read() throws SQLException{
			Connection conn = null;
			java.sql.PreparedStatement pst = null;
			ResultSet rs = null;
			try{
				// 2.创建连接
				conn = JdbcUtils.getConnection();
				// 3.创建语句
				String sql = "select username, email, create_time, choPassword from user where username=?";
				pst = conn.prepareStatement(sql);
				// 4.执行语句
				pst.setString(1, "fmeng111");
				rs = pst.executeQuery();
				// 5.处理结果
				while (rs.next()) {
					for (int i = 1; i < 4; i++) {
						System.out.println(rs.getObject(i));
					}
				}
			}finally{
				JdbcUtils.free(rs, pst, conn);
			}
		}

## 处理`java.util.date`和sql中的date不一致 ##
因为java.util中date是没有时间的。在java.sql.date中的是有时间的。


----
				public static void main(String[] args) {
			
			//util.date
			Date utilDate = new java.util.Date();
			System.out.println(utilDate.toGMTString());
			System.out.println(utilDate.toLocaleString());
			System.out.println(utilDate.toString());

			//sql.date
			Date sqlDate = new java.sql.Date(utilDate.getTime());
			System.out.println(sqlDate.toGMTString());
			System.out.println(sqlDate.toLocaleString());
			System.out.println(sqlDate.toString());
	
	/**输出结果
	26 Aug 2014 04:36:44 GMT
	2014-8-26 12:36:44
	Tue Aug 26 12:36:44 CST 2014
	26 Aug 2014 04:36:44 GMT
	2014-8-26 12:36:44
	2014-08-26
	 */
		}