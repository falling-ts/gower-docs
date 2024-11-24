---
title: passwd-密码哈希服务
url: "/single/passwd-密码哈希服务"
---

## 使用的开源库

```yaml
"github.com/alexedwards/argon2id"
"golang.org/x/crypto/bcrypt"
"golang.org/x/crypto/scrypt"
```

## 服务提供者

### 服务标识符

```yaml
"passwd"
```

### 注册服务

```go
P.Register("passwd", func() (Depends, Resolve) {
    return Depends{"config", "exception"}, func(ss ...services.Service) services.Service {
        return passwd.New().Init(ss...)
    }
})
```

### 注册服务格式见 auth 服务

## 依赖的服务

```yaml
"config", "exception"
```

## ENV 配置

```yaml
# 密码哈希模式: bcrypt, argon2id, scrypt
PASSWD_MODE=argon2id
```

## 服务接口方法

```go
type PasswdService interface {
    Service // 通用服务接口

    Hash(passwd string) (string, error) // 对密码进行 hash 散列计算
    Check(passwd string, hash string) error // 检查明文与 hash 是否一致
}
```



## 使用示例

```go
hash := passwd.Hash(req.Password)
err := passwd.Check(req.Password, user.Password)
```
> `err == nil` 时, 密码一致