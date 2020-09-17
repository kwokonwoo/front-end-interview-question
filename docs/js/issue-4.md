### `for...in`和`for...of`区别

最早的数组遍历方式：

```javascript
var array = ['a', 'b', 'c'];
for (var index = 0; index < array.length; index++) {
  console.log(array[index]);
}
```

自从ES5发布以后，可以用内建的`forEach`来遍历数组：

```javascript
var array = ['a', 'b', 'c'];
array.forEach(function(element) {
  console.log(element);
});
```
`forEach`的缺点是无法使用`break`退出循环，也不能使用`return`语句返回到外层。

还可以使用`for...in`循环来遍历数组：
```javascript
var array = ['a', 'b', 'c'];
for (var index in array) {
  console.log(array[index]);
  // console.log(typeof index);
}
```

`for...in`遍历属性`enumerable`属性为true的键，其中自定义属性的`enumerable`属性默认为true：
```javascript
let log = console.log;

let names = ["Maxon","Moureen", "Kaihura-nkuba"];

names.elf = "Lindolf";

Object.defineProperty(names, "ent", {value: "Treebard", enumerable: false});

log(names) // Output: ["Maxon", "Moureen", "Kaihura-nkuba", elf: "Lindolf", ent: "Treebard"]

for (prop in names) {
  log(prop); // Output: "0" ,"1" ,"2" ,"elf"
}

```

用`for...in`来遍历数组有以下缺点：

1. 数组的键名是数字，但是`for...in`循环是以字符串作为键名"0"、"1"、"2"等等。
2. 作用于数组的`for...in`循环除了遍历数组元素之外，还会遍历自定义属性，甚至访问数组原型链上的属性。
3. 某些情况下，可能按照任意顺序遍历数组。
4. 总之，`for...in`循环是为**普通对象**设计的，不适用于数组的遍历。推荐在循环对象属性的时候使用`for...in`，在遍历数组的时候使用`for...of`。

对于第2点缺点，可以通过`hasOwnProperty`避免引入原型对象的属性：
```javascript
Object.prototype.objCustom = function() {};
Object.prototype.arrCustom = function() {};

var arr = [3, 5, 7];
arr.foo = 'hello';

for (var i in arr) {
  console.log(i); // Output: 0, 1, 2, foo, arrCustom, objCustom
}

for (var i in arr) {
  if (arr.hasOwnProperty(i)) {
    console.log(i); // Output: 0 1 2 foo
  } // 数组本身的属性可以使用```forEach`去除。
}

for (var i of arr) {
  console.log(i); // Output: 3, 5, 7
} // `for...of`会忽略array中的不可迭代元属性`objCustom`、`arrCustom`和实例属性`foo`的值。
```

**正确遍历数组的方式**是使用ES6引入的`for...of`循环：
```javascript
var array = ['a', 'b', 'c'];
for (var value of array) {
  console.log(value);
}
```
此方法修复了`for...in`遍历数组的所有缺点，还可以正确的响应`break`,`continue`,`return`语句。

除此之外，`for...of`还支持大部分的类数组对象。但是值得注意的是：`for...of`语句不支持普通对象，如果想要迭代一个对象的属性，需要使用`for...in`语句或者内建的`Object.keys()`方法：

```javascript
var obj = {
  a: 1,
  b: [],
  c: function() {}
};
for (var key of Object.keys(obj)) {
  console.log(key);
} // 如果直接对原对象使用for...of将导致：Uncaught TypeError: obj is not iterable

//对于普通对象直接使用for...in语句更为简洁
for (var key in obj) {
  console.log(key);
}
```

#### 总结

- `for...in`循环遍历对象所有`enumerable`属性为true的**keys**。
- `for...of`循环遍历可迭代对象的**values**。所谓可迭代对象指的是原生具备Iterator接口的数据结构：
  - Array
  - Map
  - Set
  - String
  - TypedArray
  - 函数的arguments对象
  - NodeList对象

#### 参考链接

[Iterator 和 for...of 循环](https://es6.ruanyifeng.com/#docs/iterator)

[Iterable vs Enumerable in JavaScript](https://www.qandeelacademy.com/lesson/javascript-arrays-tutorial/HZjvoftRvGE#:~:text=Iterable%20and%20Enumerable%20are%20different,certain%20values%20from%20the%20object.)

[Javascript中的for-of循环](https://github.com/wujunchuan/wujunchuan.github.io/issues/11)

[为什么es6里的object不可迭代？](https://www.zhihu.com/question/50619539)
