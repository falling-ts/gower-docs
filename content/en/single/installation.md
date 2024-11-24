---
title: Installation
url: "/en/single/installation"
---

## Before Use, Please Install System Environment Requirements

> go >= v1.20
>
> nodejs >= v16.13
>
> pnpm >= v7.0
>
> docker >= v20.10
>
> docker compose >= v2.0
>
> git >= 2.39
>
> gradle == 8.10.2
>
> jdk >= 23

## Workspace Mode

[Workspace Mode - Installation](https://falling-ts.github.io/gower-docs/workspace/安装/)

## Single Project Mode

### Install the gower Command

```shell
$ go install -tags cli github.com/falling-ts/gower@latest
```


### Create a Project

```shell
$ gower create my-project
```


### Run in Docker

```shell
$ cd my-project
$ ./docker/run-dev
```


### Run with Gradle

- Install the gradle plugin in GoLand beforehand.
- Modify `build.gradle`, remove the comments from the beginning of the plugin references.
- When opening `my-project` in GoLand for the first time, it will prompt you with `Found Gradle 'my-project' build script`, then click `Load Gradle project` to initialize the gradle build system.
- Finally, find Run under dev in the right-hand gradle tasks and run it.

### Access `http://localhost:8080`

## Git Installation

### Download Project Files

```shell
$ git clone -b v0.6.0 --single-branch --depth 1 https://github.com/falling-ts/gower.git
or
$ git clone -b v0.6.0 --single-branch --depth 1 https://gitee.com/falling-ts/gower.git
```


### Install Dependencies and Command-line Tools

```shell
$ pnpm install
$ go mod tidy
$ go install -tags cli
```


### Frontend Environment

In the root directory, copy out two frontend environment files: `.env.test` and `.env.prod`.

### Backend Environment

In the `envs/` directory, copy out two backend environment files: `.env.test` and `.env.prod`.

### Generate Keys

```shell
$ gower init key
$ gower jwt key
```


### Install Executable Program

```shell
$ go test
$ go install
```


### Or Run Directly in Docker

```shell
$ ./docker/run-dev
```
