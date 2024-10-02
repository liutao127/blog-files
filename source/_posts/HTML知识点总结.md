---
title: HTML知识点总结
date: 2022-04-03 14:00:34
tags: HTML
---

# 1、HTML5 的新特性

<!-- more -->

1. 语义化标签:header、footer、section、nav、aside、article
2. 增强型表单：input的多个type(color, date, datetime, email, month, number, range, search, tel, time, url, week)
3. 新增表单元素：
   - datalist 定义选项列表。请与 input 元素配合使用该元素，来定义 input 可能的值。 
   - keygen 规定用于表单的密钥对生成器字段。 
   - output 定义不同类型的输出，比如脚本的输出。 
4. 新增表单属性：placehoder, required, min和max, step, height 和 width, aut[ofo]()cus, multiple
5. 音频视频：
   - audio 定义音频内容 
   - video 定义视频（video 或者 movie） 
   - source 定义多媒体资源 video 和 audio 
   - embed 定义嵌入的内容，比如插件。 
   - track 为诸如 video 和 audio 元素之类的媒介规定外部文本轨道。 
6. canvas：
7. 拖拽：drag
8. 本地存储：localStorage、sessionStorage
9. 新事件：onresize、ondrag、onscroll、onmousewheel、onerror、onplay、onpause
10. WebSocket
11. 移除元素
    acronym，applet，basefont ，big ，center，dir，font，frame，frameset，noframes，strike，tt

# 2、DOCTYPE的作用是什么



(1) 作用：告诉浏览器以什么样的模式来解析文档。DOCTYPE 不存在或格式不正确会导致文档以兼容模式呈现。
(2) 一般指定了之后会以标准模式来进行文档解析，否则就以兼容模式进行解析。



# 3、语义化标签的作用



1. 
   去掉或样式丢失的时候能让页面呈现清晰的结构
2. 方便其他设备解析（如屏幕阅读器、盲人阅读器、移动设备）以意义的方式来渲染网页
3. 有利于SEO
4. 便于团队开发和维护，遵循W3C标准，可以减少差异化

# 4、事件模型以及三种事件绑定方法



现代浏览器事件模型二个过程：捕获；冒泡



**事件捕获阶段**。捕获指的是事件从html根元素向下传播到实际点击的目标元素（指在dom文档树中向下层），依次检查经过的节点是否绑定了该事件监听函数，如果有且该事件是设定在捕获阶段执行，便执行。

**冒泡阶段**：捕获阶段结束后，反向再检查一次，冒泡回根元素

现代浏览器中，默认情况下，所有事件处理程序都在冒泡阶段执行。

事件绑定方法：

- 行内事件，过时了，不建议使用,缺点行为与结构不分离

  ```html
  <button onclick="alert('ok')">Press me</button> 
  ```

- js中在元素节点上添加事件属性，缺点：重复定义后者会覆盖前者

  ```js
   document.querySelector('button').onclick=alert('ok')
  ```

- addEventListener（eventName，function，useCaptrue），常用，可取消

  ```js
  const btn = document.querySelector('button');
  
  function bgChange() {
    const rndCol = 'rgb(' + random(255) + ',' + random(255) + ',' + random(255) + ')';
    document.body.style.backgroundColor = rndCol;
  }
  
  btn.addEventListener('click', bgChange);
  btn.removeEventListener('click', bgChange);
  ```

addEventListener，其中第三个参数可以指定事件是否在捕获阶段执行。默认是false，在冒泡阶段执行。

# 5、事件代理/事件委托 以及 优缺点



事件委托本质上是利用了浏览器事件冒泡的机制。因为事件在冒泡过程中会上传到父节点，并且父节点可以通过事件对象获取到目标节点，因此可以把子节点的监听函数定义在父节点上，由父节点的监听函数统一处理多个子元素的事件，这种方式称为事件代理。

使用事件代理我们可以不必要为每一个子元素都绑定一个监听事件，这样减少了内存上的消耗。并且使用事件代理我们还可以实现事件的动态绑定，比如说新增了一个子节点，我们并不需要单独地为它添加一个监听事件，它所发生的事件会交给父元素中的监听函数来处理。



**事件委托的优点：**

1. 减少内存消耗，不必为大量元素绑定事件 
2. 为动态添加的元素绑定事件 

**事件委托的缺点:**

1. 部分事件如 focus、blur 等无冒泡机制，所以无法委托。 
2. 事件委托有对子元素的查找过程，委托层级过深，可能会有性能问题 
3. 频繁触发的事件如 mousemove、mouseout、mouseover等，不适合事件委托

**如何阻止事件冒泡**：e.stopPropagation()
**如何阻止事件默认行为**：e.preventDefault();

# 6、js 延迟加载的方式有哪些



js 延迟加载，也就是等页面加载完成之后再加载 JavaScript 文件。 js 延迟加载有助于提高页面加载速度。

一般有以下几种方式：

- defer 属性
- async 属性
- 动态创建 DOM 方式
- 使用 setTimeout 延迟方法
- 让 JS 最后加载

# 7、defer 和 async 的区别



defer 属性表示延迟执行引入的 JavaScript，即这段 JavaScript 加载时 HTML 并未停止解析，这两个过程是并行的。当整个 document 解析完毕后再执行脚本文件，在 DOMContentLoaded 事件触发之前完成。

多个脚本按顺序执行。

- async 属性表示异步执行引入的 JavaScript，与 defer 的区别在于，如果已经加载好，就会开始执行，也就是说它的执行仍然会阻塞文档的解析，只是它的加载过程不会阻塞。

  多个脚本的执行顺序无法保证。

# 8、meta标签

```html
// name : 指定数据的名称
<meta name="author" content="作者名字">
<meta name="description" content="网站描述">
<meta name="generator" content="FrontPage 4.0"> // 指定文档生成的工具名称。
<meta name="keywords" content="HTML, HTML DOM, JavaScript"> // 搜索该网站的关键字
<meta charset="UTF-8"> // 指定网页的字符集
<meta http-equiv="refresh" content="3;url=https://www.baidu.com"> // 自动跳转
```

# 9、html语义化

```
html主要是用来负责网页的结构的，我们在使用html的时候，更应该关注的是它的语义，而不是他的样式

hgroup 标签：用来为标签分组，可以将一组相关标题同时放入到 hgroup 中。

p标签 - 块儿级元素：用来表示页面的一个段落。

em标签：斜体，语义语调的加重。

strong：加粗，表示强调。

blockquote：表示一个长引用，前面会有缩进。

q：表示一个短引用，用自动用引号" "包裹起来

header：头部。

main：表示网页的主体部分（一个页面中只会有一个main）。

footer：表示网页的底部。

nav：表示网页中的导航。

aside：和主体相关的其他内容（侧边栏）。

article：表示一个独立的文章。

section：表示一个独立的区块，上面的标签都不能表示的时候使用section

iframe：内联框架，用于向页面中引入一个其他的页面。src用来指定引入网页的路径。有width和	      height。frameborder 指定内联框架的边框。
```

# 10、音频 audio

```html
<!--  
audio 标签用来向页面中引入一个外部的音频文件
	音频文件引入时，默认情况下不允许用户自己控制播放停止
属性：
	controls 是否允许用户控制播放
	autoplay 是否自动播放，但是大多数浏览器都不会自动播放
	loop 是否循环播放
-->
<audio src="" controls loop autoplay"></audio>


<!-- 
 加文字是为了防止有的浏览器不识别audio标签，但是会识别文字，如果识别标签，不会显示文字。
 指定多个source，是为了有的音频格式部分浏览器不支持，所以指定多个source
-->                                     
<audio controls>
    对不起，您的浏览器不支持播放音频！请升级浏览器！
    <source src="xxx.mp3">
    <source src="xxx.ogg">
</audio>
                         
// embed 在老版本的浏览器【IE】中支持播放音频，会自动播放，而且所有浏览器都可以用，但是必须指定width和height，不然不会展示出来播放器。type指定文件格式：前面大类型，后面小类型
<embed src="" type="audio/mp3" width="" height=""></audio>
                                                 

// 从上往下进行遍历，匹配到一个下面就不进行了。
<audio controls>
    对不起，您的浏览器不支持播放音频！请升级浏览器！
    <source src="xxx.mp3">
    <source src="xxx.ogg">
    <embed src="" type="audio/mp3" width="" height=""></audio>
</audio>
```

# 11、视频 video

```html
<video controls>
    <source src="xxx.webm">
    <source src="xxx.mp4">
    <embed src="xxx.mp4" type="video/mp4">
</video>
```

# 12、



# 13、

# 14、



