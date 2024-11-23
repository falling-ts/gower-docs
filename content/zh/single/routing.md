---
title: 路由
url: "/single/路由"
---

## 前言

路由功能主要由 `route` 服务来提供, 而 route 服务内部继承了 Gin.

`services/route/route.go`
```go
package route

import (
    "html/template"
    "io/fs"
    "net/http"
    "path/filepath"

    "gower/services"

    "github.com/gin-gonic/gin"
    "github.com/gin-gonic/gin/render"
)

// Service 路由服务主结构体
type Service struct {
    *gin.Engine
    delims render.Delims
}

var (
    config    services.Config
    exception services.Exception
    db        services.DBService
    response  services.Response
    util      services.UtilService
)

func New() services.RouteService {
    return new(Service)
}

// Init 初始化
func (s *Service) Init(args ...services.Service) services.Service {
    config = args[0].(services.Config)
    exception = args[1].(services.Exception)
    db = args[2].(services.DBService)
    response = args[3].(services.Response)
    util = args[4].(services.UtilService)

    mode := map[string]string{
        "development": "debug",
        "production":  "release",
        "test":        "test",
    }[config.Get("app.mode", "test").(string)]
    gin.SetMode(mode)

    s.Engine = gin.New()
    s.delims = render.Delims{Left: "{{", Right: "}}"}

    return s
}

// ... 重写基础路由方法, 实现自由控制器
```

`route.Service` 结构体实现了 `services.RouteService` 接口

## 如何使用

本质上与 Gin 定义路由时, 没有太大区别, 同样支持路由分组功能.

定义路由请在 `routes/web.go` 中定义 Web 路由, 在 `routes/api.go` 中定义 api 路由.

`routes/web.go`
```go
package routes

import (
	web "gower/app/http/controllers"
	mws "gower/app/http/middlewares"
	"gower/public"
)

func init() {
	route.GET("/", mws.Default(), web.Home.Index)

	// 注册与登录
	auth := route.Group("/auth")
	{
		auth.GET("/register", web.Auth.RegisterForm)
		auth.POST("/register", web.Auth.Register)
		auth.GET("/login", web.Auth.LoginForm)
		auth.POST("/login", web.Auth.Login)
		auth.GET("/me", mws.Auth(), web.Auth.Me)
		auth.POST("/logout", mws.Auth(), web.Auth.Logout)
	}

	route.GET("/400", web.Excp.BadRequest)
	route.GET("/404", web.Excp.NotFound)

	if public.Static == nil {
		route.StaticFile("/favicon.ico", "public/static/images/favicon.ico")
		route.Static("/static", "public/static")
	} else {
		route.StaticFileFS("/favicon.ico", "images/favicon.ico", public.Static)
		route.StaticFS("/static", public.Static)
	}

	route.Static("/upload", "public/upload")
	
	//TODO: 在这里继续扩展程序
}
```

定义基础路由格式如下:

```go
route.GET("/path", middlewares.Mw1, middlewares.Mw2, controllers.Home.Index)
```
> 控制器方法永远要放在最后, 前面可以定义若干路由中间件, POST 等其它方法同理.

## 如何添加自己的路由配置

假设, 我们要做一个后台管理页面, 需要添加一个 admin.go 的路由配置, 那就创建 `routes/admin.go` 文件, 初始内容如下:

```go
package routes

import (
	admin "gower/app/admin/controllers"
)

func init() {
	ar := route.Group("/admin")
	{
		ar.GET("/", admin.Home.Index)
		//TODO: 定义其它路由
	}
}

```

## `controllers.Home.Index`

- controllers: 或者别名 web, 是控制器所在的包名
- Home: 实际控制器结构体的实例
- Index: 绑定在结构体上的方法

## 关于这个方法, 例如 `Index`

由于系统对 Gin 的 `handlerFunc` 进行了 any 封装, 所以绑定在路由上的控制器方法, 就变的非常自由, 具体如何自由, 在下一章详细说明吧.