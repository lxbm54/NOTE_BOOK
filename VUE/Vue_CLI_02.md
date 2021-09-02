## 十八、Vuex 状态入门
### 1. Vuex 概念
- #### 安装脚手架的时候，勾选 Vuex 状态管理器；
- #### Vuex 用于解决组件与组件之间共享的状态，集中存储管理所有组件状态；
- #### 支持调试工具 devtools 查看分析；
- #### 分析-只能作用于这个组件，跳到其它组件即失效，返回该组件count归0；
```
<template>
	<div>
		<h1>This is an about page：{{count}}</h1>
		<button @click="addCount">计数</button>
	</div>
</template>
<script>
	export default {
		name: 'about',
		data() {
			return {
				count : 0
			}
		},
		methods: {
			addCount() {
				this.count++
			},
		}
	}
</script>
```

### 2. store 模式
- #### 如果不是大型的"单页面"应用，使用 Vuex 会繁琐冗余；
- #### 分析
- #### 创建一个 store/index.js
```
const store = {
	// 共享属性
	state: {
		count: 0
	},
	// 共享数据的累加
	increment() {
		this.state.count++
	}
}
export default store;
```

- #### 引用store
```
<template>
	<div>
		<button @click="increment">计数：{{storeCount}}</button>
	</div>
</template>
<script>
  // 引入stroe
	import store from "@/store";
	export default {
		name: "Count",
		data() {
			return {
				// 默认用共享数据给变量赋值
				storeCount: store.state.count
			}
		},
		methods: {
			increment() {
				// 调用共享方法，对共享数据进行累加
				store.increment();
				// 重新用共享数据给变量赋值
				this.storeCount = store.state.count
			}
		}
	}
</script>
```

## 十九、Vuex 安装和使用
### 1. vuex和缓存的区别
- #### vuex存储在内存，localstorage则以文件的方式存储在本地，永久保存，sessionstorage，临时保存。
- #### localStorage和sessionStorage只能存储字符串类型
- #### vuex用于组件之间的传值，localstorage，sessionstorage则主要用于不同页面之间的传值。
- #### 当刷新页面（属于清除内存）时vuex存储的值会丢失，sessionstorage页面关闭后就清除掉了，localstorage不会。
- #### localstorage，sessionstorage存储不变的数据
- #### 当两个组件共用一个数据源，如果其中一个组件改变了该数据源，希望另一个组件响应该变化时，就要使用vuex

### 2. 特性：响应式的，不能直接改变 store 状态
### 3. 目录结构
- #### Actions 获取数据->Mutations 修改处理->State 状态更新->Vue 组件渲染；
```
export default new Vuex.Store({
	state: {}, //状态值
	mutations: {}, //修改状态
	actions: {}, //接口异步请求，服务端请求数据
	modules: {}
})
```

### 4. 全局计数器
- #### main.js
```
import store from './store'
createApp(App).use(store).use(router).mount('#app')
```

- #### store/index.js 下
```
import { createStore } from 'vuex'
export default createStore({
	// 状态值
	state: {
		count : 0
	},
	// 修改状态
	mutations: {
		increment(state) {
			state.count++
		}
	},
})
```

- #### 页面组件
```
<template>
	<div>
	  <h1>全局 count：{{$store.state.count}}</h1>
		<button @click="increment">全局计数</button>
	</div>
</template>
<script>
  export default {
		name: 'about',
		methods: {
			increment() {
				//修改状态执行 mutation 里的 increment 方法
				this.$store.commit('increment')
			}	
		}
  }
</script>
```

- #### 如果不使用mutations修改数据，而是直接改变状态值如：this.$store.state.count++，则无法通过devtools最终到状态变化；

## 二十、State 状态设置.上
### 1. State 设置
- #### Vuex 是单一状态树，一个对象包含全部状态，每个应用仅一个 store 实例；
- #### 如果我们要设置多个状态在赋值取值就会比较繁琐；
- #### 分析
- #### store/index.js 下
```
export default createStore({
	// 状态值
	state: {
		count: 0,
		name: 'Mr.Lee',
		age: 100,
		gender: '男'
	},
	// 修改状态
	mutations: {
		setAge(state, value) {
			state.age = value
		}
	},
})
```

- #### 页面组件
- #### input双向绑定了age属性，当input值发生变化，则执行age的set方法，修改了共享属性值
```
<template>
	<div>
	  <ul>
			<li>姓名：{{name}}</li>
			<li>性别：{{gender}}</li>
			<li>年龄：{{age}}</li>
	  </ul>
	  <input type="text" v-model="age">
	</div>
</template>
<script>
  export default {
		name: 'about',
		computed : {
			name() {
				return this.$store.state.name
			},
			gender() {
				return this.$store.state.gender
			},
			age : {
				get() {
					return this.$store.state.age
				},
				set(value) {
					this.$store.commit('setAge', value)
				}
			}
		}
  }
</script>
```

## 二十一、State 状态设置.下
### 2. mapState 辅助函数
- #### 如果共享属性过多，对视图的插值操作就会很繁琐，所以引用mapState
- #### 页面组件
```
<template>
	<div>
	  <ul>
			<li>姓名：{{name}}</li>
			<li>性别：{{gender}}</li>
			<li>年龄：{{age}}</li>
	  </ul>
		<!-- v-model 因为丢失计算属性 set,get，改成事件 -->
	  <input type="text" @input="setAge" :value="age">
	</div>
</template>
<script>
  // 引入mapState函数
	import { mapState } from 'vuex'
	export default {
		name: 'about',
		// 数组方式插值，当状态名和模版插值名一致，用数组
		computed: mapState(['name', 'gender', 'age']),
		
		// 对象方式插值，如果状态名和模版插值名不一样，或者显示的插值还需要进一步处理的
		computed: mapState({
			// 第一个name是模板插值名，第二个name是State的属性
			name: state => state.name,
			// 简写版
			gender: gender,
			// 对显示的插值进行逻辑处理
			age(state){
				return state.age + 2
			}
		}),
		// 如果有其他计算属性，使用对象展开运算符处理
		computed: {
			test() {
				return 'test'
			},
			...mapState({
				name: 'name',
				gender: 'gender',
				age(state) {
					return state.age
				}
			})
		},
		methods: {
			setAge(e) {
				this.$store.commit('setAge', e.target.value)
			}
		}
	}
</script>
```

## 二十二、Getters 派生状态
### 1. 为什么使用Getters
- #### 在获取 state 状态时，有时我们需要对这个原生的值进行处理；
- #### 采用计算属性或辅助 mapState 均能实现，但每个组件都要进行处理就繁琐了；
- #### 所以使用 getters 派生来设置需要处理的 state 状态

### 2. 例：
- #### store/index.js 下
```
export default createStore({
	// 状态值
	state: {
		count: 0,
		name: 'Mr.Lee',
		age: 100,
		gender: '男'
	},
	// 状态派生
	getters: {
		// 给state性别属性加中括号
		getGender(state) {
			return `[${state.gender}]`
		},
		// 给 getters 传递自定义参数
		getName: (state) => (id) => {
			return `${id}：${state.name}`
		}
	}
})
```

- #### 页面组件
```
<template>
	<div>
	  <ul>
			<!-- 方法1：直接获取 -->
			<li>性别：{{ $store.getters.getGender }}</li>
			
			<!-- 方法2：通过计算属性进行返回 -->
			<li>性别：{{ getGender }}</li>
			
			<!-- 方法3：给 getters 传递自定义参数 -->
			<li>姓名：{{ getName }}</li>
			
			<!-- 方法4：使用vuex的辅助函数，数组形式 -->
			<li>性别：{{ getGender }}</li>
			
			<!-- 方法5：使用vuex的辅助函数，对象形式 -->
			<li>性别：{{ getSex }}</li>
	  </ul>
	</div>
</template>
<script>
  // 引入mapGetters函数
	import { mapGetters } from 'vuex'
	export default {
		name: 'about',
		computed: {
			// 方法2：通过计算属性进行返回
			getGender() {
				return this.$store.gettters.getGender;
			},
			
			// 方法3：给 getters 传递自定义参数
			getName() {
				return this.$store.gettters.getName(100);
			},
			
			// 方法4：使用vuex的辅助函数，数组形式
			...mapGetters(['getGender']),
			
			// 方法5：使用vuex的辅助函数，对象形式
			...mapGetters({
				getSex: 'getGender' //这种防止名称不一致可用
			})
		},
		methods: {
			
		}
	}
</script>
```

## 二十三、Mutations 状态提交
### 1. 可以改变状态的值
### 2. 提交载荷（payload），即传参
### 3. 提交载荷（payload），可传递Dom事件对象
### 4. 辅助函数
- #### store/index.js 下
```
import { createStore } from 'vuex'
export default createStore({
	// 状态值
	state: {
		count : 0
	},
	// 修改状态
	mutations: {
		increment(state) {
			state.count++
		},
		setAge(state, value) {
			state.age = value
		},
		setAge1(state, payload) {
			state.age = payload.target.value
		}
	},
})
```

- #### 页面组件
```
<template>
	<div>
	  <h1>全局 count：{{$store.state.count}}</h1>
		<button @click="increment">全局计数</button>
		<!-- 提交载荷 -->
		<input type="text" @input="setAge" :value="age">
		<!-- 提交载荷 -->
		<input type="text" @input="setAge1" :value="age">
		<!-- mapMutations 使用 -->
		<button @click="add">全局计数</button>
	</div>
</template>
<script>
	import { mapState, mapMutations } from 'vuex'
	export default {
		name: 'about',
		computed : {
			...mapState({
				age(state) {
					return state.age
				}
			}),
		},
		methods: {
			increment() {
				//修改状态执行 mutation 里的 increment 方法
				this.$store.commit('increment')
			},
			// 文本框输入的参数传递给State的age属性
			setAge(e) {
				this.$store.commit('setAge', e.target.value)
			},
			// 传递整个Dom对象
			setAge1(e) {
				this.$store.commit('setAge1', e)
			},
			// 按钮点击事件名称，与mutations下的方法名一致，使用：
			// ...mapMutations(['increment'])
			// 按钮点击事件名称，与mutations下的方法名不一致，使用：
			...mapMutations({
				add: 'increment'
			})
		}
	}
</script>
```

## 二十四、Actions 异步提交
### 1. Mutations 必须是同步函数，否则无法追踪数据；
### 2. 异步操作可以交给 Actions；同时Actions 也支持载荷
- #### store/index.js 下
```
import { createStore } from 'vuex'
export default createStore({
	// 状态值
	state: {
		info : ''
	},
	// 修改状态
	mutations: {
		// 方法1：追踪不到状态
		setInfo1(state, value) {
			setTimeout(() => {
				state.info = value
			}, 1000);
		},
		setInfo2(state, value) {
			state.info = value
		}
	},
	actions: {
		// Actions 提交
		setInfo3(context) {
			setTimeout(() => {
				context.commit('setInfo2', '异步信息')
			}, 1000)
		},
		// ES6 写法
		setInfo4({ commit }){
			setTimeout(() => {
				commit('setInfo2', '异步信息')
			}, 1000)
		}
	}
})
```

- #### 页面组件
```
<template>
	<div>
		<!-- 方法1：追踪不到状态 -->
	  <button @click="setInfo1"></button>
		<!-- 方法2：不同组件，都需要写定时器，代码冗余 -->
		<button @click="setInfo2"></button>
		<!-- 方法3：Actions 提交 -->
		<button @click="setInfo3"></button>
		<!-- 方法4：使用辅助函数 mapActions 提交 -->
		<button @click="setInfo4"></button>
	</div>
</template>
<script>
	import { mapState, mapMutations, mapActions } from 'vuex'
	export default {
		methods: {
			// 方法1：追踪不到状态
			...mapMutations(['setInfo1']),
			// 方法2：不同组件，都需要写定时器，代码冗余
			setInfo2(){
				setTimeout(() => {
					this.$sotre.commit('setInfo2', '异步请求');
				}, 1000)
			},
			// 方法3：Actions 提交
			setInfo3(){
				this.$store.dispatch('setInfo3');
			},
			// 方法4：使用辅助函数提交
			...mapActions(['setInfo4'])
		}
	}
</script>
```

## 二十五、Module 模块化
### 1. Module 设置
- #### 由于 state 使用的单一状态树，所有状态都集中在一个对象中，大了就很重
- #### Vuex 提供了 Module 模块化设置，可以将状态分割成一个个小的模块
- #### 例：新建文件store/module/list.js
```
export default {
	// 为了隔离主状态和模块状态名称冲突，需要设置命名空间为true
	namespaced: true,
	state: {
		name: ''
	},
	getters: {
		getName(state) {
			return '【' + state.name + '】'
		}
	},
	mutations: {
		setName(state, value) {
			state.name = value
		}
	},
	actions: {
		setName({commit}) {
			setTimeout(() => {
				commit('setName', 'Mr.Wang')
			}, 1000)
		}
	}
}
```

- #### 在 store/index.js 中的 Module 引入这个模块
```
import list from "@/store/module/list"
modules: {
	list
}
```

- #### 调用页面
```
<template>
	<div class="list">
	  <!-- 获取module的状态值-->
		<h3>{{ $store.state.list.name }}</h3>
		<!-- 获取module的派生状态 -->
		<h3>{{ getName }}</h3>
		<!-- 获取主模块的状态值 -->
		<h3>{{ $store.state.name }}</h3>
		<button @click="setName">获取+</button>
	</div>
</template>
<script>
	import { mapActions } from 'vuex'
	export default {
		name: "List",
		computed: {
			getName() {
				return this.$store.getters['list/getName']
			}
		},
		methods: {
			// 执行moudle的Actions方法
			setName() {
				this.$store.dispatch('list/setName')
			},
			// 使用辅助函数执行moudle的Actions方法
			...mapActions({
				setName: 'list/setName'
			})
		}
	}
</script>
```

## 二十六、Axios 跨域和封装
### 1. axios 跨域
- #### 
```
<template>
	<div class="post">
		<button @click="getList">axios+</button>
	</div>
</template>
```

- #### 普通请求
```
<template>
	<div class="post">
		<button @click="getList">axios+</button>
	</div>
</template>
<script>
	import axios from 'axios'
	export default {
		name: "Post",
		methods: {
			getList() {
				axios.get('https://cdn.liyanhui.com/data.json').then(res => {
					console.log(res.data)
				})
			}
		}
	}
</script>
```

- #### 跨域请求
- #### 创建vue.config.js
```
// 简单配置
module.exports = {
	devServer: {
		proxy: 'https://cdn.ycku.com/'
	}
}
// 完美配置
devServer : {
	proxy: {
		'/api': {
			target: 'https://cdn.ycku.com/', // API服务器的地址
			ws: true,  // 代理websockets
			changeOrigin: true, // 虚拟的站点需要更管origin
			pathRewrite: {   // 重写路径 比如'/api/aaa/ccc'重写为'/aaa/ccc'
				'^/api': ''
			}
		}
	}
}
```

- #### 请求页面
```
<script>
	import axios from 'axios'
	export default {
		name: "Post",
		methods: {
			getList() {
				axios.get('/api/data.json').then(res => {
					console.log(res.data)
				})
			}
		}
	}
</script>
```

### 2. axios 封装
- #### 首先，为何要封装？因为直接使用 axios，每个组件都要重复配置各种参数；
- #### 第二，方便后期维护，比如要更改某个参数，甚至换掉 axios，只要改一次；
- #### 在根目录创建 api/api.js，用于封装 axios；
```
import axios from 'axios'
export const get = (url) => {
	return new Promise((resolve, reject) => {
		axios.get(url).then(res => {
			resolve(res.data);
		}).catch(err => {
			reject(err.data)
		})
	})
}
```

- #### 请求页面
```
<script>
	import { get } from "@/api/api"
	export default {
		name: "Post",
		methods: {
			getList() {
				get('/api/data.json').then(data => {
					console.log(data)
				})
			}
		}
	}
</script>
```