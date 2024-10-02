---
title: vue3学习笔记
date: 2022-06-08 09:59:46
tags: vue3
---

# 1.源码

<!-- more -->

**App.vue**

```vue
<template>
<!-- Vue2中的html模板中必须要有一对根标签，Vue3组件的html模板中可以没有根标签 -->
  <img alt="Vue logo" src="./assets/logo.png">
  <HelloWorld msg="Welcome to Your Vue.js + TypeScript App"/>
</template>

<script lang="ts">
// 这里可以使用ts的代码
// defineComponent函数，目的是定义一个组件，内部可以传入一个配置对象
import { defineComponent } from 'vue';
import HelloWorld from './components/HelloWorld.vue';

// 暴露出去一个定义号的组件
export default defineComponent({
  name: 'App',
  // 注册组件
  components: {
    // 注册一个子组件
    HelloWorld
  }
});
</script>
```

**main.ts**

```ts
// 程序的主入口文件，ts文件
// 引入createApp函数，创建对应的应用，产生应用的实例对象
import { createApp } from 'vue'
// 引入App组件（所有组件的父级组件）
import App from './App.vue'
// 创建App应用返回对应的实例对象，调用mount方法进行挂载
createApp(App).mount('#app')
```

**shims-vue.d.ts**

```ts
/* 
	eslint-disable 
	shims-vue.d.ts是为了typescript做的适配定义文件，因为.vue文件不是一个常规的文件类型，ts 是不能理解vue文件是干嘛的.加这一段是是告诉 ts, vue文件是这种类型的。这一段删除，会发现import的所有vue类型的文件都会报错。
*/
declare module '*.vue' {
  import type { DefineComponent } from 'vue'
  const component: DefineComponent<{}, {}, any>
  export default component
}
```

# 2.setup的初始

```vue
<template>
  <div>
    哈哈，我又变帅了
    <!-- <h1>{{number}}</h1> -->
  </div>
</template>

<script lang="ts">

import { defineComponent } from 'vue';

export default defineComponent({
  name: 'App',
  setup(){
    // 测试代码，setup是组合API中第一个要使用的函数
    console.log("我来了") ;
    const number = 10;
    return {
      number
    }
  }
});
</script>
```

# 3.setup和ref的基本使用

```vue
<template>
  <h2>setup和ref的基本使用</h2>
  <h3>{{count}}</h3>
  <button @click="updateCount">更新数据</button>
</template>

<script lang="ts">

import { defineComponent , ref} from 'vue';

export default defineComponent({
  name: 'App',
  // 需求：页面打开后直接可以看到一个数据，点击按钮后，该数据可以发生变化
  // vue2 的方式实现
  // data() {
  //   return {
  //     count: 0,
  //   };
  // },
  // methods: {
  //   updateCount(){
  //     this.count++;
  //   }
  // },

  // vue3 的方式实现
  // setup是组合API的入口函数

  // setup只在初始化时执行一次
  // 函数如果返回对象，对象中的属性或方法，模板中可以直接使用
  setup() {
    // 变量
    // let count = 0;  // 此时的数据并不是响应式的数据（响应式数据：数据变化，页面跟着渲染变化）
    const count = ref(0)
    // ref是一个函数，作用：定义一个响应式的数据，返回的是一个Ref对象，对象中又一个value属性，
    // 如果需要对数据进行操作，需要使用Ref对象调用value属性的方式进行数据的操作
    // html模板【就是template标签中】中是不需要使用.value属性的写法
    // 一般是用来定义一个【基本类型】的响应式数据
    // count 的类型 是  Ref 类型
    console.log(count)

    // 方法
    function updateCount(){
      console.log('=========');
      // 报错原因：count是一个Ref对象，对象是不能进行++的操作
      // count++;
      count.value++
    }
    // 返回的是一个对象
    return {
      // 属性
      count,
      updateCount
    }
  }
});
</script>
```

# 4.setup和reactive的基本使用

```vue
<template>
  <h2>reactive的使用</h2>
  <h3>名字：{{user.name}}</h3>
  <h3>性别: {{user.gender}}</h3>
  <h3>年龄：{{user.age}}</h3>
  <h3>媳妇：{{user.wife}}</h3>
  <hr>
  <button @click="updateUser">更新数据</button>
</template>

<script lang="ts">

import { defineComponent , ref, reactive} from 'vue';

export default defineComponent({
  name: 'App',

  // 需求：显示用户的相关信息，点击按钮，可以更新用户的相关信息数据 
  /*
    reactive
    作用：定义多个数据的响应式
    const proxy = reactive(obj)： 接受一个普通对象然后返回该普通对象的响应式代理器对象
    响应式转换是“深层的”： 会响应对象内部所有嵌套的属性
    内部基于ES6的proxy实现，通过代理对象操作源对象内部数据都是响应式的
  */
  setup() {
    // 把数据 变成响应式的数据
    // 返回的是一个proxy的代理对象，被代理的目标对象就是obj对象
    // user现在是代理对象，obj是目标对象

    // const user = reactive({
    //   name: '小明',
    //   age: 20,
    //   wife:{
    //     name: '小甜甜',
    //     age: 18,
    //     cars: ['奔驰', '奥迪', '宝马']
    //     }
    //   })

    // const obj: any = { // 为了在使用 obj.gender = ‘男’ 的时候，不提示错误信息
    const obj = {
      name: '小明',
      age: 20,
      wife:{
        name: '小甜甜',
        age: 18,
        cars: ['奔驰', '奥迪', '宝马']
        }
    }
    const user = reactive<any>(obj)

    console.log(user)
      
      // 方法
      // function updateUser(){}
      const updateUser = () => {
        // 直接使用目标对象的方式来更新目标对象中成员的值，是不可能的，只能使用代理对象的方式来更新数据（响应式数据）
        // obj.name += '==='
        // user.name += '小红'
        // user.age += 2
        // user.wife.name += '大甜甜'
        // user.wife.cars[0] = '玛莎拉蒂'

        // user  ----> 代理对象,user ----->目标对象
        // user对象或者obj对象添加一个新的属性，哪一种方式会影响界面的更新
        // 这种方式，界面没有更新渲染，obj确实添加了这个属性
        // obj.gender = '男'
        // 这种方式，界面可以更新渲染，而且这个数据最终也添加到了obj对象上
        // user.gender = '男'

        // user对象或者obj对象中移除一个已经存在的属性，哪一种方法会影响界面的更新
        // 这种方式，页面没有更新渲染， obj中确实没有了age这个属性
        //  delete obj.age
        // 这种方式，界面更新渲染，obj中确实没有了age这个属性
        // delete user.age


        // 总结： 如果操作代理对象，目标对象中的数据也会随之变化，同时如果想要操作数据的时候，界面也要跟着重新更新渲染，那么也是操作代理对象


        // 通过当前的代理对象找到该对象中的某个属性，更改该属性中的某个数组的数据
        // user.wife.cars[1] = '玛莎拉蒂';

        // 通过当前的代理对象把目标对象中的某个数组属性添加一个新的属性
        // user.wife.cars[3] = '奥拓'

      }

      return {
        user,
        updateUser
      }
  }
});
</script>
```

# 5.响应式原理

```js
<script>
    const user = {
      name: '佐助',
      age: 20,
      wife:{
        name: '小樱',
        age: 19
      }
    }

    // 把目标对象变成代理对象
    // 参数 1： user  ---->  目标对象 target
    // 参数 2： handler   ---->   处理器对象，用来监视数据，及数据的操作
    const proxyUser = new Proxy(user, {
      // 获取目标对象的某个属性值
      get(target, prop){
        console.log('get方法调用了');
        return Reflect.get(target, prop);
      },
      // 修改目标对象的属性值、为目标对象添加新的属性
      // 可以深度更改，可以更改  对象中的对象属性中的某个属性值
      set(target, prop, val){
        console.log('set方法调用了');
        return Reflect.set(target, prop, val);
      },
      // 删除目标对象上的某个属性
      deleteProperty(target, prop){
        console.log('delete方法调用了');
        return Reflect.deleteProperty(target, prop);
      }
    })

    // 通过代理对象获取目标对象中的某个属性值
    console.log(proxyUser.name);
    // 通过代理对象更新目标对象上的某个属性值
    proxyUser.name = '鸣人';
    console.log(user);
    // 通过代理对象向目标对象中添加一个新的属性
    proxyUser.gender = '男'
    console.log(user);
    // 通过
    delete proxyUser.name;
    console.log(user);
    // 更新目标对象中的某个属性对象中的属性值
    proxyUser.wife.name = '雏田';
    console.log(user);

  </script>
```

# 6.setup细节问题

**App.vue**

```vue
<template>
  <h2>APP父级组件</h2>
  <h3>msg: {{msg}}</h3>
  <button @click = "msg += '==='">更新数据</button>
  <hr/>

  <Child :msg="msg" msg2="真香" @xxx="xxx" />
</template>

<script lang="ts">
import { defineComponent , ref } from "vue";
import Child from './components/Child.vue'

export default {
  name: "App",
  components: {
    Child
  },
  setup() {
    // 定义一个ref的基本数据
    const msg = ref('what are you nong sha lei?')

    // 这个txt后面要加一个  :string
    function xxx(txt: string){
      msg.value += txt
    }

    return {
      msg,xxx
    }
  }
};
</script>

```

**Child.vue**

```vue
<template>
  <h2>子级组件</h2>
  <h3>msg: {{msg}}</h3>
  <!-- <h3>count: {{count}}</h3> -->
  <button @click="emitXxx">点击</button>
</template>
<script lang="ts">
import { defineComponent } from "vue";
export default defineComponent({
  name: "Child",
  props: ['msg'],

  // setup的细节问题
  // setup是在beforeCreate生命周期回调之前执行了，而且就执行一次
  // 由此可以推断出：setup在执行的时候，当前的组件还没有创建出来，也就意味着，组件实例对象this根本就不能用
  // this是undefined，说明，就不能通过 this 去调用 data/computed/methods/props 中的相关内容了
  // 其实所有的 composition API 相关回调函数也不可以

  // setup中的返回值是一个对象，内部的属性和方法是给html模板使用的
  // setup中的对象内部的属性和data函数中的return对象的属性都可以在html模板中使用
  // setup中的对象内部的属性和data函数中的属性会合并为组件对象的属性
  // setup中的对象中的方法和methods对象中的方法会合并为组件对象的方法
  // 如果有重名，setup会优先
  // 注意： 在vue3中尽量不要混合的使用 data和setup 及 methods和setup
  // 一般不要混合使用： methods中可以访问setup提供的属性和方法，但在setup方法中不能访问data和methods
  // setup不是一个async函数，所以返回值不再是return对象，而是promise，模板看不到return对象中的属性数据


  // 数据初始化的生命周期回调
  // beforeCreate() {
  //   console.log('beforeCreate执行了');
  // },

  // 界面渲染完毕
  // mounted() {
  //   console.log(this);    
  // },

  // setup(props, context) {
  setup(props, {attrs, slots, emit}) {
    // props参数，是一个对象，里面有父级组件向子级组件传递的数据，并且是在子级组件中使用 props来声明了的的所有属性
    // 包含props配置声明且传入了的所有属性的对象
    console.log(props);

    // context参数，是一个对象，里面有attrs（获取当前组件标签上所有的属性的对象，但是该属性是在 props 中没有声明的 所有的属性对象），emit方法（分发事件的），slots对象（插槽）
    // 包含没有在props配置中声明的属性的对象，相当于this.$attrs

    // console.log(context.attrs);
    // console.log(context.emit);
    // console.log('setup执行了', this);
    const showMsg1 = () => {
      console.log('setup中的showMsg1方法');
    }

    function emitXxx(){
      // context.emit('xxx', "我是刘涛")
      emit('xxx', '甜甜甜甜圈')
    }

    return {
      // setup中一般都是返回一个对象，对象中的属性和方法都可以在html模板中直接使用
      showMsg1,
      emitXxx
    }
  },
  // data(){
  //   return {
  //     count: 10
  //   }
  // },
  // // 方法
  // methods:{
  //   showMsg2(){
  //     console.log('methods中的showMsg2方法');
  //   }
  // }
});
</script>

```

# 7.ref和reactive的细节问题

```vue
<template>
  <h2>reactive和ref的细节问题</h2>
  <h3>m1:{{m1}}</h3>
  <h3>m2:{{m2}}</h3>
  <h3>m3:{{m3}}</h3>
  <hr>
  <button @click="update">更新数据</button>
</template>
<script lang="ts">
import { defineComponent, ref, reactive} from 'vue'
export default defineComponent({
  name:'App',

  // 是vue3的 composition API 中2个最重要的响应式的API ( ref, reactive )
  // ref用来处理基本类型数据， reactive 用来处理对象（递归深度响应式）
  // 如果用 ref 对象/数组， 内部会自动将对象/数组转换为 reactive 的代理对象
  // reactive 内部： 通过使用Proxy来实现对对象内部所有数据的劫持，并通过Reflect操作对象内部数据
  // ref的数据操作： 在js中要 .value ， 在模板中不需要（内部解析模板时会自动添加 .value）

  setup(){
    // 通过ref的方式设置的数据
    const m1 = ref('abc');
    const m2 = reactive({
      name: '小明',
      wife: {
        name: '小红'
      }
    });
    // ref也可以传入对象吗
    const m3 = ref({
      name: '小明',
      wife:{
        name: '小红'
      }
    })

    const update = () => {
      // ref 中如果放入的是一个对象，那么是经过reactive处理，形成了一个proxy类型的对象
      m1.value += "===";
      m2.wife.name += "===";
      m3.value.name += "===";
      m3.value.wife.name += "===";
    }

    return {
      m1, m2, m3, update
    }
  }
})
</script>
```

# 8.watch和watchEffect

```vue
<template>
  <h2>计算属性和监视</h2>
  <fieldset>
    <legend>姓名操作</legend>
    姓氏： <input type="text" placeholder="请输入姓氏" v-model="user.firstName"/><br>
    名字： <input type="text" placeholder="请输入名字" v-model="user.lastName"/>
  </fieldset>
  <fieldset>
    <legend>计算属性和监视的演示</legend>
    姓名： <input type="text" placeholder="显示姓名" v-model="fullName1" /> <br>
    姓名： <input type="text" placeholder="显示姓名" v-model="fullName2" /> <br>
    姓名： <input type="text" placeholder="显示姓名" v-model="fullName3" /> <br>
  </fieldset>
</template>
<script lang="ts">
import { defineComponent, reactive, computed, watch, ref, watchEffect } from 'vue'
export default defineComponent({
  name:'App',
  setup(){
    // 定义 一个响应式的数据
    const user = reactive({
      // 姓氏
      firstName: '东方',
      // 名字
      lastName: '不败'
    })

    // 通过计算属性的方式，实现第一个姓名的显示
    // vue3中的计算属性
    // 计算属性的函数中如果只传入一个回调函数，表示的是get\
    
    // 第一个姓名： 
    // 返回的是一个ref类型的对象
    // 因为这里用到的是只有一个get方法，所以值需要传递一个箭头函数即可
    const fullName1 = computed(() => {
      return user.firstName + '_' + user.lastName
    })
    // console.log(fullName1);


    // 第二个姓名：
    // 因为这里要用到的是 get  和  set操作，  所以要传入的是一个对象，对象中有两个方法
    const fullName2 = computed({
      get(){
        return user.firstName + '_' + user.lastName
      },
      // 这里的 val 后面要加一个 :string
      set(val:string){
        const name = val.split('_');
        user.firstName = name[0];
        user.lastName = name[1];
      }
    })

    // 第三个姓名：
    const fullName3 = ref('')
    // 监视------监视指定的数据
    watch(user, ({firstName, lastName}) => {
      fullName3.value = firstName + '_' + lastName;
    }, {immediate: true, deep: true})
    // immediate 默认执行一次watch， deep深度监视

    // 监视, 不需要配置immediate，本身就会进行监视（默认执行一次）
    // watchEffect(()=>{
    //  fullName3.value = user.firstName + '_' + user.lastName;
    // })

    // 监视fullName3的数据，改变firstName和lastName
    watchEffect(()=>{
      const names = fullName3.value.split('_');
      user.firstName = names[0];
      user.lastName = names[1];
    })

    // watch --- 可以监视多个数据
    watch([() => user.firstName, () => user.lastName, fullName3], () => {
      // 这里的代码就没有执行, fullName3是响应式的数据，但是，user.firstName， user.lastName不是响应式的数据
       console.log('===');
    })
    // 当我们使用watch监听非响应式的数据的时候，代码需要改一下


    return {
      user, fullName1, fullName2, fullName3
    }
  },
  
})
</script>
```

# 9.生命周期

**App.vue**

```vue
<template>
  <h2>App父级组件</h2>
  <button @click="isShow = !isShow" >切换子级组件的显示</button>
  <hr/>
  <Child v-if="isShow"/>
  
</template>
<script lang="ts">
import { defineComponent, ref } from 'vue'
import Child from './components/Child.vue'
export default defineComponent({
  name:'App',
  components:{
    Child
  },
  setup(){
    const isShow = ref(true)

    return{
      isShow
    }
  }
})
</script>
```

**Child.vue**

```vue
<template>
  <h2>子级组件</h2>
  <h4>msg: {{msg}}</h4>
  <button @click = "update">更新msg</button>
</template>
<script lang="ts">
import { defineComponent, ref, onBeforeMount, onMounted, onBeforeUpdate, onUpdated, onBeforeUnmount, onUnmounted} from 'vue'
export default defineComponent({
  name:'Child',
  // vue2.x 中的生命周期钩子
  // 2.x 中又11个生命周期，其中还有 2个缓存组件的生命周期 和 1个错误的生命周期
  beforeCreate() {
    console.log('2.x中的beforeCreate...')
  },
  created() {
    console.log('2.x中的created...')
  },
  beforeMount() {
    console.log('2.x中的beforeMount...')
  },
  mounted() {
    console.log('2.x中的mounted...')
  },
  beforeUpdate() {
    console.log('2.x中的beforeUpdate...')
  },
  updated() {
    console.log('2.x中的updated...')
  },
  // 在vue2.x 中的beforeDestroy和destoryed这两个生命周期回调在vue3中改名了，所以，不再使用了
  beforeUnmount() {
    console.log('2.x中的beforeUnmount...')
  },
  unmounted() {
    console.log('2.x中的unmounted...')
  },

  // 3.0 中的生命周期都变成了组合API，并且3.0中的生命周期钩子要比2.0中的快，在2.0之前先执行
  setup() {
    console.log('3.0中的setup');
    const msg = ref('abc');
    const update = () => {
      msg.value += '==='
    }

    onBeforeMount(() => {
      console.log('3.0中的onBeforeMount');
    })
    onMounted(() => {
      console.log('3.0中的onMounted');
    })
    onBeforeUpdate(() => {
      console.log('3.0中的onBeforeUpdate');
    })
    onUpdated(() => {
      console.log('3.0中的onUpdated');
    })
    onBeforeUnmount(() => {
      console.log('3.0中的onBeforeUnmount');
    })
    onUnmounted(() => {
      console.log('3.0中的onUnmounted');
    })

    return {
      msg, update
    }
  }
})
</script>
```

**控制台**

```
刚开始的输出:
3.0中的setup
2.x中的beforeCreate...
2.x中的created...
3.0中的onBeforeMount
2.x中的beforeMount...
3.0中的onMounted
2.x中的mounted...

点击切换子组件的显示
3.0中的onBeforeUnmount
2.x中的beforeUnmount...
3.0中的onUnmounted
2.x中的unmounted...

点击更新msg
3.0中的onBeforeUpdate
2.x中的beforeUpdate...
3.0中的onUpdated
2.x中的updated...
```

# 10.自定义HOOK

**hook文件夹下**

```ts
useMousePosition.ts
import { ref, onBeforeUnmount, onMounted } from "vue";
export default function () {
  const x = ref(-1);
  const y = ref(-1);

  // 点击事件的回调函数
  const clickHandler = (event: MouseEvent) => {
    x.value = event.pageX;
    y.value = event.pageY;
  };

  // 页面已经加载完毕了，再进行点击的操作
  // 页面加载完毕的生命周期
  onMounted(() => {
    window.addEventListener("click", clickHandler);
  });

  // 页面卸载前的生命周期组合API
  onBeforeUnmount(() => {
    window.removeEventListener("click", clickHandler);
  });

  return {
    x,
    y,
  };
}
```

```ts
useRequest.ts
// 引入axios
import {ref} from 'vue'
import axios from 'axios'
// 发送ajax请求
export default function<T>(url:string){

  //加载状态
  const loading = ref(true)
  const data = ref<T | null>(null)    // 坑
  const errorMsg = ref('')

  // 发送请求
  axios.get(url).then(response => {
    // 改变加载状态
    loading.value = false
    data.value = response.data
  }).catch(error => {
    // 改变加载状态
    loading.value = false
    errorMsg.value = error.message || '未知错误'
  })

  return {
    loading, 
    data,
    errorMsg
  }
}
```

**public下的data文件夹**

```json
address.json
{
  "id": 1,
  "address": "北京市昌平区宏福苑",
  "distance": "1000m"
}

products.json
[
  {
    "id": "001",
    "title": "华为手机30",
    "price": 3219
  },
  {
    "id": "002",
    "title": "小米手机30",
    "price": 1982
  }
]
```

**App.vue**

```vue
<template>
  <h2>自定义hook函数操作</h2>
  <h2>x: {{x}},y: {{y}}</h2>
  <h3 v-if="loading">正在加载中.....</h3>
  <h3 v-else-if="errorMsg">{{errorMsg}}</h3>
  <ul v-else>
    <li>id: {{data.id}}</li>
    <li>address: {{data.address}}</li>
    <li>distance: {{data.distance}}</li>
  </ul>
  <hr>
  <!-- 数组数据 -->
   <ul v-for="item in data" :key="item.id">
     <li>id: {{item.id}}</li>
     <li>title: {{item.title}}</li>
     <li>price: {{item.price}}</li>
   </ul>
</template>
<script lang="ts">

import { defineComponent, watch } from 'vue'
import userMousePosition from './hooks/useMousePosition'
import useRequest from './hooks/useRequest'

// 定义接口，约束对象的类型
interface IAddressData{
  id: number,
  address: string,
  distance: string
}

interface IProductsData{
  id: string,
  title: string,
  price: number
}

export default defineComponent({
  name:'App',
  // 需求1： 用户在页面中点击页面，把点击的位置的横纵坐标手机并展示出来
  setup() {
    const {x, y} = userMousePosition()

    // 发送请求
    // const {loading, data, errorMsg} = useRequest<IAddressData>('/data/address.json') // 获取对象数据
    const {loading, data, errorMsg} = useRequest<IProductsData[]>('data/products.json') // 获取数组数据

    // 监视
    watch(data, () => {
      if(data.value){
        console.log(data.value.length);
      }
    })

    return {
      x, y, loading, data, errorMsg
    }    
  }
})
</script>
```

# 11.toRefs

```vue
<template>
  <h2>toRefs的使用</h2>
  <!-- <h3>name: {{state.name}}</h3> -->
  <!-- <h3>age: {{state.age}}</h3> -->

  <h3>name: {{name}}</h3>
  <h3>age: {{age}}</h3>

  <h3>name2: {{name2}}</h3>
  <h3>age2: {{age2}}</h3>

</template>
<script lang="ts">
import { defineComponent, reactive, toRefs } from "vue";

function useFeatureX(){
  const state = reactive({
    name2: '哦哦哦',
    age2: 14
  })

  return {
    ...toRefs(state)
  }
}

export default defineComponent({
  name: "App",
  setup() {
    const state = reactive({
      name: '自来也',
      age: 47
    })

    // 应用： 当从合成函数返回响应式对象时，toRefs 非常有用，这样消费组件就可以在不丢失响应式的情况下对返回的对象进行分解使用

    // toRefs可以把一个响应式对象转换为普通对象，该普通对象的每个 property 都是一个Ref
    const {name, age} = toRefs(state)
    // const state2 = toRefs(state)

    // 定时器，更新数据，（如果数据变化了，界面也会跟着变化，肯定是响应式的数据）
    setInterval(() => {
      // state.name += "==="
      // state.age += 1
      // state2.name.value += "==="  
      // state2.age.value += 1
      name.value += "==="
      age.value += 1
    }, 2000);

    const {name2, age2} = useFeatureX()

    return{
      // state
      // ...state // 不是响应式的数据  ---> {name: '自来也', age: 47}
      // ...state2   // toRefs返回来的对象
      name, age, name2, age2
    }
  }
});
</script>
```

# 12.ref的另一个用法

```vue
<template>
  <h2>Ref的另一个作用：可以获取页面中的元素</h2>
  <input type="text" ref="inputRef"/>
</template>
<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue'
export default defineComponent({
  name:'App',
  // 需求： 当页面加载完毕后，页面中的文本框可以直接获取焦点（自动获取焦点）
  setup() {
    // 默认是空的，页面加载完毕，页面汇总的文本框可以直接获取焦点（自动获取焦点）
    const inputRef = ref<HTMLElement | null>(null)
    // 页面加载后的生命周期组合API
    onMounted(() => {
      inputRef.value && inputRef.value.focus()  // 自动获取焦点
    })
    
    return{
      inputRef
    }
  }
})
</script>
```

# 13.shallowReactive和shallowRef

```vue
<template>
  <h2>shallowReactive和shallowRef</h2>
  <h3>m1: {{m1}}</h3>
  <h3>m2: {{m2}}</h3>
  <h3>m3: {{m3}}</h3>
  <h3>m4: {{m4}}</h3>
  <hr>
  <button @click="update">更新数据</button>
</template>
<script lang="ts">
import { defineComponent, ref, reactive, shallowReactive, shallowRef } from 'vue'
export default defineComponent({
  name:'App',
  setup() {

    // 深度劫持（深监视） ---- 深度响应式
    const m1 = reactive({
      name: '名人',
      age: 20,
      car: {
        name: '奔驰',
        color: 'red'
      }
    })
    // 浅度劫持（浅监视） ---- 浅响应式
    const m2 = shallowReactive({
      name: '名人',
      age: 20,
      car: {
        name: '奔驰',
        color: 'red'
      }
    })
    // 深度劫持（深监视） ---- 深度响应式   ----  做了reactive处理
    const m3 = ref({
      name: '名人',
      age: 20,
      car: {
        name: '奔驰',
        color: 'red'
      }
    })
    // 浅度劫持（浅监视） ---- 浅响应式   ---  只处理了value的响应式，不进行对象的reactive处理
    const m4 = shallowRef({
      name: '名人',
      age: 20,
      car: {
        name: '奔驰',
        color: 'red'
      }
    })

    const update = () => {
      // 更改m1的数据 --- reactive
      // m1.name += "===";
      // m1.car.name += "===";

      // 更改m2的数据  --- shallowReactive
      // m2.name += "===";
      // m2.car.name += "==="

      // 更改m3的数据  --- ref
      // m3.value.name += "==="
      // m3.value.car.name += "==="

      // 更改m4的数据 --- shallowRef --- 都不会变
      // m4.value.name += "==="
      // m4.value.car.name += "==="


    }
    console.log(m3, m4);
    
    return{
      m1, m2, m3, m4, update
    }
  }
})
</script>
```

# 14.readonly和shallowReadonly

```vue
<template>
  <h2>readonly和shallowReadonly</h2>
  <h3>state: {{state}}</h3>
  <hr/>
  <button @click="update">更新数据</button>
</template>
<script lang="ts">
import { defineComponent, reactive, readonly, shallowReadonly } from 'vue'
export default defineComponent({
  name:'App',
  setup() {
    const state = reactive({
      name: '名人',
      age: 19,
      car: {
        name: '奔驰',
        color: '银色'
      }
    })

    // 只读的数据 ---- 深度的只读  --- 谁都不能改
    // const state2 = readonly(state)
    // 只读的数据  ---- 浅度只读 
    const state2 = shallowReadonly(state)

    const update = () => {
      // 只能该 car.name 不能改 name
      // state2.name += "===";
      state2.car.name += "===";
    }

    return{
      state, update
    }
  }
})
</script>
```

# 15.toRaw和markRaw

```vue
<template>
  <h2>toRaw和markRaw</h2>
  <h3>state: {{state}}</h3>
  <hr/>
  <button @click="testToRaw">测试toRaw</button>
  <button @click="testMarkRaw">测试markRaw</button>
</template>
<script lang="ts">
import { defineComponent, reactive, toRaw, markRaw } from 'vue'
interface UserInfo{
  name: string;
  age: number;
  likes?: string[];
}
export default defineComponent({
  name:'App',
  setup() {
    // const state = reactive<any>({
    const state = reactive<UserInfo>({ 
      name: '小明',
      age: 20
    })

    const testToRaw = () => {
      // 把代理对象变成普通对象， 数据变化， 界面不变化
      const user = toRaw(state)
      user.name += "==="
      console.log('我好帅');
    }

    const testMarkRaw = () =>{ 
      // state.likes = ['吃', '喝'];
      // state.likes[0] += "==";
      // console.log(state);

      const likes = ['吃', '喝'];
      // markRaw标记的对象数据，从此以后都不能在成为代理对象了
      state.likes = markRaw(likes)
      setInterval(() => {
        if(state.likes){
          state.likes[0] += "=";
          console.log('定时器走起来');
        }
      }, 2000)

    }

    return {
      state, testToRaw, testMarkRaw
    }
  }
})
</script>
```

# 16.toRef的使用以及特点

**App.vue**

```vue
<template>
  <h2>toRef的使用及特点</h2>
  <h3>state: {{state}}</h3>
  <h3>age: {{age}}</h3>
  <h3>money: {{money}}</h3>
  <hr/>
  <button @click="update">更新数据</button>
  <hr/>
  <Child :age="age"/>
</template>
<script lang="ts">
import { defineComponent, reactive, toRef, ref } from 'vue'
import Child from './components/Child.vue'
export default defineComponent({
  name:'App',
  components:{
    Child
  },
  setup() {
    
    const state = reactive({
      age: 5,
      money: 100
    })

    // 把响应式数据state对象中的某个属性age变成了ref对象了
    const age = toRef(state, 'age')
    // 把响应式对象中的某个属性使用ref进行包装，变成了一个ref对象 === 为源响应式对象上的某个属性创建了一个ref对象,二者内部操作的是同一个数据值,更新时二者是同步的
    // ref相当于是拷贝了一份新的数据值单独操作,更新互不影响
    const money = ref(state.money)
    console.log(age);
    console.log(money);
    const update = () => {
      // 更新数据
      // 下面的age 都会修改 state中的数据
      state.age += 2  
      // age.value += 3
      money.value += 10  // 不会修改state中的money
    }

    return {
      state, age, money, update
    }
  }
})
</script>
```

**Child.vue**

```vue
<template>
  <h2>Child子级组件</h2>
  <h3>age: {{age}}</h3>
  <h3>length: {{length}}</h3>
</template>
<script lang="ts">
import { defineComponent, Ref, computed, ref, toRef } from 'vue'

function useGetLength(age: Ref){
  return computed(() => {
    return age.value.toString().length
  })
}

export default defineComponent({
  name:'Child',
  props: {
    age: {
      type: Number,
      required: true //必须的
    }
  },
  setup(props) {
    // const length = useGetLength(ref(props.age))
    const length = useGetLength(toRef(props, 'age'))

    return{
      length
    }
  }
})
</script>
```

# 17.customRef的使用

**相当于是做了个防抖**

```vue
<template>
  <h2>CustomRef的使用</h2>
  <input type="text" v-model="keyword" />
  <p>{{keyword}}</p>
</template>
<script lang="ts">
import { defineComponent, ref, customRef } from 'vue'

// 自定义hook防抖的函数
// value 传入的数据，将来数据的类型不确定，所以，用泛型，delay防抖的间隔时间，默认是200毫秒
function useDebouncedRef<T>(value: T, delay=200){
  // 准备一个存储定时器的 id 的变量
  let timeoutId: number

  return customRef((track, trigger) => {
    return{
      // 返回数据的
      get(){
        // 告诉vue追踪数据
        track()
        return value
      },
      // 设置数据的
      set(newValue: T){
        // 清理定时器
        clearTimeout(timeoutId)
        // 开启定时器
        setTimeout(() => { 
          value = newValue
          // 告诉vue更新界面
          trigger()
        }, delay)
      }
    }
  })
}

export default defineComponent({
  name:'App',
  setup() {
    // const keyword = ref('abc')
    const keyword = useDebouncedRef('abc', 500)

    return{
      keyword
    }
  }
})
</script>
```

# 18.provide和inject的跨层通讯

**App.vue**

```vue
<template>
  <h2>provide 与 inject</h2>
  <p>当前颜色： {{color}}</p>
  <button @click="color='red'">红色</button>
  <button @click="color='yellow'">黄色</button>
  <button @click="color='green'">绿色</button>
  <hr>
  <Son/>
</template>
<script lang="ts">
import { defineComponent, ref, provide } from 'vue'
import Son from './components/Son.vue'
export default defineComponent({
  name:'App',
  components:{
    Son
  },
  setup() {
    // 响应式的数据
    const color = ref('red')

    // 提供数据
    provide('color', color)

    return{
      color
    }
  }
})
</script>
```

**Son.vue**

```vue
<template>
  <h3>Son子级组件</h3>
  <hr>
  <GrandSon/>
</template>
<script lang="ts">
import { defineComponent } from 'vue'
import GrandSon from './GrandSon.vue'
export default defineComponent({
  name:'App',
  components:{
    GrandSon
  }
})
</script>
```

**GrandSon.vue**

```vue
<template>
  <h3 :style="{color}">GrandSon孙子组件</h3>
</template>
<script lang="ts">
import { defineComponent, inject } from 'vue'
export default defineComponent({
  name:'App',
  setup() {
  
    const color = inject('color')
    
    return{
      color
    }
  }
})
</script>
```

# 19.响应式数据判断的相关方法

```vue
<template>
  <h2>响应式数据的判断</h2>
</template>
<script lang="ts">
import { defineComponent, ref, isRef, reactive, isReactive, readonly, isReadonly, isProxy } from 'vue'
export default defineComponent({
  name:'App',
  /*
    isRef: 检查一个值是否为了个 ref 对象
    isReactive: 检查一个值是否由reactive 创建的响应式代理
    isReadonly: 检查一个对象是否有 readonly创建的只读代理
    isProxy: 检查一个对象是否有reactive 或者readonly方法创建的代理
  */

  setup() {
    // 下面全部输出为 true
    console.log(isRef( ref({}) ))
    console.log(isReactive( reactive({}) ))
    console.log(isReadonly( readonly({}) ))
    console.log(isProxy( reactive({}) ))
    console.log(isProxy( readonly({}) ))
      
    return{}
  }

})
</script>
```

# 20.手写组合API

**index.html**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <script src="./index.js"></script>
  <script>
    const proxyUser1 = shallowReactive({
      name: '小明',
      car: {
        color: 'red'
      }
    })
    // 拦截到了写的数据
    // proxyUser1.name = '小红'

    // 拦截到了读和写数据
    // proxyUser1.name += '=='

    // 拦截到了读数据，但是拦截不到写数据
    // proxyUser1.car.color + '=='

    // 拦截到了删除数据
    // delete proxyUser1.name

    // 拦截到了读数据，但是拦截不到删除    --->  说明这是一个浅度劫持
    // delete proxyUser1.car.color

    const proxyUser2 = reactive({
      name: '小明',
      car: {
        color: 'red'
      }
    })

    // 拦截到了写的数据
    // proxyUser2.name = '小红'

    // 拦截到了读和写数据
    // proxyUser2.name += '=='

    // 拦截到了读数据，但是拦截不到写数据
    // proxyUser2.car.color + '=='

    // 拦截到了删除数据
    // delete proxyUser2.name

    // 拦截到了读数据，删除数据   --->  说明这是一个深度劫持
    // delete proxyUser2.car.color


    // ===================================================
    // 测试shallowReadonly  和  readonly
    const proxyUser3 = shallowReadonly({
      name: '小明',
      car: ['奔驰', '宝马'],
    })

    // 不能修改
    // proxyUser3.name = '=='
    // 可以读取
    // console.log(proxyUser3.name);
    // 只能读取数据，不能删除数据
    // delete proxyUser3.name;
    // 拦截到了读 , 但是拦截不了深度的修改 [ 修改了数据 ]
    // proxyUser3.car[0] = '奥迪';
    // console.log(proxyUser3);
    // 拦截到了读，可以删除
    // delete proxyUser3.car[0];


    const proxyUser4 = readonly({
      name: '小明',
      car: ['奔驰', '宝马'],
    })

    // 只能读取数据，不能修改数据或者添加数据
    // proxyUser4.name = '=='
    // 拦截到了读取
    // console.log(proxyUser4.name);
    // 拦截到了读取
    // console.log(proxyUser4.car[0]);
    // 只能读取数据，不能修改数据或者添加数据
    // proxyUser4.name = '=='
    // 拦截到了读取 、 只能读取数据，不能修改数据或者添加数据
    // proxyUser4.car[0] = '奥迪'
    // 只能读取数据，不能删除数据
    // delete proxyUser4.name;
    // 拦截到了读 、 只能读取数据，不能删除数据
    // delete proxyUser4.car[0];
    


    const ref1 = shallowRef({
      name: '小明',
      car: {
        color: 'red'
      },
    })
    // console.log(ref1.value)

    // 劫持到修改数据
    // ref1.value = '=='

    // 劫持不到修改，shallowRef浅劫持
    // ref1.value.var = '=='


    const ref2 = ref({
      name: '小明',
      car: {
        color: 'red'
      },
    })
    // console.log(ref2.value)

    // 能劫持到修改数据
    // ref2.value = '=='

    // 能劫持到修改数据
    // ref2.value.car = '=='



    // ==================================================
    console.log(isRef(ref({})))
    console.log(isReadonly(readonly({})))
    console.log(isReactive(reactive({})))
    console.log(isProxy(reactive({})))
    console.log(isProxy(readonly({})))

  </script>
</body>
</html>
```

**index.js**

```js
// shallowReactive(浅度劫持，浅度监视，浅度相应数据) 与 reactive（深度）


// 定义一个reactiveHandler处理对象
const reactiveHandler = {
  // 获取属性值
  get(target, prop){
    if(prop === '_is_reactive') return true
    const result = Reflect.get(target, prop);
    console.log('拦截了读取数据', prop, result);
    return result
  },
  // 修改属性值或者是添加属性
  set(target, prop, value){
    const result = Reflect.set(target, prop, value);
    console.log('拦截了修改数据或是添加属性', prop, value);
    return result;
  },
  // 删除某个属性
  deleteProperty(target, prop){
    const result = Reflect.deleteProperty(target, prop);
    console.log('拦截了删除数据', prop);
    return result;
  }
}
// 定义一个shallowReactive函数, 传入一个目标对象
function shallowReactive (target){
  // 判断当前的目标对象是不是object类型(对象/数组)
  if(target && typeof target === 'object'){
    return new Proxy(target, reactiveHandler)
  }
  // 如果传入的数据是基本类型的数据，那么就直接返回
  return target
}

// 定义一个reactive函数，传入一个目标对象
function reactive(target){
  if(target && typeof target === 'object'){
    // 对数组或者是对象中所有的数据进行reactive的递归处理
    // 先判断当前的数据是不是数组
    if(Array.isArray(target)){
      // 数组的数据要进行遍历操作
      target.forEach((item, index) => {
        target[index] = reactive(item)
      })
    }else{
      // 在判断当前的数据是不是对象 (因为数据和对象操作方式不一样)
      // 对象的数据也要进行遍历的操作
      // 这样操作后，对象中的属性就变成了一个数组，可以进行遍历了   【 对象怎么遍历？ 】
      Object.keys(target).forEach(key => {
        target[key] = reactive(target[key])
      })
    }
    return new Proxy(target, reactiveHandler);
  }
  // 如果传入的是基本类型的数据，那么直接返回
  return target;
}



// ======================================
// 定义了一个readonlyHandler处理器
const  readonlyHandler = {
  get(target, prop){
    if(prop === '_is_readonly') return true
    const result = Reflect.get(target, prop);
    console.log('拦截到了读取数据', prop, result);
    return result
  },
  set(target, prop, value){
    console.warn('只能读取数据，不能修改数据或者添加数据');    
    return true
  },
  deleteProperty(target, prop){
    console.warn('只能读取数据，不能删除数据');
    return true
  }
}

// 定义一个shallowReadonly函数  -- 浅度
function shallowReadonly(target){
  // 需要判断当前的数据是不是对象
  if(target && typeof target === 'object'){
  
    return new Proxy(target, readonlyHandler);
  
  }

  return target
}

// 定义一个readonly函数
function readonly(target){
  if(target && typeof target === 'object'){
    if(Array.isArray(target)){
      // 判断是不是数组
      // 遍历数组，递归【 防止，数组套数组 】
      target.forEach((item, index) => {
        target[index] = readonly(item)
      })
    }else{
      // 判断是不是对象
      Object.keys(target).forEach(key => {
        target[key] = readonly(target[key])
      })
    }
    return new Proxy(target, readonlyHandler);
  
  }
  // 如果不是对象或数据，那么直接返回
  return target
}



// =====================================
// 定义一个shallowRef函数
function shallowRef(target){

  return {
    // 保存target数据
    _value : target,
    get value(){
      console.log('劫持到了读取数据')
      return this._value
    },
    set value(val){
      console.log('劫持到修改数据，准备更新界面', val)
      this._value = val
    }
  }
}


// 定义一个Ref函数
function ref(target){
  target = reactive(target)
  return {
    _is_ref : true,  // 标识当前对象是一个ref对象
    // 保存target数据
    _value : target,
    get value(){
      console.log('劫持到了读取数据')
      return this._value
    },
    set value(val){
      console.log('劫持到修改数据，准备更新界面', val)
      this._value = val
    }
  }
}



// =======================================
// 定义一个函数 isRef，判断当前的对象是不是ref对象
function isRef(obj){
  return obj && obj._is_ref
}
// 定义一个函数isReactive，判断当前的对象是不是reactive对象
function isReactive(obj){
  return obj && obj._is_reactive
}
// 定义一个函数isReadonly，判断当前的对象是不是readonly对象
function isReadonly(obj){
  return obj && obj._is_readonly
}
// 定义一个函数isProxy，判断当前对象是不是reactive对象或者readonly对象
function isProxy(obj){
  return isReactive(obj) || isReadonly(obj)
}


```

# 21.Fragment组件的介绍

```vue
<template>
  <!-- 根标签 -->
  <!-- 
    <div>
      <h2>测试1</h2>
      <h2>测试2</h2>
    </div> 
  -->
  <!--
    在vue3中，组件可以没有根标签，内部会将多个标签包含在一个Fragment虚拟元素中，好处：减少标签层级，减少内存占用
  -->
  <h2>测试1</h2>
  <h2>测试2</h2>

</template>
<script lang="ts">
import { defineComponent } from 'vue'
export default defineComponent({
  name:'App'
})
</script>
```

# 22.Teleport组件介绍(模态框)

**App.vue**

```vue
<template>
  <h2>App父级标签</h2>
  <hr/>
  <ModalButton/>
</template>
<script lang="ts">
import { defineComponent } from 'vue'
import ModalButton from './ModalButton.vue'
export default defineComponent({
  name:'App',
  components:{
    ModalButton
  }
})
</script>
```

**ModalButton.vue**

```vue
<template>

  <button @click="modalOpen=true">打开一个对话框</button>

  <teleport to="body">
      <!-- 对话框代码 -->
    <div v-if="modalOpen" class="modal">
      <div>
        这是对话框<button @click="modalOpen=false">关闭对话框</button>
      </div>
    </div>
  </teleport>

</template>
<script lang="ts">
import { defineComponent, ref } from 'vue'
export default defineComponent({
  name:'ModalButton',
  setup() {
    // 控制对话框，显示或者隐藏
    const modalOpen = ref(false)
    return{
      modalOpen
    }
  }
})
</script>

<style>
  .modal {
    position: absolute;
    top: 0; right: 0; bottom: 0; left: 0;
    background-color: rgba(0,0,0,.5);
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }
  
  .modal div {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    background-color: white;
    width: 300px;
    height: 300px;
    padding: 5px;
  }
  </style>
```

# 23.Suspense组件的使用

**相当于是,如果你发送了一个异步请求,在Suspense里面可以写一个template(用#default修饰),在加载数据的时候,可以现在default中的内容**

**App.vue**

```vue
<template>
  <h2>App父级组件： Suspense组件的使用</h2>

  <Suspense>
    <template #default>
      <!-- 异步组件 -->
      <AsyncComponent />
      <!-- <AsyncAddress /> -->
    </template>
    <template v-slot:fallback>
      <!-- 准备的内容 -->
      <h2>loading</h2>
    </template>
  </Suspense>
</template>
<script lang="ts">
import { defineComponent, defineAsyncComponent } from "vue";
// 引入组件：静态引入和动态引入

// Vue2 中的动态引入组件的写法：(在vue3中这种写法不行)
// const AsyncComponent = () => import('./AsyncComponent.vue')

// Vue3中动态引入组件的方法
// const AsyncComponent = defineAsyncComponent(
//   () => import("./AsyncComponent.vue")
// );

import AsyncComponent from './AsyncComponent.vue'
// import AsyncAddress from './AsyncAddress.vue'

export default defineComponent({
  name: "App",
  components: {
    AsyncComponent,
    // AsyncAddress
  },
});
</script>
```

**AsyncAddress.vue**

```vue
<template>
  <h2>AsyncAddress组件</h2>
  <h3>{{data}}</h3>
</template>
<script lang="ts">
import { defineComponent } from 'vue'
// 引入axios
import axios from 'axios'
export default defineComponent({
  name:'AsyncAddress',

  // 如果它等到的不是一个 Promise 对象，那 await 表达式的运算结果就是它等到的东西。

  // 如果它等到的是一个 Promise 对象，await 就忙起来了，它会阻塞后面的代码，等着 Promise 对象 resolve，然后得到 resolve 的值，作为 await 表达式的运算结果。

  async setup() {
    const result = await axios.get('/data/address.json')
    return{
      data: result.data
    }
  }

  // setup() {
  //   return axios.get('/data/address.json').then(response => {
  //     return{
  //       data: response.data
  //     }
  //   })
  // }

})
</script>
```

**AsyncComponent.vue**

```vue
<template>
  <h2>AsyncCompoennt子级组件</h2>
  <h3>{{msg}}</h3>
</template>
<script lang="ts">
import { defineComponent } from "vue";
export default defineComponent({
  name: "AsyncCompoennt",
  setup() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve({
          msg: 'what are you nong sha 嘞'
        })
      }, 2000)
    })
    // return {};
  },
});
</script>
```

