---
title: 模板
url: "/single/模板"
---

## 使用的库包

在 Gin 的 `LoadHTMLGlob()` 或者 `LoadHTMLFiles()` 基础上, 实际是 `html/template` 包的方法, 封装了两个多模板一次性加载的方法.

这个两个方法, 一个是非打包模板文件的加载方法, 一个是打包进程序的模板加载方法. 具体方法签名如下:

```go
LoadHTMLGlobs(patterns ...string) error
LoadHTMLFS(embed fs.FS, patterns ...string)
```

## 实际加载

`resources/resources.go`

```go
package resources

import (
    "embed"
    "gower/app"
)

var (
    route = app.Route()
    Tmpl  *embed.FS
)

func init() {
    err := route.LoadHTMLGlobs(
        "resources/views/*.tmpl",
        "resources/views/**/*.tmpl") // 支持多种层级的模板文件, 如果需要加三层, 那就添加一行 "resources/views/**/**/*.tmpl"
        
    if err != nil {
        if Tmpl == nil {
            panic("没有模板内容可加载")
        }
        
        // 如果程序根目录没有模板文件, 从打包的程序内部找模板, 多层级继续添加 "views/**/**/*.tmpl"
        route.LoadHTMLFS(Tmpl,
            "views/*.tmpl",
            "views/**/*.tmpl")
    }
}
```

> 注: 添加三层目录结构的模板文件时, `resources/embed.go` 也需要添加一行 `//go:embed views/**/**/*`, 用来在执行 `go build -tags tmpl` 时, 打包三层目录进程序内.

## 关于模板命名

例如在 `resources/views/home/hello.tmpl` 中

```html
{{define "home/hello"}}

  {{template "head" .}}
  <div id="home-hello">Hello, Gower</div>
  {{template "foot" .}}

{{end}}
```

> 如 `{{define "home/hello"}}{{end}}`. 所有模板文件都需要用 define 包裹, 并且 define 的名称必须是唯一的, 不管文件在几层目录下.
>
> 因为 `html/template` 内部处理模板时, 放在一个 `map[string]any` 中, 这样如果在不同目录下, define 了相同的名称, 后面的模板会覆盖前面模板, 造成实际内容不一致的问题.
>
> define 名称建议使用相对于 views 目录的路径结构, 不需要文件名后缀.

## 使用 Layout

layout 布局模板就是 `app.tmpl`, 内部 define 了 `head` 与 `foot`, 使用如上所示, 在自己模板文件名称内使用 `{{template "head" .}}` 和 `{{template "foot" .}}` 即可. 内容放在这两个 template 中间

## 如何使用

因为要响应模板, 那么前端实际请求的 `Accept` 就是 `text/html` 了, 不能是 `application/json` 哦

### 控制器内返回 `services.Response`

```go

package controllers

import (
    "gower/app"
    "gower/app/http/requests"
    "gower/app/models"
    "gower/services"
    "github.com/gin-gonic/gin"
)

type AuthController struct {
    app.Controller
}

var Auth = new(AuthController)

// Me 获取个人信息
func (a *AuthController) Me() (services.Response, error) {
    // 响应模板名称, 与模板数据即可
    return res.Ok("auth/me", app.Data{
        "title": "我",
    }), nil
}
```
> "auth/me" 就是模板名称
>
> 你可能发现怎么和响应 json 一个格式呀, 因为在处理自由控制器时, 响应根据前端 Accept 进行 Negotiate 判断, 如果是 text/html, 这时候的字符串类型就代表模板名称, 数据就代表模板数据, 如果是 application/json, 这时候的字符串就代表 Msg 或 Token, 数据就代表 Data.