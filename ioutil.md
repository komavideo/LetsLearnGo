使用ioutil库
============

`Go` 语言的 `ioutil` 包提供了一些非常实用的 `I/O` 工具函数。不过，值得注意的是，从 `Go 1.16` 开始，`ioutil` 包已被废弃，并推荐使用 `os` 和 `io` 包中的新函数来替代 `ioutil` 包中的大多数功能。

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

## Go 语言 ioutil 库

### 读取文件的全部内容

使用 `ioutil`：

```go
content, err := ioutil.ReadFile("filename.txt")
if err != nil {
    log.Fatal(err)
}
fmt.Println(string(content))
```

Go 1.16 替代方法：
```go
content, err := os.ReadFile("filename.txt")
if err != nil {
    log.Fatal(err)
}
fmt.Println(string(content))
```

### 写入文件

使用 `ioutil`：

```go
data := []byte("Hello, World!")
err := ioutil.WriteFile("filename.txt", data, 0644)
if err != nil {
    log.Fatal(err)
}
```

Go 1.16 替代方法：
```go
data := []byte("Hello, World!")
err := os.WriteFile("filename.txt", data, 0644)
if err != nil {
    log.Fatal(err)
}
```

### 读取目录

使用 `ioutil`：

```go
files, err := ioutil.ReadDir("/path/to/directory")
if err != nil {
    log.Fatal(err)
}
for _, file := range files {
    fmt.Println(file.Name())
}
```

Go 1.16 替代方法：
```go
dir, err := os.ReadDir("/path/to/directory")
if err != nil {
    log.Fatal(err)
}
for _, entry := range dir {
    fmt.Println(entry.Name())
}
```

### 使用 ioutil.TempFile 创建临时文件

使用 `ioutil`：

```go
tmpfile, err := ioutil.TempFile("", "example.*.txt")
if err != nil {
    log.Fatal(err)
}
defer os.Remove(tmpfile.Name())
fmt.Println(tmpfile.Name())
```

Go 1.16 替代方法：
```go
tmpfile, err := os.CreateTemp("", "example.*.txt")
if err != nil {
    log.Fatal(err)
}
defer os.Remove(tmpfile.Name())
fmt.Println(tmpfile.Name())
```

### 创建临时目录

使用 `ioutil`：

```go
tmpDir, err := ioutil.TempDir("", "example")
if err != nil {
    log.Fatal(err)
}
defer os.RemoveAll(tmpDir)
fmt.Println(tmpDir)
```

Go 1.16 替代方法：
```go
tmpDir, err := os.MkdirTemp("", "example")
if err != nil {
    log.Fatal(err)
}
defer os.RemoveAll(tmpDir)
fmt.Println(tmpDir)
```

### 读取全部内容从 io.Reader

使用 `ioutil`：

```go
reader := strings.NewReader("Hello, World!")
content, err := ioutil.ReadAll(reader)
if err != nil {
    log.Fatal(err)
}
fmt.Println(string(content))
```

Go 1.16 替代方法：
```go
reader := strings.NewReader("Hello, World!")
content, err := io.ReadAll(reader)
if err != nil {
    log.Fatal(err)
}
fmt.Println(string(content))
```

### Discard

`ioutil` 包有一个 `Discard` 变量，它是一个 `io.Writer`，对写入的任何数据都会被丢弃并返回成功。

使用 `ioutil`：

```go
writer := ioutil.Discard
_, err := writer.Write([]byte("Hello, World!"))
if err != nil {
    log.Fatal(err)
}
```

Go 1.16 替代方法：
```go
writer := io.Discard
_, err := writer.Write([]byte("Hello, World!"))
if err != nil {
    log.Fatal(err)
}
```

### 使用 NopCloser 将 io.Reader 转换为 io.ReadCloser

当你有一个 `io.Reader` 但需要一个 `io.ReadCloser` 时，可以使用 `ioutil.NopCloser`。它返回一个不执行任何操作的 `Close` 方法的 `ReadCloser`。

使用 `ioutil`：

```go
reader := strings.NewReader("Hello, World!")
readCloser := ioutil.NopCloser(reader)
// 使用 readCloser...
readCloser.Close()
```

Go 1.16 替代方法：
```go
reader := strings.NewReader("Hello, World!")
readCloser := io.NopCloser(reader)
// 使用 readCloser...
readCloser.Close()
```

这些是 `ioutil` 包的主要功能。如前所述，很多功能在 `Go 1.16` 以后已经迁移到了 `os` 和 `io` 包。所以，如果你正在编写新的代码，建议使用这些新的包和方法。但了解 `ioutil` 的功能对于读懂和维护旧的代码库仍然很有价值。
