# Promise

## 1. 定义

Promise 是异步编程的一种解决方案，比传统的解决方法——回调函数和事件——更合理和更强大。

Promise，简而言之就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。从语法上说，Promise是一个对象，从它可以获取异步操作的结果。Promise提供统一的API，各种异步操作都可以用同样的方法进行处理。

## 2. 特点

- 对象的状态不受外界影响。`Promise` 对象代表一个异步操作，有三种状态：`pending`, `fulfilled`, `rejected`。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。
- 一旦状态改变，就不会再变，任何时候都可以得到这个结果。两种状态变化：从 `pending` 变为 `fulfilled` 和从 `pending` 变为 `rejected`。只要这两种情况发生，状态就凝固了，不会再变，会一直保持这个结果。如果改变已经发生了，你再对 `Promise` 对象添加回调函数，也会立即得到这个结果。这个与事件完全不同，事件的特点是，如果你错过了它，再去监听，是得不到结果的。
- `Promise` 也有一个缺点。无法取消Promise，一旦新建它就会立即执行，无法中途取消；其次，如果不设置回调函数，Promise内部抛出的错误，不会反应到外部；第三，当处于 `pending` 状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

## 3. 基本用法

ES6 规定，`Promise` 对象是一个构造函数，用来生成 `Promise` 实例。

```javascript
console.log('1');

const promise = new Promise(function (resolve, reject) {
    console.log('2');
    //todo
    if (/*异步操作成功*/) {
        resolve(`success`);
    } else {
        reject(`fail`);
    }
})

// 只用 then 捕获 `resolved` 和 `rejected` 状态的回调函数
promise.then(function (success) {
    console.log('4 - success');
    // success
}, function (error) {
    console.log('4 - error');
    // fail
})

// 用 then 捕获 `resolved` 状态的回调函数，catch 捕获 `rejected` 状态的回调函数
promise.then(function (success) {
    // success
}).catch(error) {
    // fail
}

console.log('3');

// 顺序执行，Promise会立即执行，回调函数最后输出
// 1 => 2 => 3 => 4
```

- `Promise` 构造函数接受一个函数作为参数，该函数的两个参数分别是`resolve`和`reject`，它们是两个函数，由 JavaScript 引擎提供，不用自己部署。
- `resolve` 函数的作用是将 Promise 对象的状态从 pending 变为 resolved，在异步操作成功时调用，并将异步操作的结果，作为参数传递出去。
- `reject` 函数的作用是将 Promise 对象的状态从 pending 变为 rejected，在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。
- `Promse` 实例生成后，可以使用 `then` 方法分别指定 `resolved` 和 `rejected` 状态的回调函数；也可以用 `then` 方法指定 `resolved` 状态的回调函数，用 `catch` 方法指定 `rejected` 状态的回调函数。

**注意：**一般来说，调用 `resolve` 或 `reject` 以后，Promise 的使命就完成了，后继操作应该放到 `then` 方法里面，而不应该直接写在 resolve 或 reject 的后面，最好在它们前面加上 `return` 语句。

## 4. Promise.prototype.then()

`then` 方法的作用是为 Promise 实例添加状态改变时的回调函数。

`then` 方法的第一个参数是 `resolved` 状态的回调函数，第二个参数是可选的，是 `rejected` 状态的回调函数。

`then` 方法返回的是一个新的 `Promise` 实例，注意不是原来那个 Promise 实例。因此可以采用链式写法，即 then 方法后面再调用另一个 then 方法。

采用链式的 `then`，可以指定一组按照次序调用的回调函数。此时，前一个回调函数，有可能返回的还是一个 `Promise` 对象（即有异步操作），这时后一个回调函数，就会等待该 `Promise` 对象的状态发生变化，才会被调用。

## 5. Promise.prototype.catch()

`Promise.prototype.catch` 方法是 `.then(null, rejection)` 或 `.then(undefined, rejection)` 的别名，用于指定发生错误时的回调函数。

`catch` 方法可以捕获 `then` 方法的错误。

Promise 对象的错误具有“冒泡”性质，会一直向后传递，直到被捕获为止。

一般来讲，不要在 `then` 方法里面定义 `reject` 状态的回调函数，即then的第二个参数，用 `catch` 方法捕获错误。

跟传统的 `try/catch` 代码块不同的是，如果没有使用 catch 方法指定错误处理的回调函数，Promise 对象抛出的错误不会传递到外层代码，即不会有任何反应。

```javascript
promise
    .then(function (data) {
        //success
    })
    .then(function (data) {
        //success
    })
    .catch(function (error) {
        //error
    });

// catch总会捕获前面抛出的错误，包括then抛出的。
```

## 5. Promise.prototype.finally()

`finally` 方法用于指定不管 Promise 对象最后状态如何，都会执行的操作。该方法是 ES2018 引入标准的。

`finally` 方法的回调函数不接受任何参数，这意味着没有办法知道，前面的 Promise 状态到底是 `fulfilled` 还是 `rejected`。这表明，`finally` 方法里面的操作，应该是与状态无关的，不依赖于 Promise 的执行结果。

```javascript
promise
.then( result => {/*todo*/})
.catch( error => {/*todo*/})
.finally( ()=> {/*todo*/});
```