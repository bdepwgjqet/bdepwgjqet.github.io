---
layout: post
title: "Spring in Action notes : DB && iBatis"
description: ""
category: 
tags: []
---
{% include JB/setup %}

__数据库相关__

Spring 通过 __数据访问对象(DAO, Data access object)提供数据读取和写入到数据库中.DAO 以接口的方式发布功能,应用程序的其它部分通过接口进行访问.

Spring 将数据访问过程中固定的和可变的部分明确划分为两个不同的类:

- 模板 : 管理过程中固定的部分(事务控制,管理资源,处理异常).

- 回调 : 处理自定义的数据访问代码(创建语句,绑定参数,整理结果).

__配置数据源__

数据源 : 存储所有建立数据库连接信息.

连接池 : 数据库连接池负责分配、管理和释放数据库连接，它允许应用程序重复使用一个现有的数据库连接，而再不是重新建立一个. 

Spring 提供了在Spring上下文中配置数据源Bean的多种方式

- 通过JDBC驱动程序定义的数据源

- 通过JNDI查找的数据源

- 连接池的数据源

我接触的项目中通过使用DBCP在Spring中配置数据源连接池, DBCP包含了多个提供连接池功能的数据源,其中BasicDataSource是最常用的,因为它易于在Spring中配置.

可以这样配置一个简单的BasicDataSource

```XML
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">

	<!-- 指定JBDC驱动类的全限定类名 -->
	<property name="driverClassName" value="org.hsqldb.jdbcDriver" />

	<!-- 设置数据库的JDBC URL -->
	<property name="url" value="jdbc:hsqldb:hsql://localhost/appname/appname" />

	<!-- 用户名密码 -->
	<property name="username" value="uname" />
	<property name="password" value="passwd" />

	<!-- 启动时创建的连接数量 -->
	<property name="initialSize" value="5" />

	<!-- 同一时间可从池中分配的最多连接数 0表示无限制 -->
	<property name="maxActive" value="10" />
</bean>
```

使用传统的JDBC进行数据库操作完成简单的任务就需要大量的代码, 这些代码很多是重复的, 通过使用JDBC模板可以简化JDBC代码. Spring 为JDBC提供了3个模板类:

- JdbcTemplate : 最基本的Spring JDBC模板.

- NamedParameterJdbcTemplate : 使用该模板类执行查询时,可以将查询值以命名参数的形式绑定到SQL中

- SimpleJdbcTemplate : 利用java5的特性简化JDBC模板使用.

Spring 2.5中NamedParameterJdbcTemplate所具备的命名参数功能被合并到SimpleJdbcTemplate中,所以选择SimpleJdbcTemplate.

设置DataSource : 

```XML
<bean id="jdbcTemplate"
	class="org.springframework.jdbc.core.simple.SimpleJdbcTemplate">
	<constructor-arg ref="dataSource" />
</bean>
```

这样便能让SimpleJdbcTemplate正常工作.

将jdbcTemplate装配到DAO中并使用它访问数据库 :

```java
public class JdbcAppDAO implements appDAO {

	private SimpleJdbcTemplate jdbcTemplate;

	public void setJdbcTemplate(SimpleJdbcTemplate jdbcTemplate) {
		this.jdbcTemplate = jdbcTemplate;
	}
}
```

装配JdbcSpitterDAO的jdbcTemplate属性:

```XML
<bean id="appDAO"
	class="org.tec.app.JdbcAppDAO">
	<property name="jdbcTemplate" ref="jdbcTemplate" />
</bean>
```

一个数据库update实现(使用命名参数)可以是这样:

```java
public addUser(User user) {
	Map<String, Object> params = new HashMap<String, Object>();
	params.put("username", user.getUsername());
	params.put("password", user.getPassword());
	params.put("email", user.getEmail());

	jdbcTemplate.update(SQL_INSERT_USER, params);
}
```

一个数据库query实现可以是这样:

```java
public User getUserById(long id) {
	return jdbcTemplate.queryForObject(
		SQL_SELECT_USER_BY_ID,
		new ParameterizedRowMapper<User>() {
			public User mapRow(ResultSet rs, int rowNum) throws SQLException {
				User user = new user();
				user.setId(rs.getLong(1));
				user.setUsername(rs.getString(2));
				user.setPassword(rs.getString(3));
				user.setEmail(rs.getString(4));
				return user;
			}
		},
		id
	);
}
```

__iBatis__

iBatis是一个基于SQL映射支持Java和.NET的持久层框架,现已被Google托管,改名为MyBatis.

iBatis提供的持久层框架包括SQL Maps和DAO.

使用SQL Map能够大大减少访问关系数据库的代码.SQL Map使用简单的XML配置文件将Java Bean映射成SQL语句.

SQL Map使用XML配置文件统一配置不同的属性,包括DataSource的详细信息.一个配置文件例子:

```XML
<sqlMapConfig>
	<properties resource="examples/sqlmap/maps/SqlMapConfigExample.properties " />

	<settings
		cacheModelsEnabled="true"
		enhancementEnabled="true"
		lazyLoadingEnabled="true"
		maxRequests="32"
		maxSessions="10"
		maxTransactions="5"
		useStatementNamespaces="false"
	/>

	<typeAlias alias="order" type="testdomain.Order"/>

	<transactionManager type="JDBC" >
		<dataSource type="SIMPLE">
			<property name="JDBC.Driver" value="${driver}"/>
			<property name="JDBC.ConnectionURL" value="${url}"/>
			<property name="JDBC.Username" value="${username}"/>
			<property name="JDBC.Password" value="${password}"/>
			<property name="JDBC.DefaultAutoCommit" value="true" />
			<property name="Pool.MaximumActiveConnections" value="10"/>
			<property name="Pool.MaximumIdleConnections" value="5"/>
			<property name="Pool.MaximumCheckoutTime" value="120000"/>
			<property name="Pool.TimeToWait" value="500"/>
			<property name="Pool.PingQuery" value="select 1 from ACCOUNT"/>
			<property name="Pool.PingEnabled" value="false"/>
			<property name="Pool.PingConnectionsOlderThan" value="1"/>
			<property name="Pool.PingConnectionsNotUsedFor" value="1"/>
		</dataSource>
	</transactionManager>

	<sqlMap resource="examples/sqlmap/maps/Person.xml" />

</sqlMapConfig>
```

各元素说明:

- properties元素 : 用于在配置文件中使用标准的Java属性文件（name＝value）。这样做后，在属性文件中定义的属性可以作为变量在SQL Map配置文件及其包含的所有SQL Map映射文件中引用。例如，如果属性文件中包含属性： __driver=org.hsqldb.jdbcDriver__则SQL Map配置文件及其每个映射文件都可以使用占位符${driver}来代表值org.hsqldb.jdbcDriver.

- setting 元素 : 用于配置和优化SqlMapClient实例的各选项. 

- typeAlias : 为一个通常较长的、全限定类名指定一个较短的别名. 

- transactionManager : 为SQL Map配置事务管理服务.

- dataSource : transactionManager的一部分,为SQL Map数据源设置了一系列参数. 

- sqlMap元素 : 用于包括SQL Map映射文件和其他的SQL Map配置文件. 

__SQL Map XML 映射文件__

SQL Map的核心概念是Mapped Statement。Mapped Statement可以使用任意的SQL语句，并拥有Parameter Map（输入）和Result Map（输出）。如果是简单情况，Mapped Statement可以使用Java类来作为parameter和result。Mapped Statement也可以使用缓存模型，在内存中缓存常用的数据。

一个SQL Map XML映射文件可以包含任意多个Mapped Statement，Parameter Map和Result Map。

Mapped Statement的结构如下所示：

```XML
<statement id="statementName"
	[parameterClass="some.class.Name"]
	[resultClass="some.class.Name"]
	[parameterMap="nameOfParameterMap"]
	[resultMap="nameOfResultMap"]
	[cacheModel="nameOfCache"]
>
	select * from PRODUCT where PRD_ID = [?|#propertyName#]
	order by [$simpleDynamic$]
</statement>
```

statement类型:

- insert : 对应数据库的 insert 操作，该操作返回本次操作插入记录的主键值。

- update : 对应数据库的 update 操作，该操作返回被更新的记录个数。

- delete : 对应数据库的 delete 操作，该操作返回被删除的记录个数。

- select : 对应数据库的 select 操作，该操作返回特定的 POJO 或 对象。

- procedure : 对应数据库存储过程。

- statement : 类型最为通用，可以代替以上所有的类型。但由于缺乏操作直观性故不推荐。

属性:

- __parameterClass__属性的值是Java类的全限定名（即包括类的包名）。parameterClass属性是可选的，但强烈建议使用。它的目的是限制输入参数的类型为指定的Java类，并优化框架的性能。如果您使用parameterMap，则没有必要使用parameterClass属性。例如，如果要只允许Java类examples.domain.Product作为输入参数，可以这样：

  ```XML
<statement id="statementName" parameterClass="examples.domain.Product">
insert into PRODUCT values (#id#, #description#, #price#)
</statement>
```

- 属性 __parameterMap__的值等于一个预先定义的<parameterMap>元素的名称.比较少用到.

- resultClass属性的值是Java类的全限定名（即包括类的包名）。resultClass属性可以让你指定一个Java类，根据ResultSetMetaData将其自动映射到JDBC的ResultSet。只要是Java Bean的属性名称和ResultSet的列名匹配，属性自动赋值给列值。这使得查询mapped statement变得很短。例如: 

  ```XML
<statement id="getPerson" parameterClass="int" resultClass="examples.domain.Person">
	SELECT 
		PER_ID as id,
		PER_FIRST_NAME as firstName,
		PER_LAST_NAME as lastName,
		PER_BIRTH_DATE as birthDate,
		PER_WEIGHT_KG as weightInKilograms,
		PER_HEIGHT_M as heightInMeters
	FROM 
		PERSON
	WHERE 
		PER_ID = #value#
</statement>
```

- resultMap是最常用和最重要的属性。ResultMap属性的值等于预先定义的resultMap元素的name属性值.

  ResultMap的结构:

  ```XML
<resultMap id="resultMapName" class="some.domain.Class" [extends="parent-resultMap"]>
	<result property="propertyName" column="COLUMN_NAME"
		[columnIndex="1"] [javaType="int"] [jdbcType="NUMERIC"]
		[nullValue="-999999"] [select="someOtherStatement"]
	/>
	<result .../>
	<result .../>
	<result .../>
</resultMap>
```

  例子:

  ```XML
<resultMap id="get-product-result" class="com.ibatis.example.Product">
	<result property="id" column="PRD_ID"/>
	<result property="description" column="PRD_DESCRIPTION"/>
</resultMap>

<statement id="getProduct" resultMap="get-product-result">
	select * from PRODUCT
</statement>
```

  这样查询语句得到的ResultSet被映射成Product对象. 

__动态Mapped Statement__

SQL Map API使用和mapped statement非常相似的结构,可以避免一系列if-else条件语句和一连串字符串连接.

例子:

```XML
<select id="dynamicGetAccountList"
	cacheModel="account-cache"
	resultMap="account-result" >

	select * from ACCOUNT

	<isGreaterThan prepend="and" property="id" compareValue="0">
		where ACC_ID = #id#
	</isGreaterThan>

	order by ACC_LAST_NAME
</select>
```

元素说明:

- prepend : 可被覆盖的SQL语句组成部分，添加在语句的前面（可选）

- property : 被比较的属性（必选）

- compareProperty : 另一个用于和前者比较的属性（必选或选择compareValue）

- compareValue : 用于比较的值（必选或选择compareProperty） 

- \<isEqual\> 比较属性值和静态值或另一个属性值是否相等。

- \<isNotEqual\> 比较属性值和静态值或另一个属性值是否不相等。

- \<isGreaterThan\> 比较属性值是否大于静态值或另一个属性值。

- \<isGreaterEqual\> 比较属性值是否大于等于静态值或另一个属性值。

- \<isLessThan\> 比较属性值是否小于静态值或另一个属性值。

- \<isLessEqual\> 比较属性值是否小于等于静态值或另一个属性值。

- \<isPropertyAvailable\> 检查是否存在该属性（存在parameter bean的属性）。

- \<isNotPropertyAvailable\> 检查是否不存在该属性（不存在parameter bean的属性）。

- \<isNull\> 检查属性是否为null。对于int,long,double这种非Object的默认不为null.

- \<isNotNull\> 检查属性是否不为null。

- \<isEmpty\> 检查Collection.size()的值，属性的String或String.valueOf()值,是否为null或空（""或size() \< 1）。

- \<isNotEmpty\> 检查Collection.size()的值，属性的String或String.valueOf()值,是否不为null或不为空（""或size() \> 0）。

