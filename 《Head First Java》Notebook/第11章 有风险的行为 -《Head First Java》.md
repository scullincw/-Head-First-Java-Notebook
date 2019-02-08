>Java的异常处理机制是简捷、轻量化的执行期间例外状况处理方式，它让你能够将处理错误状况的程序代码摆在一个容易阅读的位置。
>如果你知道调用某个方法可能会有异常状况，你就可以预先准备好对问题的处理程序。

## 如何抛出和处理异常
方法可以用`throw`关键字抛出异常对象。**异常**是一种`Exception`类型的对象，除了可以被抛出外，并没有什么特别的。

```public void Foo( ) throws new SomeException```

用`try/catch`块告诉编译器调用的方法会有风险，并且准备好要处理它。把有风险的程序放在`try`块，然后用`catch`块摆放异常状况的处理程序

`fianlly`块是用来存放不管有没有异常都得执行的程序：

```java
try {
    ...
} catch (Exception e) {
    ...
} finally {
    ...
}
```

**如果`try`块失败了**，抛出异常，流程马上转移到`catch`块。当`catch`块完成时，会执行`fianlly`块。当`fianlly`完成时，继续执行其余部分。**如果`try`块成功**，流程会跳过`catch`块并移动到`fianlly`块，当`fianlly`完成时，继续执行其余部分。**如果`try`或`catch`块有`return`指令，`finally`还是会执行!** 流程会跳到`finally`让然后再回到`return`指令。

### 异常类的多态
可以把异常处理程序代码写成只有一个`catch`块以父型的`Exception`来捕获，因此就可以抓到任何可能被抛出的异常，**但是，这会搞不清哪里出错了**。所以我们**为每个需要单独处理的异常编写不同的`catch`块**。

在多个`catch`块时要从小排到大，因为JVM会从头开始往下找第一个符合范围的异常处理块，如果第一个就是`catch(Exception ex)`，则编译器会认为其余的都没有用：

```java
try {
    ...
} catch (TeeShirtException tex) {
    ...
} catch (ShirtException sex) {
    ...
} catch (ClothingException cex) {
    ...
}
```

## ducking（只抛出异常不处理）
方法可以`throw`异常而不`catch`。**方法抛出异常时，方法会从栈上被取出，而异常再度丢给栈上的方法，也就是调用方。**