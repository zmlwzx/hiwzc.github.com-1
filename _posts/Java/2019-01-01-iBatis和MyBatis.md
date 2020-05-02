---
title: iBatis和MyBatis
toc: false
permalink: /posts/java/ibatis-mybatis.html
categories: Java
date: 2017-01-01
---

## 关于 resultMap 的对应关系

对于 iBatis，查询的字段比 resultMap 对应的字段多时不会报错，但如果 resultMap 比查询数据的字段多了，即有字段没有查询出来数据，那就会报错。比如resultMap 中有 name 这个字段，但是SQL中没有查 name 字段，就会报类似如下错误：

```text
org.springframework.jdbc.UncategorizedSQLException: SqlMapClient operation; uncategorized SQLException for SQL []; SQL state [S0022]; error code [0];
--- The error occurred in com/hiwzc/demo/ibatis/sqlmap/User.xml.  
--- The error occurred while applying a result map.  
--- Check the Evaluation.get-Evaluation-list.  
--- Check the result mapping for the 'name' property.  
--- Cause: java.sql.SQLException: Column 'name' not found.; nested exception is com.ibatis.common.jdbc.exception.NestedSQLException:
--- The error occurred in
com/hiwzc/demo/ibatis/sqlmap/User.xml.
--- The error occurred while applying a result map.  
--- Check the Evaluation.get-User-list.  
--- Check the result mapping for the 'name' property.  
--- Cause: java.sql.SQLException: Column 'name' not found.
```

对于 MyBatis，resultMap 和 查询字段谁多谁少都不会报错的。
