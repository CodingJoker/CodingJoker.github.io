---
title: grunt-contrib-connect 中间件middleware属性
id: 201
categories:
  - Gulp / Grunt
date: 2015-12-23 17:20:58
tags:
---
<Excerpt in index | 首页摘要>
+ Grunt 中间件的创建
+ 代码！代码！代码！

<!-- more -->
<The rest of contents | 余下全文>

	说道这个属性，简直就是这个插件的神来之笔，这个属性可扩展自己在本地的静态文件服务器。用处太大了。

	第一次接触到这个属性是因为想搭一个可以按下ctrl+s之后浏览器自动刷新页面的一个功能，在网上找了找，发现前辈们已经实现了这个功能，而这个技术的**关键点就是通过middleware与长连接，grunt-contrib-watch结合，实现静态注入浏览器中打开的html长连接，非常实用**，听到这个功能还不错的话可以到菜单中的grunt中找找。

好了，如正文了，我们这篇文章要实现一个类似于**模板加载引擎**的功能（将文件中的&lt;!--HeadFragment&gt;&lt;--&gt;&lt;!--/HeadFragment&gt;&lt;--&gt;标签之间添加上一些在head中项目文件可能共有的部分，类似于angularjs中的路由什么的，或者是说jsp，php中的include，require功能）。而这个模板加载引擎的功能就的核心就是匹配，读文件，修改文件后再会写，不同于js中的ajax。

因为这个只是本地的，所以根本不用考虑性能什么的，因为项目上线的时候不会用到这些项目管理的文件。

> 首先来看看我们的grunt.init中的connect任务中的配置：

![](http://jumorzhumin-wordpress.stor.sinaapp.com/uploads/2015/12/QQ截图20151223165820.png)

> 如图，middleware后面紧跟一个函数，这个函数会**返回一个数组**，在你请求当前服务器下的某个url时connect就会顺序执行该数组中的函数。


> 说清楚middleware的值后让我们来看看它的值是什么鬼（这个我看文档差点没看明白，自己还是太弱）。



![](http://jumorzhumin-wordpress.stor.sinaapp.com/uploads/2015/12/QQ截图20151223165912.png)

	在这个数组中，首先我们创建一个自定义函数，我们自己需要的参数有connect中的配置option，当然，**这个不是固定的模式**，主要是看你项目的需要。之后return中的第一个函数就是实现上面说的功能的，具体的地方我可以讲一下，

其中引用了三个参数时connect插件自带的，  ** req 是正在请求的对象，eg:req.url返回当前服务器请求的url，res是请求的响应，next指向中间件函数队列中的下一个需要执行的函数，在这里next所指代的就是lrSnippet**，在myMiddleware中就是我所说的读文件，替换，写文件，实现覆盖，具体的都在代码中了，也不复杂，有些函数不懂得话 可以去grunt官网上file操作中看看，挺基础的。然后将自己注册的任务起一下就可以实现我所说的模板引擎了。





