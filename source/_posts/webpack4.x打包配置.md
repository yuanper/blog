---
title: webpack4.x打包配置
date: 2018-11-13 10:13:38
tags: webpack
categories: 开发工具
toc: true
---
很长时间没有进行webpack打包配置了，想起来都快有些忘记了，这个东西不是经常用到，只有在新建个项目的时候用到，不用官方模板，自己去动手配置的时候，有时候觉得还是有点难度。今天就想着自己动手进行配置一下，可是还是遇到坑了，折腾了一会，现在的webpack版本都4.x了，有些插件都有些改变。

今天就遇到了一个问题，打算对js里面的css进行分离。原本是把css文件引入到index.js入口文件里面和js一起打包。可是有时候也有这种要求，需要对css文件进行分离打包。这个时候就想到extract-text-webpack-plugin这个插件，可是在使用这个插件之后，webpack打包发生了报错：
{% asset_img extract.png  %}
发现原来extract-text-webpack-plugin这个插件都过时了，在webpack4.4.0版本以上的，开始用mini-css-extract-plugin这个分离css的插件了。
{% codeblock lang:javascript %}
npm install --save-dev mini-css-extract-plugin
{% endcodeblock %}
安装好这个插件之后，在webpack.config.js中使用:
{% codeblock lang:javascript %}
const MiniCssExtractPlugin  = require('mini-css-extract-plugin')//css分离

module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    {
                        loader: MiniCssExtractPlugin.loader,
                    },
                    "css-loader"
                ]
            },
            {
                test: /\.(png|jpg|gif)/,
                use: [
                    {
                        loader: 'url-loader',
                        options: {
                            limit: 5000//如果小于则以base64位显示，大于这个则以图片路径显示
                        }
                    }
                ]
            }
        ]
    },
    plugins: [
        new MiniCssExtractPlugin({
            filename: "[name].css",
            chunkFilename: "[id].css"
        })
    ],
{% endcodeblock %}
之后进行webpack打包，这个打包会提示你要安装webpack-cli依赖，如果没有安装这个依赖打包会报错。

打包之后dist文件下的目录结构：
{% asset_img dabao.png %}
分离成功。

最后分享一下mini-css-extract-plugin的链接，想好好去学习一下的可以点击 
https://www.npmjs.com/package/mini-css-extract-plugin进行查看。

这是我第一次玩博客，希望各位多多指教，有错的地方请帮忙指正！谢谢