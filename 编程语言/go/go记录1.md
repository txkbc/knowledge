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




