---
title: Js内各种宽度，干货哟~
id: 187
categories:
  - 大犀牛JavaScript
date: 2015-12-19 11:59:49
tags:
---

<span style="font-family: 'trebuchet ms', geneva; font-size: large;">document.body.clientWidth ==&gt; BODY对象宽度</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">document.body.clientHeight ==&gt; BODY对象高度</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">document.documentElement.clientWidth ==&gt; 可见区域宽度</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">document.documentElement.clientHeight ==&gt; 可见区域高度</span>

<span style="font-family: 'trebuchet ms', geneva; font-size: large;">网页可见区域宽： document.body.clientWidth</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">网页可见区域高： document.body.clientHeight</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">网页可见区域宽： document.body.offsetWidth (包括边线的宽)</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">网页可见区域高： document.body.offsetHeight (包括边线的高)</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">网页正文全文宽： document.body.scrollWidth</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">网页正文全文高： document.body.scrollHeight</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">网页被卷去的高： document.body.scrollTop</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">网页被卷去的左： document.body.scrollLeft</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">网页正文部分上： window.screenTop</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">网页正文部分左： window.screenLeft</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">屏幕分辨率的高： window.screen.height</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">屏幕分辨率的宽： window.screen.width</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">屏幕可用工作区高度： window.screen.availHeight</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">屏幕可用工作区宽度： window.screen.availWidth</span>

<span style="font-family: 'trebuchet ms', geneva; font-size: large;">// 部分jQuery函数</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">$(window).height() 　//浏览器时下窗口可视区域高度 </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">$(document).height()　　　　//浏览器时下窗口文档的高度 </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">$(document.body).height()　　　　　　//浏览器时下窗口文档body的高度 </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">$(document.body).outerHeight(true)　//浏览器时下窗口文档body的总高度 包括border padding margin </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">$(window).width() 　//浏览器时下窗口可视区域宽度 </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">$(document).width()//浏览器时下窗口文档对于象宽度 </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">$(document.body).width()　　　　　　//浏览器时下窗口文档body的高度 </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">$(document.body).outerWidth(true)　//浏览器时下窗口文档body的总宽度 包括border padding</span>

<span style="font-family: 'trebuchet ms', geneva; font-size: large;">HTML精确定位:scrollLeft,scrollWidth,clientWidth,offsetWidth </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">scrollHeight: 获取对象的滚动高度。 </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">scrollLeft:设置或获取位于对象左边界和窗口中目前可见内容的最左端之间的距离 </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">scrollTop:设置或获取位于对象最顶端和窗口中可见内容的最顶端之间的距离 </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">scrollWidth:获取对象的滚动宽度 </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">offsetHeight:获取对象相对于版面或由父坐标 offsetParent 属性指定的父坐标的高度 </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">offsetLeft:获取对象相对于版面或由 offsetParent 属性指定的父坐标的计算左侧位置 </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">offsetTop:获取对象相对于版面或由 offsetTop 属性指定的父坐标的计算顶端位置 </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">event.clientX 相对文档的水平座标 </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">event.clientY 相对文档的垂直座标 </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">event.offsetX 相对容器的水平坐标 </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">event.offsetY 相对容器的垂直坐标 </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">document.documentElement.scrollTop 垂直方向滚动的值 </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: large;">event.clientX+document.documentElement.scrollTop 相对文档的水平座标+垂直方向滚动的量</span>

<span style="font-family: 'trebuchet ms', geneva; font-size: large;">物理像素和独立像素： 物理像素 =（screen.width） 独立像素 * window.devicePixelRatio</span>