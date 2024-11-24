---
title: 控制器
url: "/single/控制器"
---

## 自由控制器

自由控制器, 顾名思义就是指, 控制器参数和返回值, 不受类型限制.

Gin 的控制器必须满足以下格式

```go
func(*gin.Context)
```

如果我们想通过依赖注入, 以及返回参数的形式优雅的开发程序, 就必须对控制器进行自由封装. 例如实现如下:

```go
func(req *requests.IndexRequest, user *models.User) (services.Response, error)
```

就只能修改 gin.Engine 的绑定方法, 通过继承来修改.

下面是一段 route 服务的代码

```go
// GET 是 route.Handle("GET", path, handlers) 的短语形式.
func (s *Service) GET(relativePath string, handlers ...services.Handler) services.IRoutes {
    s.Engine.GET(relativePath, toGinHandlers(handlers)...)
    return s
}
```

可以看到我们用 `toGinHandlers` 封装了自由控制器 `handlers`.

您可能要说, 这样封装成 any, 性能怎么办.

没关系的, 实际内部解析 handler 分使用断言与使用反射两种类型:

### 使用断言:

断言函数为固定类型的函数, 例如:

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
> 总的支持 8 中类型的断言类的控制器方法定义
>
> 也就是说我们还可以用 `func(*gin.Context)`, 并且性能不会丢失太多.

### 使用反射:

断言不到固定类型, 只能反射是什么参数, 返回了什么, 性能有所丢失.

```go
func(req *requests.IndexRequest, user *models.User, article models.Article, req requests.OtherRequest) (services.Response, error, string, any, any, any)
```
> 参数和返回值, 你怎么喜欢怎么来.

关于参数, 如果是 `requests` 包里的结构体, 在执行控制器方法前, 会自动进行请求结构体参数的校验, 然后注入控制器方法参数上, 假设有 required, 会校验前端传过来的参数有没有包含, 不包含的话, 响应 422 错误. 如果是 `models` 包里的结构体, 会判断请求路由参数上有没有 `:id`, 如果有, 根据这个 id 查找数据库模型, 然后注入控制器方法参数上. 没有 id, 会实例化一个空的 model 模型.

关于错误, 会根据 Accept 请求头, 判断前端需要什么格式的数据, 如果不是 html 文本, 默认 httpStatusCode 是 200, 具体错误码, 放在 json code 中.

关于响应, 也会根据 Accept 请求头, 判断前端需要什么格式的数据, 如果报错, 会跳转 400 或 404 页面.

## 不仅仅只支持方法, 也可以不是方法

可以是任何类型, 其实大致也可以分类的, 主要分为单数据和多数据.

### 单数据

`Accept: application/json`:

```go
route.GET("/", "hello, gower") // 单个 string

// 响应 {code: 0, msg:"hello, gower", data: null}

route.GET("/", map[string]any{"hello": "gower"})

// 响应 {code: 0, msg: "success", data: {"hello": "gower"}}
```

### 多数据

`Accept: application/json`:

```go
route.GET("/", []any{
    "hello, gower",
    map[string]any{"hello", "gower"},
})

// 响应 {code: 0, msg: "hello, gower", data: {"hello": "gower"}}
```

## 命令行工具

### 创建基础 Web 控制器

```shell
$ gower make --controller TestAa
或
$ gower make -c TestAa
```
> 会创建 `app/http/controllers/teat_aa_controller.go`

### 创建 RestApi Web 控制器

```shell
$ gower make --controller-rest TestAb
或
$ gower make -cr TestAb
```
> 会创建 `app/http/controllers/teat_ab_controller.go`

### 创建 RestApi Api 控制器

```shell
$ gower make --api:controller TestAc
或
$ gower make --ic TestAc
```
> 会创建 `app/api/controllers/teat_ac_controller.go`

## 创建一步到位

### 一步创建控制器, 请求, 模型

```shell
$ gower make TestAd
```
> 会创建:
>
> `app/http/controllers/teat_ad_controller.go`
>
> `app/http/requests/teat_ad_request.go`
>
> `app/models/teat_ad.go`

### 一步创建 Rest 控制器, 请求, 模型

```shell
$ gower make --rest TestAe
或
$ gower make -r TestAe
```
> 会创建:
>
> `app/http/controllers/teat_ae_controller.go`
>
> `app/http/requests/teat_ae_request.go`
>
> `app/models/teat_ae.go`

### 一步创建 Api 控制器, 请求, 模型

```shell
$ gower make --api TestAf
或
$ gower make -i TestAf
```
> 会创建:
>
> `app/api/controllers/teat_af_controller.go`
>
> `app/api/requests/teat_af_request.go`
>
> `app/models/teat_af.go`
