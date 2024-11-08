
运行下面的代码：



点击查看代码

```
package main

import "fmt"

const (
	Big = 1 << 100
)

func needFloat(x float64) float64 {
	//fmt.Printf("%T", Big)  // It is wrong！
	return x * 0.1
}

func main() {
	fmt.Println(needFloat(Big))  // 1.2676506002282295e+29
}

```


当我尝试print变量Big的类型时，发生了报错，如下：


./prog.go:10:19: cannot use Big (untyped int constant 1267650600228229401496703205376\) as int value in argument to fmt.Printf (overflows)


理由如下：
![image](https://img2024.cnblogs.com/blog/3196205/202411/3196205-20241107140701262-626156876.png)
一个方面是，将Big的值传递给任何函数都需要为其分配一个实际类型。非类型化int可以变成任何整数类型或任何浮点类型（这取决于函数参数类型）。Go中单个整数值可以具有的最大实际类型是uint64，但任何类型的规则（即fmt.Println的参数类型）都更具体。它们将没有任何其他类型信息的无类型int转换为int，在当前的CPU架构上为64位。这远远低于1\<\<100。
![image](https://img2024.cnblogs.com/blog/3196205/202411/3196205-20241107140856045-2062177826.png)
你现在可能想知道“为什么我们甚至有大的常数？”答案是，你可以在常数表达式中使用大（和类似大小的值）。这些在编译时进行评估，只有在使用了它们的值后才能得到实际的类型（粗略地说）。
所以你可以让const Small\=Big\>\>100，这相当于const Small\=1，例如可以使用该常量，因为它的值可以轻松地放入int中。
例如，在实践中出现的一个领域是计算地球表面各点之间的距离所需的计算。那里使用的大量表达式可以不断计算，这意味着与将所涉及的值分配给float64并对其进行数学运算相比，精度损失较小（因为浮点常数的精度是无限的）。


 本博客参考[楚门加速器](https://chuanggeye.com)。转载请注明出处！
