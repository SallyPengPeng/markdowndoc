## define

defineProps：规范传递数据的格式

```javascript
// 运行声明和类型声明二选一，否则报错
// 运行声明
let props = defineProps({ value: Number})

// ts类型声明，只能使用withDefaults提供默认值
export interface Props {
  msg?: string
  labels?: string[]
}
const props = withDefaults(defineProps<Props>(), {
  msg: 'hello',
  labels: () => ['one', 'two']
})
```

defineEmits：定义对外“发射”的数据

```javascript
// 运行声明和类型声明二选一，否则报错
// 运行声明
let emits = defineEmits('update-rate')
function onRate(num){ 
	emits('update-rate',num)
}
// ts类型声明
const emit = defineEmits<{
  (e: 'change', id: number): void
  (e: 'update', value: string): void
}>()
```

`defineExpose`：指定在 `<script setup>` 组件中要暴露出去的属性

```javascript
const handleNodeClick = () => {
 console.log('要执行的方法')
}
//将方法暴露出
defineExpose({ handleNodeClick})
```

```vue
// 父组件通过ref
<tree :show="show" ref="treeRef"></tree>  

const treeRef = ref()
const handleClick = () => {
    // 获取ref中的子组件方法handleNodeClick()
 treeRef.value.handleNodeClick()
}
```

## props

props声明

```javascript
export default {
  props: ['foo'],
  created() {
    // props 会暴露到 `this` 上
    console.log(this.foo)
  }
}
// 对象形式
export default {
  props: {
    title: String,
    likes: Number,
    // 必传，且为 String 类型
    propC: {
      type: Number,
      required: true,
      default: 100,
    },
    // 对象类型的默认值
    propE: {
      type: Object,
      // 对象或者数组应当用工厂函数返回。
      // 工厂函数会收到组件所接收的原始 props
      // 作为参数
      default(rawProps) {
        return { message: 'hello' }
      }
    },
    // 自定义类型校验函数
    propF: {
      validator(value) {
        // The value must match one of these strings
        return ['success', 'warning', 'danger'].includes(value)
      }
    },
    // 函数类型的默认值
    propG: {
      type: Function,
      // 不像对象或数组的默认，这不是一个工厂函数。这会是一个用来作为默认值的函数
      default() {
        return 'Default function'
      }
    }
  }
}
```

传递props

```vue
<!-- 虽然 `42` false是常量，还是需要使用 v-bind -->
<!-- 因为这是一个 JavaScript 表达式而不是一个字符串 -->
<BlogPost :likes="42" />
<BlogPost :is-published="false" />
<BlogPost :comment-ids="[234, 266, 273]" />
<BlogPost
  :author="{
    name: 'Veronica',
    company: 'Veridian Dynamics'
  }"
 />

<!-- 根据一个变量的值动态传入 -->
<BlogPost :likes="post.likes" />
```

透传attr：透传 attribute”指的是传递给一个组件，却没有被该组件声明为props 或emits 的 attribute 或者 `v-on` 事件监听器。比如 `class`、`style` 和 `id` `v-on` 。

## 触发与监听事件

```vue
<!-- MyComponent -->
<button @click="$emit('someEvent')">click me</button>

<!-- 组件实例 -->
export default {
  methods: {
    submit() {
      this.$emit('submit')
    }
  }
}

<!-- 父组件监听 -->
<MyComponent @some-event="callback" />
```

声明触发事件

```javascript
export default {
  emits: ['inFocus', 'submit']
}

export default {
  emits: {
    submit(payload) {
      // 通过返回值为 `true` 还是为 `false` 来判断
      // 验证是否通过
    }
  }
}
```

## script setup和普通的 script

普通的 `<script>` 在有这些需要的情况下或许会被使用到：

- 声明无法在 `<script setup>` 中声明的选项，例如 `inheritAttrs` 或插件的自定义选项。
- 声明模块的具名导出 (named exports)。
- 运行只需要在模块作用域执行一次的副作用，或是创建单例对象。

```vue
<script>
// 普通 <script>, 在模块作用域下执行 (仅一次)
runSideEffectOnce()

// 声明额外的选项
export default {
  inheritAttrs: false,
  customOptions: {}
}
</script>

<script setup>
// 在 setup() 作用域中执行 (对每个实例皆如此)
</script>
```

## 全局API

```javascript
import { createApp } from 'vue'
const app = createApp(/* ... */)

app.mount('#app')
app.provide('message', 'hello')
// 注册一个选项对象
app.component('my-component', {/* ... */})
```

### nextClick

在状态改变后立即使用，以等待 DOM 更新完成。可以传递一个回调函数，或者 await 返回的 Promise。

```typescript
function nextTick(callback?: () => void): Promise<void>
```

```javascript
import { nextTick } from 'vue';
increment() {
  // DOM 还未更新
  console.log(document.getElementById('counter').textContent) // 0
  await nextTick()
  // DOM 此时已经更新
  console.log(document.getElementById('counter').textContent) // 1
}
```

### defineComponent

在定义 Vue 组件时提供类型推导的辅助函数

```typescript
const Foo = defineComponent(/* ... */)
type FooInstance = InstanceType<typeof Foo>
```

### defineAsyncComponent

异步组件：懒加载

```javascript
import { defineAsyncComponent } from 'vue'
const AsyncComp = defineAsyncComponent(() =>
  import('./components/MyComponent.vue')
)
```

```javascript
import { defineAsyncComponent } from 'vue'

const AsyncComp = defineAsyncComponent(() => {
  return new Promise((resolve, reject) => {
    // ...从服务器获取组件
    resolve(/* 获取到的组件 */)
  })
})
// ... 像使用其他一般组件一样使用 `AsyncComp`
const AsyncComp = defineAsyncComponent({
  // 加载函数
  loader: () => import('./Foo.vue'),

  // 加载异步组件时使用的组件
  loadingComponent: LoadingComponent,
  // 展示加载组件前的延迟时间，默认为 200ms
  delay: 200,

  // 加载失败后展示的组件
  errorComponent: ErrorComponent,
  // 如果提供了一个 timeout 时间限制，并超时了
  // 也会显示这里配置的报错组件，默认值是：Infinity
  timeout: 3000
})
```

## 组合式API

### setup

在 `setup()` 函数中返回的对象会暴露给模板和组件实例

```javascript
setup(props, context) {
  console.log(props.title)
}
```

**props：**若解构 `props` ，解构出的变量会丢失响应性。推荐通过 `props.xxx` 的形式来使用其中的 props

如果确实需要解构 `props` 对象，或者需要将某个 prop 传到一个外部函数中并保持响应性

```javascript
import { toRefs, toRef } from 'vue'

export default {
  setup(props) {
    // 将 `props` 转为一个其中全是 ref 的对象，然后解构
    const { title } = toRefs(props)
    // `title` 是一个追踪着 `props.title` 的 ref
    console.log(title.value)

    // 或者，将 `props` 的单个属性转为一个 ref
    const title = toRef(props, 'title')
  }
}
```

**context：**暴露了其他一些在 `setup` 中可能会用到的值

```javascript
setup(props, context) {
  // 透传 Attributes（非响应式的对象，等价于 $attrs）
  console.log(context.attrs)
  // 插槽（非响应式的对象，等价于 $slots）
  console.log(context.slots)
  // 触发事件（函数，等价于 $emit）
  console.log(context.emit)
  // 暴露公共属性（函数）
  console.log(context.expose)
}
```

**返回值：**返回一个渲染函数

### ref

在 Vue 中，状态都是默认深层响应式的。

接受一个内部值，返回一个响应式的、可更改的 ref 对象，此对象只有一个指向其内部值的属性 `.value`

如果将一个对象赋值给 ref，那么这个对象将通过`reactive()` 转为具有深层次响应式的对象。这也意味着如果对象中包含了嵌套的 ref，它们将被深层地解包。

```typescript
import { ref } from 'vue'
import type { Ref } from 'vue'

const year: Ref<string | number> = ref('2020')
const year = ref<string | number>('2020')
```

```vue
let todos = ref(true);
```

### computed

自动根据状态变量计算新值，返回一个ref对象

```typescript
const double = computed<number>(() => {
  // 若返回值不是 number 类型则会报错
})
```

只读的计算属性 ref：

```javascript
const count = ref(1)
const plusOne = computed(() => count.value + 1)
console.log(plusOne.value) // 2
plusOne.value++ // 错误
```

可写的计算属性 ref：

```javascript
const count = ref(1)
const plusOne = computed({
  get: () => count.value + 1,
  set: (val) => {
    count.value = val - 1
  }
})
plusOne.value = 1
```

### reactive()

返回一个对象的响应式代理。响应式转换是“深层”的：它会影响到所有嵌套的属性。一个响应式对象也将深层地解包任何ref属性，同时保持响应性。

当访问到某个响应式数组或 `Map` 这样的原生集合类型中的 ref 元素时，不会执行 ref 的解包。

返回的对象以及其中嵌套的对象都会通过ES Proxy包裹，因此**不等于**源对象。

`reactive`和`ref`的区别：

1. `reactive`和`ref`都是用来定义响应式数据的，而`reactive`更推荐用来定义对象，`ref`更推荐定义基础数据类型，但是`ref`也可以定义数组和对象
2. 在访问数据的时候，`ref`需要使用`.value`，而`reactive`不需要
3. ef通过Object.defineProperty()的get和set实现数据代理。reactive使用Proxy实现数据代理，并且通过Reflect操作源对象内部的数据。

```typescript
interface Book {
  title: string
  year?: number
}

const book: Book = reactive({ title: 'Vue 3 指引' })
```

### readonly

接受一个对象 (不论是响应式还是普通的) 或是一个ref，返回一个原值的只读代理。

只读代理是深层的：对任何嵌套属性的访问都将是只读的。它的 ref 解包行为与 `reactive()` 相同，但解包得到的值是只读的。

```vue
const original = reactive({ count: 0 })
const copy = readonly(original)
watchEffect(() => {
  // 用来做响应性追踪
  console.log(copy.count)
})
// 更改源属性会触发其依赖的侦听器
original.count++
// 更改该只读副本将会失败，并会得到一个警告
copy.count++ // warning!
```

### watchEffect

立即运行一个函数，同时响应式地追踪其依赖，并在依赖更改时重新执行。

```typescript
function watchEffect(
  effect: (onCleanup: OnCleanup) => void,
  options?: WatchEffectOptions
): StopHandle
```

```typescript
const count = ref(0)
watchEffect(() => console.log(count.value))
// -> 输出 0
count.value++
// -> 输出 1
```

### watch

侦听一个或多个响应式数据源，并在数据源变化时调用所给的回调函数。

```typescript
// 侦听单个来源
function watch<T>(
  source: WatchSource<T>,
  callback: WatchCallback<T>,
  options?: WatchOptions
): StopHandle

// 侦听多个来源
function watch<T>(
  sources: WatchSource<T>[],
  callback: WatchCallback<T[]>,
  options?: WatchOptions
): StopHandle
```

```javascript
const count = ref(0)
watch(count, (count, prevCount) => {
  /* ... */
})
watch([fooRef, barRef], ([foo, bar], [prevFoo, prevBar]) => {
  /* ... */
})
```

### isRef unRef

```typescript
function isRef<T>(r: Ref<T> | unknown): r is Ref<T>
// 如果参数是 ref，则返回内部值，否则返回参数本身
// val = isRef(val) ? val.value : val 计算的一个语法糖
function unref<T>(ref: T | Ref<T>): T
```

### toRef toRefs

将一个响应式对象转换为一个普通对象，这个普通对象的每个属性都是指向源对象相应属性的 ref。每个单独的 ref 都是使用toRef()创建的。

将一个响应式对象转换为一个普通对象，这个普通对象的每个属性都是指向源对象相应属性的 ref。每个 ref 都是使用toRef()创建的。
当从组合式函数中返回响应式对象时，`toRefs` 相当有用。使用它，消费者组件可以解构/展开返回的对象而不会失去响应性

```vue
function useFeatureX() {
  const state = reactive({
    foo: 1,
    bar: 2
  })
  // ...基于状态的操作逻辑
  // 在返回时都转为 ref
  return toRefs(state)
}
// 可以解构而不会失去响应性
const { foo, bar } = useFeatureX()
```

### shallow

shallowRef shallowReactive shallowReadonly 浅层作用形式。

浅层 ref 的内部值将会原样存储和暴露，并且不会被深层递归地转为响应式。只有对 `.value` 的访问是响应式的

```vue
const state = shallowRef({ count: 1 })
// 不会触发更改
state.value.count = 2
// 会触发更改
state.value = { count: 2 }
```

### triggerRef

强制触发依赖于一个浅层 ref 的副作用，这通常在对浅引用的内部值进行深度变更后使用

```javascript
const shallow = shallowRef({
  greet: 'Hello, world'
})
// 触发该副作用第一次应该会打印 "Hello, world"
watchEffect(() => {
  console.log(shallow.value.greet)
})
// 这次变更不应触发副作用，因为这个 ref 是浅层的
shallow.value.greet = 'Hello, universe'
// 打印 "Hello, universe"
triggerRef(shallow)
```

### customRef

创建一个自定义的 ref，显式声明对其依赖追踪和更新触发的控制方式。

一般来说，`track()` 应该在 `get()` 方法中调用，而 `trigger()` 应该在 `set()` 中调用。然而事实上，你对何时调用、是否应该调用他们有完全的控制权。

```javascript
import { customRef } from 'vue'

export function useDebouncedRef(value, delay = 200) {
  let timeout
  return customRef((track, trigger) => {
    return {
      get() {
        track()
        return value
      },
      set(newValue) {
        clearTimeout(timeout)
        timeout = setTimeout(() => {
          value = newValue
          trigger()
        }, delay)
      }
    }
  })
}
```

### toRaw

根据一个 Vue 创建的代理返回其原始对象.

`toRaw()` 可以返回由 `reactive()`、`readonly()`、`shallowReactive()` 或者 `shallowReadonly()` 创建的代理对应的原始对象。

这是一个可以用于临时读取而不引起代理访问/跟踪开销，或是写入而不触发更改的特殊方法。不建议保存对原始对象的持久引用，请谨慎使用

```javascript
const foo = {}
const reactiveFoo = reactive(foo)

console.log(toRaw(reactiveFoo) === foo) // true
```

### 生命周期

![组件生命周期图示](https://images-sally.oss-cn-beijing.aliyuncs.com/img/vue3-lifecycle.png)

**onMounted()**

```typescript
function onMounted(callback: () => void): void
```

注册回调函数，在组件挂载完成后执行。组件在以下情况下被视为已挂载，在服务器渲染期间不会被调用：

- 其所有同步子组件都已经被挂载 (不包含异步组件或 `<Suspense>` 树内的组件)。
- 其自身的 DOM 树已经创建完成并插入了父容器中。注意仅当根容器在文档中时，才可以保证组件 DOM 树也在文档中。

**onUpdated()**

注册一个回调函数，在组件因为响应式状态变更而更新其 DOM 树之后调用。父组件的更新钩子将在其子组件的更新钩子之后调用。

```typescript
function onUpdated(callback: () => void): void
```

**onUnmounted()**

注册一个回调函数，在组件实例被卸载之后调用。一个组件在以下情况下被视为已卸载：

- 其所有子组件都已经被卸载。
- 所有相关的响应式作用 (渲染作用以及 `setup()` 时创建的计算属性和侦听器) 都已经停止。

**onBeforeMount()**：在组件被挂载之前被调用

**onBeforeUpdate()**：在组件即将因为响应式状态变更而更新其 DOM 树之前调用

**onBeforeUnmount()**：在组件实例被卸载之前调用

**onErrorCaptured()**：在捕获了后代组件传递的错误时调用

**onActivated()**：若组件实例是 `keepAlive`缓存树的一部分，当组件被插入到 DOM 中时调用

**onDeactivated()**：若组件实例是 `keepAlive`缓存树的一部分，当组件从 DOM 中被移除时调用

- `activated` 在组件挂载时也会调用，并且 `deactivated` 在组件卸载时也会调用。
- 这两个钩子不仅适用于 `<KeepAlive>` 缓存的根组件，也适用于缓存树中的后代组件。

### 依赖注入

provide：提供一个值，可以被后代组件注入

```typescript
function provide<T>(key: InjectionKey<T> | string, value: T): void
```

```javascript
provide: {
    message: 'hello!'
}

provide() {
    // 使用函数的形式，可以访问到 `this`
    return {
      message: this.message
    }
  }
```

```javascript
inject: ['message'],
// 当声明注入的默认值时
  // 必须使用对象形式
inject: {
    message: {
      from: 'message', // 当与原注入名同名时，这个属性是可选的
        default: 'default value'
    },
      user: {
        // 对于非基础类型数据，如果创建开销比较大，或是需要确保每个组件实例
        // 需要独立数据的，请使用工厂函数
        default: () => ({ name: 'John' })
      }
  }
```

## 指令

### v-text

```vue
<span v-text="msg"></span>
<!-- 等同于 -->
<span>{{msg}}</span>
```

### v-html

双大括号会将数据解释为纯文本，而不是 HTML。若想插入 HTML时使用。

```vue
<p>Using text interpolation: {{ rawHtml }}</p>
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
// Using text interpolation: <span style="color: red">This should be red.</span>
// Using v-html directive: This should be red.
```

### v-show

在 DOM 渲染中保留该元素；`v-show` 切换了该元素上名为 `display` 的 CSS 属性。

### v-if

条件性地渲染一块内容。这块内容只会在指令的表达式返回真值时才被渲染

`v-else` 为 `v-if` 添加一个“else 区块”

`v-else-if` 提供的是相应于 `v-if` 的“else if 区块”

```vue
<button @click="awesome = !awesome">Toggle</button>
<h1 v-if="awesome">Vue is awesome!</h1>
<h1 v-else>Oh no 😢</h1>
```

### v-for

列表渲染

```vue
<li v-for="(item, index) in items"  :key="item.id">
  {{ parentMessage }} - {{ index }} - {{ item.message }}
</li>

<!-- 有 index 索引时 使用解构赋值 -->
<li v-for="({ message }, index) in items">
  {{ message }} {{ index }}
</li>

```

### v-on

监听dom事件，简写为@，有2种形式

```vue
<!--内联事件处理器-->
<button @click="count++">Add 1</button>
<p>Count is: {{ count }}</p>
<!-- `greet` 是上面定义过的方法名 方法事件处理器 -->
<button @click="greet">Greet</button>
```

事件修饰符：.stop  .prevent  .self  .capture  .once  .passive

- `.stop` ——调用 `event.stopPropagation()`。
- `.prevent` ——调用 `event.preventDefault()`。
- `.capture` ——在捕获模式添加事件监听器。
- `.self` ——只有事件从元素本身发出才触发处理函数。
- `.{keyAlias}` ——只在某些按键下触发处理函数。
- `.once` ——最多触发一次处理函数。
- `.left` ——只在鼠标左键事件触发处理函数。
- `.right` ——只在鼠标右键事件触发处理函数。
- `.middle` ——只在鼠标中键事件触发处理函数。
- `.passive` ——通过 `{ passive: true }` 附加一个 DOM 事件

### v-bind

绑定

修饰符：

- `.camel` ——将短横线命名的 attribute 转变为驼峰式命名。
- `.prop` ——强制绑定为 DOM property
- `.attr` ——强制绑定为 DOM attribute

```vue
<div v-bind:id="dynamicId"></div>
<div :id="dynamicId"></div>
<!-- 动态 attribute 名 -->
<button v-bind:[key]="value"></button>
/*绑定多个值*/
<div v-bind="objectOfAttrs"></div> 
<svg :view-box.camel="viewBox"></svg>
```

### v-model

将表单输入框的内容同步给 JavaScript 中相应的变量

**原生元素**

修饰符:

- `.lazy` ——监听 `change` 事件而不是 `input`
- `.number`——将输入的合法符串转为数字
- `.trim`——移除输入内容两端空格

```
<input v-model="searchText" />
```

上面的代码其实等价于下面这段 (编译器会对 `v-model` 进行展开)：

```
<input
  :value="searchText"
  @input="searchText = $event.target.value"
/>
```

**自定义组件**

`v-model` 会被展开为如下的形式：

```
<CustomInput
  :modelValue="searchText"
  @update:modelValue="newValue => searchText = newValue"
/>
```

要让这个例子实际工作起来，`<CustomInput>` 组件内部需要做两件事：

1. 将内部原生 `input` 元素的 `value` attribute 绑定到 `modelValue` prop
2. 输入新的值时在 `input` 元素上触发 `update:modelValue` 事件

这里是相应的代码：

```
<script>
export default {
  props: ['modelValue'],
  emits: ['update:modelValue']
}
</script>

<template>
  <input
    :value="modelValue"
    @input="$emit('update:modelValue', $event.target.value)"
  />
</template>
```

### v-once

仅渲染元素和组件一次，并跳过之后的更新。在随后的重新渲染，元素/组件及其所有子项将被当作静态内容并跳过渲染。

```vue
<!-- 单个元素 -->
<span v-once>This will never change: {{msg}}</span>
<!-- 带有子元素的元素 -->
<div v-once>
  <h1>comment</h1>
  <p>{{msg}}</p>
</div>
<!-- 组件 -->
<MyComponent v-once :comment="msg" />
<!-- `v-for` 指令 -->
<ul>
  <li v-for="i in list" v-once>{{i}}</li>
</ul>
```

### v-memo

缓存一个模板的子树。在元素和组件上都可以使用。为了实现缓存，该指令需要传入一个固定长度的依赖值数组进行比较。如果数组里的每个值都与最后一次的渲染相同，那么整个子树的更新将被跳过

`v-memo` 传入空依赖数组 (`v-memo="[]"`) 将与 `v-once` 效果相同

```vue
<div v-memo="[valueA, valueB]">
  ...
</div>
```

### 自定义指令

遵循 `vNameOfDirective` 命名规范

```vue
<script setup>
const vMyDirective = {
  beforeMount: (el) => {
    // 在元素上做些操作
  }
}
</script>
<template>
  <h1 v-my-directive>This is a Heading</h1>
</template>
```

## 特殊元素和组件

### slot

```vue
<FancyButton>
  Click me! <!-- 插槽内容 -->
</FancyButton>
```

而 `<FancyButton>` 的模板是这样的：

```html
<button class="fancy-btn">
  <slot></slot> <!-- 插槽出口 -->
</button>
```

最终渲染出的 DOM 是这样：

```html
<button class="fancy-btn">Click me!</button>
```

```html
<slot>
    Submit <!-- 插槽默认内容 -->
</slot>
```

动态插槽

```vue
<template v-slot:[dynamicSlotName]>
<template #[dynamicSlotName]>
```

**具名插槽**

```vue
<BaseLayout>
  <template v-slot:header>
		<!--简写为：<template #header>-->
    <!-- header 插槽的内容放这里 -->
		<h1>Here might be a page title</h1>
  </template>
  
  <!-- 隐式的默认插槽 -->
  <p>A paragraph for the main content.</p>
  <p>And another one.</p>
</BaseLayout>
```

内部定义：

```vue
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

最终渲染：

```vue
<div class="container">
  <header>
    <h1>Here might be a page title</h1>
  </header>
  <main>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </main>
  <footer>
    <p>Here's some contact info</p>
  </footer>
</div>
```

**作用域插槽**

```vue
<!-- <MyComponent> 的模板 -->
<div>
  <slot :text="greetingMessage" :count="1"></slot>
</div>
```

```vue
<MyComponent v-slot="slotProps">
  {{ slotProps.text }} {{ slotProps.count }}
</MyComponent>
```

**具名作用域插槽**

```vue
<template #header="headerProps">
   {{ headerProps }}
</template>
```

### component

动态组件：`<component>` 元素和特殊的 `is` attribute 实现

- 当 `is` 是字符串，它既可以是 HTML 标签名也可以是组件的注册名。
- 或者，`is` 也可以直接绑定到组件的定义。

```vue
<component :is="href ? 'a' : 'span'"></component>
<component :is="Math.random() > 0.5 ? Foo : Bar" />
```

### Transition

为单个元素或组件提供动画过渡效果。进入或离开可以由以下的条件之一触发：

- 由 `v-if` 所触发的切换
- 由 `v-show` 所触发的切换
- 由特殊元素 `<component>` 切换的动态组件

```typescript
interface TransitionProps {
  /**
   * 用于自动生成过渡 CSS class 名。
   * 例如 `name: 'fade'` 将自动扩展为 `.fade-enter`、
   * `.fade-enter-active` 等。
   */
  name?: string
  /**
   * 是否应用 CSS 过渡 class。
   * 默认：true
   */
  css?: boolean
  /**
   * 指定要等待的过渡事件类型
   * 来确定过渡结束的时间。
   * 默认情况下会自动检测
   * 持续时间较长的类型。
   */
  type?: 'transition' | 'animation'
  /**
   * 显式指定过渡的持续时间。
   * 默认情况下是等待过渡效果的根元素的第一个 `transitionend`
   * 或`animationend`事件。
   */
  duration?: number | { enter: number; leave: number }
  /**
   * 控制离开/进入过渡的时序。
   * 默认情况下是同时的。
   */
  mode?: 'in-out' | 'out-in' | 'default'
  /**
   * 是否对初始渲染使用过渡。
   * 默认：false
   */
  appear?: boolean

  /**
   * 用于自定义过渡 class 的 prop。
   * 在模板中使用短横线命名，例如：enter-from-class="xxx"
   */
  enterFromClass?: string
  enterActiveClass?: string
  enterToClass?: string
  appearFromClass?: string
  appearActiveClass?: string
  appearToClass?: string
  leaveFromClass?: string
  leaveActiveClass?: string
  leaveToClass?: string
}
```

**基于 CSS 的过渡效果**

![image-20221104174900227](https://images-sally.oss-cn-beijing.aliyuncs.com/img/transition-基于css过滤效果.png)

```vue
<button @click="show = !show">Toggle</button>
<Transition>
  <p v-if="show">hello</p>
</Transition>
```

```css
.v-enter-active,
.v-leave-active {
  transition: opacity 0.5s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}
```

有名字的`Transition`，可以传递`prop`，起作用的class以`name`命名：

```vue
<Transition name="fade">
  ...
</Transition>
```

```css
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}
```

传递以下 props 指定自定义的过渡 class：

- `enter-from-class`
- `enter-active-class`
- `enter-to-class`
- `leave-from-class`
- `leave-active-class`
- `leave-to-class`

```vue
<Transition
  name="custom-classes"
  enter-active-class="animate__animated animate__tada"
  leave-active-class="animate__animated animate__bounceOutRight"
>
  <p v-if="show">hello</p>
</Transition>
```

**js钩子**

```javascript
<Transition
  @before-enter="onBeforeEnter"
  @enter="onEnter"
  @after-enter="onAfterEnter"
  @enter-cancelled="onEnterCancelled"
  @before-leave="onBeforeLeave"
  @leave="onLeave"
  @after-leave="onAfterLeave"
  @leave-cancelled="onLeaveCancelled"
>
  <!-- ... -->
</Transition>
```

**其他prop**

1、`:css="false"`：使用仅由 JavaScript 执行的动画时，添加。显式地向 Vue 表明可以跳过对 CSS 过渡的自动探测

2、初次渲染时应用一个过渡效果，你可以添加 `appear` prop

```vue
<Transition appear></Transition>
```

3、mode="out-in"(in-out)先执行离开动画，然后在其完成之后再执行元素的进入动画

### TransitionGroup

用于对 `v-for` 列表中的元素或组件的插入、移除和顺序改变添加动画效果。

`<TransitionGroup>` 支持和 `<Transition>` 基本相同的 props、CSS 过渡 class 和 JavaScript 钩子监听器，但有以下几点区别：

- 默认情况下，它不会渲染一个容器元素。但你可以通过传入 `tag` prop 来指定一个元素作为容器元素来渲染。
- 过渡模式不可用，因为不再是在互斥的元素之间进行切换。
- 列表中的每个元素都**必须**有一个独一无二的 `key` attribute。
- CSS 过渡 class 会被应用在列表内的元素上，**而不是**容器元素上

```vue
<TransitionGroup name="list" tag="ul">
  <li v-for="item in items" :key="item">
    {{ item }}
  </li>
</TransitionGroup>
```

```css
.list-move, /* 对移动中的元素应用的过渡 */
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}

/* 确保将离开的元素从布局流中删除
  以便能够正确地计算移动的动画。 */
.list-leave-active {
  position: absolute;
}
```

### KeepAlive

在多个组件间动态切换时缓存被移除的组件实例。默认情况下，一个组件实例在被替换掉后会被销毁。用 `<KeepAlive>` 内置组件将这些动态组件包装，能在被“切走”的时候保留它们的状态

```vue
<!-- 非活跃的组件将会被缓存！ -->
<KeepAlive>
  <component :is="activeComponent" />
</KeepAlive>
```

**props**

 `include` 和 `exclude` ：`<KeepAlive>` 默认会缓存内部所有组件实例，可以通过 `include` 和 `exclude` prop 来定制该行为。它会根据组件的name 选项进行匹配，所以组件如果想要条件性地被 `KeepAlive` 缓存，就必须显式声明一个 `name` 选项

max：限制可被缓存的最大组件实例数，使用LRU缓存淘汰算法

```vue
<!-- 以英文逗号分隔的字符串 -->
<KeepAlive include="a,b">
  <component :is="view" />
</KeepAlive>

<!-- 正则表达式 (需使用 `v-bind`) -->
<KeepAlive :include="/a|b/">
  <component :is="view" />
</KeepAlive>

<!-- 数组 (需使用 `v-bind`) -->
<KeepAlive :include="['a', 'b']">
  <component :is="view" />
</KeepAlive>

<KeepAlive :max="10">
  <component :is="activeComponent" />
</KeepAlive>
```

### Teleport

将一个组件内部的一部分模板“传送”到该组件的 DOM 结构外层的位置去。

**props**

`to`： 的值可以是一个 CSS 选择器字符串，也可以是一个 DOM 元素对象。

```vue
<button @click="open = true">Open Modal</button>

<Teleport to="body">
  <div v-if="open" class="modal">
    <p>Hello from the modal!</p>
    <button @click="open = false">Close</button>
  </div>
</Teleport>

```

disabled：视情况禁用 `<Teleport>`

```vue
<Teleport :disabled="isMobile">
  ...
</Teleport>
```

### Suspense

用来在组件树中协调对异步依赖的处理，可以在组件树上层等待下层的多个嵌套异步依赖项解析完成，并可以在等待时渲染一个加载状态。

## 渲染函数

```javascript
import { h } from 'vue'

const vnode = h(
  'div', // type
  { id: 'foo', class: 'bar' }, // props
  [
    /* children */
  ]
)
```













