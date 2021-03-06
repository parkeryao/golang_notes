# golang基础
***

### go run
这个命令编译一个或多个以.go结尾的源文件，链接库文件，并运行最终生成的可 执行文件。

### go build
生成可执行的二进制文件，golang用的是静态编译，不用担心在系统库更新的时候冲突

>动态编译的可执行文件需要附带一个的动态链接库。在执行时，需要调用其对应动态链接库中的命令。所以其优点一方面是缩小了执行文件本身的体积，另一方面是加快了编译速度，节省了系统资源。缺点一是哪怕是很简单的程序，只用到了链接库中的一两条命令，也需要附带一个相对庞大的链接库；二是如果其他计算机上没有安装对应的运行库，则用动态编译的可执行文件就不能运行。
静态编译就是编译器在编译可执行文件的时候，将可执行文件需要调用的对应动态链接库(.so或.lib)中的部分提取出来，链接到可执行文件中去，使可执行文件在运行的时候不依赖于动态链接库。所以其优缺点与动态编译的可执行文件正好互补。

### Unicode
Go原生支持Unicode，可以处理世界上任何语言的文本

>Unicode（统一码、万国码、单一码）是计算机科学领域里的一项业界标准,包括字符集、编码方案等。Unicode 是为了解决传统的字符编码方案的局限而产生的，它为每种语言中的每个字符设定了统一并且唯一的二进制编码，以满足跨语言、跨平台进行文本转换、处理的要求。1990年开始研发，1994年正式公布。
因为计算机只能处理数字，如果要处理文本，就必须先把文本转换为数字才能处理。最早的计算机在设计时采用8个比特（bit）作为一个字节（byte），所以，一个字节能表示的最大的整数就是255（二进制11111111=十进制255），0 - 255被用来表示大小写英文字母、数字和一些符号，这个编码表被称为ASCII编码，比如大写字母A的编码是65，小写字母z的编码是122。
如果要表示中文，显然一个字节是不够的，至少需要两个字节，而且还不能和ASCII编码冲突，所以，中国制定了GB2312编码，用来把中文编进去。
类似的，日文和韩文等其他语言也有这个问题。为了统一所有文字的编码，Unicode应运而生。Unicode把所有语言都统一到一套编码里，这样就不会再有乱码问题了。
Unicode通常用两个字节表示一个字符，原有的英文编码从单字节变成双字节，只需要把高字节全部填为0就可以。

### 格式
函数左括号必须和func函数声明在同一行，且位于末尾

### 换行
在表达式 x + y 中，可以 + 后换行，不能在 + 前换行

### 命令行参数`os.Args`
* os.Args[0]: 是命令本身的名字
* os.Args[1:]: 程序启动时传入的参数

> 切片slice
区间索引时，Go言里也采用左闭右开形式, 即，区间包括第一个索引元素，不包括最后一个, 因为这样可以简化逻辑。（译注：比如a = [1, 2, 3, 4, 5], a[0:3] = [1, 2, 3]，不包含最后一个元素）。比如s[m:n]这个切片，0 ≤ m ≤ n ≤ len(s)，包含n-m个元素。

### 变量声明与初始化
变量会在声明时直接初始化。如果变量没有显式初始化，则被隐式地赋予其类型的零值（zero value），数值类型是0，字符串类型是空字符串""

符号`:=`是短变量声明（short variable declaration）

### 字符串拼接
```
s += sep + arg
```
`+=`连接原字符串、空格和下个参数，产生新字符串, 并把它赋值给s。s原来的内容已经不再使用，将在适当时机对它进行垃圾回收。如果连接涉及的数据量很大，这种方式代价高昂。一种简单且高效的解决方案是使用strings包的Join函数：
```
strings.Join(os.Args[1:], " ")
```

### 字符串拼接效率比较
下面是一个字符串拼接的测试，目的是把一个容量为10000的字符串数组拼接成一个字符串，前者用`+`直接拼接，后者用`strings.Join`，你可以看到，后者耗时明显少很多。

```GO
package main

import (
	"fmt"
	"strings"
	"time"
)

func main() {
	s, sep := "", ""

	// Make an string array with capacity of 10000
	arr := make([]string, 10000)

	for i := 0; i < 10000; i++ {
		arr[i] = "a"
	}

	// Concat the string array by "+="
	t1 := time.Now()
	for i := 0; i < 10000; i++ {
		s += arr[i] + sep
	}
	fmt.Println("Capacity of new string: ", len(s))
	fmt.Println("Time elapsed by concat using '+=' :", time.Since(t1))

	// Concat the string array by "Join"
	s, sep = "", ""
	t2 := time.Now()
	s = strings.Join(arr, sep)
	fmt.Println("Capacity of new string: ", len(s))
	fmt.Println("Time elapsed by concat using 'Join' :", time.Since(t2))
}
```

### map的键
键可以是任意类型，只要其值能用`==`运算符比较

### range循环
`map`的迭代顺序并不确定，从实践来看，该顺序随机，每次运行都会变化。这种设计是有意为之的，因为能防止程序依赖特定遍历顺序，而这是无法保证的。

### 格式化输出
|格式|描述|
|:-|:-|
|%d          |十进制整数|
|%x, %o, %b  |十六进制，八进制，二进制整数。|
|%f, %g, %e  |浮点数： 3.141593 3.141592653589793 3.141593e+00|
|%t          |布尔：true或false|
|%c          |字符（rune） (Unicode码点)|
|%s          |字符串|
|%q          |带双引号的字符串"abc"或带单引号的字符'c'|
|%v          |变量的自然形式（natural format）|
|%T          |变量的类型|
|%%          |字面上的百分号标志（无操作数）|

### 标准输入os.Stdin
在命令行输入过程中（标准输入`os.Stdin`），`Ctrl D`会告诉系统终止输入，从而后续的程序可以继续。
虽然是从命令行中输入，但在系统看来，这些输入就是一个文件，所以我们可以说`os.Stdin`就是一个`File`实例，这也和Linux中把所以资源都当作文件的这个思想

```GO
var (
        Stdin  = NewFile(uintptr(syscall.Stdin), "/dev/stdin")
        Stdout = NewFile(uintptr(syscall.Stdout), "/dev/stdout")
        Stderr = NewFile(uintptr(syscall.Stderr), "/dev/stderr")
)

```

### 声明和使用
函数和包级别的变量可以任意顺序声明，不影响其被调用，调用可以声明之前。

### 关于Exit(x)
```GO
// Exit causes the current program to exit with the given status code.
// Conventionally, code zero indicates success, non-zero an error.
// The program terminates immediately; deferred functions are not run.
func Exit(code int) {
	if code == 0 {
		// Give race detector a chance to fail the program.
		// Racy programs do not have the right to finish successfully.
		runtime_beforeExit()
	}
	syscall.Exit(code)
}
```

### Shell输入输出重定向
http://www.runoob.com/linux/linux-shell-io-redirections.html



