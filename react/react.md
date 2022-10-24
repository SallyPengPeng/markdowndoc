## React事件

JSX 上写的事件并没有绑定在对应的真实 DOM 上，而是通过事件代理的方式，将所有的事件都统一绑定在了 `document` 上。这样的方式不仅减少了内存消耗，还能在组件挂载销毁时统一订阅和移除事件。

另外冒泡到 `document` 上的事件也不是原生浏览器事件，而是 React 自己实现的合成事件（SyntheticEvent）

React的事件和普通的HTML事件区别：

- 对于事件名称命名方式，原生事件为全小写，react 事件采用小驼峰；
- 对于事件函数处理语法，原生事件为字符串，react 事件为函数；
- react 事件不能采用 return false 的方式来阻止浏览器的默认行为，而必须要地明确地调用`preventDefault()`来阻止默认行为。

## 生命周期

https://www.jianshu.com/p/b331d0e4b398

```jsx
// 挂载组件
ReactDOM.render(<MyComponent />, document.getElementById('test'));
// 卸载组件
ReactDOM.unmountComponentAtNode(document.getElementById('test'));
// 组件挂载后调用
componentDidMount(){}
// 组件将要卸载时调用
componentWillUnmount(){}
// 初始化渲染、状态更新之后
render() {}
```

react生命周期-旧版本

componentWillReceiveProps 第一次不调用，第二次接收新参数才会调用

新版本中，componentWillMount componentWillReceiveProps componentWillUpdata，这3个方法需要加UNSAFE_，否则会有告警，预计17版本会删除原版本，只能用UNSAFE

![react生命周期(旧)](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-react%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F-%E6%97%A7--5c4592bdff188fae10cd379cb48649ca-bbc.png)

react生命周期-新版本

新的版本和旧版本相比，

废弃了3个钩子:被废弃的三个函数都是在render之前

 componentWillMount：这个函数的功能完全可以使用componentDidMount和 constructor来代替

componentWillReceiveProps：判断前后两个 props 是否相同，如果不同再将新的 props更新到相应的 state 上去。这样做一来会破坏 state 数据的单一数据源，导致组件状态变得不可预测，另一方面也会增加组件的重绘次数。

componentWillUpdate：与 componentWillReceiveProps 类似原因

新增了2个钩子:

getDerivedStateFromProps ：只有state 的值在任何时候都取决于 props用

**getSnapshotBeforeUpdate(prevProps, prevState)**

这个方法在 `render` 之后，`componentDidUpdate` 之前调用，有两个参数 `prevProps` 和 `prevState`，表示更新之前的 `props` 和 `state`，这个函数必须要和 `componentDidUpdate` 一起使用，并且要有一个返回值，默认是 `null`，这个返回值作为第三个参数传给 `componentDidUpdate`。

![react生命周期(新)](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-react%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F-%E6%96%B0--7009279b5cb691a3e79f1bebd0093cef-918.png)

**componentDidCatch(error, info)**

此生命周期在后代组件抛出错误后被调用。 它接收两个参数∶

- error：抛出的错误。
- info：带有 componentStack key 的对象，其中包含有关组件引发错误的栈信息

## React API

### component PureComponent

在React中，当prop或者state发生变化时，可以通过在shouldComponentUpdate生命周期函数中执行return false来阻止页面的更新，从而减少不必要的render执行。React.PureComponent会自动执行 shouldComponentUpdate。

React.PureComponent 中浅层对比 prop 和 state 。如果赋予 React 组件相同的 props 和 state，render() 函数会渲染相同的内容，那么在某些情况下使用 React.PureComponent 可提高性能。

### memo()

高阶组件，只适用于函数组件，而不适用 class 组件，与PureComponent类似，但只检查props变更

```react
// 默认情况下对复杂对象做浅层对比。
// 传入areEqual比较函数，控制对比过程
// 注意：props 相等，areEqual 返回 true；如果 props 不相等，则返回 false。与 shouldComponentUpdate 方法的返回值相反。
const MyComponent = React.memo(function MyComponent(props) {}, areEqual);
```

避免不必要的render的方法：

1. shouldComponentUpdate 和 PureComponent
2. 利用高阶组件。在函数组件中，并没有 shouldComponentUpdate 这个生命周期，可以利用高阶组件，封装一个类似 PureComponet 的功能
3. 使用 React.memo

### cloneElement()

以 `element` 元素为样板克隆并返回新的 React 元素。返回元素的 props 是将新的 props 与原始元素的 props 浅层合并后的结果。新的子元素将取代现有的子元素，而来自原始元素的 `key` 和 `ref` 将被保留

```jsx
React.cloneElement(
  element,
  [props],
  [...children]
)

// 等同于
<element.type {...element.props} {...props}>{children}</element.type>
```

### Children

提供了用于处理 `this.props.children` 不透明数据结构的实用方法

| 方法                     | 解释                                                      |
| ---------------------- | ------------------------------------------------------- |
| React.Children.map     | 在 `children` 里的每个直接子节点上调用一个函数                           |
| React.Children.forEach |                                                         |
| React.Children.count   | 返回 `children` 中的组件总数量，等同于通过 `map` 或 `forEach` 调用回调函数的次数 |
| React.Children.only    | 验证 `children` 是否只有一个子节点（一个 React 元素），如果有则返回它，否则此方法会抛出错误 |

### isValidElement

判断element是否为react element

```javascript
function isValidElement(object) {
  // $$typeof是定义react element时都会加上的属性
  return typeof object === 'object' && object !== null && object.$$typeof === REACT_ELEMENT_TYPE;
}
```

### Context

一种组件间通信方式, 常用于【祖组件】与【后代组件】间通信，隔代通信

React.createContext  ContextProvider  Class.contextType  Context.Consumer

1) 创建Context容器对象，需要放在需要用到的后代组件也能访问到的地方

```
const XxxContext = React.createContext(defaultValue) 
```

2) 渲染子组件时，外面包裹xxxContext.Provider, 通过value属性给后代组件传递数据：

```
<xxxContext.Provider value={数据}>
    子组件
</xxxContext.Provider>
```

3. 后代组件读取数据：需要用到的后代组件必须声明才能接收

   ```
   //第一种方式:仅适用于类组件 
   static contextType = xxxContext  // 声明接收context
   this.context // 读取context中的value数据
   
   //第二种方式: 函数组件与类组件都可以
   <xxxContext.Consumer>
   { value => ()} // value就是context中的value数据要显示的内容
   </xxxContext.Consumer>
   ```

### forwardRef

Ref 转发是一个可选特性，其允许某些组件接收 ref，并将其向下传递（换句话说，“转发”它）给子组件。

```react
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// 你可以直接获取 DOM button 的 ref：
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```

### lazy &suspense

```react
// 该组件是动态加载的
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    // 显示 <Spinner> 组件直至 OtherComponent 加载完成
    <React.Suspense fallback={<Spinner />}>
      <div>
        <OtherComponent />
      </div>
    </React.Suspense>
  );
}
```

### propTypes defaultProps

https://zh-hans.reactjs.org/docs/typechecking-with-proptypes.html

PropTypes 提供一系列验证器，可用于确保组件接收到的数据类型是有效的，只在开发模式下检查。可以对数据类型、必传进行限制

```jsx
import PropTypes from 'prop-types';

MyComponent.propTypes = {
  // 你可以将属性声明为 JS 原生类型，默认情况下
  // 这些属性都是可选的。
  optionalArray: PropTypes.array,
  optionalBool: PropTypes.bool,
  optionalFunc: PropTypes.func,
  optionalNumber: PropTypes.number,
  optionalObject: PropTypes.object,
  optionalString: PropTypes.string,
  optionalSymbol: PropTypes.symbol,

  // 任何可被渲染的元素（包括数字、字符串、元素或数组）
  // (或 Fragment) 也包含这些类型。
  optionalNode: PropTypes.node,
  // 一个 React 元素。
  optionalElement: PropTypes.element,
  // 一个 React 元素类型（即，MyComponent）。
  optionalElementType: PropTypes.elementType,  
  // 枚举类型。
  optionalEnum: PropTypes.oneOf(['News', 'Photos']),
    // 一个对象可以是几种类型中的任意一个类型
  optionalUnion: PropTypes.oneOfType([
    PropTypes.string,
    PropTypes.number,
    PropTypes.instanceOf(Message)
  ]),
  // 在任何 PropTypes 属性后面加上 `isRequired` ，确保
  // 这个 prop 没有被提供时，会打印警告信息。
  requiredFunc: PropTypes.func.isRequired,

  // 任意类型的数据
  requiredAny: PropTypes.any.isRequired,
};
```

```jsx
import PropTypes from 'prop-types';

class MyComponent extends React.Component {
  render() {
    // 这必须只有一个元素，否则控制台会打印警告。
    const children = this.props.children;
    return (
      <div>
        {children}
      </div>
    );
  }
}

MyComponent.propTypes = {
  children: PropTypes.element.isRequired
};
```

defaultProps 属性来定义 props 的默认值

```jsx
// 指定 props 的默认值：
Greeting.defaultProps = {
  name: 'Stranger'
};
```

## ReactDom API

### createPortal()

https://react.docschina.org/docs/portals.html

提供一种将子节点渲染到 DOM 节点中的方式，该节点存在于 DOM 组件的层次结构之外。

使用场景：需要将子元素插入到 DOM 节点中的不同位置。一个 portal 的典型用例是当父组件有 `overflow: hidden` 或 `z-index` 样式时，但你需要子组件能够在视觉上“跳出”其容器。例如，对话框、悬浮卡以及提示框

```react
ReactDOM.createPortal(child, container)
// child：任何可渲染的 React 子元素，例如一个元素，字符串或 fragment
// container：一个 DOM 元素。
```

注意：尽管 portal 可以被放置在 DOM 树中的任何地方，但其行为和普通的 React 子节点行为一致。由于 portal 仍存在于 React 树， 且与 DOM 树 中的位置无关，那么无论其子节点是否是 portal，像 context 这样的功能特性都是不变的。这包含事件冒泡。一个从 portal 内部触发的事件会一直冒泡至包含 React 树的祖先，即便这些元素并不是 DOM 树 中的祖先。

## render props

使A B形成父子组件的方法

1、使用this.props.children

```
// 上层组件引用A
<A><B/></A>
……
// A组件定义
<div>
    <h3>组件</h3>
    {this.props.children}
</div>
```

2、直接使用

```
// 上层组件引用A
<A />
……
// A组件定义
<div>
    <h3>组件</h3>
    <B />
</div>
```

使用第一种方法时，若B需要使用A组件内的数据，无法实现，所以需要使用render prop

向组件内部动态传入带内容的结构(标签)：

1. 使用children props: 通过组件标签体传入结构
2. 使用render props: 通过组件标签属性传入结构,而且可以携带数据，一般用render函数属性

```jsx
class Cat extends React.Component {
  render() {
    const mouse = this.props.mouse;
    return (
      <img src="/cat.jpg" style={{ position: 'absolute', left: mouse.x, top: mouse.y }} />
    );
  }
}

class Mouse extends React.Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = { x: 0, y: 0 };
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div style={{ height: '100vh' }} onMouseMove={this.handleMouseMove}>
        {/*
          使用 `render`prop 动态决定要渲染的内容，
          而不是给出一个 <Mouse> 渲染结果的静态表示
        */}
        {this.props.render(this.state)}
      </div>
    );
  }
}

class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>移动鼠标!</h1>
        <Mouse render={mouse => (
          <Cat mouse={mouse} />
        )}/>
      </div>
    );
  }
}
```

## 类中的构造器

类中的构造器能省略时尽量省略不写

通常，在 React 中，构造函数仅用于以下两种情况：

通过给 this.state 赋值对象来初始化内部 state
为事件处理函数绑定实例

在为 React.Component 子类实现构造函数时，应在其他语句之前前调用 `super(props)`。否则，`this.props` 在==构造函数==中可能会出现未定义的 bug。基本用不上

```
constructor() {
    super();
    console.log(thsi.props); // undefined
}
```

## react语法

react 使用虚拟DOM 和 Diffing算法（逐层对比，最小粒度是标签），尽量减少与真实DOM的交互

jsx语法规则：

1. 定义虚拟DOM，不能写引号

2. 标签中混入js==表达式==时要用 {}。js语句与js表达式有区别
   
   表达式会产生一个值，可以放在任何需要值的地方；语句是if for switch等

3. 样式的类型需要使用className，而不是class

4. 内联样式，要用style={{ key: value }}，其中第一个{}表示里面是js表达式，第二个{}表示类型是object

5. 根标签只能有1个

6. 标签必须闭合

7. 标签首字母：
   
   1. 小写字母开头，则转为html中同名元素。若html中无该标签对应的同名元素，则报错
   2. 大写字母开头，react就去渲染对应的组件

8. 事件监听都要是驼峰，监听必须是function
   
   ```
   <button onclick="demo()">按钮</button>  // 原生js
   <button onClick={demo}>按钮</button>  // react
   
   function demo() {
       alert('111');
   }
   ```

## 组件类别

执行函数组件时过程：

1. React解析组件标签，找到MyComponent组件
2. 发现组件是使用函数定义的，就调用该函数，拿到返回值
3. 将返回的虚拟DOM转真实DOM，随后呈现在页面中

执行类组件时过程：

1. React解析组件标签，找到MyComponent组件
2. 发现组件是使用类定义的，随后new出来该类的实例，并通过该实例调用到原型上的render方法，render中的this指向的是MyComponent的实例对象
3. 将render返回的虚拟DOM转为真实DOM，随后呈现在页面中

## 组件实例对象的三大核心属性

### state

仅适用于类组件

修改state使用内置API setState，且更新是合并，而不是替换

### prop

适用于类组件 和 函数组件

`props`是不可变数据。但是，`this`却始终是可变的 。

`this`存在的意义，就是可变的。react在执行过程中，会修改`this`上的数据，保证能够在 `render`和其他的生命周期方法里，读取到最新的数据(props, state)。

例子：https://codesandbox.io/s/pjqnl16lm7

```
let p = { name: 'Tom', age: 18, sex: 'female' };

// 原生js，不允许使用...展开对象
console.log({ ...p }); // 正确，构造字面量对象时用法
console.log(...p); // 报错，提示对象不是iterator

// babel + react, 允许...p，仅仅适用于标签属性的传递
// 注意此处理解，{} 是react用法，表明内部混入js表达式，和上面用法不同
<Person { ...p } />
```

### refs

1、字符串形式的ref，不推荐使用，存在效率问题，已过时并可能会在未来的版本被移除

```
<input ref="input1" type="text" />
 <button onClick={this.showData}>点击</button>
 ...
 showData = () => {
     const { input1 } = this.refs;
     console.log('组件实例', this);
     alert(input1.value);
 }
```

2、回调形式的ref

在更新过程中它会被执行两次，第一次传入参数 `null`，然后第二次会传入参数 DOM 元素。这是因为在每次渲染时会创建一个新的函数实例，所以 React 清空旧的 ref 并且设置新的。通过将 ref 的回调函数定义成 class 的绑定函数的方式可以避免上述问题，但是大多数情况下它是无关紧要的

```
// 将 ref 的回调函数定义成 class 的绑定函数的方式，意思是
<input ref={ this.saveInput } type="text" />
```

```
// current为input节点
<input ref={ current => this.input1 = current } type="text" />
```

3、createRef

对该节点的引用可以在 ref 的 `current` 属性中被访问。

```
input1 = React.createRef();
...
<input ref={ this.input1 } type="text" />
<button onClick={this.showData}>点击</button>
 ...
 showData = () => {
     alert(this.input1.current.value);
 }
```

4、forwardRef

```react
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// 你可以直接获取 DOM button 的 ref：
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```

当 ref 挂载完成，`ref.current` 将指向 `<button>` DOM 节点，使用 `FancyButton` 的组件可以获取底层 DOM 节点 `button` 的 ref 

## 事件处理

1、通过onXxx属性指定事件处理函数（注意大小写）

​    React使用的是自定义（合成）事件，而不是使用的原生DOM事件-为了更好的兼容性；    React中的事件是通过事件委托方式处理的（委托给组件最外层的元素）-为了更高效

2、通过event.target得到发生事件的DOM元素对象，当发生事件的元素就是操作元素时可以省略ref，使用event.target

## key

1. key有什么作用（key的内部原理是什么）
   
   简单的说，key是虚拟DOM对象的标识，在更新显示时key起着及其重要的作用
   
   详细的说，当状态中的数据发生变化时，react会根据【新数据】生成【新的虚拟DOM】，随后React进行【新虚拟DOM】与【旧虚拟DOM】的diff比较，比较规则如下：
   
   1. 旧虚拟DOM中找到了与新虚拟DOM相同的key：
      - 若虚拟DOM中内容没变，直接使用之前的真实DOM
      - 若虚拟DOM中内容变了，则生成新的真实DOM，随后替换掉页面中之前的真实DOM
   2. 旧虚拟DOM中未找到与新虚拟DOM相同的key：根据数据创建新的真实DOM，随后渲染到页面

2. 为什么遍历列表时，key最好不要用index？
   
   用index作为key可能会引发的问题：
   
   1. 若对数据进行：逆序添加、逆序删除等破坏顺序操作：会产生没有必要的真实DOM更新 ==》界面效果没问题，但效率低
   2. 如果结构中还包含输入类的DOM：会产品错误DOM更新==》界面有问题
   3. 注意，如果不存在对数据的逆序添加、逆序删除等皮怀顺序操作，仅用于渲染列表用于展示，使用index作为key没有问题

## 虚拟DOM

虚拟DOM:

1. 本质是Object类型对象
2. 虚拟DOM属性比较少，因为虚拟DOM是react内部使用，无需真实DOM那么多的属性
3. 虚拟DOM最终会被转换为真实DOM

双缓存：可以把虚拟 DOM 看成是 DOM 的一个 buffer，和图形显示一样，它会在完成一次完整的操作之后，再把结果应用到 DOM 上，这样就能减少一些不必要的更新，同时还能保证 DOM 的稳定输出。

MVC： Model-View-Controller（模型-视图-控制器）

## 解决跨域

配置代理

方法一

> 在package.json中追加如下配置

```json
"proxy":"http://localhost:5000"
```

说明：

1. 优点：配置简单，前端请求资源时可以不加任何前缀。
2. 缺点：不能配置多个代理。
3. 工作方式：上述方式配置代理，当请求了3000不存在的资源时，那么该请求会转发给5000 （优先匹配前端资源）

方法二

1. 第一步：创建代理配置文件

```
在src下创建配置文件：src/setupProxy.js
```

2. 编写setupProxy.js配置具体代理规则：

```js
const proxy = require('http-proxy-middleware')

module.exports = function(app) {
  app.use(
    proxy('/api1', {  //api1是需要转发的请求(所有带有/api1前缀的请求都会转发给5000)
      target: 'http://localhost:5000', //配置转发目标地址(能返回数据的服务器地址)
      changeOrigin: true, //控制服务器接收到的请求头中host字段的值
      /*
          changeOrigin设置为true时，服务器收到的请求头中的host为：localhost:5000
          changeOrigin设置为false时，服务器收到的请求头中的host为：localhost:3000
          changeOrigin默认值为false，但我们一般将changeOrigin值设为true
      */
      pathRewrite: {'^/api1': ''} //去除请求前缀，保证交给后台服务器的是正常请求地址(必须配置)
    }),
    proxy('/api2', { 
      target: 'http://localhost:5001',
      changeOrigin: true,
      pathRewrite: {'^/api2': ''}
    })
  )
}
```

说明：

1. 优点：可以配置多个代理，可以灵活的控制请求是否走代理。
2. 缺点：配置繁琐，前端请求资源时必须加前缀。

## redux

### redux(非react插件库)

1. redux是一个专门用于做状态管理的JS库(不是react插件库)。
2. 它可以用在react, angular, vue等项目中, 但基本与react配合使用。
3. 作用: 集中式管理react应用中多个组件共享的状态。

使用redux的场景：

1. 某个组件的状态，需要让其他组件可以随时拿到（共享）。

2. 一个组件需要改变另一个组件的状态（通信）。

![redux原理图](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-redux%E5%8E%9F%E7%90%86%E5%9B%BE-4afb420026a7eb530db1e19fcc20639a-495.png)

#### 3个核心概念

1. action
   
   1. 一般对象类型：
      1. 动作的对象
      2. 包含2个属性
             type：标识属性, 值为字符串, 唯一, 必要属性
             data：数据属性, 值类型任意, 可选属性
      3. 例子：{ type: 'ADD_STUDENT',data:{name: 'tom',age:18} }
   2. 异步action：延迟的动作不想交给组件自身，想交给action
      1. npm i redux-thunk，并配置在store中
      2. 创建action不在返回一般对象，而是返回一个函数，该函数中写异步任务
      3. 异步任务有结果后，分发一个同步的action去真正操作数据

2. reducer
   
   1. 用于初始化状态、加工状态。
   2. 加工时，根据旧的state和action， 产生新的state的纯函数。

3. store
   
   1. 将state、action、reducer联系在一起的对象
   2. 如何得到此对象?
      1) import {createStore} from 'redux'
      2) import reducer from './reducers'
      3) const store = createStore(reducer)
   3. 此对象的功能?
      1) getState(): 得到state
      2) dispatch(action): 分发action, 触发reducer调用, 产生新的state
      3) subscribe(listener): 注册监听, 当产生了新的state时, 自动调用

4. 对于 Redux
   
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

#### 核心API

store.js

```react
import { createStore } from 'redux';
// 引入为Count组件服务的reducer
import countReducer from './count_reducer';
export default createStore(countReducer);
```

UI组件中

```react
import store from './redux';
……
// 分发状态，将新的state交给store
store.dispatch({ type: 'increment', data: 111 });
……
// 获取状态
store.getState();
……
// 监听状态的改变，调用回调
store.subscribe(listener);
```

### react-redux

一个react插件库，专门用来简化react应用中使用redux

https://www.jianshu.com/p/f04e87bcb544

https://juejin.cn/post/7070825045114748964 (自己文章)

## setState

(1)setState(stateChange, [callback])------对象式的setState
        1.stateChange为状态改变对象(该对象可以体现出状态的更改)
        2.callback是可选的回调函数, 它在状态更新完毕、界面也更新后(render调用后)才被调用        
(2)setState(updater, [callback])------函数式的setState
        1.updater为返回stateChange对象的函数。
        2.updater可以接收到state和props。
        4.callback是可选的回调函数, 它在状态更新、界面也更新后(render调用后)才被调用

总结:
          1. 对象式的setState是函数式的setState的简写方式(语法糖)
         2. 使用原则：
              (1).如果新状态不依赖于原状态 => 使用对象方式
              (2).如果新状态依赖于原状态 => 使用函数方式
              (3).如果需要在setState()执行后获取最新的状态数据, 要在第二个callback函数中读取
         3. react 状态的更新是异步的

## lazyLoad

一般路由组件时使用，延迟加载在初次渲染时未用到的组件，用到时才加载。

`React.lazy()` 允许你定义一个动态加载的组件。这有助于缩减 bundle 的体积，并延迟加载在初次渲染时未用到的组件。

`React.Suspense` 可以指定加载指示器（loading indicator），以防其组件树中的某些子组件尚未具备渲染条件。

```js
/ 该组件是动态加载的
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    // 显示 <Spinner> 组件直至 OtherComponent 加载完成
    <React.Suspense fallback={<Spinner />}>
      <div>
        <OtherComponent />
      </div>
    </React.Suspense>
  );
}
```

## ReactDOM

### render

```react
ReactDOM.render(element, container[, callback])
```

在提供的 `container` 里渲染一个 React 元素，并返回对该组件的引用（或者针对无状态组件返回 `null`）。

如果 React 元素之前已经在 `container` 里渲染过，这将会对其执行更新操作，并仅会在必要时改变 DOM 以映射最新的 React 元素。

如果提供了可选的回调函数，该回调将在组件被渲染或更新之后被执行。

`ReactDOM.render()` 会控制你传入容器节点里的内容。当首次调用时，容器节点里的所有 DOM 元素都会被替换，后续的调用则会使用 React 的 DOM 差分算法（DOM diffing algorithm）进行高效的更新。

## Hooks

hook只能在组件顶层使用，不要在循环、条件或嵌套函数中调用，在任何 return 之前调用，原因：

https://blog.csdn.net/weixin_43484007/article/details/124354511

https://blog.csdn.net/gtLBTNq9mr3/article/details/119880486

```javascript
// 当前正在运行的组件
let currentComponent
// 当前 hook 的全局索引
let currentIndex
// 第一次调用 currentIndex 为 0
useState('first') 
// 第二次调用 currentIndex 为 1
useState('second')
```

每次 Hook 的调用都对应一个全局的 index 索引，通过这个索引去当前运行组件 currentComponent 上的 _hooks 数组中查找保存的值，也就是 Hook 返回的 [state, useState]。

原理：

```react

let hookStates = [];
let hookIndex = 0;
function useState(initialState){
  hookStates[hookIndex]=hookStates[hookIndex] || initialState;
  let currentIndex = hookIndex;
  function setState(newState){
    hookStates[currentIndex] = newState;
    render();
  }
  return [hookStates[hookIndex++], setState];
} 
 
 
function useEffect(callback,dependencies){
  if(hookStates[hookIndex]){
      let lastDeps = hookStates[hookIndex];
      let same = dependencies.every((item,index)=>item === lastDeps[index]);
      if(same){
        hookIndex++;
      }else{
        hookStates[hookIndex++] = dependencies;
         setTimeout(callback);
      }
  }else{
     hookStates[hookIndex++] = dependencies;
     setTimeout(callback);
  }
```

相比类组件，相同点：都可以接收props返回react元素

不同点：

1、类组件需要继承 class，函数组件不需要

2、类组件可以访问生命周期方法，函数组件不能，函数数据是通过hooks实现生命周期的方法

3、类组件中可以获取到实例化后的 this，并基于这个 this 做各种各样的事情，理解困难，而函数组件不可以

4、简单组件使用函数组件更轻量级

5、函数组件会捕获 render 内部的状态，类组件状态和渲染是隔离的

6、开类组件内部的逻辑难以实现拆分和复用。（比如高阶组件、Render Props 等可以实现）

7、编程思想：类组件需要创建实例，面向对象，函数组件不需要创建实例，接收输入，返回输出，函数式编程

### State Hook

1. State Hook让函数组件也可以有state状态, 并进行状态数据的读写操作
2. 语法: const [xxx, setXxx] = React.useState(initValue)  
3. useState()说明:
   参数: 第一次初始化指定的值在内部作缓存
   返回值: 包含2个元素的数组, 第1个为内部当前状态值, 第2个为更新状态值的函数
4. setXxx()2种写法:
   setXxx(newValue): 参数为非函数值, 直接指定新的状态值, 内部用其覆盖原来的状态值
   setXxx(value => newValue): 参数为函数, 接收原本的状态值, 返回新的状态值, 内部用其覆盖原来的状态值

5. 使用useState时候，使用push，pop，splice等直接更改数组对象，无法获取到新值，类组件没有问题

   ```react
   function Indicatorfilter() {
     let [num,setNums] = useState([0,1,2,3])
     const test = () => {
       // 直接采用push去更新num, setNums(num)是无法更新num的,取到的num仍然是原值
       // 必须使用num = [...num ,1]浅拷贝，改变了num的内存地址
       // 说明了函数组件中不可以直接修改state
       num.push(1)
       // num = [...num ,1]
       setNums(num)
     }
   return (
       <div className='filter'>
         <div onClick={test}>测试</div>
           <div>
             {num.map((item,index) => (
                 <div key={index}>{item}</div>
             ))}
         </div>
       </div>
     )
   }
   ```


### Effect Hook

Effect Hook 可以让你在函数组件中执行副作用操作(用于模拟类组件中的生命周期钩子)
语法和说明: 

```js
useEffect(() => { 
    // 在此可以执行任何带副作用操作
    return () => { // 在组件卸载前执行
        // 在此做一些收尾工作, 比如清除定时器/取消订阅等
    }
}, [stateValue]) // 如果指定的是[], 回调函数只会在第一次render后执行；不传时，每次render后都执行
```

(4). 可以把 useEffect Hook 看做如下三个函数的组合
        componentDidMount()
        componentDidUpdate()
        componentWillUnmount() 

### Ref Hook

`useRef` 返回一个可变的 ref 对象，其 `.current` 属性被初始化为传入的参数（`initialValue`）。返回的 ref 对象在组件的整个生命周期内保持不变。

useRef 不仅仅是用来管理 DOM ref 的，它还相当于 this , 可以存放任何变量。

useRef 创建的是一个普通 Javascript 对象，和自建一个 `{current: ...}` 对象的唯一区别是，`useRef` 会在每次渲染时返回同一个 ref 对象。

```react
const refContainer = useRef()
```

与createRef区别：createRef 每次渲染都会返回一个新的引用，而 useRef 每次都会返回相同的引用。

所以 createRef只能在类组件使用，useRef只能在函数组件使用。

若在函数组件使用createRef，则永远取不到ref，因为每次渲染都会重新初始化

### LayoutEffect Hook

https://juejin.cn/post/6844904008402862094

（1）共同点： useEffect 与 useLayoutEffect 两者都是用于处理副作用，这些副作用包括改变 DOM、设置订阅、操作定时器等。在函数组件内部操作副作用是不被允许的，所以需要使用这两个函数去处理。

（2）不同点

1. useLayoutEffect 相比 useEffect，通过同步执行状态更新可解决一些特性场景下的页面闪烁问题。
2. useEffect 可以满足百分之99的场景，而且 useLayoutEffect 会阻塞渲染，请谨慎使用。

rc-select组件中，使用这个hook 进行inputvalue变化时，重新赋值inputvalue的width

```jsx
// We measure width and set to the input immediately
useLayoutEffect(() => {
    setInputWidth(measureRef.current.scrollWidth);
}, [inputValue]);
```

### reducer Hook

```javascript
const [state, dispatch] = useReducer(reducer, initialState);
```

https://juejin.cn/post/6844904157892050957

`useState` 是使用 `useReducer` 构建的

- useReducer 是一个用于状态管理的 hook api。

- useReducer(reducer, initialState) 接受2个参数，分别为 reducer 函数 和 初始状态

- reducer(currentState, action) 也是接受2个参数，分别为当前状态和 action，返回一个 new state

使用`useState`和`useReducer`场景

| Scenario                    | useState                | useReducer             |
| --------------------------- | ----------------------- | ---------------------- |
| Type of state               | Number, String, Boolean | Object or Array        |
| Number of state transitions | 1 or 2                  | Too many               |
| Related state transitions   | No                      | Yes                    |
| Business logic              | No business logic       | Complex business logic |
| local vs global             | local                   | global                 |

### Context Hook

接收一个 context 对象（`React.createContext` 的返回值）并返回该 context 的当前值.

当组件上层最近的 `<MyContext.Provider>` 更新时，该 Hook 会触发重渲染，并使用最新传递给 `MyContext` provider 的 context `value` 值

```javascript
const MyContext = React.createContext(defaultValue) 
const value = useContext(MyContext);
```

### Memo Hook

`useMemo` 会返回一个“记忆化”的结果，执行当前传入的函数并返回结果值给声明的变量，且当依赖没变化时返回上一次计算的值。

```javascript
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

函数组件中 `render` 和生命周期钩子在同一个函数作用域中，这也就意味着不再需要 `state` 作中间数据桥梁

### callback Hook

```react
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```

useCallback 缓存钩子函数，useMemo 缓存返回值（计算结果）

https://segmentfault.com/a/1190000039405417

`useCallback(fn, deps) `相当于 `useMemo(() => fn, deps)`

```typescript
type DependencyList = ReadonlyArray<any>;
function useCallback<T extends (...args: any[]) => any>(callback: T, deps: DependencyList): T;
function useMemo<T>(factory: () => T, deps: DependencyList | undefined): T;
```

何时使用useCallback缓存函数？当callback作为一个props传给子组件时，并且子组件有计算依赖父组件传进来的props，因为每次都是不同的函数，为了避免`PureComponent`、`shouldComponentUpdate`、`React.memo`等相关优化失效，父组件使用useCallback缓存函数

### Ref Hook

```react
const refContainer = useRef(initialValue);
```

`useRef` 返回一个可变的 ref 对象，其 `.current` 属性被初始化为传入的参数（`initialValue`）。返回的 ref 对象在组件的整个生命周期内保持不变。

useRef 就像是可以在其 .current 属性中保存一个可变值的“盒子”。 除了可以设置为相应的 DOM 节点外，它可以很方便地保存任何可变值，其类似于在 class 中使用实例字段的方式。这是因为它创建的是一个普通 Javascript 对象。而 useRef() 和自建一个 {current: ...} 对象的唯一区别是，useRef 会在每次渲染时返回同一个 ref 对象。

### 获取上一轮的props和state

```react
function Counter() {
  const [count, setCount] = useState(0);
  const prevCount = usePrevious(count);
  return <h1>Now: {count}, before: {prevCount}</h1>;
}

function usePrevious(value) {
  const ref = useRef();
  useEffect(() => {
    ref.current = value;
  });
  return ref.current;
}

```

## 组件优化

问题：

1. 只要执行setState(),即使不改变状态数据, 组件也会重新render() ==> 效率低
2. 只当前组件重新render(), 就会自动重新render子组件，纵使子组件没有用到父组件的任何数据 ==> 效率低

原因：Component中的shouldComponentUpdate()总是返回true

解决思路：只有当组件的state或props数据发生改变时才重新render()

解决：

React.PureComponent 与 React.Component 很相似。两者的区别在于 React.Component 并未实现 shouldComponentUpdate()，而 React.PureComponent 中以浅层对比 prop 和 state 的方式来实现了该函数。

```
办法1: 
    重写shouldComponentUpdate()方法
    比较新旧state或props数据, 如果有变化才返回true, 如果没有返回false
办法2:  
    使用PureComponent
    PureComponent重写了shouldComponentUpdate(), 只有state或props数据有变化才返回true
    注意: 
        只是进行state和props数据的浅比较, 如果只是数据对象内部数据变了, 返回false  
        不要直接修改state数据, 而是要产生新数据
项目中一般使用PureComponent来优化
```

## ErrorBoundary

Error boundary：用来捕获后代组件错误，渲染出备用页面

特点：只能捕获后代组件生命周期产生的错误，不能捕获自己组件产生的错误和其他组件在合成事件、定时器中产生的错误

使用方式：getDerivedStateFromError配合componentDidCatch

```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

     // 生命周期函数，一旦后台组件报错，就会触发
    static getDerivedStateFromError(error) {
    // 更新 state 使下一次渲染能够显示降级后的 UI
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // 可以将错误日志上报给服务器
    logErrorToMyService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      // 你可以自定义降级后的 UI 并渲染
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children; 
  }
}
```

然后将它作为一个常规组件去使用

```jsx
<ErrorBoundary>
  <MyWidget />
</ErrorBoundary>
```

## 组件通信方式总结

组件间的关系

- 父子组件
- 兄弟组件（非嵌套组件）
- 祖孙组件（跨级组件）

几种通信方式：

    1.props：
        (1)children props
        (2)render props
    2.消息订阅-发布：
        pubs-sub、event等等
    3.集中式管理：
        redux、dva等等
    4.conText:
        生产者-消费者模式

比较好的搭配方式：

    父子组件：props
    兄弟组件：消息订阅-发布、集中式管理
    祖孙组件(跨级组件)：消息订阅-发布、集中式管理、conText

## 其他

### strict Mode

```react
<React.StrictMode></React.StrictMode> 
```

`StrictMode` 目前有助于：

- 识别不安全的生命周期
- 关于使用过时字符串 ref API 的警告
- 关于使用废弃的 findDOMNode 方法的警告
- 检测意外的副作用
- 检测过时的 context API

### ReactNode and ReactElement

A ReactElement is an object with a type and props.

```js
type Key = string | number
interface ReactElement<P = any, T extends string | JSXElementConstructor<any> = string | JSXElementConstructor<any>> {
  type: T;
  props: P;
  key: Key | null;
}
```

A ReactNode is a ReactElement, a ReactFragment, a string, a number or an array of ReactNodes, or null, or undefined, or a boolean:

```js
type ReactText = string | number;
type ReactChild = ReactElement | ReactText;

interface ReactNodeArray extends Array<ReactNode> {}
type ReactFragment = {} | ReactNodeArray;

type ReactNode = ReactChild | ReactFragment | ReactPortal | boolean | null | undefined;
```

### findDomNode和ref

https://stackoverflow.com/questions/31948498/what-is-the-difference-with-using-ref-and-document-getelementbyid-ect-when-usin

https://github.com/zdouble/blog/issues/5

findDomNode和ref都是react中使用的属性

**findDOMNode** only works on mounted components (that is, components that have been placed in the DOM). If you try to call this on a component that has not been mounted yet (like calling findDOMNode() in render() on a component that has yet to be created) an exception will be thrown. 
**document.getElement** 当未找到时返回null

|                     | html标签 | react组件       |
| ------------------- | ------ | ------------- |
| findDOMNode         | dom元素  | 组件挂载时生成的真实dom |
| document.getElement | dom元素  | 组件挂载时生成的真实dom |
| ref                 | dom元素  | 组件实例          |

```react
class TextInput extends React.Component {
  render() {
    return (
      <input type="text" />
    )
  }
}
class App extends React.Component {
  componentDidMount() {
    console.log(this.input) // ProxyComponent {props: {…}, context: {…}, refs: {…}, updater: {…}, __reactstandin__proxyGeneration: 1, …}
    console.log(findDOMNode(this.input)) // <input type="text">
  }

  render() {
    return (
      <TextInput ref={(ref) => {this.input = ref}} />
    )
  }
}
```

### 同时多次更新state如何执行

```react
const onClick = () => {
    setCount(count + 1);
    setCount2(count2 + 1);
  };
```

1. 类组件和函数组件都是，不管多次更新的是一个state 还是不同state，`onClick`方法都会一次性执行完
2. 在onClick内部再访问的state仍然是旧的state。当return渲染中，访问到的state才是新state
3. 执行了多少次setState，函数也会重新执行几次。但多次重复执行的state相同

### PWA渐进式web
