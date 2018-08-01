---
layout: post
title: Configure mybatis with multiple db connections
date: 2018-08-01
---

Learn how to configure mybatis to connection different databases in Java Web Project.
Assume we want to connect to CustomerDB and ProductDB two databases.

### Create configuration.xml file
This configuration file is maintaining database connection information. As below, there are two environments configured. The db url, username, password information are stored in 
jdbc.properties file. To use it, we need specify it in properties tag.
<code>
<?xml version="1.0" encoding="UTF-8"?>
 <!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<properties resource='jdbc.properties'/>
	<environments default="customerdb">
		<environment id="customerdb">
			<transactionManager type="JDBC" />
			<!-- Data source connect config -->
			<dataSource type="POOLED">
				<property name="driver" value="com.ibm.db2.jcc.DB2Driver" />
				<property name="url" value="${customerdb.url}" />
				<property name="username" value="${customerdb.username}" />
				<property name="password" value="${customerdb.password}" />
			</dataSource>
		</environment>	
		
		<environment id="productdb">
			<transactionManager type="JDBC" />
			<!-- Data source connect config -->
			<dataSource type="POOLED">
				<property name="driver" value="com.ibm.db2.jcc.DB2Driver" />
				<property name="url"
					value="${dao.aact.url}" />
				<property name="username" value="${productdb.username}" />
				<property name="password" value="${productdb.password}" />
			</dataSource>
		</environment>	
	</environments>
	<mappers>
		<mapper resource="com/xx/xx/xxDao.xml" />
		<mapper resource="com/xx/xxDao.xml" />
	</mappers>
</configuration>
</code>

### jdbc.properties file
We store the connection information in properties file, so it is easy to change the value based on which environment the application is deployed.
On dev, we need to change to DEV db connection information. On prod, we need prod connection information. 
<code>
#customer db
customerdb.url=jdbc:db2://<hostname>:<port>/<database>:sslConnection=true;
customerdb.username=username
customerdb.password=xxx

#product db 
#productdb.url=jdbc:db2://<hostname>:<port>/<database>:sslConnection=true;
#productdb.username=username
#productdb.password=xxx
</code>


### MybatisUtil.java
This class is used to get sql seesion factory from customer db / product db. Configuration.xml and jdbc.properties are used in this class.
<code>
public class MyBatisUtil {
	private static SqlSessionFactory sqlSessionFactoryCustomer;
	private static SqlSessionFactory sqlSessionFactoryProduct;
	
	static{
		Reader reader = null;
		try{
			reader = Resources.getResourceAsReader("configuration.xml");
		} catch (IOException e){
			throw new RuntimeException(e.getMessage());
		}
		sqlSessionFactoryDMP = new SqlSessionFactoryBuilder().build(reader,"customerdb");
		sLogger.info("Connecting to customer DB server");
	}
	
	public static SqlSessionFactory getSqlSession4Customer(){
		return sqlSessionFactoryCustomer;
	}
  
  public static SqlSessionFactory getSqlSession4Product(){
     if (sqlSessionFactoryProduct == null){
       // get production db sql session factory
     }
		return sqlSessionFactoryProduct;
	}
</code>

### DAO interface class
### DAO mapping xml
### Service class
Service class is where the dao interface are really used and called.
<code>
public int getCustomerNum(){
		SqlSession sqlSession = MyBatisUtil.getSqlSession4Customer().openSession();
		try {
			CustomerDao dao = sqlSession.getMapper(CustomerDao.class);
			return dao.getCustomerNum();
		} finally {
			sqlSession.close();
		}
		
	}
</code>
