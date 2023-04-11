## GORM框架

### 初始化

#### 安装

```go
go get -u gorm.io/gorm
go get -u github.com/jinzhu/gorm
```

#### 导入

```go
import (
	"gorm.io/driver/postgres"
	"gorm.io/gorm"
	"gorm.io/gorm/schema"
)

// import _ "github.com/jinzhu/gorm/dialects/mysql"
// import _ "github.com/jinzhu/gorm/dialects/postgres"
// import _ "github.com/jinzhu/gorm/dialects/sqlite"
// import _ "github.com/jinzhu/gorm/dialects/mssql"
```

#### 数据库连接

##### PostgreSQL

```go
import (
    "github.com/jinzhu/gorm"
    _ "github.com/jinzhu/gorm/dialects/postgres"
)

func main() {
  db, err := gorm.Open("postgres", "host=myhost user=gorm dbname=gorm sslmode=disable password=mypassword")
  defer db.Close()
}
```

##### MySql

```go
import (
    "github.com/jinzhu/gorm"
    _ "github.com/jinzhu/gorm/dialects/mysql"
)

func main() {
  db, err := gorm.Open("mysql", "user:password@/dbname?charset=utf8&parseTime=True&loc=Local")
  defer db.Close()
}
```

#### Struct&表

```go
// 表名是结构体名的复数形式(例如：结构体名为user，则表名为users)
// 禁用表名复数，使用`TableName`设置的表名不受影响
db.SingularTable(true) 
// 列名是字段名的蛇形小写
// 	column 			指定 db 列名
//  comment 		字段添加注释
//	primaryKey 		指定字段为主键
//	index 			字段添加索引
// 	size 			指定字段长度
//  default 		指定字段默认值
//	embedded 		嵌入结构体
// 	embeddedPrefix 	指定嵌入结构体的前缀
//	autoCreateTime 	创建时追踪当前时间
// 	autoUpdateTime 	创建/更新时追踪当前时间
type Demo struct {
	DemoId     		uint64 			`gorm:"column:demo_id;comment:ID;primaryKey;index"`
	DemoName   		string			`gorm:"column:demo_name;size:100;not null"`
	DemoStatus 		uint8			`gorm:"column:demo_status;not null;default:1"`
	DemoType   		uint8			`gorm:"column:demo_type;not null"`
	IpAddr     		string			`gorm:"column:ip_addr;size:100"`
	DemoTime   		string			`gorm:"column:demo_time;size:100"`
	Sub 			Sub				`gorm:"embedded;embeddedPrefix:sub_"`
	Base  	   		Base   			`gorm:"embedded"`
}
type Base struct {
	CreateUserId    uint64			`gorm:"column:create_user_id"`
	CreatedTime    	time.Time 		`gorm:"column:create_time;autoCreateTime"`
	UpdatedUserId	uint64			`gorm:"column:update_user_id;column:sub_id"`
	UpdatedTime		time.Time  		`gorm:"column:update_time;autoUpdateTime"`
}
type Sub struct {
	Id    			uint64			`gorm:"column:sub_id"`
	Name    		time.Time		`gorm:"column:sub_id"`
	Status			uint64			`gorm:"column:sub_id"`
}

// DemoT Demo 等效于 DemoT
type DemoT struct {
	DemoId     		uint64 		`gorm:"primaryKey"`
	DemoName   		string
	DemoStatus 		uint8
	DemoType   		uint8
	IpAddr     		string
	DemoTime   		string
	SubId    		uint64
	SubName    		time.Time
	SubStatus		uint64
	CreateUserId    uint64
	CreatedTime    	time.Time 	`gorm:"autoCreateTime"`
	UpdatedUserId	uint64
	UpdatedTime		time.Time  	`gorm:"autoUpdateTime"`
}

type DemoList []*Demo
```

### CRUD

```go
type User struct {
	Id int
	Name string
	Age int
	Sex byte
	Phone string
}

var db *gorm.DB
func init() {
	var err error
	dsn := "host=localhost port=5432 user=postgres password=postgres dbname=test sslmode=disable TimeZone=Asia/Shanghai"
	db, err= gorm.Open(postgres.Open(dsn), &gorm.Config{
		NamingStrategy: schema.NamingStrategy{
			TablePrefix:   "t_",                              // 表名前缀，`User`表为`t_user`
			SingularTable: true,                              // 使用单数表名，启用该选项后，`User` 表将是`user`
			NameReplacer:  strings.NewReplacer("CID", "Cid"), // 在转为数据库名称之前，使用NameReplacer更改结构/字段名称。
		},
	})
	if err != nil {
		fmt.Println("连接数据库出错：",err.Error())
		return
	}
}
func query() (users []User) {
	// 查询所有记录
	return u
}
```

#### 新增

```go
//插入数据
func (user *User) Insert()  {
	//这里使用了Table()函数，如果你没有指定全局表名禁用复数，或者是表名跟结构体名不一样的时候
	//你可以自己在sql中指定表名。这里是示例，本例中这个函数可以去除。
	db.Table("t_user").Create(user)
}
```

#### 删除

```go
//delete from user where id=1;
user := User{Id: 1}
db.Delete(&user)

//delete from user where id > 11;
db.Delete(&User{},"id > ?",11)
```

#### 修改

```go
//注意，Model方法必须要和Update方法一起使用
//使用效果相当于Model中设置更新的主键key（如果没有where指定，那么默认更新的key为id），Update中设置更新的值
//如果Model中没有指定id值，且也没有指定where条件，那么将更新全表
//相当于：update user set name='xiaoming' where id=1;
user := User{Id: 1,Name:"全超"}
db.Model(&user).Update(user)

//注意到上面Update中使用了一个Struct，你也可以使用map对象。
//需要注意的是：使用Struct的时候，只会更新Struct中这些非空的字段。
//对于string类型字段的""，int类型字段0，bool类型字段的false都被认为是空白值，不会去更新表

//下面这个更新操作只使用了where条件没有在Model中指定id
//update user set name='xiaohong' wehre sex=1
db.Model(&User{}).Where("sex = ?",1).Update("name","全超")
```

#### 查询

##### Find查询所有记录

```go
// 查询所有记录 SELECT * FROM t_user;
db.Find(&users)
// 带条件 SELECT * FROM t_user where id > 2 and age = 18;
db.Find(&users,"id > ? and age = ?",2,12)
```

##### First/Last查询一条记录

```go
// 按照主键顺序排序，获取第一条记录 SELECT * FROM t_user ORDER BY id LIMIT 1;
db.First(&users)
// 带where条件 SELECT * FROM t_user where age = 18 ORDER BY id LIMIT 1;
db.First(&users,"where age = ?",18)

// 按照主键顺序排序，获取最后一条记录 SELECT * FROM users ORDER BY id DESC LIMIT 1;
db.Last(&users)
// 带where条件
db.Last(&users,"where age = ?",18)
```

##### Select指定查询字段

```go
// 指定查询字段
db.Select("name,age").Where(map[string]interface{}{"age":12,"sex":1}).Find(&users)
```

##### Pluck查询单列

```go
// Pluck函数用于查询单列，返回数组
var ages []int
db.Find(&users).Pluck("age",&ages)
```

##### Table指定表名

```go
db.Table("t_user").Where("age > ?",0)
```

##### Scan将数据存储到struct

```go
type SubUser struct{
    Name string
    Age int
}
// select name,age FROM t_user WHERE age > 18;
db.Table("t_user").Select("name,age").Scan(&SubUser)
```

##### where查询条件

```go
// AND
db.Where("name = ? AND age = ?", "全超", "20").Find(&users)
// SELECT * FROM t_user WHERE name = '全超' AND age = 20;

// Struct 
db.Where(&User{Name: "全超", Age: 20}).Find(&users)
// SELECT * FROM t_user WHERE name = "全超" AND age = 20;

// Map 
db.Where(map[string]interface{}{"name": "jinzhu", "age": 20}).Find(&users)
// SELECT * FROM t_user WHERE name = "全超" AND age = 20;

// 切片Slice
db.Where([]int64{20, 21, 22}).Find(&users)
// SELECT * FROM t_user WHERE id IN (20, 21, 22);
```

##### Order排序

```go
// SELECT * FROM t_user ORDER BY age desc, name;
db.Order("age desc, name").Find(&users)
db.Order("age desc").Order("name").Find(&users)
```

##### Limit/Offset分页

```go
// SELECT * FROM t_user OFFSET 3 LIMIT 3;
db.Offset(3).Limit(30).Find(&users)
```

##### Count计数

```go
// 计数，返回int
var count int
db.Table("t_user").Where("age > ?",18).Count(&count)
// select count(*) from t_user where age > 0;
```

##### join指定连接条件

```go
db.Table("t_user u").Select("u.name,u.age").Joins("left join t_role r on u.user_id = r.user_id").Row()
```

##### Raw/Exec原生Sql

```go
// Raw+Scan
db.Raw("select * from t_user where id > ?",10).Scan(&users)

// Exec
db.Exec("DROP TABLE t_user")
db.Exec("UPDATE t_user SET name=? WHERE id IN (?)", "xiaoming", []int{11,22,33})
db.Exec("UPDATE t_user SET year = ? WHERE name = ?", gorm.Expr("? - age + ?", 2021, 1), "全超")
```
