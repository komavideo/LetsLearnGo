使用strconv库
=============

`strconv` 是 `Go` 语言的一个标准库，它为基本数据类型与其字符串表示之间的转换提供了方便的工具。

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

## Go 语言 strconv 库

### 1. Atoi 和 Itoa：整数与字符串之间的转换

#### 1.1. strconv.Atoi: 字符串转整数

```go
s := "123"
i, err := strconv.Atoi(s)
if err != nil {
    log.Fatal(err)
}
fmt.Println(i)
```

#### 1.2. strconv.Itoa: 整数转字符串

```go
i := 123
s := strconv.Itoa(i)
fmt.Println(s)
```

### 2. ParseBool: 字符串转布尔值

```go
s := "true"
b, err := strconv.ParseBool(s)
if err != nil {
    log.Fatal(err)
}
fmt.Println(b)
```

### 3. ParseFloat: 字符串转浮点数

```go
s := "3.14159"
f, err := strconv.ParseFloat(s, 64)
if err != nil {
    log.Fatal(err)
}
fmt.Println(f)
```

### 4. FormatBool, FormatInt, 和 FormatFloat: 基本数据类型转字符串

#### 4.1. FormatBool: 布尔值转字符串

```go
b := true
s := strconv.FormatBool(b)
fmt.Println(s)
```

#### 4.2. FormatInt: 整数转字符串

```go
i := int64(123)
s := strconv.FormatInt(i, 10)
fmt.Println(s)
```

#### 4.3. FormatFloat: 浮点数转字符串

```go
f := 3.14159
s := strconv.FormatFloat(f, 'f', 6, 64)
fmt.Println(s)
```

### 5. ParseInt 和 ParseUint: 字符串转整数和无符号整数

#### 5.1. strconv.ParseInt: 字符串转整数

这个函数可以指定基数。例如，如果你想将一个二进制的字符串转换为整数，可以这样做：

```go
s := "1101"
i, err := strconv.ParseInt(s, 2, 64) // 基数 2 代表二进制
if err != nil {
    log.Fatal(err)
}
fmt.Println(i)  // 输出：13
```

#### 5.2. strconv.ParseUint: 字符串转无符号整数

同样，你也可以指定基数：

```go
s := "1101"
u, err := strconv.ParseUint(s, 2, 64)
if err != nil {
    log.Fatal(err)
}
fmt.Println(u)  // 输出：13
```

### 6. Quote 和 Unquote: 字符串的引用和去引用

#### 6.1. strconv.Quote: 为字符串添加引号

这可以使字符串适用于 Go 代码中的字符串字面值。

```go
s := "Hello, world!"
q := strconv.Quote(s)
fmt.Println(q)  // 输出："Hello, world!"
```

#### 6.2. strconv.Unquote: 删除字符串的引号

```go
q := "\"Hello, world!\""
s, err := strconv.Unquote(q)
if err != nil {
    log.Fatal(err)
}
fmt.Println(s)  // 输出：Hello, world!
```

### 7. IsPrint：检查字符是否为可打印字符

```go
ch := '你'
if strconv.IsPrint(ch) {
    fmt.Println("是可打印字符")
} else {
    fmt.Println("不是可打印字符")
}
```

### 8. Append 系列函数

这些函数用于将基础数据类型的值追加到字节切片中，而不是直接生成新的字符串。这在某些情况下可以更高效。

#### 8.1. AppendBool: 追加布尔值到字节切片

```go
dst := []byte("Value: ")
result := strconv.AppendBool(dst, true)
fmt.Println(string(result))  // 输出：Value: true
```

#### 8.2. AppendInt: 追加整数到字节切片

```go
dst := []byte("Value: ")
result := strconv.AppendInt(dst, 12345, 10)
fmt.Println(string(result))  // 输出：Value: 12345
```

#### 8.3. AppendFloat: 追加浮点数到字节切片

```go
dst := []byte("Value: ")
result := strconv.AppendFloat(dst, 3.14159, 'f', 2, 64)
fmt.Println(string(result))  // 输出：Value: 3.14
```

### 9. CanBackquote: 检查字符串是否可以不改变地表示为一个原始字符串字面值

```go
s := "Hello\tworld!"
if strconv.CanBackquote(s) {
    fmt.Println("可以表示为原始字符串")
} else {
    fmt.Println("不可以表示为原始字符串")
}
```

### 10. ParseComplex 和 FormatComplex: 复数与字符串之间的转换

#### 10.1. ParseComplex: 字符串转复数

```go
s := "1+2i"
c, err := strconv.ParseComplex(s, 128)
if err != nil {
    log.Fatal(err)
}
fmt.Println(c)  // 输出：(1+2i)
```

#### 10.2. FormatComplex: 复数转字符串

```go
c := complex(1, 2)
s := strconv.FormatComplex(c, 'f', 2, 128)
fmt.Println(s)  // 输出：1.00+2.00i
```

### 11. QuoteToASCII, QuoteToGraphic, QuoteRune, QuoteRuneToASCII, 和 QuoteRuneToGraphic

这些函数提供了更具体的字符串和符文的引用功能。

#### 11.1. QuoteToASCII: 为字符串添加引号，并将非 ASCII 和不可打印字符转换为\u或\U转义

```go
s := "Hello, 世界!"
q := strconv.QuoteToASCII(s)
fmt.Println(q)  // 输出类似于："Hello, \u4e16\u754c!"
```

#### 11.2. QuoteToGraphic: 为字符串添加引号，只转义不可打印字符

```go
s := "Hello, \x00世界!"
q := strconv.QuoteToGraphic(s)
fmt.Println(q)  // 输出："Hello, \x00世界!"
```

#### 11.3. QuoteRune: 为符文添加引号

```go
r := '世'
q := strconv.QuoteRune(r)
fmt.Println(q)  // 输出：'世'
```

#### 11.4. QuoteRuneToASCII: 为符文添加引号，并将非 ASCII 符文转换为`\u`或`\U`转义

```go
r := '世'
q := strconv.QuoteRuneToASCII(r)
fmt.Println(q)  // 输出：'\u4e16'
```

#### 11.5. QuoteRuneToGraphic: 为符文添加引号，只转义不可打印的符文

```go
r := '\x00'
q := strconv.QuoteRuneToGraphic(r)
fmt.Println(q)  // 输出：'\x00'
```

### 12. IsGraphic 和 IsControl

这些函数可以用来检查字符是否为图形字符或控制字符。

#### 12.1. IsGraphic: 检查字符是否为图形字符

```go
ch := '你'
if strconv.IsGraphic(ch) {
    fmt.Println("是图形字符")
} else {
    fmt.Println("不是图形字符")
}
```

#### 12.2. IsControl: 检查字符是否为控制字符

```go
ch := '\n'
if strconv.IsControl(ch) {
    fmt.Println("是控制字符")
} else {
    fmt.Println("不是控制字符")
}
```

### 13. AppendQuote, AppendQuoteToASCII, 和 AppendQuoteRune

这些函数是为了将带引号的字符串或符文追加到字节切片中。

#### 13.1. AppendQuote: 追加带引号的字符串到字节切片

```go
dst := []byte("Greeting: ")
s := "Hello, world!"
result := strconv.AppendQuote(dst, s)
fmt.Println(string(result))  // 输出：Greeting: "Hello, world!"
```

#### 13.2. AppendQuoteToASCII: 追加带引号的字符串，并转换非ASCII字符

```go
dst := []byte("Message: ")
s := "Hello, 世界!"
result := strconv.AppendQuoteToASCII(dst, s)
fmt.Println(string(result))  // 输出可能为：Message: "Hello, \u4e16\u754c!"
```

#### 13.3. AppendQuoteRune: 追加带引号的符文到字节切片

```go
dst := []byte("Character: ")
r := '世'
result := strconv.AppendQuoteRune(dst, r)
fmt.Println(string(result))  // 输出：Character: '世'
```

### 14. AppendQuoteRuneToASCII: 追加带引号的符文，并转换非ASCII字符

```go
dst := []byte("Character: ")
r := '世'
result := strconv.AppendQuoteRuneToASCII(dst, r)
fmt.Println(string(result))  // 输出：Character: '\u4e16'
```

### 15. 使用 NumError

`strconv` 中的多数解析函数返回一个错误，当解析失败时。这个错误通常是 `*NumError` 类型。你可以使用这个类型来检查更多关于错误的细节。

```go
s := "abc"
_, err := strconv.Atoi(s)
if err != nil {
    if ne, ok := err.(*strconv.NumError); ok {
        fmt.Printf("Failed parsing at index: %v\n", ne.Err)
    } else {
        fmt.Println("Failed parsing:", err)
    }
}
```

这个示例会输出："Failed parsing at index: strconv.Atoi: parsing "abc": invalid syntax"。

### 16. 使用 Base 参数

许多 `strconv` 函数都接受一个名为 `base` 的参数，该参数指定要使用的数字基数。例如，你可以使用 `base` 为 16 来解析或格式化十六进制数字。

#### 16.1. 使用 base 解析十六进制字符串

```go
s := "1a"
i, err := strconv.ParseInt(s, 16, 64)
if err != nil {
    log.Fatal(err)
}
fmt.Println(i)  // 输出：26
```

#### 16.2. 使用 base 格式化为十六进制字符串

```go
i := int64(26)
s := strconv.FormatInt(i, 16)
fmt.Println(s)  // 输出：1a
```

### 17. AppendUint：追加无符号整数到字节切片

```go
dst := []byte("Unsigned value: ")
u := uint64(123456)
result := strconv.AppendUint(dst, u, 10)
fmt.Println(string(result))  // 输出：Unsigned value: 123456
```

### 18. ParseUint: 解析字符串为无符号整数

```go
s := "123456"
u, err := strconv.ParseUint(s, 10, 64)
if err != nil {
    log.Fatal(err)
}
fmt.Println(u)  // 输出：123456
```

### 19. 使用 bitSize 参数

许多 `strconv` 函数接受一个名为 `bitSize` 的参数。这个参数决定了目标转换类型的大小。例如，当解析浮点数时，`bitSize` 可以是 32 或 64，分别对应 `float32` 和 `float64`。

```go
s := "3.141592653589793"
f, err := strconv.ParseFloat(s, 32)
if err != nil {
    log.Fatal(err)
}
fmt.Println(f)  // 输出的值可能会有一点小小的舍入误差，因为我们使用了 float32
```

### 20. UnquoteChar: 解析由 QuoteRune 或 Quote 生成的转义字符序列

这个函数可以解析如 `\t`, `\n`, `\u4e16` 这样的转义序列。

```go
s := `\\t`
ch, multibyte, tail, err := strconv.UnquoteChar(s, '\\')
if err != nil {
    log.Fatal(err)
}
fmt.Println(ch, multibyte, tail)  // 输出：\ false t
```

### 21. AppendQuoteRuneToGraphic: 追加带引号的符文到字节切片，并只转义不可打印的字符

```go
dst := []byte("Rune value: ")
r := '\x00'
result := strconv.AppendQuoteRuneToGraphic(dst, r)
fmt.Println(string(result))  // 输出：Rune value: '\x00'
```

### 22. CanQuote: 检查字符串是否可以不改变地表示为一个双引号字符串字面值

```go
s := "Hello\tworld!"
if strconv.CanQuote(s) {
    fmt.Println("可以表示为双引号字符串")
} else {
    fmt.Println("不可以表示为双引号字符串")
}
```

### 23. QuoteRuneToGraphic: 引用符文，只转义不可打印的字符

```go
r := '\x00'
q := strconv.QuoteRuneToGraphic(r)
fmt.Println(q)  // 输出：'\x00'
```

### 24. AppendQuoteToGraphic: 追加带引号的字符串，并只转义不可打印字符

```go
dst := []byte("Message: ")
s := "Hello, \x00世界!"
result := strconv.AppendQuoteToGraphic(dst, s)
fmt.Println(string(result))  // 输出：Message: "Hello, \x00世界!"
```

### 25. UnquoteChar: 解析由 QuoteRuneToASCII、QuoteRuneToGraphic、QuoteToASCII 或 QuoteToGraphic 生成的转义字符序列

这个函数对于解析转义字符序列特别有用。

```go
s := `\u4e16`
ch, multibyte, tail, err := strconv.UnquoteChar(s, '\\')
if err != nil {
    log.Fatal(err)
}
fmt.Println(ch, multibyte, tail)  // 输出：世 true 
```

### 26. 使用转换函数的 base 和 bitSize 参数

很多函数接受 `base` 或 `bitSize` 参数，以提供更大的灵活性。例如，你可以使用不同的基数来解析或格式化数字，或使用不同的位大小来解析数字。

```go
s := "ff"
i, err := strconv.ParseInt(s, 16, 0) // 使用16进制基数
if err != nil {
    log.Fatal(err)
}
fmt.Println(i)  // 输出：255
```

### 27. IsPrint 和 IsGraphic 的区别

两者都用于检查字符是否是可打印的，但 `IsGraphic` 还包括了空格字符，而 `IsPrint` 不包括。

```go
ch := ' '
if strconv.IsPrint(ch) {
    fmt.Println("IsPrint: 是可打印字符")
} else {
    fmt.Println("IsPrint: 不是可打印字符")
}
if strconv.IsGraphic(ch) {
    fmt.Println("IsGraphic: 是图形字符")
} else {
    fmt.Println("IsGraphic: 不是图形字符")
}
```

### 28. AppendComplex: 将复数追加到字节切片

```go
dst := []byte("Complex value: ")
c := complex(1, 2)
result := strconv.AppendComplex(dst, c, 'f', 2, 64)
fmt.Println(string(result))  // 输出：Complex value: (1.00+2.00i)
```

### 29. 使用不同的格式化方式

当使用 `FormatFloat` 和 `FormatComplex` 等函数时，你可以选择不同的格式化方式，例如 `'f'、'b'、'e'、'g' 和 'x'`。

#### 29.1. 'f' - 浮点数

```go
f := 3.14159
s := strconv.FormatFloat(f, 'f', 2, 64)
fmt.Println(s)  // 输出：3.14
```

#### 29.2. 'e' - 指数表示

```go
f := 3.14159
s := strconv.FormatFloat(f, 'e', 2, 64)
fmt.Println(s)  // 输出：3.14e+00
```

#### 29.3. 'g' - 更简短的 'e' 或 'f'

```go
f := 3.14159
s := strconv.FormatFloat(f, 'g', 2, 64)
fmt.Println(s)  // 输出：3.1
```

### 30. AppendInt 使用不同的基数

`AppendInt` 允许你使用不同的基数来格式化整数。

```go
dst := []byte("Hex value: ")
i := int64(255)
result := strconv.AppendInt(dst, i, 16)
fmt.Println(string(result))  // 输出：Hex value: ff
```

### 31. NumError 的其他字段

我们之前讨论了 `NumError` 的 `Err` 字段。但 `NumError` 还有其他字段：

+ `Func`: 导致错误的函数名称，例如 "ParseInt"。
+ `Num`: 传递给函数的字符串。

```go
s := "abc"
_, err := strconv.Atoi(s)
if nerr, ok := err.(*strconv.NumError); ok {
    fmt.Printf("Function: %s, Number: %s, Error: %v\n", nerr.Func, nerr.Num, nerr.Err)
}
// 输出：Function: Atoi, Number: abc, Error: strconv.Atoi: parsing "abc": invalid syntax
```

### 32. IsGraphic vs IsPrint 之间的更多区别

`IsPrint` 返回字符是否为 Go 语言的可打印字符。这包括字母、数字、标点符号、符号和ASCII空格。

`IsGraphic` 返回字符是否为 Unicode 图形或空格字符。

```go
ch := '世'
fmt.Println("IsPrint:", strconv.IsPrint(ch))  // 输出：IsPrint: true
fmt.Println("IsGraphic:", strconv.IsGraphic(ch))  // 输出：IsGraphic: true
```

Done.
