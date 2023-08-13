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

