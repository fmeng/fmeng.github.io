---
layout: post
title: "Mysql数据库的备份和恢复"
categories:
- other
---

## 备份库 ##

1. 使用mysqldump工具
2. 命令

	`shell> mysqldump -u root -p password store > /tmp/copy_store.sql`

## 恢复库 ##

1. 使用 mysql工具
2. 命令

	`mysql> source /path/to/store.sql` 没有分号<br/>
	`shell> mysql -u root -p password < /path/to/store.sql`
	