---
title: util-Utility Service
url: "/en/single/util-utility-service"
---

> Currently, only the functions that are needed are set up. You can extend this service based on your business requirements.

## Used Open Source Libraries

```yaml
"github.com/jaevor/go-nanoid"
```

> go-nanoid: Just an open-source library for the Nanoid method.

## Service Provider

### Service Identifier

```yaml
"util"
```


### Register Service

```go
P.Register("util", func(...services.Service) services.Service {
    return util.New().Init()
})
```


### Registration Format Refer to auth Service

## Dependent Services

No dependent services

## ENV Configuration

No related configuration

## Service Interface Methods

```go
type UtilService interface {
	Service

	Nanoid(args ...int) string // Get a nanoid unique ID, the parameter is of type int, e.g., util.Nanoid(32) gets a 32-byte length unique ID, default is 21 bytes
	Direct(v reflect.Value) reflect.Value // The opposite of reflect.Indirect, i.e., get the pointer variable of the value

	SetEnv(env, key, value string) error // Modify and set the value of .env.xxx
	SecretKey(length int) (string, error) // Get a random secret key

	ExcpKey() string // Get the key for the exception cache value
	BlackTokenKey(nanoid string) string // Get the cache key for the JWT blacklist token

	Ptr(v any) any // Return the pointer of any basic scalar type value

	CreateDir(dir string) string // Recursively create a directory if it does not exist
	IsExist(file string) bool // Check if a file or directory exists
```




## Usage Examples

```go
id := util.Nanoid(21)
v := util.Direct(relValue)

err := util.SetEnv(".env.dev", "APP_NAME", "Test")
key, err := util.SecretKey(32)

excpKey := util.ExcpKey()
tokenKey := util.BlackTokenKey(util.Nanoid(21))

p := util.Ptr("test").(*string)
dir := util.CreateDir(dir)
ok := util.IsExist("/path/file.txt")
```
