JavaScript有一个基于**事件循环**的并发模型，事件循环负责执行代码、收集和处理事件以及执行队列中的子任务。

<img src="https://mdn.mozillademos.org/files/17124/The_Javascript_Runtime_Environment_Example.svg" alt="事件循环可视化描述"/>

#### 栈
**函数调用**形成了一个由若干帧组成的栈。
```javascript
function foo(b) {
  let a = 10;
  return a + b + 11;
}

function bar(x) {
  let y = 3;
  return foo(x * y);
}

console.log(bar(7)); // 返回42
```
#### 队列
JavaScript运行时候包含了一个待处理消息的消息队列。每个消息关联着一个处理此消息的回调函数。
在事件循环期间的某个时刻，运行时会从最先进入队列的消息开始处理队列中的消息。被处理的消息会被移出队列，并作为输入参数来调用与之关联的函数。如前所提，调用函数会为其创造一个新的帧栈。

函数的处理会一直进行到执行栈再次为空为止；然后事件循环将会处理队列中下一个消息。
