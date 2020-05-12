### this原理 ### 
（[阮一峰](http://www.ruanyifeng.com/blog/2018/06/javascript-this.html ""),根据阮一峰JavaScript 的 this 原理）
```
var obj = {
  foo: function() {
    console.log(this)
  }
}
var foo = obj.foo

obj.foo()
foo()

打印：
{foo: ƒ}
VM281:3 Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, parent: Window, …}

// 虽然都是指向foo()函数，但this指向却不一样。
```
> 为什么obj.foo() 是在obj局部变量，一旦var foo = obj.foo,foo() 就变成全局变量

> 内存的数据结构

`var obj = { foo: 5 }`

> 将{ foo: 5 } 赋值给obj，JavaScript引擎会将{ foo: 5 }存在堆内存中，然后将内存地址赋值给栈内存中的obj

> 堆内存 ：存储的是引用数据类型，比如 Object, Function, Array

> 栈内存 ：存储的是基本数据的变量和对象的引用


|栈内存 | 指针| 堆内存|
|:-|:-:|-:|
|obj|——>|{ foo: 5 }|

> 也就是 变量obj是一个地址。要读取 obj.foo,引擎先从 obj 拿到内存地址，然后再从该地址读出原始的对象，返回它的foo属性。原始对象以字典的结构保存，每个属性名都对应一个属性描述对象。

|栈内存 | 指针| 堆内存 -> |（存储方式）|
|:-|:-:|:-:|:-|
| 如果是 { foo: 5 } |
|obj|——>| foo -> | [[value]]: 5<br> [[writable]]: true<br> [[enumerable]]: true<br> [[configurble]]: true |
| 如果是 { foo: function () {} } |
|obj|——>| foo -> | [[value]]: 函数的内存地址<br> [[writable]]: true<br> [[enumerable]]: true<br> [[configurble]]: true |

如果{ foo: function() {} }属性的值是一个函数,引擎会将函数单独保存在内存中，然后再将函数的地址赋值给foo属性的value属性。

obj.foo()是通过obj的内存地址找到obj对象里面的foo，所以在obj对象的环境执行，而var foo1 = obj.foo 中foo1 的值是通过obj的内存地址找到foo()又通过foo()的内存地址找到的foo函数本身并赋值给foo1,所以foo1实际指向foo函数本身,foo1就变成了当前环境执行，而JavaScript允许在函数体内部，引用当前环境的其他变量

### 构造函数的this ###

> 构造函数也是函数，当通过new 关键字调 实际是在堆内存中重新创建了一个A函数对象，并将它的值给到它的实例

当执行 var a = new A()

|栈内存 | 指针| 堆内存|
|:-|:-:|:-:|
|a|->| 新的 A() 函数 |

所以当前构造函数的this指向了实例 a

### 改变this指向 ###
1. 使用 call和apply
如果想把this的值从一个环境 传到另一个环境，可以使用call和apply

class Person {
  constructor(name){
    this.name = name
  }
  sayHello (){
    return this.name + 'Hello'
  }
}

var p = new Person('张三')

var lisi = {
  name: '李思'
}
p.__proto__.sayHello.apply(lisi) // "李思Hello"

2. 使用箭头函数  ()=>{}
箭头函数本身是没有this和arguments的，在箭头函数中引用this实际上是调用的是定义时的上一层作用域的this。
这里强调的是上一层作用域，是因为对象是不能形成独立的作用域的。

```
(1)、

var obj = {
　　say: function() {
　　var f1 = ()=>{
　　　　console.log("1111",this);	
　　}
　　f1();
　　}
}
var o = obj.say;
o();//f1执行时，say函数指向window，所以f1中的this指向window
obj.say();//f1执行时，say函数指向obj，所以f1中的this指向obj；

(2)、

var ojb = {
　　pro: {
　　　　getPro: ()=>{
　　　　　　console.log(this);
　　　　}
　　}
}
obj.pro.getPro();//this指向的是window，因为箭头函数定义时，getPro的上一级是pro，是一个对象，不能形成单独的作用域，故指向window。
```


