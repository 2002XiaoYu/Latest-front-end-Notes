# setState原理-性能优化-ref-受控组件

## 为什么使用setState

- **开发中我们并不能直接通过修改state的值来让界面发生更新：**
  -  因为我们修改了state之后，希望React根据最新的State来重新渲染界面，但是这种方式的修改React并不知道数据发生了变化
  -  React并没有实现类似于Vue2中的Object.defineProperty或者Vue3中的Proxy的方式来监听数据的变化
  -  我们**必须通过setState来告知React数据已经发生了变化**
- **疑惑：在组件中并没有实现setState的方法，为什么可以调用呢？**
  - 原因很简单，setState方法是从React.Component中继承过来的

![image-20230407123746213](.\React-images\image-20230407123746213-1683026306062-17.png)

> 我们使用this.setState({})的时候，里面的{}就从内存来说就相当于创建了一个新对象了。而这个里面本来是有一个原有对象的，这个原有对象里面是我们完整的数据，我们创建了新对象大多数情况下其实是不会改动全部数据的，而是改动一部分，那为什么新的数据没有把旧的数据全部覆盖掉，而是只覆盖了改动的部分。就像下面的图片中，新创建的message内容改动，并不会导致原有对象的counter:0的消失
>
> #### 做法
>
> React中的源码中其实是使用了**Object.assign(this.state,newState)**进行了一个合并
>
> - [Object.assign() - JavaScript | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)

![image-20230407135013434](.\React-images\image-20230407135013434-1683026306062-22.png)

### setState更多用法

> setState可以传入一个回调函数
>
> - 好处1：可以在回调函数中编写新的state的逻辑
> - 好处2：当前的回调函数会将之前的state(数据)和props(英文"properties"的缩写，组件间传递的数据)传递进来
> - 好处3：setState在React的时间处理中是一个异步调用，不会堵塞我们后面代码的执行
> - 增强了代码的内聚性
>
> ```jsx
> this.setState((state,props)=>{
>   //编写一些对新state处理逻辑
>   //获取之前的state和props
>   return {
>     message:"你好啊，小余"
>   }
> })
> ```

### setState异步更新

- **setState的更新是异步的？**

  - 最终打印结果是Hello World
  - 可见setState是异步的操作，我们并不能在执行完setState之后立马拿到最新的state的结果

- **为什么setState设计为异步呢？**

  - setState设计为异步其实之前在GitHub上也有很多的讨论
  - React核心成员（Redux的作者）Dan Abramov也有对应的回复
  - https://github.com/facebook/react/issues/11527#issuecomment-360199710

  > 所以这里有一些想法。这决不是一个完整的答复，但也许这仍然比什么都不说更有帮助。
  >
  > 首先，我认为我们同意为了批量更新而延迟调和是有益的。也就是说，我们同意setState()同步重新渲染在很多情况下是低效的，如果我们知道我们可能会得到几个更新，那么批量更新会更好。
  >
  > 例如，如果我们在一个浏览器的点击处理程序中，Child和Parent都调用了setState，我们不想重新渲染Child两次，而是倾向于把它们标记为dirty，在退出浏览器事件之前一起重新渲染。
  >
  > 你在问：为什么我们不能做同样的事情（批处理），但要立即将setState的更新写入this.state，而不等待调和的结束。我不认为有一个明显的答案（两种解决方案都有取舍），但以下是我能想到的几个原因。
  >
  > 保证内部一致性
  > 即使状态是同步更新的，道具也不是。(在你重新渲染父组件之前，你无法知道道具，如果你同步进行，批处理就会失效）。
  >
  > 现在React提供的对象（state、props、refs）在内部是相互一致的。这意味着，如果你只使用这些对象，它们就能保证指向一个完全调和的树（即使它是该树的一个旧版本）。为什么这很重要？
  >
  > 当你只使用状态时，如果它是同步刷新的（就像你提议的那样），这种模式就可以工作：
  >
  > ```jsx
  > console.log(this.state.value) // 0
  > this.setState({ value: this.state.value + 1 });
  > console.log(this.state.value) // 1
  > this.setState({ value: this.state.value + 1 });
  > console.log(this.state.value) // 2
  > ```
  >
  > 然而，假设这个状态需要被解除，以便在几个组件之间共享，所以你把它移到了一个父类：
  >
  > ```jsx
  > -this.setState({ value: this.state.value + 1 });
  > +this.props.onIncrement(); // Does the same thing in a parent
  > ```
  >
  > 我想强调的是，在典型的依赖setState()的React应用中，这是最常见的一种React特定的重构，你每天都会做。
  >
  > 然而，这破坏了我们的代码
  >
  > ```jsx
  > console.log(this.props.value) // 0
  > this.props.onIncrement();
  > console.log(this.props.value) // 0
  > this.props.onIncrement();
  > console.log(this.props.value) // 0
  > ```
  >
  > 这是因为，在你提出的模型中，this.state会被立即刷新，但this.props不会。而且我们不能在不重新渲染父节点的情况下立即刷新this.props，这意味着我们必须放弃批处理（根据情况，这可能会使性能大大降低）。
  >
  > 还有一些更微妙的情况，例如，如果你将来自props（尚未刷新）和state（建议立即刷新）的数据混合在一起，以创建一个新的状态： #122（评论）。参考文献也有同样的问题： #122（评论）。
  >
  > 这些例子完全不是理论上的。事实上React Redux绑定曾经正是有这样的问题，因为它们将React道具与非React状态混合在一起：reduxjs/react-redux#86，reduxjs/react-redux#99，reduxjs/react-redux#292，reduxjs/redux#1415，reduxjs/react-redux#525。
  >
  > 我不知道为什么MobX用户没有碰到这种情况，但我的直觉是，他们可能会碰到这种情况，但认为是自己的错。或者他们没有从道具中读取那么多，而是直接从MobX可变对象中读取。
  >
  > 那么今天React是如何解决这个问题的呢？在React中，this.state和this.props都是在调和和冲刷之后才更新的，所以你会看到在重构前后都会打印0。这使得提升状态变得安全。
  >
  > 是的，在某些情况下，这可能是不方便的。特别是对于那些来自更多的OO背景的人来说，他们只想多次突变状态，而不是考虑如何在一个地方表示一个完整的状态更新。我可以理解这一点，尽管我确实认为从调试的角度来看，保持状态更新的集中性更清晰： #122（评论）。
  >
  > 尽管如此，你仍然可以选择将你想立即读取的状态转移到一些侧面的可变对象中，特别是如果你不使用它作为渲染的真实来源。这几乎是MobX让你做的事情🙂。
  >
  > 如果你知道自己在做什么，你还可以选择刷新整个树。这个API被称为ReactDOM.flushSync(fn)。我想我们还没有把它记录下来，但我们肯定会在16.x发布周期的某个时候这样做。请注意，它实际上是强制对发生在调用内部的更新进行完全的重新渲染，所以你应该非常谨慎地使用它。这样它就不会破坏道具、状态和参考文献之间的内部一致性的保证。
  >
  > 总而言之，React模型并不总是能带来最简洁的代码，但它是内部一致的，并确保提升状态是安全的。
  >
  > 
  >
  > ### 启用并发更新
  >
  > 从概念上讲，React的行为就像每个组件有一个单一的更新队列。这就是为什么讨论有意义的原因：我们讨论是否立即对this.state应用更新，因为我们毫不怀疑更新会以这个确切的顺序应用。然而，情况不一定是这样（哈哈）。
  >
  > 最近，我们一直在讨论 "异步渲染 "的问题。我承认我们没有很好地传达这意味着什么，但这就是研发的本质：你去追求一个概念上看起来很有希望的想法，但你只有在花了足够的时间之后才真正理解它的含义。
  >
  > 我们解释 "异步渲染 "的一种方式是，React可以根据setState()调用的来源为其分配不同的优先级：事件处理器、网络响应、动画等等。
  >
  > 例如，如果你正在输入一条消息，TextBox组件中的setState()调用需要立即被刷新。然而，如果你在打字时收到一条新的消息，延迟渲染新的MessageBubble到某个阈值（例如一秒钟）可能比让打字因阻塞线程而停滞不前更好。
  >
  > 如果我们让某些更新具有 "较低的优先级"，我们可以把它们的渲染分成几毫秒的小块，这样用户就不会注意到它们了。
  >
  > 我知道像这样的性能优化听起来可能不是很令人兴奋或令人信服。你可以说： "我们的MobX不需要这个，我们的更新跟踪足够快，只要避免重新渲染就可以了"。我不认为这在所有情况下都是真的（例如，无论MobX有多快，你仍然必须为新安装的视图创建DOM节点并进行渲染）。尽管如此，如果这是真的，而且如果你有意识地决定你可以接受总是将对象包装成一个特定的JavaScript库来跟踪读写，也许你不会从这些优化中受益太多。
  >
  > 但是，异步渲染不仅仅是关于性能优化。我们认为这是对React组件模型所能做到的根本性转变。
  >
  > 例如，考虑你从一个屏幕导航到另一个屏幕的情况。通常情况下，你会在新屏幕渲染时显示一个旋转器。
  >
  > 然而，如果导航的速度足够快（在一秒左右），闪现并立即隐藏一个旋转器会导致用户体验的下降。更糟的是，如果你有多层次的组件，有不同的异步依赖（数据、代码、图像），你最终会出现一连串的旋转器，一个一个地短暂闪现。这在视觉上令人不快，而且由于所有的DOM回流，在实践中会使你的应用程序变慢。这也是许多模板代码的来源。
  >
  > 如果当你做一个简单的setState()来渲染一个不同的视图时，我们可以 "在后台 "开始渲染更新的视图，这不是很好吗？想象一下，不用自己写任何协调代码，如果更新时间超过某个阈值（例如一秒钟），你可以选择显示一个旋转器，否则让React在整个新子树的异步依赖得到满足时执行无缝过渡。此外，在我们 "等待 "的时候，"旧屏幕 "保持互动（例如，你可以选择不同的项目来过渡），React强制规定，如果时间过长，你必须显示一个旋转器。
  >
  > 事实证明，通过目前的React模型和对生命周期的一些调整，我们实际上可以实现这一点 过去几周，@acdlite一直在研究这个功能，并将很快发布一个RFC。
  >
  > 请注意，这只是因为this.state没有被立即刷新而实现的。如果它被立即刷新，我们就没有办法在后台开始渲染 "新版本 "的视图，而 "旧版本 "仍然是可见和互动的。他们独立的状态更新会发生冲突。
  >
  > 我不想抢了@acdlite的风头来宣布这一切，但我希望这听起来至少有一点令人兴奋。我知道这听起来可能仍然像蒸发品，或者像我们并不真正知道我们在做什么。我希望我们能在未来几个月内说服你，并希望你能欣赏React模型的灵活性。据我所知，至少在某种程度上，这种灵活性是由于不立即刷新状态更新而实现的。

- 总结：

  - setState设计为异步，可以显著的提升性能；
  - 如果每次调用 setState都进行一次更新，那么意味着render函数会被频繁调用，界面重新渲染，这样效率是很低的
  - 最好的办法应该是获取到多个更新，之后进行批量更新。这里是一个queue队列结构(先进先出)，然后一个个处理，处理完了一次性合并进行render更新。

  ![image-20230407192103978](.\React-images\image-20230407192103978-1683026306062-18.png)

  > 这里的counter结果为1就证明了这是异步，因为如果是同步的话，在第一次调用的时候为1，第二次调用的时候就该在已经形成结果的1上继续加1为2，然后第三次调用就为3了。
  >
  > - 但是最终结果为1就证明了这就是处理完了一次性合并更新的

  - **如果同步更新了state，但是还没有执行render函数，那么state和props不能保持同步**
    - state和props不能保持一致性，会在开发中产生很多的问题

### 如何获取异步的结果

- **那么如何可以获取到更新后的值呢？**
- 方式一：setState的回调
  - setState接受两个参数：第二个参数是一个回调函数，这个回调函数会在更新后会执行
  - 格式如下：setState(partialState, callback) 

```jsx
changeText(){
  this.setState = {
    message:"你好 小余"
  },()=>{//等执行完后面的代码后又会回头执行这个回调函数的内容，然后呈现出来
    console.log(this.state.message);//你好 小余
  }
}
```

- 方式二：生命周期函数

```jsx
componentDidUpdate(prevProps,provState,snapshot){
  console.log(this.state.message)
}
```

### setState一定是异步吗？（React18之前）

- 验证一：在setTimeout中的更新：(React18前是同步，18之后就是异步的，验证二也一样)

![image-20230407183218369](.\React-images\image-20230407183218369-1683026306062-23.png)

- 验证二：原生DOM事件：

![image-20230407183242105](.\React-images\image-20230407183242105-1683026306062-20.png)

- 其实分成两种情况：
  - 在组件生命周期或React合成事件中，setState是异步
  - 在setTimeout或者原生dom事件中，setState是同步

### setState默认是异步的（React18之后）

- **在React18之后，默认所有的操作都被放到了批处理中（异步处理）**

![image-20230407183355764](.\React-images\image-20230407183355764-1683026306062-21.png)

- **如果希望代码可以同步会拿到，则需要执行特殊的flushSync操作：**
  - flushSync是刷新同步的意思，这个是需要额外从**react-dom**中进行引入才能使用的。写在flushSync里面的会当作一次合并立刻执行

![image-20230407183604861](.\React-images\image-20230407183604861-1683026306062-25.png)

## React更新机制

- **我们在前面已经学习React的渲染流程：**

![image-20230407204424297](.\React-images\image-20230407204424297-1683026306062-26.png)

- **那么React的更新流程呢？**

![image-20230407204438801](.\React-images\image-20230407204438801-1683026306062-24.png)

### React的更新流程

- **React在props或state发生改变时，会调用React的render方法，会创建一颗不同的树。**
- **React需要基于这两颗不同的树之间的差别来判断如何有效的更新UI：**
  - 如果一棵树参考另外一棵树进行完全比较更新，那么即使是最先进的算法，该算法的复杂程度为 O(n³)，其中 n 是树中元素的数量
  - https://grfia.dlsi.ua.es/ml/algorithms/references/editsurvey_bille.pdf
  - 如果在 React 中使用了该算法，那么展示 1000 个元素所需要执行的计算量将在十亿的量级范围
  - 这个开销太过昂贵了，React的更新性能会变得非常低效
- **于是，React对这个算法进行了优化，将其优化成了O(n)，如何优化的呢？**
  - 同层节点之间相互比较，不会跨节点比较
  - 不同类型的节点，产生不同的树结构
  - 开发中，可以通过key来指定哪些节点在不同的渲染下保持稳定(就是相同内容只是换了位置，那尽量不更新)

### keys的优化

- 我们在前面遍历列表时，总是会提示一个警告，让我们加入一个key属性：

![image-20230407225257108](.\React-images\image-20230407225257108-1683026306062-30.png)

- 方式一：在最后位置插入数据
  - 这种情况，有无key意义并不大
- 方式二：在前面插入数据
  - 这种做法，在没有key的情况下，所有的li都需要进行修改
- 当子元素(这里的li)拥有 key 时，React 使用 key 来匹配原有树上的子元素以及最新树上的子元素：
  - 在下面这种场景下，key为111和222的元素仅仅进行位移，不需要进行任何的修改
  - 将key为333的元素插入到最前面的位置即可
- key的注意事项：
  - key应该是唯一的
  - key不要使用随机数（随机数在下一次render时，会重新生成一个数字）
  - 使用index作为key，对性能是没有优化的(所以通常后端返回的数据都是有id的)

## render函数的调用

- **我们使用之前的一个嵌套案例：**

  - 在App中，我们增加了一个计数器的代码；
  - 当点击+1时，会重新调用App的render函数
  - 而当App的render函数被调用时，所有的子组件的render函数都会被重新调用

- 那么，我们可以思考一下，在以后的开发中，我们只要是修改了App中的数据，**所有的组件都需要重新render(重新加载组件树)，进行diff算法，性能必然是很低的：**

  - 事实上，**很多的组件没有必须要重新render**(渲染)
  - 它们调用render应该有一个前提，就是依赖的数据（state、props）发生改变时，再调用自己的render方法

- **如何来控制render方法是否被调用呢？**

  - 通过**shouldComponentUpdate**方法即可

    ```jsx
    {/*在子组件中写上如下代码就能够控制render方法不被调用*/}
    shouldComponentUpdate(nextProps,newState){ {/*中文意思：应该更新组件*/}
       {/*条件自己根据事实情况决定，优化性能*/}                                       
      if(this.state.message !== newState.message){
        return true
      }
        return false                                      
    {/*但是这样就太绝对了，可以加上判断条件，只有当此组件的数据发生变化的时候才进行更新，所以我们就需要获取到之前的数据和现在的数据，然后进行对比*/}
    }
    ```

    

![image-20230407225742935](.\React-images\image-20230407225742935-1683026306062-27.png)

### shouldComponentUpdate

- **React给我们提供了一个生命周期方法 shouldComponentUpdate（很多时候，我们简称为SCU），这个方法接受参数，并且需要有返回值：**
- **该方法有两个参数：**
  - 参数一：**nextProps** 修改之后，最新的props属性
  - 参数二：**nextState** 修改之后，最新的state属性
- **该方法返回值是一个boolean类型：**
  - 返回值为true，那么就需要调用render方法
  - 返回值为false，那么就不需要调用render方法
  - 默认返回的是true，也就是只要state发生改变，就会调用render方法
- **比如我们在App中增加一个message属性：**
  - jsx中并没有依赖这个message，那么它的改变不应该引起重新渲染
  - 但是因为render监听到state的改变，就会重新render，所以最后render方法还是被重新调用了

### PureComponent

> Pure = 纯，PureComponent是 纯组件 的意思

- **如果所有的类，我们都需要手动来实现 shouldComponentUpdate，那么会给我们开发者增加非常多的工作量。**
  - 我们来设想一下shouldComponentUpdate中的各种判断的目的是什么？
  - props或者state中的数据是否发生了改变，来决定shouldComponentUpdate返回true或者false
- **事实上React已经考虑到了这一点，所以React已经默认帮我们实现好了，如何实现呢？**
  - 将class继承自PureComponent，而不是Component了
  - PureComponent是浅层次比较，只会比较第一层

![image-20230407235712313](.\React-images\image-20230407235712313-1683026306062-28.png)

### shallowEqual方法

- **这个方法中，调用 !shallowEqual(oldProps, newProps) || !shallowEqual(oldState, newState)，这个shallowEqual就是进行浅层比较：**

![image-20230408000532612](.\React-images\image-20230408000532612-1683026306062-29.png)

### 高阶组件memo

> **memo**直接套用在函数的外面

![image-20230408000640492](.\React-images\image-20230408000640492-1683026306062-31.png)

- **目前我们是针对类组件可以使用PureComponent，那么函数式组件呢？**
  - 事实上函数式组件我们在props没有改变时，也是不希望其重新渲染其DOM树结构的
- **我们需要使用一个高阶组件memo：**
  - 我们将之前的Header、Banner、ProductList都通过memo函数进行一层包裹
  - Footer没有使用memo函数进行包裹
  - 最终的效果是，当counter发生改变时，Header、Banner、ProductList的函数不会重新执行
  - 而Footer的函数会被重新执行

### 不可变数据的力量

![image-20230408013728519](.\React-images\image-20230408013728519-1683026306062-34.png)

> "不可变的力量"指的是在 React 中使用不可变数据结构的优势。在 React 应用程序中，组件的状态（state）和属性（props）经常被修改。但是，直接修改这些数据结构可能会导致应用程序出现意外行为，因为它可能会破坏 React 对组件状态的跟踪。
>
> 相反，使用不可变的数据结构可以确保数据在修改时被复制，而不是在原始数据结构上进行修改。这意味着任何对数据的修改都会生成一个全新的数据结构，而原始数据结构则保持不变。这种方式保证了数据的纯度和不可变性，从而使 React 更容易跟踪应用程序状态的变化，并避免了潜在的副作用。
>
> 使用不可变数据结构还可以提高 React 应用程序的性能。在 React 中，每当组件的状态或属性发生变化时，React 都会对整个组件进行重新渲染。由于使用不可变数据结构可以让 React 比较新旧数据结构的区别，所以 React 可以轻松确定哪些组件需要进行更新，并且可以避免不必要的重新渲染，从而提高应用程序的性能。
>
> React 推荐使用一些不可变数据结构库（如 Immutable.js、seamless-immutable 等）来创建和处理不可变数据结构。

![image-20230408014953434](.\React-images\image-20230408014953434-1683026306062-33.png)

### React源码中PureComponent是如何实现的

> `PureComponent` 是 `React` 中的一个组件类，它类似于 `Component`，但它在组件更新时会自动执行一次浅比较来判断组件是否需要重新渲染。如果组件的 `props` 和 `state` 没有发生变化，则 `PureComponent` 将阻止组件的重新渲染，从而提高了应用程序的性能。
>
> `PureComponent` 实现的关键在于其 `shouldComponentUpdate()` 方法。在默认情况下，`shouldComponentUpdate()` 方法会比较当前组件的 `props` 和 `state` 与前一次更新时的 `props` 和 `state` 是否发生了变化。如果它们完全相等，则组件将不会重新渲染。
>
> 在 `PureComponent` 中，`shouldComponentUpdate()` 方法被重写为浅比较两个 `props` 和 `state` 对象的值是否相等。具体地说，`PureComponent` 的 `shouldComponentUpdate()` 方法会遍历当前和前一个 `props` 和 `state` 对象的属性，并检查它们是否具有相同的引用，如果不是则认为它们已经发生了变化。如果所有的属性都没有发生变化，则 `shouldComponentUpdate()` 返回 `false`，从而阻止组件重新渲染。

- 以下是 `PureComponent` 类的简化实现：

```jsx
class PureComponent extends Component {
  shouldComponentUpdate(nextProps, nextState) {
    // 比较当前和下一个 props 和 state 是否相等
    return !shallowEqual(this.props, nextProps) || !shallowEqual(this.state, nextState);
  }
}

function shallowEqual(objA, objB) {
  // 判断两个对象的引用是否相同
  if (objA === objB) {
    return true;
  }

  // 判断两个对象是否都是对象类型，且不为 null
  if (typeof objA !== 'object' || objA === null || typeof objB !== 'object' || objB === null) {
    return false;
  }

  // 获取两个对象的属性名数组
  const keysA = Object.keys(objA);
  const keysB = Object.keys(objB);

  // 如果两个对象的属性数量不相等，则返回 false
  if (keysA.length !== keysB.length) {
    return false;
  }

  // 遍历每个属性，比较它们的值是否相等
  for (let i = 0; i < keysA.length; i++) {
    const key = keysA[i];
    if (objA[key] !== objB[key]) {
      return false;
    }
  }

  // 如果所有属性的值都相等，则返回 true
  return true;
}
```

- 在实际的实现中，`PureComponent` 还会继承 `Component` 类的其他方法，并将它们重写为相应的浅比较实现。这样，`PureComponent` 就可以在与 `Component` 相同的条件下实现自动的 `shouldComponentUpdate()` 方法。

---

>  下方中一定要使用第二种方法的主要原因是要重新触发执行render (render如果不触发的话，界面就不会刷新，那修改数据不进行展示就没有意义)
>
>  - 因为其实我们使用books = xxxx，他这是一个浅拷贝，深层次的内容指向其实还是同一个地方

![image-20230408021202478](.\React-images\image-20230408021202478-1683026306062-32.png)

## 如何使用ref

- **在React的开发模式中，通常情况下不需要、也不建议直接操作DOM原生，但是某些特殊的情况，确实需要获取到DOM进行某些操作：**

  - **管理焦点，文本选择或媒体播放**
  - **触发强制动画**
  - **集成第三方 DOM 库**
  - 我们可以通过refs获取DOM

- **如何创建refs来获取对应的DOM呢？目前有三种方式：**

  - **方式一：传入字符串**
    - 使用时通过 this.refs.传入的字符串格式获取对应的元素

  ![image-20230408022048586](.\React-images\image-20230408022048586-1683026306062-37.png)

  - **方式二：传入一个对象**
    - 对象是通过 React.createRef() 方式创建出来的
    - 使用时获取到创建的对象其中有一个current属性就是对应的元素

  ![image-20230408023119379](.\React-images\image-20230408023119379-1683026306062-35.png)

  - **方式三：传入一个函数**
    - 该函数会在DOM被挂载时进行回调，这个**函数会传入一个 元素对象**，我们可以自己保存
    - 使用时，直接拿到之前保存的元素对象即可

![image-20230408023453969](.\React-images\image-20230408023453969-1683026306062-40.png)

### ref的类型

- **ref 的值根据节点的类型而有所不同：**
  - 当 ref 属性**用于 HTML 元素**时，构造函数中使用 React.createRef() 创建的 ref 接收**底层 DOM 元素作为其 current 属性**
  - 当 ref 属性**用于自定义 class 组件**时，ref 对象接收**组件的挂载实例作为其 current 属性**
  - **你不能在函数组件上使用 ref 属性**，因为他们没有实例
- **函数式组件是没有实例的，所以无法通过ref获取他们的实例：**
  - 但是某些时候，我们可能想要获取函数式组件中的某个DOM元素；
  - 这个时候我们可以通过 React.forwardRef ，后面我们也会学习 hooks 中如何使用ref

![image-20230408024438118](.\React-images\image-20230408024438118-1683026306062-36.png)

## 认识受控组件

- **在React中，HTML表单的处理方式和普通的DOM元素不太一样：表单元素通常会保存在一些内部的state**
- **比如下面的HTML表单元素：**
  - 这个处理方式是DOM默认处理HTML表单的行为，在**用户点击提交时会提交到某个服务器**中，并且**刷新页面**、
  - 在React中，**并没有禁止这个行为，它依然是有效的**
  - 但是**通常情况下会使用JavaScript函数来方便的处理表单提交**，同时**还可以访问用户填写的表单数据**
  - 实现这种效果的标准方式是**使用“受控组件”**

![image-20230408024832883](.\React-images\image-20230408024832883-1683026306062-39.png)

- 在 React 18 中，受控组件的实现方式与之前的版本没有改变，仍然是通过将表单元素的值（value）和 `onChange` 事件处理函数绑定到组件的状态上来实现的。

- 受控组件是指在表单元素（例如 `input`、`textarea` 或 `select`）中，其值是受组件状态控制的，而不是由 DOM 自己维护。当用户与表单元素交互时，组件的状态会更新，从而反映出表单元素的当前值。这样，我们就可以从组件状态中获取表单数据，而无需访问 DOM，从而实现更加可靠和可维护的代码。

- 以下是使用受控组件的示例：

```jsx
class MyForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = { name: '', email: '', message: '' };
  }

  handleInputChange = (event) => {
    const target = event.target;
    const name = target.name;
    const value = target.value;

    this.setState({
      [name]: value,
    });
  };

  handleSubmit = (event) => {
    event.preventDefault();
    console.log(this.state);
  };

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" name="name" value={this.state.name} onChange={this.handleInputChange} />
        </label>
        <label>
          Email:
          <input type="email" name="email" value={this.state.email} onChange={this.handleInputChange} />
        </label>
        <label>
          Message:
          <textarea name="message" value={this.state.message} onChange={this.handleInputChange} />
        </label>
        <button type="submit">Submit</button>
      </form>
    );
  }
}
```

- 在上面的示例中，我们创建了一个包含三个表单元素（`input`、`textarea` 和 `select`）的表单。每个表单元素都绑定到组件状态的一个属性上，并通过 `onChange` 事件处理函数将表单元素的值更新到组件状态中。当用户提交表单时，我们可以从组件状态中获取表单数据，并将其输出到控制台。

- 需要注意的是，受控组件需要额外的代码来处理每个表单元素的值和事件处理函数，因此相比于非受控组件，受控组件需要更多的代码。然而，受控组件可以帮助您更好地控制表单数据的流动，并提供更好的可维护性和可靠性。

### 作业题目

> 什么是SCU优化？类组件和函数组件分别如何进行SCU优化的？

SCU（shouldComponentUpdate）是 React 中一个生命周期方法，用于判断组件是否需要重新渲染。在默认情况下，每次组件的 `props` 或 `state` 发生变化时，React 都会重新渲染组件，即使这些变化对组件的显示没有任何影响。这可能会导致应用程序性能下降。

SCU 优化是一种优化技术，可以避免 React 在不必要的情况下重新渲染组件，从而提高应用程序的性能。具体来说，当 React 检测到组件的 `props` 或 `state` 发生变化时，它会调用 `shouldComponentUpdate` 方法，并根据该方法的返回值来决定是否重新渲染组件。如果 `shouldComponentUpdate` 返回 `false`，则 React 不会重新渲染组件，从而提高应用程序的性能。

在实现 SCU 优化时，通常需要实现一个自定义的 `shouldComponentUpdate` 方法，该方法比较当前和下一个 `props` 和 `state` 的值，以确定它们是否发生了变化。这个比较可以是浅比较，也可以是深比较，具体取决于组件的要求和应用程序的性能需求。

**类组件和函数组件的 SCU（shouldComponentUpdate）优化实现方式略有不同。**

对于类组件，可以重写 `shouldComponentUpdate` 方法来实现 SCU 优化。在 `shouldComponentUpdate` 方法中，通常会比较当前和下一个 `props` 和 `state` 的值，以确定它们是否发生了变化。如果 `shouldComponentUpdate` 返回 `false`，则 React 不会重新渲染组件，从而提高应用程序的性能。以下是一个示例：

```jsx
class MyComponent extends React.Component {
  shouldComponentUpdate(nextProps, nextState) {
    // 仅在 count 发生变化时重新渲染组件
    return this.state.count !== nextState.count;
  }

  render() {
    return <div>{this.state.count}</div>;
  }
}
```

对于函数组件，可以使用 React 提供的 `React.memo` 高阶组件来实现 SCU 优化。`React.memo` 接受一个函数组件作为参数，并返回一个新的组件，该组件在 `props` 发生变化时进行浅比较，并决定是否重新渲染原始组件。如果 `props` 没有发生变化，则返回缓存的组件，从而提高应用程序的性能。以下是一个示例：

```jsx
const MyComponent = React.memo((props) => {
  return <div>{props.count}</div>;
});
```

在上面的示例中，我们使用 `React.memo` 高阶组件将函数组件 `MyComponent` 包装起来，并返回一个新的组件。新的组件在 `props` 发生变化时进行浅比较，并决定是否重新渲染原始组件。这样，我们可以避免在不必要的情况下重新渲染函数组件，从而提高应用程序的性能。

需要注意的是，在实现 SCU 优化时，需要权衡应用程序的性能和正确性。如果过于频繁地重新渲染组件会导致性能下降，而如果过于节约地重新渲染组件可能会导致应用程序的错误或不一致。因此，在实现 SCU 优化时，需要进行适当的测试和优化，以确保应用程序的性能和正确性。

> **React的setState是同步的还是异步的？React18中是怎么样的？**

在 React 中，`setState` 方法在默认情况下是异步的。这意味着，当我们调用 `setState` 方法时，React 会将更新的状态合并到组件的状态队列中，并通知 React 重新渲染组件。但是，React 并不会立即更新组件的状态，而是将更新的状态推迟到后续的批处理操作中，以提高应用程序的性能。

在某些情况下，我们可能需要在 `setState` 方法完成后立即执行一些操作，例如获取最新的组件状态或执行某些副作用。为了实现这个目的，React 提供了一个可选的回调函数参数，我们可以将这个回调函数作为 `setState` 方法的第二个参数来使用。在回调函数中，可以访问最新的组件状态，这样就可以避免在异步更新期间访问过时的组件状态。

```jsx
this.setState({ count: this.state.count + 1 }, () => {
  console.log(`Count updated to ${this.state.count}`);
});
```

需要注意的是，`setState` 方法是异步的，这意味着在某些情况下，可能会看到更新的状态并没有立即反映在组件上。例如，在处理用户输入时，用户可能会连续快速地输入多个字符，从而导致状态更新的队列中积累了多个更新操作。在这种情况下，React 可能会将这些更新操作合并成一个操作，以减少重复渲染组件的次数。如果您需要立即访问最新的组件状态，可以使用回调函数或 `useEffect` Hook 来获取它。

在 React 18 中，`setState` 方法的行为没有发生变化。它仍然是异步的，并且仍然可以通过回调函数参数来访问最新的组件状态。另外，在 React 18 中，React 引入了一些新的性能优化技术，例如增量渲染和调度优先级，可以进一步提高应用程序的性能。但是，`setState` 方法的行为并没有发生变化。