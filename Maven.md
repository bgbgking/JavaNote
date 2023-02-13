# Maven

## 聚合

一个工程管理多个工程

### 聚合工程开发

创建Maven模块，设置打包类型为pom

```xml
<packging>pom</packging>
```

设置当前聚合工程所包含的子模块名称

```xml
<modules>
	<module>../maven_ssm</module>
    <module>../maven_pojo</module>
    <module>../maven_dao</module>
</modules>
```

## 继承

创建Maven模块，设置打包类型为pom（通常与聚合一起开发）

```xml
<packging>pom</packging>
```

在父工程pom文件中配置依赖关系（子工程沿用父工程中的依赖关系）

```xml
<dependencies>
	<dependency>
    	...
    </dependency>
</dependencies>
```

配置子工程中可选的依赖关系

```xml
<dependencyManagememt>
	<dependencies>
    	<dependency>
        	...
        </dependency>
    </dependencies>
</dependencyManagememt>
```

在子工程中配置当前工程所继承的父工程

```xml
<parent>
	<groupId></groupId>
    <artifactId></artifactId>
	<version></version>
    <relativePath></relativePath><!--填写父工程的pom文件-->
</parent>
```

在子工程中配置使用父工程中可选依赖的坐标(不需要配置版本)

```xml
<dependencies>
	<dependency>
    	...
    </dependency>
</dependencies>
```

## 属性

### 定义属性

```xml
<properties>
	<spring.version>5.2.10.RELEASE</spring.version>
</properties>
```

### 引用属性

```xml
<version>${spring.version}</version>
```

