---
title: Exceptions
url: "/en/single/exceptions"
---
## Exception Service

The exception service is provided by `services/exception/exception.go`.

The structure is `exception.Service`.

This structure implements the `services.ExceptionService` interface.

## Exception Content [Exception Carrier]

The main content is defined under the `app/exceptions` package.

Initially, some common exception methods are defined based on the HTTP 400 status code:

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


> The method names are the status text of the status codes.
>
> The essence of the exception content is still an implementation of the `error` interface.

The essential difference in defining these methods is that the `code` set when returning JSON is different. This point is also quite important because the frontend sometimes needs to perform different operations based on different codes.

### Why It's Called a Carrier

When the exception service provider registers the exception service, it needs to bind the exception content to the exception service. The purpose of doing this is to define general complex and rarely modified methods on the service, while simple and frequently modified methods are defined on the carrier. This way, when using the exception service, we only need to modify the code under `app/exceptions`.

Below is the registration code of the provider:

```go
// Create a new carrier
e := new(exceptions.Exception)
// Mount it to the exception service
return exception.Mount(e).Init(ss...)
```


> Key Point: The actual exception service provided by the service provider is `*exceptions.Exception`. This allows us to use the 40x methods mentioned above without modifying the `services.ExceptionService` interface.

## How to Use

### First, let's talk about using the service

- Call the shortcut methods in the `app` package

```go
app.Exception().BadRequest("Request failed", err)
```


- Declare an internal variable in `controllers.go` in advance [Recommended]

```go
var (
    excp = app.Exception()
)
```


- Do not use shortcut methods, assert manually [Not recommended, as the service may be nil]

```go
var (
    excp = app.Get("exception").(*exceptions.Exception)
)
```


### Specific Usage

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
        return "", nil, excp.BadRequest(err) // Return the error directly
    }
    
    return "Hello, " + *req.Name, app.Data{
        "key": "value",
    }, nil
}
```


### About Parameters

The exception methods actually create a new exception structure and then return it. For more details, see `app/exceptions/exception.go`.

```go
// New creates an exception
func (e *Exception) New(code int, args ...any) services.Exception {
    temp := *e
    newE := &temp

    return newE.Set(exception.New()).
        Set(newE).
        Build(args...).
        Set(code)
}
```


The actual parameters are `...any`. Here is another method:

```go
// Set sets the content generically
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


When the `Build` method is executed, it loops through the `Set` method to set the `Code`, `Msg`, `Data`, exception service, or the carrier itself based on the type of the passed parameters.

The benefit of this approach is flexibility. The following calls are all valid:

```go
return excp.BadRequest(err)
return excp.BadRequest("msg")
return excp.BadRequest(err, "msg")
return excp.BadRequest("msg", err)
return excp.BadRequest(err, "msg", 450)
return excp.BadRequest(460, "msg", err)
```

> Note: If there are multiple parameters of the same type, the last one takes effect.

## Modifying the Exception Carrier

### Exception Body Content
`app/exceptions/exception.go`
```go
// Exception is the exception response body
type Exception struct {
    *exception.Service `json:"-"`
    Code               int    `json:"code"`
    Msg                string `json:"msg"`
    Data               any    `json:"data"`
}

// Set sets the content generically
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

> Except for *exception.Service `json:"-"` and the first two cases, you can modify other parameters of `Exception`. When adding, deleting, or modifying these parameters, make sure to update the `Set` function to handle the logic of the structure instance correctly. For example, if you change `Msg` to `Message`, you should also change `e.Msg = arg.(string)` to `e.Message = arg.(string)`
