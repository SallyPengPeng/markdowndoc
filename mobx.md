## observable

Observable 值可以是JS基本数据类型、引用类型、普通对象、类实例、数组和映射

```javascript
import { observable } from "mobx";
```

### observable object

如果把一个普通的 JavaScript 对象传递给 `observable` 方法，对象的所有属性都将被拷贝至一个克隆对象并将克隆对象转变成可观察的。 (普通对象是指不是使用构造函数创建出来的对象，而是以 `Object` 作为其原型，或者根本没有原型。) 默认情况下，`observable` 是递归应用的，所以如果对象的某个值是一个对象或数组，那么该值也将通过 `observable` 传递。

```javascript
var person = observable({
    // observable 属性:
    name: "John",
    age: 42,
    showAge: false,

    // 计算属性:
    get labelText() {
        return this.showAge ? `${this.name} (age: ${this.age})` : this.name;
    },

    // 动作:
    setAge(age) {
        this.age = age;
    }
});
```

### observable array

```javascript
import {observable, autorun} from "mobx";

var todos = observable([
    { title: "Spoil tea", completed: true },
    { title: "Make coffee", completed: false }
]);

// 创建浅数组以禁用更深层次的observable
observable.array(values, { deep: false })
```

## 对observable做出响应

### computed

响应式的产生一个可以被其它 observer 使用的**值**，使用 `@computed`

```javascript
import {observable, computed} from "mobx";

class OrderLine {
    @observable price = 0;
    @observable amount = 1;

    constructor(price) {
        this.price = price;
    }

    @computed get total() {
        return this.price * this.amount;
    }
}
```

### autorun

当你想创建一个响应式函数，而该函数本身永远不会有观察者时,可以使用 `mobx.autorun`。Autoruns 是关于 *启动效果* (initiating effects) 的 ，而不是产生新的值。

```javascript
var numbers = observable([1,2,3]);
var sum = computed(() => numbers.reduce((a, b) => a + b, 0));

var disposer = autorun(() => console.log(sum.get()));
// 输出 '6'
numbers.push(4);
// 输出 '10'

disposer();
numbers.push(5);
// 不会再输出任何值。`sum` 不会再重新计算。
```

### when

```
when(predicate: () => boolean, effect?: () => void, options?)
```

`when` 观察并运行给定的 `predicate`，直到返回true。 一旦返回 true，给定的 `effect` 就会被执行，然后 autorunner(自动运行程序) 会被清理。 该函数返回一个清理器以提前取消自动运行程序。

```javascript
class MyResource {
    constructor() {
        when(
            // 一旦...
            () => !this.isVisible,
            // ... 然后
            () => this.dispose()
        );
    }

    @computed get isVisible() {
        // 标识此项是否可见
    }

    dispose() {
        // 清理
    }
}
```

如果没提供 `effect` 函数，`when` 会返回一个 `Promise` 。它与 `async / await` 可以完美结合。

```javascript
async function() {
    await when(() => that.isVisible)
    // 等等..
}
```

### reaction

```javascript
reaction(() => data, (data, reaction) => { sideEffect }, options?)
```

第一个(*数据* 函数)是用来追踪并返回数据作为第二个函数(*效果* 函数)的输入。 不同于 `autorun` 的是，当创建时*效果* 函数不会直接运行，只有在数据表达式首次返回一个新值后才会运行。 在执行 *效果* 函数时访问的任何 observable 都不会被追踪。

第二个函数(副作用函数)当调用时会接收两个参数。 第一个参数是由 *data* 函数返回的值。 第二个参数是当前的 reaction，可以用来在执行期间清理 `reaction`

```javascript
export default class Reaction {
  @observable reactionData = {};
  reactions = [
    reaction(
      () => this.reactionData.fundCode,
      fundCode => {}
    ),

    reaction(
      () => this.reactionData.dataSource,
      dataSource => {}
    ),
  ];

  destroyReactions = () => {
    this.reactions.forEach(item => {
      item(); // reaction()返回一个清理函数
    });
  }
}

```

```javascript
// 只调用一次并清理掉 reaction : 对 observable 值作出反应。
const reaction3 = reaction(
    () => counter.count,
    (count, reaction) => {
        console.log("reaction 3: invoked. counter.count = " + count);
        reaction.dispose();
    }
);
```

## 改变observable

### action

动作是任何用来修改状态的东西。 使用MobX你可以在代码中显式地标记出动作所在的位置。 动作可以有助于更好的组织代码。

### async action

`action` 包装/装饰器只会对当前运行的函数作出反应，而不会对当前运行函数所调用的函数（不包含在当前函数之内）作出反应！ 这意味着如果 action 中存在 `setTimeout`、promise 的 `then` 或 `async` 语句，并且在回调函数中某些状态改变了，那么这些回调函数也应该包装在 `action` 中。

#### runInAction

接收代码块并在异步动作中执行，对即时创建和执行动作非常有用，例如在异步过程中。`runInAction(f)` 是 `action(f)()` 的语法糖。

```javascript
class Store {
    @observable githubProjects = []
    @observable state = "pending" // "pending" / "done" / "error"

    @action
    fetchProjects() {
        this.githubProjects = []
        this.state = "pending"
        fetchGithubProjectsSomehow().then(
            projects => {
                const filteredProjects = somePreprocessing(projects)
                // 将‘“最终的”修改放入一个异步动作中
                runInAction(() => {
                    this.githubProjects = filteredProjects
                    this.state = "done"
                })
            },
            error => {
                // 过程的另一个结局:...
                runInAction(() => {
                    this.state = "error"
                })
            }
        )
    }
}
```

## mobx-react

### @observer

用来将 React 组件转变成响应式组件。 它用 `mobx.autorun` 包装了组件的 render 函数以确保任何组件渲染中使用的数据变化时都可以强制刷新组件。 `observer` 是由单独的 `mobx-react` 包提供的。

使用可观察的局部组件状态。

```javascript
import {observer} from "mobx-react"
import {observable} from "mobx"

@observer class Timer extends React.Component {
    @observable secondsPassed = 0

    componentWillMount() {
        setInterval(() => {
            this.secondsPassed++
        }, 1000)
    }

    render() {
        return (<span>Seconds passed: { this.secondsPassed } </span> )
    }
}

ReactDOM.render(<Timer />, document.body)

```

setState的缺点：

1. 异步的
2. setState causes unnecessary renders（值未改变、值没用到、值在特定情况才用到）
3. setState is not sufficient to capture all component state

### provider

```javascript
const colors = observable({
   foreground: '#000',
   background: '#fff'
});

const App = () =>
  <Provider colors={colors}>
     <app stuff... />
  </Provider>;

const Button = inject("colors")(observer(({ colors, label, onClick }) =>
  <button style={{
      color: colors.foreground,
      backgroundColor: colors.background
    }}
    onClick={onClick}
  >{label}</button>
));

// 稍后..
colors.foreground = 'blue';
// 所有button都会更新

```

































