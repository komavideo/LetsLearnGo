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

### 使用 Split 自定义扫描行为：
`bufio.Scanner` 默认是按行进行扫描的，但是你可以使用 `Split` 方法来自定义分隔函数，例如按空格分隔。

```go
package main

import (
	"bufio"
	"fmt"
	"os"
	"strings"
)

func main() {
	const input = "我 喜欢 学习 Go 语言"
	scanner := bufio.NewScanner(strings.NewReader(input))
	scanner.Split(bufio.ScanWords)  // 使用预定义的扫描单词函数

	for scanner.Scan() {
		fmt.Println(scanner.Text())
	}

	if err := scanner.Err(); err != nil {
		fmt.Fprintln(os.Stderr, "error:", err)
	}
}
```

### 使用 ReadBytes 和 ReadString 方法：
这两个方法允许你指定一个分隔符，直到遇到这个分隔符为止，它们都会读取数据。

```go
package main

import (
	"bufio"
	"fmt"
	"strings"
)

func main() {
	reader := bufio.NewReader(strings.NewReader("Hello, World!"))
	data, _ := reader.ReadBytes(',')
	fmt.Printf("Read data up to comma: %s\n", data)

	dataString, _ := reader.ReadString('!')
	fmt.Printf("Read the rest as a string: %s\n", dataString)
}
```

### 读取大文件：
当你需要处理大文件时，`bufio.Scanner` 和 `bufio.Reader` 是非常实用的。以下是如何使用 `bufio.Scanner` 逐行读取大文件的例子：

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	file, err := os.Open("largefile.txt")
	if err != nil {
		panic(err)
	}
	defer file.Close()

	scanner := bufio.NewScanner(file)
	lineNumber := 0
	for scanner.Scan() {
		lineNumber++
		fmt.Printf("Line %d: %s\n", lineNumber, scanner.Text())
	}

	if err := scanner.Err(); err != nil {
		fmt.Fprintln(os.Stderr, "error:", err)
	}
}
```
这种方式是高效的，因为它不会一次性加载整个文件到内存，而是逐行读取。

### 缓冲区溢出处理：
有时，可能会出现一行数据超过缓冲区大小的情况。在这种情况下，你可能需要为 `Scanner` 增加缓冲区大小。

```go
scanner := bufio.NewScanner(file)
buf := make([]byte, 0, 64*1024)
scanner.Buffer(buf, 10*1024*1024)  // 最大允许 10MB
```

### 使用 WriteRune 和 WriteByte：
你可以使用 `bufio.Writer` 来单独写入一个字节或`rune`。

```go
package main

import (
	"bufio"
	"os"
)

func main() {
	file, err := os.Create("runebyte.txt")
	if err != nil {
		panic(err)
	}
	defer file.Close()

	writer := bufio.NewWriter(file)
	writer.WriteRune('中') // 写入一个rune
	writer.WriteByte('\n')  // 写入一个字节
	writer.Flush()
}
```

### 从 Reader 中丢弃数据：
`bufio.Reader` 提供了一个 `Discard` 方法，使你可以轻松地丢弃一些不需要的数据。

```go
package main

import (
	"bufio"
	"fmt"
	"strings"
)

func main() {
	reader := bufio.NewReader(strings.NewReader("Hello, World!"))

	reader.Discard(7) // 丢弃前7个字节
	remaining, _ := reader.ReadString('!')
	fmt.Printf("After discarding: %s\n", remaining)
}
```

### 错误处理：
在使用 `bufio` 时，错误处理是非常重要的。你总是应该检查读取或写入操作后可能返回的错误。

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	file, err := os.Open("nonexistent.txt")
	if err != nil {
		panic(err)
	}
	defer file.Close()

	scanner := bufio.NewScanner(file)
	for scanner.Scan() {
		fmt.Println(scanner.Text())
	}

	if err := scanner.Err(); err != nil {
		fmt.Fprintln(os.Stderr, "reading standard input:", err)
	}
}
```

### 使用 bufio.NewReadWriter 创建一个读写器：
有时，当你在相同的连接或文件上进行读写操作时，可能会需要一个 `bufio.Reader` 和一个 `bufio.Writer`。`bufio` 提供了一个方便的方法来创建两者。

```go
package main

import (
	"bufio"
	"os"
)

func main() {
	file, err := os.Create("readwrite.txt")
	if err != nil {
		panic(err)
	}
	defer file.Close()

	readWriter := bufio.NewReadWriter(bufio.NewReader(file), bufio.NewWriter(file))
	// 使用 readWriter.Reader 和 readWriter.Writer 进行读写操作。
	// ... 其他代码
}
```

