---
title: Templates
url: "/en/single/templates"
---

### Libraries Used

Based on Gin's `LoadHTMLGlob()` or `LoadHTMLFiles()`, which are actually methods from the `html/template` package, two methods for loading multiple templates at once have been encapsulated.

These two methods are:
- One method for loading non-packaged template files.
- Another method for loading templates that are packaged into the program.

The method signatures are as follows:

```go
LoadHTMLGlobs(patterns ...string) error
LoadHTMLFS(embed fs.FS, patterns ...string)
```


### Actual Loading

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
        "resources/views/**/*.tmpl") // Supports multiple levels of template files; if you need a third level, add "resources/views/**/**/*.tmpl"

    if err != nil {
        if Tmpl == nil {
            panic("No template content to load")
        }

        // If there are no template files in the program root directory, load templates from within the packaged program, add more levels as needed "views/**/**/*.tmpl"
        route.LoadHTMLFS(Tmpl,
            "views/*.tmpl",
            "views/**/*.tmpl")
    }
}
```


> Note: When adding template files with three levels of directory structure, `resources/embed.go` also needs to add a line `//go:embed views/**/**/*` to include the third level directory when running `go build -tags tmpl`.

### Template Naming

For example, in `resources/views/home/hello.tmpl`

```html
{{define "home/hello"}}

  {{template "head" .}}
  <div id="home-hello">Hello, Gower</div>
  {{template "foot" .}}

{{end}}
```


> Like `{{define "home/hello"}}{{end}}`. All template files must be wrapped in a `define` block, and the name of the `define` must be unique, regardless of the directory level.
>
> Because `html/template` internally stores templates in a `map[string]any`, if the same name is defined in different directories, the later template will overwrite the earlier one, causing inconsistencies in the actual content.
>
> It is recommended to use the relative path structure from the `views` directory for the `define` name, without the file extension.

### Using Layouts

The layout template is `app.tmpl`, which internally defines `head` and `foot`. Use it as shown above, by including `{{template "head" .}}` and `{{template "foot" .}}` in your own template files. The content should be placed between these two templates.

### How to Use

Since the response is a template, the `Accept` header of the front-end request should be `text/html`, not `application/json`.

### Returning `services.Response` in the Controller

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
    // Respond with the template name and template data
    return res.Ok("auth/me", app.Data{
        "title": "我",
    }), nil
}
```


> "auth/me" is the template name.
>
> You might notice that this looks similar to the format used for JSON responses. This is because, when handling free controllers, the response is determined based on the `Accept` header. If it is `text/html`, the string type represents the template name, and the data represents the template data. If it is `application/json`, the string represents the `Msg` or `Token`, and the data represents the `Data`.