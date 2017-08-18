---
title: Java 泛型
date: 2017-08-18 15:29:14
tags: [java]
---
# 基础定义
泛型，即“参数化类型”。定义方法时有形参，调用方法时传递实参，参数化类型就是将类型由原来的具体类型参数化，类似与方法中的变量参数，此时类型也定义成参数形式(可以称位类型参数)，然后在使用/调用时传入具体的参数(类型实参)。
<!-- more -->

```
public class GenericTest {

    public static void main(String[] args) {
        /*
        List list = new ArrayList();
        list.add("qqyumidi");
        list.add("corn");
        list.add(100);
        */

        List<String> list = new ArrayList<String>();
        list.add("qqyumidi");
        list.add("corn");
        //list.add(100);   // 1  提示编译错误

        for (int i = 0; i < list.size(); i++) {
            String name = list.get(i); // 2
            System.out.println("name:" + name);
        }
    }
}
```
采用泛型写法后，在//1处想加入一个Integer类型的对象时会出现编译错误，通过List<String>，直接限定了list集合中只能含有String类型的元素，从而在//2处无须进行强制类型转换，因为此时，集合能够记住元素的类型信息，编译器已经能够确认它是String类型了。
结合上面的泛型定义，我们知道在List<String>中，String是类型实参，也就是说，相应的List接口中肯定含有类型形参。且get()方法的返回结果也直接是此形参类型（也就是对应的传入的类型实参）。下面就来看看List接口的的具体定义：
```
public interface List<E> extends Collection<E> {

    int size();

    boolean isEmpty();

    boolean contains(Object o);

    Iterator<E> iterator();

    Object[] toArray();

    <T> T[] toArray(T[] a);

    boolean add(E e);

    boolean remove(Object o);

    boolean containsAll(Collection<?> c);

    boolean addAll(Collection<? extends E> c);

    boolean addAll(int index, Collection<? extends E> c);

    boolean removeAll(Collection<?> c);

    boolean retainAll(Collection<?> c);

    void clear();

    boolean equals(Object o);

    int hashCode();

    E get(int index);

    E set(int index, E element);

    void add(int index, E element);

    E remove(int index);

    int indexOf(Object o);

    int lastIndexOf(Object o);

    ListIterator<E> listIterator();

    ListIterator<E> listIterator(int index);

    List<E> subList(int fromIndex, int toIndex);
}

```

我们可以看到，在List接口中采用泛型化定义之后，<E>中的E表示类型形参，可以接收具体的类型实参，并且此接口定义中，凡是出现E的地方均表示相同的接受自外部的类型实参。
自然的，ArrayList作为List接口的实现类，其定义形式是：
```
public class ArrayList<E> extends AbstractList<E> 
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable {
    
    public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }
    
    public E get(int index) {
        rangeCheck(index);
        checkForComodification();
        return ArrayList.this.elementData(offset + index);
    }
    
    //...省略掉其他具体的定义过程

}
```

# 自定义泛型接口和泛型类
泛型类中的类型参数几乎可以用于任何可以使用接口名、类名的地方，下面的代码示例展示了 JDK 5.0 中集合框架中的 Map 接口的定义的一部分：
```
public interface Map<K, V> {
    public void put(K key, V value);
    public V get(K key);
}
```
当声明或者实例化一个泛型的对象时，必须指定类型参数的值：
Map<String, String> map = newHashMap<String, String>();
对于常见的泛型模式，推荐的名称是：
 
K ——键，比如映射的键。
V ——值，比如 List 和 Set 的内容，或者 Map 中的值。
E ——异常类。
T ——泛型。

```
package Generics;

import java.util.Random;

interface Generator<T> {
    public T next();
}

class Coffee{
    public String toString(){
        return getClass().getSimpleName();
    }
}

class Mocha extends Coffee{}
class Cappuccino extends Coffee{}
class Breve extends Coffee{}
class Latte extends Coffee{}

class CoffeeGenerator implements Generator<Coffee>{ //T为Coffee
    private static Random rand = new Random(47);
    private Class[] types = {Latte.class, Mocha.class, Cappuccino.class, Breve.class};
    public Coffee next(){ //T为Coffee
        try {
            return (Coffee)
                    types[rand.nextInt(types.length)].newInstance();
        } catch (Exception e) {
            throw new RuntimeException(e);
        } 
    }
}

public class InterfaceGenTest {
    public static void main(String[] args) {
        CoffeeGenerator gen = new CoffeeGenerator();
        for(int i=0; i<4; i++){
            System.out.println(gen.next());
        }
    }
}
/*Cappuccino
Mocha
Cappuccino
Latte*/
```

# 自定义泛型方法
泛型方法：泛型方法使得该方法能独立于类而产生变化。以下是一个基本的指导原则：无论何时，只要你能做到，你就应该尽量使用泛型方法。也就是说，如果使用泛型方法可以取代将整个类泛型化，那么就应该只使用泛型方法，因为它可以使事情更清楚明白。另外，对于一个static的方法而言，无法访问泛型类的类型参数。所以，如果static方法需要使用泛型能力，就必须使其成为泛型方法。
    要定义泛型方法，只需将泛型参数列表置于返回值之前，就像下面这样：
```
package Generics;

public class GenericMethods {
//当方法操作的引用数据类型不确定的时候，可以将泛型定义在方法上
    public <T> void f(T x){
        System.out.println(x.getClass().getName());
    }
    public static void main(String[] args) {
        GenericMethods gm = new GenericMethods();
        gm.f(99);
        gm.f("掌上洪城");
        gm.f(new Integer(99));
        gm.f(18.88);
        gm.f('a');
        gm.f(gm);
    }
}
/* 输出结果：
java.lang.Integer
java.lang.String
java.lang.Integer
java.lang.Double
java.lang.Character
Generics.GenericMethods
 */
```

## 可变参数与泛型方法

泛型方法与可变参数列表能很好地共存：
```
package Generics;

import java.util.ArrayList;
import java.util.List;

public class GenericVarargs {
    public static <T> List<T> makeList(T... args){
        List<T> result = new ArrayList<T>();
        for(T item:args)
            result.add(item);
        return result;       
    }
    public static void main(String[] args) {
        List ls = makeList("A");
        System.out.println(ls);
        ls = makeList("A","B","C");
        System.out.println(ls);
        ls = makeList("ABCDEFGHIJKLMNOPQRSTUVWXYZ".split(""));
        System.out.println(ls);
    }
}
/*
[A]
[A, B, C]
[A, B, C, D, E, F, G, H, I, J, K, L, M, N, O, P, Q, R, S, T, U, V, W, X, Y, Z]
*/
```
静态方法上的泛型：静态方法无法访问类上定义的泛型。如果静态方法操作的引用数据类型不确定的时候，必须要将泛型定义在方法上。
```
public static<Q> void function(Q t) {
    System.out.println("function:"+t);
}
```


# 泛型统配符
```
public class GenericTest {

    public static void main(String[] args) {

        Box<String> name = new Box<String>("corn");
        System.out.println("name:" + name.getData());
    }

}

class Box<T> {

    private T data;

    public Box() {

    }

    public Box(T data) {
        this.data = data;
    }

    public T getData() {
        return data;
    }

}
```

类型通配符一般是使用 ? 代替具体的类型实参。注意了，此处是类型实参，而不是类型形参！且Box<?>在逻辑上是Box<Integer>、Box<Number>...等所有Box<具体类型实参>的父类。由此，我们依然可以定义泛型方法，来完成此类需求。
```
public class GenericTest {

    public static void main(String[] args) {

        Box<String> name = new Box<String>("corn");
        Box<Integer> age = new Box<Integer>(712);
        Box<Number> number = new Box<Number>(314);

        getData(name);
        getData(age);
        getData(number);
    }

    public static void getData(Box<?> data) {
        System.out.println("data :" + data.getData());
    }

}
```

类型通配符上限通过形如Box<? extends Number>形式定义，相对应的，类型通配符下限为Box<? super Number>形式，其含义与类型通配符上限正好相反。

# 泛型的应用

# 注意事项
1. Java中没有所谓的泛型数组一说
2. 泛型到底代表什么类型取决于调用者传入的类型，如果没传，默认是Object类型；
3. 使用带泛型的类创建对象时，等式两边指定的泛型必须一致；
    原因：编译器检查对象调用方法时只看变量，然而程序运行期间调用方法时就要考虑对象具体类型了；
4. 等式两边可以在任意一边使用泛型，在另一边不使用(考虑向后兼容)；
    ArrayList<String>al = new ArrayList<Object>();  //错
    //要保证左右两边的泛型具体类型一致就可以了，这样不容易出错。

    ArrayList<?extends Object> al = new ArrayList<String>();
    al.add("aa");  //错
    //因为集合具体对象中既可存储String，也可以存储Object的其他子类，所以添加具体的类型对象不合适，类型检查会出现安全问题。 ? extendsObject 代表Object的子类型不确定，怎么能添加具体类型的对象呢？

    public static voidmethod(ArrayList<? extends Object> al) {
    al.add("abc");  //错
    //只能对al集合中的元素调用Object类中的方法，具体子类型的方法都不能用，因为子类型不确定。
5. 所有的标准集合接口都是泛型化的—— Collection<V>、List<V>、Set<V> 和 Map<K,V>。类似地，集合接口的实现都是用相同类型参数泛型化的，所以HashMap<K,V> 实现 Map<K,V> 等。
6. Java泛型中的标记符含义： 
 E - Element (在集合中使用，因为集合中存放的是元素)
 T - Type（Java 类）
 K - Key（键）
 V - Value（值）
 N - Number（数值类型）
 ? - 表示不确定的java类型
 S、U、V  - 2nd、3rd、4th types

