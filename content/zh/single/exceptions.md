---
title: 异常
url: "/single/异常"
---

## 异常服务

异常服务由 `services/exception/exception.go` 提供.

结构体是 `exception.Service`.

该结构体实现了 `services.ExceptionService` 接口.

## 异常内容[异常挂载体]

主要内容是 `app/exceptions` 包下的定义.

初始根据 Http 400 状态码定义一些常用的异常方法

```go
BadRequest(args ...any) services.Exception // 400
Unauthorized(args ...any) services.Exception // 401
Forbidden(args ...any) services.Exception // 403
NotFound(args ...any) services.Exception // 404
MethodNotAllowed(args ...any) services.Exception // 405
NotAcceptable(args ...any) services.Exception // 406
RequestTimeout(args ...any) services.Exception // 408
RequestEntityTooLarge(args ...any) services.Exception // 413
RequestURITooLong(args ...any) services.Exception // 414
UnsupportedMediaType(args ...any) services.Exception // 415
UnprocessableEntity(args ...any) services.Exception // 422
TooManyRequests(args ...any) services.Exception // 429
```

> 方法名就是状态码的状态文本.
>
> 异常内容本质还是 error 接口的实现

定义这些方法, 本质区别就是返回 json 时, 设置的 code 不一样而已. 这个点也挺重要, 因为前端有时候要根据不同的 code 执行不同的操作.

### 为什么叫挂载体

因为异常服务提供者在注册异常服务的时候, 需要将异常内容绑定到异常服务上, 这样做的目的是把通用复杂以及不怎么修改的方法定义在服务上, 简单常用的方法以及经常修改的方法定义在挂载体上, 这样我们在使用异常服务的时候, 只需要更改 `app/exceptions` 下的代码就可以了.

下面是提供者的注册代码:

```go
// new一个挂载体
e := new(exceptions.Exception)
// 挂载到异常服务上
return exception.Mount(e).Init(ss...)
```

> 重点: 服务提供者实际提供的异常服务是 `*exceptions.Exception`, 这样做无需修改 `services.ExceptionService` 接口, 即可使用上面说明的那些 40x 的方法

## 如何使用

### 先说一下使用服务这块

- 调用 app 包的快捷方法

```go
app.Exception().BadRequest("请求失败", err)
```

- 在 `controllers.go` 提前声明一个内部变量[推荐]

```go
var (
    excp = app.Exception()
)
```

- 不使用快捷方法, 我要自己断言[不建议, 因为服务可能为空]

```go
var (
    excp = app.Get("exception").(*exceptions.Exception)
)
```
### 具体使用
```go
package controllers

import (
    "gower/app"
    "gower/app/api/requests"
)

type HelloController struct {
    app.Controller
}

var Hello = new(HelloController)

func (t *HelloController) Index(req *requests.IndexRequest, user *models.User) (string, any, error) {
    user.Name = req.Name
    err := db.Create(user)
    if err != nil {
        return "", nil, excp.BadRequest(err) // 直接返回错误
    }
    
    return "Hello, " + *req.Name, app.Data{
        "key": "value",
    }, nil
}

```

### 关于参数

异常方法实际 new 了一个异常结构体, 然后返回的, 具体看 `app/exceptions/exception.go`

```go
// New 创建异常
func (e *Exception) New(code int, args ...any) services.Exception {
    temp := *e
    newE := &temp

    return newE.Set(exception.New()).
        Set(newE).
        Build(args...).
        Set(code)
}
```

实际参数就是 `...any`, 在看一个方法:

```go
// Set 通用设置内容
func (e *Exception) Set(arg any) services.Exception {
    switch arg.(type) {
    case *exception.Service:
        e.Service = arg.(*exception.Service)
    case *Exception:
        e.Service.Exception = e
    case int:
        e.Code = arg.(int)
    case string:
        e.Msg = arg.(string)
    default:
        e.Data = arg
    }

    return e
}
```

在执行上一个函数的 Build 方法是, for 循环调用了 Set 根据传进来的参数类型来设置 Code, Msg, Data, 异常服务或者是挂载体本身.

这样做的好处就是自由, 下面这些调用都可以

```go
return excp.BadRequest(err)
return excp.BadRequest("msg")
return excp.BadRequest(err, "msg")
return excp.BadRequest("msg", err)
return excp.BadRequest(err, "msg", 450)
return excp.BadRequest(460, "msg", err)
```
> 注: 如果是多个同类型的参数, 最后面的生效.

## 修改异常挂载体

### 异常体内容
`app/exceptions/exception.go`
```go
// Exception 异常响应体
type Exception struct {
    *exception.Service `json:"-"`
    Code               int    `json:"code"`
    Msg                string `json:"msg"`
    Data               any    `json:"data"`
}

// Set 通用设置内容
func (e *Exception) Set(arg any) services.Exception {
    switch arg.(type) {
    case *exception.Service:
        e.Service = arg.(*exception.Service)
    case *Exception:
        e.Service.Exception = e
    case int:
        e.Code = arg.(int)
    case string:
        e.Msg = arg.(string)
    default:
        e.Data = arg
    }

    return e
}
```
> 除了 *exception.Service `json:"-"` 和前面两个 case 不能修改以外, 你可以调整 Exception 其他参数. 注意增删改这些参数的同时, 要修改 Set 函数, 实现正确处理结构体实例的逻辑. 例如 Msg 改为 Message, 同时 `e.Msg = arg.(string)` 改为 `e.Message = arg.(string)`
