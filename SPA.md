
#SPA

* 可以在页面切换间平滑过渡，增加Loading动画。
* 可以在各个页面间传递数据，不依赖URL。
* 可以选择性的保留状态，如音乐网站，切换页面时不会停止播放歌曲。
* 所有的标签都可以用来跳转，不仅仅是a标签。
* 避免了公共JS的反复执行，如无需在各个页面打开时都判断是否登录过等等。
* 减少了请求体积，节省流量，加快页面响应速度。
* 平滑降级到低版本浏览器上，对SEO也不会有影响。

```
Pjax(PushState + Ajax)的原理十分简单。
1. 拦截a标签的默认跳转动作。
2. 使用Ajax请求新页面。
3. 将返回的Html替换到页面中。
4. 使用HTML5的History API或者Url的Hash修改Url。
```


* [将网站打造成单页面应用SPA](https://segmentfault.com/a/1190000002920768)
* [spa-routers实现](http://blog.csdn.net/kai_l/article/details/53127868)