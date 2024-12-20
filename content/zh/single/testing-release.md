---
title: 发布测试
url: "/single/发布测试"
---

## 使用 Gradle 实现简单发布[推荐使用]

找到子项目中的 `gower-work/${your-project}/Tasks/test/ReleaseTest`, 双击运行


## 普通发布

发布测试需要将项目文件上传至测试服务器后, 登录服务器执行 docker 构建.

因此服务器环境是有要求的, 需要安装以下软件

> docker >= v20.10
>
> docker compose >= v2.0

### 运行发布脚本

```shell
$ ./docker/release-test
```

#### 步骤

- 构建前端
- 执行测试
- 清理 dev 运行的临时文件
- 跨平台构建
- 上传至测试服务器

### 登录服务器, 运行构建脚本

```shell
$ cd /go/bin
$ chmod +x docker/run.sh
$ ./docker/run.sh
```

#### 步骤

- 清理容器
- 启动测试

> 中间上传后, 需要用 ssh 工具登录服务器去执行构建, 之后会找工具集成到本地脚本中, 一键运行测试

## 全发布

```shell
$ ./docker/release-test-full
```
> 上传源码至服务器

### 登录服务器

```shell
$ cd /go/src
$ chmod +x docker/run-test-full.sh
$ ./docker/run-test-full.sh
```

## 简单发布

```shell
$ ./cmd/release-test
```

### 登录服务器

```shell
$ cd /go/bin
$ chmod +x cmd/run.sh
$ .cmd/run.sh
```

