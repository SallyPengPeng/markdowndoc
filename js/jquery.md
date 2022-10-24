## jQuery

javaScript library，基于原生的js封装了一些功能

### jQuery的入口函数

必须放在入口函数中，等待DOM结构渲染完毕即可执行内部代码，不必等到所有外部资源加载完成

```html
// 方法1
$(function() {
	... // 此处是页面DOM加载完成的入口
})

// 方法2
$(document).ready(function() {
	... // 此处是页面DOM加载完成的入口
})
```

### DOM对象和jQuery对象

```html
// 1. DOM对象，使用原生js获取的对象
// 只能使用原生的js属性和方法
var myDiv = document.querySelector('div');

// 2. jQuery对象
// 只能使用jQuery封装方法
$('div');
```

```
// DOM对象和jquery对象转换
 // DOM对象转换为jQuery对象：$(DOM对象)
 $('div')
 // jQuery对象转换为DOM对象（两种方式）
 $('div')[index] // index是索引号
 $('div').get(index)
```

### jQuery选择器

和css选择器保持一致，$('p')  $('#id') $('.class') $('ul li') ……

隐式迭代：jQuery自动给匹配到的所有元素进行循环遍历，执行相应的方法

#### 1. 筛选选择器

| 语法       | 用法          | 描述                                             |
| ---------- | ------------- | ------------------------------------------------ |
| :first     | $('li:first') | 获取第一个li元素                                 |
| :last      | $('li:last')  | 获取最后一个li元素                               |
| :eq(index) | $('li:eq(2)') | 获取到的li元素中，选择索引号为2的（索引从0开始） |
| :odd       | $('li:odd')   | 获取到的li元素中，选择索引号为奇数的             |
| :even      | $('li:even')  | 获取到的li元素中，选择索引号为偶数的             |

#### 2. 筛选方法

| 语法               | 用法                           | 说明                                                   |
| ------------------ | ------------------------------ | ------------------------------------------------------ |
| parent()           | $('li').parent();              | 查找父级                                               |
| parents(selector)  | $("li").parents(".parents")    | 返回指定祖先元素                                       |
| children(selector) | $('ul').children('li')         | 相当于$('ul>li')，最近一级（亲儿子）                   |
| find(selector)     | $('ul').find('li')             | 相当于$('ul li') 后代选择器                            |
| siblings(selector) | $('.first').siblings('li')     | 查找兄弟节点，不包括自己本身                           |
| nextAll([expr])    | $('.first').nextAll()          | 查找当前元素之后所有的同辈元素                         |
| prevtAll([expr])   | $('.last').prevAll()           | 查找当前元素之前所有的同辈元素                         |
| hasClass(class)    | $('div').hasClass('protected') | 检查当前的元素是否含有某个特定的类，如果有，则返回true |
| eq(index)          | $('li').eq(2)                  | 相当于$('li:eq(2)'),index从0开始                       |

```html
// 鼠标经过时展示元素
$('.nav>li').mouseover(function() {
	$(this).children('ul').show();
});
// 鼠标拿走时隐藏元素
$('.nav>li').mouseout(function() {
	$(this).children('ul').hide();
});

// 一组按钮，鼠标点击更改背景颜色
$('button').click(function() {
	// 当前的元素变化背景颜色
	$(this).css('background', 'pink');
	// 其余的兄弟去掉背景颜色
	$(this).siblings('button').css('background', '');
});

// 左侧很多小li，点击小li右侧对应出来不同的图片
$('#left li').mouseover(function() {
	// 得到当前小li的索引号
	var index = $(this).index();
	// 显示右侧盒子相应索引号的图片
	$('#content div').eq(index).show();
	// 右侧盒子其他索引号的图片隐藏
	$('#content div').eq(index).siblings().hide();
});
```

#### 3. 链式编程

```
// 以上案例改写
// 一组按钮，鼠标点击更改背景颜色
$(this).css('background', 'pink').siblings('button').css('background', '');

// 左侧很多小li，点击小li右侧对应出来不同的图片
$('#content div').eq(index).show().siblings().hide();
```

### jQuery操作

#### 1. 操作样式

css()修改样式

```
// 1. 只写属性名，返回属性值
$(this).css('color'); 
// 2. 修改单个样式，属性必须加引号，如果值是数字，可以不加单位和引号
$(this).css('color', 'red');
$(this).css('width': 200);
$(this).css('height': '300px');
// 3. 参数可以是对象形式，设置多组样式，属性可以不加引号
$(this).css({ color: 'white', fontSize: 20, 'background-color': 'red' });
```

添加、删除、切换样式类

```
// 添加类
$('div').addClass('current');
// 移除类
$('div').removeClass('current');
// 切换类
$('div').toggleClass('current');
```

```
// tab切换示例
// 1. 点击上部的li，当前li添加current类，其余兄弟移除类
$('.tab_list li').click(function() {
	$(this).addClass('current').siblings().removeClass('current');
	// 2. 点击的同时，得到当前li的索引号
	var index = $(this).index();
	// 3. 让下部里面相应索引号的item显示，其余的item隐藏
	$('.tab_con .item').eq(index).show().siblings().hide();
});
```

#### 2. jQuery效果

a: 显示隐藏：show()  hide()  toggle()

b: 滑动：slideDown() slideUp() slideToggle()

```
// 事件切换，鼠标经过和离开的复合写法，相当于mouseover 、 mouseout
$('.nav>li').hover(function(){
	$(this).children('ul').slideDown();
}, function() {
	$(this).children('ul').slideUp();
});

// 或者 （因为事件切换hover，如果参数只有一个函数，那么鼠标经过和离开都会触发该函数）
$('.nav>li').hover(function(){
	$(this).children('ul').slideToggle();
});

// 动画或者效果一旦触发就会执行，如果多次触发，就造成多个动画或者效果排队执行
// 使用stop()停止结束上一次的动画
$(this).children('ul').stop().slideToggle();
```

c: 淡入淡出：fadeIn() fadeOut() fadeToggle() fadeTo()

d: 自定义动画：animate(params, [speed], [easing], [fn])

- params: 想要更改的样式属性，以对象形式传递

```
// 王者荣耀手风琴效果案例
// 鼠标经过某个小li，有两步操作：
$(".king li").mouseenter(function() {
	// 1. 当前小li 宽度变为224px，同时里面的小图片淡出，大图片淡入
	$(this).stop().animate({
		width: 224
	}).find(".small").stop().fadeOut().siblings(".big").stop().fadeIn();
	// 2. 其余兄弟小li宽度变为69px，小图片淡入，大图片淡出
	$(this).siblings("li").stop().animate({
		width: 69
	}).find(".small").stop().fadeIn().siblings(".big").fadeOut();
});
```

#### 3. jQuery属性操作

a: 设置或获取元素固有属性值prop()

```
// 获取到href的值
$("a").prop("属性");
// 修改href的值
$("a").prop("属性", "属性值");
```

b: 设置或获取元素自定义属性值attr()

```
// 获取到href的值
$(".a").attr("属性");
// 修改href的值
$(".a").attr("属性", "属性值");
```

#### 4. jQuery获取内容文本值

a: 普通元素内容html(相当于原生innerHTML)

```
html() // 获取元素的内容
html("内容") // 设置元素的内容
```

b: 普通元素文本内容text() (相当于原生innerText)

c: 表单的值val() (相当于原生value)

```
// 购物车增减产品数量
$(".increment").click(function() {
	// 得到当前兄弟文本框的值
	var n = $(this).siblings(".itxt").val();
	n++;
	$(this),siblings(".itxt").val(n);
});
$(".decrement").click(function() {
	// 得到当前兄弟文本框的值
	var n = $(this).siblings(".itxt").val();
	if ( n == 1) {
		return false;
	}
	n--;
	$(this),siblings(".itxt").val(n);
});
```

#### 5. jQuery遍历对象

a: jquery遍历元素

```
// 注意index 为每个元素的索引，domEle为 dom元素对象，要转换为jQuery对象，$(domEle)
$("div").each(function(index, domEle) {});
```

b: $.each() 方法遍历元素，主要用于遍历数据，处理数据，可用于遍历数组、对象

```
$.each(arr, function(index, ele) {});
```

#### 6. 操作元素

a: 创建元素

var li = $("<li> create element</li>");

b: 添加元素到dom中

```
// 1. 内部添加
$("ul").append(li); // 内部添加并且放到内部元素的最后面
$("ul").prepend(li); // 内部添加并且放到内部元素的最前面
// 2. 外部添加
$("ul").after(li); // 外部添加并且放到外部元素的最后面
$("ul").before(li); // 外部添加并且放到外部元素的最前面
```

c:  从dom中删除元素

```
$("ul").remove(); // 可以删除匹配的元素 
$("ul").empty(); // 可以删除匹配的元素里面的子节点
$("ul").html(); // 可以删除匹配的元素里面的子节点
```

#### 7. 尺寸、位置操作

a: 获取/设置尺寸

| 语法                               | 用法                                               |
| ---------------------------------- | -------------------------------------------------- |
| width()/height()                   | 取得匹配元素宽度和高度值，只算width/height         |
| innerWidth()/innerHeight()         | 取得匹配元素宽度和高度值，包含 padding             |
| outerWidth()/outerHeight()         | 取得匹配元素宽度和高度值 包含padding border        |
| outerWidth(true)/outerHeight(true) | 取得匹配元素宽度和高度值 包含padding border margin |

b: 获取/设置位置

1. offset()设置或获取元素偏移

   - 该方法设置或返回被选元素相对于文档的偏移坐标，与父元素没有关系
   - 该方法有2个属性left top。offset().top用于获取距离文档顶部的距离

   - 可以设置元素的偏移：offset({ top: 10, left: 30 });

2. position() 获取距离带有定位父级位置（偏移），如果没有定位的父级，则以文档为准

   这个方法只能获取不能设置偏移

3. scrollTop()/scrollLeft()设置或获取元素被卷去的头部和左侧

```
// 返回顶部按钮案例
var boxTop = $(".container").offset().top;
$(window).scroll(function() {
	if ($(document).scrollTop() >= boxTop) {
		$(".back").fadeIn();
	} else {
		$(".back").fadeOut();
	}
});
// 返回顶部
$(".back").click(function() {
	// 不能是文档而是html和body元素做动画
	$("body, html").stop().animate({
		scrollTop: 0,
	});
});
```

```
// 电梯导航案例
// 1. 显示隐藏电梯导航
var  flag = true; // 互斥锁，避免页面滚动事件和小li点击事件都对电梯导航添加current类
var toolTop = $(".recommend").offset().top;
function toggleTool() {
	if ($(document).scrollTop() >= toolTop) {
		$(".fixedTool").fadeIn();
	} else {
		$(".fixedToll").fadeOut();
	}
}

// 进入页面后调用，避免刷新后，满足电梯导航展示的条件，但是电梯导航不展示
toggleTool();

// 页面滚动时绑定
$(window).scroll(function() {
	toggleTool();
	if (flag) {
		// 此处貌似有bug，当页面停留在第2个区域，刷新页面后滚动，此时scrollTop()也大于第1个
		$(".floor .w").each(function(i, domEle) {
			if ($(document).scrollTop() >= $(domEle).offset().top) {
		$(".fixedtoolli").eq(i).addClass("current").siblings().removeClass("current");
			}
		});
	}
});

// 2. 点击电梯导航页面可以滚动到相应内容区域
$(".fixedtool li").click(function() {
	flag = false;
	var current = $(".floor .w").eq($(this).index()).offset().top;
	// 页面动画滚动效果
	$("body, html").stop().animate({
		scrollTop: current,
	}, function() {
		flag = true;
	});
	// 点击之后，让当前的小li添加current类名，兄弟移除current类名
	$(this).addClass("current").siblings().removeClass("current");
});
```

### jQuery事件

#### 1. 事件注册on

- 事件绑定

```
element.on(events, [selector], fn)
```

events:  一个或多个用空格分隔的事件类型，如‘click’或‘keydown’

selector: 元素的子元素选择器

fn: 回调函数即绑定在元素身上的侦听函数

```
// 优势1. 绑定一个或多个事件
$("div").on({
	click: function() {},
	mouseOver: function() {},
});
// 多个事件绑定方法相同时
$("div").on("click mouseover", function() {});

// 优势2. 可以事件委派操作，把原来加给子元素身上的事件绑定在父元素身上，把事件委派给父元素
// 注意：事件绑定在ul，但是触发对象是li
$("ul").on('click', 'li', function() {
	alert('hello world!');
});

// 优势3. on可以给未来动态创建的元素绑定事件
// 普通的click不能绑定动态创建的元素，因为绑定时，元素可能不存在
```

- 事件解绑off

```
$("p").off(); // 加帮p元素所有事件处理程序
$("p").off("click"); // 解绑p元素上面的点击事件
$("p").off("click", "li"); // 解绑事件委托
```

- 只处罚1次事件，用one()来绑定事件

#### 2. 事件自动触发

```
// 绑定事件
$("div").on("click", function() {
	alert(11);
});
// 3种方法
$("div").click();
$("div").trigger("click");
// 此方法不会触发元素的默认行为，例如表单输入框获取到元素以后会有闪动
$("div").triggerHandler("click"); 
```

#### 3. 事件对象

事件被触发，就会有事件对象的产生

```
element.on(events, [selector], function(event) {})
```

阻止默认行为：event.preventDefault() 或者return false

阻止冒泡：event.stopPropagation()