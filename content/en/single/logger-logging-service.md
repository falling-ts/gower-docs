---
title: logger-Logging Service
url: "/en/single/logger-logging-service"
---

## Open Source Libraries Used

```yaml
"go.uber.org/zap"
```


## Service Provider

### Service Identifier

```yaml
"logger"
```


### Register Service

```go
P.Register("logger", func() (Depends, Resolve) {
    return Depends{"config", "util"}, func(ss ...services.Service) services.Service {
        return logger.New().Init(ss...)
    }
})
```


### Service Registration Format See auth Service

## Dependent Services

```yaml
"config", "util"
```


## ENV Configuration

```yaml
# Three modes: development, production, test
APP_MODE=development


# Log save directory, default storage/logs
LOG_DIR=storage/logs
# Log mode, default stack, including stack flat-day day hour
LOG_CHANNEL=flat-day
# Routes not to log, separated by commas, suffix automatically adds *, to match sub-routes
LOG_SKIP_PATHS="GET /"
# Routes to log, separated by commas, suffix automatically adds *, to match sub-routes
LOG_PATHS="GET /test"
# Log message key
LOG_MSG_KEY=msg
# Log level key
LOG_LEVEL_KEY=level
# Log time key
LOG_TIME_KEY=ts
# Log name key
LOG_NAME_KEY=logger
# Log caller file and line number key
LOG_CALLER_KEY=caller
# Log error call stack key
LOG_STACK_KEY=stack
# Log time format, refer to time package format.go
LOG_TIME_FORMAT="2006-01-02 15:04:05"
# Log duration display format: seconds, nanos, millis, string
LOG_DURATION_FORMAT=seconds
# Console log display delimiter, default is tab
LOG_CONSOLE_SEP=", "
```


> Log records are divided into four categories: console normal logs, console error logs, file normal logs, and file error logs.
>
> File normal logs are split by date in the format gower-xxxx-xx-xx.log. If day or hour multi-level date directories are used, they are named as xxxx/xx/xx.log.
>
> File error logs are split by date in the format error-xxxx-xx-xx.log.

### Relationship Between Modes and Log Levels

#### Development Mode

Console debug mode, file debug mode; meaning: both console and file output debug information.

#### Test Mode

Console info mode, file debug mode; meaning: console outputs info and above level information, file outputs debug information.

#### Production Mode

Console info mode, file info mode; meaning: both console and file only output info and above level information.

> Info and above levels include warn, error, panic, etc.

## Service Interface Methods

```go
// LoggerService logging service
type LoggerService interface {
    Service // General service interface

    // Used to create an instance that can write log records. The Check method specifies a log record level and message text, and returns a CheckedEntry instance, which provides an interface to add fields, stack traces, etc., to the log record.
    Check(lvl zapcore.Level, msg string) *zapcore.CheckedEntry
    
    // Used to get the underlying core logger of the current logger.
    Core() zapcore.Core

    // Used to output debug-level logs. When you need to record additional information while debugging code, you can use the Debug method. Unlike Info and Error methods, Debug method logs are typically not recorded in production environments.
    Debug(msg string, fields ...zap.Field)
    
    // Used to record a panic-level log with a stack trace and trigger a panic exception. Unlike the Panic method, DPanic includes a stack trace, making it easier to troubleshoot issues in production environments.
    DPanic(msg string, fields ...zap.Field)
    
    // Used to output error-level logs. When you need to record some error information, you can use the Error method. Unlike Info and Debug methods, Error method logs are typically recorded in production environments and often draw attention.
    Error(msg string, fields ...zap.Field)
    
    // Used to record a fatal error log and immediately terminate the program after logging. Unlike the Error method, the Fatal method forces the process to exit, so it should be used with caution.
    Fatal(msg string, fields ...zap.Field)
    
    // Used to output info-level logs. When you need to record useful information, you can use the Info method. Unlike Debug and Error methods, Info method logs are typically recorded in production environments and do not usually draw attention.
    Info(msg string, fields ...zap.Field)
    
    // Used to get the current logger's log level. The log level specifies the minimum log level to be recorded, such as Debug, Info, or Error.
    Level() zapcore.Level
    
    // Used to record a log at a specified level. You can use the Log method to output logs at any level, including Debug, Info, Error, etc.
    Log(lvl zapcore.Level, msg string, fields ...zap.Field)
    
    // Used to create a new logger with a specified name. Named loggers can be used to create a hierarchy of loggers to share configurations and organize logs.
    Named(s string) *zap.Logger
    
    // Used to record a panic-level log and trigger a panic exception. Unlike the Fatal method, the Panic method does not force the process to exit but instead triggers a panic exception, allowing you to catch and handle the program state in the panic exception.
    Panic(msg string, fields ...zap.Field)

    // Used to create a new SugaredLogger, which wraps the current logger instance and allows you to easily use the structured logging API.
    Sugar() *zap.SugaredLogger
    
    // Used to flush any unwritten log buffers and wait for all log writes to complete. If the Sync method is not called, log records may be lost or the program may crash when exiting.
    Sync() error

    // Used to create a new logger with specified configuration options. Configuration options include log level, output format, timestamp format, stack trace level, etc.
    WithOptions(opts ...zap.Option) *zap.Logger
    
    // Used to create a new logger with specified context fields. These context fields will be included in all subsequent log records and can be used in log records.
    With(fields ...zap.Field) *zap.Logger
    
    // Used to record a warn-level log.
    Warn(msg string, fields ...zap.Field)

    // Get zap logger
    Zap() *zap.Logger

    // Get DB logger output
    DB() DBLogger
}
```
