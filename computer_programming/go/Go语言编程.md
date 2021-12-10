Go语言编程
=========

## 1 初识Go语言

### 1.3 第一个Go程序

```
/*
 * hello.go
 * go run hello.go
 * go build hello.go
 * go tool compile hello.go
 * go tool link hello.o
 * ./a.out
 */
package main  // package main + func main() -> Go可执行程序

import "fmt"  // 导入依赖包, 不能包含未使用的依赖包

func main() {
    fmt.Println("Hello, World!")
}
```

### 1.5 工程管理

```
// calcproj/calc.go
package main

import "fmt"
import "nickmyb.com/calcproj/simplemath"

func main() {
    var ret = simplemath.Add(1, 2)
    fmt.Println(ret)
}
```

```
// calcproj/go.mod
module nickmyb.com/calcproj

go 1.17
```

```
// calcproj/simplemath/add.go
package simplemath

func Add(a int, b int) int {
    return a + b
}
```

## 2 顺序编程

### 2.1 变量

```
// 声明
var v3 [10]int  // 数组
var v4 []int  // 数组切片
var v5 struct {
    f int
}
var v6 *int
var v7 map[string]int
var v8 func(a int) int

var (
    v1 int
    v2 string
)

// 未声明的变量初始化
v3 := 10

// 赋值
var v10 int
v10 = 123
i, j = j, i
_, _, nickName := GetName()

// 常量
const Pi float64 = 3.14159265358979323846

const zero = 0.0  // 无类型常量

const Home = os.GetEnv("HOME")  // 常量不能出现运行期产生的结果

// true false iota(const -> 0)

const (
    c0 = iota
    c1  // const赋值语句一样可省略
    c2
)

const (
    Sunday = iota
    Monday
    Tuesday
    Wednesday
    Thursday
    Friday
    Saturday
    numberOfDays  // unexported
)
```

### 2.3 类型

- []type 数组是value type, 赋值和参数传递会产生复制操作
- value, ok := nMap[k]

### 2.7 完整示例

```
// sorter/go.mod
// go build
// go test "nickmyb.com/sorter/algorithms/bubblesort"
// ./sorter -i in.dat -o out.dat -a sort
module nickmyb.com/sorter

go 1.17
```

```
// sorter/sorter.go
package main

import "bufio"
import "flag"
import "fmt"
import "io"
import "os"
import "strconv"

import "nickmyb.com/sorter/algorithms/bubblesort"

// arg default desc
var inFile *string = flag.String("i", "inFile", "File contains values for sorting")
var outFile *string = flag.String("o", "outFile", "File to receive sorted values")
var algorithm *string = flag.String("a", "algorithm", "Sort algorithm")

func readValues(inFile string) (values []int, err error) {
    file, err := os.Open(inFile)
    if err != nil {
        fmt.Println("Fail to open", inFile)
        return
    }
    defer file.Close()

    br := bufio.NewReader(file)
    values = make([]int, 0)
    for {
        line, isPrefix, err1 := br.ReadLine()
        if err1 != nil {
            if err1 != io.EOF {
                err = err1
            }
            break
        }

        if isPrefix {
            fmt.Println("Line too long")
            break
        }

        str := string(line)
        value, err1 := strconv.Atoi(str)
        if err1 != nil {
           err = err1
           break
        }

        values = append(values, value)
    }

    return
}


func writeValues(values []int, outFile string) error {
    file, err := os.Create(outFile)
    if err != nil {
       fmt.Println("Fail to open", outFile)
       return err
    }
    defer file.Close()

    for _, v := range(values) {
        str := strconv.Itoa(v)
        file.WriteString(str + "\n")
    }
    return nil
}

func main() {
    flag.Parse()

    if inFile != nil {
        fmt.Println("inFile =", *inFile, "outFile =", *outFile, "algorithm =", *algorithm)

        values, err := readValues(*inFile)
        if err == nil {
            fmt.Println("values =", values)
        } else {
            fmt.Println("error =", err)
            return
        }

        bubblesort.BubbleSort(values)
        writeValues(values, *outFile)

        fmt.Println("sorted values =", values)
    }
}
```

```
// sorter/algorithms/bubblesort/bubblesort.go
package bubblesort

func BubbleSort(values []int) {
    for i := 0; i < len(values) - 1; i++ {
        for j := 0; j < len(values) - i - 1; j++ {
            if values[j] > values[j + 1] {
                values[j], values[j + 1] = values[j + 1], values[j]
            }
        }
    }
}
```

```
// sorter/algorithms/bubblesort/bubblesort_test.go
package bubblesort

import "testing"

func TestBubbleSort1(t *testing.T) {
    values := []int {5, 4, 3, 2, 1}
    BubbleSort(values)

    if values[0] != 1 || values[1] != 2 || values[2] != 3 || values[3] != 4 || values[4] != 5 {
        t.Error("BubbleSort failed:", values)
    }
}
```
