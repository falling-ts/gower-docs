---
title: Log Viewer
url: "/en/single/log-viewer"
---

## Grafana, Loki, and Promtail Working Together to View Gower Logs via Web

## Grafana

Grafana is an open-source data visualization and monitoring platform that supports multiple data sources, including Graphite, InfluxDB, Prometheus, Elasticsearch, and more. It allows for real-time querying, visualization, and alerting of data from these sources.

Grafana provides various visualization options through custom plugins and panels, such as line charts, bar charts, heatmaps, dashboards, maps, and more.

### Configure Account and Password

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


### Start

The standard local release version depends on Grafana, so there's no need to start Grafana separately.

```yaml
    depends_on:
      - caddy
      - tidb
      - mysql
      - grafana
```


It is recommended to use a script to run the application.

```shell
$ ./docker/run-dev
```


### Access Grafana

`http://localhost:3000`

#### Login

Enter the account and password configured above.

![](https://note.youdao.com/yws/api/personal/file/WEB1a6ce4fa1c210172e85349342b04c4b6?method=download&shareKey=7e40521c589102ce63a004b477a62039)

#### Add Data Source

Mainly add the Loki data source.

![](https://note.youdao.com/yws/api/personal/file/WEBf5c6398bcd5ffbe9bf8d7d57e035a7ff?method=download&shareKey=11f226bd6f178a9cb1f38722640fc639)

#### Select Loki

![](https://note.youdao.com/yws/api/personal/file/WEB51d2e8d9fd2979546ed3cfe5182921ab?method=download&shareKey=bd72a5a2563eacef83aa00b31470e053)

#### Configure Loki Request

Enter `http://loki:3100` in the URL.

![](https://note.youdao.com/yws/api/personal/file/WEB2489eaf999b8de7ab1fb5e46183d0293?method=download&shareKey=9f323201c41c64e2f967130b0d6b7a18)

#### Save and Test

Two checkmarks indicate that the data source has been added successfully and labels have been retrieved successfully. If label retrieval fails, it may be because no logs have been generated in the directory.

![](https://note.youdao.com/yws/api/personal/file/WEBebdbcd155187c5efd35823223666cf2e?method=download&shareKey=c5cdd8d54e75c0e9652544a6d670e05e)

#### Explore

Click the explore button in the left menu.

![](https://note.youdao.com/yws/api/personal/file/WEB262503ac3eb439468a4d504103a2be54?method=download&shareKey=91957920cca8e5f13cee4c74ae4d0289)

#### Configuration

Configure as shown in the image:

##### Select Task

Choose `filename` or `job`, then select the specific log file or task.

##### Delete Line Contains
##### Add JSON Formats
##### Add Label Filter
##### Run Query

![](https://note.youdao.com/yws/api/personal/file/WEB7f9a1631b2a2f2ffb2b847c8af533ab2?method=download&shareKey=ea036b856b22ec3e8a5b13ba2fc5f8f3)

#### View Logs

![](https://note.youdao.com/yws/api/personal/file/WEB1cb9e0fbcc96ecdf6f4627b176b6521c?method=download&shareKey=14d95029cc1af03a27b6ce0ede074895)

#### Filter Time

![](https://note.youdao.com/yws/api/personal/file/WEB06a1b23121f555021054b34c02643024?method=download&shareKey=236a8fc9a2f5b83ce57d84fa8e91d594)

## Loki

Loki is a data source provided by Grafana Labs. Its role is to provide a data interface for Grafana, with the default listening port being 3100.

It also provides a log data push interface for Promtail.

## Promtail

Promtail is a local log aggregation driver that reads log data and pushes it to Loki via its push interface, making the data available for Grafana to use.
