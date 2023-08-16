使用html/template库
===================

`html/template` 库是 Go 语言中的一个内建库，它允许你将数据与 HTML 模板结合起来，从而生成动态的 HTML 内容。

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

## Go 语言 html/template 库

### 1. 基础示例

这是一个简单的示例，展示了如何使用 `html/template` 来渲染一个字符串模板：

```go
package main

import (
	"fmt"
	"html/template"
	"os"
)

func main() {
	// 定义一个简单的模板
	const tmpl = `<p>Hello, {{.}}!</p>`

	// 解析模板
	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		fmt.Println("Error:", err)
		return
	}

	// 执行模板
	err = t.Execute(os.Stdout, "World")
	if err != nil {
		fmt.Println("Error:", err)
	}
}
```

上述代码将输出：

```
<p>Hello, World!</p>
```

### 2. 使用结构体

你可以使用结构体来传递更复杂的数据给模板：

```go
package main

import (
	"fmt"
	"html/template"
	"os"
)

type Person struct {
	Name string
	Age  int
}

func main() {
	const tmpl = `<p>{{.Name}} is {{.Age}} years old.</p>`

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		fmt.Println("Error:", err)
		return
	}

	p := Person{Name: "Alice", Age: 30}
	err = t.Execute(os.Stdout, p)
	if err != nil {
		fmt.Println("Error:", err)
	}
}
```

这段代码会输出：

```
<p>Alice is 30 years old.</p>
```

### 3. 使用条件和循环

模板也支持条件和循环。这是一个更复杂的示例：

```go
package main

import (
	"fmt"
	"html/template"
	"os"
)

type Person struct {
	Name  string
	Age   int
	Hobby []string
}

func main() {
	const tmpl = `
	<p>{{.Name}} is {{.Age}} years old.</p>
	<p>Hobbies:</p>
	<ul>
	{{range .Hobby}}
		<li>{{.}}</li>
	{{else}}
		<li>No hobbies found.</li>
	{{end}}
	</ul>
	`

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		fmt.Println("Error:", err)
		return
	}

	p := Person{Name: "Bob", Age: 35, Hobby: []string{"Reading", "Swimming"}}
	err = t.Execute(os.Stdout, p)
	if err != nil {
		fmt.Println("Error:", err)
	}
}
```

这段代码会输出：

```
<p>Bob is 35 years old.</p>
<p>Hobbies:</p>
<ul>
	<li>Reading</li>
	<li>Swimming</li>
</ul>
```

### 4. 自定义模板函数

你可以向模板中添加自定义函数，这样可以在模板中执行自定义的逻辑。例如，你可以添加一个函数来转换文本为大写：

```go
package main

import (
	"fmt"
	"html/template"
	"os"
	"strings"
)

func main() {
	funcMap := template.FuncMap{
		"toUpperCase": strings.ToUpper,
	}

	const tmpl = `<p>{{.Name | toUpperCase}} is {{.Age}} years old.</p>`

	t, err := template.New("test").Funcs(funcMap).Parse(tmpl)
	if err != nil {
		fmt.Println("Error:", err)
		return
	}

	p := Person{Name: "Charlie", Age: 28}
	err = t.Execute(os.Stdout, p)
	if err != nil {
		fmt.Println("Error:", err)
	}
}
```

这段代码会输出：

```
<p>CHARLIE is 28 years old.</p>
```

### 5. 嵌套模板和定义

你可以在一个模板中定义多个块，并在其他模板中重用这些块。这对于像页眉和页脚这样的公共部分特别有用：

```go
package main

import (
	"fmt"
	"html/template"
	"os"
)

func main() {
	const baseTmpl = `
{{define "header"}}<header>This is a header.</header>{{end}}
{{define "footer"}}<footer>This is a footer.</footer>{{end}}
`

	const pageTmpl = `
{{template "header"}}
<p>{{.Name}} is {{.Age}} years old.</p>
{{template "footer"}}
`

	t, err := template.New("base").Parse(baseTmpl)
	if err != nil {
		fmt.Println("Error:", err)
		return
	}

	_, err = t.New("page").Parse(pageTmpl)
	if err != nil {
		fmt.Println("Error:", err)
		return
	}

	p := Person{Name: "David", Age: 40}
	err = t.ExecuteTemplate(os.Stdout, "page", p)
	if err != nil {
		fmt.Println("Error:", err)
	}
}
```

输出为：

```
<header>This is a header.</header>
<p>David is 40 years old.</p>
<footer>This is a footer.</footer>
```

### 6. 防止跨站脚本攻击（XSS）

`html/template` 库默认会转义所有的字符串，这可以防止跨站脚本攻击（XSS）。例如，如果模板数据中包含 &lt;script&gt; 标签，它会被转义，从而防止恶意脚本的执行：

```go
package main

import (
	"html/template"
	"os"
)

func main() {
	const tmpl = `<p>{{.}}</p>`

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		fmt.Println("Error:", err)
		return
	}

	data := "<script>alert('Hacked!');</script>"
	err = t.Execute(os.Stdout, data)
	if err != nil {
		fmt.Println("Error:", err)
	}
}
```

输出为：

```
<p>&lt;script&gt;alert('Hacked!');&lt;/script&gt;</p>
```

### 7. 使用嵌套字段

当你使用结构体作为模板的数据源时，你可以轻松地访问嵌套的字段：

```go
package main

import (
	"html/template"
	"os"
)

type Address struct {
	City  string
	State string
}

type User struct {
	Name    string
	Age     int
	Address Address
}

func main() {
	const tmpl = `
<p>{{.Name}} lives in {{.Address.City}}, {{.Address.State}}.</p>
`

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}

	user := User{
		Name: "Eve",
		Age:  25,
		Address: Address{
			City:  "New York",
			State: "NY",
		},
	}

	t.Execute(os.Stdout, user)
}
```

输出为：

```
<p>Eve lives in New York, NY.</p>
```

### 8. 多模板文件

当你的应用变得更加复杂时，你可能希望将模板分散到多个文件中。你可以使用 `template.ParseGlob` 或 `template.ParseFiles` 来解析多个模板文件：

```go
// 假设你有两个文件：header.tmpl 和 footer.tmpl
t, err := template.ParseGlob("templates/*.tmpl")
```

或者

```go
t, err := template.ParseFiles("templates/header.tmpl", "templates/footer.tmpl")
```

### 9. 设置模板分隔符

默认情况下，`{{` 和 `}}` 是模板的分隔符。但在某些情况下，你可能希望更改它们，尤其是当你的模板中包含很多 JavaScript 代码时。你可以使用 `Delims` 方法来设置自定义的分隔符：

```go
t, err := template.New("test").Delims("[[", "]]").Parse("[[.Name]] lives in [[.Address.City]].")
```

### 10. 使用模板注释

在模板中，你可以使用 `{{/* ... */}}` 来添加注释。这些注释在执行模板时不会出现在输出中：

```go
const tmpl = `
{{/* This is a template comment. It won't appear in the output. */}}
<p>{{.Name}} is {{.Age}} years old.</p>
`

t, err := template.New("test").Parse(tmpl)
if err != nil {
	panic(err)
}

user := User{Name: "Frank", Age: 30}
t.Execute(os.Stdout, user)
```

输出为：

```
<p>Frank is 30 years old.</p>
```

### 11. 使用with动作

`with` 动作可以改变当前点的上下文（`.`）。这可以简化模板中的引用，并有助于使模板更加整洁：

```go
package main

import (
	"html/template"
	"os"
)

func main() {
	const tmpl = `
{{with .Address}}
<p>{{$.Name}} lives in {{.City}}, {{.State}}.</p>
{{end}}
`

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}

	user := User{
		Name: "Grace",
		Age:  32,
		Address: Address{
			City:  "San Francisco",
			State: "CA",
		},
	}

	t.Execute(os.Stdout, user)
}
```

输出为：

```
<p>Grace lives in San Francisco, CA.</p>
```

在 `with` 块内部，`.` 引用的是 `Address`。要引用外部的上下文，我们使用 `$.Name`。

### 12. 使用-来控制空格和换行

有时，为了使模板更具可读性，你可能会在模板中添加额外的空格和换行。但这可能会导致输出结果中包含不必要的空格和换行。你可以使用 `-` 来控制它：

```go
const tmpl = `
{{- with .Address -}}
<p>{{$.Name}} lives in {{.City}}, {{.State}}.</p>
{{- end -}}
`

// 输出不会包含额外的换行和空格。
```

### 13. 使用block定义默认模板

`block` 是一个定义和执行模板的动作。如果定义的模板不存在，则使用 `block` 中的内容：

```go
const baseTmpl = `
{{define "title"}}Default Title{{end}}
{{block "body" .}}Default Body{{end}}
`

const customTmpl = `
{{define "title"}}Custom Title{{end}}
{{define "body"}}Custom Body{{end}}
`
```

### 14. 使用template动作导入其他模板

你可以使用 `template` 动作来导入并执行其他模板：

```go
const tmpl = `
{{template "title"}}
{{template "body"}}
`

t, err := template.New("base").Parse(baseTmpl)
if err != nil {
	panic(err)
}

_, err = t.New("custom").Parse(customTmpl)
if err != nil {
	panic(err)
}

t.ExecuteTemplate(os.Stdout, "custom", nil)
```

这将输出“Custom Title”和“Custom Body”。

### 15. 安全考虑

尽管 `html/template` 库会自动转义模板中的数据来防止跨站脚本攻击，但你仍然需要注意其他安全问题，如SQL注入、命令注入等。在使用模板时，始终确保你的数据来源是可信的，并且始终使用库中提供的方法和功能来处理数据。

### 16. 使用eq, ne, lt, le, gt, 和 ge

这些是比较函数，可以在模板中进行条件判断。例如：

```go
{{if eq .Name "Hannah"}}
<p>Hello, Hannah!</p>
{{else}}
<p>Hello, Guest!</p>
{{end}}
```

这个模板会根据 `.Name` 的值输出相应的问候。

### 17. 使用 `and` 和 `or`

这些函数可以在模板中用于逻辑运算：

```go
{{if and (eq .Name "Hannah") (gt .Age 20)}}
<p>Hello, adult Hannah!</p>
{{else}}
<p>Hello, Guest or young Hannah!</p>
{{end}}
```

### 18. 使用index

如果你的数据结构是一个数组或切片，你可以使用 `index` 来访问特定的元素：

```go
{{index . 1}}
```

这会输出切片或数组的第二个元素（因为索引是从0开始的）。

### 19. 跨结构体访问

在模板中，你可以轻松地跨多个结构体进行访问：

```go
type Team struct {
    Leader User
    Members []User
}

const tmpl = `
<p>Team Leader: {{.Leader.Name}}</p>
<p>Members:</p>
<ul>
{{range .Members}}
    <li>{{.Name}}</li>
{{end}}
</ul>
`

// ... 其他代码
```

### 20. 使用printf进行格式化输出

你可以使用 `printf` 函数来格式化输出数据：

```go
{{printf "%s is %d years old" .Name .Age}}
```

这会根据提供的格式输出数据。

### 21. 使用len获取长度

对于切片、数组或字符串，你可以使用 `len` 函数来获取其长度：

```go
{{if gt (len .Hobbies) 5}}
<p>You have many hobbies!</p>
{{else}}
<p>You have a few hobbies.</p>
{{end}}
```

### 22. 常量和变量

你不能在模板中定义变量，但你可以使用 `with` 动作来创建一个新的上下文范围。此外，模板不支持常量。

### 23. 错误处理

如果在解析或执行模板时出现错误，你应该始终检查错误并处理它。错误处理不仅可以帮助你识别潜在的问题，还可以确保应用的稳定性和安全性。

### 24. 使用else if

与许多模板语言一样，你可以在条件块中使用 `else if`：

```go
{{if eq .Name "Ian"}}
<p>Hello, Ian!</p>
{{else if eq .Name "Eva"}}
<p>Hello, Eva!</p>
{{else}}
<p>Hello, Guest!</p>
{{end}}
```

### 25. nil 的处理

当处理可能为 `nil` 的字段或值时，你必须格外小心。例如，如果你尝试访问一个 `nil` 结构体的字段，程序会崩溃。你可以在模板中使用 `with` 来避免这种情况：

```go
{{with .OptionalField}}
{{.SubField}}
{{end}}
```

如果 `OptionalField` 为 `nil`，则不会尝试访问 `SubField`。

### 26. 使用模板缓存

对于经常使用的模板，考虑使用模板缓存来提高性能。这意味着你应该预先解析模板并将其存储在内存中，而不是每次需要时都重新解析它。

### 27. 使用missingkey

当模板数据缺少某个键时，默认行为是返回零值（例如，对于字符串是空字符串）。但你可以使用 `Option` 方法更改此行为：

```go
template.New("test").Option("missingkey=zero")
template.New("test").Option("missingkey=error")
```

+ `missingkey=zero` 是默认行为，返回零值。
+ `missingkey=error` 当键丢失时返回错误。

### 28. 多行定义

为了提高模板的可读性，你可以在定义中使用多行：

```go
const tmpl = `
{{define "myTemplate"}}
Hello, {{.Name}}!
{{end}}
`
```

### 29. 原始字符串

在 Go 中，使用反引号 ` 可以定义多行原始字符串，这对于模板非常有用，因为你不需要为每个引号或换行符转义。

### 30. 链接模板操作

你可以链接多个模板操作，这在组合多个操作时非常有用：

```go
{{.Field1 | printf "%s" | title}}
```

这首先将 `Field1` 的值格式化为字符串，然后将其转换为标题格式（每个单词的首字母大写）。

### 31. 使用text/template

虽然我们讨论的主题是 `html/template`，但值得注意的是 Go 还有一个 `text/template` 库，它与 `html/template` 非常相似，但不自动进行HTML转义。当你不处理HTML时，`text/template` 可能更适合你，例如生成配置文件或其他文本格式。

### 32. 为模板设置默认值

在某些情况下，你可能希望为某些字段设置默认值。这可以使用 or 功能完成：

```go
{{.Name | or "Default Name"}}
```

如果 `.Name` 为空或不存在，将使用 `"Default Name"`。

### 33. 嵌套模板

你可以在一个模板中嵌套另一个模板，这对于创建可重用的UI组件非常有用：

```go
{{define "main"}}
    {{template "header" .}}
    <div>Content goes here</div>
    {{template "footer" .}}
{{end}}
```

### 34. 使用struct 标签进行模板匹配

当你的数据是一个结构体时，你可以使用结构体标签来改变模板中的字段名称：

```go
type Data struct {
    FieldName string `template:"field_name"`
}
```

在模板中，你可以使用 `.field_name` 来引用 `FieldName`。

### 35. 处理模板执行错误

执行模板时，可能会遇到错误，例如尝试访问 `nil` 指针的字段。为了处理这些错误，你应该始终检查 `Execute` 或 `ExecuteTemplate` 的返回值：

```go
err := tmpl.Execute(writer, data)
if err != nil {
    // Handle error
}
```

### 36. 使用init 预加载模板

对于Web应用，通常建议在应用启动时预加载所有模板，这样可以立即检测任何模板错误，并避免每次请求都重新解析模板：

```go
var templates *template.Template

func init() {
    templates = template.Must(template.ParseGlob("path/to/templates/*"))
}
```

### 37. 使用模板的自定义类型

除了基本的数据类型（如字符串、整数和浮点数）外，你还可以在模板中使用自定义类型，只要这些类型具有适当的方法：

```go
type CustomType int

func (c CustomType) String() string {
    return fmt.Sprintf("Custom: %d", c)
}
```

你可以直接在模板中使用此类型，String 方法将自动调用。

### 38. 使用New 创建命名模板

使用 `New` 函数创建模板时，可以为模板指定一个名称，这在稍后使用 `ExecuteTemplate` 时非常有用：

```go
t := template.New("myTemplate")
```

### 39. 使用Clone 和 Copy

`Clone` 和 `Copy` 方法可以用来复制模板。这在你希望基于现有模板创建新模板，但不想修改原始模板时很有用。

### 40. 关于性能

虽然 `html/template` 为安全的HTML生成提供了强大的功能，但与直接的字符串连接或使用诸如 `bytes.Buffer` 的方法相比，它可能会稍慢一些。在性能关键的应用中，测试和基准测试是关键。

Done.
