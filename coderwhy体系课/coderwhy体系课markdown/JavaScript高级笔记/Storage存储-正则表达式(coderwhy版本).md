# Storage存储-正则表达式(coderwhy版本)

## (掌握)Storage-Storage的基本使用和应用

### 认识Storage

- **WebStorage主要提供了一种机制，可以让浏览器提供一种比cookie更直观的key、value存储方式：**
  - **localStorage**：本地存储，提供的是一种永久性的存储方法，在关闭掉网页重新打开时，存储的内容依然保留(cookie)
  - **sessionStorage**：会话存储，提供的是本次会话的存储，在关闭掉会话时，存储的内容会被清除；

```javascript
const token = "xiaoyu666"
const name = "xiaoyu"
const password = 123456

localStorage.setItem("token",token)
localStorage.setItem("name",name)
localStorage.setItem("password",password)

//成功保存token信息，这是将 内容 保存在本地
```

![image-20230213080502107](.\JavaScript高级_image\image-20230213080502107.png)

- 但实际中通常是会进行判断本地是否有信息的，没有的话再去服务器获取

```javascript
//获取本地的token
let token = localStorage.getItem("token")
if(!token){//判断本地是否有token
	console.log("没有token，从服务器获取");
	token = "token"
	//将服务器上的token放入本地
	localStorage.setItem("token",token)
}

//获取本地的账号密码
let username = localStorage.getItem("username")
let password = localStorage.getItem("password")
//判断本地是否有账号密码
if(!username || !password){
	console.log("让用户输入账号密码");
	//服务器获取账号密码
	username = "xiaoyu"
	password = "123456"
	//将服务器上的账号设置到本地
	localStorage.setItem("username",username)
	localStorage.setItem("password",password)
}
```

## (掌握)Storage-local和session的使用

### localStorage和sessionStorage的区别

- 我们会发现localStorage和sessionStorage看起来非常的相似
- **那么它们有什么区别呢？**
  -  验证一：关闭网页后重新打开，localStorage会保留，而sessionStorage会被删除
  -  验证二：在页面内实现跳转，localStorage会保留，sessionStorage也会保留(**就是不会开启新的页面**)，这个适合使用sessionStorage，而不是永久性存储
  - 验证三：在页面外实现跳转（打开新的网页），localStorage会保留，sessionStorage不会被保留

## (掌握)Storage-Storage的常见方法补充

### Storage常见的方法和属性(本地存储跟会话存储都有的)

- **Storage有如下的属性和方法：**

- **属性：**

  - **Storage.length**：只读属性

    `√`返回一个整数，表示存储在Storage对象中的数据项数量

    ```javascript
    console.log(Storage.length)
    ```

- **方法：**

  - **Storage.key(index)**：该方法接受一个数值n作为参数，返回存储中的第n个key名称

  - **Storage.getItem()**：该方法接受一个key作为参数，并且返回key对应的value

  -  **Storage.setItem()**：该方法接受一个key和value，并且将会把key和value添加到存储中

    `√`如果key存储，则更新其对应的值

  - **Storage.removeItem()**：该方法接受一个key作为参数，并把该key从存储中删除

  - **Storage.clear()**：该方法的作用是清空存储中的所有key

```javascript
//如果有一个需要一直固定的数值在两个地方及以上的地方用到了，我们最好将这个数值抽取到一个常量里面，防止不小心没注意修改掉导致不一致
//(这里以本地存储为例子，会话存储也是一样的)
const ACCESS_TOKEN = "token"//抽取出来的常量一般大写

//常见的方法
localStorage.setItem(access_token,"xiaoyuToken");
console.log(localStorage.getItem(access_token));

//获取本地存储按索引顺序的key值
console.log(localStorage.key(0));
console.log(localStorage.key(1));

//删掉不需要的key
localStorage.removeItem(access_token)

//清空所有key
localStorage.clear()
```

## (掌握)Storage-Cache类存储工具的封装

> 在进行这类操作的时候，我们会统一进行封装在一个文件里面，然后进行引用或者export导出、import导入

```javascript
//文件1
class Cache {
  setCache(key,value){
    localStorage.setItem(key,value)
  }

  getCache(key){
    return localStorage.getItem(key)
  }

  removeCache(key){
    localStorage.removeItem(key)
  }

  clear(){
    localStorage.clear()
  }
}

const localCache = new Cache()
```

```javascript
//文件2引入文件1
localCache.setCache("xiaoyu",666)
```

> 此时我们可能会觉得多此一举，为什么要这么做？直接用不好吗
>
> 但storage有个特点，那就是不能直接存储对象类型(复杂类型)，但是我们又想要存对象类型，那这不就得自己来处理了吗

```javascript
const userInfo = {//如果非要存的话，拿到的value也只是个类型
	name:"小余",
	nickname:"2002XiaoYu",
	level:20
}

localStorage.setItem("userInfo",userInfo)
console.log(first);
```

![image-20230214034136785](.\JavaScript高级_image\image-20230214034136785.png)

> 需要将对象转化为字符串类型，然后传入要取出的时候再从字符串转化为对象类型，那这个操作如果要每次使用都写一次的话，就太过繁琐了，而且重复代码量也会提升，最好的当然还是直接封装起来了，也能够额外扩展我们想要的很多操作

```javascript
const userInfo = {
	name:"小余",
	nickname:"2002XiaoYu",
	level:20
}

localCache.setCache("userInfo",userInfo)//成功封装
console.log(first);
```

```javascript
class Cache {
  setCache(key,value){
    if(!value){
      throw new Error("value必须有值")
    }
    if(value){
      localStorage.setItem(key,JSON.stringify(value))//进行转化为字符串
    }
  }

  getCache(key){
    const result = localStorage.getItem(key)
    if(result){//进行严谨判断，不然如果没有获取到值会获取到undefined，undefined进行转化会报错
      return JSON.parse(result)//字符串转化为对象
    }
  }

  removeCache(key){
    localStorage.removeItem(key)
  }

  clear(){
    localStorage.clear()
  }
}

const localCache = new Cache()
```

![image-20230214034815822](.\JavaScript高级_image\image-20230214034815822.png)

> 进一步修改成我们想要的，让我们自如的切换在local跟session之间

```javascript
class Cache {
  constructor(isLocal = true){
    //storage不一定就是本地存储，也可能是会话存储，我们可以将控制这个的开关也交给我们把控
    this.storage = isLocal ? localStorage : sessionStorage
  }

  setCache(key,value){
    if(!value){
      throw new Error("value必须有值")
    }
    if(value){
      this.storage.setItem(key,JSON.stringify(value))//进行转化为字符串
    }
  }

  getCache(key){
    const result = this.storage.getItem(key)
    if(result){
      return JSON.parse(result)
    }
  }

  removeCache(key){
    this.storage.removeItem(key)
  }

  clear(){
    this.storage.clear()
  }
}

const localCache = new Cache()
const sessionCache = new Cache(false)
```

## (理解)正则-正则表达式的理解和JS创建

### 什么是正则表达式？

- **我们先来看一下维基百科对正则表达式的解释：**
  - **正则表达式**（英语：Regular Expression，常简写为regex、regexp或RE），又称**正则表示式、正则表示法、规则表达式、常规表示法**，是计算机科学的一个概念
  - 正则表达式使用单个字符串来描述、匹配一系列匹配某个句法规则的字符串
  - 许多程序设计语言都支持利用正则表达式进行字符串操作
- **简单概况：正则表达式是一种字符串匹配利器，可以帮助我们搜索、获取、替代字符串；**
- **在JavaScript中，正则表达式使用RegExp类来创建，也有对应的字面量的方式：**
  - 正则表达式主要由两部分组成：模式（patterns）和修饰符（flags），也就是我们传入的两个参数

```javascript
//1.匹配的规则pattern，填入RegExp第一个参数
//2.匹配的修饰符flags，填入RegExp第二个参数
const re1 = new RegExp("hello","i")//语法糖形式

const re2 = new /规则/修饰
const re2 = new /hello/i//字面量形式的正则
```

## (理解)正则-正则表达式和普通字符串用法的对比

### 正则表达式的使用方法

- **有了正则表达式我们要如何使用它呢？**
  -  JavaScript中的正则表达式被用于 RegExp 的 exec 和 test 方法；
  - 也包括 String 的 match、matchAll、replace、search 和 split 方法；

| 方法     | 描述                                                         |                |
| -------- | ------------------------------------------------------------ | :------------: |
| exec     | 一个在字符串中执行查找匹配的 RegExp 方法，它返回一个数组（未匹配到则返回 null） | RegExp实例方法 |
| test     | 一个在字符串中测试是否匹配的 RegExp 方法，它返回 true 或 false | RegExp实例方法 |
| match    | 一个在字符串中执行查找匹配的 String 方法，它返回一个数组，在未匹配到时会返回 null |  字符串的方法  |
| matchAll | 一个在字符串中执行查找所有匹配的 String 方法，它返回一个迭代器（iterator） |  字符串的方法  |
| search   | 一个在字符串中测试匹配的 String 方法，它返回匹配到的位置索引，或者在失败时返回-1 |  字符串的方法  |
| replace  | 一个在字符串中执行查找匹配的 String 方法，并且使用替换字符串替换掉匹配到的子字符串 |  字符串的方法  |
| split    | 一个使用正则表达式或者一个固定字符串分隔一个字符串，并将分隔后的子字符串存储到数组中<br/>的 String 方法 |  字符串的方法  |

```javascript
//1.使用正则对象上的实例方法
let re1 = new RegExp()
let str = "hello world";
console.log(re1.test(str));//true

//2.使用字符串的方法，传入一个正则表达式
let message = "fdabc faBC dfABC AaaBc"
//需求：将所有的abc(忽略大小写)，换成cba
const newMessage1 = message.replaceAll("abc","cba")//有局限性，fdcba faBC dfABC AaaBc
//i = 不区分大小写，g = 全局
const newMessage2 = message.replaceAll(/abc/ig,"cba")//fdcba fcba dfcba Aacba
console.log(newMessage1,newMessage2);

//需求：将字符串中数字全部删除
// `\d`表示数字类型，`+`表示一个或者多个
let num = "105 dd5 a5dfg diff666"
let num1 = num.replaceAll(/\d+/g,"")
console.log(num1);// dd adfg diff
```

## (理解)正则-正则表达式的使用方式和方法

> 1. 使用正则对象上的实例方法
>
>    webpack -> loader -> test：匹配文件名
>
>    正则 -> 拿到文件 -> loader操作

```javascript
//1.1 test方法：检测某一个字符串是否符合正则的规则
const re1 = /abc/ig
const re2 = /^abc$/g
const message = "fdabc fa44BC df5ABC AaaBc66"

//检测某一个字符串是否符合正则的规则
if(re1.test(message)){
    console.log("你符合规则了，里面包含了大小写随意的abc")
}else{
    console.log("你里面没有包含大小写随意的abc")
}
//你符合规则了，里面包含了大小写随意的abc
const message2 = "abc"//可以再尝试一下这些其他的效果
if(re2.test(message)){
    console.log("你符合里面只有abc的规则了")
}else{
    console.log("你不符合里面只能有abc")
}
//你不符合里面只能有abc
```

### 正则案例

```javascript
//让用户输入的账号必须是 不区分大小写的xiaoyu，不能有多余内容
    输入账号：<input type="text">
    <p class="tip"></p>

const inputEl = document.querySelector("input")
const tipEl = document.querySelector(".tip")

inputEl.oninput = function(){
	const value = this.value
	if(/^xiaoyu$/ig.test(value)){
		tipEl.textContent = "账号格式正确"
	}else{
		tipEl.textContent = "输入账号规范不符合，请重新输入"
	}
}

```

### exec方法

> 使用正则执行一个字符串

```javascript
const message = "fdabc fa44BC df5ABC AaaBc66"
const re1 = /abc/ig
console.log(re1.exec(message))//index是找到位置开头的索引
```

![image-20230214065727307](.\JavaScript高级_image\image-20230214065727307.png)

### match方法

> 使用字符串的方法，传入一个正则表达式
>
> match(英文意思：匹配)：找到匹配的数据(如果要全部的数据，修饰符中就加上g，也就是全局的意思)

```javascript
const message = "fdabc fa44BC df5ABC AaaBc66"
const re1 = /abc/ig
const re2 = message.match(re1)
console.log(re2)
```

![image-20230214065933699](.\JavaScript高级_image\image-20230214065933699.png)

### matchAll方法

> 跟用修饰符g返回的结果有什么不一样？这个返回的是一个迭代器
>
> 使用matchAll方法前提：正则修饰符必须加g(因为All的前置条件就是能够全局查找)

```javascript
const message = "fdabc fa44BC df5ABC AaaBc66"
const re1 = /abc/ig
const re2 = message.matchAll(re1)
console.log(re2)

console.log(re2.next())
console.log(re2.next())
console.log(re2.next())
```

![image-20230214070656641](.\JavaScript高级_image\image-20230214070656641.png)

```javascript
//当然迭代器必不可免能够用到的当然是遍历了
const message = "fdabc fa44BC df5ABC AaaBc66"
const re1 = /abc/ig
const re2 = message.matchAll(re1)
for(item of re2){
    console.log(item)
}
```

![image-20230214071532993](.\JavaScript高级_image\image-20230214071532993.png)

### replace/replaceAll方法

> 在前面用过

### split方法

> 一个使用正则表达式或者一个固定字符串分隔一个字符串，并将分隔后的子字符串存储到数组中的 String 方法

```js
const message = "fdabc fa44BC df5ABC AaaBc66"
const re1 = /abc/ig
const result4 = message.split(re1)
console.log(result4)
```

![image-20230214071847107](.\JavaScript高级_image\image-20230214071847107.png)

### search方法

> 返回第一个符合条件的索引，没有搜索到返回-1

```javascript
const message = "fdabc fa44BC df5ABC AaaBc66"
const re1 = /abc/ig
const result5 = message.search(re1)
console.log(result5)//2
```

## (理解)正则-正则表达式规则-字符类和反向类

### 修饰符flag的使用

-  常见的修饰符：

| flag | 含义                           |
| ---- | ------------------------------ |
| g    | 全部的，给我匹配全部的(global) |
| i    | 忽略大小写(ignore)             |
| m    | 多行匹配(multiple)             |

- 需求：
  - 获取一个字符串中所有的abc；
  - 将一个字符串中的所有abc换成大写

![image-20230214072515689](.\JavaScript高级_image\image-20230214072515689.png)

### 规则 – 字符类（Character classes）

- **字符类**（Character classes） 是一个特殊的符号，匹配特定集中的任何符号

| 字符                   | 含义                                                         |
| ---------------------- | ------------------------------------------------------------ |
| \d（“d” 来自 “digit”） | 数字：从 0 到 9 的字符                                       |
| \s（“s” 来自 “space”） | 空格符号：包括空格，制表符 \t，换行符 \n 和其他少数稀有字符，例如 \v，\f 和 \r |
| \w（“w” 来自 “word”）  | “单字”字符：拉丁字母或数字或下划线 _。                       |
| .（点）                | 点 . 是一种特殊字符类，它与 “除换行符之外的任何字符” 匹配    |

- **反向类（Inverse classes）**
  - \D 非数字：除 \d 以外的任何字符，例如字母
  -  \S 非空格符号：除 \s 以外的任何字符，例如字母
  -  \W 非单字字符：除 \w 以外的任何字符，例如非拉丁字母或空格

## (理解)正则-正则表达式规则-锚点和词边界

### 规则 – 锚点（Anchors）

- **符号 ^ 和符号 $ 在正则表达式中具有特殊的意义，它们被称为“锚点”。**
  - 符号 ^ 匹配文本开头
  - 符号 $ 匹配文本末尾

```javascript
//以前字符串的方法
const message = "my name is xiaoyu"

if(message.startWith("my")){
    console.log("以my开头")
}

if(message.endWith("why")){
    console.log("以my结尾")
}
//正则的方法
if(/^my/i.test(message)){
    console.log("以my开头")
}

if(/my$/i.test(message)){
    console.log("以my结尾")
}

//如果^XXX$，则必须完全符合
```

- **词边界（Word boundary）**
  - 词边界 \b 是一种检查，就像 ^ 和 $ 一样，它会检查字符串中的位置是否是词边界
  -  词边界测试 \b 检查位置的一侧是否匹配 \w，而另一侧则不匹配 “\w”

```javascript
const message = "My name is xiaoyu"
//需求：name必须是一个单独的词，中间没有\w之类的东西
//词边界
if(/\bname\b/i.test(message)){//依靠/b XXX \b，进行划分边界
    console.log("有name")
}
```

- **在字符串 Hello, Java! 中，以下位置对应于 \b：**

![image-20230214074222085](.\JavaScript高级_image\image-20230214074222085.png)

-  **匹配下面字符串中的时间：**

```javascript
//词边界应用
const infos = "now time is 11:56 number is 123:456"
const timeRe = /\b\d\d:\d\d\b/ig	//边界 \0-9\0-9:\0-9\0-9 边界/不区分带小写、全局查找
console.log(infos.match(timeRe))
```

## (理解)正则-正则表达式规则-转义字符

### 规则 – 转义字符串

- **如果要把特殊字符作为常规字符来使用，需要对其进行转义：**
  - 只需要在它前面加个反斜杠；
- **常见的需要转义的字符：**
  - **[] \ ^ $ . | ? * + ( )**
  - 斜杠符号 ‘/’ 并不是一个特殊符号，但是在字面量正则表达式中也需要转义；
- **练习：匹配所有以.js或者jsx结尾的文件名**

```javascript
//词边界应用
const fileNames = ["node.js","three.js","Vue.js","React.js","luo.go","xiaoman.java","home.jsx"]

const jsfileRe = /\.jsx?$/i//匹配规则
///\.jsx?$/ 这里的x?表示x是0个或者1个
const newFileNames = []//创建空数组用来存放
for(const filename of fileNames){
	if(jsfileRe.test(filename)){//找出所有符合条件的，防止不符合条件的打印出来undefined
		newFileNames.push(filename)//符合的填入数组
	}
}
console.log(newFileNames);//['node.js', 'three.js', 'Vue.js', 'React.js', 'home.jsx']
```

```javascript
//优化for循环
const fileNames = ["node.js","three.js","Vue.js","React.js","luo.go","xiaoman.java","home.jsx"]
const jsfileRe = /\.jsx?$/i//匹配规则

//filter高阶函数
const newFileNames = fileNames.filter(fileName=>jsfileRe.test(fileName))
console.log(newFileNames)//['node.js', 'three.js', 'Vue.js', 'React.js', 'home.jsx']
```

- 在webpack当中，匹配文件名时就是以这样的方式。

## (理解)正则-正则表达式规则-集合和范围

### 集合（Sets）和范围（Ranges）

- **有时候我们只要选择多个匹配字符的其中之一就可以：**

  - 在方括号 […] 中的几个字符或者字符类意味着“搜索给定的字符中的任意一个”；

- **集合（Sets）**

  - 比如说，[eao] 意味着查找在 3 个字符 ‘a’、‘e’ 或者 `‘o’ 中的任意一个；

- **范围（Ranges）**

  - 方括号也可以包含字符范围
  - 比如说，[a-z] 会匹配从 a 到 z 范围内的字母，[0-5] 表示从 0 到 5 的数字
  -  [0-9A-F] 表示两个范围：它搜索一个字符，满足数字 0 到 9 或字母 A 到 F
  - \d —— 和 [0-9] 相同
  -  \w —— 和 [a-zA-Z0-9_] 相同

- **案例：匹配手机号码**：第一位是1，第二位是3、5、6、7、8、9其中之一，加上后面9位[0-9]的数字一共11位数，最后第11位就$结尾，不能再添加其他数字

  ![image-20230214082930276](.\JavaScript高级_image\image-20230214082930276.png)

- **排除范围：除了普通的范围匹配，还有类似 [^…] 的“排除”范围匹配 **

## (理解)正则-正则表达式规则-量词用法

### 量词（Quantifiers）

- **假设我们有一个字符串 +7(903)-123-45-67，并且想要找到它包含的所有数字**

  - 因为它们的数量是不同的，所以我们需要给与数量一个范围
  - 用来形容我们所需要的数量的词被称为**量词（ Quantifiers ）**

- **数量 {n}**

  - 确切的位数：{5}
  - 某个范围的位数：{3,5}   这里逗号后面不能加空格

- **缩写：**

  - +：代表“一个或多个”，相当于 {1,}
  -  ?：代表“零个或一个”，相当于 {0,1}。换句话说，它使得符号变得可选
  - *：代表着“零个或多个”，相当于 {0,}。也就是说，这个字符可以多次出现或不出现

- **案例：匹配开始或结束标签**，字符串的html元素

  > 开头/是特殊情况需要前面加\，然后?是因为如果不加就意味着这里开头就必须要有a-z其中一个字母，需要考虑下边界效应

  ![image-20230214090508401](.\JavaScript高级_image\image-20230214090508401.png)

## (理解)正则-正则表达式规则-贪婪和惰性模式

### 贪婪（ Greedy）和惰性（ lazy）模式

- **如果我们有这样一个需求：匹配下面字符串中所有使用《》包裹的内容**

```javascript
const message = "我喜欢的一些书：《龙族》-《Vue.js设计与实现》-《沙哈拉的沙漠》-《许三观卖血记》"
const result = message.match(/《.+》/g)//``.+`的意思是匹配任意内容，内容至少1个，不能无内容
console.log(result)//会打印出来['《龙族》-《Vue.js设计与实现》-《沙哈拉的沙漠》-《许三观卖血记》']，把所有的内容都连着一起打印出来了，这就是贪婪模式，找到的是龙族前面的《 跟许三观卖血记后面的》
```

- **默认情况下的匹配规则是查找到匹配的内容后，会继续向后查找，一直找到最后一个匹配的内容**
  - 这种匹配的方式，我们称之为**贪婪模式（Greedy）**
- **懒惰模式中的量词与贪婪模式中的是相反的。**(使用频率更高)
  - 只要获取到对应的内容后，就不再继续向后匹配；
  - 我们可以在量词后面再加一个问号 ‘?’ 来启用它；
  - 所以匹配模式变为 *? 或 +?，甚至将 '?' 变为 ??

```javascript
//也就是/《.+》/g变成/《.+?》/g，就是开启了惰性模式，在+后面加上问号
const message = "我喜欢的一些书：《龙族》-《Vue.js设计与实现》-《沙哈拉的沙漠》-《许三观卖血记》"
const result = message.match(/《.+?》/g)//``.+`的意思是匹配任意内容，内容至少1个，不能无内容
console.log(result)//会打印出来['《龙族》', '《Vue.js设计与实现》', '《沙哈拉的沙漠》', '《许三观卖血记》']

//惰性的会进行书名分开，而不是一口气全部输出了
```

## (理解)正则-正则表达式规则-捕获器和或操作

### 捕获组（capturing group）

- **模式的一部分可以用括号括起来 (...)，这称为“捕获组（capturing group）”**
- **这有两个作用：**
  - 它允许将匹配的一部分作为结果数组中的单独项
  - 它将括号视为一个整体；
- **方法 str.match(regexp)，如果 regexp 没有 g 标志，将查找第一个匹配并将它作为一个数组返回：**
  - 在索引 0 处：完全匹配。
  - 在索引 1 处：第一个括号的内容
  - 在索引 2 处：第二个括号的内容
  -  …等等…

```javascript
const message = "我喜欢的一些书：《龙族》-《Vue.js设计与实现》-《沙哈拉的沙漠》-《许三观卖血记》"
const result = message.matchAll(/《(.+?)》/g)//``.+`的意思是匹配任意内容，内容至少1个，不能无内容
for(item of result){
	console.log(item);
}
//第一波使用捕获组
```

![image-20230214112307091](.\JavaScript高级_image\image-20230214112307091.png)

```javascript
//就能做出如下操作
const message = "我喜欢的一些书：《龙族》-《Vue.js设计与实现》-《沙哈拉的沙漠》-《许三观卖血记》"
const result = message.matchAll(/《(.+?)》/g)//用括号 括起来，多个括号里的内容会分别排列在索引上
for(item of result){
	console.log(item[1]);//取出索引
}
```

![image-20230214112355191](.\JavaScript高级_image\image-20230214112355191.png)

- **案例：匹配到HTML标签，并且获取其中的内容**

![image-20230214113228379](.\JavaScript高级_image\image-20230214113228379.png)

### 捕获组的补充

- **命名组：**

  - 用数字记录组很困难。
  - 对于更复杂的模式，计算括号很不方便。我们有一个更好的选择：给括号起个名字。
  - 这是通过在开始括号之后立即放置 ?<name> 来完成的

  ![image-20230214113302181](.\JavaScript高级_image\image-20230214113302181.png)

- **非捕获组：**

  - 有时我们需要括号才能正确应用量词，但我们不希望它们的内容出现在结果中。
  - 可以通过在开头添加 ?: 来排除组

  ![image-20230214113326297](.\JavaScript高级_image\image-20230214113326297.png)

- **or是正则表达式中的一个术语，实际上是一个简单的“或”。**
  - 在正则表达式中，它用竖线 | 表示；
  - 通常会和捕获组一起来使用，在其中表示多个值；

```javascript
const abcRe = /(abc|cba|nba){2,}/

//{2,}表示前面的表达式匹配的次数必须是2次或更多次。例如，正则表达式/(abc|cba|nba){2,}/匹配连续出现2次或更多次的"abc"、"cba"或"nba"。例如，它将匹配"abccba"、"nbanba"、"cbacbanba"，但不匹配"abc"、"cba"或"nba"
```

