---
title: Using Gradle
url: "/en/workspace/using-gradle"
---

## Gradle Task Configuration

- The build tasks for a Gradle project are directly written in the `build.gradle` file, making it easy to customize and modify.
- It is recommended to write related properties and parameters for Gradle builds in the `gradle.properties` file.

## Usage of SSH Remote File Deployment Plugin

- The plugin used is:

```gradle
plugins {
    id "org.hidetake.ssh" version "2.11.2"
}
```

> If you are using workspace mode, the `build.gradle` file of sub-projects should not reintroduce `org.hidetake.ssh`.
