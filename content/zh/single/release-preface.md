---
title: 发布前言
url: "/single/发布前言"
---

## 发布分类

```
发布开发版
发布测试版
发布生产版
```

前端所对应的环境文件分别是

```
.env.development
.env.test
.env.production
```

后端所对应的环境文件分别是

```
envs/.env.development
envs/.env.test
envs/.env.production
```

## Rclone

Rclone 是一款 Go 语言实现的文件同步传输工具, 可以把测试版和生产版的代码, 上传到对应服务上.

同时支持检测文件修改上传, 意思是指, 除了第一次上传外, 以后只会上传修改的文件, 极大提高了工作效率.

### 安装使用

直接运行脚本, 进行安装与配置

```shell
./init-rclone
```

运行结果如下:

```
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
```
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

## 具体分类

### 普通发布[推荐]

普通发布, 也叫跨平台编译发布. 意思指程序文件在本地打包, 然后再上传服务器. 这样做服务器是没有源代码的文件, 保证了程序源码的安全性.

普通发布会使用 Docker, 为服务提供运行稳定以及第三方应用服务, 比如: Web 服务器, 数据库, 日志查看器.

普通发布的运行脚本没有任何后缀.

### 全发布

全发布, 也叫服务器编译构建. 意思是指把项目所有源码一起打包, 然后上传服务器, 由服务器运行起 Docker, 然后 对程序进行构建与运行.由于源码存放在服务器, 有一定源码泄漏风险.

全发布后缀都以 `-full` 结尾.

### 简单发布

简单发布, 不使用 Docker 提供稳定运行, 默认使用服务器的 systemd 进行程序运行的崩溃重启.

同时也是本地机器跨平台构建应用, 不会上传源码到服务器上.

由于不使用 Docker , 第三方应用就得自己配置了. 比如: 自己安装数据库和 Web 服务器等.

简单发布都以 `-simple` 结尾.
