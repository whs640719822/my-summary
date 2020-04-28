# koa

>概述：Koa 是一个新的 web 框架， 致力于成为 web 应用和 API 开发领域中的一个更小、更富有 表现力、更健壮的基石。
>
>特点： 
>
>	1. 轻量，无捆绑 
> 	2. 中间件架构 
> 	3. 优雅的API设计 
> 	4. 增强的错误处理
>
>安装： npm i koa -S
>
>

## koa基本使用

```js
const Koa = require('koa')
const app = new Koa()
app.use((ctx, next) => {
     ctx.body = [
         {
             name: 'tom'
         }
     ]
     next()
})
app.use((ctx, next) => {
     console.log('url' + ctx.url)
         if (ctx.url === '/html') {
         ctx.type = 'text/html;charset=utf-8'
         ctx.body = `<b>我的名字是:${ctx.body[0].name}</b>`
     }
})
app.listen(3000)
```

### 常见的中间件操作

1. 静态服务

   ```js
   app.use(require('koa-static')(__dirname + '/'))
   ```

2. 路由

   ```js
   const router = require('koa-router')()
   router.get('/string', async (ctx, next) => {
    ctx.body = 'koa2 string'
   })
   router.get('/json', async (ctx, next) => {
    ctx.body = {
    title: 'koa2 json'
    }
   })
   app.use(router.routes())
   ```

3. 日志

   ```js
   app.use(async (ctx,next) => {
    const start = new Date().getTime()
    console.log(`start: ${ctx.url}`);
    await next();
    const end = new Date().getTime()
    console.log(`请求${ctx.url}, 耗时${parseInt(end-start)}ms`)
   })
   ```

## koa原理

### 入门级

```js
一个基于nodejs的入门级http服务，类似下面代码：
const http = require('http')
const server = http.createServer((req, res)=>{
 res.writeHead(200)
 res.end('hi kaikeba')
})
server.listen(3000,()=>{
 console.log('监听端⼝3000')
})
```

### 模块化的回调

```js
// 创建kkb.js
const http = require("http");
class KKB {
     listen(...args) {
         const server = http.createServer((req, res) => {
         	this.callback(req, res);
         });
         server.listen(...args);
     }
     use(callback) {
     	this.callback = callback;
     }
}
module.exports = KKB;
// 调用，app.js
const KKB = require("./kkb");
const app = new KKB();
app.use((req, res) => {
     res.writeHead(200);
     res.end("hi kaikeba");
});
app.listen(3000, () => {
     console.log("监听端口3000");
});
```

### 上下文（context）



