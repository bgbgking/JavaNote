# SpringBoot

## yml

### 数据读取

```yml
#数据读取
lesson: SpringBoot

user:
  username: zhangsan
  age: 20
  likes:
    - JAVA
    - C
```

#### 普通读取

```java
@RestController
@RequestMapping("/users")
public class UserController {

    @Value("${lesson}")
    private String lesson;
    @Value("${user.username}")
    private String name;
    @Value("${user.likes[0]}")
    private String province;

    @GetMapping("/{id}")
    public String getById(@PathVariable Integer id) {
        System.out.println(lesson);
        System.out.println(name);
        System.out.println(province);
        return "hello,SpringBoot";
    }
}
```

#### 环境装配

```java
@Autowired
private Environment environment;

@GetMapping("/{id}")
public String getById(@PathVariable Integer id) {
    System.out.println(environment.getProperty("lesson"));
    System.out.println(environment.getProperty("user.username"));
    System.out.println(environment.getProperty("user.likes[0]"));
    return "hello,SpringBoot";
}
```

#### 封装对象

##### 创建实体类

```java
@Component
@ConfigurationProperties(prefix = "user")
public class User {
    private String username;
    private int age;
    private String[] likes;
    
    public String getUsername() {return username;}
    public void setUsername(String username) {this.username = username;}
    public int getAge() {return age;}
    public void setAge(int age) {this.age = age;}
    public String[] getLikes() {return likes;}
    public void setLikes(String[] likes) {this.likes = likes;}
    @Override
    public String toString() {
        return "User{" +
                "username='" + username + '\'' +
                ", age=" + age +
                ", likes=" + Arrays.toString(likes) +
                '}';
    }
}
```

##### 引用

```java
@Autowired
private User user;

@GetMapping("/{id}")
public String getById(@PathVariable Integer id) {
    System.out.println(user);
    return "hello,SpringBoot";
}
```

### 多环境启动

#### 配置环境

```yml
#设置启用的环境
spring:
  profiles:
    active: dev

---
#开发
spring:
  profiles: dev
server:
  port: 80

---
#生产
spring:
  profiles: pro
server:
  port: 81

---
#测试
spring:
  profiles: test
server:
  port: 82


```

#### 命令行环境步骤

1.clean工程

2.改编码

3.package工程

4.在打包好的文件夹目录中输入cmd进入命令行

5.输入

```cmd
java -jar （SpringBootStudy-0.0.1-SNAPSHOT）包名.jar --spring.profiles.active=（test）环境名	//使用项目中配置的环境运行
java -jar （SpringBootStudy-0.0.1-SNAPSHOT）包名.jar --server.port=（88）端口号	//使用临时的、项目中没有的端口号运行
......
```

#### 配置文件优先级

1.打包好的文件夹中的config目录下的yml文件

2.打包好的文件夹中yml文件

3.工程中resource目录下config包下的yml文件

4.工程目录下的yml文件

## 整合Junit

在Test类前加入注解即可

```java
@SpringBootTest
```

## 整合Mybatis

### 选择技术集

### 在yml中配置数据库

```yml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost/springboot?useUnicode=true&characterEncoding=UTF-8&userSSL=false&serverTimezone=GMT%2B8
    data-username: root
    data-password: root
```

### 在Dao类前加入注解

```java
@Mapper
public interface UserDao {
```

## 页面

静态页面放在resource目录下的static目录中

### 更改静态资源存放位置

```java
@Configuration
public class WebMvcConfig extends WebMvcConfigurationSupport {

    @Override
    protected void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/front/**").addResourceLocations("classpath:/front/"); //classpath即为resource目录
    }
}
```

## 解决问题

### Spring Boot配置注解执行器没有配置

加入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

### 时区报错

更改数据库url

```yml
url: jdbc:mysql://localhost/springboot?useUnicode=true&characterEncoding=UTF-8&userSSL=false&serverTimezone=GMT%2B8
```