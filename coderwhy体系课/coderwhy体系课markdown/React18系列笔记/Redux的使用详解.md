# Redux的使用详解

> Redux 是一个独立于 React 的 JavaScript 库，用于管理应用程序的状态

## 理解JavaScript纯函数

- **函数式编程**中有一个非常重要的概念叫**纯函数**，JavaScript符合**函数式编程的范式**，所以也有纯函数的概念

  - 在**react开发中纯函数是被多次提及**的
  - 比如**react中组件就被要求像是一个纯函数**（为什么是像，因为还有class组件），**redux中有一个reducer的概念**，也是要求必须是一个纯函数
  - 所以**掌握纯函数对于理解很多框架的设计**是非常有帮助的

- **纯函数的维基百科定义：**

  - 在程序设计中，若一个函数**符合以下条件**，那么这个函数被称为纯函数：

  1. 此函数在**相同的输入值时**，需**产生相同的输出**
  2. 函数的**输出和输入值以外的其他隐藏信息或状态无关**，也和**由I/O设备产生的外部输出**无关
  3. 该函数**不能有语义上可观察的函数副作用**，诸如**“触发事件”，使输出设备输出，或更改输出值以外物件的内容**等

- **总结一下：**

  - 确定的输入，一定会产生确定的输出
  - 函数在执行过程中，不能产生副作用

### 副作用概念的理解

- 那么这里又有一个概念，叫做副作用，什么又是**副作用**呢？
  - 副作用（side effect）其实本身是医学的一个概念，比如我们经常说吃什么药本来是为了治病，可能会产生一些其他的副作用
  - 在计算机科学中，也引用了副作用的概念，表示在**执行一个函数**时，除了**返回函数值**之外，还对**调用函数产生了附加的影响**，比如**修改了全局变量，修改参数或者改变外部的存储**
- **纯函数在执行的过程中就是不能产生这样的副作用：**
  - 副作用往往是产生**bug的 “温床”**

### 纯函数的案例

> - 一个对数组操作的两个函数：
>   - slice：slice截取数组时不会对原数组进行任何操作,而是生成一个新的数组
>   - splice：splice截取数组, 会返回一个新的数组, 也会对原数组进行修改
> - **slice就是一个纯函数，不会修改数组本身，而splice函数不是一个纯函数；**

![image-20230411190606478](.\React-images\image-20230411190606478-1681234900431-20-1683026306063-57.png)

### 纯函数的作用和优势

- **为什么纯函数在函数式编程中非常重要呢？**
  - 因为你可以**安心的编写**和**安心的使用**
  - 你在**写的时候**保证了函数的纯度，只是**单纯实现自己的业务逻辑**即可，**不需要关心传入的内容**是如何获得的或者**依赖其他的外部变量**是否已经发生了修改
  - 你在**用的时候**，你确定**你的输入内容不会被任意篡改**，并且**自己确定的输入**，一定会**有确定的输出**
- React中就要求我们无论是**函数还是class声明一个组件**，这个组件都必须**像纯函数一样，保护它们的props不被修改**
- **redux中，reducer也被要求是一个纯函数**

## 为什么需要redux

- **JavaScript开发的应用程序，已经变得越来越复杂了：**

  - JavaScript需要**管理的状态越来越多，越来越复杂**
  - 这些状态包括**服务器返回的数据、缓存数据、用户操作产生的数据**等等，也包括一些**UI的状态**，比如**某些元素是否被选中，是否显示加载动效，当前分页**

- **管理不断变化的state是非常困难的：**

  - **状态之间相互会存在依赖，一个状态的变化会引起另一个状态的变化，View页面也有可能会引起状态的变化**
  - 当应用程序复杂时，**state在什么时候，因为什么原因而发生了变化，发生了怎么样的变化**，会变得非常**难以控制和追踪**

- **React是在视图层帮助我们解决了DOM的渲染过程，但是State依然是留给我们自己来管理：**

  - 无论是**组件定义自己的state**，还是**组件之间的通信通过props进行传递**；也包括**通过Context进行数据之间的共享**
  - React主要负责帮助我们**管理视图**，state如何维护最终**还是我们自己来决定**

  ```
  UI = render(state)
  ```

- **Redux就是一个帮助我们管理State的容器：Redux是`JavaScript的状态容器`，提供了`可预测的状态管理`**

- **Redux除了和React一起使用之外，它也可以和其他界面库一起来使用（比如Vue），并且它非常小（包括依赖在内，只有2kb）**

### Redux的核心理念 - Store

- **Redux的核心理念非常简单，比如我们有一个朋友列表需要管理：**
  - 如果我们没有定义统一的规范来操作这段数据，那么整个数据的变化就是无法跟踪的.
  - 比如页面的某处通过products.push的方式增加了一条数据
  - 比如另一个页面通过products[0].age = 25修改了一条数据
- **整个应用程序错综复杂，当出现bug时，很难跟踪到底哪里发生的变化**

![image-20230411193213045](.\React-images\image-20230411193213045-1681234900431-22-1683026306063-59.png)

### Redux的核心理念 - action

- **Redux要求我们通过action来更新数据：**
  - 所有数据的变化，必须通过**派发（dispatch）action来更新**
  - **action是一个普通的JavaScript对象**，用来描述这次**更新的type和content**
- **比如下面就是几个更新friends的action：**
  - 强制使用action的好处是可以**清晰的知道数据到底发生了什么样的变化，所有的数据变化都是可跟追、可预测的**
  - 当然，目前我们的**action是固定的对象**
  - 真实应用中，我们会**通过函数来定义，返回一个action**

![image-20230411194220933](.\React-images\image-20230411194220933-1681234900431-24-1683026306063-63.png)

### Redux的核心理念 - reducer

- **但是如何将state和action联系在一起呢？答案就是reducer**
  - reducer是一个**纯函数**；
  - reducer做的事情就是**将传入的state和action结合起来生成一个新的state**

> 在 Redux 中，reducer 是一个纯函数，它用于处理状态更改。reducer 接受当前的状态和一个 action 对象作为参数，然后根据 action 的类型返回新的状态。action 对象是一个描述状态更改的普通 JavaScript 对象，至少包含一个 `type` 属性。除了 `type` 属性，action 对象还可以包含其他任意属性，这些属性可以提供有关如何更改状态的信息。
>
> reducer 的主要职责如下：
>
> 1. 确定如何根据 action 类型更新状态。
> 2. 不修改传入的状态，而是创建并返回一个新的状态对象。

![image-20230411233912844](.\React-images\image-20230411233912844-1681234900431-18-1683026306063-61.png)

```jsx
const initialState = {
  counter: 0,
};

function counterReducer(state = initialState, action) {
  switch (action.type) {
    case 'INCREMENT':
      return { ...state, counter: state.counter + 1 };
    case 'DECREMENT':
      return { ...state, counter: state.counter - 1 };
    default:
      return state;
  }
}
//在此示例中，我们定义了一个名为 counterReducer 的 reducer，用于处理名为 counter 的状态属性。该 reducer 接受一个状态对象和一个 action 对象作为参数。我们使用 switch 语句根据 action 的 type 属性确定如何更新状态。

//对于 INCREMENT 类型的 action，我们返回一个新对象，其中 counter 属性的值增加 1。对于 DECREMENT 类型的 action，我们返回一个新对象，其中 counter 属性的值减少 1。对于其他类型的 action，我们返回原始状态，因为我们不需要对其进行任何更改。

//注意，reducer 不直接修改传入的状态对象。相反，它使用扩展运算符（...state）创建一个新对象，并将新的 counter 属性值添加到该对象中。这是因为 Redux 要求状态对象是不可变的，这意味着我们不能直接修改它们。通过始终返回新的状态对象，我们可以确保遵循这一原则。

//在大型应用程序中，可能需要处理多个状态属性。在这种情况下，可以创建多个 reducer，每个 reducer 负责处理一部分状态，然后使用 Redux 提供的 combineReducers 函数将它们组合成一个主 reducer。这有助于保持代码的模块化和可维护性。
```

#### Redux的三大原则

- **单一数据源**
  - 整个应用程序的**state被存储在一颗object tree中**，并且**这个object tree只存储在一个 store** 中
  - Redux并**没有强制让我们不能创建多个Store**，但是那样做并**不利于数据的维护**
  - 单一的数据源可以让整个应用程序的state变得方便维护、追踪、修改
- **State是只读的**
  - **唯一修改State的方法一定是触发action，不要试图在其他地方通过任何的方式来修改State**
  - 这样就确保了View或网络请求都**不能直接修改state**，它们**只能通过action来描述自己想要如何修改state**
  - 这样可以**保证所有的修改都被集中化处理**，并且**按照严格的顺序来执行**，所以**不需要担心race condition（竟态）的问题**
- **使用纯函数来执行修改**
  - 通过reducer将 **旧state和 actions联系在一起**，并且**返回一个新的State**
  - 随着**应用程序的复杂度增加**，我们可以**将reducer拆分成多个小的reducers，分别操作不同state tree的一部分**
  - 但是**所有的reducer都应该是纯函数**，不能产生任何的副作用

### Redux测试项目搭建

- 安装redux：

```
npm install redux --save 	或	 yarn add redux
```

1. **创建一个新的项目文件夹：learn-redux**

```
# 执行初始化操作
yarn init
# 安装redux
yarn add redux
```

2. **创建src目录，并且创建index.js文件**
3. **修改package.json可以执行index.js**

```json
"scripts": {
"start": "node src/index.js"
}
```

### Redux的使用过程

1. **创建一个对象，作为我们要保存的状态：**

2. **创建Store来存储这个state**

   - 创建store时必须创建reducer

   - 我们可以通过 store.getState 来获取当前的state

3. **通过action来修改state**

   - 通过dispatch来派发action
   - 通常action中都会有type属性，也可以携带其他的数据

4. **修改reducer中的处理代码**

   - 这里一定要记住，reducer是一个纯函数，不需要直接修改state

5. **可以在派发action之前，监听store的变化**

> 这个示例展示了使用 Redux 的基本过程。首先，我们定义了一个初始状态对象，然后创建了一个 reducer 函数来处理 action。接下来，我们使用 reducer 创建了一个 store，并获取了当前状态。在派发 action 之前，我们监听了 store 的变化。最后，我们创建了一个 action 对象，并使用 `dispatch` 方法将其传递给 store。这会导致 reducer 根据 action 的类型对状态进行更新

```jsx
// 1. 创建一个对象，作为我们要保存的状态
const initialState = {
  counter: 0,
};

// 2. 创建 Store 来存储这个 state
// 2.1 创建 reducer
const reducer = (state = initialState, action) => {
  switch (action.type) {
    case 'INCREMENT':
      return { ...state, counter: state.counter + 1 }; // 注意：不要直接修改 state，而是返回新的状态对象
    default:
      return state;
  }
};

// 2.2 使用 reducer 创建 store
const { createStore } = require('redux');
const store = createStore(reducer);

// 2.3 获取当前的 state
console.log('初始状态:', store.getState());

// 5. 可以在派发 action 之前，监听 store 的变化
store.subscribe(() => {
  console.log('状态已更新:', store.getState());
});

// 3. 通过 action 来修改 state
// 3.1 创建一个 action 对象，包含 type 属性和其他数据（这里没有其他数据）
const incrementAction = {
  type: 'INCREMENT',
};

// 3.2 通过 dispatch 来派发 action
store.dispatch(incrementAction);

// 4. 修改 reducer 中的处理代码
// 在这个例子中，我们已经在上面的 reducer 代码中完成了处理。详见第 2.1 步。
```

### Redux结构划分

> 1. **State（状态）**：State 是 Redux 应用程序中的单一数据源。它是一个包含应用程序整个状态的普通 JavaScript 对象。在 Redux 中，状态是只读的，我们不能直接修改它，而是通过派发 action 来更新状态。
> 2. **Action（动作）**：Action 是描述发生了什么（状态如何更改）的对象。它是一个包含 `type` 属性的普通 JavaScript 对象。`type` 通常是一个字符串常量，用于描述要执行的操作。除了 `type` 属性，action 对象还可以包含其他属性，以提供有关状态更改的详细信息。
> 3. **Reducer（处理器）**：Reducer 是一个纯函数，用于根据 action 类型处理状态更改。它接受当前状态和一个 action 对象作为参数，返回一个新的状态。我们不能直接修改传入的状态，而是创建一个新的状态对象。Reducer 的作用是将传入的状态和 action 组合成一个新的状态。
> 4. **Store（存储）**：Store 是 Redux 应用程序的核心，将 action、reducer 和 state 绑定在一起。它提供了几个重要的方法，如 `getState`（获取当前状态）、`dispatch`（派发 action）和 `subscribe`（订阅状态更改）。Store 通过将 action 分发给 reducer 来驱动应用程序的状态更新。

- **如果我们将所有的逻辑代码写到一起，那么当redux变得复杂时代码就难以维护**

  - 对代码进行拆分，将store、reducer、action、constants拆分成一个个文件
  - 创建store/index.js文件
  - 创建store/reducer.js文件
  - 创建store/actionCreators.js文件
  - 创建store/constants.js文件

- **注意：node中对ES6模块化的支持**

  - 从node v13.2.0开始，node才对ES6模块化提供了支持

  - node v13.2.0之前，需要进行如下操作

    ✓ 在package.json中添加属性： "type": "module"

    ✓ 在执行命令中添加如下选项：node --experimental-modules src/index.js

  - node v13.2.0之后，只需要进行如下操作：

    ✓ 在package.json中添加属性： "type": "module"

  - **注意：导入文件时，需要跟上.js后缀名**

> 在大型应用程序中，为了保持代码的可维护性和可读性，通常会将这些部分按照一定的目录结构进行组织。一个常见的目录结构如下：

```jsx
src/
├── actions/              // 存放 action 创建函数的文件夹
│   ├── actionTypes.js    // 定义 action 类型常量的文件
│   └── index.js          // 导出 action 创建函数的文件
├── reducers/             // 存放 reducer 函数的文件夹
│   ├── rootReducer.js    // 合并所有 reducer 的文件
│   └── ...               // 其他子 reducer 文件
├── store/                // 存放与 store 相关的配置
│   └── configureStore.js // 创建和配置 store 的文件
└── ...                   // 其他应用程序文件，如组件、容器等
```

### Redux使用流程

- **我们已经知道了redux的基本使用过程，那么我们就更加清晰来认识一下redux在实际开发中的流程：**

> | 流程图英文     | 翻译                          |
> | -------------- | ----------------------------- |
> | Central Store  | 中心数据                      |
> | Triggers       | 触发                          |
> | Subscription   | 订阅                          |
> | state as props | 将数据传递给组件(props的含义) |
> | Dispatches     | 派发                          |
> | Action         | 动作                          |
> | Reaches        | 达成                          |
> | Reducer        | 处理器                        |
> | Updates        | 更新                          |

![image-20230412002147728](.\React-images\image-20230412002147728-1681234900431-23-1683026306063-64.png)

### Redux官方图

![image-20230412011014641](.\React-images\image-20230412011014641-1681234900431-25-1683026306063-67.png)

### redux融入react代码

- **目前redux在react中使用是最多的，所以我们需要将之前编写的redux代码，融入到react当中去**
- 这里创建了两个组件：
  - Home组件：其中会展示当前的counter值，并且有一个+1和+5的按钮
  - Profile组件：其中会展示当前的counter值，并且有一个-1和-5的按钮
- **核心代码主要是两个：**
  - 在 componentDidMount 中定义数据的变化，当数据发生变化时重新设置 counter
  - 在发生点击事件时，调用store的dispatch来派发对应的action

### react-redux使用

- **开始之前需要强调一下，redux和react没有直接的关系，你完全可以在React, Angular, Ember, jQuery, or vanilla，JavaScript中使用Redux**
- **尽管这样说，redux依然是和React库结合的更好，因为他们是通过state函数来描述界面的状态，Redux可以发射状态的更新，让他们作出相应**
- **虽然我们之前已经实现了connect、Provider这些帮助我们完成连接redux、react的辅助工具，但是实际上redux官方帮助我们提供了 react-redux 的库，可以直接在项目中使用，并且实现的逻辑会更加的严谨和高效**

- 安装react-redux：

```jsx
yarn add react-redux
```

![image-20230412014114563](.\React-images\image-20230412014114563-1681234900431-27-1683026306063-65.png)

![image-20230412014033053](.\React-images\image-20230412014033053-1681234900431-26-1683026306063-66.png)

> 基础使用Redux，在store中的入口index文件和三个主要文件代码如下

```js
//index.js文件 --入口文件
// 引入 createStore 函数，用于创建 Redux Store
import { createStore } from "redux";

// 引入 reducer 函数
import reducer from "./reducer";

// 使用 createStore 函数和 reducer 创建一个 Store
const store = createStore(reducer);

// 将 Store 导出，以便在其他地方使用
export default store;

// 从 "./actionCreators" 模块导出所有内容
export * from "./actionCreators";
```

```js
//reducer.js文件 --处理数据的文件
// 引入 actionTypes 对象，其中包含所有的 action 类型常量
import * as actionTypes from "./constants";

// 定义默认状态
const defaultState = {
  counter: 0,
};

// 定义 reducer 函数，根据传入的 action 修改状态
function reducer(state = defaultState, action) {
  switch (action.type) {
    case actionTypes.ADD_NUMBER:
      // 当 action 类型为 ADD_NUMBER 时，将 counter 的值加上 action.num
      return { ...state, counter: state.counter + action.num };
    case actionTypes.SUB_NUMBER:
      // 当 action 类型为 SUB_NUMBER 时，将 counter 的值减去 action.num
      return { ...state, counter: state.counter - action.num };
    default:
      // 如果 action 类型未知，返回原始状态
      return state;
  }
}

// 导出 reducer 函数
export default reducer;
```

```js
//constants.js文件 --记录常量的文件
// 定义 ADD_NUMBER 常量，表示增加数字的 action 类型
export const ADD_NUMBER = "add_number";

// 定义 SUB_NUMBER 常量，表示减少数字的 action 类型
export const SUB_NUMBER = "sub_number";
```

```js
//actionCreators.js文件 --抽取出action部分重复逻辑的地方
// 引入 actionTypes 对象
import * as actionTypes from "./constants";

// 定义 addNumberAction 函数，创建一个带有 ADD_NUMBER 类型的 action 对象
export const addNumberAction = (num) => ({
  type: actionTypes.ADD_NUMBER,
  num,
});

// 定义 subNumberAction 函数，创建一个带有 SUB_NUMBER 类型的 action 对象
export const subNumberAction = (num) => ({
  type: actionTypes.SUB_NUMBER,
  num,
});
```

> connect()(Aonut)这样执行的原因：
>
> - 首先`connect`来自Redux中的一个函数，连续使用()()其实就是一种柯里化的操作，进行了分步骤操作
> - 第一个()的内容是填入映射的内容，第二个()是填入我们想要执行的组件或者类。将第一个()映射到第二个()中
>
> `connect` 高阶函数的好处：
>
> 1. **解耦**：`connect` 函数允许我们将组件与 Redux store 分开处理，使组件更加纯粹，不需要直接处理状态和行为。这有助于提高组件的可维护性和可测试性。
> 2. **性能优化**：`connect` 函数会自动为组件进行性能优化。当 store 中的状态发生变化时，`connect` 函数会根据 `mapStateToProps` 和 `mapDispatchToProps` 函数的结果来判断组件是否需要重新渲染。这样可以避免不必要的渲染，提高应用程序的性能。
> 3. **易于重用**：通过 `mapStateToProps` 和 `mapDispatchToProps` 函数，您可以轻松地选择将哪些状态和操作映射到组件的属性。这使得组件更容易在不同的上下文中重用，因为您可以根据需要更改映射。
> 4. **简化代码**：`connect` 函数可以让您将 Redux 状态和操作直接映射到组件属性，从而简化组件内部的代码。这使得组件更关注于 UI 和交互，而不必处理复杂的状态管理逻辑。
> 5. **易于测试**：由于 `connect` 函数将组件与 Redux store 分离，您可以更轻松地对组件进行单元测试。您只需为组件提供模拟的属性，而无需担心设置和测试整个 Redux store。

1. 创建一个 React 组件，我们将通过 `connect` 函数将其连接到 Redux store：

```jsx
// Counter.js
import React from 'react';

// 创建一个简单的计数器组件
function Counter({ counter, onIncrement, onDecrement }) {
  return (
    <div>
      <p>Counter: {counter}</p>
      <button onClick={onIncrement}>Increment</button>
      <button onClick={onDecrement}>Decrement</button>
    </div>
  );
}

export default Counter;
```

2. 使用 `connect` 高阶函数将组件连接到 Redux store：

```jsx
// CounterContainer.js
import { connect } from 'react-redux';
import Counter from './Counter';
import { addNumberAction, subNumberAction } from './actionCreators';

// 将 store 中的状态映射到组件的 props
const mapStateToProps = (state) => ({
  counter: state.counter,
});

// 将 dispatch 函数映射到组件的 props
const mapDispatchToProps = (dispatch) => ({
  onIncrement: () => dispatch(addNumberAction(1)),
  onDecrement: () => dispatch(subNumberAction(1)),
});

// 使用 connect 高阶函数将 mapStateToProps 和 mapDispatchToProps 映射到 Counter 组件
const CounterContainer = connect(mapStateToProps, mapDispatchToProps)(Counter);

export default CounterContainer;
```

3. 在应用程序的入口点包装根组件，并使用 `Provider` 组件提供 store：

```jsx
// index.js
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import store from './store';
import CounterContainer from './CounterContainer';

ReactDOM.render(
  <Provider store={store}>
    <CounterContainer />
  </Provider>,
  document.getElementById('root')
);
```

### react-redux源码导读

![image-20230413145059879](.\React-images\image-20230413145059879.png)

### 组件中异步操作

- **在之前简单的案例中，redux中保存的counter是一个本地定义的数据**
  - 我们可以直接通过同步的操作来dispatch action，state就会被立即更新
  - 但是真实开发中，**redux中保存的很多数据可能来自服务器**，我们需要进行**异步的请求**，再**将数据保存到redux**中
- **在之前学习网络请求的时候我们讲过，网络请求可以在class组件的componentDidMount中发送，所以我们可以有这样的结构：**

![image-20230413145202591](.\React-images\image-20230413145202591.png)

- **上面的流程图有一个缺陷：**
  - 我们必须**将网络请求的异步代码放到组件的生命周期中来完成**
  - 事实上，**网络请求到的数据也属于我们状态管理的一部分**，更好的一种方式应该是**将其也交给redux来管理**

![image-20230413145920040](.\React-images\image-20230413145920040.png)

- **但是在redux中如何可以进行异步的操作呢？**

  > 异步是在函数中进行的，但是dispatch派发的需要是一个对象，所以就用到了中间件

  - 使用**中间件（Middleware）**
  - 学习过Express或Koa框架的大佬们对中间件的概念一定不陌生
  - 在这类框架中，Middleware可以帮助我们**在请求和响应之间嵌入一些操作的代码**，比如cookie解析、日志记录、文件压缩等操作

### 理解中间件

- **redux也引入了中间件（Middleware）的概念：**

  - 这个中间件的目的是在**dispatch的action和最终达到的reducer之间，扩展一些自己的代码**
  - 比如**日志记录、调用异步接口、添加代码调试功能**等等

- **我们现在要做的事情就是发送异步的网络请求，所以我们可以添加对应的中间件：**

  - 这里官网**推荐的、包括演示的网络请求的中间件**是**使用 redux-thunk**

- **redux-thunk是如何做到让我们可以发送异步的请求呢？**

  - 我们知道，**默认情况下的dispatch(action)，action需要是一个JavaScript的对象**
  - redux-thunk可以让**dispatch(action函数)，action可以是一个函数**
  - 该函数会被调用，并且会传给**这个函数一个dispatch函数和getState函数**

  ✓ dispatch函数用于我们之后再次派发action

  ✓ getState函数考虑到我们之后的一些操作需要依赖原来的状态，用于让我们可以获取之前的一些状态

### 如何使用redux-thunk

1. **安装redux-thunk**

```js
yarn add redux-thunk
```

2. **在创建store时传入应用了middleware的enhance函数**

   - 通过applyMiddleware来结合多个Middleware, 返回一个enhancer
   - 将enhancer作为第二个参数传入到createStore中

   ![image-20230413152947968](.\React-images\image-20230413152947968.png)

3. **定义返回一个函数的action：**

   - 注意：这里不是返回一个对象了，而是一个函数
   - 该函数在dispatch之后会被执行

![image-20230413153008992](.\React-images\image-20230413153008992.png)

### redux-devtools

- **redux可以方便的让我们对状态进行跟踪和调试，那么如何做到呢？**
  - redux官网为我们**提供了redux-devtools的工具**
  - 利用这个工具，我们可以知道**每次状态是如何被修改的，修改前后的状态变化**等等
- **安装该工具需要两步：**
  - 第一步：在对应的浏览器中安装相关的插件（比如Chrome浏览器扩展商店中搜索Redux DevTools即可）
  - 第二步：在redux中继承devtools的中间件

![image-20230413194940385](.\React-images\image-20230413194940385.png)

> 通常这个工具在开发环境才打开，在上线之后(生产环境)就需要关掉，不然其他用户如果也下载这个插件并且F12打开了后台，就也能够看到这里面的数据
>
> - 反面案例：腾讯课堂(能够看到数据)
> - 正面案例：网易云(什么都看不到)

![image-20230413222118249](.\React-images\image-20230413222118249-1683026306063-68.png)

- 我们默认情况下自己的也是看不到的，需要打开，关键代码如下图

![image-20230413222219993](.\React-images\image-20230413222219993-1683026306063-70.png)

顺序步骤：

1. 安装插件

2. **配置 Redux store**：需要在创建 Redux store 时添加一些配置，以便 Redux DevTools 扩展可以访问我们的 store。

   在我们的项目中，找到创建 Redux store 的地方（通常是 `index.js` 或 `store.js` 文件），然后按照以下方式修改 `createStore` 函数：

```jsx
import { createStore, applyMiddleware, compose } from 'redux';
import rootReducer from './reducers';

const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;
const store = createStore(
  rootReducer,
  composeEnhancers(applyMiddleware(/* 如果有使用中间件，这里传入 */))
);

export default store;
```

这里的 `composeEnhancers` 函数会尝试使用 Redux DevTools 扩展的 `__REDUX_DEVTOOLS_EXTENSION_COMPOSE__` 函数，如果没有安装扩展，则使用 Redux 的默认 `compose` 函数。这样可以确保项目在没有安装 Redux DevTools 扩展的情况下也可以正常运行。

3. **重启开发服务器**：对项目配置进行更改后，请确保重新启动开发服务器以应用这些更改。

### Reducer代码拆分

- **为什么这个函数叫reducer？**

  - 目前我们的reducer：

    1. 当前这个reducer既有处理counter的代码，又有处理home页面的数据
    2. 后续counter相关的状态或home相关的状态会进一步变得更加复杂
    3. 我们也会继续添加其他的相关状态，比如购物车、分类、歌单等等
    4. 如果将所有的状态都放到一个reducer中进行管理，随着项目的日趋庞大，必然会造成代码臃肿、难以维护

  - **因此，我们可以对reducer进行拆分：**

    - 我们先抽取一个对counter处理的reducer
    - 再抽取一个对home处理的reducer
    - 将它们合并起来

  - **目前我们已经将不同的状态处理拆分到不同的reducer中，我们来思考：**

    - 虽然已经放到不同的函数了，但是这些函数的处理依然是在同一个文件中，代码非常的混乱
    - 另外关于reducer中用到的constant、action等我们也依然是在同一个文件中

    ![image-20230413232827191](.\React-images\image-20230413232827191-1683026306063-69.png)

#### combineReducers函数

- **目前我们合并的方式是通过每次调用reducer函数自己来返回一个新的对象**
- **事实上，redux给我们提供了一个`combineReducers函数`可以方便的让我们对多个reducer进行合并**

![image-20230413232931985](.\React-images\image-20230413232931985-1683026306063-71.png)

- **那么combineReducers是如何实现的呢？**
  - 事实上，它也是**将我们传入的reducers合并到一个对象**中，最终**返回一个combination的函数**（相当于我们之前的reducer函数了）
    - 帮助我们将多个 reducer 函数组合成一个单一的根 reducer 函数。这样可以让我们更容易地管理和拆分复杂的应用状态。
  - 在**执行combination函数的过程**中，它会**通过判断前后返回的数据是否相同来决定返回之前的state还是新的state**
  - **新的state会触发订阅者发生对应的刷新，而旧的state可以有效的阻止订阅者发生刷新**

#### combineReducers的实现原理

> 简化版`combineReducers`实现：
>
> 这个简化版的 `combineReducers` 函数接受一个 `reducers` 对象，并返回一个名为 `rootReducer` 的函数。`rootReducer` 函数在接收到新的 action 时，会遍历 `reducers` 对象中的每一个子 reducer，并分别调用它们。每个子 reducer 根据传入的 action 返回一个新的状态，这些新状态会被保存到一个名为 `nextState` 的对象中。最后，`rootReducer` 函数返回这个新的 `nextState` 对象，作为应用的新状态。
>
> 通过使用 `combineReducers`，我们可以将应用的状态划分为更小的模块，并通过不同的子 reducer 分别处理这些模块。这样可以让我们的代码更加模块化，易于理解和维护。
>
> ---
>
> 我们使用 `for...in` 循环来遍历 `reducers` 对象中的所有键。`for...in` 循环会遍历对象本身的属性，以及对象原型链上的属性。为了确保我们只处理对象本身的属性（子 reducer 函数），而不是原型链上的属性，我们使用 `reducers.hasOwnProperty(key)` 进行检查。这样，我们可以确保只处理对象自身的属性，不会误操作原型链上的属性。
>
> 这段代码的目的是确保在遍历 `reducers` 对象时，我们仅处理实际的子 reducer 函数，忽略掉可能存在于原型链上的属性。这样可以避免在处理过程中出现意外错误，确保仅对预期的子 reducer 函数进行调用。

```jsx
function combineReducers(reducers) {
  return function rootReducer(state = {}, action) {
    const nextState = {};

    // 遍历所有的子 reducer
    for (let key in reducers) {
      //会进行逻辑判断
      if (reducers.hasOwnProperty(key)) {
        // 调用每个子 reducer，并将返回的新状态保存到 nextState 对象中
        nextState[key] = reducers[key](state[key], action);
      }
    }

    return nextState;
  };
}
```



## 认识Redux Toolkit

**Redux Toolkit 是官方推荐的编写 Redux 逻辑的方法**

- 在前面我们学习Redux的时候应该已经发现，redux的编写逻辑过于的繁琐和麻烦
- 并且代码通常分拆在多个文件中（虽然也可以放到一个文件管理，但是代码量过多，不利于管理）
- Redux Toolkit包旨在成为编写Redux逻辑的标准方式，从而解决上面提到的问题
- 在很多地方为了称呼方便，也将之称为“RTK”

- **安装Redux Toolkit：**

```
npm install @reduxjs/toolkit react-redux
```

- **Redux Toolkit的核心API主要是如下几个：**
  - `configureStore`：包装createStore以提供简化的配置选项和良好的默认值。它可以自动组合你的 slice reducer，添加你提供的任何 Redux 中间件，redux-thunk默认包含，并启用 Redux DevTools Extension
  - `createSlice`：接受reducer函数的对象、切片名称和初始状态值，并自动生成切片reducer，并带有相应的actions
  - `createAsyncThunk`: 接受一个动作类型字符串和一个返回承诺的函数，并生成一个pending/fulfilled/rejected基于该承诺分派动作类型的 thunk

### 重构代码 – 创建counter的reducer

- **我们先对counter的reducer进行重构： 通过`createSlice`创建一个slice**

- **`createSlice`主要包含如下几个参数：**

  - **name**：用户标记slice的名词

    - 在之后的redux-devtool中会显示对应的名词

  - **initialState**：初始化值

    - 第一次初始化时的值

  - **reducers**：相当于之前的reducer函数

    - 对象类型，并且可以添加很多的函数

    - 函数类似于redux原来reducer中的一个case语句

    - 函数的参数：

      ✓ 参数一：state

      ✓ 参数二：调用这个action时，传递的action参数

  - **createSlice**返回值是一个对象，包含所有的actions

![image-20230412192128017](.\React-images\image-20230412192128017-1683026306063-73.png)

![image-20230414024651635](.\React-images\image-20230414024651635-1683026306063-81.png)

### 重构代码 – 创建home的reducer

![image-20230414030701971](.\React-images\image-20230414030701971-1683026306063-72.png)

- 解构优化

![image-20230414030744214](.\React-images\image-20230414030744214-1683026306063-74.png)

```jsx
const homeSlice = createSlice({
  name:"home",
  initialState:{
    banners:[],
    recommends:[]
  },
  reducers:{
    changeBanners(state){
      state.banner = []
    },
    changeRecommends(state){
      state.rencommends = []
    }
  }
})

export const {changeBanners,changeRecommends} = homeSlice.actions

export default homeSlice.reducer
```

### store的创建

- **configureStore用于创建store对象，常见参数如下：**
  - **reducer**，将slice中的reducer可以组成一个对象传入此处
  - **middleware**：可以使用参数，传入其他的中间件（自行了解）
  - **devTools**：是否配置devTools工具，默认为true

![image-20230414025328204](.\React-images\image-20230414025328204-1683026306063-75.png)

### Redux Toolkit的异步操作

- **在之前的开发中，我们通过redux-thunk中间件让dispatch中可以进行异步操作**
- **Redux Toolkit默认已经给我们继承了Thunk相关的功能：createAsyncThunk**

![image-20230414032006023](.\React-images\image-20230414032006023-1683026306063-76.png)

- **当createAsyncThunk创建出来的action被dispatch时，会存在三种状态：**
  - **pending**：action被发出，但是还没有最终的结果(正在执行也处于这个阶段)
  - **fulfilled**：获取到最终的结果（有返回值的结果）
  - **rejected**：执行过程中有错误或者抛出了异常
- **我们可以在createSlice的entraReducer(附加的Reducer)中监听这些结果：**

![image-20230414032045692](.\React-images\image-20230414032045692-1683026306063-80.png)

### extraReducer的另外一种写法

- **extraReducer还可以传入一个函数，函数接受一个builder参数**
  -  我们可以向builder中添加case来监听异步操作的结果：

![image-20230414033458864](.\React-images\image-20230414033458864-1683026306063-78.png)

- 第三中写法，在请求数据的时候直接dispatch更新数据

![image-20230414034223582](.\React-images\image-20230414034223582-1683026306063-77.png)

### Redux Toolkit的数据不可变性（了解）

- **在React开发中，我们总是会强调数据的不可变性：**
  - 无论是类组件中的state，还是redux中管理的state
  - 事实上在整个JavaScript编码过程中，数据的不可变性都是非常重要的
- **所以在前面我们经常会进行浅拷贝来完成某些操作，但是浅拷贝事实上也是存在问题的：**
  - 比如过大的对象，进行浅拷贝也会造成性能的浪费
  - 比如浅拷贝后的对象，在深层改变时，依然会对之前的对象产生影响
- **事实上Redux Toolkit底层使用了immerjs的一个库来保证数据的不可变性**
- 专门讲解immutable-js库的底层原理和使用方法：https://mp.weixin.qq.com/s/hfeCDCcodBCGS5GpedxCGg
- **为了节约内存，又出现了一个新的算法：Persistent Data Structure（持久化数据结构或一致性数据结构）**
  - 用一种数据结构来保存数据(尽可能复用相同的节点)
  - 当**数据被修改**时，会**返回一个对象**，但是**新的对象会尽可能的利用之前的数据结构而不会对内存造成浪费**

## 自定义connect函数

> 该 `connect` 函数是一个高阶函数，接收两个参数 `mapStateToProps` 和 `mapDispatchToProps`，并返回一个新的高阶组件函数。这个新的高阶组件函数接收一个组件作为参数，并返回一个新的类组件。这个新的类组件使用 React 的 `context` 特性来获取 `store` 对象，并通过 `mapStateToProps` 和 `mapDispatchToProps` 函数将 `store` 中的状态和 `dispatch` 函数传递给被包装的组件。这样，被包装的组件就可以访问到 `store` 中的状态和 `dispatch` 函数，而不需要在每个组件中手动调用 `store.getState()` 或 `store.dispatch()`。
>
> 其中涉及到一些 React 的生命周期函数，包括 `componentWillMount()` 和 `componentWillUnmount()`，以及一些 React 的特性，包括 `context` 和 `...props` 运算符等。

```jsx
// 高阶函数，接收一个组件作为参数
function connect(mapStateToProps, mapDispatchToProps) {
  // 返回一个新的高阶组件函数
  return function(WrappedComponent) {
    // 返回一个新的类组件
    return class extends React.Component {
      // 在构造函数中初始化 state
      constructor(props) {
        super(props);
        this.state = {};
      }

      // 在组件渲染之前调用
      componentWillMount() {
        // 获取 store 对象并订阅 store 的变化
        this.store = this.context.store;
        this.unsubscribe = this.store.subscribe(() => {
          // 每当 store 发生变化，重新计算 mapStateToProps 的结果并更新 state
          this.setState(mapStateToProps(this.store.getState()));
        });
      }

      // 在组件即将销毁时调用
      componentWillUnmount() {
        // 取消订阅 store
        this.unsubscribe();
      }

      // 渲染组件
      render() {
        // 获取当前的 state 和 dispatch 函数
        const stateProps = mapStateToProps(this.store.getState());
        const dispatchProps = mapDispatchToProps(this.store.dispatch);

        // 合并 stateProps 和 dispatchProps，传递给 WrappedComponent
        return <WrappedComponent {...this.props} {...stateProps} {...dispatchProps} />;
      }
    };

    // 在类组件中指定 store 对象的 contextType
    class.contextType = StoreContext;
  };
}

```

```js
//coderwhy版本
import {PureComponent} from "react";
import store from "../store";

export default function connect(mapStateToProps,mapDispatchToProps){

    return function (WrapperComponent){
        class NewComponents extends PureComponent{
            constructor(props) {
                super(props);
                this.state = mapStateToProps(store.getState())
            }

            componentDidMount() {
                this.unsubscribe = store.subscribe(() => {
                    this.setState(mapStateToProps(store.getState()))
                })
            }

            componentWillUnmount() {
                this.unsubscribe()
            }

            render() {
                const stateObj = mapStateToProps(store.getState())
                const dispatchObj = mapDispatchToProps(store.dispatch)
                return <WrapperComponent {...this.props} {...stateObj} {...dispatchObj} />
            }
        }
        return NewComponents
    }
}
```

### context处理store

- **但是上面的connect函数有一个很大的缺陷：依赖导入的store**
  - 如果我们将其封装成一个独立的库，需要依赖用于创建的store，我们应该如何去获取呢？

> 假设我们这是要发布到npm上的话，是无法让别人使用的，因为我们引入了store这个文件，而在别人的项目里面，store不一定就叫store

所以又创建了一个StoreContext文件，然后引入connect中替代了store，最后两个在导入入口文件index.js。这样使用的时候就可以直接从我们的hoc文件夹中导出StoreContext(导入到入口文件了)进行使用，将value传递进去

- 正确的做法是我们提供一个Provider，Provider来自于我们创建的Context，让用户将store传入到value中即可

- 实现解耦效果

- React的createContext是用来创建一个Context对象的方法，它可以在组件树中向下传递数据，避免了使用props逐层传递的繁琐过程。

  createContext的作用是创建一个包含默认值的Context对象。这个Context对象可以被Provider组件用来传递数据，也可以被Consumer组件用来接收数据。

  一般情况下，我们会在应用的根组件中使用createContext创建一个Context对象，并将它作为属性传递给需要访问共享数据的组件。这样就可以实现在组件之间共享数据的目的，而不需要通过props手动传递数据

![image-20230414220101511](.\React-images\image-20230414220101511-1683026306063-79.png)

![image-20230414220421902](.\React-images\image-20230414220421902-1683026306063-86.png)

![image-20230414220615278](.\React-images\image-20230414220615278-1683026306063-83.png)

```jsx
{/*最终代码*/}
// connect的参数:
// 参数一: 函数
// 参数二: 函数
// 返回值: 函数 => 高阶组件

// 引入React和PureComponent模块
import React,{PureComponent} from "react";

// 引入StoreContext和store模块
import {StoreContext} from "/context"
import store from "../store";

// 定义connect2函数
export default function connect2(mapStateToProps,mapDispatchToProps){

    // 返回一个函数，参数为WrapperComponents组件
    return function handleMapCpn(WrapperComponents){

        // 定义ConnectCpn组件，并继承自PureComponent
        class ConnectCpn extends PureComponent{

            // 构造函数，接受props和context参数
            constructor(props,context) {
                super(props);
                // 设置state为调用mapStateToProps方法后的结果
                this.state = mapStateToProps(context.getState())
            }
            
            // 组件挂载后执行的方法
            componentDidMount() {
                // 订阅store的状态变化事件，回调函数中调用setState方法更新状态
                this.unsubscribe = this.context.subscribe(() => {
                    this.setState(mapStateToProps(store.getState()))
                })
            }
            
            // 组件卸载前执行的方法，取消订阅状态变化事件
            componentWillUnmount() {
                this.unsubscribe()
            }
            
            // 渲染函数，返回WrapperComponents组件，并传入props和mapStateToProps和mapDispatchToProps方法的执行结果
            render() {
                return <WrapperComponents {...this.props}
                    {...mapStateToProps(this.context.getState())}
                    {...mapDispatchToProps(this.context.dispatch)}
                />
            }
        }

        // 设置ConnectCpn的contextType为StoreContext，使得ConnectCpn可以访问StoreContext中的属性
        ConnectCpn.contextType = StoreContext;
        
        // 返回ConnectCpn组件
        return ConnectCpn;
    }
}
```

> 展现的编程思想主要是高阶组件和单向数据流，通过高阶组件来对原组件进行包装，并注入store的状态和操作方法，从而实现数据共享和状态管理。同时，通过单向数据流的原则，保证了数据的一致性和可维护性

## 打印日志需求

- **中间件的目的是在redux中插入一些自己的操作：**
  - 比如我们现在有一个需求，在dispatch之前，打印一下本次的action对象，dispatch完成之后可以打印一下最新的store state
  - 也就是我们需要将对应的代码插入到redux的某部分，让之后所有的dispatch都可以包含这样的操作
- **如果没有中间件，我们是否可以实现类似的代码呢？ 可以在派发的前后进行相关的打印。**
- **但是这种方式缺陷非常明显：**
  - 首先，每一次的dispatch操作，我们都需要在前面加上这样的逻辑代码
  - 其次，存在大量重复的代码，会非常麻烦和臃肿
- **是否有一种更优雅的方式来处理这样的相同逻辑呢？**
  - 我们可以将代码封装到一个独立的函数中
- **但是这样的代码有一个非常大的缺陷：**
  - 调用者（使用者）在使用我的dispatch时，必须使用我另外封装的一个函数dispatchAndLog
  - 显然，对于调用者来说，很难记住这样的API，更加习惯的方式是直接调用dispatch

### 修改dispatch

- **事实上，我们可以利用一个hack一点的技术：Monkey Patching，利用它可以修改原有的程序逻辑**

  - Monkey Patching是指在运行时（runtime）动态修改一个代码对象（如module，class或function），以改变它的行为或增强它的功能的一种技术。

    Monkey Patching的实现方式通常是使用动态语言的一些特性，比如JavaScript的可以直接修改对象属性或函数的能力。通过Monkey Patching，我们可以在不更改原始代码的情况下，向已经存在的模块、类或函数中添加新的功能，或是改变其行为方式

- **我们对代码进行如下的修改：**

  - 这样就意味着我们已经直接修改了dispatch的调用过程
  - 在调用dispatch的过程中，真正调用的函数其实是dispatchAndLog

- **当然，我们可以将它封装到一个模块中，只要调用这个模块中的函数，就可以对store进行这样的处理：**

```jsx
function log(store){
  let next = store.dispatch
  function dispatchAndLog(action){
    console.log("当前派发的action",action)
    //真正派发的代码，使用之前的dispatch进行派发
    next(action)

    console.log("派发之后的结果",store.getState())

  }
  //monkey patch 猴补丁 => 串改现有的代码，对整体的执行逻辑进行修改
  store.dispatch = dispatchAndLog
}

log(store)
```

### thunk需求

- **redux-thunk的作用：**
  - 我们知道redux中利用一个中间件redux-thunk可以让我们的dispatch不再只是处理对象，并且可以处理函数
  - 那么redux-thunk中的基本实现过程是怎么样的呢？事实上非常的简单

- 我们又对dispatch进行转换，这个dispatch会判断传入的

```jsx
function patchThunk(store){
  let next = store.dispatch
  function dispatchAndThunk(action){
    if (typeof action === "function"){
      action(store.dispatch,store.getState)
    }else{
      next(action)
    }
  }
  
  store.dispatch = dispatchAndThunk
}
```

### 合并中间件

- **单个调用某个函数来合并中间件并不是特别的方便，我们可以封装一个函数来实现所有的中间件合并：**
  - store是我们需要绑定到的地方，middlewares则是我们需要绑定的中间件。进行了一个forEach遍历调用

![image-20230416031258578](.\React-images\image-20230416031258578-1683026306063-84.png)

- **理解一下上面操作之后，代码的流程：**

![image-20230416031452283](.\React-images\image-20230416031452283-1683026306063-82.png)

- **当然，真实的中间件实现起来会更加的灵活，这里我们仅仅做一个抛砖引玉，有兴趣可以参考redux合并中间件的源码流程**

## React中的state如何管理

- **学习了Redux用来管理我们的应用状态，并且非常好用（当然，你学会前提下，没有学会，那肯定难）**
- **目前我们已经主要学习了三种状态管理方式：**
  - 方式一：组件中自己的state管理
  - 方式二：Context数据的共享状态
  - 方式三：Redux管理应用状态
- **在开发中如何选择呢？**
  - 首先，这个没有一个标准的答案
  - 某些用户，选择将所有的状态放到redux中进行管理，因为这样方便追踪和共享
  - 有些用户，选择将某些组件自己的状态放到组件内部进行管理
  - 有些用户，将类似于主题、用户信息等数据放到Context中进行共享和管理
  - 做一个开发者，到底选择怎样的状态管理方式，是我们的工作之一，可以一个最好的平衡方式

- **Redux的作者有给出自己的建议：**

![image-20230416031702633](.\React-images\image-20230416031702633-1683026306063-91.png)

- **目前项目中coderwhy采用的state管理方案：**
  - UI相关的组件内部可以维护的状态(不通用，单个组件才用到的部分)，在组件内部自己来维护
  - 大部分需要共享的状态，都交给redux来管理和维护
  - 从服务器请求的数据（包括请求的操作），交给redux来维护
- **根据不同的情况会进行适当的调整**