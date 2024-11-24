---
title: Directory Structure
url: "/en/single/directory-structure"
---

```go
app // Core package
|--api // API directory
|  |--controllers // API controllers
|  |--middlewares // API middlewares
|  |--requests // API request validators
|--consoles // Command-line source code
|--exceptions // Exception service mounting content
|--http // Web directory
|  |--controllers // Web controllers
|  |--middlewares // Web middlewares
|  |--requests // Web request validators
|--middlewares // Common middlewares
|--models // Models
|--providers // Service providers
|--responses // Response service mounting content
|--app.go // Core struct
|--controller.go // Common controller struct
|--model.go // Common model data handler
|--request.go // Common request validator
bootstrap // Bootstrap package
|--0 // Control route startup, due to the import order change to ASCII sorting in go1.21, custom import sorting is needed using number directories
|--1 // Control template library startup, due to the import order change to ASCII sorting in go1.21, custom import sorting is needed using number directories
cmd // Non-Docker related run and build
configs // Configuration package, configuration service mounting content
docker // Docker related run and build
envs // Backend environment package
public // Static resource package, also the root directory of the Web service software
resources // Template library
|--app // Frontend root directory
|  |--api // Frontend API
|  |--lib // Frontend library
|  |--styles // Frontend style source files
|  |--main.ts // Frontend build entry file
|--lang // For future multi-language functionality
|--views // HTML templates
routes // Route configuration
services // Services
|--auth // JWT service
|--cache // Cache service
|--config // Configuration service
|--cookie // Cookie service
|--db // Database service
|--exception // Exception service
|--logger // Logging service
|--passwd // Password hash service
|--response // Response service
|--route // Route service
|--symcrypt // Symmetric encryption service
|--translate // Translation service
|--upload // Upload service
|--util // Utility functions service
|--validator // Validator service
storage // Local storage
|--app // Project static files and uploaded files
|--caches // Persistent caching
|--logs // Logs
tests // Test package
|--benchmarks // Benchmark test package
third_apps // Third-party applications
|--caddy // Web server
|  |--config // Server configuration
|  |--data // Server data, CA certificates, root certificates
|  |--site // Site, use public directory in actual usage
|--grafana // Log viewing tool, access localhost:3000
|--loki // Grafana data source
|--mysql5.7 // Database
|--promtail // Local logs push driver
|--tidb // Database
trans // Translation package
utils // General base type handling package
```
