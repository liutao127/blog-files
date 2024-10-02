---
title: thin学习笔记
date: 2022-11-29 19:21:34
tags: thin
---

# 1. thin字符串模板的使用

<!-- more -->

**说明: 如果使用thin 必须要先引入 jquery**

**前提: 所有的代码模板都如下, 在data中写入要使用的数据, 并且在template中写入最后DOM树的虚拟DOM, 再传给render函数就可以实现上树了**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <link rel="stylesheet" href="../utils/thin.css">
</head>
<body>
  <div class="container"></div>

  <script src="../utils/jquery.js"></script>  
  <script src="../utils/thin.js"></script>
  <script>
    const data = {

    };
    const template = {
      
    };

    $('.container').render({
      data,
      template
    })

  </script>

</body>
</html>
```

## 1. 字符串模板的使用

```js
const data = {
    name: '刘涛',
    height: 185
};
// 注意: 如果没有要传入的data, 可以不给data, 直接template
$('.container').render({
    data,
    // this is 刘涛, height is 185
    template: 'this is [[name]], height is [[height]]'
})
```

# 2. element模板的使用

## 1. element渲染模板

```js
const data = {
    name: '刘涛',
    age: 22
}
const template = {
    e: 'p',
    t: 'this is [[name]]'
}

$('.container').render({
    data,
    template
})

console.log($('p')[0]); // 最后生成的p标签
```

## 2. attribute的使用

```html
<style>
    #box {
      width: 250px;
      height: 250px;
      background-color: red;
    }
    .divClass {
      width: 200px;
      height: 200px;
      background-color: pink;
    }
</style>

<script>
    const data = {
      name: '刘涛',
      age: 22
    }
    const template = {
      e: 'div',
      a: {
        id: 'box',
        class: 'divClass',
        "data-test": 'this is data-test [[age]]',
        "city": '天津'
      },
      t: 'this is [[name]], age is [[age]]'
    }

    $('.container').render({
      data,
      template
    })

    const div = document.getElementById('box');
    console.log($('.divClass')[0].dataset); // DOMStringMap {test: 'this is data-test 22'}
    console.log($('.divClass').attr('city')); // 天津
    console.log(div.getAttribute('city')); // 天津

    console.log($('.divClass')[0]['data_of_thin']); // {name: '刘涛', age: 22}
</script>
```

## 3. a的函数使用

```js
const data = {
    name: '刘涛',
    age: 22,
    field: 'a'
}
const template = {
    e: 'div',
    // 重点!!!
    a: {
        class: function(param) {
            if(param.data.field === 'a') {
                return 'divClassA';
            } else {
                return 'divClassB';
            }
        }
    },
    t: 'this is [[name]], age is [[age]]'
}

$('.container').render({
    data,
    template
})

const div = document.getElementById('box');
console.log($('.divClassA')[0]); // class为divClass的div标签
```

## 4. 快捷设置属性的方式

```js
const data = {
    name: '刘涛',
    age: 22,
    field: 'a'
}
// class id name 都是可以的
const template = {
    e: 'div',
    id: 'box',
    class: 'divClass',
    name: 'test-name',
    t: 'this is [[name]], age is [[age]]'
}

$('.container').render({
    data,
    template
})

console.log($('.divClass')[0]); // class为divClass的div标签
```

## 5. datapath和foreach的使用

**下面两段代码中, 二者的生成DOM结构相同**

```html
<div class=" container">
    <div>
		<p>this is name1</p>
        <p>this is name2</p>
        <p>this is name3</p>
        <p>this is name4</p>
    </div>
</div>
```

**二者的data相同, 只是template不同, data如下: **

```js
const data = {
    arr: [
        {name: 'name1'},
        {name: 'name2'},
        {name: 'name3'},
        {name: 'name4'},
    ]
};
```



### datapath

```js
const template = {
    e: 'div',
    t: {
        e: 'p',
        datapath: 'arr',
        t: 'this is [[name]]'
    }
};

$('.container').render({
    data,
    template
})
```

### foreach

```js
const template = {
    e: 'div',
    t: {
        foreach: 'arr',
        t: {
            e: 'p',
            t: 'this is [[name]]'
        }
    }
};

$('.container').render({
    data,
    template
})
```

## 6. 父模板给子模板data数据的使用

```js
const data = {
    arr: [
        {name: 'name1'},
        {name: 'name2'},
        {name: 'name3'},
        {name: 'name4'},
    ]
};
const template = {
    e: 'div',
    t: [
        {
            e: 'p',
            datapath: 'arr',
            t: 'this is [[name]]'
        },
        {
            e: 'span',
            // 这样给data, 下面t中子模板就能访问到data中的属性
            data: {
                name: '刘涛',
                age: 22,
            },
            t: '我是[[name]], 我[[age]]'
        }
    ]
};

$('.container').render({
    data,
    template
})
```

## 7. 子模板使用最外层data的使用

```js
const data = {
    name: 'ipt',
    info: {
        name: '刘涛',
        age: 22,
        car: {
            name: '奔驰',
            color: 'black',
            info: {
                buyDate: '2024'
            }
        }
    }
};
// 注意: 这里的数据选择的时候都是从 data 的子数据开始的
// 说明查找数据的时候都是从头开始, 不是每次都传入每级的子数据
const template = {
    e: 'div',
    t: [
        '我是[[info/name]], 我[[info/age]]',
        {
            e: 'div',
            t: [
                '车名字: [[info/car/name]], 颜色: [[info/car/color]]',
                {
                    e: 'p',
                    t: '购买时间: [[info/car/info/buyDate]]',
                }
            ],
            click: function(param) {
                console.log(param);
            }
        }
    ]
};

$('.container').render({
    data,
    template
})
```

## 8. event的处理方式

```js
const data = {
    name: '刘涛',
    age: 22
};
const template = {
    e: 'form',
    class: 'formClass',
    t: [
        {
            e: 'label',
            t: [
                '姓名: ',
                {
                    e: 'input',
                    a: {
                        name: 'name',
                        type: 'text',
                        value: '[[name]]'
                    }
                }
            ]
        },
        {
            e: 'label',
            t: [
                '年龄: ',
                {
                    e: 'input',
                    a: {
                        name: 'age',
                        type: 'number',
                        value: '[[age]]'
                    }
                }
            ]
        },
        {
            e: 'input',
            a: {
                type: 'submit',
                value: '提交'
            },
            // 注意: 这里的 click 可以不用写在 event 里面, 因为 click 肯定要经常使用, 相当于是click的快捷使用方式
            click: function(param) {
                console.log(param);
            },
            event: {
                dblclick: function(param) {
                    console.log('双击了提交', param);
                },
                mouseover: function(param) {
                    console.log('鼠标移动到了提交上', param);
                }
            }
        },
        {
            e: 'button',
            a: {
                type: 'reset'
            },
            t: '重置',
            click: function(param) {
                console.log('点击了重置', param);
            }
        }
    ]
};

$('.container').render({
    data,
    template
})
```

## 9. 我的思考

```js
const data = {
    name: 'ipt',
    info: {
        name: '刘涛',
        age: 22,
        car: {
            name: '奔驰',
            color: 'black',
            info: {
                buyDate: '2024'
            }
        }
    }
};
const template = {
    e: 'div',
    t: [
        '我是[[info/name]], 我[[info/age]]',
        {
            e: 'div',
            t: [
                '车名字: [[info/car/name]], 颜色: [[info/car/color]]',
                {
                    e: 'input',
                    name: 'name',
                    value: '[[info/name]]'
                }
            ],
            click: function(param) {
                // 注意, 这里param中打印出来的new_data直接就是new_data: {name: '刘涛'}
                // 说明这里的 new_data 不是拷贝出来的原来的值, 而是哪个标签有name, 最后就打印哪个
                // old_data 是引用
                console.log(param);
                console.log(data);
            }
        }
    ]
};

$('.container').render({
    data,
    template
})
```

# 3. element操作style

## 1. style的设置

```js
const template = {
    e: 'div',
    style: {
        width: '200px',
        height: '200px',
        'background-color': 'red',
        'font-size': '20px'
    },
    t: '我是一个div'
};

$('.container').render({
    template
})
```

## 2. style的属性简写

```js
const template = {
    e: 'div',
    // 这里后面直接加数字就可以
    width: 300,
    height: 300,
    style: {
        'background-color': 'red',
        'font-size': '30px'
    },
    t: '我是一个div'
};

$('.container').render({
    template
})
```

## 3. style的函数使用

```js
const data = {
    field: 'div'
}

const template = {
    e: 'div',
    width: 300,
    height: 300,
    style: {
        'background-color': function(param) {
            // container   data 
            console.log(param);
            if(param.data.field === 'div') {
                // 最后结果就是红色
                return 'red'
            } else {
                return 'orange'
            }
        },
        'font-size': function(param) {
            if(param.data.boo) {
                return '30px'
            } else {
                return '20px'
            }
        }
    },
    t: '我是一个div'
};

$('.container').render({
    data,
    template
})
```

# 4. element使用timer

## 1. interval的使用

```js
let index = 1;

const template = {
    e: 'p',
    t: {
        timer: {
            interval: 1000,
            do: function(param) {
                console.log(index);
                index++;
                if(index > 4) {
                    $(param.container).remove();
                }
            }
        }
    }
};

$('.container').render({
    template
})
```

## 2. timeout的使用

```js
const template = {
    e: 'p',
    t: {
        timer: {
            delay: 1000,
            do: function(param) {
                console.log('我是延迟1s的计时器');
            }
        }
    }
};

$('.container').render({
    template
})
```

# 5. element中options下拉框的使用

**下面运行的DOM结果如下: **

```html
<div class="container">
    <form>
		性别:
		<select name="sex">
			<option value="man">男</option>
         	 <option value="woman">女</option>
       	 </select>
	</form>
</div>

```



## 1. options-对象的使用

```js
const template = {
    e: 'form',
    t: [
        '性别: ',
        {
            e: 'select',
            a: {
                name: 'sex'
            },
            options: {
                // 前面是标签的文字部分, 后面是value部分
                // innerText: value
                男: 'man',
                女: 'woman'
            }
        }
    ]
};

$('.container').render({
    template
})
```

## 2. options-数组的使用

```js
const template = {
    e: 'form',
    t: [
        '性别: ',
        {
            e: 'select',
            a: {
                name: 'sex'
            },
            // 这样不能设置value, 所以不推荐使用
            options: ['男', '女']
        }
    ]
};

$('.container').render({
    template
})
```

## 3. options-字符串的使用

```js
const template = {
    e: 'form',
    t: [
        '性别: ',
        {
            e: 'select',
            a: {
                name: 'sex'
            },
            // 这样不能设置value, 可以加空格, 应该有trim操作
            options: '男, 女'
        }
    ]
};

$('.container').render({
    template
})
```

## 4. options-绑定某个属性

```js
const data = {
    name: '刘涛',
    age: 22,
    sex: 'man'
}
const template = {
    e: 'form',
    t: [
        {
            e: 'label',
            t: [
                '姓名: ',
                {
                    e: 'input',
                    name: 'name',
                    a: {
                        type: 'text'
                    },
                    value: '[[name]]'
                }
            ]
        },
        {
            e: 'label',
            t: [
                '年龄: ',
                {
                    e: 'input',
                    name: 'age',
                    a: {
                        type: 'number'
                    },
                    value: '[[age]]'
                }
            ]
        },
        {
            e: 'lebel',
            t: [
                '性别: ',
                {
                    e: 'select',
                    name: 'sex',
                    options: {
                        男: 'man',
                        女: 'woman'
                    },
                    // value属性存储该项的值，后端程序通过value属性获取该项的值；
                    // selected是个特殊属性，只有属性名没有值，option标签添加该属性后，该项被默认选择。
                    selected: '[[sex]]',
                    // value: '[[sex]]'
                }
            ]
        }
    ]
};

$('.container').render({
    data,
    template
})
```

# 6. element中bind的绑定使用

```js
const data = {
    name: '刘涛',
    age: 22,
    info: {
        wife: '王甜甜',
        age: 22,
        car: {
            name: '宝马',
            color: 'white'
        }
    }
};
const template = {
    e: 'form',
    t: [
        {
            e: 'label',
            t: [
                '姓名: ',
                {
                    e: 'input',
                    name: 'name',
                    a: {
                        type: 'text'
                    },
                    bind: 'name'
                }
            ]
        },
        {
            e: 'label',
            t: [
                '年龄: ',
                {
                    e: 'input',
                    name: 'age',
                    a: {
                        type: 'text'
                    },
                    bind: 'age'
                }
            ]
        },
        '<br/>',
        {
            e: 'label',
            t: [
                '爱人: ',
                {
                    e: 'input',
                    name: 'wife',
                    a: {
                        type: 'text'
                    },
                    bind: 'info/wife'
                }
            ]
        },
        {
            e: 'label',
            t: [
                '年龄: ',
                {
                    e: 'input',
                    a: {
                        type: 'text'
                    },
                    bind: 'info/age'
                }
            ]
        },
        '<br/>',
        {
            e: 'label',
            t: [
                '王甜甜的车的信息: ',
                {
                    e: 'ul',
                    t: {
                        foreach: 'info/car',
                        t: {
                            t: {
                                e: 'li',
                                t: '我开的车是一辆[[color]]的[[name]]'
                            }
                        }
                    }
                },
                {
                    e: 'ul',
                    t: {
                        e: 'li',
                        datapath: 'info/car',
                        t: '[[name]]---[[color]]'
                    }
                }
            ]
        },
        {
            e: 'buton',
            t: '点我查看数据',
            click: (param) => {
                // 这里修改上面的数据, 都可以获取最新的数据, bind !!!
                console.log(param);
            }
        }
    ]
};

$('.container').render({
    data,
    template
})
```

# 7. t模板中函数的使用

```js
const data = {
    name: '刘涛',
    age: 22
};
const template = {
    e: 'div',
    t: [
        '姓名: ',
        {
            e: 'input',
            a: {
                type: 'text'
            },
            value: '[[name]]'
        },
        '<br/>',
        '年龄: ',
        {
            e: 'span',
            name: 'age',
            t: (param) => {
                // container    data
                console.log($('[name=age]').text(param.data.age));
                // console.log(this); // 普通和箭头都是 window
            }
        }
    ]
};

$('.container').render({
    data,
    template
})
```

# 8. if结构的使用

```js
/* 
      语法: 
        {
          if: 布尔表达式|判断函数,
          then: 真模板,
          else: 假模板
        }
*/
const data = {
    name: '刘涛',
    age: 22
};
const template = {
    e: 'div',
    t: {
        if: function(param) {
            // container data
            console.log(param);
            return param.data.age === 22;
        },
        then: { // if返回为true的时候执行
            e: 'p',
            t: 'if返回结果为true, 当前为 true模板'
        },
        else: {
            e: 'p',
            t: 'if返回结果为false, 当前为 false模板'
        }
    }
};

$('.container').render({
    data,
    template
})
```

# 9. switch-case结构的使用

```js
/* 
    语法: 
      {
        switch:"[[参数]]",
        case:{
            "条件1":"template1",
            "条件2":"template2",
            default:"default template"
        }
      }
*/
const data = {
    name: '王甜甜',
    age: 22
};
const t1 = {
    e: 'div',
    t: '我是刘涛, 我今年[[age]]'
};
const t2 = {
    e: 'div',
    t: '我是王甜甜, 我今年[[age]]'
}
const template = {
    e: 'div',
    t: {
        switch: '[[name]]',
        case: {
            '刘涛': t1,
            '王甜甜': t2
        }
    }
};

$('.container').render({
    data,
    template
})
```

# 10. when结构的使用

## 属性值

```js
const data = {
    name: '王甜甜',
    age: 22,
    boo1: true
};

const template = {
    e: 'div',
    t: '我是[[name]], 我今年[[age]]',
    // 如果该数据路径值存在且不为空，渲染当前容器；反之不渲染。
    // 也可填入布尔值：true渲染；false不渲染
    when: 'boo1'
};


$('.container').render({
    data,
    template
})
```

## 函数

```js
const data = {
    name: '王甜甜',
    age: 22,
    boo: true
};

const template = {
    e: 'div',
    t: '我是[[name]]], 我今年[[age]]',
    when: 'boo',

    // 这里有问题, 会报错, 找不到 data_of_then
    // 是因为这里的 t 不能放在最外面, 需要再给他的外面加一层

    // when: function (r) {
    //     if (r.data.boolen) {
    //         return true;
    //     } else {
    //         return false;
    //     }
    // }
};

$('.container').render({
    data,
    template
})
```

# 11. 模板组合使用

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <link rel="stylesheet" href="../utils/thin.css">
</head>
<body>
  <div class="container"></div>

  <script src="../utils/jquery.js"></script>  
  <script src="../utils/thin.js"></script>
  <script>
    const data = {
      name: '刘涛',
      age: 22,
      info: {
        wife: '王甜甜',
        age: 22
      },
      boo: true,
      case: 'A',
      fruits: ['apple', 'bnanan', 'pear', 'peach'],
      infos: [
        {name: '刘涛', age: 22},
        {name: '王甜甜', age: 22},
        {name: '刘静怡', age: 12}
      ]
    };
    const template = {
      e: 'div',
      a: {
        id: 'box'
      },
      // 字符串形式
      t: [
        '我是一个div, 我在最上面',
        {
          e: 'ul',
          // element 形式
          t: [
            {
              e: 'li',
              t: '我是[[name]], 我今年[[age]]'
            },
            {
              e: 'li',
              // if形式
              t: {
                if: function(param) {
                  return param.data.boo;
                },
                then: '我是刘夫人[[info/wife]], 我今年[[info/age]]',
                else: 'if条件判断为false'
              }
            }
          ]
        },
        {
          e: 'div',
          // when形式
          t: '我是when模板成功后显示的',
          when: function(param) {
            if(param.data.boo) {
              return true;
            } else {
              return false;
            }
          }
        },
        {
          e: 'div',
          // switch形式
          t: {
            switch: '[[case]]',
            case: {
              'A': '我是switch的模板A',
              'B': '我是switch的模板B',
            }
          }
        },
        // 函数模板
        function(param) {
          $(param.container).append(param.data.fruits[2]);
        },
        '<br/>',
        // foreach
        {
          e: 'ul',
          t: {
            // 这层的 div 是不会添加到 div 上的
            // 后面的 li 会全部追加到 ul 上
            e: 'div',
            foreach: 'fruits',
            t: {
              e: 'li',
              t: '[[.]]'
            }
          }
        },
        // datapath
        {
          e: 'ul',
          t: {
            e: 'li',
            datapath: 'fruits',
            t: '[[.]]'
          }
        }
      ]
    };

    $('.container').render({
      data,
      template
    })

  </script>

</body>
</html>
```

# 12. poplayer弹出层的使用

## 1. 初体验

```js
const data = {
    name: '刘涛',
    age: 22
}

const template = {
    e: 'div',
    t: '我是[[name]], 我今年[[age]]'
}

// 注：onclose 只为 poplayer 弹出层右上角关闭按钮的回调函数。
// 其他 jq 操作移出弹出层都不会触发 onclose 事件
const onclose = function() {
    // 这个回调没有参数
    console.log('我关闭了');
}

poplayer({
    header: '我是header',
    width: '300px',
    height: '300px',
    data,
    template,
    onclose
})
```

## 2. 自定义浮层

```js
const data = {
    name: '刘涛',
    age: 22
}

const template = {
    e: 'div',
    t: '我是[[name]], 我今年[[age]]'
}

const onclose = function() {
    // 这个回调没有参数
    console.log('我关闭了');
}


poplayer({
    header: 'header',
    render: renderFun
});

function renderFun(param) {
    // to render

    // param结构：
    // { container:{popbodycontainer} }
    console.log(param);
}
```

## 3. 动态关闭弹出层

```js
const data = {
    name: '刘涛',
    age: 22,
    fruits: ['苹果', '香蕉', '橘子']
}

const testPop = poplayer({
    data,
    header: '弹出框',
    template: {
        e: 'div',
        t: [
            '我是[[name]], 我今年[[age]]',
            {
                e: 'ul',
                t: {
                    e: 'li',
                    datapath: 'fruits',
                    t: '[[.]]'
                }
            },
            {
                e: 'button',
                t: '关闭',
                click: function(param) {
                    console.log(param);
                    // 主动关闭 !!!!!!!!!!!!!
                    testPop.remove();
                }
            }
        ]
    },
    onclose: function() {
        console.log('我关闭了');
    }
})
```

# 13. tab标签+multiview

## 1. 基本使用

```js
const data = {
    name1: '刘涛',
    age1: 22,
    name2: '王甜甜',
    age2: 3
};
const template = [
    {
        multiview: [
            {
                e: "tab",
                t: [
                    {
                        e: "tab-nav",
                        t: "导航1",
                        a: {
                            class: "active",
                            // 这里的 view 是下面 view 的id
                            view: "nav1",
                        },
                    },
                    {
                        e: "tab-nav",
                        t: "导航2",
                        a: {
                            view: "nav2",
                        },
                    },
                    // more tab-nav
                ],
            },
            {
                e: "view",
                t: "我是[[name1]], 我今年[[age1]]",
                id: "nav1",
                class: "active",
            },
            {
                e: "view",
                t: "我是[[name2]], 我今年[[age2]]",
                id: "nav2",
            },
            // more view
        ],
    },
];

$(".container").render({
    data,
    template,
});
```

## 2. 路由-动态渲染

```js
$('.container').render({
    data: "",
    template: [
        {
            tab: {
                class: 'className',
                nav: {
                    '导航1': {
                        // 自动向浏览器添加哈希路由(可选)
                        // 刷新页面自动调用当前哈希路由的click事件
                        hashpath: "#nav1",
                        click: function (a) {
                            // 渲染（可异步）
                            $("#show_container").text(1);
                        },
                    },
                    '导航2': {
                        hashpath: "#nav2",
                        click: function () {
                            $("#show_container").text(2);
                        },
                    },
                    '导航3': {
                        hashpath: "#nav3",
                        click: function () {
                            $("#show_container").text(3);
                        },
                    },
                    '导航4': {
                        hashpath: "#nav4",
                        click: function () {
                            $("#show_container").text(4);
                        },
                    },
                },
                // 默认渲染第几个
                default: 2,
            },
        },
        {
            e: "div",
            id: "show_container",
        },
    ],
});
```

# 14. 简写以及补充内容

## 1. 模板的简写

```js
// 之前
    /*
      const data = {name: '刘涛'};
      const template = {
        e: 'p',
        t: 'this is [[name]]'
      };
      $('.container').render({data, template}) 
	*/

    // 简写
    const data = {name: '刘涛'};
    const template = {
      p: 'this is [[name]]'
    };
    $('.container').render({data, template})
```

## 2. 简写的特例

```js
/* 
      p: 'this is [[name]]'  =>  <p>this si 刘涛</p>
      'input':'age'  => <input name="age" type="text">
      'select':'hobbit' => <select name="hobbit" id="hobbit"></select>
      'textarea':'description' => <textarea name="description" id='description'></textarea>
      'a':'www.wanfangdata.com.cn'  =>  <a href="www.wanfangdata.com.cn">www.wanfangdata.com.cn</a>
*/

// 简写
const data = {
    name: '刘涛',
    age: 22
};
const template = [
    { 
        p: 'this is [[name]]' 
    }, {
        div: '我是一个div'
    }, {
        'input': 'age'
    }, {
        'select':'hobbit'
    }, {
        'textarea':'description'
    }, {
        // a后面跟的值会被渲染成a标签的href属性和content
        // 不建议使用简化写法渲染a标签，建议使用标准写法，
        // 因为简化写法中a标签的标签名和attr得简写相同，容易混淆。
        'a':'www.wanfangdata.com.cn'
    }


];
$('.container').render({data, template})
```

## 3. handler的用法

```js
var testDiv = {
    e: "ul",
    closeon: "click", // 该点击事件绑定在当前的ul上
    // 这里的in 是加到里面的最后面, append
    in: ".test_div",
    // before: '.test_div',
    // after: '.test_div',
    t: [
        {
            e: "li",
            t: "1",
        },
        {
            e: "li",
            t: "2",
        },
        {
            e: "li",
            t: "3",
        },
        {
            e: "li",
            t: "4",
        },
    ],
    click: function(param) {
        console.log(param);
        console.log('我是ul的click');
    }
};
$('.container').render({
    template: {
        e: "div",
        t: [
            '<div class="test_div">我是容器</div>',
            {
                e: "button",
                t: "test",
                click: testDiv, //点击渲染一组DOM树在指定位置
            },
        ],
    },
});
```

