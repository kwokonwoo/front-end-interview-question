**数组乱序**  

常见的思路是利用`random`方法获取随机正负数并通过`sort`函数进行排序：
```javascript
function shuffle(array) {
  array.sort(() => Math.random() - 0.5);
}
```
ECMAScript只规定了`sort`函数的效果，但是不同浏览器的实现方式是不一样的。以v8为例，v8在处理`sort`方法时使用了快速排序和插入排序两种排序方式。其中插入排序的源码是：
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



**参考链接**

[Shuffle an array](https://javascript.info/task/shuffle)
