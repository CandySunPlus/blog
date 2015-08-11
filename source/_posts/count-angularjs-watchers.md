title: 统计 AngularJS 中的 Watchers
date: 2015-08-11 22:50:18
tags:
- AngularJS
- Javascript
categories:
- 前端记录
---
[1]: http://www.ionicframework.com "Ionic Frameworks"
[2]: https://github.com/Pasvaz/bindonce "AngularJS Bindonce"
[3]: http://plnkr.co/edit/jwrHVb?p=preview "Demo without Bindonce"
[4]: http://plnkr.co/edit/0DGOrk?p=preview "Demo with Bindonce"
最近在开发Hybrid APP (我用的是[Ionic Frameworks][1]) 的过程中，随着首页栏目的逐渐走增加，APP在手机上的执行效率越来越底，有时在切换频道的时候有明显的卡顿和执行延迟。
<!-- more -->

在检查完代码逻辑和算法后，并没有发现什么问题 （网络通讯，本地缓存）。最近在翻阅代码的过程中，突然意识到可能是由于频道增加，页面中双向绑定使用数量变多，导致 AngularJS 在作 `digest loop` 的时候，由于 `$$watchers` 数量过多导致程序变慢。

要想确定猜想，需要先能统计到页面中随着频道进入`$$watchers`的数量，于是写了如下代码，在Chrome的控制台中执行，便可以方便的进行统计。

```javascript
function getWatchers(root) {
  root = angular.element(root || document.documentElement);
  var watcherCount = 0;

  function getElemWatchers(element) {
    var isolateWatchers = getWatchersFromScope(element.data().$isolateScope);
    var scopeWatchers = getWatchersFromScope(element.data().$scope);
    var watchers = scopeWatchers.concat(isolateWatchers);
    angular.forEach(element.children(), function (childElement) {
      watchers = watchers.concat(getElemWatchers(angular.element(childElement)));
    });
    return watchers;
  }

  function getWatchersFromScope(scope) {
    if (scope) {
      return scope.$$watchers || [];
    } else {
      return [];
    }
  }

  return getElemWatchers(root);
}
getWatchers().length
```

随即发现，在频道页面不断加载的过程中， `$$watchers`的数量在不断增加，最多的时候有 5000个之多。

发现问题就好解决了。由于频道内容列表页面在一次加载后就不需要再进行变化监听，所以这里打算对代码中的`data-binding` 部分使用 [bindonce][2] 的方式解决。

这里还有两个关于 [bindonce][2] 的示例：

[使用AngularJS 原有Bind][3]
[使用Bindonce][4]

