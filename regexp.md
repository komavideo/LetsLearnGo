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

### 

```go
```
### 

```go
```
