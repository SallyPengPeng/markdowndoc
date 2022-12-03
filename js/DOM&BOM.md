## DOM

文档：一个页面就是一个文档，DOM中使用document表示

元素：页面中的所有标签都是元素，DOM中使用element表示

节点：网页中的所有内容都是节点（标签、属性、文本、注释等），DOM中使用node表示

节点和元素区别和联系：

https://dmitripavlutin.com/dom-node-element/

an element is a node of a specific type — element (Node.ELEMENT_NODE). Along with types like document, comment, text, etc.

In simple words, an element is a node that’s written using a tag in the HTML document. <html>, <head>, <title>, <body>, <h2>, <p> are all elements because they are represented by tags.

`Node.nodeType` 可以具有代表节点类型的以下值之一：

- `Node.ELEMENT_NODE`
- `Node.ATTRIBUTE_NODE`
- `Node.TEXT_NODE`
- `Node.CDATA_SECTION_NODE`
- `Node.PROCESSING_INSTRUCTION_NODE`
- `Node.COMMENT_NODE`
- `Node.DOCUMENT_NODE`
- `Node.DOCUMENT_TYPE_NODE`
- `Node.DOCUMENT_FRAGMENT_NODE`
- `Node.NOTATION_NODE`

### 操作元素

#### 创建

innerHtml和createElement

```js
 // innerHtml
 inner.innerHtml = '<a>1</a>';
 
 // createElement
a = document.createElement('a');
create.appendChild(a)
```

相同点：都可以创建元素

不同点：innerHTML创建多个元素效率更高（但是不要使用拼接字符串，使用数组形式拼接）

#### 新增

在 HTML DOM 中，Element 对象表示 HTML 元素。Element 对象可以拥有类型为元素节点、文本节点、注释节点的子节点。以下都可用于HTML元素
1、element.appendChild 向元素添加新的子节点，作为最后一个子节点。
2、element.insertBefore 指定的已有的子节点之前插入新节点。
3、element.insertAdjacentHTML(position, text)  方法将指定的文本解析为 Element 元素，并将结果节点插入到DOM树中的指定位置。

| 参数     |                                                              |
| -------- | ------------------------------------------------------------ |
| position | 一个 DOMString，表示插入内容相对于元素的位置，并且必须是以下字符串之一：<br/>'beforebegin'：元素自身的前面。<br/>'afterbegin'：插入元素内部的第一个子节点之前。<br/>'beforeend'：插入元素内部的最后一个子节点之后。<br/>'afterend'：元素自身的后面。 |
| text     | 是要被解析为HTML或XML元素，并插入到DOM树中的 DOMString。     |

4、element.insertAdjacentText(position, text) 将一个给定的文本节点插入在相对于被调用的元素给定的位置。

#### 删除

1、element.removeChild 从DOM中删除一个子节点。返回删除的节点

2、ChildNode.remove() 把对象从它所属的 DOM 树中删除

```javascript
<div id="div-01">Here is div-01</div>
<div id="div-02">Here is div-02</div>
<div id="div-03">Here is div-03</div>
var el = document.getElementById('div-02');
el.remove();
// id 为 'div-02' 的 div 被删掉了
```

####  修改

```
// 获取或设置元素内容
// 1. 从其实际位置到终止位置的内容，但它去除html标签，同时空格和换行也会去掉
element.innerText
// 2. 从其实际位置到终止位置的全部内容，包括html标签，同时保留空格和换行
element.innerHTML
// 3. 表单中内容
element.value

// 元素样式
element.style // 行内样式操作
element.className  // 类名样式操作
element.classList // 返回元素类名DOMTokenList对象
// 可用于在元素中添加，移除及切换CSS类。只读。add remove toggle
// 例如：element.classList.add('my-style', 'my-style-1');

// 获取或设置属性
// 1. 内置属性
element.属性
// 2. 自定义属性
element.getAttribute(属性)
element.setAttribute(属性)
```

```javascript
// 覆盖原来的样式
var dom=element.setAttribute("class","test1");
// 追加样式不覆盖原来的样式
element.classList.add("test1","test2","test3");
```

#### 查

- 根据ID获取，返回element对象：getElementById('id名')
- 根据标签名获取，返回带有指定标签名的对象的集合：getElementsByTagName(‘标签名’)

- getElementsByName()  查询元素的 name 属性，返回带有指定名称的对象的集合

- 通过HTML5新增的方法获取
  - 根据类名返回元素对象集合：document.getElementsByClassName(‘类名’)
  - 根据指定选择器返回第一个元素对象：document.querySelector(‘选择器’)，如‘.nav’  '#nav'   'ul'
  - 根据指定选择器返回所有元素集合：document.querySelectorAll('选择器')
- 获取特殊元素（body html）
  - 获取body元素：document.body
  - 获取html元素：document.documentElement

- 利用节点获取元素

一般的，节点至少拥有nodeType nodeName nodeValue这3个基本属性

元素节点nodeType为1，属性节点nodeType为2，文本节点nodeType为3（文本节点包含文字、空格、换行等）

| 获取节点                                      |                                                              |
| --------------------------------------------- | ------------------------------------------------------------ |
| node.parentNode                               | 获取node节点最近的父节点，找不到时返回null                   |
| node.parentElement                            | 获取node节点最近的父元素，找不到时返回null，一般等同于parentNode |
| parentNode.childNodes                         | 获取parentNode中所有的子节点，也包含文本节点，其中连空格和换行符都会默认文本节点，不常用 |
| parentNode.children                           | 获取parentNode中所有的元素节点                               |
| parentNode.firstChild/lastChild               | 获取第一个/最后一个子节点，包含文本节点                      |
| parentNode.firstElementChild/lastElementChild | 获取第一个/最后一个元素节点，有兼容问题                      |
| parentNode.children[0]                        | 一般开发中使用获取第一个元素节点                             |

| 操作节点                |                                   |
| ----------------------- | --------------------------------- |
| node.removeChild(child) | 从DOM中删除一个子节点             |
| node.cloneNode()        | 复制节点，括号为空或false为浅拷贝 |

###  事件

事件源、事件类型、事件处理程序

DOM事件流

![DOM](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-DOM-cd6bec769166626ac7c272c04d1eace9-592.png)

事件冒泡/事件捕获

https://blog.csdn.net/chenjuan1993/article/details/81347590

- 事件冒泡：事件按照从最特定的事件目标到最不特定的事件目标(document对象)的顺序触发，addEventListener默认处于冒泡
- 事件捕获：事件从最不精确的对象(document 对象)开始触发，然后到最精确

常见的事件

| 鼠标事件   | 触发条件                                                     |
| ---------- | ------------------------------------------------------------ |
| click      | 鼠标点击左键触发  element.click() 用于模拟鼠标左键点击一个元素，其他事件同理 |
| mouseover  | 鼠标经过触发，与mouseenter区别，冒泡，经过子盒子也会触发     |
| mouseout   | 鼠标离开触发                                                 |
| focus      | 获得鼠标焦点触发                                             |
| blur       | 失去鼠标焦点触发                                             |
| mousemove  | 鼠标移动触发                                                 |
| mouseup    | 鼠标弹起触发                                                 |
| mousedown  | 鼠标按下触发                                                 |
| mouseenter | 不会冒泡，鼠标移动到元素上会触发，经过子盒子不触发           |
| mouseleave | 不会冒泡，鼠标离开元素上会触发，离开子盒子不触发             |

注：mouseenter和mouseover及mousemove的异同：

mouseenter：当鼠标经过被选元素才会触发，不会冒泡，当鼠标经过被选元素的子元素时，不会触发mouseenter事件，对应mouseleave事件。
mouseover：当鼠标经过被选元素和被选元素的子元素时都会触发mouseover事件，对应mouseout事件。
mousemove：当鼠标移入被选元素内后，任意移动一个像素点都会触发。
mouseleave: 当鼠标移除被选元素才会触发，后代元素不会触发，该方法不会冒泡。
mouseout: 无论鼠标离开被选元素还是被选元素的子元素都会触发

| 键盘事件 | 触发条件                                                     |
| -------- | ------------------------------------------------------------ |
| keyup    | 某个键盘按键被松开时触发                                     |
| keydown  | 某个键盘按键被按下时触发                                     |
| keypress | 某个键盘按键被按下时触发，但是不识别功能键，比如ctrl shift 箭头等 |

注：3个事件的执行顺序：keydown - keypress - keyup

| 事件   | 触发条件 |
| ------ | -------- |
| scroll | 页面滚动 |

#### 2.1. 添加注册事件

给元素添加事件，称为注册事件或者绑定事件。注册事件有两种方式：传统方式和方法监听注册方式

传统注册方式：利用on开头的事件，注册事件有唯一性，同一个元素同一个事件只能设置一个处理函数，最后处理的处理函数将会覆盖前面注册的处理函数。只能得到冒泡阶段

方法监听注册方式：addEventListener()是一个方法，同一个元素同一个事件可以注册多个监听器，按照注册顺序依次执行

```
// 将指定的监听器注册到eventTarget(目标对象)上，当该对象触发指定的事件时，就会执行事件处理函数
eventTarget.addEventListener(type, listener[, useCapture])
```

type：事件类型字符串，比如click mouseover

listener：事件处理函数，事件发生时，会调用该监听函数

useCapture：可选参数，默认false，表示处于冒泡阶段

#### 2.2. 删除事件（解绑事件）

```js
// 传统事件解绑
eventTarget.onClick = null;
// 方法监听注册方式
eventTarget.removeEventListener(type, listener[, useCapture])
```

#### 2.3. 事件对象

e.target: 返回的是触发事件的对象（元素）

this: 返回的是绑定事件的对象（元素）

事件对象的常见属性和方法

| 事件对象属性方法    | 说明                                                |
| ------------------- | --------------------------------------------------- |
| e.target            | 返回触发事件的对象 标准                             |
| e.srcElement        | 返回触发事件的对象 非标准 ie6-8使用                 |
| e.type              | 返回事件的类型，比如click mouseover                 |
| e.cancelBubble      | 该属性阻止冒泡 非标准 ie6-8使用                     |
| e.returnValue       | 该属性阻止默认事件（默认行为）非标准 ie6-8使用      |
| e.preventDefault()  | 该方法阻止默认事件（默认行为）标准 比如不让链接跳转 |
| e.stopPropagation() | 阻止冒泡 标准                                       |

#### 2.4. 事件委派（利用DOM冒泡）

事件委托的原理：不是每个子节点单独设置事件监听器，而是事件监听器设置在其父节点上，然后利用冒泡原理影响设置每个子节点

### 鼠标事件获取到坐标的属性

![鼠标事件获取到坐标的属性](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-06-2022-01-05-%E9%BC%A0%E6%A0%87%E4%BA%8B%E4%BB%B6%E8%8E%B7%E5%8F%96%E5%88%B0%E5%9D%90%E6%A0%87%E7%9A%84%E5%B1%9E%E6%80%A7-0d4401425dcfb7b4b5c0e957dc624aa9-867-20220106145930684-0d4401425dcfb7b4b5c0e957dc624aa9-7db.png)

https://segmentfault.com/a/1190000002405897

#### event

- pageX/Y

  触发点相对文档区域左上角距离，会随着页面滚动而变化

  兼容性：除IE6/7/8不支持外，其余浏览器均支持

- clientX/Y

  触发点相对浏览器可视区域左上角距离，不随页面滚动而变化

  兼容性：所有浏览器均支持

- screenX/Y

  触发点相对显示器屏幕左上角的距离，不随页面滚动而改动

  兼容性：所有浏览器均支持

- offsetX/Y

  触发点相对被触发dom的左上角距离，不过左上角基准点在不同浏览器中有区别，其中在IE中以内容区左上角为基准点不包括边框，如果触发点在边框上会返回负值，而chrome中以边框左上角为基准点。

  兼容性：IE所有版本，chrome，Safari均完美支持，Firefox不支持

- layerX/Y

  触发点相对被触发dom左上角的距离，数值与offsetX/Y相同，这个变量就是firefox用来替代offsetX/Y的，基准点为边框左上角，但是有个条件就是，被触发的dom需要设置为position:relative或者position:absolute，否则会返回相对html文档区域左上角的距离。

  兼容性：IE6/7/8不支持，opera不支持，IE9/10和Chrome、Safari均支持

#### event.target

触发事件的对象 (某个DOM元素) 的引用

- offsetHeight/offsetWidth：元素自身可见高度 + padding + border + 滚动条(17)，不包含margin，不包括溢出不可见部分的高度。

- clientHeight/clientWidth：元素自身可见高度 + padding，不包含border与margin，不包括溢出不可见部分的高度。

- scrollHeight/scrollWidth：只读属性是一个元素内容高度的度量，包括由于溢出导致的视图中不可见内容，包含width和padding，不包含border与margin

- scrollTop/scrollLeft:获取元素滚动后的距离文档顶部的距离，也就是滚动条滚动的距离。

- clientTop:获取元素边框的厚度，也就是border的宽度。
   有个公式：target.scrollTop + target.offsetHeight === target.scrollHeight用于判断滚动条是否滚动到底。

### 不支持冒泡的事件

常见：focus blur mouseenter mouseleave load unload resize

### element

Element.getBoundingClientRect()：返回元素的大小（取决于box-sizing类型）及其相对于视口的位置。

![获取元素位置](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-获取元素位置-a0645655355a69a1ec6b7fa16381f143-abb.png)

	<div id="box"></div>
	var object=document.getElementById('box');  
	rectObject = object.getBoundingClientRect();

rectObject.top：元素上边到视窗上边的距离;
rectObject.right：元素右边到视窗左边的距离;
rectObject.bottom：元素下边到视窗上边的距离;
rectObject.left：元素左边到视窗左边的距离;
rectObject.width：是元素自身的宽
rectObject.height是元素自身的高

## HTMLElement.dataset

The dataset read-only property of the HTMLElement interface provides read/write access to custom data attributes (data-*) on elements. 

```
<div id="user" data-id="1234567890" data-user="johndoe" data-date-of-birth>John Doe</div>
```

```
const el = document.querySelector('#user');

// el.id === 'user'
// el.dataset.id === '1234567890'
// el.dataset.user === 'johndoe'
// el.dataset.dateOfBirth === ''

// set a data attribute
el.dataset.dateOfBirth = '1960-10-03';
// Result: el.dataset.dateOfBirth === '1960-10-03'

delete el.dataset.dateOfBirth;
// Result: el.dataset.dateOfBirth === undefined

if ('someDataAttr' in el.dataset === false) {
  el.dataset.someDataAttr = 'mydata';
  // Result: 'someDataAttr' in el.dataset === true
}
```

## BOM

`BOM` (Browser Object Model)，浏览器对象模型，提供了独立于内容与浏览器窗口进行交互的对象。其作用就是跟浏览器做一些交互效果。浏览器的全部内容可以看成`DOM`，整个浏览器可以看成`BOM`

window对象是浏览器的顶级对象

![BOM](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-06-2022-01-05-BOM-00d8a08e82dc61f55fb941068b1725ad-61b-20220106145931231-00d8a08e82dc61f55fb941068b1725ad-cdc.png)

### window常见事件

| 事件             | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| load             | 窗口加载事件，当文档内容完全加载完成会触发该事件，包括图像、脚本文本、CSS文件等 |
| DOMContentLoaded | 当DOM加载完成触发，不包括图像、脚本文本、CSS文件等           |
| resize           | 调整窗口大小加载事件                                         |

### 定时器

setTimeout() setInterval

```js
// 发送短信案例
var btn = document.querySelector('button');
var time = 3;
btn.addEventListener('click', function() {
	btn.disabled = true;
    var timer = seInterval(function() {
        if (time == 0) {
            clearInterval(timer);
            btn.disabled = false;
            time = 3;
        } else {
            btn.innerHTML = '还剩下' + time + '秒';
            time--;
        }
    }, 1000);
});
```

### location对象

用于获取或设置窗体的URL，并且可以用于解析URL。表示其链接到的对象的位置（URL）。所做的修改反映在与之相关的对象上

3.1  URL

统一资源定位符（Uniform Resource Locator）是互联网上标准资源的地址，互联网上的每个文件都有一个唯一的URL

```
// url一般语法格式
protocol: //host[:port]/path/[?query]#fragment
http: //www.itcast.cn/index.html?name=andy$age=18#link
```

| 组成     | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| protocol | 通信协议 常用的http ftp maito等                              |
| host     | 主机（域名）www.itheima.com                                  |
| port     | 端口号 可选，省略时使用方案的默认端口，如http的默认端口为80  |
| path     | 路径 由零活多个‘/’符号隔开的字符串，一般用来表示主机上的一个目录或者文件地址 |
| query    | 参数 以键值对的性质，通过&符号分隔开                         |
| fragment | 片段 #后面内容常见于连接锚点                                 |

location对象的属性和方法

| 属性              | 返回值                                 |
| ----------------- | -------------------------------------- |
| location.href     | 获取或者设置 整个URL                   |
| location.host     | 返回主机（域名）                       |
| location.port     | 返回端口号                             |
| location.pathname | 返回路径                               |
| location.search   | 包含url参数的DOMString，开头有一个“?”  |
| location.hash     | 包含块标识符的DOMString，开头有一个“#” |

| 方法               | 返回值                                                       |
| ------------------ | ------------------------------------------------------------ |
| location.assign()  | 跟href一样，可以跳转页面（也称为重定向页面）                 |
| location.replace() | 替换当前页面，因为不记录历史，所以不能后退页面               |
| location.reload()  | 重新加载页面，相当于刷新按钮或者f5，如果参数为true，则强制刷新ctrl+f5 |

### navigator对象

navigator对象包含有关浏览器的信息，它有很多属性，最常用的是userAgent

userAgent是只读属性

```javascript
chrome$ window.navigator.userAgent
// "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.159 Safari/537.36"

edge$ window.navigator.userAgent
'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/94.0.4606.71 Safari/537.36 Edg/94.0.992.38'

IE$ window.navigator.userAgent
// "Mozilla/5.0 (Windows NT 10.0; WOW64; Trident/7.0; .NET4.0C; .NET4.0E; rv:11.0) like Gecko"
```

### history对象

history对象，与浏览器历史记录进行交互，该对象包含用户（在浏览器窗口中）访问过的URL

允许操作浏览器的曾经在标签页或者框架里访问的会话历史记录。

`history.back()` 在浏览器历史记录里前往上一页, 用户可点击浏览器左上角的返回

`history.forward()` 在浏览器历史记录里前往下一页，用户可点击浏览器左上角的前进

`history.go()` 通过当前页面的相对位置从浏览器历史记录( 会话记录 )加载页面

`history.pushState(state, title[, url]) ` 按指定的名称和URL（如果提供该参数）将数据push进会话历史栈

`history.replaceState(stateObj, title[, url])` 按指定的数据，名称和URL(如果提供该参数)，更新历史栈上最新的入口

```javascript
window.history.back();
window.history.forward();
window.history.go(2);
window.history.go(-2);
```

```javascript
const state = { 'page_id': 1, 'user_id': 5 }
const title = ''
const url = 'hello-world.html'

history.pushState(state, title, url);
history.replaceState(stateObj, "", "bar2.html");
```

### 元素偏移量offset

利用offset系列相关属性可以动态的得到该元素的位置（偏移）、大小等。注意：返回的数值都不带单位

| offset系列属性       | 作用                                                         |
| -------------------- | ------------------------------------------------------------ |
| element.offsetParent | 返回作为该元素带有定位的父级元素，如果父级都没有定位则返回body |
| element.offsetTop    | 返回元素相对带有定位父元素上方的偏移                         |
| element.offsetLeft   | 返回元素相对带有定位父元素左边框的偏移                       |
| element.offsetWidth  | 元素自身可见宽度 + padding + border + 滚动条(17)，不包含margin，不包括溢出不可见部分的宽度 |
| element.offsetHeight | 元素自身可见高度 + padding + border + 滚动条(17)，不包含margin，不包括溢出不可见部分的高度 |

```js
// 模态框拖拽功能
title.addEventListener('mousedown', function(e) {
    // 1、当鼠标按下，获取鼠标在盒子内的坐标
    var x = e.pageX - login.offsetLeft;
    var y = e.pageY - login.offsetTop;
   	
    // 2. 鼠标移动时，把鼠标在页面中的坐标减去鼠标在盒子内的坐标
    // 将它赋给盒子的top和left值
    function move(e) {
        login.style.left = e.pageX - x + 'px';
        login.style.top = e.pageY - y + 'px';
    }
    document.addEventListener('mousemove', move);
    
    // 3. 鼠标弹起，就让鼠标移动事件移除
    document.addEventListener('mouseup', function() {
        document.removeEventListener('mousemove', move);
    });
});
```

### 元素可视区client

通过client系列的相关属性可以动态的得到该元素的边框大小、元素大小等。

| client系列属性       | 作用                                                         |
| -------------------- | ------------------------------------------------------------ |
| element.clientTop    | 返回元素上边框的大小                                         |
| element.clientLeft   | 返回元素左边框的大小                                         |
| element.clientWidth  | 元素自身可见宽度 + padding，不含border与margin，不包括溢出不可见部分的高度。返回数值不带单位 |
| element.clientHeight | 元素自身可见高度 + padding，不含border与margin，不包括溢出不可见部分的高度。返回数值不带单位 |

### 元素滚动scroll系列

使用scroll系列的相关属性可以动态的得到该元素的大小、滚动距离等，返回数值不带单位

| scroll系列属性       | 作用                                                         |
| -------------------- | ------------------------------------------------------------ |
| element.scrollTop    | 返回被卷去的上侧距离，从上边框下沿开始计算                   |
| element.scrollLeft   | 返回被卷去的左侧距离                                         |
| element.scrollWidth  | 只读属性。是一个元素内容宽度的度量，包括由于溢出导致的视图中不可见内容，包含padding和width，不包含border与margin |
| element.scrollHeight | 只读属性。是一个元素内容高度的度量，包括由于溢出导致的视图中不可见内容，包含padding和width，不包含border与margin |

注：元素被卷去的头部是element.scrollTop，如果是页面被卷去的头部则是window.pageYoffset

```javascript
// 应用：是否滚动到底部
target.scrollTop + target.offsetHeight === target.scrollHeight
```

### IFrame contentWindow

contentDocument 属性能够以 HTML 对象来返回 iframe 中的文档。可以通过所有标准的 DOM 方法来处理被返回的对象。

```html
<script>
function changeStyle(){
    var x=document.getElementById("myframe");
    var y=x.contentWindow;
    if (y.document)y=y.document;
    y.body.style.backgroundColor="#0000ff";
}
</script>
    
<iframe id="myframe" src="demo_iframe.htm">
	<p>你的浏览器不支持iframes。</p>
</iframe>
```

### postMessage

```js
otherWindow.postMessage(message, targetOrigin, [transfer]);
```

| 参数         | 说明                                                         |
| :----------- | :----------------------------------------------------------- |
| otherWindow  | 其他窗口的一个引用，比如 iframe 的 contentWindow 属性、执行 window.open 返回的窗口对象、或者是命名过或数值索引的 window.frames。 |
| message      | 将要发送到其他 window的数据。                                |
| targetOrigin | 指定哪些窗口能接收到消息事件，其值可以是 *****（表示无限制）或者一个 URI。在发送消息的时候，如果目标窗口的协议、主机地址或端口这三者的任意一项不匹配targetOrigin提供的值，那么消息就不会被发送；只有三者完全匹配，消息才会被发送。 |
| transfer     | 可选，是一串和 message 同时传递的 Transferable 对象。这些对象的所有权将被转移给消息的接收方，而发送一方将不再保有所有权。 |

例子iframe通信：

```html
<div>
    <input id="text" type="text" value="Runoob" />
    <button id="sendMessage" >发送消息</button>
</div>
<iframe loading="lazy" id="receiver" src="https://c.runoob.com/runoobtest/postMessage_receiver.html" width="300" height="360">
    <p>你的浏览器不支持 iframe。</p>
</iframe>
<script>
  window.onload = function() {
      var receiver = document.getElementById('receiver').contentWindow;
      var btn = document.getElementById('sendMessage');
      btn.addEventListener('click', function (e) {
          e.preventDefault();
          var val = document.getElementById('text').value;
          receiver.postMessage("Hello "+val+"！", "https://c.runoob.com");
      });
  }
</script>
```

接收程序：https://c.runoob.com/runoobtest/postMessage_receiver.html

```html
<div id="recMessage">
Hello World!
</div>
<script>
window.onload = function() {
    var messageEle = document.getElementById('recMessage');
    window.addEventListener('message', function (e) {  // 监听 message 事件
      // e.source – 消息源，消息的发送窗口/iframe。
      // e.origin – 消息源的 URI(可能包含协议、域名和端口)，用来验证数据源。
      // e.data – 发送过来的数据。
        alert(e.origin);
        if (e.origin !== "https://www.runoob.com") {  // 验证消息来源地址
            return;
        }
        messageEle.innerHTML = "从"+ e.origin +"收到消息： " + e.data;
    });
}
</script>
```

### 其他

1、window.top：顶层窗口，即浏览器窗口

2、window.parent：返回父窗口，如果一个窗口没有父窗口,则它的 `parent` 属性为自身的引用。如果当前窗口是一个 `<iframe>`, 则它的父窗口是嵌入它的那个窗口

3、x.opener：返回打开当前窗口的那个窗口的引用，例如：在window A中打开了window B，B.opener 返回 A.

window.opener 是 window.open 打开的子页面调用父页面对象

opener：对打开当前窗口的window对象的引用，如果当前窗口被用户打开，则它的值为null。

self代表自身窗口，opener代表打开自身的那个窗口，比如窗口a.html打开窗口b.html。如果靠window.open方法，则对于窗口b.html，self代表b.html自己，而opener代表窗口a.html。

4、window.open() 是用指定的名称将指定的资源加载到浏览器上下文（窗口 `window` ，内嵌框架 `iframe` 或者标签 `tab` ）

```javascript
let windowObjectReference = window.open(strUrl, strWindowName, [strWindowFeatures]);
// strUrl === 要在新打开的窗口中加载的URL。
// strWindowName === 新窗口的名称。
// 打开的新窗口对象的引用。如果调用失败，返回值是 null。如果父子窗口满足“同源策略”，可通过这个引用访问新窗口的属性或方法。
```

Window.getComputedStyle() 

返回一个对象，该对象在应用活动样式表并解析这些值可能包含的任何基本计算后报告元素的所有CSS属性的值。 私有的CSS属性值可以通过对象提供的API或通过简单地使用CSS属性名称进行索引来访问。

## 总结

### 判断元素是否在可视区域

https://juejin.cn/post/7006521586836570126

1、元素的位置和大小

```javascript
function isContain(dom) {
    // 获取可视窗口的高度，兼容浏览器
    const screenHeight = window.innerHeight || document.documentElement.clientHeight || document.body.clientHeight;
    // 获取滚动条滚动的高度，返回被卷去的上侧距离
    const scrollTop = document.documentElement.scrollTop;
    // 获取当前元素所在位置偏移的高度
    const offsetTop = dom.offsetTop;
    // 元素距离可视窗口顶端的距离
  	const top = offsetTop - scrollTop
    return top <= screenHeight;
}
```

2、getBoundingClientRect

```javascript
const target = document.querySelector('.target');
const clientRect = target.getBoundingClientRect();
console.log(clientRect);
// top：就是元素上外边框到视口顶端距离
// left：就是元素左外边框到视口左端距离
// bottom：就是元素下外边框到视口顶端距离
// right：就是元素右外边框到视口左端距离
```

```javascript
function isContain(dom) {
  const totalHeight = window.innerHeight || document.documentElement.clientHeight;
  const totalWidth = window.innerWidth || document.documentElement.clientWidth;
  // 当滚动条滚动时，top, left, bottom, right时刻会发生改变。
  const { top, right, bottom, left } = dom.getBoundingClientRect();
  return (top >= 0 && left >= 0 && right <= totalWidth && bottom <= totalHeight);
}
```

3、通过webAPI，Intersection Observer

重叠观察者，用于判断两个元素是否重叠，因为不用进行事件的监听，性能方面相比`getBoundingClientRect `会好很多

使用步骤主要分为两步：创建观察者和传入被观察者

创建观察者：

```javascript
const options = {
  // 表示重叠面积占被观察者的比例，从 0 - 1 取值，
  // 1 表示完全被包含
  threshold: 1.0, 
  rootMargin: "0px" // 给祖先节点设置margin。用来扩展或缩小`rootBounds`这个矩形的大小，从而影响`intersectionRect`交叉区域的大小。
  root:document.querySelector('#scrollArea') // 必须是目标元素的父级元素
};

const callback = (entries, observer) => { ....}
const observer = new IntersectionObserver(callback, options);
```

通过`new IntersectionObserver`创建了观察者 `observer`，传入的参数 `callback` 在重叠比例超过 `threshold` 时会被执行

关于`callback`回调函数常用属性如下：

```javascript
// 上段代码中被省略的 callback
const callback = function(entries, observer) { 
    entries.forEach(entry => {
        entry.time;               // 触发的时间
        entry.rootBounds;         // 根元素的位置矩形，这种情况下为视窗位置
        entry.boundingClientRect; // 被观察者的位置举行
        entry.intersectionRect;   // 重叠区域的位置矩形
        entry.intersectionRatio;  // 重叠区域占被观察者面积的比例（被观察者不是矩形时也按照矩形计算）
        entry.target;             // 被观察者
    });
};
```

传入被观察者：

通过 `observer.observe(target)` 这一行代码即可简单的注册被观察者

```javascript
const target = document.querySelector('.target');
observer.observe(target);
```

### ResizeObserver

https://juejin.cn/post/7129046057719169055#heading-0

监视 Element 内容盒或边框盒或者 SVGElement 边界尺寸的变化

```typescript
new ResizeObserver((entries, observer) => void)
```



```javascript
// 1. 构造函数，创建并返回一个 ResizeObserver 对象
const resizeObserver = new ResizeObserver(entries => {
  console.log('监听到了尺寸变化了...', entries)
})

// 2. 开始观察指定的 Element 或 SVGElement 的尺寸变化
resizeObserver.observe(document.getElementById('box'))

// 3. 结束观察指定的 Element 或 SVGElement
resizeObserver.unobserve(document.getElementById('box'))

// 4. 取消和结束目标对象上所有对 Element或 SVGElement 观察
resizeObserver.disconnect()
```

示例:

```javascript
let loginPage: HTMLElement
let resizeObserver: ResizeObserver
onMounted(() => {
    loginPage = document.querySelector('.login-vue') as HTMLElement
    resizeObserver = new ResizeObserver(entries => {      
        if (entries[0].contentRect.width < 700) {
            loginPage!.style.backgroundSize = '0% 0%'
        } else {
            loginPage!.style.backgroundSize = '80% 100%'
        }
    })
    resizeObserver.observe(loginPage)
})

onUnmounted(() => resizeObserver.unobserve(loginPage))
```

























