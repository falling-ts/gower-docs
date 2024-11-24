---
title: cache-Cache Service
url: "/en/single/cache-cache-service"
---

## Used Open Source Libraries

```yaml
"github.com/patrickmn/go-cache"
```


## Service Provider

### Service Identifier

```yaml
"cache"
```


### Register Service

`app/providers/cache`

```go
P.Register("cache", []string{"config"}, func(ss ...services.Service) services.Service {
    return cache.New().Init(ss...)
})
```


### Registration Format See Auth Service


## Dependent Services

```yaml
"config"
```


## ENV Configuration

```yaml
# Default cache expiration time, default 5m, or write as 300s
CACHE_EXPIRE=300s
# Periodic cleanup of expired cache, default 10m, or write as 600s
CACHE_CLEAN=600s
# Whether to enable persistent cache storage, 0 for off, positive integer for how often to save, default 10m
CACHE_INTERVAL=600s
# Cache directory, default storage/caches
CACHE_DIR=storage/caches
# Cache file, default go.cache
CACHE_FILE=go.cache
```


## Service Interface Methods

```go
package services

import "time"

type CacheService interface {
	Service // General service interface

	SetDefault(k string, x any) // Overwrite key-value using default expiration time
	Set(k string, x any, d time.Duration) // Overwrite key-value
	Add(k string, x any, d time.Duration) error // Non-overwriting set key-value, error if key exists
	Replace(k string, x any, d time.Duration) error // Replace key-value, error if key does not exist
	Increment(k string, n int64) error // Increment key value
	Decrement(k string, n int64) error // Decrement key value

	Get(k string) (any, bool) // Get value
	GetWithExpiration(k string) (any, time.Time, bool) // Get value and expiration time
	Flash(k string) (any, bool) // Flash get value, i.e., get value and delete key-value

	Delete(k string) // Delete key-value
	Flush() // Delete all key-values

	SaveFile(filename string) error // Persistently save
	LoadFile(filename string) error // Load persistent file

	ItemCount() int // Number of key-values
}
```


## Usage Example

```go
// Set key-value
cache.SetDefault("test-key", "123")
// Delete
cache.Delete("test-key")
// Custom expiration time
cache.Set("test-key", "123", time.Millisecond)
time.Sleep(time.Millisecond * 10)
// Definitely cannot get it
_, ok = cache.Get("test-key")
```
