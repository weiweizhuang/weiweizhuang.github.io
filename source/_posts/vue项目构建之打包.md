---
title: vue项目打包优化
date: 2020-01-01 10:49
tags: [vue,前端]
categories: 
    - [前端, vue, 生产构建之打包优化]
---

# vue项目打包

## 一个多月的时间，终于把这个vue项目写的差不多了。然后我试着build了一下，吓到我了，这么小的项目竟然打包出来14M+
![优化前体积大小](/images/build1.png "优化前体积大小")
![优化前体积大小](/images/byte1.png "优化前体积大小")

> 这可难住我了，于是乎就想能不能压缩一下体积，毕竟一张图片的大小竟然超过了1M，这肯定是不符合生产规范的。于是乎，一只菜鸟又开始了无尽的百度之路！
+ *下面就放几个有关优相关的博客文章，毕竟是人家大神分享的，还是注明一下转载 ！*

 	肯定是先从webpack的配置入手 
[Vue脚手架工程优化之源码和图片压缩--webpack配置](https://blog.csdn.net/llf1991/article/details/79869351)
 	+ 把配置文件config/index.js文件 productionSourceMap: true 这个操作，基本上可以减少一半的体积
  	+ 然后在下载 npm install image-webpack-loader --save-dev 在build/webpack.base.conf.js中改如下配置 
[vue打包通过image-webpack-loader插件对图片压缩优化](https://blog.csdn.net/przlovecsdn/article/details/82761606)
 ```javascript
       module.exports = {
    	module: {
            {
               test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
             // loader: 'url-loader',
             // options: {
                 // limit: 100000,
                 // name: utils.assetsPath('img/[name].[hash:7].[ext]')
             // }
             	loader: ['url-loader?limit=10000&name=' +	utils.assetsPath('img/[name].[hash:7].[ext]'),'image-webpack-loader']
                        },
                    ]
                },
            }
 ```
 	+ 到这一步，从最初的14M+已经压缩到了3.64M。可以说体积小了很多。后面还会让UI把一些静态资源尽量压缩小一点给我，不然一张图就几MB给我，还真hold不住！待你兴冲冲的打包完，然后发布到服务器时，你呆住了，一些背景图片访问不到了。这可咋搞???

![优化后体积大小](/images/build2.png "优化后体积大小")
![优化后体积大小](/images/byte2.png "优化后体积大小")
这可难道精通百度的我，带着这个疑惑，又是一番百度，果真找到了答案。
 	+ 由于我使用了image-webpack-load 重新定义了图片的访问规则。所以导致一些使用背景图片的相对路径找不到文件，直接按照

[vue项目打包在本地直接访问](https://www.jianshu.com/p/45acfd463347) 设置就可以了

		 	1 打开项目文件夹。找到config文件夹里的index.js文件中的build对象，将assetsPublicPath中的“/”，改为“./”。
	        2 assetsPublicPath: './',改成相对路径; 如果你的css文件中引用了background相对路径,那么在打包后预览后是会出现资源找不到的情况的;可以在build文件夹的utils.js下:新增这个publicPath这个属性;


            
 ```javascript
     if(options.extract) {
         return ExtractTexPlugin.extract({
             use: loaders,
             fallback: 'vue-style-loader',
             publicPath: '../../' //这个是重点
         })
     }
 ```


	* 最后直接打开运行打包后的dist目录下面的index.html，项目就完美的运行起来了。*
	* 目前就优化这些，后面再有做别的优化，再更新上来……