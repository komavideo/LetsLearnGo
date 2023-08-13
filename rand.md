使用rand库
==========

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

## Go 语言 rand 库

Go语言的 math/rand 包提供了生成伪随机数的功能。

### 1. 生成整数随机数

你可以使用 `rand.Intn` 函数来生成一个在 `[0, n)` 之间的随机整数。

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	rand.Seed(time.Now().UnixNano())
	randomInt := rand.Intn(100) // 生成 0 到 99 之间的随机整数
	fmt.Println("Random integer:", randomInt)
}
```

### 2. 生成浮点随机数

你可以使用 `rand.Float64` 来生成一个在 `[0, 1)` 之间的随机浮点数。

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	rand.Seed(time.Now().UnixNano())
	randomFloat := rand.Float64() // 生成 0 到 1 之间的随机浮点数
	fmt.Println("Random float:", randomFloat)
}
```

### 3. 从切片中随机选择元素

你可以使用 `rand.Intn` 和切片的长度来随机选择一个元素。

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	rand.Seed(time.Now().UnixNano())
	fruits := []string{"Apple", "Banana", "Cherry", "Date"}
	randomIndex := rand.Intn(len(fruits))
	randomFruit := fruits[randomIndex]
	fmt.Println("Random fruit:", randomFruit)
}
```

### 4. 生成固定范围的随机浮点数

你可以使用下列方式生成一个在 `[min, max)` 之间的随机浮点数。

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	rand.Seed(time.Now().UnixNano())
	min := 10.0
	max := 20.0
	randomFloat := min + rand.Float64()*(max-min)
	fmt.Println("Random float between 10 and 20:", randomFloat)
}
```

### 5. 随机排列

你可以使用 `rand.Shuffle` 函数来随机排列切片。

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	rand.Seed(time.Now().UnixNano())
	numbers := []int{1, 2, 3, 4, 5}
	rand.Shuffle(len(numbers), func(i, j int) { numbers[i], numbers[j] = numbers[j], numbers[i] })
	fmt.Println("Shuffled numbers:", numbers)
}
```

请注意，在使用随机函数之前，最好通过当前时间来设置随机种子，以确保每次运行程序时都能得到不同的随机结果。如果不设置随机种子，每次程序运行时生成的随机数序列将会是相同的。

### 6. 生成随机字符串

你可以结合 `rand.Intn` 来生成一个指定长度的随机字符串。

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func randomString(length int) string {
	const letters = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"
	result := make([]byte, length)
	for i := range result {
		result[i] = letters[rand.Intn(len(letters))]
	}
	return string(result)
}

func main() {
	rand.Seed(time.Now().UnixNano())
	fmt.Println("Random string:", randomString(10))
}
```

### 7. 生成正态分布的随机数

使用 `rand.NormFloat64` 可以生成一个服从标准正态分布（均值为0，标准差为1）的随机浮点数。

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	rand.Seed(time.Now().UnixNano())
	randomNorm := rand.NormFloat64() // 生成正态分布随机数
	fmt.Println("Random normal distributed float:", randomNorm)
}
```

### 8. 使用自定义源生成随机数

如果你想有更细粒度的控制，可以使用自定义源来生成随机数。

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	source := rand.NewSource(time.Now().UnixNano())
	r := rand.New(source)
	randomInt := r.Intn(100) // 使用自定义源生成随机整数
	fmt.Println("Random integer:", randomInt)
}
```

### 9. 生成指定范围的随机整数

你可以结合 `rand.Intn` 生成一个在指定范围的随机整数。

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func randomInt(min, max int) int {
	return min + rand.Intn(max-min+1)
}

func main() {
	rand.Seed(time.Now().UnixNano())
	fmt.Println("Random integer between 10 and 20:", randomInt(10, 20))
}
```

### 10. 生成指定长度的随机字节切片

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func randomBytes(length int) []byte {
	bytes := make([]byte, length)
	rand.Read(bytes) // 注意检查错误，在此示例中省略
	return bytes
}

func main() {
	rand.Seed(time.Now().UnixNano())
	fmt.Println("Random bytes:", randomBytes(10))
}
```

### 11. 使用特定的种子源生成随机数

你可以使用特定的种子源来生成随机数，这在需要可重复的随机数序列时非常有用。

```go
package main

import (
	"fmt"
	"math/rand"
)

func main() {
	source := rand.NewSource(42) // 使用固定的种子值
	r := rand.New(source)
	randomInt := r.Intn(100)
	fmt.Println("Random integer with fixed seed:", randomInt)
}
```

### 12. 生成随机的时间

你可以生成一个随机的时间，比如在过去一周内的随机时间。

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	rand.Seed(time.Now().UnixNano())
	randomTime := time.Now().Add(-time.Duration(rand.Intn(7*24)) * time.Hour)
	fmt.Println("Random time in the past week:", randomTime)
}
```

### 13. 生成随机颜色

你可以生成随机的 RGB 颜色。

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	rand.Seed(time.Now().UnixNano())
	red := uint8(rand.Intn(256))
	green := uint8(rand.Intn(256))
	blue := uint8(rand.Intn(256))
	fmt.Printf("Random color: R:%v G:%v B:%v\n", red, green, blue)
}
```

### 14. 生成指定长度的随机密码

你可以生成一个包含大小写字母和数字的随机密码。

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func randomPassword(length int) string {
	const chars = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"
	result := make([]byte, length)
	for i := range result {
		result[i] = chars[rand.Intn(len(chars))]
	}
	return string(result)
}

func main() {
	rand.Seed(time.Now().UnixNano())
	fmt.Println("Random password:", randomPassword(12))
}
```

### 15. 使用rand.Perm生成随机排列

你可以使用 `rand.Perm` 来生成一个随机排列的整数切片。

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	rand.Seed(time.Now().UnixNano())
	perm := rand.Perm(10) // 生成 0 到 9 的随机排列
	fmt.Println("Random permutation:", perm)
}
```

### 16. 使用 ExpFloat64 生成指数分布的随机数

你可以使用 `rand.ExpFloat64` 来生成一个服从指数分布的随机浮点数。

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	rand.Seed(time.Now().UnixNano())
	randomExp := rand.ExpFloat64() // 生成指数分布随机数
	fmt.Println("Random exponential distributed float:", randomExp)
}
```

### 17. 随机选择枚举类型

假设你有一个枚举类型，你可以随机选择其中一个枚举值。

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

type Direction int

const (
	North Direction = iota
	East
	South
	West
)

func main() {
	rand.Seed(time.Now().UnixNano())
	randomDirection := Direction(rand.Intn(4))
	fmt.Println("Random direction:", randomDirection)
}
```

### 18. 使用 rand.Rand 创建随机数生成器

你可以创建一个 `*rand.Rand` 类型的变量，这样你可以在多个地方使用同一个随机数生成器，保证随机性。

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	source := rand.NewSource(time.Now().UnixNano())
	randomizer := rand.New(source)
	fmt.Println("Random integer:", randomizer.Intn(100))
	fmt.Println("Random float:", randomizer.Float64())
}
```

### 19. 使用 Int31, Int63 生成特定大小的整数

你可以使用 `Int31` 和 `Int63` 生成特定位大小的随机整数。

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	rand.Seed(time.Now().UnixNano())
	randomInt31 := rand.Int31()
	randomInt63 := rand.Int63()
	fmt.Println("Random 31-bit integer:", randomInt31)
	fmt.Println("Random 63-bit integer:", randomInt63)
}
```

### 20. 使用 rand.Uint32 生成无符号32位整数

你还可以生成无符号的32位随机整数。

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	rand.Seed(time.Now().UnixNano())
	randomUint32 := rand.Uint32()
	fmt.Println("Random 32-bit unsigned integer:", randomUint32)
}
```

Done.
