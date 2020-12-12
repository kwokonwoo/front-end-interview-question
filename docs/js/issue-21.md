获取页面所有元素并统计每个标签的个数
```js
let tagNames = document.getElementsByTagName('*');

let tagCount = {};

for (let i = 0; i < tagNames.length; i++) {
  let tagName = tagNames[i].tagName.toLowerCase();
  if (!tagCount[tagName]{
    tagCount[tagName] = 1;
  } else {
    tagCount[tagName]++;
  }
}
console.log(tagCount);
```
