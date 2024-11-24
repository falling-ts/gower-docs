---
title: route-路由服务
url: "/single/route-路由服务"
---

## 使用的开源库

```yaml
"github.com/gin-gonic/gin"
```

## 服务提供者

### 服务标识符

```yaml
"route"
```

### 注册服务

```go
P.Register("route", func() (Depends, Resolve) {
    return Depends{"config", "exception", "db", "response", "util"}, func(ss ...services.Service) services.Service {
        return route.New().Init(ss...)
    }
})
```

### 注册服务格式见 auth 服务

## 依赖的服务

```yaml
"config", "exception", "db", "response", "util"
```

## ENV 配置

暂无相关配置

## 服务接口方法

```go
// Handler 将 gin 中间件使用的处理程序定义为返回值.
type Handler any

// Handlers HandlersChain 定义 HandlerFunc 的切片.
type Handlers []Handler

// IRouter 定义所有路由器句柄接口, 包括单路由器和组路由器.
type IRouter interface {
	IRoutes
	Group(string, ...Handler) IRouter // 路由分组
}

// IRoutes 定义所有路由器句柄接口.
type IRoutes interface {
    Use(...Handler) RouteService // 使用公共中间件

    Handle(string, string, ...Handler) IRoutes // 通用绑定路由
    Any(string, ...Handler) IRoutes // 绑定任意方法路由
    GET(string, ...Handler) IRoutes // 绑定 GET 方法路由
    POST(string, ...Handler) IRoutes // 绑定 POST 方法路由
    DELETE(string, ...Handler) IRoutes // 绑定 DELETE 方法路由
    PATCH(string, ...Handler) IRoutes // 绑定 PATCH 方法路由
    PUT(string, ...Handler) IRoutes // 绑定 PUT 方法路由
    OPTIONS(string, ...Handler) IRoutes // 绑定 OPTIONS 方法路由, OPTIONS 可以用来获取跨域预检数据
    HEAD(string, ...Handler) IRoutes // 绑定 HEAD 方法路由
    Match([]string, string, ...Handler) IRoutes // 绑定多个方法路由

    StaticFile(string, string) IRoutes // 静态文件路由
    StaticFileFS(string, string, http.FileSystem) IRoutes // 带文件接口的文件路由
    Static(string, string) IRoutes // 通用静态资源路由
    StaticFS(string, http.FileSystem) IRoutes // 带文件接口的通用静态资源路由
}

// RouteService 路由服务接口
type RouteService interface {
    Service // 通用服务接口

    Handler() http.Handler // 获取控制器方法
    SecureJsonPrefix(prefix string) RouteService // 设置 JSON 响应的前缀，以防止 JSON 劫持攻击

    Delims(left, right string) RouteService // 模板变量左右分割符
    LoadHTMLGlob(pattern string) // 加载单层目录体系的模板文件
    LoadHTMLGlobs(patterns ...string) error // 加载多层目录体系的模板文件
    LoadHTMLFS(embed fs.FS, patterns ...string) // 加载多层目录体系的嵌套模板文件
    LoadHTMLFiles(files ...string) // 加载多个模板文件
    SetHTMLTemplate(tmpl *template.Template) // 设置 Gin 模板
    SetFuncMap(funcMap template.FuncMap) // 设置模板函数的映射, 模板函数是可以在模板中调用的函数, 用于执行一些操作或处理数据

    NoRoute(handlers ...Handler) // 没有找到路由的处理方法
    NoMethod(handlers ...Handler) // 方法拒绝的处理方法

    Routes() (routes gin.RoutesInfo) // 返回所有已注册的路由信息

    Run(addr ...string) (err error) // 启动 http, 绑定端口
    RunTLS(addr, certFile, keyFile string) // 启动 HTTPS 服务
    RunUnix(file string) (err error) // 启动 Unix Socket 服务的方法
    RunFd(fd int) (err error) // 启动 HTTP 服务的方法, 它接收一个 fd 参数, 表示已经打开的一个文件描述符.
    RunListener(listener net.Listener) (err error) // 启动 HTTP 服务的方法, 接收一个 listener 参数, 表示已经创建好的一个 net.Listener 对象.

    HandleContext(c *gin.Context) // 实现自定义中间件的一个函数签名, 它接收一个 *gin.Context 对象作为参数, 并在自定义中间件中被调用.

    Group(relativePath string, handlers ...Handler) IRouter
    UseBefore(middleware ...Handler) RouteService // 在所有中间前面绑定中间件
    Use(middleware ...Handler) RouteService // 使用中间件, 默认放在后面
    Handle(httpMethod, relativePath string, handlers ...Handler) IRoutes
    Any(relativePath string, handlers ...Handler) IRoutes
    GET(relativePath string, handlers ...Handler) IRoutes
    POST(relativePath string, handlers ...Handler) IRoutes
    DELETE(relativePath string, handlers ...Handler) IRoutes
    PATCH(relativePath string, handlers ...Handler) IRoutes
    PUT(relativePath string, handlers ...Handler) IRoutes
    OPTIONS(relativePath string, handlers ...Handler) IRoutes
    HEAD(relativePath string, handlers ...Handler) IRoutes
    Match(methods []string, relativePath string, handlers ...Handler) IRoutes

    StaticFile(relativePath, filepath string) IRoutes
    StaticFileFS(relativePath, filepath string, fs http.FileSystem) IRoutes
    Static(relativePath, root string) IRoutes
    StaticFS(relativePath string, fs http.FileSystem) IRoutes

    ServeHTTP(w http.ResponseWriter, req *http.Request) // 自定义的 HTTP 处理器的接口实现方法
}
```

## 使用示例

见 基础内容-路由 一章