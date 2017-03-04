---
title: AvalonJS系列01之快速入门
date: 2016/5/22 
categories:
- 技术分享
tags:
- JS框架
- AvalonJS
---

## 关于AvalonJS ##
### AvalonJS简介 ###
avalon是一个简单易用迷你的MVVM框架，为解决同一业务逻辑存在各种视图呈现而开发出来的。
MVVM将所有前端代码彻底分成两部分，视图的处理通过绑定实现（angular中叫指令）， 业务逻辑则集中在一个个叫VM的对象中处理。我们只要操作VM的数据，它就自然而然地神奇地同步到视图。
avalon的所有指令都是以ms-*命名。跟MicroSoft并没有关系。
### AvalonJS的优势 ###
>&emsp;&emsp;绝对的优势就是降低了耦合， 让开发者从复杂的各种事件中挣脱出来。 举一个简单地例子， 同一个状态可能跟若干个事件的发生顺序与发生时的附加参数都有关系， 不用 MVC (包括 MVVM) 的情况下, 逻辑可能非常复杂而且脆弱。 并且通常需要在不同的地方维护相关度非常高的一些逻辑， 稍有疏忽就会酿成 bug 不能自拔。使用这类框架能从根本上降低应用开发的逻辑难度， 并且让应用更稳健。

>&emsp;&emsp;除此之外， 也免去了一些重复的体力劳动， 一个 {value} 就代替了一行 $(selector).text(value)。 一些个常用的 directive 也能快速实现一些原本可能需要较多代码才能实现的功能

<!--more-->

- 国产框架，文档易于阅读理解。对于我这种英语捉急的来说简直福音。。
- 容易上手，没有太多复杂的概念， 指令数量控制得当，个人感觉酷似angular
- 兼容性非常好， 支持IE6+，firefox3.5+, opera11+, safari5+, chrome4，甚至兼容很多的国产山寨浏览器（~~这个不能算优点，只是符合国情。低版本和山寨浏览器都得死~~~~）
- 无DOM操作。这是所有VMMV框架的一个共性，让开发者投入业务逻辑开发而不用在头疼的DOM操作上浪费时间
- 使用类似CSS的重叠覆盖机制，让各个ViewModel分区交替地渲染页面

### avalon现在有三个分支: ###
- avalon.js 兼容IE6，标准浏览器, 及主流山寨浏览器(QQ, 猎豹, 搜狗, 360, 傲游);
- avalon.modern.js 则只支持IE10等支持HTML5现代浏览器 ; 
- avalon.mobile.js，添加了触屏事件与fastclick支持，用于移动端。


## 初探AvalonJS ##
### avalon简单实例 ###
```html
<!DOCTYPE html>
<html>
    <head>
        <title></title>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <script src="avalon.js"></script>
    </head>
    <body>
        <div ms-controller="box">
            <div style=" background: #a9ea00;" ms-css-width="w" ms-css-height="h"  ms-click="click"></div>
            <p>{{ w }} x {{ h }}</p>
            <p>W: <input type="text" ms-duplex="w" data-duplex-event="change"/></p>
            <p>H: <input type="text" ms-duplex="h" /></p>
        </div>
        <script>
              var vm = avalon.define({
                 $id: "box",
                  w: 100,
                  h: 100,
                  click: function() {
                    vm.w = parseFloat(vm.w) + 10;
                    vm.h = parseFloat(vm.h) + 10;
                  }
              })
        </script>
    </body>
</html>
```
上面的代码中，我们可以看到在JS中，没有任何一行操作DOM的代码，也没有选择器，非常干净。在HTML中， 我们发现就是多了一些以ms-开始的属性与`{ { } }`标记，有的是用于渲染样式， 有的是用于绑定事件。这些属性或标记，实质就是avalon的绑定系统的一部分。绑定（有的框架也将之称为指令）， 负责帮我们完成视图的各种操作，相当于一个隐形的jQuery。正因为有了绑定，我们就可以在JS代码专注业务逻辑本身， 写得更易维护的代码！
### 扫描 ###
不过上面的代码并不完整，它能工作，是因为框架默认会在`DOMReady`时扫描DOM树，将视图中的绑定属性与`{ { } }`插值表达式抽取出来，转换为求值函数与视图刷新函数。

上面的JS代码相当于：
```javascript
avalon.ready(function() {
    var vm = avalon.define({
          $id: "box",
          w: 100,
          h: 100,
          click: function() {
             vm.w = parseFloat(vm.w) + 10;
             vm.h = parseFloat(vm.h) + 10;
          }
      })
      avalon.scan()
 })
```
**`avalon.scan`**是一个非常重要的方法，它有**两个**可选参数，第一个是扫描的起点元素，默认是HTML标签，第2个是VM对象。
```javascript
//源码
    avalon.scan = function(elem, vmodel) {
        elem = elem || root
        var vmodels = vmodel ? [].concat(vmodel) : []
        scanTag(elem, vmodels)
    }
```
### 视图模型 ###

视图模型，`ViewModel`，也经常被略写成VM，是通过`avalon.define`方法进行定义。生成的对象会默认放到`avalon.vmodels`对象上。 每个VM在定义时必须指定$id。如果你有某些属性不想监听，可以直接将此属性名放到`$skipArray`数组中。

接着我们说一些重要的概念：

- **`$id`**， 每个VM都有$id，如果VM的某一个属性是对象（并且它是可监控的），也会转换为一个VM，这个子VM也会默认加上一个$id。 但只有用户添加的那个最外面的$id会注册到`avalon.vmodels`对象上。
- **监控属性**，一般地，VM中的属性都会转换为此种属性，当我们以`vm.aaa = yyy`这种形式更改其值时，就会同步到视图上的对应位置上。
- **计算属性**，定义时为一个对象，并且只存在set,get两个函数或只有一个get一个函数。它是监控属性的高级形式，表示它的值是通过函数计算出来的，是依赖于其他属性合成出来的。
- **监控数组**，定义时为一个数组，它会添加了许多新方法，但一般情况下与普通数组无异，但调用它的`push`, `unshift`, `remove`, `pop`等方法会同步视图。
- **非监控属性**，这包括框架添加的$id属性，以**$开头的属性**，放在**`$skipArray`**数组中的属性，值为**函数、元素节点、文本节点**的属性，总之，改变它们的值不会产生同步视图的效果。
`$skipArray` 是一个字符串数组，只能放**当前对象的直接属性名**，想禁止子对象的某个属性的监听，在那个`子对象`上再添加一个`$skipAray`数组就行了。

视图里面，我们可以使用`ms-controller`, `ms-important`指定一个**VM的作用域**。

此外，在`ms-each, ms-with，ms-repeat`绑定属性中，它们会创建一个临时的VM，我们称之为代理VM， 用于放置`$key, $val, $index, $last, $first, $remove`等变量或方法。

另外，avalon**不允许在VM定义之后，再追加新属性与方法**，也**不允许在define里面直接调用方法或ajax**

### 如何更新VM中的属性(重点)： ###
```javascript
var model : avalon.define({
     $id:  "update", 
     aaa : "str",
     bbb : false,
     ccc : 1223,
     time : new Date,
     simpleArray : [1, 2, 3, 4],
     objectArray : [{name: "a"}, {name: "b"}, {name: "c"}, {name: "d"}],
     object : {
         o1: "k1",
         o2: "k2",
         o3: "k3"
     },
     simpleArray : [1, 2, 3, 4],
     objectArray : [{name: "a", value: "aa"}, {name: "b", value: "bb"}, {name: "c", value: "cc"}, {name: "d", value: "dd"}],
     object : {
         o1: "k1",
         o2: "k2",
         o3: "k3"
     }
 })
 
       setTimeout(function() {
           //如果是更新简单数据类型（string, boolean, number）或Date类型
           model.aaa = "这是字符串"
           model.bbb = true
           model.ccc = 999999999999
           var date = new Date
           model.time = new Date(date.setFullYear(2005))
       }, 2000)
 
       setTimeout(function() {
           //如果是数组，注意保证它们的元素的类型是一致的
           //只能全是字符串，或是全是布尔，不能有一些是这种类型，另一些是其他类型
           //这时我们可以使用set方法来更新（它有两个参数，第一个是index，第2个是新值）
           model.simpleArray.set(0, 1000)
           model.simpleArray.set(2, 3000)
           model.objectArray.set(0, {name: "xxxxxxxxxxxxxxxx", value: "xxx"})
       }, 2500)
       setTimeout(function() {
           model.objectArray[1].name = "5555"
       }, 3000)
       setTimeout(function() {
           //如果要更新对象，直接赋给它一个对象，注意不能将一个VM赋给它，可以到VM的$model赋给它（要不会在IE6-8中报错）
           model.object = {
               aaaa: "aaaa",
               bbbb: "bbbb",
               cccc: "cccc",
               dddd: "dddd"
           }
       }, 3000)
```
### 绑定 ###

avalon的绑定（或指令），拥有以下三种类型：

- `{ { } }`插值表达式， 这是开标签与闭标签间，换言之，也是位于**文本节点**中，`innerText`里。`{ { } }`里面可以添加各种过滤器（以|进行标识）。值得注意的是`{ { } }`实际是文本绑定(`ms-text`)的一种形式。
- ms-*绑定属性， 这是位于开标签的内部， 95%的绑定都以这种形式存在。 它们的格式大概是这样划分的 ` "ms" + type + "-" + param1 + "-" + param1 + "-" + param2 + ... + number = value`。关于绑定属性指令将在[下一篇](http://fedt.coding.me/AvalonJS02/)中详细说到。
```javascript
ms-skip                //这个绑定属性没有值
ms-controller="expr"   //这个绑定属性没有参数
ms-if="expr"           //这个绑定属性没有参数
ms-if-loop="expr"       //这个绑定属性有一个参数
ms-repeat-el="array"    //这个绑定属性有一个参数
ms-attr-href="xxxx"    //这个绑定属性有一个参数
ms-attr-src="xxx/{{a}}/yyy/{{b}}"   //这个绑定属性的值包含插值表达式，注意只有少部分表示字符串类型的属性可以使用插值表达式
ms-click-1="fn"       //这个绑定属性的名字最后有数字，这是方便我们绑定更多点击事件 ms-click-2="fn"  ms-click-3="fn"  
ms-on-click="fn"     //只有表示事件与类名的绑定属性的可以加数字，如这个也可以写成  ms-on-click-0="fn"    
ms-class-1="xxx" ms-class-2="yyy" ms-class-3="xxx" //数字还表示绑定的次序
ms-css-background-color="xxx" //这个绑定属性有两个参数，但在css绑定里，相当于一个，会内部转换为backgroundColor 
ms-duplex-aaa-bbb-string="xxx"//这个绑定属性有三个参数，表示三种不同的拦截操作 
```
- `data-xxx-yyy="xxx"`，辅助指令，比如ms-duplex的某一个辅助指令为`data-duplex-event="change"`，`ms-repeat`的某一个辅助指令为`data-repeat-rendered="yyy"`
