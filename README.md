# LetsLearnGo
采用Go语言的项目越来越多，今后要需要好好学习Go语言。

# Go 语言教程

## 创建新项目

首先，你可以在你想要的位置创建一个新的目录来存放你的项目，并进入该目录。

```bash
mkdir myproject
cd myproject
```

## 初始化 Go Module

在项目目录中，运行以下命令来初始化 Go Module。这将创建一个名为 `go.mod` 的文件，其中包含项目的依赖信息。

```bash
go mod init myproject
```

## 添加第三方库

你可以通过运行 `go get` 命令来添加第三方库。

```bash
go get github.com/gin-gonic/gin
```

## 编写代码

现在你可以开始编写代码了。例如，你可以创建一个名为 `main.go` 的文件。

```go
package main

import "github.com/gin-gonic/gin"

func main() {
    r := gin.Default()
    r.GET("/", func(c *gin.Context) {
        c.String(200, "Hello, World!")
    })
    r.Run(":8080")
}
```

## 运行项目

由于你已经使用 Go Modules，所以你可以直接在项目目录中运行 `go run` 命令：

```bash
go run main.go
```

## 更多操作

你还可以执行以下操作：

- 使用 `go build` 编译项目
- 使用 `go test` 运行测试
- 使用 `go list -m all` 列出所有依赖
- 使用 `go mod tidy` 清理未使用的依赖
