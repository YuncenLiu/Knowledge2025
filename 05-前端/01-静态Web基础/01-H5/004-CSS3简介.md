# CSS3

## CSS3简介

### CSS3私有前缀

如下代码中 `-webkit-` 就是私有前缀

```css
div {
	width:400px;
	height: 400px;
	-webkit-border-radius: 20px;
}
```

W3C 标准所提出的某个 CSS 特性, 在被浏览器正式支持之前, 浏览器厂商会根据浏览器的内核, 使用私有前缀来测试改 CSS 特性,在浏览器正式支持 CSS 特性后,就不需要私有前缀了.

查询 CSS3 兼容性的网站 [http://caniuse.com](http://caniuse.com)

1. `Chrome` 浏览器: `-webkit-`
2. `Safari` 浏览器 `-webkit-`
3. `Firefox` 浏览器 `-moz-`
4. `Edge` 浏览器 `-webkit-`
5. 旧 `Opera` 浏览器 `-o-`
6. 旧 `IE` 浏览器 `-ms-`

举例子,按顺序写

```css
-webkit-border-radius: 20px;
-moz-border-raidus: 20px;
-mz-border-radius: 20px;
-o-border-radius: 20px;
border-radius: 20px
```

### CSS3新增长度

1. `rem` 根元素字体大小的倍数,只与根元素字体大小有关
2. `vm` 视口宽度的百分之多少, 10vm 就是视口宽度的 10%
3. `vh` 视口高度的百分之多少, 10vh 就是视口高度的 10%
4. `vmax` 视口宽高中最大的那个百分比, vmin 含义相似

### CSS3新增盒模型

#### box-sizing怪异盒子模型

属性:  `box-sizing: border-box;` 

1. `content-box` 默认值, `width` 和 `height` 是盒子内容区的大小
2. `border-box` 是盒子的总大小,会 压缩 `padding` 和 `border` 的值

#### resize调整盒子大小

使用 `resize: both;` 设置用户是否直接在页面上调整尺寸

1. `none` 不允许(默认值)
2. `both` 宽高都可以调整
3. `horizontal` 只可以调整宽度, `vertical` 只可用调整宽

注意, 需要在当前被调整元素添加 `overflow` 元素才会生效 原因是因为如果存在子元素超出当前元素的情况下, 是否展示与滚动条的问题.

> 实际没啥用

#### box-shadow

为盒子添加阴影

语法: `box-shadow: h-shadow v-shadow blur spread color inset;`

1. `h-shadow` 水平阴影,必须写,可用为负数
2. `v-shadow` 垂直阴影,必须写,可用为负数
3. `blur` 可选,模糊距离
4. `spread` 可选,阴影外延值
5. `color` 可选,阴影颜色
6. `inset` 可选, 将外部阴影改为内部阴影

`box-shadow: none;` 表示没有阴影, 配合 `position`. `transition` 效果更佳


```css
.box2 {  
    height: 200px;  
    width: 200px;  
    margin: 0 auto;  
    background-color: skyblue;  
    position: relative;  
    transition: 100ms linear all;  
  
}  
.box2:hover {  
    top: -5px;  
    left: 0;  
    box-shadow: 10px 10px 20px -10px black;  
}
```


#### opacity不透明度

`opacity` 属性能为整个元素添加透明效果,值是 `0 - 1 ` 之间的小数, `0`  是完全透明, `1` 是完全不透明

> `opacity` 与 `rbga` 的区别? `opacity` 是一个 css3 的属性, 设置的是整个元素,包括元素里的内容的不透明度, `rgba` 用于设置颜色,仅仅是调整颜色的不透明度


#### background-origin设置背景图

设置背景图的原点： `background-origin`

1. `padding-box` 从 padding 区域开始显示背景图（默认值）
2. `border-box` 从 border区域开始显示
3. `content-box` 从 content 区域开始显示

#### background-clip背景图裁切

作用：设置背景图的向外裁剪的区域

1. `border-box` 从 boxer 区域开始向外裁切（默认值）
2. `padding-box` 从 padding 区域开始
3. `content-box` 从 content 区域开始
4. `text` 背景图只呈现在文字上

> 若值为 text ，需要修改为 -webkit-background-clip

#### background-size背景图尺寸

设置背景图尺寸 `background-size`

1. 用长度指定背景图大小，不允许负值
	```css
	background-size: 400px 400px
	```
2. 用百分比指定背景图大小，不允许负值
	```css
	background-size: 100% 100%;
	```
3. `auto` 背景图真实大小，（默认值）
4. `contain` 背景图等比缩放，使背景图宽高与容器相等，可能会造成容器部分没有背景图片
	```css
	background-size: contain;
	```
5. `cover` 将背景图等比缩放，直到完全覆盖容器，（相对较好）
	```css
	background-size: cover;
	```

#### background复合属性

```css
background: color url repeat posistion / size origin clip
```

1. `origin` 和 `clip` 的值如果一样，如果只写一个值， 则 `origin` 和 `clip` 都设置，`origin` 必须写前面， `clip` 写后面
2. `size` 的值必须可在 `position` 的后面，并且用 / 分开

#### 多背景图


```css
background: url('../../doc/source/4_CSS3/images/bg-lt.png') no-repeat left top,  
url('../../doc/source/4_CSS3/images/bg-rt.png') no-repeat right top,  
url('../../doc/source/4_CSS3/images/bg-lb.png') no-repeat left bottom,  
url('../../doc/source/4_CSS3/images/bg-rb.png') no-repeat right bottom;
```

![多背景图](images/Pasted%20image%2020240624135729.png)

#### border-radius圆角边框

同时设置四个角：`border-radius: 50%;`

也可以分别设置，不过一般不用，`border-top-left-radius` ，一个值是正圆半径，两个值是椭圆的 x半径，y半径，然后还有 `border-top-right-radius`、`border-bottom-right-radius`、`border-bottom-left-radius` 根据瞬时间旋转形成的复合属性


#### 边框外轮廓

1. `outline-width` 外轮廓宽度
2. `outline-color` 外轮廓颜色
3. `outline-style` 外轮廓风格
	1. `none` 无轮廓
	2. `dotted` 点状
	3. `dashed` 虚线
	4. `solid` 实现
	5. `double` 双实线
4. `outline-offset `边框距离，正负都可以
5. `outline` 复合属性 `outline: 50px solid blud;`


### CSS3文本属性

#### text-shadow文本阴影

语法： `text-shadow：h-shadow v-shadow blur color;`

1. `h-shadow` 水平阴影，允许负值
2. `v-shadow `垂直阴影，允许负值
3. `blur` 模糊距离
4. `color` 颜色

#### white-space文本换行

1. `normal` 文本超出边界自动换行，文本中换行被浏览器识别为空格（默认值）
2. `pre` 原样输出，与 `pre` 标签效果相同
3. `pre-wrap` 在 `pre` 基础上，超出元素边界自动换行
4. `pre-line` 在 `pre` 基础上，超出边界自动换行，且识别文本中的换行，空格会忽略掉
5. `nowrap` 强制不换号

#### text-overflow文本溢出

1. clip 当内联内容溢出时，将溢出部分拆切掉（默认值）
2. ellipsis 当溢出时，将溢出部分替换为 ...

> 块容器必须定义为 overflow 为非 visible 值， white-space 为 nowrap 值才会生效

![text-overflow文本溢出](images/Pasted%20image%2020240624160414.png)

```css
ul {  
    width: 400px;  
    height: 400px;  
    border: 1px solid black;  
    margin: 100px auto;  
    font-size: 20px;  
    list-style: none;  
    padding: 10px;  
}  
  
li {  
    margin-bottom: 10px;  
    white-space: nowrap;  
    overflow: hidden;  
    text-overflow: ellipsis;  
}
```


#### text-decoration文本修饰

语法： `text-decoration: text-decoration-line  text-decoration-style  text-decoration-color`

1. `text-decoration-line` 线位置
	1. `none` 无修饰
	2. `underline` 下划线、`overline` 上划线、`line-through` 中划线
2. `text-decoration-style` 线条形状
	1. `solid` 实线（默认）
	2. `double` 双实线、`dotted` 点状线、`dashed` 虚线、`wavy` 波浪线
3. `text-decoration-color` 线颜色

#### 文本描边

目前只支持 -webkit- 内核浏览器

1. `-webkit-text-stroke-width` 文字描边宽度，长度
2. `-webkit-text-stroke-color` 颜色
3. `-webkit-text-stroke` 复合属性

```css
-webkit-text-stroke: 1px red;
```

### CSS3渐变

![](images/Pasted%20image%2020240624164036.png)

属性名： `background-image: linear-gradient()`

```css
.box1 {  
    background-image: linear-gradient(red, yellow, green);  
}  
  
.box2 {  
    background-image: linear-gradient(to right, red, yellow, green);  
}  
.box3 {  
    background-image: linear-gradient(20deg, red, yellow, green);  
}  
.box4 {  
    background-image: linear-gradient( red 50px, yellow 100px, green 150px);  
}  
.box5 {  
    background-image: linear-gradient(20deg, red, yellow, green);  
    text-align: center;  
    line-height: 200px;  
    font-weight: bold;  
    color: transparent;  
    -webkit-background-clip: text;  
}
```


径向渐变

```css
.box1 {  
    background-image: radial-gradient(red, yellow,green);  
}  
.box2 {  
    background-image: radial-gradient(at right top ,red, yellow,green);  
}  
.box3 {  
    background-image: radial-gradient(at 100px 50px ,red, yellow,green);  
}  
.box4 {  
    background-image: radial-gradient(circle ,red, yellow,green);  
}  
.box5 {  
    background-image: radial-gradient(200px 200px ,red, yellow,green);  
}  
.box6 {  
    background-image: radial-gradient(red 50px, yellow 100px ,green 150px);  
}  
.box7 {  
    background-image: radial-gradient(100px 50px at 150px 150px ,red 50px, yellow 100px ,green 150px);  
}
```


### web字体

> [阿里 定制字体网站](https://www.iconfont.cn/webfont#!/webfont/index) 

简写

```css
@font-face {  
    font-family: "情书字体";  
    src: url('./font1/方正手迹.ttf');  
}  
```

语法 (高兼容写法)

```css

@font-face {  
    font-family: "atguigu";  
    font-display: swap;  
    src: url('./font2/webfont.eot'); /* IE9 */  
    src: url('./font2/webfont.eot?#iefix') format('embedded-opentype'), /* IE6-IE8 */  
    url('./font2/webfont.woff2') format('woff2'),  
    url('./font2/webfont.woff') format('woff'), /* chrome、firefox */  
    url('./font2/webfont.ttf') format('truetype'), /* chrome、firefox、opera、Safari, Android, iOS 4.2+*/  
    url('./font2/webfont.svg#webfont') format('svg'); /* iOS 4.1- */  
}
```


### 2D变化

#### 2D位移

首先给元素添加转换属性 transform, 具体属性参考如下

1. translateX 水平位移
2. translateY 垂直位移
3. translate 第一个值代表水平 第二个值代表垂直

注意点:

1. 位移与相对定位很相识, 都不脱离文档流,不会影响其他元素
2. 与相对定位的区别,相对定位的百分比值,参考的都是父元素,定位的百分比值,参考其自身
3. 浏览器针对位移有优化,与相对定位相比,浏览器处理位移效率更高
4. transform 可用链式编程 transform: translateX(30px) taranslateY(40px)
5. 位移对行内元素无效
6. 配合位移实现元素水平居中
	```css
.box{
	position: absolute;  
	top: 50%;  
	left: 50%;  
	transform: translate(-50%, -50%);
}
	```

#### 2D缩放

让元素放大或缩小

首先给 元素添加 `transform`

1. `scaleX` 水平缩放比例,值为一个数字, 1表示不缩放, 小于1缩小,大于1放大
2. `scaleY` 垂直缩放比例
3. `scale` 水平垂直,如果只写一个值,表示水平 垂直 都缩放

注意: scale 的值,支持写负数,但是几乎不用,容易让人产生误解, 借助缩放,可实现小于 12 px 的文字

#### 2D旋转

在二维平面内顺时针、逆时针旋转

首先给元素添加 `transform`

1. `rotate` 设置旋转角度， 角度值 deg，正值顺时针，负值逆时针

`rotateZ(30deg)` 相当于 `rotate(30deg) `


#### 2D扭曲

长方形 变成平行四边形，也是需要 `transform`， 值有 `skewX`、`skewY`、`skew` ，分别代表水平、垂直


#### 多重变化

使用 transform 把旋转、移动、缩放都放在一起

```css
transform: scale(0.5) translate(50px , 50px) rotate(20deg);
```

多重变化时，建议最后旋转

1. 移动和缩放之间关系：移动的距离根据自身大小相关，如果先缩放，移动的距离则会有变化
2. 旋转和其他的关系：如果先旋转的话，会导致 X、Y 坐标被修改 随后移动会在 旋转的基础上旋转，一般是 建议把具有破坏性的旋转放在最后面

#### 变换原点

元素变换时，默认原点是元素中心点，使用 transform-origin 设置变换原点，对位移没有影响，对缩放和旋转会产生影响，如果提供两个值，分别代表横坐标、纵坐标，如果提供一个值，比如 left，那就算左中。
1. `transform-origin: 50px 50px;`
2. `transform-origin: bottom right;`
3. `transform-origin: 20% 20%;`
4. `transform-origin: 0;`


### 3D变换

#### `transform-style`开启3D空间

给父元素设置 3D 空间

```css
transform-style: preserve-3d;
```

1. `flag`： 让子元素位于此元素的二维平面内（2D空间） 默认值
2. `preserve-3d`：3D空间

#### `perspective`设置景深

官方：观察者与 z=0 平面距离

使用 `perspective: 1px;` 设置景深，设置在发生 3D 变换元素的父元素

#### `perspective-origin`透视点位置

默认透视点在元素的中心，如果设置了 `padding`、`boder` 也要计算在里面，通常不需要调整透视点

```css
perspective-origin: 102px 102px;
```

#### `translate3d`位移

`translateZ` 设置z轴位置，需要制定长度，正值向屏幕外，负值向屏幕里，且不能写百分比

```css
transform: translate3d(100px,100px,-600px);
```

#### `scaleZ`缩放

```css
/* scaleZ(2) 可以理解为直接调整了 景深 perspective */
transform: scaleZ(2) rotateY(45deg);
```


#### `backface-visibility`背部可见性

```css
backface-visibility: hidden;
```


## 伸缩盒模型

伸缩容器： `display: flex;` 就变成伸缩容器了，一般是套在父元素，使得所有子元素变成伸缩项目

伸缩项目：伸缩容器所有子元素自动变成伸缩项目。

> `inline-flex` 会使得当前元素变成 行内块元素，多个块之间会有空隙，一般不用。


#### 主轴与侧轴

1. 主轴，水平的，默认从左往右
2. 侧轴，垂直的，默认从上往下

#### 主轴方向

通过属性 `flex-direction` 调整

1. `flex-direction: row; `水平从左到右，默认
2. `flex-direction: row-reverse;`   水平从右到左
3. `flex-direction: column; `  垂直从上到下 默认
4. `flex-direction: column-reverse; `  垂直从下到上

![主轴方向](images/Pasted%20image%2020240625154048.png)

### 主轴换行方式

属性名： `flex-wrap`  值分别有 `nowrap` 不换行，`wrap` 自动换行，` wrap-reverse` 反换行

![flex-wrap](images/Pasted%20image%2020240625154232.png)

### 主轴复合属性

```css
flex-flow: row wrap;
```

### 主轴对齐

水平位置对齐 属性名： `justify-content`

1. `flex-start` ：主轴起点对齐。—— 默认值
2. `flex-end `：主轴终点对齐。
3. `center` ：居中对齐
4. `space-between` ：均匀分布，两端对齐（最常用）。
5. `space-around` ：均匀分布，两端距离是中间距离的一半。
6. `space-evenly` ：均匀分布，两端距离与中间距离一致。

![justify-content主轴对齐](images/Pasted%20image%2020240625154743.png)


### 侧轴对齐

分单行、多行情
```css
/* 主轴的对齐方式，主轴的起始位置 */
justify-content: flex-start;  
  
/* 侧轴的对齐方式，侧轴的起始位置对齐 */
align-items: flex-start;  
  
/* 侧轴的对齐方式，侧轴的结束位置对齐 */
align-items: flex-end; 
  
/* 侧轴的对齐方式，侧轴的中间位置对齐 */
align-items: center; 
  
/* 侧轴的对齐方式，侧轴的中间位置对齐 */
align-items: baseline; 
  
/* 侧轴的对齐方式，拉伸到整个父容器（前提：伸缩项目不能给高度），默认 */
align-items: stretch; 
```


多行
```css
/* 侧轴的对齐方式（多行）侧轴的起始位置对齐 */ 
align-content: flex-start;   
  
/* 侧轴的对齐方式（多行）侧轴的结束位置对齐 */ 
align-content: flex-end;   
  
/* 侧轴的对齐方式（多行）侧轴的中间位置对齐 */ 
align-content: center;   
  
/* 侧轴的对齐方式（多行），伸缩项目之间的距离是相等的，且是边缘距离的2倍 */ 
align-content:space-around;   
  
/* 侧轴的对齐方式（多行），伸缩项目之间的距离是相等的，且边缘没有距离 */ 
align-content:space-between;   
  
/* 侧轴的对齐方式（多行），伸缩项目之间的距离是相等的 */ 
align-content:space-evenly;   
  
/* 侧轴的对齐方式（多行），拉伸，默认 */ 
align-content: stretch;
```


### Flex实现水平居中

```html
<div class="outer">  
    <div class="inner"></div>  
</div>
```

方案一
```css
.outer {  
    width: 400px;  
    height: 400px;  
    background-color: #888;  
    display: flex;  
     justify-content: center;   
     align-items: center;   
}  
.inner {  
    width: 100px;  
    height: 100px;  
    background-color: orange;  
}
```

方案二
```css
.outer {  
    width: 400px;  
    height: 400px;  
    background-color: #888;  
    display: flex;  
}  
.inner {  
    width: 100px;  
    height: 100px;  
    background-color: orange;  
    margin: auto;  
}
```


## 响应式布局

### 媒体查询

1. `all` 检测所有设备
2. `screen` 检测所有电子屏幕、手机、平板、各种屏幕
3. `print` 检测打印机

[完整列表](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@media)


![媒体特性](images/Pasted%20image%2020240625163620.png)

![屏幕](images/Pasted%20image%2020240625163637.png)

#### 运算符

1. `and` 并且
2. `，` 或。`or` 或
3. `not` 否定
4. `only` 只有

#### 外部样式

```html
<link rel="stylesheet" media="screen and (min-width:1200px)" href="./css/huge.css">
```

```css
/* 超小屏幕 */@media screen and (max-width:768px) {  
    h1 {  
        background-color: orange;  
    }  
}

/* 大屏幕 */@media screen and (min-width:992px) and (max-width:1200px) {  
    h1 {  
        background-color: deepskyblue;  
    }  
}
```

## BFC

+ MDN 对 BFC 描述
	块格式化上下文（Block Formatting Context，BFC） 是 Web 页面的可视 CSS 渲染的一部分，是块盒子的布局过程发生的区域，也是浮动元素与其他元素交互的区域。

BFC 可以理解为一个特异功能


### BFC解决什么问题

1. 开启 BFC 后，子元素不会产生 margin 塌陷问题
2. 开启后，直接不回被浮动元素所覆盖
3. 开启后，就算子元素浮动，元素自身高度也不会塌陷

### 如何开启

1. 根元素
2. 浮动元素
3. 绝对定位、固定定位元素
4. 行内块元素
5. 表单单元格 `table`、`thead`、`tbody`、`tfoot`、`th`、`td`、`tr`、`caption`
6. `overflow` 的值不为 `visable` 的块元素
7. 伸缩项目
8. 多列容器
9. `column-span` 为 `all` 的元素
10. `display` 的值为 `flow-root`

