# 核心库

## Boolean

### =
(Boolean Boolean ...) -> Boolean

返回判断多个布尔值是否相等的布尔值。

### and
(Boolean Boolean ...) -> Boolean

返回多个布尔值的与。

### not
(Boolean) -> Boolean

返回布尔值的非。

### or
(Boolean Boolean ...) -> Boolean

返回多个布尔值的或。

### xor
(Boolean Boolean ...) -> Boolean

返回多个布尔值的异或。

## Number

### +
(Number Number ...) -> Number

返回多个数值的和。

### -
(Number) -> Number

返回数值的负。

(Number Number ...) -> Number

返回多个数值的差。

### *
(Number Number ...) -> Number

返回多个数值的积。

### /
(Number Number ...) -> Number

返回多个数值的商。

### =
(Number Number ...) -> Boolean

返回判断多个数值是否相等的布尔值。

### <
(Number Number ...) -> Boolean

返回判断多个数值参数是否按照从小到大排列（不允许重复）的布尔值。

### <=
(Number Number ...) -> Boolean

返回判断多个数值参数是否按照从小到大排列（允许重复）的布尔值。

### >
(Number Number ...) -> Boolean

返回判断多个数值参数是否按照从大到小排列（不允许重复）的布尔值。

### >=
(Number Number ...) -> Boolean

返回判断多个数值参数是否按照从大到小排列（允许重复）的布尔值。

### ->String
(Number) -> String

返回数值转换为String后的值。

### abs
(Number) -> Number

返回数值的绝对值。

### dec
(Number) -> Number

返回数值的减。

### gcd
(Number Number) -> Number

返回两个数值的最大公约数。

### inc
(Number) -> Number

返回数值的增。

### mod
(Number Number) -> Number

返回两个数值的模。

### rem
(Number Number) -> Number

返回两个数值的余。

## String

### +
(String String ...) -> String

返回多个字符串的连接。

### ->List
(String) -> List

返回字符串转换为List后的字符表。

### ->Number
(String) -> Number

返回字符串转换为Number后的值。

### ->Symbol
(String) -> Symbol

返回字符串转换为Symbol后的值。

### sub
[(str : String) (id : Integer) (len : Integer)] -> String

返回字符串从id位置起，长度为len的子串。

