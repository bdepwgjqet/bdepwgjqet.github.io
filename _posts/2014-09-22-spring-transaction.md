---
layout: post
title: "Spring in Action notes : Transaction"
description: ""
category: 
tags: []
---
{% include JB/setup %}

__事务__

- 定义 : 事务是全有或全无的操作,事务将几个操作组合成一个要么全部发生要么全部不发生的工作单元.(把完成一件事的几个步骤包装起来,要么全部步骤都完成,要么都不完成)

- 作用 : 确保数据或资源处于一致的状态, 避免应用程序业务规则的不一致性.

- 特性

  - 原子性 : 事务由一个或多个活动所组成的一个工作单元

  - 一致性 : 一旦事务完成(不管成功还是失败), 系统确保它所建模的业务处于一致的状态

  - 隔离性 : 允许多个用户对相同数据操作,不同用户之间不会相互纠缠在一起.

  - 持久性 : 结果应该持久化, 这样能从任何的系统崩溃中恢复过来.(将结果存在持久化存储中)

使用事务管理器需先声明本地事务的支持(例如JDBC,iBatis,MyBatis框架事务管理器):

```XML
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"/>
</bean>
```

dataSource是上下文文件中javax.sql.DataSource Bean, DataSourceTransactionManager通过DataSource调用java.sql.Connection管理事务.通过调用连接的commit()方法来提交事务.

有一个saveData方法:

```java
public void saveData(Data data) {
    dataDao.saveData(data);
}
```

使用Spring的TransactionTemplate来添加事务性上下文:

先将TransactionTemplate注入到SpiterServiceImpl中

```XML
<bean id="spiterService class="com.habuma.spitter.service.SpitterServiceImpl">
    ...
    <property name="transactionTemplate">
        <bean class="org.springframework.transaction.support.TransactionTemplate">
            <property name="transactionManager" ref="transactionManager" />
        </bean>
    <property>
</bean>
```

实现TransactionCallBack接口,该接口只要实现doInTransaction方法.

```java
public void saveData(final Data data) {
    transactionTemplate.execute(new TransactionCallback<void>() {
        public void doInTransaction(TransactionStatus transactionStatus) {
            try{
                dataDao.saveData(data);
            } catch (RuntimeException e) {
                transactionStatus.setRollbackOnly();
                throw e;
            }
            return null;
        }
    });
}
```
