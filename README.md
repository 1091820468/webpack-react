# webpack-react
使用webpack搭建react项目
如果你的代码不需要模块化，那么你不需要 webpack；如果你的代码需要模块化，那么你可能需要 webpack；如果你的代码里，
JavaScript、图片、CSS、JSON 等等千奇百怪的文件都要模块化，那么一定需要 webpack。

为什么选择 webpack
webpack 官网 是这样定义 webpack 的：

    webpack is a module bundler

什么是 module？我们首先会想到 JavaScript 的 ES 模块、AMD 模块，又或 Node.js 下的 CommonJS 模块。

只不过在 webpack 下，所有类型的文件都可以是模块，包括 JavaScript、CSS、图片、JSON。

我们当然知道在 JavaScript 里 import/require 一张图片会报错。但在 webpack 下，这是允许的，这归功于加载器（loader）。通过加载器，webpack 将 JavaScript 的模块化推广到其它类型文件 - 这正是 webpack 跨出的与众不同的一步 - 但也导致它配置繁多，广受诟病。

但明白 webpack 这一用意，我们就掌握了 webpack 核心，接下来的事，查查文档基本都能搞定。

React.js 项目
我在这里草拟了一个简单的单页面应用，页面上有一张图片，点击图片，图片会旋转，再次点击则恢复原来的位置。

接下来我们将从零开始，用 webpack 搭建一个 React.js 开发环境，并完成编码、打包、部署。

初始化项目
首先，我们来初始化项目：
    
      npm init -y

项目初始化完成后，webpack-demo 目录下多出 package.json 文件，内容如下：

    {
      "name": "webpack-demo",
      "version": "1.0.0",
      "description": "",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "keywords": [],
      "author": "",
      "license": "ISC"
    }
    
新建 HTML
在 webpack-demo 下新建 index.html：

    <!DOCTYPE html>
    <html lang="en">
    <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <meta http-equiv="X-UA-Compatible" content="ie=edge">
      <title>webpack 教程</title>
    </head>
    <body>

    </body>
    </html>
    
我们暂时还没有引用 JavaScript。

创建 JavaScript 文件
在 webpack-demo 目录下新建 index.js，内容暂时留空。

我们需要安装 react 及 react-dom：

    npm i react react-dom
    
装好后，在 index.js 文件中 import 它们：

    import React from 'react'
    import ReactDOM from 'react-dom'
    
好了，现在在 index.html 中引用 index.js：

   <body>
     <script src="index.js"></script>
   </body>
   
现在在浏览器中打开 index.html，，console 中报错了
浏览器的兼容性问题几乎是与生俱来的 - 要怎么抹平它们的差别，好让代码能够运行在尽可能多的浏览器平台上？

需要用 webpack 来构建我们的源代码。

安装 webpack
在 webpack-demo 项目根目录中安装 webpack：

    npm install -D webpack
    
安装完成后，我们就会看到当前安装的 webpack 版本号。

编译 JavaScript
webpack 是个打包工具，那么就需要一个输入（入口文件），一个输出（目标文件）。

我们试试在命令行下将 index.js 打包成 build.min.js：

    npx webpack index.js -o build.min.js
    
    The CLI moved into a separate package: webpack-cli
    Would you like to install webpack-cli? (That will run npm install -D webpack-cli) (yes/NO)
    
没有打包，反而是提示我们是否安装 webpack-cli。这是因为 webpack 在 webpack 4 里将命令行相关的都迁移至 webpack-cli 包。

输入 yes 然后回车，稍等一会儿，webpack-cli 就安装好了。之后我们的命令继续执行，结果如下：

    Hash: cbb2732def750315c477
    Version: webpack 4.5.0
    Time: 243ms
    Built at: 2018-4-7 09:56:02
           Asset     Size  Chunks             Chunk Names
    build.min.js  105 KiB       0  [emitted]  main
    Entrypoint main = build.min.js
      [13] ./index.js 58 bytes {0} [built]
        + 13 hidden modules

    WARNING in configuration
    The 'mode' option has not been set, webpack will fallback to 'production' for this value. Set 'mode' option to 'development' or 'production' to enable defaults for each environment.
    You can also set it to 'none' to disable any default behavior. Learn more: https://webpack.js.org/concepts/mode/

打包成功，但出现黄色警告。这是 webpack 4 引入的模式，包括 development、production、none 三个值，我们不传入值的话，默认使用 production。

我们来调整下命令：

   npx webpack index.js -o build.min.js --mode development

不再出现黄色警告了。
如果，不指定输入、输出的话会怎样？

    npx webpack --mode development
    
    Hash: 86d52d22c7b7982a7ebf
    Version: webpack 4.5.0
    Time: 41ms
    Built at: 2018-4-7 10:00:39

    ERROR in Entry module not found: Error: Can't resolve './src' in '/Users/sam/tmp/webpack-demo'
    
报错，说在 ./src 下找不到 index.js 文件 - 这也是 webpack 4 引入的约定，不指定输入文件的话，则默认为 src/index.js。
我们按照约定将项目根目录下的 index.js 移动到 src/index.js：

然后再打包一遍：

    npx webpack --mode development
    
    Hash: 4726bc2e160759010056
    Version: webpack 4.5.0
    Time: 321ms
    Built at: 2018-4-7 10:03:24
      Asset     Size  Chunks             Chunk Names
    main.js  688 KiB    main  [emitted]  main
    Entrypoint main = main.js
    [./src/index.js] 58 bytes {main} [built]
        + 21 hidden modules
        
打包成功，且目录下多出 dist/main.js - 这也正是 webpack 4 未指定输出文件时默认的位置。也就是说，我们按照 webpack 约定组织代码文件的话，可以省去许多配置。

打包完成后，我们将 index.html 中对 JavaScript 引用调整为编译后的 dist/main.js：

   <body>
    <script src="dist/main.js"></script>
   </body>
   
再刷新浏览器，控制台已经不再报错。

但这样的代码结构在部署时十分不便，我们除了要拷贝 dist 目录外，还要从众多文件中拷贝出 index.html 文件，从部署上说，只需拷贝一个目录是最方便的，
因此将 index.html 也移入 dist 目录，顺便清理 build.min.js 文件：

然后再调整 index.html 中的 js 引用：

     <body>
      <script src="main.js"></script>
     </body>
     
！！！实时刷新问题
在 index.html 文件中引用 main.js 文件后，我们有几个问题需要解决。

入口文件 src/index.js 的变化，包括它所引用的其它模块的变化如何通知给 webpack，以便重新构建 dist/main.js 文件？
dist/main.js 文件更新后，浏览器中打开的页面该如何自动刷新、监控文件

第一个问题，webpack 有好几个解决办法，其中 watch 选项最直观，我们可以让 webpack 监控文件变化，一旦文件有变化，就重新构建。但默认情况下，watch 选项是禁用的。

我们可以在命令行下启用它：

    npx webpack --mode development --watch

    Webpack is watching the files…

    Hash: 4726bc2e160759010056
    Version: webpack 4.5.0
    Time: 367ms
    Built at: 2018-4-7 10:12:59
      Asset     Size  Chunks             Chunk Names
    main.js  688 KiB    main  [emitted]  main
    Entrypoint main = main.js
    [./src/index.js] 58 bytes {main} [built]
        + 21 hidden modules
        
我们试试在 index.js 文件中添加一行 console.log('hello webpack')，保存文件后就会看到命令行下的变化：

    Hash: 0d285ea4d1c26ac17380
    Version: webpack 4.5.0
    Time: 19ms
    Built at: 2018-4-7 10:13:35
      Asset     Size  Chunks             Chunk Names
    main.js  688 KiB    main  [emitted]  main
    Entrypoint main = main.js
    [./src/index.js] 87 bytes {main} [built]
        + 21 hidden modules
        
webpack 监控到 src/index.js 文件的变化，重新构建了 dist/main.js，耗时 19ms。

刷新浏览器

至于自动刷新浏览器的问题，webpack 提供 webpack-dev-server 来解决，它是一个基于 expressjs 的开发服务器，提供实时刷新浏览器页面的功能
首先在项目下安装 webpack-dev-server：

     npm install -D webpack-dev-server
     
安装完成后在命令行下执行 webpack-dev-server：

     npx webpack-dev-server --mode development --content-base ./dist

我们现在可以在 http://localhost:8080/ 访问我们的 index.html。

在入口文件 src/index.js 里再添加一行代码验证下浏览器页面的实时刷新功能：

    console.log('webpack live reload is working')

webpack 重新打包了 dist/main.js，浏览器中打开的页面同时也刷新了。实际上，我们看 webpack-dev-server 的命令行会发现，
它同样监控入口文件的变化并重新编译 - 换句话说，webpack-dev-server 已经启用 --watch 效果。

编码


我们在 src/index.js 中先写个简单的 React 代码：

  import React from 'react'
  import ReactDOM from 'react-dom'
  ReactDOM.render(<div>hello webpack</div>, document.body)

查看 webpack-dev-server 的状态：

    [./src/index.js] 255 bytes {main} [built] [failed][1 error]
        + 25 hidden modules

    ERROR in ./src/index.js
    Module parse failed: Unexpected token (3:16)
    You may need an appropriate loader to handle this file type.
    | import React from 'react'
    | import ReactDOM from 'react-dom'
    | ReactDOM.render(<div>hello webpack</div>, document.body)
    |
     @ multi (webpack)-dev-server/client?http://localhost:8080 ./src
    ｢wdm｣: Failed to compile.
    
报错。为什么？因为我们写了 JSX 语法，webpack 不认识。需要babel-loader 来翻译。

     npm install -D "babel-loader@^8.0.0-beta" @babel/core @babel/preset-react
     
接着重启 webpack-dev-server：

    npx webpack-dev-server --mode development --content-base ./dist --module-bind 'js=babel-loader?presets[]=@babel/preset-react'

不再报错，且页面上已经显示 hello webpack。

webpack 配置文件

随着我们配置的插件越来越多，命令行会越来越长，这时，我们可以将命令行参数移入 webpack 配置文件。

我们来新建一个 webpack.config.js 文件：

    const path = require('path')
    module.exports = {
      mode: 'development',
      devServer: {
        contentBase: path.resolve(__dirname, 'dist')
      },
      module: {
        rules: [
          {
            test: /\.js$/,
            include: [
              path.resolve(__dirname, 'src')
            ],
            loader: 'babel-loader',
            options: {
              presets: ['@babel/preset-react']
            }
          }
        ]
      }
    }
    
这样，我们只要执行 npx webpack-dev-server 就行，不再需要输入一长串命令行。

图片加载器
我们需要一张图片，放到 src/img/rose.jpg 位置。

我们在 src/index.js 中 import 它：

   import ReactDOM from 'react-dom'
   import Rose from './img/rose.jpg'
   ReactDOM.render(<div>hello webpack</div>, document.body)
   
可以看到命令行报错了：

    [./src/img/rose.jpg] 177 bytes {main} [built] [failed] [1 error]
    [./src/index.js] 178 bytes {main} [built]
        + 46 hidden modules

    ERROR in ./src/img/rose.jpg
    Module parse failed: Unexpected character '�' (1:0)
    You may need an appropriate loader to handle this file type.
    (Source code omitted for this binary file)
     @ ./src/index.js 3:0-34
     @ multi (webpack)-dev-server/client?http://localhost:8080 ./src
    ℹ ｢wdm｣: Failed to compile.
    
和处理 JSX 一个道理，我们需要翻译（即加载器）。

在 webpack 里，负责图片翻译的是 file-loader：

   npm install -D file-loader
   
接着是配置 webpack.config.js：

        {
         test: /\.(png|jpg|gif)$/,
         use: [
            {
              loader: 'file-loader',
              options: {}
            }
          ]
         }
       ]
       
重启 webpack-dev-server，发现 webpack 已经能正常编译了 - 图片摇身一变，也是一个模块。

而且，webpack 在最终构建时，会自动将模块中引用的图片拷贝到相应目录。

确保图片加载没问题后，我们来完善下代码：

      class App extends React.Component {
        render () {
          return (
            <div><img src={Rose} alt='hah' /></div>
          )
        }
      }
      ReactDOM.render(<App />, document.body)
      
查看浏览器，图片已经渲染出来 - 但是图片太大了。我们需要 CSS 来控制图片尺寸。

加载 CSS 文件
在 React.js 里，CSS 有很多种写法，比如我们可以直接写在 style 中：

  <img src={Rose} alt='hah' style={{maxWidth: 500}} /> 
  
因为这就是 JavaScript，我们也就不需要额外处理。

但我们也可以写在 css 文件中。

在 src 下新增 index.css：

  .flower {
    max-width: 500px;
  }
  
然后在 index.js 中引入并应用：

     import React from 'react'
     import ReactDOM from 'react-dom'
     import Rose from './img/rose.jpg'
     import './index.css'
     class App extends React.Component {
       render () {
         return (

            <div><img src={Rose} alt='玫瑰' className='flower' /></div>
         )
       }
     }
     
但 webpack-dev-server 又报错了：

    [./src/index.css] 166 bytes {main} [built] [failed] [1 error]
    [./src/index.js] 391 bytes {main} [built]
        + 47 hidden modules

    ERROR in ./src/index.css
    Module parse failed: Unexpected token (1:0)
    You may need an appropriate loader to handle this file type.
    | .flower {
    |   max-width: 500px;
    | }
     @ ./src/index.js 4:0-21
     @ multi (webpack)-dev-server/client?http://localhost:8080 ./src
    ℹ ｢wdm｣: Failed to compile.
    
同样的，我们需要 CSS 加载器：

css-loader - 预处理 CSS 文件
style-loader - 将 CSS 插入到 DOM 中的 style 标签
注意，我们如果只使用了 css-loader，则 webpack 只是将 CSS 文件预处理成模块然后打包到构建文件中，并不会插入到页面 - 这是 style-loader 的作用。

我们先来安装它们：

    npm install -D css-loader style-loader

然后修改 webpack.config.js 配置：

      {
        test: /\.css$/,
        use: [{ loader: 'style-loader' }, { loader: 'css-loader' }]
       }
       
请注意，loader 的顺序很重要，比如上面新增的这一节，如果把 style-loader 放到 css-loader 后面，我们就会撞见错误：
因为 style-loader 无法理解 CSS 文件，需要先经 css-loader 预处理 - 是的，加载器的执行顺序是从后往前的。

重启 webpack-dev-server，编译正常，css 已生效。

在成功配置图片与 CSS 后，我们可以继续完善代码：

src/index.css：

  .flower--rotate {
    transform: rotate(30deg);
   }
   
src/index.js：

     class App extends React.Component {
      state = {
        reset: 'yes'
      }
      onClick = () => {
        this.setState({
          reset: this.state.reset === 'yes' ? 'no' : 'yes'
        })
      }
       render () {
         return (
          <div><img src={Rose} alt='玫瑰' className={this.state.reset === 'yes' ? 'flower' : 'flower flower--rotate'} onClick={this.onClick} /></div>
         )
       }
     }
     
但随即发现 webpack-dev-server 又抛出错误：
这是因为我们用了 JavaScript 的新特性 - 需要 @babel/plugin-proposal-class-properties 插件的支持。
首先是安装该插件：

     npm i -D @babel/plugin-proposal-class-properties
     
然后调整 webpack.config.js 配置：

           options: {
             presets: ['@babel/preset-react']
              plugins: ['@babel/plugin-proposal-class-properties']
           }
         },
         
重启 webpack-dev-server，编译正常。

查看浏览器，图片已经可以点击了。

打包
在完成项目开发后，我们需要输出文件给生产环境部署，只要执行：

    npx webpack --mode production
    
就可以打包出所有静态资源。

部署
部署时，拷贝 dist 目录即可。

清理 dist
随着某些文件的增删，我们的 dist 目录下会产生一些不再使用的文件，我们不想这些文件也部署到生产环境上占用空间，所以 webpack 在打包前最好能删除 dist 目录。

我们来试试 clean-webpack-plugin。

首先是安装：

    npm i -D clean-webpack-plugin
    
然后在 webpack.config.js 中调用：

     const path = require('path')
     const CleanWebpackPlugin = require('clean-webpack-plugin')
     module.exports = {
       mode: 'development',
       devServer: {
         contentBase: path.resolve(__dirname, 'dist')
       },
      plugins: [
        new CleanWebpackPlugin(['dist'])
      ],
       
再执行 npx webpack --mode production，webpack 确实会在打包前清空 dist 目录，但我们的 index.html 也一起被清空了。

html-webpack-plugin
我们使用 html-webpack-plugin 来自动生成 index.html。

首先是安装：

   npm i --save-dev html-webpack-plugin
   
调整 webpack.config.js：

   const CleanWebpackPlugin = require('clean-webpack-plugin')
   const HtmlWebpackPlugin = require('html-webpack-plugin')
   module.exports = {
     mode: 'development',
     devServer: {
       contentBase: path.resolve(__dirname, 'dist')
     },
     plugins: [
        new CleanWebpackPlugin(['dist']),
        new HtmlWebpackPlugin()
     ],
     
再运行 npx webpack --mode production，dist 下已经自动生成 index.html，再 title 却是 Webpack App，我们需要再调整一下 webpack.config.js：

     plugins: [
        new CleanWebpackPlugin(['dist']),
        new HtmlWebpackPlugin({
          title: 'webpack 教程'
        })
     ],
