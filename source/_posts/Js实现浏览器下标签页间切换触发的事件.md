---
title: Js实现浏览器下标签页间切换触发的事件
id: 191
categories:
  - 大犀牛JavaScript
date: 2015-12-21 15:16:56
tags:
---

<pre><span style="font-family: 'trebuchet ms', geneva; font-size: large;">visibilitychange事件是浏览器新添加的一个事件，当浏览器的某个标签页切换到后台，或从后台切换到前台时就会触发该消息，现在主流的浏览器都支持该消息了，例如Chrome, Firefox, IE10等。</span></pre>
<pre><span style="font-family: 'trebuchet ms', geneva; font-size: medium;">var hiddenProperty = 'hidden' in document ? 'hidden' :    </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: medium;">    'webkitHidden' in document ? 'webkitHidden' :    </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: medium;">    'mozHidden' in document ? 'mozHidden' :    </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: medium;">    null;</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: medium;">var visibilityChangeEvent = hiddenProperty.replace(/hidden/i, 'visibilitychange');</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: medium;">var onVisibilityChange = function(){</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: medium;">    if (!document[hiddenProperty]) {    </span>
<span style="font-family: 'trebuchet ms', geneva; font-size: medium;">        console.log('页面非激活');</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: medium;">    }else{</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: medium;">        console.log('页面激活')</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: medium;">    }</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: medium;">}</span>
<span style="font-family: 'trebuchet ms', geneva; font-size: medium;">document.addEventListener(visibilityChangeEvent, onVisibilityChange);</span></pre>