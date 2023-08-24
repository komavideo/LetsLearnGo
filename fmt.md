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

