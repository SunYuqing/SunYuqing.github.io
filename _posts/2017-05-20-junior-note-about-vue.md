---
layout: post
title: 关于vue的一点笔记--PART I
categories: [blog ]
tags: [vue, ]
description: 一个关于如何使用Vue建立一个web app的教程
---


>阅读本文前需要你有过[vue.js官方文档](https://cn.vuejs.org)的一些学习基础，本博文对一些基础的概念和示例不做重复介绍。如果你已经有扎实的基础积累可以直接阅读[下一篇](www.yuqingsun.com)的博文，会进入到更加深入的vue.js学习。

### 1.data属性

注意只有这些被data代理的属性是响应的。如果在实例创建之后添加新的属性到实例上，它不会触发视图更新。
你得在new之前，就把所有的data都传进去。实例创建之后，其实可以使用`$set`来加入属性，也可以实现响应功能。
	
### 2.`app.$data/ $el`

Vue 实例暴露了一些有用的实例属性与方法。这些属性与方法都有前缀 $，以便与代理的 data 属性区分。
	
```
		app.$data === data // -> true
```
### 3.app.$set

你要知道一个事实：`app.$data.msg === app.msg`，而且由于vue的响应系统，所以你直接`app.msg = 'Let us go!'`和
	`app.$set(app.$data, 'msg', 'Let us go')`是一样的。
	总之，因为javascript的对象是引用型数据，所以你只要使用对了引用，怎么搞都是一样的。

### 4.计算属性

所谓计算属性，就是跟ES5的getter一样的，用function来定义个属性，当你获取这个属性的时候，实际上是要执行这个function，执行function的过程就是计算过程，所以也就叫计算属性。所有的计算属性被放computed里面，computed和data, methods同级。

### 5. computed **VS**  methods

	计算属性是基于它们的依赖进行缓存的!
	计算属性是基于它们的依赖进行缓存的!
	计算属性是基于它们的依赖进行缓存的!
	重要的事情说三遍。。。

计算属性只有在它的相关依赖发生改变时才会重新求值。这就意味着只要 message 还没有发生改变，多次访问计算属性会立即返回之前的计算结果，而不必再次执行函数。
这也同样意味着下面的计算属性将不再更新，因为 Date.now() 不是响应式依赖：
```
	computed: {
		now: function () {
			return Date.now()
		}
	}
```
相比而言，只要发生重新渲染，method 调用总会执行该函数。

	我们为什么需要缓存？假设我们有一个性能开销比较大的的计算属性 A ，它需要遍历一个极大的数组和做大量的计算。
	然后我们可能有其他的计算属性依赖于 A 。如果没有缓存，我们将不可避免的多次执行 A 的 getter！如果你不希望有缓存，请用 method 替代。
### 6.watch属性

watch是和data, methods, computed同级的一个参数，你可以在watch里面规定你要watch的属性，当这些属性发生变化的时候，它会执行对应的那个函数。栗子：
```
	var app = new Vue({
		data: {
			message: '',
			msg: '',
		},
		watch: {
			message: function() { // 当app.$data.message发生变化的时候，执行这个函数
			this.msg = 'Changed!'
			},
		},
	})
```
### 7.指令
	
指令也可以有参数，主要是指v-on和v-bind这两个指令，它们后面跟上一个冒号，冒号和等号之间的内容就是参数，例如`v-bind:src="src"`。

* v-if
	* `<div v-if="is"></div>`
	* v-if是有dom消耗的，使用时不应该反复更改is值。
* v-show
	*  `<div v-show="is"></div>`
	* is为false的时候，会给这个元素加一个display:none，仅仅是隐藏这个元素，不会有dom的消耗。
* v-for
	* ```<div v-for="item in items"></div>
		 <div v-for="(item, index) in items"></div>
		 <div v-for="(val, key) in object"></div>
		<div v-for="(val, key, index) in object"></div>	 
	```
	* v-for 默认行为试着不改变整体，而是替换元素。迫使其重新排序的元素,需要提供一个 key 的特殊属性:
	* ```<div v-for="item in items" :key="item.id">{{ item.text }</div>
	```
* v-bind / :
	* ``` <!-- 绑定一个属性 -->
	<img v-bind:src="imageSrc">
	<!-- 缩写 -->
	 <img :src="imageSrc">
	<!-- 字符串拼接 -->
	<img :src="'/path/to/images/' + fileName">
	<!-- class 绑定 -->
	<div :class="{ red: isRed }"></div>
	<div :class="[classA, classB]"></div>
	<div :class="[classA, { classB: isB, classC: isC }]">
	<!-- style 绑定 -->
	<div :style="{ fontSize: size + 'px' }"></div>
	<div :style="[styleObjectA, styleObjectB]"></div>
	<!-- 绑定一个有属性的对象 -->
	<div v-bind="{ id: someProp, 'other-attr': otherProp }">  </div>
	<!-- 通过 prop 修饰符绑定 DOM 属性 -->
	<div v-bind:text-content.prop="text"></div>
	<!-- prop 绑定. “prop” 必须在 my-component 中声明。 -->
	<my-component :prop="someThing"></my-component>
	``` 
	* 动态地绑定一个或多个attr或prop属性。简单的说就是，通过v-bind绑定一个属性，这个属性可以是html原本有的，也可以是某些特殊的，绑定的表达式的值赋值给这个属性。
	
	* 在绑定 class 或 style 特性时，支持其它类型的值，如数组或对象。

	* 在绑定 prop 时，prop 必须在子组件中声明。

	* 没有参数时，可以绑定到一个包含键值对的对象。注意此时 class 和 style 绑定不支持数组和对象。
* v-on / @ 
	* ```<!-- 方法处理器 -->
	<button v-on:click="doThis"></button>
	<!-- 内联语句 -->
	<button v-on:click="doThat('hello', $event)"></button>
	<!-- 缩写 -->
	<button @click="doThis"></button>
	<!-- 停止冒泡 -->
	<button @click.stop="doThis"></button>
	<!-- 阻止默认行为 -->
	<button @click.prevent="doThis"></button>
	<!-- 阻止默认行为，没有表达式 -->
	<form @submit.prevent></form>
	<!-- 串联修饰符 -->
	<button @click.stop.prevent="doThis"></button>
	<!-- 键修饰符，键别名 -->
	<input @keyup.enter="onEnter">
	<!-- 键修饰符，键代码 -->
	<input @keyup.13="onEnter">
	<!-- 点击回调只会触发一次 -->
	<button v-on:click.once="doThis"></button>
	 ```
	
	* 绑定事件监听器。事件类型由参数指定。表达式可以是一个方法的名字或一个内联语句，如果没有修饰符也可以省略。
	
	* 用在普通元素上时，只能监听 原生 DOM 事件。用在自定义元素组件上时，也可以监听子组件触发的自定义事件。
	
	* 在监听原生 DOM 事件时，方法以事件为唯一的参数。如果使用内联语句，语句可以访问一个 $event 属性：` v-on:click="handle('ok', $event)"`。
	*  修饰符
		* .stop - 调用 event.stopPropagation()。
		* .prevent - 调用 event.preventDefault()。
		* .capture - 添加事件侦听器时使用 capture 模式。
		* .self - 只当事件是从侦听器绑定的元素本身触发时才触发回调。
		* .{keyCode | keyAlias} - 只当事件是从特定键触发时才触发回调。
		* .native - 监听组件根元素的原生事件。
		* .once - 只触发一次回调。
		* .left - (2.2.0) 只当点击鼠标左键时触发。
		* .right - (2.2.0) 只当点击鼠标右键时触发。
		* .middle - (2.2.0) 只当点击鼠标中键时触发。 
* v-model
	* `<input v-model="something">`其实是`<input v-bind:value="something" v-on:input="something = $event.target.value">`的语法糖，也就是说v-model本身就已经包含了`v-bind`，所以当`v-bind:value, v-on:input`和`v-model`同时出现在一个input上时，这个v-bind, v-on会失效。理解这一点很重要。
* v-pre
	* 不进行模板编译的部分。
	* `<span v-pre>{{ this will not be compiled }}</span>`
* v-once
	* once让渲染失去了数据绑定机制，只有在第一次渲染的时候，会获取当时的变量对应的数据进行渲染，渲染结束之后，将不再跟着数据变化而变化。

	* 这个指令有的时候非常有用。比如一些组件并不需要跟着数据变化而变化的时候。 
* v-cloak
	* 这个指令保持在元素上直到关联实例结束编译。和 CSS 规则如 `[v-cloak] { display: none } `一起用时，这个指令可以隐藏未编译的 Mustache 标签直到实例准备完毕。 
	* ```[v-cloak] {
		  display: none;
	}
	<div v-cloak>
		  {{ message }}
	</div>
	```
	* 编译结束的时候，就把这个属性移除掉。所以上面那个css，使得这些有v-cloak属性的元素全部先隐藏起来，最后当v-cloak被移除的时候，这些元素就显示出来了。为什么要有这么一个奇怪的属性呢？因为你要知道，{{message}}在原始的html中是字符串，是会显示在界面中的。如果你的浏览器编译模板速度慢，或者用户网速慢，vue代码半天没加载完，那么用户就会看到这些奇怪的{{}}，而如果使用一个v-cloak属性，就可以把这些内容事先隐藏起来，不让用户看到。
	
### 8. 自定义事件

* vue提供了四个和事件相关的实例方法，分别是`$on, $once, $off, $emit`，用过jquery的同学应该非常熟悉了。

* 对于事件绑定而言，vue内部直接提供了非指令的事件系统，跟jquery的用法几乎一样，v-on主要是在模板中绑定DOM原生的一些事件，而`$on, $once`可以绑定自定义的事件，两者互不干扰。`$emit`也无法触发原生的DOM事件。

	*  **`$on`**
		* 监听当前实例上的自定义事件。事件可以由vm.$emit触发。回调函数会接收所有传入事件触发函数的额外参数。
		*  ```vm.$on('test', function (msg) {
			  console.log(msg)
			})
			vm.$emit('test', 'hi')
			// -> "hi"
			```
	* **`$once`**
		* 监听一个自定义事件，但是只触发一次，在第一次触发之后移除监听器。
	> `$on`和`$once`绑定的事件是在实例上，而非DOM元素上，所以它们跟DOM原生的事件是两回事。DOM原生事件是在触发DOM元素特定事件时被触发的，比如`click`。但是对于这里的实例`vm`而言，`click`没有来源，实例根本不存在被`click`之说，所以`$on`和`$once`跟DOM原生事件扯不上任何关系。同理，`$emit`也是作用于实例之上，既然实例跟原生的DOM事件扯不上关系，那么`$emit`也就跟原生DOM事件扯不上关系了。
所以说，`$on`和`$once`绑定的是一个自定义事件，这些事件是存储在vue内部的事件管理器中，跟DOM事件是两码事，既然如此，跟v-on事件绑定也就是两回事。

	* **`$off`**
		* 移除事件监听器。
			* 如果没有提供参数，则移除所有的事件监听器；
			* 如果只提供了事件，则移除该事件所有的监听器；
			* 如果同时提供了事件与回调，则只移除这个回调的监听器。
	* **`$emit` **
		* 触发当前实例上的事件。附加参数都会传给监听器回调。 

### 9.组件

>组件（Component）是 Vue.js 最强大的功能之一。组件可以扩展 HTML 元素，封装可重用的代码。在较高层面上，组件是自定义元素， Vue.js 的编译器为它添加特殊功能。在有些情况下，组件也可以是原生 HTML 元素的形式，以 is 特性扩展。
在开发层面上，你只需要使用`Vue.extend({...})`就可以得到一个组件。而开发的大部分工作，就是写好大括号里面的内容。

#### 创建一个组件

上面说过了，开发层面上，创建一个组件只需要使用Vue.extend，如下：

```
var MyComponent = Vue.extend({
  template: '<div>OK</div>',
})
```
这样就创建了一个组件构造器MyComponent。实际上，`Vue.extend`的结果是Vue的一个子类。既然是一个类，就可以被实例化，通过实例化得到一个组件实例。组件构造器是一个类，可以被实例化为组件实例。

```
var component1 = new MyComponent()
```
关于`Vue.extend`，我们会在后文继续深入。组件实例有一些方法可以用，例如`$mount`方法，这也在后面的`Vue.extend`部分去解释。

#### 注册一个组件

```
// 局部注册
new Vue({
  el: '#app',
  template: '<div><my-component></my-component></div>',
  components: {
    'my-component': MyComponent,
  },
})
```
```
// 全局注册
Vue.component('my-component', MyComponent)
```
全局注册有一个好处，就是可以在任何一个vue实例中去使用。

#### 使用一个注册好的组件

使用一个组件（比如在模板中插入`<my-component>`元素）的本质，是创建一个组件实例。也就是说，一个`<my-component>`就是一个组件实例，它们共享一个组件构造器（一个js类）。

#### 简易注册

上面我们的做法是先创建一个组件构造器，然后把组件构造器传入实例构造器。在实践开发中，基本不会这样干，我们大部分都会使用简易方式直接注册，跳过创建步骤，相当于注册过程会自动创建一个匿名的组件构造器：

```
Vue.component('my-component', {
  template: '<div>OK</div>',
})
```

#### data必须是函数

之所以必须是函数是由于js本身的特性（引用）带来的。为了不使组件的实例化函数相互影响，data必须是函数。这个我觉得有点像闭包一样，我们需要一个闭包来保护变量不受外部污染，使实例的data属性都是独立的不相互影响。（这是我的理解，不知道对错哈）

#### prop
一说到prop就要提一提和它很像又经常容易搞混的attrbution。这两个的区别就是attribution往往是固定的值的属性，而prop往往是动态的状态值属性。最简单的例子就是`input checkbox`的`checked`属性，`checked`属性的attr值是它的初始值，而prop值是它的当前值，这对于熟悉jquery的同学而已应该比较好理解。

#### 为组件声明props属性

vue里面，组件实例的作用域是孤立的。这意味着不能(也不应该)在子组件的模板内直接引用父组件的数据。要让子组件使用父组件的数据，我们需要通过子组件的props选项。

子组件要显式地用 props 选项声明它期待获得的数据：

```
Vue.component('child', {
  // 声明 props
  props: ['message'],
  // 就像 data 一样，prop 可以用在模板内
  // 同样也可以在 vm 实例中像 “this.message” 这样使用
  template: '<span>{{ message }}</span>'
})
```
#### prop属性命名注意点
属性名在注册组件时使用的是驼峰式命名方式。
```
Vue.component('child', {
  props: ['myMessage'],
  template: '<span>{{ myMessage }}</span>'
})
```
但是在使用组件的时候，传入的属性名得是短横线隔开的：
```
<child my-message="hello!"></child>
```
这是因为html不区分大小写，你写成<child myMessage="hello!"></child>的话，假如有一个属性名是mymessage怎么办？所以，一定要注意这一点。

#### 事件反馈
前面一节指出，子组件不能自己主动修改prop，以防改变了父组件或app的值导致bug。那么如果子组件确实需要上级应用修改这些变量，让自己更好的适应新情况怎么办？那就让父组件或app自己来改。可是父组件怎么知道自己要改？**通过子组件的事件就可以做到了**。

我们可以在子组件里面触发事件，在父组件里面监听这个事件，当事件被触发时，父组件通过绑定的回调函数来执行prop的更改。当然除了更改prop，还可以做其他的事情。

####只能使用v-on绑定自定义事件

前文我们指出，直接用实例的`$on`方法来绑定自定义事件。但是在这里不行，不能直接在父组件里面直接用`$on`，因为我们使用`<my-component>`实例化组件时，没有得到一个变量用来存储实例化对象，它相当于是匿名的，所以我们找不到它，当然也找不到它的`$on`。

那怎么办？**只能使用v-on来进行事件绑定**。
```
<child @clicked="showSomething"></child>
```
这里的clicked是事件名，在子组件里面，通过`this.$emit('clicked')`触发事件。父组件里面，通过上面代码中的`@clicked="showSomething"`监听这个事件，而`showSomething`就是事件回调函数，是父组件methods方法之一。

#### 使用v-model实现input双向绑定

上面不是说只能使用v-on绑定事件吗？是的，但是你是否还记得前文提到过v-model其实是v-bind和v-on的语法糖？上一节我们说过了，`<child>`可以使用v-bind，而这里又说可以使用v-on，所以只要情况允许，就可以使用v-model。

所谓情况允许，是指符合下面条件：

* 接受一个 value 属性
* 在有新的 value 时触发 input 事件
```
Vue.component('child', {
  template: '<input :value="value" @keyup="update($event.target.value)">', // 
  props: ['value'],
  methods: {
    update(value) {
      this.$emit('input', value)
    },
  },
})
```
在父组件中才可以这样使用：
```
<child v-model="someData"></child>
// 等价于：
<child :value="someData" @input="someData = $event.target.value"></child>
```
蓝色部分表示的是v-bind部分，红褐色部分表示v-on部分。组件内部，绿色的keyup是input元素的DOM原生事件，红色的udpate是回调函数，当keyup的时候执行update()，而update()的时候就`$emit('input')`，触发了父组件的`v-on:input`。

基于这种原理，不一定要使用在input输入框上，实际上，任何元素都可以模拟这种方式实现数据双向绑定。当然，如果没有输入，双向绑定的说法就很奇怪。

####组件小结
组件的内容还有很多，但是项目中常用的就是组建的两个特性，一个是隔离性，父子组件通信的方式使得组件具有隔离性，主要表现在两个方面：

*  1.组件通过props属性获取父组件或app层面传来的数据，这些数据不应该被直接修改，也就是说这些数据仅属于组件的上一层，而不属于当前组件，组件不能通过修改这些数据来影响上一层；
* 2.组件不能直接对父组件或app层产生影响，但是可以通过事件绑定对上一层进行事件通知，上一层接收到这些通知时，自己决定是否要进行变化。

组件的另一个特性就是可复用性。基于上面两个方面的特性，组件应该是具备高可复用性的，当一个地方需要使用这个组件时，只需要实例化，并给适合的props即可。只要props给的符合要求，组件就可以根据自己的逻辑运行，既不受外界影响，也不影响外界。











>本篇[读书笔记](http://www.yuqingsun.com)是对唐霜的编写的这本[vue.js中文教程](http://www.tangshuang.net/3512.html#title-2-1)的整理分析。个人觉得这本书（也可以称之为长博客吧）编写的很接地气，比官网更通俗一些。
>
> Written with [StackEdit](https://stackedit.io/).