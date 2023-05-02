# React中如何编写CSS？

## 组件化天下的CSS

- **整个前端已经是组件化的天下：**
  - 而CSS的设计就不是为组件化而生的，所以在目前组件化的框架中都在需要一种合适的CSS解决方案
- **在组件化中选择合适的CSS解决方案应该符合以下条件：**
  - 可以编写局部css：css具备自己的具备作用域，不会随意污染其他组件内的元素
    - 模块化：CSS 解决方案应支持模块化，即每个组件的样式应该能够独立编写和维护，避免全局样式对组件造成影响。
    - 作用域隔离：选用的 CSS 解决方案应能确保组件之间的样式互不干扰。组件的样式应该限制在组件内部，避免对其他组件产生意外影响
  - 可以编写动态的css：可以获取当前组件的一些状态，根据状态的变化生成不同的css样式
  - 支持所有的css特性：伪类、动画、媒体查询等
  - 编写起来简洁方便、最好符合一贯的css风格特点
    - 可维护性：CSS 解决方案应具有良好的可维护性。当组件样式需要修改时，应能够轻松找到并更改相应的样式代码
    - 可扩展性：随着项目的增长，CSS 解决方案应具有良好的可扩展性，支持大量组件的样式管理和优化。
    - 性能优化：CSS 解决方案应考虑性能优化，例如按需加载组件样式、样式压缩等，以减小样式文件的体积，提高页面加载速度

## React中的CSS

- **事实上，css一直是React的痛点，也是被很多开发者吐槽、诟病的一个点**
- **在这一点上，Vue做的要好于React：**
  - Vue通过在.vue文件中编写 <style><style> 标签来编写自己的样式
  - 通过是否添加 scoped 属性来决定编写的样式是全局有效还是局部有效
  - 通过 lang 属性来设置你喜欢的 less、sass等预处理器
  - 通过内联样式风格的方式来根据最新状态设置和改变css
- **Vue在CSS上虽然不能称之为完美，但是已经足够简洁、自然、方便了，至少统一的样式风格不会出现多个开发人员、多个项目采用不一样的样式风格**
- **相比而言，React官方并没有给出在React中统一的样式风格：**
  - 由此，从普通的css，到css modules，再到css in js，有几十种不同的解决方案，上百个不同的库
  - 大家一致在寻找最好的或者说最适合自己的CSS方案，但是到目前为止也没有统一的方案

### 内联样式

> **内联样式是官方推荐的一种css样式的写法：**
>
> - style 接受一个采用小驼峰命名属性的 JavaScript 对象，，而不是 CSS 字符串
> - 并且可以引用state中的状态来设置相关的样式

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

{/*函数组件*/}
function App() {
  {/*我们创建了一个名为 App 的简单组件，其中包含一个带有内联样式的段落元素。我们定义了一个名为 inlineStyles 的 JavaScript 对象，其中包含了一些 CSS 属性和值。然后将这个对象传递给段落元素的 style 属性*/}
  const inlineStyles = {
    backgroundColor: 'lightblue',
    padding: '20px',
    borderRadius: '5px',
    fontSize: '18px',
    textAlign: 'center',
  };

  return (
    <div>
      <h1>Hello, React 18!</h1>
      <p style={inlineStyles}>This is a paragraph with inline styles.</p>
    </div>
  );
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);
```

> 我们将 `App` 组件从 `React.Component` 更改为从 `React.PureComponent` 继承。这意味着当组件的 props 或 state 发生浅层更改时，组件将不会重新渲染。
>
> 然而，在某些情况下，使用 `React.PureComponent` 可能会导致错误地跳过重新渲染。如果组件的 props 或 state 包含嵌套对象，`React.PureComponent` 可能无法检测到更改，因为它仅执行浅层对比。在这种情况下，我们可能需要手动实现 `shouldComponentUpdate` 或使用不可变数据结构来避免问题

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
{/*类组件*/}
class App extends React.Component {
  {/*在这个例子中，我们将 App 组件更改为从 React.Component 继承的类组件(使用PureComponent会更好~，性能优势在前面有写)。我们在 render 方法中定义了 inlineStyles 对象，并将其传递给段落元素的 style 属性。与之前的函数组件示例相比，其他部分保持不变*/}
  render() {
    const inlineStyles = {
      backgroundColor: 'lightblue',
      padding: '20px',
      borderRadius: '5px',
      fontSize: '18px',
      textAlign: 'center',
    };

    return (
      <div>
        <h1>Hello, React 18!</h1>
        <p style={inlineStyles}>This is a paragraph with inline styles in a class component.</p>
      </div>
    );
  }
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);
```

- **内联样式的优点:**
  - 内联样式, 样式之间不会有冲突
  - 可以动态获取当前state中的状态
- **内联样式的缺点：**
  - 写法上都需要使用驼峰标识
  - 某些样式没有提示
  - 大量的样式, 代码混乱
  - 某些样式无法编写(比如伪类/伪元素)
- **所以官方依然是希望内联合适和普通的css来结合编写**

### 普通的css

- **普通的css我们通常会编写到一个单独的文件，之后再进行引入。**
- **这样的编写方式和普通的网页开发中编写方式是一致的：**
  - 如果我们按照普通的网页标准去编写，那么也不会有太大的问题
  - 但是组件化开发中我们总是希望组件是一个独立的模块，即便是样式也只是在自己内部生效，不会相互影响
  - 但是普通的css都属于全局的css，样式之间会相互影响
- **这种编写方式最大的问题是样式之间会相互层叠掉**

### css modules

- **css modules并不是React特有的解决方案，而是所有使用了类似于webpack配置的环境下都可以使用的**
  - 如果在其他项目中使用它，那么我们需要自己来进行配置，比如配置**webpack.config.js中的modules: true**等
- **React的脚手架已经内置了css modules的配置：**
  - **.css/.less/.scss 等样式文件**都需要**修改成 .module.css/.module.less/.module.scss** 等
  - 之后就可以引用并且进行使用了
- **css modules确实解决了局部作用域的问题，也是很多人喜欢在React中使用的一种方案**
- 但是这种方案也有自己的缺陷：
  - 引用的类名，**不能使用连接符(.home-title)**，在JavaScript中是不识别的
  - 所有的**className都必须使用{style.className} 的形式**来编写
  - 不方便**动态来修改某些样式**，依然**需要使用内联样式的方式**
- **如果你觉得上面的缺陷还算OK，那么你在开发中完全可以选择使用css modules来编写，并且也是在React中很受欢迎的一种方式**

1. 创建一个 CSS Modules 文件，例如 `src/App.module.css`：

```css
/* App.module.css */
.title {
  font-size: 24px;
  color: blue;
}

.paragraph {
  font-size: 16px;
  color: darkgreen;
}
```

注意文件名使用了 `.module.css` 的扩展名，这是使用 Create React App 创建的项目默认支持的 CSS Modules 文件名格式。

2. 修改 `src/App.js`，引入 CSS Modules 并使用：

```JSX
import React from 'react';
import ReactDOM from 'react-dom';
import styles from './App.module.css';

function App() {
  return (
    <div>
      <h1 className={styles.title}>Hello, React 18!</h1>
      <p className={styles.paragraph}>
        This is a paragraph with CSS Modules in a React 18 application.
      </p>
    </div>
  );
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);
```

在这个示例中，我们引入了 `App.module.css` 文件并将其命名为 `styles`。然后我们使用 `styles` 对象的属性（例如 `styles.title` 和 `styles.paragraph`）为组件的元素应用样式。CSS Modules 会将这些类名转换为唯一的类名，以避免样式冲突。

## 认识CSS in JS(终极方案)

> coderwhy认为最好的方案

- **官方文档也有提到过CSS in JS这种方案：**
  - “CSS-in-JS” 是指一种模式，其中 CSS 由 JavaScript 生成而不是在外部文件中定义
  - 注意此功能并不是 React 的一部分，而是由第三方库提供
  - React 对样式如何定义并没有明确态度
- **在传统的前端开发中，我们通常会将结构（HTML）、样式（CSS）、逻辑（JavaScript）进行分离**
  - 但是在前面的学习中，我们就提到过，React的思想中认为**逻辑本身和UI是无法分离**的，所以才会**有了JSX的语法**
  - **样式呢？样式也是属于UI的一部分**
  - 事实上CSS-in-JS的模式就是**一种将样式（CSS）也写入到JavaScript中的方式**，并且**可以方便的使用JavaScript的状态**
  - 所以React有被人称之为 **All in JS**
- **当然，这种开发的方式也受到了很多的批评：**
  - Stop using CSS in JavaScript for web development
  - https://hackernoon.com/stop-using-css-in-javascript-for-web-development-fa32fb873dcc

### 认识styled-components

- **批评声音虽然有，但是在我们看来很多优秀的CSS-in-JS的库依然非常强大、方便：**
  - CSS-in-JS通过**JavaScript来为CSS赋予一些能力**，包括**类似于CSS预处理器一样的样式嵌套、函数定义、逻辑复用、动态修改状态等等**
  - 虽然**CSS预处理器也具备某些能力**，但是**获取动态状态依然是一个不好处理的点**
  - 所以，目前**可以说CSS-in-JS是React编写CSS最为受欢迎的一种解决方案**
- **目前比较流行的CSS-in-JS的库有哪些呢？**
  - styled-components
  - emotion
  - glamorous
- **目前可以说styled-components依然是社区最流行的CSS-in-JS库，所以我们以styled-components的讲解为主**
- 安装styled-components：

```jsx
yarn add styled-components//或者npm，看使用的是哪个包管理工具
```

### ES6标签模板字符串

- **ES6中增加了模板字符串的语法，这个对于很多人来说都会使用**
- **但是模板字符串还有另外一种用法：标签模板字符串（Tagged Template Literals）**
- 一个普通的JavaScript的函数：
  - 正常情况下，我们都是通过 函数名() 方式来进行调用的，其实函数还有另外一种调用方式：
  - **如果我们在调用的时候插入其他的变量：**
    - 模板字符串被拆分了；
    - 第一个元素是数组，是被模块字符串拆分的字符串组合；
    - 后面的元素是一个个模块字符串传入的内容；

```jsx
function foo(...args){
  console.log(args)
}

foo("Hello World");

foo`Hello World`;

const name = "XiaoYu";
foo`Hello ${name}`;
```

- **在styled component中，就是通过这种方式来解析模块字符串，最终生成我们想要的样式的**

### styled的基本使用

- **styled-components的本质是通过函数的调用，最终创建出一个组件：**
  - 这个组件会被自动添加上一个不重复的class
  - styled-components会给该class添加相关的样式
- **另外，它支持类似于CSS预处理器一样的样式嵌套：**
  - 支持**直接子代选择器或后代选择器，并且直接编写样式**
  - 可以**通过&符号获取当前元素**
  - 直接**伪类选择器、伪元素**等

![image-20230410172603991](.\React-images\image-20230410172603991-1681234900431-13-1683026306063-56.png)

> 为了让CSS的代码拥有高亮，Vscode需要从扩展商店中安装一个插件
>
> - vscode-styled-components

![image-20230410172919167](.\React-images\image-20230410172919167-1681234900431-14-1683026306063-58.png)

### props、attrs属性

> 通过props将变量传递给CSS使用

- **props可以传递**

```jsx
{/*把内容传递进XYInput的CSS中后，CSS就能够通过props接收到*/}
<XYInput type="password" left="20px" />
```

- **props可以被传递给styled组件**

  - 获取props需要通过${}传入一个插值函数，props会作为该函数的参数
  - 这种方式可以有效的解决动态样式的问题

  > props在样式中的写入方式

![image-20230411160310742](.\React-images\image-20230411160310742-1681234900431-21-1683026306063-55.png)

- **添加attrs属性**

> 就是当没有传递内容进来的时候，我们可以自己设置默认的内容。但这个其实是通过attrs给标签模板字符串中提供的属性的意思。所以我们填入attrs的是一个 `||` 判断条件，只有当我们前者没有的时候才会执行后者

```jsx
const XYInput = styled.input。attrs({
  placeholder:"请填写密码",
  paddingLeft: props => props.left || "5px"
})`
	border-color : red;
	padding-left:${props => props.paddingLft};
	
	&:focus {
		outline-color:orange;
	}
`
```

### styled高级特性

- 动态属性：

  你可以根据组件的属性（props）动态地更改样式。例如，基于`primary`属性为按钮设置不同的颜色：

```jsx
const Button = styled.button`
  background-color: ${(props) => (props.primary ? 'blue' : 'white')};
  color: ${(props) => (props.primary ? 'white' : 'blue')};
`;
```

- 嵌套样式：

  `styled-components`允许你使用嵌套选择器来设置子元素的样式，类似于Sass或Less预处理器。

```jsx
const Container = styled.div`
  h2 {
    color: blue;
  }
`;
```

- 伪类和伪元素：

  使用`styled-components`，你可以直接在组件样式中添加伪类和伪元素。

```jsx
const Button = styled.button`
  &:hover {
    background-color: blue;
    color: white;
  }
  
  &::before {
    content: '◆';
    display: inline-block;
    margin-right: 8px;
  }
`;
```

- 主题：

  使用`ThemeProvider`组件，你可以为应用程序设置一个主题，从而轻松地在多个组件中共享样式变量。

```jsx
import { ThemeProvider } from 'styled-components';

const theme = {
  primaryColor: 'blue',
  secondaryColor: 'green',
};

const Button = styled.button`
  background-color: ${(props) => props.theme.primaryColor};
`;

function App() {
  return (
    <ThemeProvider theme={theme}>
      <Button>My Button</Button>
    </ThemeProvider>
  );
}
```

- 继承样式：

  你可以通过`styled()`函数继承现有组件的样式，然后向其添加或覆盖样式

```jsx
const BaseButton = styled.button`
  font-size: 16px;
`;

const PrimaryButton = styled(BaseButton)`
  background-color: blue;
  color: white;
`;
```

- 与其他组件库/框架集成：

  `styled-components`可以与其他组件库或框架（如Material-UI或Ant Design）一起使用，以轻松地自定义它们的样式。

```jsx
import { Button } from 'antd';
import styled from 'styled-components';

const CustomButton = styled(Button)`
  background-color: blue;
  color: white;
`;
```

## vue中添加class

- **vue中添加class是一件非常简单的事情：**
- **你可以通过传入一个对象：**

```vue
<template>
  <div>
    <div :class="{ active: isActive, disabled: isDisabled }">Object syntax example</div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const isActive = ref(true);
const isDisabled = ref(false);
</script>

<style>
.active {
  color: green;
}

.disabled {
  opacity: 0.5;
}
</style>
```

- **你也可以传入一个数组：**

```vue
<template>
  <div>
    <div :class="[classA, classB]">Array syntax example</div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const classA = ref('red-text');
const classB = ref('bold-text');
</script>

<style>
.red-text {
  color: red;
}

.bold-text {
  font-weight: bold;
}
</style>
```

- **甚至是对象和数组混合使用：**

```vue
<template>
  <div>
    <div :class="[classA, { active: isActive }]">Mixed object and array syntax example</div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const classA = ref('blue-text');
const isActive = ref(true);
</script>

<style>
.blue-text {
  color: blue;
}

.active {
  text-decoration: underline;
}
</style>
```

## React中添加class

- **React在JSX给了我们开发者足够多的灵活性，你可以像编写JavaScript代码一样，通过一些逻辑来决定是否添加某些class：**

![image-20230411175506523](.\React-images\image-20230411175506523-1681234900431-15-1683026306063-60.png)

- **这个时候我们可以借助于一个第三方的库：`classnames`**

  - 很明显，这是一个用于动态添加classnames的一个库

  ```jsx
  npm install classnames
  ```

![image-20230411175537796](.\React-images\image-20230411175537796-1681234900431-17-1683026306063-62.png)

#