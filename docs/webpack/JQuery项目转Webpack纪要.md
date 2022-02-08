---
title: JQuery项目转Webpack纪要
date: 2022-02-08 14:36:08
permalink: /pages/c7379f/
categories:
  - webpack
tags:
  - 
---
# Webpack

## [基本安装](https://webpack.docschina.org/guides/getting-started/)

```
mkdir webpack-demo 	//创建项目目录
cd webpack-demo		//进入项目目录
npm init -y			//初始化npm（创建package.json）
npm install webpack webpack-cli --save-dev	//安装webpack-cli
yarn add webpack webpack-cli --dev	//安装webpack-cli或者用这句
```

## 安装

首先，创建一个目录`webpack-tutorial`，相关命令如下：

```
mkdir webpack-tutorial
cd webpack-tutorial
npm init -y  // 创建默认的 package.json
```

安装`webpack`和`webpack-cli`：

```
npm i -D webpack webpack-cli
```

接着，创建目录 `src`，并在其里面创建 `index.js`，内容如下：

```
console.log('Interesting!')
```

## 基本配置

在项目的根目录中创建一个`webpack.config.js`。

#### Entry

`entry`是配置模块的入口，可抽象成输入，`Webpack` 执行构建的第一步将从入口开始搜寻及递归解析出所有入口依赖的模块。

`entry` 配置是必填的，若不填则将导致 Webpack 报错退出。这里，我们将`src/index.js`做为入口点。

```
const path = require('path')

module.exports = {
  entry: {
    main: path.resolve(__dirname, './src/index.js'),
  },
}
```

#### Output

配置 `output` 选项可以控制 webpack 如何向硬盘写入编译文件。注意，即使可以存在多个入口起点，但只指定一个`输出`配置。这里指定输出的路径为 'dist'：

```
module.exports = {
  /* ... */

  output: {
    path: path.resolve(__dirname, './dist'),
    filename: '[name].bundle.js',
  },
}
```

现在，我们具有构建捆绑包所需的最低配置。 在`package.json`中，我们可以创建一个运行`webpack`命令的构建脚本。

```
"scripts": {
  "build": "webpack"
}
```

现在可以运行它了:

```
npm run build
```

![clipboard.png](https://segmentfault.com/img/bVcHRV5)

现在在 dist 目录会生成一个 `main.bundle.js` 文件

## 插件

webpack有一个插件接口，这使得它更加灵活。内部webpack代码和第三方扩展使用插件，有一些主要的方法几乎每个webpack项目都会用到。

## HTML 模板文件

目前，我们有一个随机的bundle文件，但它对我们还不是很有用。如果需要使用`main.bundle.js`，就要借助 HTML页面来加载这个 JS 包作为脚本。我们希望HTML文件自动引入这个生成 `js` 文件，所以我们将使用`html-webpack-plugin`创建一个HTML模板。

安装一下：

```
npm i -D html-webpack-plugin
```

在`src`文件夹中创建一个`template.html`文件，这里，我们自定义一个`title`，内容如下：

**src/template.html**

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <title><%= htmlWebpackPlugin.options.title %></title>
  </head>

  <body>
    <div id="root"></div>
  </body>
</html>
```

创建配置的`plugins`属性，然后将插件，文件名添加到输出（`index.html`），并链接到将基于该模板的模板文件。

```
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  /* ... */

  plugins: [
    new HtmlWebpackPlugin({
      title: 'webpack Boilerplate',
      template: path.resolve(__dirname, './src/template.html'), // template file
      filename: 'index.html', // output file
    }),
  ],
}
```

现在再次运行构建，会看到`dist`文件夹现在包含一个`index.html`，里面也自动引入了我们打包好的 js 文件。用浏览器打开 `index.html`，会在控制台看到 `Interesting!`。

接着，在`index.js`中我们动态插入一些 dom 元素到页面中，内容如下：

```
// Create heading node
const heading = document.createElement('h1')
heading.textContent = 'Interesting!'

// Append heading node to the DOM
const app = document.querySelector('#root')
app.append(heading)
```

重新构建，在进入 dist 目录下面，用 `http-server` 运行 html 文件。

```
http-server
```

可以在页面上看到，我们注入的 `"Interesting!"`，还会注意到捆绑文件已缩小。

> **注意**:在安装`HtmlWebpackPlugin`后，你会看到一个`DeprecationWarning`，因为插件在升级到webpack 5后还没有完全摆脱`deprecation`警告。

## Clean

我们还需要设置`clean-webpack-plugin`,在每次构建后清除`dist`文件夹中的所有内容。 这对于确保不遗留任何旧数据很重要。

`clean-webpack-plugin`-删除/清理构建文件夹

```
const path = require('path')

const HtmlWebpackPlugin = require('html-webpack-plugin')
const {CleanWebpackPlugin} = require('clean-webpack-plugin')

module.exports = {
  /* ... */

  plugins: [
    /* ... */
    new CleanWebpackPlugin(),
  ],
}
```

## Modules and Loaders

webpack 使用 `loader` 预处理一些加载的文件，如 js 文件、静态资源(如图像和CSS样式)和编译器(如`TypeScript`和`Babel`)。webpack 5也有一些内置的资产加载器。

在我们的项目中，有一个HTML文件，该文件可以加载并引入一些 JS ，但实际上并没有执行任何操作。 那么这个`webpack`配置要做的主要事情是什么？

- 将 JS 编译为浏览器可以理解的版本
- 导入样式并将 `SCSS` 编译为 `CSS`
- 导入图像和字体
- （可选）设置React或Vue

## Babel (JavaScript)

`Babel`是一个工具，可让使用最新的 JS 语法。

建立一个规则来检查项目中（`node_modules`之外）的任何`.js`文件，并使用`babel-loader`进行转换。 Babel 还有一些其他的依赖项：

- `babel-loader`-使用 Babel 和 webpack 传输文件。
- `@babel/core`-将ES2015+ 转换为向后兼容的 JavaScript
- `@babel/preset-env`-Babel 的智能默认设置
- `@babel/plugin-proposal-class-properties`-自定义 Babel 配置的示例（直接在类上使用属性）

```
npm i -D babel-loader @babel/core @babel/preset-env @babel/preset-env @babel/plugin-proposal-class-properties
```

**webpack.config.js**

```
module.exports = {
  /* ... */

  module: {
    rules: [
      // JavaScript
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: ['babel-loader'],
      },
    ],
  },
}
```

> 如果是 TypeScript 项目，使用的是`typescript-loader`而不是`babel-loader`。

现在Babel已经设置好了，但是我们的`Babel`插件还没有。可以在`index.js`中添加一些新的语法来证明它还不能正常工作。

```
// 创建没有构造函数的类属性
class Game {
  name = 'Violin Charades'
}
const myGame = new Game()
// 创建 p 节点
const p = document.createElement('p')
p.textContent = `I like ${myGame.name}.`

const heading = document.createElement('h1')
heading.textContent = 'Interesting!'

const app = document.querySelector('#root')
app.append(heading, p)
```

![clipboard.png](https://segmentfault.com/img/bVcHSmw)

要解决这个问题，只需在项目的根目录中创建一个`.babelrc`文件。可以使用`preset-env`和`plugin-proposal-class-properties`添加更多默认值。

```
{
  "presets": ["@babel/preset-env"],
  "plugins": ["@babel/plugin-proposal-class-properties"]
}
```

现在运行`npm run build` 一切准备就绪。

## Images

假设我们需要引用一张图片并直接导入到 JS 文件中，这样是无法正常工作的。 为了演示，创建 `src/ images` 并向其中添加图像，然后尝试将其导入到`index.js`文件中。

**src/index.js**

```
import example from './images/example.png'

/* ... */
```

运行构建时，再次看到错误：

![clipboard.png](https://segmentfault.com/img/bVcHSoX)

`webpack`有一些内置的[asset modules](https://link.segmentfault.com/?url=https%3A%2F%2Fwebpack.js.org%2Fguides%2Fasset-modules%2F) ，可用于静态资源。 对于图像类型，我们将使用`asset/resource`,注意，这里是一个`type`，而不是`loader`。

**webpack.config.js**

```
module.exports = {
  /* ... */
  module: {
    rules: [
      // Images
      {
        test: /\.(?:ico|gif|png|jpg|jpeg)$/i,
        type: 'asset/resource',
      },
    ],
  },
}
```

构建后，可以在`dist`文件夹查看。

## 字体和内联

webpack 还有一`个asset module` ，可以使用`asset/inline`内联某些数据，例如`svgs`和字体。

**src/index.js**

```
import example from './images/example.svg'

/* ... */
```

**webpack.config.js**

```
module.exports = {
  /* ... */
  module: {
    rules: [
      // Fonts and SVGs
      {
        test: /\.(woff(2)?|eot|ttf|otf|svg|)$/,
        type: 'asset/inline',
      },
    ],
  },
}
```

## Styles

同样的需要使用 `style loader`才能在脚本中执行类似`import 'file.css'`的操作。

现在很多人都在使用[CSS-in-JS](https://link.segmentfault.com/?url=https%3A%2F%2Fcssinjs.org%2F)、[styled-components](https://link.segmentfault.com/?url=https%3A%2F%2Fstyled-components.com%2F)和其他工具来将样式引入到他们的 JS 应用程序中。

当网站只有一个 CSS 文件，仅能够加载一个CSS文件就足够了。但如果想使用[PostCSS](https://link.segmentfault.com/?url=https%3A%2F%2Fpostcss.org%2F)，为了能在任何浏览器中使用所有最新的CSS特性。或者想使用Sass, CSS预处理器，那就需要使用其它的 loader 处理。

我想使用这三种方法——在Sass中编写，在`PostCSS`中处理，以及编译到CSS。这需要引入一些加载器和依赖项。

- `sass-loader` — 加载 SCSS 并编译为CSS
- `node-sass` — Node Sass
- `postcss-loader` — 使用 PostCSS 处理 CSS
- `css-loader` — 解析 css import
  - `style-loader` —— 将CSS注入到DOM中（和MiniCssExtractPlugin不能同时用，如果用需要放在css-loader之前）
- `MiniCssExtractPlugin.loader` —— 将CSS转成JS再注入CSS

```
npm i -D sass-loader postcss-loader css-loader style-loader postcss-preset-env node-sass
```

就像Babel一样，PostCSS 也需要一个配置文件 `postcss.config.js`，在根目录中创建它，并输入以下内容：

**postcss.config.js**

```
module.exports = {
  plugins: {
    'postcss-preset-env': {
      browsers: 'last 2 versions',
    },
  },
}
```

为了测试 Sass 和 PostCSS 是否正常工作，创建 `src/styles/main.scss`，并输入以下内容：

**src/styles/main.scss**

```
$font-size: 1rem;
$font-color: lch(53 105 40);

html {
  font-size: $font-size;
  color: $font-color;
}
```

现在，将文件导入`index.js`并添加四个 `loader` 。 它们从最后编译到第一个，因此列表中最后一个是`sass-loader`，因为需要编译，然后是`PostCSS`，然后是CSS，最后是`style-loader`，它将CSS注入到DOM 中。

**src/index.js**

```
import './styles/main.scss'

/* ... */
```

**webpack.config.js**

```javascript
module.exports = {
  /* ... */
  module: {
    rules: [
      // CSS, PostCSS, and Sass
      {
        test: /\.(scss|css)$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader', 'postcss-loader', 'sass-loader'],
      },
    ],
  },
  plugins: [
  	new MiniCssExtractPlugin()
  ]
}
```

现在，重新构建时，项目中已经应用了`Sass`和`PostCSS`。

## JQuery

全局解析JQuery代码

```javascript
module.exports = {
    /* ... */
    plugins: [
        new webpack.ProvidePlugin({
            $: "jquery",
            jQuery: 'jquery',
            jquery: 'jquery'
        })
    ]
}
```


## 开发

每次进行更新时都要运行`npm run build`，站点越大，构建所需的时间就越长，这样就十分的烦琐。 为此可以为 webpack 设置两种配置：

- 生产配置，用于最小化，优化和删除所有源映射
- 开发配置，该配置在服务器中运行webpack，每次更改都会更新，并具有源映射

开发模式下是在内存中运行所有内容，而不是构建一个`dist`文件，需要安装 `webpack-dev-server`

```
npm i -D webpack-dev-server
```

出于演示目的，我们可以仅将开发配置添加到正在构建的当前`webpack.config.js`文件中并对其进行测试。 但是，我们开发一般要创建两个配置文件：一个生产环境用的 `mode: production`，一个开发环境用的`mode: development`。`mode:production` 环境下打包后的文件压缩的更小。

```javascript
const webpack = require('webpack')

module.exports =  {
  /* ... */
  mode: 'development',
  devServer: {
    historyApiFallback: true,
    contentBase: path.resolve(__dirname, './dist'),
    open: true,
    compress: true,
    hot: true,
    port: 8080,
  },

  plugins: [
    /* ... */
    // Only update what has changed on hot reload
    new webpack.HotModuleReplacementPlugin(),
  ],
})
```

我们添加`mode: development`，并创建`devServer`属性，其中，默认端口将为`8080`，自动打开浏览器窗口，并使用`hot-module-placement`，这需要`webpack.HotModuleReplacementPlugin`插件。 这样模块执行更新而无需完全重新加载页面-因此，如果你更新某些样式，则这些样式将发生变化，并且不用重新加载整个 JS ，大大加快了开发速度。

现在，可以使用`webpack serve`命令来启动项目。

**package.json**

```
"scripts": {
  "start": "webpack serve"
}
npm start
```

运行此命令时，将在浏览器中自动弹出一个指向`localhost：8080`的链接。 现在，您可以更新Sass和JavaScript，并观看其动态更新。

### 热更新

开发的时候不用一次次打包，测试，代码保存之后，浏览器直接更新

```javascript
const webpack = require('webpack')
module.export = {
	plugins: [
        new webpack.HotModuleReplacementPlugin(),
    ]
}
```





### package.json

```json
{
  "name": "webpack-demo",
  "version": "1.0.0",
  "description": "",
  "private": true,
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "webpack serve",
    "build": "webpack"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "@babel/core": "^7.14.6",
    "@babel/plugin-proposal-class-properties": "^7.14.5",
    "@babel/polyfill": "^7.12.1",
    "@babel/preset-env": "^7.14.7",
    "babel-loader": "^8.2.2",
    "clean-webpack-plugin": "^4.0.0-alpha.0",
    "copy-webpack-plugin": "^9.0.1",
    "core-js": "^3.15.2",
    "cross-env": "^7.0.3",
    "css-loader": "^6.1.0",
    "eslint": "^7.30.0",
    "eslint-loader": "^4.0.2",
    "file-loader": "^6.2.0",
    "glob": "^7.1.7",
    "html-loader": "^2.1.2",
    "html-webpack-plugin": "^5.3.2",
    "mini-css-extract-plugin": "^2.1.0",
    "node-sass": "^6.0.1",
    "optimize-css-assets-webpack-plugin": "^6.0.1",
    "postcss": "^8.3.5",
    "postcss-loader": "^6.1.1",
    "postcss-preset-env": "^6.7.0",
    "sass": "^1.35.2",
    "sass-loader": "^12.1.0",
    "style-loader": "^3.1.0",
    "ts-loader": "^9.2.3",
    "url-loader": "^4.1.1",
    "webpack": "^5.44.0",
    "webpack-cli": "^4.7.2",
    "webpack-dev-server": "^3.11.2",
    "webpack-merge": "^5.8.0"
  },
  "dependencies": {
    "jquery": "^3.6.0",
    "typescript": "^4.3.5"
  }
}

```



## 常见问题

**Q: 报错 SCRIPT5022: SecurityError sockjs.js (1687,3)**

A: 找到/node_modules/sockjs-client/dist/sockjs.js 找到代码的 1605行 try { // self.xhr.send(payload)

**Q: Promise找不到**

A: 入口文件头引入"core-js/features/promise"

**Q: 各种语法找不到**

A: webpack5 打包的时候默认会用一些 ES6 的语法糖，需要在 output 的时候配置一下，如下：

```javascript
{
    output: {
    path: path.resolve(__dirname, './dist'),
    filename: '[name].bundle.js',
    environment: {
        arrowFunction: false,  // 不支持箭头函数
        bigIntLiteral: false,  // 不支持BigInt
        const: true,
        destructuring: false,  // 不支持解构
        dynamicImport: false,  // 不支持异步import
        forOf: false,   // 不支持for...of
        module: false,  // 不支持module
    },
}}
```

**Q: js中的图片加载不出**

A: JS中图片需要先引入，再调用，webpack 才能识别出图片并打包

**Q: CSS中的图片路径不对**

A: 如果用的 `MiniCssExtractPlugin.loader` 打包 css 文件，在 `MiniCssExtractPlugin.loader` 下设置个 publicPath

```javascript
 {
     test: /\.(scss|css)$/,
         use: [{
             loader: MiniCssExtractPlugin.loader,
             options: {
                 publicPath: '../',
             }
         }, 'css-loader', 'postcss-loader', 'sass-loader']
 },
```



