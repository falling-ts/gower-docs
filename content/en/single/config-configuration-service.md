---
title: config-Configuration Service
url: "/en/single/config-configuration-service"
---

## Used Open Source Libraries

```yaml
"github.com/joho/godotenv"
"github.com/caarlos0/env/v7"
```


## Service Provider

### Service Identifier

```yaml
"config"
```


### Register Service

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


### Registration Format Refer to Auth Service

## Dependent Services

None

## ENV Configuration

```yaml
# Backend environment

APP_NAME=Gower
APP_CLI=gower
APP_VERSION=v0.2.1
APP_KEY=mXQ60jZG61/bfdW2hI8bWJZNrpN1RZhwRjiM7pY7/Nk=
# Three modes: development, production, test
APP_MODE=development
APP_URL=https://localhost
# Domain, related to Cookie settings
APP_DOMAIN=localhost
```

> Actually, the entire `.env` file is provided by `config`, otherwise there wouldn't be so many services depending on `config`.

## Service Interface Methods

```go
// Config configuration content interface
type Config interface {
    Service // General service interface
    
    Set(arg any) Config // Set configuration service
    Get(fieldStr string, args ...any) any  // Get configuration item
}
```

> The actual service provider for the configuration service is the configuration content [carrier].

## Usage Examples

### Using Get Method

Slower, directly get if possible.

```go
appName := config.Get("app.name", "Gower").(string)
```


### Directly Get

Extremely fast, a few tenths of a nanosecond.

```go
appName := config.App.Name
```


### Benchmark Comparison

```yaml
Benchmark/BenchmarkConfig-12            1000000000               0.3776 ns/op
Benchmark/BenchmarkConfigGet-12          3089138               388.0 ns/op
```
