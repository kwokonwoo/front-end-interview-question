### 冒泡算法和快排算法

- 三行快排（非原地算法）
```js
function quickSort(nums) {
    if (nums.length === 0) return [];
    let start = nums.splice(0, 1)[0];
    return quickSort(nums.filter(num => num <= start)).concat([start], quickSort(nums.filter(num => num > start)));
}
```
