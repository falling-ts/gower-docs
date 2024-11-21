---
title: Introduce
url: "/en/workspace/introduce"
---

Go/Gin Gower Workspace Directory is a workspace mode implemented by combining Go language with GOPATH.

## Go Workspace

The `go.work` file is a new feature introduced in Go 1.18, used to manage multiple modules (modules) within a workspace. It allows you to develop and test multiple modules simultaneously within a single workspace without adding these modules as dependencies to each module's `go.mod` file.

### Workspace Concepts
- **Workspace**: A directory containing multiple Go modules.
- **`go.work` File**: The configuration file that defines the workspace, specifying which modules belong to this workspace.

### `go.work` File Structure
```go
go 1.18

use (
    ./src/module1
    ./src/module2
)
```


- **`go 1.18`**: Specifies the Go version.
- **`use`**: Lists the paths of the modules included in the workspace.

### Creating a `go.work` File
- Use the command `go work init` to create a new `go.work` file and specify initial modules.
  ```sh
  $ go work init
  ```


- This will generate a `go.work` file.

### Adding and Removing Modules
- **Adding Modules**: Use the `go work use` command to add new modules to the `go.work` file.
  ```sh
  $ go work use src/module1
  ```

> Note: Before adding, the `src/module1` project already has a `go.mod` file.

- **Removing Modules**: Use the `go work remove` command to remove modules from the `go.work` file.
  ```sh
  $ go work remove src/module1
  ```


## GOPATH

`GOPATH` is an important environment variable in the Go language, used to specify the default working directory for Go projects. It is primarily used to manage the organization, building, and dependencies of Go code. Here are the details about `GOPATH`:

### 1. Concept of GOPATH
- **GOPATH**: An environment variable that specifies the default working directory for Go projects.
- **Working Directory**: Under this directory, Go looks for and manages source code, binary files, and package objects.

### 2. Structure of GOPATH
The `GOPATH` directory typically contains three subdirectories:
- **`src`**: Stores source code files.
- **`pkg`**: Stores third-party dependency packages.
- **`bin`**: Stores compiled executable files.

### 3. Setting GOPATH

In GoLand, you can set the project-level `GOPATH` in `Settings` -> `Go` -> `GOPATH`.

## Combined Usage

When Workspace and GOPATH are used together, `go mod tidy` will download dependency packages to the `pkg/mod` directory. `go install` will install the executable files in the `bin` directory by default.
