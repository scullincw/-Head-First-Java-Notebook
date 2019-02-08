## 存储对象
**对象可以被序列化也可以展开。**对象有状态和行为两种属性。行为存在于类中，而状态存在于个别对象中。

在存储对象状态时：

* 如果只有自己写的Java程序会用到这些数据，就用**序列化(serialiazation)**，将被序列化的对象写到文件中。然后可以让你的程序去文件中读取序列化的对象并把它们展开。
* 如果数据需要被其他程序引用，写一个**纯文本文件**。用其他程序可以解析的特殊字符写到文件中。

## 将序列化对象写入文件
1. 创建出FileOutputStream

```java
//创建存取文件的FileOutputStream对象，如果文件不存在，它会自动被创建出来
FileOutputStream fileStream = new FileOutputStream("MyGame.ser");
```

2. 创建ObjectOutputStream

```java
//ObjectOutputStream能让你写入对象，但无法直接地连接文件，所以需要参数fileStream的指引
ObjectOutputStream os = new ObjectOutputStream(fileStream);
```

3. 写入对象

```java
//将变量所引用的对象序列化并写入MyGame.ser这个文件
os.writeObject(characterOne);
os.writeObject(characterTwo);
os.writeObject(characterThree);
```

4. 关闭ObjectOutputStream

```java
//关闭所关联的输出串流
os.close();
```

基本思路是：把`Object`写入`ObjectOutputStream`，**堆上的对象**变为**被序列化的对象**；被序列化的对象会被`ObjectOutputStream`输出到`FileOutputStream`，而`FileOutputStream`把被序列化的对象**当作字节处理**，输出到文件中。

>如果一个要被序列化的对象有**引用**其他对象的**实例变量**，这些被引用到的对象也会被序列化。

## 类的序列化
**如果要让类能够被序列化，就实现`Serializable`接口。**

>`Serializable`接口又被成为marker或tag类的标记用接口，因为此接口并没有任何方法需要实现的。它的唯一目的就是声明有实现它的类是可以被序列化的。

* 如果某个实例变量不能或不应该被序列化，就把它标记为`transient`（瞬时）的。在序列化过程中该变量就会被忽略。


## 解序列化(Deserialization)
1.创建FileInputStream

```java
FileInputStream fileStream = new FileInputStream("MyGame.ser");
//FileInputStream知道如何连接文件
```

2.创建ObjectInputStream

```java
ObjectInputStream os = new ObjectInputStream(fileStream);
//ObjectInputStream知道如何读取对象，但是要靠链接的fileStream提供文件存取
```

3.读取对象并转换对象类型

```java
//每次调用readObject()都会从stream中读出下一个对象，读取顺序与写入顺序相同，次数超过会抛出异常
GameCharacter elf = (GameCharacter) os.readObject();
GameCharacter troll = (GameCharacter) os.readObject();
GameCharacter magician = (GameCharacter) os.readObject();
```

4.关闭ObjectInputStream

```java
//FileInputStream会自动跟着关掉
os.close();
```

基本流程：MyGame.ser当作**字节流** -> `FileInputStream` -> 序列化的对象 -> `ObjectInputStream` ->  对象


### 解序列化的完整过程

1. 对象从`Stream`中读出来。
2. JVM通过存储的信息判断出对象的class类型。
3. JVM尝试寻找和加载对象的类。如果JVM找不到或无法加载该类，JVM会抛出异常。
4. 新的对象会被配置在堆上，但**构造函数不会执行**！因为执行构造函数会把对象的状态抹去变成全新的，而这不是我们想要的结果。我们需要的是对象回到存储时的状态。
5. 如果对象在继承树上有个不可序列化的祖先类，则该不可序列化类以及它之上的类的构造函数就会执行。一旦构造函数连锁启动之后将无法停止。也就是说，从第一个不可序列化的父类开始，全部都会重新初始状态。
6. 对象的实例变量会被还原成序列化时点点状态值。`transient`变量会被赋值为`null`、0或`false`。

## Version ID
如果已经序列化了一些对象，但是修改了类，这些对象解序列化时就会出现问题。

每当对象被序列化的同时，该对象都会被“盖”上一个**类的版本识别ID**，成为serialVersionUID，它是根据**类的结构信息**计算出来的。对象被解序列化时，如果在对象被序列化之后类有了不同的serialVersionUID，还原操作会失败。

* 如果想知道某个类的serialVersionUID，可以使用JDK里所带的serialver工具来查询。

## 写入文件
### 缓冲区

```java
BufferedWriter writer = new BufferedWriter(new FileWriter(aFile));
```

使用缓冲区比没有使用缓冲区的效率更高。你可以直接使用`FileWriter`，调用它的`write()`来写文件，但它每次都会直接写下去，每趟磁盘操作都比内存操作要花费更多时间。通过`BufferedWriter`和`FileWriter`的链接，`FileWriter`可以暂存一堆数据，然后到满的时候再实际写入磁盘，这样可以减少对磁盘操作的次数。

如果想强制缓冲区立即写入，只要调用`write.flush()`这个方法就可以要求缓冲区马上把内容写下去。

## 读取文件
从文本文件读取数据的基本思想是使用`File`对象来表示文件，以`FileReader`来执行实际的读取，并用`BufferReader`来读取更有效率。

**最常见的读取方式是以`while`循环来读取，一直到`readLine()`的结果为`null`为止。**

```java
import java.io.*;

class ReadAFile {
    public static void main (String[] args) {
    	try {
    		File myFile = new File("MyText.txt");
    		FileReader fileReader = new FileReader(myFile);
    		BufferReader reader = new BufferReader(fileReader);
    		
    		String line = null;
    		while ((line = reader.readLine()) != null) {
    			System.out.println(line);
    		}
    		reader.close();
    	} catch(Exception ex) {
    		ex.printStackTrace();
    	}
    }
}
```

