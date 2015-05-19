
nogen: true

翻译完成度：100%

====

# Observable

### riot.observable(el)

为 `el` 对象添加 [Observer](http://en.wikipedia.org/wiki/Observer_pattern) 支持，如果参数为空，则返回一个新创建的observable实例。之后该对象就可以触发和监听事件了。例如:

``` js
function Car() {

  // 使 Car 实例成为 observable
  riot.observable(this)

  // 监听 'start' 事件
  this.on('start', function() {
    // 发动引擎
  })

}

// 创建一个新的 Car 实例
var car = new Car()

// 触发 'start' 事件
car.trigger('start')
```

@返回值 参数 `el` 或新的observable 实例


### el.on(events, callback) | #observable-on

监听用空格分隔的 `event` 列表，每次事件被触发时调用 `callback` 

``` js
// 监听单个事件
el.on('start', function() {

})

// 监听多个事件，事件类型将作为回调函数的参数传递
el.on('start stop', function(type) {

  // type 是 'start' 或 'stop'

})
```

@返回值 `el`

### el.one(event, callback) | #observable-one

监听指定的 `event` 但只执行 `callback` 最多一次.

``` js
// 即使 'start' 被触发多次，也只执行回调函数一次
el.one('start', function() {

})
```

@返回值 `el`

### el.off(events) | #observable-off

删除参数中指定的以空格分隔的事件的监听器

``` js
el.off('start stop')
```

@返回值 `el`

### el.off(events, fn)

从给定的事件列表的监听器中删除指定的那个

``` js
function doIt() {
  console.log('starting or ending')
}

el.on('start middle end', doIt)

// 从 start 和 end 事件的监听器中删除指定的那个
el.off('start end', doIt)
```

@返回值 `el`

### el.off('*')

删除所有事件的所有监听器

@返回值 `el`


### el.trigger(event) | #observable-trigger

触发事件。执行所有监听 `event` 的回调函数

``` js
el.trigger('start')
```

@返回值 `el`

### el.trigger(event, arg1 ... argN)

执行所有监听 `event` 的回调函数，可以传递任意数量的附加参数给监听器。

``` js
// 监听 'start' 事件，并期待附加参数
el.on('start', function(engine_details, is_rainy_day) {

})

// 触发 start 事件，并传递附加参数
el.trigger('start', { fuel: 89 }, true)

```

@返回值 `el`
