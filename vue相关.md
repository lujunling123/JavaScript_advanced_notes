### MVVM的理解 ###
M：Model数据模型层，V：View页面视图层，VM:ViewModel用于连接两者之前数据传递的桥梁，从而实现了双向数据的传递
在MVVM框架中View和Model是不能直接进行通信的，通过ViewModel来进行通信，ViewMode承担的是观察者模式，当数据发生变化ViewModel能监听到数据的改变，通知对应的视图自动更新，而当用户操作视图，viewModel也能监听到视图的变化，通知Model修改数据
```
      ---> 数据绑定
Model      ViewModel <---> View
      <--- Dom事件监听
```

### Vue实现数据双向绑定原理 ### 
Vue2.0是Object.defineProperty()：采用的数据劫持结合发布者-订阅者模式实现，通过Object.defineProperty()来劫持各个属性的setter和getter，在数据变动时发布消息给订阅者，触发相应监听回调。
Vue3.0是Proxy ：通过观察者模式实现，函数自动观察数据对象，一旦对象有变化，函数就会自动执行

### Vue的生命周期 ###
beforeCreate
created
beforeMount
mounted
beforeUpdate
updated
beforeDestroy
destroyed

### Vue组件间的参数传递 ###
父传子：通过：绑定一个属性给子组件，子组件通过props获取
自传父：通过绑定事件传递，子组件通过 $emit()传递给父组件
兄弟之间传参：可以使用eventBus实现，也可以用Vuex

### Vue的路由实现 ###
history：使用的是浏览器提供的接口，通过浏览器的back(),go(),forward()等，通过读取浏览器历史记录来进行各种跳转操作。

hash：是通过触发点击带锚点的链接（#）本身以及它后面的字符称为hash,可以通过window.location.hash属性读取
HashHistory：有push()将新路由添加到浏览器访问历史的栈顶，replace()方法替换掉当前的路由