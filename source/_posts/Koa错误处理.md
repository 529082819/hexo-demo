---
title: Nodejs-koa之如何优雅的进行错误捕捉和返回
---


在进行网站后端代码编写时总是会有一种情况发生，那就是错误获取和返回，谁都不能保证自己写的代码一定是正确的，或者在调用数据库获取数据、读取redis时总会有一些不可预料的情况发生，当发生这些情况时，总不能放任不管，否则程序就会挂掉了。

---

<!--more-->


### 代码

``` bash
const Koa = require('koa2');
const router = require('koa-router')();
const app = new Koa();
 
/**
 * 错误捕捉中间件
 */
app.use(async(ctx, next) => {
  try {
    ctx.error = (code, message) => {
      if (typeof code === 'string') {
        message = code;
        code = 500;
      }
      ctx.throw(code || 500, message || '服务器错误');
    };
    await next();
  } catch (e) {
    let status = e.status || 500;
    let message = e.message || '服务器错误';
    ctx.response.body = { status, message };
 
  }
});
app.use(require('koa-bodyparser')());
app.listen(3000);
console.log('start at port 3000...');
```

这段代码启动了koa，并有一个中间件来专门处理koa错误。在中间件里，我是给ctx对象添加了一个error方法，接收错误编号和错误内容，也可以不写错误编号，默认500，当执行ctx.error方法时，就会抛出一个异常，这样，在其他的路由或中间件里，代码执行到ctx.error时就会直接跳回到我的错误捕捉中间件，ctx.error后面的代码就不会再执行了。

使用方法也很简单。

``` bash
router.get('/',async(ctx)=>{
    if(!ctx.request.query.project){
        ctx.error('Project not found!');
        //ctx.error(12345,'Project not found!') 这样也可以，明确指出当错误编码为12345时代表project字段未找到
    }
    ....
    //逻辑处理
    ....
    ctx.response.body = res;
});
```

就是直接调用ctx.error方法。当调用这个方法后，if后面的逻辑处理就不会再执行了。


