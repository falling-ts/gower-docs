---
title: translate-翻译服务
url: "/single/translate-翻译服务"
---

> 翻译服务基础方法, 需要根据开发需求, 自行添加方法与翻译映射. 现在只有 mysql 常见错误的相关翻译.

## 使用的开源库

暂时没有是开源库

## 服务提供者

### 服务标识符

```yaml
"translate"
```

### 注册服务

```go
P.Register("translate", func() (Depends, Resolve) {
    return Depends{"config"}, func(ss ...services.Service) services.Service {
        return translate.Mount(trans.All).Init(ss...)
    }
})
```

### 注册服务格式见 auth 服务

## 依赖的服务

```yaml
"config"
```

## ENV 配置

暂无相关配置

## 服务接口方法

```go
type TranslateService interface {
	Service // 通用服务接口

	DBError(err error) error  // 翻译数据库常见标准错误
}
```



## 使用示例

```go
// Register 注册
func (u *User) Register() error {
	result := db.Create(u)
	return trans.DBError(result.Error)
}
```
