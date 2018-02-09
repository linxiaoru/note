### JavaScript 题目收集
1. 介绍 JavaScript 的基本数据类型？
2. 说说写 JavaScript 的基本规范？
3. JavaScript 原型、原型链是什么？有什么特点？
4. JavaScript 有几种类型的值？（堆：原始数据类型 栈：引用数据类型），画一下它们的内存图？
5. JavaScript 如何实现继承？
6. JavaScript 创建对象的几种方式？
7. JavaScript 作用域链？
8. 谈谈对 This 对象的理解？
9. eval 是做什么的？
10. 什么是window对象? 什么是document对象?
11. null，undefined的区别？
12. 写一个通用的事件侦听器函数(机试题)。
13. ["1", "2", "3"].map(parseInt) 答案是多少？
14. 关于事件，IE与火狐的事件机制有什么区别？ 如何阻止冒泡？
15. 什么是闭包（closure），为什么要用它？
16. javascript 代码中的"use strict";是什么意思 ? 使用它区别是什么？
17. 如何判断一个对象是否属于某个类？
18. new操作符具体干了什么呢?
19. 用原生JavaScript的实现过什么功能吗？
20. Javascript中，有一个函数，执行时对象查找时，永远不会去查找原型，这个函数是？
21. 对JSON的了解？
22. [].forEach.call($$(""),function(a){ a.style.outline="1px solid #"+(~~(Math.random()(1<<24))).toString(16) }) - 能解释一下这段代码的意思吗？
23. js延迟加载的方式有哪些？
24. Ajax 是什么? 如何创建一个Ajax？
25. 同步和异步的区别?
26. 如何解决跨域问题?
27. 页面编码和被请求的资源编码如果不一致如何处理？
28. 模块化开发怎么做？
29. AMD（Modules/Asynchronous-Definition）、CMD（Common Module Definition）规范区别？
30. requireJS的核心原理是什么？（如何动态加载的？如何避免多次加载的？如何 缓存的？）
31. 让你自己设计实现一个requireJS，你会怎么做？
32. 谈一谈你对ECMAScript6的了解？
33. ECMAScript6 怎么写class么，为什么会出现class这种东西?
34. 异步加载的方式有哪些？
35. documen.write和 innerHTML的区别?
36. DOM操作——怎样添加、移除、移动、复制、创建和查找节点?
37. .call() 和 .apply() 的含义和区别？
38. 数组和对象有哪些原生方法，列举一下？
39. JS 怎么实现一个类。怎么实例化这个类
40. JavaScript中的作用域与变量声明提升？
41. 如何编写高性能的Javascript？
42. 那些操作会造成内存泄漏？
43. JQuery的源码看过吗？能不能简单概况一下它的实现原理？
44. jQuery.fn的init方法返回的this指的是什么对象？为什么要返回this？
45. jquery中如何将数组转化为json字符串，然后再转化回来？
46. jQuery 的属性拷贝(extend)的实现原理是什么，如何实现深拷贝？
47. jquery.extend 与 jquery.fn.extend的区别？
48. jQuery 的队列是如何实现的？队列可以用在哪些地方？
49. 谈一下Jquery中的bind(),live(),delegate(),on()的区别？
50. JQuery一个对象可以同时绑定多个事件，这是如何实现的？
51. 是否知道自定义事件。jQuery里的fire函数是什么意思，什么时候用？
52. jQuery 是通过哪个方法和 Sizzle 选择器结合的？（jQuery.fn.find()进入Sizzle）
53. 针对 jQuery性能的优化方法？
54. Jquery与jQuery UI有啥区别？
55. JQuery的源码看过吗？能不能简单说一下它的实现原理？
56. jquery 中如何将数组转化为json字符串，然后再转化回来？
57. jQuery和Zepto的区别？各自的使用场景？
58. 针对 jQuery 的优化方法？
59. Zepto的点透问题如何解决？
60. jQueryUI如何自定义组件?
61. 需求：实现一个页面操作不会整页刷新的网站，并且能在浏览器前进、后退时正确响应。给出你的技术实现方案？
62. 如何判断当前脚本运行在浏览器还是node环境中？（阿里）
63. 移动端最小触控区域是多大？
64. jQuery 的 slideUp动画 ，如果目标元素是被外部事件驱动, 当鼠标快速地连续触发外部元素事件, 动画会滞后的反复执行，该如何处理呢?
65. 把 Script 标签 放在页面的最底部的body封闭之前 和封闭之后有什么区别？浏览器会如何解析它们？
66. 移动端的点击事件的有延迟，时间是多久，为什么会有？ 怎么解决这个延时？（click 有 300ms 延迟,为了实现safari的双击事件的设计，浏览器要知道你是不是要双击操作。）
67. 知道各种JS框架(Angular, Backbone, Ember, React, Meteor, Knockout...)么? 能讲出他们各自的优点和缺点么?
68. Underscore 对哪些 JS 原生对象进行了扩展以及提供了哪些好用的函数方法？
69. 解释JavaScript中的作用域与变量声明提升？
70. 那些操作会造成内存泄漏？
71. JQuery一个对象可以同时绑定多个事件，这是如何实现的？
72. Node.js的适用场景？
73. (如果会用node)知道route, middleware, cluster, nodemon, pm2, server-side rendering么?
74. 解释一下 Backbone 的 MVC 实现方式？
75. 什么是“前端路由”?什么时候适合使用“前端路由”? “前端路由”有哪些优点和缺点?
76. 知道什么是webkit么? 知道怎么用浏览器的各种工具来调试和debug代码么?
77. 如何测试前端代码么? 知道BDD, TDD, Unit Test么? - 知道怎么测试你的前端工程么(mocha, sinon, jasmin, qUnit..)?
78. 前端templating(Mustache, underscore, handlebars)是干嘛的, 怎么用?
79. 简述一下 Handlebars 的基本用法？
80. 简述一下 Handlerbars 的对模板的基本处理流程， 如何编译的？如何缓存的？
81. 用js实现千位分隔符?(来源：前端农民工，提示：正则+replace)
82. 检测浏览器版本版本有哪些方式？
83. 我们给一个dom同时绑定两个点击事件，一个用捕获，一个用冒泡，你来说下会执行几次事件，然后会先执行冒泡还是捕获]