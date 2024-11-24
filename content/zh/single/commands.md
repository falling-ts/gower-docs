---
title: 命令
url: "/single/命令"
---

## 使用的开源库

```yaml
github.com/urfave/cli/v2
```

## 命令源码位置

`app/consoles`

## 使用

```shell
$ gower
```

输出以下内容

```yaml
NAME:
   gower - 命令行工具.

USAGE:
   辅助开发的命令工具, 在项目根目录下使用 go install 安装.

VERSION:
   v0.2.1

AUTHOR:
   Falling TS <zgh.yuanshang@gmail.com>

COMMANDS:
   create, c  创建项目
   init, i    初始化应用内容
   jwt, j     操作 JWT 各项参数
   make, m    初始化创建文件
   run, r     启动应用
   help, h    Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --help, -h     show help
   --version, -v  输出软件版本. (default: false)

COPYRIGHT:
   (c) 2023 falling ts
```

## 本质

本质就是构建的系统应用包, 命令就是本体

如上所示, 通过 `gower run` 即可运行 http 服务

也就是说 http 服务只是其中一个命令而已.

## 打包命令模式

命令模式就是简化模式, 因为如果全部打包, 在运行 gower 命令的时候, 会在目录下生成一些日志临时文件, 这并不是我们想见到的.

所以命令模式移除了 route, logger, db 等服务.

```shell
$ go install -tags cli
或
$ go build -tags cli
```

## 详细查看子命令的帮助

例如 run 子命令

```shell
$ gower run help
```

输出:

```yaml
NAME:
   gower run - 启动应用

USAGE:
   gower run [command options] [arguments...]

OPTIONS:
   --port value, -p value  启动应用, 并监听端口. (default: "8080")
   --help, -h              show help
```

> 可以看出命令支持指定端口, 例如绑定在 8000 端口上

```shell
$ gower run --port 8000
```
