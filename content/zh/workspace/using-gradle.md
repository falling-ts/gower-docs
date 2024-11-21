---
title: Gradle 构建工具使用
url: "/workspace/gradle-构建工具使用"
---

## Gradle 任务配置

- Gradle 项目的构建任务, 是直接写在 `build.gradle` 中, 方便自定义修改
- Gradle 构建时相关属性参数建议写在 `gradle.properties` 文件中

## 关于 ssh 远程文件部署插件的使用

- 使用的插件

```gradle
plugins {
    id "org.hidetake.ssh" version "2.11.2"
}
```

> 如果是工作区模式, 子项目的 `build.gradle` 不能再次引入 `org.hidetake.ssh`

