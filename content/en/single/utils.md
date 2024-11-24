---
title: Utils
url: "/en/single/utils"
---

utils is a set of basic type processing tools.

The difference from services is that utils only provide syntactic sugar for chainable calls for basic type processing, without declaring related interfaces.

Of course, the current functionality is just a starting point, and will be improved over time.

## Slice

### String Slice

```go
s := []string{"a", "b", "c"}

ok := slice.Strings(s).Has("a") // true
```


### String

```go
s := "UpDate"
str.Conv(s).Snake() // up_date
```


> The functionality is just getting started, aiming to achieve a chainable call processing effect.
>
> Chainable calls feel more comfortable to develop compared to directly passing types to functions.

For example, implementing a Map function to process `map[string]any`

```go
result := mapValue.Map(func (item any) any {
    return item
})

Equivalent to

for key, item := range mapValue {
    mapValue[key] = item
}
```
