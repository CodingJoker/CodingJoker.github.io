---
title: canvas实现图片尺寸等比压缩并转换为base64字符串
id: 296
categories:
  - 前端小记
date: 2016-03-27 09:54:01
tags:
---

<Excerpt in index | 首页摘要>
+ Canvas妙用
+ Canvas.toDataURL

<!-- more -->
<The rest of contents | 余下全文>

	最近公司的一个H5活动，搜狐新闻客户端开机图制作，需要用户上传一张图片之后，先显示出来再进行裁剪，然后上传**base64字符串**到服务器上。

	但是问题来了，用户一般上传的图片文件的大小都在3-5M左右，转成base64后提交给服务器的话实在是太大了，到时上传到服务器的时候服务器超时了（都是泪啊！），所以这里需要先压缩下图片。

	偶然间想起来可以用canvas进行图片压缩，因为这个H5只是在客户端的webview中的，而webview又是webkit内核，所以对canvas的支持自然是比较好的，我们就毫无疑问的选择了用canvas进行压缩。

> 先上代码吧：

```
var width = img_this.width,height = img_this.height;
var **scale** = width / height;
width1 = 720;
height1 = parseInt(width1 / scale);
var canvas = $("#cans");
canvas[0].width = width1; canvas[0].height = height1;
var ctx = canvas[0].getContext('2d');
ctx.**drawImage**(img_this,0,0,width,height,0,0,width1,height1);
  var cropStr = canvas[0].**toDataURL**(**"image/jpeg",0.7**)
```
> 这里的img_this是一个img对象，首先我们先得到这个图片的宽高比，然后指定canvas的宽度为720，高度由之前的宽高比来定。这里指定了canvas的宽度为720后，将图片画在canvas里面后转成的base64的宽度也会为720。

	我们用drawImage方法将图片的**（0,0）坐标到(0 + width , 0+ height)坐标也就是整张图片** 画到 canvas**（0,0）到（width1，height1）也就是整个canvas内**。然后使用toDataUrl将图片转换成jpeg的格式，后面**0.7为图片的压缩质量，可以理解为压缩率**。**不要把图片压缩成png，因为压缩成png后base64的字符串可能比不转换前的长**！

> 就这么简单的几步就实现了对图片的压缩，咋样，方便吧~