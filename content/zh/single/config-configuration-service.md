---
title: config-配置服务
url: "/single/config-配置服务"
---

## 使用的开源库

```
"github.com/joho/godotenv"
"github.com/caarlos0/env/v7"
```

## 服务提供者

### 服务标识符

```
"config"
```

### 注册服务

`app/providers/config`

```go
P.Register("config", func(...services.Service) services.Service {
    c := new(configs.Config)
    if err := env.Parse(c); err != nil {
        panic(err)
    }

    return config.Mount(c).Init()
})
```

### 注册服务格式见 auth 服务

## 依赖的服务

无

## ENV 配置

```
# 后端环境

APP_NAME=Gower
APP_CLI=gower
APP_VERSION=v0.2.1
APP_KEY=mXQ60jZG61/bfdW2hI8bWJZNrpN1RZhwRjiM7pY7/Nk=
# 三种模式: development, production, test
APP_MODE=development
APP_URL=https://localhost
# 域, 关系到 Cookie 设置
APP_DOMAIN=localhost
```
> 其实整个 `.env` 文件, 都是有 `config` 提供的, 否则也不会有这么多服务需要依赖 `config` 了.

## 服务接口方法

```go
// Config 配置内容接口
type Config interface {
    Service // 通用服务接口
    
    Set(arg any) Config // 设置配置服务
    Get(fieldStr string, args ...any) any  // 获取配置项
}
```
> 配置服务的实际服务者是配置内容[挂载体].

## 使用示例

### 使用 Get 方法

速度较慢, 能直接获取就直接获取.

```go
appName := config.Get("app.name", "Gower").(string)
```

### 直接获取

速度极快, 零点几纳秒

```go
appName := config.App.Name
```

### 基准测试对比

```
Benchmark/BenchmarkConfig-12            1000000000               0.3776 ns/op
Benchmark/BenchmarkConfigGet-12          3089138               388.0 ns/op
```