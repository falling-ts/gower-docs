---
title: cookie-Cookie服务
url: "/single/cookie-cookie服务"
---

## 使用的开源库

```
"github.com/gin-gonic/gin"
```
> 实际使用了 `*gin.Context` 的 cookie 功能, 简化了调用, 内置了加密和解密.

## 服务提供者

### 服务标识符

```
"cookie"
```

### 注册服务

```go
P.Register("cookie", Depends{"config", "sym-crypt"}, func(ss ...services.Service) services.Service {
    return cookie.New().Init(ss...)
})
```

### 注册服务格式见 auth 服务

## 依赖的服务

```
"config", "sym-crypt"
```

## ENV 配置

```
# 域, 关系到 Cookie 设置
APP_DOMAIN=localhost
```

## 服务接口方法

```go
type CookieService interface {
    Service // 通用服务接口

    Set(c *gin.Context, key, val string, args ...any)  // 加密设置 Cookie
    Get(c *gin.Context, key string) (string, error) // 解密获取 Cookie
}
```



## 使用示例

### 加密设置 Cookie

```go
cookie.Set(c, "key", "value")
```
> c 是 `*gin.Context`

关于其它参数, 会有一个默认值, 看下面代码

```go
s.maxAge = 100000000
s.path = "/"
s.domain = config.Get("app.domain", "localhost").(string)
s.httpOnly = true
s.secure = false
```

> 想修改, 可以在 "value" 参数后面, 添加若干参数即可
>
> 传入不同于 path, domain 的字符串, 第一个修改 path, 第二个修改 domain
>
> 传入 int 类型, 修改 maxAge
>
> 传入 true, 修改 secure 为 true, 传入 false 修改 httpOnly 为 false


### 解密获取 Cookie

```go
v, err := cookie.Get(c, "key")
```