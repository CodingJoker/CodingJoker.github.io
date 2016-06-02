---
title: Angular双向数据绑定原理
date: 2016-06-01 13:44:44
categories:
  - angularJs
  - 前端小记
tags:
	AngularJS
---

<Excerpt in index | 首页摘要>
+ AngularJs 双向数据绑定机制
+ Source Code Analysis

<!-- more -->
<The rest of contents | 余下全文>

上一篇文章讲了一下MVC , MVP , MVVM的相互关系，今天来谈一谈一直让我有些模棱两可的双向数据绑定机制，也就是MVP模式过渡到MVVM的关键点。

> AngularJs的元素与模型双向绑定依赖于`循环检测`它们之间的值，这种做法叫做`脏检测`,首先看看如何将 Model 的变更更新到 UI

	Angular 的 Model 是一个 Scope 的类型，每个 Scope 都归属于一个 Directive 对象，比如 $rootScope 就归属于 ng-app。

	从 ng-app 往下，每个 Directive 创建的 Scope 都会一层一层链接下去，形成一个以 $rootScope 为根的链表,注意 Scope 还有同级的概念，形容更贴切我觉得应该是一棵树。

> 我们大概看一下 Scope 都有哪些成员：

```
  function Scope() {
      this.$id = nextUid();
      // 依次为： 阶段、父 Scope、Watch 函数集、下一个同级 Scope、上一个同级 Scope、首个子级 Scope、最后一个子级 Scope
      this.$$phase = this.$parent = this.$$watchers =
                     this.$$nextSibling = this.$$prevSibling =
                     this.$$childHead = this.$$childTail = null;
          // 重写 this 属性以便支持原型链
      this['this'] = this.$root =  this;
      this.$$destroyed = false;
      // 以当前 Scope 为上下文的异步求值队列，也就是一堆 Angular 表达式
      this.$$asyncQueue = [];
      this.$$postDigestQueue = [];
      this.$$listeners = {};
      this.$$listenerCount = {};
      this.$$isolateBindings = {};
}
```

> `Scope.$digest` 这是 Angular 提供的从 Model 更新到 UI 的接口

	你从哪个 Scope 调用，那它就会从这个Scope开始遍历，通知模型更改给各个 watch 函数，

> 来看看 $digest 的源码：

```
$digest: function() {
    var watch, value, last,
        watchers,
        asyncQueue = this.$$asyncQueue,
        postDigestQueue = this.$$postDigestQueue,
        length,
        dirty, ttl = TTL,
        next, current, target = this,
        watchLog = [],
        logIdx, logMsg, asyncTask;

    // 标识阶段，防止多次进入
    beginPhase('$digest');

    // 最后一个检测到脏值的 watch 函数
    lastDirtyWatch = null;

    // 开始脏检测,只要还有脏值或异步队列不为空就会一直循环
    do {
      dirty = false;
      // 当前遍历到的 Scope
      current = target;

      // 处理异步队列中所有任务, 这个队列由 scope.$evalAsync 方法输入
      while(asyncQueue.length) {
        try {
          asyncTask = asyncQueue.shift();
          asyncTask.scope.$eval(asyncTask.expression);
        } catch (e) {
          clearPhase();
          $exceptionHandler(e);
        }
        lastDirtyWatch = null;
      }

      traverseScopesLoop:
      do {
        // 取出当前 Scope 的所有 watch 函数
        if ((watchers = current.$$watchers)) {
          length = watchers.length;
          while (length--) {
            try {
              watch = watchers[length];

              if (watch) {
                // 1.取 watch 函数的运算新值,直接与 watch 函数最后一次值比较
                // 2.如果比较失败则尝试调用 watch 函数的 equal 函数，如果没有 equal 函数则直接比较新旧值是否都是 number 而且都是 NaN
                if ((value = watch.get(current)) !== (last = watch.last) &&
                    !(watch.eq
                        ? equals(value, last)
                        : (typeof value == 'number' && typeof last == 'number'
                           && isNaN(value) && isNaN(last)))) {
                  // 检测到值改变，设置一些标识
                  dirty = true;
                  lastDirtyWatch = watch;
                  watch.last = watch.eq ? copy(value, null) : value;
                  // 调用 watch 函数的变更通知函数, 也就是说各个 directive 从这里更新 UI
                  watch.fn(value, ((last === initWatchVal) ? value : last), current);

                  // 当 digest 调用次数大于 5 的时候（默认10），记录下来以便开发人员分析。
                  if (ttl < 5) {
                    logIdx = 4 - ttl;
                    if (!watchLog[logIdx]) watchLog[logIdx] = [];
                    logMsg = (isFunction(watch.exp))
                        ? 'fn: ' + (watch.exp.name || watch.exp.toString())
                        : watch.exp;
                    logMsg += '; newVal: ' + toJson(value) + '; oldVal: ' + toJson(last);
                    watchLog[logIdx].push(logMsg);
                  }
                } else if (watch === lastDirtyWatch) {
                  // If the most recently dirty watcher is now clean, short circuit since the remaining watchers
                  // have already been tested.
                  dirty = false;
                  break traverseScopesLoop;
                }
              }
            } catch (e) {
              clearPhase();
              $exceptionHandler(e);
            }
          }
        }

        // 恕我理解不能，下边这三句是卖萌吗
        // Insanity Warning: scope depth-first traversal
        // yes, this code is a bit crazy, but it works and we have tests to prove it!
        // this piece should be kept in sync with the traversal in $broadcast

        // 没有子级 Scope,也没有同级 Scope
        if (!(next = (current.$$childHead || (current !== target && current.$$nextSibling)))) {
          // 又判断一遍不知道为什么,不过这个时候 next === undefined 了，也就退出当前 Scope 的 watch 遍历了
          while(current !== target && !(next = current.$$nextSibling)) {
            current = current.$parent;
          }
        }
      } while ((current = next));


      // 当 TTL 用完，依旧有未处理的脏值和异步队列则抛出异常
      if((dirty || asyncQueue.length) && !(ttl--)) {
        clearPhase();
        throw $rootScopeMinErr('infdig',
            '{0} $digest() iterations reached. Aborting!\n' +
            'Watchers fired in the last 5 iterations: {1}',
            TTL, toJson(watchLog));
      }

    } while (dirty || asyncQueue.length);

    // 退出 digest 阶段，允许其他人调用
    clearPhase();

    while(postDigestQueue.length) {
      try {
        postDigestQueue.shift()();
      } catch (e) {
        $exceptionHandler(e);
      }
    }
  }
 ```
 虽然看起来很长，但是很容易理解，默认从 $rootScope 开始遍历，对每个 watch 函数求值比较，出现新值则调用通知函数，由通知函数更新 UI。我们再来看看 ng-model 是怎么注册通知函数的:

 ```
 $scope.$watch(function ngModelWatch() {
    var value = ngModelGet($scope);

    // 如果 ng-model 当前记录的 modelValue 不等于 Scope 的最新值
    if (ctrl.$modelValue !== value) {

      var formatters = ctrl.$formatters,
          idx = formatters.length;

      // 使用格式化器格式新值，比如 number,email 之类
      ctrl.$modelValue = value;
      while(idx--) {
        value = formatters[idx](value);
      }

      // 将新值更新到 UI
      if (ctrl.$viewValue !== value) {
        ctrl.$viewValue = value;
        ctrl.$render();
      }
    }

    return value;
});
```

> 说完了Model更新到UI，再来谈谈UI更新到Model

很简单，靠 Directive 编译时绑定的事件，比如 ng-model 绑定到一个输入框的时候事件代码如下：

```
var ngEventDirectives = {};
forEach(
  'click dblclick mousedown mouseup mouseover mouseout mousemove mouseenter mouseleave keydown keyup keypress submit focus blur copy cut paste'.split(' '),
    function(name) {
           var directiveName = directiveNormalize('ng-' + name);
           ngEventDirectives[directiveName] = ['$parse', function($parse) {
          return {
            compile: function($element, attr) {
              var fn = $parse(attr[directiveName]);
              return function(scope, element, attr) {
            // 触发以上指定的事件，就将元素的 scope 和 event 对象一起发送给 direcive
                element.on(lowercase(name), function(event) {
                  scope.$apply(function() {
                    fn(scope, {$event:event});
                  });
                });
              };
            }
         };
    }];
  }
);
```

Directive 接收到输入事件后根据需要再去 Update Model 就好啦。

	相信经过以上研究应该对 Angular 的绑定机制相当了解了吧，现在可别跟人家说起脏检测就觉得是一个 while(true) 一直在求值效率好低什么的，跟你平时用事件没啥两样，多了几次循环而已。

> 最后注意一点就是平时你通常不需要手动调用 `scope.$digest`，特别是当你的代码在一个 $digest 中被回调的时候，因为已经进入了 digest 阶段所以你再调用则会抛出异常。
我们只在没有 Scope 上下文的代码里边需要调用 digest，因为此时你对 UI 或 Model 的更改 Angular 并不知情。
