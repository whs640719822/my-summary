# react入门

## 起步



1. 安装官⽅脚⼿架：**npm install -g create-react-app**

2. 创建项⽬：**create-react-app lesson1** 或者不执⾏步骤**1**直接执**`npx create-react-app lesson1** `

3. 打开lesson1： cd lesson1

4. 启动项⽬：npm start

5. 暴露配置项：npm run eject

## React和ReactDom

```js
import React from 'react';
import ReactDOM from 'react-dom';
// 这里怎么没有出现React字眼？
// JSX => React.createElement(...)
ReactDOM.render(<h1>Hello React</h1>,
document.querySelector('#root'));
```

>React负责逻辑控制，数据 -> VDOM
>
>ReactDom渲染实际DOM，VDOM -> DOM

# JSX

> JSX是一种JavaScript的语法扩展，其格式比较像模版语法，但事实上完全是在JavaScript内部实现的。
>
> JSX实质就是React.createElement的调用，最终的结果是React“元素”（JavaScript对象）。
>
> ```
> const jsx = <h2>react study</h2>;
> ReactDOM.render(jsx,
> document.getElementById('root'));
> ```

## 使用JSX

### 表达式**{}**的使用

```
const name = "react study";
const jsx = <h2>{name}</h2>;
```

### 函数的使用

```js
const user = { firstName: "tom", lastName:"jerry" };
function formatName(user) {return user.firstName + " " + user.lastName; }
const jsx = <h2>{formatName(user)}</h2>;
```

### jsx是js对象，也是合法表达式

```react
const greet = <p>hello, Jerry</p>
const jsx = <h2>{greet}</h2>;
```

### 条件语句的实现

```react
const showTitle = true;
const title = name ? <h2>{name}</h2> : null;
const jsx = (
 <div>
 {/* 条件语句 */}
 {title}
 </div>
);
```

### 数组的使用

```react
const arr = [1,2,3].map(num => <li key={num}> {num}</li>)
const jsx = (
     <div>
     	{/* 数组 */}
     	<ul>{arr}</ul> 
     </div>
);
```

### 属性的使用

```react
import logo from "./logo.svg";
const jsx = (
 <div>
 	{/* 属性：静态值⽤双引号，动态值⽤花括号；class、for等要特殊处理。 */}
 	<img src={logo} style={{ width: 100 }} className="img" />
 </div>
);
```

### CSS模块化使用

```react
{/* 创建index.module.css，index.js;index引用index.module.css */}
import style from "./index.module.css";
<img className={style.img} />
```

# 组件

> 组件是抽象的独⽴功能模块，react应⽤程序由组件构建构成。

## 组件的形式

> 组件有两种形式：**function**组件和**class**组件

### **class**组件

> class组件通常拥有状态和⽣命周期，继承于**Component**，实现render方法，

```react
{/* 创建pages/ClassComponent.js */}
import React, { Component } from "react";
import logo from "../logo.svg";
import style from "../index.module.css";
export default class ClassComponent extends Component {
 render() {
     const name = "react study";
     const user = { firstName: "tom", lastName:"jerry" };
     function formatName(user) {
     	return user.firstName + " " +user.lastName;
 	 }
     const greet = <p>hello, Jerry</p>;
     const arr = [1, 2, 3].map(num => <li key= {num}>{num}</li>);
     return (
         <div>
             {/* 条件语句 */}
             {name ? <h2>{name}</h2> : null}
             {/* 函数也是表达式 */}
             {formatName(user)}
             {/* jsx也是表达式 */}
             {greet} 
             {/* 数组 */}
             <ul>{arr}</ul>
             {/* 属性 */}
             <img src={logo} className={style.img} alt="" />
         </div>
     );
 }
}
{/* 创建指定src/App.js为根组件 */}
import React, { Component } from 'react';
import ClassComponent from  './pages/ClassComponent';
class App extends Component {
 render() {
     return (
             <div>
                 <ClassComponent/>
             </div>
         );
 	} 
}
export default App;

{/*创建index.js*/}
import App from "./App";
ReactDOM.render(<App />,document.getElementById("root"));
```

### **function**组件

> 函数组件通常⽆状态，仅关注内容展示，返回渲染结果即可。

```react
{/* 创建pages/ClassComponent.js */}
import React, { Component } from "react";
import logo from "../logo.svg";
import style from "../index.module.css";
export default class ClassComponent extends Component {
 render() {
     const name = "react study";
     const user = { firstName: "tom", lastName:"jerry" };
     function formatName(user) {
     	return user.firstName + " " +user.lastName;
 	 }
     const greet = <p>hello, Jerry</p>;
     const arr = [1, 2, 3].map(num => <li key= {num}>{num}</li>);
     return (
         <div>
             {/* 条件语句 */}
             {name ? <h2>{name}</h2> : null}
             {/* 函数也是表达式 */}
             {formatName(user)}
             {/* jsx也是表达式 */}
             {greet} 
             {/* 数组 */}
             <ul>{arr}</ul>
             {/* 属性 */}
             <img src={logo} className={style.img} alt="" />
         </div>
     );
 }
}
{/* 创建指定src/App.js为根组件 */}
import React from 'react';
import ClassComponent from  './pages/ClassComponent';
import React from "react";
import FuncCmp from "./pages/FuncCmp";
function App() {
 	return (
         <div className="App">
         	<ClassCmp />
         </div>
     );
}
export default App;

{/*创建index.js*/}
import App from "./App";
ReactDOM.render(<App />,document.getElementById("root"));
```

## 组件状态管理

> 如果组件中数据会变化，并影响⻚⾯内容，则组件需要拥有状态（state）并维护状态

### 类组件中的状态管理

> class组件使⽤state和setState维护状态

```react
{/*创建一个Clock.js类组件*/}
import React, { Component } from "react";
export default class ClassComponent extends
React.Component {
 constructor(props) {
     super(props);
     // 使⽤state属性维护状态，在构造函数中初始化状态
     this.state = { date: new Date() };
 }
 componentDidMount() {
     // 组件挂载之后启动定时器每秒更新状态
     this.timerID = setInterval(() => {
     // 使⽤setState⽅法更新状态
     	this.setState({
     		date: new Date()
         });
     }, 1000);
 }
 componentWillUnmount() {
     // 组件卸载前停⽌定时器
     clearInterval(this.timerID);
 }
 componentDidUpdate() {
 	console.log("componentDidUpdate");
 }
 render() {
 	return <div> {this.state.date.toLocaleTimeString()}</div>;
 }
}
```

### 函数组件中的状态管理

> 函数组件通过hooks api维护状态

```react
import React, { useState, useEffect } from"react";
export default function FuncCmp() {
 	 const [date, setDate] = useState(new Date());
     useEffect(() => {
         const timeId = setInterval(
             () => {
         		setDate(new Date()
         );	
     }, 1000);
        return () => clearInterval(timeId);
     });
 	 return <div>{date.toLocaleTimeString()}</div>; 
}
```

# 事件处理

> React中使⽤onXX写法来监听事件。

 * 用户输入事件，创建Search.js

   ```react
   import React, { Component } from "react";
   export default class Search extends Component {
    constructor(props) {
        super(props);
        this.state = { name: "" };
        // this.change = this.change.bind(this);
    }
    btn = () => {
        //使⽤箭头函数，不需要指定回调函数this，且便于传递参数
        console.log("btn");
    };
    change = e => {
        let value = e.target.value;
        this.setState({
        	name: value,
        });
        console.log("name", this.state.name);
    };
    render() {
        const { name } = this.state;
        return (
            <div>
                <button onClick={this.btn}>按钮</button>
                <input
                type="text"
                placeholder="请输入"
                name={name}
                onChange={this.change}
                />
            </div>
        );
        }
   }
   ```

   > 事件回调函数注意绑定this指向，常用三种方法：
   >
   > 1. 构造函数中绑定并覆盖：this.change =this.change.bind(this)
   >
   > 2. 方法定义为箭头函数：change = ()=>{}
   >
   > 3. 事件中定义为箭头函数：onChange={()=>this.change()}react里遵循单项数据流，没有双向绑定，输入框要设置value和onChange，称为受控组件

# 组件通信

## **props**属性传递

> Props属性传递可用于父子组件相互通信

```react
// index.js
ReactDOM.render(<App title="开课吧真不错" />,document.querySelector('#root'));
// App.js
<h2>{this.props.title}</h2>
```

## 函数通信

> 如果父组件传递的是函数，则可以把子组件信息传入父组件，这个常称为状态提升

```react
// StateMgt
<Clock change={this.onChange}/>
// Clock
this.timerID = setInterval(() => {
 this.setState({
 	date: new Date()
 }, ()=>{
     // 每次状态更新就通知⽗组件
     this.props.change(this.state.date);
 });
}, 1000);
```

## **context**通信

> React中使用Context实现祖代组件向后代组件跨层级传值。Vue中的 provide & inject来源于Context
>
> 在Context模式下有两个⻆色：
>
> 1.  Provider：外层提供数据的组件 
> 2. Consumer ：内层获取数据的组件

### 使用Context

> 创建Context => 获取Provider和Consumer => Provider提供值 => Consumer消费值

```react
//AppContext.js 
// 创建Context，provide，consumer
import React, { Component } from 'react'
export const Context = React.createContext()
export const Provider = Context.Provider
export const Consumer = Context.Consumerr
```

```react
//App.js
// 引入Provider并注入值
import React from 'react';
import Home from './pages/Home'
import User from './pages/User'
import { Provider } from './AppContext' //引入Context的Provider
const store = {
 home: {
 imgs: [{
 "src":"//m.360buyimg.com/mobilecms/s700x280_jfs/t1/49973/2/8672/125419/5d679259Ecd46f8e7		/0669f8801dff67e8.jpg!cr_1125x445_0_171!q70.jpg.dpg"
 	}
 	]
 },
 user: {
    isLogin: true,
 	userName: "true"
 }
}
function App() {
 return (
 <div className="app">
     <Provider value={store}>
     	<Home />
     </Provider>
 </div>
 );
}
export default App;
```

```react
//Home.js
// 引入Consumer 并消费值
import React, { Component } from 'react'
import { Consumer } from '../AppContext';
export default class Home extends Component {
     render() {
         return (
             <Consumer>
                 {
                    ctx => <HomeCmp {...ctx} />
                 }
             </Consumer>
     	)
 	}
}
function HomeCmp(props) {
 const { home, user } = props
 const { isLogin, userName } = user
 return (
     <div>
         {
            isLogin ? userName : '登录'
         }
     </div>
 )
}
```

```react
// User.js
// 引入Consumer 并消费值
import React, { Component } from 'react'
import { Consumer } from '../AppContext';
import TabBar from '../components/TabBar';
export default class User extends Component {
 render() {
     return (
         <>
             <Consumer>
                 {
                 	ctx => <UserCmp {...ctx} />
                 }
             </Consumer>
             <TabBar />
         </>
     )
 }
}
function UserCmp(props) {
 const { home, user } = props
 const { isLogin, userName } = user
 return (
     <div>
         {
         	isLogin ? userName : '登录'
         }
     </div>
 	)
}

//使用高阶组件
import React from 'react'
import { Consumer } from '../AppContext';
import Layout from './Layout';
const handleConsumer = Cmp => props => {
 return <Consumer>
         {
         	ctx => <Cmp {...ctx} {...props}></Cmp>
         }
     </Consumer>
}
export default function User(props) {
 const HandleConsumer = handleConsumer(UserCmp)
 return (
     <Layout title="用户中心">
          <HandleConsumer />
     </Layout>
 )
}
function UserCmp(props) {
 console.log('user', props)
 return <div>
 		User
 	</div>
}
```

```react
// TabBar.js
// 引入Consumer 并消费值
import React from 'react'
import { Consumer } from '../AppContext';
export default function TabBar() {
 return (
     <div>
         <Consumer>
             {
             	ctx => <TabBarCmp {...ctx} />
             }
         </Consumer>
     </div>
 )
}
function TabBarCmp(props) {
 const { home, user } = props
 const { isLogin, userName } = user
 return (
     <div>
         {
         	isLogin ? userName : '登录'
         }
     </div>
 	)
 }
```



## **redux**通信

> 类似vuex，无明显关系的组件间通信 ,后面全家桶部分详细介绍

# 生命周期

## React V16.3之前的生命周期

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200414113735868.png" alt="image-20200414113735868" style="zoom: 80%;" />

## React V16.4之后的生命周期

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200414114612381.png" alt="image-20200414114612381" style="zoom:150%;" />

## 验证生命周期

```react
import React, { Component } from "react";
/*
V17可能会废弃的三个⽣命周期函数⽤
getDerivedStateFromProps替代，⽬前使⽤的话加上
UNSAFE_：
- componentWillMount
- componentWillReceiveProps
- componentWillUpdate
 */
export default class LifeCycle extends Component
{
 constructor(props) {
     super(props);
     this.state = {
     	counter: 0,
 	 };
 	console.log("constructor",this.state.counter);
 }
 static getDerivedStateFromProps(props, state) {
     // getDerivedStateFromProps 会在调用 render 方法之前调用，
     //并且在初始挂载及后续更新时都会被调用。
     //它应返回一个对象来更新 state，如果返回 null 则不更新任何内容。
     const { counter } = state;
     console.log("getDerivedStateFromProps",counter);
     return counter < 8 ? null : { counter: 0 };
 }
 getSnapshotBeforeUpdate(prevProps, prevState) {
     const { counter } = prevState;
     console.log("getSnapshotBeforeUpdate",counter);
     return null;
 }
 /* 
     UNSAFE_componentWillMount() {
         //不推荐，将会被废弃
         console.log("componentWillMount",this.state.counter);
     } 
 */
 componentDidMount() {
 	console.log("componentDidMount",this.state.counter);
 }
 componentWillUnmount() {
     //组件卸载之前
     console.log("componentWillUnmount",this.state.counter);
 }
 /* 
     UNSAFE_componentWillUpdate() {
         //不推荐，将会被废弃
         console.log("componentWillUpdate",this.state.counter);
     } 
 */
 componentDidUpdate() {
 	console.log("componentDidUpdate",this.state.counter);
 }
 shouldComponentUpdate(nextProps, nextState) {
     const { counter } = this.state;
     console.log("shouldComponentUpdate", counter,nextState.counter);
     return counter !== 5;
 }
 setCounter = () => {
     this.setState({
     	counter: this.state.counter + 1,
     });
 };
 render() {
     const { counter } = this.state;
     console.log("render", this.state);
     return (
         <div>
         <h1>我是LifeCycle周期</h1>
         <p>{counter}</p>
         <button onClick={this.setCounter}>改变counter</button>
         {/* {!!(counter % 2) && <Foo />} */}
         <Foo counter={counter} />
         </div>
     );
 }
}
class Foo extends Component {
 UNSAFE_componentWillReceiveProps(nextProps) {
     //不推荐，将会被废弃
     // UNSAFE_componentWillReceiveProps() 会在已挂载的组件接收新的 props 之前被调用
     console.log("Foo componentWillReceiveProps");
 }
 componentWillUnmount() {
     //组件卸载之前
     console.log(" Foo componentWillUnmount");
 }
 render() {
     return (
         <div
         	style={{ border: "solid 1px black",margin: "10px", padding: "10px" }}
         >
             我是Foo组件
             <div>Foo counter: {this.props.counter}</div>
         </div>
     );
 }
}
```

# 高阶组件

> 为了提高组件复用率，可测试性，就要保证组件功能单一性；但是若要满足复杂需求就要扩展功能单一的组件，在React里就有了HOC（HigherOrder Components）的概念。
>
> 定义：是一个函数，它接收一个组件并返回另一个组件。

## 基本使用

```react
// HocPage.js
import React from 'react'
function Child(props) {
 	return <div>Child</div>
}
const foo = Cmp => props => {
 	return <Cmp {...props} />
}
/*const foo = (Cmp) => {
     return (props) => {
     return <Cmp {...props} />
 }
}*/
export default function HocPage(props) {
 const Foo = foo(Child)
 return (
     <div>
         HocPage
         <Foo />
     </div>
 )
}
```

## 链式调用

```react
import React from 'react'
function Child(props) {
 	return <div>Child</div>
}
const foo = Cmp => props => {
 return <div style={{ background: 'red' }}>
 	<Cmp {...props} />
 </div>
}
const foo2 = Cmp => props => {
 return <div style={{ border: 'solid 1px green' }}>
 	<Cmp {...props} />
 </div>
}
export default function HocPage() {
 const Foo = foo2(foo(Child))
 return (
 <div>
         HocPage
         <Foo />
 </div>
 )
}
```

## 装饰器调用

### 装饰器配置

> 1. npm run eject （如果是直接down下来的代码，并且有改动，一般需 要你先commit本地代码）
>
> 2. 配置package.json（如果不会配置， 直接看提供的package.json代 码）
>
>    > ```js
>    > "babel": {
>    >      "presets": [
>    >          "react-app"
>    >       ],
>    >       "plugins": [
>    >           	[
>    >           	"@babel/plugin-proposal-decorators",
>    >           	{
>    >           		"legacy": true
>    >       		}
>    >       	]
>    >   	 ]
>    >  }
>    > ```
>    >
>    > 
>
> 3.  安装装饰器插件 npm install @babel/plugin-proposal-decorators -- save-dev
>
> 注意：CRA项目中默认不支持js代码使用装饰器语法，可修改后缀名为tsx则可以 直接支持，cra版本高于2.1.0。

### 装饰器的使用

````react
// 装饰器只能用在class上
// 执行顺序从下往上
const foo = Cmp => props => {
 return (
     <div className="border">
     	<Cmp />
     </div>
 );
};
const foo2 = Cmp => props => {
 return (
     <div className="border" style={{ border: "green 1px solid" }}>
        <Cmp />
     </div>
 	);
};

@foo2
@foo
class Child extends Component {
 render() {
 	return <div className="border">Child</div>;
 }
}
export default class HocPage extends Component {
 render() {
 // const Foo = foo2(foo(Child));
 return (
     <div>
        <h1>HocPage</h1>
        {/* <Foo /> */}
        <Child />
     </div>
 	);
 }
}
````

# 组件复合

> 复合组件给与你足够的敏捷去定义自定义组件的外观和行为，这种方式更明确和安全。如果组件间有公用的非UI逻辑，将它们抽取为JS模块导入使 用而不是继承它。

## 基本使用

```react
//Layout.js
import React, { Component } from 'react'
export default class Layout extends Component {
 componentDidMount() {
     const { title = "商城" } = this.props
     document.title = title
 }
 render() {
     const { children, title = "商城" } = this.props
     return (
         <div style={{ background: 'yellow' }}>
         <p>{title}</p>
             {
             children.btns ? children.btns : children
             }
         	<TabBar />
         </div>
     )
 }
}
function TabBar(props) {
     return <div>
        TabBar
    </div>
}
```

```react
//Home.js
import React, { Component } from 'react'
import { Consumer } from '../AppContext';
import Layout from './Layout';
export default class Home extends Component {
 render() {
     return (
             <Consumer>
                 {
                 	ctx => <HomeCmp {...ctx} />
                 }
             </Consumer>
         )
     }
}
function HomeCmp(props) {
 const { home, user } = props
 const { carsouel = [] } = home
 const { isLogin, userName } = user
 return (
     <Layout title="首页">
         <div>
         <div>{isLogin ? userName : '未登录'}</div>
             {
                 carsouel.map((item, index) => {
                 	return <img key={'img' + index} src={item.img} />
                 })
             }
         </div>
     </Layout>
 )
}
```

## 具名插槽

> 传个对象进去就是具名插槽

```react
import React, { Component } from 'react'
import { Consumer } from '../AppContext';
import Layout from './Layout';
export default class User extends Component {
 render() {
 return (
     <div>
         <p>用户中心</p>
         <Consumer>
             {
                ctx => <UserCmp {...ctx} />
             }
         </Consumer>
     </div>
 )
 }
}
function UserCmp(props) {
 const { home, user } = props
 const { carsouel = [] } = home
 const { isLogin, userName } = user
 return (
     <Layout title="用户中心">
         {
             {
             	btns: <button>下载</button>
             }
         }
     {
     /* <div>
     		<div>⽤户名： {isLogin ? userName : '未登录'}</div>
     	</div> 
     */
     }
     </Layout>
 )
}
```

## 实现一个复合组件

```react
import React, { Component } from 'react'
function Card(props) {
 return <div xu="card">
         {
         	props.children
         }
 	</div>
}
function Formbutton(props) {
     return <div className="Formbutton">
         <button onClick=
        	{props.children.defaultBtns.searchClick}>默认查询
         </button>
         <button onClick={props.children.defaultBtns.resetClick}>默认重置
         </button>
         {
             props.children.btns.map((item, index) => {
                 return <button key={'btn' + index} onClick=
                	{item.onClick}>{item.title}</button>
             })
         }
     </div>
}
export default class CompositionPage extends Component {
 render() {
 return (
     <div>
         <Card>
         <p>我是内容</p>
         </Card>
         CompositionPage
         <Card>
         <p>我是内容2</p>
         </Card>
            <Formbutton>
                 {{
                 /* btns: (
                     <>
                         <button onClick={() => console.log('enn')}>查询</button>
                         <button onClick={() =>console.log('enn2')}>查询2</button>
                     </>
                 ) */
                 defaultBtns: {
                     searchClick: () => console.log('默认查询'),
                     resetClick: () => console.log('默认重置')
                 },
                 btns: [
                         {
                             title: '查询',
                             onClick: () => console.log('查询')
                         }, {
                             title: '重置',
                             onClick: () => console.log('重置')
                         }
                     ]
                 }}
     		</Formbutton>
     </div>
 	)
 }
}
```

# HOOKS

> Hook是React16.8一个新增项，它可以让你在不编写 class 的情况下使⽤ state 以及其他的 React 特性。
>
> Hooks的特点：
>
> 		* 使你在⽆需修改组件结构的情况下复⽤状态逻辑
> 		* 可将组件中相互关联的部分拆分成更⼩的函数，复杂组件将变得更容易 理解
> 		* 更简洁、更易理解的代码

##  State Hook

### 基本使用

```react
import React, { useState, useEffect } from "react";
export default function HookPage() {
 // const [date, setDate] = useState(new Date());
 const [counter, setCounter] = useState(0);
 return (
     <div>
         <h1>HookPage</h1>
         <p>{useClock().toLocaleTimeString()}</p>
         <p onClick={() => setCounter(counter + 1)}>{counter}</p>
     </div>
 );
}
function useClock() {//自定义hook，必须use开头
 	const [date, setDate] = useState(new Date());
     useEffect(() => {
         console.log("useEffect");
         const timer = setInterval(() => {
         	setDate(new Date());
         }, 1000);
        return () => clearInterval(timer);
     }, []);
 	return date;
}
```

### 创建多个状态变量

```react
import React, { useState, useEffect } from "react";
// import FruitAdd from "../components/FruitAdd";
import FruitList from "../components/FruitList";
export default function HooksPage() {
 const [counter, setCounter] = useState(0);
 const [fruits, setFruits] = useState(["apple","banana"]);
 return (
     <div>
         <h1>HooksPage</h1>
         <p>{useDate().toLocaleTimeString()}</p>
         <p onClick={() => setCounter(counter + 1)}>{counter}</p>
         {/* <FruitAdd
         fruits={fruits}
         addFruit={name => setFruits([...fruits, name])}
         /> */}
         <FruitList fruits={fruits} setFruits={setFruits}/>
     </div>
 );
}
```

```react
import React from "react";
export default function FruitList({ fruits, setFruits }){
 const delFruit = delIndex => {
     const tem = [...fruits];
     tem.splice(delIndex, 1);
     setFruits(tem);
 };
 return (
     <ul>
         {fruits.map((item, index) => (
             <li key={item} onClick={() => delFruit(index)}>
             	{item}
             </li>
         ))}
     </ul>
 );
}
```

### 用户输入员处理

```react
import React, { useState } from "react";
export default function FruitAdd({ fruits, addFruit }) {
 const [name, setName] = useState("");
 return (
 <div>
     <input value={name} onChange={event =>setName(event.target.value)} />
     <button onClick={() =>addFruit(name)}>add</button>
 </div>
 );
}
```

##  Effect Hook

> useEffect 给函数组件增加了执⾏副作⽤操作的能力。 副作用（Side Effect）是指一个 function 做了和本身运算返回值无关的 事，比如：修改了全局变量、修改了传⼊的参数、甚是是 console.log()， 所以 ajax 操作，修改 dom 都是算作副作用。 
>
> React 保证了每次运行effect 的同时，DOM 都已经更新完毕。

### 异步获取数据

```react
import { useEffect } from "react";
useEffect(()=>{
 setTimeout(() => {
 	setFruits(['香蕉','西瓜'])
 }, 1000);
})
```

> 测试会发现副作用操作会被频繁调用

### 设置依赖

```react
// 设置空数组意为没有依赖，则副作用操作仅执行一次
useEffect(()=>{...}, [])
```

```react
//如果副作用操作对某状态有依赖，务必添加依赖选项
import React, { useState, useEffect } from "react";
export default function FruitAdd({ fruits, addFruit}) {
 const [name, setName] = useState("");
 useEffect(() => {
     document.title = name;
 }, [name]);
 return (
     <div>
     <input value={name} onChange={event => setName(event.target.value)} />
     <button onClick={() =>addFruit(name)}>add</button>
 </div>
 );
}
```

### 清除副作用

> 有些副作用是需要清除的，清除工作非常重要的，可以防止引起内存泄露（比如定时器，监听函数）

```react
useEffect(() => {
     const timer = setInterval(() => {
     	console.log('msg');
     }, 1000);
     return function(){
        clearInterval(timer);
     }
 }, []);
//组件卸载后会执行返回的清理函数
```

## useContext Hook

> useContext用于在快速在函数组件中导出上下文。

```react
import React, { useContext } from "react";
import { Context } from "../AppContext";
export default function UseContextPage() {
 const ctx = useContext(Context);
 const { name } = ctx.user;
 return (
     <div>
         <h1>UseContextPage</h1>
         <p>{name}</p>
     </div>
 );
}
```

## useReducer Hook

> reducer 就是一个纯函数，接收旧的 state 和 action，返回新的 state。 useReducer是useState的可选项，常用于组件有复杂状态逻辑时，类似于 redux中reducer概念。

```react
import React, { useEffect, useReducer } from "react";
import FruitList from "../components/FruitList";
import FruitAdd from "../components/FruitAdd";
function fruitReducer(state = [], action) {
     switch (action.type) {
         case "replace":
         case "init":
         	return [...action.payload];
         case "add":
         	return [...state, action.payload];
         default:
         	return state;
 }
}
export default function UseReducerPage() {
 const [fruits, dispatch] = useReducer(fruitReducer,[]);
 useEffect(() => {
     setTimeout(() => {
         dispatch({ type: "init", payload: ["apple","banana"] });
     }, 1000);
     return () => {};
     }, 
 []);
 return (
 <div>
     <h1>UseReducerPage</h1>
     <FruitAdd
         fruits={fruits}
         addFruit={name => dispatch({ type: "add",payload: name })}/>
     <FruitList
         fruits={fruits}
         setFruits={newList => dispatch({ type: "init",payload: newList })}
     />
 </div>
 );
 }

```

# ant Design

## 直接使用

> 直接安装：npm install antd - -save

```react
import React, { Component } from 'react'
import Button from 'antd/es/button'
import "antd/dist/antd.css"
class App extends Component {
 render() {
     return (
         <div className="App">
             <Button type="primary">Button</Button>
         </div>
     )
 }
}
export default App
```

## 配置按需加载

> 安装react-app-rewired取代react-scripts，可以扩展 webpack的配置 ，类似vue.config.js。
>
> npm install react-app-rewired customize-cra babel-plugin-import -D

```js
//根目录创建config-overrides.js
const { override, fixBabelImports } =require("customize-cra");
module.exports = override(
     fixBabelImports("import", {//antd按需加载
     libraryName: "antd",
     libraryDirectory: "es",
     style: "css"
 })
);
//修改package.json
 "scripts": {
     "start": "react-app-rewired start",
     "build": "react-app-rewired build",
     "test": "react-app-rewired test",
     "eject": "react-app-rewired eject"
 },
```

> 支持装饰器配置

```js
npm install -D @babel/plugin-proposaldecorators

//配置完成后记得重启下
const { addDecoratorsLegacy } =require("customize-cra");
module.exports = override(
 ...,
 addDecoratorsLegacy()//配置装饰器
);
```

## 实现一个装饰器组件

```react
//按需加载和实现装饰器之后的⻚⾯如下：HocPage.js
import React, { Component } from "react";
import { Button } from "antd";
const foo = Cmp => props => {
 return (
     <div className="border">
     	<Cmp {...props} />
     </div>
 );
};
const foo2 = Cmp => props => {
 return (
     <div className="border" style={{border: "solid 1px red" }}>
            开课吧web全栈架构师
            <Cmp {...props} />
     </div>
 );
};
@foo
@foo2
class Child extends Component {
     render() {
        return <div className="border">child</div>;
     }
}
/* function Child(props) {
 return <div
className="border">child</div>;
} */
@foo2
class HocPage extends Component {
 render() {
 // const Foo = foo2(foo(Child));
 return (
     <div>
         <h1>HocPage</h1>
         	<Child />
         <Button type="dashed">click</Button>
     </div>
 );
 }
}
export default HocPage;
```

## antd表单试用

```react
import React, { Component } from "react";
import { Form, Input, Icon, Button } from "antd";
const FormItem = Form.Item;
//校验规则
const nameRules = { required: true,message: "please input your name" };
const passwordRules = { required: true,message: "please input your password" };

@Form.create()
class FormPageDecorators extends Component{
 handleSubmit = () => {
 /* const { getFieldsValue,
getFieldValue } = this.props.form;
 console.log("submit",
getFieldsValue()); */
 const { validateFields } =this.props.form;
     validateFields((err, values) => {
         if (err) {
            console.log("err", err);
         } else {
            console.log("submit", values);
         }
     });
 };
 render() {
 const { getFieldDecorator } =this.props.form;
 return (
     <div>
     <h1>FormPageDecorators</h1>
     <Form>
         <FormItem label="姓名">
             {getFieldDecorator("name", {rules: [nameRules] })(<Input prefix={<Icon type="user" />} />)}
         </FormItem>
     <FormItem label="密码">
         {getFieldDecorator("password",{ rules: [passwordRules] })(<Input type="password" prefix={<Icon type="lock" />} />,
         )}
     </FormItem>
     <FormItem>
     	<Button type="primary" onClick={this.handleSubmit}>提交</Button>
     </FormItem>
     </Form>
     </div>
 );
 }
}
export default FormPageDecorators
```

## 实现一个表单

### 表单基础结构

```react
import React, { Component } from "react";
import kFormCreate from "../../components/kFormCreate";
const nameRules = { required: true,message: "please input your name!" };
const passwordRules = {
 required: true,
 message: "please input your password!",
};
class MyFormPage extends Component {
 handleSubmit = () => {
     const { getFieldValue } =this.props;
     const res = {
         name: getFieldValue("name"),
         password:getFieldValue("password"),
     };
 	console.log("hah", res);
 };
 handleSubmit2 = () => {
 // 加入校验
 const { validateFields } =this.props;
     validateFields((err, values) => {
         if (err) {
            console.log("validateFields",err);
         } else {
            console.log("submit", values);
         }
     });
 };
 render() {
 const { getFieldDecorator } = this.props;
 return (
     <div>
         <h1>MyFormPage</h1>
         <div>
             {getFieldDecorator("name", {rules: [nameRules] })(<input type="text" />)}
             {getFieldDecorator("password",[passwordRules])(<input type="password" />)}
         </div>
         <button onClick={this.handleSubmit2}>submit</button>
     </div>
 );
 }
}
export default kFormCreate(MyFormPage);
```

### class组件实现kFromCreate

```react
//kFormCreate的实现
import React, { Component } from "react";
export default function kFormCreate(Cmp){
     return class extends Component {
            constructor(props) {
            super(props);
            this.options = {}; //各字段选项
            this.state = {}; //各字段值
         }
         handleChange = e => {
             let { name, value } = e.target;
             this.setState({ [name]: value });
         };
         getFieldValue = field => {
            return this.state[field];
         };
         validateFields = callback => {
             const res = { ...this.state };
             const err = [];
             for (let i in this.options) {
                 if (res[i] === undefined) {
                     err.push({ [i]: "error")};
                 }
             }
             if (err.length > 0) {
                callback(err, res);
             } else {
                callback(undefined, res);
             }
         };
         getFieldDecorator = (field, option)=> {
             this.options[field] = option;
             return InputCmp => (
                 <div>
                     {// 由React.createElement⽣成的元素不能修改，需要克隆⼀份再扩展
                         React.cloneElement(InputCmp, {
                         name: field,
                         value: this.state[field] ||"", //控件值
                         onChange: this.handleChange,
                        //控件change事件处理
                         })
                     }
                 </div>
             );
         };
         render() {
             return (
                 <div className="border">
                     <Cmp
                         {...this.props}
                         getFieldDecorator={this.getFieldDecorator}
                         getFieldValue={this.getFieldValue}
                         validateFields={this.validateFields}
                     />
                 </div>
             );
         }
     };
}
```

### 使用Hook实现kFormCreate

```react
import React, { useState } from "react";
const kFormCreate = Cmp => props => {
 const [state, setState] = useState({});
 const options = {};
 const handleChange = event => {
     setState({ ...state,[event.target.name]: event.target.value });
 };
 const getFieldDecorator = (field, option)=> {
 	 options[field] = option;
     return InpurtCmp => {
         return (
         <>
             {React.cloneElement(InpurtCmp, {
                 name: field,
                 value: state[field] || "",
                 onChange: handleChange,
             })}
         </>
         );
     };
 };
 const getFieldsValue = () => {
 	return { ...state };
 };
 const getFieldValue = field => {
 	return state[field];
 };
 const validateFields = callback => {
     const res = { ...state };
     const err = [];
     for (let item in options) {
         if (res[item] === undefined) {
            err.push({ [item]: "error" });
         }
     }
     if (err.length) {
     	callback(err, res);
     } else {
     	callback(undefined, res);
     }
 };
 return (
     <div className="border">
         <Cmp
             {...props}
             getFieldDecorator={getFieldDecorator}
             getFieldsValue={getFieldsValue}
             getFieldValue={getFieldValue}
             validateFields={validateFields}
         />
     </div>
 );
};
export default kFormCreate;
```

## 弹窗类组件的实现

> 弹窗类组件的要求弹窗内容在A处声明，却在B处展示。 react中相当于弹窗内容看起来被render到一个组件里面 去，实际改变的是网页上另一处的DOM结构，这个显然 不符合正常逻辑。但是通过使用框架提供的特定API创建 组件实例并指定挂载目标仍可完成任务。

```react
// 常见用法如下：Dialog在当前组件声明，但是却在body中另一个div中显示
<div class="foo">
 <div> ... </div>
 {
 	needDialog &&
     <Dialog>
         <header>Any Header</header>
         <section>Any content</section>
     </Dialog>
 }
</div>
```

### Dialog组件的实现

```react
// Diallog.js
//它做得事情是通过调用createPortal把要画的东西画在DOM树上另一个⻆落。
import React, { Component } from "react";
import { createPortal } from "react-dom";
import "./index.scss";
export default class Diallog extends Component {
 constructor(props) {
     super(props);
     const doc = window.document;
     this.node = doc.createElement("div");
     doc.body.appendChild(this.node);
 }
 componentWillUnmount() {
	window.document.body.removeChild(this.node)
 }
 render() {
 const { hideDialog } = this.props;
 return createPortal(
     <div className="dialog">
         {this.props.children}
         {typeof hideDialog === "function"&& (
         	<button onClick={hideDialog}>关掉弹窗</button>
         )}
     </div>,
     this.node,
 	);
 }
}
```

# redux

> redux里函数的基本概念：
>
> 1. createStore 创建store
> 2.   reducer 初始化、修改状态函数 
> 3.  getState 获取状态值 
> 4.  dispatch 提交更新 
> 5. subscribe 变更订阅

## redux的安装

> npm install redux --save



## 累加器了解redux

1. 需要一个store来存储数据
2. store里的reducer初始化state并定义state修改规则
3. 通过dispatch一个action来提交对数据的修改
4. action提交到reducer函数里，根据传入的action的type，返回 新的state

```react
import {createStore} from 'redux'
const counterReducer = (state = 0, action) => {
     switch (action.type) {
         case 'add':
         return state + 1
         case 'minus':
         return state - 1
         default:
         return state
 	}
 }
const store = createStore(counterReducer)
export default store
```

```react
import React, { Component } from "react";
import store from "../store/ReduxStore";
export default class ReduxPage extends Component {
 componentDidMount() {
     //store的订阅，当每次dispatch之后触发
     store.subscribe(() => {
         console.log("subscribe");
         this.forceUpdate();//强制更新页面
         //this.setState({});
     });
 }
 add = () => {
 	store.dispatch({ type: "add" });
 };
 minus = () => {
 	store.dispatch({ type: "minus" });
 };
 stayStatic = () => {
 	store.dispatch({ type: "others" });
 };
 render() {
     console.log("store", store);
     return (
         <div>
         <h1>ReduxPage</h1>
         <p>{store.getState()}</p>
         <button onClick={this.add}>add</button>
         <button onClick={this.minus}>minus</button>
         <button onClick={this.stayStatic}>static</button>
         </div>
     );
 }
}
```

> 还可以在index.js的render里全局订阅状态变更
>
> 注意：性能消耗大
>
> ```react
> import store from './store/ReduxStore'
> const render = ()=>{
>  ReactDom.render(
>  <App/>,
>  document.querySelector('#root')
>  )
> }
> render()
> store.subscribe(render)
> ```

## 异步中间件

> Redux只是个纯粹的状态管理器，默认只支持同步，实现异步任务 比如延迟，比如网络请求数据，需要中间件的支持，比如我们试用最简单的 redux-thunk和redux-logger

### 中间件的安装

> npm install redux-thunk redux-logger --save

### 中间件的使用

```react
import { createStore, applyMiddleware } from "redux";
import logger from "redux-logger";
import thunk from "redux-thunk";
import counterReducer from './counterReducer'
const store = createStore(counterReducer,applyMiddleware(logger, thunk));
const mapDispatchToProps = {
 add: () => {
 	return { type: "add" };
 },
 minus: () => {
 	return { type: "minus" };
 },
 asyAdd: () => dispatch => {
     setTimeout(() => {
         // 异步结束后，⼿动执⾏dispatch
         dispatch({ type: "add" });
     }, 1000);
     },
};
```

## redux的模块化

> redux提供了一个combineReducers的方法来支持redux模块化，有利于代码维护和开发

### 模块化的使用

```react
import { combineReducers } from "redux";
const store = createStore(
    combineReducers({counter: counterReducer}),
    applyMiddleware(logger, thunk)
);


import React, { Component } from "react";
import { connect } from "react-redux";
import { add, minus, asyAdd } from "../action/reactReduxPage";
class ReactReduxPage extends Component {
    render() {
        console.log("props", this.props);
        const { counter, add, minus, asyAdd } =this.props;
        const { num } = counter;
        return (
             <div>
                 <h1>ReactReduxPage</h1>
                 <p>{num}</p>
                 <button onClick={add}>add</button>
                 <button onClick={minus}>minus</button>
                 <button onClick={asyAdd}>asyAdd</button>
             </div>
         );
    }
}
const mapStateToProps = state => {
    return {
    	counter: state,
    };
};
const mapDispatchToProps = {
    add,
    minus,
    asyAdd,
};
export default connect(
    mapStateToProps, //状态映射 mapStateToProps
    mapDispatchToProps, //派发事件映射
)(ReactReduxPage);
```

## redux的基本实现原理

> 此处只是实现了最基本的几个核心模块：
>
> 	1. 储存状态state
>  	2. 获取状态getState
>  	3. 更新状态dispatch
>  	4. 变更订阅subscribe

```react
//reducer函数，enhancer一般传入的是middware
export function createStore(reducer, enhancer){
 if (enhancer) {
 	return enhancer(createStore)(reducer)
 }
 // 保存状态
 let currentState = undefined;
 // 回调函数
 let currentListeners = [];
 function getState(){
 	return currentState
 }
 function subscribe(listener){
 	currentListeners.push(listener)
 }
 function dispatch(action){
     currentState = reducer(currentState, action)
     currentListeners.forEach(v=>v())
     return action
 }
 //初始化随便dispatch一个reducer里面不存在的action.type值
 dispatch({type:'@IMOOC/KKB-REDUX'}) 
 return { getState, subscribe, dispatch}
}

//中间件middlweare的实现
export function applyMiddleware(...middlewares){
 // 返回强化以后函数
 return createStore => (...args) => {//args是reducer
     const store = createStore(...args)
     let dispatch = store.dispatch
     const midApi = {
         getState:store.getState,
         dispatch:(...args)=>dispatch(...args)
     }
     // 使中间件可以获取状态值、派发action
     const middlewareChain =middlewares.map(middleware => middleware(midApi))
     // compose可以middlewareChain函数数组合并成⼀个函数
     dispatch = compose(...middlewareChain)(store.dispatch)
     return {
         ...store,
         dispatch
     }
  }
}
function compose(...funcs) {
     if (funcs.length === 0) {
     	return arg => arg
     }
     if (funcs.length === 1) {
     	return funcs[0]
     }
    //此处最后返回的格式是（args）=>f1(f2(f3(args)))
     return funcs.reduce((a, b) => (...args) =>a(b(...args)))
}
function logger() {
     return dispatch => action => {
         // 中间件任务
         action.type && console.log(action.type + "执行了！");
         return dispatch(action);
 	};
}

//创建store
const counterReducer = (state = 0, action) => {
     switch (action.type) {
         case "add":
         return state + 1;
         case "minus":
         return state - 1;
         default:
         return state;
     }
};
const store = createStore(counterReducer,applyMiddleware(logger));
export default store;
```

## redux-thunk原理

```react
function thunk({ getState }) {
     return dispatch => action => {
         if (typeof action === "function") {
         	return action(dispatch, getState);
         } else {
         	return dispatch(action);
         }
     };
}
const store = createStore(counterReducer,applyMiddleware(logger,thunk));
```



# react-redux

> 由于redux每次都重新调用render和getState太low了，想用更react的方式来写，需要react-redux的支持
>
> 提供了两个api :
>
> 1.  Provider 为后代组件提供store 
> 2.  connect 为组件提供数据和变更方法

## react-redux的安装

> npm install react-redux --save

## react-redux的基本使用

```react
//注入store
import React from 'react'
import ReactDom from 'react-dom'
import App from './App'
import store from './store/ReactReduxStore'
import { Provider } from 'react-redux'
ReactDom.render(
 <Provider store={store}>
 <App/>
 </Provider>,
 document.querySelector('#root')
)
```

```react
import React, { Component } from "react";
import { connect } from "react-redux";
class ReactReduxPage extends Component {
     render() {
         const { num, add, minus, asyAdd } = this.props;
         return (
             <div>
                 <h1>ReactReduxPage</h1>
                 <p>{num}</p>
                 <button onClick={add}>add</button>
                 <button onClick={minus}>minus</button>
                 {/* <button onClick={asyAdd}>asyAdd</button>*/}
             </div>
         );
     }
}
const mapStateToProps = state => {
     return {
     	num: state,
     };
};
const mapDispatchToProps = {
 add: () => {
 	return { type: "add" };
 },
 minus: () => {
 	return { type: "minus" };
 }
};
export default connect(
 mapStateToProps, //状态映射 mapStateToProps
 mapDispatchToProps, //派发事件映射
)(ReactReduxPage);
```

## react-redux的实现原理

### class组件实现

> react-redux一共有两个核心任务：
>
> 1. 实现 个高阶函数工厂connect，可以根据传入状态映射规则函 数和派发器映射规则函数映射需要的属性，可以处理变更检测和 刷新任务
> 2. 实现一个Provider组件可以传递store

```react
import React from 'react'
import PropTypes from 'prop-types'
import {bindActionCreators} from './kkb-redux'
export const connect = (mapStateToProps =state=>state, mapDispatchToProps = {}) =>(WrapComponent) => {
 return class ConnectComponent extends React.Component{
     static contextTypes = {
        store: PropTypes.object
     }
     constructor(props, context){
         super(props, context)
         this.state = {
            props:{}
         }
     }
     componentDidMount(){
         const {store} = this.context
         store.subscribe(()=>this.update())
         this.update()
     }
     update(){
         const {store} = this.context
         // state => ({num: state.counter})
         const stateProps =mapStateToProps(store.getState())
         // {add:()=>({type:'add'})}
         // {add:(...args) =>dispatch(creator(...args))}
         const dispatchProps =bindActionCreators(mapDispatchToProps,store.dispatch)
         this.setState({
             props:{
                 ...this.state.props, // 之前的值
                 ...stateProps, // num: state.counter
                 ...dispatchProps // add:(...args) =>dispatch(creator(...args))
         	}
         })
     }
     render(){
        return <WrapComponent {...this.state.props}></WrapComponent>
     }
 }
}
export class Provider extends React.Component{
 static childContextTypes = {
 	store: PropTypes.object
 }
 getChildContext() {
 	return { store: this.store }
 }
 constructor(props, context) {
     super(props, context)
     this.store = props.store
 }
 render() {
 	return this.props.children
 }
}
//实现bindActionCreators
function bindActionCreator(creator, dispatch){
 return (...args) => dispatch(creator(...args))
}
function bindActionCreators(creators,dispatch){
 // {add:()=>({type:'add'})}
 // {add:(...args) => dispatch(creator(...args))}
 return Object.keys(creators).reduce((ret,item)=>{
 	ret[item] =bindActionCreator(creators[item],dispatch)
	return ret
 },{})
}
```

### hook实现

```react
import React, { useContext, useState, useEffect } from "react";
const Context = React.createContext();
function Provider({ store, children }) {
 return <Context.Provider value={store}>{children}</Context.Provider>;
}
const connect = (
 mapStateToProps = state => state,
 mapDispatchToProps = {},
) => Cmp => props => {
     const store = useContext(Context)
     const getMoreProps = () => {
     const stateProps =mapStateToProps(store.getState());
     const dispatchProps = bindActionCreators(
             mapDispatchToProps,
             store.dispatch,
         );
        return { ...stateProps, ...dispatchProps };
     };
     const [moreProps, setMoreProps] =useState(getMoreProps());
     useEffect(() => {
        store.subscribe(() => {
         	setMoreProps({ ...moreProps, ...getMoreProps();
        });
     });
     }, []);
     return <Cmp {...props} {...moreProps} />;
};
function bindActionCreator(creator, dispatch) {
 	return (...args) => dispatch(creator(...args));
}
function bindActionCreators(actionCreators,dispatch) {
     const boundActionCreators = {};
     for (const key in actionCreators) {
        boundActionCreators[key] =bindActionCreator(actionCreators[key], dispatch);
     }
     return boundActionCreators;
}
```

# react-router

> react-router包含3个库，react-router、react-router-dom和 react-router-native。react-router提供最基本的路由功能， 实际使用的时候我们不会直接安装react-router，而是根据应 用运用的环境选择安装react-router-dom（在浏览器中使 用）或react-router-native（在rn中使用）。react-routerdom和react-router-native都依赖react-router，所以在安装 时，react-router也会自动安装，创建web应用。

## 安装

> npm install --save react-router-dom

## 基本使用

> react-router中奉行一切皆组件的思想，路由器-Router、链 接-Link、路由-Route、独占-Switch、重定向-Redirect都 以组件形式存在

```react
import React, { Component } from "react";
import { BrowserRouter, Link, Route } from "react-router-dom";
import HomePage from "./HomePage";
import UserPage from "./UserPage";
export default class RouterPage extends Component {
 render() {
     return (
         <div>
             <h1>RouterPage</h1>
             <BrowserRouter>
                 <nav>
                     <Link to="/">首页</Link>
                     <Link to="/user">用户中心</Link>
                 </nav>
                     {/* 根路由要添加exact，实现精确匹配 */}
                     <Route exact path="/" component={HomePage} />
                     <Route path="/user" component={UserPage} />
             </BrowserRouter>
         </div>
     );
 }
}
```

## 动态路由

### 定义路由

```react
<Route path="/search/:id" component={Search} />
```

## 添加导航链接

```react
<Link to={"/search/" + searchId}>搜索</Link>
```

## 创建Search组件

```react
import React, { Component } from "react";
import { BrowserRouter, Link, Route } from "react-router-dom";
import HomePage from "./HomePage";
import UserPage from "./UserPage";
function Search({ match, history, location }) {
     const { id } = match.params;
     return (
         <div>
         	<h1>Search: {id}</h1>
         </div>
     );
}
export default class RouterPage extends Component {
 render() {
 const searchId = "1234";
 return (
     <div>
     <h1>RouterPage</h1>
     <BrowserRouter>
         <nav>
             <Link to="/">⾸⻚</Link>
             <Link to="/user">⽤户中⼼</Link>
             <Link to={"/search/" + searchId}>搜索</Link>
         </nav>
         {/* 根路由要添加exact，实现精确匹配 */}
         <Route exact path="/" component={HomePage} />
         <Route path="/user" component={UserPage} />
         <Route path="/search/:id" component={Search} />
     </BrowserRouter>
     </div>
 	);
 }
}
```

