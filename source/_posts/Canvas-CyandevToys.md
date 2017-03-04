---
title: 用 Canvas 编织璀璨星空图
date: 2016/6/14 
categories:
- 精品转载
tags:
- JavaScript
- Canvas
---

>转载自： [简书-用 Canvas 编织璀璨星空图](http://www.jianshu.com/p/f5c0f9c4bc39#)
>作者：[Cyandev](http://www.jianshu.com/users/c49454e0ae54)

先来看看最终的效果：
![璀璨星空图](/images/CyandevToys.png)

GitHub项目: [CyandevToys / ParticleWeb](https://github.com/unixzii/CyandevToys/tree/master/ParticleWeb)

是不是还蛮酷的呢？本文我们就来一点一点分析怎么实现它！

<!--more-->

## 分析 ##
首先我们看看这个效果具体有那些要点。首先，这么炫酷的效果肯定是要用到 Canvas 了，每个星星可以看作为一个粒子，因此，整个效果实际上就是粒子系统了。此外，我们可以发现每个粒子之间是相互连接的，只不过离的近的粒子之间的连线较粗且透明度较低，而离的远的则相反。

## 开始 Coding ##
### HTML 部分 ###
这部分我就简单放了一个 `<canvas>` 标签，设置样式使其填充全屏。
```html
<canvas height="620" width="1360" id="canvas" style="position: absolute; height: 100%;"></canvas>
```
然后为了让所有元素没有间距和内补，我还加了一条全局样式：
```css
<style>
    * {
      margin: 0;
      padding: 0;
    }
</style>
```
### JavaScript 部分 ###
下面我们来写核心的代码。首先我们要得到那个 canvas 并得到绘制上下文：
```js
var canvasEl = document.getElementById('canvas');
var ctx = canvasEl.getContext('2d');
var mousePos = [0, 0];
```
紧接着我们声明两个变量，分别用于存储“星星”和边：
```js
var nodes = [];
var edges = [];
```
下一步，我们做些准备工作，就是让画布在窗口大小发生变化时重新绘制，并且调整自身分辨率：
```js
window.onresize = function () {
    canvasEl.width = document.body.clientWidth;
    canvasEl.height = canvasEl.clientHeight;

    if (nodes.length == 0) {
      constructNodes();
    }

    render();
};

window.onresize(); // trigger the event manually.
```
我们在第一次修改大小后构建了所有节点，这里就要用到下一个函数`（constructNodes）`了

这个函数中我们随机创建几个点，我们用字典对象的方式存储这些点的各个信息：
```js
function constructNodes() {
    for (var i = 0; i < 100; i++) {
      var node = {
        drivenByMouse: i == 0,
        x: Math.random() * canvasEl.width,
        y: Math.random() * canvasEl.height,
        vx: Math.random() * 1 - 0.5,
        vy: Math.random() * 1 - 0.5,
        radius: Math.random() > 0.9 ? 3 + Math.random() * 3 : 1 + Math.random() * 3
      };

      nodes.push(node);
    }

    nodes.forEach(function (e) {
      nodes.forEach(function (e2) {
        if (e == e2) {
          return;
        }

        var edge = {
          from: e,
          to: e2
        }

        addEdge(edge);
      });
    });
  }
```
为了实现后面一个更炫酷的效果，我给第一个点加了一个 drivenByMouse 属性，这个点的位置不会被粒子系统管理，也不会绘制出来，但是它会与其他点连线，这样就实现了鼠标跟随的效果了。

这里稍微解释一下 radius 属性的取值，我希望让绝大部分点都是小半径的，而极少数的点半径比较大，所以我这里用了一点小 tricky，就是用概率控制点的半径取值，不断调整这个概率阈值就能获取期待的半径随机分布。

点都构建完毕了，就要构建点与点之间的连线了，我们用到双重遍历，把两个点捆绑成一组，放到 `edges` 数组中。注意这里我用了另外一个函数来完成这件事，而没有直接用 `edges.push()` ，为什么？

假设我们之前连接了 A、B两点，也就是外侧循环是A，内侧循环是B，那么在下一次循环中，外侧为B，内侧为A，是不是也会创建一条边呢？而实际上，这两个边除了方向不一样以外是完全一样的，这完全没有必要而且占用资源。因此我们在 addEdge 函数中进行一个判断：
```js
function addEdge(edge) {
    var ignore = false;

    edges.forEach(function (e) {
      if (e.from == edge.from && e.to == edge.to) {
        ignore = true;
      }

      if (e.to == edge.from && e.from == edge.to) {
        ignore = true;
      }
    });

    if (!ignore) {
      edges.push(edge);
    }
  }
```
至此，我们的准备工作就完毕了，下面我们要让点动起来：
```js
function step() {
    nodes.forEach(function (e) {
      if (e.drivenByMouse) {
        return;
      }

      e.x += e.vx;
      e.y += e.vy;

      function clamp(min, max, value) {
        if (value > max) {
          return max;
        } else if (value < min) {
          return min;
        } else {
          return value;
        }
      }

      if (e.x <= 0 || e.x >= canvasEl.width) {
        e.vx *= -1;
        e.x = clamp(0, canvasEl.width, e.x)
      }

      if (e.y <= 0 || e.y >= canvasEl.height) {
        e.vy *= -1;
        e.y = clamp(0, canvasEl.height, e.y)
      }
    });

    adjustNodeDrivenByMouse();
    render();
    window.requestAnimationFrame(step);
  }

  function adjustNodeDrivenByMouse() {
    nodes[0].x += (mousePos[0] - nodes[0].x) / easingFactor;
    nodes[0].y += (mousePos[1] - nodes[0].y) / easingFactor;
  }
```
看到这么一大段代码不要害怕，其实做的事情很简单。这是粒子系统的核心，就是遍历粒子，并且更新其状态。更新的公式就是
```js
v = v + a
s = s + v
```
a是加速度，v是速度，s是位移。由于我们这里不涉及加速度，所以就不写了。然后我们需要作一个边缘的碰撞检测，不然我们的“星星”都无拘无束地一点点飞～走～了～。边缘碰撞后的处理方式就是让速度矢量反转，这样粒子就会“掉头”回来。

还记得我们需要做的鼠标跟随吗？也在这处理，我们让第一个点的位置一点一点移动到鼠标的位置，下面这个公式很有意思，可以轻松实现缓动：
```js
x = x + (t - x) / factor
```
其中 factor 是缓动因子，t 是最终位置，x 是当前位置。至于这个公式的解释还有个交互大神 Bret Victor 在他的演讲中提到过，视频做的非常好，有*条(ti)件(zi)*大家一定要看看： [Bret Victor - Stop Drawing Dead Fish](https://www.youtube.com/watch?v=ZfytHvgHybA)

好了，回到主题。我们在上面的函数中处理完了一帧中的数据，我们要让整个粒子系统连续地运转起来就需要一个timer了，但是十分不提倡大家使用 `setInterval`，而是尽可能使用 `requestAnimationFrame`，它能保证你的帧率锁定在 <= 60 fps ，不至于爆掉你的浏览器。函数参数即为回调函数，看起来有点像递归调用，但实际上，浏览器内部处理是将这个回调函数先入事件列队，等所有绘制计算工作完毕后再从列队中取出执行，十分高效。

剩下的就是绘制啦：
```js
function render() {
    ctx.fillStyle = backgroundColor;
    ctx.fillRect(0, 0, canvasEl.width, canvasEl.height);

    edges.forEach(function (e) {
      var l = lengthOfEdge(e);
      var threshold = canvasEl.width / 8;

      if (l > threshold) {
        return;
      }

      ctx.strokeStyle = edgeColor;
      ctx.lineWidth = (1.0 - l / threshold) * 2.5;
      ctx.globalAlpha = 1.0 - l / threshold;
      ctx.beginPath();
      ctx.moveTo(e.from.x, e.from.y);
      ctx.lineTo(e.to.x, e.to.y);
      ctx.stroke();
    });
    ctx.globalAlpha = 1.0;

    nodes.forEach(function (e) {
      if (e.drivenByMouse) {
        return;
      }

      ctx.fillStyle = nodeColor;
      ctx.beginPath();
      ctx.arc(e.x, e.y, e.radius, 0, 2 * Math.PI);
      ctx.fill();
    });
  }
```
常规的 **`Canvas`** 绘图操作，注意 `beginPath` 一定要调用，不然你的线就全部穿在一起了... 需要说明的是，在绘制边的时候，我们先要计算两点距离，然后根据一个阈值来判断是否要绘制这条边，这样我们才能实现距离远的点之间连线不可见的效果。

到这里，我们的整个效果就完成了。如果不明白大家也可以去我文章开头放的 repo 离去看完整的源码。Have fun!!