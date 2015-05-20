
title: Riot developer guide
subtitle: Guide
description: Developing client-side applications with Riot
minify: false

完成度100%

====

# 自定义标签示例

Riot 自定义标签是构建用户界面的单元。它们构成了应用的"视图"部分。我们先从一个实现TODO应用的例子来感受一下Riot的各个功能:

```html
<todo>

  <h3>{ opts.title }</h3>

  <ul>
    <li each={ items }>
      <label class={ completed: done }>
        <input type="checkbox" checked={ done } onclick={ parent.toggle }> { title }
      </label>
    </li>
  </ul>

  <form onsubmit={ add }>
    <input name="input" onkeyup={ edit }>
    <button disabled={ !text }>Add #{ items.length + 1 }</button>
  </form>

  <script>
    this.disabled = true

    this.items = opts.items

    edit(e) {
      this.text = e.target.value
    }

    add(e) {
      if (this.text) {
        this.items.push({ title: this.text })
        this.text = this.input.value = ''
      }
    }

    toggle(e) {
      var item = e.item
      item.done = !item.done
      return true
    }
  </script>

</todo>
```

自定义标签会被 [编译](compiler.md) 成 JavaScript.

参阅 [在线示例](http://muut.github.io/riotjs/demo/), 也可以浏览 [代码](https://github.com/muut/riotjs/tree/gh-pages/demo), 或下载[zip包](https://github.com/muut/riotjs/archive/gh-pages.zip).



### 标签语法

Riot标签是布局（HTML）与逻辑（JavaScript）的组合。以下是基本的规则:

* 先定义HTML，然后将逻辑包含在一个可选的  `<script>` 标签里. *注意: 如果在document body里包含标签定义，则不能使用 script 标签，它只能用于外部标签文件中*
* 如果不写 `<script>` 标签，则最后一个 HTML 标签结束的位置被认为是 JavaScript 代码是开始。
* 自定义标签可以是空的，可以只有HTML，也可以只有 JavaScript
* 标签属性值的引号是可选的: `<foo bar={ baz }>` 会被理解成 `<foo bar="{ baz }">`.
* 支持ES6 方法语法: `methodName()` 会被理解成 `this.methodName = function()` ，`this` 变量总是指向当前标签实例。
* 可以使用针对 class 名的简化语法: 当 `done` 的值是 true时，`class={ completed: done }` 将被渲染成 `class="completed"`。
* 如果表达式的值不为真值，则布尔型的属性(checked, selected 等等..)将不被渲染: `<input checked={ undefined }>` 的渲染结果是 `<input>`.
* 所有的属性名必须是 *小写*.
* 支持自关闭标签: `<div/>` 等价于 `<div></div>`. 那些众所周知的 "不关闭标签" 如 `<br>`, `<hr>`, `<img>` or `<input>` 在编译后总是不关闭的。
* 自定义标签需要被关闭（正常关闭，或自关闭）。
* 标准 HTML tags (`label`, `table`, `a` 等..) 也可以被自定义，但并不建议这么做。



自定义标签文件里的标签定义总是从某一行的行首开始：

```html
<!-- 正确 -->
<my-tag>

</my-tag>

<!-- 正确 -->
<my-tag></my-tag>

  <!-- 错误，没有从行首开始 -->
  <my-tag>

  </my-tag>
```

内置标签定义(定义在 document body中) 必须正确缩进，所有的自定义标签拥有相同的最低级的缩进级别, 不建议将tab与空格混合使用。

### 省略 script 标签

可以省略 `<script>` 标签:

```html
<todo>

  <!-- 布局 -->
  <h3>{ opts.title }</h3>

  // 逻辑
  this.items = [1, 2, 3]

</todo>
```

这种情况下，逻辑从最后一个 HTML 标签结束处开始，本站的示例中更多地使用这种“开放语法”。


### 预处理

可以使用 `type` 属性来指定预处理器. 例如:

```html
<script type="coffeescript">
  # 标签逻辑
</script>
````

现在可选的`type`值包括 "coffeescript", "typescript", "es6" 和 "none". 也可以为 `language` 加上 "text/" 前缀, 如 "text/coffeescript".

参阅 [预处理器](compiler.html#预处理器) 获取更多细节。


### 标签css

在标签内部可以放置一个 `style` 标签. Riot.js 会自动将它提取出来并放到 `<head>` 部分.

```html
<todo>

  <!-- 布局 -->
  <h3>{ opts.title }</h3>

  <style>
    todo { display: block }
    todo h3 { font-size: 120% }
    /** 本标签其它专有的css **/
  </style>

</todo>
```

### 局部 CSS

还支持[局部 CSS](https://developer.mozilla.org/en-US/docs/Web/CSS/:scope) 。 下面的例子与前一个等价。

```html
<todo>

  <!-- 布局 -->
  <h3>{ opts.title }</h3>

  <style scoped>
    :scope { display: block }
    h3 { font-size: 120% }
    /** 本标签其它专有的css **/
  </style>

</todo>
```

css的提取和移动只执行一次，无论此自定义标签被初始化多少次。

## 加载

自定义标签实例被创建后，就可以象这样将其加载到页面上：


``` html
<body>

  <!-- 将自定义标签放在body内部的任何地方 -->
  <todo></todo>

  <!-- 引入 riot.js -->
  <script src="riot.min.js"></script>

  <!-- 引入标签定义文件 -->
  <script src="todo.js" type="riot/tag"></script>

  <!-- 加载标签实例 -->
  <script>riot.mount('todo')</script>

</body>
```

放置在页面 `body` 中的自定义标签必须使用正常关闭方式: `<todo></todo>` ，自关闭的写法: `<todo/>` 不支持。


`mount` 方法的使用方法:

``` js
// mount 页面中所有的自定义标签
riot.mount('*')

// mount 自定义标签到指定id的html元素
riot.mount('#my-element')

// mount 自定义标签到选择器选中的html元素
riot.mount('todo, forum, comments')
```

一个html文档中可以包含一个自定义标签的多个实例。

`mount` 方法的第二个参数用来传递标签参数

```js
<script>
riot.mount('todo', { title: 'My TODO app', items: [ ... ] })
</script>
```

参数可以是任何数据，从简单的对象到完整的应用API。也可以是一个Flux数据仓库。这取决于应用架构的设计。

在标签内部，通过 `opts` 变量来访问这些参数，如下:

```js
<my-tag>

  <!-- 在HTML中访问参数 -->
  <h3>{ opts.title }</h3>

  // 在 JavaScript 中访问参数
  var title = opts.title

</my-tag>
```

### Mixin

Mixin 可以将公共代码在不同标签之间方便地共享。

```js
var OptsMixin = {
    init: function() {
      this.on('updated', function() { console.log('Updated!') })
    }

    getOpts: function() {
        return this.opts
    },

    setOpts: function(opts, update) {
        this.opts = opts

        if(!update) {
            this.update()
        }

        return this
    }
}

<my-tag>
    <h1>{ opts.title }</h1>

    this.mixin(OptsMixin)
</my-tag>
```

上例中，我们为所有 `my-tag` 标签实例混入了 `OptsMixin` ，它提供 `getOpts` 和 `setOpts` 方法. `init` 是个特殊方法，用来在标签载入时对mixin进行初始化。 (`init` 方法不能在其它方法里访问)

```
var my_tag_instance = riot.mount('my-tag')[0]

console.log(my_tag_instance.getOpts()) //will log out any opts that the tag has
```

标签的mixin可以是 object -- `{'key': 'val'}` `var mix = new function(...)` -- 混入任何其它类型的东西将报错.

`my-tag` 定义现在又加入了一个 `getId` 方法.

```
function IdMixin() {
    this.getId = function() {
        return this._id
    }
}

var id_mixin_instance = new IdMixin()

<my-tag>
    <h1>{ opts.title }</h1>

    this.mixin(OptsMixin, id_mixin_instance)
</my-tag>
```

由于定义在标签这个级别，mixin不仅仅扩展了你的标签的功能, 也能够在重复的界面中使用. 每次标签被加载时，即使是子标签, 标签实例也获得了mixin中的代码功能.

### 共享 mixin

为了能够在文件之间和项目之间共享mixin，提供了 `riot.mixin` API. 你可以象这样全局性地注册mixin :

```javascript
riot.mixin('mixinName', mixinObject)
```

用 `mixin()` 加上mixin名字来将mixin混入标签.

```
<my-tag>
    <h1>{ opts.title }</h1>

    this.mixin('mixinName')
</my-tag>
```

### 标签生命周期 

自定义标签的创建过程是这样的:

1. 创建标签实例
2. 标签定义中的JavaScript被执行
3. HTML 中的表达式被首次计算并首次触发 "update" 事件
4. 标签被加载 (mount) 到页面上，触发 "mount" 事件

加载完成后，表达式会在以下时机被更新:

1. 当一个事件处理器被调用（如上面ToDo示例中的`toggle`方法）后自动更新。你也可以在事件处理器中设置 `e.preventUpdate = true` 来禁止这种行为。
2. 当前标签实例的 `this.update()` 方法被调用时
3. 当前标签的任何一个祖先的 `this.update()` 被调用时. 更新从父亲到儿子单向传播。
4. 当 `riot.update()` 方法被调用时, 会更新页面上所有的表达式。

每次标签实例被更新，都会触发 "update" 事件。

由于表达式的首次计算发生在加载之前，所以不会有类似 `<img src={ src }>` 计算失败之类的意外问题。


### 监听生命周期事件

在标签定义内部可以这样监听各种生命周期事件：


``` js
<todo>

  this.on('mount', function() {
    // 标签实例被加载到页面上以后
  })

  this.on('update', function() {
    // 允许在更新之前重新计算上下文数据
  })

  this.on('unmount', function() {
    // 标签实例被从页面上删除后
  })

  // 监听多个事件的方法
  this.on('mount update unmount', function(eventName) {
    console.info(eventName)
  })

</todo>
```

对同一个事件可以有多个事件监听器. 参阅 [observable](api/observable.md) 获取关于事件的更多细节。


## 表达式

在 HTML 中可以混合写入表达式，用花括号括起来：

``` js
{ /* 某个表达式 */ }
```

表达式可以放在html属性里，也可以作为文本结点嵌入：

``` html
<h3 id={ /* 属性表达式 */ }>
  { /* 嵌入表达式 */ }
</h3>
```

表达式是 100% 纯 JavaScript. 一些例子:

``` js
{ title || 'Untitled' }
{ results ? 'ready' : 'loading' }
{ new Date() }
{ message.length > 140 && 'Message is too long' }
{ Math.round(rating) }
```

建议的设计方法是使表达式保持最简从而使你的HTML尽量干净。如果你的表达式变得太复杂，建议你考虑将它的逻辑转移到 "update" 事件的处理逻辑中. 例如:


```js
<my-tag>

  <!-- `val` 的值在下面的代码中计算 .. -->
  <p>{ val }</p>

  // ..每次更新时计算
  this.on('update', function() {
    this.val = some / complex * expression ^ here
  })
</my-tag>
```


### 布尔属性

如果表达式的值为非真，则布尔属性 (checked, selected 等..) 将不被渲染:

`<input checked={ null }>` 渲染为 `<input>`.

W3C 声称布尔属性只要存在，其值就是 true - 即使它的 value 是空的或者 `false`

下面的代码无法如预期的工作:

``` html
<input type="checkbox" { true ? 'checked' : ''}>
```

因为Riot只支持属性（值）表达式和嵌入的文本表达式. Riot 能够识别 44 种不同的布尔属性。


### Class 属性简化写法

Riot 为 CSS class 名称提供了特殊语法. 例如:

``` js
<p class={ foo: true, bar: 0, baz: new Date(), zorro: 'a value' }></p>
```

中的表达式被计算为 "foo baz zorro". 表达式中为真值的属性名会被加入到class名称列表中. 这种用法并不限于用在计算class名称的场合。


### 花括号转义

用以下的写法来对花括号进行转义：

`\\{ this is not evaluated \\}` 输出为 `{ this is not evaluated }`


### 自定义括号

你可以随自己的喜欢来自定义括号. 例如:

``` js
riot.settings.brackets = '${ }'
riot.settings.brackets = '\{\{ }}'
```

起始括号和终止括号之间用空格分隔。

如果使用 [预编译](compiler.md#预编译) ，必须提前指定 `brackets` 参数.



### 其它

 `style` 标签中的表达式将被忽略.


### 渲染原始HTML

Riot 表达式只能渲染不带HTML格式的文本值。如果真的需要，可以写一个自定义标签来做这件事. 例如:

``` js
<raw>
  <span></span>

  this.root.innerHTML = opts.content
</raw>
```

这个标签定义以后，可以被用在其它的标签里. 例如

```js
<my-tag>
  <p>原始HTML: <raw content="{ html }"/> </p>

  this.html = 'Hello, <strong>world!</strong>'
</my-tag>
```

[jsfiddle上的demo](http://jsfiddle.net/23g73yvx/)

**警告** 这可以会导致用户遭受XSS攻击，所以请确保你不从不受信任的来源加载数据。



## 嵌套的标签

我们来定义一个父标签 `<account>` ，其中嵌套一个标签 `<subscription>`:


```js
<account>
  <subscription  plan={ opts.plan } show_details="true" />
</account>


<subscription>
  <h3>{ opts.plan.name }</h3>

  // 取得参数
  var plan = opts.plan,
      show_details = opts.show_details

  // 访问父标签实例
  var parent = this.parent

</subscription>
```

如果在页面上加载 `account` 标签，带 `plan` 参数:

```js
<body>
  <account></account>
</body>

<script>
riot.mount('account', { plan: { name: 'small', term: 'monthly' } })
</script>
```

父标签的参数通过 `riot.mount` 方法的参数设置，而子标签的参数通过标签属性来传递.

**重要** 嵌套的标签只能定义在自定义父标签里，如果定义在页面上，将不会被初始化。

### 嵌套 HTML

以下是页面上一个包含嵌入HTML的自定义标签的例子:

```
<body>
  <my-tag>
    <h3>Hello world!</h3>
  </my-tag>
</body>

<script>
riot.mount('my-tag')
</script>
```

有个很酷的方法能够让你访问内部HTML，自定义一个 `inner-html` 标签:

```
<my-tag>
  <p>Some tag specific markup</p>
  <!-- here comes the inner HTML defined on the page -->
  <inner-html/>
</my-tag>
```

以下是 `inner-html` 的代码:

```
<inner-html>
  var p = this.parent.root
  while (p.firstChild) this.root.appendChild(p.firstChild)
</inner-html>
```

这个标签会成为Riot将来版本中 "核心标签" 的一部分.


## HTML transclusion

"HTML transclusion" 是处理页面上标签内部 HTML 的方法. 通过内置的 `<yield>` 标签来实现. 例如:


### 标签定义

```html
<my-tag>
  <p>Hello <yield/></p>
  this.text = 'world'
</my-tag>
```

### 使用方法

页面上放置自定义标签，并包含嵌套的 HTML

```html
<my-tag>
  <b>{ text }</b>
</my-tag>
```

### 结果

```html
<my-tag>
  <p>Hello <b>world</b><p>
</my-tag>
```

`yield` 的用法 参阅 [API文档](api/tags.md#yield) .

## DOM元素与name自动绑定

带有 `name` 或 `id` 属性的DOM元素将自动被绑定到上下文中，这样就可以从JavaScript中方便地访问它们：

```js
<login>
  <form id="login" onsubmit={ submit }>
    <input name="username">
    <input name="password">
    <button name="submit">
  </form>

  // 获取 HTML 元素
  var form = this.login,
    username = this.username.value,
    password = this.password.value,
    button = this.submit

</login>
```

当然，这些元素也可以在HTML中引用: `<div>{ username.value }</div>`


## 事件处理器

响应DOM事件的函数称为 "事件处理器". 这样定义:

```js
<login>
  <form onsubmit={ submit }>

  </form>

  // this method is called when above form is submitted
  submit(e) {

  }
</login>
```

以"on" (`onclick`, `onsubmit`, `oninput` 等...)开头的属性的值是一个函数名，当相应的事件发生时，此函数被调用. 函数也可以通过表达式来动态定义. 例如:


``` html
<form onsubmit={ condition ? method_a : method_b }>
```

在此函数中，`this`指向当前标签实例。当处理器被调用之后， `this.update()` 将被自动调用，将所有可能的变化体现到UI上。

如果事件的目标元素不是checkbox或radio按钮，默认的事件处理器行为是 *自动取消事件* . 意思是它总会自动调用 `e.preventDefault()` , 因为通常都需要调用它，而且容易被遗忘。如果要让浏览器执行默认的操作，在处理器中返回 `true` 就可以了.

例如，下面的submit处理器将会实际提交表单至服务器：

```js
submit() {
  return true
}
```



### 事件对象

事件处理器的第一个参数是标准的事件对象。事件对象的以下属性已经被Riot进行了跨浏览器兼容：

- `e.currentTarget` 指向事件处理器的所属元素.
- `e.target` 是发起事件的元素，与 `currentTarget` 不一定相同.
- `e.which` 是键盘事件(`keypress`, `keyup`, 等...)中的键值 .
- `e.item` 是循环中的当前元素. 参阅 [循环](#循环) 了解详情.


## 渲染条件

可以基于条件来决定显示或隐藏元素。例如:

``` html
<div if={ is_premium }>
  <p>This is for premium users only</p>
</div>
```

同样, 渲染条件中的表达式也可以是一个简单属性，或一个完整的 JavaScript 表达式. 有以下选择：

- `show` – 当值为真时用 `style="display: ''"` 显示元素
- `hide` – 当值为真时用 `style="display: none"` 隐藏元素
- `if` – 在 document 中添加 (真值) 或删除 (假值) 元素

判断用的操作符是 `==` 而非 `===`. 例如: `'a string' == true`.


## 循环

循环是用 `each` 属性来实现的:

```js
<todo>
  <ul>
    <li each={ items } class={ completed: done }>
      <input type="checkbox" checked={ done }> { title }
    </li>
  </ul>

  this.items = [
    { title: 'First item', done: true },
    { title: 'Second item' },
    { title: 'Third item' }
  ]
</todo>
```

定义 `each` 属性的html元素根据对数组中的所有项进行重复。 当数组使用 `push()`, `slice()` or `splice` 方法进行操作后，新的元素将被自动添加或删除，


### 上下文

循环中的每一项将创建一个新的上下文，从其中通过 `parent` 变量来访问上级上下文. 例如:


```js
<todo>
  <div each={ items }>
    <h3>{ title }</h3>
    <a onclick={ parent.remove }>Remove</a>
  </div>

  this.items = [ { title: 'First' }, { title: 'Second' } ]

  remove(event) {

  }
</todo>
```

在循环元素中，除了 `each` 属性外，其它都属于子上下文, 因此 `title` 可以被直接访问而 `remove` 需要从 `parent.` 中访问，因为remove方法并不是循环元素的属性.

每一个循环项都是一个 [标签实例](api/tags.md#标签实例). Riot 不会修改原始数据项，因此不会为其添加新的属性。


### 循环项的事件处理器

事件处理器中可以通过 `event.item` 来访问单个集合项. 下面我们来实现 `remove` 函数:

```js
<todo>
  <div each={ items }>
    <h3>{ title }</h3>
    <a onclick={ parent.remove }>Remove</a>
  </div>

  this.items = [ { title: 'First' }, { title: 'Second' } ]

  remove(event) {

    // 循环项
    var item = event.item

    // 在集合中的索引
    var index = this.items.indexOf(item)

    // 从集合中删除
    this.items.splice(index, 1)
  }
</todo>
```

事件处理器被执行后，当前标签实例会自动调用 `this.update()` （你也可以在事件处理器中设置 `e.preventUpdate = true` 来禁止这种行为）从而导致所有循环项也被更新. 父亲会发现集合中被删除了一项，从而将对应的DOM结点从document中删除。


### 循环自定义标签

自定义标签也可以被循环. 例如:

``` html
<todo-item each="{ items }" data="{ this }"></todo-item>
```

当前循环项可以用 `this` 来引用，你可以用它来将循环项作为一个参数传递给循环标签。


### 非对象数组

数组元素不要求是对象. 也可以是字符串或数字. 这时可以用 `{ name, i in items }` 写法:


```js
<my-tag>
  <p each="{ name, i in arr }">{ i }: { name }</p>

  this.arr = [ true, 110, Math.random(), 'fourth']
</my-tag>
```

`name` 是元素的名字，`i` 是索引. 这两个变量的变量名可以自由选择。


### 对象循环

也可以对普通对象做循环. 例如:

```js
<my-tag>
  <p each="{ name, value in obj }">{ name } = { value }</p>

  this.obj = {
    key1: 'value1',
    key2: 1110.8900,
    key3: Math.random()
  }
</my-tag>
```

不太建议使用对象循环，因为在内部实现中，Rio使用 `JSON.stringify` 来探测对象内容的改变. *整个* 对象都会被检查，只要有一处改变，整个循环将会被重新渲染. 会很慢. 普通的数组要快得多，而且只有变化的部分会在页面上体现。


## 使用标准 HTML 元素作为标签 | #riot-tag

页面body中的标准 HTML 元素也可以作为riot标签来使用，只要加上 `riot-tag` 属性.

```html
<ul riot-tag="my-tag"></ul>
```

这为用户提供了一种选择，与css框架的兼容性更好. 这些标签将被与其它自定义标签一样进行处理。

```js
riot.mount('my-tag')
```

会将 `my-tag` 标签 加载到 `ul` 元素上。

## 服务端渲染 | #server-side

Riot 支持服务端渲染，使用 Node/io.js 可以方便地引用标签定义并渲染成 html:

```js
var riot = require('riot')
var timer = require('timer.tag')

var html = riot.render(timer, { start: 42 })

console.log(html) // <timer><p>Seconds Elapsed: 42</p></timer>
```

循环和条件渲染都支持.

# 设计理念 


## 提供工具，而不是策略

Riot 提供了自定义标签，事件触发器 (observable) 和路由功能. 我们相信这些是前端应用的基本组成单元：

1. 自定义标签用于用户界面,
2. 事件用于模块化
3. 路由用于 URL 管理和回退按钮支持.

Riot 尽量不使用强制的规则，而是提供最基本的工具，希望你能够有创意地使用它们。这种灵活的方式将应用层面的大的架构决策交还给开发者。

我们还认为这些组成单元应该在文件大小和API的数量上最小化。基本的东西应该是简单的，将认知的负担降到最低。


## Observable

Observable 是发送和接收消息的一般化工具。它是区分不同模块，减少依赖或“耦合”的常用模式。使用事件可以将大的程序分解成更小更简单的单元。可以添加、删除、修改各个模块而不影响应用的其它部分。

一种常用实践是将应用划分成单一的核心和多个扩展。这个核心在某些事情发生时（添加了新项，旧项被删除，或从服务端获取了数据）触发事件。

通过使用 observable，扩展部分可以监听这些事件并对其进行响应。核心并不知道这些扩展模块的存在。这称为“松耦合”。

这些扩展可以是自定义标签 (UI 组件) 或 非 UI 模块.

只要慎重设计好核心部分和事件接口，团队成员就可以各自独立开发，而互相不打扰。

[Observable API](api/observable.md)


## Routing

路由器是管理URL和浏览器后退按钮的一般化工具。Riot的路由器是你能找到的最小实现，兼容包括IE8在内的众多浏览器。它做以下这些事情:

1. 修改 URL 的 hash 部分
2. hash 变化时进行通知
3. 查看当前 hash 

路由的逻辑可以放在任何地方; 在自定义标签中或 非UI模块中. 有些应用框架将路由器作为一个中央模块，由它将任务派发给应用的其它部分。而有些则采取更温和的方式，将URL事件象键盘事件一样处理，不影响整体的架构。

任何浏览器应用都需要路由akce因为在地址栏里总是有一个URL的。

[路由器 API](api/router.md)


## 模块化

自定义标签构成了应用的视图部分。在模块化的应用中，这些标签不应知晓互相之间的存在，应被隔离。理想情况下，你可以在整个项目中使用同一个标签，而不论外部的HTML布局如何变化。

如果两个标签知晓彼此的存在，则称它们互相依赖，造成了“紧耦合”。这样的标签就无法在系统中四处重用。

为了减少耦合，让标签之间互相监听消息而不是进行直接调用. 你都要好好的是用 `riot.observable` 构建的 发布/订阅系统。

这个事件发布系统可以是一套简单 API，或是象 Facebook Flux 一样的大型架构决策.

### Riot 应用设计实例

以下是一个非常小的实现用户登录的Riot应用骨架结构:

```js
// Login API
var auth = riot.observable()

auth.login = function(params) {
  $.get('/api', params, function(json) {
    auth.trigger('login', json)
  })
}


<!-- login 视图 -->
<login>
  <form onsubmit="{ login }">
    <input name="username" type="text" placeholder="username">
    <input name="password" type="password" placeholder="password">
  </form>

  login() {
    opts.login({
      username: this.username.value,
      password: this.password.value
    })
  }

  // any tag on the system can listen to login event
  opts.on('login', function() {
    $(body).addClass('logged')
  })
</login>
```

加载

```html
<body>
  <login></login>
  <script>riot.mount('login', auth)</script>
</body>
```

这样，系统中其它的标签不需要知道彼此的存在，只需要监听 "login" 事件，在处理器里实现自己需要的逻辑。

Observable 是实现松耦合（模块化）应用的经典模式。
