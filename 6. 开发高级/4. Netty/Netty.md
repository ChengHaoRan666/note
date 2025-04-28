## 1. NIO BIO AIO

### 1.1 NIO BIO AIO区别

1. BIO：java实现的网络编程模型。`同步并阻塞`

   > 服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销

   ![image](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/Netty/image.7lk71tk4p3.webp)

2. NIO：java 1.4 之后添加的网络编程模型。`同步非阻塞`

   > 服务器实现模式为一个线程处理多个请求（连接），即客户端发送的连接请求都会注册到多路复用器上，多路复用器轮询到连接有 `I/O` 请求就进行处理

   ![NiO模式图](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/Netty/NiO模式图.esppyzqmi.webp)

3. AIO：java 1.7 添加的网络编程模型。`同步非阻塞`

   > `Java AIO(NIO.2)`：异步非阻塞，`AIO` 引入异步通道的概念，采用了 `Proactor` 模式，简化了程序编写，有效的请求才启动线程，它的特点是先由操作系统完成后才通知服务端程序启动线程去处理，一般适用于连接数较多且连接时间较长的应用。



### 1.2 BIO NIO AIO 使用场景分析

1. `BIO` 方式适用于连接数目比较小且固定的架构，这种方式对服务器资源要求比较高，并发局限于应用中，`JDK1.4` 以前的唯一选择，但程序简单易理解。
2. `NIO` 方式适用于连接数目多且连接比较短（轻操作）的架构，比如聊天服务器，弹幕系统，服务器间通讯等。编程比较复杂，`JDK1.4` 开始支持。
3. `AIO` 方式使用于连接数目多且连接比较长（重操作）的架构，比如相册服务器，充分调用 `OS` 参与并发操作，编程比较复杂，`JDK7` 开始支持。



## 2. NIO - 三大组件

1. `Java NIO` 全称 **`Java non-blocking IO`** ，是指 `JDK` 提供的新 `API`。从 `JDK1.4` 开始，`Java` 提供了一系列改进的输入/输出的新特性，被统称为 `NIO`，是同步非阻塞的
2. `NIO` 相关类都被放在 **`java.nio`** 包及子包下，并且对原 `java.io` 包中的很多类进行改写
3. `NIO` 有三大核心部分: **`Channel`（通道）、`Buffer`（缓冲区）、`Selector`（选择器）** 
4. `NIO` 是**面向缓冲区，或者面向块编程**的。数据读取到一个它稍后处理的缓冲区，需要时可在缓冲区中前后移动，这就增加了处理过程中的灵活性，使用它可以提供非阻塞式的高伸缩性网络
5. `Java NIO` 的非阻塞模式，使一个线程从某通道发送请求或者读取数据，但是它仅能得到目前可用的数据，如果目前没有数据可用时，就什么都不会获取，而不是保持线程阻塞，所以直至数据变的可以读取之前，该线程可以继续做其他的事情。非阻塞写也是如此，一个线程请求写入一些数据到某通道，但不需要等待它完全写入，这个线程同时可以去做别的事情
6. 通俗理解：`NIO` 是可以做到用一个线程来处理多个操作的。假设有 `10000` 个请求过来,根据实际情况，可以分配 `50` 或者 `100` 个线程来处理。不像之前的阻塞 `IO` 那样，非得分配 `10000` 个
7. `HTTP 2.0` 使用了多路复用的技术，做到同一个连接并发处理多个请求，而且并发请求的数量比 `HTTP 1.1` 大了好几个数量级



### 2.1 NIO组件 - Channel

和`stream`类似，但是他是读写数据的双向通道，可以从`chanel`中将数据读入`buffer`，也可以将`buffer`的数据写入`Channel`，之前的`stream`要么是输入，要么是输出。`Channel`比`stream`更加底层

常见的`Channel`有：

1. ==FileChannel==：用于读写文件数据的通道
2. ==DatagramChannel==：用于 UDP 数据包传输的通道
3. ==SocketChannel==：用于 TCP 客户端连接的通道
4. ==ServrSocketChannel==：用于 TCP 服务器端监听连接请求的通道

<font color="red">`FileChannel`是阻塞的,`DatagramChannel`,`SocketChannel`,`ServrSocketChannel`可以设置是非阻塞的</font>



### 2.2 NIO组件 - Buffer

`Buffer` 是一块内存区域，专门用来存储数据。`NIO` 的所有数据读写都要经过 `Buffer`，不能直接操作 `Channel`。

常用的的`Buffer`有：

1. ==ByteBuffer==：<font color="red">最常用</font>   存储字节数据到缓冲区
2. ==ShortBuffer==：存储字符串数据到缓冲区
3. ==IntBuffer==：存储整数数据到缓冲区
4. ==LongBuffer==：存储长整形数据到缓冲区
5. ==FloatBuffer==：存储小数到缓冲区
6. ==DoubleBuffer==：存储小数到缓冲区
7. ==CharBuffer==：存储字符到缓冲区



### 2.3 NIO组件 - selector

服务器设计有三个阶段：多线程版设计，线程池版设计，selector设计

#### 2.3.1 服务器设计 - 多线程版

![服务器多线程版](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/Netty/服务器多线程版.70ajh7lrfp.webp)
多线程版有以下问题：

1. 内存占用高，一个线程占用一个连接，每开一个线程，操作系统要为其分配一些系统资源
2. 上下文切换资源大，多线程意味着CPU需要来回切换，在切换过程中，成本高
3. 只适用于小范围连接





#### 2.3.2 服务器设计 - 线程池版

![服务器线程池版](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/Netty/服务器线程池版.2h8ie8hdc8.webp)

线程池有以下问题：

1. 在<font color="red">阻塞状态</font>下，一个线程只能处理一个连接
2. 仅仅适用于短连接场景



#### 2.3.3 服务器设计 - selector版

![服务器selector版](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/Netty/服务器selector版.6wqxji7c8w.webp)

1. `selector`会监听`Channel`的动作，当某一个`Channel`有动作时，`selector`会将动作传达给`thread`
2. 在`selector`版本中，`Channel`工作在<font color="red">非阻塞状态</font>下
3. 适合连接多，流量低的场景



## 3. ByteBuffer

### 3.1 ByteBuffer 使用

```java
package com.chr.netty.NIO;

import lombok.extern.slf4j.Slf4j;

import java.io.FileInputStream;
import java.io.IOException;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;

/**
 * 使用Buffer和Channel实现文件读取
 *
 * @author 程浩然
 * @since 2025-04-27
 */
@Slf4j
public class TestByteBuffer {
    public static void main(String[] args) throws IOException {
        // 创建 fileChannel
    try (FileChannel fileChannel = new FileInputStream("data.txt").getChannel()) {
            // 创建 byte 缓冲区
            ByteBuffer byteBuffer = ByteBuffer.allocateDirect(10);

            while (true) {
                // 从 Channel 读，向 byteBuffer 写
                int len = fileChannel.read(byteBuffer);
                log.info("读取的字节数量：{}", len);
                if (len == -1) {
                    break;
                }
                // 读数据
                // 切换为读模式
                byteBuffer.flip();
                // 判断是否有数据
                while (byteBuffer.hasRemaining()) {
                    byte b = byteBuffer.get();
                    log.info("读取的字节内容：{}", (char) b);
                }
                // 切换为写模式
                byteBuffer.clear();
            }
        } catch (Exception e) {
        }
    }
}
```

输出为：

```txt
15:57:21.129 [main] INFO com.chr.netty.NIO.TestByteBuffer -- 读取的字节数量：10
15:57:21.133 [main] INFO com.chr.netty.NIO.TestByteBuffer -- 读取的字节内容：1
15:57:21.133 [main] INFO com.chr.netty.NIO.TestByteBuffer -- 读取的字节内容：2
15:57:21.133 [main] INFO com.chr.netty.NIO.TestByteBuffer -- 读取的字节内容：3
15:57:21.133 [main] INFO com.chr.netty.NIO.TestByteBuffer -- 读取的字节内容：4
15:57:21.133 [main] INFO com.chr.netty.NIO.TestByteBuffer -- 读取的字节内容：5
15:57:21.133 [main] INFO com.chr.netty.NIO.TestByteBuffer -- 读取的字节内容：6
15:57:21.133 [main] INFO com.chr.netty.NIO.TestByteBuffer -- 读取的字节内容：7
15:57:21.133 [main] INFO com.chr.netty.NIO.TestByteBuffer -- 读取的字节内容：8
15:57:21.133 [main] INFO com.chr.netty.NIO.TestByteBuffer -- 读取的字节内容：9
15:57:21.133 [main] INFO com.chr.netty.NIO.TestByteBuffer -- 读取的字节内容：0
15:57:21.133 [main] INFO com.chr.netty.NIO.TestByteBuffer -- 读取的字节数量：6
15:57:21.133 [main] INFO com.chr.netty.NIO.TestByteBuffer -- 读取的字节内容：a
15:57:21.133 [main] INFO com.chr.netty.NIO.TestByteBuffer -- 读取的字节内容：b
15:57:21.133 [main] INFO com.chr.netty.NIO.TestByteBuffer -- 读取的字节内容：c
15:57:21.133 [main] INFO com.chr.netty.NIO.TestByteBuffer -- 读取的字节内容：d
15:57:21.133 [main] INFO com.chr.netty.NIO.TestByteBuffer -- 读取的字节内容：e
15:57:21.133 [main] INFO com.chr.netty.NIO.TestByteBuffer -- 读取的字节内容：f
15:57:21.133 [main] INFO com.chr.netty.NIO.TestByteBuffer -- 读取的字节数量：-1
```





步骤：

1. 创建`byteBuffer`缓冲区
2. 向`Buffer`中写入数据
3. 调用`flip()`切换为读模式
4. 从`Buffer`中读数据
5. 调用`clear()`或`compart()`切换为写模式
6. 重复2~5步



### 3.2 ByteBuffer 结构

ByteBuffer结构类似数组，有三个属性：

1. `Capacity`：容量，即可以容纳的最大数据量，在缓冲区创建时被设置，不能改变
2. `Limit`：表示缓冲区的当前终点，不能对缓冲区超过极限的位置进行读写操作，且极限是可以修改的
3. `Position`：位置，下一个要被读或写的元素的索引，每次读写缓冲区数据时都会改变，为下次读写作准备



一开始

![0021](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/Netty/0021.wireun2la.webp)

写模式下，position 是写入位置，limit 等于容量，下图表示写入了 4 个字节后的状态

![0018](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/Netty/0018.1e8t3fog64.webp)

flip 动作发生后，position 切换为读取位置，limit 切换为读取限制

![0019](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/Netty/0019.6t7blv3ljx.webp)

读取 4 个字节后，状态

![0020](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/Netty/0020.54xyoodbdq.webp)

clear 动作发生后，状态

![0021](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/Netty/0021.wireun2la.webp)

compact 方法，是把未读完的部分向前压缩，然后切换至写模式

![0022](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/Netty/0022.8z6q7mv9b3.webp)





#### ByteBuffer结构调试工具类

```java
package com.chr.netty.ByteBuffer;

import io.netty.util.internal.StringUtil;

import java.nio.ByteBuffer;

import static io.netty.util.internal.MathUtil.isOutOfBounds;
import static io.netty.util.internal.StringUtil.NEWLINE;

/**
 * ByteBuffer工具类，用于测试
 *
 * @author 程浩然
 * @since 2025-04-27
 */
public class ByteBufferUtil {
    private static final char[] BYTE2CHAR = new char[256];
    private static final char[] HEXDUMP_TABLE = new char[256 * 4];
    private static final String[] HEXPADDING = new String[16];
    private static final String[] HEXDUMP_ROWPREFIXES = new String[65536 >>> 4];
    private static final String[] BYTE2HEX = new String[256];
    private static final String[] BYTEPADDING = new String[16];

    static {
        final char[] DIGITS = "0123456789abcdef".toCharArray();
        for (int i = 0; i < 256; i++) {
            HEXDUMP_TABLE[i << 1] = DIGITS[i >>> 4 & 0x0F];
            HEXDUMP_TABLE[(i << 1) + 1] = DIGITS[i & 0x0F];
        }

        int i;

        // Generate the lookup table for hex dump paddings
        for (i = 0; i < HEXPADDING.length; i++) {
            int padding = HEXPADDING.length - i;
            StringBuilder buf = new StringBuilder(padding * 3);
            for (int j = 0; j < padding; j++) {
                buf.append("   ");
            }
            HEXPADDING[i] = buf.toString();
        }

        // Generate the lookup table for the start-offset header in each row (up to 64KiB).
        for (i = 0; i < HEXDUMP_ROWPREFIXES.length; i++) {
            StringBuilder buf = new StringBuilder(12);
            buf.append(NEWLINE);
            buf.append(Long.toHexString(i << 4 & 0xFFFFFFFFL | 0x100000000L));
            buf.setCharAt(buf.length() - 9, '|');
            buf.append('|');
            HEXDUMP_ROWPREFIXES[i] = buf.toString();
        }

        // Generate the lookup table for byte-to-hex-dump conversion
        for (i = 0; i < BYTE2HEX.length; i++) {
            BYTE2HEX[i] = ' ' + StringUtil.byteToHexStringPadded(i);
        }

        // Generate the lookup table for byte dump paddings
        for (i = 0; i < BYTEPADDING.length; i++) {
            int padding = BYTEPADDING.length - i;
            StringBuilder buf = new StringBuilder(padding);
            for (int j = 0; j < padding; j++) {
                buf.append(' ');
            }
            BYTEPADDING[i] = buf.toString();
        }

        // Generate the lookup table for byte-to-char conversion
        for (i = 0; i < BYTE2CHAR.length; i++) {
            if (i <= 0x1f || i >= 0x7f) {
                BYTE2CHAR[i] = '.';
            } else {
                BYTE2CHAR[i] = (char) i;
            }
        }
    }

    /**
     * 打印所有内容
     *
     * @param buffer
     */
    public static void debugAll(ByteBuffer buffer) {
        int oldlimit = buffer.limit();
        buffer.limit(buffer.capacity());
        StringBuilder origin = new StringBuilder(256);
        appendPrettyHexDump(origin, buffer, 0, buffer.capacity());
        System.out.println("+--------+-------------------- all ------------------------+----------------+");
        System.out.printf("position: [%d], limit: [%d]\n", buffer.position(), oldlimit);
        System.out.println(origin);
        buffer.limit(oldlimit);
    }

    /**
     * 打印可读取内容
     *
     * @param buffer
     */
    public static void debugRead(ByteBuffer buffer) {
        StringBuilder builder = new StringBuilder(256);
        appendPrettyHexDump(builder, buffer, buffer.position(), buffer.limit() - buffer.position());
        System.out.println("+--------+-------------------- read -----------------------+----------------+");
        System.out.printf("position: [%d], limit: [%d]\n", buffer.position(), buffer.limit());
        System.out.println(builder);
    }

    private static void appendPrettyHexDump(StringBuilder dump, ByteBuffer buf, int offset, int length) {
        if (isOutOfBounds(offset, length, buf.capacity())) {
            throw new IndexOutOfBoundsException("expected: " + "0 <= offset(" + offset + ") <= offset + length(" + length + ") <= " + "buf.capacity(" + buf.capacity() + ')');
        }
        if (length == 0) {
            return;
        }
        dump.append("         +-------------------------------------------------+").append(NEWLINE).append("         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |").append(NEWLINE).append("+--------+-------------------------------------------------+----------------+");

        final int startIndex = offset;
        final int fullRows = length >>> 4;
        final int remainder = length & 0xF;

        // Dump the rows which have 16 bytes.
        for (int row = 0; row < fullRows; row++) {
            int rowStartIndex = (row << 4) + startIndex;

            // Per-row prefix.
            appendHexDumpRowPrefix(dump, row, rowStartIndex);

            // Hex dump
            int rowEndIndex = rowStartIndex + 16;
            for (int j = rowStartIndex; j < rowEndIndex; j++) {
                dump.append(BYTE2HEX[getUnsignedByte(buf, j)]);
            }
            dump.append(" |");

            // ASCII dump
            for (int j = rowStartIndex; j < rowEndIndex; j++) {
                dump.append(BYTE2CHAR[getUnsignedByte(buf, j)]);
            }
            dump.append('|');
        }

        // Dump the last row which has less than 16 bytes.
        if (remainder != 0) {
            int rowStartIndex = (fullRows << 4) + startIndex;
            appendHexDumpRowPrefix(dump, fullRows, rowStartIndex);

            // Hex dump
            int rowEndIndex = rowStartIndex + remainder;
            for (int j = rowStartIndex; j < rowEndIndex; j++) {
                dump.append(BYTE2HEX[getUnsignedByte(buf, j)]);
            }
            dump.append(HEXPADDING[remainder]);
            dump.append(" |");

            // Ascii dump
            for (int j = rowStartIndex; j < rowEndIndex; j++) {
                dump.append(BYTE2CHAR[getUnsignedByte(buf, j)]);
            }
            dump.append(BYTEPADDING[remainder]);
            dump.append('|');
        }

        dump.append(NEWLINE).append("+--------+-------------------------------------------------+----------------+");
    }

    private static void appendHexDumpRowPrefix(StringBuilder dump, int row, int rowStartIndex) {
        if (row < HEXDUMP_ROWPREFIXES.length) {
            dump.append(HEXDUMP_ROWPREFIXES[row]);
        } else {
            dump.append(NEWLINE);
            dump.append(Long.toHexString(rowStartIndex & 0xFFFFFFFFL | 0x100000000L));
            dump.setCharAt(dump.length() - 9, '|');
            dump.append('|');
        }
    }

    public static short getUnsignedByte(ByteBuffer buffer, int index) {
        return (short) (buffer.get(index) & 0xFF);
    }
}
```



### 3.3 ByteBuffer 常用方法

#### 3.3.1 分配空间

分配空间有两种方法：

> //java的堆内存，读写效率低，收到java的垃圾回收机制影响
>
> ```java
> ByteBuffer.allocate(10)
> ```
>
> // 直接内存，读写效率高（少一次拷贝），不会收到java的垃圾回收机制影响，分配的效率低
>
> ```java
> ByteBuffer.allocateDirect(10) 
> ```



#### 3.3.2 向 buffer 写入数据

有两种办法

* 调用 `channel `的 `read `方法

```java
int readBytes = channel.read(buf);
```

- 调用 `buffer `自己的 `put `方法

```java
// 写入数据
buf.put((byte) 0x61);

// 写入数组中全部数据
buf.put(new byte[]{0x61, 0x62, 0x63});

// 在指定位置写入数据，position的值不会改变
buf.put(3, (byte) 0x61);

// 将另一个ByteBuffer的值写入
buf.put(otherBuf);
```



#### 3.3.3 从 buffer 读取数据

同样有两种办法

* 调用 `channel `的 `write `方法

```java
int writeBytes = channel.write(buf);
```

- 调用 `buffer `自己的 `get `方法

```java
// 读取ByteBuffer中的值
byte b = buf.get();

// 读取ByteBuffer中的值放入byte数组中
byte[] bytes = new byte[10];
buffer.get(bytes);

// 读取指定位置的值，不会让position的值改变
byte b = buf.get(3);
```

`get `方法会让 `position `读指针向后走，如果想重复读取数据

* 可以调用 `rewind `方法将 `position `重新置为 0
* 或者调用` get(int i) `方法获取索引 i 的内容，它不会移动读指针



#### 3.3.4 切换模式

- `flip()`：从写模式切换到读模式
- `clear()`：清空ByteBuffer中数据将`position`变为0，重新写数据
- `compact()`：整理ByteBuffer中数据并改变`position`，重新写数据，不覆盖原本的数据



#### 3.3.5 标记和跳转

`mark `是在读取时，做一个标记，即使 `position `改变，只要调用 `reset `就能回到 `mark `的位置

- ==buffer.mark();==
- ==buffer.reset();==

> **注意**
>
> rewind 、clear 和 flip 都会清除 mark 位置



#### 3.3.6 String和ByteBuffer互转

可以通过`StandardCharsets`类(标准字符集类)进行转换

>字符串转为ByteBuffer：通过`encode`转换
>
>```java
>ByteBuffer buffer = StandardCharsets.UTF_8.encode("你好");
>```
>
>字符串转为ByteBuffer：通过`wrap`方法转换
>
>```java
>ByteBuffer buffer2 = ByteBuffer.wrap("string".getBytes());
>```
>
>ByteBuffer转为字符串：
>
>```java
>String bufferStr = StandardCharsets.UTF_8.decode(buffer).toString();
>```

<font color="red">当使用 `decode `方法将 ByteBuffer 转为字符串需要 ByteBuffer 是读模式</font>

<font color="red">通过`wrap`方法和`encode`方法转换得到的是读模式，通过`put`方法得到的ByteBuffer是写模式</font>



### 3.4 Scattering Reads

分散读取，读取一个内容，分割为多个ByteBuffer

```java
public static void main(String[] args) {
        try (FileChannel fileChannel = FileChannel.open(Path.of("words.txt"))) {
            ByteBuffer buffer1 = ByteBuffer.allocate(3);
            ByteBuffer buffer2 = ByteBuffer.allocate(3);
            ByteBuffer buffer3 = ByteBuffer.allocate(5);
            fileChannel.read(new ByteBuffer[]{buffer1, buffer2, buffer3});
            System.out.println("buffer1:");
            debugAll(buffer1);
            System.out.println("buffer2:");
            debugAll(buffer2);
            System.out.println("buffer3:");
            debugAll(buffer3);
        } catch (IOException e) {
        }
    }
```

```
buffer1:
+--------+-------------------- all ------------------------+----------------+
position: [3], limit: [3]
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 6f 6e 65                                        |one             |
+--------+-------------------------------------------------+----------------+
buffer2:
+--------+-------------------- all ------------------------+----------------+
position: [3], limit: [3]
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 74 77 6f                                        |two             |
+--------+-------------------------------------------------+----------------+
buffer3:
+--------+-------------------- all ------------------------+----------------+
position: [5], limit: [5]
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 74 68 72 65 65                                  |three           |
+--------+-------------------------------------------------+----------------+
```





### 3.5 Gathering Writes 

集中写入：将多个内容写入一个文件中

```java
public static void main(String[] args) {
        try (FileChannel fileChannel = FileChannel.open(Path.of("src/main/java/com/chr/netty/ByteBuffer/words2.txt"),
                StandardOpenOption.READ, StandardOpenOption.WRITE, StandardOpenOption.CREATE)) {
            ByteBuffer b1 = StandardCharsets.UTF_8.encode("hello");
            ByteBuffer b2 = StandardCharsets.UTF_8.encode("world");
            ByteBuffer b3 = StandardCharsets.UTF_8.encode("你好");
            fileChannel.write(new ByteBuffer[]{b1, b2, b3});
        } catch (IOException e) {
            System.out.println(e);
        }
    }
```



### 3.6 实践

网络上有多条数据发送给服务端，数据之间使用 \n 进行分隔 但由于某种原因这些数据在接收时，被进行了重新组合，例如原始数据有3条为

- Hello,world\n
- I'm zhangsan\n
- How are you?\n

变成了下面的两个 byteBuffer (黏包，半包)

- Hello,world\nI'm zhangsan\nHo
- w are you?\n

现在要求你编写程序，将错乱的数据恢复成原始的按 \n 分隔的数据

| 现象 | 原因                                               | 示例                 |
| ---- | -------------------------------------------------- | -------------------- |
| 粘包 | 多条消息在 TCP 流中连续发出，接收端一次读到多条    | `Hello\nWorld\n`     |
| 半包 | 一条消息太大或网络分片导致，接收端一次只读到一部分 | `Hello,Wo` + `rld\n` |

```java
public class TestByteBufferPractice {
    public static void main(String[] args) {
        ByteBuffer buffer = ByteBuffer.allocate(60);
        buffer.put("Hello,world\nI'm zhangsan\nHo".getBytes());
        split(buffer);

        buffer.put("w are you?\n".getBytes());
        split(buffer);
    }

    private static void split(ByteBuffer buffer) {
        // 切换到读模式，limit为字符串长度
        buffer.flip();
        int limit = buffer.limit();
        for (int i = 0; i < limit; i++) {
            if (buffer.get(i) == '\n') {
                // 打印一句话结尾位置
                System.out.println("位置：" + i);
                // 创建一个ByteBuffer存储每一条消息，消息大小：\n的位置+1减去起始位置
                ByteBuffer buffer2 = ByteBuffer.allocate(i + 1 - buffer.position());
                // 设置limit为i + 1，用于将值复制给buffer2
                buffer.limit(i + 1);
                // 把Buffer复制到buffer2里，这个地方会把buffer的position的值更改
                buffer2.put(buffer);
                // 打印出来
                debugAll(buffer2);
                // 把limit设置回去，用于之后继续加数据
                buffer.limit(limit);
            }
        }
        buffer.compact();
    }
}
```



> TCP是面向字节流的协议，不会对消息的边界进行处理，会出现黏包半包问题，但是可靠的
>
> UDP是面向消息的协议，一次发送就是一条消息。但不是可靠的



## 4. 文件编程 - Filechannel

### 4.1 FileChannel 工作模式

<font color="red">FileChannel 只能工作在阻塞模式下</font>



### 4.2 使用

```java
ByteBuffer buffer = ByteBuffer.allocate(20);

// 1. 通过 FileInputStream 的getChannel方法获取，只能读
FileChannel fileChannel1 = new FileInputStream("word1.txt").getChannel();
// 报错
// fileChannel1.write(ByteBuffer.wrap("a".getBytes()));
fileChannel1.read(buffer);
// 切换到读模式
buffer.flip();
// 输出
debugAll(buffer);
// 切换到写模式
buffer.clear();

// 2. 通过 FileOutputStream 的getChannel方法获取，只能写
FileChannel fileChannel2 = new FileOutputStream("word1.txt").getChannel();
fileChannel2.write(ByteBuffer.wrap("ab".getBytes()));
// 报错
// fileChannel2.read(buffer);
// 切换到读模式
buffer.flip();
// 输出
debugAll(buffer);
// 切换到写模式
buffer.clear();

// 3. 通过 RandomAccessFile 是否能读写根据构造 RandomAccessFile 时的读写模式决定
FileChannel fileChannel3 = new RandomAccessFile("word1.txt", "rw").getChannel();
fileChannel3.write(ByteBuffer.wrap("abc".getBytes()));
// 将Filechannel的position指针切换到0
fileChannel3.position(0);

fileChannel3.read(buffer);
// 切换到读模式
buffer.flip();
// 输出
debugAll(buffer);
buffer.clear();

// 4. 用 FileChannel.open 创建
Path path = Paths.get("word1.txt");
// 创建FileChannel，有读写权限
FileChannel fileChannel4 = FileChannel.open(path, StandardOpenOption.READ, StandardOpenOption.WRITE);
// 写入内容
fileChannel4.write(ByteBuffer.wrap("abcd".getBytes()));
// 重置position为0
fileChannel4.position(0);
// 读文件内容到buffer
fileChannel4.read(buffer);
// 切换到读模式
buffer.flip();
// 读出
debugAll(buffer);


fileChannel1.close();
fileChannel2.close();
fileChannel3.close();
fileChannel4.close();
```

使用`RandomAccessFile`和`FileChannel.open` 创建的`FileChannel`在读取的时候要注意将`position`重新置为0



#### 4.2.1 获取

1. 通过 `FileInputStream `获取的 channel ==只能读==
2. 通过 `FileOutputStream `获取的 channel ==只能写==
3. 通过 `RandomAccessFile `是否能读写根据构造 RandomAccessFile 时的读写模式决定
4. 通过 `FileChannel.open` 创建



#### 4.2.2 读取

会从 channel 读取数据填充 ByteBuffer，返回值表示读到了多少字节，-1 表示到达了文件的末尾

```java
int readBytes = channel.read(buffer);
```



#### 4.2.3 写入

写入的正确姿势如下

```java
ByteBuffer buffer = ...;
buffer.put(...); // 存入数据
buffer.flip();   // 切换读模式

while(buffer.hasRemaining()) {
    channel.write(buffer);
}
```

在 while 中调用 channel.write 是因为 write 方法并不能保证一次将 buffer 中的内容全部写入 channel



#### 4.2.4 关闭

channel 必须关闭，不过调用了 FileInputStream、FileOutputStream 或者 RandomAccessFile 的 close 方法会间接地调用 channel 的 close 方法



#### 4.2.5 位置

获取当前位置

```java
long pos = channel.position();
```



设置当前位置

```java
long newPos = ...;
channel.position(newPos);
```



设置当前位置时，如果设置为文件的末尾

- 这时读取会返回 -1
- 这时写入，会追加内容，但要注意如果 position 超过了文件末尾，再写入时在新内容和原末尾之间会有空洞（00）



#### 4.2.6 大小

使用 size 方法获取文件的大小



#### 4.2.7 强制写入

操作系统出于性能的考虑，会将数据缓存，不是立刻写入磁盘。可以调用 force(true) 方法将文件内容和元数据（文件的权限等信息）立刻写入磁盘



### 4.3 传输数据

使用`transferTo`方法进行传输数据（只能传输2G）

- 第一个参数是起始位置
- 第二个参数是传输数据大小
- 第三个参数是传输目的

```java
static String path1 = "src/main/java/com/chr/netty/FileChannel/word3.txt";
static String path2 = "src/main/java/com/chr/netty/FileChannel/word4.txt";

public static void main(String[] args) throws IOException {
    // 传输小文件（2G以下）
    TestTransferTo1();
    // 传输大文件（2G以上）
    TestTransferTo2();
}

private static void TestTransferTo1() {
        try (
                FileChannel inputChannel = new FileInputStream(path1).getChannel();
                FileChannel outputChannel = new FileOutputStream(path2).getChannel()
        ) {
            inputChannel.transferTo(0, inputChannel.size(), outputChannel);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```

如果文件超过2G，要分开传输

```java
static String path1 = "src/main/java/com/chr/netty/FileChannel/word3.txt";
static String path2 = "src/main/java/com/chr/netty/FileChannel/word4.txt";

public static void main(String[] args) throws IOException {
    // 传输小文件（2G以下）
    TestTransferTo1();
    // 传输大文件（2G以上）
    TestTransferTo2();
}

private static void TestTransferTo2() {
    try (FileChannel inputChannel = new FileInputStream(path1).getChannel();
         FileChannel outputChannel = new FileOutputStream(path2).getChannel()) {
        long size = inputChannel.size();
        for (Long left = size; left > 0; ) {
            // transferTo 返回的是实际传输的数量
            left -= inputChannel.transferTo(size - left, left, outputChannel);
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```



### 4.4 Path

jdk7 引入了 Path 和 Paths 类

- Path 用来表示文件路径
- Paths 是工具类，用来获取 Path 实例

```java
Path source = Paths.get("1.txt"); // 相对路径 使用 user.dir 环境变量来定位 1.txt

Path source = Paths.get("d:\\1.txt"); // 绝对路径 代表了  d:\1.txt

Path source = Paths.get("d:/1.txt"); // 绝对路径 同样代表了  d:\1.txt

Path projects = Paths.get("d:\\data", "projects"); // 代表了  d:\data\projects
```



- `.` 代表了当前路径
- `..` 代表了上一级路径

例如目录结构如下

```java
d:
	|- data
		|- projects
			|- a
			|- b
```



代码

```java
Path path = Paths.get("d:\\data\\projects\\a\\..\\b");
System.out.println(path);
System.out.println(path.normalize()); // 正常化路径
```



会输出

```
d:\data\projects\a\..\b
d:\data\projects\b
```



### 4.5 Files

#### 4.5.1 检查文件是否存在

```java
Path path = Paths.get("helloword/data.txt");
System.out.println(Files.exists(path));
```



#### 4.5.2 创建一级目录

```java
Path path = Paths.get("helloword/d1");
Files.createDirectory(path);
```



- 如果目录已存在，会抛异常 FileAlreadyExistsException
- 不能一次创建多级目录，否则会抛异常 NoSuchFileException



#### 4.5.3 创建多级目录

```java
Path path = Paths.get("helloword/d1/d2");
Files.createDirectories(path);
```



#### 4.5.4 拷贝文件

```java
Path source = Paths.get("helloword/data.txt");
Path target = Paths.get("helloword/target.txt");

Files.copy(source, target);
```



- 如果文件已存在，会抛异常 FileAlreadyExistsException

如果希望用 source 覆盖掉 target，需要用 StandardCopyOption 来控制

```java
Files.copy(source, target, StandardCopyOption.REPLACE_EXISTING);
```



#### 4.5.5 移动文件

```java
Path source = Paths.get("helloword/data.txt");
Path target = Paths.get("helloword/data.txt");

Files.move(source, target, StandardCopyOption.ATOMIC_MOVE);
```



- StandardCopyOption.ATOMIC_MOVE 保证文件移动的原子性

#### 4.5.6 删除文件

```java
Path target = Paths.get("helloword/target.txt");

Files.delete(target);
```



- 如果文件不存在，会抛异常 NoSuchFileException



#### 4.5.7 删除目录

```java
Path target = Paths.get("helloword/d1");

Files.delete(target);
```



- 如果目录还有内容，会抛异常 DirectoryNotEmptyException



#### 4.5.8 遍历目录文件

```java
static void directoryCount() throws IOException {
        Path path = Paths.get("D:\\app\\jdk\\jdk-18.0.1.1");
        // 线程安全版的 int -> AtomicInteger
        AtomicInteger dirCount = new AtomicInteger();
        AtomicInteger fileCount = new AtomicInteger();

        // walkFileTree 两个参数，第一个path地址，第二个不同事件发生时干的事情
        Files.walkFileTree(path, new SimpleFileVisitor<>() {
            @Override
            // 当进入文件夹之前执行
            public FileVisitResult preVisitDirectory(Path dir, BasicFileAttributes attrs)
                    throws IOException {
                // 输出访问的目录
                System.out.println(dir);
                // 计数器+1
                dirCount.incrementAndGet();
                return super.preVisitDirectory(dir, attrs);
            }

            @Override
            // 访问到文件时执行
            public FileVisitResult visitFile(Path file, BasicFileAttributes attrs)
                    throws IOException {
                // 输出访问的目录
                System.out.println(file);
                // 计数器+1
                fileCount.incrementAndGet();
                return super.visitFile(file, attrs);
            }
        });
        System.out.println(dirCount); // 88
        System.out.println(fileCount); // 417
    }
```



`SimpleFileVisitor` 是 Java NIO 提供的一个**方便的工具类**，专门用来**遍历文件夹和文件**。
 **它本身是个适配器模式** —— 帮你把 `FileVisitor` 接口的四个方法**都默认实现**了（默认啥都不干，只是继续遍历）

| 方法名               | 什么时候触发        | 主要作用                               |
| -------------------- | ------------------- | -------------------------------------- |
| `preVisitDirectory`  | 访问一个目录之前    | 比如，想在进入目录前打印目录名         |
| `visitFile`          | 访问到一个文件时    | 比如，想在访问到文件时记录、统计、处理 |
| `visitFileFailed`    | 访问文件/目录失败时 | 比如，权限不足、文件不存在，处理异常   |
| `postVisitDirectory` | 访问完一个目录后    | 比如，目录扫描完成后做收尾工作         |



#### 4.5.9 统计 jar 的数目

```java
private static void statisticsJarCount() throws IOException {
        Path path = Paths.get("D:\\app\\jdk\\jdk-18.0.1.1");
        // 线程安全版的 int -> AtomicInteger
        AtomicInteger num = new AtomicInteger();
        Files.walkFileTree(path, new SimpleFileVisitor<>() {
            @Override
            public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) throws IOException {
                String pathName = file.getFileName().toString();
                int i = pathName.lastIndexOf('.');
                if (i != -1 && pathName.substring(i + 1).equals("jar")) {
                    num.incrementAndGet();
                    System.out.println(pathName);
                }
                return super.visitFile(file, attrs);
            }
        });
        System.out.println(num); // 1
    }
```



#### 4.5.10 删除多级目录

```java
Path path = Paths.get("d:\\a");
Files.walkFileTree(path, new SimpleFileVisitor<Path>(){
    @Override
    public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) 
        throws IOException {
        Files.delete(file);
        return super.visitFile(file, attrs);
    }

    @Override
    public FileVisitResult postVisitDirectory(Path dir, IOException exc) 
        throws IOException {
        Files.delete(dir);
        return super.postVisitDirectory(dir, exc);
    }
});
```



**⚠️ 删除很危险**



> 删除是危险操作，确保要递归删除的文件夹没有重要内容

#### 4.5.11 拷贝多级目录

```java
long start = System.currentTimeMillis();
String source = "D:\\Snipaste-1.16.2-x64";
String target = "D:\\Snipaste-1.16.2-x64aaa";

Files.walk(Paths.get(source)).forEach(path -> {
    try {
        String targetName = path.toString().replace(source, target);
        // 是目录
        if (Files.isDirectory(path)) {
            Files.createDirectory(Paths.get(targetName));
        }
        // 是普通文件
        else if (Files.isRegularFile(path)) {
            Files.copy(path, Paths.get(targetName));
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
});
long end = System.currentTimeMillis();
System.out.println(end - start);
```





## 5. 网络编程

### 5.1 阻塞和非阻塞

#### 5.1.1 阻塞

- 阻塞模式下，相关方法都会导致线程暂停
  - `ServerSocketChannel.accept` 会在没有连接建立时让线程暂停
  - `SocketChannel.read` 会在没有数据可读时让线程暂停
  - 阻塞的表现其实就是线程暂停了，暂停期间不会占用 cpu，但线程相当于闲置
- 单线程下，阻塞方法之间相互影响，几乎不能正常工作，需要多线程支持
- 但多线程下，有新的问题，体现在以下方面
  - 32 位 jvm 一个线程 320k，64 位 jvm 一个线程 1024k，如果连接数过多，必然导致 OOM，并且线程太多，反而会因为频繁上下文切换导致性能降低
  - 可以采用线程池技术来减少线程数和线程上下文切换，但治标不治本，如果有很多连接建立，但长时间 inactive，会阻塞线程池中所有线程，因此不适合长连接，只适合短连接



服务器端：

1. 创建`ServerSocketChannel`，服务端通道，用于监听客户端连接
2. 绑定监听端口
3. `ssc.accept()`获取与客户端与服务器的连接`SocketChannel`
4. 可以通过连接`SocketChannel`获取或发送数据

```java
public static void main(String[] args) throws IOException {
        // 使用 nio 来理解阻塞模式, 单线程
        // 0. ByteBuffer
        ByteBuffer buffer = ByteBuffer.allocate(16);
        // 1. 创建了服务器
        ServerSocketChannel ssc = ServerSocketChannel.open();

        // 2. 绑定监听端口
        ssc.bind(new InetSocketAddress(8080));

        // 3. 连接集合
        List<SocketChannel> channels = new ArrayList<>();
        while (true) {
            // 4. accept 建立与客户端连接， SocketChannel 用来与客户端之间通信
            log.debug("已准备建立连接...");
            SocketChannel sc = ssc.accept(); // 阻塞方法，线程停止运行
            log.debug("{} 连接", sc);
            channels.add(sc);

            for (SocketChannel channel : channels) {
                // 5. 接收客户端发送的数据
                log.debug("接收 {} 消息之前", channel);
                channel.read(buffer); // 阻塞方法，线程停止运行
                buffer.flip();
                debugRead(buffer);
                buffer.clear();
                log.debug("接收 {} 消息之后", channel);
            }
        }
    }
```

客户端：

1. 创建`SocketChannel`
2. 设置连接服务器地址
3. 手动发送数据

```java
public static void main(String[] args) throws IOException {
    SocketChannel socketChannel = SocketChannel.open();
    socketChannel.connect(new InetSocketAddress("localhost", 8080));
    System.out.println("消息发送之前发送...");
}
```

<font color="red">阻塞模式下，会让多线程连接发送消息无法实时达到</font>



#### 5.1.2 非阻塞

- 非阻塞模式下，相关方法都会不会让线程暂停
  - 在 `ServerSocketChannel.accept` 在没有连接建立时，会返回 null，继续运行
  - `SocketChannel.read` 在没有数据可读时，会返回 0，让线程不必阻塞，可以去执行其它 `SocketChannel` 的 `read`或是去执行 `ServerSocketChannel.accept`
  - 写数据时，线程只是等待数据写入 `Channel `即可，无需等 `Channel `通过网络把数据发送出去
- 但非阻塞模式下，即使没有连接建立和可读数据，线程仍然在不断运行，白白浪费了 cpu
- 数据复制过程中，线程实际还是阻塞的（AIO 改进的地方）



非阻塞服务器端代码增加两行：让`SocketChannel`和`ServerSocketChannel`设置为非阻塞

```java
public static void main(String[] args) throws IOException {
        // 使用 nio 来理解非阻塞模式, 单线程
        // 0. ByteBuffer
        ByteBuffer buffer = ByteBuffer.allocate(16);
        // 1. 创建了服务器
        ServerSocketChannel ssc = ServerSocketChannel.open();
        // 设置为非阻塞模式
        ssc.configureBlocking(false);
        // 2. 绑定监听端口
        ssc.bind(new InetSocketAddress(8080));
        // 3. 连接集合
        List<SocketChannel> channels = new ArrayList<>();
        while (true) {
            // 4. accept 建立与客户端连接， SocketChannel 用来与客户端之间通信
            // 非阻塞，线程还会继续运行，如果没有连接建立，但sc是null
            SocketChannel sc = ssc.accept(); 
            if (sc != null) {
                log.debug("{} 连接", sc);
                // 设置为非阻塞模式
                sc.configureBlocking(false); 
                channels.add(sc);
            }
            for (SocketChannel channel : channels) {
                // 5. 接收客户端发送的数据
                // 非阻塞，线程仍然会继续运行，如果没有读到数据，read 返回 0
                int read = channel.read(buffer);
                if (read > 0) {
                    buffer.flip();
                    debugRead(buffer);
                    buffer.clear();
                    log.debug("接收 {} 消息之后", channel);
                }
            }
        }
    }
```

<font color="red">非阻塞模式下，可以及时处理连接和发送的消息，但是如果没有连接和消息，线程会不停空转，CPU负载大</font>
