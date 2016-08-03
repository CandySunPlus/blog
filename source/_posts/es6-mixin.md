
title: ES6 多重继承
date: 2016-08-03 11:24:48
tags: 
- Javascript
- ES6
categories: 前端记录
thumbnailImage: [es6-logo]
---

![es6-logo](/media/es6-logo.png)

很多时候，我们需要多重继承来实现组合多个类行为，比如下面的例子

```js
class Person { ··· }
class Employee extends Person { ··· }
```

```js
class Storage {
    save(database) { ··· }
}
class Validation {
    validate(schema) { ··· }
}
```

```js
// Invented ES6 syntax:
class Employee extends Storage, Validation, Person { ··· }
```

但时可悲的是，ES6 中只支持一个继承的对象。

好在，我们可以使用动态对象的方法来实现多重继承的功能。

<!-- more -->
我们将需要多重继承的类处理一下， 以方便下方继承链的使用

```js
const Storage => Sup = class extends Sup {
	save(database) { ··· }
}
const Validation => Sup = class extends Sup {
	validate(schema) { ··· }
}
```

现在我们要组合上面两个类的行为，只需要

```js
class Employee extends Storage(Validation(Persion)) { ··· }
```

是不是方便很多？


