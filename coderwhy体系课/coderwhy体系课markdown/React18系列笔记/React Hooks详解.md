# React Hooks详解

## 为什么需要Hook?

- **Hook 是 React 16.8 的新增特性，它可以让我们在不编写class的情况下使用state以及其他的React特性（比如生命周期）**

- **我们先来思考一下class组件相对于函数式组件有什么优势？比较常见的是下面的优势：**

  - class组件可以定义自己的state，用来保存组件自己内部的状态
    - 函数式组件不可以，因为函数每次调用都会产生新的临时变量
  - class组件有自己的**生命周期**，我们可以在**对应的生命周期中完成自己的逻辑**
    - 比如在componentDidMount中发送网络请求，并且该生命周期函数只会执行一次
    - 函数式组件在学习hooks之前，如果在函数中发送网络请求，意味着每次重新渲染都会重新发送一次网络请求
  - class组件可以**在状态改变时只会重新执行render函数**以及**我们希望重新调用的生命周期函数componentDidUpdate**等
    - 函数式组件在重新渲染时，整个函数都会被执行，似乎没有什么地方可以只让它们调用一次
  - **所以，在Hook出现之前，对于上面这些情况我们通常都会编写class组件**

- **函数式组件存在最大的缺陷**

  > 1. 组件不会被重新渲染：修改message之后，组件不知道自己要渲染了
  > 2. 就算页面重新渲染，函数也会被重新执行，也就是又会被前面的赋值重新赋值一遍，比如我一开始在函数里面写上一个let name = "小余"，这是用来定义初始的状态值的，但是我更新重新渲染，这里又会重新执行，把name赋值为小余

## Class组件存在的问题

- **复杂组件变得难以理解：**
  - 我们在最初编写一个class组件时，往往逻辑比较简单，并不会非常复杂。但是**随着业务的增多**，我们的**class组件会变得越来越复杂**
  - 比如componentDidMount中，可能就会包含大量的逻辑代码：包括**网络请求、一些事件的监听**（还需要在componentWillUnmount中移除）
  - 而对于这样的class实际上非常难以拆分：因为它们的逻辑往往混在一起，强行拆分反而会造成过度设计，增加代码的复杂度
- **难以理解的class：**
  - 很多人发现**学习ES6的class是学习React的一个障碍**
  - 比如在class中，我们**必须搞清楚this的指向到底是谁**，所以需要花很多的精力去学习this
  - 虽然我认为前端开发人员必须掌握this，但是依然处理起来非常麻烦
- **组件复用状态很难：**
  - 在前面为了一些状态的复用我们需要**通过高阶组件**
  - 像我们之前学习的**redux中connect或者react-router中的withRouter**，这些高阶组件设计的目的就是**为了状态的复用**
  - 或者**类似于Provider、Consumer来共享一些状态**，但是**多次使用Consumer时，我们的代码就会存在很多嵌套**
  - 这些代码让我们**不管是编写和设计上来说，都变得非常困难**

## Hook的出现

> 它可以让我们在不编写class的情况下使用state以及其他的React特性

### Hook解决的问题

1. 复用状态逻辑：在 React Hooks 出现之前，复用组件的状态逻辑需要通过 render props 或者高阶组件（Higher-Order Components, HOCs）来实现。这种方式会导致组件树变得复杂，代码难以理解。通过自定义 Hooks，我们可以将组件逻辑提取到可重用的函数中。
2. 组件层级问题：在复杂应用中，使用 HOCs 和 render props 的方式可能导致组件层级过深，从而使组件难以维护。Hooks 通过自定义的方式，可以更简洁地重用状态逻辑，避免组件层级过深的问题。
3. 类组件的弊端：在 React 中，类组件存在一些问题，例如组件实例化过程消耗资源、繁琐的生命周期方法等。Hooks 使得我们可以在不使用类组件的情况下，实现状态和生命周期功能。这使得代码更简洁，易于理解和维护。
4. 更容易访问相关逻辑：在类组件中，相关的逻辑可能会分散在不同的生命周期方法中。Hooks 允许我们将相互关联的代码放在一起，使得代码结构更清晰。

- **Hook的使用场景：**
  - Hook的出现**基本可以代替我们之前所有使用class组件的地方**
  - 但是如果是一个旧的项目，你**并不需要直接将所有的代码重构为Hooks**，因为**它完全向下兼容，你可以渐进式的来使用它**
  - Hook**只能在函数组件中使用，不能在类组件，或者函数组件之外的地方使用**
- **在我们继续之前，请记住 Hook 是：**
  - **完全可选的**：你无需重写任何已有代码就可以在一些组件中尝试 Hook。但是如果你不想，你不必现在就去学习或使用 Hook
  - 1**00% 向后兼容的**：Hook 不包含任何破坏性改动
  - **现在可用**：Hook 已发布于 v16.8.0

## Class组件和Functional组件对比

![image-20230426073321507](.\React-images\image-20230426073321507-1683026306063-108.png)

### 计数器案例对比

- **我们通过一个计数器案例，来对比一下class组件和函数式组件结合hooks的对比：**

```jsx
//Class组件
import React, {PureComponent} from 'react';
class App extends PureComponent {
  constructor(props) {
    super(props);
    this.state = {
      counter:0
    }
  }

  addCount(){
    this.setState({
      counter:this.state.counter + 1
    })
  }

  reduceCount(){
    this.setState({
      counter: this.state.counter - 1
    })
  }

  render() {
    const { counter } = this.state

    return (
      <div>
        计数：{counter}
        <button onClick={e => this.addCount()}>+1</button>
        <button onClick={e => this.reduceCount()}>-1</button>
      </div>
    )
  }
}

export default App;

```

```jsx
//hooks实现(未优化写法)
import {useState} from "react";
function Counter(props){
  const counter = useState(0)
  return(
    <div>
      <h1>计数：{counter[0]} </h1>
      <button onClick={e=> counter[1](1)}>加1</button>
      <button onClick={e=> counter[1](-1)}>减1</button>
    </div>
  )
}

export default Counter
```

```jsx
//hooks实现(优化写法)：
import {useState} from "react";
function Counter(props){
  const [counter,setCounter] = useState(0)
  return(
    <div>
      <h1>计数：{counter} </h1>
      <button onClick={e=> setCounter(counter + 1)}>加1</button>
      <button onClick={e=> setCounter(counter - 1)}>减1</button>
    </div>
  )
}

export default Counter
```

## useState解析

> `useState` 是 React 中的一个 Hook，它允许你在函数组件中添加和管理状态。在 React 16.8 之前，只有类组件才能具有状态。通过引入 Hooks，函数组件也可以具有状态，从而简化了组件编写过程。`useState` 是最基本的 Hook，它返回一个包含两个元素的数组：状态变量和状态更新函数。

- **那么我们来研究一下核心的一段代码代表什么意思：**

  - **useState来自react**，需要**从react中导入**，它是一个**hook**

    **✓ 参数：初始化值**，如果**不设置为undefined**

    **✓ 返回值：数组**，包含**两个元素**

    ​	➢ 元素一：**当前状态的值**（第一调用为初始化值）

    ​	➢ 元素二：**设置状态值的函数**

  - 点击button按钮后，会完成两件事情：

    ✓ 调用**setCount**，**设置一个新的值**

    ✓ **组件重新渲染**，并且**根据新的值返回DOM结构**

  - **通过上面的一个简单案例，你已经会喜欢上Hook的使用**

    - Hook 就是 JavaScript 函数，这个函数可以帮助你 **钩入（hook into） React State以及生命周期等特性**
    - Hook名字的由来：
      - Hook 这个名字的来源是它的功能特点：它们允许你在不改变组件结构的情况下，在函数组件中“钩入”（hook into）React 的特性。换句话说，Hook 提供了一种在函数组件中使用 React 特性（例如状态和生命周期方法）的方式，而无需将组件转换为类组件
      - Hook 的命名通常遵循以下规则：
        1. 以 `use` 为前缀：Hook 的名称通常以 `use` 开头，这是一个约定，有助于在代码中区分 Hook 和其他函数。例如：`useState`、`useEffect` 和 `useContext`。
        2. 描述功能：Hook 名称的剩余部分通常是一个描述性的单词或短语，用于描述 Hook 的功能。例如，`useState` 表示一个用于管理状态的 Hook，`useEffect` 表示一个用于处理副作用的 Hook，而 `useContext` 表示一个用于访问上下文的 Hook

  - **但是使用它们会有两个额外的规则：**

    - 只能在**函数最外层调用 Hook**。**不要在循环、条件判断或者子函数中调用**
    - 只能**在 React 的函数组件**中调用 Hook。**不要在其他 JavaScript 函数中调用**

- **Tip：**

  - Hook指的类似于useState、useEffect这样的函数
  - Hooks是对这类函数的统称

### 计数器详解

1. 导入 `useState`： 要在组件中使用 `useState`，首先需要从 'react' 模块导入它：

```jsx
import React, { useState } from 'react';
```

2. 初始化状态： 在函数组件内部，通过调用 `useState` 并传入初始状态值来初始化状态。`useState` 返回一个包含两个元素的数组，分别是当前状态值和更新状态值的函数。通常，我们使用数组解构来分别为这两个值命名：

```jsx
//count 是状态变量，它的初始值为 0。setCount 是更新 count 的函数
const [count, setCount] = useState(0);
```

3. 读取状态： 在组件的 JSX 中，可以直接使用状态变量。例如，要显示 `count` 的值，可以在 JSX 中插入 `{count}`

```jsx
<p>Count: {count}</p>
```

4. 更新状态： 要更新状态，可以调用 `useState` 返回的状态更新函数。在这个例子中，`setCount` 函数用于更新 `count` 的值。通常，我们会在事件处理函数中调用状态更新函数。例如，要在点击按钮时增加计数器，可以编写如下代码：

```jsx
<button onClick={() => setCount(count + 1)}>Increase</button>
```

- 注意，当调用状态更新函数时，React 会重新渲染组件，以便显示最新状态的值。

## 认识useState

- **State Hook的API就是 useState，我们在前面已经进行了学习：**

  - **useState**会帮助我们**定义一个 state变量**，useState 是一种**新方法**，它与 **class 里面的 this.state 提供的功能完全相同**

    ✓ 一般来说，**在函数退出后变量就会”消失”**，而 **state 中的变量会被 React 保留**

  - **useState接受唯一一个参数**，在**第一次组件被调用时使用来作为初始化值**。（如果没有传递参数，那么初始化值为undefined）

  - **useState的返回值是一个数组**，我们可以通过**数组的解构**，来完成赋值会非常方便

    ✓ https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment

- **FAQ：为什么叫 useState 而不叫 createState?**

  - “create” 可能不是很准确，因为 **state 只在组件首次渲染的时候被创建**
  - 在**下一次重新渲染时，useState 返回给我们当前的 state**
  - **如果每次都创建新的变量，它就不是 “state”了**
  - 这也是 **Hook 的名字总是以 use 开头的一个原因**

- **当然，我们也可以在一个组件中定义多个变量和复杂变量（数组、对象）**

### 了解useState原理

`useState` 是 React 中的一个基本 Hook，它允许你在函数组件中添加和管理状态。在深入了解其原理之前，让我们先看一个简单的例子：

```jsx
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

在这个例子中，我们使用 `useState(0)` 初始化了一个状态变量 `count`，并为其提供了一个初始值 0。`useState` 返回一个包含两个元素的数组：第一个元素是当前的状态值，第二个元素是更新状态值的函数。在本例中，我们通过数组解构得到了 `count` 和 `setCount`。

当我们点击 "Increment" 按钮时，`setCount` 会被调用，从而更新 `count` 的值。这会触发组件的重新渲染，显示更新后的状态值。

现在，让我们深入了解 `useState` 的原理：

1. **状态存储**：当组件首次渲染时，React 会为每个 `useState` 调用分配一个存储单元，用于存储状态值。在组件的后续重新渲染过程中，React 会根据调用的顺序重新使用这些存储单元。
2. **状态更新**：当你调用 `setCount`（或任何其他通过 `useState` 返回的更新函数）时，React 会将更新后的状态值存储到对应的存储单元中，并将组件标记为需要重新渲染。在下一次渲染时，React 会使用最新的状态值。
3. **函数组件的重新执行**：与类组件的 `setState` 不同，使用 `useState` 更新状态会导致整个函数组件被重新执行。这意味着你可以直接在组件函数中访问更新后的状态值，而不需要使用回调函数或生命周期方法。
4. **多个状态变量**：你可以在组件中使用多个 `useState` 调用，以管理多个独立的状态变量。这样做的好处是，每个状态变量都有自己的更新函数，可以更清晰地表示状态更新的逻辑。

通过 `useState`，React 为函数组件提供了一种简洁、易用的状态管理机制，使得开发者能够以更函数式的方式编写组件，同时保持良好的性能和可读性。

- 而这其实也引发了我的思考，从而得出了一个结论：
  - 我数组解构出来的setCount其实是useState内部实现的一个函数，我在调用setCount的时候，其实就相当于调用useState里面的那个函数，而那个函数里面进行了修改count，在这个函数内修改了count其实就是形成了一个闭包，从而让count的值保存了下来
  - 在函数组件中，`useState` 利用闭包来保存和更新状态。这样，在组件的每次渲染中，都可以访问到最新的状态值。闭包允许函数访问并操纵外部函数中的变量，即使外部函数已经执行完成。在这个情况下，`useState` 利用闭包来保存和更新状态，确保组件内的状态始终保持正确

## 认识Effect Hook

- **目前我们已经通过hook在函数式组件中定义state，那么类似于生命周期这些呢？**

  - Effect Hook 可以让你来完成一些**类似于class中生命周期的功能**

  - 事实上，类似于**网络请求、手动更新DOM、一些事件的监听**，都是**React更新DOM的一些副作用**（Side Effects）

    - 在编程中，副作用（side effect）通常指的是一个函数或表达式在计算结果之外对外部环境产生的影响。换句话说，副作用是程序状态的改变，这种改变在函数调用期间发生，但并不是函数的直接返回结果。副作用可能包括改变全局变量、修改输入参数、执行 I/O 操作（如网络请求、读写文件等）、修改 DOM 等。

      在 React 中，副作用通常是指组件渲染时可能会导致的一些额外操作。这些操作不是组件的直接渲染结果，但可能会对组件的功能和性能产生影响。例如：

      1. 网络请求：获取数据并更新组件状态，这会触发组件重新渲染。
      2. 手动更新 DOM：直接操作 DOM 而不是通过 React 的状态和属性管理，这可能导致 React 与 DOM 之间的同步问题。
      3. 事件监听：为 DOM 元素添加事件监听器，这可能会引起内存泄漏，如果没有正确清除监听器。

      在 React 中，你可以使用 `useEffect` Hook 来处理副作用。`useEffect` 允许你在函数组件内执行副作用操作，如网络请求、设置事件监听器等。同时，`useEffect` 还提供了清除副作用的机制，从而确保资源被正确释放，避免内存泄漏。

  - 所以**对于完成这些功能的Hook被称之为 Effect Hook**

- 假如我们现在有一个需求：**页面的title总是显示counter的数字，分别使用class组件和Hook实现：**

  - 上面实现过

- **useEffect的解析：**

  - 通过useEffect的Hook，可以告诉**React需要在渲染后执行某些操作**
  - useEffect**要求我们传入一个回调函数**，在React**执行完更新DOM操作之后，就会回调这个函数**
  - 默认情况下**，无论是第一次渲染之后**，还是每次更新之后，**都会执行这个 回调函数**

```jsx
import { useEffect } from "react"

useEffect(()=>{
  //当前传入的回调函数会在组件被渲染完成后，自动执行
  //网络请求/DOM操作/修改标题/事件监听
})
```

### 需要清除Effect

- **在class组件的编写过程中，某些副作用的代码，我们需要在componentWillUnmount中进行清除：**

  - 比如我们之前的**事件总线或Redux中手动调用subscribe**
  -  都需要在**componentWillUnmount有对应的取消订阅**
  - Effect Hook通过什么方式来模拟componentWillUnmount呢？

- **useEffect传入的回调函数A本身可以有一个返回值，这个返回值是另外一个回调函数B：**

  ```jsx
  type EffectCallback = () => (void | (() => void | undefined));
  ```

- 为什么要在 effect 中返回一个函数？

  - 这是 **effect 可选的清除机制。每个 effect 都可以返回一个清除函数**
  - 如此**可以将添加和移除订阅的逻辑放在一起**
  - 它们**都属于 effect 的一部分**

- **React 何时清除 effect？**

  - React 会在**组件更新和卸载的时候执行清除操作**
  - 正如之前学到的，**effect 在每次渲染的时候都会执行**

```jsx
import React, { useState, useEffect } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  // 使用 useEffect 来实现副作用操作
  useEffect(() => {
    // 当 count 更新时，更新页面标题
    document.title = `点击次数：${count}`;

    // 如果组件卸载，我们需要清除副作用，避免内存泄漏
    return () => {
      document.title = 'React App';
    };
  }, [count]); // 指定 count 作为依赖，当 count 变化时，会重新执行 useEffect 函数

  return (
    <div>
      <p>点击了 {count} 次</p>
      <button onClick={() => setCount(count + 1)}>点击</button>
    </div>
  );
}

export default Counter;
```

## 使用多个Effect

- **使用Hook的其中一个目的就是解决class中生命周期经常将很多的逻辑放在一起的问题：**
  - 比如网络请求、事件监听、手动修改DOM，这些往往都会放在componentDidMount中
- **使用Effect Hook，我们可以将它们分离到不同的useEffect中：**
  - 代码组织：将不同的副作用分离到不同的 `useEffect` 中，使得每个副作用具有更清晰的责任分工，易于理解和维护。这样可以避免一个庞大的、包含多个副作用的 `useEffect` 函数，从而提高代码可读性。
  - 优化性能：通过将副作用分离到不同的 `useEffect` 中，并为每个 `useEffect` 指定不同的依赖数组，我们可以更精确地控制每个副作用的执行时机。当依赖项发生变化时，只会执行与之相关的副作用，而不会触发其他无关的副作用。这样可以减少不必要的重复执行，从而提高性能。
  - 减少副作用之间的相互干扰：在某些情况下，一个副作用可能会影响到另一个副作用，将它们分离到不同的 `useEffect` 中可以降低副作用之间的耦合，减少潜在的问题。
  - 清除副作用：每个 `useEffect` 可以返回一个清除函数，用于在组件卸载或依赖项变化时清除副作用。将不同的副作用分离到不同的 `useEffect` 中，可以使得每个副作用的清除逻辑更加独立，简化代码。
- **Hook 允许我们按照代码的用途分离它们**， 而不是像生命周期函数那样
- React 将按照 effect 声明的顺序依次调用组件中的每一个 effect

### Effect性能优化

- **默认情况下，useEffect的回调函数会在每次渲染时都重新执行，但是这会导致两个问题：**
  - 某些代码我们只是**希望执行一次**即可，类似于componentDidMount和componentWillUnmount中完成的事情；（比如网络请求、订阅和取消订阅）
  - 另外，**多次执行也会导致一定的性能问题**
- **我们如何决定useEffect在什么时候应该执行和什么时候不应该执行呢？**
  - useEffect实际上有两个参数：
    1. 参数一：执行的回调函数
    2. 参数二：该**useEffect在哪些state发生变化时，才重新执行**；（受谁的影响）
- **案例练习：**
  - 受count影响的Effect，代码在上面
- **但是，如果一个函数我们不希望依赖任何的内容时，也可以传入一个空的数组 []：**
  - 那么这里的两个回调函数分别对应的就是componentDidMount和componentWillUnmount生命周期函数了
  - 但是他并不等于生命周期，他其实本质上是控制副作用的，他比声明周期还强大

## useContext的使用

- **在之前的开发中，我们要在组件中使用共享的Context有两种方式：**
  - 类组件可以通过 **类名.contextType = MyContext方式**，在类中获取context
  - 多个Context或者在函数式组件中通过 **MyContext.Consumer** 方式共享context；
- **但是多个Context共享时的方式会存在大量的嵌套：**
  - Context Hook**允许我们通过Hook来直接获取某个Context的值**

> 这是Class的案例使用：

1. 首先创建一个 Context：

```jsx
import React from 'react';

// 创建一个共享的 Context
const ThemeContext = React.createContext();

export { ThemeContext };
```

2. 创建一个 `ThemeProvider` 组件来提供 Context 值：

```jsx
import React, { Component } from 'react';
import { ThemeContext } from './ThemeContext';

class ThemeProvider extends Component {
  state = {
    theme: 'light',
  };

  // 切换主题的函数
  toggleTheme = () => {
    this.setState((prevState) => ({
      theme: prevState.theme === 'light' ? 'dark' : 'light',
    }));
  };

  render() {
    return (
      // 通过 ThemeContext.Provider 将值传递给子组件
      <ThemeContext.Provider
        value={{
          theme: this.state.theme,
          toggleTheme: this.toggleTheme,
        }}
      >
        {this.props.children}
      </ThemeContext.Provider>
    );
  }
}

export default ThemeProvider;
```

3. 使用 `ThemeProvider` 封装应用，并在子组件中使用 `ThemeContext.Consumer`：

```jsx
import React, { Component } from 'react';
import { ThemeContext } from './ThemeContext';
import ThemeProvider from './ThemeProvider';

class App extends Component {
  render() {
    return (
      <ThemeProvider>
        <Content />
      </ThemeProvider>
    );
  }
}

class Content extends Component {
  render() {
    return (
      <ThemeContext.Consumer>
        {({ theme, toggleTheme }) => (
          <div>
            <h1>当前主题：{theme}</h1>
            <button onClick={toggleTheme}>切换主题</button>
          </div>
        )}
      </ThemeContext.Consumer>
    );
  }
}

export default App;
```

> 这是Hook的useContext案例：
>
> 我们首先创建了一个名为 `ThemeContext` 的 Context，并导出了一个自定义的 Hook `useTheme`，以便在组件中方便地访问 Context。接着，我们创建了一个 `ThemeProvider` 组件，它使用 `ThemeContext.Provider` 将主题相关的状态（`theme` 和 `toggleTheme` 函数）传递给子组件。最后，在子组件 `Content` 中，我们使用 `useTheme` 来获取当前主题和切换主题的函数，并在界面上显示当前主题以及提供一个按钮来切换主题。

1. 首先创建一个 Context：

```jsx
import { createContext, useContext, useState } from 'react';

// 创建一个共享的 Context
const ThemeContext = createContext();

// 创建一个自定义的 Hook，以便在组件中方便地访问 Context
function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within a ThemeProvider');
  }
  return context;
}

export { ThemeContext, useTheme };
```

2. 创建一个 `ThemeProvider` 组件来提供 Context 值：

```jsx
import { ThemeContext } from './ThemeContext';

function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');

  // 切换主题的函数
  function toggleTheme() {
    setTheme((prevTheme) => (prevTheme === 'light' ? 'dark' : 'light'));
  }

  return (
    // 通过 ThemeContext.Provider 将值传递给子组件
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

export default ThemeProvider;
```

3. 使用 `ThemeProvider` 封装应用，并在子组件中使用 `useTheme`：

```jsx
import { useTheme } from './ThemeContext';
import ThemeProvider from './ThemeProvider';

function App() {
  return (
    <ThemeProvider>
      <Content />
    </ThemeProvider>
  );
}

function Content() {
  const { theme, toggleTheme } = useTheme();

  return (
    <div>
      <h1>当前主题：{theme}</h1>
      <button onClick={toggleTheme}>切换主题</button>
    </div>
  );
}

export default App;
```

## useReducer(了解)

- **很多人看到useReducer的第一反应应该是redux的某个替代品，其实并不是**
- **useReducer仅仅是useState的一种替代方案：**
  - 在某些场景下，**如果state的处理逻辑比较复杂，我们可以通过useReducer来对其进行拆分**
  - 或者这次修改的state需要依赖之前的state时，也可以使用

![image-20230428120542234](.\React-images\image-20230428120542234-1683026306063-109.png)

![image-20230428120548234](.\React-images\image-20230428120548234-1683026306063-110.png)

- **数据是不会共享的，它们只是使用了相同的counterReducer的函数而已**
- **所以，useReducer只是useState的一种替代品，并不能替代Redux**

## useCallback

> - `useCallback` 是一个 React Hook，它的内在原理是通过记忆化一个函数，使其在特定的依赖项发生变化时才重新创建。这有助于避免不必要的重渲染和函数重新创建，从而提高性能。
>
> - `useCallback` 接收两个参数：一个函数和一个依赖项数组。依赖项数组是一个可选参数，它告诉 React 在哪些依赖发生变化时应该重新创建记忆化的函数。如果依赖项数组为空，那么函数将只会在组件挂载时创建一次，并在整个组件生命周期内保持不变。
>
> - 当我们使用 `useCallback` 时，**React 会将传入的函数与其依赖项数组一起存储在内部数据结构中。在组件的每次渲染期间，React 都会检查依赖项数组中的值是否发生了变化。如果依赖项没有发生变化，React 会返回之前创建的函数实例；如果依赖项发生了变化，React 将创建一个新的函数实例并返回**。
>
>   - React 内部使用了一种称为 "Fiber" 的数据结构来表示组件树。每个组件（包括函数式组件和类组件）都有一个与之关联的 Fiber 节点。这个 Fiber 节点包含了组件的状态、属性、以及与其他 Fiber 节点的关系等信息。
>
>     当我们在组件中使用 `useCallback` 时，React 会将传入的函数和依赖项数组存储在组件对应的 Fiber 节点上。每个 Hook 都有一个与之关联的存储位置，这个存储位置称为 "Hook State"。因此，`useCallback` 的函数和依赖项数组会被存储在组件的 Hook State 中。
>
>     在组件的每次渲染期间，React 会遍历组件的 Hook State。当遇到 `useCallback` 时，React 会将当前的依赖项数组与上一次渲染时的依赖项数组进行比较。如果两个数组中的值都没有发生变化，React 就会返回上一次创建的函数实例。**这是通过使用 JavaScript 中的 "闭包" 来实现的，上一次创建的函数实例在闭包中保持可访问**。
>
>     如果依赖项数组中的某个值发生了变化，React 会创建一个新的函数实例，并将其与新的依赖项数组一起存储在 Hook State 中。这样，下一次组件渲染时，React 可以使用新的函数实例和依赖项数组进行比较。
>
>     这个过程使得 `useCallback` 能够在不同渲染阶段之间跟踪函数和依赖项数组的变化，从而实现函数的记忆化。这有助于避免不必要的重渲染和函数创建，提高组件的性能。
>
> - `useCallback` 的主要目的是提高性能，特别是在处理较大的组件树时。通过记忆化函数，我们可以确保仅在必要时才进行函数重新创建和组件重新渲染，从而减少不必要的计算和渲染成本。需要注意的是，`useCallback` 并不总是提高性能，如果依赖项经常发生变化或者函数本身相对简单，过度使用 `useCallback` 反而可能导致性能下降。因此，在使用 `useCallback` 时，请确保仅在确实需要避免不必要的重渲染和函数创建时才使用。

- **useCallback实际的目的是为了进行性能的优化**

- **如何进行性能的优化呢？**

  - useCallback会**返回一个函数的 memoized（记忆的） 值**
  - 在**依赖不变的情况下，多次定义的时候，返回的值是相同**的
  - 它用于将一个函数包裹在一个记忆化的版本中，只有当依赖项数组中的一个值发生变化时，这个函数才会重新创建。这样可以避免组件重新渲染时频繁创建新的函数实例，从而优化性能

  ```jsx
  const memoizedCallback = useCallback(
    () => {
      // function body
    },
    [dependency1, dependency2, ...],
  );
     //memoizedCallback 是一个记忆化的函数，当依赖项数组中的值发生变化时，它会重新创建。如果依赖项数组保持不变，那么这个函数在组件的多次渲染之间将保持相同的实例
  ```

- **案例**

  - 案例一：使用useCallback和不使用useCallback定义一个函数是否会带来性能的优化

    - 使用 `useCallback` 的确可以在某些情况下带来性能优化。`useCallback` 可以帮助你避免在组件重新渲染时创建不必要的新函数实例。当你将一个函数作为依赖传递给其他 hooks（例如 `useEffect`）或者将函数作为属性（props）传递给子组件时，使用 `useCallback` 是有意义的。因为在这些情况下，如果函数实例发生了改变，可能会导致不必要的重新渲染或者副作用的执行。

      例如，假设你有一个子组件 `Child`，它接收一个名为 `onButtonClick` 的函数属性（prop）。如果你在父组件中直接定义 `onButtonClick` 函数，那么每次父组件重新渲染时，`onButtonClick` 函数都会创建一个新的实例。这可能会导致 `Child` 组件不必要的重新渲染，因为它接收到了一个新的 `onButtonClick` 函数实例。在这种情况下，使用 `useCallback` 可以帮助你避免这个问题

  - 案例二：使用useCallback和不使用useCallback定义一个函数传递给子组件是否会带来性能的优化

    - 因为不使用useCallback的话，函数传递给子组件每次都是新的，会每次都引起子组件的渲染，而使用了useCallback，则传递的都会是之前的函数，不会引起这些不必要的渲染

  ```jsx
  import React, { useState, useCallback } from 'react';
  
  function App() {
    // 使用useState管理count和value状态
    const [count, setCount] = useState(0);
    const [value, setValue] = useState('');
  
    // 使用useCallback创建一个记忆化的increment函数
    // 当count发生变化时，increment将被重新创建
    const increment = useCallback(() => {
      setCount(count + 1);//这里捕获到的count其实是之前第一个count，也就是闭包
    }, [count]);
  
    // 使用useCallback创建一个记忆化的handleChange函数
    // 由于没有依赖项，handleChange在组件的整个生命周期内将保持相同的实例
    const handleChange = useCallback((event) => {
      setValue(event.target.value);
    }, []);
  
    return (
      <div>
        <p>Count: {count}</p>
        <button onClick={increment}>Increment</button>
        <input value={value} onChange={handleChange} />
      </div>
    );
  }
  ```

- **通常使用useCallback的目的是不希望子组件进行多次渲染，并不是为了函数进行缓存**

- 在以下情况下，使用 `useCallback` 可能不会带来明显的性能优化：

  1. 如果你定义的函数不会作为属性（prop）传递给子组件，或者不会作为依赖传递给其他 hooks，那么使用 `useCallback` 可能是没有意义的。
  2. 如果你的组件本身渲染性能良好，或者重新渲染的代价较低，那么使用 `useCallback` 可能带来的性能优化也是有限的

> `useRef` 是一个 Hook，它允许你在组件中创建和访问一个可变的引用对象。这个引用对象通常用于保存一个 DOM 元素的引用，或者保存一个在组件的多次渲染间需要保持不变的值。`useRef` 返回的引用对象具有一个 `current` 属性，用于存储和访问这个引用的值。

```jsx
import React, { useRef, useEffect } from 'react';

function TextInput() {
  // 使用 useRef 创建一个引用对象，并将其赋值给 inputRef
  const inputRef = useRef(null);

  useEffect(() => {
    // 使用 inputRef.current 访问 input 元素的引用
    // 将焦点设置到 input 元素上
    inputRef.current.focus();
  }, []);

  return (
    <div>
      {/* 将 inputRef 关联到 input 元素上，使其能引用到 DOM 元素 */}
      <input type="text" ref={inputRef} />
    </div>
  );
}
```

- 除了用于引用 DOM 元素之外，`useRef` 还可以用于存储在组件多次渲染间需要保持不变的值：
  - 这里，`countRef` 用于在计时器回调函数中保持对当前 `count` 值的引用。由于回调函数在创建时闭包捕获了 `count` 的值，如果不使用 `countRef`，回调函数将始终访问到初始值，而不是最新的 `count` 值

```jsx
import React, { useState, useRef, useEffect } from 'react';

function Timer() {
  const [count, setCount] = useState(0);
  // 使用 useRef 创建一个引用对象，并将其赋值给 countRef
  const countRef = useRef(count);

  useEffect(() => {
    // 将 count 的值存储在 countRef.current 中
    countRef.current = count;

    // 每秒更新一次计数器
    const timer = setInterval(() => {
      setCount(countRef.current + 1);
    }, 1000);

    // 清除计时器，以避免内存泄漏
    return () => clearInterval(timer);
  }, []);

  return (
    <div>
      <p>Count: {count}</p>
    </div>
  );
}
```

## useMemo

> `useMemo` 是一个 React Hook，它允许你在组件渲染期间缓存函数的返回值。使用 `useMemo` 可以在某些情况下帮助你优化性能，特别是当计算量大或者依赖项变化较少时

- useMemo实际的目的也是为了进行性能的优化
- 如何进行性能的优化呢？
  - useMemo返回的也是一个 memoized（记忆的） 值
  - 在依赖不变的情况下，多次定义的时候，返回的值是相同的

```jsx
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

- `useMemo` 接收两个参数：一个函数和一个依赖项数组。当依赖项数组中的任何值发生变化时，它会重新执行传入的函数并缓存返回值。如果依赖项没有发生变化，那么 `useMemo` 会直接返回上一次计算的值，避免了重复的计算

> 使用 `useMemo` 的简单示例，其中计算斐波那契数列：

```jsx
import React, { useState, useMemo } from "react";

// 定义一个计算斐波那契数列的递归函数
function fibonacci(num) {
  if (num <= 1) return 1;
  return fibonacci(num - 1) + fibonacci(num - 2);
}

function App() {
  // 使用 useState 管理 number 状态
  const [number, setNumber] = useState(1);

  // 使用 useMemo 缓存 fibonacci 函数的返回值
  // 只有当 number 发生变化时，才会重新计算斐波那契数
  const fib = useMemo(() => fibonacci(number), [number]);

  return (
    <div>
      <h1>Fibonacci number for {number} is {fib}</h1>
      {/* 点击按钮时，更新 number 值 */}
      <button onClick={() => setNumber(number + 1)}>Increase number</button>
    </div>
  );
}

export default App;
```

## useRef

- 它可以用来创建一个可变的引用，这个引用的值不会在组件重新渲染时发生改变。`useRef` 返回一个 ref 对象，它有一个名为 `current` 的属性，可以存储和访问引用的值

- 最常用的ref是两种用法：

  - 用法一：引入DOM（或者组件，但是需要是class组件）元素

    - 访问和修改 DOM 元素：通过将 ref 对象的 `current` 属性赋给 DOM 元素的 `ref` 属性，可以在组件中访问和修改 DOM 元素

    ```jsx
    import React, { useRef } from 'react';
    
    function TextInputWithFocusButton() {
      const inputEl = useRef(null);
      const onButtonClick = () => {
        // 当按钮被点击时，使 input 元素获得焦点
        inputEl.current.focus();
      };
      
      return (
        <>
          <input ref={inputEl} type="text" />
          <button onClick={onButtonClick}>Focus the input</button>
        </>
      );
    }
    ```

  - 用法二：保存一个数据，这个对象在整个生命周期中可以保存不变

    - 存储可变值：`useRef` 可以用来存储一个在组件生命周期内持久存在且不会触发组件重新渲染的值。这对于在组件中跟踪一些变量，而不需要触发组件更新时非常有用

    ```jsx
    import React, { useState, useEffect, useRef } from 'react';
    
    function Timer() {
      const [timer, setTimer] = useState(0);
      const countRef = useRef(timer);
    
      useEffect(() => {
        countRef.current = timer;
      }, [timer]);
    
      useEffect(() => {
        const interval = setInterval(() => {
          setTimer((timer) => timer + 1);
        }, 1000);
    
        return () => {
          clearInterval(interval);
        };
      }, []);
    
      const alertCurrentTimer = () => {
        alert(`Current Timer: ${countRef.current}`);
      };
    
      return (
        <div>
          <div>Timer: {timer}</div>
          <button onClick={alertCurrentTimer}>Alert Current Timer</button>
        </div>
      );
    }
    ```

## useImperativeHandle(了解)

> `useImperativeHandle` 是一个 React Hook，它允许你在父组件中自定义一个子组件的实例方法。这在某些特殊场景下非常有用，例如当你需要暴露子组件内部的 DOM 操作或其他方法给父组件时。通常情况下，我们不推荐过多地使用 `useImperativeHandle`，因为它破坏了组件的封装性

- **我们先来回顾一下ref和forwardRef结合使用：**
  - 通过forwardRef可以将ref转发到子组件
  - 子组件拿到父组件中创建的ref，绑定到自己的某一个元素中
- **forwardRef的做法本身没有什么问题，但是我们是将子组件的DOM直接暴露给了父组件：**
  - 直接暴露给父组件带来的问题是某些情况的不可控
  - 父组件可以拿到DOM后进行任意的操作
  - 但是，事实上在上面的案例中，我们只是希望父组件可以操作的focus，其他并不希望它随意操作
- **通过useImperativeHandle可以值暴露固定的操作：**
  - 通过useImperativeHandle的Hook，将传入的ref和useImperativeHandle第二个参数返回的对象绑定到了一起
  - 所以在父组件中，使用 inputRef.current时，实际上使用的是返回的对象
  - 比如我调用了 focus函数，甚至可以调用 printHello函数
- `useImperativeHandle` 接受三个参数：
  1. 一个 ref，通常是从父组件传入的 ref。
  2. 一个返回包含自定义实例方法的对象的函数。
  3. 一个可选的依赖项数组，当数组中的任何变量发生改变时，将更新实例方法。

```jsx
// 引入 React 库和所需的 Hook 函数
import React, { useRef, useImperativeHandle, forwardRef } from 'react';

// 子组件 CustomInput
const CustomInput = (props, ref) => {
  // 创建一个 ref，用于引用 input 元素
  const inputRef = useRef();

  // 使用 useImperativeHandle 自定义父组件可以访问的实例方法
  useImperativeHandle(ref, () => ({
    // 定义一个名为 focus 的方法，使父组件能够调用它以聚焦 input 元素
    focus: () => {
      inputRef.current.focus();
    }
  }));

  // 返回 input 元素，并将 inputRef 与之关联
  return <input ref={inputRef} />;
};

// 使用 forwardRef 将 ref 传递给 CustomInput 子组件
const ForwardedCustomInput = forwardRef(CustomInput);

// 父组件 ParentComponent
function ParentComponent() {
  // 创建一个 ref，用于在父组件中引用子组件
  const inputRef = useRef();

  // 定义一个处理函数，当按钮被点击时，调用子组件的 focus 方法
  const handleButtonClick = () => {
    inputRef.current.focus();
  };

  // 渲染父组件，包括子组件 ForwardedCustomInput 和一个按钮
  return (
    <div>
      // 将 inputRef 传递给子组件，以便在父组件中调用子组件的方法
      <ForwardedCustomInput ref={inputRef} />
      // 当按钮被点击时，触发 handleButtonClick 函数
      <button onClick={handleButtonClick}>Focus Input</button>
    </div>
  );
}
```

## useLayoutEffect(了解)

> `useLayoutEffect` 接受两个参数：
>
> 1. 一个副作用函数：在这个函数中执行与 DOM 相关的操作。这个函数可以返回一个清理函数，用于在组件卸载或重新渲染之前执行清理操作。
> 2. 一个依赖数组：指定仅当某些值发生变化时才重新执行副作用。如果省略依赖数组，副作用将在每次渲染后执行。

- **useLayoutEffect看起来和useEffect非常的相似，事实上他们也只有一点区别而已：**
  - useEffect会在渲染的内容更新到DOM上后执行，不会阻塞DOM的更新；
  - useLayoutEffect会在渲染的内容更新到DOM上之前执行，会阻塞DOM的更新
  - `useLayoutEffect` 用于执行与 DOM 相关的副作用，例如获取 DOM 元素的尺寸或修改 DOM 属性。与 `useEffect` 相比，`useLayoutEffect` 在组件的生命周期中更早地执行，即在浏览器执行绘制之前
- 当你需要在浏览器渲染之前执行一些操作以避免闪烁或视觉差异时，可以使用 `useLayoutEffect`。但是，请注意，`useLayoutEffect` 会阻塞浏览器渲染，因此在性能关键的应用程序中需要谨慎使用
- **如果我们希望在某些操作发生之后再更新DOM，那么应该将这个操作放到useLayoutEffect**
- **案例： useEffect和useLayoutEffect的对比**

![image-20230429045931434](.\React-images\image-20230429045931434-1683026306063-112.png)

```jsx
import React, { useState, useLayoutEffect, useRef } from 'react';

function Example() {
  const [count, setCount] = useState(0);
  const divRef = useRef();

  useLayoutEffect(() => {
    // 当 count 变化时，修改 div 元素的背景色
    divRef.current.style.backgroundColor = count % 2 === 0 ? 'red' : 'blue';

    // 在控制台输出调试信息
    console.log('useLayoutEffect executed');
  }, [count]);

  return (
    <div>
      <div ref={divRef}>{count}</div>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

- 在这个例子中，我们使用 `useLayoutEffect` 在每次 `count` 变化时修改一个 div 元素的背景颜色。因为我们使用了 `useLayoutEffect` 而不是 `useEffect`，所以背景颜色的更改将在浏览器渲染之前完成，从而避免了闪烁现象

## 自定义Hook

- **自定义Hook本质上只是一种函数代码逻辑的抽取，以便在其他组件中重用。严格意义上来说，它本身并不算React的特性**
  - 函数名称必须以 “use” 开头。
  - 自定义 Hook 可以调用其他内置的 Hook（例如 useState, useEffect 等）。

```jsx
import { useState, useEffect } from 'react';

// 自定义 Hook：useFetch
function useFetch(url) {
  // 使用 useState 保存数据和加载状态
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  // 使用 useEffect 来处理副作用（获取数据）
  useEffect(() => {
    setLoading(true);

    // 请求数据
    fetch(url)
      .then((response) => response.json())
      .then((data) => {
        // 更新数据和加载状态
        setData(data);
        setLoading(false);
      });
  }, [url]); // 当 url 变化时，重新执行副作用

  // 返回数据和加载状态
  return { data, loading };
}

// 使用自定义 Hook 的组件
function App() {
  // 使用 useFetch 自定义 Hook 获取数据
  const { data, loading } = useFetch('https://api.example.com/data');

  // 根据加载状态渲染组件
  if (loading) {
    return <div>Loading...</div>;
  }

  // 显示 API 数据
  return (
    <div>
      <h1>Data from API:</h1>
      <pre>{JSON.stringify(data, null, 2)}</pre>
    </div>
  );
}
```

- 在所有的组件在创建和销毁时都进行打印的案例
  - 创建了一个名为 `useLogLifeCycle` 的自定义 Hook，用于在组件创建和销毁时进行打印。`useEffect` 用于处理副作用，当组件创建时打印创建信息，同时返回一个函数，它将在组件销毁时执行，打印组件销毁的信息。我们在 `ComponentA` 和 `ComponentB` 中使用这个自定义 Hook，实现在所有组件创建和销毁时进行打印

```jsx
import { useEffect } from 'react';

// 自定义 Hook：useLogLifeCycle
function useLogLifeCycle(componentName) {
  // 使用 useEffect 来处理组件的创建和销毁
  useEffect(() => {
    // 打印组件创建时的信息
    console.log(`Component ${componentName} is created`);

    // 返回一个函数，它将在组件销毁时执行
    return () => {
      // 打印组件销毁时的信息
      console.log(`Component ${componentName} is destroyed`);
    };
  }, [componentName]); // 当组件名变化时，重新执行副作用
}

// 使用自定义 Hook 的组件
function ComponentA() {
  // 使用 useLogLifeCycle 自定义 Hook 记录组件的生命周期
  useLogLifeCycle('ComponentA');

  return <div>Component A</div>;
}

function ComponentB() {
  // 使用 useLogLifeCycle 自定义 Hook 记录组件的生命周期
  useLogLifeCycle('ComponentB');

  return <div>Component B</div>;
}
```

> 自定义Hook完成一个localStorage数据存储的案例

```jsx
// useLocalStorage.js
import { useState, useEffect } from 'react';

// 自定义Hook: useLocalStorage
function useLocalStorage(key, initialValue) {
  // 使用useState设置一个名为storedValue的状态，初始值从localStorage中获取
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error(error);
      return initialValue;
    }
  });

  // setValue用于更新状态并将新值存储在localStorage中
  const setValue = (value) => {
    try {
      // 允许value成为函数，以便我们可以传递prevState
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.error(error);
    }
  };

  // 返回数组，包含存储的值和用于更新值的函数
  return [storedValue, setValue];
}

export default useLocalStorage;
```

## redux hooks

> 在使用之前，需要通过命令安装一下对应的包
>
> ```jsx
> npm install @reduxjs/toolkit react-redux
> //就算是使用了hook也是需要连接的，将react和redux连接在一起，所以react-redux这个包还是必不可少的
> ```

- **在之前的redux开发中，为了让组件和redux结合起来，我们使用了react-redux中的connect：**
  - 但是这种方式必须使用高阶函数结合返回的高阶组件
  - 并且必须编写：mapStateToProps和 mapDispatchToProps映射的函数
- **在Redux7.1开始，提供了Hook的方式，我们再也不需要编写connect以及对应的映射函数了**
- **useSelector的作用是将state映射到组件中：**
  - 参数一：将state映射到需要的数据中
  - 参数二：可以进行比较来决定是否组件重新渲染
- **useSelector默认会比较我们返回的两个对象是否相等**
  - 如何比较呢？ const refEquality = (a, b) => a === b
  - 也就是我们必须返回两个完全相等的对象才可以不引起重新渲染
- **useDispatch非常简单，就是直接获取dispatch函数，之后在组件中直接使用即可**
- **我们还可以通过useStore来获取当前的store对象**

### 旧写法

> 首先回顾之前的方法：

首先，让我们创建Redux store的三个主要部分：action、reducer和store。

1. actions.js - 包含action类型和action创建器：

```jsx
// actions.js
// 定义action类型
export const INCREMENT = 'INCREMENT';
export const DECREMENT = 'DECREMENT';

// 定义action创建器
export const increment = () => ({
  type: INCREMENT,
});

export const decrement = () => ({
  type: DECREMENT,
});
```

2. reducer.js - 包含我们的reducer函数：

```jsx
// reducer.js
import { INCREMENT, DECREMENT } from './actions';

const initialState = {
  count: 0,
};

// 定义reducer函数
export const counterReducer = (state = initialState, action) => {
  switch (action.type) {
    case INCREMENT:
      return { ...state, count: state.count + 1 };
    case DECREMENT:
      return { ...state, count: state.count - 1 };
    default:
      return state;
  }
};
```

3. store.js - 创建Redux store：

```jsx
// store.js
import { createStore } from 'redux';
import { counterReducer } from './reducer';

export const store = createStore(counterReducer);
```

接下来，我们需要在我们的React应用程序中设置`<Provider>`，以便我们的组件可以访问Redux store。

4. 在`index.js`文件中，将`<Provider>`组件包裹在我们的`<App>`组件外面：

```jsx
// index.js
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import { store } from './store';
import App from './App';

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```

现在我们可以使用Redux Hooks（`useSelector`和`useDispatch`）在我们的组件中访问和更新store。

5. 在`App.js`文件中，创建一个简单的计数器组件：

```jsx
// App.js
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement } from './actions';

function App() {
  // 使用useSelector Hook从Redux store中获取count值
  const count = useSelector((state) => state.count);

  // 使用useDispatch Hook获取dispatch函数以触发actions
  const dispatch = useDispatch();

  // 定义increment和decrement操作
  const handleIncrement = () => {
    dispatch(increment());
  };

  const handleDecrement = () => {
    dispatch(decrement());
  };

  return (
    <div className="App">
      <h1>使用Redux Hooks的计数器示例</h1>
      <h2>计数：{count}</h2>
      <button onClick={handleIncrement}>增加</button>
      <button onClick={handleDecrement}>减少</button>
    </div>
  );
}

export default App;
```

在这个例子中，我们使用了Redux Hooks（`useSelector`和`useDispatch`）来访问和更新Redux store。`useSelector` Hook用于从store中获取当前计数值，而`useDispatch` Hook用于获取`dispatch`函数，以便我们可以触发actions。当用户点击“增加”或“减少”按钮时，我们分别调用`handleIncrement`和`handleDecrement`函数来分派`increment`和`decrement` actions。这将触发reducer函数，更新store中的`count`值。

总结一下，我们创建了一个简单的计数器应用，使用了Redux Hooks来访问和更新Redux store。通过`useSelector` Hook，我们可以轻松地从store中获取数据，而通过`useDispatch` Hook，我们可以分派actions来更新store的状态。这使得我们的组件更简洁、可读性更强，并使我们能够更好地管理应用程序的状态。

### 新写法toolkit版本

> 使用Redux Toolkit来创建一个简单的待办事项应用。需要确保项目中安装了`redux`, `react-redux`和`@reduxjs/toolkit`库

首先，让我们创建Redux store的主要部分：slices和store。

1. todoSlice.js - 包含我们的slice，它会自动处理action创建器和reducer。

```jsx
// todoSlice.js
import { createSlice } from '@reduxjs/toolkit';

// 使用createSlice创建一个名为todos的slice
const todoSlice = createSlice({
  name: 'todos',
  initialState: [],
  reducers: {
    // 添加待办事项的reducer
    addTodo: (state, action) => {
      state.push(action.payload);
    },
    // 移除待办事项的reducer
    removeTodo: (state, action) => {
      return state.filter((todo) => todo.id !== action.payload);
    },
  },
});

// 从slice中导出action创建器
export const { addTodo, removeTodo } = todoSlice.actions;

// 导出reducer
export default todoSlice.reducer;
```

2. store.js - 创建Redux store：

```JSX
// store.js
import { configureStore } from '@reduxjs/toolkit';
import todoReducer from './todoSlice';

// 使用configureStore创建Redux store
export const store = configureStore({
  reducer: {
    todos: todoReducer,
  },
});
```

3. 在`index.js`文件中，将`<Provider>`组件包裹在我们的`<App>`组件外面：

```JSX
// index.js
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import { store } from './store';
import App from './App';

// 使用Provider组件包裹App组件，使Redux store能被应用内组件访问
ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```

4. 在`App.js`文件中，创建一个简单的待办事项组件：

```jsx
// App.js
import React, { useState } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { addTodo, removeTodo } from './todoSlice';

function App() {
  const [newTodo, setNewTodo] = useState('');
  // 使用useSelector获取todos状态
  const todos = useSelector((state) => state.todos);
  // 使用useDispatch获取dispatch函数
  const dispatch = useDispatch();

  // 添加待办事项
  const handleAddTodo = () => {
    if (newTodo.trim()) {
      dispatch(
        addTodo({
          id: Date.now(),
          title: newTodo.trim(),
        })
      );
      setNewTodo('');
    }
  };

  // 移除待办事项
  const handleRemoveTodo = (id) => {
    dispatch(removeTodo(id));
  };

  return (
    <div className="App">
      <h1>使用Redux Toolkit的待办事项应用示例</h1>
      <input
        type="text"
        placeholder="添加新待办事项"
        value={newTodo}
        onChange={(e) => setNewTodo(e.target.value)}
      />
      <button onClick={handleAddTodo}>添加</button>
      <ul>
        {todos.map((todo) => (
          <li key={todo.id}>
            {todo.title}
            <button onClick={() => handleRemoveTodo(todo.id)}>移除</button>
          </li>
        ))}
      </ul>
    </div>
  );
}

export default App;
```

现在这个待办事项应用示例中，我们使用了Redux Toolkit以简化Redux store的创建和管理。`createSlice`函数自动处理了action创建器和reducer的生成，使代码更简洁。同时，`configureStore`函数简化了store的创建过程，并包含了Redux DevTools扩展的集成。

在`App.js`中，我们使用`useSelector` Hook从Redux store中获取`todos`状态。通过`useDispatch` Hook，我们获得了`dispatch`函数，用于分派action。`handleAddTodo`函数会在输入框非空时创建一个新的待办事项并分派`addTodo` action。`handleRemoveTodo`函数则负责分派`removeTodo` action以删除指定ID的待办事项。

整个示例展示了如何使用Redux Toolkit和React Redux Hooks构建一个简单的待办事项应用。这种方式使得代码更简洁，便于维护和理解。同时，Redux Toolkit提供的一些功能和最佳实践帮助我们更高效地管理应用程序的状态。

### 新方法Hook版本 - useSelector

> 在这个示例中，我们创建了一个简单的用户列表应用。我们使用`createSlice`函数创建了一个名为`users`的slice，其中包含了初始用户数据。然后，我们在`App.js`文件中使用`useSelector` Hook从Redux store中获取`users`状态。
>
> 在`App`组件中，我们将`users`数组中的每个用户渲染为列表项。这个示例展示了如何使用`useSelector` Hook简洁地从Redux store中获取数据。这使得我们的组件更简洁、可读性更强，并有助于更好地管理应用程序的状态

- userSlice.js - 创建一个包含初始用户数据的用户slice。

```jsx
// userSlice.js
import { createSlice } from '@reduxjs/toolkit';

const userSlice = createSlice({
  name: 'users',
  initialState: [
    { id: 1, name: 'Alice' },
    { id: 2, name: 'Bob' },
  ],
  reducers: {
    // 没有额外的reducer，因为我们只是展示用户列表
  },
});

// 导出reducer
export default userSlice.reducer;
```

- store.js - 创建Redux store：

```jsx
// store.js
import { configureStore } from '@reduxjs/toolkit';
import userReducer from './userSlice';

export const store = configureStore({
  reducer: {
    users: userReducer,
  },
});

```

- 在`index.js`文件中，将`<Provider>`组件包裹在我们的`<App>`组件外面：

```jsx
// index.js
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import { store } from './store';
import App from './App';

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```

- 在`App.js`文件中，创建一个简单的用户列表组件：

```jsx
// App.js
import React from 'react';
import { useSelector } from 'react-redux';

function App() {
  // 使用useSelector获取users状态
  const users = useSelector((state) => state.users);

  return (
    <div className="App">
      <h1>用户列表示例</h1>
      <ul>
        {/* 遍历users数组并显示列表 */}
        {users.map((user) => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
}

export default App;
```

![image-20230429161224509](.\React-images\image-20230429161224509-1683026306063-113.png)

> 创建一个简单的计数器应用，演示如何使用`useDispatch` Hook 分派actions。
>
> 我们创建了一个简单的计数器应用。使用`createSlice`函数创建了一个名为`counter`的slice，其中包含了两个reducer（`increment`和`decrement`）。在`App.js`文件中，我们使用`useSelector` Hook 从Redux store中获取`counter`状态，使用`useDispatch` Hook 获取`dispatch`函数。
>
> 在`App`组件中，创建了两个按钮。`handleIncrement`函数使用`dispatch`分派`increment` action，使计数器加一。`handleDecrement`函数使用`dispatch`分派`decrement` action，使计数器减一。

1. counterSlice.js - 创建一个计数器slice：

```jsx
// counterSlice.js
import { createSlice } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: 'counter',
  initialState: 0,
  reducers: {
    increment: (state) => state + 1,
    decrement: (state) => state - 1,
  },
});

// 从slice中导出action创建器
export const { increment, decrement } = counterSlice.actions;

// 导出reducer
export default counterSlice.reducer;
```

2. store.js - 创建Redux store：

```jsx
// store.js
import { configureStore } from '@reduxjs/toolkit';
import counterReducer from './counterSlice';

export const store = configureStore({
  reducer: {
    counter: counterReducer,
  },
});
```

3. 在`index.js`文件中，将`<Provider>`组件包裹在我们的`<App>`组件外面：

```jsx
// index.js
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import { store } from './store';
import App from './App';

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```

4. 在`App.js`文件中，创建一个简单的计数器组件：

```jsx
// App.js
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement } from './counterSlice';

function App() {
  // 使用useSelector获取counter状态
  const counter = useSelector((state) => state.counter);
  // 使用useDispatch获取dispatch函数
  const dispatch = useDispatch();

  // 使用dispatch分派increment action
  const handleIncrement = () => {
    dispatch(increment());
  };

  // 使用dispatch分派decrement action
  const handleDecrement = () => {
    dispatch(decrement());
  };

  return (
    <div className="App">
      <h1>计数器示例</h1>
      <h2>当前计数: {counter}</h2>
      <button onClick={handleIncrement}>增加</button>
      <button onClick={handleDecrement}>减少</button>
    </div>
  );
}

export default App;
```

### shallowEqual

- `shallowEqual` 是一个用于浅层比较两个对象的实用函数。通常在React和Redux中使用，以提高性能。它通过比较两个对象的顶层属性是否相等来确定它们是否相等。顶层属性的值必须严格相等，即使用 `===` 运算符比较，但它不会深入检查嵌套对象的属性。

- `shallowEqual` 的工作原理是，如果两个对象的顶层属性都具有相同的值（使用 `===` 比较），则认为这两个对象是相等的。如果它们的任何顶层属性具有不同的值，那么这两个对象被认为是不相等的。

```jsx
// 定义shallowEqual函数，接受两个对象作为参数
function shallowEqual(objA, objB) {
  // 如果两个对象引用相同（完全相等），则返回true
  if (objA === objB) {
    return true;
  }

  // 如果两个对象中的任何一个不是对象类型或者是null，则它们不可能相等，返回false
  if (
    typeof objA !== 'object' ||
    objA === null ||
    typeof objB !== 'object' ||
    objB === null
  ) {
    return false;
  }

  // 获取objA和objB的所有属性名
  const keysA = Object.keys(objA);
  const keysB = Object.keys(objB);

  // 如果两个对象的属性数量不同，则它们不可能相等，返回false
  if (keysA.length !== keysB.length) {
    return false;
  }

  // 遍历objA的属性名
  for (let i = 0; i < keysA.length; i++) {
    // 如果objB没有objA当前的属性，或者它们具有相同属性名但值不相等（使用严格相等运算符比较）
    // 则认为两个对象不相等，返回false
    if (!objB.hasOwnProperty(keysA[i]) || objA[keysA[i]] !== objB[keysA[i]]) {
      return false;
    }
  }

  // 如果上面的循环完成，没有提前返回false，则认为两个对象浅层相等，返回true
  return true;
}
```

- 在React和Redux中，`shallowEqual` 常用于性能优化。例如，在 `shouldComponentUpdate` 生命周期方法或 `React.memo` 中，可以使用 `shallowEqual` 来避免不必要的重新渲染。在Redux中，`shallowEqual` 常用于 `useSelector` Hook的第二个参数，以减少不必要的组件更新。

举个例子，假设我们有一个 `User` 组件，它接受一个 `user` 对象作为属性：

```jsx
import React from 'react';
import { useSelector, shallowEqual } from 'react-redux';

function User() {
  const user = useSelector((state) => state.user, shallowEqual);

  // ...组件逻辑...
}
```

> 在这个例子中，我们使用 `shallowEqual` 作为 `useSelector` 的第二个参数。这样，当新的 `user` 对象与当前的 `user` 对象在顶层属性上相等时，组件不会重新渲染。这有助于提高应用程序的性能，特别是在大型应用程序中。
>
> 请注意，`shallowEqual` 只能检查对象的顶层属性。如果你的数据结构嵌套较深或具有复杂的关系，可能需要使用更复杂的比较方法，例如深度比较。然而，在许多情况下，浅层比较足以提高性能，而无需牺牲代码可读性和可维护性。

在实际应用中，`shallowEqual` 的使用场景并不仅限于 `useSelector`。例如，当我们使用 `React.memo` 对功能组件进行优化时，也可以利用 `shallowEqual`。以下是一个简单的例子：

```jsx
import React from 'react';
import { shallowEqual } from 'react-redux';

const User = React.memo(function User({ user }) {
  // ...组件逻辑...
}, shallowEqual);
```

- 在这个例子中，我们使用 `React.memo` 和 `shallowEqual` 高阶组件包装了 `User` 组件。这样，当传递给组件的 `user` 属性的顶层属性没有改变时，组件将不会重新渲染。这同样有助于提高应用程序的性能。

- 需要注意的是，在使用 `shallowEqual` 时，一定要确保我们的数据结构不会产生不必要的嵌套。如果数据结构中包含大量的嵌套对象，那么使用 `shallowEqual` 可能无法达到预期的性能优化效果。在这种情况下，需要评估是否需要进行深度比较，或者尝试优化数据结构以减少嵌套。

- 所以`shallowEqual` 是一个在React和Redux中常用的性能优化工具。它通过浅层比较两个对象的顶层属性来避免不必要的重新渲染，从而提高了应用程序的性能。在使用 `shallowEqual` 时，请确保您的数据结构适合进行浅层比较，并注意权衡性能优化和代码可读性之间的关系。

## SSR-SPA页面的缺陷和SSR的优点

> SPA（Single Page Application，单页应用）是一种Web应用程序或网站的设计模式，它在一个单一的HTML页面上加载所有必要的代码（HTML、CSS和JavaScript）。当用户与应用程序进行交互时，SPA动态地更新页面内容，而不是加载新页面。这样可以提供流畅的用户体验，减少服务器的负载。
>
> SSR（Server Side Rendering，服务器端渲染）是指在服务器端将应用程序或网站的内容生成为HTML字符串，然后将这些HTML字符串发送到客户端。客户端接收到完整的HTML页面，并在浏览器中呈现。这样可以加速首次页面加载，提高搜索引擎优化（SEO）。
>
> ### SPA的缺陷
>
> 1. 首次加载时间较长：由于所有的资源（HTML、CSS和JavaScript）都在一个页面上加载，首次加载时间可能较长，尤其是在网络连接较慢的情况下。
> 2. SEO不佳：由于SPA在客户端动态生成内容，搜索引擎可能无法正确抓取和索引页面内容。这可能会导致SEO排名较低。
> 3. JavaScript依赖：SPA的正常运行依赖于JavaScript。如果用户禁用了JavaScript或浏览器不支持，应用程序将无法工作。
>
> ### SSR的优点
>
> 1. 更快的首次加载：服务器端渲染的页面在浏览器接收到HTML后即可开始呈现，无需等待所有资源加载完成。这可以缩短首次加载时间，提供更好的用户体验。
> 2. 更好的SEO：服务器端渲染的页面已经包含了所有内容，搜索引擎可以直接抓取和索引。这有助于提高网站在搜索结果中的排名。
> 3. 无需JavaScript即可运行：即使在禁用JavaScript或浏览器不支持的情况下，服务器端渲染的页面仍然可以显示内容。这有助于提高可访问性和兼容性。
>
> 需要注意的是，虽然SSR有这些优点，但它也带来了一些负面影响。例如，服务器端渲染可能会增加服务器的负担，因为它需要处理更多的渲染请求。此外，服务器端渲染的页面可能在交互性方面稍逊于SPA，因为页面内容在服务器端生成，可能导致页面更新的延迟。
>
> 总之，在选择SPA和SSR时，需要根据项目需求和目标来权衡。如果关注用户体验和交互性，SPA可能是更好的选择；如果关注首次加载速度和SEO，SSR可能更适合。许多现代Web框架（如React、Vue和Angular）支持将SPA和SSR结合使用，

### useId

- **官方的解释：useld是一个用于生成横跨服务端和客户端的稳定的唯一ID的同时避免hydration不匹配的hook.**
- **这里有一个词叫hydration,要想理解这个词，我们需要理解一些服务器端渲染(SSR)的概念(上面)。**
- 早期的服务端渲染包括PHP、JSP、ASP等方式，但是在目前前后端分离的开发模式下，前端开发人员不太可能再去学习PHP、JSP等技术来开发网页；
- 不过我们可以借助于Node来帮助我们执行JavaScript代码，提前完成页面的渲染

![image-20230502124627597](.\React-images\image-20230502124627597-1683026306063-111.png)

## SSR同构应用

- **什么是同构？**
  - 一套代码既可以在服务端运行又可以在客户端运行，这就是同构应用
- **同构是一种SSR的形态，是现代SSR的一种表现形式**
  - 当用户发出请求时，先在服务器通过SSR渲染出首页的内容
  - 但是对应的代码同样可以在客户端被执行
  - 执行的目的包括事件绑定等以及其他页面切换时也可以在客户端被渲染

![image-20230502124722228](.\React-images\image-20230502124722228-1683026306063-114.png)

![image-20230502124729003](.\React-images\image-20230502124729003-1683026306064-117.png)

### Hydration

> Hydration（或称为Rehydration）是一种前端Web开发中的概念，主要用于在服务器端渲染（SSR）的应用程序中。Hydration是在服务器端生成的静态HTML页面到达客户端后，使用JavaScript重新激活该页面，使其具有交互性的过程。
>
> 在服务器端渲染（SSR）应用程序中，服务器会生成一个包含完整内容的HTML页面，并将其发送到客户端。这样可以加快首次页面加载速度，并提高搜索引擎优化（SEO）。然而，要实现与客户端渲染（CSR）相同的交互性，需要在客户端运行JavaScript，使页面上的各种事件处理程序和动态内容得到处理。
>
> 当静态HTML页面到达客户端时，浏览器会解析并显示页面。接下来，应用程序会加载和执行与页面相关的JavaScript代码。在这个过程中，JavaScript会“接管”页面的DOM（文档对象模型）并与其进行交互。这个过程被称为Hydration，因为它实质上是“激活”了服务器端渲染的静态HTML页面，使其具有动态功能和交互性。
>
> Hydration的关键优势是提供更快的首次页面加载速度，同时保持与客户端渲染相同的交互性。通过在服务器端生成HTML页面并在客户端执行JavaScript，应用程序可以实现最佳的加载速度和用户体验。许多现代前端框架，如React、Vue和Angular，都支持Hydration，使开发人员能够轻松地构建具有高性能和交互性的应用程序。

- **什么是Hydration？这里引入vite-plugin-ssr插件的官方解释**

![image-20230502153413067](.\React-images\image-20230502153413067-1683026306063-115.png)

- **在进行 SSR 时，我们的页面会呈现为 HTML**
  - 但仅 HTML 不足以使页面具有交互性。例如，浏览器端 JavaScript 为零的页面不能是交互式的（没有 JavaScript 事件处理程序来响应用户操作，例如单击按钮）
  - 为了使我们的页面具有交互性，除了在 Node.js 中将页面呈现(指将页面结构生成出来，而不是渲染出来)为 HTML 之外，我们的 UI 框架（Vue/React/...）还在浏览器中加载和呈现页面。（它创建页面的内部表示，然后将内部表示映射到我们在 Node.js 中呈现的 HTML 的 DOM 元素。）

### useId的作用

- **我们再来看一遍：useId 是一个用于生成横跨服务端和客户端的稳定的唯一 ID 的同时避免 hydration 不匹配的 hook**
- **所以我们可以得出如下结论：**
  - useId是用于react的同构应用开发的，前端的SPA页面并不需要使用它
  - useId可以保证应用程序在客户端和服务器端生成唯一的ID，这样可以有效的避免通过一些手段生成的id不一致，造成hydration mismatch

![image-20230502155651525](.\React-images\image-20230502155651525-1683026306063-116.png)

### useTransition

> `useTransition` 是一个来自 `react-spring` 库的 React Hook，用于创建在列表或组件间切换时的平滑动画过渡。`useTransition` 可以处理进入、更新和离开过渡，为应用程序提供更自然和优雅的用户体验。

- **官方解释：返回一个状态值表示过渡任务的等待状态，以及一个启动该过渡任务的函数**
  - 事实上官方的说法，还是让人云里雾里，不知所云
- **useTransition到底是干嘛的呢？它其实在告诉react对于某部分任务的更新优先级较低，可以稍后进行更新**

> **基本语法**

```jsx
const transitions = useTransition(items, keys, config);
```

参数说明：

1. `items`：一个数组，表示要为其创建过渡的元素。通常，这是一个用于渲染列表项目的数组，但它也可以是单个组件。
2. `keys`：一个函数，用于为每个元素生成唯一键。这些键用于在重新渲染时识别元素，以便应用适当的过渡。通常，你可以使用项目的唯一属性（如 `id`）作为键。
3. `config`：一个配置对象，用于定义过渡动画的属性。配置对象可以包含以下属性：
   - `from`：一个对象，描述元素进入过渡的初始状态。
   - `enter`：一个对象，描述元素进入过渡的最终状态。
   - `update`（可选）：一个对象，描述元素更新过渡的最终状态。
   - `leave`：一个对象，描述元素离开过渡的最终状态。
   - 其他 `react-spring` 支持的动画属性。

`useTransition` 返回一个函数，它接受两个参数：一个是针对每个元素的样式对象，另一个是对应的元素。你可以使用这个函数来映射项目列表，并渲染带有动画效果的项目。

在实际应用中，你需要将元素包装在 `react-spring` 的 `animated` 组件中，并将 `useTransition` 生成的样式传递给该组件。这使得 `react-spring` 能够在组件上应用动画效果。

```jsx
//创建了一个简单的列表，点击按钮时会向列表添加项目。我们使用 useTransition 为列表项创建过渡效果
import React, { useState } from 'react';
import { animated, useTransition } from 'react-spring';

const App = () => {
  const [items, setItems] = useState([]);

  const transitions = useTransition(items, {
    keys: item => item.key,
    from: { opacity: 0 },
    enter: { opacity: 1 },
    leave: { opacity: 0 },
  });

  const addItem = () => {
    const newItem = { key: Date.now(), text: 'New Item' };
    setItems([...items, newItem]);
  };

  return (
    <div>
      <button onClick={addItem}>Add Item</button>
      <ul>
        {transitions((styles, item) => (
          <animated.li style={styles} key={item.key}>
            {item.text}
          </animated.li>
        ))}
      </ul>
    </div>
  );
};

export default App;
```

![image-20230502164811702](.\React-images\image-20230502164811702-1683026306064-118.png)

### useDeferredValue

> `useDeferredValue` 是一个 React Hook，用于延迟更新某个值，使得状态更新能够在长时间运行的计算中更加平滑。它通常用于优化性能和响应性，特别是在大型组件或列表中，其中某些更新可能导致卡顿或延迟。

- **官方解释：useDeferredValue 接受一个值，并返回该值的新副本，该副本将推迟到更紧急地更新之后**
- **在明白了useTransition之后，我们就会发现useDeferredValue的作用是一样的效果，可以让我们的更新延迟**

- `useDeferredValue` 的基本用法如下：

```jsx
const deferredValue = useDeferredValue(value, { timeoutMs: 200 });
```

参数说明：

1. `value`：要延迟更新的值。当 `value` 发生变化时，`useDeferredValue` 会在指定的超时时间内逐渐更新为新值。
2. `{ timeoutMs: 200 }`：一个配置对象，包含一个名为 `timeoutMs` 的属性，用于设置延迟更新的超时时间（以毫秒为单位）。在这个例子中，我们设置了200毫秒的超时。

`useDeferredValue` 返回一个新值，这个新值会在指定的超时时间内逐渐更新为原始值。你可以将这个延迟值用于状态更新、渲染等，从而减轻性能压力。

> - 以下是一个简单的 `useDeferredValue` 示例：
> - 我们创建了一个简单的文本输入框和搜索按钮。当用户输入文本并点击搜索按钮时，应用程序会执行一个耗时的搜索功能。为了避免在执行搜索时导致卡顿或延迟，我们使用 `useDeferredValue` 对搜索值进行了延迟处理。这样，在执行搜索功能时，搜索值将在指定的超时时间内逐渐更新，从而提高应用程序的响应性。

```jsx
import React, { useState, useDeferredValue } from 'react';

const App = () => {
  const [text, setText] = useState('');
  const [search, setSearch] = useState('');

  const deferredSearch = useDeferredValue(search, { timeoutMs: 200 });

  const handleTextChange = (e) => {
    setText(e.target.value);
  };

  const handleSearchClick = () => {
    setSearch(text);
  };

  // 模拟耗时计算
  const searchResults = expensiveSearchFunction(deferredSearch);

  return (
    <div>
      <input type="text" value={text} onChange={handleTextChange} />
      <button onClick={handleSearchClick}>Search</button>
      <div>{searchResults}</div>
    </div>
  );
};

const expensiveSearchFunction = (query) => {
  // 模拟耗时计算
  return `Results for: ${query}`;
};

export default App;
```