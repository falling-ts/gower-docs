---
title: auth-JWT Service
url: "/en/single/auth-jwt-service"
---

## Used Open Source Libraries

```yaml
"github.com/golang-jwt/jwt/v5"
```


## Service Provider

### Service Identifier

```yaml
"auth"
```


### Register Service

`app/providers/auth.go`

```go
P.Register("auth", func() (Depends, Resolve) {
    return Depends{"config", "util", "cache"}, func(ss ...services.Service) services.Service {
        return auth.New().Init(ss...)
    }
})
```

> P represents the Providers Map

### Registration Format Explanation:

```go
// Depends service dependencies
type Depends []string

// Resolve service provider
type Resolve func(...services.Service) services.Service

// Actual registration operation
P.Register("identifier", func() (Depends, Resolve))
```

Or

```go
P.Register("identifier", []string{"dependencies"}, func(ss ...services.Service) services.Service)
```

> `[]string{"dependencies"}` can be replaced with `Depends{"dependencies"}`

When there are no dependencies, simply use

```go
P.Register("identifier", func(...services.Service) services.Service)
```


## Dependent Services

```yaml
"config", "util", "cache"
```

> The order matters, so it cannot be changed.

## ENV Configuration

```yaml
# Secret Key
JWT_KEY=iX/YdQ7a2PpF1WYf4rsT9tL1kD9ic7o0EcjeERO4nhE=
# Token Update Interval
JWT_UPD=5m
# Token Expiration Time
JWT_EXP=10m
# Hash Algorithm: HS256, HS384, HS512
JWT_METHOD=HS256
```


> The secret key should be generated using `gower jwt key`.
>
> The update interval must be less than the expiration time because the system has an automatic token update mechanism. If a request is made after the update interval but before the expiration time, any authorized request will respond with a new token, and the front end also handles automatic updates.
>
> For the hash algorithm, choose one; generally, HS256 is sufficient.

## Service Interface Methods

```go
package services

type AuthService interface {
    Service // Includes the generic service method Init

    Sign(args ...any) (string, error) // Sign token
    Check(token string, args ...string) (string, string, error) // Verify token
    Black(token string) error // Blacklist token
    IsToken(token string) bool // Check if the string is a JWT
}
```


## Usage Examples

### Sign Token

`Sign(args ...any) (string, error)`

```go
token, err := auth.Sign("user_model", user.IDString(), []string{c.RemoteIP()})
```

> `"user_model"` is the Issuer of the JWT. If logging in from the `admin_user` table, you can use `"admin_model"`.
>
> `user.IDString()`, the ID of the logged-in user, which is the Subject of the JWT.
>
> `[]string{c.RemoteIP()}`, the Audience of the JWT, here it uses the client's IP to increase the difficulty of interception.

### Verify Token

`Check(token string, args ...string) (string, string, error)`

```go
userId, newToken, err := auth.Check(token, c.RemoteIP())
```


### Blacklist Token

`Black(token string) error`

```go
err := auth.Black(token)
```


### Check if String is JWT

`IsToken(token string) bool`

```go
ok := auth.IsToken(token)
```
