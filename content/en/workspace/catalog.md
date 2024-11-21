---
title: Catalog
url: "/en/workspace/catalog"
---

```go
- bin/ // When executing `go install` in the source code, it will be installed in this directory
- gradle/ // Gradle wrapper directory
    - wrapper/ // Wrapper JAR files directory
- pkg/ // Directory for storing third-party dependencies of the source code
- src/ // Project source code directory
- build.gradle // Main Gradle build script
- go.work // Go workspace configuration file
- gradle.properties // Gradle configuration file
- gradlew // Gradle wrapper script [Unix]
- gradlew.bat // Gradle wrapper script [Windows]
- settings.gradle // Gradle sub-module configuration file
```


> Directories are indicated by a trailing `/`.
>
> Important directories or files are marked with an asterisk (`*`).