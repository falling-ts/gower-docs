---
title: 发布前言
url: "/single/发布前言"
---

## 发布分类

```yaml
发布开发版
发布测试版
发布生产版
```

前端所对应的环境文件分别是

```yaml
.env.dev
.env.test
.env.prod
```

后端所对应的环境文件分别是

```yaml
envs/.env.dev
envs/.env.test
envs/.env.prod
```

## 使用 Gradle 发布

找到子项目的 `gradle.properties`, 配置三个环境的编译环境, 服务器的 root 连接信息

```env
; 跳过 go 测试, 取消注释, 不执行 go test
; skipTest = 1

; 构建程序名称, gower create my-project 后, 会自动改为 bin = my-project
bin = gower

; 开发环境
dev.npmBuildEnv = dev
; 开发 go 测试标签
dev.goTestTags = tmpl,static
; 开发 go 构建标签
dev.goBuildTags = tmpl,static
; 开发环境禁用 cgo
dev.cgoEnabled = 0
; 开发环境系统
dev.goos = windows
; 开发环境架构
dev.goarch = amd64

; 测试环境
test.npmBuildEnv = test
; 测试 go 测试标签
test.goTestTags = test,tmpl,static
; 测试 go 构建标签
test.goBuildTags = test,tmpl,static
; 测试环境禁用 cgo
test.cgoEnabled = 0
; 测试环境系统
test.goos = linux
; 测试环境架构
test.goarch = amd64
; 测试环境 ssh 主机
test.host = 192.168.101.101
; 测试环境 ssh 端口
test.port = 22
; 测试环境 ssh 用户
test.user = root
; 测试环境 ssh 密码
test.password = root

; 生产环境
prod.npmBuildEnv = prod
; 生产 go 测试标签
prod.goTestTags = prod,tmpl,static
; 生产 go 构建标签
prod.goBuildTags = prod,tmpl,static
; 生产环境禁用 cgo
prod.cgoEnabled = 0
; 生产环境系统
prod.goos = linux
; 生产环境架构
prod.goarch = amd64
; 生产环境 ssh 主机
prod.host = 192.168.101.101
; 生产环境 ssh 端口
prod.port = 22
; 生产环境 ssh 用户
prod.user = root
; 生产环境 ssh 密码
prod.password = root

```

## 使用命令行 Rclone 发布

Rclone 是一款 Go 语言实现的文件同步传输工具, 可以把测试版和生产版的代码, 上传到对应服务上.

同时支持检测文件修改上传, 意思是指, 除了第一次上传外, 以后只会上传修改的文件, 极大提高了工作效率.

### 安装使用

直接运行脚本, 进行安装与配置

```shell
./cmd/init-rclone
```

运行结果如下:

```yaml
---------------- rclone installing... ----------------
go: downloading github.com/rclone/rclone v1.62.2
go: downloading github.com/buengese/sgzip v0.1.1
go: downloading github.com/gabriel-vasile/mimetype v1.4.2
go: downloading github.com/Max-Sum/base32768
.
.
.
---------------- rclone installed ----------------
rclone v1.62.2-DEV
- os/version: Microsoft Windows 11 Pro 21H2 (64 bit)
- os/kernel: 10.0.22000.1696 Build 22000.1696.1696 (x86_64)
- os/type: windows
- os/arch: amd64
- go/version: go1.20
- go/linking: static
- go/tags: none
---------------- please create test and prod ssh server ----------------
No remotes found, make a new one?
n) New remote
s) Set configuration password
q) Quit config
n/s/q> 
```

### 配置 test 和 prod 两个 remote

#### 输入 `n`
#### 输入名称 test
#### 选择 39 SSH 服务器类型
#### 输入服务器 IP
#### 输入登录账户 root
#### 输入 SSH 端口 22
#### 输入密码或选择 xxx.pem
#### 一路回车, 创建 test remote
#### 然后再输入 `n` 创建 prod remote

#### 结果如下:
```yaml
Current remotes:

Name                 Type
====                 ====
prod                 sftp
test                 sftp

e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q>
```

