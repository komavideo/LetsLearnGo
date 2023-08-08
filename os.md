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

### 11. 设置环境变量

使用 `os.Setenv` 可以设置环境变量。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	err := os.Setenv("MY_VARIABLE", "value")
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("Environment variable set")
}
```

### 12. 获取文件权限

使用 `os.FileMode` 可以获取文件的权限。

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
	fmt.Printf("File permissions: %04o\n", info.Mode().Perm())
}
```

### 13. 更改文件权限

使用 `os.Chmod` 可以更改文件的权限。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	err := os.Chmod("example.txt", 0644)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("File permissions changed")
}
```

### 14. 更改文件所有者

使用 `os.Chown` 可以更改文件的所有者。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	err := os.Chown("example.txt", 1001, 1001) // 使用具体的用户和组ID
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("File owner changed")
}
```

### 15. 创建符号链接

使用 `os.Symlink` 可以创建符号链接。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	err := os.Symlink("original.txt", "link.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("Symbolic link created")
}
```

### 16. 读取命令行参数

使用 `os.Args` 可以读取命令行参数。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	args := os.Args
	fmt.Println("Command line arguments:", args)
}
```

### 17. 退出程序

使用 `os.Exit` 可以退出程序并返回特定的退出代码。

```go
package main

import (
	"os"
)

func main() {
	os.Exit(1) // 返回退出代码 1
}
```

### 18. 获取用户的 Home 目录

使用 `os.UserHomeDir` 可以获取当前用户的 Home 目录。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	home, err := os.UserHomeDir()
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("User's home directory:", home)
}
```

### 19. 获取文件的所有者信息

通过 `os.FileInfo` 和 `os/user` 包可以获取文件的所有者信息。

```go
package main

import (
	"fmt"
	"os"
	"os/user"
)

func main() {
	info, err := os.Stat("example.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	uid := fmt.Sprint(info.Sys().(*stat_t).Uid)
	userInfo, err := user.LookupId(uid)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("File owner:", userInfo.Username)
}
```

### 20. 打开和关闭文件描述符

使用 `os.NewFile` 可以从文件描述符创建一个新文件，并用 `file.Close` 来关闭。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	file := os.NewFile(3, "example.txt") // 3 是文件描述符
	if file == nil {
		fmt.Println("Error opening file")
		return
	}
	defer file.Close()

	fmt.Println("File opened successfully")
}
```

### 21. 临时文件和目录

使用 `os.CreateTemp` 和 `os.MkdirTemp` 可以创建临时文件和目录。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	tempFile, err := os.CreateTemp("", "example.*.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer os.Remove(tempFile.Name()) // 清理

	fmt.Println("Temporary file created:", tempFile.Name())

	tempDir, err := os.MkdirTemp("", "example")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer os.RemoveAll(tempDir) // 清理

	fmt.Println("Temporary directory created:", tempDir)
}
```

### 22. 管道操作

使用 `os.Pipe` 可以创建一个匿名管道。

```go
package main

import (
	"fmt"
	"io"
	"os"
)

func main() {
	reader, writer, err := os.Pipe()
	if err != nil {
		fmt.Println(err)
		return
	}

	go func() {
		writer.Write([]byte("Hello, World!"))
		writer.Close()
	}()

	buffer := make([]byte, 13)
	reader.Read(buffer)
	fmt.Println(string(buffer)) // 输出 "Hello, World!"
}
```

### 23. 文件锁

使用 `os.File` 的 `Flock` 方法可以对文件进行锁定。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	file, err := os.OpenFile("example.txt", os.O_RDWR, 0666)
	if err != nil {
		fmt.Println(err)
		return
	}
	defer file.Close()

	err = file.Flock(os.LOCK_EX) // 获取独占锁
	if err != nil {
		fmt.Println(err)
		return
	}

	// 进行文件操作

	err = file.Flock(os.LOCK_UN) // 释放锁
	if err != nil {
		fmt.Println(err)
		return
	}
}
```

注意：Flock 是平台相关的，并不是在所有系统上都可用。

### 24. 设置文件的修改时间

使用 `os.Chtimes` 可以更改文件的访问时间和修改时间。

```go
package main

import (
	"fmt"
	"os"
	"time"
)

func main() {
	err := os.Chtimes("example.txt", time.Now(), time.Now())
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("File access and modification times updated")
}
```

### 25. 获取系统的页面大小

使用 `os.Getpagesize` 可以获取操作系统的内存页面大小。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	size := os.Getpagesize()
	fmt.Printf("System page size: %d bytes\n", size)
}
```

### 26. 获取进程ID和父进程ID

使用 `os.Getpid` 和 `os.Getppid` 可以获取进程ID和父进程ID。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	pid := os.Getpid()
	ppid := os.Getppid()
	fmt.Printf("Process ID: %d\nParent Process ID: %d\n", pid, ppid)
}
```

### 27. 环境变量的操作

使用 `os.Environ` 获取所有环境变量，`os.LookupEnv` 检查环境变量是否存在。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	// 获取所有环境变量
	envVars := os.Environ()
	for _, env := range envVars {
		fmt.Println(env)
	}

	// 查找环境变量
	value, exists := os.LookupEnv("PATH")
	if exists {
		fmt.Println("PATH exists:", value)
	} else {
		fmt.Println("PATH does not exist")
	}
}
```

### 28. 执行外部命令

虽然不是 `os` 包的一部分，但 `os/exec` 包允许您从 Go 程序运行外部命令。

```go
package main

import (
	"fmt"
	"os/exec"
)

func main() {
	cmd := exec.Command("ls", "-l")
	output, err := cmd.Output()
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(string(output))
}
```

### 29. 监听系统信号

您可以使用 `os/signal` 包来监听系统信号。

```go
package main

import (
	"fmt"
	"os"
	"os/signal"
	"syscall"
)

func main() {
	sigCh := make(chan os.Signal, 1)
	signal.Notify(sigCh, syscall.SIGINT, syscall.SIGTERM)

	sig := <-sigCh
	fmt.Println("Received signal:", sig)
}
```

### 30. 使用文件锁（跨平台）

使用 `os.OpenFile` 和 `syscall.Flock` 可以实现文件锁。

```go
package main

import (
	"fmt"
	"os"
	"syscall"
)

func main() {
	file, err := os.OpenFile("example.txt", os.O_RDWR, 0666)
	if err != nil {
		fmt.Println(err)
		return
	}
	defer file.Close()

	err = syscall.Flock(int(file.Fd()), syscall.LOCK_EX)
	if err != nil {
		fmt.Println(err)
		return
	}

	// 执行文件操作

	err = syscall.Flock(int(file.Fd()), syscall.LOCK_UN)
	if err != nil {
		fmt.Println(err)
		return
	}
}
```

### 31. 判断错误类型

使用 `os.IsPermission`, `os.IsExist`, `os.IsNotExist` 等函数可以判断错误的类型。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	_, err := os.Open("nonexistent_file.txt")
	if os.IsNotExist(err) {
		fmt.Println("File does not exist")
	} else if os.IsPermission(err) {
		fmt.Println("Permission denied")
	} else {
		fmt.Println(err)
	}
}
```

### 32. 文件和目录的遍历

使用 `filepath.Walk` 可以递归遍历目录及其所有子目录。

```go
package main

import (
	"fmt"
	"os"
	"path/filepath"
)

func main() {
	root := "." // 当前目录
	err := filepath.Walk(root, func(path string, info os.FileInfo, err error) error {
		if err != nil {
			fmt.Println(err)
			return err
		}
		fmt.Println(path)
		return nil
	})
	if err != nil {
		fmt.Println(err)
	}
}
```

### 33. 获取文件的绝对路径

使用 `filepath.Abs` 可以获取文件或目录的绝对路径。

```go
package main

import (
	"fmt"
	"path/filepath"
)

func main() {
	absPath, err := filepath.Abs("example.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("Absolute path:", absPath)
}
```

### 34. 使用 Glob 匹配文件

使用 `filepath.Glob` 可以通过模式匹配找到文件。

```go
package main

import (
	"fmt"
	"path/filepath"
)

func main() {
	matches, err := filepath.Glob("*.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	for _, match := range matches {
		fmt.Println(match)
	}
}
```

### 35. 分割和连接路径

使用 `filepath.Split` 和 `filepath.Join` 可以分割和连接路径。

```go
package main

import (
	"fmt"
	"path/filepath"
)

func main() {
	dir, file := filepath.Split("/path/to/example.txt")
	fmt.Println("Directory:", dir)
	fmt.Println("File:", file)

	path := filepath.Join(dir, file)
	fmt.Println("Joined path:", path)
}
```

### 36. 创建硬链接

使用 `os.Link` 可以创建硬链接。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	err := os.Link("original.txt", "hardlink.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("Hard link created")
}
```

### 37. 清空文件内容

使用 `os.Truncate` 可以清空文件内容。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	err := os.Truncate("example.txt", 0)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("File truncated")
}
```

### 38. 检测文件是否是符号链接

使用 `os.Lstat` 和 `os.ModeSymlink` 可以检测文件是否是符号链接。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	info, err := os.Lstat("link.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	if info.Mode()&os.ModeSymlink != 0 {
		fmt.Println("File is a symbolic link")
	} else {
		fmt.Println("File is not a symbolic link")
	}
}
```

### 39. 使用 `os.FileMode` 类型

`os.FileMode` 类型表示文件的模式和权限。可以用它来检查文件是否是目录、是否有执行权限等。

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
	mode := info.Mode()
	if mode.IsRegular() {
		fmt.Println("Regular file")
	}
	if mode.IsDir() {
		fmt.Println("Directory")
	}
	if mode&os.ModeSymlink != 0 {
		fmt.Println("Symbolic link")
	}
	if mode.Perm()&0400 != 0 {
		fmt.Println("Owner has read permission")
	}
}
```

### 40. 检查路径是否相同

使用 `os.SameFile` 可以检查两个文件描述符是否引用同一个文件。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	file1, err := os.Stat("file1.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	file2, err := os.Stat("file2.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	if os.SameFile(file1, file2) {
		fmt.Println("file1.txt and file2.txt refer to the same file")
	} else {
		fmt.Println("file1.txt and file2.txt refer to different files")
	}
}
```

### 41. 临时设置环境变量

使用 `os.Setenv` 和 `os.Unsetenv` 可以临时设置和取消设置环境变量。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	os.Setenv("TEMP_VAR", "value")
	fmt.Println("TEMP_VAR:", os.Getenv("TEMP_VAR"))

	os.Unsetenv("TEMP_VAR")
	fmt.Println("TEMP_VAR after unset:", os.Getenv("TEMP_VAR"))
}
```

### 42. 通过文件名获取文件描述符

使用 `os.Open` 和 `File.Fd` 可以通过文件名获取文件描述符。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	file, err := os.Open("example.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer file.Close()

	fd := file.Fd()
	fmt.Println("File descriptor:", fd)
}
```

### 43. 设置进程的优先级（仅限 Windows）

在 Windows 上，可以使用 `syscall.Setpriority` 来设置进程的优先级。

```go
package main

import (
	"fmt"
	"os"
	"syscall"
)

func main() {
	err := syscall.Setpriority(syscall.PRIO_PROCESS, os.Getpid(), 20)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("Process priority changed")
}
```

### 44. 获取终端大小

使用 `golang.org/x/term` 包的 `GetSize` 可以获取终端的大小。

```go
package main

import (
	"fmt"
	"golang.org/x/term"
	"os"
)

func main() {
	fd := int(os.Stdout.Fd())
	width, height, err := term.GetSize(fd)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Printf("Terminal size: %dx%d\n", width, height)
}
```

### 45. 监听文件改变

使用 `fsnotify` 库可以监听文件或目录的更改。

```go
package main

import (
	"fmt"
	"github.com/fsnotify/fsnotify"
)

func main() {
	watcher, err := fsnotify.NewWatcher()
	if err != nil {
		fmt.Println(err)
		return
	}
	defer watcher.Close()

	err = watcher.Add("example.txt")
	if err != nil {
		fmt.Println(err)
		return
	}

	for {
		select {
		case event := <-watcher.Events:
			fmt.Println("Event:", event)
		case err := <-watcher.Errors:
			fmt.Println("Error:", err)
		}
	}
}
```

### 46. 获取当前进程的用户和组

可以通过 `os/user` 包来获取当前进程的用户和组信息。

```go
package main

import (
	"fmt"
	"os/user"
)

func main() {
	user, err := user.Current()
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("User ID:", user.Uid)
	fmt.Println("Group ID:", user.Gid)
	fmt.Println("Username:", user.Username)
	fmt.Println("Home Directory:", user.HomeDir)
}
```

### 47. 通过名称查找用户和组

使用 `os/user` 包的 `Lookup` 和 `LookupGroup` 函数，可以通过名称查找用户和组。

```go
package main

import (
	"fmt"
	"os/user"
)

func main() {
	user, err := user.Lookup("username")
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("User ID:", user.Uid)

	group, err := user.LookupGroup("groupname")
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("Group ID:", group.Gid)
}
```

### 48. 文件的读写偏移量

使用 `os.File` 的 `Seek` 方法可以改变文件的读写偏移量。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	file, err := os.OpenFile("example.txt", os.O_RDWR, 0666)
	if err != nil {
		fmt.Println(err)
		return
	}
	defer file.Close()

	offset, err := file.Seek(5, 0) // 从文件开始处偏移 5 个字节
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("Current offset:", offset)

	// 读或写操作
}
```

### 49. 创建有缓冲的文件读写器

使用 `bufio` 包，可以为文件读写创建缓冲。

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	file, err := os.OpenFile("example.txt", os.O_RDWR, 0666)
	if err != nil {
		fmt.Println(err)
		return
	}
	defer file.Close()

	reader := bufio.NewReader(file)
	writer := bufio.NewWriter(file)

	// 使用 reader 和 writer 进行读写操作
}
```

### 50. 获取文件的系统状态信息

使用 `os.FileInfo` 的 `Sys` 方法可以获取文件的系统依赖状态。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	fileInfo, err := os.Stat("example.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	sysInfo := fileInfo.Sys()
	fmt.Printf("System info: %+v\n", sysInfo)
	// 使用类型断言获取特定系统的信息
}
```

### 51. 使用 `os.Signal` 捕获更多信号

使用 `os/signal` 包可以捕获更多的系统信号。

```go
package main

import (
	"fmt"
	"os"
	"os/signal"
	"syscall"
)

func main() {
	signals := make(chan os.Signal, 1)
	signal.Notify(signals, syscall.SIGINT, syscall.SIGTERM)

	sig := <-signals
	fmt.Println("Received signal:", sig)
}
```

### 52. 判断文件路径是否是绝对路径

使用 `filepath.IsAbs` 可以判断文件路径是否是绝对路径。

```go
package main

import (
	"fmt"
	"path/filepath"
)

func main() {
	path := "/path/to/file.txt"
	if filepath.IsAbs(path) {
		fmt.Println("Absolute path")
	} else {
		fmt.Println("Relative path")
	}
}
```

### 53. 扩展环境变量

使用 `os.ExpandEnv` 可以在字符串中替换环境变量。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	s := "Home directory is $HOME"
	s = os.ExpandEnv(s)
	fmt.Println(s)
}
```

### 54. 创建设备文件（Unix-like 系统）

使用 `os.Mknod` 可以在 Unix-like 系统上创建设备文件。

```go
package main

import (
	"fmt"
	"os"
	"syscall"
)

func main() {
	err := os.Mknod("device", syscall.S_IFCHR|0666, int((14<<8)|1)) // 14 is the major number, 1 is the minor number
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("Device file created")
}
```

### 55. 检测一个错误是否由于被另一个进程中断引起

使用 `os.IsTimeout` 可以检测一个错误是否由于被另一个进程中断引起。

```go
package main

import (
	"fmt"
	"os"
	"time"
)

func main() {
	file, err := os.Open("example.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer file.Close()

	buffer := make([]byte, 10)
	file.SetDeadline(time.Now().Add(1 * time.Nanosecond)) // 设置非常短的超时时间

	_, err = file.Read(buffer)
	if os.IsTimeout(err) {
		fmt.Println("Read operation timed out")
	} else {
		fmt.Println("Read operation completed")
	}
}
```

### 56. 转换相对路径为绝对路径

使用 `filepath.Abs` 可以将相对路径转换为绝对路径。

```go
package main

import (
	"fmt"
	"path/filepath"
)

func main() {
	absPath, err := filepath.Abs("example.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println("Absolute path:", absPath)
}
```

### 57. 清理路径

使用 `filepath.Clean` 可以清理路径，消除多余的斜线、点和点点。

```go
package main

import (
	"fmt"
	"path/filepath"
)

func main() {
	path := filepath.Clean("/path//to/../../file.txt")
	fmt.Println("Cleaned path:", path)
}
```

### 58. 使用 os.Exec 替换当前进程映像

使用 `os.Exec` 可以替换当前进程映像。注意，这将结束当前程序，并以新的程序替换。

```go
package main

import (
	"os"
	"os/exec"
)

func main() {
	binary, err := exec.LookPath("ls")
	if err != nil {
		os.Stderr.WriteString(err.Error())
	}
	args := []string{"ls", "-l", "-h"}
	env := os.Environ()

	execErr := syscall.Exec(binary, args, env)
	if execErr != nil {
		os.Stderr.WriteString(execErr.Error())
	}
}
```

### 59. 读取目录内容

使用 `os.ReadDir` 可以读取目录的内容。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	files, err := os.ReadDir(".")
	if err != nil {
		fmt.Println(err)
		return
	}

	for _, file := range files {
		fmt.Println(file.Name())
	}
}
```

### 60. 使用 `TempDir` 和 `TempFile` 创建临时文件和目录

使用 `os.TempDir` 可以获取系统的临时目录，`os.CreateTemp` 和 `os.MkdirTemp` 可以创建临时文件和目录。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	tempDir := os.TempDir()
	fmt.Println("System temporary directory:", tempDir)

	file, err := os.CreateTemp("", "example.*.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer os.Remove(file.Name())
	fmt.Println("Temporary file created:", file.Name())

	dir, err := os.MkdirTemp("", "example")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer os.RemoveAll(dir)
	fmt.Println("Temporary directory created:", dir)
}
```












