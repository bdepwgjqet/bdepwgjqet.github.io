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

装配JdbcSpitterDAO的jcbdTemplate属性:

```XML
<bean id="appDAO"
	class="org.tec.app.JdbcAppDAO">
	<property name="jdbcTemplate ref="jdbcTemplate" />
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


