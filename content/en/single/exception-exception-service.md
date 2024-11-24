---
title: exception-Exception Service
url: "/en/single/exception-exception-service"
---

## Open Source Libraries Used

Not using any open source libraries yet

## Service Provider

### Service Identifier

```yaml
"exception"
```


### Register Service

```go
P.Register("exception", Depends{"config", "cache", "util", "cookie"}, func(ss ...services.Service) services.Service {
    e := new(exceptions.Exception)
    return exception.Mount(e).Init(ss...)
})
```


### Registration Format Refer to Auth Service

## Dependent Services

```yaml
"config", "cache", "util", "cookie"
```


## ENV Configuration

No related configuration

## Service Interface Methods

```go
// Exception is the interface for exception content
type Exception interface {
	error // General error interface
	Service // General service interface
	
	Set(arg any) Exception // Set exception data
	Get(field string) (any, error) // Get exception data
	New(code int, args ...any) Exception // Create a new exception
	Build(args ...any) Exception // Build an exception
	Handle(c *gin.Context) bool // Handle the exception, after returning an exception, the system will automatically call this function.
}
```

> The actual exception service is provided by the exception content, the reason is explained in the chapter Basic Content - Exception.

## Usage Example

See the chapter Basic Content - Exception for usage examples.