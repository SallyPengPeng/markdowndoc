## redux原理

![redux原理](https://images-sally.oss-cn-beijing.aliyuncs.com/img/redux原理.png)

### state

react组件根据state渲染不同的页面，不能直接修改state，必须使用`dispatch(action)`通过`reducer`加工状态

### action

1. 一般`action`：是一个object，包含2个属性

   - type：标识属性, 值为字符串, 唯一, 必要属性
   - data：数据属性, 值类型任意, 可选属性

   例子：`{ type: 'ADD_STUDENT',data:{name: 'tom',age:18} }`

2. 异步action：延迟的动作不想交给组件自身，想交给action。使用`react-thunk`，异步任务有结果后，分发一个同步的action去真正操作数据，后文详细讲解

### reducer

1. 用于初始化状态、加工状态。
2. 加工时，根据旧的`state`和`action`， 产生新的state的纯函数。

### store

1. 将state、action、reducer联系在一起的对象
2. 如何得到此对象?
   1)	import {createStore} from 'redux'
   2)	import reducer from './reducers'
   3)	const store = createStore(reducer)
3. 此对象的功能?
   1)	getState(): 得到state
   2)	dispatch(action): 分发action, 触发reducer调用, 产生新的state
   3)	subscribe(listener): 注册监听, 当产生了新的state时, 自动调用

###  整体流程

- 初始启动：
  - 使用最顶层的 root reducer 函数创建 Redux store
  - store 调用一次 root reducer，并将返回值保存为它的初始 `state`
  - 当 UI 首次渲染时，UI 组件访问 Redux store 的当前 state，并使用该数据来决定要呈现的内容。同时监听 store 的更新，以便他们可以知道 state 是否已更改。

- 更新环节：
  - 应用程序中发生了某些事情，例如用户单击按钮
    - dispatch 一个 action 到 Redux store，例如 `dispatch({type: 'counter/increment'})`
    - store 用之前的 `state` 和当前的 `action` 再次运行 reducer 函数，并将返回值保存为新的 `state`
    - store 通知所有订阅过的 UI，通知它们 store 发生更新
    - 每个订阅过 store 数据的 UI 组件都会检查它们需要的 state 部分是否被更新。
    - 发现数据被更新的每个组件都强制使用新数据重新渲染，紧接着更新网页

## 创建store

使用`createStore`创建store

```javascript
import { createStore } from 'redux';
const store = createStore(reducer, enhancer)
```

### reducer

1、只有一个reduce函数，则reducer接受state 和action，根据`previous state`和用户操作的action，更新state：

```javascript
let reducers =(state = defaultState ,action)=>{
    switch (action.type){
        case "increment":
            return {
              	...state,
                counter:state.counter+1
            }
        case "decrement":
            return {
              	...state
                counter:state.counter-1
            }
        default :
        	return state 
    }
}
```

2、随着应用变得越来越复杂，考虑将 reducer 函数 拆分成多个单独的函数，拆分后的每个函数负责独立管理 state 的一部分。使用`combineReducers`。合并后的 reducer 可以调用各个子 reducer，并把它们返回的结果合并成一个 state 对象

```javascript
import { combineReducers } from "redux";

// potato 和 tomato 就是一般的reducer定义
const rootReducer = combineReducers({potato, tomato})
// rootReducer 将返回如下的 state 对象
{
  potato: {
    // ... potatoes
  },
  tomato: {
    // ... tomatoes
  }
}
```

### enhancer

可选参数，是一个组合 store creator 的高阶函数，返回一个新的强化过的 store creator。Redux 中唯一内置的 store enhander 是`applyMiddleware()`

Middleware 最常见的使用场景是无需引用大量代码或依赖类似 Rx 的第三方库实现异步 actions。这种方式可以实现像 dispatch 一般的 actions 那样 dispatch 异步 actions。

```javascript
import { createStore, applyMiddleware } from 'redux'
import reduxThunk from 'redux-thunk'

const store = createStore(reducer, applyMiddleware(reduxThunk));
```

## Provider

使整个app访问到Redux store中的数据，必须放在需要共享store的最上层组件。Provider是用context封装的。

```javascript
import { Provider } from "react-redux";
import Store from './createStore';
class App extends Component {
  render() { 
    return (      
      <Provider store={store}>
      	<Router />
      </Provider>
    );
  }
}
 
export default App;
```

## connect

将组件连接到redux store

```javascript
import { connect } from "react-redux";
function connect(mapStateToProps?, mapDispatchToProps?, mergeProps?, options?)
```

### mapStateToProps

如果组件不需要订阅state的改变，`mapStateToProps`传`null`或`undefined`。如果`mapStateToProps`定义了，则只要store发生改变，`mapStateToProps`就会被调用，从而给组件最新的props。

```typescript
mapStateToProps?: (state, ownProps?) => Object
```

普通的reducer函数时：

````javascript
const mapStateToProps = state => ({ counter: state. counter })
````

使用了`combineReducers`时，只订阅其中一个种类的state：

```javascript
const mapStateToProps = state => state.potato; // state.potato已经是一个object了
```

### mapDispatchToProps

```typescript
mapDispatchToProps?: Object | (dispatch, ownProps?) => Object
```

当不提供时，组件默认将接收`dispatch`

1、function形式的`mapDispatchToProps`，下例中，组件能接收到 `increment` `decrement` `reset`3个props，不用直接暴露`dispatch`给组件

```javascript
const mapDispatchToProps = (dispatch) => {
  return {
    increment: () => dispatch({ type: 'INCREMENT' }),
    decrement: () => dispatch({ type: 'DECREMENT' }),
    reset: () => dispatch({ type: 'RESET' }),
  }
}
```

2、Object形式，且store没有使用`react-thunk`，object的每一项都必须是action creator

```typescript
type Action = Object;
type ActionCreator<A, P extends any[] = any[]> = (...args: P) => Action | AsyncAction
```

```javascript
const addTodo = (...args) => {
  // ...一些操作
  return { type, data };
};

const deleteTodo = (...args) => {
  // ...一些操作
  return { type, data };
};

const mapDispatchToProps = {
  addTodo,
  deleteTodo,
}

```

3、Object形式，且store使用`react-thunk`，使用AsyncAction

```javascript
function makeASandwichWithSecretSauce(forPerson) {
  // Invert control!
  // Return a function that accepts `dispatch` so we can dispatch later.
  // Thunk middleware knows how to turn thunk async actions into actions.
  return function (dispatch) {
    return fetchSecretSauce().then(
      sauce => dispatch(makeASandwich(forPerson, sauce)),
      error => dispatch(apologize('The Sandwich Shop', forPerson, error))
    )
  }
}

const mapDispatchToProps = { makeASandwichWithSecretSauce };
```

## 组件中使用

```javascript
import { connect } from 'react-redux';

const Login = (props) => {
  // 经过connect包裹的组件能够接收到 state 和 dispatch
  const { token, user, login, getUserInfo } = props;
  // ……一些操作

  return <View />;
};

export default connect(state =>({ token: state.token, user: state.user }), { login, getUserInfo })(Login);
```

## reference

1. https://react-redux.js.org/api/connect#mergeprops-stateprops-dispatchprops-ownprops--object
2. https://redux.js.org/api/applymiddleware
3. https://www.jianshu.com/p/f04e87bcb544













