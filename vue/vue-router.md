## router-viewer

将显示与 url 对应的组件。可以把它放在任何地方

```html
<router-view></router-view>
```

### 嵌套路由

组件内部也可以使用 `router-view`：

```html
<div class="user">
  <h2>User</h2>
  <router-view></router-view>
</div>
```

### 命名视图name

```html
<router-view class="view left-sidebar" name="LeftSidebar"></router-view>
<router-view class="view main-content"></router-view>
<router-view class="view right-sidebar" name="RightSidebar"></router-view>
```

### route

### v-slot

主要使用 `<transition>` 和 `<keep-alive>` 组件来包裹路由组件。

- `Component` : `VNodes `, 传递给 <component>的is prop。
- `route` : 解析出的标准化路由地址。

```html
<router-view v-slot="{ Component, route }">
  <transition :name="route.meta.transition || 'fade'" mode="out-in">
    <keep-alive>
        <template #default>
          <component
            :is="Component"
            :key="route.meta.usePathKey ? route.path : undefined"
          />
        </template>
        <template #fallback> Loading... </template>
    </keep-alive>
  </transition>
</router-view>
```

## router-link

| prop               | 类型                                                         | 默认                                            | 介绍                                                         |
| ------------------ | ------------------------------------------------------------ | ----------------------------------------------- | ------------------------------------------------------------ |
| to                 |                                                              |                                                 |                                                              |
| replace            | `boolean`                                                    | false                                           |                                                              |
| active-class       | string                                                       | "router-link-active" (或者全局 linkActiveClass) | 链接激活时，应用于渲染的 `<a>` 的 class                      |
| aria-current-value | `'page' |'step' |'location' |'date' |'time' |'true' |'false'` (`string`) | 'page'                                          | 当链接激活时，传递给属性 `aria-current` 的值                 |
| custom             | `boolean`                                                    | false                                           | 是否应该将其内容包裹在 `<a>` 元素中。使用v-slot创建自定义 RouterLink 时很有用 |
| exact-active-class | string                                                       | "router-link-active" (或者全局 linkActiveClass) | 链接精准激活时，应用于渲染的 `<a>` 的 class。                |

### to

表示目标路由的链接。当被点击后，内部会立刻把 `to` 的值传到 `router.push()`

```html
<!-- 字符串 -->
<router-link to="/home">Home</router-link>
<router-link :to="{ path: '/home' }">Home</router-link>
<!-- 命名的路由 -->
<router-link :to="{ name: 'user', params: { userId: '123' }}">User</router-link>
<!-- 带查询参数，下面的结果为 `/register?plan=private` -->
<router-link :to="{ path: '/register', query: { plan: 'private' }}">
  Register
</router-link>
```

### replace

设置 `replace` 属性，当点击时，会调用 `router.replace()`，而不是 `router.push()`，所以导航后不会留下历史记录

```html
<router-link to="/abc" replace></router-link>
```

### v-slot

```
<router-link
  to="/about"
  custom
  v-slot="{ href, route, navigate, isActive, isExactActive }"
>
  <NavLink :active="isActive" :href="href" @click="navigate">
    {{ route.fullPath }}
  </NavLink>
</router-link>
```

- `href`：解析后的 URL。将会作为一个 `<a>` 元素的 `href` 属性。如果什么都没提供，则它会包含 `base`。
- `route`：解析后的规范化的地址。
- `navigate`：触发导航的函数。 **会在必要时自动阻止事件**，和 `router-link` 一样。例如：`ctrl` 或者 `cmd` + 点击仍然会被 `navigate` 忽略。
- `isActive`：如果需要应用 `active class`，则为 `true`。允许应用一个任意的 class。
- `isExactActive`：如果需要应用 `exact active class`，则为 `true`。允许应用一个任意的 class。



## createRouter

```typescript
function createRouter(options: RouterOptions): Router
interface RouterOptions {
  history: RouterHistory;
  routes: RouteRecordRaw[]
  linkActiveClass?: string
  linkExactActiveClass?: string
  parseQuery?: (searchQuery: string) => Record<string, (string | null)[] | string | null>
  /** 在页面之间导航时控制滚动的函数。可以返回一个 Promise 来延迟滚动 */
  scrollBehavior?: RouterScrollBehavior
}

const router = VueRouter.createRouter({
  history: VueRouter.createWebHashHistory(),
  // history: createWebHistory(),
  routes,
  strict: true, // applies to all routes
})
```

比说，使用 `qs` 包来解析查询，你可以同时提供 `parseQuery` 和 `stringifyQuery`：

```
import qs from 'qs'
createRouter({
  // 其他配置...
  parseQuery: qs.parse,
  stringifyQuery: qs.stringify,
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

路由组件传参：当 `props` 设置为 `true` 时，`route.params` 将被设置为组件的 props。

```javascript
const User = {
  // 请确保添加一个与路由参数完全相同的 prop 名
  props: ['id'],
  template: '<div>User {{ id }}</div>'
}
const routes = [{ path: '/user/:id', component: User, props: true }]
```



## 重定向

在写 `redirect` 的时候，可以省略 `component` 配置，因为它从来没有被直接访问过，所以没有组件要渲染。唯一的例外是嵌套路由：如果一个路由记录有 `children` 和 `redirect` 属性，它也应该有 `component` 属性

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
const routes = [
  {
    // 将总是把/users/123/posts重定向到/users/123/profile。
    path: '/users/:id/posts',
    redirect: to => {
      // 该函数接收目标路由作为参数
      // 相对位置不以`/`开头
      // 或 { path: 'profile'}
      return 'profile'
    },
  },
]
```

## 别名

```javascript
// 将 / 别名为 /home，意味着当用户访问 /home 时，URL 仍然是 /home，但会被匹配为用户正在访问 /
const routes = [{ path: '/', component: Homepage, alias: '/home' }]
const routes = [
  {
    path: '/users',
    component: UsersLayout,
    children: [
      // 为这 3 个 URL 呈现 UserList
      // - /users
      // - /users/list
      // - /people
      { path: '', component: UserList, alias: ['/people', 'list'] },
    ],
  },
]
```

## 切换路由

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

## 导航守卫

```typescript
interface NavigationGuardWithThis<T> {
    (this: T, to: RouteLocationNormalized, from: RouteLocationNormalized, next: NavigationGuardNext): NavigationGuardReturn | Promise<NavigationGuardReturn>;
}
```

### 全局前置守卫

当一个导航触发时，全局前置守卫按照创建顺序调用。守卫是异步解析执行，此时导航在所有守卫 resolve 完之前一直处于等待中

添加一个导航守卫，在任何导航前执行。返回一个删除已注册守卫的函数。

```typescript
router.beforeEach(async (to, from) => {})
```

回调：

- to：即将要进入的目标
- from：当前导航正要离开的路由

- 返回：
  - false: 取消当前的导航
  - 一个路由地址: 通过一个路由地址跳转到一个不同的地址

### 全局解析守卫

































