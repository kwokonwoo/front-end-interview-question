- 写一个difference方法，用于比较两个数组，返回两数组中不同的部分，能实现多维数组的比较，能使用ES6的一些新特性来实现。
```javascript
// traverse
function diff(arr1, arr2) {
  var result = [];
  
  for (var i = 0; i < arr1.length; i++) {
    if (arr2.indexOf(arr1[i]) === -1) {
      result.push(arr[i]);
    }
  }
  
  for (var j = 0; j < arr2.length; j++) {
    if (arr1.indexOf(arr2[j] === -1) {
      result.push(arr2[j]);
    }
  }
  
  return result;
}
```

```javascript
// ES6
function diff(arr1, arr2) {
  return arr1.filter(e => arr2.indexOf(e) === -1).concat(arr2.filter(e => arr1.indexOf(e) === -1));
}
```

```javascript
// ES7 includes
function diff(arr1, arr2) {
  // 先合并，再filter
  // includes解决了利用indexOf会对数组中的NaN返回-1的问题
  return arr1.concat(arr2).filter(e => !arr1.includes(e) || !arr2.includes(e));
}
```
