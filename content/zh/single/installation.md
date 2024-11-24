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
> 
> gradle == 8.10.2
>
> jdk >= 23

## 工作区模式

[工作区模式-安装](https://falling-ts.github.io/gower-docs/workspace/安装/)

## 单项目模式

### 安装 gower 命令

```shell
$ go install -tags cli github.com/falling-ts/gower@latest
```

### 创建项目

```shell
$ gower create my-project
```

### 运行在 Docker 里

```shell
$ cd my-project
$ ./docker/run-dev
```

### 使用 gradle 运行

- 提前在 GoLand 中安装好 gradle 插件
- 修改 `build.gradle`, 去掉开头插件引用的注释
- 第一次使用 GoLand 打开 my-project 时, 会提醒 `找到Gradle 'my-project' 构建脚本`, 然后点击 `加载 Gradle 项目`, 会初始化 gradle 构建体系
- 最后, 在右侧 gradle 任务中找到 dev 下的 Run, 运行它即可.

### 访问 `http://localhost:8080`

## Git 安装

### 下载项目文件

```shell
$ git clone -b v0.6.0 --single-branch --depth 1 https://github.com/falling-ts/gower.git
或
$ git clone -b v0.6.0 --single-branch --depth 1 https://gitee.com/falling-ts/gower.git
```

### 安装依赖和命令行工具

```shell
$ pnpm install
$ go mod tidy
$ go install -tags cli
```

### 前端环境

在根目录下，复制出 `.env.test` 和 `.env.prod` 两个前端环境文件

### 后端环境

在 `envs/` 目录下，复制出 `.env.test` 和 `.env.prod` 两个后端环境文件

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
$ ./docker/run-dev
```