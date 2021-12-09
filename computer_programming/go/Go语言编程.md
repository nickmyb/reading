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
