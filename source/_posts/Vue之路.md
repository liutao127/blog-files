---
title: Vue之路
date: 2022-05-20 18:02:16
tags: Vue
---

# Vue的理解?

<!-- more -->

**Vue是一个开源的JS框架，也是一个创建单页应用的web应用框架，vue所关注的核心是MVC中的视图层，能够方便地获取数据更新；**

**Vue的核心特性：**

1.数据驱动MVVM

```
Model层：模型层，负责处理业务逻辑以及和服务器端进行交互；
View层：视图层，负责将数据模型转化为UI展示出来，可以简单理解为html页面；
ViewModel层：视图模型层，用来连接model和view，是两者的通信桥梁；
```

2.组件化

```
将图形、非图形的各种逻辑抽象为一个统一的概念来实现开发的模式
好处：降低耦合度；调试方便；提高可维护性；
```

3.指令系统

```
指令是带有v-前缀的特殊属性作用：当表达式的值改变时，将其产生的连带影响作用于DOM
v-if v-for v-bind v-on v-model
```

react和vue的不同：

```
1.数据流向；react单向数据流 vue双向数据流；
2.数据变化的实现原理；react使用不可变数据 vue使用可变数据
3.组建化通信； react使用回调进行 vue子组件向父组件传递通过事件和回调函数
4.diff算法不同；react主要使用diff队列保存哪些需要更新的dom，得到patch树，统一操作dom vue采用双向指针，边对比边更新dom
```

# 1、vue2生命周期（详细）

## Vue的生命周期到底是什么？

**官网的话：**每一个vue实例从创建到销毁的过程，就是这个vue实例的[生命周期](https://so.csdn.net/so/search?q=生命周期&spm=1001.2101.3001.7020)。在这个过程中，他经历了从开始创建、初始化数据、编译模板、挂载Dom、渲染→更新→渲染、卸载等一系列过程。



## Vue生命周期的执行顺序

他整体是分为三个大阶段的，在三个大阶段中，有细分为若干的小阶段。我们可以在不同的阶段去做不同的事情，后文也会讲到不同的阶段适合我们去做具体什么事情的。

### 例外：

**activated**：被 keep-alive 缓存的组件激活时调用。**该钩子在服务器端渲染期间不被调用。**

**deactivated**：被 keep-alive 缓存的组件失活时调用。**该钩子在服务器端渲染期间不被调用。**

**errorCaptured：**在捕获一个来自后代组件的错误时被调用。此钩子会收到三个参数：错误对象、发生错误的组件实例以及一个包含错误来源信息的字符串。此钩子可以返回 `false` 以阻止该错误继续向上传播。

```
(err: Error, vm: Component, info: string) => ?boolean

你可以在此钩子中修改组件的状态。因此在捕获错误时，在模板或渲染函数中有一个条件判断来绕过其它内容就很重要；不然该组件可能会进入一个无限的渲染循环。


错误传播规则

默认情况下，如果全局的 config.errorHandler 被定义，所有的错误仍会发送它，因此这些错误仍然会向单一的分析服务的地方进行汇报。

如果一个组件的 inheritance chain (继承链)或 parent chain (父链)中存在多个 errorCaptured 钩子，则它们将会被相同的错误逐个唤起。

如果此 errorCaptured 钩子自身抛出了一个错误，则这个新错误和原本被捕获的错误都会发送给全局的 config.errorHandler。

一个 errorCaptured 钩子能够返回 false 以阻止错误继续向上传播。本质上是说“这个错误已经被搞定了且应该被忽略”。它会阻止其它任何会被这个错误唤起的 errorCaptured 钩子和全局的 config.errorHandler。
```



### 挂载阶段：

**beforeCreate**	// 在实例初始化之后，数据观测（data observer）和 event/watcher 事件配置之前被调用

**created**  // 在实例创建完成之后被立即调用。   在这一步，实例已经完成以下配置：数据观测，属性和方法的运算，watch/event事件回调。   然而挂载阶段还没开始，$el属性目前不可见

**beforeMount**  //  在挂载之前被调用，相关渲染render函数首次被调用。**该钩子在服务器端渲染期间不被调用。**

**mounted**  //  el 被创建的  vm.$el 替换，挂载成功

注意 `mounted` **不会**保证所有的子组件也都被挂载完成。如果你希望等到整个视图都渲染完毕再执行某些操作，可以在 `mounted` 内部使用 [vm.$nextTick](https://cn.vuejs.org/v2/api/#vm-nextTick)：

```
mounted: function () {
  this.$nextTick(function () {
    // 仅在整个视图都被渲染之后才会运行的代码
  })
}
```

**该钩子在服务器端渲染期间不被调用。**

### 更新阶段：

**beforeUpdate**	//	数据更新时调用

在数据发生改变后，DOM 被更新之前被调用。这里适合在现有 DOM 将要被更新之前访问它，比如移除手动添加的事件监听器。

**该钩子在服务器端渲染期间不被调用，因为只有初次渲染会在服务器端进行。**

**updated**	//	组件DOM已经更新，组件更新完毕

注意，`updated` **不会**保证所有的子组件也都被重新渲染完毕。如果你希望等到整个视图都渲染完毕，可以在 `updated` 里使用 [vm.$nextTick](https://cn.vuejs.org/v2/api/#vm-nextTick)：

```
updated: function () {
  this.$nextTick(function () {
    //  仅在整个视图都被重新渲染之后才会运行的代码     
  })
}
```

**该钩子在服务器端渲染期间不被调用。**

### 销毁阶段：

**beforeDestory**：实例销毁之前调用。在这一步，实例仍然完全可用。**该钩子在服务器端渲染期间不被调用。**

**destroyed**：实例销毁后调用。该钩子被调用后，对应 Vue 实例的所有指令都被解绑，所有的事件监听器被移除，所有的子实例也都被销毁。**该钩子在服务器端渲染期间不被调用。**



## 生命周期图（中文翻译）

{% asset_img  vue2生命周期.png %}



## Vue的el属性和$mount优先级？

同时存在时，`el > $mount`



## 生命周期输出（代码）

```js
<template>
	<div>
		{{message}}
		<button @click="changeMessage">点我更新</button>
	</div>
</template>

<script>
	export default{
		name: 'Life',
		data(){
			return {
				message: 'hello vue!'
			}
		},
		methods: {
			a(){
				console.log('a');
			},
			changeMessage(){
				this.message = '我是更新后的message!'
			}
		},
		beforeCreate() {
				console.log('------初始化前------')
				console.log(this.message) // undefined
				console.log(this.$el) // undefined
		},
		created () {
				console.log('------初始化完成------')
				console.log(this.message) // hello vue!
				console.log(this.$el) // undefined
		},
		beforeMount () {
				console.log('------挂载前---------')
				console.log(this.message) // hello vue!
				console.log(this.$el) // undefined
		},
		mounted () {
				console.log('------挂载完成---------')
				console.log(this.message) // hello vue!
				console.log(this.$el) // <div>...</div>
		},
		beforeUpdate () {
				console.log('------更新前---------')
				console.log(this.message) // 我是更新后的message!
				console.log(this.$el)  // <div>...</div>
		},
		updated() {
				console.log('------更新后---------')
				console.log(this.message)  // 我是更新后的message!
				console.log(this.$el)  // <div>...</div>
		}
	}
</script>

```



## 生命周期流程（文字）

参考https://blog.csdn.net/weixin_42707287/article/details/111641286

首先，从图上，我们可以看出，他的一个过程是

1. new Vue()实例化一个vue实例，然后init初始化event 和 lifecycle， 其实这个过程中分别调用了3个初始化函数（initLifecycle(), initEvents(), initRender()），分别初始化了生命周期，事件以及定义createElement函数，初始化生命周期时，定义了一些属性，比如表示当前状态生命周期状态得 isMounted ，isDestroyed ，isBeingDestroyed[判断vue实例是否正在被销毁]，表示keep-alive中组件状态的_inactive，而初始化event时，实际上就是定义了$once[监听一个自定义事件，但是只触发一次。一旦触发之后，[监听器](https://so.csdn.net/so/search?q=监听器&spm=1001.2101.3001.7020)就会被移除。]、$off、$emit、$on几个函数。而createElement函数是在初始化render时定义的（调用了initRender函数）
2. 执行beforeCreate生命周期函数
3. beforeCreate执行完后，会开始进行数据初始化，这个过程，会定义data数据，方法以及事件，并且完成数据劫持observe以及给组件实例配置watcher观察者实例。这样，后续当数据发生变化时，才能感知到数据的变化并完成页面的渲染
4. 执行created生命周期函数，所以，当这个函数执行的时候，我们已经可以拿到data下的数据以及methods下的方法了，所以在这里，我们可以开始调用方法进行数据请求了
5. created执行完后，我们可以看到，这里有个判断，判断当前是否有el参数(这里为什么需要判断，是因为我们后面的操作是会依赖这个el的，后面会详细说)，如果有，我们再看是否有template参数。如果没有el，那么我们会等待调用$mount(el)方法(后面会详细说)。
6. 确保有了el后，继续往下走，判断当有template参数时，我们会选择去将template模板转换成render函数（其实在这前面是还有一个判断的，判断当前是否有render函数，如果有的话，则会直接去渲染当前的render函数，如果没有那么我们才开始去查找是否有template模板），如果没有template，那么我们就会直接将获取到的el（也就是我们常见的#app，#app里面可能还会有其他标签）编译成templae, 然后在将这个template转换成render函数。
7. 之后再调用beforMount， 也就是说实际从creted到beforeMount之间，最主要的工作就是将模板或者el转换为render函数。并且我们可以看出一点，就是你不管是用el，还是用template, 或者是用我们最常用的.vue文件(如果是.vue文件，他其实是会先编译成为template)，最终他都是会被转换为render函数的。
8. beforeMount调用后，我们是不是要开始渲染render函数了，首先我们会先生产一个虚拟dom（用于后续数据发生变化时，新老虚拟dom对比计算），进行保存，然后再开始将render渲染成为真实的dom。渲染成真实dom后，会将渲染出来的真实dom替换掉原来的vm.$el（这一步我们可能不理解，请耐心往下看，后面我会举例说明）,然后再将替换后的$el append到我们的页面内。整个初步流程就算是走完了
9. 之后再调用mounted，并将标识生命周期的一个属性_isMounted 置为true。所以mounted函数内，我们是可以操作dom的，因为这个时候dom已经渲染完成了。
10. 再之后，只有当我们状态数据发生变化时,我们在触发 beforeUpdate，要开始将我们变化后的数据渲染到页面上了（实际上这里是有个判断的，判断当前的 isMounted 是不是为ture并且 isDestroyed 是不是为false，也就是说，保证dom已经被挂载的情况下，且当前组件并未被销毁，才会走update流程）
11. beforeUpdate调用之后，我们又会重新生成一个新的虚拟dom(Vnode)，然后会拿这个最新的Vnode和原来的Vnode去做一个diff算，这里就涉及到一系列的计算，算出最小的更新范围，从而更新render函数中的最新数据，再将更新后的render函数渲染成真实dom。也就完成了我们的数据更新
12. 然后再执行updated，所以updated里面也可以操作dom，并拿到最新更新后的dom。不过这里我要插一句话了，mouted和updated的执行，并不会等待所有子组件都被挂载完成后再执行，所以如果你希望所有视图都更新完毕后再做些什么事情，那么你最好在mouted或者updated中加一个$nextTick（），然后把要做的事情放在$netTick()中去做（至于为什么，以后讲到$nextTick再说吧）
13. 再之后beforeDestroy没啥说的，实例销毁前，也就是说在这个函数内，你还是可以操作实例的
14. 之后会做一系列的销毁动作，解除各种数据引用，移除事件监听，删除组件watcher，删除子实例，删除自身self等。同时将实例属性 isDestroyed 置为true
15. 销毁完成后，再执行destroyed

## mounted、beforeUpdate、updated

- `mounted` 直译就是挂载完毕，它表示的应当是已经渲染完毕了，具体的页面已经呈现出来了，内存中的模版成功挂载到页面
- `beforeUpdate` 直译就是更新前，它表示的应当是页面有某处即将更新时触发，但此时，页面并没有更新，由数据更新得知即将更新的信息，页面没有和data中的数据保持一致
- `updated` 直译就是更新后，它表示的应当是页面与data中数据保持一致的状态，数据更新，页面也更新结束的状态

由上可以分析，当mounted结束以后，一个完整的页面也结束了，当这个页面或数据再没有任何变化时，就不会执行其他函数了，该实例已经被完全创建好了。一旦有某个数据发生了变化，便会立即触发beforeUpdate，当页面也更新结束便会触发updated。



## 生命周期的每个阶段适合做什么?

**created：** 在Vue实例创建完毕状态，我们可以去访问data、computed、watch、methods上的方法和数据，但现在还没有将虚拟Dom挂载到真实Dom上，所以我们在此时访问不到我们的Dom元素（el属性，ref属性此时都为空）。我们在此时可以进行一些简单的Ajax，并可以对页面进行初始化之类的操作。

**beforeMount：** 它是在挂载之前被调用的，会在此时去找到虚拟Dom，并将其编译成Render。

**mounted：** 虚拟Dom已经被挂载到真实Dom上，此时我们可以获取Dom节点，`$ref`在此时也是可以访问的。我们在此时可以去获取节点信息，做Ajax请求，对节点做一些操作。

**beforeupdate：** 响应式数据更新的时候会被调用，`beforeupdate`的阶段虚拟Dom还没更新，所以在此时依旧可以访问现有的Dom。我们可以在此时访问现有的Dom，手动移除一些添加的监听事件。

**updated：** 此时补丁已经打完了，Dom已经更新完毕，可以执行一些依赖新Dom的操作。但还是不建议在此时进行数据操作，避免进入死循环。

**beforeDestroy：** 在Vue实例销毁之前被调用，在此时我们的实例还未被销毁。在此时可以做一些操作，比如销毁定时器，解绑全局事件，销毁插件对象等。



## 请求放在哪个生命周期更合适？

一般来说，会有两种回答：`created`和`mounted`。 上文已经讲了，这两个回答，前者是数据已经准备好了，后者是连dom也已经加载完成了，那么到底哪个才是正确答案呢？

其实，两个都是可以的，但是`mounted`会更好。

**有人说：**`created`的时间会更早，早些调用不是会省很多时间吗？这样性能会不会更高一点

- 首先，它确实是早了，但是早不了几微秒，所以这其实没有提高性能
- 其次，我们在`created`阶段并没有去做渲染，所以在接下来我们会去做Dom渲染，但是如果此时我们还做了Ajax操作，在Ajax结束之后就会返回数据，我们就会将其插入到主线程中去运行，去处理数据，但是我们要知道，**在浏览器机制中，渲染线程跟js线程是互斥的**，所以**有可能**我们做渲染的同时，另一边可能要处理Ajax返回的数据了，这时候渲染就**有可能被打断**，在处理完数组后，去进行重新渲染。

那如果在`created`中有多个Ajax呢？我们又要重新进行渲染，所以在`created`去做Ajax请求这明显不太合适。

- 还有，有的时候我们接到返回的数据的时候可能要在回调函数中去进行一些Dom的操作，可是`created`阶段我们还没有将真实Dom加载出来，所以相对而言我们还是在`mounted`去调用要好一些

如果是服务端渲染，我们将其放入`created`中进行，因为服务端不支持`mounted`。



## 父子组件生命周期怎么执行的？

挂载阶段：父组件 beforeMount -> 子组件 created -> 子组件 mounted -> 父组件 mounted

更新阶段：父组件 beforeUpdate -> 子组件 beforeUpdate -> 子组件 updated -> 父组件 updated

销毁阶段：父组件 beforeDestroy -> 子组件 beforeDestroy -> 子组件 destroyed -> 父组件 destroyed



# 2、data observer是什么？

{% asset_img  dataObserver.png %}

对于上图的描述：通过Observer类提供的方法，将data转化为具有getter以及setter的数据。并在getter中收集依赖装在Dep中，Dep中装的是Watcher实例。当外界发生变化的时候，通知Watcher重新渲染视图。



# 3、vue中inject和provide用法

**provide：** 选项应该是一个对象或返回一个对象的函数。该对象包含可注入其子孙的property。

**inject：**可以是一个字符串数组、也可以是一个对象。

说白了，就是provide在祖先组件中注入，inject 在需要使用的地方引入即可。

我们可以把依赖注入看做一部分大范围的prop，只不过它以下特点：

祖先组件不需要知道哪些后代组件使用它提供的属性

后代组件不需要知道被注入的属性是来自那里

**注意：**provide 和 inject 绑定并不是可响应的。这是刻意为之的。然而，如果你传入了一个可监听的对象，那么其对象的 property 还是可响应的。

即：引用数据类型响应式，基础数据类型不响应式

**作用：刷新vue组件**

**使用方法：**

在APP.vue中

```javascript
 provide(){
            return {
                reload:this.reload
            }
        },
methods:{
            reload(){
                this.isRouterAlive = false
                this.$nextTick(function(){
                    this.isRouterAlive = true
                })
            }
        }
```

子组件中使用：

```javascript
inject: ['reload'],
```

引入后直接`this.reload()`即可

# 4、nextTick && 微任务 &&  原理

## 用处

$nextTick：在下次DOM更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的DOM。

$nextTick：可以保证页面中的结构一定是有的，经常和很多插件一起使用【都需要DOM存在】

## 为什么nextTick优先是微任务？

优先是`Promise.then`方法，是个微任务，这样可以避免多一次队列，进而少一次UI渲染，节省性能

## 原理

维护一个数组，每次调用时讲回调函数压入这个数组，然后优先选择微任务，在微任务回调中去执行数组中的所有回调，同时维护一个布尔值，确保每一次队列进行一次执行数组所有回调.

```js
let callbacks = []; //回调函数
let pending = false;
function flushCallbacks() {
  pending = false; //把标志还原为false
  // 依次执行回调
  for (let i = 0; i < callbacks.length; i++) {
    callbacks[i]();
  }
}
let timerFunc; //先采用微任务并按照优先级优雅降级的方式实现异步刷新
if (typeof Promise !== "undefined") {
  // 如果支持promise
  const p = Promise.resolve();
  timerFunc = () => {
    p.then(flushCallbacks);
  };
} else if (typeof MutationObserver !== "undefined") {
  // MutationObserver 主要是监听dom变化 也是一个异步方法
  let counter = 1;
  const observer = new MutationObserver(flushCallbacks);
  const textNode = document.createTextNode(String(counter));
  observer.observe(textNode, {
    characterData: true,
  });
  timerFunc = () => {
    counter = (counter + 1) % 2;
    textNode.data = String(counter);
  };
} else if (typeof setImmediate !== "undefined") {
  // 如果前面都不支持 判断setImmediate
  timerFunc = () => {
    setImmediate(flushCallbacks);
  };
} else {
  // 最后降级采用setTimeout
  timerFunc = () => {
    setTimeout(flushCallbacks, 0);
  };
}

export function nextTick(cb) {
  callbacks.push(cb);
  if (!pending) {
    pending = true;
    timerFunc();
  }
}
```



# 5、Vue模板编译原理

```
https://juejin.cn/post/6969563640416436232
```

# 6、Vue虚拟dom与虚拟DOM怎么生成的

## 什么是虚拟DOM

虚拟DOM是通过JavaScript来模拟了一个真实的DOM树，其中包含了真实DOM树中的结构信息。对数据和状态所做的任何改动，都会被自动且高效的同步到虚拟DOM，最后再批量同步到DOM中。

在react，vue等技术出现之前，我们要改变页面展示的内容只能通过遍历查询 dom 树的方式找到需要修改的 dom 然后修改样式行为或者结构，来达到更新 ui 的目的。

## 为什么用虚拟DOM来描述真实的DOM

创建真实DOM成本比较高，而如果用js对象来描述一个dom节点，成本比较低，另外我们在频繁操作dom是一种比较大的开销。所以建议用虚拟dom来描述真实dom。

- **为什么操作真实DOM的成本比较高？**
  1、dom 树的实现模块和 js 模块是分开的这些跨模块的通讯增加了成本

  2、 dom 操作引起的浏览器的回流和重绘，使得性能开销巨大。

  原本在 pc 端是没有性能问题的，因为pc端的计算能力强，但是随着移动端的发展，越来越多的网页在智能手机上运行，而手机的性能参差不齐，会有性能问题。
  我们之前用jquery在pc端写那些商城页面都没有问题，但放到移动端浏览器访问之后会发现除了首页会出现白屏之外在其他页面的操作并不流畅。

- **html页面如何解析的**
  1、解析html：会按顺序解析。浏览器有专门的html解析器来解析HTML，并在解析的过程中构建DOM树
  2、构建dom树：它和步骤html是同步进行，可以理解为边解析边构建。
  3、构建呈现树renderTree：将dom树与css结合，也就是将样式应用到dom节点上
  4、布局：计算呈现树节点的大小和位置，这一位置是递归进行的
  5、绘制：布局完成后，便是将呈现树绘制出来显示在屏幕上

## 虚拟DOM的作用

1、可以维护视图和状态之间的关系
2、复杂视图情况下提升渲染性能
3、虚拟DOM除了可以渲染成DOM节点，还可以渲染到其他平台如ssr(nuxt.js/next.js)、原生应用（weex/rn）、小程序等，增加了跨平台能力

## 如何创建虚拟DOM

`createElement` 创建 `VNode` 的过程，每个 `VNode` 有 `children`，`children` 每个元素也是一个`VNode`，这样就形成了一个虚拟树结构，用于描述真实的`DOM`树结构

# 7、v-if和v-show的区别

- v-if的原理是根据判断条件来动态的进行增删DOM元素，v-show是根据判断条件来动态的进行显示和隐藏元素，频繁的进行增删DOM操作会影响页面加载速度和性能，项目程序不是很大的时候，v-if和v-show都可以用来进行判断展示和隐藏（这种场景使用v-if只是影响不大，并不是没有影响）。

- v-if切换有一个局部编译/卸载的过程，切换过程中合适地销毁和重建内部的事件监听和子组件；v-show只是简单的基于css切换；
- v-if是惰性的，如果初始条件为假，则什么也不做；只有在条件第一次变为真时才开始局部编译（编译被缓存？编译被缓存后，然后再切换的时候进行局部卸载); v-show是在任何条件下（首次条件是否为真）都被编译，然后被缓存，而且DOM元素保留；
- v-if有更高的切换消耗；v-show有更高的初始渲染消耗；
- 如果需要频繁的切换，使用v-show比较好，如果运行条件很少改变，使用v-if比较好。
- v-if适合运营条件不大可能改变；v-show适合频繁切换。

# 8、computed和watch的区别&&原理

## 区别

1、功能上：computed是计算属性，watch是监听一个值的变化，然后执行对应的回调。
2、是否调用缓存：computed中的函数所依赖的属性没有发生变化，那么调用当前的函数的时候会从缓存中读取，而watch在每次监听的值发生变化的时候都会执行回调。
3、是否调用return：computed中的函数必须要用return返回，watch中的函数不是必须要用return。
4、computed默认第一次加载的时候就开始监听；watch默认第一次加载不做监听，如果需要第一次加载做监听，添加immediate属性，设置为true**（immediate:true）**
5、使用场景：computed----当一个属性受多个属性影响的时候，使用computed-----购物车商品结算。watch–当一条数据影响多条数据的时候，使用watch-----搜索框.

## 原理

```
https://juejin.cn/post/6974293549135167495
```



# 9、vue和React的区别

比较的是vue2

相同：

- 都使用了虚拟dom
- 组件化开发
- 都是单项数据流（父子组件之间，不建议修改父传下来的数据）
- 都支持服务器渲染

不同点：

- react的jsx，vue的template
- 数据变化，React需要setState，vue自动（初始化已响应式处理，Object.defineProperty，Proxy）
- React单向绑定，vue双向绑定
- react的redux、mobx，vue的vuex、pinia

# 10、Vue的优点和缺点

**优点：**

- 渐进式
- 组件化开发
- 虚拟dom
- 响应式数据
- 单页面路由
- 数据与视图分开

**缺点：**

- 单页面不利于seo
- 不兼容IE
- 首屏加载时间长

# 11、为什么说Vue是一个渐进式框架？

简单地说，渐进式的概念是分层设计，每层可选，不同层可以灵活接入其他方案架构模式。

{% asset_img  渐进式.png %}

- declarative rendering（声明式渲染）
- component system（组件系统）
- client-side routing（前端路由）
- state management（状态管理）
- build system（构建系统）

# 12、MVVM是什么？和MVC有何区别呢？

## MVC

- Model：负责从数据库中取数据
- View：负责展示数据
- Controller：用户交互的地方，例如点击事件
- 思想：Controller将Model的数据展示在View上

{% asset_img  MVC.png %}

## MVVM

- VM：就是View-Model，数据双向绑定
- Model：取数据的地方
- View：展示数据的地方
- 思想：View和Model实现数据同步，不需要手动更新

> 注意：Vue不严格符合MVVM，因为MVVM规定Model和View不能直接通信，而Vue可以使用`ref`进行通信

{% asset_img  MVVM.png %}

# 13、Vue和JQuery的区别在哪？为什么放弃JQuery用Vue？

- JQuery直接操作DOM，而Vue不直接操作DOM，而是只需操作数据
- Vue的虚拟DOM技术，能适配多端
- Vue集成了一些库，大大提高开发效率，例如Route、Vuex等等

# 14、为什么data是个函数并且返回一个对象呢？

组件是一个可复用的实例，当你引用一个组件的时候，组件里的data是一个普通的对象，所有用到这个组件的都引用的同一个data，就会造成数据污染。

防止组件被多个页面使用时，造成的变量互相污染

# 15、使用过哪些Vue的修饰符呢？

`.lazy`：输入框失焦时才会更新v-model的值

`.trim`：讲v-model绑定的值首位空格给去掉

`.number`：将v-medol绑定的值转数字

`.stop`：阻止事件冒泡

`.capture`：事件的捕获

`.self`：点击事件绑定本身才触发

`.once`：事件只触发一次

`.prevent`：阻止默认事件

`.native`：绑定事件在自定义组件上时，确保能执行

`.left、.middle、.right`：鼠标左中右键的触发

`passive`：相当于给移动端滚动事件加一个`.lazy`

`camel`：确保变量名会被识别成驼峰命名

`.sync`：简化子修改父值的步骤

# 16、路由的几种模式？

- hash：哈希模式，根据hash值的更改进行组件切换，而不刷新页面
- history：历史模式，依赖于HTML5的pushState和replaceState
- abstract：适用于Node

# 17、路由的钩子函数？

## 全局守卫

```js
// 全局守卫 ---  前置守卫：（在路由跳转之前进行判断）
router.beforeEach(async (to, from, next) => {
  
});
```

## 路由独享守卫

```js
{
    path: '/pay',
    component: Pay,
    // 路由独享守卫
    beforeEnter: (to, from, next) => {
    }
  },
  {
    path: '/trade',
    component: Trade,
    // 路由独享守卫
    beforeEnter: (to, from, next) => {
    }
  }
}
```

## 组件内守卫

```js
<script>
  export default {
    name: 'PaySuccess',
    beforeRouteEnter (to, from, next) {
      // 在渲染该组件的对应路由被confirm前调用
      // 不!能!获取组件实例this
      // 因为当守卫执行前,组件实例还没被创建
    },
    beforeRouteUpdate(to, from, next){
      // 在当前路由改变,但是该组件被复用时调用
      // 举例来说，对于一个带有动态参数的路径/foo/:id,在/foo/1和/foo/2之间跳转的时候,
      // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
      // 可以访问组件实例 this
      // 当路由中的占位数据发生改变的时候，才会触发
    },
    beforeRouteLeave (to, from, next) {
      // 导航离开组件的时候被调用
      // 可以访问组件实例的 this
    }
  }
</script>
```

# 18、使用过哪些Vue的内部指令呢？

- `v-text`：元素的textContent
- `v-html`：元素的innerHTML
- `v-show`：通过样式display改变显隐
- `v-if`：通过操作DOM改变显隐
- `v-else`：配合v-if
- `v-else-if`：配合v-else
- `v-for`：循环渲染
- `v-on`：绑定事件，缩写@
- `v-bind`：绑定变量，缩写:
- `v-model`：双向绑定
- `v-slot`：插槽
- `v-once`：只渲染一次
- `v-pre`：跳过元素编译
- `v-cloak`：隐藏双括号，有值再显示

# 19、组件通信的方式

## 一：props

**场景：**父子通信

注意事项：

如果父组件给子组件传递数据（函数）：本质是子组件给父组件传递数据

如果父组件给子组件传递的数据（非函数）：本质就是父组件给子组件传递数据

书写方式： 3种

```js
props: ['propOne', 'propTwo']

props: {
    'propOne': Object,
    'propTwo': [String, Number] // 必须是字符串或数字类型
}

props: {
	propOne: {
        type: Number,
        required: true,
        default: 0
    },
   	// 如果是数组或对象，默认值必须是一个函数来返回
    prosTwo: {
        type: Object,
        default() {
        	return {}
        }
    },
    propThree: {
        type: Array,
        default: () => []
    },
    // 自定义一个验证函数
    propFour: {
        validator: (value) => {
            return value > 1
        }
    }
}

```

小提示：路由的props

书写形式： 布尔值，对象、函数形式

```js
{
    path:"/about/:id",
    name:"about",
    component:About,
    props:true
},

{path:"/about/:id",name:"about",component:About,
    props:{
        intro:"百度",
        site:"http://www.baidu.com"
    }
},

{path:"/about/:id",name:"about",component:About,
    props:(route)=>(  {params:route.params, a:1, b:2}  )}

```

## 二：自定义事件

**场景**：子组件给父组件传递数据

$on    $emit

## 三：全局事件总线

**场景**：全能

组件初始化的时候在main.js中：  Vue.$prototype.$bus = this;

$bus.$emit   $bus.$on

## 四：pubsub-js

在React框架中使用比较多

**场景**：万能

## 五：Vuex

**场景**：万能

## 六：插槽

**场景**：父子组件通信----（一般结构）

默认插槽

具名插槽

作用域插槽

# 20、如何设置动态class，动态style？

- 动态class对象：`<div :class="{ 'is-active': true, 'red': isRed }"></div>`
- 动态class数组：`<div :class="['is-active', isRed ? 'red' : '' ]"></div>`
- 动态style对象：`<div :style="{ color: textColor, fontSize: '18px' }"></div>`

# 21、为什么v-if和v-for不建议用在同一标签？

`v-for`优先级高于`v-if`，每项都通过`v-for`渲染出来后再去通过`v-if`判断显隐，做了很多无用功

# 22、vuex的有哪些属性？用处是什么？

- `state`：定义初始状态
- `getter`：从store从取数据
- `mutation`：更改store中状态，只能同步操作
- `action`：用于提交mutation，而不直接更改状态，可异步操作
- `module`：store的模块拆分

{% asset_img  vuex.png %}

# 23、不需要响应式的数据应该怎么处理？

- 定义在data的return之外
- 使用Object.freeze进行数据冻结

# 24、watch有哪些属性，分别有什么用？

- immediate：初次加载时立即执行
- deep：是否进行深度监听
- handler：监听的回调函数

# 25、对象新属性无法更新视图，删除属性无法更新视图，为什么？怎么办？

- 原因：Object.defineProperty没有对对象的新属性进行劫持

- 解决：

- - 新增属性：`Vue.set(target, key, value)`
  - 删除属性：`Vue.delete(target, key)`

# 26、直接arr[index] = xxx无法更新视图怎么办？为什么？怎么办？

- 原因：Vue出于性能考虑没有对数组下标进行劫持，而是通过改写数组原型方法

- 解决：

- - `splice：arr.splice(index, 1, value)`
  - `Vue.set(target, index, value)`

# 27、插槽的使用

## 单个插槽 | 匿名插槽

```vue
//子组件 ： (假设名为：child)
<template>
  <div class= 'child'>
      <slot></slot>
  </div>
</template>

//父组件：（引用子组件 child）
<template>
  <div class= 'app'>
     <child> 
        林三心
     </child>
  </div>
</template>
```

**编译作用域------父组件引用子组件的data**

直接传入子组件内的数据是不可以的。因为：父级模板里的所有内容都是在父级作用域中编译的；子模板里的所有内容都是在子作用域中编译的。

```vue
// 子组件 ： (假设名为：child)
<template>
  <div class= 'child'>
       <slot></slot>
  </div>
</template>

new Vue({
  el:'child',
  data:{
    child:'子组件'
  }
})

// 父组件：（引用子组件 child）

<template>
  <div class= 'app'>
     <child> {{ child }}</child>
  </div>
</template>
```

后备内容 (子组件`<slot></slot>`设置默认值)

```vue
//子组件 ： (假设名为：child)
<template>
  <div class='child'>
      <slot>这就是默认值</slot>
  </div>
</template>
```

## 具名插槽 （子组件多个slot对应插入内容）

```vue
//子组件 ： (假设名为：child)
<template>
  <div class= 'child'>
      <slot name='one'> 这就是默认值1</slot>
      <slot name='two'> 这就是默认值2 </slot>
      <slot name='three'> 这就是默认值3 </slot>
  </div>
</template>
```

父组件通过，或`slot="name"（旧语法）`，`v-slot:name`或`#name（新语法）` 的方式添加内容：

```vue
//父组件：（引用子组件 child）
<template>
  <div class= 'app'>
     <child> 
        <template v-slot:"one"> 这是插入到one插槽的内容 </template>
        <template v-slot:"two"> 这是插入到two插槽的内容 </template>
        <template v-slot:"three"> 这是插入到three插槽的内容 </template>
     </child>
  </div>
</template>
```

## 作用域插槽 (父组件在子组件slot处使用子组件 data)

> 通过`slot` 我们可以在父组件为子组件添加内容，通过给`slot`命名的方式，我们可以添加不止一个位置的内容。但是我们添加的数据都是父组件内的。上面我们说过不能直接使用子组件内的数据，但是我们是否有其他的方法，让我们能够使用子组件的数据呢？ 其实我们也可以使用`slot-scope`的方式：

```vue
//子组件 ： (假设名为：child)
<template>
  <div class= 'child'>
      <slot name= 'one' :value1='child1'> 这就是默认值1</slot>    //绑定child1的数据
      <slot :value2='child2'> 这就是默认值2 </slot>  //绑定child2的数据，这里我没有命名slot
  </div>           
</template>

new Vue({
  el:'child',
  data:{
    child1:'数据1',
    child2:'数据2'
  }
})

//父组件：（引用子组件 child）
<template>
  <div class='app'>
     <child> 
        <template v-slot:one='slotone'>  
           {{ slotone.value1 }}    // 通过v-slot的语法 将子组件的value1值赋值给slotone 
        </template>
        <template v-slot:default='slotde'> 
           {{ slotde.value2 }}  // 同上，由于子组件没有给slot命名，默认值就为default
        </template>
     </child>
  </div>
</template>
```

## 动态插槽名称

动态指令参数 也适用于 v-slot ，允许我们定义动态插槽名称：

```vue
<base-layout>
  <template v-slot:[dynamicSlotName]>
    ...
  </template>
</base-layout>
```

## 命名插槽简写

与 v-on 和 v-bind 类似，v-slot 也有一个简写，**即使用 # 代替 v-slot**。例如， v-slot:header 简写成 #header:

```vue
<base-layout>
  <template #header>
    <h1>Here might be a page title</h1>
  </template>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template #footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
```

和其他指令一样，只有在提供参数时才能使用简写形式，下面的写法是无效的：

```vue
<!-- 将会触发一个控制台警告 -->
<current-user #="{ user }">
  {{ user.firstName }}
</current-user>
```

也就是说，如果你想使用简写语法，则务必指定插值的名字：

```vue
<current-user #default="{ user }">
  {{ user.firstName }}
</current-user>
```

## 总结：

1、原来的vue插槽有三种 ：匿名插槽 具名插槽和作用域插槽（solt-scope）
2、vue2.6+之后采用v-slot指令来代替原来的三种插槽
3、v-slot代替作用域插槽的时候子组件仍然需要绑定向父组件暴露的属性
4、v-slot属性可以简写成# 但是后面必须带有插槽名称才能简写
5、v-slot可以直接访问组件绑定的属性 不需要通过取名字 v-slot="{ user }"
6、v-slot最好都写在`<templete>`中



## 插槽的原理？

**普通插槽** 普通插槽`slot`会被当做子元素进行解析，最终会被解析成一个`_t`函数，他接收的第一个参数为插槽的名称，默认是`default`，也就是`_t('default')`，执行此函数进行最终元素的渲染，如果是具名插槽，则传对应的插槽名 **作用域插槽** 插槽会被封装成一个函数放置在`scopeSlotes`对象中，解析时`_t`第二个参数接收子组件的数据，并进行渲染



## Slot插槽是怎么“插”的（通俗版）

### 普通插槽

```js
//子组件 ： (假设名为：child)
<template>
  <div class='child'>
      我在子组件里面
      <slot></slot>
      <slot name="one"></slot>
  </div>
</template>

//父组件：（引用子组件 child）
<template>
  <div class= 'app'>
     <child> 
        这是插入到默认插槽的内容 {{parent}}
        <template v-slot:"one"> 这是插入到one插槽的内容 {{parent}}</template>
     </child>
  </div>
</template>

new Vue({
  el:'.app',
  data:{
    parent:'父组件的值'
  }
})
```

1. 父组件先解析，把 `child` 当做子元素处理，把 插槽当做 `child` 的子元素处理，并且在父组件作用域内得出了parent变量的值，生成这样的节点：

```js
{    
  tag: "div",    
   children: [{        
      tag: "child",        
      children: ['这是插入到默认插槽的内容 父组件的值', 
                  '这是插入到one插槽的内容 父组件的值']
  }]
}
```

1. 子组件解析，`slot` 作为一个占位符，会被解析成一个函数，大概意思像 解析成下面

```js
{    
    tag: "div",    
    children: [
        '我在子组件里面',
        _t('default'), // 匿名插槽，默认名称为default
        _t('one') // 具名插槽，名称为one
    ]
}
```

1. _t函数需要传入插槽名称，默认是`default`，具名插槽则传入`name`，这个函数的作用，是把第一步解析得到的插槽节点拿到，然后返回解析后的节点，那么子组件的节点就完整了，插槽也成功认了爹——`div`标签

```js
{    
    tag: "div",    
    children: ['我在子组件里面', 
                '这是插入到默认插槽的内容 父组件的值', 
                '这是插入到one插槽的内容 父组件的值']
}
```

### 作用域插槽

```js
//子组件 ： (假设名为：child)
<template>
  <div class= 'child'>
      <slot :value1='child1' :value2='child1'></slot>
      <slot name='one' :value1='child2' :value2='child2'></slot>
  </div>           
</template>

new Vue({
  el:'child',
  data:{
    child1: '子数据1',
    child2: '子数据2'
  }
})

//父组件：（引用子组件 child）
<template>
  <div class='app'>
     <child> 
         <template v-slot:default='slotde'> 
            插入默认 slot 中{{ slotde.value1 }}{{ slotde.value2 }}
        </template>
        <template v-slot:one='slotone'> 
            插入one slot 中{{ slotone.value1 }}{{ slotone.value2 }}
        </template>
     </child>
  </div>
</template>
```

1. 过程很复杂，这里就通俗点讲了，父组件先解析，遇到作用域插槽，会将此插槽封装成一个函数保存到子元素 `child` 下

```js
{    
 tag: "div",    
  children: [{        
     tag: "child"
     scopeSlots:{            
         default (data) { // 记住这个data参数               
             return ['插入one slot 中插入默认 slot 中' + data.value1 + data.value2]
         },
         one (data) { // 记住这个data参数             
             return ['插入one slot 中' + data.value1 + data.value2]
         }
     }
    }]
}
```

2.轮到子组件解析了，这个时候t函数又登场了，并且子组件将对应的插槽数据包装成一个对象，传进t函数

```js
{    
 tag: "div",    
   children: [
     '我在子组件里面',
      _t('default',{value1: '子数据1', value2: '子数据1'}),
      _t('one',{value1: '子数据2', value2: '子数据2'})
      
    ]
  }
复制代码
```

> 接下来就是_t内部执行，包装后的对象被当做data参数传入了scopeSlots中的对应的各个函数，解析成：

```js
{    
  tag: "div",    
   children: [
      '我在子组件里面', 
      '插入默认 slot 中 子数据1 子数据1',
      '插入one slot 中 子数据2 子数据2'
   ]
}
复制代码
```

### $slots

> 看到这，相信大家已经清楚了大概一个过程（虽然不是很详细），那么又有一个疑问了，这些解析后的`节点VNode`对象，是存在哪儿呢？你总不能解析出来然后就扔了吧？肯定得找个地方存起来然后进行`真实dom`的渲染，这个地方就是`$slots`

```js
//子组件 ： (假设名为：child)
<template>
  <div class= 'child'>
      <slot></slot>
      <slot name='one'></slot>
      <slot name='two'></slot>
      <slot name='three'></slot>
  </div>
</template>

new Vue({
  el:'.child',
  created () {
      console.log(this.$slots) // 看看里面有啥
  }
})
复制代码
//父组件：（引用子组件 child）
<template>
  <div class= 'app'>
     <child> 
        <template> 这是插入到默认插槽的内容 </template>
        <template v-slot:"one"> 这是插入到one插槽的内容 </template>
        <template v-slot:"two"> 这是插入到two插槽的内容 </template>
        <template v-slot:"three"> 这是插入到three插槽的内容 </template>
     </child>
  </div>
</template>
复制代码
```

> console.log的结果：

{% asset_img  $slot.png %}

> 看到这里大家都懂了，`$slots`是一个`Map`，`key`是各个插槽的名称（匿名插槽的`key`为`default`），key对应的value都是各个插槽下面的VNode节点，具体VNode对象里都是长什么样，大家可以自己输出看看，里面东西太多，我就不在这展示了。

# 28、为什么不建议用index做key，为什么不建议用随机数做key？

举个例子

```vue
<div v-for="(item, index) in list" :key="index">{{item.name}}</div>

list: [
    { name: '小明', id: '123' },
    { name: '小红', id: '124' },
    { name: '小花', id: '125' }
]

渲染为
<div key="0">小明</div>
<div key="1">小红</div>
<div key="2">小花</div>

现在我执行 list.unshift({ name: '小林', id: '122' })

渲染为
<div key="0">小林</div>
<div key="1">小明</div>
<div key="2">小红</div>
<div key="3">小花</div>


新旧对比

<div key="0">小明</div>  <div key="0">小林</div>
<div key="1">小红</div>  <div key="1">小明</div>
<div key="2">小花</div>  <div key="2">小红</div>
                         <div key="3">小花</div>

可以看出，如果用index做key的话，其实是更新了原有的三项，并新增了小花，虽然达到了渲染目的，但是损耗性能

现在我们使用id来做key，渲染为

<div key="123">小明</div>
<div key="124">小红</div>
<div key="125">小花</div>

现在我执行 list.unshift({ name: '小林', id: '122' })，渲染为

<div key="122">小林</div>
<div key="123">小明</div>
<div key="124">小红</div>
<div key="125">小花</div>

新旧对比

                           <div key="122">小林</div>
<div key="123">小明</div>  <div key="123">小明</div>
<div key="124">小红</div>  <div key="124">小红</div>
<div key="125">小花</div>  <div key="125">小花</div>

可以看出，原有的三项都不变，只是新增了小林这个人，这才是最理想的结果
```

用`index`和用`随机数`都是同理，`随机数`每次都在变，做不到专一性，很`渣男`，也很消耗性能，所以，拒绝`渣男`，选择`老实人`

# 29、自定义指令的钩子函数？

这里只说Vue2的

- `bind`：指令绑定到指定元素时调用，只调用一次
- `inserted`：指定元素插入父节点时调用
- `update`：所在组件的 VNode 更新时调用
- `componnetUpdated`：所在组件以及其子组件 VNode 全部更新后调用
- `unbind`：只调用一次，指令与元素解绑时调用

# 30、Vue的SSR是什么？有什么好处？

SSR全称`Server Side Render`

- 有利于SEO：由于是在服务端，将数据填充进HTML之后再推送到浏览器，所以有利于SEO的爬取
- 首屏渲染快

# 31、为什么只对对象劫持，而要对数组进行方法重写？

数组的元素大概率是成百上千的，所以对数组下标进行劫持的话会非常消耗性能。Vue通过对数组原型上方法的重写，实现数组的响应式

# 32、Vue.set方法的原理？

Vue.set(target, key, value)

- 第一步：判断target是数组的话，则调用target.splice(key, 1, value)

- 第二步：判断target是对象的话，再判断传入的key是否已存在target中

- - 是：直接target[key] = value
  - 否：调用defineReactive(target, key, val)进行响应式处理

```js
export default function set(target, key, val) {
    if (Array.isArray(target)) {
        target.length = Math.max(target.length, key)
        target.splice(key, 1, val)
        return val
    }

    const ob = target.__ob__

    if (key in target && !(key in target.prototype) || !ob) {
        target[key] = val
        return val
    }

    defineReactive(target, key, val)
    return val
}
```

# 33、Vue.delete方法的原理？

Vue.delete(target, key)

- 第一步：判断target是否为数组，是的话调用target.splice(key, 1)
- 第二步：判断target是对象的话，调用delete关键字删除属性，并调用__ob__.dep.notify进行更新通知

```js
export default function del (target, key) {
    if (Array.isArray(target)) {
        target.splice(key, 1)
        return
    }

    const ob = target.__ob__

    if (!(key in target)) return

    delete target[key]

    if (!ob) return

    ob.dep.notify()
}
```

# 34、说说 虚拟dom 和 diff算法 吧？

## **虚拟dom**

虚拟dom是一个对象，一个描述真实DOM的对象，每次数据更新时，新旧虚拟DOM都会互相进行同层对比，而diff算法优化就是在此时做优化的。

## **diff算法**

- 第一步：调用`patch`方法，传入新旧虚拟DOM，开始同层对比

- 第二步：调用`isSameNode`方法，对比新旧节点是否同类型节点

- 第三步：如果不同，新节点直接代替旧节点

- 第四步：如果相同，调用`patchNode`进行对比节点

- - 如果旧节点和新节点都是文本节点，则新文本代替旧文本
  - 如果旧节点有子节点，新节点没，则删除旧节点的子节点
  - 如果旧节点没有子节点，新节点有，则把子节点新增上去
  - 如果都有子节点，则调用`updateChildren`方法进行新旧子节点的对比
  - 子节点对比为首尾对比法

# 35、如果子组件改变props里的数据会发生什么？

- 基础类型：如果父传给子的是基础类型，修改则会报错
- 引用类型：如果父传给子的是引用类型，修改属性则会同时修改父组件数据

# 36、props怎么自定义验证

```js
props: {
    num: {
      default: 1,
      validator: function (value) {
          // 返回值为false则验证不通过，报错
          return [
            1, 2, 3, 4, 5
          ].indexOf(value) !== -1
    }
    }
  }
}
```

# 37、watch监听一个对象时，如何排除某些属性的监听

```js
mounted() {
    Object.keys(this.params)
      .filter((_) => !["c", "d"].includes(_)) // 排除对c，d属性的监听
      .forEach((_) => {
        this.$watch((vm) => vm.params[_], handler, {
          deep: true,
        });
      });
  },
data() {
    return {
      params: {
        a: 1,
        b: 2,
        c: 3,
        d: 4
      },
    };
  },
watch: {
    params: {
      deep: true,
      handler() {
        this.getList;
      },
    },
  }
```

# 38、审查元素时发现data-v-xxxxx，这是啥？

样式模块化scoped的效果，在本组件的标签都会带上data-v-xxx的属性，然后通过属性选择器实现样式模块化的效果

# 39、vue的hook的使用？

- 同一组件中使用

这是我们常用的使用定时器的方式

```js
export default{
  data(){
    timer:null  
  },
  mounted(){
      this.timer = setInterval(()=>{
      //具体执行内容
      console.log('1');
    },1000);
  }
  beforeDestory(){
    clearInterval(this.timer);
    this.timer = null;
  }
}
```

上面做法不好的地方在于：得全局多定义一个timer变量，可以使用hook这么做：

```js
export default{
  methods:{
    fn(){
      let timer = setInterval(()=>{
        //具体执行代码
        console.log('1');
      },1000);
      this.$once('hook:beforeDestroy',()=>{
        clearInterval(timer);
        timer = null;
      })
    }
  }
}
```

- 父子组件使用

如果子组件需要在mounted时触发父组件的某一个函数，平时都会这么写：

```vue
//父组件
<rl-child @childMounted="childMountedHandle"/>
method () {
  childMountedHandle() {
  // do something...
  }
},

// 子组件
mounted () {
  this.$emit('childMounted')
},
```

使用hook的话可以更方便：

```vue
//父组件
<rl-child @hook:mounted="childMountedHandle"/>
method () {
  childMountedHandle() {
  // do something...
  }
},
```

# 40、动态指令和参数使用过吗？

```js
<aButton @[someEvent]="handleSomeEvent()" :[someProps]="1000" />
```

# 41、相同的路由组件如何重新渲染？

更改所有组件上的key即可

# 42、自定义v-model

```js
export default: {
  model: {
    event: 'change',
    prop: 'checked'  
  }
}
```

# 43、如何将获取data中某一个数据的初始状态？

使用`this.$options.data().xxx`获取初始值

# 44、计算变量时，methods和computed哪个好？

computed比较好，computed有缓存机制，可以节省性能。而method则每次更新都会重新计算，不考虑缓存

# 45、router.push、router.replace、router.go的区别？

- `router.push`：跳转，并向history栈中加一个记录，可以后退到上一个页面
- `router.replace`：跳转，不会向history栈中加一个记录，不可以后退到上一个页面
- `router.go`：传正数向前跳转，传负数向后跳转

# 46、v-for中key的作用,如果不要key呢?

key属性主要用在Vue的虚拟DOM算法，在新旧nodes对比时辨识VNodes,查看他们是否是属于同一个虚拟DOM结点；

使用key时，它会**基于key的变化重新排列元素顺序**，并且会**移除/销毁**key不存在的元素。

如果不使用key，vue会使用一种最大限度减少动态元素并且尽可能尝试就地**修改/复用**相同类型元素的算法。

# 47、vue中父、子组件相互调用的方法有哪些?

## 子组件调用父组件的方法

### **1. 直接在子组件中通过this.$parent.event来调用父组件的方法。**

**父组件**

```vue
<template>
  <div>
    <child></child>
  </div>
</template>
<script>
  import child from '~/components/dam/child';
  export default {
    components: {
      child
    },
    methods: {
      fatherMethod() {
        console.log('测试');
      }
    }
  };
</script>
```

**子组件**

```vue
<template>
  <div>
    <button @click="childMethod()">点击</button>
  </div>
</template>
<script>
  export default {
    methods: {
      childMethod() {
        this.$parent.fatherMethod();
      }
    }
  };
</script>
```

### **2. 子组件里用$emit向父组件触发一个事件，父组件监听这个事件就行了。**

### **3. 父组件把方法传入子组件中，在子组件里直接调用这个方法**

**父组件**

```vue
<template>
  <div>
    <child :fatherMethod="fatherMethod"></child>
  </div>
</template>
<script>
  import child from '~/components/dam/child';
  export default {
    components: {
      child
    },
    methods: {
      fatherMethod() {
        console.log('测试');
      }
    }
  };
</script>
```

**子组件**

```vue
<template>
  <div>
    <button @click="childMethod()">点击</button>
  </div>
</template>
<script>
  export default {
    props: {
      fatherMethod: {
        type: Function,
        default: null
      }
    },
    methods: {
      childMethod() {
        if (this.fatherMethod) {
          this.fatherMethod();
        }
      }
    }
  };
</script>
```



## 父组件调用子组件的方法

### 1. 通过ref直接调用子组件的方法



```vue
// 父组件
<template>
    <div>
        <Button @click="handleClick">点击调用子组件方法</Button>
        <Child ref="child"/>
    </div>
</template>    

<script>
import Child from './child';

export default {
    methods: {
        handleClick() {
              this.$refs.child.sing();
        },
    },
}
</script>


// 子组件

<template>
  <div>我是子组件</div>
</template>
<script>
export default {
  methods: {
    sing() {
      console.log('我是子组件的方法');
    },
  },
};
</script>
```

### 2. 通过组件的emit、on方法

```vue
//父组件中

<template>
    <div>
        <Button @click="handleClick">点击调用子组件方法</Button>
        <Child ref="child"/>
    </div>
</template>    

<script>
import Child from './child';

export default {
    methods: {
        handleClick() {
               this.$refs.child.$emit("childmethod")    //子组件$on中的名字
        },
    },
}
</script>

//子组件中

<template>
    <div>我是子组件</div>
</template>
<script>
export default {
    mounted() {
        this.$nextTick(function() {
            this.$on('childmethods', function() {
                console.log('我是子组件方法');
            });
        });
     },
};
</script>
```



# Vue响应式是怎么实现的？

- 1、劫持：通过`Object.defineProperty`对对象进行递归劫持属性的`get、set`
- 2、观察者模式：使用`watcher`进行观察数据使用的地方
- 3、发布订阅模式：使用`dep`收集`watcher`，数据更改时，通过`notify`方法通知`dep`里的`watcher`去进行相应的更新
- 4、数组

# Vue2 3区别











































