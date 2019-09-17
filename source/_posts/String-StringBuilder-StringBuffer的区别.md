---
title: 'String,StringBuilder,StringBuffer的区别'
date: 2019-09-02 14:08:39
tags:
---
区别与联系:
> `String`底层采用不可变数组存储
> `StringBuilder`底层采用可变数组存储，不支持并发
> `StringBuffer`底层采用可变数组，支持并发
## 继承关系图
![logo](String.png)
## String
先来看一下`String`部分源码:
```
private final char value[]
```
可以看到`String`底层采用不可变char数组存储
## StringBuilder
`StringBuilder`部分源码：
```
char[] value
@Override
public StringBuilder append(String str) {
    super.append(str);
    return this;
}
```
底层采用char数组,支持扩容，不支持多线程
## StringBuffer
`StringBuffer`部分源码：
```
char[] value
@Override
public synchronized StringBuffer append(String str) {
    toStringCache = null;
    super.append(str);
    return this;
}
```
底层同样采用char数组，支持扩容，所有的添加，删除，修改操作都增加了`synchronized`关键字，支持多线程