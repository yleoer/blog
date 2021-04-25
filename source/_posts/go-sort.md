---
title: Go 语言中 sort 包的使用
date: 2021-04-25 10:48:09
tags: 
	- Golang
	- 排序
categories: 编程语言
---

Go 语言的 sort 包提供了对切片和自定义集合排序的函数。

<!-- more -->

## 使用函数进行排序

sort 包内置了对 `float64`、`int` 和 `string` 的升序排序。以 `int` 作为示例：

```go
s := []int{5, 2, 6, 3, 1, 4}       // 未排序的
fmt.Println(sort.IntsAreSorted(s)) // 判断是否是升序排序：false
sort.Ints(s)                       // 进行升序排序
fmt.Println(sort.IntsAreSorted(s)) // 判断是否是升序排序：true
fmt.Println(s)                     // [1 2 3 4 5 6]
```

而对于其他切片，可以使用 `sort.Slice()` 函数简单排序：

```go
people := []struct {
    Name string
    Age  int
}{
    {"Gopher", 7},
    {"Alice", 55},
    {"Vera", 24},
    {"Bob", 75},
}
sort.Slice(people, func(i, j int) bool {
    return people[i].Name < people[j].Name
})
fmt.Println("By name:", people) // By name: [{Alice 55} {Bob 75} {Gopher 7} {Vera 24}]
sort.Slice(people, func(i, j int) bool {
    return people[i].Age < people[i].Age
})
fmt.Println("By age:", people) // By age: [{Alice 55} {Bob 75} {Gopher 7} {Vera 24}]
```

{% note warning %}

1. 第一个参数虽然函数签名是 `interface{}`，但如果不是切片就会 `panic`。
2. 当两个元素的比较项相同，那么它们的顺序可能会颠倒，如果需要保持原来的顺序，则使用 `sort.SliceStable()` 函数进行排序。

{% endnote %}

## 查找

sort 包还可以通过二分查找法对一个**有序**的切片进行查找，此处用 `int` 为例：

```go
a := []int{55, 45, 36, 28, 21, 15, 10, 6, 3, 1}
x := 6
i := sort.Search(len(a), func(i int) bool {
    return a[i] <= x
})
fmt.Println(i) // 7
```

需要注意的是，如果数组中不存在需要查找的数，那么会返回最接近的数的位置，而不是 -1：

```go
a := []int{55, 45, 36, 28, 21, 15, 10, 6, 3, 1}
x := 20
i := sort.Search(len(a), func(i int) bool {
   return a[i] <= x
})
fmt.Println(i)
```

因此获取下标后还需要将下标带入切片进行判断是否是查找的数。

## 自定义结构排序

Go 语言使用了一个接口类型 `sort.Interface`。

```go
type Interface interface {
    Len() int           // 获取元素的数量
    Less(i, j int) bool // 对连个元素进行比较
    Swap(i, j int)      // 交换两个元素
}
```

### 简单排序

参考 [官方示例](https://github.com/golang/go/blob/master/src/sort/example_interface_test.go)，我们需要对用户的年龄进行排序。首先定义用户结构体，并对用户切片自定义类型，使其实现 `sort.Interface` 接口：

```go
type Person struct {
    Name string
    Age  int
}

type ByAge []Person

func (a ByAge) Len() int { return len(a) }
func (a ByAge) Less(i, j int) bool { return a[i].Age < a[j].Age }
func (a ByAge) Swap(i, j int) { a[i], a[j] = a[j], a[i] }
```

之后，就可以使用 `sort.Sort()` 对其进行排序：

```go
people := []Person{
    {"Bob", 31},
    {"John", 42},
    {"Michael", 17},
    {"Jenny", 26},
}

fmt.Println(sort.IsSorted(ByAge(people))) // false
sort.Sort(ByAge(people))                  // 进行正序排序
fmt.Println(sort.IsSorted(ByAge(people))) // true
fmt.Println(people)                       // [{MMichael 17} {Jenny 26} {Bob 31} {John 42}]
```

### 根据关键字排序

在上个示例中，如果需求更改为根据名字排序，只有创建新的结构体，再实现 `sort.Interface` 接口。这样代码冗余。对此，官方给出了一个 [技巧示例](https://github.com/golang/go/blob/master/src/sort/example_keys_test.go)。

在这个例子中，我们对行星进行建模，可以根据它们的名字、质量或者离太阳的距离进行升序或降序排序。

首先，建立行星结构体：

```go
// earthMass 行星质量
type earthMass float64

// au 任意单位
type au float64

// Planet 行星模型
type Planet struct {
	name     string    // 行星名字
	mass     earthMass // 行星质量
	distance au        // 离太阳的距离
}
```

然后我们定义一个 `By` 类型，它基于 `sort.Interface` 接口其中 `Less()` 方法，用来管理行星参数排序：

```go
type By func(p1, p2 *Planet) bool
```

接着创建行星切片和 `By` 类型结合的结构体，并使其实现 `sort.Interface` 接口：

```go
type planetSorter struct {
    planets []Planet
    by      By
}

func (s *planetSorter) Len() int {
	return len(s.planets)
}

func (s *planetSorter) Swap(i, j int) {
	s.planets[i], s.planets[j] = s.planets[j], s.planets[i]
}

func (s *planetSorter) Less(i, j int) bool {
	return s.by(&s.planets[i], &s.planets[j])
}
```

现在实现排序接口的是 `planetSorter` 结构体，它判断排序顺序的方法 `Less()` 是基于 `By` 类型的，最后我们在 `By` 上添加方法，实现排序：

```go
func (b By) Sort(planets []Planet) {
	ps := &planetSorter{
		planets: planets,
		by:      b,
	}
	sort.Sort(ps)
}
```

整个流程就是将确定排序顺序的 `Less()` 方法提出来，其他公用：

```go
var planets = []Planet{
	{"Mercury", 0.055, 0.4},
	{"Venus", 0.815, 0.7},
	{"Earth", 1.0, 1.0},
	{"Mars", 0.107, 1.5},
}
name := func(p1, p2 *Planet) bool {
    return p1.name < p2.name
}
mass := func(p1, p2 *Planet) bool {
    return p1.mass < p2.mass
}
distance := func(p1, p2 *Planet) bool {
    return p1.distance < p2.distance
}
decreasingDistance := func(p1, p2 *Planet) bool {
    return distance(p2, p1)
}

By(name).Sort(planets)
fmt.Println("By name:", planets) // By name: [{Earth 1 1} {Mars 0.107 1.5} {Mercury 0.055 0.4} {Venus 0.815 0.7}]

By(mass).Sort(planets)
fmt.Println("By mass:", planets) // By mass: [{Mercury 0.055 0.4} {Mars 0.107 1.5} {Venus 0.815 0.7} {Earth 1 1}]

By(distance).Sort(planets)
fmt.Println("By distance:", planets) // By distance: [{Mercury 0.055 0.4} {Venus 0.815 0.7} {Earth 1 1} {Mars 0.107 1.5}]

By(decreasingDistance).Sort(planets)
fmt.Println("By decreasing distance:", planets) // By decreasing distance: [{Mars 0.107 1.5} {Earth 1 1} {Venus 0.815 0.7} {Mercury 0.055 0.4}]
```

### 多关键字排序

有时候，单一关键字排序会出现相同值的情况，因此需要同时 [多关键字排序](https://github.com/golang/go/blob/master/src/sort/example_multi_test.go)。

这次的例子，使用的是代码更改记录，首先创建结构体：

```go
type Change struct {
	user     string
	language string
	lines    int
}
```

和上一个例子一样，创建基于 `Less()` 的类型：

```go
type lessFunc func(p1, p2 *Change) bool
```

接着，创建结合了 `Change` 和 `lessFunc` 的结构体，与上个例子不同的是，这次 `lessFunc` 也是切片：

```go
type multiSorter struct {
    changes []Change
    less    []lessFunc
}
```

然后，实现 `sort.Interface` 接口：

```go
func (ms *multiSorter) Len() int {
	return len(ms.changes)
}

func (ms *multiSorter) Swap(i, j int) {
	ms.changes[i], ms.changes[j] = ms.changes[j], ms.changes[i]
}

// Less 循环判断 less，直到有大小结果或最后一组
// 文档也有提示，函数内的 less 可能会调用两次
// 可以通过返回 1,0,-1 优化
func (ms *multiSorter) Less(i, j int) bool {
	p, q := &ms.changes[i], &ms.changes[j]
	// 除了最后一个，其他都进行比较
	var k int
	for k = 0; k < len(ms.less)-1; k++ {
		less := ms.less[k]
		switch {
		case less(p, q):
			return true
		case less(q, p):
			return false
		}
		// p == q: 尝试下一个比较
	}
	// 当前面比较都是 == 时，直接返回最后一次比较结果
	return ms.less[k](p, q)
}
```

最后便是组装：

```go
func OrderedBy(less ...lessFunc) *multiSorter {
	return &multiSorter{less: less}
}

func (ms *multiSorter) Sort(changes []Change) {
	ms.changes = changes
	sort.Sort(ms)
}
```

和上一个例子的思路其实是一样的，`Less()` 方法在外面自定义，其他方法公用。只不过这次可以传入多个 `Less()` 方法，通过循环判断，直到不相等的结果或最后一个比较：

```go
var changes = []Change{
	{"gri", "Go", 100},
	{"ken", "C", 150},
	{"glenda", "Go", 200},
	{"rsc", "Go", 200},
	{"r", "Go", 100},
	{"ken", "Go", 200},
	{"dmr", "C", 100},
	{"r", "C", 150},
	{"gri", "Smalltalk", 80},
}
user := func(c1, c2 *Change) bool {
    return c1.user < c2.user
}
language := func(c1, c2 *Change) bool {
    return c1.language < c2.language
}
increasingLines := func(c1, c2 *Change) bool {
    return c1.lines < c2.lines
}
decreasingLines := func(c1, c2 *Change) bool {
    return c1.lines > c2.lines
}

OrderedBy(user).Sort(changes)
fmt.Println("By user:", changes) // By user: [{dmr C 100} {glenda Go 200} {gri Go 100} {gri Smalltalk 80} {ken C 150} {ken Go 200} {r Go 100} {r C 150} {rsc Go 200}]
OrderedBy(user, increasingLines).Sort(changes)
fmt.Println("By user,<lines:", changes) // By user,<lines: [{dmr C 100} {glenda Go 200} {gri Smalltalk 80} {gri Go 100} {ken C 150} {ken Go 200} {r Go 100} {r C 150} {rsc Go 200}]
OrderedBy(user, decreasingLines).Sort(changes)
fmt.Println("By user,>lines:", changes) // By user,>lines: [{dmr C 100} {glenda Go 200} {gri Go 100} {gri Smalltalk 80} {ken Go 200} {ken C 150} {r C 150} {r Go 100} {rsc Go 200}]

OrderedBy(language, increasingLines).Sort(changes)
fmt.Println("By language,<lines:", changes) // By language,<lines: [{dmr C 100} {ken C 150} {r C 150} {r Go 100} {gri Go 100} {ken Go 200} {glenda Go 200} {rsc Go 200} {gri Smalltalk 80}]

OrderedBy(language, increasingLines, user).Sort(changes)
fmt.Println("By language,<lines,user:", changes) // By language,<lines,user: [{dmr C 100} {ken C 150} {r C 150} {gri Go 100} {r Go 100} {glenda Go 200} {ken Go 200} {rsc Go 200} {gri Smalltalk 80}]
```

## 总结

Go 语言的 sort 包提供了排序和二分查找，常用的整数切片、浮点数切片和字符串切片可以直接使用函数进行升序排序，也可以使用 `sort.Slice()` 方法进行一次性自定义排序。另外可以通过实现 `sort.Interface` 接口使用 `sort.Sort()` 函数进行排序，当需要多参数排序时，可以参考官方示例。

[^1]: [sort 包文档](https://golang.google.cn/pkg/sort)
[^2]: [sort 源码](https://github.com/golang/go/tree/master/src/sort)