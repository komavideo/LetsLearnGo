使用flag库
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

## Go 语言 flag 库

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

### 9. 使用 flag.Usage
你可以通过设置 `flag.Usage` 自定义标志的使用说明。例如，当用户输入错误的标志或请求帮助时，可以显示这些说明。

```go
package main

import (
	"flag"
	"fmt"
	"os"
)

func main() {
	verbose := flag.Bool("verbose", false, "display verbose output")

	// 自定义使用说明
	flag.Usage = func() {
		fmt.Fprintf(os.Stderr, "Usage of %s:\n", os.Args[0])
		flag.PrintDefaults()
		fmt.Fprintf(os.Stderr, "\nExample: %s -verbose\n", os.Args[0])
	}

	flag.Parse()

	fmt.Println("Verbose:", *verbose)
}
```
运行程序，请求帮助：
```bash
$ go run main.go -h
Usage of main:
  -verbose
    	display verbose output
Example: main -verbose
```

### 10. 解析环境变量
虽然 `flag` 包专门用于解析命令行参数，但你可以结合环境变量来增强功能，以下是一个简单的例子：

```go
package main

import (
	"flag"
	"fmt"
	"os"
)

func main() {
	// 从环境变量中读取默认值
	defaultColor := os.Getenv("DEFAULT_COLOR")
	if defaultColor == "" {
		defaultColor = "blue"
	}
	color := flag.String("color", defaultColor, "set the color")

	flag.Parse()

	fmt.Println("Color:", *color)
}
```

### 11. 结合其他库使用
有一些第三方库可以与 `flag` 库结合使用，提供更多功能，例如支持子命令、更复杂的验证等。常见的库有 `cobra` 和 `kingpin`。如果你在寻找更高级的命令行工具或需要支持子命令，这些库可能会很有帮助。

### 12. 注意事项
当使用 `flag` 包时，有几点需要注意：

`flag` 库解析的标志默认都是必需的。如果你需要一个可选标志，你需要为它提供一个默认值。


当你定义了一个标志但在命令行中没有为其提供值时，该标志将使用默认值。

`flag` 包不支持短标志（如 `-v`）和长标志（如 `--verbose`）的混合使用。你需要选择其中之一。

