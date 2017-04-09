# CMS 标准

CMS (CM Assembler) 是 ICM 编译源代码生成的中间表示。可以直接运行在 CVM 虚拟机上，也可以通过 CCM (Compiler for CM) 编译为相应平台的机器指令或虚拟码。

CMS 是一种高度抽象的中间表示。

## 概述

CMS 主要由三个段组成：

- .idents 段 ： 对于各种标识符进行声明。
- .imports 段 ： 对于库的导入。
- .code 段 ： 主要的运行代码。

给定一个 Chill 语言的源程序：

```
(let a "Hello ")
(let b "World ")
(println (+ a b))

(dim c Int)
(set c 0x100)
(println c)

(define d (+ 5 7))
(println d)

(defunc f [str]
  (return str))
(println (f "call f."))

(module Math
  (define pi 3.14)
)
(println Math.pi)
```

经过 ICM 会生成如下 CMS 代码：

```
.imports:
        .room   %root
        .import %Core

.idents:
        .room   %root
        .dyvarb a
        .dyvarb b
        .stvarb c    Int
        .data   d    12
        .data   $1   "call f."
        .func   f
        .module Math
        .room   %Math
        .data   pi   3.14
        .room   %root
        .entry  main

.code:
        
f:
        mov  %res, %ar1
        ret
        
main:
        let  a, "Hello "
        let  b, "World!"
        call %1, +, a b
        call %0, println, %1
        set  c, 0x100
        call %0, println, c
        call %0, println, d
        call %0, f, $1
        call %0, println, pi(%Math)
        ret
```

## 符号表

符号的区分是 Chill 语言一个重要的功能。
Chill 语言共有以下几种符号：

- data     : 数据
- dyvarb   : 动态变量
- stvarb   : 静态变量
- func     : 函数
- module   : 模块
- struct   : 结构

## 寄存器

CMS 是基于寄存器的，且寄存器数目在理论上是无限多的。

CMS 寄存器有如下几种：

- 通用寄存器： % + 数字， 数字为 从 1 开始的整数
- 参数寄存器： %ar + 数字， 传入函数调用的参数，数字为 从 1 开始的整数
- 结果寄存器： %res ， 返回函数调用的结果

示例：

```
mov %1, %2
```

就是将 2 号 寄存器里的数据送入 1 号 寄存器中。

有时，不希望储存结果时，通过使用 %0 来表示舍弃运算的结果。

出于多线程的考虑，每个线程下都有几组名称相同的寄存器，在不同线程下，调用不同的寄存器组。因此不会由寄存器冲突引发线程问题。
