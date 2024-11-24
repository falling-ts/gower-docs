---
title: Advanced Data Processing
url: "/en/single/advanced-data-processing"
---

Model, is the center of the results from the source data; the database can be considered as the ultimate destination of the source data.

Conversely, the database and model are the sources of frontend data.

## Procedural vs Data-Oriented

Procedural, refers to processing data field by field, using traditional assignment to transfer source data to target data.

> **Advantages**: Simple and direct, good performance, suitable for clear logic and small data volumes.
>
> **Disadvantages**: When the data volume increases, processing becomes cumbersome and maintenance becomes increasingly difficult.

Data-oriented, refers to mapping rules to transfer source data to target data.

> **Advantages**: The data structure is clear, the logic is straightforward, and it can handle both large and small data volumes.
>
> **Disadvantages**: It takes time to understand and use, and there is a certain performance overhead.

## Model

We can analogize a model to a house, where each piece of data has its own seat inside.

At the entrance, there are two guards: one guard processes incoming data, let's call him In; the other guard processes outgoing data, let's call him Out.

Assume this house is named Article, which is a model for storing articles.

The structure is as follows:

```go
type Article struct {
    Model
    Title      string  `gorm:"type:string;default:'';not null;commit:标题"`
    Content    *string `gorm:"type:text;commit:内容"`
    
    // Belongs to category
    CategoryID *uint
    Category   *TestCategory `gorm:"foreignKey:CategoryID"`
    
    // Belongs to author
    UserID     *uint
    User       *TestUser     `gorm:"foreignKey:UserID"`
    
    // Article comments
    Comments   []TestComment `gorm:"foreignKey:ArticleID"`
}
```


Assume there is a controller method for adding an article:

```go
func (*ArticleController) Store(req *requests.ArticleRequest, article *models.Article, auth *models.Auth) (services.Response, error) {
    // TODO: Process and save data
    
    return res.Ok("添加成功")
}
```


> The `req` data has already been validated, all required fields are present.

### Procedural Processing

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


### Data-Oriented Processing, In Handling

Let's see how to handle data in a data-oriented manner.

In this case, the In guard will place the data from `req` into the corresponding seats inside the house.

```go
article, err := article.In(req, app.Rule{
    "title": "title", // Whether the first letter is capitalized or not doesn't matter, it will be automatically converted to uppercase internally.
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


#### About `app.Rule`

```go
type Rule map[string]any
```

> `string`, represents the model field, sometimes also the `req` field.
>
> `any`, can be divided into four categories: `string`, `map`, `struct`, and `func`.

```go
When `string`, it represents the `req` field.

When `map`, it represents another `app.Rule` itself, meaning there is another set of rules to process nested model data, allowing for recursive processing theoretically to any depth.

When `struct`, it has a strictly defined format as follows:
struct{
    Args []string
    Func func (title string) string // Function definition, varies widely based on actual needs
}{
    Args: []string{"title"},
    Func: func(title string) string {
        return title
    }
}
This allows for multiple or no parameters.

When `func`, it achieves the same functionality as `struct`, but with a fixed parameter format:
func (req any) string {}
func () string {}
That is, it can have one or no parameters.
```


> Why is function handling so complex? Mainly because after reflection, the function loses its parameter names. That is, during runtime reflection, only types are retained. When the function is called via reflection, parameters are placed in a slice in order and then passed to the function.

#### About `"_skips": app.Skips{},`

Rule mapping assignment can be either in "only" mode or "skip" mode.

Only mode: During data assignment, only the fields defined in `app.Rule` are assigned, and fields not defined will use the zero value of their type. This is the default mode when `_skips` is not added.
Skip mode: During data assignment, fields are taken from `req` according to all fields defined in the model. If `app.Skips{"title"}` is used, `title` is skipped, and other fields are assigned.

### Data-Oriented Processing, Out Handling

```go
data := article.Out(app.Rule{
    "_skips": app.Skips{},
})
```

> The processing rules are consistent with In.
>
> The returned data format is `map[string]any` by default.
>
> The format of the string keys in the data is influenced by the environment variable `RES_KEY_TYPE`.

## A More Comprehensive Data Processing Example

```go
out, err := user.Out(app.Rule{
    "_skips": app.Skips{"password", "deleted_at"}, // Skip password and deletion time, convert other fields
    "user_info": app.Rule{
        "nickname": "Nickname",
        "avatar": struct { // Process avatar, add app.url
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
                        "nickname": func(user models.TestUser) string { // Process nickname
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
