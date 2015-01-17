---
layout: post
title: "Hive Cheatsheet"
description: ""
category: "note"
tags: ["hive", "cheatsheet"]
---
{% include JB/setup %}

# Hive Cheatsheet

作者：[TheLover_Z](http://theloverz.me)

今天整理 Simplenote 的时候突然发现之前实习的时候记录下来的一份 cheatsheet. 虽然最后没有用上，但是整理一下吧…

## 命令

Hive 和 SQL 语句极为类似，比如说这样一眼就明白的：

    CREATE TABLE x (a INT);
    SELECT * FROM x;
    DROP TABLE x;

或者也可以这样，用于加载一些外部的配置：

    hive -e "SELECT * FROM mytable LIMIT 3";
    SELECT * FROM whatsit WHERE i = ${hiveconf:y};

## 从文件执行

三种方法：

    hive -f /path/to/file/withqueries.hql

    $ cat /path/to/file/withqueries.hql
    SELECT x.* FROM src x;

    $ hive
    hive> source /path/to/file/withqueries.hql;

## 配置 metastore location

    <?xml version="1.0"?>
    <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
    <configuration>
      <property>
        <name>hive.metastore.warehouse.dir</name>
        <value>/home/me/hive/warehouse</value>
        <description>Local or HDFS directory where Hive keeps table contents.</description>
      </property>
      <property>
        <name>hive.metastore.local</name>
        <value>true</value>
        <description>Use false if a production metastore server is used.</description>
      </property>
      <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:derby:;databaseName=/home/me/hive/metastore_db;create=true</value>
        <description>The JDBC connection URL.</description>
      </property>
    </configuration>

这是一个典型的配置文件，其中 `hive.metastore.warehouse.dir` 指定了数据库的存放位置，`hive.metastore.local` 指定了是否存放在本地（生产环境一般不在本地），剩下的 `javax.jdo.option.ConnectionURL` 不用解释。

## 配置 mysql

    <?xml version="1.0"?>
    <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
    <configuration>
      <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:mysql://db1.mydomain.pvt/hive_db?createDatabaseIfNotExist=true</value>
      </property>
      <property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>com.mysql.jdbc.Driver</value>
      </property>
      <property>
        <name>javax.jdo.option.ConnectionUserName</name>
        <value>database_user</value>
      </property>
      <property>
        <name>javax.jdo.option.ConnectionPassword</name>
        <value>database_pass</value>
      </property>
    </configuration>

这个基本上就是改一下 ConnenctionURL 用户名密码就 ok.

## `.hiverc`

    ADD JAR /path/to/custom_hive_extensions.jar;
    set hive.cli.print.current.db=true;
    set hive.exec.mode.local.auto=true;

新手直接用这个就好，第一行添加 java 环境，第二行显示当前使用的数据库，第三行开启 local mode.

## 注释

    -- 这是注释
    -- 别笑，我当初试了好几个都不对…

## 常用命令

### 创建数据库并指定位置

    $ CREATE DATABASE financials
    > LOCATION '/my/preferred/directory';

### 注释

    $ CREATE DATABASE financials
    > COMMENT 'Holds all financial tables';

### 创建带 DB properties 的数据库

    $ CREATE DATABASE financials
    > WITH DBPROPERTIES ('creator' = 'Mark Moneybags', 'date' = '2012-01-02');

### 查看数据库

    $ DESCRIBE DATABASE EXTENDED financials;
    financials hdfs://master-server/user/hive/warehouse/financials.db
    {date=2012-01-02, creator=Mark Moneybags);

### 使用数据库

    $ USE financials;

这个设置过上面提到过的 `set hive.cli.print.current.db=true;` 以后会显示当前的 DB.

### 查看表

    $ SHOW TABLES;

## 创建 external 表

    CREATE EXTERNAL TABLE IF NOT EXISTS stocks (
      exchange STRING,
      symbol STRING,
      ymd STRING,
      price_open FLOAT,
      price_high FLOAT,
      price_low FLOAT,
      price_close FLOAT,
      volume INT,
      price_adj_close FLOAT)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    LOCATION '/data/stocks';

没啥好说的……

## Partitions

    SHOW PARTITIONS employees;
    SHOW PARTITIONS employees PARTITION(country='US');

注意：Partition 不是实际存在的字段，而是伪列。
