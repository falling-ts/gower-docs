---
title: response-响应服务
url: "/single/response-响应服务"
---

## 使用的开源库

暂未使用开源库

## 服务提供者

### 服务标识符

```
"response"
```

### 注册服务

```go
P.Register("response", Depends{"auth", "cookie", "util", "config"}, func(ss ...services.Service) services.Service {
    r := new(responses.Response)
    return response.Mount(r).Init(ss...)
})
```

### 注册服务格式见 auth 服务

## 依赖的服务

```
"auth", "cookie", "util", "config"
```

## ENV 配置

```
# 响应数据键类型: CamelType, camelType, snake_type
RES_KEY_TYPE=snake_type
# 请求的数据响应 MIME, 多个用英文逗号分割
RES_MIMES=application/json,text/html,application/xml,text/plain,application/x-yaml,application/toml
```
> CamelType: 大驼峰
>
> camelType: 小驼峰
>
> snake_type: 蛇形

## 服务接口方法

```go
// Response 响应体接口
type Response interface {
	Service // 通用响应接口
	
	Set(any) Response // 设置响应数据
	Get(field string) (any, error) // 获取响应数据
	New(code int, args ...any) Response // 创建响应
	Build(code int, args ...any) Response // 构建响应
	Handle(c *gin.Context) bool // 处理响应
}
```



## 使用示例

详细见 基础内容-响应 一章