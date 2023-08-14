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








