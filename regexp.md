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

### 

```go
```
### 

```go
```
