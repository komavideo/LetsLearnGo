使用math库
==========

Go 语言的 math 库提供了大量用于数学运算的函数。

## 创建新项目

```bash
mkdir goapp
cd goapp
go mod init goapp

echo 'package main

import "fmt"

func main() {
	fmt.Println("Hello, World!")
}' > main.go

go run main.go
```

## Go 语言 math 库

### 1. 基础数学函数:

+ `Abs`: 返回绝对值。
+ `Ceil`: 向上取整。
+ `Floor`: 向下取整。
+ `Round`: 四舍五入。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.Abs(-5.5))  // 输出: 5.5
    fmt.Println(math.Ceil(5.4))  // 输出: 6
    fmt.Println(math.Floor(5.6)) // 输出: 5
    fmt.Println(math.Round(5.5)) // 输出: 6
}
```

### 2. 幂与对数函数:

+ `Pow`: 计算幂运算。
+ `Sqrt`: 开方。
+ `Log`: 计算自然对数。


```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.Pow(2, 3))  // 输出: 8
    fmt.Println(math.Sqrt(9))   // 输出: 3
    fmt.Println(math.Log(2.71)) // 输出: 0.996
}
```

### 3. 三角函数:

`Sin`, `Cos`, `Tan`: 常用的三角函数。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.Sin(math.Pi / 2)) // 输出: 1
    fmt.Println(math.Cos(math.Pi))    // 输出: -1
    fmt.Println(math.Tan(math.Pi / 4)) // 输出: 1
}
```

### 4. 常量:

Go 的 `math` 库还定义了一些常用的常量，例如 `Pi` 和 `E`。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.Pi) // 输出: 3.141592653589793
    fmt.Println(math.E)  // 输出: 2.718281828459045
}
```

### 5. 最大值和最小值函数:

+ `Max`: 返回两个数中的最大值。
+ `Min`: 返回两个数中的最小值。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.Max(2.3, 2.9)) // 输出: 2.9
    fmt.Println(math.Min(2.3, 2.9)) // 输出: 2.3
}
```

### 6. 指数函数:

+ `Exp`: 返回 `e` 的指定次数幂。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.Exp(1)) // 输出: 2.718281828459045 (即 e 的 1 次幂)
}
```

### 7. 取模与余数函数:

+ `Mod`: 返回两个数相除的余数。
+ `Remainder`: 返回两个数相除的余数，但它遵循IEEE754的规则。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.Mod(11, 3))        // 输出: 2
    fmt.Println(math.Remainder(11, 3)) // 输出: 2
}
```

### 8. 角度与弧度转换:

+ `DegToRad`: 将角度转换为弧度。
+ `RadToDeg`: 将弧度转换为角度。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.DegToRad(180)) // 输出: 3.141592653589793 (即 π)
    fmt.Println(math.RadToDeg(math.Pi)) // 输出: 180
}
```

### 9. 双曲函数:

`Sinh`, `Cosh`, `Tanh`: 常用的双曲函数。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.Sinh(1)) // 输出: 1.1752011936438014
    fmt.Println(math.Cosh(1)) // 输出: 1.5430806348152437
    fmt.Println(math.Tanh(1)) // 输出: 0.7615941559557649
}
```

### 10. 判断函数:

+ `IsInf`: 检查一个数字是否是正无穷或负无穷。
+ `IsNaN`: 检查一个数字是否是非数值 (Not a Number)。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.IsInf(1.0 / 0.0, 1)) // 输出: true (检查是否是正无穷)
    fmt.Println(math.IsInf(-1.0 / 0.0, -1)) // 输出: true (检查是否是负无穷)
    fmt.Println(math.IsNaN(0.0 / 0.0)) // 输出: true
}
```

### 11. 对数函数的拓展:

+ `Log10`: 计算以10为底的对数。
+ `Log2`: 计算以2为底的对数。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.Log10(100)) // 输出: 2
    fmt.Println(math.Log2(8))    // 输出: 3
}
```

### 12. 浮点数的位操作:

+ `Float32bits`: 将一个 `float32` 值转为其二进制表示的 `uint32`。
+ `Float64bits`: 将一个 `float64` 值转为其二进制表示的 `uint64`。
+ `Float32frombits`: 将一个 `uint32` 值转为其浮点数表示的 `float32`。
+ `Float64frombits`: 将一个 `uint64` 值转为其浮点数表示的 `float64`。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    f := 1.5
    bits := math.Float64bits(f)
    fmt.Println(bits) // 输出: 4607632778762754458 (这是1.5的二进制表示)

    b := uint64(4607632778762754458)
    f2 := math.Float64frombits(b)
    fmt.Println(f2) // 输出: 1.5
}
```

### 13. 特殊函数:

+ `Gamma`: 计算 Gamma 函数的值。
+ `Erf`: 计算误差函数的值。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.Gamma(5)) // 输出: 24 (4的阶乘)
    fmt.Println(math.Erf(1))   // 输出: 0.8427007929497149
}
```

### 14. 正弦和余弦的组合:

`Sincos`: 同时计算一个数字的正弦和余弦值。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    sin, cos := math.Sincos(math.Pi / 4)
    fmt.Println(sin) // 输出: 0.7071067811865475 (即 √2/2)
    fmt.Println(cos) // 输出: 0.7071067811865475 (即 √2/2)
}
```

### 15. 特殊整数函数:

+ `Ilogb`: 返回一个数字的二进制指数。
+ `Signbit`: 检查数字的符号位，返回 true 如果数字是负的。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.Ilogb(8))       // 输出: 3 (因为8 = 2^3)
    fmt.Println(math.Signbit(-3.14)) // 输出: true
    fmt.Println(math.Signbit(3.14))  // 输出: false
}
```

### 16. 数字舍入:

`Trunc`: 将数字截断为整数部分。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.Trunc(3.9)) // 输出: 3
    fmt.Println(math.Trunc(-3.9)) // 输出: -3
}
```

### 17. 其他数学函数:

+ `Atan2`: 两个参数的反正切函数，返回的是 [-π, π] 之间的值。
+ `Hypot`: 返回其两个参数的欧几里得范数

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.Atan2(1, 1)) // 输出: 0.7853981633974483 (即 π/4)
    fmt.Println(math.Hypot(3, 4)) // 输出: 5 (因为 3^2 + 4^2 的平方根是 5)
}
```

### 18. 贝塞尔函数:

+ `J0`, `J1`: 一阶和二阶的第一类贝塞尔函数。
+ `Y0`, `Y1`: 一阶和二阶的第二类贝塞尔函数。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.J0(1)) // 输出: 0.7651976865579666
    fmt.Println(math.J1(1)) // 输出: 0.4400505857449335
    fmt.Println(math.Y0(1)) // 输出: 0.08825696421567697
    fmt.Println(math.Y1(1)) // 输出: -0.7812128213002887
}
```

### 19. 浮点数的规范化形式:

+ `Frexp`: 将浮点数分解为尾数和指数。
+ `Ldexp`: 通过尾数和指数计算浮点数。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    frac, exp := math.Frexp(12.5)
    fmt.Println(frac, exp)  // 输出: 0.78125 4

    value := math.Ldexp(frac, exp)
    fmt.Println(value)  // 输出: 12.5
}
```

### 20. 余数的另一种形式:

+ `Modf`: 将浮点数分解为整数部分和小数部分。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    intPart, fracPart := math.Modf(12.345)
    fmt.Println(intPart)  // 输出: 12
    fmt.Println(fracPart) // 输出: 0.345
}
```

### 21. 数字的复数形式:

+ `Complex`: 生成复数。
+ `Real`: 获取复数的实部。
+ `Imag`: 获取复数的虚部。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    c := complex(1, 2)
    fmt.Println(c)          // 输出: (1+2i)
    fmt.Println(real(c))    // 输出: 1
    fmt.Println(imag(c))    // 输出: 2
}
```

### 22. 数字比较:

+ `Max`: 返回两个数中的最大值。
+ `Min`: 返回两个数中的最小值。
+ `Dim`: 如果 x > y，返回 x-y，否则返回 0。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.Max(3.2, 4.5))  // 输出: 4.5
    fmt.Println(math.Min(3.2, 4.5))  // 输出: 3.2
    fmt.Println(math.Dim(7.2, 5.5))  // 输出: 1.7
    fmt.Println(math.Dim(3.2, 4.5))  // 输出: 0
}
```

### 23. 对数函数拓展:

+ `Log1p`: 计算 1 + x 的自然对数。
+ `ExpM1`: 计算 e^x - 1。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.Log1p(2))  // 输出: 1.0986122886681096
    fmt.Println(math.ExpM1(1))  // 输出: 1.718281828459045
}
```

### 24. 双曲函数的逆:

+ `Asinh`: 反双曲正弦。
+ `Acosh`: 反双曲余弦。
+ `Atanh`: 反双曲正切。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.Asinh(1))   // 输出: 0.881373587019543
    fmt.Println(math.Acosh(2))   // 输出: 1.3169578969248166
    fmt.Println(math.Atanh(0.5)) // 输出: 0.5493061443340549
}
```

### 25. 数字的逆运算:

+ `Cbrt`: 计算数字的立方根。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.Cbrt(27))  // 输出: 3
}
```

### 26. 三角函数的逆:

`Acos`, `Asin`, `Atan`: 反余弦、反正弦和反正切函数。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    fmt.Println(math.Acos(0.5))   // 输出: 1.0471975511965976
    fmt.Println(math.Asin(0.5))   // 输出: 0.5235987755982988
    fmt.Println(math.Atan(1))     // 输出: 0.7853981633974483
}
```

### 27. 复数的模和角:

+ `Abs`: 返回复数的模。
+ `Phase`: 返回复数的角，范围为 [-π, π]。

```go
package main

import (
    "fmt"
    "math"
    "math/cmplx"
)

func main() {
    c := complex(3, 4)
    fmt.Println(cmplx.Abs(c))   // 输出: 5 (因为模是√(3^2 + 4^2))
    fmt.Println(cmplx.Phase(c)) // 输出: 0.9272952180016122 (即arctan(4/3))
}
```

### 28. 复数的三角函数:

`Sin`, `Cos`, `Tan`: 返回复数的正弦、余弦和正切。

```go
package main

import (
    "fmt"
    "math/cmplx"
)

func main() {
    c := complex(1, 2)
    fmt.Println(cmplx.Sin(c))
    fmt.Println(cmplx.Cos(c))
    fmt.Println(cmplx.Tan(c))
}
```

### 29. 复数的双曲函数:

`Sinh`, `Cosh`, `Tanh`: 返回复数的双曲正弦、双曲余弦和双曲正切。

```go
package main

import (
    "fmt"
    "math/cmplx"
)

func main() {
    c := complex(1, 2)
    fmt.Println(cmplx.Sinh(c))
    fmt.Println(cmplx.Cosh(c))
    fmt.Println(cmplx.Tanh(c))
}
```

### 30. 复数的逆三角和双曲函数:

`Acos`, `Asin`, `Atan`, `Acosh`, `Asinh`, `Atanh`: 返回复数的逆三角函数和逆双曲函数。

```go
package main

import (
    "fmt"
    "math/cmplx"
)

func main() {
    c := complex(1, 2)
    fmt.Println(cmplx.Acos(c))
    fmt.Println(cmplx.Asin(c))
    fmt.Println(cmplx.Atan(c))
    fmt.Println(cmplx.Acosh(c))
    fmt.Println(cmplx.Asinh(c))
    fmt.Println(cmplx.Atanh(c))
}
```

### 31. 复数的对数和指数函数:

+ `Log`: 返回复数的自然对数。
+ `Exp`: 返回 `e` 的复数次幂。

```go
package main

import (
    "fmt"
    "math/cmplx"
)

func main() {
    c := complex(1, 2)
    fmt.Println(cmplx.Log(c))
    fmt.Println(cmplx.Exp(c))
}
```

Done.
