# Node高级笔记

> ### 作者：小余同学
>
> 需要全系列笔记请到[2002XiaoYu (小余) (github.com)](https://github.com/2002XiaoYu)中自行获取，觉得不错给个star，这是对作者非常大的鼓励

## Node中fs-event-buffer等常见模块

### Node.js是什么

- **官方对Node.js的定义：**

  - Node.js是一个基于**V8 JavaScript引擎**的**JavaScript运行时环境**

  ![image-20230717102729017](.\node_pictures\image-20230717102729017.png)

- **也就是说Node.js基于V8引擎来执行JavaScript的代码，但是不仅仅只有V8引擎：**

  - 前面我们知道**V8可以嵌入到任何C ++应用程序**中，无论是**Chrome还是Node.js**，事实上都是**嵌入了V8引擎来执行JavaScript代码**
  - 但是在Chrome浏览器中，还需要**解析、渲染HTML、CSS等相关渲染引擎**，另外还需要提供**支持浏览器操作的API、浏览器自己的事件循环**等
  - 另外，在Node.js中我们也需要进行一些额外的操作，比如**文件系统读/写、网络IO、加密、压缩解压文件**等操作

### 浏览器和Node.js架构区别

- **我们可以简单理解规划出Node.js和浏览器的差异：**

![image-20230717102901171](.\node_pictures\image-20230717102901171.png)

### Node.js架构

- **我们来看一个单独的Node.js的架构图：**
  - 我们编写的JavaScript代码会经过V8引擎，再通过Node.js的Bindings，将任务放到Libuv的事件循环中
  - **libuv**（Unicorn Velociraptor—独角伶盗龙）是**使用C语言编写**的库
  - libuv提供了**事件循环、文件系统读写、网络IO、线程池**等等内容

![image-20230718133930375](.\node_pictures\image-20230718133930375.png)

### 内置模块fs

- **fs是File System的缩写，表示文件系统**
- **对于任何一个为服务器端服务的语言或者框架通常都会有自己的文件系统：**
  - 因为**服务器需要将各种数据、文件**等放置到不同的地方
  - 比如**用户数据可能大多数是放到数据库中**的（后面我们也会学习）
  - 比如**某些配置文件或者用户资源（图片、音视频）都是以文件的形式存在于操作系统上**的
- **Node也有自己的文件系统操作模块，就是fs：**
  - **借助于Node帮我们封装的文件系统**，我们**可以在任何的操作系统（window、Mac OS、Linux）上面直接去操作文件**
  - 这也是**Node可以开发服务器的一大原因**，也是它**可以成为前端自动化脚本等热门工具的原因；**

### fs的API介绍

- **Node文件系统的API非常的多：**
  - [Node的API官方查阅文档](https://nodejs.org/docs/latest-v16.x/api/fs.html)
  - 我们不可能，也没必要一个个去学习
  - 这个更多的应该是作为一个API查询的手册，等用到的时候查询即可
- **这些API大多数都提供三种操作方式：**
  - 方式一：同步操作文件：代码会被阻塞，不会继续执行
  - 方式二：异步回调函数操作文件：代码不会被阻塞，需要传入回调函数，当获取到结果时，回调函数被执行
  - 方式三：异步Promise操作文件：代码不会被阻塞，通过 fs.promises 调用方法操作，会返回一个Promise，可以通过then、catch进行处理

### Node模块-fs文件读取的三种方式

- #### 案例：获取一个文件的状态

- 我们这里以获取一个文件的状态为例：

> 方式一：同步读取文件

```js
// 引入fs模块
const fs = require('fs');

try {
    // 使用fs.readFileSync同步获取文件状态
    // 'path/to/your/file' 是你要获取状态的文件的路径
    const stats = fs.readFileSync('path/to/your/file',{
      encoding:'utf8'//以什么编码格式进行读取文件
    });
    
    // 打印文件状态信息
    console.log(stats);
} catch (error) {
    // 如果发生错误，打印错误信息
    console.error('An error occurred:', error);
}
```

> 方式二：异步回调函数读取文件

```js
// 引入fs模块
const fs = require('fs');

// 使用fs.stat异步获取文件状态，结果通过回调函数返回
// 'path/to/your/file' 是你要获取状态的文件的路径
fs.readFile('path/to/your/file',{
  encoding:'utf8'
} ,(error, stats) => {
    // 如果发生错误，打印错误信息
    if (error) {
        console.error('An error occurred:', error);
    } else {
        // 如果没有错误，打印文件状态信息
        console.log(stats);
    }
});
```

> 方式三：异步 Promise 读取文件

```js
// 引入fs模块的promises API
const fs = require('fs').promises;

// 使用fs.promises.stat异步获取文件状态，结果通过Promise返回
// 'path/to/your/file' 是你要获取状态的文件的路径
fs.stat('path/to/your/file')
    .then(stats => {
        // 如果Promise解析成功（即没有错误），打印文件状态信息
        console.log(stats);
    })
    .catch(error => {
        // 如果Promise解析失败（即发生错误），打印错误信息
        console.error('An error occurred:', error);
    });
```

### Node模块-fs获取文件描述符

- **文件描述符（File descriptors）是什么呢？**
  - 在常见的操作系统上，对于**每个进程，内核都维护着一张当前打开着的文件和资源的表格**
  - 每个打开的文件都分配了一个**称为文件描述符的简单的数字标识符**
  - 在系统层，**所有文件系统操作都使用这些文件描述符来标识和跟踪每个特定的文件**
  - **Windows 系统使用了一个虽然不同但概念上类似的机制来跟踪资源**
- **为了简化用户的工作，Node.js 抽象出操作系统之间的特定差异，并为所有打开的文件分配一个数字型的文件描述符**
- **fs.open() 方法用于分配新的文件描述符**
  - 一旦被分配，则**文件描述符可用于从文件读取数据、向文件写入数据、或请求关于文件的信息**

> 这段代码的作用是打开一个指定路径下的文件，并获取该文件的元数据信息

```js
// 引入fs模块
const fs = require('fs');

//调用了 fs.open() 方法来打开文件 abc.txt。该方法接受两个参数：文件路径和打开文件的模式。在本例中，文件路径为 ./abc.txt，表示当前目录下的 abc.txt 文件
fs.open('./abc.txt',(err,fd)=>{
  //如果出现错误，比如文件不存在或没有权限访问文件等情况，会在回调函数的第一个参数中返回错误信息，并在控制台打印错误信息
  if(err) {
    console.log('错误是：',err);
    return 
  }
  //调用了 fs.fstat() 方法来获取文件的元数据信息。该方法接受两个参数：文件描述符和回调函数
  fs.fstat(fd,(err,stats)=>{
    if(err)  return
    console.log(stats)
    //手动关闭文件
    fs.close()
  })
})
```

![image-20230719200357609](.\node_pictures\image-20230719200357609.png)

### Node模块-fs文件写入和选项参数的使用

#### 文件的读写

- **如果我们希望对文件的内容进行操作，这个时候可以使用文件的读写：**

  - fs.readFile(path[, options], callback)：读取文件的内容
  - fs.writeFile(file, data[, options], callback)：在文件中写入内容

  > 这个写入文件，如果我们该路径没有这个文件的话，会自动创建出文件然后写入内容
  >
  > ```js
  > fs.writeFile(file, data[, options], callback)
  > ```
  >
  > - `file`：字符串，表示要写入数据的文件的路径。如果文件不存在，则将创建一个新的文件。
  > - `data`：字符串或者 `Buffer`，表示要写入文件的数据。
  > - `options`：对象，可选参数，用于配置写入文件的选项。
  > - `callback`：回调函数，用于在写入文件完成后执行的回调函数。该函数接收一个错误参数 `err`，如果写入文件成功，则 `err` 为 `null`
  >
  > 可以填入的参数如下：
  >
  > - `options` 参数可选，可以包含以下属性：
  >   - `encoding`：字符串，表示要写入文件的编码方式，默认为 `'utf8'`。
  >   - `mode`：数字或字符串，用于设置文件的访问权限，默认为 `0o666`。
  >   - `flag`：字符串，用于设置写入文件时的模式，默认为 `'w'`。

```javascript
// 引入fs模块
const fs = require('fs');

const content = "颜少天天打游戏1"

fs.writeFile('./abc.txt',content,{flag:"w"},(err)=>{
  if(err){console.log("文件写入失败：",err);}
  else {console.log("文件写入成功");}
})
```

#### flag选项

- **flag的值有很多：https://nodejs.org/dist/latest-v14.x/docs/api/fs.html#fs_file_system_flags**
  - w 打开文件写入，默认值
  - w+打开文件进行读写（可读可写），如果不存在则创建文件
  - r打开文件读取，读取时的默认值
  - r+ 打开文件进行读写，如果不存在那么抛出异常
  - a打开要写入的文件，将流放在文件末尾。如果不存在则创建文件
  - a+打开文件以进行读写（可读可写），将流放在文件末尾。如果不存在则创建文件

#### encoding选项

- **我们再来看看编码：**
  - coderwhy老师在简书上关于字符编码的文章：https://www.jianshu.com/p/899e749be47c
  - 目前基本用的都是UTF-8编码
- **文件读取：**
  - 如果不填写encoding，返回的结果是Buffer

```javascript
const fs = require('fs')

fs.readFile("文件路径",{encoding:'utf-8'},(err,data) => {
  console.log(data)
})
```

### Node模块-fs模块对文件夹常见操作

- **新建一个文件夹**

  - 使用fs.mkdir()或fs.mkdirSync()创建一个新文件夹

  > ```js
  > fs.mkdir(path[, options], callback)
  > fs.mkdirSync(path[, options])
  > ```
  >
  > 其中，各个参数的含义如下：
  >
  > - `path`：字符串，表示要创建目录的路径。如果包含多个目录，必须确保前面的目录已经存在。如果在创建目录时发现路径上某些目录不存在，则会抛出一个错误。
  > - `options`：对象，可选参数，用于配置创建目录的选项。该参数包含以下属性：
  >   - `recursive`：布尔值，表示是否创建多级目录。如果设置为 `true`，则会递归地创建多级目录。如果设置为 `false` 或者不指定该参数，则只会创建指定的目录。
  >   - `mode`：数字或字符串，用于设置目录的访问权限，默认为 `0o777`。
  > - `callback`：回调函数，用于在创建目录完成后执行的回调函数。该函数接收一个错误参数 `err`，如果创建目录成功，则 `err` 为 `null`。
  >
  > `fs.mkdir()` 方法是异步的，会在创建目录完成后执行回调函数。如果不需要异步处理，可以使用 `fs.mkdirSync()` 方法，它是同步的，会在创建目录完成后直接返回结果。
  >
  > 需要注意的是，在某些情况下，创建目录可能会失败。例如，如果指定的路径已经存在一个非目录的同名文件，则会抛出一个错误。因此，在使用 `fs.mkdir()` 或 `fs.mkdirSync()` 方法创建目录时，需要特别注意路径的正确性和目录是否已经存在。

  - **获取文件夹的内容**

```javascript
// 引入fs模块
const fs = require('fs');

// 读取文件夹
//1.读取文件夹，获取文件夹中文件的字符串
fs.readdir('./', (err, files) => {
  console.log(files)
})
```

![image-20230720035126867](.\node_pictures\image-20230720035126867.png)

![image-20230720035520898](.\node_pictures\image-20230720035520898.png)

- 通过上一步，我们就能够清晰的区分文件和文件夹的区别了，然后就能够选择直接调用文件**或者**在调用文件夹的那部分去进行调用文件夹里的文件内容

```javascript
// 引入fs模块
const fs = require('fs');

// 读取文件夹
//1.读取文件夹，获取文件夹中文件的字符串
fs.readdir('./', { withFileTypes: true }, (err, files) => {
  files.forEach((item) => {
    if(item.isDirectory()){//isDirectory()API：判断是不是文件夹
      console.log("item是一个文件夹：",item.name);
      // 重复上一步操作文件夹，缺点是当文件夹嵌套层数一多，我们代码不断重复会形成嵌套地狱
      fs.readdir(`./node测试文件夹1/${item.name}`,{withFileTypes:true},(err,files) => {
        console.log(files,"222");
      })
    }else{
      console.log("item是一个文件：",item.name);
    }
  })
})
```

- ​	**递归优化版本**

```javascript
// 引入fs模块
const fs = require('fs');

// 递归读取文件夹中所有的文件
function readDirectory(path){
  fs.readdir(path,{withFileTypes:true},(err,files) => {
    files.forEach((item) => {
      if(item.isDirectory()){
        // 文件夹下还有文件夹，进行递归
        readDirectory(`${path}/${item.name}`)
      }else{
        console.log('item是个文件',item.name);
      }
    })
  })
}

readDirectory('./node测试文件夹1')
```

- **文件夹重命名**
  - 对文件重命名是一样的操作

```javascript
// 引入fs模块
const fs = require('fs');

// 对文件夹进行重命名
fs.rename('./node测试文件夹1','./node文件夹1',(err) => {
  if(!err) console.log("重命名成功");
})
```

### Node模块-event模块的基本使用

- **Node中的核心API都是基于异步事件驱动的：**

  - 在这个体系中，某些对象（发射器（Emitters））发出某一个事件
  - 我们可以监听这个事件（监听器 Listeners），并且传入的回调函数，这个回调函数会在监听到事件时调用

- **发出事件和监听事件都是通过EventEmitter类来完成的，它们都属于events对象**

  - `emitter.on(eventName, listener)`：监听事件，也可以使用addListener

  > - `emitter.on(eventName, listener)` / `emitter.addListener(eventName, listener)`
  >
  >   这两个方法的作用是向 `EventEmitter` 实例中注册一个事件监听器。当事件被触发时，`listener` 函数会被调用。
  >
  >   - `eventName`：字符串，表示要监听的事件的名称。在 `EventEmitter` 实例中，事件名称通常是一个字符串。
  >   - `listener`：函数，表示要注册的事件监听器。当事件触发时，该函数会被调用。该函数的参数取决于触发事件时所携带的参数。
  >
  >   调用 `on()` 或 `addListener()` 方法时，如果指定的事件名称在 `EventEmitter` 实例中不存在，则会自动创建该事件。如果该事件已经存在，则将新的监听器添加到该事件的监听器数组中。
  >
  >   ```javascript
  >   // 引入events模块
  >   const eventsEmitter = require('events');
  >           
  >   // 创建events模块实例
  >   const emitter = new eventsEmitter()
  >   // 使用events模块 --监听事件
  >   emitter.on('XiaoYu',() => {
  >     console.log('监听事件 -- XiaoYu');
  >   })
  >           
  >   // 发射事件
  >   emitter.emit('XiaoYu')
  >   ```

  - `emitter.off(eventName, listener)`：移除事件监听，也可以使用removeListener

  > - `emitter.off(eventName, listener)` / `emitter.removeListener(eventName, listener)`
  >
  >   这两个方法的作用是从 `EventEmitter` 实例中移除一个事件监听器。
  >
  >   - `eventName`：字符串，表示要移除监听器的事件的名称。
  >   - `listener`：函数，表示要移除的事件监听器。
  >
  >   如果指定的事件名称和监听器均存在，则该方法会将监听器从事件的监听器数组中移除。如果移除后该事件的监听器数组为空，则该事件将从 `EventEmitter` 实例中删除。
  >
  >   ```javascript
  >   // 引入events模块
  >   const eventsEmitter = require('events');
  >           
  >   // 创建events模块实例
  >   const emitter = new eventsEmitter()
  >   // 使用events模块 --监听事件
  >   function handleXYFn(){
  >       console.log('监听事件 -- XiaoYu');
  >   }
  >   emitter.on('XiaoYu',handleXYFn)
  >           
  >   // 发射事件
  >   emitter.emit('XiaoYu')
  >           
  >   setTimeout(() => {
  >     // 取消监听
  >     emitter.off('XiaoYu',handleXYFn)
  >     // 再尝试看能否调起发射请求
  >     emitter.emit('XiaoYu')
  >   }, 2000);
  >           
  >   ```

  - `emitter.emit(eventName[, ...args])`：发出事件，可以携带一些参数

  > - `emitter.emit(eventName[, ...args])`
  >
  >   该方法的作用是在 `EventEmitter` 实例上触发一个事件。当事件被触发时，`EventEmitter` 实例会同步地调用所有该事件的监听器函数。
  >
  >   - `eventName`：字符串，表示要触发的事件的名称。
  >   - `args`：可选参数列表，表示要传递给事件监听器的参数。这些参数会依次传递给监听器函数。
  >
  >   调用 `emit()` 方法时，如果指定的事件名称在 `EventEmitter` 实例中不存在，则不会发生任何事情。如果该事件存在，则将所有监听器函数同步地调用，并将传递给 `emit()` 方法的参数传递给这些监听器函数。

```javascript
// 引入events模块
const eventsEmitter = require('events');

// 创建events模块实例
const emitter = new eventsEmitter()
// 使用events模块 --监听事件
function handleXYFn(name,age,sex){
    console.log('监听事件 -- XiaoYu',name,age,sex);
}
emitter.on('XiaoYu',handleXYFn)//函数能够接收到发射事件传递进来的参数，也就是name、age和sex

// 发射事件，顺便进行传递参数。NodeJS.EventEmitter.emit(eventName: string | symbol, ...args: any[]): boolean
emitter.emit('XiaoYu','小余',21,'男')
```

#### 常见的方法

- **EventEmitter的实例有一些属性，可以记录一些信息：**
  - `emitter.eventNames()`：返回当前 EventEmitter对象注册的事件字符串数组
  - `emitter.getMaxListeners()`：返回当前 EventEmitter对象的最大监听器数量(如果太多会产生大量的回调导致浪费性能，所以会有限制)，可以通过**setMaxListeners()**来修改，默认是10
  - `emitter.listenerCount(事件名称)`：返回当前 EventEmitter对象某一个事件名称，监听器的个数
  - `emitter.listeners(事件名称)`：返回当前 EventEmitter对象某个事件监听器上所有的监听器数组

```javascript
const EventEmitter = require('events')

const ee = new EventEmitter()

ee.on("why", () => {})
ee.on("why", () => {})
ee.on("why", () => {})

ee.on('kobe', () => {})
ee.on('kobe', () => {})

// 1.获取所有监听事件的名称
console.log(ee.eventNames())

// 2.获取监听最大的监听个数
console.log(ee.getMaxListeners())

// 3.获取某一个事件名称对应的监听器个数
console.log(ee.listenerCount('why'))

// 4.获取某一个事件名称对应的监听器函数(数组)
console.log(ee.listeners('why'))
```

#### 方法的补充

- **EventEmitter的实例方法补充：**
  - emitter.once(eventName, listener)：事件监听一次
  - emitter.prependListener()：将监听事件添加到最前面
  - emitter.prependOnceListener()：将监听事件添加到最前面，但是只监听一次
  - emitter.removeAllListeners([eventName])：移除所有的监听器

```javascript
const EventEmitter = require('events')

const ee = new EventEmitter()

// ee.on("why", () => {
//   console.log("on监听why")
// })

// 1.once: 事件监听只监听一次(第一次发射事件的时候进行监听)
ee.once("why", () => {
  console.log("on监听why")
})

// 2.prependListener: 将事件监听添加到最前面
ee.prependListener('why', () => {
  console.log('on监听why2')
})

ee.emit('why')


// 3.移除所有的事件监听
// 不传递参数的情况下, 移除所有事件名称的所有事件监听
// 在传递参数的情况下, 只会移除传递的事件名称的事件监听
ee.removeAllListeners('why')
```

### Node类 - 二进制的学习和Buffer底层存储

#### 数据的二进制

- **计算机中所有的内容：文字、数字、图片、音频、视频最终都会使用二进制来表示**
- **JavaScript可以直接去处理非常直观的数据：比如字符串，我们通常展示给用户的也是这些内容**
- **JavaScript不是也可以处理图片吗？**
  - 事实上在**网页端，图片我们一直是交给浏览器来处理的**
  - **JavaScript或者HTML，只是负责告诉浏览器一个图片的地址**
  - 浏览器**负责获取这个图片**，并且**最终讲这个图片渲染出来**
- **但是对于服务器来说是不一样的：**
  - 服务器要处理的**本地文件类型相对较多**
  - 比如**某一个保存文本的文件并不是使用 utf-8进行编码**的，而是用 `GBK`，那么**我们必须读取到他们的二进制数据，再通过GKB转换成对应的文字**
  - 比如**我们需要读取的是一张图片数据（二进制）**，再通过某些手段**对图片数据进行二次的处理**（裁剪、格式转换、旋转、添加滤镜），Node中有一个**Sharp的库**，就是**读取图片或者传入图片的Buffer对其再进行处理**
  - 比如**在Node中通过TCP建立长连接，TCP传输的是字节流**，我们**需要将数据转成字节再进行传入**，并且**需要知道传输字节的大小**（客服端需要根据大小来判断读取多少内容）

#### Buffer和二进制

- **我们会发现，对于前端开发来说，通常`很少会和二进制直接打交道`，但是对于服务器端为了做很多的功能，我们`必须直接去操作其二进制的数据`**

- **所以Node为了可以方便开发者完成更多功能，提供给了我们一个类Buffer，并且它是全局的**
- **我们前面说过，Buffer中存储的是二进制数据，那么到底是如何存储呢？**
  - 我们可以将Buffer看成是一个存储二进制的数组
  - 这个数组中的**每一项**，可以保存8位二进制(一个字节)： 0000 0000
- 为什么是8位呢？
  - 在计算机中，**很少的情况我们会直接操作一位二进制**，因为一位二进制存储的数据是非常有限的
  - 所以通常会**将8位合在一起作为一个单元**，这个单元称之为一个字节（byte）
  - 也就是说 1byte = 8bit，1kb=1024byte，1M=1024kb
  - 比如很多编程语言中的**int类型是4个字节，long类型时8个字节**
  - 比如**TCP传输的是字节流**，在**写入和读取时都需要说明字节的个数**
  - 比如**RGB的值分别都是255**，所以**本质上在计算机中都是用一个字节存储的**

### Node类-Buffer和字符串之间的转换

#### Buffer和字符串

- **Buffer相当于是一个字节的数组，数组中的每一项对应一个字节的大小：**

- **如果我们希望将一个字符串放入到Buffer中，是怎么样的过程呢？**

  - buffer有三种表现形式：二进制、字节表示(八进制)、十六进制

    以`1111 1111`为例，二进制就不变，字节表示：255，十六进制：ff。其中十六进制是最常用的，能够以最少的形式表达最多的内容

  - 根据下面代码中打印的buffer，表现形式就是16进制

```javascript
const buffer01 = new Buffer("XiaoYu")//不过目前已经不推荐这样用了，会报警告
console.log(buffer01);//<Buffer 58 69 61 6f 59 75>
```

- 然后让我们来实现一下他如何再次转化为字符串内容吧！

```javascript
// 使用buffer
const buffer01 = new Buffer("XiaoYu")

// 得到十六进制的结果
console.log(buffer01);//<Buffer 58 69 61 6f 59 75>

// 转化为十进制
const hexString = buffer01.toString('hex');//'hex'：指定要输出的字符串格式，这里指定为十六进制格式，即将每个字节转换为两个十六进制字符
const decimalArray = hexString.match(/.{1,2}/g).map(byte => parseInt(byte, 16));

// 得到十进制结果
console.log(decimalArray); // [88, 105, 97, 111, 89, 117]

// 将十进制转化为对应文字
const textString = String.fromCharCode(...decimalArray)
console.log(textString);//XiaoYu
```

![image-20230721124836656](.\node_pictures\image-20230721124836656.png)

```javascript
//现在推荐使用Buffer身上的from方法来替代掉创建实例的方式。直接将上面的new Buffer("XiaoYu")替换成Buffer.from("XiaoYu")是完全可以的
const buffer2 = Buffer.from("XiaoYu")
console.log(buffer2)
```

`buffer.toString('hex')` 方法将会返回一个由每个字节转换成两个十六进制字符组成的字符串，例如 `Buffer.from('XiaoYu').toString('hex')` 将返回一个十六进制字符串 `'5869616f5975'`，其中 `'58'` 是字符 `'X'` 的十六进制表示，`'69'` 是字符 `'i'` 的十六进制表示，以此类推

需要注意的是，由于每个字节都被转换为两个十六进制字符，因此输出的字符串长度将是原始 Buffer 对象长度的两倍。同时，由于十六进制字符只能表示 0-15 的数值，因此在将十六进制字符串转换回 Buffer 对象时，会将每两个字符解析为一个字节，因此最终得到的 Buffer 对象长度将是字符串长度的一半。

#### 如果传递的是中文呢？

- **默认编码：utf-8**

```javascript
// 使用buffer
const buffer01 = Buffer.from("小余")//对于平时常用字，一个汉字通常需要对应三个字节。两个汉字则对应六个字节
console.log(buffer01);//<Buffer e5 b0 8f e4 bd 99>
const str = buffer01.toString()
console.log(str);//小余
```

- **如果编码和解码不同：**

```javascript
// 使用不同编码与解码
const buffer01 = Buffer.from("小余",'utf16le')
console.log(buffer01);//<Buffer 0f 5c 59 4f>
const str = buffer01.toString('utf8')
//编码与解码不同导致 乱码
console.log(str);//\YO
```

### Node类-Buffer其他创建方式和读取文件

#### Buffer的其他创建

![image-20230721221829551](.\node_pictures\image-20230721221829551.png)

#### Buffer.alloc

- `Buffer.alloc(size [, fill [, encoding]])` 是 Node.js 中用于创建新的 Buffer 对象的方法，该方法可以分配一个指定大小的新的 Buffer，同时可以指定填充 Buffer 的内容和编码方式。

> - `size`：指定创建的 Buffer 对象的大小，单位为字节。必填参数，且必须为一个正整数。
> - `fill`：指定填充 Buffer 的内容，可以是一个字符串或一个整数。可选参数，**默认为 0**。
> - `encoding`：指定填充 Buffer 的编码方式。可选参数，默认为 'utf8'。

当填充参数为一个字符串时，该字符串将会被使用 encoding 参数指定的编码方式转换成二进制数据，然后填充到创建的 Buffer 对象中。如果填充参数为一个整数，则该整数的值将会被填充到 Buffer 对象中

举个例子，假设我们想创建一个大小为 6 字节的 Buffer 对象，并将其中的值填充为 0x01，我们可以这样使用 `Buffer.alloc` 方法：

```javascript
const fs = require('fs')
// 向操作系统申请了8个字节的内存空间
const buffer = Buffer.alloc(8)
console.log(buffer);//<Buffer 00 00 00 00 00 00 00 00>

const buf = Buffer.alloc(6, 0x01);
console.log(buf);
// 输出：<Buffer 01 01 01 01 01 01>
```

另外，如果我们想将字符串填充到 Buffer 中，可以使用如下代码：

```javascript
const buf = Buffer.alloc(6, 'Hello', 'utf8');
console.log(buf);
// 输出：<Buffer 48 65 6c 6c 6f 00>
```

在上面的例子中，我们将字符串 `'Hello'` 填充到了大小为 6 字节的 Buffer 对象中，因为 `'Hello'` 的长度为 5，所以最后一个字节填充为了 0x00。

需要注意的是，`Buffer.alloc` 方法会先分配内存，然后将 Buffer 对象清空，因此该方法的效率比 `Buffer.from` 方法略低，但是由于创建的 Buffer 对象是已清空的，因此更加安全可靠，不容易出现数据泄露等问题。另外，如果需要创建大量的、未初始化的 Buffer 对象，可以使用 `Buffer.allocUnsafe` 方法来提高效率。

- 我们也可以对其进行操作

```javascript
const fs = require('fs')

// 1.创建一个Buffer对象
// 8个字节大小的buffer内存空间
const buf = Buffer.alloc(8)
// console.log(buf)

// 2.手动对每个字节进行访问
// console.log(buf[0])
// console.log(buf[1])

// 3.手动对每个字节进行操作
buf[0] = 100 //对应的十六进制是64
buf[1] = 0x66 //对应的十六进制是66
console.log(buf)//<Buffer 64 66 00 00 00 00 00 00>
console.log(buf.toString())//df

buf[2] = 'm'.charCodeAt()
console.log(buf)//<Buffer 64 66 6d 00 00 00 00 00>
```

#### Buffer和文件读取

- **文本文件的读取：**
  - 不仅可以进行读取，而且还能够针对某个字节进行修改然后返回读取的文件中

```javascript
const fs = require('fs')

// 1.从文件中读取buffer
fs.readFile('./aaa.txt', { encoding: 'utf-8' }, (err, data) => {
  console.log(data)
})

fs.readFile('./aaa.txt', (err, data) => {
  console.log(data.toString())
})

fs.readFile('./aaa.txt', (err, data) => {
  data[0] = 0x6d
  console.log(data.toString())
})
```

- **图片文件的读取**

```javascript
// 2.读取一个图片的二进制(node中有一个库sharp)
fs.readFile('./kobe02.png', (err, data) => {
  console.log(data)
})
```

### Node类-Buffer的创建源码和原理分析

#### Buffer的创建过程

- 事实上我们创建Buffer时，并不会频繁的向操作系统申请内存，它会默认先申请一个8 * 1024个字节大小的内存，也就是8kb
- 然后往这8kb里面不断的放东西，等放得差不多了，才会继续向操作系统申请内存

![image-20230721223833892](.\node_pictures\image-20230721223833892.png)

#### Buffer.from源码

- **假如我们调用Buffer.from申请Buffer：**

  - 这里我们以从字符串创建为例

  > 调用 `Buffer.from(string[, encoding])` 方法从字符串创建 `Buffer` 对象的过程如下：
  >
  > 1. 首先，根据传入的字符串和编码方式，将字符串转换成二进制数据。如果没有传入编码方式，则默认使用 UTF-8 编码。
  > 2. 然后，计算出转换后的二进制数据的长度，即 `byteLength`。这个长度是以字节为单位的。
  > 3. 接下来，使用 `byteLength` 作为参数调用 `Buffer.allocUnsafe(byteLength)` 方法，分配一块内存区域，并返回一个指向该内存区域的指针。需要注意的是，`Buffer.allocUnsafe` 方法分配的内存区域中的数据可能是未初始化的，因此需要手动清空或者填充数据。
  > 4. 然后，将转换得到的二进制数据复制到新分配的内存区域中，即将第一步中得到的二进制数据填充到新创建的 `Buffer` 对象中。
  > 5. 最后，返回新创建的 `Buffer` 对象。
  >
  > 需要注意的是，`Buffer.from` 方法创建的 `Buffer` 对象是已清空的，因此比使用 `Buffer.allocUnsafe` 方法创建的 `Buffer` 对象更加安全可靠。但是，由于 `Buffer.from` 方法需要先计算出转换后的二进制数据的长度，因此在处理大量数据时可能会比 `Buffer.allocUnsafe` 方法略慢一些。如果需要创建大量的、未初始化的 `Buffer` 对象，可以使用 `Buffer.allocUnsafe` 方法来提高效率。

  - node/lib/buffer.js：290行

![image-20230721223909597](.\node_pictures\image-20230721223909597.png)

#### fromString的源码

- `Buffer.fromString(string[, encoding])` 实际上是 `Buffer.from(string[, encoding])` 方法的别名，因此它们的实现方式基本相同。

![image-20230721234052264](.\node_pictures\image-20230721234052264.png)

```javascript
function from(value, encodingOrOffset, length) {
  if (typeof value === 'string') {
    encodingOrOffset = encodingOrOffset || 'utf8';
    if (typeof encodingOrOffset !== 'string') {
      throw new TypeError(
        'The "encoding" argument must be of type string. Received ' +
          typeof encodingOrOffset
      );
    }
    if (encodingOrOffset === 'buffer' && Buffer.isBuffer(value)) {
      return value.slice();
    }
    const buffer = new FastBuffer(
      _byteLength(value, encodingOrOffset)
    );
    buffer.write(value, encodingOrOffset);
    return buffer;
  }
  // ...
}
```

可以看到，如果传入的 `value` 是一个字符串，那么就会进入 `if (typeof value === 'string') {...}` 代码块。

在这个代码块中，首先判断传入的编码方式（或偏移量）是否为字符串类型，如果不是，则抛出一个类型错误。

然后，如果传入的编码方式为 `'buffer'`，并且 `value` 已经是一个 `Buffer` 对象，那么就直接返回该 `Buffer` 对象的一个副本，即 `value.slice()`。

否则，根据传入的字符串和编码方式，计算出字符串转换成二进制数据后的长度，并使用 `new FastBuffer(length)` 创建一个新的 `Buffer` 对象。需要注意的是，`FastBuffer` 是 `Buffer` 的一个子类，它具有更快的构造速度和更低的内存使用率。

接着，使用 `buffer.write(value, encodingOrOffset)` 将字符串的二进制数据写入新创建的 `Buffer` 对象中，并返回该对象。

在 `buffer.write(value, encodingOrOffset)` 方法中，会根据指定的编码方式将字符串转换成二进制数据，并将这些数据写入到 `Buffer` 对象中。具体的实现方式与 `Buffer.prototype.write()` 方法类似。

需要注意的是，`Buffer.from` 方法和 `Buffer.fromString` 方法都是异步方法，因此它们返回的是一个 `Promise` 对象，而不是一个 `Buffer` 对象。需要使用 `await` 关键字或者 `.then()` 方法来获取最终的 `Buffer` 对象。

#### fromStringFast

- 接着我们查看fromStringFast：
  -  这里做的事情是判断剩余的长度是否还足够填充这个字符串
  - 如果不足够，那么就要通过 createPool 创建新的空间
  - 如果够就直接使用，但是之后要进行 poolOffset的偏移变化
  - node/lib/buffer.js：428行
  - **无符号右移运算符（`>>>`）**（零填充右移）将左操作数计算为无符号数，并将该数字的二进制表示形式移位为右操作数指定的位数，取模 32。向右移动的多余位将被丢弃，零位从左移入。其符号位变为 `0`，因此结果始终为非负数。与其他按位运算符不同，零填充右移返回一个无符号 32 位整数

![image-20230721234451912](.\node_pictures\image-20230721234451912.png)

![image-20230721234455111](.\node_pictures\image-20230721234455111.png)

## Web服务器开发、文件上传

### Node中 流Stream概念解析和划分

#### 认识Stream

> 
> 在 Node.js 中，流（stream）是用来处理数据流的核心抽象。流可以是可读的、可写的，或者即可读又可写。流是 EventEmitter 的实例，它们会发出各种事件，可以被监听和响应。
>
> 让我们来看看可读流和可写流之间的主要区别：
>
> ### 1. 可读流 (Readable Stream)：
>
> - **目的**：允许从数据源（如文件、请求体或任何其他数据源）读取数据。
> - **主要事件**：
>   - `data`：当有数据可读时触发。
>   - `end`：当没有更多的数据可以读取时触发。
>   - `error`：在接收和处理数据时出现错误时触发。
>   - `close`：当底层资源（例如文件描述符）已关闭时触发。
> - **主要方法**：
>   - `read([size])`：从内部缓冲中读取数据。
>   - `pause()`：暂停触发 `data` 事件，从而停止数据流。
>   - `resume()`：重新开始触发 `data` 事件，从而恢复数据流。
>   - `pipe(destination[, options])`：将可读流的数据发送到可写流。
>   - `unpipe([destination])`：取消将数据发送到可写流。
>
> ### 2. 可写流 (Writable Stream)：
>
> - **目的**：允许将数据写入目的地（如文件、响应体或任何其他数据接收者）。
> - **主要事件**：
>   - `drain`：当可以安全地写入更多数据到流中时触发。
>   - `finish`：当所有数据已经被写入底层系统时触发。
>   - `error`：在接收和写入数据时出现错误时触发。
>   - `close`：当底层资源（例如文件描述符）已关闭时触发。
> - **主要方法**：
>   - `write(chunk[, encoding][, callback])`：写入数据到流。
>   - `end([chunk][, encoding][, callback])`：表示没有更多的数据要写入流。它会触发 `finish` 事件。
>   - `cork()`：暂时阻止数据被写入真正的底层资源，通常用于组合多个写操作为一个。
>   - `uncork()`：允许之前被 `cork` 暂停的数据被写入底层资源。
>
> 总结：
>
> - **可读流**是为了从数据源获取数据，它提供了一个机制来从源头读取数据并将其传递到应用程序。
> - **可写流**是为了将数据写入目的地，它提供了一个机制来接收数据并将其传递到目标位置。
>
> 流提供了一种在处理大量数据时不超出内存限制的方法，特别是当读取速度和写入速度不匹配时。

- **什么是Stream（小溪、小河，在编程中通常翻译为流）呢？**
  - 我们的第一反应应该是**流水，源源不断的流动**
  - 程序中的流也是**类似的含义**，我们可以想象**当我们从一个文件中读取数据时，文件的二进制（字节）数据会源源不断的被读取到我们程序**中
  - 而**这个一连串的字节，就是我们程序中的流**
- **所以，我们可以这样理解流：**
  - 是**连续字节的一种表现形式和抽象概念**
  - 流应该是**可读的**，也是**可写的**
- **在之前学习文件的读写时，我们可以直接通过 readFile或者 writeFile方式读写文件，为什么还需要流呢？**
  - 直接**读写文件的方式，虽然简单**，但是**无法控制一些细节的操作**
  - 比如**从什么位置开始读、读到什么位置、一次性读取多少个字节**
  - 读到某**个位置**后，**暂停读取，某个时刻恢复继续读取**等等
  - 或者**这个文件非常大**，比如一个视频文件，一次性全部读取并不合适

#### 文件读写的Stream

- **事实上Node中很多对象是基于流实现的：**

  - `http模块`的`Request`和`Response`对象

- **官方文档：另外所有的流都是EventEmitter的实例**

- **Node.js中有四种基本流类型：**

  - Writable：可以向其写入数据的流（例如 fs.createWriteStream()）

  > Writable 是一种可以向其写入数据的流，它通常用于将数据写入文件、网络连接或其他输出目标。在 Node.js 中，可以使用 fs.createWriteStream() 方法创建一个可写流

  - Readable：可以从中读取数据的流（例如 fs.createReadStream()）

  > Readable 是一种可以从中读取数据的流，它通常用于从文件、网络连接或其他输入源中读取数据。在 Node.js 中，可以使用 fs.createReadStream() 方法创建一个可读流

  - Duplex：同时为Readable和Writable（例如 net.Socket）

  > Duplex 是一种同时为 Readable 和 Writable 的流，它通常用于网络连接、WebSocket 等双向通信场景。在 Node.js 中，可以使用 net.Socket 类创建一个 Duplex 流

  - Transform：Duplex可以在写入和读取数据时修改或转换数据的流（例如zlib.createDeflate()）

  > Transform 是一种可以在写入和读取数据时修改或转换数据的流，它通常用于数据压缩、加密、解密等场景。在 Node.js 中，可以使用 zlib.createDeflate() 方法创建一个 Transform 流

### Node流 - 可读流的基本使用过程

#### Readable

- **之前我们读取一个文件的信息采用的是readFile：**

![image-20230722124934801](.\node_pictures\image-20230722124934801.png)

- **这种方式是一次性将一个文件中所有的内容都读取到程序（内存）中，但是这种读取方式就会出现我们之前提到的很多问题：**
  1. 内存浪费：当读取的文件很大时，将所有内容读取到内存中会导致内存占用过高，可能会导致内存不足或应用程序崩溃。
  2. 读取速度慢：当读取的文件很大时，读取操作需要很长时间，可能会导致应用程序的响应时间变慢，或者在读取过程中阻塞其他操作。
  3. 阻塞I/O线程：当读取操作很慢时，可能会导致I/O线程被阻塞，无法处理其他请求，从而影响应用程序的并发性能。
  4. 无法处理大文件：当读取的文件非常大时，可能会导致一次性读取失败，或者读取后无法处理这么大的数据量。
  5. 不适用于流式处理：当需要对读取的数据进行流式处理时，一次性读取数据到内存中会导致无法实现流式处理，从而影响性能。

```javascript
const fs = require('fs');

// 创建一个可读流，读取文件 ./abc.txt，从第 2 个字节的位置开始读取，读取到第 16 个字节的位置结束，每次读取 3 个字节的数据，这里的 highWaterMark 参数是可选的，默认值为 64 KB
const readStream = fs.createReadStream('./abc.txt', {
  start: 2,
  end: 16,
  highWaterMark: 3
});

// 注册 data 事件，当有数据可读取时触发回调函数
readStream.on('data', (data) => {
  // 暂停读取流，模拟异步操作
  readStream.pause(); // pause() 方法会暂停读取流，直到调用 resume() 方法时才会继续读取
  setTimeout(() => {
    // 恢复读取流
    readStream.resume(); // resume() 方法会恢复读取流
    // 输出读取的数据
    console.log(data.toString());
  }, 2000);
});
```

> 1. `fs.createReadStream()`：创建一个可读流对象，用于读取文件内容。
> 2. `readStream.on('data', callback)`：注册 data 事件，当有数据可读取时触发回调函数。
> 3. `readStream.pause()`：暂停读取流，停止从文件中读取数据，流会处于暂停状态。
> 4. `setTimeout(callback, delay)`：在指定的延迟时间后执行回调函数。
> 5. `readStream.resume()`：恢复读取流，继续从文件中读取数据，流会处于流动状态。
> 6. `data.toString()`：将二进制数据转换为字符串，方便输出到控制台。

### Node流-可读流的其他事件监听

除了在可读流上监听 `data` 事件以读取数据之外，Node.js 还支持在可读流上监听其他事件来处理流的状态和错误：

1. `readable` 事件

当流有新的数据可以读取时，会触发 `readable` 事件。在该事件的回调函数中，可以使用 `readable.read()` 方法读取数据。需要注意的是，由于流的内部缓冲机制，不一定每次触发 `readable` 事件都会有数据可读，因此需要在回调函数中使用循环来读取所有可读数据。

```javascript
const fs = require('fs');
const readStream = fs.createReadStream('file.txt');

readStream.on('readable', () => {
  let chunk;
  while ((chunk = readStream.read()) !== null) {
    console.log(`Received ${chunk.length} bytes of data.`);
  }
});
```

2. `end` 事件

当流已经没有数据可读时，会触发 `end` 事件。在该事件的回调函数中，可以进行读取结束后的操作，例如关闭文件、释放资源等等。

```javascript
const fs = require('fs');
const readStream = fs.createReadStream('file.txt');

readStream.on('end', () => {
  console.log('Finished reading file.');
});
```

3. `error` 事件

当流发生错误时，会触发 `error` 事件。在该事件的回调函数中，可以处理错误，例如输出错误信息、关闭文件、释放资源等等。

```javascript
const fs = require('fs');
const readStream = fs.createReadStream('file.txt');

readStream.on('error', (err) => {
  console.error('An error occurred:', err);
});
```

需要注意的是，如果不监听 `error` 事件，发生错误时可能会导致应用程序崩溃或无法正常工作。

4. `close` 事件

当流被关闭时，会触发 `close` 事件。在该事件的回调函数中，可以进行关闭后的操作，例如释放资源等等。

```javascript
const fs = require('fs');
const readStream = fs.createReadStream('file.txt');

readStream.on('close', () => {
  console.log('Stream closed.');
});
```

需要注意的是，如果流被关闭时发生错误，可能会触发 `error` 事件而不是 `close` 事件。因此，建议在 `error` 事件中处理关闭时可能发生的错误。

5. `pause` 和 `resume` 事件

当调用 `readStream.pause()` 方法暂停可读流时，会触发 `pause` 事件，表示流已经被暂停。当调用 `readStream.resume()` 方法恢复可读流时，会触发 `resume` 事件，表示流已经恢复。

```javascript
const fs = require('fs');
const readStream = fs.createReadStream('file.txt');

readStream.on('pause', () => {
  console.log('Stream paused.');
});

readStream.on('resume', () => {
  console.log('Stream resumed.');
});

readStream.pause();
setTimeout(() => {
  readStream.resume();
}, 1000);
```

需要注意的是，当可读流被暂停时，如果缓冲区中有数据可读，则该数据仍然可以被读取，只是流不会继续读取数据。当流被恢复时，会从上次读取的位置继续读取数据。

### 可写流的使用过程

#### Writable

- 之前我们写入一个文件的方式是这样的：

```javascript
const fs = require('fs')

fs.writeFile('文件路径',"要写入的内容}",{
  encoding:'utf-8',
  flag:'a+'
}(err) => {
  	console.log("写入文件结果:",err)
})
```

- **这种方式相当于一次性将所有的内容写入到文件中，但是这种方式也有很多问题：**
  - 比如我们希望一点点写入内容，精确每次写入的位置等
- **这个时候，我们可以使用 createWriteStream，我们来看几个参数，更多参数可以参考官网：**
  - flags：默认是w，如果我们希望是追加写入，可以使用 a(覆盖)或者 a+(追加内容)；
  - start：写入的位置；

```javascript
// 2.创建一个写入流
const writeStream = fs.createWriteStream('./ccc.txt', {
  flags: 'a'
})

writeStream.on('open', (fd) => {
  console.log('文件被打开', fd)
})

writeStream.write('XiaoYu')
writeStream.write('aaaa')
writeStream.write('bbbb', (err) => {
  console.log("写入完成:", err)
})

writeStream.on('finish', () => {
  console.log('写入完成了')
})

writeStream.on('close', () => {
  console.log('文件被关闭~')
})

// 3.写入完成时, 需要手动去掉用close方法
// writeStream.close()

// 4.end方法: 
// 操作一: 将最后的内容写入到文件中, 并且关闭文件
// 操作二: 关闭文件
writeStream.end('哈哈哈哈')
```

### Node流-可读流和可写流之间pipe方法

- **正常情况下，我们可以将读取到的 输入流，手动的放到 输出流中进行写入：**

```javascript
const fs = require('fs')

//方式1：一次性读取和写入文件。我们先读取了一个文件里面的内容，然后将内容写入到了另一个文件里面
fs.readFile('读取文件路径',(err,data) => {
  console.log(data)
  fs.writeFile('写入文件路径',data,(err) =>{
    console.log("写入文件完成:"err)
  })
})
```

- **我们也可以通过pipe来完成这样的操作：**
  - 在 Node.js 中，可读流和可写流之间可以使用 `pipe()` 方法来建立管道，将可读流中读取到的数据自动传输到可写流中进行写入，从而实现数据的传输和处理。
  - `pipe()` 方法接受一个可写流作为参数，将可读流中的数据流式传输到可写流中。当可读流中有数据可读时，`pipe()` 方法会自动将数据写入可写流中，直到可读流结束或可写流关闭。
  - 使用 `pipe()` 方法可以避免手动处理数据的过程，简化数据处理流程，并且可以节省内存，因为 `pipe()` 方法是逐个读取可读流中的数据并写入可写流中，而不是一次性将所有数据读取到内存中再写入。
- 示例代码，将可读流中的数据传输到可写流中：

```javascript
const fs = require('fs');

const readStream = fs.createReadStream('input.txt');
const writeStream = fs.createWriteStream('output.txt');

readStream.pipe(writeStream);//可读流(readStream)通过管道(pipe)将数据传输给了可写流(writeStream)
```

在上面的代码中，`readStream` 和 `writeStream` 分别是可读流和可写流，通过 `pipe()` 方法将可读流中的数据自动传输到可写流中进行写入。当可读流中有数据可读时，`pipe()` 方法会自动将数据写入可写流中，直到可读流结束或可写流关闭。

需要注意的是，如果在处理数据的过程中发生错误，`pipe()` 方法会自动关闭可写流，因此需要在可写流上监听 `error` 事件，以及在可读流上监听 `end` 事件，以确保数据处理的正确性和完整性。

在使用 `pipe()` 方法时，还可以对可读流和可写流进行链式调用，以实现多个可读流和可写流之间的数据传输，例如：

```javascript
const fs = require('fs');

const readStream1 = fs.createReadStream('input1.txt');
const readStream2 = fs.createReadStream('input2.txt');
const writeStream = fs.createWriteStream('output.txt');

readStream1.pipe(writeStream);
readStream2.pipe(writeStream);
```

在上面的代码中，`readStream1` 和 `readStream2` 分别是两个可读流，`writeStream` 是一个可写流。通过链式调用 `pipe()` 方法，将两个可读流中的数据分别传输到可写流中进行写入。

## 可写流-http模块-文件上传

### Http模块-http模块开启服务器的基本过程

#### Web服务器

- **什么是Web服务器？**
  - 当应用程序（客户端）需要某一个资源时，可以向一台服务器，通过Http请求获取到这个资源
  - 提供资源的这个服务器，就是一个Web服务器

![image-20230722213507213](.\node_pictures\image-20230722213507213.png)

- **目前有很多开源的Web服务器：Nginx、Apache（静态）、Apache Tomcat（静态、动态）、Node.js**

#### http模块

- **在Node中，提供web服务器的资源返回给浏览器，主要是通过http模块**

- **简单使用：**

  - 在监听端口时，通常建议使用1024以上的端口，这是因为1024以下的端口号被保留给一些特殊服务使用，例如FTP、SSH、HTTP等协议所使用的默认端口号，如果使用这些端口号进行服务监听，可能会与系统内置服务发生冲突，导致服务无法正常工作。

    另外，端口号是一个16位的无符号整数，范围是0到65535。因此，在选择端口号时，需要遵循以下规则：

    1. 避免使用已经被占用的端口号。可以使用 `netstat` 命令查看当前系统中已经被占用的端口号。
    2. 避免使用1024以下的端口号，以避免与系统内置服务发生冲突。
    3. 避免使用已知的常用端口号，例如80（HTTP）、443（HTTPS）、22（SSH）等。
    4. 如果需要使用多个端口号，应该选择不同的端口号，避免端口号的冲突。

    需要注意的是，虽然端口号范围是0到65535，但是并不是所有的端口号都可以使用，因为一些操作系统会限制某些端口号的使用，例如Unix-like系统中，只有超级用户（root）才能使用1024以下的端口号。因此，在选择端口号时，还需要考虑操作系统的限制。

```javascript
// 加载 Node.js 内置的 http 模块
const http = require('http');

// 创建一个 HTTP 服务器，requestListener 函数用于处理请求和响应
const server = http.createServer((request, response) => {
  // 处理请求，并向客户端返回 "Hello XiaoYu" 字符串
  response.end('Hello XiaoYu');
});

// 监听服务器的端口号，当服务器启动并开始监听端口时，触发回调函数
server.listen('8000', () => {
  console.log("8000端口启动成功");
});
```

![image-20230722222323898](.\node_pictures\image-20230722222323898.png)

1. `http.createServer([options][, requestListener])`: 该方法用于创建一个 HTTP 服务器。参数 `requestListener` 是一个回调函数，用于处理客户端的请求和响应。该回调函数接收两个参数，分别是 `request` 和 `response` 对象，分别表示客户端的请求和服务器的响应。`options` 是一个可选参数，用于配置服务器的参数，例如 SSL、TCP 等。如果省略该参数，则使用默认值。
2. `server.listen([port[, host[, backlog]]][, callback])`: 该方法用于启动服务器并监听指定的端口号和主机名。参数 `port` 是一个数字，表示监听的端口号，默认是 80 端口；`host` 是一个字符串，表示监听的主机名，默认是本地主机；`backlog` 是一个数字，表示在服务器接收连接前，操作系统可以挂起的最大连接数，默认是 511；`callback` 是一个回调函数，表示服务器启动成功后的回调函数。如果省略该参数，则不会触发回调函数。

> 在 Node.js 中，当创建 HTTP 服务器时，回调函数的两个参数 `request` 和 `response` 分别表示客户端的请求和服务器的响应。下面是常用的一些配置项：
>
> 1. `request.method`: 表示 HTTP 请求的方法，常用的方法有 GET、POST、PUT、DELETE 等。
> 2. `request.url`: 表示 HTTP 请求的 URL 地址，包括协议、主机名、端口号、路径和查询参数等。
> 3. `request.headers`: 表示 HTTP 请求的头部信息，是一个对象，包含了请求的各种参数，例如 `Content-Type`、`User-Agent`、`Cookie` 等。
> 4. `request.body`: 表示 HTTP 请求的主体内容，通常用于 POST 和 PUT 等请求方法，可以使用第三方模块如 `body-parser` 进行解析。
> 5. `response.statusCode`: 表示 HTTP 响应的状态码，常用的状态码有 200、404、500 等。
> 6. `response.setHeader(name, value)`: 用于设置 HTTP 响应的头部信息，`name` 表示头部字段名，`value` 表示头部字段值。
> 7. `response.write(data)`: 用于向 HTTP 响应中写入数据，`data` 表示要写入的数据，可以是字符串、缓冲区、流等。
> 8. `response.end([data][, encoding][, callback])`: 用于结束 HTTP 响应，并向客户端发送数据，`data` 表示要发送的数据，可以是字符串、缓冲区、流等；`encoding` 表示数据的编码方式，默认是 utf8；`callback` 表示响应结束后的回调函数。

#### 创建服务器

- **创建服务器对象，我们是通过 createServer 来完成的**

  - http.createServer会返回服务器的对象；
  - 底层其实使用直接 new Server 对象
  - 所以我们可以创建多个服务器，使用不同的端口

  ![image-20230722221605746](.\node_pictures\image-20230722221605746.png)

- **那么，当然，我们也可以自己来创建这个对象：**

![image-20230722221627867](.\node_pictures\image-20230722221627867.png)

#### 监听主机和端口号

- Server通过**listen方法来开启服务器**，并且**在某一个主机和端口上监听网络请求**：

  - 也就是当我们**通过 ip:port的方式**发送到**我们监听的Web服务器**上时
  - 我们就可以**对其进行相关的处理**

- **listen函数有三个参数：**

- **端口port**: 可以不传, 系统会默认分配端, 后续项目中我们会写入到环境变量中

- **主机host**: 通常可以传入localhost、ip地址127.0.0.1、或者ip地址0.0.0.0，默认是0.0.0.0

  - **localhost**：本质上是一个域名，通常情况下会被解析成127.0.0.1

  - **127.0.0.1**：回环地址（Loop Back Address），表达的意思其实是我们主机自己发出去的包，直接被自己接收

    ✓ 正常的数据库包经常 应用层 - 传输层 - 网络层 - 数据链路层 - 物理层 ；
    ✓ 而回环地址，是在网络层直接就被获取到了，是不会经常数据链路层和物理层的；
    ✓ 比如我们监听 127.0.0.1时，在同一个网段下的主机中，通过ip地址是不能访问的

  - **0.0.0.0：**

    - ✓ 监听IPV4上所有的地址，再根据端口找到不同的应用程序；
      ✓ 比如我们监听 0.0.0.0时，在同一个网段下的主机中，通过ip地址是可以访问的

  - **回调函数**：服务器启动成功时的回调函数

### Http模块-postman和nodemon工具

- 刚刚我们创建服务器，是直接在浏览器中输入**web地址**直接进行测试
  - 但是这个其实会有如下一个问题，当我刷新浏览器中的访问当前端口的页面的时候，服务器会被重复访问(刷新一次页面会打印两次控制台的内容)

```javascript
const http = require('http')

const server = http.createServer((request,response) =>{
  console.log("服务器被访问");//注意这行代码
  response.end('Hello XiaoYu')
})

server.listen('8000',() =>{
  console.log("8000端口启动成功");
})
```

- 当我在浏览器中刷新访问当前端口的页面时，会发现服务器被访问了两次，打印了两次控制台的内容。这是因为浏览器在请求服务器时，会分别发送两个请求，分别是 HTTP GET 请求和 HTTP Favicon 请求。

- HTTP GET 请求是浏览器请求页面资源时发送的请求，而 HTTP Favicon 请求是浏览器请求网站图标时发送的请求。如果在浏览器的地址栏中输入的地址没有指定图标，浏览器会默认向网站的根目录发送 HTTP Favicon 请求，以获取默认的图标。

- 因此，当在浏览器中访问当前端口的页面时，会发送两个请求，分别是 HTTP GET 请求和 HTTP Favicon 请求，所以服务器会被访问两次，打印两次控制台的内容。如果只希望在 HTTP GET 请求时打印控制台的内容，可以通过判断请求的 URL，来避免处理 HTTP Favicon 请求。例如：

```javascript
const http = require('http');

const server = http.createServer((request, response) => {
  // 判断请求的 URL 是否为网站图标
  if (request.url === '/favicon.ico') {
    response.writeHead(404);
    response.end();
    return;
  }

  console.log('服务器被访问');
  response.end('Hello XiaoYu');
});

server.listen(8000, () => {
  console.log('8000端口启动成功');
});
```

- 在postman这个之前用了很多次的工具是非常不错的，能够避免这个问题

![image-20230723034706732](.\node_pictures\image-20230723034706732.png)

![image-20230723034917987](.\node_pictures\image-20230723034917987.png)

- 成功完成请求
  - 这个时候，当我们在postman中发起访问的时候，就只会打印一次`服务器被访问`，不会像之前在浏览器中重复访问。这种使用工具postman肯定是会更加合适方便的

![image-20230723035047280](.\node_pictures\image-20230723035047280.png)

- 接着是第二个问题，**目前当我们修改了代码然后重新发起访问的话，服务器返回的结果是不变的。必须得重启服务器才能够返回最新的内容**，这就太麻烦了。我们想要做到实时刷新结果，如何做到？

> 社区有提供一个第三方库来方便我们，叫做nodemon，是**node monitor(node监视器)**的缩写
>
> 使用如下命令来安装他
>
> ```javascript
> pnpm install nodemon -g
> ```
>
> 这样以前使用node xxx启动服务器，现在通过nodemon xxx启动服务器

### HTTP模块-request对象携带的信息解析

- **在向服务器发送请求时，我们会携带很多信息，比如：**
  - 本次**请求的URL**，服务器**需要根据不同的URL进行不同的处理**
  - 本次**请求的请求方式**，比如**GET、POST请求传入的参数和处理的方式是不同的**
  - 本次**请求的headers中也会携带一些信**息，比如**客户端信息、接受数据的格式、支持的编码格式**等
  - 等等...
- **这些信息，Node会帮助我们封装到一个request的对象中，我们可以直接来处理这个request对象：**

![image-20230723040922354](.\node_pictures\image-20230723040922354.png)

一些常用的 `request` 对象的属性：

1. `request.url`: 表示本次请求的 URL 地址，包括协议、主机名、端口号、路径和查询参数等。例如，`http://localhost:8000/index.html?name=XiaoYu`。
2. `request.method`: 表示本次请求的请求方式，常用的方法有 GET、POST、PUT、DELETE 等。
3. `request.headers`: 表示本次请求的头部信息，是一个对象，包含了请求的各种参数，例如 `Content-Type`、`User-Agent`、`Cookie` 等。
4. `request.httpVersion`: 表示本次请求所使用的 HTTP 协议版本，常用的版本有 1.0、1.1、2.0 等。
5. `request.socket`: 表示本次请求所使用的 TCP 套接字对象，包括本地地址、本地端口、远程地址、远程端口等信息。
6. `request.rawHeaders`: 表示本次请求头部信息的原始数据，是一个数组，包含了所有的请求头部字段和值。
7. `request.rawTrailers`: 表示本次请求尾部信息的原始数据，是一个数组，包含了所有的请求尾部字段和值。
8. `request.rawBody`: 表示本次请求主体内容的原始数据，是一个缓冲区对象，包含了请求主体的原始数据。可以使用第三方模块如 `body-parser` 进行解析。

### HTTP模块-区分不同的url和method请求

#### URL的处理

- **客户端在发送请求时，会请求不同的数据，那么会传入不同的请求地址：**
  - 比如 http://localhost:8000/login
  - 比如 http://localhost:8000/products
- **服务器端需要根据不同的请求地址，作出不同的响应：**
  - 比如地址后缀是login就应该返回登录的数据，后缀是xxx就该返回对应的内容。
  - 在真实的情况下，我们会从数据库中拿到数据进行拼接。
  - 并且我们如果是登录接口的话，应该是POST请求，而不是默认的GET请求。那我们想要做到POST请求其实也很简单，request形参是能够获取到method的(获取用户的请求方法)

![image-20230723060650852](.\node_pictures\image-20230723060650852.png)

```javascript
const http = require('http')

// 1.创建server服务器
const server = http.createServer((req, res) => {
  const url = req.url
  const method = req.method

  if (url === '/login') {
    if (method === 'POST') {
      res.end('登录成功~')
    } else {
      res.end('不支持的请求方式, 请检测你的请求方式~')
    }
  } else if (url === '/products') {
    res.end('商品列表~')
  } else if (url === '/lyric') {
    res.end('天空好想下雨, 我好想住你隔壁!')
  }
})


// 2.开启server服务器
server.listen(8000, () => {
  console.log('服务器开启成功~')
})
```

### HTTP模块-传递参数-query参数解析

- **那么如果用户发送的地址中还携带一些额外的参数呢？**
  - http://localhost:8000/login?name=xiaoyu&password=123
  - 这个时候，url的值是 /login?name=xiaoyu&password=123
- **我们如何对它进行解析呢？使用内置模块url：**
  - http://localhost:8000/login?name=xiaoyu&password=123

```javascript
const http = require('http');
const url = require('url');
const qs = require('querystring');

// 1.创建server服务器
const server = http.createServer((req, res) => {
  // 1.1.解析url，获取请求的URL信息
  const urlString = req.url;
   //能够通过urlInfo.query和urlInfo获取到query参数和path后缀路径
  const urlInfo = url.parse(urlString);//parse目前已经不推荐使用了，被new URLSearchParams()替代了
  console.log(urlInfo.query,urlInfo.pathname);//name=xiaoyu&password=123 /login
  
  // 1.2.解析query参数，获取URL中的查询参数
  const queryString = urlInfo.query;
  const queryInfo = qs.parse(queryString);

  // 1.3.打印查询参数的值
  console.log(queryInfo.offset, queryInfo.size);

  // 1.4.响应客户端请求
  res.end('hello world aaaa bbb');
});

// 2.开启server服务器
server.listen(8000, () => {
  console.log('服务器开启成功~');
});
```

- query 信息获取方式：

  > 在 HTTP 请求的 URL 中，可能会包含查询参数（query string），例如：`http://localhost:8000/home/list?offset=100&size=20`。查询参数是一组键值对（key-value pair）的形式，用 `&` 连接多个键值对。
  >
  > 在 Node.js 中，可以使用 `querystring` 模块来解析查询参数。`querystring` 模块提供了一组 API，用于解析和序列化查询字符串。其中，`qs.parse()` 方法可以将查询字符串解析为一个对象。
  >
  > 在上述代码中，通过调用 `qs.parse(queryString)` 方法，将查询参数字符串解析为了一个对象 `queryInfo`，该对象包含了查询参数中所有的键值对。例如，对于 URL `http://localhost:8000/home/list?offset=100&size=20`，`queryInfo` 对象的值为 `{ offset: '100', size: '20' }`。

![image-20230723075615346](.\node_pictures\image-20230723075615346.png)

- 替代parse的URLSearchParams写法：测试的web地址是一样的

```javascript
const queryInfo = new URLSearchParams(queryString)
console.log(queryInfo);//URLSearchParams { 'name' => 'xiaoyu', 'password' => '123' }
```

### HTTP模块-传递参数-body参数

- URLSearchParams写法返回的答案其实格式跟我们以前写的JSON格式是完全一样的

![image-20230723090933888](.\node_pictures\image-20230723090933888.png)

- 而我们要如何拿到用户发给我们的body参数？

1. 使用 `http` 模块中的 `data` 事件和 `end` 事件处理：

   本质上就是服务器先接收(请求)响应体body的内容。然后响应的end事件返回内容给用户提示过程一切顺利

```javascript
const http = require('http')

const sever = http.createServer((req,res) => {
  // 设置请求编码格式
  req.setEncoding('utf-8')
  //requst对象本质上是一个readable可读流
  req.on('data',(chunk) => {
    const dataString = chunk
    console.log(dataString);
  })
  // 请求内容结束之后，响应内容给用户
  req.on('end',()=>{
    res.end('登录成功')
  })
})


sever.listen(8000,()=>{
  console.log("服务器启动成功");
})
```

上述代码中，在 `createServer` 回调函数中，使用 `req.on('data', (chunk) => {})` 方法监听 `data` 事件，将数据块 `chunk` 逐一拼接成完整的 body 参数。当所有数据块接收完毕后，使用 `req.on('end', () => {})` 方法监听 `end` 事件，处理完整的 body 参数。在这里，我们将完整的 body 参数打印出来，并使用 `res.end()` 方法响应客户端请求。

![image-20230723094359534](.\node_pictures\image-20230723094359534.png)

- 为了方便处理数据，所以我们使用`JSON.parse`将其转化形式
  - **`JSON.parse()`** 方法用来解析 JSON 字符串，构造由字符串描述的 JavaScript 值或对象
  - 这时候我们就能够拿到转化成JSON格式的数据进行判断逻辑了

```javascript
    const loginInfo = JSON.parse(dataString)
    console.log(loginInfo);
```

![image-20230723094830400](.\node_pictures\image-20230723094830400.png)

```javascript
//判断逻辑
const http = require('http')

// 1.创建server服务器
const server = http.createServer((req, res) => {
  // 获取参数: body参数
  req.setEncoding('utf-8')

  // request对象本质是上一个readable可读流
  let isLogin = false
  req.on('data', (data) => {
    const dataString = data
    const loginInfo = JSON.parse(dataString)
    if (loginInfo.name === 'xiaoyu' && loginInfo.password === '123456') {
      isLogin = true
    } else {
      isLogin = false
    }
  })

  req.on('end', () => {
    if (isLogin) {
      res.end('登录成功, 欢迎回来~')
    } else {
      res.end('账号或者密码错误, 请检测登录信息~')
    }
  })
})

// 2.开启server服务器
server.listen(8000, () => {
  console.log('服务器开启成功~')
})
```

#### method的处理

- **在Restful规范（设计风格）中，我们对于数据的增删改查应该通过不同的请求方式：**
  - GET：查询数据；
  - POST：新建数据
  - PATCH：更新数据
  - DELETE：删除数据
- **所以，我们可以通过判断不同的请求方式进行不同的处理**
  - 比如创建一个用户
  - 请求接口为 /users
  - 请求方式为 POST请求
  - 携带数据 username和password

#### 创建用户接口

- **在我们程序中如何进行判断以及获取对应的数据呢？**
  - 这里我们需要判断接口是 /users，并且请求方式是POST方法去获取传入的数据
  - 获取这种body携带的数据，我们需要通过监听req的 data事件来获取
  - 具体代码在前面也有演示了

![image-20230723110432519](.\node_pictures\image-20230723110432519.png)

### HTTP模块-传递参数-header信息

#### HTTP Request Header（一）

- **在request对象的header中也包含很多有用的信息，客户端会默认传递过来一些信息：**
  - 通过打印request形参中的header获取

![image-20230723110504387](.\node_pictures\image-20230723110504387.png)

- **content-type是这次请求携带的数据的类型：**
  - application/x-www-form-urlencoded：表示数据被编码成以 '&' 分隔的键 - 值对，同时以 '=' 分隔键和值
  - application/json：表示是一个json类型
  - text/plain：表示是文本类型
  - application/xml：表示是xml类型
  - multipart/form-data：表示是上传文件

- **content-length：文件的大小长度**

- **keep-alive：**

  - http是基于TCP协议的，但是通常在进行一次请求和响应结束后会立刻中断

  - 在http1.0中，如果想要继续保持连接：

    ✓ 浏览器需要在请求头中添加 connection: keep-alive；
    ✓ 服务器需要在响应头中添加 connection:keey-alive；
    ✓ 当客户端再次放请求时，就会使用同一个连接，直接一方中断连接；

  - 在http1.1中，所有连接默认是 connection: keep-alive的；

    ✓ 不同的Web服务器会有不同的保持 keep-alive的时间；
    ✓ Node中默认是5s中；

- **accept-encoding**：告知服务器，客户端支持的文件压缩格式，比如js文件可以使用gzip编码，对应 .gz文件
- **accept**：告知服务器，客户端可接受文件的格式类型
- **user-agent**：客户端相关的信息

![image-20230723224524061](.\node_pictures\image-20230723224524061.png)

![image-20230723224808829](.\node_pictures\image-20230723224808829.png)

### HTTP模块-响应的方式

- **如果我们希望给客户端响应的结果数据，可以通过两种方式：**

  - Write方法：这种方式是直接写出数据，但是并没有关闭流
  - end方法：这种方式是写出最后的数据，并且写出后会关闭流

  ![image-20230723225009880](.\node_pictures\image-20230723225009880.png)

  ```javascript
  const http = require('http')
  
  // 1.创建server服务器
  const server = http.createServer((req, res) => {
    // res: response对象 => Writable可写流
    // 1.响应数据方式一: write
    res.write("Hello World")
    res.write("哈哈哈哈")
  
    // // 2.响应数据方式二: end
    res.end("本次写出已经结束")
  })
  
  
  // 2.开启server服务器
  server.listen(8000, () => {
    console.log('服务器开启成功~')
  })
  ```

  - **如果我们没有调用 end和close，客户端将会一直等待结果：**
    - 所以客户端在发送网络请求时，都会设置超时时间

  ### HTTP模块-响应结果-响应状态码

  #### 返回状态码

- **Http状态码（Http Status Code）是用来表示Http响应状态的数字代码：**
  - Http状态码非常多，可以根据不同的情况，给客户端返回不同的状态码
  -  MDN响应码解析地址：https://developer.mozilla.org/zh-CN/docs/web/http/status

![image-20230723230700025](.\node_pictures\image-20230723230700025.png)

- 如下图拿到res直接修改statusCode，就能够返回对应的状态码，在postman中能够明显的看到
- 通过res.writeHead()是能够跟statusCode做到一样的事情和效果，只是用的会少一点

![image-20230723230712458](.\node_pictures\image-20230723230712458.png)

#### 响应头文件

- **返回头部信息，主要有两种方式：**

  - res.setHeader：一次写入一个头部信息
  - res.writeHead：同时写入header和status

  > 如果不设置编码格式的话，在浏览器有可能就会乱码，而在postman中则不会。这是因为在postman中，是默认采用utf-8的编码格式

![image-20230724000051519](.\node_pictures\image-20230724000051519.png)

- **Header设置 Content-Type有什么作用呢？**

  - 默认客户端接收到的是字符串，客户端会按照自己默认的方式进行处理

  ![image-20230724000123469](.\node_pictures\image-20230724000123469.png)![image-20230724000126394](.\node_pictures\image-20230724000126394.png)

- 也能够通过这些设置从而返回JSON格式的数据

### HTTP模块-http发送网络请求和Axios

`axios` 是一个流行的 HTTP 客户端库，它提供了在浏览器和 Node.js 中发送 HTTP 请求的简洁 API。由于浏览器环境和 Node.js 环境的差异，`axios` 的内部实现也有所不同。接下来，我们将深入探讨这两种环境下 `axios` 的实现差异。

1. ### 浏览器中的 axios

当在浏览器环境中使用 `axios` 时，它主要依赖于 XMLHttpRequest（通常简称为 XHR）来处理 HTTP 请求。XHR 是浏览器提供的一个接口，允许 JavaScript 与 Web 服务器交互，而不需要整个页面刷新。

**XHR 的特点**：

- **异步请求**：最大的优势是能发送异步请求。
- **支持跨域**：通过 CORS（跨源资源共享）来实现跨域请求。
- **支持事件监听**：可以监听请求的生命周期，如 `load`, `error`, `progress` 等。
- **支持进度控制**：可以获取请求或响应的进度信息。

尽管 XHR 为 Web 提供了强大的异步请求能力，但其原生 API 使用起来不够友好。因此，`axios` 通过封装 XHR 提供了更简洁、更直观的 API，并添加了一些有用的功能，如拦截器、错误处理等。

2. ### Node.js 中的 axios

在 Node.js 环境中，浏览器特有的 XHR 接口自然是不存在的。因此，当在 Node.js 中使用 `axios` 时，它主要依赖于 Node.js 的内置 `http` 和 `https` 模块来处理 HTTP 和 HTTPS 请求。

**Node.js 的 http/https 模块特点**：

- **流处理**：在 Node.js 中，请求和响应都被当作流处理，这允许高效地处理大量数据。
- **事件驱动**：可以监听各种事件，如 `data`, `end`, `error` 等。
- **灵活性**：与 XHR 相比，`http` 模块提供了更多的底层控制，但这也意味着更多的复杂性。

`axios` 在 Node.js 中也提供了与浏览器类似的 API，使得开发者无需关心底层的差异，可以在不同的环境中使用相同的代码。

- ### 总结

`axios` 的强大之处在于其为浏览器和 Node.js 提供了统一的 API，同时还提供了许多高级功能，如请求和响应拦截、取消请求、自动转换 JSON 数据等。尽管在两种环境中的底层实现有所不同，但开发者可以无缝切换，这大大简化了跨平台开发的复杂性。

```javascript
const http = require('http')

// 1.使用http模块发送get请求
// http.get('http://localhost:8000', (res) => {
//   // 从可读流中获取数据
//   res.on('data', (data) => {
//     const dataString = data.toString()
//     const dataInfo = JSON.parse(dataString)
//     console.log(dataInfo)
//   })
// })

// 2.使用http模块发送post请求
const req = http.request({
  method: 'POST',
  hostname: 'localhost',
  port: 8000
}, (res) => {
  res.on('data', (data) => {
    const dataString = data.toString()
    const dataInfo = JSON.parse(dataString)
    console.log(dataInfo)
  })
})

// 必须调用end, 表示写入内容完成。没有主动结束的话会报错
req.end()
```

```javascript
//上面的代码其实是封装在axios的里面的了，所以我们可以用axios简写的写法完成我们的需求
const axios = require("axios")

axios.get("http://localhost:8000").then(res => {
  console.log(res.data)
})
```

### HTTP模块-文件上传-文件上传的流程

#### 文件上传 – 错误示范

- 如果是一个很大的文件需要上传到服务器端，服务器端进行保存应该如何操作呢？
- 基本上不使用原生处理，因为原生处理特别的麻烦。
  - 文件上传只能是POST请求，因为文件上传要将整个文件携带发送给服务器的，GET是做不到这一点的
  - 文件上传的文件是放在`体(Body)`的`from-data`里面的
  - 如果图片比较大的话，一次是上传不完的，会分好几次上传

```javascript
//这是错误的示范，客户端上传的图片是打不开的，因为格式是有问题的
const http = require("http");
const fs = require('fs')

// 1.创建server服务器
const server = http.createServer((req, res) => {
  // 创建writable的stream
  const writeStream = fs.createWriteStream('./foo.png', {
    flags: 'a+'
  })

  // req.pipe(writeStream)

  // 客户端传递的数据是表单数据(请求体)
  req.on("data", (data) => {
    console.log(data);
    writeStream.write(data)
  });

  req.on("end", () => {
    // console.log("数据传输完成~");
    // writeStream.close()
    res.end("文件上传成功~");
  });
});

// 2.开启server服务器
server.listen(8000, () => {
  console.log("服务器开启成功~");
});
```

1. **文件类型限制**:
   - 在这段代码中，无论客户端上传什么类型的文件，都会被保存为 `foo.png`。这意味着如果有人上传了一个非PNG文件，它仍然会被保存为PNG格式，这可能会导致文件损坏。
   - 应该检查上传的文件类型，并确保文件扩展名与其内容类型匹配。
2. **文件名冲突**:
   - 该服务始终将上传的文件保存为 `foo.png`。这意味着每次上传都会覆盖现有的文件，或者如果设置了 'a+' 标志，新的内容会被追加到现有文件中。
   - 为了避免这种情况，可以为每个上传的文件生成一个唯一的文件名。
3. **没有错误处理**:
   - 当处理文件上传时，可能会发生各种错误，例如磁盘空间不足、写入错误等。
   - 你应该监听 `writeStream` 的 `error` 事件，并适当地处理这些错误。
4. **没有对请求方法进行检查**:
   - 这段代码对所有请求方法（GET、POST、PUT等）都进行了相同的处理。
   - 你应该检查请求的方法，并只对特定的请求方法（如POST）处理文件上传。
5. **没有对请求的内容类型进行检查**:
   - 如果请求的内容类型不是文件上传的 `multipart/form-data`，那么这段代码可能不会正确工作。
   - 应该检查请求的 `Content-Type` 头，并确保它是一个文件上传请求。
6. **流的关闭**:
   - 注释掉的 `writeStream.close()` 应该被调用来确保文件流被正确关闭。
   - 虽然在 Node.js 中，流通常在完成后自动关闭，但显式地关闭它们是一个好习惯。
7. **没有限制上传大小**:
   - 客户端可以上传非常大的文件，这可能导致服务器资源耗尽。
   - 应该设置一个上传大小的限制，并拒绝超过该限制的文件。
8. **不建议直接使用 `http` 和 `fs` 处理文件上传**:
   - 处理文件上传有很多潜在的问题和安全隐患。
   - 通常建议使用专门设计用于文件上传的库，如 `multer`，它会处理很多上面提到的问题。

![image-20230724124620363](.\node_pictures\image-20230724124620363.png)

![image-20230724124632923](.\node_pictures\image-20230724124632923.png)

### HTTP模块-文件上传-文件上传的实现

- 目前我们获取到图片的是一大串的二进制内容，占据了很多字节。我们不能够设置编码格式为**UTF-8**，正如我前面所说的，打印出来上传的图片内容是一堆不可读的二进制内容，二进制内容不属于UTTF-8的解码范围。需要设置为`binary`，这也是二进制的意思

![image-20230724223326458](.\node_pictures\image-20230724223326458.png)

![image-20230724223437963](.\node_pictures\image-20230724223437963.png)

- 通过Vscode的debug，在打印data(客户端传递图片过来的内容)的地方打上断点，那在第一次内容出来的时候，就不会因为内容太多分批次发送导致内容过多，在终端看不到最前面的内容的问题。debug断点会在第一次打印出内容的时候停住

![image-20230724224002151](.\node_pictures\image-20230724224002151.png)

- 动态分类返回的图片后缀类型是很麻烦的操作，通常是使用第三方库

![image-20230724225010751](.\node_pictures\image-20230724225010751.png)

```javascript
const http = require("http");
const fs = require('fs')

// 1.创建 HTTP 服务器
const server = http.createServer((req, res) => {
  // 设置请求编码为二进制，因为我们要处理的是二进制文件（例如图片）
  req.setEncoding('binary')

  // 从请求头中获取 boundary。当上传文件时，multipart/form-data 格式的请求体会使用 boundary 来分隔不同部分的数据。
  const boundary = req.headers['content-type'].split('; ')[1].replace('boundary=', '')
  console.log(boundary)

  // 初始化 formData 字符串来收集请求体中的数据
  let formData = ''
  req.on("data", (data) => {
    //因为图片的字节很多，会分几次上传，且因为数据是字符串形式，所以我们将每次上传的内容进行后缀累加。当内容实在巨大，甚至可以使用数组来存放，然后使用push来添加
    formData += data
  });

  req.on("end", () => {
    console.log(formData)
    // 1.定位图片数据的开始位置。我们使用 'image/jpeg' 作为标志来定位，但这只适用于 JPEG 图片。
    // 在实际应用中，可能需要处理多种文件类型。
    const imgType = 'image/jpeg'
    const imageTypePosition = formData.indexOf(imgType) + imgType.length
    let imageData = formData.substring(imageTypePosition)

    // 2.删除 imageData 开始位置的空白字符。这些空白字符是分隔符之后的额外字符，不属于实际的图片数据。
    imageData = imageData.replace(/^\s\s*/, '')

    // 3.删除 imageData 末尾的 boundary。这个 boundary 表示文件数据的结束。
    imageData = imageData.substring(0, imageData.indexOf(`--${boundary}--`))

    // 4.将处理后的 imageData 数据写入文件中。这里默认将文件命名为 bar.png，但在实际应用中，应该根据上传的文件类型或其他逻辑生成文件名。
    fs.writeFile('./bar.png', imageData, 'binary', () => {
      console.log('文件存储成功')
      res.end("文件上传成功~");
    })
  });
});

// 2.启动 HTTP 服务器，监听 8000 端口
server.listen(8000, () => {
  console.log("服务器开启成功~");
});
```

- 成功上传成功图片

![image-20230724234103546](.\node_pictures\image-20230724234103546.png)

### HTTP模块-文件上传-浏览器代码实现

- 上传图片会导致页面刷新，这个不是前端的问题，是服务器那边存放了图片在同一文件下，nodemon导致的刷新

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  
  <input type="file">
  <button>上传</button>

  <script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
  <script>
    // 文件上传的逻辑
    const btnEl = document.querySelector('button')
    btnEl.onclick = function() {
      // 1.创建表单对象
      const formData = new FormData()

      // 2.将选中的图标文件放入表单
      const inputEl = document.querySelector('input')
      formData.set('photo', inputEl.files[0])

      // 3.发送post请求, 将表单数据携带到服务器(axios)
      axios({
        method: 'post',
        url: 'http://localhost:8000',
        data: formData,
        headers: {
          'Content-Type': 'multipart/form-data'
        }
      })
    }
  </script>

</body>
</html>
```

### express-express的内容概述和介绍

#### 认识Web框架

- **前面我们已经学习了使用http内置模块来搭建Web服务器，为什么还要使用框架？**
  - 原生http在进行很多处理时，会较为复杂
  - 有URL判断、Method判断、参数处理、逻辑代码处理等，都需要我们自己来处理和封装
  - 并且所有的内容都放在一起，会非常的混乱
- **目前在Node中比较流行的Web服务器框架是express、koa**
  - 我们先来学习express，后面再学习koa，并且对他们进行对比
  - 其中koa是更新的，express属于比较老旧的了，但是依旧有学习的价值
- **express早于koa出现，并且在Node社区中迅速流行起来：**
  -  我们可以基于express快速、方便的开发自己的Web服务器
  - 并且可以通过一些实用工具和中间件来扩展自己功能
- **Express整个框架的核心就是中间件，理解了中间件其他一切都非常简单！**

- **express的使用过程有两种方式：**

  - 方式一：通过express提供的脚手架，直接创建一个应用的骨架
  - 方式二：从零搭建自己的express应用结构

- **方式一：安装express-generator**

  ```shell
  安装脚手架
  npm install -g express-generator
  创建项目
  express express-demo
  安装依赖
  npm install
  启动项目
  node bin/www
  ```

- **方式二：从零搭建自己的express应用结构；**

  - 更推荐这种方式，进行初始化package.json文件的时候记得先安装express

```shell
npm init -y
```

### express-框架的基本使用

- **我们来创建第一个express项目：**
  - 我们会发现，之后的开发过程中，可以方便的将请求进行分离：
  - 无论是不同的URL，还是get、post等请求方式
  - 这样的方式非常方便我们已经进行维护、扩展
  - 当然，这只是初体验，接下来我们来探索更多的用法
- **请求的路径中如果有一些参数，可以这样表达：**
  - /users/:userId
  - 在request对象中药获取可以通过 req.params.userId;
- **返回数据，我们可以方便的使用json：**
  - res.json(数据)方式
  - 可以支持其他的方式，可以自行查看文档
  - https://www.expressjs.com.cn/guide/routing.html

```javascript
const express = require('express')

// 1.创建express的服务器
const app = express()

// 客户端访问URL: /login和/home
app.post('/login', (req, res) => {
  // 处理login请求
  res.end('登录成功, 欢迎回来~')
})

app.get('/home', (req, res) => {
  res.end('首页的轮播图/推荐数据列表~')
})

// 2.启动服务器, 并且监听端口
app.listen(9000, () => {
  console.log('express服务器启动成功~')
})
```

### express-认识-express中间件的作用

> 在 Express 框架中，中间件（Middleware）是一个函数，它可以访问请求对象（request object），响应对象（response object）和应用程序的请求-响应循环中的下一个中间件函数。它可以用来执行一些通用的功能，例如验证请求、记录请求日志、处理请求参数等等。

- 在上面中，我们从app身上调用的get和post中的回调函数本质上就是一个中间件(官方就是这样解释的)

- **Express是一个路由和中间件的Web框架，它本身的功能非常少：**

  - Express应用程序本质上是一系列中间件函数的调用

- **中间件是什么呢？**

  - 中间件的本质是传递给express的一个回调函数

  - 这个回调函数接受三个参数：

    ✓ 请求对象（request对象）；
    ✓ 响应对象（response对象）；
    ✓ next函数（在express中定义的用于执行下一个中间件的函数）

#### 认识中间件

![image-20230725092638771](.\node_pictures\image-20230725092638771.png)

- **中间件中可以执行哪些任务呢？**
  - 执行任何代码
  - 更改请求（request）和响应（response）对象
  - 结束请求-响应周期（返回数据）
  - 调用栈中的下一个中间件
- **如果当前中间件功能没有结束请求-响应周期，则必须调用next()将控制权传递给下一个中间件功能，否则，请求将被挂起**
  - 其实就相当于接力棒，你不结束就调用next()把控制权交给下一个要继续完成事件的中间件

![image-20230725093541893](.\node_pictures\image-20230725093541893.png)

### express-注册中间件-普通的中间件

#### 应用中间件 – 自己编写

- **那么，如何将一个中间件应用到我们的应用程序中呢？**

  - express主要提供了两种方式：

    ✓ app/router.use；
    ✓ app/router.methods；

  - 可以是 app，也可以是router，router我们后续再学习

  - methods指的是常用的请求方式，比如： app.get或app.post等

- **先学习use的用法，因为methods的方式本质是use的特殊情况**

  - **案例一：最普通的中间件**

  > Express 框架提供了 `use` 方法来注册中间件。`use` 方法接受一个或多个回调函数作为参数，每个回调函数都是一个中间件函数。当客户端发起 HTTP 请求时，Express 应用程序会按照注册的顺序依次执行这些中间件函数，直到所有中间件函数都被执行完毕，或者其中一个中间件函数结束了请求-响应循环。
  >
  > ```javascript
  > app.use([path], callback [, callback ...])
  > ```
  >
  > 其中，`path` 是可选参数，用于指定中间件函数要处理的请求路径。如果省略 `path` 参数，中间件函数将会处理所有的请求。`callback` 参数是一个或多个中间件函数，它们会按照顺序依次执行。

  ```javascript
  const express = require('express')
  
  const app = express()
  
  // 总结: 当express接收到客户端发送的网络请求时, 在所有中间件开始进行匹配
  // 当匹配到第一个符合要求的中间件时, 那么就会执行这个中间件
  // 后续的中间件是否会执行呢? 取决于上一个中间件有没有执行next
  
  // 通过use方法注册的中间件是最普通的/简单的中间件
  // 通过use注册的中间件, 无论是什么请求方式都可以匹配上
  // login/get
  // login/post
  // abc/patch
  app.use((req, res, next) => {
    console.log('normal middleware 01')
    // res.end('返回结果了, 不要等了')
    next()
  })
  
  app.use((req, res, next) => {
    console.log('normal middleware 02')
  })
  
  
  // 开启服务器
  app.listen(9000, () => {
    console.log('express服务器启动成功~')
  })
  ```


## express中间件-路由系统-koa框架使用

### express-中间件-注册路由匹配的中间件

- **案例二：path匹配中间件**
  - 意思是路径必须符合我们的要求。我们在前面的use中间件中，use的里面是直接放上了回调函数，而注册路由匹配的中间件则是在回调函数callback前面还有一个参数，就是我们所需要匹配的路由，跟use不同的一点就是想要执行这里的内容开始有条件了，也就是路由的匹配
  - 但是对是GET还是POST或者其他请求条件则没有限制

```javascript
const express = require('express')

const app = express()

// 注册普通的中间件
// app.use((req, res, next) => {
//   console.log('match normal middleware')
//   res.end('--------')
// })

// 注册路径匹配的中间件
// 路径匹配的中间件是不会对请求方式(method)进行限制
app.use('/home', (req, res, next) => {
  console.log('match /home middleware')
  res.end('home data')
})

app.listen(9000, () => {
  console.log('express服务器启动成功~')
})
```

- **案例三：path和method匹配中间件**

- 在前面匹配对应路由才能执行的基础上，继续添加必须对应的method才能够执行对应的内容，也就限制了必须发送POST还是GET或者是其他类型的请求
- 这个也是我们使用最多的

```javascript
const express = require('express')

const app = express()


// 注册中间件: 对path/method都有限制
// app.method(path, middleware)
app.get('/home', (req, res, next) => {
  console.log('match /home get method middleware')
  res.end('home data')
})


app.post('/users', (req, res, next) => {
  console.log('match /users post method middleware')
  res.end('create user success')
})


app.listen(9000, () => {
  console.log('express服务器启动成功~')
})
```

- **案例四：注册多个中间件**
  - 不断的使用callback回调函数(也就是不断的传中间件)，记得使用next()将控制权传递给下一个中间件
  - 通常如果你要做的事情太多复杂，放在一个中间件里面就太过臃肿，就能够依据功能进行拆分成多个连续的中间件

```javascript
const express = require('express')

const app = express()

// app.get(路径, 中间件1, 中间件2, 中间件3)
app.get('/home', (req, res, next) => {
  console.log('match /home get middleware01')
  next()
}, (req, res, next) => {
  console.log('match /home get middleware02')
  next()
}, (req, res, next) => {
  console.log('match /home get middleware03')
  next()
}, (req, res, next) => {
  console.log('match /home get middleware04')
})

app.listen(9000, () => {
  console.log('express服务器启动成功~')
})
```

### express-中间件-中间媒介的匹配题目练习

```javascript
const express = require('express')

const app = express()

// 1.注册两个普通的中间件
app.use((req, res, next) => {
  console.log('normal middleware01')
  next()
})

app.use((req, res, next) => {
  console.log('normal middleware02')
  next()
})


// 2.注册路径path/method的中间件
app.get('/home', (req, res, next) => {
  console.log('/home get middleware01')
  next()
}, (req, res, next) => {
  console.log('/home get middleware02')
  next()
})

app.post('/login', (req, res, next) => {
  console.log('/login post middleware')
  next()
})


// 3.注册普通的中间件
app.use((req, res, next) => {
  console.log('normal middleware03')
  next()
})

app.use((req, res, next) => {
  console.log('normal middleware04')
})


app.listen(9000, () => {
  console.log('express服务器启动成功~')
})
```

```javascript
// 引入Express.js库
const express = require('express')

// 创建一个Express应用实例
const app = express()

// 处理登录请求
// 当用户发送一个POST请求到'/login'时，这个函数就会被调用
app.post('/login', (req, res, next) => {
  // 初始化登录状态为false
  let isLogin = false

  // 当请求的数据可用时，此事件被触发
  req.on('data', (data) => {
    // 将请求的数据（Buffer）转换为字符串
    const dataString = data.toString()

    // 将字符串解析为JSON对象
    const dataInfo = JSON.parse(dataString)

    // 检查用户名和密码是否为预设的值
    if (dataInfo.username === 'xiaoyu' && dataInfo.password === '123456') {
      // 如果是，设置登录状态为true
      isLogin = true
    }
  })

  // 当请求的数据被完全消费时，此事件被触发
  req.on('end', () => {
    // 根据登录状态返回相应的消息
    if (isLogin) {
      res.end('登录成功, 欢迎回来~')
    } else {
      res.end('登录失败, 请检测账号和密码是否正确~')
    }
  })
})

// 处理注册请求
// 当用户发送一个POST请求到'/register'时，这个函数就会被调用
app.post('/register', (req, res, next) => {
  // 初始化注册状态为false
  let isRegister = false

  // 当请求的数据可用时，此事件被触发
  req.on('data', (data) => {
    // 将请求的数据（Buffer）转换为字符串
    const dataString = data.toString()

    // 将字符串解析为JSON对象
    const dataInfo = JSON.parse(dataString)

    // 在这里，你可能需要查询数据库来检查用户名是否已被注册
    // 这里假设没有查询数据库，直接设置注册状态为false
    isRegister = false
  })

  // 当请求的数据被完全消费时，此事件被触发
  req.on('end', () => {
    // 根据注册状态返回相应的消息
    if (isRegister) {
      res.end('注册成功, 开始你的旅程~')
    } else {
      res.end('注册失败, 您输入的用户名被注册~')
    }
  })
})

// 启动服务器并监听9000端口
app.listen(9000, () => {
  console.log('express服务器启动成功~')
})
```

- 代码重构版本

```javascript
const express = require('express')

const app = express()


// app.use((req, res, next) => {
//   if (req.headers['content-type'] === 'application/json') {
//     req.on('data', (data) => {
//       const jsonInfo = JSON.parse(data.toString())
//       req.body = jsonInfo
//     })

//     req.on('end', () => {
//       next()
//     })
//   } else {
//     next()
//   }
// })

// 直接使用express提供给我们的中间件
app.use(express.json())

// 注册两个实际请求的中间件
// 案例一: 用户登录的请求处理 /login post => username/password
app.post('/login', (req, res// 引入Express.js库
const express = require('express')

// 创建一个Express应用实例
const app = express()

// 注释掉的这段代码是一个中间件，它会在每个请求被处理之前运行
// 它检查请求的Content-Type是否为'application/json'
// 如果是，它会解析请求的主体并将其存储在req.body中
// 如果不是，它会跳过解析步骤并直接调用next()，将控制权传递给下一个中间件
// app.use((req, res, next) => {
//   if (req.headers['content-type'] === 'application/json') {
//     req.on('data', (data) => {
//       const jsonInfo = JSON.parse(data.toString())
//       req.body = jsonInfo
//     })

//     req.on('end', () => {
//       next()
//     })
//   } else {
//     next()
//   }
// })

// 使用Express内置的中间件来解析JSON请求体
// 这个中间件会自动将请求体解析为JavaScript对象，并将其存储在req.body中
app.use(express.json())

// 处理登录请求
// 当用户发送一个POST请求到'/login'时，这个函数就会被调用
// 它从req.body中打印出请求体
app.post('/login', (req, res, next) => {
  console.log(req.body)
})

// 处理注册请求
// 当用户发送一个POST请求到'/register'时，这个函数就会被调用
// 它从req.body中打印出请求体
app.post('/register', (req, res, next) => {
  console.log(req.body)
})

// 启动服务器并监听9000端口
app.listen(9000, () => {
  console.log('express服务器启动成功~')
})
, next) => {
  console.log(req.body)
})

// 案例二: 注册用户的请求处理 /register post => username/password
app.post('/register', (req, res, next) => {
  console.log(req.body)
})

app.listen(9000, () => {
  console.log('express服务器启动成功~')
})
```

### express-中间件应用-urlencoded

#### 应用中间件 – body解析

- **并非所有的中间件都需要我们从零去编写：**
  - express有内置一些帮助我们完成对request解析的中间件
  - registry仓库中也有很多可以辅助我们开发的中间件
- **在客户端发送post请求时，会将数据放到body中：**
  - 客户端可以通过json的方式传递
  - 也可以通过form表单的方式传递。如果使用这种方式的话，解析json的中间件就无法进行解析

![image-20230725234947282](.\node_pictures\image-20230725234947282.png)

#### 编写解析request body中间件

- 我们在前面应用的中间件**app.use(express.json())**只能够解析客户端传递过来的json格式的数据，如果换一种形式传递过来就无法解析出来了，只会解析出空对象
- 只需要使用express中的urlencoded就行了，但是默认使用的话，显示有点过期了，单还是能够正常使用的。我们只需要传入{extended: true}作为参数就可以消除过期了

```javascript
const express = require('express')

// 创建app对象
const app = express()

// 应用一些中间件
app.use(express.json()) // 解析客户端传递过来的json
// 解析传递过来urlencoded的时候, 默认使用的node内置querystring模块
// { extended: true }: 不再使用内置的querystring, 而是使用qs第三方库
app.use(express.urlencoded({ extended: true })) // 解析客户端传递过来的urlencoded

// 编写中间件
app.post('/login', (req, res, next) => {
  console.log(req.body)
  res.end('登录成功, 欢迎回来~')
})

// 启动服务器
app.listen(9000, () => {
  console.log('express服务器启动成功~')
})
```

### express-中间件应用-请求日志记录

#### 应用中间件 – 第三方中间件

- **如果我们希望将请求日志记录下来，那么可以使用express官网开发的第三方库：morgan**

  - 注意：需要单独安装

  - ```javascript
    npm install morgan
    ```

![image-20230727143157529](.\node_pictures\image-20230727143157529.png)

```javascript
// 引入文件系统模块，用于操作文件
const fs = require('fs')

// 引入Express.js库，用于创建Web服务器
const express = require('express')

// 引入Morgan库，这是一个HTTP请求记录器中间件
const morgan = require('morgan')

// 创建一个新的Express应用实例
const app = express()

// 创建一个指向'./logs/access.log'文件的可写流
// 如果文件不存在，Node.js会创建它
// 如果文件存在，那么这个操作会覆盖文件内容
const writeStream = fs.createWriteStream('./logs/access.log')

// 将Morgan中间件添加到Express应用中
// Morgan会自动记录每个HTTP请求的详细信息，并将这些信息写入到提供的流中
// 在这个例子中，所有的请求日志都会被写入到'./logs/access.log'文件中
app.use(morgan('combined', { stream: writeStream }))

// 定义一个处理POST请求的路由，该请求的路径为'/login'
// 当用户发送一个POST请求到'/login'时，这个函数就会被调用
// 在这个函数中，它返回一个表示登录成功的消息
app.post('/login', (req, res, next) => {
  // 发送响应消息
  res.end('登录成功, 欢迎回来~')
})

// 启动一个监听9000端口的Web服务器
// 当服务器启动成功时，它会打印出一条消息
app.listen(9000, () => {
  console.log('express服务器启动成功~')
})

```

然后通过我们就能够在我们创建的access.log日志文件中看到内容了

![image-20230727143729314](.\node_pictures\image-20230727143729314.png)

- **上传文件，我们可以使用express提供的multer来完成：**
  - 直接使用中间件就一步完成了
  - multer是需要安装的，npm install multer

![image-20230727143206000](.\node_pictures\image-20230727143206000.png)

```javascript
// 引入Express.js库，用于创建Web服务器
const express = require('express')

// 引入multer库，这是一个处理multipart/form-data类型的请求数据（主要用于文件上传）的中间件
const multer = require('multer')

// 创建一个新的Express应用实例
const app = express()

// 创建一个multer实例，并配置上传文件的存储路径为'./uploads'
// multer会自动处理上传的文件，并将其保存在指定的路径中
const upload = multer({
  dest: './uploads'
})

// 定义一个处理POST请求的路由，该请求的路径为'/avatar'
// 当用户发送一个POST请求到'/avatar'时，这个函数就会被调用
// upload.single('avatar')是一个中间件，它会处理名为'avatar'的上传文件
// 处理完毕后，它会将文件的信息添加到req.file中，并调用next()将控制权传递给下一个中间件
app.post('/avatar', upload.single('avatar') , (req, res, next) => {
  // 打印出上传文件的信息
  console.log(req.file)

  // 发送响应消息
  res.end('文件上传成功~')
})

// 启动一个监听9000端口的Web服务器
// 当服务器启动成功时，它会打印出一条消息
app.listen(9000, () => {
  console.log('express服务器启动成功~')
})
```

![image-20230727150740064](.\node_pictures\image-20230727150740064.png)

- 上传的图片文件在vscode无法显示的话，不是图片的问题，是因为此时我们上传的图片没有图片后缀名，例如jpg这样的后缀，但其实是能够在图片软件打开的，或者我们手动加上后缀名就能够在vscode中显示了

### express-中间件应用-多个文件上传

```javascript
// 引入Express.js库，用于创建Web服务器
const express = require('express')

// 引入multer库，这是一个处理multipart/form-data类型的请求数据（主要用于文件上传）的中间件
const multer = require('multer')

// 创建一个新的Express应用实例
const app = express()

// 创建一个multer实例，并配置上传文件的存储路径和文件名
// multer会自动处理上传的文件，并将其保存在指定的路径中
// multer.diskStorage()允许你控制文件的存储方式
const upload = multer({
  storage: multer.diskStorage({
    // destination定义了文件应该被保存的位置
    destination(req, file, callback) {
      callback(null, './uploads')
    },
    // filename定义了保存的文件应该叫什么名字
    filename(req, file, callback) {
      callback(null, Date.now() + '_' + file.originalname)//文件名称为 时间命名_文件后缀名
    }
  })
})

// 定义一个处理POST请求的路由，该请求的路径为'/avatar'
// 当用户发送一个POST请求到'/avatar'时，这个函数就会被调用
// upload.single('avatar')是一个中间件，它会处理名为'avatar'的上传文件
// 处理完毕后，它会将文件的信息添加到req.file中，并调用next()将控制权传递给下一个中间件
app.post('/avatar', upload.single('avatar') , (req, res, next) => {
  // 打印出上传文件的信息
  console.log(req.file)

  // 发送响应消息
  res.end('文件上传成功~')
})

// 定义一个处理POST请求的路由，该请求的路径为'/photos'
// 当用户发送一个POST请求到'/photos'时，这个函数就会被调用
// upload.array('photos')是一个中间件，它会处理名为'photos'的上传文件数组。也就是多文件上传
// 处理完毕后，它会将文件的信息添加到req.files中，并调用next()将控制权传递给下一个中间件
app.post('/photos', upload.array('photos'), (req, res, next) => {
  // 打印出上传文件的信息
  console.log(req.files)

  // 发送响应消息
  res.end('上传多张照片成功~')
})

// 启动一个监听9000端口的Web服务器
// 当服务器启动成功时，它会打印出一条消息
app.listen(9000, () => {
  console.log('express服务器启动成功~')
})
```

### express-中间件应用-form-data解析

- **如果我们希望借助于multer帮助我们解析一些form-data中的普通数据，那么我们可以使用any：**
  - 在form-data中更多的还是用于文件上传，传递平时的内容还是更加推荐通过body(体)中的raw传递JSON格式的数据会更好一些
  - 像下图这种在form-data中这样做是不推荐的

![image-20230728074320564](.\node_pictures\image-20230728074320564.png)

![image-20230728074326393](.\node_pictures\image-20230728074326393.png)

### express-其他参数类型的解析过程

#### 客户端发送请求的方式

- **客户端传递到服务器参数的方法常见的是5种：**
  - 方式一：通过get请求中的URL的params
  - 方式二：通过get请求中的URL的query
  - 方式三：通过post请求中的body的json格式（中间件中已经使用过）
  - 方式四：通过post请求中的body的x-www-form-urlencoded格式（中间件使用过）
  - 方式五：通过post请求中的form-data格式（中间件中使用过）

#### 传递参数params和query

- 请求地址：http://localhost:8000/login/abc/xiaoyu
  - 获取参数，使用`:`进行动态选择，而这参数的动态内容是能够通过req.params进行解析出来的。req是请求，params是参数。完整的意思就是请求客户端中url中的参数
  - 比如下面我们想拿到`:id`的话就req.params.id进行获取

![image-20230728101053687](.\node_pictures\image-20230728101053687.png)

- 请求地址：http://localhost:8000/login?username=xiaoyu&password=123
  - 获取参数

![image-20230728101120839](.\node_pictures\image-20230728101120839.png)

### express-服务器响应数据的方式

> 在前面中，我们讲解的都是request，也就是请求的参数。而接下来的部分则是response响应这个参数的内容

- **end方法**
  - 类似于http中的response.end方法，用法是一致的
- **json方法**(用的最多)
  - json方法中可以传入很多的类型：object、array、string、boolean、number、null等，它们会被转换成json格式返回
- **status方法**
  - 用于设置状态码
  - 注意：这里是一个函数，而不是属性赋值
- 更多响应的方式：https://www.expressjs.com.cn/4x/api.html#res

```javascript
const express = require('express')

// 创建app对象
const app = express()


// 编写中间件
app.post('/login', (req, res, next) => {
  // 1.res.end方法(比较少)
  // res.end('登录成功, 欢迎回来~')

  // 2.res.json方法(最多)
  // res.json({
  //   code: 0,
  //   message: '欢迎回来~',
  //   list: [
  //     { name: 'iPhone', price: 111 },
  //     { name: 'iPad', price: 111 },
  //     { name: 'iMac', price: 111 },
  //     { name: 'Mac', price: 111 },
  //   ]
  // })

  // 3.res.status方法: 设置http状态码
  res.status(201)
  res.json('创建用户成功~')
})

// 启动服务器
app.listen(9000, () => {
  console.log('express服务器启动成功~')
})
```

### express-express路由系统的使用

> 对于一个项目来说，通常只需要有一个app，也就是只需要有一个**const app = express()**就行了，虽然可以一个项目包含多个，但这比较没有必要

- **如果我们将所有的代码逻辑都写在app中，那么app会变得越来越复杂：**

  - 一方面完整的Web服务器包含非常多的处理逻辑

  - 另一方面有些处理逻辑其实是一个整体，我们应该将它们放在一起：比如对users相关的处理

    ✓ **获取**用户列表；
    ✓ **获取**某一个用户信息；
    ✓ **创建**一个新的用户；
    ✓ **删除**一个用户；
    ✓ **更新**一个用户；

- **我们可以使用 express.Router来创建一个路由处理程序：**
  - 一个Router实例**拥有完整的中间件和路由系统**
  - 因此，它也被称为 **迷你应用程序（mini-app）**

```javascript
// 引入Express.js库，用于创建Web服务器
const express = require('express')

// 引入用户路由模块
const userRouter = require('./router/userRouter')

// 创建一个新的Express应用实例
const app = express()

// 定义一个处理POST请求的路由，该请求的路径为'/login'
// 当用户发送一个POST请求到'/login'时，这个函数就会被调用
app.post('/login', (req, res, next) => {
  // 在这里，你可以添加处理登录请求的代码
})

// 定义一个处理GET请求的路由，该请求的路径为'/home'
// 当用户发送一个GET请求到'/home'时，这个函数就会被调用
app.get('/home', (req, res, next) => {
  // 在这里，你可以添加处理获取主页请求的代码
})

// 用户的接口可以直接定义在app中，也可以定义在单独的路由对象中
// 在这个例子中，用户的接口被定义在了一个单独的路由对象（userRouter）中
// userRouter处理了各种与用户相关的请求，比如获取用户列表，获取特定用户的信息，创建用户，删除用户，修改用户信息等

// 下面的注释代码展示了如何在app对象中直接定义用户接口
// app.get('/users', (req, res, next) => {})
// app.get('/users/:id', (req, res, next) => {})
// app.post('/users', (req, res, next) => {})
// app.delete('/users/:id', (req, res, next) => {})
// app.patch('/users/:id', (req, res, next) => {})

// 下面的注释代码展示了如何在一个单独的路由对象中定义用户接口
// const userRouter = express.Router()
// userRouter.get('/', (req, res, next) => {
//   res.json('用户列表数据')
// })
// userRouter.get('/:id', (req, res, next) => {
//   const id = req.params.id
//   res.json('某一个用户的数据:' + id)
// })
// userRouter.post('/', (req, res, next) => {
//   res.json('创建用户成功')
// })
// userRouter.delete('/:id', (req, res, next) => {
//   const id = req.params.id
//   res.json('删除某一个用户的数据:' + id)
// })
// userRouter.patch('/:id', (req, res, next) => {
//   const id = req.params.id
//   res.json('修改某一个用户的数据:' + id)
// })

// 使用app.use()将用户路由添加到应用中
// 这意味着所有以'/users'开头的请求都会被userRouter处理
app.use('/users', userRouter)

// 启动一个监听9000端口的Web服务器
// 当服务器启动成功时，它会打印出一条消息
app.listen(9000, () => {
  console.log('express服务器启动成功~')
})
```

- 上面的代码如果把所有的内容都堆叠在了一起，处理逻辑交错在一起，内容一多就容易像线团糊在一起梳理不开
- 所以我们会把内容定义在路由之中，进行拆分。在上面中我们目前是放在一起，但在实际应用中，我们会将其拆分到不同的文件中进行分类

### express-express部署静态资源的服务器

- 我们之前完成了上传图片文件的功能，但是并没有办法获取到上传的图片，因为我们没有写对应的接口进行获取
  - 方式1：写一个统一的接口，去读取图片统一存放的那个文件夹的图片
  - 方式2：把图片变成一种静态资源(**更推荐，也更简便**)

```javascript
//只需要在中间件中添加这段内容就行
app.use(express.static('静态资源所读取的路径'))//内置中间件，直接将一个文件夹作为静态资源
```

![image-20230728222302865](.\node_pictures\image-20230728222302865.png)

- 在真实情况下，我们会直接部署一整个项目，也就是我们编写的文件进行`npm run build`进行打包，然后生成了build打包文件。build文件就是一个完整的静态资源
- 我们直接利用如下代码将bulid文件进行静态部署，且如果我们在url输入到端口号就不继续填写的话，服务器就会自动去找静态资源下的index文件，就会找到build的入口文件index,html

```javascript
app.use(express.static('./build'))
```

![image-20230728223248552](.\node_pictures\image-20230728223248552.png)

![image-20230728223111678](.\node_pictures\image-20230728223111678.png)

### 服务器-服务器返回错误信息的两种方案

- 正常情况下，客户端请求数据，服务器返回数据就行了
- 但是有可能有时候客户端的请求方式是错误的，那服务器就应该给客户端返回正确的错误信息
- **返回错误信息的两种方案**
  1. 直接返回http错误码
  2. http状态码返回200，但是在返回的响应信息中会包含错误信息code和message
     - 通常情况下，没有错误的话，code是0。如果有错误的话，会自己定义一套属于该项目的错误码，然后编写一套文档来说明对应错误码是是什么意思

### express-express中的错误信息处理

```javascript
const express = require('express')

// 创建app对象
const app = express()

app.use(express.json())

// 编写中间件
app.post('/login', (req, res, next) => {
  // 1.获取登录传入的用户名和密码
  const { username, password } = req.body

  // 2.对用户名和密码进行判断
  if (!username || !password) {
    next(-1001)
  } else if (username !== 'xiaoyu' || password !== '123456') {//真实情况下这里需要查询数据库
    next(-1002)//错误码信息会传递到下一个中间件的errCode中
  } else {
    res.json({
      code: 0,
      message: '登录成功, 欢迎回来~',
      token: '323dfafadfa3222'
    })
  }
})


// 错误处理的中间件
app.use((errCode, req, res, next) => {
  const code = errCode
  let message = '未知的错误信息'

  switch(code) {
    case -1001:
      message = '没有输入用户名和密码'
      break
    case -1002:
      message = '输入用户名或密码错误'
      break
  }

  res.json({ code, message })
})


// 启动服务器
app.listen(9000, () => {
  console.log('express服务器启动成功~')
})
```

### express-源码部分

#### 创建app的过程

- Express框架的核心是一个名为`createApplication`的函数，这个函数负责创建一个Express应用实例。当你调用`express()`时，你实际上是在调用`createApplication`函数。

  以下是`createApplication`函数的基本工作原理：

  1. 创建一个名为`app`的函数，这个函数将被用作HTTP服务器的请求处理函数。这个函数也是Express应用的主要接口，它有许多方法和属性，比如`app.get`，`app.post`，`app.use`等。
  2. 初始化应用的各种设置，比如视图引擎，环境变量等。
  3. 将各种方法和属性添加到`app`函数上，这些方法和属性提供了Express的主要功能，比如路由，中间件，模板渲染等。
  4. 返回`app`函数。

  以下是一个简化的`createApplication`函数的示例：

```javascript
function createApplication() {
  var app = function(req, res, next) {
    app.handle(req, res, next);
  };

  // 添加各种方法和属性
  app.get = function(path, callback) {
    // 添加一个处理GET请求的路由
  };

  app.post = function(path, callback) {
    // 添加一个处理POST请求的路由
  };

  // 更多的方法和属性...

  return app;
}
```

这个函数创建了一个可以作为HTTP服务器请求处理函数的`app`对象，同时也添加了许多Express的方法和属性。当你在你的代码中调用`express()`时，你实际上是在创建这样一个`app`对象。

#### 注册中间件

当你调用`app.use()`或`app.METHOD()`（其中`METHOD`可以是任何HTTP请求方法，如`get`，`post`等）时，你实际上是在向Express应用的主路由系统添加一个中间件或路由处理器。

以下是这个过程的基本工作原理：

1. `app.use()`或`app.METHOD()`接收一个或多个函数作为参数。这些函数将被添加到主路由的中间件或路由处理器列表中。
2. 当一个请求到达Express应用时，主路由会按照它们被添加的顺序调用这些中间件和路由处理器。每个中间件或路由处理器都会接收请求对象（req），响应对象（res）和一个名为`next`的函数。
3. 中间件或路由处理器可以对请求和响应对象进行操作，比如检查请求头，添加响应头，处理请求体等。当它完成后，它可以调用`next()`函数将控制权传递给下一个中间件或路由处理器。如果一个中间件或路由处理器没有调用`next()`，那么请求-响应周期就会在这里结束。
4. 如果一个中间件或路由处理器发送了响应（比如调用了`res.send()`或`res.end()`），那么剩余的中间件或路由处理器将不会被执行。

在Express的源码中，所有的这些逻辑都被封装在了一个名为`Router`的对象中。`app.use()`和`app.METHOD()`实际上是在调用`Router`的相应方法。这使得Express的路由系统非常灵活，你可以在应用级别添加中间件和路由处理器，也可以在单独的路由器级别添加中间件和路由处理器。

### koa-koa框架的介绍和基本使用

#### 认识Koa

- **前面我们已经学习了express，另外一个非常流行的Node Web服务器框架就是Koa**
- **Koa官方的介绍：**
  - koa：next generation web framework for node.js
  - koa：node.js的下一代web框架
- **事实上，koa是express同一个团队开发的一个新的Web框架：**
  - 目前团队的核心开发者TJ的主要精力也在维护Koa，express已经交给团队维护了
  - Koa旨在为Web应用程序和API提供**更小、更丰富和更强大的能力**
  - **相对于express具有更强的异步处理能力**
  - Koa的**核心代码只有1600+行**，是一个**更加轻量级的框架**
  - 我们可以**根据需要安装和使用中间件**
- **事实上学习了express之后，学习koa的过程是很简单的**

#### Koa初体验

> 使用koa之前，也是需要先下载koa的。
>
> 1. npm init先初始化package.json配置文件
> 2. npm install koa，安装koa

- **我们来体验一下koa的Web服务器，创建一个接口**
  - koa也是通过注册中间件来完成请求操作的
- **koa注册的中间件提供了两个参数：**
- **ctx：上下文（Context）对象**
  - koa并没有像express一样，将req和res分开，而是将它们作为ctx的属性
  - ctx代表一次请求的上下文对象
  - ctx.request：获取请求对象
  - ctx.response：获取响应对象
- **next：本质上是一个dispatch，类似于之前的next**

```javascript
const Koa = require('koa')

// 创建app对象
const app = new Koa()

// 注册中间件(middleware)
// koa的中间件有两个参数: ctx/next
app.use((ctx, next) => {
  console.log('匹配到koa的中间件')
  ctx.body = '哈哈哈哈哈'
})

// 启动服务器
app.listen(6000, () => {
  console.log('koa服务器启动成功~')
})
```

### koa-koa中间件的ctx参数解析

- **koa通过创建的app对象，注册中间件只能通过use方法：**
  - Koa并**没有提供methods的方式来注册中间件**
  - 也**没有提供path中间件来匹配路径**
- **但是真实开发中我们如何将路径和method分离呢？**
  - 方式一：根据request自己来判断
  - 方式二：使用第三方路由中间件

```javascript
const Koa = require('koa')

// 创建app
const app = new Koa()

// 中间件
app.use((ctx, next) => {
  // 1.请求对象
  console.log(ctx.request) // 请求对象: Koa封装的请求对象
  console.log(ctx.req) // 请求对象: Node封装的请求对象

  // 2.响应对象
  console.log(ctx.response) // 响应对象: Koa封装的响应对象
  console.log(ctx.res) // 响应对象: Node封装的响应对象

  // 3.其他属性
  console.log(ctx.query)
  // console.log(ctx.params)

  next()
})

app.use((ctx, next) => {
  console.log('second middleware~')
})


// 启动服务器
app.listen(6000, () => {
  console.log('koa服务器启动成功~')
})
```

### koa-koa中间件区分路径和方法

- 暂时来说，我们可以通过ctx里的request请求中的path和method来获取路径和方法。然后通过if判断来筛选。
- 注册中间件只能使用use好像反而让我们不方便了，其实解决方案放在后面的路由中了

![](.\node_pictures\image-20230729122923751.png)

### koa-koa路由的安装和使用过程

- koa官方并没有给我们提供路由的库，我们可以选择第三方库：koa-router
- 该库所在地址：[koajs/router: Router middleware for Koa. Maintained by @forwardemail and @ladjs. (github.com)](https://github.com/koajs/router)

```javascript
npm install @koa/router
```

- **我们可以先封装一个 user.router.js 的文件：**
  - 为了方便就，代码合在一起了，下面注释掉的部分就属于user.router.js的内容
  - 对于我们没有封装过的方法，例如PUT这种少见基本上用不到的，且我们没有进行处理。服务器就会返回Not Found，但是这个返回结果虽然比express直接返回一个错误的html页面代码好一点，但是可读性还是不高，我们不知道用户到底是因为发起什么请求导致的错误
    - 这个时候我们就可以使用**app.use(userRouter.allowedMethods())**，返回的结果是Method Not Allowed，表示该方法我们没有进行封装，更加见名知意

> 在Koa Router中，`prefix`选项用于为路由器的所有路由添加一个公共的路径前缀。
>
> - 例如，如果你创建了一个路由器`userRouter`，并设置了`prefix: '/users'`，那么`userRouter`的所有路由都会以`/users`为前缀。这意味着，如果你定义了一个路由`userRouter.get('/:id', callback)`，那么这个路由的完整路径实际上是`/users/:id`。
>
> - 这个功能在组织大型应用时非常有用，你可以为相关的路由创建一个路由器，并为这个路由器设置一个公共的路径前缀。例如，你可以为用户相关的路由创建一个路由器，并设置路径前缀为`/users`；为文章相关的路由创建一个路由器，并设置路径前缀为`/articles`。这样可以使代码更加清晰，更易于管理。

```javascript
const Koa = require('koa')
const userRouter = require('./router/userRouter')
// const KoaRouter = require('@koa/router')

// 创建服务器app
const app = new Koa()

// 路由的使用
// // 1.创建路由对象，并且指定url中端口后面的前缀
// const userRouter = new KoaRouter({ prefix: '/users' })

// // 2.在路由中注册中间件: path/method
// userRouter.get('/', (ctx, next) => {
//   ctx.body = 'users list data~'
// })
// userRouter.get('/:id', (ctx, next) => {
//   const id = ctx.params.id
//   ctx.body = '获取某一个用户' + id
// })
// userRouter.post('/', (ctx, next) => {
//   ctx.body = '创建用户成功~'
// })
// userRouter.delete('/:id', (ctx, next) => {
//   const id = ctx.params.id
//   ctx.body = '删除某一个用户' + id
// })
// userRouter.patch('/:id', (ctx, next) => {
//   const id = ctx.params.id
//   ctx.body = '修改某一个用户' + id
// })

// 3.让路由中的中间件生效
app.use(userRouter.routes())
app.use(userRouter.allowedMethods())

// 启动服务器
app.listen(6000, () => {
  console.log('koa服务器启动成功~')
})
```

- **在app中将router.routes()注册为中间件：**

![image-20230729215911352](.\node_pictures\image-20230729215911352.png)

- **注意：allowedMethods用于判断某一个method是否支持：**
  - 如果我们请求 get，那么是正常的请求，因为我们有实现get
  - 如果我们请求 put、delete、patch，那么就自动报错：Method Not Allowed，状态码：405
  - 如果我们请求 link、copy、lock，那么就自动报错：Not Implemented，状态码：501

### koa-koa解析客户端传递的五种参数

> 客户端传递的参数主要有以下几种方式：
>
> 1. URL参数：这是通过URL的路径传递的参数，例如`/users/:id`，我们可以通过`ctx.params.id`获取这个参数。
> 2. 查询字符串：这是通过URL的查询字符串传递的参数，例如`/users?id=123`，我们可以通过`ctx.query.id`或`ctx.request.query.id`获取这个参数。
> 3. 请求体：这是通过POST或PUT请求的请求体传递的参数。Koa本身并不支持解析请求体，我们需要使用额外的中间件，例如koa-bodyparser。安装并使用了koa-bodyparser后，我们可以通过`ctx.request.body`获取请求体中的参数。
> 4. 请求头：这是通过HTTP请求头传递的参数，我们可以通过`ctx.get(header)`或`ctx.request.get(header)`获取请求头中的参数。

```javascript
//主文件
const koa = require("koa")
const useRouter = require("./router/user.router")
const app = new koa()

app.use(useRouter.routes())

app.listen(8000,()=> {
  console.log("服务器启动成功");
})
```

#### 参数解析：params - query

> `query`参数和`params`参数都是从客户端发送到服务器的请求中提取的，但它们的来源和用途有所不同。
>
> 1. `query`参数：这些参数通常来自URL的查询字符串，即URL中`?`后面的部分。例如，在URL`/users?sort=desc`中，`sort=desc`就是一个查询字符串，它可以通过`req.query.sort`访问。查询参数通常用于非身份验证的数据，例如分页信息，过滤选项等。
> 2. `params`参数：这些参数来自URL的路径部分。当你在Express中定义路由时，你可以在路径中包含命名的参数。例如，你可以定义一个路由`/users/:id`，然后在URL`/users/123`中，`123`就是一个参数，它可以通过`req.params.id`访问。这种参数通常用于标识资源，例如用户ID或文章ID等。
>
> 总的来说，`query`参数和`params`参数的主要区别在于它们的来源：`query`参数来自URL的查询字符串，而`params`参数来自URL的路径。

- 请求地址：http://localhost:8000/users/123
  - 获取**params**

```javascript
//路由文件user.router.js
const koaRouter = require("@koa/router")

const useRouter = new koaRouter({prefix:'/users'})

useRouter.get('/:id',(ctx,next)=>{
  const id = ctx.params.id
  console.log(`params参数的id是${id}`);
  ctx.body = "请求结束"
})

module.exports = useRouter
```

- 请求地址：http://localhost:8000/login?username=xiaoyu&password=123

```javascript
const koaRouter = require("@koa/router")

const useRouter = new koaRouter({prefix:'/login'})

useRouter.post('/',(ctx,next)=>{
  const query = ctx.query//获取query参数
  ctx.body = `params参数的query是${JSON.stringify(query)}`
})

module.exports = useRouter
```

#### 参数解析：json

- 请求地址：http://localhost:8000/login
- **body是json格式：**

![image-20230730200845846](.\node_pictures\image-20230730200845846.png)

- **获取json数据：**
  - 安装依赖： npm install koa-bodyparser
  - 使用 koa-bodyparser的中间件

> `koa-bodyparser`是一个Koa的中间件，用于解析HTTP请求体中的数据。当客户端向服务器发送POST或PUT请求时，请求的数据通常存储在请求体中。`koa-bodyparser`可以解析这些数据，并将其转换为JavaScript对象，以便在你的应用中使用。
>
> `koa-bodyparser`支持多种请求体格式，包括：
>
> - `application/json`：这是JSON格式的数据。`koa-bodyparser`会将其解析为JavaScript对象。
> - `application/x-www-form-urlencoded`：这是HTML表单提交时常用的格式。`koa-bodyparser`会将其解析为JavaScript对象。
> - `text/plain`：这是纯文本数据。`koa-bodyparser`会将其解析为字符串。
> - `text/html`：这是HTML数据。`koa-bodyparser`会将其解析为字符串。

![image-20230730200913966](.\node_pictures\image-20230730200913966.png)

- **ctx.body和ctx.request.body的区别是什么？**

  - `ctx.body`：这是Koa的上下文（context）对象的一个属性，用于设置HTTP响应体。你可以将任何类型的内容赋值给`ctx.body`，包括字符串，对象，Buffer等，Koa会将其发送给客户端作为HTTP响应。例如：

  ```javascript
  ctx.body = 'Hello World'; // 发送一个文本响应
  ctx.body = { msg: 'Hello World' }; // 发送一个JSON响应
  ```

  - `ctx.request.body`：这是Koa的请求（request）对象的一个属性，用于获取HTTP请求体的内容。在默认情况下，Koa并不会解析请求体，你需要使用一个中间件（如koa-bodyparser）来解析请求体。解析后的请求体数据会被赋值给`ctx.request.body`。例如：

  ```javascript
  app.use(async ctx => {
    console.log(ctx.request.body); // 打印请求体内容
  });
  ```

  总的来说，`ctx.body`用于设置响应体，`ctx.request.body`用于获取请求体。

#### 参数解析：x-www-form-urlencoded

> `x-www-form-urlencoded`是一种编码格式，它主要用于在发送HTTP请求时，将请求参数编码为URL兼容格式的字符串。这种编码格式通常用于HTML表单提交。
>
> 在`x-www-form-urlencoded`格式中，参数以键值对的形式表示，键值对之间用`&`分隔，键和值之间用`=`分隔。例如，以下的对象：
>
> ```javascript
> {
>   "name": "John Doe",
>   "age": 30
> }
> ```
>
> 在`x-www-form-urlencoded`格式中，会被编码为：
>
> ```javascript
> name=John+Doe&age=30
> ```
>
> 注意以下几点：
>
> - 空格被替换为`+`。
> - 非字母数字字符被转换为`%HH`的形式，其中`HH`是字符的ASCII码的十六进制表示。例如，`=`被编码为`%3D`。
> - 字母数字字符和一些特殊字符（`-`，`_`，`.`，`*`）不会被编码。
>
> 在Node.js中，你可以使用`querystring`模块的`stringify`函数将对象转换为`x-www-form-urlencoded`格式的字符串。在浏览器中，你可以使用`URLSearchParams`接口。
>
> 当你发送一个`x-www-form-urlencoded`格式的POST请求时，你需要将`Content-Type`请求头设置为`application/x-www-form-urlencoded`，以告诉服务器请求体的格式。

- **请求地址：http://localhost:8000/login**

  - body是x-www-form-urlencoded格式：

  ![image-20230730200940467](.\node_pictures\image-20230730200940467.png)

- **获取json数据：(和json是一致的)**
  - 安装依赖： npm install koa-bodyparser
  - 使用 koa-bodyparser的中间件

![image-20230730203228092](.\node_pictures\image-20230730203228092.png)

#### 参数解析：form-data

- 请求地址：http://localhost:8000/login
  - body是form-data格式

![image-20230730203252497](.\node_pictures\image-20230730203252497.png)

- 解析body中的数据，我们需要使用multer
  - 安装依赖：npm install koa-multer
  - 使用 multer中间件

```javascript
// 5.post/form-data
//formParser.any()：这是一个中间件，用于解析请求体中的数据。formParser通常是multer的一个实例，multer是一个处理multipart/form-data（通常用于文件上传）的中间件。any()表示接受请求体中的所有文件。
userRouter.post('/formdata', formParser.any(), (ctx, next) => {
  console.log(ctx.request.body)
  ctx.body = '用户的formdata信息'
})
```

### koa-koa的文件上传处理方案

```javascript
// 导入所需的模块
const Koa = require('koa')
const KoaRouter = require('@koa/router')
const multer = require('@koa/multer')

// 创建Koa应用实例
const app = new Koa()

// 创建multer实例，用于处理multipart/form-data类型的请求（通常用于文件上传）
// 这里使用了multer的diskStorage存储引擎，可以将上传的文件保存到服务器的硬盘上
const upload = multer({
  storage: multer.diskStorage({
    // 设置上传文件的保存目录
    destination(req, file, cb) {
      cb(null, './uploads')
    },
    // 设置上传文件的文件名
    filename(req, file, cb) {
      cb(null, Date.now() + "_" + file.originalname)
    }
  })
})

// 创建一个新的Koa Router实例，用于处理与文件上传相关的路由
// 设置了prefix选项，因此这个路由器的所有路由都会以/upload为前缀
const uploadRouter = new KoaRouter({ prefix: '/upload' })

// 定义处理单文件上传的路由，路径为/avatar
// upload.single('avatar')是一个中间件，用于处理name为avatar的文件(也就是postman中body中的key名称)
uploadRouter.post('/avatar', upload.single('avatar'), (ctx, next) => {
  // 打印上传的文件信息
  console.log(ctx.request.file)
  // 设置响应体
  ctx.body = '文件上传成功~'
})

// 定义处理多文件上传的路由，路径为/photos
// upload.array('photos')是一个中间件，用于处理name为photos的文件数组
uploadRouter.post('/photos', upload.array('photos'), (ctx, next) => {
  // 打印上传的文件信息
  console.log(ctx.request.files)
  // 设置响应体
  ctx.body = '文件上传成功~'
})

// 使用路由器的路由和中间件
app.use(uploadRouter.routes())
app.use(uploadRouter.allowedMethods())

// 启动服务器，监听6000端口
app.listen(6000, () => {
  console.log('koa服务器启动成功~')
})
```

## express和koa的区别和源码

### koa-koa部署静态资源的服务器

- koa并没有内置部署相关的功能，所以我们需要使用第三方库：

> npm install koa-static

- 部署的过程类似于express：

```javascript
const Koa = require('koa')
const static = require('koa-static')

const app = new Koa()

// app.use((ctx, next) => {
//   ctx.body = "哈哈哈哈"
// })

app.use(static('./build'))

app.listen(8000, () => {
  console.log('koa服务器启动成功~')
})
```

### koa-koa响应结果支持的数据类型

- **输出结果：body**将响应主体设置为以下之一：
  - string ：字符串数据
  - Buffer ：Buffer数据
  - Stream ：流数据
  - Object|| Array：对象或者数组
  - null ：不输出任何内容
  - 如果response.status尚未设置，Koa会自动将状态设置为200或204

```javascript
const fs = require('fs')
const Koa = require('koa')
const KoaRouter = require('@koa/router')

// 创建app对象
const app = new Koa()

// 注册路由对象
const userRouter = new KoaRouter({ prefix: '/users' })

userRouter.get('/', (ctx, next) => {
  // 1.body的类型是string
  // ctx.body = 'user list data~'

  // 2.body的类型是Buffer，在postman中会直接解析成文字，但其实返回的是Buffer二进制内容
  // ctx.body = Buffer.from('你好啊, 小余~')

  // 3.body的类型是Stream
  // const readStream = fs.createReadStream('./uploads/1668331072032_kobe02.png')
  // ctx.type = 'image/jpeg'
  // ctx.body = readStream  //如果直接返回图片的流，会是一片乱码，这是因为流无法正确解析图片格式内容，所以我们要设置ctx.type，也就是解析类型设置为对应的图片格式

  // 4.body的类型是数据(array/object) => 使用最多
  ctx.status = 201
  ctx.body = {
    code: 0,
    data: [
      { id: 111, name: 'iphone', price: 100 },
      { id: 112, name: 'xiaomi', price: 990 },
    ]
  }

  // 5.body的值是null, 自动设置http status code为204
  // ctx.body = null
})

app.use(userRouter.routes())
app.use(userRouter.allowedMethods())

// 启动服务器
app.listen(6000, () => {
  console.log('koa服务器启动成功~')
})
```

- **请求状态：status**

![image-20230801154808488](.\node_pictures\image-20230801154808488.png)

### koa-koa中错误处理的方案解析

- 对于各种错误码的话我们不应该重复写，而是应该写到一个专门的文件中进行统一管理，然后在进行导入使用

```javascript
// 导入所需的模块
const Koa = require('koa')
const KoaRouter = require('@koa/router')

// 创建Koa应用实例
const app = new Koa()

// 创建一个新的Koa Router实例，用于处理与用户相关的路由
// 设置了prefix选项，因此这个路由器的所有路由都会以/users为前缀
const userRouter = new KoaRouter({ prefix: '/users' })

// 定义处理GET请求的路由，路径为/
userRouter.get('/', (ctx, next) => {
  const isAuth = false // 这里假设用户未授权
  if (isAuth) {
    // 如果用户已授权，返回用户列表数据
    ctx.body = 'user list data~'
  } else {
    // 如果用户未授权，触发一个error事件
    // 事件的参数是错误码和上下文对象
    ctx.app.emit('error', -1003, ctx)
  }
})

// 使用路由器的路由和中间件
app.use(userRouter.routes())
app.use(userRouter.allowedMethods())

// 监听error事件
// 当触发error事件时，根据错误码设置响应体的内容
app.on('error', (code, ctx) => {
  const errCode = code
  let message = ''
  switch (errCode) {
    case -1001:
      message = '账号或者密码错误~'
      break
    case -1002:
      message = '请求参数不正确~'
      break
    case -1003:
      message = '未授权, 请检查你的token信息'
      break
  }

  const body = {
    code: errCode,
    message
  }

  ctx.body = body
})

// 启动服务器，监听6000端口
app.listen(6000, () => {
  console.log('koa服务器启动成功~')
})
```

- 谷歌有可能会限制端口，例如6000端口，如果不行的话换一个端口就行了

### koa和express区别-设计架构上的区别

- 在学习了两个框架之后，我们应该已经可以发现koa和express的区别：

- **从架构设计上来说：**

  - express是完整和强大的，其中帮助我们内置了非常多好用的功能
  - koa是简洁和自由的，它只包含最核心的功能，并不会对我们使用其他中间件进行任何的限制
    - 甚至是在app中连最基本的get、post都没有给我们提供
    - 我们需要通过自己或者路由来判断请求方式或者其他功能
  - **因为express和koa框架他们的核心其实都是中间件：**
    - 但是他们的中间件事实上，它们的中间件的执行机制是不同的，特别是针对某个中间件中包含异步操作时
    - 所以，接下来，我们再来研究一下express和koa中间件的执行顺序问题

  > ​      Express和Koa都是基于中间件的框架，但是他们处理中间件的方式有所不同，特别是在处理异步操作时。
  >
  > **Express中间件的执行顺序**
  >
  > - Express使用的是线性的、自上而下的中间件执行顺序。当一个请求到来时，Express会按照中间件被定义的顺序依次执行它们。每个中间件完成其任务后，需要调用`next()`函数来传递控制权给下一个中间件。如果一个中间件没有调用`next()`，那么后续的中间件将不会被执行。
  >
  > - 这种方式的一个缺点是，如果一个中间件需要进行异步操作（例如读取数据库或访问远程API），那么后续的中间件必须等待这个异步操作完成才能执行。
  >
  > **Koa中间件的执行顺序**
  >
  > - Koa的中间件执行顺序是基于洋葱模型的，也就是说，中间件的执行顺序和定义顺序一致（从外到内），但是中间件的结束顺序和定义顺序相反（从内到外）。这是因为Koa使用了async/await语法，使得异步操作可以像同步操作一样顺序执行。
  >
  > - 当一个请求到来时，Koa会按照中间件被定义的顺序依次执行它们，直到遇到`await next()`语句。这时，Koa会暂停当前的中间件，转而去执行下一个中间件。当没有更多的中间件需要执行时，Koa会回到最后一个被暂停的中间件，继续执行`await next()`之后的代码。这个过程会一直持续，直到所有的中间件都被完全执行完毕。
  >
  > - 这种方式的优点是，即使中间件需要进行异步操作，也不会阻塞后续中间件的执行。这使得Koa的中间件可以更好地处理异步操作，提供更好的性能和用户体验。

#### 案例实现

- ### koa中间件执行同步

```javascript
const Koa = require('koa')
const KoaRouter = require('@koa/router')

// 创建app对象
const app = new Koa()

// 注册中间件
app.use((ctx, next) => {
  console.log('koa middleware01')
  ctx.msg = 'aaa'
  next()

  // 返回结果
  ctx.body = ctx.msg
})

app.use((ctx, next) => {
  console.log('koa middleware02')
  ctx.msg += 'bbb'
  next()
})

app.use((ctx, next) => {
  console.log('koa middleware03')
  ctx.msg += 'ccc'
})


// 启动服务器
app.listen(6000, () => {
  console.log('koa服务器启动成功~')
})
```

- ### koa中间件执行异步

  - 如果中间件是一个异步函数，那么next默认不会等到这个中间件的结果，就会直接执行下一步的操作

  - 如果我们希望等到该异步函数的结果再执行下一步的操作的话，需要怎么做？

    **next()变成awat next()**，也就是把next()这个把权限递给下一个中间件的函数也变成异步的，就能够达成我们的目的。不过记得我们next()想要使用await，外面的那层回调函数async就得写上

```javascript
const Koa = require('koa')
const axios = require('axios')

// 创建app对象
const app = new Koa()

// 注册中间件
// 1.koa的中间件1
app.use(async (ctx, next) => {
  console.log('koa middleware01')
  ctx.msg = 'aaa'
  await next()

  // 返回结果
  ctx.body = ctx.msg
})

// 2.koa的中间件2
app.use(async (ctx, next) => {
  console.log('koa middleware02')
  ctx.msg += 'bbb'
  // 如果执行的下一个中间件是一个异步函数, 那么next默认不会等到中间件的结果, 就会执行下一步操作
  // 如果我们希望等待下一个异步函数的执行结果, 那么需要在next函数前面加await
  await next()
  console.log('----')
})

// 3.koa的中间件3
app.use(async (ctx, next) => {
  console.log('koa middleware03')
  // 网络请求
  const res = await axios.get('http://123.207.32.32:8000/home/multidata')
  ctx.msg += res.data.data.banner.list[0].title
})


// 启动服务器
app.listen(6000, () => {
  console.log('koa服务器启动成功~')
})
```

### koa和express的区别-express执行

- ### express同步

  - 和koa没有什么区别

```javascript
const express = require('express')

// 创建app对象
const app = express()

// 编写中间件
app.use((req, res, next) => {
  console.log('express middleware01')
  req.msg = 'aaa'
  next()
  // 返回值结果
  res.json(req.msg)
})

app.use((req, res, next) => {
  console.log('express middleware02')
  req.msg += 'bbb'
  next()
})

app.use((req, res, next) => {
  console.log('express middleware03')
  req.msg += 'ccc'
})


// 启动服务器
app.listen(9000, () => {
  console.log('express服务器启动成功~')
})
```

- ### express中间件执行异步

  - 这里的代码已经变成了异步了，包括了next()也是使用了await，但是在下面代码第一个中间件中注释掉的代码中，res.json(req.msg)到最后都不会像koa那样被执行，还是会被直接跳过

  - 这是为什么？这是因为在express中，next是这样的：

    ![image-20230802091708109](.\node_pictures\image-20230802091708109.png)

    在koa中的next本质上是一个promise，所以可以使用await并且生效

```javascript
const express = require('express')
const axios = require('axios')

// 创建app对象
const app = express()

// 编写中间件
app.use(async (req, res, next) => {
  console.log('express middleware01')
  req.msg = 'aaa'
  await next()
  // 返回值结果
  // res.json(req.msg)
})

app.use(async (req, res, next) => {
  console.log('express middleware02')
  req.msg += 'bbb'
  await next()
})

// 执行异步代码
app.use(async (req, res, next) => {
  console.log('express middleware03')
  const resData = await axios.get('http://123.207.32.32:8000/home/multidata')
  req.msg += resData.data.data.banner.list[0].title

  // 只能在这里返回结果
  res.json(req.msg)
})


// 启动服务器
app.listen(9000, () => {
  console.log('express服务器启动成功~')
})
```

### koa和express区别-koa的洋葱模型

> Koa的洋葱模型是其中间件处理机制的核心，它有两个重要的层次含义：

1. **中间件处理代码的过程**：在Koa中，每个中间件都可以看作是洋葱的一层。当一个请求到来时，Koa会按照中间件被定义的顺序依次执行它们，这就像剥洋葱一样，从外层向内层处理。当遇到`await next()`语句时，Koa会暂停当前的中间件，转而去执行下一个中间件。当没有更多的中间件需要执行时，Koa会回到最后一个被暂停的中间件，继续执行`await next()`之后的代码。这个过程会一直持续，直到所有的中间件都被完全执行完毕，这就像从内层向外层重新包裹洋葱。
2. **Response返回body执行**：在Koa的洋葱模型中，响应体的设置通常在中间件的最内层进行，也就是最后一个执行的中间件。这是因为在这个阶段，所有的中间件都已经完成了他们的任务，包括处理请求、读取数据库、调用API等。在这个阶段，我们已经得到了所有需要的数据，可以生成并设置响应体。然后，响应体会随着中间件的结束顺序（从内到外）被逐层传递出去，直到被发送给客户端。

这种洋葱模型的优点是，它允许每个中间件在请求被处理的整个过程中都有机会进行操作，包括在请求被处理之前和之后。这使得Koa的中间件非常灵活，可以用于处理各种复杂的任务，包括请求预处理、路由处理、错误处理、日志记录、响应后处理等。

![image-20230802092515535](.\node_pictures\image-20230802092515535.png)

- express不符合洋葱模型的原因：

  - Express框架的中间件处理机制不符合洋葱模型，主要原因在于它的中间件执行顺序和处理方式。
  - 在Express中，中间件的执行是线性和自上而下的。当一个请求到来时，Express会按照中间件被定义的顺序依次执行它们。每个中间件完成其任务后，需要调用`next()`函数来传递控制权给下一个中间件。如果一个中间件没有调用`next()`，那么后续的中间件将不会被执行。这意味着，一旦一个中间件开始处理响应（例如设置响应头或响应体），后续的中间件就无法再对响应进行修改。
  - 这与Koa的洋葱模型有本质的不同。在Koa的洋葱模型中，中间件的执行顺序和定义顺序一致（从外到内），但是中间件的结束顺序和定义顺序相反（从内到外）。这意味着，即使一个中间件开始处理响应，后续的中间件仍然可以对响应进行修改。这种模型使得Koa的中间件在处理请求和响应时具有更大的灵活性。

  总的来说，Express的中间件处理机制不符合洋葱模型，主要是因为它的中间件执行顺序和处理方式与洋葱模型有本质的不同。

### express源码分析-server的创建过程

Express是一个基于Node.js的web开发框架，它的核心是一个HTTP服务器。在Express中，HTTP服务器的创建过程主要发生在调用`express()`函数时。以下是这个过程的详细分析：

1. **导入模块**：首先，Express会导入所需的模块，包括http、path、query等。
2. **创建应用对象**：然后，Express会调用`createApplication()`函数来创建一个应用对象。这个应用对象是一个函数，可以作为HTTP服务器的请求处理函数。这个函数有许多方法，例如`use()`、`get()`、`post()`等，这些方法用于注册中间件和路由处理函数。
3. **初始化应用对象**：在创建应用对象后，Express会对其进行初始化。这包括设置默认的配置、注册默认的中间件、初始化路由系统等。
4. **返回应用对象**：最后，`express()`函数会返回这个应用对象。用户可以调用这个对象的方法来配置应用，例如注册中间件、添加路由、设置模板引擎等。用户还可以调用`listen()`方法来启动HTTP服务器。

以下是这个过程的简化代码：

> 在这个代码中，我们使用的const express = require("express")中的require导入的就是createApplication函数
>
> - 在实际的源码导出中，是这样导出的
>
> ```javascript
> export = module.exports = createApplication
> ```
>
> 在Node.js中，`module.exports`和`exports`都可以用来导出模块，但它们有一些微妙的区别。
>
> `module.exports`是真正的接口，Node.js系统在`require()`一个模块时，实际上返回的是`module.exports`。而`exports`只是`module.exports`的一个引用，它被设计为一个帮助工具，以方便开发者导出多个单独的方法或者变量。
>
> 然而，如果你直接给`exports`赋值，例如`exports = function() {}`，那么它就不再指向`module.exports`了。这时，即使你在模块中添加了方法或变量到`exports`，但是`require()`返回的仍然是`module.exports`，因此你添加到`exports`的方法或变量不会被暴露出来。
>
> 在你看到的`export = module.exports = createApplication`这行代码中，开发者可能想要确保`exports`和`module.exports`都能正确地导出`createApplication`函数。这样，无论开发者使用`require().createApplication`还是`require().default.createApplication`，都能得到正确的结果。
>
> 总的来说，这种写法是为了兼容那些可能会使用`exports`或`module.exports`的代码。

```javascript
function createApplication() {
  var app = function(req, res, next) {
    app.handle(req, res, next);
  };

  // 添加各种方法和属性
  app.use = function use() { /* ... */ };
  app.get = function get() { /* ... */ };
  // ...

  return app;
}

var express = createApplication;

module.exports = express;
```

在这个代码中，`createApplication()`函数创建了一个应用对象，并添加了各种方法和属性。然后，这个应用对象被导出，用户可以通过`require('express')`来获取它。

总的来说，Express的HTTP服务器的创建过程主要发生在调用`express()`函数时，这个函数会创建并初始化一个应用对象，然后返回这个对象。用户可以通过这个对象来配置应用，并启动HTTP服务器。

### express源码分析-use方法和请求处理过程

在Express框架中，`app.use()`方法是用来注册中间件的主要方法。它接收一个或多个函数作为参数，这些函数会被添加到中间件栈中，按照它们被添加的顺序来处理请求。

以下是`app.use()`方法的基本使用：

```javascript
app.use(function (req, res, next) {
  console.log('Time:', Date.now())
  next()
})
```

在这个例子中，`app.use()`方法注册了一个中间件，这个中间件打印当前时间，然后调用`next()`函数将控制权传递给下一个中间件。

**Express源码中的`app.use()`方法**

在Express的源码中，`app.use()`方法的实现比较复杂。它首先会对参数进行处理，然后将中间件添加到中间件栈中。

以下是`app.use()`方法的简化代码：

```javascript
app.use = function use(fn) {
  var offset = 0;
  var path = '/';

  // 处理参数，可以接收路径和多个处理函数
  if (typeof fn !== 'function') {
    var arg = fn;

    while (Array.isArray(arg) && arg.length !== 0) {
      arg = arg[0];
    }

    // first arg is the path
    if (typeof arg !== 'function') {
      offset = 1;
      path = fn;
    }
  }

  var fns = Array.prototype.slice.call(arguments, offset);

  // 添加中间件到中间件栈
  for (var i = 0; i < fns.length; i++) {
    var fn = fns[i];

    if (typeof fn !== 'function') {
      throw new TypeError('app.use() requires a middleware function')
    }

    // 添加到中间件栈
    this.stack.push({ route: path, handle: fn });
  }

  return this;
};
```

**Express的请求处理过程**

当一个HTTP请求到达Express应用时，Express会创建一个请求对象（req）和一个响应对象（res），然后按照中间件被添加的顺序来调用中间件。

每个中间件都会接收req、res和next三个参数。它可以通过req来访问请求信息，通过res来发送响应，通过next来将控制权传递给下一个中间件。

如果一个中间件没有调用`next()`函数，那么后续的中间件将不会被执行。如果所有的中间件都被执行完毕，但是还没有发送响应，那么Express会自动发送一个404响应。

总的来说，Express的`app.use()`方法是用来注册中间件的，而请求处理过程则是按照中间件被添加的顺序来调用中间件，每个中间件都可以访问请求信息、发送响应或将控制权传递给下一个中间件。

### koa源码分析-server的创建过程解析

在Koa框架中，HTTP服务器的创建过程主要发生在调用`new Koa()`时。以下是这个过程的详细分析：

1. **创建应用对象**：首先，Koa会调用构造函数`Koa()`来创建一个应用对象。这个应用对象包含了一些属性和方法，例如`context`、`request`、`response`等，这些都是Koa应用的核心部分。
2. **初始化应用对象**：在创建应用对象后，Koa会对其进行初始化。这包括设置默认的配置、初始化中间件栈等。
3. **创建HTTP服务器**：在Koa中，HTTP服务器是在调用`app.listen()`方法时创建的。这个方法会创建一个HTTP服务器，并将Koa应用的回调函数设置为服务器的请求处理函数。这个回调函数会在每个HTTP请求到来时被调用，它会创建一个上下文对象（context），然后按照中间件的顺序来处理请求。

以下是这个过程的简化代码：

```javascript
function Koa() {
  if (!(this instanceof Koa)) return new Koa;
  this.context = Object.create(context);
  this.request = Object.create(request);
  this.response = Object.create(response);
  this.middlewares = [];
}

Koa.prototype.listen = function(...args) {
  const server = http.createServer(this.callback());
  return server.listen(...args);
};

Koa.prototype.callback = function() {
  const fn = compose(this.middlewares);
  const handleRequest = (req, res) => {
    const ctx = this.createContext(req, res);
    return this.handleRequest(ctx, fn);
  };
  return handleRequest;
};
```

在这个代码中，`Koa()`函数创建了一个应用对象，并初始化了它的属性和方法。然后，`app.listen()`方法创建了一个HTTP服务器，并设置了服务器的请求处理函数。

总的来说，Koa的HTTP服务器的创建过程主要发生在调用`new Koa()`和`app.listen()`时，这两个步骤分别创建和初始化了Koa应用，以及创建了HTTP服务器并设置了请求处理函数。

### koa源码分析-use方法和请求处理过程

![image-20230802130405842](.\node_pictures\image-20230802130405842.png)

在Koa框架中，`app.use()`方法是用来注册中间件的主要方法。它接收一个函数作为参数，这个函数会被添加到中间件栈中，按照它们被添加的顺序来处理请求。

以下是`app.use()`方法的基本使用：

```javascript
app.use(async (ctx, next) => {
  const start = Date.now();
  await next();
  const ms = Date.now() - start;
  console.log(`${ctx.method} ${ctx.url} - ${ms}ms`);
});
```

在这个例子中，`app.use()`方法注册了一个中间件，这个中间件记录请求的处理时间，然后调用`next()`函数将控制权传递给下一个中间件。

**Koa源码中的`app.use()`方法**

在Koa的源码中，`app.use()`方法的实现比较简单。它只是将中间件添加到中间件栈中。

以下是`app.use()`方法的简化代码：

```javascript
app.use = function(fn) {
  if (typeof fn !== 'function') throw new TypeError('middleware must be a function!');
  this.middleware.push(fn);
  return this;
};
```

**Koa的请求处理过程**

当一个HTTP请求到达Koa应用时，Koa会创建一个上下文对象（context），然后按照中间件被添加的顺序来调用中间件。

每个中间件都会接收context和next两个参数。它可以通过context来访问请求信息和发送响应，通过next来将控制权传递给下一个中间件。

Koa的中间件处理过程符合洋葱模型，即中间件的执行顺序和结束顺序是相反的。这意味着，即使一个中间件开始处理响应，后续的中间件仍然可以对响应进行修改。

总的来说，Koa的`app.use()`方法是用来注册中间件的，而请求处理过程则是按照中间件被添加的顺序来调用中间件，每个中间件都可以访问请求信息、发送响应或将控制权传递给下一个中间件。这种处理过程符合洋葱模型，使得Koa的中间件在处理请求和响应时具有更大的灵活性。

## 数据库

- 这部分我直接跳过了，数据库的笔记我单独存放起来了

- 剩下的内容需要从day124看起

![image-20230802125422769](.\node_pictures\image-20230802125422769.png)
