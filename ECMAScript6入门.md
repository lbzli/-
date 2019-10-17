# ECMAScript 6 入门读书笔记
## let和const命令
### 不存在变量提升
```javascript
// var 的情况
console.log(foo); // 输出undefined
var foo = 2;

// let 的情况
console.log(bar); // 报错ReferenceError
let bar = 2;
```
### 暂时性死区
ES6 明确规定，如果区块中存在let和const命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。

总之，在代码块内，使用let命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”（temporal dead zone，简称 TDZ）。

“暂时性死区”也意味着`typeof`不再是一个百分之百安全的操作。

*总之，暂时性死区的本质就是，只要一进入当前作用域，所要使用的变量就已经存在了，但是不可获取，只有等到声明变量的那一行代码出现，才可以获取和使用该变量。*

### 块级作用域与函数声明

考虑到环境导致的行为差异太大，应该避免在块级作用域内声明函数。如果确实需要，也应该写成函数表达式，而不是函数声明语句。
```javascript
// 块级作用域内部的函数声明语句，建议不要使用
{
  let a = 'secret';
  function f() {
    return a;
  }
}

// 块级作用域内部，优先使用函数表达式
{
  let a = 'secret';
  let f = function () {
    return a;
  };
}
```

## Set和Map的数据结构
### 使用Set来去除数组和字符串中的重复的值
```javascript
//去除数组的重复成员
[...new Set(array)]
//去除字符串的重复成员
[...new Set('ababbc)].join('')
//"abc"
```
### Set的属性和方法
```javascript
//属性
Set.prototype.constructor：构造函数，默认就是Set函数。
Set.prototype.size：返回Set实例的成员总数。

//实例方法
    //操作方法
    Set.prototype.add(value)：添加某个值，返回 Set 结构本身。
    Set.prototype.delete(value)：删除某个值，返回一个布尔值，表示删除是否成功。
    Set.prototype.has(value)：返回一个布尔值，表示该值是否为Set的成员。
    Set.prototype.clear()：清除所有成员，没有返回值。

    //遍历操作
    Set.prototype.keys()：返回键名的遍历器
    Set.prototype.values()：返回键值的遍历器
    Set.prototype.entries()：返回键值对的遍历器
    Set.prototype.forEach()：使用回调函数遍历每个成员

```

### Array.from方法可以将Set结构转为数组
这就提供了去除数组重复成员的另一种方法
```javascript
function dedupe(array) {
  return Array.from(new Set(array))
}
dedupe([1,1,2,3])   //[1,2,3]
```

### 使用 Set 可以很容易地实现并集（Union）、交集（Intersect）和差集（Difference）
要配合数组的filter方法

```javascript
let a = new Set([1, 2, 3]);
let b = new Set([4, 3, 2]);

// 并集
let union = new Set([...a, ...b]);
// Set {1, 2, 3, 4}

// 交集
let intersect = new Set([...a].filter(x => b.has(x)));
// set {2, 3}

// 差集
let difference = new Set([...a].filter(x => !b.has(x)));
// Set {1}
```

## 数组的扩展（重要）
### 扩展运算符   ...   
将一个数组转为用逗号分隔的***参数***序列
```javascript
console.log(...[1, 2, 3])
// 1 2 3

console.log(1, ...[2, 3, 4], 5)
// 1 2 3 4 5

[...document.querySelectorAll('div')]
// [<div>, <div>, <div>]
```
*注意...对document节点的运用*

### 扩展运算符主要用于函数调用
将一个数组转为用逗号分隔的***参数***序列
```javascript
function push(array, ...items) {
  array.push(...items);
}

function add(x, y) {
  return x + y;
}

const numbers = [4, 38];
add(...numbers) // 42
```

**注意，只有函数调用时，扩展运算符才可以放在圆括号中，否则会报错。**

### 代替函数的apply方法

由于扩展运算符可以展开数组，所以不再需要apply方法，将数组转为函数的参数了。
#### 由于 JavaScript 不提供求数组最大元素的函数，所以只能套用Math.max函数，将数组转为一个参数序列，然后求最大值。有了扩展运算符以后，就可以直接用Math.max了。
```javascript
// ES5 的写法
Math.max.apply(null, [14, 3, 77])

// ES6 的写法
Math.max(...[14, 3, 77])

// 等同于
Math.max(14, 3, 77);
```

### Array.from()
Array.from方法用于将两类对象转为真正的数组：
- 类似数组的对象（array-like object）
- 可遍历（iterable）的对象（包括 ES6 新增的数据结构 Set 和 Map）。

下面是一个**类似**数组的对象，Array.from将它转为真正的数组。
```javascript
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
实际应用中，常见的类似数组的对象是 DOM 操作返回的 NodeList 集合，以及函数内部的arguments对象。Array.from都可以将它们转为真正的数组。
```javascript
// NodeList对象
let ps = document.querySelectorAll('p');
Array.from(ps).filter(p => {
  return p.textContent.length > 100;
});

// arguments对象
function foo() {
  var args = Array.from(arguments);
  // ...
}
```
Array.from还可以接受第二个参数，作用类似于数组的map方法，用来对每个元素进行处理，将处理后的值放入返回的数组。

```javascript
Array.from(arrayLike, x => x * x);
// 等同于
Array.from(arrayLike).map(x => x * x);

Array.from([1, 2, 3], (x) => x * x)
// [1, 4, 9]
```

下面的例子是取出一组 DOM 节点的文本内容。
```javascript
let spans = document.querySelectorAll('span.name');

// map()
let names1 = Array.prototype.map.call(spans, s => s.textContent);

// Array.from()
let names2 = Array.from(spans, s => s.textContent)
```
下面的例子将数组中布尔值为false的成员转为0。
```javascript
Array.from([1, , 2, , 3], (n) => n || 0)
// [1, 0, 2, 0, 3]
```
另一个例子是返回各种数据的类型。
```javascript
function typesOf () {
  return Array.from(arguments, value => typeof value)
}
typesOf(null, [], NaN)
// ['object', 'object', 'number']
```
Array.from()可以将各种值转为真正的数组，并且还提供map功能。这实际上意味着，只要有一个原始的数据结构，你就可以先对它的值进行处理，然后转成规范的数组结构，进而就可以使用数量众多的数组方法。


```javascript
Array.from({ length: 2 }, () => 'jack')
// ['jack', 'jack']
```
上面代码中，Array.from的第一个参数指定了第二个参数运行的次数。这种特性可以让该方法的用法变得非常灵活。

### Array.of()
Array.of方法用于将一组值，转换为数组。

### 数组实例的 find() 和 findIndex()

数组实例的find方法，用于找出第一个符合条件的数组成员。它的参数是一个回调函数，所有数组成员依次执行该回调函数，直到找出第一个返回值为true的成员，然后返回该成员。如果没有符合条件的成员，则返回undefined。

```javascript
[1, 4, -5, 10].find((n) => n < 0)
// -5
```
上面代码找出数组中第一个小于 0 的成员。

### 数组实例的 fill()
fill方法使用给定值，填充一个数组。
```javascript
['a', 'b', 'c'].fill(7)
// [7, 7, 7]

new Array(3).fill(7)
// [7, 7, 7]
```
上面代码表明，fill方法用于空数组的初始化非常方便。数组中已有的元素，会被全部抹去。

## 扩展运算符的应用
### 复制数组
```javascript
// ES5
const a1 = [1, 2];
const a2 = a1.concat();

a2[0] = 2;
a1 // [1, 2]

// ES6
const a1 = [1, 2];
// 写法一
const a2 = [...a1];
// 写法二
const [...a2] = a1;
```

### 合并数组
```javascript
const arr1 = ['a', 'b'];
const arr2 = ['c'];
const arr3 = ['d', 'e'];

// ES5 的合并数组
arr1.concat(arr2, arr3);
// [ 'a', 'b', 'c', 'd', 'e' ]

// ES6 的合并数组
[...arr1, ...arr2, ...arr3]
// [ 'a', 'b', 'c', 'd', 'e' ]
```
不过，这两种方法都是浅拷贝，使用的时候需要注意。(如果元素是一个对象，修改了原数组的成员，会同步反映到新数组。)

### 与结构赋值结合
**扩展运算符可以与解构赋值结合起来，用于生成数组。**
```javascript
const [first, ...rest] = [1, 2, 3, 4, 5];
first // 1
rest  // [2, 3, 4, 5]

const [first, ...rest] = [];
first // undefined
rest  // []

const [first, ...rest] = ["foo"];
first  // "foo"
rest   // []
```
**如果将扩展运算符用于数组赋值，只能放在参数的最后一位，否则会报错。**
```javascript
const [...butLast, last] = [1, 2, 3, 4, 5];
// 报错

const [first, ...middle, last] = [1, 2, 3, 4, 5];
// 报错
```

### 字符串
扩展运算符还可以将字符串转为真正的数组。
```javascript
[...'hello']
// [ "h", "e", "l", "l", "o" ]
```
上面的写法，有一个重要的好处，那就是能够正确识别四个字节的 Unicode 字符。


### 实现了 Iterator 接口的对象
任何定义了遍历器（Iterator）接口的对象（参阅 Iterator 一章），都可以用扩展运算符转为真正的数组。
```javascript
let nodeList = document.querySelectorAll('div');
let array = [...nodeList];
```
上面代码中，querySelectorAll方法返回的是一个NodeList对象。它不是数组，而是一个类似数组的对象。这时，扩展运算符可以将其转为真正的数组，原因就在于NodeList对象实现了 Iterator 。

对于那些没有部署 Iterator 接口的类似数组的对象，扩展运算符就无法将其转为真正的数组。

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
上面代码中，arrayLike是一个类似数组的对象，但是没有部署 Iterator 接口，扩展运算符就会报错。这时，可以改为使用Array.from方法将arrayLike转为真正的数组。
### Map和Set结构，Generator函数

## Iterator 和for..of循环
一个数据结构只要部署了Symbol.iterator属性，就可以视为具有iterator接口，就可用for...of循环遍历它的成员，也就是说，for...of循环的内部调用的是数据结构的Symbol.iterator方法
for...of喧哗可以使用的范围包括数据，set和map结构类似数组的的对象（比如arguments对象，dom NODElist对象），后文的Genrator对象，以及字符串。

### for...of循环与其他的遍历语法的比较
以数组为例，javascript提供的多种遍历语法。最原始的写法就是
for循环
```javascript
for(var index = 0 ;index < myArray.length; index++) {
  console.log9myArray[index]);
}
```

foreach的缺点
1 foreach循环无法跳出循环，break命令或return命令都不能奏效

for...in 的缺点
1. 数组的键名
2. for...in循环不仅遍历数字的键名，还会遍历手动添加的其他的键，甚至包括原型链上的键
3. 某些情况下，for...in循环会以任意的顺序遍历键名。

总之，for...in循环主要是为了遍历对象而设计的，不适用于遍历数组。

for...of循环相比上面的几种做法，有一些显著的优点。

- 有着同for...in一样简洁的语法，但是没有for...in的那些缺点
- 不同于forEach犯法，他可以于break，continue和return配合使用。
- 提供了遍历数据结构的统一操作接口

## Generator
### Generator函数
调用Generator函数，会返回一个**遍历器对象**，代表Generator函数的内部的指针。以后每一个调用遍历器的next方法就会返回一个有着value和done两个属性的对象。value属性表示当前的内部的状态的值，是yield表达式后面的值；done属性是一个布尔值，表示是否遍历结束
。

### yield表达式
遍历器对象的next方法的运行逻辑如下：
1. 遇到yield表达式，就暂停执行后面的操作，并将紧跟在yield后面的那个表达式的值，转为返回对象对的那个value的属性值。
2. 下一次调用next方法时，在继续往下执行，知道遇到下一个yield表达式。
3. 如果没有在遇到新的yield表达式，就一直运行到函数结束，知道return语句为止，并将return语句后面的表达式的值，转为返回的对象的value的属性值。
4. 如果该函数没有return语句，则返回对象的value的属性值为undefined。

需要注意的是，yield表达式的后面的表达式，只有当调用next方法，内部指针指向该语句时才会执行，因此等于javascript提供了手动的“惰性求值”的语法功能。

