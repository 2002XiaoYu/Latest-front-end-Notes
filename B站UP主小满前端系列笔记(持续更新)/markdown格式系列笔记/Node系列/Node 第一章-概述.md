# Node 第一章-概述

> 大家好，我是小余，一名前端程序员。向大家分享最实用的技术笔记
>
> - 以下是我发布文章所在的平台，大家可以任意挑选一个合适的平台进行阅读，您的鼓励是作者前进的动力
>
>   知乎：XiaoYu2002
>
>   掘金：XiaoYu2002
>
>   CSDN：XiaoYu2002-AI
>
>   小红书：小余
>
>   GitHub：[2002XiaoYu (小余) (github.com)](https://github.com/2002XiaoYu)
>
> - 我的微信：XiaoYu2002-AI

## 基本介绍

1. nodejs 并不是`JavaScript`应用，也不是编程语言，因为编程语言使用的`JavaScript`,Nodejs是 `JavaScript`的运行时。
   - **Node.js并不是JavaScript应用**：这里的"JavaScript应用"通常指用JavaScript编写的程序或软件。而Node.js本身并不是用JavaScript编写的一个应用程序；它是一个提供环境让JavaScript应用程序运行的平台。
   - **Node.js也不是编程语言**：编程语言是一种用于编写程序的标准化通信方法，例如JavaScript、Python和C++等。Node.js并不引入新的编程语言，它使用的是JavaScript这门已经存在的语言。
   - **Node.js是JavaScript的运行时**：运行时（runtime）指的是程序运行时的环境，它提供了程序运行所需的内置库、工具和其他资源。Node.js为JavaScript提供了一个运行时环境，这意味着它让JavaScript代码可以在服务器端运行，而不仅仅是在浏览器中。在此之前，JavaScript主要用于浏览器环境中，用于增强网页的交互性。Node.js的出现扩展了JavaScript的使用范围，让开发者可以用JavaScript编写服务器端软件，进行文件操作，以及进行网络通信等。

![image-20240401183939175](D:\Desktop\桌面文件夹\GitHub笔记库\Latest-front-end-Notes\B站UP主小满前端系列笔记(持续更新)\markdown格式系列笔记\Node系列\node-images\image-20240401183939175.png)

2. Node.js是建立在Google的V8 JavaScript引擎上的，这个引擎用C++编写，使得我们写的JavaScript代码能够被转换并执行。Node.js采用了异步I/O和事件驱动的架构，这让它在处理大量并发请求时变得非常高效。它利用非阻塞I/O接口和一个名为`libuv`的库来实现事件循环，从而使开发者能够创建高性能且易于扩展的应用程序。

3. Node.js使用npm（Node Package Manager）作为其包管理器，这和Python的pip或Java的Maven相似。Npm是一个巨大的库，提供了数以百万计的可复用代码模块，可通过[官网](https://www.npmjs.com/)访问。

4. `Node.js特别适合于I/O密集型的应用`，比如网络服务器或数据库操作，因为它的I/O处理能力非常强大。但对于CPU密集型任务，比如图像处理或大量的计算操作，Node.js可能不是最佳选择，因为它的单线程模型可能会导致性能瓶颈。不过，对于需要执行CPU密集型任务的场景，可以通过编写C++插件或使用Node.js的Cluster模块来提升性能。

   > 在计算机术语中，I/O代表输入/输出（Input/Output），它涵盖了系统进行数据传输的所有操作，包括从磁盘读取数据、向磁盘写入数据、通过网络发送数据、从网络接收数据等。当我们提到Node.js适合I/O密集型的应用时，我们指的是那些涉及大量读写操作、网络请求或者任何需要频繁访问外部资源的应用。
   >
   > I/O密集型应用的典型场景包括：
   >
   > - 网络服务，如Web服务器或API端点，它们需要处理大量并发的客户端请求。
   > - 文件处理系统，涉及对文件的频繁读写操作。
   > - 数据库操作，尤其是那些需要频繁查询和更新数据库的应用。
   > - 实时通信服务，比如聊天服务器或协同编辑工具，它们需要快速、频繁地在用户之间传递信息。
   >
   > Node.js之所以适合这些场景，是因为它的非阻塞I/O模型使得在一个事件循环中并行处理大量的I/O请求成为可能，而不必为每个I/O操作启动新的线程，从而提高资源利用率和应用性能。

## NodeJS架构图

![image-20240401184559620](D:\Desktop\桌面文件夹\GitHub笔记库\Latest-front-end-Notes\B站UP主小满前端系列笔记(持续更新)\markdown格式系列笔记\Node系列\node-images\image-20240401184559620.png)

这张图展示了Node.js的架构和流程，从JavaScript代码的运行到底层的系统调用。图中的结构可以从上到下解释如下：

1. **Node.js API**: 最上层是Node.js提供的各种内置模块的API，例如文件系统（`fs`）、HTTP、操作系统信息（`os`）、网络（`net`）、数据压缩（`zlib`）等。这是开发者直接与之交互的层面。
2. **Node.js bindings (JS和C/C++的接口)**: 这一层是JavaScript和C/C++之间的桥梁。Node.js的很多API在JavaScript层面是无法直接访问操作系统功能的，它们需要通过这一层的绑定调用C/C++编写的库。
3. **C/C++ 层面的 addon 和 Napi**: 这是一些高级的接口和模块，它们使用C/C++编写，可以通过Node.js的API访问。开发者可以编写addon来扩展Node.js的功能。
4. **底层技术支持**:
   - **V8**: 这是JavaScript执行的引擎，负责编译和运行JavaScript代码。
   - **libuv**: 这是提供事件驱动的I/O的库，支持异步操作和跨平台功能。
   - **DNS解析 (c-ares)**: 这是一个异步DNS请求库，Node.js用它来执行非阻塞的名称解析。
   - **SSL/TLS (OpenSSL)**: 这里的SSL或者是TSL证书都是使用这个C or C++编写的OpenSSL解析的
   - **http-parser**: 这是解析HTTP消息的工具。

整个图的意图是展示Node.js从接收JavaScript调用到最终的系统级执行是如何通过不同层次交互的，确保高性能和高扩展性。这种分层架构让Node.js能够在保持JavaScript编写的简单性的同时，利用C/C++的强大能力来进行系统级别的操作。

## Nodejs 应用场景

> 以下展示并不是所有东西都是nodejs编写而是运行环境可以配合nodejs或者依靠nodejs运行。

### 前端

- **Vue, Angular, React**: 这些是流行的前端JavaScript框架，开发者通常会使用Node.js来运行前端构建工具，如Webpack，或者在开发阶段运行开发服务器。
- **Nuxt.js, Next.js**: 分别为Vue和React的服务端渲染(SSR)框架，可以用Node.js来执行SSR，提高首次加载性能和SEO效果。

### 后端

- **Serverless**: Node.js可以在无服务器架构中作为执行运行时，处理事件驱动的函数。
- **Web应用 (Express, NestJS, Koa)**: 这些是Node.js的Web框架，用于快速开发Web服务和API。
- **gRPC**: 一个高性能的远程过程调用(RPC)框架，Node.js支持gRPC服务器和客户端。

### 爬虫

- **Puppeteer, Cheerio**: Node.js库，用于Web爬取，Puppeteer提供了一个高层次的API来控制Chrome，Cheerio用于解析HTML。

### BFF层/网关层

- **Socket.io**: 一个实时、双向和基于事件的通信库，常用于Node.js来构建实时Web应用。

### 桌面端

- **Electron, Tauri, NWjs**: 这些框架允许开发者使用Node.js和前端技术来构建跨平台的桌面应用。

### 移动端

- **Weex, Ionic, React Native**: 允许使用JavaScript开发移动应用，Node.js通常用于提供CLI工具，编译或打包应用。

### 基建端

- **Webpack, Vite, Rollup, Gulp**: 前端打包和构建工具，运行在Node.js环境中，处理资源编译、打包、优化等任务。
- **Less, Scss, PostCSS**: CSS预处理器，Node.js用于运行这些工具，将代码转换为浏览器可识别的CSS。
- **Babel, SWC**: JavaScript编译器，将ES6+代码转译为向后兼容的JavaScript代码。
- **Inquirer, Command, Shelljs**: Node.js库，用于创建命令行应用、处理命令行输入和执行Unix shell命令。

### 嵌入式

- **Ruff.js**: Ruff.js 是一个基于 JavaScript 的嵌入式软件开发框架，它可以让开发者使用 JavaScript 或 TypeScript 来开发嵌入式应用。这种情况下，Node.js 可能被用来运行一些开发工具，或者Ruff提供了与Node.js类似的API。

### 单元测试

- **Jest**: Jest 是一个流行的JavaScript测试框架，它对异步测试支持良好，易于配置，还自带断言库。它经常用于Node.js应用的单元测试。
- **Vitest**: Vitest 是一个由 Vite 驱动的单元测试框架，它借鉴了 Jest 的API设计，用于在 Vite 项目中进行快速单元测试。
- **e2e (端对端测试)**: 端对端测试是一种测试方法，通常用于测试应用的流程从开始到结束是否按预期工作。这些测试可以在 Node.js 环境中运行，使用诸如 Puppeteer、Playwright 等工具模拟用户交互。

### CICD (持续集成和持续部署)

- **Jenkins**: Jenkins 是一个开源的自动化服务器，用于自动化软件开发过程中的构建、测试和部署。它可以与Node.js项目集成，自动化JavaScript的构建和测试过程。
- **Docker**: Docker 是一个开源的容器化平台，它可以将应用及其环境打包在一个容器中运行。对于Node.js，你可以创建包含Node.js环境的容器来运行应用。
- **Husky**: Husky 是一个工具，可以防止不良的git commit或git push，通过在git钩子中运行测试和linters。它常用于Node.js项目来提高代码质量。
- **miniprogram-ci**: 这是微信小程序的持续集成工具，Node.js可以用来执行这个工具，自动化小程序的构建和发布过程。

### 反向代理

- **http-proxy**: http-proxy 是一个Node.js的库，为实现HTTP代理的功能提供了支持。它可以用于Node.js应用中创建反向代理服务器，转发客户端请求到其他服务器。
- **AnyProxy**: AnyProxy 是一个可扩展的HTTP代理服务器，用Node.js编写，可以用来拦截和修改HTTP请求/响应，用于测试或开发。