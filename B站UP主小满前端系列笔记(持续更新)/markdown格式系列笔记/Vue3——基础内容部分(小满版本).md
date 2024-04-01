# Vue3+vite+pinia+实战+源码项目(Vue3部分笔记)

> ## 作者：小余同学
>
> 此笔记有借鉴部分小满的CSDN and 掘金
>
> 如果你觉得笔记不错的话，可以在GitHub帮作者点个Star吗？这对作者是极大的鼓励和动力，每过一段时间可以上去看看有没有新的优质笔记产出，都是原创且最新的的哦，PDF版本还有markdown版本都会进行上传，如果PDF的没有上传就是说明内容太大传输不了，但可以自行将现有的markdown文档进行转换
>
> 此笔记会**持续更新**(小满出新则动态更新)，平时会根据水友的意见进行修改其中的细节错误，**关注我在GitHub上面这个项目可以随时获取到最新的笔记**
>
> GitHub地址：[2002小余 (github.com)](https://github.com/2002XiaoYu)
>
> 小满的QQ群：**855139333**	小满的微信：**a1195566313**(想加入微信群请加小满，让小满拉你)
>
> 欢迎你加入小满的群聊和小满一起探讨技术上的问题，一个人只能闭门造车，一起探讨难点才能走得更远(记得视频多刷刷弹幕)
>
> 小余祝你学习愉快，未来可期~

[TOC]

# 第一章 课程导读

- 这章节是小满的自我介绍，还有介绍学习课程前需要的前置技术栈，祝你学习路程愉快
- 然后就是没事的话**多发弹幕**，可以将你在小满视频中遇到的问题的解决方法发在弹幕中，给后来者提供一些道路，学习的氛围也会更好

# 第二章 — 构建 vite 项目

[vite官方文档](https://cn.vitejs.dev/guide/)

1. 使用vite初始化一个项目(npm、yarn)

```javascript
npm init vite@latest
```

```js
yarn create vite
//或者使用pnpm
pnpm create vite
```

2. y继续，然后给项目起个名字，这里我的名字是vite-app

3. 注意：在哪里打开终端，你的项目就建立在哪里

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221001030134810.png" alt="image-20221001030134810" style="zoom:50%;" />

```
安装依赖：npm install
依赖安装好了之后就会放到node_modules这个文件夹下
启动项目：npm run dev
```

第二天之后启动项目可能会报错，说你没有权限

解决方法在下方链接里：

https://blog.csdn.net/weixin_37861326/article/details/104295379

#### package json 命令解析

```json
{
  "scripts": {
    "dev": "vite", // 启动开发服务器，别名：`vite dev`，`vite serve`
    "build": "vite build", // 为生产环境构建产物
    "preview": "vite preview" // 本地预览生产构建产物
  }
}
```

### 安装Vue cli脚手架

```json
安装：npm install @vue/cli -g
检查安装：Vue -V
```

![image-20221001031307642](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221001031307642.png)

![image-20221001031601936](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221001031601936.png)

> 使用脚手架
>
> vue create project(命令行)

# Vite 目录

`public` 下面的不会被编译 可以存放静态资源

`assets` 下面可以存放可编译的静态资源

`components` 下面用来存放我们的组件

`App.vue` 是全局组件

`main ts` 全局的 ts 文件

**index.html 非常重要的入口文件 （webpack，rollup 他们的入口文件都是 enrty input 是一个 js 文件 而 Vite 的入口文件是一个 html 文件，他刚开始不会编译这些 js 文件 只有当你用到的时候 如 script src="xxxxx.js" 会发起一个请求被 vite 拦截这时候才会解析 js 文件）**

vite config ts 这是 vite 的配置文件具体配置项

VsCode Vue3 插件推荐 Vue Language Features (Volar)

## SFC 语法规范

```
*.vue` 件都由三种类型的顶层语法块所组成：`<template>`、`<script>`、`<style>
```

### `<template>`

- 每个 `*.vue` 文件最多可同时包含一个顶层 `<template>` 块。
- 其中的内容会被提取出来并传递给 `@vue/compiler-dom`，预编译为 JavaScript 的渲染函数，并附属到导出的组件上作为其 `render` 选项。

------

### `<script>`

- 每一个 `*.vue` 文件可以有多个 `<script>` 块
- 该脚本将作为 ES Module 来执行。
- 其**默认导出**的内容应该是 Vue 组件选项对象，它要么是一个普通的对象，要么是 [defineComponent](https://v3.cn.vuejs.org/api/global-api.html#definecomponent) 的返回值。

------

### `<script setup>`

- 每个 `*.vue` 文件最多只能有一个 `<script setup>` 块 (不包括常规的 `<script>`)
- 该脚本会被预处理并作为组件的 `setup()` 函数使用，也就是说它会在**每个组件实例**中执行。`<script setup>` 的顶层绑定会自动暴露给模板。更多详情请查看 [ 文档](https://v3.cn.vuejs.org/api/sfc-script-setup)。

------

### `<style>`

- 一个 `*.vue` 文件可以包含多个 `<style>` 标签。
- `<style>` 标签可以通过 `scoped` 或 `module` attribute (更多详情请查看 [SFC 样式特性](https://v3.cn.vuejs.org/api/sfc-style)) 将样式封装在当前组件内。多个不同封装模式的 `<style>` 标签可以在同一个组件中混

## NVM 介绍

NVM 全称 [node](https://so.csdn.net/so/search?q=node&spm=1001.2101.3001.7020).js version management ，专门针对 node 版本进行管理的工具，通过它可以安装和切换不同版本的 node.js

`GitHub地址`:https://github.com/coreybutler/nvm-windows/releases

### Nvm 常用命令操作

|      nvm list      |   查看现在所有安装的 node 版本   |
| :----------------: | :------------------------------: |
| nvm list available |    查看 nodejs 官方的所有版本    |
|    nvm install     | （版本号）下载对应的 node 版本号 |
|      nvm use       |          切换 node 版本          |

### nrm 介绍

nrm 是一个 npm 源管理器，允许你快速地在 npm 源间切换。(其实就是镜像，你可以安淘宝镜像啥的)

1. 安装

   ```
   npm install -g nrm
   ```

2. npm ls **查看可选源 星号代表当前使用源**

3. nrm use （源）切换源

4. nrm add （名称）（源）

5. 测试速度 nrm test npm

### 替代nrm包的新一代包：xmzs

> 没错，这个xmzs就是包名，由此教程的作者小满zs编写的包。
>
> 在正式介绍这个包之前，我们来将他与nrm这个包进行一个对比：xmzs 是一个基于 Node.js 的命令行工具，它提供了与 nrm 类似的功能，可以方便地切换不同的 npm registry。虽然它和 nrm 有一些相似之处，但是两者有一些不同之处：
>
> 1. 功能不完全相同：xmzs 和 nrm 提供的功能略有不同。nrm 除了切换 registry 外，还提供了列出当前可用 registry 和测试 registry 响应时间的功能，而 xmzs 提供了批量修改 registry、查看当前 registry 等功能。
> 2. 社区支持：nrm 是一个较为流行的开源工具，它已经得到了广泛的社区支持，并且在维护更新方面也比较活跃。xmzs 相对来说较为新(因为刚写的)，目前社区支持还不如 nrm 火热。
> 3. 使用体验：xmzs 相对来说使用体验较为简单和便捷(当你安装下来的时候，会直接附带了各种镜像源能够直接切换，切换的手感也更加丝滑)，但是 nrm 在命令行交互和使用习惯方面已经比较成熟，也能够满足大多数用户的需求。

- 地址：[xmzs - npm (npmjs.com)](https://www.npmjs.com/package/xmzs)

- 使用说明：(他的命令是`mmp`，一个很社会的命令)

  1. 安装：

     ```javascript
     npm i xmzs -g
     ```

  2. ##### mmp ls 查看目前源：

     | npm       | https://registry.npmjs.org/            |
     | --------- | -------------------------------------- |
     | yarn      | https://registry.yarnpkg.com/          |
     | tencent   | https://mirrors.cloud.tencent.com/npm/ |
     | cnpm      | https://r.cnpmjs.org/                  |
     | taobao    | https://registry.npmmirror.com/        |
     | npmMirror | https://skimdb.npmjs.com/registry/     |

  3. 用法详解表格：

     | mmp命令集   | 作用                                       |
     | ----------- | ------------------------------------------ |
     | mmp use     | 切换源：选择你要切换的源                   |
     | mmp current | 查看当前源：当前源: npm                    |
     | mmp add     | 添加源：1.输入添加的地址 2.输入源地址      |
     | mmp ping    | 测试源：? 请选择镜像 cnpm 响应时长: 1635ms |
     | mmp delete  | 删除自定义源：add添加的源都可以删除        |
     | mmp rename  | 删除自定义源：add添加的源都可以删除        |
     | mmp edit    | 编辑自定义镜像地址                         |

  4. 下载动画(MMP图效)：

     > npm版本6.14.15能够看到动画，如果升级到7.1.2的npm及以上就无法看到MMP动画的显示

![image-20230421173905841](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\React笔记(coderwhy)\React-images\image-20230421173905841.png)

## 第三章 — 认识目录与SFC

| public         | 静态文件夹，通常用来存放一些无需编译的东西，比如图片啥的 |
| -------------- | -------------------------------------------------------- |
| src.assets     | 可编译，用来放图片的                                     |
| src.components | 组件存放的地方                                           |
| src.App.vue    | 全局组件                                                 |
| src.main.js    | 用来放公共API的                                          |
| src.index.html | 入口文件                                                 |
| package.json   | 配置一些命令还有依赖包的地方                             |
| tsconfig.json  | 配置文件的，比如严格模式、语法之类的                     |
| vite.config.ts | vite的配置文件                                           |

> 对App.vue的认识
>
> - template只能写一个
> - setup也只能写一个(写多了报错)

### npm run dev全过程

- vite启动项在package里面，但是不能直接在终端输入vite，因为我们没有在环境变量中配置过vite这个变量，没办法执行。

- npm run dev之所以可以是因为他使用了一种软链接的方式，从源码中可以看到当安装后vite地址会被软链接提升到.bin文件夹中。执行的时候程序就先从这里开始找的

- 这个.bin里有三个关于vite的文件

  | vite     | linux系统                      |
  | -------- | ------------------------------ |
  | vite.cmd | windows系统                    |
  | vite.ps1 | 跨平台的，Linux和windows都可以 |

# 第四章 — 模板语法 && Vue指令(代号-伊丽莎白)

> 这个代号为什么叫伊丽莎白我也不知道，但小满说起这个那就这个啦

| data()方法  | 返回组件所需要的数据                                         |
| ----------- | ------------------------------------------------------------ |
| methods属性 | 定义组件所需要的方法函数                                     |
| v-if        | Vue提供的条件渲染功能，指定变量为true则渲染这个元素，否则不渲染 |
| v-model     | 双向绑定，输入框中的文字变化时，会将其变化同步到绑定的变量上。反之亦然 |
| setup       | 组合式API                                                    |
| {{}}        | 插值语法，将当前组件中定义的变量插入指定位置，且默认实现绑定效果 |
| v-once      | 这个指令设置的组件在进行变量插值的时候只会插值一次           |
| v-html      | 指定一个Vue变量数据，通过HTML解析的方式将原始HTML替换到指定的位置标签 |
| v-bind      | 与标签属性绑定起来，比如v-bind:id=""，可以直接缩写为:id=" "  |
|             | 对于可以添加参数的指令，参数和指令之间使用冒号分隔           |
| v-on        | 事件绑定指令，v-on可以被@整个替换                            |
| v-else      | 必须紧跟在v-if的后面才能被识别到，if的条件不满足则显示渲染else内的内容 |
| v-show      | 与v-if不一样的是渲染逻辑不一样，v-show指令不管条件是真是假，当前元素都会被渲染。v-if如果是假，则不会渲染 |
| v-for       | 将数组中的数据渲染为列表视图，与index配合的话，index的索引值从0开始。第一个参数为遍历的对象中的属性的值，第二个参数为遍历的对象中的属性的名字，第三个参数为遍历的索引 |

## 书写风格

### option API的书写风格

```vue
<script>
export default{
    data(){
        return{
            age:18
        }
    },
    methods: {
        xiaoyu(){

        }
    },
}
</script>
```

### setup函数模式

```vue
<template>
    <div>
		{{a}}
    </div>
</template>

<script>
export default{
	setup(){
        //所有东西都在这里面定义
        const a = "小满开了个免费的知识星球，免费的"
        //要手动return出去
        return a
    }
}
</script>
```

### setup语法糖模式

```vue
<template>
  <div>
    <!-- 支持数字运算 -->
    {{ zs }}，{{ num + 5 }}
  </div>
  <div>
    <!-- 支持三元表达式 -->
    {{ num ? "true" : "false" }}
  </div>
  <div>
    <!-- API运算 -->
    {{ _API.map(v => { num: v }) }}
  </div>
</template>

<script setup lang="ts">
//也支持运算
const num: number = 1
//API运算
const _API: number[] = [1, 2, 3, 4, 5]
const zs: string = "小余祝大家2023年会越来越好"
</script>
```

## 各种Vue指令用法

### v-text

```vue
<template>
  <div v-text="zs">

  </div>
</template>

<script setup lang="ts">
const zs: string = "小余祝大家2023年会越来越好"
</script>
```

### v-html

```vue
<template>
  <div v-html="zs">

  </div>
</template>

<script setup lang="ts">
    //使用v-html，外面一层要是section
const zs: string = '<section style="color:red">小红帽班花姐姐</section>'
</script>
```

![image-20221226232858145](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221226232858145-1711983932538-2.png)

### v-if

```vue
<template>
  <div v-if="num">
  <!-- 显示 -->
    <h1>小余在不在，不在我进来了</h1>
  </div>
</template>

<script setup lang="ts">
//也支持运算
const num: number = 1
</script>
------------------------------------------
<template>
  <div v-if="num">
  <!-- 隐藏，是会将div块内的东西给注释掉，通过F12可以看到被注释节点 -->
    <h1>小余在不在，不在我进来了</h1>
  </div>
</template>

<script setup lang="ts">
//也支持运算
const num: number = 0
</script>
```

#### v-if配合v-else

> 当值为true的时候，if显示，else隐藏。反之亦然

```vue
<template>
  <div v-if="bol">
    <!-- 显示 -->
    <h1>小余在不在，不在我进来了</h1>
  </div>
  <div v-else="bol">
    <!-- 显示 -->
    <h1>小余在不在，不在我进来了，看来不在，进去看看有没有洛洛的私房照</h1>
  </div>
</template>

<script setup lang="ts">
const bol: boolean = false
</script>
```

#### v-if配合v-else-if和v-else使用

```vue
<template>
  <div v-if="num == '小余在家' ">
    <!-- 不显示 -->
    <h1>小余在不在，不在我进来了，原来你在啊，为什么不说话</h1>
  </div>
  <div v-else-if="num == '小余不在家' ">
    <!-- 不显示 -->
    <h1>小余在不在，不在我进来了，看来不在，进去看看有没有洛洛的私房照</h1>
  </div>
  <div v-else>
    <!-- 显示 -->
    <h1>小余去找小满串门了</h1>
  </div>
</template>

<script setup lang="ts">
//也支持运算
const num: string = "小余去找小满串门了"
</script>

```

### v-show

> 跟v-if的功能有那么一点像，但是比v-if的性能更高一点，因为v-show是将CSS样式的display调整成none，只切换了一下CSS
>
> - 而v-if会把整个节点变成一个注释节点，怎么想工作量都会更大一点
> - `v-show` 不支持在 `<template>` 元素上使用，也不能和 `v-else` 搭配使用

```vue
<template>
  <div v-if="bol">
    <!-- 显示 -->
    <h1>小余在不在，不在我进来了</h1>
  </div>
</template>

<script setup lang="ts">
//也支持运算
const bol: boolean = true
</script>
-----------------------------------------------------------------
<template>
  <div v-if="bol">
    <!-- 显示 -->
    <h1>小余在不在，不在我进来了</h1>
  </div>
</template>

<script setup lang="ts">
//也支持运算
const bol: boolean = false
</script>
----------------------------------------------------------------------
<template>
<div>
  <!-- v-show的展示部分 -->
  <h1 v-show="switch_luo">我是洛洛</h1>
  <!-- v-show隐藏部分 -->
  <h2 v-show="switch_yu">我是小余</h2>
</div>
</template>

<script setup lang='ts'>
const switch_luo = true
const switch_yu = false

</script>

<style scoped lang='less'>

</style>
```

### v-on

> `v-on:`可以简写为`@`，没错，剩一个冒号了

```vue
<template>
  <div>
    <button v-on:click="love()">请尽情的吩咐小满吧</button>
  </div>
</template>

<script setup lang="ts">
const love = ()=>{
  console.log("吩咐+1");
}
</script>
------------------------------------------------------------------------
<!-- 动态事件切换并且简写-->
<template>
  <div>
    <button @[xiaoman]="love()">请尽情的吩咐小满吧</button>
  </div>
</template>

<script setup lang="ts">
const xiaoman = "click"
const love = ()=>{
  console.log("吩咐+1");
}
</script>
------------------------------------------------------------------------
<!--修饰符，触发冒泡 点击parent，父级也跟着触发，通常情况下我们会设置禁止这种行为-->
<template>
  <div @click="parent">
    <button @[xiaoman]="love()">请尽情的吩咐小满吧</button>
  </div>
</template>

<script setup lang="ts">
const xiaoman = "click"
const parent = ()=>{
  console.log("我是父级");
  
}
const love = ()=>{
  console.log("吩咐+1");
  
}
</script>
-------------------------------------------------------------------------------
<!-- 禁止父级冒泡，只需要加上一个stop即可-->
<div @click="parent">
<button @[xiaoman].stop="love()">请尽情的吩咐小满吧</button>
</div>
```

### v-bind

```vue
<template>
  <div>
      <!-- 简写:	没错，简写就一个冒号，这里是绑定了对象里的值，能够成功使用-->
    <h1 :style="style1">小满现在什么色</h1>
  </div>
</template>

<script setup lang="ts">
const style1 = {
  color:"green",
  border:"2px soild #red"
}
</script>
-----------------------------------------------------------
<template>
  <div>
      <!-- 简写:	没错，简写就一个冒号，当然你也可以绑定css，或者css的各种变种 -->
    <h1 :class="['a','b']">小满现在什么色</h1>
  </div>
</template>

<script setup lang="ts">

</script>

<style>
.a{
  color: aqua;
}
.b{
  border: 1px solid #ccc;
}
</style>
-----------------------------------------------------------
<!-- 进阶，加上条件判断-->
<template>
  <div class="c" :class="[Cls ? 'a' : 'b' ]">
    小满现在什么色，取决在你手里
  </div>
</template>

<script setup lang="ts">
const Cls :boolean= true//一个条件判断，没错。除了支持动态的之外，他还能同时支持一个静态class，一动一静，别整多了，整多就报错了
</script>

<style >
.a{
  color: green;
}
.b{
  color:yellow;
}
.c{
  font-size: 50px;
}
</style>
```

### v-model

> ref和reactive后面我们会详细讲解的暂时可以先理解只要包裹就是响应式的

```vue
<template>
  <div>
    <div>{{ a }}</div>
    <input type="text" v-model="a">
  </div>
</template>

<script setup lang="ts">
import {ref} from "vue"
const a = ref('小满')
</script>
```

### v-for

```vue
<template>
  <div>
      <!--基础用法 -->
    <div v-for="item in arr">{{ item }}</div>
  </div>
</template>

<script setup lang="ts">
const arr = ['小满','学姐','小余','班花姐姐','菜菜哥','草莓']
</script>
----------------------------------------------------------------------
<!--第二个参数则是索引index，这是一个唯一值，从0开始计数-->
<template>
  <div>
    <div v-for="(item,index) in arr">{{index}} - {{ item }}</div>
  </div>
</template>

<script setup lang="ts">
const arr:string[] = ['小满','学姐','小余','班花姐姐','菜菜哥','草莓']
</script>
```

### 提升性能

#### v-once

> 只渲染元素和组件一次，并跳过之后的更新。其实就是只执行一次，后面都不执行了，所以性能会有所提升
>
> - 这里我们将刚刚小满写过的案例进行一个组合
> - 我在h1的位置加上了v-once，证明了他只执行了一次，那就是在渲染出来的时候，所以当我们点击按钮的时候，数字没有跳到，因为已经跳过更新了，将v-once删除掉，我们界面上的数字就能继续跳动了

```vue
<template>
  <div>
    <button @click="click1" v-text="strawberry"></button>
    <h1 v-once>成功偷走 {{ state.count }} 枚，还剩{{200000-state.count}}枚金币，请谨慎偷取，不然被草莓发现你偷拿她的金币的时候，只有决一死战了</h1>
  </div>
</template>

<script setup lang="ts">
import { reactive } from 'vue';
const strawberry:string = "请从草莓身上偷走金币"
const state = reactive({count:0})
const click1 = function(){
  state.count++
}
</script>
```

#### v-memo

> ### 用法(摘录自小满掘金)
>
> 在组件和元素都可以使用，主要是可以`缓存` 期望的类型是个数组`any[]`，该指令需要传入一个固定长度的依赖值数组进行比较。如果数组里的每个值都与最后一次的渲染相同，那么他的更新将会被跳过，甚至虚拟 DOM 的 vnode 创建也将被跳过，提升了性能。
>
> tips：如果`v-memo="[]"` 传入的是一个空数组，那么他的效果和`v-once` 一样
>
> ```vue
> <div v-memo="[val]"></div>
> ```
>
> `v-memo` 仅用于性能至上场景中的微小优化，应该很少需要。最常见的情况可能是有助于渲染海量 `v-for` 列表 (长度超过 1000 的情况)
>
> ### 配合v-for
>
> 配合v-for 属于最常见的情况，但是只有助于大数据量的情况例如`（1000条以上）`。
>
> tips:   当搭配 `v-for` 使用 `v-memo`，确保两者都绑定在同一个元素上。**`v-memo` 不能用在 `v-for` 内部。**
>
> 测试代码 `未加v-memo 一万条数据`
>
> 当组件的 `selected` 状态改变，默认会重新创建大量的 vnode，尽管绝大部分都跟之前是一模一样的。`v-memo` 用在这里本质上是在说“只有当该项的被选中状态改变时才需要更新”。这使得每个选中状态没有变的项能完全重用之前的 vnode 并跳过差异比较。
>
> ### 总结
>
> 如果你的项目对性能要求非常严格可以使用，但也只是小部分提升性能，如果你的项目平时没有那么大的数据量，感觉没什么有用。

```vue
<!--这是一个数组，如果数组里面什么都不填入的话，就跟v-once是一样的效果-->
<template>
  <div>
    <div @click="select(item.id)"   :key="item.id" v-for="(item) in arr">
      {{ item.id }} - selected： {{ item.id == active }}
    </div>
  </div>
</template>

<script setup lang='ts'>
import { ref, reactive } from 'vue'

const arr = reactive<any[]>([])
for (let i = 0; i < 10000; i++) {
  arr.push({
    id: i + 1,
    name: "test"
  })
}

const active = ref(1)

const select = async (index: number) => {
  active.value = index;
  console.time()
  await Promise.resolve()
  console.timeEnd()
}

</script>

<style scoped lang='less'>

</style>
```

**优化前后端对比**：

![image-20221227024614768](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221227024614768-1711983932538-1.png)![image-20221227024650122](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221227024650122-1711983932538-3.png)

# 第五章 — 虚拟DOM和VueKeyDiff算法(代号：巴别塔)

## 前置了解

- Diff算法是面试的高频问答点，在很多的语言中也会去使用
  - `TypeScript` 转 `JavaScript`的转换过程也会进行AST转换
  - 插件babel，ES6语法转ES5的时候也会经过抽象语法树的一个转换
  - js走V8引擎转字节码的时候，也会经过AST

## 介绍虚拟DOM

[Vue Template Explorer ](https://vue-next-template-explorer.netlify.app/#eyJzcmMiOiI8ZGl2PlxyXG4gICAgPGRpdj4gXHJcbiAgICAgICAgIDxzZWN0aW9uPnRlc3Q8L3NlY3Rpb24+XHJcbiAgICAgIDwvZGl2PiAgXHJcbjwvZGl2PiIsIm9wdGlvbnMiOnt9fQ==)

- 就是通过JS来生成一个AST节点树

> 查看虚拟DOM上面属性的方法，将下方代码块复制在控制台

```javascript
let div = document.createElement('div')
let str = ''
for (const key in div) {
  str += key + ''
}
console.log(str)
```

- 这上面的属性是非常多的，所以直接操作DOM非常浪费性能	(这就是为什么我们不直接去操作这个DOM，而是使用js去描述DOM对象的原因)

- 解决方案就是 我们可以用 `JS` 的计算性能来换取操作 `DOM` 所消耗的性能，既然我们逃不掉操作 `DOM` 这道坎，但是我们可以尽可能少的操作 `DOM`

  ```
  操作JS是非常快的
  ```

Vue3 源码地址[ https://github.com/vuejs/core](https://github.com/vuejs/core)

## Diff算法

- 先来看看图片的描述

![image-20221231162001284](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221231162001284-1711983932538-5.png)

![image-20221231184348742](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221231184348742-1711983932538-4.png)

### 没有key的diff算法

> 一共3步
>
> <img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221231164920117-1711983932538-6.png" alt="image-20221231164920117" style="zoom:67%;" />
>
> 1. 无key，patch的时候会替换
> 2. 新增
> 3. 删除
>
> - 情况1：
>   - 当替换结束之后，发现有多于的出来就会进行新增的操作，然后diff算法结束
> - 情况2：
>   - 当替换结束之后，发现bew Vnode有变少就会对old Vnode进行删除的操作，然后diff算法结束
>
> ---
>
> - 在上图中，相同的其实不必替换，需要替换的只有一个DDD，相同的我们可以进行复用，想要进行这个复用的操作的话，我们就要进行一个标记，也就`key`，有key才可以复用，那就得到有key的diff算法去啦

```typescript
  const patchUnkeyedChildren = (
    c1: VNode[],
    c2: VNodeArrayChildren,
    container: RendererElement,
    anchor: RendererNode | null,
    parentComponent: ComponentInternalInstance | null,
    parentSuspense: SuspenseBoundary | null,
    isSVG: boolean,
    slotScopeIds: string[] | null,
    optimized: boolean
  ) => {
    c1 = c1 || EMPTY_ARR
    c2 = c2 || EMPTY_ARR
    const oldLength = c1.length
    const newLength = c2.length
    const commonLength = Math.min(oldLength, newLength)
    let i
    ////首先通过for循环去patch重新渲染我们的元素
    for (i = 0; i < commonLength; i++) {
      const nextChild = (c2[i] = optimized
        ? cloneIfMounted(c2[i] as VNode)
        : normalizeVNode(c2[i]))
      patch(
        c1[i],
        nextChild,
        container,
        null,
        parentComponent,
        parentSuspense,
        isSVG,
        slotScopeIds,
        optimized
      )
    }
      //判断
    if (oldLength > newLength) {
      // remove old
      unmountChildren(
        c1,
        parentComponent,
        parentSuspense,
        true,
        false,
        commonLength
      )
    } else {
      // mount new	新增
      mountChildren(
        c2,
        container,
        anchor,
        parentComponent,
        parentSuspense,
        isSVG,
        slotScopeIds,
        optimized,
        commonLength
      )
    }
  }
```

### 有key的diff算法

> 有key的diff算法一共是分为5步，也是新旧虚拟DOM进行一个对比

#### 第一步：前序算法

#### 第二步：后序算法

```typescript
  // can be all-keyed or mixed
  const patchKeyedChildren = (
    c1: VNode[],
    c2: VNodeArrayChildren,
    container: RendererElement,
    parentAnchor: RendererNode | null,
    parentComponent: ComponentInternalInstance | null,
    parentSuspense: SuspenseBoundary | null,
    isSVG: boolean,
    slotScopeIds: string[] | null,
    optimized: boolean
  ) => {
    let i = 0
    const l2 = c2.length
    let e1 = c1.length - 1 // prev ending index
    let e2 = l2 - 1 // next ending index
	//前序算法，只对比前面的
    // 1. sync from start
    // (a b) c
    // (a b) d e
    while (i <= e1 && i <= e2) {
      const n1 = c1[i]
      const n2 = (c2[i] = optimized
        ? cloneIfMounted(c2[i] as VNode)
        : normalizeVNode(c2[i]))
      if (isSameVNodeType(n1, n2)) {
        patch(
          n1,
          n2,
          container,
          null,
          parentComponent,
          parentSuspense,
          isSVG,
          slotScopeIds,
          optimized
        )
      } else {
          //当旧VNode与新VNode对比着对比着发现不一样的时候，就跳出循环
        break
      }
      i++//前序算法从前面开始往后推，是i++，如果是尾序算法的话就从后面开始往前进，是i--
        //其实都差不多
    }
```

> 进入isSameVNode Type(n1,n2)内进行一探究竟
>
> 下方的type是什么？
>
> - 例如我们现在渲染div这个元素，那type就等于这个div
>
> 下方的key是什么？
>
> - 是我们在for循环中绑定的key，唯一值

```typescript
export function isSameVNodeType(n1: VNode, n2: VNode): boolean {
  if (
    __DEV__ &&
    n2.shapeFlag & ShapeFlags.COMPONENT &&
    hmrDirtyComponents.has(n2.type as ConcreteComponent)
  ) {
    // HMR only: if the component has been hot-updated, force a reload.
    return false
  }
  //判断type 和 key是不是一样的，如果是一样的，他才会进行一个复用
  return n1.type === n2.type && n1.key === n2.key
}
```

```javascript
<template>
  <div>
    <!-- key是唯一值，通常后端返回，类似item.id的形式 -->
    <div :key="item" v-for="(item) in Arr">{{ item }}</div>
  </div>
</template>
 
 
 
<script setup lang="ts">
const Arr: Array<string> = ['A', 'B', 'C', 'D']
Arr.splice(2,0,'DDD')
</script>
 
 
<style>
</style>
```

#### 第三步：处理新节点

> 如果有的话，没有就跳过

```typescript
// 3. common sequence + mount
// (a b)
// (a b) c
// i = 2, e1 = 1, e2 = 2
// (a b)
// c (a b)
// i = 0, e1 = -1, e2 = 0
if (i > e1) {
  if (i <= e2) {
    const nextPos = e2 + 1
    const anchor = nextPos < l2 ? (c2[nextPos] as VNode).el : parentAnchor
    while (i <= e2) {
      patch(
        null,//patch的参数为null的话就是新增
        (c2[i] = optimized
          ? cloneIfMounted(c2[i] as VNode)
          : normalizeVNode(c2[i])),
        container,
        anchor,
        parentComponent,
        parentSuspense,
        isSVG,
        slotScopeIds,
        optimized
      )
      i++
    }
  }
}
```

#### 第四步：卸载

> 跟第三步一样，如果有的话，没有就跳过

```typescript
// 4. common sequence + unmount
// (a b) c
// (a b)
// i = 2, e1 = 2, e2 = 1
// a (b c)
// (b c)
// i = 0, e1 = 0, e2 = -1
else if (i > e2) {
  while (i <= e1) {
    unmount(c1[i], parentComponent, parentSuspense, true)
    i++
  }
}
```

#### 第五步：特殊情况乱序

> 也是最难的一步，有可能是做了位移，或者新增、删除、位移、更新同时发生，主要为不可控的一些情况
>
> 源码将其分为3小节去处理
>
> - 构建新节点映射关系案例
>
> ```typescript
> key  1 2 3 4 5
> 索引  0 1 2 3 4
> sort
> key 5 4 3 2 1
> 索引 0 1 2 3 4
> 5=>0 4=>1 3=>2 2=>3 1=>4(构建成map关系)
> ```
>
> 

```typescript
    // 5. unknown sequence
    // [i ... e1 + 1]: a b [c d e] f g
    // [i ... e2 + 1]: a b [e d c h] f g
    // i = 2, e1 = 4, e2 = 5
    else {//构建新旧节点的映射关系
      const s1 = i // prev starting index
      const s2 = i // next starting index

      // 5.1 build key:index map for newChildren
      const keyToNewIndexMap: Map<string | number | symbol, number> = new Map()
      for (i = s2; i <= e2; i++) {
        const nextChild = (c2[i] = optimized
          ? cloneIfMounted(c2[i] as VNode)
          : normalizeVNode(c2[i]))
        if (nextChild.key != null) {
          if (__DEV__ && keyToNewIndexMap.has(nextChild.key)) {
            warn(
              `Duplicate keys found during update:`,
              JSON.stringify(nextChild.key),
              `Make sure keys are unique.`
            )
          }
          keyToNewIndexMap.set(nextChild.key, i)
        }
      }

      // 5.2 loop through old children left to be patched and try to patch
      // matching nodes & remove nodes that are no longer present
      let j
      let patched = 0
      const toBePatched = e2 - s2 + 1
      let moved = false
      // used to track whether any node has moved
      let maxNewIndexSoFar = 0
      // works as Map<newIndex, oldIndex>
      // Note that oldIndex is offset by +1
      // and oldIndex = 0 is a special value indicating the new node has
      // no corresponding old node.
      // used for determining longest stable subsequence
      //记录新节点在旧节点中的位置数组
      //[5,4,3,2,1]
      const newIndexToOldIndexMap = new Array(toBePatched)
      for (i = 0; i < toBePatched; i++) newIndexToOldIndexMap[i] = 0

      for (i = s1; i <= e1; i++) {
        const prevChild = c1[i]
        if (patched >= toBePatched) {
          // all new children have been patched so this can only be a removal
            //多余的旧节点会进行删除
          unmount(prevChild, parentComponent, parentSuspense, true)
          continue
        }
        let newIndex
        if (prevChild.key != null) {
          newIndex = keyToNewIndexMap.get(prevChild.key)
        } else {
          // key-less node, try to locate a key-less node of the same type
          for (j = s2; j <= e2; j++) {
            if (
              newIndexToOldIndexMap[j - s2] === 0 &&
              isSameVNodeType(prevChild, c2[j] as VNode)
            ) {
              newIndex = j
              break
            }
          }
        }
          //如果新节点不包含旧节点里面了，也会进行删除操作
        if (newIndex === undefined) {
          unmount(prevChild, parentComponent, parentSuspense, true)
        } else {
          newIndexToOldIndexMap[newIndex - s2] = i + 1
          if (newIndex >= maxNewIndexSoFar) {
            maxNewIndexSoFar = newIndex
          } else {
            //节点出现交叉，说明是移动要去求最长递增子序列
            moved = true
          }
          patch(
            prevChild,
            c2[newIndex] as VNode,
            container,
            null,
            parentComponent,
            parentSuspense,
            isSVG,
            slotScopeIds,
            optimized
          )
          patched++
        }
      }

      // 5.3 move and mount
      // generate longest stable subsequence only when nodes have moved(仅当节点发生移动时生成最长稳定子序列)
        //求最长递增子序列升序
      const increasingNewIndexSequence = moved
        ? getSequence(newIndexToOldIndexMap)
        : EMPTY_ARR
      j = increasingNewIndexSequence.length - 1
      // looping backwards so that we can use last patched node as anchor(反向循环，以便我们可以使用最后打补丁的节点作为锚点)
      for (i = toBePatched - 1; i >= 0; i--) {
        const nextIndex = s2 + i
        const nextChild = c2[nextIndex] as VNode
        const anchor =
          nextIndex + 1 < l2 ? (c2[nextIndex + 1] as VNode).el : parentAnchor
        if (newIndexToOldIndexMap[i] === 0) {
          // mount new
          patch(
            null,
            nextChild,
            container,
            anchor,
            parentComponent,
            parentSuspense,
            isSVG,
            slotScopeIds,
            optimized
          )
        } else if (moved) {
          // move if:
          // There is no stable subsequence (e.g. a reverse)
          // OR current node is not among the stable sequence(或者当前节点不在稳定序列中)
            //如果当前遍历的这个节点不在子序列，说明要进行移动
          if (j < 0 || i !== increasingNewIndexSequence[j]) {
            move(nextChild, container, anchor, MoveType.REORDER)
          } else {
            j--//如果节点在子序列中直接跳过
          }
        }
      }
    }
  }
```

##### getSequence函数内部

> 这是第五步中处理的第三小节，最长递增子序列其中处理的算法，由贪心算法和二分查找组成
>
> - 由小满提供注释

```typescript
function getSequence (arr) {
  const p = arr.slice()
  const result = [0]
  let i, j, u, v, c
  const len = arr.length
  for (i = 0; i < len; i++) {
    const arrI = arr[i]
    // 排除等于 0 的情况
    if (arrI !== 0) {
      j = result[result.length - 1]
      // 与最后一项进行比较
      if (arr[j] < arrI) {
        // 存储在 result 更新前的最后一个索引的值
        p[i] = j
        result.push(i)
        continue
      }
      u = 0
      v = result.length - 1
      // 二分搜索，查找比 arrI 小的节点，更新 result 的值
      while (u < v) {
        // 取整得到当前位置
        c = ((u + v) / 2) | 0
        if (arr[result[c]] < arrI) {
          u = c + 1
        }
        else {
          v = c
        }
      }
      if (arrI < arr[result[u]]) {
        if (u > 0) {
          // 正确的结果
          p[i] = result[u - 1]
        }
        // 有可能替换会导致结果不正确，需要一个新数组 p 记录正确的结果
        result[u] = i
      }
    }
  }
  u = result.length
  v = result[u - 1]

  // 回溯数组 p，找到最终的索引
  while (u-- > 0) {
    result[u] = v
    v = p[v]
  }
  return result
}
```

### diff算法源码

```typescript
    // fast path
    if (patchFlag > 0) {
      if (patchFlag & PatchFlags.KEYED_FRAGMENT) {
        // this could be either fully-keyed or mixed (some keyed some not)
        // presence of patchFlag means children are guaranteed to be arrays
        //有key的diff算法
        patchKeyedChildren(
          c1 as VNode[],
          c2 as VNodeArrayChildren,
          container,
          anchor,
          parentComponent,
          parentSuspense,
          isSVG,
          slotScopeIds,
          optimized
        )
        return
      } else if (patchFlag & PatchFlags.UNKEYED_FRAGMENT) {
        // unkeyed(没有key的diff算法)
        patchUnkeyedChildren(
            //c1、c2都是由虚拟DOM生成的 
          c1 as VNode[],//c1是旧的Vnode
          c2 as VNodeArrayChildren,//c2是指新的Vnode
          container,
          anchor,
          parentComponent,
          parentSuspense,
          isSVG,
          slotScopeIds,
          optimized
        )
        return
      }
    }
```

> 乱序的对比，为了防止浪费性能，前面对比一下，后面对比一下，能够有效的分辨出是刚开始就不一样了还是最后不一样。最后面往前移，直到遇到不一样了之后在开始进行下一步的排序复用

# 第六章 — Ref全家桶 & 源码解析(重写部分)

> 小工具介绍 => 根据输出提前自定义好的单词可以输出自定义对应的内容(乍一看好像有点像剪切板)
>
> 在vue.json中进行设置
>
> ```js
> {
>     "Print to console":{
>         "prefix":"自定义单词部分",
>         "body":[
>             "输出自定义内容"
>         ]
>     }
> }
> ```

## ref

接受一个内部值并返回一个响应式且可变的 ref 对象。ref 对象仅有一个 `.value` property，指向该内部值。

> 不同的声明方法

![image-20221001094826076](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221001094826076-1711983932538-9.png)

- 推荐类型比较复杂的时候方便自定义

![image-20221001094922141](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221001094922141-1711983932538-8.png)

- 或者什么都不要，让编辑器自己推导

![image-20221001095054253](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221001095054253-1711983932538-7.png)

这里要响应改变值的话，要记得加.value，因为Proxy是在.value中的

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221001095226462-1711983932538-10.png" alt="image-20221001095226462" style="zoom:50%;" />

```ts
<template>
  <div>{{Man}}</div>
  <button @click="change()">修改</button>
</template>

<script setup lang="ts">
  import {ref,isRef} from 'vue'
  const Man = ref({name:"小余"})

  const change = () =>{
    Man.value.name = 'yupi'
    console.log(Man)
    console.log(isRef(Man))
  }
</script>
```

### isRef

> 作用：判断一个东西是不是一个ref对象，结果返回布尔值 

```js
console.log(isRef(Man))
```

### shallowRef

> 作用：shallowRef是浅层次的响应，而ref是深层次的响应
>
> shallowRef只响应到.value，如果这个.value后面在跟值进行修改，就只能在控制台中看见值改变了，而没办法响应到页面中
>
> 也就是创建一个跟踪自身 `.value` 变化的 ref，但不会使其值也变成响应式的
>
> 注意：**ref不能跟shallowRef一起使用，不然shallowRef会被Ref影响，从而造成视图的更新(失去浅层次响应的作用)**

```js
<template>
  <div>Ref:{{XiaoYu}}</div>
  <div>shallowRef:{{XiaoYu2}}</div>
  <button @click="change()">修改</button>
</template>

<script setup lang="ts">
  import {ref,isRef,shallowRef} from 'vue'
  const XiaoYu = ref({name:"小余"})
  const XiaoYu2 = shallowRef({name:"小余2号"})
  const yupi = {name:"鱼皮"}

  const change = () =>{
    // XiaoYu.value.name = '小余抓住了一只yupi' 这里必须注销掉，不如ref会影响到shallowRef，导致视图的更新(也就是会造成下方的小余今天摸鱼了也一同刷新)
    XiaoYu2.value.name = '小余今天摸鱼了'
    console.log(XiaoYu)
    console.log("这是小余",isRef(XiaoYu))
    console.log("这是鱼皮",isRef(yupi))
  }
</script>
```

### triggerRef

- 因为在ref底层更新的逻辑的时候，会调用triggerRef
- 而triggerRef会强制更新我们这个收集的依赖
- 强制更新页面 DOM

### customRef

- 让我们自己去自定义一个ref
- customRef 是个工厂函数要求我们返回一个对象 并且实现 get 和 set 适合去做防抖之类的
- customRef也是一个浅层的响应
- 里面返回的是set跟get两个方法，回调里面接收的是两个函数，一个是track，一个是trigger
  - track：收集依赖，收集完return回去
  - 触发依赖交给set，接收一个newVal
  - **track 作用：通知 Vue 追踪 value 的变化 (相当于提前和 get 商量一下，让他认为这个 value 有用的！)**
  - **trigger 作用：通知 Vue 重新解析模板。**

```vue
<template>
 
  <div>{{obj}}</div>//不用设置ref了，因为我们自己手动实现了一个
  <hr>
  <div>
    {{ name }}
  </div>
  <hr>
  <button @click="change()">修改</button>
 
</template>
 
<script setup lang='ts'>
import { ref, reactive, onMounted, shallowRef, customRef } from 'vue'
 //这下面是customRef的演示，自己定义ref，接近源码的表现
  const change = ()=>{
  function MyRef<T>(value:T){//泛型
    let timer:any
    return customRef((track, trigger)=>{//track：收集依赖的 trigger：触发依赖
      return{
        get(){
          track()
          return value//收集完依赖就返回 回去了
        },
        set(newVal){//这里会收到新的值，调用接口，调用太多次的话，服务器的压力就会很大，这个时候我们就可以自己设置一个防抖节流
          clearTimeout(timer)//清除定时器，不让他同时存在多个定时器(同一时间只能有一个定时器)
          timer = setTimeout(()=>{
            console.log("吃午饭了")
            value = newVal//将新值赋给value
            timer = null//清空一下时间
            trigger()
          },500)//时间间隔设置为0.5秒，防抖节流
        }
      }
    })
  }
 
 
const obj = MyRef<string>('customRef小余')
 
 
  const change = ()=>{
    obj.value = "customRef被修改了，到饭点准备干饭了"
  }
 
</script>
<style scoped>
</style>
```

#### ref小技巧

1. <img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221001111117720-1711983932538-11.png" alt="image-20221001111117720" style="zoom:33%;" />打开这个能够让我们观察value方便一点，控制台点一下就行，少点一下。就是格式化了，e f系列跟reactive系列都可以使用
2. **ref是可以获取DOM元素的**，在html部分中ref="xxx"，这个xxx要与在JavaScript里面声明的变量名一样，比如const xxx = ref<泛型>()，然后console控制台输出一下，ref.value.innerText就能够获取到了

## 源码部分

> 在packages里面这个reactivity下面这个ref.js中，代码在六七十行左右

在视频15分钟开始解析源码

收获：通过观看源码获知了ref跟shallowRef的区别在于.value后会不会进行一个判断，如果后面跟着数组对象之类的，ref会进行判断`return create(value,true)`创建一个Ref去继续接收，从而实现响应。shallowRef就不是true而是false，所以当shallowRef的value后面跟着东西的话，不会给他套上Ref的，也就是不会进行创建Ref，那value后面自然就不会是响应的了

## 第七章 — Reactive全家桶 & 源码讲解

> ref支持所有类型，Reactive支持引用类型，Array，Object，Map，Set
>
> ref取值是需要.value的，reactive取值的话不需要.value

- reactive proxy不能直接赋值，不然是会破坏响应式对象的

  - 解决方法(解构：从数组和对象中提取值，对变量进行赋值，这被称为解构)

  - 数组可以使用push加解构(...res)，把数组里的内容拿出来

  - 或者添加一个对象  把数组作为一个属性去解决

    ```vue
    <script>
    let obj = reactive({name:'小余'})
    const read = readonly(obj)
    //readonly就是把被他包在里面的变成只读的，但会受到reactive的影响
    </script>
    ```

- shallowReactive

  - 一样跟shallowRef是浅层次的修改
  - 但一样跟shallowRef一样，shallowReactive也会受到Reactive的影响

> toRefs()适合做一些解构的操作，当我们想要解构reactive的时候先考虑toRefs
>
> toref只能修改响应式对象的值   非常响应式视图毫无变化
>
> 解构目的 =>将原本一些没法响应式使其响应

## 第八章 — toRaw，toRefs,toRaw &源码解析

> - toRef 和传入的数据形成引用关系，修改 toRef 会影响之的数据，但是不会更新 UI
> - ref 是单纯的复制，影响不影响之前复制的数据，取决于复制的数据类型，但是会更新 UI
> - ref 数据会引起监听行为，而 toRef 不会

1. 与经过reactive包装的内容相比，我们可以看到，toRaw把reactive套上的Proxy外壳给脱掉了

### 源码解析

####  toRef部分

- 通过一个变量将对象内的值取出，然后返回一个3元表达式

- 这个三元表达式内容为：经过isRef的判断是不是Ref类型的，是则直接返回，不是则

  ```Vue
  (new ObjectRefImpl(Object,key,defaultValue))
  没错，变成了一个RefImpl
  ```

- RefImpl是做了收集依赖`trackRefValue(this)`的，然后触发依赖的更新`triggerRefValue(this,newVal)`

- toRef创建出来的RefImpl并没有收集依赖，也没有触发依赖更新

- 这就是toRef对非响应式对象是不会改变视图的，因为没有收集依赖也没有触发依赖的更新这两个操作

- 响应式对象可以改变视图的因为用`reactive`，`reactive`里面调用Proxy，这个里面就已经触发了收集依赖跟触发依赖这两个操作。所以我们在这个RefImpl中没有看见这个<收集依赖、触发更新>两个操作是正常的，不然已经通过reactive触发过两个操作了，这里再次调用就连续调用两次了，会出现bug。并且没办法区分出这个原始对象

- 因为你这里调用的话，已经响应式的变成触发2次，原始对象也触发一次了，那就不好区分了

#### toRefs部分

- 刚开始就先判断一个是不是对象，是的话将其里面的值放到数组中初始化一下(ps：真的很严谨)
- 然后每个属性去做一下toRef，然后把这个内容做一个返回。也判断了一下是不是Proxy对象

#### toRaw部分

- 根据一个 Vue 创建的代理返回其原始对象。
- 这是一个可以用于临时读取而不引起代理访问 / 跟踪开销，或是写入而不触发更改的特殊方法。不建议保存对原始对象的持久引用，请谨慎使用。

2. 在这里对raw进行的是一个跳转操作，`ReactiveFlags.RAW`，ReactiveFlags是定义在上方的，对RAW做一个赋值`'_v_raw'`操作，这个操作就是将原始对象给取出来了

# 进阶 响应式原理源码实现

>  **响应式原理**
>
> Vue2 使用的是 `Object.defineProperty Vue3` 使用的是 `Proxy`

## Vue2.0的不足

> 核心：核心代码是使用 Object.defineProperty () 来劫持对象中每一个属性的 set 和 get 方法
>
> 总结：
>
> 缺点一：如果对象中属性过多，那么需要给每个属性都绑定 defineProperty，十分损耗效率。
>
> 缺点二：只能监听对象属性的修改、读取，如果涉及删除、增加就无法响应式刷新页面了（只能通过 Vue.set）
>
> 缺点三：通过下标、length 修改数组也不会响应式刷新页面，可以直接重新赋值，或者使用 filter、map、concat、slice 等生成新数组对其赋值

1. 对象只能劫持 设置好的数据
   - 是否可以删除（Configurable）
   - 是否可以枚举（Enumerable）
   - 是否可以修改（Writable）
   - 返回值（value）
   - set（重要，修改触发的回调函数）
   - get（重要，获取触发的回调函数）
2. 新增的数据需要 Vue.Set (xxx) 数组只能操作七种方法，修改某一项值无法劫持(就算能劫持也会造成性能上的问题)
   - 1.splice()2.push(),3.pop(),4.shift()5. unshift(),6.sort(),7.reverse()

## Vue3 响应式

> 和 Vue2 不同的是，它的核心是 es6 的 Proxy 结合 Reflect 实现的，使用代理，可以不直接操作对象，这样就可以监听到所有对象的增删改查，同时也提升了效率。

### 手写实现reactive

> 实现最基础的get与set

```ts
const isObject = (target)=> target !=null && typeof target == 'object'//此行是在13分的时候加上的，准备递归，递归代码写在最下面，进行对比
export const reactive = <T extends object>(target:T)=>{//使用一个泛型约束
    return new Proxy(target,{
        get (target,key,receiver){//返回三个参数，分别为当前传入的对象、对象的属性、也是当前对象
            let res = Reflect.get(target,key,receiver)//需要使用Reflect接收三个参数，也是取值的，防止上下文错乱
            
            return res;
            //return target[key] 这种返回方式会造成上下文的错乱
        },
        set(target,key,value,receiver){//set需要接收的是一个布尔值
            let res = Reflect.set(target,key,value,receiver);//Reflect刚好返回的就是布尔值
            
            return res;
        }
    })
}

reactive({})
```

### effect track trigger 实现

> 实现 effect 副作用函数
>
>  使用一个`全局变量`active 收集当前副作用函数，并且初始化的时候调用一下

```js
let activeEffect:any//ts提示下需要这样定义一下，不然爆红线了
export const effect = (fn:Function)=>{//fn是匿名函数方便用户去自定义，实现依赖收集与更新
    //_effect是一个闭包，里面全局变量(全局变量在effect外面已经定义)将闭包收集起来
    const _effect = function (){
        activeEffect = _effect//收集起来
        fn()//需要调用一下去执行effect的副作用函数
    }
    
    _effect()//首次默认调用一下
}
```

### 实现 track

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221118090908299-1711983932538-12.png" alt="image-20221118090908299" style="zoom:47%;" />

```ts
//target参数被当作targetMap中的key收集起来了
//Value里的new Map的key对应的就算targetMap的key。然后new Map对key去收集依赖(也就是set结构)，set结构下去存effect副作用函数
----------------------------------------------------------->
//注意：entries?: [object, any][] | null): WeakMap<object, any>，也就是说weakMap只接收对象类型
const targetMap = new WeakMap()//key需要拼装格式，需要一个全局变量先存一下
export const track = (target?:any,key?:any) => {//target是一个对象
    //定义好第一层
    let depsMap = targetMap.get(target)//target是targetMap下的key，我们先获取key
    if (!depsMap){//第一次值是取不到的
        depsMap = new Map()//此时我们进入流程图的第一阶段，将new Map传入最外层的depsMap
        target.set(target,depsMap)//分别对应了new Map往下传的key与Value
    }
    //定义好第二层
    let deps = depsMap.get(key)//获取第二层的new Set值
    if (!deps){//一样的，第一次的值还是取不到的
        deps = new Set()//取不到值，但是我们获取到了第二层的new Set
        depsMap.set(key,deps)//通过new Map将key与value关联起来
    }

    deps.add(activeEffect)//将副作用函数添加进来，也就是流程图最下面的操作
}
```

```ts
export const trigger = (target:any,key:any)=>{
    const depsMap = target.get(target)//通过targetMap下的key取到Map下的key了
    if (depsMap){
         const deps = depsMap.get(key)//又是重复的操作，通过Map下的key取到了依赖
        //取到依赖后进行一个更新(遍历数组)，调用上面定义好的副作用函数
        deps.forEach((effect:any)=>effect())
    }else{
        return "不好意思，你没取到值"
    }
}
```

### **完整形式代码**

> reactive有进行改动，并非单纯拼凑

```ts
let activeEffect:any
export const effect:any = (fn:Function)=>{//fn是匿名函数方便用户去自定义，实现依赖收集与更新
    //_effect是一个闭包，里面定义全局变量将闭包收集起来
    const _effect = function (){
        activeEffect = _effect//收集起来
        fn()//需要调用一下去执行effect的副作用函数
    }

    _effect()//首次默认调用一下
}

//注意：entries?: [object, any][] | null): WeakMap<object, any>，也就是说weakMap只接收对象类型
const targetMap = new WeakMap()//key需要拼装格式，需要一个全局变量先存一下
export const track = (target?:any,key?:any) => {//target是一个对象
    //定义好第一层
    let depsMap = targetMap.get(target)//target是targetMap下的key，我们先获取key
    if (!depsMap){//第一次值是取不到的
        depsMap = new Map()//此时我们进入流程图的第一阶段，将new Map传入最外层的depsMap
        target.set(target,depsMap)//分别对应了new Map往下传的key与Value
    }
    //定义好第二层
    let deps = depsMap.get(key)//获取第二层的new Set值
    if (!deps){//一样的，第一次的值还是取不到的
        deps = new Set()//取不到值，但是我们获取到了第二层的new Set
        depsMap.set(key,deps)//通过new Map将key与value关联起来
    }

    deps.add(activeEffect)//将副作用函数添加进来，也就是流程图最下面的操作
}

export const trigger = (target:any,key:any)=>{
    const depsMap = target.get(target)//通过targetMap下的key取到Map下的key了
    if (depsMap){
        const deps = depsMap.get(key)//又是重复的操作，通过Map下的key取到了依赖
        //取到依赖后进行一个更新(遍历数组)，调用上面定义好的副作用函数
        deps.forEach((effect:any)=>effect())
    }else{
        return "不好意思，你没取到值"
    }
}

export const reactive = <T extends object>(target:T)=>{//使用一个泛型约束
    return new Proxy(target,{
        get (target,key,receiver){//返回三个参数，分别为当前传入的对象、对象的属性、接收方
            let res = Reflect.get(target,key,receiver)//需要使用Reflect接收三个参数，也是取值的，防止上下文错乱
            track(target,key)//传入target
            return res;
            //return target[key] 这种返回方式会造成上下文的错乱
        },
        set(target,key,value,receiver){//set需要接收的是一个布尔值
            let res = Reflect.set(target,key,value,receiver);//Reflect刚好返回的就是布尔值
            trigger(target,key)//传入trigger
            return res;
        }
    })
}

reactive({})
```

**测试代码**

> html使用import注意加上type 并且要起一个服务 如liveServer

```js
<!DOCTYPE html>
<html lang="en">
 
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
 
<body>
 
    <div id="app">
 
    </div>
 
    <script type="module">
        import { reactive } from './reactive.js'
        import { effect } from './effect.js'
        const user = reactive({
            name: "小满",
            age: 18
        })
        effect(() => {
            document.querySelector('#app').innerText = `${user.name} - ${user.age}`
        })
 
        setTimeout(()=>{
            user.name = '小满打出了康康卡'
            setTimeout(()=>{
                user.age = '23'
            },1000)
        },2000)
 
    </script>
</body>
 
</html>
```

### 递归实现

```ts
import { track, trigger } from './effect'
 
const isObject = (target) => target != null && typeof target == 'object'
 
export const reactive = <T extends object>(target: T) => {
    return new Proxy(target, {
        get(target, key, receiver) {
            const res = Reflect.get(target, key, receiver) as object
 
            track(target, key)
 
            if (isObject(res)) {//深层次的劫持
                return reactive(res)
            }
 
            return res
        },
        set(target, key, value, receiver) {
            const res = Reflect.set(target, key, value, receiver)
 
            trigger(target, key)
 
            return res
        }
    })
}
```

**新的测试html的demo**

```js
<!DOCTYPE html>
<html lang="en">
 
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
 
<body>
 
    <div id="app">
 
    </div>
 
    <script type="module">
        import { reactive } from './reactive.js'
        import { effect } from './effect.js'
        const user = reactive({
            name: "小满",
            age: 18,
            foo:{
                bar:{
                    sss:123
                }
            }
        })
        effect(() => {
            document.querySelector('#app').innerText = `${user.name} - ${user.age}-${user.foo.bar.sss}`
        })
 
        setTimeout(()=>{
            user.name = '麒麟哥的笔记是txt格式的'
            setTimeout(()=>{
                user.age = '23'
                setTimeout(()=>{
                    user.foo.bar.sss = 66666666
                },1000)
            },1000)
        },2000)
 
    </script>
</body>
 
</html>
```



# 第九章 — computed计算属性

> 1. 接受一个 getter 函数，返回一个只读的响应式 [ref](https://cn.vuejs.org/api/reactivity-core.html#ref) 对象。该 ref 通过 `.value` 暴露 getter 函数的返回值。它也可以接受一个带有 `get` 和 `set` 函数的对象来创建一个可写的 ref 对象。
> 2. 当依赖的属性的值发生变化的时候，才会触发他的更改，如果依赖的值，不发生变化的时候，使用的是缓存中的属性值。

### 购物车案例

```vue
<template>
   <div>
      <table style="width:800px" border>
         <thead>
            <tr>
               <th>名称</th>
               <th>数量</th>
               <th>价格</th>
               <th>操作</th>
            </tr>
         </thead>
         <tbody>
            <tr :key="index" v-for="(item, index) in data">
               <td align="center">{{ item.name }}</td>
               <td align="center">
                  <button @click="AddAnbSub(item, false)">-</button>
                  {{ item.num }}
                  <button @click="AddAnbSub(item, true)">+</button>
               </td>
               <td align="center">{{ item.num * item.price }}</td>
               <td align="center">
                  <button @click="del(index)">删除</button>
               </td>
            </tr>
         </tbody>
         <tfoot>
            <tr>
               <td></td>
               <td></td>
               <td></td>
               <td align="center">总价:{{ $total }}</td>
            </tr>
         </tfoot>
      </table>
   </div>
</template>
 
<script setup lang="ts">
import { computed, reactive, ref } from 'vue'
type Shop = {
   name: string,
   num: number,
   price: number
}
let $total = ref<number>(0)
const data = reactive<Shop[]>([
   {
      name: "小满的袜子",
      num: 1,
      price: 100
   },
   {
      name: "小满的裤子",
      num: 1,
      price: 200
   },
   {
      name: "小满的衣服",
      num: 1,
      price: 300
   },
   {
      name: "小满的毛巾",
      num: 1,
      price: 400
   }
])
 
const AddAnbSub = (item: Shop, type: boolean = false): void => {
   if (item.num > 1 && !type) {
      item.num--
   }
   if (item.num <= 99 && type) {
      item.num++
   }
}
const del = (index: number) => {
   data.splice(index, 1)
}
 
 
 
$total = computed<number>(() => {
   return data.reduce((prev, next) => {
      return prev + (next.num * next.price)
   }, 0)
})
 
 
 
</script>
 
<style>
</style>
```

# 第十章 — watch侦听器 & 源码讲解

> 用于声明在数据更改时调用的侦听回调。
>
> `watch` 选项期望接受一个对象，其中键是需要侦听的响应式组件实例属性 (例如，通过 `data` 或 `computed` 声明的属性)—— 值是相应的回调函数。该回调函数接受被侦听源的新值和旧值。
>
> 第一个参数新值，第二个参数旧值

- 第三个参数是决定是否深层次监听的(deep:true)，但其实我们通过reactive也一样可以实现深层次监听
  - 但是有一个问题，就是通过控制台我们可以看到新值旧值都已经被新值覆盖了
- immediate决定是否一开始就自调用

## 侦听单个数据源

```ts
<template>
  <div>
  	case1:<input v-model="message" type="text">
    <hr>
    case2:<input type="text">
  </div>
  <h1>{{tom}}</h1>
</template>

<script setup lang="ts">
import {ref} from "vue";
let message = ref<string>('小满')
//监听器第一个参数：侦听的数据源(sources)	第二个参数 回调函数 cb（newVal,oldVal）
watch(message,(newVal,oldVal)=>{
    console.log('新的值----', newVal);
    console.log('旧的值----', oldVal);
})
</script>

<style scoped>

</style>
```

## 侦听多个数据源

```ts
<template>
  <div>
  	case1:<input v-model="message" type="text">
    <hr>
    case2:<input v-model="message2" type="text">
  </div>
  <h1>{{tom}}</h1>
</template>

<script setup lang="ts">
import {ref} from "vue";
let message = ref<string>('小满')
let message2 = ref<string>('喜多川学姐')

//使用数组的形式侦听多个数据源，返回的结果也会变成数组，结果顺序 按照 监听顺序
watch([message,message2],(newVal,oldVal)=>{//此时新值旧值也会变成一个数组
    console.log('新的值----', newVal);
    console.log('旧的值----', oldVal);
})
</script>

<style scoped>

</style>
```

## 监听 Reactive

> 使用 reactive 监听深层对象开启和不开启 deep 效果一样
>
> 深层次监听啦啦啦，需要给reactive开启第三个属性option(是一个配置项) => 里面有一个deep就是用来代表深度监听的

```ts
//监听语句变成如下
case1:<input v-model="message.nav.bar.name" type="text">

import { ref, watch ,reactive} from 'vue'
 
let message = ref({//reactive已经隐性开启deep了，不需要再手动开启了
    nav:{
        bar:{
            name:"学姐好好吃饭"
        }
    }
})
 
watch([message,message2],(newVal,oldVal)=>{//此时新值旧值也会变成一个数组
  console.log('新的值----', newVal);
  console.log('旧的值----', oldVal);
},{deep:true})//深度监听
```

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221118215901847-1711983932538-13.png" alt="image-20221118215901847" style="zoom:50%;" />

通过控制台打印，我们发现了其中的Proxy下的深层次监听

---

此时会发现有一个问题，那就是旧值跟新值的内容是一样的(原因是因为引用类型返回的新值是跟旧值一样的)

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221118220127745-1711983932538-16.png" alt="image-20221118220127745" style="zoom:67%;" />

我们在使用的时候会发现一个问题，那就是我只改变了一个值，为什么没有改变的那个值(例如上面的`喜多川学姐`)也跟着带出来了，我只想要得到改变的值,那要怎么办到呢？  =>  简洁版提问：想要侦听单一属性

- Vue推荐是让我们把要监听的变成一个函数(而不是直接.xxx.xxx得到的字符串，这样会报错的，因为不是Proxy所代理的对象)

- 创建一个回调函数去返回这个要侦听的属性

- ```ts
  //区别就是()=>message.value.nav.bar.name跟单纯message.value.nav.bar.name，后者会报错
  watch(()=>message.value.nav.bar.name,(newVal,oldVal)=>{//此时新值旧值也会变成一个数组
    console.log('新的值----', newVal);
    console.log('旧的值----', oldVal);
  },{deep:true})
  ```

### immediate

> watch侦听器 第三参数
>
> 开局立即执行一次

```ts
watch(()=>message.value.nav.bar.name,(newVal,oldVal)=>{//此时新值旧值也会变成一个数组
  console.log('新的值----', newVal);
  console.log('旧的值----', oldVal);
},{immediate:true})
```

### flush

> watch侦听器 第三参数
>
> 1

```ts
watch(()=>message.value.nav.bar.name,(newVal,oldVal)=>{//此时新值旧值也会变成一个数组
  console.log('新的值----', newVal);
  console.log('旧的值----', oldVal);
},{
    flush:"pre"//pre组件更新之前调用 sync同步执行 post组件更新之后做的执行
})
```

## 源码讲解 黄金流程

> 位于视频7分钟
>
> 源码位置：packages > runtime-core > src > apiWatch.ts > watch

```ts
// overload: watching reactive object w/ cb
export function watch<
  T extends object,
  Immediate extends Readonly<boolean> = false
>(
  source: T,
  cb: WatchCallback<T, Immediate extends true ? T | undefined : T>,
  options?: WatchOptions<Immediate>
): WatchStopHandle

// implementation
export function watch<T = any, Immediate extends Readonly<boolean> = false>(
  source: T | WatchSource<T>,
  cb: any,
  options?: WatchOptions<Immediate>
): WatchStopHandle {
  if (__DEV__ && !isFunction(cb)) {
    warn(
      `\`watch(fn, options?)\` signature has been moved to a separate API. ` +
        `Use \`watchEffect(fn, options?)\` instead. \`watch\` now only ` +
        `supports \`watch(source, cb, options?) signature.`
    )
  }
  return doWatch(source as any, cb, options)//核心函数调用了doWatch。source数据源，cd回调函数(新旧值)，option配置项
}

function doWatch(
//source支持4种模式，ref对象、reactive对象、数组侦听多个源，传入函数侦听单一属性
  source: WatchSource | WatchSource[] | WatchEffect | object,//第一件事情格式化source，将四种类型都赋给一个函数
  cb: WatchCallback | null,
  { immediate, deep, flush, onTrack, onTrigger }: WatchOptions = EMPTY_OBJ
): WatchStopHandle {
  if (__DEV__ && !cb) {
    if (immediate !== undefined) {
      warn(
        `watch() "immediate" option is only respected when using the ` +
          `watch(source, callback, options?) signature.`
      )
    }
    if (deep !== undefined) {
      warn(
        `watch() "deep" option is only respected when using the ` +
          `watch(source, callback, options?) signature.`
      )
    }
  }
```

```ts
  const warnInvalidSource = (s: unknown) => {
    warn(
      `Invalid watch source: `,
      s,
      `A watch source can only be a getter/effect function, a ref, ` +
        `a reactive object, or an array of these types.`
    )
  }
  //ref Reactive [msg,msg2] ()=>msg.bar.name
  const instance = currentInstance
  let getter: () => any
  let forceTrigger = false
  let isMultiSource = false
//如果是ref对象
  if (isRef(source)) {
    //创建一个getter函数并且读取了 ref对象的value属性
    getter = () => source.value
    forceTrigger = isShallow(source)
  } else if (isReactive(source)) {
    //如果是Reactive 对象直接返回一个getter 函数 并且设置deep 为true，所以就是刚刚上面说reactive不需要在手动打开deep的原因，因为源码已经默认开启了
    getter = () => source
    deep = true
  } else if (isArray(source)) {
    //如果是数组 就遍历该数组 然后处理里面的ref 和 Reactive
    isMultiSource = true
    forceTrigger = source.some(s => isReactive(s) || isShallow(s))
    getter = () =>
      source.map(s => {//对数组做了一个遍历
        if (isRef(s)) {
          return s.value
        } else if (isReactive(s)) {
          return traverse(s)//traverse是一个递归，会把里面的每个属性都做一个侦听
        } else if (isFunction(s)) {
          return callWithErrorHandling(s, instance, ErrorCodes.WATCH_GETTER)
        } else {
          __DEV__ && warnInvalidSource(s)
        }
      })
  } else if (isFunction(source)) {
    //如果source 是一个函数 则会判断 cb是否存在 getter就会对 source进行简单的封装
    if (cb) {//判断有无cb，决定走cb还是with effect
      // getter with cb
      getter = () =>
        callWithErrorHandling(source, instance, ErrorCodes.WATCH_GETTER)
    } else {
      // no cb -> simple effect
      getter = () => {
        if (instance && instance.isUnmounted) {
          return
        }
        if (cleanup) {
          cleanup()
        }
        return callWithAsyncErrorHandling(
          source,
          instance,
          ErrorCodes.WATCH_CALLBACK,
          [onCleanup]
        )
      }
    }
  } else {
    getter = NOOP
    __DEV__ && warnInvalidSource(source)
  }
```

```ts
//处理deep 深度监听
  if (cb && deep) {
    const baseGetter = getter
    getter = () => traverse(baseGetter())//traverse 一个比较耗时的递归
  }
```

```ts
  job.allowRecurse = !!cb
//调度都赋给scheduler
  let scheduler: EffectScheduler
  if (flush === 'sync') {//如果sync就同步执行
    scheduler = job as any // the scheduler function gets called directly
  } else if (flush === 'post') {//post的话又把值传给了job
    //组件更新之后执行 queuePostRenderEffect，源码中有看到这个就一定是在组件更新之后去做一个执行的
    scheduler = () => queuePostRenderEffect(job, instance && instance.suspense)
  } else {
    // default: 'pre'
    job.pre = true
    if (instance) job.id = instance.uid
    scheduler = () => queueJob(job)
  }

```

```ts
  let oldValue = isMultiSource ? [] : INITIAL_WATCHER_VALUE//初始化旧值
  const job: SchedulerJob = () => {
    if (!effect.active) {
      return
    }
      
      
      
    if (cb) {
      // watch(source, cb)
      const newValue = effect.run()
      if (
        deep ||
        forceTrigger ||
        (isMultiSource
          ? (newValue as any[]).some((v, i) =>
              hasChanged(v, (oldValue as any[])[i])
            )
          : hasChanged(newValue, oldValue)) ||
        (__COMPAT__ &&
          isArray(newValue) &&
          isCompatEnabled(DeprecationTypes.WATCH_ARRAY, instance))
      ) {
        // cleanup before running cb again
        if (cleanup) {
          cleanup()
        }
        callWithAsyncErrorHandling(cb, instance, ErrorCodes.WATCH_CALLBACK, [
          newValue,
          // pass undefined as the old value when it's changed for the first time
            //第一次执行旧值 是undefined 或者 空数组
          oldValue === INITIAL_WATCHER_VALUE ? undefined : oldValue,
          onCleanup
        ])
        //直接赋值，如果是对象旧直接引用了，所以新值和旧值是一样的。这个就是为什么引用类型新旧值没有变化
        oldValue = newValue
      }
    } else {
      // watchEffect
      effect.run()
    }
  }
```

# 第十一章 — watchEffect高级侦听器

- 跟watch是不一样的，watchEffect是非惰性的。函数会帮你自己去调用一下

**watchEffect**(一开始会自己自调用一次)

立即执行传入的一个函数，同时响应式追踪其依赖，并在其依赖变更时重新运行该函数。

如果用到 message 就只会[监听](https://so.csdn.net/so/search?q=监听&spm=1001.2101.3001.7020) message 就是用到几个监听几个 而且是非惰性 会默认调用一次

```javascript
let message = ref<string>('')
let message2 = ref<string>('')
 watchEffect(() => {
    //console.log('message', message.value);
    console.log('message2', message2.value);
})
```

### 清除副作用

就是在触发监听之前会调用一个函数可以处理你的逻辑例如防抖

```javascript
import { watchEffect, ref } from 'vue'
let message = ref<string>('')
let message2 = ref<string>('')
 watchEffect((oninvalidate) => {
    //console.log('message', message.value);
    oninvalidate(()=>{
        
    })
    console.log('message2', message2.value);
})
```

**停止跟踪 watchEffect 返回一个函数 调用之后将停止更新**

```javascript
const stop =  watchEffect((oninvalidate) => {
    //console.log('message', message.value);
    oninvalidate(()=>{
 
    })
    console.log('message2', message2.value);
},{
    flush:"post",
    onTrigger () {
 
    }
})
stop()
```

在watchEffect里面会先处理回调函数，在处理其他内容，所以我们可以在里面去进行防抖之类的操作

> **断言 (assertion)** 是一种在程序中的一阶逻辑 (如：一个结果为真或假的逻辑判断式)，目的为了表示与验证软件开发者预期的结果 —— 当程序执行到断言的位置时，对应的断言应该为真。若断言不为真时，程序会中止执行，并给出错误信息。

### 副作用刷新时机 flush 一般使用 post

|          | pre                | sync                 | post               |
| :------- | :----------------- | :------------------- | :----------------- |
| 更新时机 | 组件**更新前**执行 | 强制效果始终同步触发 | 组件**更新后**执行 |

# 第十二章 — 生命周期 & 源码讲解

|    选项式 API     | Hook inside `setup` |
| :---------------: | :-----------------: |
|  `beforeCreate`   |     Not needed*     |
|     `created`     |     Not needed*     |
|   `beforeMount`   |   `onBeforeMount`   |
|     `mounted`     |     `onMounted`     |
|  `beforeUpdate`   |  `onBeforeUpdate`   |
|     `updated`     |     `onUpdated`     |
|  `beforeUnmount`  |  `onBeforeUnmount`  |
|    `unmounted`    |    `onUnmounted`    |
|  `errorCaptured`  |  `onErrorCaptured`  |
|  `renderTracked`  |  `onRenderTracked`  |
| `renderTriggered` | `onRenderTriggered` |
|    `activated`    |    `onActivated`    |
|   `deactivated`   |   `onDeactivated`   |

1. > setup语法糖模式下是没有`beforeCreate` `created`这两个生命周期的
   >
   > 被setup代替 

   ```js
   onBeforeMount()
   
   //在组件 DOM 实际渲染安装之前调用。在这一步中，根元素还不存在。
   
   onMounted()
   
   //在组件的第一次渲染后调用，该元素现在可用，允许直接 DOM 访问
   
   onBeforeUpdate()
   
   //数据更新时调用，发生在虚拟 DOM 打补丁之前。
   
   onUpdated()
   
   //DOM 更新后，updated 的方法即会调用。
   
   onBeforeUnmount()
   
   //在卸载组件实例之前调用。在这个阶段，实例仍然是完全正常的。
   
   onUnmounted()
   
   //卸载组件实例后调用。调用此钩子时，组件实例的所有指令都被解除绑定，所有事件侦听器都被移除，所有子组件实例被卸载。
   ```

使用v-if触发伏笔：当组件绑定一个v-if的时候是会触发组件的创建跟销毁两个生命钩子的

## beforeMount[#](https://cn.vuejs.org/api/options-lifecycle.html#beforemount)

在组件被挂载之前调用。

> 就是创建之前

- **类型**

  ```ts
  interface ComponentOptions {
    beforeMount?(this: ComponentPublicInstance): void
  }
  ```

- **详细信息**

  当这个钩子被调用时，组件已经完成了其响应式状态的设置，但还没有创建 DOM 节点。它即将首次执行 DOM 渲染过程。

  **这个钩子在服务端渲染时不会被调用。**

## mounted[#](https://cn.vuejs.org/api/options-lifecycle.html#mounted)

在组件被挂载之后调用。

> 创建完成

- **类型**

  ```ts
  interface ComponentOptions {
    mounted?(this: ComponentPublicInstance): void
  }
  ```

- **详细信息**

  组件在以下情况下被视为已挂载：

  - 所有同步子组件都已经被挂载。(不包含异步组件或 `<Suspense>` 树内的组件)
  - 其自身的 DOM 树已经创建完成并插入了父容器中。注意仅当根容器在文档中时，才可以保证组件 DOM 树也在文档中。

  这个钩子通常用于执行需要访问组件所渲染的 DOM 树相关的副作用，或是在[服务端渲染应用](https://cn.vuejs.org/guide/scaling-up/ssr.html)中用于确保 DOM 相关代码仅在客户端被调用。

  **这个钩子在服务端渲染时不会被调用。**

## beforeUpdate[#](https://cn.vuejs.org/api/options-lifecycle.html#beforeupdate)

在组件即将因为一个响应式状态变更而更新其 DOM 树之前调用。

> 获取的是更新之前的DOM
>
> 例子：比如原本的文本是"小满穿黑丝"，更新完成后是"小满cos女装"。在这里所谓获取更新之前的DOM，获取的就是"小满穿黑丝"

- **类型**

  ```ts
  interface ComponentOptions {
    beforeUpdate?(this: ComponentPublicInstance): void
  }
  ```

- **详细信息**

  这个钩子可以用来在 Vue 更新 DOM 之前访问 DOM 状态。在这个钩子中更改状态也是安全的。

  **这个钩子在服务端渲染时不会被调用。**

## updated[#](https://cn.vuejs.org/api/options-lifecycle.html#updated)

在组件因为一个响应式状态变更而更新其 DOM 树之后调用。

> 获取的是更新之后的DOM

- **类型**

  ```ts
  interface ComponentOptions {
    updated?(this: ComponentPublicInstance): void
  }
  ```

- **详细信息**

  父组件的更新钩子将在其子组件的更新钩子之后调用。

  这个钩子会在组件的任意 DOM 更新后被调用，这些更新可能是由不同的状态变更导致的。如果你需要在某个特定的状态更改后访问更新后的 DOM，请使用 [nextTick()](https://cn.vuejs.org/api/general.html#nexttick) 作为替代。

  **这个钩子在服务端渲染时不会被调用。**

  WARNING

  不要在 updated 钩子中更改组件的状态，这可能会导致无限的更新循环！

## beforeUnmount[#](https://cn.vuejs.org/api/options-lifecycle.html#beforeunmount)

在一个组件实例被卸载之前调用。

- **类型**

  ```ts
  interface ComponentOptions {
    beforeUnmount?(this: ComponentPublicInstance): void
  }
  ```

- **详细信息**

  当这个钩子被调用时，组件实例依然还保有全部的功能。

  **这个钩子在服务端渲染时不会被调用。**

## unmounted[#](https://cn.vuejs.org/api/options-lifecycle.html#unmounted)

在一个组件实例被卸载之后调用。

- **类型**

  ```ts
  interface ComponentOptions {
    unmounted?(this: ComponentPublicInstance): void
  }
  ```

- **详细信息**

  一个组件在以下情况下被视为已卸载：

  - 其所有子组件都已经被卸载。
  - 所有相关的响应式作用 (渲染作用以及 `setup()` 时创建的计算属性和侦听器) 都已经停止。

  可以在这个钩子中手动清理一些副作用，例如计时器、DOM 事件监听器或者与服务器的连接。

  **这个钩子在服务端渲染时不会被调用。**

## errorCaptured[#](https://cn.vuejs.org/api/options-lifecycle.html#errorcaptured)

在捕获了后代组件传递的错误时调用。

- **类型**

  ```ts
  interface ComponentOptions {
    errorCaptured?(
      this: ComponentPublicInstance,
      err: unknown,
      instance: ComponentPublicInstance | null,
      info: string
    ): boolean | void
  }
  ```

- **详细信息**

  错误可以从以下几个来源中捕获：

  - 组件渲染
  - 事件处理器
  - 生命周期钩子
  - `setup()` 函数
  - 侦听器
  - 自定义指令钩子
  - 过渡钩子

  这个钩子带有三个实参：错误对象、触发该错误的组件实例，以及一个说明错误来源类型的信息字符串。

  你可以在 `errorCaptured()` 中更改组件状态来为用户显示一个错误状态。然而重要的是，不要让错误状态渲染为导致本次错误的内容，否则组件就会进入无限的渲染循环中。

  这个钩子可以通过返回 `false` 来阻止错误继续向上传递。请看下方的传递细节介绍。

  **错误传递规则**

  - 默认情况下，所有的错误都会被发送到应用级的 [`app.config.errorHandler`](https://cn.vuejs.org/api/application.html#app-config-errorhandler) (前提是这个函数已经定义)，这样这些错误都能在一个统一的地方报告给分析服务。
  - 如果组件的继承链或组件链上存在多个 `errorCaptured` 钩子，对于同一个错误，这些钩子会被按从底至上的顺序一一调用。这个过程被称为 “向上传递”，类似于原生 DOM 事件的冒泡机制。
  - 如果 `errorCaptured` 钩子本身抛出了一个错误，那么这个错误和原来捕获到的错误都将被发送到 `app.config.errorHandler`。
  - `errorCaptured` 钩子可以通过返回 `false` 来阻止错误继续向上传递。即表示 “这个错误已经被处理了，应当被忽略”，它将阻止其他的 `errorCaptured` 钩子或 `app.config.errorHandler` 因这个错误而被调用。

## renderTracked [#](https://cn.vuejs.org/api/options-lifecycle.html#rendertracked)

在一个响应式依赖被组件的渲染作用追踪后调用。(英译中：渲染追踪)

**这个钩子仅在开发模式下可用，且在服务器端渲染期间不会被调用。**

> 收集依赖创建的钩子，调试用的

- **类型**

  ```ts
  interface ComponentOptions {
    renderTracked?(this: ComponentPublicInstance, e: DebuggerEvent): void
  }
  
  type DebuggerEvent = {
    effect: ReactiveEffect
    target: object
    type: TrackOpTypes /* 'get' | 'has' | 'iterate' */
    key: any
  }
  ```

- **参考**：[深入响应式系统](https://cn.vuejs.org/guide/extras/reactivity-in-depth.html)

## renderTriggered [#](https://cn.vuejs.org/api/options-lifecycle.html#rendertriggered-sup-classvt-badge-dev-only)

在一个响应式依赖被组件触发了重新渲染之后调用。英译中：渲染触发

**这个钩子仅在开发模式下可用，且在服务器端渲染期间不会被调用。**

> 触发依赖更新，调试用的

- **类型**

  ```ts
  interface ComponentOptions {
    renderTriggered?(this: ComponentPublicInstance, e: DebuggerEvent): void
  }
  
  type DebuggerEvent = {
    effect: ReactiveEffect
    target: object
    type: TriggerOpTypes /* 'set' | 'add' | 'delete' | 'clear' */
    key: any
    newValue?: any
    oldValue?: any
    oldTarget?: Map<any, any> | Set<any>
  }
  ```

- **参考**：[深入响应式系统](https://cn.vuejs.org/guide/extras/reactivity-in-depth.html)

## activated[#](https://cn.vuejs.org/api/options-lifecycle.html#activated)

若组件实例是 [``](https://cn.vuejs.org/api/built-in-components.html#keepalive) 缓存树的一部分，当组件被插入到 DOM 中时调用。

**这个钩子在服务端渲染时不会被调用。**

- **类型**

  ```ts
  interface ComponentOptions {
    activated?(this: ComponentPublicInstance): void
  }
  ```

- **参考**：[指南 - 被缓存实例的生命周期](https://cn.vuejs.org/guide/built-ins/keep-alive.html#lifecycle-of-cached-instance)

## deactivated[#](https://cn.vuejs.org/api/options-lifecycle.html#deactivated)

若组件实例是 [``](https://cn.vuejs.org/api/built-in-components.html#keepalive) 缓存树的一部分，当组件从 DOM 中被移除时调用。

**这个钩子在服务端渲染时不会被调用。**

- **类型**

  ```ts
  interface ComponentOptions {
    deactivated?(this: ComponentPublicInstance): void
  }
  ```

- **参考**：[指南 - 被缓存实例的生命周期](https://cn.vuejs.org/guide/built-ins/keep-alive.html#lifecycle-of-cached-instance)

## serverPrefetch [#](https://cn.vuejs.org/api/options-lifecycle.html#serverprefetch)

当组件实例在服务器上被渲染之前要完成的异步函数。

- **类型**

  ```ts
  interface ComponentOptions {
    serverPrefetch?(this: ComponentPublicInstance): Promise<any>
  }
  ```

- **详细信息**

  如果这个钩子返回了一个 Promise，服务端渲染会在渲染该组件前等待该 Promise 完成。

  这个钩子仅会在服务端渲染中执行，可以用于执行一些仅在服务端才有的数据抓取过程。

- **示例**

  ```js
  export default {
    data() {
      return {
        data: null
      }
    },
    async serverPrefetch() {
      // 组件会作为初次请求的一部分被渲染
      // 会在服务端预抓取数据，因为这比客户端更快
      this.data = await fetchOnServer(/* ... */)
    },
    async mounted() {
      if (!this.data) {
        // 如果数据在挂载时是 null，这意味着这个组件
        // 是在客户端动态渲染的，请另外执行一个
        // 客户端请求作为替代
        this.data = await fetchOnClient(/* ... */)
      }
    }
  }
  ```

## 源码解析

> 位置：runtime-core下的apiLifecycle.ts中

- 钩子函数(简称)挂载到组件自身的实例上

- 通过getCurrertInstance可以获取当前组件的实例

- 通过获取组件实例，在控制台就能直接看到生命周期已经挂载上来了，虽然是简称

- 然后做了一个createHook，函数柯里化进行封装

- > 柯里化：在计算机科学中，柯里化（Currying）是把接受多个参数的函数变换成接受一个单一参数 (最初函数的第一个参数) 的函数，并且返回接受余下的参数且返回结果的新函数的技术。

- 然后调用injectHook这个函数去做一个缓存 =>

  如果第一次没有值的化设为空数组，有值的话将直接读取

- 在下面使用了push将warppedHook做一个添加

- 对warppeHook进行了一个判断，判断当前组件`target.isUnmounted`是否卸载掉，是的话直接return返回。不是的话根据源码注释可以得知为停止收集依赖，避免重复收集依赖

- 然后设置当前darget为组件实例，接着执行钩子函数，清空当前组件实例，恢复收集依赖

> **在renderer.ts文件夹中对每个阶段都进行一个调用**

- 在componentUpdateFn中做一个调用
- 做一个判断，当前组件如果没有挂载的话走没挂载组件的判断，否则就是更新
- 挂载阶段
  - 先执行一下invokeArrayFns钩子，然后做一些渲染，进行一个挂载到容器中，此时就有了el也就是DOM了
  - 然后就开始判断有没有m，也就是枚举，有的话就执行`queuePostRenderEffect`，执行mounted的一个生命周期

- 更新
- 卸载
  - 卸载完后执行onunmounted证明卸载完成

# 第十三章 — 组件认识less & scoped(旧)

**Less （Leaner Style Sheets 的缩写） 是一门向后兼容的 CSS 扩展语言。这里呈现的是 Less 的官方文档（中文版），包含了 Less 语言以及利用 JavaScript 开发的用于将 Less 样式转换成 CSS 样式的 Less.js 工具。**

> 需要安装一下(友情提示装到根目录下面，例如Vite-App)
>
> 命令：npm install less less-loder -D(大D就是安装到开发依赖)
>
> index的命名能够直接识别

 layout分区：

- index.vue(然后把布局返回App.vue)

  - Menu =>index.vue =>表单

  - Header =>index.vue=> 头部

  - Content =>index.vue=> 主体内容

> 在main.ts进行引入，`import './assets/css/reset.less'`

在css里

1. &-xxx：{}是副级，能写在其他大属性的里面

2. <style lang="less" scoped>，scoped的作用就是隔离样式的，我们这个是单组件的，每个页面里可能会有重名的样式名字称，使用这个scoped将其隔断，能够有效的防止在打包的时候文件之间重名的冲突影响报错问题 

# 第十三章 BEM架构 + layout布局(新)

> 布局（Layout）是指在网页设计中，将各个元素（包括导航、标题、正文、图片、表单等）放置在恰当的位置，以实现美观、易读、易用等目标的过程。在 Web 开发中，布局是 CSS 的一个重要应用领域，负责组织和安排页面上的各个内容模块，使它们在各种屏幕尺寸和设备上都能呈现出良好的结构和视觉效果。
>
> 布局的核心目标包括：
>
> 1. 确保内容在不同的屏幕尺寸和设备上能够自适应，包括桌面电脑、平板电脑和移动设备等。
> 2. 使页面上的元素以一种易于理解和使用的方式呈现，有助于提高用户体验。
> 3. 提高页面的视觉美观性，使之更具吸引力。
>
> 在 CSS 中，有多种布局方法可以实现这些目标，包括：
>
> 1. 块级布局（Block Layout）：基于常规的文档流，将元素按照块级和内联元素的方式排列。
> 2. 浮动布局（Float Layout）：通过设置元素的浮动属性，使其脱离文档流并向左或向右移动，直到碰到容器边界或其他浮动元素。
> 3. 定位布局（Positioning Layout）：使用相对定位、绝对定位、固定定位等 CSS 属性，直接设置元素在页面上的位置。
> 4. Flexbox 布局：一种现代的、弹性的布局模型，允许通过简单的 CSS 属性在一维空间内对元素进行灵活的排列和对齐。
> 5. 网格布局（Grid Layout）：另一种现代布局模型，适用于在二维空间内创建复杂的页面结构和对齐元素。

- 而小满举例的是Element Plus组件库的`Layout container`布局

![image-20230425133245664](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20230425133245664-1711983932538-14.png)

- 小满在这一集中使用sass预处理器，css架构使用BEM架构。所以在课程正式开始前，可以先补充一下前置知识

## 什么是BEM架构

> BEM官网：[BEM — Block Element Modifier (getbem.com)](https://getbem.com/)
>
> BEM（Block Element Modifier）是一种命名和组织 CSS 类的方法论。它旨在提高 CSS 代码的可读性和可维护性，通过为 CSS 类名提供一致的结构，使得开发者能够更容易地理解代码的结构和关系。BEM 采用了一种明确的命名约定，将代码分为三个部分：块（Block）、元素（Element）和修饰符（Modifier）。

在 Element Plus 组件库中，BEM 架构可以帮助我们更好地组织和命名组件样式。

1. 块（Block）：独立的实体，具有明确的功能。在 Element Plus 中，一个组件可以被视为一个块。例如，按钮（Button）组件。块的命名使用单词，多个单词间用连字符（-）连接。

   示例：`.el-button`

2. 元素（Element）：块的一部分，具有某种功能，但不能独立存在。元素的命名使用双下划线（__）连接到块的名称后面。

   示例：`.el-button__icon`（按钮组件中的图标元素）

3. 修饰符（Modifier）：表示块或元素的不同状态或变种。修饰符的命名使用双中划线线（--）连接到块或元素的名称后面。

   示例：`.el-button--primary`（表示按钮组件的主要样式）

在 Element Plus 组件库中，遵循 BEM 架构的 CSS 类名可以帮助我们更轻松地理解各个组件的结构和样式，同时提高代码的可读性和可维护性

![image-20230425134213521](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20230425134213521-1711983932538-15.png)

## Sass基础语法

> Sass官网：[Sass教程 Sass中文文档 | Sass中文网](https://www.sass.hk/docs/)

### 嵌套写法

> 使用嵌套写法时要注意避免过度嵌套，以保持代码的可读性

1. 选择器嵌套：在 Sass 中，你可以将子选择器嵌套在父选择器中，这样可以减少代码的重复，并且使代码更加结构化。

```scss
.parent {
  color: blue;

  .child {
    font-size: 16px;
  }
}
```

- 编译后的 CSS：

```css
.parent {
  color: blue;
}

.parent .child {
  font-size: 16px;
}
```

2. 使用 `&` 符号：在 Sass 中，`&` 符号代表当前选择器(父选择器)。你可以使用它来表示伪类、伪元素或者创建修改器等

```scss
.button {
  background-color: red;

  &:hover {
    background-color: darkred;
  }

  &_primary {
    background-color: blue;
  }
}
```

- 编译后的CSS：

```css
.button {
  background-color: red;
}

.button:hover {
  background-color: darkred;
}

.button_primary {
  background-color: blue;
}
```

3. 媒体查询和其他指令的嵌套：你还可以将媒体查询和其他指令嵌套在选择器内部，这样可以使代码更加有组织

```scss
.element {
  width: 50%;

  @media (max-width: 768px) {
    width: 100%;
  }
}
```

- 编译后的 CSS：

```css
.element {
  width: 50%;
}

@media (max-width: 768px) {
  .element {
    width: 100%;
  }
}
```

### 变量

> 在 SCSS 中，变量是一种存储值的方法，可以帮助你管理和复用样式。这些值可以是颜色、字体大小、边距等。使用变量可以让你的代码更加整洁、一致且易于维护

1. 定义变量：在 SCSS 中，变量以 `$` 符号开始，后跟变量名和赋值。变量名通常使用小写字母和下划线来表示

```scss
$primary-color: #4a90e2;
$secondary-color: #f5a623;
$font-size: 16px;
$padding: 10px;
```

2. 使用变量：定义了变量后，你可以在样式中使用它们。将变量名替换为实际值时，只需在变量名前加上 `$` 符号即可

```scss
.button {
  background-color: $primary-color;
  font-size: $font-size;
  padding: $padding;
}

.alert {
  background-color: $secondary-color;
  font-size: $font-size;
  padding: $padding;
}
```

- 编译后的 CSS：

```css
.button {
  background-color: #4a90e2;
  font-size: 16px;
  padding: 10px;
}

.alert {
  background-color: #f5a623;
  font-size: 16px;
  padding: 10px;
}
```

3. 变量的作用域：SCSS 中的变量具有作用域，即变量只在其所在的代码块（大括号内）中有效。你可以在嵌套的代码块中定义具有相同名称的变量，该变量仅在嵌套的代码块中生效，而不会影响外部的同名变量。

```scss
$padding: 10px;

.button {
  $padding: 20px; // 这里定义的变量仅在 .button 选择器内有效
  padding: $padding;
}

.alert {
  padding: $padding; // 这里使用的是全局变量 $padding
}
```

- 编译后的 CSS：

```css
.button {
  padding: 20px;
}

.alert {
  padding: 10px;
}
```

### 插值语法

> 在 SCSS 中，插值语法（Interpolation）是一种将变量或表达式的值嵌入到字符串或选择器中的方法。插值语法使用 `#{}` 结构来包裹变量或表达式。

1. 动态生成类名：

```scss
$size: 'small';

.button-#{$size} {/*是不是很熟悉，el-xxx的即视感*/
  font-size: 12px;
}
```

- 编译后的 CSS：

```css
.button-small {
  font-size: 12px;
}
```

2. 动态生成属性名：

```scss
$direction: 'margin';

.box {
  #{$direction}-top: 10px;
  #{$direction}-bottom: 20px;
}
```

- 编译后的 CSS：

```css
.box {
  margin-top: 10px;
  margin-bottom: 20px;
}
```

3. 动态生成媒体查询：

```scss
$breakpoint: 768px;

.container {
  width: 100%;

  @media (min-width: #{$breakpoint}) {
    width: 80%;
  }
}
```

- 编译后的 CSS：

```css
.container {
  width: 100%;
}

@media (min-width: 768px) {
  .container {
    width: 80%;
  }
}
```

4. 在字符串中插入变量值：

```scss
$width: 50;

.element {
  width: #{$width}%;
  content: "Width is #{$width}%";
}
```

- 编译后的 CSS：

```css
.element {
  width: 50%;
  content: "Width is 50%";
}
```

### @at-root

> 在 SCSS 中，`@at-root` 指令用于将嵌套的规则或声明提升到根级别，人话：追加上父级的类名。这对于在复杂的嵌套结构中创建全局样式或需要在特定上下文之外定义规则时非常有用

```scss
/*语法*/
@at-root {
  // 规则或声明
}
```

- 在 `@at-root` 内部编写的规则或声明将被提升到根级别。如果在嵌套规则内部使用 `@at-root`，那么它将跳过所有嵌套层级，将该规则或声明放在根级别

1. 在嵌套规则内创建全局样式：

```scss
.container {
  padding: 20px;

  @at-root {
    .global-title {
      font-size: 24px;
      font-weight: bold;
    }
  }
}
```

- 编译后的 CSS：

```css
.container {
  padding: 20px;
}

.global-title {
  font-size: 24px;
  font-weight: bold;
}
```

2. 在嵌套规则内将部分样式提升到根级别：

```scss
.parent {
  background-color: #f5f5f5;

  .child {
    color: #333;
    font-size: 14px;

    @at-root {
      .child:hover {
        color: #666;
      }
    }
  }
}
```

- 编译后的 CSS：

```css
.parent {
  background-color: #f5f5f5;
}

.parent .child {
  color: #333;
  font-size: 14px;
}

.child:hover {
  color: #666;
}
```

- 通过使用 `@at-root` 指令，你可以轻松地在嵌套规则内创建全局样式或提升部分样式到根级别。这样可以使得代码更加清晰和有组织
- 因为我们等等在使用BEM的时候，已经是一个块了，没必要再追加上父级的类名，所以选择跳出嵌套

### 混入语法

> 在 SCSS 中，混入（mixin）是一种创建可重用的样式片段的方法。混入可以包含 CSS 规则、属性、选择器等，并且可以接收参数，使得它们变得更加灵活和可配置。要使用混入，需要定义一个混入，并在需要的地方通过 `@include` 指令将其包含到其他样式中

- 语法模板：

```scss
@mixin mixin-name($param1, $param2, ...) {
  // 规则或声明
}
```

- 要包含一个混入，使用 `@include` 指令：

```scss
.selector {
  @include mixin-name($arg1, $arg2, ...);
}
```

#### 混入案例

> 定义一个用于生成圆角边框的混入：

```scss
/*我们定义了一个名为 rounded-border 的混入，它接受一个名为 $radius 的参数，默认值为 5px */
@mixin rounded-border($radius: 5px) {
  border-radius: $radius;
  border: 1px solid #ccc;
}
```

- 在其他选择器中使用混入：

```scss
/*我们使用 @include 指令将 rounded-border 混入包含到 .button 和 .alert 选择器中。对于 .button 选择器，我们使用了混入的默认参数值。对于 .alert 选择器，我们传递了自定义的边框半径值 10px*/
.button {
  @include rounded-border;
  background-color: #007bff;
  color: #fff;
  padding: 8px 16px;
}

.alert {
  @include rounded-border(10px);
  background-color: #f8d7da;
  color: #721c24;
  padding: 16px;
}
```

- 编译后的 CSS：

```css
.button {
  border-radius: 5px;
  border: 1px solid #ccc;
  background-color: #007bff;
  color: #fff;
  padding: 8px 16px;
}

.alert {
  border-radius: 10px;
  border: 1px solid #ccc;
  background-color: #f8d7da;
  color: #721c24;
  padding: 16px;
}
```

### 参数用法

> 在 SCSS 中，混入（mixin）和函数（function）可以接受参数。参数允许你在调用混入或函数时传递自定义值，从而使得混入或函数具有更高的灵活性和可配置性。参数在定义混入或函数时用括号括起来，并用逗号分隔

1. 必选参数：必须在调用混入或函数时提供值，否则会导致编译错误

```scss
// 定义一个名为 box-shadow 的混入，接受四个参数：$x、$y、$blur 和 $color
@mixin box-shadow($x, $y, $blur, $color) {
  // 为元素应用 box-shadow 样式
  box-shadow: $x $y $blur $color;
}

// 使用 .button 类选择器
.button {
  // 调用 box-shadow 混入并传递四个参数值
  @include box-shadow(0, 1px, 3px, rgba(0, 0, 0, 0.3));
}
```

2. 默认参数值：可以为参数提供一个默认值，在调用混入或函数时，如果没有传递该参数的值，则使用默认值

```scss
// 定义一个名为 box-shadow 的混入，参数带有默认值
@mixin box-shadow($x: 0, $y: 1px, $blur: 3px, $color: rgba(0, 0, 0, 0.3)) {
  // 为元素应用 box-shadow 样式
  box-shadow: $x $y $blur $color;
}

// 使用 .button 类选择器
.button {
  // 调用 box-shadow 混入并传递一个参数值，其余参数使用默认值
  @include box-shadow($color: rgba(0, 0, 0, 0.5));
}
```

3. 可变参数（Variable arguments）：使用 `...` 语法可以接收任意数量的参数。这在你需要传递一个不确定数量的值时非常有用，例如处理多个边框或阴影

```scss
// 定义一个名为 box-shadows 的混入，接受可变参数 $shadows
@mixin box-shadows($shadows...) {
  // 为元素应用 box-shadow 样式
  box-shadow: $shadows;
}

// 使用 .button 类选择器
.button {
  // 调用 box-shadows 混入并传递两个阴影值
  @include box-shadows(0 1px 3px rgba(0, 0, 0, 0.3), 0 2px 6px rgba(0, 0, 0, 0.2));
}
```

4. 关键字参数（Keyword arguments）：在调用混入或函数时，可以使用关键字参数的形式传递值。这可以提高代码的可读性，特别是在参数较多的情况下

```scss
// 定义一个名为 box-shadow 的混入，接受四个参数：$x、$y、$blur 和 $color
@mixin box-shadow($x, $y, $blur, $color) {
  // 为元素应用 box-shadow 样式
  box-shadow: $x $y $blur $color;
}

// 使用 .button 类选择器
.button {
  // 使用关键字参数的形式调用 box-shadow 混入并传递四个参数值
  @include box-shadow($x: 0, $y: 1px, $blur: 3px, $color: rgba(0, 0, 0, 0.3));
}
```

## 编写BEM架构和全局sass属性

1. 安装 Sass： 首先，需要安装 Sass。可以通过两种方式来安装：使用包管理器（如 npm 或 yarn 或pnpm）或者使用 Dart Sass（推荐）

```
pnpm install sass
```

2. 创建 Sass 文件： 创建一个新的文件夹，例如 `sass-project`，然后在文件夹中创建一个新的 `.scss` 文件，例如 `BEM.scss`。就可以在这个文件中编写 Sass 代码

3. 编写  bem 架构 的 Sass 代码：

   > `!default` 标志表示，如果该变量已经被定义，那么这里的定义将不会覆盖原有的定义。这允许用户在这些默认值之前自定义变量，从而更改配置。
   >
   > 通俗的说就是，如果$namespace这个变量没有赋过其他值就默认采用"xy"这个默认值

```scss
//设置 BEM 命名约定的相关配置

// 定义一个命名空间变量，默认值为 "xy"
$namespace: "xy" !default;

// 定义一个用于分隔块名（Block）的分隔符变量，默认值为 "-"
$block-sel: "-" !default;

// 定义一个用于分隔元素名（Element）的分隔符变量，默认值为 "__"
$elem-sel: "__" !default;

// 定义一个用于分隔修饰符名（Modifier）的分隔符变量，默认值为 "--"
$mod-sel: "--" !default;


//定义了三个 Sass 混入（mixin），用于方便地生成 BEM 命名约定的类名
// 定义一个名为 b (Block) 的混入，接受一个参数 $block
@mixin b($block) {
  // 将命名空间、块名分隔符和 $block 参数拼接在一起，创建一个新的变量 $B
  $B: #{$namespace + $block-sel + $block};
  
  // 使用 $B 变量作为生成的 CSS 类名
  .#{$B} {
    // 插入混入内部传入的内容(相当于一个占位符，到时候会替换成我们真正的样式，理解为slot插槽)
    @content;
  }
}

// 定义一个名为 e (Element) 的混入，接受一个参数 $el
@mixin e($el) {
  // 将当前选择器存储在变量 $selector 中
  $selector: &;
  
  // 在当前选择器的根级别生成元素类名，跳出嵌套
  @at-root {
    // 使用 $selector、元素名分隔符和 $el 参数拼接生成的 CSS 类名
    #{$selector + $elem-sel + $el} {
      // 插入混入内部传入的内容
      @content;
    }
  }
}

// 定义一个名为 m (Modifier) 的混入，接受一个参数 $m
@mixin m($m) {
  // 将当前选择器存储在变量 $selector 中
  $selector: &;
  
  // 在当前选择器的根级别生成修饰符类名
  @at-root {
    // 使用 $selector、修饰符名分隔符和 $m 参数拼接生成的 CSS 类名
    #{$selector + $mod-sel + $m} {
      // 插入混入内部传入的内容
      @content;
    }
  }
}
```

### sass文件全局生效

- 错误的引入方法

```scss
//用不了的，需要通过配置项去配置才能够变成全局通用的样式
@import url()
```

### Vite.config.ts的文件配置

> 配置 SCSS 预处理器，使得在每个 SCSS 文件中自动导入 `bem.scss` 文件，从而可以在项目的任何 SCSS 文件中直接使用 `bem.scss` 中定义的混入、变量
>
> - PS：`preprocessorOptions` 是 Vite 配置中的一个选项，用于配置 CSS 预处理器（例如 SCSS、Less、Stylus 等）的选项。当你在项目中使用这些预处理器时，你可能需要指定一些全局配置，例如全局变量、混入等。`preprocessorOptions` 使你可以在 Vite 配置中为预处理器提供这些全局配置。

```typescript
// 从 'vite' 包中导入 defineConfig 函数
import { defineConfig } from 'vite'

// 从 '@vitejs/plugin-vue' 包中导入 Vue 插件
import vue from '@vitejs/plugin-vue'

// 使用 defineConfig 函数来定义 Vite 配置
// 参考文档: https://vitejs.dev/config/
export default defineConfig({
    // 将 Vue 插件添加到插件数组中
    plugins: [vue()],
    
    // 配置 CSS 预处理器选项
    css: {
        preprocessorOptions: {
            // 配置 scss 预处理器选项
            scss: {
                // 在所有 scss 文件中自动导入 'src/bem.scss' 文件
                additionalData: "@import './src/bem.scss';"
            }
        }
    }
})
```

- 在 Vue 文件中进行使用

```vue
<script lang="ts" setup>
</script>

<template>
 <div class="xy-test">
	 小满zs
 	<div class="xy-test__inner">el</div>
 	<div class="xy-test--success">成功</div>
 </div>
</template>
 
<style lang="scss" scoped>
/*使用名为 'test' 的块（block）创建一个 BEM 结构*/ 
@include b(test) {
    /*设置该块的文字颜色为红色*/
    color: red;

    /*为名为 'inner' 的元素（element）创建一个 BEM 结构，并放在 'test' 块内*/
    @include e(inner) {
        /*设置该元素的文字颜色为蓝色*/
        color: blue;
    }

    /*为名为 'success' 的修饰符（modifier）创建一个 BEM 结构，并放在 'test' 块内*/
    @include m(success) {
        /*设置该修饰符的文字颜色为浅绿色*/
        color: aqua;
    }
}
</style>
```

## Layout布局

> 创建项目... 启动项目...reset.css清除默认样式...

- 编写scss文件
  - 在刚刚那个bem.scss文件继续加内容

```scss
@mixin bfc {
  height:100%;
  overflow:hidden
}
//中途可能报错，重启一下就行了，可能是因为缓存的问题

```

- Menu文件夹下的index.vue

```vue
<template>
  <div class="xy-menu"> Menu </div>
</template>

<script setup lang="ts">
import {ref,reactive} from "vue"
</script>

<style scoped lang="scss">
@include b(menu){
  min-width: 200px;
  border-right: 1px solid #ccc;
  height: 100%;
}
</style>
```

- Content文件夹下的index.vue

```vue
<template>
  <div class="xy-content"> 
  	<div v-for="item in 100">{{item}}</div>
  </div>
</template>

<script setup lang="ts">
import {ref,reactive} from "vue"
</script>

<style scoped lang="scss">
@include b(content){
  flex:1
  overflow:auto/*内容自适应*/
    @include e(items){
      padding:10px;
      margin:10px;
      border:1px soild #ccc
      border-radius: 4px 
  }
}
</style>
```

- Layout文件夹下的index.vue
  - 用来编写Layout样式

```vue
<template>
    <div class="xy-wraps">
         <div>
            <Menu></Menu>
         </div>
         <div class="xy-wraps__right">
            <Header></Header>
            <Content></Content>
         </div>
    </div>
</template>
 
<script lang="ts" setup>
import { ref, reactive } from "vue"
import Menu from './Menu/index.vue'
import Content from './Content/index.vue'
import Header from './Header/index.vue'
</script>
 
<style lang="scss" scoped>
/*使用名为 'wraps' 的块（block）创建一个 BEM 结构*/
@include b('wraps') {
    /*应用清除浮动的辅助方法（bfc: Block Formatting Context）*/
    @include bfc;
    
    /*应用 display: flex 的辅助方法，使该元素变为 flex 容器*/
    @include flex;
    
    /*为名为 'right' 的元素（element）创建一个 BEM 结构，并放在 'wraps' 块内*/
    @include e(right) {
        /*设置 flex-grow、flex-shrink 和 flex-basis 的缩写属性*/
        flex: 1;
        
        /*设置元素为 flex 容器*/ 
        display: flex;
        
        /*设置 flex 容器的主轴方向为纵向*/
        flex-direction: column;
    }
}
</style>
```

> style中加上scoped的作用：
>
> 在 Vue 文件中，当你为 `<style>` 标签添加 `scoped` 属性时，这个作用域的 CSS 将只应用于当前组件。这是一种局部范围样式的方式，可以确保组件样式不会泄露到全局范围，从而避免样式冲突。
>
> 这是如何实现的呢？Vue 通过为组件内的 HTML 元素添加一个唯一的属性（例如 `data-v-3d5e5f5a`），然后为每个在 `<style scoped>` 内定义的选择器添加相同的属性选择器，从而使得样式规则只匹配这个特定的组件。因为vue是SPA应用，只有一个index.html

举个栗子：

- 在一个使用 `scoped` 的 Vue 组件中

```vue
<template>
  <div class="example">
    Hello world
  </div>
</template>

<style scoped>
.example {
  color: red;
}
</style>
```

- 当 Vue 处理这个组件时，生成的 HTML 和 CSS 将类似于以下代码：
  - 3d5e5f5a 是 哈希值

```vue
<div class="example" data-v-3d5e5f5a>
  Hello world
</div>

<style>
.example[data-v-3d5e5f5a] {
  color: red;
}
</style>
```

- 这样一来，你的样式就被限制在了当前组件的范围内。需要注意的是，使用 `scoped` 时，父组件的样式不会渗透到子组件中，但子组件的根元素会同时受到父组件和子组件的样式影响。

### Element Plus组件库按钮例子

```scss
// 全局变量和配置
$namespace: "el-";
$block-sel: "";
$elem-sel: "__";
$mod-sel: "--";

// BEM 混入
@mixin b($block) {
  $B: #{$namespace + $block-sel + $block};
  .#{$B} {
    @content;
  }
}

@mixin m($m) {
  $selector: &;
  @at-root {
    #{$selector + $mod-sel + $m} {
      @content;
    }
  }
}

// 主题颜色变量
$primary-color: #409eff;
$success-color: #67c23a;
$warning-color: #e6a23c;
$danger-color: #f56c6c;

// 按钮基本样式
@include b(button) {
  display: inline-block;
  padding: 12px 20px;
  font-size: 14px;
  line-height: 1;
  text-align: center;
  border: 1px solid transparent;
  border-radius: 4px;
  cursor: pointer;
  transition: all 0.3s ease;
  user-select: none;
  outline: none;

  // 按钮类型修改器
  @include m(primary) {
    background-color: $primary-color;
    color: #fff;
  }

  @include m(success) {
    background-color: $success-color;
    color: #fff;
  }

  @include m(warning) {
    background-color: $warning-color;
    color: #fff;
  }

  @include m(danger) {
    background-color: $danger-color;
    color: #fff;
  }

  // 按钮大小修改器
  @include m(small) {
    padding: 9px 16px;
    font-size: 12px;
  }

  @include m(mini) {
    padding: 7px 14px;
    font-size: 10px;
  }
}
```

- 在Vue文件中进行使用

```vue
<template>
  <div class="example">
    <button class="el-button el-button--primary">Primary</button>
    <button class="el-button el-button--success">Success</button>
    <button class="el-button el-button--warning">Warning</button>
    <button class="el-button el-button--danger">Danger</button>
    <br>
    <button class="el-button el-button--small">Small</button>
    <button class="el-button el-button--mini">Mini</button>
  </div>
</template>

<script setup lang="ts">

</script>

<style scoped lang="scss">
@import './button-styles.scss';

.example {
  display: flex;
  flex-direction: column;
  gap: 10px;
}
</style>
```

# 第十四章 — 父子组件传参

## Props(基础用法讲解)

> 一个组件需要显式声明它所接受的 props，这样 Vue 才能知道外部传入的哪些是 props，哪些是透传 attribute

### 父传子(组件)

props 需要使用 [`props`](https://cn.vuejs.org/api/options-state.html#props) 选项来定义、除了使用字符串数组来声明 prop 外，还可以使用对象的形式

- 在父组件想要传递一些稍微复杂的东西的话就需要使用`v-band`去进行绑定在你那个子组件上面，`v-band`可以直接简写为`：`，`:data="list"` ，然后就能够直接去子组件存入Props中然后使用了

  - 父组件通过 v-bind 绑定个数据，然后子组件通过 defineProps 接受传过来的值(字符串数据类型不需要v-bind)

  - 子组件接受值

    通过 defineProps 来接受 **defineProps 是无须引入的直接使用即可**(defineProps有返回值，返回值就是接收到的props属性)

    ```js
    //文件water-fall.vue
    <template>
      <div>子集</div>
      <div>值：{{title}}</div>
    </template>
    
    <script setup lang="ts">
    //defineProps是宏函数。接收父组件传过来的值(没有使用ts的话他就是一个函数，然后里面接收一个对象)------->无TS写法
    defineProps({
      title:{
        type:String,
        default:"小满最近在微信群天天送礼物"
      }
    })//通过内置函数defineProps完成传递，即可在子组件中直接使用在上面定义好的内容
    </script>
    
    <style scoped>
    
    </style>
    ```
  
  
  ### TypeScript字面量写法
  
  > 使用传递字面量类型的纯类型语法做为参数
  
  ```ts
  //water-fall.vue文件
  <template>
    <div>子集</div>
    <div>小余：{{xiaoyu}}</div>
    <div>小满今年：{{xiaoman}}岁了</div>
  </template>
  
  <script setup lang="ts">
  
  defineProps<{//字面量写法，想要传输多个值就继续往下加，名字要跟父组件的一样(其实就是定义父组件传过来的值的类型，不符合就拦截)
    xiaoyu:String
    xiaoman:number
  }>()
  </script>
  
  <style scoped>
  
  </style>
  --------------------------------------------------------------------
  //App.vue文件
  <template>
  <water-fall :xiaoyu="name" :xiaoman="num"></water-fall>
  </template>
  
  <script setup lang="ts">
  let name="我是小满的课代表";
  let num = 3;
  import WaterFall from "./components/Loading/water-fall.vue";
  </script>
  
  <style scoped>
  
  </style>
  
  ```
  
  ### TypeScript中父传子设置默认值
  
  > 有没有发现你从js的写法转换成TS的字面量写法之后，没办法设置默认值了，其实不是没用办法，而是通过TS特有的方式实现了
  >
  > 让我们介绍`withDefault`，这是一个函数withDefault(XXX)，XXX填的只能是defineProps
  >
  > withDefaults 是个函数也是无须引入开箱即用接受一个 props 函数第二个参数是一个对象设置默认值
  
  ```ts
  //TS 特有定义默认值 withDefault写法
  withDefaults(defineProps<{
    xiaoyu:String
    xiaoman:number
  }>(),{
    xiaoman:32,
    xiaoyu:()=>"抓住了一只小满"//string类型就必须回调了，不然会报错
  })
  //---------------上面是我的写法，下面是小满的写法(其实差不多哈哈，是可以定义多个默认值的)，用回调的方式会好一些(小满推荐方式)
  withDefaults(defineProps<{
    xiaoyu:String
    xiaoman:number
  }>(),{
    xiaoman:()=>[666]
  })
  ```
  
  > 如果想要获取defineProps里title内的值的话，需要使用一个参数去接收
  
  ```ts
  const xiaoman = defineProps<Props>({
      title:{
          type:"String",
          name:"洛洛是小满QQ群的霸主"
      }
  })//通过内置函数defineProps完成传递，即可在子组件中直接使用在上面定义好的内容
  console.log(xiaoman.title)
  ```
  
- **传送复杂的数据类型**

  - 要使用v-bind来进行绑定，简写为`:`

    ```js
    layout文件夹下的index文件
    <template>
      <div>
        <Header title="今天没有夜宵吃"></Header>
        <Content :xiaoman="xiaoyu"></Content>  //复杂的数据类型在这里，这个xiaoman也是自定义的，写后是要在子组件进行定位的，而后面的xiaoyu则是在本组件中进行填入的复杂数据(通过reactive进行)
        <Menu></Menu>
      </div>
    </template>
    
    <script setup lang="ts">
    import Content from "./Content/index.vue";
    import Header from "./Header/index.vue";
    import Menu from "./Menu/index.vue";
    import {reactive} from "vue";
    
    interface A{
      name:string,
      sex:number,
      height?:number
    }
    
    const xiaoyu = reactive<A>({
      name:"小余",
      sex:1,
      height:175
    })
    </script>
    
    <style scoped>
    
    </style>
    ```

    ```js
    layout下的Content文件夹的index文件
    <template>
    <div>我是Content</div>
      <div>{{xiaoman}}</div>  //将父组件自定义好的属性在子组件中通过插值语法进行展示
    </template>
    
    <script setup lang="ts">
    type Props = {//类型集合，名为Props，这名也是自己取的
      xiaoman:any//这里是TS的写法，限定类型为any(其实就是所有类型都可以)
    }
    defineProps<Props>()//进行使用，通过泛型进行限定类型，也就是我们刚刚提前定义好的 xiaoman:any
    
    withDefaults(defineProps(),{//TS独有的
      title:"我是默认值",
      data:() =>[1,2,3,4,666]//一样的默认值，如果父组件没有设置的话
    })//父那边传递内容来给子组件，如果没有传递则显示默认值
    </script>
    
    <style scoped>
    
    </style>
    //这里能扩展很多内容，比如加个按键，通过按键来决定内容的展示
    ```

    


> 上面是父传子，接下来我们来进行子组件传递信息给父组件的写法

### defineEmits

- `defineProps` 和 `defineEmits` 都是只能在 `<script setup>` 中使用的**编译器宏**。他们不需要导入，且会随着 `<script setup>` 的处理过程一同被编译掉。
- `defineProps` 接收与 `props` 选项相同的值，`defineEmits` 接收与 `emits` 选项相同的值。
- `defineProps` 和 `defineEmits` 在选项传入后，会提供恰当的类型推导。
- 传入到 `defineProps` 和 `defineEmits` 的选项会从 setup 中提升到模块的作用域。因此，传入的选项不能引用在 setup 作用域中声明的局部变量。这样做会引起编译错误。但是，它*可以*引用导入的绑定，因为它们也在模块作用域内。

### 子传父(组件)

> 1. 从子组件Menu入手，这时候需要用到`defineEmit`，这个内置函数里参数是一个数组，数组里面放的是我们自定义的名称(这个名称会在父组件中用到)，这里我们使用一个常量接住
> 2. 使用上我们的defineEmit，这里使用一个常量接住了此内置函数，使用上的话，里面有两个参数要填写，第一个是我们自定义的参数，也就是刚刚在defineEmit中写到的，这里则是要将其绑定起来`派发`到父组件中，第二个参数则是我们要派发出去的内容(也就是从子组件到父组件的内容)
> 3. 下一步我们就要到父组件中去接收了，我们这里要通过事件进行接收(也就是@)，还记得我们刚刚说会在父组件名称中用到的自定义名称吗？没错，这里要用到了，@在子组件自定义名称="在父组件自定义名称"
> 4. 父组件自定义名称要在父组件中script中使用，父组件中自定义名称的内容则可以通过子组件触发

#### 代码演示(重录前)

```js
//layout下的index.vue
<template>
  <div>
    <Header title="今天没有夜宵吃"></Header>
    <Content :xiaoman="xiaoyu"></Content>
    <Menu @XiaoYu="AAA" :Menu="Menu2"></Menu>  //这从我们通过这里进行子传父的操作，XiaoYu是我们在子组件中自定义的名称(通过@接收)，AAA则是我们在父组件即将使用的自定义名称
  </div>
</template>

<script setup lang="ts">
import Content from "./Content/index.vue";
import Header from "./Header/index.vue";
import Menu from "./Menu/index.vue";
import {reactive} from "vue";

interface A{
  name:string,
  sex:number,
  height?:number
}

const xiaoyu = reactive<A>({
  name:"小余",
  sex:1,
  height:175
})
const Menu2 = reactive<A>({
  name:"大余",
  sex:0,
  height:165
})

const AAA = (yupi?:any) =>{//没错，就是上面Menu组件中的AAA，这里要进行断言一下爱，因为yupi是可能为any类型
  console.log(yupi,"我是鱼皮派来的小兵")
}
</script>
```

```js
layout文件下的Menu文件夹下的index
<template>
<div>我是Menu</div>
  <button @click="xiaoman()">派发</button>  //通过这个按钮触发子组件向父组件传输信息的操作
</template>

<script setup lang="ts">
import {reactive} from "vue";


const yupi:string[] = reactive<string[]>(["鱼皮是一只B站的野生UP主"])
const emit = defineEmits(["XiaoYu"])//要用到的是数组的形式，决定了我们要暴露给父组件的名称
const xiaoman = ()=>{//通过上方那个派发按钮触发这个事件
  emit('XiaoYu',yupi)//第一个是我们自定义的参数，也就是刚刚在defineEmit中写到的，这里则是要将其绑定起来`派发`到父组件中
  // 第二个参数则是我们要派发出去的内容(也就是从子组件到父组件的内容)
}
</script>

<style scoped>

</style>
```

输出：(我们可以看到，控制台输出的时候，子组件传递到父组件的信息会放置在后面，而在父组件定义的内容则会放在前面)

![image-20221019001010912](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221019001010912-1711983932538-17.png)

```js
//对Menu进行一点小修改
const yupi:string[] = reactive<string[]>(["鱼皮是一只B站的野生UP主","今天出海抓鱼皮","鱼皮今天潜水没有出没，没有抓到"])
```

![image-20221019001900761](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221019001900761-1711983932538-19.png)

```js
//进一步修改
const AAA = (yupi?:any) =>{//没错，就是上面Menu组件中的AAA
  console.log(yupi,"我是鱼皮派来的小兵")
  document.title = yupi[1]//将子组件传递到父组件的内容映射到标题上面
}
//哈哈，不玩了，剩下的玩法自行开发
//可以传多个数据，然后使用多个@进行绑定
```

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221019002148260-1711983932538-18.png" alt="image-20221019002148260" style="zoom:50%;" /> 通过点击按钮  <img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20221019002210245.png" alt="image-20221019002210245" style="zoom:50%;" />

#### 代码演示(重录后)

```TS
//App.vue组件
// let num = 66;
import WaterFall from "./components/Loading/water-fall.vue";
import {ref} from "vue";
let Name = ref("小满");
const getName = (name:string) => {
  console.log(name)
  return Name.value = name
}
</script>


<style scoped>
.father{
  background: bisque;
  width: 600px;
  height: 300px;
}
</style>

```

```ts
//water-fall.vue组件
<template>
<div class="back">
  <div>子集</div>
  <button @click="send()">子传父的按钮</button>
</div>
</template>

<script setup lang="ts">

//子传父，给父组件传值使用defineEmits
const emit = defineEmits(['on-click'])//小括号内记得加上大括号
const send = () => {
  emit('on-click','我是小满 今天抽奖 明天破产')//进行派发，第一个是派发，后面内容
}
</script>

<style scoped>
.back{
  background: #888888;
  height: 300px;
  width: 300px;
}

</style>

```

```ts
//water-fall.vue文件
<template>
<div class="back">
  <div>子集</div>
  <button @click="send()">子传父的按钮</button>
</div>
</template>

<script setup lang="ts">

//子传父，给父组件传值使用defineEmits
const emit = defineEmits<{
  (e:"on-click",name:string):void
  (e:"on-click",name:string):void
  (e:"on-click",name:string):void
  (e:"on-click",name:string):void//这边想定义就往下加就行了，TS的写法
}>()

const send = () => {
  emit('on-click','我是小满 今天抽奖 明天破产')//进行派发，第一个是派发，后面都是内容
}
</script>

<style scoped>
.back{
  background: #888888;
  height: 300px;
  width: 300px;
}

</style>
```

### defineExpose

> 父组件想要读到子组件的属性可以通过 defineExpose 暴露
>
> 这样的话会更加安全，就不允许父组件直接修改子组件的内容
>
> 在子组件中定义，然后在父组件中使用(父组件中获取到的值是Proxy实例，是因为我们使用的是ref去定义menus)
>
> 如果使用reactive去定义的话则会直接显示reactive而非Proxy，且reactive并不需要像ref还需要输入.value获取值(他们的区别在于浅层响应与深层次响应)
>
> 当父组件通过模板引用的方式获取到当前组件的实例，获取到的实例会像这样 `{ dayu:number }` (ref 会和在普通实例中一样被自动解包)
>
> ---
>
> 通常的适用场景：
>
> 1. Form表单：表单的一些方法都是通过这种方式暴露出来的，父组件(也就是你在写的那个页面)，引入了表单，就能够安全的使用它暴露出来的方法名称，更加安全(小满举出的例子是Element Plus的表单验证案例方法)

### TypeScript写法(defineExpose)

> Demo的演示

```ts
//water-fall.vue组件(子组件)
defineExpose({
    name:"小满",
    open()=>console.log("不甘心辜负他人的期盼 就多学一些")
})

//App.vue组件(父组件)
<template>
	<waterFallVue ref="waterFall" @on-click="getName" :title="name"></waterFallVue>    
</template>

<script setup lang="ts">
import waterFall from "./components/water-fall.vue"
    //接收组件的实例，起的变量名要跟ref中的名字对应上。
	//重点，如何定义类型？通过TS自带的工具InstanceType，接受一个泛型，通过typeof去接收waterFallVue的类型
    const waterFall = ref<InstanceType<typeof waterFallVue>>()
                                       
    //然后就可以读取到了
    console.log(waterFall.value?.name)
    console.log(waterFall.value?.open)
    //父组件能通过defineExpose就能够读取到子组件的属性，或者调子组件的方法
</script>
```

#### 案例(翻新后——新案例之瀑布流)

> 瀑布流——PS：小满贼想要你看这个
>
> 记得安装less哦，npm install less
>
> ### 瀑布流步骤
>
> - 需要操作到DOM，所以需要先引入生命周期onMounted
> - 定义第一行的排列规则
>   - 方块的颜色，大小(宽高)，方块之间的间隙
>   - 排列规则：排列完第一排的后，会先找到最小的那一个(高度最低)，然后塞入一个新的方块，下一次计算到这一列的时候就将这两个合并起来形成新的大方块来计算。然后就开始无限的这样计算下来了，直到触碰到浏览器的底部为止
>   - 这就是瀑布流，向下倾泄的过程(如果添加动画过程演示就会非常的形象了)

```ts
//父组件
<template>
    <waterFallVue :list="list"></waterFallVue>
</template>
 
<script setup lang='ts'>
import { ref, reactive } from 'vue'
import waterFallVue from './components/water-fall.vue';
const list = [//自动生成的数据，将以父传子Props的形式传入子组件中
    {
        height: 300,
        background: 'red'
    },
    {
        height: 400,
        background: 'pink'
    },
    {
        height: 500,
        background: 'blue'
    },
    {
        height: 200,
        background: 'green'
    },
    {
        height: 300,
        background: 'gray'
    },
    {
        height: 400,
        background: '#CC00FF'
    },
    {
        height: 200,
        background: 'black'
    },
    {
        height: 100,
        background: '#996666'
    },
    {
        height: 500,
        background: 'skyblue'
    },
    {
        height: 300,
        background: '#993366'
    },
    {
        height: 100,
        background: '#33FF33'
    },
    {
        height: 400,
        background: 'skyblue'
    },
    {
        height: 200,
        background: '#6633CC'
    },
    {
        height: 300,
        background: '#666699'
    },
    {
        height: 300,
        background: '#66CCFF'
    },
    {
        height: 300,
        background: 'skyblue'
    },
    {
        height: 200,
        background: '#CC3366'
    },
    {
        height: 200,
        background: '#CC9966'
    },
    {
        height: 200,
        background: '#FF00FF'
    },
    {
        height: 500,
        background: '#990000'
    },
    {
        height: 400,
        background: 'red'
    },
    {
        height: 100,
        background: '#999966'
    },
    {
        height: 200,
        background: '#CCCC66'
    },
    {
        height: 300,
        background: '#FF33FF'
    },
    {
        height: 400,
        background: '#FFFF66'
    },
    {
        height: 200,
        background: 'red'
    },
    {
        height: 100,
        background: 'skyblue'
    },
    {
        height: 200,
        background: '#33CC00'
    },
    {
        height: 300,
        background: '#330033'
    },
    {
        height: 100,
        background: '#0066CC'
    },
    {
        height: 200,
        background: 'skyblue'
    },
    {
        height: 100,
        background: '#006666'
    },
    {
        height: 200,
        background: 'yellow'
    },
    {
        height: 300,
        background: 'yellow'
    },
    {
        height: 100,
        background: '#33CCFF'
    },
    {
        height: 400,
        background: 'yellow'
    },
    {
        height: 400,
        background: 'yellow'
    },
    {
        height: 200,
        background: '#33FF00'
    },
    {
        height: 300,
        background: 'yellow'
    },
    {
        height: 100,
        background: 'green'
    }
 
]
</script>
 
<style  lang='less'>
#app,
html,
body {
    height: 100%;
}
 
* {
    padding: 0;
    margin: 0;
}
</style>
```

```ts
//子组件
<template>
    <div class="wraps">
        <div :style="{height:item.height+'px',background:item.background,top:item.top+'px',left:item.left + 'px'}"//定义方块的属性(高度，方块色，距离上方的高度，距离左边的高度)
            v-for="item in waterList" class="items"></div>
    </div>
</template>
 
<script setup lang='ts'>
import { ref, reactive, onMounted } from 'vue'
const props = defineProps<{//TS的写法去进行父传子
    list: any[]
}>()
const waterList = reactive<any[]>([])//将数据进行一个响应化处理
const init = () => {
    const heightList: any[] = []//这是形成新的大方块，作用是套住两个同一列的方块
    const width = 130;//定义宽度，我们方块的宽度为120，这边为130就能够让方块之间产生空隙
    const x = document.body.clientWidth//获取可视区域的距离(就是你浏览器的最大宽度)
    const column = Math.floor(x / width)//宽度能摆下多少列，进行一个取整
 
    for (let i = 0; i < props.list.length; i++) {//遍历第一行的数据
        if (i < column) {//数量要小于浏览器能承受的最大范围
            props.list[i].top = 10;//方块距离上方的距离
            props.list[i].left = i * width;//横向排列放入刚好(能放下的最大数量)的方块(第一排)
            heightList.push(props.list[i].height + 10)//形成的大方块的高度
            waterList.push(props.list[i])//向响应式数据传入方块，并返回新的长度
        } else {
            let current = heightList[0]//默认第一个是最小的
            let index = 0;//默认索引
            heightList.forEach((h, inx) => {//找到真正最小的那个单位，对数组内的每个方块都执行一次判断
                if (current > h) {//不停的去进行筛选，每次筛选出来最小的那个方块，原理来自冒泡排序
                    current = h;
                    index = inx;
                }
            })
            console.log(current,'c')//控制台找出来了
            props.list[i].top = (current + 20);//定义新方块相对浏览器上方的距离(同列上面的方块 + 20)
            console.log(props.list[i].top,'top',i)
            props.list[i].left = index * width;//通过索引定位到该位置
            heightList[index] =  (heightList[index] + props.list[i].height + 20);//找到个最小的之后会重新计算最小的并将上下间距拉开20
            waterList.push(props.list[i])//将方块添加进去
        
        }
    }
    console.log(props.list)
}
 
onMounted(() => {//操作DOM，引入生命周期
    window.onresize = () => init()
    init()
})
 
</script>
 
<style scoped lang='less'>
.wraps {//这是父级
    position: relative;
     height: 100%;
    .items {//这是子集，每个item都是通过定位去摆放位置了
        position: absolute;//绝对定位
        width: 120px;//每个的宽度固定120px
    }
}
</style>
```

> **效果展示**

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221119035843986-1711983932539-20.png" alt="image-20221119035843986" style="zoom:67%;" />

#### 案例(翻新前——旧案例)

```js
//layout文件夹下的index
<template>
  <div>
    <Header title="今天没有夜宵吃"></Header>
    <Content :xiaoman="xiaoyu"></Content>
    <Menu ref="menus" @XiaoYu="AAA" :Menu="Menu2"></Menu> //使用ref
  </div>
</template>

<script setup lang="ts">
import Content from "./Content/index.vue";
import Header from "./Header/index.vue";
import Menu from "./Menu/index.vue";
import {reactive ,ref} from "vue";//首先需要额外引入ref

interface A{//TS中的接口
  name:string,
  sex:number,
  height?:number
}

const xiaoyu = reactive<A>({
  name:"小余",
  sex:1,
  height:175
})
const Menu2 = reactive<A>({
  name:"大余",
  sex:0,
  height:165
})
const menus = ref(null)//定义一个响应式
const AAA = (yupi?:any) =>{//没错，就是上面Menu组件中的AAA
  console.log(yupi,"我是鱼皮派来的小兵")
  document.title = yupi[1]
  console.log(menus.value)//输出menus中的响应式，这里联动了AAA，AAA是由子组件的按钮进行触发的
}
</script>

<style scoped>

</style>
```

```js
//Menu子组件
<template>
<div>我是Menu</div>
  <button @click="xiaoman()">派发</button>
</template>

<script setup lang="ts">
import {reactive} from "vue";
const dayu = reactive<number[]>([10086,110,1314520])//定义内容
defineExpose({
    dayu//defineExpose的使用形式是对象形式，将dayu暴露给了父组件
})
const yupi:string[] = reactive<string[]>(["鱼皮是一只B站的野生UP主","今天出海抓鱼皮","鱼皮今天潜水没有出没，没有抓到"])
const emit = defineEmits(["XiaoYu"])//要用到的是数组的形式，决定了我们要暴露给父组件的名称
const xiaoman = ()=>{//通过上方那个派发按钮触发这个事件
  emit('XiaoYu',yupi)//第一个是我们自定义的参数，也就是刚刚在defineEmit中写到的，这里则是要将其绑定起来`派发`到父组件中
  // 第二个参数则是我们要派发出去的内容(也就是从子组件到父组件的内容)
}


</script>

<style scoped>

</style>
```

效果图：

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221019005124778-1711983932539-21.png" alt="image-20221019005124778" style="zoom:70%;" />

> 总结：这个defineExpose是在子组件中使用，以对象的形式，往里面丢入我们想要传递到父组件中的变量(我们在这个变量中使用reactive将其传递的内容变为响应式)，变量被暴露出来，可以被父组件通过Proxy实例对象给发现。我们通过定义一个空的const xxx = ref(null)，来观察xxx.value中已经从Menu子组件传递过来的数据dayu。之所以是Reactive形式的则是需要我们看Vue3源码中的定义，在ref中嵌套了reactive

# 第十五章 — 全局组件、局部组件、递归组件

### 配置全局组件

> 例如组件使用频率非常高（table 表格，Input 文本框，button 按钮，等）这些组件 几乎每个页面都在使用便可以封装成全局组件
>
> 全局组件的意思：可以直接使用，无需引入
>
> 调用app.component方法全局注册组件
>
> 配置组件的两种方式：
>
> 1. 直接在components中创建组件，然后去main.ts配置文件下引入
> 2. 第二种是在components中创建文件夹(此文件夹为你想要的组件名)，然后在该文件夹下创建index.vue。在使用的时候直接引用文件夹的名称，然后就会自动锁定到index.vue的身上

```typescript
//为了方便测试，减少测试成本，只保存必要的内容，该代码块为准备配置的全局组件
<template>
  <div>我是Card组件</div>
</template>

<script setup lang="ts">

</script>

<style scoped lang='less'>

</style>
```

> 第一个参数组件名称 第二个参数组件实例

```js
//在配置文件main.ts下
import { createApp } from 'vue'
import './style.css'
import App from './App.vue'
import Card from './components/Card/index.vue'//引入Card组件

const app =  createApp(App)
app.component('Card',Card)//第一个参数是名字，可以随便写的。第二个参数则是我们想要全局使用的组件(想要全局使用的组件记得要先引入才能使用)
app.mount('#app')
```

### 配置局部组件

> 使用频率不高
>
> 就是在一个组件内（A） 通过 import 去引入别的组件 (B) 称之为局部组件
>
> 应为 B 组件只能在 A 组件内使用 所以是局部组件
>
> 如果 C 组件想用 B 组件 就需要 C 组件也手动 import 引入 B 组件

```typescript
<template>
  <div class="wraps">
    <layout-menu :flag="flag" @on-click="getMenu" @on-toogle="getMenuItem" :data="menuList" class="wraps-left"></layout-menu>
    <div class="wraps-right">
      <layout-header> </layout-header>
      <layout-main class="wraps-right-main"></layout-main>
    </div>
  </div>
</template>
 
<script setup lang="ts">
import { reactive,ref } from "vue";
import layoutHeader from "./Header.vue";
import layoutMenu from "./Menu.vue";
import layoutMain from "./Content.vue";//这三个都是组件，引入此组件后也只能在此组件使用
//这些组件，想实现的话自己配套创建一下
```

### 配置递归组件mock数据

> 原理跟我们写 js 递归是一样的 自己调用自己 通过一个条件来结束递归 否则导致内存泄漏
>
> 案例递归树
>
> 在父组件配置数据结构 数组对象格式 传给子组件

```typescript
type TreeList = {//TS的定义类型方式
  name: string;
  icon?: string;
  children?: TreeList[] | [];//children是子集，意思就是可以为TreeList这个数组或者空数组。问号则是可选的意思(可以不要这个也不会报错)
};
const data = reactive<TreeList[]>([
  {
    name: "no.1",
    children: [//套娃开始第一层
      {
        name: "no.1-1",
        children: [//套娃第二层
          {
            name: "no.1-1-1",
          },
        ],
      },
    ],
  },
  {
    name: "no.2",
    children: [//套娃第一层
      {
        name: "no.2-1",
      },
    ],
  },
  {
    name: "no.3",
  },
]);
//后面就要通过:xxx="xxx"暴露给其他组件，其他组件通过defineProps接收
```

#### 递归组件定义名称(重录进行补充)

> 递归的第一个条件就是确认递归的名称
>
> - 在Vue3中可以直接使用这个文件名来当这个递归组件的名称(就是你的文件名叫做Tree，那就可以直接将Tree进行使用)
>
>   ```ts
>   <div @click.stop="clickTap(item,$event)" v-for="item in data">  //@click.stop的stop阻止冒泡，clickTap就是控制台打印的事件
>       <input v-model="item.checked" type="checkbox"> <span>{{item.name}}</span>
>   	<Tree v-if="item?.children?.length" :data="item?.children"></Tree>
>   //因为item?.children的children是数组，数组永远等于true，所以我们要判断它的length
>   </div>
>                                                                                 
>   const clickTap =(item,e)=>{//我们能通过vue提供的一个$event在事件中来获取event元素，进行一些我们想要的操作
>       console.log(item)
>   }
>   ```
>
>   此时我们进行判断一下三种方式的优劣：
>
>   - 使用文件名称，方便但是固定得太死了
>   - 再写一个script能够自定义名称的话，那也太麻烦了
>   - 插件实现：感觉这个插件有点麻烦，而且还会丢失响应式，外加此时这个时间点小满已经写出更好的插件了，所以第三种方法插件实现笔记里就不记录下来了(功能重复)，需要的去看视频10分50秒的位置
>   - 三种方式都不是最优解，因此小满写了一个Vite插件，实现了这个功能，插件的创造会有一期笔记进行完善(小满已经确认要出插件视频)

#### 递归组件配置方式

##### **第一种**

> 在自己组件中引入自己

```typescript
//比如在Tree文件夹下的index.vue
//组件要先自己定义一下自己的组件名，好在递归的时候调用
<script lang="ts">
export default {
  name:"TreeItem"
}
</script>

import TreeItem from './index.vue'
```

> template部分
>
> TreeItem 其实就是当前组件 通过 import 把自身又引入了一遍 如果他没有 children 了就结束(这个children是上面代码块中标注了套娃一层，套娃二层的那里)

```typescript
  <div style="margin-left:10px;" class="tree">	//margin-left是为了让视觉效果更好，可以看出缩进之间的关系
    <div :key="index" v-for="(item,index) in data">
      <div @click='clickItem(item)'>
        {{item.name}}
      </div>
    <TreeItem @on-click='clickItem' v-if='item?.children?.length' :data="item.children"></TreeItem>


        //注意看，我们第二层也定义了，要自己派发一遍，接收一遍，不然会停留在第一层，后面的套娃部分(递归)就接收不到，根据弹幕显示这个应该是bug，已经修复了，不再需要自己接收on-click事件，但权当作知识点补充
//这种方式虽然可以使用，但是会报错。比较不好
  </div>
  </div>
```

##### 第二种方式

> 给组件定义一个内容，就跟Vue2是一样的了，但是setup是没办法去定义内容的
>
> 这里就要灵活使用了，没人规定说只能有一个`script`，我们再多定义一个，这个新的`script`我们不设置`setup`，但是需要保证类型是一样的(就是你之前在setup部分使用的是lang="ts"，那在这个新的里面也要使用typescript)
>
> 我们可以在该递归的template里面加入点击事件或者派发出去等各种操作

```typescript
//还是在当前文件中，定义新的第二个script
export default{//对外暴露一个name出来
    name:"TreeItem"//这样子，我们调用自身就不会发生报错
}
//此时使用defineExpose组件实例就有了name属性
const emit = defineEmits('on-click')//准备派发出去，on-click是作为暴露出去给父组件的命名(在父组件中想要使用就得通过我们定义好的名字，比如在这里是on-click)，在其他组件使用的时候记得加上@，变成@on-click="XX"，XX则是在父组件中设置(这里就决定了父组件怎么处理我们从子组件传送过来的内容)
const clickItem = (item:TreeList) => {
    console.log("触发一下")//给递归的每个内容都加上点击效果
    emit('on-click',item)//我们将这个派发的时机跟这个点击事件绑定在一起，通过on-click这个设定好的媒介将通过参数item传输进来的内容派送出去
}
```

> 弹幕方法：
>
> 1. 最新语法能够直接使用当前vue文件名(直接用子组件文件名取代<TreeItem>，不用export)

补充知识点：

- 可选链操作符：？，在上面已经有使用过？来表示操作了，代表可以有这个属性、也可以没有。没有的话不会报错而是显示undefined

- 配套操作`??`：这个就是在当前面没有读取到内容的时候就输出`??`后面的内容，但只处理null跟undefined

  ```typescript
  //例如我们在控制台打出
  item.children?.length//undefined
  item.children?.length??[]//输出[]
  //??的使用的话，0跟false不会返回[]，而是返回0跟false本身。null这个空值则是返回[]
  ```


## 案例(重录补充)-批量注册全局组件

> 封装一个 Card 组件想在任何地方去使用
>
> 想去任何地方使用，你需要去main.ts中进行配置 => 配置方法：
>
> ```ts
> app.component('Card',CardVue)//第一个是组件名字(可以随便取名)，第二个就是将我们引入到main.ts中的组件放进去
> ```

```ts
//card组件
<template>
  <div class="card">
     <div class="card-header">
         <div>标题</div>
         <div>副标题</div>
     </div>
     <div v-if='content' class="card-content">
         {{content}
     </div>
  </div>
</template>
 
<script setup lang="ts">
type Props = {
    content:string
}
defineProps<Props>()
 
</script>
 
<style scoped lang='less'>
@border:#ccc;
.card{
    width: 300px;
    border: 1px solid @border;
    border-radius: 3px;
    &:hover{
        box-shadow:0 0 10px @border;
    }
 
    &-content{
        padding: 10px;
    }
    &-header{
        display: flex;
        justify-content: space-between;
        padding: 10px;
        border-bottom: 1px solid @border;
    }
}
</style>
```

# 第十六章 — 动态组件 & 源码解析(重录)

> 什么是动态组件 就是：让多个组件使用同一个挂载点，并动态切换，这就是动态组件。
>
> 在挂载点使用 [component](https://so.csdn.net/so/search?q=component&spm=1001.2101.3001.7020) 标签，然后使用 v-bind:is=” 组件”
>
> 小满补充的可选链操作符知识点我写在上一章节了

**引入组件**

```typescript
import A from './A.vue'
import B from './B.vue'
import C from './C.vue'

import {reactive,markRaw} from 'vue'
```

**通过 is 切换 A B 组件**

```typescript
  <component :is="A"></component>
```

**在setup语法糖中定义数据内容**

> reactive 会进行 proxy 代理 而我们组件代理之后毫无用处 节省性能开销 推荐我们使用 shallowRef 或者 markRaw 跳过 proxy 代理

```typescript
type Tabs = {//定义类型
    name:string,
    comName:any
}
//这里会有警告，reactive会去代理Proxy，然后里面的组件A,B,C也会去代理，但这是没有必要的。代理一次就够了，多了浪费性能
//这里使用的是markRaw就是给做个标记，意味着告诉vue此组件永远不会改变或者说进入响应式
const data = reactive<Tabs[]>([//泛型，这里是TS的定义方法，Tabs类型且为数组，类型如果编辑器能推导出来，我们就尽量不要去多定义
    {
        name:'我是A组件',
        //comName:A
        comName:markRaw(A)
    },
    {
    	name:'我是B组件',
    	//comName:B
        comName:markRaw(B)
    },
    {
    	name:'我是C组件',
    	//comName:C
        comName:markRaw(C)
    }
])
```

#### 了解markPaw

```typescript
let obj = {name:"小满裤子被顺走了"}
let o = markRaw(obj)
console.log(o)//通过o去观察markRaw
//发现其实就是Vue会识别，从而套上一个__v_skip:true属性，从而跳过Proxy的代理
```

### **通过点击来动态切换组件**

```typescript
//template部分
  <div class="content">
    <div class="tab">
      <div @click="switchCom" :key="item.name" v-for="item in data">{{item.name}}</div>
    </div>
    <component :is="current.comName"></component> //注意我们是通过这个:is来实现的
  </div>
//点击部分
const switchCom = (item:Tabs) =>{//switchCom是一个点击事件，需要我们主动在template的某个标签中加上
    current.comName = item.comName
}
```

> 通过上面，我们就做到了通过点击动态的去切换组件了
>
> 呃，这个感觉跟路由切换组件有点像，但是这个动态切换组件是不会改变路径的

## 动态组件补充知识

```typescript
<component :is="current.comName"></component> //这种方式能在Vue3中实现
<component is="current.comName"></component> //但是去掉了冒号就无法实现了，而这种方式是可以在Vue2中实现的
//因为如果使用is绑定的话是字符串，:is绑定的是实例。
//绑定是字符串的话，由于Vue3使用setup语法糖，他们之间会没有媒介，所以只能通过`:is`这种实例的方式去绑定
```

## 重录案例(动态组件)

> 小满在它的CSDN没有更新这一个代码，那我就更新在这里好了，很好玩的一个案例
>
> A、B、C三个子组件就自己去写了，看你们自己喜欢展示什么

```ts
<template>
  <div style="display: flex">
    <div @click="switchCom(item,index)" :class="[active == index ? 'active' : '']" class="tabs" v-for="(item,index) in data">
      v-for里的index是索引
      class是可以写两个的，一个动态一个静态，但不能两静态或者两动态
      <div>{{item.name}}</div>
    </div>
  </div>
  <component :is="comId"></component> 默认展示这个A组件了，component是Vue的内置组件
</template>

<script setup lang='ts'>
import { ref, reactive } from 'vue'
import WaterFall from "./components/water-fall.vue";
import A from "./components/A.vue"
import B from "./components/B.vue"
import C from "./components/C.vue"

const comId = ref(A)
const active = ref(0)

const data = reactive([
  {
    name:"A组件",
    com:A
  },
  {
    name:"B组件",
    com:B
  },
  {
    name:"C组件",
    com:C
  }
])
const switchCom = (item:any,index:any)=>{
  comId.value = item.com,//要切换的组件
  active.value = index//切换的索引
}
</script>

<style  lang='less'>
.active{
  background: skyblue;
}
.tabs{
  border: 1px solid #ccc;
  padding: 5px 10px;
  margin: 5px;
  cursor: pointer;
}
</style>
```

> 有一个报错：意思是组件的信息也被劫持了，但其实是没有必要的事情，会造成性能浪费
>
> Vue提供了两种解决方式(要一起使用)：
>
> - shallowRef
> - markRaw
>
> ```ts
> import {ref, reactive, markRaw, shallowRef} from 'vue'
> import WaterFall from "./components/water-fall.vue";
> import A from "./components/A.vue"
> import B from "./components/B.vue"
> import C from "./components/C.vue"
> 
> const comId = shallowRef(A)
> const active = ref(0)
> 
> const data = reactive([
>   {
>     name:"星期六要做什么事情",
>     com:markRaw(A)
>   },
>   {
>     name:"星期日要做什么事情",
>     com:markRaw(B)
>   },
>   {
>     name:"星期一了怎么办",
>     com:markRaw(C)
>   }
> ])
> ```

- 补充用法是Vue2的写法就不写进来了，此写法在视频9分40秒

## 源码解析

> 在视频的第10分45秒处开始
>
> 源码位于core > node_modules > runtime-core > src > helpers > resolveAssets.ts

[小满视频里出现的那个**Vue 3 Template Explorer**网站链接][https://vue-next-template-explorer.netlify.app/#eyJzcmMiOiI8ZGl2PkhlbGxvIFdvcmxkPC9kaXY+Iiwib3B0aW9ucyI6e319]

```ts
/**
 * @private
 */
export function resolveDynamicComponent(component: unknown): VNodeTypes {
  //如果component 是字符串去做处理
  if (isString(component)) {//判断组件是不是字符串类型，因为有两种传递方式：1是整个对象传递进去 2是传递string方式
    return resolveAsset(COMPONENTS, component, false) || component
  } else {
    //如果是对象直接返回 然后调用render 重新patch 重新去创建组件
    // invalid types will fallthrough to createVNode and raise warning
    return (component || NULL_DYNAMIC_COMPONENT) as any
  }
}
```

```ts
  const instance = currentRenderingInstance || currentInstance//获得组件实例
  if (instance) {
    const Component = instance.type //当前组件的render setup 以及对应的template，获取type是为了区分当前使用的optionsAPI还是CompositionAPI
//optionsAPI是Vue2的写法，CompositionAPI是Vue3的写法
    // explicit self name has highest priority
    if (type === COMPONENTS) {
      const selfName = getComponentName(
        Component,
        false /* do not include inferred name to avoid breaking existing code */
      )
      if (
        selfName &&
        (selfName === name ||
          selfName === camelize(name) ||
          selfName === capitalize(camelize(name)))
      ) {
        return Component
      }
    }
```

```ts
//res为当前要切换的组件
    const res =
      // local registration
      // check instance[type] first which is resolved for options API
        //局部注册
      resolve(instance[type] || (Component as ComponentOptions)[type], name) ||
      // global registration
        //全局注册
      resolve(instance.appContext[type], name)

    if (!res && maybeSelfReference) {
      // fallback to implicit self-reference
      return Component
    }

    if (__DEV__ && warnMissing && !res) {
      const extra =
        type === COMPONENTS
          ? `\nIf this is a native custom element, make sure to exclude it from ` +
            `component resolution via compilerOptions.isCustomElement.`
          : ``
      warn(`Failed to resolve ${type.slice(0, -1)}: ${name}${extra}`)
    }

    return res//最下面的这里做出一个返回
```



# 第十七章 — 插槽(slot)全家桶

> `插槽`就是子组件中的提供给父组件使用的一个占位符，用 <slot></slot> 表示，父组件可以在这个`占位符`中填充任何模板代码，如 HTML、组件等，填充的内容会替换子组件的 < slot></slot > 标签。
>
> 只要你在子组件里写一个 <slot></slot > 就可以从父组件向内部填充了.（通常也是父组件将信息传给子组件）

## 匿名插槽

> 在子组件放置一个插槽

```typescript
<template>
    <div>
       <slot></slot>  //这个就是插槽
    </div>
</template>
```

> 父组件使用插槽
>
> 在父组件给这个插槽填充内容

```typescript
        <Dialog>
           <template v-slot> //使用匿名插槽
               <div>小满10w粉丝女装，线下聚会给粉丝看</div>
           </template>
        </Dialog>
```

## 具名插槽

> 在匿名函数的基础上加上了name名字定向
>
> 具名插槽其实就是给插槽取个名字。一个子组件可以放多个插槽，而且可以放在不同的地方，而父组件填充内容时，可以根据这个名字把内容填充到对应插槽中

```typescript
    <div>
        <slot name="header"></slot>
        <slot></slot>
 
        <slot name="footer"></slot>
    </div>
```

> 父组件使用需对应名称

```typescript
        <Dialog>
            <template v-slot:header>
               <div>1</div>
           </template>
           <template v-slot>
               <div>2</div>
           </template>
           <template v-slot:footer>
               <div>3</div>
           </template>
        </Dialog>
```

> 插槽简写，将v-slot简写为#

```typescript
        <Dialog>
            <template #header>
               <div>1</div>
           </template>
           <template #default>
               <div>2</div>
           </template>
           <template #footer>
               <div>3</div>
           </template>
        </Dialog>
```

## 作用域插槽

> 在子组件动态绑定参数 派发给父组件的 slot 去使用

```typescript
    <div>
        <slot name="header"></slot>
        <div>
            <div v-for="item in 100">
                <slot :data="item"></slot>
            </div>
        </div>
 
        <slot name="footer"></slot>
    </div>
```

>  通过结构方式取值

```typescript
         <Dialog>
            <template #header>
                <div>1</div>
            </template>
            <template #default="{ data }">
                <div>{{ data }}</div>
            </template>
            <template #footer>
                <div>3</div>
            </template>
        </Dialog>
```

## 动态插槽

> 插槽可以是一个变量名

```typescript
        <Dialog>
            <template #[name]>//name会替换成下面响应式的name
                <div>
                    23
                </div>
            </template>
        </Dialog>
```

```typescript
const name = ref('header')//header或者footer或者其他什么乱七八糟的东西都可以，只要是你定义好的插槽名字(具名插槽)
```

---

>  在上面中我们用到子组件与父组件之间的关系，为防止弄混他们之间的关系，这里进行一个知识补充

### 父组件和子组件

我们经常分不清什么是父组件，什么是子组件。现在来简单总结下：我们将某段代码封装成一个组件，而这个组件又在另一个组件中引入，而引入该封装的组件的文件叫做父组件，被引入的组件叫做子组件。

# 第十八章 — 异步组件&代码分包&suspense(重写部分)

## 顶层 `await`

> #### 异步组件
>
> 在大型应用中，我们可能需要将应用分割成小一些的代码块 并且减少主包的体积
>
> 这时候就可以使用异步组件
>
> ```typescript
>//在setup语法糖里面 使用方法
> <script setup> 中可以使用顶层 await。结果代码会被编译成 async setup()
> ```

```typescript
<script setup>
const post = await fetch(`/api/post/1`).then(r => r.json())
</script>
```

> 父组件引用子组件 通过 defineAsyncComponent 加载异步配合 import 函数模式便可以分包

```typescript
<script setup lang="ts">
import { reactive, ref, markRaw, toRaw, defineAsyncComponent } from 'vue'
 
const Dialog = defineAsyncComponent(() => import('../../components/Dialog/index.vue'))
```

## 代码分包

> 打包代码：npm run build
>
> 打完包后会生成一个`dist`文件夹：
>
> 1. dist下的assets下的文件
>    - index.js(放我们代码的主逻辑的)
>    - vendor.js(第三方的模块库或者说依赖库会打到这里，比如Vue，elementUI之类的)
> 2. 通过index.html去加载我们的index.js，正式项目的时候这个index.js必然是比较大的，等待缓慢加载的时候是会白屏的，为了不影响用户体验，这个时候就能使用异步组件去优化
> 3. 我们在组件文件夹中去封装一些接口的工具，比如说新建一个叫做`server.ts`的文件(这个可以任意取名的，不过这样比较规范)

### 封装axios

```typescript
//server.ts文件
type NameList = {
    name:string
}
export const axios = (url:string):Promise<NameList[]>=>{//返回的是一个NameList类型的数组
    return new Promise((resolve)=>{
        let xhr:XMLHttpRequest = new XMLHttpRequest()

        xhr.open('GET',url)//GET是请求的方式，除了GET还有POST等等之类的。url就是地址啦。open：打开

        xhr.onreadystatechange = () =>{//在状态准备改变时
            if(xhr.readyState === 4 && xhr.status == 200){
                //进行一个判断4表示已经完成了，0-4的状态分别为...(放下面代码块里)
                //200是一个HTTP的状态码，表示正常连接(计算机网络相关知识点)
                //resolve(xhr.responseText)//返回的是一个文本，我们需要进行一个转化
                resolve(JSON.parse(xhr.responseText))//转化为JSON格式
            }

            //异步写法
            if(xhr.readyState === 4 && xhr.status == 200){
                setTimeout(()=>{
                    resolve(JSON.parse(xhr.responseText))//转化为JSON格式  
                },2000)//通过定时器延迟2s执行
            }
        }

        xhr.send(null)
    })
}
//-------------------------------------------->重录版本的封装axios，有所区别3


export const axios = {
    get<T>(url:string):Promise<T>{
        return new Promise((resolve)=>{
            const xhr = new XMLHttpRequest()
            xhr.open('GET',url)

            xhr.onreadystatechange = ()=>{
                if(xhr.readyState == 4 && xhr.status == 200){
                    setTimeout(()=>{
                        resolve(JSON.parse(xhr.responseText))
                    },2000)
                }
            }
            xhr.send(null)
        })
    }
}
```

### ajax状态 

> - 0 － （未初始化）还没有调用 send () 方法
>
> - 1 － （载入）已调用 send () 方法，正在发送请求
> - 2 － （载入完成）send () 方法执行完成，已经接收到全部响应内容
> - 3 － （交互）正在解析响应内容
> - 4 － （完成）响应内容解析完成，可以在客户端调用了
>
> **readyState 状态说明**
>
> (0) 未初始化
>
> 　　此阶段确认 XMLHttpRequest 对象是否创建，并未调用 open（）方法进行未初始化作好准备。值未 0 表示对象已经存在，否则浏览器会报错 --- 对象不存在。
>
> (1) 载入
>
> 此阶段对 xml (标准化越来越近了) HttpRequest 对象进行初始化，即调用 open () 方法，根据参数 (method,url,true) 完成对象状态的设置。并调用 send () 方法开始向服务端发送请求。值为 1 表示正在向服务端发送请求。
>
> (2) 载入完成
>
> 此阶段接收服务器端的响应数据。但获得的还只是服务端响应的原始数据，并不能直接在客户端使用。值为 2 表示已经接收完全部响应数据。并为下一阶段对数据解析作好准备。
>
> (3) 交互
>
> 此阶段解析接收到的服务器端响应数据。即根据服务器端响应头部返回的 MIME 类型把数据转换成能通过 responseBody、responseText 或 responsexml (标准化越来越近了) 属性存取的格式，为在客户端调用作好准备。状态 3 表示正在解析数据。
>
> (4) 完成
>
> 此阶段确认全部数据都已经解析为客户端可用的格式，解析已经完成。值为 4 表示数据解析完毕，可以通过 xml (标准化越来越近了) HttpRequest 对象的相应属性取得数据。

上面是自己封装的一个axios，其实是可以直接`npm i axios -s`下载一下来使用的

- 然后我们就可以通过import引入的方式在index.vue中使用axios

```typescript
//index.vue文件
<template>
  <div>
    <div v-for="item in list">
      {{item.name}}
    </div>
  </div>
</template>

<script setup lang="ts">
import {axios} from "./server"

//const a = async() => await//自定义的函数需要自己加上async，但在语法糖中不用
const list = await axios('./data.json')//根据我们刚刚定义的能知道返回的是一个url。注意这个.data.json是要自己定义数据的

console.log(list)//这个时候是显示不出来的，因为此时主文件也要进行同步修改
</script>

<style scoped>

</style>
```

**主文件的修改**

```typescript
//template部分
这里的内容就是下方代码块中的Suspense内容了，就不额外多写一遍了。需要使用这个才能够显示异步

//setup部分
import {defineAsyncComponents} from 'vue'//这个是这次修改多出来要用的
import A from "../../components/A/index.vue"//原来的引入方式
//import from是顶级函数，只能在最上方使用，不然会报错。跟import函数是不一样的，import函数允许在下方使用，返回的是一个Promise

const A = defineAsyncComponents(()=> import('../../components/A/index.vue'))//证明他是我们要返回的异步函数，里面填入的是一个工厂函数
//当await遇到这段import函数形式的时候会把这段逻辑拆分出去，当我们重新打包的时候就会发现在assets下会多出一个文件来
```

## suspense

`<suspense>` 组件有两个插槽。它们都只接收一个直接子节点。`default` 插槽里的节点会尽可能展示出来。如果不能，则展示 `fallback` 插槽里的节点。

```typescript
     <Suspense>
            <template #default>//插槽1
                <Dialog>
                    <template #default>
                        <div>我在哪儿</div>
                    </template>
                </Dialog>
            </template>
 
            <template #fallback>//插槽2.当我们的插槽1的内容还没加载出来的时候，我们可以先做点什么
                <div>loading...等待中，你可以先看看小满的黑丝舞蹈</div>
            </template>
        </Suspense>
```

> 问题：组件里请求接口算是异步组件吗？
>
> A组件里面有写await，就是异步组件
>
> 弹幕提示：多个异步组件需要有div包裹一下，不然会报错

### 优势

- 将异步的逻辑拆分成一个文件
- 什么时候去调用？
  - 使用异步组件或者是等浏览器闲下来的时候
- 好处：
  - 节省了内存
  - 减少用户的等待时间

## 骨架屏案例(完整代码+注释)

### 第一种方法

> serve文件夹下的axios.ts文件

```js

export const axios = {
    get<T>(url:string):Promise<T>{
        return new Promise((resolve)=>{
            const xhr = new XMLHttpRequest()
            xhr.open('GET',url)

            xhr.onreadystatechange = ()=>{
                if(xhr.readyState == 4 && xhr.status == 200){
                    setTimeout(()=>{
                        resolve(JSON.parse(xhr.responseText))
                    },2000)
                }
            }
            xhr.send(null)
        })
    }
}
```

> public静态资源下的data.json文件

```json
{
    "data":{
        "name":"小余",
        "age":20,
        "url":"https://i2.hdslb.com/bfs/face/fe39a47cc13a16749186c1672822199c3b2c2d95.jpg",
        "desc":"小余今天抓到一只奇怪的小满"
    }
}
```

> sync异步组件

```ts
//异步组件
<template>
    <div class="sync">
        <div class="sync-content">
            <div><img :src="data.url" alt=""></div>
            <div class="sync-pop">
                <div>{{data.name}}</div>
                <div>&nbsp;{{data.age}}</div>
            </div>
        </div>
    </div>
    <hr>
    <div>{{data.desc}}</div>
</template>

<script setup lang="ts">
    import {ref,reactive} from "vue"
    import { axios } from "../../server/axios";

    interface Data{//定义接口
        data:{
            name:string,
            age:number,
            url:string,
            desc:string
        }
    }

    const {data} = await axios.get<Data>('./data.json')//解构
</script>
```

> App.vue组件

```ts
<template>
<!-- 展示异步组件需要使用Suspense 是Vue3新增内置组件-->
<Suspense>
  <template #default>
    <SyncVue></SyncVue>
  </template>
  <template #fallback>
    <!-- 在延迟的时候,我们就可以在这个fallback里面去做点什么,比如说骨架屏,显示加载的效果 -->
    <h1>我知道你很急,但是先别急,先把康老师唱的歌听完</h1>
  </template>
</Suspense>
</template>

<script setup lang="ts">
import { defineAsyncComponent } from 'vue';
//使用defineAsyncComponent函数进行一个引入
//有两种书写风格，一种是直接传一个回调函数，然后通过回调函数继续使用import函数模式(此模式和引入的有所区别，能够写在代码逻辑里面的)
const SyncVue = defineAsyncComponent(()=>import('./components/expame/sync.vue'))//引入异步组件

</script>

<style scoped>

</style>
```

### 第二种方法

> 在App.vue组件中进行修改

```typescript
const SynVue = defineAsyncComponent({
  loadingComponent:()=>import('./components/expame/sync.vue'),
})
```

# 第十九章 — Teleport传送组件

> `Teleport` Vue 3.0 新特性之一。也是一个内置组件
>
> `Teleport` 是一种能够将我们的模板渲染至指定 `DOM` 节点，不受父级 `style`、`v-show` 等属性影响，但 `data`、`prop` 数据依旧能够共用的技术；类似于 `React` 的 `Portal`。
>
> 主要解决的问题 因为 `Teleport` 节点`挂载`在其他指定的 `DOM` 节点下，完全不受父级 `style` 样式影响

## 使用方法

> 通过 to 属性 插入指定元素位置 to="body" 便可以将 `Teleport` 内容传送到指定位置
>
> 这里使用的例子是一个定位问题，相对定位与绝对定位之间的关系。将Loading放在一个父组件中，Loading的定位会收到这个父组件定位的影响，如何让这个影响消除掉，那这里使用的就是Telepost方法，将这个Loading传送出父组件，那Loading的相对定位就不再受父组件的绝对定位影响了

```typescript
<Teleport to="body">  //传送到body身上
    <Loading></Loading>
</Teleport>
------------------------------------------------------
<Teleport :disable="true" to="body">  //这样就无法传送了，disable是接收true和false这对布尔值的，决定了传送生不生效
    <Loading></Loading>
</Teleport>
```

> 也可以自定义传送位置 支持 class id 等 选择器

```typescript
    <div id="app"></div>
    <div class="modal"></div>
```

```typescript
<template>
 
    <div class="dialog">
        <header class="header">
            <div>我是弹框</div>
            <el-icon>
                <CloseBold />
            </el-icon>
        </header>
        <main class="main">
            我是内容12321321321
        </main>
        <footer class="footer">
            <el-button size="small">取消</el-button>
            <el-button size="small" type="primary">确定</el-button>
        </footer>
    </div>
 
</template>
 
<script setup lang='ts'>
import { ref, reactive } from 'vue'
 
</script>
<style lang="less" scoped>
.dialog {
    width: 400px;
    height: 400px;
    background: #141414;
    display: flex;
    flex-direction: column;
    position: absolute;
    left: 50%;
    top: 50%;
    margin-left: -200px;
    margin-top: -200px;
 
    .header {
        display: flex;
        color: #CFD3DC;
        border-bottom: 1px solid #636466;
        padding: 10px;
        justify-content: space-between;
    }
 
    .main {
        flex: 1;
        color: #CFD3DC;
        padding: 10px;
    }
 
    .footer {
        border-top: 1px solid #636466;
        padding: 10px;
        display: flex;
        justify-content: flex-end;
    }
}
</style>
```

## Telepost源码讲解

> 源码讲解部分开始于小满Vue3的第十九章的4分40秒处

> 其中，Vue3 的源码都是放在 packages 目录下的 ,packages 目录下的每一个目录都是一个单独的项目，并且是基于 RollUp 构建，其中每个目录代表的含义，如下所示：
>
> ```
> ├── packages              
> │   ├── compiler-core    // 核心编译器（平台无关）
> │   ├── compiler-dom     // dom编译器
> │   ├── compiler-sfc     // vue单文件编译器
> │   ├── compiler-ssr     // 服务端渲染编译
> │   ├── global.d.ts      // typescript声明文件
> │   ├── reactivity       // 响应式模块，可以与任何框架配合使用
> │   ├── runtime-core     // 运行时核心实例相关代码（平台无关）
> │   ├── runtime-dom      // 运行时dom 关api，属性，事件处理
> │   ├── runtime-test     // 运行时测试相关代码
> │   ├── server-renderer   // 服务端渲染
> │   ├── sfc-playground    // 单文件组件在线调试器
> │   ├── shared             // 内部工具库,不对外暴露API
> │   ├── size-check          // 简单应用，用来测试代码体积
> │   ├── template-explorer  // 用于调试编译器输出的开发工具
> │   └── vue                 // 面向公众的完整版本, 包含运行时和编译器复制代码
> ```
>
> ## 目录模块
>
> 通过上面源码结构，可以看到有下面几个模块比较特别：
>
> - compiler-core
> - compiler-dom
> - runtime-core
> - runtime-dom
>
> 可以看到 core, dom 分别出现了两次，那么 compiler 和 runtime 它们之间又有什么区别呢？
>
> - compile：我们可以理解为程序编绎时，是指我们写好的源代码在被编译成为目标文件这段时间，可以通俗的看成是我们写好的源代码在被构建工具转换成为最终可执行的文件这段时间，在这里可以理解为我们将.vue 文件编绎成浏览器能识别的.js 文件的一些工作。
> - runtime：可以理解为程序运行时，即是程序被编译了之后，在浏览器打开程序并运行它直到程序关闭的这段时间的系列处理。
>
> 在 package 目录下，除了上面列举的 4 个目录外，还有 reactivity 目录比较重要，他是响应式模块的源码，由于 Vue 3 整体源码采用的 Monorepo 规范，所以其下面每个子模块都可以独立编译和打包，从而独立对外提供服务，在使用时采用 require ('@vue/reactivity') 引入，进入 reactivity 目录下可以看到有对应的 package.json 文件。
>
> 其他目录：compiler-sfc 是一个单文件组件编译工具，server-renderer 目录是服务端渲染模块的源码，sfc-playground 是一个在线 Vue 单文件组件调试工具，shared 目录则包括了常用的工具库方法的源码，size-check 是一个工具，可以用来测试代码体积，template-explorer 用于调试编译器输出的开发工具，最后的 vue 目录则是 Vue.js 的完整源码产生目录。
>
> ## 构建版本
>
> 比较常见的是构建出来的 Vue 会有 vue.global.js 或者 vue.runtime.global.js 两种版本，他们分别表示：
>
> - vue.global.js：是包含编译器和运行时的 “完整” 构建版本，因此它支持动态编译模板。
> - vue.runtime.global.js：只包含运行时，并且需要在构建步骤期间预编译模板。

1. 前置：需要先找到runtime core，src下面的renderer.ts
2. 因为Teleport会经过patch这个函数去创建。判断每一个类型从而去分别创建对应的节点、元素跟组件

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221021030819712-1711983932539-22.png" alt="image-20221021030819712" style="zoom:50%;" />

3. 如果type是teleport组件 会执行 process方法

   > 如果是teleport元素的话，会调用process这个方法

   <img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221021030950416-1711983932539-23.png" alt="image-20221021030950416" style="zoom:70%;" />

   4. 点进去这个process方法，主要能够看到process还有remove

      - process：创建和更新的一个操作
      - remove：做出一些移除

      <img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221021031704707-1711983932539-25.png" alt="image-20221021031704707" style="zoom:67%;" />

   5. 重点部分——target

      ![image-20221021032013460](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221021032013460-1711983932539-27.png)

      我们进入这个resolveTarget内部看看源码是怎么做的(他有两个参数，一个是N2.pops。另一个是query selector)

      ![image-20221021032531449](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221021032531449-1711983932539-24.png)

      - 读取了props的to属性，也就是我们之前那个teleport上面传的to属性(先读取了这个)
      - selector其实就是query selector。通过query selector去读取了一下元素，然后做一个返回targetSelector，读不到则报错提示

      <img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221021033108244-1711983932539-26.png" alt="image-20221021033106919" style="zoom:87%;" />

      第一步先挂上子节点，然后有一个if判断disabled，要么在原先的地方挂载，要么在target目标(新的目标位置)挂载。上面的disabled true false就是通过这个判断去控制挂载的位置

   6. ![image-20221021033802585](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221021033802585-1711983932539-29.png)

      **移回操作**：

      如果新节点disabled为true，那旧节点的disable为false。就把这个给变回来(之前把这个东西给移走了，比如移到body里面，然后再把disabled设置为true，那就得移回来，移到我们之前那个parent里面，哪边为true就移到哪边)

   7. **移走操作**：

      ![image-20221021034341599](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221021034341599-1711983932539-28.png)

   8. **删除逻辑**

      ![image-20221021034510139](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221021034510139-1711983932539-30.png)

      1. 先获取teleport下面的所有子集(children)，通过Vnode
      2. 然后去调用一个unmount方法去做一个卸载，将所有内容清空

# 第二十章 — keep - alive缓存组件 & 源码解析

## **内置组件 keep-alive**

> 有时候我们不希望组件被重新渲染影响使用体验；或者处于性能考虑，避免多次重复渲染降低性能。而是希望组件可以缓存下来，维持当前的状态。这时候就需要用到 `keep-alive` 组件。
>
> keep-alive就是保持活跃的意思，你在里面填入的东西在你切换成其他组件的时候不会被初始化成最初的样子
>
> 开启 keep-alive [生命周期](https://so.csdn.net/so/search?q=生命周期&spm=1001.2101.3001.7020)的变化
>
> - 初次进入时： onMounted> onActivated
> - 退出后触发 `deactivated`
> - 再次进入：
> - 只会触发 onActivated
> - 事件挂载的方法等，只执行一次的放在 onMounted 中；组件每次进去执行的方法放在 onActivated 中

```typescript
<!-- 基本 -->
<keep-alive>
  <component :is="view"></component>
</keep-alive>
 
<!-- 多个条件判断的子组件 -->
<keep-alive>
  <comp-a v-if="a > 1"></comp-a>
  <comp-b v-else></comp-b>
//v-if与else的切换组件，切换后另外一个组件虽然被切换走了，但是通过声明周期我们可以看到不会被销毁
</keep-alive>
 
<!-- 和 `<transition>` 一起使用 -->
<transition>
  <keep-alive>
    <component :is="view"></component>
  </keep-alive>
</transition>
```

##  **`include` 和 `exclude`**

> include 和 exclude 允许组件有条件地缓存。二者都可以用逗号分隔字符串、正则表达式或一个数组来表示：
>
> 写在`include`里面的组件会被缓存起来，只写keep-alive是默认里面信息全部缓存的
>
> 写在`exclude`里面的组件不会被缓存起来，跟include的属性是完全相反的。如何选择使用可以根据实际情况选择
>
> :`max`属性是决定了我们缓存的最大组件数量，假设我们:max="10"，也就是最多缓存10个组件，可我们keep-alive内部即将缓存的有11个组件，他有内置算法会优先替换掉我们不常用的那一个

```typescript
 <keep-alive :include="keep-alive里的组件" :exclude="" :max=""></keep-alive>

<keep-alive :max="10">
  <component :is="view"></component>
</keep-alive>
```

## 新的两个生命组件

> 这个是只有开启keep-alive的时候才会出现的两个生命周期

```typescript
//新的两个
onActivated(()=>{
    console.log('keep-alive的初始化')
})

onDeactivated(()=>{
    console.log('keep-alive的卸载')
})
//对应有关联的两个生命周期
onMounted(()=>{
    console.log('初始化')//这个会随着onActivated一起生效
})
onUnMounted(()=>{
    console.log('卸载')//如果有onDeactivated，则优先生效onDeactivated，onUnMounted则不再生效
})
//所以有一些卸载操作我们可以写在keep-alive独有的生命卸载周期里面
//一些一次性操作则写到onMounted里面，比如一些接口请求一次就行了。onMounted只会在刚开始的时候挂载一次，你组件之前的切换就不会在重新初始化了，但是这个keep-alive的onActivated初始化则会在组件切换的时候不断触发
```

## keep-alive源码解析

> 开始时间点位于此章节第8分钟
>
> 源码目录 runtime-core/src/components/KeepAlive.ts

#### KeepAlive.ts文件

```typescript
//读取插槽的子节点，只能一个，如果多了会报错，他只渲染单个组件
const children = slots.default()
      const rawVNode = children[0]
      if (children.length > 1) {
        if (__DEV__) {
          warn(`KeepAlive should contain exactly one component child.`)
        }
        current = null
        return children
      } else if (
        !isVNode(rawVNode) ||
        (!(rawVNode.shapeFlag & ShapeFlags.STATEFUL_COMPONENT) &&
          !(rawVNode.shapeFlag & ShapeFlags.SUSPENSE))
      ) {
        current = null
          //最后返回的其实还是我们的组件，keep-alive只是一个抽象组件，本身并不渲染
        return rawVNode
      }


	//Vnode的key作为缓存的key
      pendingCacheKey = key
	//KeepLive组件返回的函数根据 vnode 对象的 key 去缓存中查找是否有缓存的组件
	//如果缓存存在，则继承组件实例，并将其用于描述组件的vnode 对象标记为 COMPONENT_KEPT_ALIVE
	//这样渲染器旧不会重新创建新的组件实例：如果缓存不存在，则将vnode对象的key添加到keys集合中
      if (cachedVNode) {
        // copy over mounted state 如果缓存中存在缓存的组件
        vnode.el = cachedVNode.el
        vnode.component = cachedVNode.component
          //无需创建组件实例，继承缓存的组件即可
        if (vnode.transition) {
          // recursively update transition hooks on subTree 如果组件上有动画，处理动画
          setTransitionHooks(vnode, vnode.transition!)
        }
        // avoid vnode being mounted as fresh	标记Vnode 不会重新渲染 ShapeFlags512静态标记(就是不会销毁重新创建，就不再去走那个卸载操作)
        vnode.shapeFlag |= ShapeFlags.COMPONENT_KEPT_ALIVE
        // make this key the freshest  key保持最新
        keys.delete(key)
        keys.add(key)
      } else {
          //将vnode 的key 添加到keys集合中，keys集合用户维护缓存组件key
        keys.add(key)
        // prune oldest entry
        if (max && keys.size > parseInt(max as string, 10)) {
          pruneCacheEntry(keys.values().next().value)
        }
      }
```

### props

```typescript
 props: {
    include: [String, RegExp, Array], // 配置了该属性，那么只有名称匹配的组件会被缓存
    exclude: [String, RegExp, Array], // 配置了该属性，那么任何名称匹配的组件都不会被缓存
    max: [String, Number]// 最多可以缓存多少组件实例
  },
 
  setup(props: KeepAliveProps, { slots }: SetupContext) {
    const instance = getCurrentInstance()!
    const sharedContext = instance.ctx as KeepAliveContext
  })
```

```js
      const { include, exclude, max } = props//这个就是keep-alive三个参数，(缓存、不缓存、缓存最大数)
        //如果include 子组件名称 不包含子组件名称 和  exclude包含的名字 就不该缓存 直接返回
      if (
        (include && (!name || !matches(include, name))) ||
        (exclude && name && matches(exclude, name))
      ) {
        current = vnode
        return rawVNode
      }
```



```js
    watch(
      () => [props.include, props.exclude],
      ([include, exclude]) => {
        //props 是响应式的 所以这个操作需要在做一遍
        include && pruneCache(name => matches(include, name))
        exclude && pruneCache(name => !matches(exclude, name))
      },
      // prune post-render after `current` has been updated
      { flush: 'post', deep: true }
    )
```

### KeepAlive缓存设计

```js
    //KeepAlive 组件的缓存管理
    const cache: Cache = new Map()
    const keys: Keys = new Set()
```

```js
    let pendingCacheKey: CacheKey | null = null
    //缓存函数
    const cacheSubtree = () => {
      // fix #1621, the pendingCacheKey could be 0
      if (pendingCacheKey != null) {//只有pendingCacheKey不为空值才会将这个组件存到cache里面
        cache.set(pendingCacheKey, getInnerChild(instance.subTree))
      }
    }
 
    //KeepLive 组件中对缓存的管理时，首先会在组件的 onMounted 或 onUpdated 生命周期钩子函数中设置缓存，如下代码所示：
    onMounted(cacheSubtree)
    onUpdated(cacheSubtree)
```

```js
   //Vnode 的key 作为缓存的key
      pendingCacheKey = key
      //KeepLive组件返回的函数中根据 vnode 对象的 key 去缓存中查找是否有缓存的组件，
       //如果缓存存在，则继承组件实例，并将用于描述组件的 vnode 对象标记为 COMPONENT_KEPT_ALIVE，
       //这样渲染器就不会重新创建新的组件实例；如果缓存不存在，则将 vnode 对象的key 添加到 keys 集合中
      if (cachedVNode) {
        // 如果缓存中存在缓存的组件
        vnode.el = cachedVNode.el
        vnode.component = cachedVNode.component
        //无须创建组件实例，继承缓存的组件即可
        if (vnode.transition) {
          // 如果组件上有动画，处理动画
          setTransitionHooks(vnode, vnode.transition!)
        }
        // 标记Vnode 不会重新渲染
        vnode.shapeFlag |= ShapeFlags.COMPONENT_KEPT_ALIVE
        // make this key the freshest
        keys.delete(key)
        keys.add(key)
      } else {
        //将 vnode 的key 添加到 keys 集合中，keys 集合用户维护缓存组件的 key
        keys.add(key)
        // prune oldest entry
        if (max && keys.size > parseInt(max as string, 10)) {
          pruneCacheEntry(keys.values().next().value)
        }
      }
```

### 生命周期

```js
   //隐藏容器
    const storageContainer = createElement('div')
    //在实例上注册两个钩子函数 activate，  deactivate
    sharedContext.activate = (vnode, container, anchor, isSVG, optimized) => {
      const instance = vnode.component!
      move(vnode, container, anchor, MoveType.ENTER, parentSuspense)
      // props 可能会发生变化，因此需要执行 patch 过程
      patch(
        instance.vnode,
        vnode,
        container,
        anchor,
        instance,
        parentSuspense,
        isSVG,
        vnode.slotScopeIds,
        optimized
      )
        //patch完成后，执行子节点的activate 和 deactivate(两个生命周期)
      queuePostRenderEffect(() => {//隐藏队列
        instance.isDeactivated = false
        if (instance.a) {
          invokeArrayFns(instance.a)
        }
        const vnodeHook = vnode.props && vnode.props.onVnodeMounted
        if (vnodeHook) {
          invokeVNodeHook(vnodeHook, instance.parent, vnode)
        }
      }, parentSuspense)
      if (__DEV__ || __FEATURE_PROD_DEVTOOLS__) {
        // Update components tree
        devtoolsComponentAdded(instance)
      }
    }
    sharedContext.deactivate = (vnode: VNode) => {
      const instance = vnode.component!
      //将组件移动到隐藏容器中
      //在 “卸载” 组件时，并不是真正的卸载，而是调用 move 方法，将组件搬运到一个隐藏的容器中
      move(vnode, storageContainer, null, MoveType.LEAVE, parentSuspense)
      queuePostRenderEffect(() => {
        if (instance.da) {
          invokeArrayFns(instance.da)
        }
        const vnodeHook = vnode.props && vnode.props.onVnodeUnmounted
        if (vnodeHook) {
          invokeVNodeHook(vnodeHook, instance.parent, vnode)
        }
        instance.isDeactivated = true
      }, parentSuspense)
      if (__DEV__ || __FEATURE_PROD_DEVTOOLS__) {
        // Update components tree
        devtoolsComponentAdded(instance)
      }
    }
```

# 第二十一章 — transition动画组件

> Vue 提供了 transition 的封装组件，在下列情形中，可以给任何元素和组件添加进入 / 离开过渡:
>
> - 条件渲染 (使用 v-if)
> - 条件展示 (使用 v-show)
> - 动态组件
> - 组件根节点
>
> 自定义 transition 过渡效果，你需要对 `transition` 组件的 `name` 属性自定义。并在 css 中写入对应的样式
>
> 之前我们都是通过v-show跟v-if来切换形式隐藏比较生硬。transition就是Vue为了解决这个事情才出来的

### 1. 过渡的类名

在进入 / 离开的过渡中，会有 6 个 class 切换。

1. ### 过渡 class

   > 在进入 / 离开的过渡中，会有 6 个 class 切换。

|        进入        |                             效果                             |
| :----------------: | :----------------------------------------------------------: |
|  **v-enter-from**  | **定义进入过渡的开始状态。在元素被插入之前生效，在元素被插入之后的下一帧移除** |
| **v-enter-active** | **定义进入过渡生效时的状态。在整个进入过渡的阶段中应用，在元素被插入之前生效，在过渡 / 动画完成之后移除。这个类可以被用来定义进入过渡的过程时间，延迟和曲线函数** |
|   **v-enter-to**   | **定义进入过渡的结束状态。在元素被插入之后下一帧生效 (与此同时 `v-enter-from` 被移除)，在过渡 / 动画完成之后移除。** |
|        离开        |                             效果                             |
|  **v-leave-from**  | **定义离开过渡的开始状态。在离开过渡被触发时立刻生效，下一帧被移除。** |
| **v-leave-active** | **定义离开过渡生效时的状态。在整个离开过渡的阶段中应用，在离开过渡被触发时立刻生效，在过渡 / 动画完成之后移除。这个类可以被用来定义离开过渡的过程时间，延迟和曲线函数。** |
|   **v-leave-to**   | **离开过渡的结束状态。在离开过渡被触发之后下一帧生效 (与此同时 `v-leave-from` 被移除)，在过渡 / 动画完成之后移除。** |

```js
       <button @click='flag = !flag'>切换</button>
       <transition name='fade'>  //用transition包住我们想要生效的部分，name=fade，我们就能根据这个fade写对应的状态
         <div v-if='flag' class="box"></div>
       </transition>
```

```js
//开始过度，这个fade就是我们设定的name
.fade-enter-from{
   background:red;
   width:0px;
   height:0px;
   transform:rotate(360deg)
}
//开始过度了
.fade-enter-active{
  transition: all 2.5s linear;//全部内容都过渡，过渡时间2.5s，匀速
}
//过度完成
.fade-enter-to{//通常不写，或者是跟开始过渡的时候保持一致，因为跟开头不一样会很突兀
   background:yellow;
   width:200px;
   height:200px;
}
//离开的过度
.fade-leave-from{
  width:200px;
  height:200px;
  transform:rotate(360deg)//旋转360度
}
//离开中过度
.fade-leave-active{
  transition: all 1s linear;
}
//离开完成
.fade-leave-to{
  width:0px;
   height:0px;
}
```

### 2. 自定义过渡 class 类名

trasnsition props

- `enter-from-class`
- `enter-active-class`
- `enter-to-class`
- `leave-from-class`
- `leave-active-class`
- `leave-to-class`

```js
<transition enter-from-class="随便取名"></transition>
//然后在css中就能直接 .随便取名{}
```

> 自定义过度时间 单位毫秒
>
> 你也可以分别指定进入和离开的持续时间：靠:duration来决定，单位毫秒

```js
<transition :duration="1000">...</transition>
 
 
<transition :duration="{ enter: 500, leave: 800 }">...</transition>
```

> 通过自定义 class 结合 css 动画库 animate css
>
> 安装库 npm install animate.css
>
> 引入 import 'animate.css'
>
> 使用方法
>
> 官方文档 [Animate.css | A cross-browser library of CSS animations.](https://animate.style/)
>
> 在4版本后的动画库要加上前缀`animate__animated`

```js
        <transition
            leave-active-class="animate__animated animate__bounceInLeft"
            enter-active-class="animate__animated animate__bounceInRight"
        >
            <div v-if="flag" class="box"></div>
        </transition>
```

### 3.transition [生命周期](https://so.csdn.net/so/search?q=生命周期&spm=1001.2101.3001.7020) 8 个

```js
  @before-enter="beforeEnter" 		//对应enter-from		进入之前
  @enter="enter"			  		//对应enter-active    进入
  @after-enter="afterEnter"   		//对应enter-to		进入后
  @enter-cancelled="enterCancelled" //显示过度打断         进度被打断
  @before-leave="beforeLeave"		//对应leave-from		离开之前
  @leave="leave"					//对应enter-active	离开
  @after-leave="afterLeave"			//对应leave-to		离开之后
  @leave-cancelled="leaveCancelled" //离开过度打断		 离开被打断
```

> 当只用 JavaScript 过渡的时候，在 **`enter` 和 `leave` 钩子中必须使用 `done` 进行回调**，除了进行回调还有决定过渡效果什么时候完成
>
> 结合 gsap 动画库使用 [GreenSock](https://greensock.com/)

```js
const beforeEnter = (el: Element) => {//接收一个Element类型
    console.log('进入之前from', el);
}
const Enter = (el: Element,done:Function) => {
    console.log('过度曲线');
    setTimeout(()=>{
       done()
    },3000)
}
const AfterEnter = (el: Element) => {
    console.log('to');
}
```

### appear

> 通过这个属性可以设置初始节点过度 就是页面加载完成就开始动画 对应三个状态(只会做首次的)

```js
appear-active-class=""
appear-from-class=""
appear-to-class=""
appear
//跟class是一样的，在css中用.xxx{}来使用
```

# 第二十二章 — transition-group过渡列表

> - 单个节点
> - 多个节点，每次只渲染一个
>
> 那么怎么同时渲染整个列表，比如使用 `v-for`？在这种场景下，我们会使用 `<transition-group>` 组件。在我们深入例子之前，先了解关于这个组件的几个特点：
>
> - 默认情况下，它不会渲染一个包裹元素，但是你可以通过 `tag` attribute 指定渲染一个元素。
> - [过渡模式](https://v3.cn.vuejs.org/guide/transitions-enterleave.html#过渡模式)不可用，因为我们不再相互切换特有的元素。
> - 内部元素**总是需要**提供唯一的 `key` attribute 值。
> - CSS 过渡的类将会应用在内部的元素中，而不是这个组 / 容器本身。
> - 生命周期是跟transition共通的，自定义过渡类名也是一样的+

```js
<transition-group>
     <div style="margin: 10px;" :key="item" v-for="item in list">{{ item }</div>
</transition-group>
```

```js
const list = reactive<number[]>([1, 2, 4, 5, 6, 7, 8, 9]) //使用TS定义类型
const Push = () => {
    list.push(123)
}
const Pop = () => {
    list.pop()
}
```

## 添加删除过渡特效

```js
//然后通过按钮@click操控add

//使用的是animate组件库
import 'animate'
const list = reactive<number[]>([1,2,3,4,5,6,7,8,9])
const add = ()=>{
    list.push(list.length+1)
}
const POP = ()=>{
    list.pop()//数组的删除
}
```

## 列表的移动过渡

> **平面过渡动画酷炫**
>
> `<transition-group>` 组件还有一个特殊之处。除了进入和离开，它还可以为定位的改变添加动画。只需了解新增的 **`v-move` 类**就可以使用这个新功能，它会应用在元素改变定位的过程中。像之前的类名一样，它的前缀可以通过 `name` attribute 来自定义，也可以通过 `move-class` attribute 手动设置
>
> 需要安装lodash动画库
>
> lodash报错需要安装ts声明文件库 => npm install @types/lodash -D
>
> - 在package.json中可以看到，"@types/lodash": "^4.14.186",我这里是不知道为什么无法靠命令安装上，自己手动加上去才给提示
> - 安装完记得重新npm install来一遍下载依赖

```JS
<template>
    <div>
        <button @click="shuffle">Shuffle</button>
        <transition-group class="wraps" name="mmm" tag="ul">
            //tag属性就是默认再套一层元素，可加可不加，比如这里套的就是ul，你要套div啥的都可以，默认是不加的
            <li class="cell" v-for="item in items" :key="item.id">{{ item.number }}</li>
			//记得绑定:key
        </transition-group>
    </div>
</template>
  
<script setup  lang='ts'>
import _ from 'lodash'
import { ref } from 'vue'
let items = ref(Array.apply(null, { length: 81 } as number[]).map((_, index) => {
    return {
        id: index,
        number: (index % 9) + 1 //希望从1开始，所以加个1
    }
}))
const shuffle = () => {
    items.value = _.shuffle(items.value)//重新随机排列，是lodash的方法
    //原生的随机则是：.sort()=>Math.random()-0.5
}
</script>
  
<style scoped lang="less">
.wraps {
    display: flex;
    flex-wrap: wrap;
    width: calc(25px * 10 + 9px);//算宽度的
    .cell {
        width: 25px;
        height: 25px;
        border: 1px solid #ccc;
        list-style-type: none;
        display: flex;
        justify-content: center;
        align-items: center;
    }
}
 
.mmm-move {
    transition: transform 0.8s ease;
}
</style>
```

展示Array.apply的区别：相对于new Array而言，一个是空属性，一个却能默认存放undefined<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221022022918006-1711983932539-32.png" alt="image-20221022022918006" style="zoom:50%;" />

随机排列效果图：

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221022025921945-1711983932539-31.png" alt="image-20221022025921945" style="zoom:50%;" />

## 状态过渡

> Vue 也同样可以给数字 Svg 背景颜色等添加过度动画 今天演示数字变化

```js
<template>
    <div>
    	//v-model双重绑定
        <input step="20" v-model="num.current" type="number" />
        <div>{{ num.tweenedNumber.toFixed(0) }}</div>
		//toFixed(0)决定后面小数点数量
    </div>
</template>
    
<script setup lang='ts'>
import { reactive, watch } from 'vue'
import gsap from 'gsap'  //gsap是动画库
const num = reactive({
    tweenedNumber: 0,
    current:0
})
 
watch(()=>num.current, (newVal,oldVal) => {//监听
    gsap.to(num, {//gsap动画库的一个to方法，第一个参数是一个过渡的对象，第二个是配置项，用来过渡对象的属性
        duration: 1, //过渡时间，单位秒
        tweenedNumber: newVal //过渡对象，将新的值newVal赋给tweenedNumber
    })
})
 
</script>
    
<style>
</style>
```

# 第二十三章 — 依赖注入provide/inject & 源码解析

> ### Provide / Inject
>
> 通常，当我们需要从父组件向子组件传递数据时，我们使用 [props](https://v3.cn.vuejs.org/guide/component-props.html)。想象一下这样的结构：有一些深度[嵌套](https://so.csdn.net/so/search?q=嵌套&spm=1001.2101.3001.7020)的组件，而深层的子组件只需要父组件的部分内容。在这种情况下，如果仍然将 prop 沿着组件链逐级传递下去，可能会很麻烦。
>
> 官网的解释很让人疑惑，那我翻译下这几句话：
>
> provide 可以在祖先组件中指定我们想要**提供**给后代组件的数据或方法，而在任何后代组件中，我们都可以使用 inject 来接收 provide **提供**的数据或方法。
>
> ---
>
>  TIPS 你如果传递普通的值 是不具有响应式的 需要通过 ref reactive 添加响应式
>
> **使用场景**
>
> 当父组件有很多数据需要分发给其子代组件的时候， 就可以使用 provide 和 inject。

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221022034628429-1711983932539-34.png" alt="image-20221022034628429" style="zoom:50%;" />

## 父组件传递数据

```js
<template>
    <div class="App">
        <button>我是App</button>
        <A></A>
    </div>
</template>
    
<script setup lang='ts'>
import { provide, ref } from 'vue'
import A from './components/A.vue'
let flag = ref<number>(1)
provide('flag', flag)  //接收两个参数，一个是key，一个是value(这个在template中绑定对应的数据也就是你要传给后代组件的信息，单项绑定双向绑定都行)
</script>
    
<style>
.App {
    background: blue;
    color: #fff;
}
</style>
```

## 子组件接收数据

```js
<template>
    <div style="background-color: green;">
        我是B
        <button @click="change">change falg</button>
        <div>{{ flag }}</div>
    </div>
</template>
    
<script setup lang='ts'>
import { inject, Ref, ref } from 'vue' //使用inject去读取
 
const flag = inject<Ref<number>>('flag', ref(1))//引入Ref类型，然后Ref类型支持number，这里会显示flag:Ref<number>|undefined，如果不想要undefined，也可以断言一下
 
const change = () => {
    flag.value = 2  //传值的话，比如字符串这类可能不符合，解决方法1：类型可以非空断言(!.)一下，可选操作符这里用不了。方法2：在上方的1的地方套上ref。这里采用第二种方法
}
</script>
    
<style>
</style>
```

### vue3小妙招

```js
<script setup lang="ts">
    import{inject} from 'vue'
    import{Ref} from "vue"
    const color = inject<Ref<number>>('color')
</script>

<style>
.box{//Vue3特有功能，css也有v-bind的功能去绑定setup中的变量
    background:V-bind(color);
}    
</style>
```

## 源码解析部分

> 开始时间点：6分16秒

```typescript
export function provide<T>(key: InjectionKey<T> | string | number, value: T) {
  if (!currentInstance) {
    if (__DEV__) {
      warn(`provide() can only be used inside setup().`)
    }
  } else {
    //currentInstance当前组件的实例去读取provides
    let provides = currentInstance.provides
    //provide默认情况下，实例继承父类的 provides 对象，如果当前组件有自己的provide
    //它使用 父provide 对象作为原型来创建自己的provide 对象，在inject只需要查询原型链就行了
    const parentProvides =
      currentInstance.parent && currentInstance.parent.provides
    if (parentProvides === provides) {//判断父组件provides与子组件的provides是否一样
      //它会从父组件的provide基础上去创建一个新的对象
      provides = currentInstance.provides = Object.create(parentProvides)//原理是在下方原型链部分原理
    }
    //在新的对象上增加这次provide的值
    provides[key as string] = value
  }
}
```

> 原型链部分原理

```js
var a = {name:1}//输出undefined
var b = Object.create(a)//输出undefined
b.name = 1//能够读取到，因为b已经继承了a，形成了原型链
```

```ts
export function inject<T>(key: InjectionKey<T> | string): T | undefined
export function inject<T>(
  key: InjectionKey<T> | string,
  defaultValue: T,//没有传defaultValue就会返回当前的类型和undefined(在上面的T|undefined)
  treatDefaultAsFactory?: false
): T
export function inject<T>(
  key: InjectionKey<T> | string,
  defaultValue: T | (() => T),
  treatDefaultAsFactory: true
): T
export function inject(
  key: InjectionKey<any> | string,
  defaultValue?: unknown,
  treatDefaultAsFactory = false
) {
  // fallback to `currentRenderingInstance` so that this can be called in
  // a functional component
  const instance = currentInstance || currentRenderingInstance
  if (instance) {
    // #2400
    // to support `app.use` plugins,
    // fallback to appContext's `provides` if the instance is at root
    //inject:查询父级的provide，如果实例是在根目录，回退到appContext的provide中
    const provides =
      instance.parent == null//能读取到会尝试读取父组件
        ? instance.vnode.appContext && instance.vnode.appContext.provides//读不到则返回这个
        : instance.parent.provides//读得到就返回父组件的provides，是一个三元表达式。是一个原型链，找不到会往上一直找

    if (provides && (key as string | symbol) in provides) {
      // TS doesn't allow symbol as index type
      return provides[key as string]//读取到就return了
    } else if (arguments.length > 1) {//读不到就开始报错了
      return treatDefaultAsFactory && isFunction(defaultValue)
        ? defaultValue.call(instance.proxy)
        : defaultValue
    } else if (__DEV__) {
      warn(`injection "${String(key)}" not found.`)
    }
  } else if (__DEV__) {
    warn(`inject() can only be used inside setup() or functional components.`)
  }
}
```

> ### PS:全局事件总线在Vue3已废除，但Vue官网给出两个第三方库的推荐替代
>
> ### mitt 和 tiny-emitter 对比分析
>
> #### 共同点
>
> 1. 都支持 on (type, handler)、off (type, [handler]) 和 emit (type, [evt]) 三个方法来注册、注销、派发事件
>
> #### 不同点
>
> - emit
>   1.  有 all 属性，可以拿到对应的事件类型和事件处理函数的映射对象，是一个 Map 不是 {}
>   2.  支持监听 '*' 事件，可以调用 emitter.all.clear () 清除所有事件
>   3. 返回的是一个对象，对象存在上面的属性
> -  tiny-emitter
>   1.  支持链式调用，通过 e 属性可以拿到所有事件（需要看代码才知道）
>   2. 多一个 once 方法 并且 支持设置 this (指定上下文 ctx)
>   3.  返回的一个函数实例，通过修改该函数原型对象来实现的
>
> 更推荐使用**mitt**

# 第二十四章 — 兄弟组件和Bus

> 两种方式的传参
>
> - 借助父组件传参
> - Event Bus

## 借助父组件传参

> 例如父组件为App，子组件为 A 和 B ，他两个是同级的
>
> A组件想派发事件给B组件，借助父组件进行一个实现(同级的传输信息)
>
> A 组件派发事件通过 App.vue 接受 A 组件派发的事件然后在 Props 传给 B 组件 也是可以实现的
>
> 缺点就是比较麻烦 ，无法直接通信，只能充当桥梁

### App组件

```typescript
<template>
    <div>
        <A @on-click="getFalg"></A>  //这里@xxx的on-click是A组件派发出来的
        <B :flag="Flag"></B>  //在这里将Flag传输到B组件中
    </div>
</template>
    
<script setup lang='ts'>
import A from './components/A.vue'
import B from './components/B.vue'
import { ref } from 'vue'
let Flag = ref<boolean>(false)
const getFalg = (flag: boolean) => {
   Flag.value = flag;//在父组件声明了一个Flag变量来接收A子组件传过来的值，下一刻就要传到B子组件去了(起到一个桥梁的作用)
}
</script>
    
<style>
</style>
```

### A组件派发

```typescript
<template>
<div>
  <button @click="emitB">派发一个事件</button>
</div>
</template>

<script setup lang="ts">
    const emit = defineEmits(['on-click'])
    let flag = false
	const emitB = ()=>{
        flag = !flag
		emit('on-click',flag)//将flag内容通过on-click派发了出去。这个on-click是可以随便取的
    }
</script>

<style scoped>

</style>
```

### B组件

```typescript
<template>
    <div>
		{{flag}}
    </div>
</template>

<script setup lang="ts">
	type Props = {
        flag:boolean
    }

	defineProps<Props>()
</script>

<style scoped>

</style>
```

## Bus(mitt)

> 在Vue2中我们经常使用全局事件总线来进行组件之间的传值，在main.ts中进行配置，但是在Vue3中被废除了。从而延伸新的方法
>
> 我们在 Vue2 可以使用 $emit 传递 $on 监听 emit 传递过来的事件
>
> 这个原理其实是运用了 JS 设计模式之发布订阅模式

### 简易版发布订阅模式

> 在小满写的Bus.ts文件下
>
> [forEach](https://so.csdn.net/so/search?q=forEach&spm=1001.2101.3001.7020) 函数用来遍历数组
>
> forEach 方法需要一个函数作为参数 
>
> —— 像这种函数，由我们创建但不由我们调用的，称为[回调](https://so.csdn.net/so/search?q=回调&spm=1001.2101.3001.7020)函数
>
> —— [数组](https://so.csdn.net/so/search?q=数组&spm=1001.2101.3001.7020)中有几个元素，函数就会执行几次，每次执行时，浏览器会将遍历的元素，以实参的形式传递进来，我们可以定义形参来读取这些数据
>
> —— 浏览器会在回调函数中传递三个参数

#### TS版本

```typescript
 type BusClass<T> = {
    emit: (name: T) => void //这两个都不返回值，所以使用void，emit有一个自定义事件的名称参数
    on: (name: T, callback: Function) => void//第一个参数一样是自定义事件，第二个是函数类型的callback。emit的参数结果会传到callback中进行接收
}
type BusParams = string | number | symbol //定义BusParams属于这三种类型
type List = {
    [key: BusParams]: Array<Function> //是动态的，所以我们使用对象嵌名的方式，Array这样类型的值可以接收多个的
}
class Bus<T extends BusParams> implements BusClass<T> {
    list: List
    constructor() {
        this.list = {}
    }
    //emit里的参数要传给on的
    emit(name: T, ...args: Array<any>) {//参数可以多个的，这里就使用ES6中解构的方式来取出剩下所有的参数
        let eventName: Array<Function> = this.list[name]//取到下面on里push的集合
        eventName.forEach(ev => {
            ev.apply(this, args)//使用apply的方式调用，这个在前面部分也有使用过，在列表的移动过渡部分
        })
    }
    //事件收集(on接收订阅，emit发布)
    on(name: T, callback: Function) {//on可以多次注册
        let fn: Array<Function> = this.list[name] || [];//由于多次注册，名字可能会一样，所以我们使用对象嵌名的方式，没注册就返回空数组
        fn.push(callback)//将回调函数callback添加(push)进去再赋值给list
        this.list[name] = fn
    }
}
 
export default new Bus<number>()//导出
```

#### JS版本

```js
class EventBus{
    constructor(){
        this.callback = {}
    }
}

emit(name,...args){
    this.callback[name].forEach((fn)=>{
        fn.apply(this.args)
    })
}

on(name,fn){
    if(this.callback[name]){
        this.callback.push(fn)
    }else{
        this.callback[name] = [fn]
    }
}

const bus = new EventBus()

bus.on('on',(o) =>{
    console.log(o)
})
bus.emit('on',"小满是外黄内更黄的阿宅")
```

> 然后挂载到 Vue config 全局就可以使用啦

## Mitt(二十四章 — 下半部分)

> 在 vue3 中 `$on`，`$off` 和 `$once` 实例方法已被移除，组件实例不再实现事件触发接口，因此大家熟悉的 EventBus 便无法使用了。然而我们习惯了使用 EventBus，对于这种情况我们可以使用 Mitt 库（其实就是我们视频中讲的发布订阅模式的设计）

### 1. 安装 

> - 安装的环境不同
>   -D 是 --save-dev 的简写，会安装在开发环境中 (production) 中的 devPendencies 中
>   -S 是 --save 的简写，会安装在生产环境中 (development) 中的 dependencies 中
> - 包管理工具适用范围不同
>   --save-dev 只是适用于 npm 和 cnpm
>   对于 yarn 不适用，比如 yarn add express --save-dev 会安装在生产环境中 (development) 中的 dependencies 中
>   因为 yarn 默认就会安装在生产环境中，yarn 只对 - D 安装的才会出现在开发环境中 (production) 中的 devPendencies 中

```javascript
npm install mitt -S
```

### 2.main.ts 初始化

> 全局总线，vue 入口文件 main.js 中挂载全局属性

```javascript
import { createApp } from 'vue'
import App from './App.vue'
import mitt from 'mitt'//引入

const Mit = mitt()//初始化

//TypeScript注册
// 由于必须要拓展ComponentCustomProperties类型才能获得类型提示
declare module "vue" {
    export interface ComponentCustomProperties {
        $Bus: typeof Mit//获取Mit所有的类型
    }
}

const app = createApp(App)

//Vue3挂载全局API
app.config.globalProperties.$Bus = Mit

app.mount('#app')
```

### 3 使用方法

> #### 通过 emit 派发， on 方法添加事件，off 方法移除，clear 清空所有

#### A 组件派发（emit）

```javascript
<template>
    <div>
        <h1>我是A</h1>
        <button @click="emit1">emit1，通过点击事件来触发派发</button>
        <button @click="emit2">emit2</button>
    </div>
</template>

<script setup lang='ts'>
import { getCurrentInstance } from 'vue'
const instance = getCurrentInstance();//初始化getCurrentInstance函数
const emit1 = () => {
    //emit(自定义事件，参数...)参数可以用...args解构
    instance?.proxy?.$Bus.emit('on-num', 100)//？可选
}
const emit2 = () => {
    instance?.proxy?.$Bus.emit('*****', 500)
}
</script>

<style>
</style>
```

#### B 组件监听（on）

```javascript
<template>
    <div>
        <h1>我是B</h1>
    </div>
</template>

<script setup lang='ts'>
import { getCurrentInstance } from 'vue'//引入
//getCurrentInstance允许访问对高级用法或库创建者有用的内部组件实例。
const instance = getCurrentInstance()
instance?.proxy?.$Bus.on('on-num', (num) => {//监听on-num这个自定义事件
    console.log(num,'===========>B')
})
</script>

<style>
</style>
```

#### 监听所有事件（ on ("*") ）

```javascript
instance?.proxy?.$Bus.on('*',(type,num)=>{//*表示监听所有的事件触发
    console.log(type,num,'===========>B')
})
```

#### 移除监听事件（off）

```javascript
const Fn = (num: any) => {
    console.log(num, '===========>B')
}
instance?.proxy?.$Bus.on('on-num',Fn)//listen
instance?.proxy?.$Bus.off('on-num',Fn)//unListen，移除监听
```

#### 清空所有监听（clear）

```javascript
instance?.proxy?.$Bus.all.clear()//取消所有的mitt事件
```

# 第二十五章 — TSX & vite插件

> **完整版用法 请看 [@vue/babel-plugin-jsx - npm](https://www.npmjs.com/package/@vue/babel-plugin-jsx)**
>
> 我们之前呢是使用 Template 去写我们模板。现在可以扩展另一种风格 TSX 风格
>
> vue2 的时候就已经支持 jsx 写法，只不过不是很友好，随着 vue3 对 `typescript`的支持度，tsx 写法越来越被接受

## 1. 安装插件

> npm install @vitejs/plugin-vue-jsx -D

### vite.config.ts 配置

```tsx
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import vueJsx from '@vitejs/plugin-vue-jsx';
//此时是重录版本的笔记，我在使用的时候发现编辑器的提示变成这样，上下两种自己到时候看情况选择就行
import vueJsxPlugin from "@vitejs/plugin-vue-jsx";
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(),vueJsx()]//或者变成vueJsxPlugin()
})

//------------>点入jsx的源码种能看到如下
import { FilterPattern, Plugin } from 'vite';
import { VueJSXPluginOptions } from '@vue/babel-plugin-jsx';

interface FilterOptions {
    include?: FilterPattern;
    exclude?: FilterPattern;
}
declare type Options = VueJSXPluginOptions & FilterOptions & {
    babelPlugins?: any[];
};

declare function vueJsxPlugin(options?: Options): Plugin;

export { FilterOptions, Options, vueJsxPlugin as default };
```

## 2. 修改 tsconfig.json 配置文件

```ts
    "jsx": "preserve",
    "jsxFactory": "h",
    "jsxFragmentFactory": "Fragment",
```

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221023171319315-1711983932539-33.png" alt="image-20221023171319315" style="zoom:50%;" />

> 配置完成就可以使用啦
>
> 在目录新建一个 xxxxxx.tsx 文件

## 3. 使用 TSX

> TIPS tsx 不会自动解包，使用 ref 加.vlaue ! ! !

### TSX书写风格(3种)

> 方式1：直接返回一个渲染函数

```tsx
App.tsx文件
//第一种，使用export导出渲染函数function
export default function(){
    
    return(<div>小满10点录制TSX 1点录制vite插件 6点剪辑结束 请观众姥爷给两个币</div>)
}
```

```ts
App.vue文件
<template>
<xiaoman></xiaoman>
</template>

<script setup lang='ts'>
//引入App.tsx的内容，当成组件来使用，App.tsx不需要加后缀tsx，命名随意，编辑器会自动去找(就是自己命名的时候不会给提示)
import xiaoman from './App'
</script>

<style  lang='less'>

</style>
```

**成功展示**![image-20221121012230783](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221121012230783-1711983932539-35.png)

> 方式2：需要引入defineComponent，也就是optionsAPi的写法

```tsx
App.tsx文件
//方式2，类似vue2的写法
import {defineComponent} from "vue";
export default defineComponent({
    //defineComponent函数内是一个对象
    data(){
        return{
            name:"麒麟哥是一个传奇"
        }
    }
    ,
    render(){
        //在TSX中使用变量是单花括号，跟Vue的双花括号是有所区别的
        return (<div>{this.name}</div>)
    }
})
```

![image-20221121013400899](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221121013400899-1711983932539-36.png)

> 方式3：Vue3的setup语法糖函数模式

```tsx
import {defineComponent} from "vue";
export default defineComponent({
    setup(){
        return ()=>(
            <div>小余今天也有在好好学习</div>
    )
    }
})
```

![image-20221121013418369](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221121013418369-1711983932539-37.png)

> 方式总结：方式1跟方式3常用，方式2不常用

### sx 支持 v-model 的使用

```JS
import { ref } from 'vue'
 
let v = ref<string>('')
 
const renderDom = () => {
    return (//JS和HTML的代码混合写的
        <>
           <input v-model={v.value} type="text" /> //所有的变量都用小括号代替{v.value}
           <div>
               {v.value}//插值语法的话不再是{{}}，而是{}，只需要一个括号了
           </div>
        </>
    )
}
 
export default renderDom
```

### v-show

```Tsx
import { ref } from 'vue'
 
let flag = ref(false)
 
const renderDom = () => {
    return (
        <div>
           <div v-show={flag.value}>景天</div>
           <div v-show={!flag.value}>雪见</div>
        </div>
    )
}
 
export default renderDom
//------------------重录写法(方式3)
//支持v-show
import {defineComponent} from "vue";
export default defineComponent({
    setup(){
        const flag = false
        return ()=>(//单花括号代替vue的双引号
            <div v-show={flag}>小余今天也有在好好学习</div>
    )
    }
})
//ref template下自动解包，就是自己去读取.value，不需要你自己加上去 tsx则是不会(ref需要手动解包)
import {defineComponent, ref} from "vue";
export default defineComponent({
    setup(){
        const flag = ref(false)//ref的
        return ()=>(
            <div v-show={flag.value}>小余今天也有在好好学习</div>
    )
    }
})
```

### v-if 是不支持的

> 所以需要改变风格
>
> 不支持什么？不支持像v-show那样通过等于true和false这样的布尔值来进行切换。需要使用三元表达式(JS编程思想)

```tsx
import { ref } from 'vue'
 
let flag = ref(false)
 
const renderDom = () => {
    return (
        <div>
            {//请注意这里是有括号的，单组括号在TSX模式下相当于插值语法
                flag.value ? <div>景天</div> : <div>雪见</div>
            }
        </div>
    )
}
 
export default renderDom
//------------------------------------------------>重录写法
//三元表达式替代v-if
import {defineComponent, ref} from "vue";
export default defineComponent({
    setup(){
        const flag = ref(false)
        return ()=>(
            <div>{flag.value ? <div>我true小满的裤衩子</div> : <div>我false草莓姐的舔狗</div>}</div>
    )
    }
})
```

效果展示![image-20221121015035504](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221121015035504-1711983932539-38.png)

### v-for 也是不支持的

> 跟上面不支持的一样的，不能用v-for去遍历数组

```tsx
import { ref } from 'vue'
 
let arr = [1,2,3,4,5]
 
const renderDom = () => {
    return (
        <>
            {
              arr.map(v=>{//使用了原生js的内置函数map去遍历，记得return回内容
                  return <div>${v}</div>
              })
            }
        </>
    )
}
 
export default renderDom
//----------------------------->重录写法，使用map去替代v-for
import {defineComponent, ref} from "vue";
export default defineComponent({
    setup(){
        const flag = ref(false)
        const data = [
            {
                name:"小满1"
            },
            {
                name:"草莓2"
            },
            {
                name:"汤姆3"
            }
        ]
        return ()=>(
            <div>{data.map(v=>{//原生与tsx结合的写法
                return <div>{v.name}</div>
            })}</div>
    )
    }
})
```

效果展示![image-20221121015533308](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221121015533308-1711983932539-39.png)

### v-bind 使用

> 直接赋值就可以

```tsx
import { ref } from 'vue'
 
let arr = [1, 2, 3, 4, 5]
 
const renderDom = () => {
    return (
        <>
            <div data-arr={arr}>1</div>
        </>
    )
}
 
export default renderDom
//-------------------------------------------->v-bind的写法
import {defineComponent, ref} from "vue";
export default defineComponent({
    setup(){
        const flag = ref(false)
        const data = [
            {
                name:"小满1"
            },
            {
                name:"草莓2"
            },
            {
                name:"汤姆3"
            }
        ]
        return ()=>(
            <>{data.map(v=>{//直接花括号赋值即可，但我这里莫名其妙报错，但是还是可以使用的。PS：在VSCode中不会报错
                return <div name={v.name}>{v.name}</div>
            })}</>
    )
    }
})
//错误信息：类型‘{name：string；}’不能赋值给类型‘ElementAttrs<HTMLAttributes>’。属性‘name’在类型‘ElementAttrs<HTMLAttributes>’上不存在。
```

效果展示：![image-20221121020242652](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221121020242652-1711983932539-41.png)

### v-on 绑定事件 所有的事件都按照 react 风格来

> - 所有事件有 on 开头
> - 所有事件名称首字母大写
>
> bind：将创建一个新函数，当这个函数被调用时，传入的第一个参数作为新的 this 对象，后续的参数作为绑定函数的参数，新函数与被调函数（绑定函数的目标函数）具有相同的函数体。
>
> 与 call 和 apply **相同点**：都可以传递参数
>
> **不同点**：apply 和 call 是会具体执行该[回调](https://so.csdn.net/so/search?q=回调&spm=1001.2101.3001.7020)函数，而 bind 是返回一个函数。且bind 之后无法再次通过 call 和 apply 修改 this 指向

```tsx
const renderDom = () => {
    return (
        <>
            <button onClick={clickTap}>点击</button>//这里不再是v-on，而是onClick(react的方式)
        	<button onClick={clickTap.bind(this,v)}>点击</button>//.bind(this指向,参数)参数方便下面接收
        </>
    )
}
 
const clickTap = (v:number) => {//触发点击事件
    console.log('click，我被点击到了',v);
}
 
export default renderDom
```

### Props 接受值

```js
import { ref } from 'vue'
 
type Props = {
    title:string
}
 
const renderDom = (props:Props) => {
    return (
        <>
        //props.title就能够直接从其他地方获取值过来了，前提是你在其他地方设置了title="我是标题，这里就是你要传的内容"
            <div>{props.title}</div>  
            <button onClick={clickTap}>点击</button>
        </>
    )
}
 
const clickTap = () => {
    console.log('click');
}
 
export default renderDom
//--------------------------------------->重录写法
interface Props{
    name?:String
}
import {defineComponent, ref} from "vue";
export default defineComponent({
    props:{//接收父级的值
      name:String
    },
    emits:['on-click'],
    setup(props:Props) {//传入props，为了防止你这里报错，你在上面定义接口的时候，记得?可选一下
        const flag = ref(false)
        const data = [
            {
                name:"小满1"
            },
            {
                name:"草莓2"
            },
            {
                name:"汤姆3"
            }
        ]
        const fn = (item:any)=>{
            console.log("触发了",item)
        }
        return ()=>(
            <div>
                <div>props:{props?.name}</div>
                <hr/>
                {data.map(v=>{
                    //onClick={()=>fn()为函数柯里化，这样当你点击小满 1 草莓 2 汤姆 3就会在控制台打印触发了
                return <div onClick={()=>fn(v)} name={v.name}>{v.name}</div>
            })}
            </div>
    )
    }
})
//----------------------------------->props传值中途失效了，不知道为什么，重开来了一遍又可以了，代码没用问题
```

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221121030607399-1711983932539-40.png" alt="image-20221121030607399" style="zoom:50%;" />

![image-20221204034704194](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221204034704194-1711983932539-42.png)



### Emit 派发

```js
type Props = {
    title: string
}

const renderDom = (props: Props,content:any) => {//这个content会传给下面那个bind里面的参数content
    return (
        <>
            <div>{props.title}</div>
            <button onClick={clickTap.bind(this,content)}>点击</button>
        </>
    )
}

const clickTap = (content:any) => {
    console.log("我被小满抽到了")
    content.emit('on-click',1)
}

export default renderDom
//---------------------------------------------------->重录代码
//支持v-show
interface Props{
    name?:string
}
import {defineComponent, ref} from "vue";
export default defineComponent({
    props:{//接收父级的值
        name:String
    },
    emits:['on-click'],
    setup(props:Props,{emit}) {//emit是属于setup的第二个参数，记得使用花括号{}将emit圈起来
        console.log(props.name);
        const flag = ref(false)
        const data = [
            {
                name:"小满1"
            },
            {
                name:"草莓2"
            },
            {
                name:"汤姆3"
            }
        ]
        const fn = (item:any)=>{
            console.log("触发了",item)
            emit('on-click',item)
        }
        return ()=>(
            <>
                <div>props:{props?.name}</div>
                <hr/>
                {data.map(v=>{
                    //onClick={()=>fn()为函数柯里化，这样当你点击小满 1 草莓 2 汤姆 3就会在控制台打印触发了
                return <div onClick={()=>fn(v)}> {v.name} </div>
            })}
            </>
    )
    }
})
```

### **接收Emit传过来的值**(父组件)

```tsx
<template>
<TransitionAA></TransitionAA>
  <renderDom @on-click="getNum" title="我是标题"></renderDom>
</template>

<script setup lang="ts">
import {ref, isRef, shallowRef, customRef, reactive} from 'vue'
  import renderDom from "./App"

const getNum = (num:number)=>{
  console.log(num)//打印出接收到的值
}
</script>

<style scoped>

</style>
//------------------------------------------------->重录代码
<template>
  <div>
    <xiaoman @on-click="getItem" name="小满的黑丝被props传递给他儿子了"/>
    <div>666</div>
  </div>
</template>

<script setup lang='ts'>
import xiaoman from './App'
const getItem = (item:any)=>{
  console.log(item,'已经传递到父组件了')
  console.log('Vue3笔记迭代十多个版本终于写完了')
}
</script>

<style  lang='less'>

</style>
```

> 派发生效

![image-20221204034914317](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221204034914317-1711983932539-43.png)

> 通过上面的例子，大家可以发现小满在视频里面的最外层<div>是只有一个<>的，事实上写不写都是可以生效的，由于小满在视频里面没有提这个事情，但不管加不加确实都是可以的
>
> ```tsx
>     return (
>         //不加
>         <>
>             <div>{props.title}</div>
>             <button onClick={clickTap.bind(this,content)}>点击</button>
>         </>
>         //加
>         <div>
>             <div>{props.title}</div>
>             <button onClick={clickTap.bind(this,content)}>点击</button>
>         </div>
>     )
> ```

### 插槽(重录新增)

```tsx
//这里是插槽部分
//props不要了，用_占位符代替，此时我使用webstorm提示_与slot具有any的隐式转换，给其定义上any防止报错
const A = (_:any,{slots}:any)=>(<>
    <div>{slots.default ? slots.default() : "默认值"}</div>
</>)

interface Props{
    name?:string
}
import {defineComponent, ref} from "vue";
export default defineComponent({
    props:{//接收父级的值
        name:String
    },
    emits:['on-click'],
    setup(props:Props,{emit}) {//emit是属于setup的第二个参数，记得使用花括号{}将emit圈起来
        console.log(props.name);
        const flag = ref(false)
        const data = [
            {
                name:"小满1"
            },
            {
                name:"草莓2"
            },
            {
                name:"汤姆3"
            }
        ]
        const fn = (item:any)=>{
            console.log("触发了",item)
            emit('on-click',item)
        }
        //定义插槽默认值
        const slot = {
            default:()=>(<div>default-小余今天被洛洛卷死了，slots-狗洛就该被抓去踩缝纫机</div>)
        }
        return ()=>(
            <>
            	//打印插槽的内容
                <A v-slots={slot}/>
                <hr/>
                <div>props:{props?.name}</div>
                <hr/>
                {data.map(v=>{
                    //onClick={()=>fn()为函数柯里化，这样当你点击小满 1 草莓 2 汤姆 3就会在控制台打印触发了
                return <div onClick={()=>fn(v)}> {v.name} </div>
            })}
            </>
    )
    }
})
```

- 打印效果如下

![image-20221204040819930](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221204040819930-1711983932539-50.png)

#### 多个插槽的使用

```tsx
//支持v-show
//props不要了，用_占位符代替，此时我使用webstorm提示_与slot具有any的隐式转换，给其定义上any防止报错
const A = (_:any,{slots}:any)=>(<>
    <div>{slots.default ? slots.default() : "默认值"}</div>
    {/*此时我们不想要默认值，要怎么做？其实只需要加上可选链操作符就可以了，有传内容在调用，进行多个插槽的使用*/}
    <div>{slots.foo?.()}</div>
</>)

interface Props{
    name?:string
}
import {defineComponent, ref} from "vue";
export default defineComponent({
    props:{//接收父级的值
        name:String
    },
    emits:['on-click'],
    setup(props:Props,{emit}) {//emit是属于setup的第二个参数，记得使用花括号{}将emit圈起来
        console.log(props.name);
        const flag = ref(false)
        const data = [
            {
                name:"小满1"
            },
            {
                name:"草莓2"
            },
            {
                name:"汤姆3"
            }
        ]
        const fn = (item:any)=>{
            console.log("触发了",item)
            emit('on-click',item)
        }
        const slot = {
            default:()=>(<div>default-小余今天被洛洛卷死了，slots-狗洛就该被抓去踩缝纫机</div>),
            //在这里进行定义多个插槽内容，插槽的内容将在这里定义好然后传递到变量A中，因为我们在return中的定义A中使用了v-slots，完成了传递的桥梁
            foo:()=>(<div>努力自律才是最好的入场券</div>)
        }
        return ()=>(
            <>
                <A v-slots={slot}/>
                <hr/>
                <div>props:{props?.name}</div>
                <hr/>
                {data.map(v=>{
                    //onClick={()=>fn()为函数柯里化，这样当你点击小满 1 草莓 2 汤姆 3就会在控制台打印触发了
                return <div onClick={()=>fn(v)}> {v.name} </div>
            })}
            </>
    )
    }
})
```

### v-model的使用(重录新增)

```tsx
import {defineComponent, ref} from "vue";
export default defineComponent({
    setup() {
        const v = ref<string>("")
        return ()=>(
            <>
                {/*v-model直接使用即可*/}
                <input type="text" v-model={v.value}/>
                <div>{v.value}</div>
            </>
        )
    }
})
```

![image-20221204042320424](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221204042320424-1711983932539-45.png)

## 手写Vite插件+babel使用

> 就是手写插件
>
> [Babel 网站](https://babeljs.io/)：常用于ES6语法转ES5
>
> 主要功能如下：
>
> ![image-20221204043403171](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221204043403171-1711983932539-44.png)

### 需要用到的第三方插件

> 需要用到的五个依赖包

```js
npm install @vue/babel-plugin-jsx //Vue给babel写的插件，编译ts的这些指令
npm install @babel/core//babel核心库用作转换
npm install @babel/plugin-transform-typescript//编译ts的
npm install @babel/plugin-syntax-import-meta//编译import这些语法的
npm install @types/babel__core//babel的声明文件
```

### 插件编写规范约定

**如果插件不使用Vte特有的钩子，可以作为兼客Rollup的插件来实现，推荐使用Rollup插件名称约定**

- Rollup插件应该有一个带`rollup-plugin-`前缀、语义清晰的名称。
- 在package.json中包含`rollup-plugin`和`vite-plugin`关键字。

这样，插件也可以用于纯Rollup或基于WMR的项目。

**对于Vite专属的插件：**

- Vite插件应该有一个带`vite-plugin-`前缀、语义清晰的名称。
- 在package.json中包含`vite-plugin`关键字。
- 在插件文档增加一部分关于为什么本插件是一个Vite专属插件的详细说明（如，本插件使用了Vite特有的插件钩子)。

**如果你的插件只适用于特定的框架，它的名字应该遵循以下前缀格试：**

- `vite-plugin-vue-`前缀作为Vue插件
- `vite-plugin-react-`前缀作为React插件
- `vite-plugin-svelte-`前缀作为Svelte插件

### 插件代码

> 新建的plugin文件夹（位于根目录中，与src同级）
>
> 代码就写在plugin文件夹下的index.ts文件中

```ts
import type { Plugin } from 'vite'
import * as babel from '@babel/core'; //@babel/core核心功能：将源代码转成目标代码。
import jsx from '@vue/babel-plugin-jsx'; //Vue给babel写的插件支持tsx v-model等
export default function (): Plugin {
    return {
        name: "vite-plugin-tsx",
        config (config) {
           return {
              esbuild:{
                 include:/\.ts$/
              }
           }
        },
        async transform(code, id) {
            if (/.tsx$/.test(id)) {
                //@ts-ignore
                const ts = await import('@babel/plugin-transform-typescript').then(r=>r.default)
                const res = babel.transformSync(code,{
                    plugins:[jsx,[ts, { isTSX: true, allowExtensions: true }]], //添加babel插件
                    ast:true, // ast: 抽象语法树，源代码语法结构的一种抽象表示。babel内部就是通过操纵ast做到语法转换。
                    babelrc:false, //.babelrc.json
                    configFile:false //默认搜索默认babel.config.json文件
                })
                return res?.code //code: 编译后的代码
            }
           
            return code
        }
    }
}
```

### 注册插件

> 在vite.config.ts中进行注册

```ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import vueJsxPlugin from "@vitejs/plugin-vue-jsx";
import tsx from "./plugin/index"

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(),vueJsxPlugin(),tsx()]
})

```

> 爆红时候进行创建tsconfig.config.json文件，由于小满没把这个代码贴出来，我就把代码放在下面了，直接粘贴即可
>
> 不报错的效果![image-20221204051141373](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221204051141373-1711983932539-47.png)

```ts
{
  "extends": "@vue/tsconfig/tsconfig.node.json",
  "include": ["vite.config.*","vitest.config.*","cypress.config.*","plugin"],
  "compilerOptions": {
    "composite": true,
    "types": ["node"]
  }
}
```

- 成功渲染出来内容

![image-20221204052626766](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221204052626766-1711983932539-46.png)

- 页面内容正常渲染

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221204052738605-1711983932539-49.png" alt="image-20221204052738605" style="zoom:50%;" />

# Vue3彩蛋部分

> ### 小彩蛋——自动引入插件
>
> 配置完成之后使用 ref reactive watch 等 无须 import 导入 可以直接使用 。但其实webstorm已经内嵌了这个功能，如果是使用VScode的话，那可以进行引入一下
>
> ```tsx
> npm i -D unplugin-auto-import//这个是一个在GitHub上的自动引入插件
> //地址：https://github.com/antfu/unplugin-auto-import
> ```
>
> 请自行根据地址中给出的自述markdown文档进入使用
>
> <img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221025080212131-1711983932539-48.png" alt="image-20221025080212131" style="zoom:67%;" />

### vite 配置

> 配置前请先记得使用npm包管理器进行下载，命令放在上面了。安装完后就能进行引入插件了
>
> 在`vite.config.ts`中引入
>
> 引入完，记得重新运行项目才会生效

```tsx
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import VueJsx from '@vitejs/plugin-vue-jsx'
import AutoImport from 'unplugin-auto-import/vite'//引入的是这个
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(),VueJsx(),AutoImport({//AutoImport进行注入
    imports:['vue'],//注入Vue
    dts:"src/auto-import.d.ts"//生成声明文件并指定地址存放，不然直接使用会找不到
  })]
})
```

# 第二十六章 — 组件(V-model & 源码解析)

> # v-model
>
> TIps 在 Vue3 中 v-model 是破坏性更新的
>
> v-model 在组件里面也是很重要的
>
> v-model 其实是一个语法糖 通过 props(单向数据流，只允许父级传输数据给子集) 和 emit 组合而成的
>
> #### 对比Vue2中的变化
>
> 1. 默认值的改变
>
> - prop：`value` -> `modelValue`；
> - 事件：`input` -> `update:modelValue`；
> - `v-bind` 的 `.sync` 修饰符和组件的 `model` 选项已移除
> - 新增 支持多个 v-model
> - 新增 支持自定义 修饰符 Modifiers

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221025081006020-1711983932539-52.png" alt="image-20221025081006020" style="zoom:80%;" />

## 案例 

### 子组件

```ts
<template>
     <div v-if='propData.modelValue ' class="dialog">
         <div class="dialog-header">
             <div>标题</div><div @click="close">x</div>
         </div>
         <div class="dialog-content">
            内容
         </div>
         
     </div>
</template>
 
<script setup lang='ts'>
 
type Props = {
   modelValue:boolean
}
 
const propData = defineProps<Props>()
 
const emit = defineEmits(['update:modelValue'])//defineEmits(['update:'])固定写法，update后面写的是我们要暴露出去的名字
 
const close = () => {
     emit('update:modelValue',false)//成功派送出去
}
 
</script>
 
<style lang='less'>
.dialog{
    width: 300px;
    height: 300px;
    border: 1px solid #ccc;
    position: fixed;
    left:50%;
    top:50%;
    transform: translate(-50%,-50%);
    &-header{
        border-bottom: 1px solid #ccc;
        display: flex;
        justify-content: space-between;
        padding: 10px;
    }
    &-content{
        padding: 10px;
    }
}
</style>
```

### 父组件

```js
<template>
  <button @click="show = !show">开关{{show}}</button>
  //双向绑定子组件
  <Dialog v-model="show"></Dialog>
</template>
 
<script setup lang='ts'>
import Dialog from "./components/Dialog/index.vue";
import {ref} from 'vue'
const show = ref(false) //开关使用布尔值切换
</script>
 
<style>
</style>
```

## 绑定多个案例

### 子组件

```ts
<template>
    //最外层div绑定modelValue，决定了显示与隐藏
     <div v-if='modelValue ' class="dialog">
         <div class="dialog-header">
             <div>标题---{{title}}</div><div @click="close">x</div>
         </div>
         <div class="dialog-content">
            内容
         </div>
         
     </div>
</template>
 
<script setup lang='ts'>
 
type Props = {
   modelValue:boolean,//这里Vue2与Vue3发生了改变，2=>value 3=>modelValue
   title:string
}
 
const propData = defineProps<Props>()
 
const emit = defineEmits(['update:modelValue','update:title'])//一样的update固定写法
 
const close = () => {
     emit('update:modelValue',false)
     emit('update:title','我要改变')
}
 
</script>
 
<style lang='less'>
.dialog{
    width: 300px;
    height: 300px;
    border: 1px solid #ccc;
    position: fixed;
    left:50%;
    top:50%;
    transform: translate(-50%,-50%);
    &-header{
        border-bottom: 1px solid #ccc;
        display: flex;
        justify-content: space-between;
        padding: 10px;
    }
    &-content{
        padding: 10px;
    }
}
</style>
```

### 父组件

```ts
<template>
    //决定子组件的显示与隐藏
  <button @click="show = !show">开关{{show}} ----- {{title}}</button>
  <Dialog v-model:title='title' v-model="show"></Dialog>
</template>
 
<script setup lang='ts'>
import Dialog from "./components/Dialog/index.vue";
import {ref} from 'vue'
const show = ref(false)
const title = ref('我是标题')
</script>
 
<style>
</style>
```

## 自定义修饰符

> 添加到组件 `v-model` 的修饰符将通过 `modelModifiers` prop 提供给组件。在下面的示例中，我们创建了一个组件，其中包含默认为空对象的 `modelModifiers` prop
>
> 通俗的说就是：我们在父组件可以随便自定义名字，主要设置还是在子组件的派送方面，在子组件中派送的时候写法：
>
> 1. 自定义名字+Modifiers
> 2. 默认值：modelModifiers
> 3. 然后放入emit中进行派送

```ts
<script setup lang='ts'>
 
type Props = {
    modelValue: boolean,
    title?: string,
    modelModifiers?: {//XXXModifiers是固定的，这是自定义的写法。XXX是你之前自定义的名字。如果你想要默认值的话，XXX写成model
    //?为可选操作
        default: () => {}
    }
    titleModifiers?: {
        default: () => {}
    }
 
}
 
const propData = defineProps<Props>()
 
const emit = defineEmits(['update:modelValue', 'update:title'])
 
const close = () => {
    console.log(propData.modelModifiers);//这里是自定义的内容，如果要派送出去就写到emit中
 
    emit('update:modelValue', false)
    emit('update:title', '我要改变')
}
```

## v-model源码解析

> 在视频的9分54秒
>
> 源文件在packages->runtime-dom->src->directives->vModelText

```ts
export const vModelText: ModelDirective<
  HTMLInputElement | HTMLTextAreaElement
> = {
    //el dom节点对象 bing对象
  created(el, { modifiers: { lazy, trim, number } }, vnode) {
      //获取props当中的modelValue 属性对应的函数
    el._assign = getModelAssigner(vnode)
      //number修饰符
    const castToNumber =
      number || (vnode.props && vnode.props.type === 'number')
    //通过addEventListener 如果有lazy属性(内置修饰符)，触发change 应为change 只有在改变值且鼠标离开焦点才会触发 input会一直走(触发)
    addEventListener(el, lazy ? 'change' : 'input', e => {//对el做的绑定事件
      if ((e.target as any).composing) return
      let domValue: string | number = el.value
      //如果有trim 去除左右空格
      if (trim) {
        domValue = domValue.trim()//手动调用trim
          //如果是数字就调用下面的toNumber转化成数字
      }
      if (castToNumber) {
        domValue = toNumber(domValue)
      }
      el._assign(domValue)
    })
    if (trim) {
      addEventListener(el, 'change', () => {
        el.value = el.value.trim()
      })
    }
    if (!lazy) {
        //处理中文输入法，当用户选中输入法的值 手动触发input。当我们在输入的时候不会触发，只有当我们按下回车确定的时候触发
      addEventListener(el, 'compositionstart', onCompositionStart)//监听compositionstart
      addEventListener(el, 'compositionend', onCompositionEnd)
      // Safari < 10.2 & UIWebView doesn't fire compositionend when
      // switching focus before confirming composition choice
      // this also fixes the issue where some browsers e.g. iOS Chrome
      // fires "change" instead of "input" on autocomplete.
      addEventListener(el, 'change', onCompositionEnd)
    }
  },
  // set value on mounted so it's after min/max for type="range"
  mounted(el, { value }) {
    el.value = value == null ? '' : value//绑定的值，赋值给value 目前只是单向流动
  },
  beforeUpdate(el, { value, modifiers: { lazy, trim, number } }, vnode) {
    el._assign = getModelAssigner(vnode)
    // avoid clearing unresolved text. #2302
    if ((el as any).composing) return
    if (document.activeElement === el && el.type !== 'range') {
      if (lazy) {//是lazy直接return回去
        return
      }
      if (trim && el.value.trim() === value) {//如果做过trim也是直接return
        return
      }
      if ((number || el.type === 'number') && toNumber(el.value) === value) {//number做过toNumber转换了也直接return
        return
      }
    }
    const newValue = value == null ? '' : value
    if (el.value !== newValue) {//新值旧值不一样才做一个更新
      el.value = newValue
    }
  }
}
```

## componentEmits源码

> 在视频的12分08秒
>
> 源文件位置：package->runtime-core->src->componentEmits

```ts
export function emit(
  instance: ComponentInternalInstance,
  event: string,//下面读取的就是这个
  ...rawArgs: any[]
) {
  if (instance.isUnmounted) return
  const props = instance.vnode.props || EMPTY_OBJ//先读取了当前实例的props一个对象

  if (__DEV__) {
    const {
      emitsOptions,
      propsOptions: [propsOptions]
    } = instance
    if (emitsOptions) {
      if (
        !(event in emitsOptions) &&
        !(
          __COMPAT__ &&
          (event.startsWith('hook:') ||
            event.startsWith(compatModelEventPrefix))
        )
      ) {
        if (!propsOptions || !(toHandlerKey(event) in propsOptions)) {
          warn(
            `Component emitted event "${event}" but it is neither declared in ` +
              `the emits option nor as an "${toHandlerKey(event)}" prop.`
          )
        }
      } else {
        const validator = emitsOptions[event]
        if (isFunction(validator)) {
          const isValid = validator(...rawArgs)
          if (!isValid) {
            warn(
              `Invalid event arguments: event validation failed for event "${event}".`
            )
          }
        }
      }
    }
  }
    

  let args = rawArgs
  const isModelListener = event.startsWith('update:')//读取完获取这个event(上面定义emit里的event)，也就是我们传过来的事件，也就是emit(update:modelValue)，会进行一个判断，看是不是update:开头的

  // for v-model update:xxx events, apply modifiers on args
  const modelArg = isModelListener && event.slice(7)//如果有update:就截掉这部分，也就是后7位
  if (modelArg && modelArg in props) {
    const modifiersKey = `${
      modelArg === 'modelValue' ? 'model' : modelArg
    }Modifiers`
    const { number, trim } = props[modifiersKey] || EMPTY_OBJ//获取number跟trim两个修饰符，回传的时候也进行处理过滤。就是emit派发的时候后面那个要派发出去的数据
    if (trim) {
      args = rawArgs.map(a => a.trim())
    }
    if (number) {
      args = rawArgs.map(toNumber)
    }
  }
    
    
  //handlerName = on + 事件名称
  let handlerName
  let handler =
    props[(handlerName = toHandlerKey(event))] ||
    // also try camelCase event handler (#2249)
    props[(handlerName = toHandlerKey(camelize(event)))]
  // for v-model update:xxx events, also trigger kebab-case equivalent
  // for props passed via kebab-case
  if (!handler && isModelListener) {
    handler = props[(handlerName = toHandlerKey(hyphenate(event)))]
  }
	//执行对应的回调函数
  if (handler) {
    callWithAsyncErrorHandling(
      handler,
      instance,
      ErrorCodes.COMPONENT_EVENT_HANDLER,
      args
    )
  }
```

# 第二十七章 — 自定义指令directive

> ### directive - 自定义指令（属于破坏性更新）
>
> Vue 中有 `v-if`，`v-for`，`v-bind`，`v-show`,`v-model` 等等一系列方便快捷的指令 今天一起来了解一下 vue 里提供的自定义指令

## 1.Vue3 指令的钩子函数

| **created**       | 元素初始化的时候                    |
| ----------------- | ----------------------------------- |
| **beforeMount**   | **指令绑定到元素后调用 只调用一次** |
| **mounted**       | **元素插入父级 dom 调用**           |
| **beforeUpdate**  | **元素被更新之前调用**              |
| **update**        | **这个周期方法被移除 改用 updated** |
| **beforeUnmount** | **在元素被移除前调用**              |
| **unmounted**     | **指令被移除后调用 只调用一次**     |

> Vue2 指令 bind inserted update componentUpdated unbind

## 2. 在 setup 内定义局部指令

> 但这里有一个需要注意的限制：必须以 `vNameOfDirective` 的形式来命名本地自定义指令，以使得它们可以直接在模板中使用。
>
> 注意啊，这个`vNameOfDirective`的意思是：v + 你想自定义的名字 + Directive

```typescript
<template>
  <button @click="show = !show">开关{{show}} ----- {{title}}</button>
//通过v-move-directive来绑定，那是因为我们在下面声明的就是这个。在下面所有类型的钩子种都可以收到这里的值
  <Dialog  v-move-directive="{background:'green',flag:show}"></Dialog>
//自定义参数aaa：v-move-directive:aaa="{background:'green',flag:show}"
//自定义修饰符xiaoman：v-move-directive:aaa.xiaoman="{background:'green',flag:show}"
</template>
```

```typescript
 //const声明指令，:Directive锁定类型
const vMoveDirective: Directive = {
  created: () => {
    console.log("初始化====>");
  },
  beforeMount(...args: Array<any>) {//通过使用ES6的解构方式可以在我们不知道传进来什么参数的时候将其解构出来，在控制台打印来观察。像aaa，background:'green'这些什么的都会被打印出来，我们在控制台看一下就知道了
    // 在元素上做些操作，指令绑定此元素后调用一次
    console.log("初始化一次=======>");
  },
    //常用
  mounted(el: any, dir: DirectiveBinding<Value>) {//第一个参数为el，第二个参数为directiveBinding类型
      //元素插入父级 dom 调用
    el.style.background = dir.value.background;//将拿到的背景值赋到el的背景值上
    console.log("初始化========>");
    console.log(dir.value);//能够拿到value的值，因为在源码中，是有设定value去接收值的
  },
  beforeUpdate() {
      //虚拟DOM调用之前调用
    console.log("更新之前");
  },
    //常用2
  updated() {
      //虚拟DOM调用之后调用
    console.log("更新结束");
  },
  beforeUnmount(...args: Array<any>) {
    console.log(args);
    console.log("======>卸载之前");
  },
    //常用3
  unmounted(...args: Array<any>) {
    console.log(args);
    console.log("======>卸载完成");
  },
};
```

```typescript
//DirectiveBinding源码
export declare interface DirectiveBinding<V = any> {
    instance: ComponentPublicInstance | null;
    value:V;
    oldValue:V|null;
    arg?:string;
    modifiers:DirectiveModifiers;
    dir:ObjectDirective<any,V>;
}
```

## 3. 生命周期钩子参数详解

> 第一个 el 当前绑定的 DOM 元素
>
> 第二个 binding
>
> | instance    | 使用指令的组件实例                                           |
> | ----------- | ------------------------------------------------------------ |
> | `value`     | 传递给指令的值。例如，在 `v-my-directive="1 + 1"` 中，该值为 `2` |
> | `oldValue`  | 先前的值，仅在 `beforeUpdate` 和 `updated` 中可用。无论值是否有更改都可用 |
> | `arg`       | 传递给指令的参数 (如果有的话)。例如在 `v-my-directive:foo` 中，arg 为 `"foo"` |
> | `modifiers` | 包含修饰符 (如果有的话) 的对象。例如在 `v-my-directive.foo.bar` 中，修饰符对象为 `{foo: true，bar: true}` |
> | `dir`       | 一个对象，在注册指令时作为参数传递。例如，在以下指令中       |
>
> 第三个 当前元素的虚拟 DOM 也就是 Vnode
>
> 第四个 prevNode 上一个虚拟节点，仅在 `beforeUpdate` 和 `updated` 钩子中可用 

![image-20221027001014931](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221027001014931-1711983932539-51.png)

## 4. 函数简写(指令鉴权-代号：斯塔缇可电刃)

> 你可能想在 `mounted` 和 `updated` 时触发相同行为，而不关心其他的钩子函数。那么你可以通过将这个函数模式实现

```typescript
<template>
   <div>
      <input v-model="value" type="text" />
          //自己建个A组件
      <A v-move="{ background: value }"></A>
   </div>
</template>
   
<script setup lang='ts'>
import A from './components/A.vue'
import { ref, Directive, DirectiveBinding } from 'vue'
let value = ref<string>('')
type Dir = {
   background: string
}
const vMove: Directive = (el:HTMLElement, binding: DirectiveBinding<Dir>) => {//还是接收两个值el与binding，binding是我们要传入的数据
   el.style.background = binding.value.background
}
</script>
 
 
 
<style>
</style>
```

### 鉴权事件

> 这就是通过后端给出的权限信息来对按钮动态的鉴定当前用户是否拥有权限
>
> - 无权限
>   - 将其绑定的元素CSS设置为disply:none，进行隐藏
> - 有权限
>   - 什么都不做，正常显示

```vue
<template>
  <div class="btns">
    <button v-has-show="'shop:create'">创建</button>

    <button v-has-show="'shop:edit'">编辑</button>

    <button v-has-show="'shop:delete'">删除</button>
  </div>
</template>

<script setup lang='ts'>
// import {ref,reactive} from "vue"
import type {Directive} from "vue"
//permission
localStorage.setItem('userid','xiaoyu')

//mock后台返回的数据
const permission = [
  '用户ID:shop:edit',//shop:信息；edit:权限
  'xiaoyu:shop:create',//很显然我们xiaoyu的权限只有这个创建，所以屏幕中应该只有这个按钮
  'xiaoman:shop:delete'//这些都是字符串，所以我们在底下vHasShow中的第二个参数推断为字符串
]

const userid = localStorage.getItem('userid') as String
const vHasShow:Directive<HTMLElement,String> = (el,binding)=>{//el是我们绑定的元素，bingding包含详细信息。将Directive泛型推断成一个元素，方便写代码的时候有提示
  // console.log(el,binding);
  if(!permission.includes(userid+":"+binding.value)){
    el.style.display = 'none'
  }
}

</script>

<style scoped lang='less'>
.btns{
  margin: 10px;
}
</style>
```

### 案例自定义拖拽指令 

> 步骤分为三步：
>
> 1. 鼠标摁下滑动
> 2. 鼠标抬起，事件清除

```typescript
<template>
  <div v-move class="box">
    <div class="header"></div>
    <div>
      事件一：小满在10w粉丝的时候cos女装
	<br>
	  事件二：小满的QQ群有一只被群友称为洛佬的天才少年，有问题多问他，准没错
	<br>
      事件三：小满的QQ群号为：855139333
	<br>
      事件四：小满群里有个还在学HTML5的菜鸡小余，没事请多帮帮他
    </div>
  </div>
</template>
 
<script setup lang='ts'>
import { Directive } from "vue";
const vMove: Directive<any,void> = {//定义类型为Directive，如果没有返回值我们就定义为void
  mounted(el: HTMLElement) {
    let moveEl = el.firstElementChild as HTMLElement;
      //TS写法：let moveEl:HTMLDivElement = el.firstElementChild as HTMLElement;
    const mouseDown = (e: MouseEvent) => {//mouseDown定义在这里
      //鼠标点击物体那一刻相对于物体左侧边框的距离=点击时的位置相对于浏览器最左边的距离-物体左边框相对于浏览器最左边的距离
      console.log(e.clientX, e.clientY, "-----起始", el.offsetLeft);//获取坐标值
      let X = e.clientX - el.offsetLeft;//减去初始值
      let Y = e.clientY - el.offsetTop;
      const move = (e: MouseEvent) => {
        el.style.left = e.clientX - X + "px";
        el.style.top = e.clientY - Y + "px";
        console.log(e.clientX, e.clientY, "---改变");
      };
      document.addEventListener("mousemove", move);//鼠标按下
      document.addEventListener("mouseup", () => {//鼠标抬起
        document.removeEventListener("mousemove", move);//鼠标抬起就清除掉mousemove事件
      });
    };
      //当我们对着header区域按下鼠标准备移动，绑定事件进行调用
    moveEl.addEventListener("mousedown", mouseDown);//mouseDown事件定义在上方，mouseDown是参数一的一个事件类型，下方有事件类型大全
  },
};
</script>
 
<style lang='less'>
.box {
  position: fixed;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  width: 600px;
  height: 200px;
  border: 1px solid #ccc;
  .header {
    height: 20px;
    background: black;
    cursor: move;
  }
}
</style>
```

### addEventListener知识点补充

**参数说明：有三个参数**

参数一、事件名称，字符串，必填。

- 事件名称不用带 "on" 前缀，点击事件直接写："click"，键盘放开事件写："keyup"

参数二、执行函数，必填。

- 填写需要执行的函数，如：function (){代码...} 
- 当目标对象事件触发时，会传入一个事件参数，参数名称可自定义，如填写 event，不需要也可不填写。 事件对象的类型取决于特定的事件。例如， “click” 事件属于 MouseEvent (鼠标事件) 对象。
  function(event){console.log(event)}

参数三、触发类型，布尔型，可空 

- true - 事件在捕获阶段执行
- false - 事件在冒泡阶段执行，默认是 false

> 这个事件绑定可以有多种事件名称，事件名称大全放在下面

#### 参数一事件类型大全

##### **鼠标事件**

| click       | 当用户点击某个对象时调用的事件句柄。   |
| ----------- | -------------------------------------- |
| contextmenu | 在用户点击鼠标右键打开上下文菜单时触发 |
| dblclick    | 当用户双击某个对象时调用的事件句柄     |
| mousedown   | 鼠标按钮被按下                         |
| mouseenter  | 当鼠标指针移动到元素上时触发           |
| mouseleave  | 当鼠标指针移出元素时触发               |
| mousemove   | 鼠标被移动。                           |
| mouseover   | 鼠标移到某元素之上。                   |
| mouseout    | 鼠标从某元素移开                       |
| mouseup     | 鼠标按键被松开                         |

##### **键盘事件**

| 属性     | 描述 DOM                 |
| -------- | ------------------------ |
| keydown  | 某个键盘按键被按下       |
| keypress | 某个键盘按键被按下并松开 |
| keyup    | 某个键盘按键被松开       |



##### **框架 / 对象（Frame/Object）事件**

abort 图像的加载被中断。 ( )
beforeunload 该事件在即将离开页面（刷新或关闭）时触发
error 在加载文档或图像时发生错误。 (, 和)
hashchange 该事件在当前 URL 的锚部分发生修改时触发。
load 一张页面或一幅图像完成加载。
pageshow 该事件在用户访问页面时触发
pagehide 该事件在用户离开当前网页跳转到另外一个页面时触发
resize 窗口或框架被重新调整大小。
scroll 当文档被滚动时发生的事件。
unload 用户退出页面

##### **表单事件**

blur 元素失去焦点时触发
change 该事件在表单元素的内容改变时触发 (, , , 和)
focus 元素获取焦点时触发
focusin 元素即将获取焦点是触发
focusout 元素即将失去焦点是触发
input 元素获取用户输入是触发
reset 表单重置时触发
search 用户向搜索域输入文本时触发 (

##### **剪贴板事件**

copy 该事件在用户拷贝元素内容时触发
cut 该事件在用户剪切元素内容时触发
paste 该事件在用户粘贴元素内容时触发

##### **打印事件**

afterprint 该事件在页面已经开始打印，或者打印窗口已经关闭时触发
beforeprint 该事件在页面即将开始打印时触发

##### **拖动事件**

drag 该事件在元素正在拖动时触发
dragend 该事件在用户完成元素的拖动时触发
dragenter 该事件在拖动的元素进入放置目标时触发
dragleave 该事件在拖动元素离开放置目标时触发
dragover 该事件在拖动元素在放置目标上时触发
dragstart 该事件在用户开始拖动元素时触发
drop 该事件在拖动元素放置在目标区域时触发

##### **多媒体（Media）事件**

abort 事件在视频 / 音频（audio/video）终止加载时触发。
canplay 事件在用户可以开始播放视频 / 音频（audio/video）时触发。
canplaythrough 事件在视频 / 音频（audio/video）可以正常播放且无需停顿和缓冲时触发。
durationchange 事件在视频 / 音频（audio/video）的时长发生变化时触发。
emptied The event occurs when the current playlist is empty
ended 事件在视频 / 音频（audio/video）播放结束时触发。
error 事件在视频 / 音频（audio/video）数据加载期间发生错误时触发。
loadeddata 事件在浏览器加载视频 / 音频（audio/video）当前帧时触发触发。
loadedmetadata 事件在指定视频 / 音频（audio/video）的元数据加载后触发。
loadstart 事件在浏览器开始寻找指定视频 / 音频（audio/video）触发。
pause 事件在视频 / 音频（audio/video）暂停时触发。
play 事件在视频 / 音频（audio/video）开始播放时触发。
playing 事件在视频 / 音频（audio/video）暂停或者在缓冲后准备重新开始播放时触发。
progress 事件在浏览器下载指定的视频 / 音频（audio/video）时触发。
ratechange 事件在视频 / 音频（audio/video）的播放速度发送改变时触发。
seeked 事件在用户重新定位视频 / 音频（audio/video）的播放位置后触发。
seeking 事件在用户开始重新定位视频 / 音频（audio/video）时触发。
stalled 事件在浏览器获取媒体数据，但媒体数据不可用时触发。
suspend 事件在浏览器读取媒体数据中止时触发。
timeupdate 事件在当前的播放位置发送改变时触发。
volumechange 事件在音量发生改变时触发。
waiting 事件在视频由于要播放下一帧而需要缓冲时触发。

##### **动画事件**

animationend 该事件在 CSS 动画结束播放时触发
animationiteration 该事件在 CSS 动画重复播放时触发
animationstart 该事件在 CSS 动画开始播放时触发

##### **过渡事件**

transitionend 该事件在 CSS 完成过渡后触发。

##### **其他事件**

message 该事件通过或者从对象 (WebSocket, Web Worker, Event Source 或者子 frame 或父窗口) 接收到消息时触发
online 该事件在浏览器开始在线工作时触发。
offline 该事件在浏览器开始离线工作时触发。
popstate 该事件在窗口的浏览历史（history 对象）发生改变时触发。 event occurs when the window’s history changes
show 该事件当

##### **元素在上下文菜单显示时触发**

storage 该事件在 Web Storage (HTML 5 Web 存储) 更新时触发
toggle 该事件在用户打开或关闭 元素时触发
wheel 该事件在鼠标滚轮在元素上下滚动时触发

# 第二十八章 自定义Hooks

> Vue3 自定义 Hook
>
> 主要用来处理复用代码逻辑的一些封装
>
> 这个在 vue2 就已经有一个东西是 Mixins
>
> mixins 就是将这些多个相同的逻辑抽离出来，各个组件只需要引入 mixins，就能实现一次写代码，多组件受益的效果。
>
> 弊端就是 会涉及到覆盖的问题
>
> **组件的 data、methods、filters 会覆盖 mixins 里的同名 data、methods、filters。**

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221027010659745-1711983932539-53.png" alt="image-20221027010659745" style="zoom:67%;" />

> 问题1：mixins的生命周期调用比组件快(假设主文件跟mixins都定义了一个A，主文件会去覆盖掉mixins的A)
>
> 问题2： 变量来源不明确（隐式传入），不利于阅读，使代码变得难以维护。也就是在mixins定义了变量，在主文件中是能够直接使用的，但找起来的话就会非常的麻烦

## Vue3 的自定义的 hook

> - Vue3 的 hook 函数 相当于 vue2 的 mixin, 不同在与 hooks 是函数
> - Vue3 的 hook 函数 可以帮助我们提高代码的复用性，让我们能在不同的组件中都利用 hooks 函数

Vue3 hook 库 [Get Started | VueUse](https://vueuse.org/guide/)

```typescript
import {useAttrs} from 'vue';
let attr = useAttrs()//这个能够将父组件传过来的值通过这个attr将所有内容打印出来
console.log(attr)
```

### 例子

> 这个例子实现的是将一个本地图片转化成Base64格式的图片
>
> hooks文件夹(自己创建的)下的index.ts文件

```typescript
import { onMounted } from 'vue'
 
 
type Options = {
    el: string
}
 
type Return = {
    Baseurl: string | null
}
export default function (option: Options): Promise<Return> {//Promise的泛型什么类型自己定义
 
    return new Promise((resolve) => {//套一个Promise，或者可以使用callback回去，就可以不使用promise(异步)了
        onMounted(() => {
            const file: HTMLImageElement = document.querySelector(option.el) as HTMLImageElement;//断言
            file.onload = ():void => {//放在onload里面的东西会等其他所有内容加载完后再开始加载
                resolve({
                    Baseurl: toBase64(file)//在套了一层Promise后进行返回
                })
            }
 
        })
 
 		//转化为Base64图片的步骤
        const toBase64 = (el: HTMLImageElement): string => {
            const canvas: HTMLCanvasElement = document.createElement('canvas')//创建画布
            const ctx = canvas.getContext('2d') as CanvasRenderingContext2D//创建画布
            canvas.width = el.width//指定画步宽高
            canvas.height = el.height
            ctx.drawImage(el, 0, 0, canvas.width,canvas.height)//下方有drawImage参数详解
            console.log(el.width);
            
            return canvas.toDataURL('image/png')//返回导出图片。图片是啥自己替换
 
        }
    })
 
 
}


--------------------
//在其他导入此hook的使用方式
//先import引入
useBase64(el:"#img").then(res=>{
    console.log(res.baseUrl)
})
```

### querySelector知识点补充

> querySelector () 方法返回文档中匹配指定 CSS 选择器的一个元素。
>
> **注意：**querySelector () 方法仅仅返回匹配指定选择器的第一个元素。如果你需要返回所有的元素，请使用 querySelectorAll () 方法替代。
>
> **异常**
>
> 匹配指定 CSS 选择器的第一个元素。 如果没有找到，返回 null。如果指定了非法选择器则 抛出 **SYNTAX_ERR** 异常。
>
> **参数值介绍**
>
> 必须。指定一个或多个匹配元素的 CSS 选择器。 可以使用它们的 id、类、 类型、属性、属性值等来选取元素。
>
> 对于多个选择器，使用逗号隔开，返回一个匹配的元素。
>
> **部分实例介绍**
>
> 1、获取文档中第一个 <span> 元素：
>
> document.querySelector("span");
>
> 2、获取文档中 class="demo" 的第一个元素：
>
> document.querySelector(".demo");
>
> 3、获取文档中 class="demo" 的第一个 <span> 元素：
>
> document.querySelector("span.demo");
>
> 4、获取文档中有 "target" 属性的第一个 <a> 元素：
>
> document.querySelector("a[target]");
>
> 5、设置文档中第一个 h1 的背景颜色为红色：
>
> document.querySelector("h1").style.backgroundColor = "red";等等

## 画布知识点

> Canvas 组件提供两种方法让你指定或者获取画布对象：`Item handles`、`tags`。
>
> - `Item handles` 事实上是一个用于指定某个画布对象的整型数字（也成为画布对象的 ID）。当你在 Canvas 组件上创建一个画布对象的时候，tkinter 将自动为其指定一个在该 Canvas 组件中独一无二的整型值。然后各种 Canvas 的方法可以通过这个值操纵该画布对象。
> - `tags` 是附在画布对象上的标签，`tags` 由普通的非空白字符串组成。一个画布对象可以与多个 `tags` 相关联，一个 `tag` 也可用于描述多个画布对象。然而，与 Text 组件不同，没有指定画布对象的 `tags` 不能进行事件绑定和配置样式。也就是说，Canvas 组件的 `tags` 是仅为画布对象所拥有。
> - Canvas 组件预定义了两个 `tags`：`'all'`（表示 Canvas 组件中的所有画布对象）与 `"current"`（表示鼠标指针下存在的画布对象）。

### 参数简介

> 调用：`Canvas(master=None, cnf={}, **kw)`
>
> - `master`：父组件
> - `**kw`：组件选项，下方表格详细列举了各个选项的具体含义和用法

#### drawImage参数详解

| 参数    | 描述                                       |
| ------- | ------------------------------------------ |
| img     | 规定要使用的图像、画布或视频               |
| sx      | 可选。开始剪切的x坐标位置                  |
| sy      | 可选。开始剪切的y坐标位置                  |
| swidth  | 可选。被剪切图像的宽度。                   |
| sheight | 可选。被剪切图像的高度                     |
| x       | 在画布上放置图形的x坐标位置                |
| y       | 在画布上放置图形的y坐标位置                |
| width   | 可选。要使用的图形的宽度(伸展或缩写图像)   |
| height  | 可选。要使用的图形的高度。(伸展或缩小图像) |

----

| 选项                | 含义                                                         |
| :------------------ | :----------------------------------------------------------- |
| background          | 指定 Canvas 的背景颜色，可简写为 `bg`                        |
| borderwidth         | 指定 Canvas 的边框宽度，可简写为 `bd`                        |
| closeenough         | 指定一个（浮点类型的）距离，当鼠标与画布对象的距离小于该值时，鼠标被认为在画布对象上 |
| confine             | 指定 Canvas 组件是否允许滚动超出 `scrollregion` 选项指定的范围；默认值是 `True` |
| cursor              | 指定当鼠标在 Canvas 上飘过的时候的鼠标样式                   |
| height              | 指定 Canvas 的高度；单位是像素                               |
| width               | 指定 Canvas 的宽度；单位是像素                               |
| highlightbackground | 指定当 Canvas 没有获得焦点的时候高亮边框的颜色               |
| highlightcolor      | 指定当 Canvas 获得焦点的时候高亮边框的颜色                   |
| highlightthickness  | 指定高亮边框的宽度                                           |
| relief              | 指定 Canvas 的边框样式；默认值是 'flat'，其他可以选择的值有'sunken', 'raised', 'groove', 'ridge' |
| scrollregion        | 指定 Canvas 可以被滚动的的矩形范围为 ![(x_0,y_0, x_1, y_1)](.\xmzs-images\math.svg+xml) |
| selectbackground    | 指定当画布对象被选中时的背景色                               |
| selectborderwidth   | 指定当画布对象被选中时的边框宽度（选中边框）                 |
| selectforeground    | 指定当画布对象被选中时的前景色                               |
| state               | 设置 Canvas 的状态：'normal'（默认）, 'disabled'；注意：该值不会影响画布对象的状态 |
| takefocus           | 指定使用 Tab 键可以将焦点移动到输入框中；默认是开启的，可以将该选项设置为 `False` 避免焦点在此输入框中 |
| xscrollcommand      | 与 scrollbar（水平方向滚动条）组件相关联，具体使用参考 Scrollbar 组件 |
| yscrollcommand      | 与 scrollbar（垂直方向滚动条）组件相关联， 使用方法可以参考 Scrollbar 组件 |
| xscrollincrement    | 指定 Canvas 水平滚动的 “步长”（默认值是 `0`，表示可以水平滚动到任意位置）。例如 `'3c'` 表示 3 厘米，可以选择的单位有 `'i'`（英寸），`'m'`（毫米）和 `'p'`（DPI，大约是 `'1i'` = `'72p'`） |
| yscrollincrement    | 指定 Canvas 垂直滚动的 “步长”（默认值是 `0`，表示可以垂直滚动到任意位置）。 |

### 方法简介

下方表格列举了各个 options 选项的具体含义：

| 选项      | 含义                                                         |
| :-------- | :----------------------------------------------------------- |
| colormode | 该选项的值可以是：'color'（颜色输出），'gray'（灰阶输出）和 'mono'（黑白输出） |
| file      | 1. 该选项指定一个文件，将 PostScript 写入该文件中 2. 如果忽略该选项，PostScript 将以字符串的形式返回 height 1. 指定要打印的 Canvas 组件的高度 2. 默认值是 Canvas 组件的整体高度 |
| rotate    | 1. 如果该选项的值为 False，该页面将以纵向呈现 2. 如果该选项的值为 True，该页面将以横向呈现 |
| x         | 开始打印的最左边位置，以画布坐标系表示                       |
| y         | 开始打印的最顶端位置，以画布坐标系表示                       |
| width     | 1. 指定要打印的 Canvas 组件的宽度 2. 默认值是 Canvas 组件的整体宽度 |

**具体详细信息**=> [画布的详细信息大全](https://www.jianshu.com/p/9ada3a496907/)

# 第二十九章 — Vue3定义全局函数和变量

> ### globalProperties
>
> 由于 Vue3 没有 Prototype 属性 使用 `app.config.globalProperties` 代替 然后去定义变量和函数

**Vue2写法**

```typescript
// 之前 (Vue 2.x)
Vue.prototype.$http = () => {}
```

**Vue3写法**

```typescript
//写在main.ts这个入口文件中
// 之后 (Vue 3.x)
const app = createApp({})
app.config.globalProperties.$http = () => {}
//---------------------------->小满重录写法
export const app = createApp({})
app.config.globalProperties.$env = 'dev'//通过这个方式去定义全局函数和变量，这个$env是能直接在各个地方去使用的
```

```ts
//在App.vue组件中使用，此时插值语法中的$env会报错，但是页面上已经是可以显示出来了
<script setup lang="ts">

</script>

<template>
  <div>{{$env}}</div>  
</template>

<style scoped>

</style>
```

> 知识点回顾，重复的记忆会让知识的流失速度越来越慢

```js
|-node_modules       -- 所有的项目依赖包都放在这个目录下
|-public             -- 公共文件夹
---|favicon.ico      -- 网站的显示图标
---|index.html       -- 入口的html文件
|-src                -- 源文件目录，编写的代码基本都在这个目录下
---|assets           -- 放置静态文件的目录，比如logo.pn就放在这里
---|components       -- Vue的组件文件，自定义的组件都会放到这
---|App.vue          -- 根组件，这个在Vue2中也有
---|main.ts          -- 入口文件，因为采用了TypeScript所以是ts结尾
---|shims-vue.d.ts   -- 类文件(也叫定义文件)，因为.vue结尾的文件在ts中不认可，所以要有定义文件
|-.browserslistrc    -- 在不同前端工具之间公用目标浏览器和node版本的配置文件，作用是设置兼容性
|-.eslintrc.js       -- Eslint的配置文件，不用作过多介绍
|-.gitignore         -- 用来配置那些文件不归git管理
|-package.json       -- 命令配置和包管理文件
|-README.md          -- 项目的说明文件，使用markdown语法进行编写
|-tsconfig.json      -- 关于TypoScript的配置文件
|-yarn.lock          -- 使用yarn后自动生成的文件，由Yarn管理，安装yarn包时的重要信息存储到yarn.lock文件中
```

## 过滤器(已移除)

> 在Vue3中过滤器已经被移除了，Filters(过滤器)被全局函数替代，所以我们可以自己定义一个，重录写法中已经加上这个案例

### 全局函数案例

#### 挂载

```typescript
//此写法是写在main.ts中的，进行挂载全局。因为已经全局挂载了，所以可以直接在模板中进行使用了
app.config.globalProperties.$filters = {
  format<T extends any>(str: T): string {
    return `$${str}`//底下输出效果显示的不是这个，你就当这行已经被我注释掉了
    //视频写法
    return `群主说：${str}`
  }
}

---------------//随便写点其他的
app.config.globalProperties.$env = 'dev被洛佬拿走了'    
//-----------------------------------重录写法补充，过滤器
//main.ts入口文件中写入
//在vue3中，过滤器已经被去除掉了，我们可以自己再定义一个过滤器filter
app.config.globalProperties.$filters = {
    format<T>(str:T){
        return `${str}的飞机杯子被发现了`
    }
}
//app.vue组件中使用，随便任意一个组件都可以使用
<div>{{$filters.format('小满')}}</div>
//输出效果：小满的飞机杯子被发现了
```

> 重录补充：在js中想要去使用的话，可以使用 -- 读取当前实例的一个方法`getCurrentInstance`

```ts
//在js部分中使用
<script setup lang="ts">
import { getCurrentInstance } from 'vue';
//调用该方法，读取到当前的实例
const app = getCurrentInstance()
console.log(app?.proxy.$filters.format('ts'));
</script>
```

![image-20221202202833703](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221202202833703-1711983932539-54.png)

#### 使用全局函数

```typescript
<template>
	<div>
    	{{$filters.format('群里禁止搞小满色')}}//会输出 => 群主说：群里禁止搞小满色
        //此时会报错说找不到名称$filters，虽然我们使用的全局函数其实已经生效了。问题在于我们缺少了声明文件，下方的代码块中有声明文件怎么写
    </div>
	<div>
        {{$env}}//输出：dev被洛佬拿走了。如果你不希望这个也爆红的话，那就在声明文件中加上这个的声明
    </div>
</template>
```

> 声明文件 declare 
>
> 使TypeScript可以正确类型推导
>
> 当使用`第三方库`时，我们需要引用它的声明文件，才能获得对应的代码补全、接口提示等功能。

```typescript
//此代码跟全局挂载一样写在main.ts文件中
type Filter = {
    format: <T extends any>(str: T) => T
  }
// 声明要扩充@vue/runtime-core包的声明.
// 这里扩充"ComponentCustomProperties"接口, 因为他是vue3中实例的属性的类型.
  declare module '@vue/runtime-core' {//declare是专门用来声明文件的
    export interface ComponentCustomProperties {//ComponentCustomProperties名字是固定的，直接复制起来直接用，然后export将接口暴露出去
        $filters: Filter,//Filter已经在上面定义好了接收的类型
        $env:string//定义为string类型
    }
  }
//如果定义完后爆红，就重启编辑器
//-------------------------------------重录后的声明文件，在main.ts入口文件加上就行
type Filter = {
    format<T>(str: T): string
}
 
// 声明要扩充@vue/runtime-core包的声明.
// 这里扩充"ComponentCustomProperties"接口, 因为他是vue3中实例的属性的类型.
declare module 'vue' {
    export interface ComponentCustomProperties {
        $filters: Filter,
        $env:string
    }
}
//此时app组件中的proxy还是报错状态，因为值可能没用，所以加上可选操作符就行了
```

#### declare知识点补充

```typescript
declare var       声明全局变量
declare function  声明全局方法
declare class     声明全局类
declare enum      声明全局枚举类型
declare namespace 声明（含有子属性的）全局对象
interface 和 type 声明全局类型
//想了解详细具体相关的内容请移步TypeScript笔记(小满版本)部分
```

### setup 读取值

```typescript
import { getCurrentInstance, ComponentInternalInstance } from 'vue';
 
const { appContext } = <ComponentInternalInstance>getCurrentInstance()
 
console.log(appContext.config.globalProperties.$env);
```

## 源码部分

> 在视频4分50秒处
>
> 在源码文件中的packages > runtime-core > src > apiCreateApp.ts

```typescript
export function createAppContext(): AppContext {
  return {
    app: null as any,
    config: {//我们所用到的属性就来自这里
      isNativeTag: NO,
      performance: false,
      globalProperties: {},
      optionMergeStrategies: {},
      errorHandler: undefined,
      warnHandler: undefined,
      compilerOptions: {}
    },
    mixins: [],
    components: {},
    directives: {},
    provides: Object.create(null),
    optionsCache: new WeakMap(),
    propsCache: new WeakMap(),
    emitsCache: new WeakMap()
  }
}

export type CreateAppFunction<HostElement> = (
  rootComponent: Component,
  rootProps?: Data | null
) => App<HostElement>

let uid = 0

export function createAppAPI<HostElement>(//这里就是我们所用到的api
  render: RootRenderFunction<HostElement>,
  hydrate?: RootHydrateFunction
): CreateAppFunction<HostElement> {
  return function createApp(rootComponent, rootProps = null) {//会返回一个我们所用到的createApp函数，接受rootComponent这个根组件
    if (!isFunction(rootComponent)) {
      rootComponent = { ...rootComponent }
    }

    if (rootProps != null && !isObject(rootProps)) {
      __DEV__ && warn(`root props passed to app.mount() must be an object.`)
      rootProps = null
    }

    //初始化，初始化调用了createAppContext
    const context = createAppContext()
    const installedPlugins = new Set()

    let isMounted = false

    const app: App = (context.app = {//做完初始化就赋值给了这个对象，去填充这些属性、版本或者方法
      _uid: uid++,
      _component: rootComponent as ConcreteComponent,
      _props: rootProps,
      _container: null,
      _context: context,
      _instance: null,

      version,

      get config() {
        return context.config
      },

      set config(v) {
        if (__DEV__) {
          warn(
            `app.config cannot be replaced. Modify individual options instead.`
          )
        }
      },

      //注册插件
      use(plugin: Plugin, ...options: any[]) {
        if (installedPlugins.has(plugin)) {
          __DEV__ && warn(`Plugin has already been applied to target app.`)
        } else if (plugin && isFunction(plugin.install)) {
          installedPlugins.add(plugin)
          plugin.install(app, ...options)
        } else if (isFunction(plugin)) {
          installedPlugins.add(plugin)
          plugin(app, ...options)
        } else if (__DEV__) {
          warn(
            `A plugin must either be a function or an object with an "install" ` +
              `function.`
          )
        }
        return app
      },

      //混入
      mixin(mixin: ComponentOptions) {
        if (__FEATURE_OPTIONS_API__) {
          if (!context.mixins.includes(mixin)) {
            context.mixins.push(mixin)
          } else if (__DEV__) {
            warn(
              'Mixin has already been applied to target app' +
                (mixin.name ? `: ${mixin.name}` : '')
            )
          }
        } else if (__DEV__) {
          warn('Mixins are only available in builds supporting Options API')
        }
        return app
      },

      //全局组件
      component(name: string, component?: Component): any {
        if (__DEV__) {
          validateComponentName(name, context.config)
        }
        if (!component) {
          return context.components[name]
        }
        if (__DEV__ && context.components[name]) {
          warn(`Component "${name}" has already been registered in target app.`)
        }
        context.components[name] = component
        return app
      },

      //指令
      directive(name: string, directive?: Directive) {
        if (__DEV__) {
          validateDirectiveName(name)
        }

        if (!directive) {
          return context.directives[name] as any
        }
        if (__DEV__ && context.directives[name]) {
          warn(`Directive "${name}" has already been registered in target app.`)
        }
        context.directives[name] = directive
        return app
      },

      //挂载，就是我们app.mount('#app')，非常熟悉
      mount(
        rootContainer: HostElement,
        isHydrate?: boolean,
        isSVG?: boolean
      ): any {
        if (!isMounted) {
          // #5571
          if (__DEV__ && (rootContainer as any).__vue_app__) {
            warn(
              `There is already an app instance mounted on the host container.\n` +
                ` If you want to mount another app on the same host container,` +
                ` you need to unmount the previous app by calling \`app.unmount()\` first.`
            )
          }
          const vnode = createVNode(
            rootComponent as ConcreteComponent,
            rootProps
          )
          // store app context on the root VNode.
          // this will be set on the root instance on initial mount.
          vnode.appContext = context

          // HMR root reload
          if (__DEV__) {
            context.reload = () => {
              render(cloneVNode(vnode), rootContainer, isSVG)
            }
          }

          if (isHydrate && hydrate) {
            hydrate(vnode as VNode<Node, Element>, rootContainer as any)
          } else {
            render(vnode, rootContainer, isSVG)
          }
          isMounted = true
          app._container = rootContainer
          // for devtools and telemetry
          ;(rootContainer as any).__vue_app__ = app

          if (__DEV__ || __FEATURE_PROD_DEVTOOLS__) {
            app._instance = vnode.component
            devtoolsInitApp(app, version)
          }

          return getExposeProxy(vnode.component!) || vnode.component!.proxy
        } else if (__DEV__) {
          warn(
            `App has already been mounted.\n` +
              `If you want to remount the same app, move your app creation logic ` +
              `into a factory function and create fresh app instances for each ` +
              `mount - e.g. \`const createMyApp = () => createApp(App)\``
          )
        }
      },

      //对应卸载的一个方法
      unmount() {
        if (isMounted) {
          render(null, app._container)
          if (__DEV__ || __FEATURE_PROD_DEVTOOLS__) {
            app._instance = null
            devtoolsUnmountApp(app)
          }
          delete app._container.__vue_app__
        } else if (__DEV__) {
          warn(`Cannot unmount an app that is not mounted.`)
        }
      },

      //提供依赖注入provide
      provide(key, value) {
        if (__DEV__ && (key as string | symbol) in context.provides) {
          warn(
            `App already provides property with key "${String(key)}". ` +
              `It will be overwritten with the new value.`
          )
        }

        context.provides[key as string | symbol] = value

        return app//最后将app进行一个返回
      }
    })

    if (__COMPAT__) {
      installAppCompatProperties(app, context, render)
    }

    return app
  }
}
```

### 源码之Proxy

> app?.proxy中的proxy是怎么做到的呢？让我们进入源码进行感受
>
> 来自源码同级目录下的components去进行一个赋值

```typescript
// 0. create render proxy property access cache
  //通过instance，也就是当前组件的实例
  instance.accessCache = Object.create(null)
  // 1. create public instance / render proxy
  // also mark it raw so it's never observed
  //设置值proxy markRaw，注意(知识回顾) -> markRaw会去添加一个_skip属性去跳过reactive的代理，因为他自己会去做proxy代理，如下已经进行展示
  //防止这个重复的代理，
  instance.proxy = markRaw(new Proxy(instance.ctx, PublicInstanceProxyHandlers))//ctx就是定义那些$开头的一些api，其中_skip属性也已经加上了，将这些属性进行跳过，防止重复
  if (__DEV__) {
    exposePropsOnRenderContext(instance)
  }


//点进PublicInstanceProxyHandlers属性中
    const publicGetter = publicPropertiesMap[key]
    let cssModule, globalProperties
    // public $xxx properties
    if (publicGetter) {
      if (key === '$attrs') {
        track(instance, TrackOpTypes.GET, key)
        __DEV__ && markAttrsAccessed()
      }
      return publicGetter(instance)
    } else if (
      // css module (injected by vue-loader)
      (cssModule = type.__cssModules) &&
      (cssModule = cssModule[key])
    ) {
      return cssModule
    } else if (ctx !== EMPTY_OBJ && hasOwn(ctx, key)) {
      // user may set custom properties to `this` that start with `$`
      accessCache![key] = AccessTypes.CONTEXT
      return ctx[key]
    } else if (
      // global properties
      ((globalProperties = appContext.config.globalProperties),//这里进行了一次赋值操作
      hasOwn(globalProperties, key))//通过了hasOwn函数去判断了key(我们要读的属性)是否存在globalProperties中，存在则直接去做一个返回
    ) {
      if (__COMPAT__) {//处理特殊情况，调用Object.getOwnPropertyDescriptor
        //此方法返回指定对象上一个自有属性对应的属性操作符，自有属性是指直接赋予该对象的属性，不需要从原型链上进行查找的属性
        const desc = Object.getOwnPropertyDescriptor(globalProperties, key)!
        if (desc.get) {
          return desc.get.call(instance.proxy)
        } else {
          const val = globalProperties[key]
          return isFunction(val)
            ? Object.assign(val.bind(instance.proxy), val)
            : val
        }
      } else {//大部分情况下直接进行返回了
        return globalProperties[key]
      }
```

# 第三十章 — 编写Vue3插件

## 插件编写

> 插件是自包含的代码，通常向 Vue 添加全局级功能。你如果是一个对象需要有 install 方法 Vue 会帮你自动注入到 install 方法 你如果是 function 就直接当 install 方法去使用

## 使用插件

> 在使用 `createApp()` 初始化 Vue 应用程序后，你可以通过调用 `use()` 方法将插件添加到你的应用程序中。

## 实现一个 Loading

### Loading.Vue

```typescript
<template>
    <div v-if="isShow" class="loading">
        <div class="loading-content">Loading...默认不展示，调用后展示</div>
    </div>
</template>
    
<script setup lang='ts'>
import { ref } from 'vue';
const isShow = ref(false)//定位loading 的开关
 
const show = () => {
    isShow.value = true
}
const hide = () => {
    isShow.value = false
}
//对外暴露 当前组件的属性和方法
defineExpose({//将这isShow、show、hide抛出去之后，我们就可以在vnode.component?.exposed中拿到这三个值了，因为被抛出去的东西会放在vnode.component?.exposed内
    isShow,
    show,
    hide
})
</script>
 
 
    
<style scoped lang="less">
.loading {
    position: fixed;
    inset: 0;
    background: rgba(0, 0, 0, 0.8);
    display: flex;
    justify-content: center;
    align-items: center;
    &-content {
        font-size: 30px;
        color: #fff;
    }
}
</style>
```

### Loading.ts

> 支持导出两种形式：
>
> 1. 对象形式
>    1. 要求里面必须有一个install函数
>    2. install会回传一个app，这个app就是main.ts中的那个app，读取到这个就能够做一些全局的挂载操作
> 2. 函数形式

```typescript
import {  createVNode, render, VNode, App } from 'vue';
//render函数是用来手动挂载函数的
//VNode是用来将app转化为虚拟DOM的结构
import Loading from './index.vue'
 
export default {
    install(app: App) {//回传一个app，定义为App类型
        //createVNode vue提供的底层方法 可以给我们组件创建一个虚拟DOM 也就是Vnode
        const vnode: VNode = createVNode(Loading)//VNode是一个类型
        //render 把我们的Vnode 生成真实DOM 并且挂载到指定节点(因为初始的App里面的东西没办法直接用，通过控制台打印一下就可以看出来)
        render(vnode, document.body)//手动挂载vnode，第二个参数是一个挂载点(因为我们这里要挂载到全局的，我们就挂载到document.body上)，这个时候VNode里面的component里面就有值了
        // Vue 提供的全局配置 可以自定义
        app.config.globalProperties.$loading = {//这个$loading是可以自定义的，你叫$xiaoman也可以(记得不要重名，loading其实也不保险，如果报错就换个名字)
            show: () => vnode.component?.exposed?.show(),//通过查看源码，我们决定使用exposed去读取，而不是ComponentInternalInstance
            hide: () => vnode.component?.exposed?.hide()//加上?可选操作符，因为有时候是为null
        }
        app.config.globalProperties.$loading.show()//调用一下，看看效果
        //在其他地方调用其实也行了，比如App.vue，会生效，但是如果报错的话可能就是声明文件没有声明
        
 		console.log(app,"我们可以看看app里面究竟有什么",VNode)//对比app跟VNode之间的区别
        console.log(vnode.component?.exposed,"看看这里面")//其实就是通过defineExpose去抛出的一些东西都会放在这里面
    }
}
```

### 在main.ts中进行引入

```typescript
import Loading from "./components/Loading"//路径写自己的对应所在文件

const app = create(App)//挂载App，先挂载后使用(相当于想吃饭，首先你得先有饭。。。)

//插件注册通过use方法，只要是插件就必须通过app.use进行注册使用
app.use(Loading)
 
type Lod = {
    show: () => void,
    hide: () => void
}
//编写ts loading 声明文件放置报错 和 智能提示
declare module '@vue/runtime-core' {//这个路径也可以换成'vue'，就是有时候不会生效
    export interface ComponentCustomProperties {//ComponentCustomProperties一样的，固定语法，我们使用过了的
        $loading: Lod//设定了Lod类型
    }
}
 
 
 
app.mount('#app')
```

## 使用方法

```typescript
<template>
 
  <div></div>
 
</template>
 
<script setup lang='ts'>
import { ref,reactive,getCurrentInstance} from 'vue'
const  instance = getCurrentInstance()  
instance?.proxy?.$Loading.show()//声明文件写在上面了，没写声明文件能用会生效，但是会报错(为了不让编辑器都是红色，还是写上去吧)
setTimeout(()=>{//设定5秒后恢复
  instance?.proxy?.$Loading.hide()
},5000)
 
 
// console.log(instance)
</script>
<style>
*{
  padding: 0;
  margin: 0;
}
</style>
```

### 拆分知识点

> Vnode源码地址：node_modules -> @vue -> runtime-core -> dist -> runtime-core.d.ts

```typescript
//我们在使用Vnode.component.setupState的时候会爆红，显示ComponentInternalInstance中不存在属性setupState
//明明我们通过控制台是能看到有这个属性的，为什么会找不到。这就得进去VNode的源码部分去看了
```

**位置1**

```typescript
export declare interface VNode<HostNode = RendererNode, HostElement = RendererElement, ExtraProps = {
    [key: string]: any;
}> {
    /* Excluded from this release type: __v_isVNode */
    /* Excluded from this release type: __v_skip */
    type: VNodeTypes;
    props: (VNodeProps & ExtraProps) | null;
    key: string | number | symbol | null;
    ref: VNodeNormalizedRef | null;
    /**
     * SFC only. This is assigned on vnode creation using currentScopeId
     * which is set alongside currentRenderingInstance.
     */
    scopeId: string | null;
    /* Excluded from this release type: slotScopeIds */
    children: VNodeNormalizedChildren;
    component: ComponentInternalInstance | null;//在这里，往这里看----------------------------------
    dirs: DirectiveBinding[] | null;
    transition: TransitionHooks<HostElement> | null;
    el: HostNode | null;
    anchor: HostNode | null;
    target: HostElement | null;
    targetAnchor: HostNode | null;
    /* Excluded from this release type: staticCount */
    suspense: SuspenseBoundary | null;
    /* Excluded from this release type: ssContent */
    /* Excluded from this release type: ssFallback */
    shapeFlag: number;
    patchFlag: number;
    /* Excluded from this release type: dynamicProps */
    /* Excluded from this release type: dynamicChildren */
    appContext: AppContext | null;
    /* Excluded from this release type: memo */
    /* Excluded from this release type: isCompatRoot */
    /* Excluded from this release type: ce */
}
```

> 我们在里面确实没有找到ComponentInternalInstance，原因可能是如果setupState里面的方法没有暴露出来的话，Vue可能并不想要我们直接去用
>
> 所以我们通过另一套方案，从下方源码代码块的exposed入手，也可以去读取到

```typescript
export declare interface ComponentInternalInstance {
    uid: number;
    type: ConcreteComponent;
    parent: ComponentInternalInstance | null;
    root: ComponentInternalInstance;
    appContext: AppContext;
    /**
     * Vnode representing this component in its parent's vdom tree
     */
    vnode: VNode;
    /* Excluded from this release type: next */
    /**
     * Root vnode of this component's own vdom tree
     */
    subTree: VNode;
    /**
     * Render effect instance
     */
    effect: ReactiveEffect;
    /**
     * Bound effect runner to be passed to schedulers
     */
    update: SchedulerJob;
    /* Excluded from this release type: render */
    /* Excluded from this release type: ssrRender */
    /* Excluded from this release type: provides */
    /* Excluded from this release type: scope */
    /* Excluded from this release type: accessCache */
    /* Excluded from this release type: renderCache */
    /* Excluded from this release type: components */
    /* Excluded from this release type: directives */
    /* Excluded from this release type: filters */
    /* Excluded from this release type: propsOptions */
    /* Excluded from this release type: emitsOptions */
    /* Excluded from this release type: inheritAttrs */
    /**
     * is custom element?
     */
    isCE?: boolean;
    /**
     * custom element specific HMR method
     */
    ceReload?: (newStyles?: string[]) => void;
    proxy: ComponentPublicInstance | null;
    exposed: Record<string, any> | null;//这里，关键的部位在这里<-------------------------------------------
    exposeProxy: Record<string, any> | null;
    /* Excluded from this release type: withProxy */
    /* Excluded from this release type: ctx */
    data: Data;
    props: Data;
    attrs: Data;
    slots: InternalSlots;
    refs: Data;
    emit: EmitFn;
    /* Excluded from this release type: emitted */
    /* Excluded from this release type: propsDefaults */
    /* Excluded from this release type: setupState */
    /* Excluded from this release type: devtoolsRawSetupState */
    /* Excluded from this release type: setupContext */
    /* Excluded from this release type: suspense */
    /* Excluded from this release type: suspenseId */
    /* Excluded from this release type: asyncDep */
    /* Excluded from this release type: asyncResolved */
    isMounted: boolean;
    isUnmounted: boolean;
    isDeactivated: boolean;
    /* Excluded from this release type: bc */
    /* Excluded from this release type: c */
    /* Excluded from this release type: bm */
    /* Excluded from this release type: m */
    /* Excluded from this release type: bu */
    /* Excluded from this release type: u */
    /* Excluded from this release type: bum */
    /* Excluded from this release type: um */
    /* Excluded from this release type: rtc */
    /* Excluded from this release type: rtg */
    /* Excluded from this release type: a */
    /* Excluded from this release type: da */
    /* Excluded from this release type: ec */
    /* Excluded from this release type: sp */
    /**
     * For caching bound $forceUpdate on public proxy access
     */
    f?: () => void;
    /**
     * For caching bound $nextTick on public proxy access
     */
    n?: () => Promise<void>;

```

## Vue use源码部分(手动实现)

> 在视频的11分52秒部分
>
> 在src下创建一个myUse.ts文件

```typescript
import type { App } from 'vue'
import { app } from './main'//在main.ts导出完后，记得在这个文件中进行导入
 
interface Use {//泛型约束
    install: (app: App, ...options: any[]) => void//除了第一个app是App类型，后面是用户输入的参数(解构出来)
}
 
const installedList = new Set()//缓存策略，防止重复添加主键
 
export function MyUse<T extends Use>(plugin: T, ...options: any[]) {//MyUse接收泛型T并约束为Use
    if(installedList.has(plugin)){//检查插件是否已经注册过了，注册过就进行提示重复添加插件
      return console.warn('重复添加插件',plugin)
    }else{
        ////通过函数调用install，这个app是来自main.ts，记得在main.ts文件进行export导出一下
        plugin.install(app, ...options)//这里的...option是对参数...option进行一个回传
        installedList.add(plugin)//添加进缓存策略里(原本缓存策略是没有东西的，调用一次后就加上了，下次再调用就会被if判断给拦下来提示重复添加插件)
    }
}
```

> 提示：实现完后要在main.ts中进行导入，然后就可以直接MyUse(Loading)使用了，因为我们这是自己实现的，所以这个插件不需要挂载在app.use()下

## 源码讲解

> 视频的16分12秒
>
> 源码位置：packages -> runtime-core -> src -> apiCreateApp.ts

```typescript
        const installedPlugins = new Set()    

		use(plugin: Plugin, ...options: any[]) {//接收plugin跟options(这是用户自己的配置项)
        if (installedPlugins.has(plugin)) {//installedPlugins是一个缓存，我们刚刚手写Use源码的时候有解释过，防止重复添加
          __DEV__ && warn(`Plugin has already been applied to target app.`)
        } else if (plugin && isFunction(plugin.install)) {//判断plugin是否有值且plugin内是否有install函数，且这整体是不是一个函数
          installedPlugins.add(plugin)//是就将plugin添加进缓存里面
          plugin.install(app, ...options)//将app注册进去plugin，然后用户的自定义配置项也传回去
        } else if (isFunction(plugin)) {//判断plugin是不是函数。这就是我们说支持两种模式:对象跟函数的 对象内要有install函数
          installedPlugins.add(plugin)
          plugin(app, ...options)
        } else if (__DEV__) {
          warn(
            `A plugin must either be a function or an object with an "install" ` +
              `function.`
          )
        }
        return app//将app进行return，就可以进行一个链式调用
      }
```

> return app的链式调用意思：app.use().use().use()等等无限调用下去
>
> 能够不断调用下去是因为这个每次返回的都是自身的当前实例

## es6 里面 has () 的用法是什么

> installedPlugins.has(plugin)中的has

has 作为函数的 in 运算符，它返回一个布尔值，指示是否存在自有或继承的属性。

下面给出了函数 has () 的语法，其中，

`target` 是要在其中查找属性的目标对象。

`propertyKey` 是要检查的属性的名称。

```js
Reflect.has(target, propertyKey)
```

has () 方法用来拦截 HasProperty 操作，即判断对象是否具有某个属性时，这个方法会生效。典型的操作就是 in 运算符。

has () 方法可以接受两个参数，分别是目标对象、需查询的属性名。

使用 has () 方法隐藏某些属性，不被 in 运算符发现。

# 第三十一章 — Vue3组件库

> **vue** 作为一款深受广大群众以及尤大崇拜者的喜欢，特此列出在 [github](https://so.csdn.net/so/search?q=github&spm=1001.2101.3001.7020) 上开源的 vue 优秀的 UI 组件库供大家参考
>
> 这几套[框架](https://so.csdn.net/so/search?q=框架&spm=1001.2101.3001.7020)主要用于后台管理系统和移动端的制作，方便开发者快速开发
>
> 下面序列1，2，3的组件库，写法都不大一样的
>
> 1. Vue3写法，setup语法糖模式
> 2. Vue3写法，但这是setup()函数模式
> 3. 这套还是使用传统的Vue2写法

## **1.Element UI Plus**

> 就是 `饿了么` 团队开源出来的
>
> 采用的是Vue3写法，setup语法糖模式。序列1、2、3的组件库中只有这个ElementUI Plus支持Volar
>
> 在tsconfig.json中加上此段代码将会在我们写组件的时候给出提示
>
> ```typescript
> "types": ["element-plus/global"],
> ```

### 安装方法

```apl
# NPM
$ npm install element-plus --save
 
# Yarn
$ yarn add element-plus
 
# pnpm
$ pnpm install element-plus
```

### main ts 引入

```typescript
import { createApp } from 'vue'
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'
import App from './App.vue'
 
const app = createApp(App)
 
app.use(ElementPlus)
app.mount('#app')
```

###  volar 插件支持

```typescript
{
  "compilerOptions": {
    // ...
    "types": ["element-plus/global"]
  }
}
```

> [Vue3的UI框架|Element Plus](https://element-plus.gitee.io/zh-CN/)

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221028000901611-1711983932539-57.png" alt="image-20221028000901611" style="zoom:67%;" />

## 2. Ant Design Vue

> Vue3写法，但这是setup()函数模式，跟语法糖setup不一样了。这个函数模式需要我们将里面的函数跟值都进行一个手动return返回(会比较麻烦)
>
> 但 是可以支持TypeScript的

### 安装

```apl
$ npm install ant-design-vue@next --save
$ yarn add ant-design-vue@next
```

### 使用

```typescript
import { createApp } from 'vue';
import Antd from 'ant-design-vue';
import App from './App';
import 'ant-design-vue/dist/antd.css';
 
const app = createApp(App);
 
app.use(Antd).mount('#app');
```

[Ant Design Vue地址链接][https://next.antdv.com/docs/vue/introduce-cn]

## 3.Iview

> 这套还是使用传统的Vue2写法

### 安装

```apl
npm install view-ui-plus --save
```

### 使用

```typescript
import { createApp } from 'vue'
import ViewUIPlus from 'view-ui-plus'
import App from './App.vue'
import router from './router'
import store from './store'
import 'view-ui-plus/dist/styles/viewuiplus.css'
 
const app = createApp(App)
 
app.use(store)
  .use(router)
  .use(ViewUIPlus)
  .mount('#app')
```

[View Design组件库地址][https://www.iviewui.com/]

![image-20221028001204072](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221028001204072-1711983932539-55.png)

## 4.Vant 移动端

### 安装

```typescript
npm i vant -S
```

### 使用

```typescript
import Vant from 'vant'
import 'vant/lib/index.css';
createApp(App).use(vant).$mount('#app)
```

[Vant组件库地址][https://vant-contrib.gitee.io/vant/#/zh-CN/home]

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221028001422281-1711983932539-56.png" alt="image-20221028001422281" style="zoom:50%;" />

# 第三十二章 — 详解Scoped和样式 穿透

> 主要是**用于修改很多 vue 常用的组件库（element, vant, AntDesigin）**，虽然配好了样式但是还是需要更改其他的样式
>
> 就需要用到样式穿透
>
> - scoped 的原理
>
> vue 中的 scoped 通过在 DOM 结构以及 css 样式上加唯一不重复的标记:data-v-[hash](https://so.csdn.net/so/search?q=hash&spm=1001.2101.3001.7020) 的方式，以保证唯一（而这个工作是由过 PostCSS 转译实现的），达到样式私有化模块化的目的。
>
> 总结一下 scoped 三条渲染规则：
>
> 1. 给 HTML 的 DOM 节点加一个不重复 data 属性 (形如：data-v-123) 来表示他的唯一性
> 2. 在每句 css 选择器的末尾（编译后的生成的 css 语句）加一个当前组件的 data 属性选择器（如 [data-v-123]）来私有化样式
> 3. 如果组件内部包含有其他组件，只会给其他组件的最外层标签加上当前组件的 data 属性
>
> 视频中出现的代码展示：
>
> ```vue
> <!--App.vue文件-->
> <template>
>   <main>
>     <el-input placeholder="测试代码" class="ipt"></el-input>
>   </main>
> </template>
> 
> <script setup lang='ts'>
> 
> 
> </script>
> 
> <style scoped lang='less'>
> .ipt{
>   width: 300px;
>   margin: 100px 400px;
> }
> </style>
> <!--main.ts文件,记得导入element plus组件库-->
> import { createApp } from 'vue'
> import ElementPlus from 'element-plus'
> import 'element-plus/dist/index.css'
> import App from './App.vue'
> 
> const app = createApp(App)
> 
> app.use(ElementPlus)
> app.mount('#app')
> ```
>
> ![image-20230106115531991](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20230106115531991-1711983932539-58.png)

> `PostCSS` 会给一个组件中的所有 dom 添加了一个独一无二的动态属性 data-v-xxxx，然后，给 CSS 选择器额外添加一个对应的属性选择器来选择该组件中 dom，这种做法使得样式只作用于含有该属性的 dom—— 组件内部 dom, 从而达到了 ' 样式模块化 ' 的效果.

## 案例修改 Element Plus Input 样式

> 发现没有生效，属性选择器在父级上面了
>
> 这里小满重录了，笔记已经更新

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221028010957470-1711983932539-59.png" alt="image-20221028010957470" style="zoom:50%;" />

>  如果不写 Scoped 就没问题
>
> 原因就是 Scoped 搞的鬼 他在进行 PostCss 转化的时候把元素选择器默认放在了最后

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221028011031260-1711983932539-60.png" alt="image-20221028011031260" style="zoom:67%;" />

> Vue 提供了样式穿透`:deep ()` 他的作用就是用来改变 属性选择器的位置 =>这是Vue3的写法
>
> 在Vue2中是通过`/deep/`
>
> ```vue
> <style scoped lang='less'>
> .ipt{
>   width: 300px;
>   margin: 100px 400px;
>   :deep(.el-input__inner){
>     background: green;
>   }
> }
> </style>
> ```

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20221028011055850.png" alt="image-20221028011055850" style="zoom:50%;" /><img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20230106120442069-1711983932539-68.png" alt="image-20230106120442069" style="zoom:67%;" />

## 源码解析

> 位于源码的：core > packages > compiler-sfc > src > compileStyle.ts
>
> - compiler-sfc的sfc什么意思？
>   - 就是帮我们处理.vue文件(单文件组件)的 。一共由三部分组成(专门写了3个解析器)
>     - template(第一部分模板)
>     - script
>     - style
>   - ![image-20230106121235367](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20230106121235367-1711983932539-63.png)

```typescript
 //compileStyle文件
  const shortId = id.replace(/^data-v-/, '')
  const longId = `data-v-${shortId}`

  const plugins = (postcssPlugins || []).slice()
  plugins.unshift(cssVarsPlugin({ id: shortId, isProd }))
  if (trim) {
    plugins.push(trimPlugin())
  }
  //如果scoped = true 就向postCss 。PostCss = 是一个用JavaScript工具和插件转换CSS代码的工具
  if (scoped) {
    plugins.push(scopedPlugin(longId))
  }
  let cssModules: Record<string, string> | undefined
  if (modules) {
    if (__GLOBAL__ || __ESM_BROWSER__) {
      throw new Error(
        '[@vue/compiler-sfc] `modules` option is not supported in the browser build.'
      )
    }
    if (!options.isAsync) {
      throw new Error(
        '[@vue/compiler-sfc] `modules` option can only be used with compileStyleAsync().'
      )
    }
    plugins.push(
      postcssModules({
        ...modulesOptions,
        getJSON: (_cssFileName: string, json: Record<string, string>) => {
          cssModules = json
        }
      })
    )
  }
```

```typescript
//stylePluginScoped.ts文件
//PostCss插件
//PostCss 接收一个CSS文件并提供一个API来分析、修改它的规则(通过把CSS规则转换成一个抽象语法树的方式)
//跟babel类似的 babel是做js的AST postCss是做Css的，一样转化成抽象语法树
const scopedPlugin: PluginCreator<string> = (id = '') => {
  const keyframes = Object.create(null)
  const shortId = id.replace(/^data-v-/, '')

  return {
    //定义插件名称
    postcssPlugin: 'vue-sfc-scoped',
    //处理Css的AST
    Rule(rule) {
      processRule(id, rule)
    },
    //处理@相关的Css 例如media keyframes
    AtRule(node) {
      if (
        /-?keyframes$/.test(node.name) &&
        !node.params.endsWith(`-${shortId}`)
      ) {
        // register keyframes
        keyframes[node.params] = node.params = node.params + '-' + shortId
      }
    },
    //最后执行而且只处理一次
    OnceExit(root) {
      if (Object.keys(keyframes).length) {
        // If keyframes are found in this <style>, find and rewrite animation names
        // in declarations.
        // Caveat: this only works for keyframes and animation rules in the same
        // <style> element.
        // individual animation-name declaration
        root.walkDecls(decl => {
          if (animationNameRE.test(decl.prop)) {
            decl.value = decl.value
              .split(',')
              .map(v => keyframes[v.trim()] || v.trim())
              .join(',')
          }
          // shorthand
          if (animationRE.test(decl.prop)) {
            decl.value = decl.value
              .split(',')
              .map(v => {
                const vals = v.trim().split(/\s+/)
                const i = vals.findIndex(val => keyframes[val])
                if (i !== -1) {
                  vals.splice(i, 1, keyframes[vals[i]])
                  return vals.join(' ')
                } else {
                  return v
                }
              })
              .join(',')
          }
        })
      }
    }
  }
}

const processedRules = new WeakSet<Rule>()
//做缓存
function processRule(id: string, rule: Rule) {
  if (
    processedRules.has(rule) ||//这里的rule就是通过CSS转化后的一个AST
    (rule.parent &&
      rule.parent.type === 'atrule' &&
      /-?keyframes$/.test((rule.parent as AtRule).name))
  ) {
    return
  }
  processedRules.add(rule)
  //遍历AST节点
  rule.selector = selectorParser(selectorRoot => {
    selectorRoot.each(selector => {
      //重写选择器
      rewriteSelector(id, selector, selectorRoot)
    })
  }).processSync(rule.selector)
}

function rewriteSelector(
  id: string,
  selector: selectorParser.Selector,
  selectorRoot: selectorParser.Root,
  slotted = false
) {
  let node: selectorParser.Node | null = null
  let shouldInject = true
  // find the last child node to insert attribute selector
  selector.each(n => {
    // DEPRECATED ">>>" and "/deep/" combinator
    //废弃改用:deep()做样式穿透
    if (
      n.type === 'combinator' &&
      (n.value === '>>>' || n.value === '/deep/')
    ) {
      n.value = ' '
      n.spaces.before = n.spaces.after = ''
      warn(
        `the >>> and /deep/ combinators have been deprecated. ` +
          `Use :deep() instead.`
      )
      return false
    }

    if (n.type === 'pseudo') {//判断类型是否为pseudo
      const { value } = n
      // deep: inject [id] attribute at the node before the ::v-deep
      // combinator.
      if (value === ':deep' || value === '::v-deep') {//判断是否类型为pseudo的基础上看是否有:deep。然后做样式穿透
        if (n.nodes.length) {
            //.foo .bar[xxxxxx]这就是加在选择器末尾的独一无二的那个
          // .foo ::v-deep(.bar) -> .foo[xxxxxxx] .bar
          // replace the current node with ::v-deep's inner selector，下面这一段就是为了挪动属性选择器的位置
          let last: selectorParser.Selector['nodes'][0] = n
          n.nodes[0].each(ss => {
            selector.insertAfter(last, ss)
            last = ss
          })
          // insert a space combinator before if it doesn't already have one
          const prev = selector.at(selector.index(n) - 1)
          if (!prev || !isSpaceCombinator(prev)) {
            selector.insertAfter(
              n,
              selectorParser.combinator({
                value: ' '
              })
            )
          }
          selector.removeChild(n)
        } else {
          // DEPRECATED usage
          // .foo ::v-deep .bar -> .foo[xxxxxxx] .bar
          warn(
            `::v-deep usage as a combinator has ` +
              `been deprecated. Use :deep(<inner-selector>) instead.`
          )
          const prev = selector.at(selector.index(n) - 1)
          if (prev && isSpaceCombinator(prev)) {
            selector.removeChild(prev)
          }
          selector.removeChild(n)
        }
        return false
      }

      // slot: use selector inside `::v-slotted` and inject [id + '-s']
      // instead.
      // ::v-slotted(.foo) -> .foo[xxxxxxx-s]
      if (value === ':slotted' || value === '::v-slotted') {
        rewriteSelector(id, n.nodes[0], selectorRoot, true /* slotted */)
        let last: selectorParser.Selector['nodes'][0] = n
        n.nodes[0].each(ss => {
          selector.insertAfter(last, ss)
          last = ss
        })
        // selector.insertAfter(n, n.nodes[0])
        selector.removeChild(n)
        // since slotted attribute already scopes the selector there's no
        // need for the non-slot attribute.
        shouldInject = false
        return false
      }

      // global: replace with inner selector and do not inject [id].
      // ::v-global(.foo) -> .foo
      if (value === ':global' || value === '::v-global') {
        selectorRoot.insertAfter(selector, n.nodes[0])
        selectorRoot.removeChild(selector)
        return false
      }
    }
      
  //Vue3新增的穿透slot
  // slot: use selector inside `::v-slotted` and inject [id + '-s']
  // instead.
  // ::v-slotted(.foo) -> .foo[xxxxxxx-s]
  if (value === ':slotted' || value === '::v-slotted') {
    rewriteSelector(id, n.nodes[0], selectorRoot, true /* slotted */)
    let last: selectorParser.Selector['nodes'][0] = n
    n.nodes[0].each(ss => {
      selector.insertAfter(last, ss)
      last = ss
    })
    // selector.insertAfter(n, n.nodes[0])
    selector.removeChild(n)
    // since slotted attribute already scopes the selector there's no
    // need for the non-slot attribute.
    shouldInject = false
    return false
  }
      
      //全局穿透
  // global: replace with inner selector and do not inject [id].
  // ::v-global(.foo) -> .foo
  if (value === ':global' || value === '::v-global') {
    selectorRoot.insertAfter(selector, n.nodes[0])
    selectorRoot.removeChild(selector)
    return false
  }
```



# 第三十三章 — css Style 完整新特性

> 上一章已经讲过了:deep (), 其实还有两个`选择器`可以补充

## 1.插槽选择器

> 想修改插槽里面的内容可以使用这个:slotted()
>
> A 组件定义一个插槽

```typescript
<template>
    <div>
        我是插槽
        <slot></slot>
    </div>
</template>
 
<script>
export default {}
</script>
 
<style scoped>
 
</style>
```

> 在 App.vue 引入

```typescript
<template>
    <div>
        <A>
            <div class="a">私人定制div</div>
        </A>
    </div>
</template>
 
<script setup>
import A from "@/components/A.vue"
</script>
 
 
<style lang="less" scoped>
</style>
```

> 在 A 组件修改 class a 的颜色

```typescript
<style scoped>
.a{
    color:red
}
</style>
```

> 无效果
>
> 默认情况下，作用域样式不会影响到 `<slot/>` 渲染出来的内容，因为它们被认为是父组件所持有并传递进来的。

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221028012502463-1711983932539-61.png" alt="image-20221028012502463" style="zoom:50%;" />

> 解决方案 slotted

```typescript
//这是在子组件进行的修改，虽然class="a"在App父组件中
<style scoped>
 :slotted(.a) {//生效在这里:slotted
    color:red
}
</style>
```

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221028012602471-1711983932539-62.png" alt="image-20221028012602471" style="zoom:60%;" />

## 2.全局选择器

> 在之前我们想加入全局 样式 通常都是新建一个 style 标签 不加 scoped 现在有更优雅的解决方案
>
> 效果等同于上面 
>
> ### 一、 scoped 的作用：
>
> 使当前组件的样式不会和其它组件冲突
>
> ### 二、注意事件：
>
> 1、使用 scoped 后，父组件的样式将不会渗透到子组件中。
> 2、不过一个子组件的根节点会同时受其父组件的 scoped CSS 和子组件的 scoped CSS 的影响。
> 3、这样设计是为了让父组件可以从布局的角度出发，调整其子组件根元素的样式

```typescript
<style>
 div{
     color:red
 }
</style>
 
<style lang="less" scoped>
 
</style>
```

```typescript
//这是在父组件进行的修改，在父组件修改子组件的css就通过:global()。在子组件修改插槽就通过:slotted()
//App.vue中a样式会覆盖组件中的a
<style lang="less" scoped>
:global(div){
    color:red
}
</style>
```

## 3.动态 CSS

> 单文件组件的 `<style>` 标签可以通过 `v-bind` 这一 CSS 函数将 CSS 的值关联到动态的组件状态上：
>
> 就能响应式的改变css了

```typescript
<template>
    <div class="div">
       小满是个弟弟
    </div>
</template>
 
<script lang="ts" setup>
import { ref } from 'vue'
const red = ref<string>('red')
</script>
 
<style lang="less" scoped>
.div{
   color:v-bind(red)//将css属性与script的变量绑定起来了，这里red绑定的是const red的red
}
 
</style>
```

> 如果是对象 v-bind 请加引号
>
> 就是说ref({XXX})的写法

```typescript
 <template>
    <div class="div">
        小满是个弟弟
    </div>
</template>
 
<script lang="ts" setup>
import { ref } from "vue"
const red = ref({
    color:'pink'
})
</script>
 
    <style lang="less" scoped>
.div {
    color: v-bind('red.color');
}
</style>
```

## 4.css `module`

> <style module> 标签会被编译为 CSS Modules 并且将生成的 CSS 类作为 $style 对象的键暴露给组件

```typescript
<template>
    <div :class="$style.red">//默认名字是$style.你class的名字。请注意，这里是:class=""，有冒号的
        小满是个弟弟
    </div>
</template>
 
<style module>
.red {
    color: red;
    font-size: 20px;
}
</style>
```

> 自定义注入名称（多个可以用数组）
>
> 你可以通过给 `module` attribute 一个值来自定义注入的类对象的 property 键

```typescript
<template>
    <div :class="[zs.red,zs.border]">
        小满是个弟弟
    </div>
</template>
 
<style module="zs">//这个zs可写可不写，写了就前缀为zs，不写就依旧为$style
.red {
    color: red;
    font-size: 20px;
}
.border{
    border: 1px solid #ccc;
}
</style>
```

> 与组合式 API 一同使用
>
> 注入的类可以通过 [useCssModule](https://v3.cn.vuejs.org/api/global-api.html#usecssmodule) API 在 `setup()` 和 `<script setup>` 中使用。对于使用了自定义注入名称的 `<style module>` 模块，`useCssModule` 接收一个对应的 `module` attribute 值作为第一个参数

```typescript
<template>
    <div :class="[zs.red,zs.border]">
        小满是个弟弟
    </div>
</template>
 
 
<script setup lang="ts">
import { useCssModule } from 'vue'
const css = useCssModule('zs')//利用哈希生成类名防止重名
</script>
 
<style module="zs">
.red {
    color: red;
    font-size: 20px;
}
.border{
    border: 1px solid #ccc;
}
</style>
```

- 使用场景一般用于 TSX 和 render 函数 居多

# 第三十四章 — Vue3集成Tailwind CSS

> Tailwind CSS 是一个由 js 编写的 CSS [框架](https://so.csdn.net/so/search?q=框架&spm=1001.2101.3001.7020) 他是基于 postCss 去解析的
>
>  官网地址 [Tailwind CSS 中文文档 - 无需离开您的 HTML，即可快速建立现代网站。](https://www.tailwindcss.cn/)
>
> 对于 PostCSS 的插件使用，我们再使用的过程中一般都需要如下步骤：
>
> 1. PostCSS 配置文件 postcss.config.js，新增 tailwindcss 插件。
> 2. TaiWindCss 插件需要一份配置文件，比如:tailwind.config.js。
>
> [PostCSS - 是一个用 JavaScript 工具和插件来转换 CSS 代码的工具 | PostCSS 中文网](https://www.postcss.com.cn/)
>
> postCss 功能介绍
>
> 1. 增强代码的可读性 （利用从 Can I Use 网站获取的数据为 CSS 规则添加特定厂商的前缀。 [Autoprefixer](https://github.com/postcss/autoprefixer) 自动获取浏览器的流行度和能够支持的属性，并根据这些数据帮你自动为 CSS 规则添加前缀。）
>
> 2. 将未来的 CSS 特性带到今天！（[PostCSS Preset Env](https://preset-env.cssdb.org/) 帮你将最新的 CSS 语法转换成大多数浏览器都能理解的语法，并根据你的目标浏览器或运行时环境来确定你需要的 polyfills，此功能基于 [cssdb](https://cssdb.org/) 实现。）
>
> 3. 终结全局 CSS（[CSS 模块](https://github.com/css-modules/css-modules) 能让你你永远不用担心命名太大众化而造成冲突，只要用最有意义的名字就行了。）
>
> 4. 避免 CSS 代码中的错误（通过使用 [stylelint](https://stylelint.io/) 强化一致性约束并避免样式表中的错误。stylelint 是一个现代化 CSS 代码检查工具。它支持最新的 CSS 语法，也包括类似 CSS 的语法，例如 SCSS 。）
>
> postCss 处理 tailWind Css 大致流程
>
> - 将 CSS 解析成抽象语法树 (AST 树)
> - 读取插件配置，根据配置文件，生成新的抽象语法树
> - 将 AST 树” 传递” 给一系列数据转换操作处理（变量数据循环生成，切套类名循环等）
> - 清除一系列操作留下的数据痕迹
> - 将处理完毕的 AST 树重新转换成字符串

## 安装

### 1. 初始化项目

```ini
npm init vue@latest
```

### 2. 安装 Tailwind 以及其它依赖项

```typescript
npm install -D tailwindcss@latest postcss@latest autoprefixer@latest
```

### 3. 生成配置文件

```typescript
npx tailwindcss init -p
```

[配置 - Tailwind CSS 中文文档](https://www.tailwindcss.cn/docs/configuration)

### 4. 修改配置文件 tailwind.config.js

> 作用：当你要打包的时候，有一些类名没有使用，就不会把没用过的类名打包进去

#### 2.6 版本 

```typescript
module.exports = {
  purge: ['./index.html', './src/**/*.{vue,js,ts,jsx,tsx}'],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

#### 3.0 版本

```typescript
module.exports = {
  content: ['./index.html', './src/**/*.{vue,js,ts,jsx,tsx}'],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

### 5. 创建一个 index.css

> 在src建立个文件夹装进去，然后在main.ts配置文件中引入，这样在全局都能使用了

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

###  在 main.ts 引入

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221028165506768-1711983932539-64.png" alt="image-20221028165506768" style="zoom:35%;" /><img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20221028165529086.png" alt="image-20221028165529086" style="zoom:40%;" />

>  最后 npm run dev 就可以使用啦

```typescript
  <div class="max-w-md mx-auto bg-white rounded-xl shadow-md overflow-hidden md:max-w-2xl">
    <div class="md:flex">
      <div class="md:flex-shrink-0">
        <img class="h-48 w-full object-cover md:w-48" src="http://n.sinaimg.cn/translate/20170815/OoVn-fyixtym5144510.jpg" alt="Man looking at item at a store">
      </div>
      <div class="p-8">
        <div class="uppercase tracking-wide text-sm text-indigo-500 font-semibold">Case study</div>
        <a href="#" class="block mt-1 text-lg leading-tight font-medium text-black hover:underline">Finding customers
          for your new business</a>
        <p class="mt-2 text-gray-500">Getting a new business off the ground is a lot of hard work. Here are five ideas
          you can use to find your first customers.</p>
      </div>
    </div>
  </div>
```

# 第三十五章 — (Evnet Loop 和 nextTick)

## 异步小测试

> 计时器属于`宏任务`

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221028174817630-1711983932539-65.png" alt="image-20221028174817630" style="zoom:67%;" />

```JS
<script setup lang="ts">
  async function Prom(){
    console.log('Y')
    await Promise.resolve()
    console.log('X')
  }
  setTimeout(()=>{
    console.log(1)
    Promise.resolve().then(()=>{
      console.log(2)
    })
  },0)
  setTimeout(()=>{
    console.log(3)
    Promise.resolve().then(()=>{
      console.log(4)
    })
  },0)
  Promise.resolve().then(()=>{
    console.log(5)
  })
  Promise.resolve().then(()=>{
    console.log(6)
  })
  Promise.resolve().then(()=>{
    console.log(7)
  })
  Promise.resolve().then(()=>{
    console.log(8)
  })
  Prom()
  console.log(0)
</script>

首先定义了一个异步函数 Prom()，其中先打印出 Y，然后使用 await 等待一个 resolved 状态的 Promise，等待过程中会暂停函数的执行，直到 Promise 状态变为 resolved 才会继续执行，因此这段代码会先打印出 Y，然后再打印出 X。

接下来使用两个 setTimeout 函数，分别在 0 秒后执行，第一个函数内部先打印出 1，然后使用 Promise 对象的 then() 方法注册一个回调函数，在 Promise 状态变为 resolved 时打印出 2。第二个 setTimeout 函数内部先打印出 3，然后使用 Promise 对象的 then() 方法注册一个回调函数，在 Promise 状态变为 resolved 时打印出 4。

接下来又使用了四个 Promise 对象，分别使用 then() 方法注册了四个回调函数，分别打印出 5、6、7、8，这些 Promise 对象的状态都是立即变为 resolved 的。

最后调用了 Prom() 函数，该函数会先打印出 Y，然后等待一个 resolved 状态的 Promise，等待过程中暂停函数的执行，直到 Promise 状态变为 resolved 才会继续执行，因此该函数最后打印出 X。

最后执行了一条同步语句，打印出 0。因此最终的输出结果为：
//输出Y 0 5 6 7 8 X 1 2 3 4
```

> 在我们学习 nextTick 之前需要先了解 Event Loop 事件循环机制

## JS 执行机制

在我们学 js 的时候都知道 js 是单线程的如果是[多线程](https://so.csdn.net/so/search?q=多线程&spm=1001.2101.3001.7020)的话会引发一个问题在同一时间同时操作 DOM 一个增加一个删除 JS 就不知道到底要干嘛了，所以这个语言是单线程的但是随着 HTML5 到来 js 也支持了多线程 webWorker 但是也是不允许操作 DOM

单线程就意味着所有的任务都需要排队，后面的任务需要等前面的任务执行完才能执行，如果前面的任务耗时过长，后面的任务就需要一直等，一些从用户角度上不需要等待的任务就会一直等待，这个从体验角度上来讲是不可接受的，所以 `JS` 中就出现了异步的概念。

## 同步任务

> 代码从上到下按顺序执行

## 异步任务

### 1. 宏任务

> script (整体代码)、setTimeout、setInterval、UI 交互事件、postMessage、Ajax

### 2. 微任务

> Promise.then catch finally、MutaionObserver(监听DOM的变化)、process.nextTick (Node.js 环境)

- 运行机制
  - 所有的同步任务都是在主进程执行的形成一个执行栈，主线程之外，还存在一个 "任务队列"，异步任务执行队列中先执行宏任务，然后清空当次宏任务中的所有微任务，然后进行下一个 tick 如此形成循环。

`nextTick` 就是创建一个异步任务，那么它自然要等到同步任务执行完成后才执行。

```typescript
<template>
   <div ref="xiaoman">
      {{ text }}
   </div>
   <button @click="change">change div</button>
</template>
   
<script setup lang='ts'>
import { ref,nextTick } from 'vue';
 
const text = ref('小满开飞机')
const xiaoman = ref<HTMLElement>()
 
const change = async () => {
   text.value = '小满不开飞机'
   console.log(xiaoman.value?.innerText) //小满开飞机
   await nextTick();//这里不加的话，界面的值已经发生改变，但控制台输出的还是没有发生变化
   console.log(xiaoman.value?.innerText) //小满不开飞机
}
 
 
</script>
 
 
<style  scoped>
</style>
```

### 源码解析

> 视频1分46
>
> 地址 core\packages\runtime-core\src\scheduler.ts

```typescript
const resolvedPromise = /*#__PURE__*/ Promise.resolve() as Promise<any>//两个状态
let currentFlushPromise: Promise<void> | null = null

const resolvedPromise: Promise<any> = Promise.resolve()
let currentFlushPromise: Promise<void> | null = null
 
export function nextTick<T = void>(
  this: T,
  fn?: (this: T) => void//fn为回调函数
): Promise<void> {
  const p = currentFlushPromise || resolvedPromise//p永远为Promise
  //currentFlushPromise：队列里有没有Prmise，有的话就会去赋一个值，如果没有的话就有一个兜底逻辑resolvedPromise(其实也是返回一个状态)
  return fn ? p.then(this ? fn.bind(this) : fn) : p//三元表达式，如果有fn就放在p.then(this ? fn.bind(this)，没有就直接返回p。所以永远会返回一个微任务
}

//nextTick 接受一个参数 fn（函数）定义了一个变量 P 这个 P 最终返回都是 Promise，最后是 return 如果传了 fn 就使用变量 P.then 执行一个微任务去执行 fn 函数，then 里面 this 如果有值就调用 bind 改变 this 指向返回新的函数，否则直接调用 fn，如果没传 fn，就返回一个 promise，最终结果都会返回一个 promise
```

> 之前的 ref 源码中有一段 triggerRefValue 他会去调用 triggerEffects

```typescript
export function triggerRefValue(ref: RefBase<any>, newVal?: any) {
  ref = toRaw(ref)
  if (ref.dep) {
    if (__DEV__) {
      triggerEffects(ref.dep, {
        target: ref,
        type: TriggerOpTypes.SET,
        key: 'value',
        newValue: newVal
      })
    } else {
      triggerEffects(ref.dep)
    }
  }
}
```

```typescript
export function triggerEffects(
  dep: Dep | ReactiveEffect[],
  debuggerEventExtraInfo?: DebuggerEventExtraInfo
) {
  // spread into array for stabilization
  for (const effect of isArray(dep) ? dep : [...dep]) {
    if (effect !== activeEffect || effect.allowRecurse) {
      if (__DEV__ && effect.onTrigger) {
        effect.onTrigger(extend({ effect }, debuggerEventExtraInfo))
      }
      //当响应式对象发生改变后，执行 effect 如果有 scheduler 这个参数，会执行这个 scheduler 函数
      if (effect.scheduler) {
        effect.scheduler()
      } else {
        effect.run()
      }
    }
  }
}
```

> 那么 scheduler 这个函数从哪儿来的 我们看这个类 ReactiveEffect

```typescript
export class ReactiveEffect<T = any> {
  active = true
  deps: Dep[] = []
  parent: ReactiveEffect | undefined = undefined
 
  /**
   * Can be attached after creation
   * @internal
   */
  computed?: ComputedRefImpl<T>
  /**
   * @internal
   */
  allowRecurse?: boolean
 
  onStop?: () => void
  // dev only
  onTrack?: (event: DebuggerEvent) => void
  // dev only
  onTrigger?: (event: DebuggerEvent) => void
 
  constructor(
    public fn: () => T,
    public scheduler: EffectScheduler | null = null, //我在这儿 
    scope?: EffectScope
  ) {
    recordEffectScope(this, scope)
  }
```

> scheduler 作为一个参数传进来的

```typescript
   const effect = (instance.effect = new ReactiveEffect(
      componentUpdateFn,
      () => queueJob(instance.update),
      instance.scope // track it in component's effect scope
    ))
```

> 他是在初始化 effect 通过 queueJob 传进来的

```typescript
//queueJob 维护job列队，有去重逻辑，保证任务的唯一性，每次调用去执行，被调用的时候去重，每次调用去执行 queueFlush
export function queueJob(job: SchedulerJob) {
  // 判断条件：主任务队列为空 或者 有正在执行的任务且没有在主任务队列中  && job 不能和当前正在执行任务及后面待执行任务相同
  // 重复数据删除：
  // - 使用Array.includes(Obj, startIndex) 的 起始索引参数：startIndex
  // - startIndex默认为包含当前正在运行job的index，此时，它不能再次递归触发自身
  // - 如果job是一个watch()回调函数或者当前job允许递归触发，则搜索索引将+1，以允许他递归触发自身-用户需要确保回调函数不会死循环
  if (
    (!queue.length ||
      !queue.includes(
        job,
        isFlushing && job.allowRecurse ? flushIndex + 1 : flushIndex
      )) &&
    job !== currentPreFlushParentJob
  ) {
    if (job.id == null) {
      queue.push(job)
    } else {
      queue.splice(findInsertionIndex(job.id), 0, job)
    }
    queueFlush()
  }
}
```

>  queueJob 维护 job 列队 并且调用 queueFlush

```typescript
function queueFlush() {
  // 避免重复调用flushJobs
  if (!isFlushing && !isFlushPending) {
    isFlushPending = true
     //开启异步任务处理flushJobs
    currentFlushPromise = resolvedPromise.then(flushJobs)
  }
}
```

> queueFlush 给每一个队列创建了微任务

# 第三十六章 — Vue如何开发移动端

> **如果使用 npm init vue@latest 报错**
>
> **error when starting dev server: Error: Cannot find module 'node:path'**
>
> **nodejs 升级为 16 版本就好了**
>
> 开发移动端最主要的就是适配各种手机，为此我研究了一套解决方案
>
> 在之前我们用的是 rem 根据 HTML font-size 去做缩放
>
> 现在有了更好用的 vw vh
>
> vw 视口的最大宽度，1vw 等于视口宽度的百分之一
>
> vh 视口的最大高度，1vh 等于视口高度的百分之一

## 1. 安装依赖

```typescript
 npm install postcss-px-to-viewport -D
//这个依赖的作用就是将我们的px转化为vw跟vh
```

> 因为 vite 中已经内联了 `postcss`，所以并不需要额外的创建 postcss.config.js 文件

### vite.config.ts

> 配置完重启项目

```typescript
import { fileURLToPath, URL } from 'url'
 
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import vueJsx from '@vitejs/plugin-vue-jsx'
import postcsspxtoviewport from "postcss-px-to-viewport" //插件，这个插件会报错的原因是因为没有声明文件，社区没有给TS进行适配，不过小满在底下已经替大家写好了声明文件，也许未来的某一天社区会写好这个声明文件，那到时候直接按提示安装就行了
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(), vueJsx()],
  css: {
    postcss: {
      plugins: [
        postcsspxtoviewport({
          unitToConvert: 'px', // 要转化的单位
          viewportWidth: 750, // UI设计稿的宽度
          unitPrecision: 6, // 转换后的精度，即小数点位数
          propList: ['*'], // 指定转换的css属性的单位，*代表全部css属性的单位都进行转换
          viewportUnit: 'vw', // 指定需要转换成的视窗单位，默认vw
          fontViewportUnit: 'vw', // 指定字体需要转换成的视窗单位，默认vw
          selectorBlackList: ['ignore-'], // 指定不转换为视窗单位的类名，
          minPixelValue: 1, // 默认值1，小于或等于1px则不进行转换
          mediaQuery: true, // 是否在媒体查询的css代码中也进行转换，默认false
          replace: true, // 是否转换后直接更换属性值
          landscape: false // 是否处理横屏情况
        })
      ]
    }
  },
  resolve: {
    alias: {
      '@': fileURLToPath(new URL('./src', import.meta.url))
    }
  }
})
```

> ### 如果你用的 vite 是 ts 他这个插件并没有提供声明文件我已经帮大家写好了声明文件（良心）

```typescript
//postcss-px-to-viewport.d.ts文件

declare module 'postcss-px-to-viewport' {
 
    type Options = {//设定了一个Option类型并将其暴露出去
        unitToConvert: 'px' | 'rem' | 'cm' | 'em',
        viewportWidth: number,
        viewportHeight: number, // not now used; TODO: need for different units and math for different properties
        unitPrecision: number,
        viewportUnit: string,
        fontViewportUnit: string,  // vmin is more suitable.
        selectorBlackList: string[],
        propList: string[],
        minPixelValue: number,
        mediaQuery: boolean,
        replace: boolean,
        landscape: boolean,
        landscapeUnit: string,
        landscapeWidth: number
    }
 
    export default function(options: Partial<Options>):any//暴露出去且类型any。Partial<Options>的意思就是非必填(可选)
}
```

> 引入声明文件 tsconfig.app **postcss-px-to-viewport.d.ts(这个是我们自己创建的文件) 跟 vite.ts 同级**

```typescript
//tsconfig.config.json文件
{
  "extends": "@vue/tsconfig/tsconfig.web.json",
  "include": ["env.d.ts", "src/**/*", "src/**/*.vue", "postcss-px-to-viewport.d.ts"],
  "exclude": ["src/**/__tests__/*"],
  "compilerOptions": {
    "composite": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

> 代码案例
>
> css想使用嵌套语法需要安装less，npm install less-D

```typescript
<template>
  <div class="wraps">
    <header class="header">
      <div>left</div>
      <div>中间</div>
      <div>right</div>
    </header>
 
    <main class="main">
      <div class="main-items" v-for="item in 100">
        <div class="main-port">头像</div>
        <div class="main-desc">
          <div>小满{{item}}</div>
          <div>你妈妈喊你回家穿丝袜啦</div>
        </div>
      </div>
    </main>
 
 
    <footer class="footer">
      <div class="footer-items" v-for="item in footer">
        <div>{{ item.icon }}</div>
        <div>{{ item.text }}</div>
      </div>
    </footer>
  </div>
 
</template>
  
<script setup lang='ts'>
import { reactive } from 'vue';
 
type Footer<T> = {
  icon: T,
  text: T
}
 
const footer = reactive<Footer<string>[]>([
  {
    icon: "1",
    text: "首页"
  },
  {
    icon: "2",
    text: "商品"
  },
  {
    icon: "3",
    text: "信息"
  },
  {
    icon: "4",
    text: "我的"
  }
])
</script>
  
<style lang="less">
@import url('@/assets/base.css');
 
html,
body,
#app {
  height: 100%;
  overflow: hidden;//超出的范围进行隐藏
  font-size: 14px;
}
 
.wraps {
  height: inherit;
  overflow: hidden;
  display: flex;
  flex-direction: column;
}
 
.header {
  background-color: pink;
  display: flex;
  height: 30px;
  align-items: center;
  justify-content: space-around;
 
  div:nth-child(1) {
    width: 40px;
  }
 
  div:nth-child(2) {
    text-align: center;
  }
 
  div:nth-child(3) {
    width: 40px;
    text-align: right;
  }
}
 
.main {
  flex: 1;
  overflow: auto;//让中间部位可以滚动，配合overflow:hidden。形成滚动条样式
 
  &-items {
    display: flex;
    border-bottom: 1px solid #ccc;
    box-sizing: border-box;
    padding: 5px;
  }
 
  &-port {
    background: black;
    width: 30px;
    height: 30px;
    border-radius: 200px;
  }
  &-desc{
     margin-left:10px;
     div:last-child{
        font-size: 10px;
        color:#333;
        margin-top: 5px;
     }
  }
}
 
.footer {
 
  border-top: 1px solid #ccc;
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;//grid-template-columns 属性规定网格布局中的列数（和宽度），你写几个1fr就一行排几个
 
  &-items {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    padding: 5px;
  }
}
</style>
```

## 如何将我们的 Vue 项目打包成 App（会安卓的可以跳过）

1. 安装 JDK [Java Downloads | Oracle](https://www.oracle.com/java/technologies/downloads/#java8-windows)
   - 这是基于JAVA环境的，所以需要JDK(选择8会更稳定)

> 配置环境变量
>
> JAVA_HOME

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029011414486-1711983932539-66.png" alt="image-20221029011414486" style="zoom:50%;" />

>  CLASSPATH （下面的照抄就行）

```typescript
.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;
```

>  最后一个 Path
>
> 添加 % JAVA_HOME%\bin

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029011505040-1711983932539-67.png" alt="image-20221029011505040" style="zoom:67%;" />

> 安装完后通过终端输入java或者javac，看能不能正常显示，可以的话就是安装成功了

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029012007573-1711983932539-69.png" alt="image-20221029012007573" style="zoom:50%;" />

2. [安卓编辑器下载地址(建议科学上网不然很慢)][https://developer.android.com/]

> 一直 next 就行了 然后磁盘选一下 安装就可以了 
>
> 装完启动需要还需要安装 sdk 然后就可以打开了
>
> 新建一个空项目选这个就可以了

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20221029012119703.png" alt="image-20221029012119703" style="zoom:40%;" /><img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029012139135-1711983932539-70.png" alt="image-20221029012139135" style="zoom:40%;" />

> 语言会 java 选 java 会别的就选别的

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029012224076-1711983932539-71.png" alt="image-20221029012224076" style="zoom:50%;" />

> 等待加载完成

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029012249002-1711983932539-77.png" alt="image-20221029012249002" style="zoom:67%;" />

> 打开之后可以创建一个虚拟机 

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029012308994-1711983932539-72.png" alt="image-20221029012308994" style="zoom:67%;" />

> 创建完成之后就可以运行安卓项目

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029012334927-1711983932539-73.png" alt="image-20221029012334927" style="zoom:50%;" />

>  其实就是一个虚拟手机

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029012412260-1711983932539-74.png" alt="image-20221029012412260" style="zoom:67%;" />

> 切换成代码模式

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029012434685-1711983932539-78.png" alt="image-20221029012434685" style="zoom:50%;" />

>  修改成以下代码
>
> layout文件夹下的activity_main.xml文件

```typescript
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout  xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:orientation="vertical"
    android:layout_height="match_parent">
 
    <WebView
        android:id="@+id/web_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout  >
```

### LinearLayout概述

> `线性布局(LinearLayout)`主要以水平或垂直方式来排列界面中的控件。并将控件排列到一条直线上。在线性布局中，如果水平排列，垂
> 直方向上只能放一个控件，如果垂直排列，水平方向上也只能方一个控件。
>
> 其实就是类似于我们前端的 display felx

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029012546483-1711983932539-75.png" alt="image-20221029012546483" style="zoom:50%;" />

> webView 就和小程序的 webView 基本一样 套网页的
>
> **match_parent 表示让当前控件的大小和父布局的大小一样，也就是让父布局来决定当前控件的大小**

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029012637496-1711983932539-76.png" alt="image-20221029012637496" style="zoom:50%;" />

> java 代码逻辑

```java
package com.example.myapplication;
 
import androidx.appcompat.app.AppCompatActivity;
 
import android.os.Bundle;
 
import android.webkit.WebView;
 
import android.app.Activity;
 
import android.webkit.WebViewClient;
 
public class MainActivity extends Activity {
 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        //设置一个Activity的显示界面，
        setContentView(R.layout.activity_main);
 
        WebView view = (WebView)findViewById(R.id.web_view);
        //设置 WebView 属性，能够执行 Javascript 脚本
        view.getSettings().setJavaScriptEnabled(true);
        //加载需要显示的网页 不能使用局域网地址 只能虚拟机专属地址 http://10.0.2.2 端口是我们vue 项目端口
        view.loadUrl("http://10.0.2.2:3000");
 
        view.setWebViewClient(new WebViewClient());
    }
}
```

> ```
> 加载需要显示的网页 不能使用局域网地址 只能虚拟机专属地址 http://10.0.2.2 端口是我们vue 项目端口
> ```

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029012717584-1711983932539-79.png" alt="image-20221029012717584" style="zoom:67%;" />

> 配置权限

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029012741080-1711983932539-83.png" alt="image-20221029012741080" style="zoom:67%;" />

```java
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.example.myapplication">
 
    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.MyApplication"
        android:usesCleartextTraffic="true"
        tools:targetApi="31">
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
 
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
     //解决权限不足的问题
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
</manifest>
```

![image-20221029012803227](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029012803227-1711983932539-80.png)

> 你就可以看到我们的项目了
>
> 最后打包
>
> 打包的时候路径记得换成线上的服务器地址

![image-20221029012833653](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029012833653-1711983932539-81.png)

# 第三十七章 — unocss原子化

## 什么是CSS原子化

> CSS 原子化的优缺点
>
> 1. 减少了 css 体积，提高了 css 复用
>
> 2. 减少起名的复杂度
>
> 3. 增加了记忆成本 将 css 拆分为原子之后，你势必要记住一些 class 才能书写，哪怕 tailwindcss 提供了完善的工具链，你写 background，也要记住开头是 bg

## 接入 unocss

> **tips：最好用于 vite ，webpack 属于阉割版功能很少**

### 安装

```js
npm i -D unocss
//或者pnpm的方式
pnpm add unocss
```

### vite.config.ts 

```ts
import unocss from 'unocss/vite'
 
 plugins: [vue(), vueJsx(),unocss({
      rules:[
        //自定义匹配规则，是一个二维数组
        //第一个参数是class类名。第二个参数对象{}，里面有属性
          ['flex',{display:"flex"}],//这是两个例子
          ['red',{color:'red'}]
      ]
  })],
```

### main.ts 引入 

```ts
import 'uno.css'
```

### 配置静态 css

```js
rules: [
  ['flex', { display: "flex" }]
]
```

![image-20221029014539491](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029014539491-1711983932539-82.png)

### 配置动态 css（使用`正则表达式`）

> m - 参数 * 10  例如 m-10 就是 margin:100px

```TS
rules: [
  [/^m-(\d+)$/, ([, d]) => ({ margin: `${Number(d) * 10}px` })],//假设我们传入m-1,就margin:1*10px
  ['flex', { display: "flex" }]
]
```

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029014635847-1711983932539-84.png" alt="image-20221029014635847" style="zoom:67%;" />

### shortcuts 可以自定义组合样式

```TS
  plugins: [vue(), vueJsx(), unocss({
    rules: [
      [/^m-(\d+)$/, ([, d]) => ({ margin: `${Number(d) * 10}px` })],
      ['flex', { display: "flex" }],
      ['pink', { color: 'pink' }]
    ],
    shortcuts: {//这里
      btn: "pink flex"
      //cike:['flex','red']
    }
  })],
```

![image-20221029014714339](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029014714339-1711983932539-85.png)

###  unocss 预设

```TS
//写在vite.config.ts文件中的，跟rules同级 
presets:[presetIcons(),presetAttributify(),presetUno()]//presetAttributify()用来美化预设属性的
```

### 1.presetIcons Icon 图标预设

#### 图标集合安装

> 图标库地址：https://icones.js.org/collection/ic

```TS
npm i -D @iconify-json/ic
//@iconify-json/ic是图标库，ic是后缀，不同的库，后缀不一样
```

> 首先我们去 [icones](https://icones.js.org/) 官网（方便浏览和使用 iconify）浏览我们需要的 icon，比如这里我用到了 Google Material Icons 图标集里面的 baseline-add-circle 图标

```js
<div  class="i-ic-baseline-backspace text-3xl bg-green-500" />
```

### 2.presetAttributify 属性化模式支持

> 属性语义化 无须 class

```TS
<div font="black">
     btn
</div>
```

![image-20221029014857392](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029014857392-1711983932539-87.png)

### 3.presetUno 工具类预设

> 默认的 `@unocss/preset-uno` 预设（**实验阶段**）是一系列流行的原子化框架的 **通用超集**，包括了 Tailwind CSS，Windi CSS，Bootstrap，Tachyons 等。
>
> 例如，`ml-3`（Tailwind），`ms-2`（Bootstrap），`ma4`（Tachyons），`mt-10px`（Windi CSS）均会生效。

# 第三十八章 — 函数式编程、h函数

> 之前跟大家介绍了两种 vue 编写风格分别是 template 模板方式，和 JSX 方式感觉 JSX 被大家吐槽的很厉害，其实用习惯还挺好用的今天介绍第三种[函数式编程](https://so.csdn.net/so/search?q=函数式编程&spm=1001.2101.3001.7020)
>
> 主要会用到 h 函数
>
> `h` 接收三个参数
>
> - type 元素的类型
> - propsOrChildren 数据对象，这里主要表示 (props, attrs, dom props, class 和 style)
> - children 子节点

## h 函数拥有多种组合方式

```ts
// 除类型之外的所有参数都是可选的
h('div')
h('div', { id: 'foo' })
 
//属性和属性都可以在道具中使用
//Vue会自动选择正确的分配方式
h('div', { class: 'bar', innerHTML: 'hello' })
 
// props modifiers such as .prop and .attr can be added
// with '.' and `^' prefixes respectively
h('div', { '.name': 'some-name', '^width': '100' })
 
// class 和 style 可以是对象或者数组
h('div', { class: [foo, { bar }], style: { color: 'red' } })
 
// 定义事件需要加on 如 onXxx
h('div', { onClick: () => {} })
 
// 子集可以字符串
h('div', { id: 'foo' }, 'hello')
 
//如果没有props是可以省略props 的
h('div', 'hello')
h('div', [h('span', 'hello')])
 
// 子数组可以包含混合的VNode和字符串
h('div', ['hello', h('span', 'hello')])
```

## 使用 props 传递参数

```ts
<template>
    <Btn text="按钮"></Btn>
</template>
  
<script setup lang='ts'>
import { h, } from 'vue';
type Props = {
    text: string
}
const Btn = (props: Props, ctx: any) => {
    return h('div', {
        class: 'p-2.5 text-white bg-green-500 rounded shadow-lg w-20 text-center inline m-1',
 
    }, props.text)
}
</script>
```

## 接受 emit

```ts
<template>
    <Btn @on-click="getNum" text="按钮"></Btn>
</template>
  
<script setup lang='ts'>
import { h, } from 'vue';
type Props = {
    text: string
}
const Btn = (props: Props, ctx: any) => {
    return h('div', {
        class: 'p-2.5 text-white bg-green-500 rounded shadow-lg w-20 text-center inline m-1',
        onClick: () => {//派发
            ctx.emit('on-click', 123)
        }
    }, props.text)
}
 
const getNum = (num: number) => {
    console.log(num);
}
</script>
```

## 定义插槽

```ts
<template>
    <Btn @on-click="getNum">
        <template #default>
            按钮slots
        </template>
    </Btn>
</template>
  
<script setup lang='ts'>
import { h, } from 'vue';
type Props = {
    text?: string
}
const Btn = (props: Props, ctx: any) => {
    return h('div', {
        class: 'p-2.5 text-white bg-green-500 rounded shadow-lg w-20 text-center inline m-1',
        onClick: () => {
            ctx.emit('on-click', 123)
        }
    }, 
             ctx.slots.default() )//在这里定义插槽
}
 
const getNum = (num: number) => {
    console.log(num);
}
</script>
```

## 挂载使用

```typescript
//使用createVNode
const div = createVNode('div',{id:"foo"},'小余')//声明使用

render(div,document.body)//挂载到页面上
console.log(div)//打印看看属性
```

# 第三十九章 — Vue开发桌面程序Electron

> [Electron](https://so.csdn.net/so/search?q=Electron&spm=1001.2101.3001.7020) 官网 [Electron | Build cross-platform desktop apps with JavaScript, HTML, and CSS.](https://www.electronjs.org/)
>
> 我们用的 [VsCode](https://so.csdn.net/so/search?q=VsCode&spm=1001.2101.3001.7020) 也是 electron 开发的
>
>  electron 内置了 Chromium 和 nodeJS 其中 Chromium 是渲染进程 主要渲染和解析 HTML，Nodejs 作为主进程，其中管道用 IPC 通信

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029024147767-1711983932539-86.png" alt="image-20221029024147767" style="zoom:50%;" />

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029024230035-1711983932539-88.png" alt="image-20221029024230035" style="zoom:67%;" />

## 1. 使用 vite 构建 electron 项目

> 创建一个 vite 项目

```JS
npm init vite@latest
```

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029024326972-1711983932539-89.png" alt="image-20221029024326972" style="zoom:50%;" />

> 安装 **electron**

```typescript
npm install electron -D
npm install vite-plugin-electron -D//vite插件
```

>  根目录新建 electron /index.ts

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029024413185-1711983932539-90.png" alt="image-20221029024413185" style="zoom:100%;" />

> 修改 vite.config.ts 配置文件
>
> 引入 electron 插件配置 main entry 对应 electron 的文件

```typescript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import electron from 'vite-plugin-electron'
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(), electron({
    main: {
      entry: "electron/index.ts"
    }
  })]
})
//上方代码是视频中的，现在已经无法使用，现在已经不能加上main了，否则会报错
//正确代码如下
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import electron from 'vite-plugin-electron'
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(), electron({
      entry: "electron/index.ts"
  })]
})
```

> 通过源码部分，我们可以看到确实没有main的选项了

```typescript
export interface Configuration {
  /**
   * Shortcut of `build.lib.entry`
   */
  entry?: import('vite').LibraryOptions['entry']//外面确实不需要再包一层
  vite?: import('vite').InlineConfig
  /**
   * Triggered when Vite is built every time.
   * 
   * If this `onstart` is passed, Electron App will not start automatically.  
   * However, you can start Electroo App via `startup` function.  
   */
  onstart?: (
    this: import('rollup').PluginContext,
    options: {
      /** Electron App startup function */
      startup: (args?: string[]) => Promise<void>
      /** Reload Electron-Renderer */
      reload: () => void
    },
  ) => void
}
```

![image-20221029024448813](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029024448813-1711983932539-92.png)

> 编写代码 electron /index.ts

```js
import { app, BrowserWindow } from 'electron'//app是应用程序，BrowserWindow用来控制打开应用的框
import path from 'path'
//app 控制应用程序的事件生命周期。
//BrowserWindow 创建并控制浏览器窗口。
 
let win: BrowserWindow | null;
//定义全局变量获取 窗口实例
 
const createWindow = () => {
    win = new BrowserWindow({
        //
        webPreferences: {
            devTools: true,
            contextIsolation: false,
            nodeIntegration: true
            //允许html页面上的javascipt代码访问nodejs 环境api代码的能力（与node集成的意思）
        }
    })
    if (app.isPackaged) {//打完包后应该去走静态文件
        win.loadFile(path.join(__dirname, "../dist/index.html"));
    } else {
//VITE_DEV_SERVER_HOST 如果是undefined 换成  VITE_DEV_SERVER_HOSTNAME
        win.loadURL(`http://${process.env['VITE_DEV_SERVER_HOST']}:${process.env['VITE_DEV_SERVER_PORT']}`)
        //win.loadURL放本地符的地址
    }
}
//isPackage 不好使换下面的
  //  if(process.env.NODE_ENV != 'development'){
      //  win.loadFile(path.join(__dirname, "../index.html"));
  //  }else{
        //win.loadURL(`http://${process.env['VITE_DEV_SERVER_HOSTNAME']}:${process.env['VITE_DEV_SE//RVER_PORT']}`)
   // }
//在Electron完成初始化时被触发
app.whenReady().then(createWindow)
```

> 配置 package json 增加 main 字段(指定入口文件)  	**type 去掉**
>
> 不需要做默认的导出了

```typescript
{
  "name": "electron-vite",
  "private": true,
  "version": "0.0.0",
  "main": "dist/electron/index.js",
      /我这里产生的路径跟小满不大一样，我的需要写为：  "main": "dist-electron/index.js",
  "scripts": {
    "dev": "vite",
    "build": "vue-tsc --noEmit && vite build  &&  electron-builder",
    "preview": "vite preview"
  },
  "dependencies": {
    "vue": "^3.2.37"
  },
  "devDependencies": {
    "@vitejs/plugin-vue": "^3.0.0",
    "electron": "^19.0.10",
    "electron-builder": "^23.1.0",
    "typescript": "^4.6.4",
    "vite": "^3.0.0",
    "vite-plugin-electron": "^0.8.3",
    "vue-tsc": "^0.38.4"
  }
}
```

> 通过npm run dev在终端进行启动

![image-20221029024620786](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029024620786-1711983932539-91.png)

> 上面是小满的效果，我的不知道为什么内容跟窗口分离了，就是白屏了，在后面会有讲解到，需要使用到Debugtron工具(自行下载)
>
> 问题贼多，由于我还有其他事情，目前也不写这个桌面程序，所以就看着，不继续踩坑了

![image-20221029145302924](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029145302924-1711983932539-96.png)

## 2. 打包 Electron

> 需要安装 electron-builder

```typescript
npm install electron-builder -D
```

> package json 配置 build 修改 npm run build 命令

```js
"build": "vue-tsc --noEmit && vite build  &&  electron-builder",
```

```js
  "build": {
    "appId": "com.electron.desktop",
    "productName": "electron",
    "asar": true,
    "copyright": "Copyright © 2022 electron",
    "directories": {
      "output": "release/"
    },
    "files": [
      "dist"
    ],
    "mac": {
      "artifactName": "${productName}_${version}.${ext}",
      "target": [
        "dmg"
      ]
    },
    "win": {
      "target": [
        {
          "target": "nsis",
          "arch": [
            "x64"
          ]
        }
      ],
      "artifactName": "${productName}_${version}.${ext}"
    },
    "nsis": {
      "oneClick": false,
      "perMachine": false,
      "allowToChangeInstallationDirectory": true,
      "deleteAppDataOnUninstall": false
    },
    "publish": [
      {
        "provider": "generic",
        "url": "http://127.0.0.1:8080"
      }
    ],
    "releaseInfo": {
      "releaseNotes": "版本更新的具体内容"
    }
  }
```

### nsis 配置详解 

> 应用程序安装的一个配置

```typescript
{"oneClick": false, // 创建一键安装程序还是辅助安装程序（默认是一键安装）
    "allowElevation": true, // 是否允许请求提升，如果为false，则用户必须使用提升的权限重新启动安装程序 （仅作用于辅助安装程序）
    "allowToChangeInstallationDirectory": true, // 是否允许修改安装目录 （仅作用于辅助安装程序）
    "installerIcon": "public/timg.ico",// 安装程序图标的路径
    "uninstallerIcon": "public/timg.ico",// 卸载程序图标的路径
    "installerHeader": "public/timg.ico", // 安装时头部图片路径（仅作用于辅助安装程序）
    "installerHeaderIcon": "public/timg.ico", // 安装时标题图标（进度条上方）的路径（仅作用于一键安装程序）
    "installerSidebar": "public/installerSiddebar.bmp", // 安装完毕界面图片的路径，图片后缀.bmp，尺寸164*314 （仅作用于辅助安装程序）
    "uninstallerSidebar": "public/uninstallerSiddebar.bmp", // 开始卸载界面图片的路径，图片后缀.bmp，尺寸164*314 （仅作用于辅助安装程序）
    "uninstallDisplayName": "${productName}${version}", // 控制面板中的卸载程序显示名称
    "createDesktopShortcut": true, // 是否创建桌面快捷方式
    "createStartMenuShortcut": true,// 是否创建开始菜单快捷方式
    "shortcutName": "SHom", // 用于快捷方式的名称，默认为应用程序名称
    "include": "script/installer.nsi",  // NSIS包含定制安装程序脚本的路径，安装过程中自行调用  (可用于写入注册表 开机自启动等操作)
    "script": "script/installer.nsi",  // 用于自定义安装程序的NSIS脚本的路径
    "deleteAppDataOnUninstall": false, // 是否在卸载时删除应用程序数据（仅作用于一键安装程序）
    "runAfterFinish": true,  // 完成后是否运行已安装的应用程序（对于辅助安装程序，应删除相应的复选框）
    "menuCategory": false, // 是否为开始菜单快捷方式和程序文件目录创建子菜单，如果为true，则使用公司名称
}
```

> npm run build 	进行打包

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029024754088-1711983932539-93.png" alt="image-20221029024754088" style="zoom:67%;" />

> 安装成功后的样子

![image-20221029024822686](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029024822686-1711983932539-94.png)

##  3.Electron Vscode 输出乱码解决 方案

> dev 的时候 加上 chcp 65001 输出中文

```typescript
 "dev": "chcp 65001 && vite",
```

![image-20221029024900534](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029024900534-1711983932539-99.png)

> 加上之后

![image-20221029024916311](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029024916311-1711983932539-98.png)

## 4. 渲染进程和主进程通信

> vite.config.ts 需要修改 不然会报一个错 Error: Module "path" has been externalized for browser compatibility. Cannot
>
> 只要安装了 vite-plugin-electron 就会带上 vite-plugin-electron-renderer 直接引入用就行

```js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import electron from 'vite-plugin-electron'
import electronRender from 'vite-plugin-electron-renderer'
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(), electron({
    main: {
      entry: "electron/index.ts"
    }
  }),electronRender()],
  build:{
    emptyOutDir: false,
  }
})
```

> 渲染进程使用 ipcRenderer 发送

```typescript
import { ipcRenderer } from 'electron'
 
const open = () => {
     ipcRenderer.send('openFlyCar')
}
```

> 主进程使用 ipcMain 接受

```js
ipcMain.on('openFlyCar',()=>{
    console.log('收到')
})
```

> 主进程通知渲染进程

```typescript
const  win = new BrowserWindow(xxxxx)
win!.webContents.send('load', { message: "electron初始化了" })
```

> 渲染进程接受

```typescript
ipcRenderer.on('load',(_,data)=>{
  console.log(data)
})
```

# 第四十章 — Vue响应性语法糖

> **小提示 本章内容所讲的东西都是实验性的产物 暂时不要再生产环境使用，自己开发玩可以使用，不过大体框架应该不会变了。**
>
> 要求 vue 版本 `3.2.25` 及以上	

## 1. 开启配置（开启之后才能使用新特性）

> vite 开启 reactivityTransform

```typescript
import { fileURLToPath, URL } from 'url'
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import vueJsx from '@vitejs/plugin-vue-jsx'
// https://vitejs.dev/config/
export default defineConfig({
  server: {
    port: 3000
  },
  plugins: [
    vue({
      reactivityTransform:true//如果是使用vite通过配置这个参数即可使用
    }),
   vueJsx()],
  resolve: {
    alias: {
      '@': fileURLToPath(new URL('./src', import.meta.url))
    }
  },
})
```

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029154223821-1711983932539-95.png" alt="image-20221029154223821" style="zoom:70%;" />

> 如果你是 vue-cli

```typescript
// vue.config.js
module.exports = {
  chainWebpack: (config) => {
    config.module
      .rule('vue')
      .use('vue-loader')
      .tap((options) => {
        return {
          ...options,
          reactivityTransform: true
        }
      })
  }
}
```

## 第一个例子 $ref

> 在之前 ref 修改值 和 获取值 都要.value 一下 感觉很繁琐，不想用.value 我们可以使用 vue3 的新特性 $ref 。
>
> 目前此特性处于实验性阶段
>
> **我们可以直接使用 $ref 宏函数 就不需要.value 了。能帮我们快速书写，但是宏函数是基于运行时的他最终还是会转换成 ref 加.value 只不过 vue 帮我们做了这个操作了**

```typescript
<template>
    <div>
        <button @click="add">每按一下，小满长高一厘米</button>
    </div>
    <h2>
        {{count}}
    </h2>
</template>
    
<script setup lang='ts'>
import { $ref } from 'vue/macros'
let count = $ref(0)
 
const add = () => {
   //传统ref => count.value++
    count++
}
</script>
    
<style>
</style>
```

> 当然跟 ref 有关的函数都做处理 都不需要.value 了
>
> - [ref](https://cn.vuejs.org/api/reactivity-core.html#ref) -> `$ref`
> - [computed](https://cn.vuejs.org/api/reactivity-core.html#computed) -> `$computed`
> - [shallowRef](https://cn.vuejs.org/api/reactivity-advanced.html#shallowref) -> `$shallowRef`
> - [customRef](https://cn.vuejs.org/api/reactivity-advanced.html#customref) -> `$customRef`
> - [toRef](https://cn.vuejs.org/api/reactivity-utilities.html#toref) -> `$toRef`

### 2.$ref 的弊端

> 因为他编译之后就是 count.value 并不是一个 ref 对象所以 watch 无法监听而且会抛出一个警告

```ts
//警告
[Vue warn]: Invalid watch source:  0 A watch source can only be a getter/effect function, a ref, a reactive object, or an array of these types. 
  at <App>
```

```typescript
<template>
</template>
    
<script setup lang='ts'>
import { reactive, ref, toRefs,watch } from 'vue';
import { $ref} from 'vue/macros'
 
let count = $ref<number>(0)
 
watch(count,(v)=>{
    console.log(v)
})
 
 
setInterval(()=>{
    count++
},1000)
 
 
</script>
    
<style>
</style>
```

> 解决这个问题需要 $$ 符号 就是再让他编译的时候变成一个 ref 对象不加.value 

```typescript
<template>
</template>
    
<script setup lang='ts'>
import { reactive, ref, toRefs,watch } from 'vue';
import { 。ef,$$ } from 'vue/macros'//声明$$
 
let count = $ref<number>(0)
 
watch($$(count),(v)=>{//经过变化，多加一个$
    console.log(v)
})
 
 
setInterval(()=>{
    count++
},1000)
 
 
</script>
    
<style>
</style>
```

### 3. 解构

> 在之前我们解构一个对象使用 toRefs 解构完成之后 获取值和修改值 还是需要.value
>
> vue3 也提供了 [语法糖](https://so.csdn.net/so/search?q=语法糖&spm=1001.2101.3001.7020) $() 解构完之后可以直接赋值
>
> Vue3包括对$computed，$shallowRef等等都做了响应式的$处理，直接引入进行使用就行了

```typescript
<template>
    <div>
        {{name}}
    </div>
</template>
    
<script setup lang='ts'>
import { reactive, toRefs } from 'vue'
import {$} from 'vue/macros'
const obj = reactive({
    name: '小满'
})
 
let { name } = $(obj);//在之前的时候直接解构是会丢失响应性的，但现在加上$后不会丢失响应性(新特性)
const { name } = toRefs(obj)//之前的写法，但这样写如果要取值的话，依旧需要.value(旧特性)
 
 
setTimeout(()=>{
   name = '大满'
},2000)
 
</script>
    
<style>
</style>
```

# 第四十二章 — 环境变量

> PS：你问我四十一章去哪了，我也不知道，去问小满
>
> **环境变量：他的主要作用就是让开发者区分不同的运行环境，来实现 兼容开发和生产**
>
> **例如 npm run dev 就是开发环境 npm run build 就是生产环境等等**
>
> Vite 在一个特殊的 **`import.meta.env`** 对象上暴露[环境变量](https://so.csdn.net/so/search?q=环境变量&spm=1001.2101.3001.7020)。这里有一些在所有情况下都可以使用的内建变量。想查看就通过控制台进行打印一下

```typescript
{
"BASE_URL":"/", //部署时的URL前缀
"MODE":"development", //运行模式
"DEV":true,"  //是否在dev环境
PROD":false, //是否是build 环境
"SSR":false //是否是SSR 服务端渲染模式
}
```

> **需要注意的一点就是这个环境变量不能使用动态赋值 import.meta.env [key] 应为这些环境变量在打包的时候是会被硬编码的通过 JSON.stringify 注入浏览器的**

## 配置额外的环境变量

> 在`根目录`新建 env 文件 可以创建多个
>
> 如下 env.[name]

![image-20221029165142677](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029165142677-1711983932539-97.png)

###  修改启动命令

> 在 package json 配置 --mode env 文件名称。修改后就能生效

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029165222746-1711983932539-103.png" alt="image-20221029165222746" style="zoom:67%;" />

###  配置智能提示

```typescript
interface ImportMetaEnv {
    VITE_XIAOMAN:string
}
```

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029165253714-1711983932539-100.png" alt="image-20221029165253714" style="zoom:67%;" />

### App输出

> 然后 App.vue 输出 JSON.stringify (import.meta.env)
>
> 就已经添加进去了

![image-20221029165331964](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029165331964-1711983932539-101.png)

### 生产环境使用

> 创建 .env.production 在执行 npm run build 的时候他会自己加载这个文件 
>
> `.env.production`是生产环境的名字
>
> 图中的VITE_XIAOMAN就是环境变量

![image-20221029165357165](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029165357165-1711983932539-102.png)

![image-20221029165409004](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029165409004-1711983932539-105.png)

### **如果想在 vite.config.ts 使用环境变量**

![image-20221029165428545](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029165428545-1711983932539-104.png)

```typescript
import { fileURLToPath, URL } from 'node:url'
 
import { defineConfig, loadEnv } from 'vite'
import vue from '@vitejs/plugin-vue'
import vueJsx from '@vitejs/plugin-vue-jsx'
 
 
 
// https://vitejs.dev/config/
export default ({mode}:any) => {
 
  console.log(loadEnv(mode,process.cwd()))
  
  return defineConfig({
    plugins: [vue(), vueJsx()],
    resolve: {
      alias: {
        '@': fileURLToPath(new URL('./src', import.meta.url))
      }
    }
  })
} 
```

> 我们就可以通过环境变量这个值 做一些事情比如 切换接口 url 等 

# 第四十三章 — webpack 构建Vue3项目

> **为什么要手写 webpack 不用 cli （脑子有病）并不是 其实是为了加深我们对 webpack 的了解方便以后灵活运用 webpack 的技术**

## 小白知识基础介绍

> 为什么多了这个环节呢，为了降低看小满视频的要求难度哈哈

| webpack                             | *静态模块打包器*，它有一个模块的概念，webpack 将所有文件都看作是模块，将这些模块分类并配置一定的规则，按照这些规则进行处理的过程，就是 webpack 的工作过程。 |
| ----------------------------------- | ------------------------------------------------------------ |
| mode                                | 告知 webpack 使用相应模式的内置优化。PS：webpack 4 之前是没有 mode 配置的 |
| entry                               | 指定入口文件，也可以是个数组，放入多个入口文件               |
| outpub                              | 输出，可以控制 webpack 如何向硬盘写入编译文件                |
| entry                               | 目录中的 main.js 是 webpack 的入口文件，什么是入口文件？ 入口文件，是为了让 webpack 知道我们项目使用了哪些模块，以及这些模块之间的依赖关系，即作为 webpack 内部依赖图的开始。 |
| module                              | 配置文件中的 模块 用于处理项目中不同类型的模块。             |
| module.rules                        | **module 中的主要属性 rules**<br>创建模块时，匹配请求的规则数组 每个规则又有很多属性，这里我们主要用到了 `test` 和 `use`<br>test：条件，即通过条件去匹配需要处理的文件 use：在规则条件匹配时使用，每个 use 指定使用一个 loader，loader 用于对模块的源代码进行转换 |
| loader                              | 预处理器                                                     |
| plugins                             | 插件，是用来扩展 webpack 功能的，它们会在整个构建过程中生效，执行相关的任务。 很多时候，Loaders 和 Plugins 很容易被搞混，其实它们是完全不同的东西，loaders 是在打包构建工程中用来处理源文件的，一次处理一个；插件不会直接操作单个文件，而直接对整个构建过程起作用。 |
| htmlWebpackPlugin<br>中会用到的参数 | filename：生成的文件名称<br> template：本地模板文件位置，生成的.html 文件以指定的文件为模板<br> inject: 引入的外部资源，插入到 html 中的位置 |

## 正式环节

**初始化项目结构**

> 要跟 cli 结构保持一致

**初始化结构第一步**：

| **public** | **静态资源**                       |
| ---------- | ---------------------------------- |
| **src**    | **源码(也就是平时编写代码的地方)** |

### 创建在最外层的必要文件

| 文件          | 作用                                                         | 命令        |
| ------------- | ------------------------------------------------------------ | ----------- |
| package.json  | 依赖·编译配置文件                                            | npm init -y |
| tsconfig.json | typescript 的语法识别和执行配置文件 , 也是 ts 向 js 转化的中间站 | tsc --init  |

### 在src下初始化各个必要文件

> 没有`tsc` 可以使用命令`npm install -g typescript`(使用npm命令全局安装typescript)
>
> 如果你使用webstorm创建不了App.vue文件是正常的，因为你还没有装vue(位于视频7分30秒)，使用命令`pnpm add vue`
>
> 此时的时间是2022年11月19号，Vue的版本是3.2.45

| 文件           | 作用               | 命令     |
| -------------- | ------------------ | -------- |
| assets(文件夹) | webpack打包的资源  | 直接创建 |
| views(文件夹)  | 视图页面           | 直接创建 |
| App.vue(文件)  | 根组件             | 直接创建 |
| main.ts(文件)  | 入口文件(配置文件) | 直接创建 |

### 在public下初始化必要文件

| 文件       | 作用                            |
| ---------- | ------------------------------- |
| index.html | webpack向外展示的门面(静态资源) |

### Webpack.config.js配置

> 首先，你得先创建一个名叫Webpack.config.js的文件，文件创建在最外层
>
> 这个文件是`webpack 配置文件`，基于node环境
>
> 在使用pnpm的时候，我显示版本过低，官方更新命令：pnpm add -g pnpm报错
>
> 根据提示使用pnpm setup生成pnpm-lock.yaml文件

| 命令                                     | 作用                                            |
| ---------------------------------------- | ----------------------------------------------- |
| pnpm add webpack(生成文件pnpm-lock.yaml) | 安装webpack的依赖文件，存放在package.json文件下 |
| pnpm add webpack-cli                     | 跟`pnpm add webpack`配套的命令(版本大于3必要)   |
| pnpm add webpack-dev-server              | 安装webpack的启动服务server                     |
| pnpm add html-webpack-plugin             | 安装webpack需要的插件(html模板)                 |

**安装完效果演示**

> 要粘贴出去使用记得把注释去掉，不然报错

```ts
{
  "name": "webpack",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "webpack-dev-server",//启动开发环境，这两个命令(dev和build)是在视频6分钟后加上去的
    "build": "webpack"//打包环境
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {//这下面四个就是我们安装出来的
    "html-webpack-plugin": "^5.5.0",
    "webpack": "^5.75.0",
    "webpack-cli": "^5.0.0",
    "webpack-dev-server": "^4.11.1"
  }
}
```

### 配置webpack.config.js文件

```js
//引入智能提示，ts的功能
const {Configuration} = require('webpack')
//引入path
const path = require('path')
//引入webpack需要的插件html-webpack-plugin
const htmlWebpackPlugin = require('html-webpack-plugin')
//做出智能提示的，记得要先引入智能提示
/**
 * @type{Configuration}
 * */

const config = {
    //配置mode，不配置在打包的时候会报错，这个命令是不压缩的，想要压缩使用production
    mode:"development",
    //配置入口文件
    entry: "./src/main.ts",
    //出口文件：打完包放到哪
    output: {
        filename: "[hash].js",//打完包的文件名，跟coi一样使用哈希加js
        path:path.resolve(__dirname,'dist')//输出位置，输出到根目录下的dist
    },

    plugins: [//plugins是一个数组
        new htmlWebpackPlugin({
            template: "./public/index.html"//将模板指定位置
        })
    ]
};

module.exports = config
```

### 进行打包

> 打包前在main.ts中随便写点东西
>
> 我写的是：const a = "小满没裤子"

- 然后使用命令npm run build进行打包
- 生成一个dist文件夹，里面有两个文件：`index.html`跟`b95a2e17e9dc17438061.js`，这个js文件名是随机的，产生的命名不一样无所谓

> 让我们看看打包出来的js文件长什么样子，中间那句话非常显眼对吧哈哈

```js
/*
 * ATTENTION: The "eval" devtool has been used (maybe by default in mode: "development").
 * This devtool is neither made for production nor for readable output files.
 * It uses "eval()" calls to create a separate source file in the browser devtools.
 * If you are trying to read the output file, select a different devtool (https://webpack.js.org/configuration/devtool/)
 * or disable the default devtool with "devtool: false".
 * If you are looking for production-ready output files, see mode: "production" (https://webpack.js.org/configuration/mode/).
 */
/******/ (() => { // webpackBootstrap
/******/ 	var __webpack_modules__ = ({

/***/ "./src/main.ts":
/*!*********************!*\
  !*** ./src/main.ts ***!
  \*********************/
/***/ (() => {

eval("const a = \"小满没裤子\"\n\n//# sourceURL=webpack://webpack/./src/main.ts?");

/***/ })

/******/ 	});
/************************************************************************/
/******/ 	
/******/ 	// startup
/******/ 	// Load entry module and return exports
/******/ 	// This entry module can't be inlined because the eval devtool is used.
/******/ 	var __webpack_exports__ = {};
/******/ 	__webpack_modules__["./src/main.ts"]();
/******/ 	
/******/ })()
;
```

### 配置main.ts文件

```ts
const a = "小满没裤子"//刚刚随便写的
//引入Vue
import {createApp} from "vue";
//引入根组件
import App from "./App.vue"
const app = createApp(App)//接收根组件，也就是App.vue,记得把根组件引入一下
app.mount('#app');//进行挂载，挂载的这个#app是id上面的app，在public文件夹下的index能够看到(前提记得去配置一下)

----------------------------------------------------
public下的index中应该配置的东西
<div id="app"></div>
```

### 二次打包

> 经过一些操作，我们决定重新打包看看效果，此时打包会报错，错误如下

```js
PS D:\Desktop\Project\Webpack> npm run build

> webpack@1.0.0 build
> webpack

(node:15044) [DEP_WEBPACK_TEMPLATE_PATH_PLUGIN_REPLACE_PATH_VARIABLES_HASH] DeprecationWarning: [hash] is now [fullhash] (also consider using [chunkhash] or [contenthash], see documentation for details)
(Use `node --trace-deprecation ...` to show where the warning was created)
assets by status 518 KiB [cached] 1 asset
asset index.html 216 bytes [compared for emit]
runtime modules 1.13 KiB 5 modules
cacheable modules 432 KiB
  modules by path ./node_modules/.pnpm/ 431 KiB
    ./node_modules/.pnpm/registry.npmmirror.com+vue@3.2.45/node_modules/vue/dist/vue.runtime.esm-bundler.js 597 bytes [built] [code generated]
    ./node_modules/.pnpm/registry.npmmirror.com+@vue+runtime-dom@3.2.45/node_modules/@vue/runtime-dom/dist/runtime-dom.esm-bundler.js 58.9 KiB [built] [code generated]
    ./node_modules/.pnpm/registry.npmmirror.com+@vue+runtime-core@3.2.45/node_modules/@vue/runtime-core/dist/runtime-core.esm-bundler.js 308 KiB [built] [code generated]
    ./node_modules/.pnpm/registry.npmmirror.com+@vue+reactivity@3.2.45/node_modules/@vue/reactivity/dist/reactivity.esm-bundler.js 41.4 KiB [built] [code generated]
    ./node_modules/.pnpm/registry.npmmirror.com+@vue+shared@3.2.45/node_modules/@vue/shared/dist/shared.esm-bundler.js 22.8 KiB [built] [code generated]
  modules by path ./src/ 475 bytes
    ./src/main.ts 361 bytes [built] [code generated]
    ./src/App.vue 114 bytes [built] [code generated] [1 error]

ERROR in ./src/App.vue 1:0
Module parse failed: Unexpected token (1:0)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
> <template>   //错误在这里，他不认识template，这个时候就需要安装两个loader，让他去帮我们解析这个东西
| 
| </template>
 @ ./src/main.ts 5:0-27 6:22-25

webpack 5.75.0 compiled with 1 error in 357 ms

```

#### 两个loder(载入程序)

```js
pnpm add vue-loader@next
pnpm add @vue/compiler-sfc//解析Vue文件的
```

> 安装完就能去解析模板了
>
> 此时就需要再去webpack.json中去配置一下
>
> ```ts
> const { VueLoaderPlugin } = require('vue-loader/dist/index');//引入loader配置
> //然后在plugin中new出来一个配置
> 
>     plugins: [//plugins是一个数组
>         new htmlWebpackPlugin({
>             template: "./public/index.html"//将模板指定位置
>         }),
>         new VueLoaderPlugin()//新增内容在这
>     ]
> //还要进行module配置
>     //module
>     module: {
>         rules: [
>             {
>                 test:/\.vue$/,//解析后缀为vue的正则
>                 use:"vue-loader"//这个配置就能帮我们去解析App.vue
>             }
>         ]
>     }
> ```
>
> 进行完上面两步就能进行打包了，所以准备正式开始我们第二次打包吧！

**成功打包**，信息如下(这个是在App.vue未加上ts功能情况下才能成功打包，如果在根组件下lang="ts"，会打包失败，因为还没配置ts的东西)

```js
PS D:\Desktop\Project\Webpack> npm run build

> webpack@1.0.0 build
> webpack

(node:8524) [DEP_WEBPACK_TEMPLATE_PATH_PLUGIN_REPLACE_PATH_VARIABLES_HASH] DeprecationWarning: [hash] is now [fullhash] (also consider using [chunkhash] or [contenthash], see documentation for details)
(Use `node --trace-deprecation ...` to show where the warning was created)
asset 26b6411917086593961e.js 527 KiB [emitted] [immutable] (name: main)
asset index.html 216 bytes [emitted] [compared for emit]
runtime modules 891 bytes 4 modules
cacheable modules 434 KiB
  modules by path ./src/ 2.08 KiB
    ./src/main.ts 361 bytes [built] [code generated]
    ./src/App.vue 842 bytes [built] [code generated]
    ./src/App.vue?vue&type=template&id=7ba5bd90 371 bytes [built] [code generated]
    ./src/App.vue?vue&type=script&lang=js 426 bytes [built] [code generated]
    + 2 modules
  modules by path ./node_modules/.pnpm/ 432 KiB
    ./node_modules/.pnpm/registry.npmmirror.com+vue@3.2.45/node_modules/vue/dist/vue.runtime.esm-bundler.js 597 bytes [built] [code generated]
    ./node_modules/.pnpm/registry.npmmirror.com+@vue+runtime-dom@3.2.45/node_modules/@vue/runtime-dom/dist/runtime-dom.esm-bundler.js 58.9 KiB [built] [code generated]
    ./node_modules/.pnpm/registry.npmmirror.com+@vue+runtime-core@3.2.45/node_modules/@vue/runtime-core/dist/runtime-core.esm-bundler.js 308 KiB [built] [code generated]
    ./node_modules/.pnpm/registry.npmmirror.com+vue-loader@17.0.1_ceoavo7i67lvjzxxtqa4yhrx7m/node_modules/vue-loader/dist/exportHelper.js 328 bytes [built] [code generated]
    ./node_modules/.pnpm/registry.npmmirror.com+@vue+reactivity@3.2.45/node_modules/@vue/reactivity/dist/reactivity.esm-bundler.js 41.4 KiB [built] [code generated]
    ./node_modules/.pnpm/registry.npmmirror.com+@vue+shared@3.2.45/node_modules/@vue/shared/dist/shared.esm-bundler.js 22.8 KiB [built] [code generated]
webpack 5.75.0 compiled successfully in 384 ms
```

> dist文件夹下的东西只会随着我们不断打包而越来越多，此时我们就需要引入一个包来让我们每次打包都将之前的内容清除掉

```ts
pnpm add clean-webpack-plugin//这个就是那个每次 打包都将dist文件夹之前的内容清除掉 的包
//因为这也是一个plugin，所以要继续在webpack.config.js中继续注册就行了

    plugins: [//plugins是一个数组
        new htmlWebpackPlugin({
            template: "./public/index.html"//将模板指定位置
        }),
        new VueLoaderPlugin(),
        new cleanWebpackPlugin()//新增加的东西
    ]
};
```

在App.vue中输出点东西，然后重新打包，我们打开dist下的index.html文件

![image-20221118041311032](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221118041311032-1711983932539-106.png)

发现是没有问题的

![image-20221118041336382](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221118041336382-1711983932539-109.png)

> 以上是打包后的效果，让我们使用npm run dev启动项目试一试
>
> 正常启动，莫得问题，从路径中我们能发现他们的不同之处

![image-20221118041528445](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221118041528445-1711983932539-107.png)

>  本地启动项目是支持热更新的，也就是你编辑器里的东西一改，页面同步进行改动

![image-20221118041507642](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221118041507642-1711983932539-108.png)

## 强化Webpack功能

- 在assets下创建index.css文件夹

  ```css
  body,html{
      height: 100%;
      background: red;
  }
  ```

- 在webpack.config.js下进行配置别名

  ```js
      resolve: {
          alias: {//配置别名
              '@':path.resolve(__dirname,'src')//这是别名，用@来替代src
          },
          extensions: [//自动识别后缀，然后跟随过去
              '.vue','.ts','.js'
          ]
      }
  ```

- 在App.vue中进行引入

  ```ts
  import '@/assest/index.css'//@ 是别名
  ```

- 然后启动，会报错，错误信息如下

  ```ts
  Compiled with problems:X
  
  ERROR in ./src/App.vue?vue&type=script&lang=js (./node_modules/.pnpm/registry.npmmirror.com+vue-loader@17.0.1_ceoavo7i67lvjzxxtqa4yhrx7m/node_modules/vue-loader/dist/index.js??ruleSet[1].rules[2].use[0]!./src/App.vue?vue&type=script&lang=js) 2:0-27
  
  Module not found: Error: Can't resolve '@/assest/index.css' in 'D:\Desktop\Project\Webpack\src'
  ```

- 需要安装插件

  - `pnpm add css-loader`，作用是帮我们解析CSS文件
  - `pnpm add style-loader `，作用是帮我们解析CSS的样式的
  - 安装完需要去webpack.json.js下的rules进行一个配置

  ```ts
      module: {
          rules: [
              {
                  test:/\.vue$/,//解析后缀为vue的
                  use:"vue-loader"//这个配置就能帮我们去解析App.vue
              },
              {//新配置在这里
                  test:/\.css$/,//解析css
                  use:["style-loader","css-loader"]//使用这两个loader
              }
          ]
      },
  ```

  - 重新运行项目，然后就可以了(这里记得css文件不要放到views文件夹去，他们两个挨太近了，我给放错位置，报错显示找不到哈哈)

    <img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221118053037140-1711983932539-110.png" alt="image-20221118053037140" style="zoom:67%;" />

  ![image-20221118052907487](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221118052907487-1711983932539-111.png)

  > 额外内容，你也可以安装less进行使用
  >
  > | 一条路服务                     | 命令                                    |
  > | ------------------------------ | --------------------------------------- |
  > | 安装less                       | pnpm add less                           |
  > | 安装解析less文件的配置         | pnpm add less-loader                    |
  > | 不需要安装style-loader这个东西 | 不用安装我就不写了，记得去rules配置一下 |
  >
  > ```ts
  >             {
  >                 test: /\.less$/, //解析 less
  >                 use: ["style-loader", "css-loader", "less-loader"],
  >             },
  > ```

- 修改完配置项，记得重启重新运行项目
- 然后我们就可以将assets下的index.css修改成index.less，然后在App.vue引入的css后缀也要修改一下

### 配置 vue 声明文件不然 ts 识别不了 vue 后缀

> 我使用webstorm未出现这种情况(可能内置了)，如果你跟小满一样使用vscode可能会发生，声明文件我也贴在这里

```js
 declare module "*.vue" {
    import { DefineComponent } from "vue"
    const component: DefineComponent<{}, {}, any>
    export default component
  }
```

### 配置适应typescript

> 时间点位于视频的19分12秒

```ts
pnpm add typescript
pnpm add ts-loader//两条命令
```

- 然后继续在rules这里配置加上去

  ```ts
  {
      test: /\.ts$/,  //解析ts
      loader: "ts-loader",
      options: {//多了一个options是因为ts-loader不能够直接使用，要对vue单文件做一些处理
          configFile: path.resolve(process.cwd(), 'tsconfig.json'),
          appendTsSuffixTo: [/\.vue$/]
      },
  }
  ```

- 小满他是这样就运行起来了，我报错了(见鬼)，错误如下：

  ```ts
  Cannot find module './App.vue' or its corresponding type declarations.
  //报错原因：typescript 只能理解 .ts 文件，无法理解 .vue 文件
  ```

- 解决方式如下：

  - 在项目根目录或 src 文件夹下创建一个名为 shims-vue.d.ts 的文件，并写入以下内容(跟上面没法识别Vue一样的操作，声明文件，声明的东西不同而已)

  - ```ts
    declare module '*.vue' {
      import { ComponentOptions } from 'vue'
      const componentOptions: ComponentOptions
      export default componentOptions
    }
    ```

- 成功解决问题后，我们尝试一下在App.vue中写下TS代码尝试效果

  ```ts
  <template>
    <div>hello 班花姐姐</div>
    <h1>{{tom}}</h1>
  </template>
  
  <script setup lang="ts">
  import '@/assest/index.css'//没错我没有安装less哈哈，你安装了的话自己改一下
  import {ref} from "vue";
  const tom = ref<string>('班花姐姐永远18岁')
  </script>
  
  <style scoped>
  
  </style>
  ```

  效果展示(没有问题，一切正常)：

  ![image-20221118060659343](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221118060659343-1711983932539-112.png)

### webpack美化插件

```ts
//命令
pnpm add friendly-errors-webpack-plugin
//引入，在webpack.config.js中引入
const FriendlyErrorsWebpackPlugin = require("friendly-errors-webpack-plugin");//变量名称意思：友好的webpack错误插件提示
//使用，在plugin中new一个
        new FriendlyErrorsWebpackPlugin({
            compilationSuccessInfo:{ //美化样式
                messages:['班花姐姐欢迎你访问： http://localhost:8080']//这里是提示，为了明显一点，这里整个活
            }
        })
---//以上是美化插件的，我们还需要将原本那一坨清空掉，需要配置一个与plugin同级的配置
stats: "errors-only",
```

> 小提示：rules下的基本上都是处理文件的，plugins下的基本上都是插件

让我们来看看实际效果(非常不错)：

![image-20221118061813271](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221118061813271-1711983932540-117.png)

### devServer

```ts
    devServer: {
        proxy: {},//代理跨域
        port: 9001,//修改端口的
        hot: true,//热更新
        open: true,//打开网页
    },
```

### externals

> 优化打包文件的，大大节省空间
>
> 这里的数据对比来自小满，我就没搞了
>
> 优化前：823KB	优化后：46.9KB
>
> 少了那么多内容都去哪里了，其实就是vue的源码消失了，变成了CDN引入了，大大节省空间

```ts
    externals: {
        vue: "Vue" //CDN 引入，Vue不是vue，V大写
    },
```

- 缺陷：CDN是网络请求，需要保持网络通畅，打开网页会先请求CDN，如果网络不好，会白屏很长时间
- 优点：减少服务器压力，打包体积大幅度降低

### 总结配置环节

#### webpack.json.js文件

```ts
//引入智能提示，ts的功能
const {Configuration} = require('webpack')
//引入path
const path = require('path')
//引入webpack需要的插件html-webpack-plugin
const htmlWebpackPlugin = require('html-webpack-plugin')
//引入loader配置
const { VueLoaderPlugin } = require('vue-loader/dist/index');
//引入clean-webpack-plugin
const { CleanWebpackPlugin } = require('clean-webpack-plugin')
//引入webpack美化样式
const FriendlyErrorsWebpackPlugin = require("friendly-errors-webpack-plugin");
//做出智能提示的，记得要先引入
/**
 * @type{Configuration}
 * */

const config = {
    //配置mode，不配置在打包的时候会报错，这个命令是不压缩的，想要压缩使用production
    mode:"development",
    //配置入口文件
    entry: "./src/main.ts",
    //module
    module: {
        rules: [
            {
                test:/\.vue$/,//解析后缀为vue的
                use:"vue-loader"//这个配置就能帮我们去解析App.vue
            },
            {
                test:/\.css$/,//解析css
                use:["style-loader","css-loader"]
            },
            {
                test: /\.ts$/,  //解析ts
                loader: "ts-loader",
                options: {
                    configFile: path.resolve(process.cwd(), 'tsconfig.json'),
                    appendTsSuffixTo: [/\.vue$/]
                }
            }
        ]
    },
    //出口文件：打完包放到哪
    output: {
        filename: "[hash].js",//打完包的文件名，跟coi一样使用哈希加js
        path:path.resolve(__dirname,'dist')//输出位置，输出到根目录下的dist
    },
    resolve: {
        alias: {//配置别名
            '@':path.resolve(__dirname,'src')//这是别名，用@来替代src
        },
        extensions: [//自动识别后缀，然后跟随过去
            '.vue','.ts','.js'
        ]
    },
    stats: "errors-only",//去掉没用的提示
    plugins: [//plugins是一个数组
        new htmlWebpackPlugin({
            template: "./public/index.html"//将模板指定位置
        }),
        new VueLoaderPlugin(),
        new CleanWebpackPlugin(),
        new FriendlyErrorsWebpackPlugin({
            compilationSuccessInfo:{ //美化样式
                messages:['班花姐姐欢迎你访问： http://localhost:8080']//这里是提示
            }
        })
    ]
};

module.exports = config//将config暴露出去
```

#### main.ts文件

```ts
//引入Vue
import {createApp} from "vue";
//引入根组件
import App from "./App.vue"
const app = createApp(App)//接收根组件，也就是App.vue,记得把根组件引入一下
app.mount('#app');//进行挂载，挂载的这个#app是id上面的app，在public文件夹下的index能够看到(前提记得去配置一下)
```

#### shims-vue.d.ts声明文件

```ts
//TS的声明文件
declare module '*.vue' {
    import { ComponentOptions } from 'vue'
    const componentOptions: ComponentOptions
    export default componentOptions
}
//vue的声明文件
declare module "*.vue" {
    import { DefineComponent } from "vue"
    const component: DefineComponent<{}, {}, any>
    export default component
  }
```

#### package.json文件

```ts
{
    "name": "webpack-vue",
    "version": "1.0.0",
    "description": "",
    "main": "webpack.config.js",
    "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1",
        "dev": "webpack-dev-server",
        "build": "webpack"
    },
    "keywords": [],
    "author": "",
    "license": "ISC",
    "dependencies": {
        "@vue/compiler-sfc": "^3.2.38", //解析vue文件
        "clean-webpack-plugin": "^4.0.0", //打包 的时候清空dist
        "css-loader": "^6.7.1", //处理css文件
        "friendly-errors-webpack-plugin": "^1.7.0", //美化dev
        "html-webpack-plugin": "^5.5.0", //html 模板
        "less": "^4.1.3",  //处理less
        "less-loader": "^11.0.0", //处理less文件
        "style-loader": "^3.3.1", //处理style样式
        "ts-loader": "^9.3.1", //处理ts
        "typescript": "^4.8.2", //ts
        "vue": "^3.2.38", //vue
        "vue-loader": "^17.0.0", //解析vue
        "webpack": "^5.74.0",
        "webpack-cli": "^4.10.0",
        "webpack-dev-server": "^4.10.0"
    }
```



## 彩蛋环节

> 班花姐姐要征婚啦，我笔记也打个广告好啦，把小满这个彩蛋环节也加入进来(此时小满视频时间9月1号，笔记时间11月19号)，**请注意时效性**，想必大家通过上面例子已经对班花姐姐不陌生了，想了解详细内容请加小满微信`a1195566313`

班花姐姐照片以及猫咪照片如下(是跟小满视频里面的不一样的图片哦)

猫咪照片：

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221120192059771-1711983932539-114.png" alt="image-20221120192059771" style="zoom:30%;" /><img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20221120192118071.png" alt="image-20221120192118071" style="zoom:40%;" /> <img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20221120230622097.png" alt="image-20221120230622097" style="zoom:40%;" /><img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20221120230634700.png" alt="image-20221120230634700" style="zoom:40%;" />

班花姐姐照片如下：

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20221120192236922.png" alt="image-20221120192236922" style="zoom:33%;" /><img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221120192247449-1711983932539-113.png" alt="image-20221120192247449" style="zoom:33%;" />

要求如下：

1. 有腹肌
2. 收入稳定
3. 最好离得近(河北)
4. 班花姐姐带着一只猫，猫很好看(怎么感觉像古代大小姐带着丫鬟一样ya哈哈)

# 第四十四章 — Vue3高级性能优化

> ### 性能优化
>
> 我们可以使用谷歌浏览器自带的 DevTools 进行性能分析 LightHouse

![image-20221029171146467](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029171146467-1711983932539-115.png)

![image-20221029171227572](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029171227572-1711983932540-118.png)

##  参数介绍

> 从 Performance 页的表现结果来看，得分 37 分，并提供了很多的时间信息，我们来解释下这些选项代表的意思：

| FCP (First Contentful Paint)       | 首次内容绘制的时间，浏览器第一次绘制 DOM 相关的内容，也是用户第一次看到页面内容的时间 |
| ---------------------------------- | ------------------------------------------------------------ |
| **Speed Index**                    | **页面各个可见部分的显示平均时间，当我们的页面上存在轮播图或者需要从后端获取内容加载时，这个数据会被影响到。** |
| **LCP (Largest Contentful Paint)** | **最大内容绘制时间，页面最大的元素绘制完成的时间**           |
| **TTI（Time to Interactive）**     | **从页面开始渲染到用户可以与页面进行交互的时间，内容必须渲染完毕，交互元素绑定的事件已经注册完成** |
| **TBT（Total Blocking Time）**     | **记录了首次内容绘制到用户可交互之间的时间，这段时间内，主进程被阻塞，会阻碍用户的交互，页面点击无反应** |
| **CLS（Cumulative Layout Shift）** | **计算布局偏移值得分，会比较两次渲染帧的内容偏移情况，可能导致用户想点击 A 按钮，但下一帧中，A 按钮被挤到旁边，导致用户实际点击了 B 按钮** |

##  代码分析

> 由于我们使用的是 vite vite 打包是基于 rollup 的我们可以使用 rollup 的插件

```typescript
npm install rollup-plugin-visualizer
```

> vite.config.ts 配置 记得设置 open 不然无效

```typescript
import { visualizer } from 'rollup-plugin-visualizer';
plugins: [vue(), vueJsx(),visualizer({
      open:true//这个是一定要打开的，不然会无效
 })],
```

> 然后进行 npm run build 打包

![image-20221029171552764](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029171552764-1711983932539-116.png)

> 项目中使用了 Ant Design 发现 这个 UI 库非常庞大 这时候 就可以使用 Ant Design 的按需引入减少 包大小

## Vite 配置优化

```typescript
build:{
       chunkSizeWarningLimit:2000,
       cssCodeSplit:true, //css 拆分
       sourcemap:false, //不生成sourcemap
       minify:false, //是否禁用最小化混淆，esbuild打包速度最快，terser打包体积最小。
       assetsInlineLimit:5000 //小于该值 图片将打包成Base64 ，Base64的就不会被打包加载到容器里
},
```

## PWA 离线存储技术

```typescript
npm install vite-plugin-pwa -D
```

```typescript
import { VitePWA } from 'vite-plugin-pwa' 
plugins: [vue(),VitePWA(), vueJsx(),visualizer({
      open:true
})],
```

> PWA 技术的出现就是让 web 网页无限接近于 Native 应用
>
> 1. 可以添加到主屏幕，利用 manifest 实现
> 2. 可以实现离线缓存，利用 service worker 实现
> 3. 可以发送通知，利用 service worker 实现

```typescript
VitePWA({
      workbox:{
          cacheId:"XIaoman",//缓存名称
          runtimeCaching:[
            {
              urlPattern:/.*\.js.*/, //缓存文件
              handler:"StaleWhileRevalidate", //重新验证时失效
              options:{
                cacheName:"XiaoMan-js", //缓存js，名称
                expiration:{
                  maxEntries:30, //缓存文件数量 LRU算法(优先缓存活跃的)
                  maxAgeSeconds:30 * 24 * 60 * 60 //缓存有效期
 
                }
              }
            }
          ]
      },
    })
```

> 进行 npm run build 打包会生成 sw.js

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029171732821-1711983932540-119.png" alt="image-20221029171732821" style="zoom:67%;" />

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029171744045-1711983932540-120.png" alt="image-20221029171744045" style="zoom:67%;" />

##  其他性能优化

### 图片懒加载 

> import lazyPlugin from 'vue3-lazy'
>
> <img v-lazy="user.avatar" >

### 虚拟列表

> 可以承受住好几万条数据

![image-20221029171836454](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029171836454-1711983932540-122.png)

>  多线程 使用 new Worker 创建
>
> worker 脚本与主进程的脚本必须遵守同源限制。他们所在的路径协议、域名、端口号三者需要相同

```typescript
const myWorker1 = new Worker("./calcBox.js");//后台运行，不会影响主进程
```

> 都使用 postMessage 发送消息

```typescript
worker.postMessage(arrayBuffer, [arrayBuffer]);
```

> 都使用 onmessage 接收消息

```typescript
self.onmessage = function (e) {
 // xxx这里是worker脚本的内容
};//这些都是不允许去操作DOM的
```

> 关闭

```typescript
worker.terminate();    
```

> VueUse 库已经集成了 webWorker

![image-20221029172012528](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029172012528-1711983932540-121.png)

### 防抖节流

> 同样 VueUse 也是集成了

![image-20221029172042033](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029172042590-1711983932540-123.png)

# 第四十五章 — Web Components

> 什么是 Web Components
>
> **Web Components 提供了基于原生支持的、对视图层的封装能力，可以让单个组件相关的 javaScript、css、html 模板运行在以 html 标签为界限的局部环境中，不会影响到全局，组件间也不会相互影响** **。** 
>
> **再简单来说：就是提供了我们自定义标签的能力，并且提供了标签内完整的生命周期** **。**

![image-20221029174408114](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029174408114-1711983932540-124.png)

> **Custom elements（自定义元素）**：JavaScript [API](https://so.csdn.net/so/search?q=API&spm=1001.2101.3001.7020)，允许定义 custom elements 及其行为，然后可以在我们的用户界面中按照需要使用它们。
>
> **Shadow DOM（影子 DOM）：**JavaScript API，用于将封装的 “影子” DOM 树附加到元素（与主文档 DOM 分开呈现）并控制其关联的功能。通过这种方式，开发者可以保持元素的功能私有，这样它们就可以被脚本化和样式化，而不用担心与文档的其他部分发生冲突(隔离样式)。
>
> **HTML templates（HTML 模板）：**和元素使开发者可以编写与 HTML 结构类似的组件和样式。然后它们可以作为自定义元素结构的基础被多次重用。
>
> 京东的跨端框架 Taro 的组件部分，就是用基于 Web Components 开发的 

## 1. 实战案例

```typescript
class Btn extends HTMLElement {
    constructor () {
        //调用super 来建立正确的原型链继承关系
        super()
        const p = this.h('p')
        p.innerText = '小满'
        p.setAttribute('style','height:200px;width:200px;border:1px solid #ccc;background:yellow')
        //表示 shadow DOM 子树的根节点。
        const shaDow = this.attachShadow({mode:"open"})
 
        shaDow.appendChild(this.p)
    }
 
    h (el) {
       return  document.createElement(el)
    }
 
    /**
     * 生命周期
     */
    //当自定义元素第一次被连接到文档 DOM 时被调用。
    connectedCallback () {
        console.log('我已经插入了！！！嗷呜')
    }
 
    //当自定义元素与文档 DOM 断开连接时被调用。
    disconnectedCallback () {
        console.log('我已经断开了！！！嗷呜')
    }
 
    //当自定义元素被移动到新文档时被调用
    adoptedCallback () {
        console.log('我被移动了！！！嗷呜')
    }
    //当自定义元素的一个属性被增加、移除或更改时被调用
    attributeChangedCallback () {
        console.log('我被改变了！！！嗷呜')
    }
 
}
 
window.customElements.define('xiao-man',Btn)//挂载，第一个随便取，第二个是放类名
```

## 2.template 模式

```typescript
class Btn extends HTMLElement {
    constructor() {
        //调用super 来建立正确的原型链继承关系
        super()
        const template = this.h('template')
        template.innerHTML = `
        <div>小满</div>
        <style>
            div{
                height:200px;
                width:200px;
                background:blue;
            }
        </style>
        `
        //表示 shadow DOM 子树的根节点。
        const shaDow = this.attachShadow({ mode: "open" })
 
        shaDow.appendChild(template.content.cloneNode(true))
    }
 
    h(el) {
        return document.createElement(el)
    }
 
    /**
     * 生命周期
     */
    //当自定义元素第一次被连接到文档 DOM 时被调用。
    connectedCallback() {
        console.log('我已经插入了！！！嗷呜')
    }
 
    //当自定义元素与文档 DOM 断开连接时被调用。
    disconnectedCallback() {
        console.log('我已经断开了！！！嗷呜')
    }
 
    //当自定义元素被移动到新文档时被调用
    adoptedCallback() {
        console.log('我被移动了！！！嗷呜')
    }
    //当自定义元素的一个属性被增加、移除或更改时被调用
    attributeChangedCallback() {
        console.log('我被改变了！！！嗷呜')
    }
 
}
 
window.customElements.define('xiao-man', Btn)
```

### 使用方式

```typescript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>web Component</title>
    <script src="./btn.js"></script>
</head>
<body>
    <xiao-man></xiao-man>
</body>
</html>
```

### 3. 如何在 Vue 使用

> defineCustomElement
>
> 告知 vue 这是一个自定义 Component 跳过组件检查

```typescript
/*vite config ts 配置*/
vue({
   template:{
     compilerOptions:{
         isCustomElement:(tag)=> tag.includes('xiaoman-')//xiaoman-是我们设定好的前缀，只要是`xiaoman-`开头的都会跳过组件的检测
         //我们使用这种这种Vue单文件。有新的文件命名规则，那就是.ce.vue的后缀，才会开启这个模式
      }
    }
})
```

#### 父组件

```typescript
<template>
    <div>
        <xiaoman-btn :title=" JSON.stringify(name) "></xiaoman-btn>
    </div>
</template>
 
<script setup lang='ts'>
import { ref, reactive, defineCustomElement } from 'vue'//要使用的话，需要导出一个defineCustomElement，这里面封装了那些我们要使用的方法
//自定义元素模式  要开启这个模式，只需要将你的组件文件以 .ce.vue 结尾即可
import customVueVue from './components/custom-vue.ce.vue'//对这个子组件做一个引入
const Btn = defineCustomElement(customVueVue)//将子组件塞进defineCustomElement函数内使方法生效
customElements.define('xiaoman-btn', Btn)//进行挂载，前面是要以xiaoman-开头的，才能跳过组件的检测，后面btn则是我们的子组件
//跳过组件的检测的话，就没有组件的提示了
 
const name = ref({a:1})
 
</script>
 
<style scoped lang='less'>
 
</style>
```

#### 子组件

> 传递参数 如果是对象需要序列化 他是作用于 标签上的
>
> 如果是引用类型的话需要做一个JSON.stringify()
>
> ```ts
> //区别
> :obj="obj"
> :obj="JSON.stringify(obj)"
> ```

```typescript
<template>
    <div>
 
        小满123213 {{title}}
    </div>
</template>
 
<script setup lang='ts'>
 
import { ref, reactive } from 'vue'
 
defineProps<{
    title:string
}>()
 
</script>
 
<style scoped lang='less'>
 
</style>
```

![image-20221029175604096](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029175604096-1711983932540-126.png)

![image-20221029175614603](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221029175614603-1711983932540-125.png)



# 第四十六章 —Vue3（Proxy跨域）-永恒之歌

> 小满给这个章节取绰号叫做`永恒之歌`，但看他没用写上去，估计是不好意思，觉得还是太中二了，没关系，我替他完成这个想法

## 1.什么是跨域

主要是出于浏览器的同源策略限制，它是浏览器最核心也最基本的安全功能。

当一个请求url的 协议、域名、端口 三者之间任意一个与当前页面url不同即为跨域。

```ABAP
例如 xxxx.com -> xxxx.com 存在跨域 协议不同

例如 127.x.x.x:8001 -> 127.x.x.x:8002 存在跨域 端口不同

例如 www.xxxx.com -> www.yyyy.com 存在跨域 域名不同
```

## 2.如何解决跨域

jsonp 这种方式在之前很常见，他实现的基本原理是利用了HTML里script元素标签没有跨域限制 **动态创建script标签**，将src作为服务器地址，服务器返回一个callback接受返回的参数(百度就在使用这种方式)

> script标签只能使用GET请求，因为他是没办法去发送POST请求的

```ts
function clickButton() {
    let obj, s
    obj = { "table":"products", "limit":10 }; //添加参数
    s =  document.createElement("script"); //动态创建script
    s.src = "接口地址xxxxxxxxxxxx"  + JSON.stringify(obj);
    document.body.appendChild(s);
 }
//与后端定义callback名称
function myFunc(myObj)  {
    //接受后端返回的参数
    document.getElementById("demo").innerHTML = myObj;
}
```

> cors 设置 CORS 允许跨域资源共享 需要后端设置，前端设置不了

```ts
{
  "Access-Control-Allow-Origin": "http://web.xxx.com" //可以指定地址
}
-----------------------------------------------------------------------------------------------------
{
  "Access-Control-Allow-Origin": "*" //也可以使用通配符 任何地址都能访问 安全性不高
}
//后端有时候需要存session，存session的时候会往前端去存一个cookie，如果设置为星号，则无法存
```

> 使用Vite proxy 或者 node代理 或者 webpack proxy 他们三种方式都是代理(**常用**)
>
> 我们先创建一个接口使用express简单构建一下

```ts
const express = require('express')
const app = express()
 
//创建get请求
app.get('/xm',(req,res)=>{//request为接收参数，response返回参数
     res.json({
        code:200,
        message:"请求成功"
     })
})
//端口号9001
app.listen(9001)
```

> 我们使用vite项目的fetch 请求一下(前端启动一下)

```js
<script lang="ts" setup>
import {ref,reactive } from 'vue'
fetch('http://localhost:9001/xm')
</script>
```

![image-20221203034619148](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221203034619148-1711983932540-127.png)

> 发现是存在跨域的,这时候我们就可以配合vite的代理来解决跨域 用法如下
>
> 需要在vite.config.js/ts 进行配置

```js
export default defineConfig({
  plugins: [vue()],
  server:{
     proxy:{
        '/api':{
            target:"http://localhost:9001/", //指向跨域地址
            changeOrigin:true, //支持跨域
            rewrite:(path) => path.replace(/^\/api/, "")//使用正则替换匹配路径,替换/api，因为我们后端并没有/api这个地址的，而是http://localhost:9999，所以这么写当我们匹配到/api就会自己去做一个替换
        }
     }
  }
})
```

> fetch 替换/api 他会截取/api 替换成 target地址。或者去后台将get请求我们手动加上/api

```js
<script lang="ts" setup>
import {ref,reactive } from 'vue'
fetch('/api/xm')
</script>
---------------------------------------------------------
    //后台手动加上/api
//创建get请求
app.get('/api/xm',(req,res)=>{//request为接收参数，response返回参数
     res.json({
        code:200,
        message:"请求成功"
     })
})
```

![image-20221203034721309](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221203034721309-1711983932541-129.png)

> webpack proxy 和 node proxy 用法都类似，且**proxy代理只适合dev环境**，上线之后这段代码是无效的，因为上线之后没人给你起node服务，上线之后可能使用的是Nginx，Tomcat或者apache之类都可能，到时候就需要通过它们的代理再去做一个转发

## 3.vite proxy 原理解析

> vite源码地址[github.com/vitejs/vite](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fvitejs%2Fvite)
>
> 源码路径 [vite](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fvitejs%2Fvite)/[packages](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fvitejs%2Fvite%2Ftree%2Fmain%2Fpackages)/[vite](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fvitejs%2Fvite%2Ftree%2Fmain%2Fpackages%2Fvite)/[src](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fvitejs%2Fvite%2Ftree%2Fmain%2Fpackages%2Fvite%2Fsrc)/[node](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fvitejs%2Fvite%2Ftree%2Fmain%2Fpackages%2Fvite%2Fsrc%2Fnode)/[server](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fvitejs%2Fvite%2Ftree%2Fmain%2Fpackages%2Fvite%2Fsrc%2Fnode%2Fserver)/**index.ts** vite源码 发现他处理proxy 是调用了proxyMiddleware

```js
// proxy                                                            
const { proxy } = serverConfig                              
if (proxy) {                                                
 middlewares.use(proxyMiddleware(httpServer, proxy, config)) 
}
```

> [vite](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fvitejs%2Fvite%2Ftree%2Fd76db0cae645beaecd970d95b4819158c5dd568a)/[packages](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fvitejs%2Fvite%2Ftree%2Fd76db0cae645beaecd970d95b4819158c5dd568a%2Fpackages)/[vite](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fvitejs%2Fvite%2Ftree%2Fd76db0cae645beaecd970d95b4819158c5dd568a%2Fpackages%2Fvite)/[src](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fvitejs%2Fvite%2Ftree%2Fd76db0cae645beaecd970d95b4819158c5dd568a%2Fpackages%2Fvite%2Fsrc)/[node](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fvitejs%2Fvite%2Ftree%2Fd76db0cae645beaecd970d95b4819158c5dd568a%2Fpackages%2Fvite%2Fsrc%2Fnode)/[server](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fvitejs%2Fvite%2Ftree%2Fd76db0cae645beaecd970d95b4819158c5dd568a%2Fpackages%2Fvite%2Fsrc%2Fnode%2Fserver)/[middlewares](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fvitejs%2Fvite%2Ftree%2Fd76db0cae645beaecd970d95b4819158c5dd568a%2Fpackages%2Fvite%2Fsrc%2Fnode%2Fserver%2Fmiddlewares)/**proxy.ts**
>
> 找到 **proxyMiddleware** 发现他是调用了 http-proxy这个库

```ts
import httpProxy from 'http-proxy'
export function proxyMiddleware(
    httpServer: http.Server | null,
    options: NonNullable<CommonServerOptions['proxy']>,
    config: ResolvedConfig
  ): Connect.NextHandleFunction {
    // lazy require only when proxy is used
const proxy = httpProxy.createProxyServer(opts) as HttpProxy.Server
```

> http-proxy npm地址 [http-proxy - npm (npmjs.com)](https://www.npmjs.com/package/http-proxy)
>
> http-proxy 模块用于转发 http 请求，其实现的大致原理为使用 http 或 https 模块搭建 node 代理服务器，将客户端发送的请求数据转发到目标服务器，再将响应输送到客户端
>
> 跟小满写的其实大差不差都是差不多的

```js
const http = require('http')
 
const httpProxy = require('http-proxy')
 
const proxy = httpProxy.createProxyServer({})
 
//创建一个代理服务 代理到9001
http.createServer((req,res)=>{
    proxy.web(req,res,{
        target:"http://localhost:9001/xm", //代理的地址
        changeOrigin:true, //是否有跨域
        ws:true //webSocetk
    })
}).listen(8888)
```

> 9001服务

```ts
const express = require('express')
const app = express()
 
//创建get请求
app.get('/xm',(req,res)=>{
     res.json({
        code:200,
        message:"请求成功"
     })
})
//端口号9001
app.listen(9001)
```

> 成功代理 访问8888端口代理9001的请求，成功跨域请求

![image-20221203035020508](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\编程笔记(小满系列)--原创\xmzs-images\image-20221203035020508-1711983932540-128.png)

**作者留言**：

​	到这里，你就已经学完了Vue3基础的内容

​	小余祝你在编程的道路上一路顺风，少有bug，志向远大的你一定可以在这条路渐行渐远，如果不开心的话就睡一觉，实在难受也可以来小满的微信群或者QQ群找我聊天，大家都是很热情的啦，包括UP主小满本人也是哦~![image-20230403193657226](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\JavaScript高级笔记(coderwhy) -- 原创\React笔记(coderwhy)\React-images\image-20230403193657226.png)
