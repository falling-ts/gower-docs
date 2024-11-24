---
title: translate-Translation Service
url: "/en/single/translate-translation-service"
---

### Translation Service Basic Methods

The basic methods of the translation service need to be added according to development requirements, along with the corresponding translation mappings. Currently, only translations for common MySQL errors are provided.

## Open Source Libraries Used

No open source libraries are used at this time.

## Service Provider

### Service Identifier

```yaml
"translate"
```


### Registering the Service

```go
P.Register("translate", func() (Depends, Resolve) {
    return Depends{"config"}, func(ss ...services.Service) services.Service {
        return translate.Mount(trans.All).Init(ss...)
    }
})
```


### Registration Format Refer to the Auth Service

## Dependent Services

```yaml
"config"
```


## ENV Configuration

No related configuration at this time.

## Service Interface Methods

```go
type TranslateService interface {
	Service // General service interface

	DBError(err error) error  // Translate common standard database errors
}
```


## Usage Example

```go
// Register 注册
func (u *User) Register() error {
	result := db.Create(u)
	return trans.DBError(result.Error)
}
```
