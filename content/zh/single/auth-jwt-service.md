---
title: auth-JWT服务
url: "/single/auth-jwt服务"
---

## 使用的开源库

```
"github.com/golang-jwt/jwt/v5"
```

## 服务提供者

### 服务标识符

```
"auth"
```

### 注册服务

`app/providers/auth.go`

```go
P.Register("auth", func() (Depends, Resolve) {
    return Depends{"config", "util", "cache"}, func(ss ...services.Service) services.Service {
        return auth.New().Init(ss...)
    }
})
```
> P 代表 Providers Map

### 注册格式说明:

```go
// Depends 服务依赖
type Depends []string

// Resolve 服务提供者
type Resolve func(...services.Service) services.Service

// 实际注册操作
P.Register("标识符", func() (Depends, Resolve))
```
或者

```go
P.Register("标识符", []string{"依赖项"}, func(ss ...services.Service) services.Service)
```
> `[]string{"依赖项"}` 可以换成 `Depends{"依赖项"}`

没有依赖的时候, 直接

```go
P.Register("标识符", func(...services.Service) services.Service)
```
即可.


## 依赖的服务

```
"config", "util", "cache"
```
> 讲顺序的, 因此顺序不能改变

## ENV 配置

```
# 密钥
JWT_KEY=iX/YdQ7a2PpF1WYf4rsT9tL1kD9ic7o0EcjeERO4nhE=
# Token 更新时限
JWT_UPD=5m
# Token 过期时间
JWT_EXP=10m
# 哈希算法: HS256, HS384, HS512
JWT_METHOD=HS256
```

> 密钥请使用 `gower jwt key` 生成
>
> 更新时限需要小于过期时限, 因为系统内置自动 Token 更新机制, 如果请求时, 过了更新时限, 没过过期时限, 则任何授权请求都会响应一个新 Token, 前端也做了自动更新.
>
> 哈希算法, 选一个就是, 一般 HS256 够用了

## 服务接口方法

```go
package services

type AuthService interface {
    Service // 包含 Init 通用服务方法

    Sign(args ...any) (string, error) // 签署 token
    Check(token string, args ...string) (string, string, error) // 校验token
    Black(token string) error // 拉黑 token
    IsToken(token string) bool // 检测字符串是 JWT 吗
}
```

## 使用示例

### 签署 token

`Sign(args ...any) (string, error)`

```go
token, err := auth.Sign("user_model", user.IDString(), []string{c.RemoteIP()})
```
> `"user_model"` JWT 的 Issuer 发布者, 如果 admin_user 表登录的, 可以用 `"admin_model"`
>
> `user.IDString()`, 登录者的 ID, 也是 JWT 的 Subject 主题
>
> `[]string{c.RemoteIP()}`, JWT 的 Audience 受众, 这里使用的是客户端 IP, 增加劫持难度

### 校验 token

`Check(token string, args ...string) (string, string, error)`

```go
userId, newToken, err := auth.Check(token, c.RemoteIP())
```

### 拉黑 token

`Black(token string) error`

```go
err := auth.Black(token)
```

### 检测字符串是 JWT 吗

`IsToken(token string) bool`

```go
ok := auth.IsToken(token)
```