Cobra命令行工具开发
=================

Cobra是一个用于构建强大的命令行应用程序的Go语言库。它提供了一种简单而优雅的方式来定义命令行接口，并支持子命令、标志、参数等功能。Cobra库由Spf13开发，并且在Go社区中被广泛使用。

## 网站

### Cobra
https://echo.labstack.com/

### Cobra Generator
https://github.com/spf13/cobra-cli

## 创建新项目

```bash
# 安装Cobra Generator
go install github.com/spf13/cobra-cli@latest

# 确认安装
cobra-cli help

# 建立命令行工程
mkdir goapp
cd goapp
go mod init goapp

# 建立cobra初始化文件
cat <<EOF >~/.cobra.yaml
author: Koma
license: MIT
useViper: true
EOF

# 初始化命令行工具工程
cobra-cli init
# 确认工程
tree
.
├── LICENSE
├── cmd
│   └── root.go # 默认命令处理
├── go.mod
├── go.sum
└── main.go # 程序入口

# 编译
go build
# 运行
./goapp
```

## 加入子命令

```bash
# 添加serve子命令
cobra-cli add serve

# 确认生成
tree               
.
├── LICENSE
├── cmd
│   ├── root.go
│   └── serve.go #<-新生成的命令
├── go.mod
├── go.sum
└── main.go

# 编译确认
go build && ./goapp
...
Usage:
  goapp [command]

Available Commands:
  completion  Generate the autocompletion script for the specified shell
  help        Help about any command
  serve       A brief description of your command # 新生成的serve命令摘要

Use "goapp [command] --help" for more information about a command.
...

# 执行命令
./goapp serve
./goapp help serve
```

## 加入子命令的子命令

```bash
# 添加config子命令
cobra-cli add config

# 添加config子命令的子命令
cobra-cli add http -p 'configCmd'

# 确认生成
tree        
.
├── LICENSE
├── cmd
│   ├── config.go
│   ├── http.go
│   ├── root.go
│   └── serve.go
├── go.mod
├── go.sum
└── main.go

# 编译确认
go build && ./goapp
...
Usage:
  goapp [command]

Available Commands:
  completion  Generate the autocompletion script for the specified shell
  config      A brief description of your command # 新生成的config命令摘要
  help        Help about any command
  serve       A brief description of your command

Use "goapp [command] --help" for more information about a command.
...


# 执行命令
./goapp config
./goapp help config
./goapp config http
./goapp help config http
```

Done.
