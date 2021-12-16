*The Go Programming Language*
=============================

- [官网](https://go.dev/)
- [Go语言圣经](https://book.itsfun.top/gopl-zh/)
- [github](https://github.com/gopl-zh/gopl-zh.github.com)
- [源码](https://github.com/adonovan/gopl.io)

```
$ export GOPATH=$HOME/gobook    # 选择工作目录
$ go get gopl.io/ch1/helloworld # 获取/编译/安装
$ $GOPATH/bin/helloworld        # 运行程序
```

## 1 入门

### 1.1 Hello, World

```
$ go run main.go
$ go build main.go
$ gofmt main.go
```

### 1.2 命令行参数

- os.Args[i]
    - 0: 命令本身
- i++是语句不是表达式
- for _, arg := range
- := 只能用在函数内部
- strings.Join(os.Args[1:], " ")

```
// 练习 1.1
package main

import (
    "fmt"
    "os"
)

func main() {
    s, sep := "", ""
    for _, arg := range os.Args {
        s += sep + arg
        sep = " "
    }
    fmt.Println(s)
}
```

```
// 练习 1.2
package main

import (
    "fmt"
    "os"
)

func main() {
    for index, arg := range os.Args {
        fmt.Println(index, arg)
    }
}
```

```
// TODO
// 练习 1.3
// 做实验测量潜在低效的版本和使用了strings.Join的版本的运行时间差异。（1.6节讲解了部分time包，11.4节展示了如何写标准测试程序，以得到系统性的性能评测。）
```

### 1.3 查找重复的行

- make(map[string]int)
- map的key需要支持==
- map[k]: k不存在时为默认零值

```
input := bufio.NewScanner(os.Stdin)
for input.Scan() {  // Scan()移除\n
}
```

```
// 练习 1.4
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	counts := make(map[string]int)
	files := os.Args[1:]
	if len(files) == 0 {
		countLines(os.Stdin, counts)
	} else {
		for _, arg := range files {
			f, err := os.Open(arg)
			if err != nil {
				fmt.Fprintf(os.Stderr, "dup2: %v\n", err)
				continue
			}
			countLines(f, counts)
			f.Close()
		}
	}
	for line, n := range counts {
		if n > 1 {
			fmt.Printf("%d\t%s\n", n, line)
		}
	}
}

func countLines(f *os.File, counts map[string]int) {
	input := bufio.NewScanner(f)
	for input.Scan() {
		counts[input.Text()]++
		if counts[input.Text()] > 1 {
			fmt.Println(f.Name())
		}
	}
	// NOTE: ignoring potential errors from input.Err()
}
```

### 1.4 GIF动画

- import "image/color": color.Xxx

```
// 练习 1.5
package main

import (
	"image"
	"image/color"
	"image/gif"
	"io"
	"math"
	"math/rand"
	"os"
	"time"
)

var palette = []color.Color{color.White, color.RGBA{0x00, 0xff, 0x00, 0xff}}

const (
	whiteIndex = 0 // first color in palette
	blackIndex = 1 // next color in palette
)

func main() {
	// The sequence of images is deterministic unless we seed
	// the pseudo-random number generator using the current time.
	// Thanks to Randall McPherson for pointing out the omission.
	rand.Seed(time.Now().UTC().UnixNano())
	lissajous(os.Stdout)
}

func lissajous(out io.Writer) {
	const (
		cycles  = 5     // number of complete x oscillator revolutions
		res     = 0.001 // angular resolution
		size    = 100   // image canvas covers [-size..+size]
		nframes = 64    // number of animation frames
		delay   = 8     // delay between frames in 10ms units
	)

	freq := rand.Float64() * 3.0 // relative frequency of y oscillator
	anim := gif.GIF{LoopCount: nframes}
	phase := 0.0 // phase difference
	for i := 0; i < nframes; i++ {
		rect := image.Rect(0, 0, 2*size+1, 2*size+1)
		img := image.NewPaletted(rect, palette)
		for t := 0.0; t < cycles*2*math.Pi; t += res {
			x := math.Sin(t)
			y := math.Sin(t*freq + phase)
			img.SetColorIndex(size+int(x*size+0.5), size+int(y*size+0.5),
				blackIndex)
		}
		phase += 0.1
		anim.Delay = append(anim.Delay, delay)
		anim.Image = append(anim.Image, img)
	}
	gif.EncodeAll(out, &anim) // NOTE: ignoring encoding errors
}
```

```
// 练习 1.6
package main

import (
	"image"
	"image/color"
	"image/gif"
	"io"
	"math"
	"math/rand"
	"os"
	"time"
)

// White, Green, Red, Blue
var palette = []color.Color{color.White, color.RGBA{G: 0xff, A: 0xff}, color.RGBA{R: 0xff, A: 0xff}, color.RGBA{B: 0xff, A: 0xff}}

const (
	whiteIndex = 0 // first color in palette
	blackIndex = 1 // next color in palette
)

func main() {
	// The sequence of images is deterministic unless we seed
	// the pseudo-random number generator using the current time.
	// Thanks to Randall McPherson for pointing out the omission.
	rand.Seed(time.Now().UTC().UnixNano())
	lissajous(os.Stdout)
}

func lissajous(out io.Writer) {
	const (
		cycles  = 5     // number of complete x oscillator revolutions
		res     = 0.001 // angular resolution
		size    = 100   // image canvas covers [-size..+size]
		nframes = 64    // number of animation frames
		delay   = 8     // delay between frames in 10ms units
	)

	freq := rand.Float64() * 3.0 // relative frequency of y oscillator
	anim := gif.GIF{LoopCount: nframes}
	phase := 0.0 // phase difference
	for i := 0; i < nframes; i++ {
		rect := image.Rect(0, 0, 2*size+1, 2*size+1)
		img := image.NewPaletted(rect, palette)
		for t := 0.0; t < cycles*2*math.Pi; t += res {
			x := math.Sin(t)
			y := math.Sin(t*freq + phase)
			colorIndex := uint8(rand.Int() % len(palette))
			img.SetColorIndex(size+int(x*size+0.5), size+int(y*size+0.5),
				colorIndex)
		}
		phase += 0.1
		anim.Delay = append(anim.Delay, delay)
		anim.Image = append(anim.Image, img)
	}
	gif.EncodeAll(out, &anim) // NOTE: ignoring encoding errors
}
```

### 1.5 获取URL

```
// 练习 1.7
package main

import (
	"fmt"
	"io"
	"net/http"
	"os"
)

func main() {
	for _, url := range os.Args[1:] {
		resp, err := http.Get(url)
		if err != nil {
			fmt.Fprintf(os.Stderr, "fetch: %v\n", err)
			os.Exit(1)
		}
		written, err := io.Copy(os.Stdout, resp.Body)
		resp.Body.Close()
		if err != nil {
			fmt.Fprintf(os.Stderr, "fetch: reading %s: %v\n", url, err)
			os.Exit(1)
		}
		fmt.Printf("Get(%s): %d\n", url, written)
	}
}
```

```
// 练习 1.8
package main

import (
	"fmt"
	"io/ioutil"
	"net/http"
	"os"
	"strings"
)

func main() {
	httpPrefix := "http://"
	for _, url := range os.Args[1:] {
		if !strings.HasPrefix(url, httpPrefix) {
			url = httpPrefix + url
		}
		resp, err := http.Get(url)
		if err != nil {
			fmt.Fprintf(os.Stderr, "fetch: %v\n", err)
			os.Exit(1)
		}
		b, err := ioutil.ReadAll(resp.Body)
		resp.Body.Close()
		if err != nil {
			fmt.Fprintf(os.Stderr, "fetch: reading %s: %v\n", url, err)
			os.Exit(1)
		}
		fmt.Printf("%s", b)
	}
}
```

```
// 练习 1.9
package main

import (
    "fmt"
    "io/ioutil"
    "net/http"
    "os"
)

func main() {
    for _, url := range os.Args[1:] {
        resp, err := http.Get(url)
        if err != nil {
            fmt.Fprintf(os.Stderr, "fetch: %v\n", err)
            os.Exit(1)
        }
        status := resp.Status
        b, err := ioutil.ReadAll(resp.Body)
        resp.Body.Close()
        if err != nil {
            fmt.Fprintf(os.Stderr, "fetch: reading %s: %v\n", url, err)
            os.Exit(1)
        }
        fmt.Printf("%s", b)
        fmt.Println("HTTP STATUS =", status)
    }
}
```

### 1.6 并发获取多个URL

```
// 练习 1.10
// TODO for go无法成功请求
package main

import (
	"errors"
	"fmt"
	"io"
	"net/http"
	"os"
	"strings"
	"time"
)

func main() {
	const TIMES = 2
	start := time.Now()
	ch := make(chan string)
	for _, url := range os.Args[1:] {
		for i := 0; i < TIMES; i++ {
			go fetch(url, ch) // start a goroutine
		}
	}
	for range os.Args[1:] {
		fmt.Println(<-ch) // receive from channel ch
	}
	fmt.Printf("%.2fs elapsed\n", time.Since(start).Seconds())
}

func fetch(url string, ch chan<- string) {
	start := time.Now()
	resp, err := http.Get(url)
	if err != nil {
		ch <- fmt.Sprint(err) // send to channel ch
		return
	}

	var f *os.File
	pwd := "/tmp/GoLand/"
	url = strings.Split(url, "/")[2]
	if _, err = os.Stat(pwd + url); errors.Is(err, os.ErrNotExist) {
		f, err = os.Create(pwd + url)
		if err != nil {
			ch <- fmt.Sprint(err) // send to channel ch
			return
		}
	} else {
		f, err = os.Create(pwd + url + ".backup")
		if err != nil {
			ch <- fmt.Sprint(err) // send to channel ch
			return
		}
	}
	nbytes, err := io.Copy(f, resp.Body)
	resp.Body.Close() // don't leak resources
	if err != nil {
		ch <- fmt.Sprintf("while reading %s: %v", url, err)
		return
	}
	secs := time.Since(start).Seconds()
	ch <- fmt.Sprintf("%.2fs  %7d  %s", secs, nbytes, url)
}
```

```
// 练习 1.11
// TODO
// 在fetchall中尝试使用长一些的参数列表，比如使用在alexa.com的上百万网站里排名靠前的。如果一个网站没有回应，程序将采取怎样的行为？（Section8.9 描述了在这种情况下的应对机制）。
```

### 1.4 Web服务

- sync.Mutex: Lock() / Unlock()

```
// TODO can't show gif in firefox
// main.go
package main

import (
	"image"
	"image/color"
	"image/gif"
	"io"
	"math"
	"math/rand"
	"os"
)

//!-main
// Packages not needed by version in book.
import (
	"log"
	"net/http"
	"strconv"
	"time"
)

//!+main

var palette = []color.Color{color.White, color.Black}

const (
	whiteIndex = 0 // first color in palette
	blackIndex = 1 // next color in palette
)

var cycleParam int

func main() {
	//!-main
	// The sequence of images is deterministic unless we seed
	// the pseudo-random number generator using the current time.
	// Thanks to Randall McPherson for pointing out the omission.
	rand.Seed(time.Now().UTC().UnixNano())

	if len(os.Args) > 1 && os.Args[1] == "web" {
		//!+http
		handler := func(w http.ResponseWriter, r *http.Request) {
			if err := r.ParseForm(); err != nil {
				log.Print(err)
			}
			for k, v := range r.Form {
				if k == "cycles" {
					var err error
					cycleParam, err = strconv.Atoi(v[0])
					if err != nil {
						log.Print(err)
						cycleParam = 0
					}
				}
			}

			lissajous(w)
		}
		http.HandleFunc("/", handler)
		//!-http
		log.Fatal(http.ListenAndServe("localhost:8000", nil))
		return
	}
	//!+main
	lissajous(os.Stdout)
}

func lissajous(out io.Writer) {
	const (
		cycles  = 5     // number of complete x oscillator revolutions
		res     = 0.001 // angular resolution
		size    = 100   // image canvas covers [-size..+size]
		nframes = 64    // number of animation frames
		delay   = 8     // delay between frames in 10ms units
	)

	var cyc float64
	if cycleParam > 0 {
		cyc = float64(cycleParam)
	} else {
		cyc = cycles
	}

	freq := rand.Float64() * 3.0 // relative frequency of y oscillator
	anim := gif.GIF{LoopCount: nframes}
	phase := 0.0 // phase difference
	for i := 0; i < nframes; i++ {
		rect := image.Rect(0, 0, 2*size+1, 2*size+1)
		img := image.NewPaletted(rect, palette)
		for t := 0.0; t < cyc*2*math.Pi; t += res {
			x := math.Sin(t)
			y := math.Sin(t*freq + phase)
			img.SetColorIndex(size+int(x*size+0.5), size+int(y*size+0.5),
				blackIndex)
		}
		phase += 0.1
		anim.Delay = append(anim.Delay, delay)
		anim.Image = append(anim.Image, img)
	}
	gif.EncodeAll(out, &anim) // NOTE: ignoring encoding errors
}
```

```
// gif.go
package main

import (
	"image"
	"image/color"
	"image/gif"
	"io"
	"math"
	"math/rand"
)

var palette = []color.Color{color.White, color.Black}

const (
	whiteIndex = 0 // first color in palette
	blackIndex = 1 // next color in palette
)

func lissajous(out io.Writer, cycles int) {
	const (
		// cycles  = 5     // number of complete x oscillator revolutions
		res     = 0.001 // angular resolution
		size    = 100   // image canvas covers [-size..+size]
		nframes = 64    // number of animation frames
		delay   = 8     // delay between frames in 10ms units
	)

	freq := rand.Float64() * 3.0 // relative frequency of y oscillator
	anim := gif.GIF{LoopCount: nframes}
	phase := 0.0 // phase difference
	for i := 0; i < nframes; i++ {
		rect := image.Rect(0, 0, 2*size+1, 2*size+1)
		img := image.NewPaletted(rect, palette)
		for t := 0.0; t < float64(cycles)*2*math.Pi; t += res {
			x := math.Sin(t)
			y := math.Sin(t*freq + phase)
			img.SetColorIndex(size+int(x*size+0.5), size+int(y*size+0.5),
				blackIndex)
		}
		phase += 0.1
		anim.Delay = append(anim.Delay, delay)
		anim.Image = append(anim.Image, img)
	}
	gif.EncodeAll(out, &anim) // NOTE: ignoring encoding errors
}
```

### 1.8 本章要点

## 2 程序结构

### 2.1 命名

- [字母_][字母数字_]*
- 首字母大写才是包外可见
- 驼峰
- 缩略词保留大写

### 2.2 声明

- var const type func

### 2.3 变量

- var name type = expression
    - omit type
    - omit expression -> value zero
- := 简短变量声明,函数内部才可以使用,若是外部词法域声明的变量会新声明一个变量
    - 变量: 可寻址的值, *p: 变量的别名
    - new(T) = *T
        - 预定义函数,可以被同名变量覆盖
        - 类型为空可能返回相同地址, struct{} & [0]int
    - )另起一行缩进可以在前一行末尾增加,
    - 一个变量的有效周期只取决于是否可达

### 2.4 赋值

- i++: 语句不是表达式
- v, ok = m[key] x.(T) <-ch
- v = m[key] x.(T) <-ch
    - x.(T) -> panic, m[key] <-ch 返回零值
- ==: 第二个值必须是对第一个值类型对应的变量是可赋值的，反之亦然

### 2.5 类型

- 只有当两个类型的底层基础类型相同时才允许转型,转换只改变类型而不会影响值本身
- 类型相同才可以比较
- 命名类型还可以为该类型的值定义新的行为
    - type Celsius float64
    - func (c Celsius) String() string { return fmt.Sprintf("%g°C", c) }

### 2.6 包和文件

```
// 练习 2.1
// demo/main.go
package main

import (
	"nickmyb.com/demo/tempconv"
	"fmt"
)

func main() {
	var k tempconv.Kelvin = tempconv.CToK(12)
	fmt.Println(k)
}
```

```
// demo/go.mod
module nickmyb.com/demo

go 1.17
```

```
// demo/tempconv/conv.go
// Copyright © 2016 Alan A. A. Donovan & Brian W. Kernighan.
// License: https://creativecommons.org/licenses/by-nc-sa/4.0/

// See page 41.

//!+

package tempconv

// CToF converts a Celsius temperature to Fahrenheit.
func CToF(c Celsius) Fahrenheit { return Fahrenheit(c*9/5 + 32) }

// FToC converts a Fahrenheit temperature to Celsius.
func FToC(f Fahrenheit) Celsius { return Celsius((f - 32) * 5 / 9) }

// CToK converts a Celsius temperature to Kelvin.
func CToK(c Celsius) Kelvin { return Kelvin(c) }

// KToC converts a Kelvin temperature to Celsius.
func KToC(k Kelvin) Celsius { return Celsius(k) }

//!-
```

```
// demo/tempconv/tempconv.go
// Copyright © 2016 Alan A. A. Donovan & Brian W. Kernighan.
// License: https://creativecommons.org/licenses/by-nc-sa/4.0/

//!+

// Package tempconv performs Celsius and Fahrenheit conversions.
package tempconv

import "fmt"

type Celsius float64
type Fahrenheit float64
type Kelvin float64

const (
	AbsoluteZeroC Celsius = -273.15
	FreezingC     Celsius = 0
	BoilingC      Celsius = 100
)

func (c Celsius) String() string    { return fmt.Sprintf("%g°C", c) }
func (f Fahrenheit) String() string { return fmt.Sprintf("%g°F", f) }
func (k Kelvin) String() string     { return fmt.Sprintf("%g°K", k) }

//!-
```

- import "${go.mod.module}/${package}

```
// 练习 2.2
package main

import (
	"flag"
	"fmt"
	"os"
	"strconv"
)

type Foot float64
type Meter float64
type Pound float64
type Kilogram float64

func MToF(m Meter) Foot {
	return Foot(m * 3.2808)
}

func FToM(m Foot) Meter {
	return Meter(m / 3.2808)
}

func KToP(k Kilogram) Pound {
	return Pound(k * 2.2046)
}

func PToK(p Pound) Kilogram {
	return Kilogram(p / 2.2046)
}

func (m Meter) String() string {
	return fmt.Sprintf("%g M", m)
}

func (f Foot) String() string {
	return fmt.Sprintf("%g F", f)
}

func (p Pound) String() string {
	return fmt.Sprintf("%g lb", p)
}

func (k Kilogram) String() string {
	return fmt.Sprintf("%g kg", k)
}

func main() {
	length := flag.Bool("l", false, "length conversions")
	weight := flag.Bool("w", false, "weight conversions")

	flag.Parse()

	if len(os.Args) < 3 {
		fmt.Println("param missed")
		os.Exit(1)
	}
	arg := os.Args[2]
	n, err := strconv.ParseFloat(arg, 64)
	if err != nil {
		fmt.Fprintf(os.Stderr, "n: %v\n", err)
		os.Exit(1)
	}

	switch {
	case *length:
		m := Meter(n)
		f := Foot(n)
		fmt.Printf("%s = %s, %s = %s\n", m, MToF(m), f, FToM(f))
	case *weight:
		k := Kilogram(n)
		p := Pound(n)
		fmt.Printf("%s = %s, %s = %s\n", k, KToP(k), p, PToK(p))
	default:
		fmt.Println("--help")
	}
}
```

- 包的初始化首先是解决包级变量的依赖顺序，然后按照包级变量声明出现的顺序依次初始化
- init初始化函数不能被调用或引用，在程序开始执行时按照它们声明的顺序被自动调用
- 每个包在解决依赖的前提下，以导入声明的顺序初始化，每个包只会被初始化一次
- init() / 匿名函数初始化

```
// 练习 2.3
// 重写PopCount函数，用一个循环代替单一的表达式。比较两个版本的性能。（11.4节将展示如何系统地比较两个不同实现的性能。）
// TODO
package popcount

// pc[i] is the population count of i.
var pc [256]byte

func init() {
	for i := range pc {
		pc[i] = pc[i/2] + byte(i&1)
	}
}

// PopCount returns the population count (number of set bits) of x.
func PopCount(x uint64) int {
	var ret byte
	for i := 0; i < 8; i++ {
		ret += pc[byte(x>>(i*8))]
	}
	return int(ret)
}
```

```
// 练习 2.4
// 用移位算法重写PopCount函数，每次测试最右边的1bit，然后统计总数。比较和查表算法的性能差异。
// TODO
```

```
// 练习 2.5
//  表达式x&(x-1)用于将x的最低的一个非零的bit位清零。使用这个算法重写PopCount函数，然后比较性能。
// TODO
```

### 2.7 作用域

- 词法块

```
// 这段有点坑
func main() {
    x := "hello!"
    for i := 0; i < len(x); i++ {
        x := x[i]
        if x != '!' {
            x := x + 'A' - 'a'
            fmt.Printf("%c", x) // "HELLO" (one letter per iteration)
        }
    }
}
```

```
// Go语言的习惯是在if中处理错误然后直接返回，这样可以确保正常执行的语句不需要代码缩进
f, err := os.Open(fname)
if err != nil {
    return err
}
f.ReadByte()
f.Close()
```

```
var cwd string

func init() {
	// cwd, err := os.Getwd() // NOTE: wrong!
    var err error
    cwd, err = os.Getwd()
    if err != nil {
        log.Fatalf("os.Getwd failed: %v", err)
    }
}
```

## 3 基础数据类型

### 3.1 整型

- (u)int[8|16|32|64]: 有无符号, x bit
- (u)int: 对应特定CPU平台机器字大小, 32/64bit
- rune <=> int32, byte <=> uint8
- uintptr: 没有指定具体的bit大小但是足以容纳指针
- 使用无符号数进行左移右移
	- 有符号数的右移运算会用符号位的值填充左边空缺的bit位
	- 无符号数左移右移都是填充0

### 3.2 浮点数

- 一般使用float64

```
// 练习 3.1 - 3.4
// TODO
```

### 3.3 复数

- complex[64|128] -> float[32|64]
- complex real imag
- math/cmplx

```
// 练习 3.5 - 3.9
// TODO
```

### 3.4 布尔型

- 优先级 && > ||

### 3.5 字符串

- 字符串: 指针 + len
- 字符串是一个不可改变的字节序列,可以包含byte=0
- len: 字节数目（不是rune字符数目）
- s[i]返回第i个字节的字节值
- s[7:] & s 共享相同内存
- \ooo 255 <= \377
- \xhh
- ``: 没有转义操作 "`" = "0x60"
- code point: rune = int32
- UTF-8: 变长字节序列
- unicode/utf8
- 码点大于256 "\uhhhh" "\Uhhhhhhhh"
- 码点小于256 \xhh
- []rune(s) string([]rune) string(int)
- packages: bytes strings strconv unicode

```
// 练习 3.10 - 3.12
// TODO
```

### 3.6 常量

- 常量在编译期就确定
- const iota

```
// 练习 3.13
// TODO
```

- 无类型常量

## 4 复合数据类型

### 4.1 数组

- var r [3]int = [3]int{1, 2}  // r[2] = 0
- r := [...]int{99: -1}

```
func zero(ptr *[32]byte) {
	// for i, v := range ptr {
    for i := range ptr {
        ptr[i] = 0
    }
}
```

```
// 练习 4.1 - 4.2
// TODO
```

### 4.2 Slice

- []T: data: 指向第一个slice元素的指针 len cap
- slice拓展不超过cap即可
- == 不能比较slice,只能比较nil
- bytes.Equal可以比较[]byte
- slice的元素是间接引用的,[]interface{}时slice的元素可以是自身,slice扩容就会导致其本身的值/地址变化
- []int{} make([]int, 3)[3:] []int(nil)
- make([]T, len, cap)

#### 4.2.1 append函数

- x = append(x, x...)
- copy

```
// 练习 4.3 - 4.7
// TODO
```

### 4.3 Map

- map[K]V, k需要支持==,不建议使用浮点数作为k
- make(map[string]int) / map[string]int{}
- 不存在的k返回零值
- delete(ages, "alice")
- 禁止对v的取址操作: &m[k], 元素增长可能分配更大空间使之前地址无效
- map写入数据前需要先make

```
// map equal
func equal(x, y map[string]int) bool {
    if len(x) != len(y) {
        return false
    }
    for k, xv := range x {
        if yv, ok := y[k]; !ok || yv != xv {
            return false
        }
    }
    return true
}
```

- 对于k为slice类似不支持==的类型,使用辅助函数转可比较类型(string, struce, int, [n]T)确保一一对应即可

```
// 练习 4.8 - 4.9
// TODO
```

### 4.4 结构体

- structAddress.Positio = (*structAddress).Positio
- 调用函数返回的是值,赋值语句左边需要是一个变量
- 成员顺序不同的结构体是不同的
- 结构体不能包含自身类型的成员但可以包含自身指针的成员
- 注意结构体的未导出成员

```
package p
type T struct{ a, b int } // a and b are not exported

package q
import "p"
var _ = p.T{a: 1, b: 2} // compile error: can't reference a, b
var _ = p.T{1, 2}       // compile error: can't reference a, b
```

- 结构体的所有成员可以比较结构体就可以比较
- 结构体匿名成员只声明类型(命名的类型或指向一个命名的类型的指针),可以省略匿名成员部分路径(匿名成员需要导出),外层的结构体不仅仅是获得了匿名成员类型的所有成员，而且也获得了该类型导出的全部的方法
- 结构体初始化}换行前需要添加,

### 4.5 JSON

- 只有导出的结构体成员才会被编码
- Tag: \`json:"color,omitempty"\`
- json.Marshal[Indent]

```
// 练习 4.10 - 4.13
// TODO
```

### 4.6 文本和HTML模板

- TODO

```
// 练习 4.14
// TODO
```

## 5 函数

### 5.1 函数声明

```
func name(parameter-list) (result-list) {
    body
}
```

- Go语言没有默认参数值
- 没有函数体的函数声明表示该函数不是以Go实现

### 5.2 递归

- slice改变的时候改变data len cap,slice本身的地址是不会改变的

```
// 练习 5.1 - 5.4
// TODO
```

### 5.3 多返回值

- 垃圾回收回收内存但不回收操作系统层面的资源
- 一个函数所有的返回值都有显式的变量名，return语句可以省略操作数

```
// 练习 5.5 - 5.6
// TODO
```

### 5.4 错误

#### 5.4.1 错误处理策略

- 传播错误
- 错误信息避免大写和换行
- retry
- 输出错误信息并结束程序: os.Exit(1)
	- log会输出时间
- 输出错误信息继续执行
- 直接忽略掉错误并写下注释
- 处理失败在成功前,成功的逻辑代码不应该在else中
	- 先初始检查后实际逻辑

#### 5.4.2 文件结尾错误EOF

### 5.5 函数值

- 函数是first-class values
- 函数拥有类型
- 函数值之间不可以比较(closures存在局部变量的引用),只可以和nil比较

```
// 练习 5.7 - 5.9
// TODO
```

### 5.6 匿名函数

- 拥有函数名的函数只能在包级语法块中被声明
- 当匿名函数需要被递归调用时必须首先声明一个变量

```
// 练习 5.10 - 5.14
// TODO
```

#### 5.6.1 警告：捕获迭代变量

- 和Python迭代相同的问题
	- go / defer需要注意
	- 引入一个与循环变量同名的局部变量
	- 没有立即调用都是循环结束后才执行

### 5.7 可变参数

- 声明: func name(vals ...T)
- 调用: name(s...), 只能使用slice不能用数组
- ...T 和 []T 作为参数函数的签名是不同的

```
// 练习 5.15 - 5.17
// TODO
```

### 5.8 Deferred函数

- 函数和参数表达式得到计算，但直到包含该defer语句的函数执行完毕时，defer后的函数才会被执行
	- 只有defer的最后一层调用会在最后被执行
	- defer非最后一层调用会被先执行
- defer执行顺序与声明顺序相反
- defer对返回值的修改会影响最终的返回值
- 循环体中的defer用单独函数封装以便正常关闭

```
// 练习 5.18
// TODO
```

### 5.9 Panic异常

- 一般情况不需要断言自己调用panic
- panic会引起程序的崩溃,优先使用错误机制
- defer的调用在释放堆栈信息之前

### 5.10 Recover捕获异常

- 设置成特殊类型panic value才应该被恢复
- 一般在defer中调用recover()
- fatal错误无法recover

```
// 练习 5.19
// TODO
```
