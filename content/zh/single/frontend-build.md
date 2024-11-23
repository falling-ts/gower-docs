---
title: 前端构建
url: "/single/前端构建"
---

> 如果你想开发非前后端分离的, 就使用前端构建工具, 构建你的 js 和 css 吧
>
> 如果是前后分离的, 那么整套系统的前端部分, 你是用不到的.

## 前端接口

在 `resources/app/api` 定义你的请求接口, 前端请求默认使用 fetch 方法, 发送异步请求.

下面是 api v1 的请求接口

```typescript
import client from "../lib/client";

interface Data {
    test: string
    name: string
}

async function hello(data: Data, option: RequestInit = {}) {
    return await client.get("/api/v1/hello", data, option)
}

async function image(data: FormData, option: RequestInit = {}) {
    return await client.post("/api/v1/upload/image", data, option)
}

export default {
    hello,
    image
}

```

`client` 库都已经封装好, 有关错误信息 toast, 授权 401 跳转登录, 自动 token 设置. 所以你实际使用时, 无需关心这些.

## 定义自己的接口

假设定义一个 v2 的接口, 在导出实际方法后, 需要在 `resources/app/api/index.ts` 导入 v2, 如下:

```typescript
import auth from "./auth"
import v1 from "./v1"
import v2 from "./v2"

export default {
    auth,
    v1,
    v2
}

```

`main.ts` 打包了整个 index.ts 的对象, 所以可以在模板上直接使用

重点: 使用前, 先打包 `npm run dev`

`resources/views/home/index.tmpl`
```html
{{define "home/index"}}
.
.
.
{{template "foot" .}}
<script type="module">
    import { createApp, api, msg, cookie } from "/static/main.js"
    const { auth, v1, v2 } = api
    
    async myFunc() {
        const res = await v2.someMethod()
        if (!res.code) {
            msg.success(res.msg)
        }
    }
</script>

{{end}}
```

> 就是因为总是忘记打包, 所以在使用 Docker 时, 会根据选择的运行脚本的类别, 首先打包前端. 防止因为忘记打包前端, 造成人为低级 Bug.

## message 与 confirm

库文件已经定义好了, 直接通过一下方法使用

```javascript
import { msg } from "/static/main.js"

let message = "my message"
msg.default(message)
msg.info(message)
msg.success(message)
msg.warning(message)
msg.error(message)

msg.confirm("ok?", async () => {
    msg.success("ok")
})
```

## 关于使用的导入

导入功能模块, 需要在 main.ts 中导出这个模块, 如下所示

```typescript
export {
    createApp,
    $,
    api,
    msg,
    cookie,
    store
}
```

## 前端使用的库

### vite

vue3 的打包工具, 非常好用, 根目录的 `vite.config.js` 已经配置好了, 基本开发时不需要修改的.

### cross-env

通用环境设置工具, 在打包时根据 dev test prod 三类设置相应 NODE_ENV 环境, 从而选择相应 .env 文件, 进行打包.

### tailwindcss

顺风 css, 顾名思义写样式, 写的很顺.

### postcss, autoprefixer

通过 postcss 插件安装了 tailwindcss.

### daisyui

tailwindcss 的前端组件, 支持老多主题了

### simplebar

优美的滚动条

### animate.css

纯 css 实现的动画效果

### 其它

jquery, vue, localforage, js-cookie 等

以上组件都有相应官网, 我这边就不贴地址了, 不了解的, 一搜就搜到了, 建议用 cn.bing 搜索, 没别的意思, 纯干净舒服.