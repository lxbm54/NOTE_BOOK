## 二十四、实例和生命周期
### 1. 应用实例
- #### 创建一个应用实例，{}里是选项对象，这里的 app 是实例

```
const app = Vue.createApp({})
```

- #### .mount()返回的是根组件实例对象，它可以直接调用 data()内的数据；

```
const vm = app.mount('#app')
```

### 2. 生命周期
- #### created 钩子：当实例被创建后会执行(在模板渲染前执行，一般用于初始化属性值)；
- #### mounted 钩子：当实例被挂载后会执行(在模板渲染完成后执行，此时可以操作 DOM)；
- #### updated 钩子：当虚拟 DOM 被修改后会执行；

```
<div id="app">
	<div id="countDom">{{ count }}</div>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const app = Vue.createApp({
		data() {
			return {
				count: 100
			}
		},
		// 初始化
		created() {
			console.log('created...');
			console.log(document.getElementById('countDom')); //null
		},
		// 挂载后执行
		mounted() {
			console.log('mounted...');
			console.log(document.getElementById('countDom').innerText); //100
		},
		updated() {
			console.log('updated...');
		}
	});
	// console.log(app);
	
	const vm = app.mount('#app');
</script>
```

## 二十五、动画和转场效果
### 1. 转场效果
- #### 使用 Vue 的效果组件```<transition>```来实现过渡效果；
- #### 点击按钮显示/隐藏文本标签，实现渐入渐出效果

```
<style>
	.fade-enter-active, .fade-leave-active {
		transition: opacity 0.5s ease;
	}
	.fade-enter-from, .fade-leave-to {
		opacity: 0;
	}
</style>
<div id="app">
	<button @click="show = !show">切换 {{ show }}</button>
	
	<transition name="fade">
		<h4 v-if="show">显示 / 隐藏</h4>
	</transition>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const app = Vue.createApp({
		data() {
			return {
				show: true
			}
		}
	});
	const vm = app.mount('#app');
</script>
```

## 二十六、ref 和$refs 的使用
### 1. ref 和$refs
- 需要操作DOM中的内容，可以使用 ref 在元素标签中进行设置注册，通过 $refs 对注册的信息进行内容获取；
- 在子组件上使用 ref，在外部父组件调用的方法；

```
<div id="app">
	<input type="text" ref="user" value="Lance.Liu">
	<!-- 子组件标签也要设置ref -->
	<html-a ref="child"></html-a>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const htmlA = {
		data(){
			return{
				message:'我是子组件A'
			}
		},
		template:`
			<input type="text" ref="person" value="Miss.Sun">
		`
	}

	const app = Vue.createApp({
		components:{
			'html-a': htmlA
		},
		data() {
			return {
				show: true
			}
		},
		created() {
			// 无法获取
			console.log(this.$refs.user);
		},
		mounted() {
			// 可以获取
			console.log(this.$refs.user);
			
			// 调用子组件ref方法
			console.log(this.$refs.child.$refs.user.value);
		}
	});
	const vm = app.mount('#app');
	// 可以获取
	console.log(vm.$refs.user.value);
</script>
```

## 二十七、watch 监听入门
### 1. watch 监听器
- #### 监听数据的变化

```
<div id="app">
	<!-- 方式 1，正常监听  -->
	<input type="text" v-model="user1">
	
	<!-- 方式 2，将实际逻辑封装到方法里 -->
	<input type="text" v-model="user2">
	
	<!-- 方式 3，监听对象 -->
	<input type="text" v-model="user3.name">
	
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const app = Vue.createApp({
		data() {
			return {
				user1: '',
				user2: '',
				user3: {
					name: 'Lance.Liu'
				}
			}
		},
		// 监听
		watch: {
			// 方式 1
			// 方法名和双向绑定名称一致
			// 参数 1 为新值，参数 2 为上一次值
			user1(newValue, oldValue) {
				console.log('new:' + newValue + ', old:' + oldValue)
			},
			
			// 方式 2
			// 将实际逻辑封装到方法里
			user2: 'getValue',
			
			// 方式 3
			// 调用计算属性监听
			getUserName: 'getValue'
		},
		computed: {
			// 返回对象中要监听的属性
			getUserName() {
				return this.user3.name
			}
		},
		methods: {
			getValue(newValue, oldValue) {
				console.log('new:' + newValue + ', old:' + oldValue);
			}
		}
	});
	
	const vm = app.mount('#app');
</script>
```

## 二十八、watch 监听进阶
### 1. watch 进阶
- #### 字符串形式监听对象属性
- #### 对象有多个数据，需要进行深度监听
- #### watch 提供了监听的三种属性
	+ #### (1) .handle：要监听的方法名；
	+ #### (2) .deep：是否开启深度监听；
	+ #### (3) .immediate：是否以当前的初始值执行方法(立即执行)；
- #### 使用数组方式进行监听，可以同时实现多个监听；

```
<div id="app">
	
	<!-- 字符串形式监听对象属性 -->
	<input type="text" v-model="user1.name">
	
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const app = Vue.createApp({
		data() {
			return {
				user: {
					name: 'Lance.Liu'
				}
			}
		},
		// 监听
		watch: {
			// 字符串形式监听对象属性
			'user.name': 'getValue',
			
			// 深度监听
			user1: {
				// 此时在 getUser 方法传递的参数只有一个，且只是 user 对象实例；
				handler: 'getUser', // 执行方法
				deep: true, // 开启深度监听
				immediate: true // 立即执行
			},
			
			// 数组监听
			user: [
				{
					handler: 'getUser1',
					deep: true,
				},
				{
					handler: 'getUser2',
					deep: true,
				}
			]
		},
		computed: {
			
		},
		methods: {
			// 字符串监听调用的方法
			getValue(newValue, oldValue) {
				console.log('new:' + newValue + ', old:' + oldValue);
			},
			
			// 深度监听，传递 User 对象
			getUser(obj) {
				// 此时可以处理 user 对象下的所有内容
				console.log(obj)
			},
			
			// 数组监听调用的方法
			getUser1(obj) {
				console.log('监听 1~'+ JSON.stringify(obj));
			},
			getUser2(obj) {
				console.log('监听 2~'+ JSON.stringify(obj));
			}
		}
	});
	
	const vm = app.mount('#app');
</script>
```

## 二十九、$watch 的使用
### $watch 实例方法和 watch 选项对象的作用功能是一样的

```
<div id="app">
	
	<input type="text" v-model="count">
	
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const app = Vue.createApp({
		data() {
			return {
				count: 100,
				user: {
					name: 'Miss.Sun'
				}
			}
		},
		created: {
			this.$watch('count', (newValue, oldValue) => {
				console.log(newValue + ',' + oldValue);
			});
			
			// 对于复杂的对象类型，需要使用箭头函数来表示需要监听的内容；
			this.$watch(() => this.user.name, (newValue, oldValue) => {
				console.log(newValue + ',' + oldValue)
			});
			
			// $watch 也支持深度监听的，支持关键字 deep 和 immediate 选项；
			this.$watch('user', (obj) => {
				console.log(obj.age)
			}, {
				deep : true,
				immediate : true
			});
			
		},
		methods: {
			
		}
	});
	
	const vm = app.mount('#app');
	
	// 这里本身会返回一个取消侦听的函数；
	const unwatch = vm.$watch('count', (newValue, oldValue) => {
		console.log(newValue + ',' + oldValue)
	});
	
	// 可以根据业务适时取消监听
	unwatch();
	
</script>
```

## 三十、混入可复用
### 1. 混入(mixin)：用来分发 Vue 组件中可复用的部分
- #### (1) 重名时，会替换掉混入的部分，没有重名，则合并；
- #### (2) 如果复用的部分有声明周期钩子，比如 created()，将会执行这个；
- #### (3) 如果复用的部分都有声明周期钩子，那么两个钩子都会被执行；
- #### (4) 如果复用的部分有 methods 等方法，重名将覆盖，不重名则合并；
- #### (5) 全局混入使用 app.mixin()，和全局组件注册类似

```
<div id="app">
	
	<button @click="getInfo">执行</button>
	
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>

	// 创建一个要复用的混入 Mixin
	const baseMixin = {
		data(){
			return{
				count: 0
			}
		},
		created() {
			this.count = 100;
			console.log('我是复用的钩子~' + this.count);
		},
		methods: {
			getInfo() {
				console.log('我是复用的方法~')
			}
		}
	}

	const app = Vue.createApp({
		// 载入复用的混入
		mixins : [baseMixin],
		data() {
			return {
				count: 100,
				message: 'Vue3.x~'
			}
		},
		created() {
			this.count = 300;
			console.log('我是组件内的钩子~' + this.count);
		},
		methods: {
			getInfo() {
				console.log('我是组件内的方法~' + this.count);
			}
		}
	});
	
	const vm = app.mount('#app');
	
</script>
```