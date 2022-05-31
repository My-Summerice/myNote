#  slince

**从数组或切片生成新的切片**

```go
a := [...][3]int{{1,2,3},3:{3}}
fmt.Println(a)
fmt.Println(a[1:3]) // 切数组的第2，3个元素，即a[1],a[2]
```
**输出**
```go
[[1 2 3] [0 0 0] [0 0 0] [3 0 0]]
[[0 0 0] [0 0 0]]
```
**直接声明新的切片**
```go
// 声明字符串切片
var strList []string
// 声明整型切片
var numList []int
// 声明一个空切片
var numListEmpty = []int{}
// 输出3个切片
fmt.Println(strList, numList, numListEmpty)
// 输出3个切片大小
fmt.Println(len(strList), len(numList), len(numListEmpty))
// 切片判定空的结果
fmt.Println(strList == nil)
fmt.Println(numList == nil)
fmt.Println(numListEmpty == nil)
```
**输出**
```go
[] [] []
0 0 0
true
true
false
```
**注意**：
* 取出元素不包含结束索引对应的值，可用`slice[len(slice)-n]`来获取切片的某个元素
* 当开始与结束位置均缺省时，该切片就等同于被操作的数组或切片
* 当两者都为0时，等效于空切片，一般用于切片复位，输出`[]`
* 切片是动态结构，只能与 `nil`判定相等，不能互相判定相等。声明新的切片后，可以使用 `append()`函数向切片中添加元素

**使用make()函数动态构造切片**

```go
make( []Type, size, cap )
```
例：
```go
a := make([]int, 2)
b := make([]int, 2, 10)
fmt.Println(a, b)
fmt.Println(len(a), len(b))
```
输出：
```go
[0 0] [0 0]
2 2
```
**Tips**:
使用**make()函数构造切片**与**从数组或切片中生成新的切片**有本质性的区别，make()函数构造切片会直接分配新的内存，而另一种则是将新的切片结构指向已分配好的内存区域，即引用

 ### append()

```go
var a []int
a = append(a, 1) // 追加1个元素
a = append(a, 1, 2, 3) // 追加多个元素
a = append(a, []int{1,2,3}...) // 追加一个切片,省略号不可省略
a = append(a, make([]int, 2)...) // 追加切片时使用make()创建一个切片
a = append(a, a[0:3]...) // 同上
```
输出：
```go
[1]
[1 2 3]
[1 2 3 0 0]
[1 2 3 0 0 1 2 3]
```

**注意**：
* 调换原切片和被添加元素位置可实现在切片头增加元素，例如：`a = append([]int{0}, a...)`，切片头添加元素会导致内存重新分配，而且会导致已有元素全部被复制一次，所以说切片头增加元素性能很差
* 在使用append()添加元素时,若切片**空间不足**则会**自动进行扩容**，每次扩容后空间变为原来的**两倍**，当一次性添加过多元素时**扩容2倍还不够用**，则会将cap**扩容为增加完元素后切片的len()+1**
* 每当发生扩容操作时，系统就会为切片重新分配内存，所以切片的地址也会发生改变

**append()嵌套&在指定位置添加元素**
```go
var a []int
a = append(a[:i], append([]int{x}, a[i:]...)...) // 在第i个位置插入x
a = append(a[:i], append([]int{1,2,3}, a[i:]...)...) // 在第i个位置插入切片
```

### copy()
`copy(destSlice, srcSlice []T) int`
其中 srcSlice 为数据来源切片，destSlice 为复制的目标（也就是将 srcSlice 复制到destSlice），目标切片必须分配过空间且足够承载复制的元素个数，并且来源和目标的类型必须一致，copy()函数的返回值表示实际发生复制的元素个数。

```go
slice1 := []int{1, 2, 3, 4, 5}
slice2 := []int{5, 4, 3}
copy(slice2, slice1) // 只会复制slice1的前3个元素到slice2中
copy(slice1, slice2) // 只会复制slice2的3个元素到slice1的前3个位置
```
copy() 函数的第一个参数是要复制的目标 slice，第二个参数是源 slice，两个 slice 可以共享同一个底层数组，甚至有重叠也没有问题。

#### 关于slice删除元素

* 直接移动数据指针（**会导致重新分配内存：地址和容量大小都发生改变**）：
```go
a = []int{1, 2, 3}
a = a[1:] // 删除开头1个元素
a = a[N:] // 删除开头N个元素

a = a[:len(a)-1] // 删除尾部1个元素
a = a[:len(a)-N] // 删除尾部N个元素
```
* 使用append()或copy()实现（**在原切片内存中进行操作所以不会改变内存大小与地址**）：
```go
a = []int{1, 2, 3, ...}
a = append(a[:i], a[i+1:]...) // 删除下标为i的元素
a = append(a[:i], a[i+N:]...) // 删除下标i到i+N-1的元素
a = a[:i+copy(a[i:], a[i+1:])] // 删除下标为i的元素
a = a[:i+copy(a[i:], a[i+N:])] // 删除下标为i到i+N-1的元素
```

总结：Go语言中删除切片元素的本质是，以被删除元素为分界点，将前后两个部分的内存重新连接起来。



