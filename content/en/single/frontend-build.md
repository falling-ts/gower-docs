---
title: Frontend Build
url: "/en/single/frontend-build"
---

If you want to develop a non-separated front-end and back-end application, use the front-end build tools to build your JS and CSS.

If it's a separated front-end and back-end system, then you won't need the front-end part of the entire system.

## Frontend API

Define your request interfaces in `resources/app/api`. The frontend requests use the `fetch` method by default to send asynchronous requests.

Here is the v1 API interface:

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


The `client` library is already encapsulated, handling error message toasts, redirecting to login on 401 authorization errors, and automatically setting tokens. Therefore, you don't need to worry about these details when using it.

## Defining Your Own Interfaces

Assume you are defining a v2 interface. After exporting the actual methods, you need to import v2 in `resources/app/api/index.ts` as follows:

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


`main.ts` packages the entire `index.ts` object, so you can use it directly in templates.

Important: Before using, make sure to build with `npm run dev`.

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


> Because it's easy to forget to build, when using Docker, the chosen script will first build the frontend to prevent low-level bugs caused by forgetting to build the frontend.

## Message and Confirm

The library files are already defined. You can use them directly with the following methods:

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


## About Imports

To import functional modules, you need to export these modules in `main.ts`, as shown below:

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


## Frontend Libraries

### Vite

A build tool for Vue 3, very user-friendly. The `vite.config.js` file in the root directory is already configured, so you generally don't need to modify it during development.

### Cross-env

A universal environment setting tool. During the build process, it sets the appropriate `NODE_ENV` environment (dev, test, prod) to select the corresponding `.env` file for building.

### TailwindCSS

A utility-first CSS framework that makes writing styles smooth and efficient.

### PostCSS, Autoprefixer

TailwindCSS is installed via PostCSS plugins.

### DaisyUI

A set of frontend components for TailwindCSS, supporting many themes.

### Simplebar

An elegant scrollbar.

### Animate.css

Pure CSS animations.

### Others

jQuery, Vue, LocalForage, js-cookie, etc.

All of these components have their respective official websites. If you are not familiar with them, you can search for them easily. I recommend using cn.bing for searching.