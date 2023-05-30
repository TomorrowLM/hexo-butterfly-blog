---
slug: ES6，TS和设计模式
title: ES6，TS和设计模式
description: ES6，TS和设计模式
keywords: ES6,TS,设计模式
category: ES6,TS,设计模式
tags: [ES6,TS,设计模式]
sticky: 2
swiper_index: 2
author: liming
date: 25-September-2020
swiper_desc: ES6,TS,设计模式
---

http://caibaojian.com/es6/

# ES6

## **let和const**

`let`和`const`。其中，`let`完全可以取代`var`，因为两者语义相同，而且`let`没有副作用。在`let`和`const`之间，建议优先使用`const`，尤其是在全局环境，不应该设置变量，只应设置常量。同时JavaScript 编译器会对`const`进行优化，所以多使用`const`，有利于提高程序的运行效率 

### 作用域

作用域定义了变量的可见性或可访问性。大白话来说，就是一个变量能不能被访问或引用，是由它的作用域决定的。

- **全局作用域（Global Scope）**

  在代码中任何地方都能访问到的对象拥有全局作用域，一般来说以下几种情形拥有全局作用域：

  （1）最外层函数和在最外层函数外面定义的变量拥有全局作用域

  （2）所有末定义直接赋值的变量自动声明为拥有全局作用域

  （3）所有window对象的属性拥有全局作用域

- **局部作用域（Local Scope）**

  - **函数作⽤域**：函数体中的局部变量只在函数执行时生成，函数执行完毕时局部变量即刻销毁

  - **块级作⽤域**：ES6引⼊了 let 和 const 关键字,和 var 关键字不同，在⼤括号中使⽤ let 和 const 声明的变量存 在于块级作⽤域中。在⼤括号之外不能访问这些变量

**作用域链**

当一个变量在当前作用域无法找到时，便会尝试寻找其外层的作用域，如果还找不到，再继续往上寻找

**好处**

1. `防止命名冲突`：你写了一万行的代码文件，如果没有作用域，你要给每个变量取独一无二的名字，屁股想想也知道是种折磨。
2. `安全性`： 变量不会被外部访问，保证了变量值不会被随意修改。你定义在函数内的变量，如果能在几千行之后不小心被修改，脚趾头想想也知道是种折磨。
3. `更高级的语法`：封装、面向对象等的实现离不开对变量的隔离，这是依靠作用域所达到的。

### var变量和函数提升

**词法分析阶段/预解析**：在JS代码执行之前，浏览器的解析器在**遇到 var 变量名 和function 整个函数** 提升到当前作用域的最前面。

- 变量提升只会提升变量名的声明，而不会提升变量的赋值初始化。

  ```
  console.log(foo); // undefined
  var foo = '小花猫';
  console.log(foo)  // 小花猫
  ```

- 函数声明会提升而函数表达表达式不会提升

  ```
  console.log(test1()) // 'this is test1'
  function test1(){
    return 'this is test1';
  }
  
  console.log(test2) // undefined
  var test2 = function(){
    return 'this is test2'
  }
  ```

- **函数提升的优先级大于变量提升的优先级**

  ```js
  var foo = 3;
  function hoistVariable() {
    var foo = foo || 5;
    console.log(foo); // 5
  }
  hoistVariable();
  
  //预编译后
  var foo;
  function hoistVariable() {
    var foo
    foo = foo || 5; // 此时 等号右侧 foo 为 undefined
    console.log(foo); // 5
  }
  foo = 3
  hoistVariable();
  ```

### **不存在变量提升**

暂时性死区: 区级作用域中存在`let`和`const`命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。**凡是在声明之前就使用这些变量，就会报错。** 

```javascript
function bar(x = y, y = 2) {
  return [x, y];
}
//(x = y, y = 2)作为函数参数作用域，父作用域
//函数内部是一个单独的子作用域
bar(); // 报错。参数x默认值等于另一个参数y，而此时y还没有声明，属于”死区“。
```

### **暂时性死区**

只要块级作用域内存在`let`命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部的影响。

```javascript
var tmp = 123;
if (true) {
  tmp = 'abc'; // ReferenceError
  let tmp;
}
```

上面代码中，存在全局变量`tmp`，但是块级作用域内`let`又声明了一个局部变量`tmp`，导致后者绑定这个块级作用域，所以在`let`声明变量前，对`tmp`赋值会报错。

ES6 明确规定，如果区块中存在`let`和`const`命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。

### **不允许重复声明**

```javascript
// 报错。let不允许在相同作用域内，重复声明同一个变量
function () {
  let a = 10;
  var a = 1;
}
```

### **为什么需要块级作用域**

```javascript
//1.变量提升,内层变量可能会覆盖外层变量。 
var tmp = new Date();
function f() {
  console.log(tmp);
  if (false) {
    var tmp = "hello world";
  }
}
f(); // undefined

//2.用来计数的循环变量泄露为全局变量。
var s = 'hello';
for (var i = 0; i < s.length; i++) {
  console.log(s[i]);
}
console.log(i); // 5
```

### **块级作用域与函数**

 `let`实际上为 JavaScript 新增了块级作用域。

```javascript
function f1() {
  let n = 5;
  if (true) {
    let n = 10;
  }
  console.log(n); // 5
}
```

上面的函数有两个代码块，都声明了变量`n`，运行后输出 5。这表示外层代码块不受内层代码块的影响,即**块级作用域也属于作用域链中**。如果两次都使用`var`定义变量`n`，最后输出的值才是 10。

### **const命令**

 `const`声明一个只读的常量。一旦声明，常量的值就不能改变。

 只在声明所在的块级作用域内有效 

 对于**复合类型**的变量，变量名不指向数据，而是指向数据所在的地址。`const`命令只是保证变量名指向的地址不变，并不保证该地址的数据不变  

### **顶层对象的属性**

顶层对象，在浏览器环境指的是`window`对象，在Node指的是`global`对象。 

ES5之中，顶层对象的属性与全局变量是等价的。  这样的设计带来了几个很大的问题，首先是没法在编译时就报出变量未声明的错误，只有运行时才能知道 。 其次，程序员很容易不知不觉地就创建了全局变量 。 顶层对象的属性是到处可以读写的，这非常不利于模块化编程。 

 ES6为了改变这一点，一方面规定，为了保持兼容性，`var`命令和`function`命令声明的全局变量，依旧是顶层对象的属性；另一方面规定，`let`命令、`const`命令、`class`命令声明的全局变量，不属于顶层对象的属性。也就是说，从ES6开始，全局变量将逐步与顶层对象的属性脱钩。 

```javascript
var a = 1;
// 如果在Node的REPL环境，可以写成global.a
// 或者采用通用方法，写成this.a
window.a // 1

let b = 1;
window.b // undefined
```



## 变量的解构赋值

### 1.数组

如果等号的右边不是数组（或者严格地说，不是可遍历的结构，参见《Iterator》一章），那么将会报错。 

```javascript
1//“模式匹配”
var [a, b, c] = [1, 2, 3];
let [x, y, z] = new Set(["a", "b", "c"]);//x="a"
let [ , , third] = ["foo", "bar", "baz"]; //third = "baz"

2//另一种情况是不完全解构，即等号左边的模式，只匹配一部分的等号右边的数组。
let [x, y] = [1, 2, 3];//x = 1  y = 2
let [x, y, ...z] = ['a'];
x // "a"
y // undefined
z // []

3//嵌套
let [a, [b], d] = [1, [2, 3], 4];//b=2

4//
let [head, ...tail] = [1, 2, 3, 4];//head=1	tail=[2, 3, 4]

5//只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值。
function* fibs() {
  let a = 0;
  let b = 1;
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}
let [first, second, third, fourth, fifth, sixth] = fibs();
//sixth=5

6//解构赋值允许指定默认值。
var [x = 1] = [undefined]; x // 1
var [x = 1] = [null]; x // null
[x, y = 'b'] = ['a']; // x='a', y='b'
[x, y = 'b'] = ['a', undefined]; // x='a', y='b'

7//ES6 内部使用严格相等运算符（===），判断一个位置是否有值。所以，只有当一个数组成员严格等于undefined，默认值才会生效。
let [x = 1] = [undefined];//x=1
let [x = 1] = [null];//x=null
//undefined == null 	true
```

### 2.对象

```javascript
1//数组的元素是按次序排列的，变量的取值由它的位置决定；对象的属性没有次序，变量必须与属性同名，才能取到正确的值。
var { foo, bar } = { foo: "aaa", bar: "bbb" };
2//如果变量名与属性名不一致，必须写成下面这样。
var { foo: baz } = { foo: 'aaa', bar: 'bbb' };
3//
let obj = { first: 'hello', last: 'world' };
let { first: f, last: l } = obj;
//f = 'hello'
//l = 'world'
4//与数组一样，解构也可以用于嵌套结构的对象。
let obj = {
  p: [
    'Hello',
    { y: 'World' }
  ]
};
let { p: [x, { y }] } = obj;
x // "Hello"
y // "World"
5//对象的解构也可以指定默认值。
var {x = 3} = {};
x // 3
var {x, y = 5} = {x: 1};
x // 1
y // 5
var {x:y = 3} = {x: 5};
y // 5
//默认值生效的条件是，对象的属性值严格等于undefined。
var {x = 3} = {x: undefined};
x // 3
var {x = 3} = {x: null};
x // null

//注意点
//如果要将一个已经声明的变量用于解构赋值，必须非常小心。
//JavaScript 引擎会将{x}理解成一个代码块，从而发生语法错误。只有不将大括号写在行首，避免 JavaScript 将其解释为代码块，才能解决这个问题。
let x;
{x} = {x: 1};// SyntaxError: syntax error
// 正确的写法
let x;
({x} = {x: 1});
2//由于数组本质是特殊的对象，因此可以对数组进行对象属性的解构。
let arr = [1, 2, 3];
let {0 : first, [arr.length - 1] : last} = arr;
first // 1
last // 3
```

### 3.字符串

```javascript
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"
//类似数组的对象都有一个length属性
let {length : len} = 'hello';
len // 5
```

### 4.函数参数

```javascript
function add([x, y]){
  return x + y;
}
//函数add的参数表面上是一个数组，但在传入参数的那一刻，数组参数就被解构成变量x和y
add([1, 2]); // 3

[[1, 2], [3, 4]].map(([a, b]) => a + b);
// [ 3, 7 ]
```

**函数参数的默认值**

```js
function move({ x = 0, y = 0 } = {}) {
  console.log(x, y);
}

move({ x: 3, y: 8 })// [3, 8]
move({ x: 3 }); // [3, 0]
move({}); // [0, 0]
move(); // [0, 0]
```

```js
function foo({ x, y = 5 }) {
  console.log(x, y);
}

foo({}) // undefined 5
foo({ x: 1 }) // 1 5
foo({ x: 1, y: 2 }) // 1 2
//解构赋值没有设置默认值
foo() // TypeError: Cannot read property 'x' of undefined
```

**解构赋值的默认值**

```js
function move({ x, y } = { x: 0, y: 0 }) {
  console.log(x, y);
}

move({ x: 3, y: 8 }); // [3, 8]
move({ x: 3 }); // [3, undefined]
move({}); // [undefined, undefined]
move(); // [0, 0]
```

上面代码是为函数`move`的参数指定默认值，而不是为变量`x`和`y`指定默认值

**为了更容易理解，将上面两个代码合成一个，如下。**

```js
function move({x = 0, y = 0} = { x: 1, y: 1 }) {
    return [x, y];
}
// 解构赋值的模式 {x, y}
// 解构赋值的默认值 x = 0, y = 0

console.log(move({x: 3, y: 8})); // 被解构赋值的参数 : {x: 3, y: 8} 
// [3, 8]
console.log(move({x: 3})); // 被解构赋值的参数 : {x: 3} 
// [3, 0]
console.log(move({})); // 被解构赋值的参数 : {} 
// [0, 0]
console.log(move()); // 被解构赋值的参数 : {x: 1, y: 1} 
// [1, 1]
console.log(move(undefined)); // 被解构赋值的参数 : {x: 1, y: 1} 
// [1, 1]
```

### 5.变量的解构赋值的作用

（1）交换变量的值

（2）从函数返回多个值

```js
// 返回一个数组
function example() {
  return [1, 2, 3];
}
let [a, b, c] = example();
```

（3）函数参数的定义

```js
//有序的参数：
function f([x,y,z]){
    console.log([x,y,z])
}
f([1,2,3]);
//无序的参数：
function f({x,y,z}){
    console.log({x,z,y})
}
f({z:3,x:22,y:31});
```

（4）提取 JSON 数据

```js
let jsonData = {
  id: 42,
  status: "OK",
  data: [867, 5309]
};
let { id, status, data: number } = jsonData;
```

（4）函数参数的默认值(数组或者对象的默认值)

```js
function foo({ x, y = 5 }) {
  console.log(x, y);
}

foo({}) // undefined 5
foo({ x: 1 }) // 1 5
foo({ x: 1, y: 2 }) // 1 2
foo() // TypeError: Cannot read property 'x' of undefined
```

（5）遍历 Map 结构

```js
var map = new Map();
map.set('first' ,'hello');
map.set('second' ,'world');
for(let [key,value] of map){
	console.log(key + "is" + value);
}
```

（6）输入模块的指定方法

```js
const {first,second} = require('xxx');
```

## 数据类型扩展

### 字符串的扩展

```javascript
//允许采用\uxxxx形式表示一个字符，其中“xxxx”表示字符的码点。
"\u0061"  // "a"
//但是，这种表示法只限于\u0000——\uFFFF之间的字符。超出这个范围的字符，必须用两个双字节的形式表达。
"\uD842\uDFB7"
// "𠮷"
"\u20BB7"
// " 7",如果直接在\u后面跟上超过0xFFFF的数值（比如\u20BB7），JavaScript会理解成\u20BB+7。由于\u20BB是一个不可打印字符，所以只会显示一个空格，后面跟着一个7。
"\u{20BB7}"
// "𠮷",只要将码点放入大括号，就能正确解读该字符
//'\u{1F680}' === '\uD83D\uDE80'大括号表示法与四字节的UTF-16编码是等价的。

//JavaScript内部，字符以UTF-16的格式储存，每个字符固定为2个字节。对于那些需要4个字节储存的字符（Unicode码点大于0xFFFF的字符），JavaScript会认为它们是两个字符。ES6提供了codePointAt方法，能够正确处理4个字节储存的字符，返回一个字符的码点。charCodeAt方法只能分别返回前两个字节和后两个字节的值
var s = '𠮷a';
//汉字“𠮷”（注意，这个字不是”吉祥“的”吉“）的码点是0x20BB7，UTF-16编码为0xD842 0xDFB7（十进制为55362 57271）
s.charCodeAt(0) // 55362
s.charCodeAt(1) // 57271

s.codePointAt(0) // 134071
s.codePointAt(0).toString(16) // "20bb7"
s.codePointAt(1) // 57271
s.codePointAt(2) // 97
s.codePointAt(2).toString(16) // "61"

//codePointAt方法是测试一个字符由两个字节还是由四个字节组成的最简单方法。
function is32Bit(c) {
  return c.codePointAt(0) > 0xFFFF;
}
is32Bit("𠮷") // true
is32Bit("a") // false
//ES5提供String.fromCharCode方法，用于从码点返回对应字符，但是这个方法不能识别32位的UTF-16字符（Unicode编号大于0xFFFF）.所以0x20BB7就发生了溢出，最高位2被舍弃了，最后返回码点U+0BB7对应的字符，而不是码点U+20BB7对应的字符。
//String.fromCodePoint
String.fromCodePoint(0x20BB7)
// "𠮷"
String.fromCodePoint(0x78, 0x1f680, 0x79) === 'x\uD83D\uDE80y'
// true
字符串的遍历器接口：for...of循环遍历，可以识别大于0xFFFF的码点
ES5对字符串对象提供charAt方法，返回字符串给定位置的字符。该方法不能识别码点大于0xFFFF的字符。
'abc'.at(0) // "a"
'𠮷'.charAt(0) // "\uD842"
'𠮷'.at(0) // "𠮷"

'\u01D1'==='\u004F\u030C' //false
'\u01D1'.length // 1
'\u004F\u030C'.length // 2
'\u01D1'.normalize() === '\u004F\u030C'.normalize()
// true

var s = 'Hello world!';
s.startsWith('Hello') // true
s.endsWith('!') // true
s.includes('o') // true
'x'.repeat(3) // "xxx"
//如果repeat的参数是负数或者Infinity，会报错。
//如果参数是0到-1之间的小数，则等同于0，这是因为会先进行取整运算。0到-1之间的小数，取整以后等于-0，repeat视同为0。
//参数NaN等同于0。

//模板字符串，
var name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`
//模板字符串之中还能调用函数。
function fn() {
  return "Hello World";
}
`foo ${fn()} bar`
// foo Hello World bar

//ES6还为原生的String对象，提供了一个raw方法。
//String.raw方法，往往用来充当模板字符串的处理函数，返回一个斜杠都被转义（即斜杠前面再加一个斜杠）的字符串，对应于替换变量后的模板字符串。
String.raw`Hi\n${2+3}!`;
// "Hi\\n5!"
String.raw`Hi\u000A!`;
// 'Hi\\u000A!'
String.raw({ raw: 'test' }, 0, 1, 2);
// 't0e1s2t'
// 等同于
String.raw({ raw: ['t','e','s','t'] }, 0, 1, 2);
```

### 正则的扩展

 字符串对象共有4个方法，可以使用正则表达式：`match()`、`replace()`、`search()`和`split()`。 

ES6对正则表达式添加了`u`修饰符，含义为“Unicode模式”，用来正确处理大于`\uFFFF`的Unicode字符。也就是说，会正确处理四个字节的UTF-16编码。

```javascript
/^\uD83D/u.test('\uD83D\uDC2A')
// false
/^\uD83D/.test('\uD83D\uDC2A')
// true
```

对于码点大于`0xFFFF`的Unicode字符，点字符不能识别，必须加上`u`修饰符。

```javascript
var s = '𠮷';

/^.$/.test(s) // false
/^.$/u.test(s) // true
```

 ES6新增了使用大括号表示Unicode字符，这种表示法在正则表达式中必须加上`u`修饰符，才能识别。  使用`u`修饰符后，所有量词都会正确识别码点大于`0xFFFF`的Unicode字符。 

 除了`u`修饰符，ES6还为正则表达式添加了`y`修饰符，叫做“粘连”（sticky）修饰符。 

 `g`修饰符只要剩余位置中存在匹配就可，而`y`修饰符确保匹配必须从剩余的第一个位置开始，这也就是“粘连”的涵义。 

### 数值的扩展

#### 二进制和八进制表达方式

ES6提供了二进制和八进制数值的新的写法，分别用前缀`0b`（或`0B`）和`0o`（或`0O`）表示。

```javascript
0b111110111 === 503 // true
0o767 === 503 // true
```

从ES5开始，在严格模式之中，八进制就不再允许使用前缀`0`表示，ES6进一步明确，要使用前缀`0o`表示。

```javascript
// 非严格模式
(function(){
  console.log(0o11 === 011);
})() // true

// 严格模式
(function(){
  'use strict';
  console.log(0o11 === 011);
})() // Uncaught SyntaxError: Octal literals are not allowed in strict mode.
```

如果要将`0b`和`0o`前缀的字符串数值转为十进制，要使用`Number`方法。

```javascript
Number('0b111')  // 7
Number('0o10')  // 8
```

#### isFinite和isNaN

`Number.isFinite()`用来检查一个数值是否为有限的（finite）。

```javascript
Number.isFinite(15); // true
Number.isFinite(0.8); // true
Number.isFinite(NaN); // false
Number.isFinite(Infinity); // false
Number.isFinite(-Infinity); // false
Number.isFinite('foo'); // false
Number.isFinite('15'); // false
Number.isFinite(true); // false
```

`Number.isNaN()`用来检查一个值是否为`NaN`。

```javascript
Number.isNaN(NaN) // true
Number.isNaN(15) // false
Number.isNaN('15') // false
Number.isNaN(true) // false
Number.isNaN(9/NaN) // true
Number.isNaN('true'/0) // true
Number.isNaN('true'/'true') // true
```

它们与传统的全局方法`isFinite()`和`isNaN()`的区别在于，传统方法先调用`Number()`将非数值的值转为数值，再进行判断，而这两个新方法只对数值有效，非数值一律返回`false`。

#### parseInt和parseFloat 

```javascript
// ES5的写法
parseInt('12.34') // 12
parseFloat('123.45#') // 123.45
// ES6的写法，这样做的目的，是逐步减少全局性方法，使得语言逐步模块化。
Number.parseInt('12.34') // 12
Number.parseFloat('123.45#') // 123.45
```

#### isInteger 

 `Number.isInteger()`用来判断一个数值是否为整数。需要注意的是，在JavaScript内部，整数和浮点数是同样的储存方法，所以3和3.0被视为同一个值。

```javascript
Number.isInteger(25) // true
Number.isInteger(25.0) // true
```

#### Math对象的扩展

##### Math.trunc()

```js
//1.
//Math.trunc方法用于去除一个数的小数部分，返回整数部分。
Math.trunc(4.1) // 4
Math.trunc(4.9) // 4
Math.trunc(-4.1) // -4
Math.trunc(-4.9) // -4
Math.trunc(-0.1234) // -0
//对于非数值，Math.trunc内部使用Number方法将其先转为数值。
Math.trunc('123.456') // 123
Math.trunc(true) //1
Math.trunc(false) // 0
Math.trunc(null) // 0
//对于空值和无法截取整数的值，返回NaN。
Math.trunc(NaN);      // NaN
Math.trunc('foo');    // NaN
Math.trunc();         // NaN
Math.trunc(undefined) // NaN
```

##### Math.sign()

`Math.sign`方法用来判断一个数到底是正数、负数、还是零。

它会返回五种值。

- 参数为正数，返回+1；
- 参数为负数，返回-1；
- 参数为0，返回0；
- 参数为-0，返回-0;
- 其他值，返回NaN。

```js
Math.sign('')  // 0
Math.sign(true)  // +1
Math.sign(false)  // 0
Math.sign(null)  // 0
Math.sign('9')  // +1
Math.sign('foo')  // NaN
Math.sign()  // NaN
Math.sign(undefined)  // NaN
```

##### Math.cbrt()

方法用于计算一个数的立方根。对于非数值，Math.cbrt()方法内部也是先使用Number()方法将其转为数值。

### 数组的扩展

#### from

`Array.from`方法用于将两类对象转为真正的数组：**类似数组的对象（array-like object）和可遍历（iterable）的对象**（包括ES6新增的数据结构Set和Map）。

```javascript
//类似数组的对象
let arrayLike = {
    '0': 'a',
    '1': 'b',
    '2': 'c',
    length: 3
};

// ES5的写法
var arr1 = [].slice.call(arrayLike); // ['a', 'b', 'c']

// ES6的写法
let arr2 = Array.from(arrayLike); // ['a', 'b', 'c']
```

实际应用中，常见的类似数组的对象是DOM操作返回的NodeList集合，以及函数内部的`arguments`对象。`Array.from`都可以将它们转为真正的数组。

只要是部署了**Iterator**接口的数据结构，`Array.from`都能将其转为数组。

```js
// 可遍历（iterable）
Array.from('123123')
// ['1', '2', '3', '1', '2', '3']

let namesSet = new Set(['a', 'b'])
Array.from(namesSet) // ['a', 'b']
```

**值得提醒的是，扩展运算符（...）也可以将某些数据结构转为数组。**

```javascript
// arguments对象
function foo() {
  console.log(arguments); //{ [Iterator]  0: 1, 1: 2 }
  var args = [...arguments];
  return args
}
foo(1,2)

// NodeList对象
[...document.querySelectorAll('div')]
```

扩展运算符背后调用的是遍历器接口（`Symbol.iterator`），如果一个对象没有部署这个接口，就无法转换。`Array.from`方法则是还支持类似数组的对象。所谓类似数组的对象，本质特征只有一点，即必须有`length`属性。因此，任何有`length`属性的对象，都可以通过`Array.from`方法转为数组，而此时扩展运算符就无法转换。

```javascript
Array.from({ length: 3 });
// [ undefined, undefined, undefined ]
```

`Array.from`还可以接受第二个参数，作用类似于数组的`map`方法，用来对每个元素进行处理，将处理后的值放入返回的数组。

```javascript
Array.from(arrayLike, x => x * x);
// 等同于
Array.from(arrayLike).map(x => x * x);

Array.from([1, 2, 3], (x) => x * x)
// [1, 4, 9]
```

下面的例子是取出一组DOM节点的文本内容。

```javascript
let spans = document.querySelectorAll('span.name');

// map()
let names1 = Array.prototype.map.call(spans, s => s.textContent);

// Array.from()
let names2 = Array.from(spans, s => s.textContent)
```

#### Array.of

`Array.of`方法用于将一组值，转换为数组。

```javascript
Array.of(3, 11, 8) // [3,11,8]
Array.of(3) // [3]
Array.of(3).length // 1
```

这个方法的主要目的，是弥补数组构造函数`Array()`的不足。因为参数个数的不同，会导致`Array()`的行为有差异。

```javascript
Array() // []
//参数个数只有一个时，实际上是指定数组的长度。
Array(3) // [, , ,]
Array(3, 11, 8) // [3, 11, 8]
```

`Array.of`基本上可以用来替代`Array()`或`new Array()`，并且不存在由于参数不同而导致的重载。它的行为非常统一。

```javascript
Array.of() // []
Array.of(undefined) // [undefined]
Array.of(1) // [1]
Array.of(1, 2) // [1, 2]
```

#### copyWithin

数组实例的`copyWithin`方法，在当前数组内部，将指定位置的成员复制到其他位置（会覆盖原有成员），然后返回当前数组。也就是说，使用这个方法，会修改当前数组。

```javascript
Array.prototype.copyWithin(target, start = 0, end = this.length)
```

它接受三个参数。

- target（必需）：从该位置开始替换数据。
- start（可选）：从该位置开始读取数据，默认为0。如果为负值，表示倒数。
- end（可选）：到该位置前停止读取数据，默认等于数组长度。如果为负值，表示倒数。

这三个参数都应该是数值，如果不是，会自动转为数值。

```javascript
[1, 2, 3, 4, 5].copyWithin(0, 3)
// [4, 5, 3, 4, 5]
```

上面代码表示将从3号位直到数组结束的成员（4和5），复制到从0号位开始的位置，结果覆盖了原来的1和2。

```js
// 将3号位复制到0号位
[1, 2, 3, 4, 5].copyWithin(0, 3, 4)
// [4, 2, 3, 4, 5]
```

#### find和findIndex

数组实例的`find`方法，用于找出第一个符合条件的数组成员。它的参数是一个回调函数，所有数组成员依次执行该回调函数，直到找出**第一个**返回值为`true`的成员，然后**返回该成员**。如果没有符合条件的成员，则返回`undefined`。

```javascript
[1, 4, -5, 10].find((n) => n < 0)
// -5
```

上面代码找出数组中第一个小于0的成员。

```javascript
[1, 5, 10, 15].find(function(value, index, arr) {
  return value > 9;
}) // 10
```

上面代码中，`find`方法的回调函数可以接受三个参数，依次为当前的值、当前的位置和原数组。

数组实例的`findIndex`方法的用法与`find`方法非常类似，**返回第一个符合条件的数组成员的位置**，如果所有成员都不符合条件，则返回`-1`。

```javascript
[1, 5, 10, 15].findIndex(function(value, index, arr) {
  return value > 9;
}) // 2
```

另外，这两个方法都可以发现`NaN`，弥补了数组的`IndexOf`方法的不足。

```javascript
[NaN].indexOf(NaN)
// -1

[NaN].findIndex(y => Object.is(NaN, y))
// 0
```

上面代码中，`indexOf`方法无法识别数组的`NaN`成员，但是`findIndex`方法可以借助`Object.is`方法做到。

#### fill()

`fill`方法使用给定值，填充一个数组。

```javascript
['a', 'b', 'c'].fill(7)
// [7, 7, 7]

new Array(3).fill(7)
// [7, 7, 7]
```

上面代码表明，`fill`方法用于空数组的初始化非常方便。

#### entries和keys和values

ES6提供三个新的方法——`entries()`，`keys()`和`values()`——用于遍历数组。它们都返回一个遍历器对象（详见《Iterator》一章），可以用`for...of`循环进行遍历，唯一的区别是`keys()`是对键名的遍历、`values()`是对键值的遍历，`entries()`是对键值对的遍历。

```js
for (let index of ['a', 'b'].keys()) {
  console.log(index);
}
// 0
// 1

for (let elem of ['a', 'b'].values()) {
  console.log(elem);
}
// 'a'
// 'b'

for (let [index, elem] of ['a', 'b'].entries()) {
  console.log(index, elem);
}
// 0 "a"
// 1 "b"
```

如果不使用`for...of`循环，可以手动调用遍历器对象的`next`方法，进行遍历。

```javascript
let letter = ['a', 'b', 'c'];
let entries = letter.entries();
console.log(entries.next()); // { value: [ 0, 'a' ], done: false }
console.log(entries.next().value); // [1, 'b']
console.log(entries.next().value); // [2, 'c']
```

#### includes()

`Array.prototype.includes`方法返回一个布尔值，表示某个数组是否包含给定的值，与字符串的`includes`方法类似。该方法属于ES7，但Babel转码器已经支持。

```javascript
[1, 2, 3].includes(2);     // true
[1, 2, 3].includes(4);     // false
[1, 2, NaN].includes(NaN); // true
```

```
[{a:1},2,3,{b:2}].includes({a:1})//false
```

#### 数组的空位

数组的空位指，数组的某一个位置没有任何值。比如，`Array`构造函数返回的数组都是空位。

```javascript
Array(3) // [, , ,]
```

上面代码中，`Array(3)`返回一个具有3个空位的数组。

注意，空位不是`undefined`，一个位置的值等于`undefined`，依然是有值的。空位是没有任何值，`in`运算符可以说明这一点。

```javascript
0 in [undefined, undefined, undefined] // true
0 in [, , ,] // false
```

上面代码说明，第一个数组的0号位置是有值的，第二个数组的0号位置没有值。

ES5对空位的处理，已经很不一致了，大多数情况下会忽略空位。

- `forEach()`, `filter()`, `every()` 和`some()`都会跳过空位。
- `map()`会跳过空位，但会保留这个值
- `join()`和`toString()`会将空位视为`undefined`，而`undefined`和`null`会被处理成空字符串。

```javascript
// forEach方法
[,'a'].forEach((x,i) => console.log(i)); // 1

// filter方法
['a',,'b'].filter(x => true) // ['a','b']

// every方法
[,'a'].every(x => x==='a') // true

// some方法
[,'a'].some(x => x !== 'a') // false

// map方法
[,'a'].map(x => 1) // [,1]

// join方法
[,'a',undefined,null].join('#') // "#a##"

// toString方法
[,'a',undefined,null].toString() // ",a,,"
```

ES6则是明确将空位转为`undefined`。

`Array.from`方法会将数组的空位，转为`undefined`，也就是说，这个方法不会忽略空位。

```javascript
Array.from(['a',,'b'])
// [ "a", undefined, "b" ]
```

扩展运算符（`...`）也会将空位转为`undefined`。

```javascript
[...['a',,'b']]
// [ "a", undefined, "b" ]
```

`copyWithin()`会连空位一起拷贝。

```javascript
[,'a','b',,].copyWithin(2,0) // [,"a",,"a"]
```

`fill()`会将空位视为正常的数组位置。

```javascript
new Array(3).fill('a') // ["a","a","a"]
```

`for...of`循环也会遍历空位。

```javascript
let arr = [, ,];
for (let i of arr) {
  console.log(1);
}
// 1
// 1
```

上面代码中，数组`arr`有两个空位，`for...of`并没有忽略它们。如果改成`map`方法遍历，空位是会跳过的。

`entries()`、`keys()`、`values()`、`find()`和`findIndex()`会将空位处理成`undefined`。

```javascript
// entries()
[...[,'a'].entries()] // [[0,undefined], [1,"a"]]

// keys()
[...[,'a'].keys()] // [0,1]

// values()
[...[,'a'].values()] // [undefined,"a"]

// find()
[,'a'].find(x => true) // undefined

// findIndex()
[,'a'].findIndex(x => true) // 0
```

由于空位的处理规则非常不统一，所以建议避免出现空位。

### 对象的扩展

#### 简洁表示法

##### **属性简写**

ES6允许直接写入变量和函数，作为对象的属性和方法。这样的书写更加简洁。

```javascript
var foo = 'bar';
var baz = {foo};
 // {foo: "bar"}

// 等同于
var baz = {foo: foo};
```

上面代码表明，ES6允许在对象之中，直接写变量。这时，属性名为变量名, 属性值为变量的值。下面是另一个例子。

```javascript
function f(x, y) {
  return {x, y};
}

// 等同于

function f(x, y) {
  return {x: x, y: y};
}

f(1, 2) // Object {x: 1, y: 2}
```

##### **方法简写**

```javascript
var o = {
    method() {
        return "Hello!";
    }
};

// 等同于

var o = {
  method: function() {
    return "Hello!";
  }
};
```

下面是一个实际的例子。

```javascript
var birth = '2000/01/01';

var Person = {
  name: '张三',
  //等同于birth: birth
  birth,
  // 等同于hello: function ()...
  hello() { console.log('我的名字是', this.name); }

};
```

这种写法用于函数的返回值，将会非常方便。

```javascript
function getPoint() {
  var x = 1;
  var y = 10;
  return {x, y};
}

getPoint()
// {x:1, y:10}
```

CommonJS模块输出变量，就非常合适使用简洁写法。

```javascript
var ms = {};
function getItem (key) {
  return key in ms ? ms[key] : null;
}
function setItem (key, value) {
  ms[key] = value;
}
function clear () {
  ms = {};
}
module.exports = { getItem, setItem, clear };
// 等同于
module.exports = {
  getItem: getItem,
  setItem: setItem,
  clear: clear
};
```

属性的赋值器（setter）和取值器（getter），事实上也是采用这种写法。

```javascript
var cart = {
  _wheels: 4,

  get wheels () {
    return this._wheels;
  },

  set wheels (value) {
    if (value < this._wheels) {
      throw new Error('数值太小了！');
    }
    this._wheels = value;
  }
}
```

注意，简洁写法的属性名总是字符串，这会导致一些看上去比较奇怪的结果。

```javascript
var obj = {
  class () {}
};

// 等同于

var obj = {
  'class': function() {}
};
```

上面代码中，`class`是字符串，所以不会因为它属于关键字，而导致语法解析报错。

如果某个方法的值是一个Generator函数，前面需要加上星号。

```javascript
var obj = {
  * m(){
    yield 'hello world';
  }
};
```

#### 属性名表达式

JavaScript语言定义对象的属性，有两种方法。

```javascript
var obj = {
  foo: true,
  abc: 123
};
// 方法一
obj.foo = true;

// 方法二
obj['a' + 'bc'] = 123;
```

方法一是直接用标识符作为属性名，方法二是用表达式作为属性名，这时要将表达式放在方括号之内。

ES6 允许字面量定义对象时，用方法二（表达式）作为对象的属性名，即把表达式放在方括号内。

```javascript
let propKey = 'foo';

let obj = {
  [propKey]: true,
  ['a' + 'bc']: 123
};
```

下面是另一个例子。

```javascript
var lastWord = 'last word';

var a = {
  'first word': 'hello',
  [lastWord]: 'world'
};

a['first word'] // "hello"
a[lastWord] // "world"
a['last word'] // "world"
```

表达式还可以用于定义方法名。

```javascript
let obj = {
  ['h' + 'ello']() {
    return 'hi';
  }
};

obj.hello() // hi
```

注意，**属性名表达式与简洁表示法，不能同时使用**，会报错。

```javascript
// 报错
var foo = 'bar';
var bar = 'abc';
var baz = { [foo] };

// 正确
var foo = 'bar';
var baz = { [foo]: 'abc'};
```

注意，属性名表达式如果是一个对象，默认情况下会自动将对象转为字符串`[object Object]`，这一点要特别小心。

```javascript
const keyA = {a: 1};
const keyB = {b: 2};

const myObject = {
  [keyA]: 'valueA',
  [keyB]: 'valueB'
};

myObject // Object {[object Object]: "valueB"}
```

上面代码中，`[keyA]`和`[keyB]`得到的都是`[object Object]`，所以`[keyB]`会把`[keyA]`覆盖掉，而`myObject`最后只有一个`[object Object]`属性。

#### 方法的name属性

函数的`name`属性，返回函数名。对象方法也是函数，因此也有`name`属性。

```javascript
var person = {
  sayName() {
  },
};
console.log(person.sayName.name);   // "sayName"
```

#### 对象的扩展运算符

目前，ES7有一个[提案](https://github.com/sebmarkbage/ecmascript-rest-spread)，将Rest运算符（解构赋值）/扩展运算符（`...`）引入对象。Babel转码器已经支持这项功能。

##### 解构赋值

对象的解构赋值用于从一个对象取值，相当于将所有可遍历的、但尚未被读取的属性，分配到指定的对象上面。所有的键和它们的值，都会拷贝到新对象上面。

```javascript
let { x, y, ...z } = { x: 1, y: 2, a: 3, b: 4 };
x // 1
y // 2
z // { a: 3, b: 4 }
```

上面代码中，变量`z`是解构赋值所在的对象。它获取等号右边的所有尚未读取的键（`a`和`b`），将它们连同值一起拷贝过来。

由于解构赋值要求等号右边是一个对象，所以如果等号右边是`undefined`或`null`，就会报错，因为它们无法转为对象。

```javascript
let { x, y, ...z } = null; // 运行时错误
let { x, y, ...z } = undefined; // 运行时错误
```

解构赋值必须是最后一个参数，否则会报错。

```javascript
let { ...x, y, z } = obj; // 句法错误
let { x, ...y, ...z } = obj; // 句法错误
```

上面代码中，解构赋值不是最后一个参数，所以会报错。

注意，解构赋值的拷贝是浅拷贝，即如果一个键的值是复合类型的值（数组、对象、函数）、那么解构赋值拷贝的是这个值的引用，而不是这个值的副本。

```javascript
let obj = { a: { b: 1 } };
let { ...x } = obj;
obj.a.b = 2;
x.a.b // 2
```

上面代码中，`x`是解构赋值所在的对象，拷贝了对象`obj`的`a`属性。`a`属性引用了一个对象，修改这个对象的值，会影响到解构赋值对它的引用。

另外，**解构赋值不会拷贝继承自原型对象的属性**。

```javascript
let o1 = { a: 1 };
let o2 = { b: 2 };
o2.__proto__ = o1;
let o3 = { ...o2 };
o3 // { b: 2 }
```

上面代码中，对象`o3`是`o2`的拷贝，但是只复制了`o2`自身的属性，没有复制它的原型对象`o1`的属性。

下面是另一个例子。

```javascript
var o = Object.create({ x: 1, y: 2 });
o.z = 3;

let { x, ...{ y, z } } = o;
x // 1
y // undefined
z // 3
```

上面代码中，变量`x`是单纯的解构赋值，所以可以读取继承的属性；解构赋值产生的变量`y`和`z`，只能读取对象自身的属性，所以只有变量`z`可以赋值成功。

解构赋值的一个用处，是扩展某个函数的参数，引入其他操作。

```javascript
function baseFunction({ a, b }) {
  // ...
}
function wrapperFunction({ x, y, ...restConfig }) {
  // 使用x和y参数进行操作
  // 其余参数传给原始函数
  return baseFunction(restConfig);
}
```

上面代码中，原始函数`baseFunction`接受`a`和`b`作为参数，函数`wrapperFunction`在`baseFunction`的基础上进行了扩展，能够接受多余的参数，并且保留原始函数的行为。

##### 扩展运算符

扩展运算符（`...`）用于取出参数对象的所有可遍历属性，拷贝到当前对象之中。

```javascript
let z = { a: 3, b: 4 };
let n = { ...z };
n // { a: 3, b: 4 }
```

这等同于使用`Object.assign`方法。

```javascript
let aClone = { ...a };
// 等同于
let aClone = Object.assign({}, a);
```

扩展运算符可以用于合并两个对象。

```javascript
let ab = { ...a, ...b };
// 等同于
let ab = Object.assign({}, a, b);
```

如果用户自定义的属性，放在扩展运算符后面，则扩展运算符内部的同名属性会被覆盖掉。

```javascript
let aWithOverrides = { ...a, x: 1, y: 2 };
// 等同于
let aWithOverrides = { ...a, ...{ x: 1, y: 2 } };
// 等同于
let x = 1, y = 2, aWithOverrides = { ...a, x, y };
// 等同于
let aWithOverrides = Object.assign({}, a, { x: 1, y: 2 });
```

上面代码中，`a`对象的`x`属性和`y`属性，拷贝到新对象后会被覆盖掉。

这用来修改现有对象部分的部分属性就很方便了。

```javascript
let newVersion = {
  ...previousVersion,
  name: 'New Name' // Override the name property
};
```

上面代码中，`newVersion`对象自定义了`name`属性，其他属性全部复制自`previousVersion`对象。

如果把自定义属性放在扩展运算符前面，就变成了设置新对象的默认属性值。

```javascript
let aWithDefaults = { x: 1, y: 2, ...a };
// 等同于
let aWithDefaults = Object.assign({}, { x: 1, y: 2 }, a);
// 等同于
let aWithDefaults = Object.assign({ x: 1, y: 2 }, a);
```

扩展运算符的参数对象之中，如果有取值函数`get`，这个函数是会执行的。

```javascript
// 并不会抛出错误，因为x属性只是被定义，但没执行
let aWithXGetter = {
  ...a,
  get x() {
    throws new Error('not thrown yet');
  }
};

// 会抛出错误，因为x属性被执行了
let runtimeError = {
  ...a,
  ...{
    get x() {
      throws new Error('thrown now');
    }
  }
};
```

如果扩展运算符的参数是`null`或`undefined`，这个两个值会被忽略，不会报错。

```javascript
let emptyObject = { ...null, ...undefined }; // 不报错
```

#### 属性描述对象

JavaScript 提供了一个内部数据结构，用来描述对象的属性，控制它的行为，比如该属性是否可写、可遍历等等。这个内部数据结构称为“属性描述对象”（attributes object）。每个属性都有自己对应的属性描述对象，保存该属性的一些元信息。

属性描述对象提供6个元属性。

（1）`value`

`value`是该属性的属性值，默认为`undefined`。

（2）`writable`

`writable`是一个布尔值，表示属性值（value）是否可改变（即是否可写），默认为`true`。

（3）`enumerable`

`enumerable`是一个布尔值，**表示该属性是否可遍历**，默认为`true`。如果设为`false`，会使得某些操作（比如`for...in`循环、`Object.keys()`）跳过该属性。

（4）`configurable`

`configurable`是一个布尔值，表示属性的可配置性，默认为`true`。如果设为`false`，将阻止某些操作改写属性描述对象，比如无法删除该属性，也不得改变各种元属性（`value`属性除外）。也就是说，`configurable`属性控制了属性描述对象的可写性。

（5）`get`

`get`是一个函数，表示该属性的取值函数（getter），默认为`undefined`。

（6）`set`

```
set`是一个函数，表示该属性的存值函数（setter），默认为`undefined
```

##### 属性的可枚举性

对象的每个属性都有一个描述对象（Descriptor），用来控制该属性的行为。`Object.getOwnPropertyDescriptor`方法可以获取该属性的描述对象。

```javascript
let obj = { foo: 123 };
Object.getOwnPropertyDescriptor(obj, 'foo')
//  {
//    value: 123,
//    writable: true,
//    enumerable: true,
//    configurable: true
//  }
```

描述对象的`enumerable`属性，称为”可枚举性“，如果该属性为`false`，就表示某些操作会忽略当前属性。

ES5有三个操作会台跳过`enumerable`为`false`的属性。

- `for...in`循环：只遍历对象自身的和**继承**的可枚举的属性
- `Object.keys()`：返回对象自身的所有可枚举的属性的键名
- `JSON.stringify()`：只串行化对象自身的可枚举的属性

ES6新增了一个操作`Object.assign()`，会忽略`enumerable`为`false`的属性，只拷贝对象自身的可枚举的属性。

**这四个操作之中，只有`for...in`会返回继承的属性。实际上，引入`enumerable`的最初目的，就是让某些属性可以规避掉`for...in`操作。**

比如，对象原型的`toString`方法，以及数组的`length`属性，就通过这种手段，不会被`for...in`遍历到。

```javascript
Object.getOwnPropertyDescriptor(Object.prototype, 'toString').enumerable
// false

Object.getOwnPropertyDescriptor([], 'length').enumerable
// false
```

上面代码中，`toString`和`length`属性的`enumerable`都是`false`，因此`for...in`不会遍历到这两个继承自原型的属性。

**另外，ES6规定，所有Class的原型的方法都是不可枚举的。**

```javascript
Object.getOwnPropertyDescriptor(class {foo() {}}.prototype, 'foo').enumerable
// false
```

总的来说，操作中引入继承的属性会让问题复杂化，大多数时候，我们只关心对象自身的属性。所以，尽量不要用`for...in`循环，而用`Object.keys()`代替。

##### get和set

```js
var person = {
  firstName: 'liming',
  sayName() {
    return this.secondName
  },
  get secondName() {
    return "Nicholas";
  },
  set secondName(a) {//set里面不能给secondName赋值
    console.log(a, this.firstName);//newliming liming
    this.firstName = a
  }
};
person.secondName = 'newliming'
console.log(person.firstName, person.secondName)//newliming Nicholas
console.log(person.sayName())//Nicholas
```

当使用 `get` 关键字时，属性将被定义在实例的原型上，当使用[`Object.defineProperty()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)时，属性将被定义在实例自身上。

#### 属性的遍历

##### **for...in**

`for...in`循环遍历对象自身的和继承的可枚举属性（不含Symbol属性）。

##### Object.keys()

ES5引入了`Object.keys`方法，返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键名。

```javascript
var obj = { foo: "bar", baz: 42 };
Object.keys(obj)
// ["foo", "baz"]
```

目前，ES7有一个[提案](https://github.com/tc39/proposal-object-values-entries)，引入了跟`Object.keys`配套的`Object.values`和`Object.entries`。

```javascript
let {keys, values, entries} = Object;
let obj = { a: 1, b: 2, c: 3 };

for (let key of keys(obj)) {
  console.log(key); // 'a', 'b', 'c'
}

for (let value of values(obj)) {
  console.log(value); // 1, 2, 3
}

for (let [key, value] of entries(obj)) {
  console.log([key, value]); // ['a', 1], ['b', 2], ['c', 3]
}
```

##### Object.values()

`Object.values`方法返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键值。

```javascript
var obj = { foo: "bar", baz: 42 };
Object.values(obj)
// ["bar", 42]
```

返回数组的成员顺序，与本章的《属性的遍历》部分介绍的排列规则一致。

```javascript
var obj = { 100: 'a', 2: 'b', 7: 'c' };
Object.values(obj)
// ["b", "c", "a"]
```

上面代码中，属性名为数值的属性，是按照数值大小，从小到大遍历的，因此返回的顺序是`b`、`c`、`a`。

`Object.values`只返回对象自身的可遍历属性。

```javascript
var obj = Object.create({}, {p: {value: 42}});
Object.values(obj) // []
```

上面代码中，`Object.create`方法的第二个参数添加的对象属性（属性`p`），如果不显式声明，默认是不可遍历的。`Object.values`不会返回这个属性。

`Object.values`会过滤属性名为Symbol值的属性。

```javascript
Object.values({ [Symbol()]: 123, foo: 'abc' });
// ['abc']
```

如果`Object.values`方法的参数是一个字符串，会返回各个字符组成的一个数组。

```javascript
Object.values('foo')
// ['f', 'o', 'o']
```

上面代码中，字符串会先转成一个类似数组的对象。字符串的每个字符，就是该对象的一个属性。因此，`Object.values`返回每个属性的键值，就是各个字符组成的一个数组。

如果参数不是对象，`Object.values`会先将其转为对象。由于数值和布尔值的包装对象，都不会为实例添加非继承的属性。所以，`Object.values`会返回空数组。

```javascript
Object.values(42) // []
Object.values(true) // []
```

##### Object.entries

`Object.entries`方法返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键值对数组。

```javascript
var obj = { foo: 'bar', baz: 42 };
console.log(Object.entries(obj));
// [ ["foo", "bar"], ["baz", 42] ]
```

除了返回值不一样，该方法的行为与`Object.values`基本一致。

如果原对象的属性名是一个Symbol值，该属性会被省略。

```javascript
Object.entries({ [Symbol()]: 123, foo: 'abc' });
// [ [ 'foo', 'abc' ] ]
```

上面代码中，原对象有两个属性，`Object.entries`只输出属性名非Symbol值的属性。将来可能会有`Reflect.ownEntries()`方法，返回对象自身的所有属性。

`Object.entries`的基本用途是遍历对象的属性。

```javascript
let obj = { one: 1, two: 2 };
for (let [k, v] of Object.entries(obj)) {
  console.log(`${JSON.stringify(k)}: ${JSON.stringify(v)}`);
}
// "one": 1
// "two": 2
```

`Object.entries`方法的一个用处是，将对象转为真正的`Map`结构。

```javascript
var obj = { foo: 'bar', baz: 42 };
var map = new Map(Object.entries(obj));
map // Map { foo: "bar", baz: 42 }
```

自己实现`Object.entries`方法，非常简单。

```javascript
// Generator函数的版本
function* entries(obj) {
  for (let key of Object.keys(obj)) {
    yield [key, obj[key]];
  }
}

// 非Generator函数的版本
function entries(obj) {
  let arr = [];
  for (let key of Object.keys(obj)) {
    arr.push([key, obj[key]]);
  }
  return arr;
}
```

#### 方法

##### 属性删除

`delete`命令用于删除对象的属性，删除成功后返回`true`。 

```js
var obj = {};
delete obj.p // true
```

 删除一个不存在的属性，`delete`不报错，而且返回`true`。 

 只有一种情况，`delete`命令会返回`false`，那就是该属性存在，且不得删除。 

```js
var obj = Object.defineProperty({}, 'p', {
  value: 123,
  configurable: false
});

obj.p // 123
delete obj.p // false
```

 **`delete`命令只能删除对象本身的属性，无法删除继承的属性** 

```js
var obj = {};
delete obj.toString // true
obj.toString // function toString() { [native code] }
```

##### **属性是否存在：in**

```js
var obj = { p: 1 };
'p' in obj // true
'toString' in obj // true
```

 `in`运算符的一个问题是，**它不能识别哪些属性是对象自身的，哪些属性是继承的。** 
 可以使用对象的`hasOwnProperty`方法判断一下，是否为对象自身的属性。 

```js
var obj = {};
if ('toString' in obj) {
  console.log(obj.hasOwnProperty('toString')) // false
}
```

##### valueOf()

**`valueOf`方法的作用是返回一个对象的“值”，默认情况下返回对象本身。**

```js
var obj = new Object();
console.log(obj.valueOf() === obj);// true
```

```js
var obj = new Object();
1 + obj // "1[object Object]"

let obj = new Object();
obj ={
  valueOf:function(){
    return {}
  },
  toString:function(){
    return 1
  }
}
console.log(1 + obj);// 2
```

上面代码将对象`obj`与数字`1`相加，这时 JavaScript 就会默认调用`valueOf()`和`toString`方法，求出`obj`的值再与`1`相加。

##### toString() 

`Object.prototype.toString`方法**返回对象的类型字符串，因此可以用来判断一个值的类型。**

```js
var obj = {};
obj.toString() // "[object Object]"
```

返回一个字符串`object Object`，其中第二个`Object`表示该值的构造函数。

由于实例对象可能会自定义`toString`方法，覆盖掉`Object.prototype.toString`方法，所以为了得到类型字符串，最好直接使用`Object.prototype.toString`方法。通过函数的`call`方法，可以在任意值上调用这个方法，帮助我们判断这个值的类型。

```js
Object.prototype.toString.call(value)
```

上面代码表示对`value`这个值调用`Object.prototype.toString`方法。

不同数据类型的`Object.prototype.toString`方法返回值如下。

- 数值：返回`[object Number]`。
- 字符串：返回`[object String]`。
- 布尔值：返回`[object Boolean]`。
- undefined：返回`[object Undefined]`。
- null：返回`[object Null]`。
- 数组：返回`[object Array]`。
- arguments 对象：返回`[object Arguments]`。
- 函数：返回`[object Function]`。
- Error 对象：返回`[object Error]`。
- Date 对象：返回`[object Date]`。
- RegExp 对象：返回`[object RegExp]`。
- 其他对象：返回`[object Object]`。

https://wangdoc.com/javascript/stdlib/attributes.html

##### Object.create

方法创建一个新对象，使用现有的对象来提供新创建的对象的__proto__。 

```js
Object.create(proto，[propertiesObject])
```

- proto
  新创建对象的原型对象。

- propertiesObject
  可选。需要传入一个对象，**该对象的属性类型参照Object.defineProperties()的第二个参数**。如果该参数被指定且不为 undefined，该传入对象的自有可枚举属性(即其自身定义的属性，而不是其原型链上的枚举属性)将为新创建的对象添加指定的属性值和对应的属性描述符。

  ```js
  let obj = Object.create({a:1}, {b:3})
  // Uncaught TypeError: Property description must be an object: 3
  let obj = Object.create({a:1}, {b:{value:2}})
  console.log(obj) // {b: 2}
  console.log(obj.a) // 1
  obj.b = 3
  console.log(obj.b) // 2
  console.log(Object.keys(obj))// []
   
   
  let obj2 = Object.create(null,{a:{
      value: 2,       // 属性值
      writable: true,     //  是否可以重写值
      enumerable: true,   //是否可枚举
      configurable: true  //是否可以修改以上几项配置
  }})
   
  console.log(obj2)//{a: 2}
  obj2.a=3
  console.log(obj2)// {a: 3}
  Object.keys(obj2)// ["a"]
  
  ```

##### Object.is

ES5比较两个值是否相等，只有两个运算符：相等运算符（`==`）和严格相等运算符（`===`）。它们都有缺点，前者会**自动转换数据类型**，后者的`NaN`不等于自身，以及`+0`等于`-0`。JavaScript缺乏一种运算，在所有环境中，只要两个值是一样的，它们就应该相等。

ES6提出“Same-value equality”（同值相等）算法，用来解决这个问题。`Object.is`就是部署这个算法的新方法**。它用来比较两个值是否严格相等，与严格比较运算符（===）的行为基本一致。**

```javascript
Object.is('foo', 'foo')
// true
Object.is({}, {})
// false
```

不同之处只有两个：一是`+0`不等于`-0`，二是`NaN`等于自身。

```javascript
+0 === -0 //true
NaN === NaN // false

Object.is(+0, -0) // false
Object.is(NaN, NaN) // true
```

ES5可以通过下面的代码，部署`Object.is`。

```javascript
Object.defineProperty(Object, 'is', {
  value: function(x, y) {
    if (x === y) {
      // 针对+0 不等于 -0的情况
      return x !== 0 || 1 / x === 1 / y;
    }
    // 针对NaN的情况
    return x !== x && y !== y;
  },
  configurable: true,
  enumerable: false,
  writable: true
});
```

##### Object.assign()

https://www.cnblogs.com/xiaoxiaoxun/p/12157591.html

**基本用法**

`Object.assign`方法用于对象的合并，将源对象（source）的**所有可枚举属性**，复制到目标对象（target）。

```javascript
var target = { a: 1 };

var source1 = { b: 2 };
var source2 = { c: 3 };

Object.assign(target, source1, source2);
target // {a:1, b:2, c:3}
```

`Object.assign`方法的第一个参数是目标对象，后面的参数都是源对象。

注意，如果目标对象与源对象有同名属性，或多个源对象有同名属性，则后面的属性会覆盖前面的属性。

```javascript
var target = { a: 1, b: 1 };

var source1 = { b: 2, c: 2 };
var source2 = { c: 3 };

Object.assign(target, source1, source2);
target // {a:1, b:2, c:3}
```

如果只有一个参数，`Object.assign`会直接返回该参数。

```javascript
var obj = {a: 1};
Object.assign(obj) === obj // true
```

如果该参数不是对象，则会先转成对象，然后返回。

```javascript
typeof Object.assign(2) // "object"
```

由于`undefined`和`null`无法转成对象，所以如果它们作为参数，就会报错。

```javascript
Object.assign(undefined) // 报错
Object.assign(null) // 报错
```

如果非对象参数出现在源对象的位置（即非首参数），那么处理规则有所不同。首先，这些参数都会转成对象，如果无法转成对象，就会跳过。这意味着，如果`undefined`和`null`不在首参数，就不会报错。

```javascript
let obj = {a: 1};
Object.assign(obj, undefined) === obj // true
Object.assign(obj, null) === obj // true
```

其他类型的值（即数值、字符串和布尔值）不在首参数，也不会报错。但是，除了字符串会以数组形式，拷贝入目标对象，其他值都不会产生效果。

```javascript
var v1 = 'abc';
var v2 = true;
var v3 = 10;

var obj = Object.assign({}, v1, v2, v3);
console.log(obj); // { "0": "a", "1": "b", "2": "c" }
```

上面代码中，`v1`、`v2`、`v3`分别是字符串、布尔值和数值，结果只有字符串合入目标对象（以字符数组的形式），数值和布尔值都会被忽略。这是因为只有字符串的包装对象，会产生可枚举属性。

```javascript
Object(true) // {[[PrimitiveValue]]: true}
Object(10)  //  {[[PrimitiveValue]]: 10}
Object('abc') // {0: "a", 1: "b", 2: "c", length: 3, [[PrimitiveValue]]: "abc"}
```

上面代码中，布尔值、数值、字符串分别转成对应的包装对象，可以看到它们的原始值都在包装对象的内部属性`[[PrimitiveValue]]`上面，这个属性是不会被`Object.assign`拷贝的。只有字符串的包装对象，会产生可枚举的实义属性，那些属性则会被拷贝。

`Object.assign`拷贝的属性是有限制的，只拷贝源对象的自身属性（不拷贝继承属性），也不拷贝不可枚举的属性（`enumerable: false`）。

```javascript
Object.assign({b: 'c'},
  Object.defineProperty({}, 'invisible', {
    enumerable: false,
    value: 'hello'
  })
)
// { b: 'c' }
```

上面代码中，`Object.assign`要拷贝的对象只有一个不可枚举属性`invisible`，这个属性并没有被拷贝进去。

属性名为Symbol值的属性，也会被`Object.assign`拷贝。

```javascript
Object.assign({ a: 'b' }, { [Symbol('c')]: 'd' })
// { a: 'b', Symbol(c): 'd' }
```

**注意点**

`Object.assign`方法实行的是**浅拷贝**，而不是深拷贝。也就是说，如果源对象某个属性的值是对象，那么目标对象拷贝得到的是这个对象的引用。

```javascript
var obj1 = {a: {b: 1}};
var obj2 = Object.assign({}, obj1);

obj1.a.b = 2;
obj2.a.b // 2
```

上面代码中，源对象`obj1`的`a`属性的值是一个对象，`Object.assign`拷贝得到的是这个对象的引用。这个对象的任何变化，都会反映到目标对象上面。

对于这种嵌套的对象，一旦遇到同名属性，`Object.assign`的处理方法是替换，而不是添加。

```javascript
var target = { a: { b: 'c', d: 'e' } }
var source = { a: { b: 'hello' } }
Object.assign(target, source)
// { a: { b: 'hello' } }
```

上面代码中，`target`对象的`a`属性被`source`对象的`a`属性整个替换掉了，而不会得到`{ a: { b: 'hello', d: 'e' } }`的结果。这通常不是开发者想要的，需要特别小心。

有一些函数库提供`Object.assign`的定制版本（比如Lodash的`_.defaultsDeep`方法），可以解决浅拷贝的问题，得到深拷贝的合并。

注意，`Object.assign`可以用来处理数组，但是会把数组视为对象。

```javascript
Object.assign([1, 2, 3], [4, 5])
// [4, 5, 3]
```

上面代码中，`Object.assign`把数组视为属性名为0、1、2的对象，因此目标数组的0号属性`4`覆盖了原数组的0号属性`1`。

**常见用途**

`Object.assign`方法有很多用处。

**（1）为对象添加属性**

```javascript
class Point {
  constructor(x, y) {
    Object.assign(this, {x, y});
  }
}
```

上面方法通过`Object.assign`方法，将`x`属性和`y`属性添加到`Point`类的对象实例。

**（2）为对象添加方法**

```javascript
Object.assign(SomeClass.prototype, {
  someMethod(arg1, arg2) {
    ···
  },
  anotherMethod() {
    ···
  }
});

// 等同于下面的写法
SomeClass.prototype.someMethod = function (arg1, arg2) {
  ···
};
SomeClass.prototype.anotherMethod = function () {
  ···
};
```

上面代码使用了对象属性的简洁表示法，直接将两个函数放在大括号中，再使用assign方法添加到SomeClass.prototype之中。

**（3）克隆对象**

```javascript
function clone(origin) {
  return Object.assign({}, origin);
}
```

上面代码将原始对象拷贝到一个空对象，就得到了原始对象的克隆。

**不过，采用这种方法克隆，只能克隆原始对象自身的值，不能克隆它继承的值。如果想要保持继承链，可以采用下面的代码。**

```javascript
function clone(origin) {
  let originProto = Object.getPrototypeOf(origin);
  return Object.assign(Object.create(originProto), origin);
}
```

**（4）合并多个对象**

将多个对象合并到某个对象。

```javascript
const merge =
  (target, ...sources) => Object.assign(target, ...sources);
```

如果希望合并后返回一个新对象，可以改写上面函数，对一个空对象合并。

```javascript
const merge =
  (...sources) => Object.assign({}, ...sources);
```

**（5）为属性指定默认值**

```javascript
const DEFAULTS = {
  logLevel: 0,
  outputFormat: 'html'
};

function processContent(options) {
  options = Object.assign({}, DEFAULTS, options);
}
```

上面代码中，`DEFAULTS`对象是默认值，`options`对象是用户提供的参数。`Object.assign`方法将`DEFAULTS`和`options`合并成一个新对象，如果两者有同名属性，则`option`的属性值会覆盖`DEFAULTS`的属性值。

注意，由于存在深拷贝的问题，`DEFAULTS`对象和`options`对象的所有属性的值，都只能是简单类型，而不能指向另一个对象。否则，将导致`DEFAULTS`对象的该属性不起作用。

##### Prototype

###### **`__proto__`**

`__proto__`属性（前后各两个下划线），用来读取或设置当前对象的`prototype`对象。目前，所有浏览器（包括IE11）都部署了这个属性。

```javascript
// es6的写法
var obj = {
  method: function() { ... }
};
obj.__proto__ = someOtherObj;

// es5的写法
var obj = Object.create(someOtherObj);
obj.method = function() { ... };
```

该属性没有写入ES6的正文，而是写入了附录，原因是`__proto__`前后的双下划线，说明它本质上是一个内部属性，而不是一个正式的对外的API，只是由于浏览器广泛支持，才被加入了ES6。标准明确规定，只有浏览器必须部署这个属性，其他运行环境不一定需要部署，而且新的代码最好认为这个属性是不存在的。因此，无论从语义的角度，还是从兼容性的角度，都不要使用这个属性，而是使用下面的`Object.setPrototypeOf()`（写操作）、`Object.getPrototypeOf()`（读操作）、`Object.create()`（生成操作）代替。

在实现上，`__proto__`调用的是`Object.prototype.__proto__`，具体实现如下。

```javascript
Object.defineProperty(Object.prototype, '__proto__', {
  get() {
    let _thisObj = Object(this);
    return Object.getPrototypeOf(_thisObj);
  },
  set(proto) {
    if (this === undefined || this === null) {
      throw new TypeError();
    }
    if (!isObject(this)) {
      return undefined;
    }
    if (!isObject(proto)) {
      return undefined;
    }
    let status = Reflect.setPrototypeOf(this, proto);
    if (!status) {
      throw new TypeError();
    }
  },
});
function isObject(value) {
  return Object(value) === value;
}
```

如果一个对象本身部署了`__proto__`属性，则该属性的值就是对象的原型。

```javascript
Object.getPrototypeOf({ __proto__: null })
// null
```

###### getPrototypeOf

该方法与setPrototypeOf方法配套，用于读取一个对象的prototype对象。

```javascript
Object.getPrototypeOf(obj);
```

下面是一个例子。

```javascript
function Rectangle() {
}

var rec = new Rectangle();

Object.getPrototypeOf(rec) === Rectangle.prototype
// true

Object.setPrototypeOf(rec, Object.prototype);
Object.getPrototypeOf(rec) === Rectangle.prototype
// false
```

###### setPrototypeOf

`Object.setPrototypeOf`方法的作用与`__proto__`相同，用来设置一个对象的`prototype`对象。它是ES6正式推荐的设置原型对象的方法。

```javascript
// 格式
Object.setPrototypeOf(object, prototype)

// 用法
var o = Object.setPrototypeOf({}, null);
```

该方法等同于下面的函数。

```javascript
function (obj, proto) {
  obj.__proto__ = proto;
  return obj;
}
```

下面是一个例子。

```javascript
let proto = {};
let obj = { x: 10 };
Object.setPrototypeOf(obj, proto);

proto.y = 20;
proto.z = 40;

obj.x // 10
obj.y // 20
obj.z // 40
```

上面代码将proto对象设为obj对象的原型，所以从obj对象可以读取proto对象的属性。

##### Property

###### defineProperty()

```
Object.defineProperty(obj, prop, descriptor)
```

- **参数**
  obj
  要定义属性的对象。
- prop
  要定义或修改的属性的名称或 Symbol 。
- descriptor
  要定义或修改的属性描述符。

对象里目前存在的属性描述符有两种主要形式：***数据描述符*和*存取描述符***。*数据描述符*是一个具有值的属性，该值可以是可写的，也可以是不可写的。*存取描述符*是由 getter 函数和 setter 函数所描述的属性。一个描述符只能是这两者其中之一；**不能同时是两者**。

- configurable
  true 只有该属性描述符的类型可以被改变并且该属性可以从对应对象中删除。
  默认为 false

- enumerable
  true 只有在枚举相应对象上的属性时该属性显现。
  默认为 false

  `enumerable` 定义了对象的属性是否可以在 [`for...in`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...in) 循环和 [`Object.keys()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/keys) 中被枚举。

  ```js
  var o = {};
  Object.defineProperty(o, "a", { value : 1, enumerable: true });
  Object.defineProperty(o, "b", { value : 2, enumerable: false });
  Object.defineProperty(o, "c", { value : 3 }); // enumerable 默认为 false
  o.d = 4; // 如果使用直接赋值的方式创建对象的属性，则 enumerable 为 true
  Object.defineProperty(o, Symbol.for('e'), {
    value: 5,
    enumerable: true
  });
  Object.defineProperty(o, Symbol.for('f'), {
    value: 6,
    enumerable: false
  });
  for (var i in o) {
    console.log(i);
  }
  // a,d
  Object.keys(o); // ['a', 'd']
  ```

- value
  与属性关联的值。可以是任何有效的JavaScript值（数字，对象，函数等）。
  默认为 undefined.

- writable
  true只有与该属性相关联的值被assignment operator改变时。
  默认为 false

- get
  作为该属性的 getter 函数，如果没有 getter 则为undefined。函数返回值将被用作属性的值。
  默认为 undefined

- set
  作为属性的 setter 函数，如果没有 setter 则为undefined。函数将仅接受参数赋值给该属性的新值。
  默认为 undefined

###### hasOwnProperty() 

方法会返回一个布尔值，指示对象**自身属性**中是否具有指定的属性（也就是，是否有指定的键）。

```js
const object1 = {};
object1.property1 = 42;

console.log(object1.hasOwnProperty('property1'));
// expected output: true
```

即使属性的值是 null 或 undefined，只要属性存在，hasOwnProperty 依旧会返回 true。

```js
o = new Object();
o.propOne = null;
o.hasOwnProperty('propOne'); // 返回 true
o.propTwo = undefined;
o.hasOwnProperty('propTwo'); // 返回 true
```

**自身属性与继承属性**

```js
o = new Object();
o.prop = 'exists';
o.hasOwnProperty('prop');             // 返回 true
o.hasOwnProperty('toString');         // 返回 false
o.hasOwnProperty('hasOwnProperty');   // 返回 false
```

###### getOwnPropertyDescriptors

ES5有一个`Object.getOwnPropertyDescriptor`方法，返回某个对象属性的描述对象（descriptor）。

```javascript
var obj = { p: 'a' };

Object.getOwnPropertyDescriptor(obj, 'p')
// Object { value: "a",
//   writable: true,
//   enumerable: true,
//   configurable: true
// }
```

ES7有一个提案，提出了`Object.getOwnPropertyDescriptors`方法，返回指定对象所有自身属性（非继承属性）的描述对象。

```javascript
const obj = {
  foo: 123,
  get bar() { return 'abc' }
};

Object.getOwnPropertyDescriptors(obj)
// { foo:
//    { value: 123,
//      writable: true,
//      enumerable: true,
//      configurable: true },
//   bar:
//    { get: [Function: bar],
//      set: undefined,
//      enumerable: true,
//      configurable: true } }
```

`Object.getOwnPropertyDescriptors`方法返回一个对象，所有原对象的属性名都是该对象的属性名，对应的属性值就是该属性的描述对象。

该方法的实现非常容易。

```javascript
function getOwnPropertyDescriptors(obj) {
  const result = {};
  for (let key of Reflect.ownKeys(obj)) {
    result[key] = Object.getOwnPropertyDescriptor(obj, key);
  }
  return result;
}
```

该方法的提出目的，主要是为了解决`Object.assign()`无法正确拷贝`get`属性和`set`属性的问题。

```javascript
const source = {
  set foo(value) {
    console.log(value);
  }
};

const target1 = {};
Object.assign(target1, source);

Object.getOwnPropertyDescriptor(target1, 'foo')
// { value: undefined,
//   writable: true,
//   enumerable: true,
//   configurable: true }
```

上面代码中，`source`对象的`foo`属性的值是一个赋值函数，`Object.assign`方法将这个属性拷贝给`target1`对象，结果该属性的值变成了`undefined`。这是因为`Object.assign`方法总是拷贝一个属性的值，而不会拷贝它背后的赋值方法或取值方法。

这时，`Object.getOwnPropertyDescriptors`方法配合`Object.defineProperties`方法，就可以实现正确拷贝。

```javascript
const source = {
  set foo(value) {
    console.log(value);
  }
};

const target2 = {};
Object.defineProperties(target2, Object.getOwnPropertyDescriptors(source));
Object.getOwnPropertyDescriptor(target2, 'foo')
// { get: undefined,
//   set: [Function: foo],
//   enumerable: true,
//   configurable: true }
```

上面代码中，将两个对象合并的逻辑提炼出来，就是下面这样。

```javascript
const shallowMerge = (target, source) => Object.defineProperties(
  target,
  Object.getOwnPropertyDescriptors(source)
);
```

`Object.getOwnPropertyDescriptors`方法的另一个用处，是配合`Object.create`方法，将对象属性克隆到一个新对象。这属于浅拷贝。

```javascript
const clone = Object.create(Object.getPrototypeOf(obj),
  Object.getOwnPropertyDescriptors(obj));

// 或者

const shallowClone = (obj) => Object.create(
  Object.getPrototypeOf(obj),
  Object.getOwnPropertyDescriptors(obj)
);
```

上面代码会克隆对象`obj`。

另外，`Object.getOwnPropertyDescriptors`方法可以实现，一个对象继承另一个对象。以前，继承另一个对象，常常写成下面这样。

```javascript
const obj = {
  __proto__: prot,
  foo: 123,
};
```

ES6规定`__proto__`只有浏览器要部署，其他环境不用部署。如果去除`__proto__`，上面代码就要改成下面这样。

```javascript
const obj = Object.create(prot);
obj.foo = 123;

// 或者

const obj = Object.assign(
  Object.create(prot),
  {
    foo: 123,
  }
);
```

有了`Object.getOwnPropertyDescriptors`，我们就有了另一种写法。

```javascript
const obj = Object.create(
  prot,
  Object.getOwnPropertyDescriptors({
    foo: 123,
  })
);
```

`Object.getOwnPropertyDescriptors`也可以用来实现Mixin（混入）模式。

```javascript
let mix = (object) => ({
  with: (...mixins) => mixins.reduce(
    (c, mixin) => Object.create(
      c, Object.getOwnPropertyDescriptors(mixin)
    ), object)
});

// multiple mixins example
let a = {a: 'a'};
let b = {b: 'b'};
let c = {c: 'c'};
let d = mix(c).with(a, b);
```

上面代码中，对象`a`和`b`被混入了对象`c`。

出于完整性的考虑，`Object.getOwnPropertyDescriptors`进入标准以后，还会有`Reflect.getOwnPropertyDescriptors`方法。

###### getOwnPropertyNames

该方法返回一个数组，其中包含了当前对象**所有属性**的名称（字符串），不论它们是否可枚举。当然，也可以用`Object.keys()`来单独返回可枚举的属性。（不含Symbol属性，但是包括不可枚举属性）

### 函数的扩展

#### 默认值

##### 函数参数的默认值

在ES6之前，不能直接为函数的参数指定默认值，只能采用变通的方法。

```javascript
function log(x, y) {
  y = y || 'World';
  console.log(x, y);
}

log('Hello') // Hello World
log('Hello', 'China') // Hello China
log('Hello', '') // Hello World
```

上面代码检查函数`log`的参数`y`有没有赋值，如果没有，则指定默认值为`World`。这种写法的缺点在于，如果参数`y`赋值了，但是对应的布尔值为`false`，则该赋值不起作用。就像上面代码的最后一行，参数`y`等于空字符，结果被改为默认值。

为了避免这个问题，通常需要先判断一下参数`y`是否被赋值，如果没有，再等于默认值。

```javascript
if (typeof y === 'undefined') {
  y = 'World';
}
```

ES6允许为函数的参数设置默认值，即直接写在参数定义的后面。

```javascript
function log(x, y = 'World') {
  console.log(x, y);
}

log('Hello') // Hello World
log('Hello', 'China') // Hello China
log('Hello', '') // Hello
```

参数变量是默认声明的，所以不能用`let`或`const`再次声明。

```javascript
function foo(x = 5) {
  let x = 1; // error
  const x = 2; // error
}
```

##### 解构赋值默认值

参数默认值可以与解构赋值的默认值，结合起来使用。

```javascript
function foo({x, y = 5}) {
  console.log(x, y);
}

foo({}) // undefined, 5
foo({x: 1}) // 1, 5
foo({x: 1, y: 2}) // 1, 2
foo() // TypeError: Cannot read property 'x' of undefined
```

##### 对比

```
// 写法一
function m1({x = 0, y = 0} = {}) {
  return [x, y];
}

// 写法二
function m2({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}
上面两种写法都对函数的参数设定了默认值，
区别是写法一函数参数的默认值是空对象，但是设置了对象解构赋值的默认值；
写法二函数参数的默认值是一个有具体属性的对象，但是没有设置对象解构赋值的默认值。
// 函数没有参数的情况
m1() // [0, 0]
m2() // [0, 0]

// x和y都有值的情况
m1({x: 3, y: 8}) // [3, 8]
m2({x: 3, y: 8}) // [3, 8]

// x有值，y无值的情况
m1({x: 3}) // [3, 0]
m2({x: 3}) // [3, undefined]

// x和y都无值的情况
m1({}) // [0, 0];
m2({}) // [undefined, undefined]

m1({z: 3}) // [0, 0]
m2({z: 3}) // [undefined, undefined]
```



#### 函数的length属性

指定了默认值以后，函数的`length`属性，将返回没有指定默认值的参数个数。也就是说，指定了默认值后，`length`属性将失真。

```javascript
(function (a) {}).length // 1
(function (a = 5) {}).length // 0
(function (a, b, c = 5) {}).length // 2
```

#### rest参数

ES6引入rest参数（形式为“...变量名”），用于获取函数的多余参数，这样就不需要使用arguments对象了。rest参数搭配的变量是一个数组，该变量将多余的参数放入数组中。

```javascript
function add(...values) {
  let sum = 0;

  for (var val of values) {
    sum += val;
  }

  return sum;
}

add(2, 5, 3) // 10
```

#### 扩展运算符

##### **含义**

扩展运算符（spread）是三个点（`...`）。它好比rest参数的逆运算，将一个数组转为用逗号分隔的参数序列。

```javascript
console.log(...[1, 2, 3])
// 1 2 3

console.log(1, ...[2, 3, 4], 5)
// 1 2 3 4 5
```

该运算符主要用于函数调用。

```javascript
function push(array, ...items) {
  array.push(...items);
}
```

##### 扩展运算符的应用

**（1）合并数组**

扩展运算符提供了数组合并的新写法。

```javascript
// ES5
[1, 2].concat(more)
// ES6
[1, 2, ...more]
```

**（2）与解构赋值结合**

扩展运算符可以与解构赋值结合起来，用于生成数组。

```javascript
const [first, ...rest] = [1, 2, 3, 4, 5];
first // 1
rest  // [2, 3, 4, 5]
```

如果将扩展运算符用于数组赋值，只能放在参数的最后一位，否则会报错。

```javascript
const [...butLast, last] = [1, 2, 3, 4, 5];
// 报错

const [first, ...middle, last] = [1, 2, 3, 4, 5];
// 报错
```

**（4）字符串**

扩展运算符还可以将字符串转为真正的数组。

```javascript
[...'hello']
// [ "h", "e", "l", "l", "o" ]
```

**（5）实现了Iterator接口的对象**

任何Iterator接口的对象，都可以用扩展运算符转为真正的数组。

```javascript
var nodeList = document.querySelectorAll('div');
var array = [...nodeList];
```

上面代码中，`querySelectorAll`方法返回的是一个`nodeList`对象。它不是数组，而是一个类似数组的对象。这时，扩展运算符可以将其转为真正的数组，原因就在于`NodeList`对象实现了Iterator接口。

对于那些没有部署Iterator接口的类似数组的对象，扩展运算符就无法将其转为真正的数组。

```javascript
let arrayLike = {
  '0': 'a',
  '1': 'b',
  '2': 'c',
  length: 3
};

// TypeError: Cannot spread non-iterable object.
let arr = [...arrayLike];
```

上面代码中，`arrayLike`是一个类似数组的对象，但是没有部署Iterator接口，扩展运算符就会报错。这时，可以改为使用`Array.from`方法将`arrayLike`转为真正的数组。

#### 箭头函数

##### 基本用法

ES6允许使用“箭头”（`=>`）定义函数。

```javascript
var f = v => v;
```

上面的箭头函数等同于：

```javascript
var f = function(v) {
  return v;
};
```

如果箭头函数不需要参数或需要多个参数，就使用一个圆括号代表参数部分。

```javascript
var f = () => 5;
// 等同于
var f = function () { return 5 };
```

如果箭头函数的代码块部分多于一条语句，就要使用大括号将它们括起来，并且使用`return`语句返回。

```javascript
var sum = (num1, num2) => { return num1 + num2; }
```

由于大括号被解释为代码块，所以如果箭头函数直接返回一个对象，必须在对象外面加上括号。

```javascript
var getTempItem = id => ({ id: id, name: "Temp" });
```

##### 注意点

- 函数体内的`this`对象，就是定义时所在的对象，而不是使用时所在的对象。

  ```js
  function foo() {
    setTimeout(() => {
      console.log('id:', this.id);
    }, 100);
  }
  
  var id = 21;
  foo()//id: undefined
  foo.call({ id: 42 });// id: 42
  ```

  上面代码中，`setTimeout`的参数是一个箭头函数，这个箭头函数的定义生效是在`foo`函数生成时，而它的真正执行要等到100毫秒后。如果是普通函数，执行时`this`应该指向全局对象`window`，这时应该输出`21`。但是，箭头函数导致`this`总是指向函数定义生效时所在的对象（本例是`{id: 42}`），所以输出的是`42`。

- 不可以当作构造函数，也就是说，不可以使用`new`命令，否则会抛出一个错误。
- 不可以使用`arguments`对象，该对象在函数体内不存在。如果要用，可以用Rest参数代替。
- 不可以使用`yield`命令，因此箭头函数不能用作Generator函数。

上面四点中，第一点尤其值得注意。`this`对象的指向是可变的，但是在箭头函数中，它是固定的。

## Iterator循环

### 概念

JavaScript表示“**集合**”的数据结构：数组（`Array`）和对象（`Object`），Map和Set

若数组的成员是`Map`，`Map`的成员是对象 ， 这样就需要一种统一的接口机制，来处理所有不同的数据结构。  

遍历器（Iterator）就是这样一种机制。它是一种接口，为各种不同的数据结构**提供统一的访问机制**。 任何数据结构只要部署 Iterator 接口，就可以完成遍历操作 

**Iterator 的遍历过程是这样的。**

（1）创建一个指针对象，指向当前数据结构的起始位置。也就是说，**遍历器对象本质上，就是一个指针对象。**

（2）第一次调用指针对象的`next`方法，可以将指针指向数据结构的第一个成员。

（3）第二次调用指针对象的`next`方法，指针就指向数据结构的第二个成员。

（4）不断调用指针对象的`next`方法，直到它指向数据结构的结束位置。

 每一次调用`next`方法，都会返回数据结构的当前成员的信息。具体来说，就是返回一个包含`value`和`done`两个属性的对象。其中，`value`属性是当前成员的值，`done`属性是一个布尔值，表示遍历是否结束。 

模拟`next`方法返回值的例子。

```javascript
const it = makeIterator(['a', 'b']);

function makeIterator(array) {
  let nextIndex = 0;
  return {
    next: function () {
      //闭包
      console.log(nextIndex); // 0，1，2
      return nextIndex < array.length ?
        { value: array[nextIndex++], done: false } :
        { value: undefined, done: true };
    }
  };
}

console.log(it.next()); // { value: "a", done: false }
console.log(it.next()); // { value: "b", done: false }
console.log(it.next());// { value: undefined, done: true }
```

### 数据结构的默认Iterator接口

Iterator接口的目的，就是为所有数据结构，提供了一种统一的访问机制，即`for...of`循环（详见下文）。当使用`for...of`循环遍历某种数据结构时，该循环会自动去寻找Iterator接口。

**一种数据结构只要部署了Iterator接口，我们就称这种数据结构是”可遍历的“（iterable）。**

ES6规定，**默认的Iterator接口部署在数据结构的`Symbol.iterator`属性**，或者说，一个数据结构只要具有`Symbol.iterator`属性，就可以认为是“可遍历的”（iterable）。

`Symbol.iterator`属性本身是一个函数，就是当前数据结构默认的遍历器生成函数。执行这个函数，就会返回一个遍历器。至于属性名`Symbol.iterator`，它是一个表达式，返回`Symbol`对象的`iterator`属性，这是一个预定义好的、类型为Symbol的特殊值，所以要放在**方括号**内

```
const obj = {
  [Symbol.iterator] : function () {
    return {
      next: function () {
        return {
          value: 1,
          done: true
        };
      }
    };
  }
};
```

**对象（Object）之所以没有默认部署Iterator接口，是因为对象的哪个属性先遍历，哪个属性后遍历是不确定的，需要开发者手动指定。**

一个对象如果要有可被`for...of`循环调用的Iterator接口，就必须在`Symbol.iterator`的属性上部署遍历器生成方法（原型链上的对象具有该方法也可）。

```javascript
class RangeIterator {
  constructor(start, stop) {
    this.value = start;
    this.stop = stop;
  }

  [Symbol.iterator]() { return this; }

  next() {
    var value = this.value;
    if (value < this.stop) {
      this.value++;
      return {done: false, value: value};
    } else {
      return {done: true, value: undefined};
    }
  }
}

function range(start, stop) {
  return new RangeIterator(start, stop);
}

for (var value of range(0, 3)) {
  console.log(value);
}
```

上面代码是一个类部署Iterator接口的写法。`Symbol.iterator`属性对应一个函数，执行后返回当前对象的遍历器对象。

### 调用Iterator接口的场合

#### **解构赋值**

对数组和 Set 结构进行解构赋值时，会默认调用`Symbol.iterator`方法。

#### **扩展运算符**

扩展运算符（...）也会调用默认的 Iterator 接口。

#### **yield**

`yield*`后面跟的是一个可遍历的结构，它会调用该结构的遍历器接口。

```javascript
let generator = function* () {
  yield 1;
  yield* [2,3,4];
  yield 5;
};

var iterator = generator();

iterator.next() // { value: 1, done: false }
```

#### **其他场合**

由于数组的遍历会调用遍历器接口，所以任何接受数组作为参数的场合，其实都调用了遍历器接口。下面是一些例子。

- for...of
- Array.from()
- Map(), Set(), WeakMap(), WeakSet()（比如`new Map([['a',1],['b',2]])`）
- Promise.all()
- Promise.race()

###  for...of 

当使用`for...of`循环遍历某种数据结构时，该循环会自动去寻找 Iterator 接口。 

 默认的 Iterator 接口部署在数据结构的`Symbol.iterator`属性，或者说，一个数据结构只要具有`Symbol.iterator`属性，就可以认为是“可遍历的”（iterable）。**`Symbol.iterator`属性本身是一个函数**，就是当前数据结构默认的遍历器生成函数。  执行这个函数，就会返回一个遍历器对象。

```javascript
const obj = {
  [Symbol.iterator] : function () {
    return {
      next: function () {
        return {
          value: 1,
          done: true
        };
      }
    };
  }
};
```

 对象`obj`是可遍历的（iterable），因为具有`Symbol.iterator`属性。执行这个属性，会返回一个遍历器对象。

 一个对象如果要具备可被`for...of`循环调用的 Iterator 接口，就必须在`Symbol.iterator`的属性上部署遍历器生成方法（原型链上的对象具有该方法也可）。 如果`Symbol.iterator`方法对应的不是遍历器生成函数（即会返回一个遍历器对象），解释引擎将会报错。  

 **原生具备 Iterator 接口的数据结构如下。**

- Array
- Map
- Set
- String
- TypedArray
- 函数的 arguments 对象
- NodeList 对象

```javascript
let arr = ['a', 'b', 'c'];
let iter = arr[Symbol.iterator]();

iter.next() // { value: 'a', done: false }
iter.next() // { value: 'b', done: false }
iter.next() // { value: 'c', done: false }
iter.next() // { value: undefined, done: true }
```

 变量`arr`是一个数组，原生就具有遍历器接口，部署在`arr`的`Symbol.iterator`属性上面。所以，调用这个属性，就得到遍历器对象。 

对于原生部署 Iterator 接口的数据结构，不用自己写遍历器生成函数，`for...of`循环会自动遍历它们。除此之外，其他数据结构（主要是对象）的 Iterator 接口，都需要自己在`Symbol.iterator`属性上面部署，这样才会被`for...of`循环遍历。

### 与其他遍历语法的比较

1. 最原始的写法就是`for`循环。

   ```javascript
   for (var index = 0; index < myArray.length; index++) {
     console.log(myArray[index]);
   }
   ```

2. 数组提供内置的`forEach`方法。

   ```javascript
   myArray.forEach(function (value) {
     console.log(value);
   });
   //这种写法的问题在于，无法中途跳出forEach循环，break命令或return命令都不能奏效。
   ```

3. `for...in`循环可以遍历数组的键名。 

   ```javascript
   for (var index in myArray) {
     console.log(myArray[index]);
   }
   //for...in循环主要是为遍历对象而设计的，不适用于遍历数组。
   ```

4. `for...of`循环相比上面几种做法，有一些显著的优点。

   ```javascript
   for (var n of fibonacci) {
     if (n > 1000)
       break;
     console.log(n);
   }
   //不同于forEach方法，它可以与break、continue和return配合使用。
   ```

## Class

### Class基本语法

```js
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}
var b = new Point(1,2);
```

上面代码定义了一个“类”，可以看到里面有一个`constructor`方法，这就是构造方法，而`this`关键字则代表实例对象。也就是说，ES5的构造函数`Point`，对应ES6的`Point`类的构造方法。

`Point`类除了构造方法，还定义了一个`toString`方法。注意，**定义“类”的方法的时候，前面不需要加上`function`这个关键字**，直接把函数定义放进去了就可以了。另外，**方法之间不需要逗号分隔**，加了会报错。

```javascript
class Point {
  // ...
}
console.log(typeof Point)// "function"
console.log(Point === Point.prototype.constructor); //true
```

上面代码表明，**类的数据类型就是函数，类本身就指向构造函数。**

**构造函数的`prototype`属性，在ES6的“类”上面继续存在**。事实上，类的所有方法都定义在类的`prototype`属性上面。

```javascript
class Point {
  constructor(){
    // ...
  }
  fn(){}
}
// 等同于
Point.prototype = {
  fn(){}
};

console.log(Point.prototype); // constructor，fn
```

![class](https://z3.ax1x.com/2021/01/28/y9AUb9.png)

`b`是B类的实例，它的`constructor`方法就是B类原型的`constructor`方法。

```javascript
class B {}
let b = new B();

b.constructor === B
b.constructor === B.prototype.constructor // true
```

`Object.assign`方法可以很方便地一次向类添加多个方法。

```javascript
class Point {
  constructor(){
    // ...
  }
}

Object.assign(Point.prototype, {
  toString(){},
  toValue(){}
});
```

另外，**类的内部所有定义的方法，都是不可枚举的**（non-enumerable）。

```javascript
class Point {
  constructor(x, y) {
    // ...
  }

  toString() {
    // ...
  }
}

Object.keys(Point.prototype)
// []
Object.getOwnPropertyNames(Point.prototype)
// ["constructor","toString"]
```

这一点与ES5的行为不一致。

```javascript
var Point = function (x, y) {
  // ...
};

Point.prototype.toString = function() {
  // ...
};

Object.keys(Point.prototype)
// ["toString"]
Object.getOwnPropertyNames(Point.prototype)
// ["constructor","toString"]
```

### constructor方法

`constructor`方法是类的默认方法，通过`new`命令生成对象实例时，**自动调用**该方法。一个类必须有`constructor`方法，如果没有显式定义，一个空的`constructor`方法会被默认添加。

**`constructor`方法默认返回实例对象（即`this`），可以指定返回另外一个对象。**

```javascript
class Foo {
  constructor() {
    console.log(this);//Foo {}
    return Object.create(null);
    // return Object.create(this);将Foo赋值给了实例对象的原型对象
  }
}

console.log(new Foo() instanceof Foo);//false
```

上面代码中，`constructor`函数返回一个全新的对象，结果导致实例对象不是`Foo`类的实例。

### prototype和__proto__

大多数浏览器的ES5实现之中，每一个对象都有`__proto__`属性，指向对应的构造函数的prototype属性。Class作为构造函数的语法糖，同时有prototype属性和`__proto__`属性，因此同时存在两条继承链。

（1）子类的`__proto__`属性，表示构造函数的继承，总是指向父类。

（2）子类`prototype`属性的`__proto__`属性，表示方法的继承，总是指向父类的`prototype`属性。

```javascript
class A {
}

class B extends A {
}
let C = new B()
B.__proto__ === A // true
B.prototype.__proto__ === A.prototype // true
C.__proto__ === B.prototype // true
C.__proto__.__proto__===A.prototype // true
```

![class-proto](img/js/class-proto.png)

### this的指向

类的方法内部如果含有`this`，它默认指向类的实例。但是，必须非常小心，一旦单独使用该方法，很可能报错。

```javascript
class Logger {
  printName(name = 'there') {
    this.print(`Hello ${name}`);
  }

  print(text) {
    console.log(text);
  }
}

const logger = new Logger();
const { printName } = logger;
printName(); // TypeError: Cannot read property 'print' of undefined
```

上面代码中，`printName`方法中的`this`，默认指向`Logger`类的实例。但是，如果将这个方法提取出来单独使用，`this`会指向该方法运行时所在的环境，因为找不到`print`方法而导致报错。

一个比较简单的解决方法是，在构造方法中绑定`this`，这样就不会找不到`print`方法了。

```javascript
class Logger {
  constructor() {
    this.printName = this.printName.bind(this);
  }

  // ...
}
```

另一种解决方法是使用箭头函数。

```javascript
class Logger {
  constructor() {
    this.printName = (name = 'there') => {
      this.print(`Hello ${name}`);
    };
  }

  // ...
}
```

还有一种解决方法是使用`Proxy`，获取方法的时候，自动绑定`this`。

```javascript
function selfish (target) {
  const cache = new WeakMap();
  const handler = {
    get (target, key) {
      const value = Reflect.get(target, key);
      if (typeof value !== 'function') {
        return value;
      }
      if (!cache.has(value)) {
        cache.set(value, value.bind(target));
      }
      return cache.get(value);
    }
  };
  const proxy = new Proxy(target, handler);
  return proxy;
}

const logger = selfish(new Logger());
```

### 类的实例对象

类的构造函数，不使用`new`是没法调用的，会报错。这是它跟普通构造函数的一个主要区别，后者不用`new`也可以执行。

```javascript
//定义类
class Point {

  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }

}

var point = new Point(2, 3);
point.toString() // (2, 3)
point.hasOwnProperty('x') // true
point.hasOwnProperty('y') // true
point.hasOwnProperty('toString') // false
point.__proto__.hasOwnProperty('toString') // true
```

上面代码中，`x`和`y`都是实例对象`point`**自身的属性**（因为定义在`this`变量上），所以`hasOwnProperty`方法返回`true`，而`toString`是原型对象的属性（因为定义在`Point`类上），所以`hasOwnProperty`方法返回`false`。这些都与ES5的行为保持一致。

与ES5一样，类的所有实例共享一个原型对象。这也意味着，**可以通过实例的`__proto__`属性为Class添加方法。**

```javascript
var p1 = new Point(2,3);
var p2 = new Point(3,2);
p1.__proto__ === p2.__proto__ //true

p1.__proto__.printName = function () { return 'Oops' };

p1.printName() // "Oops"
p2.printName() // "Oops"

var p3 = new Point(4,2);
p3.printName() // "Oops"
```

### 属性和方法

```
class Foo {
  static a1 = 1
  static classMethod() {
    return 'hello';
  }
}

console.log(Foo.a1, Foo.classMethod()); //1 'hello'

const foo = new Foo();
console.log(foo.a1);//undefined
// TypeError: foo.classMethod is not a function
```

#### 类的实例属性

类的实例属性可以用等式，写入类的定义之中。

```javascript
class MyClass {
  myProp = 42;

  constructor() {
    console.log(this.myProp); // 42
  }
}
```

上面代码中，`myProp`就是`MyClass`的实例属性。在`MyClass`的实例上，可以读取这个属性。

以前，我们定义实例属性，只能写在类的`constructor`方法里面。

```javascript
class ReactCounter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }
}
```

上面代码中，构造方法`constructor`里面，定义了`this.state`属性。有了新的写法以后，可以不在`constructor`方法里面定义。

#### 类的静态属性

静态属性指的是Class本身的属性，即`Class.myStaticProp`，而不是定义在实例对象（`this`）上的属性。

```javascript
class MyClass {
  static myStaticProp = 42;

  constructor() {
    console.log(MyClass.age, MyClass.myStaticProp); // 2 42
  }
}
MyClass.age = 2
new MyClass()
```

#### 类的静态方法

类相当于实例的原型，所有在类中定义的方法，都会被实例继承。如果在一个方法前，加上`static`关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。

```javascript
class Foo {
  static classMethod() {
    return 'hello';
  }
}

console.log(Foo.classMethod()); // 'hello'

var foo = new Foo();
foo.classMethod()
// TypeError: foo.classMethod is not a function
```

上面代码中，`Foo`类的`classMethod`方法前有`static`关键字，表明该方法是一个静态方法，可以直接在`Foo`类上调用（`Foo.classMethod()`），而不是在`Foo`类的实例上调用。如果在实例上调用静态方法，会抛出一个错误，表示不存在该方法。**静态属性指的是Class本身的属性，即`Class.propname`，而不是定义在实例对象（`this`）上的属性。**

父类的静态方法，可以被子类继承。

```javascript
class Foo {
  static classMethod() {
    return 'hello';
  }
}

class Bar extends Foo {
}

Bar.classMethod(); // 'hello'
```

上面代码中，父类`Foo`有一个静态方法，子类`Bar`可以调用这个方法。

静态方法也是可以从`super`对象上调用的。

```javascript
class Foo {
  static classMethod() {
    return 'hello';
  }
}

class Bar extends Foo {
  static classMethod() {
    return super.classMethod() + ', too';
  }
}

Bar.classMethod();
```

#### 私有方法

私有方法是常见需求，但ES6不提供，只能通过变通方法模拟实现。

一种做法是在命名上加以区别。

```javascript
class Widget {

  // 公有方法
  foo (baz) {
    this._bar(baz);
  }

  // 私有方法
  _bar(baz) {
    return this.snaf = baz;
  }

  // ...
}
```

上面代码中，`_bar`方法前面的下划线，表示这是一个只限于内部使用的私有方法。但是，这种命名是不保险的，在类的外部，还是可以调用到这个方法。

另一种方法就是索性将私有方法移出模块，因为模块内部的所有方法都是对外可见的。

```javascript
class Widget {
  foo (baz) {
    bar.call(this, baz);
  }

  // ...
}

function bar(baz) {
  return this.snaf = baz;
}
```

上面代码中，`foo`是公有方法，内部调用了`bar.call(this, baz)`。这使得`bar`实际上成为了当前模块的私有方法。

还有一种方法是利用`Symbol`值的唯一性，将私有方法的名字命名为一个`Symbol`值。

```javascript
const bar = Symbol('bar');
const snaf = Symbol('snaf');

export default class myClass{

  // 公有方法
  foo(baz) {
    this[bar](baz);
  }

  // 私有方法
  [bar](baz) {
    return this[snaf] = baz;
  }

  // ...
};
```

上面代码中，`bar`和`snaf`都是`Symbol`值，导致第三方无法获取到它们，因此达到了私有方法和私有属性的效果。

### Class的继承

Class之间可以通过`extends`关键字实现继承，让子类继承父类的属性和方法。除了私有属性，父类的所有属性和方法，都会被子类继承，其中包括静态方法。这比ES5的通过修改原型链实现继承，要清晰和方便很多。

```javascript
class ColorPoint extends Point {
  constructor(x, y, color) {
    super(x, y); // 调用父类的constructor(x, y)
    this.color = color;
  }

  toString() {
    return this.color + ' ' + super.toString(); // 调用父类的toString()
  }
}
```

上面代码中，`constructor`方法和`toString`方法之中，都出现了`super`关键字，它在这里表示父类的构造函数，用来新建父类的`this`对象。

子类必须在`constructor`方法中调用`super`方法，否则新建实例时会报错。这是因为子类没有自己的`this`对象，而是继承父类的`this`对象，然后对其进行加工。如果不调用`super`方法，子类就得不到`this`对象。

```
class A {
  constructor() {
    console.log(new.target.name);
  }
}
class B extends A {
  constructor() {
    super();
  }
}
new A() // A
new B() // B
```

**ES5的继承，实质是先创造子类的实例对象`this`，然后再将父类的属性和方法添加到`this`上面（`Parent.apply(this)`）。**

**ES6的继承机制完全不同，实质是先初始化父类，创造父类的实例对象`this`（所以必须先调用`super`方法），然后再用子类的构造函数修改`this`。`super()`在这里相当于`A.prototype.constructor.call(this)`。**

```js
class Animal {
  name = 'animal';
  constructor() {
    //执行a,this是指向Animal
    //执行b,super先创建Animal实例对象，this指向Rabbit，但还没赋值name，此时还是animal
    console.log(this) //Animal { name: 'animal' } Rabbit { name: 'animal' }
    console.log(this.name); //animal,animal
  }
}

class Rabbit extends Animal {
  name = 'rabbit';
  constructor() {
    super()
    console.log(this);//Rabbit { name: 'rabbit' }
    console.log(this.name); // rabbit
  }
}
console.log(Rabbit.prototype); //Animal {constructor: ƒ}
let a = new Animal(); // *
let b = new Rabbit(); // *

```

如果子类没有定义`constructor`方法，这个方法会被默认添加，代码如下。也就是说，不管有没有显式定义，任何一个子类都有`constructor`方法。

```javascript
constructor(...args) {
  super(...args);
}
```

### new.target属性

`new`是从构造函数生成实例的命令。ES6为`new`命令引入了一个`new.target`属性，（在构造函数中）**返回`new`命令作用于的那个构造函数。如果构造函数不是通过`new`命令调用的，`new.target`会返回`undefined`**，因此这个属性可以用来确定构造函数是怎么调用的。

```javascript
// 另一种写法
function Person(name) {
  if (new.target === Person) {
    this.name = name;
  } else {
    throw new Error('必须使用new生成实例');
  }
}

var person = new Person('张三'); // 正确
var notAPerson = Person.call(person, '张三');  // 报错
```

上面代码确保构造函数只能通过`new`命令调用。

需要注意的是，子类继承父类时，`new.target`会返回子类。

```javascript
class Rectangle {
  constructor(length, width) {
    console.log(new.target === Rectangle);
    // ...
  }
}

class Square extends Rectangle {
  constructor(length) {
    super(length, length);
  }
}

var obj = new Square(3); // 输出 false
```

### 特性

- **不存在变量提升**
- 类的所有实例共享一个原型对象（_proto_）
- 类本身就指向构造函数
- 类的所有方法都定义在类的`prototype`属性上面
- 类的内部所有定义的方法，都是不可枚举的
- 类没有自身属性，所有方法都定义在类的`prototype`属性上面。而类创造的实例，有自身属性，这是类中constructor赋值给实例的，相当于调用了类中的constructor方法，并返回给实例对象。

## 异步操作

### 背景

**js语言执行环境是单线程**

JavaScript 之所以采用单线程，而不是多线程，跟历史有关系。JavaScript 从诞生起就是单线程，原因是不想让浏览器变得太复杂，因为多线程需要共享资源、且有可能修改彼此的运行结果，对于一种网页脚本语言来说，这就太复杂了。如果 JavaScript 同时有两个线程，一个线程在网页 DOM 节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？

这种模式的好处是实现起来比较简单，执行环境相对单纯；坏处是只要有一个任务耗时很长，后面的任务都必须排队等着，会拖延整个程序的执行。常见的浏览器无响应（假死），往往就是因为某一段 JavaScript 代码长时间运行（比如死循环），导致整个页面卡在这个地方，其他任务无法执行。JavaScript 语言本身并不慢，慢的是读写外部数据，比如等待 Ajax 请求返回结果。这个时候，如果对方服务器迟迟没有响应，或者网络不通畅，就会导致脚本的长时间停滞。

如果排队是因为计算量大，CPU 忙不过来，倒也算了，但是很多时候 CPU 是闲着的，因为 IO 操作（输入输出）很慢（比如 Ajax 操作从网络读取数据），不得不等着结果出来，再往下执行。JavaScript 语言的设计者意识到，这时 CPU 完全可以不管 IO 操作，挂起处于等待中的任务，先运行排在后面的任务。等到 IO 操作返回了结果，再回过头，把挂起的任务继续执行下去。这种机制就是 JavaScript 内部采用的“**事件循环**”机制（Event Loop）

### 回调函数

回调函数就是一个参数，将这个函数作为参数传到另一个函数里面，当主函数执行完之后，再执行传进去的这个函数。这个过程就叫做回调。回调，回调，就是回头调用的意思。

```js
function f1(callback){
	setTimeout(function(){
	callback()//f1的任务
	},1000)
}
f1(f2);
```

但是回调函数有一个致命的弱点，就是容易写出**回调地狱（Callback hell）**。假设多个请求存在依赖性，你可能就会写出如下代码：

```scss
ajax(url, () => {
    // 处理逻辑
    ajax(url1, () => {
        // 处理逻辑
        ajax(url2, () => {
            // 处理逻辑
        })
    })
})
```

回调函数的优点是简单、容易理解和实现，缺点是不利于代码的阅读和维护，各个部分之间高度耦合，使得程序结构混乱、流程难以追踪（尤其是多个回调函数嵌套的情况），而且每个任务只能指定一个回调函数。此外它不能使用 try catch 捕获错误，不能直接 return。

### 事件监听

这种方式下，**异步任务的执行不取决于代码的顺序，而取决于某个事件是否发生**。

监听函数:on,bind,listen,addEventListener

监听方法:onclick...

### 发布/订阅

事件完全可以理解成“信号”，如果存在一个“信号中心”，某个任务执行完成，就向信号中心“发布”（publish）一个信号，其他任务可以向信号中心“订阅”（subscribe）这个信号，从而知道什么时候自己可以开始执行。这就叫做”[发布/订阅模式](https://en.wikipedia.org/wiki/Publish-subscribe_pattern)”（publish-subscribe pattern），又称“[观察者模式](https://en.wikipedia.org/wiki/Observer_pattern)”（observer pattern）。

首先，`f2`向信号中心`jQuery`订阅`done`信号。

```
jQuery.subscribe('done', f2);
```

然后，`f1`进行如下改写。

```
function f1() {
  setTimeout(function () {
    // ...
    jQuery.publish('done');
  }, 1000);
}
```

上面代码中，`jQuery.publish('done')`的意思是，`f1`执行完成后，向信号中心`jQuery`发布`done`信号，从而引发`f2`的执行。

`f2`完成执行后，可以取消订阅（unsubscribe）。

```
jQuery.unsubscribe('done', f2);	
```

这种方法的性质与“事件监听”类似，但是明显优于后者。因为可以通过查看“消息中心”，了解存在多少信号、每个信号有多少订阅者，从而监控程序的运行。

### promise

#### 概念

- **状态**

  一共有三种状态，分别为`pending`（进行中）、`fulfilled`（已成功）和`rejected`（已失败）。

- **特点**
  
- (1)只有异步操作可以决定当前处于的状态，并且任何其他操作无法改变这个状态；
  (2)一旦状态改变，就不会在变。状态改变的过程只可能是：从`pending`变为`fulfilled`和从`pending`变为`rejected`。如果状态发生上述变化后，此时状态就不会在改变了，这时就称为`resolved`（已定型）
  
- **优点：** 可以将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数 

- **缺点：** 无法取消`Promise`  其次，如果没有使用`catch()`方法指定错误处理的回调函数，`Promise`内部抛出的错误，不会反应到外部。 **Promise 内部的错误不会影响到 Promise 外部的代码，通俗的说法就是“Promise 会吃掉错误”。**    

  ```js
  new Promise((resolved, reject) => {
    throw new Error('x 必须为正数');
  }).then((res) => {
    console.log(res);
  }).catch((res) => {
    console.log(res);//'x 必须为正数'
  })
  ```

#### 基本用法

-  **`Promise`对象是一个构造函数，用来生成`Promise`实例。** 

  ```js
  const promise = new Promise(function(resolve, reject) {
    if (/* 异步操作成功 */){
      resolve(value);
    } else {
      reject(error);//在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去
    }
  });
  
  //Promise实例生成以后，可以用then方法分别指定resolved状态和rejected状态的回调函数。第二个函数是可选的，不一定要提供。
  promise.then(function(value) {
    // success
  }, function(error) {
    // failure
  });
  ```

- `Promise`对象实现的 Ajax 操作的例子。

  ```js
  const getJSON = function(url) {
    const promise = new Promise(function(resolve, reject){
      const handler = function() {
        if (this.readyState !== 4) {
          return;
        }
        if (this.status === 200) {
          resolve(this.response);
        } else {
          reject(new Error(this.statusText));
        }
      };
      const client = new XMLHttpRequest();
      client.open("GET", url);
      client.onreadystatechange = handler;
      client.responseType = "json";
      client.setRequestHeader("Accept", "application/json");
      client.send();
    });
    return promise;
  };
  getJSON("/posts.json").then(function(json) {
    console.log('Contents: ' + json);
  }, function(error) {
    console.error('出错了', error);
  });
  ```

- 调用`resolve`函数和`reject`函数时带有参数,参数是另一个Promise对象

  ```js
  const p1 = new Promise(function (resolve, reject) {
    // ...
  });
  
  const p2 = new Promise(function (resolve, reject) {
    // ...
    resolve(p1);
  })
  //`p1`和`p2`都是 Promise 的实例，但是`p2`的`resolve`方法将`p1`作为参数，即一个异步操作的结果是返回另一个异步操作。p1的状态由p2决定
  //注意，这时`p1`的状态就会传递给`p2`，也就是说，`p1`的状态决定了`p2`的状态。如果`p1`的状态是`pending`，那么`p2`的回调函数就会等待`p1`的状态改变；如果`p1`的状态已经是`resolved`或者`rejected`，那么`p2`的回调函数将会立刻执行。
  ```

  ```js
  const p1 = new Promise(function (resolve, reject) {
    setTimeout(() => reject(new Error('fail')), 3000)
  })
  
  const p2 = new Promise(function (resolve, reject) {
    setTimeout(() => resolve(p1), 1000)
  })
  
  p2
    .then(result => console.log('result', result))
    .catch(error => console.log('error', error))
  // error [Error: fail] 
  //上面代码中，`p1`是一个 Promise，3 秒之后变为`rejected`。`p2`的状态在 1 秒之后改变，`resolve`方法返回的是`p1`。由于`p2`返回的是另一个 Promise，导致`p2`自己的状态无效了，由`p1`的状态决定`p2`的状态。所以，后面的`then`语句都变成针对后者（`p1`）。又过了 2 秒，`p1`变为`rejected`，导致触发`catch`方法指定的回调函数。
  ```

#### API

##### then

`Promise.prototype.then()`方法返回的是一个新的`Promise`实例（注意，不是原来那个`Promise`实例）。因此可以采用链式写法，即`then`方法后面再调用另一个`then`方法。 

```javascript
getJSON("/post/1.json").then(function(post) {
  return getJSON(post.commentURL);
}).then(function (comments) {
  console.log("resolved: ", comments);
}, function (err){
  console.log("rejected: ", err);
});
```

上面代码中，第一个`then`方法指定的回调函数，返回的是另一个`Promise`对象。这时，第二个`then`方法指定的回调函数，就会等待这个新的`Promise`对象状态发生变化。如果变为`resolved`，就调用第一个回调函数，如果状态变为`rejected`，就调用第二个回调函数。

##### catch

`Promise.prototype.catch`方法是`.then(null, rejection)`的别名，用于指定发生错误时的回调函数。

```javascript
getJSON('/posts.json').then(function(posts) {
  // ...
}).catch(function(error) {
  // 处理 getJSON 和 前一个回调函数运行时发生的错误
  console.log('发生错误！', error);
});
```

`getJSON()`方法返回一个 Promise 对象，如果该对象状态变为`resolved`，则会调用`then()`方法指定的回调函数；如果异步操作抛出错误，状态就会变为`rejected`，就会调用`catch()`方法指定的回调函数，处理这个错误。另外，**`then()`方法指定的回调函数**，如果运行中抛出错误，也会被`catch()`方法捕获。 

```javascript
var promise = new Promise(function(resolve, reject) {
  throw new Error('test');
});
promise.catch(function(error) {
  console.log(error);// Error: test
});

// 写法二
var promise = new Promise(function(resolve, reject) {
  reject(new Error('test'));
});
promise.catch(function(error) {
  console.log(error);// Error: test
});
```

如果Promise状态已经变成`Resolved`，再抛出错误是无效的。

```javascript
var promise = new Promise(function(resolve, reject) {
  resolve('ok');
  throw new Error('test');
});
promise
  .then(function(value) { console.log(value) })
  .catch(function(error) { console.log(error) });
// ok
```

上面代码中，Promise在`resolve`语句后面，再抛出错误，不会被捕获，等于没有抛出。

总结：一般来说，不要在`then`方法里面定义Reject状态的回调函数（即`then`的第二个参数），总是使用`catch`方法。理由是catch可以捕获前面`then`方法执行中的错误，也更接近同步的写法（`try/catch`）。因此，建议总是使用`catch`方法，而不使用`then`方法的第二个参数。

##### all

`Promise.prototype.all()`方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。

```javascript
const p = Promise.all([p1, p2, p3]);
```

 `Promise.all()`方法接受一个数组作为参数，`p1`、`p2`、`p3`都是 Promise 实例，**如果不是，就会先调用下面讲到的`Promise.resolve`方法，将参数转为 Promise 实例，再进一步处理**。另外，`Promise.all()`方法的参数可以不是数组，但**必须具有 Iterator 接口**，且返回的每个成员都是 Promise 实例。 

`p`的状态由`p1`、`p2`、`p3`决定，分成两种情况。

（1）只有`p1`、`p2`、`p3`的状态都变成`fulfilled`，`p`的状态才会变成`fulfilled`，此时`p1`、`p2`、`p3`的返回值组成一个数组，传递给`p`的回调函数。

（2）只要`p1`、`p2`、`p3`之中有一个被`rejected`，`p`的状态就变成`rejected`，此时第一个被`reject`的实例的返回值，会传递给`p`的回调函数。

##### race

`Promise.race`方法同样是将多个Promise实例，包装成一个新的Promise实例。

```javascript
var p = Promise.race([p1, p2, p3]);
```

上面代码中，只要`p1`、`p2`、`p3`之中有一个实例率先改变状态，`p`的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给`p`的回调函数。

`Promise.race`方法的参数与`Promise.all`方法一样，如果不是 Promise 实例，就会先调用下面讲到的`Promise.resolve`方法，将参数转为 Promise 实例，再进一步处理。

下面是一个例子，**如果指定时间内没有获得结果，就将Promise的状态变为`reject`，否则变为`resolve`。**

```javascript
var p = Promise.race([
  fetch('/resource-that-may-take-a-while'),
  new Promise(function (resolve, reject) {
    setTimeout(() => reject(new Error('request timeout')), 5000)
  })
])
p.then(response => console.log(response))
p.catch(error => console.log(error))
```

上面代码中，如果5秒之内`fetch`方法无法返回结果，变量`p`的状态就会变为`rejected`，从而触发`catch`方法指定的回调函数。

##### finally

 `finally()`方法用于指定不管 Promise 对象最后状态如何，都会执行的操作。 `finally`方法的回调函数不接受任何参数，这意味着没有办法知道，前面的 Promise 状态到底是`fulfilled`还是`rejected`。 

```javascript
promise
.then(result => {···})
.catch(error => {···})
.finally(() => {···});
```

 在执行完`then`或`catch`指定的回调函数以后，都会执行`finally`方法指定的回调函数。  

##### resolve

有时需要将现有对象转为Promise对象，`Promise.resolve`方法就起到这个作用。

**（1）参数是一个Promise实例**

如果参数是Promise实例，那么`Promise.resolve`将不做任何修改、原封不动地返回这个实例。

**（3）参数不是一个Promise实例**

如果参数是一个原始值，或者是一个不具有`then`方法的对象，则`Promise.resolve`方法返回一个新的Promise对象，状态为`Resolved`。

```javascript
var p = Promise.resolve('Hello');

p.then(function (s){
  console.log(s)
});
// Hello
```

**（4）不带有任何参数**

`Promise.resolve`方法允许调用时不带参数，直接返回一个`Resolved`状态的Promise对象。

所以，如果希望得到一个Promise对象，比较方便的方法就是直接调用`Promise.resolve`方法。

```javascript
var p = Promise.resolve();

p.then(function () {
  // ...
});
```

上面代码的变量`p`就是一个Promise对象。

需要注意的是，立即`resolve`的Promise对象，是在本轮“事件循环”（event loop）的结束时，而不是在下一轮“事件循环”的开始时。

```javascript
setTimeout(function () {
  console.log('three');
}, 0);

Promise.resolve().then(function () {
  console.log('two');
});

console.log('one');

// one
// two
// three
```

##### reject 

`Promise.reject(reason)`方法也会返回一个新的Promise实例，该实例的状态为`rejected`。它的参数用法与`Promise.resolve`方法完全一致。

```javascript
var p = Promise.reject('出错了');
// 等同于
var p = new Promise((resolve, reject) => reject('出错了'))

p.then(null, function (s){
  console.log(s)
});
// 出错了
```

上面代码生成一个Promise对象的实例`p`，状态为`rejected`，回调函数会立即执行。

#### 源码

https://zhuanlan.zhihu.com/p/76811638

#### 代码

```js
const a = new Promise((resolve, reject) => {
  reject(1)
})
a.catch((i) => {
  console.log(i)
  return new Promise((resolve, reject) => {
    resolve(2)
  })
}).then((i) => {
  console.log(i)
})
// 1
//返回新的promise，那么下一级.then()会在新的promise状态改变之后执行
```

- 返回新的promise，那么下一级.then()会在新的promise状态改变之后执行
- 没有return，相当于return Promise.resolve(undefined);
- return非Promise的数据data，相当于return Promise.resolve(data);

```js
let a = new Promise((resolve, reject) => {
  console.log(1);
  setTimeout(() => {
    console.log(2);
    resolve(3)
}, 1000);
});
a.then((index) => {
  console.log(index);
});
console.log(a);

1 ​​​​​at ​​​​​​quokka.js:2:3​

Promise { <pending> }
  ​​​​​at ​​​​​​​​a​​​ ​quokka.js:11:1​

2 ​​​​​at ​​​​​​quokka.js:4:5​

3 ​​​​​at ​​​​​​​​index​​​ ​quokka.js:9:3​
```



```js
new Promise((resolved,reject)=>{
  console.log(1);
  resolved(2)
  console.log(3);
  resolved(4)
  console.log(5);
  reject(4)
}).then((res)=>{
  console.log(res);
}).catch((res)=>{
  console.log(res);
})

1 ​​​​​at ​​​​​​quokka.js:2:3​

3 ​​​​​at ​​​​​​quokka.js:4:3​

5 ​​​​​at ​​​​​​quokka.js:6:3​

2 ​​​​​at ​​​​​​​​res​​​ ​quokka.js:9:3​
```



```js
new Promise((resolved, reject) => {
  throw new Error('x 必须为正数');
}).then((res) => {
  console.log(res);
}).catch((res) => {
  console.log(res);
})
[Error: x 必须为正数] ​​​​​at ​​​​​​​​res​​​ ​quokka.js:6:3​
```



### async

#### Generator 

#### async含义

定义：使异步函数以同步函数的形式书写(Generator函数语法糖)

原理：将`Generator函数`和自动执行器`spawn`包装在一个函数里

形式：将`Generator函数`的`*`替换成`async`，将`yield`替换成`await`

`async`函数对 Generator 函数的改进，体现在以下四点。 

（1）内置执行器

Generator 函数的执行必须靠执行器，所以才有了`co`模块，而`async`函数自带执行器。也就是说，`async`函数的执行，与普通函数一模一样，只要一行。 不像 Generator 函数，需要调用`next`方法，或者用`co`模块，才能真正执行 

（2）更好的语义

`async`和`await`，比起星号和`yield`，语义更清楚了。

（3）更广的适用性。

`co`模块约定，`yield`命令后面只能是 Thunk 函数或 Promise 对象，而`async`函数的`await`命令后面，可以是 Promise 对象和原始类型的值（数值、字符串和布尔值，但这时会自动转成立即 resolved 的 Promise 对象）。

（4）**返回值是 Promise**

`async`函数的返回值是 Promise 对象，这比 Generator 函数的返回值是 Iterator 对象方便多了。你可以用`then`方法指定下一步的操作。 `async`函数内部`return`语句返回的值，会成为`then`方法回调函数的参数。 

#### async语法

##### 声明

- 函数：`async function Func() {}`
- 函数表达式：`const func = async function() {}`
- 箭头函数：`const func = async() => {}`
- 对象方法：`const obj = { async func() {} }`
- 类方法：`class Cla { async Func() {} }`

##### 例子

- 没有显式return，相当于return Promise.resolve(undefined);

  ```js
  async function f() {
      let a = 1
      //没有return，就类似于resolved(undefined)
  }
  
  f().then(
      v => console.log(v),//undefined
      e => console.log(e)
  )
  ```

- return非Promise的数据data，相当于return Promise.resolve(data);

  `async`函数内部`return`语句返回的值，会成为`then`方法回调函数的参数。如果在函数中 `return` 一个直接量，async 会把这个直接量通过 `Promise.resolve()` 封装成 Promise 对象。

  ```
  async function f() {
    return 'hello world';
  }
  
  f().then(v => console.log(v))
  // "hello world"
  ```

- `async`函数返回一个Promise对象。

  - return Promise, 会得到Promise对象本身。

    `async`函数返回的Promise对象，必须等到内部所有`await`命令的Promise对象执行完，才会发生状态改变。也就是说，只有`async`函数内部的异步操作执行完，才会执行`then`方法指定的回调函数。

  - 返回rejected

    `async`函数内部抛出错误，会导致返回的Promise对象变为`reject`状态。抛出的错误对象会被`catch`方法回调函数接收到。**只要一个`await`语句后面的Promise变为`reject`，那么整个`async`函数都会中断执行。**

    ```js
    async function f() {
      throw new Error('出错了');
      //await Promise.reject('出错了');
      await Promise.resolve('hello world'); // 不会执行
    }
    
    f().then(
      v => console.log(v),
      e => console.log(e)
    )
    // Error: 出错了
    ```

    为了避免这个问题，可以将第一个`await`放在`try...catch`结构里面，这样第二个`await`就会执行。

#### 错误处理

如果`await`后面的异步操作出错，那么等同于`async`函数返回的 Promise 对象被`reject`。且下面的代码不会被执行

```javascript
async function f() {
  await Promise.reject('出错了');
  await Promise.resolve('hello world'); // 不会执行
}

f()
.then(v => console.log(v))
.catch(e => console.log(e))
//出错了
```

**为了防止状态变为rejected，中断后面的异步操作**，将报错代码放在`try...catch`代码块之中。

```javascript
async function f() {
  try {
    await new Promise(function (resolve, reject) {
      throw new Error('出错了');
    });
  } catch (e) {
  }
  return await ('hello world');
}

f()
  .then(v => console.log(v))
  .catch(v => console.log(v))
// hello world
```

另一种方法是`await`后面的Promise对象再跟一个`catch`方面，处理前面可能出现的错误。

```js
async function f() {
  await Promise.reject('出错了')
    .catch(e => console.log(e));
  return await Promise.resolve('hello world');
}

f()
.then(v => console.log(v))
// 出错了
// hello world
```

#### 优化

- 多个`await`命令后面的异步操作，如果不存在继发关系，最好让它们同时触发。

  ```
  let foo = await getFoo();
  let bar = await getBar();
  ```

  上面代码中，`getFoo`和`getBar`是两个独立的异步操作（即互不依赖），被写成继发关系。这样比较耗时，因为只有`getFoo`完成以后，才会执行`getBar`，完全可以让它们同时触发。

  ```
  // 写法一
  let [foo, bar] = await Promise.all([getFoo(), getBar()]);
  
  // 写法二
  let fooPromise = getFoo();
  let barPromise = getBar();
  let foo = await fooPromise;
  let bar = await barPromise;
  ```

#### 代码

````
## 

```js
//在async中将异步的代码同步执行
var a = async () => {
  console.log(1);
  function fn(){
    setTimeout(()=>{
        console.log(5)
    })
  }
  await fn()
  // await的Promise会同步执行完
  await的 new Promise((resolved) => {
    console.log(2);
    resolved(3);
  }).then((v) => {
    console.log(v);
  });
  console.log(4);
};
a();
```

```
1 ​​​​​at ​quokka.js:2:2​

2 ​​​​​at ​quokka.js:10:4​

3 ​​​​​at ​​​v​​​ ​quokka.js:13:4​

4 ​​​​​at ​quokka.js:16:2​

5 ​​​​​at ​quokka.js:5:8​
```

```js
async function f1(){
  console.log(2);
  await f2()
  console.log(6);
}
async function f2(){
  console.log(3);
}
console.log(1);
setTimeout(() => {
  console.log(8);
}, 0);
f1()
new Promise((resolved)=>{
  console.log(4);
  resolved(7)
}).then((res)=>{
  console.log(res);
})
console.log(5);
```

```
1 ​​​​​at ​​​​​​quokka.js:9:1​

2 ​​​​​at ​​​​​​quokka.js:2:3​

3 ​​​​​at ​​​​​​quokka.js:7:3​

4 ​​​​​at ​​​​​​quokka.js:15:3​

5 ​​​​​at ​​​​​​quokka.js:20:1​

6 ​​​​​at ​​​​​​quokka.js:4:3​

7 ​​​​​at ​​​​​​​​res​​​ ​quokka.js:18:3​

8 ​​​​​at ​​​​​​quokka.js:11:3​
```
````

## Proxy 和 Reflect

https://mp.weixin.qq.com/s/Ez2Cf6w4SwX1HOjnE1wl6g

### Proxy代理

Proxy 用于修改某些操作的默认行为，等同于在语言层面做出修改，所以属于一种“元编程”（meta programming），即对编程语言进行编程。

Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。Proxy 这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“代理器”。

```javascript
var obj = new Proxy({}, {
  get: function (target, key, receiver) {
    console.log(`getting ${key}!`);
    return Reflect.get(target, key, receiver);
  },
  set: function (target, key, value, receiver) {
    console.log(`setting ${key}!`);
    return Reflect.set(target, key, value, receiver);
  }
});
```

上面代码对一个空对象架设了一层拦截，重定义了属性的读取（`get`）和设置（`set`）行为。这里暂时先不解释具体的语法，只看运行结果。对设置了拦截行为的对象`obj`，去读写它的属性，就会得到下面的结果。

```javascript
obj.count = 1
//  setting count!
++obj.count
//  getting count!
//  setting count!
//  2
```

上面代码说明，Proxy 实际上重载（overload）了点运算符，即用自己的定义覆盖了语言的原始定义。

ES6 原生提供 Proxy 构造函数，用来生成 Proxy 实例。

```javascript
var proxy = new Proxy(target, handler);
```

Proxy 对象的所有用法，都是上面这种形式，不同的只是`handler`参数的写法。其中，`new Proxy()`表示生成一个`Proxy`实例，`target`参数表示所要拦截的目标对象，`handler`参数也是一个对象，用来定制拦截行为。

下面是另一个拦截读取属性行为的例子。

```javascript
var proxy = new Proxy({}, {
  get: function(target, property) {
    return 35;
  }
});

proxy.time // 35
proxy.name // 35
proxy.title // 35
```

上面代码中，作为构造函数，`Proxy`接受两个参数。第一个参数是所要代理的目标对象（上例是一个空对象），即如果没有`Proxy`的介入，操作原来要访问的就是这个对象；第二个参数是一个配置对象，对于每一个被代理的操作，需要提供一个对应的处理函数，该函数将拦截对应的操作。比如，上面代码中，配置对象有一个`get`方法，用来拦截对目标对象属性的访问请求。**`get`方法的两个参数分别是目标对象和所要访问的属性**。可以看到，由于拦截函数总是返回`35`，所以访问任何属性都得到`35`。

**如果`handler`没有设置任何拦截，那就等同于直接通向原对象。**

```javascript
var target = {};
var handler = {};
var proxy = new Proxy(target, handler);
proxy.a = 'b';
target.a // "b"
```

Proxy 实例也可以作为其他对象的原型对象。

```javascript
var proxy = new Proxy({}, {
  get: function(target, property) {
    return 35;
  }
});

let obj = Object.create(proxy);
obj.time // 35
```

上面代码中，`proxy`对象是`obj`对象的原型，`obj`对象本身并没有`time`属性，所以根据原型链，会在`proxy`对象上读取该属性，导致被拦截。

### Reflect映射对象

`Reflect`对象与`Proxy`对象一样，也是ES6为了操作对象而提供的新API。

#### 为什么会有Reflect

1） 将`Object`对象的一些明显属于语言内部的方法（比如`Object.defineProperty`），放到`Reflect`对象上。现阶段，某些方法同时在`Object`和`Reflect`对象上部署，未来的新方法将只部署在`Reflect`对象上。

（2） 修改某些Object方法的返回结果，让其变得更合理。比如，`Object.defineProperty(obj, name, desc)`在无法定义属性时，会抛出一个错误，而`Reflect.defineProperty(obj, name, desc)`则会返回`false`。

```javascript
// 老写法
try {
  Object.defineProperty(target, property, attributes);
  // success
} catch (e) {
  // failure
}

// 新写法
if (Reflect.defineProperty(target, property, attributes)) {
  // success
} else {
  // failure
}
```

（3） 让`Object`操作都变成函数行为。某些`Object`操作是命令式，比如`name in obj`和`delete obj[name]`，而`Reflect.has(obj, name)`和`Reflect.deleteProperty(obj, name)`让它们变成了函数行为。

```javascript
// 老写法
'assign' in Object // true

// 新写法
Reflect.has(Object, 'assign') // true
```

（4）`Reflect`对象的方法与`Proxy`对象的方法一一对应，只要是`Proxy`对象的方法，就能在`Reflect`对象上找到对应的方法。这就让`Proxy`对象可以方便地调用对应的`Reflect`方法，完成默认行为，作为修改行为的基础。也就是说，不管`Proxy`怎么修改默认行为，你总可以在`Reflect`上获取默认行为。

```javascript
Proxy(target, {
  set: function(target, name, value, receiver) {
    var success = Reflect.set(target,name, value, receiver);
    if (success) {
      log('property ' + name + ' on ' + target + ' set to ' + value);
    }
    return success;
  }
});
```

上面代码中，`Proxy`方法拦截`target`对象的属性赋值行为。它采用`Reflect.set`方法将值赋值给对象的属性，然后再部署额外的功能。

#### Reflect对象操作Object对象

Reflect对象让我们操作`Object`对象不再是通过点语法了，而是变成了函数行为。

我们看下面的例子，获取对象属性可以使用`Reflect.get`方法、将对象的属性赋值可以使用`Reflect.set`方法。

```js
const obj = {
  name: "_island",
  age: 18
};

// 获取对应属性的值
console.log(obj.name); // _island
console.log(Reflect.get(obj, "name")); // _island

// 对对象的属性赋值操作
obj.name = "abc";
Reflect.set(obj, "name", "abc");
console.log(Reflect.get(obj, "name")); // abc


// 判断一个对象中是否有该属性
console.log("name" in obj); // true
console.log(Reflect.has(obj, "name")); // true

```

### this指向

```js
const obj={
  name:'_island',
  foo:function(){
    return this === objProxy
  }
 }
 
 const objProxy=new Proxy(obj,{})
 console.log(obj.foo()); // false
 console.log(objProxy.foo()); // true
```

`Proxy`对象可以对我们的目标对象进行访问，但没有做任何拦截时，也不能保证与目标对象的行为一致，因为目标对象内部的`this`会自动改变为`Proxy`代理对象。

### Reflect搭配Proxy

`Proxy`对象中的方法也能在`Reflect`对象中调用。

通常我们将`Reflect`对象搭配`Proxy`一起使用，我们看下面这个`Reflect`搭配`Proxy`对象使用的案例。

```js
const obj = {
  name: "_island"
};

const objProxy = new Proxy(obj, {
 // get陷阱中target表示原对象 key表示访问的属性名
  get: function (target, key, receiver) {
     console.log(receiver === proxy);//true
    // 原来的写法
    // return target[key]
    // 使用Reflect
    return Reflect.get(target, key, receiver);
  },
  set: function (target, key, newVal, receiver) {
    // 原来的写法
    // target[key]=newVal
    // 使用Reflect
    Reflect.set(target, key, newVal, receiver);
  },
});

objProxy.name = "abc";
console.log(objProxy.name); // abc

```

在上面，`Proxy`对象中`get`、`set`捕获器多了一个`receiver`参数，这是这两个捕获器特有的，这个`receiver`参数是当前代理的目标。

当`Proxy`和`Reflect`搭配使用时，`Proxy`对象会拦截对应的操作，后者完成对应的操作，如果传入`receiver`，那么`Reflect.get`属性会触发`Proxy.defineProperty`捕获器。我们再上面这里案例上再新增一些代码。

```js
const obj = {
  name: "_island"
};

const objProxy = new Proxy(obj, {
  get: function (target, key, receiver) {
    // 原来的写法
    // return target[key]
    // 使用Reflect
    console.log(receiver);
    return Reflect.get(target, key, receiver);
  },
  set: function (target, key, newVal, receiver) {
    // 原来的写法
    // target[key]=newVal
    // 使用Reflect
    Reflect.set(target, key, newVal, receiver);
  },
  defineProperty: function (target, key, attr) {
    console.log("defineProperty");
    console.log(target, key, attr);
    Reflect.defineProperty(target, key, attr);
  }
});

objProxy.name = "abc";
console.log(objProxy.name); 
// defineProperty
// { name: '_island' } name { value: 'abc' }
// { name: 'abc' }
// abc

```

传入在我们获取代理对象中的`name`属性时，当`Reflect`有`receiver`参数传入时，获取属性值时会获取到`receiver`中的，所以会触发`defineProperty`捕获器，如果没有传入`receiver`参数，则不会触发`defineProperty`捕获器。

### 监听案例

在`ES5`中使用`Object.defineProperty`（对象属性描述符）对对象的监听，将一个对象进行遍历，并设定`getter`、`setter`方法进行监听和拦截。

```javascript
// 定义一个Object对象
const obj = {
  name: "_island",
  age: 18
};

Object.keys(obj).forEach((key) => {
  let val = obj[key];
  Object.defineProperty(obj, key, {
    get: function () {
      console.log(key + "调用了get方法");
      return val;
    },
    set: function (newVal) {
      console.log(key + "调用了set方法");
      val = newVal;
    }
  });
});

// 操作obj对象
obj.name = "QC2125";
// name调用了set方法
obj.age = 30;
// age调用了set方法
console.log(obj.name); 
// name调用了get方法
// QC2125
```

`Object.defineProperty`的设计初衷并不是为了去监听拦截一个对象中的属性，且他也实现不了更加丰富的操作，例如添加、删除属性等操作。

我们将上面通过`Object.defineProperty`实现对象监听的方法修改成`Proxy`方案。在Vue3框架中的响应式原理也是用到了Proxy对象进行对属性的监听操作。

```javascript
const obj = {
  name: "_island",
  age: 18
};

const objProxy = new Proxy(obj, {
  // 获取值时的捕获器
  get: function (target, key) {
    console.log(`监听到了${key}被获取值`);
    return target[key];
  },
  // 设置值时的捕获器
  set: function (target, key, newValue) {
    console.log(`监听到了${key}被设置值`);
    target[key] = newValue;
  }
});

console.log(objProxy.name);
// 监听到了name被获取值
// _island
console.log(objProxy.age);
// 监听到了age被获取值
// 18
objProxy.name = "QC2125";
// 监听到了name被设置值
console.log(objProxy.name);
// 监听到了name被获取值
// QC2125
```

### 方法

`Reflect`对象的方法与`Proxy`对象的方法一一对应，只要是`Proxy`对象的方法，就能在`Reflect`对象上找到对应的方法

| 对象中的方法                       | 说明                      |
| ---------------------------------- | ------------------------- |
| Reflect.apply()                    | 对一个函数进行`apply`调用 |
| Reflect.construct()                | 对构造函数进行`new`操作   |
| Reflect.defineProperty()           | 定义一个属性              |
| Reflect.deleteProperty()           | 删除一个属性              |
| Reflect.get()                      | 获取一个属性              |
| Reflect.getOwnPropertyDescriptor() | 获取一个属性描述符        |
| Reflect.getPrototypeOf()           | 获取一个对象的原型        |
| Reflect.has()                      | 判断一个属性是否在对象中  |
| Reflect.isExtensible()             | 判断可以扩展              |
| Reflect.ownKeys()                  | 获取一个对象中的`key`集合 |
| Reflect.preventExtensions()        | 使一个对象不可扩展        |
| Reflect.set()                      | 设置一个属性              |
| Reflect.setPrototypeOf()           | 设置一个对象的原型        |

# TS

TypeScript 是一门基于 JavaScript 拓展的语言，它是 JavaScript 的超集，并且给 JavaScript 添加了静态类型检查系统。TypeScript 能让我们在开发时发现程序中类型定义不一致的地方，及时消除隐藏的风险，大大增强了代码的可读性以及可维护性。

## TS和JS

### 编译

具体可在中查看[常识文档](./02-常识.md)

### 类型绑定

**JavaScript**

JavaScript 是一门解释型语言，没有编译阶段，所以它是动态类型。`JavaScript`**动态**绑定类型，只有运行程序才能知道类型，在程序运行之前`JavaScript`对类型一无所知

**TypeScript**

`TypeScript`是在程序运行前（也就是编译时）就会知道当前是什么类型。当然如果该变量没有定义类型，那么`TypeScript`会自动类型推导出来。

### 类型转换

**JavaScript弱类型语言**

比如在`JavaScript`中`1 + true`这样一个代码片段，`JavaScript`存在隐式转换，这时`true`会变成`number`类型`number(true)`和1相加。

**TypeScript强类型语言**

在`TypeScript`中，`1+true`这样的代码会在`TypeScript`中报错，提示`number`类型不能和`boolean`类型进行运算。

### 何时检查类型

**JavaScript**

在`JavaScript`中只有在程序运行时才能检查类型。类型也会存在隐式转换，很坑。

**TypeScript**

在`TypeScript`中，在编译时就会检查类型，如果和预期的类型不符合直接会在编辑器里报错、爆红

## TS特殊符号

https://blog.csdn.net/qiwoo_weekly/article/details/108557466

### ?. 运算符

可选链（Optional Chaining）运算符是一种先检查属性是否存在，再尝试访问该属性的运算符

```go
a?.b;
// 相当于 a == null ? undefined : a.b;
// 如果 a 是 null/undefined，那么返回 undefined，否则返回 a.b 的值.

a?.[x];
// 相当于 a == null ? undefined : a[x];
// 如果 a 是 null/undefined，那么返回 undefined，否则返回 a[x] 的值

a?.b();
// 相当于a == null ? undefined : a.b();
// 如果 a 是 null/undefined，那么返回 undefined
// 如果 a.b 不是函数的话，会抛类型错误异常，否则计算 a.b() 的结果
```

### ?:

在 TypeScript 中使用 `interface` 关键字就可以声明一个接口：

```go
interface Person {
  name: string;
  age: number;
}
let semlinker: Person = {
  name: "semlinker",
  age: 33,
};
```

在以上代码中，我们声明了 `Person` 接口，它包含了两个必填的属性 `name` 和 `age`。在初始化 Person 类型变量时，如果缺少某个属性，TypeScript 编译器就会提示相应的错误信息，比如：

```go
// Property 'age' is missing in type '{ name: string; }' but required in type 'Person'.(2741)
let lolo: Person  = { // Error
  name: "lolo"  
}
```

为了解决上述的问题，我们可以把某个属性声明为可选的：

```go
interface Person {
  name: string;
  age?: number;
}

let lolo: Person  = {
  name: "lolo"  
}
```

### !:



### | 分隔符

在 TypeScript 中联合类型（Union Types）表示取值可以为多种类型中的一种，联合类型使用 `|` 分隔每个类型。联合类型通常与 `null` 或 `undefined` 一起使用：

```go
const sayHello = (name: string | undefined) => { /* ... */ };
```

以上示例中 `name` 的类型是 `string | undefined` 意味着可以将 `string` 或 `undefined` 的值传递给 `sayHello` 函数。

### type

定义类型由两种方式：接口（interface）和类型别名（type alias）

interface只能定义对象类型，type声明的方式可以定义组合类型，交叉类型和原始类型

## 基础类型

### 布尔值

最基本的数据类型就是简单的true/false值，在JavaScript和TypeScript里叫做`boolean`（其它语言中也一样）。

```ts
let isDone: boolean = false;
```

### 数字

和JavaScript一样，TypeScript里的所有数字都是浮点数。 这些浮点数的类型是 `number`。 除了支持十进制和十六进制字面量，TypeScript还支持ECMAScript 2015中引入的二进制和八进制字面量。

```ts
let decLiteral: number = 6;// 十进制
let hexLiteral: number = 0xf00d; // 十六进制
let binaryLiteral: number = 0b1010;// 二进制
let octalLiteral: number = 0o744;  // 八进制
```

### 字符串

```ts
let name: string = "bob";
name = "smith";
```

你还可以使用*模版字符串*，它可以定义多行文本和内嵌表达式。 这种字符串是被反引号包围（ ```），并且以`${ expr }`这种形式嵌入表达式

```ts
let name: string = `Gene`;
let age: number = 37;
let sentence: string = `Hello, my name is ${ name }.I'll be ${ age + 1 } years old next month.`;
```

这与下面定义`sentence`的方式效果相同：

```ts
let sentence: string = "Hello, my name is " + name + ".\n\n" +"I'll be " + (age + 1) + " years old next month.";
```

### Null 和 Undefined

**null**

null是一个只有一个值的特殊类型。表示一个空对象引用。在 JavaScript 中 null 表示 "什么都没有"。用 typeof 检测 null 返回是 object。

**undefined**

在 JavaScript 中, undefined 是一个没有设置值的变量。typeof 一个没有值的变量会返回 undefined。

与 `void` 的区别是，`undefined` 和 `null` 是所有类型的子类型。也就是说 `undefined` 类型的变量，可以赋值给 `number` 类型的变量：

```ts
// 这样不会报错
let num: number = undefined;
// 这样也不会报错
let u: undefined;
let num: number = u;
```

而 `void` 类型的变量不能赋值给 `number` 类型的变量：

```ts
let u: void;
let num: number = u;

// Type 'void' is not assignable to type 'number'.
```

### 数组

TypeScript像JavaScript一样可以操作数组元素。 有两种方式可以定义数组。 第一种，可以在**元素类型**后面接上 `[]`，表示由此类型元素组成的一个数组：

```ts
let list: number[] = [1, 2, 3];
```

第二种方式是使用**数组泛型**，`Array<元素类型>`：

```ts
let list: Array<number> = [1, 2, 3];
```

### 元组 Tuple

**元组类型允许表示一个已知元素数量和类型的数组**，各元素的类型不必相同。 比如，你可以定义一对值分别为 `string`和`number`类型的元组。

```ts
// Declare a tuple type
let x: [string, number];
// Initialize it
x = ['hello', 10]; // OK
// Initialize it incorrectly
x = [10, 'hello']; // Error
```

当访问一个已知索引的元素，会得到正确的类型：

```ts
console.log(x[0].substr(1)); // OK
console.log(x[1].substr(1)); // Error, 'number' does not have 'substr'
```

当访问一个越界的元素，会使用**联合类型**替代：

```ts
x[3] = 'world'; // OK, 字符串可以赋值给(string | number)类型

console.log(x[5].toString()); // OK, 'string' 和 'number' 都有 toString

x[6] = true; // Error, 布尔不是(string | number)类型
```

### Object

`object`表示非原始类型，也就是除`number`，`string`，`boolean`，`symbol`，`null`或`undefined`之外的类型。

使用`object`类型，就可以更好的表示像`Object.create`这样的API。例如：

```ts
declare function create(o: object | null): void;

create({ prop: 0 }); // OK
create(null); // OK

create(42); // Error
create("string"); // Error
create(false); // Error
create(undefined); // Error
```

### Any

有时候，**我们会想要为那些在编程阶段还不清楚类型的变量指定一个类型。 这些值可能来自于动态的内容，比如来自用户输入或第三方代码库**。 这种情况下，我们不希望类型检查器对这些值进行检查而是直接让它们通过编译阶段的检查。 那么我们可以使用 `any`类型来标记这些变量：

```ts
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean
```

在对现有代码进行改写的时候，`any`类型是十分有用的，它允许你在**编译时**可选择地包含或移除类型检查。 

```ts
let notSure: any = 4;
notSure.ifItExists(); // okay, ifItExists might exist at runtime
notSure.toFixed(); // okay, toFixed exists (but the compiler doesn't check)

let prettySure: Object = 4;
prettySure.toFixed(); // Error: Property 'toFixed' doesn't exist on type 'Object'.
```

当你只知道一部分数据的类型时，`any`类型也是有用的。 比如，你有一个数组，它包含了不同的类型的数据：

```ts
let list: any[] = [1, true, "free"];

list[1] = 100;
```

**变量如果在声明的时候，未指定其类型，那么它会被识别为任意值类型**：

```ts
let something;
something = 'seven';
something = 7;

something.setName('Tom');
```

等价于

```ts
let something: any;
something = 'seven';
something = 7;

something.setName('Tom');
```

### Void空值

某种程度上来说，`void`类型像是与`any`类型相反，它表示没有任何类型。 当一个函数没有返回值时，你通常会见到其返回值类型是 `void`：

```ts
function warnUser(): void {
    console.log("This is my warning message");
}
```

声明一个`void`类型的变量没有什么大用，因为你只能为它赋予`undefined`和`null`：

```ts
let unusable: void = undefined;
```

### Never

never 是其它类型（包括 null 和 undefined）的子类型，代表那些永不存在的值的类型。这意味着声明为 never 类型的变量只能被 never 类型所赋值。 例如， `never`类型是那些总是会抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型；

```ts
let x: never;
let y: number;

// 运行错误，数字类型不能转为 never 类型
x = 123;
y = 123;

// 运行正确，never 类型可以赋值给 never类型
x = (()=>{ throw new Error('exception')})();

// 运行正确，never 类型可以赋值给 数字类型
y = (()=>{ throw new Error('exception')})();

// 返回值为 never 的函数可以是抛出异常的情况
function error(message: string): never {
    throw new Error(message);
}

// 返回值为 never 的函数可以是无法被执行到的终止点的情况
function loop(): never {
    while (true) {}
}
```

### 类型断言

类型断言（Type Assertion）可以用来手动指定一个值的类型。

通过*类型断言*这种方式可以告诉编译器，“相信我，我知道自己在干什么”。 它没有运行时的影响，只是在编译阶段起作用。 **类型断言会假设程序员，已经进行了必须的检查，让TypeScript跳过类型检测。**

```
语法：<类型>值 或 值 as 类型
在 tsx 语法（React 的 jsx 语法的 ts 版）中必须用后一种
```

类型断言有两种形式。 其一是“尖括号”语法：

```ts
let someValue: any = "this is a string";

let strLength: number = (<string>someValue).length;
```

另一个为`as`语法：

```ts
let someValue: any = "this is a string";

let strLength: number = (someValue as string).length;
```

- 例子一

  ```tsx
  function func(val: string | number): number {
    if (val.length) {
      return val.length
    } else {
      return val.toString().length
    }
  }
  ```

  函数的参数 val 是一个联合类型，在这里的意思是说 val 可以是字符串类型也可以是数值类型。代码中要返回参数的长度，但是 length 是字符串的属性，而数值是没有这个属性的，所以当 val 是数值时，就先用 toSting() 来将数字转换为字符串再取长度。这样的逻辑本身没问题，但是在编译阶段一访问 val.length 时就报错了，因为 **访问联合类型值的属性时，这个属性必须是所有可能类型的共有属性，**而length不是共有属性，val 的类型此时也没确定，所以编译不通过。为了通过编译，此时就可以使用类型断言了

  ```tsx
  function func(val: string | number): number {
    if ((<string>val).length) {
      return (<string>val).length
    } else {
      return val.toString().length
    }
  }
  ```

- 例子二

  ```tsx
  const foo = {};
  foo.bar = 123; // Error: 'bar' 属性不存在于 ‘{}’
  foo.bas = 'hello'; // Error: 'bas' 属性不存在于 '{}'
  
  interface Foo {
    bar: number;
    bas: string;
  }
  const foo = {} as Foo;
  foo.bar = 123;
  foo.bas = 'hello';
  ```

#### const 断言

TypeScript 3.4 引入了一种新的字面量构造方式，也称为 const 断言。当我们使用 const 断言构造新的字面量表达式时，我们可以向编程语言发出以下信号

- 表达式中的任何字面量类型都不应该被扩展；
- **对象字面量的属性**，将使用 `readonly` 修饰；
- **数组字面量**将变成 `readonly` 元组。

下面我们来举一个 const 断言的例子：

```ts
let x = "hello" as const;
type X = typeof x; // type X = "hello"
let y:X='hello';

let y = [10, 20] as const;
type Y = typeof y; // type Y = readonly [10, 20]

let z = { text: "hello" } as const;
type Z = typeof z; // let z: { readonly text: "hello"; }
```

#### 类型断言的用途

- 将一个联合类型断言为其中一个类型
- 将一个父类断言为更加具体的子类
- 将任何一个类型断言为 `any`
- 将 `any` 断言为一个具体的类型

### 类型推论

如果没有明确的指定类型，那么 TypeScript 会依照类型推论（Type Inference）的规则推断出一个类型。

以下代码虽然没有指定类型，但是会在编译的时候报错：

```ts
let myFavoriteNumber = 'seven';
myFavoriteNumber = 7;

// index.ts(2,1): error TS2322: Type 'number' is not assignable to type 'string'.
```

事实上，它等价于：

```ts
let myFavoriteNumber: string = 'seven';
myFavoriteNumber = 7;

// index.ts(2,1): error TS2322: Type 'number' is not assignable to type 'string'.
```

TypeScript 会在没有明确的指定类型的时候推测出一个类型，这就是类型推论。

**如果定义的时候没有赋值，不管之后有没有赋值，都会被推断成 `any` 类型而完全不被类型检查**：

```ts
let myFavoriteNumber;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
```

## 枚举

### 是什么

枚举是一个被命名的整型常数的集合，用于声明一组命名的常数,当一个变量有几种可能的取值时,可以将它定义为枚举类型。

通俗来说，枚举就是一个对象的所有可能取值的集合。

在日常生活中也很常见，例如表示星期的SUNDAY、MONDAY、TUESDAY、WEDNESDAY、THURSDAY、FRIDAY、SATURDAY就可以看成是一个枚举。

枚举的说明与结构和联合相似，其形式为：

```tsx
enum 枚举名{
    标识符①[=整型常数],
    标识符②[=整型常数],
    ...
    标识符N[=整型常数],
}枚举变量;
```

### 使用

枚举的使用是通过`enum`关键字进行定义，形式如下：

```tsx
enum xxx { ... }
```

声明关键字为枚举类型的方式如下：

```tsx
// 声明d为枚举类型Direction
let d: Direction;
```

类型可以分成：

- 数字枚举
- 字符串枚举
- 异构枚举

#### 数字枚举

当我们声明一个枚举类型是,虽然没有给它们赋值,但是它们的值其实是默认的数字类型,而且**默认从0开始依次累加:**

```tsx
enum Direction {
    Up,   // 值默认为 0
    Down, // 值默认为 1
    Left, // 值默认为 2
    Right // 值默认为 3
}

console.log(Direction.Up === 0); // true
console.log(Direction.Down === 1); // true
console.log(Direction.Left === 2); // true
console.log(Direction.Right === 3); // true
```

**如果我们将第一个值进行赋值后，后面的值也会根据前一个值进行累加1：**

```tsx
enum Direction {
    Up = 10,
    Down,
    Left,
    Right
}

console.log(Direction.Up, Direction.Down, Direction.Left, Direction.Right); // 10 11 12 13
```

#### 字符串枚举

```tsx
枚举类型的值其实也可以是字符串类型：

enum Direction {
    Up = 'Up',
    Down = 'Down',
    Left = 'Left',
    Right = 'Right'
}

console.log(Direction['Right'], Direction.Up); // Right Up
```

如果设定了一个变量为字符串之后，后续的字段也需要赋值字符串，否则报错：

```tsx
enum Direction {
 Up = 'UP',
 Down, // error TS1061: Enum member must have initializer
 Left, // error TS1061: Enum member must have initializer
 Right // error TS1061: Enum member must have initializer
}
```

#### 异构枚举

即将数字枚举和字符串枚举结合起来混合起来使用，如下：

```tsx
enum BooleanLikeHeterogeneousEnum {
    No = 0,
    Yes = "YES",
}
```

通常情况下我们很少会使用异构枚举

#### 本质

现在一个枚举的案例如下：

```tsx
enum Direction {
    Up,
    Down,
    Left,
    Right
}
```

通过编译后，`javascript`如下：

```tsx
var Direction;
(function (Direction) {
    Direction[Direction["Up"] = 0] = "Up";
    Direction[Direction["Down"] = 1] = "Down";
    Direction[Direction["Left"] = 2] = "Left";
    Direction[Direction["Right"] = 3] = "Right";
})(Direction || (Direction = {}));
```

上述代码可以看到， `Direction[Direction["Up"] = 0] = "Up"`可以分成

- Direction["Up"] = 0
- Direction[0] = "Up"

所以定义枚举类型后，可以通过正反映射拿到对应的值，如下：

```tsx
enum Direction {
    Up,
    Down,
    Left,
    Right
}

console.log(Direction.Up === 0); // true
console.log(Direction[0]); // Up
```

**并且多处定义的枚举是可以进行合并操作**，如下：

```tsx
enum Direction {
    Up = 'Up',
    Down = 'Down',
    Left = 'Left',
    Right = 'Right'
}

enum Direction {
    Center = 1
}
```

编译后，`js`代码如下：

```tsx
var Direction;
(function (Direction) {
    Direction["Up"] = "Up";
    Direction["Down"] = "Down";
    Direction["Left"] = "Left";
    Direction["Right"] = "Right";
})(Direction || (Direction = {}));
(function (Direction) {
    Direction[Direction["Center"] = 1] = "Center";
})(Direction || (Direction = {}));
```

可以看到，`Direction`对象属性回叠加

```tsx
{ 
  1: 'Center',
  Up: 'Up',
  Down: 'Down',
  Left: 'Left',
  Right: 'Right',
  Center: 1 
}
```

### 应用场景

就拿回生活的例子，后端返回的字段使用 0 - 6 标记对应的日期，这时候就可以使用枚举可提高代码可读性，如下：

```tsx
enum Days {Sun, Mon, Tue, Wed, Thu, Fri, Sat};

console.log(Days["Sun"] === 0); // true
console.log(Days["Mon"] === 1); // true
console.log(Days["Tue"] === 2); // true
console.log(Days["Sat"] === 6); // true
```

包括后端日常返回0、1 等等状态的时候，我们都可以通过枚举去定义，这样可以提高代码的可读性，便于后续的维护

```tsx
/**
 * 投放方式
 * 0: 无对接投放
 * 1: 直接投放
 * 2: 事件触发投放
 */
export enum PUT_WAY {
  NO_DOCKING = 0,
  DIRECT = 1,
  EVENT = 2
}

export const PUT_WAY_LABEL = {
  [`${PUT_WAY.NO_DOCKING}`]: '无对接投放',
  [`${PUT_WAY.DIRECT}`]: '直接投放',
  [`${PUT_WAY.EVENT}`]: '事件触发投放'
}
```

## 接口

**作用：**

- 对“对象”进行约束描述
- 对“类”的一部分行为进行抽象

**特点：**

- 接口与值的结构必须相同

  ```tsx
  interface LabelledValue {
      size: number;
      label: string;
  }
  function printLabel(labelledObj: LabelledValue) {
      console.log(labelledObj.label);
  }
  let myObj1 = { label: "Size 10 Object" };
  let myObj2 = { label: "Size 10 Object", other:1 };
  //少了
  printLabel(myObj1);
  //多了
  printLabel(myObj2);
  ```

- 可以捕获引用了不存在的属性时的错误

  ```tsx
  interface LabelledValue {
      size: number;
      label: string;
  }
  function printLabel(labelledObj: LabelledValue) {
      console.log(labelledObj.labell);
    }
  let myObj = { size:10, label: "Size 10 Object" };
  printLabel(myObj);
  ```

### 可选属性

接口里的属性不全都是必需的。 有些是只在某些条件下存在，或者根本不存在。 可选属性在应用“option bags”模式时很常用，即给函数传入的参数对象中只有部分属性赋值了。

下面是应用了“option bags”的例子：

```ts
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
  let newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.color;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

let mySquare = createSquare({color: "black"});
```

带有可选属性的接口与普通的接口定义差不多，只是在可选属性名字定义的后面加一个`?`符号。

### 只读属性

一些对象属性只能在对象刚刚创建的时候修改其值。 你可以在属性名前用 `readonly`来指定只读属性:

```ts
interface Point {
    readonly x: number;
    readonly y: number;
}
```

你可以通过赋值一个对象字面量来构造一个`Point`。 赋值后， `x`和`y`再也不能被改变了。

```ts
let p1: Point = { x: 10, y: 20 };
p1.x = 5; // error!
```

TypeScript具有`ReadonlyArray<T>`类型，它与`Array<T>`相似，只是把所有可变方法去掉了，因此可以确保数组创建后再也不能被修改：

```ts
let a: number[] = [1, 2, 3, 4];
let ro: ReadonlyArray<number> = a;
ro[0] = 12; // 类型“readonly number[]”中的索引签名仅允许读取
ro.push(5); // 类型“readonly number[]”上不存在属性“push”
ro.length = 100; // 无法分配到 "length" ，因为它是只读属性
a = ro; // 类型 "readonly number[]" 为 "readonly"，不能分配给可变类型 "number[]"
```

上面代码的最后一行，可以看到就算把整个`ReadonlyArray`赋值到一个普通数组也是不可以的。 但是你可以用类型断言重写：

```ts
a = ro as number[];
```

**`readonly` vs `const`**

最简单判断该用`readonly`还是`const`的方法是看要把它做为变量使用还是做为一个属性。 做为变量使用的话用 `const`，若做为属性则使用`readonly`。

### 额外的属性检查

```ts
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): { color: string; width: number } {
  return {color: '1', width: 1}
}

let mySquare = createSquare({ colour: "red", width: 100 });
```

注意传入`createSquare`的参数拼写为*`colour`*而不是`color`。 在JavaScript里，这会默默地失败。 **编译器能够捕获引用了未声明属性的错误**

```ts
// error: 'colour' not expected in type 'SquareConfig'
let mySquare = createSquare({ colour: "red", width: 100 });
```

**解决**

- 最简便的方法是使用**类型断言**：

```ts
let mySquare = createSquare({ width: 100, opacity: 0.5 } as SquareConfig);
```

- 加上额外属性声明，允许对象引用除可选属性以外的其他属性

```ts
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;//加上额外属性声明，允许对象引用除可选属性以外的其他属性
}
```

### 函数类型

为了使用接口表示函数类型，我们需要给接口定义一个调用签名。 具体的格式是一个只有**参数列表**和**返回值类型**的函数定义。**参数列表**里的每个参数都需要名字和类型。

```ts
interface Func {
  (param1: string, param2: number): boolean;
}
```

这样定义后，我们可以像使用其它接口一样使用这个函数类型的接口。 下例展示了如何**创建一个函数类型的变量，并将一个同类型的函数赋值给这个变量。**

```ts
const myFunc: Func = function(param1, param2){
  return typeof param1 === "string" && typeof param2 === "number";
};
```

**对于函数类型的类型检查来说，函数的参数名不需要与接口里定义的名字相匹配。** 比如，我们使用下面的代码重写上面的例子：

```ts
interface Func {
  (param1: string, param2: number): boolean;
}

const myFunc: Func = function(param2, param3){
  console.log(param2, param3);// 1，2
  return typeof param2 === "string" && typeof param3 === "number";
};

myFunc('1',2)
```

### 可索引的类型

与使用接口描述函数类型差不多，我们也可以描述那些能够“通过索引得到”的类型，比如`a[10]`或`ageMap["daniel"]`。 可索引类型具有一个 *索引签名*，它描述了对象索引的类型，还有相应的索引返回值类型。 

```ts
// 数字索引——约束数组
// index 是随便取的名字，可以任意取名
// 只要 index 的类型是 number，那么值的类型必须是 string
interface StringArray {
  // key 的类型为 number ，一般都代表是数组
  // 限制 value 的类型为 string
  [index:number]:string
}
let arr:StringArray = ['aaa','bbb'];
console.log(arr);


// 字符串索引——约束对象
// 只要 index 的类型是 string，那么值的类型必须是 string
interface StringObject {
  // key 的类型为 string ，一般都代表是对象
  // 限制 value 的类型为 string
  [index:string]:string
}
let obj:StringObject = {name:'ccc'};

```

字符串索引签名能够很好的描述`dictionary`模式，并且它们也会确保所有属性与其返回值类型相匹配。 因为字符串索引声明了 `obj.property`和`obj["property"]`两种形式都可以。 下面的例子里， `name`的类型与字符串索引类型不匹配，所以类型检查器给出一个错误提示：

```ts
interface NumberDictionary {
  [index: string]: number;
  length: number;    // 可以，length是number类型
  name: string       // 错误，`name`的类型与索引类型返回值的类型不匹配
}
```

最后，你可以将索引签名设置为只读，这样就防止了给索引赋值：

```ts
interface ReadonlyStringArray {
    readonly [index: number]: string;
}
let myArray: ReadonlyStringArray = ["Alice", "Bob"];
myArray[2] = "Mallory"; // error!
```

你不能设置`myArray[2]`，因为索引签名是只读的。

**Element implicitly has an ‘any‘ type because expression of type ‘string‘ can‘t be used to index type**
英文报错：Element implicitly has an ‘any’ type because expression of type ‘string’ can’t be used to index type

中文报错：元素隐式具有 “any” 类型，因为类型为 “string” 的表达式不能用于索引类型 “UserInfo”。

```
interface UserInfo {
  name: string;
  age: number;
  address: string;
}
```

```
let userInfo: UserInfo = {
  name: "刘德华",
  age: 18,
  address: "吉林市长春市九台区其塔木镇",
};
let coontent = userInfo[v.value]
解决方法：
let content = userInfo[v.value as keyof typeof userInfo]
原因分析：

根据英文分析，是断言 v.value 的值是对象 userInfo 的 key
```

### 类类型

接口描述了类的公共部分，而不是公共和私有两部分。 它不会帮你检查类是否具有某些私有成员。

#### 类实现接口

实现（implements）是面向对象中的一个重要概念。一般来讲，一个类只能继承自另一个类，有时候**不同类之间可以有一些共有的特性**，这时候就可以把特性提取成接口（interfaces），用 `implements` 关键字来实现。这个特性大大提高了面向对象的灵活性。

举例来说，门是一个类，防盗门是门的子类。如果防盗门有一个报警器的功能，我们可以简单的给防盗门添加一个报警方法。这时候如果有另一个类，车，也有报警器的功能，就可以考虑把报警器提取出来，作为一个接口，防盗门和车都去实现它：

```ts
interface Alarm {
    alert(): void;
}

class Door {
}

class SecurityDoor extends Door implements Alarm {
    alert() {
        console.log('SecurityDoor alert');
    }
}

class Car implements Alarm {
    alert() {
        console.log('Car alert');
    }
}
let a = new SecurityDoor()
a.alert()
let b = new Car()
b.alert()
```

一个类可以实现多个接口，下例中，`Car` 实现了 `Alarm` 和 `Light` 接口，既能报警，也能开关车灯：

```ts
interface Alarm {
    alert();
}

interface Light {
    lightOn();
    lightOff();
}

class Car implements Alarm, Light {
    alert() {
        console.log('Car alert');
    }
    lightOn() {
        console.log('Car light on');
    }
    lightOff() {
        console.log('Car light off');
    }
}
```

#### 接口继承接口

接口可以继承接口，下例中，我们使用 `extends` 使 `LightableAlarm` 继承 `Alarm：`

```ts
interface Alarm {
  alert();
}

interface LightableAlarm extends Alarm {
  lightOn()
  lightOff()
}

class A implements LightableAlarm {
  alert() {
    console.log(1);
  }

  lightOn() {
    console.log(2);
  }

  lightOff() {
    console.log(3);
  }
}

let c = new A();
```

#### 接口继承类

接口也可以继承类：

```ts
class Point {
    x: number;
    y: number;
    constructor(x: number, y: number) {
        this.x = x;
        this.y = y;
    }
    point(){
       
    }
}

interface Point3d extends Point {
    z: number;
}

let point3d: Point3d = {x: 1, y: 2, z: 3, point:function(){
    console.log(this.x)
}};
point3d.point()
```

为什么 TypeScript 会支持接口继承类呢？

实际上，当我们在声明 `class Point` 时，除了会创建一个名为 `Point` 的类之外，同时也创建了一个名为 `Point` 的类型（实例的类型）。

所以我们既可以将 `Point` 当做一个类来用（使用 `new Point` 创建它的实例）,也可以将 `Point` 当做一个类型来用（使用 `: Point` 表示参数的类型）

### 继承接口

和类一样，接口也可以相互继承。 这让我们能够从一个接口里复制成员到另一个接口里，可以更灵活地将接口分割到可重用的模块里。

```ts
interface Shape {
    color: string;
}

interface SquareSquare extends Shape {
    sideLength: number;
}

const square ={} as SquareSquare;
square.color = "blue";
square.sideLength = 10;
```

一个接口可以继承多个接口，创建出多个接口的合成接口。

```ts
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```

## 类

传统方法中，JavaScript 通过构造函数实现类的概念，通过原型链实现继承。而在 ES6 中，我们终于迎来了 `class`。

TypeScript 除了实现了所有 ES6 中的类的功能以外，还添加了一些新的用法。

这一节主要介绍类的用法，下一节再介绍如何定义类的类型。

### 类的概念

虽然 JavaScript 中有类的概念，但是可能大多数 JavaScript 程序员并不是非常熟悉类，这里对类相关的概念做一个简单的介绍。

- 类（Class）：定义了一件事物的抽象特点，包含它的属性和方法
- 对象（Object）：类的实例，通过 `new` 生成
- 面向对象（OOP）的三大特性：封装、继承、多态
- 封装（Encapsulation）：将对数据的操作细节隐藏起来，只暴露对外的接口。外界调用端不需要（也不可能）知道细节，就能通过对外提供的接口来访问该对象，同时也保证了外界无法任意更改对象内部的数据
- 继承（Inheritance）：子类继承父类，子类除了拥有父类的所有特性外，还有一些更具体的特性
- 多态（Polymorphism）：由继承而产生了相关的不同的类，对同一个方法可以有不同的响应。比如 `Cat` 和 `Dog` 都继承自 `Animal`，但是分别实现了自己的 `eat` 方法。此时针对某一个实例，我们无需了解它是 `Cat` 还是 `Dog`，就可以直接调用 `eat` 方法，程序会自动判断出来应该如何执行 `eat`
- 存取器（getter & setter）：用以改变属性的读取和赋值行为
- 修饰符（Modifiers）：修饰符是一些关键字，用于限定成员或类型的性质。比如 `public` 表示公有属性或方法
- 抽象类（Abstract Class）：抽象类是供其他类继承的基类，抽象类不允许被实例化。抽象类中的抽象方法必须在子类中被实现
- 接口（Interfaces）：不同类之间公有的属性或方法，可以抽象成一个接口。接口可以被类实现（implements）。一个类只能继承自另一个类，但是可以实现多个接口

### ES6 中类的用法

下面我们先回顾一下 ES6 中类的用法，更详细的介绍可以参考 [ECMAScript 6 入门 - Class](http://es6.ruanyifeng.com/#docs/class)。

#### 属性和方法

使用 `class` 定义类，使用 `constructor` 定义构造函数。

通过 `new` 生成新实例的时候，会自动调用构造函数。

```javascript
class Animal {
    public name;
    constructor(name) {
        this.name = name;
    }
    sayHi() {
        return `My name is ${this.name}`;
    }
}

let a = new Animal('Jack');
console.log(a.sayHi()); // My name is Jack
```

#### 类的继承

使用 `extends` 关键字实现继承，子类中使用 `super` 关键字来调用父类的构造函数和方法。

```javascript
class Cat extends Animal {
  constructor(name) {
    super(name); // 调用父类的 constructor(name)
    console.log(this.name);
  }
  sayHi() {
    return 'Meow, ' + super.sayHi(); // 调用父类的 sayHi()
  }
}

let c = new Cat('Tom'); // Tom
console.log(c.sayHi()); // Meow, My name is Tom
```

#### 存取器

使用 getter 和 setter 可以改变属性的赋值和读取行为：

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }
  get name() {
    return 'Jack';
  }
  set name(value) {
    console.log('setter: ' + value);
  }
}

let a = new Animal('Kitty'); // setter: Kitty
a.name = 'Tom'; // setter: Tom
console.log(a.name); // Jack
```

#### 静态方法

使用 `static` 修饰符修饰的方法称为静态方法，它们不需要实例化，而是直接通过类来调用：

```javascript
class Animal {
  static isAnimal(a) {
    return a instanceof Animal;
  }
}

let a = new Animal('Jack');
Animal.isAnimal(a); // true
a.isAnimal(a); // TypeError: a.isAnimal is not a function
```

### ES7 中类的用法

ES7 中有一些关于类的提案，TypeScript 也实现了它们，这里做一个简单的介绍。

#### 实例属性

ES6 中实例的属性只能通过构造函数中的 `this.xxx` 来定义，ES7 提案中可以直接在类里面定义：

```javascript
class Animal {
  name = 'Jack';

  constructor() {
    // ...
  }
}

let a = new Animal();
console.log(a.name); // Jack
```

#### 静态属性

ES7 提案中，可以使用 `static` 定义一个静态属性：

```javascript
class Animal {
  static num = 42;

  constructor() {
    // ...
  }
}

console.log(Animal.num); // 42
```

### TypeScript 中类的用法

#### public private 和 protected

TypeScript 可以使用三种访问修饰符（Access Modifiers），分别是 `public`、`private` 和 `protected`。

- `public` 修饰的属性或方法是公有的，可以在任何地方被访问到，默认所有的属性和方法都是 `public` 的
- `private` 修饰的属性或方法是私有的，不能在声明它的类的外部访问
- `protected` 修饰的属性或方法是受保护的，它和 `private` 类似，区别是它在子类中也是允许被访问的

下面举一些例子：

```ts
class Animal {
  public name;
  public constructor(name) {
    this.name = name;
  }
}

let a = new Animal('Jack');
console.log(a.name); // Jack
a.name = 'Tom';
console.log(a.name); // Tom
```

上面的例子中，`name` 被设置为了 `public`，所以直接访问实例的 `name` 属性是允许的。

很多时候，我们希望有的属性是无法直接存取的，这时候就可以用 `private` 了：

```ts
class Animal {
  private name;
  public constructor(name) {
    this.name = name;
  }
}

let a = new Animal('Jack');
console.log(a.name); // Jack
a.name = 'Tom';

// index.ts(9,13): error TS2341: Property 'name' is private and only accessible within class 'Animal'.
// index.ts(10,1): error TS2341: Property 'name' is private and only accessible within class 'Animal'.
```

需要注意的是，TypeScript 编译之后的代码中，并没有限制 `private` 属性在外部的可访问性。

上面的例子编译后的代码是：

```javascript
var Animal = (function () {
  function Animal(name) {
    this.name = name;
  }
  return Animal;
})();
var a = new Animal('Jack');
console.log(a.name);
a.name = 'Tom';
```

使用 `private` 修饰的属性或方法，在子类中也是不允许访问的：

```ts
class Animal {
  private name;
  public constructor(name) {
    this.name = name;
  }
}

class Cat extends Animal {
  constructor(name) {
    super(name);
    console.log(this.name);
  }
}

// index.ts(11,17): error TS2341: Property 'name' is private and only accessible within class 'Animal'.
```

而如果是用 `protected` 修饰，则允许在子类中访问：

```ts
class Animal {
  protected name;
  public constructor(name) {
    this.name = name;
  }
}

class Cat extends Animal {
  constructor(name) {
    super(name);
    console.log(this.name);
  }
}
```

当构造函数修饰为 `private` 时，该类不允许被继承或者实例化：

```ts
class Animal {
  public name;
  private constructor(name) {
    this.name = name;
  }
}
class Cat extends Animal {
  constructor(name) {
    super(name);
  }
}

let a = new Animal('Jack');

// index.ts(7,19): TS2675: Cannot extend a class 'Animal'. Class constructor is marked as private.
// index.ts(13,9): TS2673: Constructor of class 'Animal' is private and only accessible within the class declaration.
```

当构造函数修饰为 `protected` 时，该类只允许被继承：

```ts
class Animal {
  public name;
  protected constructor(name) {
    this.name = name;
  }
}
class Cat extends Animal {
  constructor(name) {
    super(name);
  }
}

let a = new Animal('Jack');

// index.ts(13,9): TS2674: Constructor of class 'Animal' is protected and only accessible within the class declaration.
```

#### 参数属性

修饰符和`readonly`还可以使用在构造函数参数中，等同于类中定义该属性同时给该属性赋值，使代码更简洁。

```ts
class Animal {
  // public name: string;
  public constructor(public name) {
    // this.name = name;
  }
}
```

#### readonly

只读属性关键字，只允许出现在属性声明或索引签名或构造函数中。

```ts
class Animal {
  readonly name;
  public constructor(name) {
    this.name = name;
  }
}

let a = new Animal('Jack');
console.log(a.name); // Jack
a.name = 'Tom';

// index.ts(10,3): TS2540: Cannot assign to 'name' because it is a read-only property.
```

注意如果 `readonly` 和其他访问修饰符同时存在的话，需要写在其后面。

```ts
class Animal {
  // public readonly name;
  public constructor(public readonly name) {
    // this.name = name;
  }
}
```

#### 抽象类

`abstract` 用于定义抽象类和其中的抽象方法。

什么是抽象类？

首先，抽象类是不允许被实例化的：

```ts
abstract class Animal {
  public name;
  public constructor(name) {
    this.name = name;
  }
  public abstract sayHi();
}

let a = new Animal('Jack');

// index.ts(9,11): error TS2511: Cannot create an instance of the abstract class 'Animal'.
```

上面的例子中，我们定义了一个抽象类 `Animal`，并且定义了一个抽象方法 `sayHi`。在实例化抽象类的时候报错了。

其次，抽象类中的抽象方法必须被子类实现：

```ts
abstract class Animal {
  public name;
  public constructor(name) {
    this.name = name;
  }
  public abstract sayHi();
}

class Cat extends Animal {
  public eat() {
    console.log(`${this.name} is eating.`);
  }
}

let cat = new Cat('Tom');

// index.ts(9,7): error TS2515: Non-abstract class 'Cat' does not implement inherited abstract member 'sayHi' from class 'Animal'.
```

上面的例子中，我们定义了一个类 `Cat` 继承了抽象类 `Animal`，但是没有实现抽象方法 `sayHi`，所以编译报错了。

下面是一个正确使用抽象类的例子：

```ts
abstract class Animal {
  public name;
  public constructor(name) {
    this.name = name;
  }
  public abstract sayHi();
}

class Cat extends Animal {
  public sayHi() {
    console.log(`Meow, My name is ${this.name}`);
  }
}

let cat = new Cat('Tom');
```

上面的例子中，我们实现了抽象方法 `sayHi`，编译通过了。

需要注意的是，即使是抽象方法，TypeScript 的编译结果中，仍然会存在这个类，上面的代码的编译结果是：

```javascript
var __extends =
  (this && this.__extends) ||
  function (d, b) {
    for (var p in b) if (b.hasOwnProperty(p)) d[p] = b[p];
    function __() {
      this.constructor = d;
    }
    d.prototype = b === null ? Object.create(b) : ((__.prototype = b.prototype), new __());
  };
var Animal = (function () {
  function Animal(name) {
    this.name = name;
  }
  return Animal;
})();
var Cat = (function (_super) {
  __extends(Cat, _super);
  function Cat() {
    _super.apply(this, arguments);
  }
  Cat.prototype.sayHi = function () {
    console.log('Meow, My name is ' + this.name);
  };
  return Cat;
})(Animal);
var cat = new Cat('Tom');
```

#### 类的类型

给类加上 TypeScript 的类型很简单，与接口类似：

```ts
class Animal {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
  sayHi(): string {
    return `My name is ${this.name}`;
  }
}

let a: Animal = new Animal('Jack');
console.log(a.sayHi()); // My name is Jack
```

## 函数

### 函数类型

#### 为函数定义类型

```ts
// 函数声明
function add(x: number, y: number): number {
    return x + y;
}
// 函数表达式
let myAdd = function(x: number, y: number): number { return x + y; };
```

**书写完整函数类型**

现在我们已经为函数指定了类型，下面让我们写出函数的完整类型。

```ts
let myAdd: (x: number, y: number) => number = function(x: number, y: number): number { return x + y; };
```

函数类型包含两部分：参数类型和返回值类型。 当写出完整函数类型的时候，这两部分都是需要的

```
let myAdd: (baseValue: number, increment: number) => number = 
	function(x: number, y: number): number { return x + y; };
```

只要参数类型是匹配的，那么就认为它是有效的函数类型，**而不在乎参数名是否正确。**

**推断类型**

如果你在赋值语句的一边指定了类型但是另一边没有类型的话，TypeScript编译器会自动识别出类型：

```
// myAdd has the full function type
let myAdd = function(x: number, y: number): number { return x + y; };

// The parameters `x` and `y` have the type number
let myAdd: (baseValue: number, increment: number) => number =
    function(x, y) { return x + y; };
```

这叫做“按上下文归类”，是类型推论的一种。

#### 可选参数

前面提到，输入多余的（或者少于要求的）参数，是不允许的。那么如何定义可选的参数呢？

与接口中的可选属性类似，我们用 `?` 表示可选的参数：

```ts
function buildName(firstName: string, lastName?: string) {
    if (lastName) {
        return firstName + ' ' + lastName;
    } else {
        return firstName;
    }
}
let tomcat = buildName('Tom', 'Cat');
let tom = buildName('Tom');
```

需要注意的是，可选参数必须接在必需参数后面。换句话说，**可选参数后面不允许再出现必需参数了**：

```ts
function buildName(firstName?: string, lastName: string) {
    if (firstName) {
        return firstName + ' ' + lastName;
    } else {
        return lastName;
    }
}
let tomcat = buildName('Tom', 'Cat');
let tom = buildName(undefined, 'Tom');

// index.ts(1,40): error TS1016: A required parameter cannot follow an optional parameter.
```

#### **默认参数**

在 ES6 中，我们允许给函数的参数添加默认值，**TypeScript 会将添加了默认值的参数识别为可选参数**：

```ts
function buildName(firstName: string, lastName: string = 'Cat') {
    return firstName + ' ' + lastName;
}
let tomcat = buildName('Tom', 'Cat');
let tom = buildName('Tom');
```

**此时就不受「可选参数必须接在必需参数后面」的限制了：**

```ts
function buildName(firstName: string = 'Tom', lastName: string) {
    return firstName + ' ' + lastName;
}
let tomcat = buildName('Tom', 'Cat');
let cat = buildName(undefined, 'Cat');
```

#### 剩余参数

ES6 中，可以使用 `...rest` 的方式获取函数中的剩余参数（rest 参数）：

```javascript
function push(array, ...items) {
    items.forEach(function(item) {
        array.push(item);
    });
}

let a: any[] = [];
push(a, 1, 2, 3);
```

事实上，`items` 是一个数组。所以我们可以用数组的类型来定义它：

```ts
function push(array: any[], ...items: any[]) {
    items.forEach(function(item) {
        array.push(item);
    });
}

let a = [];
push(a, 1, 2, 3);
```

注意，rest 参数只能是最后一个参数，关于 rest 参数，可以参考 [ES6 中的 rest 参数](http://es6.ruanyifeng.com/#docs/function#rest参数)。

### `this`

学习如何在JavaScript里正确使用`this`就好比一场成年礼。 由于TypeScript是JavaScript的超集，TypeScript程序员也需要弄清 `this`工作机制并且当有bug的时候能够找出错误所在。 幸运的是，TypeScript能通知你错误地使用了 `this`的地方。 如果你想了解JavaScript里的 `this`是如何工作的，那么首先阅读Yehuda Katz写的[Understanding JavaScript Function Invocation and "this"](http://yehudakatz.com/2011/08/11/understanding-javascript-function-invocation-and-this/)。 Yehuda的文章详细的阐述了 `this`的内部工作原理，因此我们这里只做简单介绍。

#### `this`和箭头函数

JavaScript里，`this`的值在函数被调用的时候才会指定。 这是个既强大又灵活的特点，但是你需要花点时间弄清楚函数调用的上下文是什么。 但众所周知，这不是一件很简单的事，尤其是在返回一个函数或将函数当做参数传递的时候。

下面看一个例子：

```ts
let deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    createCardPicker: function() {
        return function() {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```

可以看到`createCardPicker`是个函数，并且它又返回了一个函数。 如果我们尝试运行这个程序，会发现它并没有弹出对话框而是报错了。 因为 `createCardPicker`返回的函数里的`this`被设置成了`window`而不是`deck`对象。 因为我们只是独立的调用了 `cardPicker()`。 顶级的非方法式调用会将 `this`视为`window`。 （注意：在严格模式下， `this`为`undefined`而不是`window`）。

为了解决这个问题，我们可以在函数被返回时就绑好正确的`this`。 这样的话，无论之后怎么使用它，都会引用绑定的‘deck’对象。 我们需要改变函数表达式来使用ECMAScript 6箭头语法。 **箭头函数能保存函数创建时的 `this`值，而不是调用时的值**：

```ts
let deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    createCardPicker: function() {
        // NOTE: the line below is now an arrow function, allowing us to capture 'this' right here
        return () => {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```

更好事情是，TypeScript会警告你犯了一个错误，如果你给编译器设置了`--noImplicitThis`标记。 它会指出 `this.suits[pickedSuit]`里的`this`的类型为`any`。

#### `this`参数

不幸的是，`this.suits[pickedSuit]`的类型依旧为`any`。 这是因为 `this`来自对象字面量里的函数表达式。 修改的方法是，提供一个显式的 `this`参数。 `this`参数是个假的参数，它出现在参数列表的最前面：

```ts
function f(this: void) {
    // make sure `this` is unusable in this standalone function
}
```

让我们往例子里添加一些接口，`Card` 和 `Deck`，让类型重用能够变得清晰简单些：

```ts
interface Card {
    suit: string;
    card: number;
}
interface Deck {
    suits: string[];
    cards: number[];
    createCardPicker(this: Deck): () => Card;
}
let deck: Deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    // NOTE: The function now explicitly specifies that its callee must be of type Deck
    createCardPicker: function(this: Deck) {
        return () => {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```

现在TypeScript知道`createCardPicker`期望在某个`Deck`对象上调用。 也就是说 `this`是`Deck`类型的，而非`any`，因此`--noImplicitThis`不会报错了。

#### `this`参数在回调函数里

你可以也看到过在回调函数里的`this`报错，当你将一个函数传递到某个库函数里稍后会被调用时。 因为当回调被调用的时候，它们会被当成一个普通函数调用， `this`将为`undefined`。 稍做改动，你就可以通过 `this`参数来避免错误。 首先，库函数的作者要指定 `this`的类型：

```ts
interface UIElement {
    addClickListener(onclick: (this: void, e: Event) => void): void;
}
```

`this: void` means that `addClickListener` expects `onclick` to be a function that does not require a `this` type. Second, annotate your calling code with `this`:

```ts
class Handler {
    info: string;
    onClickBad(this: Handler, e: Event) {
        // oops, used this here. using this callback would crash at runtime
        this.info = e.message;
    }
}
let h = new Handler();
uiElement.addClickListener(h.onClickBad); // error!
```

指定了`this`类型后，你显式声明`onClickBad`必须在`Handler`的实例上调用。 然后TypeScript会检测到 `addClickListener`要求函数带有`this: void`。 改变 `this`类型来修复这个错误：

```ts
class Handler {
    info: string;
    onClickGood(this: void, e: Event) {
        // can't use this here because it's of type void!
        console.log('clicked!');
    }
}
let h = new Handler();
uiElement.addClickListener(h.onClickGood);
```

因为`onClickGood`指定了`this`类型为`void`，因此传递`addClickListener`是合法的。 当然了，这也意味着不能使用 `this.info`. 如果你两者都想要，你不得不使用箭头函数了：

```ts
class Handler {
    info: string;
    onClickGood = (e: Event) => { this.info = e.message }
}
```

这是可行的因为箭头函数不会捕获`this`，所以你总是可以把它们传给期望`this: void`的函数。 缺点是每个 `Handler`对象都会创建一个箭头函数。 另一方面，方法只会被创建一次，添加到 `Handler`的原型链上。 它们在不同 `Handler`对象间是共享的。

### 重载

重载允许一个函数接受不同数量或类型的参数时，作出不同的处理。

比如，我们需要实现一个函数 `reverse`，输入数字 `123` 的时候，输出反转的数字 `321`，输入字符串 `'hello'` 的时候，输出反转的字符串 `'olleh'`。

利用联合类型，我们可以这么实现：

```ts
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
```

**然而这样有一个缺点，就是不能够精确的表达，输入为数字的时候，输出也应该为数字，输入为字符串的时候，输出也应该为字符串。**

这时，我们可以使用重载定义多个 `reverse` 的函数类型：为同一个函数提供多个函数类型定义来进行函数重载。 编译器会根据这个列表去处理函数的调用

```ts
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
```

## 泛型

### 介绍

> 考虑可重用性。 组件不仅能够支持当前的数据类型，同时也能支持未来的数据类型

泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。

当我们需要写一个传入什么类型就得到什么类型的函数

- ```ts
  function one(a: any) : any{
    return a;
  }
  ```

- ```ts
  function one(a: any) : any{
      if(typeof a === 'number') {
          let ret = (a as number)
          return ret ;
      }
      return a;
  }
  //每一种类型都写一个方法
  ```

- ```ts
  function one<T>(a: T) : T{
      return a;
  }
  let a1 = one<number>(1)
  let a2 = one(520)
  //描述T是什么类型的时候，可以在<number>描述它是一个number类型，
  //也可以类型推论
  ```

### 使用泛型变量

如果我们想同时打印出`arg`的长度。 我们很可能会这样做：

```ts
function loggingIdentity<T>(arg: T): T {
    console.log(arg.length);  // Error: T doesn't have .length
    return arg;
}
```

如果这么做，编译器会报错说我们使用了`arg`的`.length`属性，但是没有地方指明`arg`具有这个属性。 记住，这些类型变量代表的是任意类型，所以使用这个函数的人可能传入的是个数字，而数字是没有 `.length`属性的。

现在假设我们想操作是`T`类型的数组而不直接是`T`。由于我们操作的是数组，所以`.length`属性是应该存在的。 我们可以像创建其它数组一样创建这个数组：

```ts
function loggingIdentity<T>(arg: T[]): T[] {
    console.log(arg.length);  // Array has a .length, so no more error
    return arg;
}

function loggingIdentity<T>(arg: Array<T>): Array<T> {
    console.log(arg.length);  // Array has a .length, so no more error
    return arg;
}
```

### 泛型类型

泛型函数的类型与非泛型函数的类型没什么不同，只是有一个类型参数在最前面，像函数声明一样：

```ts
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: <T>(arg: T) => T = identity;
```

我们也可以使用不同的泛型参数名，只要在数量上和使用方式上能对应上就可以。

```ts
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: <U>(arg: U) => U = identity;
```

我们还可以使用带有调用签名的对象字面量来定义泛型函数：

```ts
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: {<T>(arg: T): T} = identity;
```

这引导我们去写第一个泛型接口了。 我们把上面例子里的对象字面量拿出来做为一个接口：

```ts
interface GenericIdentityFn {
    <T>(arg: T): T;
}

function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: GenericIdentityFn = identity;
```

一个相似的例子，我们可以把**泛型参数当作整个接口的一个参数**。 这样我们就能清楚的知道使用的具体是哪个泛型类型（比如： `Dictionary<string>而不只是Dictionary`）。 这样接口里的其它成员也能知道这个参数的类型了。

```ts
interface GenericIdentityFn<T> {
    (arg: T): T;
}

function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: GenericIdentityFn<number> = identity;
```

### 泛型类

泛型类看上去与泛型接口差不多。 泛型类使用（ `<>`）括起泛型类型，跟在类名后面。

```ts
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
console.log(myGenericNumber.add(1,2))
```

类有两部分：静态部分和实例部分。 泛型类指的是实例部分的类型，所以类的静态属性不能使用这个泛型类型。

### 泛型约束extends

你应该会记得之前的一个例子，我们有时候想操作某类型的一组值，并且我们知道这组值具有什么样的属性。 在 `loggingIdentity`例子中，我们想访问`arg`的`length`属性，但是编译器并不能证明每种类型都有`length`属性，所以就报错了。

```ts
function loggingIdentity<T>(arg: T): T {
    console.log(arg.length);  // Error: T doesn't have .length
    return arg;
}
```

相比于操作any所有类型，我们想要限制函数去处理任意带有`.length`属性的所有类型。 只要传入的类型有这个属性，我们就允许，就是说至少包含这一属性。 为此，我们需要列出对于T的约束要求。

为此，我们定义一个接口来描述约束条件。 创建一个包含 `.length`属性的接口，使用这个接口和`extends`关键字来实现约束：

```ts
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);  // Now we know it has a .length property, so no more error
    return arg;
}
```

现在这个泛型函数被定义了约束，因此它不再是适用于任意类型：

```ts
loggingIdentity(3);  // Error, 类型“number”的参数不能赋给类型“Lengthwise”的参数。
```

我们需要传入符合约束类型的值，必须包含必须的属性：

```ts
loggingIdentity({length: 10, value: 3});
```

### 案例

- Promise的泛型T(Promise<T>)的含义

  interface AxiosInstance {  <T = any>(value: T): Promise<T> }

  [Promise](https://so.csdn.net/so/search?q=Promise&spm=1001.2101.3001.7020)的泛型T代表promise变成成功态之后resolve的值，resolve(value)

## 高级类型

### 交叉类型

交叉类型是将多个类型合并为一个类型。 这让我们可以把现有的多种类型叠加到一起成为一种类型，它包含了所需的所有类型的特性。 例如，type是type1和type2接口的交集。 就是说这个类型的对象同时拥有了这二种类型的成员。

```ts
interface type1{
    a1:number,
    a2:string
}
interface type2{
    b1:number,
    b2:string
}
interface type<t,k>{
   a:t,
   b:k
}
let c:type<type1,type2> = {
   a:{
       a1:1,
       a2:'1'
   },
   b:{
       b1:1,
       b2:'1'
   }
}
console.log(c)//{ a: { a1: 1, a2: '1' }, b: { b1: 1, b2: '1' } } 
```

```ts
interface type1{
    a1:number,
    a2:string
}
interface type2{
    b1:number,
    b2:string
}
interface type<t,k>{
   a1,
   a2,
   b1,
   b2
}
let c:type<type1,type2> = {
       a1:1,
       a2:'1',
       b1:1,
       b2:'1'
}
console.log(c)//{ a1: 1, a2: '1', b1: 1, b2: '1' } 
```

### 联合类型

联合类型（Union Types）表示取值可以为多种类型中的一种。

#### 简单的例子

```ts
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
let myFavoriteNumber: string | number;
myFavoriteNumber = true;

// index.ts(2,1): error TS2322: Type 'boolean' is not assignable to type 'string | number'.
//   Type 'boolean' is not assignable to type 'number'.
```

联合类型使用 `|` 分隔每个类型。

这里的 `let myFavoriteNumber: string | number` 的含义是，允许 `myFavoriteNumber` 的类型是 `string` 或者 `number`，但是不能是其他类型。

#### 访问联合类型的属性或方法

当 TypeScript 不确定一个联合类型的变量到底是哪个类型的时候，我们**只能访问此联合类型的所有类型里共有的属性或方法**：

```ts
function getLength(something: string | number): number {
    return something.length;
}

// index.ts(2,22): error TS2339: Property 'length' does not exist on type 'string | number'.
//   Property 'length' does not exist on type 'number'.
```

上例中，`length` 不是 `string` 和 `number` 的共有属性，所以会报错。

访问 `string` 和 `number` 的共有属性是没问题的：

```ts
function getString(something: string | number): string {
    return something.toString();
}
```

联合类型的变量在被赋值的时候，会根据**类型推论**的规则推断出一个类型：

```ts
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
console.log(myFavoriteNumber.length); // 5
myFavoriteNumber = 7;
console.log(myFavoriteNumber.length); // 编译时报错

// index.ts(5,30): error TS2339: Property 'length' does not exist on type 'number'.
```

上例中，第二行的 `myFavoriteNumber` 被推断成了 `string`，访问它的 `length` 属性不会报错。

而第四行的 `myFavoriteNumber` 被推断成了 `number`，访问它的 `length` 属性时就报错了。

### `typeof`类型保护

### `instanceof`类型保护

*`instanceof`类型保护*是通过构造函数来细化类型的一种方式。 比如，我们借鉴一下之前字符串填充的例子：

```ts
interface Padder {
    getPaddingString(): string
}

class SpaceRepeatingPadder implements Padder {
    constructor(private numSpaces: number) { }
    getPaddingString() {
        return Array(this.numSpaces + 1).join(" ");
    }
}

class StringPadder implements Padder {
    constructor(private value: string) { }
    getPaddingString() {
        return this.value;
    }
}

function getRandomPadder() {
    return Math.random() < 0.5 ?
        new SpaceRepeatingPadder(4) :
        new StringPadder("  ");
}

// 类型为SpaceRepeatingPadder | StringPadder
let padder: Padder = getRandomPadder();

if (padder instanceof SpaceRepeatingPadder) {
    padder; // 类型细化为'SpaceRepeatingPadder'
}
if (padder instanceof StringPadder) {
    padder; // 类型细化为'StringPadder'
}
```

`instanceof`的右侧要求是一个构造函数，TypeScript将细化为：

1. 此构造函数的 `prototype`属性的类型，如果它的类型不为 `any`的话
2. 构造签名所返回的类型的联合

以此顺序。

### 类型别名type

#### **type关键字**

说明：字面意思，用来给一个类型起个新名字。生成一个接口

```ts
type str1 = string;
type str2 = ()=>string;   //此为函数类型形状，注意跟下面区分
type str = str1 | str2;

let s:str = "hello";
let s1:str = () =>"heihei";   //为箭头函数
```

类型别名会给一个类型起个新名字。 类型别名有时和接口很像，但是可以作用于原始值，联合类型，元组以及其它任何你需要手写的类型。

```ts
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
    if (typeof n === 'string') {
        return n;
    }
    else {
        return n();
    }
}
```

起别名不会新建一个类型 - 它创建了一个新 *名字*来引用那个类型。 给原始类型起别名通常没什么用，尽管可以做为文档的一种形式使用。

同接口一样，类型别名也可以是泛型 - 我们可以添加类型参数并且在别名声明的右侧传入：

```ts
type Container<T> = { value: T };
```

我们也可以使用类型别名来在属性里引用自己：

```ts
type Tree<T> = {
    value: T;
    left: Tree<T>;
    right: Tree<T>;
}
```

与交叉类型一起使用，我们可以创建出一些十分稀奇古怪的类型。

```ts
type LinkedList<T> = T & { next: LinkedList<T> };

interface Person {
    name: string;
}

var people: LinkedList<Person>;
var s = people.name;
var s = people.next.name;
var s = people.next.next.name;
var s = people.next.next.next.name;
```

然而，类型别名不能出现在声明右侧的任何地方。

```ts
type Yikes = Array<Yikes>; // error
```

#### type 与 interface 的区别

相同点：

- 都可以描述一个对象或者函数
- 都允许拓展（extends和交叉类型）

不同点：

- type 可以声明基本类型别名，联合类型，元组等类型
- type 语句中还可以使用 typeof 获取实例的类型进行赋值
- interface 能够声明合并

##### 相似的

`type` 用于定义数据的类型别名。`interface` 用于定义数据的类型别名。

```ts
type User = {
    name1: string
    age: number
  };
let user:User={
    name1:'1',
    age:1
}

interface User {
    name1: string
    age: number
  };
let user:User={
    name1:'1',
    age:1
}
```

##### 不相似的

`type` 与 `interface` 都可以实现继承，但是他们的表现形式不同。

```ts
//interface extends interface

interface Name {
  name: string;
}
interface User extends Name {
  age: number;
}

//type 与 type 交叉

type Name = {
  name: string;
}
type User = Name & { age: number  };

//interface extends type

type Name = {
  name: string;
}
interface User extends Name {
  age: number;
}

//type 与 interface 交叉

interface Name {
  name: string;
}
type User = Name & {
  age: number;
}
```

因为 `type` 作为类型的别名，因此可以轻易的实现声明基本类型别名，联合类型，元组等类型，而 interface 则不行。

```ts
// 基本类型别名
type Name = string

// 联合类型,
interface Dog {
    a:number
}
interface Cat {
    b:string
}
type Pet = Dog | Cat
let c:Pet={a:1}

// 具体定义数组每个位置的类型
type PetList = [Dog, Pet]
```

interface 能够声明合并，而 type 不行（会报重复声明错误）。

```ts
interface User {
    name: string,
    age: number,
}
interface User {
    sex: string,
}
let a:User={
    name:'q',
    age:1,
    sex:'men'
}
type guest = {
    name: string,
    age: number,
}
type guest = {
    sex: string,
}
//type 报错
```

# 设计模式

## 观察者和发布订阅模式

### 观察者模式

https://juejin.cn/post/7055441354054172709#heading-1

#### 故事背景

前端宗门自从发布了传承方案后，宗门日渐繁荣，弟子们的水平不断提高，但新的问题出现了——高质量任务严重不足。宗门任务大殿每个月发布的五星任务是有限的，想要接取五星任务的弟子却如过江之鲫，于是滋生了**武侠黄牛**，恶意抢任务，坐地起价。

宗门不愿任务被恶意哄抢，决定调整任务市场秩序，因此推出任务订阅功能——观察者模式。

#### 实现

在观察者模式中，只有两种主体：目标对象 (`Object`) 和观察者 (`Observer`)。宗门任务大殿就是目标对象，弟子们就是观察者。

- 目标对象 Subject:
  - 维护观察者列表 `observerList` ———— 维护拥有订阅权限的弟子列表
  - 定义**添加观察者的方法** ———— 提供弟子购买订阅权限的功能
  - 当自身发生变化后，通过调用自己的 `notify` 方法依次通知每个观察者执行 `update` 方法 ———— 发布对应任务后通知有订阅权限的弟子
- 观察者 `Observer` 需要实现 `update` 方法，供目标对象调用。`update`方法中可以执行自定义的业务逻辑 弟子们需要定义接收任务通知后的方法，例如去抢任务或任务不适合，继续等待下一个任务

![Observer.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1f96a826bb7945dd96f9696f41b45534~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

```js
class Observer {
  constructor(name) {
    this.name = name;
  }
  update({ taskType, taskInfo }) {
    // 假设任务分为日常route和战斗war
    if (taskType === "route") {
      console.log(`${this.name}不需要日常任务`);
      return;
    }
    this.goToTaskHome(taskInfo);
  }
  goToTaskHome(info) {
    console.log(`${this.name}去任务大殿抢${info}任务`);
  }
}

class Subject {
  constructor() {
    this.observerList = []
  }
  addObserver(observer) {
    this.observerList.push(observer);
  }
  notify(task) {
    console.log("发布五星任务");
    this.observerList.forEach(observer => observer.update(task))
  }
}

const subject = new Subject();
const stu1 = new Observer("弟子1");
const stu2 = new Observer("弟子2");

// stu1 stu2 购买五星任务通知权限
subject.addObserver(stu1);
subject.addObserver(stu2);

// 任务殿发布五星战斗任务
const warTask = {
  taskType: 'war',
  taskInfo: "猎杀时刻"
}

// 任务大殿通知购买权限弟子
subject.notify(warTask);

// 任务殿发布五星日常任务
const routeTask = {
  taskType: 'route',
  taskInfo: "种树浇水"
}

subject.notify(routeTask);

输出结果:
// 战斗任务
发布五星任务
弟子1去任务大殿抢猎杀时刻任务
弟子2去任务大殿抢猎杀时刻任务

// 日常任务
发布五星任务
弟子1不需要日常任务
弟子2不需要日常任务
```

### 发布订阅模式

发布订阅模式与观察者模式相比，发布订阅模式中有三个角色，发布者 `Publisher` ，事件调度中心 `Event Channel` ，订阅者 `Subscriber` 。

宗门感觉把任务订阅放在任务大殿中有些繁琐，于是决定在任务大殿和弟子中间添加**中介**。弟子在中介中订阅其需要的任务类型，当任务大殿发布任务后，中介会将发布任务给对应的订阅者。

![publish-subscribe.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8fc5d255760a4192a939785e427fabec~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

以目前的**热播剧开端**为例，临近过年，摸鱼的心思越来越重，每天就迫不及待的等开端更新，想在开端更新的第一刻就开始看剧，那你会怎么做那？总不能时时刻刻刷新页面吧。平台提供了消息订阅功能，如果你选择订阅，平台更新开端后，会第一时间发消息通知你，订阅后，你就可以愉快的追剧了。

上面案例中，开端就是发布者 `Publisher`，追剧人就是订阅者 `Subscribe`，平台则承担了事件通道 `Event Channel` 功能。

```js
class PubSub {
    constructor() {
        // 事件中心
        // 存储格式: warTask: [], routeTask: []
        // 每种事件(任务)下存放其订阅者的回调函数
        this.events = {}
    }
    // 订阅方法
    subscribe(type, cb) {
        if (!this.events[type]) {
            this.events[type] = [];
        }
        this.events[type].push(cb);
    }
    // 发布方法
    publish(type, ...args) {
        if (this.events[type]) {
            this.events[type].forEach(cb => cb(...args))
        }
    }
    // 取消订阅方法
    unsubscribe(type, cb) {
        if (this.events[type]) {
            const cbIndex = this.events[type].findIndex(e=> e === cb)
            if (cbIndex != -1) {
                this.events[type].splice(cbIndex, 1);
            }
        }
        if (this.events[type].length === 0) {
            delete this.events[type];
        }
    }
    unsubscribeAll(type) {
        if (this.events[type]) {
            delete this.events[type];
        }
    }
}

// 创建一个中介公司
let pubsub = new PubSub();

// 弟子一订阅战斗任务
pubsub.subscribe('warTask', function (taskInfo){
    console.log("宗门殿发布战斗任务，任务信息:" + taskInfo);
})
// 弟子一订阅战斗任务
pubsub.subscribe('routeTask', function (taskInfo) {
    console.log("宗门殿发布日常任务，任务信息:" + taskInfo);
});
// 弟子三订阅全类型任务
pubsub.subscribe('allTask', function (taskInfo) {
    console.log("宗门殿发布五星任务，任务信息:" + taskInfo);
});

// 发布战斗任务
pubsub.publish('warTask', "猎杀时刻");
pubsub.publish('allTask', "猎杀时刻");

// 发布日常任务
pubsub.publish('routeTask', "种树浇水");
pubsub.publish('allTask', "种树浇水");

输出结果:

// 战斗任务
宗门殿发布战斗任务，任务信息:猎杀时刻
宗门殿发布五星任务，任务信息:猎杀时刻
// 日常任务
宗门殿发布日常任务，任务信息:种树浇水
宗门殿发布五星任务，任务信息:种树浇水
```

### 总结

<img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/52a175a22ade4793a0297b18c5fb293d~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?" alt="observer-publish_subscribe.png" style="zoom: 50%;" />

| **设计模式** | **观察者模式**                                | 发布订阅模式                                            |
| ------------ | --------------------------------------------- | ------------------------------------------------------- |
| 主体         | Object观察者、Subject目标对象                 | Publisher发布者、Event Channel事件中心、Subscribe订阅者 |
| 主体关系     | Subject中通过observerList记录ObServer         | Publisher和Subscribe不想不知道对方，通过中介联系        |
| 优点         | 角色明确，Subject和Object要遵循约定的成员方法 | 松散耦合，灵活度高，通常应用在异步编程中                |
| 缺点         | 紧耦合                                        | 当事件类型变多时，会增加维护成本                        |
| 使用案例     | **双向数据绑定**                              | **事件总线EventBus**                                    |

