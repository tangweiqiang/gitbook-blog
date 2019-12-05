# Java笔记

## HashMap

**hashmap**

参考资料:  [https://www.cnblogs.com/chengxiao/p/6059914.html](https://www.cnblogs.com/chengxiao/p/6059914.html)  
需要注意的地方

* hash值是二次哈希的结果，而不是直接的hashCode\(\)
* 哈希表的结构是一个HashMap.Entry\[\] table的表，里面是Entry的链表，在链表长度为8时会转化为红黑树 
* put是放到链表末尾，resize是放到头部，resize是只有到了threshold并发生哈希冲突时，才会扩容，在1.8中链表到达8并且size小于64时，也会扩容

```java
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
```

**ConcurrentHashMap**

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

