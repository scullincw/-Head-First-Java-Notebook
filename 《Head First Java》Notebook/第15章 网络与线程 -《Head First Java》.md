## 聊天程序概述
>客户端必须认识服务器。服务器必须认识所有客户端。

工作方式：
1. 客户端连接到服务器。
2. 服务器建立连接并把所有客户端加到来宾清单中。
3. 另外一个用户链接上来。
4. 用户A发送信息到聊天服务器上。
5. 服务器将信息送给所有的来宾。

## 连接、传送与接受
### 建立Socket连接
**`java.net.Socket`是个代表两台机器之间网络连接的对象。**Socket连接的建立代表两台机器之间存有对方的信息，包括网络地址和TCP的端口号。

```java
Socket chatSocket = new Socket("196.164.1.103", 5000);
//196.164.1.103是IP地址，5000是TCP的端口号
```

>TCP端口只是一个16位宽，用来识别服务器上特定程序的数字。

### 从Socket读取数据

1.（客户端）建立对服务器的Socket连接

```java
Socket chatSocket = new Socket("127.0.0.1", 5000);
//127.0.0.1这个IP地址就是本机IP地址，所以可以在自己这台计算机上同时测试客户端和服务器
```

2.建立连接到Socket上低层输入串流的InputStreamReader

```java
InputStreamReader stream = new InputStreamReader(chatSocket.getInputStream());
//InputStreamReader是个低层和高层串流间的桥梁，创建的对象从Socket取得输入串流
```

3.建立BufferedReader来读取

```java
//将BufferedReader链接到InputStreamReader
BufferedReader reader = new BufferedReader(stream);
String message = reader.readLine();
```

### 写数据到Socket上

1.对服务器建立Socket连接

```java
Socket chatSocket = new Socket("127.0.0.1", 5000);
//127.0.0.1这个IP地址就是本机IP地址，所以可以在自己这台计算机上同时测试客户端和服务器
```

2.建立链接到Socket的PrintWriter

```java
PrintWriter writer = new PrintWriter(chatSocket.getInputStream());
//PrintWriter是字符数据和字节间的转换桥梁，可以衔接String和Socket两端
```

3.写入数据

```java
writer.println("message to send");
writer.print("another message");
```

### 编写服务器程序

**服务器应用程序需要2个`Socket`**，一个用来等待用户请求，另一个用来和用户tong xun

## 多线程
线程代表独立的执行空间(stack)。