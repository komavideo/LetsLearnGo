Go Echo Web 开发
================

Echo 是 Go 语言中的一个高性能、简洁的 web 框架。

## 网站

https://echo.labstack.com/

## Go Echo Web 开发

### 安装 echo

```bash
go get -u github.com/labstack/echo/v4
```

### Hello World

```go
package main

import (
	"net/http"
	"github.com/labstack/echo/v4"
)

func main() {
	e := echo.New()

	e.GET("/", func(c echo.Context) error {
		return c.String(http.StatusOK, "Hello, World!")
	})

	e.Start(":8080")
}
```

### 路由参数

```go
e.GET("/users/:id", func(c echo.Context) error {
	id := c.Param("id")
	return c.String(http.StatusOK, "User ID: "+id)
})
```

### 查询参数

```go
e.GET("/show", func(c echo.Context) error {
	name := c.QueryParam("name")
	return c.String(http.StatusOK, "Hello, "+name)
})
```

### POST 请求和表单参数

```go
e.POST("/save", func(c echo.Context) error {
	name := c.FormValue("name")
	return c.String(http.StatusOK, "Hello, "+name)
})
```

### JSON 响应

```go
type User struct {
	Name  string `json:"name"`
	Email string `json:"email"`
}

e.GET("/users", func(c echo.Context) error {
	u := &User{
		Name:  "John",
		Email: "john@example.com",
	}
	return c.JSON(http.StatusOK, u)
})
```

### 组路由

```go
adminGroup := e.Group("/admin")
adminGroup.GET("/dashboard", func(c echo.Context) error {
	return c.String(http.StatusOK, "This is the admin dashboard.")
})
```

### 中间件

```go
// 日志和恢复中间件
e.Use(middleware.Logger())
e.Use(middleware.Recover())
```

### 静态文件服务
要为 `public` 目录下的静态文件创建一个路由，你可以这样做：

```go
e.Static("/static", "public")
```
此时，你可以通过 `/static/js/main.js` 或 `/static/css/style.css` 等路径来访问静态文件。

### 模板渲染
首先，需要设置一个模板引擎，例如使用 `Go` 的标准 `html/template` 包：

```go
package main

import (
	"html/template"
	"io"
	"github.com/labstack/echo/v4"
)

type TemplateRenderer struct {
	templates *template.Template
}

func (t *TemplateRenderer) Render(w io.Writer, name string, data interface{}, c echo.Context) error {
	return t.templates.ExecuteTemplate(w, name, data)
}

func main() {
	e := echo.New()

	renderer := &TemplateRenderer{
		templates: template.Must(template.ParseGlob("views/*.html")),
	}
	e.Renderer = renderer

	e.GET("/hello", func(c echo.Context) error {
		return c.Render(http.StatusOK, "hello", "world")
	})

	e.Start(":8080")
}
```
其中 `views/hello.html` 是一个简单的模板文件。

### 绑定请求数据
`Echo` 提供了直观的 `API` 来绑定请求数据到你的数据结构：

```go
type User struct {
	Name  string `json:"name" form:"name" query:"name"`
	Email string `json:"email" form:"email" query:"email"`
}

e.POST("/users", func(c echo.Context) error {
	u := new(User)
	if err := c.Bind(u); err != nil {
		return err
	}
	return c.JSON(http.StatusOK, u)
})
```

### 中间件
除了上面提到的日志和恢复中间件，还有许多有用的中间件，例如：

+ CORS: 跨域资源共享。
```go
e.Use(middleware.CORS())
```
+ Secure: 提供各种安全特性。
```go
e.Use(middleware.Secure())
```
+ JWT: JSON Web Token 认证。
```go
e.Use(middleware.JWTWithConfig(middleware.JWTConfig{
	SigningKey: []byte("secret"),
}))
```

### 自定义中间件
你可以轻松地为你的应用编写自定义的中间件：

```go
func ServerHeader(next echo.HandlerFunc) echo.HandlerFunc {
	return func(c echo.Context) error {
		c.Response().Header().Set(echo.HeaderServer, "Echo/3.0")
		return next(c)
	}
}

e.Use(ServerHeader)
```

### WebSockets
`Echo` 同样支持 `WebSockets`。这是一个简单的 `Echo WebSocket` 例子：

```go
e.GET("/ws", func(c echo.Context) error {
	ws, err := e.Upgrader.Upgrade(c.Response(), c.Request(), nil)
	if err != nil {
		return err
	}
	defer ws.Close()
	for {
		mt, msg, err := ws.ReadMessage()
		if err != nil {
			break
		}
		if err = ws.WriteMessage(mt, msg); err != nil {
			break
		}
	}
	return nil
})
```

### 错误处理
`Echo` 提供了一个中央化的错误处理机制，它对 `HTTP` 错误和自定义错误都很友好：

```go
e.HTTPErrorHandler = func(err error, c echo.Context) {
	code := http.StatusInternalServerError
	if he, ok := err.(*echo.HTTPError); ok {
		code = he.Code
	}
	c.Logger().Error(err)
	c.String(code, http.StatusText(code))
}
```

### 重定向
`Echo` 使重定向变得容易：

```go
e.GET("/old", func(c echo.Context) error {
	return c.Redirect(http.StatusMovedPermanently, "/new")
})
```

### 组中间件
你可以为特定的路由组添加中间件：

```go
admin := e.Group("/admin", middleware.BasicAuth(func(username, password string, c echo.Context) (bool, error) {
	if username == "admin" && password == "password" {
		return true, nil
	}
	return false, nil
}))

admin.GET("/dashboard", func(c echo.Context) error {
	return c.String(http.StatusOK, "Admin Dashboard")
})
```

### 请求预处理
`Echo` 允许你拦截请求并对其进行预处理：

```go
e.Pre(middleware.RemoveTrailingSlash())
```

### 扩展绑定
`Echo` 默认使用 `net/http` 的 `Request.ParseForm()` 进行绑定。但是，你可以扩展它以支持自定义数据源或绑定机制：

```go
type CustomBinder struct{}

func (cb *CustomBinder) Bind(i interface{}, c echo.Context) (err error) {
	// 你的自定义绑定逻辑
	return
}

e.Binder = &CustomBinder{}
```

### 自定义验证
`Echo` 默认使用 `go-playground/validator` 进行验证，但你可以替换它以支持自定义验证器：

```go
type CustomValidator struct{}

func (cv *CustomValidator) Validate(i interface{}) error {
	// 你的自定义验证逻辑
	return nil
}

e.Validator = &CustomValidator{}
```

### 子域名路由
你可以为特定子域名创建路由，这在多租户应用中非常有用：

```go
e.GET("/", func(c echo.Context) error {
	return c.String(http.StatusOK, "Welcome to Echo!")
})

admin := e.Group("admin.")
admin.GET("/", func(c echo.Context) error {
	return c.String(http.StatusOK, "Welcome to Admin!")
})
```
这样，对于 `example.com` 的请求会返回 `"Welcome to Echo!"`，而对于 `admin.example.com` 的请求会返回 `"Welcome to Admin!"`。

### 自定义 HTTP 上下文
你可以扩展 `Echo` 的上下文来添加自定义方法或字段：

```go
type CustomContext struct {
	echo.Context
}

func (c *CustomContext) Foo() {
	// 自定义方法
}

e.Use(func(next echo.HandlerFunc) echo.HandlerFunc {
	return func(c echo.Context) error {
		cc := &CustomContext{c}
		return next(cc)
	}
})
```

### 请求钩子
你可以为特定的路由注册一个钩子，以在路由执行前后运行特定的代码：

```go
e.GET("/", func(c echo.Context) error {
	return c.String(http.StatusOK, "Hello, Echo!")
}).Before(func(c echo.Context) {
	// 在路由处理之前执行的代码
}).After(func(c echo.Context) {
	// 在路由处理之后执行的代码
})
```

### 自定义日志
默认情况下，`Echo` 使用其内置的日志机制，但你可以使用自己的日志实现：

```go
type CustomLogger struct{}

func (l *CustomLogger) Output() io.Writer {
	// 你的实现
	return os.Stdout
}

func (l *CustomLogger) SetOutput(w io.Writer) {
	// 你的实现
}

func (l *CustomLogger) Prefix() string {
	// 你的实现
	return ""
}

func (l *CustomLogger) SetPrefix(p string) {
	// 你的实现
}

func (l *CustomLogger) Level() echo.LogLevel {
	// 你的实现
	return echo.INFO
}

func (l *CustomLogger) SetLevel(v echo.LogLevel) {
	// 你的实现
}

func (l *CustomLogger) Print(i ...interface{}) {
	// 你的实现
	fmt.Println(i...)
}

func (l *CustomLogger) Printf(format string, args ...interface{}) {
	// 你的实现
	fmt.Printf(format, args...)
}

// 同样，你还需要实现其他的日志方法，如 Printj, Debug, Info 等...

e.Logger = &CustomLogger{}
```

### 内建测试助手
`Echo` 提供了一个简单的 `API` 来帮助你测试你的 `HTTP` 服务：

```go
e.GET("/something", func(c echo.Context) error {
	return c.String(http.StatusOK, "Hello, Test!")
})

req := httptest.NewRequest(http.MethodGet, "/something", nil)
rec := httptest.NewRecorder()
e.ServeHTTP(rec, req)
assert.Equal(t, http.StatusOK, rec.Code)
assert.Equal(t, "Hello, Test!", rec.Body.String())
```

### GRPC 支持
`Echo` 可以很容易地与 `gRPC` 服务集成。虽然这超出了简单示例的范围，但你可以查看官方文档或相关教程来深入学习如何将 `Echo` 与 `gRPC` 服务集成。

### 优雅地关闭或重启
`Echo` 提供了优雅地关闭或重启你的 `HTTP` 服务器的功能：

```go
e.Server.Addr = ":8080"

// Start server
go func() {
	if err := e.Start(":8080"); err != nil {
		e.Logger.Info("Shutting down the server")
	}
}()

// Wait for interrupt signal to gracefully shut down the server
quit := make(chan os.Signal)
signal.Notify(quit, os.Interrupt)
<-quit
ctx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
defer cancel()
if err := e.Shutdown(ctx); err != nil {
	e.Logger.Fatal(err)
}
```

### 参数验证
`Echo` 与 `go-playground` 的 `validator` 集成得很好，可以帮助你验证请求参数：

```go
type User struct {
	Name  string `json:"name" validate:"required"`
	Email string `json:"email" validate:"required,email"`
}

e.POST("/users", func(c echo.Context) error {
	u := new(User)
	if err := c.Bind(u); err != nil {
		return err
	}
	if err := c.Validate(u); err != nil {
		return err
	}
	return c.JSON(http.StatusOK, u)
})
```

### 路径参数
`Echo` 支持命名的路径参数，例如：

```go
e.GET("/users/:id", func(c echo.Context) error {
	id := c.Param("id")
	return c.String(http.StatusOK, "User ID: "+id)
})
```

### 路径通配符
可以使用通配符匹配多个路径：

```go
e.GET("/files/*", func(c echo.Context) error {
	path := c.Param("*")
	return c.String(http.StatusOK, "File path: "+path)
})
```

### 自定义数据绑定
要为特定的 `MIME` 类型实现自定义绑定器：

```go
e.Binder = &CustomBinder{}

type CustomBinder struct{}

func (cb *CustomBinder) Bind(i interface{}, c echo.Context) (err error) {
	if c.Request().Header.Get(echo.HeaderContentType) == "application/custom" {
		// Your custom binding logic
	}
	return echo.DefaultBinder{}.Bind(i, c)
}
```

### 数据解组
除了默认的 `JSON` 和 `XML` 解组，`Echo` 还支持 `msgpack`, `protobuf` 等，只需导入相应的包：

```go
import (
	_ "github.com/labstack/echo/v4/middleware"
)

e.GET("/users/:id", func(c echo.Context) error {
	u := &User{}
	return c.JSON(http.StatusOK, u)
})
```

### 响应格式化
你可以将数据格式化为你想要的任何格式：

```go
e.GET("/users/:id", func(c echo.Context) error {
	u := &User{}
	return c.Render(http.StatusOK, "user", u)
})
```

### 封装请求和响应
`Echo` 允许你封装请求和响应对象，以便可以添加自定义方法或字段：

```go
type CustomResponseWriter struct {
	echo.ResponseWriter
}

func (rw *CustomResponseWriter) WriteHeader(code int) {
	rw.ResponseWriter.WriteHeader(code)
	// Custom logic here
}

e.Response().Wrap(func(w http.ResponseWriter) http.ResponseWriter {
	return &CustomResponseWriter{w}
})
```

### 自动 TLS
`Echo` 可以使用 `Let's Encrypt` 自动管理 `TLS` 证书：

```go
e.AutoTLSManager.Cache = autocert.DirCache("/var/www/.cache")
e.Logger.Fatal(e.StartAutoTLS(":443"))
```

### 静态文件服务
`Echo` 提供了方便的方法来为静态文件提供 `HTTP` 服务。以下是一个简单示例：

```go
e.Static("/static", "assets")
```
这行代码将会为 `"assets"` 目录中的文件提供 `HTTP` 服务，可以通过 `"/static"` 路径来访问。

### 模板渲染
`Echo` 支持任何实现了 `http/template` 接口的模板引擎。下面是如何使用 `Go` 的内置模板引擎：

```go
e.Renderer = &TemplateRenderer{
    templates: template.Must(template.ParseGlob("views/*.html")),
}

type TemplateRenderer struct {
    templates *template.Template
}

func (t *TemplateRenderer) Render(w io.Writer, name string, data interface{}, c echo.Context) error {
    return t.templates.ExecuteTemplate(w, name, data)
}

e.GET("/hello", func(c echo.Context) error {
    return c.Render(http.StatusOK, "hello", "World")
})
```
上述代码将渲染一个位于 `"views"` 目录下名为 `"hello.html"` 的模板文件。

### WebSockets
`Echo` 有内置的支持简化 `WebSockets` 的使用：

```go
e.GET("/ws", func(c echo.Context) error {
    ws, err := websocket.Upgrade(c.Response(), c.Request(), nil, 1024, 1024)
    if err != nil {
        return err
    }
    defer ws.Close()

    for {
        mt, msg, err := ws.ReadMessage()
        if err != nil {
            return err
        }
        if err = ws.WriteMessage(mt, msg); err != nil {
            return err
        }
    }
})
```
这是一个简单的 `echo` 服务器，它会将接收到的 `WebSocket` 消息发送回客户端。

### HTTP/2 和 Server Push
`Echo` 支持 `HTTP/2`，并允许你使用 `server push` 功能：

```go
e.GET("/", func(c echo.Context) error {
    pusher, ok := c.Response().Writer.(http.Pusher)
    if ok {
        if err := pusher.Push("/app.js", nil); err != nil {
            return err
        }
    }
    return c.File("index.html")
})
```

### 请求数据绑定和验证
`Echo` 提供了数据绑定和验证的功能，这有助于简化和加速开发过程：

```go
type User struct {
    Name  string `json:"name" validate:"required"`
    Email string `json:"email" validate:"required,email"`
}

e.POST("/users", func(c echo.Context) error {
    user := &User{}
    if err := c.Bind(user); err != nil {
        return err
    }
    if err := c.Validate(user); err != nil {
        return err
    }
    return c.JSON(http.StatusOK, user)
})
```

### 中间件使用顺序
中间件的执行顺序在 `Echo` 中是非常重要的。例如：

```go
e.Use(middleware.Logger())
e.Use(middleware.Recover())
```
在上述代码中，`Logger` 中间件将在 `Recover` 中间件之前运行。这意味着如果你的应用崩溃，`Logger` 将不会记录该请求，因为它发生在恢复之前。所以要注意中间件的顺序。

### JWT 认证
`Echo` 和 `JWT (JSON Web Tokens)` 之间有一个内置的集成。以下是如何使用 `JWT` 进行认证的示例：

```go
e.GET("/restricted", handleRestricted, middleware.JWTWithConfig(middleware.JWTConfig{
    SigningKey: []byte("secret"),
}))
```

### CORS
跨域资源共享 (CORS) 是一个重要的安全特性，`Echo` 为此提供了中间件：

```go
e.Use(middleware.CORSWithConfig(middleware.CORSConfig{
    AllowOrigins: []string{"https://example.com"},
    AllowMethods: []string{http.MethodGet, http.MethodPut, http.MethodPost, http.MethodDelete},
}))
```

### 请求限制
为了避免滥用或攻击，可以使用 `Echo` 的 `RateLimiter` 中间件：

```go
store := middleware.NewRateLimiterMemoryStore(50)
e.Use(middleware.RateLimiterWithConfig(middleware.RateLimiterConfig{
    Store: store,
}))
```

### 压缩响应内容
你可以使用中间件自动压缩 `HTTP` 响应内容：

```go
e.Use(middleware.GzipWithConfig(middleware.GzipConfig{
    Level: 5,
}))
```

### 请求跟踪
可以为每个请求生成一个唯一ID，便于跟踪和日志记录：

```go
e.Use(middleware.RequestID())
```

### 重定向
`Echo` 提供了方便的方法来执行重定向：

```go
e.GET("/old", func(c echo.Context) error {
    return c.Redirect(http.StatusMovedPermanently, "/new")
})
```

### 自定义 HTTP 错误
你可以自定义 HTTP 错误的处理方法：

```go
e.HTTPErrorHandler = func(err error, c echo.Context) {
    code := http.StatusInternalServerError
    if he, ok := err.(*echo.HTTPError); ok {
        code = he.Code
    }
    errorPage := fmt.Sprintf("%d.html", code)
    c.File(errorPage)
}
```

### 连接数据库
尽管 `Echo` 本身不提供数据库连接功能，但它可以很容易地与像 `GORM` 这样的库集成，来实现完整的数据库操作。

### 扩展性
`Echo` 的设计允许你轻松地扩展和添加自己的中间件、模板引擎或其他组件。这意味着你可以根据项目的特定需求定制 `Echo`。

### 处理请求体大小
为了防止潜在的拒绝服务攻击，你可以限制请求体的大小：

```go
e.Use(middleware.BodyLimit("2M"))
```
上述中间件将限制所有请求体的大小为2MB。

### 处理多部分请求
如果你的应用需要处理如文件上传之类的多部分请求，`Echo` 提供了便捷的API来处理它们：

```go
e.POST("/upload", func(c echo.Context) error {
    form, err := c.MultipartForm()
    if err != nil {
        return err
    }
    files := form.File["files"]
    for _, file := range files {
        dst := fmt.Sprintf("/tmp/%s", file.Filename)
        if err := c.SaveUploadedFile(file, dst); err != nil {
            return err
        }
    }
    return c.HTML(http.StatusOK, "Files uploaded successfully!")
})
```

### 中间件函数的定义
你可以定义自己的中间件函数并将它们与应用程序集成。以下是如何定义并使用一个简单的中间件的示例：

```go
func SetCustomHeader(next echo.HandlerFunc) echo.HandlerFunc {
    return func(c echo.Context) error {
        c.Response().Header().Set("X-Custom-Header", "MyValue")
        return next(c)
    }
}

e.Use(SetCustomHeader)
```

### 请求后的钩子
有时，你可能希望在请求完成后执行某些操作。这可以使用 `Echo` 的 `After` 方法来实现：

```go
e.After(func(c echo.Context) {
    log.Printf("After request: %v", c.Path())
})
```

### 嵌套组
你可以在 `Echo` 中嵌套组来组织路由：

```go
adminGroup := e.Group("/admin")
v1Group := adminGroup.Group("/v1")
v1Group.GET("/users", getUserList)
```

### 自定义上下文
要在 `Echo` 的上下文中添加自己的方法或字段，你可以定义一个自己的上下文类型，并使用 `HideContext` 和 `Wrap` 方法来包装原始的上下文：

```go
type CustomContext struct {
    echo.Context
}

func (c *CustomContext) MyCustomMethod() string {
    return "This is custom!"
}

e.HideContext()
e.Use(func(next echo.HandlerFunc) echo.HandlerFunc {
    return func(c echo.Context) error {
        cc := &CustomContext{c}
        return next(cc)
    }
})

e.GET("/custom", func(c echo.Context) error {
    cc := c.(*CustomContext)
    return c.String(http.StatusOK, cc.MyCustomMethod())
})
```

### 安全中间件
`Echo` 提供了一个内置的安全中间件，用于增加安全性，如内容安全策略、`XSS` 保护等：

```go
e.Use(middleware.SecureWithConfig(middleware.SecureConfig{
    XSSProtection: "1; mode=block",
}))
```

### 自定义绑定器
如果需要，你可以实现自己的请求数据绑定逻辑：

```go
type MyBinder struct{}

func (b *MyBinder) Bind(i interface{}, c echo.Context) error {
    // Your custom binding logic here
    return nil
}

e.Binder = &MyBinder{}
```

### 集成 OpenTracing
`Echo` 可以与 `OpenTracing` 集成，提供请求的跟踪信息，这对于分析和监控生产环境的性能非常有用。

### 优雅地关闭
你可以优雅地关闭 `Echo` 服务，确保所有正在处理的请求都被适当地完成：

```go
go func() {
    if err := e.Start(":8080"); err != nil && err != http.ErrServerClosed {
        e.Logger.Fatal("shutting down the server")
    }
}()

quit := make(chan os.Signal, 1)
signal.Notify(quit, os.Interrupt)
<-quit
ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
defer cancel()
if err := e.Shutdown(ctx); err != nil {
    e.Logger.Fatal(err)
}
```

Done.
