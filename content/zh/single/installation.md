---
title: 安装
url: "/single/安装"
---

## 使用之前, 请安装系统环境要求

> go >= v1.20
>
> nodejs >= v16.13
>
> pnpm >= v7.0
>
> docker >= v20.10
>
> docker compose >= v2.0
>
> git >= 2.39

## 推荐使用源码编译安装

### 安装 gower 命令

```shell
$ go install -tags cli github.com/falling-ts/gower@latest
```

### 创建项目

```shell
$ gower create myproject
```

### 运行在 Docker 里

```shell
$ cd myproject
$ ./run-dev
```

### 访问 `https://localhost`

## Git 安装

### 下载项目文件

```shell
$ git clone https://github.com/falling-ts/gower.git
或
$ git clone https://gitee.com/falling-ts/gower.git
```

### 切换发布版本

```shell
$ git checkout v0.2.1
```

### 安装依赖和命令行工具

```shell
$ pnpm install
$ go mod tidy
$ go install -tags cli
```

### 前端环境

在根目录下，复制出 `.env.test` 和 `.env.production` 两个前端环境文件

### 后端环境

在 `envs/` 目录下，复制出 `.env.test` 和 `.env.production` 两个后端环境文件

### 生成密钥

```shell
$ gower init key
$ gower jwt key
```

### 安装可运行程序

```shell
$ go test
$ go install
```
### 或者直接运行在 Docker 里

```shell
$ ./run-dev
```