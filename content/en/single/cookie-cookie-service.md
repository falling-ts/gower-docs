---
title: cookie-Cookie Service
url: "/en/single/cookie-cookie-service"
---

## Used Open Source Libraries

- `"github.com/gin-gonic/gin"`

> Actually used the `*gin.Context` cookie feature, which simplifies calls and includes built-in encryption and decryption.

## Service Provider

### Service Identifier

- `"cookie"`

### Register Service

```go
P.Register("cookie", Depends{"config", "sym-crypt"}, func(ss ...services.Service) services.Service {
    return cookie.New().Init(ss...)
})
```


### Registration Format Refer to Auth Service

## Dependent Services

- `"config", "sym-crypt"`

## ENV Configuration

```yaml
# Domain, related to Cookie settings
APP_DOMAIN=localhost
```


## Service Interface Methods

```go
type CookieService interface {
    Service // General service interface

    Set(c *gin.Context, key, val string, args ...any)  // Encrypt and set Cookie
    Get(c *gin.Context, key string) (string, error) // Decrypt and get Cookie
}
```


## Usage Examples

### Encrypt and Set Cookie

```go
cookie.Set(c, "key", "value")
```

> `c` is of type `*gin.Context`

For other parameters, there are default values, see the code below:

```go
s.maxAge = 100000000
s.path = "/"
s.domain = config.Get("app.domain", "localhost").(string)
s.httpOnly = true
s.secure = false
```


> If you want to modify these values, you can add several parameters after the "value" parameter:
>
> - Strings different from `path` and `domain` will modify `path` first, then `domain`.
> - Integers will modify `maxAge`.
> - `true` will set `secure` to `true`, and `false` will set `httpOnly` to `false`.

### Decrypt and Get Cookie

```go
v, err := cookie.Get(c, "key")
```
