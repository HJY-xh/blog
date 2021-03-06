---
title: 关系型数据库遵循的ACID规则
categories: 技术
tags:
 - 关系型数据库
date: 2020-01-24 09:35:24
---

>本博客 [hjy-xh](https://hjy-xh.github.io/)，转载请申明出处

>** 事务的英文中是transaction  **

>** ACID是原子性（atomicity）、一致性（consistency）、隔离性（isolation）、持久性（durability）的缩写 **

### 事务
事务是访问数据库的一个操作序列，数据库应用系统通过事务集来完成对数据库的存取。
事务的正确执行使得数据库从一种状态转换为另一种状态。

### 原子性（atomicity）
原子性很容易理解，即不可分割，事务里的所有操作要么全部被执行，要么全部不执行。也就是说事务成功的条件是事务里的所有操作都成功，只要有一个操作失败，整个事务就失败，需要**回滚<sup>[1]</sup>**。

### 一致性（consistency）
事务的执行使得数据库从一种正确状态转换成另外一种正确状态。

例子:假设用户A和用户B两者账户的钱加起来一共是5000，那么不管A和B之间如何转账，转几次账，事务结束后两个账户户的钱相加起来应该还得是5000，这就是事务的一致性。

### 隔离性（isolation）
所谓的隔离性(在有些教程中也称独立性)是指并发的事务之间不会互相影响，如果一个事务要访问的数据正在被另外一个事务修改，只要另外一个事务未提交，它所访问的数据就不受未提交事务的影响。

例子:A账户转100元至B账户，在这个交易还未完成的情况下，如果此时B查询自己的账户，是看不到新增加的100元的。

### 持久性（durability）
事务正确提交之后，其结果将永远保存在数据库之中，即使在事务提交之后有了其他故障，事务的处理结果也会得到保存。

#### 释义:
**回滚**:删除由一个或多个部分完成的事务执行的更新。为保证应用程序、数据库或系统错误后还原数据库的完整性，需要使用回滚。
回滚泛指程序更新失败, 返回上一次正确状态的行为。