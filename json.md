处理json数据
===========

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

## Go 语言操作 JSON 的例子

### 1. 将结构体编码为 JSON

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Person struct {
	Name string
	Age  int
}

func main() {
	p := Person{Name: "John", Age: 30}
	jsonData, err := json.Marshal(p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(string(jsonData))
}
```

### 2. 将 JSON 解码为结构体

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Person struct {
	Name string
	Age  int
}

func main() {
	jsonData := `{"Name":"John","Age":30}`
	var p Person
	err := json.Unmarshal([]byte(jsonData), &p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(p.Name, p.Age)
}
```

### 3. 使用 json.NewEncoder 编码为 JSON

```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
)

type Person struct {
	Name string
	Age  int
}

func main() {
	p := Person{Name: "John", Age: 30}
	encoder := json.NewEncoder(os.Stdout)
	err := encoder.Encode(p)
	if err != nil {
		fmt.Println(err)
	}
}
```

### 4. 使用 json.NewDecoder 从 JSON 解码

```go
package main

import (
	"encoding/json"
	"fmt"
	"strings"
)

type Person struct {
	Name string
	Age  int
}

func main() {
	jsonData := `{"Name":"John","Age":30}`
	r := strings.NewReader(jsonData)
	decoder := json.NewDecoder(r)
	var p Person
	err := decoder.Decode(&p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(p.Name, p.Age)
}
```

### 5. 操作嵌套 JSON 对象

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Address struct {
	City  string
	State string
}

type Person struct {
	Name    string
	Age     int
	Address Address
}

func main() {
	jsonData := `{"Name":"John","Age":30,"Address":{"City":"New York","State":"NY"}}`
	var p Person
	err := json.Unmarshal([]byte(jsonData), &p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(p.Name, p.Age, p.Address.City, p.Address.State)
}
```

### 6. 使用 `json.RawMessage` 处理灵活的 JSON 结构

有时，您可能想在不完全解码的情况下操作某些 JSON 字段。`json.RawMessage` 可以帮助您做到这一点。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Person struct {
	Name string
	Age  int
	Data json.RawMessage
}

func main() {
	jsonData := `{"Name":"John","Age":30,"Data":{"City":"New York"}}`
	var p Person
	err := json.Unmarshal([]byte(jsonData), &p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(string(p.Data)) // 输出: {"City":"New York"}
}
```

### 7. 将 JSON 解码到 Map 中

如果您不知道 JSON 结构，可以使用 map 解码它。

```go
package main

import (
	"encoding/json"
	"fmt"
)

func main() {
	jsonData := `{"Name":"John","Age":30,"Address":{"City":"New York","State":"NY"}}`
	var result map[string]interface{}
	err := json.Unmarshal([]byte(jsonData), &result)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(result["Name"], result["Age"]) // 输出: John 30
}
```

### 8. 使用 `json:",omitempty"` 标记忽略空值

如果您想在编码 JSON 时忽略结构体中的空值，可以使用 `omitempty` 标记。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Person struct {
	Name string `json:"name,omitempty"`
	Age  int    `json:"age,omitempty"`
}

func main() {
	p := Person{Name: "John"}
	jsonData, err := json.Marshal(p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(string(jsonData)) // 输出: {"name":"John"}
}
```

### 9. 使用 `json:"-"` 标记忽略字段

如果您想在 JSON 编码和解码时完全忽略某个字段，可以使用 `-` 标记。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Person struct {
	Name string
	Age  int `json:"-"`
}

func main() {
	p := Person{Name: "John", Age: 30}
	jsonData, err := json.Marshal(p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(string(jsonData)) // 输出: {"Name":"John"}
}
```

### 10. 使用自定义标签名

您可以通过在结构体字段旁边的标签中指定自定义名称来自定义 JSON 键的名称。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Person struct {
	Name string `json:"full_name"`
	Age  int    `json:"age_in_years"`
}

func main() {
	p := Person{Name: "John", Age: 30}
	jsonData, err := json.Marshal(p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(string(jsonData)) // 输出: {"full_name":"John","age_in_years":30}
}
```

### 11. 使用 `interface{}` 处理未知结构的 JSON

当您不知道 JSON 的确切结构时，可以使用空接口 `interface{}` 来解码。

```go
package main

import (
	"encoding/json"
	"fmt"
)

func main() {
	jsonData := `{"Name":"John","Age":30,"Address":{"City":"New York"}}`
	var result interface{}
	err := json.Unmarshal([]byte(jsonData), &result)
	if err != nil {
		fmt.Println(err)
		return
	}
	m := result.(map[string]interface{})
	fmt.Println(m["Name"], m["Age"]) // 输出: John 30
}
```

### 12. 使用 `json.NewEncoder` 将 JSON 写入文件

您可以使用 `json.NewEncoder` 将 JSON 编码并写入文件。

```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
)

type Person struct {
	Name string
	Age  int
}

func main() {
	p := Person{Name: "John", Age: 30}
	file, err := os.Create("data.json")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer file.Close()

	encoder := json.NewEncoder(file)
	err = encoder.Encode(p)
	if err != nil {
		fmt.Println(err)
	}
}
```

### 13. 使用 `json.NewDecoder` 从文件读取 JSON

您可以使用 `json.NewDecoder` 从文件中解码 JSON。

```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
)

type Person struct {
	Name string
	Age  int
}

func main() {
	file, err := os.Open("data.json")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer file.Close()

	decoder := json.NewDecoder(file)
	var p Person
	err = decoder.Decode(&p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(p.Name, p.Age) // 输出: John 30
}
```

### 14. 使用自定义 JSON 编码和解码

您可以通过实现 `json.Marshaler` 和 `json.Unmarshaler` 接口来自定义 JSON 的编码和解码。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Person struct {
	Name string
	Age  int
}

func (p Person) MarshalJSON() ([]byte, error) {
	return []byte(fmt.Sprintf(`{"full_name": "%s", "age_in_years": %d}`, p.Name, p.Age)), nil
}

func (p *Person) UnmarshalJSON(data []byte) error {
	var v map[string]interface{}
	if err := json.Unmarshal(data, &v); err != nil {
		return err
	}
	p.Name = v["full_name"].(string)
	p.Age = int(v["age_in_years"].(float64))
	return nil
}

func main() {
	p := Person{Name: "John", Age: 30}
	jsonData, _ := json.Marshal(p)
	fmt.Println(string(jsonData)) // 输出: {"full_name": "John", "age_in_years": 30}
}
```

### 15. 在数组中编码和解码 JSON

您可以将多个结构体编码为 JSON 数组，或从 JSON 数组中解码为结构体切片。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Person struct {
	Name string
	Age  int
}

func main() {
	people := []Person{{"John", 30}, {"Alice", 25}}
	jsonData, err := json.Marshal(people)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(string(jsonData)) // 输出: [{"Name":"John","Age":30},{"Name":"Alice","Age":25}]
}
```

### 16. 处理混合类型的 JSON

有时，JSON 中的某些字段可能有多种可能的类型。您可以使用空接口和类型断言来处理这种情况。

```go
package main

import (
	"encoding/json"
	"fmt"
)

func main() {
	jsonData := `{"name":"John","age":30,"contact":{"phone":"1234567890"}}`
	var result map[string]interface{}
	err := json.Unmarshal([]byte(jsonData), &result)
	if err != nil {
		fmt.Println(err)
		return
	}

	contact := result["contact"].(map[string]interface{})
	phone := contact["phone"].(string)
	fmt.Println(phone) // 输出: 1234567890
}
```

### 17. 使用 `json:",string"` 标记将数字编码为字符串

有时，您可能希望将数字编码为 JSON 字符串而不是数字。可以使用 `string` 标记实现。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Product struct {
	ID    int    `json:"id,string"`
	Name  string `json:"name"`
	Price float64
}

func main() {
	p := Product{ID: 1, Name: "Widget", Price: 9.99}
	jsonData, err := json.Marshal(p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(string(jsonData)) // 输出: {"id":"1","name":"Widget","Price":9.99}
}
```

### 18. 解码 JSON 中的任意字段

有时，您可能想解码 JSON 中的某些字段，而忽略其他字段。可以使用 `map[string]interface{}` 来解码任意 JSON 对象。

```go
package main

import (
	"encoding/json"
	"fmt"
)

func main() {
	jsonData := `{"name":"John","age":30,"address":{"city":"New York"}}`
	var result map[string]interface{}
	err := json.Unmarshal([]byte(jsonData), &result)
	if err != nil {
		fmt.Println(err)
		return
	}
	name := result["name"].(string)
	city := result["address"].(map[string]interface{})["city"].(string)
	fmt.Println(name, city) // 输出: John New York
}
```

### 19. 使用 `json.Number` 解析数字

当您不确定数字字段是否为整数或浮点数时，可以使用 `json.Number` 类型。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Product struct {
	ID   json.Number `json:"id"`
	Name string      `json:"name"`
}

func main() {
	jsonData := `{"id":12345678901234567890,"name":"Widget"}`
	var p Product
	err := json.Unmarshal([]byte(jsonData), &p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(p.ID, p.Name) // 输出: 12345678901234567890 Widget
}
```

### 20. 用不同的结构体解码同一 JSON

有时，您可能需要根据 JSON 的内容选择不同的结构体来解码。您可以先解码到中间结构，然后根据需要选择最终结构体。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Animal struct {
	Type string `json:"type"`
}

type Dog struct {
	Breed string `json:"breed"`
}

type Cat struct {
	Color string `json:"color"`
}

func main() {
	jsonData := `{"type":"dog","breed":"Labrador"}`

	var animal Animal
	err := json.Unmarshal([]byte(jsonData), &animal)
	if err != nil {
		fmt.Println(err)
		return
	}

	switch animal.Type {
	case "dog":
		var dog Dog
		err = json.Unmarshal([]byte(jsonData), &dog)
		fmt.Println(dog.Breed) // 输出: Labrador
	case "cat":
		var cat Cat
		err = json.Unmarshal([]byte(jsonData), &cat)
		fmt.Println(cat.Color)
	}
}
```

### 21. 使用匿名结构体解码 JSON

如果您只需要解码 JSON 的部分字段，可以使用匿名结构体，而无需定义完整的结构体。

```go
package main

import (
	"encoding/json"
	"fmt"
)

func main() {
	jsonData := `{"name":"John","age":30,"address":{"city":"New York"}}`

	var result struct {
		Name string `json:"name"`
		Age  int    `json:"age"`
	}
	err := json.Unmarshal([]byte(jsonData), &result)
	if err != nil {
		fmt.Println(err)
		return
	}

	fmt.Println(result.Name, result.Age) // 输出: John 30
}
```

### 22. 使用第三方库解析 JSON

有时，标准库可能不足以满足需求。例如，您可以使用第三方库 `github.com/json-iterator/go` 来替代标准库。

```bash
go get github.com/json-iterator/go
```

```go
package main

import (
	"fmt"
	jsoniter "github.com/json-iterator/go"
)

type Person struct {
	Name string `json:"name"`
	Age  int    `json:"age"`
}

func main() {
	var json = jsoniter.ConfigCompatibleWithStandardLibrary
	jsonData := `{"name":"John","age":30}`
	var p Person
	err := json.Unmarshal([]byte(jsonData), &p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(p.Name, p.Age) // 输出: John 30
}
```

### 23. 将嵌套 JSON 解码到结构体和 Map 的组合中

您可以将嵌套 JSON 解码到结构体和 Map 的组合中，以灵活处理未知或动态字段。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Person struct {
	Name    string                 `json:"name"`
	Age     int                    `json:"age"`
	Details map[string]interface{} `json:"details"`
}

func main() {
	jsonData := `{"name":"John","age":30,"details":{"city":"New York","married":false}}`
	var p Person
	err := json.Unmarshal([]byte(jsonData), &p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(p.Name, p.Details["city"], p.Details["married"]) // 输出: John New York false
}
```

### 24. 使用 `json.RawMessage` 处理部分未解码的 JSON

如果您想在解码时保留部分 JSON 的原始字节，可以使用 `json.RawMessage` 类型。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Person struct {
	Name string          `json:"name"`
	Age  int             `json:"age"`
	Info json.RawMessage `json:"info"`
}

func main() {
	jsonData := `{"name":"John","age":30,"info":{"city":"New York","married":false}}`
	var p Person
	err := json.Unmarshal([]byte(jsonData), &p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(string(p.Info)) // 输出: {"city":"New York","married":false}
}
```

### 25. 使用 `json.MarshalIndent` 格式化 JSON 输出

您可以使用 `json.MarshalIndent` 函数格式化 JSON 输出，使其更易读。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Person struct {
	Name string `json:"name"`
	Age  int    `json:"age"`
}

func main() {
	p := Person{Name: "John", Age: 30}
	jsonData, err := json.MarshalIndent(p, "", "  ")
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(string(jsonData))
	// 输出:
	// {
	//   "name": "John",
	//   "age": 30
	// }
}
```

### 26. 将 JSON 数组解码为结构体切片

您可以将 JSON 数组解码为结构体的切片。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Person struct {
	Name string `json:"name"`
	Age  int    `json:"age"`
}

func main() {
	jsonData := `[{"name":"John","age":30},{"name":"Alice","age":25}]`
	var people []Person
	err := json.Unmarshal([]byte(jsonData), &people)
	if err != nil {
		fmt.Println(err)
		return
	}
	for _, person := range people {
		fmt.Println(person.Name, person.Age)
	}
	// 输出:
	// John 30
	// Alice 25
}
```

### 27. 使用 `omitempty` 标记忽略空值字段

在编码 JSON 时，您可以使用 `omitempty` 标记来忽略具有零值（例如空字符串、零、nil 等）的字段。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Person struct {
	Name string `json:"name,omitempty"`
	Age  int    `json:"age,omitempty"`
}

func main() {
	p := Person{Name: "John"}
	jsonData, err := json.Marshal(p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(string(jsonData)) // 输出: {"name":"John"}
}
```

### 28. 使用指针处理可选字段

在解码 JSON 时，您可以使用指针来处理可选字段，从而区分缺失字段和零值字段。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Person struct {
	Name *string `json:"name"`
	Age  *int    `json:"age"`
}

func main() {
	jsonData := `{"name":"John"}`
	var p Person
	err := json.Unmarshal([]byte(jsonData), &p)
	if err != nil {
		fmt.Println(err)
		return
	}
	if p.Age == nil {
		fmt.Println("Age is missing")
	} else {
		fmt.Println("Age is zero")
	}
	// 输出: Age is missing
}
```

### 29. 将 JSON 解码为自定义类型

您可以创建自定义类型并将 JSON 解码到其中，以便于处理特定的数据结构。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Age int

type Person struct {
	Name string `json:"name"`
	Age  Age    `json:"age"`
}

func main() {
	jsonData := `{"name":"John","age":30}`
	var p Person
	err := json.Unmarshal([]byte(jsonData), &p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(p.Name, p.Age) // 输出: John 30
}
```

### 30. 解码带有未知键的 JSON 对象

有时，您可能需要解码具有未知键的 JSON 对象。您可以使用 `map[string]interface{}` 来实现。

```go
package main

import (
	"encoding/json"
	"fmt"
)

func main() {
	jsonData := `{"name":"John","age":30,"attributes":{"city":"New York","married":false}}`
	var result map[string]interface{}
	err := json.Unmarshal([]byte(jsonData), &result)
	if err != nil {
		fmt.Println(err)
		return
	}

	attributes := result["attributes"].(map[string]interface{})
	for key, value := range attributes {
		fmt.Println(key, ":", value)
	}
	// 输出:
	// city : New York
	// married : false
}
```

### 31. 解析 JSON 中的时间戳

您可以使用 `time.Time` 类型来解析 JSON 中的时间戳。

```go
package main

import (
	"encoding/json"
	"fmt"
	"time"
)

type Event struct {
	Name      string    `json:"name"`
	Timestamp time.Time `json:"timestamp"`
}

func main() {
	jsonData := `{"name":"Birthday Party","timestamp":"2023-08-07T14:00:00Z"}`
	var event Event
	err := json.Unmarshal([]byte(jsonData), &event)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(event.Name, event.Timestamp) // 输出: Birthday Party 2023-08-07 14:00:00 +0000 UTC
}
```

### 32. 使用 `-` 标记忽略字段

您可以使用 `-` 标记在 JSON 编码或解码时完全忽略结构体字段。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Person struct {
	Name    string `json:"name"`
	Secrets string `json:"-"`
}

func main() {
	p := Person{Name: "John", Secrets: "Hidden"}
	jsonData, err := json.Marshal(p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(string(jsonData)) // 输出: {"name":"John"}
}
```

### 33. 使用自定义函数处理特定的编码/解码逻辑

您可以定义自定义方法来处理特定字段的编码或解码逻辑。

```go
package main

import (
	"encoding/json"
	"fmt"
	"strings"
)

type Person struct {
	Name string `json:"name"`
	Age  int    `json:"age"`
}

func (p *Person) UnmarshalJSON(data []byte) error {
	var aux struct {
		Name string `json:"name"`
		Age  int    `json:"age"`
	}
	if err := json.Unmarshal(data, &aux); err != nil {
		return err
	}
	p.Name = strings.ToUpper(aux.Name) // 转换为大写
	p.Age = aux.Age
	return nil
}

func main() {
	jsonData := `{"name":"John","age":30}`
	var p Person
	err := json.Unmarshal([]byte(jsonData), &p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(p.Name, p.Age) // 输出: JOHN 30
}
```

### 34. 在嵌套结构体中使用 JSON 标记

您可以在嵌套结构体中使用 JSON 标记以便于解析复杂的 JSON 对象。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Address struct {
	City  string `json:"city"`
	State string `json:"state"`
}

type Person struct {
	Name    string  `json:"name"`
	Age     int     `json:"age"`
	Address Address `json:"address"`
}

func main() {
	jsonData := `{"name":"John","age":30,"address":{"city":"New York","state":"NY"}}`
	var p Person
	err := json.Unmarshal([]byte(jsonData), &p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(p.Name, p.Address.City, p.Address.State) // 输出: John New York NY
}
```

### 35. 使用自定义编码/解码函数处理复杂类型

您可以定义自定义的编码和解码函数来处理复杂类型。

```go
package main

import (
	"encoding/json"
	"fmt"
	"strings"
)

type Name struct {
	First  string
	Middle string
	Last   string
}

func (n *Name) MarshalJSON() ([]byte, error) {
	fullName := fmt.Sprintf("%s %s %s", n.First, n.Middle, n.Last)
	return json.Marshal(fullName)
}

func (n *Name) UnmarshalJSON(data []byte) error {
	var fullName string
	if err := json.Unmarshal(data, &fullName); err != nil {
		return err
	}
	parts := strings.Split(fullName, " ")
	if len(parts) == 3 {
		n.First = parts[0]
		n.Middle = parts[1]
		n.Last = parts[2]
	}
	return nil
}

func main() {
	jsonData := `"John A Doe"`
	var name Name
	err := json.Unmarshal([]byte(jsonData), &name)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(name.First, name.Middle, name.Last) // 输出: John A Doe
}
```

### 36. 使用 `json.Encoder` 和 `json.Decoder` 进行流式处理

对于大型 JSON 数据，您可以使用 `json.Encoder` 和 `json.Decoder` 进行流式处理。

```go
package main

import (
	"encoding/json"
	"strings"
	"fmt"
)

type Person struct {
	Name string `json:"name"`
	Age  int    `json:"age"`
}

func main() {
	jsonStream := `{"name":"John","age":30}{"name":"Alice","age":25}`
	decoder := json.NewDecoder(strings.NewReader(jsonStream))

	for {
		var p Person
		if err := decoder.Decode(&p); err != nil {
			fmt.Println("Error:", err)
			break
		}
		fmt.Println(p.Name, p.Age)
	}
	// 输出:
	// John 30
	// Alice 25
}
```

### 37. 解析内联数组

您可以使用切片类型来解析 JSON 中的内联数组。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Product struct {
	Name  string   `json:"name"`
	Tags  []string `json:"tags"`
}

func main() {
	jsonData := `{"name":"Laptop","tags":["electronics","computers"]}`
	var p Product
	err := json.Unmarshal([]byte(jsonData), &p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(p.Name, p.Tags) // 输出: Laptop [electronics computers]
}
```

### 38. 使用 `json.NewEncoder` 进行自定义输出

您可以使用 `json.NewEncoder` 创建自定义的 JSON 编码器。

```go
package main

import (
	"encoding/json"
	"os"
)

type Person struct {
	Name string `json:"name"`
	Age  int    `json:"age"`
}

func main() {
	p := Person{Name: "John", Age: 30}
	encoder := json.NewEncoder(os.Stdout)
	encoder.SetIndent("", "  ")
	encoder.Encode(p)
	// 输出:
	// {
	//   "name": "John",
	//   "age": 30
	// }
}
```

### 39. 使用 `interface{}` 类型处理灵活的 JSON 结构

有时，JSON 数据的结构可能会发生变化。您可以使用 `interface{}` 类型来处理此类灵活的 JSON 结构。

```go
package main

import (
	"encoding/json"
	"fmt"
)

func main() {
	jsonData := `{"name":"John","contact":{"email":"john@example.com"}}`
	var result map[string]interface{}
	err := json.Unmarshal([]byte(jsonData), &result)
	if err != nil {
		fmt.Println(err)
		return
	}

	contact, ok := result["contact"].(map[string]interface{})
	if ok {
		email, ok := contact["email"].(string)
		if ok {
			fmt.Println(email) // 输出: john@example.com
		}
	}
}
```

### 40. 使用 `omitempty` 和指针来解码可选字段

您可以使用 `omitempty` 标记和指针来解码可选字段，从而区分缺失字段和零值字段。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Person struct {
	Name string  `json:"name"`
	Age  *int    `json:"age,omitempty"`
}

func main() {
	jsonData := `{"name":"John"}`
	var p Person
	err := json.Unmarshal([]byte(jsonData), &p)
	if err != nil {
		fmt.Println(err)
		return
	}

	if p.Age == nil {
		fmt.Println("Age is missing") // 输出: Age is missing
	} else {
		fmt.Println("Age is zero")
	}
}
```

### 41. 使用预定义的 JSON 格式

如果您要与特定的 JSON 格式进行交互，可以预先定义必要的结构。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type User struct {
	FullName string `json:"name"`
	Age      int    `json:"age"`
}

type Users struct {
	Users []User `json:"users"`
}

func main() {
	jsonData := `{"users":[{"name":"John","age":30},{"name":"Alice","age":25}]}`
	var users Users
	err := json.Unmarshal([]byte(jsonData), &users)
	if err != nil {
		fmt.Println(err)
		return
	}

	for _, user := range users.Users {
		fmt.Println(user.FullName, user.Age)
	}
	// 输出:
	// John 30
	// Alice 25
}
```

### 42. 使用 `json:"-"` 忽略嵌入的结构体字段

您可以在嵌入的结构体中使用 `json:"-"` 忽略所有字段。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type HiddenDetails struct {
	Secret string
}

type Person struct {
	HiddenDetails `json:"-"`
	Name          string `json:"name"`
}

func main() {
	p := Person{HiddenDetails: HiddenDetails{Secret: "Hidden"}, Name: "John"}
	jsonData, err := json.Marshal(p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(string(jsonData)) // 输出: {"name":"John"}
}
```

### 43. 使用 `json.Encoder` 设置日期格式

您可以使用 `json.Encoder` 设置日期格式。

```go
package main

import (
	"encoding/json"
	"fmt"
	"time"
)

type Event struct {
	Timestamp time.Time `json:"timestamp"`
}

func main() {
	event := Event{Timestamp: time.Now()}
	encoder := json.NewEncoder(os.Stdout)
	encoder.SetTimeFormat(time.RFC3339Nano)
	encoder.Encode(event) // 输出日期时间的纳秒格式
}
```

### 44. 使用 `json:",inline"` 嵌入结构体字段

您可以使用 `json:",inline"` 在 JSON 编码中嵌入结构体字段，而不是创建一个新的对象。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Address struct {
	City  string `json:"city"`
	State string `json:"state"`
}

type Person struct {
	Name    string  `json:"name"`
	Address Address `json:",inline"`
}

func main() {
	p := Person{Name: "John", Address: Address{City: "New York", State: "NY"}}
	jsonData, err := json.Marshal(p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(string(jsonData)) // 输出: {"name":"John","city":"New York","state":"NY"}
}
```

### 45. 使用自定义结构体解码不同的 JSON 格式

有时，可能需要在不同的场景下解析不同的 JSON 格式。可以使用自定义的 Unmarshal 方法实现。

```go
package main

import (
	"encoding/json"
	"fmt"
	"strings"
)

type Role string

type User struct {
	Name string `json:"name"`
	Role Role   `json:"role"`
}

func (r *Role) UnmarshalJSON(data []byte) error {
	var roleString string
	if err := json.Unmarshal(data, &roleString); err != nil {
		return err
	}

	switch strings.ToLower(roleString) {
	case "admin":
		*r = "Administrator"
	case "user":
		*r = "Standard User"
	default:
		*r = "Unknown"
	}
	return nil
}

func main() {
	jsonData := `{"name":"John","role":"admin"}`
	var user User
	err := json.Unmarshal([]byte(jsonData), &user)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(user.Role) // 输出: Administrator
}
```

### 46. 使用接口实现动态解码

可以使用接口和类型断言来动态解码 JSON。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Animal interface {
	Speak() string
}

type Dog struct {
	Breed string `json:"breed"`
}

func (d Dog) Speak() string {
	return "Woof!"
}

type Cat struct {
	Color string `json:"color"`
}

func (c Cat) Speak() string {
	return "Meow!"
}

func main() {
	jsonData := `{"animal":"dog","breed":"Labrador"}`
	var m map[string]interface{}
	err := json.Unmarshal([]byte(jsonData), &m)
	if err != nil {
		fmt.Println(err)
		return
	}

	var animal Animal
	switch m["animal"].(string) {
	case "dog":
		var dog Dog
		json.Unmarshal([]byte(jsonData), &dog)
		animal = dog
	case "cat":
		var cat Cat
		json.Unmarshal([]byte(jsonData), &cat)
		animal = cat
	}

	fmt.Println(animal.Speak()) // 输出: Woof!
}
```

### 47. 使用 `map[string]interface{}` 来动态构建 JSON

您可以使用 `map[string]interface{}` 类型动态构建 JSON 对象，然后使用 `json.Marshal` 将其转换为 JSON 字符串。

```go
package main

import (
	"encoding/json"
	"fmt"
)

func main() {
	data := make(map[string]interface{})
	data["name"] = "John"
	data["age"] = 30
	data["address"] = map[string]string{
		"city":  "New York",
		"state": "NY",
	}

	jsonData, err := json.Marshal(data)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(string(jsonData)) // 输出: {"address":{"city":"New York","state":"NY"},"age":30,"name":"John"}
}
```

### 48. 使用 `json:",string"` 将数字编码为 JSON 字符串

可以使用 `json:",string"` 标签将数字编码为 JSON 字符串。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Product struct {
	ID    int    `json:"id,string"`
	Name  string `json:"name"`
	Price float64 `json:"price,string"`
}

func main() {
	p := Product{ID: 1, Name: "Laptop", Price: 999.99}
	jsonData, err := json.Marshal(p)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(string(jsonData)) // 输出: {"id":"1","name":"Laptop","price":"999.99"}
}
```

### 49. 解析 JSON 数组的一部分

您可以使用 `json.RawMessage` 来解析 JSON 数组的一部分，稍后再解析剩余部分。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Partial struct {
	Name   string          `json:"name"`
	Colors json.RawMessage `json:"colors"`
}

func main() {
	jsonData := `{"name":"Rainbow","colors":["red","orange","yellow","green","blue","indigo","violet"]}`
	var partial Partial
	err := json.Unmarshal([]byte(jsonData), &partial)
	if err != nil {
		fmt.Println(err)
		return
	}

	var colors []string
	json.Unmarshal(partial.Colors, &colors)
	fmt.Println(colors) // 输出: [red orange yellow green blue indigo violet]
}
```

### 50. 使用 `json.Decoder.Token` 读取 JSON 流

可以使用 `json.Decoder.Token` 逐个读取 JSON 流中的元素。

```go
package main

import (
	"encoding/json"
	"fmt"
	"strings"
)

func main() {
	jsonStream := `{"name":"John","age":30,"address":{"city":"New York"}}`
	decoder := json.NewDecoder(strings.NewReader(jsonStream))

	for {
		t, err := decoder.Token()
		if err != nil {
			break
		}
		fmt.Println(t) // 输出每个 JSON 标记
	}
}
```

Done.
