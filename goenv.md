使用 goenv 管理本地 Go 语言版本
=============================

## Goenv

### Github

https://github.com/go-nv/goenv

### Homebrew

https://formulae.brew.sh/formula/goenv

### @Mac

```bash
# 更新 brew
brew update
# 安装 goenv
brew install goenv
# 更新终端环境变量
echo 'eval "$(goenv init -)"' >> ~/.zshrc
eval "$(goenv init -)"
# 确认安装
brew info goenv
# 确认版本
goenv -v
# 确认可以安装的Go语言版本
goenv install --list
# 确认本地已经安装的Go语言版本
goenv versions
# 安装 Go 1.20.7
goenv install 1.20.7
# 确认本地已经安装的Go语言版本
goenv versions
# 使用 Go 1.20.7
goenv global 1.20.7
# 确认使用的版本
goenv versions
* 1.20.7 (set by /Users/xiaoma/.goenv/version)
# 确认使用中的Go语言的版本
go version
```

Done.