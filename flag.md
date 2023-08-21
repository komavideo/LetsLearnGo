flag的使用
=========

Go 的 flag 包主要用于解析命令行参数。

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

## Go 语言 math 库

### 1. 基础使用
首先是如何定义一个简单的命令行标志。例如，我们可以定义一个布尔标志 `verbose` 和一个字符串标志 `color`。

```go
package main

import (
	"flag"
	"fmt"
)

func main() {
	// 定义一个布尔标志和一个字符串标志
	verbose := flag.Bool("verbose", false, "display verbose output")
	color := flag.String("color", "blue", "set the color")

	// 解析命令行参数
	flag.Parse()

	// 输出结果
	fmt.Println("Verbose:", *verbose)
	fmt.Println("Color:", *color)
}
```
运行上述程序，可以通过以下方式为参数提供值：
```bash
$ go run main.go -verbose -color=red
Verbose: true
Color: red
```

### 2. 使用非默认类型
除了上述的基本类型， `flag` 包还提供了其他类型如 `int`、`int64`、`uint`、`uint64`、`float64` 等。

```go
intFlag := flag.Int("number", 1, "an integer flag")
```

### 3. 使用 flag.Var
如果需要更复杂的数据类型或验证，可以使用 `flag.Var` 函数。

```go
var myValue customType

func main() {
    flag.Var(&myValue, "name", "description")
    flag.Parse()
    fmt.Println(myValue)
}

type customType struct {
    // ... your fields here
}

func (c *customType) Set(s string) error {
    // Parse and set your value here
    return nil
}

func (c *customType) String() string {
    return fmt.Sprint(*c)
}
```

### 4. 解析参数后的动作
`flag.Args()` 可以返回没有被解析为标志的命令行参数，而 `flag.NArg()` 可以返回这些参数的数量。

```go
func main() {
    flag.Parse()
    fmt.Println("Remaining args:", flag.Args())
    fmt.Println("Number of remaining args:", flag.NArg())
}
```

### 5. 使用自定义的 FlagSet
除了默认的命令行标志解析器，你还可以创建自定义的 `flag.FlagSet` 来解析不同的参数集。

```go
package main

import (
	"flag"
	"fmt"
)

func main() {
	fs := flag.NewFlagSet("custom", flag.ExitOnError)
	verbose := fs.Bool("verbose", false, "display verbose output")

	// 解析自定义的参数列表
	err := fs.Parse([]string{"-verbose"})
	if err != nil {
		fmt.Println("Error:", err)
	}

	fmt.Println("Verbose:", *verbose)
}
```

### 6. 定义自己的 flag.Value 接口
如前所述，你可以通过实现 `flag.Value` 接口来定义自己的数据类型。但这里我们更进一步：定义一个可以接受多个值的标志。

```go
package main

import (
	"flag"
	"fmt"
	"strings"
)

func main() {
	var languages stringList
	flag.Var(&languages, "lang", "List of languages")
	flag.Parse()

	fmt.Println("Languages:", languages)
}

type stringList []string

func (s *stringList) String() string {
	return fmt.Sprintf("%v", *s)
}

func (s *stringList) Set(value string) error {
	*s = append(*s, value)
	return nil
}
```
运行程序：
```bash
$ go run main.go -lang=go -lang=python -lang=javascript
Languages: [go python javascript]
```

### 7. 使用持续标志
有时，你可能想要一个标志可以被多次使用，每次都追加一个值。这可以通过自定义的数据类型实现，就像我们在上面的 `stringList` 示例中所做的那样。

### 8. 查看所有的标志
`flag.Visit` 和 `flag.VisitAll` 函数可以用来遍历所有已设置的标志或所有定义的标志。

```go
package main

import (
	"flag"
	"fmt"
)

func main() {
	verbose := flag.Bool("verbose", false, "display verbose output")
	color := flag.String("color", "blue", "set the color")

	flag.Parse()

	flag.Visit(func(f *flag.Flag) {
		fmt.Println("Set flag:", f.Name, "Value:", f.Value)
	})

	flag.VisitAll(func(f *flag.Flag) {
		fmt.Println("Defined flag:", f.Name, "Default value:", f.DefValue)
	})
}
```
运行程序：
```bash
$ go run main.go -verbose
Set flag: verbose Value: true
Defined flag: color Default value: blue
Defined flag: verbose Default value: false
```

