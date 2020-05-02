---
title: Java操作Derby数据库
toc: true
permalink: /posts/java/java-derby.html
categories: Java
date: 2017-01-01
---

Apache Derby 是一个用 Java 语言编写的开源关系型数据库，得益于 Java 的跨平台特性，Derby 可以运行在大多数操作系统上。Derby 占用的存储和运行内存都很小，可以容易地内嵌到 Java 应用程序中。此外，Derby 也支持最常见都客户端-服务器模式。基于 Derby 的应用可以很容易的切换为使用功能更强大的数据库。Apache Derby 入门可参考 [Apache Derby Tutorial](http://db.apache.org/derby/papers/DerbyTut/index.html)。

## 安装

从官网 <http://db.apache.org/derby/> 下载解压即可。运行 Derby 需要设置 JAVA_HOME 环境变量，使用命令行工具运行 Derby 建议设置 DERBY_HOME 和 PATH 环境变量。

```shell
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home/
export DERBY_HOME=/opt/apache/db-derby-10.14.2.0-bin/
export PATH=$PATH:$DERBY_HOME/bin
```

## 命令行工具

### sysinfo

```shell
$ sysinfo
------------------ Java 信息 ------------------
Java 版本：        1.8.0_201
......
--------- Derby 信息 --------
[/opt/apache/db-derby-10.14.2.0-bin/lib/derby.jar] 10.14.2.0 - (1828579)
[/opt/apache/db-derby-10.14.2.0-bin/lib/derbytools.jar] 10.14.2.0 - (1828579)
[/opt/apache/db-derby-10.14.2.0-bin/lib/derbynet.jar] 10.14.2.0 - (1828579)
[/opt/apache/db-derby-10.14.2.0-bin/lib/derbyclient.jar] 10.14.2.0 - (1828579)
[/opt/apache/db-derby-10.14.2.0-bin/lib/derbyoptionaltools.jar] 10.14.2.0 - (1828579)
------------------------------------------------------
----------------- 区域设置信息 -----------------
当前区域设置：  [中文/中国 [zh_CN]]
......
------------------------------------------------------
```

### ij

ij 是一个 Derby 的命令行交互式工具。

```sql
$ ij
ij 版本 10.14
ij> help;
ij> connect 'jdbc:derby:/tmp/derby/demodb;create=true';
ij> create table t_user(id int, name varchar(40));
ij> show tables;
ij> insert into t_user values(1, 'hiwzc');
ij> run '/Users/wangzhichao/data.sql';
ij> select * from t_user;
```

## Java 示例

Derby 数据库有两种使用方式，一种是作为内嵌的数据库，与应用程序在同一个 JVM 中运行，数据文件保存在本地文件系统中，数据库只与运行在同一 JVM 中的应用程序通信。另一种是作为客户机-服务器连接，应用程序通过网络连接与数据库通信，应用程序和数据库分别在各自的 JVM 中运行，数据库服务器可以与多个客户机应用程序通信。

### 内嵌数据库示例

maven 的 pom 配置如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.hiwzc.demo</groupId>
    <artifactId>derby-embedded</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <dependency>
            <groupId>org.apache.derby</groupId>
            <artifactId>derby</artifactId>
            <version>10.14.2.0</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

Java 代码如下：

```java
package com.hiwzc.demo.derby;

import java.sql.*;

public class DerbyEmbeddedDemo {
    public static void main(String[] args) {
        String url = "jdbc:derby:/tmp/derby/demodb;create=true";
        try (Connection conn = DriverManager.getConnection(url)) {
            try (Statement s = conn.createStatement()) {
                s.execute("create table t_user(id int, name varchar(40))");
            }

            try (PreparedStatement ps = conn.prepareStatement("insert into t_user values (?, ?)")) {
                for (int i = 0; i < 10; i++) {
                    ps.setInt(1, i);
                    ps.setString(2, "hiwzc" + i);
                    ps.executeUpdate();
                }
            }

            try (PreparedStatement ps = conn.prepareStatement("select * from t_user where id between ? and ?")) {
                ps.setInt(1, 1);
                ps.setInt(2, 5);
                try (ResultSet rs = ps.executeQuery()) {
                    while (rs.next()) {
                        System.out.println(rs.getInt(1) + " | " + rs.getString(2));
                    }
                }
            }

            try (Statement s = conn.createStatement()) {
                s.execute("drop table t_user");
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

```

### 客户端-服务器示例

代码跟内嵌模式类似，只有两点差异：

1. 依赖的包不同

    ```xml
        <dependency>
            <groupId>org.apache.derby</groupId>
            <artifactId>derbyclient</artifactId>
            <version>10.14.2.0</version>
        </dependency>
    ```

2. 数据库连接 URL 不同

    ```java
        String url = "jdbc:derby://localhost:1527//tmp/derby/demodb;create=true";
    ```
