---
title: Responses
url: "/en/single/responses"
---

## Response Service

The response service is provided by `services/response/response.go`.

The structure is `response.Service`.

This structure implements the `services.ResponseService` interface.

## Response Content [Response Carrier]

The main content is defined in the `app/responses` package.

Initially, some commonly used exception methods are defined based on the HTTP 400 status code.

```go
Ok(args ...any) services.Response // 200
Created(args ...any) services.Response // 201
Accepted(args ...any) services.Response // 202
NonAuthoritative(args ...any) services.Response // 203
NoContent(args ...any) services.Response // 204
ResetContent(args ...any) services.Response // 205
```


> The method names are the status text of the status codes.
>
> These status codes only affect the `httpStatus`, while the `json code` is always 0.

The essence of defining these methods is that when returning the response body, the `httpStatus` is set differently. The `json code` is always 0.

### Why is it called a Carrier

When registering the response service, the response provider needs to bind the response content to the response service. The purpose of this is to define common and complex methods that are rarely modified on the service, and simple and frequently modified methods on the carrier. This way, when using the response service, we only need to modify the code under `app/responses`.

Here is the registration code for the provider:

```go
r := new(responses.Response)
return response.Mount(r).Init(ss...)
```


> Key point: The actual response service provided by the service provider is `*responses.Response`. This allows the use of the 20x methods mentioned above without modifying the `services.ResponseService` interface.

## How to Use

### Specific Usage

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

// Image Upload an image
func (*UploadController) Image(c *gin.Context) (services.Response, error) {
    path, url, err := upload.Image(c)
    if err != nil {
        return nil, excp.BadRequest(err)
    }

    // Directly call the response body method, return services.Response
    return res.Created("Upload successful", app.Data{
        "path": path,
        "url":  url,
    }), nil
}
```


### About Parameters

The same as the exception handling.

## Modifying the Response Carrier

### Response Content

`app/responses/response.go`

```go
// Response Non-HTML request success response body
type Response struct {
    *response.Service `json:"-"`
    Code              int    `json:"code"`
    Msg               string `json:"msg"`
    Data              any    `json:"data"`
    Token             string `json:"token"`
}

// Set Set the response body content, do not set Code, the default is 0, indicating success
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

> Modification rules are the same as exceptions.
