使用log库
=========

Go语言的 `log` 库用于记录错误、警告和其他重要信息。

## 创建新项目

```bash
mkdir goapp
cd goapp
go mod init goapp

echo 'package main

import "fmt"

func main() {
	fmt.Println("Hello, World!")
}' > main.go

go run main.go
```

## Go 语言 log 库

### 1. 基本日志

首先，你可以使用 `log` 包的 `Println` 函数来打印一条日志消息。

```go
package main

import (
	"log"
)

func main() {
	log.Println("这是一条普通的日志信息")
}
```

### 2. 改变日志前缀

你可以使用 `SetPrefix` 函数来更改日志的前缀。

```go
package main

import (
	"log"
)

func main() {
	log.SetPrefix("INFO: ")
	log.Println("这是一条信息日志")
}
```

### 3. 输出到文件

你还可以将日志输出到一个文件中。

```go
package main

import (
	"log"
	"os"
)

func main() {
	file, err := os.OpenFile("info.log", os.O_CREATE|os.O_APPEND|os.O_WRONLY, 0644)
	if err != nil {
		log.Fatal(err)
	}
	defer file.Close()

	log.SetOutput(file)
	log.Println("这条日志会被写入文件")
}
```

### 4. 自定义日志格式

你可以使用 `log.New` 创建一个自定义的日志器，以便更灵活地控制日志的格式和行为。

```go
package main

import (
	"log"
	"os"
)

func main() {
	file, err := os.OpenFile("info.log", os.O_CREATE|os.O_APPEND|os.O_WRONLY, 0644)
	if err != nil {
		log.Fatal(err)
	}
	defer file.Close()

	customLogger := log.New(file, "CUSTOM: ", log.LstdFlags|log.Lshortfile)
	customLogger.Println("这是一条自定义的日志")
}
```

上述代码将创建一个新的日志器，并设置前缀、日期、时间和调用源文件的信息。

### 5. 致命错误

使用 `log.Fatal` 或 `log.Fatalf` 可以记录错误信息并结束程序。


```go
package main

import (
	"log"
)

func main() {
	log.Fatal("这是一条致命错误，程序将终止")
}
```

### 6. 日志级别的设置

Go的标准库中并没有直接提供日志级别的设置，但你可以通过自定义结构来实现类似功能。以下是一个简单的例子，它定义了一个包含三个不同级别的日志器的结构体。

```go
package main

import (
	"log"
	"os"
)

type MyLogger struct {
	Info  *log.Logger
	Warn  *log.Logger
	Error *log.Logger
}

func main() {
	file, err := os.OpenFile("info.log", os.O_CREATE|os.O_APPEND|os.O_WRONLY, 0644)
	if err != nil {
		log.Fatal(err)
	}
	defer file.Close()

	myLogger := MyLogger{
		Info:  log.New(file, "INFO: ", log.LstdFlags),
		Warn:  log.New(file, "WARN: ", log.LstdFlags),
		Error: log.New(file, "ERROR: ", log.LstdFlags),
	}

	myLogger.Info.Println("这是一条信息日志")
	myLogger.Warn.Println("这是一条警告日志")
	myLogger.Error.Println("这是一条错误日志")
}
```

### 7. 控制调用者信息的输出

你可以使用 `log.Lshortfile` 或 `log.Llongfile` 来控制调用者信息的输出。

```go
package main

import (
	"log"
)

func main() {
	log.SetFlags(log.LstdFlags | log.Lshortfile)
	log.Println("这条日志会显示短文件名和行号")

	log.SetFlags(log.LstdFlags | log.Llongfile)
	log.Println("这条日志会显示完整路径和行号")
}
```

### 8. 同时将日志输出到文件和控制台

你可以使用 `io.MultiWriter` 将日志同时输出到多个地方。

```go
package main

import (
	"io"
	"log"
	"os"
)

func main() {
	file, err := os.OpenFile("info.log", os.O_CREATE|os.O_APPEND|os.O_WRONLY, 0644)
	if err != nil {
		log.Fatal(err)
	}
	defer file.Close()

	multiWriter := io.MultiWriter(file, os.Stdout)
	log.SetOutput(multiWriter)
	log.Println("这条日志会被写入文件并打印到控制台")
}
```

### 9. 使用第三方库来增强日志功能

有时，标准的 `log` 库可能不满足所有需求。这时，你可以考虑使用第三方库，例如 `logrus`。以下是一个使用 `logrus` 的例子：

```go
package main

import (
	"github.com/sirupsen/logrus"
)

func main() {
	log := logrus.New()
	log.WithFields(logrus.Fields{
		"animal": "walrus",
		"size":   10,
	}).Info("A group of walrus emerges from the ocean")
}
```

### 10. 使用 `log.Panic` 和 `log.Panicf`

`log.Panic` 和 `log.Panicf` 函数用于在打印日志消息后调用 `panic` 函数。这可以在不结束程序的情况下触发错误，并允许通过 `recover` 函数进行恢复。

```go
package main

import "log"

func main() {
	defer func() {
		if r := recover(); r != nil {
			log.Println("Recovered from panic:", r)
		}
	}()
	
	log.Panic("This is a panic message")
}
```

### 11. 通过环境变量控制日志级别

有时，你可能希望根据环境变量来控制日志的级别。虽然Go的标准 `log` 库没有提供直接的支持，但你可以通过以下方式实现：

```go
package main

import (
	"log"
	"os"
)

func main() {
	logLevel := os.Getenv("LOG_LEVEL")

	switch logLevel {
	case "ERROR":
		log.Println("ERROR level logging is enabled")
	case "INFO":
		log.Println("INFO level logging is enabled")
	default:
		log.Println("Default logging is enabled")
	}
}
```

### 12. 自定义日志输出格式

通过实现 `log.Logger` 的 `Output` 方法，你可以完全控制日志的格式。

```go
package main

import (
	"fmt"
	"log"
	"time"
)

type customLog struct{}

func (l *customLog) Write(p []byte) (n int, err error) {
	fmt.Print(time.Now().Format(time.RFC3339), " [CUSTOM] ", string(p))
	return len(p), nil
}

func main() {
	logger := log.New(&customLog{}, "", 0)
	logger.Println("This is a custom log message")
}
```

这个例子中的自定义日志器会在每条日志消息前添加当前的日期和时间，并使用 [CUSTOM] 作为前缀。

### 13. 使用第三方库设置日志级别

使用第三方库如 logrus 可以更容易地实现日志级别的控制。

```go
package main

import (
	"github.com/sirupsen/logrus"
)

func main() {
	log := logrus.New()
	log.SetLevel(logrus.WarnLevel)

	log.Info("This is an info message")  // 不会被打印
	log.Warn("This is a warning message") // 会被打印
}
```

通过使用 SetLevel 方法，你可以控制哪些级别的日志消息被打印。

### 14. 使用不同的日志级别

虽然Go的标准库没有直接支持日志级别，但你可以通过封装多个日志器来模拟不同的日志级别。

```go
package main

import (
	"log"
	"os"
)

type LogLevel int

const (
	DEBUG LogLevel = iota
	INFO
	WARN
	ERROR
	FATAL
)

type Logger struct {
	Debug *log.Logger
	Info  *log.Logger
	Warn  *log.Logger
	Error *log.Logger
	Fatal *log.Logger
}

func NewLogger(logLevel LogLevel) *Logger {
	flags := log.Ldate | log.Ltime
	logger := &Logger{
		Debug: log.New(os.Stdout, "DEBUG: ", flags),
		Info:  log.New(os.Stdout, "INFO: ", flags),
		Warn:  log.New(os.Stdout, "WARN: ", flags),
		Error: log.New(os.Stdout, "ERROR: ", flags),
		Fatal: log.New(os.Stdout, "FATAL: ", flags),
	}

	switch logLevel {
	case INFO:
		logger.Debug.SetOutput(os.Stderr)
	case WARN:
		logger.Debug.SetOutput(os.Stderr)
		logger.Info.SetOutput(os.Stderr)
	case ERROR:
		logger.Debug.SetOutput(os.Stderr)
		logger.Info.SetOutput(os.Stderr)
		logger.Warn.SetOutput(os.Stderr)
	case FATAL:
		logger.Debug.SetOutput(os.Stderr)
		logger.Info.SetOutput(os.Stderr)
		logger.Warn.SetOutput(os.Stderr)
		logger.Error.SetOutput(os.Stderr)
	}

	return logger
}

func main() {
	logger := NewLogger(INFO)
	logger.Debug.Println("This message won't be printed")
	logger.Info.Println("This message will be printed")
}
```

### 15. 创建一个支持不同日志级别和格式的日志库

你可以创建一个自定义的日志库，支持不同的日志级别和自定义的格式。

```go
package mylog

import (
	"fmt"
	"log"
	"os"
)

type Logger struct {
	level int
}

const (
	DEBUG = iota
	INFO
	WARN
	ERROR
)

func (l *Logger) Log(level int, format string, v ...interface{}) {
	if level < l.level {
		return
	}
	msg := fmt.Sprintf(format, v...)
	prefix := ""
	switch level {
	case DEBUG:
		prefix = "DEBUG"
	case INFO:
		prefix = "INFO"
	case WARN:
		prefix = "WARN"
	case ERROR:
		prefix = "ERROR"
	}
	log.Printf("[%s] %s\n", prefix, msg)
}

func NewLogger(level int) *Logger {
	return &Logger{level: level}
}

// 使用示例
func main() {
	logger := mylog.NewLogger(mylog.INFO)
	logger.Log(mylog.DEBUG, "Debug message: %d", 42) // 这条不会打印
	logger.Log(mylog.INFO, "Info message: %s", "hello") // 这条会打印
}
```

### 16. 使用日志钩子

第三方库如 `logrus` 允许你添加钩子，以便在日志事件发生时执行自定义操作。

```go
package main

import (
	"github.com/sirupsen/logrus"
)

type MyHook struct{}

func (hook *MyHook) Levels() []logrus.Level {
	return logrus.AllLevels
}

func (hook *MyHook) Fire(entry *logrus.Entry) error {
	// 在这里执行自定义操作，例如发送告警通知等
	return nil
}

func main() {
	log := logrus.New()
	log.AddHook(&MyHook{})

	log.Info("This will trigger the custom hook")
}
```

### 17. 使用结构化日志

有时，你可能希望以结构化的格式记录日志，例如JSON。`logrus` 库提供了此类功能。

```go
package main

import (
	"github.com/sirupsen/logrus"
)

func main() {
	log := logrus.New()
	log.SetFormatter(&logrus.JSONFormatter{})

	log.WithFields(logrus.Fields{
		"animal": "walrus",
		"size":   10,
	}).Info("A group of walrus emerges from the ocean")
}
```

### 18. 使用自定义时间格式

你可以通过实现自定义的Writer来控制日志的时间格式。

```go
package main

import (
	"fmt"
	"log"
	"time"
)

type customWriter struct{}

func (cw *customWriter) Write(p []byte) (n int, err error) {
	timestamp := time.Now().Format("2006-01-02 15:04:05")
	fmt.Printf("%s %s", timestamp, p)
	return len(p), nil
}

func main() {
	logger := log.New(&customWriter{}, "", 0)
	logger.Println("This log entry has a custom timestamp format.")
}
```

### 19. 记录HTTP请求的中间件

你可以使用日志库来创建一个记录HTTP请求的中间件。

```go
package main

import (
	"log"
	"net/http"
	"time"
)

func loggingMiddleware(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		start := time.Now()
		next.ServeHTTP(w, r)
		log.Printf("%s %s %s", r.Method, r.RequestURI, time.Since(start))
	})
}

func main() {
	http.Handle("/", loggingMiddleware(http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		w.Write([]byte("Hello, World!"))
	})))

	log.Fatal(http.ListenAndServe(":8080", nil))
}
```

### 20. 使用日志上下文

在某些情况下，你可能希望在请求的整个生命周期内共享某些日志信息。你可以使用 `context` 包来实现。

```go
package main

import (
	"context"
	"log"
)

type key int

const requestIDKey key = 0

func logRequestID(ctx context.Context, msg string) {
	reqID, ok := ctx.Value(requestIDKey).(string)
	if ok {
		log.Printf("%s (Request ID: %s)", msg, reqID)
	} else {
		log.Print(msg)
	}
}

func main() {
	ctx := context.WithValue(context.Background(), requestIDKey, "12345")
	logRequestID(ctx, "This log entry has a request ID.")
}
```

### 21. 使用日志过滤器

有时，你可能希望根据特定条件过滤日志条目。你可以通过实现自定义的Writer来做到这一点。

```go
package main

import (
	"bytes"
	"log"
)

type filterWriter struct{}

func (fw *filterWriter) Write(p []byte) (n int, err error) {
	if bytes.Contains(p, []byte("filter me")) {
		return len(p), nil
	}
	log.Print(string(p))
	return len(p), nil
}

func main() {
	logger := log.New(&filterWriter{}, "", 0)
	logger.Println("This log entry will be printed.")
	logger.Println("This log entry contains 'filter me' and will not be printed.")
}
```

### 22. 使用第三方库集成其他服务

许多第三方日志库（例如logrus）可以通过钩子或插件与其他服务集成，例如将日志发送到远程服务器或集中日志系统。

例如，你可以使用 `logrus` 的 `logstash` 钩子将日志发送到 `Logstash`。

```go
package main

import (
	"github.com/bshuster-repo/logrus-logstash-hook"
	"github.com/sirupsen/logrus"
)

func main() {
	log := logrus.New()
	hook, err := logrustash.NewHook("tcp", "logstash.example.com:5000", "")
	if err != nil {
		log.Fatal(err)
	}
	log.Hooks.Add(hook)
	log.Info("This log entry will be sent to Logstash.")
}
```

### 23. 集成第三方监控服务

你可以使用特定的钩子或插件集成第三方监控服务，例如Datadog或Prometheus。以下是使用logrus与Datadog集成的示例：

```go
package main

import (
	"github.com/sirupsen/logrus"
	"gopkg.in/DataDog/dd-trace-go.v1/ddtrace/tracer"
)

func main() {
	// 启动Datadog跟踪
	tracer.Start()
	defer tracer.Stop()

	log := logrus.New()

	// 使用Datadog进行监控
	// 在这里添加与Datadog集成的代码

	log.Info("This log entry will be monitored by Datadog.")
}
```

### 24. 使用环境变量配置日志

你可以使用环境变量来配置日志的输出和行为，使其更容易在不同的环境中运行。

```go
package main

import (
	"log"
	"os"
)

func main() {
	logLevel := os.Getenv("LOG_LEVEL")

	if logLevel == "DEBUG" {
		log.SetFlags(log.Ldate | log.Ltime | log.Lshortfile)
	}

	log.Println("This log entry may include additional debug information.")
}
```

### 25. 使用日志记录性能度量

你可以使用日志记录性能度量和基准测试结果。

```go
package main

import (
	"log"
	"time"
)

func main() {
	start := time.Now()
	// 执行一些工作
	time.Sleep(100 * time.Millisecond)
	duration := time.Since(start)

	log.Printf("Operation completed in %v", duration)
}
```

### 26. 实现异步日志记录

在高性能应用程序中，你可能希望异步记录日志以减小I/O的影响。以下是一个使用通道实现的简单异步日志记录器示例：

```go
package main

import (
	"log"
)

func logger(ch <-chan string) {
	for msg := range ch {
		log.Println(msg)
	}
}

func main() {
	ch := make(chan string, 100)
	go logger(ch)

	ch <- "This log entry is sent asynchronously."
	close(ch)
}
```

### 27. 使用结构化日志进行分析

使用结构化日志（例如JSON）可以方便后续的分析和处理。

```go
package main

import (
	"github.com/sirupsen/logrus"
)

func main() {
	log := logrus.New()
	log.SetFormatter(&logrus.JSONFormatter{})

	log.WithFields(logrus.Fields{
		"event": "login",
		"user":  "john_doe",
	}).Info("User login event")
}
```

### 28. 与分布式追踪系统集成

在微服务架构中，你可能希望将日志与分布式追踪系统集成。一些第三方库和服务，如Jaeger或Zipkin，可以实现这一点。

```go
package main

import (
	"github.com/uber/jaeger-client-go"
	"github.com/uber/jaeger-client-go/config"
	"log"
)

func main() {
	// 配置Jaeger
	cfg, err := config.FromEnv()
	if err != nil {
		log.Fatalf("Could not initialize jaeger tracer: %s", err.Error())
		return
	}
	tracer, closer, err := cfg.NewTracer()
	if err != nil {
		log.Fatalf("Could not initialize jaeger tracer: %s", err.Error())
		return
	}
	defer closer.Close()

	// 在这里添加你的代码和日志逻辑
}
```

### 29. 使用第三方库轻松迁移到不同的日志系统

有些第三方库，例如 `zap`，通过提供更高的抽象层，使你能够更容易地在不同的日志系统之间迁移。以下是一个使用`zap`的基本示例：

```go
package main

import (
	"go.uber.org/zap"
)

func main() {
	logger, err := zap.NewProduction()
	if err != nil {
		log.Fatalf("Can't initialize zap logger: %v", err)
	}
	defer logger.Sync() // flushes buffer, if any

	sugar := logger.Sugar()
	sugar.Infow("Log message with zap",
		"URL", "http://example.com",
		"attempt", 3,
	)
}
```

### 30. 使用日志记录敏感操作

你可以使用日志来记录系统中的敏感操作，以便进行审计和分析。

```go
package main

import (
	"log"
)

func main() {
	// 假设这是一个敏感操作，例如用户权限更改
	log.Println("User permissions were changed by Admin")
}
```

### 31. 使用动态日志级别

你可以通过信号或特定的HTTP端点来动态更改日志级别。以下是一个示例，显示了如何使用HTTP端点更改日志级别：

```go
package main

import (
	"log"
	"net/http"
	"os"
)

var logger *log.Logger

func setLogLevel(w http.ResponseWriter, r *http.Request) {
	level := r.URL.Query().Get("level")
	switch level {
	case "DEBUG":
		logger.SetFlags(log.Ldate | log.Ltime | log.Lshortfile)
		w.Write([]byte("Set log level to DEBUG"))
	default:
		logger.SetFlags(log.Ldate | log.Ltime)
		w.Write([]byte("Set log level to default"))
	}
}

func main() {
	logger = log.New(os.Stdout, "", log.Ldate|log.Ltime)

	http.HandleFunc("/setLogLevel", setLogLevel)
	http.ListenAndServe(":8080", nil)
}
```

通过访问 http://localhost:8080/setLogLevel?level=DEBUG，你可以动态更改日志级别。

### 32. 使用日志记录长时间运行的操作

你可以使用日志记录长时间运行的操作，以便分析性能和监控进度。

```go
package main

import (
	"log"
	"time"
)

func longRunningTask() {
	start := time.Now()
	log.Println("Long running task started")

	// 模拟长时间运行的任务
	time.Sleep(5 * time.Second)

	log.Printf("Long running task completed in %v", time.Since(start))
}

func main() {
	longRunningTask()
}
```

### 33. 创建可插拔的日志接口

如果你的应用程序或库需要与多个日志系统兼容，你可以创建一个可插拔的日志接口。

```go
package main

import "log"

type Logger interface {
	Log(message string)
}

type StdLogger struct{}

func (l *StdLogger) Log(message string) {
	log.Println(message)
}

func main() {
	var logger Logger
	logger = &StdLogger{}
	logger.Log("This is a pluggable log message.")
}
```

这种方法允许你轻松切换不同的日志实现，而不必更改大量代码。

### 34. 使用日志记录重要的业务指标

你可以使用日志来记录关键业务指标，如订单数量、用户登录次数等。

```go
package main

import "log"

func main() {
	// 假设这是一个订单处理函数
	ordersProcessed := 100
	log.Printf("Processed %d orders today", ordersProcessed)
}
```

通过记录这些指标，你可以使用日志分析工具来监视业务健康状况和性能。

### 35. 使用日志库实现本地化

你可以使用日志库和本地化工具一起记录本地化消息。

```go
package main

import (
	"log"
	"golang.org/x/text/language"
	"golang.org/x/text/message"
)

func main() {
	p := message.NewPrinter(language.English)
	log.Println(p.Sprintf("There are %d users online", 5))
}
```

Done.
