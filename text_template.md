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















