# Java 虚拟机

官方规范：

https://docs.oracle.com/javase/specs/index.html

语言热度：

https://www.tiobe.com/tiobe-index/

Java发展重大事件

![image-20230623113358006](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306231134222.png)

![image-20230623113617687](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306231136842.png)

JVM的位置：

运行在操作系统之上，它与硬件没有直接的交互

![image-20230623114044534](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306231140594.png)

![image-20230623114658062](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306231146198.png)

## 1. JVM 整体架构

![image-20230623114826333](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306231148426.png)

方法区和堆：多线程共享的

栈、本地方法栈、程序计数器：每个线程独享一份的

执行引擎【高级语言翻译成机器语言】包含三部分：(特点：解释执行、即时编译并存的这一种方式)

- 解释器
- JIT Compiler（JIT 即时编译器）--- 编译器的后端
- GC（垃圾回收器）

## 2. Java代码执行流程

![image-20230624125002377](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306241250465.png)

操作系统只能识别0101 的机器指令

## 3. JVM 架构模型

![image-20230624131226717](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306241312908.png)

![image-20230624131252066](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306241312137.png)

```java
package com.llq;

public class StackStruTest {
    public static void main(String[] args) {
        int i = 2;
        int j = 3;
        int k = i + j;
    }
}
```

反编译

```java
javap -v StackStruTest.class
```

![image-20230624132334704](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306241323827.png)

总结：

![image-20230624132628310](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306241326474.png)

## 4. JVM的生命周期

### 4.1 启动

Java虚拟机的启动是通过引导类加载器(bootstrap class loader) 创建一个初始类(initial class) 来完成的，这个类是由虚拟机的具体实现指定的。



### 4.2 执行

- 一个运行中的Java虛拟机有着一个清晰的任务:执行Java程序。
- 程序开始执行时他才运行，程序结束时他就停止。
- 执行一个所谓的Java程序的时候，真真正正在执行的是一个叫做 <font color="yellow">Java**虛拟机的进程**</font>。

```java
package com.llq;

public class StackStruTest {
    public static void main(String[] args) {
        int i = 2;
        int j = 3;
        int k = i + j;

        try {
            Thread.sleep(6000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

```bash
jps
```

![image-20230624154414287](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306241544360.png)

### 4.3 退出

有如下的几种情况:

- 程序正常执行结束
- 程序在执行过程中遇到了异常或错误而异常终止.
- 由于操作系统出现错误而导致Java虚拟机进程终止
- 某线程调用Runtime类或System类的exit方法，或Runtime类的 halt方法，并且Java安全管理器也允许这次exit 或halt 操作。
- 除此之外，JNI ( Java Native Interface) 规范描述了用JNI Invocation API来加载或卸载Java虚拟机时，Java虚拟机的退出情况。

## 5. JVM 发展历程

1. 1996年 java1.0，SUN公司发布了一款名为 Sun Classic VM的 Java虚拟机【只有解释器（步行：响应时间），没有JIT（坐公交车：执行性能）】
2. JDK 1.2 Exact VM：直到内存中某个位置的数据具体是什么类型【有 JIT：进行热点探测】
3. HotSpot VM：热点代码探测技术：
   - 通过计数器找到最具编译价值的代码，触发即时编译活栈上替换
   - 通过编译器与解释器协同工作，在最优化的程序响应时间与最佳执行性能取得平衡
4. BEA 的 JRockit（专注于服务器端）：不包含解释器
5. IBM 的 J9

## 6. 内存结构

![image-20230624163133674](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306241631773.png)

如果自己手写一个 Java 虚拟机的话，主要考虑 2个方面：

- 类加载器
- 执行引擎

## 7. 类加载器与类的加载过程

![image-20230624163606073](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306241636145.png)

- 类加载器子系统负责从文件系统或者网络中加载Class文件，class文件在文件开头有特定的文件标识。
- ClassLoader只负责class文件的加载，至于它是否可以运行，则由ExecutionEngine决定。
- 加载的类信息存放于一块称为方法区的内存空间。除了类的信息外，方法区中还会存放 <font color="yellow">**运行时常量池信息**</font>，可能还包括字符串字面量和数字常量(这部分常量信息是Class文件中常量池部分的内存映射)

类加载器 ClassLoader角色

![image-20230624163922878](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306241639932.png)

1. class file 存在于本地硬盘上，可以理解为一个模板，而最终这个模板在执行的时候是要加载到 JVM当中，来根据这个模板，实例化出 n 个实例
2. class file 加载到 JVM  中，被称为 DNA元数据模板，放在方法区
3. 在 .class 文件 ==> JVM ==> 最终称为元数据模板，此过程就要一个运输工具（类装载器 ClassLoader），扮演一个快递员的角色

### 7.1 加载

1. 通过一个类的全限定名获取定义此类的二进制字节流
2. 将这个字节流所代表的静态存储结构转化为方法区的运行时数据结构
3. <font color="yellow">**在内存中生成一个代表这个类的 **java. lang.Class对象</font>，作为方法区这个类的各种数据的访问入口

补充：加载 .class 文件的方式

- 从本地系统中直接加载
- 通过网络获取，典型场景：Web Applet
- 从 zip 压缩包中读取，称为日后 jar、war格式的基础
- 运行时计算，使用最多的是：动态代理技术（Proxy）
- 由其它文件生成，典型应用：JSP应用
- 从专有数据库中提取 .class 文件，比较少见
- 从加密文件中获取，典型防 Class 文件被反编译的保护措施

### 7.2 链接

![image-20230624180444654](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306241804820.png)

验证

![image-20230624181703822](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306241817864.png)

准备：

```java
package com.llq;

public class HelloApp {
    private static int a = 1;   //  prepare：a = 0 ---> initial：a = 1

    public static void main(String[] args) {
        System.out.println(a);
    }
}
```

解析：

事实上，解析操作往往会伴随着 JVM在执行完初始化操作后再执行的！

### 7.3 初始化

![image-20230624190829105](C:\Users\李隆齐\AppData\Roaming\Typora\typora-user-images\image-20230624190829105.png)

任何一个类声明以后，内部至少存在一个类的构造器 ===> 对应 \<init> 方法

![image-20230624185135929](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306241851121.png)

![image-20230624185255277](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306241852393.png)

## 8. 类加载分类

![image-20230624191134355](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306241911426.png)

除了 1（C、C++ 编写的） 都是 2（Java 写的）

![image-20230624191245821](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306241912899.png)

```java
public class ClassLoaderTest {
    public static void main(String[] args) {
        //  获取系统类加载器
        ClassLoader systemClassLoader = ClassLoader.getSystemClassLoader();
        System.out.println(systemClassLoader);  //  jdk.internal.loader.ClassLoaders$AppClassLoader@3fee733d
        //  获取其扩展类加载器
        ClassLoader extClassLoader = systemClassLoader.getParent();
        System.out.println(extClassLoader); //  jdk.internal.loader.ClassLoaders$PlatformClassLoader@10f87f48
        //  试图再获取其上层：获取不到引导类加载器
        ClassLoader bootstrapClassLoader = extClassLoader.getParent();
        System.out.println(rapClassLoader);   //  null

        //  对于用户自定义类来说：使用系统类加载器进行加载
        ClassLoader classLoader = ClassLoaderTest.class.getClassLoader();
        System.out.println(classLoader);    //  jdk.internal.loader.ClassLoaders$AppClassLoader@3fee733d

        //  String 类使用引导类加载器进行加载的 ---> Java 的核心类库都是这样加载的
        ClassLoader classLoader1 = String.class.getClassLoader();
        System.out.println(classLoader1);   //  null
    }
}
```

![image-20230625184127613](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306251841724.png)

获取 BootStrapClassLoader 能够加载的 api 的路径

![image-20230625184336075](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306251843169.png)

![image-20230625184456564](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306251844669.png)

为什么要自定义类加载器：

- 隔离加载类
- 修改类加载的方式
- 修改加载源
- 防止源码泄露

用户自定义类加载器实现步骤

1. 开发人员可以通过继承抽象类java. lang.ClassLoader类的方式，实现自己的类加载器，以满足- -些特殊的需求
2. 在JDK1.2之前， 在自定义类加载器时，总会去继承ClassLoader类并重写loadClass ()方法，从而实现自定义的类加载类，但是在JDK1.2之后已不再建议用户去覆盖loadClass()方法，而是建议把自定义的类加载逻辑写在 <font color="yellow">findClass()方法 </font>中
3. 在编写自定义类加载器时，如果没有太过于复杂的需求，可以直接继承URLClassLoader类，这样就可以避免自己去编写findClass()方法及其获取字节码流的方式，使自定义类加载器编写更加简洁。

关于 ClassLoader

- 是一个抽象类，其后所有的类加载器都继承自 ClassLoader（不包括启动类加载器）

- 常用方法

  | 方法名称                                         | 描述                                                         |
  | ------------------------------------------------ | ------------------------------------------------------------ |
  | getParent()                                      | 返回该类加载器的超类加载器                                   |
  | loadClass(String name)                           | 加载名你为name的类，返回结果为java.lang.Class类的实例        |
  | findClass(String name)                           | 查找名称为name的类，返回结果为java.lang.Class类的实例        |
  | findLoadedClass(String name)                     | 查找名称为name的已经被加载过的类，返回结果为java.lang.Class类的实例 |
  | defineClass(String name,byte[]b,int off,int len) | 把字节数组b中的内容转换为一个Java类，返回结果为java.lang.Class类的实例 |
  | resolveClass(Class<?> c)                         | 连接指定的一个Java类                                         |

![image-20230625190919530](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306251909732.png)

获取 ClassLoader 的途径

1. class.forName("路径").getClassLoader()

2. 获取当前线程上下文的 ClassLoader

   ```java
   Thread.currentThread().getContextClassLoader();
   ```

3. 获取系统的类加载器

   ```java
   ClassLoader.getSystemClassLoader();
   ```

4. 获取调用者的 ClassLoader

   ```java
   DriverManager.getCallerClassLoader();
   ```

## 9. 双亲委派机制（⭐）---> 孔融让梨

- Java虚拟机对 class 文件采用的是 <font color="yellow">**按需加载** </font>的方式，也就是当需要使用该类时，才会将它的 class 文件加载到内存生成 class对象
- 而且加载到某个类的 class 文件时，Java 虚拟机采用的是 <font color="yellow">**双亲委派机制**</font>，即把请求交由父类处理，他是一种任务委派模式

构建一个错误场景：

![image-20230625192611538](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306251926785.png)

![image-20230625201006265](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306252010436.png)

![image-20230625201553879](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306252015161.png)

优势：

- 避免类的重复加载
- 保护程序安全，防止核心 API 被随意篡改
  - 自定义类：java.lang.String
  - 自定义类；java.lang.ShkStart

## 10. 沙箱安全机制（⭐）

保证对 Java 核心源代码的保护

![image-20230625202102190](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306252021260.png)

注意：在JVM 中表示2个 Class 对象是否为同一个类存在2个必要条件：

- 类的完整名必须一致，包括包名
- 加载这个类的 ClassLoader（指 ClassLoader 实例对象）必须相同

换句话，在 JVM 中，即使这两个类对象（class 对象）来源同一个 Class 文件，被同一个虚拟机所加载，但只要加载它们的 ClassLoader实例对象不同，那么这2个类对象也是不相等的。

## 11. 对类加载器的引用

JVM必须知道一个类型是由

- 启动类加载器（bootstrapClassLoader）
- 还是由 <font color="yellow">**用户类加载器**</font> ===> 那么 JVM 会将这个 <font color="red">**类加载器的一个引用** </font>作为类型信息的一部分保存在 <font color="yellow">**方法区** </font>中。

当解析一个类型到另一个类型的引用的时候，JVM 需要保证这 两个类型的类加载器是相同的。

## 12. 类的主动使用和被动使用

主动使用：

- 创建类的实例
- 访问某个类活接口的静态变量，或者对该静态变量赋值
- 调用类的静态方法
- 反射【Class.forName("com.llq.test")】
- 初始化一个类的子类
- Java虚拟机启动时被标明为启动类的类
- JDK7 开始提供的动态语言支持：
  - java.lang.invoke.MethodHandler 实例的解析结果
  - REF_getStatic
  - REF_invokeStatic句柄对应的类没有初始化，则初始化

被动使用：

​	除了上面 7 种情况，其它使用 Java类的方式都被看做是对类的被动使用，都不会导致类的初始化

## 13. 运行时数据区概述以及线程

![image-20230628092133013](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306280921168.png)

![image-20230628092150759](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306280921820.png)

不同的 JVM 对于内存的划分方式和管理机制存在着部分差异

![image-20230628092654322](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306280926661.png)

Java虚拟机定义了若干种程序运行期间会使用到的运行时数据区：

- 其中有一些会随着虚拟机启动而创建，随着虚拟机退出而销毁【一个虚拟机对应一个进程】
- 另外一些则是与线程一一对应，这些与线程对应的数据区域会随着线程开始和结束而创建和销毁

![image-20230628093250643](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306280932723.png)

![image-20230628093537785](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306280935876.png)

垃圾回收：

- 堆：95%
- 方法区：5%【JDK8 后面改叫 元空间了 ===> 使用的是本地内存】

在 HotSpot JVM里，每个线程都与操作系统的本地线程直接映射：

- 当一个 Java线程准备好执行以后，此时操作系统的本地线程也同时创建
- Java线程执行终止后，本地线程也会回收

操作系统负责所有线程的安排调度到任何一个可用的CPU 上。一旦本地线程初始化成功，它就会调用 Java 线程种的 run() 方法

JVM 系统线程：

![image-20230628094757747](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306280947881.png)

### 1. 程序计数器（PC 寄存器）---> 数据库结果集种的游标、集合中的迭代器

![image-20230628095218872](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306280952992.png)

作用：PC 寄存器用来存储指向下一条指令的地址，也即将要执行的指令代码。由执行引擎读取下一条指令

![image-20230628095526167](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306280955249.png)

在 JVM 规范中：

- 每个线程都有它自己的程序计数器，是线程私有的，生命周期与线程的声明周期保持一致。
- 任何时间一个线程只有一个方法在执行，也就是所谓的 <font color="yellow">**当前方法**</font>，程序计数器会存储当前线程正在执行的 Java 方法的 JVM指令地址，或者：如果是在执行 native 方法，则是未指定值（undefined）
- 它是程序控制流的指示器，分支、循环、跳转、异常处理、线程恢复等基础功能都需要依赖这个计数器来完成
- 字节码解释器工作时就是通过改变这个计数器的值来选取下一个需要执行的字节码指令
- 它是唯一一个在 Java 虚拟机规范中没有规定任何 OutOfMemoryError 情况的区域

![image-20230628100626420](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306281006514.png)

方法区、堆会有：GC

PC计数器：无 OOM

举例说明：

```java
package com.llq.java1;

public class PCRegisterTest {
    public static void main(String[] args) {
        int i = 10;
        int j = 20;
        int k = i + j;

        String s = "abc";
        System.out.println(i);
        System.out.println(k);
    }
}
```

反编译：

```java
javap -v PCRegisterTest.class
```

第一列：指令地址（偏移地址）

第二列：操作指令

![image-20230628104552009](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306281045074.png)

![image-20230628104227128](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306281042340.png)

### 2. 2个常见问题

1. 使用 PC寄存器存储字节码指令地址有什么用呢？【为什么使用PC寄存器记录当前线程的执行地址呢？】		
   - 因为 CPU 要不停地切换各个线程，这时候切换回来后，就得知道接着从哪里继续执行
   - JVM 的字节码解释器需要通过改变 PC 寄存器的值来明确下一条应该执行什么样的字节么指令

2. PC 寄存器为什么会被设定为线程私有？
   - 为了能够准确地记录各个线程正在执行的当前字节码指令地址，最好的办法自然是为每一个线程都分配一个 PC寄存器，避免出现相互干扰

CPU 时间片：

- CPU 分配给各个程序的时间。每个线程都会分配一个时间段，称作它的时间片

![image-20230628105802148](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306281058216.png)

### 3. 虚拟机栈【每个线程在创建时都会创建一个 JVM栈】

由于跨平台的设计，Java指令都是根据栈来设计的。不同的 CPU 架构不同，所以不能设计为基于寄存器的

比如：AMD / Intel

优缺点：

- 优点：跨平台，指令集小，编译器容器实现
- 缺点：性能下降，实现同样的功能需要更多的指令

栈是运行时的单位，而堆是存储的单位

- 栈：解决程序的运行问题，即程序如何执行，或是说如何处理数据
- 堆：解决的是数据存储的问题，即数据怎么放，放在哪

![image-20230628133156169](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306281331234.png)

Java虚拟机栈是什么?

- JVM栈，每个线程在创建时都会创建一个 JVM栈，其内部保存一个个的栈帧（Stack Frame），对应者一次次的 Java 方法调用
- 是线程私有的

![image-20230628172035086](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306281720158.png)

生命周期：

- 生命周期和线程一致

作用：

- 主管 Java 程序的运行，它保存方法的局部变量、部分结果，并参与方法的调用和返回

栈的特点：

![image-20230628133609073](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306281336151.png)

栈中出现的异常：

![image-20230628134003464](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306281340553.png)

设置栈内存大小：

```java
-Xss256k
```

![image-20230628173556157](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306281735212.png)

![image-20230628173704765](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306281737832.png)

```java
package com.llq.java1;
/**
 * 默认情况下：count: 20351
 * 设置栈的大小 -Xss256k，count：4004
 */
public class PCRegisterTest {
   private static int count = 1;
   public static void main(String[] args){
       System.out.println(count);
       count++;
       main(args);
   }
}
```

栈的大小直接决定了函数调用的最大可达深度

栈的存储单位

- 栈中存储什么？

  - 每个线程都有自己的栈，栈中的数据都是以 <font color="yellow">栈帧（Stack Frame）</font>的格式存在
  - 在这个线程上正在执行的每个方法都各自对应一个栈帧（Stack Frame）
  - 栈帧是一个内存区块，是一个数据集，维系着方法执行过程中的各种数据信息

- 栈运行原理

  - 入出栈

  - 在一条活动线程中，一个时间点上，只会有一个活动的栈帧。即只有当前正在执行的方法的栈帧（栈顶栈帧）是有效的，这个栈顶被称为 <font color="yellow">当前栈帧（Current Frame）</font>，与当前栈帧相对应的方法就是 <font color="yellow">当前方法（Current Method）</font>，定义这个方法的类就是<font color="yellow">当前类（Current Class）</font>

  - 执行引擎运行的所有字节码指令只针对当前栈帧进行操作

  - 如果在该方法中调用了其它方法，对应的新的栈帧就会被创建出来，放在栈的顶端，称为新的当前帧

    ![image-20230628135423333](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306281354400.png)

- 栈帧的内部结构

  每个栈帧中存储者：

  - <font color="red">局部变量表</font>（Local Variables）
  - <font color="red">操作数栈</font>（Operand Stack）（或表达式栈）
  - 动态链接（Dynamic Linking）（或指向运行时常量池的方法引用）
  - 方法返回地址（Return Address）（或方法正常退出或者异常退出的定义）
  - 一些附加信息

  ![image-20230628140214629](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306281402690.png)

  ![image-20230628140256453](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306281402515.png)

#### 局部变量表（LV）

- 定义为一个 <font color="yellow">数字数组</font>，主要用于存储方法参数和定义在方法体内的局部变量
- 局部变量表是建立在线程的栈上，是线程的私有数据，因此不存在数据安全问题
- 局部变量表所需容量大小是 <font color="yellow">在编译期确定 </font>下来的，并保存在方法的 <font color="yellow">**code**</font> 属性的 maxium local variables 数据项中，在方法运行时是不会改变局部变量表的大小的

关于 Slot 的理解：

- 参数值的存放总是在局部变量表数组的 index0 开始，到数组长度 -1 的索引结束
- 局部变量表，最基本的存储单元是 slot（变量槽）
- 局部变量表中存放编译期可知的各种基本数据类型（8种）、引用类型（reference）、returnAddress类型的变量
- 在局部变量表里，32位以内的类型只占用一个 slot（包括 returnAddress 类型）、64位的类型（long 和 double）占用2个 slot
  - byte、short、char 在存储前被转换为 int、boolean 也被转换为 int，0表示 false，1表示 true
  - long、doube 占据 2 个 slot

![image-20230629103452566](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306291034757.png)

Slot 的重复利用

![image-20230629104708540](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306291047666.png)

静态变量与局部变量的对比：

复习一下之前的知识：

按照数据类型分：

- 基本数据类型
- 引用数据类型

按照在类中声明位置：

- 成员变量：在使用前，都经历过默认初始化赋值
  - 类变量：Linking 的 prepared 阶段：给类变量默认赋值 ===> initial 阶段，给类变量显示赋值即静态代码块赋值
  - 实例变量：随着对象的创建，会在堆空间中分配实例变量空间，并进行默认赋值
- 局部变量：在使用前，必须进行显示赋值，否则编译不通过

![image-20230629110043527](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306291100639.png)

#### 操作数栈（OS）

每个独立的栈帧中除了包含局部变量表以外，还包含一个 操作数栈，也称作表达式栈

操作数栈：在方法执行过程中，根据字节码指令，在栈中写入数据或提取数据，即：入栈 / 出栈

![image-20230629153239386](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306291532439.png)

举例子：

```java
public class PCRegisterTest {
   public static void main(String[] args){
       byte i = 15;
       int j = 8;
       int k = i + j;
   }
}
```

![image-20230629153510527](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306291535634.png)

- 操作数栈，主要用于保存计算过程中的 <font color="yellow">**中间结果**</font>，同时作为计算过程中变量临时的存储空间
- 操作数栈就是 JVM 执行引擎的一个工作区，当一个方法刚开始执行的时候，一个新的栈帧也会随之被创建出来，<font color="yellow">**这个方法的操作数栈是空的**</font>。
- 每一个操作数栈都会拥有一个明确的栈深度用于存储数值，其所需的最大深度在编译器就定义好了，保存在方法的 Code 属性中，为max_stack的值
- 栈中的任何一个元素都是可以任意的 Java 数据类型
  - 32bit 的类型占用一个栈单位深度
  - 64bit 的类型占用两个栈单位深度
- 操作数栈 <font color="yellow">**并非采用索引的方式来进行数据访问** </font>的，而只能通过标准的入栈和出栈操作来完成一次数据访问
- 如果被调用的方法带有返回值的话，其返回值将会被压入当前栈帧的操作数栈中，并更新 PC寄存器中下一条需要执行的字节码指令
- 操作数栈中元素的数据类型必须与字节码指令的序列严格匹配，这由编译器在编译期间进行验证，同时在类加载过程中的类检验阶段的数据流分析阶段要再次验证
- 另外，我们说 JVM 的 <font color="red">**解释引擎是基于栈的执行引擎**</font>，其中栈指的就是操作数栈

我们来用代码追踪一下：

![image-20230702112040167](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307021120405.png)

![image-20230702111643748](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307021116900.png)

![image-20230702113246166](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307021132248.png)

![image-20230702113312877](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307021133958.png)

![image-20230702113624909](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307021136026.png)

![image-20230702114107145](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307021141264.png)

![image-20230702114133070](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307021141155.png)

如果被调用的方法带有返回值的话，其返回值将会被压入当前栈帧的操作数栈中，并更新PC寄存器中下一条需要执行的字节码指令

面试题：i++ 和 ++i 的区别【后续会讲】

栈顶缓存技术（Top-of-Stack Cashing）技术

- 基于栈式架构的 JVM 所使用的零地址指令更加紧凑，但完成一项操作的时候必然需要使用更多的入栈和出栈指令，这同时也就意味着需要更多的指令分派（instruction dispatch）次数和内存 读写次数
- 由于操作数是存储在内存中的，因此频繁地执行内存 读写操作会影响执行速度，为了解决这个问题，HotSpot JVM 设计者们提出了栈顶栈顶缓存（Tos，Top-of-Stack Cashing）技术，将栈顶元素全部缓存在物理 CPU 的寄存器中，以此降低堆内存的读写次数，提升执行引擎的执行效率

#### 动态链接（DL）--->  指向运行时常量池的方法引用

![image-20230702130001873](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307021300975.png)

- 每一个栈帧内部都包含一个指向 <font color="blue">**运行时常量池** </font>中 <font color="red">**该栈帧所属方法的引用**</font>，包含这个引用的目的就是为了支持当前方法的代码能够实现动态链接（Dynamic Linking），比如：invokedynamic指令
- 在 Java源文件被编译到字节码文件中时，所有的变量和方法都作为符号引用（symbolic Reference）保存在 class 文件的常量池里。比如：描述一个方法调用了另外的方法时，就是通过常量池中方法的符号引用来表示的，那么 <font color="red">动态链接的作用就是为了将这些符号引用转换为调用方法的直接引用</font>。

```java
package com.llq.java1;

public class DynamicLinkingTest {
    int num = 10;
    public void methodA(){
        System.out.println("methodA()...");
    }

    public void methodB(){
        System.out.println("methodB()...");
        methodA();
        num++;
    }
}
```

我们来关注下 methodB()

![image-20230702142923996](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307021429195.png)

我们再去常量池中去看下：

![image-20230702143257736](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307021432858.png)

![image-20230702143332163](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307021433333.png)

![image-20230702143945185](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307021439253.png)

![image-20230702144029330](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307021440410.png)



为什么需要常量池呢？

常量池的作用，就是为了提供一些符号和常量，便于指令的识别



方法的调用：

在 JVM 中，将符号引用转换为调用方法的直接引用与方法的绑定机制相关

- 静态链接：

  当一个字节码文件被装载进 JVM内部时，如果被调用的 <font color="yellow">**目标方法在编译器可知**</font>，且运行期保持不变时。这种情况下将调用方法的符号引用转换为直接引用的过程称之为静态链接。

- 动态链接：

  如果被调用的方法编译期无法被确定下来，只能够在程序运行期将调用方法的符号引用转换为直接引用，由于这种引用转换过程具备动态性，因此也就被称之为动态链接

对应的绑定机制为：早期绑定（Early Binding）和晚期绑定（Late Binding）。绑定是一个字段、方法或者类在 <font color="red">**符号引用** </font>被替换为 <font color="red">**直接引用**</font> 的过程，这仅仅发生一次

- 早期绑定

  被调用的目标方法如果在编译器可知，且运行期保持不变时，即可将这个方法与所属的类型进行绑定，这样一来，由于明确了被调用的目标方法究竟是哪一个，因此也就可以使用静态链接的方式将符号引用转换为直接引用

- 晚期绑定

  如果被调用的方法在编译器无法被确定下来，只能够在程序运行期根据实际的类型绑定相关的方法，这种绑定方式也就被称之为晚期绑定

```java
package com.llq.java2;

/**
 * 说明早期绑定 和 晚期绑定的例子
 */
public class Animal {
    public void eat(){
        System.out.println("动物进食");
    }
}

interface Huntable{
    void hunt();
}

class Odg extends Animal implements Huntable{
    @Override
    public void eat() {
        System.out.println("狗拿耗子，多管闲事");
    }

    @Override
    public void hunt() {
        System.out.println("狗吃骨头");
    }
}

class Cat extends Animal implements Huntable{
    @Override
    public void eat() {
        System.out.println("猫吃鱼");
    }

    @Override
    public void hunt() {
        System.out.println("猫抓老鼠");
    }
}
```

```java
package com.llq.java2;

public class AnimalTest {
    public void showAnimal(Animal animal){
        animal.eat();   //  晚期绑定
    }
    public void showHunt(Huntable huntable){
        huntable.hunt();    //  晚期绑定
    }
}
```

![image-20230702150436864](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307021504996.png)

![image-20230702150638994](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307021506107.png)

方法的调用：虚方法和非虚方法

- 非虚方法
  - 在编译期就确定了具体的调用版本，这个版本在运行时是不可变的
  - 静态方法、私有方法、final方法、实例构造器、父类方法都是非虚方法
- 虚方法

虚拟机提供了以下几条调用指令：

![image-20230630175640268](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306301756392.png)

关于 invokedynamic 指令

![image-20230630175746581](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306301757710.png)

动态类型语言和静态类型语言 ===> Java 本身是静态语言！！！

![image-20230630175841377](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202306301758471.png)

```java
package com.llq.java2;

/**
 * 体会 invokedynamic 指令
 */
interface Func{
    public boolean func(String str);
}

public class Lambda {
    public void lambda(Func func){
        return;
    }

    public static void main(String[] args) {
        Lambda lambda = new Lambda();
        Func func = str -> false;
        lambda.lambda(func);

        lambda.lambda(str -> false);
    }
}
```

![image-20230702162203952](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307021622143.png)

![image-20230702162359749](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307021623863.png)

可以看到字节码的 第8行对应 源文件的 第 21 行

![image-20230702162507679](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307021625806.png)



方法的调用：方法重写的本质

1. 找到操作数栈顶的第一个元素所执行的对象的实际类型，记作 C
2. 观察类型 C 中能否找到与常量中的描述符合简单名称都相符的方法
   - 找到了：进行访问权限校验
     - 如果通过则返回这个方法的直接引用，查找过程结束
     - 不通过：抛出 java.lang.IllegalAccessError 异常
   - 在类型C 中没找到，则按照继承关系，从下往上依次对 C 的各个父类进行 搜索和查找
   - 如果始终没有找到，抛出：java.lang.AbstractMethodError 异常

IllegalAccessError 介绍：

程序试图访问或修改一个属性或调用一个方法，这个属性或方法，你没有权限访问，一般的，这个会引起编译器异常。

这个错误如果发生在运行时，就说明一个类发生了不兼容的改变



虚方法表：

![image-20230702164519851](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307021645955.png)

#### 方法返回地址（RA）

存放调用该方法的 pc 寄存器【该方法下一条指令的值】的值

一个方法的结束，有 2 种方式：

- 正常执行完成
- 出现未处理的异常，非正常退出

无论通过哪种方式退出，在方法退出后都返回到该方法被调用的位置

- 方法正常退出时，<font color="red">调用者的 pc 计数器的值作为返回地址，即调用该方法的指令的下一条指令的地址</font>
- 而通过异常退出时，返回地址是要通过 <font color="yellow">**异常表**</font> 来确定，栈帧中一般不会保存这部分信息

当一个方法开始执行后，只有 2 种方式可以退出这个方法：

1. 执行引擎遇到任意一个方法返回的字节码指令（return），会有返回值传递给上层的方法调用者，即：<font color="yellow">正常完成出口</font>。

   在 Java 字节码中，areturn 指令用于从方法中返回一个引用类型的值。areturn 指令的操作数栈顶部应该是一个引用类型的值，该值将被返回给方法的调用者。

   areturn 指令通常出现在方法的返回语句中。例如，以下是一个返回字符串类型值的方法的字节码示例：

   ```java
   public static String getString(){
       return "Hello, World!";
   }
   ```

   ![image-20230704202447152](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307042024233.png)

   ldc：将字符串常量 "Hello, World!" 推送到操作数栈中

   areturn 指令：将该字符串常量作为返回值返回给方法的调用者

   其它如下：

   ireturn（boolean、byte、char、short、int）

   lreturn：long

   freturn：float

   dreturn：double

   return：void、构造器、类和接口的初始化方法

2. 在方法执行过程中遇到了异常（Exception），并且这个没有在方法内处理，也就是在本方法的异常表中没有搜索到匹配的异常处理器，就会导致方法退出。简称：<font color="yellow">异常完成出口</font>

   方法执行过程中抛出异常时的异常处理，存储在一个异常处理表，方便在发生异常时找到处理异常的代码。

#### 一些附加信息

栈帧中还允许携带与 JAVA 虚拟机实现相关的一些附加信息

#### 栈面试题

1. 举例栈溢出的情况？（StackOverflowError）【静态】

   通过 -Xss 设置栈的大小：OOM【动态】

2. 调整栈大小，就能保证不出现溢出么？

   不能

3. 分配栈内存越大越好么？

   不是

4. GC是否会涉及到虚拟机栈

   ![image-20230705182837954](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307051828027.png)

   | 分区       | Error | GC   |
   | ---------- | ----- | ---- |
   | PC         | ×     | ×    |
   | 虚拟机栈   | √     | ×    |
   | 本地方法栈 | √     | ×    |
   | 方法区     | √     | √    |
   | 堆         | √     | √    |

   不会 ===> 因为栈只有 push 和 pop 操作，无法通过 GC 优化

5. 方法中定义的局部变量是否线程安全？具体问题具体分析

   比如：实现线程不安全的 StringBuilder

   何为线程安全？

   - 如果只有一个线程才可以操作此数据，则必是线程安全的
   - 如果有多个线程操作此数据，则此数据是共享数据，如果不考虑同步机制的话，会存在线程安全问题

## 14. 本地方法接口

![image-20230704204728009](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307042047098.png)

什么是本地方法？

一个 Native Method 就是一个 Java 调用非 Java 代码的接口

- 在定义一个 native method时，并不提供实现体（有些像定义一个 Java Interface），因为其实现体是由非 Java 语言在外面实现的
- 本地接口的作用是融合不同的变成语言为 Java 所用，它的初衷是融合 C / C++ 程序

举例子：

```java
package com.llq.java1;

public class IHAVENatives {
    public native void methodNative1(int x);
    public native static long methodNative2();
    private native synchronized float methodNative3(Object o);
    native void methodNative4(int[] array) throws Exception;
}
```

表示符 native 可以与所有其它的 java 标识符连用，但是 <font color="yellow">abstract 除外</font>

为什么使用 Native Method?

- 与 Java环境外交互

- 与操作系统交互：通过本地方法，我们得以用 Java 实现了 JRE的与底层系统的交互，甚至 JVM 的一些部分就是用 C 写的

  ![image-20230705191400164](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307051914286.png)

- Sun's Java：Sun 的解释器是 C 实现的，这使得它能像一些普通的 C 一样与外部交互

现状：用的少了，除非是与硬件有关的应用，比如：

- 通过 Java 程序驱动打印机打印
- Java 系统管理生产设备

现在的异构领域间的通信很发达，比如可以使用 Scoket通信、也可以使用 Web Service 等等

## 15. 本地方法栈（运行时数据区）

- Java 虚拟机栈用于管理 Java方法的调用
- 而本地方法栈用于管理本地方法的调用

本地方法栈：也是线程私有的

允许被实现成固定或者是可动态扩展的内存大小。（在内存溢出方面是相同的）

- 如果线程请求分配的栈容量超过本地方法栈允许的最大容量，Java虚拟机将会抛出一个 StackOverflowError 异常
- 如果本地方法栈可以动态扩展，并且在尝试扩展的时候无法申请到足够的内存，或者在创建新的线程时没有足够的内存去创建对应的本地方法栈，那么 Java虚拟机将会抛出一个 OutOfMemoryError 异常

本地方法是使用 C 语言实现的

它的具体使用 C 语言实现的

它的具体做法是 Native Method Stack 中登记 native 方法，在 Execution Engine 执行时加载本地方法库

![image-20230705203732449](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307052037537.png)

当某个线程调用一个本地方法时，它就进入了一个全新的并且不再受虚拟机限制的世界。它和虚拟机拥有同样的权限：

- 本地方法可以通过本地方法接口来 访问虚拟机内部的运行数据区
- 它甚至可以直接使用本地处理器中的寄存器
- 直接从本地内存的堆中分配任意数量的内存

并不是所有的 JVM 都支持本地方法。因为 Java 虚拟机规范并没有明确要求本地方法栈的使用语言、具体实现方式、数据结构等。

如果 JVM 产品不打算支持 native 方法，也可以无需实现本地方法栈

在 HotSpot JVM中，直接将本地方法栈和虚拟机栈合二为一。

## 16. 堆

### 1. 堆的核心描述

![image-20230705205723182](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307052057272.png)

一个 JVM 实例只存在一个堆内存，堆也是 Java 内存管理的核心区域

Java 堆区在 JVM 启动的时候被创建，其空间大小也就确定了。是 JVM 管理的最大一块内存空间【<font color="yellow">**堆内存的大小是可以调节** </font>的】

《Java 虚拟机规范》规定：堆可以处理物理上不连续的内存空间，但在逻辑上它应该被视为连续的

所有的线程共享 Java堆，在这里还可以划分线程私有的缓冲区（Thread Local Allocation Buffer, TLAB）

《Java虚拟机规范》中对 Java堆的描述是：所有的对象实例以及数组都应当在运行时分配在堆上。

- 几乎所有的对象实例都在这里分配内存。
- 数组和对象可能永远不会存储在栈上，
- 在方法结束后，堆中的对象不会马上被移除，仅仅在 GC 的时候，才会被移除
- 堆，是 GC 执行垃圾回收的重点区域

### 2. 设置堆内存大小与OOM

```bash
# 初始值 max
-Xms10m -Xms10m
```

![image-20230727105221019](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307271052150.png)

在命令行中打开工具：jvisualvm

![image-20230727112514921](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307271125066.png)

### 3. 年轻代与老年代

![image-20230727114927706](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307271149812.png)

注意：元空间在方法区中了就

![image-20230727120248845](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307271202927.png)

Survivor0(S0)：也称为：from区

Survivor0(S1)：也称为：to区

<font color="yellow">S0 和 S1 只能使用一个</font>：涉及到垃圾回收算法

注意：我们设置的参数

```bash
-Xms10m -Xmx10m # 只涉及 年轻代和老年代

# 通常将 -Xms 和 -Xmx 2个参数配置成相同的值：
# 为了能够在 GC 清理完堆区后不需要重新分隔计算堆区的大小，从而提升性能
# 1. 初始大小：电脑物理内存的大小 / 64
# 2. 最大内存大小：电脑物理内存的大小 / 4
```

![image-20230727120428889](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307271204970.png)

```java
package com.llq.heap;

public class HeapSpaceInitial {
    public static void main(String[] args) {
        //  返回 JVM 中堆内存总量
        long initialMemory = Runtime.getRuntime().totalMemory() / 1024 / 1024;
        //  返回JVM 试图使用的最大堆内存量
        long maxMemory = Runtime.getRuntime().maxMemory() / 1024 / 1024;
        System.out.println("-Xms" + initialMemory + "M");
        System.out.println("-Xmx:" + maxMemory + "M");

        System.out.println("系统内存大小为：" + initialMemory * 64 / 1024 + "G");
        System.out.println("系统内存大小为：" + maxMemory * 4 / 1024 + "G");
    }
}
```

我们来构造一个 OOM 异常：（创建对象和数组都是放在堆空间中的）

```java
-Xms600m -Xmx600m
```

```java
package com.llq.heap;

class Picture{
    private byte[] pixels;

    public Picture(int length){
        this.pixels = new byte[length];
    }
}

public class OOMTest {
    public static void main(String[] args) {
       List<Picture> pictures = new ArrayList<>();
       while (true){
           try {
               Thread.sleep(20);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
           pictures.add(new Picture(new Random().nextInt(1024 * 1024)));
       }

    }
}
```

年轻代与老年代

![image-20230727173930131](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307271739235.png)

我们可以手动修改：新生代和老年代的比例

```bash
-XX:NewRatio=2 # 默认：新生代占1，老年代占2 ====> 新生代占整个堆的 三分之一
```

![image-20230727174210453](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307271742544.png)

还可以在命令行中查看：

```bash
jps # 找到进程号
jinfo -flag NewRatio 进程号
```

![image-20230727174806388](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307271748485.png)

```bash
-XX:SurvivorRatio=8 # 调整 eden 与 survivor 的比例 8:1:1
```



### 4. 图解对象分配过程

一般过程

![mnq](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307271815480.png)

总结：

- 针对幸存者 S0，S1区的总结：复制后有交换，谁空谁是 to
- 关于垃圾回收：频繁在新生区收集，很少在老年区收集，几乎不在永久区 / 元空间收集

### 5. Minor GC、Major GC、Full GC

JVM 在进行GC时，并非每次都对上面3个内存（新生代、老年代、方法区）一起回收的，大部分时候回收的都是新生代

针对 HotSpot VM实现，GC按照回收区域又分为2大种类型

1. 部分收集（Partial GC）
   - 新生代收集（Minor GC / Yong GC）
   - 老年代收集（Major GC / Old GC）
     - 目前，只有 CMS GC会有单独收集老年代的行为
     - <font color="yellow">注意，很多时候 Major GC 会和 Full GC 混淆使用，需要分辨是老年代回收还是整堆回收</font>
   - 混合收集（Mixed GC）：整个新生代和部分老年代的垃圾收集
     - 目前，只有 G1 GC 会有这种行为
2. 整堆收集（Full GC）：收集整个 Java 堆和方法区的垃圾收集

年轻代GC 触发机制：年轻代【指的是 Eden代满，Survivor满不会引发 GC】空间不足时

Minor GC 会引发 STW（Stop the world），暂停其它用户的线程，等待 GC 结束，用户线程才恢复运行

### 6. 堆空间分代思想

![image-20230728005010537](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307280050637.png)

### 7. 内存分配策略

或称对象提升（Promotion）

### 8. 为对象分配内存：TLAB（Thread Local Allocation Buffer）

![image-20230728010515329](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307280105403.png)

![image-20230728010956315](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307280109396.png)

### 9. 小结堆空间的参数设置

https://docs.oracle.com/javase/8/docs/technotes/tools/windows/java.html

 测试堆空间常用的jvm参数:

- -XX:+PrintFlagsInitial:查看所有的参数的默认初始值
- -XX:+PrintFlagsFinal:查看所有的参数的最终值(可能会存在修改，不再是初始值)
- 具体查看某个参数的指令：
  1. jps 查看进程
  2. jinfo -flag SurvivorRatio 进程号
- -Xms:初始堆空间内存(默认为物理内存的1/64)
- -Xmx:最大堆空间内存(默认为物理内存的1/4)
- -Xmn:设置新生代的大小。(初始值及最大值)
- -XX:NewRatio:配置新生代与老年代在堆结构的占比
- -XX:SurvivorRatio:设置新生代中Eden和 S0/S1空间的比例
- -XX:MaxTenuringThreshold:设置新生代垃圾的最大年龄
- -XX:+PrintGCDetails:输出详细的Gc处理日志
- 打印gc简要信息:-xx:+PrintGC -verbose:gc
- -XX:HandlePromotionFailure:是否设置空间分配担保

### 10. 堆是分配对象的唯一选择么？（逃逸分析：栈）

逃逸分析（Escape Analysis），一个对象并没有逃逸出方法的话，那么就可能被优化成栈上分配 

逃逸分析的基本行为就是分析对象动态作用域：当一个对象在方法中被定义后

- 对象只在方法内部使用，则认为没有发生逃逸 ===> <font color="yellow">没有发生逃逸的对象，可以分配到栈上</font>，随着方法结束，栈空间就被移除
- 对象被外部方法所引用，则认为发生逃逸。（例如：作为调用参数传递到其它地方中）

![image-20230728154712391](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307281547538.png)

如何判断 <font color="yellow">**是否发生了逃逸分析**</font>：就看 new 的对象实体是否有可能在方法外被调用

结论：开发中能够使用局部变量的，就不要使用在方法外定义。

## 17. 方法区

### 1. 栈、堆、方法区的交互关系

![image-20230728161818490](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307281618593.png)

![image-20230728161938121](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307281619198.png)

### 2. 方法区的理解

方法区的大小决定了系统可以保存多少个类，如果系统定义了太多的类，导致方法区溢出，VM 同样会抛出内存溢出错误

- java.lang.OutOfMemoryError：PermGen
- java.lang.OutOfMemoryError：Metaspace

关闭 JVM 就会释放这个区域的内存

### 3. 设置方法区大小与 OOM

```bash
-XX:PermSize设置永久代初始分配空间（默认：20.75M）
-XX:MaxPermSize设置永久代最大可分配空间（32位：64M，64位：82M）
# jps
# jinfo -flag PermSize 进程号
# jinfo -flag MaxPermSize 进程号

# JDK8 中
-XX:MetaspaceSize	# 21M（Windows 系统下）
-XX:MaxMetaspaceSize	# -1 没有限制 
```

### 4. 方法区的内部结构

![image-20230728183345219](https://cdn.jsdelivr.net/gh/RonnieLee24/PicGo_Pictures@master/imgs/DB/202307281833318.png)

反编译 class 文件，并输出到 txt 文件中

```bash'
javap -v MethodInnerStrucTest.class > test.txt
```

补充：全局常量：static final：在编译的时候就会被分配了



### 5. 方法区使用举例

### 6. 方法区的演进细节

HotSpot 虚拟机

元空间和永久代最大区别：元空间不在虚拟机设置的内存中，而是使用本地内存

### 7. 方法区的垃圾回收

















