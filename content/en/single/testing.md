---
title: Testing
url: "/en/single/testing"
---

### Writing Tests

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
    // TODO: Continue adding services to be tested
)
```


Let's take `cache` as an example.

Create `tests/cache.go`

```go
package tests

import (
    "fmt"
    "testing"
    "time"
)

func TestCache(t *testing.T) {
    fmt.Println("----------------TestCache Start----------------")
    
    // Get assertion tool
    assert := getAssert(t)

    // Set and get cache
    cache.SetDefault("test-key", "123")
    value, ok := cache.Get("test-key")

    // Assertion success
    assert.True(ok)
    assert.Equal(value, "123")

    // Delete cache
    cache.Delete("test-key")

    // After deletion, assertion fails
    _, ok = cache.Get("test-key")
    assert.False(ok)

    // Test expiration time
    cache.Set("test-key", "123", time.Millisecond)
    time.Sleep(time.Millisecond * 10)

    _, ok = cache.Get("test-key")
    assert.False(ok)

    fmt.Println("----------------TestCache End----------------")
}
```


### Writing Benchmark Tests

#### Declare Services

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
    // TODO: Continue declaring services you need to benchmark
)
```


#### Write Benchmark Tests

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


### Aggregate All Tests

In `main_test.go`, aggregate all tests

```go
func Test(t *testing.T) {
    t.Run("TestAuth", tests.TestAuth)
    t.Run("TestCache", tests.TestCache)
    t.Run("TestConfig", tests.TestConfig)
    t.Run("TestDB", tests.TestDB)
    t.Run("TestException", tests.TestException)
    t.Run("TestPasswd", tests.TestPasswd)
    t.Run("TestRoute", tests.TestRoute)
    t.Run("TestSymCrypt", tests.TestSymCrypt)
    t.Run("TestTrans", tests.TestTrans)
    t.Run("TestUtil", tests.TestUtil)
    // TODO: Continue writing your tests
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
    // TODO: Continue writing your benchmark tests
}
```


### Running Tests

#### Run Tests

```shell
$ go test
```


#### Run Benchmark Tests

```shell
$ go test -bench=Benchmark
```
