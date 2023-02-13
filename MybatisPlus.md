# MybatisPlus

## BaseMapper< T >

### 分页查询

#### 设置拦截器

```java
@Configuration
public class MpConfig {

    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor(){
        MybatisPlusInterceptor mpInterceptor = new MybatisPlusInterceptor();    //定义一个拦截器
        mpInterceptor.addInnerInterceptor(new PaginationInnerInterceptor());    //添加一个分页拦截器
        return mpInterceptor;
    }
}
```

#### 执行分页查询

```java
void getByPage(){
    IPage page = new Page(1,2);
    userMapper.selectPage(page,null);
    System.out.println("当前页码值："+page.getCurrent());
    System.out.println("每页显示数："+page.getSize());
    System.out.println("一共多少页："+page.getPages());
    System.out.println("一共多少条："+page.getTotal());
    System.out.println("数     据："+page.getRecords());
}
```

### 条件查询

#### 执行条件查询

lt：小于

le：小于等于

gt：大于

ge：大于等于

eq：等于

between：范围

like：模糊查询

likeleft：通配符在左

likeright：通配符在右

其他：[条件构造器 | MyBatis-Plus (baomidou.com)](https://baomidou.com/pages/10c804/)

```java
void get(){
    QueryWrapper qw = new QueryWrapper();
    qw.lt("age",20); 
    qw.gt("age",18); 
    List list = userMapper.selectList(qw);
    System.out.println(list);
}
```

#### NULL值判定

```java
@Test
//按条件查询
void get1(){
    UserQuery userQuery = new UserQuery();
    userQuery.setAge(18);
    userQuery.setAge1(20);
    //此处使用lambda表达式
    LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<User>();

    lqw.gt(null !=userQuery.getAge(),User::getAge,userQuery.getAge());
    lqw.lt(null !=userQuery.getAge1(),User::getAge,userQuery.getAge1());

    List<User> users = userMapper.selectList(lqw);
    System.out.println(users);
}
```

### 查询投影

#### 查询结果包含模型类中部分属性

```java
@Test
void get2(){
    LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<User>();
    lqw.select(User::getId,User::getUsername);
    List<User> users = userMapper.selectList(lqw);
    System.out.println(users);
}
```

#### 查询结果包含模型类中未定义属性

```java
@Test
void get3(){
    QueryWrapper<User> lqw = new QueryWrapper<User>();
    lqw.select("count(*) as count");
    List<Map<String, Object>> users = userMapper.selectMaps(lqw);
    System.out.println(users);
}
```

### 字段映射与表名映射问题

```java
@TableName("user")  //设置与数据库相同的表名
public class User {
    @TableField(select = false)  //隐藏id字段
    private long id;

    private String username;

    @TableField(value = "age")  //设置与数据库相同的字段名
    private Integer age;

    @TableField(exist = false)  //声明数据库中没有online字段，调用sql时跳过
    private Integer online;
}
```

