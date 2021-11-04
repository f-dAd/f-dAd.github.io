title: eventloop
date: 2021-11-02 10:49:32
tags: 

  - js

categories:

- tools

---

```js
console.log('script start');

setTimeout(function () {
  console.log('setTimeout');
}, 0);

Promise.resolve()
  .then(function () {
    console.log('promise1');
  })
  .then(function () {
    console.log('promise2');
  });

console.log('script end');
 //script start, script end, promise1, promise2, setTimeout
```

需要理解 event loop 如何处理 tasks 和 microtasks.

每个 'thread' 有自己的 **event loop**, 所以每个web工作者有自己的envent loop, 所以它可以独立执行，然而同源的所有窗口共享同一个event loop并且它们可以同步通信. event loop是连续运行的,执行一些任务队列. 一个 event loop 有不同的 task sources保证执行顺序在 within that source (specs [such as IndexedDB](https://w3c.github.io/IndexedDB/#database-access-task-source) define their own), but the browser gets to pick which source to take a task from on each turn of the loop. This allows the browser to give preference to performance sensitive tasks such as user-input. Ok ok, stay with me…
