# Proxy-Reflect-Promise使用详解

> ### 作者：小余同学
>
> 为什么没有01，那是因为01是讲谷歌V8引擎的原理，我看完了，但忘记写笔记了，不想重新看一遍写笔记了hh，大致来说也是很重要的一章节
>
> 需要全系列笔记请到[2002XiaoYu (小余) (github.com)](https://github.com/2002XiaoYu)中自行获取，觉得不错给个star，这是对作者非常大的鼓励

## Proxy-Reflect使用详解

### 监听对象属性的操作

- **我们先来看一个需求：有一个对象，我们希望监听这个对象中的属性被设置或获取的过程**
  - 通过我们前面所学的知识，能不能做到这一点呢？
  -  其实是可以的，我们可以通过之前的属性描述符中的存储属性描述符来做到；

```javascript
//以前的做法

//在HTML文件
<h1 class="name"></h1>
//在JS文件
const obj = {
  name:"xiaoyu",
  age:20
}

//需求：监听对象属性的所有操作
console.log(obj.name)
obj.name = "小余"
console.log(obj.age);
obj.age = 18

//这里的操作就是响应式了
const nameEl = document.querySelector(".name")
nameEl.textContent = obj.name//小余	很显然，我们目前是最基础的操作，这样虽然也可以监听对象，但是每当我们修改了值之后，又必须手动将修改的值赋给nameEl。能不能做到我们修改了数值，就能够在控制台自动打印出来新的数值。(先不做到自动刷新显示在页面上，因为这个还要涉及到其他内容)
```

```javascript
//监听属性的操作(实时监听)
const obj = {
  name:"xiaoyu",
  age:20
}

//需求：监听对象属性的所有操作(针对一个属性)
let _name = obj.name
Object.defineProperty(obj,"name",{
  set:function(newValue){
    console.log("set监听到新的值：",newValue);
    _name = newValue
  },
  get:function(){
    console.log("get获取到新的值：",_name);
    return _name
  }
})

const nameEl = document.querySelector(".name")
nameEl.textContent = obj.name//小余
//通过改变name值，成功自动监听到打印在了控制台
obj.name = "小满"//set监听到新的值： 小满
```

> 但是上面的写法只能针对一个属性，如果要实时监听多个属性怎么办？
>
> 下面的监听多个属性的实现方式就是Vue2的**核心代码**
>
> - 弊端：
>   -  首先，Object.defineProperty设计的初衷，不是为了去监听截止一个对象中所有的属性的。
>   - 我们在定义某些属性的时候，初衷其实是定义普通的属性，但是后面我们强行将它变成了数据属性描述符。
>   - 其次，如果我们想监听更加丰富的操作，比如新增属性、删除属性，那么Object.defineProperty是无能为力的。(监听能力有限)
> - 所以我们要知道，存储数据描述符设计的初衷并不是为了去监听一个完整的对象

```javascript
const obj = {
  name:"xiaoyu",
  age:20
}

//需求：监听对象属性的所有操作
const keys = Object.keys(obj)//拿到对象所有的键
console.log(keys);//['name', 'age']
for(const key of keys){
  let value = obj[key]
  Object.defineProperty(obj,key,{
    set:function(newValue){
      console.log(`监听：给${key}设置了新的值：`,newValue);
      value = newValue//这里的value引用外部的value形成闭包，使得上层的value得以保持
    },
    get:function(){
      console.log(`获取到了${value}的值`);
      return value
    }
  })
} 

const nameEl = document.querySelector(".name")
nameEl.textContent = obj.name//小余

//能够监听到多个值
obj.name = "小满"//监听：给name设置了新的值： 小满
obj.age = 24//监听：给age设置了新的值： 24

console.log(obj.age);//获取到了24的值  24
obj.age = 6//监听：给age设置了新的值： 6
console.log(obj.age);//获取到了6的值   6
```

```javascript
//另一种形式的实现
Object.keys(obj).forEach(key => {
    let value = obj[key]
    Object.defineProperty(obj,key,{
        set:function(newValue){
            console.log(`监听到给${key}设置值`)
            value = newValue
        },
        get:function(){
            console.log(`监听到获取${key}的值`)
            return value
        }
    })
})
```

## (掌握)Proxy-监听对象属性的操作方式一

### Proxy基本使用

-  **在ES6中，新增了一个Proxy类，这个类从名字就可以看出来，是用于帮助我们创建一个代理的：**

  - 也就是说，如果我们希望**监听一个对象的相关操作**，那么我们可以**先创建一个代理对象（Proxy对象）**；
  - 之后对**该对象的所有操作**，都通过**代理对象来完成**，代理对象**可以监听我们想要对原对象进行哪些操作**

- **我们可以将上面的案例用Proxy来实现一次：**

  - 首先，我们需要**new Proxy对象**，并且**传入需要侦听的对象以及一个处理对象**，可以称之为**handler**

  - ```javascript
    const p = new Proxy(target, handler)//监听的对象 处理对象
    ```

  -  其次，**我们之后的操作都是直接对Proxy的操作**，而**不是原有的对象**，因为我们需要在handler里面进行侦听；

```javascript
const obj = {
  name:"xiaoyu",
  age:20
}

//1.创建一个Proxy对象，Proxy对象是一个类，所以使用new创建
const objProxy = new Proxy(obj,{//代理obj

})

//2.对obj的所有操作，应该去操作objProxy
console.log(objProxy.name);//xiaoyu
objProxy.name = "小余"
console.log(objProxy.name);//小余
console.log(obj);//已经被修改了，{name: '小余', age: 20}
```

### Proxy的set和get捕获器

- **如果我们想要侦听某些具体的操作，那么就可以在handler中添加对应的捕捉器（Trap）：**
- **set和get分别对应的是函数类型；**
  -  **set函数有四个参数：**
    1. target：目标对象（侦听的对象）；
    2. property：将被设置的属性key(就是侦听对象的key(你修改那个属性的key))；
    3. value：新属性值；
    4. receiver：调用的代理对象；
  - **get函数有三个参数：**
    1.  target：目标对象（侦听的对象
    2. property：被获取的属性key
    3. receiver：调用的代理对象

```javascript
const obj = {
  name:"xiaoyu",
  age:20
}

//1.创建一个Proxy对象，Proxy对象是一个类，所以使用new创建
const objProxy = new Proxy(obj,{//代理obj
  set:function(target,key,value){//target：侦听对象，key：侦听对象的key(你修改那个属性的key)，value：修改的新值
    console.log(`监听：监听${key}的设置值`);
    target[key] = value
  },

  get:function(target,key){//obj是会默认传进来参数的
    console.log(`监听:监听${key}的获取`);
    return target[key]
  }
})

//2.对obj的所有操作，应该去操作objProxy
console.log(objProxy.name);//监听:监听name的获取  xiaoyu
objProxy.name = "小余"//监听：监听name的设置值
console.log(objProxy.name);//监听:监听name的获取  小余
console.log(obj);//{name: '小余', age: 20}

objProxy.address = "福建"//监听：监听address的设置值
console.log(objProxy.address);// 监听:监听address的获取 福建，类似添加这些defineProperty做不到的操作，Proxy都可以做到  
```

## (掌握)Proxy-监听对象属性的操作方式二

### Proxy所有捕获器

#### 13个活捉器分别是做什么的呢？

> 里面有常用的将进行加粗，其他不常用的作为了解，需要用到的时候再查就可以了，知道有这个东西
>
> 想侦听什么就添加什么的捕捉器

1. handler.getPrototypeOf()
   - Object.getPrototypeOf 方法的捕捉器。
2. handler.setPrototypeOf()
   - Object.setPrototypeOf 方法的捕捉器。
3. handler.isExtensible()
   - Object.isExtensible 方法的捕捉器(判断是否可以新增属性)或者说是扩展
4. handler.preventExtensions()
   - Object.preventExtensions 方法的捕捉器
5. handler.getOwnPropertyDescriptor()
   - Object.getOwnPropertyDescriptor 方法的捕捉器
6. handler.defineProperty()
   - Object.defineProperty 方法的捕捉器
7. handler.ownKeys()
   - Object.getOwnPropertyNames 方法和Object.getOwnPropertySymbols 方法的捕捉器
8. **handler.has()**
   - in 操作符的捕捉器。
9.  **handler.get()**
   - 属性读取操作的捕捉器。
10. **handler.set()**
    -  属性设置操作的捕捉器
11. **handler.deleteProperty()**
    - delete 操作符的捕捉器
12.  handler.apply()
    -  函数调用操作的捕捉器
13. handler.construct()
    - new 操作符的捕捉器

```javascript
const obj = {
  name:"xiaoyu",
  age:20
}

//1.创建一个Proxy对象，Proxy对象是一个类，所以使用new创建
const objProxy = new Proxy(obj,{//代理obj
  set:function(target,key,value){//target：侦听对象，key：侦听对象的key(你修改那个属性的key)，value：修改的新值
    console.log(`监听：监听${key}的设置值`);
    target[key] = value
  },

  get:function(target,key){//obj是会默认传进来参数的
    console.log(`监听:监听${key}的获取`);
    return target[key]
  },

  deleteProperty:function(target,key){//使用上述第11个 delete 操作符的捕捉器
    console.log(`监听：监听删除${key}属性`);//监听：监听删除name属性
    delete obj.name
  },
  has:function(target,key){//使用上述第8个 in 操作符的捕捉器。
    console.log(`监听：监听in判断${key}属性`);
    return key in target//返回结果
  }
})

delete objProxy.name

console.log("age" in objProxy);//通过in 判断"age"有没有再objProxy里面，true
```

### Proxy的construct和apply

- **当然，我们还会看到捕捉器中还有construct和apply，它们是应用于函数对象的：**
- 用得很少，知道有这个东西就行

```javascript
function foo(){
  console.log("foo函数被调用了",this,arguments);
  return "foo"
}

const fooProxy = new Proxy(foo,{
  apply:function(target,thisArg,otherArgs){
    console.log("函数的apply侦听");
    return target.apply(thisArg,otherArgs)
  },
  construct:function(target,argArray,newTarget){
    console.log(target,argArray,newTarget);
    return new target()
  }
})

foo()
```

## (理解)Reflect-Reflect的作用和Object

### Reflect的作用

- **Reflect也是ES6新增的一个API，它是一个对象，字面的意思是反射**
- **那么这个Reflect有什么用呢？**
  - 它主要提供了很多操作JavaScript对象的方法，有点像Object中操作对象的方法；
  -  比如Reflect.getPrototypeOf(target)类似于 Object.getPrototypeOf()；
  - 比如Reflect.defineProperty(target, propertyKey, attributes)类似于Object.defineProperty() ；
- **如果我们有Object可以做这些操作，那么为什么还需要有Reflect这样的新增对象呢？**
  -  这是因为在早期的ECMA规范中没有考虑到这种**对 `对象本身` 的操作如何设计会更加规范**，所以**将这些API放到了Object上面**（但后续Object上面的新东西就越来越多，Object越来越重，对于最顶层的Object来说，身为所有类的父类，他本身不应该包含太多的东西的，因为父类里的东西是会被继承到子类中的，太多的东西必然会加重子类的负担而过于臃肿）
  - 但是**Object作为一个构造函数**，这些操作实际上**放到它身上并不合适**
  - 另外还包含一些**类似于 in、delete操作符**，让JS看起来是会有一些奇怪的
  - 所以在ES6中**新增了Reflect**，让我们这些操作都集中到了Reflect对象上
  -  另外在使用Proxy时，可以做到**不操作原对象**
-  **那么Object和Reflect对象之间的API关系，可以参考MDN文档：**
  -  https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/Comparing_Reflect_and_Object_methods

```javascript
ECMA设计规范(这是一开始的时候，考虑得不够完善)
对对象本身的操作obj
Object -> 构造函数(类)
Object.foo -> 类方法

//在ES6之后，JS做出了类方法迁移
Object类方法 -> Reflect对象
```

### 比较 Reflect 和 Object 方法

> 详细介绍了`Object` 和 `Reflect` API 上可用方法之间的差异。请注意，如果 API 中不存在某种方法，则将其标记为 N/A

| Method Name                   | `Object`                                                     | `Reflect`                                                    |
| :---------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| `defineProperty()`            | [`Object.defineProperty()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) 返回传递给函数的对象。如果未在对象上成功定义属性，则返回`TypeError`。 | 如果在对象上定义了属性，则[`Reflect.defineProperty()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/defineProperty)返回`true`，否则返回`false`。 |
| `defineProperties()`          | [`Object.defineProperties()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperties) 返回传递给函数的对象。如果未在对象上成功定义属性，则返回`TypeError`。 | N/A                                                          |
| `set()`                       | N/A                                                          | 如果在对象上成功设置了属性，则[`Reflect.set()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/set)返回`true`，否则返回`false`。如果目标不是`Object`，则抛出`TypeError` |
| `get()`                       | N/A                                                          | [`Reflect.get()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/get)返回属性的值。如果目标不是`Object`，则抛出`TypeError`。 |
| `deleteProperty()`            | N/A                                                          | 如果属性从对象中删除，则[`Reflect.deleteProperty()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/deleteProperty)返回`true`，否则返回`false`。 |
| `getOwnPropertyDescriptor()`  | 如果传入的对象参数上存在[`Object.getOwnPropertyDescriptor()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptor) ，则会返回给定属性的属性描述符，如果不存在，则返回`undefined`。 | 如果给定属性存在于对象上，则[`Reflect.getOwnPropertyDescriptor()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/getOwnPropertyDescriptor) 返回给定属性的属性描述符。如果不存在则返回`undefined`，如果传入除对象（原始值）以外的任何东西作为第一个参数，则返回`TypeError` |
| `getOwnPropertyDescriptors()` | [`Object.getOwnPropertyDescriptors()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptors) 返回一个对象，其中包含每个传入对象的属性描述符。如果传入的对象没有拥有的属性描述符，则返回一个空对象。 | N/A                                                          |
| `getPrototypeOf()`            | [`Object.getPrototypeOf()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/GetPrototypeOf)返回给定对象的原型。如果没有继承的原型，则返回`null。`在 ES5 中为非对象抛出`TypeError`，但在 ES2015 中强制为非对象。 | [`Reflect.getPrototypeOf()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/getPrototypeOf)返回给定对象的原型。如果没有继承的原型，则返回 null，并为非对象抛出`TypeError`。 |
| `setPrototypeOf()`            | 如果对象的原型设置成功，则[`Object.setPrototypeOf()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/setPrototypeOf)返回对象本身。如果设置的原型不是`Object`或`null`，或者被修改的对象的原型不可扩展，则抛出`TypeError`。 | 如果在对象上成功设置了原型，则[`Reflect.setPrototypeOf()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/setPrototypeOf) 返回 true，否则返回 false（包括原型是否不可扩展）。如果传入的目标不是`Object`，或者设置的原型不是`Object`或`null`，则抛出`TypeError`。 |
| `isExtensible()`              | 如果对象是可扩展的，则 Object.isExtensible（）返回 true，否则返回 false。如果第一个参数不是对象（原始值），则在 ES5 中抛出`TypeError`。在 ES2015 中，它将被强制为不可扩展的普通对象并返回`false`。 | 如果对象是可扩展的，则[`Reflect.isExtensible()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/isExtensible) 返回`true`，否则返回`false`。如果第一个参数不是对象（原始值），则抛出`TypeError`。 |
| `preventExtensions()`         | [`Object.preventExtensions()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/preventExtensions) 返回被设为不可扩展的对象。如果参数不是对象（为原始值），则在 ES5 中抛出 `TypeError`。在 ES2015 中，则将参数视为不可扩展的普通对象，并返回对象本身。 | 如果对象已变得不可扩展，则 [`Reflect.preventExtensions()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/preventExtensions) 返回 `true`，否则返回 `false`。如果参数不是对象（为原始值），则抛出 `TypeError`。 |
| `keys()`                      | [`Object.keys()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)返回一个字符串`数组`，该字符串映射到目标对象自己的（可枚举）属性键。如果目标不是对象，则在 ES5 中抛出`TypeError`，但将非对象目标强制为 ES2015 中的对象 | N/A                                                          |
| `ownKeys()`                   | N/A                                                          | [`Reflect.ownKeys()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/ownKeys)返回一个属性名称数组，该属性名称映射到目标对象自己的属性键。如果目标不是`Object`，则抛出`TypeError`。 |

```javascript
const obj = {
  name:"小余",
  age:20
}

//你要如何判断obj.name有没有删除成功？
delete obj.name//其实就单纯这句话是很难判断是否执行成功的
// obj.name = "小满"
//判断的方式有很多种,可以这样,也可以if判断,或者三元表达式,有很多种方法(使用if方法在我们使用defineProperty设置无法配置的时候，会报错，所以用if判断obj.name的话有更好的方式去判断，放在下面Reflect的判断是否删除属性中)
const judgment = obj.name || "删除成功"  
console.log(judgment);
//甚至可以一行解决判断问题，但是在不依靠这些的时候是很难判断是否删除成功的
console.log(obj.name ??= "删除成功")
```

```javascript
//还有通过禁止删除的方式来进行的

const obj = {
  name:"小余",
  age:20
}

//通过设置属性让其无法配置，也包括了删除
Object.defineProperty(obj,"name",{
  configurable:false
})

delete obj.name
const judgment = obj.name || "删除成功"  
console.log(judgment);//小余

console.log(obj.name ??= "删除成功")//小余
//显示了name的内容，证明了删除失败
```

> 使用Reflect进行判断是否删除属性

```javascript
Reflect.deleteProperty(obj,"name")//判断obj里的name属性是否被删除,返回布尔值类型
//关键在于返回布尔值类型，可以直接用作if判断，会更加明朗一点，不过上述的方法也可以使用。Reflect更多的是在框架中使用
if(Reflect.deleteProperty(obj,"name")){//判断obj里的name属性是否被删除,返回布尔值类型
    console.log("删除成功")
}else{
    console.log("删除失败")
}
```

## (理解)Reflect-Reflect和Proxy共同

### Reflect的常见方法

**Reflect中有哪些常见的方法呢？它和Proxy是一一对应的，也是13个：**

> 加粗的部分是常用的

- Reflect.getPrototypeOf(target)

  -  类似于 Object.getPrototypeOf()。

  - 用于获取一个对象的原型（也称为 `prototype`）。

    该方法返回该对象的原型对象，即该对象继承的对象。如果该对象没有继承任何对象，则返回 `null`。

- eflect.setPrototypeOf(target, prototype)

  - 设置对象原型的函数. 返回一个 Boolean， 如果更新成功，则返回true

- Reflect.isExtensible(target)

  - 类似于 Object.isExtensible()
  - 用于判断一个对象是否可以被扩展，即是否可以添加新的属性
  - 该方法返回一个布尔值，表示该对象是否可以被扩展，即是否可以通过 `Object.defineProperty()` 或者直接赋值添加新的属性

- Reflect.preventExtensions(target)

  -  类似于 Object.preventExtensions()。返回一个Boolean

  - 用于阻止一个对象被扩展，即不允许添加新的属性。

    该方法返回一个布尔值，表示该对象是否被阻止了扩展，即是否不允许添加新的属性。

    其中，`target` 是要阻止扩展的对象。

-  Reflect.getOwnPropertyDescriptor(target, propertyKey)

  - 类似于 Object.getOwnPropertyDescriptor()。如果对象中存在该属性，则返回对应的属性描述符, 否则返回 undefined

- Reflect.defineProperty(target, propertyKey, attributes)

  - 和 Object.defineProperty() 类似。如果设置成功就会返回 true

- Reflect.ownKeys(target)

  - 返回一个包含所有自身属性（不包含继承属性）的数组。(类似于Object.keys(), 但不会受enumerable影响)

- **Reflect.has(target, propertyKey)**

  -  判断一个对象是否存在某个属性，和 in 运算符 的功能完全相同

- **Reflect.get(target, propertyKey[, receiver])**

  - 获取对象身上某个属性的值，类似于 target[name]。

- **Reflect.set(target, propertyKey, value[, receiver])**

  - 将值分配给属性的函数。返回一个Boolean，如果更新成功，则返回true

- **Reflect.deleteProperty(target, propertyKey)**

  - 作为函数的delete操作符，相当于执行 delete target[name]。

- Reflect.apply(target, thisArgument, argumentsList)

  - 对一个函数进行调用操作，同时可以传入一个数组作为调用参数。和Function.prototype.apply() 功能类似

- Reflect.construct(target, argumentsList[, newTarget])

  - 对构造函数进行 new 操作，相当于执行 new target(...args)

### Reflect的使用

-  那么我们可以将之前Proxy案例中对原对象的操作，都修改为Reflect来操作：
  - 修改成Reflect进行操作肯定是有好处

```javascript
const obj = {
  name:"小余",
  age:20
}

const objProxy = new Proxy(obj,{
  set:function(target,key,newValue,receiver){
    //下面这种写法好不好,规范吗? 有点奇怪，因为直接操作原对象了
    // target[key] = value
    //代理对象的目的:不再直接操作原对象,所以我们采用间接操作的方式(好处一)
    //从语言层面通过反射去操作
    const isSuccess = Reflect.set(target,key,newValue)
    //Reflect.set会返回布尔值,可以判断本次操作是否成功(好处二)
    if(!isSuccess){
      throw new Error(`set${key}failure`)
    }
  },
  get:function(target,key,receiver){

  }
})

//操作代理对象
objProxy.name = "xiaoyu"
console.log(obj);//{name: 'xiaoyu', age: 20},修改成功
```

## (理解)Reflect-Reflect和receive

> 回顾：
>
> 刚刚在上面讲了使用Reflect代替的两点好处:
>
> 1. 代理对象的目的:不再直接操作原对象
> 2. Reflect.set会返回布尔值,可以判断本次操作是否成功
>
> 接下来让我们看看好处3是什么

### Receiver的作用

- **我们发现在使用getter、setter的时候有一个receiver的参数，它的作用是什么呢？**
  - Reflect就是外层的Proxy
  -  如果我们的源对象（obj）有setter、getter的访问器属性，那么可以通过receiver来改变里面的this

```javascript
const obj = {
  name:"小余",
  age:20
}

const objProxy = new Proxy(obj,{
  set:function(target,key,newValue,receiver){
      //好处3：receiver就是外层Proxy对象
    console.log(objProxy === receiver);//true
  },
  get:function(target,key,receiver){

  }
})
```

```javascript
//下面一共是两层，产生了闭包。这里的set name里面的this默认是obj对象。而Reflect.set的最后一个参数就是用来修改这里的this指向的
const obj = {
  _name:"小余",//第二层,set name里面的值修改了外面的_name属性里的内容
  age:20,
  set name(newValue){
    this._name = newValue//第一层,这里赋值给this._name外面传进入的newValue
  },
  get name(){
    return this._name
  }
}

const objProxy = new Proxy(obj,{
  set:function(target,key,newValue,receiver){
    //下面这种写法好不好,规范吗?
    // target[key] = value
    //代理对象的目的:不再直接操作原对象,所以我们采用间接操作的方式(好处一)
    //从语言层面通过反射去操作
    const isSuccess = Reflect.set(target,key,newValue,receiver)
    console.log(objProxy === receiver);//true
    //Reflect.set会返回布尔值,可以判断本次操作是否成功(好处二)
    if(!isSuccess){
      throw new Error(`set${key}failure`)
    }
  },
  get:function(target,key,receiver){
    //proxy中获取方法被调用 会打印两次,原因是:receiver也调用了一次,去掉receiver就只剩下一次了
    console.log("proxy中获取方法被调用");
    return Reflect.get(target,key,receiver)
  }
})

//操作代理对象
objProxy.name = "xiaoyu"//这里设置的话依旧会成功的，虽然我们属性值修改成_name了，但此时调用的变成了set name，传入"xiaoyu"
console.log(objProxy.name);//xiaoyu
```

### Reflect的construct

> 我们创建出来的类型是Animal，但是我们却想要执行Student里面的代码。这时候我们就可以使用反射

```javascript
function Person(name,age){
  this.name = name
  this.age = age
}

function Student(name,age){

}

//我们创建什么类型,将那个类型放到最后,比如我们这里要创建Student类型,我Student类型就放到了最后,但是我们要执行Person的代码,所以Person放到最前面,中间则放我们传入的参数
const stu = Reflect.construct(Person,["小余",20],Student)//执行的部分，参数，创建的类型
console.log(stu.__proto__ === Student.prototype);//true
console.log(stu);//Student {name: '小余', age: 20}
```

```javascript
//之前的用法
function Person(name,age){
  this.name = name
  this.age = age
}

function Student(name,age){
  Person.call(this,age,name)//通过call将name和age跟随this去到了Person借用。this指向在下面new关键字中产生了变化
}

const stu = new Student("小余",20)
console.log(stu);//Student {name: 20, age: '小余'}
```

# Promise使用详解

## (理解)Promise-异步代码处理存在的困境

### 异步任务的处理

- **在ES6出来之后，有很多关于Promise的讲解、文章，也有很多经典的书籍讲解Promise**
  - 虽然等你学会Promise之后，会觉得Promise不过如此；
  - 但是在初次接触的时候都会觉得**这个东西不好理解**；
- **那么这里我从一个实际的例子来作为切入点：**
  -  我们调用一个函数，这个函数中发送网络请求（我们可以用定时器来模拟）
  -  如果**发送网络请求成功**了，那么**告知调用者发送成功**，**并且将相关数据返回过去**
  - 如果**发送网络请求失败**了，那么**告知调用者发送失败**，**并且告知错误信息**

```javascript
function execCode(){
  setTimeout(()=>{
    console.log("你好 小余");
    console.log("重铸前端荣光");
    let total = 0
    for(let i = 0;i<100;i++){
      total +=1
    }
  },2000)
}

//这里要如何知道上面函数里面的内容准备好了?在真实的环境中,我们网络请求可能快也可能慢,不可能像定时器清楚的知道是固定两秒钟
execCode()
```

```javascript
//做法：
function execCode(callback){
  setTimeout(()=>{
    console.log("你好 小余");
    console.log("重铸前端荣光");
    let total = 0
    for(let i = 0;i<100;i++){
      total +=1
    }
    callback()//准备好了就执行,相当于提醒外面的我们,里面已经准备好了
  },2000)
}

execCode(()=>{
	console.log("函数里面准备好了");
})//我们传入一个回调函数,相当于等你函数里面执行完再叫我的意思
```

> 当控制台打印出：`函数里面准备好了`这句话的时候，就已经说明准备好了

![image-20230203073853659](.\JavaScript高级_image\image-20230203073853659.png)

> 甚至可以进行定制性的返回信息

```javascript
function execCode(callback){
    //异步任务
  setTimeout(()=>{
    console.log("你好 小余");
    console.log("重铸前端荣光");
    let total = 0
    for(let i = 0;i<5;i++){
      total +=1
    }
    callback(total)//准备好了就执行,相当于提醒外面的我们,里面已经准备好了
  },2000)
}

execCode((value)=>{
  console.log(`你买的${value}个二次元手办已经到货了`);//你买的5个二次元手办已经到货了
})//我们传入一个回调函数,相当于等你函数里面执行完再叫我的意思
```

> 在上面的基础上，如果超过了一定的时间或者不满足了什么条件(比如下面的条件就是counter>0)，我们就不进行原来的操作了，可以提示用户信息超时了也可以执行另外的东西
>
> - 这是早期处理异步的方式，现在已经有了更好的方式了，但不妨碍我们学习其中的思想

```javascript
function execCode(counter,successCallback,failureCallback){//参数1:传入的数据,参数2:成功的请求处理,参数3:失败的请求处理
  setTimeout(()=>{
    console.log("你好 小余");
    console.log("重铸前端荣光");
    if(counter > 0){//counter可以计算的情况,成功的情况
      let total = 0
      for(let i = 0;i<counter;i++){
        total +=1
      }
      successCallback(total)
    }else{//失败的情况,counter有问题
      failureCallback(`${counter}有问题`)//执行失败的流程
    }
  },2000)
}

execCode(100,(value)=>{
  console.log(`本次执行成功了:${value}`);
},(err)=>{
  console.log(`本次执行失败了:${err}`);
})
```

## (掌握)Promise解决的问题

### 什么是Promise呢？

- **在上面的解决方案中，我们确确实实可以解决请求函数得到结果之后，获取到对应的回调，但是它存在两个主要的问题：**
  - 第一，我们**需要自己来设计回调函数、回调函数的名称、回调函数的使用**等
  - 第二，**对于不同的人、不同的框架设计出来的方案是不同**的，那么我们必须耐心**去看别人的源码或者文档**，以便可以理解它
    这个函数到底怎么用。那这个就太花费时间了，对人的要求也会变得很高
- **我们来看一下Promise的API是怎么样的：**
  -  **Promise是一个类**，可以翻译成 **承诺、许诺 、期约**；
  -  当我们需要的时候，给**予调用者一个承诺**：待会儿**我会给你回调数据时**，就可以**创建一个Promise的对象**
  -  在通过new创建Promise对象时，我们需要**传入一个回调函数**，我们称之为**executor**
    - 这个回调函数**会被立即执行**，并且给**传入另外两个回调函数resolve、reject**；
    -  当我们**调用resolve回调函数**时，**会执行Promise对象的then方法**传入的回调函数；
    - 当我们**调用reject回调函数**时，**会执行Promise对象的catch方法**传入的回调函数；

### Promise的代码结构

```javascript
const promise = new Promise((resolve,reject)=>{
  //调用resolve,那么then传入的的回调会被执行
  resolve("调用成功")
  //调用reject,那么catch传入的回调会被执行
  reject("调用失败")
})

promise.then(res =>{
  console.log(res);
})
promise.catch(err =>{
  console.log(err);
})
```

```javascript
//创建promise对象
const promise = new Promise((resolve,reject)=>{
  //1.在执行resolve跟reject之前的状态叫做待定状态(pending)，还没决定我们是否调用成功
  console.log("小满穿黑丝");
  console.log("快开学了");
  console.log("小余要去上学了");
    //2.兑现状态：fulfilled
  resolve("调用成功")
  resolve("调用成功1")
  resolve("调用成功2")//只会调用第一次
  console.log("-------------");//放在resolve后面调用,也会调用成功,而且会比resolve调用得更早(涉及宏任务和微任务)
    //3.拒绝状态rejected
    reject()
})

promise.then(res =>{
  console.log(res);
}).catch(err =>{
  console.log(err);
})
```

- **上面Promise使用过程，我们可以将它划分成三个状态：**

  - **待定（pending）**: 初始状态，既没有被兑现，也没有被拒绝

    `√`当执行executor中的代码时，处于该状态；

  -  **已兑现（fulfilled）**: 意味着操作成功完成；

    `√`执行了resolve时，处于该状态，Promise已经被兑现

  - **已拒绝（rejected）**: 意味着操作失败；

    `√`执行了reject时，处于该状态，Promise已经被拒绝；

- **注意：Promise的状态一旦被确定下来，就不会再更改，也不能再执行某一个回调函数来改变状态**

  - 这也解释了为什么上面连续调用三次resolve只有第一次的调用成功，其他的没有反应，因为Promise的状态已经被确认下来了。后面两行不是没有执行，他执行了，只是没法生效而已，无法改变已经被锁死的状态

| 执行了`resolve()`       | Promise状态会变成`fulfilled`，即 **已完成状态**              |
| ----------------------- | ------------------------------------------------------------ |
| 执行了`reject()`        | Promise状态会变成`rejected`，即 **被拒绝状态**               |
| Promise只以`第一次为准` | 第一次成功就`永久`为`fulfilled`，第一次失败就永远状态为`rejected` |
| Promise中有`throw`      | 相当于执行了`reject()`                                       |

```javascript
//对比以前的调用
//以前
execCode(100,(value)=>{
  console.log(`本次执行成功了:${value}`);
},(err)=>{
  console.log(`本次执行失败了:${err}`);
})
//现在，逻辑更加清晰
execCode(100).then(value=>{
    console.log("成功：",value)
}).catch(err=>{
    console.log("失败：",err)
})
```

> coderwhy视频中的案例

```javascript
function execCode(counter){
  const promise = new Promise((resolve,reject)=>{
    //异步任务
    setTimeout(()=>{
      if(counter > 0){
        let total = 0
        for(let i =0;i < counter;i++){
          total += i
        }
        //成功的回调
        resolve(total)
      }else{
        //失败的回调
        reject(`${counter}有问题`)
      }
    },3000)
  })
  return promise
}

execCode(100).then(value=>{
  console.log("成功：",value)
}).catch(err=>{
  console.log("失败：",err)
})//成功： 4950
```

## (掌握)Promise的各个状态-Executor

- **Executor是在创建Promise时需要传入的一个回调函数，这个回调函数会被立即执行，并且传入两个参数：**

  ```javascript
  new Promise((resolve,reject)=>{
      console.log("executor代码")//初步创建的时候，里面的代码会马上执行
  })
  ```

- **通常我们会在Executor中确定我们的Promise状态：**

  - 通过**resolve**，可以兑现（**fulfilled**）Promise的状态，我们也可以称之为已决议（resolved）
  - 通过**reject**，可以拒绝（**reject**）Promise的状态

- **这里需要注意：一旦状态被确定下来，Promise的状态会被 锁死，该Promise的状态是不可更改的**
  -  在我们**调用resolve**的时候，如果**resolve传入的值本身不是一个Promise**，那么**会将该Promise的状态变成 兑现（fulfilled）**
  - 在**之后我们去调用reject时，已经不会有任何的响应**了（并不是这行代码不会执行，而是无法改变Promise状态）

```javascript
//创建promise对象
const promise = new Promise((resolve,reject)=>{
  resolve("调用成功")//就是这里面的代码是立即执行的部分，也就是Executor的部分
  resolve("调用成功1")
  resolve("调用成功2")//只会调用第一次
})

promise.then(res =>{
  console.log(res);
}).catch(err =>{
  console.log(err);
})
```

## (理解)Promise-Promise中resolve

### resolve不同值的区别

- **情况一：如果resolve传入一个普通的值或者对象，那么这个值会作为then回调的参数**

```javascript
//创建promise对象
const promise = new Promise((resolve,reject)=>{
  //1.普通值
  resolve("普通值")
  resolve({name:"小余"})//对象这也是普通值
  //包括数组这些都算是普通值
  resolve([
    {name:"MacBook",price:19999,intro:"贵到姥姥家"},
    {name:"ikun",price:9.9,intro:"鸡你太美"}
  ])
})

promise.then(res =>{
  console.log(res);
})
```

-  **情况二：如果resolve中传入的是另外一个Promise，那么这个新Promise会决定原Promise的状态：**

  > 这种情况平时不多见，通常见于框架源码

```javascript
//创建promise对象
const p = new Promise((resolve,reject)=>{
  setTimeout(()=>{
    resolve("p的resolve")
  },2000)
})
const promise = new Promise((resolve,reject)=>{
  //2.resolve中传入的是另外一个Promise
  resolve(p)
})
//我们调用了promise，但是发挥作用的确是p，因此看出新的Promise决定了原来Promise的状态
promise.then(res =>{
  console.log(res);//p的resolve
})
```

- **情况三：如果resolve中传入的是一个对象，并且这个对象有实现then方法，那么会执行该then方法，并且根据then方法的结**
  **果来决定Promise的状态：**

```javascript
//创建promise对象
const promise = new Promise((resolve,reject)=>{
  //3.resolve(thenable对象)
  resolve({
    name:"小余",
    //resolve里面有一个then方法,那就会直接调用这里面的then方法
    then:function(resolve){
      resolve("这是resolve里面的then方法")
    }
  })
})

promise.then(res =>{
  console.log(res,"这是外面promise的then方法");
})

//控制台打印结果：
//这是resolve里面的then方法 这是外面promise的then方法
```

## (了解)Promise-Promise的then和catch

### then方法 – 接受两个参数

-  **then方法是Promise对象上的一个方法（实例方法）：**
  - 它其实是放在Promise的原型上的 Promise.prototype.then
-  **then方法接受两个参数：**
  -  **fulfilled的回调函数**：当状态变成fulfilled时会回调的函数；
  - **reject的回调函数**：当状态变成reject时会回调的函数

```javascript
promise.then(res=>{
  console.log(res);
},err=>{
  console.log(err);
})
//上面代码等价于下面这个
promise.then(res =>{
  console.log(res);
}).catch((err)=>{
  console.log(err);
})
```

### then方法 – 多次调用

- **一个Promise的then方法是可以被多次调用的：**
  - 每次调用我们都可以**传入对应的fulfilled回调**；
  - 当Promise的状态**变成fulfilled的时候**，这些**回调函数都会被执行**；

```javascript
//相当于会调用4次，都会执行
promise.then(res =>{
  console.log(res);
})
promise.then(res =>{
  console.log(res);
})
promise.then(res =>{
  console.log(res);
})
promise.then(res =>{
  console.log(res);
})
```

### then方法 – 返回值

- **then方法本身是有返回值的，它的返回值是一个Promise，所以我们可以进行如下的链式调用：**

  - 但是**then方法返回的Promise到底处于什么样的状态呢**？

- **Promise有三种状态，那么这个Promise处于什么状态呢？**

  - 当**then方法中的回调函数本身在执行的时候，那么它处于pending**状态

  - 当then**方法中的回调函数返回一个结果时**

    `√`情况1：返回一个普通的值，那么它**处于fulfilled状态**，并且**会将结果作为resolve的参数**

    `√`情况2：返回一个Promise，由新的Promise的状态来决定

    `√`情况3：返回一个thenable值，会调用then方法，决定状态

  -  当**then方法抛出一个异常时，那么它处于reject状态**

```javascript
//创建promise对象
const promise = new Promise((resolve,reject)=>{
  reject('failure')
})

//我们上面执行了reject,也就是错误的回调,但是我们这里then跟catch是分开写的.then会不会接收到reject的信息?
//答案是会的,上面reject执行会传入下面5个调用的过程,包括只有then的也不例外,但是我们then后面都没有写catch,怎么接收?
//接收不了,所以会报错?错误信息:Uncaught (in promise) failure,也就是没有在promise中发现failure
promise.then(res =>{
  console.log("成功的回调",res);
})

promise.catch(err =>{
  console.log("失败的回调",err);
})
promise.catch(err =>{
  console.log("失败的回调",err);
})
promise.catch(err =>{
  console.log("失败的回调",err);
})
promise.catch(err =>{
  console.log("失败的回调",err);
})
```

### catch方法 – 多次调用

-  **catch方法也是Promise对象上的一个方法（实例方法）：**
  - 它也是放在Promise的原型上的 Promise.prototype.catch
-  **一个Promise的catch方法是可以被多次调用的：**
  - 每次调用我们**都可以传入对应的reject回调**
  - 当**Promise的状态变成reject的时候，这些回调函数都会被执行**

```javascript
//就刚刚上面不断重复调用的catch
promise.catch(err =>{
  console.log("失败的回调",err);
})
promise.catch(err =>{
  console.log("失败的回调",err);
})
promise.catch(err =>{
  console.log("失败的回调",err);
})
promise.catch(err =>{
  console.log("失败的回调",err);
})
```

## (理解)Promise-then方法的返回新Promise

### catch方法 – 返回值

- **事实上catch方法也是会返回一个Promise对象(新的)的，所以catch方法后面我们可以继续调用then方法或者catch方法：**

  - 下面的代码，后续是catch中的err2打印，还是then中的res打印呢？

    ```javascript
    promise.catch(err =>{
      console.log("err1",err);
    }).catch(err =>{
      console.log("err2",err);
    }).then(res =>{
      console.log("res",res);
    })
    
    //不管是then还是catch都是返回promise，所以我们可以then基础上继续.then。这个叫做链式调用
    //then方法是返回一个新的Promise，链式中的then是在等待这个新的Promise有决议之后执行。(意思就是第一个then最后应该有个return返回新的Promise给下一个then或者catch继续调用，适用于链式调用的情况)
    ```

  - 答案是res打印，这是因为catch传入的回调在执行完后，**默认状态依然会是fulfilled的**

- **那么如果我们希望后续继续执行catch，那么需要抛出一个异常：**

  - ```javascript
    promise.catch(err =>{
      console.log("err1",err);
        throw new Error("error message")//抛出异常(然后直接跳到catch部分)，会中断函数继续执行(我们直接中断函数执行是通过return)
    }).then(res =>{
      console.log("res",res);//then接住了并传递给了后续连接着的catch
    }).catch(err =>{
      console.log("err2",err);
    })
    
    //暂时性中断执行：yield(后面会写)
    ```

### finally方法

- **finally是在ES9（ES2018）中新增的一个特性：表示无论Promise对象无论变成fulfilled还是rejected状态，最终都会被执行**
  **的代码**
- **finally方法是不接收参数的，因为无论前面是fulfilled状态，还是rejected状态，它都会执行**

```javascript
const promise = new Promise((resolve,reject)=>{
    reject("reject")//如果重复的代码重复的放到resolve跟reject的里面是更加臃肿的，我们使用finally更好一些
})

promise.then(res =>{
    console.log("res：",res)
}).catch(err =>{
    console.log("err：",err)
}).finally(()=>{//不管执行then还是catch。这里的finally是一定会执行
    console.log("finally action")
})
```

## (理解)Promise类方法-resolve和reject

### resolve方法

- **前面我们学习的then、catch、finally方法都属于Promise的实例方法，都是存放在Promise的prototype上的**

  - **下面我们再来学习一下Promise的类方法**

- **(使用场景)：有时候我们已经有一个现成的内容了，希望将其转成Promise来使用，这个时候我们可以使用 Promise.resolve 方法来完成**

  - **Promise.resolve的用法相当于new Promise，并且执行resolve操作**

  ```javascript
  Promise.resolve("小余")
  //等价于
  new Promise((resolve)=> resolve("小余"))
  
  //----------------------------------------------------(使用方式：简化Promise的写法，只考虑接收成功的情况)
  Promise.resolve("小余").then(res=>{
    console.log(`${res}一定将coderwhy的课程学完`);//小余一定将coderwhy的课程学完
  })
  //对于上面的写法也可以像下面分开写写会更灵活(真实开发更接近下方的写法)
  const promise = Promise.resolve("小余")
  promise.then(res=>{
    console.log(`${res}一定将coderwhy的课程学完`);//小余一定将coderwhy的课程学完
  })
  ```

- **resolve参数的形态：**

  - 情况一：参数是一个普通的值或者对象
  - 情况二：参数本身是Promise
  - 情况三：参数是一个thenable

### reject方法

- **reject方法类似于resolve方法，只是会将Promise对象的状态设置为reject状态。**
- **Promise.reject的用法相当于new Promise，只是会调用reject：**

```javascript
Promise.reject("小余")
//相当于
new Promise((resolve,reject) =>reject("小余"))
//但通常我们不使用的参数都是通过下划线替代
new Promise((_,reject) =>reject("小余"))

//其他写法就跟resolve方法差不多
```

- **Promise.reject传入的参数无论是什么形态，都会直接作为reject状态的参数传递到catch的。**

## (掌握)Promise类方法-all方法的使用

- **另外一个类方法是Promise.all：**

  - 它的作用是**将多个Promise包裹在一起形成一个新的Promise**

  - **新的Promise状态由包裹的所有Promise共同决定：**

    `√` 当**所有的Promise状态变成fulfilled状态**时，**新的Promise状态为fulfilled**，并且**会将所有Promise的返回值组成一个数组**

    `√` 当**有一个Promise状态为reject**时，**新的Promise状态为reject**，并且**会将第一个reject的返回值作为参数**

```javascript
const p1 = new Promise((resolve,reject)=>{
  setTimeout(()=>{
    resolve("p1 resolve")
  },3000)
})

const p2 = new Promise((resolve,reject)=>{
  setTimeout(()=>{
    resolve("p1 resolve")
  },2000)
})

const p3 = new Promise((resolve,reject)=>{
  setTimeout(()=>{
    resolve("p1 resolve")
  },5000)
})

//all:所有
Promise.all([p1,p2,p3]).then(res => {
  //这条内容会在5秒后执行，因为要等所有的promise都决议了，这个才会决议，且结果是数组格式
  console.log("all promise res：",res);//all promise res： (3) ['p1 resolve', 'p1 resolve', 'p1 resolve']
})//将内容放入可迭代的数组中

//上面一共创建了4个promise，前三个是p1，p2，p3，最后一个则是promise.all，这最后一个的状态由前三个决定(三个都出结果后才会执行最后一个决议的结果)
```

```javascript
//如果p1就出现了错误reject，会怎么进行？
const p1 = new Promise((resolve,reject)=>{
  setTimeout(()=>{
    reject("p1 reject error")
  },3000)
})

const p2 = new Promise((resolve,reject)=>{
  setTimeout(()=>{
    resolve("p2 resolve")
  },2000)
})

const p3 = new Promise((resolve,reject)=>{
  setTimeout(()=>{
    resolve("p3 resolve")
  },5000)
})

Promise.all([p1,p2,p3]).then(res => {
  //这条内容会在5秒后执行，因为要等所有的promise都决议了，这个才会决议，且结果是数组格式
  console.log("all promise res：",res);//all promise res： (3) ['p1 resolve', 'p1 resolve', 'p1 resolve']
}).catch(err =>{
  console.log("all promise err：",err)//如果中途出现了reject错误，就会马上中止继续往下读取并通过catch返回错误信息
})
```

> 在实际应用中也是有经常使用的，假设有三个网络请求，我们希望这三个网络请求都要有结果的时候，就可以通过这种方式

## (了解)Promise类方法-allSettled-race

### allSettled方法

- **all方法有一个缺陷：当有其中一个Promise变成reject状态时，新Promise就会立即变成对应的reject状态**

  -  那么对于resolved的，以及依然处于pending状态的Promise，我们是获取不到对应的结果的

- **在ES11（ES2020）中，添加了新的API `Promise.allSettled`**

  - 该方法**会在所有的Promise都有结果（settled），无论是fulfilled，还是rejected时，才会有最终的状态**
  - 并且**这个Promise的结果一定是fulfilled的**

  ```javascript
  Promise.allSettled([p1,p2,p3]).then(res => {
    console.log("all promise res：",res)
  }).catch(err =>{
    console.log("all promise err：",err)
  })
  ```

- **打印的结果：**

  -  allSettled的结果是一个数组，数组中存放着每一个Promise的结果，并且是对应一个对象的
  - 这个对象中**包含status状态，以及对应的value值**

![image-20230205201732623](.\JavaScript高级_image\image-20230205201732623.png)

### race方法

- **如果有一个Promise有了结果，我们就希望决定最终新Promise的状态，那么可以使用race方法：**
  - **race是竞技、竞赛**的意思，表示**多个Promise相互竞争**，谁**先有结果，那么就使用谁的结果**

```javascript
const p1 = new Promise((resolve,reject)=>{
  setTimeout(()=>{
    reject("p1 reject error")
  },3000)
})

const p2 = new Promise((resolve,reject)=>{
  setTimeout(()=>{
    resolve("p2 resolve")
  },2000)
})

const p3 = new Promise((resolve,reject)=>{
  setTimeout(()=>{
    resolve("p3 resolve")
  },5000)
})

//类方法，reve方法  
Promise.race([p1,p2,p3]).then(res => {
  console.log("res：",res)
}).catch(err =>{
  console.log("err：",err)
})
//res： p2 resolve，输出最早打印出来的
```

### any方法

-  **any方法是ES12中新增的方法，和race方法是类似的：**

  - **any方法会等到一个fulfilled状态，才会决定新Promise的状态**
  -  如果**所有的Promise都是reject**的，那么**也会等到所有的Promise都变成rejected状态**
  - **上面`race`是不管结果好坏，只要第一个。而`any`方法是必须要遇到第一个好的结果才会决定状态**

  ```javascript
  Promise.any([p1,p2,p3]).then(res => {
    console.log("res：",res)
  }).catch(err =>{
    console.log("err：",err)
  })
  ```

  -  **如果所有的Promise都是reject的，那么会报一个AggregateError的错误**

# Iterator-Generator详解

> Iterator：迭代器，这个跟`可迭代对象`不是一个意思
>
> Generator：生成器 =>使用yield控制

## (理解)迭代器-迭代器理解和JavaScript中的迭代

### 什么是迭代器？

- **迭代器**（iterator），使用户**在容器对象（container，例如链表或数组）上遍访的对象**，使用该接口**无需关心对象的内部实现细节**(细节指我们不需要关系他是数组还是链表或者说是哈希表、树结构这些，统统不需要关注)

  - 其行为**像数据库中的光标**，迭代器最早出现**在1974年设计的CLU编程语言中**

    > 我们有迭代器能去访问容器的一个元素(容器里面通常很多个元素)，迭代器会一个个访问迭代器里的元素。乍一看这不就是索引，但其实索引是迭代器在遍历的过程当中会做的事情

  - 在各种编程语言的实现中，**迭代器的实现方式各不相同**，但是**基本都有迭代器，比如Java、Python等**

- **从迭代器的定义我们可以看出来，迭代器是帮助我们对某个数据结构进行遍历的对象**

- **在JavaScript中，迭代器也是一个具体的对象，这个对象需要符合迭代器协议（iterator protocol）：**

  - 迭代器协议定义了**产生一系列值（无论是有限还是无限个）的标准方式；**
  - 在JavaScript中这个标准就是一个**特定的next方法；**

- **next方法有如下的要求：**

  - 一个**无参数或者一个参数的函数**，返回一个应当**拥有以下两个属性的对象**：

  - **done（boolean）**

    `√` 如果迭代器**可以产生序列中的下一个值，则为 false**。（这等价于没有指定 done 这个属性。）

    `√`如果迭代器**已将序列迭代完毕，则为 true**。这种情况下，value 是可选的，如果它依然存在，即为迭代结束之后的默认返回值

  - **value**

    `√`迭代器返回的任何 JavaScript 值。done 为 true 时可省略。

### 迭代器的代码演示

```javascript
//我们以前遍历都是通过for循环的，for循环能够实现遍历是因为里面有个迭代器

const names = ["小余","小满","康老师","coderwhy"]

//给数组names创建一个迭代器
const namesIterator = {
  //迭代器里面一定有next这个方法
  next:function(){
    //next方法里面返回的是一个对象
    return {
      //对象里面有两个属性，1.done(返回布尔值)2.value(返回具体值或者undefined)，你什么都不填就默认undefined
      done:false,
      value:"小余"
    }
  }
}

//当我们有迭代器的时候，就可以直接调用这个方法
//第一次迭代我们返回第一个内容"小余"，但是后面还有3个内容，还没有迭代完。什么时候迭代完(names的东西已经都输出过了)，done的内容就会变成true，value内容为undefined，判定为我们迭代结束
//过程类似如下(只是举例，一个个输出)
const value1 = namesIterator.next()//小余
const value2 = namesIterator.next()//小满
const value3 = namesIterator.next()//康老师
const value4 = namesIterator.next()//coderwhy
const value5 = namesIterator.next()//undefined
```

> 但是这有个问题，我们value和done里面的内容是写死的，是不是要解决一下。
>
> 解决方式如下：判断+遍历

```javascript
const names = ["小余","小满","康老师","coderwhy"]

let index = 0
const namesIterator = {
  next:function(){
    //核心判断内容
      if(index < names.length){//判断：if
        return {
          done:false,
          value:names[index++]//遍历：index++
        }
      }else{
        return {done:true,value:undefined}//当index索引大于names的数量证明迭代完了就让done返回true。使其遍历结束
      }
  }
}

//进行输出验证
console.log(namesIterator.next());//{done: false, value: '小余'}
console.log(namesIterator.next());//{done: false, value: '小满'}
console.log(namesIterator.next());//{done: false, value: '康老师'}
console.log(namesIterator.next());//{done: false, value: 'coderwhy'}
console.log(namesIterator.next());//{done: true, value: undefined}
```

> 问题还没有解决完，当前的迭代器只是names的迭代器，如果我们要迭代一个东西就重写一遍迭代器是不是代码重复量过高了？那我们就可以继续进行优化。
>
> 没错，要用到封装的思想啦，有的人重复性高的代码不进行封装，代码的质量就会很差。所以我们要避免这种行为。
>
> 我们通过函数进行封装

```javascript
const names = ["小余","小满","康老师","coderwhy"]
const ages = [20,24,29,18]

//封装一个函数
function createArrayIterator(arr){//内容通过参数传递进来
  let index= 0
  return {//执行函数后返回这个next方法，也就是我们下面的namesIterator跟ageIterator。通过这样来调用
    next:function(){
      if (index < arr.length) {//判断传递进来参数的长度，和索引光标进行比对，index够了就返回done:true，结束遍历
        return{
          done:false,
          value:arr[index++]//遍历
        }
      }else{
        return{
          done:true,//遍历结束
          value:undefined
        }
      }
    }
  }
}

const namesIterator = createArrayIterator(names)
console.log(namesIterator.next());
console.log(namesIterator.next());
console.log(namesIterator.next());
console.log(namesIterator.next());
console.log(namesIterator.next());
const agesIterator = createArrayIterator(ages)
console.log(agesIterator.next());
console.log(agesIterator.next());
console.log(agesIterator.next());
console.log(agesIterator.next());
console.log(agesIterator.next());

//namesIterator跟ageIterator就叫做迭代器
```

![image-20230207010545765](.\JavaScript高级_image\image-20230207010545765.png)

## (理解)可迭代对象-将对象变成可迭代对象

- **但是上面的代码整体来说看起来是有点奇怪的：**
  - 我们获取一个数组的时候，需要自己**创建一个index变量**，再**创建一个所谓的迭代器对象**；
  - 事实上**我们可以对上面的代码进行进一步的封装，让其变成一个可迭代对象**；

- **什么又是可迭代对象呢？**

  - **它和迭代器是不同的概念**

  - 当一个对象实现了**iterable protocol协议**时，它就是**一个可迭代对象**

  -  这个对象的要求是**必须实现 @@iterator 方法**，在代码中我们**使用 Symbol.iterator 访问该属性**

    > `@@iterator`：规范的名字
    >
    > `Symbol.iterator`：开发中实际用的名字(固定的)

  - **可迭代（Iterable）** 对象是数组的泛化。这个概念是说任何对象都可以被定制为可在 `for..of` 循环中使用的对象。

- **当然我们要问一个问题，我们转成这样的一个东西有什么好处呢？**

  - 当**一个对象变成一个可迭代对象**的时候，就可以**进行某些迭代操作**
  - 比如 **for...of 操作**时，其实就会调用它的 @@iterator 方法

### 可迭代对象的代码演示

```javascript
//基础的迭代对象
const infos = {
  friends:["小余","小满","康老师","coderwhy"]
}
//给infos创建一个迭代器，迭代infos中的friends
let index = 0
const infosIterator = {
  next:function(){
    if(index<infos.friends.length){//进入了对象里面的属性去进行迭代
      return{done:false,value:infos.friends[index++]}
    }else{
      return{done:false,value:undefined}
    }
  }
}

console.log(infosIterator.next());
console.log(infosIterator.next());
console.log(infosIterator.next());
console.log(infosIterator.next());
console.log(infosIterator.next());
```

> 我们看infos里的内容本身是跟迭代器分开的，但这其实是可以合并起来的，毕竟我们迭代的目的不就是为了迭代对象里面的内容，所以我们可以将迭代器放到对象里面
>
> 对象本身是不可迭代的，我们刚刚能够迭代是因为我们给对象设置了一个迭代器而已。但是我们是不是可以将迭代器放到了对象里面

```javascript
// const infos = {
//   friends:["小余","小满","康老师","coderwhy"]
// }，这个本身是不可迭代的

//将infos变成可迭代对象

/*
  1.必须实现一个特定的函数：[Symbol.iterator]
  2.这个函数需要返回一个迭代器
*/
//给infos创建一个迭代器，迭代infos中的friends
const infos = {
  friends:["小余","小满","康老师","coderwhy"],
  [Symbol.iterator](){//[](){}的[]是计算属性名。有这个才能实现可迭代对象
    //将我们刚刚写的迭代器放进来
    let index = 0
    const objIterator = {
      next:function(){
        if(index < infos.friends.length){//进入了对象里面的属性去进行迭代
          return {done:false,value:infos.friends[index++]}
        }else{
          return {done:true,value:undefined}
        }
      }
    }
    return objIterator//返回迭代器
  }
}

//可迭代对象必然具备特点
//infos[Symbol.iterator]得到的一定是一个函数
//const iterator = infos[Symbol.iterator]()，执行得到的一定是一个迭代器
const iterator = infos[Symbol.iterator]()//执行了得到一个迭代器，然后就可以调用迭代器里面的next方法
console.log(iterator.next());//{done: false, value: '小余'}
console.log(iterator.next());//{done: false, value: '小满'}
console.log(iterator.next());//{done: false, value: '康老师'}
console.log(iterator.next());//{done: false, value: 'coderwhy'}
console.log(iterator.next());//{done: true, value: undefined}

//很显然，我们已经在对象中实现可迭代对象了
//一个关键的区别：可以使用for of操作了
for(const item of infos){
  console.log(item);
}

//可迭代对象必然有一个[Symbol.iterator]函数
//数组是一个可迭代对象(因为他本身已经内置了这个函数)
const students = ["小余","xiaoyu","2002XiaoYu"]
students[Symbol.iterator]//相当于数组里面自带这个了
//证明
const studentsIterator = students[Symbol.iterator]()//获取数组里面的迭代器，记得最后()调用一下
//调用数组的迭代器
console.log(studentsIterator.next());//{value: '小余', done: false}
console.log(studentsIterator.next());//{value: 'xiaoyu', done: false}
console.log(studentsIterator.next());//{value: '2002XiaoYu', done: false}
console.log(studentsIterator.next());//{value: undefined, done: true}
```

> 如果你仔细看，会发现我们遍历对象的时候都是infos这个对象，里面的处理也只是针对这个对象`infos.friends.length`，如果换个对象岂不是就又得重复代码来一遍，那肯定不行，在下面的优化处理中我们进行解决

## (理解)可迭代对象-可迭代对象的优化处理

> 我们想要针对多个对象来进行使用迭代器的话，里面就不能够`infos.friends.length`这样处理，这样只会针对infos这个对象，那其实很好想出来其实是使用this来解决问题

```javascript
const infos = {
  friends:["小余","小满","康老师","coderwhy"],
  [Symbol.iterator](){
    let index = 0
    const objIterator = {
      next:function(){
        if(index < this.friends.length){//但如果只修改这里的this，是不对的，因为这里的this指向的是外层的迭代器objIterator，原因是因为我们调用是infos[Symbol.iterator]()这样子调用，通过迭代器iterator调用的，而迭代器身上显然没有friends(那就更没有friends的长度了)，所以会找不到(会报错)
        //报错信息：Uncaught TypeError: Cannot read properties of undefined (reading 'length')at Object.next
          return {done:false,value:this.friends[index++]}
        }else{
          return {done:true,value:undefined}
        }
      }
    }
    return objIterator
  }
}
```

> 解决方法：next的方法采用箭头函数的方式进行调用，因为箭头函数不绑定this，所以不会受到this隐式绑定的影响，会直接去找next方法的上一层，那为什么不会找到`objIterator`身上呢？因为对象没有作用域，继续往上找形成作用域的是`[Symbol.iterator](){}`函数，所以this会找到这里然后进行调用，那这里的this其实就是`[Symbol.iterator](){}`的this了，而`[Symbol.iterator](){}`的this是什么？那当然是得看这里的隐式调用啦，毕竟这里可不是箭头函数。进行回顾一下
>
> ![image-20230207035711566](.\JavaScript高级_image\image-20230207035711566.png)
>
> 是谁调用了`[Symbol.iterator](){}`？当然是infos啦，我们只需要将infos换成其他对象。就形成了对象的复用了

```javascript
//完成对象的复用
const infos = {
  friends:["xiaomna","小余","小菜"],
  [Symbol.iterator](){
    let index = 0
    const objIterator = {
      next:()=>{//关键点
        if (index < this.friends.length) {
          return{done:false,value:this.friends[index++]}
        }else{
          return{done:true,value:undefined}
        }
      }
    }
    return objIterator
  }
}

const infos1 = infos[Symbol.iterator]()
console.log(infos1.next());
console.log(infos1.next());
console.log(infos1.next());
console.log(infos1.next());
```

### 迭代对象的键值对

> 有时候我们想迭代得到的不是friends的内容，而是里面的键值对，那要怎么做呢？

```javascript
const infos = {
  name:"小余",
  age:20,
  sex:"男"
}

//对象本身是不可迭代的，要怎么解决呢？
for(const item of infos){
  console.log(item);//报错信息：Uncaught TypeError: infos is not iterable
}
```

> 实现迭代
>
> 

```javascript
const infos = {
  name:"小余",
  age:20,
  sex:"男",
  [Symbol.iterator](){
    let index = 0
    //拿到infos对象的所有key
    const keys = Object.keys(this)
    //拿到infos对象的所有值
    const values = Object.values(this)
    const iterator = {
      next:()=>{
        if(index < keys.length){//迭代键key
          return {done:false,value:keys[index++]}
        }else{
          return {done:true,value:undefined}
        }
      }
    }
    return iterator
  }
}
```

```javascript
//迭代对象所有值只需要将keys换成values就行了
        if(index < values.length){//迭代键key
          return {done:false,value:values[index++]}
        }else{
          return {done:true,value:undefined}
        }
```

> 但我们想要键值对同时拿到，怎么做？那就得用到我们之前学的entries了。如果你还没不知道，建议去看我前面的ES6-ES13的笔记，里面有详细记录
>
> ![image-20230207042214493](.\JavaScript高级_image\image-20230207042214493.png)

```javascript
//完整写法，只是将原来keys跟values的位置进行替换
const infos = {
  name:"小余",
  age:20,
  sex:"男",
  [Symbol.iterator](){
    let index = 0
    //拿到infos对象的所有key
    const keys = Object.keys(this)
    //拿到infos对象的所有值
    const values = Object.values(this)
    //拿到infos的键值对，相当于keys跟values两个ES6+方法的结合(本代码块使用这个方式)
    const entries = Object.entries(this)
    const iterator = {
      next:()=>{
        if(index < entries.length){//迭代键key
          return {done:false,value:entries[index++]}
        }else{
          return {done:true,value:undefined}
        }
      }
    }
    return iterator
  }
}

//显然我们不仅做到了迭代对象，还做到了迭代对象里面属性的键值对
for(const item of infos){
  const [key,value] = item
  console.log(`key : ${key} , value : ${value}`);
}
```

## (理解)可迭代对象-可迭代对象的应用场景

### 原生迭代器对象

- **事实上我们平时创建的很多原生对象已经实现了可迭代协议，会生成一个迭代器对象的：**
  - String、Array、Map、Set、arguments对象、NodeList集合(里面全部都有迭代器)

```javascript
//1.迭代字符串string
const str = "小余的笔记连接地址：github.com/2002XiaoYu"
for(const item of str){
  console.log(item);
}

//2.迭代数组Array
const arr = ["小余","GitHub地址","github.com/2002XiaoYu"]
for(const item of arr){
  console.log(item);
}

//3.迭代数据结构Map
const Person1 = {
  name:"小余",
  age:20,
  GitHub:"GitHub.com/2002XiaoYu"
}
const Person2 = {
  name:"小满",
  age:24,
  sex:"男"
}
const map = new Map()
map.set(Person1,"小余的个人信息")
map.set(Person2,"小满的个人信息")
for(const item of map){
  console.log(item);
  [key,value] = item
  console.log(key,value);
}

//4.迭代数据结构Set
const set = new Set(["小余","小余","小余"])
for(const item of set){
  console.log(item);//小余
}

//5.arguments
function foo(){
  for(const argu of arguments){
    console.log(argu);//迭代了arguments里的内容
  }
}
foo("小满的前端微信学习群","加小满微信让他拉你：a1195566313")
```

**可迭代Map的打印结果：**

![image-20230207050102745](.\JavaScript高级_image\image-20230207050102745.png)

## (理解)可迭代对象-可迭代对象的应用场景

### 可迭代对象的应用

- **那么这些东西可以被用在哪里呢？**
  - **JavaScript中语法：for ...of、展开语法（spread syntax）、yield*（后面讲）、解构赋值（Destructuring_assignment）**
  - **创建一些对象时：new Map([Iterable])、new WeakMap([iterable])、new Set([iterable])、new WeakSet([iterable])**
  - **一些方法的调用：Promise.all(iterable)、Promise.race(iterable)、Array.from(iterable)**

```javascript
//1.用在特定的语法上面
const names = ["小余","小满","凡哥"]
const info = {
  name:"小余",
  age:20,
  address:"福建"
}

function foo(arg1,arg2,arg3){
  console.log(arg1,arg2,arg3);
}

foo(...names)//小余 小满 凡哥，数组是能够这样进行的
//但显然你想这样遍历对象，是没门了。
foo(...info)//报错信息：Uncaught TypeError: Spread syntax requires ...iterable[Symbol.iterator] to be a function
//从报错信息上看，扩展运算符需要使用[Symbol.iterator]为函数，其实就是没有迭代器遍历不了
```

> 在对象中创建迭代器，然后就可以迭代对象了

```javascript
//1.用在特定的语法上面
const names = ["小余","小满","凡哥"]
const info = {
  name:"小余",
  age:20,
  address:"福建",
  [Symbol.iterator](){
    let index = 0
    const entries = Object.entries(this)//解锁键值对，只想要value就Object.values。想要key就Object.keys
    const objInterator = {
      next:()=>{
        if (index < entries.length) {
          return {done:false,value:entries[index++]}
        }else{
          return {done:true,value:undefined}
        }
      }
    }
    return objInterator
  }
}

function foo(arg1,arg2,arg3){
  console.log(arg1,arg2,arg3);
}

foo(...info)//(2) ['name', '小余'] (2) ['age', 20] (2) ['address', '福建']
```

> 然后创建一些对象时这些也是用得上的，比如Map，Set。因为这些都是只能够遍历可迭代对象的，你得知道什么是可迭代对象才能够记住本质，而不是记住Map、Set能够传入数组什么的，这样能传的就太多了，谁记得住

![image-20230207130936108](.\JavaScript高级_image\image-20230207130936108.png)

```javascript
//2.一些类的构造函数中，也是传入的可迭代对象
const set = new Set(["小余","这是数组","可迭代对象","小余"])
const set1 = new Set("这是字符串1111")
console.log(set);
console.log(set1);
```

> 接着就是用法三：一些方法的调用了

```javascript
//3.一些常用方法
const p1 = Promise.resolve("小余")
const p2 = Promise.resolve("小余")
const p3 = Promise.resolve("小余")
const pSet = new Set()
pSet.add(p1)
pSet.add(p2)
pSet.add(p3)
Promise.all(pSet).then(res=>{//一样传入可迭代对象
  console.log("res：",res);
}).catch(err =>{
  console.log("err：",err);
})

//arguments的运用，arguments不是数组，而是像数组的对象。导致很多方法无法使用，所以我们通常将他转化为数组
function bar(){
  console.log(arguments);//Arguments(3) ['小余', '天天', '不学习', callee: ƒ, Symbol(Symbol.iterator): ƒ]
  //将argument转化为array数组类型
  const arr = Array.from(arguments)//其实也相当于遍历里面的内容，要求是可迭代对象的
  console.log(arr);//['小余', '天天', '不学习']
}

bar("小余","天天","不学习")
```

![image-20230207132006755](.\JavaScript高级_image\image-20230207132006755.png)

## (理解)可迭代对象-自定义类的对象迭代

### 自定义类的迭代

- **在前面我们看到Array、Set、String、Map等类创建出来的对象都是可迭代对象：**
  - 在面向对象开发中，我们可以通过**class定义一个自己的类，这个类可以创建很多的对象**：
  - 如果我们也希望**自己的类创建出来的对象默认是可迭代**的，那么在**设计类的时候我们就可以添加上 @@iterator 方法**
- **案例：创建一个classroom的类**
  - 教室中有自己的位置、名称、当前教室的学生
  - 这个教室可以进来新学生（push）
  - 创建的教室对象是可迭代对象

```javascript
//自定义类的迭代
class Person{
  constructor(name,age,height,friends){
    this.name = name 
    this.age = age
    this.height = height
    this.friends = friends
  }
}

//其实创建的这两个对象都不是可迭代对象(因为对象本身就是不可迭代的)
const p1 = new Person("小余",20,1.75,["小满","cool","heart","狗洛"])
const p2 = new Person("小满",24,1.77,["康老师","班花姐姐","Tom","琦思妙想"])
```

> 1. 我们可以在Person这个类中进行定义，然后创建对象的时候就继承上面类，使其可迭代
> 2. 实现思路：p1跟p2里面必须有一个属性，而这个属性对应的是一个函数，而这个函数需要返回一个迭代器

```javascript
//自定义类的迭代
class Person{
  constructor(name,age,height,friends){
    this.name = name 
    this.age = age
    this.height = height
    this.friends = friends
  }

    //没错，我们将迭代器塞进来了。这里的迭代器对于p1和p2而言相当于在他们的原型上面，但这种方式平时中不会太常见，通常会出现在框架里面
  [Symbol.iterator](){
    let index = 0
    let entries = Object.entries(this)
    const personIterator = {
      next:function(){
        if (index < entries.length) {
          return {done:false,value:entries[index++]}
        }else{
          return {done:true,value:undefined}
        }
      }
    }
    return personIterator
  }
}

//其实创建的这两个对象都不是可迭代对象(因为对象本身就是不可迭代的)
const p1 = new Person("小余",20,1.75,["小满","cool","heart","狗洛"])
const p2 = new Person("小满",24,1.77,["康老师","班花姐姐","Tom","琦思妙想"])

//我们可以在Person这个类中进行定义，然后创建对象的时候就继承上面类，使其可迭代
//实现思路：p1跟p2里面必须有一个属性，而这个属性对应的是一个函数，而这个函数需要返回一个迭代器
for(const item of p1){
  console.log(item);
}
```

> 但通常我们可能只会针对对象中的某一个属性，那这种情况要怎么实现？
>
> - 动用到我们的this指向了，这个时候next这里的function函数就需要变回this指向了，上面有讲原因。是为了保证this的指向能够指到personIterator身上

```javascript
  [Symbol.iterator](){
    let index = 0
    const personIterator = {
      next:()=>{
        if (index < this.friends.length) {//通过this进行绑定里面的friends属性，实现针对 对象 单个属性的迭代
          return {done:false,value:this.friends[index++]}
        }else{
          return {done:true,value:undefined}
        }
      }
    }
    return personIterator
  }
}
```

```javascript
//完整代码
//自定义类的迭代
class Person{
  constructor(name,age,height,friends){
    this.name = name 
    this.age = age
    this.height = height
    this.friends = friends
  }

  [Symbol.iterator](){
    let index = 0
    const personIterator = {
      next:()=>{
        if (index < this.friends.length) {
          return {done:false,value:this.friends[index++]}
        }else{
          return {done:true,value:undefined}
        }
      }
    }
    return personIterator
  }
}

//其实创建的这两个对象都不是可迭代对象(因为对象本身就是不可迭代的)
const p1 = new Person("小余",20,1.75,["小满","cool","heart","狗洛"])
const p2 = new Person("小满",24,1.77,["康老师","班花姐姐","Tom","琦思妙想"])

//我们可以在Person这个类中进行定义，然后创建对象的时候就继承上面类，使其可迭代
//实现思路：p1跟p2里面必须有一个属性，而这个属性对应的是一个函数，而这个函数需要返回一个迭代器

//具体结果将代码贴到编辑器中在控制台打印观看
for(const item of p1){
  console.log(item);
}

for(const item of p2){
  console.log(item);
}
```

## (了解)可迭器-迭代器的中断检测

### 迭代器的中断

- **迭代器在某些情况下会在没有完全迭代的情况下中断：**
  - 比如遍历的过程中通过**break、return、throw中断**了循环操作
  - 比如在解构的时候，没有解构所有的值
- **那么这个时候我们想要监听中断的话，可以添加return方法：**

```javascript
//进行中断
for(const item of p2){
  console.log(item);
  //中断迭代器
  if(item === "班花姐姐"){
    break
  }
}
```

```javascript
//监听中断，基于上面的`自定义类迭代`代码块内容进行修改
  [Symbol.iterator](){
    let index = 0
    const personIterator = {
      next:()=>{
        if (index < this.friends.length) {
          return {done:false,value:this.friends[index++]}
        }else{
          return {done:true,value:undefined}
        }
      },
      return:()=>{//新增部分，监听中断
        console.log("监听到迭代器中断");
        return {done:true}
      }
    }
    return personIterator
  }
}
```

```javascript
//用来直接复制运行的 迭代器中断代码(在编辑器可能更方便看)

//自定义类的迭代
class Person{
  constructor(name,age,height,friends){
    this.name = name 
    this.age = age
    this.height = height
    this.friends = friends
  }

  [Symbol.iterator](){
    let index = 0
    const personIterator = {
      next:()=>{
        if (index < this.friends.length) {
          return {done:false,value:this.friends[index++]}
        }else{
          return {done:true,value:undefined}
        }
      },
      return:()=>{
        console.log("监听到迭代器中断");
        return {done:true}
      }
    }
    return personIterator
  }
}

//其实创建的这两个对象都不是可迭代对象(因为对象本身就是不可迭代的)
const p1 = new Person("小余",20,1.75,["小满","cool","heart","狗洛"])
const p2 = new Person("小满",24,1.77,["康老师","班花姐姐","Tom","琦思妙想"])

//我们可以在Person这个类中进行定义，然后创建对象的时候就继承上面类，使其可迭代
//实现思路：p1跟p2里面必须有一个属性，而这个属性对应的是一个函数，而这个函数需要返回一个迭代器
for(const item of p1){
  console.log(item);
}

for(const item of p2){
  console.log(item);
  //中断迭代器
  if(item === "班花姐姐"){
    break
  }
}
```

## (理解)生成器-生成器函数和生成器对象基本使用

- **生成器是ES6中新增的一种函数控制、使用的方案，它可以让我们更加灵活的控制函数什么时候继续执行、暂停执行等**

  - 平时我们会编写很多的函数，这些函数终止的条件通常是返回值或者发生了异常
  - 而生成器能够精准控制你什么时候终止

- **生成器函数也是一个函数，但是和普通的函数有一些区别：**

  - 首先，**生成器函数需要在function的后面加一个符号：`*`**

  - 其次，**生成器函数可以通过yield关键字来控制函数的执行流程：**

  - 最后，**生成器函数的返回值是一个Generator（生成器）：**

    `√`**生成器事实上是一种特殊的迭代器**

    `√`**MDN：Instead, they return a special type of iterator, called a Generator.**

### 生成器函数执行

- **我们发现下面的生成器函数foo的执行体压根没有执行，它只是返回了一个生成器对象**
  -  那么我们**如何可以让它执行函数中的东西**呢？**调用next**即可
  - 我们之前学习迭代器时，知道**迭代器的next是会有返回值**的
  - 但是我们很多时候**不希望next返回的是一个undefined，这个时候我们可以通过yield来返回结果**

> **生成器函数：**
>
> 1. function后面会跟上`*`
> 2. 代码的执行可以被yield控制
> 3. 生成器函数默认在执行时，返回一个生成器(这就是为什么我们直接调用，一行代码都没执行的原因)
>    1. 要想执行函数内的代码，需要返回生成器对象之后，调用它的next操作
>    2. 当遇到yield时，就会中断执行

```javascript
//这个*就是我们生成器函数的标志，靠在function这边也行，靠在foo旁边也行，最好不要function、*、foo连起来写
//1.定义了一个生成器函数
function* foo(){
console.log("1");
console.log("2");
yield
console.log("3");
console.log("4");
yield
console.log("5");
console.log("6");
}
foo()//一行代码都没执行，因为返回的是生成器返回一个生成器(这就是为什么我们直接调用，一行代码都没执行的原因)

//2.调用生成器函数，返回一个生成器对象
const generator = foo()

//调用生成器的next方法
generator.next()//返回1跟2，执行到第一个yield后暂停
generator.next()//返回3跟4，执行到第二个yield后暂停
generator.next()//返回5跟6，执行到第三个yield后暂停
```

### 生成器传递参数 – next函数

- **函数既然可以暂停来分段执行，那么函数应该是可以传递参数的，我们是否可以给每个分段来传递参数呢？**
  - 答案是可以的
  - 我们在**调用next函数的时候，可以给它传递参数，那么这个参数会作为上一个yield语句的返回值**
  - 注意：**也就是说我们是为本次的函数代码块执行提供了一个值**

```javascript
//1.定义了一个生成器函数
function* foo(){
console.log("执行内部代码：1");
console.log("执行内部代码：2");
yield
console.log("执行内部代码：3");
console.log("执行内部代码：4");
yield
console.log("执行内部代码：5");
console.log("执行内部代码：6");
}

//2.调用生成器函数，返回一个生成器对象
const generator = foo()

//调用生成器的next方法
console.log(generator.next());
console.log(generator.next());
console.log(generator.next());
```

> 根据下方的打印出来结果的图片可以看到，我们log打印出来的就是生成器，跟我们之前的迭代器格式非常相似，一样有value跟done，不过区别在于这里的done的布尔值决定其实取决于关键字`yield`的，如果还有`yield`，其实就证明了后续还有代码没有执行完，这时候done就返回false。如果后续没有yield了，就证明了后面的代码已经是最后一段代码了，这时候done就返回了true。
>
> 当done为true其实就相当于return返回了

![image-20230207162353458](.\JavaScript高级_image\image-20230207162353458.png)

> 但是我们仔细看一下，就会发现生成器返回的值value为什么是undefined，如果我想要有值需要怎么做呢？

```javascript
function* foo(){
console.log("执行内部代码：1");
console.log("执行内部代码：2");
yield "第一段yield" //只需要在yield后面进行输出就行了，这个参数会作为上一个yield语句的返回值
console.log("执行内部代码：3");
console.log("执行内部代码：4");
yield "第二段yield"
console.log("执行内部代码：5");
console.log("执行内部代码：6");
}

//2.调用生成器函数，返回一个生成器对象
const generator = foo()

//调用生成器的next方法
console.log(generator.next());
console.log(generator.next());
console.log(generator.next());
```

![image-20230207163035626](.\JavaScript高级_image\image-20230207163035626.png)

### 生成器提前结束 – return函数

- **还有一个可以给生成器函数传递参数的方法是通过return函数：**
  - **return传值后这个生成器函数就会结束，之后调用next不会继续生成值**了

```javascript
function* foo(){
console.log("执行内部代码：1");
console.log("执行内部代码：2");
yield "第一段yield"
console.log("执行内部代码：3");
console.log("执行内部代码：4");
return "第二段yield"			//注意这里的代码，变成了return。那再调用next的话，后面5、6的代码就不会执行了
console.log("执行内部代码：5");
console.log("执行内部代码：6");
}

//2.调用生成器函数，返回一个生成器对象
const generator = foo()

//调用生成器的next方法
console.log(generator.next());
console.log(generator.next());
console.log(generator.next());
```

![image-20230207163356808](.\JavaScript高级_image\image-20230207163356808.png)

### 生成器传递参数 – 函数内使用

> 如果我们想要在函数内部除了执行函数内本来的内容之外，还能够通过我们函数外面参数传递进去

```javascript
//1.定义了一个生成器函数
function* foo(){
console.log("执行内部代码：1");
console.log("执行内部代码：2"); 
  const name2 = yield "第一段yield"
console.log("执行内部代码：3",name2);
console.log("执行内部代码：4",name2);
return "第二段yield"
console.log("执行内部代码：5");
console.log("执行内部代码：6");
}

//2.调用生成器函数，返回一个生成器对象
const generator = foo()

//调用生成器的next方法
console.log(generator.next());
console.log(generator.next("雨下整夜，我对你的爱忆如潮水"));//来啦，传递参数了，这个怎么执行的，我采用图片的方式解释
console.log(generator.next());
```

![image-20230207171238143](.\JavaScript高级_image\image-20230207171238143.png)

- 代码执行结果

![image-20230207171306785](.\JavaScript高级_image\image-20230207171306785.png)

> 而且这里有一点不一样的是，第一个传参的地方在开头foo的形参里面，我们通常不写在调用生成器的第一个方法里面

```javascript
//1.定义了一个生成器函数
function* foo(name1){
console.log("执行内部代码：1",name1);
console.log("执行内部代码：2",name1); 
  const name2 = yield "第一段yield"
console.log("执行内部代码：3",name2);
console.log("执行内部代码：4",name2);
return "第二段yield"
console.log("执行内部代码：5");
console.log("执行内部代码：6");
}

//2.调用生成器函数，返回一个生成器对象
const generator = foo("第一个传参放这里")

//调用生成器的next方法
console.log(generator.next());
console.log(generator.next("雨下整夜，我对你的爱忆如潮水"));
console.log(generator.next());
```

![image-20230207173728774](.\JavaScript高级_image\image-20230207173728774.png)

![image-20230207173744036](.\JavaScript高级_image\image-20230207173744036.png)

## (理解)生成器-生成器代替迭代器的应用场景

### 生成器抛出异常 – throw函数

- **除了给生成器函数内部传递参数之外，也可以给生成器函数内部抛出异常：**
  - **抛出异常后我们可以在生成器函数中捕获异常**
  - 但是在**catch语句中不能继续yield新的值了，但是可以在catch语句外使用yield继续中断函数的执行**

```javascript
//调用生成器的next方法
//generator.return，提前结束函数
console.log(generator.next());
console.log(generator.return("------------"));//这样就直接return，后面next的函数就不再执行了
console.log(generator.next());//后面的内容就变成了：{value: undefined, done: true}
```

```javascript
//generator.throw向函数抛出一个异常
//调用生成器的next方法
console.log(generator.next());
//异常是一层层往上抛出，到最上层还没被捕获就会抛到浏览器去了，也就形成了我们看到的报错
console.log(generator.throw(new Error("next2 throw error")));//抛出异常
console.log(generator.next());
```

![image-20230207180205707](.\JavaScript高级_image\image-20230207180205707.png)

```javascript
//参考
function* foo(){
  console.log("函数开始执行");

  try {
    yield
  } catch (err) {
    console.log("内部捕获异常",err);
  }
  
  yield 2222
  
  console.log("函数执行结束");

}
    
    
const generator = foo()
console.log(generator.next());
console.log(generator.next());
console.log(generator.next());
```

### 生成器替代迭代器

- **我们发现生成器是一种特殊的迭代器，那么在某些情况下我们可以使用生成器来替代迭代器：**

```javascript
//以前的做法
const names = ["aaaa","bbbb","cccc"]
const nums = [1,2,3,4];

function createArrayInterator(arr){
  let index = 0
  const arrayInerator = {
    next:()=>{
      if(index < arr.length){
        return {done:false,value:arr[index++]}
      }else{
        return {done:true,value:undefined}
      }
    }
  }
  return arrayInerator
}

const namesIterator = createArrayInterator(names)
console.log(namesIterator.next());
```

```javascript
//现在的做法
const names = ["aaaa","bbbb","cccc"]
const nums = [1,2,3,4];

function* createArrayInterator(arr){
  //需要返回一个迭代器，而生成器就是一个特殊的迭代器
  for(let i = 0;i<arr.length;i++){
    yield arr[i]
  }
  return undefined
}

const namesIterator = createArrayInterator(names)
console.log(namesIterator.next());//执行一次给一次的结果，yield的特性
console.log(namesIterator.next());
console.log(namesIterator.next());
```

![image-20230209033159389](.\JavaScript高级_image\image-20230209033159389.png)

```javascript
function* createRangeGenerator(start, end) {
  //需要返回一个迭代器，而生成器就是一个特殊的迭代器
  for (let i = start; i < end; i++) {
    yield i
  }
}

//生成器函数，可以生成某个范围的值，例如3-9之间
const rangeGen = createRangeGenerator(3, 9)
console.log(rangeGen.next());
console.log(rangeGen.next());
console.log(rangeGen.next());
console.log(rangeGen.next());
console.log(rangeGen.next());
console.log(rangeGen.next());
console.log(rangeGen.next());
```

## (理解)生成器-生成器自定义类的可迭代对象

**事实上我们还可以使用yield*来生产一个可迭代对象：**

-  这个时候相当于是**一种yield的语法糖**，只不过会**依次迭代这个可迭代对象，每次迭代其中的一个值**
- 其实这就是简写而已，方便我们按顺序迭代输出
- 因为`yield*`是生成器函数的语法糖，所以只能写在生成器里面

```javascript
const names = ["aaaa","bbbb","cccc"]
const nums = [1,2,3,4];

function* arrIterator(arr){
  yield* arr
}
const p1 = arrIterator(nums)
console.log(p1.next());
console.log(p1.next());
console.log(p1.next());
console.log(p1.next());
const names = ["aaaa","bbbb","cccc"]
const nums = [1,2,3,4];

function* arrIterator(arr){
  yield* arr
}
const p1 = arrIterator(nums)
console.log(p1.next());
console.log(p1.next());
console.log(p1.next());
console.log(p1.next());
```

![image-20230209035740148](.\JavaScript高级_image\image-20230209035740148.png)

```javascript
//还记得我们前面写在类里面的迭代器吗？
class Person{
  constructor(name,age,height,friends){
    this.name = name 
    this.age = age
    this.height = height
    this.friends = friends
  }

  [Symbol.iterator](){
    let index = 0
    const personIterator = {
      next:()=>{
        if (index < this.friends.length) {
          return {done:false,value:this.friends[index++]}
        }else{
          return {done:true,value:undefined}
        }
      },
      return:()=>{
        console.log("监听到迭代器中断");
        return {done:true}
      }
    }
    return personIterator
  }
}
```

### 自定义类迭代 – 生成器实现

- **在之前的自定义类迭代中，我们也可以换成生成器：**

```javascript
//类中没有生成器怎么办？写在那个[Symbol.iterator]函数的前面，就是生成器了
class Person{
  constructor(name,age,height,friends){
    this.name = name 
    this.age = age
    this.height = height
    this.friends = friends
  }

  *[Symbol.iterator](){
    yield* this.friends
  }
}

const p1 = new Person("小满",24,1.77,["康老师","班花姐姐","Tom","琦思妙想"])
//一样可以实现迭代
for(const item of p1){
  console.log(item);
}
```

### 对生成器的操作

- **既然生成器是一个迭代器，那么我们可以对其进行如下的操作**

![image-20230209040633119](.\JavaScript高级_image\image-20230209040633119.png)

## (掌握)异步处理-传统处理和Promise处理方案

### 异步处理方案

- **学完了我们前面的Promise、生成器等，我们目前来看一下异步代码的最终处理方案。**
- **案例需求：**
  - 我们需要向服务器发送网络请求获取数据，一共需要发送三次请求
  - 第二次的请求url依赖于第一次的结果
  - 第三次的请求url依赖于第二次的结果
  - 依次类推

```javascript
/*
  1.发送一次网络请求，等到这次网络请求的结果
  2.发送第二次网络请求，等待这次网络请求的结果
  3.发送第三次网络请求，等待这次网络请求的结果

  这发送这多次网络请求不是并行的，而是后者需要依赖前者的结果
  就是说第一次网络请求会携带一些信息回来，而第二次网络请求就需要这些信息，第三次网络请求也可能需要的不止第二次网络请求带回来的内容，还有第一次网络请求带回来的内容
*/
```

#### **方式1：层层嵌套**

- (回调地狱图二)

![image-20230209044025539](.\JavaScript高级_image\image-20230209044025539.png)

#### **方式2：链式调用**

```javascript
//上面右边第一幅图是链式调用
//使用Promise进行重构(解决回调地狱) => 链式调用
function requestData(url){
  return new Promise((resolve,reject) => {
    setTimeout(()=>{
      resolve(url)
    },2000)
  })
}

function getData(){
  requestData("为什么").then(res1 => {
    console.log("第一次结果：",res1);
    return requestData(res1+"小余和小满")//不断的返回本身到下一层处理
  }).then(res2 =>{
    console.log("第二次结果：",res2);
    return requestData(res2+"在群里看黑丝")
  }).then(res3 =>{
    console.log("第三次结果：",res3);
  })
}

getData()
//但是这种方式可读性也不够好
```

![image-20230209045648245](.\JavaScript高级_image\image-20230209045648245.png)

### Generator方案

- 但是上面的代码其实看起来也是阅读性比较差的，有没有办法可以继续来对上面的代码进行优化呢？

#### **方式3：最终代码**

```javascript
function requestData(url){
  return new Promise((resolve,reject) => {
    setTimeout(()=>{
      resolve(url)
    },2000)
  })
}

function* getData(){
  //使用生成器的方式
  const res1 = yield requestData("小余")//这里未返回结果不能执行下一个，因为需要res1的结果
  console.log("res1",res1);
  const res2 = yield requestData(res1 + "小满")
  console.log("res2",res2);
  const res3 = yield requestData(res2 +"不知道去哪了")
  console.log("res3",res3);
}

const generator =  getData()//因为我们使用了生成器了，也就是特殊的迭代器，所以调用是返回迭代器
generator.next().value.then(res1 =>{//因为next返回的是迭代器里的内容，也就是{done，value}，value里面是Promise，我们的异步数据就在Promsie里面，所以我们在.value的基础上继续点then。相当于Promise.then，然后就拿到了我们第一个res1的值，这里就yield精准断掉了，所以继续重复操作到res3
  generator.next(res1).value.then(res2 =>{
    generator.next(res2).value.then(res3 => {
      console.log(res3);
    })
  })
});

```

```javascript
//无注释版本，看getData的清晰结构
function requestData(url){
  return new Promise((resolve,reject) => {
    setTimeout(()=>{
      resolve(url)
    },2000)
  })
}

function* getData(){
  const res1 = yield requestData("小余")//拿到res1的结果执行res2结果
  const res2 = yield requestData(res1 + "小满")//拿到res2结果执行res3结果
  const res3 = yield requestData(res2 +"不知道去哪了")
}

const generator =  getData()
generator.next().value.then(res1 =>{
  generator.next(res1).value.then(res2 =>{
    generator.next(res2).value.then(res3 => {
      console.log(res3);
    })
  })
});

```

### 自动执行generator函数

- **目前我们的写法有两个问题：**
  - 第一，我们不能确定到底需要调用几层的Promise关系
  -  第二，如果还有其他需要这样执行的函数，我们应该如何操作呢？
- **所以，我们可以封装一个工具函数execGenerator自动执行生成器函数：**

```javascript
function execGenerator(genFn){
    //获取对应函数的generator
    const generator = genFn()
    function exec(res){
        const result = generator.next(res)
        if(result.done) return result.value//result里的done是false跟true，为true证明已经到最后了，就返回result的value
        result.value.then(res => {
            exec(res)//递归
        })
    }
    exec()
}

//使用
execGenerator(genFn)//要自动执行就放进参数
```

#### 方式4：async/await的解决方案

```javascript
//最终写法：方案四：async/await的解决方案
function requestData(url){
  return new Promise((resolve,reject) => {
    setTimeout(()=>{
      resolve(url)
    },2000)
  })
}

async function getData(){
  //使用生成器的方式
  const res1 = await requestData("小余")//这里未返回结果不能执行下一个，因为需要res1的结果
  console.log("res1",res1);
  const res2 = await requestData(res1 + "小满")
  console.log("res2",res2);
  const res3 = await requestData(res2 +"不知道去哪了")
  console.log("res3",res3);
}

getData()//使用了语法糖，去除了后面不断回调的情况
```

```javascript
//无注释版本
function requestData(url){
  return new Promise((resolve,reject) => {
    setTimeout(()=>{
      resolve(url)
    },2000)
  })
}

async function getData(){
  const res1 = await requestData("小余")
  const res2 = await requestData(res1 + "小满")
  const res3 = await requestData(res2 +"不知道去哪了")
  console.log(res3);
}

getData()
```

# await、async、事件循环

## (掌握)异步函数-异步函数的定义方式

```javascript
//普通函数
// function foo(){}
// const bar = function(){}
// const naz = ()=>{}

//生成器函数
// function* foo(){}

//异步函数
async function foo(){
  console.log("foo function1");
  console.log("foo function2");
  console.log("foo function3");
}

foo()
```

### 异步函数 async function

- **async关键字用于声明一个异步函数：**
  -  async是asynchronous单词的缩写，异步、非同步；
  - sync是synchronous单词的缩写，同步、同时
- **async异步函数可以有很多中写法：**

```javascript
async function foo1(){
    
}

const foo2 = async function(){
    
}

const foo3 = async () =>{
    
}

class Person{
    async foo(){
        
    }
}
```

## (掌握)异步函数-异步函数的返回值和异常

### 异步函数的执行流程

- **异步函数的内部代码执行过程和普通的函数是一致的，默认情况下也是会被同步执行。**
- **异步函数有返回值时，和普通函数会有区别：**
  - **情况一**：异步函数也可以有返回值，但是异步函数的返回值相当于被包裹到Promise.resolve中；
  - **情况二**：如果我们的异步函数的返回值是Promise，状态由会由Promise决定;
  - **情况三**：如果我们的异步函数的返回值是一个对象并且实现了thenable，那么会由对象的then方法来决定
- **如果我们在async中抛出了异常，那么程序它并不会像普通函数一样报错，而是会作为Promise的reject来传递；**

```javascript
//异步函数
async function foo(){
  return undefined
}

foo()
//async是返回一个Promise
```

![image-20230209191919285](.\JavaScript高级_image\image-20230209191919285.png)

```javascript
async function foo1(){
//第一种返回一个普通的值
  return 321//相当于返回一个Promise.resolve(321)
}
//既然返回的是一个Promise，则证明了是可以直接then的
foo1().then(res => {
    console.log("res",res)
})
```

```javascript
//异步函数
async function foo1(){
  //第二种返回一个Promise，状态由里面的promise决定
    return new Promise((resolve,reject)=>{
      setTimeout(()=>{
        console.log("等待两秒钟出结果");
      },2000)
    })
  }
  //既然返回的是一个Promise，则证明了是可以直接then的
  foo1().then(res => {
      console.log("res",res)
  })
```

```javascript
//异步函数
async function foo1(){
  //第三种，返回一个thenable对象
  return {
    then:function(resolve,reject){
      resolve("小满BBBB")
    } 
  }
  }
  foo1().then(res => {
      console.log("res",res)
  })
```

> 如果异步函数中有抛出异常(产生错误)，这个异常不会被浏览器立即处理，而是进行如下操作
>
> 1. Promise.reject(error)，也就是能够被我们的Promise捕获到错误

```javascript
async function foo(){
  console.log("1");
  console.log("2");
  "abc".filter()//在这里产生错误
  console.log("你看不到小余");

  return 123
}

foo().then(res => {
  console.log(res);
}).catch(err=>{
  console.log(err);
    //捕获了异常之后，我们就可以继续执行我们其他的逻辑的代码
})
```

![image-20230210001720422](.\JavaScript高级_image\image-20230210001720422.png)

## (掌握)异步函数-await关键字的使用

### await关键字

- **async函数另外一个特殊之处就是可以在它内部使用await关键字，而普通函数中是不可以的**
- **await关键字有什么特点呢？**
  - 通常使用await是后面会**跟上一个表达式**，这个**表达式会返回一个Promise**；
  -  那么await会**等到Promise的状态变成fulfilled状态**，之后**继续执行异步函数**
- **如果await后面是一个普通的值，那么会直接返回这个值**
- **如果await后面是一个thenable的对象，那么会根据对象的then方法调用来决定后续的值**
- **如果await后面的表达式，返回的Promise是reject的状态，那么会将这个reject结果直接作为函数的Promise的reject值**

```javascript
//await后面一般都是跟随着表达式的，也就是Promise，异步的内容在这里会进行停顿，跟yield有些类似，直到异步的resolve内容执行后才会继续往下执行
function bar(){
  return new Promise((resolve,reject)=>{
    setTimeout(()=>{
      resolve("小余在这里停止3s")
    },3000)
  })
}

async function foo(){
    //我们在这里使用await，await后续返回一个Promise，那么会等待Promise有结果后，才会继续执行后续的代码
  const res = await bar()
  console.log(res);
  const p1 = "这句话如果不是执行在`小余在这里停止3s`前面，说明我们等待前面Promise异步执行结束得到结果才继续往下执行"
  console.log(p1);
}
foo()
```

> await后面的表达式，返回的Promise是reject的状态。那很显然咯，我们需要把抛出来的错误接住，不然这个错误就要一层层丢上去，最终丢到浏览器变成真的爆红了。
>
> 怎么接住？bar()返回的是Promise，得到的结果当然是使用catch来接住了

```javascript
function bar(){
  return new Promise((resolve,reject)=>{
    setTimeout(()=>{
      reject("小余在这里停止3s")
    },3000)
  })
}

async function foo(){
    //我们在这里使用await，我们bar里面是一个Promise，会等待Promise内的异步执行结束才会继续往下执行
  const res = await bar()
  console.log(res);
  const p1 = "这句话如果不是执行在`小余在这里停止3s`前面，说明我们等待前面Promise异步执行结束才继续往下执行"
  console.log(p1);
}

//第一种处理方式
foo().catch(err =>{
  console.log(`出现---${err}---这个错误`);
})
```

![image-20230210021540911](.\JavaScript高级_image\image-20230210021540911.png)

```javascript
function bar(){
  return new Promise((resolve,reject)=>{
    setTimeout(()=>{
      reject("小余在这里停止3s")
    },3000)
  })
}

async function foo(){
    //处理方式2：try catch
try {
      //我们在这里使用await，我们bar里面是一个Promise，会等待Promise内的异步执行结束才会继续往下执行
      const res = await bar()
      console.log(res);
      const p1 = "这句话如果不是执行在`小余在这里停止3s`前面，说明我们等待前面Promise异步执行结束才继续往下执行"
      console.log(p1);
} catch (error) {
  console.log(`错误信息：${error},我们使用第二种处理方式try catch来捕获`);
}
}

foo()
```

![image-20230210021834683](.\JavaScript高级_image\image-20230210021834683.png)

## (掌握)异步函数-await和async关键字结合使用

```javascript
function requestData(url){
  return new Promise((resolve)=>{
    setTimeout(()=>{
      resolve(url)
    },3000)
  })
}

async function test(){
  console.log("test function");
  return "test"
}

async function bar(){
  console.log("bar function");

  return new Promise((resolve)=>{
    setTimeout(()=>{
      resolve("bar")
    },2000)
  })
}

async function demo(){
  console.log("demo function");

  return {
    then:(resolve)=>{
      resolve("demo")
    }
  }
}



//调用的入口async函数
async function foo(){
  console.log("foo function");
  //一层层等待
  const res1 = await requestData("小余")
  console.log("res1",res1);

  const res2 = await test()
  console.log("res2",res2);

  const res3 = await bar()
  console.log("res3",res3);

  const res4 = await demo()
  console.log("res4",res4);
}

foo()
```

## (理解)操作系统的进程和线程理解

### 进程和线程

- **线程和进程是操作系统中的两个概念：**
  -  **进程（process）**：计算机**已经运行的程序**，是**操作系统管理程序**的一种方式；
  -  **线程（thread）**：操作系统能够运行**运算调度的最小单位**，通常情况下**它被包含在进程**中；
- **听起来很抽象，这里还是给出我的解释：**
  - **进程：**我们可以认为，启动**一个应用程序**，就会默认**启动一个进程**（也可能是多个进程）；
  - **线程**：每一个进程中，都会启动**至少一个线程**用来执行程序中的代码，这个线程被称之为**主线程**
  - 所以我们也可以说进程是线程的容器；(进程包含线程)
- **再用一个形象的例子解释：**
  - 操作系统类似于一个大工厂
  -  工厂中里有很多车间，这个车间就是进程；
  - 每个车间可能有一个以上的工人在工厂，这个工人就是线程;(多线程想当于多个工人一起干活，效率自然会更高)

### 操作系统 – 进程 – 线程

![image-20230210024424461](.\JavaScript高级_image\image-20230210024424461.png)

### 操作系统的工作方式

> 如果是单核CPU的话，同一时间只能做一件事情，之所以能够做多件事情，是因为运行速度很快，以非常快的速度在进程之间来回切换。如果是多核CPU就能够真正实现在同一时间实现多运算

-  **操作系统是如何做到同时让多个进程（边听歌、边写代码、边查阅资料）同时工作**呢？
  - 这是因为**CPU的运算速度非常快**，它可以**快速的在多个进程之间迅速的切换**；
  -  当我们**进程中的线程**获取到**时间片**时，就可以**快速执行我们编写的代码**；
  - 对于用户来说**是感受不到这种快速的切换**的
- 你可以在Mac的活动监视器或者Windows的资源管理器中查看到很多进程

![image-20230210025036767](.\JavaScript高级_image\image-20230210025036767.png)

## (理解)JavaScript单线程-事件队列-循环

### 浏览器中的JavaScript线程

- 我们经常会说**JavaScript是单线程（可以开启workers）**的，但是**JavaScript的线程应该有自己的容器进程：浏览器或者Node**
- **浏览器是一个进程吗，它里面只有一个线程吗？**
  - 目前**多数的浏览器其实都是多进程**的，当我们**打开一个tab页面时就会开启一个新的进程**，这是为了**防止一个页面卡死而造成**
    **所有页面无法响应**，整个浏览器需要强制退出
  - **每个进程中又有很多的线程**，其中**包括执行JavaScript代码的线程**
- **JavaScript的代码执行是在一个单独的线程中执行的：**
  -  这就意味着JavaScript的代码，在**同一个时刻只能做一件事；**
  - 如果**这件事是非常耗时**的，就意味着当前的线程就**会被阻塞**；
  - **为什么JavaScript的代码执行只在一个单独的线程上执行，而不是多个线程？**
    - 因为多线程操作是很容易产生不安全的数据的，我们访问数据的时候通常都是需要上锁的(多线程的情况)，而上锁再解锁的操作是比较耗时的
- **所以真正耗时的操作，实际上并不是由JavaScript线程在执行的：**
  - 浏览器的每个进程是多线程的，那么**其他线程可以来完成这个耗时的操作**
  - 比如**网络请求、定时器**，我们只需要在特性的时候执行应该有的回调即可

```javascript
let name = "小余"
name = "凡哥"

function bar(){
    console.log("bar function")
}

function foo(){
    console.log("foo function")
    bar()
}

foo()
console.log("小余开学了")

//上面的代码中，我们执行foo()函数的时候，就会停止继续往后执行(所以控制台打印`小余开学了`是最后才轮到他的)，因为JavaScript的代码时在一个单独的线程中进行的，从而执行foo内的函数，在foo函数中再执行bar函数。

//在内存中的表现就是，全局上下文中有let name = "小余"跟name = "凡哥" 和foo()，当执行foo的时候就会开启一个foo函数执行上下文，在foo执行上下文里面有bar(),就又会开启bar函数执行上下文，等bar函数执行上下文执行结束销毁才会继续执行foo函数中bar()后面的内容，foo函数对于全局执行上下文也是同理
```

### 浏览器的事件循环

- **如果在执行JavaScript代码的过程中，有异步操作呢？**
  - 中间我们插入了一个setTimeout的函数调用
  - 这个函数被放到入调用栈中，执行会立即结束，并不会阻塞后续代码的执行
  - 计时操作是由浏览器来计时的(而不是JavaScript线程)，浏览器会叫另一个线程来执行

```javascript
function bar(){
  console.log("bar function");
}

function foo(){
  console.log("foo function")

  //1.在JavaScript内部执行
  let total = 0
  for(let i = 0;i< 1000000;i++){
    total += i
  }

  //2.创建一个定时器
  setTimeout(()=>{//setTimeout函数本身还是由JavaScript线程执行的，但是计时环节是由浏览器派出了另一个线程来执行，那JavaScript线程就空出手来执行其他代码了，这也算一种异步操作
    console.log("setTimeout");
  },10000)//这个计时的操作不是由JavaScript的线程来做的，如果JavaScript用来做这件事情，后面的内容就得等这里计时结束才能够执行了(因为JavaScript线程同一时间只能做一件事情)，那这显然是没有的
  bar()
}

foo()
```

![image-20230210031923032](.\JavaScript高级_image\image-20230210031923032.png)

## (理解)JavaScript单线程-宏任务和微任务的区别

### 宏任务和微任务

> 知识点补充：
>
> 回调函数是一种在异步编程中使用的技术，它是一个函数，在完成一个异步操作后可以被调用。回调函数允许您在异步操作完成后执行特定的代码，而不是等待它完成。这种方法常用于需要等待长时间的操作，例如 I/O 操作或网络请求，以便在后台执行它们，不阻塞主线程。
>
> 回调函数的语法是将回调函数作为参数传递给其他函数，以便在完成异步操作后调用该回调函数。回调函数通常是匿名函数，但也可以是已命名的函数。

- **但是事件循环中并非只维护着一个队列，事实上是有两个队列：**
  - **宏任务队列（macrotask queue）**：ajax、setTimeout、setInterval、DOM监听、UI Rendering等
  - **微任务队列（microtask queue）**：Promise的then回调、 Mutation Observer API、queueMicrotask()等

> - 当浏览器的其他线程处理完计时器setTimeout的10s的时候(我们上面的例子是10秒)，就要回头处理这个函数了(下图部分)
>
> ![image-20230210045629876](.\JavaScript高级_image\image-20230210045629876.png)
>
> - 这里的这个函数是怎么执行的？
>
>   1. 在最开始计时的时候，我们是传入一个**回调函数**，这个时候会将这个回调函数放到一个**队列结构(queue)**里面
>
>   2. 而这个队列我们叫做事件队列，执行上下文栈在执行完内容后就会到这个事件队列里面看有没有任务要执行
>
>   3. 队列特点：先进先出
>
>   4. 不止这种情况会被放入事件队列中等待，还会有很多其他种情况也会被放进来(setTimeout哪怕计时是0秒也会被放入事件队列里面)
>
>      ```javascript
>      const btn = querySelector("button")
>      btn.onclick = function(){
>          console.log("btn click function")
>      }
>      //直到我们按下界面上的按钮后(我们btn绑定了点击事件)，这个function函数就会被放到事件队列里面等待执行
>      ```
>
>      

#### 宏微任务执行顺序

- **那么事件循环对于两个队列的优先级是怎么样的呢？**

  - **main script中的代码优先执行**（编写的顶层script代码）；

  - 在**执行任何一个宏任务之前（不是队列，是一个宏任务）**，都会**先查看微任务队列中是否有任务需要执行**

    `√`也就是宏任务执行之前，必须保证微任务队列是空的；

    `√`如果不为空，那么就优先执行微任务队列中的任务（回调）

    > - 在执行一个新的宏任务之前，会保证微任务是一定会被清空的。
    > - 也就是说我们本来要执行宏任务了，这时候微任务来东西了，就会先回头将微任务先处理了再去处理宏任务

```javascript
//最基础的
console.log("1");

function bar(){
  console.log("2");
}

function foo(){
  console.log("3");
  bar()
}

foo()//1 3 2
```

```javascript
  //Promise基础
  console.log("1");  
  //promise 本身传入的就是回调函数(这个回调函数是在全局代码里面执行的)，所以执行顺序是 1 2 3 ，2不会被放到事件队列里面
  new Promise((resolve,reject)=>{
  console.log("2");
  })
  console.log("3");
```

```javascript
//Promise基础+穿插resolve
  console.log("1");  
  new Promise((resolve,reject)=>{
  console.log("2");
  console.log("3");
      resolve("小余")//穿插这个resolve返回了fulfilled状态，那就会返回promise需要进行回调
  console.log("4");
  console.log("5");
  }).then(res =>{
      console.log("进行回调了",res)
  })
  console.log("6");
//输出1 2 3 4 5 6，最后输出了`进行回调了 小余`，很显然resolve的回调被放入了事件队列里面，等其他内容执行完后最后执行(连6都执行得比resolve执行得前面)
```

![image-20230210053905555](.\JavaScript高级_image\image-20230210053905555.png)

## (理解)JavaScript代码的执行顺序-面试题一

```javascript
console.log("script start")
setTimeout(function () {
    console.log("setTimeout1");
    new Promise(function (resolve) {
        resolve();
    }).then(function () {
        new Promise(function (resolve) {
            resolve();
        }).then(function () {
            console.log("then4");
        });
        console.log("then2");
    });
});

new Promise(function (resolve) {
    console.log("promise1");
    resolve();
}).then(function () {
    console.log("then1");
});

setTimeout(function () {
    console.log("setTimeout2");
});

console.log(2);

queueMicrotask(() => {
    console.log("queueMicrotask1")
});

new Promise(function (resolve) {
    resolve();
}).then(function () {
    console.log("then3");
});

//script start
// promise1
// 2
// then1
// queueMicrotask1
// then3
// setTimeout1
// then2
// then4
// setTimeout2
```

![image-20230212000058978](.\JavaScript高级_image\image-20230212000058978.png)

## (理解)JavaScript代码的执行顺序-await

```javascript
console.log("script start")

function requestData(url){
    return new Promise((resolve)=>{
        setTimeout(()=>{
            console.log("setTimeout");
            resolve(url)
        },2000)
    })
}

function getData(){
    console.log("getData start");
    //这是Promise在调度，在下个代码块中我们要修改成async跟await
    requestData("小余").then(res =>{
        console.log("then1-res：",res);
    })
    console.log("getData end");
}

getData()
console.log("script start");
//script start
//getData start
//getData end
//script end
//setTimeout
//then1-res：小余
```

### async、await修改版

```javascript
console.log("script start")

function requestData(url){
    return new Promise((resolve)=>{
        setTimeout(()=>{
            console.log("setTimeout");
            resolve(url)
        },2000)
    })
}

//发生了变化
async function getData(){
    console.log("getData start");
    //await意味着我们将拿到requestData("小余")返回的结果
    const res = await requestData("小余")//这里的代码不会马上执行的，因为我们await拿到的是一个promise，必须要等到Promise有结果了才能够执行。这里就会停顿住2s，因为上面计数器要2s后才会有结果。await其实就相当于.then(res=>{})返回的结果
    console.log("then1-res:",res);//这是微任务对应的执行代码
    console.log("getData end");
}

getData()//async是异步函数，但是跟正常函数一样执行的
console.log("script end");//上面块级作用域停顿住了两秒，后面会先执行

// script start
// getData start
// script end
// setTimeout
// then1-res: 小余
// getData end
```

## (理解)JavaScript代码的执行顺序-面试题二

```javascript
async function async1() {
    console.log('async1 start')
    await async2();//关键在这里，await前面的代码跟await这行的代码是正常执行的。但是await下面的代码必须等await这行的代码(也就是Promise.resolve())有结果了才会被加到`微任务`里面等待执行
    console.log('async1 end')
}

async function async2() {
    console.log('async2')
}

console.log('script start')

setTimeout(function () {
    console.log('setTimeout')
}, 0)

async1();

new Promise(function (resolve) {
    console.log('promise1')
    resolve();
}).then(function () {
    console.log('promise2')
})

console.log('script end')

  // script start
  // async1 start
  // async2
  // promise1
  // script end
  // async1 end
  // promise2
  // setTimeout

```

## (理解)异常处理-异常处理的应用场景

### 错误处理方案

- **开发中我们会封装一些工具函数，封装之后给别人使用：**
  - 在其他人使用的过程中，**可能会传递一些参数**；
  - 对于函数来说，需要**对这些参数进行验证**，否则可能得到的是我们不想要的结果；
- **很多时候我们可能验证到不是希望得到的参数时，就会直接return：**
  -  但是return存在很大的弊端：**调用者不知道是因为函数内部没有正常执行**，还是执行结果就是一个undefined，调用者没有提示也看不到内部的实现过程，肯定就不知道如何进行修改了
  -  事实上，正确的做法应该是**如果没有通过某些验证，那么应该让外界知道函数内部报错了**
- **如何可以让一个函数告知外界自己内部出现了错误呢？**
  -  通过**throw关键字**，抛出一个异常；
- **throw语句：**
  - **throw语句用于抛出一个用户自定义的异常**
  - 当**遇到throw语句**时，**当前的函数执行会被停止**（throw后面的语句不会执行）
-  **如果我们执行代码，就会报错，拿到错误信息的时候我们可以及时的去修正代码**

```javascript
function foo(){
    "abc".filters()//这样导致的报错会让后面一行代码都显示不了。存在很大的安全隐患

    console.log("我赌你看不到我");
    console.log("我是小余");
}

foo()
console.log("----------");
```

![image-20230212035826451](.\JavaScript高级_image\image-20230212035826451.png)

## (理解)异常处理-throw抛出异常的类型

### throw关键字

- **throw表达式就是在throw后面可以跟上一个表达式来表示具体的异常信息：**

  > throw expression
  >
  > 1. 函数中的代码遇到throw，后面的代码都不会执行了(可以中断我们的代码)
  > 2. throw可以抛出一个具体的错误信息

- **throw关键字可以跟上哪些类型呢？**

  - **基本数据类型**：比如number、string、Boolean
  - **对象类型**：对象类型可以包含更多的信息

```javascript
function foo(){
    console.log("小余");
    //- 1.**基本数据类型**：比如number、string、Boolean
    //throw "一个错误"

    //- 2.抛出一个对象
    throw {errMessage:"我是错误信息",errCode:10001}
    console.log("么么哒");
}

foo()
//有了错误码，就能够针对的去查后端设定的对应码的意思(很多公共API都有这种操作)
```

![image-20230212050952950](.\JavaScript高级_image\image-20230212050952950.png)

- **但是每次写这么长的对象又有点麻烦，所以我们可以创建一个类：**

```javascript
class XYError{
    constructor(errCode,errMessage){
        this.errCode = errCode
        this.errMessage = errMessage
    }
}

function foo(){
    console.log("小余");
    //- 1.**基本数据类型**：比如number、string、Boolean
    //throw "一个错误"

    //- 2.抛出一个对象
    //之前的方式
    //throw {errMessage:"我是错误信息",errCode:10001}
    //现在使用类的方式
    throw new XYError("错误信息",10001)
    console.log("么么哒");
}

foo()
//但是平时我们也不这么用，JavaScript给我们提供了Error类了
```

![image-20230212051314596](.\JavaScript高级_image\image-20230212051314596.png)

### Error类型

- **事实上，JavaScript已经给我们提供了一个Error类，我们可以直接创建这个类的对象：**

- **Error包含三个属性：**
  - **messsage**：创建Error对象时传入的message
  - **name**：Error的名称，通常和类的名称一致
  -  **stack**：整个Error的错误信息，包括函数的调用栈，当我们直接打印Error对象时，打印的就是stack
- **Error有一些自己的子类：**
  - RangeError：下标值越界时使用的错误类型
  - SyntaxError：解析语法错误时使用的错误类型
  - TypeError：出现类型错误时，使用的错误类型

```javascript
function num(num1,num2){
    if(typeof num1 !== "number"){//用来提示用户输入正确的内容
        throw new Error("你传入的num1不是数字类型")
    }
    if(typeof num2 !== "number"){
        throw new Error("你传入的num2不是数字类型")
    }
    return num1+num2
}

console.log(num("小余",666));//控制台成功提供提示
```

![image-20230212040926212](.\JavaScript高级_image\image-20230212040926212.png)

### 异常的处理

- **我们会发现在之前的代码中，一个函数抛出了异常，调用它的时候程序会被强制终止：**
  -  这是因为如果我们在调用一个函数时，这个函数**抛出了异常**，但是我们**并没有对这个异常进行处理**，那么这个异常会**继续传**
    **递到上一个函数调用中**
  - 而如果**到了最顶层（全局）的代码中依然没有对这个异常的处理代码**，这个时候就会报错并且终止程序的运行
- **我们先来看一下后面这段代码的异常传递过程：**
  -  foo函数在被执行时会抛出异常，也就是我们的bar函数会拿到这个异常；
  - 但是bar函数并没有对这个异常进行处理，那么这个异常就会被继续传递到调用bar函数的函数，也就是test函数；
  - 但是test函数依然没有处理，就会继续传递到我们的全局代码逻辑中；
  - 依然没有被处理，这个时候程序会终止执行，后续代码都不会再执行了

```javascript
function foo(){
    throw "小余 error message"
}

function bar(){
    foo()
}
function test(){
    bar()
}

test()
console.log("test后续代码")
```

## (理解)异常处理-捕获异常的方式

### 异常的捕获

- **但是很多情况下当出现异常时，我们并不希望程序直接退出，而是希望可以正确的处理异常：**
  - 这个时候我们就可以使用**try catch**

```javascript
function foo(){
    throw "小余 error message"
}

function bar(){
    try {
        foo()
    } catch (error) {
        console.log(error);
    }
}

bar()//小余 error message
```

- 在ES10（ES2019）中，catch后面绑定的error可以省略
- **当然，如果有一些必须要执行的代码，我们可以使用finally来执行：**
  - finally表示最终一定会被执行的代码结构；
  - 注意：如果try和finally中都有返回值，那么会使用finally当中的返回值
