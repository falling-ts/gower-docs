---
title: cache-缓存服务
url: "/single/cache-缓存服务"
---

## 使用的开源库

```yaml
"github.com/patrickmn/go-cache"
```

## 服务提供者

### 服务标识符

```yaml
"cache"
```

### 注册服务

`app/providers/cache`

```go
P.Register("cache", []string{"config"}, func(ss ...services.Service) services.Service {
    return cache.New().Init(ss...)
})
```

### 注册服务格式见 auth 服务


## 依赖的服务

```yaml
"config"
```

## ENV 配置

```yaml
# 默认缓存生存时间, 默认 5m, 或者写成 300s
CACHE_EXPIRE=300s
# 定期清理过期缓存的时间, 默认 10m, 或者写成 600s
CACHE_CLEAN=600s
# 是否开启缓存长久化存储, 0 为关闭, 正整数为多长时间保存一次, 默认 10m
CACHE_INTERVAL=600s
# 缓存目录, 默认 storage/caches
CACHE_DIR=storage/caches
# 缓存文件, 默认 go.cache
CACHE_FILE=go.cache
```

## 服务接口方法

```go
package services

import "time"

type CacheService interface {
	Service // 通用服务接口

	SetDefault(k string, x any) // 覆盖设置键值, 使用默认过期时间
	Set(k string, x any, d time.Duration) // 覆盖设置键值
	Add(k string, x any, d time.Duration) error // 不覆盖设置键值, 有键报错
	Replace(k string, x any, d time.Duration) error // 替换设置键值, 无键报错
	Increment(k string, n int64) error // 自增键的值
	Decrement(k string, n int64) error // 自减键的值

	Get(k string) (any, bool) // 获取值
	GetWithExpiration(k string) (any, time.Time, bool) // 获取值和过期时间
	Flash(k string) (any, bool) // 闪存取值, 即获取值, 并删除键值

	Delete(k string) // 删除键值
	Flush() // 删除所有键值

	SaveFile(filename string) error // 长久化保存
	LoadFile(filename string) error // 加载长久化文件

	ItemCount() int // 键值数量
}
```

## 使用示例

```go
// 设置键值
cache.SetDefault("test-key", "123")
// 删除
cache.Delete("test-key")
// 自定义过期时间
cache.Set("test-key", "123", time.Millisecond)
time.Sleep(time.Millisecond * 10)
// 一定获取不到
_, ok = cache.Get("test-key")
```