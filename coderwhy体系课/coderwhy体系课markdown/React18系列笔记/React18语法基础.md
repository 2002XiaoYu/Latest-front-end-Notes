# React18语法基础

> GitHub地址：[2002XiaoYu (小余) (github.com)](https://github.com/2002XiaoYu)

## **React的介绍（技术角度）**

**React是什么？**

1. React：用于构建用户界面的 JavaScript 库
2. React的官网文档：https://zh-hans.reactjs.org/

## **React的特点：**

1. 声明式编程
2. 组件化开发
3. 多平台适配

### Hello React

- 开发React必须依赖三个库：
  - react ： 包含react所必须的核心代码
  - react_dom ： react渲染在不同平台所需要的核心代码
  - babel ：将jsx转换成React代码的工具
- 为什么要进行拆分呢？原因就是react-native
  -  react包中包含了**react web和react-native**所共同拥有的**核心代码**
  -  react-dom针对**web和native**所完成的事情不同：
     - web端：react-dom会将jsx最终渲染成真实的DOM，显示在浏览器中
     - native端：react-dom会将jsx最终渲染成原生的控件（比如Android中的Button，iOS中的UIButton）

**暂时我们直接通过CDN引入，来演练下面的示例程序：**

> 这里有一个crossorigin的属性，这个属性的目的是为了拿到跨域脚本的错误信息

```html
  <div id="app"></div>
  
  <script src="./lib/react.js"></script>
  <script src="./lib/react_dom.js"></script>
  <script src="./lib/babel.js"></script>
  <script type="text/babel">
    // 1.定义变量
    const message = "Hello World"
 
    // 2.渲染内容
    const root = ReactDOM.createRoot(document.querySelector("#app"))
    root.render(<h2>{message}</h2>)
  </script>
```

**点击按钮修改内容：**

  需要只用render方法来修改

```javascript
<script type="text/babel"> 
    // 1.定义变量
    let message = "Hello World"
    const btnClick = () => {
      message = "Hello React"
      render()
    }
 
    // 2.渲染内容
    const root = ReactDOM.createRoot(document.querySelector("#app"))
    render()
 
    function render() {
      root.render((
        <div>
          <h2>{message}</h2>
          <button onClick={btnClick}>修改文本</button>
        </div>
      ))
    }
</script>
```

------

### **Hello React – 组件化开发**

**整个逻辑其实可以看做一个整体，那么我们就可以将其封装成一个组件：**

​    **1.** 我们说过 root.render 参数 是一个 HTML元素或者一个组件 ；

​    2. 所以我们可以先将之前的业务逻辑封装到 一个组件 中，然后传入到 ReactDOM.render 函数中的第一个参数；

**在React中，如何封装一个组件呢？** 这里我们暂时使用 类的方式 封装组件：

​    1. 定义一个类 （类名大写，组件的名称是必须大写的，小写会被认为是HTML元素），继承自React.Component

​    2. 实现当前组件的render函数

​     render当中返回的jsx内容，就是之后React会帮助我们渲染的内容

```javascript
  <script type="text/babel">
    function foo() {
      console.log("foo", this)
    }
 
    // 1.定义根组件
    class App extends React.Component {
      constructor() {
        super()//用来调用和访问父类React.Component的构造函数和方法
        this.state = {
          message: "Hello World"
        }
      }
 
      changeText() {
        this.setState({ message: "Hello React" })
      }
 
      render() {
        return (
          <div>
            <h2>{this.state.message}</h2>
            <button onClick={this.changeText.bind(this)}>修改文本</button>
          </div>
        )
      }
    }
 
    // 2.渲染根组件
    const root = ReactDOM.createRoot(document.querySelector("#root"))
    root.render(<App/>)
  </script>
```

------

### **组件化 - 数据依赖**

**组件化问题一：** **数据在哪里定义** **？**

**在组件中的数据，我们可以分成两类：**

​    1. 参与界面更新的数据 ：当数据变量时，需要更新组件渲染的内容；

​    2. 不参与界面更新的数据 ：当数据变量时，不需要更新将组建渲染的内容；

**参与界面更新的数据我们也可以称之为是** **参与数据流** **，这个数据是** **定义在当前对象的state** **中**

​    1. 我们可以通过在 构造函数中 this.state = {定义的数据}

​    2. 当 我们的数据发生变化 时，我们可以 调用 this.setState 来更新数据 ，并且通知React进行update操作；

​    在进行update操作时，就会 重新调用render函数 ，并且使用最新的数据，来渲染界面

------

## **组件化 – 事件绑定**

**组件化问题二：事件绑定中的this**

​    在类中直接定义一个函数，并且将这个函数绑定到 元素的onClick事件 上，当前 这个函数的this指向的是谁 呢？

**默认情况下是undefined**

​    1. 很奇怪，居然是 undefined ；

​    2. 因为在正常的DOM操作中，监听点击，监听函数中的this其实是节点对象（比如说是button对象）；

​    3. 这次因为React并不是直接渲染成真实的DOM，我们所编写的button只是一个语法糖，它的本质React的Element对象；

​    4. 那么在这里发生监听的时候，react在执行函数时并没有绑定this，默认情况下就是一个undefined；

**我们在绑定的函数中，可能想要使用当前对象，比如执行 this.setState 函数，就必须拿到当前对象的this**

1. 我们就需要在传入函数时，给这个函数直接绑定this
2. 类似于下面的写法： 

```react
<button onClick={this.changeText.bind(this)}>改变文本</button>//需要使用bind绑定this。否则的话，onClick在调用的时候是正常调用，没有额外做出处理
//onClick内部调用的过程：const click = this.changeText.bind(this) 然后 click()，所以没有显示绑定this的话，this会飘到undefined中去

//当然写在行内里面肯定不太优雅的，我们可以提前在constructor构造函数中绑定，constructor(){ this.changeText = this.changeText.band(this)}
```

> - 当我们在React组件中使用`onClick`属性绑定一个事件处理程序时，由于事件处理程序的调用方式，`this`不会自动指向组件实例。因此，当我们尝试访问`this.setState`时，它会报错，因为`this`的值并不是组件实例。
> - 使用`.bind(this)`：在事件处理程序上使用`.bind(this)`将事件处理程序的上下文绑定到组件实例

补充：

- 在Class中，就算没开严格模式，this也会产生在严格模式的效果。
- 在babel中，默认开启严格模式(babel会在顶部自动加上，默认开启)

> 在组件方法(实例方法)中，从this中调用**setState**是从我们类继承的父类**React.Component**的

------

### **电影列表展示**

```javascript
<script type="text/babel">
    // 1.定义根组件
    class App extends React.Component {
      constructor() {
        super()
        this.state = {
          message: "Hello App",
          movies: ["星际穿越", "大话西游", "盗梦空间", "少年派的奇幻漂流"]
        }
      }
 
      render() {
        return (
          <ul>
            {
              this.state.movies.map(item => {
                return <li key={item}>{item}</li>
              })
            }
          </ul>
        )
      }
    }
 
    // 2.渲染根组件
    const root = ReactDOM.createRoot(document.querySelector("#root"))
    root.render(<App/>)
  </script>
```

------

### 计数器案例

```javascript
  <script type="text/babel">
    class App extends React.Component{
      constructor(){
        super()
        this.state = {
          number:100
        }
        this.join = this.join.bind(this)
        this.minus = this.minus.bind(this)
      }

      render(){
        const { number } = this.state

        return(
          <div>
            <h1>计数器</h1>
            <button onClick={this.join}>+1</button> 
            <span>{number}</span> 
            <button onClick={this.minus}>-1</button>
          </div>
        )
      }

      join(){
        this.setState({
          number:this.state.number + 1
        })
      }
      minus(){
        this.setState({
          number:this.state.number - 1
        })
      }
    }

    const root = ReactDOM.createRoot(document.querySelector("#root"))
    root.render(<App/>)
  </script>
```

------

## **VSCode代码片段**

**我们在前面练习React的过程中，有些代码片段是需要经常写的，我们在VSCode中我们可以生成一个代码片段，方便我们快速**

**生成。**

◼ **VSCode中的代码片段有固定的格式，所以我们一般会借助于一个在线工具来完成。**

◼ **具体的步骤如下：**

​    第一步，复制自己需要生成代码片段的代码；

​    第二步， https://snippet-generator.app/ 在该网站中生成代码片段；

​    第三步，在VSCode中配置代码片段；

------

## React基础

### JSX语法

> ◼ **这段element变量的声明右侧赋值的标签语法是什么呢？**
>
> ​    1.它不是一段字符串（因为没有使用引号包裹）；
>
> ​    2.它看起来是一段HTML元素，但是我们能在js中直接给一个变量赋值html吗？
>
> ​    3.其实是不可以的，如果我们将 type="text/babel" 去除掉，那么就会出现语法错误；
>
> ​    4.它到底是什么呢？其实它是一段 jsx的语法 ；
>
> ◼ **JSX是什么？**
>
> ​    1.JSX是 一种JavaScript的语法扩展（eXtension），也在很多地方称之为JavaScript XML，因为看起就是一段XML语法 ；
>
> ​    2.它用于 描述我们的UI界面 ，并且其 完成可以和JavaScript融合在一起使用 ；
>
> ​    3.它 不同于Vue中的模块语法 ，你 不需要专门学习模块语法中的一些指令 （比如v-for、v-if、v-else、v-bind）；

```javascript
  <script type="text/babel">
    // 1.定义根组件
    const element = <div>Hello World</div>
    // 2.渲染根组件
    const root = ReactDOM.createRoot(document.querySelector("#root"))
    root.render(element)
  </script>
```

**为什么React选择了JSX ？**

> **React认为** **渲染逻辑** **本质上** **与其他UI逻辑** **存在内在耦合**
>
> ​    1.比如 UI需要绑定事件 （button、a原生等等）；
>
> ​    2.比如 UI中需要展示数据状态；
>
> ​    3.比如 在某些状态发生改变时，又需要改变UI ；
>
> 他们之间是 密不可分 ，所以React 没有将标记分离到不同的文件 中，而是 将它们组合到了一起 ，这个地方就是 组件（Component） ；
>
> ​     当然，后面我们还是会继续学习更多组件相关的东西；
>
> 在这里，我们只需要知道，JSX其实是嵌入到JavaScript中的一种结构语法；
>
> **JSX的书写规范：**
>
> ​    1.JSX的顶层 只能有一个根元素 ，所以 我们很多时候会在外层包裹一个div元素 （或者使用后面我们学习的Fragment）；
>
> ​    2.为了方便阅读，我们通常在jsx的外层包裹一个小括号()，这样可以方便阅读，并且jsx可以进行换行书写；
>
> ​    3.JSX中的标签可以是 单标签 ，也可以是 双标签 ；
>
> ​    注意：如果是单标签，必须以/>结尾；

### JSX的基本使用

> **jsx中的注释**
>
> ```javascript
> {/*  要注释的内容  */}
> ```
>
> -  **JSX嵌入变量作为子元素**
>
>   - 情况一 ：当变量是 Number、String、Array类型 时，可以直接显示
>   - 情况二 ：当变量是 null、undefined、Boolean类型 时，内容为空；
>
>   ✓ 如果希望可以显示null、undefined、Boolean，那么需要转成字符串；
>
>   ✓ 转换的方式有很多，比如toString方法、和空字符串拼接，String(变量)等方式；
>
>   - 情况三 ： Object对象类型 不能作为子元素（not valid as a React child）
>
> - **JSX嵌入表达式**
>   - 运算表达式
>   - 三元运算符
>   - 执行一个函数
>
> 
>
> - **jsx绑定属性**
>   - 比如元素都会有 title属性  ： 使用{xxx变量}的方式绑定 ==>  <h2 title={title}><h2/>
>   - 比如img元素会有 src属性 
>   - 比如a元素会有 href属性 
>   - 比如元素可能需要 绑定class : 三元表达式、数组、 第三方库
>   - 比如原生使用 内联样式style : {{ key : value }} ，不能写- ，要写驼峰

### JSX的事件绑定

> - **如果原生DOM原生有一个监听事件，我们可以如何操作呢？**
>  - 方式一：获取DOM原生，添加监听事件；
>   - 方式二：在HTML原生中，直接绑定onclick；
>
> - **在React中是如何操作呢？** 我们来实现一下React中的事件监听，这里主要有两点不同
>  - React 事件的 命名采用小驼峰式（camelCase），而不是纯小写 ；
>   - 我们需要 通过{}传入一个事件处理函数 ，这个函数会在事件发生时被执行；
>
> **this的绑定问题**
>
> - **在事件执行后，我们可能需要获取当前类的对象中相关的属性，这个时候需要用到this**
>  - 如果我们这里直接打印this，也会发现它是一个undefined
>
> - **为什么是undefined呢？**
>   - 原因是btnClick函数并不是我们主动调用的，而且当button发生改变时，React内部调用了btnClick函数；
>  - 而它内部调用时，并不知道要如何绑定正确的this；
>
> - **如何解决this的问题呢？**
>   - 方案一 ：bind给btnClick显示绑定this
>  - 方案二 ：使用 ES6 class fields 语法(在类中你使用的方法从正常使用到变成使用箭头函数，因为箭头函数不受this的指向影响，所以不会导致this指向undefined)，不推荐
>   - 方案三 ：事件监听时传入箭头函数（个人推荐），跟第二种的区别在于使用箭头函数的地方不一样。这个传递参数也方便
>
> ![image-20230402185916584](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\React笔记(coderwhy)\React-images\image-20230402185916584-1683026306061-1.png)
>
> **事件参数传递**
>
> - **在执行事件函数时，有可能我们需要获取一些参数信息：比如event对象、其他参数**
>
>   - **情况一：获取event对象**
>    - 很多时候我们需要拿到 event对象 来做一些事情（比如阻止默认行为）
>     - 那么默认情况下， event对象有被直接传入 ，函数就可以获取到event对象；
>
>   - **情况二：获取更多参数**
>    - 有更多参数时，我们最好的方式就是 传入一个箭头函数 ， 主动执行的事件函数，并且传入相关的其他参数 ；

### JSX的条件渲染

- [if判断](https://so.csdn.net/so/search?q=if判断&spm=1001.2101.3001.7020)
- [三元运算符](https://so.csdn.net/so/search?q=三元运算符&spm=1001.2101.3001.7020)
- &&逻辑与
- v-show实现效果

### JSX的列表渲染

- map高阶函数(重要)
- filter
- slice

循环要加 key

### JSX的原理和本质

> - **实际上，jsx 仅仅只是 React.createElement(component, props, ...children) 函数的语法糖。**
>   - 所有的jsx最终都会被转换成 React.createElement的函数 调用。
>
> - **createElement需要传递三个参数：**
>   - 参数一：type
>   - 当前ReactElement的类型；
>   - 如果是标签元素，那么就使用字符串表示 “div”；
>   - 如果是组件元素，那么就直接使用组件的名称；
>
> - 参数二：config
>   - 所有jsx中的属性都在config中以对象的属性和值的形式存储；
>   - 比如传入className作为元素的class；
>
> - 参数三：children
>   - 存放在标签中的内容，以children数组的方式进行存储；
>   - 当然，如果是多个元素呢？React内部有对它们进行处理，处理的源码在下方

### 排他点击案例

```jsx
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>排他点击</title>
  <style>
    .a{
      color:red
    }
  </style>
</head>
<body>
  <div id="root"></div>
  <script src="./lib/react.js"></script>
  <script src="./lib/react-dom.js"></script>
  <script src="./lib/babel.js"></script>

  <script type="text/babel">
    class App extends React.Component{
      constructor(){
        super()
        this.state = {
          movies:["你的名字","十二生肖","肖申克的救赎","绿皮书"],
          currentIndex:0
        }
      }

      indexClick = (index) => {
        this.setState({
          currentIndex:index
        })
      }

      render(){
        const { movies , currentIndex} = this.state
        return (
          <div>
            <h1>排他</h1>
            <ul>
              {movies.map((item,index) => 
                <li key={item} 
                    className={currentIndex === index ? "a" : ""}
                    onClick={()=>this.indexClick(index)}
                >
                  {item}
                </li>
                )
              }
            </ul>
          </div>
        )
      }
    }

    const root = ReactDOM.createRoot(document.querySelector("#root"))
    root.render(<App/>)
  </script>
</body>
</html>
```

