将GORM集成到Clean Architecture架构
=================================

```go
// domain/entity/user.go
package entity

type User struct {
    ID      uint
    Name    string
    Email   string
    Balance float64
}

type Account struct {
    ID      uint
    UserID  uint
    Balance float64
}

// domain/repository/repository.go
package repository

import "context"

type UserRepository interface {
    CreateUser(ctx context.Context, user *entity.User) error
    GetUserByID(ctx context.Context, id uint) (*entity.User, error)
}

type AccountRepository interface {
    CreateAccount(ctx context.Context, account *entity.Account) error
    UpdateBalance(ctx context.Context, userID uint, amount float64) error
}

// domain/repository/transaction.go
package repository

import "context"

type TxManager interface {
    WithinTransaction(ctx context.Context, fn func(txCtx context.Context) error) error
}

// usecase/user_usecase.go
package usecase

type UserUseCase struct {
    userRepo    repository.UserRepository
    accountRepo repository.AccountRepository
    txManager   repository.TxManager
}

func NewUserUseCase(ur repository.UserRepository, ar repository.AccountRepository, tm repository.TxManager) *UserUseCase {
    return &UserUseCase{
        userRepo:    ur,
        accountRepo: ar,
        txManager:   tm,
    }
}

// CreateUserWithAccount 在事务中创建用户和账户
func (uc *UserUseCase) CreateUserWithAccount(ctx context.Context, user *entity.User, initialBalance float64) error {
    return uc.txManager.WithinTransaction(ctx, func(txCtx context.Context) error {
        // 创建用户
        if err := uc.userRepo.CreateUser(txCtx, user); err != nil {
            return err
        }

        // 创建账户
        account := &entity.Account{
            UserID:  user.ID,
            Balance: initialBalance,
        }
        if err := uc.accountRepo.CreateAccount(txCtx, account); err != nil {
            return err
        }

        return nil
    })
}

// infrastructure/persistence/gorm_transaction.go
package persistence

import (
    "context"
    "gorm.io/gorm"
)

type GormTxManager struct {
    db *gorm.DB
}

func NewGormTxManager(db *gorm.DB) *GormTxManager {
    return &GormTxManager{db: db}
}

// WithinTransaction 实现事务管理接口
func (tm *GormTxManager) WithinTransaction(ctx context.Context, fn func(txCtx context.Context) error) error {
    return tm.db.WithContext(ctx).Transaction(func(tx *gorm.DB) error {
        // 创建新的上下文，包含事务信息
        txCtx := context.WithValue(ctx, "tx", tx)
        return fn(txCtx)
    })
}

// infrastructure/persistence/gorm_user_repository.go
package persistence

import (
    "context"
    "gorm.io/gorm"
)

type gormModel struct {
    ID        uint `gorm:"primarykey"`
    Name      string
    Email     string
    Balance   float64
}

type GormUserRepository struct {
    db *gorm.DB
}

func NewGormUserRepository(db *gorm.DB) *GormUserRepository {
    return &GormUserRepository{db: db}
}

func (r *GormUserRepository) CreateUser(ctx context.Context, user *entity.User) error {
    db := r.getDBFromContext(ctx)
    
    model := &gormModel{
        Name:    user.Name,
        Email:   user.Email,
        Balance: user.Balance,
    }
    
    result := db.Create(model)
    if result.Error != nil {
        return result.Error
    }
    
    user.ID = model.ID
    return nil
}

// getDBFromContext 从上下文获取事务或默认DB
func (r *GormUserRepository) getDBFromContext(ctx context.Context) *gorm.DB {
    if tx, ok := ctx.Value("tx").(*gorm.DB); ok {
        return tx
    }
    return r.db.WithContext(ctx)
}

// infrastructure/persistence/gorm_account_repository.go
package persistence

type GormAccountRepository struct {
    db *gorm.DB
}

func NewGormAccountRepository(db *gorm.DB) *GormAccountRepository {
    return &GormAccountRepository{db: db}
}

func (r *GormAccountRepository) CreateAccount(ctx context.Context, account *entity.Account) error {
    db := r.getDBFromContext(ctx)
    
    result := db.Create(account)
    return result.Error
}

func (r *GormAccountRepository) UpdateBalance(ctx context.Context, userID uint, amount float64) error {
    db := r.getDBFromContext(ctx)
    
    result := db.Model(&entity.Account{}).
        Where("user_id = ?", userID).
        Update("balance", amount)
    return result.Error
}

// 和UserRepository一样的getDBFromContext方法
func (r *GormAccountRepository) getDBFromContext(ctx context.Context) *gorm.DB {
    if tx, ok := ctx.Value("tx").(*gorm.DB); ok {
        return tx
    }
    return r.db.WithContext(ctx)
}

// interfaces/http/handler/user_handler.go
package handler

type UserHandler struct {
    userUseCase *usecase.UserUseCase
}

func NewUserHandler(uc *usecase.UserUseCase) *UserHandler {
    return &UserHandler{userUseCase: uc}
}

func (h *UserHandler) CreateUser(c *gin.Context) {
    var request struct {
        Name           string  `json:"name"`
        Email          string  `json:"email"`
        InitialBalance float64 `json:"initial_balance"`
    }

    if err := c.ShouldBindJSON(&request); err != nil {
        c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
        return
    }

    user := &entity.User{
        Name:  request.Name,
        Email: request.Email,
    }

    err := h.userUseCase.CreateUserWithAccount(c.Request.Context(), user, request.InitialBalance)
    if err != nil {
        c.JSON(http.StatusInternalServerError, gin.H{"error": err.Error()})
        return
    }

    c.JSON(http.StatusCreated, user)
}

// cmd/main.go
func main() {
    // 初始化数据库连接
    db := initDB()

    // 初始化repositories
    txManager := persistence.NewGormTxManager(db)
    userRepo := persistence.NewGormUserRepository(db)
    accountRepo := persistence.NewGormAccountRepository(db)

    // 初始化use cases
    userUseCase := usecase.NewUserUseCase(userRepo, accountRepo, txManager)

    // 初始化handlers
    userHandler := handler.NewUserHandler(userUseCase)

    // 设置路由等...
}
```
