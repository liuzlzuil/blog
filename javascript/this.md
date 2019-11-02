# 执行上下文中的this

### `this`的几种指向
- **单独的情况下，`this`指向全局对象**
```javascript
console.log(this === window); // true
```

- **在函数中，非严格模式下`this`指向全局对象，严格模式下是`undefined`**
```javascript
function foo() {
  console.log(this === window);
}
function bar() {
  'use strict'; // 使用严格模式
  console.log(this === undefined);
}
foo();  // true
bar();  // true
```

- **在对象方法中，`this`指向方法的拥有者**
```javascript
var value = 'window';
var foo = {
  value: 'foo',
  getValue () {
    console.log(this.value);
  }
}
var getValue = foo.getValue;
foo.getValue(); // foo
getValue(); // window
```

- **调用`new`生成的实例对象，`this`指向实例对象本身**
```javascript
function Foo(value) {
  this.value = value;
}
Foo.prototype.getValue = function () {
  console.log(this.value);
}
var foo = new Foo('实例foo');
foo.getValue(); // 实例foo
```

- **使用`call,apply,bind`来改变`this`的指向**
```javascript
var value = 'window';
function fn() {
  console.log(this.value);
}
var foo = {
  value: 'foo'
}
fn(); // window
fn.call(foo); // foo
```

- **在事件中，`this`指向接收事件的元素**
```html
<!-- html -->
<div id="foo"><div>
```
```javascript
// js
var el = document.querySelector('#foo');
el.onclick = function() {
  console.log(this.id);
}
el.click(); // foo
```

- **值得注意箭头函数的`this`是根据*外层* 执行上下文决定的**
```javascript
var value = 'window';
var foo = {
  value: 'foo',
  showValue: function () {
    console.log(this.value);
  } 
}
var bar  = {
  value: 'bar',
  showValue: () => {
    console.log(this.value);
  }
}
var baz = {
  value: 'baz',
  showValue: function () {
    setTimeout(() => {
      console.log(this.value);
    }, 0);
  }
}
foo.showValue(); // foo
bar.showValue(); // window  这里是指向外层所以不bar
baz.showValue(); // baz
```

### 注意 坑
 - 当构造函数有返回值时
返回值是一个对象，`new`出来的实例`this`指向的是这个返回的对象
返回值是其他值，仍然指向实例本身
```javascript
function Foo(value) {
  this.value = value;
  return {
    value: '返回一个对象',
    getValue: function () {
      console.log(this.value);
    }
  }
}
Foo.prototype.getValue = function () {
  console.log(this.value);
}

function Bar(value) {
  this.value = value;
  return '返回一个字符串';
}
Bar.prototype.getValue = function () {
  console.log(this.value);
}

var foo = new Foo('实例foo');
var bar = new Bar('实例bar');
foo.getValue(); // 返回一个对象
bar.getValue(); // 实例bar
```

 - 箭头函数的`this`不能被修改
```javascript
function fn() {
  return () => {
    console.log(this.value)
  }
}
var foo = {
  value: 'foo'
}
var bar = {
  value: 'bar'
}
var arrowFn =  fn.call(foo);
arrowFn(); // foo
arrowFn.call(bar); // foo
```


### 一道面试题
```javascript
var value = 1;
function fn() {
  console.log(this.value);
}
var foo = {
  value: 2,
  getValue: function(fn) {
    this.value = 3;
    fn();  // 第一行
    arguments[0]();  // 第二行
  }
}
foo.getValue(fn);
```
