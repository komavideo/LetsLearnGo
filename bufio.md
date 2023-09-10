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

### 使用 ReadSlice：
此方法会返回缓冲数据的切片，直到遇到分隔符为止，但这种方法的结果是易失的。如果之后进行了其他读取操作，返回的切片可能会被修改。

```go
package main

import (
	"bufio"
	"fmt"
	"strings"
)

func main() {
	reader := bufio.NewReader(strings.NewReader("Go is fun!"))
	line, _ := reader.ReadSlice(' ')
	fmt.Printf("Read slice: %s\n", line)  // 输出 "Go"
}
```

### 按段读取大文件：
在处理非常大的文件时，我们可能想要按固定大小的段来读取，而不是按行。以下是一个例子：

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

	reader := bufio.NewReader(file)
	chunk := make([]byte, 1024)  // 定义每个块的大小

	for {
		n, err := reader.Read(chunk)
		if err != nil {
			break
		}
		fmt.Printf("Read %d bytes\n", n)
	}
}
```

### 为 Scanner 创建自定义分割函数：
我们之前看到了预定义的 `ScanWords` 分割函数。但你也可以为 `Scanner` 创建自己的分割函数。以下是一个简单的例子，该函数将字符串按照两个字节进行分割：

```go
package main

import (
	"bufio"
	"bytes"
	"fmt"
)

func TwoByteSplit(data []byte, atEOF bool) (advance int, token []byte, err error) {
	if atEOF && len(data) == 0 {
		return 0, nil, nil
	}

	if len(data) >= 2 {
		return 2, data[0:2], nil
	}

	return 0, data, bufio.ErrFinalToken
}

func main() {
	scanner := bufio.NewScanner(bytes.NewReader([]byte("abcdef")))
	scanner.Split(TwoByteSplit)

	for scanner.Scan() {
		fmt.Println(scanner.Text())
	}
}
```

### ReadFrom 和 WriteTo 方法：
如果你正在使用 `bufio.Reader` 或 `bufio.Writer` ，并希望从其他 `io.Reader` 或向 `io.Writer` 传输数据，可以使用这些方法。

```go
package main

import (
	"bufio"
	"os"
	"strings"
)

func main() {
	reader := bufio.NewReader(strings.NewReader("Go is amazing!"))
	file, err := os.Create("output.txt")
	if err != nil {
		panic(err)
	}
	defer file.Close()

	writer := bufio.NewWriter(file)
	reader.WriteTo(writer)
	writer.Flush()
}
```

### 合并多个 Writers：
有时，你可能想要将输出写入多个地方。为此，可以使用 `io.MultiWriter` ，但与 `bufio.Writer` 结合使用可以提高效率。

```go
package main

import (
	"bufio"
	"io"
	"os"
)

func main() {
	file1, err := os.Create("output1.txt")
	if err != nil {
		panic(err)
	}
	defer file1.Close()

	file2, err := os.Create("output2.txt")
	if err != nil {
		panic(err)
	}
	defer file2.Close()

	multi := io.MultiWriter(file1, file2)
	writer := bufio.NewWriter(multi)
	writer.WriteString("Hello to multiple files!")
	writer.Flush()
}
```

### 使用 Peek 预览缓冲区内容：
有时你可能希望预览来自 `Reader` 的内容，但不真正地从中读取内容。使用 `Peek` 方法可以实现这一目标：

```go
package main

import (
	"bufio"
	"fmt"
	"strings"
)

func main() {
	r := strings.NewReader("Go is versatile!")
	reader := bufio.NewReader(r)
	peek, _ := reader.Peek(5)
	fmt.Printf("Peeked: %s\n", peek)

	data, _ := reader.ReadString(' ')
	fmt.Printf("Read: %s\n", data)
}
```

### 为 Scanner 使用最大令牌大小：
如果你处理的文本令牌（如单词或行）可能非常大，你可以使用 `Scanner.Buffer` 来调整最大令牌大小：

```go
package main

import (
	"bufio"
	"fmt"
	"strings"
)

func main() {
	const input = "This is a very long line which exceeds default buffer size for bufio.Scanner"
	scanner := bufio.NewScanner(strings.NewReader(input))

	buf := make([]byte, 2)
	scanner.Buffer(buf, bufio.MaxScanTokenSize*2)

	for scanner.Scan() {
		fmt.Println(scanner.Text())
	}
}
```

### 并发安全：
需要注意的是，`bufio` 的 `Reader` 和 `Writer` 并不是并发安全的。如果你想从多个协程同时读取或写入，则需要自己添加锁来同步操作。

### 使用 bufio 进行网络编程：
在网络编程中，`bufio` 也很常用，因为它可以缓冲数据并提高 `I/O` 效率：

```go
package main

import (
	"bufio"
	"fmt"
	"net"
)

func handleConnection(conn net.Conn) {
	defer conn.Close()

	reader := bufio.NewReader(conn)
	for {
		line, err := reader.ReadString('\n')
		if err != nil {
			break
		}
		fmt.Printf("Received: %s", line)
	}
}

func main() {
	ln, err := net.Listen("tcp", ":8080")
	if err != nil {
		panic(err)
	}
	defer ln.Close()

	for {
		conn, err := ln.Accept()
		if err != nil {
			fmt.Println("Connection error:", err)
			continue
		}
		go handleConnection(conn)
	}
}
```

### 处理 bufio.ErrTooLong 错误：
当扫描的令牌超过最大令牌大小时，会得到一个 `bufio.ErrTooLong` 错误。当处理未知大小的输入数据时，特别是来自外部来源的数据时，很重要的一点是始终检查这个错误，并相应地处理它。

### 使用 Scanner 读取文件：
虽然前面我们已经介绍过了 `Scanner` 的使用，但在实际中，读取文件是一个非常常见的应用场景。

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	file, err := os.Open("sample.txt")
	if err != nil {
		panic(err)
	}
	defer file.Close()

	scanner := bufio.NewScanner(file)
	for scanner.Scan() {
		fmt.Println(scanner.Text())
	}

	if err := scanner.Err(); err != nil {
		fmt.Fprintln(os.Stderr, "reading file:", err)
	}
}
```

### 使用 NewWriterSize 和 NewReaderSize 指定缓冲区大小：
你可以使用这两个函数为读写器指定一个自定义的缓冲区大小：

```go
r := strings.NewReader("Hello, World!")
reader := bufio.NewReaderSize(r, 4) // 使用4字节的缓冲区

file, _ := os.Create("output.txt")
writer := bufio.NewWriterSize(file, 4)  // 使用4字节的缓冲区
```

### 在网络编程中使用 ReadBytes 和 WriteBytes：
在网络编程中，你可能会需要按字节读取和写入数据。以下是如何实现这一点：

```go
func handleConnection(conn net.Conn) {
	defer conn.Close()

	reader := bufio.NewReader(conn)
	data, _ := reader.ReadBytes('\n')
	fmt.Println("Received:", string(data))

	writer := bufio.NewWriter(conn)
	writer.Write([]byte("Acknowledge\n"))
	writer.Flush()
}
```

### 处理UTF-8输入：
`bufio.Reader` 的 `ReadRune` 方法允许你按UTF-8字符读取输入：

```go
package main

import (
	"bufio"
	"fmt"
	"strings"
)

func main() {
	r := strings.NewReader("Hello, 世界!")
	reader := bufio.NewReader(r)
	for {
		runeValue, _, err := reader.ReadRune()
		if err != nil {
			break
		}
		fmt.Printf("%#U\n", runeValue)
	}
}
```

### 在 Writer 中使用 Available 和 Buffered 方法：
`Available` 方法返回缓冲区中未使用的字节数，而 `Buffered` 返回已写入缓冲区但尚未刷新到底层 `io.Writer` 的字节数。

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	file, _ := os.Create("bufferinfo.txt")
	defer file.Close()

	writer := bufio.NewWriter(file)
	fmt.Println("Available bytes:", writer.Available())

	writer.WriteString("Hello, World!")
	fmt.Println("Buffered bytes:", writer.Buffered())

	writer.Flush()
}
```

### 使用 Discard 快速丢弃数据：
如果你只想从 `Reader` 丢弃一定数量的数据，而不需要实际读取它，你可以使用 `Discard` 函数。例如，从当前位置开始丢弃10个字节：

```go
reader.Discard(10)
```

### 在多个 Scanner 之间切换分割函数：
根据输入数据的特点，你可能需要在不同的扫描模式之间切换：

```go
scanner := bufio.NewScanner(strings.NewReader("Line 1\nLine 2\nWord1 Word2"))
scanner.Scan()
fmt.Println(scanner.Text())  // 输出 "Line 1"

scanner.Scan()
fmt.Println(scanner.Text())  // 输出 "Line 2"

scanner.Split(bufio.ScanWords)  // 切换到按单词扫描
scanner.Scan()
fmt.Println(scanner.Text())  // 输出 "Word1"
```

### 优雅地处理长行：
当使用 `Scanner` 读取非常长的行时，你可能会遇到 `bufio.ErrTooLong`。为了处理这种情况，你可以逐步增加缓冲区大小：

```go
const initialBufSize = 4096
scanner := bufio.NewScanner(r)
buf := make([]byte, 0, initialBufSize)
scanner.Buffer(buf, bufio.MaxScanTokenSize*10)  // 允许最大10倍于默认大小的令牌
```

### 使用 Reset 重置 Reader 或 Writer：
如果你希望使用相同的 `Reader` 或 `Writer` 读取或写入不同的数据源或目标，可以使用 `Reset` 函数：

```go
r1 := strings.NewReader("Source 1")
r2 := strings.NewReader("Source 2")

reader := bufio.NewReader(r1)
data, _ := reader.Peek(8)
fmt.Println(string(data))  // 输出 "Source 1"

reader.Reset(r2)
data, _ = reader.Peek(8)
fmt.Println(string(data))  // 输出 "Source 2"
```

### 为写入操作设置超时：
虽然 `bufio.Writer` 本身没有超时功能，但你可以使用 `net.Conn` 的 `SetWriteDeadline` 方法为基于网络的写入设置超时：

```go
conn, _ := net.Dial("tcp", "example.com:80")
conn.SetWriteDeadline(time.Now().Add(5 * time.Second))

writer := bufio.NewWriter(conn)
_, err := writer.WriteString("Hello, World!")
if err != nil {
    fmt.Println("Failed to write:", err)
}
writer.Flush()
```

### 手动管理缓冲区大小：
使用 `bufio.ReaderSize` 和 `bufio.WriterSize`，你可以手动设置和管理缓冲区的大小。这可能对于调优某些特定的I/O-bound任务特别有用。

### 使用 ReadLine 处理没有换行符的行：
当输入数据可能不总是以换行符结束时，`ReadLine` 是一个非常有用的方法。它可以返回一个行，不论它是否以换行符结束。

```go
reader := bufio.NewReader(strings.NewReader("Line without newline"))
line, isPrefix, _ := reader.ReadLine()
fmt.Println(string(line), isPrefix)  // 输出 "Line without newline false"
```

