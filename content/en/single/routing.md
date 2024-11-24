---
title: Routing
url: "/en/single/routing"
---

### Preface

The routing functionality is mainly provided by the `route` service, which internally inherits from Gin.

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

// Service is the main structure of the routing service
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

// Init initializes the service
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

// ... Override base routing methods to implement free controllers
```


The `route.Service` struct implements the `services.RouteService` interface.

### How to Use

Essentially, defining routes in this system is not much different from defining routes in Gin, and it also supports route grouping functionality.

Define web routes in `routes/web.go` and API routes in `routes/api.go`.

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

	// Registration and login
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
	
	// TODO: Continue to expand the program here
}
```


The basic format for defining routes is as follows:

```go
route.GET("/path", middlewares.Mw1, middlewares.Mw2, controllers.Home.Index)
```

> The controller method should always be placed last, with any number of route middleware defined before it. The same applies to other methods like POST.

### How to Add Your Own Route Configuration

Assume we want to create an admin management page and need to add an `admin.go` route configuration. Create the `routes/admin.go` file with the following initial content:

```go
package routes

import (
	admin "gower/app/admin/controllers"
)

func init() {
	ar := route.Group("/admin")
	{
		ar.GET("/", admin.Home.Index)
		// TODO: Define other routes
	}
}
```


### `controllers.Home.Index`

- `controllers`: Or alias `web`, is the package name where the controllers are located.
- `Home`: The actual instance of the controller struct.
- `Index`: The method bound to the struct.

### About This Method, For Example `Index`

Since the system has encapsulated Gin's `handlerFunc` with `any`, the controller methods bound to the routes become very flexible. The specifics of this flexibility will be detailed in the next chapter.