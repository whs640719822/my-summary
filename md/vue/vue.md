# vue组件化实战

## 组件化

### 组件通信

#### 父组件=>子组件

* 属性props通信

  ```vue
  // child
  props: { msg: String }
  // parent
  <HelloWorld msg="Welcome to Your Vue.js App"/>
  ```

* 特性$attrs通信

  ```vue
  // child：并未在props中声明foo
  <p>{{$attrs.foo}}</p>
  // parent
  <HelloWorld foo="foo"/>
  ```

* 引用refs通信

  ```vue
  // parent
  <HelloWorld ref="hw"/>
  mounted() {
  	this.$refs.hw.xx = 'xxx'
  }
  ```

* 子元素$children通信

  ```vue
  // parent
  this.$children[0].xx = 'xxx'
  ```

#### 子组件=>父组件通信

```vue
自定义事件
// child
this.$emit('add', good)
// parent
<Cart @add="cartAdd($event)"></Cart>

```

#### 兄弟组件：通过共同祖辈组件

```vue
// brother1
this.$parent.$on('foo', handle)
// brother2
this.$parent.$emit('foo')
```

#### 祖先和后代通信

> 由于嵌套层数过多，传递props不切实际，vue提供了 provide/inject API完成该任务

```vue
// ancestor
provide() {
	return {foo: 'foo'}
}
// descendant
inject: ['foo']

```

#### 任意两个组件之间：事件总线 或 vuex

```js
// Bus：事件派发、监听和回调管理
class Bus{
	constructor(){
		this.callbacks = {}	
	}		
	$on(name, fn){
		this.callbacks[name] = this.callbacks[name] || []
		this.callbacks[name].push(fn)
	}
	$emit(name, args){
		if(this.callbacks[name]){
			this.callbacks[name].forEach(cb => cb(args))
		}
	}
}
// main.js
Vue.prototype.$bus = new Bus()
// child1
this.$bus.$on('foo', handle)
// child2
this.$bus.$emit('foo')
```

### 插槽

#### 匿名插槽

```vue
// comp1
<div>
	<slot></slot>
</div>
// parent
<comp>hello</comp>
```

#### 具名插槽

```vue
// comp2
<div>
    <slot></slot>
    <slot name="content"></slot	>
</div>
// parent
<Comp2>
    <!-- 默认插槽用default做参数 -->
    <template v-slot:default>具名插槽</template>
    <!-- 具名插槽用插槽名做参数 -->
    <template v-slot:content>内容...</template>
</Comp2>
```

#### 作用域插槽

> 分发内容要用到子组件中的数据

```vue
// comp3
<div>
    <slot :foo="foo"></slot>
</div>
// parent
<Comp3>
    <!-- 把v-slot的值指定为作用域上下文对象 -->
    <template v-slot:default="slotProps">
    	来自子组件数据：{{slotProps.foo}}
    </template>	
</Comp3>
```

## 组件化实战

### 实现一个form表单

> 需要安装一个验证的插件 async-validator： npm i async-validator -S

```vue
KInput.vue
<template>
	<div>
		<input :value="value" @input="onInput" v-bind="$attrs">
	</div>
</template>
<script>
export default {
    inheritAttrs: false,
        props: {
            value: {
            type: String,
            default: ''
        },
    },
	methods: {
		onInput(e) {
			this.$emit('input', e.target.value);
            this.$parent.$emit('validate');
		}
	},
}
</script>

实现KFormItem
<template>
	<div>
		<label v-if="label">{{label}}</label>
		<slot></slot>
		<p v-if="errorMessage">{{errorMessage}}</p>
	</div>
</template>
<script>
export default {
    props: {
        label: {// 输入项标签
        	type: String,
        	default: ''
   	 	},
		prop: {// 字段名
    		type: String,
    		default: ''
		},
	},
	data() {
		return {
			errorMessage: '' // 校验错误
		}
	},
    mounted(){
      this.on("validate",()=>{this.validate()})  
    },
    methods:{
        validate(){
            // 获取对应FormItem校验规则
			const rules = this.form.rules[this.prop];
            // 获取校验值
            const value = this.form.model[this.prop];
            // 校验描述对象
            const descriptor = { [this.prop]: rules };
            // 创建校验器
            const schema = new Schema(descriptor);
            // 返回Promise，没有触发catch就说明验证通过
            return schema.validate({ [this.prop]: value }, errors => {
                if (errors) {
                    // 将错误信息显示
                    this.error = errors[0].message;
                } else {
                    // 校验通过
                    this.error = "";
                }
            });
        }         
    }
};
</script>

实现KForm
<template>
    <form>
    	<slot></slot>
    </form>
</template>
<script>
export default {
    provide() {
        return {
            form: this // 将组件实例作为提供者，子代组件可方便获取
        };
    },	
    props: {
        model: { type: Object, required: true },
        rules: { type: Object }
    }，
    methods:{
    	validate(cb) {
                // 调用所有含有prop属性的子组件的validate方法并得到Promise数组
                const tasks = this.$children.filter(item => item.prop).map(item => item.validate());
			   // 所有任务必须全部成功才算校验通过，任一失败则校验失败
                Promise.all(tasks).then(() => cb(true)).catch(() => cb(false))
         }
	}
};
</script>


使用KForm
<template>
	<div>
		<h3>KForm表单</h3>
		<hr>
		<k-form :model="model" :rules="rules" ref="loginForm">
                <k-form-item label="用户名" prop="username">
                    <k-input v-model="model.username"></k-input>
                </k-form-item>
                <k-form-item label="确认密码" prop="password">
                    <k-input type="password" v-model="model.password"></k-input>
                </k-form-item>
		</k-form>
	</div>
</template>
<script>
import KForm from "./KForm";
import kFormItem from "./kFormItem";
import kInput from "./kInput";
export default {
    components: {
        KForm,
    },
	data() {
		return {
    		rules: {
        		username: [{ required: true, message: "请输入用户名" }],
        		password: [{ required: true, message: "请输入密码" }]
    		},
           	 model: { username: "tom", password: "" },
		};	
	},
	methods: {
		submitForm() {
			this.$refs['loginForm'].validate(valid => {
                if (valid) {
                    alert("请求登录!");
                } else {
                    alert("校验失败！");
                }
			});
		}
	}
};
</script>
```

### 实现弹框组件

```js
create函数
import Vue from "vue";
// 创建函数接收要创建组件定义
function create(Component, props) {
    // 创建一个Vue新实例
    const vm = new Vue({
        render(h) {
            // render函数将传入组件配置对象转换为虚拟dom
            console.log(h(Component, { props }));
            return h(Component, { props });
        }
    }).$mount(); //执行挂载函数，但未指定挂载目标，表示只执行初始化工作
    // 将生成dom元素追加至body
    document.body.appendChild(vm.$el);
    // 给组件实例添加销毁方法
    const comp = vm.$children[0];
    comp.remove = () => {
        document.body.removeChild(vm.$el);
        vm.$destroy();
    };
    return comp;
}
// 暴露调用接口
export default create;
```

```vue
Notice弹框模板
<template>
    <div class="box" v-if="isShow">
        <h3>{{title}}</h3>
        <p class="box-content">{{message}}</p>
    </div>
</template>
<script>
export default {
    props: {
        title: {
            type: String,
            default: ""
        },
        message: {
            type: String,
            default: ""
        },
        duration: {
            type: Number,
            default: 1000
         }
    },
	data() {
        return {
            isShow: false
        };
	},
    methods: {
        show() {
            this.isShow = true;
            setTimeout(this.hide, this.duration);
        },
        hide() {
            this.isShow = false;
            this.remove();
        }
    }
};
</script>

```

```vue
使用create api
<script>
import create from "@/utils/create";
import Notice from "@/components/Notice";
export default {
	methods: {
        submitForm(form) {
            this.$refs[form].validate(valid => {
                const notice = create(Notice, {
                    title: "社会你杨哥喊你来搬砖",
                    message: valid ? "请求登录!" : "校验失败!",
                    duration: 1000
                });
                notice.show();
            });
        }
	}
};
</script>

```

# vue全家桶

## vue-router

### vue-router的安装

> vue add router

### vue-router的使用

#### 简单使用流程

> 配置，router.js
>
> > ```js
> > { path: '/', name: 'home', component: Home }
> > ```
>
> 路由出口
>
> > ```vue
> > <router-view/>
> > ```
>
> 导航链接
>
> > ```vue
> > <router-link to="/">Home</router-link>
> > <router-link to="/about">About</router-link>
> > ```
>
> 添加到vue选项
>
> > ```js
> > new Vue({ 
> > 	router, // 为什么挂载 
> > 	render: h => h(App) 
> > }).$mount('#app')
> > ```

#### 动态路由

​		把某种模式匹配到的所有路由，全都映射到同一个组件。

		1. 创建显示组件

```vue
<template>
    <div>
        <h2>商品详情</h2>
        <p>{{$route.params.id}}</p>
    </div>
</template>
```

2. route.js文件配置

```json
{
    path: '/detail/:id',
    name: 'detail',
    component: Detail
}
```

3. 跳转页面

```vue
<div v-for="item in items" :key="item.id">
    <router-link :to="`/detail/${item.id}`">
    	{{ item.name }}
    </router-link>
</div>
```

### vue-router简单源码实现

```js
class VueRouter {
	constructor(options) {
        this.$options = options;
        // path、component映射
        this.routeMap = {};
        // current保存当前hash
        // vue使其是响应式的
        this.app = new Vue({
            data: {
                current: "/"
            }
        });
	}
    init() {
        this.bindEvents();
        this.createRouteMap();
        this.initComponent();
    }	
	// hash变更检测
    bindEvents() {
        window.addEventListener("load", this.onHashChange.bind(this), false);
        window.addEventListener("hashchange", this.onHashChange.bind(this), false);
    }
	// 路径变更处理
    onHashChange() {
    	this.app.current = window.location.hash.slice(1) || "/";
    }
	// 创建路由映射表
    createRouteMap() {
        this.$options.routes.forEach(item => {
            this.routeMap[item.path] = item;
        });
    }
	// router-link/router-view声明	
    initComponent() {
        Vue.component("router-link", {
            props: {
                to: String
            },
            render(h) {
                return <a href={this.to}>{this.$slots.default}</a>;
            }
        });
        Vue.component("router-view", {
            render: h => {
                var component = this.routeMap[this.app.current].component;
                return h(component);
            }
        });
    }
};
let Vue;
// 插件逻辑
VueRouter.install = function(_Vue) {
    Vue = _Vue;
    Vue.mixin({
        beforeCreate() {
            if (this.$options.router) {
                // 确保是根组件时执行一次，将router实例放到Vue原型，以后所有组件实例就均有$router
                Vue.prototype.$router = this.$options.router;
                this.$options.router.init();
            }
        }
    });
}
```

## Vuex

> Vuex 是一个专为 Vue.js 应用开发的状态管理模式，集中式存储管理应用所有组件的状态。 Vuex遵循“单向数据流”理念，易于问题追踪以及提高代码可维护性。

### vue的安装

> vue add vuex

### vue的使用

 	1. 状态和状态变更

```vue
export default new Vuex.Store({
    state: { count:0 },
    mutations: {
        increment(state) {
       	 	state.count += 1;
    	}
    }
})

<template>
    <div>
        <div>冲啊，手榴弹扔了{{$store.state.count}}个</div>
        <button @click="add">扔一个</button>
    </div>
</template>
<script>
export default {
    methods: {
        add() {
        	this.$store.commit("increment");
        }
    }
};
</script>

```

2. 派生状态 - getters

```vue
export default new Vuex.Store({
	getters: {
        left(state) { // 计算剩余数量
        	return 10 - state.count;
		}	
	}	
})	
<span>还剩{{$store.getters.left}}个</span>
```

3. 动作 - actions

   ```vue
   export default new Vuex.Store({
       actions: {
           increment({ getters, commit }) {
               // 添加业务逻辑
               if (getters.left > 0) {
                   commit("increment");
                   return true;// 返回结果
               }
               return false;// 返回结果
           },
           asyncIncrement({ dispatch }) {
               // 异步逻辑返回Promise
               return new Promise(resolve => {
                   setTimeout(() => {
                       // 复用其他action
                       resolve(dispatch("increment"));
                   }, 1000);
               });
           },
   	}
   })
   
   //使用action
   <template>
       <div id="app">
       	<button @click="asyncAdd">蓄力扔一个</button>
       </div>
   </template>
   <script>
   export default {
       methods: {
           add() {
               // 即使action执行同步代码返回的结果依然是promise
               this.$store.dispatch("increment").then(result => {
                   if (!result) {
                       alert("投掷失败！没货啦");
                   }
           	});
           	// this.$store.commit("increment");
           },
           asyncAdd() {	
               this.$store.dispatch("asyncIncrement").then(result => {
               if (!result) {
               	alert("投掷失败！没货啦");
               }
           });
       }
   };
   </script>
   ```

4. 模块化

```vue
const count = {
    namespaced: true,
    // ...
};
export default new Vuex.Store({
	modules: {a: count}
});
<template>
    <div id="app">
        <div>冲啊，手榴弹扔了{{$store.state.a.count}}个</div>
        <p>{{$store.getters['a/score']}}</p>
        <button @click="add">扔一个</button>
        <button @click="addAsync">蓄力扔俩</button>
    </div>
</template>
<script>
export default {
    methods: {
        add() {
        	this.$store.commit("a/increment");
        },
        addAsync() {
        	this.$store.dispatch("a/incrementAsync");
        }
    }
};
</script>
```

### vuex的简单原理解析

```js
let Vue;
function install(_Vue) {
    Vue = _Vue;
    // 这样store执行的时候，就有了Vue，不用import
    // 这也是为啥Vue.use必须在新建store之前
    Vue.mixin({	
        beforeCreate() {
            // 这样才能获取到传递进来的store
            // 只有root元素才有store，所以判断一下
            if (this.$options.store) {
                Vue.prototype.$store = this.$options.store;
            }
        }
    });
}
class Store {
    constructor(options = {}) {
        this.state = new Vue({
        	data: options.state
    	});
    	this.mutations = options.mutations || {};
        this.actions = options.actions;
    }
    // 注意这里用箭头函数形式，后面actions实现时会有作用
    commit = (type, arg) => {
    	this.mutations[type](this.state, arg);
    }
    dispatch(type, arg) {
        this.actions[type]({commit: this.commit,state: this.state},arg );
    }
}
```

# vue源码（简版)

## vue的工作流程

1. 初始化 

   > 初始化data、props、事件等

   

2. 挂载 

   >执行编译，首次渲染、创建和追加过程

3. 编译

   > 编译模块分为三个阶段：parse、optimize、generate

4. 数据响应式

   >渲染函数执行时会触发getter进行依赖收集，将来数据变化时会触发setter进行更新

5. 虚拟dom

   > Vue2开始支持Virtual DOM，通过 JS对象描述dom，数据变更时映射为dom操作

6. 更新视图

   > 数据修改时监听器会执行更新，通过对比新旧vdom，得到最小修改，就是 patch

## vue2响应式原理

> defineProperty
>
> ```vue
> <div id="app">
> <p>你好，<span id='name'></span></p>
> </div>
> <script>
> var obj = {};
>     Object.defineProperty(obj, "name", {
>         get() {
>             console.log('获取name')
>             return document.querySelector('#name').innerHTML;
>     	},	
>         set(nick) {
>             console.log('设置name')
>             document.querySelector('#name').innerHTML = nick;
>         }
> 	});
>     obj.name = "Jerry";
>     console.log(obj.name)
> </script>
> ```

## 实现简版vue

```js
class KVue {
    constructor(options) {
        // 保存选项
        this.$options = options;
        // 传入data选项
        this.$data = options.data;
        // 响应化
        this.observe(this.$data);
    }
    observe(value) {
        if (!value || typeof value !== "object") {
        	return;
    	}
    	// 遍历，执行数据响应式
        Object.keys(value).forEach(key => {
        	this.defineReactive(value, key, value[key]);
        });
    }
    defineReactive(obj, key, val) {
        // 递归
        this.observe(val);
        // 给obj定义属性
        Object.defineProperty(obj, key, {
            get() {
            	return val;
            },
            set(newVal) {
            	if (newVal === val) {
            		return;
        		}
                val = newVal;
                console.log(`${key}属性更新了`);
        	}
        });
    }
}
未写完...
```

# 持续更新中.......