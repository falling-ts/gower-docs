---
title: 高级数据处理
url: "/single/高级数据处理"
---

模型, 是来源数据的结果中心; 数据库可以算作是来源数据的最终归宿.

反方向同理, 数据库和模型是前端数据的源头.

## 面向过程 与 面向数据

面向过程, 是指处理数据, 一个一个字段的来, 通过传统赋值来把来源数据, 赋值到目标数据上

> 优点, 简单直接, 性能好, 适合逻辑清晰, 数据量小的情况
>
> 缺点, 数据量一多, 处理繁琐, 维护起来越来越难

面向数据, 是指通过映射规则, 把来源数据赋值到目标数据上

> 优点, 数据结果一目了然, 逻辑清晰, 数据量大小均可使用
>
> 缺点, 使用理解需要花时间, 并且有一定性能损耗

## 模型

我们把模型类比为一栋房屋, 房屋内部声明了一个个数据的座位.

门口有两个门卫, 一个门卫负责对进来的数据进行处理, 就叫他 In 吧; 一个门卫负责则对出去的数据进行处理, 就叫他 Out 吧.

假设这个房屋名字叫 Article, 是存放文章的模型.

结构如下:

```go
type Article struct {
    Model
    Title      string  `gorm:"type:string;default:'';not null;commit:标题"`
    Content    *string `gorm:"type:text;commit:内容"`
    
    // 所属类别
    CategoryID *uint
    Category   *TestCategory `gorm:"foreignKey:CategoryID"`
    
    // 所属作者
    UserID     *uint
    User       *TestUser     `gorm:"foreignKey:UserID"`
    
    // 文章评论
    Comments   []TestComment `gorm:"foreignKey:ArticleID"`
}
```

假设有个控制器方法, 是添加文章

```go
func (*ArticleController) Store(req *requests.ArticleRequest, article *models.Article, auth *models.Auth) (services.Response, error) {
    //TODO: 处理与保存数据
    
    return res.Ok("添加成功")
}
```
> req 数据已经经过校验, 必填都会必填

### 面向过程处理

```go
article.Title = req.Title
article.Content = req.Content
article.CategoryID = req.CategoryID
article.UserID = auth.ID

result := db.Create(article)
if result.Error != nil {
    return nil, excp.BadRequest(result.Error, "添加失败")
}
```

### 面向数据处理, In 处理

我们来看一下如何面向数据, 进行处理.

首先这种情况就是 In 门卫, 将 req 的数据, 放入屋子内对应的座位上.

```go
article, err := article.In(req, app.Rule{
    "title": "title", // 首字母大不大写, 无所谓, 内部自动转换为首字母大写.
    "_skips": app.Skips{},
    "userId": func() uint {
        return auth.ID
    },
})

if err != nil {
    return nil, excp.BadRequest(err)
}

result := db.Create(article)
if result.Error != nil {
    return nil, excp.BadRequest(result.Error, "添加失败")
}
```

#### 关于 `app.Rule`

```go
type Rule map[string]any
```
> string, 代表模型的字段, 有时也代表 req 的字段
>
> any, 分为 string, map, struct, func 四大类

```
string 时, 代表 req 的字段

map 时, 代表 app.Rule 本身, 意思是又有一套 Rule 规则, 处理模型中的模型数据, 实现一个递归处理, 理论上无限深层处理.

struct 时, 有严格规定的格式, 如下:
struct{
    Args []string
    Func func (title string) string // 函数定义, 根据实际变化万千
}{
    Args: []string{"title"},
    Func: func(title string) string {
        return title
    }
}
这样做, 参数可以有多个或没有.

func 时, 其实实现的功能和 struct 一样, 只是函数定义, 参数有固定格式
func (req any) string {}
func () string {}
即, 参数有一个或没有.
```

> 为什么函数处理要这么麻烦, 主要是因为函数反射后, 会丢失参数名, 也就是说, 函数反射运行时, 只保留类型, 反射调用时, 将参数以顺序方式放入切片, 最后传入调用的函数中.

#### 关于 `"_skips": app.Skips{},`

规则映射赋值, 分为仅仅模式和跳过模式.

仅仅模式: 就是数据赋值时, 仅赋值 app.Rule 内定义的字段, 没有定义的字段将使用类型的零值, 在不加 "_skips" 时, 这就是默认模式.
跳过模式: 就是数据赋值时, 根据模型内定义的所有字段, 依次从 req 中取同名字段的值, 如果是 app.Skips{"title"}, 则跳过 title , 赋值其它字段.

### 面向数据处理, Out 处理

```go
data := article.Out(app.Rule{
    "_skips": app.Skips{},
})
```
> 处理规则与 In 一致
>
> 返回的数据格式默认是 map[string]any
>
> 数据键的字符串格式受 env 环境的 RES_KEY_TYPE 影响.

## 稍微全一些的数据处理示例

```go
out, err := user.Out(app.Rule{
    "_skips": app.Skips{"password", "deleted_at"}, // 跳过密码和删除时间, 转换其它字段
    "user_info": app.Rule{
        "nickname": "Nickname",
        "avatar": struct { // 处理头像, 添加 app.url
            Args []string
            Func func(avatar string) string
        }{
            Args: []string{"avatar"},
            Func: func(avatar string) string {
                return config.App.Url + avatar
            },
        },
    },
    "categories": app.Rule{
        "name": "Name",
        "children": app.Rule{
            "name": "Name",
            "articles": app.Rule{
                "title": "Title",
                "comments": app.Rule{
                    "content": "Content",
                    "user": app.Rule{
                        "username": "Username",
                        "nickname": func(user models.TestUser) string { // 处理 nickname
                            return *user.UserInfo.Nickname
                        },
                        "avatar": func(user models.TestUser) string {
                            return fmt.Sprintf("%s%s", config.App.Url, *user.UserInfo.Avatar)
                        },
                    },
                    "children": app.Rule{
                        "content": "Content",
                        "user": app.Rule{
                            "username": "Username",
                            "nickname": func(user models.TestUser) string {
                                return *user.UserInfo.Nickname
                            },
                            "avatar": func(user models.TestUser) string {
                                return fmt.Sprintf("%s%s", config.App.Url, *user.UserInfo.Avatar)
                            },
                        },
                    },
                },
            },
        },
    },
    "articles": app.Rule{
        "title":   "Title",
        "content": "Content",
        "comments": app.Rule{
            "content": "Content",
            "user": app.Rule{
                "username": "Username",
                "nickname": func(user models.TestUser) string {
                    return *user.UserInfo.Nickname
                },
                "avatar": func(user models.TestUser) string {
                    return fmt.Sprintf("%s%s", config.App.Url, *user.UserInfo.Avatar)
                },
            },
            "children": app.Rule{
                "content": "Content",
                "user": app.Rule{
                    "username": "Username",
                    "nickname": func(user models.TestUser) string {
                        return *user.UserInfo.Nickname
                    },
                    "avatar": func(user models.TestUser) string {
                        return fmt.Sprintf("%s%s", config.App.Url, *user.UserInfo.Avatar)
                    },
                },
            },
        },
    },
    "comments": app.Rule{
        "content": "Content",
        "parent": app.Rule{
            "user": app.Rule{
                "username": "Username",
                "nickname": func(user models.TestUser) string {
                    return *user.UserInfo.Nickname
                },
                "avatar": func(user models.TestUser) string {
                    return fmt.Sprintf("%s%s", config.App.Url, *user.UserInfo.Avatar)
                },
            },
        },
        "article": app.Rule{
            "id":    "ID",
            "title": "title",
            "user": app.Rule{
                "username": "Username",
                "nickname": func(user models.TestUser) string {
                    return *user.UserInfo.Nickname
                },
                "avatar": func(user models.TestUser) string {
                    return fmt.Sprintf("%s%s", config.App.Url, *user.UserInfo.Avatar)
                },
            },
        },
    },
})
```