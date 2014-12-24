---
layout: post
title: "Java notes"
description: ""
category: 
tags: []
---
{% include JB/setup %}

__String__

- String.toLowerCase :  不会影响中文。

- StringUtils.isEmpty(String str) 判断String是否为null或str.length=0

- StringUtils.isBlank(String str) 判断String是否为一个或多个空格符或str.length=0. PS : str=null时会抛出异常.

__Map__

- Map.Entry当作C++中的Pair使用 : Map.Entry<String, Integer> pair = new AbstractMap.SimpleEntry<String,Integer>("abc", 1);

- TreeMap.tailMap(K fromKey)返回TreeMap中大于等于fromKey的部份.

__Arrays__

- Arrays.asList(T[]) 将数组转成list

__Spring__

- @Resource注入查找的是配置中的bean, bean中property属性调用的是set方法, 不能用@Resource

__IDE__

- IntelliJ IDEA

 - ideaVim

- Eclipse

 - vrapper

__序列化__

将对象转换为字节流保存到文件(序列化), 用于java虚拟机重启后在内存中还原这个对象(反序列化),叫对象序列化.

- static变量无法序列化

- 传递性(A引用B, 序列化A时, B也会被序列化, 若B无法序列化, 抛异常)

示例

```java
public void SerializableExample() {
    Person person = new Person("Mike");  

    //序列化
    ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("output"));  
    oos.writeObject(person);
    oos.close();  

    //反序列化
    ObjectInputStream ois = new ObjectInputStream(new FileInputStream("output"));  
    Person personBack = (Person) ois.readObject();  
    ois.close();  

    System.out.println(personBack.getName());  
}
```

serialVersionUID的作用:

当对象Person被序列化到文件中后, 其结构变化了(例如增加一个age变量), 这时候反序列化时就会报错(这是由于序列化时对象没有指定serialVersionUID, 编译器会自动生成一个serialVersionUID, 而结构变化后, 编译器又自动生成一个serialVersionUID, 前后自动生成的两个serialVersionUID不一致导致的).

因此我们可以自己指定一个serialVersionUID(Eclipse和Intellij IDEA 都有自动生成的功能, 也可以手动生成), 这样在结构变化后, 仍然可以反序列化结构变化前的对象.

