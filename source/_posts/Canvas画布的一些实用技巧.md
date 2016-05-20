---
title: Canvas画布的一些实用技巧
id: 133
categories:
  - 大犀牛JavaScript
date: 2015-12-01 18:46:03
tags:
---

<Excerpt in index | 首页摘要>
+ drawImage
+ Canvas绘图环境

<!-- more -->
<The rest of contents | 余下全文>


	Canvas 画布， 可能对于大多数人都有点陌生，不过，这个真得是一个酷炫的东西，你真的值得拥有，这是我这两天用它的时候产生的一种想法~



> 这篇文章主要介绍的在**Canvas中图片展示**。

	有没有想到一种场景，关于图片展示的，就像某宝的商品详情页，当鼠标在图片上移动的时候，在旁边的一个区域出现图片的部分区域放大图。

> 而实现这个的函数就是  drawImage（params_0 - params_8)

	他的参数最多有九个，最少有三个，具体用法自查，比较简单，介绍下9个参数时候具体的参数。

> dramImage（image,src_X,src_Y,src_width,src_height,des_X,des_Y,des_width,des_height）

*  image是一个image对象，使用new Image(),创建的对象，需要制定该对象的src，就是你要处理的图片。

*  src_X,src_Y 对应的是你image.src指向的图像上的坐标，图像的左上角坐标是（0,0），这两个参数就是**相对于原点**的坐标。

* src_width,src_height是你从你相对原点的位置取得一块区域。


	后面那四个参数你可以类比，哦，对了，des_X,des_Y是**相对Canvas原点**的位置。



> 然后你就可以开始YY了，其实超简单的，只是你没过度过来。^_^



监听鼠标的事件知道吧，用mousemove监听鼠标在对象上的对应位置（相对位置是算出来的，你可以去瞅瞅怎么算，挺简单的）

然后每次把画布clean了，重画，可以用clearRect这个方法来搞。



	大致就介绍到这，你要是有点编程能力或者头脑，应该差不多能搞定吧。



> 对了 drawImage和clearRect都是基于canvas的**绘图环境**的：eg: var canvas = document.getElementById("canvas");  var context = canvas.getContext("2d");//基于context的



> 还是附上代码吧：

```
 <!DOCTYPE html>
 <html>
 <head>
 <meta charset="UTF-8">
 <title>My Canvas</title>
 <style type="text/css" media="screen">
 .mycanvas{
 display: block;
 margin: 0 auto ;
  border:1px solid #aaa;
  cursor: pointer;
  }
  .box{
  position: absolute;
  top:600px;
  left: 50%;
  }
  </style>
  <script type="text/javascript">
  window.onload = function(){
  var canvas = document.getElementById('canvas');
  var context = canvas.getContext("2d");
  var image = new Image();
  image.src = "img.png";
  canvas.width = 500;
  canvas.height = 500;
  image.onload = function(){
  context.drawImage(image,0,0,canvas.width,canvas.height);
  // canvas.addEventListener('mouseover',getImageHandler,false);
  canvas.addEventListener('mousemove',getImageHandler,false);
  }
  }
  function getImageHandler(e){
  var x = e.clientX;
  var y = e.clientY;
  console.log("x:"+x+" y:"+y);
  var box = document.getElementById("box");
  var boxcontext = box.getContext("2d");
  boxcontext.width =200;
  boxcontext.height = 200;
  var image = new Image();
  image.src = "img.png";
  boxcontext.clearRect(0,0,0,0);
  boxcontext.drawImage(image,x-e.target.offsetLeft,y-e.target.offsetTop,100,100,0,0,200,200);
  }
  </script>
  </head>
  <body>
  <canvas id="canvas" class="mycanvas">
  </canvas>
  <canvas id="box" class="box">
  </canvas>
  </body>
  </html>
```