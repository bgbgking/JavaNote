# javaweb

## mysql

### mysql中字段自增

使用auto_increment而不是identity（1，1）。

## jdbc

### 流程

注册驱动；获取连接；定义sql语句；获取pstmt对象；设置参数；执行sql；处理结果；释放资源；

### PreparedStatement

优点：可预编译，防止sql注入。但是MySQL8.0之前的版本需要手动开启预编译，并配置MySQL执行日志。

![image-20220702142127497](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220702142127497.png)

### 数据库连接池

优点：资源重用；提升响应速度；避免数据库连接遗漏；

## Junit

作用：单元测试

### 步骤

1.定义测试类

2.定义测试方法

3.给方法加**@Test**

4.导入junit依赖环境

5.判定结果

### @Before和@After

@Before即运行在测试代码前

@After即运行在测试代码后

