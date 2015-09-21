title: Angular中使用$apply的注意事项
date: 2015-09-21 16:23:33
tags:
- Javascript
- AngularJS
categories: 前端记录
thumbnailImage: http://7xl1ay.com1.z0.glb.clouddn.com/angular.jpg
---
一般在AngularJS中我们是靠各种框架自己封装的事件中来触发`digest loop`来达到双向绑定属性脏检查的。有的时候我们对`$scope`操作的时候，是在AngularJS作用域外进行的，这时候我们会发现双向绑定失效了，因为我们没有触发属性脏检查，这种时候，我们就要用`$apply`来主动触发`digest loop`。
但是，有时候我们的`$apply`是处于一个外部的回调中触发的，如果一不小心回调频繁调用了多次，那么就会报错。因为`$apply`时AngularJS发现程序已经在一个$digest loop过程中了。
为了防止这种现象，我们需要在进行`$apply`时进行检查，可以封装成一个新的方法：
```javascript
$scope.safeApply = function(fn) {
  var phase = this.$root.$$phase;
  if(phase == '$apply' || phase == '$digest') {
    if(fn && (typeof(fn) === 'function')) {
      fn();
    }
  } else {
    this.$apply(fn);
  }
};
```

这样使用：
```javascript
$scope.safeApply(function() {
  alert('Now I'm wrapped for protection!');
});
```
