---
date: 2018-05-03 14:06
status: public
title: Java反射机制
---

## 定义：
```
在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制。

```
* *Class 类十分特殊。它和一般类一样继承自Object，其实体用以表达Java程序运行时的classes和interfaces，也用来表达enum、array、primitive Java types（boolean, byte, char, short, int, long, float, double）以及关键词void。当一个class被加载，或当加载器（class loader）的defineClass()被JVM调用，JVM 便自动产生一个Class 对象。*
* `Class是Reflection故事起源`:针对任何您想探勘的类，唯有先为它产生一个Class 对象，接下来才能经由后者唤起为数十多个的Reflection APIs。
* Class类中的方法可以获取到的内容如下：
 * `构造器`、`方法`、`属性`、`包含的Annotation`、`内部类`、`外部类`、`所实现的接口`、`修饰符`、`所在包`、`类名`、`简称`等；
 * 一些判断方法：`注解类？`、`被某个Annotation修饰？`、`匿名类？`、`数组？`、`枚举类？`、`原始类型？`、`接口？`、`是这个类的实例？boolean isInstance(Object obj)`
* 获取Class的方法：![](https://img-blog.csdn.net/20160724035029056)