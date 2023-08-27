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

### 使用 bufio.Scanner 读取数据：
`bufio.Scanner` 是另一种从输入源读取数据的方法，经常用来按行读取。

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	scanner := bufio.NewScanner(os.Stdin)
	fmt.Print("请输入文本：")
	if scanner.Scan() {
		text := scanner.Text()
		fmt.Println("你输入了:", text)
	}
	if err := scanner.Err(); err != nil {
		fmt.Fprintln(os.Stderr, "error:", err)
	}
}
```

### 使用 bufio.Writer 写入数据：
这是一个将文本写入文件的示例：

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	file, err := os.Create("example.txt")
	if err != nil {
		panic(err)
	}
	defer file.Close()

	writer := bufio.NewWriter(file)
	writer.WriteString("这是一行示例文本。\n")
	writer.Flush()
}
```

### 按字节读取数据：
使用 `bufio.Reader`，你可以按字节读取数据，而不仅仅是按行。

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	reader := bufio.NewReader(os.Stdin)
	fmt.Print("请输入一个字符：")
	char, _, err := reader.ReadRune() // 读取一个Unicode字符
	if err != nil {
		panic(err)
	}
	fmt.Println("你输入的字符是:", char)
}
```

### 使用 Peek 预览数据：
有时，你可能只想查看数据，而不实际读取它。`Peek` 可以实现这一功能。

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
	bytes, _ := reader.Peek(5) // 预览前5个字节
	fmt.Println("前五个字节是:", string(bytes))
}
```

### 按行写入数据：
除了 `WriteString` 外，`bufio.Writer` 还提供了 `Write` 和 `WriteByte` 等方法。

```go
package main

import (
	"bufio"
	"os"
)

func main() {
	file, err := os.Create("example.txt")
	if err != nil {
		panic(err)
	}
	defer file.Close()

	writer := bufio.NewWriter(file)
	lines := []string{
		"这是第一行。",
		"这是第二行。",
		"这是第三行。",
	}

	for _, line := range lines {
		writer.WriteString(line + "\n")
	}
	writer.Flush()
}
```

### 创建带有指定缓冲区大小的 Reader 和 Writer：
默认情况下，`bufio.NewReader` 和 `bufio.NewWriter` 使用`4096`字节的缓冲区大小。但你可以指定其他的大小。

```go
package main

import (
	"bufio"
	"os"
)

func main() {
	file, err := os.Create("example.txt")
	if err != nil {
		panic(err)
	}
	defer file.Close()

	// 创建一个大小为2KB的缓冲写入器
	writer := bufio.NewWriterSize(file, 2048)
	writer.WriteString("这是一个示例文本。\n")
	writer.Flush()
}
```

