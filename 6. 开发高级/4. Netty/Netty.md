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

// 读取数据到另一个ByteBuffer中
buf.get(otherBuf);
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

