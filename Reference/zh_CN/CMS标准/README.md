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

(define pi 3.14)
(println pi)
```

经过 ICM 会生成如下 CMS 代码：

```
.idents:
        .room %root
        .dyvarb a
        .dyvarb b
        .data   pi   3.14
        .entry  main

.imports:
        .room %root
        .import %Core
        
.code:
        
main:
        let  a, "Hello "
        let  b, "World!"
        call %1, +, a b
        call %0, println, %1
        call %0, println, pi
        ret
```
