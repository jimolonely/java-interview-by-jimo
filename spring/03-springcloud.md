## Eureka和ZK作为注册中心的区别？
Eureka保证的是AP，ZK保证的是CP，但在微服务下高可用更重要，可以容忍短时间的不一致。

## Feign,Ribbon,Hystrix的关系？
`Hystrix`是熔断器，`Ribbon`的负载均衡的客户端，而`Feign`是一个很方便的HTTP客户端，通过接口注解`FeignClient`的方式就可以实现服务调用。

如果项目里加了`Hystrix`和`Ribbon`的依赖，`Feign`就可以集成了这俩功能，他们就像铁三角，经常一起出现。

https://www.cnblogs.com/crazymakercircle/p/11664812.html

