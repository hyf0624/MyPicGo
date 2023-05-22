### 一丶使用vite创建vue3项目

```js
## 创建工程
npm init vite-app <project-name>
## 进入工程目录
cd <project-name>
## 安装依赖
npm install
## 运行
npm run dev
```



### 二丶入口文件main.js

```js
## 先来回顾一下之前vue2 main.js的写法
import Vue from 'vue' // 引入Vue构造函数
import App from './App.vue' // 引入App根组件
new Vue({
    render: h => h(app)
}).$mount('#app')

## vue3中 main.js 写法
import { createApp } from 'vue' // 引入的不在是Vue构造函数了, 引入的是一个名为createApp的工厂函数
import App from './App.vue' // 引入App根组件
createApp(App).mount('#app')

/*
 分析代码: 
*/
// 创建应用实例对象—— app(类似于之前vue2中的vm, 但是app比vm更'轻')
const app = createApp(App)
console.log(app)
app.mount('#app')	

// 需要特别注意的是在vue3环境下是没有Vue构造函数的，也就是说 import Vue from 'vue' 是行不通的。
```



### 三丶setup入口

1.理解： Vue3中一个新的配置项，值是一个函数

2.setup是所有**Composition ApI**(组合API)的载体

3.组件中所用到的：数据、方法等等，均要配置在setup中，

4.setup函数的两种返回值:

​	1.若返回一个对象，则对象中的属性，方法在模板中均可直接使用。**(重点）**

​	2.若返回一个渲染函数：则可以自定义渲染内容。(了解)

5.注意点：

​	1，尽量不要与Vue2.x配置混用

​			Vue2.x配置(data, methods, computed...)中可以访问到setup中的属性、方法。

​			但在setup中不能访问到Vue2.x配置(data, methods, computed...)

​			如果有重名,setup优先。

	2. setup不能是一个asyunc函数，因为返回值不再return对象，而是promise，模板看不到return对象属性。
	3. **Vue3中的this不在指向vue实例了**

   4.不过在新版本的Vue3.2中，不需要写setup入口函数了。

​	

 ```js
// setup直接写在script标签上，也不用return对象了。
<script setup>

</script>
 ```



#### 1.ref函数

- 作用：定义一个响应式数据
- 语法：`const xxx = ref(initValue)`
  - 创建一个包含响应式数据的引用对象(reference对象)
  - JS中的操作数据：`xxx.value`
  - 模板中读取数据：不需要 `.value` ，直接: `<div>{{ xxx }}</div>`

-  备注:
  - 接收的数据可以是: 基本类型，也可以是对象类型。
  - 基本类型的数据：响应式依然依靠`Object.defineProperty()`的get和set完成的
  - 对象类型的数据：内部，“求助”了Vue3.0中的一个新函数——`reactive`函数。



```js
<template>
  <!-- Vue3组件中低端模板可以没有根标签 -->
  <!-- 在template 模板中读取数据的时候，不用.value读取 -->
  <span>信息：{{ name }} {{ age }}</span>
  <button @click="changeInfo">点击方法</button>
</template>

<script setup>
import { ref } from 'vue'
// 必须使用ref 进行加工的数据才算是响应式的数据，页面才能检测到数据更新
let name = ref('张三')
let age = ref(18)

//  方法
const changeInfo = () => {
  // 在setup中修改ref数据要用 .value的形式修改
  name.value = '李四'
  age.value = 25
}
</script>

<style></style>
```



#### 2.reactive函数

- 作用：定义一个==对象类型==的响应式数据(基本类型的数据不要用它，要用ref函数)
- 语法：`const 代理对象 = reactive(源对象)` 接收一个对象(或数组)，返回一个==代理对象（proxy对象）==
- reactive定义的响应式数据是深层次的。 
- 内部基于ES6 的 proxy实现的，通过代理对象操作源对象内部数据进行操作



#### 3.Vue3.0的响应式

- 实现原理：

  - 通过Proxy(代理): 拦截对象中任意属性，包括：属性值的读写、属性的添加、属性的删除等

  - 通过Reflect(反射)：对源对象的属性进行操作。

  - MDN文档中描述的Proxy与Reflect：

    - Proxy：https://es6.ruanyifeng.com/#docs/proxy

    - Reflect：https://es6.ruanyifeng.com/#docs/reflect

      ```js
       	// 源数据
            let person = {
              name: '孙悟空',
              age: 500
            }
            
            
            const proxy = new Proxy(person, {
              // 拦截读取操作
              get(target, proKey) {
                console.log(`有人读取了proxy身上的${proKey}属性`)
                return Reflect.get(target, proKey)
              },
      
              // 拦截修改和新增操作
              set(target, proKey, val) {
                console.log(`有人修改了proxy身上的${proKey}属性, 我要去更新界面了`)
                return Reflect.set(target, proKey, vla)
              },
      
              // 拦截删除操作
              deleteProperty(target, proKey) {
                console.log(`有人删除了proxy身上的${proKey}属性, 我要去更新界面了`)
                return Reflect.deleteProperty(target, proKey)
              }
            })
      ```

      



#### 4.Vue3.2中props 父组件传值给子组件

```js
// 父组件
<template>
  <Demo :name="name" />
</template>

<script setup>
// <script setup> 这样子组件就不用注册，引入了就可以直接在template中使用
import Demo from './components/Demo.vue'
import { ref } from 'vue'
const name = ref('')
</script>
<style scoped></style>

// 子组件
<template>
  <p>姓名：{{ props.name }}</p>
  <p>年龄：{{ info.age }}</p>
</template>

<script setup>
import { reactive, watch } from 'vue'
// import { defineProps } from 'vue'
// defineProps在<script setup>中自动可用，无需导入
// 需在.eslintrc.js文件中【globals】下配置【defineProps: true】
// 必须使用ref 进行加工的数据才算是响应式的数据，页面才能检测到数据更新
const props = defineProps({
  name: {
    type: String,
    default: ''
  }
})
const info = reactive({
  name: 1221,
  age: 88,
  sex: '男',
  a: {
    b: {
      c: 999
    }
  }
})
</script>

<style>
</style>
```





5.子组件发送数据给父组件(emit)

```js
// 父组件
<template>
  <Demo :name="name" @updateName="updateName" />
</template>

<script setup>
import Demo from './components/Demo.vue'
import { ref } from 'vue'
const name = ref('')
const updateName = (val) => {
  console.log('子组件传递给父组件的数据', val)
  name.value = val
}
</script>

<style scoped></style>


// 子组件
<template>
  <p>姓名：{{ props.name }}</p>
  <p>年龄：{{ info.age }}</p>
  <button @click="changeName">子组件emit发送事件</button>
</template>

<script setup>
import { reactive, watch } from 'vue'
// import { defineProps } from 'vue'
// defineEmits<script setup>中自动可用，无需导入
// 需在.eslintrc.js文件中【globals】下配置【defineEmits: true】
// 必须使用ref 进行加工的数据才算是响应式的数据，页面才能检测到数据更新
const props = defineProps({
  name: {
    type: String,
    default: ''
  }
})
const emit = defineEmits(['updateName'])

const info = reactive({
  name: 1221,
  age: 88,
  sex: '男',
  a: {
    b: {
      c: 999
    }
  }
})

const changeName = () => {
  emit('updateName', '子组件发送给父组件的数据')
}
</script>

<style></style>

```



#### 5.插槽(匿名插槽，具名插槽，作用域插槽)

```js
// 子组件
<template>
  <!-- 匿名插槽 -->
  <slot />
  <!-- 具名插槽 -->
  <slot name="title" />
  <!-- 作用域插槽 -->
  <slot name="footer" :scope="state" />
</template>

<script setup>
import { useSlots, reactive } from 'vue'
const state = reactive({
  name: '张三',
  age: '25岁'
})
// 使用插槽需要先引入useSlots,在调用创建一个实例对象
// slots.default 就是默认插槽(匿名插槽) slots.title就是具名插槽
const slots = useSlots()
// 匿名插槽使用情况
const defaultSlot = reactive(slots.default && slots.default().length)
console.log(defaultSlot) // 1
// 具名插槽使用情况
const titleSlot = reactive(slots.title && slots.title().length)
console.log(titleSlot) // 3
</script>

```





#### 6.计算属性computed

与vue2中的写法几乎一致

````js
<template>
  姓：<input type="text" v-model="person.firstName" />
  <br />
  名：<input type="text" v-model="person.lastName" />
  <br />
  全名：<input type="text" v-model="person.fullName" />
</template>

<script setup>
// 使用计算属性需要先引入
import { reactive, computed } from 'vue'
const person = reactive({
  firstName: '张',
  lastName: '三丰'
})
// 计算属性的简写形式，只考虑了getter
// person.fullName = computed(() => {
//   return person.firstName + '-' + person.lastName
// })

// 计算属性完整写法
person.fullName = computed({
  get() {
    return person.firstName + '-' + person.lastName
  },
  set(value) {
    console.log(value)
    const name = value.split('-')
    person.firstName = name[0]
    person.lastName = name[1]
  }
})
</script>

````



#### 7.watch监听

1.监听ref定义的数据

```js
<template>
  <h1>当前值：{{ num }}</h1>
  <button @click="num++">点我+1</button>
  <hr />
  <h1>当前信息：{{ msg }}</h1>
  <button @click="msg += '!'">修改信息</button>
</template>

<script setup>
// 引入watch
import { ref, watch } from 'vue'
const num = ref(0)
const msg = ref('xx')

// 情况一： 只监听ref定义的一个响应式数据
// watch(
//   num,
//   (newValue, oldValue) => {
//     console.log('num发生改变了', newValue, oldValue)
//   },
//   {
//     immediate: true
//   }
// )

// 情况二：监听多个ref定义的响应式数据
watch([num, msg], (newValue, oldValue) => {
  // 此时的newValue和oldValue都是数组
  console.log('num和msg都发生改变了', newValue, oldValue)
}, {
  immediate: true
})
</script>
```



![image-20221123105931242](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20221123105931242.png)



![image-20221123110044660](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20221123110044660.png)





2.监听reactive定义的数据

```js
<script setup>
 import {  reactive, watch } from 'vue'
 const info = reactive({
  age: 85,
  name: '摩根弗里曼',
  a: {
    b: {
      c: 2
    }
  }
})
 
// 情况三：监听reactive定义数据的全部属性
/*
 注意：1.此处无法正确的获取oldValue
      2. 强制开启了深度监视(deep配置无效)
*/
watch(info, (newValue, oldValue) => {
  console.log(newValue)
  console.log(oldValue)
})

// 情况四：监听reactive定义数据的某个属性
watch(
  () => info.age,
  (newValue, oldValue) => {
    console.log(newValue)
    console.log(oldValue)
  }
)

// 情况五：监听reactive定义数据的某些属性
watch([() => info.age, () => info.name], (newValue, oldValue) => {
  console.log(newValue, oldValue)
})

// 情况六：监听reactive定义数据的某个属性，并且该属性是个对象，那么就需要开启deep：true
watch(
  () => info.a,
  (newValue, oldValue) => {
    console.log(newValue)
    console.log(oldValue)
  },
  {
    deep: true
  }
)
</script>
```



#### 8.watchEffect函数

- watch的套路是：既要指明监听的属性，也要指明监听的回调函数
- watchEffect的套路是：不用指明监听哪个属性，监听的回调中用到了哪个属性，那就监听哪个属性
- watchEffect有点像computed：
  - 但computed注重的计算出来的值(回调函数的返回值), 所以必须要写返回值
  - 而watchEffect更注重的是过程(回调函数的函数体)，所以不用写返回值。

```js
// watchEffect所指定的回调中用到的数据只要发生变化，则重新执行回调。
watchEffect(() => {
  const x1 = num.value + 2
  const x2 = info.value.age + 3
  console.log('watchEffect配置执行了')
})
```



#### 9.自定义hooks

什么是hooks，类似于vue2中的`mixins` , 可以将setup中的Composition API 进行了封装。

自定义hooks的优势: 复用代码, 让setup中的逻辑更清楚易懂。

```js
// 一般在src下定义一个hooks目录，将需要封装的方法提出来。


// 先定义好hooks
import { reactive, onMounted, onBeforeUnmount } from 'vue'
export default function () {
  const point = reactive({
    x: 0,
    y: 0
  })
  function savePoint(event) {
    point.x = event.pageX
    point.y = event.pageY
  }
  // 挂载之后
  onMounted(() => {
    console.log('挂载完毕，给window添加事件监听')
    window.addEventListener('click', savePoint)
  })

  // 卸载之前
  onBeforeUnmount(() => {
    window.removeEventListener('click', savePoint)
  })

  return point
}


// 在组件中直接引入使用
<script setup>
import usePoint from '../hooks/usePoint'
const point = usePoint()
</script>
```



#### 11.toRef和toRefs的用法

- 作用：创建一个ref对象，其value值指向另一对象中的某个属性。
- 语法：`const name = toRef(person,'name')`
- 应用：将响应式对象中的某个属性单独提供给外部使用时。
- toRef和toRefs功能一致，但可以批量创建多个ref对象，语法: `toRefs(person)`

```js
<template>
  <h4>{{ info }}</h4>
  <h3>年龄: {{ age }}</h3>
  <h3>姓名: {{ name }}</h3>
  <h3>薪资: {{ job.j1.salary }}</h3>
  <button @click="changeInfo">修改info</button>
</template>

<script setup>
import { reactive, toRef, toRefs } from 'vue'
const info = reactive({
  age: 25,
  name: 'xx',
  job: {
    j1: {
      salary: 3
    }
  }
})
/**
 * toRef的作用是将对象里的指定属性变成ref对象
 * toRefs作用和toRef一样，不同的是可以接收多个数据
 */
// 这里toRef定义的age，name，salary 指向的其实还是info对象里的几个属性。
// const age = toRef(info, 'age')
// const name = toRef(info, 'name')
// const salary = toRef(info.job.j1, 'salary')
const { age, name, job } = toRefs(info)
console.log(age)
console.log(name)
console.log(job)
const changeInfo = () => {
  // age.value++
  // name.value++
  // salary.value++
  age.value++
  name.value += 'x'
  job.value.j1.salary++
}
</script>

```







#### 12.shallowReactive 与 shallowRef (不常用)

- shallowReactive: 只处理对象最外层属性的响应式（浅响应式）
- shallowRef: 只处理基本数据类型的响应式，不进行对象的响应式处理。
- 什么时候使用呢?
  - 如果有一个对象数据，结构比较深，但变化只是外层属性变化 ===> shallowReactive。
  - 如果有一个对象数据，后续功能不会修改对象中的属性，而是生新的对象来替换 ===>shallowRef。







#### 13.readonly 和 shallowReadonly

- readonly: 让一个响应式的数据变为只读的(深度 只读)
- shallowReadonly: 让一个响应式数据变为只读(浅 只读)
- 应用场景: 不希望数据被修改时





#### 14.toRaw和markRaw

- toRaw：
  - 作用：将一个由`reactive`生成的的响应式对象转为普通对象。
  - 使用场景：用于读取响应式对象对应的普通对象，对这个普通对象的所有操作，不会引起页面更新。
- markRaw: 
  - 标记一个对象，使其永远不会再成为响应式对象。
  - 应用场景：
    1. 有些值不应被设置未响应式的，例如复杂的第三方类库等。
    2. 当渲染具有不可变数据源的大列表时，跳过响应式转换可以提高性能。





#### 15.customRef

- 作用: 创建一个自定义的ref，并对其依赖项追踪和更新触发进行显示控制。

- 实现防抖效果

  ```js
  <template>
    <input type="text" v-model="message" />
    <h3>{{ message }}</h3>
  </template>
  
  <script setup>
  import { ref, customRef } from 'vue'
  
  // 自定义ref
  const myRef = (value) => {
    return customRef((track, trigger) => {
      let timer
      return {
        get() {
          console.log('读取了myRef中的数据了')
          track() // 追踪value的变化
          return value
        },
        set(val) {
          console.log('修改了myRef中的数据')
          clearTimeout(timer)
          timer = setTimeout(() => {
            // 延迟一秒再执行。
            value = val
            trigger() // 触发，通知vue重新解析模板
          }, 1000)
        }
      }
    })
  }
  //let message = ref('Hello World Vue3') // 使用vue提供的内置的ref
  let message = myRef('Hello World Vue3')
  </script>
  
  <style scoped></style>
  
  
  ```

  ​	



#### 16.provide与inject

 	作用：**实现祖与后代组件间通信**

​	 用法：父组件有一个`provide`选项来提供数据，后代组件有一个`inject`选项来开始使用这些数据。

​	 具体写法：

​			1.祖组件中：

```js
<template>
  <div class="app">
    <h3>我是App组件</h3>
    
    {{ car }}
    <Child />
  </div>
</template>

<script setup>
import Child from './components/Child.vue'
import { reactive, toRefs, provide } from 'vue'
const car = reactive({
  name: '奔驰',
  price: '50w'
})
// provide将数据抛出去，后代组件就能够使用父组件的数据
provide('car', car)
</script>

<style scoped>
.app {
  padding: 10px;
  background: gray;
}
</style>

```



​			2.后代组件中：

```js
<template>
  <div class="son">
    <h3>我是son孙组件</h3>
    {{ car }}
  </div>
</template>

<script setup>
import { inject } from 'vue'

const car = inject('car') // 接收祖组件的数据
console.log(car)
</script>

<style scoped>
.son {
  padding: 10px;
  background: orange;
}
</style>

```

​				







### 四丶Composition API的优势



**1.vue2中传统的 Options API存在的问题**

使用传统OptionsAPI中，新增或者修改一个需求, 就需要分别在data， methods，computed里修改。

**2.Compostion API的优势**

我们可以更加优雅的组织我们的代码，函数。让相关功能的代码更加有序的组织在一起。



### 五丶vue3中新的组件

 **1.Fragment**

- 在vue2中： 组件必须有一个根标签
- 在vue3中：组件可以没有根标签，内部将多个标签包含在一个Fragment虚拟元素中。
- 好处：减少标签层级，减小内存占用。





 **2.Teleport**

-  什么是Teleport?  `Teleport`是一种能够将我们的`组件html结构`移动到指定位置的技术。

  ```js
  // to 后面跟的是移动的位置，可以填写div的类名或者id名称，但是一般都是写body
  <teleport to='body'>
   <div v-if="isShow" class='mask'>
       <div class='dialog'>
       	<h3>弹窗</h3>
  		<button @click='isShow = false'>关闭弹窗</button>
       </div>
   </div>
  </teleport>
  ```

  

**3. Suspense**

- 等待异步组件时渲染一些额外内容，让应用有更好的用户体验

- 使用步骤：

  - 异步引入组件

    ```js
    import { defineAsyncComponent } from 'vue'
    // defineAsyncComponent 定义异步组件
    const Child = defineAsyncComponent(() => {
      return new Promise((reslove, reject) => {
        setTimeout(async () => {
          let com = await import('./components/Child.vue')
          reslove(com)
        }, 2000)
      })
    })
    ```

  - 使用`Suspense`包裹组件，并配置好`default`与`fallback`

    ```js
    <template>
      <div class="app">
        <h3>我是App组件</h3>
        <Suspense>
          <template v-slot:default>
            <Child />
          </template>
          <template v-slot:fallback>
            <h3>稍等，加载中...</h3>
          </template>
        </Suspense>
      </div>
    </template>
    ```

    





### **六丶其他**

#### **1.全局API的转移**

- Vue2.x有许多全局API和配置。

  - 例如： 注册全局组件，注册全局指令等。

    ```js
    // 注册全局组件
    Vue.component('MyButton', {
        data: () => {
            count: 0
        },
        template: `<button @click='count++'>Clicked {{ count }} times.</button>`
    })
    
    
    // 注册全局指令
    Vue.directive('focus', {
        inserted: el => el.focus()
    })
    ```

- Vue3中对这些API做出了调整：

  - 将全局的API，即：`Vue.xxx`调整到应用实例 `app`上。

    ​	

    | 2.x 全局API ( Vue )      | 3.x 实例 API ( `app` )      |
    | :----------------------- | --------------------------- |
    | Vue.config.xxxx          | app.config.xxxx             |
    | Vue.config.productionTip | 移除                        |
    | Vue.component            | app.component               |
    | Vue.directive            | app.directive               |
    | Vue.mixin                | app.mixin                   |
    | Vue.use                  | app.use                     |
    | Vue.prototype            | app.config.globalProperties |

    



#### **2.其他改变**

- data选项应始终被声明为一个函数。

- 过渡类名的更改：

  -  Vue2.x写法

    ```js
    .v-enter,
    .v-leave-to{
        opacity: 0;
    }
    
    .v-leave,
    .v-enter-to{
        opacity: 1;
    }
    ```

  - Vue3.x写法

    ```
    .v-enter-from,
    .v-leave-to{
    	opacity: 0;
    }
    
    .v-leave-from,
    .v-enter-to{
    	opacity: 1;
    }
    ```

- **移除**了keyCode作为v-on的修饰符，同时也不再支持`config.keyCodes`

- **移除**`v-on.native`修饰符

  - 父组件中的绑定事件

    ```js
    <my-component 
      v-on:colse="handleComponentEvent"
      v-on:click="handleNativeClickEvent"
    />
    ```

  - 子组件中声明自定义事件

    ```js
    <script>
      export default {
      	emits: ['close']
      }
    </script>
    ```

- **移除了过滤器（filters）**

  虽然这看起来很方便，但它需要一个自定义语法，打破大括号内表达式是“只是 JavaScript”的假设，这不仅有学习成本，而且有实现成本

