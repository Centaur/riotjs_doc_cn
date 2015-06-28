
title: Riot compiler
subtitle: Compiler
description: From &lt;custom-tag> to JavaScript

翻译完成度 100%

====

## 浏览器内编译

**译者注** 根据我们的使用经验，浏览器内编译有很多问题，对调试非常不友好。因此不建议使用。这部分文档也不计划翻译。

## 预编译

在服务器上预编译有以下好处:

- 可以使用你喜爱的 [预处理器](#预处理器)
- 小小的性能优势. 不需要在浏览器里加载和执行编译器。
- "单语言应用" 以及可以在服务端预渲染标签。


预编译使用 `riot` 命令, 用 NPM 安装:

``` sh
npm install riot -g
```

运行 `riot --help` 看看是否安装成功。 你的机器上需要安装有 [node.js](http://nodejs.org/) .

使用预编译的话，你的 HTML 长这个样:

``` html
<!-- 加载点 -->
<my-tag></my-tag>

<!-- 包含 riot.js -->
<script src="//cdn.jsdelivr.net/riot/2.1/riot.min.js"></script>

<!-- 包含预编译的自定义标签 (正常 javascript) -->
<script src="path/to/javascript/with-tags.js"></script>

<!-- mount 方法一样 -->
<script>
riot.mount('*')
</script>
```

### 用法

 `riot` 命令的用法:

``` sh
# 编译到当前目录
riot some.tag

# 编译到目标目录
riot some.tag some_folder

# 编译到目标路径
riot some.tag some_folder/some.js

# 将源目录下的所有文件编译至目的目录
riot some/folder path/to/dist

# 将源目录下的所有文件编译（合并）到单个js文件
riot some/folder all-my-tags.js

```

每个源文件可以包含一个或多个自定义标签，也可以有标准JavaScript代码混在里面。编译器只会转换自定义标签，其它的内容不会动。

运行 `riot --help` 查看帮助。


### Watch模式

你可以 watch 目录，当文件有变化时自动编译

``` sh
# watch for
riot -w src dist
```


### 指定后缀名

对标签定义文件，你可以随意使用后缀名 (默认为 `.tag`):

``` sh
riot --ext html
```


### Node 模块

```js
var riot = require('riot')

var js = riot.compile(source_string)
```

compile 函数接受string参数，返回string.

### 融入你的工作流程

- [Gulp](https://github.com/e-jigsaw/gulp-riot)
- [Grunt](https://github.com/ariesjia/grunt-riot)
- [Browserify](https://github.com/jhthorsen/riotify)


## 预处理器

这是预编译的最大优势. 你可以使用你喜欢的预处理器来创建自定义标签。HTML 和 JavaScript 处理器都可以自定义。

源语言使用命令行参数 `--type` or `-t` 来指定，也可以在script标签上指定:

```html
<my-tag>
  <h3>My layout</h3>

  <script type="coffee">
    @hello = 'world'
  </script>
</my-tag>
```


### CoffeeScript

``` sh
# 使用 coffeescript 预处理器
riot --type coffee --expr source.tag
```

 `--expr` 参数表示所有的表达式也做预处理. 还可以使用 "cs" 作为 "coffee" 的别名. 以下是 CoffeeScript 自定义标签的例子:

```html
<kids>

  <h3 each={ kids[1 .. 2] }>{ name }</h3>

  # Here are the kids
  this.kids = [
    { name: "Max" }
    { name: "Ida" }
    { name: "Joe" }
  ]

</kids>
```

注意 `each` 属性也是用 CoffeeScript 写的. 你的机器上需要安装有 CoffeeScript:

``` sh
npm install coffee-script -g
```


### EcmaScript 6

ECMAScript 6 使用 "es6" type:

``` sh
# 使用 ES6 预处理器
riot --type es6 source.tag
```

ES6 自定义标签示例:

```js
<test>

  <h3>{ test }</h3>

  var type = 'JavaScript'
  this.test = `This is ${type}`

</test>
```

所有的 ECMAScript 6 [功能](https://github.com/lukehoban/es6features) 都能用. 转换过程是用 [Babel](https://babeljs.io/) 完成的:

``` sh
npm install babel
```

这是一个在Riot中使用Babel的 [稍大的例子](https://github.com/txchen/feplay/tree/gh-pages/riot_babel) .

### TypeScript

TypeScript 为 JavaScript 增加了可选的类型. 要使用它用 `--type typescript` :

``` sh
# 使用 TypeScript 预处理器
riot --type typescript source.tag
```

用 TypeScript 实现的自定义标签示例:

```js
<test>

  <h3>{ test }</h3>

  var test: string = 'JavaScript';
  this.test = test;

</test>
```

转换使用的是 [typescript-simple](https://github.com/teppeis/typescript-simple) :

``` sh
npm install typescript-simple
```
### LiveScript

参考 [LiveScript](http://livescript.net) 以了解语言特性和阅读文档.

``` sh
# use livescript pre-processor
riot --type livescript --expr source.tag
```

`--expr` 参数表示所有的表达式也要被预处理。也可以使用 "ls" 作为 "livescript" 的别名. LiveScript 的例子:

```html
<kids>

<h3 each={ kids[1 .. 2] }>{ name }</h3>

# Here are the kids
this.kids =
* name: \Max
* name: \Ida
* name: \Joe

</kids>
```

注意 `each` 属性也是 LiveScript. 你的机器上必须安装有 LiveScript :

``` sh
npm install LiveScript -g
```

### Jade

HTML 布局可以用 `template` 参数来进行预处理. 以下是使用 Jade – "用干净，空格敏感的语法写html" 的例子


``` sh
# 使用 Jade HTML 预处理器
riot --template jade source.tag
```

Jade 例子:

```jade
sample
  p test { value }
  script(type='text/coffee').
    @value = 'sample'
```

你可能注意到了，在template中也可以定义script类型. 以上使用的是 coffee. 转换使用的是 [jade](https://github.com/jadejs/jade) :

``` sh
npm install jade
```



### 任何语言

你可以写一个自定义解析函数，这样就可以使用你喜欢的语言了。例如:

``` js
function myParser(js, options) {
  return doYourThing(js, options)
}
```

解析器将被作为 `parser` 参数传递给编译器:

``` js
var riot = require('riot')

var js = riot.compile(source_string, { parser: myParser, expr: true })
```

如果希望表达式也被解析，设置 `expr: true` 。

#### 浏览器和服务器中的 riot.parsers

你也可以创建自己的riot解析器，把它加到 `riot.parsers` 中，在浏览器中和服务器上共享. 例如

```js
riot.parsers.js.myJsParser = function(js, options) {
  return doYourThing(js, options)
}

riot.parsers.css.myCssParser = function(tagName, css) {
  return doYourThing(tagName, css)
}
```

创建好自定义 `riot.parsers` 后，可以象这样使用它们来进行编译

```html
<custom-parsers>
  <p>hi</p>
  <style type="text/myJsParser">
    @tag {color: red;}
  </style>
  <script type="text/myCssParser">
    this.version = "@version"
  </script>
</custom-parsers>
```

### 不处理

Riot 默认使用内置的转换器来支持简短的 ES6- 风格的方法写法. 你可以用 `--type none` 来禁止所有的预处理:

``` sh
# 不作预处理
riot --type none --expr source.tag
```

### AMD 与 CommonJS

Riot 标签的编译可以加入 `AMD` (Asynchronous Module Definition) 和 `CommonJS` 支持. 如果 Riot 是通过 AMD 加载器如 [RequireJS](http://requirejs.org/) 或 CommonJS 加载器如 [Browserify](http://browserify.org/) 加载则需要这种配置.

无论用哪种加载器，都需要将 Riot 库以 `riot` 来 define 或 require.

``` sh
# 使用 AMD 和 CommonJS
riot --m
```

AMD 例子:

```js

define(['riot', 'tags'], function (riot) {
  riot.mount('*');
});
```

CommonJS 例子:

```js
var riot = require('riot');
var tags = require('tags');

riot.mount('*');
```

如果你有好的相关产品, 请 [共享之](https://github.com/riotjs/riotjs/issues/58) !
