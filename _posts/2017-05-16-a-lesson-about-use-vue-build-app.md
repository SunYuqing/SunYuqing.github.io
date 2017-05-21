---
layout: post
title: 让我们使用Vue来建一个Web App吧
categories: [blog ]
tags: [vue, ]
description: 一个关于如何使用Vue建立一个web app的教程
---

*本教程是关于使用vue、npm API接口搭建一个小型的**web app**，生成简洁的图表。*



## 让我们快点开始吧 

### 技术栈：

*	vue.js
*	Chart.js
*	vue-chartjs
*	vue-cli
*	axios
*	webpack

### 创建&&配置

首先我们需要创建**vue-cli** 作为脚手架。希望你已经把node和npm更新到最新版本啦。如果你用**yarn** 也可以。（注：Yarn 是 Facebook 创造的 JavaScript 包管理器。他向在应用中使用 JavaScript 的开发者提供了快速、高可靠性安全依赖的包管理。）

好了开始创建项目的脚手架吧。

```
$ npm install -g vue-cli
```

如果你想做单元测试或e2e测试，你也可以创建他们。然而我们这次并不需要测试。但是我们需要使用**vue-router** 来实现路由功能。

现在我们开始使用webpack创建我们的项目吧。

```
$ vue init webpack npm-stats
```

然后为我们的项目添加`vue-chartjs` , `chart.js` 和` axios` 依赖。

```
$ npm install vue-chartjs chart.js axios --save
```

通过` npm run dev` 来检查一下项目是不是可以启动。如果没有问题就进行下一步啦。

*PS : 使用css预处理的需要安装css预处理的依赖包，我在这个项目中是用`stylus` 编写css，需要在`package.json` 文件内写好依赖的版本，一般为最新版本号，如若不清楚可以查看[我的代码库](https://github.com/sunyuqing/npm-stats)中`package.json`文件*

## 开始搭建了

声明：本教程只关注`javascript` 相关代码，不关注具体样式,默认对于样式你可以靠自己搞定，你也可以到我的[代码库](https://github.com/sunyuqing/npm-stats)查看样式的实现。

### 组件

下面这些是我们需要创建的组件：

* components/header.vue
* components/fooder.vue
* components/lineChart.vue
* page/start.vue

其中header和footer只是一些样式、logo和链接，我们将跳过，重点在后面两个组件。

#### lineChart

lineChart组件是chart.js的实例。我们需要导入`line`扩展它。首先我们在Vue的实例里为props创建两个属性。一个代表下载的次数，另一个表示标签，如年、月、日。

```
props: {
	chartData: {
		type: Array,
		required: false
	},
	chartLabels: {
		type: Array,
		required: true
	}
},
```

由于我们需要所有的图表看以来都一样，所以我们在数据模型（data model）中定义了一些Chart.js的样式选项，该模型作为renderChart()方法的选项传递。

现在我们有了一组数据，可以建立数组绑定标签和数据了。

[lienChart.vue 代码段](https://gist.github.com/apertureless/bc973fe1b2ea7803a195a5d282ae1827)

#### start
我们的lienChart组件已经完成了，接下来要进行下一步。在页面中我们需要一个输入框和一个按钮来提交需要查询的package名称，发起请求，并向chart组件传递数据。

先思考一下我们需要的是什么样的数据和状态/数据模型。首先需要一个package数据模型，我们使用v-model来绑定数据。我们还将使用package的名称作为标题，把它定义为packageName。然后我们用两个数组用于记录请求数据下载和标签。若请求出错，我们需要调用这两个函数errorMessage和showError。最后我们需要在请求成功后才加载图表。

##### npm API

获取package的下载量的api有很多种，例如下面这个：

```
GET https://api.npmjs.org/downloads/point/{period}[/{package}]
```
  
  period是用来定义日期范围的。我们把它默认定义为*最近一个月*。在下一节教程中我们将添加一个日期插件可以自定义日期。

现在我们的数据模型长成这个样子：

```
data () {
	package: null,
	packageName: '',
	period: 'last-month',
	loaded: false,
	downloads: [],
	labels: [],
	showError: false,
	errorMessage: '请输入一个package名称'
}
```

####	模版

接下来该建立模版了。我们有五件事要做：

*	文本输入框
*	搜索按钮
*	输出错误
*	package名称作为表头
*	绘制表格

```
<input	 class="Search__input"
         @keyup.enter="requestData"
         placeholder="npm package name"
         type="search" name="search"
         v-model="package">
<button  class="Search__button"
		 @click="requestData">搜索</button>     
```

```
<div class="error-message" v-if="showError">
       {{ errorMessage }}
</div>
```

```
<h1 class="title" v-if="loaded">{{ packageName }}</h1>
<line-chart v-if="loaded" :chart-data="downloads" :chart-labels="labels"></line-chart>
```
先忽略css的类名。我们为输入框绑定了keyup中的enter事件。所以只要你回车就会触发requestData()方法。

对于潜在的错误，我们有一个判断条件。只有当showError为true时，我们才会显示该消息。有两种可能发生的错误：一种是有人尝试搜索一个package但是输入空名称或者输入一个不存在的名称。

对于第一种情况我们会显示默认的errorMessage,对于第二种情况，我们会从返回的请求数据中抓取错误信息。

[start-01代码段](https://gist.github.com/apertureless/8f97ddc6a865c0063b581bb807f0b209)

## JavaScript

现在要开始编码了。首先我们来完善requestData()方法。

> Written with [StackEdit](https://stackedit.io/).