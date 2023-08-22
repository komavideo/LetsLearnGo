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

### 13. 参数顺序
在 `flag` 包中，命令行参数的顺序是很重要的。标志必须在位置参数之前。例如，考虑以下程序：

```go
package main

import (
	"flag"
	"fmt"
)

func main() {
	verbose := flag.Bool("verbose", false, "display verbose output")
	flag.Parse()

	fmt.Println("Verbose:", *verbose)
	fmt.Println("Remaining args:", flag.Args())
}
```
以下是有效的命令行调用：
```bash
$ go run main.go -verbose file1 file2
Verbose: true
Remaining args: [file1 file2]
```
但是，将标志放在位置参数之后是无效的：
```bash
$ go run main.go file1 file2 -verbose
```

### 14. 使用-作为标志的值
如果你需要传递一个值，该值以 `-` 开头（例如，一个负数），你可以使用 `--` 来标识标志的结束。这告诉 `flag` 包后面的内容都应该被视为参数，而不是标志。

```go
package main

import (
	"flag"
	"fmt"
)

func main() {
	number := flag.Int("number", 0, "An integer")
	flag.Parse()

	fmt.Println("Number:", *number)
	fmt.Println("Remaining args:", flag.Args())
}
```
调用方式：
```bash
$ go run main.go -- -12345
Number: 0
Remaining args: [-12345]
```

### 15. flag.Shorthand
虽然 `flag` 包本身不支持短标志和长标志的混合使用，但你可以使用两次 `flag` 函数调用来模拟这种行为。

```go
package main

import (
	"flag"
	"fmt"
)

func main() {
	var verbose bool
	flag.BoolVar(&verbose, "v", false, "display verbose output (shorthand)")
	flag.BoolVar(&verbose, "verbose", false, "display verbose output")

	flag.Parse()

	fmt.Println("Verbose:", verbose)
}
```
这样，`-v` 和 `--verbose` 都可以用来设置 `verbose` 标志。

### 16. 解析未知的标志
默认情况下，当你传递一个未知的标志时，`flag` 会输出一个错误并停止程序。但有时你可能想要处理这些未知标志。为此，你可以使用自定义的 `flag.ErrorHandling`。

```go
package main

import (
	"flag"
	"fmt"
)

func main() {
	fs := flag.NewFlagSet("custom", flag.ContinueOnError)
	verbose := fs.Bool("verbose", false, "display verbose output")

	err := fs.Parse(os.Args[1:])
	if err != nil {
		fmt.Println("Error:", err)
	}

	fmt.Println("Verbose:", *verbose)
}
```
这样，当你传递一个未知的标志时，程序不会立即退出，而是继续运行。

### 17. 标志的默认值
当你定义一个标志时，可以为其设置一个默认值。如果在命令行中没有为该标志提供值，就会使用这个默认值。你可以使用各种 `flag` 函数（如 `String`, `Bool`, `Int` 等）的第二个参数来设置默认值。

```go
package main

import (
	"flag"
	"fmt"
)

func main() {
	// 设置默认值为 "green"
	color := flag.String("color", "green", "set the color")

	flag.Parse()

	fmt.Println("Color:", *color)
}
```

### 18. flag.NFlag()
`flag.NFlag()` 函数返回已设置的标志数量。这个函数可以帮助你了解用户在命令行中提供了多少个标志。

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

	fmt.Println("Number of flags set:", flag.NFlag())
}
```

### 19. 将标志绑定到变量
你可以使用 `flag.Var()` 函数将标志直接绑定到变量，而不是先定义一个指针，然后再解引用它。这在某些情况下可以使代码更加简洁。

```go
package main

import (
	"flag"
	"fmt"
)

func main() {
	var verbose bool
	flag.BoolVar(&verbose, "verbose", false, "display verbose output")

	flag.Parse()

	fmt.Println("Verbose:", verbose)
}
```

### 20. 使用标志验证
有时，你可能需要验证标志的值是否有效。可以在调用 `flag.Parse()` 后执行这些验证。

```go
package main

import (
	"flag"
	"fmt"
)

func main() {
	color := flag.String("color", "blue", "set the color")

	flag.Parse()

	if *color != "blue" && *color != "red" && *color != "green" {
		fmt.Println("Invalid color value")
		return
	}

	fmt.Println("Color:", *color)
}
```

### 21. 定义你自己的帮助消息
虽然 `flag` 包提供了默认的帮助消息，但有时你可能想要提供自定义的帮助消息。你可以通过检查特定的标志（如 `-h` 或 `--help`）来实现这一点。

```go
package main

import (
	"flag"
	"fmt"
)

func main() {
	help := flag.Bool("help", false, "display help message")
	verbose := flag.Bool("verbose", false, "display verbose output")

	flag.Parse()

	if *help {
		displayHelp()
		return
	}

	fmt.Println("Verbose:", *verbose)
}

func displayHelp() {
	fmt.Println("This is a custom help message for the application.")
	fmt.Println("Available flags:")
	flag.PrintDefaults()
}
```

### 22. 与 os.Args 交互
`os.Args` 是一个包含原始命令行参数的字符串切片。`os.Args[0]` 是程序的名称，其余是传递给程序的参数。与 `flag.Args()` 不同， `os.Args` 包含标志和它们的值。

```go
package main

import (
	"flag"
	"fmt"
	"os"
)

func main() {
	flag.Parse()

	fmt.Println("os.Args:", os.Args)
	fmt.Println("flag.Args():", flag.Args())
}
```

### 23. 使用 flag.Lookup
`flag.Lookup` 函数可以用于检索已定义的标志。这允许你在其他地方访问或修改标志的属性。

```go
package main

import (
	"flag"
	"fmt"
)

func main() {
	color := flag.String("color", "blue", "set the color")
	flag.Parse()

	f := flag.Lookup("color")
	if f != nil {
		fmt.Println("Flag 'color' description:", f.Usage)
		fmt.Println("Flag 'color' value:", *color)
	}
}
```

### 24. 定义标志的持续时间
`flag` 包为时间持续时间定义了一个特殊的标志类型。这可以用于解析像 `"5s"` 或 `"3m"` 这样的时间表示。

```go
package main

import (
	"flag"
	"fmt"
	"time"
)

func main() {
	duration := flag.Duration("interval", 1*time.Second, "time interval")

	flag.Parse()

	fmt.Printf("Interval: %v\n", *duration)
}
```
调用：
```bash
$ go run main.go -interval=2m30s
Interval: 2m30s
```

### 25. 重置所有标志
有时，你可能需要在程序运行期间多次解析标志。在这种情况下，你可以使用 `flag.CommandLine` 来重置所有的标志。

```go
package main

import (
	"flag"
	"fmt"
)

func main() {
	verbose := flag.Bool("verbose", false, "display verbose output")
	flag.Parse()

	fmt.Println("First run, Verbose:", *verbose)

	// 重置所有标志
	flag.CommandLine = flag.NewFlagSet(os.Args[0], flag.ExitOnError)
	verbose2 := flag.Bool("verbose", false, "display verbose output")

	flag.Parse()
	fmt.Println("Second run, Verbose:", *verbose2)
}
```

### 26. 使用flag.Set
如果你想在代码中动态地为某个标志设置值，而不是从命令行获取，可以使用 `flag.Set` 函数。

```go
package main

import (
	"flag"
	"fmt"
)

func main() {
	color := flag.String("color", "blue", "set the color")

	// 在解析之前为 color 设置一个新值
	flag.Set("color", "red")
	flag.Parse()

	fmt.Println("Color:", *color)
}
```

### 27. flag.VisitAll vs flag.Visit
我们之前提到了 `flag.VisitAll` 和 `flag.Visit`。这两个函数都接受一个函数作为参数，该函数会应用于每个标志。但它们的差异在于：

+ `flag.VisitAll` 遍历所有定义的标志，无论它们是否在命令行中设置了值。
+ `flag.Visit` 仅遍历在命令行中设置了值的标志。

### 28. 解析自定义的参数列表
虽然默认情况下 `flag` 包会解析 `os.Args`，但你可以为其提供一个自定义的参数列表。

```go
package main

import (
	"flag"
	"fmt"
)

func main() {
	color := flag.String("color", "blue", "set the color")

	args := []string{"-color=green"}
	flag.CommandLine.Parse(args)

	fmt.Println("Color:", *color)
}
```

### 29. 检测标志是否被设置
有时，你可能想知道一个标志是否在命令行中被明确设置，而不只是使用了默认值。这可以通过 `flag.Visit` 或 `flag.Lookup` 实现。

```go
package main

import (
	"flag"
	"fmt"
)

func main() {
	color := flag.String("color", "blue", "set the color")
	flag.Parse()

	if flag := flag.Lookup("color"); flag != nil && flag.Value.String() != flag.DefValue {
		fmt.Println("Color was explicitly set to:", *color)
	} else {
		fmt.Println("Using default color:", *color)
	}
}
```

### 30. 使用-作为标志名
在某些命令行工具中，`-` 用作标准输入的占位符。虽然 `flag` 包不直接支持这一点，但你可以检查 `flag.Args()` 中是否存在 -，并据此采取行动。

```go
package main

import (
	"flag"
	"fmt"
)

func main() {
	flag.Parse()
	args := flag.Args()

	for _, arg := range args {
		if arg == "-" {
			fmt.Println("Read from standard input!")
		} else {
			fmt.Println("Filename:", arg)
		}
	}
}
```

