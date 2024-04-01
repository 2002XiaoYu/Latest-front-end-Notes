# Node 第三章-npm Package json

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

## npm

`npm`（全称 Node Package Manager）是 Node.js 的包管理工具，它是一个基于命令行的工具，用于帮助开发者在自己的项目中安装、升级、移除和管理依赖项。

- 查找npm包可以通过官网：[npm | Home (npmjs.com)](https://www.npmjs.com/)

### npm的主要功能包括：

- **包安装**：可以通过命令行工具安装、更新、卸载包。
- **依赖管理**：在项目的`package.json`文件中自动记录项目所依赖的包及其版本，确保开发环境和生产环境的一致性。
- **脚本运行**：可以在`package.json`中定义脚本命令，如启动应用、运行测试等。
- **版本控制**：npm支持语义化版本控制，可以定义包的版本号以指示更新的性质（如主要更新、次要更新或补丁）。
- **包发布**：开发者可以将自己编写的模块发布到npm，供其他人使用。

### npm 常用命令

1. `npm init`：初始化一个新的 npm 项目，创建 package.json 文件。

   - 这里可以注意一下版本号version，在下面的图片中，我有讲解结构是主版本.次版本.补丁，那这么一听其实还是有点玄乎的，那我在这里进行详细解释一下

   | 主版本                                                       | 次版本                                                       | 补丁                                                         |
   | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
   | 当你做了不兼容的 API 修改时，必须增加主版本号(重大升级更新)。比如说，当你的更新改变了之前版本的使用方式，或者是移除了之前版本的某个功能，这时就应该增加主版本号。 | 当你在保持向下兼容的前提下添加了功能时，增加次版本号。也就是说，新增的功能或改动不会破坏现有的API或功能 | 当你做了向下兼容的问题修正时，增加补丁版本号。通常是对bug进行修复时增加此版本号。 |

![image-20240401195402263](node-images\image-20240401195402263.png)

#### 初始化注意事项

- **This utility will walk you through creating a package.json file.** 
  - 这个工具将指导你创建一个 `package.json` 文件。
- **It only covers the most common items, and tries to guess sensible defaults.** 
  - 它只包括最常见的条目，并尝试猜测合理的默认值。
- **See `npm help init` for definitive documentation on these fields and exactly what they do.** 
  - 查看 `npm help init` 可以得到关于这些字段的确切文档以及它们的具体作用。
- **Use `npm install <pkg>` afterwards to install a package and save it as a dependency in the package.json file.** 
  - 之后使用 `npm install <pkg>` 来安装一个包，并将其保存为 `package.json` 文件中的依赖项。
- **Press ^C at any time to quit.** 
  - 你可以在任何时候按 `^C`（即Control + C）退出。

#### npm官网显示信息

- 其中有一些命令是和npm官网是有所串联的，一开始配置的信息会写在发布的包中，如下图的部分

![image-20240401222050623](node-images\image-20240401222050623.png)

2. `npm install`：安装一个包或一组包，并且存放在当前目录的node_modules文件夹中(没有的话则会自动创建)。其中install可以简写为`i`，就像上面的axios的安装命令为`npm i axios`，这就是简写的写法

   - 通常都是默认安装最新的版本，比如下图axios和上图的npm官网中，axios都是显示最新的1.6.8版本

   - 如果我想要安装具体的某个版本，可以使用npm i axios@xxxx就行了，使用@然后加上具体的版本

   - 有安装则也会有卸载，卸载的命令是`npm uninstall 包名`，un在这里表示否定的一层意思

   - 其中npm在安装包的时候，后缀有时候还会加上一点内容

     - 例如npm i axios --save-dev(可以简写为npm i axios -D)，就会生成devDependencies
     - 常用的有如下三个配置：

     1. **dependencies**：

        这些是项目运行时必不可少的包(**开发环境**)。当你发布一个包或者其他人安装你的应用时，这些依赖项都会被安装。例如，如果你正在开发一个Web应用，你可能会依赖`express`框架来处理HTTP请求。

     2. **devDependencies**

        在开发过程中需要，但是在**生产环境**中不会使用的包。这包括测试框架（如`jest`或`mocha`）、构建工具（如`webpack`或`gulp`）、文档生成器、linters（如`eslint`或`tslint`）等。当你在本地开发或运行测试时，这些依赖项很重要，但当你的应用部署到生产环境时，它们并不是必需的。默认情况下，当你执行`npm install`时，`dependencies`和`devDependencies`都会被安装，但是如果你在生产环境下（例如运行`npm install --only=prod`或设置`NODE_ENV=production`环境变量时），`devDependencies`不会被安装。

     3. **peerDependencies**

        用于插件等包的依赖项声明，这些依赖项是必需的，但是由安装你包的用户管理。一个包声明`peerDependencies`时，它实际上说的是：“为了正常工作，我需要这些东西，但我不会自己安装它们”。这样做的好处是避免依赖冲突和重复。例如，如果你创建了一个与React配合使用的组件库，你可以将`react`和`react-dom`列为`peerDependencies`，这样你的库就可以使用用户项目中已有的React实例，而不是打包自己版本的React。这有助于确保插件和宿主之间的兼容性。

![image-20240401223412628](node-images\image-20240401223412628.png)

### 切换与查看npm源

> 在使用npm时，你可能会因为各种原因需要切换npm的源（registry）。例如，一些源可能提供更快的下载速度，或者你可能需要访问一些特定的私有包。
>
> - 默认的源，其实是国外的，所以我们在国内的运行速度有可能会有点慢或者干脆就直接安装失败

#### 查看当前npm源

```js
npm config get registry//这将显示当前设置的npm源的URL
```

![image-20240401225152235](node-images\image-20240401225152235.png)

#### 切换npm源

1. **通过命令行直接设置**

你可以直接使用`npm config set registry`命令来切换npm源。例如，要切换到淘宝的npm镜像，可以运行：

```npm
npm config set registry https://registry.npmmirror.com/
```

2. **使用npm命令临时切换**

如果你只是想临时切换源进行一次安装，不想更改全局配置，你可以在安装时添加`--registry`标志。例如：

```npm
npm install <package_name> --registry=https://registry.npmmirror.com/
```

3.  **替代nrm包的新一代包：xmzs**

- xmzs 是一个基于 Node.js 的命令行工具，它提供了与 nrm 类似的功能，可以方便地切换不同的 npm registry

- 地址：[xmzs - npm (npmjs.com)](https://www.npmjs.com/package/xmzs)

- 使用说明：(他的命令是`mmp`，一个很社会的命令)

  1. 安装：

     ```javascript
     npm i xmzs -g
     ```

  2. ##### mmp ls 查看目前源：

     | npm       | https://registry.npmjs.org/            |
     | --------- | -------------------------------------- |
     | yarn      | https://registry.yarnpkg.com/          |
     | tencent   | https://mirrors.cloud.tencent.com/npm/ |
     | cnpm      | https://r.cnpmjs.org/                  |
     | taobao    | https://registry.npmmirror.com/        |
     | npmMirror | https://skimdb.npmjs.com/registry/     |

  3. 用法详解表格：

     | mmp命令集   | 作用                                       |
     | ----------- | ------------------------------------------ |
     | mmp use     | 切换源：选择你要切换的源                   |
     | mmp current | 查看当前源：当前源: npm                    |
     | mmp add     | 添加源：1.输入添加的地址 2.输入源地址      |
     | mmp ping    | 测试源：? 请选择镜像 cnpm 响应时长: 1635ms |
     | mmp delete  | 删除自定义源：add添加的源都可以删除        |
     | mmp rename  | 删除自定义源：add添加的源都可以删除        |
     | mmp edit    | 编辑自定义镜像地址                         |

### npm的一些其他命令

`npm install <package-name> --save`：安装指定的包，并将其添加到 package.json 文件中的依赖列表中。

`npm install <package-name> --save-dev`：安装指定的包，并将其添加到 package.json 文件中的开发依赖列表中。

`npm install -g <package-name>`：全局安装指定的包。

`npm update <package-name>`：更新指定的包。

`npm uninstall <package-name>`：卸载指定的包。

`npm run <script-name>`：执行 package.json 文件中定义的脚本命令。

`npm search <keyword>`：搜索 npm 库中包含指定关键字的包。

`npm info <package-name>`：查看指定包的详细信息。

`npm list`：列出当前项目中安装的所有包。

`npm outdated`：列出当前项目中需要更新的包。

`npm audit`：检查当前项目中的依赖项是否存在安全漏洞。

`npm publish`：发布自己开发的包到 npm 库中。

`npm login`：登录到 npm 账户。

`npm logout`：注销当前 npm 账户。

`npm link`: 将本地模块链接到全局的 `node_modules` 目录下

`npm config list` 用于列出所有的 npm 配置信息。执行该命令可以查看当前系统和用户级别的所有 npm 配置信息，以及当前项目的配置信息（如果在项目目录下执行该命令）

`npm get registry` 用于获取当前 npm 配置中的 registry 配置项的值。registry 配置项用于指定 npm 包的下载地址，如果未指定，则默认使用 npm 官方的包注册表地址

`npm set registry` `npm config set registry <registry-url>` 命令，将 registry 配置项的值修改为指定的 `<registry-url>` 地址

## Package json的常用配置项

- 其中一部分在init初始化package.json文件的时候，就会自动生成了，在上面的图中已经详细写出

1. **name**: 项目的名称。这个名称必须是唯一的，因为如果你要将你的包发布到 npm，它将用作包的唯一标识符。

2. **version**: 项目的当前版本。版本号应该遵循 [语义化版本控制](https://semver.org/) 的约定。

3. **description**: 一个简短的描述，说明项目或模块是做什么的。

4. **main**: 指定模块的入口文件，Node.js 将会加载这个文件当模块被 `require` 调用。

5. **scripts**: 定义了一系列可以运行的脚本命令。常用的脚本命令包括 `start`或者`dev`（启动应用），`test`（运行测试），以及其他自定义脚本。

   ```js
   //大概就是如下的格式，使用npm运行一串命令
   npm run dev
   npm run start
   npm run xxx
   ```

6. **repository**: 指定项目的源代码仓库，通常是一个 Git 仓库。

7. **keywords**: 一个由关键词组成的数组，有助于用户在 npm 搜索引擎中找到你的项目。

8. **author**: 项目的作者的信息。这可以是一个人的名字也可以包括联系信息如电子邮件地址。

9. **license**: 项目的许可证。这指定了其他人可以如何使用该项目的代码。

10. **dependencies**: 项目运行所需的 npm 包列表。当你通过 `npm install <package_name>` 安装依赖时，它们会自动被添加到这个列表中。

11. **devDependencies**: 项目开发所需的 npm 包列表，这些依赖不会被包含在生产环境中。

12. **peerDependencies**: 指定宿主项目需要遵循的依赖。当你的包被安装时，npm 会提示宿主项目的开发者也安装这些依赖。

13. **bundledDependencies**: 这是一个数组，其中包含了项目依赖的包名称，这些依赖将一同打包发布。

14. **private**: 如果设置为 `true`，将防止项目被意外发布到 npm。

15. **engines**: 指定你的项目需要的 Node.js 版本。