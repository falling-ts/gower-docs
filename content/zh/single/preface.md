---
title: 前言
url: "/single/前言"
aliases:
  - "/single/"
---

> 注: 由于方便书写代码, 文档标点默认使用英文标点.

重在快速启动, 集成当前开源项目常用的工具, 实现快速开始开发业务. 默认项目架构是使用 Go 模板, 既可以开发接口, 也可以前后端集成在一起.

## 项目背景

主要是作者为学习 Go 语言开发，结合几年 Laravel 开发经验，实现用 Go 也能很快做出上线的项目.

## 简单介绍

Gower 是一个基于 Go/Gin 的快速开发架构, 内部尽可能减少包间的引用, 来避免环形引用问题.

整体设计, 以业务开发为核心, 有 app 包提供所有结构体实例的服务, app 包通过服务提供者, 来获取绑定的各类服务. 服务提供者导入服务实例以及服务载体, 再由 app 导入服务提供者, 实现 services 的绑定.

绑定以 `map[string]func (...services.Service) services.Service` 映射为主, 当需要使用时, 就会执行函数, 并缓存服务, 来实现动态获取服务的功能

用一个注册树模式的类比说明这个设计: 假如一棵树的树干是 app 包, 我们是在树干的中心进行业务开发的, app 包不能逆向引用我们的业务代码, 业务代码可以向外引用外面的服务包, 树干通过树枝节点获取一个服务, 而提供服务的树枝就是一个服务提供者, 树枝通过分树枝组装实际的服务以及服务的载体, 实现对内提供服务功能. 整体就是内可以引外, 外不能引内.

## 当前服务功能列表

```
auth: JWT 无状态的用户校验服务
cache: 缓存服务
config: 配置服务
cookie: 内置加解密的 cookie 服务
db: gormDB 服务
exception: 异常服务
logger: 日志服务
passwd: 密码散列服务
response: 响应体服务
route: Gin 路由服务
symcrypt: 对称加密服务
translate: 翻译服务
upload: 上传服务
util: 各类杂项方法服务
validator: 请求参数校验服务
```

以上功能, 大部分基本实现基础使用, 复杂深化的功能, 大部分是继承三方开源的功能.

## 关于前端

前端代码, 与传统前端有很大区别, 主要是 html 文件不再通过前端进行管理, 前端框架主要进行库文件打包, 也就是只打包出 js 和 css 文件. html 通过 Go 模板提供.

前端技术栈列表

```
vite
typescript
vue
tailwindcss
stylus
daisyui
animate.css
autoprefixer
postcss
localforage
js-cookie
jquery
simplebar
```

具体信息, 请查阅项目根目录的 `package.json`
