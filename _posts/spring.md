- Spring容器之Bean工厂,由org.springframework.beans.factory.BeanFactory接口定义。

JavaBean 是一种特殊的Java类，Java Bean的属性由方法定义，set开始的方法是可写属性，get开始的方法是可读属性，set方法不应拥有返回类型，并且只能有一个参数，参数的数据类型必须和属性的数据类型一致。get方法应返回合适的类型并且不允许有参数。

- Spring 配置：在XML文件中<beans>标签内放置所有Spring配置信息，包括<bean>声明（beans不是唯一命名空间）。
