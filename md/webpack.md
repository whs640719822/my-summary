# webpack简介

> Webpack是一个打包模块化JavaScript的工具，它会从入口模块出发，识别 出源码中的模块化导入语句，递归地找出入口件的所有依赖，将入口和其所 有的依赖打包到一个单独的文件中

# 安装webpack

## 环境准备

> NodeJs https://nodejs.org/en/

## 全局安装

```tex
# 安装webpack V4+版本时，需要额外安装webpack-cli
npm install webpack webpack-cli -g
# 检查版本
webpack -v
# 卸载
npm uninstall webpack webpack-cli -g
注意：全局安装webpack，这会将你项⽬中的webpack锁定到指定版本，造成不同的项⽬中因为webpack依赖不同版本⽽导致冲突，构建失败
```

## 项目安装 

```text
# 安装最新的稳定版本
npm i -D webpack
# 安装指定版本
npm i -D webpack@<version>
# 安装最新的体验版本 可能包含bug,不要⽤于⽣产环境
npm i -D webpack@beta
# 安装webpack V4+版本时，需要额外安装webpack-cli
npm i -D webpack-cli
```

## 检查安装

```
webpack -v //command not found 默认在全局环境中查找
npx webpack -v// npx帮助我们在项目中的node_modules⾥查找webpack
./node_modules/.bin/webpack -v//到当前的node_modules模块里指定webpack
```

# 启动webpack执行构建

## webpack默认配置

* webpack默认支持JS模块和JSON模块
* 支持CommonJS Es moudule AMD等模块类型
* webpack4支持零配置使用,但是很弱，稍微复杂些的场景都需要额外扩 展

## 准备执行构建

```js
// index.js
const json = require("./index.json");//commonJS
import { add } from "./other.js";//es module
console.log(json, add(2, 3));

// index.json
{
 "name": "JOSN"
}
// other.js
export function add(n1, n2) {
 	return n1 + n2;
}
```

## 执行构建

```
# npx方式
	npx webpack
修改package.json⽂件：
    "scripts": {
     	"test": "webpack"
    },
# npm script
	npm run test

```

## 构建成功

> 我们会发现目录下多出一个 dist 目录，里面有个 main.js ，这个文件是 一个可执行的JavaScript文件，里面包含webpackBootstrap启动函数。

## 默认配置

> 再根目录下新建一个webpack.config.js的文件，里面内容如下：
>
> ```js
> const path = require("path");
> module.exports = {
>      // 必填 webpack执⾏构建⼊⼝
>      entry: "./src/index.js",
>      output: {
>          // 将所有依赖的模块合并输出到main.js
>          filename: "main.js",
>          // 输出⽂件的存放路径，必须是绝对路径
>          path: path.resolve(__dirname, "./dist")
>      }
> };
> ```
>
> 