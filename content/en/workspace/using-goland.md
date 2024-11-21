---
title: Using GoLand
url: "/en/workspace/using-goland"
---

## GoLand Plugins to Install

- Make sure to install the Gradle plugin in GoLand in advance.
- Install JDK 23 or higher and set the Gradle JVM to JDK 23.
- Recommended GoLand plugins to install:
  ```shell
  .env files support
  PostCSS
  Stylus
  Tailwind CSS
  Vite
  Vue.js
  ```


## Using GoLand to Open the `gower-work` Project

- When opening `gower-work` with GoLand for the first time, a prompt will appear saying `Found Gradle 'gower-work' build script`. Click `Load Gradle project` to initialize the Gradle build system.
- Find `Settings/GO/GOPATH/Project GOPATH` and add the absolute path of the `gower-work` directory.
  - This ensures that when you run `go install` within the project, the binary will be automatically installed in the `bin` directory.
  - The `bin` directory under the project will also be added to the environment variables of the built-in terminal, making it easier to execute commands.

> If you encounter an error saying `go list` cannot find the `go.mod` file, you can ignore and close the prompt.
