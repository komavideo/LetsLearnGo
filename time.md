使用time库
==========

Go语言的 `time` 库用于记录错误、警告和其他重要信息。

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

## Go 语言 time 库

### 1. 获取当前时间

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	currentTime := time.Now()
	fmt.Println("当前时间:", currentTime)
}
```

### 2. 格式化时间

你可以使用特定的格式化字符串来打印日期和时间。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	currentTime := time.Now()
	fmt.Println("当前时间:", currentTime.Format("2006-01-02 15:04:05"))
}
```

### 3. 时间计算

你可以使用 `time` 包对时间进行加减操作。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	currentTime := time.Now()
	// 添加一小时
	oneHourLater := currentTime.Add(time.Hour)
	fmt.Println("一小时后:", oneHourLater)

	// 减去一天
	oneDayBefore := currentTime.Add(-24 * time.Hour)
	fmt.Println("一天前:", oneDayBefore)
}
```

### 4. 时间间隔测量

你可以使用 `time` 包来测量代码执行的时间。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	start := time.Now()
	// 一些耗时的操作
	time.Sleep(2 * time.Second)
	elapsed := time.Since(start)
	fmt.Println("耗时:", elapsed)
}
```

### 5. 时间解析

你可以从字符串解析时间。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	timeString := "2023-08-11 15:04:05"
	parsedTime, err := time.Parse("2006-01-02 15:04:05", timeString)
	if err != nil {
		fmt.Println("解析错误:", err)
		return
	}
	fmt.Println("解析时间:", parsedTime)
}
```

### 6. 定时器

使用 `time.After` 和 `time.Tick` 可以创建定时器。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	timer := time.After(2 * time.Second)
	<-timer
	fmt.Println("2秒已过")

	ticker := time.Tick(1 * time.Second)
	for i := 0; i < 5; i++ {
		<-ticker
		fmt.Println("又过了1秒")
	}
}
```

### 7. 比较时间

你可以使用标准的比较运算符来比较时间。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	t1 := time.Now()
	time.Sleep(1 * time.Second)
	t2 := time.Now()

	if t1.Before(t2) {
		fmt.Println("t1 在 t2 之前")
	}

	if t2.After(t1) {
		fmt.Println("t2 在 t1 之后")
	}

	if t1.Equal(t2) {
		fmt.Println("t1 和 t2 相等")
	}
}
```

### 8. 时间的组成部分

你可以提取时间的组成部分，例如年、月、日等。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	currentTime := time.Now()
	year, month, day := currentTime.Date()
	hour, min, sec := currentTime.Clock()

	fmt.Printf("年: %d, 月: %d, 日: %d\n", year, month, day)
	fmt.Printf("时: %d, 分: %d, 秒: %d\n", hour, min, sec)
}
```

### 9. 转换时区

你可以将时间转换到指定的时区。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	currentTime := time.Now()
	fmt.Println("本地时间:", currentTime)

	location, err := time.LoadLocation("UTC")
	if err != nil {
		fmt.Println("加载时区错误:", err)
		return
	}

	utcTime := currentTime.In(location)
	fmt.Println("UTC时间:", utcTime)
}
```

### 10. 使用time.Sleep暂停执行

你可以使用 `time.Sleep` 来暂停执行指定的时间。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	fmt.Println("开始等待3秒...")
	time.Sleep(3 * time.Second)
	fmt.Println("3秒过去了!")
}
```

### 11. 创建自定义的定时器

使用 `time.NewTimer` 你可以创建一个定时器，并通过它的 `C` 属性来接收定时器触发的通知。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	timer := time.NewTimer(3 * time.Second)
	<-timer.C
	fmt.Println("3秒已过")
}
```

### 12. 创建自定义的打点器

使用 `time.NewTicker` 你可以创建一个打点器，它会定期发送时间到它的 `C` 属性。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	ticker := time.NewTicker(1 * time.Second)
	defer ticker.Stop()

	for i := 0; i < 5; i++ {
		<-ticker.C
		fmt.Println("又过了1秒")
	}
}
```

### 13. 解析和格式化持续时间

可以使用 `time.ParseDuration` 来解析持续时间，并使用 `String` 方法来格式化。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	duration, err := time.ParseDuration("1h30m")
	if err != nil {
		fmt.Println("解析错误:", err)
		return
	}
	fmt.Println("解析持续时间:", duration.String())
}
```

### 14. 使用time.AfterFunc调度函数调用

你可以使用 `time.AfterFunc` 来在指定的持续时间之后调用一个函数。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	time.AfterFunc(2*time.Second, func() {
		fmt.Println("2秒后的函数调用")
	})

	// 保持程序运行，以便查看输出
	time.Sleep(3 * time.Second)
}
```

### 15. 计算两个时间的差值

使用 `Sub` 方法，你可以计算两个时间的差值。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	start := time.Now()
	time.Sleep(2 * time.Second)
	end := time.Now()

	duration := end.Sub(start)
	fmt.Println("持续时间:", duration)
}
```

### 16. 获取时间的Unix时间戳

可以使用 `Unix` 和 `UnixNano` 来获取时间的 `Unix` 时间戳。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	currentTime := time.Now()
	unixTime := currentTime.Unix()
	unixNano := currentTime.UnixNano()

	fmt.Println("Unix 时间戳:", unixTime)
	fmt.Println("Unix 纳秒时间戳:", unixNano)
}
```








