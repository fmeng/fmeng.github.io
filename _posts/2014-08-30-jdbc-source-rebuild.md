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





