---
title: Initializing Project
url: "/en/workspace/initializing-project"
---

### Create a New Gower Project

- In the terminal at the root directory of the `gower-work` project, execute:
```shell
$ gower create my-project
```

- The output will be as follows:
```shell
src\my-project project created successfully
---------------- Initializing APP key...:
APP key generated successfully
---------------- Initializing JWT key...:
JWT key generated successfully
---------------- Downloading Go dependencies...:
---------------- Downloading frontend dependencies...:
Lockfile is up to date, resolution step is skipped
......
+ @types/vue 2.0.0
+ @vitejs/plugin-vue 4.0.0
+ cross-env 7.0.3
+ vite 5.4.6

Done in 1.2s
---------------- Initializing Git repository...: 
Initialized empty Git repository in D:/Go/gower-work/src/my-project/.git/
---------------- Adding all files...: 
---------------- Initial commit...: 
......
create mode 100644 utils/slice/strings.go
create mode 100644 utils/str/str.go
create mode 100644 vite.config.js
---------------- Building frontend library files...:
......
public/static/main.umd.cjs    640.00 kB │ gzip: 211.13 kB
✓ built in 4.88s
npm notice
npm notice New minor version of npm available! 10.7.0 -> 10.9.0
npm notice Changelog: https://github.com/npm/cli/releases/tag/v10.9.0
npm notice To update run: npm install -g npm@10.9.0
npm notice
---------------- Adding working directory...:
---------------- Running benchmark tests...: 
2023-10-17 12:48:37, ERROR, Gower, Error Trace, {"error": "record not found", "sql": "SELECT * FROM `admin
_users` WHERE `admin_users`.`id` = 1 AND `admin_users`.`deleted_at` IS NULL ORDER BY `admin_users`.`id` LIMIT 1", "elapsed": 0, "rows": 0}
......
Benchmark/BenchmarkEncode-12             1896417               628.9 ns/op
Benchmark/BenchmarkDecode-12             3177018               389.5 ns/op
PASS
ok      my-project      25.313s
```


### Run the Project

- In the Gradle tasks, find `my-project/Tasks/dev/Run`, and double-click to run it.
- The output will be as follows:
```shell
......
> Task :my-project:t0300GoBuild
# go build -tags tmpl,static -o my-project.exe

> Task :my-project:Run
# my-project.exe run
http://localhost:8080

BUILD SUCCESSFUL in 10s
6 actionable tasks: 6 executed
00:57:32: Execution completed 'Run'.
```

- Clicking on `http://localhost:8080` will open the browser and access the project.