关于window.requestAnimationFrame
===============================

最近项目需求是要做一款小游戏，开始的时候使用的是setInterval，来实现动画切换的效果。但发现会有卡顿效果，经过一番google后发现`requestAnimationFrame`这个函数很符合需求。这里科普下这个函数的具体用法，以及动画帧节点的控制。

## [MDN window.requestAnimationFrame](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestAnimationFrame)

window.requestAnimationFrame()方法告诉浏览器希望您执行动画，并请求浏览器调用指定的函数在下一次重绘之前更新动画。该方法将在重绘之前调用的回调作为参数。

> 注意：如果您想在下一次重绘时为另一个框架设置动画，您的回调历程必须调用`requestAnimationFrame()`。

如果你想做逐帧动画的时候，你应该用这个方法，这就要求你的动画函数执行会先于浏览器重绘动作。通常开说，被调用的频率是每秒60次，但一般会遵循W3C标准规定的频率。如果是后台标签页面，重绘频率则大大降低。

回调函数只会被传入一个[DOMHighResTimeStamp](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMHighResTimeStamp)参数，这个参数指示当前被requestAnimationFrame序列化的函数队列被处罚的时间。因为很多函数在这一帧被执行，所以每个函数都将被传入一个相同的时间戳，尽管经过之前很多的计算工作。这个数值是一个小数，单位毫秒，精确度在10 µs。

### 语法

```js

requestID = window.requestAnimationFrame(callback);               // Firefox 23 / IE10 / Chrome / Safari 7 (incl. iOS)
requestID = window.mozRequestAnimationFrame(callback);                // Firefox < 23
requestID = window.webkitRequestAnimationFrame(callback); // Older versions Chrome/Webkit

```

### 参数

callback：在每次需要重新绘制动画时，会调用这个参数所指定的函数。这个回调函数会受到一个参数，这个[DOMHighResTimeStamp](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMHighResTimeStamp)类型的参数指示当前时间距离开始出发`requestAnimationFrame`的回调的时间。

### 返回值

`requestID`是一个长整型非零值，作为一个唯一的标识符。你可以将改值作为参数传给[window.cancelAnimationFrame()](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/cancelAnimationFrame)来取消这个回调函数。

### 例子

```js

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

### 浏览器兼容性

**Desktop**

<table class="compat-table">
 <tbody>
  <tr>
   <th>Feature</th>
   <th>Chrome</th>
   <th>Firefox (Gecko)</th>
   <th>Internet Explorer</th>
   <th>Opera</th>
   <th>Safari (WebKit)</th>
  </tr>
  <tr>
   <td>Basic support</td>
   <td>
    <p>10&nbsp;<span title="prefix" class="inlineIndicator prefixBox prefixBoxInline"><a title="The name of this feature is prefixed with 'webkit' as this browser considers it experimental" class="new" href="/zh-CN/docs/Web/Guide/Prefixes">webkit</a></span>&nbsp;</p>

    <p>24.0</p>
   </td>
   <td>
    <p>4.0 <span title="prefix" class="inlineIndicator prefixBox prefixBoxInline"><a title="The name of this feature is prefixed with 'moz' as this browser considers it experimental" class="new" href="/zh-CN/docs/Web/Guide/Prefixes">moz</a></span> [1]<br>
     23 [2]</p>
   </td>
   <td>
    <p>10.0</p>
   </td>
   <td><span title="Please update this with the earliest version of support." style="color: #888;">(Yes)</span> <span title="prefix" class="inlineIndicator prefixBox prefixBoxInline"><a title="The name of this feature is prefixed with '-o' as this browser considers it experimental" class="new" href="/zh-CN/docs/Web/Guide/Prefixes">-o</a></span><br>
    15.0</td>
   <td>
    <p><em><a class="external external-icon" href="http://nightly.webkit.org/">Nightly build</a></em> <span title="prefix" class="inlineIndicator prefixBox prefixBoxInline"><a title="The name of this feature is prefixed with 'webkit' as this browser considers it experimental" class="new" href="/zh-CN/docs/Web/Guide/Prefixes">webkit</a></span></p>
   </td>
  </tr>
  <tr>
   <td><code>requestID</code> return value</td>
   <td>23.0 <span title="prefix" class="inlineIndicator prefixBox prefixBoxInline"><a title="The name of this feature is prefixed with 'webkit' as this browser considers it experimental" class="new" href="/zh-CN/docs/Web/Guide/Prefixes">webkit</a></span><br>
    24.0</td>
   <td><a title="Released on 2012-03-13." href="/en-US/Firefox/Releases/11">11.0</a> (11.0) <span title="prefix" class="inlineIndicator prefixBox prefixBoxInline"><a title="The name of this feature is prefixed with 'moz' as this browser considers it experimental" class="new" href="/zh-CN/docs/Web/Guide/Prefixes">moz</a></span></td>
   <td>10.0</td>
   <td><span title="Please update this with the earliest version of support." style="color: #888;">(Yes)</span> <span title="prefix" class="inlineIndicator prefixBox prefixBoxInline"><a title="The name of this feature is prefixed with '-o' as this browser considers it experimental" class="new" href="/zh-CN/docs/Web/Guide/Prefixes">-o</a></span><br>
    15.0</td>
   <td><span title="Compatibility unknown; please update this." style="color: rgb(255, 153, 0);">?</span></td>
  </tr>
 </tbody>
</table>

**Mobile**

<table class="compat-table">
 <tbody>
  <tr>
   <th>Feature</th>
   <th>Android</th>
   <th>BlackBerry Browser</th>
   <th>Chrome for Android</th>
   <th>Firefox Mobile (Gecko)</th>
   <th>IE Phone</th>
   <th>Opera Mobile</th>
   <th>Safari Mobile</th>
  </tr>
  <tr>
   <td>Basic support</td>
   <td><span title="Compatibility unknown; please update this." style="color: rgb(255, 153, 0);">?</span></td>
   <td>10.0 <span title="prefix" class="inlineIndicator prefixBox prefixBoxInline"><a title="The name of this feature is prefixed with 'webkit' as this browser considers it experimental" class="new" href="/zh-CN/docs/Web/Guide/Prefixes">webkit</a></span></td>
   <td>
    <p>16.0 <span title="prefix" class="inlineIndicator prefixBox prefixBoxInline"><a title="The name of this feature is prefixed with 'webkit' as this browser considers it experimental" class="new" href="/zh-CN/docs/Web/Guide/Prefixes">webkit</a></span>&nbsp;</p>
   </td>
   <td><span title="Compatibility unknown; please update this." style="color: rgb(255, 153, 0);">?</span></td>
   <td><span title="Compatibility unknown; please update this." style="color: rgb(255, 153, 0);">?</span></td>
   <td><span title="Compatibility unknown; please update this." style="color: rgb(255, 153, 0);">?</span></td>
   <td><span title="Compatibility unknown; please update this." style="color: rgb(255, 153, 0);">?</span></td>
  </tr>
  <tr>
   <td><code>requestID</code> return value</td>
   <td><span title="Compatibility unknown; please update this." style="color: rgb(255, 153, 0);">?</span></td>
   <td>&nbsp;</td>
   <td><span title="Compatibility unknown; please update this." style="color: rgb(255, 153, 0);">?</span></td>
   <td>11.0 (11.0) <span title="prefix" class="inlineIndicator prefixBox prefixBoxInline"><a title="The name of this feature is prefixed with 'moz' as this browser considers it experimental" class="new" href="/zh-CN/docs/Web/Guide/Prefixes">moz</a></span></td>
   <td><span title="Compatibility unknown; please update this." style="color: rgb(255, 153, 0);">?</span></td>
   <td><span title="Compatibility unknown; please update this." style="color: rgb(255, 153, 0);">?</span></td>
   <td><span title="Compatibility unknown; please update this." style="color: rgb(255, 153, 0);">?</span></td>
  </tr>
 </tbody>
</table>

### Gecko备注

在Gecko 11.0(Firefox 11.0/Thunderbird 11.0 / Seamonkey 2.8)之前，调用mozRequestAnimationFrame()方法时可以不传入任何参数。目前，该方法的callback参数是必须的。

### 规范

[HTML 5.1-Web application APIs:Animation Frames](https://www.w3.org/TR/html51/webappapis.html#animation-frames)

## 关于动画帧的控制

js最简单的控制动画帧的方式就是开一个定时器，并推动响应的绘制逻辑执行。

```js

var fps = 60;
setInterval(draw, 1000/fps);

```

这个简单做法，如果draw带有大量逻辑运算，导致计算时间超过帧等待时间，将会出现丢帧。除外，如果FPS太高，超过了当时浏览器的重绘频率，将会造成计算浪费，例如浏览器才重绘两针，但却计算了3帧，那么有1帧的计算就浪费了。

成熟做法：

引入`requestAnimationFrame`,这个方法是用来在页面重绘之前，同志浏览器调用一个指定的函数，以满足开发者操作动画的需求。

这个函数类似setTimeout，只调用一次

```js

function draw() {
	requestAnimationFrame(draw);
	// code for Drawing the Frame
}

```

递归调用，就可以实现定时器。

但是，这样就完全跟浏览器帧频率同步了，无法实现自定义动画帧，是无法满足需求的。

**接下来需要考虑的就是如何控制动画帧**

最简单的做法：

```js

var fps = 30;
function tick() {
　　setTimeout(function() {
　　　　requestAnimationFrame(tick);
　　　　draw(); // ... Code for Drawing the Frame ...
　　}, 1000 / fps);
}
tick();

```

这种做法，比较直观的可以发现，每一次setTimeout执行的时候，都还要在等到下一个requestAnimationFrame事件到达，累计下去会造成动画变慢。

**自行控制时间跨度**

```js

var fps = 30;
var now;
var then = Date.now();
var interval = 1000/fps;
var delta;

function tick() {
　　requestAnimationFrame(tick);
　　now = Date.now();
　　delta = now - then;
　　if (delta > interval) {
　　　　// 这里不能简单then=now，否则还会出现上边简单做法的细微时间差问题。例如fps=10，每帧100ms，而现在每16ms（60fps）执行一次draw。16*7=112>100，需要7次才实际绘制一次。这个情况下，实际10帧需要112*10=1120ms>1000ms才绘制完成。
　　　　then = now - (delta % interval);
　　　　draw(); // ... Code for Drawing the Frame ...
　　}
}
tick();

```

**针对低版本浏览器优化**

如果浏览器没有requestAnimationFrame函数，实际底层还只能用setTimeout模拟，上边做的都是无用功，那么可以在改进一下：

```js

var fps = 30;
var now;
var then = Date.now();
var interval = 1000/fps;
var delta;
window.requestAnimationFrame = window.requestAnimationFrame || window.mozRequestAnimationFrame || window.webkitRequestAnimationFrame || window.msRequestAnimationFrame;

function tick() {
　　if(window.requestAnimationFrame)
   {
　　    requestAnimationFrame(tick);
　　    now = Date.now();
　　    delta = now - then;
　　    if (delta > interval) {
        // 这里不能简单then=now，否则还会出现上边简单做法的细微时间差问题。例如fps=10，每帧100ms，而现在每16ms（60fps）执行一次draw。16*7=112>100，需要7次才实际绘制一次。这个情况下，实际10帧需要112*10=1120ms>1000ms才绘制完成。
　　　　    then = now - (delta % interval);
　　　　    draw(); // ... Code for Drawing the Frame ...
　　    }
   }
   else
   {
       setTimeout(tick, interval);
　　　　draw();
   }
}
tick();

```

**最后，还可以加上暂停**

```js

var fps = 30;
var pause = false;
var now;
var then = Date.now();
var interval = 1000/fps;
var delta;
window.requestAnimationFrame = window.requestAnimationFrame || window.mozRequestAnimationFrame || window.webkitRequestAnimationFrame || window.msRequestAnimationFrame;

function tick() {
     if(pause)
          return;
 　　if(window.requestAnimationFrame)
     {
　　　　...
     }
     else
     {
          ...
     }
}
tick();

```

## 参考链接

*	[JS：指定FPS帧频，requestAnimationFrame播放动画](http://www.cnblogs.com/kenkofox/p/3849067.html)
*	[MDN requestAnimationFrame](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestAnimationFrame)
