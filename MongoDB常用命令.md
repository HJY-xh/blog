---
title: MongoDB常用命令
categories: 技术
tags:
 - NoSQL
 - MongoDB
date: 2020-01-31 17:22:58
---

>本博客 [hjy-xh](https://hjy-xh.github.io/)，转载请申明出处

为了从命令提示符下运行 MongoDB 服务器，必须从 MongoDB 目录的 bin 目录中执行 mongod.exe 文件。
```
E:\MongoDB\bin>mongod --dbpath E:\MongoDB\data\db
```

### 数据库:

##### 查看当前版本
```
db.version()
```
### 数据库:

##### 查看所有数据库
```
show dbs
```

##### 查看当前数据库
```
db
```

##### 创建数据库
```
use DATABASE_NAME
```

##### 删除当前数据库
```
db.dropDatabase()
```

### 集合:

##### 在当前数据库创建集合
```
db.createCollection(name, options)
```
参数说明：

- name: 要创建的集合名称
- options: 可选参数, 指定有关内存大小及索引的选项


字段	|类型|描述
------------- | ------------- | -------------
capped	|布尔	（可选）|如果为 true，则创建固定集合。固定集合是指有着固定大小的集合，当达到最大值时，它会自动覆盖最早的文档。当该值为 true 时，必须指定 size 参数。
autoIndexId	|布尔	（可选）|如为 true，自动在 _id 字段创建索引。默认为 false。
size|数值	（可选）|为固定集合指定一个最大值，以千字节计（KB）。如果 capped 为 true，也需要指定该字段。
max	|数值	（可选）|指定固定集合中包含文档的最大数量。

在插入文档时，MongoDB 首先检查固定集合的 size 字段，然后检查 max 字段。

例子:创建固定集合 hjy_friends，整个集合空间大小 6142800 KB, 文档最大个数为 10000 个。
```
db.createCollection("hjy_friends", { capped : true, autoIndexId : true, size : 6142800, max : 10000 } )
```

在 MongoDB 中，可以不需要创建集合。当插入一些文档时，MongoDB 会自动创建集合。
```
db.hjy_classmates.insert({"name":"小黄"})
```

##### 查看当前数据库下所有集合
```
show tables

show collections
```

##### 删除集合
```
db.COLLECTION_NAME.drop()
```

### 文档:

##### 向集合中插入文档
```
db.COLLECTION_NAME.insert(document)
```
例子:在hjy_friends集合中创建一条文档:
```
db.hjy_friends.insert({name:"小黄朋友",age:22})
```

##### 更新文档
```
db.COLLECTION_NAME.update()
```
例子:
```
db.hjy_friends.update({age:22},{$set:{name:"小黄的朋友"}})
```

##### 删除文档(2.6版本后)
```
db.collection.remove(
   <query>,
   {
     justOne: <boolean>,
     writeConcern: <document>
   }
)
```
例子:
```
db.hjy_friends.update({age:22},{$set:{name:"小黄的朋友"}})
```

##### 查询的文档
```
db.COLLECTION_NAME.find()
```
pretty()方法以格式化的方式来显示所有文档
```
db.COLLECTION_NAME.find().pretty()
```