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

### 60. 使用条件操作符

`text/template` 库没有内置的三元操作符，但你可以通过嵌套 `if` 语句来模拟这种行为。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{if .IsPremium}}Premium{{else}}Free{{end}} User
`

	data := struct {
		IsPremium bool
	}{true}

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
Premium User
```

### 61. 避免 HTML 转义

默认情况下，模板会转义 HTML 字符。如果你希望防止这种转义，可以使用 `template.HTML` 类型。

```go
package main

import (
	"html/template"
	"os"
)

func main() {
	tmpl := `Message: {{.Message}}`

	data := struct {
		Message template.HTML
	}{"<strong>Hello</strong>"}

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
Message: <strong>Hello</strong>
```

### 62. 自定义分隔符

如果默认的 `{{` 和 `}}` 分隔符与你的数据冲突，你可以使用 `Delims` 方法更改它们。

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

	data := struct {
		Name string
		Age  int
	}{"Alice", 30}

	t, err := template.New("test").Delims("[[", "]]").Parse(tmpl)
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
Alice is 30 years old.
```

### 63. 嵌入和使用自定义函数

你可以在模板中定义和使用自定义函数。

```go
package main

import (
	"text/template"
	"os"
	"strings"
)

func main() {
	funcs := template.FuncMap{
		"capitalize": strings.Title,
	}

	tmpl := `{{. | capitalize}}`

	t, err := template.New("test").Funcs(funcs).Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, "hello world")
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Hello World
```

### 64. 使用 template 嵌套模板

你可以在模板中嵌套其他模板，这在组织和重用模板片段时非常有用。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{define "list"}}
	<ul>
	{{range .}}
		<li>{{.}}</li>
	{{end}}
	</ul>
{{end}}

Items:
{{template "list" .Items}}
`

	data := struct {
		Items []string
	}{[]string{"Apple", "Banana", "Cherry"}}

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
Items:
	<ul>
		<li>Apple</li>
		<li>Banana</li>
		<li>Cherry</li>
	</ul>
```

### 65. 使用 must 简化错误处理

`template.Must` 是一个辅助函数，它简化了模板解析时的错误处理。如果模板解析出错，它会引发 `panic`。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmplStr := `Hello, {{.Name}}!`
	t := template.Must(template.New("test").Parse(tmplStr))

	data := struct {
		Name string
	}{"Alice"}

	err := t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Hello, Alice!
```

### 66. 使用 ParseGlob 加载多个模板文件

如果你有多个模板文件，可以使用 `ParseGlob` 函数一次性加载它们。

```go
// 假设你有两个文件: tmpl1.txt 和 tmpl2.txt
// tmpl1.txt 的内容为: "Template 1: Hello, {{.}}!"
// tmpl2.txt 的内容为: "Template 2: Hi, {{.}}!"

package main

import (
	"text/template"
	"os"
)

func main() {
	t := template.Must(template.New("test").ParseGlob("*.txt"))

	err := t.ExecuteTemplate(os.Stdout, "tmpl1.txt", "World")
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Template 1: Hello, World!
```

### 67. 使用 init 在模板中设置默认函数

你可以使用 `init` 函数在包级别预先为模板设置函数。

```go
package main

import (
	"text/template"
	"os"
	"strings"
)

var funcMap = template.FuncMap{
	"upper": strings.ToUpper,
}

func init() {
	template.New("test").Funcs(funcMap)
}

func main() {
	tmpl := `{{. | upper}}`

	t, err := template.New("test").Parse(tmpl)
	if err != nil {
		panic(err)
	}

	err = t.Execute(os.Stdout, "hello")
	if err != nil {
		panic(err)
	}
}
```

输出：

```
HELLO
```

### 68. 使用 block 定义模板块

`block` 是一个定义模板块的动作。它定义了可以用于其他模板的模板块。如果没有定义替代模板，`block` 的默认模板将被执行。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{define "list"}}
<ul>
	{{range .}}
	<li>{{.}}</li>
	{{end}}
</ul>
{{end}}

{{block "list" .Items}}
<ul>
	{{range .}}
	<li><strong>{{.}}</strong></li>
	{{end}}
</ul>
{{end}}
`

	data := struct {
		Items []string
	}{[]string{"Apple", "Banana", "Cherry"}}

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
<ul>
	<li><strong>Apple</strong></li>
	<li><strong>Banana</strong></li>
	<li><strong>Cherry</strong></li>
</ul>
```

### 69. 使用 clone 复制模板

你可以使用 `Clone` 方法复制一个模板，这在多个模板共享同一组函数或设置时非常有用。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmplStr := `Hello, {{.Name}}!`

	// 创建一个模板并解析
	t := template.New("test")
	template.Must(t.Parse(tmplStr))

	// 克隆模板
	tClone, err := t.Clone()
	if err != nil {
		panic(err)
	}

	data := struct {
		Name string
	}{"Alice"}

	err = tClone.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Hello, Alice!
```

### 70. 使用 nest 嵌套模板定义

使用 `nest`，你可以在一个模板定义中嵌套另一个模板定义。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{define "T1"}}ONE{{end}}

{{define "T2"}}
{{template "T1"}}
TWO
{{end}}

{{template "T2"}}
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
ONE
TWO
```

### 71. 使用 text/template/parse 解析模板

除了常规的模板处理外，你还可以直接使用 `text/template/parse` 包来手动解析模板。

```go
package main

import (
	"text/template/parse"
	"fmt"
)

func main() {
	tmplStr := `Hello, {{.Name}}!`
	tree, err := parse.Parse("test", tmplStr, "{{", "}}")
	if err != nil {
		panic(err)
	}

	// 输出解析后的模板树
	fmt.Println(tree.Root.String())
}
```

输出（解析后的模板树）：

```
"Hello, {{.Name}}!"
```

### 72. 使用 - 控制空白

在模板中，`{{-` 和 `-}}` 语法允许你控制生成的输出中的空白。`{{-` 会消除其左侧的所有空白，而 `-}}` 会消除其右侧的所有空白。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{- "Hello," -}} 
{{- " World!" -}}
`

	err := template.Must(template.New("test").Parse(tmpl)).Execute(os.Stdout, nil)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Hello, World!
```

### 73. 递归模板

模板可以递归地引用自己，这在处理嵌套结构如树时非常有用。

```go
package main

import (
	"text/template"
	"os"
)

type Node struct {
	Value    string
	Children []*Node
}

func main() {
	tmpl := `
{{define "node"}}
	{{.Value}}
	{{if .Children}}
		{{range .Children}}
		{{template "node" .}}
		{{end}}
	{{end}}
{{end}}

{{template "node" .}}
`

	root := &Node{
		Value: "Root",
		Children: []*Node{
			{Value: "Child1"},
			{
				Value: "Child2",
				Children: []*Node{
					{Value: "Grandchild1"},
					{Value: "Grandchild2"},
				},
			},
		},
	}

	err := template.Must(template.New("test").Parse(tmpl)).Execute(os.Stdout, root)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Root
Child1
Child2
Grandchild1
Grandchild2
```

### 74. 使用结构体方法作为模板函数

你可以在模板中直接调用数据结构的方法。

```go
package main

import (
	"text/template"
	"os"
)

type Person struct {
	FirstName string
	LastName  string
}

func (p Person) FullName() string {
	return p.FirstName + " " + p.LastName
}

func main() {
	tmpl := `Full name: {{.FullName}}`

	person := Person{"Alice", "Johnson"}

	err := template.Must(template.New("test").Parse(tmpl)).Execute(os.Stdout, person)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Full name: Alice Johnson
```

### 75. 使用模板注释

`{{/* */}}` 允许你在模板中添加注释，这些注释在执行模板时不会被包含在输出中。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{/* This is a comment and will not appear in the output. */}}
Hello, {{.Name}}!
`

	data := struct {
		Name string
	}{"Alice"}

	err := template.Must(template.New("test").Parse(tmpl)).Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Hello, Alice!
```

### 76. 使用 len 函数获取长度

你可以使用内置的 `len` 函数来获取字符串、数组、切片或映射的长度。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `Number of items: {{len .Items}}`

	data := struct {
		Items []string
	}{[]string{"apple", "banana", "cherry"}}

	err := template.Must(template.New("test").Parse(tmpl)).Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Number of items: 3
```

### 77. 使用 printf 进行字符串格式化

虽然我们已经简要介绍了 `printf`，但你可以使用它进行更复杂的字符串格式化。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `{{printf "%s has %d apples." .Name .Count}}`

	data := struct {
		Name  string
		Count int
	}{"Alice", 5}

	err := template.Must(template.New("test").Parse(tmpl)).Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Alice has 5 apples.
```

### 78. 使用 index 访问数组、切片或映射

`index` 函数可以用于访问数组、切片或映射的元素。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `Second item: {{index .Items 1}}`

	data := struct {
		Items []string
	}{[]string{"apple", "banana", "cherry"}}

	err := template.Must(template.New("test").Parse(tmpl)).Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Second item: banana
```

### 79. 使用 call 调用方法或函数

你可以使用 `call` 功能来调用方法或函数，并传递参数。

```go
package main

import (
	"text/template"
	"os"
)

type MathOps struct{}

func (MathOps) Add(a, b int) int {
	return a + b
}

func main() {
	tmpl := `Result: {{call .Add 5 3}}`

	math := MathOps{}

	err := template.Must(template.New("test").Parse(tmpl)).Execute(os.Stdout, math)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Result: 8
```

### 80. 使用模板集

你可以创建一个模板集，其中包含多个模板，这些模板可以相互引用。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	templates := `
{{define "A"}}Template A {{template "B"}}{{end}}
{{define "B"}}Template B{{end}}
`

	tmpl, err := template.New("set").Parse(templates)
	if err != nil {
		panic(err)
	}

	err = tmpl.ExecuteTemplate(os.Stdout, "A", nil)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Template A Template B
```

### 81. 使用 else if 构造

在模板中，你可以使用 `else if` 来构造更复杂的条件逻辑。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{if eq .Color "red"}}
Red
{{else if eq .Color "blue"}}
Blue
{{else}}
Unknown
{{end}}
`

	data := struct {
		Color string
	}{"blue"}

	err := template.Must(template.New("test").Parse(tmpl)).Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Blue
```

### 82. 使用 with 构造

`with` 动作可以更改当前点的上下文。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{with .Address}}
City: {{.City}}, Country: {{.Country}}
{{end}}
`

	data := struct {
		Name    string
		Address struct {
			City    string
			Country string
		}
	}{"Alice", struct {
		City    string
		Country string
	}{"New York", "USA"}}

	err := template.Must(template.New("test").Parse(tmpl)).Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
City: New York, Country: USA
```

### 83. 使用 `$` 访问顶级上下文

在嵌套的模板操作中，你可以使用 `$` 符号来访问最顶级的上下文。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{range .Items}}
{{with .SubItems}}
Item: {{$.Name}}, SubItem: {{.}}
{{end}}
{{end}}
`

	data := struct {
		Items []struct {
			Name     string
			SubItems []string
		}
	}{
		{
			Name:     "Item1",
			SubItems: []string{"Sub1", "Sub2"},
		},
	}

	err := template.Must(template.New("test").Parse(tmpl)).Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Item: Item1, SubItem: Sub1
Item: Item1, SubItem: Sub2
```

### 84. 处理空值

模板中的 `with` 动作也可以用于处理可能为空的字段。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{with .Description}}
Description: {{.}}
{{else}}
No description provided.
{{end}}
`

	data := struct {
		Name        string
		Description *string
	}{"Item1", nil}

	err := template.Must(template.New("test").Parse(tmpl)).Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
No description provided.
```

### 85. 使用自定义函数处理错误

你可以定义自定义函数来处理可能的错误，并在模板中使用它。

```go
package main

import (
	"text/template"
	"os"
	"errors"
)

func safeDivide(a, b float64) (float64, error) {
	if b == 0 {
		return 0, errors.New("division by zero")
	}
	return a / b, nil
}

func main() {
	funcs := template.FuncMap{
		"divide": safeDivide,
	}

	tmpl := `{{.a}} divided by {{.b}} is {{divide .a .b}}`

	data := struct {
		a float64
		b float64
	}{10, 0}

	t, err := template.New("test").Funcs(funcs).Parse(tmpl)
	if err != nil {
		panic(err)
	}
	err = t.Execute(os.Stdout, data)
	if err != nil {
		// 在此处处理模板执行时的错误
		panic(err)
	}
}
```

由于除以零的操作，上述代码会引发一个 panic。

### 86. 使用 ParseFiles 和 ParseGlob

除了使用字符串定义模板，你还可以直接从文件或匹配的文件集加载模板。

```go
// 假设你有一个名为 "template.txt" 的文件，内容为："Hello, {{.Name}}!"

package main

import (
	"text/template"
	"os"
)

func main() {
	t, err := template.ParseFiles("template.txt")
	if err != nil {
		panic(err)
	}

	data := struct {
		Name string
	}{"Alice"}

	err = t.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

### 87. 将模板结果写入不同的输出

默认情况下，模板的执行结果会写入 `os.Stdout`，但你也可以写入任何实现了 `io.Writer` 接口的对象。

```go
package main

import (
	"text/template"
	"bytes"
	"fmt"
)

func main() {
	tmpl := `Hello, {{.Name}}!`

	data := struct {
		Name string
	}{"Bob"}

	var buf bytes.Buffer

	t := template.Must(template.New("test").Parse(tmpl))
	err := t.Execute(&buf, data)
	if err != nil {
		panic(err)
	}

	// 从缓冲区获取结果
	result := buf.String()
	fmt.Println(result)
}
```

### 88. 处理日期和时间

你可以在模板中处理日期和时间。

```go
package main

import (
	"text/template"
	"os"
	"time"
)

func main() {
	funcs := template.FuncMap{
		"formatDate": func(t time.Time) string {
			return t.Format("2006-01-02")
		},
	}

	tmpl := `Date: {{formatDate .Date}}`

	data := struct {
		Date time.Time
	}{time.Now()}

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
Date: 2023-08-14
```

### 89. 使用 and 和 or 运算符

模板中可以使用 `and` 和 `or` 运算符来进行布尔逻辑操作。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{if and .HasBooks .HasPen}}You have both books and a pen.{{end}}
{{if or .HasBooks .HasPen}}You have either books or a pen or both.{{end}}
`

	data := struct {
		HasBooks bool
		HasPen   bool
	}{true, false}

	err := template.Must(template.New("test").Parse(tmpl)).Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
You have either books or a pen or both.
```

### 90. 使用 not 运算符

你可以使用 `not` 运算符来反转布尔值。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{if not .HasBooks}}You don't have books.{{end}}
`

	data := struct {
		HasBooks bool
	}{false}

	err := template.Must(template.New("test").Parse(tmpl)).Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
You don't have books.
```

### 91. 使用 eq, ne, lt, le, gt, ge 运算符

这些运算符分别代表等于、不等于、小于、小于或等于、大于和大于或等于。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{if eq .Age 30}}You are 30 years old.{{end}}
{{if lt .Age 30}}You are younger than 30.{{end}}
`

	data := struct {
		Age int
	}{28}

	err := template.Must(template.New("test").Parse(tmpl)).Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
You are younger than 30.
```

### 92. 使用 slice 函数获取切片或字符串的子集

`slice` 函数允许你获取切片或字符串的子集。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
Subslice: {{slice . 1 3}}
`

	data := []int{0, 1, 2, 3, 4}

	err := template.Must(template.New("test").Parse(tmpl)).Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Subslice: [1 2]
```

### 93. 使用自定义分隔符与管道结合

你可以使用自定义的分隔符，并与管道结合使用。

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

	tmpl := `
[[.Name | upper]]
`

	data := struct {
		Name string
	}{"alice"}

	t, err := template.New("test").Funcs(funcs).Delims("[[", "]]").Parse(tmpl)
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
ALICE
```

### 94. 使用 {{template}} 动作引用其他模板

你可以使用 `{{template}}` 动作在一个模板中引用另一个模板。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{define "T1"}}Hello, {{.}}!{{end}}
{{template "T1" .Name}}
`

	data := struct {
		Name string
	}{"Alice"}

	err := template.Must(template.New("test").Parse(tmpl)).Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Hello, Alice!
```

### 95. 使用 {{range}} 遍历映射

之前，我们已经展示了如何使用 `{{range}}` 遍历切片。现在，我们来看一下如何遍历映射。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{range $key, $value := .Items}}
Key: {{$key}}, Value: {{$value}}
{{end}}
`

	data := struct {
		Items map[string]string
	}{
		Items: map[string]string{
			"A": "Apple",
			"B": "Banana",
		},
	}

	err := template.Must(template.New("test").Parse(tmpl)).Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Key: A, Value: Apple
Key: B, Value: Banana
```

### 96. 在管道中使用 if-else

你可以在管道中使用条件逻辑。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{if .HasBooks}}
You have {{.Books | len}} books.
{{else}}
You don't have any books.
{{end}}
`

	data := struct {
		HasBooks bool
		Books    []string
	}{true, []string{"Book1", "Book2"}}

	err := template.Must(template.New("test").Parse(tmpl)).Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
You have 2 books.
```

### 97. 在模板中使用 variable

你可以在模板中定义和使用变量。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{ $x := "Hello" }}
{{ $y := "World" }}
{{ $x }} {{ $y }}
`

	err := template.Must(template.New("test").Parse(tmpl)).Execute(os.Stdout, nil)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Hello World
```

### 98. 使用 . 访问当前值

在模板的动作中，`.` 表示当前值。例如，在 `range` 循环中，`.` 将代表当前的迭代项。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{range .}}
Value: {{.}}
{{end}}
`

	data := []string{"A", "B", "C"}

	err := template.Must(template.New("test").Parse(tmpl)).Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Value: A
Value: B
Value: C
```

### 99. 使用 template 包中的 HTMLEscape

`HTMLEscape` 函数可以帮助你转义 `HTML` 字符串，确保生成的内容在 Web 页面上正确显示。

```go
package main

import (
	"text/template"
	"os"
	"html"
)

func main() {
	funcs := template.FuncMap{
		"escape": html.EscapeString,
	}

	tmpl := `Escaped: {{escape .Content}}`

	data := struct {
		Content string
	}{"<b>Hello</b>"}

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
Escaped: &lt;b&gt;Hello&lt;/b&gt;
```

### 100. 使用 template 包中的 JSEscape

与 `HTMLEscape` 类似，`JSEscape` 可以帮助你转义 `JavaScript` 字符串。

```go
package main

import (
	"text/template"
	"os"
	"html/template"
)

func main() {
	tmpl := `Escaped: {{.Content | js}}`

	data := struct {
		Content string
	}{"alert('Hello');"}

	t, err := template.New("test").Funcs(template.FuncMap{
		"js": template.JSEscapeString,
	}).Parse(tmpl)
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
Escaped: alert(\x27Hello\x27);
```

### 101. 使用 Must 简化错误处理

`template.Must` 是一个便捷函数，它可以简化模板解析中的错误处理。如果模板解析失败，`Must` 会产生 `panic`。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmplStr := "Hello, {{.Name}}!"
	tmpl := template.Must(template.New("test").Parse(tmplStr))

	data := struct {
		Name string
	}{"Alice"}

	err := tmpl.Execute(os.Stdout, data)
	if err != nil {
		panic(err)
	}
}
```

输出：

```
Hello, Alice!
```

### 102. 使用 associate 关联数据

`associate` 允许你将模板与特定的数据关联，这在嵌套模板中非常有用。

```go
package main

import (
	"text/template"
	"os"
)

func main() {
	tmpl := `
{{define "A"}}{{.}}{{end}}
{{define "B"}}{{with "World"}}{{template "A" .}}{{end}}{{end}}
{{template "B" .}}
`

	data := "Hello"

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
World
```

Done.
