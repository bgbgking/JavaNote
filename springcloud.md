# SpringCloud

## RestTemplate工具

### 概述

spring框架提供的RestTemplate类可用于在应用中调用rest服务，它简化了与http服务的通信方式，统一了RESTful的标准，封装了http链接， 我们只需要传入url及返回值类型即可。相较于之前常用的HttpClient，RestTemplate是一种更优雅的调用RESTful服务的方式。

考虑到RestTemplate类是为调用REST服务而设计的，因此它的主要方法与REST的基础紧密相连就不足为奇了，后者是HTTP协议的方法:HEAD、GET、POST、PUT、DELETE和OPTIONS。例如，RestTemplate类具有headForHeaders()、getForObject()、postForObject()、put()和delete()等方法。

### 注册RestTemplate

```java
@SpringBootApplication
public class OrderApplication {

    public static void main(String[] args) {
        SpringApplication.run(OrderApplication.class, args);
    }
	//在启动类中将RestTemplate注册为对象
    @Bean
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }

}
```



## Eureka注册中心

### 搭建

#### 引入依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId><!--服务端-->
</dependency>
```

#### 编写启动类

在启动类上添加@EnableEurekaServer注解

#### 添加application.yml配置文件

```yml
server:
  port: 10086
spring:
  application:
    name: eurekaserver
eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:10086/eureka
```

### 服务注册

#### 引入依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId><!--客户端-->
</dependency>
```

#### 在yml中配置eureka地址

```yml
spring:
  application:
    name: userservice
eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:10086/eureka
```

### 服务发现

#### 修改访问的url路径

用服务名代替ip、端口

```java
public Order queryOrderById(Long orderId) {
    // 1.查询订单
    Order order = orderMapper.findById(orderId);
    //2.利用RestTemplate发起http请求，查询用户
    //2.1 url路径
    String url = "http://userservice/user/"+order.getUserId();
    //2.2 发起http请求，实现远程调用
    User user = restTemplate.getForObject(url,User.class);
    //3.封装user到order
    order.setUser(user);
    // 4.返回
    return order;
}
```

#### 加入负载均衡注解

```java
public class OrderApplication {

    public static void main(String[] args) {
        SpringApplication.run(OrderApplication.class, args);
    }

    @Bean
    @LoadBalanced
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }

}
```

## Ribbon负载均衡

### 规则

规则接口为IRule

### 自定义负载均衡策略

```yml
userservice:
  ribbon:
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule #负载均衡规则
```

### 饥饿加载

在服务器启动时加载微服务

```yml
ribbon:
  eager-load:
    enabled: true #开启饥饿加载
    clients:  #指定饥饿加载服务名称
      - userservice
```

## Nacos

### nacos注册中心

#### 搭建

在bin目录下运行指令

```shell
startup.cmd -m standalone
```

#### 服务注册或发现

##### 引入依赖

父工程

```xml
<!--nacos管理依赖-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-alibaba-dependencies</artifactId>
    <version>2.2.5.RELEASE</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```

子工程

```xml
<!--nacos客户端依赖-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

##### 配置nacos地址

```yml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848 #nacos 服务地址
```

#### nacos服务分级存储模型

一级：服务

二级：集群

三级：实例

##### 设置实例的集群属性

```yml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848 #nacos 服务地址
      discovery:
        cluster-name: shanghai #集群名称
```

#### nacosRule负载均衡策略

优先选择同集群服务实例列表

本地集群无实例，去其他集群选择

确定可用实例列表，采用随机负载均衡挑选实例

```yml
userservice:
  ribbon:
    NFLoadBalancerRuleClassName: com.alibaba.cloud.nacos.ribbon.NacosRule #负载均衡规则
```

#### 服务实例权重设置

nacos控制台可以设置实例权重值，权重值在0~1之间

多个实例权重值越高，被访问频率越高

权重为0，不会被访问

#### nacos环境隔离

namespace作环境隔离

每个namespace拥有唯一id

不同的namespace中的服务不可相互访问

```yml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848
      discovery:
        cluster-name: hangzhou
        namespace: 1b101a60-88a5-448d-bd55-a984da426087 #dev环境
```

#### 设置实例是否临时

```yml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848
      discovery:
        cluster-name: hangzhou
        namespace: 1b101a60-88a5-448d-bd55-a984da426087 #dev环境
        ephemeral: false #是否是临时实例
```

#### nacos配置管理

##### 配置读取

引入nacos配置管理客户端依赖

```xml
<!--nacos配置管理依赖-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
        </dependency>
```

在微服务resource目录添加bootstrap.yml文件，此文件优先级高于application.yml

```yml
spring:
  application:
    name: userservice
  profiles:
    active: dev
  cloud:
    nacos:
      server-addr: localhost:8848
      config:
        file-extension: yaml
```

测试是否成功读取

```java
@Autowired
private UserService userService;

@Value("${pattern.dateformat}")
private String dateformat;

@GetMapping("now")
public String now(){
    return LocalDateTime.now().format(DateTimeFormatter.ofPattern(dateformat));
}
```

##### 自动刷新

方式一：

在@value注入的变量所在类上加注解@RefreshScope

```java
@RestController
@RequestMapping("/user")
@RefreshScope
public class UserController {
```

方式二：

使用@ConfigurationProperties注解

##### 配置文件优先级

服务名-profile.yaml > 服务名.yaml > 本地配置

## Feign

### 使用步骤

引入依赖

```xml
<!--feign客户端依赖-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

主启动类添加注解

```java
@EnableFeignClients
public class OrderApplication {
```

编写接口

```java
@FeignClient("userservice")
public interface UserClient {

    @GetMapping("/user/{id}")
    User findById(@PathVariable("id") Long id);
}
```

使用接口中的方法

```java
@Service
public class OrderService {

    @Autowired
    private OrderMapper orderMapper;

    @Autowired
    private UserClient userClient;

    public Order queryOrderById(Long orderId) {
        // 1.查询订单
        Order order = orderMapper.findById(orderId);
        //2.用feign远程调用
        User user = userClient.findById(order.getUserId());
        //3.封装user到order
        order.setUser(user);
        // 4.返回
        return order;

    }
}
```

### 自定义日志配置

配置文件加入代码

```yml
feign:
  client:
    config:
      default:  #default为全局配置，若为服务名称比如userservice则为局部配置
        loggerLevel: FULL	#NONE	BASIC	HEADER	FULL
```

java代码配置Logger.Level这个Bean

### 性能优化

使用支持连接池的客户端替代默认的底层URLConnection客户端

引入依赖

```xml
<!--        引入feign-httpClient依赖-->
        <dependency>
            <groupId>io.github.openfeign</groupId>
            <artifactId>feign-httpclient</artifactId>
        </dependency>
```

配置文件开启httpClient功能，设置连接池参数

```yml
feign:
  httpclient:
    enabled: true #支持打开httpclient的开关
    max-connections: 200
    max-connections-per-route: 50 #单个路径最大连接数
```

日志级别用basic或none

## 网关Gateway

### 作用

对用户请求作身份验证、权限校验

将用户请求路由到微服务，并实现负载均衡

对用户请求作限流

### 搭建

创建服务（模块），引入nacos服务发现和gateway依赖

```xml
<!--        nacos服务发现依赖-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
<!--        网关gateway依赖-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-gateway</artifactId>
        </dependency>
```

配置application.yml，配置服务信息

配置包括

id	路由唯一标示

uri	路由目标地址，http表示固定地址，lb表示负载均衡

predicates	路由断言，判断路由是否符合规则

filters	路由过滤器，对请求或响应做处理

```yml
server:
  port: 10010
spring:
  application:
    name: gateway
  cloud:
    nacos:
      server-addr: localhost:8848
    gateway:
      routes:
        - id: user-service  #路由标示，必须唯一
          uri: lb://userservice #路由的目标地址
          predicates: #路由断言，判断请求是否符合规则
            - Path=/user/** #路径断言，判断路径是否是以/user开头
#          filters: #局部过滤器
#            - AddRequestHeader=oh, ohohohohohoh!
        - id: order-service
          uri: lb://orderservice
          predicates:
            - Path=/order/**
      default-filters:  #全局过滤器
        - AddRequestHeader=oh, ohohohohohoh!
```

### 自定义过滤器

#### 作用

对所有路由生效，且可以自定义处理逻辑

#### 实现步骤

自定义类，实现GlobalFilter接口，添加@Order注解

```java
@Order(-1)  //值越小，过滤器优先级越高
@Component
public class AuthorizeFilter implements GlobalFilter {

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {

        //1.获取请求参数
        ServerHttpRequest request = exchange.getRequest();
        MultiValueMap<String, String> params = request.getQueryParams();
        //2.获取参数中的authorization参数
        String auth = params.getFirst("authorization");
        //3.判断参数值是否等于admin
        if ("admin".equals(auth)){
            //3.1 是，放行
            return chain.filter(exchange);
        }

        //3.2 否，拦截
        //3.2.1 设置状态码
        exchange.getResponse().setStatusCode(HttpStatus.UNAUTHORIZED);
        return exchange.getResponse().setComplete();
    }
}
```

#### 执行顺序

路由过滤器和defaultFileter的order由spring指定，默认是按声明顺序从1递增

当order值相同时，defaultFilter > 路由过滤器 > GlobalFilter

### 跨域问题

浏览器禁止请求发起者与服务端发生跨域的ajax请求，请求被浏览器拦截的问题

解决方案：CORS