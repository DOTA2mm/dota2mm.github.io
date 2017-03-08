---
title: Hexo博客Bug：“{{”符号引起的生成报错
date: 2016/5/24 
categories: 
- 技术分享
tags:
- Hexo
- Hexo bug
---
## 使用Hexo写博客的一个小插曲 ##
从发现Hexo那天起，他就让我难以忘怀。这个小巧的静态博客框架搭建简单，无需复杂的配置，更不用自己写一行的代码就可以圆自己的博客梦。这让我这样对后台一无所知的小白也搜集教程，搭建了现在的这个博客。
Hexo的使用非常简便，写好markdown文件后只需要一个简单的指令`hexo d -g`就能完成从md到html的编译并且部署到服务器。
新博客搭建不久当我写到第6篇博客[AvalonJS系列01之快速入门](http://pages.fedt.xin/2016/05/22/AvalonJS01/)时，突然出现了问题：输入`hexo generate`指令生成时报错了！错误提示如下：

<!--more-->

```
$ hexo generate
INFO  Start processing
FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html
Template render error: (unknown path) [Line 20, Column 83]
  unexpected token: } }
    at Object.exports.prettifyError (E:\Hexo\node_modules\nunjucks\src\lib.js:34:15)
    at Obj.extend.render (E:\Hexo\node_modules\nunjucks\src\environment.js:468:27)
    at Obj.extend.renderString (E:\Hexo\node_modules\nunjucks\src\environment.js:326:21)
    at E:\Hexo\node_modules\hexo\lib\extend\tag.js:66:9
    at Promise._execute (E:\Hexo\node_modules\bluebird\js\release\debuggability.js:272:9)
    at Promise._resolveFromExecutor (E:\Hexo\node_modules\bluebird\js\release\promise.js:473:18)
    at new Promise (E:\Hexo\node_modules\bluebird\js\release\promise.js:77:14)
    at Tag.render (E:\Hexo\node_modules\hexo\lib\extend\tag.js:64:10)
    at Object.tagFilter [as onRenderEnd] (E:\Hexo\node_modules\hexo\lib\hexo\post.js:253:16)
    at E:\Hexo\node_modules\hexo\lib\hexo\render.js:63:19
    at tryCatcher (E:\Hexo\node_modules\bluebird\js\release\util.js:16:23)
    at Promise._settlePromiseFromHandler (E:\Hexo\node_modules\bluebird\js\release\promise.js:502:31)
    at Promise._settlePromise (E:\Hexo\node_modules\bluebird\js\release\promise.js:559:18)
    at Promise._settlePromise0 (E:\Hexo\node_modules\bluebird\js\release\promise.js:604:10)
    at Promise._settlePromises (E:\Hexo\node_modules\bluebird\js\release\promise.js:683:18)
    at Async._drainQueue (E:\Hexo\node_modules\bluebird\js\release\async.js:138:16)
    at Async._drainQueues (E:\Hexo\node_modules\bluebird\js\release\async.js:148:10)
    at Immediate.Async.drainQueues [as _onImmediate] (E:\Hexo\node_modules\bluebird\js\release\async.js:17:14)
    at tryOnImmediate (timers.js:543:15)
    at processImmediate [as _immediateCallback] (timers.js:523:5)
FATAL (unknown path) [Line 20, Column 83]
  unexpected token: }}
Template render error: (unknown path) [Line 20, Column 83]
  unexpected token: }}
    at Object.exports.prettifyError (E:\Hexo\node_modules\nunjucks\src\lib.js:34:15)
    at Obj.extend.render (E:\Hexo\node_modules\nunjucks\src\environment.js:468:27)
    at Obj.extend.renderString (E:\Hexo\node_modules\nunjucks\src\environment.js:326:21)
    at E:\Hexo\node_modules\hexo\lib\extend\tag.js:66:9
    at Promise._execute (E:\Hexo\node_modules\bluebird\js\release\debuggability.js:272:9)
    at Promise._resolveFromExecutor (E:\Hexo\node_modules\bluebird\js\release\promise.js:473:18)
    at new Promise (E:\Hexo\node_modules\bluebird\js\release\promise.js:77:14)
    at Tag.render (E:\Hexo\node_modules\hexo\lib\extend\tag.js:64:10)
    at Object.tagFilter [as onRenderEnd] (E:\Hexo\node_modules\hexo\lib\hexo\post.js:253:16)
    at E:\Hexo\node_modules\hexo\lib\hexo\render.js:63:19
    at tryCatcher (E:\Hexo\node_modules\bluebird\js\release\util.js:16:23)
    at Promise._settlePromiseFromHandler (E:\Hexo\node_modules\bluebird\js\release\promise.js:502:31)
    at Promise._settlePromise (E:\Hexo\node_modules\bluebird\js\release\promise.js:559:18)
    at Promise._settlePromise0 (E:\Hexo\node_modules\bluebird\js\release\promise.js:604:10)
    at Promise._settlePromises (E:\Hexo\node_modules\bluebird\js\release\promise.js:683:18)
    at Async._drainQueue (E:\Hexo\node_modules\bluebird\js\release\async.js:138:16)
    at Async._drainQueues (E:\Hexo\node_modules\bluebird\js\release\async.js:148:10)
    at Immediate.Async.drainQueues [as _onImmediate] (E:\Hexo\node_modules\bluebird\js\release\async.js:17:14)
    at tryOnImmediate (timers.js:543:15)
    at processImmediate [as _immediateCallback] (timers.js:523:5)
```
我自然是看不懂这是什么错，所以我只好重新安装一遍Hexo。但是当我再次生成时，仍然报这个错！！
这就有点尴尬了，一直都是简单无脑的Hexo为什么今天就调皮了呢？
随后我试着还原我之前的一些更改，当我删掉那篇博客后就能正常生成了。。这一现象让我把注意力放在了我的md文件上！
但是我一直都是这样写的，md文件没有什么语法错误，markdownPad生成的html也是正常的。于是我试着去看那冗长的错误提示：果然，第五行赫然写着`unexpected token: }}`！！我查看我的md文件，其中果然出现了“{ { } }”！！
问题就出在这“{ {”上面，他会让Hexo编译失败并报错！
找到了问题所在，但我并没有找到怎么解决这个问题，所以我只好用空格符隔开“{”，这样就不会生成失败啦！