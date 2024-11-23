---
title: symcrypt-对称加密服务
url: "/single/symcrypt-对称加密服务"
---

## 使用的开源库

```
"crypto/aes"
```

## 服务提供者

### 服务标识符

```
"sym-crypt"
```

### 注册服务

```go
P.Register("sym-crypt", Depends{"config"}, func(ss ...services.Service) services.Service {
    return symcrypt.New().Init(ss...)
})
```

### 注册服务格式见 auth 服务

## 依赖的服务

```
"config"
```

## ENV 配置

```
APP_KEY=mXQ60jZG61/bfdW2hI8bWJZNrpN1RZhwRjiM7pY7/Nk=
```

## 服务接口方法

```go
type SymCryptService interface {
    Service // 通用服务接口

    Encrypt(plain string) (string, error) // 加密
    Decrypt(cipher string) (string, error) // 解密
}
```



## 使用示例

```go
plaintext := "123456"
encrypt, err := symCrypt.Encrypt(plaintext)
decrypt, err := symCrypt.Decrypt(encrypt)

// plaintext == decrypt
```
