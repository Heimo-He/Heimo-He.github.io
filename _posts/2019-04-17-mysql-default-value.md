---
title: MySQL Field 'xxxx' doesn’t have a default value
categories: mysql
tags:
 - mysql
---

#### 问题

```
SQLSTATE[HY000]: General error: 1364 Field 'xxxx' doesn't have a default value
```

#### 原因

- **1. 该字段为设置default value**
- **2. mysql配置开启了严格模式**

<!-- more -->

#### 解决方案

- **1. 为改字段设置default value**

```sql
alter table <table_name> alter column <column_name> set default '';
```

- **2. 编辑`my.cnf`(windows为`my.ini`)中`sql_mode`，移除`STRICT_TRANS_TABLES`**（直接删除`sql_mode`无效），编辑完需要重启mysql

```ini
#sql_mode="NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES"
sql_mode=""
```



> ***转载使用注明出处。原文链接 [https://heimo-he.github.io/mysql/2019/04/17/mysql-default-value/](https://heimo-he.github.io/mysql/2019/04/17/mysql-default-value/)***