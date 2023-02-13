# SSM

## 创建项目

使用骨架创建项目

## 框架整合

### 导入依赖

spring依赖

```xml
<!--    spring依赖-->
   <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.2.0.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>5.2.0.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>5.2.0.RELEASE</version>
    </dependency>
<!--    mybatis依赖-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.10</version>
    </dependency>
<!--    mybatis整合spring依赖-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>1.3.0</version>
    </dependency>
<!--    连接mysql依赖-->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.47</version>
    </dependency>
<!--    数据源依赖-->
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.1.16</version>
    </dependency>
<!--    servlet依赖-->
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>4.0.1</version>
    </dependency>
<!--    json依赖-->
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.0</version>
    </dependency>
<!--    单元测试依赖-->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.13.2</version>
      <scope>test</scope>
    </dependency>
```

### 创建目录

![image-20220929100105699](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220929100105699.png)

### 编写配置类

#### SpringConfig

```java
@Configuration  //设置其为一个配置类
@ComponentScan("com.l.service") //加载配置的Bean，若有多个路径，用大括号。
@PropertySource("classpath:jdbc.properties")  //加载properties文件
@Import({JdbcConfig.class,MybatisConfig.class}) //加载其他配置类
@EnableTransactionManagement    //开启事务管理
public class SpringConfig {
}
```

#### MybatisConfig

```java
public class MybatisConfig {

    @Bean
    public SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource){
        SqlSessionFactoryBean factoryBean = new SqlSessionFactoryBean();
        factoryBean.setDataSource(dataSource);
        factoryBean.setTypeAliasesPackage("com.l.domain");
        return factoryBean;
    }

    @Bean
    public MapperScannerConfigurer mapperScannerConfig(){
        MapperScannerConfigurer msc = new MapperScannerConfigurer();
        msc.setBasePackage("com.l.dao");
        return msc;

    }
}
```

#### JdbcConfig

```java
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
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName(driver);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        return dataSource;
    }

    @Bean
    public PlatformTransactionManager transactionManager(DataSource dataSource){
        DataSourceTransactionManager ds = new DataSourceTransactionManager();
        ds.setDataSource(dataSource);
        return ds;
    }
}
```

#### ServletConfig

```java
public class ServletConfig extends AbstractAnnotationConfigDispatcherServletInitializer {
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{SpringConfig.class};
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{SpringMvcConfig.class};
    }

    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }
}
```

#### SpringMvcConfig

```java
@Configuration  //作为配置类
@ComponentScan("com.l.controller")  //扫描包
@EnableWebMvc
public class SpringMvcConfig {
}
```



### 放行前端页面

#### 新建配置类

```java
@Configuration
public class SpringMvcSupport extends WebMvcConfigurationSupport {
    @Override
    protected void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/pages/**").addResourceLocations("/pages/");
    }
}
```

#### 在SpringMvcConfig类中扫描配置类

```java
@Configuration  //作为配置类
@ComponentScan({"com.l.controller","com.l.config"})  //扫描包
@EnableWebMvc
public class SpringMvcConfig {
}
```

## 测试业务层和表现层

## 解决问题

### 解决自动装配时报错

1.

![image-20220929103958560](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220929103958560.png)

2.

<img src="C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220929104030569.png" alt="image-20220929104030569" style="zoom: 200%;" />

### 解决测试时因版本不同报错

specify @bootstrapwith's 'value' attribute or make the default bootstrapper class available.

将spring各版本设为一致

### 解决找不到properties文件报错

在SpringConfig中加载properties文件时加入classpath

```java
@PropertySource("classpath:jdbc.properties")  //加载properties文件
```

