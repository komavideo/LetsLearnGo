使用context库
============

`context` 是 `Go` 语言标准库的一部分，用于在多个 `goroutine` 之间传递截止时间、取消信号和其他请求范围的值。它在处理超时、取消和传递额外信息至其他 `API` 时非常有用。

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

## Go 语言 context 库

### 使用 context.WithCancel 创建可取消的上下文：

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	ctx, cancel := context.WithCancel(context.Background())
	go func() {
		select {
		case <-time.After(2 * time.Second):
			fmt.Println("Completed!")
		case <-ctx.Done():
			fmt.Println("Canceled!")
		}
	}()

	time.Sleep(1 * time.Second)
	cancel()
	time.Sleep(2 * time.Second)
}
```
在这个例子中，`goroutine` 在 `context` 被取消或超过 `2` 秒后终止。

### 使用 context.WithTimeout 创建有超时的上下文：

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	ctx, cancel := context.WithTimeout(context.Background(), 1*time.Second)
	defer cancel()

	go func() {
		select {
		case <-time.After(2 * time.Second):
			fmt.Println("Completed!")
		case <-ctx.Done():
			fmt.Println("Timeout!")
		}
	}()

	time.Sleep(3 * time.Second)
}
```
这个例子中，`goroutine` 会因为上下文的超时而终止。

### 使用 context.WithValue 传递值：

```go
package main

import (
	"context"
	"fmt"
)

type keyType string

func main() {
	key := keyType("name")
	ctx := context.WithValue(context.Background(), key, "Alice")

	displayName(ctx, key)
}

func displayName(ctx context.Context, key keyType) {
	if name, ok := ctx.Value(key).(string); ok {
		fmt.Println("Name:", name)
	} else {
		fmt.Println("No name found in context")
	}
}
```
在这个例子中，我们创建了一个新的上下文，其中包含一个名为 `"name"` 的键和值 `"Alice"`。然后我们在另一个函数中提取并显示这个值。

当使用 `context.WithValue` 时，建议使用自定义类型作为键，而不是普通字符串或其他基本类型，以避免意外的冲突。

### context.Background() 和 context.TODO()
两者都用于创建空的 `context`。主要的区别在于使用场景：

`context.Background()` 是最常用的，当你不知道应该使用哪种 `context` 时，通常会使用这个。

`context.TODO()` 用于那些还未确定怎样传递 `context` 的地方。例如，当你正在重构代码但还不确定最终如何传递 `context` 时，可以使用 `context.TODO()`。

```go
package main

import (
	"context"
	"fmt"
)

func main() {
	ctx := context.Background()
	process(ctx)

	ctxTODO := context.TODO()
	process(ctxTODO)
}

func process(ctx context.Context) {
	//...处理逻辑...
	fmt.Println(ctx)
}
```

### 检查 context 是否已经结束
你可以使用 `ctx.Err()` 检查 `context` 是否已经被取消或超时：

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	ctx, cancel := context.WithTimeout(context.Background(), 1*time.Second)
	defer cancel()

	// Sleep for 2 seconds, longer than the context's timeout
	time.Sleep(2 * time.Second)

	if err := ctx.Err(); err != nil {
		fmt.Println("Context error:", err)
	}
}
```
在上面的例子中，由于我们休眠了`2`秒（超过了上下文的1秒超时），因此 `ctx.Err()` 会返回一个非 `nil` 错误。

### 嵌套 context
你可以嵌套使用多个 `context`，例如，你可能想要一个可取消的上下文，但也带有超时：

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	// 创建一个可以手动取消的 context
	parentCtx, cancel := context.WithCancel(context.Background())
	defer cancel()

	// 在上面的基础上，创建一个有1秒超时的 context
	timeoutCtx, _ := context.WithTimeout(parentCtx, 1*time.Second)

	go func() {
		select {
		case <-time.After(2 * time.Second):
			fmt.Println("Completed!")
		case <-timeoutCtx.Done():
			fmt.Println(timeoutCtx.Err())
		}
	}()

	// 这里，我们让主函数休眠，以观察 goroutine 的行为
	time.Sleep(3 * time.Second)
}
```
上述代码将输出 `"context deadline exceeded"`，因为 `timeoutCtx` 在`2`秒的 `time.After` 完成之前超时了。

