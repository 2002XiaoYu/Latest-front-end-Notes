# React18脚手架

## **React脚手架解析**

### 认识脚手架工具

> - **对于现在比较流行的三大框架都有属于自己的脚手架：**
>  - Vue的脚手架： @vue/cli
>   - Angular的脚手架： @angular/cli
>  - React的脚手架： create-react-app
>
> - **它们的作用都是帮助我们生成一个通用的目录结构，并且已经将我们所需的工程环境配置好。**
>
> - **使用这些脚手架需要依赖什么呢？**
>   - 目前这些脚手架都是 使用node编写 的，并且都是 基于webpack 的；
>  - 所以我们必须在自己的电脑上 安装node环境 ；
>
> - **这里我们主要是学习React，所以我们以React的脚手架工具：** **create-react-app作为讲解** **；**

### create-react-app

- 创建Eeact项目

> - **创建React项目的命令如下：**
>  - 注意：项目名称 不能包含大写字母
>   - 另外还有更多创建项目的方式，可以参考GitHub的readme
>  - 创建完成后，进入对应的目录，就可以将项目跑起来：
>
> - **创建React项目**
>   - create-react-app 项目名称
>  - cd 01-test-react
>   - npm start

![image-20230403011732377](.\React-images\image-20230403011732377-1683026306062-2.png)

####  **了解 PWA**

> - **整个目录结构都非常好理解，只是有一个PWA相关的概念：**
>   - PWA全称 Progressive Web App ，即 渐进式WEB应用 ；
>   - 一个 PWA 应用首先是 一个网页 , 可以 通过 Web 技术编写出一个网页应用 ；
>   - 随后添加上 App Manifest 和 Service Worker 来实现 PWA 的 安装和离线 等功能；
>   - 这种Web存在的形式，我们也称之为是 Web App ；
>
> - **PWA解决了哪些问题呢？**
>   - 可以 添加至主屏幕 ，点击主屏幕图标可以实现启动动画以及隐藏地址栏；
>   - 实现 离线缓存功能 ，即使用户手机没有网络，依然可以使用一些离线功能；
>   - 实现了 消息推送 ；
>   - 等等一系列类似于Native App相关的功能；
>
> - **更多PWA相关的知识，可以自行去学习更多；**
>   - https://developer.mozilla.org/zh-CN/docs/Web/Progressive_web_apps

### webpack的配置

默认给隐藏起来了

- 如果我们希望看到webpack的配置信息，应该怎么来做呢？
  - 我们可以执行一个package.json文件中的一个脚本：**"eject": "react-scripts eject"**
  - 这个操作是不可逆的，所以在执行过程中会给与我们提示；
  - yarn eject或者npm eject，看你使用的是哪个包管理工具

> 我们在进行引入ReactDOM的时候，这是来自**"react-dom/client"**，除了这个还需要引入React，因为React.Component需要用到

# React组件化开发

## **什么是组件化开发呢？**

- **组件化是一种分而治之的思想：**
  - 如果我们将一个页面中所有的处理逻辑全部放在一起，处理起来就会变得非常复杂，而且不利于后续的管理以及扩展。
  - 但如果，我们讲一个页面拆分成一个个小的功能块，每个功能块完成属于自己这部分独立的功能，那么之后整个页面的管理和维护就变得非常容易了。

- ****我们需要通过组件化的思想来思考整个应用程序****：
  - 我们将一个完整的页面分成很多个组件；
  - 每个组件都用于实现页面的一个功能块；
  - 而每一个组件又可以进行细分；
  - 而组件本身又可以在多个地方进行复用

- 组件化思想的应用：
  - 有了组件化的思想，我们在之后的开发中就要充分的利用它。
  - 尽可能的将页面拆分成一个个小的、可复用的组件。
  - 这样让我们的代码更加方便组织和管理，并且扩展性也更强

- **React的组件相对于Vue更加的灵活和多样，按照不同的方式可以分成很多类组件：**
  - 根据组件的定义方式，可以分为：**函数组件(Functional Component )**和**类组件(Class Component)**；
  - 根据组件内部是否有状态需要维护，可以分成：无状态组件(Stateless Component )和有状态组件(Stateful Component)；
  - 根据组件的不同职责，可以分成：展示型组件(Presentational Component)和容器型组件(Container Component)

- 这些概念有很多重叠，但是他们最主要是关注数据逻辑和UI展示的分离：
  - 函数组件、无状态组件、展示型组件主要关注UI的展示；
  - 类组件、有状态组件、容器型组件主要关注数据逻辑；

### 类组件

- **类组件的定义有如下要求：**
  - **组件的名称是大写字符开头**（无论类组件还是函数组件） 
  - 类组件需要**继承自 React.Component**
  - 类组件必须实现**render函数**

在ES6之前，可以通过create-react-class 模块来定义类组件，但是目前官网建议我们使用ES6的class类定义。

- 使用class定义一个组件：

​    constructor是可选的，我们通常在constructor中初始化一些数据；

​    this.state中维护的就是我们组件内部的数据；

​    render() 方法是 class 组件中唯一必须实现的方法；

```jsx
import React from "react";
// 1.类组件
class App extends React.Component {
constructor() {
 super()
 this.state = {
   message: "App Component"
 }
}
render() {
 // const { message } = this.state
 // 1.react元素: 通过jsx编写的代码就会被编译成React.createElement, 所以返回的就是一个React元素
 // return <h2>{message}</h2>
 // 2.组件或者fragments(后续学习)
 // return ["abc", "cba", "nba"]
 // return [
 //   <h1>h1元素</h1>,
 //   <h2>h2元素</h2>,
 //   <div>哈哈哈</div>
 // ]
 // 3.字符串/数字类型
 // return "Hello World"
 return true
}
}
export default App;
```

###  render函数的返回值

> **当 render 被调用时，它会检查 this.props 和 this.state 的变化并返回以下类型之一：**
>
> - **React 元素：**
>
> ​    通常通过 JSX 创建。
>
> ​    例如，<div /> 会被 React 渲染为 DOM 节点，<MyComponent /> 会被 React 渲染为自定义组件；
>
> ​    无论是 <div /> 还是 <MyComponent /> 均为 React 元素。
>
> - **数组或 fragments：**使得 render 方法可以返回多个元素。
>
> - **Portals：**可以渲染子节点到不同的 DOM 子树中。
>
> - **字符串或数值类型**：它们在 DOM 中会被渲染为文本节点
>
> - **布尔类型或 null：**什么都不渲染。

### 函数组件

> 函数组件是使用function来进行定义的函数，只是这个函数会返回和类组件中render函数返回一样的内容。
>
> **函数组件有自己的特点（当然，后面我们会讲hooks，就不一样了）：**
>
> ​    没有生命周期，也会被更新并挂载，但是没有生命周期函数；
>
> ​    this关键字不能指向组件实例（因为没有组件实例）；
>
> ​    没有内部状态（state）；
>
> **我们来定义一个函数组件：**
>
> ```javascript
> // 函数式组件
> function App(props) {
> // 返回值: 和类组件中render函数返回的是一致
> return <h1>App Functional Component</h1>
> }
> export default App
> ```

## React组件生命周期

- **很多的事物都有从创建到销毁的整个过程，这个过程称之为是生命周期**
- React组件也有自己的生命周期，了解组件的生命周期可以让我们**在最合适的地方完成自己想要的功能**；
- 生命周期和生命周期函数的关系：
- 生命周期是一个**抽象的概念**，在生命周期的整个过程，分成了很多个阶段；
  - 比如挂载阶段（Mount），组件第一次在DOM树中被渲染的过程
  - 比如更新过程（Update），组件状态发生变化，重新更新渲染的过程
  - 比如卸载过程（Unmount），组件从DOM树中被移除的过程
- **React内部为了告诉我们当前处于哪些阶段，会对我们组件内部实现的某些函数进行回调，这些函数就是生命周期函数**
  - 比如实现componentDidMount函数：组件已经挂载到DOM上时，就会回调
  - 比如实现componentDidUpdate函数：组件已经发生了更新时，就会回调
  - 比如实现componentWillUnmount函数：组件即将被移除时，就会回调
  - 我们可以在这些回调函数中编写自己的逻辑代码，来完成自己的需求功能
- 我们谈React生命周期时，主要谈的类的生命周期，因为函数式组件是没有生命周期函数的；（后面我们可以通过hooks来模拟一些生命周期的回调）

### 生命周期解析

- 我们先来学习一下最基础、最常用的生命周期函数：
- 生命周期图谱：

![image-20230403190835912](.\React-images\image-20230403190835912-1683026306062-3.png)

### 生命周期函数

> 下面中Did的意思：做了。will：将
>
> - 例如componentDidMount：在组件中 做了 挂载

- **Constructor**
  - 如果不初始化 state 或不进行方法绑定，则不需要为 React 组件实现构造函数
  - constructor中通常只做两件事情：
    - 通过给 this.state 赋值对象来初始化内部的state
    - 为事件绑定实例（this）
- **componentDidMount**
  - componentDidMount() 会在组件挂载后（插入 DOM 树中）立即调用
  - componentDidMount中通常进行哪里操作呢？
    - 依赖于DOM的操作可以在这里进行
    - 在此处发送网络请求就最好的地方；（官方建议）
    - 可以在此处添加一些订阅（会在componentWillUnmount取消订阅）；

- **componentDidUpdate**
  -  componentDidUpdate() 会在更新后会被立即调用，首次渲染不会执行此方法。
     - 当组件更新后，可以在此处对 DOM 进行操作
     - 如果你对更新前后的 props 进行了比较，也可以选择在此处进行网络请求；（例如，当 props 未发生变化时，则不会执行网络请求）
- **componentWillUnmount**
  - componentWillUnmount() 会在组件卸载及销毁之前直接调用
    - 在此方法中执行必要的清理操作
    - 例如，清除 timer，取消网络请求或清除在 componentDidMount() 中创建的订阅等

### 不常用生命周期函数

- **除了上面介绍的生命周期函数之外，还有一些不常用的生命周期函数：**

  - getDerivedStateFromProps：state 的值在任何时候都依赖于 props时使用；该方法返回一个对象来更新state
  - **getSnapshotBeforeUpdate**：在React更新DOM之前回调的一个函数，可以获取DOM更新前的一些信息（比如说滚动位置）
  - **shouldComponentUpdate**：该生命周期函数很常用，这块内容写在后面
  - **另外，React中还提供了一些过期的生命周期函数，这些函数已经不推荐使用**

  - 更详细的生命周期相关的内容，可以参考官网：
    - https://zh-hans.reactjs.org/docs/react-component.html

```jsx
{/*类组件*/}
import React from "react"
 
class HelloWorld extends React.Component {
  // 1.构造方法: constructor
  constructor() {
    console.log("HelloWorld constructor")
    super()
 
    this.state = {
      message: "Hello World"
    }
  }
 
  changeText() {
    this.setState({ message: "你好啊, 李银河" })
  }
 
  // 2.执行render函数
  render() {
    console.log("HelloWorld render")
    const { message } = this.state
 
    return (
      <div>
        <h2>{message}</h2>
        <p>{message}是程序员的第一个代码!</p>
        <button onClick={e => this.changeText()}>修改文本</button>
      </div>
    )
  }
 
  // 3.组件被渲染到DOM: 被挂载到DOM
  componentDidMount() {
    console.log("HelloWorld componentDidMount")
  }
 
  // 4.组件的DOM被更新完成： DOM发生更新
  componentDidUpdate(prevProps, prevState, snapshot) {
    console.log("HelloWorld componentDidUpdate:", prevProps, prevState, snapshot)
  }
 
  // 5.组件从DOM中卸载掉： 从DOM移除掉
  componentWillUnmount() {
    console.log("HelloWorld componentWillUnmount")
  }
 
 
  // 不常用的生命周期补充
  shouldComponentUpdate() {
    return true
  }
 
  getSnapshotBeforeUpdate() {
    console.log("getSnapshotBeforeUpdate")
    return {
      scrollPosition: 1000
    }
  }
}
 
export default HelloWorld
```

```jsx
{/*App组件*/}
import React from "react"
import HelloWorld from "./HelloWorld"
 
class App extends React.Component {
  constructor() {
    super()
 
    this.state = {
      isShowHW: true
    }
  }
 
  switchHWShow() {
    this.setState({ isShowHW: !this.state.isShowHW })
  }
 
  render() {
    const { isShowHW } = this.state
 
    return (
      <div>
        哈哈哈
        <button onClick={e => this.switchHWShow()}>切换</button>
        { isShowHW && <HelloWorld/> }
      </div>
    )
  }
}
 
export default App
```

![image-20230403185843205](.\React-images\image-20230403185843205-1683026306062-8.png)

## 认识组件的嵌套

- **组件之间存在嵌套关系：**
  - 在之前的案例中，我们只是创建了一个组件App；
  - 如果我们一个应用程序将所有的逻辑都放在一个组件中，那么这个组件就会变成非常的臃肿和难以维护；
  - 所以组件化的核心思想应该是对组件进行拆分，拆分成一个个小的组件；
  - 再将这些组件组合嵌套在一起，最终形成我们的应用程序；
- **上面的嵌套逻辑如下，它们存在如下关系：**
  - App组件是Header、Main、Footer组件的父组件
  - Main组件是Banner、ProductList组件的父组件

![image-20230403204228279](.\React-images\image-20230403204228279-1683026306062-4.png)

## 认识组件间的通信

- **在开发过程中，我们会经常遇到需要组件之间相互进行通信：**
  -  比如App可能使用了多个Header，每个地方的Header展示的内容不同，那么我们就需要使用者传递给Header一些数据，让其进行展示
  -  又比如我们在Main中一次性请求了Banner数据和ProductList数据，那么就需要传递给他们来进行展示
  -  也可能是子组件中发生了事件，需要由父组件来完成某些操作，那就需要子组件向父组件传递事件
- **总之，在一个React项目中，组件之间的通信是非常重要的环节**
- **父组件在展示子组件，可能会传递一些数据给子组件：**
  - 父组件通过 **属性=值** 的形式来传递给子组件数据(<xxxx nammers={banners}/>)
  - 子组件通过 **props** 参数获取父组件传递过来的数据

![image-20230403204629406](.\React-images\image-20230403204629406-1683026306062-6.png)

### 父组件传递子组件 - 类组件和函数组件

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\React笔记(coderwhy)\React-images\image-20230403210754309.png" alt="image-20230403210754309" style="zoom:50%;" />![image-20230403210801984](.\React-images\image-20230403210801984-1683026306062-5.png)

### 参数propTypes

- 对于传递给子组件的数据，有时候我们可能希望进行验证，特别是对于大型项目来说：
  - 当然，如果你项目中默认继承了Flow或者TypeScript，那么直接就可以进行类型验证；
  - 但是，即使我们没有使用Flow或者TypeScript，也可以通过 prop-types 库来进行参数验证
- 从 React v15.5 开始，React.PropTypes 已移入另一个包中：prop-types 库

- 更多的验证方式，可以参考官网：https://zh-hans.reactjs.org/docs/typechecking-with-proptypes.html
  - 比如验证数组，并且数组中包含哪些元素；
  - 比如验证对象，并且对象中包含哪些key以及value是什么类型
  - 比如某个原生是必须的，使用 requiredFunc: PropTypes.func.isRequired
- **如果没有传递，我们希望有默认值呢？**
  - 我们使用defaultProps就可以了

### 子组件传递父组件

- **某些情况，我们也需要子组件向父组件传递消息：**
  - 在 React 中，子组件向父组件传递数据和函数通常使用回调函数的方式完成。具体的传递顺序如下：
    1. 在父组件中定义一个函数，用于接收子组件传递的数据或函数。
    2. 将这个函数作为 props 属性传递给子组件。
    3. 在子组件中通过 props 属性调用这个函数，并将需要传递给父组件的数据或函数作为参数传递给它。
- **我们这里来完成一个案例：**
  - 将计数器案例进行拆解
  - 将按钮封装到子组件中：CounterButton
  - CounterButton发生点击事件，将内容传递到父组件中，修改counter的值

### 组件通信案例练习

![image-20230404123026129](.\React-images\image-20230404123026129-1683026306062-7.png)

```jsx
{/*APP.jsx*/}
import React from 'react';
import TabControl from "./TabControl"
//或者使用import {Component} from 'react'导出也行

class App extends React.Component{
  constructor(){
    super()
    this.state = {
      Index:0,
      title:["首页","商品","个人详情"],
      context:["这是首页","这是商品","这是个人信息"]
    }
  }

  tabClick(Index){
    this.setState({
      Index
    })
  }

  render(){
    const { title ,context ,Index } = this.state
    return(
      <div>
        <TabControl title={title} tabClick={index => this.tabClick(index)}/>
        <h1>{context[Index]}</h1>
      </div>
    )
  }
}

export default App;
```

```JSX
{/*子组件*/}
import React, { Component } from 'react'
import style from "./style.css"
export class index extends Component {
  constructor(props){
    super(props)
    this.state = {
      currentIndex:0
    }
  }

  changeIndex(index){
    
    this.props.tabClick(index)

    this.setState({
      currentIndex:index
    })
  }

  render() {
    const { title } = this.props
    const { currentIndex } = this.state
    return (
      <div className='tab'>
          {title.map((item,index) => 
            <span key={item} 
              	//jsx很自由，有多种实现方法
              	//className={`context ${index === currentIndex && "active"}`}
                  className={`${index === currentIndex ? "active" : "context"}`}
                  onClick={() => this.changeIndex(index)}
            >
              {item}
            </span>
            )}
      </div>
    )
  }
}

export default index
```

```CSS
/*子组件样式*/
.tab{
  display: flex;
  justify-content: space-around;
}

.tab .context{
  font-weight: 600;
  padding: 4px;
}

.tab .active{
  color:red;
  font-weight: 600;
  padding: 4px;
  border-bottom: 4px solid red;
}
```

## React中的插槽（slot）

- **在开发中，我们抽取了一个组件，但是为了让这个组件具备更强的通用性，我们不能将组件中的内容限制为固定的div、span等等这些元素**
- **我们应该让使用者可以决定某一块区域到底存放什么内容**
- 这种需求在Vue当中有一个固定的做法是通过slot来完成的，React呢？
- React对于这种需要插槽的情况非常灵活，有两种方案可以实现：
  - 组件的**children**子元素
  - **props**属性传递React元素

### children实现插槽

- **每个组件都可以获取到 props.children：它包含组件的开始标签和结束标签之间的内容**

![image-20230404202804530](.\React-images\image-20230404202804530-1683026306062-9.png)![image-20230404202807588](.\React-images\image-20230404202807588-1683026306062-10.png)

### props实现插槽

- **通过children实现的方案虽然可行，但是有一个弊端：通过索引值获取传入的元素很容易出错，不能精准的获取传入的原生**
- **另外一个种方案就是使用 props 实现：**
  - 通过具体的属性名，可以让我们在传入和获取时更加的精准

![image-20230404202840179](.\React-images\image-20230404202840179-1683026306062-12.png)

![image-20230404202844841](.\React-images\image-20230404202844841-1683026306062-11.png)

## Context应用场景

- **非父子组件数据的共享：**
  - 在开发中，比较常见的数据传递方式是通过props属性自上而下（由父到子）进行传递
  - 但是对于有一些场景：比如一些数据需要在多个组件中进行共享（地区偏好、UI主题、用户登录状态、用户信息等）
  - 如果我们在顶层的App中定义这些信息，之后一层层传递下去，那么对于一些中间层不需要数据的组件来说，是一种冗余的操作
- **我们实现一个一层层传递的案例：**
  -  这边顺便补充一个小的知识点：[JSX In Depth – React (reactjs.org)](https://legacy.reactjs.org/docs/jsx-in-depth.html)
  -  这个是传递参数的时候，如果不想要一个个慢慢传可以直接定义成对象，然后直接在组件中写上**...展开运算符**，把整个对象给传递给子组件，不然一般情况下{}是处理不了对象的。这样可以快速一层层组件传递信息下来。但是也不推荐，因为还有更简单的方法
- **但是，如果层级更多的话，一层层传递是非常麻烦，并且代码是非常冗余的：**
  - React提供了一个API：**Context**
  - Context 提供了一种**在组件之间共享此类值的方式**，而**不必显式地通过组件树的逐层传递 props**
  - Context 设计目的是为了**共享那些对于一个组件树而言是“全局”的数据**，例如当前认证的用户、主题或首选语言

### Context相关API

- **React.createContext**
  - 创建一个需要共享的Context对象：
  - 如果一个组件订阅了Context，那么这个组件会从离自身最近的那个匹配的 Provider 中读取到当前的context值；
  - defaultValue是组件在顶层查找过程中没有找到对应的Provider，那么就使用默认值

![image-20230404204400830](.\React-images\image-20230404204400830-1683026306062-13.png)

- **Context.Provider**，provider可以理解为**生产者**的意思
  - 每个 Context 对象都会返回一个 Provider React 组件，它允许消费组件订阅 context 的变化:
  - Provider 接收一个 value 属性，传递给消费组件
  - 一个 Provider 可以和多个消费组件有对应关系
  - 多个 Provider 也可以嵌套使用，里层的会覆盖外层的数据
  - 当 Provider 的 value 值发生变化时，它内部的所有消费组件都会重新渲染

![image-20230404204433926](.\React-images\image-20230404204433926-1683026306062-14.png)

- **Class.contextType**

  - 挂载在 class 上的 contextType 属性会被重赋值为一个由 React.createContext() 创建的 Context 对象：
  - 这能让你使用 this.context 来消费最近 Context 上的那个值
  - 你可以在任何生命周期中访问到它，包括 render 函数中

  ![image-20230404204508004](.\React-images\image-20230404204508004-1683026306062-15.png)

- **Context.Consumer**

  - 这里，React 组件也可以订阅到 context 变更。这能让你在 函数式组件 中完成订阅 context
  - 这里需要 函数作为子元素（function as child）这种做法
  - 这个函数接收当前的 context 值，返回一个 React 节点

![image-20230404204530730](.\React-images\image-20230404204530730-1683026306062-16.png)

### Context代码演练

- **Context的基本使用**

React中的Context是一种跨组件层级传递数据的方式，可以让我们避免通过props一层层地传递数据，而是将数据传递到整个组件树中。

使用Context需要先创建一个Context对象，可以通过React.createContext()方法来创建。

接下来，我们需要在组件树中提供该Context的值，在组件中使用该Context的值。

以下是一个使用Context的简单示例：

首先创建一个Context对象：

```jsx
const MyContext = React.createContext();
```

然后在需要提供Context值的组件中，使用MyContext.Provider组件包裹子组件，并将value属性设置为需要传递的值：

```jsx
<MyContext.Provider value="hello">
  <Child />
</MyContext.Provider>
```

在需要使用该Context值的组件中，使用MyContext.Consumer组件来订阅该Context的值：

```jsx
<MyContext.Consumer>
  {value => <div>{value}</div>}
</MyContext.Consumer>
```

完整代码如下：

```jsx
import React, { createContext } from 'react';

const MyContext = createContext();

function Parent() {
  return (
    <MyContext.Provider value="hello">
      <Child />
    </MyContext.Provider>
  );
}

function Child() {
  return (
    <MyContext.Consumer>
      {value => <div>{value}</div>}
    </MyContext.Consumer>
  );
}

function App() {
  return (
    <div>
      <Parent />
    </div>
  );
}

export default App;
```

- **什么时候使用默认值defaultValue呢？**

> 当我们组件写在我们想要传达的范围外，就像下面中Home组件不在UserContext.provider里面，获取不到UserContext.provider的值，就可以使用到默认值了

![image-20230404221730477](.\React-images\image-20230404221730477-1683026306062-19.png)

- **什么时候使用Context.Consumer呢？**Consumer可以理解为**消费者**的意思
  - 当使用value的组件是一个函数式组件时
  - 当组件中需要使用多个Context时

#