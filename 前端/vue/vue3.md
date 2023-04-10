



# Promise



回调地狱：多层函数回调，耦合性高

解决回调地狱：使用 promise



**基本概念：**

- Promise 是一个构造函数 -> 可以通过 new 构建
  - new 出来的 Promise 实例对象，代表一个异步操作

- Promise.prototype 上包含了一个 .then() 方法  --> p.then()
  - .then() 方法可以有预先指定成功和失败的回调函数
  - 成功的回调是必选的，失败的回调函数是可选的

~~~js
// 通过new 创建 promise 实例
const  p = new Promise()

p.then(res => {},error =>{})
~~~



**Promise 支持链式调用，按顺序 执行异步**

**通过  .catch  捕获错误**

如果不希望前面的错误导致后续的 .then 无法正常执行，则可以将 .catch 的调用提前

~~~js
this.https.get('/test')	// 返回值是一个 Promise 的实例对象
.catch(err=>{	// 先捕获错误，保证执行
    consolo.log(err.message)
})
.then(res1=>{	// 通过 .then 为第一个 Promise 实例指定成功之后的回调函数
    consolo.log(res1)
    return this.https.get('/test2') // 返回 一个Promise
})
.then(res2=>{ // 为上一个 .then 的返回值（新的Promise实例）指定成功回调
    consolo.log(res2)
})
~~~



**Promise.all()** **方法**

Promise.all() 方法会发起并行的 Promise 异步操作，等所有的异步操作全部结束后才会执行下一步的 .then 

操作（等待机制）



 **Promise.race()** **方法**

Promise.race() 方法会发起并行的 Promise 异步操作，只要任何一个异步操作完成，就立即执行下一步的

.then 操作（赛跑机制）



**async/await**

简化 Promise 异步操作。在 async/await 出现之前，开发者只能通过链式 .then() 的方式处理 Promise 异步操作

~~~
async fun getInfo() {
 const data1 = await this.https.get('/test1')	
 const data2 = await this.https.get('/test2')	
}
~~~

- 如果在 function 中使用了 await，则 function 必须被 async 修饰
- 在 async 方法中，第一个 await 之前的代码会同步执行，await 之后的代码会异步执行





# webpack

 **webpack 中的默认约定**

- 默认的打包入口文件为 src -> index.js
- 默认的输出文件路径为 dist -> main.js

可以在 webpack.config.js 中修改打包的默认约定



## 配置

**自定义打包的入口与出口**

在 webpack.config.js 配置文件中，通过 entry 节点指定打包的入口。通过 output 节点指定打包的出口。

~~~javascript
// 导入path模块
const path = require('path')

// webpack配置
module.exports = {
    // ---------------- 打包模式
    mode: 'development',
    
   /* // 配置打包入口文件
    entry: path.resolve(__dirname, 'src', 'index.js'),
    // 配置打包输出位置，及文件名
    output: {
        path: path.resolve(__dirname, 'dist'),
        // 输出文件名
        filename: 'bundle.js'
     */
    }
}
~~~



# 基础语法

## 创建应用实例

- 每个 Vue 应用都是通过 [`createApp`](https://cn.vuejs.org/api/application.html#createapp) 函数创建一个新的 **应用实例**：

  ~~~vue
  import { createApp } from 'vue'
  
  const app = createApp({
    /* 根组件选项 */
  })
  ~~~

- vue 的组件结构

~~~
App (root component)
├─ TodoList
│  └─ TodoItem
│     ├─ TodoDeleteButton
│     └─ TodoEditButton
└─ TodoFooter
   ├─ TodoClearButton
   └─ TodoStatistics
~~~



**挂载应用：**

应用实例必须在调用了 `.mount()` 方法后才会渲染出来。该方法接收一个“容器”参数，可以是一个实际的 DOM 元素或是一个 CSS 选择器字符串：

~~~vue
<div id="app"></div>
app.mount('#app')
~~~

- `.mount()` 方法应该始终在整个应用配置和资源注册完成后被调用。同时请注意，不同于其他资源注册方法，它的返回值是根组件实例而非应用实例。



**应用配置：**

应用实例会暴露一个 `.config` 对象允许我们配置一些应用级的选项，例如定义一个应用级的错误处理器，用来捕获所有子组件上的错误：

~~~javascript
app.config.errorHandler = (err) => {
  /* 处理错误 */
}
~~~

应用实例还提供了一些方法来注册应用范围内可用的资源，例如注册一个组件：

~~~vue
app.component('TodoDeleteButton', TodoDeleteButton)
~~~

- 这使得 `TodoDeleteButton` 在应用的任何地方都是可用的
- 确保在挂载应用实例之前完成所有应用配置！



## 数据绑定

### 文本插值

双大括号标签会被替换为相应组件实例中 `msg` 属性的值。同时每次 `msg` 属性更改时它也会同步更新

~~~vue
<span>Message: {{ msg }}</span>
~~~



### 属性绑定

`v-bind` 指令指示 Vue 将元素的 `id` attribute 与组件的 `dynamicId` 属性保持一致。如果绑定的值是 `null` 或者 `undefined`，那么该 attribute 将会从渲染的元素上移除

~~~vue
<div v-bind:id="dynamicId"></div>
<div :id="dynamicId"></div>
~~~

- 简写为 :



**布尔型 attribute：**

依据 true / false 值来决定 attribute 是否应该存在于该元素上。[`disabled`](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/disabled) 就是最常见的例子之一。

~~~vue
<button :disabled="isButtonDisabled">Button</button>
~~~

- 当 `isButtonDisabled` 为真值或一个空字符串 (即 `<button disabled="">`) 时，元素会包含这个 `disabled` attribute。而当其为其他假值时 attribute 将被忽略。



**动态绑定多个值：**

~~~javascript
data() {
  return {
    objectOfAttrs: {
      id: 'container',
      class: 'wrapper'
    }
  }
}
<div v-bind="objectOfAttrs"></div>
~~~



**JavaScript 表达式:**

在 Vue 模板内，JavaScript 表达式可以被使用在如下场景上：

- 在文本插值中 (双大括号)
- 在任何 Vue 指令 (以 `v-` 开头的特殊 attribute) attribute 的值中

~~~vue
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div :id="`list-${id}`"></div>
~~~



## 指令

指令是带有 `v-` 前缀的特殊 attribute。



### 参数

某些指令会需要一个“参数”，在指令名后通过一个冒号隔开做标识。例如用 `v-bind` 指令来响应式地更新一个 HTML attribute：

~~~vue
<a v-bind:href="url"> ... </a>

<!-- 简写 -->
<a :href="url"> ... </a>
~~~

这里 `href` 就是一个参数，它告诉 `v-bind` 指令将表达式 `url` 的值绑定到元素的 `href` attribute 上。在简写中，参数前的一切 (例如 `v-bind:`) 都会被缩略为一个 `:` 字符。



**动态参数：**

同样在指令参数上也可以使用一个 JavaScript 表达式，需要包含在一对方括号内：

~~~vue
<!--
注意，参数表达式有一些约束，
参见下面“动态参数值的限制”与“动态参数语法的限制”章节的解释
-->
<a v-bind:[attributeName]="url"> ... </a>

<!-- 简写 -->
<a :[attributeName]="url"> ... </a>
~~~

这里的 `attributeName` 会作为一个 JavaScript 表达式被动态执行，计算得到的值会被用作最终的参数。举例来说，如果你的组件实例有一个数据属性 `attributeName`，其值为 `"href"`，那么这个绑定就等价于 `v-bind:href`。



相似地，你还可以将一个函数绑定到动态的事件名称上：

~~~vue
<a v-on:[eventName]="doSomething"> ... </a>

<!-- 简写 -->
<a @[eventName]="doSomething">
~~~

在此示例中，当 `eventName` 的值是 `"focus"` 时，`v-on:[eventName]` 就等价于 `v-on:focus`



**动态参数的限制：**

动态参数中表达式的值应当是一个字符串，或者是 `null`。特殊值 `null` 意为显式移除该绑定。其他非字符串的值会触发警告。



### v-for

基于原始数据多次渲染元素或模板块。

- 期望的绑定值类型：`Array | Object | number | string | Iterable`
- 指令值必须使用特殊语法 `alias in expression` 为正在迭代的元素提供一个别名：

~~~vue
<div v-for="item in items">
  {{ item.text }}
</div>
~~~

为索引指定别名 (如果用在对象，则是键值)：

~~~vue
<div v-for="(item, index) in items"></div>
<div v-for="(value, key) in object"></div>
<div v-for="(value, name, index) in object"></div>
~~~

`v-for` 的默认方式是尝试就地更新元素而不移动它们。要强制其重新排序元素，你需要用特殊 attribute `key` 来提供一个排序提示：

~~~vue
<div v-for="item in items" :key="item.id">
  {{ item.text }}
</div>
~~~



### v-if

- 当 `v-if` 元素被触发，元素及其所包含的指令/组件都会销毁和重构。如果初始条件是假，那么其内部的内容根本都不会被渲染。

- `v-else-if` 提供的是相应于 `v-if` 的“else if 区块”
- 当同时使用时，`v-if` 比 `v-for` 优先级更高。我们并不推荐在一元素上同时使用这两个指令

~~~vue
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
~~~



**template 中的 v-if**

因为 `v-if` 是一个指令，他必须依附于某个元素。但如果我们想要**切换不止一个元素**呢？在这种情况下我们可以在一个 `<template>` 元素上使用 `v-if`，这只是一个不可见的包装器元素，最后渲染的结果并不会包含这个 `<template>` 元素。

~~~vue
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
~~~



### v-show

另一个可以用来按条件显示一个元素的指令是 `v-show`。其用法基本一样：

~~~vue
<h1 v-show= true>Hello!</h1>
~~~

- 不同之处在于 `v-show` 会在 DOM 渲染中保留该元素；`v-show` 仅切换了该元素上名为 `display` 的 CSS 属性。

- `v-show` 不支持在 `<template>` 元素上使用，也不能和 `v-else` 搭配使用



`v-if` 是“真实的”按条件渲染，因为它确保了在切换时，条件区块内的事件监听器和子组件都会被销毁与重建。

`v-if` 也是**惰性**的：如果在初次渲染时条件值为 false，则不会做任何事。条件区块只有当条件首次变为 true 时才被渲染。

相比之下，`v-show` 简单许多，元素无论初始条件如何，始终会被渲染，只有 CSS `display` 属性会被切换。

总的来说，`v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销。

因此，**如果需要频繁切换，则使用 `v-show` 较好；如果在运行时绑定条件很少改变，则 `v-if` 会更合适。**



### v-on

给元素绑定事件监听器。

- **缩写：**`@`
- **期望的绑定值类型：**`Function | Inline Statement | Object (不带参数)`

- **修饰符：**
  - `.stop` - 调用 `event.stopPropagation()`。
  - `.prevent` - 调用 `event.preventDefault()`。
  - `.capture` - 在捕获模式添加事件监听器。
  - `.self` - 只有事件从元素本身发出才触发处理函数。
  - `.{keyAlias}` - 只在某些按键下触发处理函数。
  - `.once` - 最多触发一次处理函数。
  - `.left` - 只在鼠标左键事件触发处理函数。
  - `.right` - 只在鼠标右键事件触发处理函数。
  - `.middle` - 只在鼠标中键事件触发处理函数。
  - `.passive` - 通过 `{ passive: true }` 附加一个 DOM 事件。



当监听原生 DOM 事件时，方法接收原生事件作为唯一参数。

如果使用内联声明，声明可以访问一个特殊的 `$event` 变量：`v-on:click="handle('ok', $event)"`

`v-on` 还支持绑定不带参数的事件/监听器对的对象。请注意，当使用对象语法时，不支持任何修饰符。

~~~vue
<button @click="doThis"></button>

<!--动态事件 -->
<button @[event]="doThis"></button>

<!-- 停止传播 -->
<button @click.stop="doThis"></button>

<!-- 阻止默认事件 -->
<button @click.prevent="doThis"></button>

<!-- 不带表达式地阻止默认事件 -->
<form @submit.prevent></form>

<!-- 链式调用修饰符 -->
<button @click.stop.prevent="doThis"></button>

<!-- 按键用于 keyAlias 修饰符-->
<input @keyup.enter="onEnter" />

<!-- 点击事件将最多触发一次 -->
<button v-on:click.once="doThis"></button>

<!-- 对象语法 -->
<button v-on="{ mousedown: doThis, mouseup: doThat }"></button>

<!-- 滚动事件的默认行为 (scrolling) 将立即发生而非等待 `onScroll` 完成 -->
<!-- 以防其中包含 `event.preventDefault()` -->
<div @scroll.passive="onScroll">...</div>

~~~

- `.passive` 修饰符一般用于触摸事件的监听器，可以用来改善移动端设备的滚屏性能

监听子组件的自定义事件 (当子组件的“my-event”事件被触发，处理函数将被调用)：

- 请勿同时使用 `.passive` 和 `.prevent`，因为 `.passive` 已经向浏览器表明了你*不想*阻止事件的默认行为。如果你这么做了，则 `.prevent` 会被忽略，并且浏览器会抛出警告。

~~~vue
<MyComponent @my-event="handleThis" />

<!-- 内联声明 -->
<MyComponent @my-event="handleThis(123, $event)" />
~~~



### v-slot

用于声明具名插槽或是期望接收 props 的作用域插槽。



- **缩写：**`#`
- **期望的绑定值类型**：能够合法在函数参数位置使用的 JavaScript 表达式。支持解构语法。绑定值是可选的——只有在给作用域插槽传递 props 才需要。
- **参数**：插槽名 (可选，默认是 `default`)

- **仅限：**
  - `<template>`
  - components (用于带有 prop 的单个默认插槽)

~~~vue
<!-- 具名插槽 -->
<BaseLayout>
  <template v-slot:header>
    Header content
  </template>

  <template v-slot:default>
    Default slot content
  </template>

  <template v-slot:footer>
    Footer content
  </template>
</BaseLayout>

<!-- 接收 prop 的具名插槽 -->
<InfiniteScroll>
  <template v-slot:item="slotProps">
    <div class="item">
      {{ slotProps.item.text }}
    </div>
  </template>
</InfiniteScroll>

<!-- 接收 prop 的默认插槽，并解构 -->
<Mouse v-slot="{ x, y }">
  Mouse position: {{ x }}, {{ y }}
</Mouse>
~~~





### 其他



**v-text:**

更新元素的文本内容。

- **期望的绑定值类型：**`string`

- **详细信息**

  `v-text` 通过设置元素的 [textContent](https://developer.mozilla.org/en-US/docs/Web/API/Node/textContent) 属性来工作，因此它将覆盖元素中所有现有的内容。如果你需要更新 `textContent` 的部分，应该使用 [mustache interpolations](https://cn.vuejs.org/guide/essentials/template-syntax.html#text-interpolation) 代替。



~~~vue
<span v-text="msg"></span>
<!-- 等同于 -->
<span>{{msg}}</span>
~~~



## 计算属性





# 响应式基础

使用 `reactive()` 函数创建一个响应式对象或数组：







# 组件





## 插槽

为子组件传递一些模板片段，让子组件在它们的组件中渲染这些片段

`<FancyButton>` 组件：

~~~vue
<FancyButton>
  Click me! <!-- 插槽内容 -->
</FancyButton>
~~~

`<FancyButton>` 的模板：

~~~vue
<button class="fancy-btn">
  <slot></slot> <!-- 插槽出口 -->
</button>
~~~

- `<slot>` 元素是一个**插槽出口** (slot outlet)，标示了父元素提供的**插槽内容** (slot content) 将在哪里被渲染。

最终渲染出的 DOM 是这样：

~~~vue
<button class="fancy-btn">Click me!</button>
~~~



插槽内容可以是任意合法的模板内容，不局限于文本。例如我们可以传入多个元素，甚至是组件：

~~~vue
<FancyButton>
  <span style="color:red">Click me!</span>
  <AwesomeIcon name="plus" />
</FancyButton>
~~~

通过使用插槽，`<FancyButton>` 组件更加灵活和具有可复用性。现在组件可以用在不同的地方渲染各异的内容，但同时还保证都具有相同的样式。



**渲染作用域：**

- 插槽内容**无法访问**子组件的数据。Vue 模板中的表达式只能访问其定义时所处的作用域
- 插槽内容可以访问到父组件的数据作用域，因为插槽内容本身是在父组件模板中定义的

~~~vue
<span>{{ message }}</span>
<FancyButton>{{ message }}</FancyButton>
~~~



**默认内容：**

如果我们想在父组件没有提供任何插槽内容时在 `<button>` 内渲染“Submit”，只需要将“Submit”写在 `<slot>` 标签之间来作为默认内容：

~~~vue
<button type="submit">
  <slot>
    Submit <!-- 默认内容 -->
  </slot>
</button>
~~~



**具名插槽：**

有时在一个组件中包含多个插槽出口是很有用的。举例来说，在一个 `<BaseLayout>` 组件中，有如下模板：

~~~vue
<div class="container">
  <header>
    <!-- 标题内容放这里 -->
  </header>
  <main>
    <!-- 主要内容放这里 -->
  </main>
  <footer>
    <!-- 底部内容放这里 -->
  </footer>
</div>
~~~

对于这种场景，`<slot>` 元素可以有一个特殊的 attribute `name`，用来给各个插槽分配唯一的 ID，以确定每一处要渲染的内容：

~~~vue
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
~~~

这类带 `name` 的插槽被称为具名插槽 (named slots)。没有提供 `name` 的 `<slot>` 出口会隐式地命名为“default”。

在父组件中使用 `<BaseLayout>` 时，我们需要一种方式将多个插槽内容传入到各自目标插槽的出口。此时就需要用到**具名插槽**了：

要为具名插槽传入内容，我们需要使用一个含 `v-slot` 指令的 `<template>` 元素，并将目标插槽的名字传给该指令：

~~~vue
<BaseLayout>
  <template #header>
    <h1>Here might be a page title</h1>
  </template>

  <template #default>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </template>

  <template #footer>
    <p>Here's some contact info</p>
  </template>
</BaseLayout>
~~~

`v-slot` 有对应的简写 `#`，因此 `<template v-slot:header>` 可以简写为 `<template #header>`。将这部分模板片段传入子组件的 header 插槽中。



 **JavaScript 函数来类比：**

~~~vue
// 传入不同的内容给不同名字的插槽
BaseLayout({
  header: `...`,
  default: `...`,
  footer: `...`
})

// <BaseLayout> 渲染插槽内容到对应位置
function BaseLayout(slots) {
  return `<div class="container">
      <header>${slots.header}</header>
      <main>${slots.default}</main>
      <footer>${slots.footer}</footer>
    </div>`
}
~~~





**动态插槽名：**

~~~vue
<base-layout>
  <template v-slot:[dynamicSlotName]>
    ...
  </template>

  <!-- 缩写为 -->
  <template #[dynamicSlotName]>
    ...
  </template>
</base-layout>
~~~























































































































































































































































