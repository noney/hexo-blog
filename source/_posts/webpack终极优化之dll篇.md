---
title: webpack终极优化之dll篇
date: 2017-09-01 11:40:47
tags: 
     - 前端工具
     - webpack
---

[bingoo]: https://github.com/noney/bingoo "基于webpack3的vue2构建工具"

#### 写在前面
最近webpack很火，并且确实很好用，但是入手并不是很容易，过程中会遇到很多的坑，今天和大家踩的坑是关于第三方库的。在我们构建前端项目的时间，使用到第三方库的几率非常大，因为我们不需要自己造轮子，而且当前前端生态圈如此火热，涌现出一大批优秀的库和库架，如react、angular、vue 、jquery等。

当我们用到这些库或框架的时候，我们用webpack打包的时候往往不需要把它们和逻辑代码打到一起，因为第三方库我们不需要去对它进行经常更新，需逻辑代码是我们需要经常更新的，而且如果每次我们修改逻辑代码的时候都要打包第三方库或框架，还会拖慢打包构建速度，做了一些无用功，所以接下来就和大家一起讨论一下如何单独打包第三方库或框架，我们的主角要登场了，那就是dll。


#### 使用CommonsChunkPlugin

webpack官方建议使用CommonsChunkPlugin插件去处理第三方库或框架，它的作用是可以把第三方库或框架放在entry的vendor里面单独打包下面是部分代码完整代码请查看[bingoo]工具

```
{
    entry: {
        vendors: ['vue', 'vuex']
    },
    plugins: [
        new webpack.optimize.CommonsChunkPlugin({
            name: ['vendors'],
            filename: 'vendors.js',
            minChunks: 2
        })
    ]
}

```
这种方法确实能达到单独打包第三方库的目的，但是还是会有两个问题需要解决，第一是当我们需要做静态文件缓存的时候，我们通常会在output filename配置[hash]，但是这样我们的vendor同样会中招，它也会随着hash变化，这样我们每次都会去加载一遍处三方库或框架，问题就严重了，随然有其它解决办法，但是都不是很容易，还要添加各种插件。

第二个问题就是我们每次构建的时候尤其是当我们调试的时候每次webpack都要重新打一次第三方库或框架，随然我们可以设置cache但是都不如dll来的直接。下面就是今天的主角dll出场的时候。这里也只是展示部分关健代码，完整代码请查看[bingoo]工具

#### webpack dll具体用法

首先需要新建一个webpack dll[配置文件](https://github.com/noney/bingoo/blob/master/build/dll.config.js "dll 配置")

```
{
    entry: {
        vendor: ['vue', 'vuex']
    },
    output: {
        path: `${__dirname}/dll`,
        filename: '[name].dll.js',
        library: '[name]'
    },
    plugins: [
        new webpack.DllPlugin({
            path: path.join(__dirname, 'dll', '[name]-manifest.json'),
            name: '[name]',
            context: __dirname
        })
    ]
}

```
有了配置文件我们运行webpack --config dll.config.js 会生成一个dll文件夹，里面包含两个文件一个是vendor.dll.js，它打包了vue、vuex, 另一个vendor-manifest.json文件，它里面描述的是vue、vuex框架的索引，当我们在逻辑代码中引用它们的时候，webpack就会通过索引来找到它们。

然后我们需要在webpack的配置文件中去配置dll，这里只取plugins的部分，完整代码请查看[webpack base](https://github.com/noney/bingoo/blob/master/build/webpack.base.js)配置。

```
{
    plugins: [
        new webpack.DllReferencePlugin({
            context: __dirname,
            manifest: require(`${__dirname}/dll/vendor-manifest.json`)
        }),
        new AddAssetHtmlPlugin({ filepath: require.resolve(`${__dirname}/dll/vendor.dll.js`), includeSourcemap: false }),
    ]
}

```
这里而要注意一点的是，使用AddAssetHtmlPlugin插件，它的作用是将生成的dll文件插入到你的页面文件中，如果你的页面不是静态的，你可以采取模块变量的方式添加进去。

现在我们就已经实现了webpack dll的功能了，当我们在构建的时候就不会把第三方的库或框架和逻辑代码混在一起了打包了，我们的构建调试的速度会有一个质的提高，在我的项目中我用这种方式至少能有50%的提高，同时我们也能对静态文件做缓存了，完全影响不到第三方库或框架，所以说dll简单就是webpack的神器。

#### 写在后面

这只是webpack系列的开遍，也是优化的一小部分，之后会有针对优化和webpack各种用法的文章出现，敬请期待^-^， 也欢迎大家一起来填坑，把自己遇到的坑和填坑的方法分享出来，大家共勉，如果文中有错误或者有更好的用法也希望大家指正~

最后，也分享一个基于webpack3的vue构建工具[bingoo]。

