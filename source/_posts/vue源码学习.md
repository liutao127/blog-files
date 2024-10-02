---
title: vue源码学习
date: 2022-10-11 15:33:18
tags: vue源码
---

# 1. mustache模板语法

<!-- more -->

**模板语法历史上有四个阶段**

## 1.纯dom法: 通过document.create***()来直接操作dom实现

```js
<ul id="list"></ul>
<script>
    var arr = [
        { "name": "小明", "age": 23, sex: "男" },
        { "name": "小刚", "age": 14, sex: "男" },
        { "name": "小强", "age": 3, sex: "男" },
    ]
    var list = document.getElementById('list');
    for(var i = 0; i < arr.length; i++) {
        // 每遍历一项都要用DOM方法创建一个li标签
        let oLi = document.createElement('li');
        // oLi.innerText = arr[i].name;
        // list.appendChild(oLi);
        var hd = document.createElement('div');
        hd.className = 'hd';
        hd.innerText = arr[i].name + '的基本信息';
        oLi.appendChild(hd);
        var db = document.createElement('div');
        db.className = 'db';
        var p1 = document.createElement('p');
        p1.innerText = '姓名: ' + arr[i].name;
        var p2 = document.createElement('p');
        p2.innerText = '年龄:' + arr[i].age;
        var p3 = document.createElement('p');
        p3.innerText = '性别: ' + arr[i].sex;
        db.appendChild(p1);
        db.appendChild(p2);
        db.appendChild(p3);
        oLi.appendChild(db);
        list.appendChild(oLi);
    }
</script>
```



## 2.数组join法: 通过在数组中分行填充数组,最后调用join()来实现

**因为数组中是可以进行阻断进行拼接的(+ doSomething +)**

```js
<ul id="list"></ul>

<script>
    var arr = [
        { "name": "小明", "age": 23, sex: "男" },
        { "name": "小刚", "age": 14, sex: "男" },
        { "name": "小强", "age": 3, sex: "男" },
    ]
    var list = document.getElementById('list');
    for(let i = 0; i < arr.length; i++) {
        var str = [
            '<li>',
            '  <div class="hd">' + arr[i].name + '的基本信息' + '</div>',
            '  <div class="db">',
            '    <p>' + '姓名: ' + arr[i].name + '</p>',
            '    <p>' + '年龄: ' + arr[i].age + '</p>',
            '    <p>' + '性别: ' + arr[i].sex + '</p>',
            '  </div>',
            '</li>'
        ].join('');
    	list.innerHTML += str;
}
</script>
```

## 3.ES6模板字符串

**一个``直接进行拼接需要的变量就完事了**

```js
<ul id="list"></ul>
<script>
    var arr = [
        { "name": "小明", "age": 23, sex: "男" },
        { "name": "小刚", "age": 14, sex: "男" },
        { "name": "小强", "age": 3, sex: "男" },
    ]
	var list = document.getElementById('list');
	for(let i = 0; i < arr.length; i++) {
    	var str = `
            <li>
            <div class="hd">${arr[i].name}的基本信息</div>
            <div class="db">
            <p>姓名: ${arr[i].name}</p>
            <p>年龄: ${arr[i].age}</p>
            <p>性别: ${arr[i].sex}</p>
            </div>
            </li>
		`;
    	list.innerHTML += str;
    }
</script>
```

## 4. mustache语法

### 循环

```js
<div id="container"></div>
<script type="module">
    import Mustache from '../jslib/mustache.js';
    var templateStr = `
    <ul>
      {{#arr}}
        <li>
          {{name}}的爱好是: 
          <ol>
            {{#hobbies}}
              <li>
                {{.}}
              </li>
            {{/hobbies}}
          </ol>
        </li>
      {{/arr}}
    </ul>
    `;
    var data = {
      arr: [
        { "name": "小明", "age": 23, sex: "男", "hobbies": ['游泳', '写作业'] },
        { "name": "小刚", "age": 14, sex: "男" , "hobbies": ['打篮球', '编程']},
        { "name": "小强", "age": 3, sex: "男", "hobbies": ['rap', '唱歌'] }
      ]
    };
    var dataStr = Mustache.render(templateStr, data);
    var con = document.getElementById('container');
    con.innerHTML = dataStr;
</script>
```

### 不循环 + if

```js
<div id="container"></div>
<script type="module">
    import Mustache from '../jslib/mustache.js';
    var templateStr = `
      {{#m}}
        <h1>今天我好开心</h1>
      {{/m}}
      {{#n}}
        <h1>今天天气不好</h1>
      {{/n}}
    `;
    var data = {
      m: false,
      n: true
    };
    var dataStr = Mustache.render(templateStr, data);
    var con = document.getElementById('container');
    con.innerHTML = dataStr;
</script>
```

### 使用script标签

用text/template(名字随便起, 只要是浏览器不识别就可以)

**因为script标签和``(反引号)会识别换行符,而单独的''或者""(单引号或者双引号)是不识别换行符的.**

```html
<div id="container"></div>
  <!-- 模板引擎 -->
<script type="text/template" id="mytemplate">
    <ul>
      {{#arr}}
        <li>
          <div class="hd">{{name}}的基本信息</div>
          <div class="db">
            <p>姓名: {{name}}</p>
            <p>年龄: {{age}}</p>
            <p>性别: {{sex}}</p>
          </div>
        </li>
      {{/arr}}
    </ul>
</script>
<script type="module">
    import Mustache from '../jslib/mustache.js'
    var mytemplate = document.getElementById('mytemplate').innerHTML;
    var data = {
      arr: [
        { "name": "小明", "age": 23, sex: "男" },
        { "name": "小刚", "age": 14, sex: "男" },
        { "name": "小强", "age": 3, sex: "男" },
      ]
    }
    var dataStr = Mustache.render(mytemplate, data);
    console.log(dataStr);
    var con = document.getElementById('container');
    con.innerHTML = dataStr;

</script>
```

## 重点: 手写mustache(简易版)

**文件功能说明**

```js
index.js: 程序入口文件
parseTemplateInToTokens.js -> return nestTokens(tokens): 将mustache模板转化为tokens
Scanner.js -> return word: 扫描mustache模板,返回word字符串给parseTemplateInToTokens
nestTokens.js -> return tokens: 将tokens格式化,因为此时收到的tokens是不规则的,没有对循环的数据进行归并
renderTemplate.js -> return resultStr: 将tokens数据变成dom字符串
parseArray.js -> return resultStr: 将循环嵌套的tokens数组,转换为dom字符串
lookup.js -> return dataObj: 找到传入的data中的keyName属性,并返回这个属性值
```

**index.js**

```js
import parseTemplateInToTokens from './parseTemplateInToTokens';
import renderTemplate from './renderTemplate';

window.SGG_TemplateEngine = {
  render: function(template, data) {
    // 调用parseTemplateToTokens函数, 让模板字符串能够变成tokens数组
    var tokens = parseTemplateInToTokens(template);
    console.log(tokens);
    // 调用renderTemplate函数, 让tokens数组变为dom字符串
    var domStr = renderTemplate(tokens, data);
    console.log(domStr);
    return domStr;
  }
}
```

**parseTemplateInToTokens.js**

```js
import Scanner from './Scanner';
import nextTokens from './nextTokens';
/* 
  将模板字符串变成tokens数组
*/
export default function parseTemplateToTokens(template) {
  // 实例化一个扫描器,构造时候提供一个参数,这个参数就是模板字符串
  // 也就是说这个扫描器就是针对这个模板字符串工作的
  var tokens = [];
  // 创建扫描器
  var scanner = new Scanner(template);
  var word;
  // 让扫描器工作
  while(!scanner.eos()) {
    word = scanner.scanUtil('{{');
    if(word != '') {
      // 存起来,去掉空格, 智能判断是普通文字的空格,还是标签中的空格
      // 不能去掉标签名和类名之间的空格
      let isInJJH = false;
      var _word = '';
      for(let i = 0; i < word.length; i++) {
        // 上来先判断是否在标签内
        if (word[i] == '<') {
          isInJJH = true;
        } else if (word[i] == '>') {
          isInJJH = false;
        }
        if(word[i] != ' ') {
          _word += word[i];
        } else {
          if (isInJJH) {
            // 如果这项是空格, 那么只有当他在标签内的时候才拼接上
            _word += word[i]
          }
        }
      }
      tokens.push(['text', _word]);
    }
    scanner.scan('{{');
    word = scanner.scanUtil('}}');
    if (word != '') {
      // 这的word就是{{}}中间的东西, 判断一下首字符
      if(word[0] == '#') {
        tokens.push(['#', word.substring(1)]);
      } else if (word[0] == '/') {
        tokens.push(['/', word.substring(1)]);
      } else {
        tokens.push(['name', word]);
      }
    }
    scanner.scan('}}');
  }
  // 返回折叠收集过的tokens
  return nextTokens(tokens);
}
```

**Scanner.js**

```js
/* 
  扫描器类
*/
export default class Scanner {
  constructor(templateStr) {
    this.templateStr = templateStr;
    // 指针
    this.pos = 0;
    this.tail = templateStr;
  }

  // 功能弱, 就是走过指定内容,没有返回值
  scan(tag) {
    if(this.tail.indexOf(tag) == 0) {
      // tag有多长, 比如{{长度是2, 就让指针后移多少位
      this.pos += tag.length;
      // 尾巴也要变,改变尾巴为从当前指针这个字符开始,到最后的字符
      this.tail = this.templateStr.substring(this.pos);
    }
  }

  // 让指针进行扫描,直到遇到指定内容结束,并且能够返回结束之前的文字
  scanUtil(stopTag) {
    // 记录一下执行本方法的时候pos的值
    const pos_backup = this.pos;
    // 当尾巴的开头不是stopTag的时候, 就说明还没有扫描stopTag
    // 加&&是特别重要的, 因为如果是最后一波, 防止指针到最后也没停下来, 陷入死循环
    while(!this.eos() != 0 && this.tail.indexOf(stopTag) ) {
      this.pos++;
      this.tail = this.templateStr.substring(this.pos);
    }
    
    return this.templateStr.substring(pos_backup, this.pos);
  }

  // 指针是否到头, 返回布尔值. end of string
  eos() {
    return this.pos >= this.templateStr.length;
  }

}
```

**nestTokens.js**

```js
/* 
  函数功能: 折叠tokens, 能够将#和/之间的tokens整合起来, 作为他的下标为3的项
*/
export default function nextTokens(tokens) {
  // 结果数据
  var nextTokens = [];
  // 收集器,天生指向nextTokens, 收集器的指向会变化,如果遇到#的时候,收集器会指向token下标为2的新数组
  var collector = nextTokens;
  // 栈结构, 存放小tokens, 栈顶(靠近端口的, 最新进入的) 的tokens数组中当前操作的这个tokens小数组
  var sections = [];

  for(let i = 0; i < tokens.length; i++) {
    let token = tokens[i];
    switch(token[0]) {
      case '#':
        // 收集器中放入这个token
        collector.push(token);
        // 入栈
        sections.push(token);
        // 收集器要换人了, 给token添加下标为2的项,并且让收集器指向它
        collector = token[2] = [];
        break;
      case '/':
        // 出栈
        sections.pop();
        // 改变收集器为栈结构的队尾(队尾为栈底)
        collector = sections.length > 0 ? sections[sections.length-1][2] : nextTokens;
        break;
      default:
        // 甭管collector是谁,可能是结果nextTokens,也可能是某个token的下标为2的数组,甭管是谁,推入collector即可
        collector.push(token);        
    }
  }
  return nextTokens;
}
```

**renderTemplate.js**

```js
/* 
  函数功能: 让tokens数组,变为dom字符串
*/
import lookup from './lookup';
import parseArray from './parseArray';

export default function renderTemplate(tokens, data) {
  var resultStr = '';
  for(let i = 0; i < tokens.length; i++) {
    let token = tokens[i];
    if(token[0] == 'text') {
      resultStr += token[1];
    } else if (token[0] == 'name') {
      // 这里 lookup 是为了防止 遇到 a.b.c 这种嵌套的对象名
      if(token[1] == '.') {
        resultStr += data;
      } else {
        resultStr += lookup(data, token[1]);
      }
    } else if (token[0] == '#') {
      resultStr += parseArray(token, data);
    }
  }
  return resultStr;
}
```

**parseArray.js**

```js
/* 
  功能: 处理数组, 结合renderTemplate实现递归
  注意, 这个函数接受的参数是token!而不是tokens
  token是什么, 就是一个简单的['#', 'student', [...]]

  这个函数要递归调用renderTemplate, 调用多少次??
  调用次数由data决定
*/
import lookup from "./lookup";
import renderTemplate from "./renderTemplate";
export default function parseArray (token, data) {
    
  // 得到整体数据data中这个数据中要使用的部分
  var v = lookup(data, token[1]);
  // 结果字符串
  var resultStr = '';
  // 遍历v数组, v一定是数组
  // ************************************************
  // 最重要的循环
  for(let i = 0; i < v.length; i++) {
    // 为什么是用v[i]?
    // - 因为这里是循环渲染相同的模板, 每次都是一行新的数据
    resultStr += renderTemplate(token[2], v[i]);
  }
  // ************************************************
  return resultStr;
}
```

**lookup.js**

```js
/* 
  功能: 可以在dataObj对象中, 寻找用连续点符号的keyName属性
  比如: dataObj 是
  {
    a: {
      b: {
        c: 100
      }
    }
  }
  那么lookup(dataObj, 'a.b.c')的结果就是100;
*/
export default function lookup(dataObj, keyName) {
  if(keyName.indexOf('.') != -1 && keyName != '.') {
    var arr = keyName.split('.');
    var temp = dataObj;
    for(let i = 0; i < arr.length; i++) {
      temp = temp[arr[i]];
    }
    return temp;
  }
  return dataObj[keyName];
}
```

# 2. 虚拟节点VNode和diff算法

**太优雅了,呜呜呜呜**

## 1. 学习h函数

```js
import {
  init,
  classModule,
  propsModule,
  styleModule,
  eventListenersModule,
  h,
} from "snabbdom";


// 创建patch函数
const patch = init([classModule, propsModule, styleModule, eventListenersModule]);

// 创建虚拟结点
const myVnode1 = h('a', {
  props: {
    href: 'http://www.baidu.com',
    target: '_blank'
  }
},'百度一下');

const myVnode2 = h('div', '我是一个盒子');
// const myVnode2 = h('div', {class: {box: true}}, '我是一个盒子');

const myVnode3 = h('ul', [
  h('li', {class: {li1: true}}, '苹果'),
  h('li', {}, '西瓜'),
  h('li', '梨'),
  h('li', [
    h('div', [
      h('p', '哈哈'),
      h('p', '嘻嘻')
    ])
  ]),
  h('li', [h('span', '火龙果'), h('span', '提子')])
])
console.log(myVnode3);
// 让虚拟结点上树
const container = document.getElementById('container');

patch(container, myVnode3);
```

## 2. 体验diff算法

```js
import {
  init,
  classModule,
  propsModule,
  styleModule,
  eventListenersModule,
  h,
} from "snabbdom";


// 创建patch函数
const patch = init([classModule, propsModule, styleModule, eventListenersModule]);

/* 
  - 最小量更新太厉害啦!真的是最小量更新!当然，key很重要。
    key是这个节点的唯一标识，告诉diff算法，在更改前后它们是同一个DOM节点。
  - 只有是同一个虚拟节点，才进行精细化比较，否则就是暴力删除旧的、插入新的。
    延伸问题:如何定义是同一个虚拟节点? 答:选择器相同且key相同。
  - 只进行同层比较，不会进行跨层比较。
    即使是同一片虚拟节点，但是跨层了，对不起，精细化比较不diff你。而是暴力删除旧的、然后插入新的。
  - diff并不是那么的“无微不至”啊!真的影响效率么??
    答:上面的操作在实际Vue开发中，基本不会遇见，所以这是合理的优化机制。
*/

const container = document.getElementById('container');
const btn = document.getElementById('btn');

const vnode1 = h('ul', {}, [
  h('li', { key: 'A' }, 'A'),
  h('li', { key: 'B' }, 'B'),
  h('li', { key: 'C' }, 'C'),
  h('li', { key: 'D' }, 'D'),
]);

patch(container, vnode1);

const vnode2 =  h('ul', {}, [
  h('li', { key: 'F' }, 'F'),
  h('li', { key: 'A' }, 'A'),
  h('li', { key: 'B' }, 'B'),
  h('li', { key: 'C' }, 'C'),
  h('li', { key: 'D' }, 'D'),
  h('li', { key: 'E' }, 'E'),
]);

// 点击按钮的时候让vnode1变为vnode2
btn.onclick = function() {
  patch(vnode1, vnode2);
}
```

## 3.我的h函数

### **虚拟dom的格式 --- vnode.js**

```js
export default function(sel, data, children, text, elm) {
  const {key} = data;
  return {
    sel, data, children, text, elm, key
  }
}
```

### **h函数**

**功能: 将dom结点的信息转换为虚拟dom, 返回值是一个vnode类型的对象(其实就是vnode把传入的对象解构成一个对象返回了出去)**

```js
import vnode from './vnode.js';

// 编写一个低配版本的h函数，这个函数必须接受3个参数，缺一不可
// 相当于它的重载功能较弱。
// 也就是说，调用的时候形态必须是下面的三种之一:
// 形态1 h( 'div'， {}，‘文字')
// 形态2 h( 'div ', {}，[])
// 形态3 h( 'div', {},h())

export default function(sel, data, c) {
  if(arguments.length != 3) {
    console.log(arguments, arguments.length);
    throw new Error('对不起我的h函数属于低配版,必须三个参数!');
  }
  // 检查c参数的类型
  if(typeof c === 'string' || typeof c === 'number') {
    // 形态1
    return vnode(sel, data, undefined, c, undefined);
  } else if (Array.isArray(c)) {
    // 说明是形态2
    let children = [];
    for(let i = 0; i < c.length; i++) {
      if(!(typeof c[i] === 'object' && c[i].hasOwnProperty('sel'))) {
        throw new Error('传入的数据中有项不是h函数');
      }
      // 这里的c[i]不需要执行, 因为在测试语句中已经执行了, 所以只需要进行收集就可以了
      children.push(c[i]);
    }
    // 循环结束,children收集完毕,可以返回虚拟节点了
    return vnode(sel, data, children, undefined, undefined);
  } else if (typeof c === 'object' && c.hasOwnProperty('sel')) {
    // 说明形态3
    // 这里说明c是h函数,在测试中已经执行完了,所以这里直接返回就可以了
    let children = [c];
    return vnode(sel, data, children, undefined, undefined);
  } else {
    // 传入的第三个参数, 类型不对
    throw new Error('传入的第三个参数的类型不对');
  }
}
```

## 重点: diff算法分析以及知识点

### 1. 如何判断新旧结点是否是同一个节点?

{% asset_img  如何判断新旧结点是否是同一个结点.jpg %}

### 2. patch函数流程?

{% asset_img  patch函数流程图总.jpg %}

### 3. diff算法的四种判定方式?

{% asset_img  diff四判定方式1.jpg %}

### 4. 循环结束之后,如果有多余结点怎么处理?

**一: 如果旧节点先循环完毕, 仍然有新节点, 说明新节点中有要插入到旧节点中的, 这个时候通过循环, 将新节点中剩余节点插入到旧节点**

**二: 如果新节点先循环完毕, 仍有旧节点, 说明旧节点中要删除部分节点, 这个时候通过循环, 删除旧节点中的剩余节点**

## 重点: 手写 diff 算法(简易版)

### 文件说明: 

**vnode.js属性说明**

```js
sel, data, children, text, elm, key
sel: 真实DOM结点的标签(eg: div, ul, li...)
data: 真实DOM结点的标签属性(name, id, class, style)
children: 真实DOM结点的孩子属性,属性值也是vnode对象
text: 真实DOM节点中的文本信息
elm: 真实DOM结点
key: 节点绑定的key值
****** 其中key属性是从data属性中解构出来的 *******
```

- **虚拟DOM**: 其实就是一个object对象{}
- **index.js**: 文件的入口
- **vnode.js**: 将传入的DOM结点信息转换为虚拟dom
- **h.js**: 传入dom结点信息,并且判断类型,最后调用vnode返回虚拟dom
- **createElement.js**: 将传入的虚拟DOM创建为真实DOM
- **patch.js**: 1. 首先会判断老节点是否为真实DOM结点? 
   -1.1如果是转换为虚拟DOM 
   -1.2如果不是会判断新旧结点是否是同一个结点? 

   1.2.1如果是, 那么直接 patchVnode(old, new);

   1.2.2如果不是, 那么会直接暴力删除老节点,将新节点转换为真实DOM,然后append到页面中
 - **patchVnode.js**: 将相同的2个虚拟DOM进行对比,进行精细化比较的第一步
  - 如果是同一个虚拟DOM对象,什么都不做,直接return
  - 如果两个结点都是只有文本信息, 如果文本相同什么都不做,如果不同,替换旧虚拟DOM中
  - 如果老虚拟DOM中没有children属性(说明只是text属性), 那么直接清空text属性, 将新虚拟DOM的children属性通过createElement方法创建为真实DOM,然后appendChild到老DOM节点的elm属性中
  - 这种情况最复杂, 说明老节点和新节点都有children属性, 然后调用updateChildren.js的方法进行精细化比较.
- **updateChildren.js**: 精细化比较children属性

### **index.js**

```js
import h from './mySnabbdom/h';
import patch from './mySnabbdom/patch';

const btn = document.getElementById('btn');
const container = document.getElementById('container');

const vnode1 = h('ul', {}, [
  h('li', { key: 'A' },'A'),
  h('li', { key: 'B' },'B'),
  h('li', { key: 'C' },'C'),
  h('li', { key: 'D' },'D'),
  h('li', { key: 'E' },'E'),
])

patch(container, vnode1);

const vnode2 = h('ul', {}, [
  h('li', { key: 'Q' }, 'Q'),
  h('li', { key: 'B' },'B'),
  h('li', { key: 'C' },'C'),
  // h('li', { key: 'D' },'D'),
  // h('li', { key: 'E' },'E'),
  h('li', { key: 'F' },'F'),
  h('li', { key: 'A' },'AAA'),
  h('li', { key: 'E' },'E'),
]);

btn.onclick = function() {
  patch(vnode1, vnode2);
}
```

### **patch.js**

```js
import vnode from './vnode';
import createElement from './createElement';
import patchVnode from './patchVnode';

export default function(oldVnode, newVnode) {
  // 判断传入的第一个参数, 是DOM节点还是虚拟节点
  if(oldVnode.sel == '' || oldVnode.sel == undefined) {
    oldVnode = vnode(oldVnode.tagName.toLowerCase(), {}, [], undefined, oldVnode);
  }
  // 判断oldVnode和newVnode是不是同一个节点
  if(oldVnode.key == newVnode.key && oldVnode.sel == newVnode.sel) {
    console.log('是同一个');
    patchVnode(oldVnode, newVnode);
  } else {
    console.log('不是同一个');
    // 将newVDOM创建为DOM节点,并且插入到oldVDOM前面进行上树操作
    let newVnodeElm = createElement(newVnode);
    // 插入到老节点之前
    if(oldVnode.elm.parentNode && newVnodeElm) {
      oldVnode.elm.parentNode.insertBefore(newVnodeElm, oldVnode.elm);
    }
    // 删除老的节点
    oldVnode.elm.parentNode.removeChild(oldVnode.elm);
  }
}
```

### **createElement.js**

```js
// 真正创建节点
// 将vnode创建为DOM,是孤儿节点, 不进行插入
export default function createElement(vnode) {
  // console.log('把虚拟节点', vnode, '插入到标杆', pivot, '之前');
  // 孤儿节点
  let domNode = document.createElement(vnode.sel);
  // 有子节点或者有孩子节点?[这个是弱智版, 只能存在一个]
  if(vnode.text != '' && (vnode.children == undefined || vnode.children.length == 0)) {
    // 内部是文字 => 相当于上树
    domNode.innerText = vnode.text;
    // pivot.parentNode.insertBefore(domNode, pivot);
  } else if (Array.isArray(vnode.children) && vnode.children.length > 0) {
    // 他的内部是子节点, 就要递归创建节点
    for(let i = 0; i < vnode.children.length; i++) {
      // 得到这个children
      let ch = vnode.children[i];
      // 创建出它的DOM, 一旦调用createElement意味着:创建出DOM了
      // 并且elm指向了创建出的DOM, 但是还没有上树,是一个孤儿节点
      let chDom = createElement(ch);
      // 上树
      domNode.appendChild(chDom);
    }
  }
  // 补充elm属性
  vnode.elm = domNode;
  // 返回elm, elm是一个纯DOM对象
  return vnode.elm;
}
```

### **patchVnode.js  精细化第一步**

```js
import createElement from "./createElement";
import updateChildren from "./updateChildren";

// 对比两个结点
export default function patchVnode(oldVnode, newVnode) {
  // 如果新旧vnode是同一个对象
  if(oldVnode === newVnode) return;
  // 判断新vnode有没有text属性
  if(newVnode.text != undefined && (newVnode.children === undefined || newVnode.children.length == 0)) {
    if(newVnode.text != oldVnode.text) {
      // createElement(newVnode);
      // console.log(oldVnode.elm, newVnode);
      // 如果新虚拟节点中的text和老虚拟节点的text不同,那么直接让新节点的text写入老的节点的elm中即可.即使老的节点中有children也会立即消失
      oldVnode.elm.innerText = newVnode.text;
      // 自己加的代码
      newVnode.elm = oldVnode.elm;
    }
  } else {
    // 最复杂的情况, 新节点有children属性
    // 新vnode没有text属性
    // console.log('新vnode没有text属性');
    // 判断老的有没有children
    if(oldVnode.children != undefined && oldVnode.children.length > 0) {
      // 老节点有children, 此时是最复杂的情况, 就是新老都有children
      // 所有未处理的节点(老节点中)的开头
      updateChildren(oldVnode.elm, oldVnode.children, newVnode.children);
    } else {
      // 老节点没有children, 新的有
      // 先清空oldVnode的内容
      oldVnode.elm.innerText = '';
      // 然后遍历新的节点的children,逐个进行append到老节点上 --- 循环上树
      for(let i = 0; i < newVnode.children.length; i++) {
        let dom = createElement(newVnode.children[i]);
        oldVnode.elm.appendChild(dom);
      }
    }
  }
}
```

### **精细化重点(diff算法):  updateChild.js **

```js
import patchVnode from "./patchVnode";
import createElement from "./createElement";

export default function updateChildren(parentElm, oldCh, newCh) {
  console.log('我是updateChildren');
  // console.log(parentElm, oldCh, newCh);

  // 索引值
  // 旧前
  let oldStartIndex = 0;
  // 旧后
  let oldEndIndex = oldCh.length - 1;
  // 新前
  let newStartIndex = 0;
  // 新后
  let newEndIndex = newCh.length - 1;

  // 真正的节点
  // 旧前节点
  let oldStartVnode = oldCh[0];
  // 旧后节点
  let oldEndVnode = oldCh[oldEndIndex];
  // 新前节点
  let newStartVnode = newCh[0];
  // 新后节点
  let newEndVnode = newCh[newEndIndex];

  // keymap, 用来保存旧节点的key, 为了结块查找速度, 提高效率
  let keyMap = null;

  // 开始大while循环
  while(oldStartIndex <= oldEndIndex && newStartIndex <= newEndIndex) {
    if(oldStartVnode == null) {
      oldStartVnode = oldCh[++oldStartIndex];
    } else if(oldEndVnode == null){
      oldEndVnode = oldCh[--oldEndIndex];
    } else if(newStartVnode == null){
      newStartVnode = newCh[++newStartIndex];
    } else if(newEndVnode == null){
      newEndVnode = newCh[--newEndIndex];
    } else if(checkSameVnode(oldStartVnode, newStartVnode)) {
      // 新前和旧前
      console.log('新前和旧前');
      patchVnode(oldStartVnode, newStartVnode);
      oldStartVnode = oldCh[++oldStartIndex];
      newStartVnode = newCh[++newStartIndex];
    } else if(checkSameVnode(oldEndVnode, newEndVnode)) {
      // 新后和旧后
      console.log('新后和旧后');
      patchVnode(oldEndVnode, newEndVnode);
      oldEndVnode = oldCh[--oldEndIndex];
      newEndVnode = newCh[--newEndIndex]
    } else if(checkSameVnode(oldStartVnode, newEndVnode)) {
      // 新后和旧前
      console.log('新后和旧前');
      patchVnode(oldStartVnode, newEndVnode);
      // 插入 --- 当新后与旧前命中的时候,此时要移动节点,移动新前指向的这个结点到老节点的旧后的后面
      // 如何移动结点? 
      // 只要你插入一个已经在DOM树上的节点, 它就会被移动
      parentElm.insertBefore(oldStartVnode.elm, oldEndVnode.elm.nextSibling);
      oldStartVnode = oldCh[++oldStartIndex];
      newEndVnode = newCh[--newEndIndex];
    } else if(checkSameVnode(oldEndVnode, newStartVnode)) {
      // 新前和旧后
      console.log('新前和旧后');
      // 插入 --- 移动新前指向的节点到老节点的旧前的前面
      parentElm.insertBefore(oldEndVnode.elm, oldStartVnode.elm);
      oldEndVnode = oldCh[--oldEndIndex];
      newStartVnode = newCh[++newStartIndex];
    } else {
      /* 
        都没有的情况 , 寻找key的map --- 缓存
        这里用到了map的方法, 提前把所有的旧节点进行map缓存,所以不需要每次进行循环查找,提高效率
        进行保存map表之后进行查找旧节点中是否有这个结点, 并且进行操作之后, 置这项为undefined
      */
      if(!keyMap) {
        keyMap = {};
        for(let i = oldStartIndex; i <= oldEndIndex; i++) {
          const key = oldCh[i].key;
          if(key != undefined) {
            keyMap[key] = i;
          }
        }
      }
      console.log(keyMap);
      const idInOld = keyMap[newStartVnode.key];
      console.log(idInOld);
      // 如果idInOld是undefined说明他是全新的一项, 否则需要移动
      if(idInOld == undefined) {
        // 需要进行添加
        // 现在要加入的项还是虚拟Dom,还不是真正的DOM节点
        parentElm.insertBefore(createElement(newStartVnode), oldStartVnode.elm);
      } else {
        // 移动
        const elmToMove = oldCh[idInOld];
        patchVnode(elmToMove, newStartVnode);
        // 并且把这项设置为undefined
        oldCh[idInOld] = undefined;
        parentElm.insertBefore(elmToMove.elm, oldStartVnode.elm);
      }
      // 指针下移, 并且只移 新前
      newStartVnode = newCh[++newStartIndex];
    }
  }// 循环结束
  // 继续看有没有剩余的节点或者需要删除的节点
  // 有剩余的节点
  if(newStartIndex <= newEndIndex) {
    console.log('new有剩余,需要进行添加');
    // 遍历新的newCh, 添加到老的没有处理的之前
    // 因为是阉割版的, 所以这里的before会有bug,有时候会是undefined,所以直接插入到旧前之前
    const before = newCh[newEndIndex + 1] == null ? null : newCh[newEndIndex + 1].elm;
    for(let i = newStartIndex; i <= newEndIndex; i++) {
      // insertBefore可以自动识别null, 如果是插入到null之前,那么就会自动添加到队尾
      parentElm.insertBefore(createElement(newCh[i]), oldCh[oldStartIndex]);
    }
  } else if(oldStartIndex <= oldEndIndex) {
    console.log('old中有剩余, 需要进行删除');
    for(let i = oldStartIndex; i <= oldEndIndex; i++) {
      if(oldCh[i] != null) {
        parentElm.removeChild(oldCh[i].elm);
      }
    }
  }
}
// 判断是否是同一个结点
function checkSameVnode(a, b) {
  return a.sel == b.sel && a.key == b.key;
}
```

# 3. 响应式原理

## 1.感受defineProperty

```js
var obj = {};

// Object.defineProperty(obj, 'a', {
//   value: 1,
//   // 是否可写? true 可以改变, false 不会改变
//   writable: false,
//   // 是否可以被枚举
//   enumerable: false,
// });

// get和value不能一起设置
// Object.defineProperty(obj, 'a', {
//   value: 1,
//   get: function() {
//     console.log('我是a个get');
//   }
// });

Object.defineProperty(obj, 'a', {
  get: function() {
    console.log('我是a的get');
  },
  set: function(a) {
    console.log('我是a的set', a);
  }
});

Object.defineProperty(obj, 'b', {
  value: 2,
});
console.log(obj.a);
obj.a = 22;
console.log(obj.a);
```

## 2.封装defineProperty

```js
var obj = {};

function defineReactive(data, key, val) {
  Object.defineProperty(data, key, {
    enumerable: true,
    // 可以被配置, 比如可以被delete
    configurable: true,
    get: function() {
      console.log('视图访问obj的' + key + '属性');
      return val;
    },
    set: function(newval) {
      console.log('视图改变obj的' + key + '属性', newval);
      if(val === newval) {
        return;
      }
      val = newval;
    }
  });
}


defineReactive(obj, 'a', 1);
defineReactive(obj, 'b', 33);

console.log(obj.a);
obj.a = 22;
console.log(obj.a);

console.log(obj.b);
obj.b++;
console.log(obj.b);
```

## 3.手写响应式

### 文件说明

```
index.js: 程序的入口文件
observe.js: 给对象添加observer实例, 递归响应的开始文件
Observer.js: 1: 每个Observer实例对象身上都添加一个Dep, 2: 调用def给对象添加__ob__属性, 属性值为当前的Observer实例对象本身, 3: 将一个正常的object转换为每个层级的属性都是响应式(可以被侦测的)object, 4: 将数组的其中方法进行响应式
utils.js: 暴露def方法 -> 封装的defineProperty, 用来将对象转换为响应式
array.js: 给数组的七种方法进行特殊的操作
defineReactive.js: 将属性进行defineProperty变为响应式属性, 并且进行收集依赖的操作
Dep.js: 使用发布订阅模式, 进行添加订阅者, 添加依赖, 通知更新的操作. 当数据发生变化时，会循环依赖列表，把所有的watcher都通知一遍。
Watcher.js: 依赖就是watcher。只有watcher触发的getter才会收集依赖，哪个watcher触发了getter，就把哪个watcher收集到Dep中。

代码实现的巧妙之处: watcher把自己设置到全局的一个指定位置，然后读取数据，因为读取了数据，所以会触发这个数据的getter。在getter中就能得到当前正在读取数据的watcher，并把这个watcher收集到Dep中。

我感觉Dep更像是一个中间人, 因为 Watcher 是要来访问 obj 的, 我们会提前给obj进行defineReactive调用object.defineProperty进行响应式的处理, 而且 obj 中是会定义 __ob__.dep 对象的(他是Dep的实例对象), 然后当 Watcher 去访问obj的时候, 会触发obj.get方法, 然后get中会调用dep.depend去收集依赖(Watcher) , 看谁访问了 obj.  
然后!!!!!! 当obj中的值发生变化的时候, 就会调用set方法, 然后set中就会调用 dep.notify , 去通知 依赖(Watcher) 去调用 watcher.update 去进行更新操作.
对, 就是这样的!
```

### index.js

```js
import defineReactive from './defineReactive';
import { observe } from './observe';
import Observer from './Observer';
import Watcher from './Watcher';

var obj = {
  a: 1,
  g: [11, 22, 33, 44],
  c: {
    m: {
      n: 4
    }
  }
}

observe(obj);
new Watcher(obj, 'c.m.n', (a, b, c) => {
  console.log('我是watcher' , a, b, c);
})
// obj.a++;
// obj.g.push(55);
obj.c.m.n = 222;
console.log(obj);

```

### observe.js

```js
import Observer from "./Observer";
// 创建observe函数, 注意函数的名字没有r
export const observe = function(value) {
  // 如果value不是对象, 什么都不做
  if(typeof value != 'object') return;
  // 定义ob
  var ob;
  if(typeof value.__ob__ !== 'undefined') {
    ob = value.__ob__;
  } else {
    // console.log(value);
    ob = new Observer(value);
  }
  return ob;
}
```

### Observer.js

```js
import { def } from './utils';
import defineReactive from './defineReactive';
import { arrayMethods } from './array';
import { observe } from './observe';
import Dep from './Dep';

// Observer类的目的是: 将一个正常的object转换为每个层级的属性都是响应式(可以被侦测的)object
export default class Observer {
  constructor(value) {
    // 每个Observer实例对象的身上都有一个Dep
    this.dep = new Dep();
    // 给实例添加了__ob__属性, 值是这次new的实例 (this, 指向的是实例本身, 不是class类本身)
    def(value, '__ob__', this, false);
    // console.log('我是observer构造器', value);
    // 检查是数组还是对象
    if(Array.isArray(value)) {
      //  如果是数据, 要非常强行的蛮干: 将这个数组的原型, 指向arrayMethods
      Object.setPrototypeOf(value, arrayMethods);
      // 让这个数据变的observe
      this.observeArray(value);
    } else {
      this.walk(value);
    }
  }
  // 遍历
  walk(value) {
    for(let k in value) {
      defineReactive(value, k);
    }
  }
  // 数组的特殊遍历
  observeArray(arr) {
    for(let i = 0, l = arr.length; i < l; i++) {
      // 逐项进行observe
      observe(arr[i]);
    }
  }
}
```

### utils.js

```js
export const def = function(obj, key, value, enumerable) {
  Object.defineProperty(obj, key, {
    value,
    enumerable,
    writable: true,
    configurable: true
  })
}
```

### array.js

```js
// 改写Array的七种方法
import { def } from './utils';

// 得到Array.prototype
const arrayPrototype = Array.prototype;

// 以Array.prototype为原型创建arrayMethods对象
// 暴露出去
export const arrayMethods = Object.create(arrayPrototype);

// 要被改写的七个数据方法
const methodsNeedChange = [
  'push',
  'pop',
  'shift',
  'unshift',
  'splice',
  'sort',
  'reverse',
] 

methodsNeedChange.forEach(methodName => {
  // 备份原来的方法
  const original = arrayPrototype[methodName];
  
  // 定义新的方法
  def(arrayMethods, methodName, function() {
    // 把arguments 类数组对象转换为数组对象
    const args = [...arguments];
    // 恢复原来的功能
    const result = original.apply(this, args);

    // 把数组身上的__ob__取出来, __ob__已经被添加了. 
    // 为什么被添加了? 因为数组肯定不是一个对象的最高层,所以代码走到这个地方的时候数组已经添加上了__ob__属性
    const ob = this.__ob__;
    // 有三种方法 push\unshift\splice能够插入新项,现在要把插入的心项也变成observe的
    let inserted = [];
    switch (methodName) {
      case 'push':
      case 'unshift':
        inserted = args;
        break;
      case 'splice':
        inserted = args.slice(2);
        break;
    }

    // 判断有没有要插入的新项, 让新项也变成响应的 
    if(inserted) {
      // observeArray 是Observer类上的一个方法, 而ob是Observer的实例
      ob.observeArray(inserted);
    }

    console.log('lalalalaa');

    ob.dep.notify();

    return result;
  }, false);
})
```

### defineReactive.js

```js
import { observe } from './observe';
import Dep from './Dep';

export default function defineReactive(data, key, val) {
  const dep = new Dep();
  // console.log('我是defineReactive', data, key);
  if(arguments.length == 2) {
    val = data[key];
  }
  // 子元素要进行observe, 至此形成了递归,但是这个递归不是自己调用自己,而是几个函数之间的循环调用
  let childOb = observe(val);

  Object.defineProperty(data, key, {
    enumerable: true,
    // 可以被配置, 比如可以被delete
    configurable: true,
    get: function() {
      console.log('视图访问' + key + '属性');
      // 如果现在处于依赖的收集阶段
      if(Dep.target) {
        dep.depend();
        if(childOb) {
          childOb.dep.depend();
        }
      }
      return val;
    },
    set: function(newval) {
      console.log('视图改变' + key + '属性', newval);
      if(val === newval) {
        return;
      }
      val = newval;
      childOb = observe(newval);
      // 发布订阅者模式, 通知dep
      dep.notify();
    }
  });
}
```

### Dep.js

```js
var uid = 0;
// 每个Observer实例对象的身上都有一个Dep
export default class Dep{
  constructor() {
    // console.log('我是Dep构造器');
    this.id = uid++;
    // 用数据存储自己的订阅者, subs是英语subscribes订阅的意思
    // 这个数组里面放的是Watcher的实例
    this.subs = [];

  }
  // 添加订阅者
  addSub(sub) {
    this.subs.push(sub);
  }
  // 添加依赖
  depend() {
    if(Dep.target) {
      // Dep.target就是我们自己制定的全局的位置,你用window.target也行, 只要是全局唯一,没有歧义就行
      this.addSub(Dep.target);
    }
  }

  // 移除订阅

  // 通知更新
  notify() {
    console.log('notify');
    // 浅克隆一份
    const subs = this.subs.slice();
    // 遍历
    for(let i = 0; i < subs.length; i++) {
      subs[i].update();
    }
  }
};
```

### Watcher.js

```js
import Dep from './Dep';
var uid = 0;
export default class Watcher{
  constructor(target, expression, callback) {
    console.log('我是Watcher构造器');
    this.id = uid++;
    this.target = target;
    this.getter = parsePath(expression);
    this.callback = callback;
    this.value = this.get();
  }
  update() {
    this.run();
  }
  get() {
    // 进入依赖收集阶段, 让全局的Dep.target设置为watcher本身,那么就是进入依赖的手机阶段
    Dep.target = this;
    const obj = this.target;
    var value
    try {
      value =  this.getter(obj);
      
    } finally {
      // 退出依赖收集阶段, 因为别人可能后面会用
      Dep.target = null;
    }
    return value;
  }
  run() {
    // 得到并且唤起
    this.getAndInvoke(this.callback);
  }
  getAndInvoke(cb) {
    const value = this.get();
    if(value !== this.value || typeof value == 'object') {
      const oldValue = this.value;
      this.value = value;
      cb(this.target, value, oldValue);
    }
  }
}

function parsePath(str) {
  var segments = str.split('.');
  return (obj) => {
    for(let i = 0; i < segments.length; i++) {
      if(!obj) return;
      obj = obj[segments[i]];
    }
    return obj;
  }
}

```

# 4. AST抽象语法树

**代码虽少, 但是通过算法学到了不少**

## AST和虚拟节点VNode的区别

{% asset_img  AST-1.png %}

## 1. 递归算法题

{% asset_img  AST-2.png %}

**重点思想: 用map代替循环, 并且map返回值是数组!!**

```js
// 方法一
// function foo(item) {
//   let obj = {};
//   if(!Array.isArray(item)) {
//     obj.value = item;
//   } else {
//     obj.children = [];
//     for(let i = 0; i < item.length; i++) {
//       obj.children.push(foo(item[i]));
//     }
//   }
//   return obj;
// }

// 方法二
// 更简洁的方式  ===  用map代替for循环
function foo(item) {
  if(!Array.isArray(item)) {
    return {
      value : item
    };
  } else {
    return {
      children: item.map(_item => foo(_item))
    };
  }
  return obj;
}

var arr = [1, 2, [3, [4, 5], 6], 7, [8, [10, 11, [112, 113]]], 9];

var obj = foo(arr);
console.log(obj);
```

## 2. 栈算法题

{% asset_img  栈算法题目.jpg %}

**解题思路**

{% asset_img  栈解题思路.jpg %}

**正则知识点: **

{% asset_img  正则1.jpg %}

{% asset_img  正则2.png %}

{% asset_img  正则3.jpg %}

**代码**

```js
var str = '2[3[a]4[1[b]2[c]]]';
function foo(str) {
  let num = []; // 存放数字的栈
  let strs = []; // 存放字符的栈
  let res = ''; // 结果字符串
  for(let i = 0; i < str.length; i++) {
    let cur = str[i];
    if(Number(cur) || Number(cur) == 0) {
      // 数字
      if(!Number.isNaN(Number(str[i-1]))) {
        // 防止出现两位以及两位以上数字的情况
        num[num.length-1] = num[num.length-1] + cur;
      } else {
        num.push(cur);
      }
    } else if(cur == '[') {
      strs.push('');
    } else if(cur == ']') {
      var tempNum = num.pop();
      var tempStr = strs.pop();
      // var resStr = '';
      /* 
        str.repeat(num)
         - 将一个字符串重复num次, 然后返回结果字符串
         - console.log('刘涛'.repeat(4));  // 刘涛刘涛刘涛刘涛
      */
      var resStr = tempStr.repeat(tempNum);
      // for(let i = 0; i < tempNum; i++) {
      //   resStr += tempStr;
      // }
      if(i + 1 == str.length) {
        // 这里说明当前是最后一项, 并且是最后一个 ']'
        // 这个时候num和strs都是空栈了,所以 strs[strs.length-1] == undefined
        // 这个时候直接确定是结果值了
        return resStr;
      } else {
        strs[strs.length-1] += resStr;
      }
    } else {
      strs[strs.length-1] = cur;
    }
  }
}
console.log(foo(str));
```

## 3. 手写AST算法

### 文件说明

```
index.js: 程序入口文件
parse.js: 扫描html字符串, 进行拆分
	- 组成父子: {tag: 'div', children: Array(2), attrs: undefined}
parseAttrsToString.js: 将标签中的字符串进行拆分, 拆成kv对的形式
	- 标签: <h3 class="aa bb cc" data-n="7" id="mybox">你好</h3>
	- 传入 class="aa bb cc" data-n="7" id="mybox"
	- 返回[{name: 'class', value: 'aa bb cc'}, {name: 'data-n', value: '7'}, {name: 'id', value: 'mybox'}]
```

### index.js

```js
import parse from './parse';

var templateStr = `<div>
    <h3 class="aa bb cc" data-n="7" id="mybox">你好</h3>
    <ul>
      <li>A</li>
      <li>B</li>
      <li>C</li>
    </ul>
  </div>`;
const ast = parse(templateStr);
console.log(ast);
```

### parse.js

```js
import parseAttrsToString from './parseAttrsToString'
export default function parse(templateStr) {
  // 指针
  var index = 0;
  // 字符串剩余部分
  var rest = '';
  // 开头标签的正则
  var startRegExp = /^\<([a-z]+[1-6]?)(\s[^\<]+)?\>/;
  // 结束标签
  var endRegExp = /^\<\/([a-z]+[1-6]?)\>/;
  // 抓取结束标记前的文字
  var wordRegExp = /^([^\<]+)\<\/[a-z]+[1-6]?\>/;
  // 准备两个栈
  var stack1 = [];
  // 这里声明stack2并且给他一个初始值, 防止最后stack2里面是空的
  var stack2 = [{'children': []}];

  while(index < templateStr.length) {
    rest = templateStr.substring(index);
    // console.log(rest);
    // console.log(templateStr[index]);
    // 识别遍历到的字符, 是不是一个开始标签
    if(startRegExp.test(rest)) {
      let tag = rest.match(startRegExp)[1];
      // 这里的attrsString是检测到的 attrs === class/id
      // 但是这个时候, 就有问题了, 因为有的标签会有attrs属性, 但是有的标签没有attrs属性
      let attrsString = rest.match(startRegExp)[2];
      console.log('检测开始标记', tag);
      console.log(rest.match(startRegExp));
      // 将开始标记推入栈中
      stack1.push(tag);
      // 将空数组推入栈2中
      // 并且如果说有attrs的话, 要分割为数组, 如果没有就是undefined
      stack2.push({'tag': tag, 'children': [], 'attrs': attrsString ? parseAttrsToString(attrsString) : attrsString});
      // 指针移动标签的长度加2, 为什么+2? 因为< >也占2位.
      index += attrsString ? (tag.length + 2 + attrsString.length) : tag.length + 2;
      // index += tag.length + 2 + attrsString.length;
    } else if(endRegExp.test(rest)) {
      let tag = rest.match(endRegExp)[1];
      // console.log('检测结束标记', tag);
      // 此时, tag一定是和栈顶部的是相同的
      let pop_tag = stack1.pop();
      if(tag == pop_tag) {
        let pop_arr = stack2.pop();
        if(stack2.length > 0) {
          stack2[stack2.length-1].children.push(pop_arr);
        }
      } else {
        throw new Error(stack1[stack1.length-1] + '标签没有封闭!!');
      }
      // 识别遍历到的字符, 是不是一个结束标签
      // 指针移动标签的长度+3, 因为</>占三位
      index += tag.length + 3;
    } else if (wordRegExp.test(rest)) {
      // 识别遍历到的字符, 是不是文字, 并不能全是空
      let word = rest.match(wordRegExp)[1];
      // 看word是不是全空
      if(!/^\s+$/.test(word)) {
        // 不是全是空
        // console.log('检测到文字', word);
        // 推入到stack2栈顶的数组元素
        stack2[stack2.length-1].children.push({'text': word, 'type': 3});
      }
      index += word.length;
    } else {
      index++;
    }
  }
  // 此时stack2就是我们之前默认放置的一项了, 此时要返回这一项的children项即可
  return stack2[0].children[0];
}
```

### parseAttrsToString.js

```js
export default function parseAttrsToString(attrsString) {
  if(attrsString == undefined) return [];

  // 遍历attrsString, 不能用split, 因为可能出现多个class的情况, 中间通过空格分开
  attrsString = attrsString.trim();
  // 当前是否在引号内
  var isInYinhao = false;
  // 断点
  var point = 0;
  // 结果数组
  var result = [];
  for(let i = 0; i < attrsString.length; i++) {
    let char = attrsString[i];
    if(char == '"') {
      isInYinhao = !isInYinhao;
    } else if(char == ' ' && !isInYinhao) {
      // 遇到了空格, 并且不再引号中
      if(!/^\s*$/.test(attrsString.substring(point, i))) {
        result.push(attrsString.substring(point, i).trim());
        point = i;
      }
    }
  }
  // 循环结束之后, 最后还剩一个属性k="v"
  result.push(attrsString.substring(point).trim());
  // 映射, 因为现在数组中的所有attrs都是存的字符串, 所以这里进行拆分
  // 拆分完毕之后是将 ["k=v", "k=v", "k=v"] 
  // 变为-> [{name: k, value: v}, {name: k, value: v}, {name: k, value: v}]的形式
  result = result.map(item => {
    // 根据等号进行拆分
    const o = item.match(/^(.+)="(.+)"$/);
    return {
      name: o[1],
      value: o[2]
    }
  })

  console.log(result);

  return [
    {name: 'class', value: '测试1'},
    {name: 'id', value: '测试2'}
  ]
}
```

# 5. 指令和生命周期

## 1. 介绍

- 用到了之前封装的响应式
- 自己创建了一个Vue.js, 主要操作在 Compiler里面

## 2. 文件介绍

```
index.js: 程序入口文件, 就是简单暴露了一下vue, 并且将vue设置为window变量
vue.js: 创建的Vue类, 主要进行初始化参数, 将数据变成响应式, 给实例添加data数据, 给数据添加watch, 模板编译, 生命周期函数
Compiler.js: 将模板进行编译, 主要用到了 Fragment, 最后将结点创建完毕之后再appendChild 到#app上
```

## index.js

```js
import Vue from './Vue';
window.Vue = Vue;
```

## Vue.js

```js
import Compile from './Compiler';
import { observe } from './data_reactive/observe';
import Watcher from './data_reactive/Watcher';

export default class Vue {
  constructor(options) {
    // 把参数 options 对象, 存为$options
    this.$options = options || {};
    // 数据
    this._data = options.data || undefined;
    observe(this._data);
    // 将默认数据要变为响应式的, 这里就是生命周期
    this._initData();
    // this._initComputed();
    // 调用默认的watch
    this._initWatch();
    // 模板编译
    new Compile(options.el, this);
    options.create();
  }
  // initData是为了将数据添加到实例中!!!!!!!!!!!!!!!!!!!!!!!!!
  // 意思就是可以直接通过vue.***直接访问属性!!!!!!!!
  _initData() {
    var self = this;
    Object.keys(this._data).forEach(key => {
      Object.defineProperty(self, key, {
        get() {
          return self._data[key];
        },
        set(newVal) {
          self._data[key] = newVal;
        }
      })
    })
  } 
  _initComputed() {

  };
  _initWatch() {
    var self = this;
    var watch = this.$options.watch;
    Object.keys(watch).forEach(key => {
      new Watcher(self, key, watch[key]);
    })
  }
} 
```

## Compiler.js

```js
import Watcher from './data_reactive/Watcher';

export default class Compile {
  constructor(el, vue) {
    // vue实例
    this.$vue = vue;
    // 挂载点
    this.$el = document.querySelector(el);

    // 如果用户传入的挂载点存在
    if(this.$el) {
      // 结点 到 碎片   2:to
      // 调用函数, 让结点变为fragment, 类似于mustache的tokens, 实际上是用的AST, 这里就是轻量级的 fragment
      let $fragment = this.node2Fragment(this.$el);
      // 编译
      this.compile($fragment);
      // 替换好了内容进行上树
      this.$el.appendChild($fragment);
    }
  }
  node2Fragment(el) {
    var fragment = document.createDocumentFragment();
    // console.log(fragment);
    var child;
    // 让所有的DOM节点, 都进入fragment
    while(child = el.firstChild) {
      fragment.appendChild(child);
    }
    return fragment;
  }
  compile(el) {
    // console.log(el);
    // 得到子元素
    var childNodes = el.childNodes;
    var self = this;
    
    var reg = /\{\{(.*)\}\}/;

    childNodes.forEach(node => {
      var text = node.textContent;
      if(node.nodeType == 1) {
        self.compileElement(node);
      } else if(node.nodeType == 3 && reg.test(text)) {
        let name = text.match(reg)[1];
        self.compileText(node, name);
      }
    })
  }
  compileElement(node) {
    // console.log(node);
    // 这里的方便之处不是将HTML的解构看成字符串, 而是真正的属性列表
    var nodeAttrs = node.attributes;
    var self = this;
    
    // 类数组对象变为数组
    Array.prototype.slice.call(nodeAttrs, 0).forEach(attr => {
      // 分析指令
      var attrName = attr.name;
      // 把v-后面的内容截取出来
      var dir = attrName.substring(2);
      // 指令值
      var value = attr.value;
      // 看看是不是v-***这种的指令
      if(attrName.indexOf('v-') == 0) {
        // v-开头的就是指令
        if(dir == 'model') {
          // model指令
          // console.log('发现了model指令', value);
          new Watcher(self.$vue, value, value => {
            node.value = value;
          });
          // 双向绑定中的值是如何传进去的? 就是这里
          // 但是这里只能保证,在 vm.c.m.n改变的时候输入框的值变化
          // 不会在输入框值变化的时候, vm.c.m.n跟着变
          var v = self.getVueVal(self.$vue, value);
          node.value = v;
          // 所以这里要添加监听, 当input 变化的时候, vm.c.m.n变
          node.addEventListener('input', e => {
            var newV = e.target.value;
            self.setVueVal(self.$vue, value, newV);
          })

        } else if (dir == 'for') {
          // console.log('发现了for指令', value);
        } else if (dir == 'if') {
          // console.log('发现了if指令', value);
        }
      }
    });
  }
  compileText(node, name) {
    // console.log(text);
    node.textContent = this.getVueVal(this.$vue, name);
    new Watcher(this.$vue, name, value => {
      console.log(value);
      node.textContent = value;
    })
  }
  getVueVal(vue, exp) {
    var val = vue;
    exp = exp.split('.');
    exp.forEach(key => {
      val = val[key];
    });
    return val;
  }
  setVueVal(vue, exp, value) {
    var val = vue;
    exp = exp.split('.');
    exp.forEach((k, i) => {
      if(i < exp.length - 1) {
        val = val[k];
      } else {
        val[k] = value;
      }
    });
  }
}
```







