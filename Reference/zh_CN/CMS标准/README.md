# CMS 标准

CMS (CM Assembler) 是 ICM 编译源代码生成的中间表示。可以直接运行在 CVM 虚拟机上，也可以通过 CCM (Compiler for CM) 编译为相应平台的机器指令或虚拟码。

CMS 是一种高度抽象的中间表示。

## 概述

CMS 主要由三个段组成：

- .datas 段 ： 存放各种数据
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
;; Main.cms

.datas:
        .string $1   "Hello "
        .string $2   "World!"
        .string $3   "call f."

.idents:
        .room   %root
        .dyvarb a
        .dyvarb b
        .stvarb c    Int
        .data   d    12
        .func   f
        .module Math
        .room   %Math
        .data   pi   3.14
        .room   %root
        .entry  main

.imports:
        .room   %root
        .import %Core
        .using  println(%Core)

.codes:

f:
        mov  %res, %1
        ret

main:
        let  a, $1
        let  b, $2
        call %1, +, a b
        call %0, println, %1
        set  c, 0x100
        call %0, println, c
        call %0, println, d
        call %1, f, $3
        call %0, println, %1
        call %0, println, pi(%Math)
        ret
```

## 数据

CMS 的数据分为可存放于指令中的短数据 与 只能存放于数据段的长数据。

短数据 是 不大于 32位 的数据。包括但不限于：
- Boolean 类型数据。
- Int8, Int16, Int32, UInt8, UInt16, UInt32 等机器无关的不超过32位的定长数据。

长数据 是 长度超过 32位，或者不定长的数据。包括但不限于：
- String 字符串数据。
- Int64, UInt64 等机器无关的超过32位定长数据。

机器相关的数据具有未知的长度，根据具体的编译时信息来确定。包括但不限于：

- Int, UInt, Float, Double 等不定长数据。
- CPointer C 指针数据。

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

- 通用寄存器： %ar + 数字，数字为 从 1 开始的整数。函数间共有的寄存器，每个线程内是独立的。
- 全局寄存器： %g + 数字，数字为 从 1 开始的整数。跨线程的共有寄存器。
- 私有寄存器： % + 数字，数字为 从 1 开始的整数。函数等进程私有的寄存器，一般使用栈表示。
- 结果寄存器： %res ， 存储函数调用的结果的寄存器，一般放置在函数栈的最前面。
- 参数寄存器： %a + 数字，数字为 从 1 开始的整数。 参数寄存器在指令fcal中使用，实际上是将被调用函数的私有寄存器。

示例：

```
mov %1, %2
```

就是将 2 号 寄存器里的数据送入 1 号 寄存器中。

有时，不希望储存结果时，通过使用 %0 来表示舍弃运算的结果。

出于多线程的考虑，每个线程下都有名称相同的通用寄存器组，在不同线程下，调用不同的通用寄存器组。因此不会由通用寄存器冲突引发线程问题。


## 指令集

### 说明

在结果位、参数位，使用的标志：
- R 寄存器
- Rn 私有寄存器
- Ran 参数寄存器
- I 标识符（不包含数据标识符）
- Iv 变量标识符
- Idv 动态变量标识符
- Isv 静态变量标识符
- Irv 引用变量标识符
- If 函数标识符
- D 数据（包含数据标识符）
- Di 数据标识符
- \- 无

### 寄存器指令

指令|结果位|第一参数|说明
---|------|-------|-----
mov|R|R|将某一寄存器的数据传送给另一寄存器
mvd|R|D|将数据传送给寄存器
mvdv|R|Idv|将动态变量的数据传送给寄存器
mvsv|R|Isv|将静态变量的数据传送给寄存器
mvrv|R|Irv|将引用变量的数据传送给寄存器

### 函数调用指令

指令|结果位|第一参数|其他参数|多参？|说明
---|------|-------|--------|-----|-----
call|R|R/If|R/I/D|√|调用函数，函数和参数同时书写
farg|-|R/I/D||√|将参数打包
fcal|R|R/If|||调用函数，使用打包的参数
fcfe|-|R|||检查是否为函数
fcae|-|R/If|||检查对于已打包的参数，函数调用是否合法
ret|-||||从当前函数中返回

call 指令可以接受函数标识符或者存储有函数数据的寄存器作为第一参数。

当变量存储了函数时，需要经过如下操作：

```
; (let f +)
let  f, +  ; 将 + 通过 let 操作赋给 变量 f

; (f 5 6)
mvdv %1, f  ; 将动态变量 f 存储的函数对象移至寄存器%1中。
fcfe %1     ; 检查 %1 寄存器是否为函数对象。若不是则抛出异常。
farg 5 6    ; 将 5 和 6 作为参数打包。
fcae %1     ; 检查参数对 %1 的调用是否合法。若不是则抛出异常。
fcal %2, %1 ; 调用函数 %1，即 f，也即 + ；并将结果传给 %2 寄存器。
```

当然，通过一定的预先检验（通常为在编译期的静态检查），可以省略部分运行时检查代码。优化后的代码如下：
```
; (f 5 6)
mvdv %1, f
farg 5 6
fcal %2, %1
```

### 变量操作指令

指令|结果位|第一参数|说明
---|------|-------|-----
let| Iv |D/I/R| 使用let操作
set| Iv |D/I/R| 使用set操作
cpy| Iv/R |D/I/R| 使用cpy操作
ref| Iv/R |D/I/R| 使用ref操作
