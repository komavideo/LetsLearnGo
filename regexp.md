使用regexp库
============

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

## Go 语言 regexp 库

### 基本匹配:

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`foo`)
	fmt.Println(re.MatchString("seafood")) // 输出: true
}
```

### 查找匹配的字符串:

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`\w+`)
	fmt.Println(re.FindString("hello world")) // 输出: hello
}
```

### 查找所有匹配的字符串:

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`\w+`)
	fmt.Println(re.FindAllString("hello world, go is great", -1)) // 输出: [hello world go is great]
}
```

### 查找并返回匹配的下标:

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`\w+`)
	fmt.Println(re.FindStringIndex("hello world")) // 输出: [0 5]
}
```

### 带子匹配的查找:

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`(\w+):(\w+)`)
	matches := re.FindStringSubmatch("username:password")
	fmt.Println(matches) // 输出: [username:password username password]
}
```

### 替换:

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`\w+`)
	fmt.Println(re.ReplaceAllString("hello world", "REPLACED")) // 输出: REPLACED REPLACED
}
```

### 验证邮件格式:

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$`)
	fmt.Println(re.MatchString("example@email.com")) // 输出: true
}
```

### 查找所有匹配的下标:

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`\w+`)
	fmt.Println(re.FindAllStringIndex("hello world", -1)) // 输出: [[0 5] [6 11]]
}
```

### 使用 Compile 和 MustCompile:
`regexp.Compile` 返回一个正则表达式对象和一个错误，而 `regexp.MustCompile` 在出现错误时会产生 `panic`。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re, err := regexp.Compile(`\w+`)
	if err != nil {
		fmt.Println("Invalid regexp:", err)
		return
	}
	fmt.Println(re.MatchString("hello")) // 输出: true
}
```

### 匹配特定的数字次数:

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`\d{3}-\d{2}-\d{4}`)
	fmt.Println(re.MatchString("123-45-6789")) // 输出: true
}
```

### 非贪婪匹配:
正则默认是贪婪匹配，但有时候我们可能想要非贪婪匹配：

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`a.*?b`)
	fmt.Println(re.FindString("aaxbb ayb")) // 输出: aaxb
}
```

### 正则表达式中的转义:
假如你想匹配文本中的 . 字符，你需要对它进行转义，因为在正则表达式中，. 表示匹配任意字符（除了换行符）。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`\.`)
	fmt.Println(re.MatchString("example.com")) // 输出: true
}
```

### 分组并命名:
您可以使用 `?P<name>` 来为正则表达式的分组命名。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`(?P<first>\w+)\s(?P<second>\w+)`)
	match := re.FindStringSubmatch("hello world")
	for i, name := range re.SubexpNames() {
		if i != 0 {
			fmt.Printf("%s: %s\n", name, match[i])
		}
	} // 输出:
  // first: hello
  // second: world
}
```

### 替换匹配的部分内容:
您可以使用 `ReplaceAllStringFunc` 方法来替换匹配到的字符串。

```go
package main

import (
	"fmt"
	"regexp"
	"strings"
)

func main() {
	re := regexp.MustCompile(`\w+`)
	result := re.ReplaceAllStringFunc("hello world", strings.ToUpper)
	fmt.Println(result) // 输出: HELLO WORLD
}
```

### 匹配任意数字:
使用 `\d` 可以匹配任意数字。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`\d+`)
	fmt.Println(re.MatchString("12345")) // 输出: true
}
```

### 匹配非数字字符:
使用 `\D` 可以匹配任意非数字字符。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`\D+`)
	fmt.Println(re.MatchString("abc")) // 输出: true
}
```

### 匹配单词边界:
`\b` 表示单词的边界。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`\bGo\b`)
	fmt.Println(re.MatchString("Go is great!")) // 输出: true
	fmt.Println(re.MatchString("Going on a trip")) // 输出: false
}
```

### 查找特定长度的匹配:
使用 `{min,max}` 可以匹配特定长度的字符。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`\d{2,4}`)
	fmt.Println(re.FindString("123"))    // 输出: 123
	fmt.Println(re.FindString("12345"))  // 输出: 1234
}
```

### 匹配行的开始与结束:
`^` 表示行的开始，`$` 表示行的结束。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`^hello`)
	fmt.Println(re.MatchString("hello world")) // 输出: true
	fmt.Println(re.MatchString("world hello")) // 输出: false
}
```

### 字符类匹配:
使用 `[]` 可以创建字符类。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`[aeiou]`)
	fmt.Println(re.FindAllString("hello world", -1)) // 输出: [e o o]
}
```

### 否定字符类:
使用 `[^...]` 可以匹配除指定字符之外的任意字符。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`[^aeiou]`)
	fmt.Println(re.FindAllString("hello world", -1)) // 输出: [h l l  w r l d]
}
```

### 匹配 Unicode 字符:
您可以使用 `\p` 和 `\P` 来匹配 `Unicode` 字符类。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`\p{L}`) // 匹配任何字母
	fmt.Println(re.FindAllString("你好, world!", -1)) // 输出: [你 好 w o r l d]
}
```

### 查找所有匹配的数量限制:
使用 `FindAll...` 函数时，您可以指定一个非负的整数来限制返回的匹配数量。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`\w+`)
	fmt.Println(re.FindAllString("hello world, go is great", 2)) // 输出: [hello world]
}
```

### 替换匹配的部分内容，并引用匹配的内容:
使用 `$1, $2,...` 来引用正则表达式中的分组。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`(\w+) (\w+)`)
	result := re.ReplaceAllString("hello world", "$2 $1")
	fmt.Println(result) // 输出: world hello
}
```

### 拆分字符串:
使用 `Split` 方法可以根据正则表达式拆分字符串。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`\s+`)
	fmt.Println(re.Split("hello\tworld  go is great", -1)) // 输出: [hello world go is great]
}
```

### 使用 Match 和 MatchString:
这两个函数用于检查字节切片或字符串是否与正则表达式匹配，但不返回实际匹配的内容。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`hello`)
	fmt.Println(re.Match([]byte("hello world")))   // 输出: true
	fmt.Println(re.MatchString("hello world"))    // 输出: true
	fmt.Println(re.MatchString("world goodbye"))  // 输出: false
}
```

### 匹配开始和结束的字符串或单词:
使用 `^` 和 `$` 可以匹配字符串的开始和结束，而 `\b` 可以匹配单词的开始和结束。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`^go\b`)
	fmt.Println(re.MatchString("go is great"))  // 输出: true
	fmt.Println(re.MatchString("going on"))     // 输出: false
}
```

### 匹配多选分支:
您可以使用 `|` 运算符来匹配多个子模式中的一个。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`apple|banana`)
	fmt.Println(re.MatchString("I have an apple"))   // 输出: true
	fmt.Println(re.MatchString("I have a banana"))  // 输出: true
	fmt.Println(re.MatchString("I have a cherry"))  // 输出: false
}
```

### 非捕获分组:
使用 `(?: ...)` 创建一个非捕获分组，这意味着它会匹配但不捕获内容。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`(?:apple|banana)s?`)
	fmt.Println(re.FindString("I have 2 apples and 1 banana"))  // 输出: apples
}
```

### 正向前视断言:
正向前视断言允许您检查所匹配的内容前面是否有指定的子模式，但不包含那个子模式。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`\d(?=px)`)
	fmt.Println(re.FindString("I have a font size of 14px"))  // 输出: 14
	fmt.Println(re.FindString("The number is 14."))           // 输出: ""
}
```

### 负向前视断言:
与正向前视断言相反，负向前视断言检查内容前面是否没有指定的子模式。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	// Go 的 regexp 库不直接支持负向前视断言。但这是一个通常的要求，所以如果您需要这个功能，您可能需要查找其他的库或使用其他方法。
}
```

### 字节切片匹配:
除了字符串外，`regexp` 也支持字节切片。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`hello`)
	fmt.Println(re.Match([]byte("hello world")))  // 输出: true
}
```

### 获取匹配的字符串及其位置:
使用 `FindStringSubmatch` 和 `FindStringSubmatchIndex` 可以获取匹配的字符串及其位置。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`(a)(b)(c)`)
	matches := re.FindStringSubmatch("abcdef")
	fmt.Println(matches)  // 输出: [abc a b c]

	indices := re.FindStringSubmatchIndex("abcdef")
	fmt.Println(indices)  // 输出: [0 3 0 1 1 2 2 3]
}
```

### 从源字符串中提取信息:
正则表达式不仅可以用于查找和替换，还可以从源字符串中提取特定的信息。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`(?P<Year>\d{4})-(?P<Month>\d{2})-(?P<Day>\d{2})`)
	match := re.FindStringSubmatch("Today's date is 2023-09-23.")
	for i, name := range re.SubexpNames() {
		if i != 0 {
			fmt.Printf("%s: %s\n", name, match[i])
		}
	} // 输出:
  // Year: 2023
  // Month: 09
  // Day: 23
}
```

### 贪婪匹配与非贪婪匹配:
默认情况下，正则表达式的 `*`, `+`, 和 `?` 限定符都是贪婪的，这意味着它们会尽可能多地匹配。但是，您可以通过在限定符后面添加 `?` 来进行非贪婪匹配。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	greedy := regexp.MustCompile(`a.*b`)
	fmt.Println(greedy.FindString("aabb")) // 输出: aabb

	nonGreedy := regexp.MustCompile(`a.*?b`)
	fmt.Println(nonGreedy.FindString("aabb")) // 输出: aab
}
```

### 使用反斜杠转义特殊字符:
正则表达式中的特殊字符（如 `*, +, ., ?`）需要使用 `\` 进行转义才能匹配其字面值。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`\.`)
	fmt.Println(re.MatchString(".")) // 输出: true
	fmt.Println(re.MatchString("a")) // 输出: false
}
```

### 编译多个正则表达式:
在某些情况下，您可能需要同时处理多个正则表达式。使用 `Compile` 可以为每个正则表达式创建一个独立的对象。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	expressions := []string{`apple`, `banana`, `cherry`}
	regexps := make([]*regexp.Regexp, len(expressions))

	for i, expr := range expressions {
		regexps[i] = regexp.MustCompile(expr)
	}

	test := "I like apple and cherry."
	for _, re := range regexps {
		if re.MatchString(test) {
			fmt.Println("Matched:", re.String())
		}
	}
}
```

### 使用 FindAllStringSubmatch 获取所有子匹配:
这个函数会返回所有匹配，每个匹配都是一个字符串切片，其中第一个元素是完整的匹配，后面的元素是每个子匹配。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`(\w+), (\w+)`)
	allMatches := re.FindAllStringSubmatch("apple, pie; cherry, pie; peach, cobbler", -1)

	for _, match := range allMatches {
		fmt.Println("Full match:", match[0])
		fmt.Println("First group:", match[1])
		fmt.Println("Second group:", match[2])
	}
}
```

### 使用 SubexpNames 获取分组的名称:
当您使用命名分组时，该方法会返回一个字符串切片，其中包含分组的名称。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`(?P<First>\w+), (?P<Second>\w+)`)
	names := re.SubexpNames()
	fmt.Println(names) // 输出: ["", "First", "Second"]
}
```

### 处理无效的正则表达式:
使用 `Compile`（而不是 `MustCompile`）时，如果提供的正则表达式无效，将返回错误。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re, err := regexp.Compile("[")
	if err != nil {
		fmt.Println("Error compiling regex:", err)
		return
	}
	fmt.Println(re.MatchString("test"))
}
```

### 完整的匹配:
使用 `^` 和 `$` 可以确保整个字符串与正则表达式匹配。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`^abc$`)
	fmt.Println(re.MatchString("abc"))   // 输出: true
	fmt.Println(re.MatchString("abcdef")) // 输出: false
	fmt.Println(re.MatchString("xabc"))   // 输出: false
}
```

### 命令行工具的简单实现:
您可以使用 `regexp` 库创建一个简单的命令行工具来匹配和替换文本。

```go
package main

import (
	"bufio"
	"fmt"
	"os"
	"regexp"
)

func main() {
	if len(os.Args) != 3 {
		fmt.Println("Usage: match <pattern> <replacement>")
		return
	}

	pattern := os.Args[1]
	replacement := os.Args[2]

	re, err := regexp.Compile(pattern)
	if err != nil {
		fmt.Println("Invalid pattern:", err)
		return
	}

	scanner := bufio.NewScanner(os.Stdin)
	for scanner.Scan() {
		line := scanner.Text()
		fmt.Println(re.ReplaceAllString(line, replacement))
	}

	if err := scanner.Err(); err != nil {
		fmt.Fprintln(os.Stderr, "Reading from stdin:", err)
	}
}
```

### 检查是否包含子模式:
可以使用 `(?i)` 在正则表达式中执行不区分大小写的匹配。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`(?i)go`)
	fmt.Println(re.MatchString("GOlang")) // 输出: true
}
```

### 限制匹配的次数:
可以使用 `{m,n}` 来匹配一个模式至少 `m` 次，但不超过 `n` 次。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`a{2,4}`)
	fmt.Println(re.FindAllString("aaaaaa", -1)) // 输出: [aaaa aa]
}
```

### 匹配字母、数字或下划线:
`\w` 是一个常见的字符类，用于匹配任何字母、数字或下划线。对应的，`\W` 匹配任何非字母、数字或下划线的字符。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`\w+`)
	fmt.Println(re.FindAllString("Hello, world!", -1)) // 输出: [Hello world]
}
```

### 匹配任何空白字符:
`\s` 匹配任何空白字符，包括空格、制表符、换行符等。相对的，`\S` 匹配任何非空白字符。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`\S+`)
	fmt.Println(re.FindAllString("Hello, world!", -1)) // 输出: [Hello, world!]
}
```

### 匹配字符串的开头和结尾:
在多行模式下，`^` 和 `$` 分别匹配每一行的开头和结尾。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`(?m)^test$`)
	fmt.Println(re.FindAllString("test\ntest\ntesting", -1)) // 输出: [test test]
}
```

### 使用反向引用:
在 `Go` 的正则表达式中，您可以使用 `\1`、`\2` 等来引用先前捕获的分组。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`(a)b\1`) // 这会匹配 'aba'
	fmt.Println(re.MatchString("aba"))  // 输出: true
	fmt.Println(re.MatchString("abb"))  // 输出: false
}
```

### 匹配 Unicode 字符:
使用 `\p{Sc}` 可以匹配货币符号，而 `\p{L}` 可以匹配任何字母。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`\p{L}+`)
	fmt.Println(re.FindAllString("你好, world!", -1)) // 输出: [你好 world]
}
```

### 预处理常用正则表达式:
在实际应用中，您可能会重复使用某些正则表达式。预编译这些正则表达式可以提高性能。

```go
package main

import (
	"fmt"
	"regexp"
)

var (
	emailRegex    = regexp.MustCompile(`^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$`)
	phoneNumberRegex = regexp.MustCompile(`^\+?[0-9\-]+\(?[0-9]+\)?[0-9\-]+[0-9]$`)
)

func main() {
	fmt.Println(emailRegex.MatchString("test@example.com"))    // 输出: true
	fmt.Println(phoneNumberRegex.MatchString("+123-456-7890")) // 输出: true
}
```

### 匹配单词边界:
`\b` 用于匹配单词的边界，这使得您可以准确地匹配整个单词，而不是部分单词。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`\bgo\b`)
	fmt.Println(re.MatchString("go"))       // 输出: true
	fmt.Println(re.MatchString("golang"))   // 输出: false
	fmt.Println(re.MatchString("let's go!")) // 输出: true
}
```

### 使用命名分组:
使用命名分组可以使正则表达式更加清晰，并在后续处理中提供更有意义的引用。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`(?P<year>\d{4})-(?P<month>\d{2})-(?P<day>\d{2})`)
	match := re.FindStringSubmatch("2023-09-23")
	for i, name := range re.SubexpNames() {
		if i != 0 && name != "" {
			fmt.Printf("%s: %s\n", name, match[i])
		}
	}
	// 输出:
	// year: 2023
	// month: 09
	// day: 23
}
```

### 匹配字符串的开始和结束位置:
在非多行模式下，`^` 和 `$` 分别匹配字符串的开始和结束位置。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`^start|end$`)
	fmt.Println(re.FindAllString("start of the string end", -1)) // 输出: [start end]
}
```

### 在替换中使用函数:
您可以使用 `ReplaceAllStringFunc` 方法在替换时执行一些逻辑。

```go
package main

import (
	"fmt"
	"regexp"
	"strings"
)

func main() {
	re := regexp.MustCompile(`\w+`)
	result := re.ReplaceAllStringFunc("Hello World", func(s string) string {
		return strings.ToUpper(s)
	})
	fmt.Println(result) // 输出: HELLO WORLD
}
```

### 替换中的反向引用:
在替换字符串中，您可以使用 `$1`、`$2` 等来引用正则表达式中的分组。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`(\w+) (\w+)`)
	fmt.Println(re.ReplaceAllString("Hello World", "$2 $1")) // 输出: World Hello
}
```

### 使用正向和负向前视断言:
注意：`Go` 的 `regexp` 包不支持前视断言。但为了完整性，我还是想提一下这个概念。在许多其他语言和正则表达式引擎中，前视断言允许您进行匹配，但不消费任何字符。
例如, `x(?=y)` 会匹配 `x` 只有当 `x` 后面跟着 `y`。这是正向前视断言。与之相对的是负向前视断言 `x(?!y)`，它会匹配 `x` 只有当 `x` 后面没有跟着 `y`。

### 获取编译正则表达式的字符串:
您可以使用 `String` 方法获取编译后的正则表达式字符串。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`hello.*world`)
	fmt.Println(re.String()) // 输出: hello.*world
}
```

### 使用 LiteralPrefix:
这个函数返回一个字符串和一个布尔值。如果正则表达式可以被解释为一个简单的字符串查找，则返回的字符串是该查找字符串，布尔值为 `true`。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`hello`)
	prefix, complete := re.LiteralPrefix()
	fmt.Println(prefix, complete) // 输出: hello true
}
```

### 查找字符串中所有非重叠匹配:
使用 `FindAll` 和 `FindAllString` 可以查找所有的匹配。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`a.`)
	fmt.Println(re.FindAllString("ab ac ad ae", -1)) // 输出: [ab ac ad ae]
}
```

### 查找和匹配的区别:
在 `Go` 中，查找和匹配的函数有所不同。查找函数 (如 `FindString`) 返回匹配的字符串，而匹配函数 (如 `MatchString`) 返回一个布尔值，表示是否找到了匹配项。

```go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`go`)
	fmt.Println(re.FindString("golang"))  // 输出: go
	fmt.Println(re.MatchString("golang")) // 输出: true
}
```
