
title: Riot FAQ
subtitle: FAQ
description: none

翻译完成度 100%

====

# 常见问题

### 为什么叫 Riot?
Riot 是对当前写大量 boilerplate 代码和不必要的复杂性的反动。我们认为对前端库来说，少量而强大的API加上简洁的语法是至关重要的。


### 为什么要支持 IE8?
因为其它它还在被广泛使用着. 根据 [Net Market Share](http://www.netmarketshare.com/) 全球桌面的占有率为 19% ，根据 [StatCounter](http://statcounter.com/demo/browser/) 占有率是 2.6%. 所以在很多项目中还是需要考虑[对IE8的支持](https://muut.com/riotjs/download.html#ie8-support)。

Net Market Share 的统计对每个用户单独计数，而 StatCounter 的算法给常上网的用户额外的高权重.

### Riot 免费吗?
Riot 免费，开源，使用 MIT License. 没有专利 [Additional Grant of Patent Rights](https://github.com/facebook/react/blob/master/PATENTS) 风险.


### Riot 可以用于生产吗?
我们认为可以. 我们自己的 muut.com 网站大量使用它: 注册, 论坛设置，帐号页面等等. 但不是所有的场景都被细致地测试到了, Riot 2 是一次大的重写，还没有被广泛使用. 请到 [这里](https://github.com/riotjs/riotjs/issues)提交 issues .


### 标签名中必须使用横线 (译者注：-) 吗?
W3C 规范要求在标签名中使用横线。所以 `<person>` 需要写成 `<my-person>`. 如果你关心 W3C 的话就遵循这个规则. 其实两者都能跑.


### 为什么源码中没有分号?
不写分号使代码更整洁。这与我们的整体的最小化哲学是一致的。同样的原因，我们使用单引号，如果你想为Riot贡献代码，请不要使用分号和双引号。

### 为什么使用邪恶的 `==` 运算符?
这个运算符，如果你知道它的工作原理，就没那么邪恶了，例如:

`node.nodeValue = value == null ? '' : value`

这将导致 `0` 和 `false` 被显示而 `null` 和 `undefined` 显示为空字符串。这正是我们想要的！


### 可以在 .tag 文件中使用 `style` 标签吗?
可以。可以在标签定义中使用CSS。web component标准也有一套CSS封装的机制。但是其实这不会改进对你的CSS的管理。


### jQuery是个什么角色?
Riot 减少了对jQuery的需求。你不再需要选择器，遍历，事件和dom操作功能。有些功能如delegate事件会有用. jQuery插件可以和 Riot 一起使用.


### `onclick` 不是邪恶的吗?
不是邪恶， 只是看起来“过时”。将JS和HTML放在同一个模块里比美学重要。Riot的最小化语法使事件处理器看起来很象样儿。

### 将来的计划?

有:

1. 性能提升 ( 特别是循环 )
2. 插件系统
3. 使用 HTML属性进行标签参数验证
