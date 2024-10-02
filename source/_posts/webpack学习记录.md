---
title: webpack学习记录
date: 2022-04-07 15:35:25
tags: webpack
---

# webpack开发环境配置详细

## 1、打包样式资源

<!-- more -->

```js
/* 
  webpack.config.js   webpack的配置文件
   作用：知识webpack干那些活儿，当你运行webpack指令时，会加载里面的配置

   所有构建工具都是基于nodejs平台运行的  模块化默认采用commonjs
*/

// resolve用来拼接绝对路径的方法
const { resolve } = require('path');

module.exports = {
  // webpack配置
  // 入口起点
  entry: './src/index.js',
  // 输出
  output: {
    // 输出文件名
    filename: 'built.js',
    // 输出路径
    // __dirname nodejs的变量，代表当前文件的目录绝对路径
    path: resolve(__dirname, 'build')
  },
  // loader的配置
  module: {
    rules: [
      // 详细的loader配置
      // 不同文件必须配置不同的loader
      {
        // 匹配哪些文件
        test: /\.css$/,
        // 使用哪些loader
        use: [
          // use数组中loader执行顺序：从 右到左，从下到上 一次执行
          'style-loader', // 创建style标签，将js中的样式资源插入进去，添加到页面中生效
          'css-loader' // 将css文件变成commonjs模块加载到js中，里面内容是样式字符串
        ]
      },
      {
        test: /\.less$/,
        use:[
          'style-loader',
          'css-loader',
          // 这里需要两个loader:  less-loader  和  less
          'less-loader' // 将less文件编译成css文件
        ]
      }

    ]
  },
  // plugins的配置
  plugins: [
    // 详细plugins的配置

  ],
  // 模式
  mode: 'development',  // 开发模式
  // mode: 'production'
}
```

## 2、打包html资源

```js
/* 
  loader： 1.下载  2. 使用（配置）
  plugins： 1.下载  2. 引入  3. 使用
*/
const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'built.js',
    path: resolve(__dirname, 'build')
  },
  module: {
    rules:[
      // loader的配置
    ]
  },
  plugins:[
    // plugins的配置
    // html-webpack-plugin 
    // 功能： 默认会创建一个空的HTML， 自动引入打包输出所有的资源 （JS/CSS）
    // 需求： 需要有结构的HTML文件
    new HtmlWebpackPlugin({
      // 复制 './src/index.html'  文件，并自动引入打包输出的所有资源（JS/CSS）
      template: './src/index.html'
    })
  ],
  mode: 'development'
}
```



## 3、打包图片资源

```js

const { resolve } = require('path'); 
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'built.js',
    path: resolve(__dirname, 'build')
  },
  module:{
    rules:[
      {
        test: /\.less$/,
        // 使用多个loader处理，用use数组
        use:[
          'style-loader',
          'css-loader',
          'less-loader'
        ]
      },
      // {
      //   test: /\.(jpg|png|gif)$/,
      //   // 使用一个loader处理
      //   // 需要下载两个包， url-loader， file-loader
      //   loader: 'url-loader',
      //   // 配置loader
      //   options:{
      //     // 图片大小小于8kb，就会被base64处理
      //     // 优点： 减少请求数量（减轻服务器执行压力）
      //     // 缺点： 图片体积会更大(文件请求速度更慢)
      //     limit: 8 * 1024,
      //     type: 'javascript/auto'
      //   }
      // }
        {
          test:/\.(jpg|png|gif)$/,
          parser: {
            dataUrlCondition: {maxSize: 8 * 1024}
          },
          generator:{
            filename: 'img/[name].[hash:8][ext]'
          }
        },
        {
          test: /\.html$/,
          // 处理html文件的图片（负责引入img，从而能被 url-loader进行处理）
          loader: 'html-loader'
        }
      
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html'
    })
  ],
  mode: 'development'
}
```



## 4、打包其他资源

```js

const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'built.js',
    path: resolve(__dirname, 'build')
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          'style-loader',
          'css-loader'
        ]
      },
      {
        // 排除css js  html 资源
        exclude: /\.(css|js|html)$/,
        type: 'asset/resource'
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html'
    })
  ],
  mode: 'development'
}

```



## 5、配置devServer

```js

const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'built.js',
    path: resolve(__dirname, 'build')
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          'style-loader',
          'css-loader'
        ]
      },
      {
        // 排除css js  html 资源
        exclude: /\.(css|js|html)$/,
        type: 'asset/resource'
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html'
    })
  ],
  mode: 'development',
  // 开发服务器  devServer：用来自动化 （自动编译，自动打开浏览器，自动刷新浏览器~~~）
  // 特点：自会在内存中编译打包，不会有任何输出
  // 启动devServer指令为： webpack server
  devServer: {
    static: resolve(__dirname, 'src'),
    // 启动gzip压缩 --- 代码体积更小，速度更快  
    compress: true,
    // 端口号
    port: 7000,
    open: true
  }
}

```

# 开发环境配置

```js
/* 
  指令： 
    webpack =  打包
    webpack serve  只会在内存中编译打包，无输出
*/
const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/js/index.js',
  output: {
    filename: 'js/built.js',
    path: resolve(__dirname, 'build')
  },
  module: {
    rules:[
      {
        test: /\.css$/,
        use:[
          'style-loader',
          'css-loader'
        ]
      },
      {
        test: /\.less$/,
        use:[
          'style-loader',
          'css-loader',
          'less-loader'
        ]
      },
      {
        test: /\.(jpg|png|gif)$/,
        type: 'asset',
        parser:{
          dataUrlCondition: {
            maxSize: 8 * 1024
          }
        },
        generator: {
          filename: 'img/[hash:10][ext]'
        }
      },
      {
        test: /\.html$/,
        loader: 'html-loader'
      },
      {
        exclude: /\.(css|js|html|less|scss|jpg|png|gif)$/,
        type: 'asset/resource',
        generator:{
          filename: 'medias/[hash:10][ext]'
        }
      }
    ]
  },
  plugins:[
    new HtmlWebpackPlugin({
      template: './src/index.html'
    })
  ],
  devServer: {
    static: resolve(__dirname, 'src'),  // 入口文件
    compress: true,  // 压缩  gip
    port: 7000,   // 端口号
    open: true    // 自动打开
  },
  mode: 'development'
}
```



# webpack生产环境配置详细

## 1、提取css成单独文件

```js

const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  entry: './src/js/index.js',
  output: {
    filename: 'js/built.js',
    path: resolve(__dirname, 'build')
  },
  module:{
    rules:[
      {
        test: /\.css$/,
        use: [
          // 创建style标签，将样式方进去
          // 'style-loader',
          // 这个loader取代 style-loader。作用：提取css成单独文件
          MiniCssExtractPlugin.loader,
          // 将css文件整合到js文件中
          'css-loader'
        ]
      }
    ]
  },
  plugins:[
    new HtmlWebpackPlugin({
      template: './src/index.html'
    }),
    new MiniCssExtractPlugin({
      filename: 'css/main.css'
    })
  ],
  mode: 'development',
  devServer: {
    static: resolve(__dirname, 'src'),
    compress: true,
    port: 7000,
    open: true
  }
}
```



## 2、css的兼容处理

```js

const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

// 设置nodejs环境变量
// process.env.NODE_ENV = "development"


module.exports = {
  entry: './src/js/index.js',
  output: {
    filename: 'js/built.js',
    path: resolve(__dirname, 'build')
  },
  module:{
    rules:[
      {
        test: /\.css$/,
        use: [
          MiniCssExtractPlugin.loader,
          'css-loader',
          /* 
            css兼容性处理：  postcss   ---》  postcss-loader    postcss-preset-env
            postcss-preset-env 帮助 postcss-loader 找到package.json中 browserslist里面的卑职，通过配置加载指定的兼容性样式

            "browserslist":{
              // 生产环境 --》  设置node环境变量： process.env.NODE_ENV = development
              "development": [
                "last 1 chrome version",
                "last 1 firefox version",
                "last 1 safari version"
              ],
              // 生产环境：默认是生产环境
              "production":[
                "> 0.2%",
                "not dead",
                "not op_mini all"
              ]
            }

          */
          // 使用loader的默认配置
          //  'postcss-loader',
          // 修改loader配置
          {
            loader: 'postcss-loader',
            options: {
              postcssOptions: {
                // webpack 4配置
                // ident: 'postcss',
                // plugins: () => [
                //   require('postcss-preset-env')()
                // ]
                // webpack 5配置
                plugins: [require('postcss-preset-env')()]
              }
            }
  
          }
        ]
      }
    ]
  },
  plugins:[
    new HtmlWebpackPlugin({
      template: './src/index.html'
    }),
    new MiniCssExtractPlugin({
      filename: 'css/main.css'
    })
  ],
  mode: 'development',
  devServer: {
    static: resolve(__dirname, 'src'),
    compress: true,
    port: 7000,
    open: true
  }
}
```



## 3、压缩css

```js

const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');

// 设置nodejs环境变量
// process.env.NODE_ENV = "development"

// optimize-css-assets-webpack-plugin

module.exports = {
  entry: './src/js/index.js',
  output: {
    filename: 'js/built.js',
    path: resolve(__dirname, 'build')
  },
  module:{
    rules:[
      {
        test: /\.css$/,
        use: [
          MiniCssExtractPlugin.loader,
          'css-loader',
          {
            loader: 'postcss-loader',
            options: {
              postcssOptions: {
                plugins: [require('postcss-preset-env')()]
              }
            }
          }
        ]
      },
      {
        test: /\.(jpg|png|gif)$/,
        type: 'asset',
        parser:{
          dataUrlCondition: {
            maxSize: 8 * 1024
          }
        },
        generator: {
          filename: 'img/[hash:10][ext]'
        }
      }
    ]
  },
  plugins:[
    new HtmlWebpackPlugin({
      template: './src/index.html'
    }),
    new MiniCssExtractPlugin({
      filename: 'css/main.css'
    }),
    // 压缩css
    new OptimizeCssAssetsWebpackPlugin()
  ],
  mode: 'development',
  devServer: {
    static: resolve(__dirname, 'src'),
    compress: true,
    port: 7000,
    open: true
  }
}
```



## 4、js语法检查

```js

const { resolve }  = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const ESLintPlugin = require('eslint-webpack-plugin');

module.exports = {
  entry: './src/js/index.js',
  output: {
    filename: 'js/built.js',
    path: resolve(__dirname, 'build')
  },
  module: {
    rules:[
      /* 
        语法检查：  eslint-loader  eslint
          注意： 只检查自己的源代码，第三方库的是不检查的
          设置检查规则：
            package.json中 eslintConfig中设置~
            "eslintConfig":{
              "extends": "airbnb-base"
            }
            airbnb  ---》  eslint-config-airbnb-base   eslint-plugin-import  eslint 

      */
      // {
      //   test: /\.js$/,
      //   exclude: /node_modules/,
      //   loader: 'eslint-loader',
      //   options:{
      //     // 自动修复
      //     fix: true
      //   }
      // },
      {
        test: /\.css$/,
        use:[
          'style-loader', 'css-loader'
        ]
      }
    ]
  },
  plugins:[
    new HtmlWebpackPlugin({
      template: './src/index.html'
    }),
    // eslint-loader已经弃用，现在用 eslint-webpack-plugin
    new ESLintPlugin({
      extensions: ['js'],
      context: resolve('src'),
      exclude: '/node_modules',
      // fix: true
    })
  ],
  mode: "development"
}
```



## 5、js的兼容处理

```js

const { resolve }  = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/js/index.js',
  output: {
    filename: 'js/built.js',
    path: resolve(__dirname, 'build')
  },
  module: {
    rules:[
      /* 
        语法检查：  eslint-loader  eslint
          注意： 只检查自己的源代码，第三方库的是不检查的
          设置检查规则：
            package.json中 eslintConfig中设置~
            "eslintConfig":{
              "extends": "airbnb-base"
            }
            airbnb  ---》  eslint-config-airbnb-base   eslint-plugin-import  eslint 

      */
      // {
      //   test: /\.js$/,
      //   exclude: /node_modules/,
      //   loader: 'eslint-loader',
      //   options:{
      //     // 自动修复
      //     fix: true
      //   }
      // },
      /* 
        js兼容性处理： babel-loader @babel/core  @babel/preset-env
        这样加presets: [['@babel/preset-env',{targets: {edge: "11",firefox: "60",chrome: "58",safari: "11"}}]]
        1. 基本js兼容性问题  --》 @babel/preset-env
            问题：只能转换基本语法，如Promise高级语法不能转换
        2. 全部的js兼容性处理  --》  @babel/polyfill
            问题：我只要解决部分兼容性问题，但是将所有兼容性代码全部引入，体积太大了~
        3. 需要做兼容性处理的就做： 按需加载   --》  corejs
            
      */
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel-loader',
        options: {
          // 预设：指示babel做怎么样的兼容性处理
          presets: [
            [
              '@babel/preset-env',
              {
                // 按需下载
                useBuiltIns: 'usage',
                // 指定corejs的版本
                corejs: {
                  version: 3
                },
                // 指定兼容性做到那个版本浏览器
                targets: {
                  chrome: '60',
                  firefox: '60',
                  ie: '9',
                  safari: '10',
                  edge: '17'
                }
              }
            ]
          ]
        }
      },
      {
        test: /\.css$/,
        use:[
          'style-loader', 'css-loader'
        ]
      }
    ]
  },
  plugins:[
    new HtmlWebpackPlugin({
      template: './src/index.html'
    })
  ],
  mode: "development"
}
```



## 6、压缩js

```js

const { resolve }  = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/js/index.js',
  output: {
    filename: 'js/built.js',
    path: resolve(__dirname, 'build')
  },
  plugins:[
    new HtmlWebpackPlugin({
      template: './src/index.html'
    })
  ],
  // 生产环境下会自动压缩js代码
  mode: "production"
}
```



## 7、压缩html

```js

const { resolve }  = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/js/index.js',
  output: {
    filename: 'js/built.js',
    path: resolve(__dirname, 'build')
  },
  plugins:[
    new HtmlWebpackPlugin({
      template: './src/index.html',
      minify:{
        // 移除空格
        collapseWhitespace: true,
        // 移除注释
        removeComments: true
      }
    })
  ],
  // 生产环境下会自动压缩js代码
  mode: "production"
}
```



# 生产环境配置

```js

const {resolve} = require('path');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');
const ESLintWebpackPlugin = require('eslint-webpack-plugin');

// 定义nodejs环境变量： 决定使用browserlist的哪个环境
process.env.NODE_ENV = 'production';

const commonCssLoader = [
  MiniCssExtractPlugin.loader,
  'css-loader',
  {
    // 兼容性处理css ，还需要在 package.json中定义browserlist
    loader: 'postcss-loader',
    options: {
      postcssOptions: {
        plugins: [require('postcss-preset-env')()]
      }
    }
  }
]

module.exports = {
  entry: './src/js/index.js',
  output: {
    filename: 'js/built.js',
    path: resolve(__dirname, 'build')
  },
  module: {
    rules:[
      {
        // 提取css文件成为单独的文件
        test: /\.css$/,
        use: [...commonCssLoader]
      },
      {
        // 提取css文件成为单独的文件
        test: /\.less$/,
        use: [...commonCssLoader,'less-loader']
      },
      /* 
        正常来讲，一个文件只能被一个loader处理
        当一个文件被两个loader处理的时候，一定要指定loader执行先后顺序
      */
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel-loader',
        options: {
          // 预设
          presets: [
            [
              '@babel/preset-env',
              {
                // 按需下载
                useBuiltIns: 'usage',
                // 指定corejs的版本
                corejs: {version: 3},
                targets: {
                  chrome: '60',
                  firefox: '60',
                  ie: '9',
                  safari: '10',
                  edge: '17'
                }
              }
            ]
          ]
        }
      },
      {
        test: /\.(jpg|png|gif)$/,
        parser: {
          dataUrlCondition: {maxSize: 8 * 1024}
        },
        generator: {
          filename: 'img/[name].[hash:8][ext]'
        }
      },
      {
        test: /\.html$/,
        loader: 'html-loader'
      },
      {
        test: /\.(js|css|less|html|jpg|png|gif)$/,
        generator: {
          filename: 'medias/[hash:10][ext]'
        }
      }
    ]
  },
  plugins:[
    // 提取css为单独文件的插件
    new MiniCssExtractPlugin({
      filename: 'css/built.css'
    }),
    new HtmlWebpackPlugin({
      template: './src/index.html',
      minify:{
        collapseWhitespace: true,
        removeComments: true
      }
    }),
    new OptimizeCssAssetsWebpackPlugin(),
    // eslint-webpack-plugin的用法，并且要的package.json中进行配置
    new ESLintWebpackPlugin({
      extensions: ['js'],
      context: resolve('src'),
      exclude: '/node_modules',
      fix: true
    })
  ],
  mode: "production"
}
```

# webpack优化环境配置详细

## 1、HMR

```js
/* 
  HMR: hot module replacement 热模块替换/ 模块热替换
    作用： 一个模块发生变化，只会重新打包这一个模块（而不是所有模块）
    极大提升构建速度

    样式文件： 可以使用 HMR 功能，因为style-loader内部实现了~
    js文件： 默认不能使用 HMR 功能  ---》  修改 js代码，添加支持 HMR  的代码
    	if(module.hot){
          // 一旦module上有hot说明开启了HMR功能
          module.hot.accept('./print.js', function(){
            // 方法会监听print.js文件的变化，一旦发生变化，其他模块不会重新打包构建。
            // 会执行后面的回调函数
            console.log('printjs发生了热模块');
            // print();
          })
        }
        注意： HMR 功能对js处理只能处理非入口的js文件
    html文件： 默认不能使用 HMR 功能，同时会导致问题： html文件不能热更新了~ （不用做 HMR 功能）
      解决： 修改entry入口，将html文件引入
*/
const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: ['./src/js/index.js', './src/index.html'],
  output: {
    filename: 'js/built.js',
    path: resolve(__dirname, 'build')
  },
  module: {rules:[]},
  plugins:[
    new HtmlWebpackPlugin({
      template: './src/index.html'
    })
  ],
  devServer: {
    static: resolve(__dirname, 'src'),  // 入口文件
    compress: true,  // 压缩  gip
    port: 7000,   // 端口号
    open: true,    // 自动打开
    // 开启 HMR功能，当修改了webpack的配置之后，新配置要想生效，必须重启webpack服务
    hot: true
  },
  mode: 'development'
}
```



## 2、source-map

```js

const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: ['./src/js/index.js', './src/index.html'],
  output: {
    filename: 'js/built.js',
    path: resolve(__dirname, 'build')
  },
  mode: 'development',
  devtool: 'cheap-module-source-map'
}


/* 
  source-map： 一种提供源代码到构建后代码映射技术（如果后代码，通过映射可以追踪源代码错误）

    [inline-|hidden-|eval-][nosources-][cheap-[module-]]source-map

    source-map ： 外部
      - 错误代码准确信息 和 源代码的错误位置
    ------ 上下他们两个基本一致，只不过一个内联，一个外部 ------
    inline-source-map  : 内联   只生成一个内联 source-map     
      - 错误代码准确信息 和 源代码的错误位置

    hidden-source-map  ：外部
      - 错误代码错误原因，但是没有错误位置
      - 不能追踪源代码错误，只能提示到构建后代码的错误位置

    eval-source-map  ： 内联   每一个文件都生成对应的source-map，都在eval
      - 错误代码准确信息 和 源代码的错误位置 
      - 区别是：  这个在代码的每行多了一个哈希值，相当于是唯一标识符

    nosources-source-map ： 外部
      - 错误代码准确信息，但是没有任何源代码信息

    cheap-source-map： 外部
      - 错误代码准确信息 和 源代码的错误位置
      - 只能精确到行
    cheap-module-source-map : 外部
      - 错误代码准确信息 和 源代码的错误位置
      - 只精确到行
      - module会将loader的source map 加入

    内联 和 外部 的区别：  1、 外部生成了文件，内联没有  2、 内联构建速度更快

    开发环境： 速度快， 调试更友好
      速度快（eval > inline > cheap > ...）
        eval-cheap-source-map
        eval-source-map
      调试更友好
        source-map
        cheap-module-source-map
        cheap-source-map

      --->  eval-source-map  /  eval-cheap-module-source-map

    生产环境： 源代码要不要隐藏？ 调试要不要更友好
      内联会让代码体积变大，所以在生产环境不用内联
      nosources-source-map  全部隐藏
      hidden-source-map   只隐藏源代码，会提示构建够的代码错误

      --->  source-map / cheap-module-source-map
*/

```



## 3、oneOf

```js

const {resolve} = require('path');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');
const ESLintWebpackPlugin = require('eslint-webpack-plugin');

// 定义nodejs环境变量： 决定使用browserlist的哪个环境
process.env.NODE_ENV = 'production';

const commonCssLoader = [
  MiniCssExtractPlugin.loader,
  'css-loader',
  {
    // 兼容性处理css ，还需要在 package.json中定义browserlist
    loader: 'postcss-loader',
    options: {
      postcssOptions: {
        plugins: [require('postcss-preset-env')()]
      }
    }
  }
]

module.exports = {
  entry: './src/js/index.js',
  output: {
    filename: 'js/built.js',
    path: resolve(__dirname, 'build')
  },
  module: {
    rules:[
      {
        oneOf: [
          {
            // 提取css文件成为单独的文件
            // 注意： 不能有两个配置处理同一个类型的文件
            test: /\.css$/,
            use: [...commonCssLoader]
          },
          {
            // 提取css文件成为单独的文件
            test: /\.less$/,
            use: [...commonCssLoader,'less-loader']
          },
          /* 
            正常来讲，一个文件只能被一个loader处理
            当一个文件被两个loader处理的时候，一定要指定loader执行先后顺序
          */
          {
            test: /\.js$/,
            exclude: /node_modules/,
            loader: 'babel-loader',
            options: {
              // 预设
              presets: [
                [
                  '@babel/preset-env',
                  {
                    // 按需下载
                    useBuiltIns: 'usage',
                    // 指定corejs的版本
                    corejs: {version: 3},
                    targets: {
                      chrome: '60',
                      firefox: '60',
                      ie: '9',
                      safari: '10',
                      edge: '17'
                    }
                  }
                ]
              ]
            }
          },
          {
            test: /\.(jpg|png|gif)$/,
            parser: {
              dataUrlCondition: {maxSize: 8 * 1024}
            },
            generator: {
              filename: 'img/[name].[hash:8][ext]'
            }
          },
          {
            test: /\.html$/,
            loader: 'html-loader'
          },
          {
            test: /\.(js|css|less|html|jpg|png|gif)$/,
            generator: {
              filename: 'medias/[hash:10][ext]'
            }
          }
        ]
      }
    ]
  },
  mode: "production"
}
```



## 4、缓存（babel缓存）

```js

const {resolve} = require('path');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');
const ESLintWebpackPlugin = require('eslint-webpack-plugin');

/* 
  缓存： 
    babel缓存
      - cacheDirectory: true
      ---> 让第二次打包速度更快
    文件资源缓存
      - hash: 每次webpack构建打包时会生成一个唯一的一个hash值
        问题： 因为js和css同时使用一个hash值，
          如果重新打包，会导致所有缓存失效（可能我只改动一个文件）
    chunkhash： 根据chunk生成的hash值，如果打包来源于同一个chunk，那么hash值就一样
      问题： js 和 css 的hash值还是一样的
        因为css是在js中被引入的，所以属于同一个chunk
    contenthash: 根据文件的内容生成hash值。不同文件hash值一定不一样
      -----》 让代码上线运行缓存更好使用
  */


// 定义nodejs环境变量： 决定使用browserlist的哪个环境
process.env.NODE_ENV = 'production';

const commonCssLoader = [
  MiniCssExtractPlugin.loader,
  'css-loader',
  {
    // 兼容性处理css ，还需要在 package.json中定义browserlist
    loader: 'postcss-loader',
    options: {
      postcssOptions: {
        plugins: [require('postcss-preset-env')()]
      }
    }
  }
]

module.exports = {
  entry: './src/js/index.js',
  output: {
    filename: 'js/built.[contenthash:10].js',
    path: resolve(__dirname, 'build')
  },
  module: {
    rules:[
      {
        oneOf: [
          {
            // 提取css文件成为单独的文件
            // 注意： 不能有两个配置处理同一个类型的文件
            test: /\.css$/,
            use: [...commonCssLoader]
          },
          {
            // 提取css文件成为单独的文件
            test: /\.less$/,
            use: [...commonCssLoader,'less-loader']
          },
          /* 
            正常来讲，一个文件只能被一个loader处理
            当一个文件被两个loader处理的时候，一定要指定loader执行先后顺序
          */
          {
            test: /\.js$/,
            exclude: /node_modules/,
            loader: 'babel-loader',
            options: {
              // 预设
              presets: [
                [
                  '@babel/preset-env',
                  {
                    // 按需下载
                    useBuiltIns: 'usage',
                    // 指定corejs的版本
                    corejs: {version: 3},
                    targets: {
                      chrome: '60',
                      firefox: '60',
                      ie: '9',
                      safari: '10',
                      edge: '17'
                    }
                  }
                ]
              ],
              // 开启babel缓存
              // 第二次构建时，会读取之前的缓存
              cacheDirectory: true
            }
          }
        ]
      }
    ]
  },
  mode: "production",
  devtool: 'source-map'
}
```



## 5、tree shaking （树摇）

```js
/* 
  tree shaking: 去除无用代码
    前提 1：必须使用ES6模块化  2：开启production环境

  在package.json中配置
    "sideEffects": fasle  所有代码都没有副作用（都可以进行tree shaking）
      问题：可能会把  css / @babel/polyfill （副作用）文件干掉
    "sideEffects": ["*.css", "*.less"] : 就会忽略css文件【这些写入的文件就不会进行 tree shaking】
*/

"browserslist": {
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ],
    "production": [
      ">0.2%",
      "not dead",
      "not op_mini all"
    ]
  },
  "eslintConfig": {
    "extends": "airbnb-base",
    "env": {
      "browser": true
    }
  },
  "sideEffects": [
    "*.css"
  ]

```



## 6、code split

```js
const {resolve} = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

// 定义nodejs环境变量： 决定使用browserlist的哪个环境
process.env.NODE_ENV = 'production';

module.exports = {
  entry: './src/js/index.js',
  // entry:{
  //   index: './src/js/index.js',
  //   test: './src/js/test.js'
  // },
  output: {
    filename: 'js/[name].[contenthash:10].js',
    path: resolve(__dirname, 'build')
  },
  /* 
    1. 可以将node_modules中代码单独打包一个chunk最终输出
    2. 自动分析多入口chunk中，有没有公共的文件。如果有会打包成单独的一个chunk
  */
  optimization: {
    splitChunks: {
      chunks: 'all'
    }
  },
  mode: "production"
}



-----------index.js-------------

function sum(...args) {
  return args.reduce((pre, cur) => pre + cur, 0);
}

/* 
  ES10 语法 import('文件路径').then(callback()).catch(callback())
  通过js代码，让某个文件被单独打包成一个chunk
  /* webpackChunkName: 'test' * /用来给打包的文件固定的名字
*/
import (/* webpackChunkName: 'test' */'./test')
  .then((result) => {
    // eslint-disable-next-line
    console.log(result);
  })
  .catch(() => {
    // eslint-disable-next-line
    console.log('文件加载失败');
  })

// eslint-disable-next-line
// console.log(mul);

// eslint-disable-next-line
console.log(sum(1, 2, 3, 4, 5, 6));

```



## 7、lazy loading

```js
const {resolve} = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

// 定义nodejs环境变量： 决定使用browserlist的哪个环境
process.env.NODE_ENV = 'production';

module.exports = {
  entry: './src/js/index.js',
  // entry:{
  //   index: './src/js/index.js',
  //   test: './src/js/test.js'
  // },
  output: {
    filename: 'js/[name].[contenthash:10].js',
    path: resolve(__dirname, 'build')
  },
  /* 
    1. 可以将node_modules中代码单独打包一个chunk最终输出
    2. 自动分析多入口chunk中，有没有公共的文件。如果有会打包成单独的一个chunk
  */
  optimization: {
    splitChunks: {
      chunks: 'all'
    }
  },
  mode: "production"
}




--------------index.js----------------
console.log('index.js文件被加载~');

// import {mul} from './test';

// console.log(mul);

document.getElementById('btn').onclick = function (){
  //   懒加载:用的时候再加载          预加载:会在使用之前就被加载
  //  正常加载可以认为是并行加载（同一时间加载多个文件）
  //  预加载 prefetch: 等其他资源加载完毕，浏览器空闲了，再偷偷加载资源
  import(/* webpackChunkName: 'test', webpackPreFetch: true */'./test').then(({mul}) => {
    console.log(mul(3, 4));
  })
};


```



## 8、PWA （渐进式网络开发应用程序--离线可访问）

```js
const {resolve} = require('path');
const WorkboxWebpackPlugin = require('workbox-webpack-plugin');

/* 
  PWA: 渐进式网络开发应用程序（离线可访问）
    workbox --->  workbox-webpack-plugin
*/

// 定义nodejs环境变量： 决定使用browserlist的哪个环境
process.env.NODE_ENV = 'production';

module.exports = {
  entry: './src/js/index.js',
  output: {
    filename: 'js/built.[contenthash:10].js',
    path: resolve(__dirname, 'build')
  },
  module: {
    rules:[
      {
        oneOf: [
          /* 
            正常来讲，一个文件只能被一个loader处理
            当一个文件被两个loader处理的时候，一定要指定loader执行先后顺序
          */
          {
            test: /\.js$/,
            exclude: /node_modules/,
            loader: 'babel-loader',
            options: {
              // 预设
              presets: [
                [
                  '@babel/preset-env',
                  {
                    // 按需下载
                    useBuiltIns: 'usage',
                    // 指定corejs的版本
                    corejs: {version: 3},
                    targets: {
                      chrome: '60',
                      firefox: '60',
                      ie: '9',
                      safari: '10',
                      edge: '17'
                    }
                  }
                ]
              ],
              // 开启babel缓存
              // 第二次构建时，会读取之前的缓存
              cacheDirectory: true
            }
          }
        ]
      }
    ]
  },
  plugins:[
    new WorkboxWebpackPlugin.GenerateSW({
      /* 
        1. 帮助serviceworker快速启动
        2. 删除旧的serviceworker

        生成一个 serviceworker配置文件~
      */
      clientsClaim: true,
      skipWaiting: true
    })
  ],
  mode: "production",
  devtool: 'source-map'
}
```



## 9、多线程打包

```js

const {resolve} = require('path');

module.exports = {
  entry: './src/js/index.js',
  output: {
    filename: 'js/built.[contenthash:10].js',
    path: resolve(__dirname, 'build')
  },
  module: {
    rules:[
      {
        oneOf: [
          {
            test: /\.js$/,
            exclude: /node_modules/,
            // loader: 'babel-loader',
            use:[
              /* 
                开启多进程打包
                进程启动大概 600ms， 进程通信也有开销。
                只有工作消耗时间比较长，才需要多进程打包
              */
              {
                loader: 'thread-loader', 
                options: {
                  workers: 2  // 进程2个
                }
              },
              {
                loader: 'babel-loader',
                options: {
                  // 预设
                  presets: [
                    [
                      '@babel/preset-env',
                      {
                        // 按需下载
                        useBuiltIns: 'usage',
                        // 指定corejs的版本
                        corejs: {version: 3},
                        targets: {
                          chrome: '60',
                          firefox: '60',
                          ie: '9',
                          safari: '10',
                          edge: '17'
                        }
                      }
                    ]
                  ],
                  // 开启babel缓存
                  // 第二次构建时，会读取之前的缓存
                  cacheDirectory: true
                }
              }
            ]
          }
        ]
      }
    ]
  },
  plugins:[
    new ESLintWebpackPlugin({
      extensions: ['js'],
      context: resolve('src'),
      exclude: '/node_modules',
      fix: true
    })
  ],
  mode: "production",
  devtool: 'source-map'
}
```



## 10、externals

```js
const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/js/index.js',
  output: {
    filename: 'js/built.js',
    path: resolve(__dirname, 'build')
  },
  module: {rules:[]},
  plugins:[
    new HtmlWebpackPlugin({
      template: './src/index.html'
    })
  ],
  mode: 'production',
  externals: {
    // 拒绝 jQuery被打包进来
    jquery: 'jQuery'
  }
}




-------------index.js--------------
import $ from 'jquery';
console.log($);

------------index.html------------
<body>
  <h1>hello html</h1>
  <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
</body>


```



## 11、dll

```js
webpack.config.js

const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const webpack = require('webpack')
const AddAssetHtmlWebpackPlugin = require('add-asset-html-webpack-plugin')

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'built.js',
    path: resolve(__dirname, 'build')
  },
  plugins:[
    new HtmlWebpackPlugin({
      template: './src/index.html'
    }),
    // 告诉webpack哪些库不参与打包，同时使用时的名称也得变~
    new webpack.DllReferencePlugin({
      manifest: resolve(__dirname, 'dll/manifest.json')
    }),
    // 将某个文件打包输出出去，并在html中自动引入该文件
    new AddAssetHtmlWebpackPlugin({
      outputPath: 'auto',
      filepath: resolve(__dirname, 'dll/jquery.js')
    })
  ],
  mode: 'production'
}


-----------------webpack.dll.js--------------------
/* 
  使用dll技术，对某些库（第三方的库：jquery、react、vue....）进行单独打包
    当你运行webpack 时，默认查找 webpack.config.js 配置文件
    需求： 需要运行 webpack.dll.js 文件
      --> webapck --config webpack.dll.js
*/
const {resolve} = require('path')
const webpack = require('webpack')

module.exports = {
  entry: {
    // 最终打包生成的[name] ---> jquery
    // ['jquery'] -->  要打包的库是jquery
    jquery: ['jquery']
  },
  output: {
    filename: '[name].js',
    path: resolve(__dirname, 'dll'),
    library: '[name]_[hash]' // 打包的库里面向外暴露出去的内容叫什么名字
  },
  plugins: [
    // 打包生成一个 manifest.json  --->  提供和jquery映射
    new webpack.DllPlugin({
      name: '[name]_[hash]',  // 映射库的暴露的内容名称
      path: resolve(__dirname, 'dll/manifest.json')  // 输出文件路径
    })
  ],
  mode: "production"
}


-------------index.js--------------------
import $ from 'jquery'
console.log($);


```





## 12、以上部分代码所用到的server.js【express服务器】

```js
/* 
  服务器代码
  启动服务器 : nodemon server.js  前提是全局安装了  nodemen    cnpm i nodemon -g
  或者  node server.js

  访问服务器地址： http://localhost:3000
*/
const express = require('express');

const app = express();

app.use(express.static('build', {maxAge: 1000 * 3600}));

app.listen(3000);

```



# webpack性能优化总结

- 开发环境性能优化
- 生产环境性能优化

## 开发环境性能优化

- 优化打包构建速度

  - HMR：一个模块改变，只会重新打包这个模块，而不是全部模块。
- 优化调试代码调试
  - source-map：方便调试

## 生产环境性能优化

- 优化打包构建速度
  - oneOf：默认情况下一个文件只会执行一个loader
  - babel缓存：更有利于js文件的打包处理
  - externals：让某些文件不打包，通过cdn进行引入
  - dll：让某些文件提前打包，用的时候直接引用
- 优化代码运行的性能
  - 缓存（hash-chunkhash-contenthash）：更改了的文件才会重新加载，其余没有改变则用缓存
  - tree shaking：前提：必须开启ES6模块化。只要开启production，那么tree shaking默认启动。sideEffect:['XXX']，可以出去这些代码，不去删除。
  - code split：将最终的整个js文件分割成多个小的js文件，并行加载，比加载一个大的文件更快
  - 懒加载/预加载：懒加载-》用的时候加载。预加载-》重要组件加载完毕剩余偷偷加载，更好点，但是兼容不好
  - PWA：离线可访问，网站更加友好，访问更加高效，兼容性不好



# webpack基本配置常用【包括默认选项】详解

## 1、entry

```js

const {resolve} = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

/* 
  entry: 入口文件
    1. string --> './src/index.js'
      - 单入口
      - 打包形成一个chunk。输出一个bundle[built.js]文件
      - 此时chunk的名称默认是 main
    2. array -->  ['./src/index.js', './src/add.js']
      - 多入口
      - 所有入口文件最终只会形成一个 chunk，输出出去只有一个 bundle文件。
      - 只有在HMR功能中让html热更新生效；['./src/js/index.js', './src/index.html']
    3. object -->  { index: './src/index.js', add: './src/add.js' }
      - 多入口
      - 有几个入口文件就形成几个chunk，输出几个bundle文件，此时chunk的名称就是key
      
    4. 特殊用法
      {
        // 所有入口文件最终只会形成一个 chunk，输出出去只有一个 bundle文件。
        index: ['./src/index.js', './src/count.js'],
        // 形成一个chunk。输出一个bundle文件
        add: './src/add.js'
      }

*/

module.exports = {
  entry: {
    index: ['./src/index.js', './src/count.js'],
    add: './src/add.js'
  },
  output: {
    filename: '[name].js',
    path: resolve(__dirname, 'build')
  },
  plugins: [new HtmlWebpackPlugin()],
  mode: 'development'
};

```



## 2、output

```js

const {resolve} = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    // 文件名称 （目录+指定名称）
    filename: 'js/[name].js',
    // 输出目录（将来所有资源输出的公共目录）
    path: resolve(__dirname, 'build'),
    // 所有资源引入的公共路径前缀  -->  'imgs/a.jpg'   ->  '/imgs/a.jpg'
    publicPath: '/',
    chunkFilename: 'js/[name]_chunk.js',  // 非入口chunk的名称
    // library: '[name]',  // 整个库向外暴露的变量名
    // libraryTarget: 'window', // 变量名添加到 browser 上
    // libraryTarget: 'global' // 变量名添加到 node -- 服务端  上
    // libraryTarget: 'commonjs' // 用 commonjs的方式暴露
  },
  plugins: [new HtmlWebpackPlugin()],
  mode: 'development'
};

```



## 3、module

```js

const {resolve} = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const EslintWebpackPlugin = require('eslint-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    // 文件名称 （目录+指定名称）
    filename: 'js/[name].js',
    // 输出目录（将来所有资源输出的公共目录）
    path: resolve(__dirname, 'build'),
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-laoder', 'css-loader']
      },
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel-loader',
        options: {
          // 预设：指示babel做怎么样的兼容性处理
          presets: [
            [
              '@babel/preset-env',
              {
                // 按需下载
                useBuiltIns: 'usage',
                // 指定corejs的版本
                corejs: {
                  version: 3
                },
                // 指定兼容性做到那个版本浏览器
                targets: {
                  chrome: '60',
                  firefox: '60',
                  ie: '9',
                  safari: '10',
                  edge: '17'
                }
              }
            ]
          ]
        }
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin(),
    new EslintWebpackPlugin({
      extensions: ['js'],
      context: resolve('src'),
      exclude: '/node_module',
      fix: true
    })
  ],
  mode: 'development'
};

```



## 4、resolve

```js

const {resolve} = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const EslintWebpackPlugin = require('eslint-webpack-plugin');

module.exports = {
  entry: './src/js/index.js',
  output: {
    // 文件名称 （目录+指定名称）
    filename: 'js/[name].js',
    // 输出目录（将来所有资源输出的公共目录）
    path: resolve(__dirname, 'build'),
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin()
  ],
  mode: 'development',
  // 解析模块的规则
  resolve: {
    // 配置路径别名： 优点：简写路径    缺点：路径没有提示
    alias: {
      $css: resolve(__dirname, 'src/css')
    },
    // 配置省略文件路径的后缀名
    extensions: ['.js', '.json', '.css'],
    modules: [resolve(__dirname, '../../node_modules'), 'node_modules']
  }
};

```



## 5、devServer

```js

const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'built.js',
    path: resolve(__dirname, 'build')
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          'style-loader',
          'css-loader'
        ]
      },
      {
        // 排除css js  html 资源
        exclude: /\.(css|js|html)$/,
        type: 'asset/resource'
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html'
    })
  ],
  mode: 'development',
  devServer: {
    static: resolve(__dirname, 'src'),
    // 监视 contentBase目录下的所有文件，一旦文件变化就会 reload
    watchContentBase: true, 
    watchOptions:{
      // 忽略文件
      ignored: /node_modules/
    },
    // 启动gzip压缩 --- 代码体积更小，速度更快  
    compress: true,
    // 端口号
    port: 7000,
    // 域名
    host: 'localhost',
    // 自动打开
    open: true,
    // 开启HMR功能
    hot: true,
    // 不要显示启动服务器日志信息
    clientLogLevel: 'none',
    // 除了一些基本启动信息以外，其他内容都不要显示
    quiet: true,
    // 如果出错，不要全屏提示
    overlay: false,
    // 服务器代理  -->  解决开发环境跨域问题
    proxy: {
      // 一旦 devServer(7000) 服务器接收到  /api/xxx 的请求，就会把请求转发到另一个服务器 (3000)
      '/api': {
        target: 'http://loacalhost:3000',
        // 发送请求时，请求路径重写：将 /api/xxx  -->  /xxx （去掉  / api）
        pathRewrite:{
          // 路径重写
          '^/api': ''
        }
      }
    }
  }
}

```



## 6、optimization

```js

const {resolve} = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const EslintWebpackPlugin = require('eslint-webpack-plugin');
// 用于压缩 js ，因为 4.26之后 uglify已经不维护了，现在用的terser
const TerserWebpackPlugin = require('terser-webpack-plugin');

module.exports = {
  entry: './src/js/index.js',
  output: {
    // 文件名称 （目录+指定名称）
    filename: 'js/[name].[contenthash:10].js',
    // 输出目录（将来所有资源输出的公共目录）
    path: resolve(__dirname, 'build'),
    chunkFilename: 'js/[name]_[contenthash:10]_chunk.js'
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      }
    ]
  },
  mode: 'production',
  /* 
    1. 可以将node_modules中代码单独打包一个chunk最终输出
    2. 自动分析多入口chunk中，有没有公共的文件。如果有会打包成单独的一个chunk
  */
  optimization: {
    splitChunks: {
      chunks: 'all',
      // 以下为默认值，可以不写
      /* miniSize: 30 * 1024, // 分割的chunk 最小为 30kb
      maxSize: 0, // 最大没有限制
      miniChunks: 1, // 要提取的chunk最少被引用1次
      maxAsyncRequests: 5, // 按需加载时并行加载的文件的最大数量
      maxInitialRequests: 3, // 入口js文件最大并行请求数量
      automaticNameDelimiter: '~', // 名称连接符
      name: true, // 可以使用命名规则
      cacheGroups: { // 分割chunk的组
        // node_modules文件会被打包的vendors 组的chunk中。  -->  vendors~xxx.js
        // 满足上面的公共规则，如大小超过30kb，引用超过1次
        vendors: {
          test: /[\\/]node_modules[\\/]/,
          // 打包优先级
          priority: -10
        },
        default: {
          // 要提取的chunk最少被引用2次
          minChunks: 2,
          // 优先级
          priority: -20,
          // 如果当前要打包的模块和之前已经被提取的模块是同一个就会复用，而不是重新打包模块
          reuseExistingChunk: true
        }
      } */
    },
    // 将当前模块记录其他模块的hash值，单独打包成一个 runtime 文件
    // 解决： 修改a文件导致b文件的contenthash变化 
    //  -【因为a文件修改之后，b文件引用了a文件，所以b文件中引用内容部分的contenthash变化，所以b文件也需要重新打包，所以导致b文件的缓存失效】
    runtimeChunk: {
      name: entrypoint => `runtime-${entrypoint.name}`
    },
    minimizer: [
      // 配置生产环境的压缩方案， js 和 css
      new TerserWebpackPlugin({
        // 开启缓存
        // cache: true,
        // 开启多进程打包
        parallel: true,
        // 启用source-map，否则 会被压缩掉
        // sourceMap: true
      })
    ]
  }
};

```



# webpack5

此版本重点关注以下内容:

- 通过持久缓存提高构建性能.
- 使用更好的算法和默认值来改善长期缓存.
- 通过更好的树摇和代码生成来改善捆绑包大小.
- 清除处于怪异状态的内部结构，同时在 v4 中实现功能而不引入任何重大更改.
- 通过引入重大更改来为将来的功能做准备，以使我们能够尽可能长时间地使用 v5.

## 下载

- npm i webpack@next webpack-cli -D

## 自动删除 Node.js Polyfills

早期，webpack 的目标是允许在浏览器中运行大多数 node.js 模块，但是模块格局发生了变化，许多模块用途现在主要是为前端目的而编写的。webpack <= 4 附带了许多 node.js 核心模块的 polyfill，一旦模块使用任何核心模块（即 crypto 模块），这些模块就会自动应用。

尽管这使使用为 node.js 编写的模块变得容易，但它会将这些巨大的 polyfill 添加到包中。在许多情况下，这些 polyfill 是不必要的。

webpack 5 会自动停止填充这些核心模块，并专注于与前端兼容的模块。

迁移：

- 尽可能尝试使用与前端兼容的模块。
- 可以为 node.js 核心模块手动添加一个 polyfill。错误消息将提示如何实现该目标。

## Chunk 和模块 ID

添加了用于长期缓存的新算法。在生产模式下默认情况下启用这些功能。

`chunkIds: "deterministic", moduleIds: "deterministic"`

## Chunk ID

你可以不用使用 `import(/* webpackChunkName: "name" */ "module")` 在开发环境来为 chunk 命名，生产环境还是有必要的

webpack 内部有 chunk 命名规则，不再是以 id(0, 1, 2)命名了

## Tree Shaking

1. webpack 现在能够处理对嵌套模块的 tree shaking

```js
// inner.js
export const a = 1;
export const b = 2;

// module.js
import * as inner from './inner';
export { inner };

// user.js
import * as module from './module';
console.log(module.inner.a);
```

在生产环境中, inner 模块暴露的 `b` 会被删除

2. webpack 现在能够多个模块之前的关系

```js
import { something } from './something';

function usingSomething() {
  return something;
}

export function test() {
  return usingSomething();
}
```

当设置了`"sideEffects": false`时，一旦发现`test`方法没有使用，不但删除`test`，还会删除`"./something"`

3. webpack 现在能处理对 Commonjs 的 tree shaking

## Output

webpack 4 默认只能输出 ES5 代码

webpack 5 开始新增一个属性 output.ecmaVersion, 可以生成 ES5 和 ES6 / ES2015 代码.

如：`output.ecmaVersion: 2015`

## SplitChunk

```js
// webpack4
minSize: 30000;
```

```js
// webpack5
minSize: {
  javascript: 30000,
  style: 50000,
}
```

## Caching

```js
// 配置缓存
cache: {
  // 磁盘存储
  type: "filesystem",
  buildDependencies: {
    // 当配置修改时，缓存失效
    config: [__filename]
  }
}
```

缓存将存储到 `node_modules/.cache/webpack`

## 监视输出文件

之前 webpack 总是在第一次构建时输出全部文件，但是监视重新构建时会只更新修改的文件。

此次更新在第一次构建时会找到输出文件看是否有变化，从而决定要不要输出全部文件。

## 默认值

- `entry: "./src/index.js`
- `output.path: path.resolve(__dirname, "dist")`
- `output.filename: "[name].js"`

## 更多内容

https://github.com/webpack/changelog-v5