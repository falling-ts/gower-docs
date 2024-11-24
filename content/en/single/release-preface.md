---
title: Release Preface
url: "/en/single/release-preface"
---
Sure, here is the translation:

---

## Release Categories

```yaml
Development Release
Test Release
Production Release
```


The corresponding environment files for the frontend are:

```yaml
.env.dev
.env.test
.env.prod
```


The corresponding environment files for the backend are:

```yaml
envs/.env.dev
envs/.env.test
envs/.env.prod
```


## Using Gradle for Release

Find the `gradle.properties` file in the subproject, configure the compilation environments for the three environments, and provide the root connection information for the server.

```env
; Skip Go tests, uncomment to not run go test
; skipTest = 1

; Build program name, after running gower create my-project, it will automatically change to bin = my-project
bin = gower

; Development environment
dev.npmBuildEnv = dev
; Development Go test tags
dev.goTestTags = tmpl,static
; Development Go build tags
dev.goBuildTags = tmpl,static
; Disable cgo in development environment
dev.cgoEnabled = 0
; Development environment system
dev.goos = windows
; Development environment architecture
dev.goarch = amd64

; Test environment
test.npmBuildEnv = test
; Test Go test tags
test.goTestTags = test,tmpl,static
; Test Go build tags
test.goBuildTags = test,tmpl,static
; Disable cgo in test environment
test.cgoEnabled = 0
; Test environment system
test.goos = linux
; Test environment architecture
test.goarch = amd64
; Test environment SSH host
test.host = 192.168.101.101
; Test environment SSH port
test.port = 22
; Test environment SSH user
test.user = root
; Test environment SSH password
test.password = root

; Production environment
prod.npmBuildEnv = prod
; Production Go test tags
prod.goTestTags = prod,tmpl,static
; Production Go build tags
prod.goBuildTags = prod,tmpl,static
; Disable cgo in production environment
prod.cgoEnabled = 0
; Production environment system
prod.goos = linux
; Production environment architecture
prod.goarch = amd64
; Production environment SSH host
prod.host = 192.168.101.101
; Production environment SSH port
prod.port = 22
; Production environment SSH user
prod.user = root
; Production environment SSH password
prod.password = root

```


## Using Command Line Rclone for Release

Rclone is a file synchronization and transfer tool implemented in Go. It can upload the code for the test and production versions to the corresponding servers.

It also supports detecting and uploading modified files, meaning that after the first upload, only modified files will be uploaded in subsequent uploads, greatly improving work efficiency.

### Installation and Usage

Run the script to install and configure Rclone.

```shell
./cmd/init-rclone
```


The output will be as follows:

```yaml
---------------- rclone installing... ----------------
go: downloading github.com/rclone/rclone v1.62.2
go: downloading github.com/buengese/sgzip v0.1.1
go: downloading github.com/gabriel-vasile/mimetype v1.4.2
go: downloading github.com/Max-Sum/base32768
.
.
.
---------------- rclone installed ----------------
rclone v1.62.2-DEV
- os/version: Microsoft Windows 11 Pro 21H2 (64 bit)
- os/kernel: 10.0.22000.1696 Build 22000.1696.1696 (x86_64)
- os/type: windows
- os/arch: amd64
- go/version: go1.20
- go/linking: static
- go/tags: none
---------------- please create test and prod ssh server ----------------
No remotes found, make a new one?
n) New remote
s) Set configuration password
q) Quit config
n/s/q> 
```


### Configure test and prod Remotes

#### Input `n`
#### Input name test
#### Select 39 SSH server type
#### Input server IP
#### Input login account root
#### Input SSH port 22
#### Input password or select xxx.pem
#### Press Enter to create the test remote
#### Then input `n` to create the prod remote

#### The result will be as follows:
```yaml
Current remotes:

Name                 Type
====                 ====
prod                 sftp
test                 sftp

e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q>
```
