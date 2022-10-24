## router v5

### SPA的理解

1. 单页Web应用（single page web application，SPA）。

2. 整个应用只有**一个完整的页面**。

3. 点击页面中的链接**不会刷新**页面，只会做页面的**局部更新。**

4. 数据都需要通过ajax请求获取, 并在前端异步展现

### BrowserRouter

use the HTML5 history API (`pushState`, `replaceState` and the `popstate` event) 

```react
{/* 最外层必须包裹路由器，BrowserRouter使用的原理是BOM history */}
<BrowserRouter>
    {/* 导航区使用Link标签 */}
    <Link className="list-group-item" to="/about">About</Link>
    <Link className="list-group-item" to="/home">Home</Link>
    {/* 注册路由 path是路径，component是组件 */}
    <Route path="/about" component={About} />
    <Route path="/home" component={Home} />
</BrowserRouter>
```

### HashRouter

use the hash portion of the URL (i.e. `window.location.hash`)

```react
{/* 最外层必须包裹路由器，HashRouter使用的原理是锚点链接 */}
<HashRouter>
    {/* 导航区使用NavLink标签，相比Link标签，可以实现点击时增加样式 */}
    <NavLink activeClassName="active" className="list-group-item" to="/about">About</NavLink>
    <NavLink activeClassName="active" className="list-group-item" to="/home">Home</NavLink>
    {/* Switch包裹后，匹配后不会继续往下寻找，效率高 */}
    <Switch>
        {/* 注册路由 path是路径，component是组件 */}
        <Route exact path="/about" component={About} />
        <Route exact path="/home" component={Home} />
        {/* 一般写在所有路由的最下方，当所有路由都无法匹配时，跳转到Redirect指定的路由 */} 
        <Redirect to="/home" />
    </Switch>
</HashRouter>
```

BrowserRouter与HashRouter的区别
1.底层原理不一样：
BrowserRouter使用的是H5的history API，不兼容IE9及以下版本。HashRouter用的是URL的哈希值。
2.path表现形式不一样
BrowserRouter的路径中没有#,例如：localhost:3000/demo/test
HashRouter的路径包含#,例如：localhost:3000/#/demo/test
3.刷新后对路由state参数的影响
(1).BrowserRouter没有任何影响，因为state保存在history对象中。
(2).HashRouter刷新后会导致路由state参数的丢失！！！ 
4.备注：HashRouter可以用于解决一些路径错误相关的问题。

==嵌套路由必须写上父路由的path值==，路由匹配是按照注册路由的顺序进行：

```react
<ul className="nav nav-tabs">
    <li>
        <MyNavLink to="/home/news">News</MyNavLink>
    </li>
    <li>
        <MyNavLink to="/home/message">Message</MyNavLink>
    </li>
</ul>
{/* 注册路由 */}
<Switch>
    {/* 二级路由， /home/news先匹配到第一级的/home，然后继续Home中又是注册路由，继续匹配到二级路由*/}
    <Route path="/home/news" component={News} />
    <Route path="/home/message" component={Message} />
    <Redirect to="/home/news" />
</Switch>
```

### Link

| 属性            | 意义                                                         |
| --------------- | ------------------------------------------------------------ |
| to              | 路由地址                                                     |
| activeClassName | 选中时样式                                                   |
| replace         | 默认false，表示开启的是push模式，是往栈顶追加；设置为true开启replace模式，替换栈顶 |

注册路由模糊匹配、精准匹配：不加exact为true属性时，是模糊匹配（输入的路径必须包含匹配的路径，且顺序要一致），严格模式一般不加，有些时候开启了严格模式会导致无法匹配二级路由

路由组件与一般组件：

1. 写法不同，一般组件：<Demo /> ; 路由组件：以上例子中About Home都是路由组件

2. 接收到的props不同：

   一般组件：写标签时传递了什么，就能收到什么

   路由组件：接收到3个固定属性：history location match

   | 对象     | 常用属性             | 说明                                                         |
   | -------- | -------------------- | ------------------------------------------------------------ |
   | history  | go(n)                | n代表前进后退几步，例如-1后退一步                            |
   |          | goBack               | 后退                                                         |
   |          | goForward            | 前进                                                         |
   |          | push(path, state)    | 可用来手动实现push路由跳转                                   |
   |          | replace(path, state) | 可用来手动实现replace路由跳转                                |
   | location | pathname             | The path of the URL                                          |
   |          | search               | 使用search方法路由传参时，使用这里接收，接收到的是urlencoded字符串，如 ?name=tom&age=18 |
   |          | state                | 使用state方法路由传参时，使用这里接收，接收到的是对象，如{ name: 'Tom', age: 18} |
   | match    | params               | 使用params方法路由传参时，使用这里接收，接收到的为对象，如{ name: 'Tom', age: 18} |
   |          | path                 | The path pattern used to match. Useful for building nested `<Route>`s |
   |          | url                  | The matched portion of the URL. Useful for building nested `<Link>`s |
   |          | isExact              | `true` if the entire URL was matched (no trailing characters) |

### Route

render some UI when its `path` matches the current URL.

### withRouter

普通组件用witchRouter包裹后拥有路由组件的特性。

```javascript
import React from "react";
import PropTypes from "prop-types";
import { withRouter } from "react-router";

// A simple component that shows the pathname of the current location
class ShowTheLocation extends React.Component {
  static propTypes = {
    match: PropTypes.object.isRequired,
    location: PropTypes.object.isRequired,
    history: PropTypes.object.isRequired
  };

  render() {
    const { match, location, history } = this.props;

    return <div>You are now at {location.pathname}</div>;
  }
}

// Create a new component that is "connected" (to borrow redux
// terminology) to the router.
const ShowTheLocationWithRouter = withRouter(ShowTheLocation);
```

### 向路由传递参数

1、传递params参数

路由链接(携带参数)：`<Link to='/demo/test/tom/18'>detail</Link>`

注册路由(声明接收)：`<Route path="/demo/test/:name/:age" component={Test}/>`

接收参数：this.props.match.params

```jsx
<ul>
    {messageArr.map((msgObj) => {
        return (
            <li key={msgObj.id}>
                <Link to={`/home/message/detail/${msgObj.id}/${msgObj.title}`}>{msgObj.title}</Link>
            </li>
        );
    })}
</ul>
<hr />
<Route path="/home/message/detail/:id/:title" component={Detail} />
```

```react
// 接收params参数
const { id, title } = this.props.match.params;
```

2、传递search参数

urlencoded编码 与 js字符串互相转换

```react
// react脚手架自身下载了querystring库
import qs from 'querystring';

let obj = { name: 'tom', age: 18 }; // js字符串
let objUrlEncode = qs.stringify(obj); 
console.log(objUrlEncode); // name=tom&age=18
console.log(qs.parse(objUrlEncode)); // { name: 'tom', age: 18 }
```

路由链接(携带参数)：<Link to='/demo/test?name=tom&age=18'>详情</Link>

注册路由(无需声明，正常注册即可)：<Route path="/demo/test" component={Test}/>

接收参数：this.props.location.search

备注：获取到的search是urlencoded编码字符串，需要借助querystring解析

```react
<ul>
    {messageArr.map((msgObj) => {
        return (
            <li key={msgObj.id}>
                <Link to={`/home/message/detail/?id=${msgObj.id}&title=${msgObj.title}`}>{msgObj.title}</Link>
            </li>
        );
    })}
</ul>
<hr />
<Route path="/home/message/detail" component={Detail} />
```

```react
// 接收search参数
const { search } = this.props.location;
const { id, title } = qs.parse(search.slice(1));
```

3、传递state参数

路由链接(携带参数)：`<Link to={{ pathname:'/demo/test',  state:{ name:'tom', age:18 } }}>detail</Link> `

注册路由(无需声明，正常注册即可)：`<Route path="/demo/test" component={Test}/>`

接收参数：this.props.location.state

备注：刷新也可以保留住参数

```react
<ul>
    {messageArr.map((msgObj) => {
        return (
            <li key={msgObj.id}>
                <Link to={{ pathname: '/home/message/detail', state: { id: msgObj.id, title: msgObj.title} }}>{msgObj.title}</Link>
            </li>
        );
    })}
</ul>
<Route path="/home/message/detail" component={Detail} />
```

```react
// 接收state参数
const { id, title } = this.props.location.state || {};
```

4、手动实现路由跳转，并携带参数，可以push replace

```react
pushShow =(id, title) => {
    // 实现跳转到Detail组件，且为push跳转
    return () => {
      // 携带params参数
      // this.props.history.push(`/home/message/detail/${id}/${title}`);

      // 携带search参数
      // this.props.history.push(`/home/message/detail/?id=${id}&title=${title}`);

      // 携带state参数
      this.props.history.push(`/home/message/detail`, { id, title });
    }
  };
```

## Router v6

https://mp.weixin.qq.com/s/KKF6kbPMsobdMfG5UQqOXg

## react-router-cache-route

