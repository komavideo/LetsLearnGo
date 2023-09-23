使用sort库
==========

Go 语言中的 sort 包提供了对切片和用户定义的集合的排序操作。

https://pkg.go.dev/sort

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

## Go 语言 sort 库

### 1. 基本排序
对基础数据类型（如 int, float64 和 string）的切片进行排序。

```go
package main

import (
	"fmt"
	"sort"
)

func main() {
	ints := []int{3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5}
	sort.Ints(ints)
	fmt.Println(ints) // [1 1 2 3 3 4 5 5 5 6 9]

	strs := []string{"c", "a", "b"}
	sort.Strings(strs)
	fmt.Println(strs) // [a b c]
}
```

### 2. 自定义排序
如果你有一个自定义的数据类型，例如一个结构体，并且想按照某种特定的顺序进行排序，你可以这么做：

```go
package main

import (
	"fmt"
	"sort"
)

type Person struct {
	Name string
	Age  int
}

type ByAge []Person

func (a ByAge) Len() int           { return len(a) }
func (a ByAge) Swap(i, j int)      { a[i], a[j] = a[j], a[i] }
func (a ByAge) Less(i, j int) bool { return a[i].Age < a[j].Age }

func main() {
	people := []Person{
		{"Bob", 31},
		{"John", 25},
		{"Michael", 17},
		{"Jenny", 26},
	}

	sort.Sort(ByAge(people))
	fmt.Println(people)
}
```
在这个例子中，我们定义了一个 `Person` 结构体，并通过创建一个满足 `sort.Interface` 接口的新类型 `ByAge` 来按照年龄进行排序。

### 3. 检查是否已排序
你可以使用 `sort` 包提供的函数来检查一个切片是否已经被排序：

```go
ints := []int{1, 2, 3, 4, 5}
if sort.IntsAreSorted(ints) {
    fmt.Println("The slice is sorted!")
}
```

### 4. 倒序排序
使用 `sort.Reverse` 可以实现倒序排序。它返回一个满足 `sort.Interface` 的接口，使得原来的 `Less` 方法的意义被颠倒：

```go
ints := []int{3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5}
sort.Sort(sort.Reverse(sort.IntSlice(ints)))
fmt.Println(ints) // [9 6 5 5 5 4 3 3 2 1 1]
```

### 5. 对结构体进行多字段排序
如果想基于多个字段对结构体进行排序，可以这样实现：

```go
type Person struct {
	Name string
	Age  int
}

type ByNameThenAge []Person

func (a ByNameThenAge) Len() int      { return len(a) }
func (a ByNameThenAge) Swap(i, j int) { a[i], a[j] = a[j], a[i] }
func (a ByNameThenAge) Less(i, j int) bool {
	if a[i].Name == a[j].Name {
		return a[i].Age < a[j].Age
	}
	return a[i].Name < a[j].Name
}
```

### 6. 使用 sort.Slice
如果不想为你的类型定义一个新的排序方法，你可以使用 `sort.Slice`，它需要一个切片和一个返回 `bool` 的函数。这个函数会为切片中的两个元素提供索引，并返回第一个元素是否应该在第二个之前：

```go
people := []Person{
	{"Bob", 31},
	{"John", 25},
	{"Michael", 17},
	{"Jenny", 26},
}

sort.Slice(people, func(i, j int) bool {
	return people[i].Age < people[j].Age
})

fmt.Println(people)
```

### 7. 查找元素
`sort` 包还提供了在已排序的切片中查找元素的函数，例如 `sort.SearchInts`、`sort.SearchFloat64s` 和 `sort.SearchStrings`：

```go
ints := []int{1, 2, 3, 4, 5}
index := sort.SearchInts(ints, 3)

if index < len(ints) && ints[index] == 3 {
	fmt.Println("Found 3 at index:", index)
} else {
	fmt.Println("Did not find 3")
}
```

### 

```go
```
### 

```go
```
### 

```go
```
