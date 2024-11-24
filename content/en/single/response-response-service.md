---
title: response-Response Service
url: "/en/single/response-response-service"
---

## Open Source Libraries Used

No open source libraries are used.

## Service Provider

### Service Identifier

```yaml
"response"
```


### Registering the Service

```go
P.Register("response", Depends{"auth", "cookie", "util", "config"}, func(ss ...services.Service) services.Service {
    r := new(responses.Response)
    return response.Mount(r).Init(ss...)
})
```


### Service Registration Format See auth Service

## Dependent Services

```yaml
"auth", "cookie", "util", "config"
```


## ENV Configuration

```yaml
# Response data key type: CamelType, camelType, snake_type
RES_KEY_TYPE=snake_type
# Request data response MIME types, multiple values separated by commas
RES_MIMES=application/json,text/html,application/xml,text/plain,application/x-yaml,application/toml
```

> CamelType: PascalCase
>
> camelType: camelCase
>
> snake_type: snake_case

## Service Interface Methods

```go
// Response is the response body interface
type Response interface {
    Service // General response interface
    
    Set(any) Response // Set response data
    Get(field string) (any, error) // Get response data
    New(code int, args ...any) Response // Create response
    Build(code int, args ...any) Response // Build response
    Handle(c *gin.Context) bool // Handle response
}
```


## Usage Example

See the chapter on Basic Content - Response for detailed examples.