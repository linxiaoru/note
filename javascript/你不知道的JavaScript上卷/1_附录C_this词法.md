---
title: 附录C this词法
tags: javascript, 作用域, 闭包
notebook: 你不知道的JS上
---

```javascript
  var obj = {
    id: "awesome",
    cool: function coolFn() {
      console.log( this.id );
    }
  };

  var id = "not awesome";

  obj.cool();   // 酷

  setTimeout( obj.cool, 100 );    // 不酷
```
问题在于 cool() 函数丢失了同 this 之间的绑定。解决办法好几种，最常用的是 `var self = this;`。
如下：
```javascript
  var obj = {
    count: 0,
    cool: function coolFn() {
      var self = this;

      if(self.count < 1) {
        setTimeout( function timer() {
          self.count++;
          console.log( "awesome?" );
        }, 100 );
      }
    }
  };

  obj.cool();   // 酷吧？
```
self 只是一个可以通过词法作用域和闭包进行引用的标识符，不关心 this 绑定的过程中发生了什么。

ES6 中的箭头函数引入了一个叫作 this 词法的行为:
```javascript
  var obj = {
    count: 0,
    cool: function coolFn() {
      if (this.count < 1) {
        setTimeout(() => {
          this.count++;
          console.log( "awesome?" );
        }, 100 );
      }
    }
  };

  obj.cool();   // 很酷吧？
```
箭头函数在涉及 this 绑定时的行为和普通函数的行为完全不一致。它放弃了所有普通 this 绑定的规则，取而代之的是用当前的词法作用域覆盖了 this 本来的值。

因此，这个代码片段中的箭头函数并非是以某种不可预测的方式同所属的 this 进行绑定，而只是“继承”了 cool() 函数的 this 绑定（因此调用它并不会出错）。

正确使用和包含 this 机制的解决办法：
```javascript
  var obj = {
    count: 0,
    cool: function coolFn() {
      if (this.count < 1) {
        setTimeout( function timer(){
          this.count++;   // this 是安全的，因为 bind(..)
          console.log( "more awesome" );
        }.bind( this ), 100); // look, bind()!
      }
    }
  };

  obj.cool();   // 更酷了
```