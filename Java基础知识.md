## java基础知识

### string 和 stringbuffer 和stringBuilder
1. string由final修饰，不可修改。操作数量较少的字符串用String，不可修改的字符串；
2. 在单线程且操作大量字符串用StringBuilder,速度快，但线程不安全，可修改；
3. 在多线程且操作大量字符串用StringBuffer，线程安全，可修改。
4. 两个字符串相加，原理是调用stringbuilder进行处理的。扩容为二倍+2
### 数组 (Array) 和列表 (ArrayList) 的区别
1. Array可以包含基本类型和对象类型，ArrayList只能包含对象类型。
2. Array大小是固定的，ArrayList的大小是动态变化的。
3. ArrayList提供了更多的方法和特性，比如：addAll()，removeAll()，iterator()等等。

### equals和==的区别
对于复合数据类型之间进行equals比较，在没有覆写equals方法的情况下，他们之间的比较还是内存中的存放位置的地址值，跟双等号（==）的结果相同；如果被复写，按照复写的要求来。<br>
1. == 的作用：  
 基本类型：比较的就是值是否相同  
 引用类型：比较的就是地址值是否相同   
2. equals 的作用:  
引用类型：默认情况下，比较的是地址值，重写该方法后比较对象的成员变量值是否相同  
``` js
public static void main(String[] args) {
	Integer a = new Integer(3);
	Integer b = 3;                 
	int c = 3;
	System.out.println(a == b);    
	System.out.println(a == c);   
}
```
1. a == b分析  
Integer b = 3; 自动调用Integer.valueOf(3) 返回一个Integer的对象。 这个对象是存放到cache中的。 而 Integer a = new Integer(3);这里创建了一个新的对象Integer。所以 a == b 返回的是false
2. a == c 分析  
一个Integer 与 int比较，先将Integer转换成int类型，再做值比较，所以返回的是true。  
java中还有与Integer类似的是Long，它也有一个缓存，在区间[-128,127]范围内获取缓存的值，而Long与long比较的时候先转换成long类型再做值的比较。Double类型，它没有缓存，但是当Double与double比较的时候会先转换成double类型，再做值的比较  

### HashMap和ConcurrentHashMap

由于HashMap是线程不同步的，虽然处理数据的效率高，但是在多线程的情况下存在着安全问题，因此设计了CurrentHashMap来解决多线程安全问题。  
HashMap最多只允许一条记录的键为Null;允许多条记录的值为 Null;  
1. HashMap在put的时候，插入的元素超过了容量（由负载因子决定0.75?）的范围就会触发扩容操作，就是rehash，这个会重新将原数组的内容重新hash到新的扩容数组中，在多线程的环境下，存在同时其他的元素也在进行put操作，如果hash值相同，可能出现同时在同一数组下用链表表示，造成闭环，导致在get时会出现死循环，所以HashMap是线程不安全的。
1.7中，在resize时，转移元素的transfer方法，由于采用的头插法和直接覆盖新数组位置，在多线程情况下，有可能造成循环链表，形成死循环。（扩容逆序和环形）线程1正向标记，线程2完成扩容变成逆序，线程1再进入时形成环形链表
1.8中，通过尾插法和先链接到指定节点上，最后再覆盖新数组的情况，在并发执行put操作时会发生数据覆盖的情况。
1.7和1.8共有的问题是，多线程情况下，线程的操作可能被覆盖或者被忽视，导致数据丢失。其余也有例如size只是采用transient修饰，并没有采用volatile修饰，所以size也可能产生问题。
**HashMap的环**：若当前线程此时获得ertry节点，但是被线程中断无法继续执行，此时线程二进入transfer函数，并把函数顺利执行，此时新表中的某个位置有了节点，之后线程一获得执行权继续执行，因为并发transfer，所以两者都是扩容的同一个链表，当线程一执行到e.next = new table[i] 的时候，由于线程二之前数据迁移的原因导致此时new table[i] 上就有ertry存在，所以线程一执行的时候，会将next节点，设置为自己，导致自己互相使用next引用对方，因此产生链表，导致死循环。
HashMap底层数组+链表+红黑树  2倍扩容!

2. 在JDK1.7版本中，ConcurrentHashMap维护了一个Segment数组，Segment这个类继承了重入锁ReentrantLock，并且该类里面维护了一个 HashEntry<K,V>[] table数组，在写操作put，remove，扩容的时候，会对Segment加锁，所以仅仅影响这个Segment，不同的Segment还是可以并发的，所以解决了线程的安全问题，同时又采用了分段锁也提升了并发的效率。在JDK1.8版本中，ConcurrentHashMap摒弃了Segment的概念，而是直接用Node数组+链表+红黑树的数据结构来实现，并发控制使用Synchronized和CAS来操作，整个看起来就像是优化过且线程安全的HashMap。（（分段锁、乐观锁CAS、violate关键字）!

3. Hashtable与 HashMap类似，不同的是:它不允许记录的键或者值为空;它支持线程的同步，即任一时刻只有一个线程能写Hashtable(锁住整张表),因此也导致了 Hashtable在写入时会比较慢。

4. LinkedHashMap 是HashMap的一个子类，保存了记录的插入顺序，在用Iterator遍历LinkedHashMap时，先得到的记录肯定是先插入的。按添加顺序遍历，多了前后一个的指针
5. TreeMap实现SortMap接口，能够把它保存的记录根据键排序,默认是按键值的升序排序，也可以指定排序的比较器，当用Iterator 遍历TreeMap时，得到的记录是排过序的。
TreeMap：按key排序、红黑树、key必须是同一个类创建的对象!

### 值传递和引用传递？
java中只有值传递    
值传递是对基本型变量而言的,传递的是该变量的一个副本,改变副本不影响原变量.   
引用传递一般是对于对象型变量而言的,传递的是该对象地址的一个副本, 并不是原对象本身 。 所以对引用对象进行操作会同时改变原对象.  
### 为什么重写equals还要重写hashcode？  
HashMap中，如果要比较key是否相等，要同时使用这两个函数！因为自定义的类的hashcode()方法继承于Object类，其hashcode码为默认的内存地址，这样即便有相同含义的两个对象，比较也是不相等的。 
HashMap中的比较key是这样的，先求出key的hashcode(),比较其值是否相等，若相等再比较equals(),若相等则认为他们是相等的。若equals()不相等则认为他们不相等。如果只重写hashcode()不重写equals()方法，当比较equals()时只是看他们是否为同一对象（即进行内存地址的比较）,所以必定要两个方法一起重写。HashMap用来判断key是否相等的方法，其实是调用了HashSet判断加入元素 是否相等。重载hashCode()是为了对同一个key，能得到相同的Hash Code，这样HashMap就可以定位到我们指定的key上。重载equals()是为了向HashMap表明当前对象和key上所保存的对象是相等的，这样我们才真正地获得了这个key所对应的这个键值对。

### Java中Iterator用法整理
1. 使用next()获得序列中的下一个元素。  
2. 使用hasNext()检查序列中是否还有元素。    
3. 使用remove()将迭代器新返回的元素删除。    

### HashMap如果我想要让自己的Object作为K应该怎么办

1. 重写hashCode()是因为需要计算存储数据的存储位置，需要注意不要试图从散列码计算中排除掉一个对象的关键部分来提高性能，这样虽然能更快但可能会导致更多的Hash碰撞；
2. 重写equals()方法，需要遵守自反性、对称性、传递性、一致性以及对于任何非null的引用值x，x.equals(null)必须返回false的这几个特性，目的是为了保证key在哈希表中的唯一性（Java建议重写equal方法的时候需重写hashcode的方法）

### 多态的必要条件
1. 重写   
2. 重载  
3. 父类引用指向子类对象：先调用子类方法，super调用父类方法  
向上转型  
Person p = new student();  
方法先找子类再找父类，属性静态绑定  是父类。  
通过set、get 方法动态绑定  


### Java比较器
对象之间的比较  
1. Comparable   
包装类实现了comparable接口，可自定义比较方法，重写compareTo()  
2. Comparator  
重写compare  
1. Comparable的compareTo(T)方法只有1个参数.  
2. Comparator接口的compare(T o1, T o2)方法有两个参数, 有@FunctionalInterface接口,所以有Lambda表达式的用法.Comparable只能在类内部实现比较功能,让想让实现比较功能的类自身实现Comparable接口  
3. Comparator可以做成比较器类,让比较器类实现Comparator接口.  

### 反射：通过对象看到类的结构
指在运行状态中,对于任意一个类,都能够知道这个类的所有属性和方法,对于任意一个对象,都能调用它的任意一个方法.这种动态获取信息,以及动态调用对象方法的功能叫java语言的反射机制。

### Java动态代理与静态代理的定义与区别
1. 静态代理:由程序员创建或由特定工具自动生成源代码，再对其编译。在程序运行前，代理类的.class文件就已经存在了。
2. 动态代理类：在程序运行时，运用反射机制动态创建而成。
与静态代理类对照的是动态代理类，动态代理类的字节码在程序运行时由Java反射机制动态生成，无需程序员手工编写它的源代码。动态代理类不仅简化了编程工作，而且提高了软件系统的可扩展性，因为Java 反射机制可以生成任意类型的动态代理类。

### 接口和抽象函数、抽象类

1. 抽象类来捕捉子类的通用特性，子类需要实现所有方法的实现。
2. 包含抽象方法的一定是抽象类，但是抽象类不一定含有抽象方法；
3. 抽象类可以包含属性、方法
4. 接口是抽象方法的集合，实现类需要实现声明的所有方法
5. 接口成员变量默认为public static final，必须赋初值，不能被修改


### volatile

volatile在多处理器开发中保证了共享变量的“ 可见性”。可见性的意思是当一个线程修改一个共享变量时，另外一个线程能读到这个修改的值。(共享内存，私有内存)  
底层通过lock指令、内存屏障来实现。作用：内存可见性、防止指令重排

### Atomic类的CAS操作

CAS是英文单词CompareAndSwap的缩写，中文意思是：比较并替换。CAS需要有3个操作数：**内存地址V**，**旧的预期值A**，即将要更新的**目标值B**。CAS指令执行时，当且仅当内存地址V的值与预期值A相等时，将内存地址V的值修改为B，否则就什么都不做。整个比较并替换的操作是一个原子操作。

### CAS操作ABA问题：

如果在这段期间它的值曾经被改成了B，后来又被改回为A，那CAS操作就会误认为它从来没有被改变过。为了解决这个问题，提供了一个带有标记的原子引用类“AtomicStampedReference”，它可以通过控制变量值的版本来保证CAS的正确性。

### synchronized底层原理

1. **对象头：**java中的对象在内存中存储的时候有一个重要的组成部分，就是对象头，对象头中主要包括两部分数据：**类型指针和标记字段**，通过类型指针可以知道该对象是什么类型的，标记字段用来存储对象运行时的数据，其中就有锁对象的指针，它是我们理解Synchronized的关键，因为Synchronized使用到了各种锁。
2. 在HotSpot虚拟机里，对象在堆内存中的存储布局可以划分为三个部分：对象头（Header）、实例数据（Instance Data）和对齐填充（Padding）。
3. 对象头部分包含两类信息。
4. 第一类是自身运行时数据，如何哈希码（hashcode）、GC分代年龄、锁状态标志、线程持有的锁、偏向线程ID等，这部分数据官方称它为“Mark Word”。
5. 第二类是类型指针，即对象指向它的类型元数据的指针，虚拟机通过它来确定对象是哪个类型的实例。
6. 标记字段中的锁对象指针指向了一个monitor对象，每个对象都有一个对应的monitor对象，monitor对象是同步工具，线程在执行加了Synchronized的代码段的时候要先去获取对象的monitor,执行完毕释放monitor。此过程是互斥的，一次只能有一个线程获取monitor,只有该线程释放monitor以后其它线程才能再获取它。


### Synchronized的四种使用方式

1. synchronized(this)：当a线程执行到该语句时，锁住该语句所在对象object，其它线程无法访问object中的所有synchronized代码块。
2. synchronized(obj)：锁住对象obj，其它线程对obj中的所有synchronized代码块的访问被阻塞。
3. synchronized method()：与（1）类似，区别是（1）中线程执行到某方法中的该语句才会获得锁，而对方法加锁则是当方法调用时立刻获得锁。
4. synchronized static method()：当线程执行到该语句时，获得锁，所有调用该方法的其它线程阻塞，但是这个类中的其它非static声明的方法可以访问，即使这些方法是用synchronized声明的，但是static声明的方法会被阻塞；注意，这个锁与对象无关。

前三种方式加的是对象锁，但是如果（2）中obj是一个class类型的对象，那么加的是类锁，并且锁的范围比（4）还要大；如果该class类型的某个实例对象获得了类锁，那么该class类型的所有实例对象的synchronized代码块均无法访问。

### Synchronized和Lock的区别
![image](https://user-images.githubusercontent.com/30047055/114558660-7f089700-9c9d-11eb-9ca1-0237d84e718d.png)

1. 首先synchronized是java内置关键字在jvm层面，Lock是个java接口。
2. synchronized无法判断是否获取锁的状态，Lock可以判断是否获取到锁，并且可以主动尝试去获取锁。
3. synchronized会自动释放锁(a 线程执行完同步代码会释放锁 ；b 线程执行过程中发生异常会释放锁)，Lock需在finally中手工释放锁（unlock()方法释放锁），否则容易造成线程死锁。
4. 用synchronized关键字的两个线程1和线程2，如果当前线程1获得锁，线程2线程等待。如果线程1阻塞，线程2则会一直等待下去，而Lock锁就不一定会等待下去，如果尝试获取不到锁，线程可以不用一直等待就结束了。
5. synchronized的锁可重入、不可中断、非公平，而Lock锁可重入、可判断、可公平（两者皆可）
6. Lock锁适合大量同步的代码的同步问题，synchronized锁适合代码少量的同步问题。

### AQS理论的数据结构

1. AQS内部有3个对象，一个是state（用于计数器，类似gc的回收计数器），一个是线程标记（当前线程是谁加锁的），一个是阻塞队列。  
2. AQS是自旋锁，在等待唤醒的时候，经常会使用自旋的方式，不停地尝试获取锁，直到被其他线程获取成功。  
1. AQS可以实现独占锁和共享锁。
2. 独占锁exclusive是一个悲观锁。保证只有一个线程经过一个阻塞点，只有一个线程可以获得锁。
3. 共享锁shared是一个乐观锁。可以允许多个线程阻塞点，可以多个线程同时获取到锁。它允许一个资源可以被多个读操作，或者被一个写操作访问，但是两个操作不能同时访问。
4. AQS使用一个int类型的成员变量state来表示同步状态，当state>0时表示已经获取了锁，当state = 0无锁。它提供了三个方法（getState()、setState(int newState)、compareAndSetState(int expect,int update)）来对同步状态state进行操作，可以确保对state的操作是安全的。
5. AQS使用一个Volatile的int类型的成员变量来表示同步状态，通过内置的FIFO队列来完成资源获取的排队工作，通过CAS完成对State值的修改。
6. AQS是通过一个CLH队列实现的（CLH锁即Craig, Landin, and Hagersten (CLH) locks，CLH锁是一个自旋锁，能确保无饥饿性，提供先来先服务的公平性。CLH锁也是一种基于链表的可扩展、高性能、公平的自旋锁，申请线程只在本地变量上自旋，它不断轮询前驱的状态，如果发现前驱释放了锁就结束自旋。）

### 如何指定多个线程的执行顺序

1. 设定一个 orderNum，每个线程执行结束之后，更新 orderNum，指明下一个要执行的线程。并且唤醒所有的等待线程。
2. 在每一个线程的开始，要 while 判断 orderNum 是否等于自己的要求值，不是，则 wait，是则执行本线程。

### 为什么要使用线程池

1. 减少创建和销毁线程的次数，每个工作线程都可以被重复利用，可执行多个任务。
2. 可以根据系统的承受能力，调整线程池中工作线程的数目，放置因为消耗过多的内存，而把服务器累趴下。

### 核心线程池ThreadPoolExecutor内部参数

1. corePoolSize：指定了线程池中的线程数量
2. maximumPoolSize：指定了线程池中的最大线程数量
3. keepAliveTime：线程池维护线程所允许的空闲时间
4. unit: keepAliveTime 的单位。
5. workQueue：任务队列，被提交但尚未被执行的任务。
6. threadFactory：线程工厂，用于创建线程，一般用默认的即可。
7. handler：拒绝策略。当任务太多来不及处理，如何拒绝任务。

### 线程池的执行流程

1. 如果正在运行的线程数量小于 corePoolSize，那么马上创建线程运行这个任务
2. 如果正在运行的线程数量大于或等于 corePoolSize，那么将这个任务放入队列
3. 如果这时候队列满了，而且正在运行的线程数量小于 maximumPoolSize，那么还是要创建非核心线程立刻运行这个任务
4. 如果队列满了，而且正在运行的线程数量大于或等于 maximumPoolSize，那么线程池会抛出异常RejectExecutionException

### 线程池都有哪几种工作队列

1. ArrayBlockingQueue：底层是数组，有界队列，如果我们要使用生产者-消费者模式，这是非常好的选择。
2. LinkedBlockingQueue：底层是链表，可以当做无界和有界队列来使用，所以大家不要以为它就是无界队列。
3. SynchronousQueue：本身不带有空间来存储任何元素，使用上可以选择公平模式和非公平模式。
4. PriorityBlockingQueue：无界队列，基于数组，数据结构为二叉堆，数组第一个也是树的根节点总是最小值。

举例 ArrayBlockingQueue 实现并发同步的原理：原理就是读操作和写操作都需要获取到 AQS 独占锁才能进行操作。如果队列为空，这个时候读操作的线程进入到读线程队列排队，等待写线程写入新的元素，然后唤醒读线程队列的第一个等待线程。如果队列已满，这个时候写操作的线程进入到写线程队列排队，等待读线程将队列元素移除腾出空间，然后唤醒写线程队列的第一个等待线程。

### 线程池的拒绝策略

1. ThreadPoolExecutor.AbortPolicy:丢弃任务并抛出RejectedExecutionException异常。
2. ThreadPoolExecutor.DiscardPolicy：丢弃任务，但是不抛出异常。
3. ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新提交被拒绝的任务
4. ThreadPoolExecutor.CallerRunsPolicy：由调用线程（提交任务的线程）处理该任务

### 线程池的正确创建方式

不能用Executors，newFixed和newSingle，因为队列无限大，容易造成耗尽资源和OOM，newCached和newScheduled最大线程数是Integer.MAX_VALUE，线程创建过多和OOM。应该通过ThreadPoolExecutor手动创建。

### 线程提交submit()和execute()有什么区别

1. submit()相比于excute()，支持callable接口，也可以获取到任务抛出来的异常
2. 可以获取到任务返回结果
3. 用submit()方法执行任务，用Future.get()获取异常

### 线程池的线程数量怎么确定

1. 一般来说，如果是CPU密集型应用，则线程池大小设置为N+1。
2. 一般来说，如果是IO密集型应用，则线程池大小设置为2N+1。
3. 在IO优化中，线程等待时间所占比例越高，需要越多线程，线程CPU时间所占比例越高，需要越少线程。这样的估算公式可能更适合：最佳线程数目 = （（线程等待时间+线程CPU时间）/线程CPU时间 ）* CPU数目

### 如何实现一个带优先级的线程池

利用priority参数，继承 ThreadPoolExecutor 使用 PriorityBlockingQueue 优先级队列。

### ThreadLocal的原理和实现

ThreadLoal 变量，线程局部变量，同一个 ThreadLocal 所包含的对象，在不同的 Thread 中有不同的副本。ThreadLocal 变量通常被private static修饰。当一个线程结束时，它所使用的所有 ThreadLocal 相对的实例副本都可被回收。

一个线程内可以存在多个 ThreadLocal 对象，所以其实是 ThreadLocal 内部维护了一个 Map ，这个 Map 不是直接使用的 HashMap ，而是 ThreadLocal 实现的一个叫做 ThreadLocalMap 的静态内部类。而我们使用的 get()、set() 方法其实都是调用了这个ThreadLocalMap类对应的 get()、set() 方法。这个储值的Map并非ThreadLocal的成员变量，而是java.lang.Thread 类的成员变量。ThreadLocalMap实例是作为java.lang.Thread的成员变量存储的，每个线程有唯一的一个threadLocalMap。这个map以ThreadLocal对象为key，”线程局部变量”为值，所以一个线程下可以保存多个”线程局部变量”。对ThreadLocal的操作，实际委托给当前Thread，每个Thread都会有自己独立的ThreadLocalMap实例，存储的仓库是Entry[] table；Entry的key为ThreadLocal，value为存储内容；因此在并发环境下，对ThreadLocal的set或get，不会有任何问题。由于Tomcat线程池的原因，我最初使用的”线程局部变量”保存的值，在下一次请求依然存在（同一个线程处理），这样每次请求都是在本线程中取值。所以在线程池的情况下，处理完成后主动调用该业务treadLocal的remove()方法，将”线程局部变量”清空，避免本线程下次处理的时候依然存在旧数据。

### ThreadLocal为什么要使用弱引用和内存泄露问题

在ThreadLocal中内存泄漏是指ThreadLocalMap中的Entry中的key为null，而value不为null。因为key为null导致value一直访问不到，而根据可达性分析导致在垃圾回收的时候进行可达性分析的时候,value可达从而不会被回收掉，但是该value永远不能被访问到，这样就存在了内存泄漏。如果 key 是强引用，那么发生 GC 时 ThreadLocalMap 还持有 ThreadLocal 的强引用，会导致 ThreadLocal 不会被回收，从而导致内存泄漏。弱引用 ThreadLocal 不会内存泄漏，对应的 value 在下一次 ThreadLocalMap 调用 set、get、remove 方法时被清除，这算是最优的解决方案。

Map中的key为一个threadlocal实例.如果使用强引用，当ThreadLocal对象（假设为ThreadLocal@123456）的引用被回收了，ThreadLocalMap本身依然还持有ThreadLocal@123456的强引用，如果没有手动删除这个key，则ThreadLocal@123456不会被回收，所以只要当前线程不消亡，ThreadLocalMap引用的那些对象就不会被回收，可以认为这导致Entry内存泄漏。

如果使用弱引用，那指向ThreadLocal@123456对象的引用就两个：ThreadLocal强引用和ThreadLocalMap中Entry的弱引用。一旦ThreadLocal强引用被回收，则指向ThreadLocal@123456的就只有弱引用了，在下次gc的时候，这个ThreadLocal@123456就会被回收。

虽然上述的弱引用解决了key，也就是线程的ThreadLocal能及时被回收，但是value却依然存在内存泄漏的问题。当把threadlocal实例置为null以后,没有任何强引用指向threadlocal实例,所以threadlocal将会被gc回收.map里面的value却没有被回收.而这块value永远不会被访问到了. 所以存在着内存泄露,因为存在一条从current thread连接过来的强引用.只有当前thread结束以后, current thread就不会存在栈中,强引用断开, Current Thread, Map, value将全部被GC回收.所以当线程的某个localThread使用完了，马上调用threadlocal的remove方法,就不会发生这种情况了。

另外其实只要这个线程对象及时被gc回收，这个内存泄露问题影响不大，但在threadLocal设为null到线程结束中间这段时间不会被回收的，就发生了我们认为的内存泄露。最要命的是线程对象不被回收的情况，这就发生了真正意义上的内存泄露。比如使用线程池的时候，线程结束是不会销毁的，会再次使用，就可能出现内存泄露。

### HashSet和HashMap

HashSet的value存的是一个static finial PRESENT = newObject()。而HashSet的remove是使用HashMap实现,则是map.remove而map的移除会返回value,如果底层value都是存null,显然将无法分辨是否移除成功。

### Boolean占几个字节

未精确定义字节。Java语言表达式所操作的boolean值，在编译之后都使用Java虚拟机中的int数据类型来代替，而boolean数组将会被编码成Java虚拟机的byte数组，每个元素boolean元素占8位。

### 阻塞非阻塞与同步异步的区别

1. 同步和异步关注的是消息通信机制，所谓同步，就是在发出一个调用时，在没有得到结果之前，该调用就不返回。但是一旦调用返回，就得到返回值了。而异步则是相反，调用在发出之后，这个调用就直接返回了，所以没有返回结果。换句话说，当一个异步过程调用发出后，调用者不会立刻得到结果。而是在调用发出后，被调用者通过状态、通知来通知调用者，或通过回调函数处理这个调用。你打电话问书店老板有没有《分布式系统》这本书，如果是同步通信机制，书店老板会说，你稍等，”我查一下"，然后开始查啊查，等查好了（可能是5秒，也可能是一天）告诉你结果（返回结果）。而异步通信机制，书店老板直接告诉你我查一下啊，查好了打电话给你，然后直接挂电话了（不返回结果）。然后查好了，他会主动打电话给你。在这里老板通过“回电”这种方式来回调。
2. 阻塞和非阻塞关注的是程序在等待调用结果（消息，返回值）时的状态。阻塞调用是指调用结果返回之前，当前线程会被挂起。调用线程只有在得到结果之后才会返回。非阻塞调用指在不能立刻得到结果之前，该调用不会阻塞当前线程。你打电话问书店老板有没有《分布式系统》这本书，你如果是阻塞式调用，你会一直把自己“挂起”，直到得到这本书有没有的结果，如果是非阻塞式调用，你不管老板有没有告诉你，你自己先一边去玩了， 当然你也要偶尔过几分钟check一下老板有没有返回结果。在这里阻塞与非阻塞与是否同步异步无关。跟老板通过什么方式回答你结果无关。

### Java SPI

由于双亲委派模型损失了一丢丢灵活性。就比如java.sql.Driver这个东西。JDK只能提供一个规范接口，而不能提供实现。提供实现的是实际的数据库提供商。提供商的库总不能放JDK目录里吧。Java从1.6搞出了SPI就是为了优雅的解决这类问题——JDK提供接口，供应商提供服务。编程人员编码时面向接口编程，然后JDK能够自动找到合适的实现。


## 水平线

___

---

***


## 文本样式

**This is bold text**

__This is bold text__

*This is italic text*

_This is italic text_

~~Strikethrough~~
> Dorothy followed 


## 列表

无序

+ Create a list by starting a line with `+`, `-`, or `*`
+ Sub-lists are made by indenting 2 spaces:
  - Marker character change forces new list start:
    * Ac tristique libero volutpat at
    + Facilisis in pretium nisl aliquet
    - Nulla volutpat aliquam velit
+ Very easy!

有序

1. Lorem ipsum dolor sit amet
2. Consectetur adipiscing elit
3. Integer molestie lorem at massa


1. You can use sequential numbers...
1. ...or keep all the numbers as `1.`

Start numbering with offset:

57. foo
1. bar


## 代码

Inline `code`

Indented code

    // Some comments
    line 1 of code
    line 2 of code
    line 3 of code


Block code "fences"

```
Sample text here...
```

Syntax highlighting

``` js
var foo = function (bar) {
  return bar++;
};

console.log(foo(5));
```
