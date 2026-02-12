---
title: From JavaScript to TypeScript
categories:
  - javascript
tags:
  - javascript
excerpt: "Basic Variables and Number Types"
date: 2025/12/30 00:00:00
---

# From JavaScript to TypeScript（2）Basic Variables and Number Types

<img src="https://s2.loli.net/2026/01/03/h8HzEIaA6xZ3UjT.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1767453491323" style="zoom: 67%;" />

<img src="https://s2.loli.net/2025/12/30/ybuUSFvcs5MhCiE.jpg" alt="nodejs" style="zoom: 67%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom:67%;" />

<!-- toc -->




## 关键字

关键字（Keyword）就是 JavaScript 语言内部使用的一组名字（或称为命令）。这些名字具有特定的用途，用户不能自定义同名的标识符，具体内容如表所示。

|             **break**             |  **delete**  |     **if**     |  **this**  | **while** |
| :-------------------------------: | :----------: | :------------: | :--------: | :-------: |
|             **case**              |    **do**    |     **in**     | **throw**  | **with**  |
|             **catch**             |   **else**   | **instanceof** |  **try**   |           |
|           **continue**            | **finally**  |    **new**     | **typeof** |           |
| **debugger（ECMAScript 5 新增）** |   **for**    |   **return**   |  **var**   |           |
|            **default**            | **function** |   **switch**   |  **void**  |           |

## 保留字

保留字就是 JavaScript 语言内部预备使用的一组名字（或称为命令）。这些名字目前还没有具体的用途，是为 JavaScript 升级版本预留备用的，建议用户不要使用。具体说明如表所示。

| abstract    | double      | goto           | native        | static           |
| ----------- | ----------- | -------------- | ------------- | ---------------- |
| **boolean** | **enum**    | **implements** | **package**   | **super**        |
| **byte**    | **export**  | **import**     | **private**   | **synchronized** |
| **char**    | **extends** | **int**        | **protected** | **throws**       |
| **class**   | **final**   | **interface**  | **public**    | **transient**    |
| **const**   | **float**   | **long**       | **short**     | **volatile**     |

ECMAScript 3 将 Java 所有关键字都列为保留字，而 ECMAScript 5 规定较为灵活，例如：

- 在严格模式下，ES 5 变得更加谨慎，严格限制 implements、interface、let、package、private、protected、public、static、yield、eval（非保留字）、arguments（非保留字）的使用。

## 预定义

JavaScript 预定义了很多全局变量和函数，用户也应该避免使用它们，具体说明如表所示。

| arguments              | encodeURL              | Infinity     | Number             | RegExp          |
| ---------------------- | ---------------------- | ------------ | ------------------ | --------------- |
| **Array**              | **encodeURLComponent** | **isFinite** | **Object**         | **String**      |
| **Boolean**            | **Error**              | **isNaN**    | **parseFloat**     | **SyntaxError** |
| **Date**               | **eval**               | **JSON**     | **parseInt**       | **TypeError**   |
| **decodeURL**          | **EvalError**          | **Math**     | **RangeError**     | **undefined**   |
| **decodeURLComponent** | **Function**           | **NaN**      | **ReferenceError** | **URLError**    |

无论是在严格模式下还是在非严格模式下，都不要在定义变量名、函数名或者属性名时使用上面列举出的名字。

## 字面量（Literal）

下面示例分别定义不同类型的直接量：

字符串、数值、布尔值、正则表达式、特殊值、对象、数组和函数

```javascript
//1 Literal String 
console.log("Hello, World!");
//2 Literal Number
console.log(1);
//3 Literal Boolean
console.log(true);
//4 Null Literal
console.log(null);
//5 Literal regular expression
console.log(/ab+c/);
//6 Literal Array
console.log([1, 2, 3]);
//7 Literal Dictionary (Object)
console.log({name: "John", age: 30});
//8 function Literal
console.log(function() { return "Hello"; });
```

输出结果：

```bash
Hello, World!
1
true
null
/ab+c/
[ 1, 2, 3 ]
{ name: 'John', age: 30 }
[Function (anonymous)]
```

有趣的是，默认的log支持不同颜色来区分不同的变量：

<img src="https://s2.loli.net/2025/12/30/xkoeYhnvyCN8gI6.png" alt="image-20251230214518108" style="zoom: 50%;" />

## 变量的定义

JavaScript 吸收了 Java、C 等语言的语法风格，但也存在一些独特的设定，下面将针对这些设定做出讲解。

### 严格模式

ES5 标准中增加了严格模式（Strict Mode）的概念。在这种模式下，一些不规范或不安全的写法会抛出错误；本质上，它更接近一种“更严格的 JavaScript 语义”。

如果需要某个脚本使用严格模式，可在脚本开头加入：

```javascript
'use strict';
```

任何 JS 引擎看到这个编译指令之后都会切换到严格模式。**比较特殊的是：ES6 的模块和类在默认情况下会自动启用严格模式**。如果想要某个函数在该模式下执行，可以在函数开头添加这个指令：

```javascript
function doSomething(){
	'use strict';
}
```

### 变量

在 ECMAScript 的语义下，变量是松散类型的：**变量本质上是一个命名占位符；它保存的是值（或指向值的引用）**。有 3 个关键字可以声明变量：

- **var**
- **let**
- **const**

`var` 关键字和另外两个关键字一样，后面跟一个变量名即可完成定义：

```javascript
var name;
let name;
const name;
```

三者存在很大的区别，这里就不详细介绍，我们等到下一篇作用域再进行。

## 数值变量类型

ES 规定了 7 种原始数据类型（Primitive Types）：

- **Undefined**
- **Null**
- **Boolean**
- **Number**
- **BigInt**
- **String**
- **Symbol**

除此之外还有 **Object**（引用类型），它不是原始类型。

这些类型足以表示各种常见数据。**ECMAScript 的变量名通常采用驼峰命名：第一个单词首字母小写，后续单词首字母大写**。

### Number类型

数值（Number）类型用来表示数值。JavaScript 中不区分整数和小数（浮点数），统一使用 Number 类型表示；其底层采用 IEEE 754 双精度浮点数（Float64）。如下例所示：

```javascript
// Number type variable
var num = 42;
console.log(num);//42

var floatNum = 3.14;
console.log(floatNum);//3.14
```

整数当然也支持 2 进制、8 进制、16 进制的字面量：

```javascript
'use strict';
//binary type variable
var binNum = 0b1010; // binary for 10
console.log(binNum);

//octal
var octNum = 0o52; // octal for 42
console.log(octNum);

//octal (legacy)
var octNum2 = 052; // octal for 42 (implicit)
console.log(octNum2);

//hexadecimal type variable
var hexNum = 0x2A; // hexadecimal for 42
console.log(hexNum);
```

需要注意的是，在严格模式下不允许使用隐式 8 进制声明，会报错：

```bash
/home/neo/Desktop/Example/JavaScript/example_variable.js:18
var octNum2 = 052; // octal for 42 (older syntax)
              ^^^

SyntaxError: Octal literals are not allowed in strict mode.
    at wrapSafe (node:internal/modules/cjs/loader:1378:20)
    at Module._compile (node:internal/modules/cjs/loader:1428:41)
    at Module._extensions..js (node:internal/modules/cjs/loader:1548:10)
    at Module.load (node:internal/modules/cjs/loader:1288:32)
    at Module._load (node:internal/modules/cjs/loader:1104:12)
    at Function.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:174:12)
    at node:internal/main/run_main_module:28:49

Node.js v20.17.0
```

#### 数值分隔符

为了增加可读性，所有数值都可以使用下划线作为数值分隔符，解释器在解释的时候会自动忽略它们，比如：

```javascript
// BigInt type variable
var bigIntNum = 1_000_000_000_000;
console.log(bigIntNum); //1000000000000

var bigfloatNum = 1_000_000.123_456;
console.log(bigfloatNum); //1000000.123456
```

需要注意的是，下划线不能出现在字面量的开头或结尾，不能紧挨小数点，前面也不能有 0：

```javascript
var wrongNum1 = 1_000_.123; // Incorrect usage of underscore
var wrongNum2 = 1._000; // Incorrect usage of underscore
var wrongNum3 = 0_123; // Incorrect usage of underscore
```

#### 值的范围

数值的范围取决于 IEEE 754 标准。IEEE 754 浮点数采用二进制科学计数法，结构如下（以 double 为例）：

- 1 位符号位（sign）
- 11 位指数位（exponent）
- 52 位尾数（fraction/mantissa）

$$
(−1) ^{sign}×1.fraction×2^{exponent−1023}
$$

JS 可以表示的**最小的非 0 正数**保存在 `Number.MIN_VALUE`，相反的**最大值**保存在 `Number.MAX_VALUE`。如果某次计算的绝对值超过了最大值，结果会变为 `Infinity` 或 `-Infinity`（取决于符号）；而当数值过小发生下溢时，结果会趋近于 `0`（可能变为 `0`）。如果要判断某个结果是否仍为有限数值，可以使用函数 `isFinite`；当结果为 `Infinity` 或 `-Infinity` 时会返回 `false`：

```javascript
var minValue = Number.MIN_VALUE;
console.log(minValue);//5e-324

var maxValue = Number.MAX_VALUE;
console.log(maxValue);//1.7976931348623157e+308

var infinityValue = Infinity;
console.log(infinityValue);//Infinity

var negativeInfinityValue = -Infinity;
console.log(negativeInfinityValue);//-Infinity

// isFinite
var isFiniteValue = isFinite(100 / 0);
console.log(isFiniteValue);//false
```

#### NaN

还有一个特殊值 `NaN`（Not a Number），表示“不是一个可表示的数值结果”。例如对负数求平方根等操作会得到 `NaN`。需要注意的是，JS 提供的 `isNaN` 会先做类型转换，因此其行为可能和一些语言的“严格 NaN 判断”不一致：

```javascript
var notANumber = NaN;
console.log(notANumber);//NaN

isNaNValue = isNaN("123");
console.log(isNaNValue);//false

isNaNValue2 = isNaN(123);
console.log(isNaNValue2);//false

isNaNValue3 = isNaN(NaN);
console.log(isNaNValue3);//true

isNaNValue4 = isNaN("Hello");
console.log(isNaNValue4);//true
```

字符串 “123” 会先被转换为数字 `123`，然后再判断。

简单总结：`isNaN(x)` 会先尝试把 `x` 转成数字；如果转换后结果是 `NaN`，才返回 `true`。

### BigInt类型

BigInt 用于表示任意精度的大整数，超出普通 Number（即 IEEE 754 double）能精确表示的范围。BigInt 并不是把大数“转化”为能放入寄存器的数值，而是通过软件层面将大整数拆分成多个小块（通常每块是 32 位或 64 位），分别存储和运算。CPU 寄存器只能直接处理有限位数（如 32 位或 64 位）的整数。BigInt 的大数运算通常由 JavaScript 引擎用多段算法在内存中实现，分多步用寄存器处理每一小段，最后合成结果。

它的设计目标之一是支持超过安全整数范围的整数：

```javascript
Number.MAX_SAFE_INTEGER; // 9007199254740991
Number.MIN_SAFE_INTEGER; // -9007199254740991
```

这个范围的大整数。

要创建 BigInt 可以使用如下方式：

```javascript
// BigInt test
Number.MAX_SAFE_INTEGER; // 9007199254740991
Number.MIN_SAFE_INTEGER; // -9007199254740991

var bigIntNum = 111111111111111111n; // BigInt type variable
console.log(bigIntNum);//111111111111111111n

var bigIntNum2 = BigInt("12345678901234567890");
console.log(bigIntNum2);//12345678901234567890n

var bigIntNum3 = BigInt("0xFFFFFFFFF");
console.log(bigIntNum3)//68719476735n
```

BigInt 支持几乎所有 Number 类型的算术运算，但其本身不支持如下运算符：

- `>>>`右移操作符
- 一元`+`操作符

BigInt 作为特殊类型与 Number 几乎完全独立，无法进行混合运算，但可以进行比较和排序。

```javascript
123 + 123n // type error
```

可以：

```javascript
console.log(123 < 1234n); // true
```

### 非数值向的数值转换

有三个常用函数可以将非数值转换为数值：

- `Number()`：用于将任何数据类型转换为数字
- `parseInt()`：主要用于从字符串中解析整数
- `parseFloat()`：主要用于从字符串中解析浮点数

#### Number

`Number()` 的转换规则相对复杂，包含以下内容：

1. 字符串转 Number

   - 纯数字字符串直接转为数字，如 "123" → 123

   - 如果字符串包含有效浮点值、十六进制值、科学计数法值也能转换，如 "3.14" → 3.14，"1e3" → 1000，**字符串前面的 0 和空格都会被省略**。

     - ```javascript
       console.log(Number(" -123")); // -123
       console.log(Number("00001.1")); // 1.1
       ```

   - 空字符串 "" 转为 0

   - 除此之外，非法数字字符串（如 "abc"）转为 NaN

     - ```javascript
       console.log(Number("abc1.1")); // NaN
       ```

2. 布尔值转 Number

   - true → 1
   - false → 0

3. null 和 undefined

   - null → 0
   - undefined → NaN

4. 对象转 Number

   - 先调用对象的 valueOf()，再调用 toString()，最后尝试转换，如 {} → NaN，[1] → 1

5. 自动类型转换场景

   - 算术运算符（如 +, -, *, /）会自动尝试将操作数转换为 Number

   - 比较运算符（如 <, >, ==）也会尝试转换

考虑到 `Number()` 的转换规则较复杂，在“把字符串转换为整数”的需求上通常使用 `parseInt()`；而在“把字符串转换为浮点数”的需求上使用 `parseFloat()`。二者用法相似，其中 `parseInt()` 的规则如下：

#### parseInt

`parseInt` 会从字符串第一个非空白字符开始解析：遇到非数字字符（不含开头的 `+`/`-`）就停止，并返回已解析的整数部分；如果第一个有效字符就不是数字（也不是 `+`/`-`），**会立即返回 NaN（注意不是 0）**。例如 `Number("")` 得到 0，但 `parseInt("")` 得到 NaN。

```javascript
console.log(parseInt("clue1234")); // NaN
```

`parseInt` 还有第二个参数用于指定进制（radix）。例如强制按 16 进制解析可以传入 16；另外需要注意，`parseInt` 不会自动识别 `0b` 二进制前缀（因此下面例子只解析到开头的 `0`）：

```javascript
console.log(parseInt("1234blue")); // 1234
console.log(parseInt("  0b1010")); // 0
console.log(parseInt("0xA", 16)); // 10
```

需要注意的是，这三个函数的共同点在于，在 10 进制底数下都会**自动忽略和省略开头多余的 0**。

```javascript
console.log(parseInt("01010")); // 1010
```

#### parseFloat

相较于 `parseInt`，主要有两点：

- 首先是没有进制参数，遇到 `0x` 这类输入通常只会解析到 `0`。
- 其次是只有第一个小数点有效，其余小数点无效。

```javascript
console.log(parseFloat("  -123.45.56abc")); // -123.45
```

