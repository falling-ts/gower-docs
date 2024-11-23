---
title: 配置
url: "/single/配置"
---

## 配置目录

`configs`

### 汇总

`configs/config.go`

```go
package configs

//...

// Config 总配置
type Config struct {
    *config.Service
    App
    Log
    Cache
    DB
    Passwd
    Jwt
    Res
    Cors
    Upload
}

// ...
```

> 如需添加自己的配置文件, 请把具体配置的结构体嵌套进 Config 中

以 `configs/app.go` 为例

```go
package configs

type App struct {
    Name    string `env:"APP_NAME" envDefault:"Gower"`
    Cli     string `env:"APP_CLI" envDefault:"gower"`
    Version string `env:"APP_VERSION" envDefault:"v0.0.1"`
    Key     string `env:"APP_KEY,required"`
    Mode    string `env:"APP_MODE" envDefault:"test"`
    Url     string `env:"APP_URL" envDefault:"http://localhost:8080"`
    Domain  string `env:"APP_DOMAIN" envDefault:"localhost"`
}
```

- 新建 `app.go` 文件
- 声明 App 结构体
- 声明字段, 具体语法见 [caarlos0-env](https://github.com/caarlos0/env)
- 分别在 `envs/` 下的所有 .env 中声明相应字段值
- 将 App 结构体嵌套进 Config 结构体中即可

## 如何使用

### 取服务

`app/http/controllers/controllers`

```go
.
.
.
var (
    config = app.Config()
)
.
.
.
```

### 用服务

`app/http/controllers/home_controllers`

```go
appName := config.App.Name

或者

appName := config.Get("app.name", "Gower").(string)
```
