---
title: 工具
url: "/single/工具"
---

utils 是一组基础类型处理工具.

与服务的区别是, utils 只是提供基础类型的处理的链式调用的语法糖, 并没有声明相关接口.

当然, 当下功能只是一个起步而已, 后期再慢慢完善.

## 切片

### 字符切片

```go


s := []string{"a", "b", "c"}

ok := slice.Strings(s).Has("a") // true
```

### 字符串

```go
s := "UpDate"
str.Conv(s).Snake() // up_date
```

> 功能刚刚起步, 想实现一种链式调用的处理效果
>
> 链式调用比直接用函数传入类型, 感觉开发起来舒服一些

比如实现一个 Map 函数, 处理 `map[string]any`

```go
result := mapValue.Map(func (item any) any {
    return item
})

等价于

for key, item := range mapValue {
    mapValue[key] = item
}
```

后面慢慢完善吧.