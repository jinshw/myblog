title: ECMAScript 6(一) let和const命令
tags:
  - es6
date: 2017-04-26 16:09:56
---

# ES6 let 和const命令

> 1. let命令
> 2. 块级作用域
> 3. const命令
> 4. 顶层对象的属性
> 5. global对象

<!--more-->

## let命令

ES6 新增了let命令，用来声明变量。类似于var，但是声明的变量，只在let命令所在的代码块内有效。

- **不存在变量提升**

  ```
  // var 的情况
  console.log(foo); // 输出undefined
  var foo = 2;

  // let 的情况
  console.log(bar); // 报错ReferenceError
  let bar = 2;
  ```

- **暂时性死区**

  总之，在代码块内，使用`let`命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”（temporal dead zone，简称 TDZ）。

- 不允许重复声明

##  块级作用域

> ES6 引入了块级作用域，明确允许在块级作用域之中声明函数。ES6 规定，块级作用域之中，函数声明语句的行为类似于`let`，在块级作用域之外不可引用。



### do表达式

> 在有一个[提案](http://wiki.ecmascript.org/doku.php?id=strawman:do_expressions)，使得块级作用域可以变为表达式，也就是说可以返回值，办法就是在块级作用域之前加上`do`，使它变为`do`表达式。

```
let x = do {
  let t = f();
  t * t + 1;
};
```



------



## const命令

> `const`声明一个只读的常量。一旦声明，常量的值就不能改变。



- `const`声明的变量不得改变值，这意味着，`const`一旦声明变量，就必须立即初始化，不能留到以后赋值。


- `const`的作用域与`let`命令相同：只在声明所在的块级作用域内有效。
- `const`命令声明的常量也是不提升，同样存在暂时性死区，只能在声明的位置后面使用。
- `const`声明的常量，也与`let`一样不可重复声明。



> 本质：
> const实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址不得改动。对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指针，const只能保证这个指针是固定的，至于它指向的数据结构是不是可变的，就完全不能控制了。因此，将一个对象声明为常量必须非常小心。

```
const foo = {};

// 为 foo 添加一个属性，可以成功
foo.prop = 123;
foo.prop // 123

// 将 foo 指向另一个对象，就会报错
foo = {}; // TypeError: "foo" is read-only

```

上面代码中，常量`foo`储存的是一个地址，这个地址指向一个对象。不可变的只是这个地址，即不能把`foo`指向另一个地址，但对象本身是可变的，所以依然可以为其添加新属性。



下面是另一个例子。

```
const a = [];
a.push('Hello'); // 可执行
a.length = 0;    // 可执行
a = ['Dave'];    // 报错

```

上面代码中，常量`a`是一个数组，这个数组本身是可写的，但是如果将另一个数组赋值给`a`，就会报错。

如果真的想将对象冻结，应该使用`Object.freeze`方法。

```
const foo = Object.freeze({});

// 常规模式时，下面一行不起作用；
// 严格模式时，该行会报错
foo.prop = 123;

```

上面代码中，常量`foo`指向一个冻结的对象，所以添加新属性不起作用，严格模式时还会报错。

除了将对象本身冻结，对象的属性也应该冻结。下面是一个将对象彻底冻结的函数。

```
var constantize = (obj) => {
  Object.freeze(obj);
  Object.keys(obj).forEach( (key, i) => {
    if ( typeof obj[key] === 'object' ) {
      constantize( obj[key] );
    }
  });
};
```



## ES6声明变量6中方法

> ES5 只有两种声明变量的方法：`var`命令和`function`命令。ES6除了添加`let`和`const`命令，后面章节还会提到，另外两种声明变量的方法：`import`命令和`class`命令。所以，ES6 一共有6种声明变量的方法。



##  global 对象

很难找到一种方法，可以在所有情况下，都取到顶层对象。下面是两种勉强可以使用的方法。

```
// 方法一
(typeof window !== 'undefined'
   ? window
   : (typeof process === 'object' &&
      typeof require === 'function' &&
      typeof global === 'object')
     ? global
     : this);

// 方法二
var getGlobal = function () {
  if (typeof self !== 'undefined') { return self; }
  if (typeof window !== 'undefined') { return window; }
  if (typeof global !== 'undefined') { return global; }
  throw new Error('unable to locate global object');
};

```

现在有一个提案，在语言标准的层面，引入`global`作为顶层对象。也就是说，在所有环境下，`global`都是存在的，都可以从它拿到顶层对象。

垫片库`system.global`模拟了这个提案，可以在所有环境拿到`global`。

```
// CommonJS的写法
require('system.global/shim')();

// ES6模块的写法
import shim from 'system.global/shim'; shim();

```

上面代码可以保证各种环境里面，`global`对象都是存在的。

```
// CommonJS的写法
var global = require('system.global')();

// ES6模块的写法
import getGlobal from 'system.global';
const global = getGlobal();

```

上面代码将顶层对象放入变量`global`。