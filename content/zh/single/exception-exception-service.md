---
title: exception-异常服务
url: "/single/exception-异常服务"
---

## 使用的开源库

暂未使用开源库

## 服务提供者

### 服务标识符

```
"exception"
```

### 注册服务

```go
P.Register("exception", Depends{"config", "cache", "util", "cookie"}, func(ss ...services.Service) services.Service {
    e := new(exceptions.Exception)
    return exception.Mount(e).Init(ss...)
})
```

### 注册服务格式见 auth 服务

## 依赖的服务

```
"config", "cache", "util", "cookie"
```

## ENV 配置

没有相关配置

## 服务接口方法

```go
// Exception 异常内容接口
type Exception interface {
	error // 通用错误接口
	Service // 通用服务接口
	
	Set(arg any) Exception // 设置异常数据
	Get(field string) (any, error) // 获取异常数据
	New(code int, args ...any) Exception // 创建新异常
	Build(args ...any) Exception // 构建异常
	Handle(c *gin.Context) bool // 处理异常, return 异常后, 系统会自动调用该函数.
}
```
> 实际提供异常服务的是异常内容, 原因在 基础内容-异常 一章有说明.

## 使用示例

使用示例见 基础内容-异常 一章