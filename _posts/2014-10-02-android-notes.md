---
layout: post
title: "android notes"
description: ""
category: 
tags: []
---
{% include JB/setup %}

__layout__

- LinearLayout标签 : 线性布局, 标签中所有元件都是由上到下的排队排成的.

- android:orientation = vertical : 定义对齐方式(vertical or horizontal)

- android:layout_width = fill_parent : 定义当前视图在屏幕上可以消费的宽度.(填充整个屏幕, 2.2以上同match_parent)

- android:layout_width = wrap_content : 随文字栏不同改变视图宽或高.

- android:layout_weight = 1 : 组件占比, 默认各个组件为0, 设置后占比为x/sum

string资源配置 : 在__res/values/strings.xml__中作如下配置

```XML
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">firstapp</string>
    <string name="edit_message">Enter a message</string>
</resources>
```

这样可以用如下方式调用:

```XML
<EditText android:hint="@string/edit_message" />
```

__动作__

在button中添加动作

```XML
<Button android:onClick="sendMessage" />
```

在主Action类中增加__public void__方法sendMessage(View view),点击按钮后会执行该方法.

使用Intent启动另一个Action

```java
public void sendMessage(View view) {
    Intent intent = new Intent(this, DisplayMessageActivity.class);
    EditText editText = (EditText) findViewById(R.id.edit_message);
    String message = editText.getText().toString();
    intent.putExtra(EXTRA_MESSAGE, message);
    startActivity(intent);
}
```

Intent相当于两个Action的粘合剂, 能把前一个Action压栈, 前往另一个Action. 用putExtra方法以K/V的形式在两个Action中传递信息.


