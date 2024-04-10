---
title: GORM入门教程与最佳实践
date: 2023-08-10 16:50:54
cover: https://tse2-mm.cn.bing.net/th/id/OIP-C.pL59BikVm-D_YjBfbgSMagHaEW?rs=1&pid=ImgDetMain
categories: go
tags:
- 后端
- grom
---

GORM是一种Go语言的ORM（对象关系映射）库，用于简化数据库的操作。它提供了一种简单、易于使用的方式来进行数据库的增删改查操作，支持多种数据库，如MySQL、PostgreSQL、SQLite等。下面，我们将从理论和实践的角度学习GORM框架。<!--more-->

# GORM入门教程与最佳实践

## 简介

### 对象关系映射（ORM）

先来讲讲什么是ORM。ORM（对象关系映射）是一种编程技术，用于将关系型数据库中的数据映射到面向对象编程语言中的对象模型。它能够自动地在数据库表和对象之间建立映射关系，并提供一种简化数据库操作的方式。

相信有过Java开发经验的人都不会对MyBatis感到陌生，GORM的思想与MyBatis相似，都是为了简化数据库的操作，**将数据库的一个关系映射到业务中的一个类或者结构体**（尽管MyBatis并不认为是一个ORM框架，因为其保留了使用xml编辑数据库操作语言）。

ORM的核心思想是通过将数据库表的行数据映射为对象的属性，将数据库表的列映射为对象的字段，从而实现数据库和对象之间的无缝转换。ORM框架负责处理数据库的增删改查操作，以及数据库表和对象之间的映射关系，开发者只需要操作对象，而无需直接与底层数据库打交道

### GORM的优点

GORM框架有许多优点，这些优点减少了开发的工作量的同时还提高了系统的效率。

- 数据库模型定义：使用结构体来定义数据库表结构，通过结构体的字段和标签来映射数据库表的列。
- CRUD操作：提供了丰富的方法来进行数据库的增删改查操作，如Create、Read、Update和Delete等。
- 查询构建器：支持链式调用和条件语句来构建复杂的查询，包括条件查询、排序、分页等。
- 关联查询：支持通过预加载、延迟加载等方式进行关联查询，方便处理数据库表之间的关系。
- 事务支持：提供了事务的开启、提交和回滚等操作，保证原子性和数据一致性。
- 数据迁移：支持数据库结构的自动迁移，可以根据模型定义自动生成数据库表结构。
- 钩子函数：支持在数据库操作前后触发的钩子函数，方便进行数据验证、处理等操作。
- 软删除：支持逻辑删除，可以通过标记字段来实现数据的软删除和恢复。

### GORM框架下载

```
go get -u gorm.io/gorm
```

使用上述指令可以获取到最新的gorm框架。

## 数据库的连接

要想使用GORM框架，首先需要连接自己的数据库。此处略去如何安装数据库，并且以MySQL为例，建立连接并进行数据库操作。

建立连接的代码如下：

```go
username := "your_username"    //用户名
password := "your_password" //密码
host := "127.0.0.1"   //数据库地址，可以是Ip或者域名
port := 3306          //数据库端口
Dbname := "test"      //数据库名
//MYSQL dsn格式： {username}:{password}@tcp({host}:{port})/{Dbname}?charset=utf8mb4
dsn := fmt.Sprintf("%s:%s@tcp(%s:%d)/%s?charset=utf8mb4", username, password, host, port, Dbname)
//gorm.Open()用于建立连接，打开数据库
db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
if err != nil {
  panic("连接数据库失败, error=" + err.Error())
}

```

dsn是数据源名称(Data Source Name)，在GORM框架中，MySQL的dsn格式为：`{username}:{password}@tcp({host}:{port})/{Dbname}?charset=utf8mb4`，?后面的是可选参数，可以设置字符集，时区等等。

db是一个gorm.DB类型的对象，建立连接之后，后面对于数据库的所有操作都可以由这个对象实现。如果没有报错，那么证明数据库连接成功。

## GORM实现CRUD

这里再科普一遍CRUD的英文单词吧。CRUD 是一个常用的缩写词，代表了常见的数据库操作，包括创建（Create）、读取（Read）、更新（Update）和删除（Delete）。下面我们将分别讲讲如何使用GORM框架实现CRUD，同时也给出官方文档地址[gorm.io/docs/](https://gorm.io/docs/)。

### 插入数据

使用GORM框架之后，插入数据变得非常简单，实现代码如下：

```go
type User struct {
	Id   int    `gorm:"id"`
	Name string `gorm:"name"`
	Age  int    `gorm:"age"`
}


func (*User) TableName() string {
	return "user"
}

func main(){
    //---------------------
    //此处省略连接数据库的代码
    //---------------------
    res := db.Create(User{1, "Veni", 20})
	if res.Error != nil {
		panic(res.Error)
	}
    
    //插入指定字段，使用Select选择插入的字段，使用Omit选择忽略的字段，Omit字段使用数据库默认值
    db.Select("id").Omit("name","age").Create(User{1,"Veni",20})
    
}
```

上面我们讲述了ORM的定义，一个对象对应一个关系，在定义结构体的时候，`gorm:"id"`的作用是将结构的变量与数据库关系表中的字段建立映射。并且`func (*User) TableName() string`是一个结构体函数的写法，其`return`的值就是想要建立映射关系的表名。如果没有这个结构体函数，那么表名就是蛇形复数，比如User对应users表，UserName对应user_names表。这里我建了一张user表，注释掉结构体函数报错如下：

![image-20230810201529078](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202308102015271.webp)

Create函数自动创建了一个INSERT语句用于插入信息，并且将结构体中的数据传入数据库。

### 读取数据

读取数据可以比较灵活，比如使用.分的控制语句查找符合要求的数据。比如：

```go
func main(){
    //---------------------
    //此处省略连接数据库的代码
    //---------------------
    
    //声明对象用以接收查询结果
	u := User{}
	res := db.Where("name=?", "Veni").Find(&u)
	if res.Error != nil {
		panic(res.Error)
	}
	fmt.Printf("%+v", u)
    
    //声明切片用于保存多个查询结果
    uu := []User{}
	res = db.Find(&uu)
	if res.Error != nil {
		panic(res.Error)
	}
	fmt.Printf("%+v", uu)
}
```

#### 指定查找范围和接收对象

GORM中用于指定接收对象的关键字有First，Last，Take，Find。此类关键字一般放在查询语句的最后，用于指定查询的范围和接收的对象。记得加取址符号&，这样才能修改u的内容。

```go
//找到符合条件并且按照主键排序的第一个user
res := db.Where("name = ?", "jinzhu").First(&u)
//找到按照age字段升序排列的第一个user——First
res := db.Order("age asc").First(&u)
//找到最后一个——Last
res := db.Order("age asc").Last(&u)
//随便找到一个——Take
res := db.Order("age asc").Take(&u)
//找到所有满足条件的——Find
res := db.Order("age asc").Find(&uu)
```

使用First查询不到数据的时候，会返回ErrRecoedNotFound，而使用Find查询不到的时候返回空数组。

#### 选择语句

使用Where方法可以实现条件查询，即数据库的选择操作。

```go
res := db.Where("name = ?","Veni").Find(&uu)
```

需要注意的是，在gorm框架使用Where需要放在Find函数前面。这一点不同于数据库查询语言DQL中的写法：Where语句写在最后。

#### 投影语句

gorm框架中的Select方法，对应的是DQL中的Select语句。

```go
res := db.Select("name,age").Where("age > ?", 0).Find(&u)
```

执行查询语句：`[{Id:0 Name:Veni Age:20} {Id:0 Name:Moss Age:75}]`，可以看到，由于我没有选择Id字段，查找结果里面的所有对象的Id均为0（因为int的空置为0，string的空值为空串）。但是，作为一个ORM框架，每一个字段都应该对应着一个对象的一个属性，即便没有Select，该属性还是会占用内存，也就是说存储一个10和一个0是差别不大的，所以Select语句使用相对较少。当然在分组语句和聚合函数中还是会使用的。

#### 分组语句

分组语句的实现方法：

```go
db.Group("age").Find(&result)
```

需要注意的是：不在分组函数中的字段必须在聚合函数中。比如一张工人薪资表，按照部门求平均工资，则group的字段为部门，那么薪资必须要使用average（或者sum等聚集函数）。这是因为，分组语句会改变行数，因此不同字段需要使用聚集函数同时改变行数才能维持表的行列形态。

#### 聚合函数

聚合函数包括sum, average,count,max,min等，使用聚合函数的方法类似，都是在Select或者Having语句中嵌套使用。

```go
type temp struct {
    Count int
    Name  string
    Age   int
}
tempArr := []temp{}
//读取数据
res := db.Table("user").Select("count(*) as `count`,name,age").Group("id,name,age").Having("count(*) >?", 0).Find(&tempArr)
```

需要注意的是，使用聚合函数建立的查找表的字段一般都不是model对应的字段，因此需要定义一个新的结构体去接收和存储查询结果。字段名遵循蛇形命名方法，同时使用Table方法指定表。

#### having语句

分组或聚集之后的查询表需要进行选择操作时，不能使用where语句，需要使用having语句，比如：

```go
res := db.Table("user").Select("count(*) as `count`,name,age").Group("id,name,age").Having("count(*) >?", 0).Find(&tempArr)
```

### 更新数据

#### 给定数据更新：

```go
//使用model指定操作的表名
db.Model(&User{}).Where("name=?", "Veni").Update("age", 20)
//使用table指定操作的表名
db.Table("user").Where("name=?", "Veni").Update("age", 20)
```

这里推荐使用Table指定表名，因为使用Table比较接近于数据库查询语言DML的写法。

#### SQL表达式更新：

```go
db.Table("user").Where("name = ?", "Moss").Update("age", gorm.Expr("age * 2 + ?", 1))
```

这里使用了`gorm.Expr()`方法，这个方法是表达式更新的写法，表达式为`age=age*2+1`，这个写法比较灵活，可以指定多个参数或不指定参数。

### 删除数据

#### 物理删除

```go
//直接删除，后面接的都是主键的值
db.Delete(&User{},1)//删除主键为1的记录
db.Delete(&User{},[]int{1,2})//删除主键为1和2的记录

//Where条件语句删除
db.Where("name = ?","Moss").Delete(&User{})

//简洁的条件删除
db.Delete(&User{},"Name = ?","Moss")
```

#### 软删除

gorm提供了goem.DeleteAt用于帮助用户实现软删。定义方法：

```go
type User struct{
    Id      int
    Name    string
    age     int
    Deleted gorm.DeleteAt
}
```

拥有软删除能力的Model调用Delete的时候，记录不会从数据库中真正删除，但是gorm会将DeleteAt设置为当前时间，并且无法正常查询。使用Unscoped可以查询到软删的数据。

## GORM的事务

数据库的事务是指一组数据库操作（如插入、更新、删除等）被视为一个不可分割的工作单元，并且要么全部成功执行，要么全部回滚（撤销）。事务可以确保数据库的一致性和完整性，同时提供了并发控制和故障恢复的机制。GORM提供了Begin、Commit、Rollback等方法用于事务。

```go
tx:=db.Begin() //开始事务
//在事务中执行db操作需要全部换成tx
res=tx.Create(&User{Name:"name"})
if res.Error!=nil{
    //遇到错误时回滚
    tx.Rollback()
    return
}
tx.Commit()///提交事务
```

Transaction方法自动提交事务：

```go
if err = db.Transaction(func(tx *gorm.DB) error {
    if err = tx.Create(&User{1, "M", 18}).Error; err != nil {
        return err
    }
    if err = tx.Create(&User{1, "Name", 11}).Error; err != nil {
        tx.Rollback()
        return err
    }
    return nil
}); err != nil {
    return
}
```

推荐使用Transaction方法提交事务，这样可以防止忘记提交的情况。

## GORM Hook

Gorm提供了CRUD的Hook（钩子函数）能力，在创建，查询，更新，删除等操作前后自动调用的函数。

```go
func (u *User) BeforeCreate (tx *gorm.DB)(err error){
    if u.Age<0{
        return errors.New("Age cannot be negative")
    }
    return
}
//同理也可以创建AfterCreate的Hook。
```

## GORM生态

GORM框架有着极其丰富的生态，以下是一些常见的GORM框架的工具及其仓库地址。在熟练使用GORM原生框架之后，可以尝试使用下面的工具提高开发效率或者系统性能。

| 工具名                  | 地址                                      |
| ----------------------- | ----------------------------------------- |
| GORM 代码生成工具       | https://github.com/go-gorm/gen            |
| GORM 分片库方案         | https://github.com/go-gorm/sharding       |
| GORM 手动索引           | https://github.com/go-gorm/hints          |
| GORM 乐观锁             | https://github.com/go-gorm/optimisticlock |
| GORM 读写分离           | https://github.com/go-gorm/dbresolver     |
| GORM OpenTelemetry 扩展 | https://github.com/go-gorm/opentelemetry  |
