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


