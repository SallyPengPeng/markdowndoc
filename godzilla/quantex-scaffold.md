## history

基于浏览器的history封装，可以不用关注各个环境的差异。

`createBrowserHistory` returns a `BrowserHistory` instance

```javascript
import { createBrowserHistory } from 'history';
let history = createBrowserHistory();
```

## js-cookie

A simple, lightweight JavaScript API for handling cookies

Cookie 是一小段文本信息，伴随着用户请求和页面在 Web 服务器和浏览器之间传递。用户每次访问站点时，Web 应用程序都可以读取 Cookie 包含的信息。

```javascript
Cookies.set('foo', 'bar')
Cookies.set('name', 'value', { expires: 7 })
Cookies.get('name') // => 'value'
Cookies.get('nothing') // => undefined
```

## single-spa

用于前端微服务，可以在同一个页面使用多个框架

```react
singleSpa.registerApplication(
  'appName',
  () => System.import('appName'),
  location => location.pathname.startsWith('appName'),
);
```

arguments

- appName: string

  App name that single-spa will register and reference this application with, and will be labelled with in dev tools.

- applicationOrLoadingFn: () => <Function | Promise>

  Must be a loading function that either returns the resolved application or a promise.

- activityFn: (location) => boolean

  Must be a pure function. The function is called with `window.location` as the first argument and should return a truthy value whenever the application should be active.

- customProps?: Object | () => Object

  Will be passed to the application during each lifecycle method.

## react-router-cache-route

Using `Route`, component can not be cached while going forward or back which lead to **losing data and interaction**

Component would be unmounted when `Route` was unmatched

After reading source code of `Route` we found that using `children` prop as a function could help to control rendering behavior.

```react
import React from 'react'
import { HashRouter as Router, Route } from 'react-router-dom'
import CacheRoute, { CacheSwitch } from 'react-router-cache-route'

import List from './views/List'
import Item from './views/Item'

const App = () => (
  <Router>
    <CacheSwitch>
      <CacheRoute exact path="/list" component={List} />
      <Route exact path="/item/:id" component={Item} />
      <Route render={() => <div>404 Not Found</div>} />
    </CacheSwitch>
  </Router>
)

export default App
```

## react-loadable

https://github.com/chinanf-boy/react-loadable-zh

A higher order component for loading components with dynamic imports.

在任何模块渲染到应用程序之前 动态加载它

```react
import Loadable from 'react-loadable';
const L = (url: string) => {
  const CompL = Loadable({
    loader: () => import(`pages/${url}`), // 动态导入组件
    loading: SkeletonLoading, // 模块加载或有错误时展示组件
    delay: 200,
    // A function to customize the rendering of loaded modules
    render(loaded, props) {
      let Comp = loaded.default;
      const { forwardedRef, ...rest } = props;
      return <Comp ref={forwardedRef} {...rest} />;
    },
  });
  return React.forwardRef((props, ref) => {
    return <CompL forwardedRef={ref} {...props} />;
  });
};
```

## 通过portal应用入口

在quantex-scaffold中：

webpack html插件使用index.html作为模板文件，index.js作为入口文件，打包后引入index.html。

1. webpack入口文件是`index.js`，给window增加了很多属性和方法，初始化了appWindow的sessionStore localStore

2. 进入Router.ts文件，使用了antd 和 quantex-design的全局配置，设置路由

   1. 先进入/路由，进入`login`组件，获取用户信息，使用获取信息执行 `this.store.afterLogin`

      设置appWindow的一些属性，实际也存储在了window对象的localStorage 和sessionStorage

   2. 非工作台模式时，使用history切换到/app路由

3. /app路由，进入App组件

   this.state.menuMap 所有的菜单

   ```javascript
   [
     ["/iframe_godzilla?appId=pks&page=positionOverview/BondPosition", 
       {
         appId: "cms"
         ……
         url: "/iframe_godzilla?appId=pks&page=positionOverview/BondPosition"}
    	],
     ["portal/system/User", 
       {
         appId: "portal"
   			……
         url: "portal/system/User"}
    	]
   ]
   ```

   this.state.pageMap 当前打开的页面

   ```javascript
   [
       [
           "portal/system/User",
           {
               "className": null,
               "name": "用户管理",
               "link": "portal/system/User"
           }
       ],
     // 只有portal才能加载其他子应用，会出现这种形式子应用的pageMap,其他子应用只能加载自己的pageMap
       [
           "/iframe_godzilla?appId=pks&page=positionOverview/BondPosition",
           {
               "className": null,
               "name": "债券持仓",
               "link": "/iframe_godzilla?appId=pks&page=positionOverview/BondPosition",
               "appId": "pks"
           }
       ]
   ]
   ```

   1. Header：快速搜索：选中某一项后，实际调用Sider中`onMenuItemClick`事件，改变了pageMap相关。

   2. Sider：选中某一项后，改变pageMap相关。

   3. TabList：根据pageMap渲染tab标题部分

   4. PageList：先过滤子应用的页面，每个子应用只保留第一个页面。使用CacheSwitch  CacheRoute渲染页面路由并缓存

      ​	portal页面：path = app/portal/system/User

      ​	子应用页面：path = app/portal/pks

      Page：CacheRoute 渲染

      ```react
      <Page ref={`page/${item.link}`} name={item.name} url={item.link} {...this.getProps(item)} />
      ```

      1. 如果是portal页面，依赖moduleMap(.tmp/path.json中路径map得到)，动态加载页面使用iframe嵌入展示

         ```javascript
         // moduleMap结构
         [[
           'portal/system/User', // 动态导入脚手架.tmp/path.json文件下存储的index文件路径
           LoadableComponent, // 动态加载组件
         ]]
         ```

      2. 如果是子应用，定位子应用路径，获取资源，使用iframe嵌入展示

         每一个子应用只会有一个CacheRoute
         
         子应用资源获取：`http://pks-gateway.sigma6-dev.bj.iquantex.com/pks?code=admin&isInIframe=true&page=CashOverview%2FOverview`
         
         然后使用iframe嵌入：`<iframe className="iframe" src={url} />`

      3. 当点击Sider或者TabList切换`curSelectedPage`时，会根据pageMap重新渲染路由，并且会执行`notifyTabChange`方法，当`path !== location?.pathname`时，需要重新设置当前页面`history?.push(path)`，从而进入相匹配路由。

         如果是iframe页面。
         
         1. 保存实例在`window.iframeContainers`
         
            ```javascript
            if (!window.iframeContainers.has(appId)) {
              window.iframeContainers.set(appId, {
                appId,
                instance: null,
                page,
              });
            }
            ```
         
         2. 执行`postMessageToIframe`
         
            ```javascript
            window.postMessageToIframe({
              appId: 'pks',
              page: 'CashOverview/Overview',
              type: 'openPage',
              props: targetTab.props,
            });
            ```
            
         
         所以此时portal中`window.iframeContainers`为：
         
         ```javascript
            [['pks', {
                appId: 'pks',
                instance: null,
                page: 'CashOverview/Overview',
              	waitOpenPage: 'CashOverview/Overview',
              	……
              }]]
         ```

## 子应用加载第一个页面

1. webpack入口文件是`index.js`，给window增加了很多属性和方法，初始化了appWindow的sessionStore localStore

2. 进入Router.ts文件，使用了antd 和 quantex-design的全局配置，设置路由

   注：此时的访问地址是：`http://pks-gateway.sigma6-dev.bj.iquantex.com/pks?code=admin&isInIframe=true&page=CashOverview%2FOverview`

   1. 先进入/路由，进入`login`组件，`window.isInIframe`设置为true，获取用户信息，并执行`afterLogin`

   2. 非工作台模式时，使用history切换到/app路由

3. /app路由，进入App组件

   1. 子组件：`componentDidMount`执行`iframeReady()`向父组件`postMessage`

      ```javascript
      if (window.isInIframe) {  // NOTE: 当项目使用 iframe 模式打开时, 通知父窗口, iframe 加载完成
          window.parent.postMessage(
            {
              type: 'iframeReady',
              appId: process.env.APP_ID,
            },
            '*'
          );
        }
      ```

      1. 父组件：监听了`message`事件，所以接收事件，父组件是portal，消息type为`iframeReady`，所以：

         ```javascript
         // 父组件中`window.iframeContainers`为
         [['pks', {
             appId: 'pks',
             instance: [pks子应用的window],
             page: 'CashOverview/Overview',
           	waitOpenPage: 'CashOverview/Overview',
           	……
           }]]
         ```

      2. 父组件：`message`监听事件中，又执行`window.postMessageToIframe`

         ```javascript
         if (iframeInfo.waitOpenPage) {
           window.postMessageToIframe({
             appId,
             page: iframeInfo.waitOpenPage,
             type: 'openPage',
             ...iframeInfo.params,
           });
           iframeInfo.waitOpenPage = null;
           iframeInfo.params = null;
         }
         ```

         2.1. window.postMessageToIframe

         ​	因为此时instance存在，所以执行父组件向子组件`postMessage`:

         ```javascript
         iframe.postMessage(
         {
           type: 'openPage',
           page: 'CashOverview/Overview',
           ...rest,
         },
         	'*',
         );
         ```
      
         2.2. 执行完成后
      
         ```javascript
         // 父组件中`window.iframeContainers`为
         [['pks', {
             appId: 'pks',
             instance: [pks子应用的window],
             page: 'CashOverview/Overview',
           	waitOpenPage: null,
           	……
           }]]
         ```
      
      3. 子组件：监听了`message`事件，接收到父组件的message，非portal应用，所以执行` pageEventListen(event)`，匹配到`openPage`，所以执行`window.openTab`
      
         ```javascript
         window.openTab({ url: 'CashOverview/Overview', name: undefined, appId: undefined, props: undefined, eventType: 'openPage' });
         ```
      
      4. 子组件：执行`window.openMenuByMain({ type: 'tab', ...config });` 实际上是模拟点击了一下菜单
      
         执行后，config为：
      
         ```javascript
         { url: 'pks/CashOverview/Overview', name: undefined, appId: 'pks', props: undefined, eventType: 'tab' }
         ```
      
         执行`window.openWindow({ url: 'pks/CashOverview/Overview' ,title: '资金总览',name: '资金总览' })`
      
      5. 子组件执行`handleMenuItemClick(config)`，初始化pageMap，定位到`CashOverview/Overview`

   2. 因为是`window.isInIframe`是true，所以直接渲染页面PageList

      ```javascript
      // this.state.menuMap
      [
        ["pks/CashOverview/Overview", 
         {
           appId: "pks"
           name: "资金总览",
           ……
           url: "pks/CashOverview/Overview"}
        ]
      ]
      // this.state.pageMap
      [
        [
          "pks/CashOverview/Overview",
          {
            link: "pks/CashOverview/Overview",
            name: "资金总览",
            props: {}
          }
        ],
      ]
      ```


## 应用页面切换

   portal应用，`curSelectedPage`变更，父应用中子应用的route path 只有`/${appId}`这一种，因为使用的是CacheRoute，子应用未卸载，所以此时子应用路由还是最后一次点击的路由。

通过portal的` onTabSwitch`使当前选择页面变化，从而portal重新执行render

### portal页面变更到另一portal页面

路由切换

### 从portal页面变更到子应用页面

   1. path变更，跳转到app/${appId}路由，执行
   
      ```javascript
      window.postMessageToIframe({
        appId: 'pks',
        page: 'cash/CashDetail',
        type: 'openPage',
        props: targetTab.props
      });
      ```

   2. 因为子应用非第一个页面加载，所以子应用instance已经存在。所以父应用给子应用`postMessage`
   
      ```javascript
      iframe.postMessage(
        {
          type: 'openPage',
          page: 'cash/CashDetail',
          ...rest,
      	}, '*');
      ```

   2. 子应用接收message，执行`pageEventListen`，匹配到`openPage`，所以执行`openTab`

      ```javascript
      window.openTab({ url: 'cash/CashDetail', name: undefined, appId: undefined, props: undefined, eventType: 'openPage' });
      ```
   
      最终执行`window.openMenuByMain`，config为
   
      ```javascript
      { type: 'tab', url: 'cash/CashDetail', appId: 'pks'};
      ```

### 从子应用页面变更到同一子应用的另一个页面

portal的路由不变，仍然是`app/${appId}`，但会执行`window.postMessageToIframe`，与portal页面切换到子应用页面类似

### 从A子应用页面变更到B子应用页面

   portal的路由变化，是`app/${appId}`，执行`window.postMessageToIframe`，与portal页面切换到子应用页面类似

## 子应用单独启动

因为cookies的Domain限制了为`.sigma6-dev.bj.iquantex.com`

若主应用地址是：`http://portal-gateway.sigma6-dev.bj.iquantex.com/`

则子应用本地开发地址应为：`http://sigma6-dev.bj.iquantex.com:8881/`，并且在host中配置`127.0.0.1  sigma6-dev.bj.iquantex.com`。

这样子应用才可以共享父应用的Cookies，查询接口才能正常。

## 工作台

### sider渲染WorkbenchList

```javascript
data.map((item: any) => item.config);
workbenchs: IWorkbenchData = [{
  autoSaveComParams: false,
  height: 1,
  id: "layout-1625039992053",
  isDefaultOpen: false,
  name: "风控工作台",
  type: "user",
  userCode: "admin",
  menuList: []
  tabs: [
    { // tabs只有一项
      id: "tab-1648793939701"
      layouts: [
  			{ // layouts可能有多个，是一个工作台中多个不同的面板
          id: "row-1"
          linkageValue: "white",
          name: "用户管理",
          url: "portal/system/User",
          props: {
            direction: [1, -1],
            flex: 0.5,
         }
			 },
       {
        id: "row-2"
        linkageValue: "white",
        name: "产品组管理",
        url: "/iframe_godzilla?appId=cs&page=FundGroup",
        props: {
          direction: [1, -1],
          flex: 0.5,
    	}
       }

      ]
		},
  ],
}];
```

### 新增工作台

#### 弹框提交要素

```javascript
createConfig = {
  id: `layout-${new Date().getTime()}`, // 创建的时候就生成唯一 id, 后续保存工作台时也使用此 id
  type: category,
  templateId: activeLayout,
  name: workbenchName,
  isDefault: isDefaultWorkbench,
  autoSaveComParams: isSaveComParams,
  height: workbenchHeight / 100,
}
```

#### window.openTab

```javascript
window.openTab({
  name: createConfig.name,
  url: 'FlexWorkbench',
  props: {
    key: createConfig.id,
    createConfig,
  },
});
```

#### window.openMenuByMain

```javascript
{
  name: createConfig.name,
  url: 'FlexWorkbench',
  props: {
    key: createConfig.id,
    createConfig,
  },
  type: 'tab', 
}
```

#### FlexWorkbench

```react
<FlexLayoutContext.Provider value={contextValue}>
  {workbenchData?.tabs.map((tab: any, index: number) => {
    return <Layout key={tab.id} layout={tab} dataPath={['tabs', index, 'layouts']} />;
  })}
  <Modal store={modal} />
</FlexLayoutContext.Provider>

props: {
    key: createConfig.id,
    createConfig,
},
```

#### 获取菜单和工作台里面组件数据

```javascript
// /api/v3/menus/list接口返回数据，并经过portal数据处理
componentMenu = [{
  id: 728,
  appId: "cs",
  className: null,
  describe: null,
  enable: 1,
  icon: null,
  id: 728,
  isDefaultOpen: 0,
  lcode: 90,
  lev: 3,
  name: "产品组管理",
  openType: "tab",
  pId: 726,
  rcode: 91,
  site: "ims",
  status: 1,
  type: ["menu", "component"],
  url: '/iframe_godzilla?appId=cs&page=FundGroup'
},
{
  appId: "portal",
  className: null,
  describe: null,
  enable: 1,
  icon: null,
  id: 629,
  isDefaultOpen: 0,
  lcode: 182,
  lev: 3,
  name: "菜单管理",
  openType: "tab",
  pId: 622,
  rcode: 183,
  site: "ims",
  status: 1,
  type: ["menu", "component"],
  url: "system/Menu",
}];
```

新建时DB没有缓存，所以是newLayout，并缓存到DB

```javascript
const templateTab = {
  ...template[createConfig.templateId],
  id: `tab-${new Date().getTime()}`,
};
const newLayout = {
  id: createConfig.id,
  name: createConfig.name, // 工作台名称
  userCode: appWindow.localStore.getItem('userCode'),
  type: createConfig.workbenchType, // 如果是 system, 则认为是系统工作台(系统场景)
  isDefaultOpen: !!createConfig.isDefault, // 是否默认打开
  autoSaveComParams: !!createConfig.autoSaveComParams, // 是否自动保存参数
  height: createConfig.height,
  tabs: [templateTab],
};

workbenchData = {
  ...newLayout,
  menuList: componentMenu,
}
```

#### Layout

 `ReflexContainer`是大容器，包含若干个`ReflexElement`，而`ReflexElement`中，根据layout.layouts决定，如果没有panels就直接是layoutItem

```react
<ReflexContainer {...containerProps}>
  <ReflexElement {...panelProps}><LayoutItem /></ReflexElement> {/*不包含panels*/}
   {/*包含panels，执行递归获取结构*/}
  <ReflexContainer {...container1Props}>
  	<ReflexElement {...panel1Props}><LayoutItem /></ReflexElement>
	</ReflexContainer>
</ReflexContainer>
```

#### LayoutItem

`<LayoutItem dataPath={path} panel={panel} pOrientation={pOrientation} />`

新增工作台，所以props.panel.url为undefined

```react
const menus = [{ id: props.panel.id, url: props.panel.url }];
```

```react
<div key={menu.url || menu.id} className={styles.content}>
  <div className={styles.plus} onClick={addMenu}>
    <span>+</span>
    <div>从组件库添加</div>
  </div>
</div>
```

### 新增组件

```javascript
componentMenuTree
componentMenuMap
```

### 选中工作台

1、`openWorkbench(data, 'list');`

其中data是工作台的全部数据

2、openTab

```javascript
window.openTab({
  name: data.name,
  url: 'FlexWorkbench',
  props: {
    key: data.id, // 选中工作台的id
    id: data.id,
  },
});
```

3、进入FlexWorkbench，DB有缓存，直接从DB获取工作台配置

4、在layoutItem组件中，拼接工作台所需url

```javascript
if (url.includes(':') || url.startsWith('/iframe')) {
  // 如果路径匹配到的是可直接访问链接,则返回 iframe
  if (url.startsWith('/iframe')) {
    // 通过服务端来重定向到指定访问链接
    url += `&theme=${window.getCurrentTheme()}`;
    if (!url.includes('isInWorkbench')) {
      url += `&isInWorkbench=true`;
    }
    // 将workbenchId和layoutItemId拼接到url上
    if (!url.includes('workbenchId')) {
      url += `&workbenchId=${workbenchData.id}&layoutItemId=${props.panel.id}`;
    }
  }
}
```































