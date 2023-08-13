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







