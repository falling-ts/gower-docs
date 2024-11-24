---
title: logger-日志服务
url: "/single/logger-日志服务"
---

## 使用的开源库

```yaml
"go.uber.org/zap"
```

## 服务提供者

### 服务标识符

```yaml
"logger"
```

### 注册服务

```go
P.Register("logger", func() (Depends, Resolve) {
    return Depends{"config", "util"}, func(ss ...services.Service) services.Service {
        return logger.New().Init(ss...)
    }
})
```

### 注册服务格式见 auth 服务

## 依赖的服务

```yaml
"config", "util"
```

## ENV 配置

```yaml
# 三种模式: development, production, test
APP_MODE=development


# 日志保存目录, 默认 storage/logs
LOG_DIR=storage/logs
# 日志模式, 默认 stack, 包含 stack flat-day day hour
LOG_CHANNEL=flat-day
# 不保存日志的路由, 用英文逗号分割, 后缀自动加 *, 用以匹配子路由
LOG_SKIP_PATHS="GET /"
# 保存日志的路由, 用英文逗号分割, 后缀自动加 *, 用以匹配子路由
LOG_PATHS="GET /test"
# 日志消息 Key
LOG_MSG_KEY=msg
# 日志级别 Key
LOG_LEVEL_KEY=level
# 日志时间 Key
LOG_TIME_KEY=ts
# 日志名称 Key
LOG_NAME_KEY=logger
# 日志调用文件和行号 Key
LOG_CALLER_KEY=caller
# 日志错误调用栈 Key
LOG_STACK_KEY=stack
# 日志时间格式, 参照 time 包下面 format.go
LOG_TIME_FORMAT="2006-01-02 15:04:05"
# 日志中时间段显示格式: seconds, nanos, millis, string
LOG_DURATION_FORMAT=seconds
# 控制台日志显示的分隔符, 不填为制表符
LOG_CONSOLE_SEP=", "
```
> 日志纪录, 分为四大类: 控制台普通日志, 控制台错误日志, 文件普通日志, 文件错误日志.
>
> 文件普通日志, 根据日期分割以 gower-xxxx-xx-xx.log 格式命名, 如果是 day, hour 多层日期目录分割, 则以 xxxx/xx/xx.log 命名.
>
> 文件错误日志, 根据日期分割以 error-xxxx-xx-xx.log 格式命名.

### 关于模式与日志等级的关系

#### development 开发模式

就是控制台 debug 模式, 文件 debug 模式; 意思指: 控制台和文件都会输出 debug 信息

#### test 开发模式

就是控制台 info 模式, 文件 debug 模式; 意思指: 控制台输出 info 以上级别信息, 文件输出 debug 信息

#### production 生产模式

就是控制台 info 模式, 文件 info 模式; 意思指: 控制台和文件只输出 info 以上级别的信息

> info 以上级别, 包含 warn error panic 等错误级别

## 服务接口方法

```go
// LoggerService 日志服务
type LoggerService interface {
    Service // 通用服务接口

    // 用于创建一个可写入日志记录的实例. Check 方法需要指定一个日志记录的级别和消息文本, 并返回一个 CheckedEntry 实例, 该实例提供了一个接口来添加日志记录的字段、堆栈跟踪等信息.
    Check(lvl zapcore.Level, msg string) *zapcore.CheckedEntry
    
    // 用于获取当前日志记录器的底层核心日志记录器.
    Core() zapcore.Core

    // 用于输出调试级别的日志. 当您需要在调试代码时记录一些额外信息时, 可以使用 Debug 方法. 与 Info 和 Error 等方法不同, Debug 方法输出的日志记录通常不会在生产环境中记录.
    Debug(msg string, fields ...zap.Field)
    
    // 用于记录一条带有堆栈跟踪的 panic 级别的日志记录, 并引发 panic 异常. 与 Panic 方法不同, DPanic 方法记录的日志记录中包含堆栈跟踪, 这使得在生产环境中排查问题变得更加容易.
    DPanic(msg string, fields ...zap.Field)
    
    // 用于输出错误级别的日志. 当您需要记录一些出现错误的信息时, 可以使用 Error 方法. 与 Info 和 Debug 等方法不同, Error 方法输出的日志记录通常会在生产环境中记录, 并且通常会引起关注.
    Error(msg string, fields ...zap.Field)
    
    // 用于记录一条致命错误的日志记录, 并使程序在记录日志记录之后立即退出. 与 Error 方法不同, Fatal 方法会强制结束当前进程, 因此应该谨慎使用.
    Fatal(msg string, fields ...zap.Field)
    
    // 用于输出信息级别的日志. 当您需要记录一些有用的信息时, 可以使用 Info 方法. 与 Debug 和 Error 等方法不同, Info 方法输出的日志记录通常会在生产环境中记录, 并且通常不会引起关注.
    Info(msg string, fields ...zap.Field)
    
    // 用于获取当前日志记录器的日志级别. 日志级别指定了要记录的最低日志级别, 例如 Debug、Info 或 Error.
    Level() zapcore.Level
    
    // 用于记录指定级别的日志记录. 您可以使用 Log 方法来输出任何级别的日志记录, 包括 Debug、Info、Error 等级别.
    Log(lvl zapcore.Level, msg string, fields ...zap.Field)
    
    // 用于创建一个新的日志记录器, 该日志记录器具有指定的名称. 命名记录器可用于创建日志记录器的层次结构, 以便在记录器之间共享配置和组织日志记录.
    Named(s string) *zap.Logger
    
    // 用于记录一条 panic 级别的日志记录, 并引发 panic 异常. 与 Fatal 方法不同, Panic 方法不会强制结束当前进程, 而是只引发一个 panic 异常, 这使得您可以在 panic 异常中捕获和处理程序状态.
    Panic(msg string, fields ...zap.Field)

    // 用于创建一个新的 SugaredLogger, 该对象包装了当前日志记录器的实例, 并允许您轻松地使用结构化日志记录 API.
    Sugar() *zap.SugaredLogger
    
    // 用于刷新任何还未写入的日志缓冲区并等待所有日志写入操作完成. 如果不调用 Sync 方法, 则可能会出现丢失日志记录或程序在退出时崩溃的情况.
    Sync() error

    // 用于创建一个新的日志记录器, 该记录器使用指定的配置选项. 配置选项包括日志级别、输出格式、时间戳格式、堆栈跟踪的级别等.
    WithOptions(opts ...zap.Option) *zap.Logger
    
    // 用于创建一个新的日志记录器, 该记录器具有指定的上下文字段. 这些上下文字段将与所有后续的日志记录一起记录, 并可以在日志记录中使用.
    With(fields ...zap.Field) *zap.Logger
    
    // 用于记录一条 warn 级别的日志记录.
    Warn(msg string, fields ...zap.Field)

    // 获取 zap logger
    Zap() *zap.Logger

    // 获取 DB 的日志输出器
    DB() DBLogger
}
```