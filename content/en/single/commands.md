---
title: Commands
url: "/en/single/commands"
---

## Used Open Source Libraries

```yaml
github.com/urfave/cli/v2
```


## Command Source Code Location

`app/consoles`

## Usage

```shell
$ gower
```


Outputs the following content:

```yaml
NAME:
   gower - Command line tool.

USAGE:
   An auxiliary command tool for development, installed using go install in the project root directory.

VERSION:
   v0.2.1

AUTHOR:
   Falling TS <zgh.yuanshang@gmail.com>

COMMANDS:
   create, c  Create a project
   init, i    Initialize application content
   jwt, j     Operate various JWT parameters
   make, m    Initialize and create files
   run, r     Start the application
   help, h    Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --help, -h     Show help
   --version, -v  Output software version. (default: false)

COPYRIGHT:
   (c) 2023 falling ts
```


## Essence

Essentially, it is a system application package, and the commands are its core components.

As shown above, you can run the HTTP service using `gower run`.

In other words, the HTTP service is just one of the commands.

## Command Mode Packaging

The command mode is a simplified mode because if everything is packaged, running the `gower` command would generate some log and temporary files in the directory, which is not what we want.

Therefore, the command mode removes services such as route, logger, and db.

```shell
$ go install -tags cli
or
$ go build -tags cli
```


## Detailed Help for Subcommands

For example, the `run` subcommand:

```shell
$ gower run help
```


Outputs:

```yaml
NAME:
   gower run - Start the application

USAGE:
   gower run [command options] [arguments...]

OPTIONS:
   --port value, -p value  Start the application and listen on the specified port. (default: "8080")
   --help, -h              Show help
```


> You can see that the command supports specifying a port, for example, binding to port 8000.

```shell
$ gower run --port 8000
```

