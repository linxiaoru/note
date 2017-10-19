---
title: 第一章 关于this
tags: javascript, this, 对象原型
notebook: 你不知道的JS上
---

### 1.1 为什么要用 this
this 提供了一种更优雅的方式来隐式“传递”一个对象引用，因此可以将 API 设计得更加简介并且易于复用。

### 1.2 误解
#### 1.2.1 指向自身
```javascript
  function foo(num) {
    console.log( "foo " + num );

    // 记录 foo 被调用的次数
    this.count++;
  }

  foo.count = 0;

  var i;

  for (i=0; i<10; i++) {
    if (i > 5) {
      foo( i );
    }
  }
  // foo: 6
  // foo: 7
  // foo: 8
  // foo: 9

  // foo 被调用了多少次？
  console.log( foo.count );   // 0 -- WTF?
```
console.log 语句产生了 4 条输出，证明 foo(..) 确实被调用了 4 次，但是 foo.count 仍然是 0。显然从字面意思来理解 this 是错误的。

执行 foo.count = 0 时，的确向函数对象 foo 添加了一个属性 count。但是函数内部代码 this.count 中的 this 并不是指向那个函数对象，所以虽然属性名相同，根对象却并不相同。实际上，这段代码在无意中创建了一个全局变量 count，它的值为 NaN。

如果要从函数对象内部引用它自身，那只使用 this 是不够的。一般需要通过一个指向函数对象的词法标识符（变量）来引用它。

```javascript
  function foo() {
    foo.count = 4;    // foo 指向它自身
  }

  setTimeout(function() {
    // 匿名函数无法指向自身
  }, 10);
```
唯一一种可以从匿名函数对象内部引用自身的方法，是使用 arguments.callee 来引用当前正在运行的函数对象。（已弃用）

解决例子的方法有三：
一、使用词法作用域：
```javascript
  function foo(num) {
    console.log( "foo " + num );

    // 记录 foo 被调用的次数
    data.count++;
  }

  var data = {
    count: 0
  };

  var i;

  for (i=0; i<10; i++) {
    if (i > 5) {
      foo( i );
    }
  }
  // foo: 6
  // foo: 7
  // foo: 8
  // foo: 9

  // foo 被调用了多少次？
  console.log( data.count );   // 4
```

二、使用 foo 标识符替代 this 来引用函数对象：
```javascript
  function foo(num) {
    console.log( "foo " + num );

    // 记录 foo 被调用的次数
    foo.count++;
  }

  foo.count = 0;

  var i;

  for (i=0; i<10; i++) {
    if (i > 5) {
      foo( i );
    }
  }
  // foo: 6
  // foo: 7
  // foo: 8
  // foo: 9

  // foo 被调用了多少次？
  console.log( foo.count );   // 4
```

二、强制 this 指向 foo 函数对象：
```javascript
  function foo(num) {
    console.log( "foo " + num );

    // 记录 foo 被调用的次数
    // 注意，在当前的调用方式下， this 确实指向 foo
    this.count++;
  }

  foo.count = 0;

  var i;

  for (i=0; i<10; i++) {
    if (i > 5) {
      // 使用 call(..) 可以确保 this 指向函数对象 foo 本身
      foo.call( foo, i );
    }
  }
  // foo: 6
  // foo: 7
  // foo: 8
  // foo: 9

  // foo 被调用了多少次？
  console.log( foo.count );   // 4
```

#### 1.2.2 它的作用域
第二种常见误解是，this 指向函数的作用域。实际上，this 在任何情况下都不指向函数的词法作用域。在 JavaScript 内部，作用域确实和对象类似，可见的标识符都是它的属性。但是作用域“对象”无法通过 JavaScript 代码访问，它存在于 JavaScript 引擎内部。

### 1.3 this 到底是什么
this 是在运行时绑定的，并不是在编写时绑定，它的上下文取决于函数调用时的各种条件。this 的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式。

当一个函数被调用时，会创建一个活动记录（有时候也称为执行上下文）。这个记录会包含函数在哪里被调用（调用栈）、函数的调用方式、传入的参数信息。this 就是这个记录的一个属性，会在函数执行的过程中用到。

### 1.4 小结
要明确 this 既不指向函数自身也不指向函数的词法作用域。

this 实际上是在函数被调用时发生的绑定，它指向什么完全取决于函数在哪里被调用。