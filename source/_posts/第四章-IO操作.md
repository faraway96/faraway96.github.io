title: 第四章 IO操作
date: 2018-2-8 22:25:12
categories: 编程基础
---
　　本章主要写一下java中IO相关的知识，已便于整理和理解。
## 什么是IO ##
> 对语言设计人员来说，创建好的输入／输出系统是一项特别困难的任务。

　　`IO`代表了Input和Output，为什么说它复杂呢，因为`IO`复杂点在于如何覆盖所有可能的因素，我们不仅仅要考虑文件、控制台、网络、内存等不同的种类，而且要处理大量的不同的读取方式，如：顺序读取、随机读取，二进制读取、字符读取，按行读取、按字符读取……
### java中的IO ###
　　当前Java中的IO包含两部分：
　　- java.io包中堵塞型IO
　　- java.nio包中的非堵塞型IO，通常称为New IO

## java.io ##
　　Java的核心库`java.io`提供了全面的IO接口。包括：`文件读写`、`标准设备输入、输出`等。Java中IO是以流为基础进行输入输出的，所有数据被串行化写入输出流，或者从输入流读入。
### 基本概念 ###

    - 流：流是一个很形象的概念，当程序需要读取数据的时候，就会开启一个通向数据源的流，这个数据源可以是文件，内存，或是网络连接。类似的，当程序需要写入数据的时候，就会开启一个通向目的地的流。这时候你就可以想象数据好像在这其中“流”动一样。
    - 阻塞式IO：所谓阻塞式IO流，就是指在从数据流当中读写数据的的时候，阻塞当前线程，直到IO流可以重新使用为止。

　　java.io包中主要包含三部分

 - 流式部分 : IO的主体部分；
 - 非流式部分 : 主要包含一些辅助流式部分的类，如：File类、RandomAccessFile类和FileDescriptor等类
 - 其他类 : 文件读取部分的与安全相关的类，如：SerializablePermission类，以及与本地操作系统相关的文件系统的类，如：FileSystem类和Win32FileSystem类和WinNTFileSystem类

　　我们可以从几方面对IO流进行分类：
- 按照来源和去向可以分为：输入流和输出流
- 按照处理数据类型的不同分为：字符流和字节流
- 按数据对象分类：
    1. 文件
    2. 字节数组
    3. 字符串
    4. “管道”
    5. 其他种类流组成的序列
    6. 其他数据源

### 字节流 ###

#### 输入字节流 ####

![InputStream][1]
 
　　`IO`中输入字节流的继承图可见上图，可以看出：

    - InputStream是所有的输入字节流的父类，它是一个抽象类。
    - ByteArrayInputStream、StringBufferInputStream(上图的StreamBufferInputStream)、FileInputStream是三种基本的介质流，它们分别从Byte数组、StringBuffer、和本地文件中读取数据。
    - PipedInputStream是从与其它线程共用的管道中读取数据.
    - ObjectInputStream和所有FilterInputStream的子类都是装饰流（装饰器模式的主角）。
　　InputStream中的三个基本的读方法
    
    - abstract int read() ：读取一个字节数据，并返回读到的数据，如果返回-1，表示读到了输入流的末尾。
    - intread(byte[]?b) ：将数据读入一个字节数组，同时返回实际读取的字节数。如果返回-1，表示读到了输入流的末尾。
    - intread(byte[]?b, int?off, int?len) ：将数据读入一个字节数组，同时返回实际读取的字节数。如果返回-1，表示读到了输入流的末尾。off指定在数组b中存放数据的起始偏移位置；len指定读取的最大字节数。

　　流结束的判断：方法read()的返回值为-1时；readLine()的返回值为null时。

　　其它方法

    - long skip(long?n)：在输入流中跳过n个字节，并返回实际跳过的字节数。
    - int available() ：返回在不发生阻塞的情况下，可读取的字节数。
    - void close() ：关闭输入流，释放和这个流相关的系统资源。
    - voidmark(int?readlimit) ：在输入流的当前位置放置一个标记，如果读取的字节数多于readlimit设置的值，则流忽略这个标记。
    - void reset() ：返回到上一个标记。
    - booleanmarkSupported() ：测试当前流是否支持mark和reset方法。如果支持，返回true，否则返回false。

#### 输出字节流 ####
![OutputStream][2]

　　`IO`中输出字节流的继承图可见上图，可以看出：

    - OutputStream是所有的输出字节流的父类，它是一个抽象类。
    - ByteArrayOutputStream、FileOutputStream是两种基本的介质流，它们分别向Byte数组、和本地文件中写入数据。PipedOutputStream是向与其它线程共用的管道中写入数据。
    - ObjectOutputStream和所有FilterOutputStream的子类都是装饰流。



　　OutputStream中的三个基本的写方法
    
    - abstract void write(int?b)：往输出流中写入一个字节。
    - void write(byte[]?b) ：往输出流中写入数组b中的所有字节。
    - void write(byte[]?b, int?off, int?len) ：往输出流中写入数组b中从偏移量off开始的len个字节的数据。

　　其它方法
    
    - void flush() ：刷新输出流，强制缓冲区中的输出字节被写出。
    - void close() ：关闭输出流，释放和这个流相关的系统资源。

### 字符流 ###

#### 字符输入流 ####
![Reader][3]
　　在上面的继承关系图中可以看出：
    
    - Reader是所有的输入字符流的父类，它是一个抽象类。
    - CharReader、StringReader是两种基本的介质流，它们分别将Char数组、String中读取数据。PipedReader是从与其它线程共用的管道中读取数据。
    - BufferedReader很明显就是一个装饰器，它和其子类负责装饰其它Reader对象。
    - FilterReader是所有自定义具体装饰流的父类，其子类PushbackReader对Reader对象进行装饰，会增加一个行号。
    - InputStreamReader是一个连接字节流和字符流的桥梁，它将字节流转变为字符流。FileReader可以说是一个达到此功能、常用的工具类，在其源代码中明显使用了将FileInputStream转变为Reader的方法。我们可以从这个类中得到一定的技巧。Reader中各个类的用途和使用方法基本和InputStream中的类使用一致。后面会有Reader与InputStream的对应关系。

　　主要方法：

    - int read()  读取一个字符，返回值为读取的字符。
    - int read(char cbuf[])   读取一系列字符到数组cbuf[]中，返回值为实际读取的字符的数量。
    - abstract int read(char cbuf[],int off,int len)  读取len个字符，从数组cbuf[]的下标off处开始存放，返回值为实际读取的字符数量，该方法必须由子类实现。

#### 字符输出流 ####
![Writer][4]
　　在上面的关系图中可以看出：

    - Writer是所有的输出字符流的父类，它是一个抽象类。
    - CharArrayWriter、StringWriter是两种基本的介质流，它们分别向Char数组、String中写入数据。PipedWriter是向与其它线程共用的管道中写入数据
    - BufferedWriter是一个装饰器为Writer提供缓冲功能。
    - PrintWriter和PrintStream极其类似，功能和使用也非常相似。
    - OutputStreamWriter是OutputStream到Writer转换的桥梁，它的子类FileWriter其实就是一个实现此功能的具体类（具体可以研究一SourceCode）。功能和使用和OutputStream极其类似.

　　主要方法：
    
    - void write(int c)  将整型值c的低16位写入输出流 
    - void write(char cbuf[])  将字符数组cbuf[]写入输出流 
    - abstract void write(char cbuf[],int off,int len)  将字符数组cbuf[]中的从索引为off的位置处开始的len个字符写入输出流 
    - void write(String str)  将字符串str中的字符写入输出流 
    - void write(String str,int off,int len)  将字符串str 中从索引off开始处的len个字符写入输出流 


### IO流使用选择 ###
　　确定选用流对象的步骤
    
    1. 确定原始数据的格式
    2. 确定是输入还是输出
    3. 是否需要转换流
    4. 数据的来源（去向）
    5. 是否需要缓冲
    6. 是否需要格式化输出

　　按照数据格式选择
    
    二进制格式（只要确定不是纯文本格式的），InputStream, OutputStream, 及其所有带Stream子类
    纯文本格式（比如英文/汉字/或其他编码文字）：Reader, Writer, 及其相关子类

　　按照输入输出选择
    
    输入：Reader， InputStream，及其相关子类
    输出：Writer，OutputStream，及其相关子类
    
　　按照数据的来源（去向）选择
    
    是文件：FileInputStream, FileOutputStream, FileReader, FileWriter
    是byte[]：ByteArrayInputStream, ByteArrayOutputStream
    是char[]：CharArrayReader, CharArrayWriter
    是String：StringBufferInputStream(已过时，因为其只能用于String的每个字符都是8位的字符串), StringReader, StringWriter
    是网络数据流：InputStream, OutputStream, Reader, Writer

　　是否需要缓冲
    
    要缓冲：BufferedInputStream, BufferedOuputStream, BuffereaReader, BufferedWriter
　　是否需要格式化输出
    
    需要格式化输出：PrintStream（输出字节），PrintWriter（输出字符）

　　特殊需要
    
    从Stream转化为Reader，Writer：InputStreamReader，OutputStreamWriter
    对象输入输出流：ObjectInputStream，ObjectOutputStream
    进程间通信：PipeInputStream，PipeOutputStream，PipeReader，PipeWriter
    合并输入：SequenceInputStream
    更特殊的需要：PushbackInputStream, PushbackReader, LineNumberInputStream, LineNumberReader

### 常用范例 ###


## java.nio ##
　　`java.nio`全称java non-blocking IO，是指jdk1.4 及以上版本里提供的新api（New IO），为所有的原始类型（boolean类型除外）提供缓存支持的数据容器，使用它可以提供非阻塞式的高伸缩性网络。

### 基本概念 ###
　　`nio`的核心概念主要有三个：
    
    - Channels : Channel是一个对象，可以通过它读取和写入数据。
    - Buffers : 是一个固定数据量的指定基本类型的数据容器。
    - Selectors : 选择器，Selector允许单线程处理多个 Channel。
### Channels ###
　　Java NIO的通道类似流，但又有些不同：

 - 既可以从通道中读取数据，又可以写数据到通道。但流的读写通常是单向的。
 - 通道可以异步地读写。
 - 通道中的数据总是要先读到一个Buffer，或者总是要从一个Buffer中写入。

#### Channel的实现类 ####
    
    - FileChannel ： 从文件中读写数据。
    - DatagramChannel ： 能通过UDP读写网络中的数据。
    - SocketChannel ： 能通过TCP读写网络中的数据。
    - ServerSocketChannel ： 可以监听新进来的TCP连接，像Web服务器那样。对每一个新进来的连接都会创建一个SocketChannel。
#### 基本示例 ####
　　下面是一个使用FileChannel读取数据到Buffer中的示例：
```java
　　public static void main(String[] args) throws IOException {
		RandomAccessFile raf = new RandomAccessFile("D:\\logs\\nio.txt", "rw");
		FileChannel fc = raf.getChannel();
		ByteBuffer buf = ByteBuffer.allocate((int)fc.size());
		int bytesRead = fc.read(buf);
		while (bytesRead != -1)
		{
			buf.flip();
            while(buf.hasRemaining())
            {
                System.out.print((char)buf.get());
            }

            buf.compact();
            bytesRead = fc.read(buf);
		}
	}
```
### Buffer ###
　　缓冲区本质上是一块可以写入数据，然后可以从中读取数据的内存。这块内存被包装成NIO `Buffer`对象，并提供了一组方法，用来方便的访问该块内存。
    
    Buffer的基本类型包括：
    - ByteBuffer
    - MappedByteBuffer
    - CharBuffer
    - DoubleBuffer
    - FloatBuffer
    - IntBuffer
    - LongBuffer
    - ShortBuffer
#### Buffer工作原理 ####
![Buffer工作原理][5]

　　想要理解`Buffer`需要先熟悉它的三个属性：
    
 - `capacity` ：
   作为一个内存块，Buffer有一个固定的大小值，也叫“capacity”.你只能往里写capacity个byte、long，char等类型。一旦Buffer满了，需要将其清空（通过读数据或者清除数据）才能继续写数据往里写数据。
 - `position`
     - 当你写数据到Buffer中时，position表示当前的位置。初始的position值为0.当一个byte、long等数据写到Buffer后，position会向前移动到下一个可插入数据的Buffer单元。position最大可为capacity
     - 当读取数据时，也是从某个特定位置读。当将Buffer从写模式切换到读模式，position会被重置为0，当从Buffer的position处读取数据时，position向前移动到下一个可读的位置。
 - `limit`
     - 在写模式下，Buffer的limit表示你最多能往Buffer里写多少数据。 写模式下，limit等于Buffer的capacity。
     - 当切换Buffer到读模式时， limit表示你最多能读到多少数据。因此，当切换Buffer到读模式时，limit会被设置成写模式下的position值。换句话说，你能读到之前写入的所有数据（limit被设置成已写数据的数量，这个值在写模式下就是position）。

#### Buffer的常用方法 ####
    
    下面列举一些`Buffer`的常用方法：
    - allocate(int capacity)  该方法用来分配一个固定大小的缓冲区。
    - put()  put方法有很多版本，允许你以不同的方式把数据写入到Buffer中。
    - flip()  flip方法将Buffer从写模式切换到读模式。调用flip()方法会将position设回0，并将limit设置成之前position的值。换句话说，position现在用于标记读的位置，limit表示之前写进了多少个byte、char等 —— 现在能读取多少个byte、char等。
    - get()  get方法有很多版本，允许你以不同的方式从Buffer中读取数据。
    - rewind()  Buffer.rewind()将position设回0，所以你可以重读Buffer中的所有数据。limit保持不变，仍然表示能从Buffer中读取多少个元素（byte、char等）。
    - clear()  position将被设回0，limit被设置成 capacity的值。换句话说，Buffer 被清空了。Buffer中的数据并未清除，只是这些标记告诉我们可以从哪里开始往Buffer里写数据。如果Buffer中有一些未读的数据，调用clear()方法，数据将“被遗忘”，意味着不再有任何标记会告诉你哪些数据被读过，哪些还没有。
    - compact()  将所有未读的数据拷贝到Buffer起始处。然后将position设到最后一个未读元素正后面。limit属性依然像clear()方法一样，设置成capacity。现在Buffer准备好写数据了，但是不会覆盖未读的数据。
    - mark()与reset()  通过调用Buffer.mark()方法，可以标记Buffer中的一个特定position。之后可以通过调用Buffer.reset()方法恢复到这个position。
    - equals()  当满足下列条件时，表示两个Buffer相等：
        - 有相同的类型（byte、char、int等）。
        - Buffer中剩余的byte、char等的个数相等。
        - Buffer中所有剩余的byte、char等都相同。
    - compareTo()  比较两个Buffer的剩余元素(byte、char等)， 如果满足下列条件，则认为一个Buffer“小于”另一个Buffer：
        - 第一个不相等的元素小于另一个Buffer中对应的元素 。
        - 所有元素都相等，但第一个Buffer比另一个先耗尽(第一个Buffer的元素个数比另一个少)。
        
### Selector ###
　　Selector（选择器）是Java NIO中能够检测一到多个NIO通道，并能够知晓通道是否为诸如读写事件做好准备的组件。这样，一个单独的线程可以管理多个channel，从而管理多个网络连接。
　　详解可以参阅[Java NIO中的Selector和IO复用][6]
### nio和io的区别 ###
　　详解可以参阅[Java NIO与IO][7]

**参考阅读**
- [深入理解Java中的IO](http://blog.csdn.net/qq_25184739/article/details/51205186)
- [java IO选择流的原则及其与IO流相关类的关系](https://www.cnblogs.com/penghuster/p/4869153.html)
- [HashSet和HashMap的区别](http://www.importnew.com/6931.html)
- [并发编程网](http://ifeve.com/java-nio-all/)

  [1]: /img/codebase4-1.jpg
  [2]: /img/codebase4-2.jpg
  [3]: /img/codebase4-3.jpg
  [4]: /img/codebase4-4.jpg
  [5]: /img/codebase4-5.jpg
  [6]: http://www.molotang.com/articles/906.html
  [7]: http://ifeve.com/java-nio-vs-io/