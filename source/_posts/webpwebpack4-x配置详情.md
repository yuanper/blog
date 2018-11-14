---
title: webpack4.x配置详情
date: 2018-11-13 11:12:41
tags: webpack
categories: 开发工具
toc: true
---
webpack打包工具现在非常流行，熟悉并且能够进行配置也变得非常重要。在学习和使用的过程中遇到过很多的问题，希望能够让自己记录下来，巩固自己的学习。
1.创建文件目录

先在自己的常用盘中（我自己的项目一般都建在E盘的一个文件夹下）创建一个文件夹，比如webpack_demo，我用的编辑器是visual studio code，使用ctrl+`直接切到这个文件目录下
{% asset_img 1.png %}
然后在该终端输入npm init 使其生成一个package.json文件
{% asset_img 2.png %}
然后在webpack_demo文件夹下建立如下文件目录：
{% asset_img 3.png %}
2.进行基础配置打包

建立好上面的目录之后，现在就该往里面添加内容了。往index.html 、index.js里面添加一些内容，这些内容就看个人随意了，只要是合理的都可以。在这两个文件里面添加内容是为了待会进行webpack打包的时候，不至于让打包出来的东西都是空的，不然这就很尴尬了。然后就该进行webpack.config.js里面配置了。下面是我进行的简单的配置：
{% asset_img 5.png %}

{% asset_img 6.png %}
最为简单的入口、出口、插件、本地服务的配置。entry是入口文件，使用的是相对路径，对应到入口文件index.js，这里配置的是单入口文件。多入口的以后配置。output是打包后的文件的路径，使用的是绝对路径。打包后的文件都在dist目录下。出口文件的名称filename使用的是[name].js，这种写法就是为了与入口的文件名称相对应，也有另一个原因，就是多入口文件的时候，出口也相应的有多个，这样使用[name].js就不怕你是单入口还是多入口，很方便。接下来就是本地服务的配置，host可以使用本机ip地址，也可以使用localhost，port就是自己设置的端口。这个html-webpack-plugin插件有两个作用：

 为html文件中引入的外部资源如script、link动态添加每次compile后的hash，防止引用缓存的外部文件问题
 可以生成创建html入口文件，比如单页面可以生成一个html文件入口，配置N个html-webpack-plugin可以生成N个页面入口
 这个插件有很多的配置项，这里我只用到了三个，removeAttributeQuotes是否去掉属性的引号，如下type 和 src属性里面的引号就都去掉了；hash 是否为所有注入的静态资源添加webpack每次编译产生的唯一hash值，添加hash形式如下所示
{% codeblock lang:javascript %}
<script type=text/javascript src=index.js?772a5c2dd58541f56576></script></body>
{% endcodeblock %}
 template就是本地模板的设置。上述打包之后的结构目录如下
{% asset_img 7.png %}
打包后的index.html文件里面的内容如下：
{% codeblock lang:html %}
<!DOCTYPE html>
<html lang=en>
<head>
    <meta charset=UTF-8>
    <meta name=viewport content="width=device-width,initial-scale=1">
    <meta http-equiv=X-UA-Compatible content="ie=edge">
    <title>webpack_demo</title>
</head>
<body>
    <div id=title></div>
  <script type=text/javascript src=index.js?772a5c2dd58541f56576></script></body>
</html>
{% endcodeblock %}
这就是一个最为简单的webpack打包使用了。

3.css样式与js分离

接下来就是往里面添砖加瓦了，有了js和html文件，当然还是需要有css文件了。在src目录下创建一个css文件夹，如下图：
{% asset_img 8.png  %}
在入口文件index.js里面引入index.css
 {% codeblock lang:javascript %}
 import './css/index.css'
document.getElementById('title').innerHTML = 'webpack demo'
{% endcodeblock %}
对css文件进行打包，需要安装一些依赖，style-loader、css-loader。
{% codeblock lang:javascript %}
npm install --save-dev style-loader css-loader
{% endcodeblock %}
然后在webpack.config.js中加入module配置：
{% codeblock lang:javascript %}
module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    {
                        loader: 'style-loader'
                    },{
                        loader: 'css-loader'
                    }
                ]
            }
        ]
    },
{% endcodeblock %}

这里的use有几种配置方法，你也可以根据自己的喜好写，比如 use:['style-loader','css-loader']

然后进行打包，这个时候css打包在index.js文件中，如果要把css文件从js文件中分离出来，这个时候就需要用到mini-css-extract-plugin,这个插件需要webpack版本在4.4.0以上,以前是extract-text-webpack-plugin这个插件。
{% codeblock lang:javascript %}
npm install --save-dev mini-css-extract-plugin
{% endcodeblock %}
相关配置如下：
{% asset_img 9.png %}
然后在进行打包，在dist文件夹下生成了css文件，内容如下:
{% asset_img 10.png %}
至此css与js分离操作成功！

 有不少的小伙伴肯定也有用less、sass、stylus这三种css预处理语言，在这里我也拿其中的一种stylus来做一个配置。顺便使用postcss + autoprefixer为 CSS 中的属性添加浏览器特定的前缀。先介绍一下postcss：PostCSS 本身是一个功能比较单一的工具。它提供了一种方式用 JavaScript 代码来处理 CSS。它负责把 CSS 代码解析成抽象语法树结构（Abstract Syntax Tree，AST），再交由插件来进行处理。PostCSS最为常用的插件是Autoprefixer，其作用是为 CSS 中的属性添加浏览器特定的前缀。Autoprefixer 可以根据需要指定支持的浏览器类型和版本，自动添加所需的带前缀的属性声明。开发人员在编写 CSS 时只需要使用 CSS 规范中的标准属性名即可。

首先安装依赖：
{% codeblock lang:javascript %}
npm install --save-dev stylus stylus-loader
{% endcodeblock %}
接着安装postcss依赖：
{% codeblock lang:javascript %}
npm install --save-dev postcss postcss-loader autoprefixer
{% endcodeblock %}
安装好这些依赖之后，就在目录下建立以下文件，如图：
{% asset_img 11.png %}
文件里面的内容如下：
{% codeblock %}
box.styl内容：

.box {
    display flex
    width 400px
    height 400px
    border 1px solid #000000
    transform rotate(45deg)
    p{
        color red
        font-size 14px
    }
}

postcss.config.js:

module.exports = {
    plugins: [
        require('autoprefixer')
    ]
}
{% endcodeblock %}
做好以上准备之后，该在webpack.config.js文件里面进行stylus解析配置了：
{% codeblock lang:javascript %}
module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    {loader: MiniCssExtractPlugin.loader},
                    'css-loader'
                ]
            },{
                test: /\.styl$/,
                use: [
                    {loader: MiniCssExtractPlugin.loader},
                    'css-loader',
                    'postcss-loader',
                    'stylus-loader'
                ]
            }
        ]
    }
//在此说明一下：因为是要对stylus文件里面的样式添加浏览器特定的前缀，所以需要在这里面引入postcss-loader进行解析，而且这几种loader的配置顺序不能颠倒，不然会产生错误或者达不到目的。我自己就尝试了把postcss-loader放在最后，解析就会出错。也算是一种小坑。
{% endcodeblock %}
 

然后进行webpack打包，生成了如下：
{% asset_img 12.png %}


此时你也会发现，不管是stylus文件还是.css文件，都会打包到一起，生成一个文件。

4、图片打包。

在src目录下创建文件夹images，并且添加一张图片，首先以背景图片的方式引入图片
{% asset_img 13.png %}


然后安装依赖
{% codeblock lang:objc %}
[rectangle setX: 10 y: 10 width: 20 height: 20];
{% endcodeblock %}
npm install --save-dev file-loader url-loader
 

然后在module里面这样配置：
{% codeblock lang:objc %}
{
              test: /\.(png|jpg|gif)/,
                use: [
                    {
                        loader: 'url-loader',
                        options: {
                            limit: 5000,//如果小于则以base64位显示，大于这个则以图片路径显示
                            outputPath: 'images/'//让图片都打包到images文件夹下
                        }
                    }
                ]
            }
 
{% endcodeblock %}
这时候打包出来后，在dist下会出现一个images文件夹：
{% asset_img 14.png %}

这时候打包是成功了，但是开启本地服务之后，发现图片的路径有问题。这里面就是有个坑。这个时候就需要解决静态资源的路劲问题。如下：
webpack.config.js
{% codeblock lang:javascript %}

let website = {
    publicPath: 'http://192.168.27.21:8080/'//这个就是本地监听服务
}

output: {
        path: path.resolve(__dirname,'dist'),
        filename: '[name].js',
        publicPath: website.publicPath//解决静态路径的问题，比如图片的路径
    },
{% endcodeblock %}
 

图片在html中还有另一种写法
{% codeblock lang:html %}
<body>
    <div id="title"></div>
    <div class="box">
        <p>这里是box里面的内容</p>
    </div>
    <div class="wrap"></div>
    <img src="./images/none.png" />
</body>
{% endcodeblock %}
 
如果需要对里面的图片打包到images/文件夹下，就需要使用一个插件html-withimg-loader
{% codeblock lang:javascript %}
npm install --save-dev html-withimg-loader
{% endcodeblock %}
{% codeblock lang:javascript %}
{
          test: /\.(htm|html)$/i,
          use: ['html-withimg-loader']//这个插件是让html里面的图片打包到images/文件夹下
 }
{% endcodeblock %}

打包后生成的内容是：
{% codeblock lang:html %}
<body>
    <div id=title></div>
    <div class=box>
        <p>这里是box里面的内容</p>
    </div>
    <!-- <div class="wrap"></div> -->
    <img src=http://192.168.27.21:8080/images/3ae16dcddacdf7d99b869750401202fb.png>
<script type=text/javascript src=http://192.168.27.21:8080/index.js?2735cd9759ec100ecd6f></script></body>
{% endcodeblock %}

5、es6语法转化babel配置

现在babel-loader的版本都8.0.0了，其他依赖配置都发生了一些改变，由以前版本的bebel-core 、babel-preset-env、babel-preset-react、babel-preset-es2015到现在的 @babel/core、@babel/preset-env、@babel/preset-es2015、@babel/preset-react
{% codeblock lang:javascript %}
npm install --save-dev babel-loader 
npm install --save-dev @babel/core  @babel/preset-env @babel/preset-react
{% endcodeblock %}
 
{% codeblock lang:javascript %}
//webpack.config.js

           {
                test: /\.(jsx|js)$/,
                use: [
                    {
                        loader: 'babel-loader',
                    }
                ],
                exclude: /node_modules/
            }
{% endcodeblock %}
//创建.babelrc文件
{% codeblock lang:javascript %}
{
    "presets": [
        "@babel/preset-env",
        "@babel/preset-react"
    ]
}
{% endcodeblock %}
这样就可以了。打包之后就可以看到效果。

6、环境配置
package.json
{% codeblock lang:javascript %}
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "server": "webpack-dev-server",
    "dev": "set type=dev&webpack",
    "build": "set type=pro&webpack"
  },
{% endcodeblock %}

webpack.config.js
{% codeblock lang:javascript %}
if(process.env.type=="dev"){//本地环境
    var website = {
        publicPath: 'http://192.168.27.21:8080/'
    }
}else{
    var website = {
        publicPath: 'http://*****' //你的线上地址
    }
}
 
{% endcodeblock %}

7、总结

以上就是一个webpack的配置详情，基本上够用了，自己写的比较啰嗦，有些地方写的不好的，麻烦留言帮我指正，谢谢！也希望各位给我一些鼓励。不吝赐教。

 