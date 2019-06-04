### 这个作业主要是练习 `setTimeout` 和 `setInterval` 这两个方法

这两个方法的用法很相近，就是作用不同。  
`setTimeout` 作用是多久时间后执行回调函数，只调用一次； 
`setInterval`作用是每隔多久时间调用一次回调函数，除非清除 timer 否则将永远循环调下去；

***

#### `setTimeout` 的具体用法

`setTimeout(code, time)` 有三个参数
- `code` : 有三种传参方式：
    - 直接传函数名，比如 `funName`
    - 直接写成匿名函数形式 比如 `()=>{}/function(){}`
    - 最后这个不推荐 写函数名➕()，再用引号包裹起来，比如 `"funName()"` 因为这种会在全局作用域下查找函数，有时候有问题。
- `time` : 时间，单位是 ms，比如 `setTimeout(()=>{...}, 1000)`，时间后面不需要单位名称。
- 第三个参数可以有，第三个参数作为第一个参数（函数）的参数，非常不常用，而且不直观。

`setTimeout` 方法会返回一个唯一的 ID，使用 `clearTimeout(ID)` 这个函数的化会清除调返回该 ID 的 `setTimeout` 方法。同理 `setInterval` 也有自己的清除函数 `clearInterval(ID)` 。具体用法非常简单：

```js
// 讲 ID 缓存在 变量中
var timerId = setTimeout(()=>{...}, 1000);
// 满足某条件时，取消延迟执行函数
if(condition){
    clearTimeout(timerId);
}
```

***

### 两个函数的异步执行机制

⚠️ **这个两函数要注意一点的是， setTimeout 和 setInterval 这两个方法是异步函数，参数中设置的延迟时间并不会严格的遵守延迟时间来执行函数，请参考 js 的 event loop 的执行机制：**

一下内容参照  **掘金** 大哥 **ssssyoki** 在2017年写的一篇 ➡️《这一次，彻底弄懂 JavaScript 执行机制》，写的很容易读懂。

原文传送门：[https://juejin.im/post/59e85eebf265da430d571f89#heading-2](https://juejin.im/post/59e85eebf265da430d571f89#heading-2)，👈戳一下进原文。

JS的执行机制是：

1. 首先判断JS是同步还是异步,同步就进入主线程,异步就进入event table
2. 异步任务在event table中注册函数,当满足触发条件后,被推入event queue
3. 同步任务进入主线程后一直执行,直到主线程空闲时,才会去event queue中查看是否有可执行的异步任务,如果有就推入主线程中

以上三步循环执行,这就是event loop

这样就能理解为什么说 `setTimeout` 函数的延迟时间并不是从函数执行后就开始算起的，所以这个时间并不是准确的。

看这样一段代码：

```js
setTimeout(() => {
    task()
},3000)

sleep(10000000)
```
这段代码的执行顺序是：
- `task()`进入Event Table并注册,计时开始。
- 执行`sleep`函数，很慢，非常慢，计时仍在继续。
- 3秒到了，计时事件timeout完成，`task()`进入Event Queue，但是`sleep`也太慢了吧，还没执行完，只好等着。
- `sleep`终于执行完了，`task()`终于从Event Queue进入了主线程执行。

上述的流程走完，我们知道`setTimeout`这个函数，是经过指定时间后，把要执行的任务(本例中为`task()`)加入到Event Queue中，又因为是单线程任务要一个一个执行，如果前面的任务需要的时间太久，那么只能等着，导致真正的延迟时间远远大于3秒。

我们还经常遇到`setTimeout(fn,0)`这样的代码，0秒后执行又是什么意思呢？是不是可以立即执行呢？

答案是不会的，`setTimeout(fn,0)`的含义是，指定某个任务在主线程最早可得的空闲时间执行，意思就是不用再等多少秒了，只要主线程执行栈内的同步任务全部执行完成，栈为空就马上执行。

**⚠️关于setTimeout要补充的是，即便主线程为空，0毫秒实际上也是达不到的。HTML5标准规定了setTimeout()的第二个参数的最小值（最短间隔），不得低于4毫秒，如果低于这个值，就会自动增加。**

上面说完了`setTimeout`，当然不能错过它的孪生兄弟`setInterval`。他俩差不多，只不过后者是循环的执行。对于执行顺序来说，`setInterval`会每隔指定的时间将注册的函数置入Event Queue，如果前面的任务耗时太久，那么同样需要等待。

唯一需要注意的一点是，对于`setInterval(fn,ms)`来说，我们已经知道不是每过`ms`秒会执行一次`fn`，而是每过`ms`秒，会有`fn`进入Event Queue。一旦`setInterval`的回调函数`fn`执行时间超过了延迟时间`ms`，那么就完全看不出来有时间间隔了。这句话请读者仔细品味。

***

#### 关于动画

对于那些DOM的变动（尤其是涉及页面重新渲染的部分），通常不会立即执行，而是每16毫秒执行一次。这时使用requestAnimationFrame()的效果要好于setTimeout()。
