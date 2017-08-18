---
title: Java 反射机制
date: 2017-08-18 11:35:23
tags: [java]
---
# 基本概念
在Java运行时环境中，对于任意一个类，能否知道这个类有哪些属性和方法？对于任意一个对象，能否调用它的任意一个方法？ 答案是肯定的。

这种动态获取类的信息以及动态调用对象的方法的功能来自于Java语言的反射（Reflection）机制。
<!-- more -->
# 反射机制的作用
1、 反编译：.class-->.java
2、 通过反射机制访问java对象的属性，方法，构造方法等；

这样好像更容易理解一些，下边我们具体看怎么实现这些功能。

# 在这里先看一下sun为我们提供了那些反射机制中的类：
java.lang.Class;                
java.lang.reflect.Constructor; java.lang.reflect.Field;        
java.lang.reflect.Method;
java.lang.reflect.Modifier;

# 具体功能实现
1、反射机制获取类有三种方法，我们来获取Employee类型
```
//第一种方式：  
Classc1 = Class.forName("Employee");  
//第二种方式：  
//java中每个类型都有class 属性.  
Classc2 = Employee.class;  
   
//第三种方式：  
//java语言中任何一个java对象都有getClass 方法  
Employeee = new Employee();  
Classc3 = e.getClass(); //c3是运行时类 (e的运行时类是Employee)  
```
2、创建对象：获取类以后我们来创建它的对象，利用newInstance：
```
[java] view plain copy print?
Class c =Class.forName("Employee");  
  
//创建此Class 对象所表示的类的一个新实例  
Objecto = c.newInstance(); //调用了Employee的无参数构造方法.  
```
3、获取属性：分为所有的属性和指定的属性
a，先看获取所有的属性的写法：
```
//获取整个类  
Class c = Class.forName("java.lang.Integer");  
//获取所有的属性?  
Field[] fs = c.getDeclaredFields();  

//定义可变长的字符串，用来存储属性  
StringBuffer sb = new StringBuffer();  
//通过追加的方法，将每个属性拼接到此字符串中  
//最外边的public定义  
sb.append(Modifier.toString(c.getModifiers()) + " class " + c.getSimpleName() +"{\n");  
//里边的每一个属性  
for(Field field:fs){  
sb.append("\t");//空格  
sb.append(Modifier.toString(field.getModifiers())+" ");//获得属性的修饰符，例如public，static等等  
sb.append(field.getType().getSimpleName() + " ");//属性的类型的名字  
sb.append(field.getName()+";\n");//属性的名字+回车  
}  

sb.append("}");  

System.out.println(sb);  
```
b，获取特定的属性，对比着传统的方法来学习：

```
public static void main(String[] args) throws Exception{  
              
//以前的方式：  
/* 
User u = new User(); 
u.age = 12; //set 
System.out.println(u.age); //get 
*/  

//获取类  
Class c = Class.forName("User");  
//获取id属性  
Field idF = c.getDeclaredField("id");  
//实例化这个类赋给o  
Object o = c.newInstance();  
//打破封装  
idF.setAccessible(true); //使用反射机制可以打破封装性，导致了java对象的属性不安全。  
//给o对象的id属性赋值"110"  
idF.set(o, "110"); //set  
//get  
System.out.println(idF.get(o));  
} 
```
4，获取方法，和构造方法，不再详细描述，只来看一下关键字：

| 方法关键字                                  | 含义          |
| -------------------------------------- | ----------- |
| getDeclaredMethods()                   | 获取所有的方法     |
| getReturnType()                        | 获得方法的放回类型   |
| getParameterTypes()                    | 获得方法的传入参数类型 |
| getDeclaredMethod("方法名",参数类型.class,……) | 获得特定的方法     |
|                                        |             |
| 构造方法关键字                                | 含义          |
| getDeclaredConstructors()              | 获取所有的构造方法   |
| getDeclaredConstructor(参数类型.class,……)  | 获取特定的构造方法   |
|                                        |             |
| 父类和父接口                                 | 含义          |
| getSuperclass()                        | 获取某类的父类     |
| getInterfaces()                        | 获取某类实现的接口   |

