#  spring framework

## IOC(控制反转)

对象的创建控制权由程序转移到外部（使用对象时，程序不主动new对象，转换为外部提供对象

spring提供了一个容器，称为ioc容器，充当ioc思想中的外部 

## 创建ioc容器

![image-20220920231901569](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220920231901569.png)

配置文件可以写多个，中间用逗号隔开

## DI（依赖注入）

在容器中建立bean和bean之间的依赖关系的整个过程

![image-20220920222729554](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220920222729554.png)

### setter注入

![image-20220921193309327](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220921193309327.png)

### 自动装配（autowire）

![image-20220921195459158](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220921195459158.png)

## Bean

### 实例化bean方法

1.构造方法实例化bean

![image-20220920230226042](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220920230226042.png)

2.使用静态工厂实例化bean

![image-20220920230243237](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220920230243237.png)

3.使用实例工厂实例化bean

![image-20220920230258344](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220920230258344.png)

4.使用FactoryBean实例化bean（*）

<img src="C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220920225709434.png" alt="image-20220920225709434" style="zoom: 50%;" />

### 获取bean

<img src="C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220920231955190.png" alt="image-20220920231955190" style="zoom:200%;" />

## 注解开发

### 导入依赖

```xml
 <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.3.23</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.10</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.16</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.3.23</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.0</version>
        </dependency>
    </dependencies>
```



### 注解开发bean

![image-20220921202951920](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220921202951920.png)

衍生注解与@component作用相同，使代码更具可读性

### 纯注解开发（注解配置文件）

![image-20220921203708545](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220921203708545.png)

### 注解依赖注入

![image-20220921205146796](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220921205146796.png)

![image-20220921205307750](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220921205307750.png)

### 在SpringConfig中加载外部properties文件

```java
@Configuration
@ComponentScan("com.l")
@PropertySource("classpath:jdbc.properties")
@Import({JdbcConfig.class,MybatisConfig.class})
public class SpringConfig {
}
```

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/spring?useSSL=false
jdbc.username=root
jdbc.password=yourname..
```



### 将独立的配置类加入核心配置

方法一：![image-20220921205817348](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220921205817348.png)

方法二：![image-20220921205850308](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220921205850308.png)

### 数据库连接（使用Druid）并配成Bean

```java
@Configuration
public class JdbcConfig {
    @Value("${jdbc.driver}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
    @Value("${jdbc.username}")
    private String username;
    @Value("${jdbc.password}")
    private String password;

    @Bean
    public DataSource dataSource(){
        DruidDataSource ds = new DruidDataSource();
        ds.setDriverClassName(driver);
        ds.setUrl(url);
        ds.setUsername(username);
        ds.setPassword(password);
        return ds;
    }
}

```

配置MybatisConfig文件

```java
public class MybatisConfig {

    @Bean
    public SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource){

        SqlSessionFactoryBean ssfb = new SqlSessionFactoryBean();
        ssfb.setTypeAliasesPackage("com.l.domain");
        ssfb.setDataSource(dataSource);
        return ssfb;
    }
    @Bean
    public MapperScannerConfigurer mapperScannerConfigurer(){
        MapperScannerConfigurer msc = new MapperScannerConfigurer();
        msc.setBasePackage("com.l.mapper");
        return msc;
    }
}
```

domain即实例对象

### Spring整合Junit

#### 导入依赖

```xml
 <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.3.23</version>
        </dependency>
```



#### 使用专用类加载器

```java
@RunWith(SpringJUnit4ClassRunner.class)
```

#### 指定spring配置文件

```java
@ContextConfiguration(classes = SpringConfig.class)
```

#### **在测试类前加入注解**

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = SpringConfig.class)
public class AccountServiceTest {}
```



## AOP(面向切面编程)

本质：代理模式

作用：在不影响原始设计的基础上为其进行功能的增强

### 导入依赖并配置文件

```xml
<dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.9.1</version>
        </dependency>
```

#### 在springConfig添加注解

```java
@EnableAspectJAutoProxy//AOP使用注解的配置
public class SpringConfig{}

```

#### 在通知类添加注解

```java
@Component
@Aspect
public class advice {}
```

#### 定义切入点

```java
 @Pointcut("execution(void com.l.service.UserAddServiceImpl.add(..))")
    private void pt(){}
```



### 通知类型

前置通知

```java
@Before
```

后置通知

```java
@After
```

环绕通知

```java
@Around
```

返回后通知

```java
@AfterReturning
```

抛出异常后通知

```java
AfterThrowing
```

### AOP获取参数和返回值

@Before获取参数

![image-20220925095843096](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220925095843096.png)

@After获取参数

![image-20220925095955754](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220925095955754.png)

@Around获取参数

![image-20220925100151421](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220925100151421.png)

@AfterReturning获取返回值

![image-20220925100351222](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220925100351222.png)

若有JoinPoint对象作参数，则该对象必须在参数第一位

## 事务

### 在业务层接口上添加Spring事务管理

```java
@Transactional
    public void transfer(String out,String in,int account);
```

### 设置事务管理器

```java
@Bean
    public PlatformTransactionManager transactionManager(DataSource dataSource){
        DataSourceTransactionManager transactionManager = new DataSourceTransactionManager();
        transactionManager.setDataSource(dataSource);
        return transactionManager;
    }
```

### 开启注解事务

```java
@EnableTransactionManagement
public class SpringConfig {
}
```

### 事务传播

可控制事务回滚