---
title: Controllers
url: "/en/single/controllers"
---

## Free Controllers

Free controllers, as the name suggests, mean that the parameters and return values of the controllers are not restricted by type.

Gin's controllers must meet the following format:

```go
func(*gin.Context)
```


If we want to develop programs elegantly through dependency injection and returning parameters, we must encapsulate the controllers freely. For example, to implement the following:

```go
func(req *requests.IndexRequest, user *models.User) (services.Response, error)
```


We can only modify the binding method of `gin.Engine` by inheriting and modifying it.

Below is a segment of the route service code:

```go
// GET is a shorthand form of route.Handle("GET", path, handlers).
func (s *Service) GET(relativePath string, handlers ...services.Handler) services.IRoutes {
    s.Engine.GET(relativePath, toGinHandlers(handlers)...)
    return s
}
```


As you can see, we use `toGinHandlers` to encapsulate the free controllers `handlers`.

You might say, such encapsulation into `any`, what about performance?

Don't worry, in practice, the internal parsing of handlers uses two types: assertions and reflections.

### Using Assertions:

Assertion functions are fixed-type functions, for example:

```go
func(*gin.Context)
func(*gin.Context) error
func(*gin.Context) services.Response
func(*gin.Context) (services.Response, error)
func()
func() error
func() services.Response
func() (services.Response, error)
```


> A total of 8 types of assertion-based controller method definitions are supported.
>
> That means we can still use `func(*gin.Context)` without losing much performance.

### Using Reflection:

If the fixed type cannot be asserted, reflection must be used to determine the parameters and return values, which will result in some performance loss.

```go
func(req *requests.IndexRequest, user *models.User, article models.Article, req requests.OtherRequest) (services.Response, error, string, any, any, any)
```


> Parameters and return values can be whatever you like.

Regarding parameters, if they are structures from the `requests` package, automatic validation of request structure parameters will occur before executing the controller method, and then the parameters will be injected into the controller method. If there is a `required` field, it will check whether the parameters passed from the front end include it, and if not, it will respond with a 422 error. If they are structures from the `models` package, it will check if the request route parameter contains `:id`. If so, it will find the database model based on this id and inject it into the controller method. If there is no id, it will instantiate an empty model.

Regarding errors, it will determine what format of data the front end needs based on the `Accept` request header. If it is not HTML text, the default `httpStatusCode` is 200, and the specific error code is placed in the JSON `code`.

Regarding responses, it will also determine what format of data the front end needs based on the `Accept` request header. If there is an error, it will redirect to a 400 or 404 page.

## Not Only Methods, Can Also Be Non-Methods

It can be any type, roughly categorized into single data and multiple data.

### Single Data

`Accept: application/json`:

```go
route.GET("/", "hello, gower") // Single string

// Response {code: 0, msg:"hello, gower", data: null}

route.GET("/", map[string]any{"hello": "gower"})

// Response {code: 0, msg: "success", data: {"hello": "gower"}}
```


### Multiple Data

`Accept: application/json`:

```go
route.GET("/", []any{
    "hello, gower",
    map[string]any{"hello": "gower"},
})

// Response {code: 0, msg: "hello, gower", data: {"hello": "gower"}}
```


## Command Line Tools

### Create a Basic Web Controller

```shell
$ gower make --controller TestAa
or
$ gower make -c TestAa
```


> This will create `app/http/controllers/teat_aa_controller.go`

### Create a RestApi Web Controller

```shell
$ gower make --controller-rest TestAb
or
$ gower make -cr TestAb
```


> This will create `app/http/controllers/teat_ab_controller.go`

### Create a RestApi API Controller

```shell
$ gower make --api:controller TestAc
or
$ gower make --ic TestAc
```


> This will create `app/api/controllers/teat_ac_controller.go`

## One-Step Creation

### One-Step Creation of Controller, Request, Model

```shell
$ gower make TestAd
```


> This will create:
>
> `app/http/controllers/teat_ad_controller.go`
>
> `app/http/requests/teat_ad_request.go`
>
> `app/models/teat_ad.go`

### One-Step Creation of REST Controller, Request, Model

```shell
$ gower make --rest TestAe
or
$ gower make -r TestAe
```


> This will create:
>
> `app/http/controllers/teat_ae_controller.go`
>
> `app/http/requests/teat_ae_request.go`
>
> `app/models/teat_ae.go`

### One-Step Creation of API Controller, Request, Model

```shell
$ gower make --api TestAf
or
$ gower make -i TestAf
```


> This will create:
>
> `app/api/controllers/teat_af_controller.go`
>
> `app/api/requests/teat_af_request.go`
>
> `app/models/teat_af.go`