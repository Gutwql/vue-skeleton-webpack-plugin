vue-skeleton-webpack-plugin
===================

[![npm version](https://badge.fury.io/js/vue-skeleton-webpack-plugin.svg)](https://badge.fury.io/js/vue-skeleton-webpack-plugin)
[![Build Status](https://travis-ci.org/lavas-project/vue-skeleton-webpack-plugin.svg?branch=master)](https://travis-ci.org/lavas-project/vue-skeleton-webpack-plugin)

[![NPM](https://nodei.co/npm/vue-skeleton-webpack-plugin.png?downloads=true&downloadRank=true&stars=true)](https://nodei.co/npm/vue-skeleton-webpack-plugin/)

这是一个基于 Vue 的 webpack 插件，为单页/多页应用生成骨架屏 skeleton，减少白屏时间，在页面完全渲染之前提升用户感知体验。

## 基本实现

参考了[饿了么的 PWA 升级实践](https://huangxuan.me/2017/07/12/upgrading-eleme-to-pwa/)一文，
使用服务端渲染在构建时渲染 skeleton 组件，将 DOM 和样式内联到最终输出的 html 中。

另外，为了开发时调试方便，会将对应路由写入`router.js`中，可通过`/skeleton`路由访问。

插件具体实现可参考[我的这篇文章](https://xiaoiver.github.io/coding/2017/07/30/%E4%B8%BAvue%E9%A1%B9%E7%9B%AE%E6%B7%BB%E5%8A%A0%E9%AA%A8%E6%9E%B6%E5%B1%8F.html)

## 使用方法

安装：
```bash
npm install vue-skeleton-webpack-plugin
```

运行测试用例：
```bash
npm run test
```

在 webpack 中引入插件：
```js
// webpack.conf.js
import SkeletonWebpackPlugin from 'vue-skeleton-webpack-plugin';

plugins: [
    new SkeletonWebpackPlugin({
        webpackConfig: require('./webpack.skeleton.conf')
    })
]
```

在单页应用中开发模式下自动插入路由规则：
```js
// webpack.dev.conf.js
import SkeletonWebpackPlugin from 'vue-skeleton-webpack-plugin';

module: {
    rules: [
        SkeletonWebpackPlugin.loader({
            resource: resolve('src/entry.js'),
            options: {
                entry: 'skeleton',
                routePathTemplate: '/skeleton',
                importTemplate: 'import Skeleton from \'./Skeleton.vue\';'
            }
        })
    ]
}
```

## 参数说明

### SkeletonWebpackPlugin

- webpackConfig *必填*，渲染 skeleton 的 webpack 配置对象
- insertAfter *选填*，渲染 DOM 结果插入位置，默认值为`'<div id="app">'`

### SkeletonWebpackPlugin.loader

参数分为两类：
1. [ webpack模块规则](https://doc.webpack-china.org/configuration/module/#rule)，skeleton 对应的路由将被插入路由文件中，所以需要指定一个或多个路由文件，使用`resource/include/test`皆可指定 loader 应用的文件。
2. `options` 将被传入 loader 中的参数对象，包含以下属性：
    - entry *必填*，支持字符串和数组类型，对应页面入口的名称
    - importTemplate *选填*，引入 skeleton 组件的表达式，默认值为`'import [nameCap] from \'@/pages/[nameCap].vue\';'`
    - routePathTemplate *选填*，路由路径，默认值为`'/skeleton-[name]'`
    - insertAfter *选填*，路由插入位置，默认值为`'routes: ['`

在`importTemplate`和`routePathTemplate`中可以使用以下占位符：
- `[name]` 和`entry`保持一致
- `[nameCap]` `entry`首字母大写

例如使用以下配置，将向路由文件中插入`'import Page1 from \'@/pages/Page1.vue\';'`和`'import Page2 from \'@/pages/Page2.vue\';'`两条语句。同时生成`/skeleton-page1`和`/skeleton-page2`两条路由规则。
```js
{
    entry: ['page1', 'page2'],
    importTemplate: 'import [nameCap] from \'@/pages/[nameCap].vue\';',
    routePathTemplate: '/skeleton-[name]'
}
```

## 示例

具体应用示例可参考[Lavas Appshell模版](https://github.com/lavas-project/lavas-template-vue-appshell)和[Lavas MPA模版](https://github.com/lavas-project/lavas-template-vue-mpa)。

或者参考[examples](https://github.com/lavas-project/vue-skeleton-webpack-plugin/tree/master/examples)下的测试用例，其中也包含了单页和多页情况。

