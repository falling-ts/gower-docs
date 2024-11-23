---
title: 请求验证器
url: "/single/请求验证器"
---

## 所使用的的开源包

是 Gin 内部默认的验证器, 即 `github.com/go-playground/validator/v10`

## 验证器初始化

验证器默认通过 validator 服务初始化, 主要进行注册中文翻译, 添加中文 zh tag, 对参数键进行中文翻译, 对多错误返回数据进行中文翻译.

## 如何定义

> 我以 api 为例, 进行说明, web 的请求验证器与 api 的一样

### 创建请求验证器

`app/api/requests/test_request.go`
```go
package requests

import "gower/app"

type HelloRequest struct {
    app.Request
    Test string `json:"test" xml:"test" form:"test" query:"test" protobuf:"test" msgpack:"test" yaml:"test" uri:"test" header:"test" toml:"test" binding:"required" zh:"测试"`
}

type IndexRequest struct {
    HelloRequest
    Name *string `form:"name" binding:"required" zh:"名字"`
}

```
> 所有结构体, 最终需要嵌套 `app.Request`, 因为执行验证的方法, 主要通过 `app.Request` 进行.
>
> 为什么要分出 `HelloRequest` 和 `IndexRequest` 呢, 因为有可能多个方法, 所验证的参数有几个是相同的, 这样的情况, 建议声明一个公共结构体, 然后具体方法的验证器嵌套这个公共的结构体即可.

## 如何使用

直接在控制器方法上, 声明需要验证的请求结构体即可.

`app/api/controllers/test_controller`
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

func (t *HelloController) Index(req *requests.IndexRequest) (string, any) {
    // 在验证完 name 和 test 不为空后, 执行本方法
    return "Hello, " + *req.Name, app.Data{
        "key": "value",
    }
}
```

## 命令行工具

### 创建 Web 请求验证器

```shell
$ gower make --request TestAb
或
$ gower make -req TestAb
```

### 创建 Api 请求验证器

```shell
$ gower make --api:request TestAc
或
$ gower make -ir TestAc
```