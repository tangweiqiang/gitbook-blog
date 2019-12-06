# JVM笔记

## 1. jvm的内存模型

java的工作内存是cpu寄存器和高速缓存的抽象描述

* **程序计数器** 记录下一条指令的地址
* **线程栈** 一个栈会包含多个栈帧，每个栈帧与方法关联起来，每运行一个方法就会创建一个栈帧，包含局部变量，操作数栈，动态链接方法，返回地址等
* **本地方法栈** 存储native方法
* **堆**   存储对象的实体
* **方法区** 存储了class文件的所有信息
* **常量池** 

## 2. jvm的垃圾回收算法

* 标记清除算法
* 复制算法
* 标记整理算法
* 分代收集算法，新生代以copy算法为主，老年带以标记整理算法为主

## 3. jvm的垃圾回收器

新生代有串行回收器，串行回收器的改良版-多线程串行回收器，并行回收器 老年代有串行回收器，并行回收器，cms 另外还有个g1回收器  
1\)Minor gc 申请空间失败时  
2\)Full gc  
`System.gc()` 老年代内存空间不足，一般因Minor gc将新生代对象放到老年代，发现老年代空间也不足的时候发生 大对象一般直接放到老年代，单又没有足够的连续空间 方法区空间不足，如果经历full gc仍然不足，则会抛出oom

## 4. jvm的类加载机制

java所有的类都是一开始就被加载的吗？ 不是，只初始化一些需要的  
1. 类加载器  
1）启动类加载器：主要加载jvm自身的需要的类，即java的核心库（jre/lib/rt.jar），由c++实现，不可调用；  
2）扩展类加载器：由sun实现，加载java的扩展库（jre/ext/\*.jar）；  
3）系统类加载器：加载应用，`ClassLoader.getSystemClassLoader()`可以调用到它；  
2. 类加载机制 双亲委派机制，当一个类开始加载时，优先父加载器进行加载

## 5. jvm的启动参数

## 6. jvm的命令行参数

### **1）jps 查看当前有哪些进程**

-q 不输出类名、Jar名和传入main方法的参数  
-m 输出传入main方法的参数  
-l 输出main类或Jar的全限名  
-v 输出传入JVM的参数

### **2）jstack**

jstack主要用来查看某个Java进程内的线程堆栈信息。语法格式如下：  
jstack \[option\] pid  
jstack \[option\] executable core  
jstack \[option\] \[server-id@\]remote-hostname-or-ip

命令行参数选项说明如下： -l long listings，会打印出额外的锁信息，在发生死锁时可以用jstack -l pid来观察锁持有情况 -m mixed mode，不仅会输出Java堆栈信息，还会输出C/C++堆栈信息（比如Native方法）

### **3） jmap（Memory Map）和jhat（Java Heap Analysis Tool）**

jmap用来查看堆内存使用状况，一般结合jhat使用。  
jmap语法格式如下：  
jmap \[option\] pid  
jmap \[option\] executable core  
jmap \[option\] \[server-id@\]remote-hostname-or-ip

如果运行在64位JVM上，可能需要指定-J-d64命令选项参数。

jmap -permstat pid

使用jmap -heap pid查看进程堆内存使用情况，包括使用的GC算法、堆配置参数和各代中堆内存使用情况  
使用jmap -histo\[:live\] pid查看堆内存中的对象数目、大小统计直方图，如果带上live则只统计活对象

### **4）jstat（JVM统计监测工具）**

语法格式如下：  
jstat \[ generalOption \| outputOptions vmid \[interval\[s\|ms\] \[count\]\] \] S0C、S1C、S0U、S1U：Survivor 0/1区容量（Capacity）和使用量（Used）  
EC、EU：Eden区容量和使用量  
OC、OU：年老代容量和使用量  
PC、PU：永久代容量和使用量  
YGC、YGT：年轻代GC次数和GC耗时  
FGC、FGCT：Full GC次数和Full GC耗时  
GCT：GC总耗时

