title: AngularJS 修饰器技巧
date: 2015-08-11 19:54:22
tags: 
- Javascript
- AngularJS
categories: 前端记录
thumbnailImage: http://7xl1ay.com1.z0.glb.clouddn.com/angular.jpg
---
可以在 `directive` 的 `link` 方法中直接使用 `translude` 方法来进行内包含元素的挪动， 在挪动的时候可以指定内包含元素的`scope`。
```javascript
app.directive('person', function() {
  return {
    restrict: 'EA',
    scope: {
      header: '='
    },
    transclude:true,
    link: function(scope, element, attrs, ctrl, transclude) {
      scope.person = {
        name: 'Directive Joe',
        profession: 'Scope guy'
      };

      scope.header = 'Directive\'s header';
      transclude(scope, function(clone, scope) {
        element.append(clone);
      });
    }
  };
});

```
<!-- more -->
可以使用修饰器 `$provide.decorator` 来对第三方的 `directive` 进行二次加工。
需要在相应 `module` 的 `config` 中针对 `$provide.decorator` 进行配置。
具体例子[参考](http://angular-tips.com/blog/2013/09/experiment-decorating-directives/)

```javascript
app.config(function($provide) {
  $provide.decorator('fooDirective', function($delegate) {
    var directive = $delegate[0];

    directive.scope.fn = "&";
    var link = directive.link;

    directive.compile = function() {
      return function(scope, element, attrs) {
        link.apply(this, arguments);
        element.bind('click', function() {
          scope.$apply(function() {
            scope.fn();
          });
        });
      };
    };

    return $delegate;
  });
});

```

位于 `$scope` 中的计算类属性（函数）是可以随 `$scope` 变化导致的 `digest loop` 一起在模板中随其他变量进行变化展示的，你也可以使用 `Object.definePrototype` 来进行函数的属性化 (`setter` `getter`)。

```javascript
var app = angular.module('app', []);

app.controller('MyCtrl', function($scope) {
  $scope.firstName = "John";
  $scope.lastName = "Doe";

  Object.defineProperty($scope, 'fullName', {
    get: function() {
      return $scope.firstName + ' ' + $scope.lastName;
    }
  });

  $scope.counter = -1;

  $scope.$watch('fullName', function() {
    $scope.counter++;
  });
});

```
`Factory` 返回的是单例， `Service` 在第一次使用的时候会进行构造，在后期的调用中始终是使用第一次构造好的实例。`Provider`一般是直接使用到 `Module` 的 `config` 中。

`$watch` 方法执行后会返回一个函数，调用该函数会停止当前 `$watch` 的监视操作。

尽量在 `$apply` 函数中进行 `angular digest loop` 之外的 `$scope` 操作。


