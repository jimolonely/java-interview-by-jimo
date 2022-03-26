## ApplicationRunner和CommandLineRunner有什么区别？
他们都可以实现程序启动后执行一些操作，都有run方法。
区别就在参数不同，ApplicationRunner是ApplicationArguments，对原生args做了一些解析。而CommandLineRunner拿到的就是原生命令行参数数组。

## Spring Boot 有哪几种读取配置的方式？
Spring Boot 可以通过 @PropertySource,@Value,@Environment,@ConfigurationPropertie注解来绑定变量。
其中@Environment是注入环境变量，类似通过System.getenv读取。

## 如何自定义一个springboot的starter？
参考：https://xie.infoq.cn/article/68621c5f5c1dc16312e0a52e4

1. 取一个标准的名字：xxx-spring-boot-stater
2. 引入依赖: 
spring-boot-starter一定要的,
spring-boot-configuration-processor可以生成配置信息，方便IDEA提示
其他业务相关依赖
3. 编写配置类，如果有的话，使用`ConfigurationProperties`注解
4. 实现bean，也就是业务逻辑
5. 编写bean的注入类，也就是`Configuration`注解，需要加上 `EnableAutoConfiguration`注解引入自定义配置类
6. 加入`META-INF/spring.factories`文件，配置第5步的配置类
7. 测试打包发布

## EnableAutoConfiguration是干什么的？原理是什么？
参考：https://www.jianshu.com/p/464d04c36fb1

简单来说，`EnableAUtoConfiguration`是`springboot-autoconfigure`模块的功能，
使用`AutoConfigurationImportSelector`(里面使用的`SpringFactoriesLoader`)导入`META-INF`下的`spring.factories`配置里面定义的配置类，否则可能找不到。

## ComponentScan注解和EnableAutoConfiguration有什么区别？
`ComponentScan`是`spring-context`的原生注解，他可以扫描指定包下标注了`@Configuration`注解的类，如果没有指定包，就是当前类下的包。

而`EnableAutoConfiguration`是`spring-boot`的配置注解，他也是去寻找并注入`Configuration`注解的bean，不过是使用`SpringFactoriesLoader`去寻找，一般自动配置的这些bean都是可选的，如果系统里已经有了，不会使用自动配置的。

## SpringBoot加载配置的顺序？
https://docs.spring.io/spring-boot/docs/1.2.3.RELEASE/reference/html/boot-features-external-config.html
1. 命令行参数
2. System.getProperties()
3. OS环境变量
4. jar包外部带profile的配置文件：application-{profile}.properties
5. jar包内部带profile的配置文件：application-{profile}.properties
6. jar包外部配置文件：application.properties
7. jar包内部配置文件：application.properties
8. Configuration注解类上的PropertySource注解里的配置

