strings的使用
=============

Go语言的strings库提供了很多用于操作字符串的有用功能。

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

## Go 语言 strings 库

### strings.Contains
检查一个字符串是否包含另一个字符串。

```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	fmt.Println(strings.Contains("Golang", "Go")) // true
}
```

### strings.Count
计算子字符串在字符串中出现的次数。

```go
fmt.Println(strings.Count("hello hello world", "hello")) // 2
```

### strings.HasPrefix 和 strings.HasSuffix
检查字符串是否以指定前缀或后缀开始。

```go
fmt.Println(strings.HasPrefix("Golang", "Go")) // true
fmt.Println(strings.HasSuffix("Golang", "lang")) // true
```

### strings.Index 和 strings.LastIndex
查找子字符串在字符串中的位置。

```go
fmt.Println(strings.Index("Golang", "lang")) // 2
fmt.Println(strings.LastIndex("Golang Go", "Go")) // 7
```

### strings.Replace
替换字符串中的子字符串。

```go
fmt.Println(strings.Replace("go go go", "go", "gone", 2)) // "gone gone go"
```

### strings.Split 和 strings.Join
分割字符串并将字符串数组连接起来。

```go
data := strings.Split("a,b,c", ",")
fmt.Println(data) // [a b c]
joined := strings.Join(data, ";")
fmt.Println(joined) // a;b;c
```

### strings.ToLower 和 strings.ToUpper
转换字符串的大小写。

```go
fmt.Println(strings.ToLower("GoLang")) // "golang"
fmt.Println(strings.ToUpper("GoLang")) // "GOLANG"
```

### strings.TrimSpace 和 strings.Trim
去除字符串的前导和尾部空白或指定的字符。

```go
fmt.Println(strings.TrimSpace("   GoLang   ")) // "GoLang"
fmt.Println(strings.Trim("!!!Hello World!!!", "!")) // "Hello World"
```

### strings.Repeat
重复字符串指定的次数。

```go
fmt.Println(strings.Repeat("Go", 3)) // GoGoGo
```

### strings.TrimLeft 和 strings.TrimRight
从字符串的左边或右边去除指定的字符。

```go
fmt.Println(strings.TrimLeft("!!!Hello!!!", "!")) // Hello!!!
fmt.Println(strings.TrimRight("!!!Hello!!!", "!")) // !!!Hello
```

### strings.Fields
分割字符串为一个由空白分隔的部分组成的切片。这是对常见的以空白字符为分隔符的`strings.Split`的简便方法。

```go
fmt.Println(strings.Fields("Go is fun")) // [Go is fun]
```

### strings.ReplaceAll
替换字符串中的所有子串匹配项。

```go
fmt.Println(strings.ReplaceAll("go go go", "go", "gone")) // gone gone gone
```

### strings.Title
将字符串中的每个单词的首字母转换为大写。

```go
fmt.Println(strings.Title("go is fun")) // Go Is Fun
```

### strings.TrimPrefix 和 strings.TrimSuffix
从字符串中去除前缀或后缀。

```go
fmt.Println(strings.TrimPrefix("GoLang", "Go")) // Lang
fmt.Println(strings.TrimSuffix("GoLang", "Lang")) // Go
```

### strings.Builder
用于有效地构建字符串。

```go
var builder strings.Builder
builder.WriteString("Go")
builder.WriteString(" is")
builder.WriteString(" fun!")
fmt.Println(builder.String()) // Go is fun!
```

