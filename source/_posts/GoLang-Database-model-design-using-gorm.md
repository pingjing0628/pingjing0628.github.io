---
title: GoLang-Database model design using gorm
date: 2020-02-27 09:35:17
tags: 
  - GoLang
  - Gorm
  - SQL
  - Database
categories: 
  - GoLang
---

## DB Setting

<!--more-->
---

### DB Connection

將連接sql db連線設定置於 `.env`

Folder `database/database.go`

```golang=

// Create variables
var (
    config map[string]string
    db     *gorm.DB
)

// Create connect to database
func Connect(db *gorm.DB) *gorm.DB {
    config, err := godotenv.Read()

    if err != nil {
        log.Fatal("Error reading .env file")
    }

    credentials := fmt.Sprintf(
        "%s:%s@(%s)/%s?charset=utf8mb4&parseTime=True&loc=Local",
        config["DB_USERNAME"],
        config["DB_PASSWORD"],
        config["DATABASE_HOST"],
        config["DB_DATABASE"],
    )

    connect, err := gorm.Open("mysql", credentials)

    if err != nil {
        log.Fatal("Connect tp DB failed", err)
    }

    return connect

}

```

### DB table schema
Folder `model/model.go`

```golang=
// A user has many products
type User struct {
    // gorm.Model
    Id        int       `gorm:"primary_key;AUTO_INCREMENT"`
    Account   string    `gorm:"type:varchar(30);" json:"account"`
    Name      string    `gorm:"type:varchar(20);" json:"name"`
    Password  string    `gorm:"type:longtext;" json:"password"`
    Phone     string    `gorm:"type:varchar(10);" json:"phone"`
    Products  []Product `json:"products"` // 有著多個 Products
    CreatedAt time.Time
}

// Uppercase an exported to json
// A product has many tags
type Product struct {
    // gorm.Model
    Id           int    `gorm:"primary_key;AUTO_INCREMENT"`
    ProductName  string `gorm:"type:varchar(50);" json:"productName"`
    Price        string `gorm:"type:int;" json:"price"`
    PurchaseDate string `json:"purchaseDate"`
    UserId       int    `gorm:"foreign_key" sql:"index" json:"userId"` 
    Tags         []Tag  `json:"tags"`    // 有著多個 Tags
    CreatedAt    time.Time
    DeletedAt    *time.Time
}

type Tag struct {
    // gorm.Model
    Id        int    `gorm:"primary_key;AUTO_INCREMENT"`
    Name      string `gorm:"type:varchar(10);" json:"name"`
    ProductId int    `gorm:"foreign_key" sql:"index" json:"productId"` 
    UserId    int    `gorm:"foreign_key" sql:"index" json:"userId"`
    DeletedAt *time.Time
}

type Auths struct {
    Id       int    `gorm:"primary_key;AUTO_INCREMENT"`
    UserId   int    `gorm:";not null;" json:"userId"`
    AuthUuid string `gorm:"size:255;not null;" json:"auth_uuid"`
}
```

### Create table
Folder `database/database.go`

```golang=
func Migrate(db *gorm.DB) *gorm.DB {
    if db.HasTable(model.User{}) {
        db.AutoMigrate(model.User{})
    } else {
        db.CreateTable(model.User{})
        }

    db.AutoMigrate(model.Product{}, model.Tag{}, model.Auths{})

    db.Model(model.Product{}).AddForeignKey("user_id", "users(id)", "RESTRICT", "RESTRICT")
    db.Model(model.Tag{}).AddForeignKey("product_id", "products(id)", "RESTRICT", "RESTRICT")

    return db
}
```