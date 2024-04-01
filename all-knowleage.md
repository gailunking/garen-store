[note.md](https://github.com/gailunking/garen-store/files/14821819/note.md)[Uploadi# Xxx

实例化对象的时候其实就是在调用构造方法，换言之，如果构造器是私有的，在其他类中就没法实例化创造该对象。

# 锁：

### 锁相关的一些概念

**锁****自旋**：CAS失败然后继续重试的过程，就是自旋。

synchronized的加锁解锁机制是jvm字节码层面的monitor enter 和monitor exit。

**锁重量锁**，包括synchronized的阻塞状态，实际都是从用户态切换到内核态，这个过程十分消耗资源，所以这个自旋，lock上锁什么得这些个获取锁资源前其实做的这些CAS操作，不断重试，都是为了去尽量避免进入sleep睡眠内核态。

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MDIxOTUzZmY2YTQ5MGMxYmEzMzA5YTJjNmNiMjU5MWRfYTZJUkZYQjZ4TUY1UnpFaEhGQVpVNVVqY0JET2JUNlZfVG9rZW46RDBCM2JWdTlPbzhFTWh4dDRwUWM2V285bmtiXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

### 锁优化：

像是锁消除锁粗化锁升级，其实也是上述那点，尽可能得不让他进入sleep状态进行优化得而像AQS底层得话线程争着去修改state变量，如果你前边就是头节点，那这个线程有很大概率CAS一下能获取到锁。或者像轻量级锁，竞争比较小，可能执行个几十毫秒拿到锁的线程，所以后面的微微自旋一下可能就拿到了，那等的时间再长点，等了个几百毫秒了拿不到，也不能迟迟等着啊，就没办法去睡觉了，这么个过程。

### synchronized：

底层原理，用mutex互斥变量实现的

# 并发：

乐观锁例子：数据库修改数据，github pull push代码共同修改代码时，

## 原子类：

暂时无法在飞书文档外展示此内容

原子类的实现其实是基于CAS和自旋实现的

对于**1.取值2.修改3.放回**这个操作，CAS的话只在第三步比较一下取值是否还和内存里的值相等，如果一样则放回如果不一样则说明已经被修改了不放回然后**重试**，这种适合**并发量较小**的情况（要深究一下的话那CAS这个操作不也是原子的吗？谁保证的呢，硬件保证的，所以这些都是相对的），并且这种**乐观锁**的形式，效率是要**高**的，因为它锁的粒度更小，但如果是**并发量大**一点，大概率是每次提交都是修改过了，那就会消耗很多资源，这种情况适用**悲观锁**，将这三步锁起来，保证程序的一个正常执行。

# JVM

<https://applink.feishu.cn/client/message/link/open?token=AmVCHPA7R0AcZWCLktwBwAM%3D>

### jvm是什么？

jvm的作用是执行java字节码，它是一种解释器，负责执行java程序。jvm相关结构图如下

暂时无法在飞书文档外展示此内容

### 类加载器：

暂时无法在飞书文档外展示此内容

class是实例化对象的模板，所有的实例化对象调用getClass得到的都是同一个class，class再调用类加载器能获得其相应的类加载器

1. 虚拟机自带的加载器
2. 启动类（根）加载器BootStarpClassLoader
3. 扩展类加载器extensionClassLoader
4. 应用类加载器AppClaasLoader

类加载器用来将类（class）加载到jvm中使用，当要使用某个类时，java会采用双亲委派机制，一直向自己逻辑上的父类加载器去请求，直到启动类加载器，如果启动类加载器没有找到该方法，则报错询问子加载器中是否持有该类，以此类推。这种方式起到了保证类安全的作用，防止恶意篡改。

类加载器子系统只负责加载字节码文件 至于字节码文件能否执行，是由Execution Engine执行引擎决定的

执行引擎结构图如下

暂时无法在飞书文档外展示此内容

### 沙箱安全机制（非重点）

概念：沙箱安全机制是一种在执行环境里限制一些资源和权限访问的机制，它能够保护系统资源，用来提高系统安全性，创造了一个受控制的系统执行环境，防止程序和数据对系统的潜在威胁。沙箱通常用于执行不受网络信任的代码，如在网络上下载的应用程序或脚本

java的沙箱机制主要体现在以下几个方面

1. 字节码验证：java程序运行不会直接运行源代码，而是先编译成字节码文件，由jvm验证字节码是否符合java语言规范，排除潜在的安全漏斗。这个过程包括类型检查，访问权限验证等。
2. 类加载器：java使用类加载器来动态加载类和字节码。类加载器按照一定结构层次加载类，每个加载器负责加载不同位置的类，能够避免不被信任的类访问系统类。
3. 权限控制：
4. 沙箱类库：java.security包，里面包含了一些用于安全性管理的类

### native关键字，方法区

java中被native修饰的地方，会在本地方法栈中登记，最终会通过调用JNI（本地方法接口）去本地方法库（native libraries）中加载对应方法，它已经脱离java程序的范围，是c，c++相关的一些东西。

本地方法栈是在内存中特意开辟的一块区域，Native method stack 用来登记native方法 

目前该方法用的越来越少了，除非与硬件有关如java程序驱动打印机，企业级应用很少见，因为现在异构领域间通讯很发达，socket通讯，webservice这些。

**PC寄存器：** 程序指令计数器，是线程私有的，指向方法区的方法字节码（用来存储指向下一条指令的地址）在执行引擎配合下执行下一条指令，所占内存空间非常小，几乎可以忽略不计。

**方法区：**

方法区被所有线程共享，里面存储了方法的一些基本信息，相当于一个方法模板，与对应的实例化对象区分开。对象指针在java栈，对象信息在堆中。像static，final，Class类信息（）都存在方法区，1.8以后常量池在堆中，详情如下：

1. 静态变量：所有类的静态变量都存储在方法区，这些变量在类加载时被分配内存，在类的生命周期内存在
2. 类信息（class）：类的结构信息

### 栈

栈是jvm中的一个重要组件，它是程序运行时利用栈帧存放方法的地方 java栈与线程的生命周期息息相关，线程关闭，栈随之消失。

暂时无法在飞书文档外展示此内容

每一次方法调用都会生成一个栈帧，栈帧里包含了一个方法的局部变量，动态链接，方法出口等信息。

栈帧结构具体包含：

1. 局部变量表：里面包含方法的局部变量，包括方法参数和在方法体内定义的局部变量。局部变量中每个元素都是一个槽位，可以存储各种数据类型的信息（包括基本数据类型和引用数据类型）
2. 操作数栈：用于执行 方法的操作，包括方法调用，赋值等。方法运行时运算符可以从局部变量表中取值将计算结果压入操作数栈。（**是一个临时存储空间，**它和上边那个表的作用可以看这个连接里的典型案例https://www.wolai.com/cmkwLpXHoSFhQhdHPkhYqq）
3. 动态连接：指向运行时常量池的方法引用。
4. 方法返回地址：记录方法调用完成后的返回地址，即下一条指令在哪执行
5. 附加信息：如异常处理表

**方法区和****局部变量****表的区别**：方法区生命周期长，和程序相关。局部变量表随方法的调用开始结束相关。方法区存储类的结构信息，静态变量，常量相关的内容。局部变量表存储方法调用时局部变量相关的内容。

**指向堆中对象的引用类型变量就在****局部变量****表中**

StackOverflowError栈溢出，程序不断调用方法没有释放最终导致栈满溢出报错

### 堆 

暂时无法在飞书文档外展示此内容

创建一个新对象的内存分配流程

暂时无法在飞书文档外展示此内容

创建一个新对象的**内存****分配**流程，首先判断**有没有伊甸区**的位置，如果有直接创建/没有说明伊甸区满了，进行一次youngGC（将大多数伊甸区对象清理，剩余的晋升到幸存者区，同时对之前幸存者区的对象进行清理，存活的移动到另一个区域，并将两个区的状态进行更换），如果伊甸区有位置分配内存，如果没有查看老年代是否有空闲位置，如果没有进行FullGC，结束后有位置了进行内存分配，如果没有报OOM错

堆中元空间逻辑上存在 物理上不存在，因为新生代的KB大小加上老年代的内存大小就等于了java运行时环境的大小。

**\*关于幸存区的from和to，谁空谁为to意思是，进行一次minorGC之后，伊甸区和from区的存活对象被移动到to区，然后from和to互换标记***

#### 堆参数调整

可以通过IDEA edit的JVM option里设置堆初始内存Xms 最大内存Xmx GC幸存区最大年龄代数等

https://www.wolai.com/gErDCqHv7uzWGNTMwQTBFj

### GC回收算法：

1. **引用计数法**（不采用）：有一个对象引用它，计数+1，失去一个引用，计数-1，计数为0时GC会将其回收。现在没有jvm会采用这个原因：

   1.   1.它不能解决对象循环引用的问题（致命问题，一票否决！）

   2.   2.回收时不能移动对象，造成内存碎片

   3.   也有优点：不用挂起程序；随时都可进行，不用等堆内存满

   4. 暂时无法在飞书文档外展示此内容

2. **标记清除法**：

在堆的有效内存告急时，将整个程序挂起，然后进行两项工作

**标记**：从根节点遍历存活对象对其进行标记为可达的对象

**清除**：再次从根节点遍历对象将没有做标记的对象清除掉

**优点**：空间占用小，实现简单

**缺点**：两次遍历，耗时太多，效率低。

  垃圾收集后可能产生大量内存碎片

  垃圾回收时挂起，造成应用程序暂停

暂时无法在飞书文档外展示此内容

1. **标记压缩法：**

在标记清除法之后，进行一次压缩算法，将可达的对象移动到一起，将之前的内存碎片合并成一个大空间。

优势：对标记清除法的优化，解决了碎片化的问题

缺点：显而易见的效率问题

1. **复制算法**

将一块内存空间一分为二，在进行垃圾回收处理后，将幸存对象复制到另一个空闲的区域，当前区域变空闲；

好处：没有内存碎片，对象存在数量较少时效率高

缺点：内存空间占用大，利用率低，在存活对象较多的情况下效率差。极端情况下如所有对象都存活，需要复制全部的内存地址，效率非常差

CMS 搭子提问

CMS垃圾回收算法：用的标记清除

/*

CMS（Concurrent Mark-Sweep）算法是一种用于Java虚拟机（JVM）中的垃圾回收的算法。它主要用于减小垃圾回收的停顿时间，特别是对于大内存堆的情况。CMS被设计用于处理那些对低延迟要求敏感的应用，以减少因垃圾回收引起的长时间停顿。

CMS算法的主要步骤包括：

1. 初始标记（Initial Mark）： 在这个阶段，垃圾回收器会标记出所有的根对象，并标记它们的直接引用对象。这个阶段会导致一次短暂的停顿。
2. 并发标记（Concurrent Mark）： 在这个阶段，垃圾回收器会并发地标记所有可达对象。与应用程序的线程并发执行，这一阶段不会引起主要的应用程序停顿。
3. 重新标记（Remark）： 在并发标记之后，垃圾回收器需要重新标记在并发标记阶段有可能被修改的对象。这一阶段会导致一次短暂的停顿。
4. 并发清除（Concurrent Sweep）： 在这个阶段，垃圾回收器并发地清除标记为垃圾的对象。这一阶段不会引起主要的应用程序停顿。

CMS算法的优点是尽可能地减小了垃圾回收导致的停顿时间，但也有一些缺点。其中一个主要的问题是，由于并发标记和并发清除过程，CMS可能导致内存碎片，可能会影响到大对象的分配。另外，CMS在进行垃圾回收时，可能需要更多的CPU资源。*/

#### 总结

内存效率：复制算法>标记清除>标记压缩

整齐度：复制算法=标记压缩>标记清除

内存空间使用率：标记清除=标记压缩>复制算法

没有最好的算法，只有最适合的算法

所以JVM GC采用分代算法 在新生代，因为99%的对象都需要清除，所以采用复制算法

而老年区，对象清理率比较低，采用标记清除法（内存碎片小），或标记压缩法

# MYSQL

## MYSQL基础：

USE database use关键字，使用某个数据库

#### 第二章：

###### select关键字

Select 列名

form 表名

-- where id=1   --可以使这一行sql语句失效

Order by name

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=OTZiNThiNzc0ZjVmYTExYzAxMmNkM2YwNzA1ZDFjYzJfM285M2VBVnVFZm51dkMzQ3NVNlo4OUhtRTlXZlBXUmNfVG9rZW46UHJNRmJGdVIyb3l4b3h4NHZFUWNkWnFRbldyXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

可以看到select后面每增加一个字段，就会增加一列，甚至可以增加重复的字段并且添加运算符（加减乘除），

###### AS：

select后面的字段+AS+名字是改变查询出来的字段名

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=Y2I4MTNkN2ZkZmRjYTIzYjNiZGFkMjI4ZjkxNmViMjFfRHF2RTI5N0lJNHluZ3g5VkVBaEJxSUc1VThKdzU4ZWdfVG9rZW46U1V1U2IySVg4b05oYUt4czhObGNtQ0lzbnk0XzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

###### DISTINCT:

select后面加DISTINCT关键字可以使查询出来的结果唯一，有去重的效果

###### IN：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=YjgzMGMzMmU4ODFhNDdiOWY1MTJhOWNhOGQzY2M4ZTVfREZRQnlBQWE2UnA1cW05SlRDZ2o0UGNWdTVxVVpZdTFfVG9rZW46SlNwbGIycEk4bzdzN0V4TmVkU2Nub3BwbkRiXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

可以写成IN 格式

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MzY4NTQ5OTVkMjdiYWNmMDg3OWNkZTQ2Y2MyNzgyNmZfTFdpdFpFdFZYQkFtaElYOUZYMUJDSnEzSDZxOUNhQU1fVG9rZW46TVYwNWJ4R1JUb3l0Z1J4RUpydWMwQVlnbklkXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

两个语句是等价的 也可以在IN前面加NOT 筛选出不在这些里面的数据 **NOT IN**

###### BETWEEN  ... AND ...

where用来筛选两者数值之间的数据

###### REGEXP：正则表达式

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ODcyN2NmMzMxNjQyNDgyYzZkODg1MDMyN2IxYTY1NmVfb1VxSTZ5OFBNUVdtcjlLZkRKdmxDOFNvYmZlWUdoMndfVG9rZW46Wlk5NmJ2WWhwb3B1Tm54TGg0bWNoUjVPbnpiXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

1. 3 4行的效果是等同的

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NzQ0YWZhMTFkOTBiYzlhOGFhNDRiZTM4MjgzNDViMWVfZjFiMWJGQWtHSkxIdHl1VVdrd1lIUXJ4U3lsQjZUQmVfVG9rZW46QU9CeWJBRkJtb2pOckJ4TWI1ZmN6V2J5blJmXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

1. **^**：在REGEXP跟随的数据值前面加个**^，**代表获得以‘field’开头的值，可以看到结果里面没有。

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MzA1NTY4OTc5MDlmYzE0ODMzM2E0MDBhNjJmZTEyOWVfNTNrUG54b2xpTENWWXhGT3pGSHROaENUa1R3TjFoRFpfVG9rZW46V1RVTWJ6N0hRb3ZVY254SGZubGNPNVExbmVjXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=OWJkMjI4ZWViZmNiYWVhYjg4ODI3OGM2MjE4NDgwMDRfczNQcjVSWVZTR1M1OVNGd2JTUkRvaFBRVnNJWnFNam5fVG9rZW46UTY2dGJDZXEyb3lvWkx4ZHZxTmMzUHpLblRoXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

1. **$**： 而刀乐符号加在字段后面代表以这个数值结尾的结果，可以看到查询结果
2. **|**：或 查询符合其中任何一条要求的数据，如下图

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MGM3YzZjYzQ5YTQxMzRkOGRlNDA2ZTdlNTI2Y2M0YzNfSEswMmZoSGdkeEkwRm96dUJEVVFTaFl6bFBQUEFIWXJfVG9rZW46SFRXT2JqMjY5b2FsVWl4MzJ1dWN5ang1blRiXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NWJlYjljNTZiYTJjMjMwNmVkYTcxNDA1MzUzYzA2MDVfT3lHYlA3eUc4cHJscHR4TWp3clNhTUs2a1NZMUp6ZGdfVG9rZW46QWhtNGJheEE3b2k4MVV4a21hV2NYUUNJbnBkXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MDI3ZDAzN2I3ZGNmNGU1NDEzMmMzMmE2M2I4NTc2YWFfR284bkF4dzlFNmYzMXpNS0FKb0NVUzNQWDd3dWVYVFpfVG9rZW46Q0RwVGJYU0lBb3A4TlV4YXozY2NuaElkblBkXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

还可以使用复合运算，如上图，查询这俩搭配使用的结果

1. []：中括号的使用如图看把

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ZWNlYTc3ODEwNWY5M2NmZTc1MDNkYjNiMzMwODA3YThfNm9TQjRoaFpCNUZnWnJ1b3dneVVjajRsWXB2bHlTdHpfVG9rZW46SEo2RGI0QnAwb2h5ZWN4VXZFTmMzSEFHbnFkXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

1. ：ORDER BY ...DESC

如图，可以这样搭配使用，查询出来的数据先按state降序排序，相同的按first_name降序/（升序）排序看怎么输入的语句 

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ODc4OWJjMDQ2NmFjM2JlMDRlNDdmY2JjOGFiNTRiY2FfQlc5Z25oVGVMeW52VFpqTjhRSE11U2RBQzBQSjFvWnRfVG9rZW46R3ZXQWJ4VWZzb1VMUkh4ejJTVmNldzJVbkUwXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

1. LIMIT 限制查询出来的数据的条数，然后可以作分页等操作如：

该语句的意思是查询所有顾客但是跳过前6条，往后获取三条 就得到了7 8 9顾客

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NDA4YzBiNjI1YzBjMmZlOGU5MTc2MzhiMmU0NzFiOWNfQURwSDJRVDZPRzc3TVVPc2l6M09aUHhZNzFtTXNVWVpfVG9rZW46VExLc2I5ZWdLb0REdTl4MkNkQWNLR3ZWbmplXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

#### 第三章：连表查询

连接 from 表名 join 表名 on 连接条件，指明了这两张表的每行字段是靠什么条件连接在一起

##### 跨数据库连接：

数据库的这个表和另一个数据库的某张表相连  是表名 join 库名.表名 ON 连接条件

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ZGE5YmJkMzM0MjhkYWI2NzExYmE4YzdiMzA5YmI0M2NfU2U5ZHJoZTBFQkZuelBQNnJOMjlJWndYczJkRkdOUjRfVG9rZW46QWVGaWJFMG1ib1lGZWV4TVloSWNGaVpVbmllXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

##### 自连接：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ODdmNjVlN2I3ZGYyZjhmNGM0OGE0NmQ3MDMwODdlOTJfbWQweVBab3BrTFF6S0FJMWJmMHF4U0dLcFNwckttWEJfVG9rZW46UVlicGJlOXFVb0xMdEp4WTVhbGNhZ2I4bnVjXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

自连接是用同一张表做连接，同一张表有一列标识了表里另一条数据与自己的关系，比如我的领导者是id为**的那条，如上图连接条件领导者id=员工id就连接起来了。

##### 多表连接：

可以连好多张表查询表1 join 表2 on 表1表2连接条件 join 表3 on 表1表3连接条件 

##### 复合连接：

**两个表里有一张表没有唯一表示一行数据的****主键****，但是有两列数据能唯一表示这个主键，并且他俩一般就是一目了然起这个作用的，比如订单ID和产品ID联合主键：**

如图一个订单有多个产品 产品有重复，订单也有重复，所以他俩充当联合主键，做连表查询的时候语句如下下图

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NDQ2MTA2YWU5NDRhODk3NWZjNTA3Mjc3ZmZmNDExZTZfOTdNc0NzYk10UVg4SXljV1d6WEl6SjlZanhodm1XT0hfVG9rZW46TllXNWJHS05Eb1l4dUR4WE5JWWM2NTJnbm5mXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=Y2E4NmRiMGVmOWIxMjFkZDExMmYyMzc1YWMzOTkwZDFfV2hUUFJXNjVZbDNpUFVTMjEwRHZnNFVsUUJ4aDA1SHhfVG9rZW46SktkVmJQQU5Cb0M4NUx4N2Z4SWNDZDZ2bkZiXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

##### 外连接：

Left join 和right join 相比于内连接，可以获得不符合条件的数据行，左连接就是左边表的所有数据都保证存在，右连接就是右边表的数据都存在

##### 多表外连接：

如名，多表连接中拒绝把不符合条件的那行数据排查掉，用外连接保留结果

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=YWE0MTkzYzYxNjc2NTJiY2RlY2E5ZWUzMDllNzlkZTVfU25lbFAwbDZKcWRpa3VLMFZjWG1MY09nTGc3TFhmYVhfVG9rZW46TFNGVmJDcloxb2dHT1J4Q0c4NmNUS1RUbnJiXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

现在有订单表，顾客表，发货人表和状态表，这是查询了每个订单的id，日期，顾客名，发货人名和订单状态，因为只有发货人是不确定有没有的，而其他三个表每个订单都有对应的数据，所以只需要发货人表前加left就行（注意：因为题目要求发货人没有的话显示null，也即要显示所有的订单）

##### 自外连接：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NmNhOTI4MDdkMTE3ZmQ2NjFkODgzNjhiZjNkNmE2NzFfNERITG9neXcxTHV5R25IdGN4RWxyTnpueGRhWFZnRUpfVG9rZW46VmF4UWJQNXJZb2JsWnh4Sk9ZNmNaWkg5bmJiXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

如图：自连接里不符合条件的那个也会被查出来

##### USERING关键字：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MGI1MzRlZTI4MzA5OTk3ZWQ2N2FkOTU4MGNiZTAzMWRfbkVmQnZVNUY5NVlhaVRTeXh6RDRXcHJBdzNZd3VVNGtfVG9rZW46UkxrdGJ1RFB6b2J4RkV4RmVaR2M3OEExbndmXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

注意字幕提到的使用条件

##### 自然连接：（不推荐使用）

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ZjM4MjE5OTBkNGYzNThhMzcyMWY1OGJhYjVmMDU3Mzlfa3ZVbVdGQ1U3RzhhME1hT3pET0I2U29TTFpKN3MyT01fVG9rZW46WE1QZWJOVDZSb25TQlF4SFFaa2M3Rjl6bkhnXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

这个连接是让数据库看着办，自己根据两个表的字段建立连接

##### 交叉连接：（笛卡尔积）

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ZmI4ZDEwM2M3YjY2N2FhNjYzYTVkNDVlM2UzZmMyMDZfYWJUbmdsOUVIbHNKQld6WnlEUzBPT0hEMmw2ak5OV0xfVG9rZW46QmNwN2I2RXdwb3ZJZUp4Y0Rvc2MyOEhGbk1RXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

上面是显示写法，隐式写法没有join表名加，写在from后面。

##### UNION：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ZDBiYzM4MDAyODYwYTJjZGQwYmVkNTJjOTY2NGIwODBfaThtVE56NmZ3WlZCdUNiWGs2S2tONENnT2xzUG1rRjVfVG9rZW46SG9PNGJMbWhnb21jZ0V4Wk81MWNrTnhubnpjXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

将查到的不同数据集联合起来

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=M2UzYTM5NTQwMTQyOWRkMTBlZjlhMmU5NTU5NWFiYWNfVDRnVXNheGs2MXNJWFFvRzdkY0hiQm9sajdPalNqaVVfVG9rZW46VmlicWIxeG41b0J2RVF4QjZBYmM5VXlJbnNnXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

#### 第四章：删改查

##### 插入分层行：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NGVmODE0MjkzMDYxZTE3YTUzZmJiZjE4ZjY1NjdiZDZfZ1M5QTJjdVZhUFE1MzZ4YWg0eVNnakdlZTRYY3lJMkVfVG9rZW46TU1LS2J4S0JVb0lEaFJ4d0Z5eGNDQWJHbnNkXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

往订单表里插入了新订单，然后订单项表里拿着订单表里的新订单id用内置功能添加了这个订单包含的订单项。

##### 创建表复制：

可以创建一个表的同时把另一个表复制过去；也可以插入数据的时候批量插入另一个表的数据后者如下图：

这两个表结构完全相同，所以第一个（）省略了，往所有字段里插数据，而value被select查出来的所有数据代替

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MGJhNTQ0ZTIwZDQ1ZjlmNTU2MjkwZGIyN2NhNzRjNDNfZjdtRHI4dUpqejk2UURleDI2em9rc2daalNTaUtFbVNfVG9rZW46QmsyZGJzd3d3b3J0WXB4NnVscmNvamJCbnViXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

##### 更新语句：

Update 表 set 字段名=目标值 where 条件

##### 更新语句配合子查询：（重点看一下）

where条件里，先看子查询得出的是数据，在这里是（1和3），数据得话就不能直接写到WHERE后面，所以加了client_id但是又由于不止一条语句，所以加了IN，至此这条语句完成更改i数据在居民id在CA和NY洲里的数据

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=N2Q0NjI1NDQ3MjUyYzkwZmUwYjRkOTIwN2UwNjgwYjdfNjBPaFE4TU5jUDY1SWRmN2I0QzBMOEdTbEE2ZkN0SU1fVG9rZW46QThMY2JVaUxNb2RmQll4ZnpOb2M2bjJVbmFmXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

#### 第五章：聚合函数

##### 汇总数据：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MGEyMzU3MDhkYzVkZTJkMGM1ZTE1NDU0ZThhMDQzNjNfd0tvajNsOXhUWDdza1MxQjlkOXVNQ1hlZzRtN2l6UDRfVG9rZW46QmFKcGIyNmk5b0dtQ3F4eVhvWmNBTzNFblBmXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

聚合函数只计算非空值

##### GROUP BY:子句

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MjVjYTM3NmI3YTc2YjdmOTM1ZTA4Zjc0NDc0NjY0MjNfa3RJZEQ5WXlpOXd1Y013MEZzTXpOc0x4MkVIN2h5dGJfVG9rZW46UHltOWJ4NENsb01NMEx4VFFuT2NLQVdtbnpnXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

汇总数据计算所有的价钱，然后按id分组，价钱排序，然后添加一个2019年之后的条件，最终得出上述语句

##### HAVING子句：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=N2ZhZGIyYzZhNTA2NjE3MTkzMGM0ODJkMWM1OTJkNDRfeFZiM29oenRkRW53N0p6UUJjUmtBQ0dHZmtSZ3FGemtfVG9rZW46UWJKUGIyTzRPb0Y0WTB4WTRNRWM2ajdLbkZmXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

看这个例子：假如我们只想要总销售额大于500的那一条，不能写where，因为where 时还没有对总金额分组。正解如下：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=N2NkNzkyMjNhMWJlZTczZDU1OTBkYTU5NDcwZDE5YmJfeTM5MFRMODVkeGZyM3dRWG5WcFpuelFla1hQaXZFbGpfVG9rZW46VTN2Y2I4VXhxb0JJcWl4c2hNWGMwRFlYbldnXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

在最后加HAVING+条件，所以由此可知，HAVING不能单独使用，只能配合GROUP BY分组使用，用来对分组后的聚合函数的值作条件判断筛选用的

##### ROLL UP:

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MmIwZTc0ZGVmN2VjNzkxNjhiMTIyMGU4Njk3MDA3YjRfS1kzUGJoMGl2SWdoTUVMZkJDa0lia2kxbTRlRGtsaGhfVG9rZW46V2dMMmJXdWxsb0VZUTB4ODRVb2NCU0JEbjBlXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

##### ALL:关键字

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ZDBjYzViZmVlM2M0YTEyZTA5ZWRhNGJhYTJiYjRhNGNfN283ZUVqNXRJQlUwUkxlb1pkTUFQdTVyS2pGNWxKQWhfVG9rZW46UW93a2JKeGJjbzdQRmZ4MFh0WGMyOXBiblFjXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

比括号内所有值都大，就返回。

##### ANY：相对的 它是大于任意值就返回，也就是最小值

##### 相关子查询：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=YTM5NzU3NjZkNWRjMjg0ZDMxNGNhYzRjNDQ0YjcxMDdfRllNTlVOMXRka0hiM1Q2Yk11Q1lXMjludWZOb2pMeEZfVG9rZW46WFNWY2JwMU10b3ZHdmF4VnlmcmNlRGpHbmxkXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

查询每个部门中大于该部门员工平均薪资的员工：如图两个表是同一表子查询外查询存在关联性，所以是相关子查询

##### 子查询先跳过，回头看：

#### 第十一章：事务

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MzI4NzViZDAyZDZlMThmMTBlMzlmYzRhM2RiODY5MGNfNDJDcmFWVFNuVm5zUWc2RndyR1NOM00zYno5NUdHejdfVG9rZW46RW0xcWJYUHJpb1o1dWZ4cm1SUGNYWHpzblRkXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

开启事务和提交两个过程，其中任何一个出问题都会回溯

##### 并发和锁定：

默认情况下，一个事务内对一行数据做更改时会加锁，在这个事务未提交之前，有其他客户端想对同一行数据做更改，需要等待， 等待前一个事务提交，所以通常情况下不会发生并发问题

##### 事务隔离级别：1234

[大白话讲解脏写、脏读、不可重复读和幻读](https://zhuanlan.zhihu.com/p/150107974)

**脏读**：这就是脏读。它的本质是事务 B 去查询了事务 A 修改过的数据，但是此时事务 A 还没提交，所以事务 A 随时会回滚导致事务 B 再次查询就读不到刚才事务 A 修改的数据了，这就是脏读。据。

**脏写：**所谓脏写，就是我刚才明明写了一个数据值，结果过了一会却没了。而它的本质就是事务 B 去修改了事务 A 修改过的值，但是此时事务 A 还没提交，所以事务 A 随时会回滚，导致事务 B 修改的值也没了，这就是脏写的定义

**其实总结一句话，无论是脏写还是****脏读****，都是因为一个事务去更新或者查询了另外一个还没提交的事务更新过的数据。因为另外一个事务还没提交，所以它随时可能会回滚，那么必然导致你更新的数据就没了，或者你之前查询到的数据就没了，这就是脏写和脏读两种场景。**

1. **读未提交****：**最低的隔离级别。一个事务可以读取另一个事务未提交的修改。这可能导致脏读（Dirty Read），即读取到未提交的数据变更。
2. **读已提交****：**线程读到的一定是别的线程已提交的事务，不会发生脏读问题,如果另一个线程更改了数据，但是还没提交，线程A是读不到的，只能读到修改之前的数据
3. **可重复读****：**T1在同一个事务的对同一数据两次查询操作之间，其他事务如T2对该数据进行了修改，导致两次读操作读到的数据不一致，而可重复读解决了这个问题，保证每次数据读取一样，就算读的过程中数据更新了
4. **序列化****：**用来解决幻读问题，事务之间完全隔离，不允许并行，会导致性能下降，开启序列化级别后，开启A事务，开启B事务，A事务要进行读操作，在读之前会进行等待（转圈圈操作），等B事务的更新操作完成后，A再读，就不会出现幻读问题了

从上到下，越高隔离级别越少的并发，用到的锁越多，相对更少出现并发问题，反之越低越容易并发，因为意味着会有更多用户同时接触同一批数据，同时也更容易出现并发问题

**幻读****：**T1事务读取查询了一批数据要对他做操作如找大于10积分的，而T1事务提交前T2更新了一条数据，又增添了一个大于10积分的客户，而T1事务结束提交之后，那条新增的数据并没有被操作，这就是幻读现象，这种突然出现的数据，就和幽灵一样

幻读就是你一个事务用一样的 SQL 多次查询，结果每次查询都会发现查到一些之前没看到过的数据。注意，幻读特指的是你查询到了之前查询没看到过的数据。此时说明你是幻读了

**Set** **TRANSACTION ISOLATION LEVEL** **READ UNCOMMINTTED**

#### 第十二章：数据类型

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NmI3OGQ4Yjk0NTM2NDQ5MTNlMzFjYzk0MDI4NDJkY2NfZWpucjYzOHoyU3REUDljQ08xYkdObVhBeXNBSVhqMEFfVG9rZW46S0l6SGJ6S05ZbzYxYWR4a1Q4NWNaMFlabmgwXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

##### 字符串类型：

[02 字符串类型:不能忽略的 COLLATION](https://learn.lianglianglee.com/%E4%B8%93%E6%A0%8F/MySQL%E5%AE%9E%E6%88%98%E5%AE%9D%E5%85%B8/02%20%20%E5%AD%97%E7%AC%A6%E4%B8%B2%E7%B1%BB%E5%9E%8B%EF%BC%9A%E4%B8%8D%E8%83%BD%E5%BF%BD%E7%95%A5%E7%9A%84%20COLLATION.md)

char（）：默认1 0-255

VARCHAR():max：65535 字节（-64KB）

MEDIUMTEXT max：16MB

LONGTEXT max：4GB

TINYTEXT max：255 bytes

TEXT max:64KB

字符串是使用最为广泛的数据类型之一，但也是设计最初容易犯错的部分，后期业务跑起来再进行修改，代价将会非常巨大。希望你能反复细读本讲的内容，从而在表结构设计伊始，业务就做好最为充分的准备。我总结下本节的重点内容：

- CHAR 和 VARCHAR 虽然分别用于存储定长和变长字符，但对于变长字符集（如 GBK、UTF8MB4），其本质是一样的，都是变长，**设计时完全可以用 VARCHAR 替代 CHAR；**
- 推荐 MySQL 字符集默认设置为 UTF8MB4，可以用于存储 emoji 等扩展字符；
- 排序规则很重要，用于字符的比较和排序，但大部分场景不需要用区分大小写的排序规则；
- 修改表中已有列的字符集，使用命令 ALTER TABLE … CONVERT TO ….；
- 用户性别，运行状态等有限值的列，MySQL 8.0.16 版本直接使用 CHECK 约束机制，之前的版本可使用 ENUM 枚举字符串类型，外加 SQL_MODE 的严格模式；
- 业务隐私信息，如密码、手机、信用卡等信息，需要加密。切记简单的MD5算法是可以进行暴力破解，并不安全，推荐使用动态盐+动态加密算法进行隐私数据的存储。

##### 整数类型：

整数类型使用相关事项[01 数字类型:避免自增踩坑](https://learn.lianglianglee.com/%E4%B8%93%E6%A0%8F/MySQL%E5%AE%9E%E6%88%98%E5%AE%9D%E5%85%B8/01%20%20%E6%95%B0%E5%AD%97%E7%B1%BB%E5%9E%8B%EF%BC%9A%E9%81%BF%E5%85%8D%E8%87%AA%E5%A2%9E%E8%B8%A9%E5%9D%91.md)

今天，我带你深入了解了 MySQL 数字类型在表结构设计中的实战。我总结一下今天的重点：

- 不推荐使用整型类型的属性 Unsigned，若非要使用，参数 sql_mode 务必额外添加上选项 NO_UNSIGNED_SUBTRACTION；
- 自增整型类型做主键，务必使用类型 BIGINT，而非 INT，后期表结构调整代价巨大；
- MySQL 8.0 版本前，自增整型会有回溯问题，**做业务开发的你一定要了解这个问题；**
- 当达到自增整型类型的上限值时，再次自增插入，MySQL 数据库会报重复错误；
- 不要再使用浮点类型 Float、Double，MySQL 后续版本将不再支持上述两种类型；
- 账户余额字段，设计是用整型类型，而不是 DECIMAL 类型，这样性能更好，存储更紧凑。

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ZTU3ZmQwMzVlOWYxODVjNzc0NTQ4OTFmZTMzYTA2MGZfODFiMHBKTGNCaWZNcVFmcFU0eDRLUlZMcmZjUlhYZmdfVG9rZW46TW1OamI4N3ZXb1NRQnZ4a2g1VGNSY2dYblRlXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

##### 定点型和浮点型：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NzA1OTI2NzU4ZjA0ODMwY2NmMmFiMTJlNDYyMjFiNTRfWjRNZElETVMwZmNKQ2dDQVFWQTF0MFhTOEFYcXNnSHZfVG9rZW46SG0wOWJZd1pLb0Z4Z2Z4dHR0emNiVjh6bmliXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

p为整数部分大小，s为小数点后位数

##### 布尔值：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ZmIwMjBlNDAzOTRkZDExZmQ2NmM2NDUxY2Q0ZDE0ZmRfdHk0WXptakswakUxVEJOT2kxajF3OFdFWGlVT1NCTFpfVG9rZW46Q0hNdWJ5NDlFb3lybzZ4QUZBUmNMNFppbk1lXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

有布尔值，但有时1 0足矣

##### 枚举值和集合：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ZGRiYTIwYjZiYzUwMDgzNTI2NWFiNTQ2M2I5OTI5M2VfekxkMHgzMHJuUDlzb2hZajhGQVZpV0lXRXJTR1BuTGlfVG9rZW46WTBOZGJXek54b05FMU54bjUxNWN3cXUybmliXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

一般这俩都不建议使用：

枚举：只能选择规定几个数据，插入规定之外mysql会报错，缺点：

1. 如果需要更改如添加和减少枚举的值，则整张表都会被重建一次，如果表内数据成百万上千万条，则会耗费大量时间
2. 如果相对枚举值添加相关的附属信息，如为大中小添加实际的尺寸（每个值都不一样），做不到，并且应用程序对枚举值数据的获取还需要设立一个下拉表以获取固定类型的值
3. 枚举值不可被重复使用

##### 日期类型：

[03 日期类型:TIMESTAMP 可能是巨坑](https://learn.lianglianglee.com/%E4%B8%93%E6%A0%8F/MySQL%E5%AE%9E%E6%88%98%E5%AE%9D%E5%85%B8/03%20%20%E6%97%A5%E6%9C%9F%E7%B1%BB%E5%9E%8B%EF%BC%9ATIMESTAMP%20%E5%8F%AF%E8%83%BD%E6%98%AF%E5%B7%A8%E5%9D%91.md)

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NWQ4OTc5NWExMDIxYWNiMGFkNDlkYjAxZmFhODg5OTJfUXpSQk9ka0dQdVlKbllRdGFEZFh2eGlZT2xzeWltc3NfVG9rZW46VXJVdGJSdUNJb1dJZHh4OEJvcWM5NjFMbm5oXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

2038问题，4字节32位机的设备从1970-1-1按秒计时到2……31之后就是2038年

日期类型通常就是使用 DATETIME 和 TIMESTAMP 两种类型，然而由于类型 TIMESTAMP 存在性能问题，建议你还是尽可能使用类型 DATETIME。我总结一下今天的重点内容：

- MySQL 5.6 版本开始 DATETIME 和 TIMESTAMP 精度支持到毫秒；
- DATETIME 占用 8 个字节，TIMESTAMP 占用 4 个字节，DATETIME(6) 依然占用 8 个字节，TIMESTAMP(6) 占用 7 个字节；
- TIMESTAMP 日期存储的上限为 2038-01-19 03:14:07，业务用 TIMESTAMP 存在风险；
- 使用 TIMESTAMP 必须显式地设置时区，不要使用默认系统时区，否则存在性能问题，推荐在配置文件中设置参数 time_zone = ‘+08:00’；
- 推荐日期类型使用 DATETIME，而不是 TIMESTAMP 和 INT 类型；
- 表结构设计时，每个核心业务表，推荐设计一个 last_modify_date 的字段，用以记录每条记录的最后修改时间。

##### Bolb类型：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=M2Q0MTE0YWIzMGU4N2JiOTgwZTFkZDQxMjQ1NGRiMzZfZktiWmFOQjNJazk4ZGFmaFJWdUsxeTdiWEtmWWR1NTNfVG9rZW46WnNTcmJhSGplb05uWXd4SnMyOGNqZVRZbjBkXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

可以存储大的二进制数据，图像这些都可以 但一般不推荐因为有如下四个问题：

1. 数据库大小会迅速增加，弱化数据备份功能
2. 性能问题（把图像从数据库提取出来，总比从文件系统中慢）
3. 要写更多代码存储图片的话

##### JSON类型：

[04 非结构存储:用好 JSON 这张牌](https://learn.lianglianglee.com/%E4%B8%93%E6%A0%8F/MySQL%E5%AE%9E%E6%88%98%E5%AE%9D%E5%85%B8/04%20%20%E9%9D%9E%E7%BB%93%E6%9E%84%E5%AD%98%E5%82%A8%EF%BC%9A%E7%94%A8%E5%A5%BD%20JSON%20%E8%BF%99%E5%BC%A0%E7%89%8C.md)

可以以JSON格式存储数据，然后可以用mysql的语句从中去特定的键值对或者做筛选

JSON 类型是 MySQL 5.7 版本新增的数据类型，用好 JSON 数据类型可以有效解决很多业务中实际问题。最后，我总结下今天的重点内容：

- 使用 JSON 数据类型，推荐用 MySQL 8.0.17 以上的版本，性能更好，同时也支持 Multi-Valued Indexes；
- JSON 数据类型的好处是无须预先定义列，数据本身就具有很好的描述性；
- 不要将有明显关系型的数据用 JSON 存储，如用户余额、用户姓名、用户身份证等，这些都是每个用户必须包含的数据；
- JSON 数据类型推荐使用在不经常更新的静态数据存储。

#### 第十三章：

##### 外键约束：

**一个表的****主键****被子表做了****外键** **要设置一个外键约束，包括两个操作，外键更新和外键删除：**

外键更新一般设置为级联，也就是主表修改了主键值，子表自动同步。

外键删除一般设置为取消，即不允许删除主键值，因为自己的子表还关联着数据，主键没了子表难堪，存放着虚空数据。

##### 三大范式：

###### 第一范式：第一范式要求表中的每个字段都是单一的、不可再分的数据单元，不允许包含集合、数组或其他非原子性的数据结构。

举个例子：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=OWQ4OTNmZGJiN2VhYjY5MWY1NTYxNWNkMzVmOWJlYzRfNlc3dTViTWU0MU1ZUGVDeFJOcVh4QzBMcGVMUWMxb0RfVG9rZW46WnVVRGJ4R3VTb25UZEd4NG1XRWNiandPbmZmXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

###### 第二范式：

第二范式

首先应该符合第一范式

要求非主键列应该完全依赖于主键，意思是一个表中的字段必须是描述同一事物的，表中的每一列都应该与这些列相关，而不是与其中的某一部分相关，这有助于消除数据冗余，提高数据库的一致性。

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=YmM4MGY4ZDZlZjU1ZDMwZTNjOGRlMTcwMGYxOWU2NzBfV2JhY2wxbFFUWVNxcGp5U25YNE5XOWdqOTQzVUJ1MkVfVG9rZW46U3R4NWJQTndRb1Jqck94WTF2bGNYRkh3bmRnXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MDgwN2VjZmI5MTdlZjIzYzFhZDM1OTU1M2VhOTk1YTJfcHJyd0FMQTJNWkpUNW1PQ1NRbE1POEJod2FEaDFkelFfVG9rZW46UlVRaGIyZHdCb2NsRTV4NmxQbGN2UXB2blNlXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

###### 第三范式：

第三范式（Third Normal Form，3NF）是数据库设计中的一个标准，它要求一个数据库表中的所有非主键列都应该直接依赖于主键，而不依赖于其他非主键列。简而言之，第三范式消除了表中的传递依赖。

在第二范式的基础上，第三范式进一步强调非主键列之间的直接依赖关系。

第二范式的回顾：

第二范式要求每个非主键列完全依赖于整个主键。

第三范式的要求：

除了满足第二范式的条件外，第三范式还要求每个非主键列不依赖于其他非主键列

举个例子，第一列是成本，第二列是收入，还有一列是利润，利润受收入和成本总的影响，前者改变，利润不改就错了，换句话说，它依赖了非主键值，所以不符合第三范式，应该另创一个表来存放它。

#### 第十四章：创建表结构与数据库

**创建数据库：**可以 CREATE DATABASE IF NOT EXIST db_mm；

**创建表:** DROP TABLE IF EXISTS customers

CREATE TABLE customers（

字段名 类型 附加（NOT NULL,PRI..,AUTO_INCR...）,

.....

）

**修改表结构：**！ALTER TABLE customer

ADD last_name VARCHAR（50）NOT NULL AFTER first_name，

MODIFY first_name VARCHAR(55) DEFAULT '' ,

DROP points

;

**创建表关系：**

```
DROP TABLE IF EXISTS orders；
CREATE TABLE orders（
order_id INT PRIMARY KEY AUTO_INCREASE,
customer_id INT NOT NULL,
    FOREIGN KEY fk_orders_customers (customer_id)
        REFERENCES customers (customer_id)
        ON UPDATE CASCADE
        ON DELETE NO ACTION
）
```

#### 第十五章 索引：

索引是为了提升查询效率而存在的，一般是查询的时候创建索引，不要在整张表上创建索引。

###### 创建索引：CREATE INDEX idx_name  ON customers（name）

###### 查看索引：SHOW INDEXS IN 字段名

###### 前缀索引：对前多少个字母做索引，如一共1000条数据取5个字符做索引有966种情况，取前10个字符是996 那么用5性价比很高，基本都能查到，而且效率高

###### 全文索引：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=YWQ5Mjk4YWMyMGM0YTA5ZjNmOTUzYmNjMWE5ZjQ0ZTVfNGNIbzhCOFVGY1RrQjFmMmdBUlM4S2E5ZXd1YjNJd2RfVG9rZW46VGdRVGJUZVNGb1YxQWh4ZWRJYmNOTE5IbllmXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

可以做字符匹配查询，按相关性降序排序

###### 复合索引：给两个以上字段做联合索引 CREATE INDEX idx_name_age ON （name，age）；

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NGFlMDVlNTQzZWFjZjI5ODk5ZGJlMGQxY2IxYTBkODFfajVpN09hbXRZekFJcUU1MzZmRGRHWUZudTJRa3FkZ1VfVG9rZW46TGJXdWJ5OVU2b1V5UFV4UWFPQ2NrbkhhbjJjXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

如果只有单独的两个索引，name和age的话，只能用其中一个做索引，而上图中我们看到where是两个条件，所以另一个要进行全表查询，效率低下，此时联合查询解决了这个问题，三选一选了它，扫描数据数也少了很多

###### 复合索引列顺序：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NDMxZWExOTYyYjg3YTI3Nzg5MjEzOGE3MzIwN2UxMTRfZlZxQjVoaFI0M0lGeG04WTU2WXFidThZSkZURU9xM2ZfVG9rZW46RzRzd2JMaU9Yb3NVZVl4bWNIamNqYzdnblFkXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

比如查询在某个洲姓名以A打头的顾客，创建的复合索引（state，last_name)和(last_name,state)是不一样的，比如采用前者先按州查询到了10行数据，那就10条里面检索A打头的就行了，而先按姓名分出了50条打头的，然后从50条里找洲是要慢于前者的

复合索引（Composite Index）是指在数据库表中同时包含多个列的索引。索引的顺序对查询的性能和效果有影响，因此选择复合索引的列顺序是重要的。以下是一些关于复合索引列顺序的考虑：

1. 查询条件的顺序： 如果你的查询经常使用某几个特定列的组合进行搜索，那么在复合索引中将这些列放在前面是有益的。这样可以更有效地利用索引进行范围查询。
2. 等值条件优先： 如果查询中包含等值条件（例如，`WHERE column1 = value1 AND column2 = value2`），那么将具有等值条件的列放在复合索引的前面，以提高这类查询的性能。
3. 区分度高的列优先： 区分度高的列值分布更广泛，对索引的效果更好。因此，如果有一个列的值相对来说更加唯一，可以考虑将其放在复合索引的前面。
4. 经常用于排序和分组的列： 如果查询经常包含`ORDER BY`或`GROUP BY`子句，可以将这些列放在复合索引的前面，以提高排序和分组操作的性能。
5. 避免过度索引： 不要过度索引。只选择对查询性能提升最为明显的列，因为索引的维护也需要额外的存储和处理成本。

###### 索引失效情景：

## MYSQL中高级：

### 索引：

创建索引就是根据索引字段，在底层生成了一个按索引字段排序的B+树，如图

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ODkwODBiMWU1YWVlZmE2ZGE1ZjU4NmI0ZmYzZDFiNjlfVm55TWRDZnk5d3l0bndBYlJkczVCOGZDbzFOZlZxSEpfVG9rZW46UGxveGJ2aU9Cb2VEOVB4Yk9wYWM2WXEwbmtjXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

索引是（name，age，position）然后底层的B+树也是这样排的，查询条件是

1. WHERE name=xx and age=xx 他就按照name先查询，然后按age查询
2. WHERE age=xx and name=xx mysql 底层解析树会耗费一些性能优化这个查询，先name后age查询
3. WHERE age=xx 这个就没法走索引顺序了，但是还是会比不加索引快

##### 聚簇索引：

索引和原始数据存放在一起的存储形式，或者整个树形结构中既有索引又有原始数据。聚簇索引都是由主键作为索引值，所以一个表中只有一个聚簇索引。

\> 这里我们可以说：主键索引其实就是聚簇索引，这两个名字是从不同的角度来说的。 ——> 主键索引：是指这个索引是根据主键字段来创建的 ——> 聚簇索引：是指这个索引的树形结构中是否同时包含了『原始数据』和『索引目录』

不仅『主键索引』和『聚簇索引』这个两个概念是指同一个东西的两个不同概念，而且它们其实也就是****数据表本身****。**也就是说其实我们并没有在原始数据库表之外再另外创建索引表**，而是应该说：**数据库表本身就是按照** **B+Tree** **的结构组织起来的，底层就是这么存储的**。

##### 主键索引：

按主键字段建立的索引  区分一下聚簇索引的定义！ 聚簇是指树形结构中包含目录和原始数据行，主键是指以主键字段创立的索引。

##### 普通索引：用非主键字段创建的索引

索引值和原始数据不在一张表上，叶子节点存储的是索引字段值和主键值。

术语 "普通索引" 更常用于指代非唯一索引、非主键索引和非全文索引的一般性索引，而这些索引可能是通过显式地创建而得到的。

##### 回表：（动作，流程）

在非聚簇索引表里查询字段，并没有再这张表里查到所有它想要的数据，需要带着主键值去聚簇索引表查，这个过程就叫回表

##### 互斥：

##### 索引覆盖：

在查询数据的时候，不用回表就能返回想要的值。就是索引字段正好覆盖了包含了想要查询的字段。

##### 联合索引：

创立的索引有多个字段

##### 索引下推：

##### 唯一索引：

由unique定义，不允许被索引的数据列包含重复数值。主键索引是一种特殊的唯一索引，由primary key定义

索引失效----如果在一个数据表的某个字段存在重复值，并且尝试为该字段创建一个唯一索引，数据库系统会拒绝创建索引并报错。唯一索引的目的是确保被索引的列中的每个值都是唯一的，而如果在创建索引的时候已经存在重复值，这将违反唯一性约束。

##### B+树优点：两个

1. 矮：减少IO次数，效率高
2. 胖：原始数据都在叶子节点，用双向链表连接，支持范围查询

K哥讲解索引 -问题：当最左边的索引字段没有匹配的时候，翻目录也比翻书快怎么理解，这个字段是怎么生效的

### MYSQL对大表DDL操作存在风险，怎么处理？

给大表加索引、加字段属于DDL（数据定义语言）操作，任何对MySQL大表的DDL操作都值得警惕，不然很可能会引起锁表，报错`Waiting for meta data lock`，造成业务崩溃。那么如何对大表进行加索引操作？

早期DDL操作分为`copy table`和`inplace`两种方式。

copy table 方式

1. 创建一个和原表相同的**临时表**，在临时表上执行DDL语句
2. **锁原表，不允许****DML****（数据操作语言），允许查询**
3. 将原表中数据逐行拷贝至临时表（过程没有排序）
4. 原表升级锁，禁止读写，即原表暂停服务
5. rename操作，将临时表重命名原表

inplace 方式（fast index creation，仅支持索引的创建跟删除）

1. 创建**frm**（表结构定义文件）临时文件
2. **锁原表，不允许****DML****（数据操作语言），允许查询**
3. 根据聚集索引顺序构建新的索引项，按照顺序插入新的索引页
4. 原表升级锁，禁止读写，即原表暂停服务
5. rename操作，替换原表的frm文件

#### 早期`copy table ` VS `inplace` 方式？

inplace 方式相对于 copy 方式来说，inplace 不会生成临时表，不会发生数据拷贝，所以**减少了****I/O****资源占用**。

inplace 只适用于**索引的创建与删除**，不适用于其他类的DDL语句。

不论是早期copy还是早期inplace方式的DDL，都会进行**锁表操作，不允许****DML****操作，仅允许查询**。

知道了DDL的机制，下面就了解一下“如何对大表进行加索引操作”！

#### 方案一：“影子策略”

此方法来自《高性能MySQL》一书中的方案。

##### 方案思路

1. 创建一张与原表（tb）结构相同的新表（tb_new）
2. 在新表上创建索引
3. 重命名原表为其他表名（tb => tb_tmp），新表重命名为原表名（tb_new => tb），此时新表（tb）承担业务
4. 为原表（tb_tmp）新增索引
5. 交换表，新表改回最初的名称（tb => tb_new），原表改回最初的名称（tb_tmp => tb），原表（tb）重新承担业务
6. 把新表数据导入原表（即把新表承担业务期间产生的数据和到原表中）

“影子策略”有哪些问题？

步骤3之后，新表改为原表名后（tb）开始承担业务，步骤3到结束之前这段时间的新产生的数据都是存在新表中的，但是如果有业务对老数据进行修改或删除操作，那将无法实现，所以步骤3到结束这段时间可能会产生数据（更新和删除）丢失。

#### 方案二：pt-online-schema-change

PERCONA提供若干维护MySQL的小工具，其中 pt-online-schema-change（简称pt-osc）便可用来相对安全地对大表进行DDL操作。

pt-online-schema-change 方案利用三个触发器（DELETE\UPDATE\INSERT触发器）解决了“影子策略”存在的问题，让新老表数据同步时发生的数据变动也能得到同步。

##### 工作原理

1. 创建一张与原表结构相同的新表
2. 对新表进行DDL操作（如加索引）
3. 在原表上创建3个触发器（DELETE\UPDATE\INSERT），用来原表复制到新表时（步骤4）的数据改动时的同步
4. 将原表数据以数据块（chunk）的形式复制到新表
5. 表交换，原表重命名为old表，新表重命名原表名
6. 删除旧表，删除触发器

#### 方案三：ONLINE DDL

MySQL5.6.7 之前由于DDL实现机制的局限性，常用“影子策略”和 pt-online-schema-change 方案进行DDL操作，保证相对安全性。在 MySQL5.6.7 版本中新推出了 Online DDL 特性，支持“无锁”DDL。5.7版本已趋于成熟，所以在5.7之后可以直接利用 ONLINE DDL特性。

对于 ONLINE DDL 下的 inplace 方式，分为了 `rebuild table` 和 `no-rebuild table`。

##### Online DDL执行阶段

大致可分为三个阶段：初始化、执行和提交

##### Initialization阶段

此阶段会使用MDL读锁，禁止其他并发线程修改表结构

服务器将考虑存储引擎能力、语句中指定的操作以及用户指定的ALGORITHM 和 LOCK选项，确定操作期间允许的并发数

##### Execution阶段

此阶段分为两个步骤 Prepared and Executed

此阶段是否需要MDL写锁取决于Initialization阶段评估的因素，如果需要MDL写锁的话，仅在Prepared过程会短暂的使用MDL写锁

其中最耗时的是Excuted过程

##### Commit Table Definition阶段

此阶段会将MDL读锁升级到MDL写锁，此阶段一般较快，因此独占锁的时间也较短

用新的表定义替换旧的表定义(如果rebuild table)

##### ONLINE DDL 过程

1. 获取对应要操作表的 MDL（metadata lock）写锁
2. MDL写锁 降级成 MDL读锁
3. 真正做DDL操作
4. MDL读锁 升级成 MDL写锁
5. 释放MDL锁

在第3步时，DDL操作时是不会进行锁表的，可以进行DML操作。但可能在拿DML写锁时锁住，见文章[MySQL · 源码阅读 · 白话Online DDL](https://link.segmentfault.com/?enc=RsUfEIikE%2FMLIfYz70YkEQ%3D%3D.EYdijkE8rfS51ABc99RAjuc%2F90IXsss52SIVcCJsmYJiTGE9jy%2FiZGR2k9cgaaff)

## 深入了解mysql

### MYSQL执行流程是怎样的？

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ZDc1ZjIxMzRhOWZlNTE1N2MwOGE3MzFhMzhlNzViNmZfcnhkOWlYcUxGRkFRMHVmWHNmNktpTHFGcnpub3lWbTdfVG9rZW46Q21Md2JWMzJsb1dRVnd4cjlORmNmWDFmbk1jXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

 MySQL 的架构共分为两层：**Server 层和存储引擎层，**

- **Server 层负责建立连接、分析和执行** **SQL****。**MySQL 大多数的核心功能模块都在这实现，主要包括连接器，查询缓存、解析器、预处理器、优化器、执行器等。另外，所有的内置函数（如日期、时间、数学和加密函数等）和所有跨存储引擎的功能（如存储过程、触发器、视图等。）都在 Server 层实现。
- **存储引擎层负责数据的存储和提取。**支持 InnoDB、MyISAM、Memory 等多个存储引擎，不同的存储引擎共用一个 Server 层。现在最常用的存储引擎是 InnoDB，从 MySQL 5.5 版本开始， InnoDB 成为了 MySQL 的默认存储引擎。我们常说的索引数据结构，就是由存储引擎层实现的，不同的存储引擎支持的索引类型也不相同，比如 InnoDB 支持索引类型是 B+树 ，且是默认使用，也就是说在数据表中创建的主键索引和二级索引默认使用的是 B+ 树索引。

### 第一步：连接器

简单总结一下：

- 与客户端进行 TCP 三次握手建立连接；
- 校验客户端的用户名和密码，如果用户名或密码不对，则会报错；
- 如果用户名和密码都对了，会读取该用户的权限，然后后面的权限逻辑判断都基于此时读取到的权限；

### 第二步：查询缓存

连接器得工作完成后，客户端就可以向 MySQL 服务发送 SQL 语句了，MySQL 服务收到 SQL 语句后，就会解析出 SQL 语句的第一个字段，看看是什么类型的语句。

如果 SQL 是查询语句（select 语句），MySQL 就会先去查询缓存（ Query Cache ）里查找缓存数据，看看之前有没有执行过这一条命令，这个查询缓存是以 key-value 形式保存在内存中的，key 为 SQL 查询语句，value 为 SQL 语句查询的结果。

如果查询的语句命中查询缓存，那么就会直接返回 value 给客户端。如果查询的语句没有命中查询缓存中，那么就要往下继续执行，等执行完后，查询的结果就会被存入查询缓存中。

这么看，查询缓存还挺有用，但是其实**查询缓存**挺鸡肋的。

对于更新比较频繁的表，查询缓存的命中率很低的，因为只要一个表有更新操作，那么这个表的查询缓存就会被清空。如果刚缓存了一个查询结果很大的数据，还没被使用的时候，刚好这个表有更新操作，查询缓冲就被清空了，相当于缓存了个寂寞。

所以，MySQL 8.0 版本直接将查询缓存删掉了，也就是说 MySQL 8.0 开始，执行一条 SQL 查询语句，不会再走到查询缓存这个阶段了。

对于 MySQL 8.0 之前的版本，如果想关闭查询缓存，我们可以通过将参数 query_cache_type 设置成 DEMAND。

- TIP

  -   这里说的查询缓存是 server 层的，也就是 MySQL 8.0 版本移除的是 server 层的查询缓存，并不是 Innodb 存储引擎中的 buffer pool。

### 第三步：解析 SQL

在正式执行 SQL 查询语句之前， MySQL 会先对 SQL 语句做解析，这个工作交由「解析器」来完成。

#### 解析器

解析器会做如下两件事情。

第一件事情，词法分析。MySQL 会根据你输入的字符串识别出关键字出来，例如，SQL语句 select username from userinfo，在分析之后，会得到4个Token，其中有2个Keyword，分别为select和from：

暂时无法在飞书文档外展示此内容

第二件事情，语法分析。根据词法分析的结果，语法解析器会根据语法规则，判断你输入的这个 SQL 语句是否满足 MySQL 语法，如果没问题就会构建出 SQL 语法树，这样方便后面模块获取 SQL 类型、表名、字段名、 where 条件等等。

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NzZiOTEyMjIzMmI4NWE2OGVhOTUzNzM0Zjg3MDE0NzNfbUxXOXZVaUlrc29zcGRlSzNUQ2E0NE1pZTFMM2JVcnpfVG9rZW46VWdpWWIyZk9Ub1BRcEp4dlF1YmNVTThxbkpkXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

如果我们输入的 SQL 语句语法不对，就会在解析器这个阶段报错。比如，我下面这条查询语句，把 from 写成了 form，这时 MySQL 解析器就会给报错。

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ZmE0ZjUzMGUxNjFkNzY0MTg2OTljYjM4YzgxNzg5YzVfTldpbVdFMXRYeFJ4eEZ0TTBJMnEyTDVaZ0ZYcVJxeUhfVG9rZW46VEJYRWJlYVJUb1RZVUN4d0dyZ2NXUXJLbjhnXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

但是注意，表不存在或者字段不存在，并不是在解析器里做的，《MySQL 45 讲》说是在解析器做的，但是经过我和朋友看 MySQL 源码（5.7和8.0）得出结论是解析器只负责检查语法和构建语法树，但是不会去查表或者字段存不存在。

那到底谁来做检测表和字段是否存在的工作呢？别急，接下来就是了

### 第四步：执行 SQL

#### 预处理器

- 检查 SQL 查询语句中的表或者字段是否存在；
- 将 `select *` 中的 `*` 符号，扩展为表上的所有列

#### 优化器

**经过预处理阶段后，**还需要为 SQL 查询语句先制定一个执行计划，这个工作交由「优化器」来完成的。

优化器主要负责将 SQL 查询语句的执行方案确定下来，比如在表里面有多个索引的时候，优化器会基于查询成本的考虑，来决定选择使用哪个索引。

#### 执行器

经历完优化器后，就确定了执行方案，接下来 MySQL 就真正开始执行语句了，这个工作是由「执行器」完成的。在执行的过程中，执行器就会和存储引擎交互了，交互是以记录为单位的。

接下来，用三种方式执行过程，跟大家说一下执行器和存储引擎的交互过程（PS ：为了写好这一部分，特地去看 MySQL 源码，也是第一次看哈哈）。

- 主键索引查询
- 全表扫描
- 索引下推

#### [#](https://xiaolincoding.com/mysql/base/how_select.html#%E4%B8%BB%E9%94%AE%E7%B4%A2%E5%BC%95%E6%9F%A5%E8%AF%A2)主键索引查询

以本文开头查询语句为例，看看执行器是怎么工作的。

```
select * from product where id = 1;
```

这条查询语句的查询条件用到了主键索引，而且是等值查询，同时主键 id 是唯一，不会有 id 相同的记录，所以优化器决定选用访问类型为 const 进行查询，也就是使用主键索引查询一条记录，那么执行器与存储引擎的执行流程是这样的：

- 执行器第一次查询，会调用 read_first_record 函数指针指向的函数，因为优化器选择的访问类型为 const，这个函数指针被指向为 InnoDB 引擎索引查询的接口，把条件 `id = 1` 交给存储引擎，让存储引擎定位符合条件的第一条记录。
- 存储引擎通过主键索引的 B+ 树结构定位到 id = 1的第一条记录，如果记录是不存在的，就会向执行器上报记录找不到的错误，然后查询结束。如果记录是存在的，就会将记录返回给执行器；
- 执行器从存储引擎读到记录后，接着判断记录是否符合查询条件，如果符合则发送给客户端，如果不符合则跳过该记录。
- 执行器查询的过程是一个 while 循环，所以还会再查一次，但是这次因为不是第一次查询了，所以会调用 read_record 函数指针指向的函数，因为优化器选择的访问类型为 const，这个函数指针被指向为一个永远返回 - 1 的函数，所以当调用该函数的时候，执行器就退出循环，也就是结束查询了。

至此，这个语句就执行完成了。

#### [#](https://xiaolincoding.com/mysql/base/how_select.html#%E5%85%A8%E8%A1%A8%E6%89%AB%E6%8F%8F)全表扫描

举个全表扫描的例子：

```
select * from product where name = 'iphone';
```

这条查询语句的查询条件没有用到索引，所以优化器决定选用访问类型为 ALL 进行查询，也就是全表扫描的方式查询，那么这时执行器与存储引擎的执行流程是这样的：

- 执行器第一次查询，会调用 read_first_record 函数指针指向的函数，因为优化器选择的访问类型为 all，这个函数指针被指向为 InnoDB 引擎全扫描的接口，让存储引擎读取表中的第一条记录；
- 执行器会判断读到的这条记录的 name 是不是 iphone，如果不是则跳过；如果是则将记录发给客户的（是的没错，Server 层每从存储引擎读到一条记录就会发送给客户端，之所以客户端显示的时候是直接显示所有记录的，是因为客户端是等查询语句查询完成后，才会显示出所有的记录）。
- 执行器查询的过程是一个 while 循环，所以还会再查一次，会调用 read_record 函数指针指向的函数，因为优化器选择的访问类型为 all，read_record 函数指针指向的还是 InnoDB 引擎全扫描的接口，所以接着向存储引擎层要求继续读刚才那条记录的下一条记录，存储引擎把下一条记录取出后就将其返回给执行器（Server层），执行器继续判断条件，不符合查询条件即跳过该记录，否则发送到客户端；
- 一直重复上述过程，直到存储引擎把表中的所有记录读完，然后向执行器（Server层） 返回了读取完毕的信息；
- 执行器收到存储引擎报告的查询完毕的信息，退出循环，停止查询。

至此，这个语句就执行完成了。

#### [#](https://xiaolincoding.com/mysql/base/how_select.html#%E7%B4%A2%E5%BC%95%E4%B8%8B%E6%8E%A8)索引下推

在这部分非常适合讲索引下推（MySQL 5.6 推出的查询优化策略），这样大家能清楚的知道，「下推」这个动作，下推到了哪里。

索引下推能够减少二级索引在查询时的回表操作，提高查询的效率，因为它将 Server 层部分负责的事情，交给存储引擎层去处理了。

举一个具体的例子，方便大家理解，这里一张用户表如下，我对 age 和 reward 字段建立了联合索引（age，reward）：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=YzY3NmI0ZmI4OGFhZDNjOGZiZDQ0ODdlMzZlYTIxZWFfRU9YNXNDS1VNcG91Q3lNZE1oYzF5RlIyYnI1c1JRc01fVG9rZW46SGEzOWJlSVZnb0gwa294aVlHUWNWNFhlbmRoXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

现在有下面这条查询语句：

```
select * from t_user  where age  20 and reward = 100000;
```

联合索引当遇到范围查询 (>、<) 就会停止匹配，也就是 age 字段能用到联合索引，但是 reward 字段则无法利用到索引。具体原因这里可以看这篇：[索引常见面试题(opens new window)](https://xiaolincoding.com/mysql/index/index_interview.html#%E6%8C%89%E5%AD%97%E6%AE%B5%E4%B8%AA%E6%95%B0%E5%88%86%E7%B1%BB)

那么，不使用索引下推（MySQL 5.6 之前的版本）时，执行器与存储引擎的执行流程是这样的：

- Server 层首先调用存储引擎的接口定位到满足查询条件的第一条二级索引记录，也就是定位到 age > 20 的第一条记录；
- 存储引擎根据二级索引的 B+ 树快速定位到这条记录后，获取主键值，然后进行回表操作，将完整的记录返回给 Server 层；
- Server 层在判断该记录的 reward 是否等于 100000，如果成立则将其发送给客户端；否则跳过该记录；
- 接着，继续向存储引擎索要下一条记录，存储引擎在二级索引定位到记录后，获取主键值，然后回表操作，将完整的记录返回给 Server 层；
- 如此往复，直到存储引擎把表中的所有记录读完。

可以看到，没有索引下推的时候，每查询到一条二级索引记录，都要进行回表操作，然后将记录返回给 Server，接着 Server 再判断该记录的 reward 是否等于 100000。

而使用索引下推后，判断记录的 reward 是否等于 100000 的工作交给了存储引擎层，过程如下 ：

- Server 层首先调用存储引擎的接口定位到满足查询条件的第一条二级索引记录，也就是定位到 age > 20 的第一条记录；
- 存储引擎定位到二级索引后，先不执行回表操作，而是先判断一下该索引中包含的列（reward列）的条件（reward 是否等于 100000）是否成立。如果条件不成立，则直接跳过该二级索引。如果成立，则执行回表操作，将完成记录返回给 Server 层。
- Server 层在判断其他的查询条件（本次查询没有其他条件）是否成立，如果成立则将其发送给客户端；否则跳过该记录，然后向存储引擎索要下一条记录。
- 如此往复，直到存储引擎把表中的所有记录读完。

可以看到，使用了索引下推后，虽然 reward 列无法使用到联合索引，但是因为它包含在联合索引（age，reward）里，所以直接在存储引擎过滤出满足 reward = 100000 的记录后，才去执行回表操作获取整个记录。相比于没有使用索引下推，节省了很多回表操作。

当你发现执行计划里的 Extr 部分显示了 “Using index condition”，说明使用了索引下推。

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=Y2U1ZjFlMDQ1OTdlNzI4ZjVjZjAzZjIwYWEyYjkwNDZfalpYRWxCWWtnclJJRFhSVjgxRGd6enpEWVo2eXZaSUFfVG9rZW46RHlHZ2Jva3Byb3RpZVJ4emlhNGM4ejA4bjVlXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

------

总结

执行一条 SQL 查询语句，期间发生了什么？

- 连接器：建立连接，管理连接、校验用户身份；
- 查询缓存：查询语句如果命中查询缓存则直接返回，否则继续往下执行。MySQL 8.0 已删除该模块；
- 解析 SQL，通过解析器对 SQL 查询语句进行词法分析、语法分析，然后构建语法树，方便后续模块读取表名、字段、语句类型；
- 执行 SQL：执行 SQL 共有三个阶段：
  - 预处理阶段：检查表或字段是否存在；将 `select *` 中的 `*` 符号扩展为表上的所有列。
  - 优化阶段：基于查询成本的考虑， 选择查询成本最小的执行计划；
  - 执行阶段：根据执行计划执行 SQL 查询语句，从存储引擎读取记录，返回给客户端；

### MyISAM是怎么被InnoDB替代的

经过上述的一系列对比后，对于为何使用InnoDB替换了MyISAM引擎的原因，相信各位小伙伴也能感受出来，这里就等价于稍微做个总结：

①存储方式：MyISAM引擎会将表数据和索引数据分成两个文件存储。

②索引支持：因为MyISAM引擎的表数据和索引数据是分开的，因此不支持聚簇索引。

③事务支持：由于MyISAM引擎没有undo-log日志，所以不支持多条SQL组成事务并回滚。

④故障恢复：MyISAM引擎依靠bin-log日志实现，bin-log中未写入的数据会永久丢失。

⑤锁粒度支持：因为MyISAM不支持聚簇索引，因此无法实现行锁，所有并发操作只能加表锁。

⑥并发性能：MyISAM引擎仅支持表锁，所以多条线程出现读-写并发场景时会阻塞。

⑦内存利用度：MyISAM引擎过于依赖MySQL Server，对缓冲池、异步IO技术开发度不够。

上述这些MyISAM不支持的，InnoDB引擎全都支持，也正由于这方方面面的原因，InnoDB引擎开始崭露锋芒，而作为MySQL亲生子的MyISAM自此之后跌落神坛，最终到了MySQL5.6版本时，MyISAM彻底让出了MySQL默认存储引擎的宝座。

### Mysql 的一行记录是怎么被存储的

mysql中我们知道用页来存储数据，那其中的关系是怎样的呢？

首先对于每张数据库我们会创建一个目录，然后对于其中的每张表，我们知道数据是存在磁盘上的，存在一个后缀为表数据的存放文件，里面的存储结构分这么几层 segment段 区 page页 然后就是行 ，行是每条数据的最小单位

行的结构大致上有四种，每行大体上分为这么几个部分可变长字段的大小，null值存储，每行都有的字段（当前事务id，上一个版本链的指针），真实数据

#### MySQL 的数据存放在哪个文件？ 

大家都知道 MySQL 的数据都是保存在磁盘的，那具体是保存在哪个文件呢？

MySQL 存储的行为是由存储引擎实现的，MySQL 支持多种存储引擎，不同的存储引擎保存的文件自然也不同。

InnoDB 是我们常用的存储引擎，也是 MySQL 默认的存储引擎。所以，本文主要以 InnoDB 存储引擎展开讨论。

先来看看 MySQL 数据库的文件存放在哪个目录？

```
mysql SHOW VARIABLES LIKE 'datadir';+---------------+-----------------+| Variable_name | Value           |+---------------+-----------------+| datadir       | /var/lib/mysql/ |+---------------+-----------------+1 row in set (0.00 sec)
```

我们每创建一个 database（数据库） 都会在 /var/lib/mysql/ 目录里面创建一个以 database 为名的目录，然后保存表结构和表数据的文件都会存放在这个目录里。

比如，我这里有一个名为 my_test 的 database，该 database 里有一张名为 t_order 数据库表。

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=YjkwYWM0ZTdlMTE5YjRmMTFhYzY2YmZkZjVhZDRlN2JfUXNDcGhCbFJXRmpxNUwyYUhMbUVUZGFRV1dwYUZ1ZzdfVG9rZW46SmhWMWJxN29Vb1UwQTJ4dTJ5cmNmZ0Zrbk1nXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

然后，我们进入 /var/lib/mysql/my_test 目录，看看里面有什么文件？

```
[root@xiaolin ~]#ls /var/lib/mysql/my_test
db.opt
t_order.frm
t_order.ibd
```

可以看到，共有三个文件，这三个文件分别代表着：

- db.opt，用来存储当前数据库的默认字符集和字符校验规则。
- t_order.frm ，t_order 的表结构会保存在这个文件。在 MySQL 中建立一张表都会生成一个.frm 文件，该文件是用来保存每个表的元数据信息的，主要包含表结构定义。
- t_order.ibd，t_order 的表数据会保存在这个文件。表数据既可以存在共享表空间文件（文件名：ibdata1）里，也可以存放在独占表空间文件（文件名：表名字.ibd）。这个行为是由参数 innodb_file_per_table 控制的，若设置了参数 innodb_file_per_table 为 1，则会将存储的数据、索引等信息单独存储在一个独占表空间，从 MySQL 5.6.6 版本开始，它的默认值就是 1 了，因此从这个版本之后， MySQL 中每一张表的数据都存放在一个独立的 .ibd 文件。

好了，现在我们知道了一张数据库表的数据是保存在「 表名字.ibd 」的文件里的，这个文件也称为独占表空间文件。

### 表空间文件的结构是怎么样的？

表空间由段（segment）、区（extent）、页（page）、行（row）组成，InnoDB存储引擎的逻辑存储结构大致如下图：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=Yjg0YmM1N2Y5ZTRmNGE4ZGE4NzI0OTdjMDczOWVkMmJfTEg3ekVSSW85Mzc2TVJnMk5udW9aQmdtaUNxZ3M5MkVfVG9rZW46VU9wRWJpQVZRb3JrOEN4cmJwbGNYQ0ZhbmZlXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

下面我们从下往上一个个看看。

#### [#](https://xiaolincoding.com/mysql/base/row_format.html#_1%E3%80%81%E8%A1%8C-row)1、行（row）

数据库表中的记录都是按行（row）进行存放的，每行记录根据不同的行格式，有不同的存储结构。

后面我们详细介绍 InnoDB 存储引擎的行格式，也是本文重点介绍的内容。

#### [#](https://xiaolincoding.com/mysql/base/row_format.html#_2%E3%80%81%E9%A1%B5-page)2、页（page）

记录是按照行来存储的，但是数据库的读取并不以「行」为单位，否则一次读取（也就是一次 I/O 操作）只能处理一行数据，效率会非常低。

因此，InnoDB 的数据是按「页」为单位来读写的，也就是说，当需要读一条记录的时候，并不是将这个行记录从磁盘读出来，而是以页为单位，将其整体读入内存。

默认每个页的大小为 16KB，也就是最多能保证 16KB 的连续存储空间。

页是 InnoDB 存储引擎磁盘管理的最小单元，意味着数据库每次读写都是以 16KB 为单位的，一次最少从磁盘中读取 16K 的内容到内存中，一次最少把内存中的 16K 内容刷新到磁盘中。

页的类型有很多，常见的有数据页、undo 日志页、溢出页等等。数据表中的行记录是用「数据页」来管理的，数据页的结构这里我就不讲细说了，之前文章有说过，感兴趣的可以去看这篇文章：[换一个角度看 B+ 树(opens new window)](https://xiaolincoding.com/mysql/index/page.html)

总之知道表中的记录存储在「数据页」里面就行。

#### [#](https://xiaolincoding.com/mysql/base/row_format.html#_3%E3%80%81%E5%8C%BA-extent)3、区（extent）

我们知道 InnoDB 存储引擎是用 B+ 树来组织数据的。

B+ 树中每一层都是通过双向链表连接起来的，如果是以页为单位来分配存储空间，那么链表中相邻的两个页之间的物理位置并不是连续的，可能离得非常远，那么磁盘查询时就会有大量的随机I/O，随机 I/O 是非常慢的。

解决这个问题也很简单，就是让链表中相邻的页的物理位置也相邻，这样就可以使用顺序 I/O 了，那么在范围查询（扫描叶子节点）的时候性能就会很高。

那具体怎么解决呢？

在表中数据量大的时候，为某个索引分配空间的时候就不再按照页为单位分配了，而是按照区（extent）为单位分配。每个区的大小为 1MB，对于 16KB 的页来说，连续的 64 个页会被划为一个区，这样就使得链表中相邻的页的物理位置也相邻，就能使用顺序 I/O 了。

#### [#](https://xiaolincoding.com/mysql/base/row_format.html#_4%E3%80%81%E6%AE%B5-segment)4、段（segment）

表空间是由各个段（segment）组成的，段是由多个区（extent）组成的。段一般分为数据段、索引段和回滚段等。

- 索引段：存放 B + 树的非叶子节点的区的集合；
- 数据段：存放 B + 树的叶子节点的区的集合；
- 回滚段：存放的是回滚数据的区的集合，之前讲[事务隔离 (opens new window)](https://xiaolincoding.com/mysql/transaction/mvcc.html)的时候就介绍到了 MVCC 利用了回滚段实现了多版本查询数据。

好了，终于说完表空间的结构了。接下来，就具体讲一下 InnoDB 的行格式了。

之所以要绕一大圈才讲行记录的格式，主要是想让大家知道行记录是存储在哪个文件，以及行记录在这个表空间文件中的哪个区域，有一个从上往下切入的视角，这样理解起来不会觉得很抽象。

### InnoDB 行格式有哪些？

行格式（row_format），就是一条记录的存储结构。

InnoDB 提供了 4 种行格式，分别是 Redundant、Compact、Dynamic和 Compressed 行格式。

- Redundant 是很古老的行格式了， MySQL 5.0 版本之前用的行格式，现在基本没人用了。
- 由于 Redundant 不是一种紧凑的行格式，所以 MySQL 5.0 之后引入了 Compact 行记录存储方式，Compact 是一种紧凑的行格式，设计的初衷就是为了让一个数据页中可以存放更多的行记录，从 MySQL 5.1 版本之后，行格式默认设置成 Compact。
- Dynamic 和 Compressed 两个都是紧凑的行格式，它们的行格式都和 Compact 差不多，因为都是基于 Compact 改进一点东西。从 MySQL5.7 版本之后，默认使用 Dynamic 行格式。

Redundant 行格式我这里就不讲了，因为现在基本没人用了，这次重点介绍 Compact 行格式，因为 Dynamic 和 Compressed 这两个行格式跟 Compact 非常像。

### COMPACT 行格式长什么样？

先跟 Compact 行格式混个脸熟，它长这样：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=YWM1YzY1MjMwZmI3YmRhOTY3NDVhNDQxNTllYmM2M2FfeHZ2YTFkVmFFVlU0TW0yTTVMa3FrWm81bHNCSFJEU29fVG9rZW46WEFrYmJ1RGN5b1VwcG14OTdsYWNSa2kzbkFnXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

可以看到，一条完整的记录分为「记录的额外信息」和「记录的真实数据」两个部分。

接下里，分别详细说下。

### [#](https://xiaolincoding.com/mysql/base/row_format.html#%E8%AE%B0%E5%BD%95%E7%9A%84%E9%A2%9D%E5%A4%96%E4%BF%A1%E6%81%AF)记录的额外信息

记录的额外信息包含 3 个部分：变长字段长度列表、NULL 值列表、记录头信息。

#### [#](https://xiaolincoding.com/mysql/base/row_format.html#_1-%E5%8F%98%E9%95%BF%E5%AD%97%E6%AE%B5%E9%95%BF%E5%BA%A6%E5%88%97%E8%A1%A8)1. 变长字段长度列表

char定长，varchar变长，变长字段实际存储的数据的长度（大小）不固定的。

现在 t_user 表里有这三条记录：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MTkxOTQ5OWQ2N2RmMTQ3OWZkMWMxMDViYjE3NWQ1YzdfVjVwbEpJZk5EZG1uaDVQYnRYWUNSN2VQekVER1hpZGFfVG9rZW46UUxtTGJmYU0xb3QxamR4RFJDWWNEZGNubnZiXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

接下来，我们看看看看这三条记录的行格式中的 「变长字段长度列表」是怎样存储的。

先来看第一条记录：

- name 列的值为 a，真实数据占用的字节数是 1 字节，十六进制 0x01；
- phone 列的值为 123，真实数据占用的字节数是 3 字节，十六进制 0x03；
- age 列和 id 列不是变长字段，所以这里不用管。

其余的直接点击链接看页面把

## mysql中的锁

暂时无法在飞书文档外展示此内容

暂时无法在飞书文档外展示此内容

在 MySQL 里，根据加锁的范围，可以分为全局锁、表级锁和行锁三类。

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NmM3Y2M3YzQzMTkwZmMzZGEwOWMyN2ZlYjc3YTEzZTlfV1YwYVRQTWdud2l1VUFuYmNyWmZVSzFKY1VEYllWNTJfVG9rZW46U210VGIxbk9xb2M5a2h4eFZXZGNoaGFobm9kXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

### [#](https://xiaolincoding.com/mysql/lock/mysql_lock.html#%E5%85%A8%E5%B1%80%E9%94%81) 全局锁

开启全局锁之后，整个数据库就进入只读的状态，其他线程进行增删改或者表结构更改的语句都会被阻塞，

要使用全局锁，则要执行这条命令：

```
flush tables with read lock
```

执行后，整个数据库就处于**只读**状态了，这时其他线程执行以下操作，都会被阻塞：

- 对数据的增删改操作，比如 insert、delete、update等语句；
- 对表结构的更改操作，比如 alter table、drop table 等语句。

如果要释放全局锁，则要执行这条命令：

```
unlock tables
```

全局锁主要用于做全库逻辑备份，如果不开启全局锁进行数据备份，会造成问题，备份的同时有事务来更改数据。

### 表级锁

#### 表锁：

表锁是针对整张表加的锁

对学生表（t_student）加表锁，可以使用下面的命令：

```
--表级别的共享锁，也就是读锁；
lock tables t_student read;

--表级别的独占锁，也就是写锁；
lock tables t_stuent write;
```

需要注意的是，表锁除了会限制别的线程的读写外，也会限制本线程接下来的读写操作。

也就是说**如果本线程对学生表加了「共享表锁」**，那么本线程接下来如果要对学生表执行写操作的语句，是会被阻塞的，当然**其他线程对学生表进行写操作时也会被阻塞**，直到锁被释放。

要释放表锁，可以使用下面这条命令，会释放当前会话的所有表锁：

```
unlock tables
```

另外，当会话退出后，也会释放所有表锁。

不过尽量避免在使用 InnoDB 引擎的表使用表锁，因为**表锁的颗粒度太大，会影响并发性能**，InnoDB 牛逼的地方在于实现了颗粒度更细的行级锁。

#### 元数据锁（MDL）：

不需要显示的使用 MDL，因为当我们对数据库表进行操作时，会**自动**给这个表加上 MDL：

- 对一张表进行 CRUD 操作时，**加的是 MDL** **读锁****；**
- 对一张表做结构变更操作的时候，**加的是 MDL** **写锁****；**

MDL 是为了保证当用户对表执行 CRUD 操作时，防止其他线程对这个**表结构做了变更。** 当有线程在执行 **select 语句（ 加 MDL** **读锁****）**的期间，如果有其他线程要更改该表的结构**（ 申请 MDL** **写锁**），那么将会被阻塞，直到执行完 select 语句（ 释放 MDL 读锁）。 反之，当有线程对表结构进行变更（ 加 MDL 写锁）的期间，如果有其他线程执行了 CRUD 操作（ 申请 MDL 读锁），那么就会被阻塞，直到表结构变更完成（ 释放 MDL 写锁）。

 -MDL 不需要显示调用，那它是在什么时候释放的? MDL 是在事务提交后才会释放，**这意味着事务执行期间，MDL 是一直持有的。** 那如果数据库有一个长事务（所谓**的长事务，就是开启了事务，但是一直还没提交**），那在对表结构做变更操作的时候，可能会发生意想不到的事情，比如下面这个顺序的场景：

1. 首先，线程 A 先启用了事务（但是一直不提交），然后执行一条 select 语句，此时就先对该表加上 MDL 读锁；
2. 然后，线程 B 也执行了同样的 select 语句，此时并不会阻塞，因为「读读」并不冲突；
3. 接着，线程 C 修改了表字段，此时由于线程 A 的事务并没有提交，也就是 MDL 读锁还在占用着，这时线程 C 就无法申请到 MDL 写锁，就会被阻塞，

那么在线程 C 阻塞后，后续有对该表的 select 语句，就都会被阻塞，如果此时有大量该表的 select 语句的请求到来，就会有大量的线程被阻塞住，这时数据库的线程很快就会爆满了。 为什么线程 C 因为申请不到 MDL 写锁，而导致后续的申请读锁的查询操作也会被阻塞？ 这是因为申请 MDL 锁的操作会形成一个队列，**队列中写锁获取优先级高于读锁**，一旦出现 MDL 写锁等待，会阻塞后续该表的所有 CRUD 操作。 所以为了能安全的对表结构进行变更，在对表结构变更前，先要看看数据库中的长事务，是否有事务已经对表加上了 MDL 读锁，如果可以考虑 kill 掉这个长事务，然后再做表结构的变更。

，再进行表结构修改，同样的，后面的crud事务也要进行等待

#### 意向锁：

- 在使用 InnoDB 引擎的表里对某些记录加上**「****共享锁****」之前**，需要先在表级别加上一个**「意向共享锁」**；
- 在使用 InnoDB 引擎的表里对某些纪录加上**「独占锁」之前**，需要先在表级别加上一个**「意向独占锁」**；

也就是，当执行**插入、更新、删除**操作，需要先对表加上「**意向独占锁**」，然后对该记录加**独占锁**。 而普通的 select 是不会加行级锁的，普通的 select 语句是利用 MVCC 实现一致性读，是无锁的。 不过，select 也是可以对记录加共享锁和独占锁的，具体方式如下：

```
//先在表上加上意向共享锁，然后对读取的记录加共享锁
select ... lock in share mode;

//先表上加上意向独占锁，然后对读取的记录加独占锁
select ... for update;
```

**意向****共享锁****和意向独占锁是表级锁，不会和行级的共享锁和独占锁发生冲突，而且****意向锁****之间也不会发生冲突，只会和共享表锁（****\*lock tables ... read*****）和独占表锁（****\*lock tables ... write*****）发生冲突**。

表锁和行锁是满足**读读共享、读写****互斥****、写写互斥**的。 如果没有「意向锁」，那么加「独占表锁」时，就需要遍历表里所有记录，查看是否有记录存在独占锁，这样效率会很慢。 那么有了「意向锁」，由于在对记录加独占锁前，先会加上表级别的意向独占锁，那么在加「独占表锁」时，直接查该表是否有意向独占锁，如果有就意味着表里已经有记录被加了独占锁，这样就不用去遍历表里的记录。 所以，**意向锁的目的是为了快速判断表里是否有记录被加锁**。就是说表锁需要判断是否有事务在操作表，直接看有无意向锁就知道了，有意向锁就不用遍历表记录了。

#### AUTO-INC 锁

表里的主键通常都会设置成自增的，这是通过对主键字段声明 `AUTO_INCREMENT` 属性实现的。

之后可以在插入数据时，可以不指定主键的值，数据库会自动给主键赋值递增的值，这主要是通过 **AUTO-INC 锁**实现的。

**AUTO-INC 锁是特殊的表锁机制，锁不是再一个事务提交后才释放，而是再执行完插入语句后就会立即释放。**

**在插入数据时，会加一个表级别的 AUTO-INC 锁**，然后为被 `AUTO_INCREMENT` 修饰的字段赋值递增的值，等插入语句执行完成后，才会把 AUTO-INC 锁释放掉。

**意向锁****（表级锁）之间是共享的，IS表锁与****IX****锁之间都共享（映照前边那句话），IS表锁与其他表级****S锁****（共享表锁）共享，表级X锁（表级独占锁（独占表锁））****互斥****，而IX与其他任意表级锁互斥（S,X）而意向锁与行锁之间是不互斥的**

### 行级锁

InnoDB 引擎是支持行级锁的，而 MyISAM 引擎并不支持行级锁。

前面也提到，**普通的 select 语句是不会对记录加锁**的，因为它属于**快照读**。如果要在查询时对记录加行锁，可以使用下面这两个方式，这种查询会加锁的语句**称为锁定读**。

```
//对读取的记录加共享锁
select ... lock in share mode;
//对读取的记录加独占锁
select ... for update;
```

上面这两条语句必须在一个事务中，**因为当事务提交了，锁就会被释放**，所以在使用这两条语句的时候，要加上 begin、start transaction 或者 set autocommit = 0。

共享锁（S锁）满足读读共享，读写互斥。独占锁（X锁）满足写写互斥、读写互斥。

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NWVkMzI0MzZlYzBhZmZhMzcyZDU1OGMwY2FhZWI5OWRfNmt4MEd1YlRHN0t5cE5ONGVSTmdIZmtmcWxuclhncmhfVG9rZW46WWt3c2JFOFRYbzRmQVl4Y2tvNWNuc3QwbnFkXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

行级锁的类型主要有三类：

- Record Lock，记录锁，也就是仅仅把一条记录锁上；
- Gap Lock，间隙锁，锁定一个范围，但是不包含记录本身；
- Next-Key Lock：Record Lock + Gap Lock 的组合，锁定一个范围，并且锁定记录本身。

#### [#](https://xiaolincoding.com/mysql/lock/mysql_lock.html#record-lock)Record Lock

Record Lock 称为记录锁，锁住的是**一条记录**。而且记录锁是有 **S 锁和 X 锁**之分的：

- 当**一个事务对一条记录加了 S 型记录锁**后，**其他事务也可以继续对该记录加 S 型记录锁**（S 型与 S 锁兼容），但是不可以对该记录加 X 型记录锁（S 型与 X 锁不兼容）;
- 当一个事务对一条记录加了 X 型记录锁后，其他事务既不可以对该记录加 S 型记录锁（S 型与 X 锁不兼容），也不可以对该记录加 X 型记录锁（X 型与 X 锁不兼容）。

举个例子，当一个事务执行了下面这条语句：

```
mysql  begin;
mysql  select * from t_test where id = 1 for update;
```

就是对 t_test 表中主键 id 为 1 的这条记录加上 X 型的记录锁，这样其他事务就无法对这条记录进行修改了。

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=OTJlZDRmNmFiYjM2OGQ0NDdiMzRmYWNhYTc5ODEyMzlfVFlLWjZpUElnWG5CdW12bk12T3czYm5xeEIxOUVvc3JfVG9rZW46RXJSVGJKZkRob3MxbDZ4a0g5ZWNIdXdLbjFjXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

当**事务执行 commit 后，**事务过程中生成的**锁都会被释放。**

#### Gap Lock

Gap Lock 称为**间隙锁**，只存在于**可重复读隔离级别**，目的是为了解决可**重复读****隔离级别下****幻读**的现象。

假设，表中有一个范围 id 为（3，5）间隙锁，那么其他事务就无法插入 id = 4 这条记录了，这样就有效的防止幻读现象的发生。

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MWU0MmM0ZTM2YzhlMGM1YjI2YmYzMDU4NjRkOGRjMTlfNE9wTm5LclpwWm0xWmVqMEN2SWNrcHdwWlVDU2pwQWdfVG9rZW46R3htR2JtbnlGb1FmNFV4N3RJeWNkU2hxbnhjXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

间隙锁虽然存在 X 型间隙锁和 S 型间隙锁，但是并没有什么区别**，间隙锁之间是兼容的，即两个事务可以同时持有包含共同间隙范围的间隙锁，并不存在****互斥****关系，因为间隙锁的目的是防止插入幻影记录而提出的**。

#### Next-Key Lock

Next-Key Lock 称为**临键锁**，是 **Record Lock + Gap Lock** 的组合，锁定一个范围，并且锁定记录本身。

假设，表中有一个范围 id 为（3，5] 的 next-key lock，那么其他事务即不能插入 id = 4 记录，也不能修改 id = 5 这条记录。

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ODA2ZGJiNDRmZmI1OWNmNjBmZWViYjkzZmEzYjE4OGZfNWloQVFRcDBIUFEzdlpaeTRkN2ZWV2t2VWxjbU53SzJfVG9rZW46Vjd4eWIyYnJhb2JhYkh4ZFVSbGMzVWdRblZkXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

所以，next-key lock 即能保护该记录，又能阻止其他事务将新纪录插入到被保护记录前面的间隙中。

n**ext-key lock 是包含间隙锁+记录锁的，如果一个事务获取了 X 型的 next-key lock，那么另外一个事务在获取相同范围的 X 型的 next-key lock 时，是会被阻塞的。**

比如，一个事务持有了范围为 (1, 10] 的 X 型的 next-key lock，那么另外一个事务在获取相同范围的 X 型的 next-key lock 时，就会被阻塞。

虽然**相同范围的间隙锁是多个事务相互兼容的**，但**对于记录锁，我们是要考虑 X 型与 S 型关系**，X 型的记录锁与 X 型的记录锁是冲突的

#### 插入意向锁

一个事务在**插入一条记录**的时 候，**需要判断插入位置是否已被其他事务加了间隙锁**（next-key lock 也包含间隙锁）。

如果有的话，**插入操作就会发生阻塞，直到拥有间隙锁的那个事务提交为止（释放间隙锁的时刻）**，在此期间会生成一个**插入****意向锁****，**表明有事务想在某个区间插入新记录，但是现在处于等待状态。

举个例子，假设事务 A 已经对表加了一个范围 id 为（3，5）间隙锁。

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ZGZiNjc3NDQ1ODAzMjBmNWZlNWMwNTcxMDBiZGVlNDlfTWtUR2d0OGs4ODY2ajhCTlZkWnB4SGJaRjR6WkpiOEVfVG9rZW46VjN1M2JTdm9tb2dyQ1J4TUVHc2NRcG5obkNjXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

当事务 A 还没提交的时候，事务 B 向该表插入一条 id = 4 的新记录，这时会判断到插入的位置已经被事务 A 加了间隙锁，于是事物 B 会生成一个插入意向锁，然后将锁的状态设置为等待状态**（****\*PS：*****\*MySQL*** **\*加锁时，是先生成锁结构，然后设置锁的状态，如果锁状态是等待状态，并不是意味着事务成功获取到了锁，只有当锁状态为正常状态时，才代表事务成功获取到了锁*****）**，此时事务 B 就会发生阻塞，直到事务 A 提交了事务。

**插入****意向锁****名字虽然有意向锁，但是它并不是意向锁，它是一种特殊的间隙锁，属于行级别锁。**

如果说间隙锁锁住的是一个区间，那么「插入意向锁」锁住的就是一个点。因而从这个角度来说，插入意向锁确实是一种特殊的间隙锁。

插入意向锁与间隙锁的另一个非常重要的差别是：尽管「插入意向锁」也属于间隙锁，但两个事务却不能在同一时间内，一个拥有间隙锁，另一个拥有该间隙区间内的插入意向锁（当然，插入意向锁如果不在间隙锁区间内则是可以的）。

### 死锁：

死锁是指两个或两个以上的进程在执行过程中,因**争夺资源而造成的一种互相等待的现象**,若无外力作用，它们都将无法推进下去.此时称系统处于死锁状态或系统产生了死锁，这些永远**在互相等待的进程称为死锁进程**。表级锁不会产生死锁.所以解决死锁主要还是针对于最常用的InnoDB。

**死锁****的关键在于**：两个(或以上)的Session加锁的顺序不一致。

那么对应的解决死锁问题的关键就是：让不同的session加锁有次序

#### 如何避免死锁？

死锁的四个必要条件：**互斥****、占有且等待、不可强占用、循环等待**。发生死锁，这些条件必然成立，但是只要破坏任意一个条件就死锁就不会成立。

1. 互斥条件（Mutual Exclusion）： 一个资源每次只能被一个进程使用。如果进程P1获得了某资源R，那么其他进程就不能再获得该资源，只能等待P1释放。
2. 占有且等待条件（Hold and Wait）： 进程可以占有一些资源并等待其他进程释放它们所占有的资源。这就意味着即使一个进程已经占有了一些资源，但它可以请求其他资源，并在等待时继续占有已分配给它的资源。
3. 不可抢占条件（No Preemption）： 进程不能强行从其他进程中抢占资源，只能在进程自愿释放时才能获取资源。
4. 循环等待条件（Circular Wait）： 存在一个进程等待序列{P1, P2, ..., Pn}，其中P1等待P2所占有的资源，P2等待P3所占有的资源，...，Pn等待P1所占有的资源，形成一个循环等待的环路。

想象两个独臂小人拿着苹果，想要交换，苹果只能被一个人拿着，已经拿着苹果并等待别人释放苹果，。。。。

在数据库层面，有两种策略通过「**打破循环等待条件**」来解除死锁状态：

- **设置事务等待锁的超时时间**。当一个事务的等待时间超过该值后，就对这个事务进行回滚，于是锁就释放了，另一个事务就可以继续执行了。在 InnoDB 中，参数 `innodb_lock_wait_timeout` 是用来设置超时时间的，默认值时 50 秒。
- 当发生超时后，就出现下面这个提示：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ZjUwN2NkOTlhOGYzYzljOWMzZTQ1ZTlmNWIwYThlZWNfeFBuQ2FnYWVycWI4ekpFb0JVc1VtWEF0c0g0cUN3bnlfVG9rZW46Vjd6N2IzOE00b3p2RXV4S2lqN2NFd2F0blhjXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

- **开启主动死锁检测。**主动死锁检测在发现死锁后，主动回滚死锁链条中的某一个事务，让其他事务得以继续执行。将参数 `innodb_deadlock_detect` 设置为 on，表示开启这个逻辑，默认就开启。
- 当检测到死锁后，就会出现下面这个提示：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NmNlNGQ1NDMyZWUxMWQxMDljMjViMjRkZTk2ZDFjZGFfaW5OYzJCaTdua3YzNTRaYzVCUVNURFM5Q2JHaWlsaE9fVG9rZW46Vm5NeGJJcVZlb2hscjB4UnM2ZWN4Y2hSbk5mXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

上面这个两种策略是「当有死锁发生时」的避免方式。

### 补充：

在行锁里，两个间隙锁（gap lock）是不冲突的，记录锁LOCK_MODE: X,REC_NOT_GAP有SX冲突，next key lock也会冲突，但是对于（n，正无穷]这样的两个next key lock不冲突，要看情况

## mysql是怎么加锁的

**不同隔离级别下，行级锁的种类是不同的。**

在**读已提交隔离级别**下，行级锁的种类只有**记录锁**，也就是仅仅把一条记录锁上。

在可重复读隔离级别下，行级锁的种类除了有记录锁，还有间隙锁（目的是为了避免幻读），所以行级锁的种类主要有三类：

- Record Lock，记录锁，也就是仅仅把一条记录锁上；
- Gap Lock，间隙锁，锁定一个范围，但是不包含记录本身；
- Next-Key Lock：Record Lock + Gap Lock 的组合，锁定一个范围，并且锁定记录本身

行级锁加锁规则比较复杂，不同的场景，加锁的形式是不同的。

**加锁的对象是索引，加锁的基本单位是 next-key lock，**它是由**记录锁和间隙锁组合**而成的，**next-key lock 是前开后闭区间，而间隙锁是前开后开区间。**

但是，next-key lock 在一些场景下会**退化成记录锁或间隙锁**。

那到底是什么场景呢？总结一句，在能使用**记录锁或者间隙锁就能避免****幻读****现象的场景下**， **next-key lock 就会退化成记录锁或间隙锁。**

### 唯一索引等值查询（细）

当我们用唯一索引进行等值查询的时候，查询的记录存不存在，加锁的规则也会不同：

- 当查询的记录是「存在」的，在索引树上定位到这一条记录后，将该记录的索引中的 next-key lock 会退化成「记录锁」。
- 当查询的记录是「不存在」的，在索引树找到第一条大于该查询记录的记录后，将该记录的索引中的 next-key lock 会退化成「间隙锁」

**为什么唯一索引等值查询并且查询记录存在的场景下，该记录的索引中的 next-key lock 会退化成记录锁？**

原因就是在唯一索引等值查询并且查询记录存在的场景下，**仅靠记录锁也能避免****幻读**的问题。

幻读的定义就是，当一个事务前后两次查询的结果集，不相同时，就认为发生幻读。所以，要避免幻读就是避免结果集某一条记录被其他事务删除，或者有其他事务插入了一条新记录，这样前后两次查询的结果集就不会出现不相同的情况。

- 由于主键具有唯一性，**所以其他事务插入 id = 1 的时候，会因为主键冲突，导致无法插入 id = 1 的新记录**。这样事务 A 在多次查询 id = 1 的记录的时候，不会出现前后两次查询的结果集不同，也就避免了幻读的问题。
- **由于对 id = 1 加了记录锁，其他事务无法删除该记录**，这样事务 A 在多次查询 id = 1 的记录的时候，不会出现前后两次查询的结果集不同，也就避免了幻读的问题。

### 唯一索引范围查询（细）

范围查询和等值查询的加锁规则是不同的。

当唯一索引进行范围查询时，会对每一个扫描到的索引加 next-key 锁，然后如果遇到下面这些情况，会退化成记录锁或者间隙锁：

- 情况一：针对「大于等于」的范围查询，因为存在等值查询的条件，那么如果等值查询的记录是存在于表中，那么该记录的索引中的 next-key 锁会退化成记录锁。

- 情况二：针对「小于或者小于等于」的范围查询，要看条件值的记录是否存在于表中：

  - 当条件值的记录不在表中，那么不管是「小于」还是「小于等于」条件的范围查询，扫描到终止范围查询的记录时，该记录的索引的 next-key 锁会退化成间隙锁，其他扫描到的记录，都是在这些记录的索引上加 next-key 锁。

  - 当条件值的记录在表中，如果是「小于」条件的范围查询，扫描到终止范围查询的记录时，该记录的索引的 next-key 锁会退化成间隙锁，其他扫描到的记录，都是在这些记录的索引上加 next-key 锁；如果「小于等于」条件的范围查询，扫描到终止范围查询的记录时，该记录的索引 next-key 锁不会退化成间隙锁。其他扫描到的记录，都是在这些记录的索引上加 next-key 锁。

  -   我这里总结下， MySQL 行级锁的加锁规则。

  - ###   唯一索引等值查询：

  - 当查询的记录是「存在」的，在索引树上定位到这一条记录后，将该记录的索引中的 next-key lock 会**退化成「记录锁」。**

  - 当查询的记录是「不存在」的，在索引树找到第一条大于该查询记录的记录后，将该记录的索引中的 next-key lock 会**退化成「间隙锁」。**

### 非唯一索引等值查询：

- 当查询的记录「存在」时，由于不是唯一索引，所以肯定存在索引值相同的记录，于是非唯一索引等值查询的过程是一个扫描的过程，直到扫描到第一个不符合条件的二级索引记录就停止扫描，**然后在扫描的过程中，对扫描到的二级索引记录加的是 next-key 锁，而对于第一个不符合条件的二级索引记录，该二级索引的 next-key 锁会退化成间隙锁。同时，在符合查询条件的记录的****主键****索引上加记录锁。**
- 当查询的记录「不存在」时，扫描到第一条不符合条件的二级索引记录，该二级索引的 next-key 锁会退化成间隙锁。因为不存在满足查询条件的记录，所以不会对主键索引加锁。

### 非唯一索引和主键索引的范围查询的加锁规则不同之处在于：

- 唯一索引在满足一些条件的时候，索引的 next-key lock 退化为间隙锁或者记录锁。
- 非唯一索引范围查询，索引的 next-key lock 不会退化为间隙锁和记录锁。

其实理解 MySQL 为什么要这样加锁，主要要以避免幻读角度去分析，这样就很容易理解这些加锁的规则了。

还有一件很重要的事情，在线上在执行 update、delete、select ... for update 等具有加锁性质的语句，一定要检查语句是否走了索引，如果是全表扫描的话，会对每一个索引加 next-key 锁，相当于把整个表锁住了，这是挺严重的问题。

最后附上「@一只小铭」同学总结的流程图。

唯一索引（主键索引）加锁的流程图如下。（*注意这个流程图是针对「主键索引」的，如果是二级索引的唯一索引，除了流程图中对二级索引的加锁规则之外，还会对查询到的记录的主键索引项加「记录锁」，流程图没有提示这一个点，所以在这里用文字补充说明下*）

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MzY5ZGQ4YzhiYWZhZjdjNGFjNTdjNDU0ZWY1NDhiOTNfNENvdFA4VEtwbzl0Z3lLRDM2aHdEeXZwa1Y3RjdzOUxfVG9rZW46Rk1TVWJZMEh6b0VsVG14WVUyV2NDR1NwbmdoXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

非唯一索引加锁的流程图：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ODUwOTQ3OWEwNWVlNTY3ZTU5Zjg3NDA2YTljZTNjM2NfQ2djd2pla2VPSnFVUnNEZlFUZkN6UXp2ZjBEU21Ud2JfVG9rZW46R21WbGJsNndIb2RVZjJ4ZFFzbWM5Z2tobm5jXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

## mysql调优：

### 架构层面：

1. 读写分离（主服务器做写操作，增删改。从服务器做读操作）
2. 主从配置，从服务器可以为主服务器分担压力，保证数据的可用性，容错性。
3. 分库分表 解决单表数据过大的问题，就比如我们单库的数据量上千万条过大了，可以考虑将数据库分成几个独立的子库，承担单库压力过大的问题，分库可以按业务模块，地理位置，用户需求这样考虑。分表的话，水平将表划分为多个子表，按照范围、按照哈希、按照分片键。
4. 缓存 双写一致性
5. java数据结构：比如说将查询的数据作为一组存储到list或者map这种java数据结构里，后面用到可以从这里面去取，再考虑去mysql查询

### 硬件层面：

1. mysql内存大一些，buffer pool的占比是mysql内存的75%-80 左右
2. cpu升级 几核
3. mysql版本 一些优化改动比较大的版本，效率也会高 5.7 5.8 8.0 比如8.0对order by做了一个比较不错的优化，或者当时我记得朋友公司有个运维，说两个一样的表结构，一样的查询，有一个就是慢，最后发现mysql版本不一样
4. 连接池的最大连接数参数设置合理点，太大了造成线程上下文来回切换 参照数 cpu核心*2 是固态硬盘+1

### SQL语句：

1. **Select \*** 避免使用，**用哪个**字段，就**查哪个**字段就可以了 比单字段快个两倍多的速度，用40条数据做测试 *0.015 age单字段0.006
2. **避免冗余多次的使用相同的查询**，比如这里要用到这**个结果集，然后那里也要用到**，或者说咱们两个人要进行查询的语句是一样的，把这个结果**保留**一下，或者那我可以把这个结果当参数**给你传过**去，避免m**ysql****这个业务线，查询线压力过大**
3. **limit语句**提升速度的方式 比如 select * from **limit 10000，20 从第一万条数据开始要20条**，这样子会很慢 如果这样 **select \*from where id>10000 limit 20 会**快很多，先定位到10000 然后**往后**查20条（有要求： 要求主键必须是自增的int型）
4. **update更新**注意的问题 更新哪个字段，查出哪个字段单独更新回去哪个字段，比如查了 age 就改age 就行了 ，如果update set name={} and set age={}and.......全更新的话，可能就发生更新丢失问题了，比如把人家刚改的name给改掉了
5. 日期类型选择：datetime 8字节(5.6.4之后5字节) timestemp4 datetime3
6. Int字节 tinyint的取舍 char和varchar如果说能确定下数据大小，用个char varchar的话他会有个变长字段类型 占用空间 字段值设为非空，null就少一个字节
7. insert插入数据 ，那需要批量插入数据的地方for循环肯定不太好，使用mysql内置的批量插入语句，

### 系统配置

redolog刷盘策略 几次提交数据刷一次盘 默认一次 调次数多了性能高 安全性降低

binlog刷盘策略 同上

### 慢sql：

Explain分析 sql的执行计划 看type字段 system const equal eq_ref ref range index all

- 索引走的是主键或者唯一索引是const
- 走的普通索引是ref
- 连表查询时被驱动表是主键索引，唯一索引，他走的字段的type就是eq_ref
- 范围查询时是range
- 索引覆盖是index

暂时无法在飞书文档外展示此内容

暂时无法在飞书文档外展示此内容

# Spring

- spring是一个开源的，轻量级的,非入侵式的框架，是一个容器
- 核心式控制反转IOC，和面向切面编程AOP
- 支持事务的整理，对框架的整合

一句话：spring就是一个轻量级的，具有控制反转IOC和面向切面AOP的框架（容器）

什么是IOC控制反转？

**IOC****（****inversion of control****）****控制反转****是一种思想，****DI****（****依赖注入****）是实现IOC的一种方式**

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=YzJjNGIxOTZjNjgxMTZjYTQ4NGIwZmE2ZTkyNTAzMmJfY1Z2MlpHY2JnSDVRQ1ZFVDZEdkxCaEs1cmpocjFtYVdfVG9rZW46THVMeGJpcDRtb09Fb3d4bUdIZGNGT3BpbmtlXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

控制：传统的程序对象是由程序代码创建的，在spring中，对象是由spring框架创建的

反转：程序主动不创建对象，反而被动的接收对象

总结为一句话，IOC就是对象由spring框架来创建，管理，装配

## spring配置：

1. bean配置：class全限定名，指定创建哪个类的对象，id代表实例化对象名称；properties name配置这个对象的 属性，value给对应的属性赋值值，也可以使用ref引用容器中其他实例化对象为其赋值，通过赋值id赋值属性。
2. Import 通过import可以导入其他的配置文件

spring框架在applicationcontext启动的时候，就把所有的bean初始化好了（包括构造方法等），你需要的话就从容器中取，并且调用同一个对象名取出来的都是同一个对象

## 依赖注入：

1. **构造器注入**

2. **set注入（重点）**

   1. ```
      <!--     
            依赖注入:给核心容器中的Bean对象的成员变量赋值
            setter方法进行依赖注入:
                在要进行依赖注入的bean标签中添加<property>子标签,该子标签的name属性就是要赋值的成员变量名
                前提是这个属性一定要有set方法,name属性的值应该是"setXXX"后面的"XXX"首字母改小写
                  -->
      ```

   2.   依赖：bean对象的创建依赖于容器

   3.   注入：bean对象的属性，由容器注入

   4. 普通值注入 value

   5. bean注入 ref引用注入

   6. 数组，集合类的，在properties里扩展子标签然后赋值 如<array>,<list>,<map>map注意是俩值，key value

3. **第三方方式注入：**

   1. p命名空间注入和c命名空间注入，可以简化注入操作，p简化properties，c简化构造器注入constructor，直接在bean标签内以p/c：加属性名/构造参数名的方式注入属性值

——注意点：c，p命名空间不能直接使用，需要导入命名空间

### bean的作用域：

通过配置bean标签的scope属性来指定bean的作用域范围

暂时无法在飞书文档外展示此内容

1. 单例模式，scope=singleton通过对象名获取的对象每次都是同一对象
2. 原型模式，prototype，每次获得对象都不是同一对象

### bean的装配方式

1. xml装配

2. java装配

3. 自动装配：

   1. 在**bean标签里输入autowired**，spring会在容器里自动寻找符合条件的对象给自己的属性赋值，如果给autowired赋值为**byName会通过其他组件的id名匹配该bean对应的实体类的set方**法的名字给成员变量赋值；如果是**byType会自动寻找符合成员变量的类型的对象赋值**。

   2. 注解autowired装配：有两个方式 autowired和resource ，

   3.   @**autowired**：先按类型查找唯一bean找，找不到装配失败，找到一个装配成功，如果找到多个，就变量上的**@Qualifier：**注解值作为bean id查找，如果没有@Qualifier注解则装配失败

   4.   @resource：默认按name自动装配 

   5. #####   **Autowired注解和Resource注解的区别**

   6.   @Autowired功能虽说非常强大，但是也有些不足之处。比如：比如它跟spring强耦合了，如果换成了JFinal等其他框架，功能就会失效。而@Resource是JSR-250提供的，它是Java标准，绝大部分框架都支持。

   7.   除此之外，有些场景使用@Autowired无法满足的要求，改成@Resource却能解决问题。接下来，我们重点看看@Autowired和@Resource的区别。

   8. @Autowired默认按byType自动装配，而@Resource默认byName自动装配。

   9. @Autowired只包含一个参数：required，表示是否开启自动注入，默认是true。而@Resource包含七个参数，其中最重要的两个参数是：name 和 type。

   10. @Autowired如果要使用byName，需要使用@Qualifier一起配合。而@Resource如果指定了name，则用byName自动装配，如果指定了type，则用byType自动装配。

   11. @Autowired能够用在：构造器、方法、参数、成员变量和注解上，而@Resource能用在：类、成员变量和方法上。

   12. @Autowired是spring定义的注解，而@Resource是JSR-250定义的注解。

@Nullable注解修饰的字段变量值可以为null

使用注解开发需要导入context约束，配置增加context注解驱动，spring4之后必需要导入aop包

## spring的注解：

- @Autowired：自动装配用到的，先byType查找
- @Resource：先按byName查找能匹配变量的对象
- @Nullable：在字段上加此注解表明值可以为空
- @Component：组件，这个类交给spring管理，是bean！

注解与xml的区别：

xml：功能全面，维护方便

注解：维护复杂，

使用注解需要注意的最大问题：让注解生效 开启注解，包扫描

## 代理模式：

分类：

- 静态代理
- 动态代理

代理模式就是在被代理者调用目标方法的时候，不直接接触目标，而是通过**代理类间接调用**，让不属于目标方法核心逻辑的代码从目标方法中剥离出来--**解耦**，调用目标方法时先调用代理对象方法，同时让附加方法集中到一起也方便管理维护

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NjIzMzBiNDVjNTIzZGQyNWZlNGJmZTMxZjBmMzcxMWFfdEdheUFqaHBKU044Q2NjTkduR2V5OUV2MkhKTXF0WE1fVG9rZW46UzNLWmJmalFDb1l6OFh4dkRoT2NuZTVUbjBkXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

总的来说，代理模式就是将被代理者的某方法核心代码交给代理者，由代理者管理此方法，并可以对此方法进行扩展和修改，如增加日志功能等

动态代理分成两种:

第一种是**JDK内置的动态代理**，这种动态代理需要被代理者实现接口，如果被代理者没有实现接口，那么则无法使用JDK的动态代理

第二种**是****CGLIB****的动**态代理，在被代理类没有实现任何接口的情况下，Spring会自动使用cglib技术实现代理。

为什么要学代理模式？它是AOP的底层

#### 静态代理：

如一个增删改查被代理类，交给代理者后，代理者通过构造方法注入这个类，实现被代理类的接口类，然后重写里面的方法，通过代理类调用业务方法，并可以在上下游进行拓展 如：

```
public class CalculatorStaticProxy implements Calculator {
    // 将被代理的目标对象声明为成员变量
    private Calculator target;

    public CalculatorStaticProxy(Calculator target) {
        this.target = target;
    }

    @Override
    public int add(int i, int j) {

        // 附加功能由代理类中的代理方法来实现
        System.out.println("[日志] add 方法开始了，参数是：" + i + "," + j);

        // 通过目标对象来实现核心业务逻辑
        int addResult = target.add(i, j);

        System.out.println("[日志] add 方法结束了，结果是：" + addResult);

        return addResult;
    }
    …
```

思考:

静态代理确实实现了解耦，但是由于代码都写死了，完全不具备任何的灵活性。就拿日志功能来说，将来其他地方也需要附加日志，那还得再声明更多个静态代理类，那就产生了大量重复的代码，日志功能还是分散的，没有统一管理。

提出进一步的需求：将日志功能集中到一个代理类中，将来有任何日志需求，都通过这一个代理类来实现。这就需要使用动态代理技术了。

#### 动态代理：

动态代理就是在**程序运行期间**，创造目标对象的代理对象，并**对目标对象中的方法进行功能性增强**的一种代理技术。在生成代理对象的过程中，**目标对象不变**，代理对象中的方法是目标对象方法的增强方法。可以理解为运行期间，**对象中方法的动态拦截，在拦截方法的前后****执行功能****操作。**

**代理类在程序运行期间**，创建的**代理对象称之为动态代理对象**。这种情况下，创建的代理对象，并**不是事先在Java代码中定义**好的。而是在运行期间，根据我**们在动态代理对象中的“指示**”，**动态生成的**。也就是说，你想获取哪个对象的代理，动态代理就会为你动态的生成这个对象的代理对象。动态代理可以对被代理对象的方法进行功能增强。有了动态代理的技术，那么就可以**在不修改方法****源码****的情况下**，**增强被代理对象的方法的功能**，在方法执行前后做任何你想做的事情。

```
package com.atguigu.factory;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

/**
 * 包名:com.atguigu.factory
 *
 * @author Leevi
 * 日期2021-09-01  10:00
 */
public class LogDynamicProxyFactory<T> {
    private T target;

    public LogDynamicProxyFactory(T target) {
        this.target = target;
    }

    /**
     * 创建动态代理对象
     * @return
     */
    public T getProxy(){
        //使用JDK的动态代理技术
        //1. 获取被代理者的字节码对象
        Class<?> clazz = target.getClass();
        //2. 使用JDK中的Proxy.newProxyInstance(classLoader, interfaces, new InvocationHandler()方法创建动态代理对象
        // 该方法的返回值就是动态代理对象
        //2.1 类加载器对象
        ClassLoader classLoader = clazz.getClassLoader();
        //2.2 需要代理的接口:如果明确要代理什么接口，那么就可以直接写new Class[]{要代理的接口.class}
        //如果不明确要代理什么接口，那么就获取被代理者实现的所有接口: 被代理者的字节码对象.getInterfaces();
        Class<?>[] interfaces = clazz.getInterfaces();
        //2.3 InvocationHandler接口的对象: 使用匿名内部类
        T t = (T) Proxy.newProxyInstance(classLoader, interfaces, new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                //invoke()方法:该方法会在代理对象调用任意方法的时候执行
                //所以我们就在这个方法中编写代理逻辑
                //参数一:proxy对象表示代理对象本身
                //参数二:method表示代理对象所调用的方法本身
                //参数三:args表示代理对像所调用的方法中传入的参数

                //编写代理逻辑:我的想法是在执行被代理对象的add()、sub()、mul()、div()这四个方法的前后添加日志打印
                //判断方法：是否是这四个方法
                String proxyMethodName = method.getName();
                //如果是: 则添加前后日志打印
                if (proxyMethodName.equals("add") || proxyMethodName.equals("sub") || proxyMethodName.equals("mul") || proxyMethodName.equals("div")) {
                    //核心逻辑之前打印日志
                    System.out.println("[日志] "+proxyMethodName+"方法开始了，参数是：" + args[0] + "," + args[1]);
                    //执行被代理者的当前方法
                    Object result = method.invoke(target, args);
                    //核心逻辑之后打印日志
                    System.out.println("[日志] "+proxyMethodName+" 方法结束了，结果是：" + result);
                    //返回执行结果
                    return result;
                }
                //如果不是: 就按照被代理者原本的方法执行
                return method.invoke(target,args);
            }
        });
        return t;
    }
}
```

Test测试

```
@Test
public void testDynamicProxy(){
    //创建被代理者
    Calculator calculator = new CalculatorPureImpl();
    //1. 创建动态代理工厂类的对象
    LogDynamicProxyFactory<Calculator> proxyFactory = new LogDynamicProxyFactory<Calculator>(calculator);
    //2. 使用工厂对象创建动态代理对象
    Calculator calculatorProxy = proxyFactory.getProxy();
    //3. 使用代理对象调用方法
    System.out.println(calculatorProxy.sub(3, 4));
}
```

## AOP

什么是AOP？

##### **概念**

AOP：Aspect Oriented Programming面向切面编程 

有几个概念：

- 横切关注点：

横切关注点是从每个方法中抽取出来的同一类非核心业务。在同一个项目中，我们可以使用多个横切关注点对相关方法进行多个不同方面的增强。

这个概念不是语法层面天然存在的，而是根据附加功能的逻辑上的需要：有十个附加功能，就有十个横切关注点。如日志，安全，缓存，事务等

- 切面：定义了要执行的通知方法的类，并包含要织入的切入点
- 切入点：要执行通知的地方，地点
- 通知：切面里的方法，切面要完成的工作
- 目标：被通知的对象，被代理的目标对象,执行核心业务代码的那个对象
- 代理：向目标对象应用通知之后创建的代理对象。
- 连接点：

这也是一个纯逻辑概念，不是语法定义的。

把方法排成一排，每一个横切位置看成x轴方向，把方法从上到下执行的顺序看成y轴，x轴和y轴的交叉点就是连接点。连接点其实就是各个方法中可以被增强或修改的点，就**是只要方法中能织入通知被增强的地方，都可以是连接点，而切入点是确定执行通知的地方，有种包含关系**

##### **作用**

1. 简化代码：把目标方法中固定位置的重复的代码**抽取**出来，让目标方法更专注于自己的核心功能，提高内聚性。
2. 代码增强：把抽取出来的特定的功能封装到切面类中，看哪里有需要，就往上套，被**套用**了切面逻辑的方法就被切面给增强了。

##### 实现aop的方式：

1. xml代码实现，又分为
   1. 使用SpringAPI接口
   2. 自定义实现
2. 注解实现：
   1. 在切面类上加@Aspect
   2. 在想要作为某个**通知的方法上加@Befor**e @After @Aroud等，作为通知吗。在注解后面括号里添加Exception，这个是确定切入点，即通知环绕的具体存放目标方法核心代码的地方

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=YzU4NDU1YTI3ZDQ2NjYyYTliYThhMWI2MmVjNDM4ZTRfRU4yVTM2ZmJmWUtRSUlxNUpITFcxT3JZT0t6Q0pNcHFfVG9rZW46VXRZZ2JmY3Fmb3RuaXJ4UDZhYWNuNmpXblVjXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

## 声明式事务：

# Redis

## 概述：

阿里云的那群疯子文章

多隆

### 为什么要用NOSQL？

单体架构服务器，已经负担不起大量的用户数据，需要NOSQL来做缓冲

1. mysql读写分离
2. 分库分表，水平拆分，mysql集群
3. 扩展性瓶颈 4kb的文本一千万条就是40GB，不放mysql里会好很多 所以出现了 如下

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=OTUyZDZiZWU3M2VjOGFkMmU0NTA1MDA2YTBlNGY3YTFfUVhFNFZ5SU5oeHlUQmp3RmNOcVNOOEhLTGk4cDNqWlpfVG9rZW46WE5Nc2JZZDFLb3JHOEt4TTMweGMwRzNlbmtiXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

####  3V3高，

1V 海量Volume

2V 多样Variety

3V 实时Velocity

3高

高并发，高可扩，高性能

### 什么是NOSQL？

非关系型数据库

- 数据之间没有关系
- 内存中存储
- 没有确切的数据结构
- 不支持外键，索引

### 一个商品页面分析

1. 基本信息：（商品名，价格，商家信息）

- mysql/orical等关系型数据库就可以了

1. 商品详情，评论（字比较多）：

- 文档型数据库，MongoDB

1. 图片信息：

图片数据库，分布式文件系统 FastDFS

- 淘宝的 TFS
- Google GFS
- Hadoop HDFS
- 阿里云 oss

1. 商品搜索关键字，如何定位到这个商品的：

- 搜索引擎，ElasticSearch solr
- ISerach：多隆

1. 商品热门的波段信息（突出一个热门，经常用到）

- 内存数据库
- Redis Memacache Tair

1. 商品交易，外部的支付接口

### NOSQL非关系型数据库的四大分类

#### KV键值对型：

新浪：redis+BerkeleyDB

美团：redis+Tair

阿里，百度：redis+memcache

#### 文档型数据库：

MongoDB，介于非关系型数据库和关系型数据库之间，类型丰富，是最像关系型数据库的非关系型数据库

#### 列存储数据库

- Hbase
- 分布式文件系统

#### 图关系数据库：

以记录关系为主，微信社交朋友圈，关注粉丝列表。

Neo4j，InfoGrid

间接数据模型，假设在一个电商系统中，有，客户，订单，地址，订购这几个模型，对比SQL与NOSQL，

- 查询一些关系图，例如共同关注，好友列表等，如果是关系型，连表查询什么七大姑的八大姨等，很麻烦
- 或者需要传输的数据，需要查很多次表，可以封装为一个Bson数据，里面存着所有需要的数据 类似json

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=OGYyMTg2NTQ1YzRiYTE5NDlmNGI0MmY4ZjZmNDA4YThfN0JsOU92SUo5eHhoblBVeFhaRjI1UnlscGdzaURuM29fVG9rZW46Q0xUOWIxQktnbzI4aG94aG5ORmN1OEg1blJlXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

### CAP原理+BASE

C：强一致性Consistency  查出来的数据，必须是正确的，要么直接失败，正确or失败，强调正确性

A：Avaiability 可用性 请求一定能得到相应，数据可能不是最新的，有延时的但是一定会返回数据，强调成功

P：Partition tolerance **分区容错性** 分布式的服务器 其中一台出错了 不影响其他服务的正常运行（不会雪崩效应） 强调的是正常运行

强调的是分布式系统的CA抉择问题，所以P是一定保证的前提，强一致性C和可用性A只能保证一个

BASE

基本可用（Basically Available）

软状态（Soft state）

最终一致（Eventually consistent） 最重要

分布式与集群的区别，分布式是不同的服务分布在好多个机器上，集群是在好多台服务器部署相同的服务。

## Redis的安装

解压完成 make命令按照

必须保证环境里有gcc，它是redis 的编译工具 c++相关的好像是

##  安装完后的杂项知识

## 基础知识：

redis有16个数据库，默认使用的是第0个

#### 趣味故事：

Redis 端口号是6379，是因为创作者当时觉得一个广告女郎很蠢，就把人家名字缩写对应的9键值拿来做端口号了。

#### 基本命令

Set * x设置*字段值为x

Get *获得*的值

Select 5 切换5号数据库

Flushdb 清空当前数据库

Flushall 清空所有数据库的内容

Dbsize 查看当前数据库大小

Keys 查看所有的key值

#### 性能：

reids是单线程，并且单线程性能极其高，大概QPS在10w，这得益于它将所有数据都放在内存中，而多线程频繁的上下文切换反而会降低它的速率。对于内存系统来说，没有上下文切换效率就是最高的

cpu并不是它的性能瓶颈，内存大小和网络带宽才是。

## 八种数据类型讲解

### key操作：

1. key命名规范：

- key尽量不要超过512kb，尽管最大512MB，多了会增加查询时间，消耗性能
- key要做到见名知意
- key多个字段之间可以用：分割 如 user：token：session：id
- Redis命令不分大小写，key分大小写

1. key命令

- Keys pattern 匹配Key 如果是全部或者多个字符可以用*，单个字符用？如？葛*（诸葛孔明可匹配）
- Move key db 把key移动到另一个数据库
- Exist key 查看key是否存在，存在返回1 不存在返回0 可以指定多个key，返回找到key的数量
- Expire key 秒 设置过期时间，使key多少秒后过期
- Ttl key 查看还多少秒过期
- Pesist key 移除过期时间，变成永久

暂时无法在飞书文档外展示此内容

1. ### String（字符串）类型操作：

Get key 

Set key value

Setnx key1 value1设置key1的值 如果key1不存在的话（像不像CAS机制，比较并更改）

Mxset key1 value1 key2 value2 批量插入数据

Incr key1 相当于 i++ 自增1

Decr key1 i-- 自减一

Set key  seconds value

Ttl key

暂时无法在飞书文档外展示此内容

String类型的value值最大时512MB，底层数据结构实现是使用int和SDS

- **SDS****不仅可以保存文本数据，还可以保存二进制数据**，这也使得String可以保存图片，文件，视频等资料
- **SDS****获取字符串长度的时间复杂度是O（1**），因为它有一个记录存储字符串长度，而C没有
- 

#### String的使用场景：

- 计数器
- 统计多单位的数量
- 粉丝数，关注数，点赞量等等这些
- 对象缓存存储！ ：

对象存储时有两种方式，一种**json****字符串** **SET** **user:1 '{"name":"tom","age":"16"}'**

一种是利用**MSET存储，**然后使用**MGET获取，如MSET user:1:name tom user:1:age 16**

- 分布式锁：SET命令有个NX参数，判断如果不存在时SET值，可以用作锁资源占用与否的情况，当加锁时，执行如下语句

  - ```
    SET lock_key unique_value NX 10000
    ```

  - lock_key是锁的key，

  - unique_value代表每个客户端的唯一标识

  - NX代表只有lock_key不存在时，才对lock——key进行上锁操作

  - 10000是10s，这个时间过后锁自动销毁，用来避免出现异常锁被长时间占用

  -   分布式锁解锁的时候，要判断是否是持锁客户端发起的请求，也就是对比一下unique_value的值是否是当前客户端，然后通过执行LUA脚本进行解锁流程，LUA脚本在redis中具有原子性，保证解锁流程的正确推进。

- session共享：

分布式系统中，服务器会单独存储用户的session，当客户端登陆时，发送请求，服务器使用本地session存储用户的信息，多台服务器存储的不一样，当用户再次登录时，用户被分配到另一台服务器，没有该用户的session信息，这时候需要重新登录，这时候我们使用redis string类型解决这个问题，如下图

暂时无法在飞书文档外展示此内容

1. ### List（列表）类型操作

暂时无法在飞书文档外展示此内容

Redis里list的底层是双向链表和压缩队列，但是在3.2之后改成quicklist了，最多存储2^32-1个元素，也就是40亿以上的数据

- 将List看作一个队列大概这个样子
- List里面的值**是有序，并且可以重复的**，是一**个左右都可进出的队**列，添加进去的元素按顺序排队
- **Redis的列表类型在**List插入数据时如果列表不存在，就**创建**该列表，当某一列表的元素全部被移除时，该列表也被**移除**
- 列表的底层**是****双向链表****，**所以它头尾操作效率高，中间操作效率低（需要遍历）
- 作消息队列 消息队列！！ 栈

暂时无法在飞书文档外展示此内容

#### 应用场景

消息队列：

消息队列在存储消息时，必须满足三个要求，**消息保序，处理重复的消息，和保证消息的可靠性**

1. 如何保证消息保序性

生产者LPUSH从一边添加消息，消费者利用RPOP从另一边获取消息，而不能从中间插入数据。list本身就是先进先出的队列，但是存在一个缺陷，就是redis的list队列生产者放入消息后，不能提醒消费者获取消息，所以消费者要不停调用RPOPwhile1循环来获取消息，如果没有消息的时候会消耗资源，所以redis为了解决这个问题，提供了一个新命令BRPOP，**BRPOP命令也称为阻塞式读取，客户端在没有读到队列数据时，自动阻塞，直到有新的数据写入队列，再开始读取新数据。**

2.如何处理重复的消息

保证这一条需要两点

- 给每条消息一个全局ID
- 消费者根据ID判断操作了重复过的消息就不再对此消息操作

**但是list不能自发生成这个id，所以需要我们手动增加一个全局id，**如SET mq "11211001：stock：67"，设置一个库存67的消息，

1. 保证可靠性

消费者处理消息的过程中，服务宕机，消息还没处理完，但已经从队列中取出来了，造成消息丢失，针对这个问题，可以**使用RPOPLPUSH****指令****，取出消息的同时将消息存入另一个队列，来对消息作了备份**

1. ### SET（无序）集合类型操作

Redis set集合里面存放无序，去重的元素，里面的数据随机存放，不能添加重复值。它是基于hash表实现的

存放string类型

Set 类型的底层数据结构是由**哈希表****或整数集合实**现的：

- 如果集合中的元素都是整数且元素个数小于 `512` （默认值，`set-maxintset-entries`配置）个，Redis 会使用**整数集合作为 Se**t 类型的底层数据结构；
- 如果集合中的元素不满足上面条件，则 Redis 使用**哈希表****作**为 Set 类型的底层数据结构。

暂时无法在飞书文档外展示此内容

暂时无法在飞书文档外展示此内容

#### 应用：

- 取交集，做共同好友，共同关注操作

1. ### Hash（哈希）类型操作

本身就是一个键值对集合。可以当做Java中的Map<String,String>对待。每一个hash可以存储2^32-1个键值对

Hash 类型的底层数据结构是由**压缩列表或****哈希表**实现的：

- 如果哈希类型元素个数小于 `512` 个（默认值，可由 `hash-max-ziplist-entries` 配置），所有值小于 `64` 字节（默认值，可由 `hash-max-ziplist-value` 配置）的话，Redis 会使用**压缩列表作**为 Hash 类型的底层数据结构；
- 如果哈希类型元素不满足上面条件，Redis 会使**用****哈希表****作为** Hash 类型的 底层数据结构。

在 Redis 7.0 中，压缩列表数据结构已经废弃了，交由 **listpack 数据结构**来实现了。

暂时无法在飞书文档外展示此内容

暂时无法在飞书文档外展示此内容

#### 场景：

购物车，用户购物车的id为key，商品id为field，商品数量为value，正好组合起来存储到Hash里

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NzJiMjNhZmQyMGZiMWM1MmVkMjI2MjFjNmJlN2YwM2FfYVlrNnpjSzZpVWVxMnVNQWFZRDJtYlFsMFZJQ2p6V3NfVG9rZW46RW94aGJDSkJ4b1NPSkN4QTM0emNvMmdwbjZlXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

涉及的命令如下：

- 添加商品：`HSET cart:{用户id} {商品id} 1`
- 添加数量：`HINCRBY cart:{用户id} {商品id} 1`
- 商品总数：`HLEN cart:{用户id}`
- 删除商品：`HDEL cart:{用户id} {商品id}`
- 获取购物车所有商品：`HGETALL cart:{用户id}`

当前仅仅是将商品ID存储到了Redis 中，在回显商品具体信息的时候，还需要拿着商品 id 查询一次数据库，获取完整的商品的信息。

1. ### ZSet（Sorted Set有序集合）类型

Zset也是去重的集合，与Set不同的是它的内容是有序的，并且每个元素都关联了一个double类型的分数，zset就是通过这个分数排序的

暂时无法在飞书文档外展示此内容

暂时无法在飞书文档外展示此内容

#### 实现：

- 工资排序表，班级成绩排序。
- TOP N 排行榜排序
- 普通消息1 重要消息2 做权重分配

1. ### GEO

存储地理位置信息，包括经纬度，底层还是Sorted Set

存储指定的地理空间位置，可以将一个或多个经度(longitude)、纬度(latitude)、位置名称(member)添加到指定的 key 中。 **GEOADD key longitude latitude member [longitude latitude member ...**]从给定的 key 里返回所有指定名称(member)的位置（经度和纬度），不存在的返回 nil。 **GEOPOS key member [member ...]**返回两个给定位置之间的距离。 **GEODIST key member1 member2 [m|****km****|ft|mi]**根据用户给定的经纬度坐标来获取指定范围内的地理位置集合。 **GEORADIUS key longitude latitude** **radius** **m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count] [****ASC****|DESC] [STORE key] [STOREDIST key]**

#### 场景：滴滴打车

这里以滴滴叫车的场景为例，介绍下具体如何使用 GEO 命令：GEOADD 和 GEORADIUS 这两个命令。

假设车辆 ID 是 33，经纬度位置是（116.034579，39.030452），我们可以用一个 GEO 集合保存所有车辆的经纬度，集合 key 是 cars:locations。

执行下面的这个命令，就可以把 ID 号为 33 的车辆的当前经纬度位置存入 GEO 集合中：

```
GEOADD cars:locations 116.034579 39.030452 33
```

当用户想要寻找自己附近的网约车时，LBS 应用就可以使用 GEORADIUS 命令。

例如，LBS 应用执行下面的命令时，Redis 会根据输入的用户的经纬度信息（116.054579，39.030452 ），查找以这个经纬度为中心的 5 公里内的车辆信息，并返回给 LBS 应用。

```
GEORADIUS cars:locations 116.054579 39.030452 5 km ASC COUNT 10
```

1. ### Bitmap 位图

存储二进制数据，一串连续的二进制数组，时间复杂度O（1），它使用bit存储，是计算机存储最小单位，所以特别适合一些**存储数据量大的二值统计场景**。

bitmap底层是使用String类型，String可以保存为二进制字节数组，可以把bitmap看成一个bit数组。

```
//key是唯一标识，offset为偏移量，是指这个数据的偏移值，上下的offset是有关联的，前后偏移， 
//value值代表是否 只能为0和1
SETBIT key offset value
//获取key值
GETBIT key offset
//获取指定范围内值为1的数
GETCOUNT key start end

# BitMap间的运算# operations 位移操作符，枚举值
  AND 与运算 &
  OR 或运算 |
  XOR 异或 ^
  NOT 取反 ~
# result 计算的结果，会存储在该key中# key1 … keyn 参与运算的key，可以有多个，空格分割，not运算只能一个key# 当 BITOP 处理不同长度的字符串时，较短的那个字符串所缺少的部分会被看作 0。返回值是保存到 destkey 的字符串的长度（以字节byte为单位），和输入 key 中最长的字符串长度相等。
BITOP [operations] [result] [key1] [keyn…]# 返回指定key中第一次出现指定value(0/1)的位置
BITPOS [key] [value]
```

#### 场景：如前面所讲，bitmap特别适合二值统计场景

**签到统计：**在一些需要签到的场景，我们可以使用01来表示签到或未签到

**用户登录状态：**

1. ### HyperLogLog

这个命令的作用是用来统计一组元素中去重的元素数量，不过它是有误差的，大概在0.81%。简单来说 **HyperLogLog** **提供不精确的去重计数**

在redis里面，一般来说它不管数据量有多大，只占12k的存储空间，即使给2^64个元素统计。所以非常节省空间

他的命令很少，就仨

```
// 添加指定元素到 HyperLogLog 中
PFADD key element [element ...]
//返回给定 HyperLogLog 的基数估算值。
PFCOUNT key [key ...]
// 将多个 HyperLogLog 合并为一个 HyperLogLog
PFMERGE destkey sourcekey [sourcekey ...]
```

#### 应用场景：

百万级网页的UV（访问数）计数，统计的是网页被不同ip的访问数，也就是说重复id只计一次，相对的概念是pv，即访问量

## redis数据结构：

String底层是int+SDS

List底层是双向链表和压缩列表，3.2后quicklist

Hash底层是压缩列表或哈希表

Set底层是哈希表或整数集合

Zset底层，压缩列表或跳表

### SDS：

SDS数据结构是一个字符数组+三个字段的结构，包括len记录字符串长度，alloc分配空间大小，flags sds类型

## redis事务：

### redis基本事务：

redis开启事务提交事务的过程大概分三步

1. 开启事务  multi
2. 命令入队 
3. 提交事务 exec

**redis事务的本质是一组命令的集合，一个事务中所有的命令都会一次性，顺序性，排他性的执行完**

即所有的事务都按顺序全部一次执行

暂时无法在飞书文档外展示此内容

**redis只保证单条命令的原子性，不保证事务的原子性**

1. 对于**编译时异常：**

整个事务其他命令也会执行失败，如gett k输入了一个错误的指令

1. 对于**运行时异常**

除了出错的那条命令执行失败，其他所有命令正常依次执行 如：

multi

 Set key1 value1  

Get key1 

Setnx key2 value2

Setnx key1 value1

LPUSH list k1

RPUSH list k2

LPOP

RPOP

SADD keys member

SADD keys member

SREM keys

SDIFF keys keys2

SINTER keys keys2

SUNION keys keys2

HSET keyh field1 value1

HMSET keyh field2 value2 field3 value3

HMGET keyh field1 field3

HGETALL keyh

HDEL keyh field2

ZADD keyz member1

ZRANGE keyz 100 200

exec

暂时无法在飞书文档外展示此内容

### redis的乐观锁机制

redis提供了一个watch监视器，用来监视某个值的改动，通过这个监视器实现一种类似乐观锁的机制，如：

```
Set money 100
Set out 0
Watch money
Multi
Decrby money 20
Incrby out 20
Exec
```

执行完成后 money少了20 out多20，如果在此期间， 其他客户端设置了money的值，这次事务操作就会提交失败，需要通过unwatch操作然后重新watch

## Jedis：

导入jedis依赖包，创建jedis对象。通过jedis.set/sadd/lpush等等方法名即为redis里的指令，然后参数代表指令后面要操作的各种key value member。

Jedis里面的事务要try catch 包裹，try之前multi，不出问题try里，exec 除了问题捕获到discard

#### springboot整合redis 

命令带ofp什么的额外输入，我们可以整一个模板类，工具类，将语句封装成一个适合的格式，比如与redis里命令一模一样。

## Redis持久化机制

### RDB快照 redis database

RDB提供两个命令来生成RDB文件，

#### 快照机制：

**save**：在主线程生成RDB文件，和执行操作命令使用同一线程，所以写入RDB时间太长，**会阻塞主线程**

**bgsave**：创建一个子线程生成RDB文件，**避免主线程的阻塞**

RDB文件的加载工作，是在redis服务器启动时自动执行的。

redis可以通过可配置的默认的配置参数**900 1，600 10，60 10000**来触发**bgsave**，来将内存中的redis命令持久化到磁盘中去。redis快照是**全量快照**，也就是在执行快照时，将内存中所有的数据都记录到磁盘中。

所以相对来说，快照是一个比较重的操作，如果**执行快照频繁，那么会对****cpu****性能有影响，如果执频率过低，服务器故障丢失的数据就会很多。**

通常设置5分钟进行一次快照，所以故障会丢失5分钟的数据，这就是RDB的缺点，AOP相对来说，秒级的记录能够保证丢失的数据少。

保存到磁盘中的文件格式是dump.rdb，文件名可以通过配置文件更改，默认的保存位置也可以更改，不过是更改目录，不是文件

#### 执行快照时，数据能被修改吗？

父进程创建一个子进程进行RDB写入操作，这个过程中只有读操作时，**子父进程指向的是同一块儿****物理内存****，**而父**进程需要执行写操作时，会通过****写时复制****技术，创建出一块物理内存的副本**，子进**程对副本进行RDB快照操作**， 父进程将修改的数据**写入原****内存****，**所以这个操作结束后，**RDB快照保存的是原来的数据**，在写入过**程中新加的数据，需要等到快照时写入**。

在极端情况下，所有的**共享内存****都被修改，会占用较大****内存****，所有要留意内存的变化**

暂时无法在飞书文档外展示此内容

#### 优势：

1. RDB文件存储的是二进制数据，所以数据恢复的速度快，效率高
2. 这种文件不易丢失

缺点：发生故障丢失数据多

数据量大，频繁时，耗费性能多

### AOF日志，持久化机制 AppendOnlyFile

  这个机制是，将所有的**写操作命令，执行完成后写入****AOF****日志（读操作不记录，因为无意义）**，默认不开启这个功能，如果需要开启，修改redis.config文件。先执行命令，后写入，有两个好处：

1. 避免额外开销，先写的话，如果是个错误语句，没有语法检查，不合理
2. 不会阻塞当前写命令。

  不过当然它也有两个风险

1. 执行之后，没有写入之前，宕机，有数据丢失风险
2. 写入的时候，可能会阻塞下一条写操作。

####   AOF写入机制：

  然后实际上，AOF的写入操作是这样一个步骤，先将**写****指令****写入****内存****缓冲区**，然后由具**体的策略决定刷盘的时机**。

暂时无法在飞书文档外展示此内容

  这个机制有三种写入策略，需要手动开启，一个是每次操作时写入，每秒写入，和自适应时间写入，默认

  在 **redis.conf** 配置文件中的 **appendfsync** 配置项可以有以下 3 种参数可填：

- **Always**，这个单词的意思是「总是」，所以它的意思是**每次写操作命令执行完**后，**同步将** **AOF** **日志数据写回硬盘**；
- **Everysec**，这个单词的意思是「每秒」，所以它的意思是**每次写操作命令执行完**后，先将**命令写入到** **AOF** **文件的内核缓冲区**，然后每隔一秒将缓冲区里的内容写回到硬盘；
- **No**，意味着不由 Redis 控制写回硬盘的时机，转交给操作系统控制写回的时机，也就是每次写操作命令执行完后，先将命令写入到 AOF 文件的内核缓冲区，再由操作系统决定何时将缓冲区内容写回硬盘。

####   AOF重写：

  aof是一个日志文件，随着写操作指令越来越多，它在重启redis的时候执行的速度可能会越来越慢，效率低下，这时候就需要使用**aof重写机制**对aof文件进行重写。

  AOF重写的时候，会记录下当前内存所有的键值对，创建一个新的AOF文件，然后用最简短的指令将他们写入AOF，写入完成后，替换掉原来的aof文件。

####   AOF后台重写：

  AOF写入日志操作不是太耗主进程性能，但是aof重写，在数据大于i64mb触发时，重写替换的过程是很耗时的，所以不能放到主线程中进行，所以，**Redis 的重写 AOF 过程是由后台****子进程** **\*bgrewriteaof*** **来完成的**，这么做可以达到两个好处：

1. 子进程重写时，不影响主进程的写操作
2. 子进程带有主进程**的数据副本**（*数据副本怎么产生的后面会说*），这里使用**子进程而不是线程**，因为如果是使用**线程，****多线程****之间会****共享内存****，那么在修改共享内存数据的时候，需要通过加锁来保证数据的安全，而这样就会降低性能**。而使用子进程，创建子进程时**，父子进程是共享内存数据的**，不过这个共享的内存**只能以****只读****的方式**，而当父子进程任意一方修改了该共享内存，**就会发生「****写时复制**」，于是父子进程就有了独**立的数据副本，就不用加锁来保证数据安全**。

还有一个问题，AOF子进程在重写AOF时，父进程修改了原先的键值对，这样会造成数据不一致，所以引入了**AOF重写缓冲区**

当子进程完成 AOF 重写工作（*扫描数据库中所有数据，逐一把**内存**数据的键值对转换成一条命令，再将命令记录到重写日志*）后，会向主进程发送一条信号，信号是进程间通讯的一种方式，且是异步的。

主进程收到该信号后，会调用一个信号处理函数，该函数主要做以下工作：

- 将 AOF 重写缓冲区中的所有内容追加到新的 AOF 的文件中，使得新旧两个 AOF 文件所保存的数据库状态一致；
- 新的 AOF 的文件进行改名，覆盖现有的 AOF 文件

### RDB联合AOF

在文件的前半部分使用RDB，后半部分使用AOF

## 过期删除策略

redis中提供了一些给字段设置过期时间的命令，例如

```
//设置某key过期时间为n，单位为秒
EXPIRE key n
//设置过期时间为n单位为毫秒
PEXPIRE KEY N

//设置过期时间为某个时间戳，精确到秒
EXPIREAT KEY N
//设置过期时间为某个时间戳，精确到毫秒
PEXPIREAT KEY N

//赋值的时候设置过期时间
SETEX key n value
//查看某个key还多久过期
ttl key
```

然后对于过期key的处理，有三种过期策略：

1. **定时删除策略：**

给key设置一个定时器，当key过期的时候，立马调用该定时器将key删除。

优点：能够及时的清除过期key，节省了内存资源

缺点：随时调用cpu资源，对cpu性能影响较大

1. **惰性删除策略：**

当调用命令去查询key的时候，才会判断key是否已经过期，过期则清除。

优点：对cpu很友好，只有查询到key的时候才会调用cpu清理

缺点：如果积攒了大量的过期key没有查询到，会导致内存空间资源紧张

1. **定期删除策略：**

会定期的抽选一部分key检查其是否过期，如果过期则清除。

优点：即保证了cpu压力不会过高，又中和了内存的占用率

缺点：定期删除的频率参数设置不合理的话，又会导致上面两种情况

Redis采用的是**[定期删除+惰性删除]**策略，每次去查询数据的时候，都会判断一下该key是否过期，不过期则返回，过期了删除key然后返回null。redis的定期删除策略，默认每10s进行一次，每次随机抽取20个key检查其是否过期，如果此次清除的数量大于25%（即5个），则会继续抽查20个，以此类推，不过为了避免出现故障一直抽查下去，这个过期最多持续25ms

## 内存淘汰策略

redis4.0之后内存淘汰策略有8种，他们大致被分为两大类

1. #### 不进行内存淘汰的策略

在内存使用量超过设置的最大限定值的时候，不删除任何内存数据，而是在新增键值时，报错无法添加数据，而不影响原本的查询删除操作。

1. #### 进行内存淘汰的策略

这个又分为两种：

1. 针对有过期值的数据的策略
   1. volatile-random：随机删除设置了过期策略的数据
   2. volatile-ttl：优先删除最先过期的数据
   3. volatile-lru：优先删除最长时间未使用的设置了过期值的数据
   4. volatile-lfu：优先删除最近使用频率最少的设置了过期值数据
2. 针对所有数据的策略
   1. allkeys-random：随机删除一些数据
   2. allkeys-lru：删除最长时间未使用的数据
   3. allkeys-lfu：删除最近使用频率最少的数据

> **如何查看当前 Redis 使用的****内存****淘汰策略？**

  可以使用 `config get maxmemory-policy` 命令，来查看当前 Redis 的内存淘汰策略，命令如下：

```
127.0.0.1:6379 config get maxmemory-policy
1) "maxmemory-policy"2) "noeviction"
```

  可以看出，当前 Redis 使用的是 `noeviction` 类型的内存淘汰策略，它是 Redis 3.0 之后默认使用的内存淘汰策略，表示当**运行内存****超过最大设置内存时，不淘汰任何数据，但新增操作会报错。**

## 主从复制

### 主从复制出现的背景：

主服务器不可避免可能会硬盘故障等问题导致数据损坏，无法恢复的情况。或者服务器宕机，数据恢复期间的请求无法得到响应。这时候给主服务器配备从服务器可以解决这个问题，实现数据多备份，继续提供服务。

### 主从复制是如何实现的：

主从复制大体分为三个过程，

1. 主从服务器建立连接

主服务器向从服务器发起主从复制请求，携带自己的id和复制进度，这个叫replicaof 命令。从服务器收到后返回确认信息，发送`psync` 命令，里面就包含主服务器id和复制进度，主服务器收到命令后会发送`FULLRESYNC` 命令，用来表示要进行**全量复制。也就是把所有数据传送给从服务器**

1. 主服务器向从服务器写入数据

主服务器将以bgsave的方式，将快照传送给从服务器，从服务器读取完bgsave后覆盖自己原先的数据。在bgsave期间主服务器产生的新的写数据，主服务器会将其写入缓冲池，从服务器读取完毕之后再将其写入

1. 主服务器写入缓冲池数据到从服务器

将缓冲池replication buffer数据，再次写入从服务器

这一顿操作结束后，他俩会维护一个tcp连接，方便继续同步，而避免多次建立连接产生的开销，上面的这个过程被称为**基于长连接的命令传播**，通过这种方式来保证第一次同步后的主从服务器的数据一致性。

### 主服务器压力分担

当从服务器较多的时候，如果全都交给主服务器同步，压力会很大，这时候将其中的某个从服务器充当主服务给它的从服务器同步数据，为主服务器分担压力

### 增量复制

当主从服务器建立连接后，发生网络故障连接中断，再次建立连接后就不进行全量复制了，因为代价太大，使用增量复制同步数据，因为主从服务器都维护了一个偏移量字段，用来记录自己的数据写到什么地方，主服务器会根据这两者来将从服务器缺失的数据写入缓存池，然后同步到从服务器，这一过程称为**增量复制**

## 哨兵机制：

#### 为什么要有哨兵机制？

在redis主从架构中，主服务器宕机之后，客户端发来的请求数据无法同步到从服务器了，哨兵机制应运而生，检测主服务器，在主服务器故障后选择一台从服务器来当作主服务器

哨兵是redis下的一个进程，所以它也是一个节点，它主要负责三件事：**监控，选主，通知**

**如何判断节点真的故障了？**

哨兵一般最低为三个，里面有两个参数，都需要由所有哨兵共同投票决定，如，有一个哨兵检测到主服务器出现问题，发起主观下线，然后其他哨兵开始投票，根据参数设置，如果投票的数量到达参数（quorum ）设置（**一般推荐哨兵数除2加一，为奇数**），将主服务器设置成客观下线，然后再次投票决定投票哨兵是否为leader（**超过半数以上并且要大于配置的quorum 数**），接着选一个从服务器作为主服务器，从服务器的选择有几点考察，**第一，选择节点安全排名优先级最高的，如果有一样的，选择复制进度最大的，再不济，选择id号小的那个**。

#### 主从故障转移是怎样的

分为四步

1. 选出从服务器，这个又有三个选择标准，**优先级，复制进度，标号id**

   1. 有个配置，当连接超时大于配置的参数，记为一次断连，根据这个断连次数排除优先级最高的
   2. 如果一样，则看复制进度，主节点的master_repl_offset 参数中记录着复制进度，从从节点找出最接近的进度
   3. 如果还一样，选择id号最小的

2. 将从节点指向新节点：

   1. 当新主节点出现之后，哨兵 leader 下一步要做的就是，让已下线主节点属下的所有「从节点」指向「新主节点」，这一动作可以通过向「从节点」发送 `SLAVEOF` 命令来实现。

3. 通知客户的主节点已更换

   1. 这主要通过 Redis 的**发布者/订阅者机制来**实现的。每个哨兵节点提供发布者/订阅者机制，**客户端可以从哨兵订阅消息。**

   2.   客户端和哨兵建立连接后，客户端会订阅哨兵提供的频道。主从切换完成后，哨兵就会向 `+switch-master` 频道发布新主节点的 IP 地址和端口的消息，这个时候客户端就可以收到这条信息，然后用这里面的新主节点的 IP 地址和端口进行通信了。

4. 将旧主节点变为从节点

   1. 旧的主节点上线后，向他发送`SLAVEOF` 命令，使其成为从节点

#### 哨兵集群如何通知：

**哨兵节点之间是通过 Redis 的发布者/订阅者机制来相互发现的。**订阅消息到主节点，通过主节点获取其他哨兵的消息，包括从节点的消息获取，也是通过主节点获取的

## 缓存篇：

数据并发量特别大时，数据库常常承担很大的压力，甚至宕机，引入了缓存机制用以分担数据库的读写压力。但是

redis有三个缓存问题：缓存雪崩，缓存击穿，缓存穿透

### 缓存雪崩：

缓存雪崩是大量请求访问时，正好有**大量缓存过期或者redis宕机**，导致大量数据涌入数据库，数据库遭不住瘫痪了。

解决策略：

1. 过期解决策略：
   1. 给redis各个缓存设置不同的，**均匀的过期时间，**比如给过期时间设置一个随机数
   2. 设**置****互斥锁****，**同一时间只有一个请求去读取数据写入缓存，其他请求要么读取缓存，要么返回空值或默认值。
   3. 不设置缓存的过期时**间，永久有效，**如果过多则设置内存中某些缓存被淘汰。
2. 宕机解决策略
   1. 给**redis配备集群，使用主从复制**。
   2. 进行**服务熔断或者服务限流配置**，宕机后拒绝后续的所有请求，或者限制数据到数据库的访问量，其他的数据在外面等待

### 缓存击穿：

缓存击穿是指，在一些热点突发的情况，比如秒杀活动，大量数据涌入缓存，但是那条热点数据过期了，然后大量数据涌入数据库，导致数据库压力飙升

解决方案：

1. 采用**互斥锁****访问机制，**当一个数据访问时，加锁写入缓存，其他请求等待。保证同一时间只有一个业务线程更新缓存，未能获取互斥锁的请求，要么**等待锁释放后重新读取缓存，要么就返回****空值****或者默认值。**
2. 不给热点数据设置过期时间，或者在即将要过期时，提前通知后台程序更新缓存

### 缓存穿透

大量数据访问时，该数据既未在缓存中找到，也未在数据库中找到，

1. 非法请求限制，如果判断是恶意请求或者不存在，通知后续请求都返回错误，避免访问缓存和数据库
2. 将该热点数据的值在缓存中设置为空值或者默认值，查询该数据时就返回空。
3. *第三种方案，使用布隆过滤器快速判断数据是否存在，避免通过查询数据库来判断数据是否存在。*

# 消息中间件（Message Queue）

### 什么是消息中间件？

消息中间件就是在分布式系统中实现不同组件间异步通信的一个工具。它实现了不同组件，不同指令调用间的解耦。

## 消息中间件的三个主要作用：应用解耦，异步通信，削峰处理

### 应用解耦：

暂时无法在飞书文档外展示此内容

在没有使用中间件的分布式架构中，不同系统之间的远程调用，是直接向应用进行接口请求调用，而这样的情况下，一旦库**存系统宕机故障，订单系统也会由于无法调用阻塞**，而如果中间涉及到更多的其他业务系统的话，相连的所有系统都会调用失**败报错故障宕机**，**引发****雪崩效应****。**

暂时无法在飞书文档外展示此内容

如果引入消息中间件，生产者订单系统只需要将消息发送到MQ，由消费者库存系统自行决定消息的获取时机，实现了**应用间****解耦****，即使发生宕机，**也不会引发连环故障

上述两种方式 涉及到RPC与MQ两个概念：

RPC: 异步调用，及时获得调用结果，具有强一致性结果，关心业务调用处理结果。

消息队列：两次异步RPC调用，将调用内容在队列中进行转储，并选择合适的时机进行投递（错峰流控）

### 异步处理：

在第一种方式中，订单系统需要等待库存系统的返回结果，或者说如下

暂时无法在飞书文档外展示此内容

各个系统之间本来也是有个先后顺序的，在电商场景中，如果用户下单以后，服务器要按流程走完所有的流程，比如先生成订单，掉支付接口，加积分，扣库存，消息通知，全部结束后再返回给用户通知结果，这体验感也太差了，而且效率非常低下，操作间都是同步的，在大量请求到来时，完全处理不过来，而引入MQ后，订单系统将任务加入MQ队列，异步返回给订单系统一个完成操作，订单系统可以去处理其他业务，而其他系统，也可以根据自己的服务情况，选择合适的时机获取信息进行处理。大大提高了效率和可用性。

### 流量削峰

假设一个系统的QPS是2000，在一些情况下有大量请求产生，比如50000个，涌入系统，会承受不住崩掉，引入MQ，可以将所有的请求存入MQ，系统根据处理情况主动的从MQ中拉取请求处理，实现了削峰。

## RocketMQ如何保证消息的顺序性？

无法保证全局有序，只能保证局部有序，将需要顺序执行几组消息存放到同一个RocketMQ中，

## RocketMQ如何保证消息不丢失？

一般来说三种解决方案

1. 生产者确认机制，生产者同步将消息送入队列
2. 消息队列持久化，将消息持久化到磁盘
3. 消费者ACK确认，处理完成消息后消费后确认，先消费后确认（默认先确认，后消费）

## RocketMQ如何实现延时消息？

通过延时队列实现（可以用插件创建延时交换机），或者再RabbitMQ中通过TTL+死信队列实现，延时队列中的消息有个过期时间，比如30分钟后会通过延时交换机送到目标队列

## RocketMQ消息堆积了怎么解决

消息积压，从这几点考虑

1. 查看资源配置是否合理，比如根据业务大致推断消费者端与消息中间件的数量，参数配置等。如果不合理，可以对消费者端开启多线程消费，（前提是保证消息不丢失），或者增加消费者机器数量
2. 或者扩充磁盘容量
3. 如果资源配置已经很合理了，那么可以考虑查看下游消费者代码效率和性能是否出现纰漏了进行调整。

## 消息的幂等性

### 什么是消息的幂等性：

*在编程中一个幂等操作的特点是其任意多次执行所产生的影响均与一次执行的影响相同。*

消息的幂等性是指同样的操作重复执行，结果都是一样的，就是说一条完全一样的消息，它消费一次和消费多次结果都是一样的。**消息的幂等性就是解决消息重复的问题**。比如有操作误判，重发了好几次，不能让他影响结果

### 对于消息中间件的重复消息，如何保证幂等性呢？

一般的解决方案是给消息标识一个唯一的ID，业余处理之前做判断，如果消息已经被处理过，则不会再次对相同ID的消息进行处理。

## 消息的顺序性

消息的顺序性是指在一些特定的业务中，前后的业务操作必须有顺序，否则会出错，如电商系统中，，比如支付要在订单之后，这些有前后逻辑的消息需要按照响应的顺序被处理，不按顺序处理的话，会导致业务操作失败。

对于消**息中间件****的顺序性问题，一般是保证局部有序，**比如有前后业务逻辑的消息做区分，将他们按顺序添加到同一MQ中，以确保消费者按顺序调用消费任务

## 消息可靠性

消息可靠性其实指的就是消息丢失问题。

为了预防消息丢失

## 机器选型

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=Y2E3ODUwZWEwOGQ2Y2FmM2NkZGIwZjEwODRmMTJmM2Jfd0J3WmRUNHR5QlR1Ykt4VmxYYVMyUGtZaU13QmlBMzBfVG9rZW46SmdZZmJpdjJHb2Q1M1h4NkpZVWNWSVRGbklnXzE3MTE5NjIyMTQ6MTcxMTk2NTgxNF9WNA)

# 混淆概念理解

## 同步异步，阻塞非阻塞

最后，再来举一个我们日常的例子来加深对这几个概念的理解。

假设小明需要在网上下载一个软件：

- 如果小明点击下载按钮之后，就一直干瞪着进度条不做其他任何事情直到软件下载完成，这是同步阻塞；
- 如果小明点击下载按钮之后，就一直干瞪着进度条不做其他任何事情直到软件下载完成，但是软件下载完成其实是会「叮」的一声通知的（但小明依然那样干等着），这是异步阻塞；（不常见）
- 如果小明点击下载按钮之后，就去做其他事情了，不过他总需要时不时瞄一眼屏幕看软件是不是下载完成了，这是同步非阻塞；
- 如果小明点击下载按钮之后，就去做其他事情了，软件下载完之后「叮」的一声通知小明，小明再回来继续处理下载完的软件，这是异步非阻塞。

相信看完以上这个案例之后，这几个概念已经能够分辨得很清楚了。

总的来说，**同步和异步关**注的是任务**完成消息通知的机制**，而**阻塞和非阻塞**关注的是等待任务完成时**请求者的状态。**

如果请求者一直在等待消息执行完成，而不去做其他事，就是阻塞，可以在等待任务完成期间进行其他行动，就是非阻塞。如果消息完成自动通知请求者，就是异步，如果需要请求者自己查看是否任务完成，就是同步。

# 知识非孤岛整合区

### 对对象的知识理解串合

暂时无法在飞书文档外展示此内容

# 知识讲解中问题：

### 11.27

阻塞队列：Arraylist阻塞队列 底层用lock锁实现，生产者消费者模式存储 假如大小为5的队列，满了用生产者Condition睡眠，然后消费者消费一个之后就唤醒生产者

synchronizer阻塞队列，不能存储队列        

## 分布式锁的原理：

分布式环境下，只有一个线程能拿到锁。所有有以下几种实现

1. mysql建立一张表，通过唯一索引避免多个机器的线程对相同资源操作
2. Redis setnx 命令语句，只有不存在时才能操作成功
3. zk里面，临时的顺序节点

# 入职相关：

## 投递相关

### 看到简历已读不回：

### 简历筛选：

### 为什么看到许多岗位都是旧需求？

1. 确实一直没招到人，一年多可能，这种概率比较小
   1. 辞退，换人等，说明需要的技术栈之类或者用人要求是没变的。

## 相关问题：

### 跳槽频繁企业最担心什么问题？

担心你的工作能力，专业的深度和宽度，能力有没有匹配到人家用人需求

### 薪资：

1. 校招生不太好谈薪资，是按级划分的，或者小公司可以，或者你有谈资的筹码

### 完整的录用流程：

### 背调：

1. 未离职投递的话，等到所有的流程结束（体检，之前工作经历背调等）再提离职，确保最近这次工作背调没大问题，
2. 背调会查询你最近的三次工作经历
3. 至少说明它至少在招人的，要不连看都不会看
4. 投递占一部分，简历挂上去等被hr联系是第二
5. HR给业务部门推荐10个简历，筛选通过率大概30~40%，然后再通知面试
6. hr回复之后，再把简历交给业务部门前，还可以修改简历，让业务部门看到的不一样
7. 一般一周之内面试情况就能确定下来，一般最多两次筛选，hr筛和业务筛
8. 正常情况下两周一个月内都可以面试结束，甚至一星期快的话
9. offer审批和薪资谈判是两个流程，可能薪资下来了，offer审批开始了，但是审批没过

### 实习生：

1. 从投简历，微信小程序，官网，app，投递
2. 正常情况下不会超过三轮，非常快，可能三四天就完事了

### 校招及其流程：

1. 人比较多，可能会群面（不要紧张，看到比自己厉害的人不要慌，不要紧张，争取发言就可以了，心态稳住，不受别人干扰）
2. 线下面试流程走的比较快
3. 感兴趣的公司列excel表投官网，一个一个投，实习生找实习可以投实习僧
4. boss直聘 智联一般不是校招 ，中小公司可能没有这个渠道，有可能在招聘开放一个接受应届生，变相的校招。

### 职场做事相关相关：

1. 给同事，领导，上司送红包都是没有用的，送些小礼物这种反而更有效一些，
2. 不要给全部门的人买奶茶吃饭这种，太高调，部门就34个人可以考虑

### 试用期问题：

1. 入职一周之内和领导定好试用期目标：这个目标是否在你能力范围内，如果太多，主动讨论，建立一个合理的目标，最好每周汇报一次自己的进度，讲讲自己遇到的困难等，想要申请哪些资源帮助
2. 每次组报，汇报结束，把todo list记下来，记录一下
3. 有机会跟领导吃饭就去吃，吃饭是一个拉进关系非常好的方式
4. 和公司老人（老员工）混的近一点，因为有时领导会参考老人的意见，或者说人干的久，公司的制度运作方式他比较了解，他的一些规范啊啥的就是懂点，和新人搞好关系没事，重心放在老人身上

甚至就是说，领导忘记了你的具体表现，会来找老人问一下你的表现之类的，

1. 双向考察：如果试用期期间察觉到公司的价值观，企业文化之类和自己非常不符，即时止损也是不错的，假如一年下来，忍下来了，技术也没有提升很多，就是荒废了时间

### 如何面试：

1. 自我介绍 
   1. 40s 50s 1分钟以内，简洁明了的将你的过往总结完 具有什么什么的能力
   2. 数字化介绍你的核心工作提炼
   3. 额外的优势，贴合岗位来讲

面试是你来我往，你一句我回一句的交流的过程，不是单方面输出，把握好尺度

hr面试一些考察点：

1. 基本面，情商：谈吐，逻辑，流畅性
2. 自驱力，反映出成长性，是否可以自发的去做一些事情，不需要利润诱惑
3. 抗压性
4. 动机，遇到一些问题时你会做什么决定应对，你的决策判定逻辑是怎样的

业务部门挂你的原因：

觉得你的技术太浅，深度不够，技术不过关，比如让你讲讲最难的项目经历，有没有难点

### 跳槽注意

1. 公司比上家好
2. 业务比上家有提示
3. 

## 面试相关：

### hr不懂专业为什么问项目经历？

比如你做过最难的项目经历？你经历过最大的困难是什么，怎么解决的？等等

1. 查看你的基本面，沟通表达能力，逻辑性，情商
2. 测试你的自驱力，抗压能力，面对压力是怎么处理的，退缩or迎难而上

### 面试准备

1. 自我介绍不用讲过程，讲讲这些年做了什么事，拿了什么结果，具备什么样的能力，自我介绍的时候尽量自然，不要背诵痕迹太明显，顺畅的说完，之后可以说一句，我的自我介绍就到这里，面试官还有什么细节问题要提问吗，我展开讲一下
2. 面试题：面试官一定会问你的项目的难点，最深刻的，最失败的项目（迁移，主要看你是怎么应对压力的）
3. 面试结束之后有没有要提问的问题ng note.md…]()
