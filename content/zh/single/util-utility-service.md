---
title: util-工具服务
url: "/single/util-工具服务"
---

> 当前仅设置了, 现在能用到的函数, 你可以根据自己的业务扩展这个服务

## 使用的开源库

```
"github.com/jaevor/go-nanoid"
```
> go-nanoid: 仅仅是 Nanoid 方法的开源库而已

## 服务提供者

### 服务标识符

```
"util"
```

### 注册服务

```go
P.Register("util", func(...services.Service) services.Service {
    return util.New().Init()
})
```

### 注册服务格式见 auth 服务

## 依赖的服务

暂无依赖的服务

## ENV 配置

暂无相关配置

## 服务接口方法

```go
type UtilService interface {
	Service

	Nanoid(args ...int) string // 获取 nanoid 唯一 id, 参数是 int 类型, 例如: util.Nanoid(32) 获取 32 字节长度的唯一 id, 默认是 21 字节
	Direct(v reflect.Value) reflect.Value // reflect.Indirect 反操作, 即获取值的指针变量

	SetEnv(env, key, value string) error // 修改设置 .env.xxx 的值
	SecretKey(length int) (string, error) // 获取随机密钥

	ExcpKey() string // 获取异常缓存值的键
	BlackTokenKey(nanoid string) string // 获取 JWT 黑名单 Token 的缓存键

	Ptr(v any) any // 返回任意基础标量类型值的指针

	CreateDir(dir string) string // 目录不存在则递归创建目录
	IsExist(file string) bool // 文件或目录是否存在
}
```



## 使用示例

```go
id := util.Nanoid(21)
v := util.Direct(relValue)

err := util.SetEnv(".env.development", "APP_NAME", "Test")
key, err := util.SecretKey(32)

excpKey := util.ExcpKey
tokenKey := util.BlackTokenKey(Nanoid(21))

p := util.Ptr("test").(*string)
dir := util.CreateDir(dir)
ok := util.IsExist("/path/file.txt")
```