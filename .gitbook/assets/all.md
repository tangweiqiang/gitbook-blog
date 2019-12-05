# 目录
`$O(n^2)$`
## 1. java基础
### 基本数据结构
#### hashmap
参考资料 https://www.cnblogs.com/chengxiao/p/6059914.html  
需要注意的地方
* hash值是二次哈希的结果，而不是直接的hashCode()
* 哈希表的结构是一个HashMap.Entry[] table的表，里面是Entry的链表，在链表长度为8时会转化为红黑树 
* put是放到链表末尾，resize是放到头部，resize是只有到了threshold并发生哈希冲突时，才会扩容，在1.8中链表到达8并且size小于64时，也会扩容
* 

~~~java
    //实际存储的key-value键值对的个数
    transient int size;
    //阈值，当table == {}时，该值为初始容量（初始容量默认为16）；当table被填充了，也就是为table分配内存空间后，threshold一般为 capacity*loadFactory。HashMap在进行扩容时需要参考threshold，后面会详细谈到
    int threshold;
    //负载因子，代表了table的填充度有多少，默认是0.75
    final float loadFactor;
    //用于快速失败，由于HashMap非线程安全，在对HashMap进行迭代时，如果期间其他线程的参与导致HashMap的结构发生变化了（比如put，remove等操作），需要抛出异常ConcurrentModificationException
    transient int modCount;

    //1.6
    static int hash(int var0) {
        var0 ^= var0 >>> 20 ^ var0 >>> 12;
        return var0 ^ var0 >>> 7 ^ var0 >>> 4;
    }
    //1.8
    static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
~~~

#### ConcurrentHashMap

~~~java
    static final class Segment<K, V> extends ReentrantLock implements Serializable {
            private static final long serialVersionUID = 2249069246763182397L;
            transient volatile int count;
            transient int modCount;
            transient int threshold;
            transient volatile ConcurrentHashMap.HashEntry<K, V>[] table;
            final float loadFactor;
    }
    static final class HashEntry<K, V> {
        final K key;
        final int hash;
        volatile V value;
        final ConcurrentHashMap.HashEntry<K, V> next;
    }
~~~

### 对象的创建方式
1）new关键字  
2）通过反射的Class的newInstance方法  
3）使用clone  
4）使用序列化和反序列化
## 2. java io
## 3. 多线程
### volatile关键字的原理

* volatile关键字是跳过了在线程工作内存中存取，而直接在主存中存取，因此保证了可见性，但是在多线程中同时存数据，没有办法保证最后存的是哪个。
* volatile只能在类中被声明，不可在方法中声明。
* volatile可以声明基本类型，数组和对象
### 线程池的工作原理
线程池一般是由ThreadPoolExecutor产生，主要参数为
corePoolSize，maximumPoolSize，keepAliveTime，unit，workQueue，threadFactory，handler

Executors.defaultThreadFactory()
PrivilegedThreadFactory
DefaultThreadFactory

ArrayBlockingQueue 
它是一个由数组实现的阻塞队列，FIFO。
LinkedBlockingQueue 
它是一个由链表实现的阻塞队列，FIFO。 
吞吐量通常要高于ArrayBlockingQueue。 
fixedThreadPool使用的阻塞队列就是它。 
它是一个无界队列。
SynchronousQueue 
它是一个没有存储空间的阻塞队列，任务提交给它之后必须要交给一条工作线程处理；如果当前没有空闲的工作线程，则立即创建一条新的工作线程。 
cachedThreadPool用的阻塞队列就是它。 
它是一个无界队列。
PriorityBlockingQueue 
它是一个优先权阻塞队列。  

Executors中newFixedThreadPool()，newSingleThreadExecutor()采用的LinkedBlockingQueue  
new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>())  
newCachedThreadPool()采用的SynchronousQueue，newScheduledThreadPool调用的ScheduledThreadPoolExecutor，采用的DelayedWorkQueue

JDK1.5有四种饱和策略：
AbortPolicy 
默认。直接抛异常。
CallerRunsPolicy 
只用调用者所在的线程执行任务。
DiscardOldestPolicy 
丢弃任务队列中最久的任务。
DiscardPolicy 
丢弃当前任务。

~~~java
    public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler) {
        if (corePoolSize < 0 ||
            maximumPoolSize <= 0 ||
            maximumPoolSize < corePoolSize ||
            keepAliveTime < 0)
            throw new IllegalArgumentException();
        if (workQueue == null || threadFactory == null || handler == null)
            throw new NullPointerException();
        this.corePoolSize = corePoolSize;
        this.maximumPoolSize = maximumPoolSize;
        this.workQueue = workQueue;
        this.keepAliveTime = unit.toNanos(keepAliveTime);
        this.threadFactory = threadFactory;
        this.handler = handler;
    }
~~~

### 并发异常

### forkjoin线程池
RecursiveTask为无返回值的任务  
RecursiveAction为有返回值的任务
其中核心方法是compute
fork为执行子任务
join为获取子任务的执行结果

### 锁
java 不通过jvm实现的锁有ReentrantLock和ReentrantReadWriteLock，

### 信号量Semaphore
CountDownLatch是让一个wait，其它countDown完之后才可以运行，CyclicBarrier是await到一定数量才能全部运行


### 
## 4. jvm
### jvm的内存模型
java的工作内存是cpu寄存器和高速缓存的抽象描述  

* **程序计数器** 记录下一条指令的地址
* **线程栈** 一个栈会包含多个栈帧，每个栈帧与方法关联起来，每运行一个方法就会创建一个栈帧，包含局部变量，操作数栈，动态链接方法，返回地址等
* **本地方法栈** 存储native方法
* **堆**   存储对象的实体
* **方法区** 存储了class文件的所有信息
* **常量池** 

### jvm的垃圾回收算法
* 标记清除算法
* 复制算法
* 标记整理算法
* 分代收集算法  新生代以copy算法为主，老年带以标记整理算法为主


### jvm的垃圾回收器
新生代有串行回收器，串行回收器的改良版-多线程串行回收器，并行回收器
老年代有串行回收器，并行回收器，cms
另外还有个g1回收器  
1)Minor gc  申请空间失败时  
2)Full gc    
System.gc()
老年代内存空间不足，一般因Minor gc将新生代对象放到老年代，发现老年代空间也不足的时候发生
大对象一般直接放到老年代，单又没有足够的连续空间
方法区空间不足，如果经历full gc仍然不足，则会抛出oom
### jvm的类加载机制
java所有的类都是一开始就被加载的吗？
不是，只初始化一些需要的
1. 类加载器  
 1）启动类加载器：主要加载jvm自身的需要的类，即java的核心库（jre/lib/rt.jar），由c++实现，不可调用；  
 2）扩展类加载器：由sun实现，加载java的扩展库（jre/ext/*.jar）；  
 3）系统类加载器：加载应用，ClassLoader.getSystemClassLoader()可以调用到它；
2. 类加载机制 双亲委派机制，当一个类开始加载时，优先父加载器进行加载
### jvm的启动参数
### jvm的命令行参数
**1）jps 查看当前有哪些进程**  
-q 不输出类名、Jar名和传入main方法的参数  
-m 输出传入main方法的参数  
-l 输出main类或Jar的全限名  
-v 输出传入JVM的参数  

**2）jstack**  
jstack主要用来查看某个Java进程内的线程堆栈信息。语法格式如下：  
jstack [option] pid  
jstack [option] executable core  
jstack [option] [server-id@]remote-hostname-or-ip  
 
命令行参数选项说明如下：
-l long listings，会打印出额外的锁信息，在发生死锁时可以用jstack -l pid来观察锁持有情况
-m mixed mode，不仅会输出Java堆栈信息，还会输出C/C++堆栈信息（比如Native方法）  

**3） jmap（Memory Map）和jhat（Java Heap Analysis Tool）**  
jmap用来查看堆内存使用状况，一般结合jhat使用。  
jmap语法格式如下：  
jmap [option] pid  
jmap [option] executable core  
jmap [option] [server-id@]remote-hostname-or-ip  

如果运行在64位JVM上，可能需要指定-J-d64命令选项参数。

jmap -permstat pid

使用jmap -heap pid查看进程堆内存使用情况，包括使用的GC算法、堆配置参数和各代中堆内存使用情况  
使用jmap -histo[:live] pid查看堆内存中的对象数目、大小统计直方图，如果带上live则只统计活对象  

**4）jstat（JVM统计监测工具）**  
语法格式如下：  
jstat [ generalOption | outputOptions vmid [interval[s|ms] [count]] ]
S0C、S1C、S0U、S1U：Survivor 0/1区容量（Capacity）和使用量（Used）  
EC、EU：Eden区容量和使用量  
OC、OU：年老代容量和使用量  
PC、PU：永久代容量和使用量  
YGC、YGT：年轻代GC次数和GC耗时  
FGC、FGCT：Full GC次数和Full GC耗时  
GCT：GC总耗时  

## 5. mysql
### 5.1 mysql的查询优化
### mysql的分页查询优化
mysql的limit在偏移量很大的时候会非常慢，因为limit的偏移量是逐行扫描
在普通情况下分页查询在offset量为百万级的时候，查询效率会特别低，所以可以先运用如下方式去查询。

```sql	  
select * from table 
where id > (select id from table limit 12345667,1) 
limit 20;
select * from table
where id in (select id from table limit 1234567,20);
```

### 5.2 mysql的索引原理
聚簇索引  
b+树的原理  
b树和b+树的区别  

### 5.3 mysql的MVCC
### 5.4 mysql的事务
事务的基本特性（ACID）:

⑴ 原子性（Atomicity）  
　　原子性是指事务包含的所有操作要么全部成功，要么全部失败回滚  

⑵ 一致性（Consistency）  
　　一致性是指事务必须使数据库从一个一致性状态变换到另一个一致性状态，也就是说一个事务执行之前和执行之后都必须处于一致性状态。

⑶ 隔离性（Isolation）  
　　隔离性是当多个用户并发访问数据库时，比如操作同一张表时，数据库为每一个用户开启的事务，不能被其他事务的操作所干扰，多个并发事务之间要相互隔离。

⑷ 持久性（Durability）  
　　持久性是指一个事务一旦被提交了，那么对数据库中的数据的改变就是永久性的，即便是在数据库系统遇到故障的情况下也不会丢失提交事务的操作。

事务的隔离级别：  

⑴ 读未提交（READ_UNCOMMITTED）  
　　原子性是指事务包含的所有操作要么全部成功，要么全部失败回滚  

⑵ 读提交（READ_COMMITED）  
　　一致性是指事务必须使数据库从一个一致性状态变换到另一个一致性状态，也就是说一个事务执行之前和执行之后都必须处于一致性状态。

⑶ 重复读（REPEATABLE_READ）  
　　隔离性是当多个用户并发访问数据库时，比如操作同一张表时，数据库为每一个用户开启的事务，不能被其他事务的操作所干扰，多个并发事务之间要相互隔离。

⑷ 序列化（SERLALIZABLE）  
　　持久性是指一个事务一旦被提交了，那么对数据库中的数据的改变就是永久性的，即便是在数据库系统遇到故障的情况下也不会丢失提交事务的操作。
　　
不同的事务带来的影响：  

⑴ 脏读（Atomicity）  
　　原子性是指事务包含的所有操作要么全部成功，要么全部失败回滚  

⑵ 不可重复读（Consistency）  
　　一致性是指事务必须使数据库从一个一致性状态变换到另一个一致性状态，也就是说一个事务执行之前和执行之后都必须处于一致性状态。

⑶ 幻读（Isolation）  
　　隔离性是当多个用户并发访问数据库时，比如操作同一张表时，数据库为每一个用户开启的事务，不能被其他事务的操作所干扰，多个并发事务之间要相互隔离。

### 5.5 分布式mysql与一致性hash
通过crc32算法，可以将一个id转化为0-2^32-1的数据
然后划分在哪一段的数据，属于哪个node，哪个node属于哪个库表
对于user，直接获取到对应的位置，然后插入数据
对于album，先通过user计算，然后获取位置，插入数据
分布式mysql我们公司的做法是：

* 建一张node表，用于存储各个节点与node之间的关系
* 通过id和crc32算法去查找对应的node，从而获取到哪个node在对应的区间里面
* id的生成是统一管理的，喜马拉雅是存放在一张表里面
* 如何生成node需要研究

### MVCC
MVCC (Multiversion Concurrency Control)，即多版本并发控制技术,它使得大部分支持行锁的事务引擎，不再单纯的使用行锁来进行数据库的并发控制，取而代之的是把数据库的行锁与行的多个版本结合起来，只需要很小的开销,就可以实现非锁定读，从而大大提高数据库系统的并发性能

## 6. redis
### 6.1 redis快的原理
redis快的原因:  
1)基于内存  
2)数据结构简单  
3)采用单线程  
4)多路i/o复用  
### 6.2 redis的事务一致性
### 6.3 redis的持久化方案
redis的持久化方案有两种:  
1)RDB 持久化可以在指定的时间间隔内生成数据集的时间点快照（point-in-time snapshot）;
2)AOF 持久化记录服务器执行的所有写操作命令，并在服务器启动时，通过重新执行这些命令来还原数据集。 AOF 文件中的命令全部以 Redis 协议的格式来保存，新命令会被追加到文件的末尾。 Redis 还可以在后台对 AOF 文件进行重写（rewrite），使得 AOF 文件的体积不会超出保存数据集状态所需的实际大小。
### 6.4 redis的分布式解决方案
1. 如何进行分布式  
redis本身支持分布式，可以通过设置成分布式
)分布式之后查找是发送到任意节点，然后对应的节点从该

2. 如何进行主从同步  
redis主从同步有两种方式，1）在配置好主从之后，会通过RDB的方式发送一份快照给从机同步，2）后面如果主库发生修改，同样的命令也会发送给丛库进行同步


### redis删除过期字段
1）在get时检查是否过期；  
2）定期清理，采用最近最久未使用算法。

## 7.spring
### ioc
### aop
1）切面  切点，通知等的集合
2）切点  在何处调用，比如执行某个方法
3）连接点  插入切面的点，比如调用方法，抛出异常，表示在何时调用切面
4）通知  所要执行的动作
5）动态代理和静态代理  
动态代理与静态代理的区别是动态代理实现InvocationHandler接口，通过invoke方法去实现代理。
### 事务
参考资料 https://my.oschina.net/pingpangkuangmo/blog/415162  
spring的事务基本都是开启新事务，设置了autocommit为false，然后通过conn.commit()来提交事务，如果失败就回滚。但是nest事务不一样，nest是通过savePoint()来提交事务，所有嵌套事务都是在一个事务里面，通过保存的回滚节点来确定回滚到哪一步。
### mvc
SpringMVC流程

1、  用户发送请求至前端控制器DispatcherServlet。

2、  DispatcherServlet收到请求调用HandlerMapping处理器映射器。

3、  处理器映射器找到具体的处理器(可以根据xml配置、注解进行查找)，生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherServlet。

4、  DispatcherServlet调用HandlerAdapter处理器适配器。

5、  HandlerAdapter经过适配调用具体的处理器(Controller，也叫后端控制器)。

6、  Controller执行完成返回ModelAndView。

7、  HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet。

8、  DispatcherServlet将ModelAndView传给ViewReslover视图解析器。

9、  ViewReslover解析后返回具体View。

10、DispatcherServlet根据View进行渲染视图（即将模型数据填充至视图中）。

11、 DispatcherServlet响应用户。

## 设计模式

## 应用
### 分布式锁
* 基于 DB 的唯一索引。
* 基于 ZK 的临时有序节点。
* 基于 Redis 的 NX EX 参数。  

### hystrix的实现原理
参考资料 https://www.jianshu.com/p/60074fe1bd86  
如何超时熔断，如何根据最近的请求比例熔断
通过ScheduledThreadPoolExecutor来完成超时
1. sfsdf
    dsafasdf  
    - dsfsadf

### 幂等性
幂等性可以通过设置一个主id来解决

### java版本的变化
### 红黑树

特性：
1）叶节点和根节点为黑，红节点下面只能为黑；  
2）从一个节点到该节点的子孙节点的所有路径上包含相同数目的黑节点  
3）确保没有一条路径会比其他路径长出俩倍。因而，红黑树是相对是接近平衡的二叉树；
4）时间复杂度为O(lgn)，一棵含有n个节点的红黑树的高度至多为2log(n+1)。
### B+树
阶： 树的最大分支树  
节点的度： 节点的分支数
对于1颗m阶b+树：  
* 根节点至少有两个子节点  
* 每个节点有M-1个key，并且以升序排列  
* 位于M-1和M key的子节点的值位于M-1 和M key对应的Value之间  
* 其它节点至少有M/2个子节点 

对于一颗节点为N度为M的子树，查找和插入需要log(M-1)N ~ log(M/2)N次比较。