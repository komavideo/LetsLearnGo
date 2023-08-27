使用bufio库
==========

`bufio` 是 `Go` 语言的标准库之一，主要用于缓冲的`I/O`操作。这个库提供了从`io.Reader`和`io.Writer`读取和写入的缓冲功能，这样可以提高`I/O`操作的效率。

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

## Go 语言 bufio 库

### 使用 bufio.Reader 读取数据：
这是从 `os.Stdin`（标准输入）读取一行的基本方法。

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	reader := bufio.NewReader(os.Stdin)
	fmt.Print("请输入文本：")
	text, _ := reader.ReadString('\n')
	fmt.Println("你输入了:", text)
}
```
