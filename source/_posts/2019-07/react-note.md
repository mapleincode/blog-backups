---
title: React.js 小书 - 第三阶段 - 学习笔记
date: 2018-05-08 17:38:23
tags: [ React.js, Redux ]
---

> [React.js 小书](http://huziketang.mangojuice.top/books/react/)



## 第三阶段

### Lesson 28 - 高阶组件(Higher-Order Components)

1. 高阶组件是函数。

2. 高阶组件的作用是提高代码复用度。

3. 高阶组件基本结构

   ```js
   export default (某个子组件, 其他参数) {
       class NewComponet extends Componet {
           // 各种可复用的逻辑，比如请求服务器、读取缓存
           // 也可以修改其他参数然后传到子组件中
           // const 修改后的参数 = 处理函数(其他参数)
           // this.setState({ 修改后的参数 })
           render() {
               return <某个子组件 data={this.修改后的参数}/>
           }
       }
       return NewComponet; // 返回这个重新被封装过的组件
   }
   ```

4. **高阶组件就是一个函数，传给它一个组件，它返回一个新的组件。**

### Lession 29 - React.js 的 context

1. 作者意思是别用 context

2. context 翻译过来就是全局的变量。但是前段的页面的层次会比较恐怖，所以前端们比较害怕全局的的属性容易被影响。

3. 控制 context 的办法有两个: 1. 控制定义，让定义麻烦点。 2. 控制接受，让接受麻烦点。

4. context 的基本代码演示:

   ```js
   // 假设这个是父组件
   class Index extends Component {
       static childContextTypes = {
           变量: PropTypes.string // prop-types 包。据说是必写的。为了让代码复杂点儿。
       }
       constructor() {
           super();
           this.state = { themeColor: 'red' }
       }
       getChildContext () {
           return { themeColr: this.state.themeColor }; // 这是子组件获得 Context 的函数 前端就是喜欢定义些奇怪的函数，然后方便框架自动获取 orz
       }
   }

   // 然后这个是子组件，子组件要获得父(爷爷)组件传过来的全局变量 context
   class Title extends Component {
       static contextTypes = {
           thiemeColor: PropTypes.string // 同 childContextTypes,也是为了麻烦
       }
       render() {
           return (
               <h1 style={{color: this.context.themeColor }}>标题</h1>
           )
       }
   }
   ```

5. Context 只能被子组件访问，哦对了还能被修改的样子。

6. 作者再次强调别用那个玩意儿，后面有封装过的更好用的东西介绍。



### Lession 30 - Redux（一）：优雅地修改共享状态

1. Redux 是一种前端框架架构形式

2. React-redux 才是将这个架构形式和 React.js 结合起来的一个库。

3. `appState`专门存数据的地方，前端毕竟模块化比较多，`(ಥ _ ಥ)`前端真难。

4. 然后就是`dispatch`这个方法

   1. 一个函数，专门管数据修改

   2. 格式

      ```js
      function dispatch(action = { type: '' }) {
          switch (action.type) {
              case: 'XXXX':
                  // 修改数据
                  break;
              default: 
                    break;
          }
      }
      ```

   3. dispatch 可以方便数据修改操作的管理，是的数据被修改的时候容易被监控。



### Lession 31 - 动手实现 Redux（二）：抽离 store 和监控数据变化

1. `createStore`，字面意思就是创建 `store`，而`store`就是 `appStore` + `dispatch`。估计类似一块块的专门的存储器 + 修改器。

2. createStore 接受两个参数: `state` 和 `stateChanger`，返回`store`对象，包含`getState()`和`dispatch()`。

3. `state`和`stateChanger`:

   ```js
   let appState = {
     title: {
       text: 'React.js 小书',
       color: 'red',
     },
     content: {
       text: 'React.js 小书内容',
       color: 'blue'
     }
   } // 其实就是一个 state 对象 没啥特点

   function stateChanger (state, action) {
     switch (action.type) {
       case 'UPDATE_TITLE_TEXT':
         state.title.text = action.text
         break
       case 'UPDATE_TITLE_COLOR':
         state.title.color = action.color
         break
       default:
         break
     }
   } // 这个比较有意思，首先这是一个函数，有两个变量
   // 变量1 是 state 一个对象用来存储数据
   // 变量2 就是之前提过的 action，包含一个 type
   // 这个 func 是一个 switch ，根据 action.type 对 state 进行修改

   const store = createStore(appState, stateChanger);

   store.getState();
   store.dispatch({ type: 'UPDATE_TITLE_TEXT', text: '这是另一个标题' });
   store.getState(); // 新的数据
   ```

4. listener 是一个监听函数。然后 store.subscribe(listener)。然后他就会保存这个监听函数。然后调用`store.dispatch()`然后监听函数就会被轮询调用一遍。Noder 可以当做一个 callback 来理解。只是这个 callback 可以多个传入(其实 Node 的 callback 也可以这么写)。

   > subscribe subscribe subscribe subscribe … 我只是在背单词/(ㄒoㄒ)/

   

### Lession 32 - 动手实现 Redux（三）：纯函数（Pure Function）简介

1. 一个函数的返回结果只依赖于它的参数，并且在执行过程里面没有副作用，我们就把这个函数叫做纯函数。
2. 粗暴的理解纯函数
   1. 函数里所有执行的变量，要么是穿进去，要么本来就是在内部的。
   2. 不修改传入的那部分参数。
   3. 类似 String.slice()，但是 Array.splice() 就不是（因为修改了传入的参数）。
3. 纯函数的纯在是为了安全。呃，如果是后端，后端会把你的函数都 hack 掉，吼吼~没有什么东西是改不了的。

### Lession 33 - 动手实现 Redux（四）：共享结构的对象提高性能



1. 复制对象的小技巧

   ```js
   const obj = { a: 1, b: 2}
   const obj2 = { ...obj } // => { a: 1, b: 2 } 类似 lodash.clone();
   ```

   > noder: 我们有 Lodash !!

2. 对 store 里的数据进行处理时，不在原有的 obj 上处理，而是重新新建一个 newObj。

   ```js
   let newAppState1 = { // 新建一个 newAppState1
     ...newAppState, // 复制 newAppState1 里面的内容
     title: { // 用一个新的对象覆盖原来的 title 属性
       ...newAppState.title, // 复制原来 title 对象里面的内容
       color: "blue" // 覆盖 color 属性
     }
   }
   ```

   原因: 可以保证修改前的数据和修改后的数据的 obj 不是同一个。

   这么做的好处有：

        1. 写法看起来更牛逼点
           2. 子对象，只有被修改的对象才会被新建，没有被修改的不会被新建。

   用 Node 的方式来写就是:

   ```js
   const _ = require('lodash');
   const newAppState = {
       title: {
           color: 'red'
       },
       qqqq: 'qqqq',
       pppp: {
           pp: 'pp'
       }
   };

   const newAppState1 = _.clone(newAppState);
   newAppState1.title = _.clone(newAppState1.title);
   newAppState1.title.color = 'red';
   ```

3. 实现办法是修改 stateChanger 的代码。修改返回为新建对象（但是我觉得是不是得判断不是和原有值相同 ？？？）

> 类似 Vue.js 数据绑定的确比 React.js 的实现来的有条理的多。



### Lession 34 - 动手实现 Redux（五）：不要问为什么的 reducer

1. 好吧  reducer 是大爷: 将 stateChanger 和 appState 合并到了一个。
2. reducer 兼顾了数据初始化 & 数据变化处理。
3. reducer 就是之前说的纯函数。
4. reducer 接受两个参数: 1. state 2. action。如果 state 为 null 则初始化 state。返回初始化后或者被修改后的 state。
5. reducer 的作用标准说法是: 初始化和计算新的`state`。面试的同学请记清楚这句话。



### Lession 35  - 动手实现 Redux（六）：Redux 总结

整个 redux 流程:

```js
// 定一个 reducer
function reducer (state, action) {
  /* 初始化 state 和 switch case */
}

// 生成 store
const store = createStore(reducer)

// 监听数据变化重新渲染页面
store.subscribe(() => renderApp(store.getState()))

// 首次渲染页面
renderApp(store.getState()) 

// 后面可以随意 dispatch 了，页面自动更新
store.dispatch(...)
```



### Lession 36 - 动手实现 React-redux（一）：初始化工程

呃…一个 demo。



### Lession 37 - 动手实现 React-redux（二）：结合 context 和 store

也是一个 demo。不过写着写着大概明白了什么回事。

1. 通过 context 来管理全局的一个 store。
2. store 存储着子组件也需要的数据。
3. 子组件获得数据直接通过 store.getState()，而子组件修改数据则通过 store.dispatch(action)。
4. 不需要繁琐的从父组件传  handle 函数，子组件通过 `onclick={this.prop.handleXXX.bind(this)}`来实现数据传递。



### Lession 38 - 动手实现 React-redux（三）：connect 和 mapStateToProps 

介绍了一个叫做`connect`的函数

实现代码: 

```react
import React, { Component } from 'react'
import PropTypes from 'prop-types'

export const connect = (mapStateToProps) => (WrappedComponent) => {
  class Connect extends Component {
    static contextTypes = {
      store: PropTypes.object
    }

    constructor () {
      super()
      this.state = { allProps: {} }
    }

    componentWillMount () {
      const { store } = this.context
      this._updateProps()
      store.subscribe(() => this._updateProps())
    }

    _updateProps () {
      const { store } = this.context
      let stateProps = mapStateToProps(store.getState(), this.props) // 额外传入 props，让获取数据更加灵活方便
      this.setState({
        allProps: { // 整合普通的 props 和从 state 生成的 props
          ...stateProps,
          ...this.props
        }
      })
    }

    render () {
      return <WrappedComponent {...this.state.allProps} />
    }
  }

  return Connect
}
```

1. mapStateToProps 是一个转换函数，方便传递需要的参数

效果代码: 

```react
const mapStateToProps = (state) => {
  return {
    themeColor: state.themeColor
  }
}
Header = connect(mapStateToProps)(Header);
```

通过 connect 函数和一个 mapStateToProps 实现 store 和子组件的数据获取和数据绑定。

### Lession 38 - 动手实现 React-redux（四）：mapDispatchToProps

> vscode 直接提示了 mapDispatchToProps ，估计也是和前者差不多的东西，通过一个高阶函数来实现，store 和函数的调用。

我猜是通过 store + mapDispatchToProps 得到一个特殊的函数。

首先在 connet 方法中加入了一个新的函数

```react
export const connect = (mapStateToProps, mapDispatchToProps) => (WrappedComponent) => {
    class Connect extends Component {
        static contextProps = {
            store: PropTypes.object
        }
        constructor () {
            super()
            this.state = {
                allProps: {}
            }
        }
        _updateProps () {
              const { store } = this.context
              let stateProps = mapStateToProps
                ? mapStateToProps(store.getState(), this.props)
                : {} // 防止 mapStateToProps 没有传入
              let dispatchProps = mapDispatchToProps
                ? mapDispatchToProps(store.dispatch, this.props)
                : {} // 防止 mapDispatchToProps 没有传入
              this.setState({
                allProps: {
                      ...stateProps,
                      ...dispatchProps,
                      ...this.props
                }
              })
        }
        componentWillMount() {
            const store = this.context.store;
            this._updateProps();
            store.subscribe(() => this._updateProps();
        }
        render() {
            return <WrappedComponent {...this.state.allProps} />
        }
    }
    return Connect;
}
```

####  mapDispatchToProps

好像是把 dispatch 的函数转换成 props，然后子组件直接调用 `this.props.xxx`，在内部写成`<button onclick={this.props.xxx.bind(this)}>` 。

不过看到后面是多了一部，现在子组件内部封装成一个函数:

```react
class ThemeSwitch extends Component {
    static propTypes = {
        themeColor: PropTypes.string,
        onSwitchColor: PropTypes.func
    }

    handleSwitchColor(color) {
        if(this.props.onSwitchColor) {
            this.props.onSwitchColor(color); // 感觉还是为了防止未传入 props 导致组件报错
        }
    }
    render() {
        return (
            <div>
                <button onclick={this.handleSwitchColor.bind(this, 'color')}></button>
            </div>
        )
    }
}
```

要注意一点是: 

mapStateToProps 和 mapDispathcToProps 的第二个参数都是`this.props`，似乎是`react-redux`里面的组件的 props 传入进去。估计只会会提到这一点。



### Lession - 40 动手实现 React-redux（五）：Provider

首先第一感觉也是和`connect` 差不多的东西。为了分离 `this.context`出去，之前 createStore 还是放在父组件 `Index`上。

后面发现完全想错了。

1. Provider 是一个组件。
2. Provider 用来包裹主组件，来实现 store 的创建和透传。
3. 通过 Provider，直接在其 props 里传入 store，实现对 store 的引用。而作为真正主组件的`Index`，则只要简单的结构代码就 OK 了。

```react
...
// 头部引入 Provider
import { Provider } from './react-redux'
...

// 删除 Index 里面所有关于 context 的代码
class Index extends Component {
  render () {
    return (
      <div>
        <Header />
        <Content />
      </div>
    )
  }
}

const store = createStore(themeReducer);

// 把 Provider 作为组件树的根节点
ReactDOM.render(
  <Provider store={store}>
    <Index />
  </Provider>,
  document.getElementById('root')
)
```

### Lession - 41 动手实现 React-redux（六）：React-redux 总结

主要讲述了 redux 概念 和  redux-react 基本逻辑:

1. connect 用来将 redux 相关代码抽离统一，主要是 store 中数据的传递和修改和绑定
2. Provider  用来将 context 初始化的代码抽离。
3. mapStateToProps state => props。将 store.getState() 转换成你需要的数据。
4. mapDispatchToProps dispatch => props。一个传入 dispatch 的函数。将 dispatch 封装到一个新的函数，用以直接调用。
5. createState 创建 store
6. reducer 创建 store 时定义的规则。



### Lession - 42 使用真正的 Redux 和 React-redux 

#### Install

```bash
npm install redux react-redux --save
```

#### Usage

``` js
import { Provider, connect } from 'react-redux'
import { createStore } from 'redux'
```



这里才真的认识到， redux 只是一种数据共享和管理的方式，所以 createStore 要从 redux 里面取。

而其他的实现则是基于 react ，所以才有了 react-redux。



还发现之前 reducer 把参数顺序搞错了，应该 const reducer = (state, action) {}



### Lession - 43  Smart 组件 vs Dumb 组件

> 杀马特组件 ？？？？

1.  通过`props` 渲染确定结果的组件我们把它叫做 Dumb 组件: 特点依赖少，但是对数据处理能力较差

2.  Smart 组件专门做数据相关的应用逻辑，和各种数据打交道、和 Ajax 打交道

3. 作者强调， Dumb 不应该引用 Smart 组件导致复用度变低。

4. 创建组件分类

   ```bash
   src/
     components/ -- Dumb 组件
     containers/ -- Smart 组件
   ```

