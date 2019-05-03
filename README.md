****项目规划****

**************************主体骨架**************************

1、Eureka注册中心集群版本【Eureka服务端】【独立部署】

2、服务提供者集群【Eureka客户端】

3、服务消费者【Eureka客户端】

**********************服务集成和部署分析**********************

1、服务注册中心【Eureka-Server】

    A、单机版配置
    B、集群版配置
    C、集成服务健康监控Actuator
    D、集成服务Basic安全机制
    E、集成Config分布式配置管理
   
2、服务提供者

    A、服务提供者单机版配置
    B、服务提供者集群版配置
    C、集成服务Hystrix容错处理【服务熔断、服务限流】
    D、集成服务Basic安全机制
    E、集成服务健康监控Actuator
    F、集成Hystrix的Dashboard客户端【服务健康监控可视化】
    G、集成Zuul路由网关【请求转发、请求过滤】
    H、集成Config分布式配置管理
    I、集成Mysql + Mybaits + 阿里线程池Druid
    J、集成Eureka客户端

3、服务消费者【Eureka-Server】

    A、Rest方式发现【Feign】【客户端集成】
    B、负载均衡发现【Ribbon】【客户端负载均衡】【客户端集成】
    C、Hystrix容错处理【服务降级】
    D、集成服务Basic安全机制
    E、集成服务健康监控Actuator
    F、集成Config分布式配置管理
    G、集成Eureka客户端

4、其他独立部署

    A、Hystrix的Dashboard服务端【服务健康监控可视化】
    B、Config服务端【布式配置中心】

**********************相关概念理解**********************

1、分包分模块的创建Maven_父工程and公共API工程

2、Eureka注册中心

    A、单机版配置
    B、集群版配置

3、Eureka自我保护机制

4、健康检查接口Actuator
    
    A、介绍
    B、端点分析

5、服务注册

    A、单机版注册
    B、集群版注册

6、服务发现

    A、RestTemplate传统方式发现
    B、Rubbin负载均衡发现
    C、Feign Rest方式发现

7、服务Basic安全机制

    1、服务消费者添加security
            前端应该如何处理，对url有什么要求
            
    2、服务提供者添加security
        客户端地址如何写才能调用服务
        答：将微服务中的 eureka.client.service-url.defaultZone 改为：
        http://用户名:密码@localhost:8761/eureka/ 即可注册到需认证的Eureka Server中
        
    3、注册中心添加security
        服务提供者，服务消费者的服务地址怎么写才能在注册中心注册与发现
        答：将微服务中的 eureka.client.service-url.defaultZone 改为：
        http://用户名:密码@localhost:8761/eureka/ 即可注册到需认证的Eureka Server中
    
    4、基于feign的security可以达到权限粒度更精确，例如：如下需求
        1：用户微服务的接口需要登录后才能调用，并且对于相同的API，不同角色的用户有不同的行为。
        2：让电影微服务中的同一个Feign接口，使用不同的账号登录，并调用用户微服务的接口。
    
    5、如何区分4和1,2,3
    
    6、操作步骤
        A:添加依赖
        B:服务端yml文件中配置开启：
         spring:
           application:
             name: eureka-register-center-8003
           security:
             basic:  # 开启基于HTTP basic认证
               enabled: true
             user:
               name: root          # 用户名
               password: 123456    # 密码
        C:修改客户端的配置
          defaultZone: http://enodetwo.com:8002/eureka/, 
          http://enodeone.com:8001/eureka/ # 集群版本
          ==>
          defaultZone: http://root@123456enodetwo.com:8002/eureka/, 
          http://root@123456enodeone.com:8001/eureka/ # 集群版本

8、Hystrix容错处理

9、Zuul路由网管

10、Config分布式部署

11、Eureka元数据

    标准元数据：主机名、IP地址、端口号、状态页和健康检查等信息，这些信息都会被发布在服务注册表中，用于服务之间的调用。
    自定义元数据：自定义元数据可以使用eureka.instance.metadata-map配置，这些元数据可以在远程客户端中访问，但是一般不会改变客户端的行为，除非客户端知道该元数据的含义。
    eureka:
      client:
        serviceUrl:
    #      defaultZone: http://peer1:9528/eureka/  # 注册中心已经开启认证 单机版本
          defaultZone: http://root:123456@enodeone.com:8001/eureka/, http://root:123456@enodetwo.com:8002/eureka/, http://root:123456@enodethree.com:8003/eureka/ # 集群版本
      instance:
        prefer-ip-address: true
        metadata-map:
          #自定义元数据，key:value  都是自定义的
          mydata: rbdata我是元数据
        instanceId: ${spring.application.name}:${server.port}  # 微服务虚拟地址

12、项目命名规范
