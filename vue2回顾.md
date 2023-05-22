# 一丶Vue基本使用

### 1.el和data的两种写法

在我们刚开始学习vue的时候一般会这样来挂载元素

````js
<div id="app"></div>
<script src="../js/vue.js"></script>
<script>
  const vm = new Vue({
    el: '#app', // 通过这种方式将元素挂载到vue上,
    data: {
        
    }
  })  
  console.log(vm)
</script>
````

我们打印vm实例的时候， 可以发现该实例的原型上的一些方法

[![image-20220413145218330](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20220413145218330.png)]()

可以直接通过$mount方法来进行挂载

```js
<script>
  const vm = new Vue({
    //el: '#app', // 通过这种方式将元素挂载到vue上,
    data: {
        
    }
  })  
  //延迟一秒挂载元素
  setTimeout(() => {
    vm.$mount('#app')
  }, 1000)
</script>
```

data的函数式写法：

```js
<script>
  const vm = new Vue({
    el: '#app', // 通过这种方式将元素挂载到vue上,
    console.log(this) // 这里的this指向的是Vue实例对象
	/*
	 千万注意的是，这里data必须是个普通的函数，不能是箭头函数，如果是箭头函数的话 this指向的是window
	*/
    data: function(){
      	
    }
  })  
</script>
```

### 2.object.defineproperty()

// 回顾 object.defineproperty()

```js
<script>
 let number = 23
 let person = {
    name: '张三',
    sex: '男'
 }
 object.defineproperty(person, 'age', {
    //value: 18,
    enumerable: true, // 属性是否可以枚举, 默认值为false
    wirtable: true,//控制属性是否可以被修改，默认值是false
    configurable: true, // 控制属性是否可被删除, 默认值为false
    // 当有人读取person的age属性时，get函数(getter)会被调用，且返回值就是age的值
    get(){
      return number
    },
    // 当有人修改person的age属性时，set函数(setter)会被调用，且会收到修改的具体值
    set(value){
      console.log('有人修改了age属性，且值是', value)
      number = value
    }
 })
 console.log(person)
</script>
```

### 3.Vue中的点击事件

```js
<div id="app">
      <div class="box" >  
         // 给按钮添加点击事件
		// <button v-on:click="clickButton">click我</button>
        // 下面的简写形式 v-on: 可以直接替换成 '@'
          /*
            $event是个关键字，Vue把它解析成事件对象,
          	加上括号后可以进行传参 
          */
          <button @click="clickButton($event, 123)">click我</button>
      </div>
    </div>
    <script src="../js/vue.js"></script>
    <script>
      //创建vue 实例
      Vue.config.productionTip = false

      const vm = new Vue({
        el: '#app',
        data: {},
        methods: {
          // 点击按钮会发现打印两次信息
          clickButton(e,a) {
            console.log(e) // e 是事件对象
            console.log(e.target.innerText === 'click我') // true
            console.log(a) // 123
            console.log('点击了按钮')
          }
        }
      })
    </script>
```



### 4.Vue中的事件修饰符

1.阻止默认行为

```js
 <div id="app">
      <!-- 
       Vue中的事件修饰符: 
          1.prevent: 阻止默事件(常用);
          2.stop: 阻止事件冒泡(常用);
          3.once: 事件只触发一次(常用);
          4.capture: 使用事件的捕获模式
          5.self: 只有event.target是当前操作的元素时才触发事件
          6.passive: 事件的默认行为立即执行，无需等待事件回调执行完毕
      -->
      <a href="http://www.baidu.com" @click.prevent="showInfo">点击事件</a>
    </div>
    <script src="../js/vue.js"></script>
    <script>
      //创建vue 实例
      Vue.config.productionTip = false

      const vm = new Vue({
        el: '#app',
        data: {},
        methods: {
          showInfo() {
            //e.preventDefault() 阻止默认行为
            // e
            console.log('阻止了默认跳转')
          }
        }
      })
    </script>
```

2.阻止冒泡行为

```js
  <div id="app">
      <div class="box" @click="clickBox">
          // 就可以阻止冒泡行为
		<button @click.stop="clickButton">click我</button>
        //<button @click="clickButton">click我</button>
      </div>
    </div>
    <script src="../js/vue.js"></script>
    <script>
      //创建vue 实例
      Vue.config.productionTip = false

      const vm = new Vue({
        el: '#app',
        data: {},
        methods: {
          // 点击按钮会发现打印两次信息
          clickButton() {
            console.log('点击了按钮')
          },
          clickBox() {
            console.log('点击了div')
          }
        }
      })
    </script>
     
```

![image-20220414150011946](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20220414150011946.png)3.once修饰符

```js
// 按钮的事件只会触发一次，点击一次后就会失效
<button @click.once="clickButton">click我</button>
```



4.capture: 使用事件的捕获模式

```js
// js中事件正常情况下都是冒泡模式，从内向外
    <div id="app">
      <div class="div1" @click="showMsg(1)">
        div1
        <div class="div2" @click="showMsg(2)">div2</div>
      </div>
    </div>
    <script src="../js/vue.js"></script>
    <script>
      //创建vue 实例
      Vue.config.productionTip = false
      const vm = new Vue({
        el: '#app',
        data: {},
        methods: {
          showMsg(msg) {
            console.log(msg)
          }
        }
      })
    </script>
```

先打印的2再打印的1

![image-20220414151236543](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20220414151236543.png)

加上capture修饰符再试一次

```js
 <div class="div1" @click.capture="showMsg(1)">
     div1
    <div class="div2" @click="showMsg(2)">div2</div>
 </div>
```

就算先点击的是div2，也是先打印1在打印2

![image-20220414151550659](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20220414151550659.png)



5.self修饰符

```js
	<div id="app">
      <div class="box" @click="showInfo">
        <button @click="showInfo">提示信息</button>
      </div>
    </div>
    <script src="../js/vue.js"></script>
    <script>
      //创建vue 实例
      Vue.config.productionTip = false
      const vm = new Vue({
        el: '#app',
        data: {},
        methods: {
          showInfo(e) {
            // 打印的都是button按钮
            console.log(e.target)
          }
        }
      })
    </script>
```

![image-20220414152005547](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20220414152005547.png)

 加上self修饰符

```js
<div class="box" @click.self="showInfo">
   <button @click="showInfo">提示信息</button>
</div>
```

![image-20220414152205524](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20220414152205524.png)

**这是因为e.target并不是这个div，所以div身上的点击事件没有被触发**

**只有点击div的时候才会有信息打印**

![image-20220414152351325](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20220414152351325.png)

### 5.计算属性

```js
<div id="app">
      <!-- 
        计算属性：
          1. 定义：要用的属性不存在，要通过已有属性计算得来
          2. 原理：底层借助了Object.definepproperty方法提供的getter和setter。
          3. getter函数什么时候执行？
              (1) 初次读取的时候会执行
              (2) 当依赖的数据发生改变时会被再次调用
          4.优势：与methods实现相比，内部有缓存机制(复用), 效率高，调试方便
          5.备注：
              1.计算属性最终会出现在vm上，直接读取使用即可
              2.如果计算属性要被修改，那必须写set函数去响应修改，且set中要引起计算时依赖的数据发生变化   
       -->
      <div>
        <input type="text" v-model="firstName" /><br /><br />
        <input type="text" v-model="lastName" /><br /><br />
        全名: {{fullName}}
      </div>
    </div>
    <script src="../js/vue.js"></script>
    <script>
      //创建vue 实例
      Vue.config.productionTip = false
      const vm = new Vue({
        el: '#app',
        data: {
          firstName: '',
          lastName: ''
        },
        computed: {
           /* 
          fullName: {
            // get 有什么作用，当有人读取fullName时，get就会被调用，且返回值就作为fullName的值
            get() {
              return this.firstName + this.lastName
            },
            set(value) {
              this.firstName = value.split('-')[0]
              this.lastName = value.split('-')[1]
            }
          } 
           */
          // 计算属性的简写形式，计算属性一般只用得到getter，并不需要为它设置值,所以可以直接简写
          fullName(){
            // 此时就把这个函数当作了fullName的getter
            return this.firstName + this.lastName
          }
        },
        methods: {
          // fullName() {
          //   console.log('只要data里的数据改变，vue就会重新解析模板')
          //   return this.firstName + this.lastName
          // }
        }
      })
      </script>
```

### 6.watch监测

```js
 <body>
    <div id="app">
      <!-- 
        监视属性watch:
          1.当被监视的属性变化时，回调函数自动调用，进行相关操作
          2.监视的属性必须存在，才能进行监视！！！
          3.监视的两种写法：
             (1) new Vue时传入watch配置
             (2) 通过vm.$watch监视
        
        深度监视：
          (1) Vue中的watch默认不监测对象内部的值的改变
          (2) 配置deep: true可以监测对象内部值的改变
        备注：
          (1) Vue自身可以监测对象内部值的改变，但Vue提供的watch默认不可以！
          (2) 使用watch时根据数据的具体结构，决定是否采用深度监视
       -->
      今天天气很{{info}}
      <button @click="changeWether">切换</button>
      {{numbers.a}}
      <button @click="numbers.a++">a++</button>
      {{numbers.b}}
      <button @click="numbers.b++">b++</button>
    </div>
    <script src="../js/vue.js"></script>
    <script>
      //创建vue 实例
      Vue.config.productionTip = false
      const vm = new Vue({
        el: '#app',
        data: {
          isHot: true,
          numbers: {
            a: 1,
            b: 2
          }
        },
        computed: {
          info() {
            return this.isHot ? '炎热' : '凉爽'
          }
        },
        watch: {
          info: {
            immediate: true, // 监测的数据在改变之前，handle就执行一次
            handler(newValue, oldValue) {
              console.log('回调函数被调用')
              console.log(newValue)
              console.log(oldValue)
            }
          },
          isHot: {
            immediate: true, // 监测的数据在改变之前，handle就执行一次
            handler(newValue, oldValue) {
              console.log(newValue)
              console.log(oldValue)
            }
          },
          // 'numbers.a': {
          //   handle(newValue, oldValue) {
          //     console.log(newValue)
          //     console.log(oldValue)
          //   }
          // },
          // 'numbers.b': {
          //   handle(newValue, oldValue) {
          //     console.log(newValue)
          //     console.log(oldValue)
          //   }
          // }
          numbers: {
            deep: true, // 开启深度监测
            handler(newValue, oldValue) {
              console.log(newValue)
              console.log(oldValue)
            }
          },

          /*
           watch的简写形式, 如果只是监测最基本的属性，不要设置immediate 和 deep 就可以简写，如果需要配置就不可以简写
          */
          isHot(newValue, oldValue) {
            console.log(newValue)
            console.log(oldValue)
          }
        },
        methods: {
          changeWether() {
            this.isHot = !this.isHot
          }
        }
      })

      vm.$watch('isHot', {
        immediate: true, // 监测的数据在改变之前，handle就执行一次
        handler(newValue, oldValue) {
          console.log(newValue)
          console.log(oldValue)
        }
      })
    </script>
  </body>
```

### 7.computed和watch的区别

````js
  <body>
    <div id="app">
      <!-- 
        compute和watch之间的区别:
          1.computed能完成的功能，watch都可以完成
          2.watch能完成的功能，computed不一定能完成:例如watch能够完成异步的操作

          两个重要的小原则：
            1.所有被Vue管理的函数，最好写成普通函数，这样的this所指向的是vm或是组件对象，而不是window
            2.所有不被Vue管理的函数，(定时器的回调，ajax的回调等)最好写成箭头函数
              这样this的指向才是vm或是组件实例对象
       -->
      姓：<input type="text" v-model="firstName" /><br /><br />
      名：<input type="text" v-model="lastName" /><br /><br />
      全名：{{fullName}}
    </div>
    <script src="../js/vue.js"></script>
    <script>
      //创建vue 实例
      Vue.config.productionTip = false
      const vm = new Vue({
        el: '#app',
        data: {
          firstName: '张',
          lastName: '三',
          fullName: ''
        },
        computed: {
          fullName() {
            return this.firstName + ' ' + this.lastName
            /*
             延迟一秒显示，computed是无法实现的, 下面是错误的写法
              setTimeout(() => {
                return this.firstName + ' ' + this.lastName
              }, 1000)
            */
          }
        },
        watch: {
          //现在如果想让 fullName延迟一秒显示，
          firstName(val) {
            //this.fullName = val + ' ' + this.lastName
            setTimeout(() => {
              this.fullName = val + ' ' + this.lastName
            }, 1000)
          },
          lastName(val) {
            //this.fullName = this.firstName + ' ' + val
            setTimeout(() => {
              this.fullName = this.firstName + ' ' + val
            }, 1000)
          }
        }
      })
    </script>
  </body>
````

### 8.动态绑定样式

```js
 <body>
    <div id="app">
      <!-- 绑定class样式: 字符串写法，适用于: 样式的类名不确定，需要动态指定 -->
      <div class="basic" :class="mood" @click="changeMood">{{name}}</div>
      <!-- 绑定class样式: 数组写法，适用于: 要绑定的样式的个数不确定、名字也不确定 -->
      <div class="basic" :class="classArr" @click="changeMood">{{name}}</div>
      <!-- 绑定class样式: 对象写法，适用于: 要绑定的样式个数确定，名字也确定，但是要动态决定用不用 -->
      <div class="basic" :class="classObj" @click="changeMood">{{name}}</div>

      <!-- 动态绑定style样式 -->
      <div class="basic" :style="styleObj">{{name}}</div>
    </div>
    <script src="../js/vue.js"></script>
    <script>
      //创建vue 实例
      Vue.config.productionTip = false
      const vm = new Vue({
        el: '#app',
        data: {
          mood: 'normal',
          classArr: ['class1', 'class2', 'class3'],
          classObj: {
            class1: false,
            class2: true,
            class3: false
          },
          styleObj: {
            fontSize: '40px'
          }
        },
        computed: {},
        watch: {},
        methods: {
          changeMood() {}
        }
      })
    </script>
  </body>
```

### 9.列表渲染，以及key的作用原理

```js
<body>
    <div id="app">
      <!-- 面试题：react，vue中的key有什么作用? (key的内部原理)
      1. 虚拟DOM中的key的作用：
          key是虚拟DOM对象的标识，当状态中的数据发送变化时，Vue会根据【新数据】生成【新的虚拟DOM】
          随后Vue进行【新的虚拟DOM】与【旧虚拟DOM】的差异比较，比较规则如下：
      2.对比规则：
          (1). 旧虚拟DOM中找到了与新虚拟DOM相同的key:
              ①若虚拟DOM中的内容没变，直接使用之前生成的真实DOM
              ②若虚拟DOM中内容变了，则生成新的真实DOM，随后替换掉页面中之前的真实DOM

          (2). 旧虚拟DOM中未找到与新虚拟DOM相同的key
                就直接创建新的真实DOM，随后渲染到页面中
      
      3. 用index作为key可能会引发的问题:
          (1). 若对数据进行：逆序添加、逆序删除等破坏顺序操作：
                会产生没有必要的真实DOM更新 ==> 界面效果没有问题，但是效率低
          (2). 如果结构中还有包含输入类的DOM:
                会产生错误的DOM更新 ==> 界面会出现问题
      
      4. 开发中如何选择key?
          1.最好使用每条数据的唯一标识作为key，比如id，手机号，身份证号，学号 唯一标识
          2.如果不存在对数据的逆序添加、逆序删除等破坏顺序操作，仅有于渲染列用于展示，
            使用index作为key是没有什么问题的 -->
      <button @click="add">添加</button>
      <ul>
        <li v-for="item in person" :key="item.id">{{item.name}}<input type="text" /></li>
      </ul>
    </div>
    <script src="../js/vue.js"></script>
    <script>
      //创建vue 实例
      Vue.config.productionTip = false
      const vm = new Vue({
        el: '#app',
        data: {
          person: [
            {
              id: '001',
              name: '张三',
              age: 18
            },
            {
              id: '002',
              name: '李四',
              age: 19
            },
            {
              id: '003',
              name: '王五',
              age: 20
            }
          ]
        },
        computed: {},
        watch: {},
        methods: {
          add() {
            this.person.unshift({
              id: '004',
              name: '老刘',
              age: 21
            })
          }
        }
      })
    </script>
  </body>
```



### 10.Vue监视数据原理

```js
/* 
1.vue会监视data中所有层次的数据.
2.如何监测对象中的数据？
	通过setter实现监视，且要在new Vue时传入监测的数据。
	(1)对象中追加的属性，Vue默认不做响应式处理
	(2)如需给后添加的属性做响应式，请使用如下API
		Vue.set(target, propertyName/index, value)或
		vm.$set(target, propertyName/index, value)
3.如何监测数组中的数据?
	通过包裹数组更新元素的方法实现，本质就是做两件事：
	(1)调用原生对应的方法对数组进行更新。
	(2)重新解析模板，进行页面更新
4.在Vue修改数组中的某个元素一定要用如下方法:
	1.使用这些API: push(), pop(), shift(), unshift(), splice(), sort(), reverse()
	2.Vue.set() 或vm.$set()
	
特别注意： Vue.set和vm.$set不能给vm或者vm的根数据对象添加属性
*/

 <body>
    <div id="app">
      <h2>人员列表</h2>
      <button @click="editFirstPerson">修改第一个人的信息</button>
      <button @click="addNewProperty">给第一个人添加一个新属性</button>

      <span>{{ person[0].hobby }}</span>
      <ul>
        <li v-for="(item, index) in person" :key="index">{{item.name}} -- {{item.age}} -- 			  {{item.sex}}
         </li>
      </ul>
    </div>
    <script src="../js/vue.js"></script>
    <script>
      //创建vue 实例
      Vue.config.productionTip = false
      const vm = new Vue({
        el: '#app',
        data: {
          keyword: '',
          person: [
            {
              sex: '女',
              name: '马冬梅',
              age: 18
            },
            {
              sex: '女',
              name: '周冬雨',
              age: 19
            },
            {
              sex: '男',
              name: '周杰伦',
              age: 20
            },
            {
              sex: '男',
              name: '温兆伦',
              age: 40
            }
          ]
        },
        methods: {
          editFirstPerson() {
            // 如下这样改是没问题的, 但是这种写法太繁琐了
            // this.person[0].name = '孙悟空'
            // this.person[0].sex = '男'
            // this.person[0].age = 500

            // 但是下面这种方式 Vue是监测不到的
            // this.person[0] = {
            //   sex: '男',
            //   name: '孙悟空',
            //   age: 500
            // }

            // 通过splice 才能被监听到
            this.person.splice(0, 1, { sex: '男', name: '孙悟空', age: 500 })
            //还可以这样写
            //this.$set(this.person, 0, { sex: '男', name: '孙悟空', age: 500 })
          },
          addNewProperty() {
            // 这种写法是监测不到的，无法更新到页面的
            // this.person[0].hobby = '网上冲浪'

            this.$set(this.person[0], 'hobby', '网上冲浪')
          }
        }
      })
    </script>
  </body>
```

### 11.过滤器

```js
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <!-- 时间戳格式转换成标准格式 -->
    <div id="root">
      <p>当前时间: {{time}}</p>
      <p>使用computed: {{dateFormater_computed}}</p>
      <p>使用methods: {{dateFormater_method()}}</p>
      <p>使用过滤器: {{ time | dateFormater }}</p>
      <p>过滤器传参: {{ time | dateFormater('YYYY_MM_DD') }}</p>
      <p>过滤器串联: {{ time | dateFormater('YYYY_MM_DD') | mySlice }}</p>
    </div>
    <div id="root1">{{ msg | publicSlice(5) }}</div>
    <script src="../js/dayjs.min.js"></script>
    <script src="../js/vue.js"></script>
    <script>
      /*
       过滤器: 
        定义：对要显示的数据进行特定格式化再显示(适用于一些简单的逻辑)
        语法：
              1.注册过滤器: Vue.filter(name, callback) 或者 new Vue({ filters:{}})
              2.使用过滤器: {{ xxx | 过滤器名 }} 或者 v-bind:属性 = 'xxx | 过滤器名'
      
        备注：
            1.过滤器也可以接收额外参数、多个过滤器也可以串联
            2.并没有改变原本的数据，是产生新的对应的数据
        
        注意：在定义全局过滤的时候必须在new Vue之前就定义好!
      */
      Vue.filter("publicSlice", (value, num = 4) => {
        return value.slice(0, num);
      });
      const vm = new Vue({
        el: "#root",
        data: {
          time: 1652753091386,
        },
        computed: {
          dateFormater_computed() {
            return dayjs(this.time).format("YYYY年MM月DD日 HH:mm:ss");
          },
        },
        // 局部过滤器
        filters: {
          dateFormater(value, str = "YYYY年MM月DD日 HH:mm:ss") {
            // 这里的value就是默认传进来的 time
            return dayjs(value).format(str);
          },
          mySlice(value) {
            return value.slice(0, 4);
          },
        },
        methods: {
          dateFormater_method() {
            return dayjs(this.time).format("YYYY年MM月DD日 HH:mm:ss");
          },
        },
      });

      const vm1 = new Vue({
        el: "#root1",
        data: {
          msg: "helloWorld",
        },
      });
    </script>
  </body>
</html>

```

### 12.自定义指令

```js
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <!-- 
    需求一: 定义一个v-big指令，和v-text功能类似，但会把绑定的数值放大十倍
    需求二：定义一个v-fbind指令，和v-bind类似，但可以让其所绑定的input元素默认获取焦点
   -->
    <div id="root">
      <h2>当前n的值：<span v-text="n"></span></h2>
      <h2>放大十倍后n的值：<span v-big="n" class="text"></span></h2>
      <div class="box" v-big></div>
      <button @click="n++">n + 1</button>
      <hr />
      <input type="text" v-fbind="n" />
    </div>
    <script src="../js/vue.js"></script>
    <script>
      Vue.config.productionTip = false;
      // 全局自定义指令
      Vue.directive("fbind", {
        // 指令与元素成功绑定时调用
        bind(element, binding) {
          element.value = binding.value;
        },
        //指令所在元素被插入页面时调用
        inserted(element, binding) {
          element.focus();
        },
        // 指令所在模板被重新解析时调用
        update(element, binding) {
          element.value = binding.value;
        },
      });
      new Vue({
        el: "#root",
        data: {
          n: 1,
        },
        directives: {
          // big函数何时会被调用?
          // 1.指令和元素成功绑定时。 2.指令所在的模板被重新解析时。

          big(element, binding) {
            console.log(this, "big"); // 注意此处的this是window
            //console.log(element instanceof HTMLElement); // true
            //console.dir(element); // 真实的dom元素
            //console.log(binding.value);
            if (element.className === "text") {
              element.innerText = binding.value * 10;
            } else {
              element.style.height = "100px";
              element.style.width = "100px";
              element.style.border = "1px solid black";
            }
          },
          // fbind(element, binding) {
          //   element.value = binding.value;
          //   element.focus(); // 直接在这获取焦点是不起作用的，因为此时页面中并没有input框
          // },

          // 自定义指令的对象写法
          fbind: {
            // 指令与元素成功绑定时调用
            bind(element, binding) {
              element.value = binding.value;
            },
            //指令所在元素被插入页面时调用
            inserted(element, binding) {
              element.focus();
            },
            // 指令所在模板被重新解析时调用
            update(element, binding) {
              element.value = binding.value;
            },
          },
        },
      });
    </script>
  </body>
</html>

```

### 13.Vue生命周期钩子

![](C:\Users\hyf\Desktop\lifecycle.png)

**beforeCreate之前**: 初始化,生命周期、事件，但是数据代理还未开始 
**beforeCreate**：此时，无法通过vm访问到data中的数据、methods中的方法
**created之前**：初始化，数据检测、数据代理
**created: 此时**，可以通过vm访问到data中的数据、methods中配置的方法
**beforeMount之前**: 此阶段Vue开始解析模板，生成虚拟DOM(内存中),   ==页面还不能显示解析好的内容==

**beforeMount**:  此时  1. 页面呈现的是==未经Vue编译==的DOM结构  2.所有对DOM的操作，最终是不奏效的

**mounted之前**:  将内存中的虚拟DOM转为真实DOM插入页面

**mounted**: 此时 1.页面中呈现的是==经过Vue编译的DOM==  2. 对DOM的操作均有效(尽可能避免). 至此初始化过程结束，一般在此进行：开启定时器、发送网络请求、订阅消息、绑定自定义事件、等==初始化操作==

**beforeUpdate**： 此时，==数据是新的，但是页面是旧的==，即：页面尚未和数据保存同步。



### 14.非单文件组件

```js
 <body>
    <div id="root">
      <school></school>
      <hr />
      <student></student>
    </div>
    <div id="root2">
      <hello></hello>
    </div>
    <script src="../js/vue.js"></script>
    <script>
      // 创建school组件
      //el: '#root', // 组件定义时一定不要写el配置项，因为最终所有组件都要被一个vm管理，由vm决定服务于哪个容器。
      const school = Vue.extend({
        template: `
          <div>
            <h2>学校名称: {{schoolName}}</h2>
            <h2>学校地址: {{address}}</h2>
          </div>
        `,
        data() {
          return {
            schoolName: "尚硅谷",
            address: "北京昌平",
          };
        },
      });

      //创建学生组件
      const student = Vue.extend({
        template: `
          <div>
            <h2>学生姓名: {{studentName}}</h2>
            <h2>学生年龄: {{age}}</h2>
          </div>
        `,
        data() {
          return {
            studentName: "张三",
            age: 18,
          };
        },
      });
      console.log(student, "组件实例对象VueComponent");
      // 全局注册组件
      const hello = Vue.extend({
        template: `
          <div>
            <h1>{{msg}}</h1>
          </div>
        `,
        data() {
          return {
            msg: "hello",
          };
        },
      });
      Vue.component("hello", hello);
      // 创建vm
      new Vue({
        el: "#root",
        // 注册组件(局部注册)
        components: {
          school,
          student,
        },
      });
      new Vue({
        el: "#root2",
      });
    </script>
  </body>
```

### 15.VueComponent实例和Vue实例直接的关系

```js
// VueComponent.prototype.__proto__ === Vue.prototype
// 为什么要有这个关系?
//    让组件实例对象可以访问到Vue原型上的属性和方法

 <script>
      // 创建school组件
    
      const school = Vue.extend({
        template: `
          <div>
            <h2>学校名称: {{schoolName}}</h2>
            <h2>学校地址: {{address}}</h2>
            <button @click="showX">点我showX</button>
          </div>
        `,
        data() {
          return {
            schoolName: "尚硅谷",
            address: "北京昌平",
          };
        },
        methods: {
          showX() {
            console.log(this.x); // 99
          },
        },
      });

      Vue.prototype.x = 99;

      // 创建vm
      const vm = new Vue({
        el: "#root",
        // 注册组件(局部注册)
        components: {
          school,
        },
      });

      console.dir(Vue, "Vue构造函数");
      console.dir(vm, "vm实例对象");
      console.dir(school, "VueComponent对象");
     
    </script>
```

# 二丶VueCLI(脚手架)

### 1.安装脚手架

Node 版本要求

Vue CLI 4.x 需要 [Node.js](https://nodejs.org/) v8.9 或更高版本 (推荐 v10 以上)。你可以使用 [n](https://github.com/tj/n)，[nvm](https://github.com/creationix/nvm) 或 [nvm-windows](https://github.com/coreybutler/nvm-windows) 在同一台电脑中管理多个 Node 版本。

安装命令: npm install -g @vue/cli (全局安装)

安装完脚手架后就可以创建项目了:

​	vue create "项目名称"

### 2.脚手架环境下的目录结构

![image-20220602091749255](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20220602091749255.png)

### 2.main.js入口文件

```js
// src/main.js

//引入vue
import Vue from "vue";
// 引入根组件App.vue
import App from "./App.vue";

//关闭生产环境提示
Vue.config.productionTip = false;

// 创建Vue实例，模板挂载
new Vue({
  render: h => h(App),
}).$mount('#app')
```

这里和之前对比有一个区别的:

```js
// 这是之前的写法
new Vue({
  el: '#app',
  templete: `<App></App>`,
  components: { App },
})
//这是VueCLI下的写法，暂且先把 render: h => h(App) 放一放
new Vue({
  render: h => h(App),
}).$mount('#app')
```

==这里需要知道的是如果我们换成之前的写法浏览器会抛出一个错误==

![image-20220602092822767](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20220602092822767.png)

```txt
You are using the runtime-only build of Vue where the template compiler is not available.
您使用的是Vue的仅运行时版本(runtime-only版本)，其中模板编译器不可用。
Either pre-compile the templates into render functions, or use the compiler-included build.
要么将模板预编译为渲染函数(render)，要么使用包含编译器的构建。

分析一下报错的原因: 
	因为我们import Vue from 'vue' 引入的是一个精简版的vue，它身上是没有template模板解析功能的。我们引入的是
 vue.runtime.esm.js, （node_modules\vue\dist\vue.runtime.esm.js）

根据报错信息它给我们提供了两种方法，第一种就是使用render函数，第二种是引入包含编译器构建的vue。
先来试下第二种方式: import Vue from "vue/dist/vue"; 直接引入最基本的vue版本。
```

```js
//第一种方式render渲染函数:
render(createElement){
  //createElement是render函数的默认参数
  //return createElement('h1','你好啊') // 这种写法是将一个标签返回,如何想要渲染组件的话
   return createElement(App)
}
//简写：
render: h => h(App)
```

### 3.总结

```js
/*
 关于不同版本的Vue：
  1.vue.js与vue.runtime.xxx.js的区别:
   	(1).vue.js是完整版的Vue，包含：核心功能 + 模板解析器。
   	(2).vue.runtime.xxx.js是运行版的Vue，只包含：核心功能; 没有模板解析器。
  2.因为vue.runtime.xxx.js没有模板解析器,所有不能使用template配置项，需要使用
  	render函数接收到的createElement函数去指定具体内容.

*/
```



### 4.ref属性

```js
/* 
  ref属性：
  	1.被用来给元素或者子组件注册引用信息(id的替代者)
  	2.应用在html标签上获取的是真实的DOM元素,应用在组件标签上是组件的实例对象
  	3. 使用方式:
  		<h3 v-text="text" ref="h3"></h3>
    	<button ref="btn" @click="showDOM">点击获取DOM元素</button>
    	<School ref="school"></School>
*/
<template>
  <div id="app">
    <h3 v-text="text" ref="h3"></h3>
    <button ref="btn" @click="showDOM">点击获取DOM元素</button>
    <School ref="school"></School>
  </div>
</template>

<script>
import School from "./components/School.vue";
export default {
  name: "App",
  components: {
    School,
  },
  data() {
    return {
      text: "h3標簽",
    };
  },
  methods: {
    showDOM() {
      // 通过 vm.$refs 来获取dom元素
      console.log(this.$refs.h3); //真实的DOM元素
      console.log(this.$refs.btn); // 真实的DOM元素
      console.log(this.$refs.school); // 打印的是 School组件实例对象
    },
  },
};
</script>
```

### 5.prop父组件传子组件

```js
// 父组件中传值的方式
<template>
  <div id="app">
    <Student name="方明旺" sex="男" :age="18"></Student>
  </div>
</template>

// 子组件中接收的几种方式
<template>
  <div class="school">
    <h2>{{ msg }}</h2>
    <p>学生姓名: {{ name }}</p>
    <p>学生性别: {{ sex }}</p>
    <p>学生年龄: {{ age }}</p>
  </div>
</template>

<script>
export default {
  name: 'StudentComponent',
  /*
    props 组件间通信
  */
  //数组的写法
  //props: ["name", "sex", "age"],

  // 对象的写法，可以限制类型, 可以设置默认值，也可以限制是否必传
  props: {
    name: {
      type: String,
      default: '方明旺', // 默认值
      required: true // 是否必选
    },
    sex: {
      type: String
    },
    age: {
      type: Number
    }
  },
  data() {
    return {
      msg: '我是一名尚硅谷的学生'
    }
  }
}
</script>

/*
 特别注意的点：Vue是不推荐在子组件中修改父组件传入的prop值，会发生警告.一般是不可以修改的。
*/
```

### 6.Mixin 混入

功能：可以把多个组件共用的配置提取成一个混入对象.

```js
// 第一步定义混合
export const mixin = {
  methods: {
    showName() {
      alert(this.name)
    }
  }
}

export const globalMixin = {
  data() {
    return {
      global: '123'
    }
  }
}

//在组件中局部混合的使用
// 引入一个混合, 局部混入
import { mixin } from '../mixin'
export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: 'School',
  mixins: [mixin],
  data() {
    return {
      name: '尚硅谷',
      address: '北京'
    }
  }
}

// 在main.js中全局混入的使用
import { globalMixin } from './mixin'
Vue.mixin(globalMixin)
```

### 7. plugins 插件

功能： 用于增强Vue.

本质： 包含install方法的一个对象，install的第一个参数是Vue，第二个以后的参数是插件使用者传递的数据。

```js
定义插件：
  {}.install = function(Vue, optons){
         // install方法中的参数默认是 Vue实例

    // 全局过滤器
    Vue.filter('publicSlice', (value, num = 4) => {
      return value.slice(0, num)
    })

    // 定义全局指令
    Vue.directive('fbind', {
      // 指令与元素成功绑定时调用
      bind(element, binding) {
        element.value = binding.value
      },
      //指令所在元素被插入页面时调用
      inserted(element) {
        element.focus()
      },
      // 指令所在模板被重新解析时调用
      update(element, binding) {
        element.value = binding.value
      }
    })

    // 全局混入
    Vue.mixin({
      data() {
        return {
          x: 100,
          y: 200
        }
      }
    })

    // 给Vue 原型上添加一个方法
    Vue.prototype.demo = () => {
      alert('hello')
    }
  }

// 使用插件 Vue.use()
```

### 8.$emit 自定义事件,子组件传参给父组件

之前通过props可以事件父组件传参给子组件，其实props也可以实现子组件传参给父组件.

```js
// 父组件
<template>
  <div id="app">
    <Company :getCompanyName="getCompanyName"></Company>
  </div>
</template>
<script>
import User from './components/User.vue'
export default {
  name: 'App',
  components: {
    Company
  },
  data() {
    return {}
  },
  methods: {
    // 利用props 子组件传父组件
    getCompanyName(val) {
      console.log('proprs子传父', val)
    }
  },
}

// 子组件
<template>
  <div class="wrapper">
     <p>子组件中的数据{{ msg }}</p>
    <button @click="SonToFather">子组件传值给父组件</button>
  </div>
</template>
<script>
export default {
  name: 'Company',
  props: ['getCompanyName'],
  data() {
    return {
      msg: 'hahahaha'
    }
  },
  methods: {
    // 将父组件传入的函数再次调用并且把参数传入， 其实就是利用回调函数来进行传参。
    SonToFather() {
      this.getCompanyName(this.msg)
    }
  }
}
</script>

```

通过$emit来进行子组件传父组件参数。

```js
// 父组件

<!-- 监听子组件触发的自定义事件 这里监听的事件名称一定要和$emit 提交的事件名称一直 -->
    <User @customEvents="customEvents"></User>
    <!-- 监听$emit自定义事件的第二种方法 -->
    <!-- <User ref="user"></User> -->

 methods: {
    //常用写法:这里的val 是$emit触发事件时传入的参数
    customEvents(val, ...params) {
      console.log(val)
      console.log(params)
    }
  },
  mounted() {
    // 在mounted中 监听事件， 这种方式可以很灵活
    setTimeout(() => {
      this.$refs.user.$on('customEvents', this.customEvents)
    }, 3000)
  }

// 子组件
export default {
  name: 'Student',
  data() {
    return {
      name: '方明旺',
      sex: '男',
      age: 18
    }
  },
  methods: {
    SonToFather() {
      // $emit 的第一个参数是定义事件名称, 第二个参数是传入的数据
      this.$emit('customEvents', this.name, 77, 88, 99)
    },
    // $emit 发送自定义事件，我们也可以进行事件解绑
    Unbind() {
      this.$off('customEvents') // 这种写法解绑一个事件
      this.$off(['customEvents','demo']) // 解绑多个事件
      this.$off() // 不传参数，这种写法很暴力 解绑所有事件
    }
  }
}
```

### 9.全局事件总线(实现兄弟组件之间的通信)

```js
// main.js 入口文件中定义全局事件总线
import Vue from 'vue'
import App from './App.vue'
Vue.config.productionTip = false
// 定义全局事件总线
new Vue({
  render: (h) => h(App),
  beforeCreate() {
    // 在Vue原型上绑定一个$bus，并且让其为 vm实例对象，这样$bus身上$on, $emit, $off 都可以使用
    Vue.prototype.$bus = this
  }
}).$mount('#app')


// 兄弟组件A中，$emit 自定义事件
 methods: {
    TriggerEvent() {
      this.$bus.$emit('brotherTrigger', '666')
    }
  }

// 兄弟组件B中, $on 来绑定事件并且可以接收到参数
 mounted() {
    this.$bus.$on('brotherTrigger', (value) => {
      console.log(value)
      console.log(this)
      this.msg = value
    })
  },
  // 并且在最后销毁之前解绑事件
  beforeDestroy() {
    this.$bus.$off('brotherTrigger')
  }
```

### 10. 消息订阅与发布(pubsub)

1.一种组件通信的方式，适用于==任意组件间通信==。

2.使用步骤：

​		1.安装pubsub:  ==npm i pubsub-js==

​		2.引入:  ==import  pubsub from 'pubsub-js'==	

​		3.接收数据: A组件想接收数据，则在A组件订阅消息, **订阅的回调留在A组件自身**

```js
...
mounted(){
  this.pid = pubsub.subscribe('xxx', (Name, data) => {
  	// 第一个参数是消息名称,第二个参数是传输的数据
  })
}
```

​	4.提供数据: ``` pubsub.publish('xxx', 数据)```

​	5.最好在**beforeDestory**钩子中, 用```pubsub.unsubscribe(pid)```去==取消订阅==

### 11.$nextTick

1.语法： ```this.$nextTick(回调函数)```

2.作用: 在下一次DOM更新结束后执行其指定的回调。

3.什么时候用: 当改变数据后，要基于更新后的新DOM进行某些操作时，在$nextTick所指定的回调函数中执行。

### 12.vue脚手架配置代理

编写vue.config.js配置具体代理规则

```js
module.exports = defineConfig({
  transpileDependencies: true,
  lintOnSave: false,
  devServer: {
    port: 8877,
    // 开启代理服务器(方式一)
    // proxy: 'http://localhost:5000',

    //开启代理服务器(方式二)
    proxy: {
      '/test': {
        // 代理路径匹配，必须以/test为前缀的才会被转发
        target: 'http://localhost:5000',
        pathRewrite: { '^/test': '' }, // 匹配所有/test开头的路径然后替换成空字符串
        ws: true, // 用于支持webSocket
        changeOrigin: true
      }
    }
    /* 
     changeOrigin 设置为true, 服务器收到的请求头中的host为: localhost:5000
     changeOrigin 设置为false, 服务器收到的请求头中的host为: localhost:8080
     changeOrigin默认为true
    */
  }
})
```



### 13.插槽

1.插槽的作用: 让父组件可以向子组件指定的位置插入html结构，也是一种组件通信的方式，适用于父组件===>子组件。

2.分类：默认插槽、具名插槽、作用域插槽。



**1.默认插槽**

```js
// 父组件
/* 
 父组件在引用子组件时，写在子组件中的内容将全部替换掉子组件中的<slot></slot>标签
如果在引用子组件时没有往里面传任何内容，那么<slot></slot>就会显示里面的默认内容
*/
<template>
  <div id="app">
    <Category title="明星">
      <img src="./assets/chengguo.jpg" alt="" />
    </Category>
    <Category title="游戏">
      <ul>
        <li v-for="(item, index) in games" :key="index">{{ item }}</li>
      </ul>
    </Category>
    <Category title="电影">
      <video controls autoplay src="./assets/3255918cac525cddcc27bc2c7c23aaeb.mp4"></video>
    </Category>
  </div>
</template>

// 子组件 Category
<template>
  <div class="category">
    <h3>{{ title }}</h3>
    <slot>默认值，当组件没有被传入内容时出现</slot>
  </div>
</template>

```



**2.具名插槽**

```js
// 父组件
/*
  注意：具名插槽的使用方法是在内容标记上加了slot属性 slot="center"
  类似于给内容定义一个名称，将替换掉子组件身上指定名称的<slot></slot>标签
*/
<template>
  <div id="app">
    <Category title="明星">
      <img slot="center" src="./assets/chengguo.jpg" alt="" />
      <span slot="footer">具名插槽1</span>
    </Category>
    <Category title="游戏">
      <ul slot="center">
        <li v-for="(item, index) in games" :key="index">{{ item }}</li>
      </ul>
      <span slot="footer">具名插槽2</span>
    </Category>
    <Category title="电影">
      <video slot="center" controls autoplay src="./assets/3255918cac525cddcc27bc2c7c23aaeb.mp4"></video>
      <template v-slot:footer>
        <span>具名插槽3</span><br />
        <span>传入多个标签可以写成template</span>
      </template>
    </Category>
  </div>
</template>

// 子组件写法
/*
 父组件中传入slot="center"的内容将替换掉子组件中的<slot name="center"></slot>
 父组件中传入slot="footer"的内容将替换掉子组件中的<slot name="footer"></slot>
 同样具名插槽中也可以设置默认内容。
*/
<template>
  <div class="category">
    <h3>{{ title }}</h3>
    <slot name="center">默认值，当组件没有被传入内容时出现(center)</slot>
    <slot name="footer">默认值，当组件没有被传入内容时出现(footer)</slot>
  </div>
</template>
```



**3.作用域插槽**

```js
// 父组件

/*
 注意：在这个父组件data选项中并没有定义games属性，那么dom中v-for遍历的games哪里来的呢？
 其实games是从Category组件中通过作用域插槽传入到父组件。
 
 作用域插槽写法：
 	1. 必须要用template把需要传入的内容给包裹起来
 	2. 在接收参数的时候，slot-scope 切勿写成scoped，固定写法
 	3. 子组件中传入的参数在父组件中是以对象的形式接收的，同时支持ES6的解构赋值.
*/
<template>
  <div id="app">
    <Category title="游戏">
      <template slot-scope="{ games }">
        <!-- 作用域插槽，此时的obj就是Category传入的数据, 注意的是obj是一个对象，对象中有一个games属性，同时支持结果赋值的写法 -->
        <ul>
          <li v-for="(item, index) in games" :key="index">{{ item }}</li>
        </ul>
      </template>
    </Category>
    <Category title="游戏">
      <template slot-scope="{ games }">
        <ol>
          <li v-for="(item, index) in games" :key="index">{{ item }}</li>
        </ol>
      </template>
    </Category>
    <Category title="游戏">
      <template slot-scope="{ games }">
        <h4 v-for="(item, index) in games" :key="index">{{ item }}</h4>
      </template>
    </Category>
  </div>
</template>

<script>
import Category from './components/Category.vue'
export default {
  name: 'App',
  components: {
    Category
  },
  data() {
    return {}
  },
  methods: {}
}
</script>

// 子组件
/*
 传入的参数 :games="games"，通过slot插槽传入到父组件
 可以传多个参数 <slot :games="games" title="123123"></slot>
*/
<template>
  <div class="category">
    <h3>{{ title }}</h3>
    <slot :games="games"></slot>
  </div>
</template>

```





# 三丶 Vuex的使用

### 1.vuex是什么，如何理解vuex？

概念： 专门在Vue中实现集中式状态(数据)管理的一个Vue插件，对Vue应用中多个组件的共享状态进行集中式的管理(读/写)，也是一种组件间的通信方式，且适用于任意组件间的通信。

### 2.如何使用vuex，并且配置vuex

安装vuex,如果是vue2版本  `` npm i vuex@3``安装3版本的vuex，如果是vue3就可以直接``npm i vuex``

配置vuex，官方推荐src目录下新建一个store文件夹，./src/sotre/index.js

![image-20220823104459988](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20220823104459988.png)

### 

在main.js中如何引用vuex:

```js
import Vue from 'vue'
import App from './App.vue'
import store from './store'
Vue.config.productionTip = false
new Vue({
  render: (h) => h(App),
  // 引入vuex
  store,
  // 定义全局事件总线
  beforeCreate() {
    Vue.prototype.$bus = this
  }
}).$mount('#app')
```

### 3.配置vuex，vuex中的五大核心概念,

 State, Getters, Mutations, Actions, Module,其中mutations 和 actions 尤为关键.如下是官方文档的图:

![image-20220823105913002](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20220823105913002.png)

1.State:

什么是state，state是用来存取数据的，并且是一个对象类型，组件间的数据共享就是通过state，类似于组件中的data选项，

```js
import Vuex from 'vuex'
import Vue from 'vue'
Vue.use(Vuex)
//该文件用于创建vuex中最为核心的store
const state = {
  sum: 0,
  flag: true
  // 其他存储的数据...
}
const mutations = {
  
}
const getters = {
  
}
const actions = {
  
}
// 创建store
const store = new Vuex.Store({
  state,
  mutations,
  getters,
  actions
})
//导出
export default store

```



2.Getters：

getters理解起来很简单，就是对state中的数据进行一些处理和加工，类似于组件中的computed。

```js
const getters = {
  // 这里的参数state就是上面的state配置项，
  bigSum(state) {
  	return state.sum * 10
  }
}
```



3.Mutations:

mutations如官网的图所说，想要改变state中数据就必须通过mutations来修改, 它只仅限于同步数据更改，如果是通过http请求获取的数据就得使用Actions

```js
// 如何提交mutations？
 methods: {
     // 因为vuex实例store可以通this.$store来获取到，$store中的commit来提交mutations，
     /*
      第一个参数事件名称(字符串)，
      第二个参数是要传递的参数
     */
    increment() {
      this.$store.commit('INCREMENT', this.n)
    }
 }

// 相对应的在store/index.js中配置mutations
const mutations = {
  INCREMENT(state, payload) {
    //第一个参数state是如上的state配置项，第二个参数payload是组件中传入的数据.
    state.sum += payload // 直接对state中的数据进行修改
  }
}
```

4.Actions:

Action 类似于 mutation，不同在于：

- Action 提交的是 mutation，而不是直接变更状态。
- Action 可以包含任意异步操作

```js
// 如何提交actions ？
methods: {
     //和mutations类似，只不过不是commit而是dispatch
     /*
      第一个参数事件名称(字符串)，
      第二个参数是要传递的参数
     */
    increment() {
      this.$store.dispatch('incrementWait', this.n)
    }
 }

// 相对应的在store/index.js中配置actions
const actions = {
  incrementWait(context, value) {
     /*
      Action 函数接受一个与 store 实例具有相同方法和属性的 context 对象，因此你可以调用 				  context.commit 提交一个 mutation，或者通过 context.state 和 context.getters 来获取 state 		和 getters。第二个参数是组件传入的参数
      
      不同于mutations的是，它可以处理异步请求的数据。
     */
     $axios.get('http://xxxx-xxx',{params:{x: 1 }}).then(res => {
         // 提交mutations
         context.commit('increment', res.data)
     }) 
  },
  
  /*
  	还有一点要注意，在actions中可以链式调用, 比获取的数据要通过多次请求和很多的逻辑才能获取到，就可以分多	   个actions来获取
  */
  
  demo1(context, value) {
    // .... 经过层层逻辑
    $axios.get('http://xxxx-xxx',{params:{x: 1 }}).then(res => {
         // 提交mutations
         context.dispatch('demo2', res.data)
     })  
  },
  demo2(context, value) {
    // .... 经过层层逻辑  
    context.dispatch('demo3', value)
  },
  demo3(context,value){
    // 最终处理完数据再提交mutaions
    context.commit('increment', value)  
  }
}
```



### 4.vuex中的四个辅助函数的使用

1.**mapState方法**: 用于帮助我们映射``state``中的数据为计算属性

```js
computed: {
    //借助mapState生成的计算属性：sum,school,subject(对象写法)
    ...mapState({sum: 'sum',school: 'school',subject: 'subject'})
    
    //借助mapState生成的计算属性：sum,school,subject(数组写法)
    ...mapState(['sum','school','subject'])
}
```

2.**mapGetters方法**：用于帮助我们映射``getters``中的数据为计算属性

```js
computed: {
   //借助mapGetters生成的计算属性：bigSum(对象写法)
    ...mapGetters({bigSum:'bigSum'})
    
    //借助mapGetters生成的计算属性：bigSum(数组写法)
    ...mapGetters(['bigSum']) 
}
```

3.**mapMutations方法:**用于帮助我们生成与`mutations`对话的方法,即：包含`$store.commit(xxx)`的函数

```js
methods: {
    // 靠mapMutations生成的: increment, decrement（对象形式）
    ...mapMutations({increment: 'JIA', decrement: 'JIAN'})
    
    // 靠mapMutations生成: JIA, JIAN（数组形式
    ...mapMutations(['JIA', 'JIAN'])
}
```

4.**mapActions方法:**用于帮助我们生成与`actions`对话的方法,即：包含`$store.dispatch(xxx)`的函数

```js
methods: {
    // 靠mapActions生成的: increment, decrement（对象形式）
    ...mapActions({increment: 'JIA', decrement: 'JIAN'})
    
    // 靠mapActions生成: JIA, JIAN（数组形式
    ...mapActions(['JIA', 'JIAN'])
}
```



### 5.**模块化(modules)+ 命名空间**

  1.目的：让代码更好的维护，让多种数据分类更加明确

  2.修改`store.js`

  ```js
// Count.js
export default {
  namespaced: true,
  state: () => {
    return {
      sum: 0,
      flag: true
    }
  },
  mutations: {
    INCREMENT(state, payload) {
      state.sum += payload
    },
    DECREMENT(state, payload) {
      state.sum -= payload
    },
    INCREMENTODD(state, payload) {
      if (state.sum % 2) {
        state.sum += payload
      }
    }
  },
  getters: {
    bigSum(state) {
      return state.sum * 10
    }
  },
  actions: {
    increment_Wait(contxt, payload) {
      setTimeout(() => {
        contxt.commit('INCREMENT', payload)
      }, 500)
    }
  }
}

// store.js
import Vuex from 'vuex'
import Vue from 'vue'
import count from './Count'
import person from './Person'
Vue.use(Vuex)

// 创建store
const store = new Vuex.Store({
  modules: {
    count,
    person
  }
})
  ```

3.开启命名空间后,组件中读取state数据:

```js
// 方式一：自己直接读取
this.$store.state.personAbout.list
//方式二：借助mapState读取
...mapState('count', ['sum', 'flag'])
```



4.开启命名空间后，组件中读取getters数据:

```js
// 方式一： 自己直接读取
this.$store.getters['person/xxx']
//方式二：借助mapGetters读取
...mapGetters('person',['xxx'])
```

5.开启命名空间后，组件中调用dispatch

```js
//方式一： 自己直接dispatch
this.$store.dispatch('person/xxx', '数据')
// 方式二： 借助mapActions
...mapActions('person',['xxx'])
```

6.开启命名空间后，组件中调用commit:

```js
//方式一： 自己直接commit
this.$store.commit('person/xxx', '数据')
// 方式二： 借助mapMutations
...mapMutations('person',['xxx'])
```







# 四丶Vue-router使用

1.vue-router的理解

​	vue的一个插件库，专门用来实现**SPA应用**

2.**对SPA应用的理解**

 	 1.单页面web应用(single page web application,  SPA)
 	2. 整个应用只有一个完整的页面.
 	3. 点击页面中的导航链接不会刷新页面，只会做页面的局部刷新。
 	4. 数据需要通过ajax请求获取

### 3.多级路由

 1.配置路由规则，使用children配置项:

```js
routes: [
    {
        path: '/about',
        component: About,
    },
    {
        path: '/home',
        component: Home,
        children: [ // 通过children配置子路由
            {
                path: 'news', // 此处一定不要写: /message
                component: News
            },
            {
                path: 'message', // 此处一定不要写: /message
                component: News
            }
        ]
    }
]
```

2.跳转(要写完整路径):

```js
<router-link to="/home/news">News</router-link>
```

### 4.命名路由的使用

```js
// 在配置路由的时候可以配置name属性
export default new VueRouter({
  routes: [
    {
      path: '/about',
      component: About
    },
    {
      path: '/home',
      component: Home,
      children: [
        {
          path: 'news',
          name: 'news', // 配置name属性
          component: News
        },
        {
          path: 'message',
          name: 'message', // 配置name属性
          component: Message
        }
      ]
    }
  ]
})

// 配置了name属性过后，在路由跳转的时候可以直接通过name 跳转
this.$router.push({
    name: 'news'
})
```



### 5.路由传参

​	1.**query方式传参:  路由地址'?'后面紧跟着的就是参数,多个参数之前用'&'来分割** 

```js
<router-link active-class="active" to="/home/news?id=666&title=你好">News</router-link>
```

​	**2.url地址栏：**

![image-20221013154818919](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20221013154818919.png)



​	**3.除了router-link 还可以通过方法(method)的形式来主动路由跳转和传参**

```js
// 这种方式更常用，地址栏也会显示传入的参数. 
methods: {
    toNews() {
      // 通过方法的形式路由跳转
      this.$router.push({
        path: '/home/news',
        query: {
          id: 666,
          title: 'ni hao!'
        }
      })
    }
  }
```

**4.在指定的页面可以通过route去接收路由传入的参数**

```js
console.log(this.$route)
```

![image-20221013155520050](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20221013155520050.png)



**5.params传参方式**

```js
// 需要在路由配置的时候指定参数
  routes: [
    {
      path: '/about',
      component: About
    },
    {
      path: '/home',
      component: Home,
      children: [
        {
          path: 'news',
          name: 'news',
          component: News
        },
        {
          // :id和:title 都是属于占位符，路由params传参的时候需要
          path: 'message/:id/:title',
          name: 'message',
          component: Message
        }
      ]
    }
  ]

 // router-link: 把传递的数据直接跟在路由地址后面，通过 / 分隔
 <router-link  active-class="active" to="/home/message/666/你好啊">Message</router-link>

// 通过方法的形式传参
 methods: {
    toMessage() {
      this.$router.push({
        name: 'message', // 一定注意的是这里不是path，而是name，name的值是配置路由时指定的
        params: {
          id: 666,
          title: 'ni hao!'
        }
      })
    }
  }
```

![image-20221014160300605](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20221014160300605.png)



### 6.路由的props配置

作用: 让路由组件更方便的收到参数

```js
{
          // :id和:title 都是属于占位符，路由params传参的时候需要
          path: 'message/:id/:title',
          name: 'message',
          component: Message,
          /* props的第一种写法，值为对象，该对象中所有的key-value都会以props的形式传入到Message组件
           props: {
            a: 1,
            b: 2
           }
          */
          /*
            props的第二种写法, 值为布尔值.
            若布尔值为真，就会把该路由组件收到的所有params参数，以props的形式给Message组件
            props: true
           */

          /*
           props的第三种写法，函数写法  
           props值为函数，该函数返回的对象中每一组key-value都会通过props传给Message组件
          */
          props(route) {
            // 这里的route就是组件中的this.$route
            return {
              id: route.query.id,
              title: route.query.title
            }
          }
        }

```



### 7.this.$router.push和 replace的区别

编程时路由：我们可以不依赖router-link来进行路由跳转，想什么时候跳转都行

```js
// router.push: 我们把浏览器历史记录当作一个栈，每次push路由进去的时候，其实就是放在了栈顶。
this.$router.push({		
   name: 'message',
   params: {
       id:'',
       title: ''
   }
})
// 如果只想路由跳转，不传参的话可以这样写
this.$router.push('/message')


// router.replace: 和push不同的是，它是直接替换当前路由，如果开始就replace的话， 栈中始终只保留一条历史记录。

// 同上
this.$router.replace({		
   name: 'message',
   params: {
       id:'',
       title: ''
   }
})
this.$router.replace('/message')


// 大多数情况下用的是this.$router.push, 这样我们就可以用router身上的其他api

this.$router.forward() // 前进
this.$router.back() // 后退
this.$router.go(-1) // 如果是负数的话表示后退，正数的话表示前进 
```



### 8.缓存路由组件

1.作用： 让不展示的路由组件保持挂在，不被销毁

2.具体编码：

```js
// include这里放的是组件的名称Name,不是路由名称。这里表示的是只缓存 News组件
<keep-alive include='News'>
    <router-view></router-view>
</keep-alive>

// 但是如果想缓存多个组件可以这样写
<keep-alive :include="['News', 'Message']">
    <router-view></router-view>
</keep-alive>
```



### 9.两个新的生命周期钩子

​		1.作用：路由组件所独有的两个钩子，用于捕获路由组件的激活状态。

​		2.具体名字：

​				1.```activated```路由组件被激活时触发。

​				2.```deactivated```路由组件失活时触发。

​		3.**需要注意的是以上两个生命周期钩子，必须要加上keep-alive才会触发.**

​		4.结合之前的生命周期钩子，看下```activated```和``deactivated`的执行顺序.

​		看下控制台打印:

![image-20221102103842280](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20221102103842280.png)

​		需要注意的是加上了keep-alive的话 ``beforeDestroy``和``destroyed``是不会触发的。



###  10.路由导航守卫

​	作用： 对路由进行权限控制

​	分类：全局守卫、独享守卫、组件内守卫

#### 	1.全局前置路由守卫

​				—— 初始化的时候被调用、每次路由切换之前被调用

```js
router.beforeEach((to, from, next) => {
  console.log('前置路由守卫',to, from) // to: 表示要前往的路由， from：表示来自的路由
  // next() 可以理解为通行的标识（函数）,在前置路由守卫里必须要写next() 否则路由就会卡死，出现白屏的现象
  // 我们一般在全局前置路由守卫里写一些权限配置，判断每个用户是否可以通行。
})
```



#### 		2.全局后置路由守卫

​					—— 初始化的时候被调用、每次路由切换之后被调用

```js
router.afterEach((to, from) => {
    // 需要注意的是，在afterEach中是没有 next的，因为路由已经切换完了，没必要用到next了
    console.log('后置路由守卫',to,from) // 这里的to和from与前置路由守卫里的是一个意思。
    
})
```



#### 3.独享路由守卫

​				——是针对某个路由执行的钩子函数。

```js
export default new VueRouter({
  routes: [
    {
      path: '/about',
      component: About
    },
    {
      path: '/home',
      component: Home,
      children: [
        {
          path: 'news',
          name: 'news',
          component: News,
          beforeEnter: (to, from, next) => {
            // 给news组件单独加上了 前置路由守卫。
         	// 这里的to、from 和beforeEach是一样的，next()也是通行的标识。
          }
        },
        {
         
          path: 'message/:id/:title',
          name: 'message',
          component: Message,
          props(route) {
            // 这里的route就是组件中的this.$route
            return {
              id: route.query.id,
              title: route.query.title
            }
          }
        }
      ]
    }
  ]
})

```



#### 4.组件内守卫：

```js
// 进入守卫：通过路由规则，进入该组件时被调用
beforeRouteEnter(to, from ,next){
    
}

// 离开守卫：通过路由规则，离开组件时被调用
beforeRouteLeave(to, from , next){
    
}
```





### 11.路由的两种工作模式

   1.对于url来说，什么是hash值?  ——— #及后面的内容就是hash值。

2. hash值不会包含在HTTP请求中，即：hash值不会带给服务器。

3. hash模式：

   ​	1.地址栏会永远带着#，不美观。

   ​	2.若以后地址栏通过第三方手机app分享，若app校验严格，则地址栏会被标记为不合法

   ​	3.兼容性比较好

4. history模式:

    	1.地址干净，美观。

   ​	 2. 兼容性和hash模式相比略差

   ​	 3.应用部署上线时需要后端人员支持，解决刷新页面服务端404的问题。

   

   











### 12.`$attrs` 和  `$listeners` 用法



**为什么要用`$attrs` 和 `$listeners`?**



​	![无标题.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3dc4e317c78b4fd09967f0808608a34b~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

   通常A组件传值给B组件是通过props，B组件发送数据给A组件是通过$emit自定义事件, 但是A组件和C组件之间的数据传输怎么实现呢，

这个时候其实也可以用props和$emit 通过B组件来做中转一层层往上传也能实现，但是这样代码会显得很臃肿，第二种方法使用Vuex其实也能实现，但

是有点杀鸡用牛刀了，大材小用了。其实还有其他解决方案，和 provide 和 inject，这方法官方不推荐，最后$bus事件总线，如果项目接触的人多的话，

代码会很难维护。





````js
// App组件 
<template>
  <div id="app">
    // 此时A组件通过props传入bar、foo给B组件  
    <Child :foo="foo" :bar="bar"  />
  </div>
</template>

<script>
import Child from './components/Child.vue'
export default {
  name: 'App',
  components: { Child },
  data() {
    return {
      foo: 'foo',
      bar: 'bar'
    }
  },
}
</script>
````



```js
// Child组件
<template>
  <div>
    <p>foo:{{ foo }}</p>
	// 注意此时的$attrs，是 { "bar": "bar" },因为props中接收了foo，它会接收除去props以外的所有属性。
    <p>attrs: {{ $attrs }}</p>
  </div>
</template>

<script>
export default {
  name: 'Child',
  props: ['foo'],
  data() {
    return {}
  }
}
</script>
```



此时在Child组件中引入Grandson组件,看看Grandson组件是如何与App组件进行通信的

```js
// App组件
<template>
  <div id="app">
    <!-- //此处监听了两个事件，可以在B组件或者C组件中直接触发 -->
    <Child :foo="foo" :bar="bar" @upFoo="update" />
  </div>
</template>

<script>
import Child from './components/Child.vue'
export default {
  name: 'App',
  components: { Child },
  data() {
    return {
      foo: 'foo',
      bar: 'bar'
    }
  },
  methods: {
    update(val) {
      // 这里的val是Grandson组件发送过来的foooooooooooo
      this.foo = val
      console.log('update success')
    }
  }
}
</script


//Child组件
<template>
  <div>
    <p>foo:{{ foo }}</p>
    <p>attrs: {{ $attrs }}</p>
    <!--  v-bind="$attrs" -->
     // $listeners 作为中转来实现父组件和孙组件之间的通信。
    <Grandson v-bind="$attrs" v-on="$listeners" />
  </div>
</template>

<script>
import Grandson from './Grandson.vue'
export default {
  name: 'Child',
  props: ['foo'],
  components: {
    Grandson
  },
  data() {
    return {}
  }
}
</script>



// Grandson组件
<template>
  <div>
    <p>bar:{{ bar }}</p>
    <button @click="startUpFoo">我要更新foo</button>
  </div>
</template>

<script>
export default {
  name: 'Grandson',
  // 这里的props是Child组件 v-bind="$attrs"传入的
  props: ['bar'],
  data() {
    return {}
  },
  methods: {
    startUpFoo() {
      // 发送自定义事件
      this.$emit('upFoo', 'foooooooooooo')
      console.log('startUpFoo')
    }
  }
}
</script>
```



