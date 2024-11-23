---
title: validator-验证服务
url: "/single/validator-验证服务"
---

## 使用的开源库

```
"github.com/go-playground/validator/v10"
```

## 服务提供者

### 服务标识符

```
"validator"
```

### 注册服务

```go
P.Register("validator", func(...services.Service) services.Service {
    return validator.New().Init()
})
```

### 注册服务格式见 auth 服务

## 依赖的服务

暂无依赖

## ENV 配置

暂无相关环境

## 服务接口方法

```go
type ValidatorService interface {
	Service // 通用服务接口
	
	Translate(errs validator.ValidationErrors) validator.ValidationErrorsTranslations // 自定义返回多个验证错误
	GetTrans() ut.Translator // 获取验证
}
```

## 使用示例

验证方法已经在 `app/request.go` 中配置好了, 一般无需修改调整, 直接按照 基础内容-请求验证器 的教程使用验证即可.