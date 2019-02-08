## 泛型
### 运用泛型的两种方法

1.使用定义在类声明的类型参数

```java
public class ArrayList<E> extends AbstractList<E> ... {
	public boolean add(E o)
	...
```

2.使用未定义在类声明的类型参数

```java
public <T extends Animal> void takeThing(ArrayList<T> list)
```


### 泛型中的多态

**注意**，这行程序

```java
public <T extends Animal> void takeThing(ArrayList<T> list)
```

与下面这个是不一样的：

```java
public void takeThing(ArrayList<Animal> list)
```

`<T extends Animal>`是方法声明的一部分，表示任何被声明为Animal或Animal的子型（如Cat或Dog）的`ArrayList`都是合法的。因此可以使用`ArrayList<Dog>`, `ArrayList<Car>`或`ArrayList<Animal>`来调用上面的方法。

但是，下面方法的参数是`ArrayList<Animal> list`，代表只有`ArrayList<Animal>`是合法的。也就是说第一个可以使用任何一种Animal的ArrayList，而第二个方法只能使用Animal的ArrayList。


### 泛型中`extends`关键词的含义

我们创建一个ArrayList装入String对象，然后对ArrayList排序：

```java
ArrayList<String> stringList = new ArrayList<String>();
...
Collections.sort(stringList);
```

显然以上代码是可以通过编译器的，但是将String改为不是primitive主数据类型的类型就不一定了，我们查看sort()方法的文档：

```java
public static <T extends Comparable<? super T>> void sort(List<T> list)
//Comparable<? super T>代表Comparable的参数类型必须是T或T的父型
```

可以看到T类型必须是Comparable，但是查看String的文档：

```java
public final class String extends Object implements Serializable, Comparable<String>, CharSequence
```

String并没有继承Comparable，只有实现，因为Comparable是一个接口，所以上面的extends不太合理？

实际上，在泛型声明时`extends`关键词代表"是一个..."，不管是接口还是类都能适用。


### Comparable和Comparator

`java.lang.Comparable`是一个接口，类通过实现compareTo()方法，使得该类的集合可以被排序：

```java
public interface Comparable<T> {
	int compareTo(T o);
}
```

如果需要让类的集合**以多种方式排序**，就需要使用`java.util.Comparator`类，这是一个独立的类。

```java
public interface Comparator<T> {
	int compare(T o1, T o2)
}
```

sort()方法有对应的重载方法：

```java
sort(List<T> list, Comparator<? super T> c)
```


## Set

`java.util.Collection`中有3个主要的**接口**：List, Set和Map。简单来说：

* List：**注重顺序。**是一种知道索引位置的集合。可以有多个元素引用相同对象。
* Set：**注重独一无二的性质。**是不允许重复的集合，不允许多个元素引用相同的对象。
* Map：**注重用key（键）和value（值）。**两个key可以引用相同的对象，但是key不能重复。


### 对象相等

* 引用相等性

	**堆上同一对象的两个引用是相等的。**如果对两个对象调用hashCode()，会得到相同的结果。想要知道两个引用是否相等，可以使用`==`来比较。
	
> hashCode()默认的行为会返回每个对象特有的序号（大部分的Java版本是依据内存位置计算此序号，所以不会有相同的hashcode）。
	
* 对象相等性

	如果要把堆上两个不同对象视为相等的，就必须覆盖过Object继承下来的hashCode()方法和equals()方法。因为上面所说的内存计算问题，所以我们必须覆盖过hashCode()方法才能确保两个对象有相同的hashcode


### hashCode()与equals()的相关规定

API文件对hashCode()与equals()的覆盖必须遵循的规则有：

1. 如果两个对象相等，则hashcode也必须相等
2. 如果两个对象相等，其中一个对象调用equals()必须返回true。也就是说，若`a.equals(b)'则`b.equals(a)`。
3. 如果两个对象有相同的hashcode值，它们不一定相等。 但若两个对象相等，它们的hashcode一定相等。
4. 因此若equals()被覆盖过，则hashCode()也必须被覆盖。

### HashSet

HashSet是一种不允许重复的哈希表。当我们把对象加入HashSet时，它会使用对象的hashcode值（[杂凑值](https://baike.baidu.com/item/%E6%9D%82%E5%87%91%E8%BF%90%E7%AE%97)，不是哈希值）判断对象加入的位置，若新对象的hashcode值与已经加入的对象的hashcode值均不相同，则HashSet认为新对象没有重复出现。

若找到了hashcode值相同的两个对象，会调用equals()来检查这两个对象是否真的相同。如果两者相同，HashSet就知道要加入的对象已经重复了，加入操作不会发生。此时不会抛出异常，但add()函数会返回`false`，表示新对象与集合中的某个项目是重复的。

### TreeSet

TreeSet同样不允许重复。使用TreeSet必须满足以下其中一个条件：

1. 集合中的元素必须实现了Comparable接口
2. 使用重载、取用Comparator参数的构造函数来创建TreeSet。


## 回到泛型：泛型与普通数组的对比

如果有下面一段程序：

```java
public void go() {
	Dog[] dogs = {new Dog(), new Dog(), new Dog()};
	takeAnimals(dogs);
}

public void takeAnimals(Animal[] animals) {
	animals[0] = new Cat();
}
```

这一段程序可以通过编译，但在运行时会抛出`ArrayStoreException`异常，因为**数组的类型是在运行期间检查的，但集合的类型是在编译期间就进行检查的**。显然，**我们希望自己的程序错误在编译期间就被发现**。

要想让这种错误在编译时期就被发现，一个方法是将普通数组改为集合，并且运用**泛型**和**万用字符`?`**：

```java
public void go() {
	ArrayList<Dog> dogs = new ArrayList<Dog>();
	dogs.add(new Dog());
	dogs.add(new Dog());
	dogs.add(new Dog());
	takeAnimals(dogs);
}

public void takeAnimals(ArrayList<? extends Animal> animals) {
	animals[0] = new Cat();
}
```

上面的程序将不能通过编译器，因为带有`<?>`声明时，编译器不会让你加入任何东西到集合中。

### 万用字符的使用

我们可以`T`代替`?`，下面两行的效果是一样的，但是`T`更加方便：

```java
public <T extends Animal> void takeThing(ArrayList<T> one, ArrayList<T> two)

public void takeThing(ArrayList<? extends Animal>one, ArrayList<? extends Animal> two)
```
