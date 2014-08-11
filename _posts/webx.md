- control : Head,Menu,Foot
- screen : Middle
- action : 例如填写表单，点击提交按钮

- ibatis : dynamic.DynamicSql

- TemplateContext context.put(form中标签的Name, Value)

- 实现screen/common下的同名java类，继承BaseScreen，实现excute函数，页面加载一次便 会调用excute函数，excute函数两个参数：
 - RunData : 封装页面请求的request数据
 - TemplateContext : 封装上下文环境参数。
