使用strings库
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

### strings.Map
对字符串中的每个字符应用指定的函数。

```go
func toAsterisk(r rune) rune {
    return '*'
}

fmt.Println(strings.Map(toAsterisk, "GoLang")) // ******
```

### strings.ReplaceAll
替换所有子串出现的位置。

```go
fmt.Println(strings.ReplaceAll("hello, hello", "hello", "world")) // world, world
```

### strings.TrimFunc 和 strings.TrimLeftFunc 和 strings.TrimRightFunc
使用自定义函数去除字符。

```go
func filterFn(r rune) bool {
    return r == '!' || r == '?'
}

fmt.Println(strings.TrimFunc("!!Hello World?!", filterFn)) // Hello World
```

### strings.ContainsRune
检查字符串是否包含指定的rune字符。

```go
fmt.Println(strings.ContainsRune("Golang", 'G')) // true
```

### strings.Compare
比较两个字符串。如果两个字符串相等则返回0，如果第一个字符串小于第二个返回-1，否则返回1。不过直接使用==操作符来比较字符串更为直观和常见。

```go
fmt.Println(strings.Compare("a", "b")) // -1
fmt.Println(strings.Compare("a", "a")) // 0
fmt.Println(strings.Compare("b", "a")) // 1
```

### strings.EqualFold
执行不区分大小写的字符串比较。

```go
fmt.Println(strings.EqualFold("Go", "go")) // true
```

### strings.LastIndexAny 和 strings.IndexAny
查找字符集合中任何字符在字符串中的最后一个或第一个匹配的索引。

```go
fmt.Println(strings.IndexAny("chicken", "aeiouy"))      // 2
fmt.Println(strings.LastIndexAny("chicken", "aeiouy")) // 5
```

### strings.IndexRune
查找rune在字符串中的索引。

```go
fmt.Println(strings.IndexRune("chicken", 'k')) // 4
```

### strings.IndexFunc
使用函数查找符合条件的字符在字符串中的索引。

```go
func isVowel(r rune) bool {
    return strings.ContainsRune("aeiouAEIOU", r)
}

fmt.Println(strings.IndexFunc("Hello", isVowel)) // 1
```

### strings.NewReader
创建一个从字符串读取的新Reader。

```go
r := strings.NewReader("Golang")

buf := make([]byte, 4)
r.Read(buf)
fmt.Printf("%s\n", buf) // Gola
```

### strings.SplitN 和 strings.SplitAfter 和 strings.SplitAfterN
这些函数提供了更具体的字符串分割方式。

```go
fmt.Println(strings.SplitN("a,b,c", ",", 2))         // [a b,c]
fmt.Println(strings.SplitAfter("a,b,c", ","))        // [a, b, c]
fmt.Println(strings.SplitAfterN("a,b,c", ",", 2))    // [a, b,c]
```

### strings.Count
计算非重叠子串出现的次数。

```go
fmt.Println(strings.Count("cheese", "e")) // 3
```

### strings.HasPrefix 和 strings.HasSuffix
检查字符串是否以指定的前缀或后缀开始或结束。

```go
fmt.Println(strings.HasPrefix("Golang", "Go"))  // true
fmt.Println(strings.HasSuffix("Golang", "lang"))// true
```

### strings.Join
连接字符串切片中的元素。

```go
strs := []string{"Hello", "World"}
fmt.Println(strings.Join(strs, ", ")) // Hello, World
```

### strings.ToLowerSpecial 和 strings.ToUpperSpecial
使用特定的unicode.CaseRange进行大小写转换。

```go
import "unicode"

fmt.Println(strings.ToLowerSpecial(unicode.TurkishCase, "İSTANBUL")) // istanbul
```

### strings.ToTitle 和 strings.ToTitleSpecial
将字符串中的每个单词的首字母转换为大写或使用特定的unicode.CaseRange进行标题化。

```go
fmt.Println(strings.ToTitle("loud noises"))          // LOUD NOISES
fmt.Println(strings.ToTitleSpecial(unicode.TurkishCase, "loud noises")) // LOUD NOISES
```

### strings.TrimSpace
删除字符串的前导和尾部空格。

```go
fmt.Println(strings.TrimSpace(" \t\n Hello, World \n\t\r\n")) // Hello, World
```

### strings.LastIndexFunc
使用函数查找符合条件的字符在字符串中的最后一个索引。

```go
func isVowel(r rune) bool {
    return strings.ContainsRune("aeiouAEIOU", r)
}

fmt.Println(strings.LastIndexFunc("Hello", isVowel)) // 4
```

### strings.Reader结构体
这是一个从字符串读取的Reader。我们已经简要地提到了`strings.NewReader`，但`strings.Reader`本身提供了更多的方法，如 `Len()`, `Size()`, `ReadRune()`, `UnreadRune()`, `Seek()` 等。

```go
r := strings.NewReader("Golang")

ch, _, _ := r.ReadRune()
fmt.Println(ch)  // 'G' 

r.UnreadRune()  // unread last rune
ch, _, _ = r.ReadRune()
fmt.Println(ch)  // 'G' again
```

### strings.ContainsAny
检查字符串是否包含字符集中的任何字符。

```go
fmt.Println(strings.ContainsAny("Golang", "xyzG"))  // true
```

### strings.Count
计算子字符串在字符串中出现的次数。

```go
fmt.Println(strings.Count("hello world hello", "hello"))  // 2
```

### strings.EqualFold
执行大小写不敏感的字符串比较。

```go
fmt.Println(strings.EqualFold("GO", "go"))  // true
```

### strings.FieldFunc
将字符串分割为一个切片，分割点由函数决定。

```go
f := func(c rune) bool {
    return c == ',' || c == ';'
}
fmt.Println(strings.FieldsFunc("a,b;c", f))  // [a b c]
```

### strings.IndexByte
返回子字节在字符串中的第一个实例的索引，如果字符串中不存在子字节，则返回-1。

```go
fmt.Println(strings.IndexByte("Golang", 'a'))  // 3
```

### strings.LastIndexByte
返回子字节在字符串中的最后一个实例的索引。

```go
fmt.Println(strings.LastIndexByte("Golang", 'g'))  // 5
```

### strings.Replacer结构体
一个Replacer替换字符串中的一组字符串。它可以安全地被多个并发Goroutines使用。

```go
r := strings.NewReplacer("<", "&lt;", ">", "&gt;")
fmt.Println(r.Replace("<Hello>"))  // &lt;Hello&gt;
```

### strings.RuneCount 和 strings.RuneCountInString
计算字符串中的Unicode码点数量。

```go
fmt.Println(strings.RuneCountInString("Golang"))  // 6
```

### strings.Replace
在`s`中使用`new`替换前`n`个`old`出现的地方，如果`n`为`-1`则替换所有`old`。

```go
fmt.Println(strings.Replace("hello world hello world", "world", "gopher", 1))  // hello gopher hello world
```

### strings.Trim
删除字符串的前导和尾部字符集中的字符。

```go
fmt.Println(strings.Trim("!!!hello!!!", "!"))  // hello
```

### strings.TrimSpace
删除字符串的前导和尾部空白字符。

```go
fmt.Println(strings.TrimSpace(" \t\n Hello, World \n\t "))  // Hello, World
```

### strings.ToLower 和 strings.ToUpper
将字符串转换为全部小写或全部大写。

```go
fmt.Println(strings.ToLower("GOLANG"))  // golang
fmt.Println(strings.ToUpper("golang"))  // GOLANG
```

### strings.NewReplacer
创建并返回一个*Replacer。已在上面的例子中提到过这个函数，它用于创建字符串的替换映射。

```go
replacer := strings.NewReplacer("foo", "bar", "hello", "world")
result := replacer.Replace("foo hello")
fmt.Println(result)  // bar world
```

### strings.Reader.Size
返回原始字符串的长度。

```go
r := strings.NewReader("Golang")
fmt.Println(r.Size())  // 6
```

### strings.Reader.Reset
将Reader重置为从新的字符串`s`读取。

```go
r := strings.NewReader("Golang")
r.Reset("Hello")
buf := make([]byte, 5)
r.Read(buf)
fmt.Printf("%s\n", buf)  // Hello
```

### strings.Reader.UnreadByte 和 strings.Reader.UnreadRune
撤消最后的`ReadByte`或`ReadRune`操作。如果最后一个操作不是读取单字节或单个rune，则操作无效。

```go
r := strings.NewReader("Golang")
b, _ := r.ReadByte()
fmt.Printf("%c\n", b)  // G
r.UnreadByte()
b, _ = r.ReadByte()
fmt.Printf("%c\n", b)  // G again
```

### strings.Title
将字符串中的每个单词的首字母转换为大写。

```go
fmt.Println(strings.Title("hello world"))  // Hello World
```

### strings.TrimLeft 和 strings.TrimRight
从字符串的开头或结尾删除字符集中的字符。

```go
fmt.Println(strings.TrimLeft("!!!hello!!!", "!"))  // hello!!!
fmt.Println(strings.TrimRight("!!!hello!!!", "!")) // !!!hello
```

### strings.TrimPrefix 和 strings.TrimSuffix
删除字符串的前缀或后缀（如果存在）。

```go
fmt.Println(strings.TrimPrefix("GoLang", "Go"))   // Lang
fmt.Println(strings.TrimSuffix("GoLang", "Lang")) // Go
```

### strings.Replacer.WriteString
*Replacer类型的方法。对于提供的writer，此方法将`s`中的每个匹配项替换为其相应的替换项，并将结果写入writer。返回的是写入的字节数以及可能的错误。

```go
replacer := strings.NewReplacer("foo", "bar")
var buf bytes.Buffer
replacer.WriteString(&buf, "foo world")
fmt.Println(buf.String())  // bar world
```

### strings.Reader.ReadAt
从原始字符串的`off`偏移量开始，读取`len(b)`个字节到`b`。返回读取的字节数和可能的错误。

```go
r := strings.NewReader("Golang")
buf := make([]byte, 4)
r.ReadAt(buf, 2)
fmt.Printf("%s\n", buf)  // lang
```

### strings.Reader.ReadByte
从Reader读取并返回下一个字节。

```go
r := strings.NewReader("Golang")
b, _ := r.ReadByte()
fmt.Printf("%c\n", b)  // G
```

### strings.Reader.ReadRune
从`Reader`读取并返回下一个Unicode代码点。

```go
r := strings.NewReader("语言")
ch, _, _ := r.ReadRune()
fmt.Printf("%c\n", ch)  // 语
```

### strings.Reader.Seek
设置下一个Read或ReadAt的偏移量。

```go
r := strings.NewReader("Golang")
r.Seek(2, io.SeekStart)
buf := make([]byte, 4)
r.Read(buf)
fmt.Printf("%s\n", buf)  // lang
```

### strings.Reader.ReadBytes
读取直到第一次遇到指定的字节，然后返回一个切片，该切片包含直到该字节（包括该字节）的数据。

```go
r := strings.NewReader("Golang, the best!")
data, _ := r.ReadBytes(',')
fmt.Printf("%s\n", data)  // "Golang,"
```

### strings.Reader.ReadString
与`ReadBytes`类似，但返回的是字符串。

```go
r := strings.NewReader("Golang, the best!")
data, _ := r.ReadString(',')
fmt.Println(data)  // "Golang,"
```

### strings.IndexAny
返回字符串中第一个与字符集中的任意字符匹配的字符的索引，如果没有匹配则返回-1。

```go
fmt.Println(strings.IndexAny("Golang", "xyzG"))  // 0
```

### strings.LastIndexAny
返回字符串中与字符集中的任何字符匹配的最后一个字符的索引。

```go
fmt.Println(strings.LastIndexAny("Golang", "xyzG"))  // 5
```

### strings.HasPrefix 和 strings.HasSuffix
检查字符串是否以指定的前缀或后缀开始或结束。

```go
fmt.Println(strings.HasPrefix("Golang", "Go"))    // true
fmt.Println(strings.HasSuffix("Golang", "Lang"))  // false
```

### strings.Map
返回字符串`s`的新版本，其中每个字符都通过函数`mapping`进行了替换。如果映射返回-1，则不包含该字符的对应项。

```go
f := func(r rune) rune {
    switch {
    case r == 'o':
        return 'a'
    default:
        return r
    }
}
fmt.Println(strings.Map(f, "Golang"))  // "Galang"
```

### strings.Compare
比较两个字符串并返回一个整数。如果两个字符串相等则返回0，如果`a`小于`b`则返回-1，否则返回1。

```go
fmt.Println(strings.Compare("a", "b"))     // -1
fmt.Println(strings.Compare("a", "a"))     // 0
fmt.Println(strings.Compare("b", "a"))     // 1
```

### strings.Repeat
将字符串重复`count`次并返回。

```go
fmt.Println(strings.Repeat("Go", 3))  // GoGoGo
```

### strings.ReplaceAll
在`s`中用`new`替换所有`old`的出现。

```go
fmt.Println(strings.ReplaceAll("oink oink oink", "k", "ky"))  // "oinky oinky oinky"
```

