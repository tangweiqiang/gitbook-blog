# Ŀ¼
`$O(n^2)$`
## 1. java����
### �������ݽṹ
#### hashmap
�ο����� https://www.cnblogs.com/chengxiao/p/6059914.html  
��Ҫע��ĵط�
* hashֵ�Ƕ��ι�ϣ�Ľ����������ֱ�ӵ�hashCode()
* ��ϣ��Ľṹ��һ��HashMap.Entry[] table�ı�������Entry��������������Ϊ8ʱ��ת��Ϊ����� 
* put�Ƿŵ�����ĩβ��resize�Ƿŵ�ͷ����resize��ֻ�е���threshold��������ϣ��ͻʱ���Ż����ݣ���1.8��������8����sizeС��64ʱ��Ҳ������
* 

~~~java
    //ʵ�ʴ洢��key-value��ֵ�Եĸ���
    transient int size;
    //��ֵ����table == {}ʱ����ֵΪ��ʼ��������ʼ����Ĭ��Ϊ16������table������ˣ�Ҳ����Ϊtable�����ڴ�ռ��thresholdһ��Ϊ capacity*loadFactory��HashMap�ڽ�������ʱ��Ҫ�ο�threshold���������ϸ̸��
    int threshold;
    //�������ӣ�������table�������ж��٣�Ĭ����0.75
    final float loadFactor;
    //���ڿ���ʧ�ܣ�����HashMap���̰߳�ȫ���ڶ�HashMap���е���ʱ������ڼ������̵߳Ĳ��뵼��HashMap�Ľṹ�����仯�ˣ�����put��remove�Ȳ���������Ҫ�׳��쳣ConcurrentModificationException
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

### ����Ĵ�����ʽ
1��new�ؼ���  
2��ͨ�������Class��newInstance����  
3��ʹ��clone  
4��ʹ�����л��ͷ����л�
## 2. java io
## 3. ���߳�
### volatile�ؼ��ֵ�ԭ��

* volatile�ؼ��������������̹߳����ڴ��д�ȡ����ֱ���������д�ȡ����˱�֤�˿ɼ��ԣ������ڶ��߳���ͬʱ�����ݣ�û�а취��֤��������ĸ���
* volatileֻ�������б������������ڷ�����������
* volatile���������������ͣ�����Ͷ���
### �̳߳صĹ���ԭ��
�̳߳�һ������ThreadPoolExecutor��������Ҫ����Ϊ
corePoolSize��maximumPoolSize��keepAliveTime��unit��workQueue��threadFactory��handler

Executors.defaultThreadFactory()
PrivilegedThreadFactory
DefaultThreadFactory

ArrayBlockingQueue 
����һ��������ʵ�ֵ��������У�FIFO��
LinkedBlockingQueue 
����һ��������ʵ�ֵ��������У�FIFO�� 
������ͨ��Ҫ����ArrayBlockingQueue�� 
fixedThreadPoolʹ�õ��������о������� 
����һ���޽���С�
SynchronousQueue 
����һ��û�д洢�ռ���������У������ύ����֮�����Ҫ����һ�������̴߳��������ǰû�п��еĹ����̣߳�����������һ���µĹ����̡߳� 
cachedThreadPool�õ��������о������� 
����һ���޽���С�
PriorityBlockingQueue 
����һ������Ȩ�������С�  

Executors��newFixedThreadPool()��newSingleThreadExecutor()���õ�LinkedBlockingQueue  
new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>())  
newCachedThreadPool()���õ�SynchronousQueue��newScheduledThreadPool���õ�ScheduledThreadPoolExecutor�����õ�DelayedWorkQueue

JDK1.5�����ֱ��Ͳ��ԣ�
AbortPolicy 
Ĭ�ϡ�ֱ�����쳣��
CallerRunsPolicy 
ֻ�õ��������ڵ��߳�ִ������
DiscardOldestPolicy 
���������������õ�����
DiscardPolicy 
������ǰ����

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

### �����쳣

### forkjoin�̳߳�
RecursiveTaskΪ�޷���ֵ������  
RecursiveActionΪ�з���ֵ������
���к��ķ�����compute
forkΪִ��������
joinΪ��ȡ�������ִ�н��

### ��
java ��ͨ��jvmʵ�ֵ�����ReentrantLock��ReentrantReadWriteLock��

### �ź���Semaphore
CountDownLatch����һ��wait������countDown��֮��ſ������У�CyclicBarrier��await��һ����������ȫ������


### 
## 4. jvm
### jvm���ڴ�ģ��
java�Ĺ����ڴ���cpu�Ĵ����͸��ٻ���ĳ�������  

* **���������** ��¼��һ��ָ��ĵ�ַ
* **�߳�ջ** һ��ջ��������ջ֡��ÿ��ջ֡�뷽������������ÿ����һ�������ͻᴴ��һ��ջ֡�������ֲ�������������ջ����̬���ӷ��������ص�ַ��
* **���ط���ջ** �洢native����
* **��**   �洢�����ʵ��
* **������** �洢��class�ļ���������Ϣ
* **������** 

### jvm�����������㷨
* �������㷨
* �����㷨
* ��������㷨
* �ִ��ռ��㷨  ��������copy�㷨Ϊ����������Ա�������㷨Ϊ��


### jvm������������
�������д��л����������л������ĸ�����-���̴߳��л����������л�����
������д��л����������л�������cms
���⻹�и�g1������  
1)Minor gc  ����ռ�ʧ��ʱ  
2)Full gc    
System.gc()
������ڴ�ռ䲻�㣬һ����Minor gc������������ŵ������������������ռ�Ҳ�����ʱ����
�����һ��ֱ�ӷŵ������������û���㹻�������ռ�
�������ռ䲻�㣬�������full gc��Ȼ���㣬����׳�oom
### jvm������ػ���
java���е��඼��һ��ʼ�ͱ����ص���
���ǣ�ֻ��ʼ��һЩ��Ҫ��
1. �������  
 1�����������������Ҫ����jvm�������Ҫ���࣬��java�ĺ��Ŀ⣨jre/lib/rt.jar������c++ʵ�֣����ɵ��ã�  
 2����չ�����������sunʵ�֣�����java����չ�⣨jre/ext/*.jar����  
 3��ϵͳ�������������Ӧ�ã�ClassLoader.getSystemClassLoader()���Ե��õ�����
2. ����ػ��� ˫��ί�ɻ��ƣ���һ���࿪ʼ����ʱ�����ȸ����������м���
### jvm����������
### jvm�������в���
**1��jps �鿴��ǰ����Щ����**  
-q �����������Jar���ʹ���main�����Ĳ���  
-m �������main�����Ĳ���  
-l ���main���Jar��ȫ����  
-v �������JVM�Ĳ���  

**2��jstack**  
jstack��Ҫ�����鿴ĳ��Java�����ڵ��̶߳�ջ��Ϣ���﷨��ʽ���£�  
jstack [option] pid  
jstack [option] executable core  
jstack [option] [server-id@]remote-hostname-or-ip  
 
�����в���ѡ��˵�����£�
-l long listings�����ӡ�����������Ϣ���ڷ�������ʱ������jstack -l pid���۲����������
-m mixed mode�����������Java��ջ��Ϣ���������C/C++��ջ��Ϣ������Native������  

**3�� jmap��Memory Map����jhat��Java Heap Analysis Tool��**  
jmap�����鿴���ڴ�ʹ��״����һ����jhatʹ�á�  
jmap�﷨��ʽ���£�  
jmap [option] pid  
jmap [option] executable core  
jmap [option] [server-id@]remote-hostname-or-ip  

���������64λJVM�ϣ�������Ҫָ��-J-d64����ѡ�������

jmap -permstat pid

ʹ��jmap -heap pid�鿴���̶��ڴ�ʹ�����������ʹ�õ�GC�㷨�������ò����͸����ж��ڴ�ʹ�����  
ʹ��jmap -histo[:live] pid�鿴���ڴ��еĶ�����Ŀ����Сͳ��ֱ��ͼ���������live��ֻͳ�ƻ����  

**4��jstat��JVMͳ�Ƽ�⹤�ߣ�**  
�﷨��ʽ���£�  
jstat [ generalOption | outputOptions vmid [interval[s|ms] [count]] ]
S0C��S1C��S0U��S1U��Survivor 0/1��������Capacity����ʹ������Used��  
EC��EU��Eden��������ʹ����  
OC��OU�����ϴ�������ʹ����  
PC��PU�����ô�������ʹ����  
YGC��YGT�������GC������GC��ʱ  
FGC��FGCT��Full GC������Full GC��ʱ  
GCT��GC�ܺ�ʱ  

## 5. mysql
### 5.1 mysql�Ĳ�ѯ�Ż�
### mysql�ķ�ҳ��ѯ�Ż�
mysql��limit��ƫ�����ܴ��ʱ���ǳ�������Ϊlimit��ƫ����������ɨ��
����ͨ����·�ҳ��ѯ��offset��Ϊ���򼶵�ʱ�򣬲�ѯЧ�ʻ��ر�ͣ����Կ������������·�ʽȥ��ѯ��

```sql	  
select * from table 
where id > (select id from table limit 12345667,1) 
limit 20;
select * from table
where id in (select id from table limit 1234567,20);
```

### 5.2 mysql������ԭ��
�۴�����  
b+����ԭ��  
b����b+��������  

### 5.3 mysql��MVCC
### 5.4 mysql������
����Ļ������ԣ�ACID��:

�� ԭ���ԣ�Atomicity��  
����ԭ������ָ������������в���Ҫôȫ���ɹ���Ҫôȫ��ʧ�ܻع�  

�� һ���ԣ�Consistency��  
����һ������ָ�������ʹ���ݿ��һ��һ����״̬�任����һ��һ����״̬��Ҳ����˵һ������ִ��֮ǰ��ִ��֮�󶼱��봦��һ����״̬��

�� �����ԣ�Isolation��  
�����������ǵ�����û������������ݿ�ʱ���������ͬһ�ű�ʱ�����ݿ�Ϊÿһ���û����������񣬲��ܱ���������Ĳ��������ţ������������֮��Ҫ�໥���롣

�� �־��ԣ�Durability��  
�����־�����ָһ������һ�����ύ�ˣ���ô�����ݿ��е����ݵĸı���������Եģ������������ݿ�ϵͳ�������ϵ������Ҳ���ᶪʧ�ύ����Ĳ�����

����ĸ��뼶��  

�� ��δ�ύ��READ_UNCOMMITTED��  
����ԭ������ָ������������в���Ҫôȫ���ɹ���Ҫôȫ��ʧ�ܻع�  

�� ���ύ��READ_COMMITED��  
����һ������ָ�������ʹ���ݿ��һ��һ����״̬�任����һ��һ����״̬��Ҳ����˵һ������ִ��֮ǰ��ִ��֮�󶼱��봦��һ����״̬��

�� �ظ�����REPEATABLE_READ��  
�����������ǵ�����û������������ݿ�ʱ���������ͬһ�ű�ʱ�����ݿ�Ϊÿһ���û����������񣬲��ܱ���������Ĳ��������ţ������������֮��Ҫ�໥���롣

�� ���л���SERLALIZABLE��  
�����־�����ָһ������һ�����ύ�ˣ���ô�����ݿ��е����ݵĸı���������Եģ������������ݿ�ϵͳ�������ϵ������Ҳ���ᶪʧ�ύ����Ĳ�����
����
��ͬ�����������Ӱ�죺  

�� �����Atomicity��  
����ԭ������ָ������������в���Ҫôȫ���ɹ���Ҫôȫ��ʧ�ܻع�  

�� �����ظ�����Consistency��  
����һ������ָ�������ʹ���ݿ��һ��һ����״̬�任����һ��һ����״̬��Ҳ����˵һ������ִ��֮ǰ��ִ��֮�󶼱��봦��һ����״̬��

�� �ö���Isolation��  
�����������ǵ�����û������������ݿ�ʱ���������ͬһ�ű�ʱ�����ݿ�Ϊÿһ���û����������񣬲��ܱ���������Ĳ��������ţ������������֮��Ҫ�໥���롣

### 5.5 �ֲ�ʽmysql��һ����hash
ͨ��crc32�㷨�����Խ�һ��idת��Ϊ0-2^32-1������
Ȼ�󻮷�����һ�ε����ݣ������ĸ�node���ĸ�node�����ĸ����
����user��ֱ�ӻ�ȡ����Ӧ��λ�ã�Ȼ���������
����album����ͨ��user���㣬Ȼ���ȡλ�ã���������
�ֲ�ʽmysql���ǹ�˾�������ǣ�

* ��һ��node�����ڴ洢�����ڵ���node֮��Ĺ�ϵ
* ͨ��id��crc32�㷨ȥ���Ҷ�Ӧ��node���Ӷ���ȡ���ĸ�node�ڶ�Ӧ����������
* id��������ͳһ����ģ�ϲ�������Ǵ����һ�ű�����
* �������node��Ҫ�о�

### MVCC
MVCC (Multiversion Concurrency Control)������汾�������Ƽ���,��ʹ�ô󲿷�֧���������������棬���ٵ�����ʹ���������������ݿ�Ĳ������ƣ�ȡ����֮���ǰ����ݿ���������еĶ���汾���������ֻ��Ҫ��С�Ŀ���,�Ϳ���ʵ�ַ����������Ӷ����������ݿ�ϵͳ�Ĳ�������

## 6. redis
### 6.1 redis���ԭ��
redis���ԭ��:  
1)�����ڴ�  
2)���ݽṹ��  
3)���õ��߳�  
4)��·i/o����  
### 6.2 redis������һ����
### 6.3 redis�ĳ־û�����
redis�ĳ־û�����������:  
1)RDB �־û�������ָ����ʱ�������������ݼ���ʱ�����գ�point-in-time snapshot��;
2)AOF �־û���¼������ִ�е�����д����������ڷ���������ʱ��ͨ������ִ����Щ��������ԭ���ݼ��� AOF �ļ��е�����ȫ���� Redis Э��ĸ�ʽ�����棬������ᱻ׷�ӵ��ļ���ĩβ�� Redis �������ں�̨�� AOF �ļ�������д��rewrite����ʹ�� AOF �ļ���������ᳬ���������ݼ�״̬�����ʵ�ʴ�С��
### 6.4 redis�ķֲ�ʽ�������
1. ��ν��зֲ�ʽ  
redis����֧�ֲַ�ʽ������ͨ�����óɷֲ�ʽ
)�ֲ�ʽ֮������Ƿ��͵�����ڵ㣬Ȼ���Ӧ�Ľڵ�Ӹ�

2. ��ν�������ͬ��  
redis����ͬ�������ַ�ʽ��1�������ú�����֮�󣬻�ͨ��RDB�ķ�ʽ����һ�ݿ��ո��ӻ�ͬ����2������������ⷢ���޸ģ�ͬ��������Ҳ�ᷢ�͸��Կ����ͬ��


### redisɾ�������ֶ�
1����getʱ����Ƿ���ڣ�  
2��������������������δʹ���㷨��

## 7.spring
### ioc
### aop
1������  �е㣬֪ͨ�ȵļ���
2���е�  �ںδ����ã�����ִ��ĳ������
3�����ӵ�  ��������ĵ㣬������÷������׳��쳣����ʾ�ں�ʱ��������
4��֪ͨ  ��Ҫִ�еĶ���
5����̬����;�̬����  
��̬�����뾲̬����������Ƕ�̬����ʵ��InvocationHandler�ӿڣ�ͨ��invoke����ȥʵ�ִ���
### ����
�ο����� https://my.oschina.net/pingpangkuangmo/blog/415162  
spring������������ǿ���������������autocommitΪfalse��Ȼ��ͨ��conn.commit()���ύ�������ʧ�ܾͻع�������nest����һ����nest��ͨ��savePoint()���ύ��������Ƕ����������һ���������棬ͨ������Ļع��ڵ���ȷ���ع�����һ����
### mvc
SpringMVC����

1��  �û�����������ǰ�˿�����DispatcherServlet��

2��  DispatcherServlet�յ��������HandlerMapping������ӳ������

3��  ������ӳ�����ҵ�����Ĵ�����(���Ը���xml���á�ע����в���)�����ɴ��������󼰴�����������(�����������)һ�����ظ�DispatcherServlet��

4��  DispatcherServlet����HandlerAdapter��������������

5��  HandlerAdapter����������þ���Ĵ�����(Controller��Ҳ�к�˿�����)��

6��  Controllerִ����ɷ���ModelAndView��

7��  HandlerAdapter��controllerִ�н��ModelAndView���ظ�DispatcherServlet��

8��  DispatcherServlet��ModelAndView����ViewReslover��ͼ��������

9��  ViewReslover�����󷵻ؾ���View��

10��DispatcherServlet����View������Ⱦ��ͼ������ģ�������������ͼ�У���

11�� DispatcherServlet��Ӧ�û���

## ���ģʽ

## Ӧ��
### �ֲ�ʽ��
* ���� DB ��Ψһ������
* ���� ZK ����ʱ����ڵ㡣
* ���� Redis �� NX EX ������  

### hystrix��ʵ��ԭ��
�ο����� https://www.jianshu.com/p/60074fe1bd86  
��γ�ʱ�۶ϣ���θ����������������۶�
ͨ��ScheduledThreadPoolExecutor����ɳ�ʱ
1. sfsdf
    dsafasdf  
    - dsfsadf

### �ݵ���
�ݵ��Կ���ͨ������һ����id�����

### java�汾�ı仯
### �����

���ԣ�
1��Ҷ�ڵ�͸��ڵ�Ϊ�ڣ���ڵ�����ֻ��Ϊ�ڣ�  
2����һ���ڵ㵽�ýڵ������ڵ������·���ϰ�����ͬ��Ŀ�ĺڽڵ�  
3��ȷ��û��һ��·���������·����������������������������ǽӽ�ƽ��Ķ�������
4��ʱ�临�Ӷ�ΪO(lgn)��һ�ú���n���ڵ�ĺ�����ĸ߶�����Ϊ2log(n+1)��
### B+��
�ף� ��������֧��  
�ڵ�Ķȣ� �ڵ�ķ�֧��
����1��m��b+����  
* ���ڵ������������ӽڵ�  
* ÿ���ڵ���M-1��key����������������  
* λ��M-1��M key���ӽڵ��ֵλ��M-1 ��M key��Ӧ��Value֮��  
* �����ڵ�������M/2���ӽڵ� 

����һ�Žڵ�ΪN��ΪM�����������ҺͲ�����Ҫlog(M-1)N ~ log(M/2)N�αȽϡ�