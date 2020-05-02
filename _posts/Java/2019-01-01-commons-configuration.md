---
title: Commons Configuration
toc: true
permalink: /posts/java/commons-configuration.html
categories: Java
date: 2017-01-01
---

## 使用 1.x 版本

### 关于默认解析为数组

使用 1.x 版本的 Apache Commons Configuration 的 PropertiesConfiguration 要注意，默认情况下，用逗号分隔的属性会自动分隔为数组，然后 getString 获取值时，只会返回第一个元素。

示例配置

```properties
department=201,202,203
```

示例代码

```java
PropertiesConfiguration configuration = new PropertiesConfiguration(filePath);
System.out.println(configuration.getString("department")); \\ 输出201

PropertiesConfiguration configuration = new PropertiesConfiguration();
configuration.setDelimiterParsingDisabled(true);
configuration.load(new File(filePath));
System.out.println(configuration.getString("department")); \\ 输出201,202,203
```

### 关于刷新策略

```java
FileChangedReloadingStrategy reloadingStrategy = new FileChangedReloadingStrategy();
reloadingStrategy.setRefreshDelay(TimeUnit.SECONDS.toMillis(10));
configuration.setReloadingStrategy(reloadingStrategy);
```
