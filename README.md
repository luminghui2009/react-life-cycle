## 场景1
### 子组件从父组件获取props后，完成第一次的渲染即componentDidMount生命周期结束，当父组件的props发生了改变，在子组件里需要将新的props属性存到state里，这时调用组件的另一个生命周期componentWillReceiveProps(nextProps){
    /需要比较this.props和nextProps,然后调用this.setState()/
}

## 各个生命周期钩子的使用
> 参考资料:

    1.<怎样和何时使用react生命周期钩子方法> https://engineering.musefind.com/react-lifecycle-methods-how-and-when-to-use-them-2111a1b692b1
    2. <正确掌握React 生命周期(Lifecycle)>https://zhuanlan.zhihu.com/p/24926575


### 1 componentWillMount()
- 执行场景
    - 在render()方法之前
    - 主要用于根组件的App配置
- 解释
    - 因为componentWillMount是在render之前执行，所以在这个方法中setState不会发生重新渲染(re-render);
- 能否使用setState:NO,可以使用默认的state

### 2 render()

- 执行场景
    - 在componentWillMount()方法之后
    - 在componentWillReceiveProps(nextProps, nextState)方法之后

### 3 componentDidMount()
- 执行场景
    - 在render()方法之后
    - 主要用于ajax加载数据
- 解释
    - 1 这个方法会在render()之后立即执行；
    - 2 这里可以对DOM进行操作，这个函数之后ref变成实际的DOM(@TODO 表述可能不清晰);
    - 3 这里可以加载服务器数据，并且如果使用了redux之类的数据服务，这里可以出发加载服务器数据的action;
    - 4 这里可以使用setState()方法触发重新渲染(re-render);
- 能否使用setState：Yes

### 4 componentWillReceiveProps(nextProps)
- 执行场景
    - 在已经挂在的组件(mounted component)接收到新props时触发;
    - 简单的说是在除了第一次生命周期(componentWillMount -> render -> componentDidMount)之后的生命周期中出发;
    - 特定的props改变触发状态的过渡
- 解释
    - 1 如果你需要在props发生变化(或者说新传入的props)来更新state，你可能需要比较this.props和nextProps, 然后使用this.setState()方法来改变this.state;
- 能否使用setState：Yes

### 5 shouldComponentUpdate(nextProps, nextState)
- 执行场景
    - 在接收到新props或state时，或者说在componentWillReceiveProps(nextProps)后触发
    - 精确控制着组件是否要重新渲染
    
- 解释
    - 1 在接收新的props或state时确定是否发生重新渲染，默认情况返回true，表示会发生重新渲染
- 能否使用setState：No


### 6 componentWillUpdate(nextProps, nextState)
- 执行场景
    - 在props或state发生改变或者shouldComponentUpdate(nextProps, nextState)触发后, 在render()之前
    
- 解释
    - 1 这个方法在组件初始化时不会被调用;
- 说明
    - 千万不要在这个函数中调用this.setState()方法.;
    - 如果确实需要响应props的改变，那么你可以在componentWillReceiveProps(nextProps)中做响应操作;
    - 如果shouldComponentUpdate(nextProps, nextState)返回false，那么componentWillUpdate()不会被触发;
- 能否使用setState：No
### 7 componentDidUpdate(prevProps, prevState)
- 执行场景
    - 在发生更新或componentWillUpdate(nextProps, nextState)后
    - 在状态或者props改变之后更新DOM
    
- 解释
    - 1 这个方法在组件初始化时不会被调用;
    - 使用这个方法可以对组件中的DOM进行操作
- 说明
    - 千万不要在这个函数中调用this.setState()方法.;
    - 如果确实需要响应props的改变，那么你可以在componentWillReceiveProps(nextProps)中做响应操作;
    - 如果shouldComponentUpdate(nextProps, nextState)返回false，那么componentWillUpdate()不会被触发;
- 能否使用setState：Yes

### 8 componentWillUnmount()
- 执行场景
    - 在组件卸载(unmounted)或销毁(destroyed)之前
    
- 解释
    - 这个方法可以让你处理一些必要的清理操作，比如无效的timers、interval，或者取消网络请求，或者清理任何在componentDidMount()中创建的DOM元素(elements);
- 能否使用setState：No


## 代码演示
```
import React from 'react';

class App extends React.Component {

   constructor(props) {
      super(props);
		
      this.state = {
         data: 0
      }

      this.setNewNumber = this.setNewNumber.bind(this)
   };

   setNewNumber() {
      this.setState({data: this.state.data + 1})
   }

   render() {
      return (
         <div>
            <button onClick = {this.setNewNumber}>INCREMENT</button>
            <Content myNumber = {this.state.data}></Content>
         </div>
      );
   }
}

class Content extends React.Component {

   componentWillMount() {
      console.log('Component WILL MOUNT!')
   }

   componentDidMount() {
      console.log('Component DID MOUNT!')
   }

   componentWillReceiveProps(newProps) {    
      console.log('Component WILL RECIEVE PROPS!')
   }

   shouldComponentUpdate(newProps, newState) {
      return true;
   }

   componentWillUpdate(nextProps, nextState) {
      console.log('Component WILL UPDATE!');
   }

   componentDidUpdate(prevProps, prevState) {
      console.log('Component DID UPDATE!')
   }

   componentWillUnmount() {
      console.log('Component WILL UNMOUNT!')
   }
	
   render() {
      return (
         <div>
            <h3>{this.props.myNumber}</h3>
         </div>
      );
   }
}

export default App;


// main.js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App.jsx';

ReactDOM.render(<App/>, document.getElementById('app'));

setTimeout(() => {
   ReactDOM.unmountComponentAtNode(document.getElementById('app'));}, 10000);
```
    
