---
title: 测试
url: "/single/测试"
---

## 写测试

`tests/tests.go`

```go
var (
    auth     = app.Auth()
    cache    = app.Cache()
    config   = app.Config()
    db       = app.DB()
    excp     = app.Exception()
    passwd   = app.Passwd()
    res      = app.Response()
    route    = app.Route()
    symCrypt = app.SymCrypt()
    _        = app.Translate()
    util     = app.Util()
    //TODO: 继续添加需要测试服务
)
```

以 `cache` 为例吧

创建 `tests/cache.go`

```go
package tests

import (
    "fmt"
    "testing"
    "time"
)

func TestCache(t *testing.T) {
    fmt.Println("----------------TestCache 开始----------------")
    
    // 获取断言工具
    assert := getAssert(t)

    // 设置缓存与取缓存
    cache.SetDefault("test-key", "123")
    value, ok := cache.Get("test-key")

    // 断言成功
    assert.True(ok)
    assert.Equal(value, "123")

    // 删除缓存
    cache.Delete("test-key")

    // 删除后, 断言不成功
    _, ok = cache.Get("test-key")
    assert.False(ok)

    // 测试过期时间
    cache.Set("test-key", "123", time.Millisecond)
    time.Sleep(time.Millisecond * 10)

    _, ok = cache.Get("test-key")
    assert.False(ok)

    fmt.Println("----------------TestCache 结束----------------")
}

```

## 写基准测试

### 声明服务

`tests/benchmarks/benchmarks.go`

```go
var (
    auth     = app.Auth()
    cache    = app.Cache()
    config   = app.Config()
    passwd   = app.Passwd()
    route    = app.Route()
    symCrypt = app.SymCrypt()
    res      = app.Response()
    //TODO: 继续声明你需要基准测试的服务
)
```

### 写基准测试

`tests/benchmarks/cache.go`

```go
package benchmarks

import "testing"

func BenchmarkCache(b *testing.B) {
    for i := 0; i < b.N; i++ {
        cache.SetDefault("key", "test")
        _, _ = cache.Get("key")
        cache.Delete("key")
    }
}

```

## 汇总所有测试

在 `main_test.go` 中进行汇总

```go
func Test(t *testing.T) {
    t.Run("TestAuth", tests.TestAuth)
    t.Run("TestCache", tests.TestCache)
    t.Run("TestConfig", tests.TestConfig)
    t.Run("TestDB", tests.TestDB)
    t.Run("TestException", tests.TestException)
    t.Run("TestPasswd", tests.TextPasswd)
    t.Run("TestRoute", tests.TestRoute)
    t.Run("TestSymCrypt", tests.TestSymCrypt)
    t.Run("TestTrans", tests.TestTrans)
    t.Run("TestUtil", tests.TestUtil)
    //TODO: 继续写你的测试
}

func Benchmark(b *testing.B) {
    b.Run("BenchmarkRoute01", benchmarks.BenchmarkRoute01)
    b.Run("BenchmarkRoute02", benchmarks.BenchmarkRoute02)
    b.Run("BenchmarkRoute03", benchmarks.BenchmarkRoute03)
    b.Run("BenchmarkRoute04", benchmarks.BenchmarkRoute04)
    b.Run("BenchmarkRoute05", benchmarks.BenchmarkRoute05)
    b.Run("BenchmarkRoute06", benchmarks.BenchmarkRoute06)
    b.Run("BenchmarkRoute07", benchmarks.BenchmarkRoute07)
    b.Run("BenchmarkRoute08", benchmarks.BenchmarkRoute08)
    b.Run("BenchmarkRoute09", benchmarks.BenchmarkRoute09)
    b.Run("BenchmarkRoute10", benchmarks.BenchmarkRoute10)
    b.Run("BenchmarkAuthSign", benchmarks.BenchmarkAuthSign)
    b.Run("BenchmarkAuthCheck", benchmarks.BenchmarkAuthCheck)
    b.Run("BenchmarkCache", benchmarks.BenchmarkCache)
    b.Run("BenchmarkConfig", benchmarks.BenchmarkConfig)
    b.Run("BenchmarkConfigGet", benchmarks.BenchmarkConfigGet)
    b.Run("BenchmarkPasswd", benchmarks.BenchmarkPasswd)
    b.Run("BenchmarkEncode", benchmarks.BenchmarkEncode)
    b.Run("BenchmarkDecode", benchmarks.BenchmarkDecode)
    //TODO: 继续写你的基准测试
}
```

## 运行测试

### 运行测试

```shell
$ go test
```

### 运行基准测试

```shell
$ go test -bench=Benchmark
```
