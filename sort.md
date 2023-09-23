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

### 8. 对复数切片进行排序
`Go` 的 `sort` 包没有为复数提供直接的排序功能，但你可以很容易地自己实现。例如，你可以基于复数的模值来进行排序：

```go
package main

import (
	"fmt"
	"sort"
	"math/cmplx"
)

type ComplexSlice []complex128

func (c ComplexSlice) Len() int           { return len(c) }
func (c ComplexSlice) Swap(i, j int)      { c[i], c[j] = c[j], c[i] }
func (c ComplexSlice) Less(i, j int) bool { return cmplx.Abs(c[i]) < cmplx.Abs(c[j]) }

func main() {
	nums := []complex128{3 + 4i, 1 + 2i, 2 + 2i}
	sort.Sort(ComplexSlice(nums))
	fmt.Println(nums) // [1+2i 2+2i 3+4i]
}
```

### 9. 使用 sort.IsSorted
除了之前提到的专门为 `int`, `float64` 和 `string` 切片设计的排序检查函数，`sort` 包还提供了一个更一般的函数 `IsSorted`，它可以接受任何满足 `sort.Interface` 的类型：

```go
nums := []complex128{1 + 2i, 2 + 2i, 3 + 4i}
fmt.Println(sort.IsSorted(ComplexSlice(nums))) // true
```

### 10. 对关联数组 (map) 进行排序
`Go` 的 `map` 是无序的，但有时你可能希望按键或值排序。虽然 `sort` 包不直接支持 `map`，但你可以通过转换为切片来达到目的：

```go
package main

import (
	"fmt"
	"sort"
)

func sortMapByKeys(m map[string]int) []string {
	keys := make([]string, 0, len(m))
	for k := range m {
		keys = append(keys, k)
	}
	sort.Strings(keys)
	return keys
}

func main() {
	m := map[string]int{
		"apple":  5,
		"banana": 2,
		"cherry": 8,
	}

	sortedKeys := sortMapByKeys(m)
	for _, k := range sortedKeys {
		fmt.Printf("%s: %d\n", k, m[k])
	}
}
```
上面的代码将按照键的字母顺序输出 map 的内容。

### 11. 基于多个切片排序
假设你有多个切片，并希望基于其中一个切片的顺序来重新排列其他切片的元素。例如，你有一个切片代表名字和另一个切片代表年龄，你想基于名字的排序来重新排列年龄。你可以这么做：

```go
package main

import (
	"fmt"
	"sort"
)

type MultiSorter struct {
	names []string
	ages  []int
}

func (ms *MultiSorter) Len() int           { return len(ms.names) }
func (ms *MultiSorter) Swap(i, j int)      { ms.names[i], ms.names[j] = ms.names[j], ms.names[i]; ms.ages[i], ms.ages[j] = ms.ages[j], ms.ages[i] }
func (ms *MultiSorter) Less(i, j int) bool { return ms.names[i] < ms.names[j] }

func main() {
	names := []string{"Zoe", "John", "Alfred"}
	ages := []int{25, 30, 20}

	ms := &MultiSorter{names: names, ages: ages}
	sort.Sort(ms)

	fmt.Println(names) // ["Alfred", "John", "Zoe"]
	fmt.Println(ages)  // [20, 30, 25]
}
```

### 12. 自定义排序函数
有时候，你可能只想快速实现一个自定义的排序逻辑，而不是定义一个新的类型。你可以使用 `sort.SliceStable`，它类似于 `sort.Slice`，但它保证相等的元素的原始顺序不会改变：

```go
people := []Person{
	{"John", 25},
	{"Bob", 25},
	{"Michael", 17},
	{"Jenny", 26},
}

sort.SliceStable(people, func(i, j int) bool {
	if people[i].Age == people[j].Age {
		return people[i].Name < people[j].Name
	}
	return people[i].Age < people[j].Age
})

fmt.Println(people)
```
这个例子首先按年龄排序，如果年龄相同，则按名字的字母顺序排序。

### 13. 查找与给定值相等或最接近的元素
`sort.Search` 可以帮助你查找满足某条件的第一个元素。这在查找与给定值相等或最接近的元素时很有用：

```go
nums := []int{1, 2, 4, 5, 7, 8, 10}
target := 6

index := sort.Search(len(nums), func(i int) bool {
	return nums[i] >= target
})

if index < len(nums) {
	if nums[index] == target {
		fmt.Println("Found:", target)
	} else {
		fmt.Println("Closest:", nums[index])
	}
}
```

