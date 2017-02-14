


#### CommonJS/AMD/CMD

```
CommonJS是主要为了JS在后端的表现制定的
AMD(异步模块定义)出现了，它就主要为前端JS的表现制定规范 - RequireJS


RequireJS: define 前依赖
define(['dep1', 'dep2'], function (dep1, dep2) {
    //Define the module value by returning a value.
    return function () {};
});

CMD(通用模块定义)

SeaJS: -  define/require
define(function(require, exports, module) {
    console.log('require module: main');

    var mod1 = require('./mod1');
    mod1.hello();
    var mod2 = require('./mod2');
    mod2.hello();

    return {
        hello: function() {
            console.log('hello main');
        }
    };
});


SeaJS对模块的态度是懒执行, 而RequireJS对模块的态度是预执行

```

modulejs 简介信息

modulejs是极致模块化的且面向对象的javascript框架，modulejs能让你在简单易用的前提下写出更佳维护性和可重用性的javascript代码。 modulejs中一个很重要的概念就是按需加载， modulej不是像jQuery一样在使用前加载整个已经打包的一个js文件，而是加载模块的最小集合。关于为什么需要按需加载，modulejs认为这不仅可以减少服务器的流量，更重要的是因为按需加载页面的加载时间也会大幅缩短，最终对用户更友好。