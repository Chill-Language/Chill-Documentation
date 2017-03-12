# 语言要素

## 语法定义

### 说明

Chill 语言是一种使用S-表达式的语言。

Chill 的表达式由位于 ```()``` 左端的 可调用对象/表达式 或 操作符 与 右侧的参数 组成。

定义中：

- fexpr : 可调用的对象/表达式
- sexpr : 子表达式，没有特殊的要求
- bexpr : 布尔表达式
- rexpr : 范围表达式
- texpr : 类型表达式
- cexpr : 常量表达式
- vexpr : 带有值的表达式
- V : 变量对象
- I : 标识符对象
- T : 类型对象
- ```...``` 位于后缀 : 一个或多个表达式
- ```<>``` 所引用 : 可省略的内容
- ```<>*``` 所引用 : 任意个内容
- 后缀的数字或 k, n 等字母 : 区别不同内容所使用的序号

### 定义

#### 可调用对象（函数、闭包等）的调用

```
(fexpr <sexpr ...>)
```

#### do

```
(do
   <sexpr ...>
)
```

#### ?

```
(? bexpr sexpr1 sexpr2)
```

#### if

```
(if bexpr0
   sexpr0 ...
 <elsif bexprk
   sexprk ...>*
 <else
   sexprn ...>
)
```

#### loop

```
(loop
   sexpr ...
)
```

#### while

```
(while bexpr
   sexpr ...
)
```

#### for

```
(for V in rexpr
   sexpr ...
)
```

#### let

```
(let V vexpr)
```

#### set

```
(set V vexpr)
```

#### cpy

```
(cpy <V> vexpr)
```

#### ref

```
(ref V V2)
```

#### dim

```
(dim V texpr)
```

#### restrict

```
(restrict V texpr)
```

#### define

```
(define I cexpr)
```

#### defunc

```
(defunc I [<(V <: Type>)>*] <-> Type>
  sexpr ...
)
```

#### function

```
(function <I> [<V <: Type>>*] <-> Type>
   sexpr ...
)
```

#### defstruct

```
(defstruct I
   <[(I <: Type>)]>*
)
```

#### struct

```
(struct <I>
   <[(I <: Type>)]>*
)
```
