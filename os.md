使用os库
=========

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

## Go 语言 os 库

### 1. 读取环境变量

使用 `os.Getenv` 可以读取环境变量的值。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	path := os.Getenv("PATH")
	fmt.Println("PATH:", path)
}
```

### 2. 创建文件

使用 `os.Create` 可以创建一个新文件。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	file, err := os.Create("example.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer file.Close()

	file.WriteString("Hello, World!")
}
```

### 3. 读取文件

使用 `os.Open` 可以打开一个文件进行读取。

```go
package main

import (
	"fmt"
	"io/ioutil"
	"os"
)

func main() {
	file, err := os.Open("example.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer file.Close()

	content, err := ioutil.ReadAll(file)
	if err != nil {
		fmt.Println(err)
		return
	}

	fmt.Println(string(content))
}
```

### 4. 检查文件或目录是否存在

使用 `os.Stat` 可以检查文件或目录是否存在。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	_, err := os.Stat("example.txt")
	if os.IsNotExist(err) {
		fmt.Println("File does not exist.")
	} else {
		fmt.Println("File exists.")
	}
}
```

### 5. 获取当前工作目录

使用 `os.Getwd` 可以获取当前工作目录。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	dir, err := os.Getwd()
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("Current working directory:", dir)
}
```

### 6. 改变当前工作目录

使用 `os.Chdir` 可以改变当前工作目录。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	err := os.Chdir("/tmp")
	if err != nil {
		fmt.Println(err)
		return
	}
	dir, _ := os.Getwd()
	fmt.Println("Changed working directory to:", dir)
}
```

### 7. 创建目录

使用 `os.Mkdir` 和 `os.MkdirAll` 可以创建目录。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	err := os.Mkdir("new_directory", 0755)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("Directory created")

	// 创建多级目录
	err = os.MkdirAll("new_directory/sub_directory", 0755)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("Sub-directory created")
}
```

### 8. 删除文件和目录

使用 `os.Remove` 和 `os.RemoveAll` 可以删除文件和目录。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	err := os.Remove("example.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("File deleted")

	// 删除目录及其内容
	err = os.RemoveAll("new_directory")
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("Directory deleted")
}
```

### 9. 重命名文件或目录

使用 `os.Rename` 可以重命名文件或目录。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	err := os.Rename("old_name.txt", "new_name.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("File renamed")
}
```

### 10. 获取文件或目录的信息

使用 `os.Stat` 可以获取文件或目录的信息。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	info, err := os.Stat("example.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("File name:", info.Name())
	fmt.Println("Size:", info.Size())
	fmt.Println("Is directory:", info.IsDir())
	fmt.Println("Modification time:", info.ModTime())
}
```









