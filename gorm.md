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

### 关联查询：

当你有关联模型时，你可能希望一次查询中获取关联的数据。

+ Preloading（预加载）：
当你要检索关联的数据时，可以使用预加载。
```go
var users []User
db.Preload("Profile").Find(&users)
```
+ Joins（联接）：
你也可以使用 `Joins` 来编写自定义的连接查询。
```go
var result []struct {
    UserName string
    Bio      string
}
db.Table("users").Select("users.name, profiles.bio").Joins("left join profiles on profiles.user_id = users.id").Scan(&result)
```

### 错误处理：

`gorm` 的每个查询方法都会返回一个错误，你应该始终检查它。

```go
result := db.Find(&users)
if result.Error != nil {
    // handle error
}
```

### 自定义数据类型：

有时你可能需要将 `Go` 中的某个数据类型映射到数据库中的特定数据类型。`gorm` 允许你创建自定义数据类型来实现这一点。

```go
type Status int

const (
    Inactive Status = iota
    Active
)

func (s *Status) Scan(value interface{}) error {
    // convert database value to your custom type
}

func (s Status) Value() (driver.Value, error) {
    // convert your custom type back to database type
}

type User struct {
    gorm.Model
    State Status
}
```

### 使用复合主键：

`gorm` 也支持复合主键。

```go
type Subscription struct {
    UserID   uint `gorm:"primaryKey"`
    CourseID uint `gorm:"primaryKey"`
    Expires  time.Time
}
```

### Scopes（作用域）：

作用域允许你定义常用的查询，然后在需要时应用它们。

```go
func ActiveUsers(db *gorm.DB) *gorm.DB {
    return db.Where("state = ?", Active)
}

db.Scopes(ActiveUsers).Find(&users)
```

### 日志记录：

`gorm` 提供了一个日志记录器，你可以使用它来查看或自定义查询日志。

```go
newLogger := logger.New(
   log.New(os.Stdout, "\r\n", log.LstdFlags),
   logger.Config{
      SlowThreshold: time.Second,
      LogLevel:      logger.Info,
      Colorful:      true,
   },
)

db, err := gorm.Open(sqlite.Open("test.db"), &gorm.Config{
   Logger: newLogger,
})
```

