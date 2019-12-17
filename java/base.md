# Java笔记

java官网文档: [https://docs.oracle.com/en/java/javase/index.html](https://docs.oracle.com/en/java/javase/index.html)

## 1. HashMap

HashMap和ConcurrentHashMap在1.8中的变化很大，其中最重要的有如下几点：

* 都加入了红黑树结构
* 对HashMap扩容在并发情况下处理进行了优化，不再会出现链表死循环的情况
* 对ConcurrentHashMap进行了优化，以前桶+哈希表的结构变成了单层哈希表的结构，优化了并发能力

不管是HashMap还是ConcurrentHashMap，由于扩容开销很大，因此，最好初始化时指定好容量，避免扩容（阿里java规约中也有指定）

### **HashMap**

参考资料:  [https://www.cnblogs.com/chengxiao/p/6059914.html](https://www.cnblogs.com/chengxiao/p/6059914.html)  
需要注意的地方：

* 取值，首先通过哈希值，定位元素在Entity数组所在的位置，然后通过对比key去查找链表或者红黑树，对比表达式为：**e.hash == hash && \(\(k = e.key\) == key \|\| \(key != null && key.equals\(k\)\)\)** 
* hash值是二次哈希的结果，而不是直接的hashCode\(\)
* 哈希表的结构是一个HashMap.Entry\[\] table的表，里面是Entry的链表，在链表长度为8时会转化为红黑树 
* put是放到链表末尾，resize是放到头部，resize是只有到了threshold并发生哈希冲突时，才会扩容，在1.8中链表到达8并且size小于64时，也会扩容

```java
    //实际存储的key-value键值对的个数
    transient int size;
    //阈值，当table == {}时，该值为初始容量（初始容量默认为16）；当table被填充了，也就是为table分配内存空间后，threshold一般为 capacity*loadFactory。HashMap在进行扩容时需要参考threshold
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
```

### **ConcurrentHashMap**

1.8之前的ConcurrentHashMap由Segment数组组成，每个Segment中由一个小的哈希表组成，每个Segment内的并发是相对独立的

* 存放数据时，首先要查找所在的Segment，然后查找对应的Entity数组的位置，然后放到链表中
* 默认每个ConcurrentHashMap中的Segment为16，扩容每个Segment中的Entity数组



```java
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
```

1.8之后的ConcurrentHashMap变成了单层数组的table，加强了并发能力，通过cas+sync的方式进行存数据

```java
    //哈希表定义
    transient volatile Node<K,V>[] table;
    // node定义
    static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        volatile V val;
        volatile Node<K,V> next;
    }
    /** Implementation for put and putIfAbsent */
    final V putVal(K key, V value, boolean onlyIfAbsent) {
        if (key == null || value == null) throw new NullPointerException();
        int hash = spread(key.hashCode());
        int binCount = 0;
        for (Node<K,V>[] tab = table;;) {
            Node<K,V> f; int n, i, fh;
            if (tab == null || (n = tab.length) == 0)
                tab = initTable();
            // 当数组中的对应节点为null时，通过cas存
            else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
                if (casTabAt(tab, i, null,
                             new Node<K,V>(hash, key, value, null)))
                    break;                   // no lock when adding to empty bin
            }
            else if ((fh = f.hash) == MOVED)
                tab = helpTransfer(tab, f);
            else {
                V oldVal = null;
                // 插入新数据时通过sync存，因为中间逻辑太多，故没必要通过其他方式，sync最简单直接
                synchronized (f) {
                    //插入数据
                }
                if (binCount != 0) {
                    if (binCount >= TREEIFY_THRESHOLD)
                        treeifyBin(tab, i);
                    if (oldVal != null)
                        return oldVal;
                    break;
                }
            }
        }
        addCount(1L, binCount);
        return null;
    
```

## 2. 对象的创建方式

* new关键字
* 通过反射的Class的newInstance方法
* 使用clone
* 使用序列化和反序列化

## 3. volatile关键字

* volatile关键字是跳过了在线程工作内存中存取，而直接在主存中存取，因此保证了可见性，但是在多线程中同时存数据，没有办法保证最后存的是哪个。
* volatile只能在类中被声明，不可在方法中声明。
* volatile可以声明基本类型，数组和对象

## 4. 线程池的工作原理

线程池一般是由ThreadPoolExecutor产生，主要参数为 corePoolSize，maximumPoolSize，keepAliveTime，unit，workQueue，threadFactory，handler

**threadFactory**，在JDK中的线程工厂在Executors类中，分别为：

* PrivilegedThreadFactory
* DefaultThreadFactory

**workQueue**有如下几种

* ArrayBlockingQueue 它是一个由数组实现的阻塞队列，FIFO。 
* LinkedBlockingQueue 它是一个由链表实现的阻塞队列，FIFO。 吞吐量通常要高于
* ArrayBlockingQueue。 fixedThreadPool使用的阻塞队列就是它。 它是一个无界队列。 
* SynchronousQueue 它是一个没有存储空间的阻塞队列，任务提交给它之后必须要交给一条工作线程处理；如果当前没有空闲的工作线程，则立即创建一条新的工作线程。 cachedThreadPool用的阻塞队列就是它。 它是一个无界队列。
* PriorityBlockingQueue 它是一个优先权阻塞队列。

在Executors中，  
newFixedThreadPool\(\)，newSingleThreadExecutor\(\)采用的LinkedBlockingQueue  
newCachedThreadPool\(\)采用的SynchronousQueue，  
newScheduledThreadPool调用的ScheduledThreadPoolExecutor，采用的DelayedWorkQueue

**handler**是当线程任务已满的时候，对于新来的任务，如何处理。JDK1.5有四种饱和策略： 

* AbortPolicy 默认，直接抛异常。 
* CallerRunsPolicy 只用调用者所在的线程执行任务。
* DiscardOldestPolicy 丢弃任务队列中最久的任务。 
* DiscardPolicy 丢弃当前任务。

```java
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
```

### synchronized与Lock

`synchronized`是通过jvm底层实现

`Lock`是通过cap和AQS实现的，主要实现类为`ReentrantLock`和`ReentrantReadWriteLock`。

