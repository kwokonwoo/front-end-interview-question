### 为什么0.1 + 0.3为false，如何解决？

`+(0.1 + 0.2).toFixed(1); // 0.3`

```javascript
function strip(number) {
  return (parseFloat(number.toPrecision(12)));
}
strip(0.1 + 0.2); // 0.3
```

```javascript
function isEqual(a, b) {
  return Math.abs(a -b) < Number.EPSILON;
}
isEqual(0.1+0.2, 0.3);
```
