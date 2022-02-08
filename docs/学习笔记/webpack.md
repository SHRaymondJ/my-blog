---
sidebar: auto
title: webpack学习笔记
date: 2022-02-08 13:09:14
permalink: /pages/e9ef38/
categories: 
  - 学习笔记
tags: 
  - 
---
# webpack 笔记

## 1. 关于 webpack 5

## 2. webpack 基础配置

### 拆解配置和 merge

把公共配置放到 webpack.common.js 内

-   公共配置：
    1. 入口文件路径 entry
    2. 处理 es6 babel-loader
-   merge
    `const {smart} = require('webpack-merge')` - webpack 4
    `const {merge} = require('webpack-merge')` - webpack 5
    使用： module.export = merge(commonWebpack, {})

### 启动本地服务

配置：

```javascript
devServer: {
    port: 3000,
    progress: true, // 显示打包进度条
    contentBase: distPath, // 根目录
    open: true, // 自动打开浏览器
    compress: true, // 使用 gzip 压缩
    proxy: {
        // 设置代理
        '/api': 'http://localhost:8088',
        '/api2': {
            target: 'http://localhost:8088',
            pathRewrite: {
                '/api2': '' // 将地址中 /api2 隐藏
            }
        }
    }
}
```

### 处理 ES6

module 中 rules 里 设置 babel-loader, 需要配置 .babelrc

```json
{
    "presets": ["@babel/preset-env"],
    "plugins": []
}
```

### 处理 CSS

-   style-loader（插入 html 的 style 标签）, css-loader（css 文件）, postcss-loader(css 兼容性)
-   `loader: ['style-loader', 'css-loader', 'postcss-loader']` 括号内以从后向前的顺序执行
-   postcss-loader 需要配置文件 postcss.config.js, 引入插件 autoprefixer
    ```javascript
    module.export = {
        plugins: [require('autoprefixer')],
    }
    ```

### 处理图片

-   开发环境
    -   file-loader 直接引用
-   生产环境
    -   url-loader
        可以把图片转换成 base64 格式编码引入文件，减少 http 请求
    ```javascript
    {
        test: /\.(png|jpg|jpeg|gif)$/,
        use: {
            loader: 'url-loader',
            options: {
                limit: 5*1024,  // 5KB以下的图片转换成base64编码
                outputPath: '/img1/'    //打包其余图片到路径下
            }
        }
    }
    ```

### 输出 (生产打包)

output

```javascript
output: {
    filename: 'bundle.[contentHash].js'     // 打包代码时，加上 hash 戳, webpack5 开始 h 改为小写： contenthash。 可以加 :数字，代表生成几位hash, 如：[contentHash:8] 生成8位hash值
    path: path.join(__dirname, '..', 'dist'),   // join是把各个path片段连接在一起， resolve把‘／’当成根目录
    environment: {
        arrowFunction: false,  // 不支持箭头函数s
        bigIntLiteral: false,  // 不支持BigInt
        const: true,
        destructuring: false,  // 不支持解构
        dynamicImport: false,  // 不支持异步import
        forOf: false,   // 不支持for...of
        module: false,  // 不支持module
    },
}
```

## 生产环境插件

1. CleanWebpackPlugin 每次生成之前都清空 path

## 多入口配置

1. entry 配置多个
2. output 的 filename 改成 `[name].bundle.[contentHash].js`
3. htmlWebpackPlugin 要有多个，其中 chunks 用来配置 html 引入的 js

## 抽离 css 文件

如果用 style-loader 打包 css 文件，css 会在 js 文件中插入 html ，非常影响性能
所以，在生产环境下，应该把 css 文件抽离出来

1. 引入 MiniCssExtractPlugin
2. 把 loader 里的 `style-loader` 替换成 `MiniCssExtractPlugin.loader`
3. Plugins 里添加 `new MiniCssExtractPlugin({ filename: 'css/bundle.[contentHash].css' })`
4. 新增配置项 `optimization` ， 用两个插件压缩：
   `minimizer: [new TerserJSPlugin({}), new OptimizeCSSAssetsPlugin({})]`

## 合并公共代码

-   在 optimization 下配置 splitChunks
-   对于多页面应用，避免重复打包
-   通常把第三方库、和自定义 js 文件分开成两种 chunk 进行合并
-   配置如下：

```javascript
splitChunks: {
    chunks: 'all',
    cacheGroups: {
        vendor: {
            name: 'vendor', // chunk 名称
            priority: 1, // 权限更高，优先抽离，重要！！！
            test: /node_modules/,   // 如果在 node_modules 目录下的，就都抽离到 vendor 中
            minSize: 0,  // 大小限制
            minChunks: 1  // 最少复用过几次
        },
        common: {
            name: 'common',
            priority: 0,  //
            minSize: 2000,
            minChunks: 2    // 自己写的代码，如果要抽离的话，至少出现在两个chunk中才需要抽离，否则没必要抽离
        }
    }
}
```

-   配置完成后，还是要将这两个 chunk 按需添加到 htmlWebpackPlugin 的 chunks 里面

## 懒加载

用 `import()` 动态引入 js 文件会返回一个原生 Promise，可以直接用 `.then()` 解析。
如果懒加载的文件是输出默认值的话（`export default {}`），引用的时候需要 读取 `res.default.xxx`

## module chunk 和 bundle 的区别

1. module - 各个源码文件，webpack 中一切皆模块
2. chunk - 多个模块合并在一起之后的东西，entry, import(), splitChunks 都能产生 chunk
3. bundle - 最终打包生成的文件

## webpack 性能优化

### 开发环境性能优化

#### babel-loader 优化

使用 babel-loader 的时候加上缓存： `use: [babel-loader?cacheDirectory]` ，没改过的部分用缓存，改过的重新loader

#### webpack.IgnorePlugin 不引入

在 plugins 里面设置： `new webpack.IgnorePlugin(/\.\/locale/, /moment/)`
例子注释： 不引入 moment 库内的 locale 目录
做了上述修改之后，需要自行**手动地**在项目中引入自己需要用到的 locale

#### noParse 引入的时候避免重复打包
项目中可能有些文件已经打包过了（xxx.min.js），可以在 module 中设置 `noParse: [/react\.min\.js$/]` 不对某些文件进行打包

#### happyPack 开启多进程打包
使用：
```javascript
// rules
// 把对 .js 文件的处理转交给 id 为 babel 的 HappyPack 实例
use: ['happypack/loader?id=babel'],

// plugins
// happyPack 开启多进程打包
new HappyPack({
    // 用唯一的标识符 id 来代表当前的 HappyPack 是用来处理一类特定的文件
    id: 'babel',
    // 如何处理 .js 文件，用法和 Loader 配置中一样
    loaders: ['babel-loader?cacheDirectory']
}),
```
#### ParallelUglifyPlugin 多进程压缩JS
使用：
```javascript
// 使用 ParallelUglifyPlugin 并行压缩输出的 JS 代码
new ParallelUglifyPlugin({
    // 传递给 UglifyJS 的参数
    // （还是使用 UglifyJS 压缩，只不过帮助开启了多进程）
    uglifyJS: {
        output: {
            beautify: false, // 最紧凑的输出
            comments: false, // 删除所有的注释
        },
        compress: {
            // 删除所有的 `console` 语句，可以兼容ie浏览器
            drop_console: true,
            // 内嵌定义了但是只用到一次的变量
            collapse_vars: true,
            // 提取出出现多次但是没有定义成变量去引用的静态值
            reduce_vars: true,
        }
    }
})
```
项目大的话，开启多进程可以加快速度，但是对于小项目来说，反而会降低打包速度，因为有进程开销在那儿

#### 网页刷新
webpack自带的保存就刷新
#### 热更新
- 需要先进行webpack入口配置、再启用插件、再在devServer中开启热更新。
- 然后给需要热更新的文件配置热更新
```javascript
const HotModuleReplacementPlugin = require('webpack/lib/HotModuleReplacementPlugin');

module.export = {
    entry: {
        index: [
            'webpack-dev-server/client?http://localhost:8080/', //热更新配置本地端口号
            'webpack/hot/dev-server',   //热更新配置 devServer
            path.join(srcPath, 'index.js')
        ],
    },
    plugins: [
        new HotModuleReplacementPlugin()    //启用热更新插件
    ],
    devServer: {
        ...,
        hot: true, //启用热更新
    }
}
```

对某个文件进行热更新：
```javascript
// 增加，开启热更新之后的代码逻辑
if (module.hot) {
    module.hot.accept(['./math'], () => {
        const sumRes = sum(10, 30)
        console.log('sumRes in hot', sumRes)
    })
}
```
#### DllPlugin 动态链接库插件
- 先把库打包出来
```javascript
const path = require('path')
const DllPlugin = require('webpack/lib/DllPlugin')
const { srcPath, distPath } = require('./paths')

module.exports = {
    mode: 'development',
    // JS 执行入口文件
    entry: {
        // 把 React 相关模块的放到一个单独的动态链接库
        react: ['react', 'react-dom']
    },
    output: {
        // 输出的动态链接库的文件名称，[name] 代表当前动态链接库的名称，
        // 也就是 entry 中配置的 react 和 polyfill
        filename: '[name].dll.js',
        // 输出的文件都放到 dist 目录下
        path: distPath,
        // 存放动态链接库的全局变量名称，例如对应 react 来说就是 _dll_react
        // 之所以在前面加上 _dll_ 是为了防止全局变量冲突
        library: '_dll_[name]',
    },
    plugins: [
        // 接入 DllPlugin
        new DllPlugin({
        // 动态链接库的全局变量名称，需要和 output.library 中保持一致
        // 该字段的值也就是输出的 manifest.json 文件 中 name 字段的值
        // 例如 react.manifest.json 中就有 "name": "_dll_react"
        name: '_dll_[name]',
        // 描述动态链接库的 manifest.json 文件输出时的文件名称
        path: path.join(distPath, '[name].manifest.json'),
        }),
    ],
    }
```
- 生成的两个文件，dll.js 引用到 index.html 中，manifest.json 描述文件配置到 DllReferencePlugin 插件中
```javascript
new DllReferencePlugin({
    // 描述 react 动态链接库的文件内容
    manifest: require(path.join(distPath, 'react.manifest.json')),
}),
```
### 生产环境性能优化
1. 小图片 base64 编码
2. Bundle hash
3. 抽离公共代码
4. IgnorePlugin
5. 懒加载
6. CDN加速
    > output里或者图片的url-loader 里设置publicPath 为 CDN 地址，打包后把资源上传到 CDN 即可
7. 使用 production
    > 自动开启代码压缩
    vue React 会自动删除调试代码（如开发环境的 warning）
    自动启用 tree-shaking (删除没用到的代码，只对ES6 module有用，commonJS 没用) 

8. 使用 scope hosting
    > 可以让多个函数合并成一个函数
    代码体积更小
    创建函数 作用域更少
    代码可读性更好
    ```javascript
    const ModuleConcatenationPlugin = require('webpack/lib/optimize/ModuleConcatenationPlugin');

    module.exports = {
        resolve: {
            // 针对 npm 中的第三方模块优先采用 jsnext:main 中指向的 ES6 模块化语法的文件
            mainFields: ['jsnext:main','browser','main']
        },
        plugins: [
            new ModuleConcatenationPlugin(),
        ]
    }
    ```

## babel
- presets-env
- core-js regenerator
- babel-polyfill 就是 core-js regenerator 的合集
- babel-polyfill 按需引入设置
    `"useBuiltIns": "usage"`
- 配置 babel-runtime 就不会污染全局环境(如果要做第三方库，建议配置)

## Q. 前端为何要进行打包和构建？
1. 代码方面
    - 体积更小（），加载更快
    - 可以编辑高级语言或语法（TS，ES6+，模块化，sass）
    - 兼容性和错误检查（Polyfill, postcss, eslint）
2. 研发流程层面
    - 统一、高效的开发环境
    - 统一的构建流程和产出标准
    - 集成公司构建规范