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

## 从 Hello World 开始

Go 是一种编译型语言，以其简洁和高效而著名。让我们从最基本的 "Hello, World!" 程序开始。

### 1. 安装 Go

首先，你需要在你的电脑上安装 Go。你可以从 [Go 官方网站](https://golang.org/) 下载适合你操作系统的安装程序。

### 2. 创建一个简单的 "Hello, World!" 程序

在你的工作目录中创建一个名为 `hello.go` 的文件，并在其中输入以下代码：

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

解释一下这段代码：

- `package main` 定义了包的名字，`main` 包是 Go 应用程序的入口。
- `import "fmt"` 导入了 `fmt` 包，它包含了打印格式化输出的函数。
- `func main()` 定义了主函数，程序的执行从这里开始。
- `fmt.Println("Hello, World!")` 调用 `fmt` 包中的 `Println` 函数来打印字符串 "Hello, World!"。

### 3. 编译和运行程序

在命令行中，导航到包含 `hello.go` 文件的目录，并运行以下命令来编译程序：

```bash
go build hello.go
```

这将会在当前目录下创建一个名为 `hello`（在 Windows 上是 `hello.exe`）的可执行文件。

然后，你可以通过运行以下命令来运行程序：

```bash
./hello
```

或在 Windows 上：

```bash
hello.exe
```

你应该会看到 "Hello, World!" 被打印到控制台上。
