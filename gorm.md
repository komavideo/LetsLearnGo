GORM 数据库开发
==============

## 官网

https://gorm.io/

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

## GORM 数据库开发

### 安装：

```bash
go get -u gorm.io/gorm
go get -u gorm.io/driver/sqlite
```

### 模型定义：

```go
package main

import (
    "gorm.io/gorm"
)

type User struct {
    ID    uint   `gorm:"primaryKey"`
    Name  string
    Age   int
    Email string `gorm:"uniqueIndex"`
}
```

### 初始化数据库连接：

```go
import (
    "gorm.io/gorm"
    "gorm.io/driver/sqlite"
)

func main() {
    db, err := gorm.Open(sqlite.Open("test.db"), &gorm.Config{})
    if err != nil {
        panic("failed to connect database")
    }

    // 迁移 schema
    db.AutoMigrate(&User{})
}
```

### CRUD 操作：

+ 创建记录：
```go
user := User{Name: "John", Age: 25, Email: "john@example.com"}
db.Create(&user)
```
+ 查询记录：
```go
// 通过 ID 查询
var user User
db.First(&user, 1) // 查找 ID 为 1 的用户

// 通过其他属性查询
db.Where("name = ?", "John").First(&user)
```
+ 更新记录：
```go
db.Model(&user).Update("Name", "Johnny")
```
+ 删除记录：
```go
db.Delete(&user)
```

### 关联 (Associations)：

假设你有一个 `Profile` 模型与 `User` 模型关联：

```go
type Profile struct {
    ID     uint
    UserID uint
    Bio    string
}

type User struct {
    gorm.Model
    Profile Profile
    Name    string
}
```

创建关联：
```go
user := User{
    Name: "John",
    Profile: Profile{Bio: "I am John"},
}
db.Create(&user)
```

查询关联：
```go
var user User
db.Preload("Profile").Find(&user)
```

### 高级查询：

+ IN 查询：
```go
db.Where("name IN ?", []string{"John", "Jane"}).Find(&users)
```
+ LIKE 查询：
```go
db.Where("name LIKE ?", "%jo%").Find(&users)
```
+ 排序和限制：
```go
db.Order("age desc").Limit(3).Find(&users)
```

### 使用 SQL 执行查询：

```go
db.Raw("SELECT name, age FROM users WHERE name = ?", "John").Scan(&user)
```

### 事务：

使用 `gorm` 进行事务操作时，你可以确保一系列操作都被成功完成，或在发生错误时所有操作都被回滚。

```go
func performTransaction(db *gorm.DB) error {
    tx := db.Begin()
    if tx.Error != nil {
        return tx.Error
    }

    // Suppose we want to create two users within a transaction
    if err := tx.Create(&User{Name: "John"}).Error; err != nil {
        tx.Rollback()
        return err
    }

    if err := tx.Create(&User{Name: "Jane"}).Error; err != nil {
        tx.Rollback()
        return err
    }

    return tx.Commit().Error
}
```

### Hooks（钩子）：

`gorm` 支持模型生命周期的钩子方法，例如，你可以在保存、更新或删除记录之前或之后执行特定操作。

```go
func (u *User) BeforeSave(tx *gorm.DB) (err error) {
    if u.Password != "" {
        u.Password = encrypt(u.Password)  // just a pseudocode for encryption
    }
    return
}
```

### Soft Delete：

`gorm` 支持软删除功能。被软删除的记录不会从数据库中真正被删除，而是设置了删除时间。

为了使用这个特性，你需要在模型中包含 `DeletedAt` 字段。

```go
type User struct {
    gorm.Model
    Name string
}
```

当你调用 `Delete` 方法时，记录只是被标记为删除。
```go
db.Delete(&user)
```

要查询不包括被软删除的记录：
```go
db.Find(&users)
```

要查询包括软删除的记录：
```go
db.Unscoped().Find(&users)
```

### 连接多个数据库：
你可以在同一个应用中连接多个数据库。只需为每个数据库创建一个新的 `gorm.DB` 实例即可。

```go
db1, err := gorm.Open(sqlite.Open("db1.sqlite"), &gorm.Config{})
db2, err := gorm.Open(sqlite.Open("db2.sqlite"), &gorm.Config{})
```

