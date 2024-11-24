---
title: passwd-Password Hashing Service
url: "/en/single/passwd-password-hashing-service"
---

## Used Open Source Libraries

```yaml
"github.com/alexedwards/argon2id"
"golang.org/x/crypto/bcrypt"
"golang.org/x/crypto/scrypt"
```


## Service Provider

### Service Identifier

```yaml
"passwd"
```


### Register Service

```go
P.Register("passwd", func() (Depends, Resolve) {
    return Depends{"config", "exception"}, func(ss ...services.Service) services.Service {
        return passwd.New().Init(ss...)
    }
})
```


### Registration Format Refer to auth Service

## Dependent Services

```yaml
"config", "exception"
```


## ENV Configuration

```yaml
# Password hashing mode: bcrypt, argon2id, scrypt
PASSWD_MODE=argon2id
```


## Service Interface Methods

```go
type PasswdService interface {
    Service // General service interface

    Hash(passwd string) (string, error) // Perform hash calculation on the password
    Check(passwd string, hash string) error // Verify if the plaintext matches the hash
}
```


## Usage Example

```go
hash := passwd.Hash(req.Password)
err := passwd.Check(req.Password, user.Password)
```

> When `err == nil`, the passwords match.