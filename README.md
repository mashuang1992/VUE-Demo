# VUE-Demo
vue+webpack demo  拆分css和img
# 常用：

```
1.判断位数 自动加0
字符串.padStart(位数，0)

2.vue2.0
return `${变量}`
```

# 配置环境：

- 1：
```
$ npm i -g webpack webpack-cli webpack-dev-server

```
- 2：
```
$ git clone https://github.com/HelloWoed/webpack-project-demo.git
```

- 3：
```
$ cd webpack-project-demo
```

- 4：
```
$ npm install
```
- 5:
```
$ cd demo01
$ npm run build
$ npm run dev

```
- 6: 打包
```
$ webpack

```

```
webpack – building for development
webpack -p – building for production (minification)
webpack --watch – for continuous incremental building
webpack -d – including source maps
webpack --colors – making building output pretty
You could customize scriptsfield in your package.json file as following.

// package.json
{
  // ... 
  " scripts " : {
     " build " :  " webpack --mode production --watch --progress --display-reasons --color " ,
     " dev " :  " webpack-dev-server --inline - -progress --mode development "
  }
  // ... 
}

```

# webpack4 前端框架基础配置实例-解决css分离图片路径问题
## 1.安装nodejs

## 2. 需要全局和项目安装webpack和webpack-dev-server

```

npm install webpack webpack-dev-server -g
npm install webpack webpack-dev-server --save-dev

```
## 3.本框架的功能如下：

```
　　1、babel babel-folyfill 处理js

　　2、css、less 自动编译成css文件并添加前缀

　　3、less语法编译

　　4、图片处理

　　5、开发环境页面自动刷新

　　6、resolve 解析

　　7、引用HTML模板

　　8、css分离

　　9、打包优化，大文件拆分

　　10、原生ajax请求（get、post）

　　11、纯原生分页

文件目录结构如图所示：

```

### 项目github地址：https://github.com/HelloWoed/webpack-project-demo

#### 说明一下：由于使用了css分离，会导致css背景图片和html img标签图片路径出错问题，这里的解决办法是：css中使用的图片用assets/static/images中的图片，css-loader配置的options中url：false，这样在解析css时就不会处理css图片路径，

```
{
                test:/\.less$/,
                use: ExtractTextWebpackPlugin.extract({
                    fallback:'style-loader',
                    use:[
                        {
                            loader:'css-loader',
                            options:{
                                url:false,
                                importLoaders:1
                            }
                        },
                         'postcss-loader',
                         'less-loader'
                    ],
                    publicPath: '../css'  
                  }),
                  exclude: path.resolve(__dirname,'./node_modules')
            },
　　最后将static目录拷贝到相应的打包目录（这里使用的是 copy-webpack-plugin 插件）static目录为你静态数据目录

new copyWebpackPlugin([
            {
            from:__dirname+'/src/assets/static',//打包的静态资源目录地址
            to:'./static' //打包到dist下面的static
            }
        ]),
 

```　　
```
 


## 4.package.json配置文件如下：

```
{
  "name": "testwebpack",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack --mode production --watch --progress --display-reasons --color",
    "dev": "webpack-dev-server --inline --hot --open --port 4008"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "@babel/core": "^7.1.2",
    "@babel/preset-env": "^7.1.0",
    "autoprefixer": "^9.2.1",
    "babel-core": "^6.26.3",
    "babel-loader": "^8.0.4",
    "babel-polyfill": "^6.26.0",
    "clean-webpack-plugin": "^0.1.19",
    "copy-webpack-plugin": "^4.5.4",
    "css-loader": "^1.0.0",
    "extract-text-webpack-plugin": "^4.0.0-beta.0",
    "file-loader": "^2.0.0",
    "html-webpack-plugin": "^3.2.0",
    "html-withimg-loader": "^0.1.16",
    "less": "^3.8.1",
    "less-loader": "^4.1.0",
    "postcss-loader": "^3.0.0",
    "style-loader": "^0.23.1",
    "url-loader": "^1.1.2",
    "vue-loader": "^15.7.1",
    "vue-template-compiler": "^2.6.10",
    "webpack": "^4.21.0",
    "webpack-cli": "^3.1.2",
    "webpack-dev-server": "^3.1.9"
  },
  "dependencies": {
    "mint-ui": "^2.2.13",
    "vue": "^2.6.10",
    "vue-resource": "^1.5.1",
    "vue-router": "^3.1.2"
  }
}

　　
```
## 5.webpack.config.js文件配置如下：（有很多配置项可根据具体情况设置，不用全部配置）

```
const path = require("path");
// const webpack = require('webpack');
const VueLoaderPlugin = require('vue-loader/lib/plugin'); //配置vue-loader
const HtmlWebpackPlugin = require("html-webpack-plugin");
// 拆分css样式的插件
const ExtractTextWebpackPlugin = require('extract-text-webpack-plugin');
const CleanWebpackPlugin = require('clean-webpack-plugin');
const copyWebpackPlugin = require('copy-webpack-plugin');

function resolve (dir) {
    return path.join(__dirname, './', dir)
}
console.log(resolve ('abcdefghijklmnop'));
module.exports = {
    mode:'development',
    // entry:['babel-polyfill','./main.js'],//可以有多个入口文件
    entry:{
        babelPolyfill:'babel-polyfill',//添加了这个东西，才能完美的将ES6转码,否则Babel默认只转换新的JavaScript句法（syntax），而不转换新的API，如：Set Map
        app:'./main.js',//可以有多个入口文件
    },
    output:{
        path:path.resolve(__dirname,"dist"),//输出文件路径    path:path.join(__dirname,"dist","js")
        filename:"./js/[name].bundle.js",//"[name].[chunckhash].bundle.js"    输出的文件名称
    },
    devtool: 'inline-source-map',
    resolve: {
        extensions: ['.js', '.json'],
        alias: {
            '@': resolve('src'),
        }
    },
    //在webpack4之前，提取公共代码都是通过一个叫CommonsChunkPlugin的插件来办到的。到了webpack4以后，内置了一个一模一样的功能，就是所谓的“优化”
   optimization: {  // 提取公共代码
        splitChunks: {
            chunks: 'async',//有效值为all，async和initial,  all可以特别强大，即使在异步和非异步块之间也可以共享块。
            minSize: 30000,//要生成的块的最小大小（以字节为单位）。
            maxSize: 0,//maxSize只是一个提示，当模块大于maxSize或分裂会违反时，可能会违反minSize。
            minChunks: 1,
            maxAsyncRequests: 5,//按需加载时的最大并行请求数。
            maxInitialRequests: 3,//入口点处的最大并行请求数。
            name: true,//拆分块的名称。提供true将基于块和缓存组密钥自动生成名称。提供字符串或函数将使用自定义名称。如果名称与入口点名称匹配，则将删除入口点。
            cacheGroups: {
                vendor: {   // 剥离第三方插件
                    test: /node_modules\/(.*)\.js/,   // 匹配绝对模块资源路径或块名称。匹配块名称时，将选择块中的所有模块。 /[\\/]node_modules[\\/]/  包括node_modules整个应用程序中的所有代码。
                    reuseExistingChunk: false,  //如果当前块包含已从主文件拆分的模块，则将重用它而不是生成新的块。可能会影响块的结果文件名。
                    filename: '[name].bundle.[hash:4].js', //允许在当且仅当它是初始块时覆盖文件名。所有占位符output.filename也可在此处获得。
                    priority: 10    // 设置优先级，防止和自定义的公共代码提取时被覆盖，不进行打包
                },
                common:{
                    test: /src\/(.*)\.js/,  
                    filename: '[name].bundle.[hash:4].js',
                    minSize:10
                },
                styles: {
                    test: /\.(less|css)$/,
                    chunks: 'all',
                    name:'app',
                    minChunks: 1,
                    reuseExistingChunk: true,
                    enforce: true
                }          
            }
        }
    },
    performance: {
        hints: "warning", // 枚举
        maxAssetSize: 30000000, // 整数类型（以字节为单位）
        maxEntrypointSize: 50000000, // 整数类型（以字节为单位）
        assetFilter: function(assetFilename) {
        // 提供资源文件名的断言函数
        return assetFilename.endsWith('.css') || assetFilename.endsWith('.js');
        
        }
    },
    module:{
        rules:[//rules
            {
                test: /\.vue$/,//配置vue-loader
                loader: 'vue-loader'
            },
            {
                test:/\.js$/,
                exclude: /node_modules/,//有变化了处理，没有变化则不处理
                // include:[resolve('src'), resolve('test')],//需要处理的文件夹
                loader:"babel-loader"
            },
            {
                test:/\.css$/,
                // loader:"style-loader!css-loader",
                use: ExtractTextWebpackPlugin.extract({
                    // 将css用link的方式引入就不再需要style-loader了
                    fallback: "style-loader",
                    use:[
                        {
                            loader:'css-loader',
                            options:{
                                url:false, //false  css中加载图片的路径将不会被解析 不会改变
                                importLoaders:1
                            }
                        },
                        'postcss-loader', 
                    ],
                    publicPath: '../css'       
                })
                // use: [ //以行内样式style的标签写进打包后的html页面中
                //     {
                //         loader: "style-loader"
                //     }, 
                //     {
                //         loader: "css-loader",
                //         options: {
                //             modules: true, // 指定启用css modules
                //             localIdentName: '[name]__[local]--[hash:base64:5]' // 指定css的类名格式
                //         }
                //     },
                //     {
                //         loader: "postcss-loader"
                //     }
                // ]
            },
            {
                test:/\.less$/,
                use: ExtractTextWebpackPlugin.extract({
                    fallback:'style-loader',
                    use:[
                        {
                            loader:'css-loader',
                            options:{
                                url:false,
                                importLoaders:1
                            }
                        },
                         'postcss-loader', 
                         'less-loader'
                    ],
                    publicPath: '../css'   
                  }),
                  exclude: path.resolve(__dirname,'./node_modules')
            },
            {
                test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
                loader: 'url-loader',
                options: {
                  limit: 10000,
                  name: "[name].[hash:4].[ext]",
                  outputPath: "./images",//打包后图片文件输出路径
                  publicPath:'./images'
                }
            },
            {
                test: /\.(mp4|webm|ogg|mp3|wav|flac|aac)(\?.*)?$/,
                loader: 'url-loader',
                options: {
                  limit: 10000,
                  name: 'media/[name].[hash:7].[ext]'
                }
            },
            {
                test: /\.(woff2?|eot|ttf|otf)(\?.*)?$/,
                loader: 'url-loader',
                options: {
                  limit: 10000,
                  name: 'fonts/[name].[hash:7].[ext]'
                }
            },
            { //页面中会用到img标签，img引用的图片地址也需要一个loader来处理,这样再打包后的html文件下img就可以正常引用图片路径了
                test: /\.(htm|html)$/,
                use: 'html-withimg-loader'
            }
        ]
    },
    plugins:[
        // 打包前先清空
        new VueLoaderPlugin(),//配置vue-loader
        new CleanWebpackPlugin('dist/') ,
        new ExtractTextWebpackPlugin({ //样式文件单独打包
            // filename: "./css/[name].min.css",  //指定生成的文件名称
            filename: "./css/style.min.css",  //指定生成的文件名称
            disable: false,  //是否禁用此插件
            allChunks: true
          }),
        new HtmlWebpackPlugin({
            template:"./index.html",//本地模板文件的位置，支持加载器(如handlebars、ejs、undersore、html等)，如比如 handlebars!src/index.hbs；
            filename: './index.html',//输出文件的文件名称，默认为index.html，不配置就是该文件名；此外，还可以为输出文件指定目录位置（例如'html/index.html'）
            title: 'Webpack App',//生成的html文档的标题
            chunks:["app"],//引入的a,b模块，这里指定的是entry中设置多个js时，在这里指定引入的js，如果不设置则默认全部引入,数组形式传入
            minify: {
                caseSensitive: false, //是否大小写敏感
                collapseBooleanAttributes: true, //是否简写boolean格式的属性如：disabled="disabled" 简写为disabled 
                collapseWhitespace: true //是否去除空格
            },
            chunksSortMode: "auto", //引入模块的排序方式
            //excludeChunks: ['a', 'b'], //排除的模块,引入的除a,b模块以外的模块，与chunks相反
            inject:true,//1、true或者body：所有JavaScript资源插入到body元素的底部2、head: 所有JavaScript资源插入到head元素中3、false： 所有静态资源css和JavaScript都不会注入到模板文件中
            showErrors:true,//是否将错误信息输出到html页面中
            hash:true,//是否为所有注入的静态资源添加webpack每次编译产生的唯一hash值
            minify: false,//传递 html-minifier 选项给 minify 输出
            favicon: "",//指定页面图标 favicon 路径到输出的 HTML 文件中。
        }),
        new copyWebpackPlugin([
            {
            from:__dirname+'/src/assets/statics',//打包的静态资源目录地址
            to:'./statics' //打包到dist下面的statics
            }
        ])
    ],
    devServer: {
        publicPath: '/',//
        contentBase: path.resolve(__dirname, 'dist'),//此处的路径必须和输出output文件的路径一致 否则无法自动更新，或者是基于output的相对路径
        compress: true,
        historyApiFallback: true,//在开发单页应用时非常有用，它依赖于HTML5 history API，如果设置为true，所有的跳转将指向index.html
        inline: true,//设置为true，当源文件改变时会自动刷新页面
        // grogress: true,
        host: 'localhost',// 默认是localhost
        port: 9000,//指定用于侦听请求的端口号
        open:true,//当open启用时，开发服务器将打开浏览器。
        //hot: true,// 开启热更新，开启热加载还需在主入口js文件中配置
        // openPage:'index.html',//指定在打开浏览器时导航到的页面。
        overlay: {//当存在编译器错误或警告时，在浏览器中显示全屏覆盖,显示警告和错误：
            warnings: true,
            errors: true
        },
        proxy: {//代理配置
            '/app': {
                target: 'http://ygcyapp.bjfda.gov.cn',
                changeOrigin: true,  //是否跨域
                pathRewrite: {},//如果不想/api传递，我们需要重写路径
            }
        },
        
    }
}

```
## 6.postcss.config.js文件配置如下：

```
module.exports = {
    plugins: [
        require('autoprefixer')
    ]
}
```　　

 
