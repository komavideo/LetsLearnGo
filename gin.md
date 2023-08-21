Go Gin Web 开发
===============

Gin是一个用Go语言编写的Web框架，它非常高效且易于使用。

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

## Go Gin Web 开发库

### 1. 安装Gin库

首先，你需要确保你的系统上已经安装了Go。然后，你可以使用以下命令安装Gin库：

```bash
go get -u github.com/gin-gonic/gin
```

### 2. 创建一个基本的HTTP服务器

下面的代码将启动一个基本的HTTP服务器，并在端口8080上监听请求。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/ping", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "pong",
		})
	})
	r.Run(":8080") // 监听并在0.0.0.0:8080上启动服务
}
```

访问http://localhost:8080/ping会收到JSON响应{"message": "pong"}。

### 3. 使用路由组

你可以使用路由组来组织具有共同前缀的路由。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	v1 := r.Group("/v1")
	{
		v1.GET("/ping", func(c *gin.Context) {
			c.JSON(200, gin.H{"message": "pong"})
		})
		v1.GET("/hello", func(c *gin.Context) {
			c.JSON(200, gin.H{"message": "hello"})
		})
	}
	r.Run(":8080")
}
```

### 4. 获取URL参数

你可以通过 `c.Param` 获取 `URL` 中的参数。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/user/:name", func(c *gin.Context) {
		name := c.Param("name")
		c.String(200, "Hello %s", name)
	})
	r.Run(":8080")
}
```

访问 `http://localhost:8080/user/John` 将显示 `“Hello John”`。

### 5. 使用中间件

Gin允许你使用中间件，如下所示：

```go
package main

import (
	"github.com/gin-gonic/gin"
	"log"
	"time"
)

func requestLogger() gin.HandlerFunc {
	return func(c *gin.Context) {
		t := time.Now()
		c.Next()
		latency := time.Since(t)
		log.Printf("Request processed in %s", latency)
	}
}

func main() {
	r := gin.Default()
	r.Use(requestLogger())
	r.GET("/ping", func(c *gin.Context) {
		c.JSON(200, gin.H{"message": "pong"})
	})
	r.Run(":8080")
}
```

### 6. 处理POST请求

你可以使用Gin接收和处理POST请求。下面的例子演示了如何接收JSON有效载荷并响应。

```go
package main

import "github.com/gin-gonic/gin"

type User struct {
	Name string `json:"name"`
	Age  int    `json:"age"`
}

func main() {
	r := gin.Default()
	r.POST("/user", func(c *gin.Context) {
		var user User
		if err := c.ShouldBindJSON(&user); err != nil {
			c.JSON(400, gin.H{"error": err.Error()})
			return
		}
		c.JSON(200, gin.H{"name": user.Name, "age": user.Age})
	})
	r.Run(":8080")
}
```

通过POST请求发送JSON，如 `{"name": "John", "age": 30}`，服务器将响应相同的JSON。

### 7. 使用查询参数

可以从URL查询参数中提取信息。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/search", func(c *gin.Context) {
		query := c.DefaultQuery("query", "none")
		c.String(200, "Search for: %s", query)
	})
	r.Run(":8080")
}
```

访问 `http://localhost:8080/search?query=gin` 将显示 `“Search for: gin”`。

### 8. 上传文件

Gin使文件上传变得容易。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"log"
)

func main() {
	r := gin.Default()
	r.POST("/upload", func(c *gin.Context) {
		file, _ := c.FormFile("file")
		log.Println(file.Filename)

		// 上传文件到指定路径
		c.SaveUploadedFile(file, "/path/to/destination")

		c.String(200, "File uploaded successfully")
	})
	r.Run(":8080")
}
```

### 9. 自定义中间件

你可以创建自定义中间件以执行特定任务，例如身份验证。

```go
package main

import "github.com/gin-gonic/gin"

func authRequired() gin.HandlerFunc {
	return func(c *gin.Context) {
		token := c.GetHeader("Authorization")
		if token != "secret-token" {
			c.JSON(401, gin.H{"error": "Unauthorized"})
			c.Abort()
			return
		}
		c.Next()
	}
}

func main() {
	r := gin.Default()
	r.Use(authRequired())
	r.GET("/secure", func(c *gin.Context) {
		c.String(200, "Secure content!")
	})
	r.Run(":8080")
}
```

只有带有正确 `Authorization` 头的请求才能访问 `“/secure”` 端点。

### 10. 错误处理

使用 `gin.Recovery()` 中间件可以捕获任何恐慌并返回500错误。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.Use(gin.Recovery())
	r.GET("/panic", func(c *gin.Context) {
		panic("Something went wrong!")
	})
	r.Run(":8080")
}
```

### 11. 重定向

你可以使用Gin轻松执行HTTP重定向。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/old", func(c *gin.Context) {
		c.Redirect(301, "/new")
	})
	r.GET("/new", func(c *gin.Context) {
		c.String(200, "Welcome to the new URL!")
	})
	r.Run(":8080")
}
```

访问 `http://localhost:8080/old` 将重定向到 `http://localhost:8080/new`。

### 12. HTML模板渲染

Gin允许你使用HTML模板来渲染页面。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.LoadHTMLGlob("templates/*")
	r.GET("/home", func(c *gin.Context) {
		c.HTML(200, "index.html", gin.H{"title": "Home Page"})
	})
	r.Run(":8080")
}
```

确保 `index.html` 文件位于名为 `templates` 的目录中。

### 13. 为静态文件提供服务

你可以使用Gin为静态文件（例如图片、CSS和JavaScript）提供服务。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.Static("/assets", "./assets")
	r.Run(":8080")
}
```

该代码将提供 `./assets` 目录下的所有文件。

### 14. 使用Basic Authentication

Gin支持基本身份验证。你可以使用 `gin.BasicAuth()` 中间件来保护特定路由。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()

	authorized := r.Group("/", gin.BasicAuth(gin.Accounts{
		"user": "password",
	}))

	authorized.GET("/admin", func(c *gin.Context) {
		c.String(200, "Welcome, admin!")
	})

	r.Run(":8080")
}
```

### 15. 使用自定义结构绑定请求参数

你可以使用自定义结构将请求参数绑定到Go结构。

```go
package main

import "github.com/gin-gonic/gin"

type Query struct {
	FirstName string `form:"first_name"`
	LastName  string `form:"last_name"`
}

func main() {
	r := gin.Default()
	r.GET("/query", func(c *gin.Context) {
		var query Query
		if err := c.ShouldBindQuery(&query); err == nil {
			c.JSON(200, gin.H{"first_name": query.FirstName, "last_name": query.LastName})
		} else {
			c.JSON(400, gin.H{"error": err.Error()})
		}
	})
	r.Run(":8080")
}
```

访问 `http://localhost:8080/query?first_name=John&last_name=Doe` 将返回JSON响应。

### 16. 多文件上传

Gin也支持多文件上传。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"log"
)

func main() {
	r := gin.Default()
	r.POST("/upload", func(c *gin.Context) {
		form, _ := c.MultipartForm()
		files := form.File["files"]

		for _, file := range files {
			log.Println(file.Filename)

			// 保存文件到指定路径
			c.SaveUploadedFile(file, "/path/to/destination/"+file.Filename)
		}

		c.String(200, "Files uploaded successfully")
	})
	r.Run(":8080")
}
```

### 17. 自定义HTTP响应

你可以使用Gin为不同的数据类型创建自定义响应。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/json", func(c *gin.Context) {
		c.JSON(200, gin.H{"message": "JSON response"})
	})
	r.GET("/yaml", func(c *gin.Context) {
		c.YAML(200, gin.H{"message": "YAML response"})
	})
	r.GET("/xml", func(c *gin.Context) {
		c.XML(200, gin.H{"message": "XML response"})
	})
	r.Run(":8080")
}
```

### 18. 使用Gin验证请求体

Gin可以帮助你验证传入的请求体，例如检查必填字段。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

type Login struct {
	User     string `json:"user" binding:"required"`
	Password string `json:"password" binding:"required"`
}

func main() {
	r := gin.Default()
	r.POST("/login", func(c *gin.Context) {
		var login Login

		if err := c.ShouldBindJSON(&login); err != nil {
			c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
			return
		}

		if login.User != "user" || login.Password != "password" {
			c.JSON(http.StatusUnauthorized, gin.H{"error": "Unauthorized"})
			return
		}

		c.JSON(http.StatusOK, gin.H{"message": "Successfully authenticated"})
	})
	r.Run(":8080")
}
```

### 19. 使用Gin提供WebSocket服务

你可以使用Gin配合WebSocket库来提供WebSocket服务。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"github.com/gorilla/websocket"
	"net/http"
)

var upgrader = websocket.Upgrader{
	ReadBufferSize:  1024,
	WriteBufferSize: 1024,
}

func main() {
	r := gin.Default()
	r.GET("/ws", func(c *gin.Context) {
		conn, err := upgrader.Upgrade(c.Writer, c.Request, nil)
		if err != nil {
			return
		}

		for {
			messageType, p, err := conn.ReadMessage()
			if err != nil {
				return
			}
			if err := conn.WriteMessage(messageType, p); err != nil {
				return
			}
		}
	})
	r.Run(":8080")
}
```

### 20. 使用CORS中间件

跨源资源共享（CORS）是一种安全措施，你可以使用Gin的CORS中间件来控制。

```go
package main

import (
	"github.com/gin-contrib/cors"
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()
	config := cors.DefaultConfig()
	config.AllowOrigins = []string{"https://example.com"}
	r.Use(cors.New(config))

	r.GET("/ping", func(c *gin.Context) {
		c.String(200, "pong")
	})
	r.Run(":8080")
}
```

### 21. 连接数据库

你可以将Gin与数据库结合使用，例如MySQL。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"github.com/jinzhu/gorm"
	_ "github.com/jinzhu/gorm/dialects/mysql"
)

type User struct {
	ID   uint   `json:"id"`
	Name string `json:"name"`
}

func main() {
	db, err := gorm.Open("mysql", "user:password@/dbname?charset=utf8&parseTime=True&loc=Local")
	if err != nil {
		panic(err)
	}
	defer db.Close()

	db.AutoMigrate(&User{})

	r := gin.Default()
	r.GET("/users", func(c *gin.Context) {
		var users []User
		db.Find(&users)
		c.JSON(200, users)
	})
	r.Run(":8080")
}
```

请确保修改上述代码中的数据库连接字符串以匹配你的配置。

### 22. 组合多个中间件

你可以为特定路由组合多个中间件。

```go
package main

import "github.com/gin-gonic/gin"

func middlewareOne() gin.HandlerFunc {
	return func(c *gin.Context) {
		// 做一些操作
		c.Next()
	}
}

func middlewareTwo() gin.HandlerFunc {
	return func(c *gin.Context) {
		// 做一些操作
		c.Next()
	}
}

func main() {
	r := gin.Default()
	r.GET("/protected", middlewareOne(), middlewareTwo(), func(c *gin.Context) {
		c.String(200, "Protected content!")
	})
	r.Run(":8080")
}
```

### 23. 使用自定义HTTP错误处理

你可以定义自定义HTTP错误处理程序来优雅地处理错误。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.NoRoute(func(c *gin.Context) {
		c.JSON(404, gin.H{"message": "Not Found"})
	})
	r.NoMethod(func(c *gin.Context) {
		c.JSON(405, gin.H{"message": "Method Not Allowed"})
	})
	r.Run(":8080")
}
```

### 24. 使用Gin测试路由

Gin提供了一种方便的方法来测试你的路由。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
	"net/http/httptest"
	"testing"
)

func TestPingRoute(t *testing.T) {
	r := gin.Default()
	r.GET("/ping", func(c *gin.Context) {
		c.String(200, "pong")
	})

	req, _ := http.NewRequest("GET", "/ping", nil)
	resp := httptest.NewRecorder()
	r.ServeHTTP(resp, req)

	if resp.Code != 200 || resp.Body.String() != "pong" {
		t.Fail()
	}
}
```

### 25. 使用Gin处理Multipart/Urlencoded Form

你可以使用Gin处理 `Multipart/Urlencoded` 表单。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.POST("/form", func(c *gin.Context) {
		form := c.Request.FormValue("message")
		file, _ := c.FormFile("file")

		c.String(200, "Message: %s, File: %s", form, file.Filename)
	})
	r.Run(":8080")
}
```

### 26. 使用Gin渲染PureJSON

与JSON不同，`PureJSON` 不仅将特殊HTML字符替换为其Unicode字面量，还会输出UTF-8。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/json", func(c *gin.Context) {
		c.PureJSON(200, gin.H{
			"message": "<b>Hello, world!</b>",
		})
	})
	r.Run(":8080")
}
```

### 27. 创建自定义实例

你可以创建自定义Gin实例并手动配置它。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	gin.SetMode(gin.ReleaseMode)
	r := gin.New()
	r.Use(gin.Logger())
	r.Use(gin.Recovery())

	r.GET("/ping", func(c *gin.Context) {
		c.String(200, "pong")
	})

	r.Run(":8080")
}
```

### 28. 使用Gin处理请求上下文

你可以在Gin的请求上下文中存储和检索数据。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/set", func(c *gin.Context) {
		c.Set("key", "value")
		c.Status(200)
	})
	r.GET("/get", func(c *gin.Context) {
		value, exists := c.Get("key")
		if exists {
			c.String(200, value.(string))
		} else {
			c.Status(404)
		}
	})
	r.Run(":8080")
}
```

### 29. 使用嵌套路由组

你可以使用嵌套的路由组来组织你的API结构。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	api := r.Group("/api")
	{
		v1 := api.Group("/v1")
		{
			v1.GET("/posts", getPostsV1)
			v1.GET("/users", getUsersV1)
		}
		v2 := api.Group("/v2")
		{
			v2.GET("/posts", getPostsV2)
			v2.GET("/users", getUsersV2)
		}
	}
	r.Run(":8080")
}

func getPostsV1(c *gin.Context) {
	c.String(200, "V1 Posts")
}

func getUsersV1(c *gin.Context) {
	c.String(200, "V1 Users")
}

func getPostsV2(c *gin.Context) {
	c.String(200, "V2 Posts")
}

func getUsersV2(c *gin.Context) {
	c.String(200, "V2 Users")
}
```

### 30. 使用自定义验证

你可以使用自定义验证来验证请求体。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"github.com/go-playground/validator/v10"
	"net/http"
)

type Register struct {
	Email    string `binding:"required,email"`
	Password string `binding:"required,min=8"`
}

func main() {
	r := gin.Default()
	r.POST("/register", func(c *gin.Context) {
		var form Register
		if err := c.ShouldBind(&form); err != nil {
			errs := err.(validator.ValidationErrors)
			for _, e := range errs {
				// 你可以自定义错误消息
				c.JSON(http.StatusBadRequest, gin.H{"error": e.Error()})
				return
			}
		}
		c.JSON(http.StatusOK, gin.H{"message": "Registration succeeded"})
	})
	r.Run(":8080")
}
```

### 31. 使用自定义渲染器

你可以实现Gin的 `Render` 接口来创建自定义渲染器。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

type HTMLDebugRenderer struct {
	Data string
}

func (r HTMLDebugRenderer) Render(w http.ResponseWriter) error {
	_, err := w.Write([]byte(r.Data))
	return err
}

func (r HTMLDebugRenderer) WriteContentType(w http.ResponseWriter) {
	w.Header().Set("Content-Type", "text/html; charset=utf-8")
}

func main() {
	r := gin.Default()
	r.GET("/", func(c *gin.Context) {
		c.Render(200, HTMLDebugRenderer{Data: "<h1>Hello, world!</h1>"})
	})
	r.Run(":8080")
}
```

### 32. 使用Graceful Restart

你可以实现优雅重启，这样在重启应用程序时不会中断现有连接。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"golang.org/x/sync/errgroup"
	"net/http"
	"os"
	"os/signal"
	"syscall"
	"time"
)

func main() {
	r := gin.Default()
	r.GET("/", func(c *gin.Context) {
		c.String(200, "Hello, world!")
	})

	server := &http.Server{
		Addr:    ":8080",
		Handler: r,
	}

	var g errgroup.Group
	g.Go(func() error {
		return server.ListenAndServe()
	})

	quit := make(chan os.Signal)
	signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM)
	g.Go(func() error {
		<-quit
		ctx, cancel := time.WithTimeout(gin.DefaultWriter, 5*time.Second)
		defer cancel()
		return server.Shutdown(ctx)
	})

	if err := g.Wait(); err != nil && err != http.ErrServerClosed {
		panic(err)
	}
}
```

### 33. 使用自定义HTTP配置

你可以通过提供自定义的HTTP配置来启动Gin服务。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
	"time"
)

func main() {
	r := gin.Default()
	r.GET("/", func(c *gin.Context) {
		c.String(200, "Hello, world!")
	})

	srv := &http.Server{
		Addr:         ":8080",
		Handler:      r,
		ReadTimeout:  5 * time.Second,
		WriteTimeout: 10 * time.Second,
	}

	if err := srv.ListenAndServe(); err != nil {
		panic(err)
	}
}
```

### 34. 使用自定义日志格式

你可以配置Gin以使用自定义的日志格式。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"time"
)

func main() {
	r := gin.New()

	// 自定义日志格式
	r.Use(gin.LoggerWithFormatter(func(param gin.LogFormatterParams) string {
		return fmt.Sprintf("%s - [%s] %s %s %d %s \"%s\" %s\n",
			param.ClientIP,
			param.TimeStamp.Format(time.RFC1123),
			param.Method,
			param.Path,
			param.StatusCode,
			param.Latency,
			param.Request.UserAgent(),
			param.ErrorMessage,
		)
	}))

	r.GET("/", func(c *gin.Context) {
		c.String(200, "Hello, world!")
	})

	r.Run(":8080")
}
```

### 35. 使用自定义绑定

你可以实现Gin的 `binding.Binder` 接口来创建自定义绑定。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"github.com/gin-gonic/gin/binding"
	"net/http"
)

type CustomBinder struct{}

func (cb CustomBinder) Bind(*http.Request, interface{}) error {
	// 自定义绑定逻辑
	return nil
}

func (cb CustomBinder) BindBody([]byte, interface{}) error {
	// 自定义绑定逻辑
	return nil
}

func main() {
	binding.Register("custom", CustomBinder{})
	r := gin.Default()
	// ...
}
```

### 36. 生成自动路由文档

你可以使用第三方库如 `swaggo/gin-swagger` 自动生成API文档。

https://github.com/swaggo/gin-swagger

### 37. 使用Gin处理HTTPS请求

你可以配置Gin以处理HTTPS请求。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/", func(c *gin.Context) {
		c.String(200, "Secure Hello, world!")
	})

	r.RunTLS(":443", "path/to/cert.pem", "path/to/key.pem")
}
```

### 38. 使用子域名

你可以使用Gin处理特定子域名的请求。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()

	// 匹配 admin 子域名
	admin := r.Group("/admin")
	admin.Use(checkAdmin())
	{
		admin.GET("/", adminIndex)
	}

	r.Run(":8080")
}

func checkAdmin() gin.HandlerFunc {
	return func(c *gin.Context) {
		// 检查是否是管理员
	}
}

func adminIndex(c *gin.Context) {
	c.String(200, "Admin dashboard")
}
```

### 39. 使用第三方中间件

Gin有一个活跃的社区，提供了许多第三方中间件，如 `gin-contrib/sessions`来 处理会话，`gin-contrib/cors` 来处理CORS等。

https://github.com/gin-contrib/sessions

https://github.com/gin-contrib/cors

### 40. 实现自定义文件服务

你可以使用Gin实现自定义文件服务。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

func main() {
	r := gin.Default()

	fs := http.Dir("./static")
	r.GET("/static/*filepath", func(c *gin.Context) {
		c.FileFromFS(c.Param("filepath"), http.FS(fs))
	})

	r.Run(":8080")
}
```

### 41. 使用中间件传递变量

你可以使用中间件在不同的处理程序之间传递变量。

```go
package main

import "github.com/gin-gonic/gin"

func setUser() gin.HandlerFunc {
	return func(c *gin.Context) {
		user := "Guest" // 假设这是从数据库或会话中获取的
		c.Set("user", user)
		c.Next()
	}
}

func main() {
	r := gin.Default()
	r.Use(setUser())
	r.GET("/", func(c *gin.Context) {
		user, _ := c.Get("user")
		c.String(200, "Hello %s", user)
	})
	r.Run(":8080")
}
```

### 42. 使用多种绑定

你可以同时使用多种绑定来解析请求体。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"github.com/gin-gonic/gin/binding"
)

type Person struct {
	Name string `form:"name" json:"name" uri:"name" binding:"required"`
	Age  int    `form:"age" json:"age" uri:"age" binding:"required"`
}

func main() {
	r := gin.Default()
	r.GET("/:name/:age", func(c *gin.Context) {
		var person Person
		if err := c.ShouldBindUri(&person); err != nil {
			c.JSON(400, gin.H{"msg": err})
			return
		}
		c.JSON(200, gin.H{"name": person.Name, "age": person.Age})
	})

	r.POST("/", func(c *gin.Context) {
		var person Person
		if c.ContentType() == "application/json" {
			if err := c.ShouldBindBodyWith(&person, binding.JSON); err != nil {
				c.JSON(400, gin.H{"msg": err})
				return
			}
		} else {
			if err := c.ShouldBindBodyWith(&person, binding.Form); err != nil {
				c.JSON(400, gin.H{"msg": err})
				return
			}
		}
		c.JSON(200, gin.H{"name": person.Name, "age": person.Age})
	})

	r.Run(":8080")
}
```

### 43. 使用Gin实现JWT身份验证

你可以使用Gin和一个适当的库，如 `appleboy/gin-jwt`，来实现JSON Web Token（JWT）身份验证。

https://github.com/appleboy/gin-jwt

```go
// 示例代码，需要结合具体的JWT库使用
func main() {
	r := gin.Default()

	// 中间件进行JWT验证
	authMiddleware := jwtMiddleware()
	r.POST("/login", authMiddleware.LoginHandler)

	auth := r.Group("/auth")
	auth.Use(authMiddleware.MiddlewareFunc())
	{
		auth.GET("/refresh_token", authMiddleware.RefreshHandler)
		auth.GET("/hello", helloHandler)
	}

	r.Run(":8080")
}

func jwtMiddleware() *jwt.GinJWTMiddleware {
	// 配置JWT中间件
}

func helloHandler(c *gin.Context) {
	c.String(200, "Hello, JWT user!")
}
```

### 44. 使用Gin实现Rate Limiting

你可以使用限速器中间件如 `ulule/limiter` 与Gin一起实现速率限制。

https://github.com/ulule/limiter

```go
// 示例代码，需要结合具体的限速器库使用
func main() {
	r := gin.Default()
	r.Use(rateLimitMiddleware())

	r.GET("/", func(c *gin.Context) {
		c.String(200, "Hello, world!")
	})

	r.Run(":8080")
}

func rateLimitMiddleware() gin.HandlerFunc {
	// 配置限速器中间件
}
```

### 45. 使用Gin响应HTTP/2 Server Push

如果你的服务器支持HTTP/2，你可以使用Server Push特性来推送资源。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"log"
)

func main() {
	r := gin.Default()
	r.GET("/", func(c *gin.Context) {
		pusher := c.Writer.Pusher()
		if pusher == nil {
			log.Println("HTTP/2 Server Push not supported.")
			return
		}

		if err := pusher.Push("/static/css/style.css", nil); err != nil {
			log.Printf("Failed to push: %v", err)
		}

		c.HTML(200, "index.html", nil)
	})

	r.RunTLS(":443", "path/to/cert.pem", "path/to/key.pem")
}
```

### 46. 使用WebSocket广播

你可以结合WebSocket库，例如 `gorilla/websocket`，使用Gin实现WebSocket广播。

https://github.com/gorilla/websocket

```go
// 示例代码，需要结合具体的WebSocket库使用
func main() {
	r := gin.Default()

	r.GET("/ws", func(c *gin.Context) {
		handleWebSocketConnection(c.Writer, c.Request)
	})

	r.Run(":8080")
}

func handleWebSocketConnection(w http.ResponseWriter, r *http.Request) {
	// 处理WebSocket连接和广播逻辑
}
```

### 47. 使用Gin优雅地处理Panic

Gin提供了一个内置的恢复中间件来优雅地处理panic，确保服务器即使在panic发生时也不会崩溃。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.Use(gin.Recovery())

	r.GET("/", func(c *gin.Context) {
		panic("Something went wrong")
		c.String(200, "Hello, world!")
	})

	r.Run(":8080")
}
```

### 48. 自定义Gin引擎的Logger

你可以替换Gin引擎的默认日志记录器。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"log"
	"os"
)

func main() {
	r := gin.New()

	// 设置自定义的日志记录器
	gin.DefaultWriter = log.New(os.Stdout, "[MyApp] ", log.LstdFlags)

	r.GET("/", func(c *gin.Context) {
		c.String(200, "Hello, world!")
	})

	r.Run(":8080")
}
```

### 49. 使用Trusted Proxies

如果你的应用程序位于反向代理后面，你可以配置Gin以从信任的代理接收客户端IP地址。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"github.com/gin-gonic/gin/internal/bytesconv"
	"net/http"
)

func main() {
	r := gin.Default()
	r.Use(gin.LoggerWithConfig(gin.LoggerConfig{
		SkipPaths:  []string{"/skip"},
		TrustedProxies: []string{"192.168.0.1"}, // 将这里的IP替换为你的信任代理IP
	}))

	r.GET("/", func(c *gin.Context) {
		clientIP := c.ClientIP()
		c.String(http.StatusOK, "Client IP: "+bytesconv.String(clientIP))
	})

	r.Run(":8080")
}
```

### 50. 使用自定义标签名

你可以在绑定结构体时自定义标签名称。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"github.com/gin-gonic/gin/binding"
)

type Person struct {
	Name string `mytag:"name"`
	Age  int    `mytag:"age"`
}

func main() {
	r := gin.Default()
	r.GET("/person", func(c *gin.Context) {
		var person Person
		if err := c.ShouldBindWith(&person, binding.QueryPostForm("mytag")); err != nil {
			c.JSON(400, gin.H{"error": err.Error()})
			return
		}
		c.JSON(200, person)
	})

	r.Run(":8080")
}
```

### 51. 使用自定义HTTP方法

你可以使用自定义HTTP方法来定义路由。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.Handle("CUSTOM", "/custom", func(c *gin.Context) {
		c.String(200, "Custom method handled")
	})

	r.Run(":8080")
}
```

### 52. 基于特定域名路由

你可以为特定域名定义路由。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()

	r.GET("/domain", func(c *gin.Context) {
		if c.FullPath() == "example.com" {
			c.String(200, "Handling for example.com")
		} else {
			c.String(200, "Handling for other domains")
		}
	})

	r.Run(":8080")
}
```

### 53. 使用Gin的内置测试框架

Gin提供了一个内置的测试框架来帮助你更容易地测试路由。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
	"testing"
)

func TestHelloWorld(t *testing.T) {
	r := gin.Default()
	r.GET("/", func(c *gin.Context) {
		c.String(200, "Hello, world!")
	})

	w := performRequest(r, "GET", "/")

	if w.Code != http.StatusOK || w.Body.String() != "Hello, world!" {
		t.Fail()
	}
}

func performRequest(r http.Handler, method, path string) *httptest.ResponseRecorder {
	req, _ := http.NewRequest(method, path, nil)
	w := httptest.NewRecorder()
	r.ServeHTTP(w, req)
	return w
}
```

### 54. 使用请求绑定和验证结构体

你可以使用Gin的请求绑定功能来验证请求体的结构。

```go
package main

import "github.com/gin-gonic/gin"

type Login struct {
	Username string `form:"username" binding:"required"`
	Password string `form:"password" binding:"required"`
}

func main() {
	r := gin.Default()
	r.POST("/login", func(c *gin.Context) {
		var form Login
		if err := c.ShouldBind(&form); err != nil {
			c.JSON(400, gin.H{"error": err.Error()})
			return
		}
		c.JSON(200, gin.H{"status": "you are logged in"})
	})

	r.Run(":8080")
}
```

### 55. 定制Gin的HTTP错误消息

你可以通过编写自定义处理程序来定制Gin的HTTP错误消息。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

func main() {
	r := gin.Default()

	r.NoRoute(func(c *gin.Context) {
		c.JSON(http.StatusNotFound, gin.H{"message": "Page not found"})
	})

	r.NoMethod(func(c *gin.Context) {
		c.JSON(http.StatusMethodNotAllowed, gin.H{"message": "Method not allowed"})
	})

	r.Run(":8080")
}
```

### 56. 在Gin中使用HTTP/2

Gin支持HTTP/2，你可以通过配置TLS来启用它。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/", func(c *gin.Context) {
		c.String(200, "Welcome to HTTP/2")
	})

	r.RunTLS(":443", "path/to/cert.pem", "path/to/key.pem")
}
```

### 57. 使用Gin进行文件上传

Gin可以方便地处理文件上传。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"log"
)

func main() {
	r := gin.Default()
	r.POST("/upload", func(c *gin.Context) {
		file, err := c.FormFile("file")
		if err != nil {
			c.JSON(400, gin.H{"error": err.Error()})
			return
		}

		if err := c.SaveUploadedFile(file, "uploads/"+file.Filename); err != nil {
			c.JSON(500, gin.H{"error": err.Error()})
			return
		}

		c.JSON(200, gin.H{"message": "File uploaded successfully"})
	})

	r.Run(":8080")
}
```

### 58. Gin中的依赖注入

你可以使用第三方库，如 `google/wire`，在Gin应用程序中实现依赖注入。

https://github.com/google/wire

### 59. 使用Gin优雅地重启或停止服务器

你可以通过创建一个自定义的HTTP服务器来优雅地重启或停止Gin服务器。

```go
package main

import (
	"context"
	"github.com/gin-gonic/gin"
	"log"
	"net/http"
	"os"
	"os/signal"
	"syscall"
	"time"
)

func main() {
	r := gin.Default()
	srv := &http.Server{
		Addr:    ":8080",
		Handler: r,
	}

	go func() {
		if err := srv.ListenAndServe(); err != nil && err != http.ErrServerClosed {
			log.Fatalf("listen: %s\n", err)
		}
	}()

	quit := make(chan os.Signal)
	signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM)
	<-quit
	log.Println("Shutting down server...")

	ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
	defer cancel()
	if err := srv.Shutdown(ctx); err != nil {
		log.Fatal("Server forced to shutdown:", err)
	}

	log.Println("Server exiting")
}
```

### 60. 使用Gin自定义路径参数匹配

你可以使用Gin自定义路径参数的匹配方式。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()

	r.GET("/user/:name", func(c *gin.Context) {
		name := c.Param("name")
		c.String(200, "Hello %s", name)
	})

	r.GET("/user/:name/*action", func(c *gin.Context) {
		name := c.Param("name")
		action := c.Param("action")
		c.String(200, "Hello %s, action: %s", name, action)
	})

	r.Run(":8080")
}
```

### 61. 使用Gin提供静态文件服务

Gin可以方便地为静态文件提供HTTP服务。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()

	// 为静态文件提供服务
	r.Static("/static", "./static")

	r.Run(":8080")
}
```

### 62. 使用Gin重定向路由

你可以使用Gin的重定向功能将请求重定向到另一个URL。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()

	r.GET("/old", func(c *gin.Context) {
		c.Redirect(301, "/new")
	})

	r.GET("/new", func(c *gin.Context) {
		c.String(200, "New endpoint")
	})

	r.Run(":8080")
}
```

### 63. 使用Gin的Cookie管理

Gin提供了直接读取和设置HTTP Cookie的方法。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()

	r.GET("/cookie", func(c *gin.Context) {
		cookie, err := c.Cookie("my_cookie")
		if err != nil {
			cookie = "Not set"
			c.SetCookie("my_cookie", "value_here", 3600, "/", "localhost", false, true)
		}
		c.String(200, "Cookie value: %s", cookie)
	})

	r.Run(":8080")
}
```

### 64. 使用Gin的路由群组和中间件

通过使用路由组和中间件，你可以更容易地组织和管理路由。

```go
package main

import "github.com/gin-gonic/gin"

func authRequired() gin.HandlerFunc {
	return func(c *gin.Context) {
		// 模拟认证
		authorized := true
		if !authorized {
			c.JSON(401, gin.H{"error": "Unauthorized"})
			c.Abort()
			return
		}
		c.Next()
	}
}

func main() {
	r := gin.Default()

	// 使用中间件的路由组
	authorized := r.Group("/", authRequired())
	{
		authorized.GET("/dashboard", func(c *gin.Context) {
			c.String(200, "Dashboard")
		})
		authorized.GET("/settings", func(c *gin.Context) {
			c.String(200, "Settings")
		})
	}

	r.Run(":8080")
}
```

### 65. 使用Gin的模板渲染

Gin支持Go模板引擎，可以用于HTML页面渲染。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()

	r.LoadHTMLGlob("templates/*")
	r.GET("/home", func(c *gin.Context) {
		c.HTML(200, "index.tmpl", gin.H{"title": "Home Page"})
	})

	r.Run(":8080")
}
```

确保你有一个名为index.tmpl的文件位于templates目录中。

### 66. 使用Gin的多文件上传

Gin支持一次上传多个文件。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"log"
)

func main() {
	r := gin.Default()
	r.POST("/upload", func(c *gin.Context) {
		form, err := c.MultipartForm()
		if err != nil {
			c.JSON(400, gin.H{"error": err.Error()})
			return
		}

		files := form.File["files"]
		for _, file := range files {
			if err := c.SaveUploadedFile(file, "uploads/"+file.Filename); err != nil {
				c.JSON(500, gin.H{"error": err.Error()})
				return
			}
		}

		c.JSON(200, gin.H{"message": "Files uploaded successfully"})
	})

	r.Run(":8080")
}
```

### 67. 使用Gin的CORS中间件

你可以通过使用Gin的CORS（跨源资源共享）中间件来控制跨域请求。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"github.com/gin-contrib/cors"
)

func main() {
	r := gin.Default()

	config := cors.DefaultConfig()
	config.AllowOrigins = []string{"https://example.com"}
	r.Use(cors.New(config))

	r.GET("/", func(c *gin.Context) {
		c.String(200, "CORS configured")
	})

	r.Run(":8080")
}
```

### 68. 使用Gin托管WebSocket服务

你可以通过使用WebSocket库，例如 `gorilla/websocket`，与Gin一起托管WebSocket服务。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"github.com/gorilla/websocket"
	"net/http"
)

var upgrader = websocket.Upgrader{
	ReadBufferSize:  1024,
	WriteBufferSize: 1024,
}

func main() {
	r := gin.Default()
	r.GET("/ws", func(c *gin.Context) {
		conn, err := upgrader.Upgrade(c.Writer, c.Request, nil)
		if err != nil {
			http.NotFound(c.Writer, c.Request)
			return
		}

		for {
			messageType, p, err := conn.ReadMessage()
			if err != nil {
				return
			}
			if err := conn.WriteMessage(messageType, p); err != nil {
				return
			}
		}
	})

	r.Run(":8080")
}
```

### 69. 使用Gin进行版本控制

你可以通过使用Gin的路由组来组织API版本。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()

	v1 := r.Group("/v1")
	{
		v1.GET("/users", func(c *gin.Context) {
			c.String(200, "Version 1 users")
		})
	}

	v2 := r.Group("/v2")
	{
		v2.GET("/users", func(c *gin.Context) {
			c.String(200, "Version 2 users")
		})
	}

	r.Run(":8080")
}
```

### 70. 使用Gin的XML和JSONP响应

Gin可以方便地提供XML和JSONP响应。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()

	r.GET("/xml", func(c *gin.Context) {
		c.XML(200, gin.H{"message": "XML response"})
	})

	r.GET("/jsonp", func(c *gin.Context) {
		c.JSONP(200, gin.H{"message": "JSONP response"})
	})

	r.Run(":8080")
}
```

### 71. 使用Gin的自定义结构体绑定验证

你可以在绑定结构体时添加自定义验证。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"github.com/go-playground/validator/v10"
)

type UserInput struct {
	Age int `binding:"gte=18"`
}

func main() {
	r := gin.Default()
	r.POST("/user", func(c *gin.Context) {
		var input UserInput
		if err := c.ShouldBindJSON(&input); err != nil {
			c.JSON(400, gin.H{"error": err.Error()})
			return
		}
		c.JSON(200, gin.H{"status": "success"})
	})

	if v, ok := binding.Validator.Engine().(*validator.Validate); ok {
		v.RegisterValidation("gte", func(fl validator.FieldLevel) bool {
			return fl.Field().Int() >= 18
		})
	}

	r.Run(":8080")
}
```

### 72. 使用Gin的路由绑定

你可以使用Gin的路由绑定来简化请求处理。

```go
package main

import "github.com/gin-gonic/gin"

type User struct {
	ID   string `uri:"id" binding:"required"`
	Name string `uri:"name" binding:"required"`
}

func main() {
	r := gin.Default()

	r.GET("/users/:name/:id", func(c *gin.Context) {
		var user User
		if err := c.ShouldBindUri(&user); err != nil {
			c.JSON(400, gin.H{"error": err.Error()})
			return
		}
		c.JSON(200, gin.H{"name": user.Name, "uuid": user.ID})
	})

	r.Run(":8080")
}
```

### 73. 使用Gin的自定义HTTP错误处理

你可以通过重写Gin的默认错误处理来自定义HTTP错误。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

func main() {
	r := gin.Default()

	r.GET("/error", func(c *gin.Context) {
		c.Error(gin.Error{
			Err:  gin.ErrPageNotFoundError,
			Type: gin.ErrorTypePublic,
		})
		c.String(http.StatusNotFound, "Custom not found error")
	})

	r.Run(":8080")
}
```

### 74. 使用Gin的内置健康检查

Gin提供了一个内置的健康检查API，你可以用来检查服务的健康状况。

```go
package main

import (
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()

	// 健康检查端点
	r.GET("/health", func(c *gin.Context) {
		c.JSON(200, gin.H{"status": "UP"})
	})

	r.Run(":8080")
}
```

### 75. 在Gin中设置请求头

你可以使用Gin中间件在每个响应中设置自定义HTTP头。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()

	r.Use(func(c *gin.Context) {
		c.Header("X-Custom-Header", "Custom-Value")
		c.Next()
	})

	r.GET("/", func(c *gin.Context) {
		c.String(200, "Custom header set")
	})

	r.Run(":8080")
}
```

### 76. 在Gin中设置和获取TLS客户端证书

你可以使用Gin来设置和获取TLS客户端证书，以增加安全性。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"log"
	"crypto/tls"
	"net/http"
)

func main() {
	r := gin.Default()

	r.GET("/", func(c *gin.Context) {
		if len(c.Request.TLS.PeerCertificates) > 0 {
			c.String(http.StatusOK, "Client certificate found!")
		} else {
			c.String(http.StatusOK, "No client certificate found.")
		}
	})

	server := &http.Server{
		Addr:    ":8443",
		Handler: r,
		TLSConfig: &tls.Config{
			ClientAuth: tls.RequestClientCert,
		},
	}

	if err := server.ListenAndServeTLS("server.crt", "server.key"); err != nil {
		log.Fatalf("listen: %s\n", err)
	}
}
```

### 77. 使用Gin的请求和响应日志

你可以使用Gin的日志中间件来记录所有请求和响应。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"time"
)

func requestLogger() gin.HandlerFunc {
	return func(c *gin.Context) {
		t := time.Now()

		c.Next()

		latency := time.Since(t)
		logLine := c.ClientIP() + " - " + c.Request.Method + " " + c.Request.URL.String() + " - " + c.Writer.Status() + " - " + latency.String()
		c.Writer.WriteString(logLine)
	}
}

func main() {
	r := gin.New()
	r.Use(requestLogger())

	r.GET("/", func(c *gin.Context) {
		c.String(200, "Hello, world!")
	})

	r.Run(":8080")
}
```

### 78. 在Gin中限制请求大小

你可以通过设置Gin的 `MaxMultipartMemory` 来限制请求的大小。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

func main() {
	r := gin.Default()
	r.MaxMultipartMemory = 8 << 20 // 8 MiB

	r.POST("/upload", func(c *gin.Context) {
		file, _ := c.FormFile("file")
		c.SaveUploadedFile(file, file.Filename)
		c.String(http.StatusOK, "File uploaded successfully.")
	})

	r.Run(":8080")
}
```

### 79. 在Gin中定制HTML渲染

你可以定制Gin的HTML渲染，使用自定义的HTML模板。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"html/template"
)

func main() {
	r := gin.Default()

	html := template.Must(template.ParseFiles("templates/mytemplate.html"))
	r.SetHTMLTemplate(html)

	r.GET("/", func(c *gin.Context) {
		c.HTML(200, "mytemplate.html", gin.H{"title": "Custom Template"})
	})

	r.Run(":8080")
}
```

### 80. 使用Gin的流式响应

你可以使用Gin的流式响应功能来流式传输大型响应。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"time"
)

func main() {
	r := gin.Default()

	r.GET("/stream", func(c *gin.Context) {
		c.Writer.WriteHeaderNow()
		for i := 0; i < 10; i++ {
			c.Writer.Write([]byte("line "))
			c.Writer.Flush()
			time.Sleep(time.Second)
		}
	})

	r.Run(":8080")
}
```

### 81. 在Gin中使用HTTPS重定向

你可以通过Gin中间件来将HTTP请求重定向到HTTPS。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"golang.org/x/crypto/acme/autocert"
	"log"
)

func main() {
	m := autocert.Manager{
		Prompt:     autocert.AcceptTOS,
		HostPolicy: autocert.HostWhitelist("example.com"),
		Cache:      autocert.DirCache("cache"),
	}

	r := gin.Default()

	// HTTP重定向到HTTPS
	go func() {
		log.Fatal(http.ListenAndServe(":80", m.HTTPHandler(nil)))
	}()

	server := &http.Server{
		Addr:      ":443",
		Handler:   r,
		TLSConfig: m.TLSConfig(),
	}

	r.GET("/", func(c *gin.Context) {
		c.String(200, "Hello, TLS!")
	})

	server.ListenAndServeTLS("", "")
}
```

### 82. 在Gin中使用JWT身份验证

使用JWT（JSON Web Token）身份验证可以增加安全性。你可以使用库，如 `github.com/appleboy/gin-jwt`，与Gin一起使用JWT。

https://github.com/appleboy/gin-jwt

```go
package main

import (
	"github.com/appleboy/gin-jwt/v2"
	"github.com/gin-gonic/gin"
	"time"
)

func main() {
	r := gin.Default()

	authMiddleware, err := jwt.New(&jwt.GinJWTMiddleware{
		Realm:       "test zone",
		Key:         []byte("secret key"),
		Timeout:     time.Hour,
		MaxRefresh:  time.Hour,
		IdentityKey: "id",
		PayloadFunc: func(data interface{}) jwt.MapClaims {
			if v, ok := data.(*User); ok {
				return jwt.MapClaims{
					"id": v.ID,
				}
			}
			return jwt.MapClaims{}
		},
		IdentityHandler: func(c *gin.Context) interface{} {
			claims := jwt.ExtractClaims(c)
			return &User{
				ID: claims["id"].(string),
			}
		},
		Authenticator: func(c *gin.Context) (interface{}, error) {
			// 用户名和密码验证逻辑
		},
		Authorizator: func(data interface{}, c *gin.Context) bool {
			// 授权逻辑
		},
		Unauthorized: func(c *gin.Context, code int, message string) {
			// 未授权处理逻辑
		},
	})

	if err != nil {
		panic("JWT Error:" + err.Error())
	}

	r.POST("/login", authMiddleware.LoginHandler)

	auth := r.Group("/auth")
	auth.Use(authMiddleware.MiddlewareFunc())
	{
		auth.GET("/refresh_token", authMiddleware.RefreshHandler)
	}

	r.Run(":8080")
}
```

### 83. 在Gin中使用HTTPS客户端证书认证

你可以通过配置TLS来实现客户端证书认证。

```go
package main

import (
	"crypto/tls"
	"github.com/gin-gonic/gin"
	"log"
)

func main() {
	r := gin.Default()

	tlsConfig := &tls.Config{
		ClientAuth: tls.RequireAndVerifyClientCert,
		ClientCAs:  loadCA(),
	}

	server := &http.Server{
		Addr:      ":8443",
		Handler:   r,
		TLSConfig: tlsConfig,
	}

	r.GET("/", func(c *gin.Context) {
		c.String(200, "Client certificate authenticated.")
	})

	log.Fatal(server.ListenAndServeTLS("server.crt", "server.key"))
}

func loadCA() *x509.CertPool {
	pool := x509.NewCertPool()
	caCert, _ := ioutil.ReadFile("ca.crt")
	pool.AppendCertsFromPEM(caCert)
	return pool
}
```

### 84. 在Gin中使用自定义HTTP方法

你可以通过 `Handle` 方法来使用自定义HTTP方法。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()

	r.Handle("SEARCH", "/search", func(c *gin.Context) {
		c.String(200, "Custom SEARCH method")
	})

	r.Run(":8080")
}
```

### 85. 在Gin中使用自定义渲染器

你可以实现 `gin.Render` 接口来创建自定义渲染器。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

type CustomRender struct{}

func (r CustomRender) Render(http.ResponseWriter) error {
	// 自定义渲染逻辑
}

func (r CustomRender) WriteContentType(w http.ResponseWriter) {
	// 设置内容类型
}

func main() {
	r := gin.Default()

	r.GET("/", func(c *gin.Context) {
		c.Render(200, CustomRender{})
	})

	r.Run(":8080")
}
```

### 86. 在Gin中使用自定义数据绑定器

你可以实现 `binding.Binding` 接口来创建自定义数据绑定器。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"github.com/gin-gonic/gin/binding"
	"net/http"
)

type CustomBinder struct{}

func (b CustomBinder) Name() string {
	return "custom"
}

func (b CustomBinder) Bind(*http.Request, interface{}) error {
	// 自定义绑定逻辑
}

func main() {
	r := gin.Default()

	r.POST("/", func(c *gin.Context) {
		var obj MyStruct
		if err := c.ShouldBindWith(&obj, CustomBinder{}); err != nil {
			c.JSON(400, gin.H{"error": err.Error()})
			return
		}
		c.JSON(200, obj)
	})

	r.Run(":8080")
}
```

### 87. 在Gin中使用HTTP/2服务器推送

你可以使用HTTP/2的服务器推送特性来提高页面加载速度。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

func main() {
	r := gin.Default()

	r.GET("/", func(c *gin.Context) {
		pusher := c.Writer.Pusher()
		if pusher != nil {
			if err := pusher.Push("/static/css/main.css", nil); err != nil {
				log.Printf("Failed to push: %v", err)
			}
		}
		c.File("./views/index.html")
	})

	r.Static("/static", "./static")

	r.RunTLS(":443", "server.crt", "server.key")
}
```

### 88. 在Gin中使用Rate Limiting（速率限制）

通过速率限制，你可以限制特定时间段内的请求数量。可以使用第三方库，例如 `github.com/didip/tollbooth`，实现此功能。

https://github.com/didip/tollbooth

```go
package main

import (
	"github.com/didip/tollbooth"
	"github.com/didip/tollbooth/gin"
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()

	// 创建速率限制器，每秒2个请求
	limiter := tollbooth.NewLimiter(2, nil)

	r.GET("/", tollbooth_gin.LimitHandler(limiter), func(c *gin.Context) {
		c.String(200, "Welcome, rate limited!")
	})

	r.Run(":8080")
}
```

### 89. 在Gin中使用HTTP Basic Authentication

Gin提供了内置支持HTTP Basic Authentication，你可以使用它来保护路由。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()

	authorized := r.Group("/", gin.BasicAuth(gin.Accounts{
		"username": "password",
	}))

	authorized.GET("/admin", func(c *gin.Context) {
		c.String(200, "Authenticated!")
	})

	r.Run(":8080")
}
```

### 90. 在Gin中使用ETag支持

通过使用ETag，你可以减少不必要的响应。Gin有内置的ETag支持。

```go
package main

import (
	"github.com/gin-contrib/etag"
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()
	r.Use(etag.Default())

	r.GET("/", func(c *gin.Context) {
		c.String(200, "ETag enabled response")
	})

	r.Run(":8080")
}
```

### 91. 在Gin中使用HTTPS和HTTP/2 Cleartext（H2C）

你可以在Gin中同时使用HTTPS和HTTP/2 Cleartext（H2C）。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"golang.org/x/net/http2"
	"golang.org/x/net/http2/h2c"
	"net/http"
)

func main() {
	r := gin.Default()

	r.GET("/", func(c *gin.Context) {
		c.String(200, "Hello, HTTP/2 Cleartext (H2C)!")
	})

	server := &http.Server{
		Addr:    ":8080",
		Handler: h2c.NewHandler(r, &http2.Server{}),
	}

	server.ListenAndServeTLS("server.crt", "server.key")
}
```

### 92. 在Gin中创建下载文件响应

你可以使用Gin来创建一个文件下载响应。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()

	r.GET("/download", func(c *gin.Context) {
		c.FileAttachment("file/to/download.zip", "filename.zip")
	})

	r.Run(":8080")
}
```

### 93. 在Gin中使用连接数据库

你可以在Gin中使用数据库连接，例如使用MySQL。

```go
package main

import (
	"database/sql"
	"github.com/gin-gonic/gin"
	_ "github.com/go-sql-driver/mysql"
)

func main() {
	db, err := sql.Open("mysql", "user:password@tcp(localhost:3306)/dbname")
	if err != nil {
		panic(err)
	}
	defer db.Close()

	r := gin.Default()

	r.GET("/users/:id", func(c *gin.Context) {
		var name string
		id := c.Param("id")
		row := db.QueryRow("SELECT name FROM users WHERE id = ?", id)
		err := row.Scan(&name)
		if err != nil {
			c.JSON(404, gin.H{"error": "User not found"})
			return
		}
		c.JSON(200, gin.H{"name": name})
	})

	r.Run(":8080")
}
```

### 94. 在Gin中使用Prometheus监控

通过使用 `github.com/zsais/go-gin-prometheus`，你可以将Prometheus监控添加到Gin应用程序中。

https://github.com/zsais/go-gin-prometheus

```go
package main

import (
	"github.com/gin-gonic/gin"
	"github.com/zsais/go-gin-prometheus"
)

func main() {
	r := gin.Default()

	p := ginprometheus.NewPrometheus("gin")
	p.Use(r)

	r.GET("/", func(c *gin.Context) {
		c.String(200, "Hello, Prometheus!")
	})

	r.Run(":8080")
}
```

### 95. 在Gin中使用GRPC

你可以在Gin中与gRPC服务协同工作。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"google.golang.org/grpc"
	"log"
)

func main() {
	conn, err := grpc.Dial("localhost:50051", grpc.WithInsecure())
	if err != nil {
		log.Fatalf("did not connect: %v", err)
	}
	defer conn.Close()

	r := gin.Default()

	r.GET("/grpc_service", func(c *gin.Context) {
		// 与gRPC服务通信的逻辑
	})

	r.Run(":8080")
}
```

### 96. 在Gin中使用Server Sent Events (SSE)

Server Sent Events (SSE)允许服务器向客户端推送事件。Gin具有内置的SSE支持。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"time"
)

func main() {
	r := gin.Default()

	r.GET("/events", func(c *gin.Context) {
		c.Stream(func(w chan bool) {
			for {
				c.SSEvent("message", "ping")
				time.Sleep(time.Second)
			}
		})
	})

	r.Run(":8080")
}
```

### 97. 在Gin中实现OAuth2身份验证

你可以使用OAuth2来保护Gin应用程序的路由。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"golang.org/x/oauth2"
	"golang.org/x/oauth2/github"
)

var (
	oauth2Config = oauth2.Config{
		ClientID:     "client_id",
		ClientSecret: "client_secret",
		Scopes:       []string{"scope1", "scope2"},
		Endpoint:     github.Endpoint,
		RedirectURL:  "http://localhost:8080/auth/callback",
	}
)

func main() {
	r := gin.Default()

	r.GET("/auth", func(c *gin.Context) {
		authURL := oauth2Config.AuthCodeURL("", oauth2.AccessTypeOffline)
		c.Redirect(302, authURL)
	})

	r.GET("/auth/callback", func(c *gin.Context) {
		code := c.DefaultQuery("code", "")
		token, err := oauth2Config.Exchange(c, code)
		if err != nil {
			c.JSON(400, gin.H{"error": err.Error()})
			return
		}
		c.JSON(200, gin.H{"token": token.AccessToken})
	})

	r.Run(":8080")
}
```

### 98. 在Gin中使用自定义Recovery中间件

你可以使用自定义Recovery中间件来捕获并处理Gin中的panic。

```go
package main

import (
	"github.com/gin-gonic/gin"
)

func customRecovery() gin.HandlerFunc {
	return func(c *gin.Context) {
		defer func() {
			if err := recover(); err != nil {
				c.JSON(500, gin.H{"error": "Internal Server Error"})
				c.Abort()
			}
		}()
		c.Next()
	}
}

func main() {
	r := gin.New()
	r.Use(customRecovery())

	r.GET("/panic", func(c *gin.Context) {
		panic("test panic")
	})

	r.Run(":8080")
}
```

### 99. 在Gin中使用中间件设置跨域资源共享（CORS）头

你可以通过自定义中间件来设置CORS头。

```go
package main

import "github.com/gin-gonic/gin"

func CORSMiddleware() gin.HandlerFunc {
	return func(c *gin.Context) {
		c.Writer.Header().Set("Access-Control-Allow-Origin", "*")
		c.Writer.Header().Set("Access-Control-Allow-Credentials", "true")
		c.Writer.Header().Set("Access-Control-Allow-Headers", "Content-Type, Content-Length, Accept-Encoding, X-CSRF-Token, Authorization, accept, origin, Cache-Control, X-Requested-With")
		c.Writer.Header().Set("Access-Control-Allow-Methods", "POST, OPTIONS, GET, PUT, DELETE")

		if c.Request.Method == "OPTIONS" {
			c.AbortWithStatus(204)
			return
		}

		c.Next()
	}
}

func main() {
	r := gin.Default()
	r.Use(CORSMiddleware())

	r.GET("/", func(c *gin.Context) {
		c.String(200, "CORS configured")
	})

	r.Run(":8080")
}
```

### 100. 在Gin中使用自定义日志格式

你可以使用Gin的日志中间件来自定义日志格式。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"time"
)

func logger() gin.HandlerFunc {
	return func(c *gin.Context) {
		start := time.Now()
		path := c.Request.URL.Path
		raw := c.Request.URL.RawQuery

		c.Next()

		timestamp := time.Now()
		latency := timestamp.Sub(start)
		clientIP := c.ClientIP()
		method := c.Request.Method
		statusCode := c.Writer.Status()

		log := timestamp.Format("2006-01-02 15:04:05") +
			" | " + latency.String() +
			" | " + clientIP +
			" | " + method +
			" | " + path

		if raw != "" {
			log += "?" + raw
		}

		log += " | " + statusCode

		c.Log(log)
	}
}

func main() {
	r := gin.New()
	r.Use(logger())

	r.GET("/", func(c *gin.Context) {
		c.String(200, "Hello, custom log format!")
	})

	r.Run(":8080")
}
```

### 101. 使用WebSockets与Gin

你可以在Gin中集成WebSockets来实现双向通信。为此，可以使用 `golang/net/websocket` 库。

https://pkg.go.dev/golang.org/x/net/websocket

```go
package main

import (
	"github.com/gin-gonic/gin"
	"golang.org/x/net/websocket"
)

func main() {
	r := gin.Default()
	r.GET("/ws", func(c *gin.Context) {
		handler := websocket.Handler(func(ws *websocket.Conn) {
			defer ws.Close()
			for {
				// Echo the data received on the WebSocket
				msg := make([]byte, 512)
				n, err := ws.Read(msg)
				if err != nil {
					break
				}
				ws.Write(msg[:n])
			}
		})
		handler.ServeHTTP(c.Writer, c.Request)
	})
	r.Run(":8080")
}
```

### 102. 使用Gin上传多个文件

在Gin中，你可以轻松处理多文件上传。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"log"
)

func main() {
	r := gin.Default()

	r.POST("/upload", func(c *gin.Context) {
		form, _ := c.MultipartForm()
		files := form.File["upload[]"]

		for _, file := range files {
			log.Println(file.Filename)
			c.SaveUploadedFile(file, file.Filename)
		}
		c.String(200, "%d files uploaded!", len(files))
	})

	r.Run(":8080")
}
```

### 103. 使用Gin自定义HTTP2.0配置

你可以使用Gin自定义HTTP/2.0的配置。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"golang.org/x/net/http2"
	"net/http"
)

func main() {
	r := gin.Default()

	server := &http.Server{
		Addr:    ":8080",
		Handler: r,
		TLSConfig: ... , // your TLS config
	}
	http2.ConfigureServer(server, &http2.Server{})

	server.ListenAndServeTLS("path_to_tls.crt", "path_to_tls.key")
}
```

### 104. 使用Gin的嵌套路由组

在Gin中，你可以创建嵌套的路由组来更好地组织你的API。

```go
package main

import (
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()

	v1 := r.Group("/v1")
	{
		posts := v1.Group("/posts")
		{
			posts.GET("", listPosts)
			posts.GET("/:id", getPost)
		}
	}

	r.Run(":8080")
}

func listPosts(c *gin.Context) {
	// List posts
}

func getPost(c *gin.Context) {
	// Get a specific post
}
```

### 105. 使用Gin限制请求主体大小

你可以设置一个中间件来限制请求主体的大小。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

func MaxAllowed(n int64) gin.HandlerFunc {
	return func(c *gin.Context) {
		c.Request.Body = http.MaxBytesReader(c.Writer, c.Request.Body, n)
		c.Next()
	}
}

func main() {
	r := gin.Default()
	r.Use(MaxAllowed(2 * 1024 * 1024))  // 2 MB
	r.POST("/upload", uploadHandler)
	r.Run(":8080")
}

func uploadHandler(c *gin.Context) {
	// Handle the upload
}
```

### 106. 使用Gin处理文件下载

Gin允许你方便地处理文件下载请求。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()

	r.GET("/download", func(c *gin.Context) {
		c.File("/path/to/file")
	})

	r.Run(":8080")
}
```

### 107. 在Gin中使用模板函数

使用模板函数可以在HTML模板中使用自定义逻辑。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"html/template"
)

func formatAsDate(t int64) string {
	return "formatted date"
}

func main() {
	r := gin.Default()
	r.SetFuncMap(template.FuncMap{
		"formatAsDate": formatAsDate,
	})
	r.LoadHTMLFiles("templates/template1.html", "templates/template2.html")

	r.GET("/", func(c *gin.Context) {
		c.HTML(200, "template1.html", gin.H{
			"myTime": time.Now().Unix(),
		})
	})

	r.Run(":8080")
}
```

### 108. 使用静态文件中间件

你可以使用Gin提供的中间件来服务静态文件。

```go
package main

import (
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()

	r.Static("/static", "./static_files")

	r.Run(":8080")
}
```

### 109. 使用Gin进行JSONP请求

JSONP是一个请求技巧，允许跨域请求。Gin提供了对JSONP的内置支持。

```go
package main

import (
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()

	r.GET("/JSONP?callback=x", func(c *gin.Context) {
		data := map[string]interface{}{
			"foo": "bar",
		}
		c.JSONP(http.StatusOK, data)
	})

	r.Run(":8080")
}
```

### 110. 使用Gin的XML渲染

除了JSON之外，Gin还支持XML响应。

```go
package main

import (
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()

	r.GET("/xml", func(c *gin.Context) {
		c.XML(200, gin.H{"message": "hey", "status": http.StatusOK})
	})

	r.Run(":8080")
}
```

### 111. 使用Gin的AsciiJSON渲染

有时，你可能想使用AsciiJSON作为响应。

```go
package main

import (
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()

	r.GET("/asciijson", func(c *gin.Context) {
		data := map[string]interface{}{
			"lang": "GO语言",
			"tag":  "<br>",
		}
		c.AsciiJSON(200, data)
	})

	r.Run(":8080")
}
```

### 112. 使用Gin的纯文本渲染

你可以使用Gin发送纯文本响应。

```go
package main

import (
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()

	r.GET("/text", func(c *gin.Context) {
		c.String(200, "plain text")
	})

	r.Run(":8080")
}
```

### 113. 使用Gin的HTML渲染
Gin允许你使用Go的模板语言渲染HTML页面。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()

	r.LoadHTMLGlob("templates/*")

	r.GET("/index", func(c *gin.Context) {
		c.HTML(200, "index.html", gin.H{
			"title": "My Home Page",
		})
	})

	r.Run(":8080")
}
```

### 114. 使用Gin的SecureCookie
你可以使用Gin来创建和读取加密的cookies。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()

	r.GET("/cookie", func(c *gin.Context) {
		// 设置加密的cookie
		c.SetSecureCookie("gin_cookie", "cookie_name", "cookie_value", "/", "localhost", 86400, true, true)

		// 读取加密的cookie
		value, err := c.SecureCookie("gin_cookie", "cookie_name")
		if err == nil {
			c.String(200, "Cookie value: %s", value)
		}
	})

	r.Run(":8080")
}
```

### 115. 使用Gin处理Multipart/Urlencoded表单
你可以使用Gin来处理 `Multipart/Urlencoded` 形式的数据。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()

	r.POST("/form", func(c *gin.Context) {
		message := c.PostForm("message")
		nick := c.DefaultPostForm("nick", "anonymous")

		c.JSON(200, gin.H{
			"status":  "posted",
			"message": message,
			"nick":    nick,
		})
	})

	r.Run(":8080")
}
```

### 116. 使用Gin的中间件绑定数据到结构体
你可以使用Gin的中间件将请求数据绑定到结构体。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

type Login struct {
	User     string `form:"user" json:"user" xml:"user"  binding:"required"`
	Password string `form:"password" json:"password" xml:"password" binding:"required"`
}

func main() {
	r := gin.Default()

	r.POST("/login", func(c *gin.Context) {
		var login Login

		if err := c.ShouldBind(&login); err == nil {
			if login.User == "user" && login.Password == "password" {
				c.JSON(http.StatusOK, gin.H{"status": "you are logged in"})
			} else {
				c.JSON(http.StatusUnauthorized, gin.H{"status": "unauthorized"})
			}
		} else {
			c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
		}
	})

	r.Run(":8080")
}
```

### 117. 使用Gin的重定向功能
Gin使重定向变得非常简单。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()

	r.GET("/redirect", func(c *gin.Context) {
		c.Redirect(http.StatusMovedPermanently, "https://www.example.com/")
	})

	r.Run(":8080")
}
```

### 118. 使用Gin自定义HTTP响应
你可以使用Gin自定义HTTP响应。

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

func main() {
	r := gin.Default()

	r.GET("/response", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{
			"message": "Hello, custom response!",
		})
	})

	r.Run(":8080")
}
```

### 119. 使用Gin的路由参数
Gin允许你在路由中使用参数。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()

	r.GET("/users/:name", func(c *gin.Context) {
		name := c.Param("name")
		c.String(http.StatusOK, "Hello %s", name)
	})

	r.Run(":8080")
}
```

### 120. 使用Gin的路由通配符
你可以使用通配符来捕获路由中的多个参数。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()

	r.GET("/files/*filepath", func(c *gin.Context) {
		path := c.Param("filepath")
		c.String(http.StatusOK, "You accessed path: %s", path)
	})

	r.Run(":8080")
}
```

### 121. 使用Gin进行分组路由的认证
你可以为特定的路由组添加认证。

```go
package main

import (
	"github.com/gin-gonic/gin"
)

func AuthRequired() gin.HandlerFunc {
	return func(c *gin.Context) {
		token := c.GetHeader("Authorization")
		if token != "my-secret-token" {
			c.JSON(401, gin.H{"error": "unauthorized"})
			c.Abort()
			return
		}
		c.Next()
	}
}

func main() {
	r := gin.Default()

	private := r.Group("/private")
	private.Use(AuthRequired())
	{
		private.GET("/profile", func(c *gin.Context) {
			c.String(200, "Hello authorized user!")
		})
	}

	r.Run(":8080")
}
```

### 122. 使用Gin中间件记录请求和响应
你可以使用中间件来记录请求和响应数据。

```go
package main

import (
	"bytes"
	"github.com/gin-gonic/gin"
	"io"
	"io/ioutil"
)

func Logger() gin.HandlerFunc {
	return func(c *gin.Context) {
		buf, _ := ioutil.ReadAll(c.Request.Body)
		rdr1 := ioutil.NopCloser(bytes.NewBuffer(buf))
		rdr2 := ioutil.NopCloser(bytes.NewBuffer(buf))
		// 使用rdr1进行读取操作
		body, _ := ioutil.ReadAll(rdr1)
		c.Request.Body = rdr2
		c.Next()
		response, _ := c.Get("response")
		// 记录请求和响应
		println("Request:", string(body))
		println("Response:", response.(string))
	}
}

func main() {
	r := gin.Default()
	r.Use(Logger())

	r.POST("/test", func(c *gin.Context) {
		response := gin.H{"hello": "world"}
		c.Set("response", response)
		c.JSON(200, response)
	})

	r.Run(":8080")
}
```

### 123. 使用Gin进行请求和响应的数据绑定
Gin提供了简单的方法来绑定请求和响应的数据。

```go
package main

import (
	"github.com/gin-gonic/gin"
)

type UserInput struct {
	Username string `json:"username" binding:"required"`
	Age      int    `json:"age"`
}

func main() {
	r := gin.Default()

	r.POST("/user", func(c *gin.Context) {
		var input UserInput
		if err := c.ShouldBindJSON(&input); err != nil {
			c.JSON(400, gin.H{"error": err.Error()})
			return
		}

		c.JSON(200, gin.H{"user": input.Username, "age": input.Age})
	})

	r.Run(":8080")
}
```

### 124. 使用Gin生成URL
你可以使用Gin的方法生成URL。

```go
package main

import (
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()

	r.GET("/post/:id", func(c *gin.Context) {
		c.String(200, "Post ID: %s", c.Param("id"))
	})

	url := r.Group("/url")
	{
		url.GET("/generate", func(c *gin.Context) {
			c.String(200, c.FullPath())
		})
	}

	r.Run(":8080")
}
```

### 125. 使用Gin的默认路由
你可以为未匹配的路由设置默认响应。

```go
package main

import (
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()

	r.GET("/hello", func(c *gin.Context) {
		c.String(200, "Hello World!")
	})

	r.NoRoute(func(c *gin.Context) {
		c.JSON(404, gin.H{"message": "Not Found"})
	})

	r.Run(":8080")
}
```

### 126. 使用Gin的重定向路由
你可以在Gin中设置重定向路由。

```go
package main

import (
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()

	r.GET("/old-path", func(c *gin.Context) {
		c.Redirect(301, "/new-path")
	})

	r.GET("/new-path", func(c *gin.Context) {
		c.String(200, "New Path")
	})

	r.Run(":8080")
}
```

