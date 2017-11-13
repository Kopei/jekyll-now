---
layout: post
title: Aliyun RDS for Mysql
---

## RDS for Mysql

### Rds的一些使用限制
- 不提供root或sa账户
- 修改部分参数限制
- 通过命令行和图形界面进行逻辑备份，控制台和api进行物理备份。还原亦然。
- 使用命令行和图形界面进行逻辑导入，mysql命令行和数据迁移服务进行数据迁移。
- 存储引擎：只支持InnoDb和TokuDB。
- 默认主备复制双节点，无需手动搭建，用户不能访问slave。5.7只有基础版本，没有主备，支持原生Json。
- 只能通过api和控制台进行重启。
- 普通账户不可以自定义授权，5.7不支持创建普通账户权限。

### 从本地迁移数据到云上
- 云上的数据库账号需要和本地一致
- 支持DTC, FTP, mysqldump
- [mysqldump doc](https://help.aliyun.com/document_detail/26133.html)

### 容灾
- RDS 通过数据传输服务（DTS）实现主实例和异地灾备实例之间的实时同步。
  主实例和灾备实例均搭建主备高可用架构，当主实例所在区域发生突发性自然灾害等状况，
  主节点（Master）和备节点（Slave）均无法连接时，可将异地灾备实例切换为主实例，
  在应用端修改数据库链接地址后，即可快速恢复应用的业务访问。
- 多收DTS的费用
- 灾备实例不支持备份设置、备份恢复、数据迁移、数据库管理、申请外网访问地址、修改连接地址功能。

### mysql和oss搭配使用
- 多类数据存储解决方案

### 数据库性能测试
-[SysBench](https://github.com/akopytov/sysbench)
- 准备数据
```
sysbench --num-threads=32 --max-time=3600 --max-requests=999999999 --test= oltp.lua --oltp-table-size=10000000 
--oltp-tables-count=64 --db-driver=mysql --mysql-table-engine=innodb 
--mysql-host= XXXX --mysql-port=3306 --mysql-user= XXXX --mysql-password= XXXX prepare
```
- 压测性能
```
sysbench --num-threads=32 --max-time=3600 --max-requests=999999999 --test= oltp.lua --oltp-table-size=10000000
--oltp-tables-count=64 --db-driver=mysql --mysql-table-engine=innodb --mysql-host= XXXX --mysql-port=3306 
--mysql-user= XXXX --mysql-password= XXXX run
```
- 清理环境
```
sysbench --num-threads=32 --max-time=3600 --max-requests=999999999 --test= oltp.lua --oltp-table-size=10000000
--oltp-tables-count=64 --db-driver=mysql --mysql-table-engine=innodb --mysql-host= XXXX --mysql-port=3306
--mysql-user= XXXX --mysql-password= XXXX cleanup
```

