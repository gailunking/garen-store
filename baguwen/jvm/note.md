[note.md](https://github.com/gailunking/garen-store/files/14821590/note.md)[Uploading # 零基础自测面试题

[知乎播放量最多的JVM课程《jvm性能调优+面试+实战》](https://zhuanlan.zhihu.com/p/444388869)

## java内存区域划分，每块内存区域都对应什么？

java内存区域划分有以下几个区域，

1. java栈：线程私有，每个线程拥有一个虚拟机栈，与线程同生命周期。栈里面是一个个的栈帧，每个方法调用都会创建一个栈帧，包括局部变量表，操作数栈，动态链接，返回值等。栈主要用于存放局部变量和部分结果，并在方法调用开始结束时压栈弹栈。
2. 堆：堆是所有线程共享的区域，存放对象实例，并分为新生代老年代，以供采用不同的垃圾回收策略。
3. 程序计数器：线程私有的，每个线程都有一个程序计数器，用于指示线程正在执行的字节码指令的地址。
4. 本地方法栈：与虚拟机栈类似，在JNI时调用本地方法时会使用本地方法栈。
5. 方法区：也是被所有线程共享的一块区域，用来存储类的结构信息，常量，静态变量（类变量），HotSpot虚拟机中方法区被称为永久代，JDK8以后永久代被元空间替代。

## 一个类的加载流程

一个类的加载流程可以分为以下几个阶段：加载（Loading）、验证（Verification）、准备（Preparation）、解析（Resolution）、初始化（Initialization）、使用（Usage）和卸载（Unloading）。这七个阶段通常被称为类加载的生命周期。

[请你说说Java类的加载过程_说说类加载的流程-CSDN博客](https://blog.csdn.net/qq_41563912/article/details/116642556)

1. 加载：类加载的第一个阶段是加载，它需要找到类的字节码文件，通过类加载器从文件系统，网络等地方加载类的字节码文件，将其转化为Class对象。
2. 验证：在这个阶段虚拟机对加载的字节码进行校验，确保它符合java语言的规范和虚拟机要求。包括验证包括文件格式验证、元数据验证、字节码验证、符号引用验证等。
3. 准备：在准备阶段，虚拟机为类的静态变量分配内存并赋予默认值（注意是初始值），这里所说的静态变量包括类级别的变量（static），以及常量（final）；
4. 解析：解析阶段是虚拟机将常量池中的符号引用替换为直接引用的过程。符号引用是一组符号来描述所引用的目标，直接引用可以是直接指向目标的指针、相对偏移量或一个能间接定位到目标的句柄。
5. 初始化：虚拟机执行类的初始化代码，包括静态变量的赋值和静态代码块的执行。初始化阶段是类加载的最后一步，它标志着类已经准备好被使用了。
6. 使用：
7.  卸载（Unloading）：卸载阶段是指虚拟机在满足一定条件下对某个类进行卸载，释放内存空间。类卸载通常发生在类加载器被回收时，而且被卸载的类的所有实例都已经被回收。

其中234阶段又统称为链接所以类的加载流程通常又分为加载，链接，初始化三个阶段

对象的初始化发生在6，包括1. 创建对象，2. 对象实例化，初始化，3. 返回引用。对应到双重锁校验的对象创建三步中出现的32步骤调换问题。

## 垃圾回收算法都有哪些？

1. 引用计数法：对象每被引用一次对应的计数器+1，少一个引用计数器-1，清除计数器为0的对象，缺点是不饿能解决对象循环引用问题。
2. 标记清除法：遍历对象，标记可达的对象。再次遍历，清除没有标记的对象
3. 标记压缩法：在2的基础上多了将标记对象移动到内存的一侧，方便清理内存碎片
4. 复制算法：将内存分为两个区，一个区是空闲的，将活动区做清除后将剩余对象移动到空闲区，然后改变两个区的状态
5. 分代收集算法：堆不同分区采用不同算法
6. 增量式垃圾回收算法：将一次垃圾回收操作分为几个小步骤，每个小步骤中间穿插执行任务工作，避免一次大的垃圾回收停顿时间过长。减少了停顿时间，但可能增加了回收总体时长
7.  并发垃圾回收算法：允许垃圾回收和程序执行并发进行，以减小垃圾回收对程序执行的影响。常见的并发垃圾回收算法有CMS（Concurrent Mark-Sweep）和G1（Garbage-First）

## 什么是双亲委派机制

双亲委派机制是类的一种加载机制，其核心思想是一个类加载器获取到加载类的请求后，它不会自己去尝试加载这个类，而是将类一层层托付给父类去加载，如果顶层父类中找不到则一层层向子类加载的方式加载类，可以有效避免类被恶意篡改，保证核心，根加载器的权威性，避免应用类加载器恶意篡改类

## 可达性分析算法？引用计数法？对应优缺点？

可达性分析算法：它是通过一组称为GC Roots对象作为起始点，从根对象开始通过引用关系往下遍历，对可以直接或间接引用到的对象作为活动对象，对没有标记的对象即不可达对象作为垃圾做清除处理

引用计数法：为每个对象维护一个计数器，有引用+1，没有-1，为0时表示对象不再被引用，可以被回收。

**引用计数法优点：**

实现简单，效率高，实时性强，随时被回收，不用等待特定的回收时间，

**缺点：**

但是有循环对引用问题，

每次引用变化都需要更新计数器增加维护开销。

**可达性分析算法优点：**

相对精确，能够有效查找不再引用的对象，没有循环问题。

**缺点：**

需要停顿应用程序的执行，因为标记阶段需要遍历整个对象图，这个过程会中断程序的正常运行。效率较低。

## 如何主动触发垃圾回收？

调用`System.gc()`或`Runtime.getRuntime().gc()`方法实现，不过需要注意的是，调用这两个方法并不一定能立即执行GC，这更多只是像jvm提供了一个建议，具体还要看jvm针对内存资源情况决定。

正常情况下，jvm的垃圾回收机制已经非常完善了，很少需要手动GC的情况，除非在特殊情况下进行性能调优或者一些特殊测试，通常不建议频繁手动GC，这可能会影响程序性能。

## 什么是young GC？什么是full GC？ 什么是stw？

1. youngGC是针对新生代进行的垃圾回收处理，这次处理一般会清除掉大部分对象。（when？在伊甸区满的时候，）
2. full GC是老通常发生在年轻代GC频繁，老年代空间不足的时候，或者由于永久代或元空间的垃圾回收需求，它是规模较大的GC ，GC时间长，量级大，会造成较长时间的停顿，所以成本相对大
3. STW（Stop-The-world）

stop-the-world是指垃圾回收过程中，应用程序的所有线程都被暂停，没有任何线程在执行应用程序的代码。这是为了确保垃圾回收期间，没有对象在修改或者移动，只有垃圾回收程序在占用cpu资源，STW是垃圾回收的一个重要指标，较短的stw有利于提高程序性能

在youngGC中，主要发生年轻代的GC回收，通常使用复制算法，将存活的对象移入空闲区。full GC涉及整个堆的垃圾回收，可能包含多个阶段，如标记清除，标记压缩等。STW则是在垃圾回收时，为了确保程序的正确性进行的程序停止现象。在实际应用中，需要尽量降低young GC频率，减少full GC次数，缩短stw时间，以提高系统性能。

## 如何减少full GC触发频率？有没有做过JVM调优？怎么做的

[JVM 调优，如何合理分配内存，减少Full GC?_写代码时有没有什么方式,尽量减少fullgc-CSDN博客](https://blog.csdn.net/wangxuelei036/article/details/108304467)

减少full GC呢是jvm调优的一个关键目标，因为full GC通常会引起较长时间的停顿，影响程序的响应性。

1. **合理设置堆大小：**确保年轻代老年代堆大小符合应用程序设计，避免频繁扩容和收缩，影响效率。可以通过

-Xms（初始内存值），-Xmx（最大内存值）。

1. **调整年轻代比例：**通过调整年轻代的比例，控制对象在伊甸区，幸存区的分布，适当调整年轻代大小，可以减少晋升到老年代的数量，从而减少full GC频率。可以通过-XX：NewRatio和-XX：SurvivorRatio等参数调整
2. **选择合适的****垃圾回收****器：**根据应用程序性能和特点，选择合适的垃圾回收器，比如G1垃圾回收器相对于CMS在一些情况下能够更好的控制full GC的停顿时间

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MWE5YzUwM2MzY2FlMGE4NzUyZTk4YWFmOGYyMmJhZjNfNFVzdjNoMjA1TUx4eFp3ZG9admJieklyTmI4MU9kWlFfVG9rZW46VXhGVWJCWHJ3b01MUEp4bTFSb2M0cTY4bndkXzE3MTE5NjA1MDQ6MTcxMTk2NDEwNF9WNA)

怎么选择垃圾回收器

1. **调整新生代****垃圾回收****策略：**选择合适的新生代垃圾回收策略，如Serial、ParNew、或者G1。新生代的垃圾回收频率会影响到对象晋升到老年代的速度，从而影响Full GC的触发。
2. **内存泄漏排查：**定期对内存泄漏问题进行排查，内存泄漏会导致堆中对象无法释放，最终导致full GC频繁触发

jvm调优是一个综合考虑多个因素的过程，首先根据程序的应用场景，进行性能测试和分析。有针对性的进行参数调整和采取措施

# java中高级工程师自测：

## 方法区和永久代，元空间的区别？

1. 作用：方法区和永久代都是用来存储类元结构，静态变量，常量等，元空间是1.8后诞生的，用来替代永久代的一部分，存储类的元数据。
2. 位置：方法区通常位于堆外，但是在一些实现中也可能在堆中，永久代通常位于堆区，是堆区的一部分。元空间替代了永久代的一部分，逻辑上位于堆区，但不是堆的一部分而是直接使用本地内存
3. 回收：

**方法区(逻辑上)：**

是逻辑上的东西，是JVM的规范，所有虚拟机必须遵循的

方法区是所有线程共享的，用于存储类的信息、常量池、方法数据、方法代码等。

**永久代：(属于实现方法区的一个方案，在JDK7以及之前的版本存在，JDK8移除永久代)**

**元空间与永久代之间最大的区别在于：元空间并不在****虚拟机****中，而是使用本地****内存****。**

元空间的引入是为了解决永久代的一些问题，例如永久代的固定大小和容易发生溢出。元空间更灵活，能够更好地适应动态的类加载和卸载，避免了永久代的一些限制。

方法区相当于一个接口，规范。永久代和元空间相当于其实现 接口和实现类的关系

## 对象什么时候进入老年代？

1. 对象年龄阈值：对象年龄代数达到阈值进入老年代，可通过`-XX:MaxTenuringThreshold`调整这个阈值
2. 动态年龄判断：如果幸存区的年龄前N的对象占用内存数超过了50%，那么这其中对象最大年龄数位M的化，年龄大于M的对象将晋升到老年去
3. 大对象直接进入老年代：如果配置了`-XX:PretenureSizeThreshold`这个参数，那么大于这个对象参数的对象，比如分配一个超大的字节数组，此时这个大对象就会被直接放入老年代，不会经过新生代，因为它占用内存太多，经历多次GC可能还存活，被复制来复制去太消耗性能。
4. minorGC后的存活对象幸存区放不下，直接放入老年代

## GC一般由什么引起？发生在JVM的哪个区域？

1. 对象不再引用：当一个对象不再被任何活动对象引用，就会变成垃圾。垃圾回收器的任务是识别和回收这些不再被引用的对象，以释放内存空间。
2. 分配内存失败：当创建一个对象时，没有多余的内存给他分配，则调用GC清理无用对象，垃圾回收器会回收一些不再使用的对象，以便为新对象腾出足够的空间。
3. 内存空间不足：某个内存区域（通常是新生代）的空间不足以容纳新的对象，就会触发垃圾回收，释放一些不再使用的对象，以便为新对象腾出空间

发生堆内存的新生代和老年代

## 方法区OOM如何避免？怎么去解决？

方法区OOM一般是由于类元结构太多，超出方法区的内存大小造成。

1. 优化代码结构：避免过度使用大量类和静态代码。合理设计类的继承关系结构，减少不必要的类加载。
2. 减少运行时生成类的数量：一些框架和库可能在运行时生成大量的类，这可能导致方法区溢出，可以减少动态生成类的数量
3. 运行时常量池问题：运行时常量池可以在程序运行时存放常量，如字符串的intern方法，所以避免大量这样的操作

java7以前可以通过`-XX:MaxPermSize`参数来增加方法区的大小。java8以后通过元空间

提问

#### 内存泄漏，内存溢出

内存泄漏是指一些对象在使用完后没有被引用了，但是也没有被及时回收，导致无法被回收，内存泄漏会导致内存溢出可以说是**因果关系**

内存溢出，jvm虚拟机无法分配为对象分配内存，垃圾GC也无法清理出更多空间，发生内存溢出

### 查看 JVM 内存分布

假设我们 Java 应用 PID 为 15162，输入命令查看 JVM 内存分布 `jmap -heap 15162`

```
[xxx@xxx ~]# jmap -heap 15162
Attaching to process ID 15162, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.161-b12
using thread-local object allocation.
Mark Sweep Compact GC
Heap Configuration:
   MinHeapFreeRatio         = 40 # 最小堆使用比例
   MaxHeapFreeRatio         = 70 # 最大堆可用比例
   MaxHeapSize              = 482344960 (460.0MB) # 最大堆空间大小
   NewSize                  = 10485760 (10.0MB) # 新生代分配大小
   MaxNewSize               = 160759808 (153.3125MB) # 最大新生代可分配大小
   OldSize                  = 20971520 (20.0MB) # 老年代大小
   NewRatio                 = 2 # 新生代比例
   SurvivorRatio            = 8 # 新生代与 Survivor 比例
   MetaspaceSize            = 21807104 (20.796875MB) # 元空间大小
   CompressedClassSpaceSize = 1073741824 (1024.0MB) # Compressed Class Space 空间大小限制
   MaxMetaspaceSize         = 17592186044415 MB # 最大元空间大小
   G1HeapRegionSize         = 0 (0.0MB) # G1 单个 Region 大小
Heap Usage:  # 堆使用情况
New Generation (Eden + 1 Survivor Space): # 新生代
   capacity = 9502720 (9.0625MB) # 新生代总容量
   used     = 4995320 (4.763908386230469MB) # 新生代已使用
   free     = 4507400 (4.298591613769531MB) # 新生代剩余容量
   52.56726495150862% used # 新生代使用占比
Eden Space:

   capacity = 8454144 (8.0625MB) # Eden 区总容量
   used     = 4029752 (3.8430709838867188MB) # Eden 区已使用
   free     = 4424392 (4.219429016113281MB) # Eden 区剩余容量
   47.665996699370154% used  # Eden 区使用占比
From Space: # 其中一个 Survivor 区的内存分布
   capacity = 1048576 (1.0MB)
   used     = 965568 (0.92083740234375MB)
   free     = 83008 (0.07916259765625MB)
   92.083740234375% used
To Space: # 另一个 Survivor 区的内存分布
   capacity = 1048576 (1.0MB)
   used     = 0 (0.0MB)
   free     = 1048576 (1.0MB)
   0.0% used
tenured generation: # 老年代
   capacity = 20971520 (20.0MB)
   used     = 10611384 (10.119804382324219MB)
   free     = 10360136 (9.880195617675781MB)
   50.599021911621094% used
10730 interned Strings occupying 906232 bytes.
```

通过查看 JVM 内存分配以及运行时使用情况，可以判断内存分配是否合理

#### Dump

dump指转储，其实就是将内存某时刻的动态数据，进行一次类似快照的操作，存储成静态文件数据，方便我们观察内存中的情况

# 补充问题：

## full GC通常在以下情况下触发：

1. 老年代空间不足： 当老年代的内存空间不足时，触发Full GC。老年代是存放长期存活的对象的区域，如果老年代无法容纳新分配的对象或者已经存活的对象，就会触发Full GC。
2. 永久代空间不足（JDK 7及之前）： 在JDK 7及之前的版本中，永久代用于存放类的元数据信息。当永久代空间不足时，可能会触发Full GC。在JDK 8及以后的版本，永久代被元空间（Metaspace）取代。
3. 执行显式的System.gc()： 尽管调用`System.gc()`并不是强制性的，但它可能会触发Full GC。在实际应用中，一般不建议显式调用`System.gc()`，因为这会削弱应用程序的性能。  
4. CMS垃圾回收失败： 在使用CMS（Concurrent Mark-Sweep）垃圾回收器时，如果CMS无法为新生代提供足够的空间，会触发Full GC。这种情况可能发生在并发标记阶段由于老年代变化较快导致新生代不足。
5. G1垃圾回收器的To空间不足： 在使用G1垃圾回收器时，如果To空间不足以存放存活对象，可能会触发Full GC。

## CMS垃圾回收机制：

四个过程

1. 预先标记，标记GC root和他的直接引用对象，会造成短暂stw
2. 并发标记，并发标记所有可达对象，不会造成应用程序停顿
3. 重新标记：这个过程短暂的停顿，对并发标记阶段时可能出现的对象引用变化进行重新标记。修掉可能漏标的对象
4. 并发清除：并发清除所有垃圾对象

缺点：

1. 空间碎片问题：他的清除是标记清除，会生成许多内存碎片
2. 垃圾漏标问题
3. 并发阶段占用cpu资源，对程序吞吐量造成影响
4. 由于内存碎片问题，CMS可能无法为大对象分配足够的空间，触发Full GC。

## 哪些对象可以当作根对象？

1. 局部变量表中的引用：方法中定义的局部变量，包括方法参数和在方法中定义的局部变量，都可以是根对象。这些根对象通常存在于栈帧中。些引用被认为是根对象的一部分，因为它们是直接可达的，即它们位于调用栈中的当前活动方法帧中。
2. 静态变量引用的对象：
3. JNI接口调用的对象：
4. 活动线程中的对象引用： 活动线程中正在执行的方法的局部变量表和操作数栈中的对象引用。

## 垃圾回收器

### https://cloud.tencent.com/developer/article/1647637 G1和CMS的区别，安全点的概念

1. **serial****垃圾回收****器：**单线程串行化执行，采用复制算法，年轻代的垃圾回收器，适合client服务器
2. **parNew****垃圾回收****器**：多线程并行执行，复制算法，年轻代，适合server端
3. **parallel：**多线程并行
   1. 相比于parNew，它是吞吐量优先；具有自适应调节策略
4. **Serial-old parallel-old：**都是老年代回收器，标记整理
5. **CMS****：**（老年代，需要搭配其他，其实除了G1都需要搭配使用）

\1. 预先标记：

\2. 并发标记

\3. 最终标记

4.并发清除

优点：效率高

缺点：产生内存碎片，浮动垃圾，造成cpu吞吐量下降

1. G1：分代，分区垃圾回收器，将堆内存一些不连续的内存空间分成不同的region，有四种类型可以存放，伊甸区，幸存区，老年代，大对象区。分区不影响分代算法。

123同上，4.并行清除，造成stw

优点：**标记整理，空间整合，不会产生****内存****碎片。**

**并行****清除，不会生成浮动垃圾。**

**并行****和并发进行。**

**分代算法**

**可预测会回收时间，根据指定的****时间片****大小，选择性价比回收价值最高的region区域**

缺点：**小****内存****环境没有优势，小内存更适合****CMS****。**

对于对用户进**程运行的负载要大于****CMS****（比如垃圾**清除阶段stw，其他阶段的负载其实也要大于cms）

总结一点：除parallel是吞吐量优先，适用于后台运算多，交互比较少的场景之外，其他都是响应速度优先。

## 空间担保机制：

介绍一下，在进行minorGC之前（因为youngGC往往伴随着新老对象进入老年代，空间不足的话会引发fullGC），检测一下老年代的最大可用连续内存空间是否大于新生代所有对象，如果大于进行minorGC，如果是否，则查看是否允许担保失败，如果不允许担保失败，就要进行fullGC，如果允许担保失败，查看是否大于每次进入老年代对象大小的平均值，如果大于，youngGC，否则fullGC，都是为了保证对象能够进入老年代，能够被分配到内存空间。

## TlAB

### https://www.jianshu.com/p/f1e5e03ed2f8 逃逸分析和TLAB和栈上分配

Thread local allocation buffer 线程本地分配缓存区

jvm会在堆为为每一个线程划分一个私有的区域，以供其分配对象内存。这个TLAB默认情况下很小，只有Eden区1%大小，所以针对某个线程要创建的对象空间不足时有两种方案，1. 在堆中为其分配内存，这个有问题，如果分配给TLAB100kb 只剩下1kb能用，那么后续可能每个对象都会需要往堆内存中放。2. 重新划分tlab大小，问题：如果发生频繁发生重新划分tlab的情形，那么tlab也就失去了它存在的必要性

## 三色标记法

jvm将对象标记为三种颜色，黑白灰，这个标记法对对象进行标记的过程如下：

1. 将所有对象标为白色
2. GCroot对象放入灰色区域
3. 灰色的对象放入黑色，将黑色对象的引用对象标记为灰色
4. 重复步骤三，直到没有灰色对象
5. 清除所有白色对象，即为不可达的垃圾对象

三色标记法会有两个主要问题

1. 浮动垃圾，如果在标记期间黑色对象断开了对灰色对象的引用，灰色对象其实就没用了，但是它此时已经被标为灰色，所以此次GC不会被回收。但是这个问题不算太大的问题，下次GC就会将其回收
2. 对象漏标，如果灰色对象断开了其某个引用对象，但是黑色对象又增加了对其的引用，但由于标记对象是对灰色对象的操作，所以这个会漏标，而造成对象的误回收。这个问题，是非常严峻的，相对于浮动垃圾。

上面问题出现的原因我觉得基于两点1. 三色标记发过程是并发的，执行期间会发生对象状态的变化2. 过程是针对灰色对象进行标记操作。

针对漏标问题常见两种解决方式一是

1. CMS采用的增量更新法：
2. G1采用的SATB：

## jvm常量池

[JVM知识梳理之二_JVM的常量池](https://zhuanlan.zhihu.com/p/351226127)

如何排查OOM问题？工具，参数打印运行

空间担保机制 了解一下 

CMS四个缺点

G1

四个常量池

tomcat打破双亲委派

tlab

三色标记法

11.27

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MzZhZTdkZmI5Zjk1YzcwMWZmYjk4M2FmZTZhMzZjOThfSG9zbGVGTHZLaE1jSThpU1h6ZmFaRE9ZbXFwUE1BWHNfVG9rZW46Tzc5RmJGVmRlb1ZISER4amo1WmNyNURCblNmXzE3MTE5NjA1MDQ6MTcxMTk2NDEwNF9WNA)

**打破双亲委派机制需要****继承****ClassLoader****类，重写loadClass方法，自定义里面的逻辑，而****tomcat****打破双亲委派机制，是因为框架中使用到了同一个第三方包类的不同版本，不打破会加载同一个版本的类，导致程序没有加载预想的类而出错。**

1. 引导类加载器（Bootstrap ClassLoader）：
   1. 位置： 由 C++ 实现，通常在 `jre/lib/rt.jar` 中。
   2. 作用： 加载 Java 核心库，如 `java.lang`、`java.util` 等。
2. 扩展类加载器（Extension ClassLoader）：
   1. 位置： 通常在 `jre/lib/ext` 目录下。
   2. 作用： 加载 Java 的扩展库，如 `javax.*` 等

## GC root对象哪些可以作为

1. 虚拟机栈中引用的对象： 由于虚拟机栈（Java栈）中保存了线程执行方法的栈帧，栈帧中的本地变量表中的引用对象被认为是GC Roots。这包括方法中的参数、局部变量以及返回值等。
2. 方法区中静态属性引用的对象： 方法区中的静态变量存放在类的元数据中，静态变量引用的对象也被视为GC Roots。
3. 方法区中常量引用的对象： 常量池中的常量引用的对象也被视为GC Roots。这包括字符串常量、类常量等。
4. 本地方法栈中JNI引用的对象： 本地方法栈中引用的对象也被认为是GC Roots。JNI（Java Native Interface）允许Java代码调用和被调用C、C++等本地语言的代码。
5. 活动线程： 当前正在执行的线程也被认为是GC Roots。垃圾收集器会确保不会回收正在运行的线程所引用的对象。
6. JNI 引用的全局对象： 由JNI创建的全局引用（Global Reference）也可以作为GC Roots。这是一种在Java代码中持有本地对象的引用。

这些对象都具有一些共同的特点，即它们都是从虚拟机的执行角度出发，能够直接或间接地访问到其他对象，因此被认为是不可回收的。GC Roots 的存在确保了垃圾收集器的正确性，它不会回收被其他部分引用的对象。

## JVM调优

### 什么情况下需要JVM调优？

GC频率出现了问题，这里主要是指full（Major GC） GC过于频繁，因为full GC是一个很重的操作，会对性能产生较大的消耗，造成系统停顿的时间较长，而youngGC（minorGC）造成的影响就小的多。

比如这里举个例子：智能货柜场景的例子

暂时无法在飞书文档外展示此内容

在业务初期，机器数量比较少的时候，连接少，cpu处理的过来，随着后期机器数量逐渐增多，建立连接数多，需要进行心跳检测，开门检测，等等内容，导致cpu线程占用率过高，处理对象请求的速度变慢，然后大量对象在eden区，处理完成前，对象逐渐增多，老对象就放入幸存区，慢慢幸存区空间也不足，空间担保机制，空间不足之类的又导致GC年龄还没到15的大量对象涌入老年代，其实这些对象并不是想长期呆在内存的对象，但是由于性能下降大量涌入老年代，而且成为一种现象，常态，本来youngGC回收的对象，频繁的触发fullGC回收，性能效率大大下降。这时候，可以考虑更改各个区的比例，比如伊甸区：幸存区 调整幸存区大些， 或者新生区整体比例调大，总之就是尽量避免对象进入老年代。

##### 另一个思路 增加内存大小：

一般这种单服务采用4核心8G的内存，非特殊需求会升到16G外，不会用32G（一听就是编造的一般），因为这么大的内存，进行一次fullGC等血长的时间了，所以升级内存的事情慎重考虑

### 工具：

[介绍几个JVM性能调优工具【通过实战案例讲解】](https://zhuanlan.zhihu.com/p/551301967)

1. 阿里的阿尔萨斯 Arthas 里面的常用命令：
2. jvisualVM jdk提供的一款图形化检测工具
3. jprofile 是一款商业化分析工具，提供了丰富的分析功能，包括cpu分析，内存分析，线程分析
4. Jconsole jdk自带的一个工具包括下面这些工具，都是jdk提供的一些分析工具

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ODY0MzJjNzE3YWZiMGZkYjM2NDE3MWFhNDA1NzM4ZjlfT2xYMU5vbDhSUzhkb1pvVTVkb05nbFZJYllSb0R6T1hfVG9rZW46TjJWTWI2NE5ob1NJcVZ4bkpOamNxSk1IbmdkXzE3MTE5NjA1MDQ6MTcxMTk2NDEwNF9WNA)

Vmoption 可以查看jvm的一些参数，比如可以手动打开printGC等很多功能参数

Profile 查看火焰图，里面记录的每个线程调用的所有方法，类似一个方法栈，一般看最上面那个方法，即自己调用的方法，如果过于宽了，就是有些问题。如下图的write

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NDU1ZjY1MzI2YjcxM2Y0YWRmMTdkMjgzZTg0NGU1OThfU2RZZEd2QzN0ZHk1V0ZwTTBLakZJbVNTM0J1bVVZbUdfVG9rZW46S25IcGJtOEtnb1M4S0h4bW5rc2NleW5mblJoXzE3MTE5NjA1MDQ6MTcxMTk2NDEwNF9WNA)

OOM异常的时候，生成dump文件，通过dump文件分析参数。

### jvm调优常用命令：

1. Top "table of processes"查看cpu的使用情况 top命令经常用来监控linux的系统状况，是常用的性能分析工具，能够实时显示系统中各个进程的资源占用情况。

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=YTg3N2ExYjg2ZDczMGY3YjFlYzcyNjJkMDI4ZTljYTRfZUx3UUNnR2RHQ1BjR2x4bEVJZ3I0QVRFWExKaFVWYTNfVG9rZW46VlNmTmJ6MFJPb3BjbzN4VjhrNGMwMlpVbm5mXzE3MTE5NjA1MDQ6MTcxMTk2NDEwNF9WNA)

1. Free -m 查看内存使用情况 显示系统内存的使用情况，包括**物理内存****、交换内存(swap)和内核缓冲区**内存

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NjIwMjk3MDQzM2E0ZTY3ZTFlZjQ1YWJhYWIyZjlhYTdfdFZ5V2ZSOXZ1QzdwUXY0cGc2WDhxaHVZbnI3MUpZc1pfVG9rZW46REdiRmJQaEZab1Jna0h4SVZveWNaQzE4bmJlXzE3MTE5NjA1MDQ6MTcxMTk2NDEwNF9WNA)

包括空闲，总共，已使用等

1. du： du 是 Disk Usage 的缩写， Linux 上最受欢迎的命令之一，它用来估算文件或目录占用的磁盘空间, 它也是很基础的命令，使用Linux的人都应该掌握它的用法
2. jps **功能描述: j**ps是用于查看有权访问的hotspot虚拟机的进程. 当未指定hostid时，默认查看本机jvm进程，否者查看指定的hostid机器上的jvm进程，此时hostid所指机器必须开启jstatd服务。 jps可以列出jvm进程lvmid，主类类名，main函数参数, jvm参数，jar名称等信息
3. jstat  主要利用JVM内建的指令对Java应用程序的资源和性能进行实时的命令行的监控

-gc 垃圾回收统计

```
[root@hadoop ~]# jstat -gc 3346 #用于查看JVM中堆的垃圾收集情况的统计
 S0C    S1C    S0U    S1U      EC       EU        OC         OU       MC     MU    CCSC   CCSU   YGC     YGCT    FGC    FGCT     GCT128.0  128.0   0.0   128.0   1024.0   919.8    15104.0     2042.4   8448.0 8130.4 1024.0 996.0       7    0.019   0      0.000    0.019
```

- S0C：年轻代中第一个survivor（幸存区）的容量 （字节）
- S1C：年轻代中第二个survivor（幸存区）的容量 (字节)
- S0U：年轻代中第一个survivor（幸存区）目前已使用空间 (字节)
- S1U：年轻代中第二个survivor（幸存区）目前已使用空间 (字节)
- EC：年轻代中Eden（伊甸园）的容量 (字节)
- EU：年轻代中Eden（伊甸园）目前已使用空间 (字节)
- OC：Old代的容量 (字节)
- OU：Old代目前已使用空间 (字节)
- MC：metaspace(元空间)的容量 (字节)
- MU：metaspace(元空间)目前已使用空间 (字节)
- CCSC：当前压缩类空间的容量 (字节)
- CCSU：当前压缩类空间目前已使用空间 (字节)
- YGC：从应用程序启动到采样时年轻代中gc次数
- YGCT：从应用程序启动到采样时年轻代中gc所用时间(s)
- FGC：从应用程序启动到采样时old代(全gc)gc次数
- FGCT：从应用程序启动到采样时old代(全gc)gc所用时间(s)
- GCT：从应用程序启动到采样时gc用的总时间(s)

1. jmap：堆转储快照，生成当前时刻堆内存中情况的快照，可以分析各个区域的内存使用情况等
2. 死锁问题可以使用jstack命令查看线程运行状况或者jvisualvm**工具直接查看**页面

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MmFlNzllMzM5MzNmMTg5YzFhMTNjMmExMWVlODJlZDVfRkVYWXY0ZnplNUFvSUFLQzRqMTNjNTRKRUpVMFBWOUpfVG9rZW46RUJrVGJ1Y2JHb0MyNGl4R29VNmNiTlE4bmxmXzE3MTE5NjA1MDQ6MTcxMTk2NDEwNF9WNA)note.md…]()
