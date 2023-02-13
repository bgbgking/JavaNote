# SpringMVC

## 入门案例

### 导入依赖

```xml
 <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
            <version>2.5</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.2.0.RELEASE</version>
        </dependency>
```

### 创建SpringMVC控制器类（相当于Servlet）

```java
@Controller
public class UserController {

    @RequestMapping("/save")    //设置当前操作的访问路径
    @ResponseBody    //设置当前操作的返回值类型
    public String save(){
        System.out.println("user save ...");
        return "{'module':'springmvc'}";
    }
}

```

### 初始化SpringMVC环境，设定SpringMVC加载对应的Bean

```java
@Configuration
@ComponentScan("com.l.controller")
public class SpringMvcConfig {
}
```

### 初始化Servlet容器，加载MVC环境，设置处理请求

```java
public class ServletContainerInitConfig extends AbstractDispatcherServletInitializer {
    @Override
    //加载springMVC容器
    protected WebApplicationContext createServletApplicationContext() {
        AnnotationConfigWebApplicationContext ctx = new AnnotationConfigWebApplicationContext();
        ctx.register(SpringMvcConfig.class);
        return ctx;
    }

    @Override
    //设置哪些请求归springMVC处理
    protected String[] getServletMappings() {
        return new String[]{"/"};//所有请求归mvc处理
    }

    @Override
    //加载spring容器的配置
    protected WebApplicationContext createRootApplicationContext() {
        AnnotationConfigWebApplicationContext ctx = new AnnotationConfigWebApplicationContext();
        ctx.register(SpringConfig.class);
        return ctx;
    }
}
```

#### 简化开发

```java
public class ServletContainerInitConfig extends AbstractAnnotationConfigDispatcherServletInitializer {

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

## Bean加载控制

### 指定和排除扫描包

```java
@ComponentScan(value = "com.l", excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION,classes = Controller.class))
//includeFilters：加载指定bean，需指定类别（type）和具体项（classes）
//excludeFilters：排除扫描路径中加载的bean，需指定类别（type）和具体项（classes）
//type按类型排除
```

## 设置请求映射路径

在控制器类上加入注解可防止不同类中方法路径相同导致报错问题

```java
@Controller
@RequestMapping("/book")
public class BookController {

    @RequestMapping("/save")    //设置当前操作的访问路径
    @ResponseBody    //设置当前操作的返回值类型
    public String save(){
```

## 处理post方式中文乱码

在ServletContainerInitConfig中覆盖getServletFilters方法

```java
public class ServletContainerInitConfig extends AbstractAnnotationConfigDispatcherServletInitializer {
    @Override
    protected Filter[] getServletFilters() {
        CharacterEncodingFilter filter = new CharacterEncodingFilter();
        filter.setEncoding("UTF-8");
        return new Filter[]{filter};
    }
```

### 注意

与简化开发共同使用

**只能处理post方式！！！**

## 参数传递

### 在http请求中发送数据

```http
#get请求发送普通参数
GET http://localhost:8080/param/gp1?name=zhangsan&age=20
Accept: application/json
###

#post请求发送普通参数
POST http://localhost:8080/param/gp1
Content-Type: application/x-www-form-urlencoded

name=张三&age=21
###

#post请求发送pojo对象
POST http://localhost:8080/param/gp2
Content-Type: application/x-www-form-urlencoded

name=wangwu&age=21&address.province=湖南&address.city=长沙
###

#post请求发送数组对象
POST http://localhost:8080/param/gp3
Content-Type: application/x-www-form-urlencoded

likes=唱&likes=跳&likes=Rap&likes=篮球
###

#post请求发送集合对象
POST http://localhost:8080/param/gp4
Content-Type: application/x-www-form-urlencoded

likes=唱&likes=跳&likes=Rap&likes=篮球
###
```

### 在控制器中接受数据

```java
@Controller
@RequestMapping("/param")
public class GetParamController {

    @RequestMapping("/gp1")
    @ResponseBody
    public String getParam1(@RequestParam("name") String name, @RequestParam("age") int age){
        System.out.println("普通参数传递 ===>"+name);
        System.out.println("普通参数传递 ===>"+age);
        return "{'module':'param'}";
    }

    @RequestMapping("/gp2")
    @ResponseBody
    public String getParam2(User user){
        System.out.println("pojo参数传递 ===>"+user);
        return "{'module':'param'}";
    }

    @RequestMapping("/gp3")
    @ResponseBody
    public String getParam3(String[] likes){
        System.out.println("数组参数传递 ===>"+ Arrays.toString(likes));
        return "{'module':'param'}";
    }

    @RequestMapping("/gp4")
    @ResponseBody
    public String getParam4(@RequestParam List<String> likes){
        System.out.println("集合参数传递 ===>"+ likes);
        return "{'module':'param'}";
    }
}
```

### @RequestParam注解

可指定参数名称，解决参数不同名问题；

防止容器将集合作pojo对象处理，让其传参时将参数作为数据存入而非属性存入。

### **json数据传递参数**

#### 导入依赖

```xml
 <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.9.0</version>
        </dependency>
```

#### 加入@EnableWebMvc注解

```java
@EnableWebMvc
public class SpringMvcConfig {
}
```

#### 发送json数据请求

##### 集合

```http
#post请求发送json集合对象
POST http://localhost:8080/param/gp5
Content-Type: application/json

["唱","跳","Rap","篮球"]
###
```

##### 单个对象

```http
#post请求发送json（pojo）对象
GET http://localhost:8080/param/gp6
Content-Type: application/json

{"name":"a","age":20,"address": {"province": "hunan","city": "changsha"}}
###
```

##### 多个对象

```http
#post请求发送多个json（pojo）对象
GET http://localhost:8080/param/gp7
Content-Type: application/json

[
  {"name":"a","age":20,"address": {"province": "hunan","city": "changsha"}},
  {"name":"b","age":21,"address": {"province": "hunan","city": "huaihua"}}
]
###
```



#### 控制器中参数前加入@RequestBody注解

```java
@RequestMapping("/gp5")
    @ResponseBody
    public String getParam5(@RequestBody List<String> likes){
        System.out.println("json参数传递 ===>"+ likes);
        return "{'module':'param'}";
    }
```

#### @RequestParam和@RequestBody的区别

@RequestParam用于接收url地址传参，表单传参（application/x-www-form-urlencoded）

@RequestBody用于接收json数据（application/json）

### 日期参数传递

使用@DateTimeFormat注解指定格式

## 响应

### @ResponseBody

作用

设置当前控制器返回值作为响应体

效果

加：对象转为json；对象集合转json数组；输出字符串

不加：返回一个页面

## 拦截器

### 声明一个拦截器类

```java
@Component
public class ProjectInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle");
        return true;    //若为false，终止方法
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion");
    }
}
```

### 配置类中继承方法并设定访问路径

```java
@Configuration
public class SpringMvcSupport extends WebMvcConfigurationSupport {
    @Autowired
    private ProjectInterceptor projectInterceptor;

    @Override
    protected void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(projectInterceptor).addPathPatterns("/users","/users/*");
    }
}
```

### 执行流程

1.preHandle(一定执行)

2.判断返回条件

3.true

​	4.controller

​	5.postHandle

​	6.afterCompletion

3.false

​	4.结束（不执行controller）

### 拦截器链执行流程

![image-20221001111206273](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20221001111206273.png)