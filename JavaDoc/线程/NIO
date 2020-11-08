

### NIO三大组件：Channel、Buffer和Selector



NIO的通道类似于流，但是有如下区别：
1、通道可以同时进行读写，而流只能读或者只能写。
2、通道可以实现异步读取数据。
3、通道可以从缓冲中读取数据，也可以写数据到缓冲。



缓冲区读/写：
读取数据到缓冲区，调用buffer.filp方法。从缓冲区中读取数据，调用buffer.clear或者buffer.compact方法。clear会清空缓冲区，compact只会清空已读的数据。



即可以从Channel写入到Buffer，也可以直接调用Buffer的put方法写到Buffer中。即可以从Channel中读取数据，也可以直接调用Buffer的get方法读取数据。



Selector用来检测多个NIO Channel，看看读写数据是否就绪。多个Channel以事件的方式注册到同一个Selector。



线程安全的写操作：FileWriter, RandomAccessFile, FileOutputStream, FileChannel



Socket中会进入阻塞状态的操作：
1、server socket的accept()
2、执行socket的输出流写数据
3、执行socket的输入流读数据
4、socket的getOutputStream()和getInputStream()



读取数据：FileInputStream –> BufferedInputStream –> DataInputStream –> 数据
写入数据：数据 –> DataOutputStream –> BufferedOutputStream –> FileOutputStream





#### Buffer

Java NIO 的 Buffer 用于和 NIO 的 Channel 进行交互。数据是从通道读入缓冲区，从缓冲区写入到通道中的。缓冲区本质上是一块可以写入数据，然后可以从中读取数据的内存。这块内存被包装成 NIO Buffer 对象，并提供了一组方法，用来方便的访问该块内存。



**用法：**

1）写入数据到 Buffer，从 Channel 读取到到缓冲区中，也可以调用 put 方法写入。

2）调用 flip() 方法，切换数据模式。

3）从 Buffer 中读取数据，一般从缓冲区读取数据写入到通道中，也可以调用 get 方法读取。

4）调用 clear() 方法或者 compact() 方法清空缓冲区。

当向 buffer 写入数据时，buffer 会记录下写了多少数据。一旦要读取数据，需要通过 flip() 方法将 Buffer 从写模式切换到读模式。在读模式下，可以读取之前写入到 buffer 的所有数据。一旦读完了所有的数据，就需要清空缓冲区，让它可以再次被写入。



**Buffer 的 capacity、position和limit**

缓冲区本质上是一块可以写入数据，然后可以从中读取数据的内存。这块内存被包装成 Buffer 对象，并提供了一组方法，用来方便的访问该块内存。为了理解 Buffer 的工作原理，需要熟悉它的三个属性:

- capacity
- position
- limit

position 和 limit 的含义取决于 Buffer 处在读模式还是写模式。不管 Buffer 处在什么模式，capacity 的含义总是一样的。这里有一个关于 capacity，position 和 limit 在读写模式中的说明：

![image-20201021142818191](E:\文档\知识文档\图片\image-20201021142818191.png)



**capacity**

作为一个内存块，Buffer 有一个固定的大小值，也叫"capacity"。你只能往里写 capacity 个 byte、long，char 等类型。一旦 Buffer 满了，需要将其清空（通过读数据或者清除数据）才能继续写数据往里写数据。



**position**

当你写数据到 Buffer 中时，position表示当前的位置，指向下一个写位置。初始 position 值为0。当一个 byte、long 等数据写到 Buffer 后，position会向前移动到下一个可插入数据的 Buffer 单元。position 最大可为 capacity –1 当读取数据时，也是从某个特定位置读。当将 Buffer 从写模式切换到读模式，position 会被重置为 0。当从 Buffer 的 position 处读取数据时，position 向前移动到下一个可读的位置。

当将 Buffer 从写模式切换到读模式，position 会被重置为 0。当从 Buffer 的 position 处读取数据时，position 向前移动到下一个可读的位置。



**limit**

在写模式下，Buffer 的 limit 表示你最多能往 Buffer 里写多少数据。写模式下，limit 等于 Buffer 的 capacity。当切换Buffer到读模式时，limit 表示你最多能读到多少数据。因此，当切换Buffer到读模式时，limit 会被设置成写模式下的 position 值。换句话说，你能读到之前写入的所有数据，即 limit 被设置成已写数据的数量，这个值在写模式下就是 position，因为position从0开始，就像上图的写入ABCD四个值，position指向第5个元素位置，即position是4，切换到读模式下，将position赋值给limit，即limit是4，表示最多能读取多少个元素。



**Buffer 的类型**

- ByteBuffer

- CharBuffer

- DoubleBuffer

- FloatBuffer

- IntBuffer

- LongBuffer

- ShortBuffer

- MappedByteBuffer







**有两种方式能清空缓冲区：**

1）clear() 方法会清空整个缓冲区。

2）compact() 方法只会清除已经读过的数据。任何未读的数据都被移到缓冲区的起始处，新写入的数据将放到缓冲区未读数据的后面。

```java
import java.io.*;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;

public class NIOBufferTest {
    public static void main(String[] args) {
        File fromFile = new File("D:\\FTP\\test.txt");
        File toFile = new File("D:\\FTP\\test_bak.txt");

        try (FileInputStream fis = new FileInputStream(fromFile);
             FileOutputStream fos = new FileOutputStream(toFile);
             // 1 获取通道
             FileChannel inChannel = fis.getChannel();
             FileChannel outChannel = fos.getChannel()) {
            // 2、分配指定大小的缓冲区
            ByteBuffer buffer = ByteBuffer.allocate(100);
    
            // 3、将通道中的数据读取到缓冲区中
            while (inChannel.read(buffer) != -1) {
                // 切换读数据模式
                buffer.flip();
    
                // 4、从缓冲区读取数据写入到通道中
                outChannel.write(buffer);
                buffer.clear();
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

}
```



【参考文献】

[【NIO】Buffer（缓冲区）](https://blog.csdn.net/yhl_jxy/article/details/79331010)
