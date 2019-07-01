---
title: 有趣的 JS 特性
date: 2019-07-01 09:26:05
categories:
- javascript
tags:
- 前端
- 计算机
- 技巧
---

## 利用 a 标签解析 URL

有的时候我们需要从一个 URL 中提取域名，查询关键字，变量参数值等，一般我们会自己去解析 URL 来获取这些内容。可是你或许不知道还有更简单的方法。

即创建一个 a 标签将需要解析的 URL 赋值给 a 的 href 属性，然后我们就能很方便的拿到这些内容。代码如下

```JavaScript
function parseURL(url) {
    var a =  document.createElement('a');
    a.href = url;
    return {
        host: a.hostname,
        port: a.port,
        query: a.search,
        params: (function(){
            var ret = {},
                seg = a.search.replace(/^\?/,'').split('&'),
                len = seg.length, i = 0, s;
            for (;i<len;i++) {
                if (!seg[i]) { continue; }
                s = seg[i].split('=');
                ret[s[0]] = s[1];
            }
            return ret;
        })(),
        hash: a.hash.replace('#','')
    };
}

```

## void 运算符

> void 运算符对给定的表达式进行求值，然后返回 undefined。

由于 **void** 会忽略操作数的值，因此在操作数具有副作用的时候使用 **void** 会更加合理。

### 使用 void 替换 undefined

由于 **undefined** 并不是 **JavaScript** 的关键字，所以我们在赋值某个变量为 **undefined** 时可能会有点意想不到的结果。

```JavaScript
function t(){
    var undefined = 10;
    console.log(undefined);
}
console.log(t()); // 所有浏览器下都是10
```

如上代码我们可能希望赋值为 **undefined**，但却得到了 **10** 这个莫名其妙的情况。所以我们可以使用使用 **void** 替换 **undefined**

> 这也是为什么我们在很多源码中都能看到使用 **void** 替换 **undefined**。

## IntersectionObserver 是什么？

>`IntersectionObserver` 可以用来监听元素是否进入了设备的可视区域之内，而不需要频繁的计算来做这个判断。

所以我们可以用这个特性来处理曝光埋点，而不是用 **getBoundingClientRect().top** 这种更加损耗性能的方式来处理。

当然你也可以用这个 API 来优化滚动吸顶,代码如下：

```JavaScript
IntersectionObserverFun: function() {
    let self = this;
    let ele = self.$refs.pride_tab_fixed;
    if( !IntersectionObserver ){
        let observer = new IntersectionObserver(function(){
            let offsetTop = ele.getBoundingClientRect().top;
            self.titleFixed = offsetTop < 0;
        }, {
            threshold: [1]
        });
        observer.observe(document.getElementsByClassName('title_box')[0]);
    } else {
        window.addEventListener('scroll', _.throttle(function(){
            let offsetTop = ele.getBoundingClientRect().top;
            self.titleFixed = offsetTop < 0;
        }, 50));
    }
}

```
