---
title: AngularJs ui-router controller加载两次的问题
date: 2016-05-21 09:19:37
categories:
  - angularJs
  - 前端小记
tags:
	AngularJS
---

<Excerpt in index | 首页摘要>
+ AngularJs Ui-router Bug
+ How To Deal
<!-- more -->
<The rest of contents | 余下全文>

这个问题是在项目开发的过程中遇到的，在网上也很难找到解决方案，不过好在在`ui-router`的`github`问题中找到了解决方案。

> 先来描述下这个问题：

	这是一个在正常情况下发生的问题，也就是说，跟Angular Controller 加载两次的一般常见问题无关，比如说像模板里面有ng-controller="indexCtrl"，然后在路由中也配置了Controller这种问题。
	这次问题是在正常情况下导致的。不过这个问题的产生的先决条件是在路由上面配置了url参数。

> 举个栗子：

```
App.config(['$stateProvider', '$urlRouterProvider',function($stateProvider, $urlRouterProvider) {
    $urlRouterProvider.otherwise("homepage");
    $stateProvider
        .state('write', {
            //写邮件
            url: '/write/:id',
            controller:'write',
            templateUrl: 'tpls/write/write.html'
        })
}])
```

在这里我们配置在`write`的state上配置了参数id，当我们通过`ui-sref="write"`或者是`$state.go('write')`这种方式的时候controller就会加载两次，也不叫做是加载两次吧，应该是路由跳转了两次。

> 先来看看老外在#issue上的回复

	The first transition via ui-sref="lessons" calls $state.go('lessons', undefined). This updates the URL to /lessons/. Then, the $locationChangeSuccess handler kicks in and matches /lessons/ to lessons {param_name: ""}, then calls $state.go('lessons', {param_name: ""}). Since the parameter has changed from undefined to "", the controller is re-invoked.

	I'm wondering why we allow transitions when the required parameters are not provided. I have a TODO somewhere about aborting transitions when non-optional parameters are not provided.

> 鄙人翻译后理解了下

	当url面有参数的时候，如果直接通过之前所说的ui-sref和$state.go方法跳转路由时，首先ui-sref会调用$state.go('write',undefined), 跳转到write状态，但是当路由跳转成功之后,会触发ui-router的$locationChangeSuccess的监听事件，然后将路由上的undefined参数设置为空字符串，然后再次调用$state.go('write','')，这就导致了路由跳转了两次，自然controller就加载了两次。

> 所以解决方案是这样的

	通过手动配置ui-sref上的参数还有$state.go的参数来防止ui-router帮我们修改参数导致路由跳转两次。Eg:ui-sref="write({id:''})",$state.go('write','').

> 这样就解决了这个奇葩的问题，我在google上查了好久！！！

> 附上ui-router的issue#1476：
> [ui-sref initialze controller twice if url has params #1476](https://github.com/angular-ui/ui-router/issues/1476#issuecomment-62807222 "issues#1476")

