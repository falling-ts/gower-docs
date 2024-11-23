---
title: 日志查看器
url: "/single/日志查看器"
---

grafana, loki, promtail 三组件配合使用, 实现通过网页, 查看 gower 日志功能

## Grafana

Grafana 是一个开源的数据可视化和监控平台, 它支持多种数据源, 包括 Graphite、InfluxDB、Prometheus、Elasticsearch 等, 能够对这些数据源中的数据进行实时的查询、可视化和告警.

Grafana 通过使用自定义的插件和面板, 提供了各种可视化方式, 例如: 折线图、柱状图、热力图、仪表盘、地图等.

### 配置账户密码

`third_apps\grafana\share\conf\defaults.ini`

```ini
.
.
.
#################################### Security ############################
[security]
# disable creation of admin user on first start of grafana
disable_initial_admin_creation = false

# default admin user, created on startup
admin_user = admin

# default admin password, can be changed before first start of grafana, or in profile settings
admin_password = admin
.
.
.
```

### 启动

普通本地发布版依赖 grafana, 所以无需单独指定启动 grafana

```yaml
    depends_on:
      - caddy
      - tidb
      - mysql
      - grafana
```

建议使用脚本运行应用

```shell
$ ./run-dev
```

### 访问 Grafana

`http://localhost:3000`

#### 登录

输入上面配置的账户和密码.

![](https://note.youdao.com/yws/api/personal/file/WEB1a6ce4fa1c210172e85349342b04c4b6?method=download&shareKey=7e40521c589102ce63a004b477a62039)

#### 添加数据源

主要是添加 Loki 数据源

![](https://note.youdao.com/yws/api/personal/file/WEBf5c6398bcd5ffbe9bf8d7d57e035a7ff?method=download&shareKey=11f226bd6f178a9cb1f38722640fc639)

#### 选择 Loki

![](https://note.youdao.com/yws/api/personal/file/WEB51d2e8d9fd2979546ed3cfe5182921ab?method=download&shareKey=bd72a5a2563eacef83aa00b31470e053)

#### 配置 Loki 请求

在 URL 上输入 `http://loki:3100`

![](https://note.youdao.com/yws/api/personal/file/WEB2489eaf999b8de7ab1fb5e46183d0293?method=download&shareKey=9f323201c41c64e2f967130b0d6b7a18)

#### 保存和测试

出现两个对号, 代表数据源添加成功, 标签获取成功. 标签获取失败, 是因为可能目录下没有生成日志.

![](https://note.youdao.com/yws/api/personal/file/WEBebdbcd155187c5efd35823223666cf2e?method=download&shareKey=c5cdd8d54e75c0e9652544a6d670e05e)

#### 探索

点击左侧菜单栏的探索按钮

![](https://note.youdao.com/yws/api/personal/file/WEB262503ac3eb439468a4d504103a2be54?method=download&shareKey=91957920cca8e5f13cee4c74ae4d0289)

#### 配置

配置如图所示:

##### 选择任务

选择 filename 或 job, 然后选择具体日志文件或者任务

##### 删除 Line Contains
##### 添加 Json Formats
##### 添加 Label Filter
##### 运行查找

![](https://note.youdao.com/yws/api/personal/file/WEB7f9a1631b2a2f2ffb2b847c8af533ab2?method=download&shareKey=ea036b856b22ec3e8a5b13ba2fc5f8f3)

#### 查看日志

![](https://note.youdao.com/yws/api/personal/file/WEB1cb9e0fbcc96ecdf6f4627b176b6521c?method=download&shareKey=14d95029cc1af03a27b6ce0ede074895)

#### 过滤时间

![](https://note.youdao.com/yws/api/personal/file/WEB06a1b23121f555021054b34c02643024?method=download&shareKey=236a8fc9a2f5b83ce57d84fa8e91d594)

## Loki

Loki 是 Grafana 官方出品的数据源, 作用是给 grafana 提供数据接口, 监听端口默认是 3100.

同时也给 promtail 提供日志数据推送接口.

## Promtail

本地日志汇总读取的驱动程序, 通过调用 Loki 推送接口, 将日志数据推送给数据源, 以供 grafana 使用.
