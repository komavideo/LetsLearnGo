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

### context 的传播
当你在一个系统中工作，尤其是在微服务架构中，经常需要将上下文从一个部分传递到另一个部分，例如从一个服务传递到另一个服务。这是为了确保跟踪、日志或其他有关请求的信息能够在整个流程中被保留和观察。

```go
package main

import (
	"context"
	"fmt"
)

type keyType string

func main() {
	key := keyType("userID")
	ctx := context.WithValue(context.Background(), key, "12345")

	processRequest(ctx)
}

func processRequest(ctx context.Context) {
	if userID, ok := ctx.Value(keyType("userID")).(string); ok {
		fmt.Println("Processing request for user:", userID)
	}
	// ...其他处理逻辑...
}
```

### 避免存储可变对象
当使用 `context.WithValue` 时，应该避免存储在 `context` 中的值是可以变的。如果你需要传递一个可变对象，最好传递该对象的副本或使用其他方法。

```go
package main

import (
	"context"
	"fmt"
)

type keyType string
type user struct {
	name string
}

func main() {
	u := &user{name: "Alice"}
	ctx := context.WithValue(context.Background(), keyType("user"), u)

	// 不建议修改存储在 context 中的值
	u.name = "Bob"

	displayUser(ctx)
}

func displayUser(ctx context.Context) {
	if u, ok := ctx.Value(keyType("user")).(*user); ok {
		fmt.Println("User name:", u.name) // 输出 "User name: Bob"
	}
}
```
在上面的例子中，我们修改了存储在 `context` 中的 `user` 对象，这通常是不推荐的。

### 优雅的处理取消
当 `context` 被取消时，可以优雅地关闭资源或完成必要的清理工作。

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

	ch := make(chan struct{})

	go doWork(ctx, ch)

	select {
	case <-ch:
		fmt.Println("Work finished!")
	case <-ctx.Done():
		fmt.Println("Context expired:", ctx.Err())
	}
}

func doWork(ctx context.Context, ch chan struct{}) {
	// 模拟工作
	time.Sleep(500 * time.Millisecond)

	select {
	case ch <- struct{}{}:
	case <-ctx.Done():
		// 清理工作或释放资源
		return
	}
}
```
上述代码模拟了一个耗时任务。如果 `context` 在任务完成之前过期或被取消，我们可以在 `<-ctx.Done()` 中执行任何必要的清理工作。

### 将 context 与标准库中的其他包一起使用
许多 `Go` 的标准库函数都接受 `context` 作为第一个参数，使得这些函数可以响应取消或超时的信号。例如，数据库和`HTTP`客户端常常会使用到这个特性。

下面是使用 `http` 包发送请求并使用 `context` 的例子：

```go
package main

import (
	"context"
	"fmt"
	"net/http"
	"time"
)

func main() {
	ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
	defer cancel()

	req, err := http.NewRequestWithContext(ctx, "GET", "https://www.example.com", nil)
	if err != nil {
		panic(err)
	}

	resp, err := http.DefaultClient.Do(req)
	if err != nil {
		fmt.Println("Request failed:", err)
		return
	}
	defer resp.Body.Close()

	fmt.Println("Response status:", resp.Status)
}
```
在这个例子中，如果请求超过`2`秒没有响应，`DefaultClient.Do(req)` 会返回一个错误，通常是 `"context deadline exceeded"`。

### 在自定义结构或类型中使用 context
尽管通常建议将 `context` 作为函数的第一个参数传递，但有时你可能想在自定义的结构或类型中包含它，特别是在某些框架或库中。

```go
package main

import (
	"context"
	"fmt"
)

type Worker struct {
	Ctx context.Context
}

func NewWorker(ctx context.Context) *Worker {
	return &Worker{Ctx: ctx}
}

func (w *Worker) DoTask() {
	if w.Ctx.Err() != nil {
		fmt.Println("Task aborted due to:", w.Ctx.Err())
		return
	}
	// ...执行任务逻辑...
}

func main() {
	ctx, cancel := context.WithCancel(context.Background())
	worker := NewWorker(ctx)

	cancel() // 模拟外部取消
	worker.DoTask()
}
```
这种模式可以方便你在类型的多个方法之间共享和传递 `context`。

### 并发地等待多个操作
有时，你可能需要并发地启动多个操作，并在其中任何一个完成或失败时取消其它的操作。`errgroup` 与 `context` 配合使用，可以简化这种模式：

```go
package main

import (
	"context"
	"errors"
	"fmt"
	"golang.org/x/sync/errgroup"
	"time"
)

func main() {
	ctx, cancel := context.WithCancel(context.Background())
	defer cancel()

	g, ctx := errgroup.WithContext(ctx)

	g.Go(func() error {
		time.Sleep(3 * time.Second)
		return errors.New("task 1 failed")
	})

	g.Go(func() error {
		select {
		case <-time.After(5 * time.Second):
			return nil
		case <-ctx.Done():
			return ctx.Err()
		}
	})

	if err := g.Wait(); err != nil {
		fmt.Println("Received error:", err)
	}
}
```

`errgroup` 可以确保当其中一个 `goroutine` 返回错误时，其它的 `goroutine` 会通过 `context` 取消。

### 注意 context 的继承性
当你从一个已有的 `context` 创建新的 `context` 时，新的 `context` 会继承父 `context` 的属性。例如，如果父 `context` 被取消，所有从它派生出来的子 `context` 也会被取消。

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	parentCtx, parentCancel := context.WithCancel(context.Background())

	childCtx, _ := context.WithTimeout(parentCtx, 5*time.Second)

	go func() {
		<-parentCtx.Done()
		fmt.Println("Parent context cancelled")
	}()

	go func() {
		<-childCtx.Done()
		fmt.Println("Child context done:", childCtx.Err())
	}()

	time.Sleep(2 * time.Second)
	parentCancel() // 取消父 context

	time.Sleep(3 * time.Second) // 使输出更清晰
}
```
在这个示例中，我们首先创建了一个可取消的 `parentCtx`。然后我们从 `parentCtx` 创建了一个带有超时的 `childCtx`。当我们取消 `parentCtx` 时，`childCtx` 也会因为继承关系被取消。

### 避免过度使用 context.Value
尽管 `context.Value` 可用于传递跨 `API` 的值，但它并不是为大量或复杂数据传递而设计的。过度使用它可能会使代码变得难以维护和理解。当你需要传递大量数据时，考虑使用其他机制，如参数、接收者或全局变量。

### context 的正确传递
在将 `context` 传递给函数或方法时，通常应将其作为第一个参数。这是一个广为接受的惯例，有助于提高代码的可读性和一致性。

```go
func doSomething(ctx context.Context, arg1 int, arg2 string) {
	// ...
}
```

### 清理资源
使用 `context` 的一个重要用例是确保在操作被取消或超时时释放资源。这包括关闭数据库连接、释放文件句柄、取消网络请求等。

```go
package main

import (
	"context"
	"fmt"
	"net/http"
	"time"
)

func main() {
	ctx, cancel := context.WithTimeout(context.Background(), 1*time.Second)
	defer cancel()

	req, _ := http.NewRequestWithContext(ctx, "GET", "https://www.example.com", nil)

	go func() {
		_, err := http.DefaultClient.Do(req)
		if err != nil {
			fmt.Println("Failed to complete the request:", err)
		}
	}()

	<-ctx.Done()

	// 当 context 完成时，我们可以执行额外的清理工作
	// 例如: 释放资源、关闭连接等
	fmt.Println("Context finished:", ctx.Err())
}
```

### 检查 context 的完成状态
有时你可能只想知道 `context` 是否已完成，而不是立即执行某些操作。你可以通过检查 `<-ctx.Done()` 来实现这一点，但如果你只想做一个快速检查而不阻塞，可以使用 `ctx.Err()`：

```go
if err := ctx.Err(); err != nil {
	fmt.Println("Context error:", err)
}
```

### context 和 Go 例程的生命周期
一个常见的模式是为每个 `Go` 例程提供一个 `context`，以便可以独立地控制每个 `Go` 例程的生命周期。例如，你可能有一个生产者和多个消费者：

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func producer(ctx context.Context, ch chan int) {
	n := 0
	for {
		select {
		case <-ctx.Done():
			close(ch)
			return
		case ch <- n:
			n++
			time.Sleep(500 * time.Millisecond)
		}
	}
}

func consumer(ctx context.Context, id int, ch chan int) {
	for {
		select {
		case v, ok := <-ch:
			if !ok {
				return
			}
			fmt.Printf("Consumer %d: received %d\n", id, v)
		case <-ctx.Done():
			return
		}
	}
}

func main() {
	ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
	defer cancel()

	ch := make(chan int)
	go producer(ctx, ch)
	for i := 0; i < 3; i++ {
		go consumer(ctx, i, ch)
	}

	<-ctx.Done()
}
```

### 使用 context.TODO()
当你不确定应该使用哪个 `context` 或你正在重构代码而不想立即引入 `context` 时，可以使用 `context.TODO()`。它返回一个非 `nil` 的空 `context`，这可以作为一个临时解决方案，但你应该尽快替换它。

```go
func SomeFunction() {
	// TODO: Decide which context to use.
	ctx := context.TODO()
	doWork(ctx)
}
```

### 不要存储 context 在结构体中作为长期使用
虽然有时可能会被诱惑将 `context` 存储为结构体的一部分，但你应该避免这样做，除非你有明确的理由。`context` 是设计用于单次请求的：它不应该跨多个请求或长时间存活的对象共享。

如果你发现自己经常需要在结构体中存储 `context`，那么可能是你的设计需要重新思考。

### context.WithValue 的使用场景
虽然我们已经提到了避免过度使用 `context.Value`，但了解它的合适使用场景是很有价值的。`context.WithValue` 主要用于跨 `API` 或函数传递请求范围内的元数据或某些不容易通过常规参数传递的数据。

例如，传递请求 `ID`、认证令牌或用户信息：

```go
type contextKey string

func main() {
	ctx := context.WithValue(context.Background(), contextKey("requestID"), "12345")
	doSomething(ctx)
}

func doSomething(ctx context.Context) {
	reqID, ok := ctx.Value(contextKey("requestID")).(string)
	if ok {
		fmt.Println("Request ID:", reqID)
	}
}
```
注意我们使用自定义类型 `contextKey` 作为键，这是为了避免潜在的键冲突。

### context 和错误处理
`context` 可以用来取消操作，但它不能替代错误处理。当 `context` 表示已完成（如超时或取消）时，你通常还需要检查函数或操作的错误返回值以获取更具体的错误原因。

### 嵌套 context
虽然可以嵌套使用多个 `context`，但这通常是不必要的。当你从一个 `context` 创建另一个时，新的 `context` 会继承旧的 `context` 的行为。例如，如果你有一个可取消的 `context` 并从中创建了一个带有超时的 `context`，则当任何一个被取消或超时时，该 `context` 都会变为“已完成”。

### context 和测试
在单元测试中，你可能经常使用 `context.Background()`。但为了更真实地模拟生产环境，考虑使用带有超时或取消功能的 `context` 来确保你的代码正确响应这些信号。

```go
func TestSomeFunction(t *testing.T) {
	ctx, cancel := context.WithTimeout(context.Background(), 100*time.Millisecond)
	defer cancel()

	// 运行你的测试函数或方法
	result, err := someFunction(ctx)
	// ...
}
```

### 注意 context 的资源使用
即使一个 `context` 完成了（被取消、超时或有其他错误），与该 `context` 相关联的资源（如计时器或内部数据）可能仍然存在直到垃圾收集。因此，当使用 `context.WithTimeout` 或 `context.WithDeadline` 时，记得总是调用返回的 `cancel` 函数来释放这些资源，即使你认为 `context` 已经完成了。

```go
ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
doSomething(ctx)
cancel() // 释放与ctx相关的资源
```

### context 的设计哲学
理解 `context` 的设计哲学可以帮助你更好地使用它。`context` 主要是为了共享跨 `API` 边界的超时、取消信号以及请求范围的元数据。它不是用来传递函数的可选参数，也不是用来存储全局状态或函数的返回值的。

### 在中间件中使用 context
在 `Web` 服务器或其他中间件中，`context` 可用于保存并传递与请求相关的数据，如请求 `ID`、认证数据等。

```go
func middleware(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		ctx := context.WithValue(r.Context(), "requestID", "123456")
		next.ServeHTTP(w, r.WithContext(ctx))
	})
}
```

### context 的并发安全性
`context` 本身是并发安全的，可以在多个 `Go` 例程之间共享而无需额外的锁。但请注意，从 `context` 中取出的值可能不是并发安全的，需要你自己确保安全的访问。

### 不要修改 context 的内部结构
虽然可以通过类型断言和反射来探索 `context` 的内部结构，但这样做是不推荐的。`context` 的具体实现可能会在未来的 `Go` 版本中发生变化。总是使用官方的 `API` 来与 `context` 交互。

### 尽量减少创建 context 的次数
每次调用 `context.WithCancel`、`context.WithDeadline`、`context.WithTimeout` 或 `context.WithValue` 都会创建新的 `context` 对象。如果你在循环或频繁调用的函数中这样做，可能会增加不必要的开销。考虑是否可以复用已有的 `context` 或将其创建移到循环或函数的外部。

到此为止，我们已经涵盖了 `Go` 的 `context` 库的大部分重要和高级概念。正如你所看到的，`context` 提供了一个强大且灵活的工具，使你可以在 `Go` 应用程序中管理并发、超时和取消操作，以及传递请求范围的元数据。使用它时，重要的是始终遵循最佳实践，并确保你的代码保持简洁和可维护。
