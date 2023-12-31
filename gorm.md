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

### 回调：

`gorm` 允许你注册在执行数据库操作之前和之后的回调方法。你可以在创建、更新、查询或删除操作之前或之后使用它们。

+ 创建记录之前的回调：
```go
func (u *User) BeforeCreate(tx *gorm.DB) (err error) {
    // 你可以在这里添加一些在创建记录之前要执行的操作
    return
}
```
+ 创建记录之后的回调：
```go
func (u *User) AfterCreate(tx *gorm.DB) (err error) {
    // 你可以在这里添加一些在创建记录之后要执行的操作
    return
}
```

同样，你可以对更新、删除和查询操作使用 `BeforeUpdate`, `AfterUpdate`, `BeforeDelete`, `AfterDelete`, `BeforeSave`, `AfterSave`, `BeforeFind`, 和 `AfterFind`。

### 复杂的 SQL 构建：

使用 `gorm.Expr` 可以帮助你构建更复杂的 SQL 表达式：

```go
db.Model(&user).Update("age", gorm.Expr("age + ?", 1))  // increment age by 1
```

### 使用 Raw SQL 插入：

有时你可能只需要执行原始 `SQL`。`gorm` 提供了这个功能：

```go
db.Exec("INSERT INTO users (name) VALUES (?)", "John")
```

### 自定义字段名称：

默认情况下，`gorm` 会将结构字段转换为下划线格式作为数据库的字段名称。但是，你可以使用 `column` 标签自定义字段名称：

```go
type User struct {
    gorm.Model
    FirstName string `gorm:"column:given_name"`
    LastName  string `gorm:"column:surname"`
}
```

### Count 和 Group：

你可以轻松地获取结果集的数量或使用 `GROUP BY`：

```go
var count int64
db.Model(&User{}).Where("age > ?", 20).Count(&count)

var result []struct {
    Age  int
    Count int64
}
db.Model(&User{}).Select("age, count(*) as count").Group("age").Find(&result)
```

### 锁定：

在执行某些查询时，你可能需要锁定，例如，为了防止其他数据库事务修改数据：

```go
db.Clauses(clause.Locking{Strength: "UPDATE"}).Find(&users)
```

### 支持其他数据库：

尽管我之前展示的是 `SQLite`，但 `gorm` 支持多种数据库。你只需更改驱动程序并相应地配置连接。例如，为了使用 `PostgreSQL`：

```go
go get -u gorm.io/driver/postgres

// 连接
dsn := "host=localhost user=gorm password=gorm dbname=gorm port=9920 sslmode=disable TimeZone=Asia/Shanghai"
db, err := gorm.Open(postgres.Open(dsn), &gorm.Config{})
```

### 数据库迁移：

你可以使用 `AutoMigrate` 来自动执行数据库迁移：

```go
db.AutoMigrate(&User{}, &Product{})
```
注意: `AutoMigrate` 只会创建表、缺失的列和索引，并不会删除/更改现有的列和索引。

### 批量插入：

如果你想一次性插入多条记录，可以使用 gorm 的批量插入功能。
```go
users := []User{{Name: "user1"}, {Name: "user2"}, {Name: "user3"}}
db.Create(&users)
```

### FirstOrInit & FirstOrCreate：

这两个函数在查询时非常有用。

+ FirstOrInit：查找符合条件的第一条记录，如果没有找到，就初始化一个符合条件的新对象（但不保存到数据库）。
```go
user := User{Name: "NonExistingUser"}
db.FirstOrInit(&user)
```
+ FirstOrCreate：查找符合条件的第一条记录，如果没有找到，就创建一个。
```go
user := User{Name: "NewUser"}
db.FirstOrCreate(&user)
```

### 更新多个字段：

可以同时更新多个字段。

```go
db.Model(&user).Updates(User{Name: "new_name", Age: 20})  // updates both name and age
```

或者使用 map：
```go
db.Model(&user).Updates(map[string]interface{}{"Name": "new_name", "Age": 21})
```

### 不更新某些字段：

使用 `Omit` 函数，你可以在保存数据时跳过某些字段。

```go
db.Model(&user).Omit("Age").Updates(map[string]interface{}{"Name": "new_name", "Age": 21})
```
在这个例子中，尽管更新 map 中有 Age，但由于使用了 Omit，Age 不会被更新。

### 查询链：

你可以串联多个查询方法来构建更复杂的查询。

```go
db.Where("name LIKE ?", "John%").Or("name LIKE ?", "Bob%").Not("age = ?", 25).Order("created_at desc").Find(&users)
```

### 自定义主键：

默认情况下，`gorm` 使用字段名为 `ID` 的字段作为主键。但你可以自定义主键。

```go
type User struct {
    UserID   int    `gorm:"primaryKey"`
    Username string
}
```

### 表名约定：

`gorm` 默认使用结构体的复数形式作为表名。但你可以自定义表名。

```go
func (User) TableName() string {
    return "my_users"
}
```

### 关联的外键和引用键：

当定义关联模型时，你可以明确指定外键和引用键。

```go
type Profile struct {
    ID     int
    UserID int
    User   User `gorm:"foreignKey:UserID;references:ID"`
}
```

### 联合唯一索引：

使用 `gorm`，你可以定义多列的联合唯一索引。

```go
type User struct {
    gorm.Model
    FirstName string
    LastName  string
    Email     string `gorm:"uniqueIndex:idx_email"`
}

type CalendarEvent struct {
    gorm.Model
    UserID   uint `gorm:"uniqueIndex:idx_user_date"`
    EventDate string `gorm:"uniqueIndex:idx_user_date"`
}
```

### 使用 SQL 表达式：

有时，你可能想在查询中使用原生的 `SQL` 表达式。使用 `gorm.Expr` 可以达到这个目的。

```go
db.Model(&product).Update("price", gorm.Expr("price * ?", 1.1)) // 将价格提高10%
```

### 删除与软删除：

+ 当你在模型中定义一个 `DeletedAt` 字段时，它将自动启用软删除功能。
```go
type User struct {
    gorm.Model
    Name string
}
```
+ 执行删除操作时，记录不会真正被删除，而是 DeletedAt 字段会被设置为当前时间。
```go
db.Delete(&user)
```
+ 要永久删除记录，请使用 Unscoped。
```go
db.Unscoped().Delete(&user)
```

### 执行原生 SQL 查询：

如果你需要执行一些不能通过 `gorm API` 表达的复杂查询，可以使用 `Raw` 方法。

```go
db.Raw("SELECT name FROM users WHERE age = ?", 20).Scan(&users)
```

### 开启与关闭连接池：

`gorm` 默认为你提供了一个数据库连接池，你可以根据需要配置这个连接池。

```go
sqlDB, err := db.DB()

// 设置最大的连接数量
sqlDB.SetMaxOpenConns(100)

// 设置最大的空闲连接数量
sqlDB.SetMaxIdleConns(10)

// 设置每个连接的生命周期
sqlDB.SetConnMaxLifetime(time.Hour)
```
当不再需要数据库连接时，确保关闭连接池：
```go
sqlDB.Close()
```

### 使用事务：

在某些情况下，你可能希望执行一系列的数据库操作，这些操作要么全部成功，要么全部失败。在这种情况下，你可以使用事务。

```go
err := db.Transaction(func(tx *gorm.DB) error {
    if err := tx.Create(&user).Error; err != nil {
        return err
    }

    if err := tx.Create(&profile).Error; err != nil {
        return err
    }

    return nil
})

if err != nil {
    // 事务失败
}
```

### 设置全局作用域：

如果你希望为每个查询都应用某个作用域，你可以设置一个全局作用域。

```go
db = db.Scopes(func(db *gorm.DB) *gorm.DB {
    return db.Where("active = ?", true)
})

db.Find(&users) // 每次查询都会自动应用上面的作用域
```

### 启用和关闭日志记录：

`gorm` 为你提供了很好的日志记录功能。如果你希望关闭日志记录，可以这样做：

```go
newLogger := logger.New(
   log.New(os.Stdout, "\r\n", log.LstdFlags),
   logger.Config{
      LogLevel: logger.Silent,
   },
)
db, _ := gorm.Open(sqlite.Open("test.db"), &gorm.Config{Logger: newLogger})
```

### 使用钩子：

你可以使用钩子（Hooks）来在数据库操作之前或之后执行特定的操作。

```go
func (u *User) BeforeSave(tx *gorm.DB) (err error) {
    fmt.Println("Before saving a user...")
    return
}

func (u *User) AfterSave(tx *gorm.DB) (err error) {
    fmt.Println("After saving a user...")
    return
}
```
类似的钩子还有：`BeforeCreate`, `AfterCreate`, `BeforeUpdate`, `AfterUpdate`, `BeforeDelete`, `AfterDelete`, `BeforeFind` 和 `AfterFind`。

### Join操作：

`gorm` 提供了方便的方法来处理数据库中的 `JOIN` 操作。

```go
type Result struct {
    Name     string
    Age      int
    Profile  Profile
}

var result Result
db.Joins("JOIN profiles ON profiles.user_id = users.id").Where("users.id = ?", 1).First(&result)
```

### Preload（预加载）：

如果你有关联的模型，并且想一次性加载它们，可以使用 `Preload`。

```go
db.Preload("Profile").Find(&users)  // 会加载用户的Profile
```

### 使用指针与非指针：

`gorm` 允许你使用指针或非指针，但它们的行为略有不同。

+ 使用非指针，如果记录不存在，`gorm` 不会返回错误。
+ 使用指针，如果记录不存在，`gorm` 会返回 `ErrRecordNotFound` 错误。

```go
var user User
err := db.First(&user, 10).Error  // 不会返回 ErrRecordNotFound
```

```go
user := &User{}
err := db.First(user, 10).Error   // 会返回 ErrRecordNotFound 如果找不到
```

### 使用Select选择特定的字段：

如果你不想加载所有字段，可以使用 `Select` 来选择你关心的字段。

```go
var names []string
db.Model(&User{}).Select("name").Find(&names)
```

### 使用 Clauses 来应用复杂的SQL子句：

```go
db.Clauses(clause.OnConflict{
    UpdateAll: true,
}).Create(&users)
```

### 使用 Scan 直接将查询结果映射到另一个结构：

这对于只需要部分数据的查询特别有用。

```go
type UserDTO struct {
    Name string
    Age  int
}

var dto UserDTO
db.Model(&User{}).Where("id = ?", 1).Scan(&dto)
```

### 为模型定义别名：

如果你需要在查询中为模型定义别名，可以使用 `Table` 方法。

```go
db.Table("users AS u").Select("u.name").Joins("JOIN profiles AS p ON p.user_id = u.id").Where("p.name = ?", "jinzhu").Find(&users)
```

### 启用/禁用跟踪创建/更新时间：

`gorm` 默认为你提供了 `created_at` 和 `updated_at` 时间戳。但如果你想禁用这一功能，可以设置 `SkipDefaultTimestamp`。

```go
db.Set("gorm:skipDefaultTimestamp", true).Create(&user)
```

### 使用FirstOrInit和Attrs方法：

`FirstOrInit` 会查找符合条件的第一条记录，如果找不到，它会为你初始化一个新的对象。而 `Attrs` 可以为这个新对象提供默认值。

```go
user := User{Name: "NonExistent"}
db.Where(&user).Attrs(User{Age: 20}).FirstOrInit(&user)
// 如果不存在名为 "NonExistent" 的用户，user 的 Age 会被设置为 20
```

### 使用Assign方法：

`Assign` 方法会始终将提供的参数分配给模型，无论记录是否找到。

```go
user := User{Name: "Jinzhu"}
db.Where(&user).Assign(User{Age: 30}).FirstOrCreate(&user)
```
如果已经存在名为 "Jinzhu" 的用户，那么这个用户的年龄将不会被改变，但如果不存在，一个名为 "Jinzhu" 且年龄为 30 的用户会被创建。

### 使用Count方法获取结果数：

你可以使用 `Count` 方法来获取满足查询条件的结果数。

```go
var count int64
db.Model(&User{}).Where("name LIKE ?", "jin%").Count(&count)
```

### 使用Not方法排除条件：

`Not` 方法可以用来排除某些条件。

```go
// 获取名字不是 "jinzhu" 的所有用户
db.Not("name", "jinzhu").Find(&users)
```

### 使用Distinct选择唯一字段：

`Distinct` 可以帮助你从结果中选择唯一字段。

```go
var ages []int
db.Model(&User{}).Select("distinct(age)").Find(&ages)
```

### 使用Group和Having进行分组查询：

你可以使用 `Group` 和 `Having` 方法来进行分组查询。

```go
var result []struct {
    Age  int
    Count int
}
db.Model(&User{}).Select("age, count(*) as count").Group("age").Having("count > ?", 3).Find(&result)
```

### 使用Related加载关联数据：

假设你有一个 `Profile` 模型与 `User` 模型关联，可以使用 `Related` 方法来加载用户的资料。

```go
var user User
db.First(&user)
db.Model(&user).Related(&user.Profile)
```
注意：从 `gorm v2` 开始，推荐使用 `Preload` 代替 `Related`。

### 设置连接的字符集和时区：

在连接数据库时，你可能需要设置字符集或时区。

```go
dsn := "gorm:gorm@tcp(localhost:3306)/gorm?charset=utf8mb4&parseTime=True&loc=Local"
db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
```

### 使用Scan在查询时使用不同的结构：

有时你可能只需要数据库表的一个子集。在这种情况下，你可以定义一个新的结构体，然后使用 `Scan` 方法将查询结果映射到这个结构体。

```go
type UserName struct {
    Name string
}

var names []UserName
db.Model(&User{}).Scan(&names)
```

### 监听数据库的SQL查询：

你可以通过 `gorm.DB` 的 `Callback` 属性监听数据库查询。

```go
db.Callback().Query().Register("my_callback", func(scope *gorm.Scope) {
    fmt.Println("SQL:", scope.SQL)
})
```

### 手动执行SQL：

尽管`gorm`是一个`ORM`，但有时你可能想要手动执行`SQL`。`gorm` 提供了这个功能。

```go
var users []User
db.Raw("SELECT name, age FROM users WHERE name = ?", "jinzhu").Scan(&users)
```

### 数据库迁移：

使用`gorm`，你可以轻松地迁移你的数据库结构。

```go
// 自动迁移
db.AutoMigrate(&User{}, &Product{})

// 删除模型的表
db.Migrator().DropTable(&User{})
```

### 使用 Pluck 提取一个列的值：

如果你只想获取查询结果的一列，并且想将这列的值存放在一个切片中，你可以使用 `Pluck`。

```go
var ages []int64
db.Model(&User{}).Pluck("age", &ages)
```

### 使用 Clauses 应用子句：

有时，你可能想在查询中添加特定的子句。`Clauses` 允许你做这件事。

```go
tx := db.Clauses(clause.Locking{Strength: "UPDATE"}).Find(&users)
```

### 处理事务：

使用 `gorm`，你可以容易地管理事务。

```go
err := db.Transaction(func(tx *gorm.DB) error {
    if err := tx.Create(&User{Name: "Gin"}).Error; err != nil {
        return err
    }
    if err := tx.Create(&User{Name: "Tonic"}).Error; err != nil {
        return err
    }
    return nil
})
```

### 使用 Save 更新记录：

`Save` 方法会更新模型的所有字段，无论它们是否更改。

```go
user := User{Name: "Gin", Age: 27}
db.Save(&user)
```

### 使用 Updates 方法部分更新：

如果你只想更新模型的某些字段，你可以使用 `Updates`。

```go
db.Model(&user).Updates(User{Name: "Tonic", Age: 28})  // 不会更新空值字段
```

### 使用 UpdateColumn 直接更新：

`UpdateColumn` 会直接更新这个值，忽略 `gorm` 的回调和 `UpdatedAt` 时间戳字段。

```go
db.Model(&user).UpdateColumn("name", "Vodka")
```

### 定义和使用复合主键：

有时你可能需要使用复合主键。`gorm` 通过 `gorm:"primaryKey"` 标签来支持这一功能。

```go
type Subscription struct {
    UserID    int `gorm:"primaryKey"`
    CourseID  int `gorm:"primaryKey"`
}
```

### 连接其他数据库：

尽管我们之前看到的示例是基于`MySQL`的，但`gorm`支持多种数据库，例如`PostgreSQL`, `SQLite`, and `Microsoft SQL Server`。只需要改变连接字符串和驱动即可。

### 使用Scopes方法自定义查询：

`Scopes` 可以将常用的查询逻辑提取为方法，这样你可以轻松地重用它们。

```go
func ActiveUsers(db *gorm.DB) *gorm.DB {
    return db.Where("active = ?", true)
}

db.Scopes(ActiveUsers).Find(&users)
```

### 使用Set方法在当前db实例上设置参数：

你可以使用 `Set` 方法来存储和检索上下文数据，这对于中间件非常有用。

```go
db = db.Set("gorm:table_options", "CHARSET=utf8mb4")
db.CreateTable(&users)
```

### 使用Joins进行连接查询：

`Joins` 方法允许你执行连接查询。

```go
type Result struct {
    UserName string
    Email    string
}

var results []Result
db.Table("users").Select("users.name, emails.email").Joins("left join emails on emails.user_id = users.id").Scan(&results)
```

### 使用Debug打印SQL查询：

你可以通过在查询链中添加`Debug`方法来打印出实际执行的`SQL`语句。

```go
db.Debug().Where("name = ?", "jinzhu").First(&User{})
```

### 设置gorm的日志模式：

`gorm`默认不显示`SQL`日志，但你可以通过设置其日志模式来启用它。

```go
newLogger := logger.New(
    log.New(os.Stdout, "\r\n", log.LstdFlags), // io writer
    logger.Config{
        SlowThreshold: time.Second,   // 慢 SQL 阈值
        LogLevel:      logger.Info,  // 日志级别
        Colorful:      true,         // 禁用彩色打印
    },
)

db, _ := gorm.Open(sqlite.Open("gorm.db"), &gorm.Config{
    Logger: newLogger,
})
```

### 使用Take获取一条记录：

与`First`方法类似，`Take`也返回匹配的第一条记录，但它不接受任何排序。

```go
db.Take(&user)
```

### 使用Last获取最后一条记录：

你可以使用`Last`方法获取最后一条记录。

```go
db.Last(&user)
```

### 使用Omit在保存记录时排除字段：

如果你想在保存模型时跳过某些字段，可以使用`Omit`。

```go
db.Omit("CreatedAt").Create(&user)
```

### 使用Select指定查询的字段：

如果你只关心表中的某些字段，可以使用`Select`来限制查询的字段。

```go
db.Select("name", "age").Find(&users)
```

### 使用Clauses应用SQL子句：

`Clauses`方法允许你添加原始`SQL`子句。

```go
db.Clauses(clause.OnConflict{UpdateAll: true}).Create(&user)
```

### 处理软删除：

默认情况下，当你调用`Delete`方法时，记录不会从数据库中删除，而是更新其`DeletedAt`字段的值。这被称为软删除。被软删除的记录不会出现在`Find`的查询结果中。

```go
// 为模型启用软删除，只需要在模型中包含 DeletedAt 字段
type User struct {
    gorm.Model
    Name string
}

// 删除记录
db.Delete(&user)

// 使用 Unscoped 方法检索被软删除的记录
db.Unscoped().Find(&users)
```

### 模型的字段和表命名约定：

默认情况下，`gorm` 会将结构体名称的复数形式作为表名，例如`User`模型对应的表名是`users`。结构体字段名称首字母大写的形式作为数据库列名，例如`UserName`对应的列名是`user_name`。

如果想指定自定义的表名或列名，可以使用标签。

```go
type User struct {
    ID   uint   `gorm:"column:my_id"`
    Name string `gorm:"column:my_name"`
}

func (User) TableName() string {
    return "my_users"
}
```

### 创建表时指定选项：

你可以在创建表时设置特定的选项，例如字符集或排序规则。

```go
db.Set("gorm:table_options", "ENGINE=InnoDB").AutoMigrate(&User{})
```

### 获取主键：

使用`InstanceSet`可以检索模型的主键信息。

```go
primaryKey := db.InstanceSet("gorm:primary_key", primaryKey)
```

### 使用指针或值接收查询结果：

在查询结果时，可以使用结构体的指针或值。使用指针是最常见的，因为这样可以确保在没有找到记录时结构体不会被填充。

```go
var u User
db.First(&u, 1)  // 使用指针

var u2 User
db.First(u2, 1)  // 使用值，不推荐这样做
```

### 使用Count获取记录数量：

你可以使用`Count`方法来获取查询的记录数量。

```go
var count int64
db.Model(&User{}).Where("name = ?", "jinzhu").Count(&count)
```

### 使用Group和Having进行分组查询：

`gorm` 支持分组查询和`HAVING`子句。

```go
rows, err := db.Table("orders").Select("date(created_at) as date, sum(amount) as total").Group("date(created_at)").Having("sum(amount) > ?", 100).Rows()
```

### 使用Not进行否定查询：

`Not` 方法允许你执行否定查询。

```go
db.Not("name = ?", "jinzhu").Find(&users)
```

### 使用Or进行联合查询：

`Or` 方法允许你组合多个条件。

```go
db.Where("name = ?", "jinzhu").Or("name = ?", "jinzha").Find(&users)
```

### 使用Column指定更新的列：

`Update` 方法默认会更新所有列，但你可以使用`Column`方法指定只更新某些列。

```go
db.Model(&user).Select("Name", "Age").Updates(map[string]interface{}{"Name": "jinzhu", "Age": 30})
```

### 关联记录的查询：

`gorm` 提供了一种简洁的方式来查询和操作模型的关联。

```go
type User struct {
    ID   uint
    Name string
    Orders []Order
}

type Order struct {
    ID     uint
    UserID uint
    Total  float64
}

var user User
db.Preload("Orders").Find(&user, "id = ?", 1)
```
使用`Preload`会自动加载用户的所有订单。

### 关联记录的创建：

当创建记录时，也可以同时创建其关联的记录。

```go
user := User{
    Name: "Jinzhu",
    Orders: []Order{
        {Total: 100.0},
        {Total: 200.0},
    },
}

db.Create(&user)
```

### 使用Related查询关联数据：

可以使用 `Related` 方法来查询关联的数据。

```go
var orders []Order
db.Model(&user).Related(&orders)
```

### 使用FirstOrInit和FirstOrCreate：

`FirstOrInit` 会返回匹配的第一条记录，或返回一个新的（未保存的）记录。而 `FirstOrCreate` 会返回匹配的第一条记录，或创建一个新的记录。

```go
user := User{Name: "non_existing"}
db.FirstOrInit(&user)
db.FirstOrCreate(&user)
```

### Raw和Exec执行原始SQL：

你可以使用 `Raw` 来执行原始的`SQL`查询，而 `Exec` 用于执行没有返回值的`SQL`语句。

```go
var result string
db.Raw("SELECT name FROM users WHERE id = ?", 1).Scan(&result)

db.Exec("UPDATE users SET name = ? WHERE id = ?", "jinzhu", 1)
```

### 使用Set存储内部数据：

你可以使用 `Set` 方法来存储一次性的设置。

```go
db.Set("gorm:association_autoupdate", false).Save(&user)
```

### 使用Begin和Commit进行事务操作：

`gorm` 支持常见的事务操作。

```go
tx := db.Begin()
defer func() {
    if r := recover(); r != nil {
        tx.Rollback()
    }
}()

if err := tx.Create(&user).Error; err != nil {
    tx.Rollback()
    return
}

tx.Commit()
```

### 使用Association处理关联：

`Association` 是一个辅助方法，允许你进行关联数据的各种操作。

```go
user := User{ID: 1}
db.Model(&user).Association("Orders").Append([]Order{{Total: 100.0}, {Total: 200.0}})
```

### 使用Attrs和Assign为对象设置属性：

使用 `Attrs` 和 `Assign` 可以为模型设置属性。

```go
user := User{Name: "Jinzhu"}
db.FirstOrCreate(&user, User{Name: "non_existing"}).Attrs(User{Age: 20}).FirstOrCreate(&user)
```

### 错误处理：
使用 `Error` 属性来获取数据库操作的错误信息。

```go
result := db.First(&user)
if result.Error != nil {
    fmt.Println(result.Error)
}
```

Done.
