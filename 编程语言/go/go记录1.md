<center><h1>go记录</h1></center>
# 1 go简介

**软件开发的挑战**
1. 多核硬件架构
2. 超大规模分布式计算集群
3. Web模式导致的前所未有的开发规模和更新速度

**- 简单:**25关键字
**- 高效:**垃圾回收、指针
**- 生产力:**复合

# 2 第一个程序
**1.  开发环境**
GOPATH在1.8版本前必须设置这个环境变量
1.8版本后（含1.8）如果没有设置使用默认值
在Unix上默认为$HOME/go,在Windows上默认为%USERPROFILE%/go

**2. 基本程序结构**
```go
package main1 //包，表明代码所在的模块（包）

import "fmt" //引入代码依赖

//功能实现
func main() {
   fmt.Println("Hello World!")
}
```

**3. 应用程序入口**
- 必须是main包
- package main
- 必须是main方法
- func main()
- 文件名不一定是main.go

**4. 退出返回值**
- Go中main函数不支持任何返回值
- 通过os.Exit来返回状态

**5. 获取命令行参数**

- main 函数不支持传入参数
func main(arg []string)
- 在程序中直接通过 os.Args 获取命令行参数

# 3 数据类型
1. GO语言不允许隐式类型转换
2. 别名和原有类型也不能进行隐式类型转换

**&^**按位置零
```C
1 &^ 0 -- 1
1 &^ 1 -- 0
0 &^ 1 -- 0
0 &^ 0 -- 0

/*
1、如果右侧是0，则左侧数保持不变
2、如果右侧是1，则左侧数一定清零
3、功能同a&(^b)相同
4、如果左侧是变量，也等同于：
*/
```
## 3.1 Map

**Map与工厂模式**
- Map的value可以是一个方法
- 与Go的Dock type接口方式一起，可以方便的实现单一方法对象的工厂模式

```go
func TestMapWithFuncValue(t *testing.T){
	m := map[int]func(op int) int{}
	m[1]=func(op int) int {return op}
	m[2]=func(op int) int {return op*op}
	m[3]=func(op int) int {return op*op*op}
	m[4]=func(op int) int {return 0}
	t.Log(m[1](2),m[2](2),m[3](2),m[4](2))
}
/*
=== RUN   TestMapWithFuncValue
    my_test.go:41: 2 4 8 0
--- PASS: TestMapWithFuncValue (0.00s)
PASS
*/

```
**实现Set**
Go的内置集合中没有Set实现，可以map[type]bool
1. 元素的唯一性
2. 基本操作
	- 添加元素
	- 判断元素是否存在
	- 删除元素
	- 元素个数
```go
func TestMapforSet(t *testing.T){
	mySet := map[int]bool{}
	mySet[1] = true
	if mySet[1]{
		t.Log("1在set中")
	}else{
		t.Log("1不在set中")
	}
	if mySet[3]{
		t.Log("3在set中")
	}else{
		t.Log("3不在set中")
	}
}
/*
=== RUN   TestMapforSet
    my_test.go:48: 1在set中
    my_test.go:55: 3不在set中
--- PASS: TestMapforSet (0.00s)
PASS
*/
```

## 3.2 字符串
1. string是数据类型，不是应用或指针类型
2. string是只读的byte slice，len函数可以返回它所包含的byte数
3. string的byte数组可以存放任何数据

**Unicode UTF-8**
1. Unicode是一种字符集(code point)
2. UTF-8是Unicode的存储实现(转换为字节序列的规则)

**字符编码**
| 字符          | “中“             |
| ------------- | ---------------- |
| Unicode       | 0x4E2D           |
| UTF-8         | 0xE4B8AD         |
| string/[]byte | [0xE4,0xB8,0xAD] |


## 3.3 数组和切片


# 4 函数

**与其他主要编程语言的差异**
1. 可以有多个返回值
2. 所有参数都是值传递：slice，map，channel会有传递引用的错觉
3. 函数可以作为变量的值
4. 函数可以作为参数和返回值

## 4.1 函数的包装(装饰模式)
```go
func timeSpent(inner func(op int ) int) func(op int) int{
	return func(n int )int {
		start := time.Now()
		ret := inner(n)
		fmt.Println("time spent:",time.Since(start).Seconds())
		return ret
	}
}

func slowFun(opt int) int{
	time.Sleep(time.Second*1)
	return opt
}

func TestFn(t *testing.T){
	tsSF := timeSpent(slowFun)
	t.Log(tsSF(10))
}

/*
=== RUN   TestFn
time spent: 1.0000404
    my1_test.go:30: 10
--- PASS: TestFn (1.00s)
PASS

*/
```

## 4.2 defer函数

等待调用方法执行完了再执行。


## 4.3 init方法
- 在main被执行前，所有依赖的package的init方法都会被执行
- 不同包的init函数按照导入得依赖关系决定执行顺序
- 每个包可以有多个init函数
- 报的每个源文件也可以有多个init函数，这点比较特殊


# 5 面向对象





# 6 Other

**channle的关闭**
- 向关闭的channel发送数据，会导致panic
- v,ok <- ch；ok为boo值，true表示正常接受，false表示通道关闭
- 所有的channel接收者都会在channel关闭时，立刻从阻塞等待中返回且上述ok值为false。这个广播机制常被利用，进行向多个订阅者同时发送信号。如：退出信号

**Context**

- 根Context：通过context.Background()创建
- 子Context：context.WithCancel(parebtContext)创建
  - ctx，cancel := context。WithCancel(context.Background())
- 当前Context被取消时，基于他的子context都会被取消
- 接收取消通知 <-ctc.Done

**sync.Pool**
1. sync.Pool对象获取
	- 尝试从石油对象获取
	- 私有对象不存在，尝试从当前Processor的共享池获取
	- 如果当前Processor共享池也是空的，那么久尝试去其他Processor的共享池获取
	- 如果所有子池都是空的，最后就用用户指定的New函数产生一个新的对象返回
2. sync.Pool对象放回
	- 如果私有对象不存在则保存为私有对象
	- 如果私有对象存在，放入当前Processor子池的共享中
3. sync.Pool对象的生命周期
	- GC会清除sync.pool缓存的对象
	- 对象的缓存有效期为下一次GC之前
4. sync.Pool总结
	- 适合于通过复用，降低复杂对象的创建和GC代价
	- 协程安全，会有所开销
	- 生命周期受GC影响，不适合做连接池等，需要自己管理生命周期的资源的池化

**单元测试**
- Fail，Error：该测试失败，该测试继续执行，其他测试继续执行
- FailNow，Fatal：该测试失败，该测试中止，其他测试继续执行
- 代码覆盖率： go test -v cover
- 断言：https://github.com/stretchr/testify

**Benchmark**
用于性能测试——运行时间
```go
//方法名以Benchmark开头
func BenchmarkConcatStringByAdd(b *testing.B){
	//与性能测试无关的代码
	b.ResetTimer()
	for i:=0;i<b.N ;i++  {
		//测试代码
	}
	b.StopTimer()
	//与性能测试无关的代码
}
```

**BDD（Behavior Driven Development)**
测试相关
- 项目网站：https://github.com/smartystreets/goconvey
- 安装： go get -u github.com/smartystreets/goconvey/convey
- 启动WEB UI : $GOPATH/bin/goconvey


**反射编程**
reflect.TypeOf     vs    reflect.ValueOf
- reflect.TypeOf返回类型(reflect.Type)
- reflect.ValueOf返回值(reflect.Value)
- 可以从reflect.Value获得类型
- 通过kind来判断类型
- Reflect.Type和Reflect.Value都有FieldByName方法，注意他们的区别

**DeepEqual**
一般的切片和map不能相互比较，只能和nil比较

**不安全包unsafe**


**Pipe-Filter架构**
- 非常适合于数据处理及数据分析系统
- Filter封装数据处理的功能
- 松耦合：Filter只跟数据（格式）耦合
- Pipe用于连接Filter传递数据或者在异步处理过程中缓冲数据流程内同步调用时，pipe演变为数据在方法调用间传递

**Micro Kernel**
- 特点
	- 易于扩展
	- 错误隔离
	- 保持架构一致性
- 要点
	- 内核包含公共流程或通用逻辑
	- 将可变或可扩展部分规划为扩展点
	- 抽象扩展点行为，定义接口
	- 利用插件进行扩展

**Http 服务**
1. 路由规则
- URL分为两种，末尾是/:表示一个子树，后面可以跟其他子路径；末尾不是/,表示一个叶子，固定路径
  以/结尾的URL可以匹配它的任何子路径，比如/images会匹配/images/cute-cat.jpg
- 它采用最长匹配原则，如果有多个匹配，一定采用匹配路劲最长的那个进行处理
- 如果没有找到任何匹配项，会返回404错误

**构建Restful服务**



**性能分析工具**
1. 准备工作
- 安装 graphviz： brew install graphviz
- 将$GOPATH/bin 加入$PATH
- 安装go-torch

**通过HTTP方式输出Profile**
- 简单，适合于持续性运行的应用
- 在应用程序中导入 import _ "net/http/pprof",并启动http server即可
- http://<host>:port/debug/pprof



**Chaos Engineering**
- 如果问题经常发生就会学习和思考解决它的方法


**相关开源项目**
- https://github.com/Netflix/chaosmonkey
- https://github.com/easierway/service_decorators/blob/master/README.md

**图书推荐**
- GO程序设计语言
- 面向模式的软件架构（模式系统）
- 计算机程序的构造和解释

















