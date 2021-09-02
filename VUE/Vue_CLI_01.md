## 一、Vue-cli的安装
### 1. node安装
### 2. 国内镜像安装，npm install -g cnpm --registry=https://registry.npm.taobao.org
### 3. Vue/Vue-cli安装，cnpm install -g @vue/cli@next

## 二、创建 Vue 项目
### 1. 创建项目，vue create cli-01
### 2. 手动操作，选择Manually
### 3. 必选，Choose Vue version，Babel
### 4. 可选，Router(路由)，Vuex(状态管理)
### 5. 空格是选择，回车是安装
### 6. Where do you prefer placing config for Babel, ESlint, etc.? 选择：In package.json
### 7. Save this as a preset for future projects? 选择：N
- #### 是否要给保存的配置起名，当你给保存的配置起了名称，下次创建，就可以直接用这个配置了；
- #### 如果想要删除这个配置信息，可以删除配置信息文件：.vuerc；
- #### Linux 下在 home/.vuerc，Win 下在 C:\Users\用户名\.vuerc；

### 8. 进入目录：cd cli-01，运行：npm run serve

## 三、组件化开发
### 1. 根组件：App.vue，入口文件：main.js
### 2. 入口文件
- #### 引入 Vue 框架：import { createApp } from 'vue'
- #### 引入 App.vue 根组件：import App from './App.vue'
- #### 创建实例/挂载：createApp(App).mount('#app')

## 四、Router 入门和安装
### 1. Router安装：创建Vue项目时，选择Router
### 2. 修改模式，位置：src/router/index.js
- #### history 模式
```
import { createRouter, createWebHistory } from 'vue-router'
const router = createRouter({
	history: createWebHistory(process.env.BASE_URL),
	routes
})
```
- #### hash 模式
```
import { createRouter, createWebHashHistory } from 'vue-router'
const router = createRouter({
	history: createWebHashHistory(),
	routes
})
```

### 3. 入口文件
- #### 引入路由：import router from './router'
- #### 创建实例/注册路由/挂载：createApp(App).use(router).mount('#app')

### 4. src/router/index.js 分析
```
// 引入 vue-router，并导入两个方法
// createRouter 用于创建路由对象
// createWebHistory 用于启用无#号的 history 模式
import { createRouter, createWebHistory } from 'vue-router'

// 引入 Home 组件，就是起始页面
import Home from '../views/Home.vue'

// 数组用于设置每个组件的信息
const routes = [
	{
		path: '/',
		name: 'Home',
		component: Home
	},
	...
]

// 路由对象，实例化
const router = createRouter({
	// 开启 history 模式，去#号
	history: createWebHistory(process.env.BASE_URL),
	// ES6 语法，key 和 value 相同时可以简写
	// routes : routes
	routes
})

//导出路由
export default router
```

### 5. 两个标签
- #### ```<router-link>```： 组件导航
- #### ```<router-view/>```： 渲染页面

## 五、动态路由匹配
### 1. query 模式
- #### 路由：user?id=5
- #### 匹配：this.$route.query.id

### 2. params 模式
- #### 配置：src/router/index.js下
```
{
	path: '/user/:id',
	name: 'User',
	component: User
}
```
- #### 路由：```<router-link to="/user/5">User/5</router-link>```
- #### 匹配：this.$route.params.id

### 3. 监听路由变化
- #### 针对同一组件，不同参数，生命周期钩子只被调用一次
- #### 使用 this.$watch()
```
// 初始化
created() {
	// 监听来去，this.$route 也可以写成 this.$route.params 缩小范围
	this.$watch(() => this.$route, (to, from) => {
		// 去哪里？
		console.log(to)
		// 从哪里来？previous
		console.log(from)
	})
}
```
- #### 使用watch监听对象
```
watch : {
	$route(to, from) {
		console.log(to)
		console.log(from)
	}
}
```

## 六、路由匹配规则
### 1. 动态路由的参数必须是 0-9 的1位或多位纯数字
- #### ```path: '/user/:id([0-9]+)',```
- #### ```path: '/user/:id(\\d+)',```

### 2. 路由参数为多个，*号(0个或多个)、+号(1个或多个)、?号(0个或1个)
- #### ```path: '/about/:params+',```
- #### 可以匹配 /about/aaa、/about/aaa/bbb/ccc

### 3. 路由参数为多个，每个参数必须是1位或多位纯数字
- #### ```path: '/user/:id(\\d+)+',```

### 4. 匹配失败页面的跳转
```
{
	path : '/:all*',
	name : 'error',
	component: () => import('../views/Error.vue')
}
```

## 七、嵌套路由
### 1. 配置：src/router/index.js下
```
{
	path: '/user/:id(\\d+)+',
	name: 'User',
	component: () => import('../views/User.vue'),
	children : [
		{
			path : 'profile',
			component : () => import('../views/user/Profile.vue'),
		}
	]
},
```
### 2. 父路由使用视图渲染加载子路由，在User.vue下
```
<template>
	<div>
		<router-view></router-view>
	</div>
</template>
```
### 3. 路由跳转：```<router-link to="/user/5/profile"></router-link>```
### 4. 子路由匹配：
```
<template>
	<h3>Profile 个人档案：{{$route.params.id}}</h3>
</template>
```

## 八、编程式导航
### 1. 编程式导航
- #### ```<router-link>```标签缺乏编程性，无法进行各种逻辑判断
- #### 可通过执行函数，去导航
- #### 创建一个按钮，点击执行函数，```<button @click="goList">goList</button>```
```
//方法一：参数为组件字符串
goList() {
	//注意：带斜杠就是跳转到根路径，不带则不是
	this.$router.push('/list')
}
//方法二：参数为对象，使用name进行路由
goList() {
	this.$router.push({
		//name 值，名称区分大小写，不是路径
		name : 'List'
	})
}
//方法三：参数为对象，使用path进行路由
goList() {
	this.$router.push({
		//路径
		path : '/list'
	})
}
```

### 2. 带参数编程式导航
- #### ```<button @click="goUser(5)"></button>```
```
//1.query方式
goUser(id) {
	this.$router.push({
		path : '/user', //path 配合 query
		query : {
			id
		}
	})
}
//2.params方式
goUser(id) {
	this.$router.push({
		name : 'User', //name 配合 params
		params : {
			id
		}
	})
}
```

### 3. 上一页，下一页
- #### 上一页： ```<button @click="go(-1)">goUp</button>```
- #### 下一页： ```<button @click="go(1)">goDown</button>```
```
go(step) {
	this.$router.go(step)
}
```

## 九、命名路由和视图
### 1. 命名路由
- #### ```<router-link>``` 上实现
```
<router-link :to="{name : 'User', params : {id : 5}}"></router-link>
```

### 2. 命名视图
- #### 需求：每个页面组件加载的时候，还需要另外加载 Footer 组件
- #### 配置：src/router/index.js下
```
import Home from '../views/Home'
// 引入Footer组件
import Footer from "../views/Footer"
{
	path: '/',
	name: 'Home',
	components : {
		default : Home,
		Footer
	}
},
{
	path: '/about',
	name: 'About',
	components: {
		default: () => import('../views/About.vue'),
		Footer
	}
}
```

- #### 根组件配置，App.vue
```
<!--加载动态视图-->
<router-view/>
<!--加载固定视图组件 Footer.vue-->
<router-view name="Footer"></router-view>
```

## 十、路由重定向
### 1. 使用redirect
- #### 配置：src/router/index.js下
```
//当地址为/a 则跳转到首页
{
	path : '/a',
	redirect : '/'
},
//跳转到指定 name 的组件
{
	path: '/a',
	redirect: {
		name: 'List' //或者 path 也支持
	}
},
```

### 2. 支持逻辑判断
- #### 配置：src/router/index.js下
```
//跳转到闭包
{
	path: '/a',
	redirect: () => {
		if(true) {
			return '/list'
		}
	}
},
{
	path: '/a',
	redirect: to => {
		//获取相关数据
		console.log(to)
		return{
			name : 'List'
		}
	}
```

### 3. 路由可以设置别名
- #### 配置：src/router/index.js下
```
{
	path: '/list',
	name: 'List',
	alias : '/liebiao',
}
```

- #### /list 和 /liebiao 跳转的地址相同

## 十一、路由的组件参数
### 1. 布尔模式
- #### 配置：src/router/index.js下
```
{
	path: '/user/:id',
	name: 'User',
	component: User,
	//这里布尔为 true 时，route.params 变成组件属性
	props : true,
}
```

- #### 组件页面
```
<template>
<div>
	<h3>User Id : {{id}}</h3>
</div>
</template>
<script>
	export default {
		name: "User",
		props: ['id']
	}
</script>
```

### 2. 对象模式
- #### 配置：src/router/index.js下
```
{
	path: '/user/:id',
	name: 'User',
	component: User,
	props: {
		data: '用户'
	},
}
```

- #### 组件页面
```
<template>
<div>
	<h3>User Id : {{data}}</h3>
</div>
</template>
<script>
	export default {
		name: "User",
		props: {
			data: {
				type: String
				default: ''
			}
		}
	}
</script>
```

### 3. 命名视图传参
- #### 配置：src/router/index.js下
```
{
	path: '/user/:id',
	name: 'User',
	component: User,
	props: {
		default: true,
		Footer: {
			data: 'Footer组件可以接收到data参数'
		}
	},
	components: {
		default: () => import('../views/User.vue'),
		Footer
	},
}
```

- #### 组件页面 Footer.vue
```
<template>
<div>
	<h3>{{data}}</h3>
</div>
</template>
<script>
	export default {
		name: "Footer",
		props: {
			data: {
				type: String
				default: ''
			}
		}
	}
</script>
```

### 4. query传参
- #### 非命名视图方案
```
// src/router/index.js下
{
	path: '/user/:id',
	name: 'User',
	component: User,
	props: route => ({
		search: route.query.search
	}),
	components: () => import('../views/User.vue'),
}
// 组件
<template>
	<div>
		<h1>{{search}}</h1>
	</div>
</template>
<script>
	export default {
		name: "User",
		props: {
			search: {
				type: String,
				default: ''
			}
		}
	}
</script>
```

- #### 命名视图方案
```
// src/router/index.js下
{
	path: '/user/:id',
	name: 'User',
	component: User,
	props: {
		default: route => ({
			search: route.query.search
		})
	},
	components: {
		default: () => import('../views/User.vue'),
		Footer
	},
}
// 组件
<template>
	<div>
		<h1>{{search}}</h1>
	</div>
</template>
<script>
	export default {
		name: "User",
		props: {
			search: {
				type: String,
				default: ''
			}
		}
	}
</script>
```

## 十二、Node 静态服务器
### 1. 打包
- #### ```npm run build```

### 2. 配置服务器
- #### 安装： ```npm i serve -g```
- #### 运行： ```serve dist```

## 十三、导航守卫
### 1. 全局前置守卫
- #### to：进入目标的路由对象；
- #### from：正要离开的路由对象；
- #### next：钩子函数；
```
router.beforeEach((to, from, next) => {
	if(flag) {
		// 如果已登录，访问Login则跳转到首页，否则正常跳转
		if (to.name === 'Login') next('/')
		else next()
	}else {
		// 如果未登录，访问Login则正常跳转，否则跳转到Login页
		if (to.name === 'Login') next()
		else next('/login')
	}
}
```
### 2. 全局后置钩子
- #### 用于路由页面加载完毕之后操作的一些动作，比如取消 loading；
```
router.afterEach((to, from) => {
	console.log('关闭 loading...')
})
```

## 十四、组件内的守卫
### 1. 路由独享守卫，仅供这个路由进行设置
```
{
	path: '/',
	name: 'Home',
	component: Home,
	//路由独享守卫
	beforeEnter: (to, from, next) => {
		console.log(from)
		console.log(to)
		next()
	}
},
```
### 2. 组件内守卫
```
export default {
	beforeRouteEnter(to, from, next) {
		console.log('组件被激活时会调用这个！即：进入这个组件时~')
		// 在激活前触发是无法得到 this 组件实例的，因为还没被创建，可以回调获取；
		next(vm => {
			console.log(vm)
		})
	},
	beforeRouteLeave(to, from, next) {
		console.log('组件失活时会调用这个！即：离开这个组件时~' + this)
		next()
	},
	beforeRouteUpdate(to, from, next) {
		console.log('路由改变，组件被复用时~，比如 about/:id 动态路由' + this)
		next()
	}
}
```

### 3. 取消导航跳转，next(false)

## 十五、元信息和过渡动效
### 1. 路由元信息
- #### 给路由配置一个 meta 属性，这个属性一般设置对象即可
```
{
	path: '/about',
	name: 'About',
	meta: {
		title : '关于',
		transition : 'abc'
	},
	component: About
}
router.beforeEach((to, from, next) => {
	// 获取元信息
	if(to.meta.title !== undefined) {
		console.log(to.meta.title)
	}
})
```

### 2. 过渡动效
- #### 老方法
```
<transition name="fade">
	<router-view/>
</transition>
```

- ### 新方法
```
<router-view v-slot="{ Component }">
	<transition name="fade">
		<component :is="Component" />
	</transition>
</router-view>
```

### 3. 单独设置某个路由的过渡效果
```
{
	path: '/about',
	name: 'About',
	meta: {
		title: '关于',
		transition: 'fade2'
	},
	component: () => import('../views/About.vue')
}
// route.meta.transition存在则使用，否则使用fade
<router-view v-slot="{Component, route}">
	<transition :name="route.meta.transition || 'fade'">
		<component :is="Component" />
	</transition>
</router-view>
```

## 十六、路由数据获取
```
<template>
	<div>
		<div v-if="post">
			<h2>{{post.title}}</h2>
			<p>{{post.body}}</p>
		</div>
	</div>
</template>
<script>
export default {
	name: "Post",
	data() {
		return {
			post : null
		}
	},
	//导航完成后获取
	created() {
		this.getData()
	},
	//导航完成前获取
	beforeRouteEnter(to, from, next) {
		//模拟获取的延迟过程
		//由于是导航完成前，用户会卡在当前界面，可以在这里做个弹窗 loaindg...
		setTimeout(() => {
			next(vm => {
				vm.getData()
			})
		}, 2000)
	},
	methods: {
		getData() {
			//模拟获取的延迟过程
			setTimeout(() => {
				this.post = {
					title: '标题',
					body: '内容...'
				}
			}, 1000)
		}
	}
}
</script>
```

## 十七、路由懒加载
### 1. 路由懒加载
```
{
	path: '/about',
	name: 'About',
	// 在这里引用即表示，加载这个路由再加载这个组件
	component: () => import('../views/About.vue')
}
```
### 2. 用@代替..路径
```
{
	path: '/about',
	name: 'About',
	// 在这里引用即表示，加载这个路由再加载这个组件
	component: () => import('@/views/About.vue')
}
```
### 3. 设置注释命名，将打包的 js 合并起来
```
const About = () => import(/* webpackChunkName:"group" */ '@/views/About.vue')
const Post = () => import(/* webpackChunkName:"group" */ '@/views/Post.vue')
const Login = () => import(/* webpackChunkName:"group" */ '@/views/Login.vue')
```