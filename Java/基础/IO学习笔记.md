# IO（Java.io）

## 输入

### 字节流

`InputStream`：所有的字节输入流的基类。

`FileInputStream`：字节输入流对象，可直接指定文件路径，可以直接读取单字节数据，也可以读取至字节数组中。通常会配合 `BufferedInputStream`使用。

`DataInputStream`：用于读取指定类型数据，不能单独使用，必须结合其它流，比如 `FileInputStream` 。

`ObjectInputStream`：用于从输入流中读取 Java 对象（反序列化），`ObjectOutputStream` 用于将对象写入到输出流(序列化)。用于序列化和反序列化的类必须实现 `Serializable` 接口，对象中如果有属性不想被序列化，使用 `transient` 修饰。



### 字符流

`Reader`：所有的字符输入流的基类，`Reader` 用于读取文本。

`InputStreamReader` ：字节流转换为字符流的桥梁，其子类 `FileReader` 是基于该基础上的封装，可以直接操作字符文件。

## 输出

`Writer`：所有字符输出流的父类。

`OutputStreamWriter` ：字符流转换为字节流的桥梁，其子类 `FileWriter` 是基于该基础上的封装，可以直接将字符写入到文件。

### 字节流

`OutputStream`：所有的字节输入流的基类

`FileOutputStream`：最常用的字节输出流对象，可直接指定文件路径，可以直接输出单字节数据，也可以输出指定的字节数组。

`BufferedOutputStream`：类似于`BufferedInputStream`。

`DataOutputStream`：类似于`DataInputStream`。

`ObjectOutputStream`：类似于`ObjectInputStream`。

### 字符流

`Writer`：所有的字符输入流的基类

## 为什么分为字节流和字符流？

字符流由虚拟机转换字节码得到，**耗时**；编码未知时**容易乱码**。

**音频文件、图片**等媒体文件用字节流比较好，如果涉及到**字符**的话使用字符流比较好。

字符流默认采用的是 `Unicode` 编码，`utf8` :英文占 1 字节，中文占 3 字节，`unicode`：任何字符都占 2 个字节，`gbk`：英文占 1 字节，中文占 2 字节。

## 为什么需要字节缓冲流？

IO操作**消耗性能**，缓冲流能够**将数据存入缓冲区**，一次性读取/写入多个字节，**避免**频繁的IO操作，**提升效率**。

采用了**装饰器模式**来增强 `InputStream` 和`OutputStream`子类对象的功能。

#### 性能差别：

主要体现在调用 `write(int b)` 和 `read()` 这两个一次只读取一个字节的方法的时候。由于字节缓冲流内部有缓冲区（字节数组），因此，字节缓冲流会先将读取到的字节存放在缓存区，大幅减少 IO 次数，提高读取效率。如果是调用 `read(byte b[])` 和 `write(byte b[], int off, int len)` 这两个写入一个字节数组的方法的话，只要字节数组的大小合适，两者的性能差距其实不大，基本可以忽略。

#### BufferedInputStream

内部维护了一个缓冲区，这个缓冲区实际就是一个字节数组，缓冲区的大小默认为 **8192** 字节。

#### BufferedOutputStream

类似于 `BufferedInputStream` ，`BufferedOutputStream` 内部也维护了一个缓冲区，并且，这个缓存区的大小也是 **8192** 字节。

## 字符缓冲流

`BufferedReader` （字符缓冲输入流）和 `BufferedWriter`（字符缓冲输出流）类似于 `BufferedInputStream`（字节缓冲输入流）和`BufferedOutputStream`（字节缓冲输入流），内部都维护了一个字节数组作为缓冲区。不过，前者主要是用来操作字符信息。

## 设计模式

**装饰器（Decorator）模式**

