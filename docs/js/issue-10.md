# 用`reduce`实现`map`

```javascript
if (!Array.prototype.mapUsingReduce) {
  Array.prototype.mapUsingReduce = function(callback, thisArg) {
    return this.reduce(function(mappedArray, currentValue, index, array) {
      mappedArray[index] = callback.call(thisArg, currentValue, index, array)
      return mappedArray
    }, [])
  }
}

[1, 2, , 3].mapUsingReduce(
  (currentValue, index, array) => currentValue + index + array.length
) // [5, 7, , 10]
```

## 利用箭头函数和展开语法实现`map`
```javascript
function map(arr, fn) {
  return arr.reduce((acc, item) => [...acc, fn(item)], [])
}
```
首先将空数组作为初始值，然后在每次迭代中，通过展开语法将这个数组和调用一次提供的函数后的返回值连接起来。

## 利用箭头函数和三元运算符实现`filter`
```javascript
function filter(arr, fn) {
  return arr.reduce((acc, item) => fn(item) ? [...acc.item] : acc, [])
}
```

## 利用箭头函数和三元运算符实现`find`
```javascript
function find(arr, fn) {
  return arr.reduce((acc, item) => fn(item) ? acc || item : acc, undefined)
}
```
使用`或`运算是为了确保一旦找到满足条件的元素则将其“挂起”，而不是被后来同样满足条件的元素覆写。但因此也导致此实现不够精确，因为像`0`这样的`falsy`值返回的是最后一个匹配而不是第一个匹配。

**参考链接**

[Array.prototype.reduce()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)

[Implementing map, filter and find with reduce in JavaScript](https://maurobringolf.ch/2017/06/implementing-map-filter-and-find-with-reduce-in-javascript/)
