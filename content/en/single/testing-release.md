---
title: Testing Release
url: "/en/single/testing-release"
---

### Release Testing

To release the project files to the test server, you need to upload them to the server and then log in to execute the Docker build.

Therefore, the server environment has certain requirements and needs to have the following software installed:

> docker >= v20.10
>
> docker compose >= v2.0

## Normal Release

### Run the Release Script

```shell
$ ./docker/release-test
```


#### Steps

- Build the frontend
- Execute tests
- Clean up temporary files from dev runs
- Cross-platform build
- Upload to the test server

### Log in to the Server and Run the Build Script

```shell
$ cd /go/bin
$ chmod +x docker/run.sh
$ ./docker/run.sh
```


#### Steps

- Clean up containers
- Start testing

> After uploading, you need to use an SSH tool to log in to the server to execute the build. In the future, we will integrate a tool into the local script to run tests with one command.

## Full Release

```shell
$ ./docker/release-test-full
```

> Upload source code to the server

### Log in to the Server

```shell
$ cd /go/src
$ chmod +x docker/run-test-full.sh
$ ./docker/run-test-full.sh
```


## Simple Release

```shell
$ ./cmd/release-test
```


### Log in to the Server

```shell
$ cd /go/bin
$ chmod +x cmd/run.sh
$ ./cmd/run.sh
```


## Using Gradle for Simple Release

Find the sub-project `gower-work/${your-project}/Tasks/test/ReleaseTest` and double-click to run it.