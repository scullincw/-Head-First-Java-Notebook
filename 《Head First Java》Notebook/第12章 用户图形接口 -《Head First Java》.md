## 创建GUI的基本步骤
1. 创建frame:```JFrame frame = new JFrame();```
2. 创建widget:```JButton button = new JButton("click me");```
3. 把widget加到frame上:```frame.getContentPane().add(button);```
4. 显示出来:```frame.setSize(300,300); frame.setVisible(true);```

## 事件处理
事件处理的思路是需要这两项：
1. 按钮被按下时要执行的方法；
2. 检测按钮被按下的方法，换句话说，就是按钮的感应装置。

### 监听、事件源与事件
>在Java上，取得与处理用户操作事件的过程称为even-handling。
>Swing的GUI组件是事件的来源。以Java的术语来说，**事件来源**是个可以将用户操作（点击鼠标、案件、关闭窗口等）转换成事件的**对象**。对Java而言，事件几乎都是以对象来表示。它会是某种事件类的对象。

**事件源**（例如按钮）会在用户做出相关动作时（按下按钮）产生**事件对象**。事件对象携带事件信息，调用接口上的方法时把事件对象当作参数传进去，它会把事件的信息带给监听者。想要接受某类事件（如`MouseEvent`)就要实现这个类型的接口（`MouseListener`)，必须把接口中所有的方法实现出来。

## 绘图
要想在屏幕上放上自己的图像，就自己创建出有绘图功能的widget，把widget放在frame上。

创建`JPanel`的子类并覆盖掉`paintComponent()`方法。

所有绘图程序代码都在`paintComponent()`里面。可以把这个方法想象成会被系统告知要把自己画出来的方法。当你的panel所处的frame显示的时候，`paintComponent()`就会被调用。如果用户缩小window或者选择最小化，JVM也会知道要调用它来重新绘制。任何时候JVM发现有必要重绘都会这么做。

>但是你自己不会调用这个方法！它的参数是跟实际屏幕有关的`Graphics`对象，你无法取得这个对象，它必须由系统来交给你。然而，你还是可以调用`repaint()`来要求系统重新绘制显示装置，然后产生`paintComponent()`的调用。

`paintComponent()`的参数被声明为`Graphics`类型。

```java
public void paintComponent(Graphics g) { }
```
实际上，由g参数所引用的对象是个`Graphics2D`的实例。有些在`Graphics2D`引用上可以做的事情不能在`Graphics`引用上做。因此，如果要调用`Graphics2D`类的方法，就不能直接使用g参数，而是首先将它转换成`Graphics2D`变量：

```java
Graphics2D g2d = (Graphics2D) g;
```
## 内部类
内部类可以自由地存取它所属的外部类的内容，如外部的方法或变量。

内部类的实例一定会绑在外部类的实例上。也就是说，想要调用一个内部类实例的方法或变量，必须先创建一个外部类的实例，然后创建内部类的实例。

如果想从外部类以外的程序代码来初始内部实例，要用特殊的语法：

```java
class Foo {
    public void static main(String[] args) {
    	MyOuter outerObj = new MyOuter();
    	MyOuter.MyInner innerObj = outerObj.new MyInner();
    }
}
```
