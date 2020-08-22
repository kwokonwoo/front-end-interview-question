**数组乱序**  

常见的思路是利用`random`方法获取随机正负数并通过`sort`函数进行排序：
```javascript
var array = [1, 2, 3, 4, 5];

array.sort(() => Math.random() - 0.5);
```
上面这个思路并不能真正的实现乱序排序，大致原因是因为`sort`函数的排序算法导致的。
ECMAScript关于`Array.prototype.sort(comparefn)`的标准中并没有规定具体的实现算法，而是作出了如下的说明：
> Calling comparefn(a,b) always returns the same value v when given a specific pair of values a and b as its two arguments.

也就是说，对同一组a、b的值，`comparefn(a, b)`需要返回相同的值。而`(a, b) => Math.random() - 0.5`显然不满足这个条件。

不同浏览器对`sort`函数的实现方式是不一样的。以chrome v8为例，v8在处理`sort`方法时使用了快速排序和插入排序两种排序方式。其中插入排序的源码是：
```javascript
function InsertionSort(a, from, to) {
  for (var i = from + 1; i < to; i++) {
    var element = a[i];
    for (var j = i - 1; j >= from; j--) {
      var tmp = a[j];
      var order = comparefn(tmp, element);
      if (order > 0) {
        a[j + 1] = tmp;
      } else {
          break;
      }
    }
    a[j + 1] = element;
  }
};
```
以数组`[1, 2, 3]`为例，分析`shuffle`函数的实现过程：

按照插入排序的原理，`insertionSort`函数的from值为0，to的值为3。数组的外层循环从`i = 1`开始，`a[i]`值为2，内层循环中`compare(1, 2)`的值等于`Math.random() - 0.5`，因此数组元素1和2有一半的概率进行交换。假设依然是`[1, 2, 3]`,接着遍历可得`i = 2`，`a[i]`的值为3，此时比较`compare(2,3)`有一半的概率是`[1, 2, 3]`，遍历结束。还有一半的概率是`[1, 3, 2]`，在这种情况下，还会进行遍历，结果可能是`[1, 3, 2]`和`[3, 1, 2]`。

综上，在数组`[1, 2, 3]`中，有50%的概率会变成`[1, 2, 3]`，有25%的概率会变成`[1, 3, 2]`和`[3, 1, 2]`。
另一种情况`[2, 1, 3]`类似，最终的结果如下表：

<table role="table">  
    <tbody><tr>
        <th>数组</th>
        <th>i = 1</th>
        <th>i = 2</th>
        <th>总计</th>
    </tr>  
    <tr>  
        <td rowspan="6">[1, 2, 3]</td>
        <td rowspan="3">50% [1, 2, 3]</td>
         <td>50% [1, 2, 3]</td>
         <td>25% [1, 2, 3]</td>
    </tr>
    <tr>  
        <td>25% [1, 3, 2]</td>
        <td>12.5% [1, 3, 2]</td>
    </tr>
    <tr>  
        <td>25% [3, 1, 2]</td>
        <td>12.5% [3, 1, 2]</td>
    </tr>  
    <tr>  
        <td rowspan="3">50% [2, 1, 3]</td>
        <td>50% [2, 1, 3]</td>
         <td>25% [2, 1, 3]</td>
    </tr>
    <tr>  
        <td>25% [2, 3, 1]</td>
        <td>12.5% [2, 3, 1]</td>
    </tr>
    <tr>  
        <td>25% [3, 2, 1]</td>
        <td>12.5% [3, 2, 1]</td>
    </tr>
</tbody></table>

此推算可以验证如下：
```javascript
var times= 100000;
var res = {};

for (var i = 0; i < times; i++) {
  var arr = [1, 2, 3];
  arr.sort(() => Math.random() - 0.5);
  
  var key = JSON.stringify(arr);
  res[key] ? res[key]++ : res[key] = 1;
}

for (var key in res) {
  res[key] = res[key] / times * 100 + '%'
}

console.log(res)
```
我们可以发现乱序后，`3`还在原位置的概率有50%。
根本原因就在于插入排序中，当待排序元素跟有序元素进行比较时，一旦确定了位置，就不会再跟前面的有序元素进行比较了，从而乱序不彻底。

实现真正的乱序需要用到**Fisher-Yates shuffle**算法。代码如下：
```javascript
function shuffle(array) {
  var m, temp, i;
  for (m = array.length; m; m--) {
    i = (Math.random() * m) >> 0; // 按位右移操作符>>后面的操作数是0的时候，结果和Math.floor()一样，向下取整，但速度更快。
    temp = array[m - 1];
    array[m - 1] = array[i];
    array[i] = temp;
  }
  return array;
}

// 其中for循环可以改成while循环：
  var m = array.length, temp, i;
  while (m) {
    // Pick a remaining element...
    i = Math.floor(Math.random() * m--);
    
    // And swap it with the current element.
    temp = array[m];
    array[m] = array[i];
    array[i] = temp;
  }
```
ES6写法如下：
```javascript
function shuffle(array) {
  for (let i = array.length - 1; i > 0; i--) {
    let j = Math.floor(Math.random() * (i + 1)); // random index from 0 to i
    // 解构赋值
    [array[i], array[j]] = [array[j], array[i]];
  }
}
```

这里我们采用类似上文测试数组`[1, 2, 3]`的方法测试Fisher-Yates shuffle算法：
```javascript
let count = {
  '123': 0,
  '132': 0,
  '213': 0,
  '231': 0,
  '321': 0,
  '312': 0
};

for (let i = 0; i < 1000000; i++) {
  let array = [1, 2, 3];
  shuffle(array);
  count[array.join('')++;
}

for (let key in count) {
  alert('${key}: ${count[key]}');
}
```
测试可得所有排列都是等概率出现，并且Fisher-Yates算法没有排序，性能更好。

补充一个在指定范围内生成随机数的方法：
```javascript
setRangeRandom(min: number, max: number) {
  let n = max - min;
  if (n == 0) {
    return max;
  } else if (n < 0) {
    [max, min] = [min, max];
    n = Math.abs(n);
  }
  
  return ((Math.random() * ++n) >> 0) + min; // ++作为前置递增时，先自增再赋值，且运算符优先级小于后置递增。
}
```

**关于Fisher-Yates算法随机性的数学归纳法证明**
对 n 个数进行随机：

1. 首先我们考虑 n = 2 的情况，根据算法，显然有 1/2 的概率两个数交换，有 1/2 的概率两个数不交换，因此对 n = 2 的情况，元素出现在每个位置的概率都是 1/2，满足随机性要求。

2. 假设有 i 个数， i >= 2 时，算法随机性符合要求，即每个数出现在 i 个位置上每个位置的概率都是 1/i。

3. 对于 i + 1 个数，按照我们的算法，在第一次循环时，每个数都有 1/(i+1) 的概率被交换到最末尾，所以每个元素出现在最末一位的概率都是 1/(i+1) 。而每个数也都有 i/(i+1) 的概率不被交换到最末尾，如果不被交换，从第二次循环开始还原成 i 个数随机，根据第二步的假设，它们出现在 i 个位置的概率是 1/i。因此每个数出现在前 i 位任意一位的概率是 (i/(i+1)) * (1/i) = 1/(i+1)，也是 1/(i+1)。

综上所述，对于任意 n >= 2，经过这个算法，每个元素出现在 n 个位置任意一个位置的概率都是 1/n。

举例：数组`[1, 2, 3, 4, 5, 6, 7, 8, 9]`, 在第一次循环中，每个数出现在末尾的概率都是1/9，并且**每个数不出现在末尾的概率是8/9**。如果不被交换到末尾，那么在第二次循环中，根据第二步的假设，它们出现在前8个位置每个位置的概率都是1/8，从而每个数出现在前8位任意一位的概率是：（出现在前8的概率）*(出现在前8任意一位的概率) = 8/9 * 1/8 = 1/9。

**参考链接**

[Shuffle an array](https://javascript.info/task/shuffle)

[Fisher–Yates Shuffle](https://bost.ocks.org/mike/shuffle/)

[JavaScript专题之乱序](https://github.com/mqyqingfeng/Blog/issues/51)

[数组的完全随机排列](https://h5jun.com/post/array-shuffle.html)

[前端面试(算法篇) - 数组乱序](https://www.cnblogs.com/wisewrong/p/10517532.html)

[关于 JavaScript 的数组随机排序](https://mp.weixin.qq.com/s/0j7iMJwaXYt3BD036M8s-w)
