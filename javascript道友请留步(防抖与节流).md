---
title: javascript道友请留步(防抖与节流)
tags: javascript道友请留步
date: 2019/09/12
categories: javascript
---

# javascript道友请留步(防抖与节流)


作为一个前端开发人员，你永远不知道你的用户会怎么使用你的产品，我们通常会做一些工作来防止用户疯狂的骚操作，比如防抖与节流，一个典型的例子就是用户疯狂点击你的按钮，那么快的频率，这怎么受得了，必须要有限制，还得有节奏，这才爽，这时防抖、节流就有用武之地了。

## 防抖 debounce

所谓防抖就是从某一个事件触发开始在接下来的固定时间内只执行一次应该调用的方法，如果在固定的时间内再次出发该时间，则重新计算开始时间。

准备一个简单的html文件，如下

```
<!DOCTYPE html>
<html>
    <head>
    </head>
    <body>
        <h1>test</h1>
        <button id="debounce">debounce</button>
    </body>
    <script>
        function debounce (fun, time){ //防抖
            let timeout = null;
            let deban = () => {
                console.log('click', Date.now())
                let ctx = this;
                let args = arguments;
                if(timeout !== null) clearTimeout(timeout);
                timeout = setTimeout( () => { fun.apply(ctx, args)}, time)
            }
            return deban
        }
        handle = () => {
            console.log('fun exe', Date.now())
        }
        document.getElementById('debounce').addEventListener('click', debounce(handle,2000));
    </script>
</html>
```

debounce函数实现了一个功能，传入一个函数和间隔时间，当点击事件触发后如果两秒内没有再次发生点击事件则触发需要执行的handle函数，如果两秒内再次发生点击则重新计时。效果如下

![debounce](https://github.com/ZGL520/MyImages/blob/master/debounce.png?raw=true)


## 节流 throttle

所谓节流就是当一个事件持续触发的时候，按照固定的平率触发需要执行的函数，话不多说，上代码

```
<!DOCTYPE html>
<html>
    <head>
    </head>
    <body>
        <h1>test</h1>
        <button id="throttle">throttle</button>
    </body>
    <script>
        function throttle (fun, time) { //节流
            let resetTime = 0;
            return function() {
                console.log('click')
                let now = Date.now();
                let ctx = this;
                let args = arguments;
                if( now - resetTime > time){
                    fun.apply(ctx, args);
                    resetTime = now;
                }
            }
        }
        handle = () => {
            console.log('fun exe', Date.now())
        }
        document.getElementById('throttle').addEventListener('click', throttle(handle, 1000))
    </script>
</html>
```

throttle函数实现了当传入一个函数和一个间隔时间数值时，如果点击时间联系发生，则会每间隔一秒执行一次handle函数，效果如下：

![throttle](https://github.com/ZGL520/MyImages/blob/master/throttle.png?raw=true)



以上就是简单实现了防抖和节流

参考文献：

https://juejin.im/post/5b651dc15188251aa30c8669#heading-1

https://mp.weixin.qq.com/s/Vkshf-nEDwo2ODUJhxgzVA
    
