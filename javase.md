# **JAVASE**

## 路径符号

./	:当前目录

../	:上一层目录

/	:根目录

## 字面量

### 制表符（\t)

将前面的字符串的长度补齐到8，最少补一个，最多补8个

## 匿名对象

### 匿名对象的使用

对象类：

```java
public class Anonymity {
    public String name="zhangsan";

    public void showname() {
        System.out.println(name);
    }
}
```

实现类：

```java
public class AnonymityDemo {

    public static void main(String[] args) {

        new Anonymity().showname();
    }
}

```

## Randam

### Random的基本使用

```java
import java.util.Random;

public class RandomDemo {
    public static void main(String[] args) {

        Random r=new Random();
        //nextInt()无参，正负都有可能
        System.out.println(r.nextInt());
        //nextInt(3)有参，左开右闭区间[0,3),参数只能是正数
        System.out.println(r.nextInt(3));

    }
}
```

## 字符串

### 字符串的比较

![image-20220704225730431](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220704225730431.png)

### 字符串截取substring

substring(int index)：截取参数位置到字符串末尾。

substring(int begin,int end)：截取begin到end结束的字符串，[begin,end)。

### 字符串转为字符数组

char[] chars = str.toCharArray();

### 分割字符串

split(String regex);按regex切割字符串

原理为正则表达式，regex不能为'.'，要写'\\\\.'。

## static

### static方法

使用了static修饰成员方法，那么这个方法就成为了一个静态方法。静态方法不属于对象，而是属于**类**。

普通成员方法需要通过创建对象调用，而静态方法可以通过创建对象调用，但是更推荐通过**类名**来调用。在本类中的静态方法，可以省略类名直接调用。

成员可以访问静态，静态不能直接访问非静态。原因：内存中现有静态，后有非静态。

### 静态代码块

格式：

```java
public static 类名称{
    static {
        //静态代码块的内容
    }
}
```

特点：**当第一次使用这个类时，静态代码块执行唯一的一次**。静态内容总是优先于非静态内容，所以静态代码块的内容比构造方法先执行。

作用：用来一次性的对静态成员变量进行赋值。

## 继承

### 继承中三种重名变量的调用

```java
public class Zi extends Fu{
    int num = 10;
    public void method(){
        int num = 20;
        System.out.println(num);//局部变量num值：10
        System.out.println(this.num);//成员变量num值：20
        System.out.println(super.num);//父类中的num值
    }
}
```

### 子类方法权限、返回值范围

子类方法的返回值必须【小于等于】父类方法的返回值范围。

子类方法的权限必须【大于等于】父类方法的权限修饰符。public>protected>(default)>private

### 构造方法的继承

子类必须调用父类的构造方法，不写则赠送super();写了则用指定的super()调用。super()只能有一个，this()也只能有一个，它们都必须位于构造方法中的第一条语句，所以super()和this()不能同时在构造方法中调用。

## 多态

### 多态性的概念

一个**对象**拥有多种形态。

### 多态的体现

```java
Fu obj = new zi();//例如 动物对象new了一个猫对象，猫作为动物，是合理的；猫作为猫对象同时作为动物对象。
```

### 多态访问成员变量

1.直接通过对象名称访问成员变量：等号左边是谁，优先用谁，没有向上找。

```java
Fu obj = new Zi();//Fu.num=10;Zi.num=20
System.out.println(obj.num);//结果为10，因为等号左边为Fu。
```

2.间接通过成员方法访问成员变量：该方法属于谁，优先用谁，没有向上找。

```java
Fu obj = new Zi();//Fu中有shownum(); Zi中也有shownum();
System.out.println(obj.shownum());//结果为20，因为该方法属于Zi。
```

### 向上转型

```java
Animal animal = new Cat();
```

### 向下转型

```java
Cat cat = (Cat) Animal;
```

### instanceof关键字

向下转型需要使用instanceof关键字进行判断转型是否合理

```java
Animal animal = new Dog();
animal.eat();

if(animal instanceof Dog){//执行成功
	Dog dog = (Dog) animal;
    dog.watchHouse();
}
if(animal instanceof Cat){//执行失败
	Cat cat = (Cat) animal;
    cat.catchMouse();
}
```

## StringBuilder

### 概念及好处

StringBuilder类是一个字符串缓冲区，可以提高字符串的操作效率，底层是一个数组，但是没有被final修饰，可以改变长度。

StringBuilder在内存中始终是一个数组，并且可以和String相互转换。

```java
byte[] value = new byte[16]
```

占用空间少，效率高，如果超出了StringBuilder的容量，会自动扩容（32、64...）。

### 常用方法

#### append方法

```java
public StringBuilder append(...);//添加任意类型数据的字符串形式，并返回当前对象自身。
```

#### toString方法

```java
public String toString();//将当前StringBuilder对象转换为String对象
```

## Collection集合

### 特点

单列集合。集合中的元素孤立存在。

### 集合框架

![image-20220709215244386](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20220709215244386.png)

### 迭代器

#### 作用

遍历集合

```java
Collection<String> coll = new ArrayList<>();
        coll.add("a");
        Iterator<String> it = coll.iterator();
        while(it.hasNext()){
            System.out.println(it.next());
        }
```

#### 主要方法

```java
it.hasNext();//判断集合中是否有下一个元素
it.next();//返回集合中下一个元素
```

#### 增强for循环

作用：遍历集合和数组。

底层代码是迭代器，使用for循环的格式，简化迭代器的书写。

格式：

```java
for(集合/数组数据类型 变量名：集合名/数组名）{
	System.out.println(变量名);
}
//例子：
int []arr = {1,2,3,4,5};
for(int i:arr){
    System.out.println(i);
}
```

## Map集合

### 特点

双列集合。集合中的元素由“键”，“值”两部分组成，并成对存在。键不可以重复，值可以重复，每个键只能对应一个值。

### 常用子类

HashMap，LinkedHashMap。

## 线程

### 进程概念

进入到内存中的程序

### 线程概念

线程是进程中的一个执行单元，负责当前进程中程序的执行，一个进程至少有一个线程。

### 创建线程的方式

#### 继承Thread类

（1）创建一个继承Thread的类的子类

（2）重写run()方法，设置线程任务

（3）创建子类的对象

（4）执行start() 方法来启动线程

```java
public class Threaddemo {

    public static void main(String[] args) {

        Demo demo = new Demo();
        demo.start();

        System.out.println("main");
    }
}
public class Demo extends Thread {

    @Override
    public void run() {
        System.out.println("run");
    }
}

```

#### 实现runnable接口

（1）创建一个runnable接口的实现类

（2）在实现类中重写runnable接口中的run方法，设置线程任务

（3）创建runnable实现类对象

（4）创建Thread类对象，构造方法中传递runnable接口的实现类对象

（5）调用Thread类中的start()方法，开启新线程执行run方法

```java
public class Runnabledemo {

    public static void main(String[] args) {

        Demo demo = new Demo();
        Thread thread = new Thread(demo);
        thread.start();

        for (int i = 0; i < 10; i++) {
            System.out.println("main");
        }
    }
}
public class Demo implements Runnable {

    @Override
    public void run() {
        System.out.println("run");
    }
}

```

#### 实现callable接口

### 并发

交替执行

### 并行

同时执行

### 解决线程安全问题

#### 同步代码块（synchronized）

格式：

```java
synchronized(同步锁){
	//需要同步的代码
}
```

锁对象可以是任意类型，多个线程对象需要使用同一把锁。

#### 同步方法

格式：

```java
public synchronized void method(){
	//需要同步的代码
}
```



#### lock锁

（1）创建lock实现类reentrantLock

```java
Lock l = new ReentrantLock();
```

（2）开锁

```java
l.lock();
```

（3）需要加锁的代码

```java
//需要加锁的代码
```

（4）关锁

```java
l.unlock();	
```

## IO流

### 分类

流的方向：输入流、输出流

操作文件的类型：字节流、字符流

### 使用范围

字节流：所有文件

字符流：纯文本文件（如：.txt	.md）

### OutputStream（抽象类）

#### 常用方法

```java
public void close();//关闭此输出流并释放与此流相关联的任何系统资源
public void flush();//刷新此输出流并强制任何缓冲的输出字节被写出
public void write(byte[] b);将b.length字节从指定的字节数组写入此输出流
public void write(byte[] b,int off,int len);从指定的字节数组写入len字节，从偏移量off开始输出到此输出流
public void write(int b);将指定的字节输出流
```

#### 常用子类

##### FileOutputstream

###### 构造方法

FileOutputStream(String name)创建一个向具有指定名称的文件中写入数据的输出文件流。参数是一个文件的路径。

FileOutputStream(File file)创建一个向指定file对象标识的文件中写入数据的文件输出流。参数是一个文件。

###### 使用步骤

1.创建一个FileOutputStream对象，构造方法中传参。

2.调用方法，把数据写入文件中。

3.释放资源。

###### 将字符串转换为字节

使用String类中的getBytes()方法将字符串变为字节数组。

```java
byte[] byte = "你好".getBytes();
```

###### 续写字节输出流和换行

续写：

在FileOutputStream的构造方法应有的参数后再加参数boolean append，append为true，继续在文件末尾续写数据；append为false，创建一个新文件，覆盖原文件。

换行：

Windows中使用"\r\n"换行；

Linux中使用"/n"换行；

Mac中使用"/r"换行；

### InputStream

#### 常用方法

```java
public void close();//关闭此输入流并释放与此流相关联的任何系统资源
public int read();//从输入流中读取数据的下一个字节，若无字节，读-1
public int read(byte [] b);//从输入流中读取一定数量的字节，并将其存储到缓冲区b中
```

###### 常用子类

FileInputStream（抽象类）

###### 构造方法

FileInputStream(String name)参数是一个文件的路径。

FileInputStream(File file)参数是一个文件。

###### 使用步骤

1.创建一个FileInputStream对象，构造方法中传参。

2.调用方法，读取文件。

3.释放资源。

#### 从文件中读取

```java
File file = new File("IO/src/a.txt");
        FileInputStream fileInputStream = new FileInputStream(file);
        InputStreamReader inputStreamReader = new InputStreamReader(fileInputStream);
        BufferedReader reader = new BufferedReader(inputStreamReader);
        String[] s = reader.readLine().split(" ",2);
        System.out.println(s[0]);
        System.out.println(s[1]);
```

#### 向文件中输入

```java
String path = "D:\\idea\\construction\\src\\equation.txt";
try {
    File file = new File(path);
    FileWriter fileWriter = new FileWriter(file);
    BufferedWriter writer = new BufferedWriter(fileWriter);
    writer.write(e.generateOneEquation());
    writer.close();
} catch (IOException ex) {
    ex.printStackTrace();
}
```

### 反射

由对象获取类名等结构称为反射

## 问题

### while循环中输入数据抛异常导致无限循环

#### 原因

Scanner在接受键盘输入时，在内存中会有一段专门用来接收键盘输入的信息。键盘信息输入内存以后与你定义的接收输入的类型匹配，如果不匹配会抛出异常，但是键盘输入的信息会停留在内存当中，当下次需要输入时直接读取该内存存在的值，继续抛出异常，导致无限循环。

#### 解决办法

1.在try catch块中，异常处理时，声明一个sc.next();这一句话的作用是读取一次内存中的内容，这样等待键盘输入内存中的数据被清空，下一次可以输入

2.new新的Scanner对象，但是此方法非常耗性能