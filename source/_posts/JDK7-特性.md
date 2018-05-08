---
title: JDK7 特性
date: 2018-05-08 10:59:28
tags: [JAVA,JDK7]
---

* switch中可以使用字串了

~~~
	String s = "test";
    switch (s) {
    case "test":
    System.out.println("test");
    case "test1":
    System.out.println("test1");
    break;
    default:
    System.out.println("break");
    break;
    }
~~~

<!-- more -->

* 泛型实例化类型自动推断：<>"这个玩意儿的运用List<String> tempList = new ArrayList<>();
* 自定义自动关闭类：只要实现该接口（AutoCloseable ），在该类对象销毁时自动调用close方法，你可以在close方法关闭你想关闭的资源，例子如下
~~~
class TryClose implements AutoCloseable {

 @Override
 public void close() throw Exception {
  System.out.println(" Custom close method … close resources ");
 }
}
//请看jdk自带类BufferedReader如何实现close方法（当然还有很多类似类型的类）

  	public void close() throws IOException {
        synchronized (lock) {
            if (in == null)
                return;
            in.close();
            in = null;
            cb = null;
        }
    }
~~~
* 数值可加下划线：下划线不代表任何意义


  ~~~
  int one_million = 1_000_000;
  ~~~

  ​

* ​