最重要的学习思维，将整个知识体系贯穿起来的思维，每多学一个知识，就把它加入自己的学习知识树里，最终将它培养成参天大树，通过一点能一直延伸下去，学到跟底层相关或者过于深入了，感觉到困难了 可以先放一下，清楚的知道自己的知识边界，等回过头来可以再看。但全局思维不能丢，前边学过的就一定让他作为知识家族的一员，不要让学过的知识成为一座孤岛。
思维导图
中红色：模拟面试补充
java基础自测
java基本数据类型
分为数值类型和布尔值类型和字符型 数值类型分整数类型，浮点类型。布尔值类型有true，false1位，整数类型分byte，short，int，long分别1，2，4，8字节。浮点有float，double，4，8字节，字符char2字节
java为什么要设置包装类
包装类可以将基本数据类型封装成对象类型，使其对象化。
提供类型转化支持：包装类提供了可以使基本类型转化为其对应对象类型的方法，以便基本数据类型可以在需要对象类型的地方使用或者对象类型在需要基本类型的地方使用
提供集合支持：基本数据类型不能配合List，Map，Collection使用，而转化成包装类后就可以。
List<Integer> list = new ArrayList<>();
int a = 1;
list.add(a);
可以在泛型中使用：经对象化后可以配合泛型使用
包含一些工具类型：包装类提供一些与基本数据类型相关的工具，可以用来进行基本数据类型的运算，大小比较，等值比较等如
Integer.compare(1,2);
[图片]
扩展：
8种基本数据类型对应8种包装类 除了Integer，Character为全称之外其他都是首字母大写，其父子继承关系为
暂时无法在飞书文档外展示此内容
可以看到上图基本数据类型为数值类型的基础自Number类，而布尔类型和字符类型直接继承自Object类
包装类在jdk1.5之后增加了自动拆装箱机制,即
//可以自动将int类型装箱成对应的Integer包装类
Integer a=1;
//自动拆箱
int b=a+1;
//而在此之前是
Integer a=new Integer(1);
//或者
Integer a=Integer.intValue(1)

而后看下面两组案例
Integer a = 100;
Integer b = 100;
Integer c = new Integer(100);
Integer d = new Integer(100);
System.out.println(a == b);
System.out.println(a == c);
System.out.println(c == d);
System.out.println(a.equals(b));
System.out.println(a.equals(d));
System.out.println(c.equals(d));
/*
true
false
false
true
true
true
*/
Integer a = 200;
Integer b = 200;
Integer c = new Integer(200);
Integer d = new Integer(200);
System.out.println(a == b);
System.out.println(a == c);
System.out.println(c == d);
System.out.println(a.equals(b));
System.out.println(a.equals(d));
System.out.println(c.equals(d));/*
false
false
false
true
true
true
*/
可以看到 equals比较数值都属true显而易见，而new新对象比较是false正常，但是两个案例里，第一第二行两个Integer 常量池比较一个ture一个false是怎么回事呢，经查阅资料源码后我们了解到，Integer的常量池机制是在Integer初始化之前，调用了一个静态内部类方法，将Integer常量池中的值设置为-128~127，如果初始化时，值在这个区间内，则直接从中取值拿出，如果超出这个范围，是以new一个新对象的方式初始化。

String，StringBuilder，StringBuffer的区别
可变性：
String类型是字符串常量，其创建后就存储到常量池里，值不可改变，由final修饰，但是其Stirng对象可以改变，改变的是其指针的指向，当与其他String相加时，添加前的字符串在常量池保持不变，相加后的字符串新添加到常量池里，String对象指向新的String常量。
StringBuidler和StringBuffer则是可变的，属于字符串变量，其创建时的构造方法里填的值如果是常量池里已经存在，如String a=“aaa”，StringBuilder b=new StringBuilder（“aaa”），则只需创建一个StringBuidler对象，并且此对象指向的是常量池aaa的地址，（即与变量a地址值相同，这也意味着想要改变String字符串的值时，需要将其封装成StringBuilder类再去append方法操作），否则需要创建两个对象。并且在合并字符串时，StringBuilder是调用append方法，不创建新对象，在原先对象的基础上直接添加，所以对象地址值是不改变的。
他们的底层都是用字符数组来存储字符串
代码参考：
】
String a = "aaa";
System.out.println("a修改之前的地址为" + a.hashCode());
a = "aaa" + 1;
System.out.println("a修改之后的地址为" + a.hashCode());
String d = "aaa";
System.out.println("d的地址值"+d.hashCode());
StringBuilder b = new StringBuilder("aaa");
System.out.println("b的地址值" + b.hashCode());
b = new StringBuilder(a);
System.out.println("b的地址值" + b.hashCode());
b.append("ccc");
System.out.println("b的地址值" + b.hashCode());
StringBuffer c = new StringBuffer("aaaccc");
System.out.println("c的地址值" + c.hashCode());
c = new StringBuffer(b);
System.out.println("c的地址值" + c.hashCode());
c = c.append(true);
System.out.println("c的地址值" + c.hashCode());
结果：
a修改之前的地址为96321
a修改之后的地址为2986000
d的地址值96321
b的地址值460141958
b的地址值1163157884
b的地址值1163157884
c的地址值1956725890
c的地址值356573597
c的地址值356573597

安全性：
String是线程安全的
StringBuffer是线程安全的，进行的是同步操作，
StringBuffer重写了length()和capacity()、append等方法，在他们的方法上面都有synchronized 关键字实现线程同步
而StringBuilder是线程不安全的，其关键在于StringBuilder底层有两个关键参数，字符数组和count值，当添加新数据时count值要进行判断然后+1这个count+1的操作不是原子性的，多线程添加元素时可能引发线程安全问题所以是不安全的
综上来说在单线程环境下使用StringBuilder效率是最高的，多线程环境下使用StringBuffer来保证线程的安全性

效率：
String的效率最慢，每次改变字符串都要在常量池里创建新的数值（在堆中创建一个新的字符串），然后改变字符串对象的指针指向新的数值，耗时很长，并会浪费内存空间，StringBuilder的效率要比StringBuffer高一些

如何理解面向对象和面向过程
面向对象（object-orentied programing，OOP）是将程序视为对象，以类和对象的概念来组织管理数据和流程，面向对象编程使得代码的复用性提高，功能和模块相互配合，易于维护和管理。
而面向过程编程，程序被视为一系列步骤，注重于代码的逻辑业务，通常程序员需要考虑如何将数据和控制流程组织成一个整体，灵活性更高，效率也比较快，可应对不同需求进行代码更改，维护起来相对麻烦。

面向对象的三大基本特征是什么，如何理解？
封装，继承，多态
封装：
封装是指将书写的代码和逻辑封装保存起来，并对外提供一个自定义的可访问的接口，可保证代码安全性，避免被误改或恶意修改。能够使调用者无须关心代码内部实现，而只专注于属性或方法的调用，并且在修改时修改封装类的代码，无需对所有使用的地方全部修改。实现了良好的安全封装性
继承：
继承是指子类继承父类之后，可以调用父类中实现的功能和方法，并可对父类功能进行重写和功能拓展，实现了代码的可重用性。
多态：
多态是通过继承和接口实现的，多态是指，不同的对象和接口对同一事物产生的不同结果，他是封装和继承产生的自然结果，它使得代码更简洁易读，结构清晰。
父类引用指向子类，多态的显著特点就是对于同一事物，不同的子类对象表现出了不同的行为
多态有三个条件：继承，重写，向上转型

为什么不能用浮点数表示金额？
计算机中的小数不能用二进制精确的表示，存在误差，而浮点数的表示根据IEEE的SME标准，分为标志位，尾数和指数，将二进制较为精确的表示，如：小数0.1用双精度浮点数表示为0.00011001100110011001100110011001100110011001100110011001
而0.2表示为0.00110011001100110011001100110011001100110011001100110011
二者相加转化为十进制为0.30000000000000004 出现了精度偏差
至此解释了如何出现


什么是反射，为什么需要反射？
反射是指在java程序运行过程中，可以动态的创建类的对象，调用对应的方法，属性变量等类属性的机制。
java的编译类型有两种：
静态编译，在javac编译阶段确定类型，绑定对象。
动态编译，在运行时确定类型，绑定对象，动态编译最大程度发挥了java的灵活性
java反射是java被视为动态语言的一个关键性质。这个机制可以让java在运行时通过反射API获取这个类的各种内部信息，包括修饰符，superclass父类，接口，属性方法等，并可以在运行时更改field或者执行method，亦可在运行时通过其获取加载探究一个在编译时完全未知的类，生成对象实体，修改field值，唤起method。
反射允许静态语言在运行时检查，修改数据结构，数据内容，静态语言的数据在编译时都是确定的，如果想要在运行时更改，就需要反射机制。
lambda表达式
Callable callable=new Callable(){
    @override
    public Object call() throws Exception {
        return null;
    }
}

Callable callable=()->{
    
}
lambda表达式本质是作为接口的实现类对象，是一个匿名函数。（这个接口里面只有一个抽象方法）
->箭头操作符，lambda操作符
->左边是lambda形参列表，对应着接口的抽象方法的实现类实现方法的形参
->右边是lambda体，对应实现类要重写的的实现方法体
为什么需要克隆，如何实现对象的克隆？深拷贝和浅拷贝的区别？
java的克隆是指将已存在的对象复制一份，产生一个于原来对象数据结构，属性方法等相同的对象。
java的克隆机制有如下几点好处：
作拷贝避免破坏原对象：有时我们会想对该对象进行改变，而又想保存下当前对象的状态，即可使用克隆创建一个新的对象，对新对象进行操作。
提升性能：在想要创建一个于原对象相同的对象时，使用克隆方法可提高性能，因为以克隆方法创建对象在考虑内存分配，条件判断上开销较小。
封闭细节：如果该对象的创建流程比较复杂，通过克隆可以省去许多复杂的步骤，如参数分配，属性复制，方法调用这些。
要实现对象克隆，需要该对象实现cloneable接口，然后重写父类clone方法，方法体内继承父类clone方法，然后在克隆时，对原对象调用clone方法，返回值为一个新的对象，数据结构与原对象相同，注意：是如果原对象中调用创建引用了其他子类对象，需保证那些对象也实现了克隆接口实现了clone方法，即需保证本类及其子类都实现了cloneable接口重写clone方法

浅拷贝是指创建一个新对象，使其指向被克隆的对象，这样做会使两个对象的指针指向同一块儿内存地址，即修改其中任何一个对象的属性内容时，会使两者均发生改变。
深拷贝是指被克隆的对象及其子对象都实现了cloneable接口与clone方法，而后调用对象的clone方法进行克隆出来的克隆对象。

try-catch-finally语句块中，catch里return了，finally还会执行吗？
首先要知道这个结构是先执行try中语句 如果出现异常则catch捕获处理后，最终在执行finally中的语句
我们观察一下
try {
    int a = 10 / 0;
} catch (RuntimeException e){
    System.out.println("捕获到异常");
    return;
}finally {
    System.out.println("finally语句执行了");
}
/**
捕获到异常
finally语句执行了
/
可以看到，在catch中return后，finally仍然执行了。而经过后续测试，我们发现finally也并不是所有情况都会执行
如：在进入try语句之前抛异常
代码没有进入到tyrcatch语句结构
在catch捕获到语句后，虚拟机中止了
可以看到，除虚拟机中止外，只要代码进入到trycatch语句，finally一定会执行，对于这个结构，finally执行完才会真正退出而非return

String为什么设计成不可变的？
从三个角度考虑
1. 线程安全：设计成不可变的，在多线程访问操作时，由于字符串不可变，就不会使其改变
2. 性能：由于不可变，可以存储频繁使用的字符串，或在一些场景中，让多个字符串对象指向同一个字符串，节省内存开销，简化GC，提高了性能
3. 存储安全：由于不可变，避免了被恶意修改。例如，可以用来存储一些密码。


Error和Exception的区别和联系？以及常见的RuntimeException？
Error异常一般属于系统级别的异常，一般是jvm内部的问题，程序自身无法处理，大多数情况下程序无法继续执行，需要重启jvm或其他紧急处理，不应该被捕获，他代表着系统不稳定，如栈溢出，内存溢出这些。
Exception异常分为已检查异常（checked）和未检查异常（unchecked）后者又称运行时异常RuntimeException，如NullPointerException，下标越界异常等，无需显示捕获，而前者需要显示捕获，如IOException，ClassNotFoundException，如不在程序里try catch或者二throws则会转化为运行时异常
抽象类和接口的区别是什么？
抽象类是一个类，其中可以有普通方法和抽象方法，类中的抽象方法子类必须实现，接口是抽象方法的集合，里面没有实现，（java8开始方法有默认实现）子类必须实现里面的所有方法。
只能继承一个抽象类，但是可以实现多个接口。
接口的方法修饰符必须是pubilc，而抽象方法可以是public，protected，default
设计层面上说，抽象类是对类的抽象，提供了一套模板，而接口是对行为的抽象，定义了一套行为规范。
如果一些类有一些共同的属性和行为，则可以继承一个公共的抽象类，如果只是有一些共同行为，则可以统一继承一个接口规范。例如：人，狗，猫，鱼，鲨鱼都实现具有呼吸功能的接口，但人狗猫继承陆地生物特征的抽象类，而鱼，鲨鱼继承水生生物特征的抽象类，抽象类定义属性与行为，接口规范行为。
抽象类不能被实例化，抽象类的子类如果只实现了父类的一部分抽象方法，那么子类必须选择继承所有抽象方法或者自己也变成抽象类，选择后者，孙子类在继承子类时，要实现子类中未实现的爷爷类的抽象方法，然后孙子类可以直接调用子类的已实现的方法


==和equals的区别？
==如果比较的两个基本数据类型，则是比较他们两个的数值，如果是两个对象则是比较两个对象的内存地址，如果相等说明引用类型指针指向的是同一块内存地址，同一个对象，
equals则只能用来比较对象，==可以比较两个int变量，而这个不能。
equals方法分为两种情况：
1. 如果是Object类底下的equals方法，则和==的用法作用一样。
2. 如果是被类覆盖重写过，则一般用来比较两个对象的值，即内容是否相等。

为什么重写了equals还要重写hashcode？
  如果只重写equals方法，不重写hashCode方法，就有可能导致在 x.equals(y) 表达式成立的条件下，x 和 y 的 hashCode 值却不相同。此时这个只重写了 equals 方法的对象在使用散列集合进行存储的时候，由于散列集合使用 hashCode 来确定 key 的位置，如果存储两个完全相同的对象但是这两个对象有不同的hashCode值，就会出现两个相同的对象储存在散列集合的不同位置，违反了散列集合的规则，也会造成该类对象无法使用散列存储结构。
原文链接：https://blog.csdn.net/weixin_57225558/article/details/128780670
super和this的区别是什么？
1. 指代对象不同：super指向的是父类对象，而this指向本类对象
2. 作用范围不同：this在本类中查找，没有才会去父类调用，而super只能指向父类
3. this可以给sychronize加锁，而super没这个功能
4. 构造方法：super调用父类构造方法，只能在子类中使用，并且要放在第一行，否则编译器会报错。this调用本类构造方法时候，无需要放在方法的第一行。（有待考究）
在子类中，如果没有没有显示的构造构造方法，则在子类初始化时会调用父类的无参构造方法，即super（）,
并且可以调用父类的额属性
public class Father {

    public int age = 12;

    public Father(String s) {
    }

    public Father() {
        System.out.println("im father");
    }
}
class Son extends Father {
    public void field() {
       int a= super.age;
        System.out.println(a);
    }
}
Son son = new Son();
son.field();
//im father
//12
而this调用属性方法时，如果本类中找不到，则会去父类中寻找。
用户线程和守护线程
用户线程，最常用的普通的线程，jvm随它结束而离开
守护线程，jvm不随它，用户线程结束，它随之结束
集合相关：
java中的集合类有哪些？，说说他们的特点？
暂时无法在飞书文档外展示此内容
collections和map map有HashMap，HashTable，LinkedHashMap
collections包括set和list
set有HashSet，LinkedHashSet，TreeSet
List有ArrayList，LinkedList，Vector
Set,Map,List
Set是自动去重的，无序的
LinkedHashSet
应用场景：Zset跳表，LRU（最近最少使用），
Map有key，value键值对，根据key可以查询value，key不可重复，value可，后添加的key可覆盖先前的
List是可重复的，有序的
集合的排序的实现方案
方法一：List的排序用到Collection的sort方法，默认为由小到大，可以传入一个comparator比较器，来自定义排序规则。（比较器的返回值为正或0，o1往前排，为负，o2往前排。
List<Integer> list = new ArrayList<>();
list.add(2);
list.add(54);
list.add(6);
list.add(10);
list.add(3);
list.add(52);
list.add(4);
Collections.sort(list);
Collections.sort(list, new Comparator<Integer>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        return o2-o1;
    }
});
for (Integer o : list) {
    System.out.println(o);
}
/*结果
54
52
10
6
4
3
2
*/
以上是比较Integer类的比较顺序，而对于自定义类，java没有通用的比较，此时可以自定义比较规则如Person类的o1.age-o2.age这种。
方法二：令要进行排序的类实现comparable接口，并实现里面的compareTo方法
public class Person implements Comparable<Person> {
    
    private int age;
    private String name;
    
    @Override
    public int compareTo(Person o) {
        if (this.age != o.age) {
            return this.age - o.age;
        } else
            return this.name.compareTo(o.name);
    }
Person p1 = new Person(10, "xiaoming");
Person p2 = new Person(12, "tom");
Person p3 = new Person(10, "kim");
Person p4 = new Person(16, "faker");
Person p5 = new Person(14, "huni");
Person p6 = new Person(10, "huni");

List<Person> list = new ArrayList<>();
list.add(p1);
list.add(p2);
list.add(p3);
list.add(p4);
list.add(p5);
list.add(p6);
Collections.sort(list);

for (Person person : list) {
    System.out.println(person);
}
/*
Person{age=10, name='huni'}
Person{age=10, name='kim'}
Person{age=10, name='xiaoming'}
Person{age=12, name='tom'}
Person{age=14, name='huni'}
Person{age=16, name='faker'}
*/
只有实现了comparable接口的类才能调用Collection方法进行比较，如Integer这些个基础类型包装类，又如上面提到的Person类,然后调用Collections.sort()方法，就能自动调用重写的comparaTo方法进行排序。
[图片]
[图片]
方法三：通过StreamAPI进行排序
StreamAPI是java8新特性，他是面向cpu针对集合对其做一系列筛选，过滤，排序，遍历，映射等操作，这些操作不改变原集合对象，会返回一个持有新元素的集合。
List<Integer> list = new ArrayList<>();
list.add(2);
list.add(54);
list.add(6);
list.add(10);
list.add(3);
list.add(52);
list.add(4);
list.stream().sorted((o1,o2)->o2-o1).forEach(System.out::println);
//结果为
/*
54
52
10
6
4
3
2
/
可以看到，用法与结果

ArrayList，LinkedList,Vector的区别?
首先三者都是List的实现类，继承AbstractList所以都有有序，可重复等特性，包括迭代器等。
三者有性能，存储结构，线程安全方面的差异，
ArrayList底层是对象数组存储，一次扩容50%作用，线程不安全，性能最好。时间复杂度查询O（1），插入O（N）
而之所以称为动态数组，是因为Arraylist在数组元素超过其容量大，Arraylist可以进行扩容扩容为原来的1.5倍
LinkedList底层是双向链表存储，所以扩容方式区别于另外两位，也是线程不安全的，查询O（n），插入O（1） 
Vector底层是对象数组，每次扩容扩容两倍，是线程安全的，掺杂了锁操作，所以性能要差一些，Vector的 add(E e) 方法添加了 synchronized 关键字以保证数据一致性，但是同时也降低了并发的性能 
ArrayList扩容原理：
初始容量：创建ArrayList对象时，会分配一个初始容量，默认为10。
增长因子：每次扩容时，ArrayList会根据增长因子来计算新的容量。默认情况下，增长因子为1.5，即每次扩容后容量变为原来的1.5倍。
扩容触发条件：当ArrayList的size超过当前容量时，就会触发扩容操作。
扩容策略：ArrayList在扩容时，会创建一个新的更大容量的数组，并将原有元素复制到新数组中。
具体的扩容流程如下：
1. 当向ArrayList添加元素时，会先检查当前容量是否足够。如果不足，则进行扩容操作。
2. 扩容时，根据增长因子计算新的容量，并创建一个新的数组。
3. 将原有数组中的元素复制到新数组中。
4. 更新ArrayList内部的引用，指向新数组。
5. 添加新元素到新数组中。
线程安全的ArrayList--CopyOnWriteArrayList
CopyOnWriteArrayList是1.5后引入，属于JUC的一部分。
他基本的原理还是和ArrayList一样，涉及线程安全的部分，是通过写时复制的方式来实现（从名字中就可以看出）。即读操作是正常的，写操作写时复制/
它内部有个volatile数组来保持数据。在“添加/修改/删除”数据时，会先获取互斥锁，在set方法中使用lock unlock方法给其加锁解锁，将内部代码包起来，新建一个数组，并将更新后的数据拷贝到新建的数组中，最后再将该数组赋值给volatile数组，然后再释放互斥锁。如下：
先将当前容器 array 进行复制一份得到新的容器 newElements，也就是代码中的 Object[] newElements = Arrays.copyOf(elements, len + 1); ；
然后往新的容器 newElements 里面添加元素，newElements[len] = e;；
最后再将原容器的引用指向新容器 ， setArray(newElements); ；
总结：
1. 写时CopyOnWriteArrayList性能较差，且随着数据量的增大，几何级下跌。读操作各方式基本没有区别。
2. CopyOnWriteArrayList，适用于以读为主，读操作远远大于写操作的场景中使用，比如缓存。
3. Collections.synchronizedList则可以用在CopyOnWriteArrayList不适用，但是有需要同步的地方使用， 比如读写操作都比较均匀的地方。
HashMap的重要相关内容
优质文章
hashmap头插法和尾插法区别_一个跟面试官扯皮半个小时的HashMap（看这一篇就足够了）
HashMap，HashTable，ConcurrentHashMap的区别？
三者都实现了Map接口，继承了AbstractMap类。
从存储结构，性能，安全性三个方面分析区别
HashMap，底层采用数组+链表的方式存储数据，在jdk1.8之后加入红黑树，红黑树在满足链表长度达到8并且数组长度大于64时转变，是线程不安全的。初始容量为16，默认扩容因子0.75，扩容为当前的容量的两倍。他的key可以为null且只能有一个
HashMap扩展：
- HashMap在jdk1.7中采用表头插入法，在扩容时会改变链表中元素原本的顺序，以至于在并发场景下导致链表成环的问题。
- 在jdk1.8中采用的是尾部插入法，在扩容时会保持链表元素原本的顺序，就不会出现链表成环的问题了。
HashTable，底层为数组+链表的存储结构，一些关键方法加了锁，因此它是线程安全的，但由于每次加锁减锁的操作，包括扩容的时候耗费的大量资源，效率及其低下，初始容量11，扩容为两倍多1，key和value都不能为null
ConcurrentHashMap，1.8后为数组+链表+红黑树，1.8后采用每个链表加一个锁，是线程安全的额
1.7是给Segment上锁，大数组和小数组，每个segment下面是Entry小数组，而1.8是给桶上锁，一个桶是一个数组指向的那个链表，锁的粒度不同，并且
[x] 待做
HashMap的初始化，put流程，get流程，扩容流程说明？
HashMap首次创建时并不会初始化，而是存在一个默认数组为null，在插入第一条数据时进行初始化，初始化时，如果是空参创建，底层生成一个大小为16的数组，然后插入数据。如果是有参，则容量为阈值，阈值为给定不小于大小参数的最小2的次幂，然后阈值改为loadFactor*容量。
put的时候，HashMap是懒惰创建数组，分这么几种情况吧，
A,如果是这个map首次插入数据，那么要先初始化集合，然后二次哈希后高低16位进行异或，计算出索引，作为该位置的第一个node直接存入，
B,如果不是第一次插入，则计算出索引之后判断插入位置是否已经存在Node值，
a,如果没有，直接存入。
b,如果有的话，看是否是红黑树结构（是否是TreeNode），
1. 是的话走红黑树添加或更新逻辑，
2. 不是的话（Node）走Node的添加或更新逻辑，插入后看是否到达链表长度出发树化逻辑，如果到的树化逻辑前检查是否到达扩容要求，如果是话先进行扩容。
get流程
调用get方法时，根据键值计算要查询数据的hash值，然后找到对应索引，在此索引处遍历所有键值对，找到目标value
扩容流程，谈谈1.8之后，如果是空参构造创建集合，则初始默认大小为16，默认存在一个为null的数组，进行第一次put时改变。如果时有参构造创建，一般这个参数是数组大小，则会将不小于这个正整数的2的次幂的数作为阈值。然后第一次调用put时，将阈值作为数组容量，然后修改阈值为容量*扩容因子。不是第一次扩容则扩容为原来两倍容量。
红黑树机制，链表长度达到8判断数组长度是否达到64，如果没有进行扩容，如果达到了转红黑树


HashMap，HashSet，ArratList是线程安全的吗？
https://blog.csdn.net/zzu_seu/article/details/106669757?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0-106669757-blog-131029879.235^v38^pc_relevant_anti_t3_base&spm=1001.2101.3001.4242.1&utm_relevant_index=3
HashMap 的线程不安全具体体现在以下两个方面
HashMap在1.7中由于头插法会导致在并发扩容时发生死环和数据丢失情况
1.8中在并发put时会发生数据覆盖情况
都是线程不安全的
hashmap的哈希函数相关
hashmap计算下标存放key时，使用了两个方式来减少hash冲突，再哈希法。独特的取模方式
1. 再哈希，拿到key的hashcode值之后，进行高低16位异或，解释：因为hashcode的值为32位，大概有四十亿个映射空间，所以单纯的hashcode散列值是非常分散的，但是问题就在于，内存容量的限制不允许我们创建这么大的hashmap数组，比如初始容量只有16，那我们想要让散列值更加分散，就需要通过二次哈希插入扰动函数的干扰，而这个高低16位异或操作，就起到了这个作用，因为它总的来说，把32hashcode的高16位的特征值，赋予给了低16位，然后在第二步进行位运算取模时，起作用
2. 取模
  hashcode采用数组容量-1与hashcode值进行与操作来取模计算索引下标，因为这时候能最大化的使其分布分散（因为数组容量都是2的n次方，所以-1都是111111全为1的二进制，这时候对其取模，更能使其分散）。
头插法
下面这个文章很好的解释了问题所在
多线程情况下HashMap的头插法和尾插法，为什么尾插法会避免死循环? - 知乎
头插法出现问题的原因在于，在并发的情况下，假如线程1，2同时进行插入操作，发现需要扩容，1在扩容前一步时间片分配结束，2开始进行扩容，1.7之前在有头插法的版本，扩容机制是逆序的，比如在某个节点是ABC，扩容完成为CBA，即B指向A，但是线程1当时在睡觉，它保存的信息还是A指向B，由此造成了循环指向问题。

线程：
创建线程的几种方式：
1. 继承Thread类，重写里面的run方法，然后实例化对象，创建新线程调用start方法，start（）方法将自动调用run方法
2. 实现Runnable接口，重写run方法，实现Thread类将对象实例传进去
3. 实现Callable接口，重写里面的call方法，封装到FaskFuture里面，然后作为new Thread参数封装进去，调用线程的start方法
runable和callable的区别
最大区别是callable有返回值，可以向上抛已检查异常（运行时异常），runnable没有返回值，异常只能在内部处理，也就是无法抛出运行时异常，捕获异常的逻辑只能在run方法内部try catch处理。
这是因为如下原因：首先明确一个概念
方法签名：方法签名是指一个方法唯一标识，包括方法名，参数列表（包括参数个数，类型，顺序）但不包括返回值
而1. runnable接口的run方法方法签名中没有声明抛出异常，也就是下图的throws XXXexception，因为这个原因，导致run方法不能抛异常，也没有返回值。其实现类也也不可以，因为实现类要遵循接口中的方法签名，不能实现比接口定义中更多的受检查异常
[图片]
2.callable接口的call方法throw了异常，可以抛出异常
[图片]



4. 使用线程池创建
线程池抛异常了怎么处理，怎么关闭线程池，关闭后会发生什么，thread和sleep区别（属于哪个类）
join，线程6种状态，阻塞对立的底层实现（是线程安全的吗？怎么实现的）
线程池抛异常了怎么处理
线程池使用Executors，有两种提交任务的方式，submit（）和execute（），对于execute，抛异常后是直接捕获异常，将异常信息打印到控制台，对于submit，需要使用future.get（）方法获取异常返回结果。因为execute里面封装的是实现runnable接口，而submit里面是实现Callable接口
java线程池使用最全详解_java 线程池-CSDN博客
什么是线程池，如何实现的，常见参数有哪些？
线程池是一种可以根据程序的需要，动态的提供线程服务，调度的池化机制，传统多线程开发中，频繁的申请，释放线程，过度消耗资源，甚至无上限的请求资源，线程池机制应运而生。
首先提交一个任务后，线程池会判断当前线程数量是否到达核心线程数，如果没有，则创建一个核心线程执行任务，如果到达，则判断阻塞队列是否已满，如果未满，则将其添加进阻塞队列。如果已满，则判断线程数是否到达最大线程数，如果未达到，则创建一个非核心线程数（去执行新任务），如果达到，则拒绝此任务。
暂时无法在飞书文档外展示此内容
常见有7个参数：
参数名称
参数含义
int corePoolsize
核心线程数
int maximumPoolSize 
最大线程数
long keepAliveTime 
最大存活时间
TimeUnit unit
时间单位
BlockingQueue workQueue
阻塞队列
ThreadFactory threadFactory
 线程创建工厂 
RejectedExecutionHandler handler
拒绝策略
1. 核心线程数：往线程池提交任务时，核心线程数没满就会创建核心线程，满了向阻塞队列添加，默认情况不会回收核心线程。
2. 最大线程数：阻塞队列满后，创建非核心线程数处理任务，超过等待时间后会回收。
3. 最大存活时间：非核心线程的最大空闲时间数，超过这个时间会被线程池回收。
4. 时间单位：最大存活时间的时间单位
5. 阻塞队列：用来保存阻塞任务的队列，有多种数据结构可选择，默认使用的是LinkedBlockingQueue，可选择的有：
LinkedBlockingQueue（基于链表实现的阻塞队列）
ArrayBlockingQueue（基于数组实现的阻塞队列）
SynchronousQueue（只有一个元素的阻塞队列）
PriorityBlockingQueue（实现了优先级的阻塞队列）
DelayQueue（实现了延迟功能的阻塞队列）
A: LinkedBlockingQueue
链表实现,等待队列长度最大为 Integer.Max_Value**
B:ArrayBlockingQueue
可以设置初始队列大小**
C:DelayQueue
按照等待的时间排序，按时间进行任务调 度,本质上是PriorityQueue .实现类要实现Delay接口，传入等待时间，隔多长时间运行
D: PriorityQueue
继承AbstractQueue,二叉树的模型，对添加的元素排序，默认是升序
E: SynchronizedQueue
容量为0，不可以往里面扔东西add，前面等着拿东西的时候才可以装put,传递东西**
F:TransferQueue
里面有transfer方法，装完东西，阻塞，等着东西被取走，才离开
阻塞队列记一下
6. 线程创建工厂：用来创建线程的工厂，默认为Executors.defaultThreadFactory()，也可自定义工厂
7. 拒绝策略：用来应对最大线程数满了，阻塞队列也满了，之后到来的任务的处理，大致有四个策略： AbortPolicy（直接终止，抛出异常）
DiscardPolicy（默默丢弃，不抛出异常）
DiscardOldestPolicy（丢弃队列中最旧的任务，执行当前任务）
CallerRunsPolicy（返回给调用者执行）
电商一般使用AbortPolicy触发日志预警，提示更改线程池大小，或者CallerRunsPolicy方法，总之任务很重要不丢弃。
怎么拿到异步处理的结果
线程同步方式有哪些？
1. 同步方法：使用synchronize修饰方法，包括静态方法，普通方法，语句块等。
2. 同步代码块：在代码中使用synchronize修饰语句块。
3. 使用重入锁ReentrantLock
4. 使用volitile实现线程同步
使线程安全的一些机制，CAS，写时复制技术（在cpu中拷贝几份数据，有线程率先修改完成后，同步到其他线程）

任务添加到线程池内的执行流程
锁：
synchronized怎么使用？加在普通方法和加载静态方法上的区别？
加在方法上修饰方法，或者包裹代码块，后者需要自主传入一个锁对象，来确保只有一个线程访问该资源。
两者的区别在于锁对象不同，加在普通方法上的锁对象是调用当前方法的对象，而静态方法锁对象是所在的类对象（即类的class对象），也就是说访问该方法的只能有一个类，即使是多个不同对象。

Java级别的锁都有哪些？怎么分类？
https://mp.weixin.qq.com/s?search_click_id=17201154027207744462-1682427663102-5696074085&__biz=MzUzMDkxMjUwMg==&mid=2247484010&idx=1&sn=ec5aa8b0d15b69444f3d84bf8719d286&chksm=fa4bc7f3cd3c4ee571aadfc4f0213dc8f815e27ed742bd8f5cf93ef16118829fe4ecac5f1b07&scene=27&key=ce67605b9100af5470489b6ed8c6fb930b5cc7f923c06f0414f462b5c2b0e5371e899064a983e7da43d7142c6b329e588d9b8e81d95a2261b407e1f85cc2ef18ad088db20431fa22654e6754fef97ca397fb4dd97c2e6d6d70575b7d1d567167d03df2ba7a43aeaab94ef710f5dc773420d5007f404ca3b21323c96ac2ec60e9&ascene=0&uin=MjM4MDUwNjkxNQ%3D%3D&devicetype=Windows+11+x64&version=63090217&lang=zh_CN&countrycode=CN&exportkey=n_ChQIAhIQgexx3tHqz2OWCgwHBUaIeBLgAQIE97dBBAEAAAAAAKqJEdux%2Fm4AAAAOpnltbLcz9gKNyK89dVj03KWCCbtDEAu4l33gR1wl9eweYSCtYJnxXI807RLmNzkrAXGvdmR9%2B%2BNkHYqjQafhxwE84h%2BGVL5BCCtL%2FVkoVFwRolvn1afpnlCoNupzJsgURHvoqNN8vde1OdyCtIckhXkSXoZHCBrgsxMSRyziXXtYJHvnfSvvyt3NDWujiD0N9JisZYaClPklodBVCyIbgYKTpnNuHQwzSetRnhYqdIxa89GI3Ed8sod3Y9S%2FEf1XFHfCyHZfYxMq&acctmode=0&pass_ticket=rHJ%2FNgRSZFHS%2F79aUh6axyQvFbBdrD77B1JgSLr56BKFcWiEyJpErTOmzt1s1Kbm9KafT22yegAspU%2F031GcmA%3D%3D&wx_header=1 
这个分类记载的非常全面
1. 隐式锁（内置锁）：synchronized锁，可以自动加锁解锁
2. 显示锁：lock及其子类，需要手动上锁解锁
3. 公平锁：锁的获取顺序与线程等待顺序一致
4. 非公平锁：锁的获取顺序与等待顺序不完全一致
5. 可重入锁：同一线程可多次获取同一把锁
6. 读写锁：它允许多个线程读同一把锁，但只允许一个线程写
7. 互斥锁：对于一个共享资源，它只允许一个线程拿到这把锁从而保证只有一个线程访问资源
8. 信号量：信号量是一种同步工具，它用来控制对共享资源的访问，限定了共享资源的同一时刻的最大访问量
9. 悲观锁：一个线程获取到锁之后，不管是否有其他线程在竞争锁，都会将锁对象上锁，（即操作之前先上锁）阻止其他线程访问。 悲观锁是一种悲观思想，即认为写多读少，认为遇到并发写的可能性高，每次拿数据的时候都认为其他线程会修改，所以每次
10. 乐观锁：两个线程可以同时修改一个变量，但是在进行最后一步将工作内存的值放入主内存前，进行一次CAS操作，（注：CAS是一种思想，比较后交换）如果值不一样就不交换。可以同时进行读操作注意体会悲观锁乐观锁的区别 
乐观锁悲观锁有哪些，原子类相关 （有ABA问题吗）ABA什么场景下需要解决，什么场景不需要
AQS底层基于什么实现的
并发量大的情况下乐观锁的一直尝试 占用大量资源会造成cpu标高
11. 轻量级锁
12. 重量级锁
java中级工程师面试自测
什么情况下需要对对象进行序列化？
序列化是指将java对象转化为字节流的过程，序列化后可以通过字节流进行传输。通常在以下情况需要序列化
1. 数据库存储：如果需要将java对象存储到数据库或者文件系统里，则需要将对象数组序列化后传入，如果需要在用到对象，可以从数据库反序列化获得
2. 在分布式平台中，需要用到共享对象的话，需要将对象序列化，输送字节流给多个部件
3. 在网络传输中，对象需要以序列化后的字节流传输
4. 缓存机制：在某些情况下，我们需要将对象序列化到缓存中以提高效率，需要时从缓存中获取

什么是AIO,BIO和NIO？
他们三个都是通信架构模型，是用来保证更好的通信的。
异步：线程告知内核需要把数据从socket读到用户空间，线程不参与这个过程，读完了通知线程一声，线程再继续参与接下来的工作
同步：线程参与内核读数据到用户空间，这个过程线程是参与的，区别于异步，属于同步
主要差异在于是否线程参与了整个全部过程
阻塞：线程在队列中等待执行任务
非阻塞：在任务到来之前，线程执行其他工作，时不时来看一眼
差异在于线程在读到自己想读的数据之前是否一直在排队等待
100%弄明白5种IO模型
[图片]
其中BIO是最早诞生的，是同步阻塞的，服务实现模式是每一个客户端，每一条连接都启动一个线程，即客户端有连接请求时服务端就需要启动一个线程。如果连接空闲没有发出操作，则线程阻塞。
NIO经过了改进，同步非阻塞的，服务器实现模式是一个请求一个连接，客户端发送的请求注入到多路复用器结，轮询到连接有请求时，启动线程对他进行操作。缺点：轮询时开销比较大 
AIO基于NIO，相当于NIO2.0，是异步非阻塞，它对于每一个有效请求建立一个连接，当os完成基本操作后才启用线程，并且读写是异步的。

锁
synchronized锁升级过程是怎样的？
锁刚创建出来没有被线程占用时是无锁状态，当有一个线程申请过锁之后，升级成偏向锁，当这个锁被多个线程申请占用过后，就属于轻量级锁，当发生锁的争用，自旋结束也没有获得锁而导致有线程进入阻塞状态，那就是重量级锁了。
补充：
重量级锁的实现：当锁升级为重量级锁时，有一个指向对象监视器的指针，就是monitorObject，monitor对象，里面有三个关键属性，owner，Watiset，entryList，当线程发现owner指针里面为null通过，CAS操作尝试获取锁，获取成功将owner指向自己线程（里面存储的是线程id），然后其他等待获取锁的线程被阻塞在entryList队列中，它是一个有序的先进先出的队列（链表实现），如果线程调用了wait方法，会进入waitset等待队列，有notify时，随机从等待队列唤醒一个线程到阻塞队列队尾，notifyall时，唤醒所有的等待线程到阻塞队列。注：synchoronized是非公平锁，锁来之前要先进行一次CAS获取锁，然后再去排队。
synchronized锁优化是怎样的？
一般有三个优化
1. 锁清除：在JVM的JIT（即使编译）阶段，如果判断此处是不可能发生共享资源竞争的锁，不需要加锁，则会编译时自动将锁操作移除。
例如：StringBuffer的append方法是一个同步方法，如果StringBuffer的变量是一个局部变量，在局部变量里加锁，因为局部变量运算时，本身就只有自身线程能参与，再加锁就会多此一举，影响性能，jvm会自动把append上的锁移除。
2. 锁粗化：将多个连续解锁加锁的地方，jvm会根据代码结构，在合适的地方把锁范围扩张，：如for循环中，每次循环加一次锁，jvm会将加锁的操作，扩张到包裹for循环，这样就和原来一样的效果，也节省加锁解锁的流程
3. 锁升级期间的锁优化：
  1. 偏向锁的申请：在申请锁之前，查看锁对象的markword偏向标识位，如果为0，说明是无锁状态，进行CAS申请锁，如果CAS申请成功，则获取锁，将锁标识位置1，id字段指向当前线程
    1. 如果为1，则说明是偏向锁，此时申请获取锁，查看锁的线程ID（threadID）是如果锁线程ID对应上了，直接获取资源，无需做同步操作。
      1. 如果threadID不对，则查看当前锁对象指向的线程ID对应线程是否存活
        1. 不存活，则将锁状态设置为无锁状态，当前线程通过竞争尝试获取锁资源
        2. 存活，则查看线程ID对应线程的栈帧信息是否仍需要使用这把锁，如果不需要，锁对象被重置为无锁状态，新线程可以竞争该锁
          1. 如果需要，撤销偏向锁，升级为轻量级锁。
  2. 轻量级锁：此状态下，想要获取锁的线程进行CAS自旋操作，进行一定次数的呃呃自旋操作还没有获取到资源时，升级为中重量级锁状态，其他线程进入阻塞

synchronized和reentrantLock的区别？
1. 用法不同：synchronized是修饰方法，代码块，而reentrantLock只能用来修饰代码块
2. 获取锁释放锁机制不同：synchronized是自动加锁，解锁，reentrantLock需要手动加锁解锁。
3. 锁类型不同：synchronized是非公平锁，reentrantLock既可以非公平锁，是公平锁。
4. 响应中断不同：synchronized不支持响应中断，进入死锁无法处理，reentrantLock支持响应中断，解决死锁问题。
5. 底层实现不同：synchronized底层是JVM实现的，reentrantLock底层是AQS类。

并发：
volatile能保证原子性吗？为什么？
跳转JMM
不能，volatile能保证内存可见性，和禁止重排序，它是通过对 变量添加volatile关键字修饰，使线程对此变量进行改变后，通过总线嗅探机制告诉其他线程，此变量已被我修改了，来保证内存可见性。
但原子性是指，一个或多个操作，要么都执行，要么都不执行，且一旦执行，就不能被外部打断，通过这个底层功能我们发现呢，volatile并不能保证原子性。
禁止重排序是指被volatile修饰的变量禁止被重排序
了解总线的嗅探机制和MESI协议，禁止重排序的底层实现机制
volatile关键字理解如下图
暂时无法在飞书文档外展示此内容
[图片]
总线嗅探机制：
它是一个在硬件层面实现的机制，它规定处理器在修改数据时，一旦数据更新完成，就通过总线嗅探机制的监视总线告诉其他处理器，并使其他处理器里的值失效，需要从新读取，用来保证缓存一致性。具体的是，当某个处理器要写入内存位置时，通过总线嗅探机制检测其他处理器是否有从同样内存位置读取值的，如果是同一内存位置，则令这些处理器的数据失效，并从主内存中重新读取值，以此来保证处理器缓存与内存间的缓存一致性 如下图：
暂时无法在飞书文档外展示此内容
缓存一致性与总线嗅探机制的关系：
缓存一致性是指—————当多个处理器的缓存中存在共享变量时，必须保证存有共享变量的处理器缓存的数据都是相等的，而这个要求，就是通过总线嗅探机制实现的！
缓存一致性协议
如MESI：它定义了四种状态：Modified（已修改）、Exclusive（独占）、Shared（共享）、Invalid（无效）。每个缓存行都有一个状态，处理器根据操作对缓存行状态进行切换。
如何保证原子性的呢？
在java中，原子类是通过CAS+volatile关键字实现原子性的。CAS保证每次提交数据时，都没有其他线程已经提交，volatile保证每次要修改的数据，都是最新值。
JUC并发包常用的工具类，分别有什么特性？适用的场景？
1. ReentrantLock（可重入锁），Lock是锁接口规范，常见api有lock，unlock，lockInterrupt，tryLock（），tryLock（long， Timeunit）等，他是一个可重入锁，支持公平锁（通过传入参数更改，默认是非公平锁），支持响应中断，可以通过tryLock方法尝试获取锁，并可设置尝试时间。
  1. 可以用来解决多线程竞争资源，因为是独占锁，可确保同一时间只有一个线程访问资源。例如多个线程对数据库操作可保证同时只有一个线程写入。
  2. 可以实现多线程任务的顺序执行，一个线程执行完通知另一个线程执行任务
  3. 实现多线程等待，通知机制，某个线程完成任务后通知其他线程继续执行
    1. 场景：多线程抢票
2. Conditionl类，这个类可以精确的唤醒或者使某个线程睡眠，通过于ReentrantLock配合使用，（锁对象.newCondition如：lock.newCondition实例化）可以完成一些交替执行的任务，如生产者消费者场景。
3. Semphore（信号量机制），Semphore维护了一个计数器，线程可以通过acquire（）方法来获取Semphore中的许可证，然后通过release（）方法释放许可证，来使得计数器+1，如果计数器为0，调用acquire方法的线程会被阻塞，直到有其他线程release，然后通过设置许可数量上限，来限制线程访问数量。
  可以用来做api访问限流。
4. CountDownLatch（倒计数门闩）：概念：一个线程等待其他全部线程执行完时，它才继续执行。 通过定义一个参数，然后在想要停止的地方，调用await方法，每有一个线程执行完，就调用countDown方法，使得计数器减一，当减到0时，await方法那个地方放行，代码继续执行。
  使用场景：
    1. 并行任务同步：用于协调多个并行任务执行，确保全部任务完成后再继续执行下一步任务。
    2.  多任务汇总：当多个线程数据需要集中统计时。
    3. 初始化操作：当需要初始化时，调用该功能，初始化完成后再执行
5. CyclicBarrier（循环屏障）
概念：和CountDownLatch效果类似，实现让一组线程等待至某个状态（屏障点）后再执行后面的逻辑，叫回环是因为等待所有线程都被释放后，CyclicBarrier可以被重用。
每次调用await方法都会使count减一，当count减到0时，执行设置的barrierAction方法，count值重置
场景：
  1. 适用于多线程任务：将一个复杂任务分成几个线程执行，大家都执行完之后，再执行下一个。
  2. 数据处理：可以用于协调多个线程间的数据处理，在所有线程处理完数据后触发后续操作
CountDownLatch        
CyclicBarrier
减计数方式 
加计数方式
计数为0时释放调用wait等待的线程  
计数达到指定值释放所有等待线程
计数为0，无法重置
计数为指定值，计数置为0重新开始
调用countDown()方法计数减一，调用await()方法只进行阻塞，对计数没有任何影响
调用await()方法计数加一，若加一后的值不等于构造方法的值，则线程阻塞
不可重复利用
可重复利用
参与的线程的职责是不一样的，有的在倒计时，有的在等待倒计时结束
 参与的线程职责是一样的
倒计数门闩和循环屏障应用区别在于，倒计数门闩是所有线程执行调用countDown（）方法，然后主方法也就是调用latch.wait（）方法的线程才能继续执行，而循环屏障是线程执行到一定程度就wait，所有线程都执行完wait，大家所有线程再继续执行，适合多线程并行执行任务。
ConcurrentHashMap在哪些地方做了并发控制，保证线程安全？
1、ConcurrentHashMap在JDK 1.7中底层使用的数组 加 链表的结构，其中数组分为两类，大树组Segment（ 固定大小为16，在数组扩容时它仍旧为16）和 小数组 HashEntry，而加锁是通过给Segment添加ReentrantLock重入锁来保证线程安全的，锁整个segment，粒度大。
2、ConcurrentHashMap在JDK1.8中底层结构使用的是数组 加 链表 加 红黑树的方式实现，它是通过 CAS（put时如果是map第一次初始化或者该数组第一个节点则CAS添加） 加 synchronized （put时节点非空则加synchronized锁操作） 来保证线程安全的，只对Node节点上锁，并且缩小了锁的粒度，查询性能也更高。1.8之后只有并发操作时才会上锁。
为什么1.8之后synchronized本身包含cas机制，还要引入cas
1. 细粒度控制： synchronized 关键字在锁升级时是对整个对象进行锁定的，而 CAS 允许对某个变量进行原子性的比较和更新。这使得在 ConcurrentHashMap 中可以更精确地控制锁的范围，而不必锁住整个数据结构。
2. 减小锁的持有时间： CAS 允许在不阻塞线程的情况下进行尝试性的更新，如果失败则可以重新尝试。这减小了锁的持有时间，减轻了线程争用的压力。相比之下，synchronized 在锁升级时需要阻塞其他线程，而 CAS 可以通过循环自旋的方式尝试获取锁，避免了线程阻塞的开销。
3. 适应性： CAS 允许在并发量较低的情况下避免使用重量级的锁，提高了性能。当并发冲突较少时，CAS 可以更高效地完成操作。
4. 无锁算法： CAS 是一种乐观锁的实现，它不需要线程阻塞，而是通过自旋的方式进行尝试。这种无锁算法对于一些高度竞争的场景更具优势。
虽然 synchronized 也包含 CAS 操作，但是 ConcurrentHashMap 使用 CAS 的目的是为了更灵活、更高效地实现并发控制，以适应高并发场景的要求。CAS 提供了更细致的控制和更轻量级的同步机制，使得在高度并发的情况下能够更好地发挥并发性能。

线程安全的hashMap还有HashTable，它是和vector一样，对在添加方法上加synchronized关键字。
SimpleDateFormat线程安全性？（SDF）
在多线程环境下，它不是线程安全的。是因为它底层存储时间的方式是将时间赋值给一个成员变量calendar，然后SDF是static修饰的，那么SDF就是一个共享变量，他的calendar可以被多个线程访问到。

AQS和CAS分别是什么，怎么理解？
AQS其实就是一个类（AbstractQueueSynchronizer），队列同步器，AQS是用来构建锁和其他同步组件的基础框架，包括并发三大工具类（CyclicBarrier循环屏障，CountDownLatch，Semphore）的基础，ReentranceLock，甚至BlockingQueue也是基于它实现。
暂时无法在飞书文档外展示此内容
AQS的底层实现：
公平锁和非公平锁的区别
State CAS 先入先出阻塞队列 线程怎么插入阻塞队列的
 学习搭子
CAS的ABA问题，看似对值好像没什么影响，但是在我们去做一些日志，记录的时候，可能就会产生影响了，影响到业务的真实性
reentrantLock和redis锁的区别
reentrylock和redis锁实现的最大区别是什么
ChatGPT
ReentrantLock 是 Java 中的一个可重入锁实现，而 Redis 锁通常是通过 Redis 数据库实现的分布式锁。它们之间有一些重要的区别：
1. 本地锁 vs 分布式锁：
  - ReentrantLock 是一个本地锁，它只在单个 JVM（Java虚拟机）内有效。多个线程在同一个 JVM 中使用 ReentrantLock 时，它们之间可以共享锁状态，即可重入性。
  - Redis 锁是一种分布式锁，它可以在多个不同的进程或服务器之间起作用。这是通过在 Redis 数据库中存储锁信息，并利用 Redis 的原子性操作来确保分布式环境中的锁的正确性。
2. 锁的粒度：
  - ReentrantLock 通常是用于控制对共享资源的访问，可以在任何范围内使用，从方法范围到代码块范围。
  - Redis 锁通常是以分布式系统的资源为单位，通过在 Redis 中存储锁信息来协调各个节点之间的锁状态。
3. 性能和开销：
  - ReentrantLock 是基于本地内存的锁，因此在同一 JVM 中的性能通常比分布式锁要高。
  - Redis 锁涉及到网络通信和远程服务器的操作，相对而言，可能有更大的开销和延迟。
4. 锁的可用性：
  - ReentrantLock 的可用性仅限于单个 JVM，如果 JVM 关闭或崩溃，锁状态也会随之失效。
  - Redis 锁的可用性更强，可以在多个 JVM 或服务器之间共享，因此在一个节点宕机时，其他节点仍然可以继续使用锁。
5. 锁的实现原理：
  - ReentrantLock 的实现通常依赖于底层的同步器，例如 AbstractQueuedSynchronizer，而其内部使用了 synchronized 关键字。
  - Redis 锁的实现通常利用 Redis 提供的事务和原子性操作，例如 SETNX（set if not exists）。
在选择使用 ReentrantLock 还是 Redis 锁时，需要根据具体的需求和应用场景来决定。如果是在单一 JVM 环境中，而且对性能要求较高，可以选择使用 ReentrantLock。如果涉及到分布式环境，并需要实现分布式锁，那么 Redis 锁是更为合适的选择。
java高级工程师面试自测
集合：
HashMap如何进行扩容的？里面的各种参数为什么这么设置？
https://blog.csdn.net/yunzhaji3762/article/details/130847218
HashMap第一次初始化时，先扩容成16或者指定参数大小的数组，然后再插入数据。当插入数据到阈值（容量*扩容因子）时，先插入数据，再扩容。
0.75是因为小了扩容太频繁，影响性能，大了hash冲突多，链表越来越长，影响效率。0.75是一个折中值 
table.length=2^n数组大小为二的n次方是为了利用位运算求key下标
因为2的n次方-1的二进制数字都是11111，而将hash值与这个做位运算时，可以很容易的求出一个分布平均的key下标值，效率高，而且分散举个例子
101100101110一堆32位hash与全为1的额数做hash能够充分的散列，如果与1001 或者0000那就更完了
所以总结就是两点
1. 位运算计算是最高效的，比%这种高效的多
2. 能够最大程度的对hash做散列
ConcurrentHashmap1.8为什么取消了使用ReentrantLock锁？
使用sychronized而不是ReentrantLock有这么两个优点：
减少内存开销：synchronized是jvm层面实现的，而ReentrantLock是节点通过继承AQS来获得同步支持，1.8只有头节点需要进行同步.内部优化：synchronized在jvm层面实现，内部自动进行锁优化如锁消除，锁粗化，锁自旋。
HashMap是如何解决Hash冲突的？解决Hash冲突都有哪些方案？
链表法+红黑树，红黑树是链表长度大于8的地方转化为红黑树存储结构，提高查询效率
1. 开放定址法，也叫线性探测法，在发生hash冲突的那个位置附近找一个空位，把数据存入
2. 链式寻址法，链表法：在冲突位置生成一个链表，用来存放相同hash地址的数据
3. 再hash法，对于发生hash冲突的地方，使用另一个hash算法将它再次hash，得到新地址
4. 建立公共移除区：把Hash表分为基本表和益处表两个部分，凡是存在冲突的元素，一律放到益处表中
HashMap和ArrayList的区别，以及他们的key是否可重复/为空？
相同点：
1. 都可以存储null值
2. 线程不安全，不同步
3. size（）方法获取元素个数
不同点：
1. 存储元素：HashMap是存放键值对，元素无序，键不可重复，值可重复，后存放的可覆盖先前的。ArrayList是存放同一类型的元素，值可重复，是有序的。
2. 实现上：HashMap实现Map接口，ArrayList实现List接口。
3. 前者底层是数组+链表+红黑树，后者底层是数组对象
4. 默认大小和扩容：ArrayList默认大小为10，每次扩容增加0.5倍+1如10第一次后为16
HashMap默认大小16，每次扩容为原来两倍大小，如第一次扩容完成后为32
5. 添加元素方法，HashMap是put，ArrayList是add方法
使用场景上：
如果需要随机快速访问元素，使用ArrayList，需要键值对查询则HashMap
线程
平时使用过线程池吗？你应该注意哪些问题？
定时任务将任务放到线程池中去执行，xxl-job相关这块代码的流程
1. 创建一个线程任务 实现runnable接口，run方法里写要实现的业务逻辑（如每天几点发货订货统计金额 等）
2. 创建一个job类，在要进行使用线程池调用任务逻辑的方法上 加上XxlJob注解，括号内为任务名（xxl-job调度中心需要通过这个定位到这个方法）
3. 
使用过，注意线程池的使用是否能提高效率，然后就是结合应用选择适合的线程参数。核心线程数和最大线程数的设置，拒绝策略的选择，阻塞队列的选择。
1. 死锁问题：线程池的死锁情况体现在所有执行线程都在等队列中某一个线程的执行结果，导致线程无法继续执行
2. 线程泄露：如果一个线程在执行时，结果没有正确返回，则可能造成线程丢失即线程泄露问题。具体体现在，如：线程执行时发生异常，线程池没有正确捕获并处理该异常，该线程就会从线程池中移除丢失，这样的情况假如出现多次，最中线程池就会变空，没有线程可用于执行其他任务
3. 线程频繁切换：线程池很大时，线程上下文频繁切换会占用很多资源，并不是越大越好
线程池大小优化：线程池最佳大小取决于可用处理器数量和任务性质，对于cpu密集型任务（耗用cpu时间长），对于N个逻辑处理器，线程池最大线程数量为N或者N+1将实现最大效率。对于I/O密集型任务，需要考虑等待时间W和任务处理时间S的比例，线程最大大小为N*（1+W/S）会实现最高效率。注：以上要根据实际情况自行判断。
业务场景：假如白天访问量大，夜间访问量小，该如何配置线程池？
白天访问量大，夜间访问量小，那就让线程池在白天时核心线程多一些，晚上核心线程少一些。白天等待队列弄的稍微多一些，设置个几百这样子，如果白天的高并发时间段比较集中的话，等待时间设置的就长一些，如果是多个高峰间接到来，就是中间隔一段时间有这么一次高峰，等待时间就设置短一些。具体的话还要根据自己的业务场景，模拟线上的环境做压测，通过业务场景测试，得到一个相对准确的值
如何设置核心参数呢？
根据cpu逻辑核心数，如果是cpu密集型，那就设置N或者N+1就好了。如果是I/O密集型，没有特别准确的一个值，根据公式N*（1+w/s）大概两倍核心数。
IO密集型难以确认最佳值的原因是IO在每一台机器，每一次请求所占用的资源和要求都是不确定的，受网络波动等的影响
线程池的等待队列有哪几种实现方式？
BlockingQueue通常有三种策略：
直接提交：
工作队列默认选项是SynchronousQueue，将任务直接提交给线程，如果不存在可用于直接执行任务的线程，则试图把任务加入队列将会失败，然后创建一个新的线程执行，直接提交通常要求无界maximumPoolSizes以避免拒绝新提交的任务。
无界队列：
使用无界队列（如没有预定义容量采用默认方式的LinkedBlockingQueue）会导致所有corePoolSize线程在无空闲时新任务全都添加到队列中等待，而永远不创建新线程，直到OOM。总之，创建的线程数不会超过corePoolSize，（因此maximumPoolSize的值就无效了）。因此，当每个任务都独立执行，互不影响时，适合于无界队列
如：在 Web页服务器中。这种排队可用于处理瞬态突发请求，当命令以超过队列所能处理的平均数连续到达时，此策略允许无界线程具有增长的可能性
有界队列：
当使用有限的maximumPoolSize时，有界队列（如ArrayBlockingQueue）有助于防止资源耗尽，就是咱们之前最早学的那个流程，采用拒绝策略的做法。先核心再队列再最大，再拒绝

java的四种引用，强弱软虚的区别？
1. 强引用：通过在堆中创建对象，栈中创建指向堆对象的引用，这样的方式就是强引用。有如下特点
- 强引用可以直接访问目标对象
- 强引用指向的对象在任何时候都不会被内存回收，jvm宁愿抛OOM异常，也不会回收。
- 强引用可能造成内存泄露
  解释：1. 内存溢出是指在程序申请内存空间时，没有多余的栈空间供使用而造成内容栈溢出
  2.内存泄漏是指已申请的内存无法得到释放，这样的情况积少成多之后会造成内存溢出
2. 软引用：软引用类型的包为java.lang.ref.SoftReference，软引用指向的对象不会被立即GC，而是当堆内存空间达到阈值时才会被GC 
3. 弱引用：弱引用生命周期较短，因为它一旦被垃圾回收器发现，就会被GC，但是由于GC线程的优先级比较低，那些只具有弱引用的对象不一定很快被回收
：弱引用和软引用都很适合来保存哪些可有可无的缓存数据，当堆内存充裕时，可以释放来补充空间，当堆空间空闲时，可以用来提速。
4. 虚引用：虚引用不能决定对象的生命周期，任何时候只有虚引用的对象都有可能被GC回收，所以虚引用对象通常用来追踪对象的回收，清理被销毁对象的相关资源。它一般必须与引用队列配合使用，引用队列是用来存储被销毁对象的引用的（软，弱，虚引用），用来进行虚引用对象相关的资源释放，日志记录清除这些。
虚引用就是相当于一个追踪记录，追踪被虚引用关联的对象，它不能决定该对象什么时候被回收或者回收逻辑（不像前边强引用关联对象永远不会被回收，弱引用关联对象内存不足时回收），虚引用关联的对象可能随时会被回收，被回收时虚引用会被添加到一个引用队列中，通过引用队列我们可以对被销毁对象相关联的其他内容比如资源释放，日志清除等。
锁
为什么说CAS是乐观锁？底层对应哪个类？
【并发基础】CAS(Compare And Swap)操作的底层原理以及应用详解_unsafe compareandswap最终还是要加锁-CSDN博客
补充：CAS缺点
1. ABA问题，采用标识位如版本控制解决
2. 循环问题，一直重试造成cpu资源消耗
3. 只能操作一个共享变量，CAS多个线程修改只能保证对一个共享变量的原子操作。
那我们首先来分析一下乐观锁的定义，乐观锁是一种思想，每个线程操作前假设不会发生并发修改冲突，所以在数据进行提交更新的时候，才会正式对数据冲突与否进行检测，发生冲突则返回错误的信息，让用户决定如何去做。所以其实乐观锁思想就强调两件事，冲突检测和数据更新。
CAS机制实际上就是乐观锁思想的一种实现，当多个线程尝试使用CAS更改同一个共享变量时，只有一个线程可以成功，其他的线程也不会被立即挂起，可以进行重试，它本身包含三个操作数，内存值V，预期值A，修改值B，如果VA不同则不修改，相同则将B更新到V，这本身就是冲突检测+数据更新的一个机制。
CAS底层是unsafe类,Unsafe 是 CAS 的核心类， Java 无法直接访问底层操作系统，而是通过本地（ native ）方法来访问。不过尽管如此， JVM 还是开了一个后门： Unsafe ，它提供了硬件级别的原子操作， 这里的 compareAndSwapInt () 就是 Unsafe 类提供的硬件原子操作之一
暂时无法在飞书文档外展示此内容
ThreadLocal使用过吗？底层原理是什么？使用时应该注意什么？有什么问题？
threadLocal的作用：提供线程内的局部变量，不同线程之间不会互相干扰，这种变量在线程生命周期内起作用，减少同一个线程内多个函数或组件间一些公共变量传递的复杂度。提炼一下就是：
1. 线程并发：多线程并发场景下
2. 传递数据：可以通过ThreadLocal在同一线程下的不同组件中传递公共变量（有点类似web中的域对象）
3. 线程隔离：多个线程之间不会互相影响变量的值
做练习时使用过
ThreadLocal底层涉及到threadLocalMap和thread类的一些实现 每个thread对象都有一个threadLocalMap对象，他是一个自定义哈希表，用于存储线程局部变量，这个threadLocalMap类的key键是threadLocal对象，值是设置的value，threadLocal通过ThreadLocal.set（T value）方法将键和值设置到threadLocalMap中，当调用ThreadLocal.get()方法时，获取与当前ThreadLocal对象关联的值
threadLocal对象的key是弱引用，容易发生内存泄漏（是因为每个线程都会被分配一个线程局部变量副本，它在线程结束时不会消失，需要手动remove删除）
JMM内存模型是什么？为什么需要JMM内存模型？
JMM内存模型是java编程语言中定义的一种规范，它定义了多线程下访问共享内存的规则（重点），确保了多线程程序在不同的计算机体系和操作系统中都能表现出一致的行为。它主要解决了可能由多线程并发引起的可见性，有序性和原子性问题。
1. 主内存：是所有线程共享的一块儿内存区域，线程从这里取数据，并将数据更新到此处
2. 工作内存（本地内存）：线程私有的内存区域，线程将数据读取到工作内存，并再次进行修改
3. 可见性：当一个线程修改一个共享变量后，其他线程能立即看到这个变化。
4. 有序性：编译器和处理器可能对指令进行重排序以提高性能，但在多线程环境下可能导致意外行为，JMM通过禁止一定范围内的指令重排序来保障线程间的一致性
5. 原子性：对共享变量的读写操作必须是原子的，即读或写操作不能被打断，JMM通过synchronized或者JUC包中的原子类保证原子性。
happens-before是多线程编程的一个重要概念，用于描述事件间的顺序关系。在java中，happens-before关系规定了一个线程的操作对其他线程操作的可见性和顺序性的影响，它规定，A happens-beforeB的话，A的操作对B一定时可见的，在JMM中，如果要求A操作的执行结果需要对B操作可见，则必须要存在A happens-beforeB关系
跳转volatile

标记：还未搞懂
暂时无法在飞书文档外展示此内容
工作中经常使用
HashMap，ArrayList的使用
Stream流常见分组，筛选，生成Map等
日期格式化以及对应加减，比较等
反射，写通用方法时偶尔会使用
拦截器，项目起始阶段会全新搭建，后面就是在原来基础上进行修改，例如拦截异常统一处理，设置非白名单等
高并发场景自测
并发和并行的区别？
线程和进程的区别
什么是线程安全性？如何保证线程安全？
java中实现并发的方式有哪些？常见的并发工具类有哪些？适用场景？
线程池的原理和实现，执行流程，常见参数等？