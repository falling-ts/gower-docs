---
title: 响应
url: "/single/响应"
---

## 响应服务

响应服务由 `services/response/response.go` 提供.

结构体是 `response.Service`.

该结构体实现了 `services.ResponseService` 接口.

## 响应内容[响应挂载体]

主要内容是 `app/responses` 包下的定义.

初始根据 Http 400 状态码定义一些常用的异常方法

```go
Ok(args ...any) services.Response // 200
Created(args ...any) services.Response // 201
Accepted(args ...any) services.Response // 202
NonAuthoritative(args ...any) services.Response // 203
NoContent(args ...any) services.Response // 204
ResetContent(args ...any) services.Response // 205
```

> 方法名就是状态码的状态文本.
>
> 响应体这些状态码只作用在 httpStatus 上, json code 永远是 0

定义这些方法, 本质区别就是返回响应体时, 设置的 httpStatus 不一样而已. json code 永远是 0

### 为什么叫挂载体

因为响应服务提供者在注册响应服务的时候, 需要将响应内容绑定到响应服务上, 这样做的目的是把通用复杂以及不怎么修改的方法定义在服务上, 简单常用的方法以及经常修改的方法定义在挂载体上, 这样我们在使用响应服务的时候, 只需要更改 `app/responses` 下的代码就可以了.

下面是提供者的注册代码:

```go
r := new(responses.Response)
return response.Mount(r).Init(ss...)
```

> 重点: 服务提供者实际提供的响应服务是 `*responses.Respoonse`, 这样做无需修改 `services.ResponseService` 接口, 即可使用上面说明的那些 20x 的方法

## 如何使用

### 具体使用

```go
package controllers

import (
    "gower/app"
    "gower/services"
    "github.com/gin-gonic/gin"
)

type UploadController struct {
    app.Controller
}

var Upload = new(UploadController)

// Image 上传图片
func (*UploadController) Image(c *gin.Context) (services.Response, error) {
    path, url, err := upload.Image(c)
    if err != nil {
        return nil, excp.BadRequest(err)
    }

    // 直接调用响应体的方法, 返回 services.Response 即可
    return res.Created("上传成功", app.Data{
        "path": path,
        "url":  url,
    }), nil
}
```

### 关于参数

与异常的说法一样的

## 修改响应挂载体

### 响应内容

`app/responses/response.go`

```go
// Response 非 HTML 请求的成功响应体
type Response struct {
    *response.Service `json:"-"`
    Code              int    `json:"code"`
    Msg               string `json:"msg"`
    Data              any    `json:"data"`
    Token             string `json:"token"`
}

// Set 设置响应体内容, 不设置 Code, 默认就是 0, 表示成功
func (r *Response) Set(arg any) services.Response {
    switch arg.(type) {
    case *response.Service:
        r.Service = arg.(*response.Service)
    case *Response:
        r.Service.Response = r
    case string:
        s := arg.(string)
        if r.IsToken(s) {
            r.Token = s
            break
        }
        r.Msg = s
    default:
        r.Data = arg
    }
    return r
}
```

> 修改规则同异常
