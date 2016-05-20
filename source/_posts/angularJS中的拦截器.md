---
title: angularJS中的拦截器
id: 308
categories:
  - angularJs
  - 前端小记
date: 2016-04-10 11:42:34
tags:
---
<Excerpt in index | 首页摘要>
+ 拦截器的实现原理
+ 拦截器在Angular如何实现
<!-- more -->
<The rest of contents | 余下全文>

这里我将的拦截器，是关于前端项目的请求的拦截器，基于angularJS中的httpProvider的。

有没有这样一种场景，在使用ajax请求的时候，服务端需要验证header中的token和cookie，所以每次请求的时候都需要加上这个token，cookie不用加，每次请求的时候浏览器都会帮你带上，所以你就需要在有ajax的地方都添加一段向header中写token的代码，是不是觉得挺闹心的？

别怕，我来帮你解决这个问题。

> 在angularJS官方api中$http处有这么一段话：

	For purposes of global error handling, authentication, or any kind of synchronous or asynchronous pre-processing of request or postprocessing of responses, it is desirable to be able to intercept requests before they are handed to the server and responses before they are handed over to the application code that initiated these requests. The interceptors leverage the [promise APIs](http://docs.angularjs.cn/api/ng/service/$q) to fulfill this need for both synchronous and asynchronous pre-processing.

	The interceptors are service factories that are registered with the `$httpProvider` by adding them to the `$httpProvider.interceptors`array. The factory is called and injected with dependencies (if specified) and returns the interceptor.

> 这段话的意思就是通过使用promise对象（处理回调的一个非常使用的对象）和$httpProvider.interceoters这个数组，按照该数组的指定格式，就可以对请求的过程进行控制，数据的格式是这样的：

```
 {

 // optional method
 'request': function(config) {
 // do something on success
 return config;
 },

// optional method
 'requestError': function(rejection) {
 // do something on error
 if (canRecover(rejection)) {
 return responseOrNewPromise
 }
 return $q.reject(rejection);
 },

 

// optional method
 'response': function(response) {
 // do something on success
 return response;
 },

// optional method
 'responseError': function(rejection) {
 // do something on error
 if (canRecover(rejection)) {
 return responseOrNewPromise
 }
 return $q.reject(rejection);
}
```
相信一些小伙伴看了这个数据已经有些感觉了，因为angular 的$http是封装了ajax的，所以可以通过$httpProvider控制请求的过程。

>附上我在程序中的代码加深理解：

```
var App = angular.module('app',[]);

App.factory('authInterceptor',['$q','authService','mAlert',function($q,authService,mAlert){
 return {
 'request':function(config){
 var token = (authService.getToken());
 config.headers['author'] = token;
 return config ||$q.when(config);
 },
 'responseError' :function(resError) {
 if(resError.status == 404){
 mAlert.alert({
 title:'系统提示',
 body:'请求资源未找到'
 })
 }
 return $q.reject(resError);
 }
 }
}])
.provider('authService',function(){
 var token="zhumin",
 self = this;
 self.$get = function(){
 return {
 getToken:function(){
 return token;
 }
 }
 }
}).config(['$httpProvider',function($httpProvider){

$httpProvider.interceptors.push('authInterceptor')

}])
```

mAlert是我自己封装的一个模态框，可以忽略。

> factory返回了一个$httpProvider.interceptors数据规定的格式，用于当request之前为header添加token，reponseError用与服务端返回错误状态时的操作。provider相当于封装了一个类，用于factory中调用。

> 在config中将拦截器注入数据中，就可以实现在整个引用程序中的ajax请求的header中带上token，效果不错吧~

> 效果图如下：

[![](http://jumorzhumin-wordpress.stor.sinaapp.com/uploads/2016/04/httpProvider.png "httpProvider")](http://jumorzhumin-wordpress.stor.sinaapp.com/uploads/2016/04/httpProvider.png)

&nbsp;