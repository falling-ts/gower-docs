---
title: Configuration
url: "/en/single/configuration"
---

## Configuration Directory

`configs`

### Summary

`configs/config.go`

```go
package configs

//...

// Config Total Configuration
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


> If you need to add your own configuration file, please embed the specific configuration structure into the Config structure.

For example, `configs/app.go`

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


- Create an `app.go` file
- Declare the App structure
- Declare fields, refer to the syntax in [caarlos0-env](https://github.com/caarlos0/env)
- Declare the corresponding field values in all .env files under `envs/`
- Embed the App structure into the Config structure

## How to Use

### Get Service

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


### Use Service

`app/http/controllers/home_controllers`

```go
appName := config.App.Name

Or

appName := config.Get("app.name", "Gower").(string)
```
