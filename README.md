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

### 4. 变量和常量

在 Go 语言中，你可以使用 `var` 关键字声明变量，使用 `const` 关键字声明常量。

#### 变量

```go
var x int // 声明一个整型变量 x
x = 10   // 给 x 赋值

var y = 20 // 声明并初始化变量 y

z := 30 // 短变量声明，自动推断类型
```

#### 常量

```go
const pi = 3.1415 // 声明一个浮点型常量 pi
```

### 5. 控制结构

Go 语言支持常见的控制结构，如 `if`、`else`、`for` 循环等。

#### 条件语句

```go
x := 10
if x > 5 {
    fmt.Println("x 大于 5")
} else {
    fmt.Println("x 小于或等于 5")
}
```

#### 循环

```go
for i := 0; i < 10; i++ {
    fmt.Println(i)
}
```

### 6. 函数

你可以使用 `func` 关键字定义函数。

```go
func add(x int, y int) int {
    return x + y
}

result := add(10, 20)
fmt.Println("结果:", result)
```

### 7. 数组和切片

Go 语言中有数组和切片两种类似的数据结构。

#### 数组

数组长度固定。

```go
var numbers [5]int
numbers[0] = 1
numbers[1] = 2
```

#### 切片

切片长度可变。

```go
numbers := []int{1, 2, 3, 4}
numbers = append(numbers, 5)
```

### 8. 结构体

结构体用于定义复合类型。

```go
type Person struct {
    Name string
    Age  int
}

p := Person{Name: "Alice", Age: 30}
fmt.Println(p.Name)
```

### 9. 接口

Go 语言中的接口是一种类型系统的抽象，允许你定义一组方法，而不必明确指定实现它们的具体类型。

```go
type Writer interface {
    Write([]byte) (int, error)
}

func SaveFile(w Writer, data []byte) error {
    _, err := w.Write(data)
    return err
}

type FileWriter struct{}

func (fw FileWriter) Write(data []byte) (int, error) {
    // 文件写入逻辑
    return len(data), nil
}

func main() {
    fileWriter := FileWriter{}
    SaveFile(fileWriter, []byte("Hello, Go!"))
}
```

### 10. 并发

Go 语言的并发通过协程（goroutines）和通道（channels）实现。

#### 协程

你可以使用 `go` 关键字启动一个新的协程。

```go
func printMessage() {
    fmt.Println("Hello, Go!")
}

func main() {
    go printMessage()
}
```

#### 通道

通道用于在协程之间传递数据。

```go
func main() {
    ch := make(chan int)
    go func() {
        ch <- 42
    }()
    value := <-ch
    fmt.Println(value) // 输出 42
}
```

### 11. 错误处理

Go 语言中的错误通常作为函数的最后一个返回值返回。

```go
func divide(x, y float64) (float64, error) {
    if y == 0 {
        return 0, errors.New("division by zero")
    }
    return x / y, nil
}

func main() {
    result, err := divide(10, 0)
    if err != nil {
        fmt.Println("Error:", err)
        return
    }
    fmt.Println("Result:", result)
}
```

### 12. 测试

Go 语言具有内置的测试框架，你可以使用它编写和运行测试。

```go
func TestAdd(t *testing.T) {
    result := add(1, 2)
    if result != 3 {
        t.Errorf("Expected 3, got %d", result)
    }
}
```

运行测试：

```bash
go test
```
