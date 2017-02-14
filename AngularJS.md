#AngularJS 知识汇总


```
 (1)MVC模型

Model：即业务数据   $scope.xx  $rootScope.xx

View：即业务数据的呈现   HTML + ngXxx

Controller：负责操作业务数据   .controller('', function(){})

  (2)双向数据绑定

方向1：Model绑定到View，只要Model变View随着变

方向2：View(表单控件)绑定到Model，只要View变Model随着变

  (3)依赖注入

  (4)模块化设计

 

面试题：AngularJS的最大的不足/应用时需要特别关注的地方？

原生ES/JS/DOM只有特定HTML元素的特定事件的监听机制，没有监听数据/对象/值改变的机制。

 

$interval和window.setInterval()的区别？

$interval修改的任何Model数据，底层会立即遍历一遍$digest队列；

setInterval()即使修改了Model数据，也不会遍历$digest队列；

$interval(function(){ $scope.count++; }, 1000)

等价于

setInterval(function(){ $scope.count++;  $digetst()/$apply(); }, 1000)
```




```
watcher = {
  fn: listener,
  last: initWatchVal,
  get: get,
  exp: prettyPrintExpression || watchExp,
  eq: !!objectEquality
};
```
+ Angular并不是周期性触发藏检查。
+ 只有当触发UI事件，ajax请求或者 timeout 延迟事件，才会触发脏检查。
+ 为什么叫脏检查? 对脏数据的检查就是脏检查，比较UI和后台的数据是否一致!


```
每一个绑定到UI上的数据都有拥有一个对应的$watch 对象，这个对象会被push到watchList中
<span>{{user}}</span>
<span>{{password}}</span>
这就会插入两个$watch 对象。
```

这就涉及到了DC中判断数据是否变“脏”的几种逻辑： 

1. 基于值的检查 
2. 基于引用的检查 
3. 基于集合的检查 [1, 2, 3] !== [1, 2, 3] // true



$digest函数的作用是调用这个监控函数，并且比较它返回的值和上一次返回值的差异。如果不相同，监听器就是脏的，它的监听函数就应当被调用。

迭代的最大值称为TTL（short for Time To Live）。这个值默认是10

$apply使用函数作参数，它用$eval执行这个函数，然后通过$digest触发digest循环。



####交互过程中的这几个循环是怎么工作的？

```
1.  首先，浏览器会一直处于监听状态，一旦有事件被触发，就会被加到一个event queue中，event queue中的事件会一个一个的执行。
2.  event queue中的事件如果是被$apply()包起来的话，就会进入到AngularJS的context中，这里的fn()是我们希望在AngularJS的context中执行的函数。
3.  AngularJS将执行fn()函数，通常情况下，这个函数会改变应用的某些状态。
4.  然后AngularJS会进入到由两个小循环组成的$digest循环中，一个循环是用来处理$evalAsync队列（用来schedule一些需要在渲染视图之前处理的操作，通常通过setTimeout(0)实现，速度会比较慢，可能会出现视图抖动的问题）的，一个循环是处理$watch列表（是一些表达式的集合，一旦有改变发生，那么$watch函数就会被调用）的。$digest循环会一直迭代知道$evalAsync队列为空并且$watch列表也为空的时候，即model不再有任何变化。
5.  一旦AngularJS的$digest循环结束，整个执行就会离开AngularJS和Javascript的context，紧接着浏览器就会把数据改变后的视图重新渲染出来。

我们还是结合代码来解析一下：

<!doctype html>
<html ng-app>
 <head>
 <script src="angular.js"></script>
 </head>
 <body>
 <input ng-model="name">
 <p>Hello {{name}}!</p>
 </body> 
</html>


这段代码和上一段代码唯一的区别就是有了一个input来接收用户的输入。在用浏览器去访问这个html文件的时候，input上的ng-model指令会给input绑上keydown事件，并且会给name变量建议一个$watch来接收变量值改变的通知。在交互阶段主要会发生以下一系列事件：
1.  当用户按下键盘上的某一个键的时候（比如说A），触发input上的keydown事件；
2.  input上的指令察觉到input里值的变化，调用$apply(“name=‘A'”)更新处于AngularJS的context中的model；
3.  AngularJS将'A'赋值给name；
4.  $digest循环开始，$watch列表检测到name值的变化，然后通知{{name}}表达式，更新DOM；
5.  退出AngularJS的context，然后退出Javascript的context中的keydown事件；
6.  浏览器重新渲染视图。
```












```
<!Doctype html>
<html ng-app>
    <head>
        <meta charset="utf-8">
        <script>
            function HelloAngular($scope){
                $scope.greeting = {
                    text: "Hello"
                };
            }
        </script>
    </head>
    <body>
        <div ng-controller="HelloAngular">
            <p>{{greeting.text}},Angular</p>
        </div>
    </body>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.2.5/angular.min.js"></script>
</html>
```


```
<!DOCTYPE html>
<html lang="en">
	<head>
	    <title>Angular Helloworld</title>
	</head>
	<body ng-app="myapp">
		<div ng-controller="HelloController" >
		    <h2>Hello { { helloTo } } !</h2>
		</div>
		<script>
		angular.module("myapp", [])
		    .controller("HelloController", function($scope) {
		        $scope.helloTo = "AngularJS";
		    } );
		</script>
		<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.2.5/angular.min.js"></script>
	</body>
	
</html>
```


























####【参考】

* [AngularJS--双向数据绑定](http://www.cnblogs.com/baiyanfeng/p/5042949.html)
* [Angularjs 脏值检测](http://www.cnblogs.com/xuezhi/p/4897831.html)
* [脏检查深入分析](http://www.cnblogs.com/likeFlyingFish/p/6183630.html)
* [Digest Cycle](http://blog.csdn.net/dm_vincent/article/details/51407292)
* [angularJS的核心特性](http://www.cnblogs.com/pp-cat/p/4631186.html)