---
title: upload-Upload Service
url: "/en/single/upload-upload-service"
---

## Used Open Source Libraries

```yaml
"github.com/gin-gonic/gin"
```

> Actually using `*gin.Context` for upload functionality

## Service Provider

### Service Identifier

```yaml
"upload"
```


### Register Service

```go
P.Register("upload", Depends{"config", "util"}, func(ss ...services.Service) services.Service {
    return upload.New().Init(ss...)
})
```


### Registration Format Refer to auth Service

## Dependent Services

```yaml
"config", "util"
```


## ENV Configuration

```yaml
# Upload repository
UPLOAD_STORAGE=local
# Local repository host
UPLOAD_LOCAL_HOST=https://localhost
# Local repository location
UPLOAD_LOCAL_PATH=storage/app
```

> Currently, only the local repository is available, other cloud repositories will be added in the future.

## Service Interface Methods

```go
type UploadService interface {
    Service

    Image(c *gin.Context) (string, string, error) // Upload image
    File(c *gin.Context) (string, string, error)  // Upload file, usually non-image
    Store(storage string) Storage  // Specify repository, currently only the local repository is available
}
```




## Usage Examples

```go
path, url, err := upload.Image(c)
path, url, err := upload.File(c)

path, url, err := upload.Store("local").Image(c)
```
