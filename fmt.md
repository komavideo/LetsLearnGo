使用fmt库
=========

`fmt` 是 `Go` 语言中用于格式化输入输出的标准库，非常实用。

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

## Go 语言 fmt 库

### Println: 打印一行文本并在末尾添加换行符。

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, world!")
}
```

### Printf: 使用格式化字符串打印文本。例如，`%v` 是一个通用的值占位符，它将使用变量的默认格式。还有其他如 `%s`（字符串），`%d`（十进制整数）等。

```go
package main

import "fmt"

func main() {
    name := "Alice"
    age := 30
    fmt.Printf("Hello, my name is %s and I am %d years old.\n", name, age)
}
```

### Sprintf: 与 Printf 类似，但不会直接打印结果。相反，它返回一个字符串。

```go
package main

import "fmt"

func main() {
    x := 3.1415
    message := fmt.Sprintf("The value of pi is approximately %v.", x)
    fmt.Println(message)
}
```

### Scan & Scanf: 从标准输入读取值。

```go
package main

import "fmt"

func main() {
    var name string
    fmt.Print("Enter your name: ")
    fmt.Scan(&name)
    fmt.Printf("Hello, %s!\n", name)
}
```

### Errorf: 创建一个新的 error 类型。

```go
package main

import "fmt"

func main() {
    err := fmt.Errorf("This is an error message: %d", 404)
    fmt.Println(err)
}
```

### %t: 输出布尔值。

```go
package main

import "fmt"

func main() {
    truth := true
    fmt.Printf("The statement is %t.\n", truth)
}
```

### %x 和 %X: 输出十六进制表示。

```go
package main

import "fmt"

func main() {
    value := 255
    fmt.Printf("Hexadecimal (lowercase): %x\n", value)
    fmt.Printf("Hexadecimal (uppercase): %X\n", value)
}
```

### %q: 输出带有双引号的字符串。

```go
package main

import "fmt"

func main() {
    str := "hello"
    fmt.Printf("Quoted string: %q\n", str)
}
```

### %T: 输出一个值的类型。

```go
package main

import "fmt"

func main() {
    var x interface{} = 5.5
    fmt.Printf("Type of x: %T\n", x)
}
```

### 宽度和精度: 使用 `fmt.Printf`，你可以定义输出的宽度和精度。例如，可以用于格式化表格输出。

```go
package main

import "fmt"

func main() {
    name := "Alice"
    age := 25
    score := 92.5
    fmt.Printf("%-10s | %4d | %5.2f\n", name, age, score)
}
```

在这个例子中：

+ `%-10s` 输出一个最大宽度为10的字符串，- 使其左对齐。
+ `%4d` 输出一个宽度至少为4的整数，右对齐。
+ `%5.2f` 输出一个宽度至少为5，且小数点后有2位的浮点数。

### Scanln & Sscanf: Scanln 从标准输入读取一行，而 Sscanf 从字符串读取格式化的输入。

```go
package main

import "fmt"

func main() {
    var day, month, year int
    date := "15/08/2023"
    fmt.Sscanf(date, "%d/%d/%d", &day, &month, &year)
    fmt.Printf("Day: %d, Month: %d, Year: %d\n", day, month, year)
}
```

### %c: 输出字符表示的整数值。

```go
package main

import "fmt"

func main() {
    char := 65
    fmt.Printf("The character representation is: %c\n", char)
}
```

### %U: 输出 Unicode 表示形式。

```go
package main

import "fmt"

func main() {
    char := '中'
    fmt.Printf("The Unicode format is: %U\n", char)
}
```

### %b: 输出二进制表示。

```go
package main

import "fmt"

func main() {
    number := 5
    fmt.Printf("The binary format is: %b\n", number)
}
```

### %e 和 %E: 输出浮点数的科学记数法表示。

```go
package main

import "fmt"

func main() {
    number := 123400000.0
    fmt.Printf("Scientific notation (lowercase): %e\n", number)
    fmt.Printf("Scientific notation (uppercase): %E\n", number)
}
```

### Fscan & Fscanf & Fscanln: 这些函数从 `io.Reader` (如文件) 读取文本。

```go
package main

import (
    "fmt"
    "strings"
)

func main() {
    reader := strings.NewReader("123 456")
    var a, b int
    fmt.Fscan(reader, &a, &b)
    fmt.Printf("a: %d, b: %d\n", a, b)
}
```

### %p: 输出指针的地址。

```go
package main

import "fmt"

func main() {
    number := 5
    fmt.Printf("The address of number is: %p\n", &number)
}
```

### 复数格式化: `%f`, `%g`, 和 `%e` 也可以用于复数类型。

```go
package main

import "fmt"

func main() {
    complexNum := 2 + 3i
    fmt.Printf("Complex number: %v\n", complexNum)
}
```

### 结构体和其他数据格式化: 使用 `%+v` 可以输出结构体的字段名和值。

```go
package main

import "fmt"

type Person struct {
    Name string
    Age  int
}

func main() {
    person := Person{Name: "Alice", Age: 30}
    fmt.Printf("Person struct: %+v\n", person)
}
```

### 格式化标志: 使用 `#` 标志为 `%v` 生成 `Go` 语法表示。

```go
package main

import "fmt"

func main() {
    numbers := []int{1, 2, 3}
    fmt.Printf("%#v\n", numbers)
}
```

### %o: 输出八进制表示。

```go
package main

import "fmt"

func main() {
    number := 64
    fmt.Printf("The octal format is: %o\n", number)
}
```

### Scanf 多字段输入: 可以从标准输入读取多个字段。

```go
package main

import "fmt"

func main() {
    var name string
    var age int
    fmt.Printf("Enter your name and age (e.g., Alice 30): ")
    fmt.Scanf("%s %d", &name, &age)
    fmt.Printf("Name: %s, Age: %d\n", name, age)
}
```

### Stringer 接口: 如果你的类型实现了 `String() string` 方法，`fmt.Print` 和相关函数会使用它来获取该类型的字符串表示。

```go
package main

import "fmt"

type Fruit struct {
    Name  string
    Color string
}

func (f Fruit) String() string {
    return fmt.Sprintf("Fruit(Name: %s, Color: %s)", f.Name, f.Color)
}

func main() {
    apple := Fruit{Name: "Apple", Color: "Red"}
    fmt.Println(apple)
}
```

### Formatter 接口: 如果你希望自定义类型在 `fmt.Printf` 和相关函数中的格式化行为，你可以为该类型实现 `fmt.Formatter` 接口。

```go
package main

import (
    "fmt"
)

type Coordinate struct {
    X, Y int
}

func (c Coordinate) Format(f fmt.State, verb rune) {
    switch verb {
    case 'v':
        if f.Flag('+') {
            fmt.Fprintf(f, "Coordinate{X:%d, Y:%d}", c.X, c.Y)
        } else {
            fmt.Fprintf(f, "Coordinate{%d, %d}", c.X, c.Y)
        }
    case 'q':
        fmt.Fprintf(f, "\"(%d, %d)\"", c.X, c.Y)
    default:
        fmt.Fprintf(f, "(%d, %d)", c.X, c.Y)
    }
}

func main() {
    point := Coordinate{5, 7}
    fmt.Printf("%v\n", point)
    fmt.Printf("%+v\n", point)
    fmt.Printf("%q\n", point)
}
```

### 宽度和精度的动态指定: 可以使用 `*` 在 `fmt.Printf` 中动态指定宽度和精度。

```go
package main

import "fmt"

func main() {
    pi := 3.14159265
    width := 10
    precision := 3
    fmt.Printf("Pi with width %d and precision %d: %*.*f\n", width, precision, width, precision, pi)
}
```

### Verb `%%`: 打印出 `%` 符号。

```go
package main

import "fmt"

func main() {
    fmt.Printf("There's a 50%% chance of rain.\n")
}
```

### Go 语法表示的其他例子:

```go
package main

import "fmt"

func main() {
    m := map[string]int{"one": 1, "two": 2}
    fmt.Printf("%#v\n", m)
}
```

### %g 和 %G: 为大指数格式化输出，使用 `%e` 或 `%E`；对于小的指数，使用 `%f`。

```go
package main

import "fmt"

func main() {
    num := 0.000000012345
    fmt.Printf("Using %%g: %g\n", num)
    fmt.Printf("Using %%G: %G\n", num)
}
```

### 零标志: 通过 `0` 标志来填充0，使得输出的宽度由后续数字指定。

```go
package main

import "fmt"

func main() {
    number := 5
    fmt.Printf("Padding with zeros: %04d\n", number)
}
```

### 符号标志: 使用 `+` 标志总是输出数字的符号。

```go
package main

import "fmt"

func main() {
    num := 42
    fmt.Printf("Using + with positive number: %+d\n", num)
    num = -42
    fmt.Printf("Using + with negative number: %+d\n", num)
}
```

### 空格标志: 使用空格标志在正数前面加上空格，而负数前面使用 `-`。

```go
package main

import "fmt"

func main() {
    num := 42
    fmt.Printf("Using space with positive number: % d\n", num)
    num = -42
    fmt.Printf("Using space with negative number: % d\n", num)
}
```

### #标志: 它会使输出的格式变为另一种形式，具体取决于所使用的格式化动词。

```go
package main

import "fmt"

func main() {
    fmt.Printf("Without #: %x\n", 255)   // ff
    fmt.Printf("With #: %#x\n", 255)    // 0xff
    fmt.Printf("Without #: %o\n", 8)    // 10
    fmt.Printf("With #: %#o\n", 8)      // 010
    fmt.Printf("Without #: %b\n", 5)    // 101
    fmt.Printf("With #: %#b\n", 5)      // 0b101
}
```

### 读取字符串直到空格或换行: 使用 `%s` 来从输入读取字符串，它会在遇到空格或换行符时停止。

```go
package main

import "fmt"

func main() {
    fmt.Println("Type a word:")
    var word string
    fmt.Scanf("%s", &word)
    fmt.Printf("You typed: %s\n", word)
}
```

### 读取整行字符串: 使用 `%s` 与 `Scan` 或 `Scanln` 来从输入读取整行内容。

```go
package main

import "fmt"

func main() {
    fmt.Println("Type a sentence:")
    var sentence string
    fmt.Scanln(&sentence)
    fmt.Printf("You typed: %s\n", sentence)
}
```

### FormatInt 和 FormatUint: 直接格式化整数值为字符串，提供更多的控制。

```go
package main

import (
    "fmt"
    "strconv"
)

func main() {
    val := int64(255)
    str := strconv.FormatInt(val, 16)
    fmt.Printf("Hexadecimal representation: %s\n", str)
}
```

### %x 和 %X: 分别以小写和大写的方式输出十六进制数。

```go
package main

import "fmt"

func main() {
    num := 255
    fmt.Printf("Hexadecimal (lowercase): %x\n", num)
    fmt.Printf("Hexadecimal (uppercase): %X\n", num)
}
```

### %q: 输出带双引号的字符串，适用于字符串、字符、[]byte，也可以用来输出数字。

```go
package main

import "fmt"

func main() {
    str := "hello"
    fmt.Printf("Quoted string: %q\n", str)
}
```

### Scan 和 Scanln: 从标准输入读取。

```go
package main

import "fmt"

func main() {
    var i int
    var s string
    fmt.Println("Enter an integer and a string:")
    fmt.Scan(&i, &s)
    fmt.Printf("You entered: %d and %s\n", i, s)
}
```

### Scanf 的特定格式输入: 使用 Scanf 定义输入的格式。

```go
package main

import "fmt"

func main() {
    var day, month, year int
    fmt.Println("Enter a date (dd-mm-yyyy):")
    fmt.Scanf("%02d-%02d-%04d", &day, &month, &year)
    fmt.Printf("You entered the date: %02d/%02d/%04d\n", day, month, year)
}
```

### Fprintln: 向指定的 `io.Writer` 输出，并在末尾添加换行。

```go
package main

import (
    "fmt"
    "os"
)

func main() {
    fmt.Fprintln(os.Stdout, "This is a line of text.")
}
```

### Errorf: 返回一个格式化的错误。

```go
package main

import (
    "fmt"
)

func main() {
    err := fmt.Errorf("This is an error with code: %d", 404)
    fmt.Println(err)
}
```

### %T: 输出值的类型。

```go
package main

import "fmt"

func main() {
    var myVar interface{} = 42
    fmt.Printf("The type of myVar is: %T\n", myVar)
}
```

### Sprint、Sprintf 和 Sprintln: 生成一个格式化字符串，但不输出它。

```go
package main

import "fmt"

func main() {
    message := fmt.Sprintf("The answer to life, the universe, and everything is %d", 42)
    fmt.Println(message)
}
```

### 动态指定格式: 有时，您可能需要根据运行时条件动态地指定格式。

```go
package main

import "fmt"

func main() {
    var format string
    if someCondition := true; someCondition {
        format = "The result is %d!"
    } else {
        format = "Result: %d."
    }
    fmt.Printf(format, 42)
}
```

### 使用索引进行参数排序: 对于复杂的格式化，您可能希望多次使用相同的参数，或更改参数的顺序。

```go
package main

import "fmt"

func main() {
    fmt.Printf("First: %d, Second: %d, First Again: %[1]d\n", 1, 2)
}
```

Done.
