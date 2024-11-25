---
title: Development Release
url: "/en/single/development-release"
---


## Running Development with Gradle

Find the `gower-work/${your-project}/Tasks/dev/Run` in the subproject and double-click to run it.

## Running Configuration with GoLand, Supporting Breakpoint Debugging

Find the Gower Run configuration, modify the working directory, select the module, and finally run in Debug mode to enable breakpoint debugging.

## Normal Release

```shell
$ ./docker/run-dev
```

> For Windows, use run-dev.cmd
>
> For Linux, use run-dev.sh

`cmd content`

```shell
@echo off

echo ---------------- build static... ----------------
call npm run dev

echo ---------------- go test... ----------------
go test -tags tmpl,static
REM go test -bench=Benchmark -tags tmpl,static

echo ---------------- go build ----------------
SET CGO_ENABLED=0
SET GOOS=linux
SET GOARCH=amd64

go build -o gower -tags tmpl,static

echo ---------------- clean docker... ----------------
docker compose down

echo ---------------- start dev ----------------
docker compose up -d --build gower

echo ---------------- tail -f dev log ----------------
docker logs -f gower

```


### Steps

- Build frontend
- Run tests
- Cross-platform packaging
- Stop running gower
- Rebuild and run gower
- Print runtime logs

### Files Used

```yaml
docker-compose.yaml
./docker/Dockerfile
```


## Full Release

```shell
$ ./docker/run-dev-full
```


### Steps

- Build frontend
- Stop running dev-full
- Rebuild and run dev-full
- Print runtime logs

> Packaging and testing tasks are included in the build and run of dev-full.

### Files Used

```yaml
docker-compose.yaml
./docker/Docker-development-full
./docker/entrypoint-dev-full.sh
```

> entrypoint-dev-full.sh is the entry file for Docker to run.
