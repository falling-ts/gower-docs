---
title: GoLand 使用
url: "/workspace/goland-使用"
---

## GoLand 需要安装的插件

- 提前在 GoLand 中安装好 Gradle 插件
- 需要安装 JDK 23 及以上, 并指定 Gradle JVM 为 JDK 23
- 推荐安装的 GoLand 插件
  ```shell
  .env files support
  PostCSS
  Stylus
  Tailwind CSS
  Vite
  Vue.js
  ```

## 使用 GoLand 打开 `gower-work` 项目

- 第一次使用 GoLand 打开 `gower-work` 时，会提醒 `找到Gradle 'gower-work' 构建脚本`, 然后点击 `加载 Gradle 项目`, 会初始化 gradle 构建体系
- 找到 `设置/GO/GOPATH/项目GOPATH`, 添加 `gower-work` 所在的绝对目录
  - 这样在项目内执行 `go install` 时, 会自动把二进制安装到 bin 目录下
  - 项目目录下的 bin 也会添加到内置终端的环境变量中, 方便执行命令

> 如果提示 `go list` 找不到 go.mod 的错误, 无视关闭即可

