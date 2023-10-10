# Webpack+Gulp+Vite+Rollup配置笔记

## 跨域处理-webpack基本使用-babel

### 什么是跨域？

- **理解跨域，首先要理解浏览器的同源策略：**
  - 同源策略是Web安全的基石，它的核心是限制文档或脚本从多个源加载。这个策略帮助防止恶意站点的攻击。
  - 同源策略的判断标准是“协议+主机名+端口号”三者都相同，就被认为是同源。这也被称为“协议/主机/端口元组”或简称“元组”。
- **跨域问题的产生与前后端分离的发展有很大的关系：**
  - 在早期的服务器端渲染时代，前后端代码通常在同一服务器上运行，因此不存在跨域问题。
  - 但随着前后端分离的发展，前端代码和后端API接口往往部署在不同的服务器上，甚至在不同的域名或端口下，这就可能产生跨域问题。
- **当我们从一个源向另一个源发送请求时，就可能产生跨域问题：**
  - 当浏览器发现一个请求（如XHR、Fetch等）不是来自同一源时，就会执行同源策略，阻止这个请求，这就是跨域问题的产生。
  - 但如果静态资源和API接口（或其他资源）部署在同一台服务器上，那么就不会产生跨域问题，因为它们是同源的。
- **下面我们将详细演示跨域产生和不产生的项目部署区别：**
  - **跨域产生的情况：** 假设你的前端代码部署在`http://myapp.com`，而你的API接口部署在`http://api.myapp.com`或`http://myapp.com:8080`。这时，你的前端代码在请求API接口时就会产生跨域问题，因为它们不是同源的。
  - **跨域不产生的情况：** 假设你的前端代码和API接口都部署在`http://myapp.com`，并且前端代码通过相对路径（如`/api/user`）来请求API接口。这时，你的前端代码和API接口是同源的，因此不会产生跨域问题。
- **解决跨域问题的常见方法：**
  - 使用CORS（跨源资源共享）：这是一种W3C标准，它允许服务器在响应头中添加特殊的头信息，告诉浏览器允许来自不同源的请求。
  - 使用JSONP：这是一种早期的跨域解决方案，它利用了`<script>`标签的src属性不受同源策略限制的特性，但它只支持GET请求。
  - 使用代理服务器：你可以在同源的服务器上设置一个代理，将请求转发到不同源的服务器上，从而绕过同源策略。

### 跨域和不跨域的不同访问策略

> 在Web开发中，跨域和不跨域的访问策略主要涉及到浏览器的同源策略和跨源资源共享（CORS）策略。下面我们将详细解释这两种策略，以及它们在跨域和不跨域的情况下的应用。

**1. 同源策略**

- 同源策略是浏览器的一种安全机制，在上面也有说，用于限制来自不同源的文档或脚本之间的交互。如果两个URL的协议、主机名和端口号都相同，那么它们就被认为是同源的。

- 在不跨域的情况下，即前端代码和后端API接口部署在同一台服务器上，同源策略允许这两者之间进行任意的交互。例如，前端代码可以通过AJAX请求获取后端API接口的数据，也可以通过DOM操作修改来自同一源的其他页面的内容。

**2. 跨源资源共享（CORS）策略**

- CORS是一种W3C标准，它允许服务器在响应头中添加特殊的头信息，告诉浏览器允许来自不同源的请求。这就解决了由于同源策略导致的跨域访问限制问题。

- 在跨域的情况下，即前端代码和后端API接口部署在不同的服务器上，如果没有正确配置CORS策略，浏览器就会因为同源策略而阻止前端代码访问后端API接口。但如果服务器在响应头中添加了正确的CORS头信息，例如`Access-Control-Allow-Origin`，那么浏览器就会允许前端代码访问后端API接口。

- 需要注意的是，CORS策略只能解决浏览器的跨域访问限制问题，它不能阻止其他客户端（如curl命令或Postman工具）的跨域请求。因此，后端服务器还需要其他的安全机制（如API密钥或OAuth）来防止未授权的访问。

**同源策略和CORS策略是浏览器在处理跨域和不跨域的访问时的两种主要策略。所以理解这两种策略，以及如何在服务器端配置CORS策略，对于解决跨域问题非常重要。**

![image-20230805231144083](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230805231144083.png)

### 跨域的解决方案总结

- **那么跨域问题如何解决呢？**
  - 所以跨域的解决方案几乎都和服务器有关系，单独的前端基本解决不了跨域（虽然网上也能看到各种方案，都是实际开发基本不会使用）
  - 那这不对吧？明明在配置前端的webpack就可以解决跨域问题了
  - 但这是因为webpack配置的本质也是在webpack-server的服务器中配置了代理

> 跨域问题是由于浏览器的同源策略导致的，因此解决跨域问题的方案大多数都需要服务器端的配合。以下是一些常见和不常见的跨域解决方案：

- **常见的解决方案：**
  - **静态资源和API服务器部署在同一个服务器中：** 这是最直接的解决方案，因为同源策略只限制不同源之间的交互，所以将所有资源部署在同一服务器上可以避免跨域问题。
  - **CORS（跨域资源共享）：** CORS是一种W3C标准，它允许服务器在响应头中添加特殊的头信息，告诉浏览器允许来自不同源的请求。这是一种常用且有效的解决跨域问题的方法。
  - **Node代理服务器：** 通过在Node服务器上设置代理，将前端的请求转发到目标服务器，从而绕过浏览器的同源策略。在开发环境中，我们常常使用webpack-dev-server的代理功能来实现这一点。
  - **Nginx反向代理：** 与Node代理服务器类似，Nginx反向代理也是通过在服务器上设置代理来解决跨域问题的。不过，与Node代理服务器不同的是，Nginx反向代理更适合在生产环境中使用。
- **不常见的解决方案：**
  - **JSONP：** JSONP是一种早期的跨域解决方案，它利用`<script>`标签的src属性不受同源策略限制的特性来实现跨域请求。但JSONP只支持GET请求，且存在一定的安全风险，因此现在很少使用。
  - **postMessage：** postMessage是HTML5引入的一种新的通信方式，它可以实现跨源之间的数据传递。但postMessage的使用相对复杂，且需要目标页面的配合，因此在实际开发中使用不多。
  - **WebSocket：** WebSocket是一种全双工的通信协议，它不受同源策略的限制，因此可以用来解决跨域问题。但WebSocket的使用相对复杂，且需要服务器支持，因此一般不会仅仅为了解决跨域问题而使用WebSocket。

### 前端请求代码

![image-20230805235137875](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230805235137875.png)

### 跨域解决方案二 - CORS

- **跨源资源共享**（CORS， Cross-Origin Resource Sharing跨域资源共享）：

  - 它是一种基于http header的机制
  - 该机制通过允许服务器标示除了它自己以外的其它源（域、协议和端口），使得浏览器允许这些 origin 访问加载自己的资源

- **浏览器将 CORS 请求分成两类：简单请求和非简单请求**

- **只要同时满足以下两大条件，就属于简单请求（不满足就属于非简单请求）（了解即可）**

  - 请求方法是以下是三种方法之一：

  1. HEAD
  2. GET
  3. POST

  - HTTP 的头信息不超出以下几种字段：

  1. Accept
  2. Accept-Language
  3. Content-Language
  4. Last-Event-ID
  5. Content-Type：只限于三个值 application/x-www-form-urlencoded、multipart/form-data、text/plain

![image-20230805235443103](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230805235443103.png)

### 跨域解决方案三 – Node代理服务器

- Node代理服务器是平时开发中前端配置最多的一种方案

> 使用Node.js作为代理服务器是解决跨域问题的一种常见方法。这种方法的基本思想是，由于浏览器的同源策略只对浏览器发出的请求有效，因此我们可以创建一个Node.js服务器，将浏览器的请求发送到这个服务器，然后由这个服务器转发请求到目标服务器，这样就绕过了浏览器的同源策略。

1. **创建一个Node.js服务器**

首先，我们需要创建一个Node.js服务器。这可以通过使用Node.js的内置http模块，或者使用像Express这样的框架来实现。

```javascript
const express = require('express');
const app = express();
```

2. **安装并引入http-proxy-middleware**

   `http-proxy-middleware`是一个非常流行的Node.js代理中间件，我们可以使用它来轻松创建代理服务器。

   ```bash
   npm install http-proxy-middleware --save
   ```

   然后在你的服务器文件中引入它：

   ```javascript
   const { createProxyMiddleware } = require('http-proxy-middleware');
   ```

3. **创建代理规则**

   使用`http-proxy-middleware`创建一个代理规则，将所有匹配某个模式的请求都转发到目标服务器。

   ```javascript
   app.use('/api', createProxyMiddleware({
     target: 'http://target-server.com',
     changeOrigin: true,
   }));
   ```

   在这个例子中，所有以`/api`开头的请求都会被转发到`http://target-server.com`。

   > ​	举个例子，假设Node.js服务器运行在`http://localhost:3000`，我在浏览器中访问`http://localhost:3000/api/users`，那么这个请求将会被Node.js服务器接收，然后转发到`http://target-server.com/api/users`。
   >
   > ​	这个过程是透明的，也就是说，对于前端来说，它就像直接访问`http://localhost:3000/api/users`一样，但实际上，后端的处理是在`http://target-server.com/api/users`。

4. **启动服务器**

   最后，启动你的Node.js服务器。

   ```javascript
   app.listen(3000, () => {
     console.log('Proxy server is running on port 3000');
   });
   ```

   现在，你可以在浏览器中访问`http://localhost:3000/api`，这个请求会被Node.js服务器接收，然后转发到`http://target-server.com`，从而绕过浏览器的同源策略。

这就是使用Node.js作为代理服务器解决跨域问题的基本步骤。需要注意的是，这种方法只适用于开发环境，在生产环境中，需要使用更复杂的解决方案，如CORS或Nginx反向代理。

![image-20230805235517168](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230805235517168.png)

### 跨域解决方案四 – Nginx反向代理

> ​	反向代理是指代理服务器接收客户端的请求，然后将请求转发到内部网络的服务器，并将从服务器得到的结果返回给客户端。这样，客户端就像直接访问代理服务器一样，实际上访问的是内部网络的服务器。

以下是使用Nginx反向代理解决跨域问题的详细步骤：

1. **安装Nginx**

   首先，需要在我们的服务器上安装Nginx。安装方法取决于我们是什么操作系统，你可以在Nginx的官方网站上找到详细的安装指南。

   下载Nginx地址：https://nginx.org/en/download.html

2. **配置Nginx**

   安装完成后，需要编辑Nginx的配置文件。这个文件通常位于`/etc/nginx/nginx.conf`或`/etc/nginx/sites-available/default`。

   在这个文件中，你可以定义一个或多个server块，每个server块对应一个虚拟服务器。在server块中，你可以使用location指令来定义路由规则，然后使用proxy_pass指令将请求转发到目标服务器。

```javascript
server {
    listen 80;
    server_name your-domain.com;

    location /api/ {
        proxy_pass http://target-server.com;
    }
}
```

在这个例子中，所有以`/api/`开头的请求都会被转发到`http://target-server.com`。

3. **重启Nginx**

   完成配置后，你需要重启Nginx以使新的配置生效。

   ```bash
   sudo service nginx restart
   ```

   或者

   ```bash
   sudo systemctl restart nginx
   ```

   然后就可以在浏览器中访问`http://your-domain.com/api/`，这个请求会被Nginx接收，然后转发到`http://target-server.com`，从而绕过浏览器的同源策略。

- 这就是使用Nginx反向代理解决跨域问题的基本步骤。需要注意的是，这种方法需要你有一个可以访问的公网服务器。

![image-20230806000322756](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230806000322756.png)

### webpack-mode属性的设置和代表含义

![image-20230806141652165](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230806141652165.png)

1. 首先`npm init`初始化一个package.json配置文件出来
2. 然后继续安装对应的配置：`npm install webpack webpack-cli -D`，-D == dev(开发时依赖)
3. 创建webpack的配置文件：**webpack.config.js**，这个文件名称是固定的，webpack会自动找到这个文件

> 我们在**webpack.config.js**文件中是使用**module.export**进行导出配置的，为什么是使用CommonJS的方式？而不是基于ES6的模块化导出呢？
>
> - 这是因为webpack是基于node的，而node支持使用的模块化导入导出就是CommonJS方案
> - 而像main.ts这个全局文件是作用于浏览器的，所以通常情况下才会使用ES6的模块化import

这个配置文件定义了webpack的入口和输出选项：

- `entry`字段指定了应用程序的入口点。webpack将从这个文件开始，然后构建其依赖关系图。
- `output`字段描述了如何处理打包后的代码。`path`选项定义了输出文件的存放目录，`filename`选项定义了输出文件的名称。

```javascript
//webpack.config.js配置信息

// 引入Node.js的path模块，用于处理文件和目录的路径
const path = require("path");

module.exports = {
  // 指定入口文件，告诉webpack从哪个文件开始构建
  // 在这个例子中，入口文件是"./src/main.js"
  entry: "./src/main.js",

  // 输出配置，告诉webpack如何编写编译后的文件
  output: {
    // 使用path.resolve拼接路径，__dirname是当前文件所在的绝对路径
    // 这里配置的是编译后文件的存放目录
    path: path.resolve(__dirname, './build'),

    // 编译后的文件名，这里是"bundle.js"
    filename: 'bundle.js'
  }
}
```

- 并且因为我们是局部-D安装的webpack，所以我们如果想要真正启动webpack的打包指令，不能通过webpack xxx直接开始

- 而是需要通过npx webpack进行一个打包

  > `npx`是一个在Node.js v8.2.0及更高版本与`npm`一同发布的命令行工具。它有许多有用的特性，但主要的作用是执行在本地项目中安装的命令行工具或其他可执行文件。
  >
  > 以下是`npx`的一些主要用途和特点：
  >
  > 1. **执行本地安装的包**：通常，全局安装的命令行工具可以直接在命令行中运行，但本地安装的工具不能。`npx`允许你轻松地运行在项目的`node_modules/.bin`目录中的命令。
  > 2. **避免全局安装**：有时，你可能只想临时运行一个包，而不是全局安装它。`npx`可以直接从npm仓库运行包，无需全局安装。
  > 3. **运行不同版本的工具**：如果你需要测试不同版本的一个包，`npx`可以让你指定要运行的版本，而不必卸载和重新安装不同版本。
  > 4. **执行GitHub gist和仓库**：`npx`还可以直接从GitHub gist或仓库运行代码，这对于快速测试和原型开发非常有用。
  > 5. **更容易的命令调用**：对于具有复杂命令行接口的工具，`npx`可以使调用更加直观和方便。
  > 6. **自动使用最新版本**：当直接从npm仓库运行包时，`npx`将总是使用最新版本，确保你总是使用最新的代码和特性。

![image-20230806135837165](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230806135837165.png)

#### webpack基础打包回顾

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  // mode: 配置webpack的打包模式，有两个选项：'development'和'production'
  // 'development'会启用一些开发环境的优化，如更快的构建速度和更详细的错误日志
  // 'production'会启用生产环境的优化，如代码压缩和性能优化
  mode: 'development',

  // entry: 定义入口文件，告诉webpack从哪里开始构建依赖图
  entry: './src/index.js',

  // output: 配置输出选项，告诉webpack如何编写编译后的文件
  output: {
    // path: 输出文件的存放目录
    path: path.resolve(__dirname, 'dist'),
    // filename: 输出文件的名称
    filename: 'bundle.js'
  },

  // devServer: 配置webpack开发服务器，提供了一个简单的web服务器和实时重新加载功能
  devServer: {
    // contentBase: 服务器从哪个目录提供内容
    contentBase: path.join(__dirname, 'dist'),
    // port: 服务器运行的端口
    port: 8080
  },

  // resolve: 配置如何解析模块
  resolve: {
    // extensions: 自动解析确定的扩展名，使你能够在引入模块时不带扩展名
    extensions: ['.js', '.jsx']
  },

  // module.rules: 定义了一组规则，告诉webpack如何处理不同类型的模块
  module: {
    rules: [
      // 每个规则可以配置一个loader，用于转换匹配的文件
      {
        // test: 匹配文件的正则表达式
        test: /\.css$/,
        // use: 使用的loader
        use: ['style-loader', 'css-loader']
      }
    ]
  },

  // plugins: 插件列表，插件可以执行范围更广的任务，如打包优化、资源管理和注入环境变量
  plugins: [
    // HtmlWebpackPlugin会自动生成一个HTML文件，并将所有的入口包含进去
    new HtmlWebpackPlugin({
      template: './src/index.html'
    })
  ]
};
```

#### Mode配置

- **Mode配置选项，可以告知webpack使用相应模式的内置优化：**

  - 默认值是production（什么都不设置的情况下）
  - 可选值有：'none' | 'development' | 'production'

- **这几个选项有什么样的区别呢?**

  - **`'production'`（生产模式）：**
    - **优化：**此模式下，webpack会自动启用许多用于生产环境的优化，如代码压缩、摇树优化（移除未使用的代码）、性能优化等。
    - **调试：**错误信息和日志会更精简，不包括大量的调试信息，以减小构建后的文件大小。
    - **性能：**构建速度可能会慢一些，因为它执行了许多额外的优化步骤来减小文件大小和提高运行时性能。
    - **用途：**适用于生产环境，即实际用户使用的环境。
  - **`'development'`（开发模式）：**
    - **优化：**不会进行过多的优化，主要关注构建速度和调试友好。
    - **调试：**提供完整的错误信息和详细日志，方便开发人员调试和定位问题。
    - **性能：**构建速度通常更快，因为它不执行耗时的优化步骤。
    - **用途：**适用于开发环境，即开发人员编写和测试代码的环境。
  - **`'none'`：**
    - **优化：**不启用任何优化，提供最原始、未经修改的构建输出。
    - **调试：**不会更改或干预你的代码，可能包括所有源代码和详细错误信息。
    - **性能：**不执行任何特定的性能优化。
    - **用途：**这个模式很少使用，但可能对于某些特殊情况或调试webpack本身有用。

  > 总的来说，`production`模式用于构建用于实际用户的优化过的代码，`development`模式用于开发和调试，而`none`模式提供了一个没有任何默认优化的中间地带。在开发过程中，通常会在本地开发环境使用`development`模式，并在构建用于生产的代码时使用`production`模式。

![image-20230806142119462](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230806142119462.png)

- 这个**为模块和chunk启动有效的名和混淆名**是什么意思？
  - 这个其实就是在生产环境的时候，会把你文件的名称随机乱造出名字来，防止别人通过这个文件名就猜测出你这个文件的作用
  - chunk则是进行分包处理

#### Mode配置代表更多

- 一个development开发环境的mode配置(绿色一行)就相当于设置了下面巨多行的红色部分的代码

![image-20230806143101650](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230806143101650.png)

- production生产环境中mode的设置也是一行代表下面多行的红色代码
  - 红色部分内具体是什么意思后面进行讲解

![image-20230806143132544](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230806143132544.png)

### webpack-source-map文件的作用

- 我们的代码通常运行在浏览器上时，是通过**打包压缩**的：

- - 也就是**真实跑在浏览器上的代码**，和**我们编写的代码其实是有差异的**
  - 比如**ES6的代码**可能被**转换成ES5**
  - 比如对应的**代码行号、列号**在经过编译后肯定会不一致
  - 比如代码进行**丑化压缩**时，会将**编码名称**等修改
  - 比如我们使用了**TypeScript**等方式编写的代码，最终转换成**JavaScript**
- 但是，当代码报错需要**调试时（debug）**，调试**转换后的代码是很困难的**
- 但是我们能保证代码不出错吗？`不可能`
- 那么如何可以**调试这种转换后不一致**的代码呢？答案就是**source-map**
  - source-map是从**已转换的代码**，映射到**原始的源文件**
  - 使浏览器可以**重构原始源(原来的源码)**并在调试器中**显示重建的原始源**

> 我们打包在webpack配置文件中设置mode为development(开发模式)的代码的时候，打包结果会多出很多内容，这些代码的作用就是用来提高开发效率和调试方便的![image-20230811191641511](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230811191641511.png)

#### 如何使用source-map

> 在这之前，我们在package.json这个配置文件中配置打包的快捷命令：
>
> - 这样就能够通过`npm run build` 来替代 `npx webpack`
>
> ```js
> 	"scripts": {
> 		"build":"webpack"
> 	}
> ```

在我们运行打包后的代码会有一个神奇的事情，那就是当我们代码报错的时候，浏览器控制台显示的错误位置不是我们打包后的错误位置，而是我们打包压缩前的代码位置

- 这个“神奇的事情”其实是**源代码映射（Source Maps）**在起作用。在开发模式下，Webpack使用源代码映射来帮助开发者定位到错误和警告的原始代码位置。下面我详细解释一下这个过程。

- ### 1. 什么是源代码映射（Source Maps）

  源代码映射是一个映射关系，它连接了转换后的代码和原始代码之间的位置信息。当代码被压缩、打包或通过编译器（如Babel）转换时，源代码映射允许开发者在浏览器中查看和调试原始代码，而不是转换后的代码。

  ### 2. Webpack中的源代码映射

  Webpack提供了多种源代码映射选项，可以通过`devtool`配置属性来设置。例如，以下配置会生成完整的源代码映射：

  ```js
  module.exports = {
    devtool: 'source-map'
  };
  ```

  这样，当你在浏览器的开发者工具中查看代码或遇到错误时，你会看到原始代码的位置，而不是打包后的代码位置。

  ![image-20230811194506651](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230811194506651.png)

  ### 3. 如何工作

  源代码映射通过一个特殊的映射文件来工作，该文件包含了转换后的代码与原始代码之间的位置关系。这个映射文件通常具有`.map`扩展名，并与转换后的文件关联。

  - 如何关联起来的？通过打包文件的最后一行注释进行关联(会写明所关联的映射文件名，这个注释是自动生成的，也就是**在转换后的代码，最后添加一个注释，它指向sourcemap**)，会被浏览器所解析

  ![image-20230811194812990](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230811194812990.png)

  当浏览器的开发者工具打开时，它会读取映射文件，并使用该映射关系来显示原始代码位置。这样，即使代码被压缩或转换，开发者依然可以方便地调试和理解代码。

  - **在Chrome中，我们可以按照如下的方式打开source-map：**

    一般情况下，默认是开启的

  ![image-20230811195348372](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230811195348372.png)

  CSS对应的source-map也是可以开启的

  ![image-20230811195433026](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230811195433026.png)

  ### 4. 开发和生产环境

  在开发环境中，源代码映射非常有用，因为它们允许开发者直接查看和调试原始代码。然而，在生产环境中，出于性能和安全考虑，通常不会生成或使用源代码映射。

  ### 总结

  Webpack中的源代码映射提供了一种强大的开发工具，使开发者能够在浏览器中直接查看和调试原始代码。这解释了为什么在开发模式下，浏览器控制台显示的错误位置是打包压缩前的代码位置，而不是打包后的位置。

### webpack - source-map文件的属性

- 最初source-map生成的文件大小是原始文件的10倍，第二版减少了约50%，第三版又减少了50%，所以目前一个133kb的文件，最终的source-map的大小大概在300kb

- **目前的source-map长什么样子呢？**

  > 里面的属性一般情况下也不需要去进行改动，所以不需要特地去记，了解就行
  >
  > ![image-20230811195802344](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230811195802344.png)

  ### 1. `version`

  - **作用**：表示源代码映射的版本。
  - **示例**：`"version": 3` 表示遵循第3版的源代码映射规范。

  ### 2. `file`

  - **作用**：生成的文件的名称，也就是打包后的文件（浏览器加载的文件）。
  - **示例**：`"file": "main.js"` 表示生成的文件名为`main.js`。

  ### 3. `sourceRoot`

  - **作用**：所有源文件的根URL，用作源文件URL的前缀。
  - **示例**：如果设置为`"sourceRoot": "/src"`, 则源文件`/src/example.js`将被映射为`example.js`。

  ### 4. `sources`

  - **作用**：包含源代码映射所涉及的所有原始文件的路径数组，从哪些文件转换过来的source-map和打包的代码（最初始的文件）。
  - **示例**：`"sources": ["example.js", "test.js"]` 表示涉及`example.js`和`test.js`这两个源文件。

  ### 5. `names`

  - **作用**：包含映射中涉及的所有符号名称的数组，如变量和函数名，因为压缩的过程中会把我们取的有意义的变量名进行压缩到只有一个字母，而这个的作用就是后期将压缩成字母的变量名进行还原。
  - **示例**：`"names": ["myFunction", "myVariable"]`。

  ### 6. `sourcesContent`

  - **作用**：包含源文件内容的数组。它允许源映射包括源代码，这样即使没有访问原始文件，也可以查看和调试源代码，转换前的具体代码信息（和sources是对应的关系）。
  - **示例**：`"sourcesContent": ["console.log('hello')", "alert('test')"]`。

  ### 7. `mappings`

  - **作用**：这是源代码映射的核心部分，包含了生成代码和源代码之间的位置映射关系。它使用一种称为VLQ（Variable Length Quantity）的编码格式。
  - **示例**：一个复杂的字符串，如`"mappings": "AAAA;AACA;;ACTY"`。

- 我们从哪里看到映射后的源代码？
  - 下面这个是打包压缩后的代码，很难看出问题

![image-20230812174130774](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230812174130774.png)

- 被source-map还原出来的代码：

![image-20230812174238195](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230812174238195.png)

### webpack-source-map常见的几个选项

![image-20230812174658919](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230812174658919.png)

#### 生成source-map

- **如何在使用webpack打包的时候，生成对应的source-map呢？**

  - webpack为我们提供了非常多的选项（目前是26个），来处理source-map；
  - https://webpack.docschina.org/configuration/devtool/
  - 选择不同的值，生成的source-map会稍微有差异，打包的过程也会有**性能的差异**，可以根据不同的情况进行选择

- **下面几个值不会生成source-map**

  - `false`：不使用source-map，也就是没有任何和source-map相关的内容

  - `none`：production模式下的默认值（什么值都不写） ，不生成source-map

  - `eval`：development模式下的默认值，不生成source-map

    - 但是它会在eval执行的代码中，添加 //# sourceURL=；

    - 它会被浏览器在执行时解析，并且在调试面板中生成对应的一些文件目录，方便我们调试代码

    - 这个虽然也没有source-map，但是也能够一定程度上的还原代码。这是怎么做到的？

      这是通过打包出去的代码中的大量注释实现的，并且这个方式虽然可以实现，但是**代码的报错位置和提示位置就会不再那么的准确**，但是eval的编译速度是很快的，这个是他的优势。根据不同的情况进行选择

    ![image-20230812175432988](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230812175432988.png)

#### eval的效果

![image-20230812180042965](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230812180042965.png)

![image-20230812180046077](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230812180046077.png)

### webpack-source-map不常见的值

> 下面这些最终都是devtool配置里的选项

#### eval-source-map值

- `eval-source-map`：会生成sourcemap，但是source-map是**以DataUrl添加到eval函数的后面**

![image-20230813172356663](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230813172356663.png)

#### inline-source-map值

- `inline-source-map`：会生成sourcemap，但是source-map是以`DataUrl`添加到bundle文件的后面

![image-20230813181124737](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230813181124737.png)

#### cheap-source-map

- **cheap-source-map：**
  - 会生成sourcemap，但是会更加高效一些（cheap低开销），因为它没有生成列映射（Column Mapping），也就是如果报错只会告诉你第几行报错，而不会告诉你第几行中的第几个字符出的问题。这样在打包文件的时候，开销会更低一些
  - 因为在开发中，我们只需要行信息通常就可以定位到错误了。这个只能用在development开发环境下

![image-20230813181643069](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230813181643069.png)

#### cheap-module-source-map值

- **cheap-module-source-map：**

  - 会生成sourcemap，类似于cheap-source-map，但是对**源自loader的sourcemap处理会更好**

- **这里有一个很模糊的概念**：对源自loader的sourcemap处理会更好，官方也没有给出很好的解释

  - 其实是**如果loader对我们的源码进行了特殊的处理，**比如babel

  > 确实，Webpack 的文档中对于 "cheap-module-source-map" 和 "对源自 loader 的 sourcemap 处理会更好" 这样的表述可能让人感到困惑。让我们尝试深入了解其中的含义。
  >
  > ### 1. `cheap-` 前缀
  >
  > 在 Webpack 的 `devtool` 选项中，"cheap" 前缀表示生成的 source map 仅包括行信息，不包括列信息。换句话说，它可以告诉你错误在哪一行，但不会告诉你具体是哪一列。这样做的好处是生成 source map 的速度更快，文件更小，但缺点是定位错误可能不够精确。
  >
  > ### 2. `-module` 后缀
  >
  > 现在我们来关注 "cheap-module-source-map" 中的 "module" 部分。与 "cheap-source-map" 相比，"module" 表示 source map 会包括 loader 的 sourcemap。
  >
  > 在 Webpack 中，loader 负责将资源（如 TypeScript、Sass 等）转换为 JavaScript。这些转换可能涉及许多中间步骤，每个步骤都可能有自己的 source map。例如，你可能使用 Babel loader 转换现代 JavaScript 语法，然后使用 Terser loader 进行压缩。
  >
  > "对源自 loader 的 sourcemap 处理会更好" 的意思是 "cheap-module-source-map" 会尝试合并这些中间步骤的 source map，以便你可以在浏览器的开发者工具中查看原始源代码，而不仅仅是转换后的 JavaScript。
  >
  > ### 3. 为什么选择 "cheap-module-source-map"
  >
  > 使用 "cheap-module-source-map" 通常是在希望快速生成 source map，并同时保留来自 loader 转换过程的源代码信息时的一个折衷方案。这可能在开发环境中很有用，因为它提供了比 "cheap-source-map" 更丰富的信息，但生成速度仍然相对较快。
  >
  > ### 总结
  >
  > "cheap-module-source-map" 是一种特定类型的 source map，旨在提供行级别的映射，并包括来自 loader 转换过程的源代码信息。这使得开发人员能够在调试时查看原始源代码，同时保持构建性能的合理水平。

- 如果我这里使用了babel-loader

  ```js
  rules:[
    {
      test:/\.js$/,
      use:{
        loader:"babel-loader",
        options:{
          presets:[
            ['@babel/preset-env',{targets:"defaults"}]
          ]
        }
      }
    }
  ]
  ```

#### cheap-source-map和cheap-module-source-map

- **cheap-source-map和cheap-module-source-map**的区别：
  - 通过对比，我们能够看到经过优化，处理得更好体现在空格换行还有代码的语法转化兼容上面

![image-20230813220649612](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230813220649612.png)

![image-20230813220653944](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230813220653944.png)

#### hidden-source-map值

- **hidden-source-map：**

  - 会生成sourcemap，但是**不会对source-map文件进行引用**
  - 相当于**删除了打包文件中对sourcemap的引用注释**

  ![image-20230813221545140](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230813221545140.png)

- **如果我们手动添加进来，那么sourcemap就会生效了**

#### nosources-source-map值

- **nosources-source-map：**
  - 会生成sourcemap，但是生成的sourcemap**只有错误信息的提示，不会生成源代码文件**，如果我们不需要从浏览器中看源文件的报错位置的话就可以选择这个
- 正确的错误提示：

![image-20230813221709623](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230813221709623.png)

- 点击错误提示，无法查看源码：

![image-20230813221720381](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230813221720381.png)

#### 多个值的组合

- **事实上，webpack提供给我们的26个值，是可以进行多组合的。**

- **组合的规则如下：**

  - inline-|hidden-|eval：三个值时三选一
  - nosources：可选值
  - cheap可选值，并且可以跟随module的值

  ![image-20230813222033368](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230813222033368.png)

- **那么在开发中，最佳的实践是什么呢？**

  - **开发阶段**：推荐使用 source-map或者**cheap-module-source-map**

    ✓ 这分别是vue和react使用的值，可以获取调试信息，方便快速开发

  - **测试阶段**：推荐使用 source-map或者cheap-module-source-map

    ✓ 测试阶段我们也希望在浏览器下看到正确的错误提示

  - **发布阶段**：false、缺省值（不写）

### babel-babel在前端的地位和作用

#### 为什么需要babel？

- **事实上，在开发中我们很少直接去接触babel，但是babel对于前端开发来说，目前是不可缺少的一部分：**
  - 开发中，我们想要使用ES6+的语法，想要使用TypeScript，开发React项目，它们都是离不开Babel的
  - 我们平时使用的脚手架webpack和vite都已经内置babel了
  - 所以，学习Babel对于我们理解代码从编写到线上的转变过程直观重要
  - 了解真相，你才能获得真知的自由！
- **那么，Babel到底是什么呢？**
  - Babel是一个工具链，主要用于旧浏览器或者缓解中将ECMAScript 2015+代码转换为向后兼容版本的JavaScript
  - 包括：语法转换、源代码转换、Polyfill实现目标环境缺少的功能等，例如下面将ES6中的箭头函数转化成普通的函数

![image-20230813225910962](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230813225910962.png)

### babel-babel命令行-插件的使用

#### Babel命令行使用

- **babel本身可以作为一个独立的工具（和postcss一样），不和webpack等构建工具配置来单独使用。**

- **如果我们希望在命令行尝试使用babel，需要安装如下库：**

  - **@babel/core**：babel的核心代码，必须安装
  - **@babel/cli**：可以让我们在命令行使用babel

  ```js
  npm install @babel/cli @babel/core -D
  ```

- **使用babel来处理我们的源代码：**

  - `src`：是源文件的目录
  - `--out-dir`：指定要输出的文件夹dist

  ```js
  npx babel src --out-dir dist
  ```

  #### 插件的使用

- **比如我们需要转换箭头函数，那么我们就可以使用箭头函数转换相关的插件：**

```js
npm install @babel/plugin-transform-arrow-functions -D
npx babel src --out-dir dist --plugins=@babel/plugin-transform-arrow-functions
```

- **查看转换后的结果：我们会发现 const 并没有转成 var**

  - 这是因为 plugin-transform-arrow-functions，并没有提供这样的功能，babel还得找对应转化功能的插件
  - 我们需要使用 plugin-transform-block-scoping 来完成这样的功能，通过在--plugins后面不断添加，然后以逗号作为分割

  ```js
  npm install @babel/plugin-transform-block-scoping -D
  npx babel src --out-dir dist --plugins=@babel/plugin-transform-block-scoping,@babel/plugin-transform-arrow-functions
  ```

### babel-babel命令行-预设的使用

#### Babel的预设preset

Babel 是一个广泛使用的 JavaScript 编译器，允许开发人员使用最新的 ECMAScript 特性，同时确保代码能在旧版本的浏览器和环境中运行。Babel 通过插件来实现各种转换，而预设（preset）是一组有特定目的的插件集合。

让我们详细了解一下 Babel 的预设：

#### 1. **预设的概念**

预设基本上是一组预先配置的 Babel 插件。这些插件通常是针对特定的使用场景或环境组合在一起的，以便开发人员不必手动选择和配置每个所需插件。

#### 2. **常见的预设**

以下是一些常用的 Babel 预设：

- **@babel/preset-env**：这是最常用的预设之一。它根据你的目标浏览器或运行环境自动确定所需的 Babel 插件和 polyfills。你可以指定目标浏览器的版本，它会自动包括所需的转换。

  ```js
  //安装
  npm install @babel/preset-env -D
  //执行命令
  npx babel src --out-dir dist --presets=@babel/preset-env
  ```

- **@babel/preset-react**：用于转换 React JSX 语法。如果你正在开发 React 应用程序，你可能需要此预设。

- **@babel/preset-typescript**：允许你使用 TypeScript，并在 Babel 中转换 TypeScript 代码。

- **@babel/preset-flow**：用于支持 Flow 静态类型检查语言。

#### 3. **自定义预设**

除了使用现有预设外，你还可以创建自定义预设。这在组织内共享 Babel 配置时特别有用。自定义预设允许你将一组特定的插件和配置打包在一起，以便在多个项目之间重用。

#### 4. **预设的使用**

在 Babel 配置文件（例如 `.babelrc` 或 `babel.config.js`）中，你可以指定要使用的预设。例如，如果你想使用 `@babel/preset-env` 和 `@babel/preset-react`，你的配置可能如下所示：

```js
{
  "presets": ["@babel/preset-env", "@babel/preset-react"]
}
```

#### 5. **预设的顺序**

预设的顺序很重要。在配置文件中，预设从上到下的顺序执行。有时，正确的顺序对于确保代码正确转换至关重要。

> Babel 预设简化了设置和管理编译 JavaScript 代码所需的插件和配置的过程。通过使用预设，开发人员可以方便地针对特定的目标环境或使用场景编译代码，无需手动管理每个插件。此外，自定义预设还提供了在多个项目中共享配置的能力，从而进一步提高开发效率。

### Babel-babel的底层编译代码原理

#### Babel的底层原理

- **babel是如何做到将我们的一段代码（ES6、TypeScript、React）转成另外一段代码（ES5）的呢？**

  - 从一种**源代码（原生语言）**转换成**另一种源代码（目标语言）**，比如将JS代码转化成C语言代码，这是什么的工作呢？
  - 就是**编译器**，事实上我们可以**将babel看成就是一个编译器**
  - Babel编译器的作用就是将**我们的源代码**，转换成浏览器可以直接识别的**另外一段源代码**

- **Babel也拥有编译器的工作流程：**

  - 解析阶段（Parsing）
  - 转换阶段（Transformation）
  - 生成阶段（Code Generation）

  > ### 1. **解析阶段（Parsing）**
  >
  > 解析阶段的主要任务是读取源代码并将其转换为抽象语法树（AST），更详细的内容建议去看《你不知道的JavaScript上卷》。这个过程可以分为两个子阶段：
  >
  > - **词法分析**：首先，Babel 会将源代码分解为称为“tokens”的最小单位。这些 tokens 表示语言的语法结构，例如标识符、关键字和符号。
  > - **语法分析**：然后，Babel 会使用这些 tokens 构建一个称为抽象语法树（AST）的结构。AST 是源代码的层次结构表示，其中每个节点表示代码中的特定结构。
  >
  > ### 2. **转换阶段（Transformation）**
  >
  > 一旦 AST 被创建，转换阶段就开始了。在这个阶段，Babel 可以执行多个不同的操作来转换或操纵 AST。这包括：
  >
  > - **遍历**：Babel 遍历 AST 的每个节点，查找可以应用转换的节点。
  > - **应用插件和预设**：Babel 应用定义的插件和预设来转换 AST。这可能包括添加、删除或更改节点。
  > - **构造新的 AST**：通过应用插件和预设所定义的转换，Babel 构建一个新的、修改后的 AST。这个新的 AST 代表了目标语言的代码结构。
  >
  > ### 3. **生成阶段（Code Generation）**
  >
  > 在生成阶段，Babel 接收修改后的 AST 并将其转换回源代码。这个过程通常包括：
  >
  > - **序列化 AST**：Babel 遍历修改后的 AST，并将每个节点转换为相应的代码字符串。
  > - **源映射**：如果启用了源映射，Babel 还会生成一个映射文件，该文件描述原始源代码与生成代码之间的对应关系。这有助于在调试时查看原始代码。

- [小型编译器--GitHub代码](https://github.com/jamiebuilds/the-super-tiny-compiler)

#### babel编译器执行原理

- **Babel的执行阶段**

![image-20230814000529023](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230814000529023.png)

- **当然，这只是一个简化版的编译器工具流程，在每个阶段又会有自己具体的工作：**

![image-20230814000544964](D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\coderwhy体系课笔记\markdown格式笔记(coderwhy)\Node高级笔记\node_pictures\image-20230814000544964.png)

## 浏览器兼容-polyfill-本地服务-优化

### webpack和babel结合的配置使用

#### 为什么需要babel？

- **事实上，在开发中我们很少直接去接触babel，但是babel对于前端开发来说，目前是不可缺少的一部分：**
  - 开发中，我们想要使用ES6+的语法，想要使用TypeScript，开发React项目，它们都是离不开Babel的
  - 所以，学习Babel对于我们理解代码从编写到线上的转变过程直观重要

- **那么，Babel到底是什么呢？**
  - Babel是一个工具链，主要用于旧浏览器或者缓解中将ECMAScript 2015+代码转换为向后兼容版本的JavaScript
  - 包括：语法转换、源代码转换、Polyfill实现目标环境缺少的功能等

#### Babel命令行使用

- **babel本身可以作为一个独立的工具（和postcss一样），不和webpack等构建工具配置来单独使用**
- **如果我们希望在命令行尝试使用babel，需要安装如下库：**
  - **@babel/core**：babel的核心代码，必须安装
  - **@babel/cli**：可以让我们在命令行使用babel

```javascript
npm install @babel/cli @babel/core
```

- **使用babel来处理我们的源代码：**

  - **src**：是源文件的目录
  - **--out-dir**：指定要输出的文件夹dist

  ```js
  npx babel src --out-dir dist
  ```

#### 插件的使用

- **比如我们需要转换箭头函数，那么我们就可以使用箭头函数转换相关的插件：**

```json
npm install @babel/plugin-transform-arrow-functions -D
```

> 这个命令用于安装一个名为 `@babel/plugin-transform-arrow-functions` 的Babel插件，并将其添加到项目的开发依赖中。
>
> 让我们逐部分解这个命令：
>
> 1. `npm install`: 这是Node.js包管理工具npm的命令，用于安装一个或多个包。
> 2. `@babel/plugin-transform-arrow-functions`: 这是您要安装的具体包的名称。这个Babel插件的目的是将ES6+的箭头函数转换为ES5之前版本的JavaScript，这样老版本的浏览器或环境就可以执行这些代码。
> 3. `-D`：这是一个选项，也可以写作 `--save-dev`。它的意思是将这个包添加到项目的`package.json`文件中的`devDependencies`部分。这意味着这个包主要用于开发目的，而不是在生产环境中运行的代码的实际依赖。
>
> 总的来说，这个命令将帮助我们在开发过程中使用ES6+的箭头函数语法，同时确保代码可以在不支持箭头函数的老版本浏览器中运行。

```json
npx babel src --out-dir dist --plugins=@babel/plugin-transform-arrow-functions
```

> 1. **`npx`**: 这是一个包运行工具，它随Node.js的npm包管理器一起安装。它的目的是使您能够运行本地安装的Node.js二进制文件，或者直接从npm注册中心下载并运行代码。
> 2. **`babel`**: 这是Babel的命令行接口 (CLI)。Babel是一个广泛使用的JavaScript编译器，它可以将新的JavaScript语法（例如ES6、ES7等）转换为旧的版本，以确保它们在老版本的浏览器中可以运行。
> 3. **`src`**: 这指定了要转换的源代码的目录。
> 4. **`--out-dir dist`**: 这指定了转换后的代码应该输出到哪里。在这种情况下，它将输出到一个名为`dist`的目录。
> 5. **`--plugins=@babel/plugin-transform-arrow-functions`**: 这指定了Babel应该使用哪些插件来转换代码。在这里，我们指定了`@babel/plugin-transform-arrow-functions`插件，它会将ES6+的箭头函数转换为ES5版本的JavaScript。
>
> 总的来说，这个命令的作用是：从`src`目录获取ES6+的JavaScript代码（特别是箭头函数），然后使用Babel和指定的插件将其转换为可以在老版本的浏览器中运行的JavaScript代码，并将转换后的代码输出到`dist`目录。

- **查看转换后的结果：我们会发现 const 并没有转成 var**

  - 这是因为 plugin-transform-arrow-functions，并没有提供这样的功能
  - 我们需要使用 plugin-transform-block-scoping 来完成这样的功能

  ```js
  npm install @babel/plugin-transform-block-scoping -D
  ```

  > **`@babel/plugin-transform-block-scoping`**: 这是要安装的具体包的名称。这个Babel插件的主要目的是转换ES6中引入的块级作用域，特别是`let`和`const`声明。在ES5及更早版本的JavaScript中，不存在块级作用域，所以这个插件会将`let`和`const`转换为`var`，同时确保它们在正确的作用域中被引用和更新。

```js
npx babel src --out-dir dist --plugins=@babel/plugin-transform-block-scoping,@babel/plugin-transform-arrow-functions
```

> 1. **`babel`**: 这是Babel的命令行接口 (CLI)。Babel是一个广泛使用的JavaScript编译器，它可以将新的JavaScript语法（例如ES6、ES7等）转换为旧的版本，以确保它们在老版本的浏览器中可以运行。
> 2. **`src`**: 这指定了要转换的源代码的目录。
> 3. **`--out-dir dist`**: 这指定了转换后的代码应该输出到哪里。在这种情况下，它将输出到一个名为`dist`的目录。
> 4. **`--plugins=@babel/plugin-transform-block-scoping,@babel/plugin-transform-arrow-functions`**: 这指定了Babel应该使用哪些插件来转换代码。在这里，我们指定了两个插件：
>    - `@babel/plugin-transform-block-scoping`: 将处理ES6中的块级作用域，特别是`let`和`const`声明。
>    - `@babel/plugin-transform-arrow-functions`: 将处理ES6+的箭头函数。
>
> 总结，这个命令的作用是：从`src`目录中获取使用了ES6+特性（如箭头函数和块级作用域）的JavaScript代码，然后使用Babel和指定的插件将其转换为可以在不支持这些特性的旧版本浏览器中运行的JavaScript代码，并将转换后的代码输出到`dist`目录。

### Babel的预设preset

- **但是如果要转换的内容过多，一个个设置是比较麻烦的，我们可以使用预设（preset）：**

  - 后面我们再具体来讲预设代表的含义

- **安装@babel/preset-env预设：**

  ```js
  npm install @babel/preset-env -D
  ```

- **执行如下命令：**

  ```js
  npx babel src --out-dir dist --presets=@babel/preset-env
  ```

  > `@babel/preset-env`是一个Babel预设，它根据你的目标浏览器或运行环境自动决定需要使用哪些Babel插件和polyfills。这意味着你不需要单独指定每一个语法特性的插件，它会帮你处理。
  >
  > 使用这个预设的好处是，你可以写最新版本的JavaScript，而不用担心旧浏览器的兼容问题。它会根据你的需要进行转换。

- 正常情况下我们不可能一直使用命令行进行babel的转换，这样就太麻烦了，而到目前为止。babel还没有跟webpack产生关联。那我们要如何做，才能够将babel集成进webpack里面呢？