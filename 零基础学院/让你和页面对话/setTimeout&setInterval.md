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

#### 为什么不推荐使用 `setInterval` 函数
在制作动画时推荐使用 `setTimeout` 的递归调用模拟 `setInterval`函数用来代某些浏览器环境下不支持的 `window.requestAnimationFrame(fun)` 函数。

目前可寻的理由是：

1. `setInterval` 无视代码错误。 持续不断地调用该代码
2. `setInterval` 无视网络延迟(这个场景是深有体会)。比如：在轮询 **AJAX** 时会不顾服务器过载、临时断网、流量剧增、用户带宽受限，等等原因儿坚持不断的请求，从而造成客户端网络队列会塞满 **AJAX** 调用
3. `setInterval` 不保证执行。你调用的函数需要花很长时间才能完成，那某些调用会被直接忽略

***

⚠️ **这个两函数要注意一点的是， setTimeout 和 setInterval 这两个方法是异步函数，参数中设置的延迟时间并不会严格的遵守延迟时间来执行函数，请参考 js 的 event loop 的执行机制：**

JS的执行机制是：

1. 首先判断JS是同步还是异步,同步就进入主线程,异步就进入event table
2. 异步任务在event table中注册函数,当满足触发条件后,被推入event queue
3. 同步任务进入主线程后一直执行,直到主线程空闲时,才会去event queue中查看是否有可执行的异步任务,如果有就推入主线程中

以上三步循环执行,这就是event loop

这样就能理解为什么说 `setTimeout` 函数的延迟时间并不是从函数执行后就开始算起的，所以这个时间并不是准确的。