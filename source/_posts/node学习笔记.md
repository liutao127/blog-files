---
title: node学习笔记
date: 2022-04-12 17:45:48
tags: node
---

# 1、fs读取文件

<!-- more -->

```js
const fs = require('fs');

fs.readFile('./files/11.txt', 'utf8', (err, dataStr) => {
  if(err){
    // 读取失败!ENOENT: no such file or directory, open 'F:\VSCode-Station\LearnNodeJS\day01\files\11.txt'
    return console.log('读取失败!' + err.message);
  }else{
    // 读取成功！我的名字叫做小饼干。
    return console.log('读取成功！' + dataStr);
  }
})

```

# 2、fs写文件

```js
const fs = require('fs');

// 1. 文件路径     2.写入的内容    3.可选：编码    4.回调函数
fs.writeFile('./files/2.txt', 'abcd', (err) => {
  if(err){
    return console.log('写入文件失败' + err.message);
  }
  console.log('写入成功~');  // 写入成功后err值为 null
})

```



# 3、路径问题

```js
const fs = require('fs');
const { resolve } = require('path')

/* 
  当通过其他路径下来执行本文件的时候，就会出现路径错误，
  因为这个文件的路径是动态路径，扫描的文件是通过执行文件的时候的路径来判断的
fs.readFile('./files/1.txt', 'utf8', (err, dataStr) => {
  if(err){
    return console.log('读取失败!' + err.message);
  }else{
    return console.log('读取成功！' + dataStr);
  }
})
 */

// 成功 ------》 files/1.txt前面不能加 /，但是可以是 ./
fs.readFile(resolve(__dirname, './files/1.txt'), 'utf8', (err, dataStr) => {
  console.log(__dirname);
  if(err){
    return console.log('读取失败!' + err.message);
  }else{
    return console.log('读取成功！' + dataStr);
  }
})

// 成功，-----》 /files/1.txt，前面必须加 /
// fs.readFile(__dirname + '/files/1.txt', 'utf8', (err, dataStr) => {
//   console.log(__dirname);
//   if(err){
//     return console.log('读取失败!' + err.message);
//   }else{
//     return console.log('读取成功！' + dataStr);
//   }
// })


// 成功 ---  移植性差，不利于维护
// fs.readFile('F:\\VSCode-Station\\LearnNodeJS\\day01\\files\\1.txt', 'utf8', (err, dataStr) => {
//   if(err){
//     return console.log('读取失败!' + err.message);
//   }else{
//     return console.log('读取成功！' + dataStr);
//   }
// })

```

# 4、path的 join、basename、extname方法的使用

```js
join

const path = require('path');
const fs = require('fs');

const pathStr = path.join('/a', '/b/c', '../', './d', '/e')
console.log(pathStr); // \a\b\d\e

// files/1.txt 前面可以啥都没，也可以 /，也可以 ./
fs.readFile(path.join(__dirname, '/files/1.txt'), 'utf8', (err, dataStr) => {
  console.log(__dirname);
  if(err){
    return console.log('读取失败!' + err.message);
  }else{
    return console.log('读取成功！' + dataStr);
  }
})



basename

const path = require('path');
const fpath = '/a/b/c/index.html';
const fullName = path.basename(fpath); 
console.log(fullName); // index.html
const fullName2 = path.basename(fpath, '.html');
console.log(fullName2); // index



extname

const path = require('path')
// 这是文件的存放路径
const fpath = '/a/b/c/index.html';
const extName = path.extname(fpath);
console.log(extName); // .html

```

# 5、http服务器

## 1、服务器的创建

```js
/* 
  1. 导入http模块
  2. 创建web服务器实例
  3. 为服务器实例绑定 request 事件，监听客户端的请求
  4. 启动服务器
*/

const http = require('http');

const server = http.createServer();

server.on('request', (req, res) => {
  // 解决中文乱码问题
  res.setHeader('Content-Type', 'text/html;charset=utf-8');
  const url = req.url;
  const method = req.method;
  const str = `请求地址url为${url}, 请求方法method为：${method}`;
  console.log(str);
  // 调用res.end()方法向客户端响应一些内容
  res.end(str)
})

server.listen(80, () => {
  console.log('服务器80端口号创建成功!');
});


```

## 2、根据不同url响应不同的html内容

```js
const http = require('http');
const server = http.createServer();
server.on('request', (req, res) => {
  const url = req.url;
  console.log(url);
  let content = '<h1>404 Not Found！</h1>';
    
  if(url === '/' || url === '/index.html'){
    content = '<h1>首页</h1>'
  }else if(url === '/about.html'){
    content = '<h1>关于</h1>'
  }
    
  res.setHeader('Content-Type', 'text/html;charset=utf-8');
  res.end(content)
})

server.listen(80, () => {
  console.log('80端口服务器请求成功！');
})
```

## 3、时钟案例

```js
const fs = require('fs');
const http = require('http');
const server = http.createServer();
const path = require('path');
server.on('request', (req, res) => {
  //  /clock/index.html   /clock/index.js   /clock/index.css
  const url = req.url;
  let fpath = '';
  if(url === '/') {
    fpath = path.join(__dirname, '/clock/index.html');
  }
  else {
    // 必须要 /clock
    // fpath = path.join(__dirname, '/clock/', url);
    fpath = path.join(__dirname, url);
  }

  console.log(fpath);
  fs.readFile(fpath, 'utf8', (err, dataStr) => {
    if(err) res.end('<h1>404 Not Found！</h1>');

    res.end(dataStr);

  })

})

server.listen(80, () => {
  console.log('80端口创建成功！');
})
```

## 4、格式化时间案例

**dataFormat.js**

```js
// 定义格式化时间的方法

function dataFormat(dtStr){
  const dt = new Date(dtStr);
  console.log(dt);

  const y = dt.getFullYear();
  const m = padZero(dt.getMonth() + 1);
  const d = padZero(dt.getDate());
  
  const hh = padZero(dt.getHours());
  const mm = padZero(dt.getMinutes());
  const ss = padZero(dt.getSeconds());

  return `${y}-${m}-${d} ${hh}:${mm}:${ss}`;

}

// 定义补0的函数
function padZero(n){
  return n > 9 ? n : '0' + n; 
}

module.exports = dataFormat;

```

**test.js**

```js
const TIME = require('./15.dataFormat');

const dt = new Date();
console.log(dt); // 2022-04-10T10:25:45.510Z

const newDt = TIME(dt);
console.log(newDt); // 2022-04-10 18:25:45
```

使用moment来格式化时间

```js
const moment = require('moment');

const dt = moment().format('YYYY-MM-DD HH-mm-ss');
console.log(dt);

```

# 6、模块引入机制以及笔记

## 1、引入多次模块

```
// 自会打印一次，模块在第一次加载后会被缓存。
// 这意味着，就算调用在多次也不会导致代码执行多行。
// 因为模块引入会优先从缓存中加载，从而提高代码的效率。
require('./03.自定义模块');
require('./03.自定义模块');
require('./03.自定义模块');
```

## 2、模块加载机制

```js
当用 require() 导入自定义模块的时候，
  必须以 ./ 或者 ../ 开头的路径标识符。
  如果没有加，那么node会把它当做内置模块或第三方模块进行加载，在node_modules中查找第三方模块
  如果没有找到对应的第三方模块，则移动到再上一层父目录汇总，进行加载，知道文件系统的根目录。
  例如   在： 
  F:\VSCode-Station\LearnNodeJS\day03\test.js中 require('tools')
  F:\VSCode-Station\LearnNodeJS\day03\node_modules\目标模块[tools]
  F:\VSCode-Station\LearnNodeJS\node_modules\tools
  F:\VSCode-Station\node_modules\tools
  F:\node_modules\tools


  当用 require() 导入自定义模块的时候，
  必须以 ./ 或者 ../ 开头的路径标识符。
  如果省略了文件的扩展名，
  那么 node.js 就会按照顺序分别尝试加载以下的文件：
  1. 按照确切的文件名去加载
  2. 补全.js扩展名进行加载
  3. 补全.json扩展名进行加载
  4. 补全.node扩展名进行加载
  5. 加载失败，终端报错


  当把目录作为模块标识符，传递给require()进行加载的时候，有三种加载方式:
  1. 在被加载的目录下查找一个叫做package.json 的文件，并寻找main属性，作为require()加载的入口
  2. 如果目录里没有package.json文件，或者main 入口不存在或无法解析，则 Node,js将会试图加载目录下的 index.js文件。
  3. 如果以上两步都失败了，则Node.is 会在终端打印错误消息，报告模块的缺失:Error. Cannot find module 'xxx'


```

# 7、express服务器



## 1、用express搭建最基本的服务器

```js
/* 
  内置模块： http创建服务器方法： 
  const http = require('http');
  const server = http.createServer();
  server.on('request', (req, res) => {

  });
  server.listen(80, () => {
    console.log('启动成功！')
  });
*/
const express = require('express');

const app = express();

app.get('/user', (req, res) => {
  res.send({name: 'zs', age: 20, sex: '男'});
})

app.post('/user', (req, res) => {
  res.send({name: 'ls', age: 2, sex: '女'});
})

app.get('/', (req, res) => {
  console.log(req.query);
  // req.query 可以获取客户端发送过来的 查询参数
  res.send(req.query);
  /* 
    http://localhost/?name=张三&age=2
    {
      "name": "张三",
      "age": "2"
    }
  */
})

// 这里的 :id 是一个动态的参数
app.get('/user/:id/:name', (req, res) => {
  console.log(req.params);
  // req.params 是动态匹配到的 URL 参数，默认也是一个空对象
  res.send(req.params);
  /*
    http://localhost/user/123/李四
    {
      "id": "123",
      "name": "李四"
    }
  */

})


app.listen(80, () => {
  console.log('80端口服务器创建成功！');
})

```

## 2、用express.static对外提供静态资源

```js
const express = require('express');

const app = express();

// 在这里， 调用express.static() 方法，快速提供对外提供静态资源
// 谁在前面 就 先展示谁
//  假如说 files里面没有 a.html， 但是clock里面有 a.html， 用户访问 a.html ，那么就会展示clock的 html
//  但是如果说files 里面有 a.html，而且 clock里面也有， 那么用户访问 a.html，会展示 fles中的 html
// app.use(express.static('./files'))
// app.use(express.static('./clock'))

//  /abc 为访问的前缀   http://localhost/abc/index.html 需要这样输入才可以访问到
app.use('/abc', express.static('./clock'));

app.listen('80', () => {
  console.log('80端口服务器创建成功！');
})

```

## 3、express中的路由Router

### 1、最简单的路由创建

```js
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send('hello world')
})

app.post('/', (req, res) => {
  res.send('post request')
})

app.listen(80, () => {

})

```

### 2、模块化路由

**Router.js**

```js
// 路由模块
const express = require('express');

const router = express.Router();

router.get('/user/list', (req, res) => {
  res.send('get user list');
})

router.get('/user/add', (req, res) => {
  res.send('get user add');
})

module.exports = router;

```

**testRouter.js**

```js
const express = require('express');

const app = express();

const router = require('./03.Router');
// app.use() 方法就是来注册中间件的

// 没有访问前缀  http://localhost/user/list
// app.use(router); 
app.use('/abc', router);  // http://localhost/abc/user/list

app.listen(80, () => {
  console.log('80端口服务器创建成功');
})

```

## 4、express中间件

### 1、体验中间件

```js
const express = require('express');
const moment = require('moment');

const app = express();

app.use((req, res, next) => {
  const time = moment().format('YYYY-MM-DD HH-mm-ss');
  // 获取到请求到达服务器的时间并把它挂在到中间件上，共享给后面的所有路由
  req.startTime = time;
  next();
})

app.get('/', (req, res) => {
  console.log('home被访问');
  res.send('home 页面' + req.startTime);
})

app.get('/user', (req, res) => {
  console.log('user被访问');
  res.send('user 页面' + req.startTime);
})

app.listen(80, () => {
  console.log('80端口服务器启动成功');
})

```

### 2、定义多个全局中间件

```js
const express = require('express');
const app = express();

app.use((req, res, next) => {
  console.log('第一个中间件');
  next();
})

app.use((req, res, next) => {
  console.log('第二个中间件');
  next();
})

app.get('/', (req, res) => {
  console.log('home被访问！');
  res.send('home 页面！')
})

app.listen(80, () => {
  console.log('80端口服务器启动成功');
})
```

### 3、局部生效的中间件(多个中间件)

```js
const express = require('express');
const app = express();

const mw1 = (req, res, next) => {
  console.log('经过了局部中间件mw1的处理');
  next();
}

const mw2 = (req, res, next) => {
  console.log('经过了局部中间件mw2的处理');
  next();
}

// 先经过 mw1， 在经过 mw2
// app.get('/', mw1, mw2, (req, res) => {
//   console.log('home 被访问');
//   res.send('home 页面')
// })

// 也可以用参数的方式来调用中间件
app.get('/', [mw1, mw2], (req, res) => {
  console.log('home 被访问');
  res.send('home 页面')
})

app.get('/user', (req, res) => {
  console.log('user 被访问');
  res.send('/user 页面')
})

app.listen(80, () => {
  console.log('80端口服务器启动成功！');
})
```

### 4、中间件注意事项

**了解中间件的5个使用注意事项**

1. 一定要在路由之前注册中间件

2. 客户端发送过来的请求，可以连续调用多个中间件进行处理

3. 执行完中间件的业务代码之后，不要忘记调用next()函数

4. 为了防止代码逻辑混乱，调用next()函数后不要再写额外的代码

5. 连续调用多个中间件时，多个中间件之间，共享req和res对象

## 5、错误级中间件

**演示代码**

```js
const express = require('express');
const app = express();

app.use('/', (req, res) => {
  // 1.1 认为的制造错误
  throw new Error('服务器内部发生错误！')
  res.send('home page.')  // 这行代码是没有执行的，因为服务器在发生错误之后，中间件会捕获到错误然后进行处理
})

// 注意！！  错误中间件一定要定义在 所有中间件之后
// 定义错误级别的中间件，捕获整个项目的异常错误，从而防止程序的崩溃
app.use((err, req, res, next) => {
  console.log('发生了错误' + err.message);
  res.send('Error: ' + err.message);
})

app.listen(80, () => {
  console.log('80端口服务器启动成功！');
})

```

## 6、内置中间件

**演示代码**

```js
const express = require('express');
const app = express();

// 除了错误级别的中间件，其他的中间件，必须在路由之前进行配置
// 解析表单中 JSON 格式的数据
app.use(express.json());
// 解析表单中的 url-encoded 格式的数据
app.use(express.urlencoded({extended: false}));

app.get('/', (req, res) => {
  console.log(req.body);
  res.send('success !')
})

app.listen(80, () => {
  console.log('80端口服务器启动成功');
})
```

## 7、body-parser中间件的使用

```js
const express = require('express');
const app = express();

// 导入需要解析表单数据的中间件 body-parser
const parser = require('body-parser');

app.use(parser.urlencoded({extended: false}));

app.post('/', (req, res) => {
  console.log(req.body);
  res.send('success !')
})

app.listen(80, () => {
  console.log('80端口服务器启动成功');
})

```

## 8、自定义解析表单的中间件

```js
const express = require('express');
const app = express();
// nodejs内置的 querystring模块
const qs = require('querystring');

/* 
  在中间件中，需要监听req对象的data事件，来获取客户端发送到服务器的数据。
  如果数据量比较大，无法一次性发送完毕，则客户端会把数据切割后，分批发送到服务器。
  所以data事件可能会触发多次，每一次触发data事件时，获取到数据只是完整数据的一部分，需要手动对接收到的数据进行拼接。
*/

// 解析表单数据的中间件
app.use((req, res, next) => {
  // 定义中间件具体的业务逻辑
  // 定义一个 str 字符串，专门用来存储客户端发送过来的请求体数据
  let str = '';
  req.on('data', (chunk) => {
    str += chunk;
  })
  // 监听req的end事件
  req.on('end', () => {
    // console.log(str);
    const body = qs.parse(str)
    console.log(body);
    req.body = body
    next();
  })
})

app.post('/user', (req, res) => {
  res.send(req.body);
})


app.listen(80, () => {
  console.log('80端口服务器启动成功');
})

```



## 9、express写接口

```js
const express = require('express');
const app = express();
const router = require('./16.apiRouter');
const cors = require('cors');

app.use(express.urlencoded({extended: false}));

// 必须在配置cors中间件之前，去配置JSONP的接口
app.get('/api/jsonp', (req, res) => {
  // 定义 JSONP 接口的具体实现过程
  // 1. 得到函数的名称
  const callName = req.query.callback;
  // 2. 定义要发送到客户端的数据对象
  const data = {name: 'zs', age: 22};
  // 3. 拼接处一个函数的调用
  const scriptStr = `${callName}(${JSON.stringify(data)})`
  // 4. 把拼接的字符串，响应给客户端
  res.send(scriptStr);
})


/* 
  CORS(Cross-Origin Resource Sharing，跨域资源共享）由一系列HTTP响应头组成，这些HTTP响应头决定 浏览器是否阻止前端 JS 代码跨域获取资源。
  浏览器的同源安全策略默认会阻止网页“跨域”获取资源。但如果接口服务器配置了CORS相关的 HTTP响应头，就可以解除浏览器端的跨域访问限制。
*/
// 使用 cors 中间件，来解决跨域问题
app.use(cors());

app.use('/api', router);

app.listen(80, () => {
  console.log('80端口服务器启动成功！');
})

```

# 8、cors笔记

```
#CORS概念： 
CORS(Cross-Origin Resource Sharing，跨域资源共享）由一系列HTTP响应头组成，这些HTTP响应头决定 浏览器是否阻止前端 JS 代码跨域获取资源。
浏览器的同源安全策略默认会阻止网页“跨域”获取资源。但如果接口服务器配置了CORS相关的 HTTP响应头，就可以解除浏览器端的跨域访问限制。

#CORS注意事项
CORS主要在服务器端进行配置。客户端浏览器无须做任何额外的配置，即可请求开启了CORS的接口。
CORS在浏览器中有兼容性。只有支持XMLHttpRequest Level2的浏览器，才能正常访问开启了CORS的服务端接口(例如:IE10+、Chrome4+、FireFox3.5+)。

#CORS请求的分类
客户端在请求CORS接口时，根据请求方式和请求头的不同，可以将CORS的请求分为两大类，分别是:
1. 简单请求
2. 预检请求

简单请求
同时满足以下两大条件的请求，就属于简单请求:
请求方式:GET、POST、HEAD三者之一
HTTP头部信息不超过以下几种字段： 无自定义头部字段、Accept、Accept-Language、Content-Language、DPR、Downlink、Save-Data、Viewport-Width、Width 、Content-Type (只有三个值application/x-www-form-urlencoded、multipart/form-data、text/plain)

预检请求
只要符合以下任何一个条件的请求，都需要进行预检请求:
1. 请求方式为GET、POST、HEAD之外的请求Method类型
2. 请求头中包含自定义头部字段
3. 向服务器发送了application/json格式的数据
在浏览器与服务器正式通信之前，浏览器会先发送OPTION请求进行预检，以获知服务器是否允许该实际请求，所以这一次的OPTION请求称为“预检请求”。服务器成功响应预检请求后，才会发送真正的请求并且携带真实数据。

简单请求和预检请求的区别
简单请求的特点:客户端与服务器之间只会发生一次请求。
预检请求的特点:客户端与服务器之间会发生两次请求，OPTION预检请求成功之后，才会发起真正的请求。

```

# 9、JSONP笔记

```js
# JSONP概念
概念:浏览器端通过<script>标签的src属性，请求服务器上的数据，同时，服务器返回一个函数的调用。这种请求数据的方式叫做JSONP。
# 特点
JSONP不属于真正的Ajax请求，因为它没有使用XMLHttpRequest这个对象。
JSONP仅支持GET请求，不支持POST、PUT、DELETE等请求。
# 创建JSONP接口的注意事项
如果项目中已经配置了CORS 跨域资源共享，为了防止冲突，必须在配置CORS中间件之前声明JSONP 的接口。
否则JSONP接口会被处理成开启了CORS的接口。
```

# 10、apiRouter

```js
const express = require('express');
const router = express.Router();

router.get('/get', (req, res) => {
  // console.log(req);
  // 通过 req.query 获取客户端通过查询字符串，发送到服务器的数据
  const query = req.query;
  // 调用res.send 方法
  res.send({
    status: 0,  // 0->处理成功， 1->处理失败
    msg: 'GET 请求成功！', //状态描述
    data: query // 需要响应给客户端的数据
  })
});

router.post('/post', (req, res) => {
  // 通过req.body 获取请求体中包含 url-encoded 格式的数据
  const body = req.body;
  // 通过res.send 方法，向客户端响应结果
  res.send({
    status: 0,
    msg: 'POST 请求成功！',
    data: body
  })
})

router.delete('/delete', (req, res) => {
  res.send({
    status: 0,
    msg: 'DELETE 请求成功！',
  })
})


module.exports = router;

```

## 13、测试跨域

```html
<body>

  <button id="btnGET">GET</button>
  <button id="btnPOST">POST</button>
  <button id="btnDELETE">DELETE</button>
  <button id="btnJSONP">JSONP</button>

  <script>
    $(function () {

      // 测试GET接口
      $('#btnGET').on('click', function() {
        $.ajax({
          type: 'GET',
          url: 'http://127.0.0.1/api/get',
          data: {name: 'zs', age: 12},
          success: function (res) {
            console.log(res);
          }
        })
      })

      // 测试POST接口
      $('#btnPOST').on('click', function(){
        $.ajax({
          type: 'POST',
          url: 'http://127.0.0.1/api/post',
          data: {name: 'ls', age: 20},
          success: function (res) {
            console.log(res);
          }
        })
      })

      // 测试 DELETE接口
      $("#btnDELETE").on('click', function(){
        $.ajax({
          type: 'DELETE',
          url: 'http://127.0.0.1/api/DELETE',
          success: function(res) {
            console.log(res);
          }
        })
      })

      // 测试 JSONP接口
      $("#btnJSONP").on('click', function(){
        $.ajax({
          type: 'GET',
          url: 'http://127.0.0.1/api/JSONP',
          dataType: 'jsonp',
          success: function(res){
            console.log(res);
          }
        })
      })

    })
  </script>
</body>
```



# 11、操作数据库

```js
// 1. 导入mysql模块
const mysql = require('mysql');
// 2. 建立与 mysql 数据库的连接关系
const db = mysql.createPool({
  host: '127.0.0.1',
  user: 'root',
  password: '123456',
  database: 'test'  // 指定哪个数据库
})

// 测试 mysql 模块能否正常工作
/* db.query('select 1', (err, results) => {
  if(err){
    return console.log(err.message);
  }
  console.log(results);
}) */

// 查询users 表中的所有数据
/* const sqlStr = "select * from root"
db.query(sqlStr, (err, results) => {
  if(err) return console.log(err.message);
  // 注意：如果执行的是 select 查询语句，则执行的结果是数组
  console.log(results);
}) */

// 向roor 表中，新增一条数据
/* const info = {rid: '123', rpwd: '123'}
const sqlStr = "insert into root values(?, ?)";
db.query(sqlStr, [info.rid, info.rpwd], (err, results) => {
  if(err) return console.log(err.message);
  // 如果执行的是 insert 语句，则results 是一个对象
  // 其中有一个属性为 affectedRows 属性，来判断是否插入成功！
  if(results.affectedRows === 1){
    console.log('插入数据成功！');
  }
}) */


// 快速插入数据的方式
/* const info = {rid: '124', rpwd: '124'}
const sqlStr = "insert into root set ?";
db.query(sqlStr, info, (err, results) => {
  if(err) return console.log(err.message);
  if(results.affectedRows === 1){
    console.log('插入成功！');
  }
})
 */

// 更新用户的信息
/* const info = {rid: '123', rpwd: '123123'}
const sqlStr = "update root set rpwd = ? where rid = ?";
db.query(sqlStr, [info.rpwd, info.rid], (err, results) => {
  if(err) return console.log(err.message);
  if(results.affectedRows === 1){
    console.log('更新成功');
  }
})
 */

// 快速更新用户信息
/* const info = {rid: '123', rpwd: '123111'}
const sqlStr = "update root set ? where rid = ?";
db.query(sqlStr, [info, info.rid], (err, results) => {
  if(err) return console.log(err.message);
  if(results.affectedRows === 1) {
    console.log('更新成功');
  }
})
 */


// 删除id为 1 的用户
/* const sqlStr = "delete from root where rid = ?";
db.query(sqlStr, 1, (err, results) => {
  if(err) return console.log(err.message);
  if(results.affectedRows === 1){
    console.log('删除成功');
  }
})
 */


/* 
标记删除
  使用DELETE语句，会把真正的把数据从表中删除掉。
  为了保险起见，推荐使用标记删除的形式，来模拟删除的动作。
  所谓的标记删除，就是在表中设置类似于status这样的状态字段，来标记当前这条数据是否被删除。
  当用户执行了删除的动作时，我们并没有执行DELETE语句把数据删除掉，
  而是执行了UPDATE语句，将这条数据对应的status字段标记为删除即可。
  
  说白了，就是在数据库创建的同时，创建一列用来标记这条数据有没有被删除，如果想要删除这条数据那么就修改这列数据为 删除的标记【0为没删除，1为删除了】。
*/

```

# 12、web开发模式

```js
# 1、Web开发模式
目前主流的Web开发模式有两种，分别是:
基于服务端渲染的传统Web开发模式
基于前后端分离的新型Web开发模式

# 2、服务端渲染的Web开发模式
服务端渲染的概念:服务器发送给客户端的HTML页面，是在服务器通过字符串的拼接，动态生成的。
因此，客户端不需要使用Ajax这样的技术额外请求页面的数据。
## 优点
1. 前端耗时少。因为服务器端负责动态生成 HTML内容，浏览器只需要直接渲染页面即可。尤其是移动端，更省电。
2. 有利于SEO。因为服务器端响应的是完整的HTML页面内容，所以爬虫更容易爬取获得信息，更有利于SEO.。
## 缺点
1. 占用服务器端资源。即服务器端完成HTML页面内容的拼接，如果请求较多，会对服务器造成一定的访问压力。
2. 不利于前后端分离，开发效率低。使用服务器端渲染，则无法进行分工合作，尤其对于前端复杂度高的项目，不利于项目高效开发。

# 3、前后端分离的Web开发模式
1. 前后端分离的概念:前后端分离的开发模式，依赖于Ajax技术的广泛应用。
2. 简而言之，前后端分离的Web开发模式，就是后端只负责提供API接口，前端使用Ajax调用接口的开发模式。
## 优点
1. 开发体验好。前端专注于UI页面的开发，后端专注于api的开发，且前端有更多的选择性.
2. 用户体验好。Ajax技术的广泛应用，极大的提高了用户的体验，可以轻松实现页面的局部刷新。
3. 减轻了服务器端的渲染压力。因为页面最终是在每个用户的浏览器中生成的。
## 缺点
1. 不利于SEO。因为完整的HTML页面需要在客户端动态拼接完成，
所以爬虫对无法爬取页面的有效信息。(解决方案:利用Vue、React等前端框架的SSR(server side render）技术能够很好的解决SEO问题!)

# 4、如何选择Web开发模式
不谈业务场景而盲目选择使用何种开发模式都是耍流氓。
比如
1. 企业级网站，主要功能是展示而没有复杂的交互，并且需要良好的SEO，则这时我们就需要使用服务器端渲染;
2. 而类似后台管理项目，交互性比较强，不需要考虑SEO，那么就可以使用前后端分离的开发模式。
3. 另外，具体使用何种开发模式并不是绝对的，为了同时兼顾了首页的渲染速度和前后端分离的开发效率，一些网站采用了首屏服务器端渲染＋其他页面前后端分离的开发模式。

```



# 13、身份认证



## 1、什么是身份认证

身份认证(Authentication)又称“身份验证”、“鉴权”，是指通过一定的手段，完成对用户身份的确认。
日常生活中

的身份认证随处可见，例如:高铁的验票乘车，手机的密码或指纹解锁，支付宝或微信的支付密码等。
在Web开发中，也涉及到用户身份的认证，例如:各大网站的手机验证码登录、邮箱密码登录、二维码登录等。



## 2、为什么要身份认证

身份认证的目的，是为了确认当前所声称为某种身份的用户，确实是所声称的用户。



## 3、不同开发模式下的身份认证

对于**服务端渲染**和**前后端分离**这两种开发模式来说，分别有着不同的身份认证方案:
服务端渲染推荐使用Session认证机制
前后端分离推荐使用JWT认证机制



# 14、Session认证机制

## 1、HTTP协议的无状态性

   了解HTTP协议的无状态性是进一步学习Session认证机制的必要前提。
   HTTP协议的无状态性，指的是客户端的每次 HTTP请求都是独立的，连续多个请求之间没有直接的关系，服务器不会主动保留每次HTTP请求的状态。

## 2、如果突破HTTP的无状态性

​	**通过cookie**

## 3、什么是cookie

​	Cookie是存储在用户浏览器中的一段不超过4KB的字符串。它由一个名称(Name)、一个值 (Value)和其它几个用于控制Cookie有效期、安全性、使用范围的可选属性组成。

​	不同域名下的Cookie 各自独立，每当客户端发起请求时，会自动把当前域名下所有未过期的Cookie一同发送到服务器。

## 4、Cookie的几大特性

​	自动发送
​	域名独立
​	过期时限
​	4KB限制

## 5、Cookie在身份认证中的作用

​	客户端第一次请求服务器的时候，服务器通过**响应头的形式**，向客户端发送一个身份认证的Cookie，客户端会自动将Cookie保存在浏览器中。
​	随后，当客户端浏览器每次请求服务器的时候，浏览器会**自动**将身份认证相关的Cookie，**通过请求头的形式**发送给服务器，服务器即可验明客户端的身份。

## 6、Cookie不具有安全性

​	由于Cookie 是存储在浏览器中的，而且浏览器也提供了读写Cookie的API，因此 Cookie很容易被伪造，不具有安全性。因此不建议服务器将重要的隐私数据，通过Cookie的形式发送给浏览器。

## 7、提高身份认证的安全性

​	通过服务器端进行cookie的认证，是否此cookie真正存在。

## 8、Session局限性

​	Session认证机制需要配合Cookie 才能实现。由于Cookie默认不支持跨域访问，所以，当涉及到前端跨域请求后端接口的时候，需要做很多额外的配置，才能实现跨域Session认证。

## 9、注意

当前端请求后端接口不存在跨域问题的时候，推荐使用Session身份认证机制。
当前端需要跨域请求后端接口的时候，不推荐使用Session身份认证机制，推荐使用JWT认证机制。



# 15、JWT [ JSON Web Token ] 认证机制



## 1、什么是JWT认证机制

​	JWT(英文全称:JSON Web Token）是目前最流行的跨域认证解决方案。

## 2、JWT的组成部分

​	JWT通常由三部分组成，分别是Header (头部)、Payload(有效荷载)、Signature(签名)

​	三者之间通过英文 " . " 分隔。

```js
header.Payload.Signature
```

## 3、JWT的三个部分各自代表的含义

Payload部分才是真正的用户信息，它是用户信息经过加密之后生成的字符串。

Header和Signature是安全性相关的部分，只是为了保证Token 的安全性。

## 4、JWT的使用方式

客户端收到服务器返回的JWT之后，通常会将它储存在localStorage或 sessionStorage 中。

此后，客户端每次与服务器通信，都要带上这个WT的字符串，从而进行身份认证。推荐的做法是把JWT放在 HTTP请求头的Authorization字段中，格式如下:

```
Authorization: Bearer <token>
```

# 16、express + session案例

```html
index.html
<body>
  <h1>首页</h1>

  <button id="btnLogout">退出登录</button>

  <script>
    $(function () {

      // 页面加载完成后，自动发起请求，获取用户姓名
      $.get('/api/username', function (res) {
        // status 为 0 表示获取用户名称成功；否则表示获取用户名称失败！
        console.log(res.data);
        if (res.status !== 0) {
          alert('您尚未登录，请登录后再执行此操作！')
          location.href = './login.html'
        } else {
          alert('欢迎您：' + res.username)
        }
      })

      // 点击按钮退出登录
      $('#btnLogout').on('click', function () {
        // 发起 POST 请求，退出登录
        $.post('/api/logout', function (res) {
          if (res.status === 0) {
            // 如果 status 为 0，则表示退出成功，重新跳转到登录页面
            location.href = './login.html'
          }
        })
      })
    })
  </script>
</body>
```

```html
login.html
<body>
  <!-- 登录表单 -->
  <form id="form1">
    <div>账号：<input type="text" name="username" autocomplete="off" /></div>
    <div>密码：<input type="password" name="password" /></div>
    <button>登录</button>
  </form>

  <script>
    $(function () {
      // 监听表单的提交事件
      $('#form1').on('submit', function (e) {
        // 阻止默认提交行为
        e.preventDefault()
        // 发起 POST 登录请求
        $.post('/api/login', $(this).serialize(), function (res) {
          // status 为 0 表示登录成功；否则表示登录失败！
          if (res.status === 0) {
            location.href = './index.html'
          } else {
            alert('登录失败！')
          }
        })
      })
    })
  </script>
</body>
```

```js
// 导入 express 模块
const express = require('express')
// 创建 express 的服务器实例
const app = express()

// TODO_01：请配置 Session 中间件
const session = require('express-session')
app.use(
  session({
    secret: 'itheima',
    resave: false,
    saveUninitialized: true,
  })
)

// 托管静态页面
app.use(express.static('./pages'))
// 解析 POST 提交过来的表单数据
app.use(express.urlencoded({ extended: false }))

// 登录的 API 接口
app.post('/api/login', (req, res) => {
  // 判断用户提交的登录信息是否正确
  if (req.body.username !== 'admin' || req.body.password !== '000000') {
    return res.send({ status: 1, msg: '登录失败' })
  }

  // TODO_02：请将登录成功后的用户信息，保存到 Session 中
  // 注意：只有成功配置了 express-session 这个中间件之后，才能够通过 req 点出来 session 这个属性
  req.session.user = req.body // 用户的信息
  req.session.islogin = true // 用户的登录状态

  res.send({ status: 0, msg: '登录成功' })
})

// 获取用户姓名的接口
app.get('/api/username', (req, res) => {
  // TODO_03：请从 Session 中获取用户的名称，响应给客户端
  if(!req.session.islogin){
    return res.send({status: 1, msg: 'fail'});
  }
  res.send({
    status: 0,
    msg: 'success',
    username: req.session.user.username,
    data: require('./temp.json')
  })
})

// 退出登录的接口
app.post('/api/logout', (req, res) => {
  // TODO_04：清空 Session 信息
  req.session.destroy();
  res.send({status: 0, msg: '退出登录成功'})
})

// 调用 app.listen 方法，指定端口号并启动web服务器
app.listen(80, function () {
  console.log('Express server running at http://127.0.0.1:80')
})
```

# 17、express + jwt案例

```js
// 导入 express 模块
const express = require('express')
// 创建 express 的服务器实例
const app = express()

// TODO_01：安装并导入 JWT 相关的两个包，分别是 jsonwebtoken 和 express-jwt
const jwt = require('jsonwebtoken')
const expressJWT = require('express-jwt')

// 允许跨域资源共享
const cors = require('cors')
app.use(cors())

// 解析 post 表单数据的中间件
const bodyParser = require('body-parser')
app.use(bodyParser.urlencoded({ extended: false }))

// TODO_02：定义 secret 密钥，建议将密钥命名为 secretKey
const cecretKey = 'itheima No1 ^_^'

// TODO_04：注册将 JWT 字符串解析还原成 JSON 对象的中间件
// 注意： 只要配置成功了  express-jwt 中间件，就可以把解析出来的用户信息，挂载到 req.user 属性上
app.use(expressJWT({secret: cecretKey, algorithms: ['HS256']}).unless({path: [/^\/api\//]}))

// 登录接口
app.post('/api/login', function (req, res) {
  // 将 req.body 请求体中的数据，转存为 userinfo 常量
  const userinfo = req.body
  // 登录失败
  if (userinfo.username !== 'admin' || userinfo.password !== '000000') {
    return res.send({
      status: 400,
      message: '登录失败！'
    })
  }
  // 登录成功
  // TODO_03：在登录成功之后，调用 jwt.sign() 方法生成 JWT 字符串。并通过 token 属性发送给客户端
  // 参数 1：用户的信息对象    参数 2：加密的密钥     参数 3：配置对象，可以配置当前token有效期
  // 千万不能把密码加密到token中
  const tokenStr = jwt.sign({username: userinfo.username}, cecretKey, {expiresIn: '30s'})

  res.send({
    status: 200,
    message: '登录成功！',
    token: tokenStr // 要发送给客户端的 token 字符串
  })
})

// 这是一个有权限的 API 接口
app.get('/admin/getinfo', function (req, res) {
  // TODO_05：使用 req.user 获取用户信息，并使用 data 属性将用户信息发送给客户端
  console.log(req.user);
  res.send({
    status: 200,
    message: '获取用户信息成功！',
    data: req.user // 要发送给客户端的用户信息
  })
})

// TODO_06：使用全局错误处理中间件，捕获解析 JWT 失败后产生的错误
app.use((err, req, res, next) => {
  // 这次错误是由 token解析失败导致的
  if(err.name === 'UnauthorizedError'){
    return res.send({
      status: 401,
      message: '无效的token'
    })
  }
  res.send({
    status: 500,
    message: '未知的错误'
  })
})

// 调用 app.listen 方法，指定端口号并启动web服务器
app.listen(8888, function () {
  console.log('Express server running at http://127.0.0.1:8888')
})

```



# 18、Node中，JS对象和JSON文件相互转换问题



## JSON文件转换为JS对象

```js
// test.json
{
  "name": "张三",
  "age": 22
}

// test.js
const test = require('./test.json')
console.log(test)

$ node test
{ name: '张三', age: 22 }

```

## JS对象转换为JSON文件

```js
const fs = require('fs')
 
// 待转换的对象
const lisi = {
  name: '李四',
  age: 25,
  friends: ['张三', '王五', '赵六']
}
 
fs.writeFileSync('lisi.json', JSON.stringify(lisi))


// lisi.json
{"name":"李四","age":25,"friends":["张三","王五","赵六"]}
```

# 19、自己搭建接口传送数据

```html
index.html

</head>
<body>
  <script>
    $(function(){
      $.get('http://127.0.0.1/api/data', (res) => {
        console.log(res);
      })
    })

  </script>
</body>

```

```js
server.js

const express = require('express');
const cors = require('cors');
const app = express();
const data = require('./test.json');

app.use(cors());

app.get('/api/data', (req, res) => {
  res.send({
    status: 0,
    msg: 'success',
    data: data
  })
})


app.listen(80, () => {
  console.log('80端口服务器启动成功');
})
```





