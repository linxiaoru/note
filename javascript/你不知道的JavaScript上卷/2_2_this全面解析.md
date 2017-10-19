---
title: 第二章 this 全面解析 
tags: javascript, this, 对象原型
notebook: 你不知道的JS上
---

### 2.1 调用位置
**调用位置：** 调用位置就是函数在代码中被调用的位置（而不是声明的位置）。  </br>
**调用栈：** 为了到达当前执行位置所调用的所有函数。
```javascript
  function baz() {
    // 当前调用栈是： baz
    // 因此，当前调用位置是全局作用域

    console.log( "baz" );
    bar();  // <-- bar 的调用位置    
  }

  function bar() {
    // 当前调用栈是： baz -> bar
    // 因此，当前调用位置在 baz 中

    console.log( "bar" );
    foo();  // <-- foo 的调用位置    
  }

  function foo() {
    // 当前调用栈是： baz -> bar -> foo
    // 因此，当前调用位置在 bar 中

    console.log( "foo" );
  }

  baz();  // <-- baz 的调用位置
```

### 2.2 绑定规则
#### 2.2.1 默认绑定
**独立函数调用：** 可以把这条规则看作是无法应用其他规则时的默认规则。
```javascript
  function foo() {
    console.log( this.a );
  }

  var a = 2;

  foo();
```
声明在全局作用域中的变量（比如 var a = 2）就是全局对象的一个同名属性。

本例中函数调用时应用了 this 的默认绑定，因此 this 指向全局对象。 foo() 是直接使用不带任何参数修饰的函数引用进行调用的，因此只能使用 *默认绑定* ，无法应用其他规则。

如果使用严格模式（ strict mode ），则不能将全局对象用于默认绑定，因此 this 会绑定到 undefined；

#### 2.2.2 隐式绑定
当函数引用有上下文对象时，**隐式绑定** 规则会把函数调用中的 this 绑定到这个上下文对象。

我们必须在一个对象内部包含一个指向函数的属性，并通过这个属性间接引用函数，从而把 this 间接（隐式）绑定到这个对象上。

对象属性引用链中只有上一层或者说最后一层在调用位置中起作用。示例：
```javascript
  function foo() {
    console.log( this.a );
  }

  var obj2 = {
    a: 42,
    foo: foo
  };

  var obj1 = {
    a: 2,
    obj2: obj2
  };

  obj2.foo();   // 42
  obj1.obj2.foo();    // 42
```

**隐式丢失**
被隐式绑定的函数会丢失绑定对象，也就是说它会应用默认绑定，从而把 this 绑定到全局对象或者 undefined 上，取决于是否是严格模式。

```javascript
  function foo() {
    console.log( this.a );
  }

  var obj = {
    a: 2,
    foo: foo
  };
  
  var bar = obj.foo;  // 函数别名！
  var a = "oops, global";   // a 是全局对象的属性

  bar();  // "oops, global"
```
虽然 bar 是 obj.foo 的一个引用，但实际上，它引用的是 foo 函数本身，因此此时的 bar() 其实是一个不带任何修饰的函数调用，因此应用了默认绑定。

回调函数丢失 this 是非常常见的，此外，回调函数的函数可能会修改 this。在一些流行的 JS 库中事件处理器会把回调函数的 this 强制绑定到触发事件的 DOM 元素上。

回调函数丢失 this 实际上因为回调函数的调用位置问题触发的，即便是 javascript 语言内置的函数，比如说 setTimeout()。

JavaScript 环境中内置的 setTimeout() 函数实现和下面的伪代码类似：
```javascript
  function setTimeout(fn, delay) {
    // 等待 delay 毫秒
    fn();   // <-- 调用位置！
  }
```

#### 2.2.3 显式绑定
**显式绑定：** 使用函数的 call(..) 和 apply(..) 方法直接指定 this 的绑定对象。

call(..) 和 apply(..) 的第一个参数是一个对象，它们会把这个对象绑定到 this。
```javascript
  function foo() {
    console.log( this.a );
  }

  var obj = {
    a: 2
  };

  foo.call( obj );    // 2
```
通过 foo.call(..) 在调用 foo 时强制把它的 this 绑定到 obj 上。 

**装箱：** 如果你传入了一个原始值（字符串类型、布尔类型或者数字类型）来当作 this 的绑定对象，这个原始值会被转换成它的对象形式（也就是 new String(..)、new Boolean(..) 或者 new Number(..)）。

显式绑定也会有丢失绑定问题。解决办法是使用显式绑定的变种方法。

**1.硬绑定**
- 典型应用场景——创建一个包裹函数，传入所有的参数并返回接收到的所有值：
  ```javascript
    function foo(something) {
      console.log( this.a, something );
      return this.a + something
    }

    var obj = {
      a: 2
    };

    var bar = function() {
      return foo.apply( obj, arguments );
    };

    var b = bar( 3 );   // 2 3
    console.log( b );   // 5
  ```

- 创建一个可以重复使用的辅助函数：
  ```javascript
    function foo(something) {
      console.log( this.a, something );
      return this.a + something;
    }

    // 简单的辅助绑定函数
    function bind(fn, obj) {
      return function() {
        return fn.apply( obj, arguments );
      };
    }

    var obj = {
      a: 2
    };

    var bar = bind( foo, obj );

    var b = bar( 3 );   // 2 3
    console.log( b );   // 5
  ```

ES5 提供了内置的方法 Function.prototype.bind，使用方法（改写上例 `var bar = foo.bind( obj )`） bind(..) 会返回一个硬编码的新函数，它会把参数设置为 this 的上下文并调用原始函数。

**2. API 调用的“上下文”**
第三方的许多函数，以及 JavaScript 语言和宿主环境中许多新的内置函数，都提供了一个可选的参数，通常被称为“上下文”（context），其作用和 bind(..) 一样，确保你的回调函数使用指定的 this。
例：
```javascript
  function foo(el) {
    console.log( el, this.id );
  }

  var obj = {
    id: "awesome"
  };

  // 调用 foo(..) 时把 this 绑定到 obj 
  [1, 2, 3].forEach( foo, obj );
  // 1 awesome 2 arguments 3 awesome
```
这些函数实际上就是通过 call(..) 或者 apply(..) 实现了显式绑定。

#### 2.2.4 new 绑定
在 JavaScript 中，构造函数只是一些使用 new 操作符时被调用的函数。它们并不会属于某个类，也不会实例化一个类。实际上，它们甚至都不能说是一种特殊的函数类型，它们只是被 new 操作符调用的普通函数而已。

实际上并不存在所谓的“构造函数”， 只有对于函数的“构造调用”。

使用 new 来调用函数，或者说发生构造函数调用时，会自动执行下面的操作：</br>
1. 创建（或者说构造）一个全新的对象。
2. 这个新对象会被执行[[原型]]连接。
3. 这个新对象会绑定到函数调用的 this。
4. 如果函数没有返回其他对象，那么 new 表达式中的函数调用会自动返回这个新对象。

```javascript
  function foo(a) {
    this.a = a;
  }

  var bar = new foo(2);
  console.log( bar.a );   // 2
```
使用 new 来调用 foo(..) 时，我们会构造一个新对象并把它绑定到 foo(..) 调用中的 this 上。

### 2.3 优先级
**判断 this**
1. 函数是否在 new 中调用（new 绑定）？如果是的话，this 绑定的是新创建的对象。
```javascript
  var bar = new foo()
```
2. 函数是否通过 call、apply（显式绑定）或者硬绑定调用？如果是的话，this 绑定的是指定的对象。
```javascript
  var bar = foo.call(obj2)
```
3. 函数是否在某个上下文对象中调用（隐式绑定）？如果是的话，this 绑定的是那个上下文对象。
```javascript
  var bar = obj1.foo()
```
4. 如果都不是的话，使用默认绑定。如果在严格模式下，就绑定到 undefined，否则绑定到全局对象。
```javascript
  var bar = foo()
```

### 2.4 绑定例外
#### 2.4.1 被忽略的 this 
**DMZ 对象** ——一个空的非委托对象。

JavaScript 中创建一个空对象最简单的方法是 Object.create(null)，Object.create(null) 和 {} 很像，但是并不会创建 Object.prototype 这个委托，所以它比 {} “更空”
```javascript
  function foo(a, b) {
    console.log( "a:" + a + ", b:" + b );
  }

  // 我们的 DMZ 空对象
  var ø = Object.create( null ); 

  // 把数组展开成参数
  foo.apply( ø, [2, 3] );   // a:2, b:3

  // 使用 bind(..) 进行柯里化
  var bar = foo.bind( ø, 2 );
  bar( 3 );   // a:2, b:3
```
使用变量名 ø 不仅让函数变得更加“安全”， 而且可以提高代码的可读性， 因为 ø 表示“我希望 this 是空”， 这比 null 的含义更清楚。 

#### 2.4.2 间接引用
另一个需要注意的是， 你有可能（有意或者无意地） 创建一个函数的“间接引用”， 在这种情况下， 调用这个函数会应用默认绑定规则。

*间接引用* 最容易在赋值时发生：
```javascript
  function foo() {
    console.log( this.a );
  }

  var a = 2;
  var o = { a: 3, foo: foo };
  var p = { a: 4 };

  o.foo();    // 3
  (p.foo = o.foo)();    // 2
```
赋值表达式 p.foo = o.foo 的返回值是目标函数的引用，因此调用位置是 foo() 而不是 p.foo() 或者 o.foo()。这里应用默认绑定。

**注意:** 对于默认绑定来说，决定 this 绑定对象的并不是调用位置是否处于严格模式，而是函数体是否处于严格模式。如果函数体处于严格模式，this 会被绑定到 undefined，否则 this 会被绑定到全局对象。

#### 2.4.3 软绑定
硬绑定可以把 this 强制绑定到指定的对象（除了使用 new 时），防止函数调用应用默认绑定规则。但是硬绑定会大大降低函数的灵活性，使用硬绑定之后就无法使用隐式绑定或者显式绑定来修改 this。

如果可以给默认绑定指定一个全局对象和 undefined 以外的值，那就可以实现和硬绑定相同的效果，同时保留隐式绑定或者显式绑定修改 this 的能力。

使用软绑定方法来解决。
```javascript
  if (!Function.prototype.softBind) {
    Function.prototype.softBind = function(obj) {
      var fn = this;
      // 捕获所有 curried 参数
      var curried = [].slice.call( arguments, 1);
      var bound = function() {
        return fn.apply(
          (!this || this === (window || global))?
            obj : this,
            curried.concat.apply( curried, arguments )
        );
      };
      bound.prototype = Object.create( fn.prototype );
      return bound;
    };
  }
```
除了软绑定之外，softBind(..) 的其他原理和 ES5 内置的 bind(..) 类似。它会对指定的函数进行封装，首先检查调用时的 this，如果 this 绑定到全局对象或者 undefined，那就把指定的默认对象 obj 绑定到 this，否则不会修改 this。此外这段代码还支持可选的柯里化。

### 2.5 this 词法
箭头函数不使用 this 的四种标准规则，而是根据外层（函数或者全局）作用域来决定 this。 
```javascript
  function foo() {
    // 返回一个箭头函数
    return (a) =>{
      // this 继承自 foo()
      console.log( this.a );
    };
  }

  var obj1 = {
    a: 2
  };

  var obj2 = {
    a: 3
  };

  var bar = foo.call( obj1 );
  bar.call( obj2 );   // 2, 不是 3！
```
foo() 内部创建的箭头函数会捕获调用时 foo() 的 this。由于 foo() 的 this 绑定到 obj1，bar（引用箭头函数）的 this 也会绑定到 obj1，箭头函数的绑定无法被修改。（new 也不行！）

箭头函数最常用于回调函数中，例如事件处理器或者定时器。
```javascript
  function foo() {
    setTimeout(() => {
      // 这里的 this 在词法上继承自 foo()
      console.log( this.a );
    }, 100);
  }

  var obj = {
    a: 2
  };

  foo.call( obj );    // 2
``` 

箭头函数可以像 bind(..) 一样确保函数的 this 被绑定到指定对象，此外，其重要性还体现在它用更常见的词法作用域取代了传统的 this 机制。

### 2.6 小结
如果要判断一个运行中函数的 this 绑定，就需要找到这个函数的直接调用位置。找到之后就可以顺序应用以下四条规则来判断 this 的绑定对象。
1. 有 new 调用？绑定到新创建的对象。
2. 由 call 或者 apply（或者 bind）调用？绑定到指定的对象。
3. 由上下文对象调用？绑定到那个上下文对象。
4. 默认：在严格模式下绑定到 undefined，否则绑定到全局对象。

有些调用可能在无意中使用默认绑定规则。如果想“更安全”地忽略 this 绑定，可以创建一个 DMZ 对象，比如  `ø = Object.create(null)` ，以保护全局对象。

ES6 中的箭头函数并不会使用四条标准的绑定规则，而是根据当前的词法作用域来决定 this，具体来说，箭头函数会继承外层函数调用的 this 绑定（无论 this 绑定到什么）。这其实和 ES6 之前代码中的 `self = this` 机制一样。