## router-viewer

```html
<div id="app">
  <router-view></router-view>
</div>
```

**嵌套路由**

组件内部也可以使用router-view：

```html
<div class="user">
  <h2>User {{ $route.params.id }}</h2>
  <router-view></router-view>
</div>
```

**命名视图**

```html
<router-view class="view left-sidebar" name="LeftSidebar"></router-view>
<router-view class="view main-content"></router-view>
<router-view class="view right-sidebar" name="RightSidebar"></router-view>
```

router定义：

```javascript
const router = createRouter({
  history: createWebHashHistory(),
  routes: [
    {
      path: '/',
      components: {
        default: Home,
        // LeftSidebar: LeftSidebar 的缩写
        LeftSidebar,
        // 它们与 `<router-view>` 上的 `name` 属性匹配
        RightSidebar,
      },
    },
  ],
})
```

## createRouter

```javascript
const router = VueRouter.createRouter({
  history: VueRouter.createWebHashHistory(),
  routes,
  strict: true, // applies to all routes
})
```

## route

```javascript
const routes = [
  { path: '/', component: Home },
  { path: '/about', component: About },
  // 动态字段以冒号开始
  { path: '/users/:id', component: User },
  // 将匹配所有内容并将其放在 `$route.params.pathMatch` 下
  { path: '/:pathMatch(.*)*', name: 'NotFound', component: NotFound },
  // 将匹配以 `/user-` 开头的所有内容，并将其放在 `$route.params.afterUser` 下
  { path: '/user-:afterUser(.*)', component: UserGeneric },
  // /:orderId -> 仅匹配数字
  { path: '/:orderId(\\d+)' },
    // /:chapters ->  匹配 /one, /one/two, /one/two/three, 等
  { path: '/:chapters+' },
  // /:chapters -> 匹配 /, /one, /one/two, /one/two/three, 等
  { path: '/:chapters*' },
  // 匹配 /users 和 /users/posva
  { path: '/users/:userId?' },
  // 命名路由
  { path: '/user/:username', name: 'user', component: User },
]
```

嵌套路由结构：

```javascript
const routes = [
  {
    path: '/user/:id',
    component: User,
    children: [
      {
        // 当 /user/:id/profile 匹配成功
        // UserProfile 将被渲染到 User 的 <router-view> 内部
        path: 'profile',
        component: UserProfile,
      }
    ],
  },
]
```

## 导航

**push**

1. 如果提供了 `path`，`params` 会被忽略
2. 当指定 `params` 时，可提供 `string` 或 `number` 参数（或者对于可重复的参数可提供一个数组）。任何其他类型（如 `undefined`、`false` 等）都将被自动字符串化。对于可重复的参数，可以提供一个空字符串（`""`）来跳过
3. `router.push` 和其他导航方法都会返回一个 *Promise*，可以等导航完成后才知道是成功还是失败

```javascript
// 字符串路径
router.push('/users/eduardo')
// 带有路径的对象
router.push({ path: '/users/eduardo' })
// 命名的路由，并加上参数，让路由建立 url
router.push({ name: 'user', params: { username: 'eduardo' } })
// 带查询参数，结果是 /register?plan=private
router.push({ path: '/register', query: { plan: 'private' } })
// 带 hash，结果是 /about#team
router.push({ path: '/about', hash: '#team' })
```

**replace**

```javascript
router.push({ path: '/home', replace: true })
// 相当于
router.replace({ path: '/home' })
```

**go**

该方法采用一个整数作为参数，表示在历史堆栈中前进或后退多少步

```javascript
// 向前移动一条记录，与 router.forward() 相同
router.go(1)
// 返回一条记录，与 router.back() 相同
router.go(-1)
// 前进 3 条记录
router.go(3)
// 如果没有那么多记录，静默失败
router.go(-100)
router.go(100)
```

## 重定向

```javascript
const routes = [{ path: '/home', redirect: '/' }]
// 命名路由
const routes = [{ path: '/home', redirect: { name: 'homepage' } }]
// 方法重定向
const routes = [
  {
    // /search/screens -> /search?q=screens
    path: '/search/:searchText',
    redirect: to => {
      // 方法接收目标路由作为参数
      // return 重定向的字符串路径/路径对象
      return { path: '/search', query: { q: to.params.searchText } }
    },
  }
]
// 相对重定向

```

