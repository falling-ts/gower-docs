---
title: VSCode 使用
url: "/workspace/vscode-使用"
---

## VSCode 需要安装的插件

- Go 插件
- Gradle for Java 插件
- 需要安装 JDK 23 及以上, 并指定 Gradle JVM 为 JDK 23
  - 配置方法: 打开设置, 搜索 `gradle.java.home`
  - 找到 `Java > Import > Gradle > Java:Home`
  - 如果你下载 JDK 在 `C:\Program Files\Java\jdk-23`, 则设置值为 `C:\Program Files\Java\jdk-23`
  
## 使用 VSCode 打开 `gower-work`

- 打开项目后, 如果提示安装推荐插件, 建议进行安装
- 等待 Gradle 初始化完成

## 绑定 `*.tmpl` 的 html 语法高亮

- 打开 VSCode 设置
- 搜索 `files.associations`
- 添加 `*.tmpl`, `html`

## 注意事项

使用 VSCode 进行项目开发时, 需要修改 `envs/.env.dev` 的 `DB_DRIVER` 为 `mysql`, 否则动态 `yesicon` 无法正常加载, 会影响后台菜单页面左侧图标  