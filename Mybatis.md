# Mybatis

## 创建步骤

1.搭建环境

​	1.1新建项目

​	1.2在pom.xml中导入依赖

2.创建模块

​	2.1编写mybatis核心配置文件mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
  <mappers>
	<mapper resource="com/l/dao/UserMapper.xml"/>
  </mappers>
</configuration>
```

​	2.2编写mybatis工具类

```java
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

public class MybatisUtils {

    private static SqlSessionFactory sqlSessionFactory;

    static {
        try {
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public static SqlSession getSqlSession(){

        return sqlSessionFactory.openSession();
    }
}

```

​	2.3编写代码

​		2.3.1实体类（pojo）

​		2.3.2Mapper接口

```java
package com.l.dao;

import com.l.pojo.User;

import java.util.List;

public interface UserMapper {
    List<User> getUserList();

    User getUserById(int id);

    int addUser(User user);

    int deleteUser(int id);

    int updateUser(User user);
}


```



​		2.3.3接口实现类（xml配置文件）需要和接口放在同一个目录下，若在resource中也需要创建相同的目录

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.l.dao.UserMapper">

    <select id="getUserList" resultType="com.l.pojo.User">
        select * from mybatis.user
    </select>
    <select id="getUserById" parameterType="int" resultType="com.l.pojo.User">
        select * from mybatis.user where id = #{id}
    </select>
    <insert id="addUser" parameterType="com.l.pojo.User">
        insert into mybatis.user(id ,name ,pwd) values (#{id},#{name},#{pwd})
    </insert>
    <delete id="deleteUser" parameterType="com.l.pojo.User">
        delete from mybatis.user where id = #{id}
    </delete>
    <update id="updateUser" parameterType="com.l.pojo.User">
        update mybatis.user set name = #{name},pwd = #{pwd} where id = #{id};
    </update>
</mapper>
```

​		命名空间即为接口路径

​	2.4测试

```java
public void testSelectById(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        User user = mapper.getUserById(1);
        System.out.println(user);

        sqlSession.close();
    }
```



## 注意

1.maven由于约定大于配置，可能配置文件无法导出或生效，解决方案

```xml
<build>
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>

            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>
    </build>
```

2.每个Mapper.xml都需要在<mapper>中注册