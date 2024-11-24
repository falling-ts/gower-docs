---
title: 目录结构
url: "/single/目录结构"
---

```go
app // 核心包
|--api // 接口目录
|  |--controllers // 接口控制器
|  |--middlewares // 接口中间件
|  |--requests // 接口请求验证器
|--consoles // 命令行源代码
|--exceptions // 异常服务挂载的内容
|--http // Web目录
|  |--controllers // Web控制器
|  |--middlewares // Web中间件
|  |--requests // Web请求验证器
|--middlewares // 公共中间件
|--models // 模型
|--providers // 服务提供者
|--responses // 响应服务挂载的内容
|--app.go // 核心结构体
|--controller.go // 公共控制器结构体
|--model.go // 公共模型数据处理器
|--request.go // 公共请求验证器
bootstrap // 启动包
|--0 // 控制启动路由, 由于 go1.21 的导入顺序改为 ASCII 排序, 故需要数字目录自定义导入排序
|--1 // 控制启动模板库, 由于 go1.21 的导入顺序改为 ASCII 排序, 故需要数字目录自定义导入排序
cmd // 非 Docker 相关的运行和打包
configs // 配置包, 配置服务的挂载内容
docker // Docker 相关的运行和打包
envs // 后端环境包
public // 静态资源包, 同为 Web 服务软件的 root 目录
resources // 模板库
|--app // 前端根目录
|  |--api // 前端 api
|  |--lib // 前端库
|  |--styles // 前端样式源文件
|  |--main.ts // 前端构建入口文件
|--lang // 以后做多语言功能
|--views // html 模板
routes // 路由配置
services // 服务
|--auth // JWT 服务
|--cache // 缓存服务
|--config // 配置服务
|--cookie // cookie 服务
|--db // 数据库服务
|--exception // 异常服务
|--logger // 日志服务
|--passwd // 密码哈希服务
|--response // 响应服务
|--route // 路由服务
|--symcrypt // 对称加密服务
|--translate // 翻译服务
|--upload // 上传服务
|--util // 工具函数服务
|--validator // 验证器服务
storage // 本地仓库
|--app // 项目静态文件与上传的文件
|--caches // 缓存长久化
|--logs // 日志
tests // 测试包
|--benchmarks // 基准测试包
third_apps // 第三方软件
|--caddy // Web 服务器
|  |--config // 服务器配置
|  |--data // 服务器数据, CA 证书, 根证书
|  |--site // 站点, 实际使用 public 即可
|--grafana // 日志查看工具, 访问 localhost:3000
|--loki // grafana 数据源
|--mysql5.7 // 数据库
|--promtail // 本地 logs 推送驱动
|--tidb // 数据库
trans // 翻译包
utils // 通用基础类型处理包
```