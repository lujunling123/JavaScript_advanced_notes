### React 15 和 16 的生命周期与区别 ###

> React 15

1. 初始化阶段

    * constructor 构造函数
    * getDefaultProps props 默认值
    * getInitialState state 默认值

2. 挂载阶段

    * componentWillMount 组件已经construct()初始化数据，但还未渲染DOM。
    * render 组件渲染
    * componentDidMount 组件第一次渲染完成，Dom已经生成，进行请求返回setState后组件会重新渲染

3. 更新阶段   
    * componentWillReceivePorps(nextProps) 接受父组件改变后的props,对比nextProps和this.props,将nextProps的state为当前组件的state，需要重新渲染
    * shouldComponentUpdate(nextProps,nextState) 用于控制组建重新渲染
    * componentWillUpdate(nextProps,nextState) shouldComponentUpdate为true时进入重新渲染阶段调用
    * render 组件渲染
    * componentDidUpdate(prevProps,PrevState) 组件重新更新完毕后，prevProps,PrevState更新前的props state

4. 卸载阶段 
    * componentWillUnMount 组件卸载前调用

>React 16

16.4+ 与15相比较，除了shouldComponentUpdate,其他在render函数之前的所有函数（componentWillMount，componentWillRecevieProps，componentWillUpdate，都被getDerivedStateFromProps代替），在render之后添加了getSnapshotBeforeUpdate

* static getDerivedStateFromProps(props, state) 组件创建更新时render方法之前调用，返回一个对象更新状态，或者返回null不更新 此时的this为undefined，static方法只能构造函数来调用，实例不能调用
* getSnapshotBeforeUpdate(prevProps, prevState) render方法之后调用，此时可以读取但无法使用Dom，此方法的返回值将作为参数传递给componentDidUpdate()

``` 
class ScrollingList extends React.Component {
  constructor(props) {
    super(props);
    this.listRef = React.createRef();
  }

  getSnapshotBeforeUpdate(prevProps, prevState) {
    //我们是否要添加新的 items 到列表?
    // 捕捉滚动位置，以便我们可以稍后调整滚动.
    if (prevProps.list.length < this.props.list.length) {
      const list = this.listRef.current;
      return list.scrollHeight - list.scrollTop;
    }
    return null;
  }

  componentDidUpdate(prevProps, prevState, snapshot) {
    //如果我们有snapshot值, 我们已经添加了 新的items.
    // 调整滚动以至于这些新的items 不会将旧items推出视图。
    // (这边的snapshot是 getSnapshotBeforeUpdate方法的返回值)
    if (snapshot !== null) {
      const list = this.listRef.current;
      list.scrollTop = list.scrollHeight - snapshot;
    }
  }

  render() {
    return (
      <div ref={this.listRef}>{/* ...contents... */}</div>
    );
  }
}
```

### diff算法 ###
通过diff对比新旧虚拟节点，计算出虚拟DOM中真正变化的部分，并针对该部分进行原生DOM操作(调和过程)，而非重新渲染整个页面。 （当节点数量过大或更新操作过于频繁的时候会影响React的渲染性能）

### key值的作用 ###
key是给每个虚拟节点一个唯一的ID，可以更准确，更快的拿到旧的虚拟节点中对应的节点，用于追踪列表项的新增，删除，修改等的辅助标识，key需要保证他的唯一性，在React diff算法中会根据key的值来判断这个元素是新创建的还是修改的，从而减小不必要的渲染

### setState ###
当调用setState的时候，不会立即更新this.state，而是将修改之后的state添加在队列中，当react自身的事件处理函数 或 生命周期出发时，isBatchingUpdates 会被赋值为true时才会遍历合并更新，当更新完成时isBatchingUpdates有会复原为false。React的setState本身不是异步的，是因为批量处理机制给人一种一部的假象

### redux ###
Redux里面只有一个store，应用的共享数据都存在store中，redux通过createStore函数生成store，用subscribe监听每次修改情况，每当在store上通过dispatch触发action，store的数据就会相应的发生变化。react-redux提供了两个重要的对象，provider和connect，provider是组件可被连接，connect把组件和redux的store正真连接起来
```
// 在最外层容器中，把所有内容包裹在provider组件中，将之前创建的store作为prop传递给provider。
const App = () => {
  return (
    <Provider store={store}>
      <Comp/>
    </Provider>
  )
};
// 而在使用store的时候都需要通过connect连接到组件
```
使用connect可以把state和dispatch绑定到react组件，是得组件可以访问到redux的数据。connect是一个高阶函数，他接收参数最终返回一个组件，这个组件最终连接了顶层组件provider提供的store数据
@connect是connect的装饰器，语法糖，将model和component串联起来











