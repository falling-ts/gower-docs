---
title: route-Routing Service
url: "/en/single/route-routing-service"
---

## Open Source Libraries Used

```yaml
"github.com/gin-gonic/gin"
```


## Service Provider

### Service Identifier

```yaml
"route"
```


### Register Service

```go
P.Register("route", func() (Depends, Resolve) {
    return Depends{"config", "exception", "db", "response", "util"}, func(ss ...services.Service) services.Service {
        return route.New().Init(ss...)
    }
})
```


### Service Registration Format See auth Service

## Dependent Services

```yaml
"config", "exception", "db", "response", "util"
```


## ENV Configuration

No related configuration

## Service Interface Methods

```go
// Handler defines the handler used by gin middleware as a return value.
type Handler any

// Handlers defines a slice of HandlerFunc.
type Handlers []Handler

// IRouter defines all router handle interfaces, including single routers and group routers.
type IRouter interface {
	IRoutes
	Group(string, ...Handler) IRouter // Route grouping
}

// IRoutes defines all router handle interfaces.
type IRoutes interface {
    Use(...Handler) RouteService // Use common middleware

    Handle(string, string, ...Handler) IRoutes // Bind generic route
    Any(string, ...Handler) IRoutes // Bind any method route
    GET(string, ...Handler) IRoutes // Bind GET method route
    POST(string, ...Handler) IRoutes // Bind POST method route
    DELETE(string, ...Handler) IRoutes // Bind DELETE method route
    PATCH(string, ...Handler) IRoutes // Bind PATCH method route
    PUT(string, ...Handler) IRoutes // Bind PUT method route
    OPTIONS(string, ...Handler) IRoutes // Bind OPTIONS method route, OPTIONS can be used to get CORS preflight data
    HEAD(string, ...Handler) IRoutes // Bind HEAD method route
    Match([]string, string, ...Handler) IRoutes // Bind multiple method routes

    StaticFile(string, string) IRoutes // Static file route
    StaticFileFS(string, string, http.FileSystem) IRoutes // File route with file system interface
    Static(string, string) IRoutes // Generic static resource route
    StaticFS(string, http.FileSystem) IRoutes // Generic static resource route with file system interface
}

// RouteService defines the router service interface.
type RouteService interface {
    Service // General service interface

    Handler() http.Handler // Get controller method
    SecureJsonPrefix(prefix string) RouteService // Set JSON response prefix to prevent JSON hijacking attacks

    Delims(left, right string) RouteService // Template variable delimiters
    LoadHTMLGlob(pattern string) // Load template files from a single directory level
    LoadHTMLGlobs(patterns ...string) error // Load template files from multiple directory levels
    LoadHTMLFS(embed fs.FS, patterns ...string) // Load nested template files from multiple directory levels
    LoadHTMLFiles(files ...string) // Load multiple template files
    SetHTMLTemplate(tmpl *template.Template) // Set Gin template
    SetFuncMap(funcMap template.FuncMap) // Set template function map, template functions are callable in templates to perform operations or process data

    NoRoute(handlers ...Handler) // Handler for when no route is found
    NoMethod(handlers ...Handler) // Handler for method not allowed

    Routes() (routes gin.RoutesInfo) // Return all registered route information

    Run(addr ...string) (err error) // Start HTTP server, bind port
    RunTLS(addr, certFile, keyFile string) // Start HTTPS server
    RunUnix(file string) (err error) // Start Unix Socket server
    RunFd(fd int) (err error) // Start HTTP server, it receives an fd parameter, indicating an already opened file descriptor.
    RunListener(listener net.Listener) (err error) // Start HTTP server, it receives a listener parameter, indicating an already created net.Listener object.

    HandleContext(c *gin.Context) // Function signature for implementing custom middleware, it receives a *gin.Context object as a parameter and is called within custom middleware.

    Group(relativePath string, handlers ...Handler) IRouter
    UseBefore(middleware ...Handler) RouteService // Bind middleware before all others
    Use(middleware ...Handler) RouteService // Use middleware, default is placed at the end
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

    ServeHTTP(w http.ResponseWriter, req *http.Request) // Implementation of the custom HTTP handler interface method
}
```


## Usage Example

See the chapter on Basic Content - Routing.