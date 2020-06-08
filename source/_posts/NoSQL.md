---
title: NoSQL
date: 2019-02-08 20:04:46
categories:
- database
tags:
- mongodb
---

# NoSQL

NoSQL(Not Only SQL)意指不仅仅是SQL(Structured Query Language)。是为了应对传统关系型数据库所不能解决的挑战而生的。可以分为以下四类：
- 文档数据库（mongodb）
- 键值对数据库（redis）
- 列族（column-family）数据库（Cassandra）
- 图数据库（Neo4J）

通用情况下，文档数据库能提供良好的性能以及可伸缩性。在不需要复杂的查询需求时，键值对数据库可以提供最佳性能。

## 文档数据库（document database）

文档（document）也就是自包含的一块信息，用于描述单一实体（entity）。如下面的JSON文档：
```json
{
  "firstName": "John",
  "lastName": "Dahn",
  "age": 25,
  "address": {
    "streetAddress": "21 2nd Street",
    "city": "Shanghai"
  }
}
```
当然也可以使用XML数据格式甚至是二进制格式表示这个document，在这里我们采用了JSON。关系型数据库中，这样的document会存放在两个不同的表中，一个persons表，一个addresses表。文档数据库中就仅仅是一个文档。

## 键值数据库

键值数据库就是保留了必要功能的精简版的文档数据库。键值数据库的键是一个特殊的ID，用于标识某一特定文档，而值则是该键对应的文档。不同的是，键值数据库只允许通过键查询，而文档数据库可以根据文档的内容进行查询，这就使得键值数据库可针对基于键的查询做性能上的优化，并且它们也可以对值进行压缩。

Redis就是很优秀的键值数据库，实际上它将整个数据库保存在RAM中，而在硬盘中做备份，具有闪电般的性能。

## MongoDB

MongoDB名字来源于humongous，下载的MongoDB包含的mongod.exe是MongoDB的守护程序，也就是主服务器二进制文件。为了开启数据库服务器，可以执行它。而mongo.exe则是MongoDB提供的repl工具，用于管理数据库以及进行一些测试性的实验。

MongoDB的运行需要一个数据文件夹（在MongoDB的语境中叫做`dbpath`）来存储数据库数据。默认的`dbpath`是`data/db`（取决于当前工作的磁盘），最好总是指定一个明确的`dbpath`。