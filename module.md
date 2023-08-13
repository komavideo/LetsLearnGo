模块化编程 module
=================

## Go 语言模块化编程

Go语言的模块化管理是一项非常有用的功能，允许你更容易地组织和管理代码。从 `Go 1.11` 开始，Go引入了模块（module）支持，使得依赖管理变得更简单。

### 1. 创建一个新项目

首先，你需要创建一个新的项目文件夹，并在其中初始化一个新的模块。

假设你的项目名为 goapp

```bash
mkdir goapp
cd goapp
go mod init github.com/komavideo/goapp

echo 'package main

import "fmt"

func main() {
	fmt.Println("Hello, World!")
}' > main.go

go run main.go
```

`go mod init` 命令将创建一个 `go.mod` 文件，其中包含了模块的名称和Go语言的版本。

### 2. 添加依赖

你可以通过在代码中导入所需的包，并运行 `go get` 命令来添加依赖。

```bash
go get -u github.com/gin-gonic/gin
```

这将更新 `go.mod` 文件，添加新的依赖，并创建一个 `go.sum` 文件来存储依赖的确切版本。

### 3. 编写代码

在你的项目文件夹中创建一个新的Go文件，例如 `main.go`。

```go
package main

import (
  "net/http"

  "github.com/gin-gonic/gin"
)

func main() {
  r := gin.Default()
  r.GET("/ping", func(c *gin.Context) {
    c.JSON(http.StatusOK, gin.H{
      "message": "pong",
    })
  })
  r.Run() // listen and serve on 0.0.0.0:8080 (for windows "localhost:8080")
}
```

### 4. 构建和运行项目

你可以使用以下命令来构建和运行你的项目：

```bash
go build
./goapp
```

```bash
# run main.go and visit 0.0.0.0:8080/ping on browser
$ go run main.go
```

### 5. 创建自己的模块

你也可以在你的项目中创建自己的模块。例如，你可以创建一个名为 `mymodule` 的文件夹，并在其中添加一个名为 `mymodule.go` 的文件。

```bash
mkdir mymodule
nano mymodule/mymodule.go
```

```go
package mymodule

import "fmt"

func MyFunction() {
	fmt.Println("这是我的第一个模块函数 MyFunction()!")
}
```

然后在你的main.go文件中导入并使用它：

```go
package main

import (
	"github.com/komavideo/goapp/mymodule"
)

func main() {
	mymodule.MyFunction()
}
```

```bash
go run main.go
```

### 6. 更新模块依赖

如果你想更新项目中的某个依赖，可以使用以下命令：

```bash
go get -u github.com/some/dependency
```

这将获取该依赖的最新版本，并更新go.mod和go.sum文件。

### 7. 移除未使用的依赖

如果你从代码中删除了对某个依赖的引用，可以运行以下命令来清理 `go.mod` 文件中未使用的依赖：

```bash
go mod tidy
```

### 8. 替换依赖（本地开发）

有时你可能想使用本地文件系统上的包代替远程库。例如，当你正在开发一个库并想在项目中测试它时。你可以在 `go.mod` 文件中使用 `replace` 指令：

```bash
replace github.com/some/dependency => /path/to/local/dependency
```

### 9. 发布你的模块

如果你想让其他人能够使用你的模块，你需要将代码托管在像GitHub这样的公共代码仓库中。然后你可以使用语义版本控制，通过Git标签发布版本：

```bash
git tag v0.1.0
git push --tags
```

其他人现在可以通过运行以下命令在他们的项目中使用你的模块：

```bash
go get github.com/yourusername/myproject@v0.1.0
```

### 10. 使用代理服务器

从 `Go 1.13` 开始，`Go`默认使用公共代理服务器 `proxy.golang.org` 来拉取依赖。这有助于提高获取依赖的速度和可靠性。你可以通过设置 `GOPROXY` 环境变量来使用自定义代理或禁用此功能：

```bash
export GOPROXY=direct
```

### 11. 分析模块依赖

你可以使用以下命令来查看模块的依赖树：

```bash
go mod graph
```

这有助于理解你的项目如何依赖于其他模块，并可能有助于诊断问题。

### 12. 制定依赖的特定版本

你可以在 `go get` 命令中指定依赖的特定版本：

```bash
go get github.com/some/dependency@v1.2.3
```

这将使你的项目依赖于该库的特定版本，而不是最新版本。你也可以使用分支名或提交哈希。

### 13. 使用vendor模式

从 `Go 1.14` 开始，你可以选择将项目的依赖复制到项目的 `vendor` 目录中。这使得你可以在没有 `Internet` 连接的情况下构建项目，或者确保你的项目总是使用依赖的特定副本。

要创建 `vendor` 目录，运行：

```bash
go mod vendor
```

然后，你可以使用以下命令构建项目，强制 `Go` 使用 `vendor` 目录中的依赖：

```bash
go build -mod=vendor
```

### 14. 模块兼容性和向后兼容性

当发布模块的新版本时，务必注意向后兼容性。Go鼓励遵循语义版本控制，其中破坏性更改应该导致主版本号增加。

https://semver.org/

### 15. 使用内部模块

你可以在项目中创建“内部”模块，这些模块只能由同一存储库中的其他代码导入。只需将模块放在名为 `“internal”` 的目录下。例如：

```bash
myproject/internal/mymodule
```

此模块只能被 `myproject` 中的其他代码导入。

### 16. 分析模块下载时间

你可以使用以下命令来查看模块下载的详细信息，这有助于诊断性能问题：

```bash
go get -x github.com/some/dependency
```

`-x` 标志将打印执行的每个步骤。

### 17. 理解模块的最小版本选择

Go的模块系统使用一种称为“最小版本选择”的算法来确定依赖的版本。这确保了构建的可重复性和一致性。你可以通过查看go.mod文件来理解哪些版本被选择。

### 18. 使用go list查询模块信息

你可以使用 `go list` 命令查询有关模块的各种信息。例如，以下命令将列出所有直接依赖：

```bash
go list -m all
```

### 19. 模块的隔离和安全性

请注意，依赖可能包含恶意代码。只使用来自可信来源的依赖，并定期审查安全更新。

### 20. 使用go mod why诊断依赖

如果你想了解为什么某个特定模块存在于你的依赖图中，你可以使用 `go mod why` 命令：

```bash
go mod why -m github.com/some/dependency
```

这将提供有关为什么项目依赖于该模块的信息，有助于理解和优化依赖关系。

### 21. 使用go mod edit编辑go.mod文件

`go mod edit` 命令允许你通过命令行编辑 `go.mod` 文件。例如，你可以使用以下命令添加一个替换：

```bash
go mod edit -replace=github.com/some/dependency=/path/to/local/dependency
```

这允许你通过自动化脚本更灵活地管理 `go.mod` 文件。

### 22. 测试依赖的特定版本

如果你想测试项目与依赖的不同版本之间的兼容性，你可以使用 `go test` 与 `-mod` 标志：

```bash
go test -mod=mod all
```

这将使用go.mod文件中指定的依赖版本运行测试。

### 23. 使用go mod download预下载依赖

你可以使用 `go mod download` 命令来预先下载项目的所有依赖项，而不构建它们：

```bash
go mod download
```

这可以用于预先填充缓存或准备离线工作。

### 24. 理解和管理间接依赖

间接依赖是你的项目所依赖的模块所需的依赖。你可以在 `go.mod` 文件中看到它们，它们通常在 `require` 块中标记为 `// indirect`。

虽然你不直接控制这些依赖，但了解它们的来源和作用有助于诊断问题。

### 25. 使用go mod verify验证依赖完整性

你可以使用 `go mod verify` 命令来确保缓存中的依赖与 `go.sum` 文件中的预期哈希匹配：

```bash
go mod verify
```

这有助于确保依赖的完整性和安全性。

### 26. 模块和环境变量

有几个与 `Go` 模块工作相关的环境变量，如 `GOPROXY` 和 `GONOPROXY`。了解和管理这些变量可以让你更精细地控制模块的行为。

### 27. 多模块存储库

在一个存储库中管理多个模块可能会变得复杂。如果你选择这样做，请务必了解每个模块如何相互作用，并确保正确设置了版本标签。

### 28. 使用go mod init的模块路径参数

当使用 `go mod init` 初始化新模块时，可以提供模块路径作为参数。这可以是任何字符串，但通常是代码托管在源代码存储库中的路径：

```bash
go mod init example.com/my/module
```

### 29. 分离测试依赖

在 `go.mod` 文件中，测试依赖与主应用程序依赖分开列出。通过运行 `go test ./...`，Go将自动检测和添加测试依赖。

### 30. 使用go get安装可执行文件

`go get` 不仅可以用于添加库依赖，还可以用于安装可执行的Go程序。例如：

```bash
go get github.com/golangci/golangci-lint/cmd/golangci-lint@latest
```

这将下载并安装指定的可执行文件。

### 31. 理解go.sum文件

`go.sum` 文件包含依赖的加密哈希，用于验证模块的完整性。它可能包括多个条目，以支持跨不同版本和系统的验证。

### 32. 排除特定依赖

如果你想从依赖解析中排除特定模块，你可以使用 `exclude` 指令在 `go.mod` 文件中进行操作：

```go
exclude github.com/some/dependency v1.0.0
```

### 33. 制定Go语言版本

在 `go.mod` 文件中，你可以通过 `go` 指令指定项目支持的Go语言版本：

```go
go 1.19
```

这有助于确保所有开发者使用相同的语言特性。

### 34. 使用GOPRIVATE环境变量

`GOPRIVATE` 环境变量可以用于控制哪些模块路径不应通过公共代理服务器获取。这对于私有或内部模块非常有用：

```bash
export GOPRIVATE=example.com/my/private,module
```

### 35. 使用go mod vendor与C语言依赖

如果你的项目依赖于C语言库，可以使用 `go mod vendor` 命令将这些库复制到 `vendor` 目录中，然后在构建时链接它们。

### 36. 托管私有模块

如果你有私有代码仓库，你可以通过配置适当的身份验证和访问控制来托管私有Go模块。这可能涉及与特定代码托管平台的集成。

### 37. 探索golang.org/x/子仓库

`golang.org/x/` 子仓库包含Go团队维护的实验和补充包。这些不是Go的标准库的一部分，但通常可以作为模块依赖安全地使用。

### 38. 使用go mod tidy -v了解更改

使用 `-v` 标志运行 `go mod tidy` 可以查看哪些模块被添加或删除。这有助于理解依赖关系的变化：

```bash
go mod tidy -v
```

### 39. 使用replace进行模块分叉

如果你需要对依赖的模块进行更改并在项目中测试它们，可以使用 `replace` 指令分叉它们。只需将 `replace` 指向你的本地副本：

```go
replace github.com/some/dependency => /path/to/your/forked/dependency
```

### 40. 理解和处理循环依赖

在某些情况下，项目可能会遇到循环依赖问题。解决这些问题可能涉及重新设计包和模块结构，以消除循环。

### 41. 使用-modfile标志

从 `Go 1.14` 开始，你可以使用 `-modfile` 标志与特定的 `go.mod` 文件一起运行go命令。这允许你在不更改主 `go.mod` 文件的情况下尝试更改：

```bash
go build -modfile=other.mod
```

### 42. 使用GOINSECURE跳过TLS验证

对于私有或自签名证书的仓库，可以使用 `GOINSECURE` 环境变量跳过 `TLS` 验证。请注意，这会降低安全性：

```bash
export GOINSECURE=example.com
```

### 43. 使用go mod why的-vendor选项

你可以使用 `-vendor` 选项与 `go mod why` 命令一起使用，以确定为什么特定依赖包含在 `vendor` 目录中：

```bash
go mod why -vendor github.com/some/dependency
```

### 44. 使用gofumpt进行代码格式化

`gofumpt` 是 `Go` 代码格式化工具的扩展，提供了更严格的格式化。它可以作为模块安装，并与 `Go` 工具链无缝集成。

### 45. 使用go mod graph的可视化工具

`go mod graph` 输出的依赖图可以使用可视化工具进行可视化。一些开源工具可以将输出转换为DOT格式或其他图形格式。

### 46. 探索和使用替代Go代理

除了官方的 `proxy.golang.org`，还有一些其他Go模块代理可用。它们可能提供附加功能，例如支持私有模块。

### 47. 使用gobin管理可执行依赖

`gobin` 是一个用于安装和管理 `Go` 可执行文件的工具。与 `go get` 不同，它允许更精细的控制和隔离。

### 48. 管理和升级间接依赖

间接依赖是由直接依赖引入的。你可以使用 `go get` 手动升级间接依赖，或使用 `go mod tidy` 来自动清理未使用的间接依赖。

### 49. 使用go list进行更复杂的查询

`go list` 命令非常强大，可以用于查询各种关于模块和包的信息。通过学习其标志和输出格式，你可以创建复杂的查询和自动化脚本。

### 50. 了解和遵循社区最佳实践

Go社区不断发展，随着时间的推移，可能会出现新的最佳实践和工具。通过关注Go博客、论坛和开发者社区，你可以保持与最佳实践和趋势的同步。

Done.
