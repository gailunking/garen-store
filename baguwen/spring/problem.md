[spring.md](https://github.com/gailunking/garen-store/files/14821838/spring.md)[Uploading sp# 零基础自测

## Spring IOC和AOP的理解

**IOC****（**inversion of control）控制反转，实际上就是将对象的创建从代码转化角色为框架，将原来由程序代码统一创建管理的对象交由spring框架去管理

优点：易于对象的管理调度，易于添加拓展功能，

**DI****依赖注入****：**容器中的对象需要IOC容器为其注入需要的资源，如属性赋值等

**AOP****面向切面编程****：**对目标对象代码与原来的对象剥离开来，在他的前后添加一些某种功能的代码，而不影响原对象，对代码进行了扩展，通过运行期动态代理实现对程序功能统一维护的一种技术

## Spring注入bean的几种方式

- **set方法注入：**

类中创建属性的set方法，在xml中使用propertiesy name value手动注入 引用类型ref传bean的id

- **构造器注入**

类中创建有参构造，在xml中的bean子标签constructor-arg中name value构造器注入或者使用index 或者type

- **静态工厂注入**

类中创建静态工厂方法，创建一个工厂类，包含一个静态方法，通过factory-method="getCar"创建

```
<!-- 
        注意，这里的配置并不是创建一个SimpleFactory对象，取名为car，
    这一句配置的意思是，调用SimpleFactory的getCar方法，创建一个car实例对象，
    将这个car对象取名为car。
-->
<bean id="car" class="cn.tewuyiang.factory.SimpleFactory" factory-method="getCar"/>

<bean id="user" class="cn.tewuyiang.pojo.User">
    <!-- name和age使用set注入 -->
    <property name="name" value="aaa"/>
    <property name="age" value="123"/>
    <!-- 将上面配置的car，注入到user的car属性中 -->
    <property name="car" ref="car"/>
</bean>
```

- **实例工厂注入**   实例工厂与静态工厂类似，不同的是，静态工厂调用工厂方法不需要先创建工厂类的对象，因为静态方法可以直接通过类调用，所以在上面的配置文件中，并没有声明工厂类的`bean`。但是，实例工厂，需要有一个实例对象，才能调用它的工厂方法。

```
<!-- 声明实例工厂bean，Spring容器需要先创建一个SimpleFactory对象，才能调用工厂方法 -->
<bean id="factory" class="cn.tewuyiang.factory.SimpleFactory" />

<!-- 
    通过实例工厂的工厂方法，创建三个bean，通过factory-bean指定工厂对象，
    通过factory-method指定需要调用的工厂方法
-->
<bean id="name" factory-bean="factory" factory-method="getName" />
<bean id="age" factory-bean="factory" factory-method="getAge" />
<bean id="car" factory-bean="factory" factory-method="getCar" />

<bean id="user" class="cn.tewuyiang.pojo.User">
    <!-- 将上面通过实例工厂方法创建的bean，注入到user中 -->
    <property name="name" ref="name"/>
    <property name="age" ref="age"/>
    <property name="car" ref="car"/>
```

- **注解注入**

在需要注入的类或者方法属性上加对应注解，如@Autowired，value，如果`bean`依赖于其他`bean`（比如`User`依赖`Car`），那么我们可以使用`@Autowired`或者`@Resource`这两个注解进行依赖注入，这个大家应该都知道。但是如果要为基本数据类型或者是`Java`的封装类型（比如`String`）赋值呢？这时候可以使用`@Value`注解。这里我就不演示了，感兴趣的可以自行去研究，应该是比`xml`的方式简单多了。

## bean的生命周期？

bean的生命周期概括起来就四句话，实例化，属性赋值，初始化，销毁

1. 实例化（Instantiation）： 在这个阶段，Spring容器会实例化Bean，通常是通过**构造函数来创建Bean的实例**。这是Bean的诞生阶段。
2. 属性设置（Populate Properties）： 在B**ean实例创建之后**，Spring容器会**通过****依赖注入**（Dependency Injection）或其他方式来**设置Bean的属性值**和其他依赖关系。
3. BeanPostProcessor的前置处理（BeanPostProcessor PreInitialization）： 如果在容器中定义了`BeanPostProcessor`接口的实现类，它们将在Bean初始化之前进行处理。这提供了在Bean初始化前进行自定义处理的机会。
4. 初始化（Initialization）： 在这个阶段，Spring容器调用Bean的初始化方法。可以通过配置`init-method`属性或实现`InitializingBean`接口来指定初始化方法。这是在Bean实例创建完成后、属性设置完成后的一步。

```
javaCopy code
public class MyBean implements InitializingBean {@Overridepublic void afterPropertiesSet() throws Exception {// 初始化逻辑
    }
}
```

或者通过XML配置：

```
xmlCopy code
<bean id="myBean" class="com.example.MyBean" init-method="init"/>
```

1. BeanPostProcessor的后置处理（BeanPostProcessor PostInitialization）： 如果在容器中定义了`BeanPostProcessor`接口的实现类，它们将在Bean初始化之后进行处理。这提供了在Bean初始化后进行自定义处理的机会。
2. 销毁（Destruction）： 在Bean不再需要时，Spring容器会调用Bean的销毁方法。可以通过配置`destroy-method`属性或实现`DisposableBean`接口来指定销毁方法。

```
javaCopy code
public class MyBean implements DisposableBean {@Overridepublic void destroy() throws Exception {// 销毁逻辑
    }
}
```

或者通过XML配置：

```
xmlCopy code
<bean id="myBean" class="com.example.MyBean" destroy-method="destroy"/>
```

这些阶段组成了一个Bean的完整生命周期。在Spring容器中，这个生命周期是由容器来管理的，开发人员主要需要关注初始化和销毁阶段的自定义逻辑。

然后扩展开的话，实际就是关注Spring Bean生命周期的扩展点，但是由于他的扩展点很多，我就介绍几个核心的，在扩展点可以实现自己的自定义逻辑。

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MzBlNmM4ZTYyMzI3YzZmMDhkODhiYmIxNWUyNzU2NWVfQUFPMFdxdUhSUHRMOXo0NjR2S2doTGRaZjc5aUNBTVJfVG9rZW46WE1KN2I1eTRBb2NoaUJ4c3hTSmNqS21Dbk9mXzE3MTE5NjIzNjg6MTcxMTk2NTk2OF9WNA)

bean生命周期中一些容器级的方法，主要是围绕**BeanPostProcessor 一系列接口展开的。**

在实例化前后 调用InstantiationAwareBeanPostProcessor简称Aware相关的方法如 postProcessBeforeInstantiation 在实例化之前postProcessAfterInitialization在实例化之后调用////

实例化前从各种组件注解中获取到Bean的Class信息，根据这些信息生成BeanDefinition，将其加载到BeanFactory，然后调用BeanFactoryPostProcessor，对容器进行一些扩展操作，**实例化Bean执行其无参构造，**

执行被PostConstruct注解标注的方法，**进行set方法属性注入，**，检查**Aware相**关接口，设置相关依赖（调用对应的方法），然后**执行BeanPostProcessor接口的postProcessBeforeInitialization**方法，然后**在init-method初始化之前**，检查Bean是否实现chushihuaBean这个接口，里面有个afterPropertiesSet方法可以对Bean属性执行最后一次赋值操作，**调用初始化方法**，**完事执行BeanPostProcessor接口的postProcessAfterInitialization方法，至此，初始化完毕，**可以从容器中获取bean使用，然后销毁前可以调用preDestory注解标注的方法。

有三个增强方法的核心接口 **BeanFactoryPostProcessor****接口 ，****BeanPostProcessor****接口 ，****InstantiationAwareBeanPostProcessor** **接口，**

1. 其中BeanFactoryPostProcessor是BeanFactory初始化之后，bean实例化之前调用，用于修改或者添加Bean的定义
2. **BeanPostProcessor****用在bean实例化后，初始化阶段前后调用的自定义行为**
3. **InstantiationAwareBeanPostProcessor** **用于实例化前后或者属性注入前修改Bean的属性设置**

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=OTE1YjgyMmE3MGVhYjU3ZDg3MmY1NDhmYzgyZjc4NDVfTmtYMDdHSlNjV0RRZHh6Wk02ck9yVlVWQmp4N3Vqa0dfVG9rZW46QkVpVWJ6dE9wb3Y5MEJ4dVhRMmNaell0bkFmXzE3MTE5NjIzNjg6MTcxMTk2NTk2OF9WNA)

暂时无法在飞书文档外展示此内容

## spring中bean的线程安全性

1. 在单例模式下singleton，成员变量和静态变量都是线程不安全的
2. 在proptotype原型模式（多例模式）下，成员变量是线程安全的，静态变量是线程不安全的

原因：对**于静态变量**来讲，它的生命周期是伴随类的，随着类的实例化，创建这个静态变量，所以在spring加载后，无**论创建多少个对象，发多少次请求，访问的这个静态变量都是同一个变量**，是不变的；

- 对于**成员变量**来说，在**单例模式****下，**第一次发送请求，调用getBean方法，会先**判断容器中是否有这个对象，没有的话创建一个，如果有了的话，就不会创建，而是拿之前那个对象**了，所以操作的是同一个变量，**线程不安全**，
- 而对于**原型模式**，每次请求都会创建一个新的对象，线程之间是隔离的，属于线程安全的。

（注：这里的线程不安全体现的是不同线程访问这些变量时，是否是线程私有的，就是说他们的访问结果是否会因为其他线程的访问而受到影响，如：变量是int a=10类型，每次访问输出a+1后的值，如果所有线程都是11，就是安全，一次11，然后12，再次13就是不安全）

#### 如何解决单例模式下线程不安全问题呢？

threadLocal解决，将单例模式的成员变量或静态变量，封装到threadLocal当中，这个是实现线程隔离的方法，然后每次请求访问时，单例模式下对这两个变量的操作都会变成线程安全的。

## Trancational注解的作用？

它是声明式事务范畴

声明式事务：基于AOP面向切面的，它将**具体业务与事务处理部分****解耦**，**代码侵入性很低**，所以在实际开发中声明式事务用的比较多。声明式事务也有两种实现方式，一是基于TX和AOP的xml配置文件方式，二种就是基于@[Transactional](https://so.csdn.net/so/search?q=Transactional&spm=1001.2101.3001.7020)注解了

编程式事务：是指在代码中手动的管理事务的提交、回滚等操作，代码侵入性比较强，如下示例：

try {

//TODO something

transactionManager.commit(status);

} catch (Exception e) {

transactionManager.rollback(status);

throw new InvoiceApplyException(“异常失败”);

}

作用于**类**：当把@Transactional 注解放在类上时，表示所有该类的**public方法**都配置相同的事务属性信息。

作用于方法：当类配置了@Transactional，方法也配置了@Transactional，**方法的事务会覆盖类的事务配置信息**。

作用于接口：不推荐这种使用方法，因为一旦标注在Interface上并且配置了**Spring** **AOP** **使用****CGLib****动态代理**，将会导致@Transactional注解失效

在Spring中，对方法或者类加@Trancational注解，可以使这些类或者方法在遇到异常时回滚。但在一些特定情况下回滚注解不生效，

### 事务失效：

如：

1. **@Transactional 应用在非 public 修饰的方法上**

如果Transactional注解应用在非public 修饰的方法上或者被final修饰的方法，Transactional将会失效。

之所以会失效是因为在Spring AOP 代理时， TransactionInterceptor （事务拦截器）在目标方法执行前后进行拦截，DynamicAdvisedInterceptor（CglibAopProxy 的内部类）的 intercept 方法或 JdkDynamicAopProxy 的 invoke 方法会间接调用 AbstractFallbackTransactionAttributeSource的 computeTransactionAttribute 方法，获取Transactional 注解的事务配置信息。

```
protected TransactionAttribute computeTransactionAttribute(Method method,
Class<?> targetClass) {
// Don’t allow no-public methods as required.
if (allowPublicMethodsOnly() && !Modifier.isPublic(method.getModifiers())) {
return null;
}
```

此方法会检查目标方法的修饰符是否为 public，不是 public则不会获取@Transactional 的属性配置信息。

注意：protected、private 修饰的方法上使用 @Transactional 注解，虽然事务无效，但不会有任何报错

1. 事务传播级别设置错误，never，not_support或者supports，此时事务不会发生回滚
2. rollbackfor设置，默认处理未受检异常（运行时异常，runtimeException），如果想要处理其他异常，需要设置，否则不生效。
3. **嵌套方法使用@Trancational时**，如果被注解标记的方法被**同类或者其他类的其他方法调用**，此方法发生异常，不会回滚数据，但相对的，如果在最外层方法加@Trancational注解，内部调用其他方法出问题，会发生回滚。
4. 在有被**try catch包裹的语句**的方法上增加@Trancational注解，包裹体内发生异常，**@Trancational注解会**失效，异常被处理，数据正常提交，没有回滚
5. 本方法内调用其他方法，（此时是this.其他方法（），是this对象而不是代理对象生成的从而事务失效）

下面这个有更详细的，可以查看

[一篇弄懂@Transactional注解-CSDN博客](https://blog.csdn.net/liuerchong/article/details/123567337?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0-123567337-blog-112434905.235^v39^pc_relevant_3m_sort_dl_base2&spm=1001.2101.3001.4242.1&utm_relevant_index=3)

## Spring常用注解有哪些？注解的作用？相似注解的区别？

### 常用注解

1. @Autowired，自动装配，将该注解修饰的对象注入到容器中
2. @Qulifierd，在Autowired默认匹配方式找不到对应对象进行依赖注入时，查看这个注解内部定义的name名字属性值，匹配到xml文件指定名称的bean
3. @Resource，也是自动装配的注解
4. @Value，设置基本类型的属性值
5. @Nullable，被该注解修饰的变量值可以为null
6. @Component：组件，这个类交给spring管理，是bean！

1. ###### @Configuration 声明当前类为配置类，相当于 xml 形式的 Spring 配置。

12.@RequestMapping 用于映射 Web 请求，包括访问路径和参数。

13.@ResponseBody  支持将返回值放在 response 内，而不是一个页面，通常用户返回 json 数据。

14.@RestController 用于标注控制层组件，包含 @Controller 和 @ResponseBody。

15.@RequestParam 用于将请求参数区数据映射到功能处理方法的参数上。

###### @Transactional通过这个注解可以声明事务，可以添加在类上或者方法上。

原文链接：[Spring常用注解你知道有哪些吗?_说一下你熟悉的spring注解-CSDN博](https://blog.csdn.net/active_pig/article/details/105613944)

### 注解作用

给spring容器提示该组件属于什么部分

### 相似注解

Autowired和Resource区别：

@Autowired功能虽说非常强大，但是也有些不足之处。比如：比如它跟**spring强**耦合了，如果换成了JFinal等其他框架，功能就会失效。而@Resource是JSR-250提供的，它是**Java标准**，**绝大部分框架**都支持。

除此之外，有些场景使用@Autowired无法满足的要求，改成@Resource却能解决问题。接下来，我们重点看看@Autowired和@Resource的区别。

- @Autowired默认按**byType自动装配**，而**@Resource默认byName自动装配。**
- @Autowired只包含一个参数：**required，表示是否开启自动注入**，默认是true。而@Resource包含七个参数，其中最重要的两个参数是：**name 和 type。这两个参数代表先按什么查找bean，同时指定了的话查找name和type唯一匹配的**
- @Autowired如果要使用**byName，需要使用@Qualifier一起配合**。而@Resource如果指定了name，则用byName自动装配，如果指定了type，则用byType自动装配。
- @Autowired能够用在：构造器、方法、参数、成员变量和注解上，而@Resource能用在：类、成员变量和方法上。
- @Autowired**是spring定义的注解，而@Resource是JSR-250定义的注**解

# Spring全家桶中级工程师面试自测

## Spring

### Component和Bean的区别

java中spring帮助我们管理Bean有两个步骤：一是注册bean，二是装配bean

有三个方式：1.自动装配 2.javaconfig配置类 3.xml文件方式

1. 作用对象不同 ：`@Component` 注解作用于类，而 `@Bean` 注解作用于方法
2. `@Component` 通常是通过路径扫描来自动侦测以及自动装配到 Spring 容器中(我们可以使用 `@ComponentScan` 注解定义要扫描的路径从中找出标识了需要装配的类自动装配到 Spring 的 bean 容器中)。`@Bean` 注解通常是我们在标有该注解的方法中定义产生这个 bean，`@Bean` 告诉了 Spring 这是某个类的实例，当我们需要用它的时候还给我。
3. `@Bean` 注解比 `@Component` 注解的自定义性更强，而且很多地方我们只能通过 `@Bean` 注解来注册 bean。比如当我们引用第三方库中的类需要装配到 Spring 容器时，只能通过 `@Bean` 来实现。

@Bean 需要在配置类中使用，即类上需要加上@Configuration注解

@Configuration

public class WebSocketConfig {

​    @Bean

​    public Student student(){

​        return new Student();

​    }

}

### BeanFactory，FactoryBean和ApplicationContext的区别？

#### 自己总结

BeanFactory是一个Bean工厂，是IOC容器的核心底层类，许多类比如其他两个类都是由他派生而来，用BeanFactory创建Bean对象时，只有第一次调用到getBean方法时才会对该Bean对象实例化，是一种延迟加载机制。

而ApplicationContext在容器初始化时就创建了所有的Bean对象，进行了属性注入，而且相对于BeanFactory，可以对许多功能进行扩展，能实现的功能更为强大比如**支持spring各种插件和AOP特性**

FactoryBean不是一个普通bean对象，在Spring容器中分为普通Bean和工厂Bean，它属于工厂bean，调用getBean方法时，调用的不是这个对象本身，而是手动设置的对象，这个方法可以自定义Bean的一些内容，而不是像其他方法，只能手动设置属性注入的值，更为灵活。可以通过在参数前面加一个&符号，返回自身的对象。**有定制化的实例化逻辑**

#### 复制

1. BeanFactory:
   1. `BeanFactory` 是 Spring 框架的最基本的容器。
   2. 它是一个工厂模式的实现，**负责管理和装配 bean** 对象。
   3. `BeanFactory` 提供了基本的依赖注入功能，但是它在**实例化和初始化 bean 的时候是延迟的**，也就是说，当**第一次请求一个 bean 的时候，才会去创建和初始化这个 b**ean。
2. FactoryBean:
   1. `FactoryBean` 是一个接口，允许用户**自定义实例化和配置 bean 的逻辑**。
   2. 用户可以**通过实现** **FactoryBean** **接口**，来定义一个工厂类，该工厂类负责创建和配置一个特定类型的 bean。
   3. 与普通的 bean 不同，使用 `FactoryBean` 创建的 bean 实际上是由 **FactoryBean** **的实现类返回的对象，而不是** **FactoryBean** **实现类本身**。
   4. 通过 `FactoryBean`，你**可以实现更复杂的逻辑，例如在创建 bean 之前进行一些额外的初始化操作**。
3. ApplicationContext:
   1. **ApplicationContext** **是 Spring 框架的高级容器**，它继承了 **BeanFactory** **的功能，提供了更多的企业级特性**。比如
   2. MessageSource, 提供国际化的消息访问 
   3. 资源访问，如URL和文件  
   4. 事件传播特性，即支持aop特性
   5. 载入多个（有继承关系）上下文 ，使得每一个上下文都专注于一个特定的层次，比如应用的web层 
   6. **ApplicationContext** **在容器启动时就会****预加载****所有的 bean**，并在**需要的时候进行初始化**。
   7. 除了基本的 bean 管理功能，`ApplicationContext` 还提供了诸如事件传播、AOP、事务管理等方面的支持。
   8. `ClassPathXmlApplicationContext`、`FileSystemXmlApplicationContext` 等是 `ApplicationContext` 的一些具体实现类。

### Spring循环依赖问题如何解决？常用的解决方案，三级缓存

spring循环依赖是指spring容器创建实例化时，有两个以上的bean对象互相调用，形成闭环，导致双方各都无法顺利初始化完毕注入到对方属性中的现象

三级缓存，bean对象创建出来通过构造器实例化结束后会被放进Bean缓存，然后进行属性注入的过程中，有需要的新对象，就去实例化创建，新对象构造器实例化依赖注入过程发现需要A对象，就去Bean缓存中取出，然后赋值完毕，B对象属性赋值结束，初始化结束，A对象也拿到B对象，初始化结束。这个过程解决方案是针对singleton模式的，因为这个模式下对象会在缓存中存储，而原型模式下，对象不会往Bean缓存里存储，就无法解决循环依赖问题，除此之外，构造器互相引用的循环依赖问题在使用构造器注入方式时也无法解决，因为构造器初始化是实例化阶段发生，此时还没有将对象存入缓存池。

#### bean的创建步骤

1. 在创建 Bean 之前，Spring 会通过扫描获取 BeanDefinition。
2. BeanDefinition就绪后会读取 BeanDefinition 中所对应的 class 来加载类
3. bean实例化（称之为原始对象，还未赋值，放入缓存这个动作称之为**提早暴露**）
4. 属性注入（循环依赖发生在这个阶段）
5. 如果bean中某个方法有AOP操作，则需要根据原始对象生成代理对象
6. 初始化（初始化结束后放入一级缓存）

#### 循环依赖三个场景

1. 循环依赖使用set方式注入（三级缓存可解决）
2. 使用构造器注入（无法解决）
3. bean对象使用原型模式创建（无法解决，原型模式下不会将原始对象放入缓存）

第三级缓存主要用于解决循环依赖中AOP问题，如果没有第三级缓存的话，循环依赖中第二个对象引用最先创建的原始对象，然后原始对象中有AOP操作，在最后会生成一个代理对象放到一级缓存中，那就出现了对象B引用原始对象，而实际A对象是由代理对象代理的，主要还是由于AOP是在属性注入后执行的。

引入三级缓存之后，当B对象想引用A对象时，A对象会提前执行AOP操作并加载到二级缓存里，而对象引用的查找顺序是先从一级缓存中搜索，没有就去二级缓存中取

1. 二级缓存中的对象什么时候会上移到一级缓存？
   1. 二级缓存是为了提前暴露 Bean 来解决循环依赖问题，此时的 Bean 可能还没有进行属性注入，只有等完成了属性注入、初始化后的 Bean 才会上移到一级缓存（单例池）中。
2. 为什么可以解决 AOP 的问题？
   1. 三级缓存通过利用 `ObjectFactory` 和 `getEarlyBeanReference()` 做到了提前执行 AOP 操作从而生成代理对象。
   2. 这样在上移到二级缓存时，可以做到如果 Bean 中有 AOP 操作，那么提前暴露的对象会是 AOP 操作后返回的代理对象；如果没有 AOP 操作，那么提前暴露的对象会是原始对象。
   3. 这样就能做到出现循环依赖问题时，注入依赖的对象和最终生成的对象是同一个对象。（相当于 AOP 提前在属性注入前完成，这样就不会导致后面生成的代理对象与属性注入时的对象的不一致）

所以 Spring 利用 **三级缓存** 巧妙地将出现 **循环依赖** 时的 **AOP** **操作** 提前到了 **属性注入** 之前，避免了对象不一致问题。

作者：单程车票

链接：https://juejin.cn/post/7218080360403615804

来源：稀土掘金

著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

#### **怎么样的循环依赖无法处理?**

（1）因为加入singletonFactories三级缓存的前提是**执行了构造器来创建半成品的对象**，所以**构造器的循环依赖没法解决**。因此,Spring不能解决“A的构造方法中依赖了B的实例对象，同时B的构造方法中依赖了A的实例对象”这类问题了！

（2）spring**不支持原型(prototype)bean属性注入循环依赖**，不同于构造器注入循环依赖会在创建spring容器context时报错，它会在用户执行代码如context.getBean()时抛出异常。因为对**于原型bean，spring容器只有在需要时才会实例化，初始化它**。

因为spring容器不缓存prototype类型的bean，使得无法提前暴露出一个创建中的bean。spring容器在获取prototype类型的bean时，如果因为环的存在，检测到当前线程已经正在处理该bean时，就会抛出异常。

原文链接：https://blog.csdn.net/wangxuelei036/article/details/104960558

### Spring事务的底层实现原理

AOP就是方法增强，对需要扩展功能的方法进行代理，执行前后增加逻辑，如事务，日志等。

Spring分为编程式事务和声明式事务，编程式事务的处理逻辑就是手动try catch业务代码，执行commit事务提交和rollback事务回滚，事务开启。

而声明式事务是在xml或者注解方式标明事务，由框架自动生成，无需我们手动提交，减少了对代码的入侵性，不需要对源代码做任何修改。底层是spring通过我们需要进行事务**的类生成一个代理类，**在原代码前后用trycatch包围做开启事务遇错回滚处理

### Spring中单例Bean的线程安全问题

单例Bean由于它在spring初始化时就创建好了一个实例，并且每次有请求调用都是返回的这唯一一个实例，所以多线程访问其静态变量或者成员变量时，都会出现线程安全问题，一般的解决思路是，将相关资源使用ThreadLocal类封装起来，这个类可以将某些资源设置成线程私有的，线程间对于共享变量的修改就具有了隔离性，从而避免了单例Bean的线程安全问题。

ThreadLocal底层是被放到了Thead对象的TheadLocalMap中，其key是threadlocal，value对于各个线程都不相同，具体参考如下

[史上最全ThreadLocal 详解(一)-CSDN博客](https://blog.csdn.net/u010445301/article/details/111322569)

### Spring框架中使用到了哪些设计模式，谈一谈

1. 代理模式，动态代理静态代理中都有，它是将一个类的核心方法，核心逻辑交给了一个代理类实现，当我们需要调用这个方法的时候，向代理类获取，代理类也可以对这个方法前后进行功能增强，而原对象代码没有进行任何改动，增强扩展性，却有对原业务逻辑代码具有很好的封装性
2. 单例模式：每次调用返回同一个实例，单例模式有懒加载（懒汉式）和饿汉式。在我们的系统中，有一些对象其实我们只需要一个，比如说：线程池、缓存、对话框、注册表、日志对象、充当打印机、显卡等设备驱动程序的对象，bean的默认作用域就是singleton单例的
3. 原型模式（多例模式），每次调用返回一个新实例化对象
4. 工厂模式，通过方法从指定某逻辑的工厂中获取实例对象。例如beanFactory和applicationContext中拿取对象
5. 模板方法模式：在一个抽象类中描述他的方法的执行顺序，通过父类中方法前后具有逻辑的定义，来使子类实现遵循一定的规则，这样的类就用到了模板模式。

它可以使子类不修改内部方法或者算法的执行结构，而单独修改某一个特定步骤，如：`jdbcTemplate`、`hibernateTemplate` 等以 Template 结尾的对数据库操作的类

```
// 抽象类
abstract class AbstractTemplate {
    // 模板方法，定义算法骨架
    public final void templateMethod() {
        step1();
        step2();
        step3();
    }

    // 抽象方法，由具体子类实现
    protected abstract void step1();

    protected abstract void step2();

    protected abstract void step3();
}

// 具体子类
class ConcreteTemplate extends AbstractTemplate {
    // 实现抽象方法
    protected void step1() {
        System.out.println("Step 1");
    }

    protected void step2() {
        System.out.println("Step 2");
    }

    protected void step3() {
        System.out.println("Step 3");
    }
}

// 客户端代码
public class TemplatePatternExample {
    public static void main(String[] args) {
        AbstractTemplate template = new ConcreteTemplate();
        template.templateMethod();
    }
}
```

1. 观察者模式，在另一个对象改变的时候，绑定对象作出相应的动作。

### JDK动态代理和Cglib动态代理的区别？

## SpingMVC

### SpingMVC中的MVC分别代表什么？对应是处理什么逻辑，功能？

MVC分别指Model模型，view视图，controller控制器

模型是指工程中**的****javabean**，用来处理数据的，java分为两种javabean，

1. 实体bean，**专门存储业务数据的**，如实体类User，Student这种
2. 业务处理bean，如service和dao对象**，专门用于处理业务逻辑和数据访问。**

视图是**与用户交互的页面，展示数据**，如html，jsp页面，

控制器是完成视图与模型的交互匹配的，**用来接收请求和响应请求**

MVC的工作流程大致是这样的，首先用户通过视图层发送数据给服务器，服务器通过controller通过处理将数据接收到model进行处理，然后通过controller响应给view视图进行渲染，渲染完毕后上传给用户看

然后springMVC是spirng的子项目，用来给表示层做的一套完备的解决方案，是帮助表示层完成工作用的。

## SpringBoot

### springboot是什么？springboot优势？为什么要有springboot？

在没有springboot之前，我们传统的ssm框架开发业务，各种xml配置文件，配置前端控制器，视图解析器，乱码过滤器，配置web.xml，加载spring，spingmvc，配置数据库连接，spring事务，配置文件的读取，配置日志，tomcat调试等等等一系列费时费力的麻烦重复操作，springboot应运而生。

主要是两个思想的体现，**约定大于配置，自动装配**，

##### 约定大于配置的体现：

配置文件放到resource资源文件底下。

配置文件要以application.yml或者application.properties

配置文件中连接数据库的格式spring.datasource

项目默认编译后的文件放入target

##### 自动装配：

实际就是从spring.factories文件中获取到需要进行自动装配的类，生成相对应的Bean对象，把他们交给spring容器管理

### SpringBoot的启动流程？

1. 首先，SpringBoot会读取配置文件和启动类，启动类是springboot应用程序的入口。
2. 然后，SpringBoot会调用sping框架初始化spring容器，包括创建各种bean对象和依赖注入等操作。
3. 接着，SpringBoot开启自动配置功能，读取configuration修饰的类，自动注册bean，便于使用
4. SpringBoot启动内嵌的各种服务器，如tomcat，jetty这种，在web服务器上部署应用程序
5. 最后，启动SpringBoot自身应用程序，启用相应线程进行服务处理

在回答时需要强调SpringBoot是Spring框架的封装，并对开发者进行了很多简化，导致SpringBoot启动流程相较于Spring框架的启动流程更为简单，而且SpringBoot内置Web服务器的特性更是让SpringBoot的使用变得像使用一般的Java应用程序一样简单，十分适合快速构建Web应用程序。

作者：ovO

链接：https://juejin.cn/post/7212901252014440504

来源：稀土掘金

著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

#### 约定大于配置：

有哪些约定呢？比如r**esource文件底下要存放资源配置文**件，默认配置文件要**以application.yml或者properties命名**且唯一，**spring：datasource为前缀的数据库连接信息**。src-main-resources，src-main-java。默认的编译器生成的类都在ta**rget文件夹下**面。

#### 启动流程：

SpringBoot从SpringApplication.run方法作为程序入口，启动，执行针对于运行过程的监听器，然后进行环境信息构建，接着进行IOC容器创建，IOC容器刷新的前置操作，容器刷新，IOC容器刷新后置操作，然后执行结束文件，最后调用runner。

最后的runner是调用commandLineRunner类的run方法，这个也可以自定义

其中有许多自定义点

自定义`BootstrapRegistryInitializer`类 配置到`spring.factories`文件中

自定义`ApplicationContextInitializer`类 配置到`spring.factories`文件中

自定义`ApplicationListener`类 配置到`spring.factories`文件中

自定义`SpringApplicationRunListener`类 配置到`spring.factories`文件中 用于扩展启动的各个阶段

自定义banner图，在`classpath`下创建`banner.txt`文件

自定义`ApplicationRunner CommandLineRunner`类，使用`@Component`交给`spring`容器管理，容器启动完成后调用其run方法

**如何创建一个starter也在下面写着：👇**

根据springboot提供的自动配置类规则创建一个starter，首先创建一个maven工程，配置好针对于要实现的starter的相关依赖，然后创建一个properties类和configuration类，加上对应的注解，autoenableconfiguration，然后在metainf底下将这个starter配置进去。然后重启项目install，

链接：https://juejin.cn/post/7174031833183551519

来源：稀土掘金

著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

### SpringCloud

你们公司使用了哪些组件？为什么使用，他的功能是什么？当前有哪些实现方式，你们的架构筛选考虑的是哪些方面？

## Spring，SpringBoot，SpringCloud的区别是什么

# 高级工程师自测

## SpringBoot的自动装配流程

首先SpingApplication.run方法，里面有个r**efreshContext****（****context****）方法**（刷新容器），这个方法会读取配置类上的注解

自动装配流程是从**SpringBootApplication**注解开始的，这个注解包括三个注解，Configuration，**EnableAutoConfiguration**，ComponentScan，其中EnableAutoConfiguration，注解里面的类，上面有一个I**mport（****AutoConfigurationImportSelector****.class）**注解，里面引入的那个类有个Sp**ringFactoriesLoader.loadFactoryNames**(getSpringFactoriesLoaderFactoryClass(), getBeanClassLoader()方法，会读取jar包中的M**ETA-INF/sping.factories文件**，这个文件里配置了自动装配的类，根据配置类的条件，实现bean的自动装配。Condition ON name什么什么布鲁布鲁

## SpingMVC的底层实现

# 面试补充问题：

## AOP的实现方式有哪几种？

1. XML配置文件

   1. springAPI实现 ：

      1. ​    通过使增强类继承API接口，重写里面的方法来决定切入点的位置如重写afterRetuning（）方法，

   2. 自定义实现

      1. ​    自定义切入类，xml文件配置

2. 注解实现

在切面类增加@aspect注解，增强方法上标注切入点对应的注解@Before之类，环绕通知

#### 或者面试官可能想问动态代理实现的两种方式！

1. jdk动态代理
   1. 当bean实现了接口时，spring使用jdk动态代理实现
   2. 是jdk官方的Proxy创建代理对象
2. CGlib
   1. 当bean没有实现接口时，spring使用cglib实现aop
   2. 使用CGlib的enhancer类创建代理对象，是第三方的一个类

### 有没有在项目中用到过AOP

在输出excel表格的时候用到过

## component注解和bean注解有什么区别？

1. component注解加在类上，bean注解加在方法上
2. componet注解一般通过componentScan包扫描识别出来加载到容器自动装配，bean一般是加在某个方法上告诉容器在我需要时返回这个方法创造的实例
3. bean的自定义性更强，引入第三方类时只能通过bean注解加载到容器里

bean一般搭配configuration注解使用

## 事务失效的场景

1. 非public修饰，使用private或者protected修饰
2. 被final修饰的方法事务会失效，因为在动态代理过程要重写这个类的方法，被final修饰的话无法进行这个操作，也就无法被动态代理，事务也就不生效
3. 嵌套子方法使用事务，外层方法未使用事务会失效
4. 被try catch包括的代码异常不会生效
5. 设置了错误的传播模式

## 事务的传播特性

首先明确两个概念，

1. **当前 当前**指的是我这个方法**外面的那个方法，**也就是调用这个方法方法，所以**当前存在事务**的意思是外面的方法上有事务注解
2. 必须基于内部**这个方法有事务注解**，拥有对应的事务传播行为，相对于是主体

### 7种传播形式

1. 必须事务——propogation-REQUIRED 如果当前存在事务，则加入，不存在则创建 例子 回家老婆做饭了吃饭，没做饭自己做饭吃饭
2. 支持事务——SUPPORT如果当前存在事务，则加入，否则无事务 回家老婆做饭了吃饭，没做饭不吃
3. MADATORY——强制 必须以非事务执行，如果存在事务报错，不存在事务运行 做饭了回去生气，不做饭减肥
4. REQUIRED_NEW——隔离 首先新建事务，然后如果当前存在事务，则挂起当前事务，（也就是当前事务自己回滚自己的，出了错只管自己，不回滚当前） 回家就自己做饭自己吃，无论老婆做没做
5.  NOT_SUPPORT 非事务 不存在事务执行 存在事务挂起事务 做饭了不吃 没做饭也不吃
6. NEVEW——强制非事务 只能以非事务执行 当前存在事务则抛异常 就是减肥，而且做饭了打你

[带你读懂Spring 事务——事务的传播机制](https://zhuanlan.zhihu.com/p/148504094)

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=OTY1N2U4ZjgyMGRiZjMyNDZhMTdlNmY3NDg3MzJiMzhfcEp2eGJsTHExdm44SlBYRTNWSFBXR2FqNFNqcWd3Q1ZfVG9rZW46WEw5TWJlZ2lZb0Q5c3p4Qm40WmNBaWdIbkVjXzE3MTE5NjIzNjg6MTcxMTk2NTk2OF9WNA)

## starter是什么

## 有没有手写过一个starter？

写过，当时写了一个将对象转化为JSON字符串的starter，写完逻辑之后，在类上加入configuration注解，在方法上加入conditionOnClass注解

[手把手教你手写一个最简单的 Spring Boot Starter](https://zhuanlan.zhihu.com/p/353561846)

## 有没有自己用spring的钩子函数写过一些东西？

## 浏览器给表示层传参等这些注解传参方式请求头/体 问号 requestBody等等这些注解及其作用再复习一下

**springmvc-day02**

前端发送请求url  匹配到前端控制器dispatcherservlet，解析到处理器handler映射到对应的类的方法上，处理完成后通过视图解析器（除了数据解析外，还包括对应的prefix前缀/WEB-INF/templates/，suffix后缀.html这种匹配映射）返回到对应页面

如果前端发送的请求携带请求参数，如{user/add（id=7）}带括号的，或者form表单中携带了多选框或者多个input值，后端需要用requestParam注解在参数面前获取，或者只有一个请求参数的时候可以省略。如果是可以封装到实体类中，可以直接用实体类接收

###### **3.2.1 访问Thymeleaf前后缀控制范围内的页面**

访问这个路径，它的requestMapping方法定位到之后，除了跳转或者重定向，没做任何其他操作，可以用这个

在 SpringMVC 配置文件中使用 mvc:view-controller 配置： 

```
<mvc:view-controller path="/index.html" view-name="portal"/>
```

此时浏览器路径"index.html"可以访问到Thymeleaf前后缀控制范围内的portal.html页面

###### **3.2.2 访问Thymeleaf前后缀控制范围外的页面**

在 SpringMVC 配置文件中使用 mvc:view-controller 配置：

```
<mvc:view-controller path="/test/redirect/command" view-name="redirect:/outter.html"/>
```

此时就相当于代替了之前的重定向访问的那个handler方法

### **PathVariable注解获取路径参数**

1. **REST** **风格路径参数**

请看下面链接：

/emp/20

/shop/product/iphone

如果我们想要获取链接地址中的某个部分的值，就可以使用 @PathVariable 注解，例如上面地址中的20、iphone部分。

1. **具体操作**

##### **2.1 单个路径参数**

###### **2.1.1 发送请求携带参数**

<a th:href="@{/rest/movie/2}">携带参数movieId</a>

###### **2.1.2 handler方法获取路径参数**

//注意:{movieId是一个占位符}，表示获取该位置的值，@PathVariable("movieId")表示获取占位符为"movieId"的值

```
@GetMapping("/movie/{movieId}")
public String findMovieById(@PathVariable("movieId") Integer movieId){
    logger.debug("GET请求...."+movieId);
    return "target";
}
```

## SpringMVC的执行流程？

[blog.csdn.net](https://blog.csdn.net/weixin_43246215/article/details/108345624)

什么handler 映射这一套

对于autolinkmvc:annotation-driven/autolink扫描指定的包中的类上的注解，常用的注解如下所示：

@Controller 声明Controller组件，通常作用在控制层（如 Spring MVC 的 Controller），用于将控制层的类标识为 Spring 中的 Bean，其功能与 @Component 相同。

@Service 声明Service组件 @Service(“myMovieLister”) ，通常作用在业务层（Service 层），用于将业务层的类标识为 Spring 中的 Bean，其功能与 @Component 相同。

@Repository 声明Dao组件，将数据访问层（DAO 层）的类标识为 Spring 中的 Bean，其功能与 @Component 相同。

@**Component** 泛指组件, 可以使用此注解描述 Spring 中的 Bean，但它是一个泛化的概念，仅仅表示一个组件（Bean），并且可以作用在任何层次。使用时只需将该注解标注在相应类上即可。

@RequestMapping("/menu") 请求映射，用于映射url到控制器类或一个特定的处理程序方法。可用于类或方法上。用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径。

@**Resource** 用于注入，( j2ee提供的 ) 默认按名称装配， @Resource(name=“beanName”)

@**Autowired** 用于注入，(srping提供的) 默认按类型装配

@**Transactional**( rollbackFor={Exception.class}) 事务管理

**@RequestBody：注解实现接收****http请求****的****json****数据，将json转换为java对象。**

@**ResponseBody**：注解实现将conreoller方法返回对象转化为json对象响应给客户。

@Scope(“prototype”) 设定bean的作用域

@**GetMapping**：它是 @RequestMapping(method =RequestMethod.GET) 的一个快捷方式，只能处理 Get 类型请求。

@**PostMapping**： 它是@RequestMapping(method =RequestMethod.POST) 的一个快捷方式，只能处理 Post 类型请求。

@**RequestParam** ：将请求参数绑定到控制器的方法参数上

@**Configuration**：声明当前类为配置类，相当于 xml 形式的 Spring 配置中的，该注解需要添加到类上。

原文链接：https://blog.csdn.net/weixin_43246215/article/details/108345624

## spring事务的传播机制

## Spi机制

关于spi能通俗易懂的给我解释下吗

ChatGPT

当我们提到SPI（Service Provider Interface），我们通常是在讨论一种在 Java 中用于扩展和插件的机制。让我尝试用通俗易懂的语言来解释SPI：

假设你正在开发一个软件框架，这个框架希望允许其他人轻松地为它添加新功能或扩展。SPI 就是一种让你的框架变得灵活、可插拔的方法。

1. 服务接口（Service Interface）：
   1. 想象一下你的框架提供了一些服务，比如说文件存储服务。你可以定义一个接口，比如`FileStorageService`，里面有一些方法，比如`saveFile()`和`loadFile()`。

- javaCopy code public interface FileStorageService {void saveFile(String fileName, bytecontent);byteloadFile(String fileName); }

1. 服务提供者（Service Provider）：
   1. 现在，你希望其他人可以通过编写自己的实现来扩展 `FileStorageService`。SPI 允许他们这样做。他们只需要实现你定义的接口，并在某个地方声明自己是服务提供者。

```
javaCopy code
public class AmazonS3FileStorage implements FileStorageService {// 实现 saveFile() 和 loadFile() 方法
}
```

1. 服务加载器（Service Loader）：
   1. SPI 中的服务加载器是一个智能工具，它会在你的框架初始化时检测到并加载所有实现了你服务接口的类（服务提供者）。
2. 框架使用：
   1. 当你的框架运行时，它会使用服务加载器来发现所有的服务提供者，并把它们实例化并使用。这样，你的框架就能够透明地获得所有可用的功能扩展。

SPI 的优势在于，它使得框架的开发者和扩展者之间的协作更加简单。框架的开发者定义了接口和规范，而扩展者只需要实现这些接口，框架会自动发现并使用它们。这样，软件框架变得更加灵活和可扩展。

# decent实训

## 组件注册

### @Conditional按照条件注册

我们知道**Configuration配置类，里面的bean方法注解**，会根据**return的对象**，在**容器中添加**对应对象，并且可以通过这个配置类的configuration括号里面的e**xclusion注解用来排除这个类里要添加的组件**，等**等很多操作**，相应的，针对于每个bean，也可以通**过不同地情况下，用来选择性的注册到容器中**，而这就是通过conditonal注解实现的

1. 创建一个conditionn子类，**继承condition类**，然后重写里面**的判断方法**，用来判断一个**bean的context上下文**的一些条件，比如它的beanfactory，classloader，environment环境信息，等等，判断当什么什么时，返回true，也就是注册到容器，比如环境信息的**os.name属性为window系统时**，return true 

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=OTFhZTFjMWRlMDdjODY5MWI3M2ExYmIxZTlhZGNhODBfN0dkazlRUWJTeXBlNGpSQllYd1hOOUdaUkxNdmdJSnJfVG9rZW46VUZvU2IwV1Nab09yY1Z4Yk5tMWNLN1NFbjZmXzE3MTE5NjIzNjg6MTcxMTk2NTk2OF9WNA)

1. 在需要的这个判断条件的配置类地方，的括号里面写想要的条件的那个condition类的class类，然后就能通过容器上下文进行判断然后注册

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=N2NjYTlmZGIzMWMzNzA4YTBmMzk1MThhZGNkZjA4ZDdfUDViTDhEWTFmenFBZTVKa2ZEWVF3YUtENzZzS2s3RjZfVG9rZW46VEt0R2JqRFRHb0NlSWp4ZGk0bGNhZE1yblJkXzE3MTE5NjIzNjg6MTcxMTk2NTk2OF9WNA)

[Spring系列第20篇:@Conditional通过条件来控制bean的注册_csdn envcondition-CSDN博客](https://blog.csdn.net/likun557/article/details/105108901)

### @Import注解

[Spring全解系列 - @Import注解](https://zhuanlan.zhihu.com/p/147025312)

1. import注解就是在括号里直接导入需要注册的组件的类 我猜测需要必须在配置类上使用，（不过配置类，除了configuration注解 其他的诸如component注解，等 如下图 都属于配置类

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=YmE5ODM3ODA4ZjQ2ZWYzYTA2MjU5MmQ1YWNiNGUwZDRfTlZLNkxuRDFHTXI0cEV0cmNJeERSbFdVWXF4VDZSaWNfVG9rZW46RklnN2JjaW9hb1lFNlF4RXRGY2NrYWRKbnFmXzE3MTE5NjIzNjg6MTcxMTk2NTk2OF9WNA)

）如图

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NDc3Y2MwYWE0NGY2MGMwYzA4OTA4ZTZiMWU2MWIyMDZfYmgxUnpaSlFLSDdCVnhyM0d3Mk5kZnpOazB2bm1sZGlfVG9rZW46VVhZNGJSZzhNbzJndEp4elNWbGNDWk5pbk1kXzE3MTE5NjIzNjg6MTcxMTk2NTk2OF9WNA)

上面也引出了第二点

1. selector，比如通过impor**t某某selector来引入这个类里重写了相应方法的所有return的string[]数组**，包**括green和yellow，**相当于一个套**娃封装的组件的嵌套引用**
2. 如下图，其实上面加上这一点共三点，都是import的三个value参数，某某.class的class对象数组，而importBwanDefinitionRegistrar这个接口的实现类里面，可以根据条件判断，手动将某个类注册到容器中。
3. 总结：以上三者的区别，我个人认为，就是一个是直接导入类名来实现bean注入，一个是通过importSelector导入好多个类（bean），最后一个是通过那个注册，通过条件判断将某个类注册到容器

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=Y2Q3NDIzMmI0YzM4NGY2MjYxMzdiZTUyNmVkZmRkMmZfRUo5bFBMeVJXYjRYdlJEck5zZEVPdkJjT1B2dWdJOG9fVG9rZW46Sm5lVWJYT3pwb3RNWkh4UFQ0WGNUUEwybmhjXzE3MTE5NjIzNjg6MTcxMTk2NTk2OF9WNA)

## bean生命周期应用

我们根据上面都知道，bean要经历实例化，属性注入，初始化，销毁四个阶段，具体使用的使用，通过视频看到了，在配置类当中，对某个bean进行工厂bean的注入，然后加入对应的注解参数信息，指定调用哪个方法，具体如图

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=YjNlN2U5Y2M1MTBjNzA2OTJhMGJmMjE5MTMwODA2NmJfNHNETUx4YUxWVlBBTUt5NllkOXVjbzBxVE1ycWc1NFFfVG9rZW46U2tGWGJ3Q3pzb2NoRlF4Nlp5NGNVQUJ0bjJnXzE3MTE5NjIzNjg6MTcxMTk2NTk2OF9WNA)

然后能在Car通过定义对应的方法，进行一些beanFactory所谓的能实现更加复杂的初始化逻辑操作

1. 针对于其他的扩展点，我感觉大多数都是通过实现相应的接口，重写里面的方法，来实现的，例如

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NTEzYjgwNmYzZjhjNzRlN2I3NzY1OGUxNmJlM2IyY2JfU2FsNENiOUhrcVFReURXalhuR3ZxM3hBY1p2bGg3VVNfVG9rZW46QTVadWIzUnUyb3lmcmt4QTFnOGNrSE1Qbk9SXzE3MTE5NjIzNjg6MTcxMTk2NTk2OF9WNA)

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=YWZkZTk4YmY1ZWRiOTk2YzBkMTFhNWQ3NmRmOWY3ZDFfc1ZzMlJOam1oRUVBbGxvZnNlV0ExbURjVFBnVkRGOWxfVG9rZW46T2lPZmJ6NHRsb3hVZHp4SllwQWNMY0pybk9lXzE3MTE5NjIzNjg6MTcxMTk2NTk2OF9WNA)

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NmQwNzY2M2JkOTZmZTdkYjk3MTg1NjAwZjgxOTk2MTNfSmQyZjZucmhOdm95UVhUS1JEVG1uTkVVMk5EZWxxZElfVG9rZW46UU44VmI4c3RTbzN5MlB4MFJyZ2NrT3k2bnJiXzE3MTE5NjIzNjg6MTcxMTk2NTk2OF9WNA)

1. 视频查看了beanPostProcessor后置处理器的源码，是在beanPostBeforeInitialization方法打的断点，然后通过查看方法栈的方式，看了一下栈里的方法调用情况，主要是在refresh方法里面的，然后一步一步到了执行初始化方法那里，前后有before和after初始化，然后这三个操作都在属性注入之后，相当于回顾了把 

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NWJjNDJlZGEyOTY0M2E2OGE3MTFmNGYxY2EwODg2ZmFfQ0I4UmpxV0V0WmduVE03RWhoRlVWWDdUS1VseWlHek1fVG9rZW46VEdsaWJ5dkNNb2ZEYmR4bmZTOWNJNGlrbk1iXzE3MTE5NjIzNjg6MTcxMTk2NTk2OF9WNA)

##  自动装配相关：

1. @Autowired注解 应用在**方法，参数，构造器，属性**上，**不管怎么用，原**则就是从容器中获取一个对应类型的组件bean，注入到对应的地方上，如对于方法，构造器和参数是通过参数注入。
2. Aware接口相关，Aware接口的功能是，针对于这个接口有许多子接口，选择性的实现对应的子接口，然后重写里面的方法，就会在容器创建期间，通过对应的processor去判断然后进行注入，

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ODM0YmExY2U0NTRkYTRlNTg3OTczY2Q5MWIyZmEyNjFfU3JvQTRpUjhoRnVZSE5vR0lQbXF0cGxrVndYT1VMRlpfVG9rZW46RE5jc2JBUnU3bzF6cnJ4OVg1QWNFWGpBbk5mXzE3MTE5NjIzNjg6MTcxMTk2NTk2OF9WNA)

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=YzFhMDQ2Y2MxNjFhMWY0NmM0MTM2ZDZkNDYwOWFiMmZfYnQwaUwzYk5WMkZtb2xJZUtCQnQ0VFdqRXZadkttUVNfVG9rZW46WnNJRmJ3SGc0b2ZFcmV4aWU0M2MybWR3bmZkXzE3MTE5NjIzNjg6MTcxMTk2NTk2OF9WNA)

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=M2I1Yjc5ZmI2N2RmMGQyNTk2MDY4NTdkNDY1NTdkMGZfVmlIUGtRcTlNZWZPcU5wbWtwRzQxdnVRQ3lYQ1R4cHBfVG9rZW46S2dwTmJGQ2RYb0trVFF4UGcwM2M4bk9VbnhlXzE3MTE5NjIzNjg6MTcxMTk2NTk2OF9WNA)

## AOP

  springAOP的步骤如下图

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=YjQ4YTc2MmYyZTU3MTc4NWYwOWQ1MmMwNTBhODdmOGNfem5sNUtnU1liSHNYeFQzeUVmVG1zSGRKZUlQRURHeUNfVG9rZW46Ukpka2JmUXlkb1R5QXF4Z3dmM2M2bjRsbnZmXzE3MTE5NjIzNjg6MTcxMTk2NTk2OF9WNA)

之前认为的那个，可能是动态代理，上面这个步骤的关键点就是两个类一个开启注解，分别为

1. 业务逻辑类，定义要被切面类包裹的目标
2. 切面类，加aspect注解，并且@before等注解方法，并标明要切入的点，书写好表达式（哪个类的哪个方法）
3. 然后在引入了这两个组件的配置类中，加上开启aop的注解。

### AOP实现原理

查看EnableAspectJAutoProxy注解，发现里面存在这样一层关系

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ZDM0YWU2YmIzMTU3ZjZhMzRiYzgxMjE1NWUwYmYzZDNfSU5INmFIbWo4YnpyY2FUd1FEYkRuckJEWXNmeVNvcFNfVG9rZW46WDViVWJNUlV4b3VrVWJ4T25vUGNJVktWblBmXzE3MTE5NjIzNjg6MTcxMTk2NTk2OF9WNA)

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=Y2RiOGE5YWUzZDRmZThlNTRlZDZhMDhmYTAzNmU1NzBfNXN1UHBKU2haRElkU2E0TWhpUDEwd212R1lPSm56YmZfVG9rZW46RXl1aWJEUVZWbzVpcWV4bDJVSmNDd3NlbkViXzE3MTE5NjIzNjg6MTcxMTk2NTk2OF9WNA)

未完待续

## 声明式事务

源码分析，待续

## 扩展原理

#### 扩展原理之beanFactoryPostProcessor，

是beanFactory的后置处理器，在beanFactory初始化完成后（这个是指所有的bean都创建完成后，还没实例化之前）调用，具体来说是在refresh中执行invokeBeanFactoryProcessors方法，然后在beanFatory中找到所有类型是invokeBeanFactoryProcessor的组件，执行他们的方法

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=YjdkMjdkMjM5YmVmZThlY2MxMzUzNDcyYmZjZjJjN2FfYWE0ZFVpeVNkNzhhUUtkUGtYTmVnWW1LaFRLb2tVUzFfVG9rZW46QnVuTWJ5blRtb0tIRVF4SmNJVWNnZXhmbk1oXzE3MTE5NjIzNjg6MTcxMTk2NTk2OF9WNA)

#### 扩展原理之beanDefinitionRegistryPostProcessor

它是在beanFa ctory创建之后，所有bean定义信息加载前，bean实例化之前调用，用来最后给容器添加一些组件时用的

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=Zjg4ODQ1OTE0OTJhMzM4NmFkN2RiODRhY2EyY2UwOWRfeVNXS0Z3b1BXWGFGcWI1UnUyWEZpdFQxWmN6VklDYVdfVG9rZW46S3JiWmJRQm92b2ZIdmd4ZGJub2NIVWlrbkRlXzE3MTE5NjIzNjg6MTcxMTk2NTk2OF9WNA)

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=Y2QzMTkwNGJjMmYwNmQwNjY2NDgxM2VmYjY0Y2Q4NDhfaHB2M3Y5NzZROTU2TXVlR1c4bXhma2hRbEZ6WWxiM2dfVG9rZW46TWZiUWJYN3ZzbzRKT3h4Y1lDWGN0RFBtbmJmXzE3MTE5NjIzNjg6MTcxMTk2NTk2OF9WNA)

#### ApplicationListener： 

待续

Spring容器创建过程ring.md…]()
