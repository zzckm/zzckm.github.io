---
title: 'Mybatis'
date: 2019-04-25 23:00:57
tags: [Mybatis]
---
# Mybatis

## **简介**
> MyBatis 是支持普通 SQL 查询,存储过程和高级映射的优秀持久层框架。MyBatis 消除 了几乎所有的 JDBC 代码和参数的手工设置以及结果集的检索。使用简单的 XML 或注解用于配置和原始映射,将接口和 Java 的 POJO(Plain Ordinary Java Object,普通的 Java 对象)映射成数据库中的记录。MyBatis的前身是iBatis，MyBatis在iBatis的基础上面，对代码结构进行了大量的重构和简化。

## **第一个程序**

1. 导入相关的包
   ![image_1ch13bp7i1mgl1ie11uaqfkq139jp.png-28.6kB][1] 
2. 添加主配置文件，设置数据库连接信息
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
 <configuration>
 	<!-- 环境配置，可以配置多个 -->
 	<environments default="development">
 	<!-- 表示一个数据库环境 -->
    	<environment id="development">
    		<!-- 
    			事务管理器：权限定名=包名+类名
    			type表示一个类，就是一个权限定名的别名。使用的是JDBC的一个事务管理
    		 -->
	      	<transactionManager type="JDBC"/>
	      	<!-- 
	      		数据源配置，与Spring继承就需要被替换掉
	      		POOLED：使用mybatis中的内置连接池
	      	 -->
	      	<dataSource type="POOLED">
	        <property name="driver" value="com.mysql.jdbc.Driver"/>
	        <property name="url" value="jdbc:mysql:///spring"/>
	        <property name="username" value="root"/>
	        <property name="password" value="123456"/>
	      </dataSource>
	    </environment>
  </environments>
 </configuration>
 
    ```
3. 创建一个实体类文件User.java
4. 添加映射文件UserMapper.xml文件
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace=包名+Mapper文件的文件名 -->
<mapper namespace="com.zhiyou100.mybatis.pojo.UserMapper">
	<!-- 
		insert:表示要插入的语句
		com.zhiyou100.mybatis.pojo.User：调用insert方法传入的参数
		keyColumn:数据库中自增字段的名字
		keyProperty:自增长的ID值应该注入到哪个实体的字段中
		useGeneratedKeys：标记这个标签需要使用数据库中的自增长
	 -->
	<insert id="save" parameterType="com.zhiyou100.mybatis.pojo.User" keyColumn="id" keyProperty="id" useGeneratedKeys="true">
		insert into user(name,password,phone,money) values (#{name},#{password},#{phone},#{money})
	</insert>
</mapper>

    ```
5. 在主配置文件中配置映射文件
    ```xml
   <mappers>
  	    <mapper resource="com/zhiyou100/mybatis/pojo/UserMapper.xml"/>
  </mappers>
  
    ```
6. 创建一个测试类进行测试
    ```java
    package com.zhiyou100.mybatis.test;
    import java.io.IOException;
    import org.apache.ibatis.io.Resources;
    import org.apache.ibatis.session.SqlSession;
    import org.apache.ibatis.session.SqlSessionFactory;
    import org.apache.ibatis.session.SqlSessionFactoryBuilder;
    import org.junit.Test;
    import com.zhiyou100.mybatis.pojo.User;
    public class TestMybatis {
    	@Test
    	public void save() throws IOException {
    		SqlSessionFactory ssf = new SqlSessionFactoryBuilder().build(Resources.getResourceAsReader("mybatis-config.xml"));
    		SqlSession ss = ssf.openSession();
    		User user = new User("zhangsan2","123456","123",12.2);
    		ss.insert("com.zhiyou100.mybatis.pojo.UserMapper.save", user);
    		ss.commit();
    		ss.close();
    		System.out.println(user);
    	}
    }
    
    ```
7. 细节：
    POOLED：对应org.apache.ibatis.datasource.pooled.PooledDataSourceFactory
    JDBC：对应org.apache.ibatis.transaction.jdbc.JdbcTransaction
## **配置日志打印，监控SQL语句**
创建log4j.properties文件，添加一下内容
: 
    ```shell
    # Global logging configuration
    log4j.rootLogger=ERROR, stdout
    # MyBatis logging configuration...下面为namespace的包或者父包
    log4j.logger.com.zhiyou100.mybatis.pojo.UserMapper=TRACE
    # Console output...
    log4j.appender.stdout=org.apache.log4j.ConsoleAppender
    log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
    log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
    
    ```

## **更新操作**
1. 添加配置信息，在UserMapper.xml中
    ```xml
    <update id="update" parameterType="com.zhiyou100.mybatis.pojo.User">
		update user set name=#{name},password=#{password},phone=#{phone},money=#{money} where id=#{id}
	</update>
	
    ```
2. 测试代码
    ```java
    @Test
	public void update() throws IOException {
		SqlSessionFactory ssf = new SqlSessionFactoryBuilder().build(Resources.getResourceAsReader("mybatis-config.xml"));
		SqlSession ss = ssf.openSession();
		User user = new User("zhangsan2","123456","123",12.2);
		user.setId(4);
		ss.update("com.zhiyou100.mybatis.pojo.UserMapper.update", user);
		ss.commit();
		ss.close();
		System.out.println(user);
	}
	
    ```

## 查询操作

1. 添加配置信息，在UserMapper.xml文件中
    ```xml
    <!-- 
	resultType:查询出的每一条结果封装成什么样的对象
	 -->
	<select id="select" parameterType="java.lang.Integer" resultType="com.zhiyou100.mybatis.pojo.User">
		select id,name,password,phone,money from user where id=#{id}
	</select>
    
    ```
2. 测试代码
    ```java
    @Test
	public void select() throws IOException {
		SqlSessionFactory ssf = new SqlSessionFactoryBuilder().build(Resources.getResourceAsReader("mybatis-config.xml"));
		
		SqlSession ss = ssf.openSession();
		User user = ss.selectOne("com.zhiyou100.mybatis.pojo.UserMapper.select", 4);
		ss.commit();
		ss.close();
		System.out.println(user);
	}
    ```
## 查询所有

1. 添加配置信息，在UserMapper.xml文件中
    ```xml
    <!-- 
	resultType:查询出的每一条结果封装成什么样的对象
	 -->
	<select id="list" resultType="com.zhiyou100.mybatis.pojo.User">
		select id,name,password,phone,money from user
	</select>
    
    ```
2. 测试代码
    ```java
   @Test
	public void list() throws IOException {
		SqlSessionFactory ssf = new SqlSessionFactoryBuilder().build(Resources.getResourceAsReader("mybatis-config.xml"));
		
		SqlSession ss = ssf.openSession();
		List<User> list = ss.selectList("com.zhiyou100.mybatis.pojo.UserMapper.list");
		ss.commit();
		ss.close();
		for(User u:list) {
			System.out.println(u);
		}
	}

    ```

## 删除操作
1. 添加配置信息，在UserMapper.xml文件中
    ```xml
    <delete id="delete" parameterType="java.lang.Integer">
		delete from user where id=#{id}
	</delete>
    
    ```
2. 测试代码
    ```java
   @Test
	public void delete() throws IOException {
		SqlSessionFactory ssf = new SqlSessionFactoryBuilder().build(Resources.getResourceAsReader("mybatis-config.xml"));
		SqlSession ss = ssf.openSession();
		ss.delete("com.zhiyou100.mybatis.pojo.UserMapper.delete",6);
		ss.commit();
		ss.close();
	}

    ```
## 别名细节
参考官方文档 XML配置——>别名
|别名|	映射的类型
|--|--|
|_byte|	byte
|_long|	long
|_short|	short
|_int	|int
|_integer|	int
|_double|	double
|_float|	float
|_boolean|	boolean
|string	|String
|byte	|Byte
|long	|Long
|short	|Short
|int	|Integer
|integer|	Integer
|double	|Double
|float	|Float
|boolean|	Boolean
|date	|Date
|decimal|	BigDecimal
|bigdecimal|	BigDecimal
|object	|Object
|map	|Map
|hashmap|	HashMap
|list	|List
|arraylist	|ArrayList
|collection	|Collection
|iterator	|Iterator


注意：底层其实都是调用update方法，但是尽可能还是规范使用


## 配置自己的别名
1. 在主配置文件中
    ```xml
    <typeAliases>
	 	<!-- 
	 		type:需要定义别名的全限定名类
	 		alias：起的别名
	 	 -->
 		<typeAlias type="com.zhiyou100.mybatis.pojo.User" alias="user" />
 	</typeAliases>
    
    ```
2. 使用别名
    ```XML
    <insert id="save" parameterType="user" keyColumn="id" keyProperty="id" useGeneratedKeys="true">
		insert into user(name,password,phone,money) values (#{name},#{password},#{phone},#{money})
	</insert>
	
    ```

## 数据库信息自定义配置
1. 数据库配置文件db.properties
2. 主配置文件就使用占位符（类似于Spring）
3. 测试类代码
    ```java
    @Test
	public void update() throws IOException {
		Properties p = new Properties();
		p.load(Resources.getResourceAsReader("db.properties"));
		SqlSessionFactory ssf = new SqlSessionFactoryBuilder().build(Resources.getResourceAsReader("mybatis-config.xml"),p);
		
		SqlSession ss = ssf.openSession();
		User user = new User("zhangsan2","123456","123",12.2);
		user.setId(4);
		ss.update("com.zhiyou100.mybatis.pojo.UserMapper.update", user);
		ss.commit();
		ss.close();
		System.out.println(user);
	}
    ```
 4. 方式2，直接配置主配置文件如下
    ```xml
    <properties resource="db.properties"></properties>
    ```
    
## 使用Mapper接口的方式

1. 出现的问题
    使用statementID的方式存在如下问题
	1.传入的statement字符串有可能会写错,写错的时候必须等到运行的时候才发现
	2.传入的参数无法限定类型.
2. 使用Mapper接口的方式
    新建UserMapper接口
	* 接口的全限定名==UserMapper.xml中的namespace
	* 接口中方法==UserMapper.xml中标签ID
	* 接口方法上的参数==UserMapper.xml中的parameterType
	* 接口方法上的返回值类型==UserMapper.xml中的resultType
	* 对应的API  ：UserMapper mapper = session.getMapper(UserMapper.class);
3. 测试代码
    ```java
    //接口
    package com.zhiyou100.mybatis.pojo;
//com.zhiyou100.mybatis.pojo.UserMapper
import java.util.List;
public interface UserMapper {
	
	void save(User user);
	void update(User user);
	void delete(int id);
	User select(int id);
	List<User> list();
}
//测试方法
@Test
	public void list() throws IOException {
		SqlSessionFactory ssf = new SqlSessionFactoryBuilder().build(Resources.getResourceAsReader("mybatis-config.xml"));
		SqlSession ss = ssf.openSession();
		UserMapper mapper = ss.getMapper(UserMapper.class);
		List<User> list = mapper.list();
		ss.close();
		for(User u:list) {
			System.out.println(u);
		}
	}
    ```

## ResultMap映射
当查询出来的字段名和对象中的属性名不一致的情况,就没办法使用resultType来默认映射(同名规则)

解决方案:
: 使用resultMap来映射数据库中的字段到底注入到对象中什么属性中.

1. 在Mapper文件中定义一下内容
    ```xml
    <resultMap id="base_map" type="user">
		<!-- 
		column:查询出来的结果的列名
		property：对象的属性名
		jdbcType：数据库中的字段类型
		javaType：实体类中的字段类型
		-->
		<id column="_id" property="id" jdbcType="int" javaType="java.lang.Integer"/>
		<result column="_name" property="name"/>
		<result column="_password" property="password"/>
		<result column="_phone" property="phone"/>
		<result column="_money" property="money"/>
	</resultMap>
	
    ```
2. 在查询结果中使用
    ```xml
    <select id="list" resultMap="base_map" >
		select id as _id,name as _name,password as _password,phone as _phone,money as _money from user;
	</select>
	
    ```
注意：resultMap与resultType只能写一个

数据库连接池的配置
https://blog.csdn.net/tianxiezuomaikong/article/details/68942638



  [1]: http://static.zybuluo.com/zhangwen100/24tm10o8p5r6wz7ocl0mr41e/image_1ch13bp7i1mgl1ie11uaqfkq139jp.png
  [2]: http://static.zybuluo.com/zhangwen100/zbhtuc42awjdm0aucjvz2nse/image_1ch1b5mv8rqkbgp1gqu1ubvqlv2d.png