---
title: 初始化项目
url: "/workspace/初始化项目"
---

### 新建一个 Gower 项目

- 在 `gower-work` 项目根目录终端上, 执行:
```shell
$ gower create my-project
```
- 运行结果如下:
```shell
src\my-project 项目创建成功
---------------- 初始化 APP 密钥...:
APP 密钥生成成功
---------------- 初始化 JWT 密钥...:
JWT 密钥生成成功
---------------- 下载 Go 依赖包...:
---------------- 下载前端依赖包...:
Lockfile is up to date, resolution step is skipped
......
+ @types/vue 2.0.0
+ @vitejs/plugin-vue 4.0.0
+ cross-env 7.0.3
+ vite 5.4.6

Done in 1.2s
---------------- 初始化 Git 仓库...: 
Initialized empty Git repository in D:/Go/gower-work/src/my-project/.git/
---------------- 添加所有文件...: 
---------------- 初始化 commit...: 
......
create mode 100644 utils/slice/strings.go
create mode 100644 utils/str/str.go
create mode 100644 vite.config.js
---------------- 构建前端库文件...:
......
public/static/main.umd.cjs    640.00 kB │ gzip: 211.13 kB
✓ built in 4.88s
npm notice
npm notice New minor version of npm available! 10.7.0 -> 10.9.0
npm notice Changelog: https://github.com/npm/cli/releases/tag/v10.9.0
npm notice To update run: npm install -g npm@10.9.0
npm notice
---------------- 添加工作目录...:
---------------- 执行基准测试...: 
2023-10-17 12:48:37, ERROR, Gower, Error Trace, {"error": "record not found", "sql": "SELECT * FROM `admin
_users` WHERE `admin_users`.`id` = 1 AND `admin_users`.`deleted_at` IS NULL ORDER BY `admin_users`.`id` LIMIT 1", "elapsed": 0, "rows": 0}
......
Benchmark/BenchmarkEncode-12             1896417               628.9 ns/op
Benchmark/BenchmarkDecode-12             3177018               389.5 ns/op
PASS
ok      my-project      25.313s
```

### 运行项目

- 在 gradle 任务中找到 `my-project/Tasks/dev/Run`, 双击运行
- 运行结果如下:
```shell
......
> Task :my-project:t0300GoBuild
# go build -tags tmpl,static -o my-project.exe

> Task :my-project:Run
# my-project.exe run
http://localhost:8080

BUILD SUCCESSFUL in 10s
6 actionable tasks: 6 executed
00:57:32: 执行完成 'Run'。
```
- 点击 `http://localhost:8080` 会打开浏览器, 并访问项目
