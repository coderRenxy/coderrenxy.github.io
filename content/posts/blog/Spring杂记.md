
---
title: "Spring杂记（顺着链条自上往下看）"
date: 2022-02-02T02:45:59+08:00
author: ["小任同学"]
draft: false # 是否为草稿
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
lastmod: 2022-05-05T23:33:37+08:00	#更新文章的时候手动改一下时间就可以
description: "本篇文章都是本人的理解，看不懂可以评论，会回复评论。"
tags: 
- Spring
- 框架

cover:
    image: "/img/spring.webp"
    caption: ""
    alt: ""
    relative: false
---


   
讲到Spring就一定绕不开IOC、AOP两个概念了，在我看来Spring的一切都基于IOC，所以先聊IOC吧。

# IOC

### 什么是 IOC
首先看看 IOC 的作用，我们可以试着写一个从 service 层到 dao 层的用例，会发现如果是一层一层实现了这个用例，将来要修改就要一层层改，
这样耦合度极高，而把控制权交给第三方（ Test 中
 new 一个 serviceImpl 来 set 一个 userDao），能达到解耦目的。此时，主动去 new 一个 dao 对象叫正向获取，而等着 serviceImpl 来 set 是等着别人给我这个对象，是反向获取。就像自己找对象（正向）和婚介公司分配对象（反向）。

 
 ### IOC 的两种容器及异同
 对于 IOC 最重要的是容器，容器管理着 Bean 的生命周期，控制着 Bean 的 DI（依赖注入），那 Spring 是怎么设计的容器？   
 Spring 提供两个接口用以表示容器，一个是 BeanFactory，一个是 ApplicationContext，咱们就聊聊异同吧。
  1. BeanFactory 粗暴简单，可以理解为一个 HashMap，key是BeanName，value 是 Bean 实例，通常只提供注册（put），获取（get）功能，我们称为低级容器。BeanFactory 是 Spring 底层 IoC 容器，ApplicationContext 是 BeanFactory 的子接口。在该接口中利用反射创建对象。
  2. ApplicationContext称为高级容器，因为他比BeanFactory多了更多功能，他继承了多个接口。因此具备更多功能，例如资源的获取、支持多种消息（例如jsp tag的支持）、对比BeanFactory多了工具级别的支持等等。所以名字也不是BeanFactory之类的工厂了，而是“应用上下文”，代表整个大容器的所有功能，该接口定义了一个refresh方法（刷新整个容器，即重新加载所有的bean）。
  3. 隶属 ApplicationContext 的 “高级容器”，依赖着 “低级容器”，这里说的是依赖，不是继承哦。他依赖着 “低级容器” 的 getBean 功能。而高级容器有更多的功能：支持不同的信息源头，可以访问文件资源，支持应用事件（Observer模式），而低级容器只负责加载Bean、获取Bean。值得一提的还有两个容器之间的区别。了解区别之前必须明白IOC在启动过程做了些什么操作，IOC启动过程分为两个阶段：  
 	1. 容器的启动：加载配置信息，分析配置信息。  
	2. Bean的实例：实例化对象，装配依赖，生命周期回调。
  4. 两者的区别：BeanFactory 延时加载，只有在使用某个 bean 时（即调用 getBean()方法时），才会对 bean 进行实例化，而 ApplicationContext
 在容器启动的时候，一次性完成两个阶段，因此BeanFactory在启动过程不能在容器启动阶段发现配置问题，而 ApplicationContext 可以，但是由于一次性实例化所有的 Bean，启动花费的时间也长。
### Bean的生命周期  
  其实吧，上面的 IOC 启动过程都与 Bean 的生命周期有关，聊到这里就避不开这个话题了。Bean 的生命周期：
  1. Bean 的定义：beanDefinitionReader（抽象接口约束）加载配置文件（xml、properties、注解、yaml）读取bean的定义信息并包装成BeanDefinition。
  2. 执行BeanFactoryPostProcessor 准备 BeanPostProcessor、广播器、监听器。（注：beanfactorypostprocessor 完成对 beanfactory 相关信息的修改和拓展(容器运行需要的对象)。beanpostprocessor 完成对bean的修改或拓展（用户自定义对象））。
  3. Bean 的实例化：在 ioc 中利用反射实例化所有的非懒加载的单例 bean。
  4. Bean 的初始化：  
  	1. Bean 的属性赋值：实例化后的对象还是一个空对象，根据 Bean 的元信息对该对象的所有属性进行赋值。即 PopulateBean 方法。  
  	2. 执行 Aware 接口的方法。Bean 分为两种，一种是用户 bean 对象，一种容器对象 bean（environment、applicationContext、beanFactory），aware 接口是为了使某些用户 bean 对象能够方便的获取容器bean对象。  
  	3. 执行 BeanPostProcessor（增强器）的 before 方法。“增强 Bean（AOP）”。  
  	4. 执行 init-method 方法。  
  	5. 执行 BeanPostProcessor（增强器）的 after 方法。  对应过程 before，这样就获得了完整对象。如果一个对象需要生成代理对象来增强 bean，会进行反射的普通创建一个实例化的对象，所以叫拓展。不是所有的 bean 都会增强，所以一定是会创建新的（代理）对象。  
 5.	Bean 的调用：有三种方式可以得到 Bean 并进行调用：
 	1. 使用 BeanWrapper。  
 	2. 使用 BeanFactory 。  
 	3. 使用 ApplicationContext。  
 6.	Bean的销毁：
 	1. 使用配置文件中的 destory-method 属性。  
 	2. 实现 *org.springframwork.bean.factory.DisposebleBean*接口。  

### Spring 中可以出现两个 ID 相同的 bean 吗，如果不行会在什么时候报错
分情况，同一个 spring 配置文件里不能存在 id 相同的 bean，会在解析 xml 文件转换为 BeanDefinition 阶段报错。  
不同的 spring 配置文件里可以存在 id 相同的两个 bean，默认会把多个 id 相同的 bean 进行覆盖。  
spring 3.x 版本后使用 @Configuration 进行配置的时候：  
- 同一个配置类中使用 @Bean 声明多个相同名字的 bean 默认只会注册第一个。  
- 使用 @Autowired  可能会提示找不到未注册的类。  
- 使用 @Resource 注解会在 bean 初始化之后依赖注入的时候可能会提示类型不匹配错误 

### IOC常见的实现方式
 IOC是个原理（基于工厂模式+反射机制），是把以前在工厂方法中写死的对象生成代码，改由配置文件来定义，真正的实现方式常见的有两种：
 		1、依赖注入。
 		2、依赖查找。
 
 两者都是调用相关接口获取bean对象，区别在于DI（依赖注入）是IoC容器启动时由容器帮你实现，DL（依赖查找）要手动。目前用到DL（依赖查找）的非常少了，所以来聊聊DI（依赖注入），依赖注入从XML配置上来说就是ref标签，对应的是Spring中的RuntimeBeanReference对象，实现方法如下。
 
 

 ### DI（依赖注入）的实现方式
  1. 构造器注入：构造器依赖注入通过容器触发一个类的构造器来实现的，通过构造器的参数注入相关依赖对象。用xml文件配置就是property中通过construct-arg来指定构造器的参数，用注解配置就是在**构造方法**上加上@Autowire注解。这种方式好比学渣从一开始就赖上了一个学霸，并且和这个学霸建立了长期合作关系。
  2. setter注入：通过 setter 方法注入依赖对象，也可以理解为字段注入。通过Xml配置就是property中指定name=”age”或Age；ref=”.....”。因为Spring会自动的将首字母大写再在前面加上set，这里也可以看到，有关的是set方法后的名称，而与属性（成员变量无关）。用注解来写就是在**setter方法**上加上@Autowire注解。这种方式学霸和学渣只是暂时的合作关系，如果学渣赖上了另一个学霸（调用set()方法传入了另一个对象），那么学渣和上一学霸的合作关系就结束了。
  3. 属性注入（方法参数注入）：定义**成员变量**来添加@Autowire注入。这种方式不建议使用，但是工作中用的最多，因为真的方便。这么方便为什么不推荐？如果是IOC以外的环境，除了使用反射来提供他需要的依赖，无法复用该实现类。  
    
  那setter注入和构造器注入用哪个？看上面我的描述，构造器不是有点强买强卖的意思？所以构造器参数实现强制依赖，setter方法实现可选依赖。构造器注入可以保证有序的被注入，而setter方法注入是通过反射机制注入，无法保证注入顺序。构造器注入不允许出现循环依赖，因此被注入的对象需要保证能实例化，构造器依赖初始化时对象才注入依赖对象，保证了bean初始化后就是不变的对象。setter方法的循环依赖Spring已经解决了，先聊聊循环依赖吧。  
    
  这里多嘴提一句Autowired：@Autowired默认是byType，类型一样时会根据id查找，默认的id为类名（自动改为首字母小写）。找到了直接注入，找不到报错。如果指定id（别名）就是用@Qualifier。如果@Autowired添加在方法上时，此方法在创建对象的时候会默认调用，同时方法中的参数会自动进行装配。@Autowired也能用在方法的参数上指定当前属性的别名。Jdk提供了@Resource和@Autowired一样的功能。Resource可以在其他框架中用，是按照id进行装配的，id找不到就用type。Autowired通过反射来注入。
 

 ### 循环依赖  
 （只有单例Bean才会出现循环依赖）  
 
 如果一段依赖关系为beanA-->beanB-->beanC-->beanA，这就是循环依赖。如果没有最后一个beanA而是beanA-->beanB-->beanC，此时Spring将创建beanC，然后创建beanB（并将beanC注入beanB）然后创建beanA（并将beanB注入beanA），但是在有两次beanA时，Spring无法决定应该首先创建哪个bean（注意：这里是创建，不是初始化，初始化在上文Bean的生命周期有记载，是根据用户xml中对bean定义的顺序来加载，若有依赖，先用占位符_代替，那为什么不在加载Bean的时候直接注入呢？因为我们并不能要求用户按照顺序定义Bean，这样是不人道的！可能A依赖于B，但是B还没有加载好），因为他们彼此依赖，这个情况下Spring将在加载上下文时引发BeanCurrentlyInCreationException。使用构造方法注入时，他可能在Spring中发生，其他类型应该无此问题（setter注入的循环依赖已经被Spring解决）。
 <hr>
  那在构造器注入中如何解决循环依赖呢?其实方法很多，当然我们只讲流行的，况且最好的方法就是重新设计或者用setter注入，简单了解一下吧。
  
 
  1. 使用@Lazy放在构造方法参数列表的参数前，意思就是懒洋洋的初始化其中一个bean。它不是完全初始化bean，而是创建一个代理将它注入到另一个bean。注入的bean只有第一次需要时才会完全创建。用人话来讲就是第一次被需要才创建，之后在需要这个bean就是创建它的代理对象。
  2. 在其中一个bean上加@AutoWired，其他依赖项上使用@PostConstruct。
 
 		
 		那我们肯定还是要了解一下setter注入中Spring是怎么解决循环依赖的吧！
 <hr>
 		先透个实底：Spring通过提前暴露对象的方式解决循环依赖问题，即
 对“半成品对象”（实例化后、初始化前的对象叫做“半成品对象”）设置缓存来预存对象，等后续再根据A对象的引用来完成赋值操作，实例化后、初始化前的对象叫做“半成品对象”。这里缓存有三级。了解三级缓存前，先了解spring常用的6个方法：	

```java 
 getBean-->doGetBean-->createBean-->doCreateBean-->createInstance-->populateBean 
```

 		  那三层分别什么作用?   
    一级缓存singletonObjects：存放成品对象。             
    二级缓存earlySingletonObjects：存放半成品对象。
    三级缓存singletonFactories：存放lamdb表达式。
   
   
 **为何要有三级？只用第一级行不行？只用一、二级行不行? 	 别着急，小任细细道来。**
 
 如果只有一级缓存：那么意味着半成品对象和成品对象都要放到一级缓存，那就有可能获取到对象的非完整状态，此时不可以使用。
 
 如果只有一二级缓存：没有AOP的时候就可以，三级缓存是解决代理过程中的循环依赖。
 
  - [ ] **总结**一下以上：每次我们在获取对象的时候，是通过对象的name来获取bean的，如果原始对象和代理对象同时存在的话，那么我通过名字再进行获取的时候应该选择哪个？无法选择的，其实还有最核心的点，你怎么能够确认对象什么时候需要被引用呢？使用lambda表达式其实代表了一种回调机制，当需要使用当前对象的时候，通过lamdba表达式来最终返回一个确定的最终版本对象，而不需要判断几个对象，因为是替换的过程，所以只能有一个。接下来给IOC留个结尾干巴的面试题吧，干就完了！
 

 ### Spring核心类
 
  1. BeanFactory：产生一个新的实例，可以实现单例模式。
  2. BeanWrapper：提供统一的get及set方法。
  3. ApplicationContext:提供框架的实现，包括BeanFactory的所有功能。
 
 
 

 ### Spring中的设计模式
  1. 工厂模式：Spring使用工厂模式，通过BeanFactory和ApplicationContext来创建对象。
  2. 单例模式：Bean默认为单例模式。
  3. 代理模式：Spring的AOP功能用到了JDK的动态代理和CGLIB字节码生成技术。
  4. 模板方法：可以将相同部分的代码放在父类中，而将不同的代码放入不同的子类中，用来解决代码重复的问题。比如RestTemplate, JmsTemplate, JpaTemplate。
  5. 适配器模式：Spring AOP的增强或通知（Advice）使用到了适配器模式，Spring MVC中也是用到了适配器模式适配Controller。
  6. 策略模式：例如Resource的实现类，针对不同的资源文件，实现了不同方式的资源获取策略。
  7. 观察者模式：Spring事件驱动模型就是观察者模式的一个经典应用。
  8. 桥接模式：可以根据客户的需求能够动态切换不同的数据源。比如我们的项目需要连接多个数据库，客户在每次访问中根据需要会去访问不同的数据库。
 

 ### Bean的作用域
 
  1. singleton：这种bean范围是默认的，这种范围确保不管接受到多少个请求，每个容器中只有一个bean的实例，单例的模式由BeanFactory自身来维护。
  2. prototype：原型范围与单例范围相反，为每一个bean请求提供一个实例。
  3. request：在请求Bean范围内会对每一个来自客户端的网络请求创建一个实例，在请求完成以后，Bean会失效并被垃圾回收器回收。
  4. session：与请求范围类似，确保每个session中有一个bean的实例，在session过期后，bean会随之失效。
  5. globalsession：每个全局的HTTP Session，使用session定义的Bean都将产生一个新实例。典型情况下，仅在使用portlet context的时候有效。（不知所云）
 
 


 ### ApplicationContext 通常的实现
  1. FileSystemXmlApplicationContext：此容器从一个 XML 文件中加载beans 的定义，XML Bean 配置文件的全路径名必须提供给它的构造函数。
  2. ClassPathXmlApplicationContext：此容器也从一个 XML 文件中加载beans 的定义，这里，你需要正确设置 classpath 因为这个容器将在 classpath 里找 bean 配置。
  3. WebXmlApplicationContext：此容器加载一个 XML 文件，此文件定义了一个 Web 应用的所有 bean。



# AOP
温馨提示：上面IOC没捋顺就别往下看AOP了

### 通知  

 大家都是怎么描述AOP和其中各种名词呢? 听听小任的见解。  
 想象一下：方法是纵向的，而各种打印的日志信息就都是横向的，横纵向的交汇点就是**连接点**，通俗来讲一个方法中可以填入额外的代码的地方都叫做**连接点**，而实际填充了代码的叫做**切入点**，**切入点**是**连接点**的子集。**切面类**就是存放打印日志方法的logUtil类，每一个**切面类**对应的方法都是**横切面**，使用AOP的话就在切面类上加@Aspect。
 通知注解的几种类型：
 
  1. @Before前置通知：方法执行前执行。
  2. @After后置通知：方法执行后执行。
  3.  @AfterReturing返回通知：结果返回后运行。
  4. @AfterThrowing异常通知：出现异常时使用。
  5.  @Around环绕通知：其他四个注解都是方法被调用就会根据情景来执行的，比如异常了就执行@AfterThrowing，其他四个注解都是被方法绑死，而环绕通知比较特殊，他能决定一个类是否需要返回对象，能决定该方法是否被调用。它是spring框架为我们提供的一种可以在代码中手动控制增强方法何时执行的方式。
 
 
 执行顺序：环绕前置通知-->before-->环绕后置通知-->after-->afterReturing或者：环绕前置通知-->before-->环绕后置通知-->after-->afterThrowing。
 
 这些通知都需要指定方法的权限修饰符、方法的返回值类型、方法的全限定名。在方法的参数的列表中不要随便添加参数值，会异常，如果需要参数就在参数列表加上joinpoint来getArgs（获取参数列表），返回值在注解中指定（returning=”result”）之后才能从在方法的参数列表指定。execution精确匹配的方式其实并不友好，一般用的是通配符（*和.）的方式也可以多个execution来进行逻辑运算（与或非）。

需要注意的是，一个通知只能针对一个特定的连接点（即切点）。因此，如果需要在不同的切点上应用不同的通知，就需要定义多个切面类。 在一个切面中放置多类通知是正常的，并且常用。  

### 代理  

讲到AOP一定逃不掉的一个东西：动态代理的实现   
 有动态，那静态呢？ 动态代理的代理类是动态生成的 ，静态代理的代理类是我们提前写好的。 那为什么要有动态代理呢？且先看看静态代理的优劣：  
 **静态代理的好处**是：我们的真实角色更加纯粹 . 不再去关注一些公共的事情 ，公共的业务由代理来完成，
 实现了业务的分工，公共业务发生扩展时变得更加集中和方便。说白了就是（动态、静态）代理类的好处。  
 **静态代理的缺点**是：类多了 , 多了代理类 , 工作量变大了 . 开发效率降低 。
 我们想要静态代理的好处，又不想要静态代理的缺点，所以 , 就有了动态代理 !
 
 动态代理的核心：一个动态代理 , 一般代理某一类业务 , 一个动态代理可以代理多个类，代理的是接口！
 
 
 动态代理分为两类 : 一类是基于接口动态代理 , 一类是基于类的动态代理。 两种实现方式：
  - JDK代理：基于接口的动态代理。
  - CGlib代理：基于类的动态代理。
  - 二者区别是：JDK代理只能对实现接口的类生成代理，利用反射机制生成一个匿名类，CGlib是针对类实现代理，对指定的类生成一个子类，并覆盖其中的方法，但是这种通过继承类的实现方式不能代理final修饰的类。
  
  如何实现JDK动态代理？		 （JDK 动态代理最核心的一个接口和方法如下）
  - InvocationHandler 接口：使用方法首先是需要实现该接口，并且我们可以在 invoke方法中调用被代理类的方法并获得返回值，自然也可以在调用该方法的前后去做一些额外的事情，从而实现动态代理。传入的参数如下：  
 		1.  proxy：被代理的类的实例；  
 		2. method：调用被代理的类的方法；  
 		3. args：该方法需要的参数；  
  - Proxy 类中的 newProxyInstance 方法：该方法会返回一个被修改过的类的实例，从而可以自由的调用该实例的方法。传入参数如下：  
  		1. loader：被代理的类的类加载器；  
  		2. interfaces：被代理类的接口数组；  
  		3. invocationHandler：调用处理器类的对象实例；  



### Spring的事务
 
 事务分为两种：声明式事务、编程式事务。一一介绍一下，先来精简的。
 
 
 
  -  编程式事务：在代码中直接加入处理逻辑，可能需要在代码中显式调用beginTransaction、commit、rollback方法。
  -  声明式事务：方法外部添加@Transational注解或在配置文件中直接定义，将事务代码和业务方法分离，以声明的方式实现事务管理。AOP恰好能完成，通过AOP方法模块化，进而实现声明式事务。其中，声明式事务用法值得一提其中的属性：  
  	1. isolation：隔离级别。（大写不方便本人认读，所以还是小写）事务应该不会陌生吧?跟着MySQL/Oracle走，如果是MySQL，那隔离级别默认为RR（可重复读Read
 Repeated）Oracle则是默认RC（读已提交Read Commited)。  
  	2. timeout：超过时间。   
  	3. readonly：设置为只读事务。   
    4. noRollBackfor：设置为发生该异常也不回滚（指定异常类的类名.class）只对特定异常类起作用。   
    5. rollBackfor发生指定异常回滚（指定异常类的类名.class）只对特定异常类起作用。  

>持续更新中........    敬请期待
  



