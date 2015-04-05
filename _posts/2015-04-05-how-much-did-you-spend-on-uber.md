---
layout: post
title: "你在 Uber 上花了多少钱？"
description: ""
category: "scripts"
tags: ['script']
---
{% include JB/setup %}

# 你在 Uber 上花了多少钱？

1. 打开 Uber.com 并登录，然后进入左边的 [我的行程](https://riders.uber.com/trips)，如图
![my trip](/images/uber/1.png)

2. 在页面随便一个地方右键，点击审查元素（Inspect Element）
![console](/images/uber/2.png)

3. 然后点击 Console
![uber](/images/uber/3.png)

4. 输入下面这段脚本，回车执行

        eval(function(p,a,c,k,e,d){e=function(c){return(c<a?'':e(parseInt(c/a)))+((c=c%a)>35?String.fromCharCode(c+29):c.toString(36))};if(!''.replace(/^/,String)){while(c--){d[e(c)]=k[c]||e(c)}k=[function(e){return d[e]}];e=function(){return'\\w+'};c=1};while(c--){if(k[c]){p=p.replace(new RegExp('\\b'+e(c)+'\\b','g'),k[c])}}return p}('2 4=0;2 i=1;2 6=5;g(6==5){b.8("加载第 "+i+" 页…");$.m({o:"u://p.q.t/s?n="+i,h:5}).e(9(a){2 7=$(a).j(\'l.k-r G.F--v\');c(7.E!=0){7.I(9(D){2 3=C.x;c(3.w("已取消")==-1){2 d=3.y("￥")[1];4+=z(d)}})}B{6=A}});i+=1};b.8("你在 H 上一共花了 "+f(4)+" 元");',45,45,'||var|priceText|totalAmount|false|end|arr|log|function|data|console|if|price|done|parseInt|while|async||find|trip|tr|ajax|page|url|riders|uber|expand__origin|trips|com|https|right|indexOf|innerText|split|parseFloat|true|else|this|index|length|text|td|Uber|each'.split('|'),0,{}))

5. 然后就可以看到惹！

