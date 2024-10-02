---
title: React学习笔记
date: 2022-06-09 10:37:05
tags: React
---

# 1.Hello React

<!-- more -->

```html
<body>
  <!-- 准备好一个“容器” -->
  <div id="test"></div>
  <!-- 引入react核心库 -->
  <script src="../js/react.development.js"></script>
  <!-- 引入react-dom. 用于支持react操作DOM -->
  <script src="../js/react-dom.development.js"></script>
  <!-- 引入babel，用于将jsx转为js -->
  <script src="../js/babel.min.js"></script>
  
  <script type="text/babel"> 
      // 此处一定要写babel   我写的是jsx  让 bable帮我翻译
      // 1. 创建虚拟DOM
      const VDOM = <h1>Hello,React</h1>  // 这里一定不要写单引号，因为不是字符串
      // 2. 渲染虚拟DOM到页面
      // ReactDOM.render(虚拟DOM，容器)
      ReactDOM.render(VDOM, document.getElementById('test'))
  </script>
</body>
```

# 2.虚拟DOM创建的两种方式以及虚拟DOM和真实DOM的区别和关系

## 虚拟DOM创建的两种方式

### 使用jsx创建虚拟DOM

```jsx
<body>
  <!-- 准备好一个“容器” -->
  <div id="test"></div>
  <!-- 引入react核心哭 -->
  <script src="../js/react.development.js"></script>
  <!-- 引入react-dom. 用于支持react操作DOM -->
  <script src="../js/react-dom.development.js"></script>
  <!-- 引入babel，用于将jsx转为js -->
  <script src="../js/babel.min.js"></script>
  
  <script type="text/babel"> 
      // 此处一定要写babel   我写的是jsx  让 bable帮我翻译
      // 1. 创建虚拟DOM
      const VDOM = (
        <h1 id="title">
          <span>Hello,React</span>
        </h1>
      )  // 这里一定不要写单引号，因为不是字符串
      // 2. 渲染虚拟DOM到页面
      // ReactDOM.render(虚拟DOM，容器)
      ReactDOM.render(VDOM, document.getElementById('test'))
  </script>
</body>
```

### 使用js创建虚拟DOM

```html
<body>
  <!-- 准备好一个“容器” -->
  <div id="test"></div>
  <!-- 引入react核心哭 -->
  <script src="../js/react.development.js"></script>
  <!-- 引入react-dom. 用于支持react操作DOM -->
  <script src="../js/react-dom.development.js"></script>
  
  <script type="text/javascript"> 
  	// 1. 创建虚拟DOM, 这里的creatElement， 在document中是创建真实DOM，在此处是虚拟DOM
    // const VDOM = React.creatElement(标签名，标签属性，标签内容)
    // const VDOM = React.createElement('h1', {id: 'title'}, 'hello,React')
    const VDOM = React.createElement('h1', {id: 'title'}, React.createElement('span', {name: 'span'}, 'Hello,React'))
  	// 2. 渲染虚拟DOM到页面
  	ReactDOM.render(VDOM, document.getElementById('test'))
  </script>
</body>    
```

## 虚拟DOM和真实DOM

```js
  <script type="text/babel"> 
  // 此处一定要写babel   我写的是jsx  让 bable帮我翻译
  // 1. 创建虚拟DOM
  const VDOM = (
    <h1 id="title">
      <span>Hello,React</span>
    </h1>
  )  // 这里一定不要写单引号，因为不是字符串
  // 2. 渲染虚拟DOM到页面
  // ReactDOM.render(虚拟DOM，容器)
  ReactDOM.render(VDOM, document.getElementById('test'))
  console.log('虚拟DOM', VDOM);

  // 真实DOM
  const TDOM = document.getElementById('test')
  console.log('真实DOM', TDOM);
  debugger;
  // console.log(typeof VDOM); // Object
  // console.log(VDOM instanceof Object); // true
  /*
    关于虚拟DOM： 
      1. 本质是Object类型的对象（一般对象）
      2. 虚拟DOM比较“ 轻 ”， 真实DOM比较 “ 重 ”， 因为虚拟DOM是React内部在用，无需真实DOM上那么多的属性
      3. 虚拟DOM最终会被 react 转化为 真实DOM， 呈现在页面上。
  */ 
  </script>
```

# 3.jsx语法规则

```js
<style>
    .title {
        background-color: orange;
        width: 200px;
    }
</style>
  <body>
    <div id="test"></div>
	<script src="../js/react.development.js"></script>
    <script src="../js/react-dom.development.js"></script>
    <script src="../js/babel.min.js"></script>

    <script type="text/babel">
      const myId = "atguigu";
      const myData = "Hello, React";

      // 1. 创建虚拟DOM
      const VDOM = (
        <div>
          <h2 className="title" id={myId.toLowerCase()}>
            <span style={{ color: "blue", fontSize: "40px" }}>
              {myData.toLowerCase()}
            </span>
          </h2>
          <h2 className="title" id={myId.toUpperCase()}>
            <span style={{ color: "blue", fontSize: "40px" }}>
              {myData.toLowerCase()}
            </span>
          </h2>
          <input type="text"/>
          <good>123321</good>
        </div>
      );
      // 2. 渲染虚拟DOM到页面
      ReactDOM.render(VDOM, document.getElementById("test"));

      /* 
      jsx语法规则：
        1. 定义虚拟DOM时，不要写引号。
        2. 标签中混入JS表达式时要用{}
        3. 样式的类名指定不要用calss，要用className
        4. 内联样式，要用style={{ key: value }} 的形式去写
        5. 虚拟DOM必须只有一个根标签
        6. 标签必须闭合
        7. 标签首字母
            若小写字母开头：则将该标签转为html中同名元素，若html中无标签对应的同名元素，则报错。
            若大写字母开头：react就去渲染对应的组件（Component），弱组件没有定义，则报错
        
    */
    </script>
  </body>
```

# 4.jsx小练习

```js
  <!-- 容器 -->
  <div id="test"></div>

  <script src="../js/react.development.js"></script>
  <script src="../js/react-dom.development.js"></script>
  <script src="../js/babel.min.js"></script>

  <script type="text/babel">
    // 一定注意区分： 【JS语句（代码）】 与 【JS表达式】
    /*
      1. 表达式： 一个表达式会产生一个值，可以放在任何一个需要值的地方
        下面的这些都是表达式： 
          1. a
          2. a+b
          3. demo(1)
          4. arr.map()
          5. function test() {}
      2. 语句（代码）
        下面这些都是语句（代码）：
          1. if(){}
          2. for(){}
          3. switch() case: xxx {}
    */  
    // 模拟一些数据
    const data = ['Angular', 'React', 'Vue']

    const VDOM = (
      <div>
        <h1>前端js框架列表</h1>  
        <ul>
          {
           data.map((item, index) => {
            return <li key={index}>{item}</li>
           })
          }
        </ul>
      </div>
    )

    ReactDOM.render(VDOM, document.getElementById('test'))
  </script>
```

# 5.react自定义组件

## 函数式组件

```js
<body>
  <div id="test"></div>
  <script type="text/babel">
    // 1.创建函数式组件
    function Demo(){
      console.log(this);
      return <h2>我是用函数定义的组件(适用于【简单组件】的定义)</h2>
    }
    // 2. 渲染组件到页面
    ReactDOM.render(<Demo/>, document.getElementById('test'))

    /*
      执行了ReactDOM。render(<Demo/>....)之后，发生了什么？
        1. React解析组件标签，找到了Demo组件
        2. 发现组件是使用函数定义的，随后调用该函数，将返回的虚拟DOM转为真实的DOM，随后呈现在页面中
    */
  </script>
</body>
```

## 类式组件

```js
<body>
  <div id="test"></div>
  <script type="text/babel">
    // 1. 创建类式组件
    class MyComponent extends React.Component {
      render() {
        // render放在哪里？ —— MyComponent类的原型对象上，供实例使用
        // render中的this是谁？ ——MyComponent的实例对象
        console.log(this);
        return <h2>我是用类定义的组件（适用于【复杂组件】的定义）</h2>
      }     
    }
    // console.log(new MyComponent());
    // 2. 渲染组件到页面
    ReactDOM.render(<MyComponent/>, document.getElementById('test'));
    /*
      执行了ReactDOM。render(<MyComponent/>)之后，发生了什么？
        1. React解析组件标签，找到了MyComponent组件
        2. 发现组件是使用类定义的，随后new出来该类的实例，通过该实例调用到原型上了render方法
        3. 将render返回的虚拟DOM转为真实的DOM，随后呈现在页面中
    */
  </script>
</body>
```

# 6.组件三大属性-state

## state

```js
<body>
  <div id="test"></div>

  <script type="text/babel">
    // 1. 创建组件
    class Weather extends React.Component{
      
      // 构造器调用几次？ —— 1次
      constructor(props){
        super(props);
        // 初始化状态
        this.state = {
          isHot: true,
          wind: '微风'
        }
        // 解决changeWeather中的this指向问题
        this.changeWeather = this.changeWeather.bind(this)
      }

      // render调用几次？ —— 1+n次，1是初始化的那次，n是每次更新状态的次数
      render(){
        // console.log(this);
        const {isHot, wind} = this.state
        return <h1 id="title" onClick={this.changeWeather}>今天天气很{isHot? '炎热':'凉爽'}，{wind}</h1>
      }

      // changeWeather调用几次？ —— 点击几次调几次
      changeWeather(){
        // 由于changeWeather是作为onClick的回调，所以不是通过实例调用的，而是直接调用
        // 类中的方法默认开启了局部的严格模式，所以changeWeather中的this为undefined

        // 严重注意： 状态（state）不可以直接更改，下面这行就是直接更改，要借助一个api进行更改
        // 错误： this.state.isHot = !this.state.isHot

        // 获取原来的isHot值
        const isHot = this.state.isHot;
        // 严重注意： 状态必须通过 setState 来更新，且更新是一种合并，不是替换
        this.setState({isHot: !isHot});
        // console.log(this); // Weather
      }
    }
    ReactDOM.render(<Weather/>, document.getElementById('test'))

    // const title = document.getElementById('title');
    // title.addEventListener('click', () => {
    //   console.log('标题发生了点击');
    // })

    // const title2 = document.getElementById('title');
    // title2.onclick = function() {
    //   alert('标题发生了点击')
    // }
    // title2.onclick = () => {
    //   alert('标题发生了点击')
    // }


    // 测试： 满足一下好奇心， 这里的是对的
    // const w = new Weather()
    // w.changeWeather()

  </script>

</body>
```

## state的简写方式

```js
class Weather extends React.Component{
      // 初始化状态
      state = {isHot: true, wind: '微风'}
      render(){
        const {isHot, wind} = this.state
        return <h1 id="title" onClick={this.changeWeather}>今天天气很{isHot? '炎热':'凉爽'}，{wind}</h1>
      }
      // 自定义方法 —— 要用赋值语句的形式+箭头函数
      changeWeather = () => {
        console.log(this);
        const isHot = this.state.isHot;
        this.setState({isHot: !isHot});
      }
    }
ReactDOM.render(<Weather/>, document.getElementById('test'))
```

# 7.组件三大属性-props

## props基本使用

```js
class Person extends React.Component{
	// state = {name: 'tom', age: 18, sex: '女'}
	render(){
		// console.log(this);
		const {name, age, sex} = this.props;
		return(
			<ul>
				<li>姓名：{name}</li>
				<li>性别：{sex}</li>
				<li>年龄：{age+1}</li>
			</ul>
		)
	}
}

ReactDOM.render(<Person name="tom" sex="女" age="18" />, document.getElementById('test1'));
ReactDOM.render(<Person name="candy" sex="女" age="58" />, document.getElementById('test2'));
ReactDOM.render(<Person name="老刘" sex="女" age="58" />, document.getElementById('test3'));

// 这个age 是 36,因为这个age是number类型的,上面的都是 string
const p = {name: '老刘', age: 35, sex:"男"}
ReactDOM.render(<Person {...p} />, document.getElementById('test4'));
```

## 对props进行限制

```js
<!-- 引入react核心库 -->
<script src="../js/react.development.js"></script>
<!-- 引入react-dom. 用于支持react操作DOM -->
<script src="../js/react-dom.development.js"></script>
<!-- 引入babel，用于将jsx转为js -->
<script src="../js/babel.min.js"></script>
<!-- 引入prop-types，用于对组件标签属性进行限制 -->
<script src="../js/prop-types.js"></script>

class Person extends React.Component{
	// state = {name: 'tom', age: 18, sex: '女'}
	render(){
		// console.log(this);
		const {name, age, sex} = this.props;
		// props 是只读的
		// this.props.name = 'jack' // 此行代码会报错，因为props是只读的
		return(
			<ul>
				<li>姓名：{name}</li>
				<li>性别：{sex}</li>
				<li>年龄：{age+1}</li>
			</ul>
		)
	}
}
// 对标签进行类型、必要性的限制
Person.propTypes = { 
	name: PropTypes.string.isRequired,  // 限制必须传，并且为string
	sex: PropTypes.string,
	age: PropTypes.number,
	speak: PropTypes.func // 限制speak为函数
}
// 指定默认标签属性值
Person.defaultProps = {
	sex: '男',  // sex默认值 男
	age: 18   // age默认值 18
}

ReactDOM.render(<Person name="tom" sex="女" age={18} speak={speak} />, document.getElementById('test1'));
ReactDOM.render(<Person name="candy" sex="女" age={58} />, document.getElementById('test2'));
// ReactDOM.render(<Person name="老刘" sex="女" age="58" />, document.getElementById('test3'));

const p = {name: '老刘', sex:"男"}
ReactDOM.render(<Person {...p} />, document.getElementById('test3'));

function speak(){
	console.log('我说话了');
}

// 结果
姓名：tom
性别：女
年龄：19
姓名：candy
性别：女
年龄：59
姓名：老刘
性别：男
年龄：19
```

## props的简写方式

```js
<!-- 引入prop-types，用于对组件标签属性进行限制 -->
<script src="../js/prop-types.js"></script>

<script type="text/babel">
	class Person extends React.Component {
		constructor(props){
			// 构造器是否接收props，是否传递给super，取决于：是否希望在构造器中通过this访问props
			console.log(props);
			super(props);
		}
		state = {};

		static propTypes = {
			name: PropTypes.string.isRequired, // 限制必须传，并且为string
			sex: PropTypes.string,
			age: PropTypes.number,
			// speak: PropTypes.func, // 限制speak为函数
		};
		// 指定默认标签属性值
		static defaultProps = {
			sex: "男", // sex默认值 男
			age: 18, // age默认值 18
		};

		// state = {name: 'tom', age: 18, sex: '女'}
		render() {
			// console.log(this);
			const { name, age, sex } = this.props;
			// props 是只读的
			// this.props.name = 'jack' // 此行代码会报错，因为props是只读的
			return (
				<ul>
					<li>姓名：{name}</li>
					<li>性别：{sex}</li>
					<li>年龄：{age + 1}</li>
				</ul>
			);
		}
	}
	// 对标签进行类型、必要性的限制

	ReactDOM.render(
		<Person name="tom" sex="女" age={19} />,
		document.getElementById("test1")
	);

	// ReactDOM.render(
	//   <Person name="candy" sex="女" age={58} speak={speak} />,
	//   document.getElementById("test2")
	// );
	// // ReactDOM.render(<Person name="老刘" sex="女" age="58" />, document.getElementById('test3'));

	// const p = { name: "老刘", sex: "男" };
	// ReactDOM.render(<Person {...p} />, document.getElementById("test3"));

	// function speak() {
	//   console.log("我说话了");
	// }
</script>
```

## 函数组件使用props

```js
function Person(props){
	const {name, age, sex} = props
	return(
			<ul>
				<li>姓名：{name}</li>
				<li>性别：{sex}</li>
				<li>年龄：{age + 1}</li>
			</ul>
		)
}

Person.propTypes = {
	name: PropTypes.string.isRequired, // 限制必须传，并且为string
	sex: PropTypes.string,
	age: PropTypes.number,
	// speak: PropTypes.func, // 限制speak为函数
};
// 指定默认标签属性值
Person.defaultProps = {
	sex: "男", // sex默认值 男
	age: 18, // age默认值 18
};

ReactDOM.render(
	// <Person name="tom" sex="女" age={19} />,
	<Person name="tom"/>,
	document.getElementById("test1")
);

/*
ReactDOM.render(
  <Person name="candy" sex="女" age={58} speak={speak} />,
  document.getElementById("test2")
);
//报错:age为number ReactDOM.render(<Person name="老刘" sex="女" age="58" />, document.getElementById('test3'));

const p = { name: "老刘", sex: "男" };
ReactDOM.render(<Person {...p} />, document.getElementById("test3"));

function speak() {
  console.log("我说话了");
}
*/
```

# 8.组件三大属性-refs

## 字符串形式的ref

```js
class Demo extends React.Component{
	render(){
		return (
			<div>
				<input ref="input1" type="text" placeholder="点击按钮提示内容"/>&nbsp;
				<button onClick={this.showData} >点我提示左侧的数据</button>&nbsp;&nbsp;&nbsp;
				<input ref="input2" onBlur={this.showData2} type="text" placeholder="失去焦点提示数据" />
			</div>
		)
	}
	showData = () => {
		// 原生写法
		// const str = document.getElementById('input1');
		// console.log(str.value);
		const {input1} = this.refs;
		alert(input1.value)
	}
	showData2 = () => {
		const {input2} = this.refs;
		alert(input2.value);
	}
}

ReactDOM.render(<Demo/>, document.getElementById('test'))
```

## 回调形式的ref

```js
class Demo extends React.Component{
	render(){
		return (
			<div>
             // c代表的是当前的结点current,相当于是把 当前结点 挂在了 当前实例自身上,并且取了个名字叫 input1. 注意:这里是箭头函数,this指向外层的函数(render),render是谁?当前实例对象Demo
				<input ref={c => this.input1 = c} type="text" placeholder="点击按钮提示内容"/>&nbsp;
				<button onClick={this.showData} >点我提示左侧的数据</button>&nbsp;&nbsp;&nbsp;
				<input ref={c => this.input2 = c} onBlur={this.showData2} type="text"/>
			</div>
		)
	}
	showData = () => {
		// 原生写法
		// const str = document.getElementById('input1');
		// console.log(str.value);
		const {input1} = this;
		alert(input1.value)
	}
	showData2 = () => {
		const {input2} = this;
		alert(input2.value);
	}
}
ReactDOM.render(<Demo/>, document.getElementById('test'))
```

## 回调ref中回调执行次数的问题

```js
/*
  如果ref回调函数是以内联函数的方式定义的，在更新过程中它会被执行两次，第一次传入参数null，然后第二次会传入参数DOM元素。这是因为在每次渲染时会创建一个新的函数实例，所以React清空旧的ref并且设置新的。通过将ref 的回调函数定义成class的绑定函数的方式可以避免上述问题，但是大多数情况下它是无关紧要的。
  说白了,因为更改数据的时候需要重新render,所以ref的回调函数相当于是新的函数,因为没有东西来存储,所以相当于又是一个新的函数,react需要先清除原来的那个ref,然后才能重新定义新的ref,所以调用两次,第一次是null,第二次才是当前结点的值.
  如果用函数的形式,那么不管怎么样只调用干开始的第一次,以后的更改数据也不会调用
*/
class Demo extends React.Component{
	state = {isHot: true}
	render(){
		const {isHot} = this.state
		return(
			<div>
				<h2 onClick={this.h2Click}>今天天气很{isHot? '炎热':'凉爽'}</h2>
				{/* <input ref = {(c) => {this.input1 = c;console.log('@', c);}} type="text"/> */}	// 刚开始一次,后面每更新一次调用两次
				<input ref = {this.saveInput} type="text"/>  // 就是刚开始调一次就没了
				<button onClick={this.showData}>点我</button>
			</div>
		)
	}
	showData = () => {
		const{input1} = this
		alert(input1.value)
	}
	h2Click = () => {
		const {isHot} = this.state;
		this.setState({isHot : !isHot})
	}
	saveInput =(c) => {
		this.input1 = c;
		console.log('@', c);
	}
}
ReactDOM.render(<Demo/>, document.getElementById('test'))
```

## createRef

```js
class Demo extends React.Component{
	// React.createRef调用后可以返回一个容器，该容器可以存储被ref所标识的节点
	// 该容器是专人专用的
	myRef = React.createRef()

	render(){
		return (
			<div>
				<input ref={this.myRef} type="text" placeholder="点击按钮提示内容"/>&nbsp;
				<button onClick={this.showData} >点我提示左侧的数据</button>&nbsp;&nbsp;&nbsp;
			</div>
		)
	}

	showData = () => {
		// console.log(this.myRef.current.value);
		alert(this.myRef.current.value)
	}
}
ReactDOM.render(<Demo/>, document.getElementById('test'))
```

# 9.react中的事件处理

```js
class Demo extends React.Component{
	/*
		1) 通过onXxx属性指定组件的事件处理函数(注意大小写)
			a. React使用的是自定义(合成)事件, 而不是使用的原生DOM事件 ———— 为了更好的兼容性
			b. React中的事件是通过事件委托方式处理的(委托给组件最外层的元素)  ———— 为了高效
		2) 通过event.target得到发生事件的DOM元素对象
	*/

	// React.createRef调用后可以返回一个容器，该容器可以存储被ref所标识的节点
	// 该容器是专人专用的
	myRef = React.createRef()
	myRef2 = React.createRef()

	showData = (event) => {
		// console.log(this.myRef.current.value);
		// alert(this.myRef.current.value)
		console.log(event.target)
	}

	showData2 = (event) => {
		// alert(this.myRef2.current.value)
		console.log(event.target);
		alert(event.target.value)
	}

	render(){
		return (
			<div>
				<input ref={this.myRef} type="text" placeholder="点击按钮提示内容"/>&nbsp;
				<button onClick={this.showData} >点我提示左侧的数据</button>&nbsp;&nbsp;&nbsp;
				{/* <input ref={this.myRef2} type="text" onBlur={this.showData2} placeholder="失去焦点提示内容"/> */}
				<input type="text" onBlur={this.showData2} placeholder="失去焦点提示内容"/>
			</div>
		)
	}
}
ReactDOM.render(<Demo/>, document.getElementById('test'))
```

# 10.react中的收集表单数据

**说白了,这个组件中的值是我所一直都知道的,就是受控组件,如果我想在用的时候再拿他的值,那么就是非受控组件了.**

## 非受控组件

```js
<!-- 现用现取   就是   非受控组件 -->
<!-- 准备好一个“容器” -->
<div id="test"></div>
<script type="text/babel"> 
	class Login extends React.Component{
		handleSubmit = (event) => {
			event.preventDefault()   // 阻止表单的提交
			const {username, pwd} = this
			// alert(username.value)
			alert(`你输入的用户名是： ${username.value}, 你的密码是：${pwd.value}`)
		}
		render(){
			return(
				<form action="http://www.baidu.com" onSubmit={this.handleSubmit}>
					用户名： <input ref = {c => this.username = c} type="text" name="username"/>
					密  码： <input ref = {c => this.pwd = c} type="password" name="pwd"/>
					<button>登录</button>
				</form>
			)
		}
	}
	ReactDOM.render(<Login/>, document.getElementById('test'))
</script>
```

## 受控组件

```js
<!-- 受控组件  --- input中的值是我可以存储的 -->
<!-- 准备好一个“容器” -->
<div id="test"></div>
<script type="text/babel"> 
	class Login extends React.Component{
		// 初始化状态
		state = {username: '', pwd: ''}
		handleSubmit = (event) => {
			event.preventDefault()   // 阻止表单的提交
			const {username, pwd} = this.state
			// const {username, pwd} = this
			// alert(username.value)
			alert(`你输入的用户名是： ${username}, 你的密码是：${pwd}`)
		}
		saveUsername = (event) => {
			// console.log(event.target.value);
			this.setState({username: event.target.value})
		}
		savePwd = (event) => {
			this.setState({pwd: event.target.value})
		}
		render(){
			return(
				<form action="http://www.baidu.com" onSubmit={this.handleSubmit}>
					用户名： <input onChange={this.saveUsername} type="text" name="username"/>
					密  码： <input onChange={this.savePwd} type="password" name="pwd"/>
					<button>登录</button>
				</form>
			)
		}
	}
	ReactDOM.render(<Login/>, document.getElementById('test'))
</script>
```

# 11.函数柯里化

**下面的对比中,注意看 saveFormData和render中的方法**

## 高阶函数---柯里化实现表单

```js
<script type="text/babel"> 
// #region
	/*
		高阶函数： 如果一个函数符合下面2个规范中的任何一个，那么该函数就是高阶函数。
			1.若A函数，接收的参数是一个函数，那么A就可以称之为高阶函数。
			2.若A函数，调用的返回值依然是一个函数，那么A就可以称之为高阶函数。
			常见的高阶函数有哪些： Promise、延时定时器setTimeout、arr.map()、arr.reduce()、arr.filter()、arr.forEach()等等
		函数的柯里化：通过函数调用继续返回函数的方式，实现多次接收参数最后统一处理的函数编码形式。
	*/
// #endregion
class Login extends React.Component{
	state = {username: '', pwd: ''}
	handleSubmit = (event) => {
		event.preventDefault()   // 阻止表单的提交
		const {username, pwd} = this.state
		alert(`你输入的用户名是： ${username}, 你的密码是：${pwd}`)
	}
	
	saveFormData = (dataType) => {
		return (event) => {
			console.log(dataType, event.target.value);
			this.setState({[dataType]: event.target.value})
		}
	}
	render(){
		return(
			<form action="http://www.baidu.com" onSubmit={this.handleSubmit}>
				用户名： <input onChange={this.saveFormData('username')} type="text" name="username"/>
				密  码： <input onChange={this.saveFormData('pwd')} type="password" name="pwd"/>
				<button>登录</button>
			</form>
		)
	}
}
ReactDOM.render(<Login/>, document.getElementById('test'))
</script>
```

## 不使用柯里化的实现

```js
class Login extends React.Component{
	state = {username: '', pwd: ''}
	handleSubmit = (event) => {
		event.preventDefault()   // 阻止表单的提交
		const {username, pwd} = this.state
		alert(`你输入的用户名是： ${username}, 你的密码是：${pwd}`)
	}
	
	saveFormData(dataType, event){
			this.setState({[dataType]: event.target.value})
			console.log(dataType, event.target.value);
	}

	render(){
		return(
			<form action="http://www.baidu.com" onSubmit={this.handleSubmit}>
				用户名： <input onChange={ event => this.saveFormData('username', event) } type="text" name="username"/>
				密  码： <input onChange={ event => this.saveFormData('pwd', event) } type="password" name="pwd"/>
				<button>登录</button>
			</form>
		)
	}
}
ReactDOM.render(<Login/>, document.getElementById('test'))
```

# 12.组件的生命周期

## react生命周期(旧)

{% asset_img  2_react生命周期(旧).png %}

```js
/*
    a. 第一次初始化渲染显示: ReactDOM.render()
        constructor(): 创建对象初始化state
        componentWillMount() : 将要插入回调
        render() : 用于插入虚拟DOM回调      =>    常用
        componentDidMount() : 已经插入回调    =>  常用，一般在这个钩子中做一些初始化的事，例如：开启定时器、发送网络请求、订阅消息
    b. 每次更新state: this.setSate(),或者父组件render触发
        shouldComponentUpdate() : 控制更新的阀门
        componentWillUpdate() : 将要更新回调     
        render() : 更新(重新渲染)
        componentDidUpdate() : 已经更新回调
    c. 移除组件: ReactDOM.unmountComponentAtNode(containerDom)
        componentWillUnmount() : 组件将要被移除回调   =>    常用，一般在这个钩子中做一些收尾的事，例如：关闭定时器、取消订阅消息
*/
```



### 单独的Count组件

```js
// 页面刚开始【什么也不操作】Count---constructor---componentWillMount---render---componentDidMount
class Count extends React.Component{
	constructor(props){
		console.log('Count---constructor');
		super(props);
		this.state = {count: 0}
	}
	add = () => {
		let {count} = this.state;
		count += 1;
		this.setState({count: count})
	}
	death = () => {
		ReactDOM.unmountComponentAtNode(document.getElementById('test'))
	}
	force = () => {
		this.forceUpdate()
	}
	// 组件将要挂载的钩子
	componentWillMount(){
		console.log('Count---componentWillMount');
	}
	// 组件挂载完毕的钩子
	componentDidMount(){
		 console.log('Count---componentDidMount');
	}
	// 组件将要卸载的钩子
	componentWillUnmount(){
		console.log('Count---componentWillUnmount');
	}
	// 控制组件更新的阀门  -- 返回值要是布尔值，如果为true，那么后面的可以执行， 否则不能
	shouldComponentUpdate(){
		console.log('Count---shouldComponentUpdate');
		return true
	}
	// 组件将要更新的钩子
	componentWillUpdate(){
		console.log('Count---componentWillUpdate');
	}
	// 组件更新完毕的钩子
	componentDidUpdate(){
		console.log('Count---componentDidUpdate');
	}
	render(){
		const {count} = this.state
		console.log('Count---render');
		return(
			<div>
				<h2>当前的和为{count}</h2>  
				<button onClick={this.add} >点我+1</button>
				<button onClick={this.death}>卸载组件</button>
				<button onClick={this.force}>不更改任何状态中的数据强制更新一下</button>
			</div>
		)
	}
}
// 渲染组件
ReactDOM.render(<Count/>, document.getElementById('test'))

// 刚开始
Count---constructor
Count---componentWillMount
Count---render
Count---componentDidMount
// 点击 点我+1
Count---shouldComponentUpdate
Count---componentWillUpdate
Count---render
Count---componentDidUpdate
// 点击 不更改任何状态强制更新一下
Count---componentWillUpdate
Count---render
Count---componentDidUpdate
// 点击 卸载组件
Count---componentWillUnmount
```

### 父子组件(A父B子)

**A**

```js
class A extends React.Component{
	state = {carName: '奔驰'}

	constructor(props){
		console.log('A---constructor');
	}

	// 组件将要挂载的钩子
	componentWillMount(){
		console.log('A---componentWillMount');
	}

	componentDidMount(){
		console.log('A---componentDidMount');
	}
	
	// 组件将要接受新的 props钩子
	componentWillReceiveProps(props){
		console.log('A---componentWillReceiveProps', props);
	}

	// 控制组件更新的阀门
	shouldComponentUpdate(){
		console.log('A---shouldComponentUpdate');
		return true
	}

	// 组件将要更新
	componentWillUpdate(){
		console.log('A---componentWillUpdate');
	}

	// 组件更新完毕
	componentDidUpdate(){
		console.log('A---componentDidUpdate');
	}

	changeCar = () => {
		this.setState({carName: '奥迪'})
	}
	render(){
		console.log('A---render');
		return(
			<div>
				<div>我是A组件</div>
				<button onClick={this.changeCar}>换车</button>
				<B carName={this.state.carName} />
			</div>
		)
	}
}
```

**B**

```js
class B extends React.Component{

	constructor(props){
		console.log('B---constructor');
	}

	// 组件将要挂载的钩子
	componentWillMount(){
		console.log('B---componentWillMount');
	}
	
	componentDidMount(){
		console.log('B---componentDidMount');
	}
	
	// 组件将要接受新的 props钩子
	componentWillReceiveProps(props){
		console.log('B---componentWillReceiveProps', props);
	}

	// 控制组件更新的阀门
	shouldComponentUpdate(){
		console.log('B---shouldComponentUpdate');
		return true
	}

	// 组件将要更新
	componentWillUpdate(){
		console.log('B---componentWillUpdate');
	}

	// 组件更新完毕
	componentDidUpdate(){
		console.log('B---componentDidUpdate');
	}

	render(){
		console.log('B---render');
		return(
			<div>我是B组件， 接受到的车是： {this.props.carName}</div>
		)
	}
}
```

```js
// 刚开始
A---constructor
A---componentWillMount
A---render
B---constructor
B---componentWillMount
B---render
B---componentDidMount
A---componentDidMount
// 点击 换车
A---shouldComponentUpdate
A---componentWillUpdate
A---render
B---componentWillReceiveProps {carName: '奥迪'}
B---shouldComponentUpdate
B---componentWillUpdate
B---render
B---componentDidUpdate
A---componentDidUpdate
```

## react生命周期(新)

{% asset_img  3_react生命周期(新).png %}

```js
<body>
  <!-- 准备好一个“容器” -->
  <div id="test"></div>

  <!-- 引入react核心库 -->
  <script src="../js/16.14.0/react.development.js"></script>
  <!-- 引入react-dom. 用于支持react操作DOM -->
  <script src="../js/16.14.0/react-dom.development.js"></script>
  <!-- 引入babel，用于将jsx转为js -->
  <script src="../js/16.14.0/babel.min.js"></script>
  
  <script type="text/babel"> 
  /*
    1.初始化阶段:由ReactDOM. render( )触发---初次渲染
      1. constructor( )
      2. getDerivedStateFromProps
      3. render()
      4. componentDidMount( )    =>   常用，一般在这个钩子中做一些初始化的事，例如：开启定时器、发送网络请求、订阅消息
    2.更新阶段:由组件内部this .setSate( )或父组件重新render触发
      1. getDerivedstateFromProps
      2. shouldComponentUpdate()
      3. render( )
      4. getSnapshotBeforeUpdate
      5. componentDidUpdate()
    3.卸载组件:由ReactDOM. unmountComponentAtNode()触发
      1. componentWillUnmount( )    =>    常用，一般在这个钩子中做一些收尾的事，例如：关闭定时器、取消订阅消息
  */
  // 页面刚开始【什么也不操作】Count---constructor---componentWillMount---render---componentDidMount
    class Count extends React.Component{

      constructor(props){
        console.log('Count---constructor');
        super(props);
        this.state = {count: 0}
      }

      add = () => {
        let {count} = this.state;
        count += 1;
        this.setState({count: count})
      }

      death = () => {
        ReactDOM.unmountComponentAtNode(document.getElementById('test'))
      }

      force = () => {
        this.forceUpdate()
      }

      // 弃用：组件将要挂载的钩子
      // UNSAFE_componentWillMount(){
      //   console.log('Count---componentWillMount');
      // }

      
      static getDerivedStateFromProps(props, state){
        // 若 state的值在任何时候都取决于 props，那么可以使用 getDerivedStateFromProps
        console.log('Count---getDerivedStateFromProps', props, state);
        return null
      }
      
      getSnapshotBeforeUpdate(){
        // 在更新之前获取快照
        console.log('Count---getSnapshotBeforeUpdate');
        return 'coderlt'
      }

      componentDidMount(){
        // 组件挂载完毕的钩子
         console.log('Count---componentDidMount');
      }

      // 组件将要卸载的钩子
      componentWillUnmount(){
        console.log('Count---componentWillUnmount');
      }

      // 控制组件更新的阀门  -- 返回值要是布尔值，如果为true，那么后面的可以执行， 否则不能
      shouldComponentUpdate(){
        console.log('Count---shouldComponentUpdate');
        return true
      }

      // 弃用：组件将要更新的钩子
      // UNSAFE_componentWillUpdate(){
      //   console.log('Count---componentWillUpdate');
      // }

      
      componentDidUpdate(preProps, preState, snapshotValue){
        // 组件更新完毕的钩子
        console.log('Count---componentDidUpdate', preProps, preState, snapshotValue);
      }

      render(){
        const {count} = this.state
        console.log('Count---render');
        return(
          <div>
            <h2>当前的和为{count}</h2>  
            <button onClick={this.add} >点我+1</button>
            <button onClick={this.death}>卸载组件</button>
            <button onClick={this.force}>不更改任何状态中的数据强制更新一下</button>
          </div>
        )
      }
    }
    // 渲染组件
    ReactDOM.render(<Count count={199} />, document.getElementById('test'))
  </script>
</body>
```

```js
// 刚开始
Count---constructor
Count---getDerivedStateFromProps {count: 199} {count: 0}
Count---render
Count---componentDidMount
// 点击 点我+1
Count---getDerivedStateFromProps {count: 199} {count: 1}
Count---shouldComponentUpdate
Count---render
Count---getSnapshotBeforeUpdate
Count---componentDidUpdate {count: 199} {count: 0} coderlt
// 点击 不更改任何状态强制更新
Count---getDerivedStateFromProps {count: 199} {count: 1}
Count---render
Count---getSnapshotBeforeUpdate
Count---componentDidUpdate {count: 199} {count: 1} coderlt
// 点击卸载组件
Count---componentWillUnmount
```

### getSnapshotBeforeUpdate

```js
class NewsList extends React.Component{
	state = {newsArr:[]}
	componentDidMount(){
		setInterval(() => {
			const {newsArr} = this.state
			// 模拟一条新闻
			const news = '新闻' + (newsArr.length + 1)
			// 更新状态
			this.setState({newsArr: [news, ...newsArr]})
		}, 1000);
	}
	getSnapshotBeforeUpdate(){
		// 在更新之前获取快照
		return this.refs.list.scrollHeight
	}
	// 这里的height 是上面的快照传递下来的值
	componentDidUpdate(preProps, preState, height){
		this.refs.list.scrollTop += this.refs.list.scrollHeight - height
	}
	render(){
		return(
			<div className="list" ref="list">
				{
					this.state.newsArr.map((n, index) => {
						return <div key={index} className="news">{n}</div>
					})
				}
			</div>
		)
	}
}
ReactDOM.render(<NewsList/>, document.getElementById('test'))
```

# 13.Diff算法+俩个面试题

## 验证diff

```js
// 这里每次更新的时候,input中的内容都不会丢失,说明 Diff 很强啊!他还会对标签里面的内容进行对比
class Time extends React.Component{
	state = {date: new Date()}
	componentDidMount(){
		setInterval(() => {
			this.setState({date: new Date()})
		}, 1000);
	}
	render(){
		return(
			<div>
				<h1>hello</h1>
				<input type="text"/>
				<span>现在是: {this.state.date.toTimeString()}<input type="text"/></span>
			</div>
		)
	}
}
ReactDOM.render(<Time/>, document.getElementById('test'))
```

## key的作用

```js
<!-- 
经典面试题： 
	1) react/vue中的key有什么作用？ （key的内部原理是什么？）
	2) 为什么遍历列表时，key最好不要用index？

	1. 虚拟DOM中key的作用：
		1). 简单的说：key是虚拟DOM对象的标识，在更新显示时key起着极其重要的作用。

		2). 详细的说：当状态的数据发生变化时，react会根据【新数据】生成【新的虚拟DOM】
								随后React进行【新虚拟DOM】与【旧虚拟DOM】的diff比较，比较规则如下：
						a. 旧虚拟DOM中找到了与新虚拟DOM相同的key:
									(1).若虚拟DOM中内容没变，直接使用之前的真实DOM
									(2).若虚拟DOM中内容变了，则生成新的真实DOM，随后替换掉页面中之前的真实DOM
						b.旧虚拟DOM中未找到与新虚拟DOM相同的key
									根据数据创建新的真实DOM，随后渲染到到页面
	2.用index作 为key可能会引发的问题: 
			1.若对数据进行:逆序添加、逆序删除等破坏顺序操作:
				会产生没有必要的真实DOM更新==>界面效果没问题，但效率低。
			2.如果结构中还包含输入类的DOM:
				会产生错误DOM更新==>界面有问题。
			3.注意!如果不存在对数据的逆序添加、逆序删除等破坏顺序操作，
				仅用于渲染列表用于展示，使用index作为key是没有问题的。
	3. 开发中如何选择key？
			1.最好使用每条数据的唯一标识作为ey,比如id、手机号、身份证号、学号等唯一-值。
			2.如果确定只是简单的展示数据，用index也是可以的。
-->

<!-- 
慢动作回放----使用index索引值作为key

	数据：
				{id: 1, name: '嚣张', age: 18},
				{id: 2, name: '小李', age: 19}
	初始的虚拟DOM: 
				<li key=0>嚣张----18</li>
				<li key=1>小李----19</li>
	更新后的数据：
				{id: 3, name: '小王', age: 20},
				{id: 1, name: '嚣张', age: 18},
				{id: 2, name: '小李', age: 19}
	更新数据后的虚拟DOM：
				<li key=0>小王----20</li>
				<li key=1>嚣张----18</li>
				<li key=2>小李----19</li>
	
=================================================

	慢动作回放----使用id索引值作为key

	数据：
				{id: 1, name: '嚣张', age: 18},
				{id: 2, name: '小李', age: 19}
	初始的虚拟DOM: 
				<li key=1>嚣张----18</li>
				<li key=2>小李----19</li>
	更新后的数据：
				{id: 3, name: '小王', age: 20},
				{id: 1, name: '嚣张', age: 18},
				{id: 2, name: '小李', age: 19}
	更新数据后的虚拟DOM【这些id和内容没有改变的DOM，直接复用在页面的真实DOM】：
				<li key=3>小王----20</li>
				<li key=1>嚣张----18</li>
				<li key=2>小李----19</li>
	
-->
class Person extends React.Component{
	state = {
		persons: [
			{id: 1, name: '嚣张', age: 18},
			{id: 2, name: '小李', age: 19}
		]
	}
	addPerson = () => { 
		const {persons} = this.state;
		const p = {id: 3, name: '小王', age: 20}
		this.setState({persons: [p, ...persons]})
	}
	render(){
		return(
			<div>
				<h2>使用index索引，展示人员信息</h2>
				<ul>
					{
						this.state.persons.map((personObj, index) => {
							return <li key={index}>{personObj.name} -----  {personObj.age} <input type="text"/></li>
						})
					}
				</ul>
				<hr/><hr/>
				<h3>使用数据唯一[id]标识索引，展示人员信息</h3>
				<ul>
					{
						this.state.persons.map((personObj, index) => {
							return <li key={personObj.id}>{personObj.name} -----  {personObj.age} <input type="text"/></li>
						})
					}
				</ul>
				<button onClick={this.addPerson}>添加一个小王信息</button>
			</div>
		)
	}
}
ReactDOM.render(<Person/>, document.getElementById('test'))
```

# 以下内容使用react脚手架

