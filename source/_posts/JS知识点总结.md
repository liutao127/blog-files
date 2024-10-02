---

title: JS知识点总结
date: 2022-04-03 11:09:49
tags: JavaScript
---

# 1、学习新的ES知识

<!-- more -->

## ES6

### 1. Symbol语法使用

```js
var s1 = Symbol();
var s2 = Symbol();
console.log(s1 == s2);

// 调用Symbol的时候传入的参数最后作为这个symbol变量的description(描述)值
var s3 = Symbol('aaa');
console.log(s3.description);

var obj = {
  [s1]: 'abc',
  [s2]: 'cba',
}
obj[s3] = 'nba'
console.log(obj);

var s4 = Symbol();
Object.defineProperty(obj, s4, {
  enumerable: true,
  configurable: true,
  writable: true,
  value: 'mba'
})

console.log(obj);

// 不能通过.语法获取obj的Symbol值, 比如 obj.s1 是错的
console.log(obj.s1); // undefined
console.log(obj[s1]); // abc

console.log(Object.keys(obj)); // []
console.log(Object.getOwnPropertyNames(obj)); // []
// 通过 Object.getOwnPropertySymbols 来获取symbol的key
console.log(Object.getOwnPropertySymbols(obj)); // [ Symbol(), Symbol(), Symbol(aaa), Symbol() ]
var symKeys = Object.getOwnPropertySymbols(obj);
console.log(symKeys);
for(let key of symKeys) {
  console.log(obj[key]);
}


// 这种方法可以创建相同的 symbol 值
const sa = Symbol.for('bbb');
const sb = Symbol.for('bbb');
console.log(sa === sb); // true
```

### 2. Set的基本使用

```js
// 1. 创建Set结构
const set = new Set();
set.add(10);
set.add(20);
set.add(40);
set.add(50);
set.add(60);
set.add(70);
console.log(set); // Set(6) { 10, 20, 40, 50, 60, 70 }

// 2. 添加对象的时候要注意的事情
set.add(10);
console.log(set); // Set(6) { 10, 20, 40, 50, 60, 70 }

set.add({});
set.add({});
// 这里肯定有两个 {} 啊!
console.log(set); // Set(8) { 10, 20, 40, 50, 60, 70, {}, {} }

const obj = {};
const obj2 = obj;
set.add(obj);
set.add(obj);
console.log(set); // Set(9) { 10, 20, 40, 50, 60, 70, {}, {}, {} }
set.add(obj2);
console.log(set); // Set(9) { 10, 20, 40, 50, 60, 70, {}, {}, {} }

// 3. 数组去重
const arr = [33, 30, 10, 20, 30, 40, 50, 20, 10, 100];
// - 方法一: 重新创建一个空的新数组, 遍历原数组, 判断新数组是否有当前值, 如果有下一轮, 没有push
// - 方法二: 直接Set , set转换为数组也可以用  Array.from(set)
// console.log([...new Set(arr)]); // [33, 30,  10, 20, 40, 50, 100]
const newSet = new Set(arr);
// const newArr = Array.from(newSet);
const newArr = [...newSet];
console.log(newArr); // [33, 30, 10, 20, 40, 50, 100]


// 4. set的size属性
console.log(newSet.size); // 7

// 5. set的方法
// add
newSet.add(1212);
console.log(newSet); // Set(8) { 33, 30, 10, 20, 40, 50, 100, 1212 }

// delete: 删除某一个元素, 因为不支持索引, 所以是直接删除某个元素
newSet.delete(33);
console.log(newSet); // Set(7) { 30, 10, 20, 40, 50, 100, 1212 }
newSet.delete(222);
// 就算原本没有某个元素也不会报错
console.log(newSet); // Set(7) { 30, 10, 20, 40, 50, 100, 1212 }

// has: 判断是否包含某个值
console.log(newSet.has(33)); // false
console.log(newSet.has(10)); // true

// clear: 清楚set的所有属性
// newSet.clear();
console.log(newSet); // Set(0) {}


// 6. 遍历set
newSet.add({name: 'lt', age: 22}) // 下面两种遍历都会遍历出 { name: 'lt', age: 22 }
newSet.forEach(item => {
  console.log(item);
})

for(let i of newSet) {
  console.log(i);
}
```

### 3. WeakSet的使用

```js
/* 
  WeakSet和Set的区别?
    - WeakSet只能存放对象类型, 不能存放基本数据类型
    - WeakSet对元素的引用是弱引用, 如果没有其他引用对某个对象的引用, 那么GC可以对该对象进行回收
*/
// 区别1
const weakSet = new WeakSet();
// weakSet.add(10); // 直接报错 TypeError: Invalid value used in weak set

// 区别2
let obj = {name: 'lt'};

const set = new Set();
set.add(obj);
console.log(set);


weakSet.add(obj);
console.log(weakSet);

// 3. weakSet的引用场景
const personSet = new WeakSet();
class Person{
  constructor() {
    personSet.add(this);
  }
  running() {
    if(!personSet.has(this)) {
      throw new Error('不能通过不是Person的对象来调用该方法')
    }
    console.log('running', this);
  }
}

const p = new Person();
p.running();
p.running.call(null);
```

### 4. Map的基本使用

```js
const obj1 = {name: 'lt'};
const obj2 = {name: 'wtt'};

// 1. JS中是对象是不能作为对象的key来使用的
const info = {
  [obj1]: 'aaa',
  [obj2]: 'bbb'
}
// 这里是因为, 当用 [对象名] 作为key的时候, 是将该对象进行了 toString 转换为字符串,
// - obj1 和 obj2 的 toString 都是返回一个 [object Object] 的一个字符串的,
// 然后在给新对象添加属性了, 所以因为两个都是一样的字符串, 后面直接覆盖前面,
// 所以最后只有一个属性
console.log(info); // { '[object Object]': 'bbb' }


// 2. Map就是允许我们对象类型来作为key的
const map = new Map();
map.set(obj1, 'aaa');
map.set(obj2, 'bbb');
console.log(map); // Map(2) { { name: 'lt' } => 'aaa', { name: 'wtt' } => 'bbb' }
map.set(1, 'ccc');
console.log(map); // Map(3) {{ name: 'lt' } => 'aaa',{ name: 'wtt' } => 'bbb',1 => 'ccc'}


// 3. 初始化map的时候是可以传入数组的 --- 格式就是 entries
const map2 = new Map([[obj1, 'aaa'], [obj2, 'bbb'], [2, 'ccc']]);
console.log(map2);

// 4. 常见属性和方法  size, set(), get(), has(), delete(), clear()
console.log(map2.size); // 3

map2.set('lt', 'wtt');
console.log(map2); // Map(4) {{ name: 'lt' } => 'aaa',{ name: 'wtt' } => 'bbb',2 => 'ccc','lt' => 'wtt'}

console.log(map2.get(2)); // ccc 
console.log(map2.get('lt')); // wtt

console.log(map2.has(2)); // true
console.log(map2.has('lt')); // true
console.log(map2.has('wtt')); // false

console.log('delete2: ' + map2.delete(2)); // delete2: true
console.log(map2.has(2));// false

// map2.clear();
console.log(map2); // Map(0) {}



// 5. 遍历
map2.forEach(item => {
  console.log(item); // aaa bbb wtt
})

map2.forEach((item, index) => {
  /* 
    aaa { name: 'lt' }
    bbb { name: 'wtt' }
    wtt lt
  */
  console.log(item, index);
})

for(const item of map2) {
  /*
    这里是数组格式的, 所以可以通过过 item[0] item[1] 来访问值 
    [ { name: 'lt' }, 'aaa' ]
    [ { name: 'wtt' }, 'bbb' ]
    [ 'lt', 'wtt' ]
  */
  console.log(item);
}

for(const [key, value] of map2) {
  /*
    { name: 'lt' } aaa
    { name: 'wtt' } bbb
    lt wtt
  */
  console.log(key, value);
}
```

### 5. WeakMap的使用

```js
/* 
  Map和WeakMap的区别?
    - 区别一: WeakMap的key只能使用对象，不接受其他的类型作为key ;
    - 区别二: WeakMap的key对对象的引用是弱引用，如果没有其他引用引用这个对象，那么GC可以回收该对象;
*/

let obj1 = {name: 'lt'};
const obj2 = {name: 'wtt'};

// 区别二
// 强引用
const map = new Map();
map.set(obj1, 'aaa');
console.log(map);

// 弱引用
const weakMap = new WeakMap();
weakMap.set(obj1, 'aaa');
console.log(weakMap);

// 区别一
// weakMap.set(1, 'bbb'); // TypeError: Invalid value used as weak map key


// 3. 常见方法 set()  get()  delete()
console.log(weakMap.get(obj1)); // aaa
console.log(weakMap.has(obj1)); // true
console.log(weakMap.delete(obj1)); // true


// 4. 应用场景 (vue3的响应式原理)
const obj3 = {
  name: 'xbg',
  age: 16
}
function obj3Name1() {
  console.log('obj3Name1');
}
function obj3Name2() {
  console.log('obj3Name2');
}
function obj3Age1() {
  console.log('obj3Age1');
}
function obj3Age2() {
  console.log('obj3Age2');
}

const obj4 = {
  name: 'wtt',
  height: 1.66,
  address: '太原市'
}
function obj4Name1() {
  console.log('obj4Name1');
}
function obj4Name2() {
  console.log('obj4Name2');
}

// 创建weakMap
const weakMap2 = new WeakMap();

// 收集obj3数据结构
const obj3Map = new Map();
obj3Map.set('name', [obj3Name1, obj3Name2]);
obj3Map.set('age', [obj3Age1, obj3Age2]);
weakMap2.set(obj3, obj3Map);

// 收集obj4
const obj4Map = new Map();
obj4Map.set('name', [obj4Name1, obj4Name2]);
weakMap2.set(obj4, obj4Map);

// 如果obj3.name 发生了改变
obj3.name = 'james';
const targetMap = weakMap2.get(obj3);
const fns = targetMap.get('name');
fns.forEach(item => item() )
```

## ES7

### 1. Array.includes方法

```js
const names = ['abc', 'cba', 'nba', 'mba', NaN];

if(names.indexOf('cba') !== -1) {
  console.log('names中包含cba');
}


// ES7  ES2016
// names.includes('cba', index)
// index: 代表的是从第几个位置开始查询
// 如果这里 index = 2, 那么就查询不到
if(names.includes('cba', 2)) {
  console.log('names中包含cba');
} else {
  console.log('names中不包含cba');
}


if(names.indexOf(NaN) !== -1) {
  console.log('indexOf-names中包含NaN');
} else {
  console.log('indexOf-names中不包含NaN');
}

if(names.includes(NaN)) {
  console.log('includes-names中包含NaN');
} else {
  console.log('includes-names中不包含NaN');
}
```

### 2. 指数运算符

```js
// 算3的三次方
const result1 = Math.pow(3, 3);
console.log(result1);

// ES7   
// exponentiation运算符
const result2 = 3**3;
console.log(result2);
```

## ES8

### 1. keys和values

```js
const obj = {
  name: 'lt',
  wife: 'wtt'
}

// keys, values
console.log(Object.keys(obj)); // [ 'name', 'wife' ]
console.log(Object.values(obj)); // [ 'lt', 'wtt' ]

console.log(Object.keys(['abc', 'cba', 'mba'])); // [ '0', '1', '2' ]
console.log(Object.values(['abc', 'cba', 'mba'])); // [ 'abc', 'cba', 'mba' ]
console.log(Object.values('abc')); // [ 'a', 'b', 'c' ]

```

### 2. entries

```js
const obj = {
  name: 'lt',
  wife: 'wtt'
}

// entries
console.log(Object.entries(obj)); // [ [ 'name', 'lt' ], [ 'wife', 'wtt' ] ]
for(const [key, value] of Object.entries(obj)) {
  /* 
    name lt
    wife wtt
  */
  console.log(key, value);
}
```

### 3. padStart padEnd

```js
// padStart padEnd : 对字符串的首部和尾部进行填充的效果
const message = 'hello world';
const newMessage = message.padStart(15);
console.log(newMessage); //    hello world
const message2 = 'hello world';
const newMessage2 = message2.padStart(20, '*');
console.log(newMessage2); //*********hello world
const message3 = 'hello world';
const newMessage3 = message3.padStart(15, '-').padEnd(20, '*');
console.log(newMessage3); // ----hello world*****

// 案例 --- 银行卡号的隐藏
const cardNumber = '2313232311253412132123';
const lastFourCard = cardNumber.slice(-4);
const finalCard = lastFourCard.padStart(cardNumber.length, '*');
console.log(finalCard); //******************2123

```

### 4. Trailling-Commas使用

```js
// Trailling-Commas使用 --- 意思就是: 结尾的逗号
// 以前这样会报错, 但是ES8之后不会
function foo(m, n,) {
  
}

foo(3, 2,);
```

### 5. 其他知识点

```js
// async  await 的使用
// 作用: 异步转化为同步 
```



## ES9知识点总和

```js
// Async iterators: 
// Object sprea operators: 对象解构
// Promise finally: 
```

## ES10

### 1. flat和flatMap的使用

```js
// flat
const nums = [1, 2, 3, [4, 5, 6, [7, 8, 9, [10, 11, 12]]], [78, 89]];
const newNums = nums.flat(2);
console.log(newNums);


// flatMap
// flatMap 会先执行callback, 然后再进行flat降维
const nums2 = [10, 20, 30]
const newNums3 = nums2.flatMap(item => {
  return item*2;
})
const newNum4 = nums2.flatMap(item => {
  return item*2;
})
console.log(newNums3, newNum4); // [ 20, 40, 60 ] [ 20, 40, 60 ]


// flatMap的应用场景
const message = ['hello world', '你好啊, 王甜甜', 'my name is coderLT'];
const words = message.flatMap(item => {
  return item.split(' ');
})
/* 
  [
    'hello',   'world',
    '你好啊,', '王甜甜',
    'my',      'name',
    'is',      'coderLT'
  ]
*/
console.log(words);
```

### 2. Object.fromEntries方法

```js
const obj = {
  name: 'lt',
  wife: 'wtt',
  age: '22',
  dog: 'xbg',
  car: {
    name: 'benchi',
    color: 'black'
  }
}

const entries = Object.entries(obj);
/* 
  [
    [ 'name', 'lt' ],
    [ 'wife', 'wtt' ],
    [ 'age', '22' ],
    [ 'dog', 'xbg' ]
  ]
*/
console.log(entries);

const newObj = {};
for(let [key, value] of entries) {
  newObj[key] = value;
}
console.log(newObj); // { name: 'lt', wife: 'wtt', age: '22', dog: 'xbg' }
// fromEntries 是用来将 entries 数组重新转换回一个对象的方法
const newObj2 = Object.fromEntries(entries);
console.log(newObj2); // { name: 'lt', wife: 'wtt', age: '22', dog: 'xbg' }

console.log(obj == newObj2); // false

// 这里的queryString不能是www.baidu.com?name=lt&age=22&height=1.88这种形式
// 这样会把 www.baidu.com?name 当成第一个key
const queryString = 'name=lt&age=22&height=1.88';
const queryParams = new URLSearchParams(queryString);
// queryParams就是一个可迭代的对象
console.log(queryParams); // URLSearchParams { 'name' => 'lt', 'age' => '22', 'height' => '1.88' }
for(let item of queryParams) {
  /* 
    [ 'name', 'lt' ]
    [ 'age', '22' ]
    [ 'height', '1.88' ]
  */
  console.log(item);
}
```

### 3. trimStart和trimEnd使用

```js
const str = '    hello world   ';
console.log(str.trim());
console.log(str.trimStart());
console.log(str.trimEnd());
```

### 4. 其他知识点

```js
// Symbol description --- 创建Symbol值的时候传入的描述
// Optional catch binding : try  catch
```



## ES11

### 1. 大数字BigInt的使用

```js
// ES11之前
console.log(Number.MAX_SAFE_INTEGER); // 9007199254740991
console.log(Number.MIN_SAFE_INTEGER); // -9007199254740991
// 大1可以表示, 但是大2就不行了
console.log(Number.MAX_SAFE_INTEGER+1); // 9007199254740992
console.log(Number.MAX_SAFE_INTEGER+2); // 9007199254740992



const maxInt = Number.MAX_SAFE_INTEGER;
const bigInt = 900719925474099100n;
console.log(bigInt);
// console.log(bigInt + 22); // 报错-> TypeError: Cannot mix BigInt and other types, use explicit conversions
console.log(bigInt + 22n);

const num = 100;
console.log(bigInt + BigInt(num)); // 900719925474099200n 

const smallNum = Number(bigInt);
// 这可转换, 但是不建议, 因为是不安全的
console.log(smallNum); // 900719925474099100

```

### 2. Nullish-Coalescing-operator 控制运算符

```js
// ES11: 空值合并运算 ??    真正的空值运算

const foo = undefined;

// const bar = foo || 'defalut value';
const bar = foo ?? 'defalut value';

console.log(foo); // undefined
console.log(bar); // defalut value


// 用了?? 就只有当 foo 为undefined 或者null 的时候才会默认值赋给bar

```

### 3. OptionalChaining 操作可选链符 ?

```js
const info = {
  name: 'lt',
  girlFriend: {
    name: 'wtt',
    age: 22
  }
}

// console.log(info.car.color); // TypeError: Cannot read property 'color' of undefined

if(info.car && info.car.color) {
  console.log(info.car.color);
}


// ES11的可选链
console.log(info.car?.color); // undefined
console.log(info.girlFriend?.age); // 22
console.log(info.girlFriend?.age?.abc); // undefined

console.log('其他逻辑代码');

```

### 4. 获取全局对象的globalThis

```js
// 获取某一个环境下的全局对象

// 浏览器下
// console.log(window);
// console.log(this);

// node下
/* 
  <ref *1> Object [global] {
    global: [Circular *1],
    clearInterval: [Function: clearInterval],
    clearTimeout: [Function: clearTimeout],
    setInterval: [Function: setInterval],
    setTimeout: [Function: setTimeout] {
      [Symbol(nodejs.util.promisify.custom)]: [Getter]
    },
    queueMicrotask: [Function: queueMicrotask],
    performance: [Getter/Setter],
    clearImmediate: [Function: clearImmediate],
    setImmediate: [Function: setImmediate] {
      [Symbol(nodejs.util.promisify.custom)]: [Getter]
    }
  }
*/
// console.log(global);



// ES11 globalThis
// 会判断当前的环境, 并且指向当前环境的全局对象
console.log(globalThis);
```

### 5. for-in的标准化

```js
// for-in的标准化
const obj = {
  name: 'lt',
  age: 22
}

for(const item in obj) {
  console.log(item);
}

```

### 6. 其他知识点

```js
// Dynamic Import 动态导入  ---  ES Module模块化
import('').then()

// Promise.allSettled: Promise
// import meta
```

## ES12

### 1. FinalizationRegistry 监听对象是否被回收

```js
// FinalizationRegistry
// 用来监听对象是否被回收
const finalRegistry = new FinalizationRegistry((value) => {
  /* 
    注册在finalRegistry中的对象, 某一个被销毁了 我是info
    注册在finalRegistry中的对象, 某一个被销毁了 我是obj
  */
  console.log('注册在finalRegistry中的对象, 某一个被销毁了', value);
})

let obj = {name: 'lt'}
let info = {age: 22}

finalRegistry.register(obj, "我是obj");
finalRegistry.register(info, "我是info");

obj = null;
info = null;
```

### 2. WeakRef的使用

```js
// FinalizationRegistry
// 用来监听对象是否被回收
const finalRegistry = new FinalizationRegistry((value) => {
  console.log('注册在finalRegistry中的对象, 某一个被销毁了', value);
})

let obj = {name: 'lt'}
// 这种引用是强引用, 在让obj=null的时候,obj中原来的对象是不会被销毁的
// let info = obj;
// 使用WeakRef是弱引用, 这样当obj=null的时候,obj原来对象就会被销毁
let info = new WeakRef(obj);
// 如果原对象没有销毁, deref()会获取到原对象, 否则获取到的是undefined
console.log(info.deref()); // {name: 'lt'}

finalRegistry.register(obj, "我是obj");

obj = null;

setTimeout(() => {
  console.log(info.deref());
}, 10000);
```

### 3. 逻辑运算符

```js
// 1. ||= 逻辑或赋值运算
let message = undefined;
// 下面两行代码一样
// message = message || 'defalut value';
message ||= 'defalut value';
console.log(message); // defalut value

let message2 = 'hello world';
message2 ||= 'defalut value 2';
console.log(message2); // hello world


// 2. &&= 逻辑与赋值运算
let info = {
  name: 'lt'
}
// info = info && info.name;
// console.log(info); // lt
info &&= info.name;
console.log(info); // lt


// 3. ??= 逻辑空赋值运算
let message3 = '';
message3 ??= 'defalut value'
console.log(message3); // 空字符串
```

### 4. 数字分隔符 --- 方便阅读

```js
// 当数字非常大的时候, 为了方便阅读, 可以用_来对数字进行分割
const num1 = 1234567891011;
console.log(num1); // 1234567891011

const num2 = 123_456_789_101_1;
console.log(num2); // 1234567891011
```

### 5. 字符串的 replaceAll

```js
// 字符串的 replaceAll : 字符串替换
/* 
  const newStr = str.replaceAll(regexp|substr, newSubstr|function)
  regexp|substr: 必须。规定子字符串或要替换的模式的 RegExp 对象。
                 请注意，如果该值是一个字符串，则将它作为要检索的直接量文本模式，而不是首先被转换为 RegExp 对象。
                 当使用一个 regex 时，您必须设置全局（"g"）标志， 否则，它将引发 TypeError："必须使用全局 RegExp 调用 replaceAll"。
  newSubstr|function: 必需。一个字符串值。规定了替换文本或生成替换文本的函数。
*/
const str = new String("abd43ad2c43dd");   　　
console.log(str.replaceAll('d', 'x')); // abx43ax2c43xx
console.log(str.replaceAll(/\d/g, 'x')); // abdxxadxcxxdd
```

## ES13

### 1. at方法

```js
// 1. 数组
const arr = ['abc', 'cba', 'nba']; 
console.log(arr.at(1)); // cba
console.log(arr.at(-1)); // nba

// 2. 字符串
const str = 'hello lt wtt';
console.log(str.at(1)); // e
console.log(str.at(-1)); // t
```

### 2. Object.hasOwn方法

```js
// 对象属性的判断
const obj = {
  name: 'lt',
  age: 22,
  __proto__: {
    address: '临汾市'
  }
}
console.log(obj);

console.log(obj.hasOwnProperty('name')); // true
console.log(obj.hasOwnProperty('address')); // false

console.log(Object.hasOwn(obj, 'name')); // true
console.log(Object.hasOwn(obj, 'address')); // false

// Object.hasOwn(obj, propKey)
// 关系: hasOwn是用来替代前者的
// 区别一: 为了防止用户重写这个方法, 直接在实例对象身上添加 hasOwnProperty 方法
//        所以就在Object.prototype上添加这个方法, 用户一般不会重写
// 区别二: 代码如下
const info = Object.create(null);
info.name = 'wtt';
console.log(info.hasOwnProperty); // undefined
console.log(Object.hasOwn(info, 'name')); // true

```

### 3. class新增成员 --- 公共属性和"真"私有属性

```js
class Person {
  // 1. 实例属性
  // 对象属性: public 公共 -> public instance fields 公共实例字段
  height = 1.88;

  // 对象属性: private 私有 -> 程序员之间的约定
  _intro = 'name  is  lt';

  // ES13中: 真正的私有属性
  #intro = 'name is wttttt';

  // 2. 类属性(static)
  // public 公共的
  static totalCount = '1亿';

  // 雷属性  私有的
  static #myCount = '20亿';

  constructor(name, age) {
    // 对象中的属性: 在constructor通过this设置
    this.name = name;
    this.age = age;
    this.address = '临汾市'
  }

  // 3. 静态代码块
  static {
    console.log('hello world');
    console.log('Person static 静态代码块');
  }
}
const p = new Person('lt', 22);

console.log(p); // Person { height: 1.88, _intro: 'name  is  lt', name: 'lt', age: 22, address: '临汾市' }
console.log(p.name, p.age, p.address, p.height); // lt 22 临汾市 1.88
console.log(p._intro); // name is lt
// console.log(p.#intro); // 报错: SyntaxError: Private field '#intro' must be declared in an enclosing class

console.log(Person.totalCount); // 1亿
// console.log(Person.#myCount); // 报错: SyntaxError: Private field '#myCount' must be declared in an enclosing class
```

# 1、基本数据类型

- number：数字类型
- string：字符串类型
- boolean：布尔值类型
- undefined：未定义类型
- null：空类型
- object：对象类型
- symbol：symbol类型
- bigint：大数字类型

**最大安全数字和最小安全数字**

```js
console.log(Number.MAX_SAFE_INTEGER)
// 9007199254740991
console.log(Number.MIN_SAFE_INTEGER)
// -9007199254740991
```



 js 一共有7种**基本**数据类型，分别是 Undefined、Null、Boolean、Number、String。（**注意大写字母开头、大写字母开头、大写字母开头**）

ES6 中新增的 Symbol 类型，代表创建后独一无二且不可变的数据类型，主要是为了解决可能出现的全局变量冲突的问题。

ES2020新增BigInt，用于处理任意精度整数的新数字基元n，比如3n。



**ES5中有6种数据类型**：（另一个说法，注意没有基本2字）
null,undefined,number,string,boolean,object。

- **复杂类型**是指object即广义的对象类型，可由多个简单类型的值的合成，可以看作是一个存放各种值的容器。包括Array,Math，Date,RegExp,Function等 
- **基础类型**指number,string,boolean，null,undefined。

**基础类型和复杂类型的区别：**

- 基础类型将内容直接存储在**栈**中（大小固定位置连续的存储空间），记录的是该数据类型的值，即直接访问，基础类型赋值是复制（copy）； 
- 复杂类型将内容存储在堆中，堆所对应的栈中记录的是**指针**（堆的地址），外部访问时先引出地址，再通过地址去找到值所存放的位置。复杂类型赋值是地址引用。

```js
//经典考题1地址引用
let a = {name:'du',age:19};
let b =a;
console.log(b.name);//du
b.name = 'lee';
console.log(b.name);//lee
console.log(a.name);//lee a中的属性改变，说明a和b指向同一地址


//经典考题2
let a = {name:'du',age:19};
function change (obj){
 obj.age=29;  //obj作为对象参数，引用地址传入，修改age属性影响了原对象
 obj = {   //赋值操作，相当新建了一个对象让obj指向他
     name:'lee',
     age:39
 }
 return obj;  //返回新对象
}
let b=change(a);
console.log(a.age);//29，a中已改变
console.log(b.age);//39，b为新对象
```



**为什么 0.1+0.1===0.2，但是0.1+0.2 ！== 0.3？**

**答：**因为在JS中，浮点数是使用64位固定长度来表示的，其中的1位表示符号位，11位用来表示指数位，剩下的52位尾数位，由于只有52位表示尾数位。

```
所以只能取近似值，解决方法有：
- 先转大数，再变小数
- 使用`toFixed`
```

而`0.1`转为二进制是一个无限循环数`0.0001100110011001100......`(1100循环)

由于只能存储52位尾数位，所以会出现精度缺失，把它存到内存中再取出来转换成十进制就不是原来的`0.1`了，就变成了`0.100000000000000005551115123126`,而为什么02+0.1>0.3是因为

```
// 0.1 和 0.2 都转化成二进制后再进行运算
0.00011001100110011001100110011001100110011001100110011010 +
0.0011001100110011001100110011001100110011001100110011010 =
0.0100110011001100110011001100110011001100110011001100111

// 转成十进制正好是 0.30000000000000004
```

**那为什么0.2+0.3=0.5呢?**

```
// 0.2 和 0.3 都转化为二进制后再进行计算
0.001100110011001100110011001100110011001100110011001101 +
0.0100110011001100110011001100110011001100110011001101 = 
0.10000000000000000000000000000000000000000000000000001 //尾数为大于52位

// 而实际取值只取52位尾数位，就变成了
0.1000000000000000000000000000000000000000000000000000   //0.5
```

**答：**`0.2` 和`0.3`分别转换为二进制进行计算：在内存中，它们的尾数位都是等于52位的，而他们相加必定大于52位，而他们相加又恰巧前52位尾数都是`0`，截取后恰好是`0.1000000000000000000000000000000000000000000000000000`也就是0.5

**那既然0.1不是0.1了，为什么在console.log(0.1)的时候还是0.1呢?**

**答：在`console.log`的时候会二进制转换为十进制，十进制再会转为字符串**的形式，在转换的过程中发生了取近似值，所以打印出来的是一个近似值的字符串



**null 和 undefined 的区别**

- Undefined 和 Null 都是基本数据类型，这两个基本数据类型分别都只有一个值，就是 undefined 和 null。 
- undefined 代表的含义是未定义，null 代表的含义是空对象。一般变量声明了但还没有定义的时候会返回 undefined，null主要用于赋值给一些可能会返回对象的变量，作为初始化。 
- undefined 在 js 中不是一个保留字，这意味着我们可以使用 undefined 来作为一个变量名，这样的做法是非常危险的，它会影响我们对 undefined 值的判断。但是我们可以通过一些方法获得安全的 undefined 值，比如说 void 0。 
- 当我们对两种类型使用 typeof 进行判断的时候，null 类型化会返回 “object”，这是一个历史遗留的问题。后者为undefined 
- `undefined==null(true)` `undefined===null(false)`
- null转数字为0，undefined转数字为NaN



**为什么typeof null 是object？**

不同数据类型底层都是用二进制来表示的，二进制前三位为`000`则会被判断为object，而null二进制全是0，所以被判断成object

这个bug是初版本的JavaScript中留下的，扩展一下其他五种标识位：

- `000` 对象
- `1` 整型
- `010` 双精度类型
- `100`字符串
- `110`布尔类型



**NaN是什么？有什么特点？**

- typeof NaN 为 number
- NaN不等于自身，不大于自身，不小于自身
- NaN可以使用`Number.isNaN`判断
- NaN是假值，转布尔值为false



**`==`和`===`有什么区别？**

**"=="允许在相等比较过程中进行强制类型转换，而“===”不允许**。

1. 字符串和数字之间的比较：字符串执行 ToNumber() 转化为数字 
2. 其他类型和布尔值之间的比较：布尔值执行 ToNumber() 转化为数字 
3. null只==undefined，其他值和 null 或 undefined 比较均不相等 
4. 对象和基本类型的比较，对象执行 ToPrimitive() 转化为基本类型 

在 === 中， NaN !== NaN, -0 === +0

而在 Object.is 中，Object.is(NaN, NaN) -> true , Object.is(-0, +0) ->false，其余与 === 一致

实现一个 Object.is:

```js
function is (x, y) {
    if (x === y) {
        // 运行到1/x === 1/y的时候x和y都为0，但是1/+0 = +Infinity， 1/-0 = -Infinity, 是不一样的
        return x !== 0 || y !== 0 || 1 / x === 1 / y
    } else {
        // NaN===NaN是false,这是不对的，我们在这里做一个拦截，x !== x，那么一定是 NaN, y 同理
        // 两个都是NaN的时候返回true
        return x !== x && y !== y
    }
}
```



**答：**

`===`是严格意义上的相等，会比较两边的数据类型和值大小

- 数据类型不同返回false
- 数据类型相同，但值大小不同，返回false

`==`是非严格意义上的相等，

- 两边类型相同，比较大小

- 两边类型不同，根据下方表格，再进一步进行比较。

  - Null == Undefined ->true
  - String == Number ->先将String转为Number，在比较大小
  - Boolean == Number ->现将Boolean转为Number，在进行比较
  - Object == String，Number，Symbol -> Object 转化为原始类型

  

# 2、判断数据类型的常用方法

## 1、typeof

typeof 缺陷：可以判断null以外的基础类型，复杂类型除function以外均判断为object。 可以进行类型判断的返回值有： 'undefined','string','number','boolean','object','symbol','function'。

typeof 对 null 返回 'object'，对正则，数组返回 'object'

typeof（null）===‘’object’；

typeof (()=>{} )）===‘function’；

## 2、instanceof

用于检测某个对象的原型链(`__proto__`)上是否存在另一个对象的 `prototype`

```js
function instance(target, cons) {
    return cons.prototype.isPrototypeOf(target)
}
```

instanceof 可以准确判断复杂引用类型，但不能判断基础类型

Object instanceof Object   // true

## 3、Object.prototype.toString.call()

可以判断所有类型，返回示例 ''[object Array]"

在任何值上调用 Object 原生的 toString() 方法，都会返回一个 [object NativeConstructorName] 格式的字符串。每个类在内部都有一个 [[Class]] 属性，这个属性中就指定了上述字符串中的构造函数名。
但是它**不能检测非原生构造函数的构造函数名**。

```js
Object.prototype.toString.call(null);
//"[object Null]"
```

## 4、constructor

指向该对象实例的`__proto__.constructor`。

constructor不能判断undefined和null，并且使用它是不安全的，因为constructor的指向是可以改变的。

# 3、一些隐式转换规则（选择题爱考这个）

字符串转换 .toString()或者String()

null 和 undefined 类型 ，null 转换为 "null"，undefined 转换为 "undefined"，
Boolean 类型，true 转换为 "true"，false 转换为 "false"。
Number 类型的值直接转换，不过那些极小和极大的数字会使用指数形式。

**数字转换：**

（1）undefined 类型的值转换为 NaN。
（2）null 类型的值转换为 0。
（3）Boolean 类型的值，true 转换为 1，false 转换为 0。
（4）String 类型的值转换如同使用 Number() 函数进行转换，包含非数字值则转换为 NaN，空字符串为 0,"-Infinity"转化为-Infinity
 {}转换为NaN。[]转为0.

**布尔值转换：**

以下均为false，除外都为true 例如Boolean(' ')[注意这里有一个空格]为true，Boolean（[]）为true

• undefined
• null
• false
• +0、-0 和 NaN
• ""

**JavaScript的隐式转换规则？**

- `转string类型`：+（字符串连接符）
- `转number类型`：++/--(自增自减运算符) + - * / %(算术运算符) > < >= <= == != === !== (关系运算符)
- `转boolean`：!（逻辑非运算符)

**双等号左右两边的转换规则？**

- 1、null == undefined 为 true
- 2、如果有一个操作数是布尔值，则在比较相等性之前先将其转换为数值——false转换为0，而true转换为1；
- 3、如果一个操作数是字符串，另一个操作数是数值，在比较相等性之前先将字符串转换为数值
- 4、如果一个操作数是对象，另一个操作数不是，则调用对象的toString()方法，用得到的基本类型值按照前面的规则进行比较

**undefined >= undefined 为什么是 false ？**

隐式转换，变成`NaN >= NaN`，`NaN`不等于自身也不大于自身

**null >= null 为什么是 true？**

隐式转换，变成`0 >= 0`，为true

**[] == ![] 为什么是 true ？**

- 第一步：转为`[] == false`
- 第二步：转为`[] == 0`
- 第三步：转为`'' == 0`
- 第四步：转为`0 == 0`

# 3、===(恒等与恒不等) , ==(相等与不相等) , 大于, 大于等于, 小于, 小于等于

## 恒等运算符

恒等运算符'==='，也叫严格相等运算符，首先计算其操作数的值，然后比较这两个值，比较过程没有任何类型转换，比较过程如下：

　　【1】如果两个值的类型不相同，则返回false

```js
console.log(1 === '1');//false
console.log(1 === [1]);//false
```

　　【2】如果两个值都是Undefined、Null、Boolean、Number、String相同原始类型的值，值相同，就返回true，否则，返回false

```js
console.log(undefined === undefined);//true
console.log(null === null);//true
console.log(true === true);//true
console.log(false === false);//true
console.log(1 === 1);//true
console.log(2.5 === 2.5);//true
```

　　[注意]不论什么进制的数字，在进行关系比较时，最终都转换为十进制进行运算

```js
console.log(10 === 0xa);//true
```

　　在数字Number类型中，有一个值比较特殊，是NaN(not a number)，它与任何值都不相等；此外，数字Number类型中存在着+0和-0，虽然其符号不同，但值相等

```js
console.log(NaN === NaN);//false
console.log(+0 === -0);//true
```

　　两个相同字符串值表现为：相同的长度和相同的字符对应相同的位置

```js
console.log('abc' === 'abc');//true
console.log('abc' === 'acb');//false
```

　　【3】如果两个值引用同一个对象，则返回true，否则，返回false

　　[注意]更详细的解释是，javascript对象的比较是引用的比较，而不是值的比较。对象和其本身是相等的，但和其他任何对象都不相等。如果两个不同的对象具有相同数量的属性，相同的属性名和值，它们依然是不相等的

```js
console.log([] === []);//false
console.log({} === {});//false    
console.log(function(){} === function(){});//false
var a = {};
b = a;
console.log(a === b);//true
```

## 恒不等运算符

恒不等运算符(!==)又叫严格不等于运算符，操作数的比较过程与恒等运算符相同，结果取反。如果'==='的比较结果是true，则'!=='的比较结果是false；如果'==='的比较结果是false，则'!=='的比较结果是true

```js
console.log(1 !== '1');//true
console.log(1 !== 1);//false
console.log(true !== false);//true
console.log({} !== {});//true
```

## 相等运算符

相等运算符'=='和恒等运算符相似，但相等运算符的比较并不严格，如果两个操作数不是同一类型，相等运算符会尝试进行一些类型转换，然后再进行比较

　　当两个操作数类型相同时，比较规则和恒等运算符规则相同

```js
console.log(undefined == undefined);//true
console.log(10 == 0xa);//true
console.log(NaN == NaN);//false
console.log([] == []);//false
```

　　当两个操作数类型不同时，相等运算符'=='会遵守如下规则：

　　【1】如果一个值是对象类型，另一值是原始类型，则对象类型会先使用valueOf()转换成原始值，如果结果还不是原始值，则再使用toString()方法转换，再进行比较

　　[注意]日期类只允许使用toString()方法转换为字符串。类似地，时间Date对象进行加法运算时使用toString()转换为字符串，而在其他数学运算，包括减法、乘法、除法、求余等运算中，都是使用Number()转换函数将时间Date对象使用valueOf()转换为数字

　　【2】在对象转换为原始值之后，如果两个操作数都是字符串，则进行字符串的比较

```js
console.log(new Date() == 'Sat Jun 25 2016 11:07:20 GMT+0800 (中国标准时间)');//true
```

　　【3】在对象转换为原始值之后，如果至少有一个操作数不是字符串(意思就是说: 如果两个数都不是字符串, 那就必定会Number转换类型, 然后进行比较, 如果都是字符串的话, 那么就是比较两个字符串的大小)，则两个操作数都将通过Number()转型函数转换成数字进行数值比较

```js
console.log(true == 1);//true
console.log(true == 0);//false
console.log(false == '1');//false
console.log(false == '0');//true
console.log(true == 'true');//false，相当于1 == NaN

console.log([1] == 1);//true，相当于1 == 1
console.log([1] == '1');//true，相当于'1' == '1'
console.log([] == 0);//true，相当于0 == 0
console.log([] == '0');//false，相当于'' == '0'

console.log([] == true);//false，相当于0 == 1
console.log([1] == true);//true，相当于1 == 1
var a = {
    valueOf:function(){
        return 1;
    },
    toString:function(){
        return '2';
    }
} 
console.log( a == '1');//true，相当于1 == 1

var a = {
    valueOf:function(){
        return {};
    },
    toString:function(){
        return '1';
    }
} 
console.log( a == 1);//true，相当于1 == 1
```

　　[注意]如果一个值是null，另一个值是undefined，则返回true。虽然Number(null)是0，但null和0并不相等

```js
console.log(null == undefined);//true
console.log(null == 0);//false
```

**null == 0**

> null在设计上，对比等于的时候不尝试转型. null和0不转型的话，是不会相等的, 所以null == 0结果为false.

　　[注意]空字符串或空格字符串会转成0

```js
console.log(null == []);//false
console.log(null == '');//false
console.log([] == ' ');//false，相当于'' == ' '
console.log([] == '');//true，相当于'' == ''
console.log(0 == '');//true
```

##  不相等运算符

　　不相等运算符(!=)的操作数比较过程与相等运算符相同，结果取反。如果'=='的比较结果是true，则'!='的比较结果是false；如果'=='的比较结果是false，则'!='的比较结果是true

```js
console.log(1 != '1');//false，相当于1 != 1
console.log(true != '1');//false，相当于1 != 1
console.log('true' != 1);//true，相当于NaN != 1
console.log([1] != '1');//false，相当于'1' != '1'
console.log([1] != true);//false，相当于1 != 1
```

## 大于运算符

大于运算符(>)用于比较两个操作数，如果第一个操作数大于第二个操作数，则大于运算符的计算结果为true，否则为false

　　大于运算符的操作数可能是任意类型，然而，只有数字和字符串才能真正执行比较操作，因此那些不是数字和字符串的操作数都将进行类型转换，类型转换规则如下：

　　【1】如果操作数是对象，则这个对象将先使用valueOf()转换成原始值，如果结果还不是原始值，则再使用toString()方法转换

　　[注意]实际上，在原生对象中，使用valueOf()方法转换为原始值的，只有转换为数字Number类型的时间Date对象，其他对象都通过toString()方法转换为字符串

　　【2】在对象转换为原始值之后，如果两个操作数都是字符串，则按照字母表的顺序对两个字符串进行比较，这里提到的字母表顺序是指组成这个字符串的16位unicode字符的索引顺序

```js
console.log('b' > 'a');//true
console.log('B' > 'a');//false

console.log({} > '[a]');//true，相当于'[object Object]' > '[a]'
console.log({} > '[p]');//false，相当于'[object Object]' > '[p]'

console.log(['a'] > ['b']);//false，相当于'a' > 'b'
console.log([2] > [11]);//true，相当于'2' > '11'
```

　　[注意]在字母表中大写字母在小写字母的前面，所以大写字母 < 小写字母；但字符串String对象有一个字符串比较的方法[localeCompare()方法](http://www.cnblogs.com/xiaohuochai/p/5612962.html#anchor10)会考虑自然语言的排序情况，把'B'排在'a'的后面，如果字符串在字母表中排在其参数之前时，则该方法返回一个负数；字符串在字母表中排在其参数之后时，返回一个正数

```js
console.log('B'.localeCompare('a'));//1
console.log('B' > 'a');//false
console.log('b'.localeCompare('a'));//1
console.log('b' > 'a');//true
```

　　【3】在对象转换为原始值之后，如果至少有一个操作数不是字符串，则两个操作数都转换成数字进行比较

　　[注意]在等于操作符中，时间Date()对象只允许通过toString()方法转换为字符串，而不允许通过valueOf()方法转换为数字；而在大于操作符中，时间Date()对象允许优先使用valueOf()方法转换为数字 

```js
console.log(new Date() > 100);//true，相当于1466826928667 > 100
console.log(true > [0]);//true，相当于 1 > 0

console.log(2 > 1);//true
console.log(11 > '2');//true，相当于11 > 2

console.log(NaN > 1);//false
console.log(1 > NaN);//false
console.log({} > true);//false，相当于 NaN > 1
```

　　[注意]null == 0的结果为false，这是因为javascript将null == undefined的结果设为true。在大于运算中，null和undefined进行Number()转型函数转换分别转换为0和NaN

```js
console.log(undefined > -1);//false，相当于NaN > -1
console.log(null > -1);//true，相当于0 > -1
console.log(undefined > 0);//false，相当于NaN > 0
console.log(null > 0);//false，相当于0 > 0    
```

　　对于数字和字符串来说，加号运算符和比较运算符的行为有所不同，加号运算符更偏爱字符串，如果它的一个操作数是字符串，就进行字符串连接。而比较运算符则更偏爱数字，只有在两个操作数都是字符串时，才进行字符串的比较

```js
console.log(1 + 2);//3
console.log('1' + '2');//'12'
console.log('1' + 2);//'12'，相当于 '1' + '2'

console.log(2 > 1);//true
console.log('2' > '1');//true
console.log('2' > 1);//true，相当于 2 > 1
```

## 小于等于运算符

小于等于运算符(<=)并不依赖于小于或等于运算符的比较规则，而是遵循大于运算符的比较规则，结果取反。如果'>'的比较结果是true，则'<='的比较结果是false；如果'>'的比较结果是false，则'<='的比较结果是true

```js
console.log(1 <= '0');//false，相当于1 <= 0
console.log(true <= '0');//false，相当于1 <= 0
console.log('true' <= 0);//false，相当于NaN <= 0
console.log([1] <= '0');//false，相当于'1' <= '0'
console.log([0] <= true);//true，相当于0 <= 1
console.log(1 <= 1);//true
```

##  小于运算符

​		小于运算符(<)用于比较两个操作数，如果第一个操作数小于第二个操作数，则小于运算符的计算结果为true，否则为false

　　小于运算符与大于运算符的类型转换规则类似，就不再赘述

## 大于等于运算符

​	同样地，大于等于运算符(>=)并不依赖于大于或等于运算符的比较规则，而是遵循小于运算符的比较规则，结果取反。如果'<'的比较结果是true，则'>='的结果是false；如果'<'的比较结果是false，则'>='的结果是true

# 4、如何将字符串转化为数字，例如 '12.3b'

（1）使用 Number() 方法，前提是所包含的字符串不包含不合法字符。

（2）使用 parseInt() 方法，parseInt() 函数可解析一个字符串，并返回一个整数。还可以设置要解析的数字的基数。当基数的值为 0，或没有设置该参数时，parseInt() 会根据 string 来判断数字的基数。

（3）使用 parseFloat() 方法，该函数解析一个字符串参数并返回一个浮点数。

**经典题：[1,2,3].map(parseInt) ===[1,NaN,NaN]**

```js
['1', '2', '3'].map((item, index) => {undefined
    return parseInt(item, index) 
}）
                    
最终是这样执行的
输入的基数是0 ，按十进制或者八进制来转换，所以转换出来是1
parseInt('1', 0) ---->1

输入的基数是1 ，但是我们并没有1进制的数，所以转换出来是NaN
parseInt('2', 1) ----> NaN

输入的基数是2 ，按二进制来转换，3不是一个2进制的数，所以转换出来是NaN
parseInt('3', 2) ----> NaN

所以最终的结果是---->[1，NaN，NaN]


再来看一个练习题，['10', '10',  '10'，'10'，'10'].map(parseInt)最终输出结果是多少呢？
上面这行代码经历了以下历程：

parseInt('10', 0) ---->10 输入的基数是0 ，按十进制或者八进制来转换，所以转换出来是10

parseInt('10', 1) ----> NaN 输入的基数是1 ，我们没有1进制的数，所以返回NaN

parseInt('10', 2) ----> 2 输入的基数是2 ，按二进制，10按二进制转换得到2

parseInt('10', 3) ----> 3 输入的基数是3 ，按三进制，10按三进制转换得到3

parseInt('10', 4) ----> 4 输入的基数是4 ，按四进制，10按四进制转换得到4

所以最终输出结果是[10, NaN, 2, 3, 4]

我们可以使用Number来代替parseInt,例如：
['10', '10',  '10'，'10'，'10'].map(Number)  ----> ['10', '10',  '10'，'10'，'10']
```

# 5、遍历对象

使用 `for in` 循环遍历对象的属性时，[原型链](https://so.csdn.net/so/search?q=原型链&spm=1001.2101.3001.7020)上的所有属性都将被访问：

```js
Object.prototype.say="cgl";   // 修改Object.prototype  
    var person ={ age: 18 };
    for (var key in person) {
        console.log(key, person[key]);//这里用person.key得不到对象key的值，用person[key] 或者 eval("person."+key);
    }   //结果： age 18 
                say cgl
```

只遍历对象自身的属性，而不遍历继承于原型链上的属性，使用`hasOwnProperty` 方法过滤一下。

```js
Object.prototype.say="cgl";
    var person ={
        age: 18
    };
    for (var key in person) {
        if(person.hasOwnProperty(key)){
            console.log(key, eval("person."+key));
        }
    }  //结果：age 18
```

**Object.keys()** 方法会返回一个由给定对象的**自身可枚举属性**组成的数组，数组中属性名的排列顺序和使用 [`for...in`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...in) 循环遍历该对象时返回的顺序一致 （两者的主要区别是 一个 for-in 循环还会枚举其原型链上的属性）。返回值是这个**对象的所有可枚举属性组成的字符串数组**。

```js
Object.prototype.say="cgl";
var person ={ age: 18};
console.log(Object.keys(person));//结果  ["age"] 
```



```js
Object.keys() // 返回一个数组,包括对象自身的(不含继承的)所有可枚举属性(不含Symbol属性).数组中属性名的排列顺序和正常循环遍历该对象时返回的顺序一致 。
let obj = {name: 'lee',sex: 'male',age: 18}
Object.keys(obj).forEach(key => {
    console.log(key, obj[key]);
})


for...in // 循环遍历对象自身的和继承的可枚举属性(不含Symbol属性).
> 注意for...in遍历的是索引
let arr = [1,2,,3];
for(let ele in arr) {
    console.log(ele);  // 0 1 3
}


for...of
let arr = [1,2,,3];
for(let ele of arr) {
    console.log(ele); // 1,2,undefined,3
}


forEach()
let arr = [1,2,,3];
arr.forEach((v,k) => {
    console.log(v);
})//跳过空
    

Object.getOwnPropertyNames() // 返回一个数组,包含对象自身的所有属性(不含Symbol属性,但是包括不可枚举属性).


Reflect.ownKeys() // 返回一个数组,包含对象自身的所有属性(包括Symbol属性和不可枚举属性).
```

# 6、js的对象的常用的方法

```js
Object.assign() //浅复制对象
Object.entries() //返回自身可枚举的[key,value]
Object.keys() 
Object.values()
Object.hasOwnProperty(key) // 用来判断一个对象是否有你给出名称的属性。不过需要注意的是，此方法无法检查该对象的原型链中是否具有该属性，该属性必须是对象本身的一个成员。 true/false
Object.getOwnPropertyNames() // 返回一个数组，该数组对元素是 obj自身拥有的枚举或不可枚举属性名称字符串。 数组中枚举属性的顺序与通过 for...in 循环（或 Object.keys）迭代该对象属性时一致。数组中不可枚举属性的顺序未定义。
//for in 对对象进行遍历，可以拿到自身以及原型链上的可枚举的属性
Object.freeze()//冻结一个对象，不可修改，不可删除。不可添加新的属性
Object.prototype.toString() // 返回数组[object,object/array/function等]
//判断是数组还是对象就是用的这个方法
```

# 6. 考察Object方法的题

```js
var dest, src, result;
dest = {name: 'dest'};
src = {id: 'src'};
result = Object.assign(dest, src); 
console.log(dest, src, result); // {name: 'dest', id: 'src'} {id: 'src'} {name: 'dest', id: 'src'}
console.log(dest === result); // true
console.log(dest === src); // false
```



# 7、js的数组的常用的方法

```js
arr.push() // 将一个或多个元素添加到数组的末尾，并返回该数组的新长度。
arr.pop() // 从数组中删除最后一个元素，并返回该元素的值。此方法更改数组的长度。
arr.shift() // 从数组中删除第一个元素，并返回该元素的值。此方法更改数组的长度。
arr.unshift() // 将一个或多个元素添加到数组的开头，并返回该数组的新长度(该方法修改原有数组)。
arr.reverse() // 将数组中元素的位置颠倒，并返回该数组。数组的第一个元素会变成最后一个，数组的最后一个元素变成第一个。该方法会改变原数组。
arr.every() // 测试一个数组内的所有元素是否都能通过某个指定函数的测试。它返回一个布尔值。
arr.some() // 测试数组中是不是至少有1个元素通过了被提供的函数测试。它返回的是一个Boolean类型的值。
arr.forEach() // 对数组的每个元素执行一次给定的函数。
arr.filter() // 创建一个新数组, 其包含通过所提供函数实现的测试的所有元素。 
arr.map() // 创建一个新数组，这个新数组由原数组中的每个元素都调用一次提供的函数后的返回值组成。
arr.reduce() // 对数组中的每个元素按序执行一个由您提供的 reducer 函数，每一次运行 reducer 会将先前元素的计算结果作为参数传入，最后将其结果汇总为单个返回值。第一次执行回调函数时，不存在“上一次的计算结果”。如果需要回调函数从数组索引为 0 的元素开始执行，则需要传递初始值。否则，数组索引为 0 的元素将被作为初始值 initialValue，迭代器将从第二个元素开始执行（索引为 1 而不是 0）。
arr.includes() // 用来判断一个数组是否包含一个指定的值，根据情况，如果包含则返回 true，否则返回 false。
arr.indexOf() // 返回在数组中可以找到一个给定元素的第一个索引，如果不存在，则返回-1。
arr.lastIndexOf() // 返回指定元素（也即有效的 JavaScript 值或变量）在数组中的最后一个的索引，如果不存在则返回 -1。从数组的后面向前查找，从 fromIndex 处开始。
arr.findIndex() // 返回数组中满足提供的测试函数的第一个元素的索引。若没有找到对应元素则返回-1
arr.find() // 返回数组中满足提供的测试函数的第一个元素的值。否则返回 undefined。
arr.join() // 将一个数组（或一个类数组对象）的所有元素连接成一个字符串并返回这个字符串。如果数组只有一个项目，那么将返回该项目而不使用分隔符。
arr.splice(3,1,'o','i')//从索引3开始，删除1个，添加两个字符串。


arr.flat() // 按照一个可指定的深度递归遍历数组，并将所有元素与遍历到的子数组中的元素合并为一个新数组返回。
const arr1 = [0, 1, 2, [3, 4]];
console.log(arr1.flat());
// expected output: [0, 1, 2, 3, 4]
const arr2 = [0, 1, 2, [[[3, 4]]]];
console.log(arr2.flat(2));
// expected output: [0, 1, 2, [3, 4]]


arr.entries() // 返回一个新的Array Iterator对象，该对象包含数组中每个索引的键/值对。
const array1 = ['a', 'b', 'c'];
const iterator1 = array1.entries();
console.log(iterator1.next().value);
// expected output: Array [0, "a"]
console.log(iterator1.next().value);
// expected output: Array [1, "b"]
```

| 方法    | 作用                           | 是否影响原数组 |
| :------ | :----------------------------- | :------------- |
| push    | 在数组后添加元素，返回长度     | ✅              |
| pop     | 删除数组最后一项，返回被删项   | ✅              |
| shift   | 删除数组第一项，返回被删项     | ✅              |
| unshift | 数组开头添加元素，返回长度     | ✅              |
| reserve | 反转数组，返回数组             | ✅              |
| sort    | 排序数组，返回数组             | ✅              |
| splice  | 截取数组，返回被截取部分       | ✅              |
| join    | 将数组变字符串，返回字符串     | ❌              |
| concat  | 连接数组                       | ❌              |
| map     | 相同规则处理数组项，返回新数组 | ❌              |
| forEach | 遍历数组                       | ❌              |
| filter  | 过滤数组项，返回符合条件的数组 | ❌              |
| every   | 每一项符合规则才返回true       | ❌              |
| some    | 只要有一项符合规则就返回true   | ❌              |
| reduce  | 接受上一个return和数组下一项   | ❌              |
| flat    | 数组扁平化                     | ❌              |
| slice   | 截取数组，返回被截取区间       | ❌              |

# 8、类数组(伪数组)和数组的区别与转换

类数组对象，如 arguments 对象、NodeList 对象等，**类数组对象有length属性****，可以通过数组下标取值**，但是类数组对象**不能调用**数组原型上的方法。

**怎么把类数组对象转换成数组？**

```js
Array.prototype.slice.call(NodeList, 0)
 
Array.from(NodeList)
 
var new = [...NodeList]
```

# 9、JS中的深拷贝与浅拷贝

- 浅拷贝：只拷贝第一层，深层的依然是引用，改变深层会影响原对象
- 深拷贝：每一层都拷贝了，改变数据不会影响原对象

浅copy复制，创建一个新对象，接受原对象的值或者地址，改变新对象会引起原对象的变。

深拷贝完全是另一个对象

```js
Object.assign(newobj,obj)//ES6浅拷贝，遍历所有可枚举属性，然后赋值。把obj的可枚举属性拷贝给newobj，如果属性的值是复杂对象，则会指向同一地址，因此是浅拷贝

let newobj = {...obj} //浅拷贝 同上

Object.create(obj)//不能算是拷贝？该是继承，此方法使用指定的原型对象及其属性去创建一个新的对象

let newObj = JSON.parse(JSON.stringify(oldObj));//不完全的深拷贝
```

## **浅拷贝的实现方法**

```js
1. for in
    
function clone(obj) {
    var cloneObj = {}
    // for in 遍历，会遍历原型链里面的属性，所以需要排除原型链
    for(var key in obj) {
        if(obj.hasOwnProperty(key)) {
            cloneObj[key] = obj[key]
        }
    }
    return cloneObj
}

2. Object.keys()

function clone(obj) {
    var cloneObj = {}
    // Object.keys()不会遍历到原型链中的属性
    for(var key of Object.keys(obj)) {
        cloneObj[key] = obj[key]
    }
    return cloneObj
}

3. Object.entries()

function clone(obj) {
    var cloneObj = {}
    for(var [key, value] of Object.entries(obj)) {
        cloneObj[key] = value
    }
    return cloneObj
}

4. Object.assign()

function clone(obj) {
    return Object.assign(obj, {})
}
```

## **深拷贝的实现方法**

```js
1. JSON.stringfy() 与 JSON.parse()

// 存在问题：遇到函数，undefined，Symbol，Date对象时会自动忽略，遇到正则时会返回空对象。
function deepClone(obj) {
    return JSON.parse(JSON.stringify(obj))
}

2. 递归

// 三种遍历方式就会有三种递归
// for in
function deepClone(obj) {
    if(!obj || typeof obj != "object") return obj
    if(obj instanceof RegExp) return new RegExp(obj)
    var cloneObj = new obj.constructor
    for(var key in obj) {
        if(obj.hasOwnProperty(key)){
            cloneObj[key] = deepClone(obj[key])
        }
    }
    return cloneObj
}
 
// Object.keys()
function deepClone(obj) {
    if(!obj || typeof obj != "object") return obj
    if(obj instanceof RegExp) return new RegExp(obj)
    var cloneObj = new obj.constructor
    for(var key of Object.keys(obj)) {
        cloneObj[key] = deepClone(obj[key])
    }
    return cloneObj
}
// Object.entries()
function deepClone(obj) {
    if(!obj || typeof obj != "object") return obj
    if(obj instanceof RegExp) return new RegExp(obj)
    var cloneObj = new obj.constructor
    for(var [key, value] of Object.entries(obj)) {
        cloneObj[key] = deepClone(value)
    }
    return cloneObj
}
```

# 10、垃圾回收机制

```
垃圾回收机制就是间歇的不定期的寻找到不再使用的变量，并释放掉它们所指向的内存; 主要为了以防内存泄漏，(内存泄漏: 当已经不需要某块内存时这块内存还存在着).

JS有两种变量: 全局变量和在函数中产生的局部变量。局部变量的生命周期在函数执行过后就结束了，此时便可将它引用的内存释放(即垃圾回收); 但全局变量生命周期会持续到浏览器关闭页面。
```



基本类型：栈空间，引用类型：堆空间；池：常量

回收方法 ：标记清除法和引用计数法

## 为啥要垃圾回收

在Chrome中，V8被限制了内存的使用`（64位约1.4G/1464MB ， 32位约0.7G/732MB）`，为什么要限制呢？

- 表层原因：V8最初为浏览器而设计，不太可能遇到用大量内存的场景
- 深层原因：V8的垃圾回收机制的限制（如果清理大量的内存垃圾是很耗时间，这样会引起JavaScript线程暂停执行的时间，那么性能和应用直线下降）

前面说到栈内的内存，操作系统会自动进行内存分配和内存释放，而堆中的内存，由JS引擎（如Chrome的V8）手动进行释放，当我们的代码没有按照正确的写法时，会使得JS引擎的垃圾回收机制无法正确的对内存进行释放（内存泄露），从而使得浏览器占用的内存不断增加，进而导致JavaScript和应用、操作系统性能下降。

## 标记清除

标记清除顾名思义是一种分两阶段对对象进行垃圾回收的[算法]()。

第一阶段：标记。从**根结点出发遍历对象，对访问过的对象打上标记，表示该对象可达**。

第二阶段：清除。对那些没有标记的对象进行回收，这样使得不能利用的空间能够重新被利用。

这个[算法]()假定设置一个叫做根（root）的对象（在Javascript里，根是全局对象）。垃圾回收器将定期从根开始，**找所有从根开始引用的对象，然后找这些对象引用的对象**……从根开始，垃圾回收器将找到所有可以获得的对象和收集所有不能获得的对象。

**理解什么是根？例如：**

- 本地函数的局部变量和参数 
- 当前嵌套调用链上的其他函数的变量和参数 
- 全局变量 
- 还有一些其他的，内部的 

具体[算法]()：

- 垃圾回收器获取根并标记它们 
- 然后它访问并标记所有来自它们的引用

## 引用计数

另一种不太常见的垃圾回收策略叫做引用计数。引用计数的含义是跟踪每个值被引用的次数。

引用计数(reference counting):  这种方式常常会引起内存泄漏， 低版本的IE使用这种方式。机制就是跟踪一个值的引用次数， 当声明一个变量并将一个引用类型赋值给该变量时该值引用次数加1， 当这个变量指向其他一个时该值的引用次数便减一。 当该值引用次数为0时就会被回收。

# 11、原型和原型链(深度，以及Object.create())

{% asset_img  原型链.png %}

```JS
注意以下几点：
 - 每个实例对象[函数也是对象]的身上都有一个 __proto__ 属性 =》 __proto__ 指向的是该对象构造函数的 prototype 属性，prototype中有两个属性，分别为 constructor【指向该构造函数】 和 __proto__【指向上一层】
 - Object.prototype.__proto__ == null [原型链的尽头]
 - prototype属性，它是函数所独有的，它是从一个函数指向一个对象。
 - 所有函数的 __proto__ 都是一样的，都是Function.prototype
 - Function 比较特殊， Function.prototype.constructor == Function ；     Function.__proto == Function.prototype。 就是自己创建了自己


这个例子中： 
浏览器默认就会有Object的原型
Fn 是一个函数对象 --》 所以有一个 prototype 属性 ， 默认指向Object空对象【空对象的意思是：	没有我们定义的属性，但是test2是我们定义的】，但是其中有 constructor 和 __proto__ 属性
fn 是通过 Fn 而 new出来的对象  --》  所以 fn.__proto__ == Fn.prototype
因为Fn的prototype是Object对象，所以呢，Fn.prototype.__proto__ == Object.prototype对象【这是我这么理解的】

```



{% asset_img  原型链2.png %}

{% asset_img  原型链3.png %}

JavaScript 只有一种结构：对象。例子：

```js
function Class(){
	this.name='name';
}
class = new Class(); // 小写class为Class类的实例；Class为构造函数

每个实例对象（object）都有一个私有属性（称之为 __proto__）指向它的构造函数的原型对象（prototype）。
即class.proto === Class.prototype; //true

prototype中一般包含2个属性，一个是constructor，指向Class函数自身；一个是proto,指向更高一级的原型对象。可以在prototype中添加新的属性方法，实例上也能访问到。

原型对象也有一个自己的原型对象（proto），层层向上直到一个对象的原型对象为 null。根据定义，null 没有原型，并作为这个原型链中的最后一个环节。

当访问一个实例对象的属性时，如果这个对象内部不存在这个属性，那么它就会去它的原型对象里找这个属性，这个原型对象又会有自己的原型，于是就这样一直找下去，也就是原型链的概念。
```

**特点：**

JavaScript 对象是通过引用来传递的，我们创建的每个新对象实体中并没有一份属于自己的原型副本。当我们修改原型时，与之相关的对象也会继承这一改变。

```js
javascript中提供Object.getPrototypeOf()方法来获得对象的直接原型。

isPrototypeOf()：检测一个对象是否是另一个对象的原型

obj.constructor.prototype：检测非Object.create()创建的对象的原型

constructor、__proto__与prototype之间的关系

在javascript中我们每创建一个对象，该对象都会获得一个__proto__属性（该属性是个对象），该属性指向创建该对象的构造函数的原型即prototype，同时__proto__对象有一个constructor属性指向该构造函数。这里我们需要注意的是只有函数才有prototype，每个对象（函数也是对象）都有__proto__，Object本身是个构造函数。举例来说：
var obj = new Object()
// 也可以使用对象字面量创建，但使用Object.create()情况会不一样
// Object本身是个构造函数
Object instanceof Function  // true
obj.__proto__ === Object.prototype  // true
obj.__proto__.constructor === Object  // true
// 我们一般习惯这样写
obj.constructor === Object  // true

当我们访问obj.constructor的时候，obj本身是没有constructor属性的，但属性访问会沿着__proto__向上查找，即在obj.__proto__里面寻找constructor属性，如果找到了就返回值，如果未找到则继续向上查找直到obj.__proto__.__proto__...(__proto__) === null 为止，没有找到则返回undefined。这样由__proto__构成的一条查找属性的线称为‘原型链’。


进一步探讨
先有Object.prototype（原型链顶端），Function.prototype继承Object.prototype而产生，最后，Function和Object和其它构造函数继承Function.prototype而产生。


Object.create()
我们知道通过Object.create()创建的对象实际上等于将该对象的__proto__指向Object.create()里面的参数对象，那么当涉及到原型时它是怎么工作的呢?
var a = {
    name: 'sillywa'
}
var b = Object.create(a)

b.__proto__ === Object.prototype  // false
b.__proto__ === a  // true
b.__proto__.constructor === Object  // true
b.__proto__.hasOwnProperty('constructor')  // false

我们可以看到当var b = Object.create(a)实际上是把b的__proto__指向了a。当访问b.constructor时，实际上访问的是b.__proto__.__proto__.constructor。

function Person(name) {
    this.name = name
}
var person1 = new Person('sillywa')

person1.__proto__ === Person.prototype
person1.__proto__.__proto__ === Person.prototype.__proto__
person1.__proto__.__proto__ === Object.prototype
Person.prototype.__proto__ === Object.prototype 
person1.__proto__.__proto__.__proto__ === null

Person.__proto__ === Function.prototype
```

# 12、继承的方式

## 前置工作

在构造函数中: 为了属性(实例基本属性)的私有性, 以及方法(实例引用属性)的复用性共享性, 所以提倡把方法定义到原型对象上, 把私有方法放到构造函数中

```js
// 定义一个动物类
function Animal (name) {
  // 属性
  this.name = name || 'Animal';
  // 实例方法
  this.sleep = function(){
    console.log(this.name + '正在睡觉！');
  }
}
// 原型方法
Animal.prototype.eat = function(food) {
  console.log(this.name + '正在吃：' + food);
};
```

## 1、原型链继承

原型链继承，分清构造函数，原型

核心：将父类的实例作为子类的原型

优点：

- 1、非常纯粹的继承关系，实例是子类的实例，也是父类的实例
- 2、父类新增原型方法/属性，子类都能访问到
- 3、简单，易于实现

缺点：

- 1、要想为子类新增属性和方法，必须要在`new Animal()`这样的语句之后执行，不能放构造器中
- 2、来自原型对象的所有属性被所有实例共享
- 3、创建子实例时，无法向父类构造函数传参
- 4、不支持多继承

```js
function Cat(){ 
}
Cat.prototype = new Animal();
Cat.prototype.name = 'cat';

var cat = new Cat();
console.log(cat.name); // cat
cat.eat('fish') // cat正在吃：fish
cat.sleep() // cat正在睡觉！
console.log(cat instanceof Animal); //true 
console.log(cat instanceof Cat); //true
```



## 2. 借用构造函数继承（经典继承）

核心：使用父类的构造器来增强子类实例，等于是复制父类的实例属性给子类（没用到原型）

优点：

- 1、解决了`原型链继承`中，子类实例共享父类引用属性的问题
- 2、创建子类实例时，可以向父类传递参数
- 3、可以实现多继承(call多个父类对象) 

缺点：

- 1、实例并不是父类的实例，知识子类的实例
- 2、是能继承父类的实例属性和方法，不能继承原型属性/方法
- 3、无法实现函数复用，每个子类都有父类实例函数的副本，影响性能

```js
function Cat(name) {
  Animal.call(this);
  this.name = name || 'Tom';
}

var cat = new Cat();
console.log(cat.name); // Tom
cat.sleep() // Tom正在睡觉！
console.log(cat instanceof Animal); // false
console.log(cat instanceof Cat); // true
```



## 3、实例继承

核心：为父类实例添加新特性，作为子类实例返回

优点：

- 1、不限制调用方式，不管是`new 子类()`还是`子类()`，返回的对象具有相同效果 

缺点：

- 1、实例是父类的实例，不是子类的实例
- 2、不支持多继承

```js
function Cat(name){
  var instance = new Animal();
  instance.name = name || 'Tom';
  return instance;
}

var cat = new Cat();
console.log(cat.name) // Tom
cat.sleep() // Tom正在睡觉！
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); // false
```



## 4. 拷贝继承

核心：就一个一个拷贝

优点：

- 1、支持多继承 

缺点：

- 1、效率低，内存占用高（因为要拷贝父类的属性）
- 2、无法获取父类不可枚举方法（不可枚举方法，不能使用for in访问到）

```js
function Cat(name){
  var animal = new Animal();
  for(var p in animal){
    Cat.prototype[p] = animal[p];
  }
  this.name = name || 'Tom';
}

var cat = new Cat();
console.log(cat.name); // Tom
cat.sleep() // Tom正在睡觉！
console.log(cat instanceof Animal); // false
console.log(cat instanceof Cat); // true
```



## 5. 组合继承(原型链+构造函数继承的优点)

核心：通过父类构造，继承父类的属性并保留传参的优点，然后通过将父类实例作为子类原型，实现函数复用

优点：

- 1、弥补了`构造继承`的缺陷，可以继承实例属性/方法，也可继承原型属性/方法
- 2、既是子类的实例，也是父类的实例
- 3、不存在引用属性共享问题
- 4、可传参
- 5、函数可复用 

缺点：

- 1、调用了两次父类构造函数，生成了两份实例（第2行和第5行, 子类实例将子类原型上的那份屏蔽了）

```js
function Cat(name){
  Animal.call(this); // 因为要在子实例创建的时候向父构造函数中传递值,所以这里必须要
  this.name = name || 'Tom';
}
Cat.prototype = new Animal(); // 这个是被屏蔽的那份实例，因为构造函数中已经调用了一次实例，所以这里的原型身上的实例是没有用到的，但是又不能不要，因为如果不要，那么就访问不到Animal身上的属性了，也就不属于Animal的子类了
// Cat.prototype = Animal.prototype;  优化过后

Cat.prototype.constructor = Cat; // 这里的缺点: Cat.prototype.constructor直接修改的就是Animal.prototype.constructor, 所以Animal的子实例的constructor就会改变指向到Cat

var cat = new Cat();
console.log(cat.name); // Tom
cat.sleep() // Tom正在睡觉！
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); // true
```

## 6. 寄生组合继承(优化组合继承之后)

核心：通过寄生方式，砍掉父类的实例属性，这样，在调用两次父类的构造时，就不会初始化两次实例方法/属性，避免`继承组合`的缺点

优点：

- 1、堪称完美

缺点：

- 1、实现复杂

**第一种**

```js
function Cat(name) {
  Animal.call(this);
  this.name = name || 'Tom';
}

// 创建一个没有实例方法的类, 下面的三行代码实际上相当于用Object.create()穿件了一个对象
var Super = function () { };
Super.prototype = Animal.prototype;
//将实例作为子类的原型
Cat.prototype = new Super();

// 优化上面三行
// Cat.prototype = Object.create(Animal.prototype);

Cat.prototype.constructor = Cat;

// Test Code
var cat = new Cat();
console.log(cat.name); // Tom
cat.sleep() // Tom正在睡觉！
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); //true
```

**第二种**

```js
function Cat(name) {
  Animal.call(this);
  this.name = name || 'Tom';
}

// 关键代码
Cat.prototype = Object.create(Animal.prototype);

Cat.prototype.constructor = Cat;
// Test Code
var cat = new Cat();
console.log(cat.name); // Tom
cat.sleep() // Tom正在睡觉！
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); //true
```

## 总结

**实例继承**: 在子类构造函数中进行创建父类实例对象,并且将子类属性赋给父类实例对象,最后返回父类实例对象

```js
function Cat(name){
  var instance = new Animal();
  instance.name = name || 'Tom';
  return instance;
}
```



**拷贝继承**: 在子类构造函数中创建父类实例对象,并且遍历该对象,将该对象中的每个属性和属性值赋值到子类的prototype中

```js
function Cat(name){
  var animal = new Animal();
  for(var p in animal){
    Cat.prototype[p] = animal[p];
  }
  this.name = name || 'Tom';
}
```



**原型链继承**: 就是将子类的原型对象设置为父类的实例对象

```js
function Cat(){ 
}
Cat.prototype = new Animal();
Cat.prototype.name = 'cat';
```



**构造函数继承**: 在子类构造函数中进行调用父类的构造函数

```js
function Cat(name) {
  Animal.call(this);
  this.name = name || 'Tom';
}
```



**组合继承**(结合原型链继承和构造函数继承): 在子类构造函数中进行调用父类构造函数, 将子类的prototype指向父类实例对象(优化之后为指向父类原型), 最后修改子类prototype的constructor为子类构造函数, 但是这里修改之后 父类的prototype.constructor会指向子类构造函数

```js
function Cat(name){
  Animal.call(this); // 因为要在子实例创建的时候向父构造函数中传递值,所以这里必须要
  this.name = name || 'Tom';
}
Cat.prototype = new Animal(); // 这个是被屏蔽的那份实例，因为构造函数中已经调用了一次实例，所以这里的原型身上的实例是没有用到的，但是又不能不要，因为如果不要，那么就访问不到Animal身上的属性了，也就不属于Animal的子类了
// Cat.prototype = Animal.prototype;  优化过后

Cat.prototype.constructor = Cat; // 这里的缺点: Cat.prototype.constructor直接修改的就是Animal.prototype.constructor, 所以Animal的子实例的constructor就会改变指向到Cat
```



**寄生继承**(优化组合继承): 在子类构造函数中进行调用父类构造函数, 然后将子类的原型prototype指向Object.create(Animal.prototype)的对象, 然后再将Cat.prototype.constructor指向Cat(相当于添加了constructor属性在Cat的prototype上).

```js
function Cat(name) {
  Animal.call(this);
  this.name = name || 'Tom';
}

// 创建一个没有实例方法的类, 下面的三行代码实际上相当于用Object.create()穿件了一个对象
var Super = function () { };
Super.prototype = Animal.prototype;
//将实例作为子类的原型
Cat.prototype = new Super();

// 优化上面三行
// Cat.prototype = Object.create(Animal.prototype);

Cat.prototype.constructor = Cat;

// Test Code
var cat = new Cat();
console.log(cat.name); // Tom
cat.sleep() // Tom正在睡觉！
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); //true
```



# 13、创建一个对象的方式有哪几种？ &  new的原理

- new Object

```js
const obj = new Object()
obj.name = 'Sunshine_Lin'
```

- 字面量

```js
const obj = { name: 'Sunshin_Lin' }
```

- 工厂模式

```js
function createObj(name) {
  const obj = new Object()
  obj.name = name
  return obj
}
const obj = createObj('Sunshine_Lin')
```

- 构造函数

```js
function Person(name) {
  this.name = name
}
const person = new Person('Sunshine_Lin')
```



**new 大概会执行以下四个步骤：**

- 创建一个空对象obj
- 将**空对象的原型链**连接到**构造函数的prototype**
- 执行构造函数中的代码并**绑定** this 到obj对象
- 如函数没有返回值，则返回该对象

自己实现一个 _new(Constructor,...args):

```js
function _new (){
    let obj = {};

    // Con 为构造函数，因为arguments是类数组，不能直接 shift
    let Con = Array.prototype.shift.call(arguments);

    obj.__proto__ = Con.prototype;

    let res = Con.apply(obj, arguments);

    return res instanceof Object ? res : obj;
}

function Person (name, age){
    this.name = name;
    this.age = age;
}

Person.prototype.getName = function(){
    return this.name;
}

let p = _new(Person, 'tom', '23');
console.log(p);		// Person {name: 'tom', age: '23'}
```

```js
function Child1(){this.name='child1';}

var p = new Child1();
p	// Child1 {name: "child1"} 一个Child1的实例

var p = Child1()  // 不使用new
p  // undefined 因为Chilid1函数结果返回值为undefined
name  // "child1" --- child1方法中this指向window，所以window由了一个全局属性name
```

# 14、JS作用域和作用域链、执行期上下文

## **什么是作用域，什么是作用域链？**

**答：**

- 规定变量和函数的可使用范围称作作用域
- 每个函数都有一个作用域链，查找变量或者函数时，需要从局部作用域到全局作用域依次(有序)查找, 变量访问到window对象即被终止，这些作用域的集合称作作用域链。

## 作用域

ES6 有块级作用域

ES5 只有全局作用域和函数作用域

- 全局作用域：代码在程序的任何地方都能被访问，window 对象的内置属性都存在全局作用域
- 函数作用域：在固定的代码片段才能被访问
- 末定义直接赋值的变量自动声明为拥有全局作用域

```js
var num1 = 1;
function fun1 (){
    num2 = 2;
}
num2;//报错
fun1();
num2;//2 需先调用函数后才能完成num2的定义和赋值



var num1 = 1;
function fun1 (num1){
    console.log(num1);
    num1 = 2;
    console.log(num1);
}
fun1();//undefined,2  未传递参数
num1;//1,



var num1 = 1;
function fun1 (){
    console.log(num1);
    num1 = 2;
    console.log(num1);
}
fun1();//1，2  访问全局变量
num1;//2  num1被改变



var num1 = 1;
function fun1 (){
    console.log(num1); 
    var num1 = 2;
    console.log(num1);
}
fun1();//undefined,2  函数内作用域，尚未赋值
num1;//1 全局num1不改变



var num1 = 1;
function fun1 (){
    console.log(num1); 
    let num1 = 2;
    console.log(num1);
}
fun1();//报错，暂时性死区，let不提升
```

## 作用域链

当代码在一个环境中执行的时候，会**为该环境创建一个作用域链**，**保证对执行环境有权访问的所有变量的有序访问**。作用域链的最前端始终是当前执行代码所在环境，在变量查找的过程中，会沿着作用域链一层一层向上查找，直到找到变量或者找不到变量。



## **什么是执行栈，什么是执行上下文？**

**答：**

执行上下文分为：

- 全局执行上下文
  - 创建一个全局的window对象，并规定this指向window，执行js的时候就压入栈底，关闭浏览器的时候才弹出
- 函数执行上下文
  - 每次函数调用时，都会新创建一个函数执行上下文
  - 执行上下文分为创建阶段和执行阶段
    - 创建阶段：函数环境会创建变量对象：arguments对象（并赋值）、函数声明（并赋值）、变量声明（不赋值），函数表达式声明（不赋值）；会确定this指向；会确定作用域
    - 执行阶段：变量赋值、函数表达式赋值，使变量对象编程活跃对象
- eval执行上下文

执行栈：

- 首先栈特点：先进后出
- 当进入一个执行环境，就会创建出它的执行上下文，然后进行压栈，当程序执行完成时，它的执行上下文就会被销毁，进行弹栈。
- 栈底永远是全局环境的执行上下文，栈顶永远是正在执行函数的执行上下文
- 只有浏览器关闭的时候全局执行上下文才会弹出

# 15、ES6 新特性

let const 块级作用域

模板字符串 

解构赋值 

箭头函数，函数参数默认值 

扩展运算符（...） 

forEach    for...of     for...in 

数组方法map reduce includes 

map和set 

模块化 

promise proxy 

async 

class

# 16、let 和 var 的区别

let 是块级作用域，var 是函数作用域 

var 存在变量提升，而 let 没有 

let变量不能覆盖作用域中已定义的变量，const变量定义同时必须赋值 

在代码块内，使用 let 命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区” (TDZ)

# 17、bind,call,apply的区别

当我们使用一个函数需要改变`this`指向的时候才会用到`call``apply``bind` 

如果你要传递的参数不多，则可以使用`fn.call(thisObj, arg1, arg2 ...)` 

如果你要传递的参数很多，则可以用数组将参数整理好调用`fn.apply(thisObj, [arg1, arg2 ...])` 

如果你想生成一个新的函数长期绑定某个函数给某个对象使用，则可以使用`const newFn = fn.bind(thisObj); newFn(arg1, arg2...)` 

call，apply，bind 不传参数自动绑定在 window

# 18、箭头函数与普通函数的区别

1. 箭头函数是匿名函数，不能作为构造函数，不能使用 new

2. 箭头函数不能绑定arguments，取而代之用...解决

   ```js
   let C = (...c) => {
     console.log(c);
   }
   C(3,82,32,11323);
   // [3, 82, 32, 11323]
   ```

3. 箭头函数没有原型属性

4. 箭头函数的this永远指向其上下文的this，没有办改变其指向，普通函数的this指向调用它的对象

# 19、普通函数的 this 指向 VS ES6 中箭头函数

## 前提

- 任何函数本质上都是通过某个对象来调用的
- 所有函数内部都有一个变量this
-  this的值是调用函数的当前对象
- test() -> window  ;   p.test() ->  p  ;   new test()   -> 新创建的对象  ;   p.call(obj)  ->   obj
- 要注意普通函数和箭头函数this的区别，下面有详细介绍
- 普通函数的this是动态的，箭头函数的this是固态的

## 普通函数的this

1. 查看函数在哪被调用。 

2. 点左侧有没有对象？如果有，它就是 “this” 的引用。如果没有，继续第 3 步。 

3. 该函数是不是用 “call”、“apply” 或者 “bind” 调用的？如果是，它会显式地指明 “this” 的引用。如果不是，继续第 4 步。 

4. 该函数是不是用 “new” 调用的？如果是，“this” 指向的就是 JavaScript 解释器新创建的对象。如果不是，继续第 5 步。 

5. 是否在“严格模式”下？如果是，“this” 就是 undefined，如果不是，继续第 6 步。 

6. JavaScript 很奇怪，“this” 会指向 “window” 对象。

## ES6 箭头函数中 this

**最重要的是： 如果箭头函数的外层，被最近一层普通函数所包裹，那么箭头函数的this就是普通函数的this，如果没有被普通函数所包裹，那么默认就是指向window的**

默认指向定义它时，所处上下文的对象的 this 指向；
偶尔没有上下文对象，this 就指向 window

```js
// 例1
function hello() {
  console.log(this) // window
}
hello()

// 例2
function hello() {
  'use strict'
  console.log(this) // undefined
}
hello()

// 例3
const obj = {
  num: 10,
  hello: function () {
    console.log(this) // obj
    setTimeout(function () {
      console.log(this) // window
    })
  },
}
obj.hello()

// 例4
const obj = {
  num: 10,
  hello: function () {
    console.log(this) // obj
    setTimeout(() => {
      console.log(this) // obj
    })
  },
}
obj.hello()

// 例5
const obj = {
  radius: 10,
  diameter() {
    return this.radius * 2//diameter是普通函数，里面的this指向直接调用它的对象obj。
  },
  perimeter: () => 2 * Math.PI * this.radius,//这里上下文没有函数对象，就默认为window。
}
console.log(obj.diameter()) // 20
console.log(obj.perimeter()) // NaN

// 例6
let p = {
  a: function () {
    var obj = {
      i: 10,
      b: () => {console.log(this.i, this)},
      c: function () {
        console.log(this.i, this)
      }
    };
    obj.b(); // undefined  p
    obj.c(); // 10  obj
  }
}
p.a()
```



```js
var out = 25,
   inner = {
        out: 20,
        func: function () {
            var out = 30;
            return this.out;
        }
    };
console.log((inner.func, inner.func)());
console.log(inner.func());
console.log((inner.func)());
console.log((inner.func = inner.func)());
// 25   20   20  25


解释： 1, 4为匿名函数
1: 
var i = 0, j = 1, k = 2;
console.log((i++, j++, k)) // 返回的是 k 的值 2 ，如果写成 ++k 的话  这里返回的就是 3
console.log(i); // 1
console.log(j); // 2
console.log(k); // 2  
// 回到原题 ( inner.func, inner.func ) 就是返回 inner.func ，而 inner.func 只是一个匿名函数

// 第二和第三个 console.log 的作用域都是 inner，也就是他们执行的其实是 inner.func();   inner 作用域中是有 out 变量的，所以结果是 20。

/*
	第四个 console.log 考查的是一个等号运算 inner.func = inner.func ，其实返回的是运算的结果，
	
举个栗子:
var a = 2, b = 3;
console.log(a = b) // 输出的是 3

	所以 inner.func = inner.func 返回的也是一个匿名函数
*/
```





# 20、闭包、闭包的作用、闭包的特性、优缺点

闭包是一个函数，是一个能让**外部**访问到**函数内部**的**函数**

- 优点：使外部能访问内部，延长内部变量寿命
- 缺点：滥用闭包造成内存泄漏

**例子**

```js
function a () {
  let num = 0
  
  // 这是个闭包
  return function () {
     return ++num
  }
}
const b = a()
console.log(b()) // 1
console.log(b()) // 2


var num = 15;
var fn1 = function (x){
    console.log(num);	// 15
    if(x > num){
        console.log(x);	// 30
    }
}
void function(fn2){
    var num = 100;
    console.log(num); // 100
    fn2(30);
}(fn1);
```



## 闭包

一个函数有权访问另一个函数作用域中的变量，就形成闭包。

## 闭包作用

闭包可以用来隐藏变量，避免全局污染。也可以用于读取函数内部的变量。

## 闭包的三个特性

- 函数嵌套函数；
- 内部函数使用外部函数的参数和变量；
- 参数和变量不会被垃圾回收机制回收。

## 闭包的优点：

- 希望一个变量长期保存内存中；
- 避免全局变量污染；
- 私有成员的存在。

## 闭包的缺点：

- 常驻内存，增加内存使用量；
- 使用不当造成内存泄漏。

# 21、JS为啥是单线程的

## **JS单线程**

JavaScript 运行时，除了一个正在运行的主线程，引擎还提供一个任务队列（task queue），里面是各种需要当前程序处理的异步任务。（实际上，根据异步任务的类型，存在多个任务队列。为了方便理解，这里假设只存在一个队列。）

首先，主线程会去执行所有的同步任务。等到同步任务全部执行完，就会去看任务队列里面的异步任务。如果满足条件，那么异步任务就重新进入主线程开始执行，这时它就变成同步任务了。等到执行完，下一个异步任务再进入主线程开始执行。一旦任务队列清空，程序就结束执行。

异步任务的写法通常是回调函数。一旦异步任务重新进入主线程，就会执行对应的回调函数。如果一个异步任务没有回调函数，就不会进入任务队列，也就是说，不会重新进入主线程，因为没有用回调函数指定下一步的操作。

JavaScript 引擎怎么知道异步任务有没有结果，能不能进入主线程呢？答案就是引擎在不停地检查，一遍又一遍，只要同步任务执行完了，引擎就会去检查那些挂起来的异步任务，是不是可以进入主线程了。这种循环检查的机制，就叫做事件循环（Event Loop）。

# 21、 EventLoop事件循环 + Node事件循环 + 他们的区别



## **事件循环**

JS中把所有任务分成了两类，一类是同步任务，一类是异步任务。同步任务指的是在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务；异步任务指的是，不进入主线程，而是进入“任务队列”的任务，只有任务队列通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。

具体来说，异步执行的机制如下：

1. 所有同步任务都在主线程上执行，形成一个执行栈 
2. 主线程之外还有一个任务队列，只要异步任务有个结果，就在任务队列之中放置一个事件 
3. 一旦执行栈中的所有同步任务执行完毕，系统就会读取任务队列，看看里面有哪些事件。那些对应的异步任务于是结束等待状态，进入执行栈，开始执行 
4. 主线程不断重复上面的第三步。 

任务队列是一个事件队列，也可以理解成消息队列，I/O设备完成一项任务，就在任务队列中添加一个事件，表示相关的异步任务可以进入执行栈了。主线程读取任务队列，就是读取里面有哪些事件。

任务队列中的事件，还包括一些用户产生的事件（比如鼠标点击，页面滚动等等）。只要指定过回调函数，这些事件发生时就会进入“任务队列”，等待主线程读取。

{% asset_img  浏览器事件循环.png %}

```
微任务: promise.then catch finally(不是promise，promise里是立即执行)   MutationObserver  process.nextTick(Node.js 环境)
宏任务: script(整体代码) setTimeout setInterval I/O setImmediate(Node.js 环境) UI 交互事件  requestAnimationFrame
同一次事件循环中: 微任务永远在宏任务之前执行

首先script脚本整体是一个大的异步任务，先执行script脚本。这个script脚本会包含同步任务和异步任务，同步任务会先在主线程上执行，异步任务（分为宏任务和微任务）会添加到任务队列中，任务队列分为宏任务队列和微任务队列。

当同步任务执行完毕后，此时的执行栈已经被清空，会去执行异步任务。此时会先从微任务队列中取一个微任务放到执行栈中执行，若有新的微任务或宏任务产生，添加到相应的任务队列中，循环往复，直至微任务队列清空。

紧接着会从宏任务队列取一个宏任务放到执行栈中执行，此时可能会产生新的微任务，将微任务放到微任务队列中，当这个宏任务执行完后会继续执行微任务队列，如果没有产生就继续执行下一个宏任务。循环往复，直至所有任务执行完毕。
```

## Node事件循环

Node.js 中的事件循环和浏览器中的是完全不相同的东西。

Node.js 采用 V8 作为 JS 的解析引擎，而 I/O 处理方面使用了自己设计的 libuv，libuv 是一个基于事件驱动的跨平台抽象层，封装了不同操作系统一些底层特性，对外提供统一的 API，事件循环机制也是它里面的实现。

Node.js 的运行机制如下:

- **V8 引擎**解析 JavaScript 脚本。
- 解析后的代码，调用 **Node API**。
- **libuv 库**负责 Node API 的执行。它将不同的任务分配给不同的线程，形成一个事件循环，以异步的方式将任务的执行结果返回给 V8 引擎。
- **V8 引擎**再将结果返回给用户。

{% asset_img  Node事件循环.png %}

**事件循环的6个阶段**

```
timers 阶段：这个阶段执行 timer（ setTimeout、setInterval ）的回调
	timers 阶段会执行 setTimeout 和 setInterval 回调，并且是由 poll 阶段控制的。同样，在 Node.js 中定时器指定的时间也不是准确时间，只能是尽快执行。
	
I/O callbacks 阶段：处理一些上一轮循环中的少数未执行的 I/O 回调

idle、prepare 阶段：仅 Node.js 内部使用

poll 阶段：获取新的 I/O 事件, 适当的条件下 Node.js 将阻塞在这里
	poll 是一个至关重要的阶段，这一阶段中，系统会做两件事情：
	      ● 回到 timers 阶段执行回调
	      ● 执行 I/O 回调
	并且在进入该阶段时如果没有设定了 timer 的话，会发生以下两件事情：
	      ● 如果 poll 队列不为空，会遍历回调队列并同步执行，直到队列为空或者达到系统限制
	      ● 如果 poll 队列为空时，会有两件事发生：
	        ○ 如果有 setImmediate 回调需要执行，poll 阶段会停止并且进入到 check 阶段执行回调
	        ○ 如果没有 setImmediate 回调需要执行，会等待回调被加入到队列中并立即执行回调，这里同样会有个超时时间设置防止一直等待下去
	当然设定了 timer 的话且 poll 队列为空，则会判断是否有 timer 超时，如果有的话会回到 timer 阶段执行回调。
	假设 poll 被堵塞，那么即使 timer 已经到时间了也只能等着，这也是为什么上面说定时器指定的时间并不是准确的时间。
	
check 阶段：执行 setImmediate( ) 的回调
	setImmediate( ) 的回调会被加入 check 队列中，从事件循环的阶段图可以知道，check 阶段的执行顺序在 poll 阶段之后。
	
close callbacks 阶段：执行 socket 的 close 事件回调
```

{% asset_img  Node事件循环流程图.png %}

从上图中，大致看出 **Node.js 中的事件循环的顺序**：

外部输入数据 –-> 轮询阶段（ poll ）-–> 检查阶段（ check ）-–> 关闭事件回调阶段（ close callback ）–-> 定时器检测阶段（ timer ）–-> I/O 事件回调阶段（ I/O callbacks ）-–>闲置阶段（ idle、prepare ）–->轮询阶段（按照该顺序反复运行）…

## Node.js 与浏览器的事件队列的差异

**浏览器环境**下，就两个队列，一个宏任务队列，一个微任务队列。宏任务的任务队列是每个微任务执行完之后执行。
在 **Node.js** 中，每个任务队列的每个任务执行完毕之后，就会清空这个微任务队列。

{% asset_img  Node和浏览器的区别.png %}

#### 补充

**process.nextTick**
这个函数其实是独立于事件循环之外的，它有一个自己的队列。当每个阶段完成后，如果存在 nextTick 队列，就会清空队列中的所有回调函数，并且优先于其他 microtask 执行。

**Promise.then**
Promise.then 也是独立于事件循环之外的，有一个自己的队列，但是优先级要比 process.nextTick 要低，所以当微任务中同时存在 process.nextTick 和 Promise.then 时，会优先执行前者。

#### 附加内容

**JS在node端和浏览器端的区别：**

{% asset_img  补充Node和浏览器的区别.png %}

# 22、防抖和节流

## 防抖

函数在指定时间内只会触发一次，具体实现方法：

- 第一次触发函数的时候，延迟delay时间执行，如果在delay时间段内再次触发该函数，则重新开始计时

- 如果delay时间段内没有触发该函数，则执行该函数

  ```js
  function debounce(fn, delay) {
      let timer = null
      return function(){
          if(timer) {
              clearTimeout(timer)
          }
          timer = setTimeout(fn, delay)
      }
  }
  ```

## 节流

防抖的问题是，在短时间内不断触发事件，回调函数永远不会执行。

节流的思想：在短时间内不断触发事件，回调函数只会在指定间隔时间内执行。

```js
function throttle(fn, delay) {
    let timer = null;
    return function() {
        if(timer) return false
        timer = setTimeout(() => {
            fn()
            timer = null
        }, delay)
    }
}
```

# 23、DOM 常见操作方法

## 获取DOM元素的方法

| 方法                                   | 描述                      |
| :------------------------------------- | :------------------------ |
| document.getElementById(id)            | 通过id获取dom             |
| document.getElementsByTagName(tagName) | 通过标签名获取dom         |
| document.getElementsByClassName(class) | 通过class获取dom          |
| document.getElementsByName(name)       | 通过标签的属性name获取dom |
| document.querySelector(选择器)         | 通过选择器获取dom         |
| document.querySelectorAll(选择器)      | 通过选择器获取dom         |

## 操作DOM的方法

| 方法                   | 描述                                                         |
| :--------------------- | :----------------------------------------------------------- |
| createElement          | 创建一个标签节点                                             |
| createTextNode         | 创建一个文本节点                                             |
| cloneNode(deep)        | 复制一个节点，连同属性与值都复制，deep为true时，连同后代节点一起复制，不传或者传false，则只复制当前节点 |
| createDocumentFragment | 创建一个文档碎片节点                                         |
| appendChild            | 追加子元素                                                   |
| insertBefore           | 将元素插入前面                                               |
| removeChild            | 删除子元素                                                   |
| replaceChild           | 替换子元素                                                   |
| getAttribute           | 获取节点的属性                                               |
| createAttribute        | 创建属性                                                     |
| setAttribute           | 设置节点属性                                                 |
| romoveAttribute        | 删除节点属性                                                 |
| element.attributes     | 将属性生成类数组对象                                         |

# 24、Ajax请求的过程

```js
var xhr = new XMLHttpRequest()

// 请求的类型、请求的 url 以及是否异步发送请求
xhr.open("get", url, true)
// 传入请求的数据
xhr.send(null)

xhr.onreadystatechange = function() {
    if(xhr.readyState == 4) {
        if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
            console.log(xhr.responseText)
        } else {
            throw new Error("error")
        }
    }
}

```

## readyState的五种状态

```
 0 － （未初始化）还没有调用send()方法
 1 － （载入）已调用send()方法，正在发送请求
 2 － （载入完成）send()方法执行完成，已经接收到全部响应内容
 3 － （交互）正在解析响应内容
 4 － （完成）响应内容解析完成，可以在客户端调用了
```



## 自己进行原生Ajax封装

```js
function Ajax() {
    let handleAjax = function(resolve, reject) {
        if((this.status >= 200 && this.status <300) || this.status == 304) {
            console.log(this)
            resolve(this.response)
        } else {
            reject(this.response)
        }
    }
    this.get = function(URL, params={}) {
        return new Promise((resolve, reject) => {
            let xhr = new XMLHttpRequest()

            let paramsStr = Object.entries(params).map(item => item[0] + "=" + item[1]).join("&")

            URL = paramsStr ? (URL + "?" + paramsStr) : URL

            xhr.open("GET", URL, true)
            xhr.send(null)
            // xhr.onreadystatechange = function() {
            //   if(xhr.readyState === 4) {
            //     if((this.status >= 200 && this.status <300) || this.status == 304) {
            //       resolve(this.responseText)
            //     } else {
            //       reject(this.response)
            //     }
            //   }  
            // }
            xhr.onload = handleAjax.bind(xhr, resolve, reject)
        })
    }
    this.post = function(URL, data={}) {
        return new Promise((resolve, reject) => {
            let xhr = new XMLHttpRequest()

            let formData = new FormData()

            Object.entries(data).map(item => formData.append(item[0], item[1]))

            xhr.open("POST", URL, true)

            xhr.send(formData)

            xhr.load = handleAjax.bind(xhr, resolve, reject)
        })
    }
}
```

# 25、JS中String、Math方法

## String

- substring 
- substr 
- slice 
- indexOf 
- lastIndexOf 
- charAt: 以索引方式返回字符 
- charCodeAt 
- concat:连接字符串 
- trim 
- toLowerCase 
- toUpperCase 
- **match()**:接受一个正则，返回匹配到的结果数组 
- search():接受一个正则，返回第一个匹配的索引 
- **replace()**:第一个参数为字符串或者正则，第二个参数为字符串或者函数 
- **split()** 

ES6扩展

- padStart():第一参数指定字符串的最小长度，第二个参数是用来补全的字符串；省略第二个字符串会用空格来补全，两者最常用的 就是为数值补全指定位数或者格式化字符串 
- endStart() 
- includes() 
- startsWith() 
- endsWith() 
- repeat() 

## Math

- random() 返回 0-1的随机数 
- min，max 
- ceil:向上舍入，大于它的最小整数 
- floor:向下舍入，小于它的最大整数 
- round:四舍五入 
- log，abs，exp，pow，sqrt

# 26、addEventListener和onClick()的区别

普通的事件就是直接触发事件，同一时间只能指向唯一对象，所以会被覆盖掉：

```js
btn.onclick = function() {
    alert("111")
}
btn.onclick = function() {
    alert("222")
}
// "222"
```

事件绑定就是对于一个可以绑定的事件对象，进行多次绑定事件都能运行：

```js
btn.addEventListener("click", function() {
    alert("111")
})
btn.addEventListener("click", function() {
    alert("222")
})

// "111"  "222"
```

onclick 同一时间只能指向唯一对象 

addEventListener可以给一个事件注册多个listener 

addEventListener对任何DOM都都是有效的，而onclick仅限于HTML 

addEventListener可以控制事件的触发阶段

# 27、事件委托(事件代理)

1. 当有很多子元素需要绑定相同的事件的时候，会造成很大的内存消耗，可以用事件委托, 即把事件委托给父元素, 让父元素担当事件监听的角色.
2. 新增的子元素也自动绑定事件 

原理: 事件代理的原理是DOM元素的事件冒泡。 

好处: 可以提高性能,  可以大量节省内存占用，减少事件注册， 比如在ul上代理所有li的click事件;

坏处：事件委托基于冒泡，对于不冒泡的事件不支持（blur，focus，mouseleave，mouseenter，load，scroll，resize）（焦点事件，鼠标移动事件）

# 28、BOM的location对象

以下是 location 对象的所有属性：

| 属性名   | 例子                                       | 说明                                        |
| -------- | ------------------------------------------ | ------------------------------------------- |
| hash     | "#contents"                                | 返回URL中的hash，如果不包含，则返回空字符串 |
| host     | "www.sillywa.com:8080"                     | 返回服务器名称号和端口号（如果有）          |
| hostname | "www.sillywa.com"                          | 返回不带端口号的服务器名称                  |
| href     | "https://www.sillywa.com/index/index.html" | 返回当前加载页面的完整URL                   |
| pathname | "/index/index.html"                        | 返回URL中的目录或者文件名                   |
| port     | "8080"                                     | 返回URL中指定的端口                         |
| protocol | "https:"                                   | 返回页面使用的协议                          |
| search   | "?q=javascript"                            | 返回URL中的查询字符串                       |
| origin   | "https://www.sillywa.com"                  | 返回源                                      |

查询字符串参数，格式化 search

```js
function getQueryStringArgs() {
  // 取得 search 并去掉问号
  var qs = (location.search.length > 0) ? location.search.slice(1) : "";
  // 保存对象的数据
  var args = {};
  var items = qs.length ? qs.split("&") : [];
  var item, i, name, value;
  for(i = 0; i < items.length; i++){
    item = items[i].split("=");
    name = decodeURIComponent(item[0]);
    value = decodeURIComponent(item[1]);
    if(name.length) {
      args[name] = value;
    }
  }
  return args;
}
```

位置操作

使用 location.assign() 可以改变浏览器的位置，该方法接受一个 URL，如果是将 location.href 或 window.location 设置为一个 URL值，也会以该值调用 assign() 方法。

**每次修改 location 的属性（hash除外），页面都会以新的 URL 加载。**

当通过任何一种方式修改 URL 之后，浏览器的历史记录就会生成一条新纪录，因此用户通过单击”后退“按钮都会导航到前一个页面。要禁用这种行为，可以使用 replace() 方法。这个方法只接受一个参数，即要导航到的 URL。结果虽然能导致浏览器位置改变，但不会在浏览器中生成新纪录。在调用 replace() 方法之后，用户不能回到前一个页面。

location.reload() 可以重新加载页面，但有可能是从缓存加载。给它传递一个参数 true,就可以强制从服务器加载。

# 29、浏览器从输入URL到页面渲染的整个流程

```
https://juejin.cn/post/6844903679821086728
```



## 1. 首先对域名进行DNS解析：

- 查询浏览器是否缓存了该域名的IP地址 

- 查询hosts文件中是否存在该域名的IP地址 

- 查询本地DNS服务器（自己手动设置的DNS服务器）是否存在该域名的IP地址 

- 本地DNS服务器向根DNS服务器（13个）发送请求，进行查询 

  

  本地域名服务器 将得到的 IP 地址返回给操作系统，同时自己也将 IP 地址缓存起来。

  操作系统将 IP 地址返回给浏览器，同时自己也将 IP 地址缓存起来，以备下次别的用户查询时，可以直接返回结果，加快网络访问。

  至此，浏览器已经得到了域名对应的 IP 地址。

  

## 2. 查询到IP地址之后，浏览器开始与服务器建立TCP连接，三次握手：

{% asset_img  29-1.png %}

## 3. 浏览器发出HTTP请求

{% asset_img  29-2.png %}

```
GET /index.html HTTP/1.1
Host: www.sillywa.com
Connection: Close
Cookie: 123456
```

请求由应用层传入传输层，TCP对发出的数据进行处理，如果数据量较大，对数据进行分包，并给每个包标记序号，加上TCP头部（源端口号、目标端口号），总共大小不得超过1480字节；TCP将TCP包交给网络层进行处理，IP在TCP包头部加上IP头部（源IP地址、目标IP地址），IP头部通常20字节，数据包的大小不得超过1500字节；数据包交给数据链路层进行处理，数据链路层将数据包封装成帧，加上源MAC地址和下一个路由器的MAC地址，添加帧首部标记和帧结束标记，然后交给物理层进行传输。物理层将数据帧转为电信号发送出去

## 4. 服务端响应

HTTP 响应由三个部分组成，分别是：**状态行、消息报头、响应正文。**

状态代码：由三位数字组成，第一个数字定义了响应的类别，且有五种可能取值：

- 1xx：指示信息--表示请求已接收，继续处理
- 2xx：成功--表示请求已被成功接收、理解、接受
- 3xx：重定向--要完成请求必须进行更进一步的操作
- 4xx：客户端错误--请求有语法错误或请求无法实现
- 5xx：服务器端错误--服务器未能实现合法的请求

常见状态代码、状态描述、说明：

- 200 OK    ：客户端请求成功
- 400 Bad Request  ：客户端请求有语法错误，不能被服务器所理解
- 401 Unauthorized ：请求未经授权，这个状态代码必须和WWW-Authenticate报头域一起使用
- 403 Forbidden  ：服务器收到请求，但是拒绝提供服务
- 404 Not Found  ：请求资源不存在，eg：输入了错误的URL
- 500 Internal Server Error ：服务器发生不可预期的错误
- 503 Server Unavailable  ：服务器当前不能处理客户端的请求，一段时间后可能恢复正常

HTTP消息报头包括：普通报头、请求报头、响应报头、实体报头。

响应正文：就是服务器返回的资源的内容




## 5. 浏览器渲染

浏览器渲染是指浏览器请求下载资源，然后解析、构建树、渲染布局、绘制，最后呈现给客户界面的整个过程。用户看到页面实际上可分为两个阶段：页面内容加载完成和页面资源加载完成，分别对应 `DOMContentLoaded`和`load`。两者的区别如下

```
如果页面中同时存在css和js，并且存在js在css后面，则DOMContentLoaded事件会在css加载完后才执行。
其他情况下，DOMContentLoaded都不会等待css加载，并且DOMContentLoaded事件也不会等待图片、视频等其他资源加载。
```

- **DOMContentLoaded: DOM渲染完成执行，此时样式表、图片、视频还可能没有加载完成** 
- **load：页面全部资源加载完成才会执行，包括图片、视频** 

浏览器渲染主要包括以下五个步骤：

{% asset_img  29-3.jpg %}

将HTML解析成DOM树 

处理CSS标记，构建层叠样式表模型CSSOM 

将**DOM和CSSOM合并成渲染树**（Render Tree） 

计算**渲染树中每个DOM元素的坐标和大小**，它被称作为布局layout。浏览器使用一种**流式处理方法**，只需要一次绘制操作就可以布局所有的元素 

将**渲染树的各个节点绘制到图层**上，这一步叫做绘制 painting。本质上就是填充像素的过程。包括**绘制文字、颜色、图像、边框和阴影**等，也就是一个 DOM 元素所有的可视效果。一般来说，这个**绘制过程是在多个层**上完成的。 

合成：由于DOM元素的绘制是在多个层上进行的，因此在每个层上绘制完成之后，浏览器会将**所有层按照合理的顺序合并成一个图层**，然后显示在屏幕上，对于有位置重叠的元素，这个过程就比较重要了。（z-index）

{% asset_img  29-4.png %}

浏览器渲染的具体流程：

1. 构建DOM树

   遍历文档节点，生成DOM树，需要注意以下几点：

   - DOM树的构建过程中，可能会被CSS和JS的加载而阻塞执行 
   - display:none的元素也会在DOM树中 
   - 注释也会在DOM树中 
   - script标签也会在DOM树中 

2. 构建CSSOM

   每个CSS文件都被拆分成一个 StyleSheet 对象，每个对象都包含CSS规则。CSS规则对象包含对应于CSS语法的选择器和声明对象以及其他对象，在这个过程中需要注意的是：

   - CSS解析可以与DOM解析同时进行 
   - CSS解析与script的执行互斥 
   - 在Webkit内核中进行了script执行优化，只有在JS访问CSS时才会发生互斥 

3. 构建Render Tree

   浏览器从DOM树的根节点开始遍历每一个可见节点，然后对每个可见节点找到适配的CSS样式规则并应用。需要注意的是：

   - Render Tree 和 DOM Tree并不完成对应 
   - display:none的元素并不在Render Tree当中 
   - visibility:hidden的元素在RenderTree中 

4. 渲染树布局

   生成渲染树之后，还是没办法显示在屏幕上，渲染到屏幕上还需要获得个节点的位置信息。这就需要布局的处理了。

   布局会定位DOM元素的坐标和大小。

   布局会从DOM元素的根节点开始遍历，由于渲染树的每一个节点都是一个 Render Object，包含宽高、位置、背景色等信息。所以浏览器就通过这些样式信息来确定每个节点在页面上的确切大小和位置，布局阶段的输出就是盒子模型，它会精确捕获每个元素在屏幕内的确切位置和大小。需要注意的是：

   - float元素，absolute元素，fixed元素会发生位置偏移 
   - 我们常说的脱离文档流，其实就是脱离 Render Tree 

5. 渲染树绘制

   在绘制阶段，浏览器会遍历渲染树，调用渲染器的`paint()`方法在屏幕上显示其内容。渲染树的绘制工作是由浏览器的UI后端组件完成的。

#### 浏览器渲染过程中遇到JS文件怎么处理

- 渲染过程中，如果遇到 script 标签就会停止渲染，执行里面的JS代码。**JS的加载、解析与执行会阻塞DOM的构建**（将script放在底部的原因） 
- **样式放在head中仅仅是为了更快的解析CSS，保证更快的首次渲染**。 

#### 回流（重排）和重绘（reflow和repaint）

- 回流：对DOM元素的修改引发DOM元素的几何尺寸发生变化（比如修改元素的宽高，隐藏元素等）时，浏览器需要重新计算元素的几何属性（其他元素的几何位置也会因此受到影响），然后再将计算的结果绘制出来。这个过程就是回流（重排） 
- 重绘：当我们对DOM的修改导致了样式的变化（比如修改了颜色或者背景颜色）却并未影响其几何属性，浏览器不需要重新计算元素的几何属性，直接为该元素绘制新的样式（跳过了重排的环节）。 

当网页生成的时候，浏览器至少会渲染一次。在用户访问的过程中还会不断重新渲染。重新渲染会导致回流或者重绘。回流必定会引发重绘，但是重绘未必引发回流。不断的重绘和回流会影响页面的性能。回流的成本也远高于重绘

#### 常见引发回流和重绘的操作

会使任何元素的几何属性发生变化的操作（如元素的位置和尺寸），都会触发回流。

- 添加或删除可见的DOM元素 
- 元素尺寸的改变：边距、边框、宽高 
- 内容变化，比如用户在input中输入文字 
- 浏览器窗口尺寸的改变——resize事件 
- 计算 offsetWidth 和 offsetHeight 属性 

常见引起重绘的操作：color,background,border-style

#### 减少reflow与repaint

- 避免逐个修改节点样式，尽量一次修改
- 避免频繁使用 style，而是采用修改`class`的方式。
- 使用 createDocumentFragment将需要多次修改的DOM元素缓存之后，最后一次性append到真是的DOM中 
- 将动画效果应用到`position`属性为`absolute`或`fixed`的元素上。
- 可以先为元素设置`display: none`，操作结束后再把它显示出来。因为在`display`属性为`none`的元素上进行的DOM操作不会引发回流和重绘
- 利用 CSS3 的`transform`、`opacity`、`filter`这些属性可以实现合成的效果，也就是`GPU`加速。
- CSS选择符避免节点层级过多 
- 避免频繁读取会引发回流/重绘的属性，如果确实需要多次使用，就用一个变量缓存起来。

#### 提升为合成层

**某些特殊的渲染层会被认为是合成层**（Compositing Layers），合成层拥有单独的 GraphicsLayer，而其他不是合成层的渲染层，则和其第一个拥有 GraphicsLayer 父层公用一个。

每个 GraphicsLayer 都有一个 GraphicsContext，GraphicsContext 会负责**输出该层的位图，位图是存储在共享内存中，作为纹理上传到 GPU 中，最后由 GPU 将多个位图进行合成，然后 draw 到屏幕上**，此时，我们的页面也就展现到了屏幕上。

**渲染层提升为合成层有一个先决条件，该渲染层必须是 SelfPaintingLayer**（基本可认为是 NormalPaintLayer）

NormalPaintLayer:

- 根元素 
- 有明确定位的元素（relative、fixed、sticky、absolute） 
- 透明的（opacity 小于 1） 
- 有 CSS transform 属性（不为 none） 

合成层简单来说有以下好处：

- 合成层的位图会交由 GPU 进行处理，比 CPU 处理快 
- 当需要 repaint 时，只会 repaint 本身，不会影响其他的层级 
- 对于 transform 和 opacity 效果，不会触发 layout 和 paint 

**合成层的好处是不会影响到其他元素的绘制**，因此，为了减少动画元素对其他元素的影响，从而减少 paint，我们需要把动画效果中的元素提升为合成层。

**提升合成层的最好方式是使用 CSS 的 \*will-change\* 属性**。从上一节合成层产生原因中，可以知道 **will-change 设置为 opacity、transform、top、left、bottom、right 可以将元素提升为合成层**。

对于那些目前还不支持 will-change 属性的浏览器，目前常用的是使用一个 3D transform 属性来强制提升为合成层：

```
#target {
  transform: translateZ(0);
}
```

# 30、跨域、同源策略及跨域实现方式和原理

跨域，是指浏览器不能执行其他网站的脚本。它是由浏览器的同源策略造成的，是浏览器对JavaScript实施的安全限制。浏览器从一个域名的网页去请求另一个域名的资源时，出现域名、端口、协议任一不同，都属于跨域。

两个不同源的URL在相互访问的时候就会产生跨域，同源的URL必须保证域名相同、端口相同、协议相同。跨域的解决方法：

## 1. JSONP

```js
// 封装一个 jsonp
function jsonp(url, params={}) {
    return new Promise((resolve, reject) => {
        window.__fn__ = data => resolve(data)

        // 获取参数
        let paramStr = Object.entries(params).map(item => `${item[0]}=${item[1]}`).join("&")

        // 动态添加 script 标签
        let script = document.createElement("script")

        script.scr = paramStr ? `${url}?callback=__fn__&${paramStr}` : `${url}?callback=__fn__`

        script.onerror= () => reject("error")
        document.body.appendChild(script)
        document.body.removeChild(script)
    })
}
```

## 2. CORS

**CORS是跨域资源共享**，它允许浏览器向跨源服务器发出 XMLHttpRequest 请求，从而克服 Ajax 只能同源使用的限制。相比于jsonp只能发送 get 请求， CORS 允许发送任何类型的请求。

整个 CORS 通讯过程都是浏览器自动完成的，不需要用户参与。CORS通讯和同源的Ajax请求没有区别。浏览器一旦发现 Ajax 请求跨域，就会自动添加一些头部信息，有时候还会多一次附加请求。

浏览器将 CORS 请求分为两大类：简单请求和非简单请求

```
https://sillywa.com/2018/12/06/%E5%90%8C%E6%BA%90%E7%AD%96%E7%95%A5/
```

# 31、JavaScript 中的 arguments

arguments对象是所有（非箭头）函数中都可用的局部变量。将其转化为真正的 Array 的方法：

- Array.prototype.slice.call(arguments) 
- [].slice.call(arguments) 
- Array.from(arguments) 
- [...arguments] 

arguments.callee 指向参数所属的当前执行环境。

# 32、发布订阅者模式与观察者的实现

观察者模式定义了对象之间的一种一对多的关系，当目标对象 Subject 的状态发生改变的时候，所有依赖它的对象 Observe 都会得到通知。

Subject 具有的方法为：添加、删除、通知 Observe

Observe 具有的方法为：接受 Subject 的状态变更并处理

Subject 被称为发布者，即被观察者

Observe 被称为订阅者，即观察者

观察者模式的实现：

```js
// 定义被观察者
class Subject {

  constructor() {
    // 观察者数组
    this.observers = []
  }

  add(observer) {
    this.observers.push(observer)
  }
  remove(observer) {
    let index = this.observers.findIndex(item => item == observer)
    if(index != -1) {
      this.observers.splice(index, 1)
    }
  }
  notify() {
    this.observers.map(item => item.update())
  }
}
// 定义观察者
class Observe {
  constructor(name) {
    this.name = name
  }
  update() {
    console.log("我被通知了");
  }
}
```

发布订阅者模式基于一个事件通道，希望接受通知的对象订阅一个事件，被激活事件的对象通过发布事件的方式通知各个订阅了该事件的对象。

以下是发布订阅者模式的简单实现

```js
function PubSub() {
    this.handles = {}
    // 订阅事件
    // 事件类型以及事件触发时的回调函数
    this.subscribe = function(eventType, callback) {
        // 如果只是一个函数的话，后面绑定的相同事件的回调函数会覆盖之前的回调函数
        // this.handles[eventType] = callback
        // 因此考虑把每个事件类型的回调函数设置成一个数组，触发事件的时候，依次执行数组里面的函数
        if(!this.handles[eventType]) {
            // 初始化
            this.handles[eventType] = []
        }
        this.handles[eventType].push(callback)

    }

    // 取消事件
    // 取消指定 eventType 事件下的 fn 回调
    this.unsubscribe = function(eventType, fn) {
      if(!this.handles[eventType]) return
      let fnList = this.handles[eventType]
      if(!fn) {
        // 如果不传入指定回调，则清除该 eventType 下的所有回调
        fnList.length = 0
      } else {
        this.handles[eventType] = fnList.filter(item => item != fn)
      }
    }

    // 发布事件
    // 事件类型以及需要传递的参数
    this.publish = function(eventType, ...args) {
        // 触发事件的时候就执行该事件绑定的回调函数
        // this.handles[eventType].apply(null, args)
        // 现在需要遍历执行
        this.handles[eventType].map(fn => fn.apply(null, args))
    }
}
let pubSub = new PubSub()

let fn1 = time => {
  console.log("上班了", time)
}
pubSub.subscribe("onwork", fn1)
pubSub.subscribe("offwork", time => {
  console.log("下班了", time)
})
pubSub.subscribe("onwork", time => {
  console.log("上班了2", time)
})

pubSub.publish("onwork", "12:00")
pubSub.unsubscribe("onwork", fn1)
pubSub.publish("onwork", "12:00")
```

两者的区别：

- 观察者模式维护单一事件对应多个依赖该事件之间的对象关系 
- 发布订阅者模式维护多个事件及依赖各事件的对象之间的关系 
- 观察者模式是目标对象直接触发通知，观察对象被迫接受通知；发布订阅者模式多了一个事件中心，由其去管理通知广播（只通知订阅对应事件的对象） 
- 观察者模式对象间依赖关系较强，发布订阅者模式中对象之间实现真正的解耦

# 33、函数柯里化及其通用封装

柯里化（Currying）是把接受多个参数的函数变换成接受一个单一参数的函数，并且返回接受余下的参数且返回结果的新技术。

函数柯里化其含义就是给函数分步传参，每次传递部分参数，并返回一个更具体的函数接受剩余的参数。这中间可能接受多层这样的接受部分参数的函数，直至返回结果。

```js
function curry(fn, args) {
    // 获取fn的形参个数
    var arity = fn.length
    // 获取上一次的参数
    var args = args || []
    // 返回一个函数
    return function() {
        // 获取本次的参数并转化为数组
        var _args = Array.prototype.slice.call(arguments)
        // 将本次参数与上次参数合并
        Array.prototype.unshift.apply(_args, args)
        // 判断参数个数是否等于形参个数
        if(_args.length < arity) {
            // 如果小于形参个数，继续收集参数
            return curry(fn,_args)
        }
        // 否则执行函数的返回结果
        return fn.apply(null, _args)
    }
}
```



# 34、Symbol概念及其作用

ES5的对象属性名都是字符串，这容易造成命名冲突。ES6引入一种新的数据类型：**Symbol，用于创建独一无二的值**。Symbol 通过 Symbol() 函数生成。

Symbol作为属性名，该属性不会出现在 for in, for of 循环中，也不会被 Object.keys()、Object.getOwnPropertyNames() 返回。但他也不是私有属性，有一个Object.getOwnPropertySymbols 方法可以获取对象的所有 Symbol 属性。



# 35、Set和Map数据结构

set的方法：add、delete、has

map的方法：set、delete、has、get

set 无序且不重复

**Map和WeakMap的区别：**

WeakMap 的 key 只能是对象，且不能被遍历。当把 WeakMap 里面的 key 设为 null 之后，可以**被垃圾回收机制回收**，可以用于存储DOM节点。

```js
let a = {name: "sillywa"}
let b = {age: 90}
let map = new Map()
let weakMap = new WeakMap()
map.set(a, "test1")
weakMap.set(b, "test2")

a=null
b=null    
map.get(a)
map.get(b)
```

# 36、XSS 和 CSRF 攻击 和 SQL注入

**前端常见的三种web攻击技术: XSS, CSRF, SQL注入**

```js
CSRF：跨站请求伪造（Cross-site Request forgery）

用户登录 A 网站得到 cookie，然后访问危险的 B 网站，B网站携带A的cookie发送A的请求

XSS：跨域脚本攻击（Cross Site Scripting）

向网站 A 注入 JS代码，然后执行 JS 里的代码，篡改网站A的内容
```

# 37、浏览器进程及重要的线程以及浏览器内核的组成?

## 浏览器进程及重要的线程

```
一个进程有一个或多个线程，线程之间共同完成进程分配下来的任务。打个比方：

假如进程是一个工厂，工厂有它的独立的资源
工厂之间相互独立
线程是工厂中的工人，多个工人协作完成任务
工厂内有一个或多个工人
工人之间共享空间
再完善完善概念：

工厂的资源 -> 系统分配的内存（独立的一块内存）
工厂之间的相互独立 -> 进程之间相互独立
多个工人协作完成任务 -> 多个线程在进程中协作完成任务
工厂内有一个或多个工人 -> 一个进程由一个或多个线程组成
工人之间共享空间 -> 同一进程下的各个线程之间共享程序的内存空间（包括代码段、数据集、堆等）
进程是cpu资源分配的最小单位（是能拥有资源和独立运行的最小单位），线程是cpu调度的最小单位（线程是建立在进程的基础上的一次程序运行单位）。
```

### 重要进程

1. **浏览器主进程**，负责协调和控制各个页面 
2. **插件进程**：使用插件时会创建 
3. **GPU进程**：主要负责 UI 渲染 
4. **浏览器渲染进程**：负责页面渲染，脚本执行，事件处理 

浏览器内核，即我们的**浏览器渲染进程**，有名为 Renderer 进程，我们页面的渲染，js的执行，事件的循环都在这一个进程内进行，也就是说，该进程下面拥有多个线程，靠着这些线程共同完成渲染任务，那么这些线程是什么，如下：

### 重要线程

1. 图形用户界面 **GUI 渲染线程**

   - 负责渲染浏览器界面，包括解析 **HTML、CSS、构建 DOM 树，Render 树、布局与绘制**等 
   - 当界面需要重绘的时候或由于某种操作引发回流时，该线程就会执行 

2. **JS 引擎线程**

   - JS内核，也称 JS 引擎，负责处理执行 javascript 脚本 
   - 等待任务队列的任务的到来，然后加以处理，浏览器无论什么时候都只有一个 JS 引擎在运行 JS 程序 

3. **事件触发线程**

   - 听起来像 JS 的执行，但是其实归属于浏览器，而不是JS引擎，用来控制时间循环（可以理解，JS引擎自己都忙不过来，需要浏览器另开线程协助）

   - 当 JS 引擎执行代码块如 setTimeout 时（也可以来自浏览器内核的其他线程，如鼠标点击、AJAX 异步请求等），会将对应任务添加到事件线程中

   - 当对应的事件符合触发条件被触发时，该线程会把事件添加到待处理队列的队尾，等待 JS 引擎的处理

     注意：由于 JS 单线程关系，所以这些待处理队列中的事件都得排队等待 JS 引擎处理（当 JS 引擎空闲时才会去执行）

4. **定时器触发线程**

   - setInterval 和 setTimeout 所在线程 
   - 定时器计时并不是由 JS 引擎计时的，因为 JS 引擎是单线程的，如果 JS 引擎处于堵塞状态，那么会影响到计时的准确性 
   - 当计时完成被触发，事件会被添加到事件队列，等待 JS 引擎空闲了执行 

5. **异步 HTTP 请求线程**

   - 在 XMLHttpRequest 连接后启动的一个线程 
   - 线程如果检测到请求的状态变更，如果设置有回调函数，该线程会把回调函数添加到事件队列，等待 JS 引擎空闲了执行

### 为什么 JS 引擎是单线程的

如果 JS 设计成多线程，那么在进行 DOM 操作的时候就可能存在两个线程同时修改同一个 DOM 节点的情况，对于这种情况，DOM就成为了一种临界资源，我们还需要实现互斥访问才能解决，这样会使得 JS 引擎变得更加复杂。

### 为什么 GUI 渲染线程与 JS 引擎线程互斥

因为JS引擎可以修改DOM树，那么如果JS引擎在执行修改了DOM结构的同时，GUI线程也在渲染页面，那么这样就会导致渲染线程获取的DOM的元素信息可能与JS引擎操作DOM后的结果不一致。为了防止这种现象，GUI线程与JS线程需要设计为互斥关系，当JS引擎执行的时候，GUI线程需要被冻结。

### JS 引擎线程与事件触发线程、定时器触发线程、异步 HTTP 请求线程

事件触发线程、定时触发器线程、异步HTTP请求线程三个线程有一个共同点，那就是使用回调函数的形式，当满足了特定的条件，这些回调函数会被执行。这些回调函数被浏览器内核理解成事件，在浏览器内核中拥有一个事件队列，这三个线程当满足了内部特定的条件，会将这些回调函数添加到事件队列中，等待JS引擎空闲执行。

但是，JS引擎对事件队列（宏任务）与JS引擎内的任务（微任务）执行存在着先后循序，当每执行完一个事件队列的时间，JS引擎会检测内部是否有未执行的任务，如果有，将会优先执行（微任务）。

## 浏览器内核的组成

```
浏览器的结构:

1）用户界面（UI）
包括菜单栏、工具栏、地址栏、后退/前进按钮、书签目录等，
也就是能看到的除了显示页面的主窗口之外的部分；

2）浏览器引擎（Rendering engine）
也被称为浏览器内核、渲染引擎，主要负责取得页面内容、
整理信息（应用CSS）、计算页面的显示方式，然后会输出到
显示器或者打印机；

3）JS解释器
也可以称为JS内核，主要负责处理javascript脚本程序，
一般都会附带在浏览器之中，例如chrome的V8引擎；

4）网络部分
主要用于网络调用，例如：HTTP请求，
其接口与平台无关，并为所有的平台提供底层实现；

5）UI后端
用于绘制基本的窗口部件，比如组合框和窗口等。

6）数据存储
保存类似于cookie、storage等数据部分，
HTML5新增了web database技术，一种完整的轻量级客
户端存储技术。

主要浏览器：
IE、Firefox、Safari、Chrome、Opera。

它们的浏览器内核（渲染引擎）：
IE--Trident
FF(Mozilla)--Gecko
Safari--Webkit
Chrome--Blink（WebKit的分支）
Opera--原为Presto，现为Blink
```



# 38、前端常见性能优化

1. 资源压缩与合并：减少文件体积，提高加载速度

2. 减少 http 请求

3. 利用浏览器缓存：提升二次访问的速度

4. 使用 CDN

   利用浏览器缓存也仅仅只能提高二次访问的速度，对于首次访问的加速，可以从网络层面进行优化，最常见的手段就是 CDN（Content Delivery Network，内容分发网络）加速。

   通过将静态资源缓存到离用户很近的相同网络运营商的 CDN 节点上，不但能提升用户的访问速度，还能节省服务器的带宽消耗。

   **CDN 是怎么做到加速的呢？**

   首先 CDN 服务商会在全国各个网站部署计算节点，CDN 加速将网站的内容缓存在网络边缘，不同地区的用户就会访问到离自己最近的相同网络线路上的 CDN 节点。当请求到达 CDN 节点之后，节点会判断自己的缓存内容是否有效，如果有效，就会立即响应缓存的内容给用户，从而加快响应速度。如果CDN节点的缓存失效，它会根据服务器的配置去我们的内容源服务器获取最先的资源响应给用户，并将内容缓存下来以便响应后续访问的用户。

   因此，**一个地区只要有一个用户先加载资源，在 CDN 中建立了缓存，该地区其他用户访问相同的资源的时候就可以使用缓存了**。

5. 将 CSS 文件放在头部， JS 文件放在尾部

   CSS文件放在头部的原因是为了让用户第一时间看到的页面是有样式的。

   另外，JS 文件也不是不可以放在头部，只要给script标签加上 defer 属性就可以异步加载，延迟执行了。

6. 图片懒加载

7. 减少重绘和重排

# 39、defer 和 async 的区别



**JS的延迟加载有助于提高页面的加载速度。**



 **JS延迟加载的方式有哪些？**

**defer和async、动态创建DOM方式（用得最多）、按需异步载入JS**

```
window.onload() 方法用于在网页加载完毕后立刻执行的操作，即当 HTML 文档加载完毕后，立刻执行某个方法。通常用于 <body> 元素，在页面完全载入后(包括图片、css文件等等)执行脚本代码。
当纯HTML被完全加载以及解析时，DOMContentLoaded 事件会被触发，而不必等待样式表，图片或者子框架完成加载。
```



两者都是异步下载，加载文件的时候**不阻塞页面的渲染**，但是执行时刻不一样。

async 脚本在他下载结束之后立刻执行，同时会在 window.onload 事件之前执行，所以就有可能出现脚本执行顺序被打乱的情况

defer 的脚本都是在页面解析完毕之后，按照原本的顺序执行，同时会在 document.DOMContentLoaded 之前执行

{% asset_img defer和async.png %}

# 40、Object.defineProperty 与 Proxy 的区别

两者都可以用于做数据劫持，它们的区别如下：

1. Proxy 代理整个对象，而 Object.defineProperty 只代理对象上的某个属性 
2. 如果对象内部要全部递归代理， Proxy 可以只在调用时递归，而 Object.defineProperty 需要在一开始就全部递归，因此 Proxy 的性能较优 
3. 对象上定义新的属性的时候，Proxy可以监听到，而 Object.defineProperty 监听不到 
4. 数组新增删除修改时，Proxy可以监听到，Object.defineProperty监听不到。

# 41、单页应用的好处

1. 单页应用只需要获取一个 HTML 文件，相比传统的网页，http请求会有所减少，用户体验更好。 
2. 开发模式上可以使用前后端分离 

坏处：

SEO难度较高（爬虫只会爬取源码，不会执行脚本，而SPA，页面的DOM元素都是由js生成的，可供去爬取的内容大大减少）

初次加载耗时较多（打包后文件体积比较大，普通客户端渲染加载所有所需文件时间较长）

# 42、使用 IntersectionObsever API 监听元素出现在视图

在开发中，常需要知道某个元素是否进入了视图，传统的实现方法是监听页面的 scroll 事件，调用目标的 getBoundingClientRect() 方法，得到它对应于视口左上角的坐标，再判断是否在视口之内。这种方法的缺点就是，由于 scroll 事件比较密集，计算量大，很容易造成性能问题。

目前有一个新的 IntersectionObserver API，可以自动观察元素是否可见，Chrome51+已经支持，IE不支持。

使用方法：

```js
let io = new IntersectionObserver(callback, option)
io.observe(document.querySelector(".inner"))

io.unobserve(element)

// 关闭观察器
io.disconnect()
```

其中 callback 是一个函数，接受一个参数：

```js
let callback = function(entries) {
    console.log(entries)
    // 可以通过这个判断元素是否进入视图
    if(entries[0].isIntersecting) {
        console.log("进入视图啦"); 
    }
    // console.log(entries[0].intersectionRatio);

}
```

这个参数为一个数组，里面保存着被监听的每一个元素的一些信息，是一个IntersectionObserverEntry ，该对象的属性如下：

```js
IntersectionObserverEntry = {
    、、
    boundingClientRect: DOMRectReadOnly {x: 8, y: 524, width: 283, height: 100, top: 524, …}
    // 目标的可见比例，已见部分占整个元素的比例 0-1
    intersectionRatio: 0
    // 目标元素与视口（或根元素）的交叉区域的信息
    intersectionRect: DOMRectReadOnly {x: 0, y: 0, width: 0, height: 0, top: 0, …}
    // 判断元素是否在视图
    isIntersecting: false    
    isVisible: false
    // 根元素矩形区域的信息，getBoundingClientRect()方法的返回值
    rootBounds: DOMRectReadOnly {x: 0, y: 0, width: 896, height: 937, top: 0, …}
    // 被观察的目标元素
    target: div.inner
    // 可见性发生变化的事件
    time: 3280.2300000330433
}

```

# 43、gitflow 工作流

```
https://www.cnblogs.com/jeffery-zou/p/10280167.html
```



{% asset_img  29-5.png %}

该工作流首先会新建两个分支，一个是 master 分支，用户保存官方的发布历史，还有一个是 develop 分支，用于开发集成各种功能。

所有开发者都应该在 develop 分支上建立自己的分支进行开发，当自己的功能开发完成之后，请求合并到 develop 分支。

一旦 develop 分支聚集了足够多的新功能之后，可以基于 develop 分支创建一个用户产品发布的 release 分支，这个分支意味着一次发布的开始，并且本次发布不再增加新功能。

在一切准备就绪之后，这个 release 分支会被合并到 master 分支，并且用版本号打上标签。

当线上产品出现 bug 的时候，可以新建一个用于维护的分支， hotfix 分支，并且维护之后直接合并入 master 分支。

# 44、服务端渲染与浏览器渲染

浏览器渲染：页面上的内容是**加载进来的 js 文件渲染出来的，js 文件运行在浏览器上面**，服务端只返回一个 html 模板。

服务端渲染：页面的内容是通过服务端渲染出来的，浏览器直接显示服务端返回的 html 文件

优势：

- **利于SEO**，服务器返回给客户端的是已经渲染了内容的完整的HTML文件，网络爬虫可以抓取到页面的完整信息 
- **利于首屏渲染**，首屏的渲染是服务端发送过来的html字符串，并不依赖于js文件了，这就会使用户更快的看到页面的内容

# 45、webpack 打包原理

webpack在打包的时候，他会从入口开始，递归地构建一个依赖图，其中包含应用程序对应的一个或多个模块，然后将它们打包成一个或多个 bundle.js

# 46、CommonJS与ES6模块的差异

1. CommonJS 是运行时加载，ES6 模块是编译时输出接口 
2. CommonJS 输出的是一个值的复制，ES6 输出的是值得引用 
3. ES6 module 在编译期间会将所有 import 提升到顶部， commonjs 不会提升 require



# 47、数组扁平化的几种方式

1. 简单的遍历

   ```js
   function flatten(arr) {
       let result = []
       for(let item of arr) {
           if(Array.isArray(item)) {
               result = result.concat(flatten(item))
           } else {
               result.push(item)
           }
       }
       return result
   }
   ```

   

2. reduce 

   ```js
   function flatten(arr) {
       arr.reduce((origin, item) => {
         return  Array.isArray(item) ? origin.concat(flatten(item)) : [...origin, item]
       }, [])
   }
   ```

   

3. toString

   只针对数字数组

   ```js
   function flatten(arr) {
       return arr.toString().split(",").map(item => Number(item))
   }
   ```

   

4. some 与 扩展运算符

   ```js
   function flatten(arr) {
       while(arr.some(item => Array.isArray(item))){
           arr = [].concat(...arr)
       }
       return arr
   }
   ```

   

5. generator 函数

   ```js
   function flatten(arr) {
       function *fn(arr) {
           for(let item of arr) {
               if(Array.isArray(item)) {
                   yield *fn(item)
               } else {
                   yield item
               }
           }
       }
       return [...fn(arr)]
   }
   ```

# 48、input change keyup的区别

input：输入了内容就会触发 input 事件

keyup：键盘抬起触发，不抬起不触发

change：当输入框失去焦点并且内容有变化的时候触发

blur：失去焦点触发

# 49、一道原型链的易错题

```js
var A = function() {};
A.prototype.n = 1;
var b = new A();
A.prototype = { n: 2, m: 3 };
var c = new A();
console.log(b.n);    // 1
console.log(b.m);    // undifined
console.log(c.n);    // 2
console.log(c.m);    // 3
```

b 是直接指向 A 的原型对象的，而不是它的 prototype，虽然刚开始两者一样，但是重写了 A 的 prototype 属性之后，b 还是指向原来的对象，而 A.prototype 指向的位置发生了变化

# 50、JS中的DOM元素的一些样式属性

## 1、clientWidth、clientHeight、offsetWidth、offsetHeight

### clientWidth、clientHeight

- 这两个属性可以获取元素的**可见宽度和高度**
- 这些属性都是不带px的,返回都是一个数字，可以直接进行计算
- 会获取元素宽度和高度，包括**内容区和内边距**
- 都是只读元素，不能修改

### offsetWidth、offsetHeight

获取元素宽度和高度，包括**内容区和内边距和边框**

```html
<script type="text/javascript">
    window.onload = function(){
        var box1 = document.getElementById('box1');
        console.log(box1.clientWidth, box1.clientHeight); // 120 120
    	console.log(box1.offsetWidth, box1.offsetHeight); // 140 140
        
    }
</script>
<style type="text/css">
    #box1{
        width: 100px;
        height: 100px;
        background-color: red;
        padding: 10px;
        border: 10px solid yellow;
    }
</style>
<body>
	<div id="box1"></div>
</body>
```

## 2、offsetParent

- 可以用来获取当前元素的**定位父元素**
- 会获取到离当前元素最近的**开启了定位**【绝对定位、相对顶对 ........】的祖先元素
- 如果没有元素开启定位，那么返回 **body**

## 3、offsetLeft、offsetTop

- 当前元素相对于其**定位父元素**的水平偏移量offsetTop
- 当前元素相对于其**定位父元素**的垂直偏移量

## 4、scrollWidth、scrollHeight

- 可以获取元素**整个滚动区域的高度和宽度**


## 5、scrollLeft、scrollTop

- 获取水平、垂直**滚动条滚动的距离**

## 6、clientX、clientY

- 获取鼠标的**水平坐标和垂直坐标**

```
clientX：当鼠标事件发生时（不管是onclick，还是omousemove，onmouseover等），鼠标相对于浏览器（这里说的是浏览器的有效区域）x轴的位置；
clientY：当鼠标事件发生时，鼠标相对于浏览器（这里说的是浏览器的有效区域）y轴的位置；
screenX：当鼠标事件发生时，鼠标相对于显示器屏幕x轴的位置；
screenY：当鼠标事件发生时，鼠标相对于显示器屏幕y轴的位置；
offsetX：当鼠标事件发生时，鼠标相对于事件源x轴的位置
offsetY：当鼠标事件发生时，鼠标相对于事件源y轴的位置
```



# 51、事件冒泡和事件捕获

## **1.事件冒泡**(event bubbling)

微软提出了名为**事件冒泡**的事件流。事件按照从最特定的事件目标到最不特定的事件目标(document对象)的顺序触发。可以想象把一颗石头投入水中，泡泡会一直从水底冒出水面。也就是说，事件会从最内层的元素开始发生，一直向上传播，直到document对象。

## **2.事件捕获**(event capturing)

网景提出另一种事件流名为**事件捕获**。事件从最不精确的对象(document 对象)开始触发，然后到最精确(也可以在窗口级别捕获事件，不过必须由开发人员特别指定)，与事件冒泡相反，事件会从最外层开始发生，直到最具体的元素。同样形象的比喻一下可以想象成警察逮捕屋子内的小偷，就要从外面一层层的进入到房子内。



```text
element.addEventListener(event, function, useCapture)
```

addEventListener方法用来为一个特定的元素绑定一个事件处理函数，是JavaScript中的常用方法，其传入三个参数，分别是‘没有on的事件类型’，‘事件处理函数’，‘控制事件阶段’，第三个参数是boolean类型，默认是false，表示在事件冒泡的阶段调用事件处理函数，像上图中传入true，就表示在事件捕获的阶段调用事件处理函数。



# 52、什么是async 和 await？解决了什么问题？

## 是什么？

是`generator + Promise`的语法糖，主要的作用是**用同步方式执行异步操作**，`await`只能在`async函数`中使用，`async函数`执行会返回一个`Promise`，值由函数的return值所决定

## async

async是一个加在函数前的修饰符，被async定义的函数会默认返回一个Promise对象resolve的值。因此对async函数可以直接then，返回值就是then方法传入的函数。

## await

await 也是一个修饰符，只能放在async定义的函数内。可以理解为**等待**。

await 修饰的如果是Promise对象：可以获取Promise中**返回的内容**（**resolve或reject的参数**），**且取到值后语句才会往下执行**；

如果不是Promise对象：把这个非promise的东西当做await表达式的结果。

# 53、isNaN 与 Number.isNaN的区别？

- `isNaN`：除了判断NaN为true外，还会把不能转成数字判断为true，例如'dasd'
- `Number.isNaN`：只会判断NaN为true

# 54、解决遍历对象时，把原型上的属性遍历出来了咋办？

使用`hasOwnProperty`判断

```js
function Person(name) {
  	this.name = name
}
Person.prototype.age = 23
const person = new Person('Sunshine_lin')
for (const key in person) { 
    console.log(key); // name age
} 
// 使用 hasOwnProperty
for (const key in person) {
  	person.hasOwnProperty(key) && console.log(key); // name
} 
```

# 55、valueOf 与 toString？

- `valueOf`比较偏向于计算，`toString`偏向于显示
- 对象转换时，优先调用`toString`
- 强转字符串时优先调用`toString`，强转数字时优先调用`valueOf`
- 正常情况下，优先调用`toString`
- 运算操作符情况下优先调用`valueOf`



# 56、讲一讲JavaScript的装箱和拆箱？

**装箱：把基本数据类型转化为对应的引用数据类型的操作**

看以下代码，s1只是一个基本数据类型，他是怎么能调用indexOf的呢？

```js
const s1 = 'Sunshine_Lin'
const index = s1.indexOf('_')
console.log(index) // 8
```

原来是JavaScript内部进行了装箱操作

- 1、创建String类型的一个实例；
- 2、在实例上调用指定的方法；
- 3、销毁这个实例；

```js
var temp = new String('Sunshine_Lin')
const index = temp.indexOf('_')
temp = null
console.log(index) // 8
```

**拆箱：将引用数据类型转化为对应的基本数据类型的操作**

通过valueOf或者toString方法实现拆箱操作

```js
var objNum = new Number(123);  
var objStr =new String("123");   
console.log( typeof objNum ); //object
console.log( typeof objStr ); //object 
console.log( typeof objNum.valueOf() ); //number
console.log( typeof objStr.valueOf() ); //string

console.log( typeof objNum.toString() ); // string 
console.log( typeof objStr.toString() ); // string
```



# 56、== 与 === 的区别？

- `==`：比较过程会进行隐式转换
- `===`：值相同，类型相同才会为true



# 57、绑定点击事件有几种方式？

三种

- `xxx.onclick = function (){}`
- `<xxx onclick=""></xxx>`
- `xxx.addEventListener('click', function(){}, false)`



# 58、JavaScript的事件流模型有哪些？

- `事件冒泡`：由最具体的元素接收，并往上传播
- `事件捕获`：由最不具体的元素接收，并往下传播
- `DOM事件流`：事件捕获 -> 目标阶段 -> 事件冒泡

# 58、为什么要有这样的事件流（传进来又传出去）吗？

Ⅰ刚开始是因为网景和[微软]()的浏览器的实现方式不同，后来的浏览器为了兼容他俩所以都这样设计了。

Ⅱ更重要的是可以根据想要的效果不同而在不同的阶段处理事件。如果某个元素被点击了，但是我不想让它被触发，就可以在事件捕获阶段把它直接砍掉。

# 59、Ajax、Axios、Fetch有啥区别？

- `Ajax`：是对XMLHttpRequest(XHR)的封装
- `Axios`：是基于Promise对XHR对象的封装
- `Fetch`：是window的一个方法，基于Promise，与XHR无关，不兼容IE

# 60、load、$(document).ready、DOMContentLoaded的区别？

- `$(document).ready、DOMContentLoaded`：DOM树构建完毕，但还没有请求静态资源
- `load`：静态资源请求完毕

# 61、如何阻止事件默认行为？

```js
function stopDefault(e) {
  if (e.preventDefault) {
    e.preventDefault();
  } else {
    window.event.returnValue = false;
  }
}
```



# 62、如何实现数组去重(Map & Set)？

```js
// 使用 Map 去重
function quchong1(arr) {
  const newArr = []
  arr.reduce((pre, next) => {
    if (!pre.get(next)) {
      pre.set(next, 1)
      newArr.push(next)
    }
    return pre
  }, new Map())
  return newArr
}

// 使用 Set 去重
function quchong (arr) {
    return [...new Set(arr)]
}
```

# 63、Set与Array的区别是什么？

- Set使用has判断有无元素，数组使用索引
- Set添加元素使用方法add，数组用push、unshift
- Set长度为size，数组为length
- Set会自动把同样的基础数据类型去重，数组不能
- Set删除元素用delete，数组用splice、pop、shift
- Set可以使用clear清空，数组需要重新赋值[]
- 数组可以传入new Set(array)，实现数组转Set
- Set可以使用keys、value方法，转数组
- Set自带forEach方法进行遍历

# 64、Map与Object的区别是什么？

- Map使用set设置属性，对象使用obj[key] = value
- Map使用get获取属性值，对象使用obj[key]
- Map使用has判断属性存在与否，对象只能obj[key]
- Map删除元素使用delete方法，对象使用delete关键字
- Map使用clear进行情空，对象需要重新赋值{}
- Map和对象都可以使用entries方法转数组键值对
- Map自带forEach方法进行遍历

# 65、处理异步的方法有哪些？

- 回调函数
- Promise
- 事件监听
- 发布订阅
- async/await

# 66、Math常用方法有哪些？

| 方法                | 作用            |
| :------------------ | :-------------- |
| Math.max(...arr)    | 取arr中的最大值 |
| Math.min(...arr)    | 取arr中的最小值 |
| Math.ceil(小数)     | 小数向上取整    |
| Math.floor(小数)    | 小数向下取整    |
| Math.round(小数)    | 小数四舍五入    |
| Math.sqrt(num)      | 对num进行开方   |
| Math.pow(num, m)    | 对num取m次幂    |
| Math.random() * num | 取0-num的随机数 |

# 67、JS中有哪些不同类型的弹出框？

在JS中有三种类型的弹出框可用，分别是：

- Alert
- Confirm
- Prompt

# 68、如何将 JS 日期转换为ISO标准

```js
var date = new Date();
var n = date.toISOString();
console.log(n);
// YYYY-MM-DDTHH:mm:ss.sssZ
```

# 69、如何在JS中编码和解码 URL

- 编码：encodeURI()
- 解码：decodeURI()

# 70、什么是BOM？有哪些api？

BOM就是`browser object model`，`浏览器对象模型`

| api                | 作用                 | 代表方法或属性                                               |
| :----------------- | :------------------- | :----------------------------------------------------------- |
| window.history     | 操纵浏览器的记录     | history.back() history.go(-1)                                |
| window.innerHeight | 获取浏览器窗口的高度 |                                                              |
| window.innerWidth  | 获取浏览器窗口的宽度 |                                                              |
| window.location    | 操作刷新按钮和地址栏 | location.host：获取域名和端口 location.hostname：获取主机名 location.port：获取端口号 location.pathname：获取url的路径 location.search：获取?开始的部分 location.href：获取整个url location.hash：获取#开始的部分 location.origin：获取当前域名 location.navigator：获取当前浏览器信息 |



# 71、BOM 和 DOM 的关系

**BOM**全称Browser Object Model，即浏览器对象模型，主要处理浏览器窗口和框架。

DOM全称Document Object Model，即文档对象模型，是 HTML 和XML 的应用程序接口（API），遵循W3C 的标准，所有浏览器公共遵守的标准。

JS是通过访问**BOM**（Browser Object Model）对象来访问、控制、修改客户端(浏览器)，由于**BOM**的window包含了document，window对象的属性和方法是直接可以使用而且被感知的，因此可以直接使用window对象的document属性，通过document属性就可以访问、检索、修改XHTML文档内容与结构。因为document对象又是DOM的根节点。

可以说，BOM包含了DOM(对象)，浏览器提供出来给予访问的是BOM对象，从BOM对象再访问到DOM对象，从而js可以操作浏览器以及浏览器读取到的文档。



# 72、JS中的substr()和substring()函数有什么区别

substr() 函数的形式为substr(startIndex,length)。它从startIndex返回子字符串并返回'length'个字符数。

```js
var s = "hello";
( s.substr(1,4) == "ello" ) // true
```

substring() 函数的形式为substring(startIndex,endIndex)。它返回从startIndex到endIndex - 1的子字符串。

```js
var s = "hello";
( s.substring(1,4) == "ell" ) // true
```



# 73、解释一下 "use strict" ?

“use strict”是Es5中引入的js指令。使用“use strict”指令的目的是强制执行严格模式下的代码。在严格模式下，咱们不能在不声明变量的情况下使用变量。早期版本的js忽略了“use strict”。

# 74、eval是做什么的？

它的功能是把对应的字符串解析成JS代码并运行；应该避免使用eval，不安全，非常耗性能（2次，一次解析成js语句，一次执行）。

```js
eval("x=10;y=20;document.write(x*y)");	// 200
document.write("<br>" + eval("2+2"));	// 4
document.write("<br>" + eval(x+17));	// 27
```



# 75、DOM操作

## （1）创建新节点

createDocumentFragment() //创建一个DOM片段
createElement() //创建一个具体的元素
createTextNode() //创建一个文本节点

## （2）添加、移除、替换、插入

appendChild()
removeChild()
replaceChild()
insertBefore() //在已有的子节点前插入一个新的子节点

## （3）查找

getElementsByTagName() //通过标签名称
getElementsByName() //通过元素的Name属性的值(IE容错能力较强，会得到一个数组，其中包括id等于name值的)
getElementById() //通过元素Id，唯一性
document.querySelector() : 该方法总会返回唯一的一个元素，如果满足条件的元素有多个，那么它只会返回第一个
document.querySelectorAll(): 它会将符合条件的元素封装到一个数组中返回, 即使符合条件的元素只有一个, 他也会返回一个数据



# 76、 哪些操作会造成内存泄漏

全局变量、闭包、DOM清空或删除时，事件未清除、子元素存在引用

# 76、下面代码会造成内存泄漏么?

```js
var user = {    
    name: '撩课', 
    age: 12, 
    gender: '女'
};

var box = document.getElementById('box');
box.onclick = function() {
    box.innerHTML = user.name;
};
// ...其他操作
user = null; // 释放对象

答：存在内存泄漏，
这是js中垃圾回收的引用计数造成的。
完全去除内存泄漏是不现实的，但是，
如果采用下面的方法可以减少内存泄漏：

var user = {    
   name: '撩课',    
   age: 12,    
   gender: '女'
};

var box = document.getElementById('box');
(function (name) {
    box.onclick = function() {
        box.innerHTML = name;
    };
})(user.name);
// ...其他操作
user = null; // 释放对象
```



# 77、什么是Cookie 隔离？（或者：请求资源的时候不要带cookie怎么做）

  通过使用多个非主要域名来请求静态文件，如果静态文件都放在主域名下，那静态文件请求的时候带有的cookie的数据提交给server是非常浪费的，还不如隔离开。因为cookie有域的限制，因此不能跨域提交请求，故使用非主要域名的时候，请求头中就不会带有cookie数据，这样可以降低请求头的大小，降低请求时间，从而达到降低整体请求延时的目的。同时这种方式不会将cookie传入server，也减少了server对cookie的处理分析环节，提高了server的http请求的解析速度。

# 78、响应事件

onclick鼠标点击某个对象；

onfocus获取焦点；

onblur失去焦点；

onmousedown鼠标被按下

# 79、XML与JSON的区别？

1) 数据体积方面。JSON相对于XML来讲，数据的体积小，传递的速度更快些。
2) 数据交互方面。JSON与JavaScript的交互更加方便，更容易解析处理，更好的数据交互。
3) 数据描述方面。JSON对数据的描述性比XML较差。
4) 传输速度方面。JSON的速度要远远快于XML。

# 80、HTML与XML的区别？

（1）XML用来传输和存储数据，HTML用来显示数据；
（2）XML使用的标签不用预先定义
（3）XML标签必须成对出现
（4）XML对大小写敏感
（5）XML中空格不会被删减
（6）XML中所有特殊符号必须用编码表示
（7）XML中的图片必须有文字说明

# 81、渐进增强与优雅降级

渐进增强：针对低版本浏览器进行构建页面，保证最基本的功能，然后再针对高级浏览器					进行效果、交互等改进，达到更好的用户体验。
优雅降级：首先完整地实现整个网站, 包括其中的功能和效果. 然后再为那些无 法支持所有功能的浏览器增加候选方案,  使之在旧式浏览器上以某种形式降级体验 却不至于完全失效。

# 82、Web Worker和Web Socket？

web socket：在一个单独的持久连接上提供全双工、双向的通信。使用自定义的协议（ws://、wss://），同源策略对web socket不适用。
web worker：运行在后台的JavaScript，不影响页面的性能。
创建worker：var worker = new Worker(url);
向worker发送数据：worker.postMessage(data);
接收worker返回的数据：worker.onmessage
终止一个worker的执行：worker.terminate();

# 83、web应用从服务器主动推送data到客户端的方式？

JavaScript数据推送：commet（基于http长连接的服务器推送技术）。
基于web socket的推送：SSE（server-send Event）

# 84、如何删除一个cookie？

1） 将cookie的失效时间设置为过去的时间（expires）

```text
document.cookie = 'user='+ encodeURIComponent(‘name’) + 'expires='+ new Date(0);
```

2） 将系统时间设置为当前时间往前一点时间

```text
var data = new Date();
date.setDate(date.getDate()-1);
```

# 85、attribute与property的区别？

attribute是dom元素在文档中作为html标签拥有的属性
property是dom元素在js中作为对象拥有的属性。
所以，对于html的标准属性来说，attribute和property是同步的，是会自动更新的。但对于自定义属性，他们不同步。

# 86、Ajax请求的页面历史记录状态问题？

（1）通过location.hash记录状态，让浏览器记录Ajax请求时页面状态的变化。
（2）通过HTML5的history.pushstate，来实现浏览器地址栏的无刷新改变。

# 87、**字面量new出来的对象和 Object.create(null)创建出来的对象有什么区别**

**答：**

- 字面量和new创建出来的对象会继承Object的方法和属性，他们的隐式原型会指向Object的显式原型，
- 而 `Object.create(null)`创建出来的对象原型为null，作为原型链的顶端，自然也没有继承Object的方法和属性

# 88、mouseenter 和 mouseover 的区别



- 不论鼠标指针穿过被选元素或其子元素，都会触发 mouseover 事件，对应 mouseout。
- 只有在鼠标指针穿过被选元素时，才会触发 mouseenter 事件，对应 mouseleave。



# 89、DOM事件机制

 javascript与HTML之间的交互是通过事件实现的。事件就是文档或浏览器窗口中发生的一些特定的交互瞬间。
  事件三要素：
     事件目标（event target）   
       发生的事件与之相关联或与之相关的对象
     事件处理程序（event [handler](https://so.csdn.net/so/search?q=handler&spm=1001.2101.3001.7020)）
       处理或相应事件的函数
     事件对象（event object）   
       与特定事件相关且包含有关该事件详细信息的对象。

# 90、document.write 和 innerHTML 的区别

- document.write 是直接写入到页面的内容流，如果在写之前没有调用 document.open, 浏览器会自动调用 open。每次写完关闭之后重新调用该函数，会导致页面被重写。
- innerHTML 则是 DOM 页面元素的一个属性，代表该元素的 html 内容。你可以精确到某一个具体的元素来进行更改。如果想修改 document 的内容，则需要修改 document.documentElement.innerElement。
- innerHTML 将内容写入某个 DOM 节点，不会导致页面全部重绘。
- innerHTML 很多情况下都优于 document.write，其原因在于其允许更精确的控制要刷新页面的那一个部分。
- document.write 是重写整个 document, 写入内容是字符串的 html；innerHTML 是 HTMLElement 的属性，是一个元素的内部 html 内容

# 91、JS 识别不同浏览器信息

```js
function myBrowser() {
  var userAgent = navigator.userAgent; //取得浏览器的userAgent字符串  
  var isOpera = userAgent.indexOf("Opera") > -1;
  if (isOpera) {
    return "Opera"
  }; //判断是否Opera浏览器  
  if (userAgent.indexOf("Firefox") > -1) {
    return "Firefox";
  }  //判断是否Firefox浏览器  
  if (userAgent.indexOf("Chrome") > -1) {
    return "Chrome";
  }   //判断是否Google浏览器  
  if (userAgent.indexOf("Safari") > -1) {
    return "Safari";
  } //判断是否Safari浏览器  
  if (userAgent.indexOf("compatible") > -1 && userAgent.indexOf("MSIE") > -1 && !isOpera) {
    return "IE";
  }; //判断是否IE浏览器  
}
```

# 92、JS组成

- 核心（ECMAScript） 描述了该语言的语法和基本对象
- 文档对象模型(DOM) 描述了处理网页内容的方法和接口
- 浏览器对象模型(BOM) 描述了与浏览器进行交互的方法和接口



# 93、JSON的了解？

JSON(JavaScript Object Notation) 是一种轻量级的数据交换格式。

它是基于 JavaScript 的一个子集。

数据格式简单，易于读写，占用带宽小。

格式：采用键值对。例如：{ “age‟: ‟12‟, ”name‟: ‟back‟ }

# 94、打乱数组的顺序

```js
var list = [1, 2, 3];
console.log(list.sort(function() { Math.random() - 0.5 })); // [2, 1, 3]
```

# 95、判断对象是对象还是数组

```js
const obj = {
    name: 'lt',
    age: 22
};
const arr = [1, 2, 3];

function is(a){
    if(typeof a == 'object'){
        if(a.length){
            return '是数组';
        }else{
            return '是对象';
        }
    }else{
        return '啥也不是';
    }
};

function is2(a){
    if(a instanceof Array){
        return '是数组';
    } else if (a instanceof Object) {
        return '是对象';
    } else {
        return '啥也不是';
    }
};
```

# 96、堆和栈的区别？



一、堆栈空间分配区别

- 栈（操作系统）：由操作系统自动分配释放 ，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈；　　
- 堆（操作系统）：一般由程序员分配释放， 若程序员不释放，程序结束时可能由 OS 回收，分配方式倒是类似于链表。　　

二、堆栈缓存方式区别

- 栈使用的是一级缓存， 他们通常都是被调用时处于存储空间中，调用完毕立即释放；　　
- 堆是存放在二级缓存中，生命周期由虚拟机的垃圾回收算法来决定（并不是一旦成为孤儿对象就能被回收）。所以调用这些对象的速度要相对来得低一些。　　

三、堆栈数据结构区别

- 堆（数据结构）：堆可以被看成是一棵树，如：堆排序；　　
- 栈（数据结构）：一种先进后出的数据结构。



# 97、getBoundingClientRect()方法

## getBoundingClientRect()分析

getBoundingClientRect()获取元素位置，这个方法没有参数

getBoundingClientRect()用于获得页面中某个元素的左，上，右和下分别相对浏览器视窗的位置。 

getBoundingClientRect()是DOM元素到浏览器可视范围的距离（不包含文档卷起的部分）。 

{% asset_img  getBoundingClientRect.png %}

该函数返回一个Object对象，该对象有6个属性：top,lef,right,bottom,width,height； 

```javascript
<div id="box"></div>
var object=document.getElementById('box');  
rectObject = object.getBoundingClientRect();
	rectObject.top：元素上边到视窗上边的距离;
	rectObject.right：元素右边到视窗左边的距离;
	rectObject.bottom：元素下边到视窗上边的距离;
	rectObject.left：元素左边到视窗左边的距离;
	rectObject.width：是元素自身的宽
	rectObject.height是元素自身的高
```



# 98、IntersectionObserver方法

网页开发中，我们经常要检测某个元素是否显示在了界面上（用户能不能看到它），而`IntersectionObserver`可以很快速的帮我实现这一功能；
 `IntersectionObserver`可以对某一个元素进行监听，当这个元素显示到界面上的时候，会有回调，我们可以再回调中，进行逻辑的处理；

四步骤：

- 创建观察器对象；
- 开始观察某一元素；
- 停止观察某一元素；
- 关闭观察器

## 创建观察器对象

```js
var io = new IntersectionObserver(callback, option);
```

两个参数：

- callback：回调函数，目标元素的可见性变化时，就会调用观察器的回调函数

> callback一般会触发两次。一次是目标元素刚刚进入视口（开始可见），另一次是完全离开视口（开始不可见）
>  `callback`函数的参数（`entries`）是一个数组，每个成员都是一个[`IntersectionObserverEntry`](https://links.jianshu.com/go?to=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FAPI%2FIntersectionObserverEntry)对象

- Option：可选的配置对象；

## 开始观察某一元素

使用observe方法

```jsx
let example = document.getElementById('example');
io.observe(example);
```

> observe可以观察多个节点元素，如果要观察多个节点，就要多次调用这个方法。

## 停止观察某一元素

使用unobserve方法；

```css
io.unobserve(example);
```

> unobserve也可以调用多次，停止观察多个元素；

## 关闭观察器

```css
io.disconnect();
```



# 99、数组的splice 与 slice 的区别？

| 方法   | 参数                                  |
| :----- | :------------------------------------ |
| splice | splice(start, num, item1, item2, ...) |
| slice  | slice(start, end)                     |

# 100、substr 和 substring 的区别？

| 方法      | 参数                 |
| :-------- | :------------------- |
| substr    | substr(start,length) |
| substring | substring(start,end) |

# 1、includes 比 indexOf好在哪？

includes可以检测`NaN`，indexOf不能检测`NaN`，includes内部使用了`Number.isNaN`对`NaN`进行了匹配

# 2、什么是Promise？解决了什么问题？

promise是异步操作的一种解决方案，一般用来解决层层嵌套的回调函数（回调地狱 callback hell）的问题。

#### 好处

- 解决了回调地狱
- 提高代码可读性
- 状态不可逆不可变
- 存值，使用时再执行方法

#### 方法

- `then`

- `catch`：专门用来处理rejected状态。

- `all`：用来关注多个Promise对象的变化的,传入多个promise实例，包装成一个新的promise实例返回。Promise.all()参数可以是数组。Promise.all()的状态变化与所有传入Promise实例对象状态有关，所有的状态都变成resolved，最终的状态才会变成resolved，只要有一个变成rejected，最终的状态就变成rejected。

- `race`：状态取决于第一个完成的Promise对象，如果第一个完成的成功了，那么最终的就成功，否则最终失败。

- `allSetteled`：状态与传入的Promise状态无关，它永远都是成功状态的，它只会记录下各个Promise的表现。

- `any`参数中的Promise都失败最终才会失败，参数中只要有一个为成功状态，那么最终就是成功。返回是第一个成功的值，后面就不管了。

  实际应用：一次性加载多张图片时，哪一张先加载出来就显示哪一张，此时就可以使用Promise.any()方法

- `finally`：用来处理最终状态的。本质上是then()的特例。该方法不论任何变化都会执行（不变化不执行），最终执行。

# 3、什么是文档碎片？

- 是什么：一个容器，用于暂时存放创建的dom元素，使用`document.createDocumentFragment()`创建
- 有什么用：将需要添加的大量元素 先添加到文档碎片 中，再将文档碎片添加到需要插入的位置，大大减少dom操作，提高性能 例子

```js
var oFragmeng = document.createDocumentFragment(); 
for(var i=0;i<10000;i++)
{ 

    var op = document.createElement("span"); 

    var oText = document.createTextNode(i); 

    op.appendChild(oText); 

    //先附加在文档碎片中

    oFragmeng.appendChild(op);  

} 
//最后一次性添加到document中
document.body.appendChild(oFragmeng); 
```

# 4、彻底理解宏任务和微任务

**首先一定要确认的是**：在任务队列中，不管任务队列的顺序是怎么样的，微任务一定在宏任务之前被运行的！！！并且如果有新加入的微任务也一定在宏任务之前运行！！！

## 第一题

14586237

```js
console.log(1)
setTimeout(() => {
  console.log(2)
  Promise.resolve().then(() => {
    console.log(3)
  })
});
console.log(4)
new Promise((resolve,reject) => {
  console.log(5)
}).then(() => {
  console.log(6)
  setTimeout(() => {
    console.log(7)
  })
})
console.log(8)
```

## 第二题

236451

```js
setTimeout(() => {
  console.log(1)
}, 0)
console.log(2)
const p = new Promise((resolve) => {
  console.log(3)
  resolve()
}).then(() => {
  console.log(4)
}).then(() => {
  console.log(5)
})
console.log(6)
```

## 第三题

123465

```js
new Promise((resolve,reject)=>{
  console.log(1)
  resolve()
}).then(()=>{
  console.log(2)
  new Promise((resolve,reject)=>{
      console.log(3)
      resolve()
  }).then(()=>{
      console.log(4)
  }).then(()=>{
      console.log(5)
  })
}).then(()=>{
  console.log(6)
})
```

## 第四题

123456

```js
这个题我的理解是：因为你里面的 then 是返回了一个promise，并且有.then，所以下面的.then是无法第一时间添加到任务队列中的，他要等 人家 return的那个promise出来之后才能添加到任务队列，所以要进行等待。

new Promise((resolve, reject) => {
  console.log(1)
  resolve()
}).then(() => {
  console.log(2)
  // 多了个return
  return new Promise((resolve, reject) => {
    console.log(3)
    resolve()
  }).then(() => {
    console.log(4)
  }).then(() => { // 相当于return了这个then的执行返回Promise
    console.log(5)
  })
}).then(() => {
  console.log(6)
})
```

## 第五题

17238465

```js
new Promise((resolve, reject) => {
  console.log(1)
  resolve()
}).then(() => {
  console.log(2)
  new Promise((resolve, reject) => {
    console.log(3)
    resolve()
  }).then(() => {
    console.log(4)
  }).then(() => {
    console.log(5)
  })
}).then(() => {
  console.log(6)
})
new Promise((resolve, reject) => {
  console.log(7)
  resolve()
}).then(() => {
  console.log(8)
})
```

## 第六题

41368275

```js
这里我的理解是：因为 async1 中在 await async2 执行之后的结果，而且这个函数被 async修饰，返回的是一个 promise 对象，所以相当于后面的 log(2) 是，这个promise的.then

async function async1() {
  console.log(1);
  await async2();
  console.log(2);
}
async function async2() {
  console.log(3);
}
console.log(4);
setTimeout(function () {
  console.log(5);
});
async1()
new Promise(function (resolve, reject) {
  console.log(6);
  resolve();
}).then(function () {
  console.log(7);
});
console.log(8);
```

# 5、AMD和CMD区别？

| 模块化 | 代表应用   | 特点                                                         |
| :----- | :--------- | :----------------------------------------------------------- |
| AMD    | require.js | 1、AMD的api默认一个当多个用 2、依赖前置，异步执行            |
| CMD    | sea.js     | 1、CMD的api严格区分，推崇职责单一 2、依赖就近，按需加载，同步执行 |

# 6、Commonjs 和 ES6 Module的区别？

- 1、前者是拷贝输出，后者是引用输出
- 2、前者可修改引入值，后者只读
- 3、前者是运行时，后者是编译时

# 7、为什么Commonjs不适用于浏览器

```js
var math = require('math');

math.add(2, 3);
```

第二行math.add(2, 3)，在第一行require('math')之后运行，因此必须等math.js加载完成。也就是说，如果加载时间很长，整个应用就会停在那里等。

这对服务器端不是一个问题，因为所有的模块都存放在本地硬盘，可以同步加载完成，等待时间就是硬盘的读取时间。但是，对于浏览器，这却是一个大问题，因为模块都放在服务器端，等待时间取决于网速的快慢，可能要等很长时间，浏览器处于"假死"状态。

因此，浏览器端的模块，不能采用"同步加载"（synchronous），只能采用"异步加载"（asynchronous）。这就是AMD规范诞生的背景。

# 8、forEach怎么跳出循环？

```js
function getItemById(arr, id) {
  var item = null;
  try {
    arr.forEach(function (curItem, i) {
      console.log(i)
      if (curItem.id == id) {
        item = curItem;
        throw Error();
      }
    })
  } catch (e) {}
  return item;
}
```

# 9、JS中如何将页面重定向到另一个页面？

- 1、使用 `location.href`：window.location.href =“https://www.onlineinterviewquestions.com/”)
- 2、使用 `location.replace`：window.location.replace(" https://www.onlineinterviewquestions.com/;")

# 10、鼠标事件有哪些？

| 事件       | 说明                                           |
| :--------- | :--------------------------------------------- |
| click      | 单机鼠标左键触发                               |
| dbclick    | 双击鼠标左键触发                               |
| mousedown  | 单机鼠标任意一个按键都触发                     |
| mouseout   | 鼠标指针位于某个元素上且将要移出元素边界时触发 |
| mouseover  | 鼠标指针出某个元素到另一个元素上时触发         |
| mouseup    | 松开任意鼠标按键时触发                         |
| mousemove  | 鼠标在某个元素上时持续发生                     |
| mouseenter | 鼠标进入某个元素边界时触发                     |
| mouseleave | 鼠标离开某个元素边界时触发                     |

{% asset_img JS鼠标事件的常用位置.png %}

# 11、键盘事件有哪些？

> 注明：`event`对象上的`keyCode`属性，是按下的按键的`ASCLL值`，通过这个值可辨别是按下哪个按键。`ASCLL`表在此ASCII码一览表，ASCII码对照表

| 事件       | 说明                                              |
| :--------- | :------------------------------------------------ |
| onkeydown  | 某个键盘按键被按下时触发                          |
| onkeyup    | 某个键盘按键被松开时触发                          |
| onkeypress | 某个按键被按下时触发，不监听功能键，如ctrl，shift |

# 12、JS中鼠标事件的各个坐标？



| 属性    | 说明                                                         | 兼容性            |
| :------ | :----------------------------------------------------------- | :---------------- |
| offsetX | 以当前的目标元素左上角为原点，定位x轴坐标                    | 除Mozilla外都兼容 |
| offsetY | 以当前的目标元素左上角为原点，定位y轴坐标                    | 除Mozilla外都兼容 |
| clientX | 以浏览器可视窗口左上角为原点，定位x轴坐标                    | 都兼容            |
| clientY | 以浏览器可视窗口左上角为原点，定位y轴坐标                    | 都兼容            |
| pageX   | 以doument对象左上角为原点，定位x轴坐标                       | 除IE外都兼容      |
| pageY   | 以doument对象左上角为原点，定位y轴坐标                       | 除IE外都兼容      |
| screenX | 以计算机屏幕左上顶角为原点，定位x轴坐标(多屏幕会影响)        | 全兼容            |
| screenY | 以计算机屏幕左上顶角为原点，定位y轴坐标                      | 全兼容            |
| layerX  | 最近的绝对定位的父元素（如果没有，则为 document 对象）左上顶角为元素，定位 x 轴坐标 | Mozilla 和 Safari |
| layerY  | 最近的绝对定位的父元素（如果没有，则为 document 对象）左上顶角为元素，定位 y 轴坐标 | Mozilla 和 Safari |

# 13、JS中元素视图的各个尺寸？

| 属性         | 说明                                                         |
| :----------- | :----------------------------------------------------------- |
| offsetLeft   | 获取当前元素到定位父节点的left方向的距离                     |
| offsetTop    | 获取当前元素到定位父节点的top方向的距离                      |
| offsetWidth  | 获取当前元素 width + 左右padding + 左右border-width          |
| offsetHeight | 获取当前元素 height + 上下padding + 上下border-width         |
| clientWidth  | 获取当前元素 width + 左右padding                             |
| clientHeight | 获取当前元素 height + 上下padding                            |
| scrollWidth  | 当前元素内容真实的宽度，内容不超出盒子宽度时为盒子的clientWidth |
| scrollHeight | 当前元素内容真实的高度，内容不超出盒子高度时为盒子的clientHeight |

# 14、Window视图的各个尺寸？

| innerWidth  | innerWidth 浏览器窗口可视区宽度（不包括浏览器控制台、菜单栏、工具栏） |
| ----------- | ------------------------------------------------------------ |
| innerHeight | innerWidth 浏览器窗口可视区高度（不包括浏览器控制台、菜单栏、工具栏） |

# 15、Document文档视图的各个尺寸？

| 属性                                  | 说明                                                         |
| :------------------------------------ | :----------------------------------------------------------- |
| document.documentElement.clientWidth  | 浏览器窗口可视区宽度（不包括浏览器控制台、菜单栏、工具栏、滚动条） |
| document.documentElement.clientHeight | 浏览器窗口可视区高度（不包括浏览器控制台、菜单栏、工具栏、滚动条） |
| document.documentElement.offsetHeight | 获取整个文档的高度（包含body的margin）                       |
| document.body.offsetHeight            | 获取整个文档的高度（不包含body的margin）                     |
| document.documentElement.scrollTop    | 返回文档的滚动top方向的距离（当窗口发生滚动时值改变）        |
| document.documentElement.scrollLeft   | 返回文档的滚动left方向的距离（当窗口发生滚动时值改变）       |

# 16、target 和 currentTarget的区别？

- `e.target`：**触发**事件的元素
- `e.currentTarget`：**绑定**事件的元素

# 17、ajax上传文件监听进度

```js
//【上传进度调用方法实现】
xhr.upload.onprogress = progressFunction
```

# 18、href和src的区别



1. 当浏览器遇到href会并行下载资源并且不会停止对当前文档的处理。(同时也是为什么建议使用 link 方式加载 CSS，而不是使用 @import 方式)
2. 当浏览器解析到src ，会暂停其他资源的下载和处理，直到将该资源加载或执行完毕。(这也是script标签为什么放在底部而不是头部的原因)



# 19、跨域处理的九种方法

**不重要: 开发模拟同源环境进行跨域: xmapp, node中间件代理**

## 1. JSONP

- 在html中的 script, img, link, iframe, audio ...  这些是不存在跨域请求限制的 (**JSONP就是用的这个特点**)
- JSONP只能处理资源处理请求(**GET请求**)
- 安全性低,因为别人可以再给你传递一个木马脚本(所以就会有下一个新的方法)

{% asset_img  jsonp原理.jpg %}

**前台**

```html
<body>
  <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.6.1/jquery.min.js"></script>
  <script src="./1.jsonp.js"></script>
</body>

1.jsonp.js
<script>
    const cb = function (data) {
      console.log('我是cb函数, 我接受到的参数是: ' + data);
      console.log(data);  
    }
    $.ajax({
      type: 'get',
      url: 'http://127.0.0.1:8001/list',
      jsonp: 'callback',
      jsonpCallback: 'cb',
      dataType: 'jsonp', // 执行的就是 jsonp 的请求,
      success: res => {
        console.log(res);
      },
      error: err => {
        console.log(err);
      }
    })
</script>
```

**后台**

```js
let express = require('express');
let app = express();

app.get('/list', (req, res) => {
  let {callback = Function.prototype} = req.query;
  console.log(callback);
  let data = {
    code: 0,
    message: '刘涛'
  };
  console.log(`${callback}(${JSON.stringify(data)})`);
  res.send(`${callback}(${JSON.stringify(data)})`);
})

app.listen(8001, _ => {
  console.log('8001端口服务器启动成功');
})
```

## 2. CORS跨域资源共享

1. 客户端进行发送请求(axios/fetch)
2. 服务器设置相关的头信息(Access-Control-Allow-Orign: *)

**前台**

```html
<body>
  <script src="../node_modules/axios/dist/axios.js"></script>
  <script>
    axios.get('http://127.0.0.1:8001/user').then(res => {
      console.log(res);
    })
  </script>
</body>
```

**后台**(局限性,cookie问题)

```js
app.all('*', function(req, response, next) {
  //设置允许跨域的域名，*代表允许任意域名跨域(不允许携带cookie), 也可以直接写指定的地址(可以携带cookie)
  response.header("Access-Control-Allow-Origin", "*");
  //允许的header类型
  // response.header("Access-Control-Allow-Headers", "X-Requested-With");
  //跨域允许的请求方式
  // response.header("Access-Control-Allow-Methods","PUT,POST,GET,DELETE,OPTIONS");
  //设置响应头信息
  // response.header("X-Powered-By",' 3.2.1')
  // response.header("Content-Type", "application/json;charset=utf-8");
  next();
});


app.get('/user', (req, res) => {
  let userList = [
    {
      id: 1,
      name: 'lt',
      age: 22
    },
    {
      id: 2,
      name: 'wtt',
      age: 22
    }
  ];
  res.send({
    code: 0,
    data: userList
  })
})
```

## 3. http proxy

=> 配置 webpack  webpack-dev-server

**重点: devServer (webpack.config.js 的中间代理方式) **

**前台**

**index.js**

```js
axios.get('/user').then(res => {
  console.log(res);
})
```

**webpack.config.js**

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
  mode: 'production',
  entry: './src/index.js',
  output: {
    filename: 'bundle.min.js',
    path: path.resolve(__dirname, 'build')
  },
  module: {
    rules: [
      
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html',
      filename: 'index.html'
    })
  ],
  devServer: { // 重点
    port: 3000,
    progress: true, // 显示打包的进度
    contentBase: './build', // 指定哪个目录下是我要访问的静态资源目录
    proxy: {
      '/': {
        target: 'http://127.0.0.1:8001',
        changeOrign: true
      }
    }
  }
};
```

**后台**

```js
app.get('/user', (req, res) => {
  let userList = [
    {
      id: 1,
      name: 'lt',
      age: 22
    },
    {
      id: 2,
      name: 'wtt',
      age: 22
    }
  ];
  res.send({
    code: 0,
    data: userList
  })
})
```

## 4. nginx反向代理

**不需要前端干啥**

## 5. postMessage

**使用iframe添加别的页面,然后通过window的message监听还有postmessage发送消息来实现数据的传输**

server1.js   &&    server2.js

```js
let express = require('express');
let app = express();
app.use(express.static('./'));
app.listen(1001, _ => {
  console.log('1001端口服务器启动成功');
})

let express = require('express');
let app = express();
app.use(express.static('./'));
app.listen(1002, _ => {
  console.log('1002端口服务器启动成功');
})

```

a.html

```html
a: 
<iframe src="http://127.0.0.1:1002/message/b.html" style="display: none;" id="iframe" frameborder="0"></iframe>
<script>
    iframe.onload = function () {
        console.log(iframe.contentWindow);
        // a 向 B 传递信息
        iframe.contentWindow.postMessage('刘涛nb', 'http://127.0.0.1:1002');
    };

    // 反监听b传递的信息
    window.onmessage = function (ev) {
        console.log(ev);
    }

</script>

```

b.html

```html
<script>
    // 监听a发送过来的信息
    window.onmessage = function (e) {
        console.log(e);
        e.source.postMessage({name: '刘涛', age: 22}, e.origin);
    }
</script>
```

## 6. WebSocket

**重点: socket.io.js实现实时通讯**

**前台**

```html
<script src="./socket.io.js"></script>
<script>
	let socket = io('http://127.0.0.1:3001/');
    socket.on('connect', function() {
        socket.on('message', function(msg) {
            console.log('data from server: ' + msg);
        });
        // 监听服务端关闭
       	socket.on('disconnect', function() {
            console.log('server socket has closed !');
        })
    });
    // 发送消息给服务器端
    socket.send('liutao');
</script>
```

**后台**

```js
// 监听socket连接: server是服务器创建的服务
socket.listen(server).on('connection', function(client) {
    // 接受信息
    client.on('message', function(msg) {
        client.send(msg + '@@@');
    });
    // 断开处理
    client.on('disconnect', function() {
        console.log('client socket has closed !');
    });
});
```

## 7. document.domain + iframe

**只能实现: 同一个主域, 不同子域之间的操作**

```
例如:   v.qq.vom  sports.qq.com  这是可以的,主域名是qq
```

父页面: http://www.liutao.cn/B.html

A  http://www.liutao.cn/B.html

B  http://www.study.liutao.cn/B.html

```html
A中做的事情
<iframe src="http://study.liutao.cn/B.html"></iframe?>
<script>
	document.domain = 'liutao.cn';
    var user = 'liutao';
</script>


B中做的事情
<script>
	document.domain = 'liutao.cn';
    // parent作用是: 子页面(B)访问父页面(A)中的user值
    alert(window.parent.user);
</script>
```

## 8. window.name + iframe

**需要三个页面!!! 其中第三个什么都不写**

**页面A**

```html
<iframe src="http://127.0.0.1:1002/windowname/b.html" id="iframe" style="display: none;" frameborder="0"></iframe>
<script>
    let count = 0;
    iframe.onload = function() {
        if(count === 0) {
            // 需要先把地址指向到同源中,才可以
            // 因为本身是存在跨域的,但是在
            iframe.src = 'http://127.0.0.1:1001/windowname/proxy.html';
            count++;
            return;
        }
        console.log(iframe.contentWindow.name);
    }
</script>
```

**页面B**

```html
<script>
    // 服务器端需要返回给a的信息,都是在window.name中存储的
    window.name = 'liutao';
  </script>
```

**页面C: 什么都不写**

## 9. location.hash + iframe

```
做法: 
1: A和B同源, A和C不同源
2: A中iframe包含B, B中iframe包含C
3: A中有一个方法func用来处理数据,并且监听iframe.onload,iframe.onload = function => 重新设置iframe.src, 设置成B的url但是要新添加hash参数url#msg=123123sads
4: 在B中监听window.onhashchange的改变,然后再次重新设置iframe.src为C的url并且添加hash参数(A中传递过来的hash值)
5: C中监听window.onhashchange的改变,然后再回调中使用window.parent(B).parent(A).func(location.hash)调用A中方法操作A
```

**分析: 不推荐,因为这是使用location.hash来传递值,并且在get请求中 url 有最大长度限制(一般4kb), 这是不足以传递很多数据的, 假如传递一个很复杂的数据就完蛋了,所以不推荐**

# 20、var变量提升的底层原理

**原理**：JS引擎的工作方式是①先解析代码，获取所有被声明的变量；②然后在运行。也就是专业来说是分为预处理和执行两个阶段。

**变量提升的定义**：所有变量的声明语句都会被提升到代码头部，这就是变量提升。

# 21、JS计算网页渲染执行时间

首先在网页的顶部放入一段代码用标记网页开始渲染的时间点

```html
<head>
    <script type="text/javascript">
        var start = new Date().getTime(); 
    </script> 
</head>
```

然后在页面中再加入一个类似的代码，能够获取页面渲染完毕的时间点，然后计算两者之间的时间差即可。

```html
<script>
    window.onload = function() {
        var time = '加载本页耗时: ' + (new Date().getTime() - start) + '毫秒';
    }
</script>
```

# 22、如何获取元素的父节点和兄弟结点?

## 获取父节点

```js
// 1. parentNode获取父节点
// 获取的是当前元素的直接父元素。
var p = document.getElementById("test").parentNode;
 
// 2. parentElement获取父节点
// parentElement和parentNode一样，只是parentElement是ie的标准。
var p1 = document.getElementById("test").parentElement;

// 3. offsetParent获取所有父节点
var p2 = document.getElementById("test").offsetParent;
```

## 获取兄弟节点

```js
// 1. 通过获取父亲节点再获取子节点来获取兄弟节点
var brother1 = document.getElementById("test").parentNode.children[1];

// 2. 获取上一个兄弟节点
// 在获取前一个兄弟节点的时候可以使用previousSibling和previousElementSibling。
// 他们的区别是previousSibling会匹配字符，包括换行和空格，而不是节点。
// previousElementSibling则直接匹配节点。
var brother2 = document.getElementById("test").previousElementSibling;
var brother3 = document.getElementById("test").previousSibling;

// 3. 获取下一个兄弟节点
var brother4 = document.getElementById("test").nextElementSibling;
var brother5 = document.getElementById("test").nextSibling;
```

# 23、原生JS动态向一个div中插入1000个div标签,如何实现?

```js
此题主要考性能!

1)  可以用JS中的createElement创建div，
    每当创建一个就把它添加到div中, 
    但会造成引发回流的次数太多;

2)  使用字符串拼接的方式，
    把1000个div都拼接完成后，
    统一的添加到页面中, 
    但会对div原有的元素标签产生影响：
    原来标签绑定的事件都消失了

3)  综合1和2可以使用文档碎片方式来处理。

追问:如果是创建1000万个呢?

可采用的方案: 数据分批异步加载
1)  首先把前两屏幕的数据量
    (例如：300条)先获取到，
    然后使用字符串拼接或者文档碎片
    的方式绑定到页面中; 

2)  当浏览器滚动到指定的区域的
    时候在加载300条...以此类推。
```

```js
## （1）创建新节点

createDocumentFragment() //创建一个DOM片段
createElement() //创建一个具体的元素
createTextNode() //创建一个文本节点

## （2）添加、移除、替换、插入

appendChild()
removeChild()
replaceChild()
insertBefore() //在已有的子节点前插入一个新的子节点

## （3）查找

getElementsByTagName() //通过标签名称
getElementsByName() //通过元素的Name属性的值(IE容错能力较强，会得到一个数组，其中包括id等于name值的)
getElementById() //通过元素Id，唯一性
document.querySelector() : 该方法总会返回唯一的一个元素，如果满足条件的元素有多个，那么它只会返回第一个
document.querySelectorAll(): 它会将符合条件的元素封装到一个数组中返回, 即使符合条件的元素只有一个, 他也会返回一个数据
```

# 24、开发中的性能优化

## 1. 雅虎35条性能优化黄金定律

```
https://www.cnblogs.com/ranyonsue/p/9558520.html
```

{% asset_img  性能优化.png %}

```
1）尽量减少HTTP请求次数
    合并js
    合并css
    图片sprite
2）延迟加载内容
    图片懒加载
    数据懒加载(点击查看更多)
    功能懒加载（曝光或者点击后加载html模块、js功能模块）
3）使用离线缓存
	把常用的变动又少的js、css、图片存储到localstorage，第二次访问的时候直接走本地缓存。在移动端使用广泛。
4）CSS、JS放置正确位置
    把css放在head中，保证页面看到的时候样式是对的。
    把js放到body里最后位置，防止加载js阻塞页面。
5）静态资源压缩
	图片、CSS、JS在发布前要压缩。
6）静态资源使用多个域名
	对于图片、CSS、JS，可使用几个域名，可以并发加载。
7）静态资源使用cdn存储
    用户与你网站服务器的接近程度会影响响应时间的长短。 可以把静态资源放到内容分发网络（Content Delivery Network，CDN）中加快访问速度。

8）预加载
	在某个功能还没展现时，在空闲时间预先加载相关图片或者js代码
9）DOM操作优化
	使用JavaScript访问DOM元素比较慢，因此为了获得更多的应该页面，应该做到：
        缓存已经访问过的有关元素
        线下更新完节点之后再将它们添加到文档树中
        避免使用JavaScript来修改页面布局
10）优化算法
	在js处理中优化查找、排序算法。尽量少使用嵌套循环。
	使用事件代理
	减少DNS查找次数
	根据域名划分页面内容
	跳转的正确使用
	可缓存的AJAX
	减少DOM元素数量
	使iframe的数量最小
	不要出现404错误
	为文件头指定Expires或Cache-Control
	Gzip压缩文件内容
	简单请求使用GET方式
	避免使用CSS表达式（Expression）
	使用外部JavaScript和CSS
	用link代替@import
	避免使用滤镜
	剔除重复脚本
	减小Cookie体积
	对于静态资源使用无coockie域名
	不要在HTML中缩放图像
	favicon.ico要小而且可缓存
	保持单个内容小于25K
```



## 2. 代码优化

```
a. 项目中的JS/CSS文件最好一个页面只用一个, 
      需要把JS/CSS进行合并压缩, 
      并且减少页面中的垃圾冗余代码。
      项目的资源文件在服务器上最好
      做一下GZIP压缩。
   
  b. 解除文件缓存; 
     我们修改代码并上传, 
     如果之前页面访问过该网站,
     很有可能不能立即见效; 
     我们在引入CSS/JS文件的时候, 
     在文件名的后面加上版本号(加时间戳), 
     比如:     
     <script src='itlike.com.js?_=202001...'></script>; 
     当我们上传新的文件后
     把时间戳改一下就可以清除缓存了。

  c. 移动端尽量少用图片:
      icon能用svg画的不用图片;
     静态资源图:做布局的时候就能确定下来的图片,
      比如:     
      1)css sprite图片合并(针对于小图片)
      2)做图片延迟加载
         (针对于大图片 头部的长条图片、背景大图...),
         开始给一张默认的小的图片
         (最好维持在10kb以内)
      3)base64
        (存在问题: 页面的代码太臃肿了,以后维护不好操作);  
        如果项目中由于图片太大实在解决不了, 
        改成base64就解决了
   
  d. 动态数据图:
     通过ajax从后台读取回来的图片 , 图片懒加载;

  e. 音视频文件的优化: 
     加载页面的时候,尽量不要加载音视频文件，
     当页面中的其他资源加载完成后,
     再开始加载音视频文件; 
     目前移动端经常给音视频做的优化是：
     走直播流文件(音频后缀名是m3u8格式);

  f. 减少页面资源请求的次数:
     如果当前只是一个宣传页,
     或者是一个简单的页面, 
     使用的css和js可以采用内嵌式开发;

  g. ajax数据请求分批请求, 
     例如：一次要请求10000条数据的话,
     我们每一次只请求100条,第一屏幕肯定能看全了,
     当页面滚动到对应的其它屏幕的时候,
     在加载下一个100条...

  h. 做数据的二次缓存,  
     能用CSS3做动画的绝对不用JS,
     能使用transform尽量使用,
     能用animation的进行不用transition...
     尽量减少同步操作,多用异步操作;
     能使用原生JS自己编写的,
     绝对不用插件或者框架;  
```

# 25、网页兼容性

```
https://juejin.cn/post/6844903493161975822
```

**普及**：浏览器的兼容性问题，往往是个别浏览器（没错，就是那个与众不同的浏览器）对于一些标准的定义不一致导致的。俗话说：没有IE就没有伤害。

### Normalize.css

不同浏览器的默认样式存在差异，可以使用 `Normalize.css` 抹平这些差异。当然，你也可以定制属于自己业务的 `reset.css`

```html
<link href="https://cdn.bootcss.com/normalize/7.0.0/normalize.min.css" rel="stylesheet">
```

简单粗暴法

```css
* { 
    margin: 0; padding: 0; 
}
```

### html5shiv.js

解决 `ie9` 以下浏览器对 `html5` 新增标签不识别的问题。

```xml
<!--[if lt IE 9]>
  <script type="text/javascript" src="https://cdn.bootcss.com/html5shiv/3.7.3/html5shiv.min.js"></script>
<![endif]-->
```

### respond.js

解决 `ie9` 以下浏览器不支持 `CSS3 Media Query` 的问题。

```xml
<script src="https://cdn.bootcss.com/respond.js/1.4.2/respond.min.js"></script>
```

### picturefill.js

解决 `IE 9 10 11` 等浏览器不支持 `` 标签的问题

```xml
<script src="https://cdn.bootcss.com/picturefill/3.0.3/picturefill.min.js"></script>
```

### IE 条件注释

IE 的条件注释仅仅针对IE浏览器，对其他浏览器无效

```
操作符       含义
lt			小于
gt			大于
lte			小于等于
gte			不小于
!			不等于
```

### IE 属性过滤器（较为常用的hack方法）

针对不同的 IE 浏览器，可以使用不同的字符来对特定的版本的 IE 浏览器进行样式控制

{% asset_img  IE属性过滤器.jpg %}

{% asset_img  IE属性过滤器2.jpg %}

### 浏览器 CSS 兼容前缀

```css
-o-transform:rotate(7deg); // Opera

-ms-transform:rotate(7deg); // IE

-moz-transform:rotate(7deg); // Firefox

-webkit-transform:rotate(7deg); // Chrome

transform:rotate(7deg); // 统一标识语句
```

### a 标签的几种 CSS 状态的顺序

很多新人在写 `a` 标签的样式，会疑惑为什么写的样式没有效果，或者点击超链接后，`hover`、`active` 样式没有效果，其实只是写的样式被覆盖了。

正确的a标签顺序应该是：==love hate==

- link:平常的状态

- visited:被访问过之后

- hover:鼠标放到链接上的时候

- active:链接被按下的时候

### 完美解决 `Placeholder`

```html
<input type="text" value="Name *" onFocus="this.value = '';" onBlur="if (this.value == '') {this.value = 'Name *';}">
```

### 清除浮动 最佳实践

```css
.fl { float: left; }
.fr { float: right; }
.clearfix:after { display: block; clear: both; content: ""; visibility: hidden; height: 0; }
.clearfix { zoom: 1; }  // zoom: 元素缩放比例, 默认normal
```

### BFC 解决边距重叠问题

当相邻元素都设置了 `margin` 边距时，`margin` 将取最大值，舍弃小值。为了不让边距重叠，可以给子元素加一个父元素，并设置该父元素为 BFC：`overflow: hidden;`

```xml
<div class="box" id="box">
  <p>Lorem ipsum dolor sit.</p>

  <div style="overflow: hidden;">
    <p>Lorem ipsum dolor sit.</p>
  </div>

  <p>Lorem ipsum dolor sit.</p>
</div>
```

### IE6 双倍边距的问题

设置 `ie6` 中设置浮动，同时又设置 `margin`，会出现双倍边距的问题

```arduino
display: inline;
```

### 解决 `IE9` 以下浏览器不能使用 `opacity`

```css
opacity: 0.5;
filter: alpha(opacity = 50);
filter: progid:DXImageTransform.Microsoft.Alpha(style = 0, opacity = 50);
```

### 解决 IE6 不支持 `fixed` 绝对定位以及IE6下被绝对定位的元素在滚动的时候会闪动的问题

```css
/* IE6 hack */
*html, *html body {
  background-image: url(about:blank);
  background-attachment: fixed;
}
*html #menu {
  position: absolute;
  top: expression(((e=document.documentElement.scrollTop) ? e : document.body.scrollTop) + 100 + 'px');
}
```

### IE6 背景闪烁的问题

问题：链接、按钮用 `CSSsprites` 作为背景，在 `ie6` 下会有背景图闪烁的现象。原因是 `IE6` 没有将背景图缓存，每次触发 `hover` 的时候都会重新加载

解决：可以用 `JavaScript` 设置 `ie6` 缓存这些图片：

```javascript
document.execCommand("BackgroundImageCache", false, true);
```

### 解决在 IE6 下，列表与日期错位的问题

日期`<span>` 标签放在标题 `<a>` 标签之前即可

### 解决 `IE6` 不支持 `min-height` 属性的问题

```css
min-height: 350px;
_height: 350px;
```

### 让 `IE7 IE8` 支持 `CSS3 background-size`属性

由于 background-size 是 CSS3 新增的属性，所以 IE 低版本自然就不支持了，但是老外写了一个 htc 文件，名叫 [background-size polyfill](https://link.juejin.cn/?target=标签)，使用该文件能够让 IE7、IE8 支持 background-size 属性。其原理是创建一个 img 元素插入到容器中，并重新计算宽度、高度、left、top 等值，模拟 background-size 的效果。

```css
html {
  height: 100%;
}
body {
  height: 100%;
  margin: 0;
  padding: 0;
  background-image: url('img/37.png');
  background-repeat: no-repeat;
  background-size: cover;
  -ms-behavior: url('css/backgroundsize.min.htc');
  behavior: url('css/backgroundsize.min.htc');
}
```

### IE6-7 `line-height` 失效的问题

问题：在`ie` 中 `img` 与文字放一起时，`line-height` 不起作用

解决：都设置成 `float`

### `width:100%`

`width:100%` 这个东西在 `ie` 里用很方便，会向上逐层搜索 `width` 值，忽视浮动层的影响.

`Firefox` 下搜索至浮动层结束，如此，只能给中间的所有浮动层加 `width:100%`才行，累啊。

```
opera` 这点倒学乖了，跟了 `ie
```

### `cursor:hand`

显示手型 `cursor: hand`，ie6/7/8、opera 都支持，但是safari 、 ff 不支持

```css
cursor: pointer;
```

### td 自动换行的问题

问题：`table` 宽度固定，`td` 自动换行

解决：设置 `Table` 为 `table-layout: fixed`，`td` 为 `word-wrap: break-word`

### 让层显示在 `FLASH` 之上

想让层的内容显示在 flash 上，把 FLASH 设置透明即可

```ini
1、<param name=" wmode " value="transparent" />
2、<param name="wmode" value="opaque"/>
```

### 键盘事件 `keyCode` 兼容性写法

```javascript
var inp = document.getElementById('inp')
var result = document.getElementById('result')

function getKeyCode(e) {
  e = e ? e : (window.event ? window.event : "")
  return e.keyCode ? e.keyCode : e.which
}

inp.onkeypress = function(e) {
  result.innerHTML = getKeyCode(e)
}
```

### 求窗口大小的兼容写法

```ini
// 浏览器窗口可视区域大小（不包括工具栏和滚动条等边线）
// 1600 * 525
var client_w = document.documentElement.clientWidth || document.body.clientWidth;
var client_h = document.documentElement.clientHeight || document.body.clientHeight;

// 网页内容实际宽高（包括工具栏和滚动条等边线）
// 1600 * 8
var scroll_w = document.documentElement.scrollWidth || document.body.scrollWidth;
var scroll_h = document.documentElement.scrollHeight || document.body.scrollHeight;

// 网页内容实际宽高 (不包括工具栏和滚动条等边线）
// 1600 * 8
var offset_w = document.documentElement.offsetWidth || document.body.offsetWidth;
var offset_h = document.documentElement.offsetHeight || document.body.offsetHeight;

// 滚动的高度
var scroll_Top = document.documentElement.scrollTop||document.body.scrollTop;
```

### DOM 事件处理程序的兼容写法（能力检测）

```javascript
var eventshiv = {
    // event兼容
    getEvent: function(event) {
        return event ? event : window.event;
    },

    // type兼容
    getType: function(event) {
        return event.type;
    },

    // target兼容
    getTarget: function(event) {
        return event.target ? event.target : event.srcelem;
    },

    // 添加事件句柄
    addHandler: function(elem, type, listener) {
        if (elem.addEventListener) {
            elem.addEventListener(type, listener, false);
        } else if (elem.attachEvent) {
            elem.attachEvent('on' + type, listener);
        } else {
            // 在这里由于.与'on'字符串不能链接，只能用 []
            elem['on' + type] = listener;
        }
    },

    // 移除事件句柄
    removeHandler: function(elem, type, listener) {
        if (elem.removeEventListener) {
            elem.removeEventListener(type, listener, false);
        } else if (elem.detachEvent) {
            elem.detachEvent('on' + type, listener);
        } else {
            elem['on' + type] = null;
        }
    },

    // 添加事件代理
    addAgent: function (elem, type, agent, listener) {
        elem.addEventListener(type, function (e) {
            if (e.target.matches(agent)) {
                listener.call(e.target, e); // this 指向 e.target
            }
        });
    },

    // 取消默认行为
    preventDefault: function(event) {
        if (event.preventDefault) {
            event.preventDefault();
        } else {
            event.returnValue = false;
        }
    },

    // 阻止事件冒泡
    stopPropagation: function(event) {
        if (event.stopPropagation) {
            event.stopPropagation();
        } else {
            event.cancelBubble = true;
        }
    }
};
```

# 26、搜索数字

```js
function _search(string) {
    // 补全代码
    return string.search(/\d/g) ? true: false;
}
```



# 27、阻止事件冒泡和阻止默认事件

```html
请补全JavaScript函数，要求在点击li元素的时候不触发ul的事件。
注意：需要自行获取li元素。

<ul>
	<li>nowcoder</li>
</ul>

<script type="text/javascript">
    // 补全代码
    let li = document.getElementsByTagName('li')[0];
    li.onclick = function(e){
        e.cancelBubble=true;
    }
</script>

<script type="text/javascript">
    // 补全代码
    let li = document.getElementsByTagName('li')[0];
    li.onclick = function(e){
        如果是IE则前者，否则后者
        window.event? window.event.cancelBubble = true : e.stopPropagation();
    }
</script>




取消默认事件
w3c的方法是e.preventDefault()，IE则是使用e.returnValue = false;
preventDefault它是事件对象(Event)的一个方法，作用是取消一个目标元素的默认行为。既然是说默认行为，当然是元素必须有默认行为才能被取消，如果元素本身就没有默认行为，调用当然就无效了。什么元素有默认行为呢？如链接<a>，提交按钮<input type=”submit”>等。当Event 对象的 cancelable为false时，表示没有默认行为，这时即使有默认行为，调用preventDefault也是不会起作用的。

    <html>
    <head>
        <meta charset=utf-8>
    </head>
    <body>
        <form>
            <label>牛客会陪伴大家</label>
            <input id="checkbox" type="checkbox" checked />
        </form>
    </body>
    <script type="text/javascript">
        // 补全代码
        let c = document.getElementById('checkbox');
        c.onclick = (e) => {
            // e.returnValue = false;
            // e.preventDefault();
            return false;
        }
    </script>
</html>
```



# 28、返回url地址

```js
location.host
```

# 29、为什么利用多个域名来提供网站资源会更有效?

```
1.CDN缓存更方便

2.突破浏览器并发限制
	（一般每个域名建立的链接不超过6个）

3.Cookieless，节省带宽，
	尤其是上行带宽一般比下行要慢;

4.对于UGC的内容和主站隔离，
    防止不必要的安全问题
    (上传js窃取主站cookie之类的)。
    正是这个原因要求用户内容的域名
    必须不是自己主站的子域名，
    而是一个完全独立的第三方域名。

5.数据做了划分，
    甚至切到了不同的物理集群，
    通过子域名来分流比较省事。

补充: 关于Cookie的问题，
    带宽是次要的，安全隔离才是主要的。
    关于多域名，也不是越多越好，
    虽然服务器端可以做泛解释，
    浏览器做dns解释也是耗时间的，
    而且太多域名，如果要走https的话，
    还有要多买证书和部署的问题。
```

# 30、请说出三种减少页面加载时间的方法? 

```
1.优化图片;
精灵图片, 字体图标
SVG, GIF, WEBP
(可用在一些对颜色要求不高的地方)

2. 优化CSS
（压缩合并css，
如margin-top,margin-left...)

3. 网址后加斜杠
（如www.itlike.com/目录，
会判断这个“目录是什么文件类型，
或者是目录。）

4. 标签标明高度和宽度
（如果浏览器没有找到这两个参数，
它需要一边下载图片一边计算大小，
如果图片很多，
浏览器需要不断地调整页面。
这不但影响速度，也影响浏览体验。
当浏览器知道了高度和宽度参数后，
即使图片暂时无法显示，
页面上也会腾出图片的空位，
然后继续加载后面的内容。
从而加载时间快了，
浏览体验也更好了。）

5.减少http请求
（合并文件，合并图片）。
```

# 31、用纯JS实现，点击一个列表时，输出对应的索引？

```js
方式一：
for(let i = 0, 
    len = lis.length; 
    i < len; i++){
    lis[i].addEventListener('click',
      function () {        
        console.log(i);
      }, false);
}

方式二：
for(var i = 0, 
    len = lis.length; 
    i < len; i++) {
    (function (i) {
        lis[i].addEventListener
        ('click', function () {            
             console.log(i);
        }, false);
    })(i)
}

方式三：
let ul = document.querySelector('ul');
let lis = document.querySelectorAll('ul li');
ul.addEventListener('click', 
function (e) {    
  let target = e.target;    
  if(target.nodeName.toLowerCase() === 'li') {        
      console.log([].indexOf.call(lis, target));
    }
}, false);
```

# 32、点击按钮发出ajax请求之后, 如何防止用户在此请求方式之前再次点击?

```js
// 点击提交按钮的时候，
// 把这个提交这个处理函数给解绑掉，
// 请求完成的时候在绑定回来
function clickHandler(){
  $(this).unbind('click', clickHandler);
    $.ajax({        
       url : 'url',        
       dataType : 'json',
       type : 'post',
       success : function (data) {
         if (data.success) { 
          //提交成功做跳转处理
         } else { 
          //处理失败，重新绑定点击事件
          $(self).click(clickHandler);
         }
        }
  });
}
                
$('#itlike').click(clickHandler);
                
// 可以点击后让按钮不可用,
// 如果提交失败可以再次设置为可用
// 1.让按钮不可用
$("#itlike").attr("disabled","disabled");
$.ajax({
    url : 'url',
    dataType : 'json',    
    type : 'post',    
    success : function (data) {
       if (data.success) {
       // 提交成功做跳转处理
       } else {            
       // 处理失败，重新绑定点击事件
       // 2. 让按钮可用
       $("#itlike").removeAttr("disabled");
      }
    }
});
```

# 33、请说明ECMAScript, JavaScript, Jscript之间的关系?

```
ECMAScript提供脚本语言必须遵守的规则、
细节和准则，是脚本语言的规范。
比如：ES5，ES6就是具体的一js版本。

JavaScript是ECMAScript的一个分支版本, 
JavaScript 实现了多数 ECMA-262 中
描述的 ECMAScript 规范，但存在少数差异。 

JScript是微软公司对ECMA-262语言规范的
一种实现,除了少数例外(这是为了保持向后兼容 ),
微软公司宣称JScript完全实现了ECMA标准.

关系:
JavaScript和JScript都是ECMAScript的版本分支, 
二者在语法上没有多大的区别; 
只不过一个是NetScape公司的, 一个是微软的; 
IE系列默认是JScript, 其它的则反之用JavaScript。
```

# 34、什么是hash? 以及hashchange事件?

```
先了解下什么是hash:hash即URL中"#"字符后面的部分：
a) 使用浏览器访问网页时，
如果网页URL中带有hash，
页面就会定位到id（或name）
与hash值一样的元素的位置；
b) hash还有另一个特点，
它的改变不会导致页面重新加载；
c) hash值浏览器是不会随请求发送到服务器端的；
d) 通过window.location.hash属性获取和设置hash值。

window.location.hash值的变化会直接
反应到浏览器地址栏（#后面的部分会发生变化），
同时，浏览器地址栏hash值的变化也会触发
window.location.hash值的变化，
从而触发onhashchange事件。

再来了解下什么是hashchange事件:
a) 当URL的片段标识符更改时，
将触发hashchange事件
（跟在#符号后面的URL部分，包括#符号）
b) hashchange事件触发时，
事件对象会有hash改变前的URL
（oldURL）和hash改变后的URL
（newURL）两个属性。
```

# 35、说几条JS的基本规范?

```
1) 不要在同一行声明多个变量;
2) 请使用===/!==来比较true/false或者数值;
3) 使用对象字面量替代new Object这种形式;
4) 减少使用全局函数, 全局变量;
5) switch语句必须带有default分支;
6) if语句必须使用大括号;
7) for-in循环中的变量;应该使用var关键字明确限定作用域;从而避免作用域全局污染。
```

# 36、从前端角度出发, 谈谈做好网站seo需要考虑什么?

```
1) 语义化html标签;
2) 合理的title, description, keywords;
3) 重要的html代码放前面;
4) 少用iframe, 搜索引擎不会抓取iframe中的内容
5) 图片加上alt
```

# 37、display: none;与visibility: hidden;的区别?

```
相同点：它们都能让元素不可见

不同点：
display:none;会让元素完全从渲染树中消失，
渲染的时候不占据任何空间；

visibility: hidden;不会让元素从渲染树消失，
渲染师元素继续占据空间，只是内容不可见;

display: none;是非继承属性，
子孙节点消失由于元素从渲染树消失造成，
通过修改子孙节点属性无法显示；

visibility: hidden;是继承属性，
子孙节点消失由于继承了hidden，
通过设置visibility: visible;可以让子孙节点显示;

修改常规流中元素的display通常会造成文档重排。
修改visibility属性只会造成本元素的重绘。

读屏器不会读取display: none;元素内容；
会读取visibility: hidden;元素内容
```

# 37、手写Promise

```js
const PENDING = '待定';
const FULFILLED = '成功';
const REJECTED = '拒绝';

class MyPromise {

  constructor (executor) {
    try{
      this.status = PENDING;
      this.value = undefined;
      this.reason = undefined;
      this.successCallback = [];
      this.failCallback = [];
      executor(this.resolve, this.reject);
    } catch(e) {
      this.reject(e);
    }
    
  }


  resolve = value => {
    if(this.status !== PENDING) return;
    this.status = FULFILLED;
    this.value = value;
    // this.successCallback && this.successCallback(this.value);
    while(this.successCallback.length) {
      this.successCallback.shift()();
    }
  }

  reject = reason => {
    if(this.status !== PENDING) return;
    this.status = REJECTED;
    this.reason = reason;
    // this.failCallback && this.failCallback(this.value);
    while(this.failCallback.length) {
      this.failCallback.shift()();
    }
  }

  then (successCallback, failCallback) {
    successCallback = successCallback ? successCallback : value => value;
    failCallback = failCallback ? failCallback : reason => reason;
    let promise2 = new MyPromise((resolve, reject) => {
      if (this.status === FULFILLED) {
        // 这里为延迟是为了把这个代码编程异步代码
        setTimeout(() => {
          try {
            let x = successCallback(this.value);
            // 判断 x 的值是普通值还是promise对象
            // 如果是普通值,直接resolve
            // 如果是promise对象 查看promise对象的返回的结果
            // 再根据promise 对象返回结果  决定调用resolve 还是调用reject
            resolvePromise(promise2, x, resolve, reject);
            // resolve(x);
          } catch (e) {
            reject(e);
          }
        }, 0);
      } else if (this.status === REJECTED) {
        // 这里为延迟是为了把这个代码编程异步代码
        setTimeout(() => {
          try {
            let x = failCallback(this.reason);
            // 判断 x 的值是普通值还是promise对象
            // 如果是普通值,直接resolve
            // 如果是promise对象 查看promise对象的返回的结果
            // 再根据promise 对象返回结果  决定调用resolve 还是调用reject
            resolvePromise(promise2, x, resolve, reject);
            // resolve(x);
          } catch (e) {
            reject(e);
          }
        }, 0);
      } else {
        this.successCallback.push(() => {
          // 这里为延迟是为了把这个代码编程异步代码
          setTimeout(() => {
            try {
              let x = successCallback(this.value);
              // 判断 x 的值是普通值还是promise对象
              // 如果是普通值,直接resolve
              // 如果是promise对象 查看promise对象的返回的结果
              // 再根据promise 对象返回结果  决定调用resolve 还是调用reject
              resolvePromise(promise2, x, resolve, reject);
              // resolve(x);
            } catch (e) {
              reject(e);
            }
          }, 0);
        });
        this.failCallback.push(() => {
          setTimeout(() => {
            try {
              let x = failCallback(this.value);
              // 判断 x 的值是普通值还是promise对象
              // 如果是普通值,直接resolve
              // 如果是promise对象 查看promise对象的返回的结果
              // 再根据promise 对象返回结果  决定调用resolve 还是调用reject
              resolvePromise(promise2, x, resolve, reject);
              // resolve(x);
            } catch (e) {
              reject(e);
            }
          }, 0);
        });
      }
    });
    return promise2;
  }

}

// 判断返回值的类型
function resolvePromise (promise2, x, resolve, reject) {
  // 判断是否是 自己 返回了 自己
  if(promise2 === x) {
    return reject(new Error('Chaining cycle detected for promise #<Promise>'));
  }
  if(x instanceof MyPromise) {
    // promise对象
    // x.then(value => resolve(value), reason => reject(reason));
    // 简化写法
    x.then(resolve, reject);
  } else {
    resolve(x);
  }
}

let mypromise = new MyPromise((resolve, reject) => {
  // resolve('成功');
  reject('失败');
  // throw new Error('执行器错误');
  // setTimeout(() => {
    // resolve('成功');
    // console.log(this);
    // reject('失败');
  // }, 2000);
});

mypromise.then().then().then(value => {
  console.log(value)
}, reason => {
  console.log(reason)
});




function temp () {
  return new MyPromise((resolve, reject) => {
    console.log('temp()');
    resolve('temp');
  })
}

```

# 38、SVG,  Canvas 以及区别

## SVG

### 什么是SVG?

[SVG](https://so.csdn.net/so/search?q=SVG&spm=1001.2101.3001.7020) (Scalable Vector Graphics) 可缩放矢量图，是一种基于XML语法的图像格式。其他图像格式都是基于像素处理的，SVG则是属于对图像的形状描述，所以它本质上是文本文件，体积相对较小，且放大或改变尺寸也不会失真。

### SVG优势

与其他图像格式相比(比如JPEG和GIF),使用SVG的优势在于：

```
1.SVG 图像可通过文本编辑器来创建和修改
2.SVG 图像可被搜索，索引，脚本化或压缩
3.SVG 是可伸缩的
4.SVG 图像可在任何的分辨率下被高质量地打印
5.SVG 可在图像质量不下降的情况下被放大
```

### SVG动画使用方式

1.创建动画，告诉动画标记哪个元素需要执行动画

2.创建元素，在元素中说明需要执行什么动画

### SVG动画属性

1.attributeType:CSS/XML规定的属性值的名称空间

2.attributeName:规定元素的哪个属性会产生动画效果

3.from/to:从哪到哪

4.dur:动画时长

5.fill:动画结束之后的状态，保持freeze结束状态/remove回复初始状态半径从50%到100%变化的圆。

## Canvas

### canvas简介

它是 `HTML5` 新增的，一个可以使用脚本(通常为`JavaScript`)在其中绘制图像的 `HTML` 元素。它可以用来制作照片集或者制作简单(也不是那么简单)的动画，甚至可以进行实时视频处理和渲染。

`Canvas`是由`HTML`代码配合高度和宽度属性而定义出的可绘制区域。`JavaScript`代码可以访问该区域，通过一套完整的绘图函数来动态生成图形。

### 例子:  画两个长方形

```html
<style type="text/css">
    canvas {
        border: 1px solid black;
    }
</style>
<canvas id="tutorial" width="300" height="300"></canvas>
<script type="text/javascript">
    function draw(){
        var canvas = document.getElementById('tutorial');
        if(!canvas.getContext) return;
        var ctx = canvas.getContext("2d");
        ctx.fillStyle = "rgb(200,0,0)";
      	//绘制矩形
        ctx.fillRect (10, 10, 55, 50);

        ctx.fillStyle = "rgba(0, 0, 200, 0.5)";
        ctx.fillRect (30, 30, 55, 50);
    }
    draw();
</script>
```

## SVG与Canvas两者之间的区别

```
1.SVG 是一种使用XML描述2D图形的语言
2.Canvas 通过JavaScript来绘制2D图形
3.SVG基于XML，这意味着SVG DOM中的每个元素都是可用的。您可以为某个元素附加JavaScript事件处理器。
4.在SVG中，每个被绘制的图形均被视为对象，如果SVG对象的属性发生变化，那么浏览器能够自动重现图形。
5.Canvas 是逐像素进行渲染的。在Canvas中，一旦图形被绘制完成，它就不会继续得到浏览器的关注。如果其位置发生改变，那么整个场景也需要重新绘制，包括任何或许已被图形覆盖的对象
```

{% asset_img  svg和canvas的区别.png %}

# 39、设置首屏的 Loading 加载

用css写一个圆圈,用来表示进度

```url
https://blog.csdn.net/weixin_46837985/article/details/124185597?ops_request_misc=&request_id=&biz_id=102&utm_term=%E7%BA%AFcss%E6%89%8B%E5%86%99loading%E6%95%88%E6%9E%9C&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-124185597.142^v50^control,201^v3^control_2&spm=1018.2226.3001.4187
```

# 40、比较转弯的赋值运算

**重点**: 首先要弄清一个问就是，var/let/const 这些声明的操作符，语句中的等号左边，绝不可能是一个 ' 表达式 ' ，也就是绝对不可以是一个'引用'例如：
    var a.x = ...   // <- 这里将导致语法出错

**所以说, 如果遇到a.x = a , 不管a.x在那个位置, 都是会优先给他赋值的!**

```js
var a = {
    x: 2,
}
a.x = a = 22;
console.log(a); // 22

var a = {
    x: 2,
}
a = a.x = 22;
console.log(a); // 2

var c = {};
var b= {}
c.a = b = {'a':1};
console.log(c.a); // {a: 1}

var d = {};
d.a = d = {'a':1};
console.log(d.a); // 1

var e = {};
e.a = e = {'ee':1};
console.log(e.a); // undefined

var a = {n:1}, ref = a;
a.x = a = {n:2};
console.log(a.x); // --> undefined
console.log(ref.x); // {n:2}
```

**另一种赋值**

```js
var a = 333;
var arr = [1, 2, 3];
a = arr[3] = [];
a.push(5, 6, 7);
console.log(a); // (3) [5, 6, 7]
console.log(arr); // (4) [1, 2, 3, Array(3)]
```











23、23、23、23

