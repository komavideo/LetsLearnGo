使用text/template库
===================

Go语言的 `text/template` 库是一个强大的模板引擎，用于处理文本格式的数据。

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

## Go 语言 text/template 库

### 1. 基本用法

我们先从最基本的例子开始。假设我们有一个模板字符串，其中包含一些占位符，并希望将其替换为实际值。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	type Person struct {
		Name string
		Age  int
	}

	tmpl := `{{.Name}} is {{.Age}} years old`
	person := Person{Name: "Alice", Age: 30}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, person)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Alice is 30 years old
```

### 2. 条件语句

我们可以在模板中使用条件语句来控制输出。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	type Person struct {
		Name string
		Age  int
	}

	tmpl := `{{if .Age}} {{.Name}} is {{.Age}} years old{{else}} Age of {{.Name}} is unknown{{end}}`
	person := Person{Name: "Alice", Age: 0}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, person)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Age of Alice is unknown
```

### 3. 循环语句

我们还可以在模板中使用循环来重复某些元素。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	type Person struct {
		Name  string
		Hobbies []string
	}

	tmpl := `{{.Name}} has hobbies:{{range .Hobbies}} {{.}}{{end}}`
	person := Person{Name: "Alice", Hobbies: []string{"Reading", "Swimming"}}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, person)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Alice has hobbies: Reading Swimming
```

### 4. 自定义函数

你可以在模板中使用自定义函数。下面的示例演示了如何创建并使用自定义函数来转换字符串为大写。

```go
package main

import (
	"text/template"
	"os"
	"strings"
)

func main() {
	funcs := template.FuncMap{
		"upper": strings.ToUpper,
	}

	tmpl := `{{.Name | upper}}`
	person := struct {
		Name string
	}{"Alice"}

	t, err := template.New("test").Funcs(funcs).Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, person)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
ALICE
```

### 5. 嵌套模板

你可以在一个模板中嵌套另一个模板，以实现更复杂的布局。下面的示例演示了如何创建和使用嵌套模板。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	const mainTmpl = `{{define "T"}}{{.Name}}: {{.Age}}{{end}}{{template "T" .}} years old`

	person := struct {
		Name string
		Age  int
	}{"Alice", 30}

	t, err := template.New("main").Parse(mainTmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, person)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Alice: 30 years old
```

### 6. 多文件模板解析

如果你的模板分散在多个文件中，可以使用 `ParseGlob` 或 `ParseFiles` 函数来解析它们。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	// 假设 "templates" 文件夹下有多个模板文件
	t, err := template.ParseGlob("templates/*.tmpl")
	if err != nil {
		panic(err)
	}
	err = t.ExecuteTemplate(os.Stdout, "specific.tmpl", nil) // 请替换为实际的模板文件名
	if err != nil {
		panic(err)
	}
}
```

请确保模板文件存在于指定的路径中，并根据需要修改模板名称。

### 7. 模板变量

你可以在模板中定义和使用变量。下面的示例展示了如何在模板中定义和使用变量。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `{{with $x := "Hello"}}{{$x}}, World!{{end}}`

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, nil)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Hello, World!
```

### 8. 处理空值

在模板中，你可能需要处理空值或缺失的字段。下面的示例演示了如何使用 `with` 和 `else` 来处理空值。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	type Person struct {
		Name string
	}

	tmpl := `{{with .Name}}{{.}}{{else}}Name is missing{{end}}`

	person := Person{}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, person)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Name is missing
```

### 9. 使用管道操作符

在模板中，你可以使用管道操作符 `|` 将一个函数的输出作为另一个函数的输入。下面的示例演示了如何使用管道操作符。

```go
package main

import (
	"text/template"
	"os"
	"strings"
)

func main() {
	funcs := template.FuncMap{
		"lower": strings.ToLower,
	}

	tmpl := `{{"HELLO" | lower}}`

	t, err := template.New("test").Funcs(funcs).Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, nil)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
hello
```

### 10. 字符串格式化

你可以使用 `printf` 功能在模板中进行字符串格式化。下面的示例展示了如何使用 `printf` 来格式化输出。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `{{printf "%s has %d apples" .Name .Count}}`

	person := struct {
		Name  string
		Count int
	}{"Alice", 5}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, person)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Alice has 5 apples
```

### 11. 使用嵌套结构

你可以在模板中使用嵌套结构并访问它们的字段。以下示例演示了如何使用嵌套结构。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	type Address struct {
		City string
	}
	type Person struct {
		Name    string
		Address Address
	}

	tmpl := `{{.Name}} lives in {{.Address.City}}`

	person := Person{Name: "Alice", Address: Address{City: "New York"}}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, person)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Alice lives in New York
```

### 12. 使用方法

在模板中，你还可以调用结构体的方法。以下示例展示了如何调用方法。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	type Person struct {
		Name string
		Age  int
	}

	func (p Person) Greeting() string {
		return "Hello, " + p.Name
	}

	tmpl := `{{.Greeting}}`

	person := Person{Name: "Alice", Age: 30}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, person)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Hello, Alice
```

### 13. 模板块

你可以使用块来创建具有默认内容的模板部分，然后在其他模板中重写它们。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	const master = `Name: {{block "name" .}}{{.Name}}{{end}}`
	const overlay = `{{define "name"}}{{.FirstName}} {{.LastName}}{{end}}`

	person := struct {
		FirstName string
		LastName  string
		Name      string
	}{"Alice", "Johnson", "Alice Johnson"}

	masterTmpl, err := template.New("master").Parse(master)
	if err != nil {
		panic(err)
	}

	overlayTmpl, err := template.Must(masterTmpl.Clone()).Parse(overlay)
	if err != nil {
		panic(err)
	}

	err = overlayTmpl.Execute(os.Stdout, person)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Name: Alice Johnson
```

### 14. 使用模板注释

你可以在模板中添加注释来提高可读性。注释在执行模板时不会产生输出。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `{{/* This is a comment */}}{{.Name}}`

	person := struct {
		Name string
	}{"Alice"}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, person)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Alice
```

### 15. 处理 JSON 数据

你可以使用 `text/template` 库来处理 JSON 格式的数据。以下示例展示了如何解析 JSON 数据并将其传递给模板。

```go
package main

import (
	"encoding/json"
	"text/template"
	"os"
)

func main() {
	const jsonStr = `{"Name": "Alice", "Age": 30}`
	const tmplStr = `{{.Name}} is {{.Age}} years old`

	var data map[string]interface{}
	if err := json.Unmarshal([]byte(jsonStr), &data); err != nil {
		panic(err)
	}

	t, err := template.New("test").Parse(tmplStr)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Alice is 30 years old
```

### 16. 逻辑运算

你可以在模板中使用逻辑运算符进行条件判断。以下示例演示了如何使用逻辑运算。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `{{if and .HasName (eq .Age 30)}}Name is available and age is 30{{end}}`

	data := struct {
		HasName bool
		Age     int
	}{true, 30}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Name is available and age is 30
```

### 17. 带参数的自定义函数

你可以在自定义函数中使用参数。以下示例展示了如何创建并使用带参数的自定义函数。

```go
package main

import (
	"text/template"
	"os"
)

func multiply(x, y int) int {
	return x * y
}

func main() {
	funcs := template.FuncMap{
		"multiply": multiply,
	}

	tmpl := `{{multiply 5 3}}`

	t, err := template.New("test").Funcs(funcs).Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, nil)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
15
```

### 18. 链接多个模板

你可以链接多个模板以共享定义和内容。以下示例展示了如何链接多个模板。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	const header = `Welcome, {{.Name}}!`
	const footer = `Thank you, {{.Name}}!`

	headerTmpl := template.New("header")
	headerTmpl.Parse(header)

	footerTmpl := template.Must(headerTmpl.Clone())
	footerTmpl.Parse(footer)

	data := struct {
		Name string
	}{"Alice"}

	headerTmpl.Execute(os.Stdout, data)
	footerTmpl.Execute(os.Stdout, data)
}
```

输出：

```
Welcome, Alice!
Thank you, Alice!
```

### 19. 使用 - 控制空白

在模板处理中，你可能会注意到生成的内容中有一些不期望的空白或换行。为了更好地控制这些空白，你可以使用 `-` 修饰符。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{- "Hello" -}} 
{{- "World" -}}
`

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, nil)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
HelloWorld
```

### 20. 使用 else if

模板支持 `else if` 结构，使得在模板中的条件判断更加灵活。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{if eq .Age 30}}
Age is 30
{{else if eq .Age 40}}
Age is 40
{{else}}
Age is unknown
{{end}}
`

	data := struct {
		Age int
	}{40}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Age is 40
```

### 21. 模板嵌套使用 .

当在模板中使用范围或其他结构时，. 变量会改变其上下文。以下示例展示了这种情况。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{range .Items}}
Item: {{.}}
{{end}}
`

	data := struct {
		Items []string
	}{"apple", "banana", "cherry"}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Item: apple
Item: banana
Item: cherry
```

### 22. 使用自定义分隔符

默认情况下，模板使用 `{{` 和 `}}` 作为动作的开始和结束分隔符。但有时，你可能希望更改这些分隔符，特别是当模板内容与这些字符冲突时。以下示例展示了如何使用自定义分隔符。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
[[.Name]] is [[.Age]] years old.
`

	t, err := template.New("test").Delims("[[", "]]").Parse(tmpl)
	if err != nil {
		panic(err)
	}

	data := struct {
		Name string
		Age  int
	}{"Alice", 30}

	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Alice is 30 years old.
```

### 23. 使用 $ 访问根对象

在模板的范围或其他结构中，你可以使用 `$` 来访问根对象。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{range .Items}}
Item: {{.}}, Root Name: {{$}}
{{end}}
`

	data := struct {
		Name  string
		Items []string
	}{"RootName", []string{"apple", "banana", "cherry"}}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Item: apple, Root Name: {RootName [apple banana cherry]}
Item: banana, Root Name: {RootName [apple banana cherry]}
Item: cherry, Root Name: {RootName [apple banana cherry]}
```

### 24. 字符串操作

使用 `text/template`，你可以进行一些基本的字符串操作，例如获取子字符串。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	funcs := template.FuncMap{
		"substr": func(value string, start, end int) string {
			return value[start:end]
		},
	}

	tmpl := `{{substr . 0 5}}`

	t, err := template.New("test").Funcs(funcs).Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, "abcdefghijklm")
	if err != nil {
		panic(err)
	}
}
```

输出：

```
abcde
```

### 25. 使用模板字符串

有时，你可能需要在模板中动态生成另一个模板字符串，然后执行它。以下示例展示了如何在模板中创建并执行另一个模板。

```go
package main

import (
	"text/template"
	"os"
	"bytes"
)

func main() {
	tmpl := `{{define "T2"}}{{.Name}} is {{.Age}} years old{{end}}{{template "T2" .}}`

	data := struct {
		Name string
		Age  int
	}{"Alice", 30}

	t, err := template.New("T1").Parse(tmpl)
	if err != nil {
		panic(err)
	}

	var buf bytes.Buffer
	if err := t.Execute(&buf, data); err != nil {
		panic(err)
	}

	// 输出从上一个模板中生成的内容
	os.Stdout.Write(buf.Bytes())
}
```

输出：

```
Alice is 30 years old
```

### 26. 多级嵌套

你可以在模板中使用多级嵌套结构。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	type GrandChild struct {
		Name string
	}
	type Child struct {
		GrandChildren []GrandChild
	}
	type Parent struct {
		Children []Child
	}

	tmpl := `
{{range .Children}}
	{{range .GrandChildren}}
		GrandChild: {{.Name}}
	{{end}}
{{end}}
`

	parent := Parent{
		Children: []Child{
			{
				GrandChildren: []GrandChild{
					{Name: "GrandChild1"},
					{Name: "GrandChild2"},
				},
			},
		},
	}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, parent)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
GrandChild: GrandChild1
GrandChild: GrandChild2
```

### 27. 创建关联数组

在 Go 中，你可以使用 map 创建关联数组，并在模板中使用它。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `{{range $key, $value := .}}
Key: {{$key}}, Value: {{$value}}
{{end}}`

	data := map[string]string{
		"first":  "Alice",
		"second": "Bob",
		"third":  "Charlie",
	}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Key: first, Value: Alice
Key: second, Value: Bob
Key: third, Value: Charlie
```

### 28. 字符串连接

你可以在模板中连接字符串。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `{{.FirstName}} {{.LastName}}`

	data := struct {
		FirstName string
		LastName  string
	}{"Alice", "Johnson"}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Alice Johnson
```

### 29. 使用模板与其他数据类型

除了字符串和结构体，你还可以使用其他数据类型，如切片和数字。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `{{range .}}{{.}} {{end}}`

	data := []int{1, 2, 3, 4, 5}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
1 2 3 4 5 
```

### 30. 执行其他模板

你可以在一个模板中执行另一个模板。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	const tmplStr = `
{{define "list"}}
	{{range .}} {{.}} {{end}}
{{end}}

{{template "list" .Items}}
`

	data := struct {
		Items []string
	}{"apple", "banana", "cherry"}

	t, err := template.New("main").Parse(tmplStr)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
apple banana cherry 
```

### 31. 处理时间

你可以在模板中格式化和显示时间。

```go
package main

import (
	"text/template"
	"os"
	"time"
)

func main() {
	tmpl := `The time is: {{. | dateFormat "2006-01-02 15:04:05"}}`

	funcs := template.FuncMap{
		"dateFormat": func(format string, t time.Time) string {
			return t.Format(format)
		},
	}

	t, err := template.New("test").Funcs(funcs).Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, time.Now())
	if err != nil {
		panic(err)
	}
}
```

输出样例（输出会随时间变化）：

```
The time is: 2023-08-14 12:34:56
```

### 32. 使用 text/template 进行 HTML 渲染

使用 `html/template` 包，你可以确保在模板中的数据在渲染到 HTML 时是安全的。

```go
package main

import (
	"html/template"
	"os"
)

func main() {
	tmpl := `Hello, {{.Name}}!<br>{{.Comment}}`

	data := struct {
		Name    string
		Comment template.HTML
	}{"Alice", "<script>alert('Hello!');</script>"}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Hello, Alice!<br><script>alert('Hello!');</script>
```

注意：在这个例子中，我们使用了 `html/template` 而不是 `text/template`。这是因为我们需要处理可能包含 HTML 的数据。

### 33. 使用模板标签

模板标签可以帮助你更好地组织和控制模板的渲染流程。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{define "T1"}}World{{end}}
Hello, {{template "T1"}}
`

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, nil)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Hello, World
```

### 34. 使用模板继承

通过模板继承，你可以基于一个主模板创建多个子模板。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	const baseTmpl = `
{{define "T"}}Hello, {{block "name" .}}World{{end}}{{end}}
`

	const overlayTmpl = `
{{define "name"}}Alice{{end}}
`

	t, err := template.New("base").Parse(baseTmpl)
	if err != nil {
		panic(err)
	}

	_, err = t.New("overlay").Parse(overlayTmpl)
	if err != nil {
		panic(err)
	}

	err = t.ExecuteTemplate(os.Stdout, "T", nil)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Hello, Alice
```

### 35. 使用模板别名

有时候，你可能想要为模板定义一个别名。以下示例展示了如何为模板定义一个别名：

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{define "T"}}Hello, {{.}}!{{end}}

{{template "MyAlias" "World"}}
`

	t, err := template.New("base").Parse(tmpl)
	if err != nil {
		panic(err)
	}

	t.AddParseTree("MyAlias", t.Lookup("T").Tree)

	err = t.Execute(os.Stdout, nil)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Hello, World!
```

### 36. 使用 eq, lt, gt 等比较操作

模板提供了一些比较操作，如 `eq`（等于）、`lt`（小于）、`gt`（大于）等，以便进行条件判断。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{if eq .Name "Alice"}}Hello, Alice!{{else}}Hello, stranger!{{end}}
`

	data := struct {
		Name string
	}{"Alice"}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Hello, Alice!
```

### 37. 使用 index 访问数组或切片元素

你可以使用 `index` 函数访问数组、切片或映射中的元素。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `{{index . 1}}`

	data := []string{"apple", "banana", "cherry"}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
banana
```

### 38. 使用 len 获取长度

你可以使用 `len` 函数获取数组、切片或字符串的长度。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `{{len .}}`

	data := []int{1, 2, 3, 4, 5}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
5
```

### 39. 使用 printf 进行格式化输出

`printf` 函数可以用于格式化字符串输出。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `{{printf "%q" .}}`

	data := "Hello, World!"

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
"Hello, World!"
```

### 40. 使用 not 进行逻辑否定

你可以使用 `not` 函数进行逻辑否定。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{if not .IsAdmin}}You are not an admin.{{else}}Welcome, admin!{{end}}
`

	data := struct {
		IsAdmin bool
	}{false}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
You are not an admin.
```

### 41. 嵌入其他文本格式

你可以在模板中嵌入其他文本格式，如 JSON。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{
	"name": "{{.Name}}",
	"age": {{.Age}}
}
`

	data := struct {
		Name string
		Age  int
	}{"Alice", 30}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
{
	"name": "Alice",
	"age": 30
}
```

### 42. 使用 call 调用模板函数

你可以使用 `call` 功能调用模板中的函数，并传递参数。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	funcs := template.FuncMap{
		"add": func(a, b int) int {
			return a + b
		},
	}

	tmpl := `{{call .Func .A .B}}`

	data := struct {
		Func func(int, int) int
		A    int
		B    int
	}{funcs["add"].(func(int, int) int), 5, 3}

	t, err := template.New("test").Funcs(funcs).Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
8
```

### 43. 使用 range 与 else

当使用 `range` 遍历切片或数组时，你可以使用 `else` 指定当切片或数组为空时的输出。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{range .Items}}Item: {{.}}{{else}}No items available.{{end}}
`

	data := struct {
		Items []string
	}{}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
No items available.
```

### 44. 使用 with 改变当前点 (.)

`with` 动作可以更改当前点 (.) 的上下文，这在访问嵌套的结构或字段时非常有用。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{with .Person}}
Name: {{.Name}}, Age: {{.Age}}
{{end}}
`

	data := struct {
		Person struct {
			Name string
			Age  int
		}
	}{"Alice", 30}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Name: Alice, Age: 30
```

### 45. 使用模板变量

你可以在模板中定义和使用变量。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{ $name := .Name }}
{{ $age := .Age }}
Name: {{$name}}, Age: {{$age}}
`

	data := struct {
		Name string
		Age  int
	}{"Alice", 30}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Name: Alice, Age: 30
```

### 46. 使用内置函数 lower 和 upper

你可以使用内置的 `lower` 和 `upper` 函数转换字符串为小写或大写。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
Name in lowercase: {{lower .Name}}, Name in uppercase: {{upper .Name}}
`

	funcs := template.FuncMap{
		"lower": func(s string) string { return strings.ToLower(s) },
		"upper": func(s string) string { return strings.ToUpper(s) },
	}

	data := struct {
		Name string
	}{"Alice"}

	t, err := template.New("test").Funcs(funcs).Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Name in lowercase: alice, Name in uppercase: ALICE
```

### 47. 在模板中使用管道

在模板中，你可以使用管道来将一个函数的输出传递为另一个函数的输入。

```go
package main

import (
	"text/template"
	"os"
	"strings"
)

func main() {
	tmpl := `
{{.Name | lower | title}}
`

	funcs := template.FuncMap{
		"lower": func(s string) string { return strings.ToLower(s) },
		"title": func(s string) string { return strings.Title(s) },
	}

	data := struct {
		Name string
	}{"ALICE"}

	t, err := template.New("test").Funcs(funcs).Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Alice
```

### 48. 用 else 与 with

与 `if` 语句类似，你可以与 `with` 一起使用 `else` 来处理 `nil` 或零值的情况。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{with .Name}}
Hello, {{.}}!
{{else}}
Name not provided.
{{end}}
`

	data := struct {
		Name *string
	}{nil}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Name not provided.
```

### 49. 嵌入文本而不解释

有时你可能需要在模板中包含文本，而不希望它被解释。可以使用反引号来实现。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{`
This text will not be interpreted.
`}}
`

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, nil)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
This text will not be interpreted.
```

### 50. 使用 printf 进行数值格式化

你可以使用 `printf` 函数来格式化数值。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `{{printf "%.2f" .Price}}`

	data := struct {
		Price float64
	}{123.456789}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
123.46
```

### 51. 使用切片作为参数

模板函数可以接受切片作为参数。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	funcs := template.FuncMap{
		"join": func(sep string, s []string) string {
			result := ""
			for i, str := range s {
				result += str
				if i != len(s)-1 {
					result += sep
				}
			}
			return result
		},
	}

	tmpl := `{{join ", " .Names}}`

	data := struct {
		Names []string
	}{[]string{"Alice", "Bob", "Charlie"}}

	t, err := template.New("test").Funcs(funcs).Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Alice, Bob, Charlie
```

### 52. 使用自定义类型

你可以在模板中处理自定义类型，只需确保为它们提供正确的方法或字段。

```go
package main

import (
	"text/template"
	"os"
)

type Person struct {
	Name string
	Age  int
}

func (p Person) DisplayName() string {
	return "Mr./Ms. " + p.Name
}

func main() {
	tmpl := `Hello, {{.DisplayName}} who is {{.Age}} years old.`

	person := Person{"Alice", 30}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, person)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Hello, Mr./Ms. Alice who is 30 years old.
```

### 53. 使用命名模板

有时，为模板命名会更容易处理，特别是当你想重用某些模板片段时。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{define "name"}}Alice{{end}}
Hello, {{template "name"}}!
`

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, nil)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Hello, Alice!
```

### 54. 使用外部文件作为模板

你可以从外部文件加载模板。这在实际应用中非常有用，因为模板通常存储在单独的文件中。

```go
// 假设你有一个名为 "template.txt" 的文件，内容为:
// Hello, {{.Name}}!

package main

import (
	"text/template"
	"os"
)

func main() {
	data := struct {
		Name string
	}{"Alice"}

	t, err := template.ParseFiles("template.txt")
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Hello, Alice!
```

### 55. 使用 and 和 or 逻辑操作

你可以在模板中使用逻辑操作 `and` 和 `or`。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{if and .HasPermission .IsAdmin}}You have admin access.{{else}}You don't have admin access.{{end}}
`

	data := struct {
		HasPermission bool
		IsAdmin       bool
	}{true, true}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
You have admin access.
```

### 56. 处理默认值

有时你可能想为某些可能缺失的数据设置默认值。可以使用自定义函数或结合现有函数来实现。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	funcs := template.FuncMap{
		"default": func(defaultVal, val interface{}) interface{} {
			if val == nil {
				return defaultVal
			}
			return val
		},
	}

	tmpl := `Hello, {{.Name | default "Guest"}}!`

	data := struct {
		Name *string
	}{nil}

	t, err := template.New("test").Funcs(funcs).Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Hello, Guest!
```

### 57. 多模板合并

你可以合并多个模板，这在分块和组织复杂的模板时非常有用。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl1 := `{{define "T1"}}One{{end}}`
	tmpl2 := `{{define "T2"}}Two{{end}}`
	tmpl3 := `{{template "T1"}} {{template "T2"}}`

	t := template.New("test")
	t, err := t.Parse(tmpl1)
	if err != nil {
		panic(err)
	}
	t, err = t.Parse(tmpl2)
	if err != nil {
		panic(err)
	}
	t, err = t.Parse(tmpl3)
	if err != nil {
		panic(err)
	}

	err = t.Execute(os.Stdout, nil)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
One Two
```

### 58. 使用 slice 创建切片

你可以在模板中使用 `slice` 函数来创建一个切片。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `{{range $index, $element := slice "apple" "banana" "cherry"}}{{$index}}: {{$element}}{{end}}`

	funcs := template.FuncMap{
		"slice": func(items ...interface{}) []interface{} {
			return items
		},
	}

	t, err := template.New("test").Funcs(funcs).Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, nil)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
0: apple1: banana2: cherry
```

### 59. 使用 eq 进行多重比较

你可以使用 `eq` 函数来进行多个值的比较。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{if eq .Color "red" "blue"}}Matched{{else}}Not Matched{{end}}
`

	data := struct {
		Color string
	}{"blue"}

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Matched
```











