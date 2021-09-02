## 一、Vue3 入门和安装
### 1. Vue 是一款基于 JavaScript 的构建用户界面的渐进式框架，版本为 3.x；
### 2. 所谓渐进式，就是按需分配，用多少，就引入多少，由开发者自行决定；
### 3. Vue 的核心库只关心视图层
```
<script src="https://unpkg.com/vue@next"></script>
<script>
	console.log(Vue);
</script>
```

### 4. Vue Devtools工具
```
https://github.com/vuejs/devtools
```

## 二、渲染和挂载
### 1. 前端框架基本都是为了简化：模版渲染、事件绑定和用户交互问题；
### 2. Vue 的 MVVM 模型：即视图(View)-视图模型(ViewModel)-模型(Model)；
### 3. Vue 的操作核心：即使用模版语法声明式的将数据渲染进 DOM 中去；
```
<!-- 创建一个Dom结构，内部有一个动态变化数据需要用Vue处理 -->
<div id="app">
	<!-- 插值对应的是data()里的counter属性 -->
	计数器：{{ counter }}
</div>
<!-- 引入Vue -->
<script src="https://unpkg.com/vue@next"></script>

<script>
	// 声明一个选项对象
	const App = {
		// 初始数据
		data() {
			return {
				counter: 100
			}
		}
	}
	
	//这个选项对象App，需要当作参数传递到Vue的全局API：createApp()
	// createApp()方法会返回应用实例，再通过这个实例来挂载对应的 DOM 即可；
	// 全局 API 对象.创建实例(App).挂载('#app')
	Vue.createApp(App).mount('#app');
</script>
```

## 三、文本插值及指令
### 1. 数据绑定最常用的方式就是：“Mustache”语法，即双花括号进行文本插值；
### 2. 绑定在模板中的插值{{ counter }}，模板是可变的，可以理解为变量；
```
<div id="app">
	<!-- 插值 -->
	<div>{{ counter }}</div>
	
	<!-- 插值，值不可变 -->
	<div v-once>{{ counter }}</div>
	
	<!-- 插值，值为html -->
	<div v-html="content"></div>
	
	<!-- attribute插值，需要使用v-bind指令 -->
	<div v-bind:class="className">Vue3.x</div>
	
	<!-- 插值运算 -->
	<div>{{ counter + 1 }}</div>
	
	<!-- 三元运算 -->
	<div>{{ true?'真':'假' }}</div>
	
	<!-- 字符串运算 -->
	<div>{{ str.split('@') }}</div>
	
	<!-- 插值不支持赋值，条件判断等语句 -->
	
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const App = {
		data() {
			return {
				counter: 100,
				content: '<span style="color:red;">Vue3.x</span>',
				className: 'blue',
				str: 'admin@admin.com'
			}
		}
	}
	
	const vm = Vue.createApp(App).mount('#app');
	// 应用实例可直接修改App对象中的数据
	vm.counter = 200;
	console.log(vm.counter);
</script>
```

## 四、指令和缩写方法
### 1. 指令：即在 HTML 中带有 v-前缀的特殊 attribute(属性)，值为单个 JS 表达式；
### 2. 指令可以动态设置参数
### 3. 指令值可以动态设置
```
<div id="app">
	<!-- 动态设置指令参数 -->
	<a v-bind:href="url">百度</a>
	<!-- v-bind 缩写方式 -->
	<a :href="url">百度</a>
	<!-- 动态设置指令值，动态指令值需为小写 -->
	<a v-bind:[attrname]="url">attribute 动态改变</a>
	<!-- v-bind 缩写方式 -->
	<a :[attrname]="url">attribute 动态改变</a>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const App = {
		data() {
			return {
				counter: 100,
				flag: true,
				url: 'https://www.baidu.com/',
				attrname: 'href'
			}
		}
	}
	
	// 全局对象.创建实例(App).挂载('#app')
	const vm = Vue.createApp(App).mount('#app');
</script>
```

## 五、事件和计算属性
### 1. 事件
- #### v-on 事件触发指令
- #### 如果触发的事件业务逻辑较多，那么行内表达式是不够的，需要触发固定方法；
```
<div id="app">
	<!-- 点击触发事件 -->
	<button v-on:click="counter++">累加1</button>
	<!-- v-on 缩写方式 -->
	<button @click="counter++">累加1</button>
	<!-- v-on 缩写方式，并动态设置指令值 -->
	<button @[key]="counter++">累加1</button>
	<!-- 触发固定方法 -->
	<button v-on:click="addCounter">累加1</button>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const App = {
		data() {
			return {
				counter: 100,
				key: 'click'
			}
		},
		methods: {
			addCounter(){
				this.counter++;
			},
		}
	}
	
	// 全局对象.创建实例(App).挂载('#app')
	const vm = Vue.createApp(App).mount('#app');
</script>
```

### 2. 计算属性
- #### 对于插值可以进行简单运算，可一旦过于复杂，则模版维护将变得异常困难；
- #### 如果插值内部需要进行各种运算，可使用方法进行返回
- #### 方法返回，每次加载页面都需要执行，没有缓存会在复杂的情况下影响性能，所以使用计算属性
- #### 计算属性和属性是一样的调用方式，而方法调用却需要括号，所以推荐用计算属性；
- #### 计算属性具有缓存，当值没有改变时，不会重新执行方法，而去使用缓存；
```
<div id="app">
	<!-- 两个插值拼装的简单运算 -->
	<div>{{ firstName + lastName }}</div>
	<!-- 方法返回 -->
	<div>{{ fullName() }}</div>
	<!-- 计算属性 -->
	<div>{{ allName }}</div>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const App = {
		data() {
			return {
				firstName: 'Mr.',
				lastName: 'Liu'
			}
		},
		// 计算属性
		computed: {
			allName(){
				return this.firstName + this.lastName;
			}
		},
		methods: {
			fullName(){
				return this.firstName + this.lastName;
			}
		}
	}
	
	// 全局对象.创建实例(App).挂载('#app')
	const vm = Vue.createApp(App).mount('#app');
</script>
```

## 六、Getter 和 Setter
### 1. 计算属性的两个方法：Getter 和 Setter
### 2. Getter：即取值，Setter：即赋值，计算属性默认执行的是 Getter 取值方法；
### 3. 当没有赋值操作，则执行一次 get，当有赋值操作，会以 get->set->get 三次执行；
```
<div id="app">
	<div>{{ fullName }}</div>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const App = {
		data() {
			return {
				firstName: 'Mr.',
				lastName: 'Liu'
			}
		},
		// 计算属性
		computed: {
			fullName:{
				// Getter
				get(){
					return this.firstName + this.lastName;
				},
				// Setter
				set(value){
					const str = value.split('.');
					this.firstName = str[0] + '.';
					this.lastName = str[1];
				}
			}
		}
	}
	
	// 全局对象.创建实例(App).挂载('#app')
	const vm = Vue.createApp(App).mount('#app');
	// 可以直接对计算属性赋值，执行 fullName下的Setter方法
	vm.fullName = 'Miss.Sun';
</script>
```

## 七、Class 和 Style 绑定
### 1. Class绑定
```
<div id="app">
	<!-- 字符串绑定 -->
	<div :class="className">Class 绑定效果</div>
	
	<!-- 对象绑定，支持多个 class 叠加，类名中有'-'需单引号括起来 -->
	<div :class="{'red': isRed, 'big-font': isBigfont}">Class 绑定效果</div>
	
	<!-- 变种对象绑定、判断 class 样式的布尔属性太多时使用 -->
	<div :class="classObject">Class 绑定效果</div>
	
	<!-- 计算属性绑定 -->
	<div :class="classComputed">Class 绑定效果</div>
	
	<!-- 数组绑定，添加就有效果，不添加就没有效果，也可以使用三元运算，来确定是否显示 -->
	<div :class="[isRed?redClass:'', bigFontClass]">Class 绑定效果</div>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const App = {
		data() {
			return {
				className: 'red',
				classObject:{
					'red': true,
					'big-font': true
				},
				isRed: true,
				redClass: 'red',
				bigFontClass: 'big-font',
			}
		},
		// 计算属性
		computed: {
			classComputed(){
				return {
					'red': true,
					'big-font': true
				}
			}
		},
	}
	
	// 全局对象.创建实例(App).挂载('#app')
	const vm = Vue.createApp(App).mount('#app');
</script>
```

### 2. Style绑定
```
<div id="app">
	<!-- 对象绑定，样式名中有'-'的，也可以用fontSize表达 -->
	<div :style="{'color': styleColor, 'font-size': fontSize + 'px'}">Style 绑定效果</div>
	
	<!-- 变种对象绑定 -->
	<div :style="styleObject">Style 绑定效果</div>
	
	<!-- 数组绑定 -->
	<div :style="[redStyle]">Style 绑定效果</div>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const App = {
		data() {
			return {
				styleColor: '#FF0000',
				fontSize: 30,
				styleObject: {
					'color': '#FF0000'
				},
				redStyle: {
					'color': '#FF0000'
				}
			}
		}
	}
	
	// 全局对象.创建实例(App).挂载('#app')
	const vm = Vue.createApp(App).mount('#app');
</script>
```

## 八、条件判断渲染
### 1. 通过布尔属性隐藏或显示Dom
### 2. 如果条件判断的区域是一大段代码，我们可以使用template来整合多个元素
### 3. 如果只是简单的切换显示或隐藏，可以使用 v-show，不支持template
### 4. 时时频繁切换用 v-show，反之 v-if；
```
<div id="app">
	<!-- v-if多分支判断 -->
	<span v-if="type === 'A'">A</span>
	<span v-else-if="type === 'B'">B</span>
	<span v-else-if="type === 'C'">C</span>
	<span v-else>404</span>
	
	<!-- 使用template来整合多个元素 -->
	<template v-if="flag">
		<h2>标题</h2>
		<h4>小标题</h4>
		<div>内容.....</div>
	</template>
	
	<!-- 使用v-show控制显示和隐藏 -->
	<div v-show="flag">显示/隐藏</div>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const App = {
		data() {
			return {
				type: 'B',
				flag: true
			}
		}
	}
	
	// 全局对象.创建实例(App).挂载('#app')
	const vm = Vue.createApp(App).mount('#app');
</script>
```

## 九、循环列表渲染
### 1. 循环列表指令 v-for
### 2. v-if,v-for不能在同一元素上使用
```
<div id="app">
	<!-- 遍历数组 -->
	<ul>
		<li v-for="(item, index) in arr">{{ index + 1 }}.{{ item.city }}</li>
	</ul>
	<!-- 遍历对象，value为值，name为键名，key为下标 -->
	<ul>
		<li v-for="(value, name, key) in obj">{{ key + 1 }}.{{ name }}:{{ value }}</li>
	</ul>
	<!-- 遍历整数，负数，小数无法遍历 -->
	<div v-for="n in 10">{{ n }}</div>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const App = {
		data() {
			return {
				arr:[
					{
						city:'北京'
					},
					{
						city:'上海'
					},
					{
						city:'广州'
					},
					{
						city:'深圳'
					}
				],
				obj:{
					'name': 'Lance',
					'sex': 'man',
					'age': 100
				}
			}
		}
	}
	
	// 全局对象.创建实例(App).挂载('#app')
	const vm = Vue.createApp(App).mount('#app');
</script>
```

## 十、key 和数组检测
### 1. 数组检测
- #### 为了更好的渲染视图，Vue 提供了如下的变更方法，执行后可渲染视图更新；	
	+ #### push()/pop()/shift()/unshift()/splice()/sort()/reverse();
	+ #### Vue 也提供了比如 filter()/concat()/slice()来替换并返回一个新数组；

```
<div id="app">
	<!-- 遍历数组 -->
	<ul>
		<li v-for="(item, index) in arr">{{ index + 1 }}.{{ item.city }}</li>
	</ul>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const App = {
		data() {
			return {
				arr:[
					{
						id: 1,
						city:'北京'
					},
					{
						id: 2,
						city:'上海'
					},
					{
						id: 3,
						city:'广州'
					},
					{
						id: 4,
						city:'深圳'
					}
				],
				countArr: [],
				concatArr: [],
				sliceArr: [] 
			}
		}
	}
	
	// 全局对象.创建实例(App).挂载('#app')
	const vm = Vue.createApp(App).mount('#app');
	
	// 对原数组进行了更改
	// 1. push() 在数组结尾添加一个元素
	vm.arr.push({
		id:5,
		city:'重庆'
	});
	
	// 2. pop() 删除数组的最后一个元素
	vm.arr.pop();
	
	// 3. shift() 删除数组的第一个元素
	vm.arr.shift();
	
	// 4. unshift() 在数组头部添加一个元素
	vm.arr.unshift({
		id:1,
		city:'北京'
	});
	
	// 5. splice() 从数组中添加/删除项目，然后返回被删除的项目
	// 参数1，添加/删除项目的位置，参数2，要删除的项目数量
	vm.arr.splice(3, 1);
	// 参数1，添加/删除项目的位置，参数2为0，则为添加，参数3，添加的内容
	vm.arr.splice(3, 0, {
		id:4,
		city: '深圳'
	});
	
	// 6. sort() 对数组排序
	vm.arr.sort();
	
	// 7. reverse() 对数组反向排序
	vm.arr.reverse();
	
	// 对原数组不进行更改，返回一个新数组
	// 1. filter() 对元素的每一项进行特定操作
	vm.countArr = vm.arr.filter((item)=>{
		if(item.city == '上海')
			return item;
	});
	
	// 2. concat() 连接两个数组
	vm.concatArr = vm.arr.concat([{
		id: 8,
		city: '重庆'
	}]);
	
	// 3. slice() 从数组中已有元素选定元素，组成一个新数组
	// 参数1，从哪开始，参数2，到哪截止
	vm.sliceArr = vm.concatArr.slice(2, 3);
	
	
</script>
```

### 2. key 问题
- #### 当选中复选框列表中的某几个项目，去执行数组的相关操作
- #### 例如：在数组结尾添加一个元素，重新渲染数组
- #### 这时会发现复选框的勾错位了；
- #### 解决办法：我们需要对每一个项目限定一个唯一的 id 指明来解决这个问题；
```
<div id="app">
	<button :click="add">添加</button>
	<ul>
		<li v-for="(item, index) in arr" :key="item.id">
			<input type="checkbox" />
			{{ index + 1 }}.{{ item.city }}
		</li>
	</ul>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const App = {
		data() {
			return {
				arr:[
					{
						id: 1,
						city:'北京'
					},
					{
						id: 2,
						city:'上海'
					},
					{
						id: 3,
						city:'广州'
					},
					{
						id: 4,
						city:'深圳'
					}
				]
			}
		},
		methods: {
			add(){
				this.arr.unshift({
					id:9,
					city:'香港'
				});
			}
		}
	}
	
	// 全局对象.创建实例(App).挂载('#app')
	const vm = Vue.createApp(App).mount('#app');
</script>
```

## 十一、事件处理能力
### 1. 事件处理
```
<div id="app">
	<!-- 默认对象传递 -->
	<button @click="addCounter">累加1</button>
	<!-- 显示对象传递 -->
	<button @click="addCounter1(5,$event)">累加1</button>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const App = {
		data() {
			return {
				counter: 0
			}
		},
		methods: {
			addCounter(){
				this.counter++;
				// 如果不传递参数，默认接受的事件对象名是 event；
				console.log(event);
			},
			addCounter1(num, e){
				this.counter += num;
				console.log(e);
			}
		}
	}
	
	// 全局对象.创建实例(App).挂载('#app')
	const vm = Vue.createApp(App).mount('#app');
</script>
```

### 2. 事件修饰符
- #### 事件修饰符
	+ #### .stop / .prevent / .capture / .self / .once / .passive
- #### 按键事件修饰符
	+ #### .enter / .tab /.delete / .esc / .space / .up / .down / .left / .right

```
<div id="app">
	<!-- 点击事件，只执行一次 -->
	<button @click.once="counter++">加一</button>
	
	<!-- 阻止冒泡，点击按钮不会促发div标签上的点击事件 -->
	<div @click="addCounter">
		<button @click.stop="addCounter">加一</button>
	</div>
	
	<!-- 修饰符串联，点击按钮不会促发div标签上的点击事件，并且只执行一次button上的点击事件 -->
	<div @click="addCounter">
		<button @click.stop.once="addCounter">加一</button>
	</div>
	
	<!-- 提交事件不再重载页面 -->
	<form @submit.prevent="onSubmit"></form>
	
	<!-- 按键事件修饰符（keyup），回车键按下触发 -->
	<button @keyup.enter="counter++">加一</button>
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const App = {
		data() {
			return {
				counter: 0
			}
		},
		methods: {
			addCounter() {
				this.counter++;
				// 如果不传递参数，默认接受的事件对象名是 event；
				console.log(event);
			},
			onSubmit() {
				
			}
		}
	}
	
	// 全局对象.创建实例(App).挂载('#app')
	const vm = Vue.createApp(App).mount('#app');
</script>
```

## 十二、表单输入绑定
### 1. 使用 v-model 指令可以实现 input、textarea 和 select 的双向绑定；
### 2. 它本质上是一个语法糖，负责监听用户输入数据并进行数据的更新操作；
### 3. v-model 对 input 和 textarea 处理时，会抛出 input 事件来时时更新；
### 4. v-model 在 checkbox 和 radio 处理时，会抛出 change 事件来时时更新；
### 5. v-model 在 select 处理时，会抛出 change 事件来时时更新；
```
<div id="app">
	<!-- 当我们在 input 文本框编辑的时候，div 中的内容会时时更新； -->
	<div>{{ message }}</div>
	<input type="text" placeholder="请输入内容" v-model="message">
	
	<!-- 单个复选框，可以直接绑定布尔值 true 和 false，自动识别是否勾选； -->
	<label for="checkbox">{{ checked }}</label>
	<input type="checkbox" id="checkbox" v-model="checked">
	
	<!-- 多个复选框，可以自动识别选中的值； -->
	<div>{{ checkedNames }}</div>
	
	<label for="a">Mr.Lee</label>
	<input type="checkbox" id="a" value="Mr.Lee" v-model="checkedNames">
	
	<label for="b">Mr.Wang</label>
	<input type="checkbox" id="b" value="Mr.Wang" v-model="checkedNames">
	
	<label for="c">Mr.Zhang</label>
	<input type="checkbox" id="c" value="Mr.Zhang" v-model="checkedNames">
	
	<!-- 单选按钮，绑定后和复选框实现一样的效果； -->
	<div>{{ gender }}</div>
	
	<label for="one">男</label>
	<input type="radio" id="one" value="男" v-model="gender">
	
	<label for="two">女</label>
	<input type="radio" id="two" value="女" v-model="gender">
	
	<!-- 下拉选择框，分单选和多选，基本同上； -->
	<div>{{ selected }}</div>
	
	<select v-model="selected">
		<option>北京</option>
		<option>上海</option>
		<option>广州</option>
		<option>深圳</option>
	</select>
	
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const App = {
		data() {
			return {
				message: 'Hello, Vue3.x~',
				checked: true,
				checkedNames: [],
				gender: '男',
				select: ''
			}
		},
		methods: {
		}
	}
	
	// 全局对象.创建实例(App).挂载('#app')
	const vm = Vue.createApp(App).mount('#app');
</script>
```

## 十三、值绑定和修饰符
### 1. 值绑定
- #### 单选框，下拉框均可以使用这种方法给 value 赋值；

```
<div id="app">
	<div>{{ checked }}</div>
	<input type="checkbox" id="checkbox" v-model="checked" :value="checked">
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const App = {
		data() {
			return {
				checked: true,
			}
		},
		methods: {
		}
	}
	
	// 全局对象.创建实例(App).挂载('#app')
	const vm = Vue.createApp(App).mount('#app');
</script>
```

### 2. 修饰符
```
<div id="app">
	<!-- .lazy 修饰符，如回车或失去焦点触发； -->
	<div>{{ message }}</div>
	<input type="text" v-model.lazy="message">
	
	<!-- .number 修饰符将输入的内容转换为 number 数值类型； -->
	<div>{{ typeof number }}</div>
	<input type="text" v-model.number="number" type="number">
	
	<!-- .trim 修饰符，清理掉内容左右的空格 -->
	<div>{{ message }}</div>
	<input type="text" v-model.trim="message">
</div>
<script src="https://unpkg.com/vue@next"></script>
<script>
	const App = {
		data() {
			return {
				message: 'Hello, Vue3.x~',
				number: 100
			}
		},
		methods: {
		}
	}
	
	// 全局对象.创建实例(App).挂载('#app')
	const vm = Vue.createApp(App).mount('#app');
</script>
```