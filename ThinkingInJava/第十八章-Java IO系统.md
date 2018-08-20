# 第十八章 Java I/O系统

### 18.1 File类

FIle类既能代表一个特定文件的名称，又能代表一个目录下的一组文件的名称

主要用来管理文件目录

### 18.2 输入和输出

输入流都有read()基本方法，读取单字节或字节数组；输出流都有write()基本方法，用于写单字节或字节数组

很少使用单一的类创建流对象，而是通过叠合多个对象提供所期望的功能

### 18.3 添加属性和有用的接口

FilterInputStream和FilterOutputStream是用来提供装饰器接口以控制特定输入流和输出流的两个类

### 18.4 Reader和Writer

提供兼容Unicode与面向字符的I/O功能（无法面向字节）

### 18.5 自我独立的类：RandomAccessFile

适用于大小已知的记录组成的文件，可以用seek()将记录从一处转移到另一处，然后读取或者修改记录。

继承自Object

### 18.6 I/O流的典型使用方式

##### 18.6.1 缓冲输入文件

带缓冲的BufferdReader提供realLine()方法

##### 18.6.2 从内存输入

##### 18.6.3 格式化的内存输入

读取格式化数据用DataInputStream

##### 18.6.4 基本的文件输出

FileWriter对象可以向文件写入数据。通常用BufferedWriter将其包装起来用以缓冲输出

##### 18.6.5 存储和恢复数据

##### 18.6.6 读写随机访问文件

RandomAccessFile只实现了DataInput和DataOutput接口。不支持装饰，有效地与I/O继承层次的其他部分实现了分离

##### 18.6.7 管道流

用于多线程场景任务之间的通信

### 18.7 文件读写的实用工具

### 18.8 标准I/O

##### 18.8.1 从标准输入中读取

System.in  System.out  System.err

```java
public class Echo {
    public static void main(String[] args) throws IOException {
        BufferedReader stdin = new BufferedReader(new InputStreamReader(System.in));
        String s;
        while ((s = stdin.readLine()) != null && s.length() != 0)
            System.out.println(s);
    }
}
```

##### 18.8.2 将System.out转换成PrintWriter

##### 18.8.3 标准I/O重定向

setIn(InputStream)  setOut(PrintStream)  setErr(PrintStream)

### 18.9 进程控制

### 18.10 新I/O

引进NIO目的：提高速度

速度提高来自于所使用的结构更接近于操作系统执行I/O的方式：通道和缓冲器（煤矿的比喻，通道是包含煤层（数据）的矿藏，而缓冲器则是派送到矿藏的卡车）

唯一直接与通道交互的缓冲器是ByteBuffer。其没办法输出或读取对象

##### 18.10.1 流与块

I/O 与 NIO 最重要的区别是数据打包和传输的方式，I/O 以流的方式处理数据，而 NIO 以块的方式处理数据。

面向流的 I/O 一次处理一个字节数据，一个输入流产生一个字节数据，一个输出流消费一个字节数据。为流式数据创建过滤器非常容易，链接几个过滤器，以便每个过滤器只负责复杂处理机制的一部分。不利的一面是，面向流的 I/O 通常相当慢。

面向块的 I/O 一次处理一个数据块，按块处理数据比按流处理数据要快得多。但是面向块的 I/O 缺少一些面向流的 I/O 所具有的优雅性和简单性。

I/O 包和 NIO 已经很好地集成了，java.io.* 已经以 NIO 为基础重新实现了，所以现在它可以利用 NIO 的一些特性。例如，java.io.* 包中的一些类包含以块的形式读写数据的方法，这使得即使在面向流的系统中，处理速度也会更快。

##### 18.10.2 通道与缓冲区

###### 1.通道

通道 Channel 是对原 I/O 包中的流的模拟，可以通过它读取和写入数据。

通道与流的不同之处在于，流只能在一个方向上移动，(一个流必须是 InputStream 或者 OutputStream 的子类)，而通道是双向的，可以用于读、写或者同时用于读写。

通道包括以下类型：

- FileChannel：从文件中读写数据；
- DatagramChannel：通过 UDP 读写网络中数据；
- SocketChannel：通过 TCP 读写网络中数据；
- ServerSocketChannel：可以监听新进来的 TCP 连接，对每一个新进来的连接都会创建一个 SocketChannel。

###### 2. 缓冲区

发送给一个通道的所有数据都必须首先放到缓冲区中，同样地，从通道中读取的任何数据都要先读到缓冲区中。也就是说，不会直接对通道进行读写数据，而是要先经过缓冲区。

缓冲区实质上是一个数组，但它不仅仅是一个数组。缓冲区提供了对数据的结构化访问，而且还可以跟踪系统的读/写进程。

缓冲区包括以下类型：

- ByteBuffer
- CharBuffer
- ShortBuffer
- IntBuffer
- LongBuffer
- FloatBuffer
- DoubleBuffer

##### 18.10.3 缓冲区状态变量

- capacity：最大容量；
- position：当前已经读写的字节数；
- limit：还可以读写的字节数。

状态变量的改变过程举例：

① 新建一个大小为 8 个字节的缓冲区，此时 position 为 0，而 limit = capacity = 8。capacity 变量不会改变，下面的讨论会忽略它。

[![img](https://github.com/CyC2018/Interview-Notebook/raw/master/pics/1bea398f-17a7-4f67-a90b-9e2d243eaa9a.png)](https://github.com/CyC2018/Interview-Notebook/blob/master/pics/1bea398f-17a7-4f67-a90b-9e2d243eaa9a.png)

 

② 从输入通道中读取 5 个字节数据写入缓冲区中，此时 position 移动设置为 5，limit 保持不变。

[![img](https://github.com/CyC2018/Interview-Notebook/raw/master/pics/80804f52-8815-4096-b506-48eef3eed5c6.png)](https://github.com/CyC2018/Interview-Notebook/blob/master/pics/80804f52-8815-4096-b506-48eef3eed5c6.png)

 

③ 在将缓冲区的数据写到输出通道之前，需要先调用 flip() 方法，这个方法将 limit 设置为当前 position，并将 position 设置为 0。

[![img](https://github.com/CyC2018/Interview-Notebook/raw/master/pics/952e06bd-5a65-4cab-82e4-dd1536462f38.png)](https://github.com/CyC2018/Interview-Notebook/blob/master/pics/952e06bd-5a65-4cab-82e4-dd1536462f38.png)

 

④ 从缓冲区中取 4 个字节到输出缓冲中，此时 position 设为 4。

[![img](https://github.com/CyC2018/Interview-Notebook/raw/master/pics/b5bdcbe2-b958-4aef-9151-6ad963cb28b4.png)](https://github.com/CyC2018/Interview-Notebook/blob/master/pics/b5bdcbe2-b958-4aef-9151-6ad963cb28b4.png)

 

⑤ 最后需要调用 clear() 方法来清空缓冲区，此时 position 和 limit 都被设置为最初位置。

[![img](https://github.com/CyC2018/Interview-Notebook/raw/master/pics/67bf5487-c45d-49b6-b9c0-a058d8c68902.png)](https://github.com/CyC2018/Interview-Notebook/blob/master/pics/67bf5487-c45d-49b6-b9c0-a058d8c68902.png)

 

##### 18.10.4 文件 NIO 实例

以下展示了使用 NIO 快速复制文件的实例：

```java
public static void fastCopy(String src, String dist) throws IOException
{
    FileInputStream fin = new FileInputStream(src);      /* 获得源文件的输入字节流 */
    FileChannel fcin = fin.getChannel();                 /* 获取输入字节流的文件通道 */
    FileOutputStream fout = new FileOutputStream(dist);  /* 获取目标文件的输出字节流 */
    FileChannel fcout = fout.getChannel();               /* 获取输出字节流的通道 */
    ByteBuffer buffer = ByteBuffer.allocateDirect(1024); /* 为缓冲区分配 1024 个字节 */
    while (true) {
        int r = fcin.read(buffer);                       /* 从输入通道中读取数据到缓冲区中 */
        if (r == -1) {                                   /* read() 返回 -1 表示 EOF */
            break;
        }
        buffer.flip();                                   /* 切换读写 */
        fcout.write(buffer);                             /* 把缓冲区的内容写入输出文件中 */
        buffer.clear();                                  /* 清空缓冲区 */
    }
}
```

##### 18.10.5 选择器

一个线程 Thread 使用一个选择器 Selector 通过轮询的方式去监听多个通道 Channel 上的事件，从而让一个线程就可以处理多个事件。

因为创建和切换线程的开销很大，因此使用一个线程来处理多个事件而不是一个线程处理一个事件具有更好的性能。

[![img](https://github.com/CyC2018/Interview-Notebook/raw/master/pics/4d930e22-f493-49ae-8dff-ea21cd6895dc.png)](https://github.com/CyC2018/Interview-Notebook/blob/master/pics/4d930e22-f493-49ae-8dff-ea21cd6895dc.png)

 

###### 1. 创建选择器

```java
Selector selector = Selector.open();
```

###### 2. 将通道注册到选择器上

```java
ServerSocketChannel ssChannel = ServerSocketChannel.open();
ssChannel.configureBlocking(false);
ssChannel.register(selector, SelectionKey.OP_ACCEPT);
```

通道必须配置为非阻塞模式，否则使用选择器就没有任何意义了，因为如果通道在某个事件上被阻塞，那么服务器就不能响应其它事件，必须等待这个事件处理完毕才能去处理其它事件，显然这和选择器的作用背道而驰。

在将通道注册到选择器上时，还需要指定要注册的具体事件，主要有以下几类：

- SelectionKey.OP_CONNECT
- SelectionKey.OP_ACCEPT
- SelectionKey.OP_READ
- SelectionKey.OP_WRITE

它们在 SelectionKey 的定义如下：

```java
public static final int OP_READ = 1 << 0;
public static final int OP_WRITE = 1 << 2;
public static final int OP_CONNECT = 1 << 3;
public static final int OP_ACCEPT = 1 << 4;
```

可以看出每个事件可以被当成一个位域，从而组成事件集整数。例如：

```java
int interestSet = SelectionKey.OP_READ | SelectionKey.OP_WRITE;
```

###### 3. 监听事件

```java
int num = selector.select();
```

使用 select() 来监听事件到达，它会一直阻塞直到有至少一个事件到达。

###### 4. 获取到达的事件

```java
Set<SelectionKey> keys = selector.selectedKeys();
Iterator<SelectionKey> keyIterator = keys.iterator();
while (keyIterator.hasNext()) {
    SelectionKey key = keyIterator.next();
    if (key.isAcceptable()) {
        // ...
    } else if (key.isReadable()) {
        // ...
    }
    keyIterator.remove();
}
```

###### 5. 事件循环

因为一次 select() 调用不能处理完所有的事件，并且服务器端有可能需要一直监听事件，因此服务器端处理事件的代码一般会放在一个死循环内。

```java
while (true) {
    int num = selector.select();
    Set<SelectionKey> keys = selector.selectedKeys();
    Iterator<SelectionKey> keyIterator = keys.iterator();
    while (keyIterator.hasNext()) {
        SelectionKey key = keyIterator.next();
        if (key.isAcceptable()) {
            // ...
        } else if (key.isReadable()) {
            // ...
        }
        keyIterator.remove();
    }
}
```

##### 18.10.6 套接字 NIO 实例

```java
public class NIOServer
{
    public static void main(String[] args) throws IOException
    {
        Selector selector = Selector.open();

        ServerSocketChannel ssChannel = ServerSocketChannel.open();
        ssChannel.configureBlocking(false);
        ssChannel.register(selector, SelectionKey.OP_ACCEPT);

        ServerSocket serverSocket = ssChannel.socket();
        InetSocketAddress address = new InetSocketAddress("127.0.0.1", 8888);
        serverSocket.bind(address);

        while (true) {
            selector.select();
            Set<SelectionKey> keys = selector.selectedKeys();
            Iterator<SelectionKey> keyIterator = keys.iterator();
            while (keyIterator.hasNext()) {
                SelectionKey key = keyIterator.next();
                if (key.isAcceptable()) {
                    ServerSocketChannel ssChannel1 = (ServerSocketChannel) key.channel();
                    // 服务器会为每个新连接创建一个 SocketChannel
                    SocketChannel sChannel = ssChannel1.accept();
                    sChannel.configureBlocking(false);
                    // 这个新连接主要用于从客户端读取数据
                    sChannel.register(selector, SelectionKey.OP_READ);
                } else if (key.isReadable()) {
                    SocketChannel sChannel = (SocketChannel) key.channel();
                    System.out.println(readDataFromSocketChannel(sChannel));
                    sChannel.close();
                }
                keyIterator.remove();
            }
        }
    }

    private static String readDataFromSocketChannel(SocketChannel sChannel) throws IOException {
        ByteBuffer buffer = ByteBuffer.allocate(1024);
        StringBuilder data = new StringBuilder();
        while (true) {
            buffer.clear();
            int n = sChannel.read(buffer);
            if (n == -1) {
                break;
            }
            buffer.flip();
            int limit = buffer.limit();
            char[] dst = new char[limit];
            for (int i = 0; i < limit; i++) {
                dst[i] = (char) buffer.get(i);
            }
            data.append(dst);
            buffer.clear();
        }
        return data.toString();
    }
}
```

```java
public class NIOClient
{
    public static void main(String[] args) throws IOException 
    {
        Socket socket = new Socket("127.0.0.1", 8888);
        OutputStream out = socket.getOutputStream();
        String s = "hello world";
        out.write(s.getBytes());
        out.close();
    }
}
```

##### 18.10.7 内存映射文件

内存映射文件 I/O 是一种读和写文件数据的方法，它可以比常规的基于流或者基于通道的 I/O 快得多。

只有文件中实际读取或者写入的部分才会映射到内存中。

现代操作系统一般会根据需要将文件的部分映射为内存的部分，从而实现文件系统。Java 内存映射机制只不过是在底层操作系统中可以采用这种机制时，提供了对该机制的访问。

向内存映射文件写入可能是危险的，仅只是改变数组的单个元素这样的简单操作，就可能会直接修改磁盘上的文件。修改数据与将数据保存到磁盘是没有分开的。

下面代码行将文件的前 1024 个字节映射到内存中，map() 方法返回一个 MappedByteBuffer，它是 ByteBuffer 的子类。因此，您可以像使用其他任何 ByteBuffer 一样使用新映射的缓冲区，操作系统会在需要时负责执行映射。

```java
MappedByteBuffer mbb = fc.map(FileChannel.MapMode.READ_WRITE, 0, 1024);
```

##### 18.10.8 对比

NIO 与普通 I/O 的区别主要有以下两点：

- NIO 是非阻塞的。应当注意，FileChannel 不能切换到非阻塞模式，套接字 Channel 可以。
- NIO 面向块，I/O 面向流。

### 18.11 压缩

### 18.12 对象序列化

不仅保存了对象的“全景图”，还能追踪对象内所包含的所有引用并保存那些对象

将实现了Serializable接口的对象转换成一个字节序列，并能在以后将这个字节序列恢复为原来的对象，可通过网络进行。意味着序列化可弥补不同操作系统之间的差异

主要为了支持两种特性：

1. 远程方法调用（RMI）：使其它计算机的对象使用起来就像在本机一样，当向远程对象发送消息时需要通过对象序列化来传输参数和返回值
2. Java Beans。使用一个Bean时一般是在设计阶段对它的状态信息进行配置，这种状态信息必须保存并在程序启动时进行后期恢复

### 18.13 XML

### 18.14 Preferences

是一个键-值集合。用于存储和读取用户的偏好以及程序配置项的设置