---
title: 发布生产
url: "/single/发布生产"
---

发布生产需要将项目文件上传至生产服务器后, 登录服务器执行 docker 构建.

因此服务器环境是有要求的, 需要安装以下软件

> docker >= v20.10
>
> docker compose >= v2.0

## 普通发布

### 运行发布脚本

```shell
$ ./release-prod
```

#### 步骤

- 构建前端
- 执行测试
- 清理 dev 运行的临时文件
- 跨平台构建
- 上传至生产服务器

### 登录服务器, 运行构建脚本

```shell
$ cd /go/bin
$ chmod +x run.sh
$ ./run.sh
```

#### 步骤

- 清理容器
- 启动测试

> 中间上传后, 需要用 ssh 工具登录服务器去执行构建, 之后会找工具集成到本地脚本中, 一键运行测试

## 全发布

```shell
$ ./release-prod-full
```
> 上传源码至服务器

### 登录服务器

```shell
$ cd /go/src
$ chmod +x run-prod-full.sh
$ ./run-prod-full.sh
```

## 简单发布

```shell
$ ./release-prod-simple
```

### 登录服务器

```shell
$ cd /go/bin
$ chmod +x run-simple.sh
$ ./run-simple.sh
```
