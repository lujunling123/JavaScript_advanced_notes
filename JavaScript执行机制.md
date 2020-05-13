### 进程与线程 ###

计算机的核心是CPU，他承担了所有的计算任务，对于操作系统来说，一个任务就是一个进程，就比如打开一个浏览器，CPU就会分配一块资源给浏览器，也就是就启动了一个进程，一个进程中至少有一个线程，线程则是在浏览器进程中竞争被调用完成它的执行任务，进程中的资源是共享的，每个线程都可以使用这些内存,而单线程就是同一时间只能做一件事，单线程就意味着所有任务要排队，前一个任务结束，才会执行后一个任务。

### 为什么需要异步 ###

但如果像ajax读取数据耗时很长，后一个任务就要一直等着，后面的代码就会被阻塞，影响了用户体验。

### JavaScript事件循环 （event loop） ###

* 当任务进入执行栈的时候就会判断是同步任务还是异步任务，如果是同步就进入主线程执行，异步就进入Event Table 进行注册
* 当指定事件完成后，Event Table将这个函数移入到事件队列
* 主线程任务执行完毕以后，去任务队列读取函数进入主线程
* 依次不断重复，构成了事件循环

除了广义的同步任务和异步任务，还有宏任务和微任务

macro-task（宏任务）：script，setTimeout，selInterval，setImmediate，requestAnimationFrame，I/O

micro-task（微任务）： Promise，process.nextTick，MutationObserver

```
// 以宏任务开始循环（宏任务一）

console.log( 111)
setTimeout(()=>{ console.log( 222 ) }, 0)     // 宏任务二
setTimeout(()=>{ console.log( 2221 ) }, 10)   // 宏任务四

new Promise( function(resolve, reject){

  console.log(333)
  resolve()
  console.log(444)

}).then(function(){                           // 微任务1
  console.log(555)
})

setTimeout(() => {								            // 宏任务二
    console.log(666);
    Promise.resolve().then(() => {            // 宏任务二 --> 微任务一
        console.log(777);
        Promise.resolve().then(() => {        // 宏任务二 --> 微任务二
            console.log(888);
        })
        console.log(999)
    })
    setTimeout(() => console.log(1001), 10);  
    setTimeout(() => console.log(100), 0);  
}, 0);
setTimeout(()=>{ console.log( 101 ) }, 0 )    // 宏任务三
setTimeout(()=>{ console.log( 102 ) }, 10 )   // 宏任务五

Promise.resolve().then(() => {                // 微任务2
    console.log(103);
})

new Promise( function(resolve, reject){
  
  setTimeout(()=>{ console.log( 104 ) }, 10 )
  resolve()
  setTimeout(()=>{ console.log( 1041 ) }, 100 )

}).then(function(){
  console.log(105)                            // 微任务3
  setTimeout(()=>{ console.log( 1051 ) }, 0 )
})

结果：
VM940:1 111
VM940:7 333
VM940:9 444
VM940:12 555
VM940:31 103
VM940:41 105
VM940:2 222
VM940:16 666
VM940:18 777
VM940:22 999
VM940:20 888
VM940:27 101
VM940:42 1051
VM940:3 2221
VM940:28 102
VM940:36 104
VM940:25 100
VM940:24 1001
VM940:38 1041

```

[](https://www.cnblogs.com/fangdongdemao/p/10262209.html "")


### promise相关 ###

Promise是一个构造函数，Promise 对象代表一个异步操作，有三种状态：Pending（进行中）、Resolved（已完成，又称 Fulfilled）和 Rejected（已失败），resolve,reject两个函数最为参数传递给执行函数，当resolve调用时状态改为完成，reject调用时状态更改为失败

![promise.png](https://github.com/lujunling123/JavaScript_advanced_notes/blob/master/img/promise.png "promise")

1. 基本api
Promise.resolve()
Promise.reject()
Promise.all()
Promise.race()
Promise.prototype.then()
Promise.prototype.catch()
Promise.prototype.finally()

2. promise是如何实现的
promise是一个构造函数

class Promise {

}


.then()方法反回了一个新的promise
// 待更新


