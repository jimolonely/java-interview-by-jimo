## @Qualifier的作用？

通过名称区分bean，一般和`Autowired`配合。

## 说一下@Resource的装配顺序

1. @Resource后面没有任何内容，默认通过name属性去匹配bean，找不到再按type去匹配
2. 指定了name或者type则根据指定的类型去匹配bean
3. 指定了name和type则根据指定的name和type去匹配bean，任何一个不匹配都将报错

区分一下@Autowired和@Resource两个注解的区别：

1. @Autowired默认按照byType方式进行bean匹配，@Resource默认按照byName方式进行bean匹配
2. @Autowired是Spring的注解，@Resource是J2EE的注解，这个看一下导入注解的时候这两个注解的包名就一清二楚了。

## Spring如何解决循环依赖问题？

二级map足矣解决循环依赖，三级map是为了解决代理对象的延迟加载，避免破坏bean的生命周期。 在DefaultSingletonBeanRegistry类里定义了3个map：

再看其 `getSingleton`方法，除了普通的对象，还有一种ObjectFactory创建的工厂对象，用来延迟加载。

```java
/** Cache of singleton objects: bean name to bean instance. */
private final Map<String, Object> singletonObjects=new ConcurrentHashMap<>(256);

/** Cache of singleton factories: bean name to ObjectFactory. */
private final Map<String, ObjectFactory<?>>singletonFactories=new HashMap<>(16);

/** Cache of early singleton objects: bean name to bean instance. */
private final Map<String, Object> earlySingletonObjects=new ConcurrentHashMap<>(16);
```

## @Import和@ImportResource注解是干嘛的？有何区别？
都是用来导入配置的，`@Import`一般用来导入`@Configuration`注解的类，而`@ImportResource`可以指定XML配置文件。 

## Spring中如何实现定时任务？
可以用`@Scheduled`注解，可以指定`cron`表达式，或`fixDelay`，`fixRate`。

## Scheduled注解背后的实现原理是什么？
是`TaskScheduler`接口，默认实现是`ThreadPoolTaskScheduler`，底层是调用java的原生`ScheduledThreadPoolExecutor`。

## AspectJ和Spring AOP的区别？
https://segmentfault.com/a/1190000022019122

一个是基于运行时增强，一个是基于编译时字节码增强，所以AspectJ性能更高，功能更全，更复杂。

## AOP中的PointCut和JoinPoint有什么区别？
如下面代码，一个`PointCut`可能切了很多方法，而每个方法在执行时就是一个`JoinPoint`，可以获得这个方法的参数,然后使用通知来做处理。
个人觉得`JoinPoint`是连接点，`PointCut`才是切点，有些资料是错的。

```java
/**
 * 所有controller方法
 */
@Pointcut("execution(public * com.jimo.controller.*Controller.*(..))")
public void allControllerMethod() {
}

@Around("allControllerMethod()")
public Object recordAccessLog(ProceedingJoinPoint jp) throws Throwable{
    // args
    String args=getArgs(jp.getArgs());
    ...
}
```

## FactoryBean和BeanFactory的区别是啥？
一个是创建bean的，一个是一类特殊的bean:工厂bean，这类bean的getObject可以得到bean。
这是工厂模式的体现。

## Spring里面用了哪些设计模式？
1. 工厂模式：`BeanFactory`
2. 代理模式：`AOP`
3. 单例模式: bean默认就是单例
4. 原型模式：bean可设置为原型实例
5. 适配器模式：`HandlerAdapter`
6. 模板方法模式：`JdbcTemplate`
7. 观察者模式：`Spring`里的事件和监听器，`ApplicationEvent`和`ApplicationListener`
8. 责任链模式：`HandlerExecutionChain`

## Spring的BeanFactory和ApplicationContext区别是什么？
`ApplicationContext`是`BeanFactory`的子接口，同时扩展了很多其他功能，比如国际化、事件监听，延迟加载等。

可以说，`ApplicationContext`是面向用户，`BeanFactory`是面向框架自身。

## Spring事务隔离级别和事务传播机制？
`TransactionDefinition`接口定义了7种传播机制和5种事务隔离级别。

事务传播机制，无非是事务嵌套情况下，存在与不存在是新建、报错、忽略还是挂起。

## Spring编程式事务如何实现？
https://cloud.tencent.com/developer/article/1697221

使用`TransactionTemplate`吧。

其原理是事务三要素：
1. 数据源：`DataSource`
2. 事务管理器：`PlatformTransactionManager`
3. 事务定义：`TransactionDefinition`

当使用注解时，是通过AOP动态给方法增加事务。

## Spring的启动流程？

参考：https://blog.csdn.net/a745233700/article/details/113761271

从 `ApplicationContext`的子类 `AnnotationConfigApplicationContext`的构造下手：

```java
	public AnnotationConfigApplicationContext(Class<?>... componentClasses) {
		this();
		register(componentClasses);
		refresh();
	}
```

分为3步：

1. 初始化
   * 创建默认的 `DefaultListableBeanFactory`
   * 还有一堆环境相关的类
2. 注册
   * 通过 `BeanDefinitionRegistry`来注册
3. 刷新
   * 是调用 `AbstractApplicationContext`的方法
   * 总共有 12 步，包括注册内置组件、事件监听、后置处理等，最后实例初始完发布刷新事件



