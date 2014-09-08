---
layout: post
title: "Java的Jdbc编程代码优化"
categories:
- java
---

#### 代码优化的思路 ####

&emsp;&emsp;对数据的操作一般分为两种，更新和操作。无论是更新，还是操作，其中都会有构建连接、创建sql语句，执行操作，释放资源的过程。我们可以把一些重复的代码进行重构。

&emsp;&emsp;把公共的部分拿到父类中去，把不同的部分交给子类。


#### 修改前的“子类” ####

继承的UserDao中有没有实现的，增、删、改、查、的方法。

	public interface UserDao {
	
		public void addUser(User user);
		public User getUser(int userId);
		public void findUser(String loginName, String password);
		public void update(User user);
		public void delete(User user);
	}

---
	public class UserDaoJdbcImpl  implements UserDao{
	
		@Override
		public void addUser(User user) {
			Connection conn = null;
			PreparedStatement ps = null;
			ResultSet rs = null;
			try{
				conn = JdbcUtils.getConnection();
				String sql = "update user set name=?, birthday=?, money=?, where id=?";
				ps = conn.prepareStatement(sql);
				ps.setString(1, user.getName());
				//时间日期格式转换
				ps.setDate(2, new java.sql.Date(user.getBirthday().getTime()));
				ps.setFloat(3, user.getMoney());
				ps.setInt(4, user.getId());
				ps.executeUpdate();
			}catch(SQLException e){
				throw new RuntimeException(e);
			}finally {
				JdbcUtils.free(rs, ps, conn);
			}
			
		}

		/**
		*实现接口中，其他没有实现的方法
		*/
	}

#### 优化后的代码结构 ####

把共有的代码部分放到超类中

	public class AbstractDao{
		
		public int addUser(String sql, Object[] args) {
			Connection conn = null;
			PreparedStatement ps = null;
			ResultSet rs = null;
			try{
				conn = JdbcUtils.getConnection();
				ps = conn.prepareStatement(sql);
				for(int i=0; i<args.length; i++){
					ps.setObject(i+1, args[i]);
				}
				ps.executeUpdate();
				return ps.executeUpdate();
			}catch(SQLException e){
				throw new RuntimeException(e);
			}finally {
				JdbcUtils.free(rs, ps, conn);
			}	
		}

		/**
		*实现其他没有实现的方法
		*/
	}

#### 子类中 ####

	public class UserDaoJdbcImpl  extends AbstractDao implements UserDao{
	
		@Override
		public void addUser(User user) {
			String sql = "update user set name=?, birthday=?, money=?, where id=?";
			Object [] args = new Object []{user.getName(), user.getBirthday(), user.getMoney(), user.getId()};
			super.update(sql, args);
		}
		/**
		*实现没有实现的方法
		*/
	}



## JDBC工具类 ##
	
&emsp;&emsp;`JdbcUtils.java`&emsp;通过该工具类,实现传入已编译过得sql语句，和Object[]完成update和query。当调用的方法是query时，要把结果集封装成对象。但是，不知道把
ResultSet封装成什么对象，以及如何封装。所以，在传入sql语句和Object[]的同时，要传入对ResultSet的处理操作。这里通过，传入一个ResultSetHandler类，并实现里面的方法。

	public class JdbcUtils {
		private static DataSource ds = null;
		private static Logger logger = Logger.getLogger(JdbcUtils.class);
		static {
			Properties prop = PropertyUtil
					.getPropertie("dbcpconfig.properties");
			BasicDataSourceFactory factory = new BasicDataSourceFactory();
			try {
				ds = factory.createDataSource(prop);
			} catch (Exception e) {
				logger.error("初始化数据源错误！", e);
				throw new ExceptionInInitializerError(e);
			}
		}
	
		public static int update(String sql, Object[] params) throws SQLException {
			Connection conn = null;
			PreparedStatement st = null;
			// 变异sql语句
			conn = JdbcUtils.getConnection();
			int resultCount;
			try {
				st = conn.prepareStatement(sql);
				for (int i = 0; i < params.length; i++) {
					st.setObject(i+1, params[i]);
				}
				resultCount = st.executeUpdate();
			} finally {
				release(conn, st, null);
			}
			return resultCount;
		}
	
		public static Object query(String sql, Object[] params, ResultSetHandler rsh)
				throws SQLException {
			Connection conn = null;
			PreparedStatement st = null;
			ResultSet rs = null;
			try {
				conn = JdbcUtils.getConnection();
				st = conn.prepareStatement(sql);
				for (int i = 0; i < params.length; i++) {
					st.setObject(i + 1, params[i]);
				}
				rs = st.executeQuery();
				return rsh.handler(rs);
			} finally {
				release(conn, st, rs);
			}
		}
	
		private static Connection getConnection() {
			Connection con = null;
			try {
				con = ds.getConnection();
			} catch (SQLException e) {
				throw new RuntimeException(e);
			}
			return con;
		}
	
		private static void release(Connection conn, PreparedStatement st,
				ResultSet rs) {
			try {
				if (rs != null) {
					rs.close();
				}
			} catch (SQLException e) {
				e.printStackTrace();
			} finally {
				try {
					if (st != null) {
						st.close();
					}
				} catch (SQLException e) {
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

&emsp;&emsp;`ResultSetHandler.java`&emsp;实现这个接口，才能对结果集处理，封装成对象。返回结果。

	public interface ResultSetHandler{
		public Object handler(ResultSet rs);
	}

&emsp;&emsp;`BeanHandler.java`在使用反射技术，创建对象的时候，要传入`clazz`，这样才能知道返回结果是什么类型的对象。同时，也可以通过clazz创建对象，调用里面的方法设置属性。

	public class BeanHandler implements ResultSetHandler{
		private Class clazz;
		public BeanHandler(Class clazz){
			this.clazz = clazz;
		}
		@Override
		public Object handler(ResultSet rs) {
			Object bean = null;
			try {
				if((rs == null)){
					return null;
				}
				bean = clazz.newInstance();
				ResultSetMetaData metadata = rs.getMetaData();
				int columnCount = metadata.getColumnCount();
				for(int i=0; i<columnCount; i++){
					String coulmnName = metadata.getColumnName(i+1);
					Object conlmnData = rs.getObject(i+1);
					//使用反射技术
					Field f = clazz.getDeclaredField(coulmnName);
					//可以得到私有属性
					f.setAccessible(true);
					f.set(bean, conlmnData);
				}
			} catch (Exception e) {
				throw new RuntimeException(e);
			}
			return bean;
		}
		
	}

`BeanListHandler.java`


	public class BeanListHandler implements ResultSetHandler{
		private Class clazz;
		public BeanListHandler(Class clazz){
			this.clazz = clazz;
		}
		
		@SuppressWarnings({ "unchecked", "rawtypes" })
		@Override
		public Object handler(ResultSet rs) {
			List list = new ArrayList<>();
			try {
				while(rs.next()){
					Object bean = clazz.newInstance();
					ResultSetMetaData metadata = rs.getMetaData();
					int columnCount = metadata.getColumnCount();
					for(int i=0; i<columnCount; i++){
						String coulmnName = metadata.getColumnName(i+1);
						Object conlmnData = rs.getObject(i+1);
						//使用反射技术
						Field f = clazz.getDeclaredField(coulmnName);
						//可以得到私有属性
						f.setAccessible(true);
						f.set(bean, conlmnData);
					}
					list.add(bean);
				}
				
				return list.size()>0 ? list : null;
			} catch (Exception e) {
				throw new RuntimeException(e);
			}
		}
		
	}

#### 注意： ####

&emsp;&emsp;通过反射技术创建对象,对要创建的对象是有要求的：<br/>

1. class是public的
2. class有`public className(){};`的构造方法。
3. class中的属性attr要有，`public setAttr(){};`和`public getAttr(){};`注意属性名和方法名的对应关系。