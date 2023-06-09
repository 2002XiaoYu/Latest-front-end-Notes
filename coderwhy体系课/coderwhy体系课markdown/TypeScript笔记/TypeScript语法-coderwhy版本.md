# TypeScript语法-coderwhy版本

> 笔记作者：小余

## 邂逅TypeScript语法

### JavaScript一门优秀的语言

- **任何新技术的出现都是为了解决原有技术的某个痛点**
- **JavaScript是一门优秀的编程语言吗？**
  -  每个人可能观点并不完全一致，但是从很多角度来看，**JavaScript是一门非常优秀的编程语言**
  - 而且，可以说在很长一段时间内**这个语言不会被代替**，并且会在**更多的领域被大家广泛使用**
- **著名的Atwood定律：**
  - Stack Overflow的创立者之一的 **Jeff Atwood** 在2007年提出了著名的 **Atwood**定律
  - any application that can be written in JavaScript, will eventually be written in JavaScript
  - 任何可以使用JavaScript来实现的应用都最终都会使用JavaScript实现
- **其实我们已经看到了，这句话正在一步步被应验：**
  - **Web**端的开发我们一直都是使用JavaScript
  - **移动端**开发可以借助于ReactNative、Weex、Uniapp等框架实现跨平台开发
  - **小程序端**的开发也是离不开JavaScript
  - **桌面端**应用程序我们可以借助于Electron来开发
  - **服务器端**开发可以借助于Node环境使用JavaScript来开发

#### JavaScript的痛点

- 并且随着近几年前端领域的快速发展，让JavaScript迅速被**普及和受广大开发者的喜爱**，借助于**JavaScript本身的强大**，也让**使用JavaScript开发的人员越来越多**

- **优秀的JavaScript没有缺点吗？**

  - 其实上由于各种历史因素，JavaScript语言本身**存在很多的缺点**
  - 比如ES5以及之前的**使用的var关键字关于作用域**的问题
  - 比如最初JavaScript设计的**数组类型并不是连续的内存空间**
  - 比如直到今天**JavaScript也没有加入类型检测**这一机制

- **JavaScript正在慢慢变好**

  - 不可否认的是，JavaScript正在慢慢变得越来越好，无论是从底层设计还是应用层面
  - ES6、7、8等的推出，每次都会让这门语言**更加现代、更加安全、更加方便**
  - 但是直到今天，JavaScript在**类型检测上依然是毫无进展**

  > JavaScript是一种动态类型语言，这意味着在运行时可以改变变量的类型，变量在声明时不需要指定类型，类型会在程序运行过程中自动确定。这为JavaScript带来了极大的灵活性，但也带来了一些类型相关的问题。
  >
  > 关于JavaScript在类型检测上的一些挑战和原因：
  >
  > 1. **历史原因**: JavaScript 最初被设计为一种简单的脚本语言，用于处理基本的浏览器交互。由于历史原因和向后兼容性的考虑，JavaScript 保留了很多早期设计的特性，包括它的动态类型系统。
  > 2. **灵活性**: JavaScript 的动态类型系统提供了极大的灵活性，使得 JavaScript 可以轻松处理各种各样的问题。这种灵活性使得 JavaScript 很受欢迎，很多开发者并不想要放弃这种灵活性。
  > 3. **向后兼容性**: 任何对JavaScript类型系统的修改都需要考虑到向后兼容性的问题。许多现有的JavaScript代码库和项目依赖于当前的动态类型系统，如果进行大的改动可能会导致这些代码库和项目出现问题。
  > 4. **性能影响**: 在JavaScript中引入更严格的类型检查可能会对性能产生影响。因为类型检查需要额外的计算，这可能会影响到代码的执行速度。
  >
  > 不过，需要指出的是，虽然JavaScript本身的类型检查存在挑战，但社区已经发展出了一些工具和语言，如TypeScript，Flow等，这些工具和语言在JavaScript的基础上增加了静态类型检查，以提高代码的可读性和可维护性，减少类型相关的错误。

### 类型带来的问题

- **首先wom 需要知道，编程开发中我们有一个共识：`错误出现的越早越好`**

  - 能在**写代码的时候**发现错误，就不要**在代码编译时**再发现（IDE的优势就是在代码编写过程中帮助我们发现错误）
  - 能在**代码编译期间**发现错误，就不要在**代码运行期间**再发现（类型检测就可以很好的帮助我们做到这一点）
  - 能在开发阶段发现错误，就不要在测试期间发现错误，能在测试期间发现错误，就不要在上线后发现错误

- **现在我们想探究的就是如何在 `代码编译期间` 发现代码的错误：**

  -  JavaScript可以做到吗？不可以，我们来看下面这段经常可能出现的代码问题

  ![image-20230602010951464](.\typescript_images\image-20230602010951464.png)

  ![image-20230602010955402](.\typescript_images\image-20230602010955402.png)

#### 类型错误

- **这是我们一个非常常见的错误**：
  - 这个错误很大的原因就是因为JavaScript没有对我们**传入的参数进行任何的限制**，只能等到**运行期间才发现这个错误**
  - 并且当这个错误产生时，会影响后续代码的继续执行，也就是整个项目都**因为一个小小的错误而深入崩溃**
- **当然，你可能会想：我怎么可能犯这样低级的错误呢？**
  - 当我们写像我们上面这样的简单的demo时，这样的错误很容易避免，并且当出现错误时，也很容易检查出来
  -  但是当我们开发一个**大型项目**时呢？你能保证自己**一定不会出现这样的问题**吗？而且如果我们是调用别人的类库，又如何知道让我们传入的到底是什么样的参数呢？
- **但是，如果我们可以给`JavaScript加上很多限制`，在开发中就可以很好的`避免这样的问题`了：**
  - 比如我们的getLength函数中str是一个`必传的类型`，没有调用者没有传编译期间就会报错
  - 比如我们要求它的必须是一个`String类型`，传入其他类型就直接报错
  - 那么就可以知道很多的错误问题在`编译期间`就被发现，而不是等到运行时再去发现和修改

#### 类型思维的缺失

- **我们已经简单体会到没有类型检查带来的一些问题，JavaScript因为从设计之初就没有考虑类型的约束问题，所以造成了前端开发人员关于`类型思维的缺失`：**
  - **前端开发人员**通常不关心变量或者参数是什么类型的，如果在必须确定类型时，我们往往需要使用各种判断验证通常不关心变量或者参数是什么类型的，如果在必须确定类型时，我们往往需要使用各种判断验证
  - 从其他方向转到前端的人员，也会因为没有类型约束，而总是担心自己的**代码不安全，不够健壮**
- **所以我们经常会说JavaScript`不适合开发大型项目`，因为当项目一旦庞大起来，这种宽松的类型约束会带来非常多的安全隐患，多人员开发它们之间也`没有良好的类型契约`**
  - 比如当我们去实现一个核心类库时，如果没有类型约束，那么需要对**别人传入的参数进行各种验证**来保证我们代码的健壮性
  -  比如我们去调用别人的函数，对方没有对函数进行任何的注释，我们只能去看里面的逻辑来理解这个函数需要**传入什么参数，返回值是什么类型**

#### JavaScript添加类型约束

- **为了弥补JavaScript类型约束上的缺陷，增加类型约束，很多公司推出了自己的方案：**
  - 2014年，Facebook推出了`flow`来对JavaScript进行类型检查
  - 同年，Microsoft微软也推出了`TypeScript1.0`版本
  -  他们都致力于为JavaScript提供类型检查
- 而现在，**无疑TypeScript已经完全胜出：**
  - Vue2.x的时候采用的就是flow来做类型检查
  - Vue3.x已经全线转向TypeScript，98.3%使用TypeScript进行了重构
  - 而Angular在很早期就使用TypeScript进行了项目重构并且需要使用TypeScript来进行开发
  - 而甚至Facebook公司一些自己的产品也在使用TypeScript
- **学习TypeScript不仅仅可以为我们的代码增加类型约束，而且可以培养我们前端程序员具备类型思维**
  - 如果之后想要学习其他语言，比如Java、Dart等也会是驾轻就熟

### 认识TypeScript

- **虽然我们已经知道TypeScript是干什么的，也知道它解决了什么样的问题，但是我们还是需要全面的来认识一下TypeScript到底是什么？**
- **我们来看一下TypeScript在GitHub和官方上对自己的定义：**
  - GitHub说法：**TypeScript is a superset of JavaScript that compiles to clean JavaScript output.**
    - 翻译：TypeScript是JavaScript的超集，可以编译生成简洁的JavaScript代码
  - TypeScript官网：**TypeScript is a typed superset of JavaScript that compiles to plain JavaScript**
    - 翻译：TypeScript是带有类型系统的JavaScript超集，它可以编译成普通的JavaScript代码
- **怎么理解上面的话呢？**
  - 我们可以将TypeScript理解成**加强版的JavaScript**
  - **JavaScript所拥有的特性，TypeScript全部都是支持的，并且它紧随ECMAScript的标准，所以ES6、ES7、ES8等新语法标准，它都是支持的**
  - TypeScript在**实现新特性的同时，总是保持和ES标准的同步甚至是领先**
  - 并且在语言层面上，**不仅仅增加了类型约束，而且包括一些语法的扩展，比如枚举类型（Enum）、元组类型（Tuple）等**
  - 并且**TypeScript最终会被编译成JavaScript代码**，所以你**并不需要担心它的兼容性问题，在编译时也可以不借助于Babel这样的工具(可以借用TSC，但是一般情况下还是借用Babel)**，因为TSC有部分缺陷
- **所以，我们可以把TypeScript理解成更加强大的JavaScript，不仅让JavaScript更加安全，而且给它带来了诸多好用的好用特性**

### TypeScript的特点

- **官方对TypeScript有几段特点的描述，我觉得非常到位（虽然有些官方，了解一下），我们一起来分享一下：**
- **始于JavaScript，归于JavaScript**
  - TypeScript从今天数以百万计的JavaScript开发者所熟悉的语法和语义开始
  - 使用现有的JavaScript代码，包括流行的JavaScript库，并从JavaScript代码中调用TypeScript代码
  - TypeScript可以编译出纯净、 简洁的JavaScript代码，并且可以运行在任何浏览器上、Node.js环境中和任何支持ECMAScript 3（或更高版本）的JavaScript引擎中
- **TypeScript是一个强大的工具，用于构建大型项目**
  - 类型允许JavaScript开发者在开发JavaScript应用程序时使用高效的开发工具和常用操作比如静态检查和代码重构
  - 类型是可选的，类型推断让一些类型的注释使你的代码的静态验证有很大的不同。类型让你定义软件组件之间的接口和洞察现有JavaScript库的行为
- **拥有先进的 JavaScript**
  - TypeScript提供最新的和不断发展的JavaScript特性，包括那些来自2015年的ECMAScript和未来的提案中的特性，比如异步功能和Decorators，以帮助建立健壮的组件
  - 这些特性为高可信应用程序开发时是可用的，但是会被编译成简洁的ECMAScript3（或更新版本）的JavaScript

#### 众多项目采用TypeScript

- **正是因为有这些特性，TypeScript目前已经在很多地方被应用：**
  - Angular源码在很早就**使用TypeScript来进行了重写**，并且**开发Angular也需要掌握TypeScript**
  - Vue3源码也**采用了TypeScript进行重写**，在**阅读源码时你会看到大量TypeScript的语法**
  - 包括目前已经变成**最流行的编辑器VSCode也是使用TypeScript来完成**的
  - 包括在**React中已经使用的ant-design的UI库，也大量使用TypeScript来编写**
  - 目前**公司非常流行Vue3+TypeScript、React+TypeScript的开发模式**
  - 包括**小程序开发，也是支持TypeScript**的

### 大前端的发展趋势

- **大前端是一群最能或者说最需要折腾的开发者：**
  - **客户端开发者**：从Android到iOS，或者从iOS到Android，到RN，甚至现在越来越多的客户端开发者接触前端相关知识（Vue、React、Angular、小程序）
  - **前端开发者**：从jQuery到AngularJS，到三大框架并行：Vue、React、Angular，还有小程序，甚至现在也要接触客户端开发（比如RN、Flutter）
  - 目前又面临着不仅仅学习ES的特性，还要学习TypeScript
  - 新框架的出现，我们又需要学习新框架的特性，比如vue3.x、react18等等
  - 新的工具也是层出不穷，比如vite（版本更新也很快）
- **但是每一样技术的出现都会让惊喜，因为他必然是解决了之前技术的某一个痛点的，而TypeScript真是解决了JavaScript存在的很多设计缺陷，尤其是关于类型检测的**
- **并且从开发者长远的角度来看，学习TypeScript有助于我们前端程序员培养 类型思维，这种思维方式对于完成大型项目尤为重要**

### TypeScript的编译环境

- **在前面我们提到过，TypeScript最终会被编译成JavaScript来运行，所以我们需要搭建对应的环境：**

  - 我们**需要在电脑上安装TypeScript**，这样就可以**通过TypeScript的Compiler将其编译成JavaScript**

  ![image-20230602034850914](.\typescript_images\image-20230602034850914.png)

- **所以，我们需要先可以先进行全局的安装：**

  - **安装TypeScript**: 通过npm（Node.js的包管理器）来全局安装TypeScript。在命令行中运行以下命令：

```js
npm install -g typescript
```

### TypeScript的运行环境

- **如果我们每次为了查看TypeScript代码的运行效果，都通过经过两个步骤的话就太繁琐了：**

  -  第一步：通过**tsc编译TypeScript到JavaScript代码**

    - 这会生成一个与TypeScript源文件名相同（除了后缀是`.js`）的JavaScript文件，例如：`main.js`。

    ```js
    //例如文件名称为main.ts
    tsc main.ts
    ```

  - 第二步：在**浏览器或者Node环境下运行JavaScript代码**

    ```js
    node main.js
    ```

- **是否可以简化这样的步骤呢？**

  - 比如**编写了TypeScript之后可以直接运行在浏览器上**？
  - 比如**编写了TypeScript之后，直接通过node的命令来执行**？

- **上面提到的两种方式，可以通过两个解决方案来完成：**

  - 方式一：通过**webpack，配置本地的TypeScript编译环境和开启一个本地服务，可以直接运行在浏览器**上
  - 方式二：通过**ts-node库，为TypeScript的运行提供执行环境**

#### **方式一：webpack配置**

- coderwhy老师的配置文章地址：[TypeScript(二)使用Webpack搭建环境 (qq.com)](https://mp.weixin.qq.com/s/wnL1l-ERjTDykWM76l4Ajw)

- 笔记中也配套使用步骤如下：

使用Webpack可以帮助我们自动化编译和打包过程，而不需要手动运行`tsc`命令。以下是使用Webpack与TypeScript的基本步骤：

1. **安装必要的包**：你需要安装Webpack，Webpack CLI，TypeScript，以及ts-loader（这是一个Webpack插件，用于处理TypeScript文件）。在项目根目录下，使用npm初始化项目并安装这些包：

```js
npm init -y
npm install --save-dev webpack webpack-cli typescript ts-loader
```

2. **创建TypeScript配置文件**：创建一个名为`tsconfig.json`的文件。这个文件会告诉TypeScript编译器如何处理TypeScript代码。一个基本的`tsconfig.json`可能看起来像这样：

```js
{
    "compilerOptions": {
        "outDir": "./dist/",
        "sourceMap": true,
        "noImplicitAny": true,
        "module": "es6",
        "moduleResolution": "node",
        "target": "es5",
        "allowJs": true
    }
}
```

3. **创建Webpack配置文件**：创建一个名为`webpack.config.js`的文件。这个文件将告诉Webpack如何处理项目中的文件。一个处理TypeScript的基本Webpack配置可能看起来像这样：

```ts
const path = require('path');

module.exports = {
  entry: './src/index.ts',
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        use: 'ts-loader',
        exclude: /node_modules/,
      },
    ],
  },
  resolve: {
    extensions: [ '.tsx', '.ts', '.js' ],
  },
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
};
```

- 这个配置告诉Webpack入口点是`src/index.ts`，并且使用`ts-loader`来处理所有`.ts`和`.tsx`文件。

4. **编写TypeScript代码**：在`src`目录下创建`index.ts`，然后开始编写你的TypeScript代码。
5. **运行Webpack**：在命令行中运行`npx webpack`（或者如果你已经全局安装了webpack，可以直接运行`webpack`）。Webpack将会按照你的配置文件，编译和打包你的TypeScript代码，然后输出到`dist/bundle.js`。
6. **运行JavaScript代码**：你现在可以运行编译后的JavaScript代码，例如使用Node.js：`node dist/bundle.js`。

需要注意的是，这只是一个基本的配置。在实际项目中，我们可能需要添加更多的配置，例如处理CSS、图片、字体等，也可能需要添加插件来优化你的代码、提供开发服务器等。

#### 方式二：使用ts-node

- **安装ts-node**

```js
npm install ts-node -g
```

- **另外ts-node需要依赖 tslib 和 @types/node 两个包：**

```ts
npm install tslib @types/node -g
```

- **现在，我们可以直接通过 ts-node 来运行TypeScript的代码：**

```
ts-node math.ts
```

#### 变量的声明

- **我们已经强调过很多次，在TypeScript中定义变量需要指定 标识符 的类型**

  > 在JavaScript和TypeScript中，`String`和`string`代表不同的概念。
  >
  > `string`是一个原始类型（primitive type），它是不可变的。当你创建一个字符串，如 `let s: string = "hello";`，你不能改变它。你只能重新赋值一个新的字符串给`s`，如 `s = "world";`。这和JavaScript中的字符串原始类型（string primitive）是一样的。
  >
  > `String`，另一方面，是一个全局对象，是一个构造函数，用于生成字符串对象。当你使用`new String("hello")`，你实际上创建了一个`String`对象而不是一个字符串原始值。虽然`String`对象在某些上下文中可以像字符串原始值一样使用，但它们有一些额外的方法和属性，并且有一些行为上的差异。
  >
  > 在TypeScript中，如果你想声明一个字符串类型的变量，你应该使用`string`，而不是`String`。这是因为`String`类型同时包含字符串原始值和`String`对象，而`string`类型仅包含字符串原始值。大多数情况下，你应该使用字符串原始值，而不是`String`对象。
  >
  > 总的来说，`string`和`String`之间的区别主要在于它们的数据类型和行为。`string`是一个原始类型，它是不可变的，而`String`是一个对象，具有一些额外的方法和属性。在TypeScript中，你通常应该优先使用`string`类型。

- **所以完整的声明格式如下：**

  - **声明了类型后TypeScript就会进行`类型检测`，声明的类型可以称之为`类型注解（Type Annotation）`**

    > var/let/const 标识符: 数据类型 = 赋值;

  - 比如**我们声明一个message，完整的写法如下：**

    - 注意：这里的string是小写的，和String是有区别的
    - string是TypeScript中定义的字符串类型，String是ECMAScript中定义的一个类

    ```ts
    let message: string = "你好，小余"
    ```

  - **如果我们给message赋值其他类型的值，那么就会报错：**

  ![image-20230602041041247](.\typescript_images\image-20230602041041247.png)

#### 声明变量的关键字

- **在TypeScript定义变量（标识符）和ES6之后一致，可以使用var、let、const来定义。**

![image-20230602041407401](.\typescript_images\image-20230602041407401-1685650447732-1.png)

- **当然，在tslint中并不推荐使用var来声明变量：**

  - 可见，在TypeScript中并不建议再使用var关键字了，主要原因和ES6升级后let和var的区别是一样的，var是没有块级作用域的，会引起很多的问题

  ![image-20230602041548121](.\typescript_images\image-20230602041548121.png)

#### 变量的类型推导（推断）

- **在开发中，有时候为了方便起见我们并不会在声明每一个变量时都写上对应的数据类型，我们更希望可以通过TypeScript本身的特性帮助我们推断出对应的变量类型：**

![image-20230602041806479](.\typescript_images\image-20230602041806479.png)

- **如果我们给names赋值2023：**

![image-20230602041752205](.\typescript_images\image-20230602041752205.png)

- **这是因为在一个变量第一次赋值时，会根据后面的赋值内容的类型，来推断出变量的类型：**
  - 上面的message就是因为后面赋值的是一个string类型，所以message虽然没有明确的说明，但是依然是一个string类型

![image-20230602041823753](.\typescript_images\image-20230602041823753.png)
在TypeScript中，类型推导是一个强大的特性，它能让你编写的代码更加简洁，并且能够减少因类型错误而导致的bug。但在使用类型推导时，我们需要注意以下几点：

1. **Best common type**: 当TypeScript尝试推导一个类型时，它会找出所有可能的类型，然后找出最适合的一个。例如，如果你有一个数组，其中包含两种类型的元素，TypeScript将尝试找到这两种类型的最佳公共类型。如果没有最佳公共类型，TypeScript将会报错。

```ts
let array = [0, 1, "2"]; // Error: No best common type exists among array members.
```

2. **Contextual Typing**: TypeScript也会使用上下文来推导类型。这意味着在某些情况下，TypeScript将根据周围的代码推导出类型。例如，如果你在一个期望返回字符串的函数中返回一个表达式，那么TypeScript将推导出该表达式的类型是字符串。

```ts
const getString = (): string => {
  return "hello"; // TypeScript knows this must be a string
}
```

3. **Implicit any**: 如果TypeScript不能推导出一个类型，它将默认为`any`类型。这是你需要格外注意的地方，因为`any`类型会绕过TypeScript的类型检查。你可以通过设置`noImplicitAny`选项为`true`来避免这种情况。
4. **Type inference does not work backwards**: TypeScript的类型推导不会向后工作。这意味着，如果一个函数的返回类型依赖于其参数的类型，TypeScript不会先推导出函数的返回类型，然后用这个信息去推导参数的类型。相反，它将从参数开始，然后推导出返回类型。

### JavaScript和TypeScript的数据类型

- **我们经常说TypeScript是JavaScript的一个超级：**

![image-20230602041850833](.\typescript_images\image-20230602041850833.png)

#### JavaScript类型 – number类型

- **数字类型是我们开发中经常使用的类型，TypeScript和JavaScript一样，不区分整数类型（int）和浮点型（double），统一为number类型**

![image-20230602041916555](.\typescript_images\image-20230602041916555.png)

- **学习过ES6应该知道，ES6新增了二进制和八进制的表示方法，而TypeScript也是支持二进制、八进制、十六进制的表示：**

![image-20230602041933955](.\typescript_images\image-20230602041933955.png)

#### JavaScript类型 – boolean类型

- **boolean类型只有两个取值：true和false，非常简单**

```ts
//boolean类型的表示
let flag:boolean = true;
flag = false
flag = 20 > 30
```

#### JavaScript类型 – string类型

- **string类型是字符串类型，可以使用单引号或者双引号表示：**

```ts
//string类型的表示
let person:string = "中国人"
person = '我是中国福建人'
```

- **同时也支持ES6的模板字符串来拼接变量和字符串：**

```ts
const myname:string = "小余"
const age:number = 18
const info = `我是${myname},今年${age}岁了`
console.log(info);//我是小余,今年18岁了
```

#### JavaScript类型 – Array类型

- **`数组类型`的定义也非常简单，有两种方式：**

  - Array<string>事实上是一种泛型的写法，我们会在后续中学习它的用法

  ```ts
  //注意事项：在真实开发中，数组一般存放相同的类型，不要去存放不同类型
  const names:string[] = ["小余","coderwhy","小满"]
  //泛型写法约束，数组中只能存放字符串类型
  const names2:Array<string> = ["小余","coderwhy","小满"]
  
  names.push("123")
  names2.push("123")
  ```

- **如果添加其他类型到数组中，那么会报错：**

![image-20230602114547493](.\typescript_images\image-20230602114547493.png)

#### JavaScript类型 – Object类型

- **object对象类型可以用于描述一个对象：**

  -  很少使用

  - `object`类型是一个宽泛的类型，用于描述一个非原始类型的值。非原始类型是除`number`、`string`、`boolean`、`symbol`、`null`或`undefined`之外的任何类型。

    然而，`object`类型的问题在于它提供的信息很少。如果你有一个`object`类型的变量，你只知道它不是原始类型的值，但除此之外你对它一无所知。你不能访问任何特定的属性，因为TypeScript不知道它们是否存在。

  ```ts
  const myInfo:object = {
    name:"xiaoyu",
    age:18,
    height:1.75
  }
  ```

- **但是从myinfo中我们不能获取数据，也不能设置数据：**

  ```ts
  const myInfo:object = {
    name:"xiaoyu",
    age:18,
    height:1.75
  }
  
  myInfo["name"] = "小余"
  console.log(myInfo["name"],myInfo["age"]);
  ```

  ![image-20230602114819198](.\typescript_images\image-20230602114819198.png)

- 相比之下，如果我们使用一个接口(后面讲)来定义我们的对象，TypeScript就能给我们提供更多的信息，我们也能写出更安全的代码：

  ```ts
  interface Person {
    name: string;
  }
  
  function printName(person: Person) {
    console.log(person.name); // Okay
  }
  ```

  这是为什么在实践中，我们更倾向于使用像接口或类型别名这样的结构来描述对象的形状，而不是使用`object`类型。

#### JavaScript类型 – Symbol类型

- **在ES5中，如果我们是不可以在对象中添加相同的属性名称的，比如下面的做法：**

![image-20230602114837548](.\typescript_images\image-20230602114837548.png)

- **通常我们的做法是定义两个不同的属性名字：比如identity1和identity2**
- **但是我们也可以通过symbol来定义相同的名称，因为Symbol函数返回的是不同的值：**

![image-20230602114853433](.\typescript_images\image-20230602114853433.png)

#### JavaScript类型 – null和undefined类型

- **在 JavaScript 中，undefined 和 null 是两个基本数据类型**
- **在TypeScript中，它们各自的类型也是undefined和null，也就意味着它们既是实际的值，也是自己的类型：**

```ts
let n : null = null
let u : undefined = undefined
```

### 函数的参数类型

- **函数是JavaScript非常重要的组成部分，TypeScript允许我们指定函数的参数和返回值的类型**

- **参数的类型注解**

  - 声明函数时，可以在**每个参数后添加类型注解**，以**声明函数接受的参数类型：**

  ![image-20230602115036958](.\typescript_images\image-20230602115036958.png)

### 函数的返回值类型

- **我们也可以添加返回值的类型注解，这个注解出现在函数列表的后面：**
  - 也就是要求函数返回值必须是数字类型

![image-20230602115136463](.\typescript_images\image-20230602115136463.png)

- **和变量的类型注解一样，我们通常情况下不需要返回类型注解，因为TypeScript会根据 return 返回值推断函数的返回类型：**
  - 某些第三方库处于方便理解，会明确指定返回类型，看个人喜好

### 匿名函数的参数

- **匿名函数与函数声明会有一些不同：**

  - 当一个函数出现在TypeScript可以确定该函数会被如何调用的地方时
  - **该函数的参数**会自动指定类型

  ![image-20230602115224211](.\typescript_images\image-20230602115224211.png)

- **我们并没有指定item的类型，但是item是一个string类型：**

  - 这是因为TypeScript会根据forEach函数的类型以及数组的**类型推断出item的类型**
  - 这个过程称之为**上下文类型（contextual typing）**，因为函数执行的上下文可以帮助确定参数和返回值的类型
  - 所以在匿名函数中最好不要加类型注解，因为匿名函数通常都是我们要传递给别人的，而别人基本上已经定义好了只能传递什么类型内容进去了，而ts也已经通过上下文知晓匿名函数的形参类型了

### 对象类型

- **如果我们希望限定一个函数接受的参数是一个对象，这个时候要如何限定呢？**

  - 我们可以使用对象类型；

  ![image-20230602115310465](.\typescript_images\image-20230602115310465.png)

- **在这里我们使用了一个对象来作为类型：**

  - 在对象我们可以添加属性，并且告知TypeScript该属性需要是什么类型
  
  - 属性之间可以使用 , 或者 ; 来分割，最后一个分隔符是可选的
  
    ```ts
    function printCoordinate(point:{x: number;y: number}) {//采用;分号运行
      xxxx
    }
    ```
  
  - 每个属性的类型部分也是可选的，如果不指定，那么就是any类型

### 可选类型

> 在 TypeScript 中，可选类型是一种非常有用的特性，它允许我们为对象的属性或函数的参数设定一个可选的类型。也就是说，可以选择是否为对象的某个属性赋值，或者是否为函数的某个参数提供值。
>
> 对于可选属性，可以在对象类型的属性后面添加一个问号（`?`）来表示该属性是可选的。例如，可以定义一个名为 `Person` 的接口，它有一个必须的 `name` 属性和一个可选的 `age` 属性：

```ts
interface Person {
  name: string;
  age?: number;
}
```

- 这样，我就可以创建一个 `Person` 对象，可以有 `age` 属性，也可以没有：

```ts
let p1: Person = { name: 'Alice' }; // OK
let p2: Person = { name: 'Bob', age: 23 }; // OK
```

- 对于可选参数，我可以在函数定义中的参数后面添加一个问号（`?`）来表示该参数是可选的。例如，我可以定义一个名为 `greet` 的函数，它有一个必须的 `name` 参数和一个可选的 `message` 参数：

```ts
function greet(name: string, message?: string) {
  if (message) {
    console.log(`${name} says: ${message}`);
  } else {
    console.log(`Hello, ${name}`);
  }
}
```

- 这样，我可以在调用 `greet` 函数时，可以有 `message` 参数，也可以没有：

```
greet('Alice'); // OK
greet('Bob', 'How are you?'); // OK
```

需要注意的是，可选参数必须位于必选参数之后。如果我尝试将可选参数放在必选参数之前，TypeScript 会给出错误提示。

### TS中独有类型

#### TypeScript类型 - any类型

- **在某些情况下，我们确实`无法确定一个变量的类型，并且可能它会发生一些变化`，这个时候我们可以使用`any类型`（类似于Dart语言中的dynamic类型）。**
- **any类型有点像一种讨巧的TypeScript手段：**
  - 我们可以**对any类型的变量进行任何的操**作，包括**获取不存在的属性、方法**，因为它允许我们在编程时选择并明确表示我们想要opt-out（选择不参与）类型检查。这样，我们就可以通过编译器的类型检查，让某些代码的部分能以任何类型存在
  - 我们**给一个any类型的变量赋值任何的值，比如数字、字符串的值**

```ts
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean
```

在上述代码中，`notSure`被定义为`any`类型，这意味着我们可以将任何类型的值赋给`notSure`，不会有类型错误。

需要注意的是，过度使用`any`类型可能会削弱TypeScript提供的类型安全性。我们应该尽量避免在我们的代码中大量使用`any`类型，尽管有时它可能是必要的，但是在可能的情况下，最好使用更精确的类型。

- **如果对于某些情况的处理过于繁琐不希望添加规定的类型注解，或者在引入一些第三方库时，缺失了类型注解，这个时候我们可以使用any：**
  - 包括**在Vue源码中，也会使用到any来进行某些类型的适配**；

#### TypeScript类型 - unknown类型

- **unknown是TypeScript中比较特殊的一种类型，它用于描述类型不确定的变量**
  - 和any类型有点类似，但是unknown类型的值上做任何事情都是不合法的，因为他假设不知道你什么类型 你的操作会造成什么后果就不知道了，既然不知道就把你所做的操作当作不合法的(除非你进行类型检查一下)
  - `unknown`类型是一种安全的类型，它代表任何值。这听起来可能和`any`类型很像，但是它们的关键区别在于，当我们试图对`any`类型的值进行操作时，TypeScript编译器会认为这是可以的，而当我们试图对`unknown`类型的值进行操作时，TypeScript编译器会产生一个错误。
  - 这是因为`unknown`类型被认为是安全的，它要求我们在访问`unknown`类型的值或对其进行任何操作之前，**首先进行类型检查(类型缩小)或类型断言**。

```ts
let value: unknown;

value = true; // OK
value = 42;   // OK
value = "Hello World"; // OK
value = []; // OK
value = {}; // OK

// 在操作之前，需要进行类型检查
if (typeof value === "number") {
    console.log(value * 2);
}
```

在上述代码中，我们可以将任何类型的值赋给`value`，但是在对`value`进行操作之前，我们需要先进行类型检查。

使用`unknown`类型，我们可以更安全地处理不确定类型的值，同时还能享受TypeScript的类型检查的优点。

#### TypeScript类型 - void类型

- **void通常用来指定一个函数是没有返回值的，那么它的返回值就是void类型：**

```ts
function sum(num1:number,num2:number){
	console.log(num1+num2)
}
```

- **这个函数我们没有写任何类型，那么它默认返回值的类型就是void的，我们也可以显示的来指定返回值是void：**

```ts
function sum(num1:number,num2:number): void {
	console.log(num1+num2)
}
```

- **这里还有一个注意事项：**
  - 我们可以将undefined赋值给void类型，也就是函数可以返回undefined
- **当基于上下文的类型推导（Contextual Typing）推导出返回类型为 void 的时候，并不会强制函数一定不能返回内容**
  - 平时是很少使用


![image-20230602120643642](.\typescript_images\image-20230602120643642.png)![image-20230602120647203](.\typescript_images\image-20230602120647203.png)

#### TypeScript类型 - never类型

- **never 表示永远不会发生值的类型，比如一个函数：**
  - 如果一个函数中是一个死循环或者抛出一个异常，那么这个函数会返回东西吗？
  - 不会，那么写void类型或者其他类型作为返回值类型都不合适，我们就可以使用never类型

- **它常常用在以下几种场景中：(实际项目中很少使用)**

  1. **函数永远不会返回值：**当一个函数永远不可能有返回值时，比如它总是会抛出错误，那么你可以为它的返回值指定 `never` 类型：

  ```ts
  function throwError(message: string): never {
      throw new Error(message);
  }
  ```

  在上面的例子中，`throwError` 函数会抛出一个错误，它不会有正常的返回值，所以它的返回类型是 `never`。

  2. **无穷循环(死循环)：**如果一个函数有一个无穷循环，那么它也可以被标记为 `never` 类型，因为它永远不会有返回值。

  ```ts
  function infiniteLoop(): never {
      while (true) {}
  }
  ```

  - 在这个例子中，`infiniteLoop` 函数将永远不会结束，所以它的返回类型是 `never`。

  注意，`never` 类型是任何类型的子类型，也可以赋值给任何类型；然而，没有类型是 `never` 的子类型或可以赋值给 `never` 类型（除了 `never` 本身之外）。这意味着你可以将 `never` 类型赋值给例如 `number` 类型的变量，但是你不能将 `number` 类型的值赋值给 `never` 类型的变量。
  
  - **应用场景总结**
    - 某些情况下会自动进行类型推导除never
    - 开发框架(通用性工具)的时候可能用到never
    - 封装类型工具的时候会用到(类型体操)
  
  ```ts
  function handleMessage(message:string | number){
    switch(typeof message){
      case "string":
        console.log(message.length);
        break
      case "number":
        console.log(message);
        break
      default:
        const check: never = message
    }
  }
  ```
  
  > 在这段代码中，`check`的类型被推断为`never`，这是因为在所有可能的情况下，`message`都被处理了（它只能是`string`或`number`），所以在`default`分支中，`message`的类型就是`never`。
  >
  > 这种情况下，将一个变量设置为`never`类型可能看起来没有什么实际用处，但是它在编程中是有意义的，因为它帮助我们确保了所有可能的情况都被处理了。当你有一个变量其类型是`never`，那么你可以确定在你的代码逻辑中，你已经处理了该变量所有可能的值。
  >
  > 如果你在`switch`语句中添加了一个新的`case`，或者更改了`message`的类型（例如，让它也可以是`boolean`），那么`check`的类型就不再是`never`，并且TypeScript会给你一个错误，告诉你你需要处理这个新的可能性。这是一种类型安全的保障，可以帮助我们避免在处理多种可能性时遗漏某一种情况。
  >
  > 此外，还有一种常见的使用`never`类型的情况，那就是抛出错误。如果你有一个函数其返回类型是`never`，那么你可以确定该函数永远不会正常返回（例如，它总是抛出一个错误）。这有助于我们理解和正确使用这些函数。

#### TypeScript类型 - tuple类型

> Tuple 类型是 TypeScript 中的一个特殊的类型，它允许我们创建具有有限数量的已知类型元素的数组，而不是一种类型的数组。元素的类型并不必须相同。每个元素的位置都有固定的类型，我们可以把它想象成一个固定长度和固定类型的数组。

- **tuple是元组类型，很多语言中也有这种数据类型，比如Python、Swift等**

![image-20230602121131562](.\typescript_images\image-20230602121131562.png)

- **那么tuple和数组有什么区别呢？**

  - 首先，**数组中通常建议存放相同类型的元素，不同类型的元素是不推荐放在数组中**。（可以放在对象或者元组中）
  - 其次，**元组中每个元素都有自己特性的类型，根据索引值获取到的值可以确定对应的类型**

  Tuple 类型非常适合用于定义有序的元素集合。例如，你可以使用 tuple 类型来表示一个带有 `string` 类型的名称和 `number` 类型的分数的学生数据：

```ts
let student: [string, number];
student = ['Alice', 85];
```

##### Tuple的应用场景

-  **那么tuple在什么地方使用的是最多的呢？**

  - **tuple通常可以作为返回的值，在使用的时候会非常的方便**
  - 例如，我们可能有一个函数，它返回一个字符串（比如名字）和一个数字（比如年龄）：

  ```ts
  function getNameAndAge(): [string, number] {
      // ... some code here ...
      return ['Alice', 25];
  }
  
  let [name, age] = getNameAndAge();
  console.log(name); // Alice
  console.log(age);  // 25
  ```

  在这个例子中，`getNameAndAge` 函数的返回类型是一个 tuple：`[string, number]`。当我们调用这个函数的时候，我们得到一个长度为 2 的数组，第一个元素是字符串，第二个元素是数字。然后我们可以使用解构赋值（destructuring assignment）将这两个值分别赋给变量 `name` 和 `age`。

  这种使用 tuple 的模式非常有用，因为它允许函数返回多个不同类型的值，而无需创建一个临时的对象或数组。这可以使代码更简洁，更易读。

### TypeScript类型总结

TypeScript 类型系统是该语言的核心特性之一。以下是 TypeScript 主要的类型及其简要描述：

1. **Boolean**：布尔类型，表示逻辑值 true 或 false。
2. **Number**：数字类型，表示整数或浮点数值。
3. **String**：字符串类型，表示文本值。
4. **Array**：数组类型，表示元素类型和数量可以预先定义的列表。
5. **Tuple**：元组类型，表示已知元素数量和类型的数组，各元素的类型不必相同。
6. **Enum**：枚举类型，表示由一组有名的常数集合组成的类型。
7. **Any**：任意类型，表示可以是任何类型的值，可用于绕过类型检查。
8. **Void**：表示没有任何类型，通常用于标明函数没有返回值。
9. **Null and Undefined**：null 和 undefined 类型，分别对应值 null 和 undefined。
10. **Never**：表示永远不会出现的值的类型，例如，never 类型的函数永远不会返回值。
11. **Object**：表示非原始类型，也就是除 number，string，boolean，symbol，null 或 undefined 之外的类型。
12. **Unknown**：表示未知类型的值，是 any 类型的安全等价。

除此之外，TypeScript 还支持高级类型，如交叉类型（Intersection Types）、联合类型（Union Types）、类型别名（Type Aliases）、字符串和数字字面量类型（String and Number Literal Types）、可辨识联合（Discriminated Unions）、映射类型（Mapped types）、条件类型（Conditional Types）等。

- 字面量类型在后续会讲到(目前到这里还用不到，在后面联合类型中一并写上)：![image-20230602195824127](.\typescript_images\image-20230602195824127.png)

## TypeScript语法细节

### 联合类型

- **TypeScript的类型系统允许我们使用多种运算符，从`现有类型中构建新类型`**
- **我们来使用第一种组合类型的方法：`联合类型（Union Type）`**
  - 联合类型是由两个或者多个其他类型组成的类型
  - 表示可以是这些类型中的任何一个值
  - 联合类型中的每一个类型被称之为联合成员（union's members）

![image-20230604034514739](.\typescript_images\image-20230604034514739.png)

#### 使用联合类型

- **传入给一个联合类型的值是非常简单的：只要保证是联合类型中的某一个类型的值即可**

  - 但是我们拿到这个值之后，我们应该如何使用它呢？因为**它可能是任何一种类型**
  - 比如我们拿到的值可能是string或者number，我们就不能对其调用string上的一些方法

- **那么我们怎么处理这样的问题呢？**

  - 我们需要使用**缩小（narrow）联合**

    - 在TypeScript中，"缩小"（narrowing）是一种技术，它可以让编译器更精确地了解某个变量的类型。当我们使用一种方法（如类型断言、类型保护或者类型谓词等）来明确变量的类型时，我们就是在对类型进行缩小。
    - 联合类型（Union types）在TypeScript中是一种复合类型，表示一个值可以是几种类型之一。我们可以使用缩小来将联合类型的可能性范围减小到一种具体的类型。

  - TypeScript可以**根据我们缩小的代码结构，推断出更加具体的类型**

    ```ts
    //比如，我们有一个类型为 string | number 的变量 value，我们可以通过 typeof 操作符来缩小它的类型：
    let value: string | number;
    
    if (typeof value === 'string') {
      // 在这个代码块中，TypeScript知道value是一个字符串
      console.log(value.toUpperCase());
    } else if (typeof value === 'number') {
      // 在这个代码块中，TypeScript知道value是一个数字
      console.log(value.toFixed(2));
    }
    //在这个例子中，我们通过 typeof 操作符的使用，对 value 进行了类型缩小，使得 TypeScript 能够准确知道 value 在不同的代码块中的具体类型。在每个代码块中，我们可以安全地调用该类型的特定方法，而不用担心类型错误。
    ```

### 类型别名

- **在前面，我们通过在类型注解中编写 对象类型 和 联合类型，但是当我们想要多次在其他地方使用时，就要编写多次**
- **比如我们可以给对象类型起一个别名：**

```ts
type PersonType = {
  name:string,
  age:number,
}

function Person(Person:PersonType){
  console.log(Person.name,Person.age);
}

Person({name:"小余",age:20})
```

### 接口的声明

- **在前面我们通过type可以用来声明一个对象类型：**

![image-20230604043336041](.\typescript_images\image-20230604043336041.png)

- **对象的另外一种声明方式就是通过接口来声明：**

![image-20230604043347457](.\typescript_images\image-20230604043347457.png)

- **那么它们有什么区别呢？**
  - 类型别名和接口非常相似，在定义对象类型时，大部分时候，你可以任意选择使用
  - 接口的几乎所有特性都可以在 type 中使用（后续我们还会学习interface的很多特性）

#### interface和type区别

- **我们会发现interface和type都可以用来定义对象类型，那么在开发中定义对象类型时，到底选择哪一个呢？**
  - 如果是`定义非对象类型`，通常`推荐使用type`，比如Direction、Alignment、一些Function+
- **如果是定义对象类型，那么他们是有区别的：**
  - interface 可以重复的对某个接口来定义属性和方法
  - 而type定义的是别名，别名是不能重复的

![image-20230604043548083](.\typescript_images\image-20230604043548083.png)![image-20230604043551461](.\typescript_images\image-20230604043551461.png)

- **所以，interface可以为现有的接口提供更多的扩展。**

```ts
// 1.区别一: type类型使用范围更广, 接口类型只能用来声明对象
type MyNumber = number
type IDType = number | string


// 2.区别二: 在声明对象时, interface可以多次声明
// 2.1. type不允许两个相同名称的别名同时存在
// type PointType1 = {
//   x: number
//   y: number
// }

// type PointType1 = {
//   z?: number
// }


// 2.2. interface可以多次声明同一个接口名称
interface PointType2 {
  x: number
  y: number
}

interface PointType2 {
  z: number
}

const point: PointType2 = {
  x: 100,
  y: 200,
  z: 300
}


// 3.interface支持继承的
interface IPerson {
  name: string
  age: number
}

interface IKun extends IPerson {
  kouhao: string
}

const ikun1: IKun = {
  kouhao: "你干嘛, 哎呦",
  name: "kobe",
  age: 30
}

// 4.interface可以被类实现(TS面向对象时候再讲)
// class Person implements IPerson {

// }


// 总结: 如果是非对象类型的定义使用type, 如果是对象类型的声明那么使用interface
//type的目前本身就是为了做类型限制的，在一般使用中，使用哪个都是无所谓的，只是interface在对象类型声明中有更多的一些选项可以拓展
```

### 交叉类型

- **前面我们学习了联合类型：**

  - 联合类型表示满足多个类型中一个即可

  ```ts
  type	Alignment = 'left' | 'right' | 'center'
  ```

- **还有另外一种类型合并，就是交叉类型（Intersection Types）：**

  - 交叉类似表示需要满足多个类型的条件
  - 交叉类型使用 & 符号

- **例如下面的交叉类型：**

  - 表达的含义是**number和string要同时满足**
  - 但是**有同时满足是一个number又是一个string的值**吗？其实是没有的，所以MyType其实是一个never类型

  ![image-20230604212229820](.\typescript_images\image-20230604212229820.png)

#### 交叉类型的应用

- **所以，在开发中，我们进行交叉时，通常是对对象类型进行交叉的：**

```ts
// 定义一个类型Person，拥有name属性
interface Person {
  name: string;
}

// 定义一个类型Logger，拥有log方法
interface Logger {
  log: (message: string) => void;
}

// 使用交叉类型将Person和Logger类型结合，得到一个新的类型LoggedPerson
type LoggedPerson = Person & Logger;

// 创建一个LoggedPerson类型的对象
let jim: LoggedPerson = {
  name: 'Jim',
  // 这里的log方法就是Logger类型的方法
  log: (message) => {
    console.log(message);
  },
};

// 测试jim对象的属性和方法
console.log(jim.name); // 输出: Jim
jim.log('Hello, TypeScript'); // 输出: Hello, TypeScript
```

`LoggedPerson` 类型是 `Person` 类型和 `Logger` 类型的交叉类型，它同时拥有 `Person` 类型的 `name` 属性和 `Logger` 类型的 `log` 方法。这样我们就可以创建一个既有 `Person` 类型的属性又有 `Logger` 类型方法的 `LoggedPerson` 类型对象。

交叉类型很适合在我们希望对象同时拥有多个类型的特性时使用，它可以帮助我们创建更复杂、更灵活的类型。

### 类型断言as

- **有时候TypeScript无法获取具体的类型信息，这个我们需要使用类型断言（Type Assertions）**

  -  比如我们通过 document.getElementById，TypeScript只知道该函数会返回 HTMLElement ，但并不知道它具体的类型：

  ![image-20230604212441338](.\typescript_images\image-20230604212441338.png)

- **TypeScript只允许类型断言转换为 更具体 或者 不太具体 的类型版本，此规则可防止不可能的强制转换：**

  - 这个什么意思呢？就是“更具体”：当前类型所包括的类型，"不太具体"：当前类型被包括的类型(通常指any或者unknown)

![image-20230604212653333](.\typescript_images\image-20230604212653333.png)

> 最好别这么做，存在安全隐患。在特殊的情况下会用到

![image-20230604212707739](.\typescript_images\image-20230604212707739.png)

### 非空类型断言!

> 非空类型断言是一种特殊的语法，它用于告诉TypeScript编译器一个表达式的值永远不会是null或undefined。这在处理可能的null或undefined值时非常有用。

- **当我们编写下面的代码时，在执行ts的编译阶段会报错：**

  - 这是因为传入的message有可能是为undefined的，这个时候是不能执行方法的

  ![image-20230604212748198](.\typescript_images\image-20230604212748198.png)

- **但是，我们确定传入的参数是有值的，这个时候我们可以使用非空类型断言：**

  - **非空断言使用的是 `!`** ，表示可以确定某个标识符是有值的，跳过ts在编译阶段对它的检测
  - 注意，我们应该只在确定表达式的值永远不会是null或undefined的时候使用非空类型断言。如果表达式的值可能是null或undefined，那么使用非空类型断言可能会导致运行时错误。

![image-20230604212828240](.\typescript_images\image-20230604212828240.png)

```ts
//非空类型断言的语法是在表达式后面加上感叹号!。例如，假设我们有一个可能是字符串或undefined的类型：
let s: string | undefined;
//如果我们确定s在某个时刻肯定不是undefined，但是TypeScript编译器无法推断出这一点，我们可以使用非空类型断言：
console.log(s!.length); // 告诉TypeScript编译器：我们确定s不是undefined
//在这个例子中，s!.length告诉TypeScript编译器，我们知道s在这个时刻肯定不是undefined，所以可以安全地访问它的length属性。
```

### 字面量类型

- **除了前面我们所说过的类型之外，也可以使用字面量类型（literal types）：**

![image-20230604212853565](.\typescript_images\image-20230604212853565.png)

```ts
function flipCoin(choice: "heads" | "tails") {
  // 我们只能传入 "heads" 或 "tails"
  // 这里的 "heads" 和 "tails" 就是字符串字面量类型
}

flipCoin("heads"); // 这是可以的
flipCoin("tails"); // 这也是可以的
flipCoin("feet"); // 这将报错，因为 "feet" 不在允许的类型中

//在上述例子中，choice 参数的类型被注解为 "heads" | "tails"。这是一个由两个字符串字面量类型组成的联合类型，表示 choice 的值只能是 "heads" 或 "tails"。
```

- **那么这样做有什么意义呢？**
  - 默认情况下这么做是没有太大的意义的，但是我们可以将多个类型联合在一起；

![image-20230604212907987](.\typescript_images\image-20230604212907987.png)

同样的，我们也可以使用数字字面量类型：

```ts
type DiceRoll = 1 | 2 | 3 | 4 | 5 | 6;

function rollDice(): DiceRoll {
  // 这个函数的返回值必须是 1, 2, 3, 4, 5, 6 中的一个
  return (Math.floor(Math.random() * 6) + 1) as DiceRoll;
}

const result = rollDice(); // result 只可能是 1, 2, 3, 4, 5, 6 中的一个
```

- 在这个例子中，`DiceRoll` 类型被定义为 `1 | 2 | 3 | 4 | 5 | 6`，这表示一个骰子的可能结果。`rollDice` 函数的返回值类型被注解为 `DiceRoll`，这意味着它的返回值只能是 1 到 6 的整数。

#### 字面量推理

- 像下方中我们在定义请求函数的时候，method其实是有固定的请求方式的，而不是什么aaa，bbbb都能填进来，这时候就可以使用字面量推理了

![image-20230604233250717](.\typescript_images\image-20230604233250717.png)

- 从上面图中，我们看到了`info.method`居然报错了，**这是因为我们的对象在进行字面量推理的时候，info其实是一个 {url: string, method: string}，所以我们没办法将一个 string赋值给一个 字面量 类型。**所以需要断言一下

![image-20230604233331215](.\typescript_images\image-20230604233331215.png)

- `as const`可以直接推理成字面量类型，那就能够进行赋值了

![image-20230604233333619](.\typescript_images\image-20230604233333619.png)

### 类型缩小

> 前面有提过，这里再统一总结一下
>
> 类型缩小的目的就是为了继续局限可写入的范围，提高代码的质量。就比如说一个国企，岗位很热门，为了防止各种牛马都跑过来报名，筛不过来，先限制再985学校范围内，如果还是筛不过来，那就继续加限制。而他的目前是为了招收优秀的人才，这样的类型缩小就有利于花费最小的精力筛选出最合适企业的人才

- **什么是类型缩小呢？**
  - 类型缩小的英文是 Type Narrowing（也有人翻译成类型收窄）
  - 我们可以通过类似于 **typeof padding === "number"** 的判断语句，来**改变TypeScript的执行路径**
  - 在给定的执行路径中，我们可以**缩小比声明时更小的类型**，这个过程称之为 **缩小（ Narrowing ）**
  - 而我们编写的 **typeof padding === "number** 可以称之为 **类型保护（type guards）**
- **常见的类型保护有如下几种：**
  - typeof
  - 平等缩小（比如===、!==）
  - instanceof
  - in
  - 等等...

#### typeof

- **在 TypeScript 中，检查返回的值typeof是一种类型保护：**
  -  因为 TypeScript 对如何typeof操作不同的值进行编码
  - 使用率最高

![image-20230604235654169](.\typescript_images\image-20230604235654169.png)

#### 平等缩小

- **我们可以使用Switch或者相等的一些运算符来表达相等性（比如===, !==, ==, and != ）：**

![image-20230605000417014](.\typescript_images\image-20230605000417014.png)

#### instanceof

- **JavaScript 有一个运算符来检查一个值是否是另一个值的“实例”：**
  - 下方就是判断传进来的内容是否是Date的实例(`const date = new Date()`)

![image-20230605000438959](.\typescript_images\image-20230605000438959.png)

#### in操作符

- **Javascript 有一个运算符，用于确定对象是否具有带名称的属性：in运算符**
  - 如果指定的属性在指定的对象或其原型链中，则**in 运算符**返回true

![image-20230605000614094](.\typescript_images\image-20230605000614094.png)

### TypeScript函数类型

- **在JavaScript开发中，函数是重要的组成部分，并且函数可以作为一等公民（可以作为参数，也可以作为返回值进行传递）**
- **那么在使用函数的过程中，函数是否也可以有自己的类型呢？**
  - 使用声明
- **我们可以编写`函数类型的表达式（Function Type Expressions），来表示函数类型`**
- 这样做的一个优势是，如果有多个需要接受或返回特定类型的函数，可以为它们定义一个类型，然后在我们的代码中重复使用这个类型。这可以帮助我们避免在代码中重复相同的类型注解，使得代码更加干净和简洁

![image-20230605003548853](.\typescript_images\image-20230605003548853.png)

![image-20230605004041844](.\typescript_images\image-20230605004041844.png)

![image-20230605003555356](.\typescript_images\image-20230605003555356.png)

### TypeScript函数类型解析

- **在上面的语法中 (num1: number, num2: number) => void，代表的就是一个函数类型：**
  - 接收两个参数的函数：num1和num2，并且都是number类型
  - 并且这个函数是没有返回值的，所以是void
- 注意：**在某些语言中，可能参数名称num1和num2是可以省略，但是TypeScript是不可以的：**

![image-20230605004235445](.\typescript_images\image-20230605004235445.png)

#### 函数类型参数格式

- TS对于传入的函数类型的参数个数通常是不进行检测(校验)，这是因为其实很多时候我们使用的时候都是用不上那么多参数的，所以通常只写上一两个参数，最后面的参数会直接不写。如果要校验的话，我们就算不去使用也得写上这个参数了，这就变成累赘了
- TS对很多类型的检测报不报错，取决于它的内部规则

> 在 TypeScript 中，类型检查的行为受到一些编译器配置选项的影响。这些选项可以在 `tsconfig.json` 文件中设置。以下是一些常见的设置：
>
> - `strict`: 这是一组开启所有严格类型检查选项的标志。当设置为 `true`，TypeScript 将执行最严格的类型检查，包括对 `null` 和 `undefined` 的检查，检查未使用的参数等。
> - `noImplicitAny`: 当设置为 `true`，如果函数参数的类型不能被推断出来，TypeScript 将会默认为 `any` 类型，并给出一个警告。
> - `strictNullChecks`: 当设置为 `true`，TypeScript 将不允许 `null` 和 `undefined` 赋值给非 `any` 和它们各自的类型。
> - `strictFunctionTypes`: 当设置为 `true`，TypeScript 将对函数参数的类型进行更加严格的检查。
> - `strictPropertyInitialization`: 当设置为 `true`，TypeScript 将检查类的非 `undefined` 属性是否在构造函数中初始化。
>
> 这些设置都会影响 TypeScript 的类型检查行为。例如，如果你在没有初始化的情况下使用了一个变量，TypeScript 默认不会报错，但如果你启用了 `strict` 选项，就会报错。
>
> 这就是为什么说 "TS对很多类型的检测报不报错，取决于它的内部规则"。这些“规则”其实就是你在 TypeScript 配置文件中设置的编译器选项。

#### TS中函数参数个数官网说明

在 TypeScript 中，官方文档对函数参数个数的定义有着明确的说明。函数的参数个数由其在定义时指定的参数列表决定。这些参数在函数类型声明中以逗号分隔的形式出现，每个参数都由一个名字和一个类型组成。

例如，对于一个接受两个数字类型参数的函数，其类型可以这样声明：

```ts
let myFunction: (a: number, b: number) => void;
```

在这个例子中，函数类型声明 `(a: number, b: number) => void` 表示 `myFunction` 是一个接受两个参数的函数，这两个参数都是 `number` 类型，函数没有返回值（`void`）。

注意，TypeScript 会进行参数数量的检查。在调用函数时，提供的参数数量必须与函数类型声明中的参数数量一致，否则 TypeScript 会报错。如果想要定义一个可接受任意数量参数的函数，可以使用剩余参数（...args）的形式：

```ts
let myFunction: (a: number, ...args: number[]) => void;
```

在这个例子中，`myFunction` 是一个函数，它接受至少一个 `number` 类型的参数，后面可以跟任意数量的 `number` 类型参数。

> 官网对参数多余的不要使用?:可选类型的具体讲解

TypeScript 中的函数参数可选性（表示为 `?`）是一种方便的语法，用于表示函数参数可能不存在或未提供。然而，TypeScript 官方文档建议避免在函数参数多余的情况下使用可选参数。这是因为当函数接受多个参数时，可选参数可能会导致调用函数时的混淆和错误。

```ts
function example(arg1: number, arg2?: string) {
  // ...
}
```

在这个例子中，`arg2` 是一个可选参数。这意味着你可以在调用 `example` 函数时省略第二个参数：

```ts
example(123);  // 这是合法的
```

然而，如果 `example` 函数接受更多的参数，那么可选参数可能会导致混淆：

```ts
function example(arg1: number, arg2?: string, arg3: boolean) {
  // ...
}
```

在这个例子中，如果你想调用 `example` 函数并提供 `arg1` 和 `arg3`，但省略 `arg2`，你将无法这样做，因为 TypeScript 会将你提供的第二个参数解析为 `arg2`，而不是 `arg3`：

```ts
example(123, true);  // TypeScript 将解析为 arg1=123, arg2="true", arg3=undefined
```

因此，为了避免这种可能的混淆和错误，TypeScript 建议在函数有多个参数时，尽量不要使用可选参数。如果你确实需要一个参数可以省略，那么可以使用默认参数或者重载等方式来解决这个问题。

### 调用签名（Call Signatures）

- **在 JavaScript 中，函数除了可以被调用，自己也是可以有属性值的**

  - 然而前面讲到的函数类型表达式**并不能支持声明属性**
  - 如果我们想描述一个带有属性的函数，我们可以在一个对象类型中写一个**调用签名（call signature）**，这样才能够进行调用，正如同下方的calc函数，如果在给形参定义接口的时候不加上函数的调用的话，直接调用是会报错的

  ```ts
  //这个例子中，GreetFunction 类型表示一个函数，这个函数接受两个 string 类型的参数，并且没有返回值（返回 void）。
  type GreetFunction = (a: string, b: string) => void;
  ```

  可以使用这个类型来定义一个函数，例如：

  ```ts
  let greet: GreetFunction;
  greet = function(a: string, b: string) {
      console.log(a, b);
  };
  ```

  在这个例子中，我们定义了一个名为 `greet` 的变量，并指定其类型为 `GreetFunction`。然后，我们可以把一个匹配这个调用签名的函数赋值给 `greet`。

  使用调用签名可以帮助我们确保函数或方法的调用是正确的。如果我们尝试将一个不符合调用签名的函数赋值给 `greet`，例如，一个接受三个参数的函数，TypeScript 将会给出一个错误。

  ```ts
  greet = function(a: string, b: string, c: string) {
      console.log(a, b, c);
  };
  // Error: Type '(a: string, b: string, c: string) => void' is not assignable to type 'GreetFunction'.
  ```

  这就是 TypeScript 中的调用签名。它是 TypeScript 类型系统的一个重要组成部分，帮助我们在编写更安全、更易于理解的代码。

![image-20230605012801598](.\typescript_images\image-20230605012801598.png)

- **注意这个语法跟函数类型表达式稍有不同，在参数列表和返回的类型之间用的是 : 而不是 =>**
- 开发中如何选择：
  - 如果只是描述函数类型本身(函数本身可以调用)，使用函数类型表达式
  - 如果在描述函数作为对象可以被调用，同时也有其他函数时，使用函数调用签名

![image-20230605013619727](.\typescript_images\image-20230605013619727.png)

### 构造签名 （Construct Signatures）

> 构造签名（Construct Signatures）是一种特殊的函数签名，它定义了一个类型可以被 "new" 操作符调用并产生一个对象实例的能力
>
> 对于类来说，其构造函数就是它的构造签名。对于接口来说，它也可以具有构造签名，表示任何实现了该接口的类都必须有一个与接口定义的构造签名相匹配的构造函数。
>
> 构造签名的语法如下：
>
> ```ts
> interface SomeInterface {
>   new (arg1: type1, arg2: type2, ...): ReturnType;
> }
> ```
>
> 在这里，关键字 `new` 表示这是一个构造签名，后面跟着一对括号，包含零个或多个参数的类型，然后是返回类型 `ReturnType`。
>
> 这就表示，任何实现 `SomeInterface` 的类都必须有一个构造函数，它接受相应类型的参数，并返回 `ReturnType` 类型的实例。
>
> 例如：
>
> ```ts
> interface Widget {
>   new (width: number, height: number): WidgetInstance;
> }
> 
> class WidgetInstance {
>   constructor(public width: number, public height: number) { }
> }
> 
> class WidgetFactory implements Widget {
>   constructor(public width: number, public height: number) {
>     return new WidgetInstance(width, height);
>   }
> }
> ```
>
> 在这个例子中，`Widget` 接口定义了一个构造签名，它接受两个 `number` 参数并返回一个 `WidgetInstance`。`WidgetFactory` 类实现了 `Widget` 接口，它的构造函数符合 `Widget` 的构造签名，并返回一个 `WidgetInstance` 实例。

- **JavaScript 函数也可以使用 new 操作符调用，当被调用的时候，TypeScript 会认为这是一个构造函数(constructors)，因为他们会产生一个新对象**
  - 你可以写一个`构造签名（ Construct Signatures` ），方法是在调用签名前面加一个 new 关键词
  - 在ts中使用class之后，除了创建出来一个类之外，还会创建出来一个构造函数

![image-20230605134016687](.\typescript_images\image-20230605134016687.png)

![image-20230605134558431](.\typescript_images\image-20230605134558431.png)

### 参数的可选类型

- **我们可以指定某个参数是可选的：**
  - 形参y中的`?:`就是可选类型

![image-20230605140801894](.\typescript_images\image-20230605140801894.png)

- **这个时候这个参数y依然是有类型的，它是什么类型呢？ number | undefined**

![image-20230605140821566](.\typescript_images\image-20230605140821566.png)

- **另外可选类型需要在必传参数的后面：**

![image-20230605140830187](.\typescript_images\image-20230605140830187.png)

### 默认参数

- **从ES6开始，JavaScript是支持默认参数的，TypeScript也是支持默认参数的：**

![image-20230605140908870](.\typescript_images\image-20230605140908870.png)

- **这个时候y的类型其实是 undefined 和 number 类型的联合**，有默认值的情况，参数的类型注解可以省略掉

![image-20230605220051548](.\typescript_images\image-20230605220051548.png)

### 剩余参数

- **从ES6开始，JavaScript也支持剩余参数，剩余参数语法允许我们将一个不定数量的参数放到一个数组中**

![image-20230605140936689](.\typescript_images\image-20230605140936689.png)

### 函数的重载（了解）

> 在 TypeScript 中，我们可以为函数提供多个函数类型定义来实现多种功能，这被称为函数的重载。通过为同一个函数提供多个函数类型定义，TypeScript 可以正确地进行类型检查，同时也能保证函数调用的正确性。
>
> 一般来说，我们在 TypeScript 中实现函数重载的步骤如下：
>
> 1. 首先，我们需要定义所有可能的重载签名。
> 2. 接着，我们提供一个实现签名。这个签名必须是前面所有重载签名的合集。换句话说，实现函数必须能够处理所有重载签名的调用。但是，实现签名本身在编译结果中会被擦除，所以它不能被直接调用。
> 3. 最后，我们需要在实现函数中，通过类型守卫等方式，处理不同的参数情况，以实现各个重载的功能。

- **在TypeScript中，如果我们编写了一个add函数，希望可以对字符串和数字类型进行相加，应该如何编写呢？**
- **我们可能会这样来编写，但是其实是错误的：**

![image-20230605143038758](.\typescript_images\image-20230605143038758.png)

- **那么这个代码应该如何去编写呢？**

  - 在TypeScript中，我们可以去**编写不同的重载签名（overload signatures）**来表示函数可以**以不同的方式进行调用**
  - 一般是**编写两个或者以上的重载签名**，再去**编写一个通用的函数以及实现**

  ```ts
  // 定义重载签名
  function reverse(x: number): number;
  function reverse(x: string): string;
  
  // 实现函数
  function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
      return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
      return x.split('').reverse().join('');
    }
  }
  
  // 使用重载函数
  let num = reverse(12345); // 结果是 54321，类型是 number
  let str = reverse('hello'); // 结果是 'olleh'，类型是 string
  
  //在上述例子中，我们为 reverse 函数定义了两个重载：当参数是 number 类型时，返回值也是 number 类型；当参数是 string 类型时，返回值也是 string 类型。然后在 reverse 函数的实现中，我们通过检查参数 x 的类型，实现了两种不同的功能。
  ```

#### sum函数的重载

- **比如我们对sum函数进行重构：**

  - 在我们调用sum的时候，它会**根据我们传入的参数类型来决定执行函数体**时，到底**执行哪一个函数的重载签名**

  ![image-20230605145349612](.\typescript_images\image-20230605145349612.png)

- **但是注意，有实现体的函数，是不能直接被调用的：**

![image-20230605145401895](.\typescript_images\image-20230605145401895.png)

#### 联合类型和重载

- **我们现在有一个需求：定义一个函数，可以传入字符串或者数组，获取它们的长度**
- **这里有两种实现方案：**
  - 方案一：使用联合类型来实现，**建议优先使用这种**
  - 方案二：实现函数重载来实现

![image-20230605145447998](.\typescript_images\image-20230605145447998.png)

![image-20230605145450544](.\typescript_images\image-20230605145450544.png)

- **在开发中我们选择使用哪一种呢？**
  - 在可能的情况下，尽量选择使用联合类型来实现
- **对象类型实现**

```ts
// 这个函数接受一个具有 `length` 属性的对象作为参数
// 这个 `length` 属性的类型必须是 `number`
function getLength(arg: {length: number}) {
  // 函数返回这个 `length` 属性的值
  return arg.length;
}

// 我们在这里调用 `getLength` 函数，并且传入一个具有 `length` 属性的对象，其值为 1.75
getLength({length: 1.75});
```

优势：

1. **类型安全**：TypeScript 在编译时期就会检查 `getLength` 函数的参数是否符合期望的类型（即，是否具有 `length` 属性，且该属性的值的类型为 `number`），从而提早发现可能的错误。
2. **代码清晰易懂**：通过类型注解，其他阅读这段代码的人可以很明确地知道 `getLength` 函数期望的参数的类型，提高了代码的可读性。

劣势：

1. **灵活性降低**：`getLength` 函数只能接收具有 `length` 属性的对象，如果我们希望它能处理其他类型的参数，可能需要更改函数的实现或者新增函数。
2. **类型过于具体**：如果我们希望 `getLength` 函数能接收的参数的 `length` 属性除了 `number` 类型以外，还可以是其他类型，那么我们可能需要进行类型的修改，这将增加维护的复杂性。

```ts
// 这是一个重载函数的例子，使用了对象类型的参数来实现。
function displayInformation(info: { name: string; age: number }): string;
function displayInformation(info: { brand: string; price: number }): string;

function displayInformation(info: any): string {
  // 当使用对象类型作为参数时，我们可以使用类型保护（即使用 typeof 或 instanceof 检查）来区分不同的对象类型。
  // 这样，我们可以安全地访问这些对象的属性，并且 TypeScript 会正确地进行类型检查。
  if ("name" in info && "age" in info) {
    // 在这个分支中，TypeScript 知道 info 是 { name: string; age: number } 类型的对象。
    return `Name: ${info.name}, Age: ${info.age}`;
  } else if ("brand" in info && "price" in info) {
    // 在这个分支中，TypeScript 知道 info 是 { brand: string; price: number } 类型的对象。
    return `Brand: ${info.brand}, Price: ${info.price}`;
  }
  return '';
}

console.log(displayInformation({ name: 'John', age: 30 }));  // Output: Name: John, Age: 30
console.log(displayInformation({ brand: 'Apple', price: 999 }));  // Output: Brand: Apple, Price: 999
```

优点：

1. 提高代码的灵活性和复用性。使用对象类型作为参数，可以方便地添加更多的属性，而无需修改函数的签名。
2. 代码更具有可读性和可维护性。与联合类型相比，对象类型的参数可以更清晰地表达参数的结构和意义。

劣势：

1. 需要更多的类型保护。我们需要用 "in" 操作符来检查对象的属性，以确保我们访问的属性是存在的。
2. 函数的调用者需要知道对象的结构。与使用单个参数的函数相比，使用对象类型的参数的函数需要调用者了解参数对象的结构。

### 可推导的this类型

- **this是JavaScript中一个比较难以理解和把握的知识点：**
  - JS高级的笔记中有写
  - [Latest-front-end-Notes/JavaScript高级笔记-coderwhy版本.md at main · 2002XiaoYu/Latest-front-end-Notes · GitHub](https://github.com/2002XiaoYu/Latest-front-end-Notes/blob/main/coderwhy体系课/coderwhy体系课markdown/JavaScript高级笔记/JavaScript高级笔记-coderwhy版本.md)
- **当然在目前的Vue3和React开发中不一定会使用到this：**
  - Vue3的Composition API中很少见到this，React的Hooks开发中也很少见到this了
- **但是我们还是简单掌握一些TypeScript中的this，TypeScript是如何处理this呢？我们先来看两个例子：**

![image-20230605151941543](.\typescript_images\image-20230605151941543.png)![image-20230605151944737](.\typescript_images\image-20230605151944737.png)

- **上面的代码默认情况下是可以正常运行的，也就是TypeScript在编译时，认为我们的this是可以正确去使用的：**
  - 这是因为在没有指定this的情况，this默认情况下是any类型的

### this的编译选项

- **VSCode在检测我们的TypeScript代码时，默认情况下运行不确定的this按照any类型去使用**

  - 但是我们可以创建一个tsconfig.json文件，并且在其中告知VSCodethis必须明确执行（不能是隐式的）

  ![image-20230605152137478](.\typescript_images\image-20230605152137478.png)

- **在设置了noImplicitThis为true时， TypeScript会根据上下文推导this，但是在不能正确推导时，就会报错，需要我们明确的指定this**

![image-20230605152150867](.\typescript_images\image-20230605152150867.png)

### 指定this的类型

- **在开启noImplicitThis的情况下，我们必须指定this的类型**

- **如何指定呢？函数的第一个参数类型：**

  - 函数的第一个参数我们可以根据该函数之后被调用的情况，用于声明this的类型（名词必须叫this）
  - 在后续调用函数传入参数时，从第二个参数开始传递的，this参数会在编译后被抹除

  ![image-20230605152226891](.\typescript_images\image-20230605152226891.png)

### this相关的内置工具

- **Typescript 提供了一些工具类型来辅助进行常见的类型转换，这些类型全局可用**

- **ThisParameterType：**

  - 用于提取一个函数类型Type的this (opens new window)参数类型
  - `ThisParameterType<T>`：这个工具类型接收一个类型参数`T`，如果`T`是一个函数类型并且有`this`参数，那么`ThisParameterType<T>`就是该`this`参数的类型。如果`T`没有`this`参数，那么`ThisParameterType<T>`将是`unknown`。这个工具类型可以帮助我们提取出函数类型中`this`参数的类型。
  - 如果这个函数类型没有this参数返回unknown

  ![image-20230605153846820](.\typescript_images\image-20230605153846820.png)

  ```ts
  function func(this: string, arg1: number) { /* ... */ }
  
  type T1 = ThisParameterType<typeof func>; // T1是string类型
  ```

- **OmitThisParameter：**

  - 用于移除一个函数类型Type的this参数类型, 并且返回当前的函数类型
  - `OmitThisParameter<T>`：这个工具类型接收一个类型参数`T`，如果`T`是一个函数类型并且有`this`参数，那么`OmitThisParameter<T>`就是去掉`this`参数后的函数类型。如果`T`没有`this`参数，那么`OmitThisParameter<T>`将是`T`。这个工具类型可以帮助我们去掉函数类型中`this`参数。

  ![image-20230605153859218](.\typescript_images\image-20230605153859218.png)

  ```ts
  function func(this: string, arg1: number) { /* ... */ }
  
  type T2 = OmitThisParameter<typeof func>; // T2是一个接收number类型参数的函数类型，没有this参数
  ```

  使用`ThisParameterType`和`OmitThisParameter`可以帮助我们更灵活地处理`this`参数。例如，我们可以利用这两个工具类型，编写一个函数，该函数接收一个对象和一个方法，然后在指定的对象上调用该方法，同时确保类型安全。

### this相关的内置工具 - ThisType

- **这个类型不返回一个转换过的类型，它被用作标记一个上下文的this类型。（官方文档）**

  - 事实上官方文档的不管是解释，还是案例都没有说明出来ThisType类型的作用

  - 在 TypeScript 中，`ThisType<T>` 是一个特殊的内置工具类型，它不会在运行时有任何的影响，但在编译时，TypeScript 编译器会使用 `T` 类型来推断 `this` 的类型。

    `ThisType<T>` 的主要用途是在 mixin 类型或者其他类似于对象字面量的类型声明上下文中，表示方法内部的 `this` 类型。

```ts
// ObjectDescriptor 是一个泛型类型，接受两个参数 D 和 M
// D 代表数据的类型，M 代表方法的类型
type ObjectDescriptor<D, M> = {
    // data 是一个可选的属性，其类型为 D
    data?: D;
    // methods 也是一个可选的属性，其类型为 M & ThisType<D & M>
    // 这里的 ThisType<D & M> 表示在 methods 对象中的方法，其内部的 `this` 指向的类型应该是 D & M
    methods?: M & ThisType<D & M>;
}

// makeObject 函数接受一个 ObjectDescriptor 类型的参数，返回一个同时具有 D 类型和 M 类型的对象
function makeObject<D, M>(desc: ObjectDescriptor<D, M>): D & M {
    let data: object = desc.data || {};
    let methods: object = desc.methods || {};
    // 使用展开操作符合并 data 和 methods 对象，并通过类型断言指定返回对象的类型为 D & M
    return { ...data, ...methods } as D & M;
}

// 使用 makeObject 函数创建一个对象
// 这个对象的 data 属性包含 x 和 y，methods 属性包含一个 moveBy 方法
let obj = makeObject({
    data: { x: 0, y: 0 },
    methods: {
        // 在 moveBy 方法内部，我们可以直接访问 this.x 和 this.y
        // 因为在 ObjectDescriptor 类型中，我们已经指定了 methods 的 `this` 类型为 D & M
        // 所以这里的 `this` 就是 { x: number; y: number; moveBy: (dx: number, dy: number) => void; }
        moveBy(dx: number, dy: number) {
            this.x += dx;  // Strongly typed this
            this.y += dy;  // Strongly typed this
        }
    }
});

// 现在我们可以安全地访问 obj 的 x、y 属性和 moveBy 方法
// TypeScript 编译器会对这些访问进行类型检查，确保它们的类型正确
obj.x = 10;
obj.y = 20;
obj.moveBy(5, 5);
```

### TS学习等级划分

1. 知道TS，但是没有使用过
2. AnyScript，万物皆可any，麒麟哥水平
3. 大多数情况使用any，但是普通的很多类型写法也是能够写出来的(例如联合类型，JS中也有的类型)
4. 大多数类型都是使用正确的，包括TS独有的类型，例如void，never，tuple。极少数使用any
5. 可以使用TS封装一些高级的类型，包括框架中某些特殊的类型(函数重载等特殊用法)，学会使用TS提供的各种内置工具，会做类型体操
6. TS融会贯通，看TS的源码。通常只有TS的开发者达到

- 达到第四层次完全足够了

## TypeScript面向对象

### 认识类的使用

- **在早期的JavaScript开发中（ES5）我们需要通过函数和原型链来实现类和继承，从ES6开始，引入了class关键字，可以更加方便的定义和使用类**
- **TypeScript作为JavaScript的超集，也是支持使用class关键字的，并且还可以对类的属性和方法等进行静态类型检测**
- **实际上在JavaScript的开发过程中，我们更加习惯于函数式编程：**
  - 比如React开发中，目前更多使用的函数组件以及结合Hook的开发模式
  - 比如在Vue3开发中，目前也更加推崇使用 Composition API
- **但是在封装某些业务的时候，类具有更强大封装性，所以我们也需要掌握它们**
- **类的定义我们通常会使用class关键字：**
  - 在面向对象的世界里，任何事物都可以使用类的结构来描述
  - 类中包含特有的**属性和方法**

### 类的定义

- **我们来定义一个Person类：**

  - 使用class关键字来定义一个类

- **我们可以声明类的属性：在类的内部声明类的属性以及对应的类型**

  - 如果类型没有声明，那么它们默认是any的

  - 我们也可以给属性设置初始化值

  - 在默认的strictPropertyInitialization模式下面我们的属性是必须初始化的，如果没有初始化，那么编译时就会报错

    ✓ 如果我们在strictPropertyInitialization模式下确实不希望给属性初始化，可以使用 name!: string语法

  - **类可以有自己的构造函数constructor，当我们通过new关键字创建一个实例时，构造函数会被调用**

    - 构造函数**不需要返回任何值**，默认**返回当前创建出来的实例**

  - **类中可以有自己的`函数`，定义的函数称之为`方法`**

```ts
// 定义一个名为Person的类
class Person {
    // 类的属性声明，TypeScript需要声明每个类的属性的类型
    private name: string; // name属性是私有的，只能在Person类内部访问
    public age: number; // age属性是公有的，可以在任何地方访问
    protected address: string; // address属性是受保护的，只能在Person类及其派生类中访问

    // 类的构造函数，TypeScript中的构造函数只能有一个，而JavaScript中可以有多个
    constructor(name: string, age: number, address: string) {
        this.name = name;
        this.age = age;
        this.address = address;
    }

    // 类的方法，TypeScript需要声明方法的返回类型
    public greet(): string {
        return `Hello, my name is ${this.name} and I'm ${this.age} years old.`;
    }

    // 静态方法，可以直接通过类名调用，而不需要创建类的实例
    static sayHello(): string {
        return 'Hello!';
    }
}

// 创建Person类的实例
let person = new Person('John Doe', 30, 'New York');

// 访问公有属性和方法
console.log(person.age); // 30
console.log(person.greet()); // Hello, my name is John Doe and I'm 30 years old.

// 访问静态方法
console.log(Person.sayHello()); // Hello!

// 访问私有属性和受保护属性会报错
// console.log(person.name); // Error: Property 'name' is private and only accessible within class 'Person'.
// console.log(person.address); // Error: Property 'address' is protected and only accessible within class 'Person' and its subclasses.
```

#### 类的继承

- **面向对象的其中一大特性就是继承，继承不仅仅可以减少我们的代码量，也是多态的使用前提**
- **我们使用`extends关键字`来实现继承，子类中使用`super`来访问父类**，和JS中的继承没有区别
- **我们来看一下Student类继承自Person：**
  - Student类可以有自己的属性和方法，并且会继承Person的属性和方法
  - 在构造函数中，我们可以通过super来调用父类的构造方法，对父类中的属性进行初始化

![image-20230606024152031](.\typescript_images\image-20230606024152031.png)

![image-20230606024155461](.\typescript_images\image-20230606024155461.png)

#### 类的成员修饰符

- **在TypeScript中，类的属性和方法支持三种修饰符： public、private、protected**
  - `public` 修饰的是在任何地方可见、公有的属性或方法，默认编写的属性就是public的
  - `private` 修饰的是仅在同一类中可见、私有的属性或方法
  - `protected` 修饰的是仅在类自身及子类中可见、受保护的属性或方法
- **public是默认的修饰符，也是可以直接访问的，我们这里来演示一下protected和private**

```ts
// 定义Person类
class Person {
  // 私有属性_name，类型为string，可以在Person类内部访问，外部无法访问
  private _name: string;

  // 私有属性_age，类型为number，可以在Person类内部访问，外部无法访问
  private _age: number;

  // 构造函数，用于初始化类的实例，接收两个参数name和age，类型分别为string和number
  constructor(name: string, age: number) {
    // 使用this关键字访问和修改类的属性
    this._name = name;
    this._age = age;
  }

  // 类的方法running，没有参数，返回值为void，表示该函数没有返回值
  running() {
    // 使用this关键字访问类的属性_name
    console.log("running:", this._name);
  }

  // getter和setter方法，用于拦截对_name属性的访问和修改
  set name(newValue: string) {
    // 在setter方法中，可以对新值进行检查和处理
    this._name = newValue;
  }

  get name() {
    // 在getter方法中，可以对返回值进行处理
    return this._name;
  }

  // getter和setter方法，用于拦截对_age属性的访问和修改
  set age(newValue: number) {
    // 在setter方法中，可以对新值进行检查，如检查年龄是否在合理范围内
    if (newValue >= 0 && newValue < 200) {
      this._age = newValue;
    }
  }

  get age() {
    // 在getter方法中，可以对返回值进行处理
    return this._age;
  }
}

// 创建Person类的实例p，传入初始的_name和_age值
const p = new Person("why", 100);

// 使用setter方法修改_name属性
p.name = "kobe";

// 使用getter方法访问_name属性，打印出修改后的_name值
console.log(p.name);

// 使用setter方法尝试修改_age属性，由于新的_age值不在合理范围内，_age属性不会被修改
p.age = -10;

// 使用getter方法访问_age属性，打印出_age值，可以看到_age值并没有被修改
console.log(p.age);

// 导出空对象，这是为了让TypeScript将此文件视为一个模块，而不是一个脚本。在一个模块中，顶级声明（如变量、函数、类、接口等）默认都是私有的，如果你想在其他地方访问这个模块中的内容，你需要导出它们
export {};
```

### 只读属性readonly

- 如果有一个属性我们不希望外界可以任意的修改，只希望确定值后直接使用，那么可以使用readonly

![image-20230606220406663](.\typescript_images\image-20230606220406663.png)

### getters/setters

- 在前面一些私有属性我们是不能直接访问的，或者某些属性我们想要监听它的获取(getter)和设置(setter)的过程，这个时候我们可以使用存取器

![image-20230606220428889](.\typescript_images\image-20230606220428889.png)![image-20230606220433487](.\typescript_images\image-20230606220433487.png)

### 参数属性（Parameter Properties）

- TypeScript 提供了特殊的语法，可以把一个构造函数参数转成一个同名同值的类属性
  - 这些就被称为参数属性（parameter properties）
  - 你可以通过在构造函数参数前**添加一个可见性修饰符 public private protected 或者 readonly 来创建参数属性，最后这些类属性字段也会得到这些修饰符**

![image-20230607070721349](.\typescript_images\image-20230607070721349.png)

> 正常情况下，在TS中我们如果在类中没有声明直接使用的话，是无法获取到的

![image-20230607071416313](.\typescript_images\image-20230607071416313.png)

```ts
class Person{
  //正常写法，需要先声明，然后才能在构造函数中使用
  name:string
  age:number

  // 我们没有声明height，height属性将获取不到并且报错
  constructor(name:string,age:number,height:number){
    this.name = name
    this.age = age
    this.height = height
  }
}

const XiaoYu = new Person("小余",20,1.75)
console.log(XiaoYu.height);
```

> 将height使用修饰符后能够不需要声明

```ts
class Person{
  //正常写法，需要先声明，然后才能在构造函数中使用
  name:string
  age:number

  // 对height使用语法糖-可见修饰符后可以直接使用
  constructor(name:string,age:number,public height:number){
    this.name = name
    this.age = age
    this.height = height
  }
}

const XiaoYu = new Person("小余",20,1.75)
console.log(XiaoYu.height);//1.75
```

### 抽象类abstract(保留字)

- **我们知道，继承是多态使用的前提。**
  - 所以在定义很多通用的**调用接口时, 我们通常会让调用者传入父类，通过多态来实现更加灵活的调用方式**
  - **但是，父类本身可能并不需要对某些方法进行具体的实现，所以父类中定义的方法,，我们可以定义为抽象方法**
- **什么是 抽象方法? 在TypeScript中没有具体实现的方法(没有方法体)，就是抽象方法**
  - 抽象方法，必须存在于抽象类中
  - 抽象类是使用abstract声明的类
- **抽象类有如下的特点：**
  - 抽象类是不能被实例的话（也就是不能通过new创建）
  - 抽象类可以包含抽象方法，也可以包含有实现体的方法
  - 有抽象方法的类，必须是一个抽象类
  - 抽象方法必须被子类实现，否则该类必须是一个抽象类

#### 抽象类演练

```ts
abstract class Shape {
  // getArea方法只有声明没有实现体
  // 实现让子类自己实现
  // 可以将getArea方法定义为抽象方法: 在方法的前面加abstract
  // 抽象方法必须出现在抽象类中, 类前面也需要加abstract
  abstract getArea()//在方法前面加上abstract就变成抽象方法，不需要加上后面的{}实现体
}


class Rectangle extends Shape {
  constructor(public width: number, public height: number) {
    super()
  }

  getArea() {
    return this.width * this.height
  }
}

class Circle extends Shape {
  constructor(public radius: number) {
    super()
  }

  getArea() {
    return this.radius ** 2 * Math.PI
  }
}

class Triangle extends Shape {
  getArea() {
    return 100
  }
}


// 通用的函数
function calcArea(shape: Shape) {
  return shape.getArea()
}
//同等于 const shape1:Shape = new Rectangle(10,20)父类引入指向子类对象
calcArea(new Rectangle(10, 20))
calcArea(new Circle(5))
calcArea(new Triangle())

// 在Java中会报错: 不允许
calcArea({ getArea: function() {} })

// 抽象类不能被实例化
// calcArea(new Shape())
// calcArea(100)
// calcArea("abc")
```

### TS类型检测-鸭子类型

> "鸭子类型"（Duck typing）是一种编程概念，其来源于英语表达 "If it walks like a duck and it quacks like a duck, then it must be a duck."。这个表达的意思是说，如果一个物体像鸭子一样走路、像鸭子一样叫，那么我们就可以认为这个物体是鸭子。
>
> 在 TypeScript 中，类型检查器采用 "鸭子类型" 或 "结构性子类型化" 这种方式进行类型检查。在 TypeScript 中，接口的作用就在于为这些类型命名和为你的代码或第三方代码定义契约。

```ts
interface Duck {
  walk: () => void;
  quack: () => void;
}

function doDuckStuff(duck: Duck) {
  duck.walk();
  duck.quack();
}

const someObject = {
  walk: () => console.log("Walking like a duck"),
  quack: () => console.log("Quacking like a duck"),
  fly: () => console.log("Flying like a bird, not a duck"),
};

doDuckStuff(someObject); // 可以正常运行，因为 someObject 拥有 Duck 接口所规定的 walk 和 quack 方法
```

在上面的示例中，尽管 `someObject` 没有明确地声明它实现了 `Duck` 接口，但它具有 `Duck` 接口所规定的 `walk` 和 `quack` 方法，因此我们可以将 `someObject` 当作 `Duck` 使用。这就是 "鸭子类型" 或 "结构性子类型化"。

```ts
// TypeScript对于类型检测的时候使用的鸭子类型
// 鸭子类型: 如果一只鸟, 走起来像鸭子, 游起来像鸭子, 看起来像鸭子, 那么你可以认为它就是一只鸭子
// 鸭子类型, 只关心属性和行为, 不关心你具体是不是对应的类型

class Person {
  constructor(public name: string, public age: number) {}

  running() {}
}

class Dog {
  constructor(public name: string, public age: number) {}
  running() {}
}

function printPerson(p: Person) {
  console.log(p.name, p.age)
}

printPerson(new Person("why", 18))
// printPerson("abc")
printPerson({name: "kobe", age: 30, running: function() {}})
printPerson(new Dog("旺财", 3))//我们明明限制使用printPerson的形参p传入的必须满足Person，但是我们传入一只Dog都没有报错，这是因为属性和行为是跟Person一样的，产生了鸭子类型的效果

const person: Person = new Dog("果汁", 5)


export {}
```

鸭子类型的优势在于它提供了更大的灵活性，可以更容易地实现松耦合的代码。然而，这种方式也可能会带来一些问题，例如可能会更容易出现类型相关的运行时错误。因此，在使用鸭子类型时，需要确保你的代码始终能够正确地处理不同的类型。

### 对象类型的属性修饰符（Property Modifiers）

- **对象类型中的每个属性可以说明`它的类型、属性是否可选、属性是否只读`等信息**
- **可选属性（Optional Properties）**
  - 我们可以在**属性名后面加一个 ? 标记**表示这个属性是可选的
- **只读属性（Readonly Properties）**
  - 在 TypeScript 中，属性`可以被标记为 readonly`，这不会改变任何运行时的行为
  - 但在类型检查的时候，一个标记为 readonly的属性是不能被写入的

![image-20230607081306898](.\typescript_images\image-20230607081306898.png)

### 索引签名（Index Signatures）

- **什么是索引签名呢？**
  - 有的时候，你不能提前知道一个类型里的所有属性的名字，但是**你知道这些值的特征**
  - 这种情况，你就**可以用一个索引签名 (index signature) 来描述可能的值的类型**

```ts
// 声明一个接口 ICollection，该接口描述了一个具有特定成员的对象类型
interface ICollection {
  // 索引签名，定义了该接口的索引类型和索引值类型
  // 这里表示该接口的实例对象可以有任意数量的属性，只要它们的属性名是字符串类型，属性值必须是数值类型
  [index: string]: number

  // 这是一个普通的接口属性，表示实例对象需要有一个名为length的数值型属性
  length: number
}

// 创建一个数值数组
const names: number[] = [111, 222, 333]
// 使用数组索引访问数组元素
console.log(names[0]) // 输出：111
console.log(names[1]) // 输出：222
console.log(names[2]) // 输出：333

// 定义一个函数，该函数接受一个ICollection类型的参数
// 这个函数会访问并输出collection参数的第0个和第1个索引的值
function iteratorCollection(collection: ICollection) {
  console.log(collection[0]) // 输出：collection的第0个索引的值
  console.log(collection[1]) // 输出：collection的第1个索引的值
}

// 将一个对象字面量作为参数传给iteratorCollection函数
// 这个对象字面量满足ICollection接口的要求，因为它有一个名为length的数值型属性，同时所有其他属性名都是字符串类型，属性值都是数值类型
iteratorCollection({ name: 111, age: 18, length: 10 }) // 输出：undefined undefined
// 注意，虽然name和age属性的值是数值类型，但是由于它们的属性名并不是数值类型，所以在函数iteratorCollection中用0和1作为索引去访问它们的值时，返回的是undefined

export {}
```

- **一个索引签名的属性类型必须是 string 或者是 number**
  - 虽然 TypeScript 可以同时支持 string 和 number 类型，但数字索引的返回类型一定要是字符索引返回类型的子类型（了解）

### 接口继承

- **接口和类一样是可以进行继承的，也是使用extends关键字：**

  - 并且我们会发现，接口是支持多继承的（类不支持多继承）

  > 在这个示例中，`Bird`接口继承了`Animal`接口，因此`Bird`接口包含了`Animal`接口的所有成员，同时也可以定义自己的成员。`Sparrow`类实现了`Bird`接口，这表示`Sparrow`类的实例将具有`Bird`接口的所有成员，也就是说`Sparrow`类的实例将具有`name`属性，`move`方法和`fly`方法。

```ts
// 声明一个Animal接口，它定义了动物共享的属性和方法
interface Animal {
    // 动物的名字，字符串类型
    name: string;

    // 动物的移动方法，接受一个距离参数（数值类型），没有返回值
    move(distance: number): void;
}

// 声明一个Bird接口，它继承自Animal接口
// 这表示Bird接口不仅具有Animal接口定义的所有成员，还可以添加自己的成员
interface Bird extends Animal {
    // 鸟类的飞行方法，接受一个高度参数（数值类型），没有返回值
    fly(height: number): void;
}

// 实现Bird接口的类
class Sparrow implements Bird {
    // 实现Bird接口的所有属性和方法
    name: string;

    // Sparrow类的构造器，接受一个name参数用于初始化Sparrow实例的name属性
    constructor(name: string) {
        this.name = name;
    }

    // 实现Animal接口的move方法
    move(distance: number) {
        console.log(`${this.name} moved ${distance} meters`);
    }

    // 实现Bird接口的fly方法
    fly(height: number) {
        console.log(`${this.name} flew ${height} meters high`);
    }
}

// 创建一个Sparrow实例
const sparrow = new Sparrow('Sparrow');
// 调用Sparrow实例的move方法
sparrow.move(10); // 输出：Sparrow moved 10 meters
// 调用Sparrow实例的fly方法
sparrow.fly(5); // 输出：Sparrow flew 5 meters high
```

### 接口的实现

- **接口定义后，也是可以被类实现的：**
  - 如果被一个类实现，那么在之后需要传入接口的地方，都可以将这个类传入
  - 这就是面向接口开发

![image-20230607094807152](.\typescript_images\image-20230607094807152.png)![image-20230607094810442](.\typescript_images\image-20230607094810442.png)

### 抽象类和接口的区别（了解）

- **抽象类在很大程度上和接口会有点类似：都可以在其中定义一个方法，让子类或实现类来实现对应的方法**
- **那么抽象类和接口有什么区别呢？**
  - 抽象类是事物的抽象，抽象类用来捕捉子类的通用特性，接口通常是一些行为的描述
  - 抽象类通常用于一系列关系紧密的类之间，接口只是用来描述一个类应该具有什么行为
  - 接口可以被多层实现，而抽象类只能单一继承
  - 抽象类中可以有实现体，接口中只能有函数的声明
- **通常我们会这样来描述类和抽象类、接口之间的关系：**
  - 抽象类是对事物的抽象，表达的是 is a 的关系。猫是一种动物（动物就可以定义成一个抽象类）
  - 接口是对行为的抽象，表达的是 has a 的关系。猫拥有跑（可以定义一个单独的接口）、爬树（可以定义一个单独的接口）的行为

### 严格的字面量赋值检测

> 在 TypeScript 中，字面量类型是一种特殊的子类型，用于约束取值为某个特定值，或者特定的一些值。与此同时，字面量赋值检测（Literal Assignment Checking）是 TypeScript 提供的一种方式，它帮助开发者确保值或变量的字面量值能被正确地赋值给预期的类型。

- **对于对象的字面量赋值，在TypeScript中有一个非常有意思的现象：**

  - 奇怪现象1：

    我们定义了接口interface，里面有name和age，然后使用接口的时候，多上一个height就会报错，错入如下：**对象字面量只能指定已知属性，并且“height”不在类型“IPerson”中**

    ```ts
    interface IPerson {
      name: string
      age: number
    }
    
    
    // 1.奇怪的现象一: 
    // 定义info, 类型是IPerson类型
    const obj = {
      name: "why",
      age: 18,
    
      // 多了一个height属性
      height: 1.88
    }
    const info: IPerson = obj
    ```

  - 奇怪现象2：

    我们定义一个函数直接使用：如下

    ```ts
    interface IPerson{
      name:string
      age:number
    }
    
    const info = {name:"小余",age:20,height:1.75}
    function printPerson(person:IPerson){
    
    } 
    printPerson(info)//不会报错，info通过了IPerson的检测，但是info明明是多出了一个height属性
    
    printPerson({name:"小余",age:20,height:1.75})//报错，info的height无法通过检测
    ```

#### 为什么会出现这种情况呢？

- **引入TypeScript成员在GitHub的issue中的回答：**

![image-20230607225209362](.\typescript_images\image-20230607225209362-1686149530593-1.png)

- 进行结合回答如下：

> 这种现象是因为 TypeScript 的“鸭式辨型法”（Duck Typing）和“额外属性检查”（Excess Property Checks）导致的。
>
> 在 TypeScript 中，只要一个对象满足接口的所有必需属性，那么它就被认为是符合该接口的，即使这个对象有额外的属性也无所谓。这就是“鸭式辨型法”的思想，"如果它走起路来像鸭子，叫起来也像鸭子，那么它就可能是鸭子"。所以在你的代码中，`info`对象虽然有额外的`height`属性，但它符合了`IPerson`接口的所有必需属性，所以它可以被视为一个`IPerson`。
>
> 然而，当我们直接在函数调用中传入对象字面量时，TypeScript 会进行“额外属性检查”。这时，如果对象字面量有任何不在接口中的额外属性，TypeScript 就会报错。这就是为什么`printPerson({name:"小余",age:20,height:1.75})`会报错的原因。
>
> 这种设计的目的是为了帮助开发者在编写代码时捕获可能的错误。例如，你可能在写代码时，不小心多写了一个属性，或者写错了属性的名字，这时 TypeScript 就会提示你。
>
> 总的来说，这两种行为的设计都是为了提高代码的安全性和可维护性。如果你想在对象字面量中使用额外的属性，你可以先将对象字面量赋值给一个变量，然后再将这个变量传入函数，就像你在`info`的例子中所做的那样。
>
> ### 结合TS的开发者成员的解释
>
> - 每个对象字面量最初都被认为是“`新鲜的（fresh）`”
> - 当一个新的对象字面量分配给一个变量或传递给一个非空目标类型的参数时，对象字面量指定目标类型中不存在的属性是错误的。
> - 当类型断言或对象字面量的类型扩大时，新鲜度会消失
>
> TypeScript 开发者们对“新鲜对象”（fresh object literal）的概念的解释。这个概念跟上面提到的“额外属性检查”（Excess Property Checks）是相互关联的。
>
> 在 TypeScript 中，如果我们直接创建一个对象字面量（例如 `{name: 'Bob', age: 20, height: 1.75}`），那么这个对象字面量在创建时就被视为一个“新鲜对象”。对于“新鲜对象”，TypeScript 会进行更严格的类型检查，包括“额外属性检查”。
>
> 当我们将一个“新鲜对象”赋值给一个变量，或者将它作为参数传递给一个函数时，TypeScript 就会检查这个对象字面量是否符合目标类型。如果对象字面量有任何不在目标类型中的额外属性，TypeScript 就会报错。
>
> 然而，一旦一个“新鲜对象”被赋值给一个变量，或者它的类型被明确地扩大（例如通过类型断言），那么它就不再是“新鲜的”，TypeScript 就不会对它进行“额外属性检查”了。
>
> 所以，你在代码中看到的现象就是这个概念的体现：`info`对象在被创建后就不再是“新鲜的”，所以它可以有额外的`height`属性；而当你直接在函数调用中传入一个对象字面量时，这个对象字面量被视为“新鲜的”，所以它不能有额外的属性。
>
> 这个设计的目的是为了帮助开发者在编写代码时捕获可能的错误，提高代码的安全性和可维护性。

```ts
// 解释现象
// 第一次创建的对象字面量, 称之为fresh(新鲜的)
// 对于新鲜的字面量, 会进行严格的类型检测. 必须完全满足类型的要求(不能有多余的属性)
const obj2 = {
  name: "why",
  age: 18,

  height: 1.88
}

const p: IPerson = obj2

export {}
```

### TypeScript枚举类型

- **枚举类型是为数不多的TypeScript特性有的特性之一：**
  - 枚举其实就是将一组可能出现的值，一个个列举出来，定义在一个类型中，这个类型就是枚举类型
  - 枚举允许开发者定义一组命名常量，常量可以是数字、字符串类型
  - 使用枚举可以清晰表述意图和创建一组有区别的用例。
  - 枚举使用 `enum` 关键字定义，后面跟上枚举的名称。枚举的成员通常使用大写字母开头

```ts
// 定义一个枚举类型
enum Direction {
    NORTH,
    SOUTH,
    EAST,
    WEST,
}

// 使用枚举类型
let dir: Direction;

dir = Direction.NORTH; // 正确
dir = "NORTH"; // 错误，"NORTH" 不是 `Direction` 类型
```

#### 枚举类型的值

在 TypeScript 中，枚举有两种主要类型：数字枚举和字符串枚举。

1. 数字枚举：成员的值是数字，可以是整数或浮点数。如果没有手动指定成员的值，那么 TypeScript 会自动为成员分配从 0 开始的递增的值。

```ts
enum Direction {
    NORTH, // 0
    SOUTH, // 1
    EAST,  // 2
    WEST   // 3
}
```

2. 字符串枚举：成员的值是字符串。

```ts
enum Direction {
    NORTH = "NORTH",
    SOUTH = "SOUTH",
    EAST = "EAST",
    WEST = "WEST"
}
```

注意，枚举类型提供了一个映射，可以从成员的名称获取到成员的值，也可以从成员的值获取到成员的名称。

```ts
// 从成员的名称获取到成员的值
console.log(Direction.NORTH); // 输出 "NORTH"

// 从成员的值获取到成员的名称
console.log(Direction["NORTH"]); // 输出 "NORTH"
```

此外，TypeScript 还支持混合枚举（包含字符串和数字的枚举）、计算枚举成员（成员的值由表达式计算得出）、常量枚举（在编译后会被删除，只会在编译阶段进行检查）等高级用法。

### TS对象类型索引签名

在 TypeScript 中，索引签名是一种特殊的成员，用于描述对象的索引操作符（`[]`）的行为。使用索引签名可以使对象支持任意数量、任意名称的属性。

索引签名的语法是在接口或类型别名中定义一个或多个签名，这些签名的形式是 `[index: type]: type`，其中 `index` 是索引的名称，可以是任何有效的标识符；第一个 `type` 是索引的类型，必须是 `string` 或 `number`；第二个 `type` 是索引操作符返回的值的类型。

以下是一个简单的索引签名的例子：

```ts
interface StringDictionary {
  [index: string]: string;
}

let dict: StringDictionary = {};

dict["hello"] = "world";  // OK
console.log(dict["hello"]);  // 输出 "world"

dict[42] = "test";  // 错误，索引必须是字符串
```

在这个例子中，`StringDictionary` 的索引签名表示了所有的 `string` 索引返回的值都是 `string` 类型。

需要注意的是，当使用 `number` 作为索引类型时，`string` 索引的返回值类型必须是 `number` 索引返回值类型的超集。这是因为在 JavaScript 中，当使用 `number` 类型的值作为对象的索引时，这个值会被自动转换为 `string` 类型。因此，为了保持一致性，`number` 索引的返回值类型必须是 `string` 索引返回值类型的子集。

以下是一个 `number` 索引签名的例子：

```ts
interface NumberDictionary {
  //[]中的类型只能是string或者number类型，其他类型不允许
  [index: number]: string;
  [index: string]: any;
}

let dict: NumberDictionary = {};

//[]只能填写数字
dict[42] = "hello";  // OK
console.log(dict[42]);  // 输出 "hello"

//[]只能填写字符串
dict["world"] = 42;  // OK
console.log(dict["world"]);  // 输出 42
```

在这个例子中，`NumberDictionary` 的索引签名表示了所有的 `number` 索引返回的值都是 `string` 类型，而所有的 `string` 索引返回的值都是 `any` 类型。

### 对象类型索引签名-类型问题

在使用 TypeScript 的索引签名时，有几个常见的类型问题可能会出现。以下是一些例子和如何解决它们。

**1. 任意属性和其他特定属性的类型不一致**

在 TypeScript 中，当你为对象类型定义了一个索引签名后，这个对象将可以接受任意数量、任意名称的属性，而这些属性的类型必须与索引签名定义的类型一致。这会导致一个问题，即当你想要为这个对象定义一些特定的属性，并且这些属性的类型与索引签名的类型不一致时，TypeScript 会报错。

```ts
interface MyObject {
  [key: string]: number;
  name: string;  // Error: Property 'name' of type 'string' is not assignable to string index type 'number'.
}
```

在这个例子中，`MyObject` 的索引签名定义了任意 `string` 键的值都应该是 `number` 类型，但是 `name` 属性的类型是 `string`，所以 TypeScript 报错。

解决这个问题的一种方式是更改索引签名的类型，使其成为所有属性类型的联合类型：

```ts
interface MyObject {
  [key: string]: number | string;
  name: string;  // OK now
}
```

**2. `number` 索引签名的返回值类型不是 `string` 索引签名返回值类型的超集**

在 TypeScript 中，当你同时为对象类型定义了 `number` 索引签名和 `string` 索引签名时，`number` 索引签名的返回值类型必须是 `string` 索引签名返回值类型的子集。这是因为在 JavaScript 中，当你使用 `number` 类型的值作为对象的索引时，这个值会被自动转换为 `string` 类型。

例如：

```ts
interface MyObject {
  [key: string]: string;
  [index: number]: number;  // Error: Numeric index type 'number' is not assignable to string index type 'string'.
}
```

在这个例子中，`MyObject` 的 `number` 索引签名定义了任意 `number` 索引的值都应该是 `number` 类型，但是 `string` 索引签名定义了任意 `string` 索引的值都应该是 `string` 类型，所以 TypeScript 报错。

解决这个问题的一种方式是更改 `string` 索引签名的类型，使其成为所有索引签名类型的联合类型：

```ts
interface MyObject {
  [key: string]: number | string;
  [index: number]: number;  // OK now
}
```

### 两个索引签名

- 可以同时满足两种索引签名方式，但是不能够使用联合类型，而是需要分开来写

```ts
interface IIndexType {
  // 两个索引类型的写法
  [index: number]: string
  [key: string]: any

  // 要求一:下面的写法不允许: 数字类型索引的类型, 必须是字符串类型索引的类型的 子类型
  // 结论: 数字类型必须是比如字符串类型更加确定的类型(需要是字符串类型的子类型)
  // 原因: 所有的数字类型都是会转成字符串类型去对象中获取内容
  // 数字0: number|string, 当我们是一个数字的时候, 既要满足通过number去拿到的内容, 不会和string拿到的结果矛盾
  // 数字"0": string

  // 数字0: string
  // 数字"0": number|string
  // [index: number]: number|string
  // [key: string]: string

  // 要求二: 如果索引签名中有定义其他属性, 其他属性返回的类型, 必须符合string类型返回的属性
  // [index: number]: string
  // [key: string]: number|string

  // aaa: string
  // bbb: boolean 错误的类型
}

const names: IIndexType = ["abc", "cba", "nba"]
const item1 = names[0]
const forEachFn = names["forEach"]

names["aaa"]

export {}
```

## TypeScript泛型编程

### 认识泛型

- **软件工程的主要目的是构建不仅仅明确和一致的API，还要让你的代码具有很强的可重用性：**
  - 比如我们可以通过函数来封装一些API，通过传入不同的函数参数，让函数帮助我们完成不同的操作
  - 但是对于参数的类型是否也可以参数化呢？
- **什么是类型的参数化？**
  - 我们来提一个需求：**封装一个函数，传入一个参数，并且返回这个参数**
- **如果我们是TypeScript的思维方式，要考虑这个参数和返回值的类型需要一致：**

```ts
function foo(arg:number):number{//传入的是number，返回的也需要是number
  return arg
}
```

- **上面的代码虽然实现了，但是不适用于其他类型，比如string、boolean、Person等类型：**
  - 但是如果我们直接any的话又会直接失去类型检测的优势

```ts
function foo(arg:any):any{
	return arg
}
```

### 泛型实现类型参数化

> 在许多编程语言中，泛型是一种非常强大的工具，它允许我们编写可处理不同数据类型的代码，而不需要对每种类型都编写特定的代码。这样可以增加代码的复用性，减少错误，提高效率。

- **虽然any是可以的，但是定义为any的时候，我们其实已经丢失了类型信息：**

  - 比如我们传入的是一个number，那么我们希望返回的可不是any类型，而是number类型
  - 所以，我们需要在函数中可以捕获到参数的类型是number，并且同时使用它来作为返回值的类型

- **我们需要在这里使用一种特性的变量 - 类型变量（type variable），它作用于类型，而不是值：**

  ![image-20230608062707425](.\typescript_images\image-20230608062707425.png)

- **这里我们可以使用两种方式来调用它：**

  - 方式一：通过 **<类型> 的方式**将类型传递给函数

  - 方式二：通过**类型推导（type argument inference）**，**自动推到出我们传入变量的类型**

    ✓ 在这里会**推导出它们是 字面量类型**的，因为**字面量类型对于我们的函数也是适用**的

```ts
//<T>使用泛型，T表示泛型应用的位置
function bar<T>(arg:T):T{
  return arg
}

//在使用的时候确认填入的泛型，会传递到函数中。这是方式1的完整写法
const res1 = bar<{name:string}>({name:"xiaoyu"})
const res2 = bar<number>(123)

//方式2：省略写法，会自动推导，且推导得更加精准
const res3 = bar("你好啊")
```

![image-20230608063747829](.\typescript_images\image-20230608063747829.png)

- 使用const会推导得更加具体，内容直接作为类型。使用let会推导出string类型

![image-20230608063809797](.\typescript_images\image-20230608063809797.png)

#### 泛型-useState练习

```ts
// 定义一个泛型函数 `useState`，它接受一个初始状态 `initialState`，并返回一个元组。
// 该元组的第一个元素是状态值，第二个元素是一个用于设置新状态的函数。
// 这是React中 `useState` 钩子函数的简化版，它用于在函数组件中添加局部状态。

// <Type> 是泛型参数，表示 `initialState`、`state` 以及 `setState` 函数接受的 `newState` 参数的类型。
function useState<Type>(initialState: Type): [Type, (newState: Type) => void] {
  
  // `state` 是一个局部变量，用于存储当前的状态值。
  let state = initialState
  
  // `setState` 是一个函数，它接受一个新的状态值 `newState`，
  // 并将 `state` 设置为 `newState`。
  function setState(newState: Type) {
    state = newState
  }

  // 返回一个包含 `state` 和 `setState` 的元组。
  return [state, setState]
}

// 使用 `useState` 函数初始化一些状态变量。
// `useState` 函数的泛型参数由传入函数的参数的类型自动推断出来。

// 初始化一个名为 `count` 的状态变量，初始值为 `100`。
// 因为 `100` 是一个 `number` 类型，所以 `count` 的类型被推断为 `number`，
// `setCount` 的类型被推断为 `(newCount: number) => void`。
const [count, setCount] = useState(100)

// 初始化一个名为 `message` 的状态变量，初始值为 `"Hello World"`。
// 因为 `"Hello World"` 是一个 `string` 类型，所以 `message` 的类型被推断为 `string`，
// `setMessage` 的类型被推断为 `(newMessage: string) => void`。
const [message, setMessage] = useState("Hello World")

// 初始化一个名为 `banners` 的状态变量，初始值为一个空数组 `[]`。
// 在这里，我们明确地指定了 `useState` 的泛型参数为 `any[]`，
// 所以 `banners` 的类型被推断为 `any[]`，
// `setBanners` 的类型被推断为 `(newBanners: any[]) => void`。
const [banners, setBanners] = useState<any[]>([])

export {}
```

### 泛型的基本补充

- **当然我们也可以传入多个类型：**

![image-20230608064001742](.\typescript_images\image-20230608064001742.png)

- **平时在开发中我们可能会看到一些常用的名称：**
  - T：Type的缩写，类型
  -  K、V：key和value的缩写，键值对
  - E：Element的缩写，元素
  - O：Object的缩写，对象

### 泛型接口

- **在定义接口的时候我们也可以使用泛型：**
  - 实现了一个简单的字典（键值对）数据结构：

```typescript
// 定义一个泛型接口 `Dictionary`，它接受两个类型参数 `K` 和 `V`，
// 分别代表字典的键的类型和值的类型。
interface Dictionary<K, V> {
  // 定义一个索引签名，表示这个接口可以用任何 `K` 类型的键来索引，
  // 索引的结果是 `V` 类型的值。
  [key: K]: V;
  
  // 定义一个 `add` 方法，它接受一个键 `key` 和一个值 `value`，
  // 并没有返回值（即返回 `void`）。
  add(key: K, value: V): void;
  
  // 定义一个 `get` 方法，它接受一个键 `key`，
  // 并返回一个 `V` 类型的值。
  get(key: K): V;
}

// 实现 `Dictionary` 接口的一个类 `MyDictionary`。
class MyDictionary<K, V> implements Dictionary<K, V> {
  private data: Map<K, V> = new Map();
  
  add(key: K, value: V): void {
    this.data.set(key, value);
  }

  get(key: K): V | undefined {
    return this.data.get(key);
  }
}

// 使用 `MyDictionary` 类创建一个新的字典实例。
// 这个实例的键的类型是 `string`，值的类型是 `number`。
const dict = new MyDictionary<string, number>();

// 使用 `add` 方法添加一些键值对。
dict.add("one", 1);
dict.add("two", 2);

// 使用 `get` 方法获取一些值。
console.log(dict.get("one"));  // 输出：1
console.log(dict.get("two"));  // 输出：2

export {}
```

### 泛型类

> 使用泛型实现的简单的 TypeScript 类，该类实现了一个栈数据结构：
>
> - 在这个例子中，泛型类 `Stack<T>` 提供了一个通用的栈数据结构，我们可以在创建 `Stack` 实例时指定栈中元素的类型。这是通过使用类型参数 `T` 来声明类的属性和方法的参数和返回值的类型实现的。这意味着我们可以使用这个类来创建任何类型的元素的栈。

```typescript
// 创建一个泛型类 `Stack`，`T` 是这个类的类型参数。
// `T` 可以代表任何类型，这样我们可以在创建 `Stack` 实例时指定栈中元素的类型。
class Stack<T> {
  // 声明一个私有的 `items` 属性，它是 `T` 类型元素组成的数组。
  // 这个数组用于存储栈中的元素。
  private items: T[] = [];

  // `push` 方法接受一个 `T` 类型的参数 `item`，
  // 并将 `item` 添加到 `items` 数组的末尾。
  // 在栈中，末尾表示栈顶。
  push(item: T): void {
    this.items.push(item);
  }

  // `pop` 方法没有参数，它移除并返回 `items` 数组的最后一个元素，
  // 即栈顶的元素。如果栈为空，返回 `undefined`。
  pop(): T | undefined {
    return this.items.pop();
  }
}

// 创建一个 `Stack` 实例 `numberStack`，并指定栈中元素的类型为 `number`。
const numberStack = new Stack<number>();

// 使用 `push` 方法向 `numberStack` 添加一些元素。
numberStack.push(1);
numberStack.push(2);
numberStack.push(3);

// 使用 `pop` 方法移除并输出 `numberStack` 的栈顶元素。
console.log(numberStack.pop());  // 输出：3
console.log(numberStack.pop());  // 输出：2
console.log(numberStack.pop());  // 输出：1

export {}
```

### 泛型约束（Generic Constraints）

> 在 TypeScript 中，如果我们希望传入的泛型类型有某些共性，但是这些共性可能不是在同一种类型中，我们可以使用泛型约束（Generic Constraints）。这允许我们限制泛型必须符合一定的接口或者类。
>
> 泛型约束的基本语法是使用 `extends` 关键字，例如 `T extends U`。这样就可以确保泛型 T 具有类型 U 的特性。我们可以创建一个接口，然后使用这个接口来约束泛型。例如，我们创建一个 `HasLength` 接口，然后让泛型 `T` 扩展这个接口：

- **有时候我们希望传入的类型有某些共性，但是这些共性可能不是在同一种类型中：**

  - 比如string和array都是有length的，或者某些对象也是会有length属性的
  - 那么只要是拥有length的属性都可以作为我们的参数类型，那么应该如何操作呢?

  ```typescript
  interface HasLength {
    length: number;
  }
  
  function getLength<T extends HasLength>(item: T): number {
    return item.length;
  }
  
  //这样无论我们传入什么类型的参数给 getLength 函数，只要它有 length 属性就行。
  
  //需要注意的是，在使用泛型约束时，我们不能使用不在约束类型中的属性或方法，否则会导致类型错误。另外，如果我们在函数内部更改了泛型参数的值，可能会导致类型丢失，因为 TypeScript 在编译时会丢弃类型信息。为了避免这种情况，我们应该避免修改泛型参数的值，或者在修改后重新检查类型。
  ```

  ```typescript
  interface ILength {
    length: number
  }
  
  // 1.getLength没有必要用泛型
  function getLength(arg: ILength) {
    return arg.length
  }
  
  const length1 = getLength("aaaa")
  const length2 = getLength(["aaa", "bbb", "ccc"])
  const length3 = getLength({ length: 100 })
  
  
  // 2.获取传入的内容, 这个内容必须有length属性
  // Type相当于是一个变量, 用于记录本次调用的类型, 所以在整个函数的执行周期中, 一直保留着参数的类型
  function getInfo<Type extends ILength>(args: Type): Type {
    return args
  }
  
  const info1 = getInfo("aaaa")
  const info2 = getInfo(["aaa", "bbb", "ccc"])
  const info3 = getInfo({ length: 100 })
  
  // getInfo(12345)
  // getInfo({})
  
  export {}
  ```

- **这里表示是传入的类型必须有这个属性，也可以有其他属性，但是必须至少有这个成员**

- **在泛型约束中使用类型参数（Using Type Parameters in Generic Constraints）**

  - 你可以声明一个类型参数，这个类型参数被其他类型参数约束

- **举个栗子：我们希望获取一个对象给定属性名的值**

  - 我们需要确保我们不会获取 obj 上不存在的属性
  - 所以我们在两个类型之间建立一个约束

  在 TypeScript 中，`keyof` 是一个索引类型查询操作符。对于任何类型 `T`，`keyof T` 的结果为 `T` 上已知的公共属性名的联合。说人话就是必须是T上面有的属性

```typescript
// 定义接口 IKun，包含两个属性，name 是 string 类型，age 是 number 类型
interface IKun {
  name: string
  age: number
}

// 使用 keyof 操作符获取 IKun 上的属性名，结果为 "name" | "age"
type IKunKeys = keyof IKun 

// 定义一个泛型函数 getObjectProperty
// O 是任意对象类型，K 是 O 的属性名的类型
// 函数接收一个 O 类型的 obj 参数和一个 K 类型的 key 参数
// 函数返回 obj 对象中 key 属性的值
function getObjectProperty<O, K extends keyof O>(obj: O, key: K){
  return obj[key]
}

// 定义一个对象 info
const info = {
  name: "why",
  age: 18,
  height: 1.88
}

// 使用 getObjectProperty 函数获取 info 对象的 name 属性的值
const name = getObjectProperty(info, "name")

export {}
```

> 在这段代码中，`keyof` 的作用是获取 `IKun` 类型的所有属性名，然后将这些属性名作为字符串类型的联合类型，赋值给 `IKunKeys` 类型。然后在 `getObjectProperty` 函数中，通过泛型约束 `K extends keyof O` 保证了传入的 key 一定是 obj 对象的属性名，从而避免了传入非法属性名的错误。

### 映射类型（Mapped Types）

- **有的时候，一个类型需要基于另外一个类型，但是你又不想拷贝一份，这个时候可以考虑使用映射类型**
  - 大部分内置的工具都是通过映射类型来实现的
  - 大多数类型体操的题目也是通过映射类型完成的
- **映射类型建立在索引签名的语法上：**
  - 映射类型，就是使用了 PropertyKeys 联合类型的泛型
  - 其中 PropertyKeys 多是通过 keyof 创建，然后循环遍历键名创建一个类型

![image-20230608073338015](.\typescript_images\image-20230608073338015.png)

### 映射修饰符（Mapping Modifiers）

- **在使用映射类型时，有两个额外的修饰符可能会用到：**
  - 一个是 readonly，用于设置属性只读
  - 一个是 ? ，用于设置属性可选
- **你可以通过前缀 - 或者 + 删除或者添加这些修饰符，如果没有写前缀，相当于使用了 + 前缀**
  - 不过用得很少

![image-20230608073408832](.\typescript_images\image-20230608073408832.png)

## 内置工具和类型体操

- **类型系统其实在很多语言里面都是有的，比如Java、Swift、C++等等，但是相对来说TypeScript的类型非常灵活：**
  - 这是因为TypeScript的目的是为JavaScript**添加一套类型校验系统**，因为JavaScript本身的灵活性，也让**TypeScript类型系统不得不增加更附加的功能**以适配JavaScript的灵活性
  - 所以TypeScript是一种可以**支持类型编程的类型系统**
- **这种类型编程系统为TypeScript增加了很大的灵活度，同时也增加了它的难度：**
  - 如果你不仅仅在开发业务的时候为自己的JavaScript代码增加上类型约束，那么基本不需要太多的类型编程能力
  - 但是如果**你在开发一些框架、库，或者通用性的工具，为了考虑各种适配的情况，就需要使用类型编程**
- **TypeScript本身为我们提供了类型工具，帮助我们辅助进行类型转换（前面有用过关于this的类型工具）**
- **很多开发者为了进一步增强自己的TypeScript编程能力，还会专门去做一些类型体操的题目：**
  - https://github.com/type-challenges/type-challenges
  - https://ghaiklor.github.io/type-challenges-solutions/en/

### 条件类型（Conditional Types）

- **很多时候，日常开发中我们需要基于`输入的值来决定输出的值`，同样我们`也需要基于输入的值的类型来决定输出的值的类型`**

- **条件类型（Conditional types）**就是用来帮助我们描述**输入类型和输出类型之间**的关系

  条件类型的写法有点类似于 JavaScript 中的条件表达式（condition ? trueExpression : falseExpression ）：

  ```ts
  SomeType extends OtherType ? TrueType : FalseType;
  ```

![image-20230608075947592](.\typescript_images\image-20230608075947592.png)

### 在条件类型中推断（inter）

- **在条件类型中推断（Inferring Within Conditional Types）**
  - 条件类型提供了 infer 关键词，可以从正在比较的类型中推断类型，然后在 true 分支里引用该推断结果
- **比如我们现在有一个数组类型，想要获取到一个函数的参数类型和返回值类型：**

![image-20230608080428293](.\typescript_images\image-20230608080428293.png)

### 分发条件类型（Distributive Conditional Types）

- **当在泛型中使用条件类型的时候，如果传入一个联合类型，就会变成 分发的（distributive）**

![image-20230608080444993](.\typescript_images\image-20230608080444993.png)

- **如果我们在 ToArray 传入一个联合类型，这个条件类型会被应用到联合类型的每个成员：**
  - 当传入string | number时，会遍历联合类型中的每一个成员
  - 相当于ToArray<string> | ToArray<number>
  - 所以最后的结果是：string[] | number[]

### Partial< Type >

> 在 TypeScript 中，`Partial<T>` 是一个非常有用的工具类型，它将一个类型的所有属性标记为可选。如果你想自己实现一个类似的类型，你可以使用映射类型：

- **用于构造一个Type下面的所有属性都设置为可选的类型**

```ts
/**
 * 自定义的Partial类型，将一个类型的所有属性标记为可选。
 * 
 * 这个类型使用了映射类型，通过 keyof T 取得类型 T 的所有属性，
 * 然后将这些属性映射为它们原来的类型，但添加了 '?' 标记，使它们变为可选。
 * 
 * @template T 要转换的类型。
 */
type MyPartial<T> = {
  [P in keyof T]?: T[P];
};
```

这段代码定义了一个泛型类型 `MyPartial`，它接受一个类型参数 `T`。`[P in keyof T]?: T[P];` 是一个映射类型，它对 `T` 的所有属性进行迭代，将每个属性的类型变为可选。`P` 是属性名，`T[P]` 是对应的属性类型。

例如，如果你有一个类型 `Person`：

```typescript
interface Person {
  name: string;
  age: number;
}
```

那么 `MyPartial<Person>` 就相当于：

```typescript
interface Person {
  name?: string;
  age?: number;
}
```

这个自定义的 `MyPartial` 类型可以让你更灵活地处理对象，因为你可以选择性地提供对象的部分属性，而不需要提供所有属性。

### Required< Type >

- 用于构造一个Type下面的所有属性全都设置为必填的类型，这个工具类型跟 Partial 相反。

- `Required<Type>` 是 TypeScript 的一个工具类型，用于创建一个类型，其中 Type 的所有属性都设置为必需。这意味着 `Required<Type>` 的结果类型将不允许任何属性为 `undefined`。

- 具体的，`Required<Type>` 的定义如下：

  ```typescript
  type Required<T> = {
      [P in keyof T]-?: T[P];
  };
  ```

  这里的 `P in keyof T` 将迭代 `T` 的所有属性，然后 `-?` 操作符将移除每个属性的可选性，使其成为必需的。

  例如：

```typescript
interface Person {
    name?: string;
    age?: number;
}

const person: Required<Person> = {
    // 这里 name 和 age 都是必需的
    name: "John Doe",
    age: 30
};

```

### Readonly< Type >

- **用于构造一个Type下面的所有属性全都设置为只读的类型，意味着这个类型的所有的属性全都不可以重新赋值**

```typescript
interface IKun {
  name: string
  age: number
  slogan?: string
}

// 类型体操
type HYReadonly<T> = {
  readonly [P in keyof T]: T[P] 
}


// IKun都变成可选的
type IKun2 = HYReadonly<IKun>

export {}
```

### Record<Keys, Type>

- **用于构造一个对象类型，它所有的key(键)都是Keys类型，它所有的value(值)都是Type类型**

```typescript
interface IKun {
  name: string
  age: number
  slogan?: string
}

// 类型体操
// name | age | slogan
type keys = keyof IKun
type Res = keyof any // => number|string|symbol

// 确实keys一定是可以作为key的联合类型
type HYRecord<Keys extends keyof any, T> = {
  [P in Keys]: T
}


// IKun都变成可选的
type t1 = "上海" | "北京" | "洛杉矶"
type IKuns = HYRecord<t1, IKun>

const ikuns: IKuns = {
  "上海": {
    name: "xxx",
    age: 10
  },
  "北京": {
    name: "yyy",
    age: 5
  },
  "洛杉矶": {
    name: "zzz",
    age: 3
  }
}

export {}
```

### Pick<Type, Keys>

- 用于构造一个类型，它是从Type类型里面挑了一些属性Keys

```typescript
interface IKun {
  name: string
  age: number
  slogan?: string
}

// 确实keys一定是可以作为key的联合类型
type HYPick<T, K extends keyof T> = {
  [P in K]: T[P]
}

// IKun都变成可选的
type IKuns = HYPick<IKun, "slogan"|"name">


export {}
```

### Omit<Type, Keys>

- **用于构造一个类型，它是从Type类型里面过滤了一些属性Keys**

```typescript
interface IKun {
  name: string
  age: number
  slogan?: string
}

// 确实keys一定是可以作为key的联合类型
type HYOmit<T, K extends keyof T> = {
  [P in keyof T as P extends K ? never: P]: T[P]
}

// IKun都变成可选的
type IKuns = HYOmit<IKun, "slogan"|"name">


export {}
```

### Exclude<UnionType, ExcludedMembers>

- **用于构造一个类型，它是从UnionType联合类型里面排除了所有可以赋给ExcludedMembers的类型**

```typescript
type IKun = "sing" | "dance" | "rap"

// 确实keys一定是可以作为key的联合类型
type HYExclude<T, E> = T extends E? never: T

// IKun都变成可选的
type IKuns = HYExclude<IKun, "rap">


export {}
```

### Extract<Type, Union>

- **用于构造一个类型，它是从Type类型里面提取了所有可以赋给Union的类型**

```typescript
type IKun = "sing" | "dance" | "rap"

// 确实keys一定是可以作为key的联合类型
type HYExtract<T, E> = T extends E? T: never

// IKun都变成可选的
type IKuns = HYExtract<IKun, "rap"|"dance">


export {}
```

### NonNullable< Type >

- **用于构造一个类型，这个类型从Type中排除了所有的null、undefined的类型**

```typescript
type IKun = "sing" | "dance" | "rap" | null | undefined

// 确实keys一定是可以作为key的联合类型
type HYNonNullable<T> = T extends null|undefined ? never: T

// IKun都变成可选的
type IKuns = HYNonNullable<IKun>

export {}
```

### ReturnType< Type >

- **用于构造一个含有Type函数的返回值的类型**
- `ReturnType<Type>` 是 TypeScript 的一个工具类型，用于获取函数 `Type` 的返回类型。这个工具类型可以帮助我们在处理函数和方法时，获取其返回值的类型。

```typescript
type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any;
```

这里使用了 TypeScript 的条件类型和类型推断。如果 `T` 是一个函数，则返回类型 `R` 是这个函数的返回类型。

```typescript
function greet(name: string): string {
  return `Hello, ${name}!`;
}

type Greeting = ReturnType<typeof greet>;  // Greeting 是 string 类型
```

在这个例子中，`greet` 函数的返回类型是 `string`，所以 `ReturnType<typeof greet>` 得到的类型 `Greeting` 也是 `string`。

### InstanceType< Type >

- **用于构造一个由所有Type的构造函数的实例类型组成的类型**

```typescript
class Person {}
class Dog {}


// 类型体操
type HYInstanceType<T extends new (...args: any[]) => any> = T extends new (...args: any[]) => infer R? R: never


const p1: Person = new Person()

// typeof Person: 构造函数具体的类型
// InstanceType构造函数创建出来的实例对象的类型
type HYPerson = HYInstanceType<typeof Person>
const p2: HYPerson = new Person()


// 构造函数的例子
// 通过的创建实例的工具函数时会用到这个InstanceType
function factory<T extends new (...args: any[]) => any>(ctor: T): HYInstanceType<T> {
  return new ctor()
}

const p3 = factory(Person)
const d = factory(Dog)
```

## TypeScript知识扩展

### TypeScript模块化

- **JavaScript 有一个很长的处理模块化代码的历史，TypeScript 从 2012 年开始跟进，现在已经实现支持了很多格式。但是随着时间流逝，社区和 JavaScript 规范已经使用为名为 ES Module的格式，这也就是我们所知的import/export 语法**
  - ES 模块在 2015 年被添加到 JavaScript 规范中，到 2020 年，大部分的 web 浏览器和 JavaScript 运行环境都已经广泛支持
  - 所以在**TypeScript中最主要使用的模块化方案就是ES Module**

![image-20230608082456014](.\typescript_images\image-20230608082456014.png)

### 非模块（Non-modules）

- **我们需要先理解 TypeScript 认为什么是一个模块。**
  - JavaScript 规范声明任何**没有 export 的 JavaScript 文件都应该被认为是一个脚本，而非一个模块**
  - 在一个脚本文件中，**变量和类型会被声明在共享的全局作用域**，导致文件之间的命名冲突，将多个输入文件合并成一个输出文件，或者在 HTML使用多个 <script> 标签加载这些文件
- **如果你有一个文件，现在没有任何 import 或者 export，但是你希望它被作为模块处理，添加这行代码：**

```ts
export {}
```

- **这会`把文件改成一个没有导出任何内容的模块`，这个语法可以生效，无论你的模块目标是什么**

### 内置类型导入（Inline type imports）

- **TypeScript 4.5 也允许单独的导入，你需要使用 type 前缀(导入type就加type前缀关键词，导入interface就加interface前缀) ，表明被导入的是一个类型：**
  - 这个类型可加可不加，但最好是要加一下，首先能够可以明确导入的是类型而不是其他函数之类的东西。
  - 最终转化成JS代码后会去掉，对最终代码没有影响，对编译的速度会更快

![image-20230608090202860](.\typescript_images\image-20230608090202860.png)

- **这些可以让一个非 TypeScript 编译器比如 Babel、swc 或者 esbuild 知道什么样的导入可以被安全移除**

![image-20230608090217500](.\typescript_images\image-20230608090217500.png)

### 命名空间namespace（了解）

- **TypeScript 有它自己的模块格式，名为 namespaces ，它在 ES 模块标准之前出现**，现在用得很少了
  - 命名空间**在TypeScript早期**时，称之为**内部模块**，目的是**将一个模块内部再进行作用域的划分，防止一些命名冲突的问题**
  - 虽然**命名空间没有被废弃，但是由于 ES 模块已经拥有了命名空间的大部分特性**，因此**更推荐使用 ES 模块**，这样才能与JavaScript 的（发展）方向保持一致

![image-20230608091322103](.\typescript_images\image-20230608091322103.png)<img src=".\typescript_images\image-20230608091327462.png" style="zoom:80%;" />

![image-20230608091338344](.\typescript_images\image-20230608091338344.png)

### 类型的查找

- **之前我们所有的typescript中的类型，几乎都是我们自己编写的，但是我们也有用到一些其他的类型：**

![image-20230608091848436](.\typescript_images\image-20230608091848436.png)

- **大家是否会奇怪，我们的HTMLImageElement类型来自哪里呢？甚至是document为什么可以有getElementById的方法呢？**
  -  其实这里就涉及到**typescript对类型的管理和查找规则**了
- **我们这里先给大家介绍另外的一种typescript文件：.d.ts文件**
  - 我们之前编写的typescript文件都是 .ts 文件，这些文件最终会输出 .js 文件，也是我们通常编写代码的地方
  - 还有另外一种文件 .d.ts 文件，它是用来做类型的声明(declare)，称之为**类型声明（Type Declaration）**或者**类型定义（Type Definition）**文件
  - 它仅仅用来做类型检测，告知typescript我们有哪些类型
- **那么typescript会在哪里查找我们的类型声明呢？**
  - 内置类型声明
  - 外部定义类型声明
  - 自己定义类型声明

#### 内置类型声明

- **内置类型声明是typescript自带的、帮助我们内置了JavaScript运行时的一些标准化API的声明文件；**
  - 包括比如Function、String、Math、Date等内置类型
  - 也包括运行环境中的DOM API，比如Window、Document等
  - 能够直接使用
- **TypeScript 使用模式命名这些声明文件lib.[something].d.ts。**
  - 通过文件目录我们可以看到只要我们使用npm下载过typescript，则会直接在Vscode中内置

![image-20230608092604214](.\typescript_images\image-20230608092604214.png)

- **内置类型声明通常在我们安装typescript的环境中会带有的；**
  - https://github.com/microsoft/TypeScript/tree/main/lib

#### 内置声明的环境

- **我们可以通过target和lib来决定哪些内置类型声明是可以使用的：**
  - 例如，startsWith字符串方法只能从称为ECMAScript 6的 JavaScript 版本开始使用
- **我们可以通过target的编译选项来配置：TypeScript通过lib根据您的target设置更改默认包含的文件来帮助解决此问题**
  - https://www.typescriptlang.org/tsconfig#lib

![image-20230608101942278](.\typescript_images\image-20230608101942278.png)

### 外部定义类型声明 – 第三方库

- 需要先安装webpack的环境

```ts
1. npm init//初始化package.json配置项，记录安装包依赖信息

2. npm install webpack webpack-cli -D//搭建webpack

3. 创建webpack.config.json文件用来导出webpack
```

#### Webpack文件配置

> 需要使用如下命令开启服务器
>
> npm install webpack-dev-server -D
>
> - 然后到package.json文件中的script中配置`"serve":webpack serve`
> - 创建tsconfig.json文件，因为xxx-loader需要依赖这个文件
> - 就能够以npm run serve进行启动

```typescript
// 导入 Node.js 的 path 模块，用于处理文件和目录的路径。
const path = require("path")
// 导入 html-webpack-plugin 插件，用于简化 HTML 文件的创建，以便为你的 webpack 包提供服务。
const HtmlWeabpckPlugin = require("html-webpack-plugin")

// 对外暴露一个配置对象，Webpack 会根据这个配置对象来进行打包。
module.exports = {
  // 指定 Webpack 的运行模式，"development" 表示开发模式。
  mode: "development",
  
  // 指定入口文件的路径。
  entry: "./src/index.ts",
  
  // 配置输出相关的选项。
  output: {
    // 输出文件的目标路径，使用 path.resolve 对路径进行解析。
    path: path.resolve(__dirname, "./dist"),
    // 输出文件的名称。
    filename: "bundle.js"
  },
  
  // 配置模块解析方式，extensions 指定了模块的后缀名列表。
  resolve: {
    extensions: [".ts", ".js", ".cjs", ".json"]
  },
  
  // 配置 webpack-dev-server，这里暂未配置任何选项。
  devServer: {},
  
  // 定义模块的处理规则。
  module: {
    rules: [
      {
        // 使用正则表达式匹配所有 .ts 文件。
        test: /\.ts$/,
        // 为这些文件指定使用 ts-loader。
        loader: "ts-loader"
      },
      {
        // 使用正则表达式匹配所有图片文件。
        test: /\.(png|jpe?g|svg|gif)$/,
        // 指定图片资源的处理方式，将图片作为资源导入。
        type: "asset/resource"
      }
    ]
  },
  
  // 配置 Webpack 使用的插件。
  plugins: [
    new HtmlWeabpckPlugin({
      // 指定插件的模板文件路径。
      template: "./index.html"
    })
  ]
}
```

- **外部类型声明通常是我们使用一些库（比如第三方库）时，需要的一些类型声明**

- **这些库通常有两种类型声明方式：**

- 方式一：在**自己库中进行类型声明（编写.d.ts文件）**，比如axios

- 方式二：通过**社区的一个公有库DefinitelyTyped存放类型声明文件**

  - 该库的GitHub地址：https://github.com/DefinitelyTyped/DefinitelyTyped/
  - 该库查找声明安装方式的地址：https://www.typescriptlang.org/dt/search?search=
  - 比如我们安装react的类型声明： npm i @types/react --save-dev

  ![image-20230609082824258](.\typescript_images\image-20230609082824258.png)

  ![image-20230609004959116](.\typescript_images\image-20230609004959116.png)

  ![image-20230609005016042](.\typescript_images\image-20230609005016042.png)

### 外部定义类型声明 – 自定义声明

- **什么情况下需要自己来定义声明文件呢？**

  - 情况一：我们**使用的第三方库是一个纯的JavaScript库**，没有对应的声明文件；比如lodash

    - 假设我们正在使用一个名为 `myJsLibrary` 的纯 JavaScript 库，该库没有提供 TypeScript 声明文件（`.d.ts` 文件）。在这种情况下，我们需要自己创建声明文件来告诉 TypeScript 如何理解这个库的类型。

      我们可以在项目根目录下创建一个 `types` 文件夹（名称可以自定义），然后在该文件夹下创建一个 `myJsLibrary.d.ts` 文件，内容可能如下：

    ```typescript
    // myJsLibrary.d.ts
    
    // 声明一个全局的 myJsLibrary 对象，具有一个名为 doSomething 的方法，该方法接收一个字符串参数，返回 void。
    declare global {
      const myJsLibrary: {
        doSomething(input: string): void;
      };
    }
    ```

    - 这样，当我们在代码中引用 `myJsLibrary` 库时，TypeScript 就会知道 `myJsLibrary.doSomething` 方法的类型。

  - 情况二：我们**给自己的代码中声明一些类型**，方便在其他地方直接进行使用

    - 假设我们有一些公用的类型定义，我们希望在项目中的多个地方使用。我们可以创建一个类型声明文件来存放这些类型定义。

      例如，我们可以创建一个 `types.d.ts` 文件，内容可能如下：

```typescript
// types.d.ts

// 声明一个 IUser 接口，表示用户对象的类型。
interface IUser {
  id: number;
  name: string;
  email: string;
}
```

然后，在我们的代码中，我们就可以直接使用 `IUser` 类型了，无需在每个文件中重新定义。

注意，在实际项目中，如果声明文件（`.d.ts` 文件）和源代码（`.ts` 或 `.tsx` 文件）位于同一目录下，TypeScript 会自动将其包含在编译中。如果声明文件位于其他位置，可能需要在 `tsconfig.json` 中的 `"include"` 或 `"files"` 字段中明确指定其位置。

### declare 声明模块

> 在 TypeScript 中，我们使用 `declare` 关键字来告诉 TypeScript 编译器一些不由 TypeScript 直接处理的元素的类型。这些元素可以是变量、类、函数、模块等。`declare` 不会创建新的局部变量，也不会影响运行时的行为。它只是在编译阶段为 TypeScript 提供类型检查信息。
>
> 当我们使用 `declare` 来声明模块时，我们通常是在描述那些由 JavaScript 编写的模块的类型。这在使用没有 TypeScript 类型定义文件（`.d.ts` 文件）的第三方 JavaScript 库时非常有用。

- **我们也可以声明模块，比如lodash模块默认不能使用的情况，可以自己来声明这个模块：**

![image-20230609083552515](.\typescript_images\image-20230609083552515.png)

- **声明模块的语法: declare module '模块名' {}**

  - 在**声明模块的内部**，我们**可以通过 export 导出对应库的类、函数等**

- 让我们以一个名为 `my-module` 的 JavaScript 模块为例，该模块导出了一个名为 `myFunction` 的函数。我们可以使用 `declare` 来描述 `my-module` 模块的类型：

  ```typescript
  // my-module.d.ts
  
  declare module 'my-module' {
    // myFunction 接受一个字符串参数，返回一个字符串
    export function myFunction(input: string): string;
  }
  ```

- 在这个例子中，我们创建了一个名为 `my-module.d.ts` 的声明文件。在这个文件中，我们使用 `declare module` 来声明一个模块，并且指定了模块的名称 `'my-module'`。然后，我们在模块内部使用 `export function` 来声明一个函数 `myFunction`，并指定了函数的参数类型和返回类型。

  现在，当我们在 TypeScript 文件中导入 `my-module` 模块时，TypeScript 将知道 `myFunction` 函数的类型，因此能够进行正确的类型检查：

  ```typescript
  // some-file.ts
  
  import { myFunction } from 'my-module';
  
  const result = myFunction('Hello'); // TypeScript 知道这是正确的
  const wrongResult = myFunction(123); // TypeScript 将报错，因为参数类型不正确
  ```

  注意，`declare module` 的方式描述 JavaScript 模块的类型，只在 TypeScript 编译阶段有效，不会影响 JavaScript 运行时的行为。

### declare 声明文件

- **在某些情况下，我们也可以声明文件：**
  - 比如**在开发vue的过程中，默认是不识别我们的.vue文件**的，那么**我们就需要对其进行文件的声明**
  - 比如**在开发中我们使用了 jpg 这类图片文件，默认typescript也是不支持的，也需要对其进行声明**
  - 导出声明模块的时候`'*.jpg'`中的`*`表示匹配前面所有内容，只要求后缀为.jpg

![image-20230609083740276](.\typescript_images\image-20230609083740276.png)

### declare 命名空间

- **比如我们在index.html中直接引入了jQuery：**
  - CDN地址： https://cdn.bootcdn.net/ajax/libs/jquery/3.6.0/jquery.js
- **我们可以进行命名空间的声明：**

![image-20230609085200350](.\typescript_images\image-20230609085200350.png)

- **在main.ts中就可以使用了：**

![image-20230609085211557](.\typescript_images\image-20230609085211557.png)

### 认识tsconfig.json文件

> `tsconfig.json` 是 TypeScript 项目的配置文件。它是可选的，但当你的项目需要特定的编译器选项，或者当你的项目文件夹中包含了除 TypeScript 文件之外的其他文件时，你可能就需要使用它了。
>
> `tsconfig.json` 文件的主要作用如下：
>
> 1. 定义项目的根文件：TypeScript 编译器会在 `tsconfig.json` 所在的目录开始查找文件，然后从这些文件开始逐步解析所有引用的和被引用的文件，编译整个项目。
> 2. 设置编译选项：`tsconfig.json` 文件里的 `compilerOptions` 对象可以被用来指定各种编译选项。例如，你可以设置要编译成的 ECMAScript 目标版本（通过 `target` 选项）、要使用的模块系统（通过 `module` 选项）、是否启用严格的类型检查（通过 `strict` 选项），等等。
> 3. 指定要包含的和要排除的文件：通过 `include` 和 `exclude` 属性，你可以更精细地控制哪些文件应该被编译，哪些文件应该被忽略。

- **什么是tsconfig.json文件呢？（官方的解释）**

  - 当目录中出现了 tsconfig.json 文件，则说明该目录是 TypeScript 项目的根目录
  - tsconfig.json 文件指定了编译项目所需的根目录下的文件以及编译选项

- **通俗解释如下：**

- **tsconfig.json文件有两个作用：**

  - **作用一（主要的作用）**：让TypeScript Compiler在编译的时候，知道如何去编译TypeScript代码和进行类型检测

  ✓ 比如是否允许不明确的this选项，是否允许隐式的any类型

  ✓ 将TypeScript代码编译成什么版本的JavaScript代码

  - **作用二**：让编辑器（比如VSCode）可以按照正确的方式识别TypeScript代码

  ✓ 对于哪些语法进行提示、类型错误检测等等

- **JavaScript 项目可以使用 jsconfig.json 文件，它的作用与 tsconfig.json 基本相同，只是默认启用了一些 JavaScript 相关的编译选项。**

  - 在之前的Vue项目、React项目中我们也有使用过

#### tsconfig.json配置

- **tsconfig.json在编译时如何被使用呢?**
  - 在**调用 tsc 命令并且没有其它输入文件参数**时，编译器**将由当前目录开始向父级目录寻找包含 tsconfig 文件的目录**
  - 调用 **tsc 命令并且没有其他输入文件参数**，**可以使用 --project （或者只是 -p）的命令行选项**来**指定包含了 tsconfig.json** 的目录
  - 当命令行中**指定了输入文件参数， tsconfig.json 文件会被忽略**
- **webpack中使用ts-loader进行打包时，也会自动读取tsconfig文件，根据配置编译TypeScript代码**
  - 一般情况下自动打包配置好的内容就足够使用了，基本上不需要修改
- **tsconfig.json文件包括哪些选项呢？**
  - tsconfig.json本身包括的选项非常非常多，我们不需要每一个都记住
  - 可以查看文档对于每个选项的解释：https://www.typescriptlang.org/tsconfig
  - 当我们开发项目的时候，选择TypeScript模板时，tsconfig文件默认都会帮助我们配置好的

### tsconfig.json顶层选项

![image-20230609090040323](.\typescript_images\image-20230609090040323.png)

![image-20230609090045179](.\typescript_images\image-20230609090045179.png)

### tsconfig.json文件

- **tsconfig.json是用于配置TypeScript编译时的配置选项：**
  - https://www.typescriptlang.org/tsconfig
- **常见的选项**

```typescript
{
  // 编译选项
  "compilerOptions": {
    // 指定 ECMAScript 目标版本: 'ES3' (default), 'ES5', 'ES6'/'ES2015', 'ES2016', 'ES2017', 'ES2018', 'ES2019', 'ES2020', 'ES2021', or 'ESNext'.
    "target": "es5",

    // 指定模块代码生成: 'None', 'CommonJS', 'AMD', 'System', 'UMD', 'ES6', 'ES2015', 'ES2020', or 'ESNext'.
    "module": "commonjs",

    // 启用严格类型检查选项
    "strict": true,

    // 在表达式和声明上有隐含的'any'类型时报错
    "noImplicitAny": true,

    // 从 .ts 转换为 .js 时，是否生成对应的 .d.ts 声明文件
    "declaration": true,

    // 解析非相对模块名的基准目录
    "baseUrl": "./",

    // 指定特殊模块的路径
    "paths": {},

    // 生成相应的 '.map' 文件
    "sourceMap": true,

    // 启用所有严格类型检查选项
    "strict": true,

    // 允许从没有设置默认导出的模块中默认导入。
    "allowSyntheticDefaultImports": true,

    // 允许 'json' 扩展名的模块
    "resolveJsonModule": true,

    // 保留 JSX 部分，'react' 对应 React.js, 'preserve' 保留为 .jsx 文件
    "jsx": "react"
  },

  // 需要被编译文件的列表
  "include": [
    "src/**/*"
  ],

  // 不需要被编译文件的列表
  "exclude": [
    "node_modules",
    "**/*.spec.ts"
  ]
}
```

## Axios二次封装(TypeScript版本)

- 创建一个server文件夹(二次封装axios的代码都放在这里面)
  - server文件夹下存放`config`配置文件夹、`modules`模块文件夹(有多个模块)、`request`请求文件夹(除了index.ts文件外还有一个`type.ts`的类型文件)，以及和其他文件夹平级的`index.ts`的导出文件

```typescript
//config文件夹
export const BASE_URL = "http://codercba.com:8000"
export const TIME_OUT = 10000
```

```typescript
//modules文件夹

//entire.ts模块
import { hyRequest2 } from "..";


hyRequest2.request({
  url: "/entire/list",
  params: {
    offset: 0,
    size: 20
  }
}).then(res => {
  console.log(res)
})


interface IHighScoreData {
  list: any[],
  subtitle: string,
  title: string
  type: string,
  _id: string
}
hyRequest2.request<IHighScoreData>({
  url: "/home/highscore",
  interceptors: {
    requestSuccessFn: (config) => {
      console.log("/home/highscore请求成功的拦截")
      return config
    },
    responseSuccessFn: (res) => {
      console.log("/home/highscore响应成功的拦截")
      return res
    }
  }
}).then(res => {
  console.log(res.list, res.subtitle, res.title)
})

//home.ts模块
import hyRequest from "..";

// 发送网络请求
// hyRequest.post
interface IHomeData {
  data: any,
  returnCode: string,
  success: boolean
}

hyRequest.request<IHomeData>({
  url: "/home/multidata"
}).then(res => {
  console.log(res.data, res.success, res.returnCode)
})
```

```typescript
//request文件夹下的index.ts
// 引入axios库
import axios from "axios"
// 引入axios的类型定义
import type { AxiosInstance, AxiosRequestConfig, AxiosResponse } from "axios"
// 引入自定义的类型定义
import type { HYRequestConfig } from "./type"

// 拦截器: 蒙版Loading/token/修改配置

/**
 * 两个难点:
 *  1.拦截器进行精细控制
 *    > 全局拦截器
 *    > 实例拦截器
 *    > 单次请求拦截器
 * 			多个拦截器是能够重叠存在的，而不是后面会覆盖前面 导致前面失效
 *  2.响应结果的类型处理(泛型)
 */

class HYRequest {
  instance: AxiosInstance

  // request实例 => axios的实例
  constructor(config: HYRequestConfig) {
    this.instance = axios.create(config)

    // 每个instance实例都添加拦截器
    this.instance.interceptors.request.use(config => {
      // loading/token
      console.log("全局请求成功的拦截")
      return config
    }, err => {
      console.log("全局请求失败的拦截")
      return err
    })
    this.instance.interceptors.response.use(res => {
      console.log("全局响应成功的拦截")
      return res.data
    }, err => {
      // 在响应失败时打印日志
      console.log("全局响应失败的拦截")
      return err
    })

    // 针对特定的hyRequest实例添加拦截器
    this.instance.interceptors.request.use(
      config.interceptors?.requestSuccessFn,
      config.interceptors?.requestFailureFn
    )
    this.instance.interceptors.response.use(
      config.interceptors?.responseSuccessFn,
      config.interceptors?.responseFailureFn
    )
  }

  // 封装网络请求的方法
  // T => IHomeData
  request<T = any>(config: HYRequestConfig<T>) {
    // 单次请求的成功拦截处理，进行一个回调，调用自身，更新了config
    if (config.interceptors?.requestSuccessFn) {
      config = config.interceptors.requestSuccessFn(config)
    }

    // 返回Promise，因为不确定什么时候返回结果，所以采用promise
    return new Promise<T>((resolve, reject) => {
      this.instance.request<any, T>(config).then(res => {
        // 单词响应的成功拦截处理，调用前面更新的config
        if (config.interceptors?.responseSuccessFn) {
          res = config.interceptors.responseSuccessFn(res)
        }
        resolve(res)
      }).catch(err => {
        reject(err)
      })
    })
  }

  get<T = any>(config: HYRequestConfig<T>) {
    return this.request({ ...config, method: "GET" })
  }
  post<T = any>(config: HYRequestConfig<T>) {
    return this.request({ ...config, method: "POST" })
  }
  delete<T = any>(config: HYRequestConfig<T>) {
    return this.request({ ...config, method: "DELETE" })
  }
  patch<T = any>(config: HYRequestConfig<T>) {
    return this.request({ ...config, method: "PATCH" })
  }
}


export default HYRequest
```

```typescript
// 引入axios的类型定义
import type { AxiosRequestConfig, AxiosResponse } from "axios"

// 定义一个新的接口，它包含四个可选的方法，用于在请求和响应时进行拦截处理。
// 这四个方法分别对应请求成功，请求失败，响应成功和响应失败的情况。
// 他们的参数和返回值分别是请求配置对象，错误对象，响应对象和错误对象。
export interface HYInterceptors<T = AxiosResponse> {
  //请求成功拦截
  requestSuccessFn?: (config: AxiosRequestConfig) => AxiosRequestConfig 
  //请求失败拦截
  requestFailureFn?: (err: any) => any
  //响应成功拦截
  responseSuccessFn?: (res: T) => T
  //响应失败拦截
  responseFailureFn?: (err: any) => any 
}

// 定义一个新的接口，它继承自axios的AxiosRequestConfig接口，并额外添加一个interceptors属性。
// interceptors属性的类型是上面定义的HYInterceptors接口，用于在axios请求中添加自定义的拦截器，在原本的AxiosRequestConfig的基础上通过继承继续去添加拦截器的部分。同时通过这个将泛型T传递了进来到HYInterceptors中进行使用，不然是无法直接传入HYInterceptors的
export interface HYRequestConfig<T = AxiosResponse> extends AxiosRequestConfig {
  interceptors?: HYInterceptors<T>
}
```

```typescript
//全局导出的index.ts文件
import { BASE_URL, TIME_OUT } from "./config";
import HYRequest from "./request";

// 创建第一个HYRequest实例，设置了基本的请求URL和超时时间
const hyRequest = new HYRequest({
  baseURL: BASE_URL,
  timeout: TIME_OUT
})

// 创建第二个HYRequest实例，设置了不同的基本请求URL和超时时间，同时添加了针对请求和响应的拦截器
export const hyRequest2 = new HYRequest({
  baseURL: "http://codercba.com:1888/airbnb/api",
  timeout: 8000,
  interceptors: {
    requestSuccessFn: (config) => {
      console.log("爱彼迎的请求成功的拦截")
      return config
    },
    requestFailureFn: (err) => {
      console.log("爱彼迎的请求失败的拦截")
      return err
    },
    responseSuccessFn: (res) => {
      console.log("爱彼迎的响应成功的拦截")
      return res
    },
    responseFailureFn: (err) => {
      console.log("爱彼迎的响应失败的拦截")
      return err
    }
  }
})

// 默认导出第一个HYRequest实例
export default hyRequest
```

