Decent实训笔记
java基础篇 
javase与javaee：
cloud.tencent.com 偏向面试
JAVASE、JAVAEE、JAVAME三者的区别和知识体系_javaee,se,me可以转换吗?-CSDN博客 详情
idea开发工具
概述：
idea架构大体分为四层project（工程，项目）-module（模块）-package（包）-class类，从project开始 可以包含多个对应子模块。
创建project时，选择JAVA模块和Empty模块的区别是 是否引入java相关jdk依赖包
1.project本身是一个目录文件夹，起到一个项目定义，范围约束，规范类型的作用
2.project可以包含多个或一个module，当为单module时，这个module其实就是一个project的作用 
IntelliJ IDEA 中 Project 和 Module 的概念及区别_创建的项目(project)与模块(module)有什么区别?-CSDN博客
具体可以看这个
设置：
字体设置 背景颜色自定义 快捷键操作 常用操作
常用
1.删除类文件 直接右键删除或者delete就可以
2.修改名称 shift+f6 类和包名直接改就可以了 module模块的修改一般修改directory目录和module一个文件夹一个idea里面都要修改
3.导入模块 File-new-module/project from exist xxx 是将目标文件夹的工程或者目录导入到当前的idea 而位置还是在原来的位置 例如 当前我正在编辑的module在D：\jiuye\xA 但是我导入的那个文件在C:\abc 那导入进来的就显示自己在C盘的底下的这个目录
a.而如果想要将目标模块放到自己的一个工作project下，创建好一个module名字 直接将对应的com包复制过来就可以了
4.删除模块： 
a.删除完idea里的module 完事文件夹目录里还存在 需要在对应Exploer里也删除对应文件夹
b.选中上面的地址栏右键删除 然后去文件夹的当前工程文件夹（project）底下idea的modules.xml里把那一行与我们文件夹相关的也删除掉 然后再重启idea  也可以达到同样效果 不过显而易见比较慢 一般采用上述方式
5.打开工程 关闭工程 open close看看就可以了
java基础语法
注释：
什么是注释？有什么特点？给程序员看的解释型文字 不会被编译 所以反编译出来之后不会看到注释（可通过javadoc来生成帮助我们阅读的程序文档  配合一些doc规范注解 具体可参考Javadoc(文档注释)详解） 节约空间 加快速度 快捷键ctrl+/   ctrl+shift+/
字面量：
java中字面量就是指告知程序员数据在程序中的书写格式， 例如字符是单引号 字符串双引号 常量 数值等
字面量编译后存储在class文件常量池中
变量：
定义：存储可以变化的值一个数据类型 格式为 数据类型 变量名=变量值
注意事项：
1.先声明后使用
2.不能在同一个{}内声明重复名的变量
3.声明时可以不初始化 使用时必须初始化
4.什么类型变量 存储什么类型值
变量底层：
使用二进制存储 例如2-》10 6-》110 快速计算的方式为除二取余法 6/2 3余0 3/2 1余1 1/2 0余1 三个余逆序排110 
计算机底层存储的最小单位是字节
重点：字符类型想输出其ascll码二进制形式 可以（int）类型转化 如 （int）c 而想要输出字符串的某个下标值可以使用内置charAt（index）函数。而转字符可以（char）的形式
文本，图片，声音，视频怎么存储的？
文本就是存储对应ascii码 图片其实是由像素点组成的 而像素点的颜色由0~255，255，255间的二进制颜色存储 声音是由波形图对应的声音二进制格式存储 如分成多大精度的波形图
进制间转化
不同进制的数据表示加上不同前缀就可以了 二,八，十六进制分别为 0B/b，0，0x 
数据类型：
1.关系：引用数据类型变量存储的是对象的内存地址值，而基本数据类型变量存储的是对象值本身， 体现在java程序中为jvm虚拟机栈的局部变量表的基本类型变量拷贝了class常量池中的对应的字面量一份到局部变量表里面 而引用类型仍旧存储了引用类型指向堆空间 。
2.书写 根据自己的大小范围 例如byte 1字节 short2字节来确定赋值的范围 如byte b=128就是错的 因为1字节是-128~127 
3.23是int，加上l，23l就是long类型 23.8默认double 加上23.8f/F是float类型
关键字&标识符：
1.关键字：java规定的名称 
2.标识符 _，$或字母开头 可以后面添加数字 不能数字开头。不能关键字
java入门基础
类型转换
自动类型转换：
为什么要有？方便不同类型的变量给对方赋值
原则？范围小的类型变量可赋值给范围大的！如
Java
byte b =20; 00011000
double a=b; 0000000 0000000 0000000 0000000 0000000 0000000 0000000 0000000 00011000
原理也看到了 就是底层二进制 都可以承受 直接将二进制赋值过去就可以了 主要范围有
byte->short->int->long->float->double
     char-> 个
其中 float类型虽然是4字节，long是8字节，但是float表示的数值范围远大于long这是因为浮点型的表示 是数值+小数位数 所有这也是为什么long向float转型的原因
表达式类型转换：
1.小范围的运算结果转化为大范围 
2.Int 以下的 byte，int，short之间的运算直接转化为int 然后其他的转化为运算类型中最大的 如
Java
double a=2;
int b=3;
byte c=4;
a+b+c需要使用double类型接收
强制类型转换：
可以强制将大范围的数据转化为小范围的数据（父子类关系也是）
弊端：会出错 例数据丢失，小数点数据转整形会直接丢失小数点后的数
运算符：
+ - * / % 这些 聊一下 / 以前不知本质 只知道取整 现在知道是根据上述的表达式类型转换，结果是最高位的类型 所以这就是整数/整数 结果只能是整数 也就是取整的原因 所以我们可以 （整数*1.0/整数）用来转化成浮点型 更精确 可以返回带小数的
字符串+做运算，结果还是字符串 是拼接 但是字符相加是遵循前面的表达式转换规则
自增自减运算符：
例如
Java
int a=5;
int b= 3;
int ps=a++ - --b+a++ + --b +b++ + ++c+2
看看等于多少 这种题
赋值运算符：
= += -= *= /= %=
关系运算符：
>  < >= <= != ==
逻辑运算符：
| & ！ ^（异或，不相同则为1） && || 判断前一个符合（不符合）条件 直接返回结果
三元运算符：
 表达式？A：B 表达式成立吗？成立返回A否则返回B
运算符优先级：


API scanner
api就是应用程序接口 使用它有三个步骤 
1.导包
2.获取对象
3.使用其功能
所有的这些导入的包然后通过对象调用功能的 其实都算的上是应用程序接口 
程序流程控制
分支结构
if适合条件 范围匹配 用的情况最多
switch适合值匹配 代码结构优雅 但是注意 case后面只能是byte，short，int jdk5后支持了enum枚举，jdk7后支持String，一定要添加break 否则遇见成功会顺序执行下面的语句 直到break
for循环
while循环
注意死循环问题
随机数类
代码练习：
1.1 水仙花数
Java
public class Demo02 {

    public static boolean isShuiXianHua(int number) {
        int unit = number % 10;
        int tens = number / 10 % 10;
        int hundreds = number / 100;
        return unit * unit * unit + tens * tens * tens + hundreds * hundreds * hundreds == number;
    }

    public static void main(String[] args) {
        int num = 153;
        System.out.println(num + "是水仙花数吗？:" + Demo02.isShuiXianHua(num));
    }
}
153是水仙花数吗？:true

1.2 珠穆朗玛峰案例
Java
float mm=0.1f;
int count=0;
while (mm<884843){
    mm*=2;
    count++;
}
System.out.println(count);

24
3.猜数字游戏
Java
Scanner sc = new Scanner(System.in);
System.out.print("现在产生了一个数字，猜测这个数字是多大，请输入一个数字:");
int target = (int) (Math.random() * 100);
while (true) {
    int userNumber = sc.nextInt();
    if (userNumber == target) {
        System.out.println("恭喜你猜对了！");
        break;
    } else if (userNumber > target) {
        System.out.println("数字大了，重猜");
    } else System.out.println("数字小了，重猜");
}

现在产生了一个数字，猜测这个数字是多大，请输入一个数字:1
数字小了，重猜
50
数字大了，重猜
25
数字大了，重猜
12
数字小了，重猜
18
数字小了，重猜
22
数字小了，重猜
24
数字大了，重猜
23
恭喜你猜对了！
数组
数组的定义
静态数组
初始化时就给添加了所有的值 不可扩容 只能修改已存在的
动态数组
先指定数组大小 后期再赋予对应的值 初始化完成后数组元素为对应的数据类型的默认值，如下
Byte int short long char（默认是0对应的ascll） 默认值是0 float double 默认0.0 Boolean默认false string等引用数据类型默认null
数组越界的理解
数组越界是指使用了数组不存在的下标，而一般发生在遍历数组进行操作时，代码编写出问题导致。可以通过以下几种方法规避一下
1.在使用索引时，始终判断一下索引是否在数组大小范围内
2.for循环遍历，条件控制在size内
3.增强for循环，安全遍历
4.使用异常捕获
debug：
Step over 当前方法内的下一条语句
Step into 进入方法内部 
数组排序

数组内存
程序class入方法区.class文件，main方法入栈，数组变量在main栈帧中的局部变量表，存储地址，指向在堆内存中的数组对象，而基本数据类型直接在局部变量表中存真实值
方法
什么是方法？方法是一种语法结构，它可以把一段代码封装成一个功能，以便重复使用。
特点：1. 提高了代码复用性 2. 使结构清晰
方法常见问题
1.方法不能嵌套定义，之间是平级关系
2.有返回值必须用return返回
3.执行有返回值的方法，不接受结果 结果就消失，只是跑一下
方法在内存中执行
方法属于类元结构在方法区class文件中，程序开始main方法入栈，执行到的方法入栈，执行完的方法弹栈
方法参数的值传递
基本数据类型实参对形参的值传递不改变原变量值，引用数据类型的址传递会改变原数的值，本质上他俩都是值传递，因为传递的是地址值，实际上导致的结果是他们两个对原实参数值是否改变的不同
方法案例
查到值返回下标 否则返回-1.
方法重载
方法重载是指这个方法创建时，已经存在一个相同方法名的方法，只是参数的类型，顺序，数量不同，其他都无所谓（例如返回值和修饰符）
return可以直接结束当前方法的执行。
break结束当前循环
continue结束当前循环执行下一次循环。
好处：体现了相同功能的多样性，可读性优良，提高开发效率，结构清晰。
案例实训
案例一：数组的拷贝（10分钟）
Java
int[] arr = {15,26,85,7,9,5,10,15,20};
int[] newArr= Arrays.copyOf(arr,arr.length);
System.out.println("arr打印结果如下👇");
for (int item : arr) {
    System.out.print(item+" ");
}
System.out.println("\n"+"newArr打印结果如下👇");
for (int i : newArr) {
    System.out.print(i+" ");
}

案例二：数字加密（30分钟）

// 原始数组
int[] arr = {1,9,8,3}
Java
public class Demo01 {

    public static String encryptInts(String password) {
        char[] chars = password.toCharArray();
        int[] nums = new int[chars.length];
        for (int i = 0; i < chars.length; i++) {
            nums[i] = Character.getNumericValue(chars[i]);
        }
        for (int i = 0; i < nums.length; i++) {
            nums[i] = (nums[i] + 5) % 10;
        }
        for (int i = 0; i < nums.length / 2; i++) {
            int temp = nums[i];
            nums[i] = nums[nums.length - i - 1];
            nums[nums.length - i - 1] = temp;
        }
        String result = "";
        for (int i = 0; i < nums.length; i++) {
            result += nums[i];
        }
        return result;
    }

    public static void main(String[] args) {
        String password = "9527";
        String result = Demo01.encryptInts(password);
        System.out.println("传输的密码是：" + password);
        System.out.println("加密完成后为：" + result);
    }
}
//结果
传输的密码是：9527
加密完成后为：2704
案例三

面向对象
创建对象
类：定义 是一组共同特征的描述（设计图），使用时实例化一个具体的对象（具体落地设计）
类中可以有成员变量和方法，用来配合完成功能
一个类文件只能有一个public类，并且要与文件名相同
对象的内存分析
对象实例化时 对象变量存储在栈中，指向堆内存的实例化对象，这个堆内存中包含了成员变量等，包括方法，然后方法调用时会将方法入栈，
方法入栈时 方法内变量的参数的值是从哪里传过来的
ChatGPT
当一个方法被调用时，方法的参数值是通过调用方（caller）传递给被调用方法的。这是通过将参数值压入调用方栈帧的操作数栈中来实现的。在调用方的栈帧中，参数值被设置到局部变量表中的相应位置，然后调用方执行invokespecial、invokestatic、invokevirtual或invokeinterface等指令调用被调用方的方法。
简而言之，参数值是通过调用方传递给被调用方法的，具体的过程如下：
1.调用方将参数值压入调用方栈帧的操作数栈中。
2.调用方栈帧的局部变量表中为方法参数分配空间。
3.调用方执行方法调用指令，将控制权转移到被调用方法。
在被调用方法的栈帧中，局部变量表中的相应位置存储了传递过来的参数值。这些参数值在方法执行期间可供被调用方法使用。
需要注意的是，Java的参数传递方式是值传递。对于基本数据类型，传递的是值的拷贝；对于对象引用，传递的是引用的拷贝，而不是对象本身。

构造器
无参有参，有有参时，无参消失，需要手动添加无参。
this关键字
指向当前类的对象，用来与形参区分
成员变量，局部变量区别
	成员变量	局部变量
声明的位置	类	方法中
修饰符	public，private，final	
内存加载位置	堆！（随对象创建，在堆内存中被分配）	栈
		
StringAPI&集合工具类
StringAPI
只要是带加号+的string字符串对象运算，string对象变量一定是指向堆内存的对象，而不是字符串常量池里的string字符串对象 而直接是字符串相加会自动编译优化 如图

构造器创建时可以选择四种：
1.无参，代表如果存在指向字符串常量池，不存在创建
2.New string，无论存在否，创建新的
3.new String（char[]）/（byte[]），都生成对应数组的字符串，字节数组是将字节转化为ascii码解码后的
 比较：
1.使用equals比较字符串是否相等
2.euqalsIsIgnoreCase忽略大小写比较字符串
返回长度：
length
获取某个位置的字符
charAt（index）
转化成字符字节数组
toCharArray，toByteArray
截取子串
1.左闭右开substring（begin，end）
2.开始到最后一个字符substring（begin）
替换：
1.replace（“target”，“replacement”）将字符串的目标值替换为替换值
按拆分：
split（“”）按照某个字符将字符串拆成字符串数组。高频！
ArrayList集合
添加元素，泛型限定类型，获取下标位置元素，更换下标位置元素，获取长度，遍历集合，删除元素，删除下标元素
遍历元素时边删除，会容易导致出错误，推荐for循环遍历从后往前遍历
Static,继承
static 的作用，修饰成员变量的用法
作用：使变量或者方法变为静态的，生命周期随类变化，可以通过类直接访问，加载在方法区，加载过程早，是类共享的，一个类存在一份实例。
用法：在类成员变量位置，使用类名或者对象都能访问。
static修饰成员变量内存原理
不敢苟同，在下认为静态变量是在方法区中作为类元结构存在的，就算在对象实例化时，也只是在所分配的内存中存在指向静态变量对应区域的指针。
static修饰成员方法基本用法
类名直接调方法，创建对象调用（不推荐）
static修饰的代码的特点
1.静态方法不能访问实例变量，只能访问静态变量和静态方法
2.实例方法可以访问静态和实例
3.静态方法中不能出现this，因为this代表的当前实例对象，而静态方法是与类相关的。
关于静态变量与实例变量访问当前类其他变量各种情况的原因
也就是说，静态方法不能访问实例变量的根本是this无法在静态方法中存在吗
ChatGPT
是的，静态方法不能访问实例变量的根本原因是在静态方法中无法使用 this 关键字，而 this 关键字是用来引用当前对象实例的。因为静态方法是属于类而不是实例的，它们不与任何特定的对象实例相关联，因此无法使用 this。
在非静态（实例）方法中，this 关键字是用来引用当前对象实例的，可以访问实例变量和调用实例方法。但在静态方法中，因为没有当前对象实例的上下文，所以 this 关键字就没有意义，不能用来引用实例变量和实例方法。
如果静态方法需要访问实例变量或实例方法，可以通过传递一个实例作为参数，或者在静态方法内部创建一个实例并调用相应的方法。但需要注意的是，这种做法可能会破坏静态方法的独立性，因此在设计时应该考虑清楚是否真的需要在静态方法中操作实例变量。
You
那实例方法调用其他实例和静态方法或者变量时，用到了this？我咋看不到
ChatGPT
在实例方法中，虽然你在代码中并没有显式使用 this 关键字，但实际上编译器会隐式地将实例方法中对实例变量和实例方法的访问转化为对当前对象实例的引用，这就是 this 的隐式使用。
当你在实例方法中引用实例变量或调用实例方法时，编译器会自动加上 this 关键字。这是因为实例方法是与对象实例相关联的，而编译器为了明确地指示使用的是实例的成员，会自动插入 this。这样可以帮助区分实例变量和局部变量（如果有同名的情况），并确保正确引用当前对象的成员。
以下是一个例子：
Java
javaCopy code
public class MyClass {private int instanceVariable;
// 实例方法public void setInstanceVariable(int value) {// 编译器隐式地加上了thisthis.instanceVariable = value;
    }
public void printInstanceVariable() {// 编译器隐式地加上了this
        System.out.println("Instance Variable: "this.instanceVariable);
    }
// 静态方法public static void staticMethod() {// 静态方法中不能使用this关键字// this.instanceVariable = 10; // 错误，无法在静态方法中使用this
    }
public static void main(String[] args) {MyClass obj = new MyClass();
        obj.setInstanceVariable(42);
        obj.printInstanceVariable();
    }
}
在上面的例子中，虽然在实例方法 setInstanceVariable 和 printInstanceVariable 中没有显式使用 this，但编译器会自动加上 this 关键字来引用实例变量 instanceVariable。在静态方法 staticMethod 中不能使用 this，因为静态方法与对象实例无关。
static相关的应用场景以及举例
1.static修饰的变量适合用于多用户共享的，例如在线人数的数值，视频点赞量等
a.
2.修饰的方法适合公共调用的方法，例如显示在线人数，初始化同一样东西等
3.static代码块适合做初始化工作 如斗地主游戏，房间系统启动时，就先把牌堆初始化 如下
斗地主
Java
private static ArrayList<String> pokers = new ArrayList<>();

static {
    String[] color = {"♠", "♣", "♥", "♦"};
    String[] number = {"3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "K", "A", "2",};
    for (int i = 0; i < color.length; i++) {
        for (int j = 0; j < number.length; j++) {
            pokers.add(color[i] + number[j]);
        }
    }
    pokers.add("big🃏");
    pokers.add("small🃏");
}

public static void main(String[] args) {
    pokers.forEach(System.out::println);
}
♠3
♠4
♠5
♠6
♠7
♠8
♠9
♠10
♠J
♠Q
♠K
♠A
♠2
♣3
♣4
♣5
♣6
♣7
♣8
♣9
♣10
♣J
♣Q
♣K
♣A
♣2
♥3
♥4
♥5
♥6
♥7
♥8
♥9
♥10
♥J
♥Q
♥K
♥A
♥2
♦3
♦4
♦5
♦6
♦7
♦8
♦9
♦10
♦J
♦Q
♦K
♦A
♦2
big🃏
small🃏


继承
继承需要满足的规范
子类共同的特征，行为应该由父类定义，特有的特征不能写到父类中
当存在父子类继承关系时，堆内存中的对象会存储子类独有的，父类的属性方法会在父类空间里，子类有super引用
继承特点：
1.可以继承父类属性和行为，不能继承父类构造器
2.只支持单继承，不支持多继承
3.Object类是所有类的父类
重写
1.私有方法不能被重写
2.静态方法不能被重写
3.要求方法名，参数顺序，类型，数量都相等
子类构造器：
1.子类所有构造器（无参，有参）在执行时，都会先调用父类的无参构造器（默认子类构造器第一行有一个super（）） 这是因为子类构造器初始化可能会用到父类的一些参数，需要父类先调用构造器初始化 总的来说就是继承父类初始化的数据
2.父类没有无参构造，子类会报错，可以通过第一行调用父类有参构造来解决
super&this构造器
1.两者不能共存在于一个构造器之中，他俩都必须放在构造器第一行
2.super指向父类，this指向当前类
3.
单例模式
懒汉：
Java
public class Lazy {

    public static Lazy lazy;

    private Lazy() {
    }

    public static Lazy getLazy() {
        if (lazy == null) {
            synchronized (Lazy.class) {
                if (lazy == null) {
                    lazy = new Lazy();
                }
            }
        }
        return lazy;
    }
}
饿汉：
Java
public class Hungry {
    private static Hungry hungry = new Hungry();

    private Hungry(){}

    public static Hungry getHungry() {
        return hungry;
    }
}
测试代码：
Java
public static void main(String[] args) {
    Hungry hungry = Hungry.getHungry();
    Hungry hungry2 = Hungry.getHungry();
    System.out.println(hungry==hungry2);

    Lazy lazy = Lazy.getLazy();
    Lazy lazy2 = Lazy.getLazy();

    System.out.println(lazy==lazy2);
}

true
true
！面向对象进阶
 ！包&权限修饰符
1.每一个类都有自己包的归属，并且在整个类的第一行显示package com.yhn.xxx；
2.相同包的类可以互相访问，但是不同包的类必须导包（import）才能访问
3.如果在同一个类使用两个类名相同的类，第一个导包，第二个使用全限定名调用。
注意：类的访问区分只在于是否是同一个包，而具体的属性行为访问，才分以下四种情况
权限修饰符层级private<缺省(default)<protected<public，具体区别与记忆方法可以这样
1.private修饰的属性和行为只能同一个类才能访问
2.public修饰的属性和行为所有类都可以访问（包括其他包的无关类）
3.default修饰的属性和行为，除了同一个类，同一个包的其他类可以访问
4.protected修饰的属性和行为，除了default，其他包的子类也可以访问
区分为是否是同一个包，同一个类，子类。
！final关键字
final可以用来修饰属性，方法，类，分别代表
1.如果修饰的是属性，则属性只能被赋值一次，如
Java
public final int a;
a=1
a=2 //报错
a.注意：如果修饰的基本数据类型，值只能赋一次
b.如果是引用数据类型，只是代表地址值不能改变，而引用的对象里的内容可以改变
c.一般来说，配合static修饰变量，因为实例非静态变量，是要被不同对象实例化时赋予不同值的，如name，age这些，每个人都不一样，改为final意义不大，静态的是类共享的，常常可以配合final
2.方法，代表这个方法不能被子类重写
3.类，代表这个类不能被继承，是最终类，例如String类
！常量
1.定义：常量是public static final修饰的成员变量，它必须有初始值，执行过程中值不能被改变，可以写在一个常量类中，调用的时候类名.常量名调用
2.好处：有利于提高系统的可读性，维护性，用常量名替代具体的字符串时体现的非常好，改一个常量就能动全身，而且见名知意例如 USER_NAME.equals(xxx)，对比用户名，并且随时可以针对更改。
3.常量另一个好处就是，编译阶段会把所有用到常量的地方替换为字面量，而无需在运行阶段重新去对照替换，具体体现为，全是某某HOUSE_NAME，编译完把class文件扔到idea里，可以直接看到全是HOUSE_NAME对应的字面量值，自动替换了
4.可以用来做信息的标志和分类，如超级玛丽的上下左右用常量代表
！枚举
枚举类的格式是public enum name{
AA,BB,CC,DD
}
第一行是枚举类的对象名，什么意思呢？当我们对枚举类编译然后反编译后 会发现AABBCCDD变成了
Java
public static final String AA=new name();
public static final String BB=new name();
public static final String CC=new name();
public static final String DD=new name();




的格式，然后枚举类相对于常量，更加的适合做信息的标志和分类，因为它在作为参数时，需要传入name类型的对象，而这时候填ABC，123等全是不符合类型要求的，必须是name对象，而常量用作参数时，传入的是常量对应的参数类型，如int，string这时候传其他同变量的随便传，但是接收到不会做出对应动作，所以
1.需要数值时具体的，如密码用户名等等判断，用常量，因为枚举代表对象
2.需要 信息标志如游戏的上下左右，用枚举就可以，因为只做标志判断，不做值校验
Java
UNPAID("未支付", 0),
PAID("已支付", 1),
FINISHED("已完成", 2),
REFUNDED("已付款", -1);

private String status;
private int index;

order(String status, int index) {
    this.status = status;
    this.index = index;
}

public String getStatus() {
    return status;
}

public void setStatus(String status) {
    this.status = status;
}

public int getIndex() {
    return index;
}

public void setIndex(int index) {
    this.index = index;
}
抽象类：
1.abstract修饰的类，一般用做基类，通过定义抽象方法，抽取子类共同特征，来提供一个类似模板的方式，然后针对子类需要不同特点的方法，重写父类抽象方法实现。
2.抽象类不能创建对象，无论里面有无抽象方法，因为父类增加抽象方法时，子类难道要通过报错来体现吗？
3.子类必须重写父类所有抽象方法，否则也要被声明为抽象类。
4.abstract只能修饰方法和类，不能修饰属性，代码块，构造器
！模板方法：
1.概述：在父类中定义的方法，里面有子类公共的部分，子类各自实现的部分采用抽象方法代替，由子类去自主实现，在主程序中调用模板方法时，会调用父类的模板方法和其中子类实现的抽象方法。
2.举例：例如银行账户类，分为定期和活期，调用handle结算利息方法时，都要登录校验和输出，但是各自的cal计算不同，那就定义一个模板handle方法，公共的写入，cal定义为抽象，子类继承account类，重写一下cal方法，然后主程序中创建子类对象，调用handle方法，自动去调用子类的对应方法，没有就去父类中查找，所以由此可见，
handle方法是最好设置成final的，因为子类一旦重写，就会调用子类的handle破坏了模板，毕竟，模板方法就是为了让子类使用的，所以推荐设置模板方法为final
接口：
接口里面只能有抽象方法，定义规范，区别于抽象类，子类对于接口可以多实现，也可以多继承。一个接口可以继承多个接口，一个类可以实现多个接口。但是一个类只能继承一个类
！接口多继承的作用：
合并规范，例如现在有个pingpongMan类，需要实现Law，People，SportMan接口，但是实现的这几个接口，是有包含和重复关系的，比如SportMan就是人啊，它也需要遵守Law啊，所以直接让SportMan继承Law和People，而pingpongMan直接实现SportMan，就能获得之前同样的效果了
！jdk8后的接口
增加了
1.默认实现default，可以给接口定义一个default方法，有它的默认实现，子类实现接口时，可以直接调用或者重写该方法，比如突然要对一个成熟的接口添加几个方法，这样所有的子类都要去手动实现，我们可以使用默认实现，然后针对性的对子类进行重写或者直接调用。
这个方法只能通过子类对象调用，因为接口不能创建对象
2.静态方法，static方法，由接口直接调用，子类调用不到，接口名.方法名调用该金泰方法
3.私有方法，private修饰，是为了提供给接口内部默认或者静态方法实现用的。
接口的注意事项：

！抽象类和接口的应用场景篇
抽象类就是为了实现多态，子类实现抽象类，重写方法后，作为父类类型的参数，传入不同的子类会产生特定特征的行为，实现了多态。
！！cloud.tencent.com
多态
多态就是指父类引用指向子类对象，子类可以是继承或者实现，表现为不同事物对同一行为表现出的不同特征。
编译时看左边，运行看右边，静态编译，动态运行。
定义方法时，使用父类类型作参数，该方法就可以接收此父类所有的子类对象，体现多态扩展性和便利。
类型转换
子类可以自动转化成父类，父类需要强转为子类，并且类型不对应还会出错Class什么转化错误
案例
需求分析：定义usb接口，在电脑上，可以连接和断开连接对usb设备，键盘和鼠标都有这个特性并且有自己的特性。
创建：USB接口类，有连接，断开连接方法，创建键盘和鼠标类，实现USB接口，并且分别添加敲击和点击方法，主类创建电脑，传入usb类对象，调用连接和断开，并且判断具体是哪个对象就调用他们特有的方法。
内部类  
1.静态内部类（实际开发使用较少），在一个类中定义一个静态（static）类，然后其内部属性，方法的定义就可以和普通类没有任何区别了，实例化时Outer.Inner in=new Outer.inner();这样的形式 注 不能访问外部类中的非静态成员
2.成员内部类：无static修饰，可以直接访问外部类的静态成员和实例变量，属于外部类的对象，使用方式为Outer.Inner in=new Outer.new inner()
3.局部内部类（鸡肋）
4.匿名内部类（重要，使用最多），匿名内部类就是一个接口或者父类在创建对象时使用{}来直接代表创建一个子类/实现类对象，而不用额外定义子类去继承父类或者实现接口，适用于调用次数不多的某个子类的场景中。
API常用：
Object： 
1.toString：默认是打印对象的全限定名@内存地址值，无意义，一般用来让子类重写，用来打印对象的内容。
2.equals：默认是比较两个对象的内存地址值是否相等，一般==作这个，而子类重写equals来比较两个对象的内容是否相等
Objects
1.equals：提供了更安全的比较方式，Object是a.equals(b),可能存在a空指针异常，而Objects提供了像Objects.equals（a，b）这样的方式比较值，
2.isNull：或者Objects.isNull（a），这样的方式判断是否为空，更加专业高级
StringBuilder
1.在拼接，反转字符串的时候，我们建议使用StringBuilder这个手段来完成操作，不过手段不能做目标，做完之后要再转化为String类
Java
String source = "朗晴很气天天今";
StringBuilder sb = new StringBuilder(source);
String target = sb.reverse().toString();
System.out.println(target);
2.append方法效率高，支持链式调用。
Math
1.abs（int a）对参数取绝对值
2.ceil（double a）向上取整 
3.floor 向下取整
4.round（float a）四舍五入
5.pow（double a，double b）返回a的b次幂
6.random返回double类型随机值 范围为[0.0~1.0）
System
exit（int status）终止当前jvm，非零表示异常终止
currentTimeMillis（）返回时间戳，毫秒值形式
arraycopy（被拷贝的数组A，A的哪个元素开始，拷贝到的目标数组B，B的开始位置，个数）数组拷贝
#BigDecimal
作用：解决浮点型数据运算失真
使用：不使用构造器获取BigDecimal对象，而是使用BigDecimal.valueOf（double a）获取
Java
BigDecimal bg = BigDecimal.valueOf(10 / 3d);
System.out.println(bg);

3.3333333333333335
Date
日期对象创建Date d=new Date();
获取时间的毫秒值，d.getTime();
System.currentTime
时间毫秒值如何转化为具体日期时间
1.Date d=new Date(haomiaoTime)
2.d.setTime(haomiaoTime)
!SimpleDateFormat
1.创建对象构造器确定好时间格式，如yyyy-MM-dd HH-mm-ss
2.然后可以用这个时间格式解析Date对象或者其他对象（long，string，时间戳）为相应格式的表示日期的字符串对象
3.也可以将符合格式的字符串对象转化为日期Date对象
Calendar
获取实例的方式Calendar.getInstance()；
可以获取时间的各个信息，年月日，是一年的第几天，一年的第几周，一周的第几天，一月的第几天等等等，可以通过api自由修改这些时间，可以返回日期（Date）对象，拿到毫秒值
！JDK8新增日期和时间API
1.LocalDate日期对象,LocalTime时间对象，LocalDateTime日期时间对象，他们三个都是通过now（）静态方法获取实例对象，且是不可变对象（调用函数得到的时间对象是产生了新的对象，原对象的值不变），提供了各种各样的方法，像是获取年月日，判断是否在某时间前后等，也可以通过ValueOf静态方法获取指定年月日的对应对象

localdatetime=localdate+localtime
SQL
LocalDateTime localDateTime = LocalDateTime.now();
System.out.println("localDataTime = " + localDateTime);
LocalDate localDate = LocalDate.now();
System.out.println("localDate = " + localDate);
LocalTime time = LocalTime.now();
System.out.println("time = " + time);
LocalDateTime of = LocalDateTime.of(localDate, time);
System.out.println("of = " + of);

localDataTime = 2024-03-07T14:37:06.451
localDate = 2024-03-07
time = 14:37:06.453
of = 2024-03-07T14:37:06.453
打印5分钟前的时间
Java
LocalDateTime ldt = LocalDateTime.now();
System.out.println("ldt = " + ldt);
LocalDateTime minusMinutes = ldt.minusMinutes(5);
System.out.println("minusMinutes = " + minusMinutes);

ldt = 2024-01-26T10:07:05.564
minusMinutes = 2024-01-26T10:02:05.564
2.Instant时间戳对象，也是通过now获取，它提供了标准时间，能够转化为Date对象。
3.DateTimeFormatter日期时间解析器，它可以按照指定格式，将日期时间对象，解析成对应指定的格式，也可以在日期时间对象中调用相应api，传入解析器对象，将某个字符串时间对象转化为日期时间对象
打印两天相隔多少时分秒
Java
LocalDateTime now = LocalDateTime.now();
DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
LocalDateTime dateTime = LocalDateTime.parse("2022-01-01 00:00:00", dtf);
System.out.println(ChronoUnit.DAYS.between(dateTime, now));
System.out.println(ChronoUnit.HOURS.between(dateTime, now));
System.out.println(ChronoUnit.MINUTES.between(dateTime, now));
System.out.println(ChronoUnit.SECONDS.between(dateTime, now));

755
18130
1087816
65269001
4.Period，Duration前者用于计算日期间隔，两个LocalDate对象相差多少年月日，后者主要用于计算日期时间间隔，两个LocalDateTime对象相差多少年月日时分秒都可以单独计算
5.ChronoUnit，用来计算两个时间日期对象的各种单位的时间间隔年月周日时分秒毫秒微秒纳秒半天等等等等，最最精确的计算差值的时间类
包装类
基本数据类型的引用类型，可以自动拆装箱，将它进行了对象化设计，可以用作泛型，集合，对象api
功能：1. 可以转化为字符串（没啥用），可以将字符串数值转化为对应得包装类对象，如“32”-》32，“32.1”-》32.1（非常有用，前提是字符串对象格式正确，可以被转化为数值）
Arrays工具类
1.toString方法，打印数组，
2.sort方法，对数组升序，基本类型是已经默认实现了compare方法，升序，但是对于降序，或者其他自定义引用类型数组，我们想要用Arrays排序，需要传入一个Compartor比较器，使用内部类重写compare方法，通过返回1或者-1来决定排序规则；
3.还有二分查找方法，存在某个元素返回下标，否则返回一个加1取反后得值为这个不存在元素如果在数组里应该在的位置，有一个前提，数组有序

Student排序
Java
List<Student> list = new ArrayList();
list.add(new Student("小明", 15, 173.5, LocalDateTime.now()));
list.add(new Student("sc", 19, 183.5, LocalDateTime.now()));
list.add(new Student("tom", 19, 163.5, LocalDateTime.now()));
list.add(new Student("vect", 15, 173.5, LocalDateTime.now()));


list.stream().sorted((o1, o2) -> {
    if (o1.getAge() != o2.getAge())
        return o1.getAge() - o2.getAge();
    else {
        int compare = Double.compare(o1.getHeight(), o2.getHeight());
        if (compare != 0) return compare;
        else return (int) ChronoUnit.MILLIS.between(o1.getLdt(), o2.getLdt());
    }
}).forEach(System.out::println);

Student{name='小明', age=15, height=173.5, ldt=2024-01-26T10:35:03.734}
Student{name='vect', age=15, height=173.5, ldt=2024-01-26T10:35:03.734}
Student{name='tom', age=19, height=163.5, ldt=2024-01-26T10:35:03.734}
Student{name='sc', age=19, height=183.5, ldt=2024-01-26T10:35:03.734}
！lambda表达式（非常重要）
1.作用：将匿名内部类简化成λ表达式，
2.要求：必须是接口（不能是抽象类）的匿名内部类（不能是抽象类的），且这个接口中只能有一个抽象方法（函数式接口）
3.好处：代码简洁，直观，代码风格精辟。
集合：
collection集合体系：
底下有list和set体系，
常用api
增删改查，判断为空，存在某元素
遍历方式
1.迭代器Iteretor
2.增强for（内部还是迭代器）
3.stream流
各种数据结构
栈：
堆：
数组：
链表（单，双）：
二叉树：最普通的，元素没什么特点，一个根节点，每个节点不超过两个子节点
查找二叉树：小的数在左，大的在右，树高可能很高，查询性能变差
平衡查找二叉树：树的高度差不超过1，增删改查效率提高，
红黑树：基于红黑规则实现了自平衡的二叉树
红黑规则：黑树的子树一定是红树，红树的子树一定不能也是红树。
！List遍历
并发修改异常，使用迭代器和增强for和lambda遍历同时的list.remove会直接报错，for循环不会报错，但是会漏删（实际上就是出问题了），for循环可以使用从后往前遍历，或者在正遍历的时候删除元素后i--一下
1.使用迭代器遍历，不能使用list.remove移除元素，可以使用迭代器自带的remove方法，这个方法会移除当前正在遍历的元素，而不会影响正常的遍历。如
Java
List<Integer> list = new ArrayList<>();
for (int i = 0; i < 5; i++) {
    list.add(i);
}
list.removeIf(integer -> (integer == 0));
System.out.println(list.toString());
这是函数式接口，迭代器是iterotor.hasNext itrotor.next==0.if iterotor.remove();
遍历移除+打印
Java
List<Employee> employeeList = new ArrayList<>();
employeeList.add(new Employee("qq", 20, 175.6, LocalDateTime.of(2020, 10, 20, 8, 20)));
employeeList.add(new Employee("张三", 21, 176.6, LocalDateTime.of(2021, 10, 20, 8, 20)));
employeeList.add(new Employee("王五", 20, 165.7, LocalDateTime.of(2022, 10, 23, 8, 20)));
employeeList.add(new Employee("孙六", 26, 185.6, LocalDateTime.of(2019, 10, 20, 8, 20)));

employeeList.removeIf(e -> "张三".equals(e.getName()));
employeeList.forEach(System.out::println);

Employee{name='qq', age=20, height=175.6, ldt=2020-10-20T08:20}
Employee{name='王五', age=20, height=165.7, ldt=2022-10-23T08:20}
Employee{name='孙六', age=26, height=185.6, ldt=2019-10-20T08:20}
 用lmbda判断是否存在叫张三的
Java
List<Employee> employeeList = new ArrayList<>();
employeeList.add(new Employee("qq", 20, 175.6, LocalDateTime.of(2020, 10, 20, 8, 20)));
employeeList.add(new Employee("张三", 21, 176.6, LocalDateTime.of(2021, 10, 20, 8, 20)));
employeeList.add(new Employee("王五", 20, 165.7, LocalDateTime.of(2022, 10, 23, 8, 20)));
employeeList.add(new Employee("孙六", 26, 185.6, LocalDateTime.of(2019, 10, 20, 8, 20)));

Employee zhangsan = employeeList.stream()
        .filter(e -> "张三".equals(e.getName()))
        .findFirst()
        .orElse(null);
if (zhangsan == null) {
    System.out.println("不存在张三");
} else {
    System.out.println("存在张三");
}

存在张三
设计一个返回给前端的结果类，要求可以传入任意类型的结果data，以及msg，code数据
Java
 CommonResult<T> {

    private int code;
    private String msg;
    private T data;


    public CommonResult(int code, String msg, T data) {
        this.code = code;
        this.msg = msg;
        this.data = data;
    }
    
     CommonResult<List<String>> commonResult = 
        new CommonResult<List<String>>(200, "成功", new ArrayList<>());
     CommonResult<Map<String, String>> commonResult1 = 
        new CommonResult<Map<String, String>>(200, "成功", new HashMap<>());
     CommonResult<User> commonResult2 = 
        new CommonResult<User>(200, "成功", new User(15, "梨花"));


    }
}

class User {
    private int age;
    private String name;


    public User(int age, String name) {
        this.age = age;
        this.name = name;
    }
！泛型：
泛型定义的地方可以是类，方法，定义了泛型的地方可以使用时声明为任意一种引用类型，然后操作时，针对泛型类或者方法时如果定义到了该泛型，那么外部一旦将不属于定义类型时约定的泛型类型与对应方法操作时，就会出现编译报错，将运行时的错误提前到编译阶段暴露，使类和方法的使用更加的规范。
经典例子：
Go
List list=new ArrayList();
list.add("ss");
list.add(1);
String s = (String)list.get(1);
会报类型转化异常，而使用泛型定义时，则会提前爆出错误，（编译时），将运行时类型判断提前到编译时验证
另一点，泛型是只是在编译时生效的，运行时会擦除泛型信息，体现在反编译时，会看不到泛型相关信息
https://www.cnblogs.com/coprince/p/8603492.html其他具体的可以看这篇文章
装饰者模式：
教授1亿经费芯片事件，在自定义的一个类中嵌套了其他成熟的类，包含了其他对象，然后稍作修改，称为装饰者模式
！泛型通配符&上下限
默认情况下，泛型定义的类型是任意时，我们可以传任意类型，但是我们使用泛型时，一旦确定的其类型，就只能穿这个类型的数据，传它的子类父类都不可以，此时我们可以在定义泛型方法或者类时，增加泛型上下限，？extends Car代表只能传入Car的子类或者Car，也代表上限，Car super ？时，代表只能是Car和它的父类，代表下限，总的来说泛型中，如果想要方法或者类接收某类相关的子类或父类型，需要手动设置通配符上下限，而不和多态一样，自动适配。
Set集合
特点：无序，无索引，不可重复
底层是HashMap，也就是说，也是数组+链表的结构，但注意哈希表=数组+链表。使用TreeSet如果传入对象类型，因为TreeSet默认是要排序的，所以传入对象的时候必须让对象实现Comparable接口重写compareTo方法或者传入一个比较器来实现排序。
场景：
Set适用于一般场景，去重，无序 TreeSet使用于元素需要排序，遍历获取元素，范围查询这些
可变参数
可变参数内部其实就是一个数组，如果传入多个参数时，可变参数必须在最后一个，且可变参数只能有一个
集合工具类Collections
1.sort方法对集合排序（前提是集合元素实现了比较ble接口，或者传入比较器）
2.打乱集合顺序（只能是List），因为set本身就是无序，而且是使用哈希确定位置dddd
Collection体系综合案例
斗地主发牌，花色大小组成54张牌存储到List集合里，然后将List集合排序打乱，可以直接调用工具类方法，然后三位玩家三个List集合，一人一张根据方式遍历51次每人17张牌，通过逻辑实现剩余三张。 
Map集合体系
遍历方式
1.keySet方式键找对
2.键值对EntrySet
3.lambda表达式超精简遍历
实现类LinkedHashMap
底层是哈希表+双向链表，除了底层与HashMap一样的数组+链表外，它还维护了一个针对于元素插入顺序的双向链表，所以它是有序的
TreeMap
对键排序的Map，也是treeSet的底层，所以它与treeset一样，泛型除了实现自主排序的类之外，其他对象类需要传入比较器或者继承类然后重写compareTo方法
不可变集合
定义：集合创建时就赋予初始值，并且后续对该集合不可进行增删改
方式：集合.of静态方法 如List.of
作用：在需要保护一些数据不能被修改时，可以使用不可变集合修改 jdk8之后才有
Steam流（重要）
流式编程名字的原因，就像是一条生产线，把元素一个个排列上去，然后进行一系列筛选等操作。
流式编程提供了一个非常简洁的集合或者数组元素操作流程，主要分为三步
1.创建流
2.流式中间操作
3.终结操作，最终操作结束后不能在衔接任何操作。
创建流方式：
1.List：list.stream 也就是调用collection的静态API，
2.map
a.键流 map.keySet.stream，以map的键创作流
b.值流map.values.stream，以map的值创建流
c.键值流map.entrySet.stream，以每一个键值对创作流
3.数组
a.Array.stream(arr)
b.Stream.of（arr）
流API：
注意注意：每一个流对象只能使用一次！
非终结操作，中间方法
1.filter（Predicate），传入一个判断条件，符合条件的元素将会被放入新的stream流
！这种函数式接口原理，在filter接口里面定义了针对传入的predicate接口的操作，将它放入新流，最后结束后返回该流 可以做如下参考
Java
    public Stream<T> filter(Predicate<? super T> predicate) {
    List<T> filteredElements = new ArrayList<>();
    for (T element : this) {
        if (predicate.test(element)) {
            filteredElements.add(element);
        }
    }
    return filteredElements.stream();
}

2.Skip 跳过前几个元素
3.limit只取前几个元素
4.distnct，去掉重复值（根据hashCode和equals方法）
5.concat（Stream a，stream b） 合并元素类型相同的a，b两个流为一个流
终结操作
1.Foreach 遍历
2.count 返回当前流中的元素个数 
异常
java程序不能正常执行发生了异常，异常分为两类，error和exception
Exception
分为运行时异常和编译时异常
运行时异常：
运行阶段才被发现然后报错的异常，有以下几种常见的
1.nullPointerException 空指针异常
2.数组下标越界异常/索引越界异常
3.类型转换异常
4.数字转换异常
5.算术异常
如何避免呢？
这种都属于未检查异常，需要通过程序员对业务场景的足够了解与代码书写规范来避免，比如遍历时使用数组或其他的大小，来遍历，类型转化时先判断是否是其子类。
编译时异常
在编译阶段就会提示报错的异常，必须手动捕获或抛出，才能运行程序，如果编译通过，在运行时阶段仍旧可能报错，所以它存在的目的只是提醒程序员，这个操作非常容易出错，请务必捕获它。
IOEception，解析异常（对时间日期类进行传入格式化时）。
异常的处理方式
1.Throws 不做处理 出错之后没有捕获处理 抛到jvm会程序停止
2.Try catch包裹出错的地方，进行当场（在出错的那个方法的那行出错代码那里）处理，进行信息打印或者其他操作
3.向外抛，抛到最外层，统一集中处理
a.好处：可以统一集中处理，代码简洁 可读性优良
i.可以选择将异常信息写入日志，或者提供给用户等操作，可进行的扩展操作比较多
具体在选择处理方式时，不一定要选择第三种，只要能保证程序的正常运行，都是可以的
自定义异常
方式：
1.继承异常类（Exception或者RuntimeException),
2.重写构造器 在里面 throw new 异常名（要打印的信息（也就是e.printStackTrace））
3.在需要使用的地方，直接throw new 自定义异常对象
可以自定义编译时异常或者运行时异常，前者代码会提醒必须捕获或者抛出，否则编译不通过，后者就是正常的编译时异常的现象
日志
日志技术的由来（为什么出现），普通的sout打印，效果较差主要体现在
1.灵活性一般，需要手动添加或移除
2.功能简单，只能输出到控制台，没法持久化
3.多线程下操作性能差
日志技术的优势，就是上面的对立 
 LogBack
是log4j的升级版本，是根据slf4j规范制定的，主要分为三个模块 core classic和 另一个在jetty与tomcat中集成的slf4j api 功能强大
Logback开发步骤
1.导入包到lib中，然后添加到项目库
2.将logback.xml拷贝到src目录下
3.在代码中创建日志对象
4.使用日志对象打印想打印的日志信息
logback日志输出格式，位置
选择输出到file，console，是否生成日志文件。可以选择每个日志文件的大小，也就是多大分层，设置1MB的话，每个日志文件1mb
日志输出级别
简单来说就是指输出的日志的详略程度
五个级别为 TRACE<DEBUG<INFO<WARN<ERROR（默认是DEBUG）
假如我只想看错误的话，那就调到ERROR级别这样子
ALL打开全部日志，OFF关闭全部日志
File文件流
New File（文件路径），会定位到所在的文件，可以通过后续操作判断文件是否存在，文件大小等操作。
\\双反斜杠或者调用File.隔断符作为目录间隔断
1.绝对路径，从盘符名开始，直到文件所在位置，会随着程序在不同机器上流转而无法找到。
2.相对路径（重要），是从当前工作目录（对idea中，是当前程序运行所在文件夹，对于命令行是指从命令行输命令）开始，进行定位，然后一直定位到文件
文件api：
1.判断是否存在，
2.是文件还是文件夹，
3.创建文件/文件夹/多层目录（文件夹）（返回成功失败）
4.返回文件的绝对路径，返回创建路径（以什么路径创建的就返回什么路径）
5.获取文件大小，文件名（带后缀）
6.删除文件夹必须是空文件夹
文件遍历：
1.调用listAPI，返回当前文件夹下所有一级目录的名称到字符串数组
2.listfileapi，返回文件夹下所有一级目录对象到file对象数组
a.注意：如果是文件夹不存在或者是文件，返回null
b.空文件夹返回空file数组
递归
递归题目，猴子吃桃和买啤酒问题。
猴子吃桃把最后一天的1当作转折条件，递归回推
啤酒，将剩余瓶瓶盖盖转化为钱和静态共享变量存储。
字符集
确认两个点
1.两个任何，是指 数字和英文在任何国家字符集都是一个字节，在任何字符集中切换都不会乱码
2.中文，在GBK编码两个字节，UTF-8三个字节，切不同字符编解码会乱码。
先以当前字符集转化为字节，然后用目的字符集解码成中文，关于23字节对应 是靠不同的映射规则实现的
IO流：
IO流（文件字节字符输入输出流）一般来说是以内存为基准，将磁盘中或者idea敲的代码中的内容，读取到内存中，称为输入，将内存中存储的数据，输出到磁盘或者idea中，为输出。
大致分为
1.字节流，字符流，两个接口，紧接着
a.字节输入流，字符输入流，字节输出流，字符输出流为实现类
2.字节流是以字节为单位进行输入输出
3.字符流是以字符为单位输入输出
api：
read（），每次将一个字节读入内存，存在返回该字节，不存在返回-1
read（byte[]）每次将这个字节数组读满，从磁盘中
字节流读取中文乱码分析
每次读取一个字节，或者是三个字节，或者n个字节，都有可能导致中文乱码，
因为默认情况下，中文是三个字节为一个字符（UTF-8），那我们可以将所有字节读到字节数组中，可以通过自定义与文件一样大的数组，或者使用官方提供的readAllapi来实现
输出流
api1. 以一个字节为单位将字节数组的数据写入文件
2.将整个字节数组的数据写入文件
3.将指定范围内的数据写入文件
4.通过构造器将append参数设置为true，表示在文件内容后面追加写（默认输出流先清空再写）
换行问题 
由于各个操作系统与早期跨平台设计的问题，单纯的\R\N会出现问题，所有现在一般用\r\n来表示换行，以便在不同平台下都能使用。 
对于输出流 我经过测试发现
对于一个全汉字文本文件，每次无论多少字节（1或2或3）的数组，以字节数组为单位输出到另一个文件中，最后都没有乱码，因此我猜测是所有的字节都写进去之后，根据字符集再统一编码
#资源释放
Try catch finally 在finally里面，释放资源，来保证资源一定会被释放。 jdk7 9后，增添了自动释放资源的方式 即
try（资源）catch 括号里的资源会自动释放，资源是指实现了Closeable和AutoCloseable接口的对象
！对象序列化与反序列化：
1.对象的序列化就是指将对象转化成可以在内存与idea等载体之间以流的形式传播的过程，具体来说，序列化是将对象写入文件，反序列化是将对象从文件中读取出来 
2.还有一个重要概念 版本号 版本号是一个成员常量，如果修改了版本号，代表对象类进行过相应修改，如果用老的对象文件反序列化，会失败
3.如果想要对密码这样的字段，避免序列化（因为序列化生成文件，有丢失的可能），可以加上transient关键字，表明这个字段，不会被序列化
4.对象想要能够被序列化 需要实现序列化接口
5.序列化写入写出用到的是对象字节输入输出流
多线程：
多线程的创建
Thread常用方法：
getName，setName，sleep
线程安全
多线程操作共享资源，容易产生线程安全问题
线程同步
sychronized锁，reentrylock锁 sychronized关键字
#线程通信：
注意：notify方法，随机唤醒目标对象监视器的一个等待线程所以使用时 一般是Phone.class.nofify这样子，代表唤醒以这个对象为锁对象的线程
线程池

定时器
单线程的timer和线程池提供的多线程允许的定时器
并发并行的概念
线程的生命周期（6个状态）















 









单元测试
概述：单元测试就是帮助我们测试代码的，传统测试方式是在main方法里允许多个方法，不灵活自由，一个出错后面的就都不能执行了
好处：可以自由调试，显示每个方法的测试与预期结果的适配情况，顺利通过是绿色，有所影响（与自己设置的Assert对应 代表小问题）为黄色
使用方式，导入junit单元测试包（一般idea都集成，直接输入@test注解然后alt insert导入即可）。使用时放在方法上，方法要求无返回值和参数（嗯？这不就是main方法的模样吗😀）
常用注解
junit4和junit5都有这五个注解，名字不相同作用是一样的除了@Test之外还有
1.before/beforeEach 在方法调用之前调用一次这个注解下的方法 启动所有方法时，每个方法前都会调用
2.after/afterEach 同上 改为之后
3.beforeClass/ 针对静态方法，没有静态方法出错，每个静态方法前调用
4.afterClass / 同上改为后
上面四个的主要作用是初始化资源和释放资源 猜测可能资源类这样子 就是继承了那个玩意的类
反射
运行时获取类对象，可以调用各属性方法的过程
类对象-》构造器对象可以创建实例，方法，属性可以选择执行或赋值这些方法属性的对应类对象，并可以访问私有成员（需要declare和解除权限）
因为是作用在运行时编译时起作用的东西可以直接绕过，最典型的我们知道，泛型是将运行时出现的错误，提前在编译时就规避，意思是只接受T类型的对象，传入E类型的，编译期发现然后禁止，但是反射是运行期的，能直接向该泛型集合添加其他类型元素，可谓闻所未闻，开天辟地
作用：
1.可以在运行时获取传入对象的所有内容并进行打印等操作（只有反射能做到）
2.可以破坏封装性
3.可以破坏泛型
4.java高级框架（最重要的作用）
注解：
注解就是一个标识，用来标识类或者方法，属性，成员变量，参数，局部变量都可以
注解的创建 class @interface 名字 的形式 可以在注解内部定义String name类似这样的方法，然后使用时写入值，
元注解：
注解注解的注解比如可以定义一个注解的@Target（参数是可以作用在哪 看第一行黑色），
@Retention（作用的时间（编译时 编译时结束 运行期间））
注解解析
注解解析就是注解存在的意义体现，通过反射解析注解的变量，作用区间 内容然后判断如何对这个类，方法等进行处理或者进行什么操作。身为一个标识的存在价值
#注解如何起作用的
注解在编译时怎么起作用呢？明明都没法通过反射获取其信息 给我解释一下
ChatGPT
我先纠正一下我的先前回答中的一些表述。在编译时，注解本身并不会直接起作用，而是由编译器进行处理。编译器会根据注解的定义和用法，进行相应的处理，可能包括生成额外的代码、检查语法或语义等。
下面是注解在编译时如何起作用的一些常见情况：
1.代码生成： 一些注解可以触发编译器生成额外的代码。例如，在Java中，使用@Getter和@Setter注解可以告诉编译器自动生成类的getter和setter方法。
Java
javaCopy code
public class MyClass {@Getter @Setterprivate int myField;
}
1.在编译时，这将由编译器生成类似于以下的代码：
Java
javaCopy code
public class MyClass {private int myField;
public int getMyField() {return myField;
    }
public void setMyField(int myField) {this.myField = myField;
    }
}
1.静态检查： 注解可以用于进行静态检查，提供更多的编译时信息。例如，在Java中，使用@Override注解可以确保被标记的方法实际上是覆盖了父类的方法，否则编译器将报错。
Java
javaCopy code
public class Child extends Parent {@Overridepublic void someMethod() {// ...
    }
}
1.如果@Override注解不存在，编译器可能不会发现子类没有正确覆盖父类的方法。
总之，注解在编译时通过编译器处理，可以触发一些自动生成的行为，进行静态检查或提供其他编译时的信息。在编译后，注解的信息通常就不再存在于生成的字节码中，因此在运行时直接通过反射获取注解信息可能是不可行的，这些信息通常需要通过额外的手段或工具来处理。
你
 一些注解可以触发编译器生成额外的代码。例如，在Java中，使用@Getter和@Setter注解可以告诉编译器自动生成类的getter和setter方法。 你说的这句话 它是怎么告诉编译器的
ChatGPT
在Java中，告诉编译器生成额外的代码通常是通过使用注解处理器（Annotation Processor）来实现的。注解处理器是一种工具，它可以在编译时扫描和处理源代码中的注解，然后根据注解的定义生成相应的代码。
让我们以Lombok库为例，它提供了@Getter和@Setter等注解，用于自动生成getter和setter方法。下面是一个简单的例子：
Java
javaCopy code
// 使用Lombok的@Getter和@Setter注解import lombok.Getter;
import lombok.Setter;

public class MyClass {@Getter @Setterprivate int myField;
}
在这个例子中，当编译器处理MyClass类时，@Getter和@Setter注解会被Lombok的注解处理器识别。注解处理器会生成与这些注解相关的getter和setter方法的代码，然后将生成的代码插入到编译结果中。这是在编译时完成的，因此在生成的字节码中，会包含由注解处理器生成的getter和setter方法。
要让编译器知道何时使用注解处理器，通常需要在编译过程中将相应的注解处理器添加到编译器的类路径中，并在编译命令或构建工具配置中指定它们。
需要注意的是，不同的注解处理器可能有不同的实现方式，但一般的流程是：扫描源代码，识别并处理特定的注解，生成相应的代码，然后将生成的代码与原始代码一起编译。这样，开发者就可以在代码中使用注解，而无需手动编写重复且机械的代码。


设计模式
工厂模式
装饰者模式











Mysql
介绍
1.下载社区版 商用版30day后收费
2.启动MySQL命令
a.net start mysql80
b.net stop  mysql80
3.mysql客户端连接
Mysql -h 127.0.0.1 -p 3306 -u root -p xxxx 
sql分类
1.DDL 数据define定义语言 定义结构的，所以是用来设计定义表的结构，数据库，字段
2.DML 数据manipulation 操作语言 用来对表数据增删改
3.DQL 数据查询语言 用来帮助查询表数据的 包括select 聚合函数 排序 分页等等都属于
4.DCL 数据控制语言 修改用户访问数据库的权限
SQL：
数据库
DDL表：
1.查看表结构 desc table_name
2.查看详细的表结构（创建时的语句）show create table
3.创建表 create table（
字段名 类型 其他
）


1.增：Alter table 表名 add 字段
2.改：Alter table 表明 modify 
3.删：Alter table 表名 drop 字段名
4.修改表名 alter table 表名 rename to xxx 
5.删除表 drop table [if exists]表名
6.Truncate table 表名 删除表结构再重新创建
DML
1.insert
2.Update
sql语句执行顺序
1.FROM： 指定要查询的表。
2.JOIN： 如果查询涉及多个表的连接，执行 JOIN 操作。
3.WHERE： 对表中的行进行筛选，基于指定的条件。
4.GROUP BY： 根据指定的列进行分组。
5.HAVING： 对分组后的结果进行条件过滤。
6.SELECT： 指定要返回的列。
7.DISTINCT： 去重，仅返回唯一的结果。
8.ORDER BY： 对结果进行排序。
9.LIMIT / OFFSET： 限制返回的行数，用于分页。
语句
1.排序 order by desc降序 asc（不写就是默认 升序）
2.分页查询 limit 起始索引 查询记录数 
注：分页查询是数据库方言，每个数据库都有自己不同的实现 mysql是limit
3.group by必须配合聚合函数使用，having必须配合group by
函数：
字符串函数
1.左边补数据lpad 右边lpad 补完一共n个字符
2.substring（string begin last）
3.Trim 去除空格
4.转化为大/小写
数值函数

日期函数

流程函数

多表查询：

内连接
外连接
自连接
联合查询：
Union all（全部拼接）  union（去重）
要查符合A条件的和符合B条件的，也就说符合一个就行，那就使用联合查询







索引优化
插入数据 insert优化
1. 单条改成批量插入
2.手动提交事务，避免频繁的开启提交事务
3.主键顺序插入
4.插入大批量数据时  使用load指令

主键优化
先了解两个概念 页合并 页分裂
页分裂：
当在叶子节点的页排满数据排列时，此时插入一个在这些按主键排列的行数据中间的数据，但是现在没有足够的空间插入了，就需要拿出一部分数据移动到另一个页 然后将数据插入以后，改变页间的指针，这样，原来一个完整的页 发生了分分裂，十分消耗性能
页合并：
当一个页的数据，被删除的超过50%（默认阈值）时，才会将其他页的数据合并过来，生成一个新的满的页，这是页合并
主键设计原则：
1.满足业务需求前提下，尽量降低主键长度（叶子节点，索引 很多用到主键的地方 太大了不合适）
2.插入数据时顺序插入，设为自增主键
3.不要使用UUID或者身份证号等自然主键 没顺序 而且大
Order by优化
1.Using filesort实际上就是没使用索引时的order by排序 在排序缓冲进行的排序 效率差
2.Using index  视频里添加索引后排序变成了这个 实际上是 索引覆盖的情形  这个地方 组会可以问一下宽哥
Group by优化
1.Using temporary 分组没用索引 性能慢
2.使用索引之后分组 然后 覆盖 using index
 limit优化
覆盖索引+子查询的形式完成优化 就是用带索引的字段排序把对应的主键查出来 相当于一张表 然后从这张表连表查询符合id相等的
count优化

update语句优化
update语句升级表锁的情况，比如：
对于只有id和name的表结构数据，执行update name=xxx where name=“xx”；
因为name没有索引，所以它会采用全表扫描 对数据加行锁和间隙锁，，这时候会在整张表的索引项上加行锁和间隙锁，相当于锁了整张表，实际上加的不是表锁。
小林codingupdate 没加索引会锁全表？谈了这个问题
1.对于小表 行锁表锁性能影响不大
2.大表
a.建立索引字段
b.查询出条件的主键 然后通过主键判断
SpringBoot
1. 概述
springboot工程创建方式
IDEA使用Spring Initializr创建项目失败解决方法_idea spring initializr 错误-CSDN博客 5种方式
1.默认
2.阿里云
3.香港节点
4.
5.maven工程 然后手动导入依赖
关于第5点的注意事项：
parent标签放父工程 既符合规范 又避免问题，因为springboot的很多操作都是基于parent的标签底下的内容规来做判断什么的 （可以理解为“注解”的作用）
springboot版本管理springparent不同的版本号
不同的boot版本，里面对应的一大堆的版本号，都各自为了适配产生了不同的改变。
1.stater 启动器 管理依赖项 用来引入某个功能时 自动引入对应的项目坐标 无需我们手动添加 
2.parent 用来管理版本依赖 为加入的项目坐标配置恰当的版本号 避免发生依赖冲突 
3.springboot中关于GAV 只需要书写GA即可 V会自动从父类获取 除非boot中没有管理这个项目坐标对应的版本号，此时才需要手动写入
内嵌tomcat
springboot是内嵌tomcat的，tomcat被组件化，对象化，作为一个对象被spring容器管理。
可以exclusion去除这个项目坐标 然后替换为jetty
2. rest开发
REST风格
REST风格是指用不同类型的访问请求来代表不同的数据库操作，用简单的id路径作为参数；而不是用不同的映射名称区分，用具体的携带参数代表访问元素 。
基本：
1.POST请求代表存储数据
2.GET请求代表查询 路径id代表查询某个id或者没有名代表查询全部
3.PUT代表更新数据
4.DELETE代表删除请求 后面跟路径id
5.类映射路径是模块名+S 如BookController为“/books”
RESTful
意思是使用的REST风格接口开发，
开发：
1.使用时 除了修改注解之外，可以在RequestMapping括号里面添加 method参数=RequestMethod.XXX（PUT等），在将传统方式修改为restful时，除了将原来的方法名，url路径参数或者表单参数删除之外，注意使用postman发送不同请求测试一下
2.！不同的rest注解作用讲解 对于我个人重要
a.RequestBody 请求体 一般用作传输json数据时接收使用
b.RequestParam 请求参数 url地址或者表单参数接收时使用 
c.PathVariable 用于接收路径参数时 也就是只有一个参数时 接收使用

！标准开发
注解全部换成名字注解 而不是括号内的那种形式 对于@ResponseBody的注解 如果所有的资源操作/映射方法/处理器方法都要返回json数据，则直接在接口类上添加RestController即可 它是ResponseBody注解和Controller注解的结合。
3. 基本配置：


1.可以将所有的数据 全部封装到environment对象 用@Autowired注解自动装配过去。
2.要想单独拿到一块内容的数据如datasource下的数据 可以单独创建一个类 然后类名上面的configurationproperties注解指定前缀是datasource 成员变量属性为各值 创建对象获取就可以了

整合Junit单元测试，mybatis，lombok
导入测试类的stater test文件中创建和src文件中结构相同的包和类（如果不在 则需要在springboottest括号里添加上class等实际引导类名 .class或XXX就可以了（看报错）） 在类上加springboottest注解 
然后@Test测试就可以了
4. 模拟项目开发
业务层开发
主要的业务流程代码都在service业务层，方法名字也应该与业务要实现的内容相关，而相对来说 持久层（数据层）方法名字应该体现的是进行了什么DML操作，通过什么干什么的形式，比如通过id查用户
然后很重要的一步，每一个接口开发完一定要进行对应的功能测试（JUnit）
表现层开发
注意映射名规范
控制层的测试一般就使用postman测试了 因为涉及到路径参数等
！表现层消息一致性处理
1.先说场景 前端发现，返回过来的数据 一会是json 一会儿是json数组，一会儿又是一个单独的true或false，很难搞，此刻 联想一下当时学的Result.ok等等等Result工具类，它可以包装各样的数据从成功结果到json数据
2.封装的数据格式一般包含整个数据和成功与否的标志位，用来帮助前后端统一数据格式，方便前端对后端的数据做统一的处理，实现前后端分离

  异常处理
1.在后端抛出异常的话，前端收到的信息又和表现层消息一致性处理一开始的一样了，格式不对，前端懵逼，
2.所以要通过Result类，再次多一个对异常信息处理的机制，返回给前端数据，一般是多一个msg信息，方便前端展示错误原因 
3.具体方法是，使用异常注解@RestControllerAdvice定义异常处理器，被包扫描加载，然后对具体的错误进行具体的处理 
BindingResult：
在实体类加注解校验，@NOTNULL @NOTBLANK这种，然后在controller接收参数的地方，@BindingResult注解参数，用来做校验
5. 运维实用
工程打包与运行
1.mvn pacakge命令打包（避免测试数据扰乱，可以勾选skip test）（需要确保pom文件的build标签里有maven插件）
2. cmd 运行java -jar 工程名.jar 来运行启动
 多环境开发
1.背景：实际项目开发的时候，一定不是使用一套环境进行所有工作的，就像彩排是彩排，上场表演是上场表演一样。一般分为三套，pro生产环境，dev开发环境，test测试环境
2.操作：在springboot的配置文件里，创建四个yml文件，其中三个代表三个环境，划分三个port接口，一个用来接管真正的启动环境，用来写三个文件公共的部分，命名也是真正的application，然后profiles属性值的active代表了我运行的是哪个环境 如图

多环境开发独立配置文件
就是指，针对于每个配置环境，还进行更加精细的分工，例如每个环境都有自己的DB配置文件，Redis相关配置，MVC相关配置等，可以区分的更加清晰，具体使用如图，

日志：
1.概述：日志用于记录基本开发过程中的调试过程，或者运营维护过程的消息
2.日志级别：共6种，4种常用DEBUG,INFO,WARN,ERROR
3.可以通过日志组（日志分组group 可以指定很多个包）或代码包（指定包）的形式，进行日志显示级别控制，也就是针对不同的包，业务进行不同细分程度的打印控制 如图

6. bean 
 用configurationProperties为第三方bean注入属性
1.在yml配置文件书写属性 如datasource
2.创建第三方配置类 data 和上面那个注解 括号里面prefix前缀标记为成员变量的上一级
3.成员变量名字和属性名一模一样
松散绑定
1.概念：指的是configurationProperties使用时，yml配置文件里的数据命名，非常的和谐，可以在字母之间进行任意的大小写，下划线_，中划线-，拼接 在java中都能识别并进行属性注入  
2.value注解无法与松散绑定规则匹配
3.仅是在配置文件中松散，对于前缀命名prefix，必须全部小写 用-连接 烤肉串模式
属性校验
属性校验就是对传入的值进行校验，如果不符合预设的规定，就弹出提示错误，操作步骤如下
1.导入接口和实现类坐标（validated和validator）
2.validated注解
3.加入校验规则和提示信息
7. 测试
 发送虚拟请求：
通过在javatest里面发送虚拟请求，调用controller接口，用来替代postman的功能，直接测试对应方法。
1.建立一个虚拟请求环境
2.注入虚拟MVC调用对象 具体如下图 mock对象（发送请求的对象，通过自动注入获取）

匹配相应执行状态：
预定一个状态，然后将实际的状态与预计比对，如果对上了，则属于比对成功，正常运行，如果没对上，返回错误信息 报错 不要想复杂 就是事件本身 如下图

业务层测试回滚
概述：在业务层的测试类中，加入transaction注解，事务执行完会自动回滚，但是id号会默认增加（测试环境数据库肯定是为测试环境提供的，不用过于在意）
8. 数据层，持久层解决方案
内置数据源：
数据源就是datasource 可以切换多种数据源 在yml 注意使用对应格式即可 以下三种

SpringBoot整合Redis步骤
1.导入依赖
2.配置文件连接redis
3.自动注入模板工具类
缓存：
不仅提高数据库，也可以当作临时数据存储，比如验证码。
9. 定时任务
定时任务就是在特定的时间开启一个线程去完成某任务
Quartz框架
比较繁琐
有几个概念，工作，工作明细，触发器，调度器
1.导入依赖
2.创建一个bean 继承Quartz那个类，重写方法，里面定义了工作内容
3.创建一个配置类（configuration注解），定义工作明细和触发器，绑定关系 具体如下图


Task框架 
非常简单
1.在启动类加EnableScheduling注解
2.新建组件类，任务方法加上Scheduled注解

阿里巴巴开发规范




SpringBoot流程开发规范
项目基本结构
不同包存储的结构：
1.entitys 存放实体类，要求：与数据库表结构完全一致，不多也不少，并且后期会随数据库字段的增删而增删。
2.enums 存放枚举类 此枚举类不同于通常，用1 0 -1等状态表示，而是使用字母，来表示状态
3.dto 存放入参结构 只传输需要的，和某个实体类对象没有的这种数据，不需要传整个，用来减少通信开销，解耦数据模型
4.Vo 存放出参结构 存放返回给前端的数据视图模型 用来展示数据
5.utils 工具类 封装好的api可以直接调用
 核心逻辑结构 
controller层 数据参数校验
Service 接口
Impl 实现类 业务逻辑实现
Dao 持久层 crud
  provider 动态sql拼接层 编写动态sql
Decent公司代码逻辑结构
controller层
1.validated校验，用来检测传进来的DTO对象中，是否符合目标校验规则，可以给校验分组，比如DTO类的属性上表名@检测注解 然后括号里面是哪个组 在validated校验里面加上对应的组，就可以检测是否符合，如图


然后通过bindingresult接收检验结果，如果不符合规则，就会在这个result对象中加入对应错误，我们可以通过检测是否有错误，来进行参数校验，如图


总结的经验，包括重要与犯错的
sql语句篇幅
在连表查询时，join 某表on 两张表的相等连接字段 ，然后想要在满足条件时 如果三个表都有这个字段，并且都连表了，那么在连表时选择哪张表和下面where这个字段判断相等时 都会有影响 具体看如下语句
pi,odi,oi都有那个字段但是因为写的内容，致使查询结果不符合预想
Go
sql.append("SELECT " +
        " oi.id," +
        " oi.order_code," +
        " oi.user_id," +
        " oi.order_pay_amount," +
        " oi.order_actual_paid," +
        " oi.order_status," +
        " oi.order_comment," +
        " oi.create_time," +
        " oi.update_time," +
        " pi.product_name," +
        " bi.brand_name" +
        " FROM order_info oi" +
        " JOIN user_info ui ON oi.user_id = ui.id" +
        //pi odi oi
        " JOIN order_detail od ON od.order_code = oi.order_code" +
        " JOIN product_info pi ON pi.product_name = od.product_name" +
        " JOIN classify_dict_info cdi ON cdi.classify_id = pi.classify_product_id " +
        " OR cdi.classify_id = pi.classify_brand_id " +
        " OR cdi.classify_id = pi.classify_id" +
        " JOIN brand_info bi ON bi.classify_id = cdi.classify_id" +
        " WHERE 1=1 ");
// 如果存在状态字段 根据状态查询
if (StringUtil.isNotBlank(orderInfoDTO.getOrderStatus())) {
    sql.append(" AND oi.order_status=#{dto.orderStatus} ");
}

//如果存在订单编号，按订单编号查询
if (StringUtil.isNotBlank(orderInfoDTO.getOrderCode())) {
    sql.append(" AND oi.order_code=#{dto.orderCode} ");
}

//如果存在用户姓名，按用户姓名查询
if (StringUtil.isNotBlank(orderInfoDTO.getUserName())) {
    sql.append(" AND INSTR(ui.user_name,#{dto.userName}) > 0");
}

//如果存在商品ID，按商品ID查询
if (!Objects.isNull(orderInfoDTO.getId())) {
    sql.append(" AND pi.id=#{dto.id} ");
}

//如果存在商品分类id，按商品ID查询
if (!Objects.isNull(orderInfoDTO.getClassifyProductId())) {
    sql.append(" AND pi.classify_product_id=#{dto.classifyProductId} ");
}

//如果存在分类ID，按分类ID查询
if (!Objects.isNull(orderInfoDTO.getClassifyId())) {
    sql.append(" AND pi.classify_id=#{dto.classifyId} ");
}

//如果存在品牌ID，按品牌ID查询
if (!Objects.isNull(orderInfoDTO.getClassifyBrandId())) {
    sql.append(" AND pi.classify_brand_id=#{dto.classifyBrandId} ");
}

//如果存在时间范围，按时间范围查询
if (!Objects.isNull(orderInfoDTO.getCreateTimeStart())
        && !Objects.isNull(orderInfoDTO.getCreateTimeEnd())) {
    sql.append(" AND oi.create_time BETWEEN #{dto.createTimeStart} AND #{dto.createTimeEnd} ");
}
sql.append(" ORDER BY oi.order_code");
return sql.toString();
for循环
你的代码存在一个问题，就是在循环中一直在往 orderDetailList 中添加同一个 orderDetail 对象，而不是在每次循环中创建一个新的 orderDetail 对象。这会导致 orderDetailList 中的所有元素都是同一个对象的引用，最终它们的属性值会被最后一个添加的值所覆盖
以上是chatgpt指出的，第一次看还震惊了一下，后来测试发现的确是这样的
for循环（）{
orderDetail.set(i)
orderDetailList.add(orderDetail)
}
这样下来，最终导致的结果是，list集合里添加了n内容相同的对象，是因为添加对象时，存储的是对象的引用类型指针，而不是死的值~！
事务
如果想让事务生效，
1.事务必须捕捉到抛出的异常，才能回滚，
a.如果被return异常类
b.或者try catch处理了，
2.没有重写throw，那事务很明显生不了效
其他
json请求与表单请求
{"aa":"bb,...}这种是json请求，http：//url/aaa=bbb&ccc=ddd这种是表单请求，一般用requestBody注解接收json请求的数据，而用多个requestParam来接收表单请求数据，但是无论哪一种，都得需要参数名匹配变量名才能正确接收到。
http请求与okhttp请求，包括其工具类
http是同步的，使用工具类只是便捷开发，也是同步阻塞等待请求回来，而okhttp是异步的。
问题总结：
方法嵌套与递归间接调用与嵌套类
1.java不允许方法嵌套定义，是指方法中不能定义另一个方法，但没说不能调用和递归
2.方法递归间接调用是指，方法调用别的方法，别的方法又调用自己，形成递归
3.嵌套类，类中有另一个类，也就是内部类，内部类中可以定义方法外部类也可以有方法，形成类似嵌套方法，实则有本质区别，那就是方法的直属上级是类。











关于sql优化
关于sql优化有两个问题：
1.对于update语句 我们知道 where条件后面的字段 如果没有索引 会产生锁表更新数据的情况，对并发限制很大 那实际业务中一定会给update后面的判断条件加索引吗 或者是where后面用主键索引做条件判断修改数据吗
2.针对order by语句 我们知道order by后面的字段加索引 然后查询字段也被索引覆盖的话排序效率是非常高的 但是我的理解的话 order by一般不都是用在查询一批数据然后针对于某个字段排序的情况吗 这时候要想出现using index 就只能是针对所有字段建立联合索引了。
然后经过查阅资料 发现上述假设或许是我是mysql5的原因 mysql8增加了索引下推 即使不符合覆盖条件也能用到索引 想知道实际应用是如何提高order by效率的。
！接口与实现类
今天了解了更深层次一个理解，关于接口与实现类，先说三个例子：1. jdbc与数据库驱动如mysql驱动
2. servlet与tomcat 3. validation与validator 他们无一例外都是接口与实现类的关系，我们用的是接口也好，嘴上说的是使用的对应的实现类这个名字也好，其实一定都必须引入具体的实现类才能使用 ，毕竟实现类才是本体，接口只是规范，所以jdbc是什么？是接口。我们使用jdbc连接数据库 是真的使用的它吗？是也不是，不是是因为使用数据库驱动才能连接，是是因为jdbc提供的规范。切记
学习方法分享？
1. 输入与输出

2. 提高学习效率，拒绝完美主义
陷入细节，陷入自己的思维圈套
3. 结构化思维，先框架后细节，先整体，后局部


4. 笔记在精不在多
5. 笔记特点
1.90%手打
2.话术偏白话，易懂，非比照视频手打，参照脑路思考
3.主次分明，简单的和过难的为轻，有一定难度和重要的为重
4.结构分明 可以快速回顾 
效果
1.记忆持久度，深刻程度
2.时间成本
a.考试分化，一天，一周，年 生涯规划

颈椎
轻松版本
1.老鹰双脚略微分开，双手从腿部向上划，划到头顶交叉向上顶，期间吸气，顶部保持5s，放下来 呼气 5次一组
2.蜻蜓，手交叉和脖子做对抗 10s 5组
3.蜂鸟 程W状 不要耸肩 飞来飞去
版本2
1.收下巴脑袋顶墙
2.先肩膀像前仰头 后脑勺和肩膀挤压 然后肩膀往后 低头 后脑勺和肩膀远离
3.弹力带
4.画粪字
腰椎
绝了❗常见腰背难受疼痛。脊医专家献大招❗_哔哩哔哩_bilibili
【Dr.奥运脊医李鹏博士】腰间盘突出现场处理全过程，高能满满_哔哩哔哩_bilibili
富贵包
主要问题是 胸椎结合处，是体态问题导致的，办公族头前伸啥的，C戈矫正动作
重点在于调整胸椎
1.脊柱松动 可以理解为猫屎伸展，胸椎最高点和最低点来回 仰头低头可以伴随 也可以不咋动 重点在于胸椎
15次三组 最高点：腰部供起来了        最低点 腰塌下去了  跟做视频在下面
	
颈椎大包 根本性解决方案，必须调整胸椎！ 2024最新，跟做打卡版本【C戈】_哔哩哔哩_bilibili
