# 			7/13、文件输入/输出

### 一、流体系

1. 处理流的用法(构造器参数是已存在的流，就是处理流)

   ```java
   public class Demo1
   {
       public static void main(String[] args) {
           try (
               FileOutputStream fo=new FileOutputStream("emxaple.txt");
               PrintStream ps=new PrintStream(fo))
           {
               ps.println();
               ps.println(new Demo1());
           } catch (FileNotFoundException e) {
               e.printStackTrace();
           }
       }
   }
   ```



2. 访问字符串流：

   ```java
   String src="字符串流需要以字符数组作为节点传入";
   char[] buffer=new char[32];
   int hasRead=0;
   try(
     StringReader sr=new StringReader(src))
   {
       System.out.println(new String(buffer,0,hasRead));
   }  
   catch(IOException ioe)
     {
       ioe.printStackTrace();
     }
   try (
     StringWriter sw=new StringWriter())
   {
     sw.write("这是字符输出流");
     System.out.println(sw.toString());
   } catch (Exception e) {
     e.printStackTrace();
   }
   ```

3. 访问管道的流：

   - 实现进程之间的通信功能

   - ```java
     PipedInputStream
     ```

   - ```
     PipedOutputStream
     ```

   - PipedReader

   - PipedWriter

4. 缓冲流

   - 提高输入、输出效率，增加缓冲功能后需要使用flush()才可以将缓冲区的内容写入物理节点

5. 对象流

   - 实现对象序列化

#### 转换流

> 用于实现字节流转换成字符流

```java
public class Demo1
{
    public static void main(String[] args) {
        try (
            //InputStreamReader ir=new InputStreamReader();
            BufferedReader br= new BufferedReader(System.in))//这是运行不了的。因为BufferedReader()方法只能传入一个Reader参数，即是字符流。但是System.in是字节流。所以需要进行转换。为什么要用BufferReader方法呢，因为其具有缓冲功能，可以一次读取一行文本——以换行为标志，如果没有读到换行符，则程序阻塞，等读到换行符为止。而BufferedInputStream没有这个功能，所以读取文本内容很不方便，所以一般使用BufferedReader读取文本内容。
        {
            String line=null;
            while((line=br.readLine())!=null)
            {
                if(line.equals("exit"))
                {
                    System.exit(1);
                }
                System.out.println(line);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
```

#### 推回输入流（与众不同的流）

- PushbackInputStream和PushbackReader，都提供了三个方法

- 作用：重复读取刚刚读取的内容

- 原理：将字节/字符数组的内容推回缓冲区中

- 使用这两个流的read方法时，总是先从推回缓冲区取“水滴”，当不够时，再从原输入流里取

  ```java
  import java.io.*;
  import java.security.acl.LastOwnerException;

  public class Demo1
  {
      public static void main(String[] args) {

          try(
              PushbackReader ps=new PushbackReader(new FileReader("Demo1.java"),64))
              {
                  char[] buf=new char[32];
                  String lastcContext="";
                  int hasRead=0;
                  while((hasRead=ps.read(buf))>0)
                  {
                      //将读取的内容转换成字符串
                      String context=new String(buf,0,hasRead);
                      int targetIndex=0;
                      //将上次读取的字符串和本次读取的字符串拼起来
                      if((targetIndex=(lastcContext+context).indexOf("new Push"))>0)
                      {
                          ps.unread((lastcContext+context).toCharArray());
                          if(targetIndex>32)
                          {
                              buf=new char[targetIndex];
                          }
                          //读取指定长度的内容（就是目标字符串之前的内容）
                          ps.read(buf,0,targetIndex);
                          System.out.println(new String(buf,0,targetIndex));
                          System.exit(0);
                      }
                      else
                      {
                          System.out.println(lastcContext);
                          lastcContext=context;
                      }
                  }
          }
          catch (IOException e)
          {
              e.printStackTrace();
          }
      }
  }

  ```



### 二、重定向标准输入/输出

- 三个方法
  - static void setErr(PrintStream err):
  - static void setIn(InputStream in)
  - static void setOut(PrintStream out)
- 重定向到文件输出

```java
public class Demo1
{
    public static void main(String[] args) {
        try (
            PrintStream ps=new PrintStream(new FileOutputStream("out.txt")))
        {
            System.setOut(ps);
            System.out.println("重定向向外输出");
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
}
}
```

- 重定向标准输入

  ```java
  import java.io.*;
  import java.security.acl.LastOwnerException;
  import java.util.Scanner;

  public class Demo1
  {
      public static void main(String[] args) {
          try (
              FileInputStream fis=new FileInputStream("poem.txt"))
          {
              System.setIn(fis);
              Scanner sc=new Scanner(System.in);
              sc.useDelimiter("\n");//只把回车作为分隔符，就是再加一行空
              while(sc.hasNext()) {
                  System.out.println(sc.next());
              }
          } catch (IOException e) {
              e.printStackTrace();
          }
      }
  }

  ```

### 四、Java虚拟机读取其他进程的数据

```java
import java.io.*;
import java.security.acl.LastOwnerException;
import java.util.Scanner;

public class Demo1
{
    public static void main(String[] args) throws IOException{
        //cmd运行javac命令，返回运行该命令的子进程
        Process p=Runtime.getRuntime().exec("javac");
        try (
            BufferedReader br=new BufferedReader(new InputStreamReader(p.getErrorStream()) ))//必须是错误流，获取子进程的输入流则显示空白
            {
                String buff=null;
                //采取循环方法读取P进程的错误输出
                while((buff=br.readLine())!=null)
                {
                    System.out.println(buff);
                }
        }catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

通过一个类调用其他进程启动另外一个类

```java

```



### 五、RandomAccessFile

- 如果只需要访问文件的部分内容，可以使用

- 只能在文件的最后追加内容

- 只能读写文件，没办法读写其他IO节点

- 方法

  - long getFilePointer():返回文件记录指针的当前位置
  - void seek(long pos):定位指针到pos位置

- mode参数（访问模式）

  - r:只读
  - rw：读写方式打开
  - rws：读写方式打开，还要求对文件的内容或者元数据的更新都同步到底层存储设备
  - rwd：......

- 构造方法

  ```java
  RandomAccessFile raf=new RandomAccessFile("Demo1.java","r");
  ```



### 六、对象序列化

- ```java
  ObjectOutputStream oos=new ObjectOutputStream(new FileOutputStream("file.txt"));
  Person per=new Person("lin",20);
  oos.writeObject(per);
  ```

- ```java
  ObjectInputStream ois=new ObjectInputStream(new FileInputStream("file.txt"));

  Person per=(Person)ois.readObject(per);
  ```



### 七、NIO

1. 使用Buffer

   - 创建方法：

     ```java
     static XxxBuffer allocate(int capacity);//实际ByteBuffer和CharBuffer用得较多
     ```

   - capacity：创建后不能改变，最大数据容量

   - limit：位于其后的数据不可被读写

   - position：指明下一个可被读出或写入缓冲区位置索引

   - mark：...

     - 0《mark《position《limit《capacity

   - 两个重要方法

     - **flip()**:为从Buffer中取出的数据做好准备
     - **clear()**：再次向Buffer中装入数据做好准备

   - 所有子类有两个重要方法：get、put

2. 使用Channel

   - Channel可以直接将指定文件部分或全部直接映射成Buffer

   - 程序不能直接访问Channel中的数据，包括读取、写入都不行。如果要从Channel中取得数据，必须先用Buffer从Channel中取出一些数据，然后让程序从Buffer中取出这些数据。

   - 实例：

     ```java
     import java.io.File;
     import java.io.FileInputStream;
     import java.io.FileNotFoundException;
     import java.io.FileOutputStream;
     import java.nio.CharBuffer;
     import java.nio.MappedByteBuffer;
     import java.nio.channels.FileChannel;
     import java.nio.charset.Charset;
     import java.nio.charset.CharsetDecoder;

     /**
      * Created by K Lin on 2017/7/13.
      */
     public class FileChannelTest {
         public static void main(String[] args) {

             File f=new File("FileChannelTest.java");
             try (FileChannel inChannel = new FileInputStream(f).getChannel();
                     FileChannel outChannel = new FileOutputStream("a.txt").getChannel())
             {
                 //将FileChannel里面的全部数据映射成ByteBuffer
                 MappedByteBuffer buffer=inChannel.map(FileChannel.MapMode.READ_ONLY,0,f.length());

                 //直接将Buffer里的数据全部输出
                 outChannel.write(buffer);
                 //再次调用buffer里面的clear()方法，复原limit、position位置
                 buffer.clear();
                 //使用GBK里面的字符集来创建解码器
                 Charset charset=Charset.forName("GBK");
                 //创建解码器(CharsetDecoder)对象
                 CharsetDecoder decoder=charset.newDecoder();
                 //使用解码器将ByteBuffer转换成CharBuffer
                 CharBuffer charBuffer=decoder.decode(buffer);
                 //CharBuffer的toString方法可以获取对应的字符串
                 System.out.println(charBuffer);
             } catch (Exception e){
                 e.printStackTrace();
             }
         }
     }
     ```

3. 字符集和Charset

4. 文件锁

   - FileChannel中提供的lock/tryLock方法，lock是有可能阻塞的，而tryLock不会。获取的是排他锁
   - lock(long position,long size,boolean shared)，shared如果为false是排他锁，如果是true则是共享锁，允许多个进程读取该文件，但是阻止其他进程获得该文件的排他锁。
   - tryLock(long position,long size,boolean shared)
   - 文件锁虽然可用于控制并发访问，但是对于高并发情况还是推荐用数据库保存信息
   - 注意点：
     - 平台之间有差异。
     - 在某些平台上关闭FileChannel时，会释放Java虚拟机在该文件上的所有锁，因此应该避免对同一个被锁定的文件打开多个FileChannel

### 八、NIO2

1. Path、Paths、Files核心API
   - Path：由Paths提供的get(String first,String...more)方法获取，主要是用于获取路径名而已
   - Files：对文件进行一系列的操作，简化了文件IO
2. 使用FileVisitor遍历文件和目录
   - 用于查找指定的文件
3. 使用WatchService监控文件变化
4. 访问文件属性
   - 可以进行简单的读取、修改操作



