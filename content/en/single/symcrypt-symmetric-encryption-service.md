---
title: symcrypt-Symmetric Encryption Service
url: "/en/single/symcrypt-symmetric-encryption-service"
---

### Open Source Libraries Used

```yaml
"crypto/aes"
```


### Service Provider

#### Service Identifier

```yaml
"sym-crypt"
```


#### Register Service

```go
P.Register("sym-crypt", Depends{"config"}, func(ss ...services.Service) services.Service {
    return symcrypt.New().Init(ss...)
})
```


#### Service Registration Format See auth Service

## Dependent Services

```yaml
"config"
```


## ENV Configuration

```yaml
APP_KEY=mXQ60jZG61/bfdW2hI8bWJZNrpN1RZhwRjiM7pY7/Nk=
```


## Service Interface Methods

```go
type SymCryptService interface {
    Service // General service interface

    Encrypt(plain string) (string, error) // Encrypt
    Decrypt(cipher string) (string, error) // Decrypt
}
```


### Usage Example

```go
plaintext := "123456"
encrypt, err := symCrypt.Encrypt(plaintext)
decrypt, err := symCrypt.Decrypt(encrypt)

// plaintext == decrypt
```
