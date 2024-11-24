---
title: 数据库
url: "/single/数据库"
---

## TiDB

TiDB 是一款分布式, 兼容 MySQL 语法的数据库.

如果只是做一个小应用, 比如后台管理之类的, 一般是用不上分布式架构的, 直接运行在 Docker 里面就可以了.

当然 TiDB 我只是做测试, 学习用的, 生成环境默认使用的还是 MySQL5.7

如果想部署分布式, 访问[TiDB](https://docs.pingcap.com/zh/tidb/stable)官网教程吧, 有很详细的步骤.

### 修改 root 密码

连接账户默认是 root, 密码是 root.

可以在 `third_apps/tidb/init.sql` 修改密码

```sql
ALTER USER 'root'@'%' IDENTIFIED BY 'root';
FLUSH PRIVILEGES;

DROP DATABASE IF EXISTS test;
CREATE DATABASE IF NOT EXISTS gower CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```
> 第一句的句尾 root 修改为你的密码, 然后重新打包启动 `./docker/run-dev`
>
> 最后一句是初始化数据库, 可以改为你的数据库名称, 同时不要忘记修改 `envs/.env.test` 的环境配置


## MySQL5.7

### 修改 root 密码

`third_apps\mysql5.7\Dockerfile`

```yaml
ENV MYSQL_ROOT_HOST="%" # 设置访问的host
ENV MYSQL_DATABASE="gower" # 初始数据库
ENV MYSQL_DATABASE_CHAR="utf8mb4,utf8mb4_unicode_ci" # 数据库编码
ENV MYSQL_ROOT_PASSWORD="root" # root 密码
```

> 修改完成后, 重新构建启动 `./docker/run-dev`
