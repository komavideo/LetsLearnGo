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

