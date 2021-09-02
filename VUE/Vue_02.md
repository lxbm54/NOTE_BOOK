## 十四、组件的概念
### 1. 组件概念
- #### （1） 拥有唯一性的组件名称，方便调用；
- #### （2） 组件名称为 HTML 元素标签形式存在，扩展了 HTML；
- #### （3） 组件可以复用，且组件与组件之间互不干涉；

```
<div id="app">
	<button-counter></button-counter>
	<button-counter></button-counter>
	<button-counter></button-counter>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	// 创建一个 Vue 应用实例
	const app = Vue.createApp({});
	
	// 定义一个全局组件
	app.component('button-counter', {
		// 数据
		data() {
			return {
				counter: 100
			}
		},
		// 模板
		template: ` 计算器：{{counter}}
			<button v-on:click="counter++">
				counter++
			</button>
		`
	});
	
	// 挂载
	app.mount('#app');
</script>
```

## 十五、组件的注册
### 1. 规则说明
- #### （1） 组件名称是唯一的；
- #### （2） 命名规则可以采用 kebab-case(button-counter)，减号隔开；
- #### （3） 命名规则也可以用 PascalCase(ButtonCounter)，单词首字母大写；
- #### （4） 这里推荐使用 kebab-case，全小写并用减号隔开，遵循 W3C，防止冲突；

### 2. 全局和局部
- #### 全局组件连缀方法

```
Vue.createApp({}).component('button-counter', {}).mount('#app');
```

- #### 局部注册
```
<div id="app">
	<button-counter></button-counter>
	<button-counter2></button-counter2>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	// 可以把组件的参数分离出来，让代码更清晰且更好管理
	const buttonCounter = {
		data(){
			return{
				counter: 100
			}
		},
		template:`
			<div>
				计数器：{{ counter }}
				<button v-on:click="counter++">counter++</button>
			</div>
		`
	}

	const vm = Vue.createApp({
		data() {
			return {}
		},
		// 定义局部组件
		components: {
			'button-counter': {
				// 数据
				data() {
					return {
						counter: 100
					}
				},
				template:`
					<div>
						计数器：{{ counter }}
						<button v-on:click="counter++">counter++</button>
					</div>
				`
			},
			'button-counter2': buttonCounter,
			'button-counter3': {}
		}
	});
	
	vm.mount('#app');
</script>
```

## 十六、组件 Prop 通信
### 1. 数据父传子
- #### Vue 提供了一个 props 来获取父组件给子组件传递的值；
- #### props 里定义数组，表示可以定义多个；在 HTML 端使用 v-bind:abc 来获取父组件对应值；

```
<div id="app">
	<!-- 这里的parent-message对应子组件的parentMessage -->
	<html-a v-bind:parent-message="message"></html-a>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const htmlA = {
		data(){
			return{
				message: '子组件'
			}
		},
		props:['parentMessage'],
		template:`
			<div>{{ parentMessage }}</div>
			<div>{{ message }}</div>
		`
	}

	const vm = Vue.createApp({
		data(){
			return{
				message: '父组件'
			}
		},
		// 定义局部组件
		components:{
			'html-a' : htmlA
		}
	});
	
	vm.mount('#app');
</script>
```

## 十七、组件 Prop 类型
### 1. 静态 Prop
- #### 直接通过 HTML 端给子组件赋值；
- #### 没有使用 v-bind 就是静态 Prop，然后将字符串直接赋值给 parentMessage；

```
<html-a parent-message="直接传入静态字符串"></html-a>
```

### 2. Prop 类型
- #### 当传入字符串的时候，不需要 v-bind，当传入数字、数组、布尔和对象时则需要；
- #### 当传入不匹配的类型时，控制台会提示警告；null 和 undefined 会通过任何类型检查；

```
props: {
	title: String,
	likes: Number,
	isPublished: Boolean,
	commentIds: Array,
	author: Object,
	callback: Function,
	contactsPromise: Promise // 或任何其他构造函数
}
```

- #### 传入对象的所有 property(属性)，子组件要根据对象元素的键名配置props

```
<div id="app">
	<html-a v-bind="person"></html-a>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const htmlA = {
		data(){
			return{
				message: '子组件'
			}
		},
		props:['name','sex','age'],
		template:`
			<div>{{ name }}</div>
			<div>{{ sex }}</div>
			<div>{{ age }}</div>
		`
	}

	const vm = Vue.createApp({
		data(){
			return{
				message: '父组件',
				person: {
					name: 'Lance',
					sex: '男',
					age: 100
				}
			}
		},
		// 定义局部组件
		components:{
			'html-a' : htmlA
		}
	});
	
	vm.mount('#app');
</script>
```

## 十八、组件单向数据流
### 1. 单向数据流
- #### （1） 父组件的 data 值更新后通过 props 选项交给子组件进行渲染，反之则不行；
- #### （2） 这就是单向数据流(单向下行绑定)，不能通过子组件来改变父组件的状态；
- #### （3） 这样做的是为了防止父组件发生改变后，数据流变得难以理解；
- #### （4） 父组件更新时，子组件所有 props 值也会更新，你不能改变子组件的 props 值；
- #### （5） Props 是只读，无法修改；
- #### （6） 解决办法：通过使用子组件的 data 属性，让 props 父组件的值先赋值过来；

```
<div id="app">
	<html-a :parent-counter="counter"></html-a>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const htmlA = {
		data(){
			return{
				childCounter : this.parentCounter
			}
		},
		props:{
			'parentCounter': Number
		},
		template:`
			<div>{{ childCounter }}</div>
			
			<!-- 通过操作这个子组件的 childCounter 实现累加，而并未修改父组件的 counter； -->
			<!-- 通过计算属性 computed 或方法 methods 来修改 childCounter 也是可以的； -->
			<button @click="childCounter++">counter+</button>
		`
	}

	const vm = Vue.createApp({
		data(){
			return{
				counter: 100
			}
		},
		// 定义局部组件
		components:{
			'html-a' : htmlA
		}
	});
	
	vm.mount('#app');
</script>
```

## 十九、组件 Prop 验证
### 1. Prop 验证
- #### 传递一个数值

```
<html-a v-bind:parent-message="100"></html-a>

props: {
	parentMessage : Number
}
```

- #### 设置限定多个类型

```
// 传递过来的类型，数值和字符串均可通过；
props: {
	parentMessage: [Number, String]
}
```

- #### 如果没有值传入的时候，也可以给其设置一个默认值；

```
props: {
	parentMessage: {
		type: [Number, String],
		default: 100
	}
}
```

- #### 默认值也可以通过函数进行返回；

```
props: {
	parentMessage: {
		type: [Number, String],
		default: function() {
			return 200
		}
	}
}
```

- #### 全部类型检查如下：String/Number/Boolean/Array/Object/Date/Function/Symbol
- #### 使用自定义验证函数 validator 来处理传递过来的数据；

```
// 传递过来的值必须是 a,b,c 中的一种，否则提示不匹配；
props: {
	parentMessage: {
		validator(value) {
			return ['a', 'b', 'c'].indexOf(value) !== -1
		}
	}
}
```

## 二十、组件自定义事件
### 1. 自定义事件，可实现更改父组件的值
- #### 执行顺序：
- #### （1） html-a子组件定义了自定义事件(child-event)，点击按钮执行自定义事件，并抛出一个值；
- #### （2） 父组件使用 v-on 以自定义事件去执行父组件的一个方法；
- #### （3） 执行父组件的一个方法，并获取子组件传递的值，然后更改父组件内容；
- #### （4） 父组件内容更改后，同时给html-b子组件进行了传值

```
<div id="app">
	<html-a v-on:child-event="parentFn"></html-a>
	<html-b :parent-message="message"></html-b>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const htmlA = {
		data(){
			return{
				info: '子组件传值给父组件'
			}
		},
		// 定义子组件的自定义事件
		emits:['child-event'],
		template:`
			<button v-on:click="childClick(info)">发送</button>
		`,
		methods:{
			childClick(val){
				this.$emit('child-event', val);
			}
		}
	}
	
	const htmlB = {
		data(){
			return{
				message: this.parentMessage
			}
		},
		mounted(){
		},
		props:{
			'parentMessage':{
				type:[Number, String],
				default: 0
			}
		},
		template:`
			<div>{{ parentMessage }}</div>
		`
	}

	const vm = Vue.createApp({
		data(){
			return{
				'message': '父组件'
			}
		},
		// 定义局部组件
		components:{
			'html-a': htmlA,
			'html-b': htmlB
		},
		methods:{
			parentFn(value){
				// console.log(value);
				this.message = value;
			}
		}
	});
	
	vm.mount('#app');
</script>
```

### 2. 验证抛出的事件
```
// 当抛出的值不等于abc，则抛出失败
emits : {
	// 复杂验证
	'child-event': (value) => {
		if (value !== 'abc') {
			console.log('验证失败~')
			return false
		} else {
			return true
		}
	}
}
```

## 二十一、组件双向绑定
### 1. 双向绑定

```
// 通过 input 事件来获取输入的值，再赋值给 message，而<input>再通过 v-bind 更新值；
<input type="text" v-model="message">
等同于
<input type="text" v-bind:value="message" v-on:input="message = $event.target.value">
```

### 2. 组件双向绑定
```
<div id="app">
	<!-- 将 v-bind:改成 v-model 即双向绑定 -->
	<!-- 通过双向绑定自定义事件，把子组件抛出的值赋值给父组件 -->
	<html-a :parent-message="message" v-model:both-message="message"></html-a>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const htmlA = {
		data(){
			return{
				message: '子组件'
			}
		},
		props: ['parentMessage'],
		template : `
			<!-- 通过input事件，执行自定义事件 parentMessage，并抛出输入到文本框的值 -->
			<div>
				<input
					type="text"
					v-bind:value="parentMessage"
					v-on:input="$emit('update:parentMessage', $event.target.value)">
			</div>
			<div>{{ parentMessage }}</div>
			<div>{{ message }}</div>
			
			<!-- 也可以通过计算属性中的 get()和 set()来改写组件中的双向绑定； -->
			<div><input type="text" v-model="value"></div>
		`,
		computed: {
			value: {
				get() {
					return this.parentMessage
				},
				set(value) {
					this.$emit('update:parentMessage', value)
				}
			}
		}
	}
	
	const vm = Vue.createApp({
		data(){
			return{
				'message': '父组件'
			}
		},
		// 定义局部组件
		components:{
			'html-a': htmlA
		}
	});
	
	vm.mount('#app');
</script>
```

## 二十二、组件插槽使用
### 插槽<slot>，可以实现内容的动态分发；
- #### 给组件传不同货币符

```
<div id="app">
	<html-a>￥</html-a>
	<html-a>$</html-a>
	<html-a>元</html-a>
	<html-a></html-a>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const htmlA = {
		data(){
			return{
				message: 100
			}
		},
		template : `
			<div>
				<!-- 插槽可以设置默认值 -->
				{{ message }} <slot>￥</slot>
			</div>
		`
	}
	
	const vm = Vue.createApp({
		components:{
			'html-a': htmlA
		}
	});
	
	vm.mount('#app');
</script>
```

## 二十三、具名插槽和作用域
### 1. 具名插槽
- #### 具名插槽也就是给插槽起一个名字，然后调用对应的内容，而其它则被忽略；

```
<div id="app">
	<html-a v-slot:header>!!!</html-a>
	<!-- 插槽缩写 -->
	<html-a #header>!!!</html-a>
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
			<div>{{ message }}</div>
			<div><slot name="header"></slot></div>
		`,
		methods:{
		}
	}
	
	const vm = Vue.createApp({
		components:{
			'html-a': htmlA
		}
	});
	vm.mount('#app');
</script>
```

### 2. 作用域问题
- #### 插槽内的值是无法获取到子组件 data 属性的内容的；
- #### 解决办法：用 v-slot:default 来处理

```
<div id="app">
	<!-- v-slot:default  -->
	<html-a v-slot:default="slotProps">
		{{ slotProps.message }}
	</html-a>
	
	<!-- 简写 -->
	<html-a v-slot="slotProps">
		{{ slotProps.message }}
	</html-a>
	
	<!-- ES6+的解构语法 -->
	<html-a v-slot="{ message }">
		{{ message }}
	</html-a>
	
	<!-- 这里的info可以自定义 -->
	<html-a v-slot="{ message: info }">
		{{ info }}
	</html-a>
	
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
			<div>{{ message }}</div>
			<div><slot v-bind:message="message"></slot></div>
		`,
		methods:{
		}
	}
	
	const vm = Vue.createApp({
		components:{
			'html-a': htmlA
		}
	});
	vm.mount('#app');
</script>
```