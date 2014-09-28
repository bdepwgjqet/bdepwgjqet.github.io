- interface : java中的抽象类，所有方法公开，抽象。

 - 类可以继成接口.

 - 类只能继成另外一个类，但能继承多个接口。

 - 接口与接口可以继承，允许多继承。

- String

 - String.toLowerCase :  不会影响中文。

 - StringUtils.isEmpty(String str) 判断String是否为null或str.length=0

 - StringUtils.isBlank(String str) 判断String是否为一个或多个空格符或str.length=0. PS : str=null时会抛出异常.

- Map

 - Map.Entry当作C++中的Pair使用 : Map.Entry<String, Integer> pair = new AbstractMap.SimpleEntry("abc", 1);

- Arrays

 - Arrays.asList(T[]) 将数组转成list

- Spring

 - @Resource注入查找的是配置中的bean, bean中property属性调用的是set方法, 不能用@Resource

- IntelliJ IDEA

 - ideaVim

- Eclipse

 - vrapper
