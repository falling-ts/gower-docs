---
title: Request Validator
url: "/en/single/request-validator"
---

## Open Source Packages Used

The default validator in Gin, which is `github.com/go-playground/validator/v10`.

## Validator Initialization

The validator is initialized through the validator service by default, mainly registering Chinese translations, adding the Chinese `zh` tag, translating parameter keys into Chinese, and translating multi-error return data into Chinese.

## How to Define

> I will explain using an API as an example; the request validator for web requests is the same as for APIs.

### Creating Request Validators

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

> All structs ultimately need to embed `app.Request`, because the validation method is primarily executed through `app.Request`.
>
> Why separate `HelloRequest` and `IndexRequest`? Because multiple methods might validate several common parameters. In such cases, it is recommended to declare a common struct and then embed this common struct in the specific method validators.

## How to Use

Directly declare the required request struct in the controller method.

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
    // After validating that name and test are not empty, execute this method
    return "Hello, " + *req.Name, app.Data{
        "key": "value",
    }
}
```


## Command Line Tools

### Creating Web Request Validators

```shell
$ gower make --request TestAb
or
$ gower make -req TestAb
```


### Creating API Request Validators

```shell
$ gower make --api:request TestAc
or
$ gower make -ir TestAc
```
