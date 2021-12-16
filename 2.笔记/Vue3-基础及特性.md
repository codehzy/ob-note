# vue3
## 一、特性
### 1、proxy
1. vue2使用的Object.definePropty来实现，只能劫持到对象的属性，并且对于数组中直接删除数据检测不到。因此在vue2中我们删除数组某个数据使用$delete。
2. Proxy可以将整个对象进行代理，可以检测到数组数据的删除变化。
3. 对于Proxy，更利于Vue3对数据响应式的更改，并且Proxy代理支持新的数据结构。如：Set、Map
4. **Proxy缺点** ：兼容性问题（IE10以下）Tip：es6-proxy-ployfill
### 2、typescript
1. vue3对typeScript进行了支持，引入了typeScript类型检测系统，让开发者在代码编译的时候就会报错，提升了开发效率。
2. 开发者使用vscode开发项目，typeScript会对类型进行检测。
### 3、Componsition API
1.  **vue2缺点**，之所以不引入Componsition API，并重写Vue2的原因。
	1.  所有数据挂载在this上，对Tree-shaking不友好
	2.  对ts支持不好
	3.  代码过多时候，开发者需要在**data**和**methods**之间来回跳转
	4.  代码不好复用，因为Vue组件很难抽离逻辑。只能使用**mixin**，并且还会有命名冲突。
2.  **Vue3优点**
	1.  所有的api都是import引入，这样对Tree-shaking很友好。
	2.  不需要像vue2那样反复在data和methods中跳转代码
	3.  vue3中不可使用this.xxxx，vue3的箭头函数this都将指向window
	4.  代码复用很方便。在开发汇总，可以将某个功能的**method**和**data**封装到一个独立的函数中，我们通常称之为**Hook**，在原有的代码中，我们只需要调用Hook函数使用Hook的返回值即可。
	 ```ts
	 // ./useTitle.ts
	 
	 import { ref } from 'vue'
	 
	 export const useTitle = (data:string) => {
		let title = ref('')
		
		title.value = data
		
		return {title}
	 }
	 
	 // ./Title.vue
	 setup(){
	 	const { title } = useTitle('学习Vue3')
	 }
	 ```
	4.  Componsition API 需要return使用。当然可以选择结合`<script setup>`语法糖。![[ComponsitionAPi对比.png]]
### 4、新组件
1. TelePort
2. **supense**: 允许将等待过程提升到组件树中处理，而不是在单个组件中
3. Fragment
### 5、vite
https://cn.vitejs.dev/guide/
1. **webpack**
	1. 根据 import -> 依赖图 -> 使用工具将整个项目打包 -> 放入内容 ，因此预打包很慢
	 ![[webpack打包依赖图.png]]
2. Vite
	1. esbuild多线程
	2. 将项目首页依赖的文件依次通过网络去获取
	 ![[Vite打包依赖图.png]]
## 二、vue2 升级 vue3
1. 在依赖中安装升级vue3
2. 安装@vue/compat，此时控制台会警告，根据警告来修改代码适应vue3
3. 升级库，https://gogocode.io/zh/docs/specification/vue2-to-vue3
	![[google Code vue2-vue3.png]]
## 三、基础

### 1、script setup
1. Vue3.2以上，不需要对声明的变量和值进行返回，直接可以在template中使用。
2. 对于同一个功能封装成hook导出使用，推荐使用**vueuse**的**hooks**库。
3. 对于css可以利用v-bind来使用js的变量
```js
<template>
  <div>
    <h1 @click="add">{{ count }}</h1>
  </div>
</template>

<script setup>
import { ref } from "vue";
let count = ref(1)
let color = ref('red')
function add() {
  count.value++
  color.value = Math.random()>0.5? "blue":"red"
}
</script>

<style scoped>
h1 {
  color:v-bind(color);
}
</style>
```
### 2、响应式原理
1. Object.defineProperty
2. Proxy
3. 拦截value的get,set
 ![[三种响应式方法对比图.png]]
### 3、组件封装
1. 通用型组件
2. 业务型组件
3. ![[组件之间值传递.png]]
### 4、基本API使用
1. emit
```js
// ./Rate.vue
let emits = defineEmits(['update:modelValue']) // 定义emit

function onRate(num) {

emits('update:modelValue', num)

}

// ./todoList
<Rate :value="count" @update:modelValue="update" theme="red">工作评分</Rate>
```
2. 插槽
3. 动画和动画列表
## 四、全家桶

### 1、vuex
1. state
2. getters
3. mutations
4. actions
 [[项目搭建#2 3 vuex集成]]
```js
import { createStore } from 'vuex'

interface Obj {

	count: number

}

const store = createStore({

	state() {

		return {

		count: 666,

	}

},

getters: {

	double(state: Obj) {

	return state.count * 2

},

},

mutations: {

	add(state: Obj) {

	state.count++

},

},

actions: {

	asyncAdd({ commit }) {

		setTimeout(() => {

			commit('add')

			}, 1000)

		},

	},

})

export default store
```
### 2、vue-router
1. **createWebHistory**，内部使用hashchange事件
2. **内部使用hashchange事件**，1、pushState，2、replaceState
3. ![[vue-router两种不同实现.png]]
4. 动态路由
	1. ![[动态路由.png]]
5. 路由守卫
	1. 权限控制
	2. 路由动态导入，可以解决首屏文件过大问题
! [[项目搭建#2 2 vue-router集成]]
### 3、vue-devtools
1. Chrome商店下载安装插件
2. F12，可以看到某个vue组件的数据和vue-router
### 4、chrome调试页面
- elements
- console
- Source
- Network
- Performance
- Memory
- Application
- Lighthouse
- Vue-devtools
### 5、虚拟DOM（JSX）
1. Vue文件中使用
```js
<script lang="tsx">

import { defineComponent } from 'vue'

export default defineComponent({

	name: '',

	setup() {

		return () => {

			return (

				<div>

				<span>JSX</span>

				</div>

			)

		}

	},

})
</script>
```
2. tsx文件
```js
import { defineComponent, ref } from 'vue'

export default defineComponent({

name: 'Index',

setup() {

		const Index = ref('')

		Index.value = '这是Index'



		return () => <div>{Index.value}</div>

	},

})
```
### 6、ts进阶API
```ts
interface VueCourse5 {

	name: string,

	price: number

}

type CourseProp = keyof VueCourse5


let k:CourseProp = 'name'

let k1:CourseProp = 'price'


type ExtendsType<T> = T extends boolean ? '重学前端' : '玩转vue3'

type ExtendsType1 = ExtendsType<boolean>

type ExtendsType2 = ExtendsType<string>

  

type Course = '玩转vue3' | '重学前端'

type CourseObj = {

	[k in Course]:number

}


function getProperty<T,K extends keyof T>(o:T,name:K) {

	return o[name]

}

  

const coursePrice:CourseObj = {

	"玩转vue3":129,

	"重学前端":123

}

  

getProperty(coursePrice,'玩转vue3')

getProperty(coursePrice,'重学前端')
  

type Foo = () => CourseObj

type ReurnType<T> = T extends () => infer P ? P : number

type Foo1 = ReturnType<Foo>


// 第一题

interface Todo {

title: string,

desc: string,

done: boolean

}

type partTodo = Partial<Todo>

// 实现myPartial

type myPartial<T> = {

[K in keyof T]?: T[K]

}


type partTodo1 = myPartial<Todo>


import axios from 'axios'

  

interface Api{

	'/course/buy': {

		id: number

	}

	'/course/comment':{

		id: number

		message: string

	}

}

  

function request<T extends keyof Api>(url:T,obj:Api[T]){

	return axios.post(url,obj)

}

  

request('/course/buy',{id:1})

request('/course/comment',{id:1,message:'嘎嘎好看'})

// request('/course/comment',{id:1}) //如果message必传 怎么类型提醒缺少参数

// request('/course/404',{id:1}) //接口不存在 类型怎么需要报错
```
### 7、代码规范
- husky
- eslint
- prettierrc
[[项目搭建#1 2 使用prettier工具]]
[[项目搭建#1 3 使用ESLint检测]]
[[项目搭建#1 4 git Husky和eslint]]
[[项目搭建#1 5 git commit规范]]
[[项目搭建#1 5 1 代码提交风格]]
[[项目搭建#1 5 2 代码提交验证]]
### 8、图表Echarts
![[简单Echart.png]]
![[简单Echart.png]]
## 五、场景

### 1、权限系统
1. 通过addRoutes
	1. 在用户 -> 角色 -> 页面之间控制，通过addRoutes新增路由
	2. 页面重新设置权限时候，调用router.removeRoute来删除路由
	3. 需要本地存储存储动态路由的状态，当reload页面清空动态路由
	4. 路由是从本地存储（localStorage）中获取 将动态路由和静态路由合并成完整路由
2. token
	1. 在axios的响应拦截器看看返回的token是否过期，过期则清空页面所有登录状态
	2. 本地在将token写入localStorage中的时候也可以设置token过期时间，从而实现token的本地过期时间控制
### 2、vue性能优化
0. 性能分析 chrome 的Lighthouse
1. 对外链引入进行cdn缓存
2. 尽可能让文件尽可能的少的情况也要尽可能的小（图片懒加载）
3. 路由懒加载
4. 使用rollup-plugin-visualizer插件，在打包中生成stat.html文件，进行分析。对大的占用进行缓存。