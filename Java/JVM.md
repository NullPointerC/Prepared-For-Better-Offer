### 什么时候会发生栈内存溢出？

![image-20220310145028084](https://gitee.com/cao_ziqiang/img/raw/master/20220310145028.png)

栈是线程私有的，栈的生命周期是和线程一致的；

当栈帧数量超过虚拟机允许的最大栈深度时，就会产生栈内存溢出，如不合理递归；

可以通过调整参数调整JVM栈大小；

### JVM内存模型？

![image-20220310145308510](https://gitee.com/cao_ziqiang/img/raw/master/20220310145308.png)

程序计数器：线程私有，一块很小的空间，用于指示当前线程的行号，当前虚拟机正在执行的线程指令地址；

虚拟机栈：线程私有，每个方法执行时都会创建栈帧，保存局部变量表，动态连接，方法返回，操作栈；

本地方法栈：线程私有的，保存native方法的信息；

堆：所有线程共享的最大的一块内存，所有的对象实例和数组几乎都需要在堆上来分配内存，也是经常发生垃圾回收的区域；

方法区：存放已被加载的类信息，常量，静态变量，JIT编译的一些即时数据；

在JDK1.8中不存在方法区的概念，而是被元数据区取代；被分为两部分，一部分存放加载类信息，一部分存储运行时的常量池；

### Java引用机制

#### 强引用

`Object object=new Object()` 这样的变量声明和定义就会产生对该对象的强引用。只要对象有强引用指向，并且GC Root可达，那么Java内存回收时，即使内存濒临耗尽，也不会回收该对象。

#### 软引用

引用力弱与强引用，用在非必须对象的场景。在即将OOM之前，垃圾回收器会把这些引用指向的对象加入回收范围，以获得更多的内存空间，让程序能够继续健康运行。主要用来缓存服务器中间计算结果及不需要实时保存的用户行为等。

#### 弱引用

引用强度较前两者更弱，也是用来描述非必须对象的。如果弱引用指向的对象只存在弱引用这一条线路，则在下一次 Y GC （年轻代GC）时会被回收。由于YGC的时间不确定性，弱引用何时被回收也具有不确定性。弱引用主要用于指向某个易消失的对象，在强引用断开后，此引用不会劫持对象，调用weakReference.get() 会返回空。

#### 虚引用

定义完成后，就无法通过该引用获取指向的对象，为对象设置虚引用的唯一目的，就是希望能在这个对象被回收时，收到一个系统通知。

虚引用与软引用和弱引用的一个区别在于，虚引用必须和引用队列（ReferenceQueue）联合使用。当垃圾回收器准备回收一个对象时，如果发现它还有虚引用，就会在回收对象的内存之前，把这个虚引用加入到与之关联的引用队列中

### JVM中一次完整的GC是什么样子的?对象如何晋升到老年代?

JDK1.8中分为新生代和老年代：

![image-20220310150056283](https://gitee.com/cao_ziqiang/img/raw/master/20220310150056.png)

新生代又分为Eden区和幸存者区，幸存者区又分为幸存者0区to和幸存者1区from。

大小大致为1：1：8。

当Eden区满时，会触发一次MinorGC，轻量级的GC，收集新生代的垃圾，存活下来的对象就会被放到幸存者区。

对于大对象，需要较大的连续区域，会被直接分配到老年区。

对于一次MinorGC存活下来的对象，被分配到幸存者区的，年龄会+1，每次minor GC存活的对象年龄+1，比如阀值为15时，即可晋升老年代，大对象除外。

参数-XX:+MaxTenuringThreshold可配置对象年龄的阀值,最大是15。

当老年代满时，无法存放更多对象，就会触发full GC来回收空间；

还有MajorGC也是发生在老年代，经常伴随一次Minor GC来回收垃圾；

### 聊聊Java中的垃圾回收算法?

Java中有四种垃圾回收算法，分别是标记清除法、标记整理法、复制算法、分代收集算法;

#### 标记清除法

算法分别标记和清除两个阶段，首先根据可达性分析遍历一遍内存，标记出所有需要回收的对象，在标记完成后统一回收所有被标记的对象。

主要不足：

①.效率问题，标记和清除两个过程的效率都不高；

②.空间问题，标记清除之后会产生大量不连续的内存碎片，空间碎片过多可能会导致以后程序运行时需要分配较大对象时，无法找到足够的连续内存而不得不提前触发另一次垃圾回收；

如下图所示：

![image.png](https://gitee.com/cao_ziqiang/img/raw/master/20220310151321.webp)



#### 标记整理法

分为标记->整理->清除三个过程，首先根据可达性分析，遍历内存，把存活对象和垃圾对象进行标记，第二步将所有存活的对象向一段进行移动，边界以外的对象都会被回收。

适用于存活对象多垃圾少的场景，不会产生碎片空间，需要消耗整理内存空间的时间；

缺点是仍然需要进行对象移动，一定程度上降低了效率。

![image.png](https://gitee.com/cao_ziqiang/img/raw/master/20220310151758.webp)

#### 复制算法

它将可用内存按容量划分为大小相等的两块，每次只使用其中的一块。当这一块的内存用完了，就将还存活着的对象复制到另外一块上面，然后再把已使用的内存空间一次清理掉，这样一来就不容易出现内存碎片的问题。

![image.png](https://gitee.com/cao_ziqiang/img/raw/master/20220310151924.webp)

这样虽然解决了内存内存碎片问题，但是却对内存空间的使用做出了高昂的代价，因为能够使用的内存缩减到原来的一半，会浪费一半的内存空间。

#### 分代收集算法

根据对象存活周期的不同将内存划分为几块。一般是把Java堆分为新生代和老年代，这样就可以根据各个年代的特点采用最适当的收集算法。

- 在新生代中，每次垃圾收集时都发现有大批对象死去，只有少量存活，那就选用复制算法，只需要付出少量存活对象的复制成本就可以完成收集。
- 老年代中因为对象存活率高、没有额外空间对它进行分配担保，就必须使用”标记—整理”算法来进行回收。

![image.png](https://gitee.com/cao_ziqiang/img/raw/master/20220310152457.webp)

### 分别说说这四种算法的特点以及在哪使用?

![image.png](https://gitee.com/cao_ziqiang/img/raw/master/20220310153703.webp)

CMS垃圾回收器就是使用的标记清除算法。

Serial Old收集器使用的标记整理算法，是Serial收集器的老年代版本，使用单线程的标记 - 整理算法。

Parallel Old是Parallel Scavenge收集器的老年代版本，使用多线程和“标记－整理”算法。

G1是复制算法+标记整理算法。

Serial，单线程模式，用于新生代的垃圾回收，使用复制算法；

parNew，多线程模型，用于新生代回收，也使用复制算法；

Parallel Scavenge，使用并行的方式进行垃圾回收，也是新生代的会后，主要采用复制算法，与ParNew类似；

### 如何判断一个对象是否存活?

引用计算法：比较老的判断方式，给每个对象设置了引用计数器，每当引用则+1，失去则-1，当为0时，则回收。缺点是无法解决循环引用；

可达性分析法：从GC Root往下搜索，如果从GC Root开始玩下搜索，对象到GC Root没有任何链连接GC Root上，判断为对象不可用；

![image-20220310160606712](https://gitee.com/cao_ziqiang/img/raw/master/20220310160606.png)

Java中可以作为GC Root的有：

1. 虚拟机栈(栈帧中的本地变量表)中引用的对象.
2. 方法区中类静态属性引用的对象
3. 方法区: 常量引用的对象;
4. 本地方法栈JNI(Native方法)中引用的对象

进行完可达性分析后，某个对象被标记为不可达时，首先会判断对象是否重写了`finalize()`方法，是否已经执行过`finalize()`方法，如果没有重写，或者已经执行过，将不再执行下一步操作，对象被回收。

如果重写了且没有执行过，说明该对象的`finalize()`方法是有必要执行的，就将对象放到`F-Queue`的队列中等待执行。稍后由一个虚拟机自动建立的、低优先级的Finalizer线程去执行它。这里的所谓的“执行”是指虚拟机会触发这个方法,但并不会承诺会等待它运行结束。

这样做的原因，如果一个对象在finalize()方法中执行缓慢，或者发生了死循环，将很可能导致F-Queue队列中其他对象永久处于等待，甚至导致整个内存回收系统崩溃。

finalize()方法是对象逃脱死亡命运的最后一次机会，稍后GC将对F-Queue中的对象进行第二次小规模的标记，如果对象要在finalize()中成功拯救自己---只要重新与引用链上的任何一个对象建立关联既可。

譬如把自己(this关键字)赋值给某个类变量或者对象的成员变量，那么第二次标记时它将被移除“即回收”的集合；如果对象这时候还没有逃脱，那基本上它就真的被回收了。

![img](https://gitee.com/cao_ziqiang/img/raw/master/20220310172307.webp)

```java
public class FinalizeEscapeGC {
    public static FinalizeEscapeGC SAVE_HOOK = null;

    public void isAlive(){
        System.out.println("yes,i am still alive:");
    }

    @Override
    protected void finalize() throws Throwable {
        super.finalize();
        System.out.println("finalize method executed!");
        FinalizeEscapeGC.SAVE_HOOK = this;
    }

    public static void main(String[] args) throws InterruptedException {
        SAVE_HOOK = new FinalizeEscapeGC();
        //对象第一次成功解救自己
        SAVE_HOOK = null;
        System.gc();
        //因为finalize方法优先级很低，所以暂停0.5秒以等待它
        Thread.sleep(500);
        if (SAVE_HOOK != null){
            SAVE_HOOK.isAlive();
        }else {
            System.out.println("no,i am dead:(");
        }

        //下面这段代码与上面的完全的相同，但是这次自救却失败了
        SAVE_HOOK = null;
        System.gc();
        //因为finalize方法优先级很低，所以暂停0.5秒以等待它
        Thread.sleep(500);
        if (SAVE_HOOK != null){
            SAVE_HOOK.isAlive();
        }else {
            System.out.println("no,i am dead:)");
        }
    }
}
```

### 有哪几种垃圾回收器，有哪些优缺点? cms和g1的区别?

![image.png](https://gitee.com/cao_ziqiang/img/raw/master/20220310174739.webp)

<hr/>

![image-20220310180229464](https://gitee.com/cao_ziqiang/img/raw/master/20220310180229.png)

#### Serial收集器（GC日志标识：DefNew）

Serial收集器是最基本、发展历史最悠久的收集器，曾经（在JDK 1.3.1之前）是虚拟机新生代收集的唯一选择。这个收集器是一个单线程的收集器，但它的“单线程”的意义并不仅仅说明它只会使用一个CPU或一条收集线程去完成垃圾收集工作，更重要的是在它进行垃圾收集时，必须暂停其他所有的工作线程，直到它收集结束。

Serial收集器简单而高效、没有线程交互的开销，专心做垃圾收集自然可以获得最高的单线程收集效率，对于运行在Client模式下的虚拟机来说是一个很好的选择。Serial收集器的工作过程如图所示：

![image.png](https://gitee.com/cao_ziqiang/img/raw/master/20220310173218.webp)

#### ParNew收集器（GC日志标识：ParNew）

ParNew收集器其实就是Serial收集器的多线程版本，**追求低停顿的时间**。除了使用多条线程进行垃圾收集之外，其余行为包括Serial收集器可用的所有控制参数、收集算法、Stop The World、对象分配规则、回收策略等都与Serial收集器完全一样，在实现上，这两种收集器也共用了相当多的代码。同时ParNew收集器也是CMS在年轻代的默认收集器，ParNew收集器的工作过程如图所示：

![image.png](https://gitee.com/cao_ziqiang/img/raw/master/20220310173347.webp)

ParNew收集器除了多线程收集之外，其他与Serial收集器相比并没有太多创新之处，但它却是许多运行在Server模式下的虚拟机中首选的新生代收集器，其中有一个与性能无关但很重要的原因是，除了Serial收集器外，目前只有它能与CMS收集器配合工作。在JDK 1.5时期，HotSpot推出了一款在强交互应用中几乎可认为有划时代意义的垃圾收集器——CMS收集器（Concurrent Mark Sweep），这款收集器是HotSpot虚拟机中第一款真正意义上的并发（Concurrent）收集器，它第一次实现了让垃圾收集线程与用户线程（基本上）同时工作。

不幸的是，CMS作为老年代的收集器，却无法与JDK 1.4.0中已经存在的新生代收集器 Parallel Scavenge配合工作（Parallel Scavenge收集器及后面提到的G1收集器都没有使用传统的GC收集器代码框架，而另外独立实现），所以在JDK 1.5中使用CMS来收集老年代的时候，新生代只能选择ParNew或者Serial收集器中的一个。

ParNew收集器在单CPU的环境中绝对不会有比Serial收集器更好的效果，甚至由于存在线程交互的开销，该收集器在通过超线程技术实现的两个CPU的环境中都不能百分之百地保证可以超越Serial收集器。当然，随着可以使用的CPU的数量的增加，它对于GC时系统资源的有效利用还是很有好处的。它默认开启的收集线程数与CPU的数量相同，在CPU非常多(譬如32个，现在CPU动辄就4核加超线程，服务器超过32个逻辑CPU的情况越来越多了)的环境下，可以使用-XX：ParallelGCThreads参数来限制垃圾收集的线程数。

#### Parallel Scavenge收集器（GC日志标识：PSYoungGen）

Parallel Scavenge收集器是一个新生代收集器，它也是使用复制算法的收集器，又是并行的多线程收集器。它的特点是它的关注点与其他收集器不同，CMS等收集器的关注点是尽可能地缩短垃圾收集时用户线程的停顿时间，而Parallel Scavenge收集器的目标则是达到一个可控制的**吞吐量**（Throughput）。**吞吐量=运行用户代码时间/（运行用户代码时间+垃圾收集时间）**，虚拟机总共运行了100分钟，其中垃圾收集花掉1分钟，那吞吐量就是99%。

停顿时间越短就越适合需要与用户交互的程序，良好的响应速度能提升用户体验，而高吞吐量则可以高效率地利用CPU时间，尽快完成程序的运算任务，**主要适合在后台运算而不需要太多交互的任务**。

GC停顿时间缩短是以牺牲吞吐量和新生代空间换来的：系统把新生代调小一些，收集300MB新生代肯定比收集500MB快吧，这也直接导致垃圾收集发生得更频繁一些，原来10秒收集一次、每次停顿100毫秒，现在变成5秒收集一次、每次停顿70毫秒。停顿时间的确在下降，但吞吐量也降下来了。

由于与吞吐量关系密切，Parallel Scavenge收集器也经常称为“吞吐量优先”收集器。除上述两个参数之外，Parallel Scavenge收集器还有一个参数-XX：+UseAdaptiveSizePolicy值得关注。这是一个开关参数，当这个参数打开之后，就不需要手工指定新生代的大小（-Xmn）、Eden与Survivor区的比例（-XX：SurvivorRatio）、晋升老年代对象年龄(-XX：PretenureSizeThreshold)等细节参数了，虚拟机会根据当前系统的运行情况收集性能监控信息，动态调整这些参数以提供最合适的停顿时间或者最大的吞吐量，这种调节方式称为GC自适应的调节策略（GC Ergonomics）。

#### Serial Old收集器（GC日志标识：Tenured）

Serial Old是Serial收集器的老年代版本，它同样是一个单线程收集器，使用“标记-整理”算法。这个收集器的主要意义也是在于给Client模式下的虚拟机使用。如果在Server模式下，那么它主要还有两大用途：

- 一种用途是在JDK 1.5以及之前的版本中与Parallel Scavenge 收集器搭配使用[1]，
- 另一种用途就是作为CMS收集器的后备预案，在并发收集发生Concurrent Mode Failure时使用。Serial Old收集器的工作过程如图所示：

![image.png](https://gitee.com/cao_ziqiang/img/raw/master/20220310174129.webp)

#### Parallel Old收集器（GC日志标识：ParOldGen）

Parallel Old是Parallel Scavenge收集器的老年代版本，使用多线程和“标记-整理”算法。这个收集器是在JDK 1.6中才开始提供的，在此之前，新生代的Parallel Scavenge收集器一直处于比较尴尬的状态。原因是，如果新生代选择了Parallel Scavenge收集器，老年代除了Serial Old（PS MarkSweep）收集器外别无选择。

直到Parallel Old收集器出现后，“吞吐量优先”收集器终于有了比较名副其实的应用组合，在注重吞吐量以及CPU资源敏感的场合，都可以优先考虑Parallel Scavenge加Parallel Old。Parallel Old收集器的工作过程如图所示：

![image.png](https://gitee.com/cao_ziqiang/img/raw/master/20220310174214.webp)

#### CMS（Concurrent Mark Sweep）收集器

CMS（Concurrent Mark Sweep）收集器是一种以**获取最短回收停顿时间为目标**的收集器。目前很大一部分的Java应用集中在互联网站或者B/S系统的服务端上，这类应用尤其重视服务的响应速度，希望系统停顿时间最短，以给用户带来较好的体验。CMS收集器就非常符合这类应用的需求。

6.1 CMS 原理

从名字(包含“Mark Sweep”)上就可以看出，CMS收集器是基于“标记—清除”算法实现的，它的运作过程相对于前面几种收集器来说更复杂一些，整个过程分为6个步骤，包括：

初始标记（CMS-initial-mark）、并发标记（CMS-concurrent-mark）、预处理（CMS-concurrent-preclean）、重新标记（CMS-Final-Remark）、并发清除（CMS-concurrent-sweep）、重置（CMS-concurrent-reset）。

1. **初始标记：** 仅仅只是标记一下GC Roots能**直接**关联到的对象，速度很快，该阶段会Stop The World。
2. **并发标记：** 进行GC RootsTracing 的过程，找出**存活**对象。因为该阶段是并发执行的，在运行期间可能发生新生代的对象晋升到老年代、或者是直接在老年代分配对象、或者更新老年代对象的引用关系等，对于这些对象，都是需要进行重新标记的，否则有些对象就会被遗漏，发生漏标的情况。为了提高重新标记的效率，该阶段会把上述对象所在的Card标识为Dirty，后续只需扫描这些Dirty Card的对象，避免扫描整个老年代。
3. **预处理**
	1. 在并发标记阶段，如果老年代中有对象内部引用发生变化，会把所在的Card标记为Dirty（这里使用一个类似CardTable的数据结构，叫ModUnionTable），通过扫描这些Table，重新标记那些在并发标记阶段引用被更新的对象（晋升到老年代的对象、原本就在老年代的对象）。
	2. 处理新生代已经发现的引用，比如在并发阶段，在Eden区中分配了一个A对象，A对象引用了一个老年代对象B（这个B之前没有被标记），在这个阶段就会标记对象B为活跃对象。可中断的预处理：该阶段发生的前提是新生代Eden区的内存使用量大于参数CMSScheduleRemarkEdenSizeThreshold（默认是2M） ，如果新生代的对象太少，就没有必要执行该阶段，直接执行重新标记阶段。该阶段主要做两件事：
		- 处理From和To区的对象，标记可达的老年代对象；
		- 跟预处理一样，扫描处理Dirty Card中的对象。
4. **重新标记：** 为了修正并发标记期间因用户程序继续运作而导致标记产生变动的那一部分对象的标记记录，这个阶段的停顿时间一般会比初始标记阶段稍长一些，但远比并发标记的时间短。
5. **并发处理：** 并发处理标记的对象。
6. **重置：** 重置线程，为下一次GC做准备。

由于整个过程中耗时最长的并发标记和并发清除过程收集器线程都可以与用户线程一起工作，所以，从总体上来说，CMS收集器的内存回收过程是与用户线程一起并发执行的。

**CMS收集器收集日志如下：** ![image.png](https://gitee.com/cao_ziqiang/img/raw/master/20220310174236.webp) **CMS收集器工作过程如图所示：**

![image.png](https://gitee.com/cao_ziqiang/img/raw/master/20220310174246.webp)

6.2 CMS 的不足

CMS是一款优秀的收集器，它的主要优点在名字上已经体现出来了：并发收集、低停顿，Sun公司的一些官方文档中也称之为并发低停顿收集器(Concurrent Low Pause Collector)。但是CMS还远达不到完美的程度，它有以下3个明显的缺点：

（1）**CMS收集器对CPU资源非常敏感。** 其实，面向并发设计的程序都对CPU资源比较敏感。在并发阶段，它虽然不会导致用户线程停顿，但是会因为占用了一部分线程（或者说CPU资源）而导致应用程序变慢，总吞吐量会降低。CMS默认启动的回收线程数是（CPU数量+3）/4。

（2）**CMS收集器无法处理浮动垃圾(Floating Garbage)**，可能出现“Concurrent Mode Failure”失败而导致另一次Full GC的产生。由于CMS并发清理阶段用户线程还在运行着，伴随程序运行自然就还会有新的垃圾不断产生，这一部分垃圾出现在标记过程之后，CMS无法在当次收集中处理掉它们，只好留待下一次GC时再清理掉。这一部分垃圾就称为“浮动垃圾”。也是由于在垃圾收集阶段用户线程还需要运行，那也就还需要预留有足够的内存空间给用户线程使用，因此CMS收集器不能像其他收集器那样等到老年代几乎完全被填满了再进行收集，需要预留一部分空间提供并发收集时的程序运作使用。在JDK 1.5的默认设置下，CMS收集器当老年代使用了68%的空间后就会被激活，这是一个偏保守的设置，如果在应用中老年代增长不是太快，可以适当调高参数-XX：CMSInitiatingOccupancyFraction的值来提高触发百分比，以便降低内存回收次数从而获取更好的性能，在JDK 1.6中，CMS收集器的启动阈值已经提升至92%。要是CMS运行期间预留的内存无法满足程序需要，就会出现一次“Concurrent Mode Failure”失败，这时虚拟机将启动后备预案：临时启用Serial Old收集器来重新进行老年代的垃圾收集，这样停顿时间就很长了。所以说参数-XX：CM SInitiatingOccupancyFraction设置得太高很容易导致大量“Concurrent Mode Failure”失败，性能反而降低。

（3）**CMS是一款基于“标记—清除”算法实现的收集器**，这意味着收集结束时会有大量空间碎片产生。空间碎片过多时，将会给大对象分配带来很大麻烦，往往会出现老年代还有很大空间剩余，但是无法找到足够大的连续空间来分配当前对象，不得不提前触发一次Full GC。为了解决这个问题，CMS收集器提供了一个-XX：+UseCMSCompactAtFullCollection开关参数（默认就是开启的），用于在CMS收集器顶不住要进行FullGC时开启内存碎片的合并整理过程，内存整理的过程是无法并发的，空间碎片问题没有了，但停顿时间不得不变长。虚拟机设计者还提供了另外一个参数-XX：CMSFullGCsBeforeCompaction，这个参数是用于设置执行多少次不压缩的Full GC后，跟着来一次带压缩的(默认值为0，表示每次进入Full GC时都进行碎片整理)。

6.3 小总结

通过以上分析，我们知道不同的垃圾收集器会适用于不同的场景，所以在使用过程中，我们也需要根据自己的业务场景，选出最适合的，才是性能最好的。

- **Servial** 是单线程垃圾收集器，会有GC STW,在新生代采用复制算法进行垃圾收集更适合在client模式下工作
- **PraNew**  是多线程垃圾收集器，会有GC STW,在新生代采用复制算法进行垃圾收集更适合在server模式下工作
- **Parallel Scavenge** 是 新生代、多线程、复制算法，吞吐量优先（要求吞吐量高的可以选择它）
- **Serial Old** 是 Servial 的老年代版本，老年代、单线程、标记-整理，有GC STW，主要是给client使用，如果是Server模式，则可以和Parallel Scavenge 搭配使用或者作为CMS收集器的后备预案，在并发收集发生Concurrent Mode Failure时使用
- **Parallel Old** 是 多线程、标记——整理算法、适合于吞吐量优先以及CPU资源敏感的场合可以和Parallel Scavenge搭配使用
- **CMS** 是有最短停顿时间 低延迟的垃圾收集器，使用分代收集新生代采用复制算法、老年代采用标记-清除算法进行回收

#### G1垃圾收集器

G1(Garbage-First)收集器是当今收集器技术发展的最前沿成果之一。G1是一款面向服务端应用的垃圾收集器。HotSpot开发团队赋予它的使命是（在比较长期的）未来可以替换掉JDK 1.5中发布的CMS收集器。与其他GC收集器相比，G1具备如下特点。

**并行与并发**：G1能充分利用多CPU、多核环境下的硬件优势，使用多个CPU（CPU或者CPU核心）来缩短Stop-The-World停顿的时间，部分其他收集器原本需要停顿Java线程执行的GC动作，G1收集器仍然可以通过并发的方式让Java程序继续执行。

**分代收集**：与其他收集器一样，分代概念在G1中依然得以保留。虽然G1可以不需要其他收集器配合就能独立管理整个GC堆，但它能够采用不同的方式去处理新创建的对象和已经存活了一段时间、熬过多次GC的旧对象以获取更好的收集效果。

**空间整合**：与CMS的“标记—清理”算法不同，G1从整体来看是基于“标记—整理”算法实现的收集器，从局部（两个Region之间）上来看是基于“复制”算法实现的，但无论如何，这两种算法都意味着G1运作期间不会产生内存空间碎片，收集后能提供规整的可用内存。这种特性有利于程序长时间运行，分配大对象时不会因为无法找到连续内存空间而提前触发下一次GC。

**可预测的停顿**：这是G1相对于CMS的另一大优势，降低停顿时间是G1和CMS共同的关注点，但G1除了追求低停顿外，还能建立可预测的停顿时间模型，能让使用者明确指定在一个长度为M毫秒的时间片段内，消耗在垃圾收集上的时间不得超过N毫秒，这几乎已经是实时Java（RTSJ）的垃圾收集器的特征了。

在G1之前的其他收集器进行收集的范围都是整个新生代或者老年代，而G1不再是这样。使用G1收集器时，Java堆的内存布局就与其他收集器有很大差别，它将整个Java堆划分为多个大小相等的独立区域（Region），虽然还保留有新生代和老年代的概念，但新生代和老年代不再是物理隔离的了，它们都是一部分Region（不需要连续）的集合。

![image.png](https://gitee.com/cao_ziqiang/img/raw/master/20220311223320.webp)

在G1中，还有一种特殊的区域，叫Humongous区域。 如果一个对象占用的空间超过了分区容量50%以上，G1收集器就认为这是一个巨型对象。这些巨型对象，默认直接会被分配在年老代，但是如果它是一个短期存在的巨型对象，就会对垃圾收集器造成负面影响。为了解决这个问题，G1划分了一个Humongous区，它用来专门存放巨型对象。如果一个H区装不下一个巨型对象，那么G1会寻找连续的H分区来存储。为了能找到连续的H区，有时候不得不启动Full GC。

G1收集器之所以能建立可预测的停顿时间模型，是因为它可以有计划地避免在整个Java堆中进行全区域的垃圾收集。G1跟踪各个Region里面的垃圾堆积的价值大小（回收所获得的空间大小以及回收所需时间的经验值），在后台维护一个优先列表，每次根据允许的收集时间，优先回收价值最大的Region（这也就是Garbage-First名称的来由）。这种使用Region划分内存空间以及有优先级的区域回收方式，保证了G1收集器在有限的时间内可以获取尽可能高的收集效率。

一个对象分配在某个Region中，它并非只能被本Region中的其他对象引用，而是可以与整个Java堆任意的对象发生引用关系。那在做可达性判定确定对象是否存活的时候，岂不是还得扫描整个Java堆才能保证准确性？这个问题其实并非在G1中才有，只是在G1中更加突出而已。在以前的分代收集中，新生代的规模一般都比老年代要小许多，新生代的收集也比老年代要频繁许多，那回收新生代中的对象时也面临相同的问题，如果回收新生代时也不得不同时扫描老年代的话，那么Minor GC的效率可能下降不少。

在其他垃圾收集器中，通过CardTable来维护老年代对年轻代的引用，CardTable可以说是Remembered Set（RS）的一种特殊实现，是Card的集合。Card是一块2的幂字节大小的内存区域，例如HotSpot用512字节，里面可能包含多个对象。CardTable要记录的是从它覆盖的范围出发指向别的范围的指针。以分代式GC的CardTable为例，要记录老年代指向年轻代的跨代指针，被标记的Card是老年代范围内的。当进进行年轻代的垃圾收集时，只需要扫描年轻代和老年代的CardTable即可保证不对全堆扫描也不会有遗漏。CardTable通常为字节数组，由Card的索引（即数组下标）来标识每个分区的空间地址。默认情况下，每个卡都未被引用。当一个地址空间被引用时，这个地址空间对应的数组索引的值被标记为”0″，即标记为dirty card。

在G1收集器中，也有和上面一样的CardTable。另外G1中每个Region还有一个与之对应的Remembered Set，虚拟机发现程序在对Reference类型的数据进行写操作时，会产生一个 Write Barrier暂时中断写操作，检查Reference引用的对象是否处于不同的Region之中，如果是，便通过CardTable把相关引用信息记录到被引用对象所属的Region的Remembered Set之中。当进行内存回收时，在GC根节点的枚举范围中加入Remembered Set即可保证不对全堆扫描也不会有遗漏。

![image.png](https://gitee.com/cao_ziqiang/img/raw/master/20220311223345.webp)

Young GC大致可以分为5个阶段：

1. 根扫描：静态和本地对象被扫描。
2. 更新RS：处理dirty card队列更新RS。
3. 处理RS：检测从年轻代指向年老代的对象。
4. 对象拷贝：拷贝存活的对象到survivor/old区域。
5. 处理引用队列：软引用，弱引用，虚引用处理。

Mixed GC大致可划分为全局并发标记（global concurrent marking）和拷贝存活对象（evacuation）两个大部分： global concurrent marking是基于SATB形式的并发标记，包括以下4个阶段：初始标记（Initial Marking）、并发标记（Concurrent Marking）、最终标记（Final Marking）、清理（Clean Up）。

- 初始标记（initial marking）：暂停阶段。扫描根集合，标记所有从根集合可直接到达的对象并将它们的字段压入扫描栈（marking stack）中等到后续扫描。G1使用外部的bitmap来记录mark信息，而不使用对象头的mark word里的mark bit。在分代式G1模式中，初始标记阶段借用young GC的暂停，因而没有额外的、单独的暂停阶段。
- 并发标记（concurrent marking）：并发阶段。不断从扫描栈取出引用递归扫描整个堆里的对象图。每扫描到一个对象就会对其标记，并将其字段压入扫描栈。重复扫描过程直到扫描栈清空。过程中还会扫描SATB write barrier所记录下的引用。
- 最终标记（final marking，在实现中也叫remarking）：暂停阶段。在完成并发标记后，每个Java线程还会有一些剩下的SATB write barrier记录的引用尚未处理。这个阶段就负责把剩下的引用处理完。同时这个阶段也进行弱引用处理（reference processing）。 注意这个暂停与CMS的remark有一个本质上的区别，那就是这个暂停只需要扫描SATB buffer，而CMS的remark需要重新扫描mod-union table里的dirty card外加整个根集合，而此时整个young gen（不管对象死活）都会被当作根集合的一部分，因而CMS remark有可能会非常慢。
- 清理（cleanup）：暂停阶段。清点和重置标记状态。这个阶段有点像mark-sweep中的sweep阶段，不过不是在堆上sweep实际对象，而是在marking bitmap里统计每个region被标记为活的对象有多少。这个阶段如果发现完全没有活对象的region就会将其整体回收到可分配region列表中。

Evacuation阶段是全暂停的。它负责把一部分region里的活对象拷贝到空region里去，然后回收原本的region的空间。

Evacuation阶段可以自由选择任意多个region来独立收集构成收集集合（collection set，简称CSet），靠per-region remembered set（简称RSet）实现。这是regional garbage collector的特征。

在选定CSet后，evacuation其实就跟ParallelScavenge的young GC的算法类似，采用并行copying（或者叫scavenging）算法把CSet里每个region里的活对象拷贝到新的region里，整个过程完全暂停。从这个意义上说，G1的evacuation跟传统的mark-compact算法的compaction完全不同：前者会自己从根集合遍历对象图来判定对象的生死，不需要依赖global concurrent marking的结果，有就用，没有拉倒；而后者则依赖于之前的mark阶段对对象生死的判定。

纯G1模式下，CSet的选定完全靠统计模型找处收益最高、开销不超过用户指定的上限的若干region。由于每个region都有RSet覆盖，要单独evacuate任意一个或多个region都没问题。