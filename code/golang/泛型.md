## 泛型
泛型让你可以定义一个函数或数据结构，它可以处理任何类型的数据，而不需要事先指定类型。你只需要在使用时决定具体的类型。
在没有泛型的时代，我们通常会面临两种选择：要么为每一种数据类型编写一个独立的函数，要么使用interface{}来实现通用性。
如果没有泛型，依赖接口和断言：
1. 类型不安全，增加潜在的panic风险
2. 代码可读性差，类型判断和转换繁琐
3. 性能消耗，需要断言或类型转换，影响运行效率

泛型的好处：
1. 代码可读性好，类型参数化，使代码更易读、易懂
2. 类型安全，避免了类型不安全的情况
3. 性能好，泛型函数在编译时就可以确定类型，避免了运行时类型检查，提高了运行效率
4. 扩展性强，可以处理多种类型的数据，灵活应对变化
5. 适应性强，可以适应不同的业务场景，提高代码复用性
### 泛型基础
在 Go 中，我们可以通过在函数名和参数列表之间添加类型参数列表来定义一个泛型函数。这些类型参数可以像普通类型一样在函数签名中使用，实现对多种类型的支持。
```go
// 这是一个泛型Filter函数，E是类型参数
func Filter[E any](data []E, filter func(E) bool) []E {
    var result []E
    for _, v := range data {
        if filter(v) {
            result = append(result, v)
        }
    }
    return result
}
```
### 以前的模式
```go
// 结构体
type IntBox struct {
    value int
}

type StringBox struct {
    value string
}
// 函数
func LengthInt(s []int) int {
    return len(s)
}

func LengthString(s []string) int {
    return len(s)
}

//集合
type IntSet struct {
    data map[int]struct{}
}

type StringSet struct {
    data map[string]struct{}
}
```
### 但有了泛型：
```go
// 结构体
type Box[T any] struct {    
}
intBox := Box[int]{value: 10}
fmt.Print(intBox.value) // 输出: 10

stringBox := Box[string]{value: "hello"}
fmt.Print(stringBox.value) // 输出: hello

// 函数
func Length[T any](s []T) int {
    return len(s)
}

// 集合
type Set[T comparable] struct {
    data map[T]struct{}
}
intSet := Set[int]{data: make(map[int]struct{})}
intSet.data[1] = struct{}{}
fmt.Print(intSet.data) // 输出: map[1:{}]

stringSet := Set[string]{data: make(map[string]struct{})}
stringSet.data["apple"] = struct{}{}
fmt.Print(stringSet.data) // 输出: map[apple:{}]

// channel
type Channel[T any] struct {
    ch chan T
}

func NewChannel[T any](size int) *Channel[T] {
    return &Channel[T]{ch: make(chan T, size)}
}

func (c *Channel[T]) Send(value T) {
    c.ch <- value
}

func (c *Channel[T]) Receive() T {
    return <-c.ch
}

// 泛型工厂函数
func NewObject[T any](value T) *T {
    return &value
}

// 1.24引入了泛型类型别名
type Vector[T any] []T
type VectorAlias[T any] = Vector[T] // 泛型类型别名
```
### 泛型接口
```go
// 定义泛型接口 Container，类型参数为 T
type Container[T any] interface {
    Add(element T)  // 添加元素
    Get(index int) (T, error)  // 获取指定索引的元素
    Len() int  // 获取容器长度
}


// -----------
// 定义一个基于切片的容器，存储类型为 T 
type SliceContainer[T any] struct {
    elements []T  // 用切片存储元素
}

// 实现 Container[T] 接口的 Add 方法
func (s *SliceContainer[T]) Add(element T) {
    s.elements = append(s.elements, element)
}

// 实现 Container[T] 接口的 Get 方法
func (s *SliceContainer[T]) Get(index int) (T, error) {
    if index < 0 || index >= len(s.elements) {
        var zero T  // 返回 T 类型的零值
        return zero, fmt.Errorf("index out of range")
    }
    return s.elements[index], nil
}

// 实现 Container[T] 接口的 Len 方法
func (s *SliceContainer[T]) Len() int {
    return len(s.elements)
}

// 泛型函数：接收任意类型的 Container 接口
func printContainer[T any](c Container[T]) {
    fmt.Print("元素: ")
    for i := 0; i < c.Len(); i++ {
        val, _ := c.Get(i)
        if i > 0 {
            fmt.Print(", ")
        }
        fmt.Print(val)
    }
    fmt.Println()
}
```