---
title: js关于静态快照（snapshot）的问题
id: 217
categories:
  - 大犀牛JavaScript
date: 2015-12-25 15:02:34
tags:
	JavaScript
---
<Excerpt in index | 首页摘要>
+ Snapshot
+ Javascript闭包

<!-- more -->
<The rest of contents | 余下全文>


> 有这么一个情景，JS代码 如下：

```
  var map = ['Mr' : 'Mr' , 'Ms' : 'Ms'];//可能有很多个
  var hello = [];
  function say(call , name){
      console.log(call + " : " +  name );
  } 
  //补全代码
  bababa~~~~
  //补全代码
  hello.Mr('JuMorZhu') // 控制台输出 Mr ： JuMorZhu
  hello.Ms('Fan BingBing') // 控制台 Ms : Fan BingBing
  一开始我想到的是这样：

for( var x  in map){

  hello[x] = function(name){

               say(map[x],name)

   }

}
```

	感觉挺简单的，自己还在得意中的时候控制台给我来了一波冷水。。。(+﹏+)~，输出的都是Ms ： Fan BingBing，我擦，你是有多喜欢范冰冰！！！

	原因是这样的，有没有听过这么一个名词： **静态快照，**是这样的，当你在执行这段代码的时候，Js首先进入预编译阶段，因为你创建函数的时候没有立即执行，所以JS在预编译的时候就会引用这个变量而不是这个变量的值，当你的函数执行的时候才会去找这个变量，在它找这个变量的时候他找不到，就会返回上一层作用域去找这个变量，而这时这个变量是map[x]，而x现在是map数组的最后一个索引，也就是Ms，所以此时无论你hello中有多少个元素，都只有一种输出结果，也就是在这种情况下创建的函数不支持静态快照。

> 所以你需要换个思路了~

	如果说函数执行的时候才去找变量，那么我们问什么不把它运行下呢，这时候我们用到了闭包。

	再来看看用闭包写的代码：

```
for（var x in map）{

  hello[x] = (function( call ){

    say(call,name)

})( map[x] )

}
```

> 你再试试，怎么样？感受到闭包的强大了吧~

&nbsp;