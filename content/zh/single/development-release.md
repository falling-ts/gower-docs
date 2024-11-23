---
title: 发布开发
url: "/single/发布开发"
---

发布开发仅仅在本地构建运行.

## 普通发布

```shell
$ ./run-dev
```
> windows 使用 run-dev.cmd
>
> linux 使用 run-dev.sh

`cmd 内容`

```shell
@echo off

echo ---------------- build static... ----------------
call npm run dev

echo ---------------- go test... ----------------
go test -tags tmpl,static
REM go test -bench=Benchmark -tags tmpl,static

echo ---------------- go build ----------------
SET CGO_ENABLED=0
SET GOOS=linux
SET GOARCH=amd64

go build -o gower -tags tmpl,static

echo ---------------- clean docker... ----------------
docker compose down

echo ---------------- start dev ----------------
docker compose up -d --build gower

echo ---------------- tail -f dev log ----------------
docker logs -f gower

```

### 步骤

- 构建前端
- 执行测试
- 跨平台打包
- 停止运行的 gower
- 重新构建运行 gower
- 打印运行日志

### 使用的文件包括

```
docker-compose.yaml
Dockerfile
```

## 全发布

```shell
$ ./run-dev-full
```

### 步骤

- 构建前端
- 停止运行的 dev-full
- 重新构建运行 dev-full
- 打印运行日志

> 打包和测试任务, 放在了构建与运行 dev-full 上了.

### 使用的文件包括

```
docker-compose.yaml
Docker-development-full
entrypoint-dev-full.sh
```

> entrypoint-dev-full.sh 是 docker 运行的入口文件
