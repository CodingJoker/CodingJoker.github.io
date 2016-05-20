---
title: 使用grunt中三个插件实现代码保存并自动刷新（记住！是自动，跟F5 say goodbye）
id: 117
categories:
  - Gulp / Grunt
date: 2015-11-27 20:39:41
tags:
---
<Excerpt in index | 首页摘要>
+ grunt中三个插件实现代码保存并自动刷新
+ 源码教程！源码教程！源码教程！
<!-- more -->
<The rest of contents | 余下全文>
<span style="font-family: 'trebuchet ms', geneva; font-size: xx-large;">作为一个Front  End  Developer，要是不会简化自己的操作，你就OUT了！</span>

<span style="font-size: large;">好了，我说说接下来我们要做什么，怎么做。</span>

<span style="font-size: large;">首先，你要用grunt，你得需要有npm环境，而npm是node.js附带的，所以你需要装以下node.js。什么？！你居然不知道什么是grunt？！npm？！node.s？！自（慢）己（走）百（不）度（送）~（！）。咱就按下暂停键，等你安装好了再回来见我！</span>

<span style="font-size: large;">安好了是吧，grunt的例子教程看了下没有？  package.json 和 Gruntfiles.js你需要自己配置的哦~这点应该不难吧。。。</span>

<span style="font-size: x-large;">嗯，正式入题：</span>
<span style="font-size: large;">有没有想过当你在你得编辑器里按下ctrl+s的时候，你的页面会自动刷新？就像那个慢的要死的IDE Dreamweaver一样，我才用了两天就没有用了，还是sublime大法好，骚年，你值得入手。扯远了，我们用grunt的目的就是这个，ctrl+s自动刷新。</span>

<span style="font-size: large;">对，你没有听错。</span>

<span style="font-size: large;">有没有觉得超级酷炫？！嗯，让我给你娓娓道来~~~</span>

<span style="font-size: large;">我们需要用到的四个插件  ： grunt-contrib-server ， grunt-contrib-watch ， grunt-open, connect-livereload自己用npm install下载下来。</span>

<span style="font-size: large;">最重要的一步来了，我先上代码：</span>

1.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">module.exports = function(grunt) {</span>
2.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">// LiveReload的默认端口号，你也可以改成你想要的端口号</span>
3.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">var conf = {</span>
4.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">src:'webapp',</span>
5.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">port:8083,</span>
6.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">host:'10.2.98.80',</span>
7.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">base:'webapp'</span>
8.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">}</span>
9.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">var lrPort = 35729;</span>
10.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">// 使用connect-livereload模块，生成一个与LiveReload脚本</span>
11.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">// &lt;script src="http://127.0.0.1:35729/livereload.js?snipver=1" type="text/javascript"&gt;&lt;/script&gt;</span>
12.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">var lrSnippet = require('connect-livereload')({</span>
13.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">port: lrPort</span>
14.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">});</span>
15.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">// var proxySnippet = require('grunt-connect-proxy/lib/utils').proxyRequest; //启用代理</span>
16.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">// 使用 middleware(中间件)，就必须关闭 LiveReload 的浏览器插件</span>
17.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">var lrMiddleware = function(connect, options) {</span>
18.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">return [</span>
19.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">// proxySnippet,</span>
20.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">lrSnippet, // 长连接脚本 注入到静态文件中</span>
21.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">connect.static(options.base[0]), // 静态文件服务器的路径</span>
22.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">connect.directory(options.base[0]) // 启用目录浏览(相当于IIS中的目录浏览)</span>
23.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">];</span>
24.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">};</span>
25.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">// 项目配置(任务配置)</span>
26.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">grunt.initConfig({</span>
27.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">// 读取我们的项目配置并存储到pkg属性中</span>
28.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">pkg: grunt.file.readJSON('package.json'),</span>
29.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">// 通过connect任务，创建一个静态服务器</span>
30.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">connect: {</span>
31.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">options: {</span>
32.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">// 服务器端口号</span>
33.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">port: conf.port,</span>
34.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">// 服务器地址(可以使用主机名localhost，也能使用IP)</span>
35.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">//hostname: '10.2.98.73',</span>
36.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">hostname: conf.host, //无线</span>
37.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">// 物理路径(默认为. 即根目录) 注：使用'.'或'..'为路径的时，可能会返回403 Forbidden. 此时将该值改为相对路径 如：/grunt/reloard。</span>
38.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">base: conf.base</span>
39.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">},</span>
40.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">livereload: {</span>
41.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">options: {</span>
42.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">middleware: lrMiddleware // 通过LiveReload脚本，让页面重新加载。</span>
43.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">}</span>
44.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">}</span>
45.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">},</span>
46.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">// 通过watch任务，来监听文件是否有更改</span>
47.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">watch: {</span>
48.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">client: {</span>
49.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">// 我们不需要配置额外的任务，watch任务已经内建LiveReload浏览器刷新的代码片段。</span>
50.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">options: {</span>
51.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">livereload: lrPort</span>
52.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">},</span>
53.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">// '**' 表示包含所有的子目录</span>
54.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">// '*' 表示包含所有的文件</span>
55.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">files: [conf.src+'/*.html', conf.src+'/css/*', conf.src+'/js/*', conf.src+'/images/**/*']</span>
56.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">}</span>
57.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">},</span>
58.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">open: {</span>
59.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">server: {</span>
60.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">path: 'http://&lt;%= connect.options.hostname %&gt;:&lt;%= connect.options.port %&gt;'</span>
61.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">}</span>
62.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">}</span>
63.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">}); // grunt.initConfig配置完毕</span>
64.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">// 加载插件</span>
65.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">grunt.loadNpmTasks('grunt-contrib-connect');</span>
66.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">grunt.loadNpmTasks('grunt-contrib-watch');</span>
67.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">grunt.loadNpmTasks('grunt-open');</span>
68.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">// 自定义任务</span>
69.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">grunt.registerTask('server', ['connect:livereload', 'open:server', 'watch']);</span>
70.  <span style="font-family: 'trebuchet ms', geneva; font-size: medium;">};</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">看了下代码，有点感觉了没有?</span>

<span style="font-family: 'trebuchet ms', geneva; font-size: large;">我的目录是这样的：</span>
[![](http://jumorzhumin-wordpress.stor.sinaapp.com/uploads/2015/11/QQ截图20151127202257.png "QQ截图20151127202257")](http://jumorzhumin-wordpress.stor.sinaapp.com/uploads/2015/11/QQ截图20151127202257.png)**<span style="font-family: 'trebuchet ms', geneva; font-size: large;"> src目录可以忽略</span>**，本人比较懒，不想去删了。

<span style="font-family: 'trebuchet ms', geneva; font-size: large;">然后conf中的src是需要是grunt-contrib-watch需要监听文件改变的目录（以下所说的，和代码里的目录都是相对gruntfile.js的）。</span>

<span style="font-family: 'trebuchet ms', geneva; font-size: large;">然后你可以结合我的代码来理解插件得配置参数，多的东西都属于自己掌握的范畴，因为代码和注释都有了，碗都给你了，还要给你添饭吗，哈哈哈。</span>

<span style="font-family: 'trebuchet ms', geneva; font-size: large;">还是提示下，我们用到的connect-livereload是一个用来将静态脚本注入对应的长连接中的插件。watch的livereload配置服务端口，然后在配置外围的lrMiddleware函数用来将server和watch联系在一起的一个函数，非常重要，还可以设置代理，这里就不多提了。</span>

<span style="font-family: 'trebuchet ms', geneva; font-size: large;">这个办法可以大大提升你coding的效率。非常酷炫~~~~</span>

&nbsp;

* * *

&nbsp;

<span style="font-family: 'trebuchet ms', geneva; font-size: large;">若需深入交流，**[点击这里~~~](http://jumorzhumin.sinaapp.com/%E5%85%B3%E4%BA%8E%E6%88%91/)**</span>