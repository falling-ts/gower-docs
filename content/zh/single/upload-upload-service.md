---
title: upload-上传服务
url: "/single/upload-上传服务"
---

## 使用的开源库

```
"github.com/gin-gonic/gin"
```
> 实际使用 `*gin.Context` 上传功能

## 服务提供者

### 服务标识符

```
"upload"
```

### 注册服务

```go
P.Register("upload", Depends{"config", "util"}, func(ss ...services.Service) services.Service {
    return upload.New().Init(ss...)
})
```

### 注册服务格式见 auth 服务

## 依赖的服务

```
"config", "util"
```

## ENV 配置

```
# 上传的仓库
UPLOAD_STORAGE=local
# 本地仓库 Host
UPLOAD_LOCAL_HOST=https://localhost
# 本地仓库位置
UPLOAD_LOCAL_PATH=storage/app
```
> 当前, 只有 local 仓库, 以后会添加其他云仓库的.

## 服务接口方法

```go
type UploadService interface {
    Service

    Image(c *gin.Context) (string, string, error) // 上传图片
    File(c *gin.Context) (string, string, error)  // 上传文件, 通常是非图片
    Store(storage string) Storage  // 指定仓库, 当前只有 local 仓库
}
```



## 使用示例

```go
path, url, err := upload.Image(c)
path, url, err := upload.File(c)

path, url, err := upload.Store("local").Image(c)
```