---
title: Production Release
url: "/en/single/production-release"
---

## Using Gradle for Simple Release

Find `gower-work/${your-project}/Tasks/prod/ReleaseProd` in the subproject and double-click to run it.

## Normal Release

Publishing to production requires uploading project files to the production server and then logging into the server to execute Docker builds.

Therefore, the server environment has certain requirements, including the installation of the following software:

> docker >= v20.10
>
> docker compose >= v2.0

### Run the release script

```shell
$ ./docker/release-prod
```


#### Steps

- Build the frontend
- Execute tests
- Clean up temporary files from dev runs
- Cross-platform build
- Upload to the production server

### Log in to the server and run the build script

```shell
$ cd /go/bin
$ chmod +x docker/run.sh
$ ./docker/run.sh
```


#### Steps

- Clean up containers
- Start tests

> After uploading, you need to log in to the server using an SSH tool to execute the build. In the future, we will integrate a tool into the local script to run tests with one command.

## Full Release

```shell
$ ./docker/release-prod-full
```

> Upload source code to the server

### Log in to the server

```shell
$ cd /go/src
$ chmod +x docker/run-prod-full.sh
$ ./docker/run-prod-full.sh
```


## Simple Release

```shell
$ ./cmd/release-prod
```


### Log in to the server

```shell
$ cd /go/bin
$ chmod +x cmd/run.sh
$ ./cmd/run.sh
```
