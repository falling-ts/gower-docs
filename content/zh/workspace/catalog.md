---
title: 目录结构
url: "/workspace/目录结构"
---

```go
- bin/ // 在源码中执行 go install, 将会安装到这个目录下
- gradle/ // Gradle 包装器目录
    - wrapper/ // 包装器 jar 包目录
- pkg/ // 源码依赖三方包的存储目录
*- src/ // 项目源码目录
*- build.gradle // Gradle 总构建脚本
*- go.work // Go 工作空间配置文件
*- gradle.properties // Gradle 配置文件
- gradlew // Gradle 包装器脚本[unix]
- gradlew.bat // Gradle 包装器脚本[windows]
*- settings.gradle // Gradle 子模块配置文件
```

> 尾部携带 `/` 的是指目录
> 
> 前面带 `*` 的是指重点目录或文件