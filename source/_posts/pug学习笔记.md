---
title: pug学习笔记
date: 2022-11-29 19:21:45
tags: pug
---

# 1. 基本使用流程

<!-- more -->

```js
// 1. 启动 express, pug 
// 2. 设置express的视图引擎为pug, 代码如下: 
	app.set('view engine', 'pug');
	app.set('views', './views');
// 3. 通过设置中间件来设置页面的渲染与传参, 代码如下
	app.get('/user', (req, res) => {
      const options = {
        name: '刘涛',
        age: 22
      }
      res.render('index', options);
    })
```

## 我的小案例

**script.js**

```js
console.log('hello');
console.log(name, age);
```

**style.css**

```css
.col {
  background-color: orange;
}
```

**layouy.pug  :  总模板**

```pug
doctype html
html(lang="en")
  head
    title Home
    link(rel="stylesheet" href="/lib/bootstrap/css/bootstrap.min.css")
    script(src="/lib/bootstrap/js/bootstrap.min.js")
    style
      include ./style.css
  body 
    block content
    br
    footer
    hr
    p Copyright &copy; 2022
```

**index.pug**

```pug
extends layout
block content 
  h1 #{name}
  p 我是home
```

**list.pug**

```pug
extends layout 
block content 
  p 我是list
  ul  
    each art, index in list
      li= art.title + ': ' + index
```





# 2. 常用方法

```js
const pug = require('pug');
 
// pug.compile(source, ?options)
// 把一个 Pug 模板编译成一个可多次使用、可传入不同局部变量渲染的函数。
const fn = pug.compile('div of pug');
console.log(fn()); // <div>of pug</div>


// pug.compileFile(path, ?options) 
// 从文件中读取一个 Pug 模板，并编译成一个可多次使用、可传入不同局部变量渲染的函数。
const compiledFunction = pug.compileFile('./template.pug');
console.log(compiledFunction({name: '刘涛'})); // <p>刘涛的pug代码!</p>
console.log(compiledFunction({name: '王甜甜'})); // <p>王甜甜的pug代码!</p>
 

// pug.compileClient(source, ?options)
// 把一个 Pug 模板编译成一份 JavaScript 代码字符串，它可以直接用在浏览器上而不需要 Pug 的运行时库。
const fn = pug.compileClient('string of pug');
const html = fn;
console.log(html); // 字符串


// pug.compileClientWithDependenciesTracked(source, ?options)
// 与 compileClient 相似，但这个函数返回的是这样一个结构： 
// {
//   'body': 'function (locals) {...}',
//   'dependencies': ['filename.pug']
// }
// 您应该在需要 dependencies 来实现一些诸如“监视 Pug 文件变动”的功能的时候，才使用该函数。


// pug.compileFileClient(path, ?options)
// 从文件中读取一个 Pug 模板并编译成一份 JavaScript 代码字符串，它可以直接用在浏览器上而不需要 Pug 的运行时库。
// 要确保已经把 Pug 的运行时库（node_modules/pug/runtime.js）传给了浏览器，从而让浏览器能够顺利执行刚才编译出来的函数。
const jsFun = pug.compileFileClient('./template.pug', {name: '刘涛'});
console.log(jsFun); // 字符串


// pug.render(source, ?options, ?callback)
const html = pug.render('string of pug');
console.log(html); // <string>of pug</string>


// pug.renderFile(path, ?options, ?callback)
const html = pug.renderFile('./template.pug', {name: '刘涛'});
console.log(html); // <p>刘涛的pug代码!</p>


// pug.filters 
// 这是一个存放自定义过滤器的哈希表。
// 这个对象和 filters 选项有着同样的语义，但它是全局地应用在所有 Pug 编译过程的。
// 当一个过滤器名称同时出现在了 pug.filters 和 options.filters 里，本 filters 选项将被优先选择。


/* 
  options的选项 - 所有的 API 方法都可以使用以下的选项：

  filename: string
    要编译的代码的文件名。用于异常信息以及（使用相对路径的）包含（include）和扩展（extends）操作。默认值是 'Pug'。
  basedir: string
    模板里所有绝对定位的根目录。
  doctype: string
    如果 doctype 没有出现模板里（比如模板只渲染一个 HTML 片段），那么您可以在这里指定它。在一些需要控制自闭合标签和布尔值属性的代码样式的时候非常有用。您可以阅读 doctype 的说明来了解更多细节。
  pretty: boolean | string
    （不赞成使用）在输出的 HTML 里添加 '  ' 这样的空格缩进来获得更好的代码可读性。如果这里指定了一个字符串（比如 '\t'），那么将会使用它作为控制缩进的字符。我们强烈建议您不要使用这个选项，它改变解释器和空格渲染工作的方式会极其频繁地导致一些错误。我们将会在未来移除这个功能。默认为 false。
  filters: object
    存放自定义过滤器的哈希表。默认为 undefined。
  self: boolean
    是否使用一个叫做 self 的命名空间来存放局部变量。这可以加速编译的过程，但是，相对于原来书写比如 variable 来访问局部变量，您将需要改为 self.variable 来访问它们。默认为 false。
  debug: boolean
    当设置为 true 时，编译产生的函数代码会被记录到标准输出。
  compileDebug: boolean
    当设置为 true 时，源代码会被包含在编译出来的模板函数中，用于提供更详实的错误信息（这在开发时会有用）。它默认是启用的，除非是在 Express 的生产环境模式中。
  globals: Array<string>
    该数组用于向模板中添加全局对象的名字，编译器将保证它们不被局部作用域影响。
  cache: boolean
    当设置为 true 时，编译出来的函数会被缓存下来。此时必须填写 filename 选项作为缓存的索引字段。该选项仅用于 render 函数。默认为 false。
  inlineRuntimeFunctions: boolean
    相对于使用 require 来获得公用的运行时函数，是否需要直接嵌入这些运行时函数。在 compileClient 函数里默认是 true，因此就不需要再手动包含那些函数（从而让其能在浏览器上运行）。在其他的 compile / render 系列函数中，默认是 false。
  name: string
    模板函数的名称。仅用于 compileClient 函数。默认值是 'template'。

*/

```

