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

```js
// request.js
module.exports = {
     get url() {
     	return this.req.url;
     }

     get method(){
     	return this.req.method.toLowerCase()
     }
};
// response.js
module.exports = {
     get body() {
     	return this._body;
     },
     set body(val) {
     	this._body = val;
     }
};
// context.js
module.exports = {
     get url() {
    	 return this.request.url;
     },
     get body() {
    	 return this.response.body;
     },
     set body(val) {
     	this.response.body = val;
     },
     get method() {
     	return this.request.method
     }
};

// kkb.js
// 导⼊这三个类
const context = require("./context");
const request = require("./request");
const response = require("./response");
class KKB {
     listen(...args) {
         const server = http.createServer((req, res) => {
             // 创建上下⽂
             let ctx = this.createContext(req, res);

             this.callback(ctx)
             // 响应
             res.end(ctx.body);
         });
     }
     // 构建上下⽂, 把res和req都挂载到ctx之上，并且在ctx.req和ctx.request.req同时保存
     createContext(req, res) {
         const ctx = Object.create(context);
         ctx.request = Object.create(request);
         ctx.response = Object.create(response);
         ctx.req = ctx.request.req = req;
         ctx.res = ctx.response.res = res;
         return ctx;
     }
}
```

### 中间件

> Koa中间件机制：Koa中间件机制就是函数组合的概念，将一组需要顺序执行的函数复合为一个函 数，外层函数的参数实际是内层函数的返回值。洋葱圈模型可以形象表示这种机制，是源码中的精 髓和难点。

#### koa中间件核心函数（compose）

```js
function compose(middlewares) {
     return function() {
         return dispatch(0);
         // 执行第0个
         function dispatch(i) {
             let fn = middlewares[i];
             if (!fn) {
             	return Promise.resolve();
         	}
             return Promise.resolve(
                 fn(function next() {
                     // promise完成后，再执行下一个
                     return dispatch(i + 1);
                 })
             );
         }
     };
}
```

#### compose在kkb中的使用

```js
const http = require("http");
const context = require("./context");
const request = require("./request");
const response = require("./response");
class KKB {
     // 初始化中间件数组
     constructor() {
     	this.middlewares = [];
     }
     listen(...args) {
         const server = http.createServer(async (req, res) => {
             const ctx = this.createContext(req, res);
             // 中间件合成
             const fn = this.compose(this.middlewares);
             // 执行合成函数并传入上下文
             await fn(ctx);
             res.end(ctx.body);
         });
         server.listen(...args);
     }
     use(middleware) {
         // 将中间件加到数组⾥
         this.middlewares.push(middleware);
     }
     // 合成函数
     compose(middlewares) {
         return function(ctx) { // 传入上下文
             return dispatch(0);
             function dispatch(i) {
                 let fn = middlewares[i];
                 if (!fn) {
                     return Promise.resolve();
                 }
                 return Promise.resolve(
                     fn(ctx, function next() {// 将上下⽂传⼊中间件，mid(ctx,next)
                         return dispatch(i + 1);
                     })
                 );
             }
         };
     }
     createContext(req, res) {
         let ctx = Object.create(context);
         ctx.request = Object.create(request);
         ctx.response = Object.create(response);
         ctx.req = ctx.request.req = req;
         ctx.res = ctx.response.res = res;
         return ctx;
     }
}
module.exports = KKB;



app.js使用
const Koa=require("./kkb")
const app=new Koa();
const delay = () => Promise.resolve(resolve => setTimeout(() => resolve(),2000));
app.use(async (ctx, next) => {
     ctx.body = "1";
     await next();
     ctx.body += "5";
});
app.use(async (ctx, next) => {
     ctx.body += "2";
     await delay();
     await next();
     ctx.body += "4";
});
app.use(async (ctx, next) => {
     ctx.body += "3";
});
//页面打印 12345
```

## koa-router原理

```js
class Router {
     constructor() {
     	this.stack = [];
     }

     register(path, methods, middleware) {
         let route = {path, methods, middleware}
         this.stack.push(route);
     }
     // 现在只支持get和post，其他的同理
     get(path,middleware){
     	this.register(path, 'get', middleware);
     }
     post(path,middleware){
     	this.register(path, 'post', middleware);
     }
     routes() {
         let stock = this.stack;
         return async function(ctx, next) {
             let currentPath = ctx.url;
             let route;
             for (let i = 0; i < stock.length; i++) {
                 let item = stock[i];
                 if (currentPath === item.path && item.methods.indexOf(ctx.method) >= 0) {
                     // 判断path和method
                     route = item.middleware;
                     break;
                 }
             }
             if (typeof route === 'function') {
                 route(ctx, next);
                 return;
             }
     		await next();
    	 };
     }
 }
 module.exports = Router;
```

## koa-static(koa静态文件服务)

```js
// static.js
const fs = require("fs");
const path = require("path");
module.exports = (dirPath = "./public") => {
     return async (ctx, next) => {
         if (ctx.url.indexOf("/public") === 0) {
             // public开头 读取文件
             const url = path.resolve(__dirname, dirPath);
             const fileBaseName = path.basename(url);
             const filepath = url + ctx.url.replace("/public", "");
             console.log(filepath);
             // console.log(ctx.url,url, filepath, fileBaseName)
         	try {
                 stats = fs.statSync(filepath);
                 if (stats.isDirectory()) {
                     const dir = fs.readdirSync(filepath);
                     // const
                    const ret = ['<div style="padding-left:20px">'];
                    dir.forEach(filename => {
                        console.log(filename);
                         // 简单认为不带小数点的格式，就是文件夹，实际应该用statSync
                         if (filename.indexOf(".") > -1) {
                             ret.push(
                             `<p><a style="color:black" href="${
                             ctx.url
                             }/${filename}">${filename}</a></p>`
                             );
                         } else {
                             // 文件
                             ret.push(
                             	`<p><a href="${ctx.url}/${filename}">${filename}</a></p>`
                             );
                         }
                     });
                     ret.push("</div>");
                     ctx.body = ret.join("");
                 } else {
                     console.log("文件");
                     const content = fs.readFileSync(filepath);
                     ctx.body = content;
                 }
             } catch (e) {
                 // 报错了 文件不存在
                 ctx.body = "404, not found";
             }
         } else {
             // 否则不是静态资源，直接去下一个中间件
             await next();
         }
     };
};
// 使用
const static = require('./static')
app.use(static(__dirname + '/public'))
```



