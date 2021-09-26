1、Spring是什么?

Spring是一个轻量级的IoC和AOP容器框架。是为Java应用程序提供基础性服务的一套框架，目的是用于简化企业应用程序的开发，它使得开发者只需要关心业务需求。主要包括以下七个模块：

Spring Context：提供框架式的Bean访问方式，以及企业级功能（JNDI、定时任务等）；
Spring Core：核心类库，所有功能都依赖于该类库，提供IOC和DI服务；
Spring AOP：AOP服务；
Spring Web：提供了基本的面向Web的综合特性，提供对常见框架如Struts2的支持，Spring能够管理这些框架，将Spring的资源注入给框架，也能在这些框架的前后插入拦截器；
Spring MVC：提供面向Web应用的Model-View-Controller，即MVC实现。
Spring DAO：对JDBC的抽象封装，简化了数据访问异常的处理，并能统一管理JDBC事务；
Spring ORM：对现有的ORM框架的支持；



Spring生命周期
4.1 生命周期的三个方法：
1）创建方法init()
2）销毁方法destory()
3）普通方法service()


Spring IOC
Ioc—Inversion of Control，即“控制反转”，不是什么技术，而是一种设计思想。在Java开发中，Ioc意味着将你设计好的对象交给容器控制，而不是传统的在你的对象内部直接控制。如何理解好Ioc呢？理解好Ioc的关键是要明确“谁控制谁，控制什么，为何是反转（有反转就应该有正转了），哪些方面反转了”，那我们来深入分析一下：

　　●谁控制谁，控制什么：传统Java SE程序设计，我们直接在对象内部通过new进行创建对象，是程序主动去创建依赖对象；而IoC是有专门一个容器来创建这些对象，即由Ioc容器来控制对 象的创建；谁控制谁？当然是IoC 容器控制了对象；控制什么？那就是主要控制了外部资源获取（不只是对象包括比如文件等）。

　　●为何是反转，哪些方面反转了：有反转就有正转，传统应用程序是由我们自己在对象中主动控制去直接获取依赖对象，也就是正转；而反转则是由容器来帮忙创建及注入依赖对象；为何是反转？因为由容器帮我们查找及注入依赖对象，对象只是被动的接受依赖对象，所以是反转；哪些方面反转了？依赖对象的获取被反转了。


依赖注入(Dependency Injection)和控制反转(Inversion of Control)是同一个概念。
具体含义是:当某个角色(可能是一个Java实例，调用者)需要另一个角色(另一个Java实例，被调用者)的协助时，在 传统的程序设计过程中，通常由调用者来创建被调用者的实例。但在Spring里，创建被调用者的工作不再由调用者来完成，因此称为控制反转；创建被调用
者实例的工作通常由Spring容器来完成，然后注入调用者，因此也称为依赖注入。


AOP

	AOP实现的关键在于 代理模式，AOP代理主要分为静态代理和动态代理。静态代理的代表为AspectJ；动态代理则以Spring AOP为代表。
	（1）AspectJ是静态代理，也称为编译时增强，AOP框架会在编译阶段生成AOP代理类，并将AspectJ(切面)织入到Java字节码中，运行的时候就是增强之后的AOP对象。
	（2）Spring AOP使用的动态代理，所谓的动态代理就是说AOP框架不会去修改字节码，而是每次运行时在内存中临时为方法生成一个AOP对象，这个AOP对象包含了目标对象的全部方法，并且在特定的切点做了增强处理，并回调原对象的方法。
	Spring AOP中的动态代理主要有两种方式，JDK动态代理和CGLIB动态代理：

	用了动态代理的思想
	AOP是能够让我们在不影响原有功能的前提下，为软件横向扩展功能。 那么横向扩展怎么理解呢，我们在WEB项目开发中，通常都遵守三层原则，包括控制层（Controller）->业务层（Service）->数据层（dao）,那么从这个结构下来的为纵向，它具体的某一层就是我们所说的横向。我们的AOP就是可以作用于这某一个横向模块当中的所有方法。
	
	我们在来看一下AOP和OOP的区别：AOP是OOP的补充，当我们需要为多个对象引入一个公共行为，比如日志，操作记录等，就需要在每个对象中引用公共行为，这样程序就产生了大量的重复代码，使用AOP可以完美解决这个问题。

接下来介绍一下提到AOP就必须要了解的知识点：

	切面：拦截器类，其中会定义切点以及通知
	切点：具体拦截的某个业务点。
	通知：切面当中的方法，声明通知方法在目标业务层的执行位置，通知类型如下：
	前置通知：@Before 在目标业务方法执行之前执行
	后置通知：@After 在目标业务方法执行之后执行
	返回通知：@AfterReturning 在目标业务方法返回结果之后执行
	异常通知：@AfterThrowing 在目标业务方法抛出异常之后
	环绕通知：@Around 功能强大，可代替以上四种通知，还可以控制目标业务方法是否执行以及何时执行
	
## Spring的两种动态代理：Jdk和Cglib 的区别和实现  
	一、原理区别：
	java动态代理是利用反射机制生成一个实现代理接口的匿名类，在调用具体方法前调用InvokeHandler来处理。
	而cglib动态代理是利用asm开源包，对代理对象类的class文件加载进来，通过修改其字节码生成子类来处理。
	1、如果目标对象实现了接口，默认情况下会采用JDK的动态代理实现AOP 
	2、如果目标对象实现了接口，可以强制使用CGLIB实现AOP 
	3、如果目标对象没有实现了接口，必须采用CGLIB库，spring会自动在JDK动态代理和CGLIB之间转换
	如何强制使用CGLIB实现AOP？
	 （1）添加CGLIB库，SPRING_HOME/cglib/*.jar
	 （2）在spring配置文件中加入<aop:aspectj-autoproxy proxy-target-class="true"/>
	
	JDK动态代理和CGLIB字节码生成的区别？
	 （1）JDK动态代理只能对实现了接口的类生成代理，而不能针对类
	 （2）CGLIB是针对类实现代理，主要是对指定的类生成一个子类，覆盖其中的方法
	   因为是继承，所以该类或方法最好不要声明成final 

Spring容器的启动流程：

详细内容可以阅读这篇文章：https://blog.csdn.net/a745233700/article/details/113761271

（1）初始化Spring容器，注册内置的BeanPostProcessor的BeanDefinition到容器中：

	① 实例化BeanFactory【DefaultListableBeanFactory】工厂，用于生成Bean对象
	② 实例化BeanDefinitionReader注解配置读取器，用于对特定注解（如@Service、@Repository）的类进行读取转化成  BeanDefinition 对象，（BeanDefinition 是 Spring 中极其重要的一个概念，它存储了 bean 对象的所有特征信息，如是否单例，是否懒加载，
	、factoryBeanName 等）
	③ 实例化ClassPathBeanDefinitionScanner路径扫描器，用于对指定的包目录进行扫描查找 bean 对象
（2）将配置类的BeanDefinition注册到容器中：

（3）调用refresh()方法刷新容器：

	① prepareRefresh()刷新前的预处理：
	② obtainFreshBeanFactory()：获取在容器初始化时创建的BeanFactory：
	③ prepareBeanFactory(beanFactory)：BeanFactory的预处理工作，向容器中添加一些组件：
	④ postProcessBeanFactory(beanFactory)：子类重写该方法，可以实现在BeanFactory创建并预处理完成以后做进一步的设置
	⑤ invokeBeanFactoryPostProcessors(beanFactory)：在BeanFactory标准初始化之后执行BeanFactoryPostProcessor的方法，即
	BeanFactory的后置处理器：
	⑥ registerBeanPostProcessors(beanFactory)：向容器中注册Bean的后置处理器BeanPostProcessor，它的主要作用是干预Spring初始化
	bean的流程，从而完成代理、自动注入、循环依赖等功能
	⑦ initMessageSource()：初始化MessageSource组件，主要用于做国际化功能，消息绑定与消息解析：
	⑧ initApplicationEventMulticaster()：初始化事件派发器，在注册监听器时会用到：
	⑨ onRefresh()：留给子容器、子类重写这个方法，在容器刷新的时候可以自定义逻辑
	⑩ registerListeners()：注册监听器：将容器中所有的ApplicationListener注册到事件派发器中，并派发之前步骤产生的事件：
	⑪  finishBeanFactoryInitialization(beanFactory)：初始化所有剩下的单实例bean，核心方法是preInstantiateSingletons()，会调用
	getBean()方法创建对象；
	⑫ finishRefresh()：发布BeanFactory容器刷新完成事件：
 

8、BeanFactory和ApplicationContext有什么区别？

        BeanFactory和ApplicationContext是Spring的两大核心接口，都可以当做Spring的容器。

	（1）BeanFactory是Spring里面最底层的接口，是IoC的核心，定义了IoC的基本功能，包含了各种Bean的定义、加载、实例化，依赖注入和
	生命周期管理。ApplicationContext接口作为BeanFactory的子类，除了提供BeanFactory所具有的功能外，还提供了更完整的框架功能：
	
	继承MessageSource，因此支持国际化。
	资源文件访问，如URL和文件（ResourceLoader）。
	载入多个（有继承关系）上下文（即同时加载多个配置文件） ，使得每一个上下文都专注于一个特定的层次，比如应用的web层。
	提供在监听器中注册bean的事件。
	（2）①BeanFactroy采用的是延迟加载形式来注入Bean的，只有在使用到某个Bean时(调用getBean())，才对该Bean进行加载实例化。这样，我们就不能提前发现一些存在的Spring的配置问题。如果Bean的某一个属性没有注入，BeanFacotry加载后，直至第一次使用调用
	getBean方法才会抛出异常。
	
	        ②ApplicationContext，它是在容器启动时，一次性创建了所有的Bean。这样，在容器启动时，我们就可以发现Spring中存在的配置错
	误，这样有利于检查所依赖属性是否注入。 
	
	        ③ApplicationContext启动后预载入所有的单实例Bean，所以在运行的时候速度比较快，因为它们已经创建好了。相对于BeanFactory，
	ApplicationContext 唯一的不足是占用内存空间，当应用程序配置Bean较多时，程序启动较慢。
	
	（3）BeanFactory和ApplicationContext都支持BeanPostProcessor、BeanFactoryPostProcessor的使用，但两者之间的区别是：BeanFactory需要
	手动注册，而ApplicationContext则是自动注册。
	
	（4）BeanFactory通常以编程的方式被创建，ApplicationContext还能以声明的方式创建，如使用ContextLoader。

10、 Spring中bean的作用域：

（1）singleton：默认作用域，单例bean，每个容器中只有一个bean的实例。

（2）prototype：为每一个bean请求创建一个实例。

（3）request：为每一个request请求创建一个实例，在请求完成以后，bean会失效并被垃圾回收器回收。

（4）session：与request范围类似，同一个session会话共享一个实例，不同会话使用不同的实例。

（5）global-session：全局作用域，所有会话共享一个实例。如果想要声明让所有会话共享的存储变量的话，那么这全局变量需要存储在global-session中。

Spring框架中的Bean是线程安全的么？如果线程不安全，那么如何处理？

（1）对于prototype作用域的Bean，每次都创建一个新对象，也就是线程之间不存在Bean共享，因此不会有线程安全问题。

（2）对于singleton作用域的Bean，所有的线程都共享一个单例实例的Bean，因此是存在线程安全问题的。但是如果单例Bean是一个无状态Bean，也就是线程中的操作不会对Bean的成员执行查询以外的操作，那么这个单例Bean是线程安全的。比如Controller类、Service类和Dao等，这些Bean大多是无状态的，只关注于方法本身。

有状态Bean(Stateful Bean) ：就是有实例变量的对象，可以保存数据，是非线程安全的。

无状态Bean(Stateless Bean)：就是没有实例变量的对象，不能保存数据，是不变类，是线程安全的。

对于有状态的bean（比如Model和View），就需要自行保证线程安全，最浅显的解决办法就是将有状态的bean的作用域由“singleton”改为“prototype”。

也可以采用ThreadLocal解决线程安全问题，为每个线程提供一个独立的变量副本，不同线程只操作自己线程的副本变量。

12、Spring基于xml注入bean的几种方式：
	• set()方法注入；
	• 构造器注入：①通过index设置参数的位置；②通过type设置参数类型；
	• 静态工厂注入；
	• 实例工厂；
Spring如何解决循环依赖问题（三级缓存）
https://blog.csdn.net/a745233700/article/details/110914620

14、Spring的自动装配：
（1）在Spring框架xml配置中共有5种自动装配：
	no：默认的方式是不进行自动装配的，通过手工设置ref属性来进行装配bean。
	byName：通过bean的名称进行自动装配，如果一个bean的 property 与另一bean 的name 相同，就进行自动装配。 
	byType：通过参数的数据类型进行自动装配。
	constructor：利用构造函数进行装配，并且构造函数的参数通过byType进行装配。
	autodetect：自动探测，如果有构造方法，通过 construct的方式自动装配，否则使用 byType的方式自动装配。

（2）基于注解的自动装配方式：
	(1) @Autowired默认是按照类型装配注入的，默认情况下它要求依赖对象必须存在（可以设置它required属性为false）。
	(2) @Resource默认是按照名称来装配注入的，只有当找不到与名称匹配的bean才会按照类型来装配注入。
15、Spring事务的实现方式和实现原理：

	Spring事务的本质其实就是数据库对事务的支持，没有数据库的事务支持，spring是无法提供事务功能的。Spring只提供统一事务管理接口，具体实现都是由各数据库自己实现，数据库事务的提交和回滚是通过binlog或者undo log实现的。Spring会在事务开始时，根据当前环境中设置的隔离级别，调整数据库隔离级别，由此保持一致。
（1）Spring事务的种类：

	spring支持编程式事务管理和声明式事务管理两种方式：
	
	①编程式事务管理使用TransactionTemplate。
	
	②声明式事务管理建立在AOP之上的。其本质是通过AOP功能，对方法前后进行拦截，将事务处理的功能编织到拦截的方法中，也就是在目
	标方法开始之前启动一个事务，在执行完目标方法之后根据执行情况提交或者回滚事务。
	
	
1、什么是Spring MVC ？简单介绍下你对springMVC的理解?

	Spring MVC是一个基于Java的实现了MVC设计模式的请求驱动类型的轻量级Web框架，通过把Model，View，Controller分离，将web层进行职责解耦，把复杂的web应用分成逻辑清晰的几部分，简化开发，减少出错，方便组内开发人员之间的配合。

 

2、SpringMVC的流程？

	（1）用户发送请求至前端控制器DispatcherServlet；
	（2）DispatcherServlet收到请求后，调用HandlerMapping处理器映射器，请求获取Handler；
	（3）处理器映射器根据请求url找到具体的处理器Handler，生成处理器对象及处理器拦截器(如果有则生成)，一并返回给DispatcherServlet；
	（4）DispatcherServlet 调用 HandlerAdapter处理器适配器，请求执行Handler；
	（5）HandlerAdapter 经过适配调用 具体处理器进行处理业务逻辑；
	（6）Handler执行完成返回ModelAndView；
	（7）HandlerAdapter将Handler执行结果ModelAndView返回给DispatcherServlet；
	（8）DispatcherServlet将ModelAndView传给ViewResolver视图解析器进行解析；
	（9）ViewResolver解析后返回具体View；
	（10）DispatcherServlet对View进行渲染视图（即将模型数据填充至视图中）
	（11）DispatcherServlet响应用户。
		
	
		
	
	
	
		
	
	前端控制器 DispatcherServlet：接收请求、响应结果，相当于转发器，有了DispatcherServlet 就减少了其它组件之间的耦合度。
	处理器映射器 HandlerMapping：根据请求的URL来查找Handler
	处理器适配器 HandlerAdapter：负责执行Handler
	处理器 Handler：处理器，需要程序员开发
	视图解析器 ViewResolver：进行视图的解析，根据视图逻辑名将ModelAndView解析成真正的视图（view）
	视图View：View是一个接口， 它的实现类支持不同的视图类型，如jsp，freemarker，pdf等等

5、 SpringMVC常用的注解有哪些？

	@RequestMapping：用于处理请求 url 映射的注解，可用于类或方法上。用于类上，则表示类中的所有响应请求的方法都是以该地址作为父路径。
	@RequestBody：注解实现接收http请求的json数据，将json转换为java对象。
	@ResponseBody：注解实现将conreoller方法返回对象转化为json对象响应给客户。


Spring Boot是什么？

	Spring Boot 伴随spring4.0诞生
	Spring Boot就是一个内嵌web容器（tomcat/jetty）的可执行程序的框架。
	你开发的web应用不需要作为war包部署到容器中，而是作为一个可执行程序，启动时把web服务器配置好，加载起来
springboot和spring 的区别?

	对第三方插件封装和整合，提供第三方接口
	无需配置复杂的xml
	核心功能，自动配置
	内嵌式web服务器（Tomcat\jetty）等
	提供POM，简化maven配置
	
SpringBoot主要解决的问题如下：

	1、spring Boot 使配置变简单
	2、spring Boot 使编码变简单 
	
	
	• 如何解决循环依赖，Spring主要的思路就是依据三级缓存，在实例化A时调用doGetBean，发现A依赖的B的实例，此时调用doGetBean去实例B，实例化的B的时候发现又依赖A，如果不解决这个循环依赖的话此时的doGetBean将会无限循环下去，导致内存溢出，程序奔溃。spring引用了一个早期对象，并且把这个"早期引用"并将其注入到容器中，让B先完成实例化，此时A就获取B的引用，完成实例化。三级缓存
	• Spring能够轻松的解决属性的循环依赖正是用到了三级缓存，在AbstractBeanFactory中有详细的注释。
	• 一级缓存：singletonObjects，存放完全实例化属性赋值完成的Bean，直接可以使用。二级缓存：earlySingletonObjects，存放早期Bean的引用，尚未属性装配的Bean。三级缓存：singletonFactories，三级缓存，存放实例化完成的Bean工厂。
	
解决循环引用

	这三级缓存分别指：
	
	 一级 singletonObjects： 单例对象工厂的cache 
	 二级 earlySingletonObjects ：正在创建中的Bean。【用于检测循环引用，与singletonFactories互斥】
	三级  singletonFactories：存储创建中的Bean
	
	Spring首先从一级缓存singletonObjects中获取。如果获取不到，并且对象正在创建中，就再从二级缓存earlySingletonObjects中获取。如果还是获取不到且允许singletonFactories通过getObject()获取，就从三级缓存singletonFactory.getObject()(三级缓存)获取，如果获取到了则：从singletonFactories中移除，并放入earlySingletonObjects中。其实也就是从三级缓存移动到了二级缓存。
	从上面三级缓存的分析，我们可以知道，Spring解决循环依赖的诀窍就在于singletonFactories这个三级cache。

		


			


