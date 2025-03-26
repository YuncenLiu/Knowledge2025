---
createTime: 2024-06-17
---
## 1、基础概念

### 1.1、文档声明

`<!DOCTYPE html>` 指的是 H5

不同版本的标识各不一致，可以通过官网查询 [https://www.w3.org/QA/2002/04/valid-dtd-list.html](https://www.w3.org/QA/2002/04/valid-dtd-list.html)

### 1.2、字符集

+ ASCII：大写字母、小写字母、数字、符号等一共128个
+ ISO 8859-1: 在 ASCII，扩充了希腊字符，共计 256个
+ GB2312: 继续扩充，收录 6763个常用汉字，682个字符（生僻字不包含）
+ GBK：收录了汉字和符号达到 20000+，支持繁体中文
+ UTF-8：万国码，包含世界上所有语言的：所有字符与符号

1. 如果是使用低版本保存文件，文件就直接废了，无法修复。
2. 如果是使用错误的解码方式打开文件，发现乱码，是可以补救的。

在 html 通过 `<meta charset="UTF-8">` 告知浏览器用 utf8 编码和解码，如果不写，默认使用 utf-8，如果乱写成其他格式，不适用工具的情况下，必定乱码。大写 `UTF-8` 和小写 `utf-8` 对浏览器无感

### 1.3、语言代码

第一种写法，（语言-国家/地区）
+ en：英文
+ zh-CN：简体中文
+ zh-TW：繁体中文
+ zh：中文
+ en-US：英语-美国
+ en-GB：英语-英国

```html
<html lang="en">
```

【W3School】

[HTML 语言代码参考手册](https://www.w3school.com.cn/tags/html_ref_language_codes.asp)

[HTML ISO 国家/地区代码参考手册](https://www.w3school.com.cn/tags/html_ref_country_codes.asp)

【W3C】

[Language tags in HTML and XML](https://www.w3.org/International/articles/language-tags/)

### 1.4、块级行内元素

原则：

1. 块级元素能写 行内元素、块级元素（几乎什么都能写）
2. 行内元素中只能写行内元素，但不能写块级元素
3. h1-h6不能相互嵌套
4. p 标签不能写块元素

> marquee 元素设计初衷是让文字有动画效果，但是如今我们可以通过 CSS 来实现了，而且还可以实现更加炫酷，现在 marquee 已经废弃了。

### 1.5、文本标签

1. 用于包裹：词汇、短语
2. 通常写在排版标签里面
3. 排版标签更宏观（大段文字），文本标签更微观（词汇、短语）
4. 文本标签通常都是行内元素

常用的：em（斜体 重要的内容）、strong（加粗 十分重要的内容）、span（没有意义）

### 1.6、图片引用

1. 使用本地绝对路径，一旦跟换设备，路径处理比较麻烦，所以很少用
2. 使用网络绝对路径，确实方便，但要注意：若服务开启了防盗链，会造成图片引入失败

常见的图片格式

1. jpg 格式：有损压缩格式，支持颜色丰富，占用空间较小，对图片细节没有极高的要求，场景：网站产品宣传图
2. png 格式，无损压缩格式，支持颜色丰富，支持透明背景，使用场景：想让图片透明背景，想要更高质量的呈现图片，公司logo、重要配图等
3. bmp 格式：不进行压缩，支持颜色丰富，保留细节更多，占空间极大，对图片细节要求较高场景
4. gif 格式，仅支持 256种颜色，支持简单透明背景，支持动态图
5. webp 格式，专门用于网页呈现图片，具备上述几种格式的优点，但是兼容性不太好。
6. base64 格式，一串特殊文本，要通过浏览器打开，直接作为 img 标签、src 属性的值即可，并且不受文件位置的影响，可以作为一些较小的图，或者需要网页一起加载的图片

### 1.8、超链接

属性：`target`
+ `_self` 直接跳转
+ `_blank` 打开新标签跳转

`<a>` 可以包裹任意元素，但不包括自己

使用 `download` 属性，使浏览器强制下载


#### 1.8.1、锚点

1. 具有 `href` 属性的 `a` 标签是超链接，具有 `name` 属性的 `a` 标签是锚点
2. `name` 和 `id` 都是区分大小写的，且 `id` 最好不要用数字开头

跳转锚点

```html
<!--去本页面锚点-->
<a href="#htl">锚点，前往固定页面</a>

<!--去其他页面锚点-->
<a href="xxx.html#htl"></a>

<!--执行一段js-->
<a href="javascript:alter('hello');"></a>
```

### 1.9、列表

`ol`、`ul` 里面最好不要有除 `li` 之外其他的标签

但是可以使用 `li` 里面内嵌新的列表

#### 1.9.1、自定义列表

1. 包含术语名称以及术语描述
2. 一个 `dl` 就算自定义列表，`dt` 就算术语名称，`dd` 就算术语描述，可以有多个 `dd`

### 1.10、表格

整体部分包括：标题、头部、主体、脚注 四部分组成

涉及标签有

+ `table` 表格
+ `caption` 标签
+ `thead` 头部
+ `tbody` 主体
+ `tfoot` 脚注
+ `tr` 每一行
+ `th`、`td` 每一个单元格，`th` 给头部用的， `td` 给主体、脚注用的


## 常用标签补充


+ pre 按原文显示（一般用于页面中潜入大段代码）

### 表单

1. from 表单
    1. action 用于指定表单提交地址
    2. target 用于控制表单提交后，如何打开页面
        1. _self 在本地窗口打开
        2. _blank 在新窗口打开
    3. method 打开方式 post、get
2. input 输入框
    4. type 设置输入框类型
    5. name 用于指定提交数据名字
3. button 按钮

```html

<form action="https://www.baidu.com/s">
    <!--  wd 是百度定义的搜索关键词 date:2024-6-14 -->
    <input type="text" name="wd">
    <button>百度搜索</button>
</form>
<form action="https://search.jd.com/search" target="_blank" method="get">
    <!--  keyword 是京东定义的搜索关键词 date:2024-6-14 -->
    <!--  云：需要跳转登录页面后才可以搜索 date-:2024-6-14 -->
    <input type="text" name="keyword">
    <button>京东搜索</button>

</form>
```

#### 常用表单控件

1. name 数据名称
2. value 输入框默认值
3. maxlength 输入框最大可输入长度
4. type 输入框类型 【text、password、radio、checkbox、hidden、submit、reset、button】

隐藏域： type="hidden" 用户不可见区域，作用：提交表单时，携带一些固定的数据

普通按钮： type="button" 点击不会引起表单提交（submit）和表单重置（reset），不写默认为 submit 引起表单提交

点击文字 input 框获取焦点 `label`,用法：

```html
<label for="account">账户：</label>
<input id="account" type="text" name="keyword" value="" maxlength="10">
```

虽然按钮也可以实现 label 关联，但是很怪异，一般没人这么干。

#### iframe

引入其他页面，使用 a 标签触发显示

```html
<a href="https://www.liuyuncen.com" target="container">点我进主页</a>
<br>
<form action="https://so.toutiao.com/search" target="container">
    <input type="text" name="keyword">
    <input type="submit" value="搜索">
</form>
<br>
<iframe name="container" frameborder="0" width="1200" height="400"></iframe>
```

#### 字符实体

在 HTML，用特殊代码描述一个符号

+ ` ` 空格：&nbsp;、&#160;
+ `<` 小于号：&lt;
+ `>` 大于号：&gt;
+ `&` 和号：&amp;

### HTML全局属性

+ id 唯一标识，不能重复，不能在 head、html、meta、title、script、style 元素中使用
+ class 类名
+ style 样式
+ dir 内容方向 ltr、rtl
+ title 设置文字提示，一般超链接和图片用的多
+ lang 给标签设定语言

### meta元信息

```html

<meta charset="UTF-8">
<!--   针对IE 浏览器兼容设置 -->
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<!--    移动端配置-->
<meta name="viewport" content="width=device-width,initial-scale=1.0">
<!-- 网页关键字 -->
<meta name="keywords" content="8-12个英文逗号隔开的单词/词语">
<!-- 网页描述信息 -->
<meta name="description" content="80字以内的一句话，与网站内容相关">
<!-- 针对搜索引擎爬虫配置 -->
<meta name="robots" content="">
<!-- 配置网页作者 -->
<meta name="author" content="yun">
<!-- 网页生成工具 -->
<meta name="generator" content="WebStorm">
<!-- 网页版权信息 -->
<meta name="copyright" content="2023-2027©版权所有">
<!-- 网页自动刷新 真的会自动跳转 这种跳转不能后退，如果不配置url 就是原地跳转 -->
<meta http-equiv="refresh" content="10;url=https://www.baidu.com">
```

爬虫配置表
+ index：允许搜索爬虫索引
+ noindex：要求搜索爬虫不索引次页面
+ follow：允许搜索爬虫跟随此页面上链接
+ nofollow：要求搜索爬虫不跟随此页面链接
+ all：与 index、follow 等价
+ none： 与 noindex、nofollow 等价
+ noarchive：要求搜索引擎不缓存页面内容
+ nocache：noarchive 题代名词
