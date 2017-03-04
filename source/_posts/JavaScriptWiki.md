---
title: JavaScript杂记(RAF,Selection,Scroll)
date: 2016/5/10 14:55:16 
categories:
- 笔记整理
tags:
- JavaScript
- 原生JS
---

## `reqeustAnimationFrame(RAF)` ##

>来自：<a href="https://developer.mozilla.org/zh-CN/docs/Web/API/Window/cancelAnimationFrame" rel="external nofollow">MDN</a>

#### 概述 ####
**window.requestAnimationFrame()**这个方法是用来在页面重绘之前，通知浏览器调用一个指定的函数，以满足开发者操作动画的需求。这个方法接受一个函数为参，该函数会在重绘前调用。

**注意**: 如果想得到连贯的逐帧动画，函数中必须重新调用 `requestAnimationFrame()`。

如果你想做逐帧动画的时候，你应该用这个方法。这就要求你的动画函数执行会先于浏览器重绘动作。通常来说，被调用的频率是每秒60次，但是一般会遵循W3C标准规定的频率。如果是后台标签页面，重绘频率则会大大降低。

回调函数只会被传入一个`DOMHighResTimeStamp`参数，这个参数指示当前被 `requestAnimationFrame` 序列化的函数队列被触发的时间。因为很多个函数在这一帧被执行，所以每个函数都将被传入一个相同的时间戳，尽管经过了之前很多的计算工作。这个数值是一个小数，单位毫秒，精确度在 10 µs。  
<!--more-->
#### 参数 ####
**callback**
在每次需要重新绘制动画时,会调用这个参数所指定的函数。这个回调函数会收到一个参数，这个 `DOMHighResTimeStamp` 类型的参数指示当前时间距离开始触发 `requestAnimationFrame` 的回调的时间。
#### 返回值 ####
`requestID` 是一个长整型非零值,作为一个唯一的标识符.你可以将该值作为参数传给 `window.cancelAnimationFrame()` 来取消这个回调函数。
#### 例子 ####
```javascript
window.requestAnimationFrame = window.requestAnimationFrame || window.mozRequestAnimationFrame || window.webkitRequestAnimationFrame || window.msRequestAnimationFrame;
    
var start = null;
var d = document.getElementById('SomeElementYouWantToAnimate');
function step(timestamp) { 
  if (start === null) start = timestamp;
  var progress = timestamp - start;
  d.style.left = Math.min(progress/10, 200) + "px";
  if (progress < 2000) {
      requestAnimationFrame(step);
  }
}
requestAnimationFrame(step);
```

## Selection对象 ##

>来源：<a href="https://developer.mozilla.org/zh-CN/docs/Web/API/Selection" rel="external nofollow">MDN</a>

Selection对象一般由`window.getSelection()`或其他方法产生。它代表页面中的文本选区，可能横跨多个元素。文本选区由用户拖拽鼠标经过文字而产生。 如果你想了解关于其他容纳纯文本的表单元素中的选区操作，请参考 Input, TextArea 和 document.activeElement ，我们可以在这些页面元素中使用 window.getSelection()。

Selection对象所对应的是用户所选择的 ranges （区域），俗称拖蓝。默认情况下，该函数只针对一个区域，我们可以这样使用这个函数：
```javascript
var selObj = window.getSelection();
var range  = selObj.getRangeAt(0);
```
- selObj 被赋予一个 Selection对象
- range 被赋予一个 Range 对象

调用 `Selection/toString()` 方法会返回被选中区域中的**纯文本**。要求变量为字符串的函数会自动对对象进行该处理，例如：
```javascript
var selObj = window.getSelection();
window.alert(selObj);
```


## 网页上的复制与剪切 ##
>来自：<a href="http://zencode.in/17.%E7%BD%91%E9%A1%B5%E4%B8%8A%E7%9A%84%E5%A4%8D%E5%88%B6%E4%B8%8E%E5%89%AA%E5%88%87.html" rel="external nofollow">MZhou's blog</a>

IE 10及以上的版本修改了Document.execCommand()方法，增加了对剪切和复制的支持。Chrome从43版本开始也支持了这项特性。

当选中了浏览器中的任意文本，只要执行这个方法就可以剪切或拷贝这段文字。有了这个API后，选中一段文字并拷贝会变的非常简单。

这个API和Selection API一起使用时将会变的特别有用。你可以决定哪些文本被复制到剪切版。之后我们会详细阐述。

#### 一个简单的例子 ####
让我们来增加一个按钮，点击这个按钮会拷贝一个email地址到用户的剪切版。

我们在网页里面添加一个email地址和一个按钮，点击按钮会拷贝email地址：
```javascript
<p>Email me at <a class="js-emaillink" href="mailto:matt@example.co.uk">matt@example.co.uk</a></p>
<p><button class="js-emailcopybtn"><img src="./images/copy-icon.png" /></button></p>
```
接下来在Javascript中，我们增加一个click事件监听器到按钮上。在事件监听器中我们通过class js-emaillink选中email地址，然后执行拷贝命令，然后用户的剪切版里面就会有email地址了。然后我们取消选中email地址，这样用户就不会见到文本被选中。
```javascript
var copyEmailBtn = document.querySelector('.js-emailcopybtn');
copyEmailBtn.addEventListener('click', function(event) {
// Select the email link anchor text
var emailLink = document.querySelector('.js-emaillink');
var range = document.createRange();
range.selectNode(emailLink);
window.getSelection().addRange(range);

try {
	// Now that we've selected the anchor text, execute the copy command
	var successful = document.execCommand('copy');
	var msg = successful ? 'successful' : 'unsuccessful';
	console.log('Copy email command was ' + msg);
} catch(err) {
	console.log('Oops, unable to copy');
}

// Remove the selections - NOTE: Should use
// removeRange(range) when it is supported
window.getSelection().removeAllRanges();
```
如上代码中使用了<a href="https://developer.mozilla.org/en-US/docs/Web/API/Selection" rel="external nofollow">Selection API</a>的<a href="https://developer.mozilla.org/en-US/docs/Web/API/Window/getSelection" rel="external nofollow">window.getSelection()</a>方法选中链接的文本。在document.execCommand()方法后，我们可以通过调用window.getSelection().removeAllRanges()方法来移除选中。如果你想检查拷贝是否成功，那么你可以checkdocument.execCommand();的返回值。如果返回false那么表示不支持拷贝或者不能使用（没有选中文本）。我们将execCommand()放到try catch里面执行是为了确保一些极端情况下浏览器会抛出错误。

剪切命令可以在文本框中使用。你可以移除文本输入框中的文字并放到剪切版中使用。

在HTML中添加一个textarea和一个按钮：
```html
<p><textarea class="js-cuttextarea">Hello I'm some text</textarea></p>
<p><button class="js-textareacutbtn" disable>Cut Textarea</button></p>
```
我们可以这么写js：
```javascript
var cutTextareaBtn = document.querySelector('.js-textareacutbtn');
cutTextareaBtn.addEventListener('click', function(event) {
    var cutTextarea = document.querySelector('.js-cuttextarea');
    cutTextarea.select();

    try {
        var successful = document.execCommand('cut');
        var msg = successful ? 'successful' : 'unsuccessful';
        console.log('Cutting text command was ' + msg);
    } catch(err) {
        console.log('Oops, unable to cut');
    }
});
```
#### queryCommandSupported和queryCommandEnabled ####
在调用 document.execCommand() 之前你应该确认这个API是否被所在浏览器支持。这时候你可以用 document.queryCommandSupported() 方法来确认是否支持。在上面的例子中，我们可以在浏览器不支持时将按钮设置为disabled。具体代码如下：

`copyEmailBtn.disabled = !document.queryCommandSupported('copy');`

document.queryCommandSupported())和document.queryCommandEnabled())的区别是：前者检测浏览器是否支持剪切或拷贝，后者检测是否有选中的文本用于剪切或拷贝。这在让用户选中文本的情况（不用Selection API）下比较有用。如果没有选中的文本，你可以显示一个消息给用户，这样更加友好～

#### 浏览器支持情况 ####
IE 10+、Chrome 43+和Opera 29+ 已经支持了这些命令。
Firefox虽然已经支持了这些命令，但是需要修改下首选项（<a href="https://developer.mozilla.org/en-US/docs/Web/API/Document/execCommand" rel="external nofollow">具体看这里</a>)。如果没有修改首选项，那么Firefox会抛出一个错误。
Safari目前不支持这些命令。

#### 已知问题 ####
直接用js代码调用 queryCommandSupported() 会一定会返回false，除非将其放在用户操作的回调函数中执行。这导致了你不能在浏览器不支持时禁用按钮。
在devtools中调用 queryCommandSupported() 一定会返回false。
目前剪切命令只在你用js选中文本时起作用。

### 附：copy事件 ###
The copy event is fired when the user initiates a copy action through the browser UI (for example, using the CTRL/Cmd+C keyboard shortcut or selecting the "Copy" from the menu) and in response to an allowed <a href="https://developer.mozilla.org/en-US/docs/Web/API/Document/execCommand" rel="external nofollow">document.execCommand('copy')</a> call.
A handler for this event can modify the provided ClipboardEvent.clipboardData object by calling setData(format, data):
```javascript
document.addEventListener('copy', function(e){
    e.clipboardData.setData('text/plain', 'Hello, world!');
    e.clipboardData.setData('text/html', '<b>Hello, world!</b>');
    e.preventDefault(); // We want our data, not data from any selection, to be written to the clipboard
});
```
A handler for this event cannot read the clipboard data using `clipboardData.getData()`.


## `scrollHeight`,`clientHeight`,`scrollTop` ##

>来源：
><a href="https://developer.mozilla.org/zh-CN/docs/Web/API/Element/scrollHeight" rel="external nofollow">MDN-Element.scrollHeight</a>
><a href="https://developer.mozilla.org/zh-CN/docs/Web/API/Element/clientHeight" rel="external nofollow">MDN-Element.clientHeight</a>
><a href="https://developer.mozilla.org/zh-CN/docs/Web/API/Element/scrollTop" rel="external nofollow">MDN-Element.scrollTop</a>

### Element.scrollHeight ###
`Element.scrollHeight` 是计量元素内容高度的**只读属性**，包括`overflow`样式属性导致的视图中不可见内容。没有垂直滚动条的情况下，`scrollHeight`值与元素视图填充所有内容所需要的最小值`clientHeight`相同。包括元素的`padding`，但不包括元素的`margin`.

>属性将会对值四舍五入取整。如果需要小数值，使用Element.getBoundingClientRect().

#### 语法 ####
```javascript
var intElemScrollHeight = document.getElementById(id_attribute_value).scrollHeight;
```
intElemScrollHeight 存储着与元素scrollHeight像素值对应的一个整数。scrollHeight是一个只读属性。
![scrollHeight图解](https://developer.mozilla.org/@api/deki/files/840/=ScrollHeight.png)
#### 判定元素是否滚动到底 ####

如果元素滚动到底，下面等式返回true，没有则返回false.
```javascript
element.scrollHeight - element.scrollTop === element.clientHeight
```
#### scrollHeight 演示 ####
监听onscroll事件，这个等式可以用来判定用户是否阅读过文本。（参考 `element.scrollTop` 和 `element.clientHeight`属性）。例如：<a href="http://jsbin.com/zeqohi/edit?html,css,js" rel="external nofollow">scrollHeight 演示</a>
### Element.clientHeight ###
返回元素内部的高度(单位像素)，包含**内边距**，但不包括水平滚动条、边框和外边距。

`clientHeight` 可以通过 `CSS height` + `CSS padding` - `水平滚动条高度` (如果存在)来计算.

#### 语法 ####
`var h = element.clientHeight;`
返回**整数 h**，表示 element 的 clientHeight（单位像素）。
clientHeight 是`只读`的.

### Element.scrollTop ###
这个`Element.scrollTop` 属性可以设置或者获取一个元素距离他**容器顶部**的像素距离。一个元素的 `scrollTop` 是可以去计算出这个元素距离它容器顶部的可见高度。当一个元素的容器没有产生垂直方向的滚动条,那它的 scrollTop 的值默认为0.

#### 语法 ####
```
// 获得滚动的距离
var  intElemScrollTop = someElement.scrollTop;
```
运行下面的代码, 可以去给一个元素设置一个正确的像素整型数值，如果element的容器已经被滚动了.
```
// 设置滚动的距离
element.scrollTop = intValue;
```
scrollTop可以被设置任何的整数, 但以下情况会报错:
- 如果一个元素不能被滚动 (e.g. 它没有溢出容器或者 这个元素是不可滚动的), scrollTop被设置为0.
- 设置scrollTop的值小于0，scrollTop 被设为0
- 如果设置了超出这个容器可滚动的值, scrollTop 会被设为最大值.
![scrollTop图解](https://developer.mozilla.org/@api/deki/files/842/=ScrollTop.png)