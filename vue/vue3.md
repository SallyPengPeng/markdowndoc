Vue 中用过三种响应式解决方案，分别是 defineProperty、Proxy 和 value setter

ref：定义状态变量

```vue
let todos = ref([{ title: "学习Vue", done: false }]);
```

computed：自动根据状态变量计算新值

```vue
let active = computed(() => {
  return todos.value.filter((v) => !v.done).length;
});
```

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



模板语法

```vue
<span>Message: {{ msg }}</span>
```



v-html：双大括号会将数据解释为纯文本，而不是 HTML。若想插入 HTML时使用。



v-bind：绑定

```vue
<div v-bind:id="dynamicId"></div>
<div :id="dynamicId"></div>
<div v-bind="objectOfAttrs"></div> /*绑定多个值*/
```



条件渲染：

v-if：条件性地渲染一块内容。这块内容只会在指令的表达式返回真值时才被渲染

`v-else` 为 `v-if` 添加一个“else 区块”

`v-else-if` 提供的是相应于 `v-if` 的“else if 区块”

```vue
<button @click="awesome = !awesome">Toggle</button>

<h1 v-if="awesome">Vue is awesome!</h1>
<h1 v-else>Oh no 😢</h1>
```

v-show： `v-show` 会在 DOM 渲染中保留该元素；`v-show` 切换了该元素上名为 `display` 的 CSS 属性。



v-for：列表渲染

```vue
<li v-for="(item, index) in items"  :key="item.id">
  {{ parentMessage }} - {{ index }} - {{ item.message }}
</li>

<!-- 有 index 索引时 使用解构赋值 -->
<li v-for="({ message }, index) in items">
  {{ message }} {{ index }}
</li>

```



v-on：监听dom事件，简写为@，有2种形式

```vue
<!--内联事件处理器-->
<button @click="count++">Add 1</button>
<p>Count is: {{ count }}</p>
<!-- `greet` 是上面定义过的方法名 方法事件处理器 -->
<button @click="greet">Greet</button>
```

事件修饰符：.stop  .prevent  .self  .capture  .once  .passive

```vue
<!-- 单击事件将停止传递 -->
<a @click.stop="doThis"></a>

<!-- 提交事件将不再重新加载页面 -->
<form @submit.prevent="onSubmit"></form>

<!-- 修饰语可以使用链式书写 -->
<a @click.stop.prevent="doThat"></a>

<!-- 也可以只有修饰符 -->
<form @submit.prevent></form>
```



v-model：将表单输入框的内容同步给 JavaScript 中相应的变量

原生元素：

修饰符  .lazy  .number  .trim

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

而当使用在一个自定义组件上时，`v-model` 会被展开为如下的形式：

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



props

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



触发与监听事件

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



透传attr：透传 attribute”指的是传递给一个组件，却没有被该组件声明为props 或emits 的 attribute 或者 `v-on` 事件监听器。比如 `class`、`style` 和 `id` `v-on` 。



依赖注入

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





计算属性

```javascript
export default {
  data() {
    return {
      author: {
        books: [
          'Vue 2 - Advanced Guide',
        ]
      }
    }
  },
  computed: {
    // 一个计算属性的 getter
    publishedBooksMessage() {
      // `this` 指向当前组件实例
      return this.author.books.length > 0 ? 'Yes' : 'No'
    }
  }
}
```

```vue
<p>Has published books:</p>
<span>{{ publishedBooksMessage }}</span>
```



侦听器

要在状态变化时执行一些“副作用”：例如更改 DOM，或是根据异步操作的结果去修改另一处的状态。

```javascript
export default {
  data() {
    return {
      question: '',
      answer: 'Questions usually contain a question mark. ;-)'
    }
  },
  watch: {
    // 每当 question 改变时，这个函数就会执行
    question(newQuestion, oldQuestion) {
      if (newQuestion.includes('?')) {
        this.getAnswer()
      }
    }
  },
  // watch: {
    // 注意：只能是简单的路径，不支持表达式。
    //'some.nested.key'(newValue) {
      // ...
    //  }
 //}
  methods: {
    async getAnswer() {
      this.answer = 'Thinking...'
      try {
        const res = await fetch('https://yesno.wtf/api')
        this.answer = (await res.json()).answer
      } catch (error) {
        this.answer = 'Error! Could not reach the API. ' + error
      }
    }
  }
}
```

```VUE
<p>
  Ask a yes/no question:
  <input v-model="question" />
</p>
<p>{{ answer }}</p>
```



动态组件：`<component>` 元素和特殊的 `is` attribute 实现

```vue
<!-- currentTab 改变时组件也改变 -->
<component :is="currentTab"></component>

```

默认情况下，一个组件实例在被替换掉后会被销毁。用 `<KeepAlive>` 内置组件将这些动态组件包装，能在被“切走”的时候保留它们的状态

```vue
<!-- 非活跃的组件将会被缓存！ -->
<KeepAlive>
  <component :is="activeComponent" />
</KeepAlive>
```



`<Teleport>` 将一个组件内部的一部分模板“传送”到该组件的 DOM 结构外层的位置去

```vue
<Teleport to="body">
  <div v-if="open" class="modal">
    <p>Hello from the modal!</p>
    <button @click="open = false">Close</button>
  </div>
</Teleport>
```



自定义指令，遵循 `vNameOfDirective` 命名规范

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



script setup和普通的 script

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



### 全局API

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

```vue
let todos = ref(true);
```

### computed

自动根据状态变量计算新值，返回一个ref对象

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

shallowRef shallowReactive shallowReadonly 浅层作用形式

```vue
const state = shallowRef({ count: 1 })
// 不会触发更改
state.value.count = 2
// 会触发更改
state.value = { count: 2 }
```

### triggerRef

强制触发依赖于一个浅层 ref 的副作用，这通常在对浅引用的内部值进行深度变更后使用











