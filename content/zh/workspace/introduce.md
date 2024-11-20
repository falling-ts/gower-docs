---
title: 介绍
url: "/workspace/介绍"
aliases:
- "/workspace"
---

Go/Gin Gower Workspace 工作目录, 是基于 Go 语言的 Workspace 与 GOPATH 相结合实现的工作区模式.

## Go Workspace 工作区

`go.work` 文件是 Go 1.18 版本引入的一个新特性, 用于管理多个模块（module）的工作区. 它允许你在一个工作区内同时开发和测试多个模块, 而不需要将这些模块作为依赖项添加到每个模块的 `go.mod` 文件中.

### 工作区的概念
- **工作区**: 一个包含多个 Go 模块的目录.
- **`go.work` 文件**: 定义了工作区的配置文件, 指定了哪些模块属于这个工作区.

### `go.work` 文件结构
```go
go 1.18

use (
    ./src/module1
    ./src/module2
)
```

- **`go 1.18`**: 指定 Go 的版本.
- **`use`**: 列出工作区中包含的模块路径.

### 创建 `go.work` 文件
- 使用命令 `go work init` 可以创建一个新的 `go.work` 文件, 并指定初始模块.
  ```sh
  $ go work init
  ```

- 这将生成一个 `go.work` 文件.

### 添加和删除模块
- **添加模块**: 使用 `go work use` 命令将新的模块添加到 `go.work` 文件中.
  ```sh
  $ go work use src/module1
  ```
> 注意: 添加之前, `src/module1` 项目中, 已经存在 go.mod 文件

- **删除模块**: 使用 `go work remove` 命令从 `go.work` 文件中移除模块.
  ```sh
  $ go work remove src/module1
  ```
  
## GOPATH

`GOPATH` 是 Go 语言中的一个重要环境变量, 用于指定 Go 项目的默认工作目录. 它主要用于管理 Go 代码的组织/构建和依赖关系. 以下是关于 `GOPATH` 的详细说明:

### 1. GOPATH 的概念
- **GOPATH**: 是一个环境变量, 指定了 Go 项目的默认工作目录.
- **工作目录**: 在这个目录下, Go 会查找和管理源代码/二进制文件和包对象.

### 2. GOPATH 的结构
`GOPATH` 目录通常包含三个子目录:
- **`src`**: 存放源代码文件.
- **`pkg`**: 存放三方依赖包的目录.
- **`bin`**: 存放编译后的可执行文件.

### 3. 设置 GOPATH

在 GoLand 中, 你可以在 `Settings` -> `Go` -> `GOPATH` 中设置项目级别的 `GOPATH`.

## 联合使用

当 Workspace 和 GOPATH 可以一起使用时, `go mod tidy` 会下载依赖包到 `pkg/mod` 目录下. `go install` 默认会将运行文件安装在 `bin` 目录下.
