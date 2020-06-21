---
title: MyBatis_02 SpringBoot 下配置 MyBatis 环境
date: 2019-10-12 21:40:27
tags:
- MyBatis
- MySQL
- Java
- SpringBoot
categories: 技术向
description:
cover_img:
feature_img:

---







## 0. 首先创建 Maven 项目

Maven 的包管理非常方便。在 IDEA 中创建新项目，选中 Maven。

注意命名 

GroupId: com.yefcion

组织名，公司网址的反写 + 项目名称

ArtifactId: mybatis-learn

项目名-模块名

## 1. 创建一张表，写一个类

### 1.1 创建表 country

确认本地安装了数据库，或者连接服务器上的数据库也可以。注意，数据库登录账户、密码要在 `config.xml` 中配置。

使用 NaviCat 或者命令行登录自己要使用的数据库，输入如下代码，创建 `Country` 表。

```mysql
-- 如果 mybatis_action_db 数据库不存在，就创建 mybatis_action_db 数据库：
CREATE DATABASE IF NOT EXISTS mybatis_action_db DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;

-- 切换到 mybatis_action_db 数据库
use mybatis_action_db;

-- 删除classes表和students表（如果存在）：
DROP TABLE IF EXISTS country;

-- 创建 country 表：
CREATE TABLE country(
  id          INT          NOT NULL AUTO_INCREMENT,
  countryname VARCHAR(255) NULL,
  countrycode VARCHAR(255) NULL,
  PRIMARY KEY (id)
);

-- 插入 country 记录：
INSERT country(countryname, countrycode)
VALUES ('中国', 'CN'),
       ('美国', 'US'),
       ('俄罗斯', 'RU'),
       ('英国', 'GB'),
       ('法国', 'FR');
```

### 1.2 创建类 Country

在`src/main/java`下新建包：com.yefcion.mybatislearn.model

在 model 包下创建类 Country，这个类与数据库中的表 country 对应

```java
package com.yefcion.mybatislearn.model;

@Data
public class Country {
    private Integer id;
    private String countryName;
    private String countryCode;
}
```





## 2. 配置相关文件

### 2.1 配置 pom.xml

修改项目根目录下的 `pom.xml` 文件，注意看 <!-- --> 部分注释。注意：JDK、MySQL 版本

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.zte</groupId>
    <artifactId>mybatisdoc</artifactId>
    <version>1.0-SNAPSHOT</version>

    <!-- 设置源代码的编码方式 utf-8 -->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <!-- 设置编译源代码的 JDK 版本，与自己的 Java JDK 对应 -->
    <build>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <!-- 添加重要的 MyBatis 的依赖坐标和 mysql 驱动的依赖坐标 -->
    <dependencies>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.1</version>
        </dependency>
        <!-- mysql 的版本看你是用的数据库是什么版本 Navicat 查看信息可以看到-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.16</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.12</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.12</version>
        </dependency>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
    </dependencies>

</project>
```

在 IDEA 中，如果没有特殊配置过，修改完 pom 文件，需要手动导入下，写完后记得 `Import Changes`



### 2.2 配置 mybatis-config.xml

在 `src/main/resources` 目录下创建 `mybatis-config.xml` 文件，内容如下。

注意：

`<dataSource type="UNPOOLED"> `  部分 `url/username/passwor`  的 `value`。

`<typeAliases> <mappers> `  部分的地址

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
<!--    <settings>节点中的logImpl属性配置指定使用LOG4J输出日志。-->
    <settings>
        <setting name="logImpl" value="LOG4J"/>
    </settings>

<!--    <typeAliases>元素下面配置了一个包名，通常确定一个类的时候需要使用类的全限定名称，例如com.yefcion.mybatislearn.model.Country。
        在MyBatis中需要频繁用到类的全限定名称，为了方便使用，我们配置了com.yefcion.mybatislearn.model包，这样配置后，在使用类的时候不需要写包名的部分，只使用Country即可。-->
    <typeAliases>
        <package name="com.yefcion.mybatislearn.model"/>
    </typeAliases>

<!--    <environments>环境配置中主要配置了数据库连接，如这里我们使用的是本机MySql中的mybatis_action_db数据库，
        用户名为root，没有密码（大家可根据自己的实际情况修改数据库及用户名和密码）。-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC">
                <property name="" value=""/>
            </transactionManager>
            <dataSource type="UNPOOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis_action_db?serverTimezone=UTC"/>
                <property name="username" value="root"/>
                <property name="password" value=""/>
            </dataSource>
        </environment>
    </environments>

<!--    <mappers>中配置了一个包含完整类路径的CountryMapper.xml，这是一个MyBatis的Sql语句和映射配置文件。-->
    <mappers>
        <mapper resource="com/yefcion/simple/mapper/CountryMapper.xml"/>
    </mappers>
</configuration>
```

### 2.3 配置 CountryMapper.xml

在`src/main/resources`下创建目录 com/yefcion/simple/mapper，然后在该目录下创建 CountryMapper.xml 文件，内容如下，注意 namespace

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.yefcion.simple.mapper.CountryMapper">
    <select id="selectAll" resultType="Country">
        SELECT id,countryname,countrycode FROM country
    </select>
</mapper>

    <!--
        yefcion.simple.mapper：XML的根元素，属性namespace定义了当前XML的命名空间。
        select：我们所定义的一个Select查询。
        id属性：定义了当前Select查询的唯一id。
        resultType：定义了当前查询的返回值类型，此处就是指实体类Country，前面配置中提到的包名主要用于这里，如果没有设置包名，此处就需要写成resultType=”com.yefcion.mybatislearn.model.Country”。
        SELECT id,countryname,countrycode FROM country：查询Sql语句。
    -->
```

### 2.4 配置 log4j.properties

在`src/main/resources`下新建 log4j.properties 文件，内容如下，注意第二行。

```properties
log4j.rootLogger=ERROR, stdout
log4j.logger.com.yefcion.simple.mapper=TRACE
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n 
```

## 3.编写测试代码

在`src/test/java`下创建包：com.yefcion.mybatislearn.mapper，然后创建测试类 CountryMapperTest，代码如下。

```java
package com.yefcion.mybatislearn.mapper;

import com.yefcion.mybatislearn.model.Country;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.BeforeClass;
import org.junit.Test;

import java.io.IOException;
import java.io.Reader;
import java.util.List;

public class CountryMapperTest {
    private static SqlSessionFactory sqlSessionFactory;

    @BeforeClass
    public static void init() {
        try {
            Reader reader = Resources.getResourceAsReader("mybatis-config.xml");
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
            reader.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Test
    public void testSelectAll() {
        SqlSession sqlSession = sqlSessionFactory.openSession();

        try {
            List<Country> countryList = sqlSession.selectList("selectAll");
            printCountryList(countryList);
        } finally {
            sqlSession.close();
        }
    }

    private void printCountryList(List<Country> countryList) {
        for (Country country : countryList) {
            System.out.printf("%-4d%4s%4s\n", country.getId(), country.getCountryName(), country.getCountryCode());
        }
    }
}
```

用 Junit 执行 Test，完成测试。



## 4.各种报错

1.Establishing SSL connection without server's identity

 https://blog.csdn.net/wd2014610/article/details/79666770 

关闭 SSL 连接

2.com.mysql.jdbc.exceptions.jdbc4.MySQLNonTransientConnectionException: Could not create conn

 https://blog.csdn.net/badao_liumang_qizhi/article/details/86553776 

mysql maven 依赖版本和 使用的数据库的 版本不一致

