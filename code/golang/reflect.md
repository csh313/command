### 反射
反射（Reflection）是指程序在运行时动态获取变量的类型信息、值信息，以及修改变量值的能力。由于 Go 是静态类型语言（变量类型在编译期确定），反射机制为静态类型系统提供了动态检查和操作的灵活性，其核心通过标准库 reflect 包实现。

Go 反射的底层依赖于类型系统和接口的内存布局，核心是通过接口（interface{}）作为桥梁，提取变量的类型和值信息。
反射的本质是：通过接口变量，在运行时解析其存储的静态类型和动态类型，以及对应的值。
核心依赖 reflect.Type 和 reflect.Value 两大对象，并遵循 “从接口到反射对象、从反射对象到接口、可设置性” 三大法则。
Go 接口在内存中由两部分组成（类似 runtime.iface 结构）：
- 类型指针（type）：指向变量的动态类型信息（reflect.Type 的来源）。
- 数据指针（data）：指向变量的实际值（reflect.Value 的来源）。
反射场景：
- 序列化/反序列化：将结构体序列化为字节流，反序列化为结构体。
- ORM框架通过反射将结构体字段映射到数据库表的列，动态生成 SQL
- 数据验证：通过反射检查结构体字段的标签（如 validate:"required"）

### 反射的使用
1. 通过 reflect.TypeOf 和 reflect.ValueOf 将变量转换为反射对象，分别获取类型信息和值信息。
```go
// 获取类型信息（reflect.Type）
t := reflect.TypeOf(u)
// 获取值信息（reflect.Value）
v := reflect.ValueOf(u)

// 打印基本信息
fmt.Println("类型:", t.Name())       // 输出：User（结构体名称）
fmt.Println("种类:", t.Kind())       // 输出：struct（类型的底层种类）
fmt.Println("值:", v.Field(0).String()) // 输出：Alice（第一个字段的值）

// 修改值
v.Field(0).SetString("Bob") // 修改第一个字段的值为 Bob
// 操作结构体
// 检查是否为结构体（反射安全操作的前提）
if t.Kind() == reflect.Struct {
    // 遍历所有字段
    for i := 0; i < t.NumField(); i++ {
        fieldType := t.Field(i)   // 字段类型信息
        fieldValue := v.Field(i)  // 字段值信息

        // 打印字段名、值、标签
        fmt.Printf("字段名: %s, 值: %v, json标签: %s\n",
            fieldType.Name,        // 字段名（如 "Name"）
            fieldValue.Interface(), // 字段值（如 "Alice"）
            fieldType.Tag.Get("json"), // 字段标签（如 "name"）
        )
    }
}
```
### 解析tag标签
tag（标签） 是附着在结构体字段上的一段特殊字符串，用于为字段提供额外的元信息（metadata）。这些信息不会直接影响结构体的逻辑功能，但可以通过反射（reflection）在运行时被读取，从而实现灵活的动态行为。

- 序列化/反序列化
//json:"name"：指定 JSON 字段名为 name（默认使用结构体字段名）。
//json:"age,omitempty"：序列化时若 Age 为零值（如 0），则忽略该字段。
//json:"-"：序列化时完全忽略该字段（如敏感信息 Password）。
```go
type User struct {
    Name string `json:"username"`
    Age  int    `json:"age,omitempty"`
}

u := User{Name: "Alice", Age: 0}
data, _ := json.Marshal(u)
fmt.Println(string(data)) // 输出：{"username":"Alice"}（Age 为 0 被忽略）
```
- ORM字段映射
gorm:"column:user_name"：指定数据库列名为 user_name。
gorm:"primaryKey"：标记该字段为表的主键。
gorm:"type:int(11);not null"：指定字段的数据库类型和约束。
```go
type User struct {
    ID   int    `gorm:"primaryKey;column:user_id"`
    Name string `gorm:"column:user_name;type:varchar(50)"`
}
// GORM 会根据 tag 生成 SQL：
// CREATE TABLE users (user_id int primary key, user_name varchar(50))
```
- 数据验证
validate:"required"：字段为必填项（不能为空）。
validate:"min=6,max=20"：字符串长度或数值范围限制。
validate:"email"：验证字段是否符合邮箱格式。
```go  
type LoginReq struct {
    Username string `validate:"required"`
    Password string `validate:"min=6,max=20"`
}
// 验证库通过反射读取 tag，检查参数是否符合规则
```
首字母小写的结构体字段（私有字段）的 tag 不能通过反射访问！！！
```go
func main() {
    u := User{}
    t := reflect.TypeOf(u)

    if t.Kind() != reflect.Struct {
        panic("目标不是结构体")
    }

    // 遍历所有字段
    for i := 0; i < t.NumField(); i++ {
        // 获取第 i 个字段的类型信息
        field := t.Field(i)

        // 1. 打印字段名
        fmt.Printf("字段名: %s\n", field.Name)

        // 2. 提取指定 key 的 tag 值（如 "json"、"gorm"）
        jsonTag := field.Tag.Get("json")    // 获取 json 标签
        gormTag := field.Tag.Get("gorm")    // 获取 gorm 标签
        validateTag := field.Tag.Get("validate") // 获取 validate 标签

        // 3. 打印标签值
        fmt.Printf("  json标签: %q\n", jsonTag)
        fmt.Printf("  gorm标签: %q\n", gormTag)
        fmt.Printf("  validate标签: %q\n", validateTag)
        fmt.Println("-----")
    }
}
```