# 非受控组件-高阶组件-Fragment-过渡动画

## 受控组件基本演练

- **在 HTML 中，表单元素（如< input >、 < textarea > 和 < select >）之类的表单元素通常自己维护 state，并根据用户输入进行更新**
- 而在 React 中，可变状态（mutable state）通常保存在组件的 **state** 属性中，并且**只能通过使用 setState()来更新**
  - 我们将两者结合起来，使**React的state成为“唯一数据源”**
  - 渲染表单的 **React 组件还控制着用户输入过程中表单发生的操作**
  - **被 React 以这种方式控制取值的表单输入元素**就叫做**“受控组件”**
- **由于在表单元素上设置了 value 属性，因此显示的值将始终为 this.state.value，这使得 React 的 state 成为唯一数据源**
- **由于 handleUsernameChange 在每次按键时都会执行并更新 React 的 state，因此显示的值将随着用户输入而更新**

![image-20230408025915725](.\React-images\image-20230408025915725-1683026306062-38.png)

### 受控组件的其他演练

> 受控组件是指需要通过组件的状态来控制输入值的表单元素。这些表单元素的值被称为“受控值”，因为它们的值由 React 控制
>
> 我们定义了一个受控的文本框组件 `ControlledInput`。该组件中的文本框元素通过 `value` 属性与一个状态值 `value` 关联起来。当表单提交时，我们使用 `handleSubmit` 函数来获取文本框的值，并将其输出到控制台中。同时，我们通过 `handleChange` 函数来更新状态值 `value`，以便在文本框中输入新的值
>
> 需要注意的是，受控组件的值由 React 控制，因此可以很容易地对输入值进行验证、格式化或限制输入。另外，使用受控组件可以避免一些潜在的安全风险，因为用户无法通过修改 DOM 来修改表单元素的值。因此，在开发应用程序时，建议使用受控组件来控制表单元素的值，以提高应用程序的可用性和安全性

```jsx
import { useState } from "react";

function ControlledInput() {
  const [value, setValue] = useState("");

  function handleChange(event) {
    setValue(event.target.value);
  }

  function handleSubmit(event) {
    event.preventDefault();
    console.log(value);
  }

  return (
    <form onSubmit={handleSubmit}>
      <input type="text" value={value} onChange={handleChange} />
      <button type="submit">Submit</button>
    </form>
  );
}
```

- **textarea标签**
  - texteare标签和input比较相似：
- **select标签**
  - select标签的使用也非常简单，只是它不需要通过selected属性来控制哪一个被选中，它可以匹配state的value来选中
- **处理多个输入**
  - 多处理方式可以像单处理方式那样进行操作，但是需要多个监听方法：
  - 这里我们可以使用ES6的一个语法：计算属性名（Computed property names）

### 非受控组件

> 在 React 中，非受控组件是指不需要通过组件的状态来控制输入值的表单元素，而是直接从 DOM 中获取输入值。这些表单元素的值被称为“非受控值”，因为 React 无法控制它们的值。相比之下，受控组件则需要通过组件的状态来控制输入值。

- **React推荐大多数情况下使用 受控组件 来处理表单数据：**
  - 一个**受控组件**中，表单数据是**由 React 组件来管理的**
  - 另一种替代方案是**使用非受控组件**，这时表单数据将**交由 DOM 节点**来处理
- **如果要使用非受控组件中的数据，那么我们需要使用 ref 来从DOM节点中获取表单数据**
  - 使用ref来获取input元素
- **在非受控组件中通常使用defaultValue来设置默认值**
- 同样，<input type="checkbox"> 和 <input type="radio"> 支持 defaultChecked，<select> 和 <textarea> 支持 defaultValue

**如何创建一个非受控的文本框组件：**

> 我们定义了一个非受控的文本框组件 `UncontrolledInput`。该组件中的文本框元素通过 `ref` 属性与一个 React 引用关联起来。当表单提交时，我们使用 `handleSubmit` 函数来获取文本框的值，并将其输出到控制台中
>
> 需要注意的是，非受控组件的值无法通过组件的状态来控制，因此如果需要对输入值进行验证或格式化，可能需要编写一些额外的逻辑来处理这些值。另外，非受控组件可能会导致一些潜在的安全风险，因为用户可以通过修改 DOM 来修改表单元素的值。因此，在开发应用程序时，建议使用受控组件来控制表单元素的值，以提高应用程序的可用性和安全性

```jsx
function UncontrolledInput() {
  const inputRef = useRef(null);

  function handleSubmit(event) {
    event.preventDefault();
    console.log(inputRef.current.value);
  }

  return (
    <form onSubmit={handleSubmit}>
      <input type="text" ref={inputRef} />
      <button type="submit">Submit</button>
    </form>
  );
}
```

## 认识高阶函数

- **高阶函数的维基百科定义：至少满足以下条件之一：**
  - 接受一个或多个函数作为输入
  - 输出一个函数
- **JavaScript中比较常见的filter、map、reduce都是高阶函数**
- **那么说明是高阶组件呢？**
  - 高阶组件的英文是 **Higher-Order Components**，简称为 **HOC**
  - 官方的定义：**高阶组件是参数为组件，返回值为新组件的函数**
- **我们可以进行如下的解析：**
  - 首先， **高阶组件 本身不是一个组件，而是一个函数**
  - 其次，**这个函数的参数是一个组件，返回值也是一个组件**

## 高阶组件的定义

> 高阶组件（Higher-Order Component，HOC）是一种函数，它接受一个组件作为输入，并返回一个新的组件作为输出。这个新的组件包装了原始组件，并且可以添加一些额外的功能或逻辑，例如：
>
> - 访问组件的状态或属性
> - 向组件传递其他属性或方法
> - 渲染条件组件
> - 处理错误或加载状态
> - 等等
>
> 优点：
>
> HOC 的主要目的是提供一种可重用的代码模式，以便在多个组件中共享功能或逻辑。通过将功能包装在 HOC 中，我们可以将它们从组件中分离出来，并且可以在多个组件中使用相同的功能，从而提高代码的可维护性和可重用性。
>
> 注意事项：
>
> HOC 是一种非常强大的工具，可以提高 React 应用程序的可维护性和可重用性。然而，如果不正确地使用 HOC，可能会导致代码复杂性和可读性的降低，因此建议在开发应用程序时，谨慎使用 HOC，并根据实际需要选择合适的解决方案

- **高阶组件的调用过程类似于这样：**

> higherOrderComponent就是一个高阶组件，我们传入的WrappendComponent是一个组件。经过内部从操作返回了EnhancedComponent

![image-20230409025332332](.\React-images\image-20230409025332332-1681234900430-1-1683026306062-43.png)

- **高阶函数的编写过程类似于这样：**

> 高阶组件并不是React API的一部分，它是基于React的组合特性而形成的设计模式
>
> 高阶组件在一些React第三方库中非常常见：
>
> - 比如redux中的connect；
> - 比如react-router中的withRouter；

![image-20230409025358062](.\React-images\image-20230409025358062-1681234900430-2-1683026306062-42.png)

- **组件的名称问题：**
  - 在ES6中，类表达式中类名是可以省略的
  - 组件的名称都可以通过displayName来修改

### 应用一 – props的增强

- **不修改原有代码的情况下，添加新的props**

```jsx
function enhanceProps(WrapperCpn,otherProps){
  return props => <WrapperCpn {...props} {...otherProps}/>
}
```

- **利用高阶组件来共享Context**

```jsx
function withUser(WrapperCpn){
  return props =>{
    return (
    <UserContext.Consumer>
        {
          value =>{
            return <WrapperCpn {...props} {...value}/>
          }
        }
    )
  }
}
```

![image-20230409034013747](.\React-images\image-20230409034013747-1681234900430-3-1683026306062-41.png)

### 应用二 – 渲染判断鉴权

- **在开发中，我们可能遇到这样的场景：**
  - 某些页面是必须用户登录成功才能进行进入
  - 如果用户没有登录成功，那么直接跳转到登录页面
- **这个时候，我们就可以使用高阶组件来完成鉴权操作：**

![image-20230409034818719](.\React-images\image-20230409034818719-1681234900430-4-1683026306062-44.png)

### 应用三 – 生命周期劫持

- **我们也可以利用高阶函数来劫持生命周期，在生命周期中完成自己的逻辑：**

> 其中下面这个类名是可以省略的，叫做匿名类。
>
> 类是有名字的，通过`类.name`获取

![image-20230409031303276](.\React-images\image-20230409031303276-1681234900431-7-1683026306063-46.png)

## 高阶函数的意义

- **我们会发现利用高阶组件可以针对某些React代码进行更加优雅的处理。**
- **其实早期的React有提供组件之间的一种复用方式是mixin，目前已经不再建议使用：**
  - Mixin 可能会**相互依赖，相互耦合，不利于代码维护**
  - **不同的Mixin中的方法可能会相互冲突**
  - Mixin非常多时，组件处理起来会比较麻烦，甚至还要为其做相关处理，这样会给代码造成滚雪球式的复杂性
- **当然，HOC也有自己的一些缺陷：**
  - HOC需要在**原组件上进行包裹或者嵌套，如果大量使用HOC**，将**会产生非常多的嵌套**，这**让调试变得非常困难**
  - HOC可以**劫持props，在不遵守约定的情况下也可能造成冲突**
- **Hooks的出现，是开创性的，它解决了很多React之前的存在的问题**
  - 比如this指向问题、比如hoc的嵌套复杂度问题等等

### ref的转发

- **在前面我们学习ref时讲过，ref不能应用于函数式组件：**

  - 因为函数式组件没有实例，所以不能获取到对应的组件对象

- **但是，在开发中我们可能想要获取函数式组件中某个元素的DOM，这个时候我们应该如何操作呢?**

  - 方式一：直接传入ref属性（错误的做法）
  - 方式二：通过forwardRef高阶函数；

  > `forwardRef` 是 React 提供的一种高阶函数，用于向子组件传递父组件的 `ref`。`forwardRef` 函数接受一个函数作为参数，该函数返回一个新的组件，该组件可以接受一个 `ref` 属性并将其传递给内部组件。

```jsx
import { forwardRef, useRef } from "react";

const MyInput = forwardRef((props, ref) => {
  return <input {...props} ref={ref} />;
});

export default function App() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <div>
      <MyInput type="text" ref={inputRef} />
      <button onClick={handleClick}>Focus</button>
    </div>
  );
}

//在上面的示例中，我们定义了一个名为 MyInput 的组件，它使用 forwardRef 函数将其接受的 ref 属性传递给内部的 input 元素。在 App 组件中，我们使用 useRef 钩子创建一个 inputRef 引用，并将其传递给 MyInput 组件。当用户单击按钮时，我们使用 inputRef.current.focus() 来将焦点设置在 input 元素上。

//需要注意的是，forwardRef 函数是一种非常强大的工具，可以帮助我们实现一些高级的组件模式。通过使用 forwardRef，我们可以将父组件的 ref 属性传递给内部组件，并从而实现更加灵活和可重用的组件。
```

### Portals的使用

- **某些情况下，我们希望渲染的内容独立于父组件，甚至是独立于当前挂载到的DOM元素中（默认都是挂载到id为root的DOM元素上的）**
- **Portal 提供了一种将子节点渲染到存在于父组件以外的 DOM 节点的优秀的方案：**
  - 第一个参数（child）是**任何可渲染的 React 子元素**，例如一个元素，字符串或 fragment
  - 第二个参数（container）是**一个 DOM 元素**，也就是准备挂载的位置

```jsx
ReactDOM.createPortal(child,container)
```

- **通常来讲，当你从组件的 render 方法返回一个元素时，该元素将被挂载到 DOM 节点中离其最近的父节点：**
- **然而，有时候将子元素插入到 DOM 节点中的不同位置也是有好处的：**

```jsx
render(){
  //React 挂载了一个新的 div，并且把子元素渲染其中
  return(
  	<div>
    	{this.props.children}
    </div>
  )
}
```

```jsx
render(){
  //React 并没有创建一个新的 div 它只是把子元素渲染到`domNode`中
  return ReactDOM.createPortal(
  	this.props.children,
    domNode
  )
}
```

#### Modal组件案例

- **比如说，我们准备开发一个Modal组件，它可以将它的子组件渲染到屏幕的中间位置：**
  - 步骤一：修改index.html添加新的节点
  - 步骤二：编写这个节点的样式
  - 步骤三：编写组件代码

```html
<div id="root"></div>
<!--新节点 -->
<div id="modal"></div>
```

```css
#modal {
  position:fixed;
  left: 50%;
  top: 50%;
  transform:translate(-50% -50%);
  background-color:red;
}
```

```jsx
class Modal extends PureComponent{
  constructor(props){
    super(props)
  }
  
  render(){
    return ReactDOM.createPortal(
    	this.props.children,
      document.getElementById("modal")
    )
  }
}
```

### fragment

- **在之前的开发中，我们总是在一个组件中返回内容时包裹一个div元素：**
  - 因为正常情况下不包裹是会报错的

![image-20230409161334011](.\React-images\image-20230409161334011-1681234900431-5-1683026306063-47.png)

- **我们又希望可以不渲染这样一个div应该如何操作呢？**
  - 使用Fragment
  - Fragment 允许你将子列表分组，而无需向 DOM 添加额外节点
  - 乍一看像Vue的Template，但其实在Vue3中对template并不是完全需要，属于可以写可以不写的程度，支持多个根。那是因为Vue3内部源码里面还有一个根`fragment`用来包裹了所有的内容

![image-20230409162145168](.\React-images\image-20230409162145168-1681234900431-6-1683026306062-45.png)

- **React还提供了Fragment的短语法：**
  - 它看起来像空标签 <> </>
  - 但是，如果我们需要在Fragment中添加key，那么就不能使用短语法

![image-20230409162643666](.\React-images\image-20230409162643666-1681234900431-8-1683026306063-49.png)

- 空标签是不能加key的，需要完整写法

![image-20230409163008873](.\React-images\image-20230409163008873-1681234900431-12-1683026306063-48.png)

![image-20230409163034114](.\React-images\image-20230409163034114-1681234900431-9-1683026306063-50.png)

## StrictMode

- **StrictMode 是一个用来突出显示应用程序中潜在问题的工具：**

  - 与 Fragment 一样，StrictMode 不会渲染任何可见的 UI
  - 它为其后代元素触发额外的检查和警告
  - 严格模式检查仅在开发模式下运行；它们不会影响生产构建

- **可以为应用程序的任何部分启用严格模式：**

  - 不会对 Header 和 Footer 组件运行严格模式检查
  - 但是，ComponentOne 和 ComponentTwo 以及它们的所有后代元素都将进行检查
  - 对部分代码会执行

  ![image-20230409163208301](.\React-images\image-20230409163208301-1681234900431-19-1683026306063-51.png)

### 严格模式检查的是什么？

> 在 React 中开启严格模式（strict mode）时，组件的 `constructor` 方法可能会被调用两次的原因如下：
>
> 1. 首次调用 `constructor`
>
> 在 React 严格模式下，当组件挂载时，React 会以严格模式重新运行整个组件，并且在运行过程中会执行两次 `constructor` 方法。第一次调用 `constructor` 是在组件实例化时进行的，用于初始化组件的状态、绑定事件等操作。
>
> 1. 第二次调用 `constructor`
>
> 第二次调用 `constructor` 是在组件渲染时进行的，用于检查组件是否正确设置了初始状态。在 React 严格模式下，如果组件的状态发生了不可预期的变化，或者组件的渲染结果与先前不同，React 将会重新渲染组件，并执行第二次 `constructor` 方法。
>
> 需要注意的是，React 在严格模式下调用 `constructor` 方法两次的行为是有意设计的，目的是为了帮助开发者检测组件状态的变化，并防止出现不可预期的错误。因此，在编写组件时，需要注意保证 `constructor` 方法的幂等性，即无论被调用多少次，其结果应该保持不变。

1. 识别不安全的生命周期：
2. 使用过时的ref API
3. 检查意外的副作用
   - 这个组件的constructor会被调用两次
   - 这是严格模式下故意进行的操作，让你来查看在这里写的一些逻辑代码被调用多次时，是否会产生一些副作用
   - 在生产环境中，是不会被调用两次的
4. 使用废弃的findDOMNode方法
   - 在之前的React API中，可以通过findDOMNode来获取DOM，不过已经不推荐使用了，可以自行学习演练一下

![image-20230409180713015](.\React-images\image-20230409180713015-1681234900431-10-1683026306063-53.png)

1. 检测过时的context API
   - 早期的Context是通过static属性声明Context对象属性，通过getChildContext返回Context对象等方式来使用Context的
   - 目前这种方式已经不推荐使用

# React过渡动画实现

## react-transition-group介绍

- **在开发中，我们想要给一个组件的显示和消失添加某种过渡动画，可以很好的增加用户体验**
- **当然，我们可以通过原生的CSS来实现这些过渡动画，但是React社区为我们提供了react-transition-group用来完成过渡动画**
- **React曾为开发者提供过动画插件 react-addons-css-transition-group，后由社区维护，形成了现在的 react-transition-group**
  - 这个库可以帮助我们方便的实现组件的 **入场** 和 **离场** 动画，使用时需要进行额外的安装：

```npm
# npm
npm install react-transition-group --save
# yarn
yarn add react-transition-group
```

- **react-transition-group本身非常小，不会为我们应用程序增加过多的负担**

## react-transition-group主要组件

- **react-transition-group主要包含四个组件：**
- `Transition`
  - 该组件是一个和平台无关的组件（不一定要结合CSS）
  - 在前端开发中，我们一般是结合CSS来完成样式，所以比较常用的是CSSTransition
- `CSSTransition`
  -  在前端开发中，通常使用CSSTransition来完成过渡动画效果
- `SwitchTransition`
  - 两个组件显示和隐藏切换时，使用该组件
- `TransitionGroup`
  - 将多个动画组件包裹在其中，一般用于列表中元素的动画

### CSSTransition

- **CSSTransition是基于Transition组件构建的：**
- **CSSTransition执行过程中，有三个状态：appear、enter、exit**
- **它们有三种状态，需要定义对应的CSS样式：**
  - 第一类，**开始状态**：对于的类是-appear、-enter、exit
  - 第二类：**执行动画**：对应的类是-appear-active、-enter-active、-exit-active
  - 第三类：**执行结束**：对应的类是-appear-done、-enter-done、-exit-done
- **CSSTransition常见对应的属性：**
  - **in：触发进入或者退出状态**
    - 如果添加了**unmountOnExit={true}**，那么该组件**会在执行退出动画结束后被移除掉**
    - **当in为true时，触发进入状态**，会添加-enter、-enter-acitve的class开始执行动画，当动画执行结束后，会移除两个class，并且添加-enter-done的class
    - **当in为false时，触发退出状态**，会添加-exit、-exit-active的class开始执行动画，当动画执行结束后，会移除两个class，并且添加-enter-done的class

### CSSTransition常见属性

- **classNames：动画class的名称**
  - 决定了在编写css时，对应的class名称：比如card-enter、card-enter-active、card-enter-done
- **timeout：**
  - 过渡动画的时间
- **appear：**
  - 是否在初次进入添加动画（需要和in同时为true）
- **unmountOnExit：退出后卸载组件**
- 其他属性可以参考官网来学习：https://reactcommunity.org/react-transition-group/transition
- **CSSTransition对应的钩子函数：主要为了检测动画的执行过程，来完成一些JavaScript的操作**
  - onEnter：在进入动画之前被触发
  - onEntering：在应用进入动画时被触发
  - onEntered：在应用进入动画结束后被触发

![image-20230409220859339](.\React-images\image-20230409220859339-1681234900431-11-1683026306063-54.png)

### SwitchTransition

- **SwitchTransition可以完成两个组件之间切换的炫酷动画：**
  - 比如我们有一个按钮需要在on和off之间切换，我们希望看到on先从左侧退出，off再从右侧进入
  - 这个动画在vue中被称之为 vue transition modes
  - react-transition-group中使用SwitchTransition来实现该动画
- **SwitchTransition中主要有一个属性：mode，有两个值**
  - in-out：表示新组件先进入，旧组件再移除
  - out-in：表示就组件先移除，新组件再进入
- **如何使用SwitchTransition呢？**
  - SwitchTransition组件里面要有CSSTransition或者Transition组件，不能直接包裹你想要切换的组件
  - SwitchTransition里面的CSSTransition或Transition组件不再像以前那样接受in属性来判断元素是何种状态，取而代之的是**key属性**

### TransitionGroup

- **当我们有一组动画时，需要将这些CSSTransition放入到一个TransitionGroup中来完成动画：**

![image-20230409221117979](.\React-images\image-20230409221117979-1681234900431-16-1683026306063-52.png)
