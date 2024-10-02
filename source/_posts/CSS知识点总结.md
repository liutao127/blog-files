---
title: CSS知识点总结
date: 2022-04-03 14:01:26
tags: CSS
---

# 1、CSS盒模型

<!-- more -->

## 盒模型总共包括4个部分：

1. content：内容，容纳着元素的”真实“内容，例如文本，图像或是视频播放器
2. padding：内边距
3. border：边框
4. margin：外边距



## 两种盒模型的区别：

- **W3C盒模型 `box-sizing: content-box` (常用的是这种)**

  W3C盒模型中，通过CSS样式设置的width的大小只是content的大小

- **IE盒模型 `box-sizing: border-box`**

  IE盒模型中，通过CSS样式设置的width的大小是content + padding + border的和

# 2、设置一个元素的背景颜色，背景颜色会填充哪些区域

border + padding + content

# 3、margin/padding 设置百分比是相对谁的



margin/padding设置百分比**都是**相对于**父盒子的宽度**(width属性)来计算量的

# 4、CSS 选择器优先级



选择器按优先级先后排列：!important > 内联 > id > class = 属性 = 伪类 > 标签 = 伪元素 > 通配符 \*

important 声明 1,0,0,0 

ID 选择器 0,1,0,0 

类选择器 0,0,1,0 

伪类选择器 0,0,1,0 

属性选择器 0,0,1,0 

标签选择器 0,0,0,1 

伪元素选择器 0,0,0,1 

通配符选择器 0,0,0,0

# 5、伪类与伪元素的区别



一般在css3中使用单冒号来表示伪类，用双冒号来表示伪元素。

伪类一般匹配的是元素的一些特殊状态，如hover、link ，active等，而伪元素一般匹配的特殊的位置，比如after、before等。 伪元素默认行内元素，css中其必须有content属性，就算是content：""也必须写，否则无效。伪元素可以用于添加文字，或者小图标。
box::before {
 content:'';
}

```
a标签中有四个：link、visited、hover、active
（1）link-设置a对象在未被访问前的样式表属性。
（2）visited-设置a对象在其链接地址已被访问过时的样式表属性。
（3）hover–设置对象在其鼠标悬停时的样式表属性。
（4）active-设置对象在被用户激活（在鼠标点击与释放之间发生的事件）时的样式表属性。
```



# 6、CSS 中哪些属性可以继承



每一个属性在定义中都给出了这个属性是否具有继承性，一个具有继承性的属性会在没有指定值的时候，会使用父元素的同属性的值来作为自己的值。

一般具有继承性的属性有，字体相关的属性，font-size和font-weight等。文本相关的属性，color和text-align等。
表格的一些布局属性、列表属性如list-style等。还有光标属性cursor、元素可见性visibility。

当一个属性不是继承属性的时候，通过将它的值设置为inherit来使它从父元素那获取同名的属性值来继承。

# 7、什么是BFC？BFC有什么特性？如何创建BFC？BFC有什么作用？

## 什么是BFC?

BFC全称是Block Formatting Context，意思就是块级格式化上下文。你可以把BFC看做一个容器，容器里边的元素不会影响到容器外部的元素。简单来说就是一个封闭的黑盒子，里面元素的布局不会影响外部。

## BFC有什么特性？

1. BFC是一个独立的容器，内部元素不会影响容器外部的元素。

2. BFC是一个块级元素，块级元素在垂直方向上依次排列。

3. BFC 的区域不会与 float 的标签区域重叠。

4. 每个标签的左外边距与包含块的左边界相接触（从左向右），即使浮动标签也是如此。

5. 属于同一个BFC的两个盒子，外边距margin会发生重叠，并且取最大外边距。

6. 计算BFC高度时, 浮动元素也要计算

7. ```css
   <style>
           body {
               margin: 0;
               padding: 0;
           }
           .continer{
               margin-top: 100px;
               width: 200px;
               height: 400px;
               background-color: green;
               overflow: hidden;
           }
           .continer .box1 {
               margin-top: 20px;
               width: 100px;
               height: 100px;
               margin-bottom: 20px;
               background-color: red;
           }
           .continer .box3 {
             width: 50px;
             height: 50px;
             background-color: pink;
             margin-top: 30px;
           }
           .continer .box2 {
               width: 100px;
               height: 100px;
               margin-top: 40px;
               background-color: red;
           }
   </style>
   <body>
     box1和box3 相对于 container 的margin-top 是30px(因为 box1 和 box3 的高度发生重叠, box3是30px > box1是20px)
     box1和box2 之间的 距离是40px, 因为box1的margin-bottom(20px) 和 box2的margin-top(40px) 发生重叠, 取最大的值40px 
       <div class="continer">
           <div class="box1">
             <div class="box3"></div>
           </div>
           <div class="box2">box2</div>
       </div>
   </body>
   ```

## 如何创建BFC?

**给父级元素添加以下任意样式**

```css
overflow: hidden;
display: flex;
display: inline-flex;
display: inline-block;
position: absolute;
position: fixed;
```

## BFC的作用

1. 解决当父级元素没有高度时，子级元素浮动会使父级元素高度塌陷的问题
2. 解决子级元素外边距会使父级元素塌陷的问题

# 8、left: 0;right: 0;top: 0;bottom: 0;的作用

```
其的两种作用：
    让明确宽高的盒子水平垂直居中
    让无宽高的盒子填充父容器
```

```css
<style>
        body {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body, html {
            height: 100%;
        }
        .parent {
            width: 400px;
            height: 400px;
            position: relative;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: #6495ed;
        }
        /*
            作用一:
            通过postion: absolute; top: 0; bottom:0; left:0; right:0;
            让明确宽高的盒子水平垂直居中
        */
        .child {
            width: 200px;
            height: 200px;
            position: absolute;
            top: 0;
            right: 0;
            bottom: 0;
            left: 0;
            margin: auto;
            background-color: #b0c4de;
        }
        /*
            作用二：
            让无宽高的盒子填充父容器
        */
        .fill {
            position: absolute;
            top: 0;
            right: 0;
            bottom: 0;
            left: 0;
            background:linear-gradient(-45deg, #fff 0%, #fff 25%, transparent 25%, transparent 50%, 
                                        #fff 50%, #fff 75%, transparent 75%, transparent 75%);
            background-size: 10px 10px;
        }
</style>


<body>
    <div class="parent">
        <div class="child">
            <div class="fill"></div>
        </div>
    </div>
</body>
```



# 9、margin塌陷问题

高度塌陷简单说就是，父级元素包涵的子元素浮动了，当父级元素没有设置高度时，会因为没有子元素“撑开”，而变成一条线。

解决方法：触发bfc，绝对定位，float，overflow：hidden，display：inline-block

## 消除浮动 （四种）

### 1. 父级元素定义高度（不推荐） 

```
 这个方法比较简单，直接给父级元素的 height 设置一定的值就行。不过这样就写死了，不过当整体布局确定时可以使用，比较方便 。
```

### 2.结尾添加空标签，给其设置 clear：both（不推荐）

  直接在高度塌陷的父元素的最后添加一个空的div，由于这个div没有浮动，所以它可以撑开父元素的高度，然后对其进行[清除浮动](https://so.csdn.net/so/search?q=清除浮动&spm=1001.2101.3001.7020)，通过这个空白div来撑起父元素的高度，clear：both 本质就是闭合浮动， 就是让父盒子闭合出口和入口，不让子盒子出来。缺点是添加无意义标签，语义化差。

```css
<body>
  <div class="one clearfix">
    <div class="two"></div>
    <div class="add"></div>
  </div>
</body>
 
<style>
  .one {
    border: 10px solid rebeccapurple;
  }
 
  .two {
    width: 100px;
    height: 100px;
    background: yellowgreen;
    float: left;
  }
 
  .add {
    clear: both;
  }
```

### 3.用伪元素 ::after 清除浮动（推荐）

  可以通过给父级元素添加after伪类，再向元素添加一个空白的块元素，然后对其清除浮动，这样做和添加一个div的原理一样，可以达到一个相同的效果，而且不会在页面中添加多余的div，这是我们最推荐使用的方式，几乎没有副作用 

```css
.clearfix::after{
    content: "";
    display: block;
    clear: both;
  }
 
.one {
    border: 10px solid red;
  }
 
.two {
    width: 100px;
    height: 100px;
    background: yellowgreen;
    float: left;
  }
```

### 4.使用before和after双伪元素清除浮动（推荐）

  在IE6中虽然没有BFC，但是具有另一个隐含的属性叫做hasLayout， 该属性的作用和BFC类似，所在IE6浏览器可以通过开hasLayout来解决该问题。
  开启方式很多，我们直接使用一种副作用最小的：直接将设置元素的 zoom：1  

```css
.clearfix::after,
  .clearfix::before {
    content: "";
    display: table;
  }
 
  .clearfix::after {
    clear: both;
  }
 
  .clearfix {
    *zoom: 1;
  }
```

# 10、图片底部与父容器之间有一段空隙

原因：行内块级元素会和文字的基线对齐。

解决方法：
（1）给图片添加 vertical-align middle | top | bottom
（2） font-size：0

# 11、响应式设计

- 媒体查询：@media screen and (min-width:800px){}

- 多列布局：column-count：3；flex布局；网格布局

- 响应式图像使用了picture元素的srcset和sizes 特性提供多种资源

- 使用百分号单位，或视口单位，或者vw加上绝对单位

- <meta name="viewport" content="width=device-width,initial-scale=1">

# 12、常用单位px 像素 % 百分比 rem 和em vw和vh



- em

  子元素字体大小的 em 是相对于父元素字体大小

  元素的width/height/padding/margin用em的话是相对于该元素的font-size

- rem

  rem 是全部的长度都相对于根元素，根元素是谁？<html>元素。

  通常做法是给html元素设置一个字体大小，然后其他元素的长度单位就为rem。

vw（Viewport Width）、vh(Viewport Height)是基于视图窗口的单位，是css3的一部分，基于视图窗口的单位，除了vw、vh还有vmin、vmax。

- vw:1vw 等于视口宽度的1% 
- vh:1vh 等于视口高度的1% 
- vmin: 选取 vw 和 vh 中最小的那个,即在手机竖屏时，1vmin=1vw 
- vmax:选取 vw 和 vh 中最大的那个 ,即在手机竖屏时，1vmax=1vh



# 13、inline-block、inline 和 block 的区别；可替换元素



Block 是块级元素，独占一行，能设置宽度，高度，margin/padding 水平垂直方向都有效。

Inline：设置 width 和 height 无效，margin 在竖直方向上无效，padding 在水平方向垂直方向都有效，前后无换行符

Inline-block：能设置宽度高度，margin/padding 水平垂直方向 都有效，前后无换行符

img 是可替换元素。

在 CSS 中，可替换元素（replaced element）的展现效果不是由 CSS 来控制的。一种外部对象，它们外观的渲染，是独立于 CSS 的。

例如 `` 元素，可能具有自己的样式表，但它们不会继承父文档的样式。

典型的可替换元素有： iframe> video> embed> img>

# 14、图片缝隙以及字体多余部分显示问题

```html
<style>
    .box{
      border: 1px solid red;

    }

    img{
      /* 让图片 和 边框 之间的缝隙消失，这不是border 、padding、margin，是基线问题 */
      vertical-align: top;
    }

    a{
      /* 取消下划线  none：啥都没   underline：下划线    line-through：删除线   overline：上划线 */
      text-decoration: underline red dotted;
    }

    .box2{
      /* white-space 设置网页如何处理空白    normal：正常    nowrap：不换行   pre：保留空白*/
      white-space: nowrap;

      /* 先把他显示成一行，然后设置长度，然后将超出的长度隐藏掉 */
      width: 200px;
      overflow: hidden;
      /* 溢出内容显示省略号 */
      text-overflow: ellipsis;
    }
  </style>


<body>
  
  <div class="box">
    <img src="./2/2.jpg" alt="">
  </div>

  <a href="">123123</a>

  <div class="box2">
    腾讯音乐娱乐集团2022暑期实习生招聘，在简历信息中填写内推码的同学，和通过腾讯音乐娱乐招聘官网投递简历的同学在流程上没有任何区别。 TME内部员工通过后台查询到的进度信息与各位同学从招聘官网个人中心查询到的进度信息一致。 内推码的目的在于从TMEr处获取更多关于意向岗...查看全文
面试流程春招实习内推腾讯音乐娱乐
  </div>
</body>
```

# 15、clearfix 解决高度塌陷

```html
<style>

    .box1{
        width: 200px;
        height: 200px;
        background-color: green;
    }

    /* .box1::before{
    content: '';
    display: table;
    } */

    .box2{
        width: 100px;
        height: 100px;
        background-color: yellow;
        margin-top: 100px;
    }

    /* clearfix 这个样式可以同时解决高度塌陷 和 外边距重叠问题，直接使用 clearfix 即可解决 */
    .clearfix::before,
    .clearfix::after{
        content: '';
        display: table;
        clear: both;
    }

</style>


<div class="box1 clearfix">
    <div class="box2"></div>
</div>
```

# 16、渐变

通过渐变可以设置一些复杂的背景颜色，可以实现从一个颜色向其他颜色过渡的效果!!

渐变是图片，需要通过background-image来设置

## 1、linear-gradient

线性渐变，颜色沿着一条直线发生变化

```css
.box1{
    width: 200px;
    height: 200px;
    background-image: linear-gradient(.25turn, red, yellow, #bfa, orange);
}

线性渐变，颜色沿着一条直线发生变化
background-image: linear-gradient(red, yellow);  // 从上往下，上红下黄
background-image: linear-gradient(to right, red, yellow);  // 从左往右
to left
to right
to bottom
to top
to top left // 向左上
xxxdeg   // deg是度数,如果是0deg那么就是 从下往上开始算，顺时针旋转。
.25turn  // 转多少圈
background-image: linear-gradient(.25turn, red, yellow, #bfa); // 可以写多种颜色
渐变可以指定多种颜色，多个颜色默认情况下平均分布
// 颜色后面带 xxpx 表示从啥地方开始，这种颜色最深
background-image: linear-gradient(red 0px, yellow 200px); // 默认情况下是这种情况,表示从0px的时候开始红色，0px处红色最深，200px处，黄色最深
```

## 2、repeating-linear-gradient

可以平铺的线性渐变

```css
.box1{
    width: 200px;
    height: 200px;
    background-image: repeating-linear-gradient(red 0px, yellow 100px);
    background-repeat: no-repeat; // 这个属性如果在设置了 渐变效果 之后，就不会生效了，就没用了。
}
background-image: repeating-linear-gradient(red 0px, yellow 100px); // 从0开始是red，从100px开始是yellow。所以（100 - 0） = 100px 为一份， 200px 可以分为两份， 从中间分开。

```

## 3、radial-gradient

径向渐变( 就像是一个太阳，向外扩散 )，默认情况下会根据元素的形状来计算的

正方形  --- 圆形

长方形  --- 椭圆形

我们也可以手动指定径向渐变的大小

```css
.box1{
    width: 300px;
    height: 300px;

    background-image: radial-gradient(red, yellow);
}

background-image: radial-gradient(red, yellow);  // 里面红色，外面黄色，从内向外
background-image: radial-gradient(circle, red, yellow); // 圆形
background-image: radial-gradient(ellipse, red, yellow); // 椭圆
background-image: radial-gradient(100px 100px, red, #bfc); // 前面的两个指定渐变区域的大小，区域外面是后面的颜色。
background-image: radial-gradient(100px 100px at 0 0, red, #bfc); // 指定渐变区域的圆心
background-image: radial-gradient(100px 100px at center center, red, #bfc); // 指定渐变区域的圆心
background-image: radial-gradient(at 0 0, red, #bfc);  // 从左上角开始

// 大小： side边、corner角，closest-side 靠最近的边，closest 靠最近的角，farthest 靠远的
background-image: radial-gradient(closest-side at 100px 100px, red, #bfc); 
background-image: radial-gradient(closest-corner at 100px 100px, red, #bfc); 
background-image: radial-gradient(farthest-side at 100px 100px, red, #bfc); 
background-image: radial-gradient(farthest-corner at 100px 100px, red, #bfc);

// radial-gradient(大小 at 位置, 颜色 开始位置, 颜色 开始位置)
大小： 
	circle 圆形
	ellipse 椭圆
	closest-side 近边
	closest-corner 近角
	farthest-side 远边
	farthest-corner 远角

```

# 17、transition  过渡

用于为样式设置过度效果

```css
/* transition 用于为样式设置过度效果，当样式发生变化的时候才有效 */
transition: height 3s; // 高度三秒展示
transition: all 3s; // 全部样式 3s 后展示完全

/*
	transition-property：要执行过度的属性
	多个属性之间用 ， 隔开
	大多数元素都支持过渡效果，但是必须是由 一个有效数值 像 另一个有效数值 进行过渡
*/
transition-property: width; 

/*
	transition-duration：要执行过渡的时间
	单位： s  ms
	transition-property: width, height; 
	transition-duration: 2s, 1s;  
	宽度 2s， 高度 1s
*/
transition-duration: 2s;

/*
	transition-timing-function: 过渡的时序函数 - 指定过渡的执行的方式
	ease: 默认值，慢速开始，先加速，在减速
	linear：匀速运动
	ease-in：加速运动
	ease-out：减速运动
	ease-in-out：先加速后减速
	cubic-bezier()：贝塞尔曲线执行:https://cubic-bezier.com cubic-bezier(.25,1.5,.66,-0.94)
	steps()：分步执行过渡效果
		-可以设置两个值， end（在时间结束时执行过渡【默认值】） 和 start（在时间开始时执行过渡）
*/
transition-timing-function: ease

/*
	延时执行
*/
transition-delay: 2s;

/*
	可以设置相关过渡的所有属性，只有一个要求，如果要写延迟，第一个时间是持续时间，第二个是延迟时间
*/
transition
```



# 18、animation 动画

动画和过渡类似，都可以实现一些动态的效果

不同的是，过渡需要某个属性发生改变时，才会触发，动画刻意自动触发动态效果

设置动画效果，必须要设置一个关键帧，关键帧设置了动画执行每一个步骤

```css
@keyframes test {
    /* 表示动画开始的位置，也可以使用 0% */
    from{
        margin-left: 0;
    }

    /* 表示动画结束的位置， 也可以使用100% */
    to{
        margin-left: 700px;
    }
}


.box2{
    background-color: #bfa;
    /* 设置box2 动画 */
    /* 动画的名字 */
    animation-name: test; 
    /* 动画执行时间 */
    animation-duration: 4s;
    /* 动画延迟 */
    animation-delay: 2s;
    /* 动画执行的方式  in减速，out加速 */
    animation-timing-function: ease-in-out;
    
	/* 
        动画执行的次数 
          可选值：
            次数（1、2、3、4）
            infinite 无限执行
      */
    animation-iteration-count: infinite;
    
      /* 
        动画执行的方向 
          默认值 normal  从 from 向  to 运行，每次都这样
          reverse 从 to  向 from 运行，每次都这样
          alternate 从 from  向 to 运行 重复执行动画时反向执行
          alternate-reverse  从 to  向 from 运行 重复执行动画时反向执行
      */
    animation-direction: alternate;
    
      /* 
        设置动画执行状态
        可选值：
          running：默认值 动画执行
          paused： 动画暂停
      */
    animation-play-state: paused;
    
      /* 
        动画填充模式
          可选值：
            none: 默认值 动画执行完毕之后回到初始位置
            forwards: 动画执行完毕，元素停止在结束的位置
            backwards: 动画延迟等待时，元素会出于动画开始的位置
            both: 结合了  forwards  和  backwards 两者
      */
    animation-fill-mode: both;
    
    animation: test 2s infinite 1s alternate both;
}




/*关键帧*/
@keyframes ball{
    0%{
        margin-top: 0;
    }
    20%, 60%, 100%{
        margin-top: 400px;
        animation-timing-function: ease-in;
    }
    40%{
        margin-top: 100px;
        animation-timing-function: ease-out;
    }
    80%{
        margin-top: 200px;
        animation-timing-function: ease-out;
    }

}
```

# 19、transform:translateX移动

```html
<body>
  <div class="box1"></div>
</body>

<style>

    html{
        /* 设置网页的视距为 800px，人眼距离网页的距离 */
        perspective: 800px;
    }
    body{
        background-color: silver;
        border: 1px solid red;
    }
    .box1{
        width: 200px;
        height: 200px;
        background-color: #bfa;
        margin: 200px auto;
        /* 
        Z轴平移，调整元素在z轴的位置，正常情况就是调整元素和人眼之间的距离
        元素越大，离人越近
        Z轴平移属于立体效果（进大远小），默认情况下网页是不支持透视的，
        如果需要看见效果，那么就要必须设置网页的 视距
        */
        /* transform: translateZ(100px); */
        transition: 2s;
    }
    body:hover .box1{
        transform: translateZ(100px);
    }
</style>
```

# 20、transform:rotateX旋转

旋转的时候，X,Y,Z轴是相对于自身的，那么就是说，当旋转之后，X,Y,Z轴的方向也可能会改变。

```html
<div class="box1">
    <img src="./aoteman.png" alt="">
</div>

<style>

    html{
        /* 设置网页的视距为 800px，人眼距离网页的距离 */
        perspective: 800px;
    }
    body{
        background-color: #fff;
        border: 1px solid red;
    }
    .box1{
        width: 200px;
        height: 200px;
        background-color: #bfa;
        margin: 200px auto;

        transition: 1s;
    }
    body:hover .box1{
        /* 
        通过旋转可以使元素沿着 x y 或 z 旋转指定的角度
        45deg   1turn  
        rotateX(); 
        rotateY();
        rotateZ();
        */
        /* transform: rotateY(180deg) ; */
        transform: rotateY(180deg) translateZ(100px);
        /* 是否显示元素的背面 */
        backface-visibility: hidden;
    }
</style>
```

# 21、transform:scaleX缩放

```css
<div class="box1"></div>

<div class="img-wrapper">
	<img src="./aoteman.png" width="100%" height="100%" alt="">
</div>

<style>
    .box1{
      width: 100px;
      height: 100px;
      background-color: #bfa;
      transition: 2s;
      margin: 100px auto;

      /* 变形的原点  默认值是 center*/
      transform-origin: 0px 0px;
    }
    /* 
      对元素进行缩放的函数
      scaleX(2) ： X轴放大 2 倍
      scaleY(2) ： Y轴放大 2 倍
      scaleZ(2) ： Z轴放大 2 倍
      scale() : X Y 轴 双方向缩放
    */
    .box1:hover{
      transform: scale(2);
    }

    .img-wrapper{
      width: 200px;
      height: 200px;
      border: 1px red solid;
      overflow: hidden;
    }

    img{
      transition: 2s;
    }

    .img-wrapper:hover img{
      transform: scale(1.2);
    }
  </style>
```

# 22、less

```less
// import 用来引入其他的less
@import "./style.less";

body{
  width: 100px;
  height: 100px;
  background-color: #ff0;
  div{
    color: blue;
  }
}

@a:200px;
@b:#bfa;
.box1{
  width: @a;
  height: @a;
  color: @b;
}

// 这个注释不会继承到 .css文件中

/* 这个注释会被继承到 .css文件中
  变量作为类名使用，必须使用  .@{x} 来用
  变量作为文件名使用时，必须要用  "" 引号来包起来
  .box6 {
    width: 200px;
  }
*/
@c:box6;
.@{c}{
  width: @a;
  background-image: url("@{c}/1.png");
}


@d: 200px;
@d: 300px;
div{
  // 变量发生重名时，会优先使用比较近的变量
  @d: 115px;
  width: @d;
  height: @e;
}
// 可以在变量声明前就使用变量
@e: 335px;

div{
  width: 400px;
  // 如果 height 想要引用 width 的值， 可以用  $width 这样
  height: $width + 200px;
}


.box1{
  .box2{
    color:red
  }
  // 后代选择器的用法
  >.box3{
    color: red;
  }

  // 为 .box1 自身添加时要用 &
  &:hover{
    // .box1:hover {
    //   color: orange;
    // }
    color: orange;
  }

  div &{
    // div .box1 {
    //   color: yellow;
    // }
    color: yellow;
  }
}


.p1{
  width: 100px;
  height: 200px;
}

// extend() 对当前选择器扩展指定选择器的演示（选择器分组）
.p2:extend(.p1){
  // .p2{
  //   width: 200px;
  //   height: 200px;
  //   background-color: #bfa;
  // }
  background-color: #bfa;
}

.p3{
  // 直接对指定样式进行引用，这里就相当于将 p1 的样式在这里进行了 复制
  // mixin 混合
  // .p3 {
  //   width: 100px;
  //   height: 200px;
  // }
  .p1();
}

// 使用类选择器时，可以在后面加一个括号，这是我们实际上就创建了一个 mixins
// .p4不会出现在 css 中
.p4(){
  width: 100px;
  height: 100px;
  background-color: #bfa;
}
.p5{
  .p4;
}


// 混合函数  在混合函数中可以设置 变量
.test(@w, @h, @bg-color:#bfa){
  width: @w;
  height: @h;
  background-color: @bg-color;
}
div{
  // .test(200px, 300px);
  .test(@h:300px, @w:100px);
}


span{
  // 取 red 和 yellow 之间的颜色
  color: average(red, yellow);
}
body{
  width: 100%;
  height: 100%;
  background-color: #bfa;
}
body:hover{
  // 加深当前颜色的 10%
  background-color: darken(#bfa, 10%);
}
```

# 23、flex弹性布局

flex（弹性盒、伸缩盒）是css中的又一种布局手段，它主要用来代替浮动来完成页面的布局。

flex可以使元素具有弹性，让元素可以跟随页面的大小的改变而改变。

弹性容器的子元素是弹性元素（弹性项）

## 弹性容器的属性

## 主轴属性

**弹性元素可以同时是弹性容器**

```css
/* 设置弹性容器 */
display:flex 设置为块级弹性容器 - 独占一行
display:inline-flex 设置为行内的弹性容器 - 行内元素
```



### **主轴与侧轴**

- 主轴：弹性元素的排列方向称为主轴
- 侧轴：与主轴垂直方向的称为侧轴

### `flex-direction` 指定容器中弹性**元素的排列方式**

- `row`默认值，弹性元素在容器中水平排列（自左向右）
- `row-reverse` 弹性元素在容器中反向水平排列（自右向左）
- `column` 弹性元素纵向排列（自上向下）
- `column-reverse` 弹性元素反向纵向排列（自下向上）

```css
/* 设置弹性元素排列方式 */
flex-direction: column;
```

### **自动换行**

`flex-wrap` 设置弹性元素是否在弹性容器中自动换行

- `nowrap` 默认值，元素不会自动换行
- `wrap` 元素沿着辅轴方向自动换行
- wrap-reverse 元素沿着主轴反方向换行

```css
/* 设置弹性元素排列方式 */
flex-direction: row;
/* 设置自动换行 */
flex-wrap: wrap;
```

**简写属性**

`flex-flow` 是`wrap`和`direction`的简写属性

```css
/* 简写属性 */
flex-flow: row wrap;
```

### 空白空间

`justify-content` 如何分配主轴上的空白空间（主轴上的元素如何排列）

- `flex-start` 元素沿着主轴起边排列

- `flex-end` 元素沿着主轴终边排列

- `center` 元素居中排列
- `space-around` 空白分布到元素两侧
- `space-between` 空白均匀分布到元素间
- `space-evenly` 空白分布到元素的单侧

## 辅轴属性

### `align-items`元素在辅轴上如何对齐

- `stretch` 默认值，将元素的高度设置为相同的值，【每行的每个元素之间高度是一样的，但是和后面的行内的元素高度不一定相等】
- `flex-start` 元素不会拉伸，沿着**辅轴起边**对齐【如果说主轴是X轴，即元素水平排列，那么辅轴就是Y轴，此时辅轴的起边，就是Y轴最上面的那条边，即与Y轴垂直，但与X轴平行的，最上方那条边】
- `flex-end` 沿着辅轴的终边对齐
- `center` 居中对齐
- `baseline` 基线对齐

### 空白空间

`align-content` 如何分配辅轴上的空白空间（辅轴上的元素如何排列）

- `flex-start` 元素沿着辅轴起边排列
- `flex-end` 元素沿着辅轴终边排列
- `center` 元素居中排列
- `space-around` 空白分布到元素两侧
- `space-between` 空白均匀分布到元素间
- `space-evenly` 空白分布到元素的单侧

## 弹性居中

```css
justify-content: center;
align-items: center;
```



## 弹性元素的属性

### 伸展系数

`flex-grow` 指定弹性元素的伸展系数，默认值为 0

- 当父元素有多余空间的时，子元素如何伸展
- 父元素的剩余空间，会按照比例进行分配

```css
li:nth-child(1) {
  background-color: #bfa;
  flex-grow: 1;
}

li:nth-child(2) {
  background-color: red;
  flex-grow: 2;
}

li:nth-child(3) {
  background-color: green;
  flex-grow: 3;
}

```

### 缩减系数

`flex-shrink` 指定弹性元素的收缩系数，默认值为 1

- 当父元素中的空间不足以容纳所有的子元素时，如何对子元素进行收缩
- 缩减系数的计算方式比较复杂，缩减多少是根据 *缩减系数* 和 *元素大小* 来计算

```css
li:nth-child(1) {
  background-color: #bfa;
  flex-shrink: 1;
}

li:nth-child(2) {
  background-color: red;
  flex-shrink: 2;
}

li:nth-child(3) {
  background-color: green;
  flex-shrink: 3;
}

```

### 覆盖辅轴

align-self 覆盖 aligin-items

```css
li:nth-child(1) {
  background-color: #bfa;
  align-self: flex-end;
}
```

### 基础长度

`flex-basis` 指定的是元素在主轴上的基础长度

- 如果主轴是横向的，则该值指定的就是元素的宽度
- 如果主轴是纵向的，则该值指定的就是元素的高度
- 默认值是`auto`，表示参考元素自身的高度或宽度
- 如果传递了一个具体的数值，则以该值为准

```css
li:nth-child(1) {
  background-color: #bfa;
  flex-basis: 200px;
}
```

### 简写属性

flex`可以设置弹性元素所有的三个样式 `flex: 增长 缩减 基础

- `initial`：`flex: 0 1 auto`
- `auto`：`flex: 1 1 auto`
- `none`：`flex: 0 0 auto` 弹性元素没有弹性

### 排列顺序

`order` 决定弹性元素的排列顺序

```css
li:nth-child(1) {
  background-color: #bfa;
  order: 2;
}

li:nth-child(2) {
  background-color: red;
  order: 3;
}

li:nth-child(3) {
  background-color: green;
  order: 1;
}

```



# 24、响应式布局

## 简介

响应式设计网页：
      a：移动端优先
      b: 渐进增强

```html
<style>
    /* 
      使用媒体查询
        语法： @media 查询规则{}
          媒体类型： 
            all 所有设备
            print 打印设备
            screen 带屏幕的设备
            speech 屏幕阅读器
        可以在媒体类型前使用only来修饰，表示 只有xxx。
          	only主要是为了兼容一些老版本浏览器
    	还可以用 not 来修饰，表示 除了
    */
    @media only print, screen{
      body{
        background-color: #bfa;
      }
    }
</style>

<!--
  响应式布局
    -网页可以根据不通的设备或窗口大小呈现出不同的效果-使用响应式布局，可以使一个网页适用于所有设备
    -响应布局的关键就是媒体查询
    -通过媒体查询，可以为不通的设备，或设备不同状态来分别设置样式
-->
```

## 媒体查询

```css
<style>

    /* 
      媒体特性：
        width: 视口的宽度
        height: 视口的高度

        min-width: 视口的最小宽度
        max-width：视口的最大宽度

      样式切换的分界点，我们称其为断点，也就是网页的样式会在这个点时发生变化
      一般常用的断点

      小于 768 超小屏幕 max-width=768px
      大于768 小屏幕 min-width=768px
      大于992 中型屏幕 min-width=992px
      大于1200 大屏幕 min-width=1200px

      @media (min-width: 500px) , (max-width: 300px)  -->  大于等于 500 或 小于等于 300
      @media (min-width: 500px) and (max-width: 700px)  -->  500 - 700 之间
    */
    @media (min-width: 500px) and (max-width: 700px){
      body{
        background-color: #bfa;
      }
    }
  </style>
```



## 完整写法

```css
/* 最完整的写法 */	
@media only screen and (min-width: 500px) and (max-width: 700px){
    body{
        background-color: #bfa;
    }
}
```



# 25、垂直居中布局



## **内联元素居中布局方式**

**①水平居中**

- 行内元素可设置text-align：center
- flex布局设置父元素：display:flex;justify-content:center

**②垂直居中**

- 单行文本父元素确定高度：height == line-height
- 多行文本父元素确定高度：display:table-cell;vertical-align:middle



## **块级元素居中布局方式**

**①水平居中**

定宽：margin:0 auto

flex布局设置父元素：display:flex;justify-content:center

绝对定位+负值margin

绝对定位+transform:translateX(-50%)

table-cell布局设置父元素：display:table-cell;text-align:center

**②垂直居中**

flex布局设置父元素：display:flex;align:center

display布局设置父元素：display:tabel-cell;vertical-align:middle

position：absolute+负值margin

position:absolute+transform:translateY(-50%)

## HTML

```html
<div  class="wrap">
    <div  class="box"></div>
</div>
```

## CSS

## 方法1（常用）：display：flex

```CSS
.wrap{
	width:300px;
	height:300px;
    border: 1px solid red;
    display:flex;
    justify-content:center;
    align-items:center;
}
.box{
	height:100px;
	width:100px
    boder:1px solid blue;
}

```

## 方法2： table-cell

```CSS
vertical-align 属性设置一个元素的垂直对齐方式。
该属性定义行内元素的基线相对于该元素所在行的基线的垂直对齐。允许指定负长度值和百分比值。这会使元素降低而不是升高。在表单元格中，这个属性会设置单元格框中的单元格内容的对齐方式。

.wrap{
    width: 300px;
    height: 300px;
    border: 1px solid red;
    display: table-cell;
    text-align: center;
    vertical-align: middle;
}
.box{
    width: 100px;
    height: 100px;
    border: 1px solid blue;
    display: inline-block;
}
```

## 方法3: margin，transform配合

```CSS
.wrap{
    width: 300px;
    height: 300px;
    background-color: pink;
    /* border: 1px solid red; */
    /*防止外边距塌陷。解决外边距塌陷的方法：
    父元素加overflow:hidden或加上边框*/
    overflow: hidden;
}
.box{
    width: 100px;
    height: 100px;
    background-color: plum;
    margin:50% auto;
    transform: translateY(-50%); 
}
```

## 方法4: inline-block+vertical-aligin

```CSS
.wrap {
    width: 300px;
    height: 300px;
    background-color: pink;
    text-align: center;
    line-height: 300px;
}
.box {
    width: 100px;
    height: 100px;
    /* 重新设置子元素的行高，否则会继承父元素的行高*/
    line-height: 100px;
    background-color: plum;
    display: inline-block;
    /* middle   把此元素放置在父元素的中部。 */
    vertical-align: middle;
}
```

## 方法5：absolute+负margin

```CSS
.wrap{
    width: 300px;
    height: 300px;
    position: relative;
    background-color: plum;
}
.box{
    width: 100px;
    height: 100px;
    position: absolute;
    left: 50%;
    top: 50%;
    /*宽高的一半*/
    margin-left: -50px;
    margin-top: -50px;
    background-color: powderblue;
}

```

## 方法6 absolute+margin:auto

和方法5类似，当宽度和高度未知时使用

```CSS
.wrap{
    width: 300px;
    height: 300px;
    position: relative;
    background-color: plum;
}
.box{
    width: 100px;
    height: 100px;
    position: absolute;
    left: 0;
    top: 0;
    bottom:0;
    right:0;
    margin:auto;
    background-color: powderblue;
}

```

## 方法7：absolute+transform

与方法5类似

```CSS
.wrap {
    width: 300px;
    height: 300px;
    position: relative;
    background-color: plum;
}

.box {
    width: 100px;
    height: 100px;
    position: absolute;
    left: 50%;
    top: 50%;

    transform: translate(-50%,-50%);
    background-color: powderblue;
}

```

## 方法8 强大的grid

```CSS
.wrap {
    width: 300px;
    height: 300px;
    display: grid;
    background-color: plum;
}

.box {
    width: 100px;
    height: 100px;
    align-self: center;
    justify-self: center;
    background-color: powderblue;
}

```

# 26、CSS选择器

**作用：选择页面上的某一个或者某一类元素**

## 层次选择器

后代选择器 ： 在某个元素的后面   祖爷爷  爷爷  爸爸 你

```css
/*后代选择器*/
body p{
    color: red;
    background-color: blue;
}
```

子选择器 只选择一代 > 儿子

```css
/*子选择器*/
body>p{
    background-color: #3cbda6;
}
```

相邻兄弟选择器  只有一个，相邻（向下）

```css
/*相邻兄弟选择器*/
.active + p {
    background-color: #3cbda6;
}
```

通用选择器  当前选中元素，所有的向下的元素

```css
/*通用选择器*/
.active~p {
    background-color: red;
}
```

## 结构伪类选择器

```css
/*ul的第一个子元素*/
ul li:first-child {
    background-color: red;
}

/*ul的最后一个子元素*/
ul li:last-child {
    background-color: green;
}

/*选中p1: 定位到父元素，选择当前的第一个元素
    算中当前p元素的父级元素，选中父级元素的第一个，并且是当前元素才会生效
*/
p:nth-child(2) {
    background-color: blue;
}

/*选中父元素，下的p元素的第二个*/
p:nth-of-type(2){
    background-color: yellow;
}
```

## 属性选择器（常用）

```css
将id 和 class 结合
选中id = first的元素
属性名： 属性名 = 属性值(正则)
存在id属性的元素 a[]{}

1.  = 表示的是绝对等于
    *= 表示包含等于
2. 以...开头或者结尾
    ^=
    $=

/*选中href中以http开头的元素*/
a[href^=http]{
    background-color: yellow;
}

/*选中href中以doc结尾的元素*/
a[href$=doc]{
    background-color: black;
}

/*选中id=first的元素*/
a[id = first]{
    background-color: green;
}

/*选中class中包含item的元素*/
a[class *= "item"]{
    background-color: pink;
}

/*选中class中绝对包含（只包含）links item first的元素*/
a[class = "links item first"]{
    background-color: blue;
}
```

# 27、文本样式

## 颜色  

​	color rgb rgba

## 文本对齐方式  

​	text-align = center

## 首行缩进 

​	 text-indent: 2em

## 行高  

​	line-height:

##  装饰   

​	text-decoration:  下划线/中划线/上划线/超链接去除默认下划线

##  文本图片水平对齐： 

​	vertical-align: middle

## 文本阴影

​	#price{
​		/* 阴影颜色  水平偏移  垂直偏移  阴影半径*/
​		text-shadow: #010a0e 10px 0px 15px;
​	}

# 28、超链接伪类

```css
a{
    /*默认颜色*/
    text-decoration: none;
    color: black;
}
a:hover {
    /*鼠标悬浮颜色, 并且改变字体大小*/
    color: #3cbda6;
    font-size: 50px;
}
a:active {
    /*鼠标点击颜色，未释放的时候*/
    color: green;
}
a:visited{
    /*未访问的时候的颜色*/
    color: rebeccapurple;
}
a:visited{
    /*已经访问过的颜色*/
    color: pink;
}
```

# 29、列表

```css
ul li {
    line-height: 30px;
    list-style: none; /* none->去掉开头的圆点  square->正方形 
                         circle->空心圆  decimal->有序列表  */
    text-indent: 1em; // 开头缩进多少
}
```

# 30、背景 - 背景图片、背景颜色

```css
<style>
    div{
        width: 1000px;
        height: 700px;
        border: 1px solid red; /*border: 粗细  样式  颜色*/
        background-image: url("css/a.png"); /*默认全部平铺*/
    }

    .div1 {
        background-repeat: repeat-x; /*平铺*/
    }

    .div2 {
        background-repeat: repeat-y; /*竖直铺*/
    }
    .div3 {
        background-repeat: no-repeat; /*不平铺，只放一个图*/
    }
</style>
```

```css
  background-color
  background-image
  background-repeat : 重复
  background-position ：位置
  background-size ：大小
  background-origin ：相对起点
  background-clip ： 裁剪
  background-attachment ：可以设置背景图画是随目标翻滚还是固定不动。
```

# 31、高度

{% asset_img  dom高度.png %}

# 32、为什么要清除浮动?举个实际场景?

```
父元素的高度是由子元素撑开的，
且子元素设置了浮动，
父元素没有设置浮动，
子元素脱离了标准的文档流，
那么父元素的高度会将其忽略，
如果不清除浮动，
父元素会出现高度不够，
那样如果设置border或者background都得不到正确的解析。

方式:
.clearfix::after,
.clearfix::before{   
   content:"";  
   display:table;   
   clear:both;
}
```

#  33、CSS 去掉inline-block元素间隙的几种方法?

```css
间隙是怎么来的:

间隙是由换行或者回车导致的;
只要把标签写成一行或者
标签直接没有空格，就不会出现间隙;

怎么去除?

方法1:
元素间的间隙出现的原因
是元素标签之间的空格，
把空格去掉间隙自然就会消失。
<div class="itlike">
  <span>撩课itlike</span><span>撩课itlike</span>
</div>

方法2:
利用HTML注释标签
<div class="demo">
    <span>撩课itlike</span>
    <!-- -->
    <span>撩课itlike</span>
</div>

方法3:
取消标签闭合
<div class="demo">
    <span>撩课itlike
    <span>撩课itlike
    <span>撩课itlike
    <span>撩课itlike
</div>

方法4:
在父容器上使用font-size:0;可以消除间隙
<div class="demo">
    <span>撩课itlike</span>
    <span>撩课itlike</span>
    <span>撩课itlike</span>
    <span>撩课itlike</span>
</div>
.demo {font-size: 0;}
```

# 34、浮动元素的特点

**特点：**

1.  脱标（脱离标准流），不占位置；
2. 元素浮动，后面标准流会上来把没浮动元素之前的位置占了，浮动元素会压住后面上来的标准流，不会对前面的标准流造成影响，如果不清除浮动的话，常常出现**高度塌陷**现象；
3. 浮动元素不会压住父元素的边框、内容和图片，只会围绕着浮动元素；
4. 浮动的元素顶对齐；
5. 浮动的元素设置宽高生效，具有行内块的特点；
6. 不能用text-align: center;因为脱标了所以盒子不能居中；

# 35、 说一说你知道的布局方式?

**常用的方法有: 流式, 浮动, 定位, 弹性盒模型, gird网格布局, 响应式布局**

## 流式布局


流式布局，就是**百分比布局**

通过盒子的**宽度设置成百分比**来根据屏幕的宽度来进行伸缩，**不受固定像素的限制**，内容向两侧填充。

流式布局方式是**移动web开发使用的比较常见的布局方式**, 也就是说将**CSS单位全部换成百分比单位**，以达到自适应屏幕的大小

缺点：需要依赖**百分比的参考对象**

核心：宽度通过设置百分比，以此来达到不同的设备对应的是不同的宽度

## 浮动布局

- 优点：兼容性好。
- 缺点：浮动会脱离标准文档流，因此要[清除浮动](https://so.csdn.net/so/search?q=清除浮动&spm=1001.2101.3001.7020)。我们解决好这个问题即可。

## 绝对定位


优点：快捷。

缺点：导致子元素也脱离了标准文档流，可实用性差。

## flex布局(CSS3中出现的)

- 优点：解决上面两个方法的不足，flex布局比较完美。移动端基本用 flex布局。

## 网格布局（[grid](https://so.csdn.net/so/search?q=grid&spm=1001.2101.3001.7020)）

- [CSS3](https://so.csdn.net/so/search?q=CSS3&spm=1001.2101.3001.7020)中引入的布局，很好用。代码量简化了很多。

```html
<style>
    html * {
        padding: 0;
        margin: 0;
    }
    /* 重要：设置容器为网格布局，宽度为100% */
    .layout.grid .left-center-right {
        display: grid;
        width: 100%;
        grid-template-rows: 100px;  /* 重要：设置网格每列的高度。*/
        grid-template-columns: 300px auto 300px;  /* 重要：设置网格为三列，并设置每列的宽度。即可。*/
    }
    .layout.grid .left {
        background: red;
    }
    .layout.grid .center {
        background: green;
    }
    .layout.grid .right {
        background: blue;
    }
</style>

<body>
    <section class="layout grid">
        <article class="left-center-right">
            <div class="left">
                我是 left
            </div>
            <div class="center">
                <h1>网格布局解决方案</h1>
                我是 center
            </div>
            <div class="right">
                我是 right
            </div>
        </article>
    </section>
</body>
```









25、25、25、