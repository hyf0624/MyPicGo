# 一丶环境的准备

​	因为是封装Vue组件，所以直接利用Vue cli 创建项目即可，这样会便于打包，当然不用Vue cli也行，

那样的话就得自己一点点配置webpack了。

(1) 初始化vue项目

````js
vue create my-app
````

(2) 运行项目

````js
npm run serve
````

![image-20230227143847204](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20230227143847204.png)



# 二丶组件的封装

## 1.在src下新建package文件夹

因为我们可能会封装多个组件，所以在src下新建一个package文件夹来存放所有需要上传的组件。

![image-20230227144340927](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20230227144340927.png)



这里package里放了modal、button，两个组件的文件夹。

## 2.编写组件代码

这里我们就以modal组件为例，开始编写代码。

````js
// src/package/modal/src/index.vue 我们把源码都放在src下，

<template>
  <teleport to="body">
    <div class="modal" v-show="modelValue" :style="{ zIndex: zIndex }">
      <div class="modal-mask" @click="maskClose"></div>
      <div class="modal-content" :style="{ width: width }">
        <div class="modal-header">
          <slot name="header">{{ title }}</slot>
        </div>
        <div class="modal-body">
          <slot></slot>
        </div>
        <div class="modal-footer" v-if="!footerHide">
          <slot name="footer">
            <button class="modal-button" @click="closeModal('cancel')">取消</button>
            <button class="modal-button modal-button-primary" @click="sure">确定</button>
          </slot>
        </div>
        <div class="modal-close" @click="closeModal('close')"></div>
      </div>
    </div>
  </teleport>
</template>

<script setup lang="ts">
const props = defineProps({
  modelValue: {
    type: Boolean,
    default: false
  },
  footerHide: {
    type: Boolean,
    default: false
  },
  title: {
    type: String,
    default: ''
  },
  width: {
    type: String,
    default: '500px'
  },
  zIndex: {
    type: Number,
    default: 1000
  },
  maskClosables: {
    type: Boolean,
    default: false
  }
})
const emit = defineEmits(['ok', 'close', 'update:modelValue'])
const maskClose = () => {
  if (props.maskClosables) {
    closeModal('close')
  }
}
const closeModal = (type: string) => {
  emit('update:modelValue', false)
  emit('close', type)
}
const sure = () => {
  emit('update:modelValue', false)
  emit('ok')
}
</script>

<style scoped lang="less">
.modal {
  position: fixed;
  top: 0;
  left: 0;
  z-index: 1000;
  &-mask {
    width: 100vw;
    height: 100vh;
    background-color: rgba(#000000, 0.4);
  }
  &-content {
    width: 500px;
    position: absolute;
    top: 8vh;
    left: 50%;
    margin-left: -250px;
    background-color: #fff;
    border-radius: 8px;
    z-index: 1;
    font-size: 14px;
  }
  &-header {
    padding: 12px 16px;
    border-bottom: 1px solid #e4e7ed;
  }
  &-footer {
    padding: 12px 16px;
    border-top: 1px solid #e4e7ed;
    text-align: right;
  }
  &-body {
    padding: 16px;
  }
  &-close {
    position: absolute;
    top: 12px;
    right: 12px;
    width: 16px;
    height: 16px;
    cursor: pointer;
    &::before,
    &::after {
      content: '';
      display: block;
      position: absolute;
      left: 8px;
      top: 0;
      width: 1px;
      height: 16px;
      background-color: #999;
      border-radius: 0.5px;
      transform: rotate(-45deg);
      z-index: -1;
    }
    &::before {
      transform: rotate(45deg);
    }
    &:hover::before,
    &:hover::after {
      background-color: #444;
    }
  }
  &-button {
    line-height: 1em;
    font-size: 14px;
    padding: 8px 20px;
    border: 1px solid #dcdfe6;
    outline: none;
    display: inline-block;
    border-radius: 4px;
    cursor: pointer;
    background-color: #fff;
    transition: 0.1s;
    &:hover {
      color: #409eff;
      border-color: #c6e2ff;
      background-color: #ecf5ff;
    }
    & + & {
      margin-left: 10px;
    }

    &-primary {
      background-color: #2d8cf0;
      border-color: #2d8cf0;
      color: white;
      &:hover {
        background: #66b1ff;
        border-color: #66b1ff;
        color: #fff;
      }
    }
  }
}
</style>

````

## 3.使用Vue插件模式

这里我们模仿常用的组件库都是 Vue.use( )的方式来引用组件。这一步是封装的重点，用到Vue提供的一个公开的方法：install。这个方法会在你使用`Vue.use(plugin)`时被调用，这样使得我们的插件注册到全局，在子组件的任何地方都能使用。

在modal目录下新建一个index.ts：

```js
// 因为这里是Vue3，已经没有Vue.component了

import { App } from 'vue'
import hyfModal from './src/hyfModal.vue'

// 定义 install 方法， App 作为参数
hyfModal.install = (app: App): void => {
  app.component('hyfModal', hyfModal)
}

export default hyfModal

```

如果想单独发布这个modal组件的话就可以把入口文件设置为`package/modal/index.ts`下。但是我们都是同事发布多个组件的。得在package下再定义一个入口文件:

````js
// package/index.ts
import { App } from 'vue'
import hyfModal from './modal'

// 所有组件列表
const components = [hyfModal]

// 定义 install 方法， App 作为参数
const install = (app: App): void => {
  // 遍历注册所有组件
  components.map((component) => app.component(component.name, component))
}

export { hyfModal }

export default {
  install
}

````

在Vue.use( )的时候会自动调用install方法，我们只需要在install方法内注册组件就行了。





# 三丶组件打包

到这来为止，组件封装基本就完成了，当然组件封装成什么样子还得看自己的业务需求，接下来就需要将组件进行打包了。

修改我们的项目得package.json文件，配置打包命令：

````js
"package": "vue-cli-service build --target lib ./src/package/index.ts --name hyf-ant-design --dest hyf-ant-design"
````

![image-20230227150924863](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20230227150924863.png)

打包命令的解释：

- target lig 关键字 指定打包目录
- --name 打包后的文件名字
- --dest打包后的文件夹的名称

执行打包命令：

```js
npm run package
```

打包执行完后我们项目目录下就会多出一个`hyf-ant-design目录

![image-20230227151942972](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20230227151942972.png)



# 四丶发布到npm

## 1.初始化package.json

想要发布到npm仓库，我们还得在hyf-ant-design文件夹下初始化一个package.json文件，进入hyf-ant-design目录，执行命令:

```js
npm init -y
```

![image-20230227154541636](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20230227154541636.png)

由于这里我们只是测试，所以我不需要更改package.json文件，如果是生产的话，最好加上版本描述和版本号等等，其中name字段便是我们上传到npm仓库后的名称。



## 2.发布到npm仓库

(1) 注册账号

想要发布到npm仓库，就必须要有一个账号，先去npm官网注册一个账号，注意记住用户名、密码和邮箱，发布的时候可能会用到。

(2) 设置npm 源头

有些小伙伴可能本地的npm镜像源采用的是淘宝镜像源或者其它的，如果想要发布npm包，我们得吧我们得npm源切换为官方得源，命令如下：

````js
npm config set registry=https://registry.npmjs.org
````

(3) 添加npm用户

进入hyf-ant-design目录，添加npm用户，执行命令：

````js
npm adduser
````

(4) 如果已经有了npm 用户直接登录，执行命令:

```js
npm login  或者  npm login --auth-type=legacy
```

![image-20230227155341699](C:\Users\hyf\AppData\Roaming\Typora\typora-user-images\image-20230227155341699.png)



(5) 发布npm:

在hyf-ant-design目录下执行命令：

````js
npm publish
````

如果发布失败可能是名字重复了，改了名字即可，发布成功后，我们即可到npm光网上查看自己发布得npm包：