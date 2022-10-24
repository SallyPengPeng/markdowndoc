1. 多行编辑
鼠标选中多行，按下 Ctrl Shift L (Command Shift L) 即可同时编辑这些行；

鼠标选中文本，反复按 CTRL D (Command D) 即可继续向下同时选中下一个相同的文本进行同时编辑；

鼠标选中文本，按下 Alt F3 (Win) 或 Ctrl Command G(Mac) 即可一次性选择全部的相同文本进行同时编辑；

Ctrl 鼠标左键(Win) 或 Command 鼠标左键(Mac) 可以手动选择同时要编辑的多处文本

2. 显示菜单栏
按住alt，视图-显示菜单栏

3. emmet语法
https://blog.csdn.net/qq_33744228/article/details/80910377
(1)!  - 快速html文档结构

(2)id # ; class . ; 子节点 > ; 兄弟节点 + ; 上级节点 ^;
div#test - <div id="test"></div>
div.test - <div class="test"></div>
div>ul>li>p

(3)重复*
div*3
```
<div></div>
<div></div>
<div></div>
```
(4)分组
div>(ul>li>a)+div>p  括号里面的内容为一个代码块，表示与括号内部嵌套和外面的的层级无关
```
<div>
   <ul>
     <li><a href=""></a></li>
   </ul>
   <div>
     <p></p>
   </div>
 </div>
 ```
(5)编号$
ul>li.test$*3 （$代表一位数，后面跟上*数字就代表从1递增到填写的数字）
```
<ul>
   <li class="test1"></li>
   <li class="test2"></li>
   <li class="test3"></li>
 </ul>
 ```
