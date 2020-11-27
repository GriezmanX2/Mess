## 原始（Primitive）类型

JS中存在六种原始变量类型，分别是：
+ Number
+ String
+ Boolean
+ null
+ undefined
+ Symbol(ES6新增)

原始类型的变量在调用函数时，会被隐式的转化为对象类型，然后调用函数。

## 对象（Object）类型

除了原始类型以外的其他类型都属于对象类型（比如：数组以及函数）。与原始类型所不同的是，原始类型存储的是值，对象类型存储的是地址（指针）。这意味着，当把一个对象a赋值给对象b后，对b的属性的修改也会导致a的改变。

## typeof 

typeof对于原始类型来说，除了null都可以显示正确的类型。
```js
  typeof 1 //'number'
  typeof '1'  //'string'
  typeof true //'boolean'
  typeof undefined //'undefined'
  typeof Symbol //'Symbol'
  typeof null //'object'
```

对于对象类型来说,除了函数都会显示object，所以说typeof并不能准确判断变量到底是什么类型
```js
  typeof [] //'object'
  typeof {} //'object'
  typeof function(){} //'function'
```

如果想准确的判断变量类型可以通过 ___Object.prototype.toString.call(变量)___ 函数得到。
```js
Object.prototype.toString.call(1)
"[object Number]"
Object.prototype.toString.call('1')
"[object String]"
Object.prototype.toString.call(true)
"[object Boolean]"
Object.prototype.toString.call(undefined)
"[object Undefined]"
Object.prototype.toString.call(null)
"[object Null]"
Object.prototype.toString.call(Symbol())
"[object Symbol]"
Object.prototype.toString.call([])
"[object Array]"
Object.prototype.toString.call(function(){})
"[object Function]"
Object.prototype.toString.call({})
"[object Object]"
```
---
## 类型转换

在JS中类型转换只有三种情况，分别是
+ 转换为布尔值
+ 转换为数字
+ 转换为字符串

| 原始值      | 转换目标 | 结果 |
| ----------- | ----------- | --- |
| number      | 布尔值       | 除0（包含+0，-0）和NaN意外都为true    |
| string | 布尔值 | 除了空字符串''以外都为true |
| undefined、null | 布尔值 | false |
| 引用类型 | 布尔值 | true | 
| number,boolean | 字符串 | 引号包裹(5 => '5'，true => 'true') |
| function | 字符串 | 函数声明的文本("function convert(){console.log('转化为字符串')}") |
| Symbol | 字符串 | Symbol() => 'Symbol()'|
| 数组 | 字符串 | [1,2] => '1,2' |
| 对象 | 字符串 | '[Object Object]' | 
| string | 数字 | 只包含数字的字符串直接转化为对应的数字，空字符串''转化为0，其他转化为NaN |
| boolean | 数字 | true => 1，false => 0
| null | 数字 | 0 |
| undefined | 数字 | NaN |
| 数组 | 数字 | 空数组转化为0,只存在一个数字元素或一个只包含数字的字符串元素的转化为该数字，其他为NaN |
| 除数组以外的对象类型 | 数字 | NaN | 
| Symbol | 数字 | 报错 |

## 对象转原始类型

对象在转换时，会调用内置的[[ToPrimitive]]函数，该概述的算法逻辑如下：
+ 如果已经是原始类型，就不需要转换了
+ 如果需要转字符串就调用toString方法，转换为基础类型的话就返回转换的值。不是字符串类型的话就先调用valueOf，结果不是基础类型的话在调用toString
+ 调用valueOf，如果转换为基础类型，就返回转化的值
+ 如果都没有返回原始类型，报错。

可以重写Symbol.toPrimitive,该方法在转原始类型时调用优先级最高。

```js
let a = {
  valueOf() {
    return 0
  },
  toString() {
    return '1'
  },
  [Symbol.toPrimitive]() {
    return 2
  }
}
1 + a // => 3
```

> valueOf()方法返回指定对象的原始值。
  默认情况下，valueOf方法由Object下的每个对象继承。每个内置的核心对象都会覆盖此方法以返回适当的值。如果对象没有原始值，则返回对象本身。JS的愈多内置对象都重写了该方法，以实现更适合自身的功能需要。因此，不同对象类型的返回值和返回值类型均可能不同。

  | 对象类型 | 返回值 |
  | --- | --- |
  | Array | 数组对象本身 |
  | Boolean | 布尔值 |
  | Date | 从1970.1.1 00:00:00到对应时间点的毫秒数 |
  | Function | 方法本身 |
  | Number | 数字值 |
  | Object | 对象本身（默认情况）|
  | String | 字符串值 |
  |        | Math和Error对象没有valueOf方法 | 

```js
// Array：返回数组对象本身
var array = ["ABC", true, 12, -5];
console.log(array.valueOf() === array);   // true

// Date：当前时间距1970年1月1日午夜的毫秒数
var date = new Date(2013, 7, 18, 23, 11, 59, 230);
console.log(date.valueOf());   // 1376838719230

// Number：返回数字值
var num =  15.26540;
console.log(num.valueOf());   // 15.2654

// 布尔：返回布尔值true或false
var bool = true;
console.log(bool.valueOf() === bool);   // true

// new一个Boolean对象
var newBool = new Boolean(true);
// valueOf()返回的是true，两者的值相等
console.log(newBool.valueOf() == newBool);   // true
// 但是不全等，两者类型不相等，前者是boolean类型，后者是object类型
console.log(newBool.valueOf() === newBool);   // false

// Function：返回函数本身
function foo(){}
console.log( foo.valueOf() === foo );   // true
var foo2 =  new Function("x", "y", "return x + y;");
console.log( foo2.valueOf() );
/*
ƒ anonymous(x,y
) {
return x + y;
}
*/

// Object：返回对象本身
var obj = {name: "张三", age: 18};
console.log( obj.valueOf() === obj );   // true

// String：返回字符串值
var str = "http://www.xyz.com";
console.log( str.valueOf() === str );   // true

// new一个字符串对象
var str2 = new String("http://www.xyz.com");
// 两者的值相等，但不全等，因为类型不同，前者为string类型，后者为object类型
console.log( str2.valueOf() === str2 );   // false
```

> toString()返回一个表示该对象的字符串。每个对象都有一个toString()方法，当该对象呗表示为一个文本值时，或者一个对象以预期的字符串方式引用时自动调用。默认情况下，toString()方法被每个Object对象继承。如果此方法在自定义对象中未被覆盖,toString()返回"[object Type]"，其中Type是对象的类型。

## this
```js
function foo() {
  console.log(this.a)
}
var a = 1
foo()

const obj = {
  a: 2,
  foo: foo
}
obj.foo()

const c = new foo()
```
+ 直接调用foo，不管foo函数被放在什么地方，this一定是window
+ 对于obj.foo()来说，谁调用了方法，this就是谁，所以obj.foo()中this就是obj对象
+ 对于new方式来说，this被永远绑定在构造函数返回的对象上，不会被任何方式改变。在以上例子中就是被绑定在了c上。
+ 箭头函数不会改变this的指向。

## ==与===
> 使用===对比时，如果对比双方的类型和值都一样返回true，否则返回false。
> 使用==对比时，如果对比双方的类型不一样的话，就会进行类型转化。
对比流程如下:
1. 首先判断双方类型是否相同，如果相同直接对比大小
2. 类型不同时，就会进行类型转换
3. 判断是否在对比null和undefined，是的话就返回true。如果只有其中一方为null或者undefined返回false
4. 判断两者类型是否都是原始类型，是的话就转换为数字再进行比较
5. 判断是否是对象类型与原始类型比较，如果是，就将对象类型转换为数字再进行比较

## 闭包

> 函数A内部有一个函数B，函数B可以访问到函数A中的变量，那么函数B就是闭包。
```js
function A() {
  let a = 1
  window.B = function () {
      console.log(a)
  }
}
A()
B() // 1
```

问题:
```js
for (var i = 1; i <= 5; i++) {
  setTimeout(function timer() {
    console.log(i)
  }, i * 1000)
}
// 6 6 6 6 6

// 通过闭包解决
for (var i = 1; i <= 5; i++) {
  (function(i){
    setTimeout(function timer() {
      console.log(i)
    }, i * 1000)
  })(i)
}
// 1 2 3 4 5

// 使用setTimeout传参
for (var i = 1; i <= 5; i++) {
  setTimeout(function timer(i) {
    console.log(i)
  }, i * 1000, i)
}
// 1 2 3 4 5

// 使用let
for (let i = 1; i <= 5; i++) {
  setTimeout(function timer() {
    console.log(i)
  }, i * 1000)
}
// 1 2 3 4 5
```

## 深浅拷贝

由于对象类型在赋值过程中其实是复制了地址，因此会导致改变了一个会影响多个对象的情况。通常在开发中都希望能够避免这种问题，我们可以通过深浅拷贝来解决。

浅拷贝：会拷贝原对象所有属性值到新的对象中，如果属性值是对象的话，拷贝的是地址。
+ Object.assgin(target,...source)
  ```js
  let a = {
    age: 1
  }
  let b = Object.assign({}, a)
  a.age = 2
  console.log(b.age) // 1
+ 展开运算符...
  ```js
  let a = {
    age: 1
  }
  let b = { ...a }
  a.age = 2
  console.log(b.age) // 1
  ```

深拷贝:浅拷贝只解决了第一层问题，如果属性值当中有对象的话，又会出现两者共享同一个对象地址的问题。
+ JSON.parse(JSON.stringify(object))
  ```js
  let a = {
    age: 1,
    jobs: {
      first: 'FE'
    }
  }
  let b = JSON.parse(JSON.stringify(a))
  a.jobs.first = 'native'
  console.log(b.jobs.first) // FE
  ```
  此方法存在一些缺点：
    + 会忽略undefined
    + 会忽略symbol
    + 不能序列化函数
    + 不能解决循环引用的对象
+ MessageChannel，如果所拷贝的对象含有内置类型并且不包含函数，可以使用此方法
  ```js
  function structuralClone(obj) {
    return new Promise(resolve => {
      const { port1, port2 } = new MessageChannel()
      port2.onmessage = ev => resolve(ev.data)
      port1.postMessage(obj)
    })
  }

  var obj = {
    a: 1,
    b: {
      c: 2
    }
  }

  obj.b.d = obj.b

  // 注意该方法是异步的
  // 可以处理 undefined 和循环引用对象
  const test = async () => {
    const clone = await structuralClone(obj)
    console.log(clone)
  }
  test()
  ```
+ lodash 的深拷贝函数

## 原型
+ 可以通过非标准属性__proto__来获取
+ 推荐使用Object.getPrototypeOf()或Reflect.getPrototypeOf(),因为非标准属性意味着未来可能会修改或移出该属性
+

## var、let、const
```js
console.log(a) // undefined
var a = 1

var a
console.log(a) // undefined
a = 1
```
从上述代码中可以发现，虽然变量还没有被声明，但我们却可以使用这个未声明的变量，这种情况就叫做提升(hoisting)，提升的是变量的声明。以上两端代码等效。

其实不仅var声明的变量会发生提升，函数也会被提升，并且优先于变量提升。
```js
console.log(a) // ƒ a() {}
function a() {}
var a = 1
```

另外var声明的变量会作为属性挂载到window上，let、const不会。并且let、const会产生暂时性死区，以至于声明的变量不能在声明前使用。

## 模块化
  使用模块化可以给我们带来以下好处
  + 解决命名冲突
  + 提供复用性
  + 提高代码可维护性

  > 立即执行函数

  在早期，使用立即执行函数实现模块化是常见的手段，通过函数作用域解决了命名冲突、污染全局作用域的问题。
    
  ```js
  (function(globalVariable){
    globalVariable.test = function() {}
    // ... 声明各种变量、函数都不会污染全局作用域
  })(globalVariable)
  ```

  > AMD和CMD
  
  鉴于目前这两种实现方式已经很少见到，所以不再对具体特性细聊，只需要了解这两者是如何使用的。

  ```js
  // AMD
  define(['./a','./b'],function(a,b){
    // 加载模块完毕可以使用
    a.do();
    b.do();
  });

  // CMD
  define(function(require,exports,module){
    // 加载模块
    // 可以把require卸载函数体的任意地方实现延迟加载
    var a = require('./a');
    a.do();
  });
  ```
  > CommonJS

  CommonJS最早是在Node中使用，目前也仍然广泛使用，比如在Webpack中你就能见到它，当然目前在Node中的模块管理已经和CommonJS有一些区别了。

  ```js
  // a.js
  module.exports = {
    a: 1
  };

  // or
  exports.a = 1;

  // b.js
  var module = require('./a.js');
  module.a // -> 1
  ```

