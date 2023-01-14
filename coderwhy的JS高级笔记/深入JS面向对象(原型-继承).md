# 深入JS面向对象

> ### 作者：小余同学
>
> 这是我的 [GitHub](https://github.com/2002XiaoYu) 地址，所有笔记都会进行实时更新，所有不妨给作者这个项目点个star，方便后续再找到继续获取最新的笔记，你的star是作者更新的动力，祝你在学习的路上一帆风顺

[TOC]

### 面向对象是现实的抽象方式

> 现实世界的东西大多数都是可以在编程中抽象出来的
>
> 比如你可以抽象出一个女朋友new GridFriend()，或者抽象出coderwhy或者小满什么的来，可惜只能单向的抽象哈哈，不能映射到现实
>
> 编程是对现实世界的抽象，而面向对象是对现实世界抽象的一种方式

- 对象是JavaScript中一个非常重要的概念，这是因为对象可以**将多个相关联的数据封装**到一起，更好的描述一个事物：
  - **比如我们可以描述一辆车**：Car，具有颜色（color）、速度（speed）、品牌（brand）、价格（price），行驶（travel）等 等
  - **比如我们可以描述一个人**：Person，具有姓名（name）、年龄（age）、身高（height），吃东西（eat）、跑步（run） 等等；
- 用**对象来描述事物**，更有利于我们**将现实的事物**，抽离成代码中**某个数据结构**：
  - 所以有一些编程语言就是纯面向对象的编程语言，比Java；
  - 你在实现任何现实抽象时都需要先创建一个类，根据类再去创建对象

### JavaScript的面向对象

- JavaScript其实支持多种编程范式，包括**函数式编程和面向对象编程：**
  - JavaScript中的对象被设计成一组**属性的无序集合**，像是一个**哈希表**，有key和value组成；
  - **key是一个标识符名称，value可以是任意类型**，也可以是**其他对象或者函数类型**；
  - 如果值**是一个函数**，那么我们可以称之为是**对象的方法**； 

- **如何创建一个对象？**
- 早期使用创建对象的方式最多的是**使用Object类**，并且使用**new关键字**来创建一个对象：
  - 这是因为早期很多JavaScript开发者是从Java过来的，它们也更习惯于Java中通过new的方式创建一个对象；
- 后来很多开发者为了方便起见，都是直接通过**字面量的形式来创建对象**： 
  - 这种形式看起来更加的简洁，并且对象和属性之间的内聚性也更强，所以这种方式后来就流行了起来；

#### 创建对象的方式

```javascript
//创建一个对象，对某个人进行抽象?(描述)
//方式1：通过new Object()创建
var obj1 = new Object()//以前的人喜欢这样创建对象
obj1.name = "洛洛"
obj1.age = 20
obj1.sex = "女"//然后这样抽象属性
//上面的方式是对obj1当作一个构造函数，然后通过new关键字来执行函数，这个时候也会创建出来对象
//方式2：字面量形式
var obj = {
    name:"小余",
    age:20,
    sex:"男",
    eating:function(){
        console.log(this.name+"在吃辣条~")
    }
}//这种创建对象方式叫做对象的字面量，现在更多是这种
```

### 对属性操作的控制

- 在前面我们的属性都是**直接定义在对象内部**，或者**直接添加到对象内部**的：

  - 但是这样来做的时候我们就**不能对这个属性进行一些限制**：比如**这个属性是否是可以通过delete删除的**？这个 属性**是否在for-in遍历的时候被遍历出来呢？**

    ```javascript
    var obj = {
        name:"小余",
        age:20,
        sex:"男"
    }
    //对属性的控制
    //获取属性
    console.log(obj.name)//小余
    //给属性赋值
    obj.name = "xiaoyu"
    console.log(obj.name)//xiaoyu
    //删除属性
    delete obj.name
    console.log(obj)//{ age: 20, sex: '男' }
    ```

- 如果我们想要对一个属性进行比较精准的操作控制，那么我们就可以使用属性描述符。

  - 通过属性描述符**可以精准的添加或修改对象的属性**； 
  - 属性描述符需要使用 **Object.defineProperty** 来对属性进行添加或者修改

### Object.defineProperty

- Object.defineProperty()方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象

  - 会修改我们的原对象的，所以这个并不是一个纯函数

  ```javascript
  Object.defineProperty(obj,prop,descriptor)
  ```

- 可接收三个参数：

  - obj：要定义属性的对象
  - prop：要定义或修改的属性的名称或者Symbol
  - descriptor：要 定义或修改的属性描述符

- 返回值：

  - 被传递给函数的对象

```javascript
var obj = {
    name:"xiaoyu",
    age:20
}
Object.defineProperty(obj,"height",{
    //很多的配置
    value:1.75
})

console.log(obj)
//node环境下打印
//{ name: 'xiaoyu', age: 20 }
```

控制台打印：

![image-20230107083010161](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230107083010161.png)

> 出现如上问题的原因：
>
> - 因为height的属性是不可枚举，不可遍历的。所以我们在node环境下整体打印就看不到新增的height，但是我们可以局部打印还是可以出来的，例如下方的案例，说明这个height已经真真实实的添加到我们的obj里面了，只是我们看不到而已
>
> ```javascript
> var obj = {
>     name:"xiaoyu",
>     age:20
> }
> Object.defineProperty(obj,"height",{
>     //很多的配置，我们在这里写入的就是属性描述符
>     value:1.75
> })
> console.log(obj.height);//1.75
> ```

### 属性描述符分类

-  属性描述符的类型有两种：
   - **数据属性**（Data Properties）描述符（Descriptor）；
   - **存取属性**（Accessor访问器 Properties）描述符（Descriptor）；

|                | configurable(可配置的) | enumerable(可枚举的) | value(值) | writable(可写的) | get(获取) | set(设置) |
| -------------- | ---------------------- | -------------------- | --------- | ---------------- | --------- | --------- |
| **数据描述符** | 可以                   | 可以                 | 可以      | 可以             | 不可以    | 不可以    |
| **存取描述符** | 可以                   | 可以                 | 不可以    | 不可以           | 可以      | 可以      |

#### 数据属性描述符

- 数据属性描述符有如下四个特征
- [[Configurable]]：表示属性是否可以通过delete删除属性，是否可以修改它的特性，或者是否可以将它修改为存取属性描述符
  - 当我们直接在一个对象上定义某个属性时，这个属性的[[Configurable]]为true
  - 当我们通过属性描述符定义一个属性时，这个属性的[[Configurable]]默认为false
- [[Enumerable]]：表示属性是否可以通过for-in或者Object.key()返回该属性;
  - 当我们直接在一个对象上定义某个属性时，这个属性的[[Enumerable]]为true
  - 当我们通过属性描述符定义一个属性时，这个属性的[[Enumerable]]默认为false
- [[Writable]]：表示是否可以修改属性的值；
  - 当我们直接在一个对象上定义某个属性时，这个属性的[[Writable]]为true
  - 当我们通过属性描述符定义一个属性的时候，这个属性的[[Writable]]为false
- [[value]]：属性的value值，读取属性时会返回该值，修改属性时，会对其进行修改；
  - 默认情况下这个值是undefined

```javascript
//name和age虽然没有使用属性描述符来定义，但是它们也是具备对应的特性的，以下是对应的默认值
//value:赋值的value
//configurable:true
//enumerable:true
//writable:true
var obj = {
    name:"xiaoyu",
    age:18
}

//数据属性描述符
Object.defineProperty(obj,"address",{
    //很多配置
    value:"福建省",//默认值undefined
    //该属性不可删除，不可修改。不可以重新定义属性描述符
    configurable:false//默认值false
    //该特性是配置对应的属性(address)是否是可以枚举的
    enumerable:true,//默认值false
    //该特性是否可以赋值
    writable:false//默认值false
})

delete obj.name
console.log(obj)//{ age: 18 }，name被成功删除
delete obj.address
console.log(obj.address);//福建省	没删除掉，因为我们设置了不可配置configurable:false

//测试enumerable的作用
console.log(obj)
for(var key in obj){
    console.log(key,'for遍历');//如果enumerable为false，则只会出来name和age，address只有设置为true的时候才会出来
}
console.log(Object.keys(obj),'keys的作用');
//enumerable前后对比
//[ 'name', 'age' ] keys的作用(enumerable:false)
//[ 'name', 'age', 'address' ] keys的作用(enumerable:true)

//测试writable的作用
obj.address = "上海市"
console.log(obj.address);//福建省，新的内容不可写入。如果我们不设置value为福建省，则在不可写入的情况下显示undefined
```

#### 存取属性描述符

- 数据描述符有如下四个特征：
- [[Configurable]]：表示属性是否可以通过delete删除属性，是否可以修改它的特性，或者是否可以将它修改为存取属性 描述符
  - 和数据属性描述符是一致的
  - 当我们直接在一个对象上定义某个属性时，这个属性的[[Configurable]]为true；
  - 当我们通过属性描述符定义一个属性时，这个属性的[[Configurable]]默认为false；
- [[Enumerable]]：表示属性是否可以通过for-in或者Object.keys()返回该属性;
  - 和数据描述符是一致的
  - 当我们直接在一个对象上定义某个属性时，这个属性的[[Enumerable]]为true
  - 当我们通过属性描述符定义一个属性时，这个属性的[[Enumerable]]默认为false；
- [[get]]：获取属性时会执行的函数，默认为undefined
- [[set]]：设置属性时会执行的函数，默认为undefined

```javascript
var obj = {
    name:"xiaoyu",
    age:18,
    _address:"泉州市"//_开头表示私有的，不希望被人看到。我们就通过get来使用address代替掉_address。别人就通过address调用我们，而不是使用_address调用
}

//当我们使用get、set，不使用value和writable的时候，叫做存取属性描述符
//使用场景：1.隐藏某一个私有属性，不希望直接被外界使用和赋值
//2.如果我们希望截获某一个属性，它访问和设置值的过程时，我们也会使用存储属性描述符
Object.defineProperty(obj,"address",{
    //很多配置
    enumerable:true,
    configurable:true,
    //value跟writable与get、set不能共存
    // value:"福建省",
    // writable:true,
    get:function(){
        foo()//我们希望获取值的时候提醒我们一下的时候就会这么干
		return this._address//将_address这个属性隐藏起来，给他套上了address这个马甲
    },
    set:function(value){
        bar()//这样就截获了它获取值的过程，这是Vue2响应式的原理
		//当我们如下对obj.address进行赋值的时候，值就通过形参传递了进来，我们在这里进行赋值的操作
        this._address = value
    }
})

console.log(obj)//{ name: 'xiaoyu', age: 18, address: [Getter/Setter] }
console.log(obj.address);//泉州市，get拿到了值
obj.address = "小满的后宫"//我们使用的是address，而不是_address了哦，注意这里的变化
console.log(obj.address);//小满的后宫

function foo(){
    console.log("获取了一次address的值")
}
function bar(){
    console.log("设置了一次address的值")
}
```

# 继承的实现方案、ES6面向对象

## 对象上同时定义多个属性

- Object.defineProperties() 方法直接在一个对象上定义 **多个** 新的属性或修改现有属性，并且返回该对象。

```javascript
var obj = {
    //私有属性(js中没有严格意义上的私有属性，你依旧可以通过obj._age访问到，但是外人是不知道这个隐藏起来的属性，只知道他的替代obj.age)
    _age:20
}
Object.defineProperties(obj,{
    name:{
        //是否可配置
        configurable:true,
        //是否枚举
        enumerable:true,
        //添加新值
        value:"小余",
        //是否可写入
        writable:true
    },
    age:{
        configurable:false,
        enumerable:false,
        get:function(){
            return this._age
        },
        set:function(value){
            this._age = value
        }
    }
})

console.log(obj.age)//20
console.log(obj,"这是为了看age的不可枚举是否生效");//{ _age: 20, name: '小余' } 这是为了看age的不可枚举是否生效
obj.age = 18
console.log(obj.age);//18
```

- 在开发中，如果我们想对某一个属性定义对应的get和set，我们也可以这么做：

  > 这种写法有一点差异，但是性能是差不多的(但如果我们想要更加精准的控制的话，还是采用在defineProperties写的方式好，因为这样子才能配置configurable这类的东西)
  >
  > 差异在于我们这里控制台打印出来的是能够看到age的，而在Object.defineProperties中是看不到的，在下方代码块中我将他们的对比效果贴出来了，放在最底下

  ```javascript
  var obj = {
      _age:20,
      set age(value){
          this._age = value
      },
      get age(){
          return this._age
      }
  }
  //以上的age写法就替代了我们在Object.defineProperties的写法
  // age:{
  //     configurable:false,
  //     enumerable:false,
  //     get:function(){
  //         return this._age
  //     },
  //     set:function(value){
  //         this._age = value
  //     }
  // }
  
  //差异对比：控制台打印obj
  //直接在对象中写法打印效果：
  { _age: 20, age: [Getter/Setter], name: '小余' }//表示的age属性有get和set
  //在Object.defineProperties中的get、set打印效果：
  { _age: 20, name: '小余' }
  ```

## 对象方法补充

- 获取对象的属性描述符：
  - getOwnPropertyDescriptor
  - getOwnPropertyDescriptors
- 禁止对象扩展新属性：preventExtensions
  - 给一个对象添加新的属性会失败(在严格模式下会报错)
- 密封对象，不允许配置和删除属性：seal
  - 实际是调用preventExtensions
  - 并且将现有属性的configurable：false
- 冻结对象，不允许修改现有属性：freeze
  - 实际上是调用seal
  - 并且将现有属性的writable：false

```javascript
var obj = {
    _age:20,
}
Object.defineProperties(obj,{
    name:{
        //是否可配置
        configurable:true,
        //是否枚举
        enumerable:true,
        //添加新值
        value:"小余",
        //是否可写入
        writable:true
    },
    age:{
        configurable:false,
        enumerable:false,
        get:function(){
            return this._age
        },
        set:function(value){
            this._age = value
        }
    }
})
//获取某一个特征属性的属性描述符
console.log(Object.getOwnPropertyDescriptor(obj,'name'));
//{ value: '小余', writable: true, enumerable: true, configurable: true }这个就是name的配置了
console.log(Object.getOwnPropertyDescriptor(obj,'age'));
//{
//  get: [Function: get],
//  set: [Function: set],
//  enumerable: false,
//  configurable: false
//}

//获取对象的所有属性描述符
console.log(Object.getOwnPropertyDescriptors(obj));//请注意这个的区别跟上面那个最后多了一个s
// {
//     _age: { value: 20, writable: true, enumerable: true, configurable: true },
//     name: { value: '小余', writable: true, enumerable: true, configurable: true },
//     age: {
//       get: [Function: get],
//       set: [Function: set],
//       enumerable: false,
//       configurable: false
//     }
// }
```

### Object的方法对对象的限制

```javascript
//禁止对象继续添加新的属性
var obj = {
    _age:20,
}
Object.preventExtensions(obj)
obj.name = "小余"
obj.sex = "男"
console.log(obj);//{ _age: 20 }，阻止添加成功
```

```javascript
//禁止对象配置/删除里面的属性
//麻烦的方式：
var obj = {
    _age:20,
    name:"小余"
}
for(var key in obj){
    Object.defineProperty(obj,key,{
        //将每一个属性都设置为不可配置，包括禁止删除里面的属性
        configurable:false,
        value:key
    })
}
console.log(Object.getOwnPropertyDescriptors(obj));//自己打印出来看看结果，其中的configurable确实都为false了
//简单的方式：
Object.seal(obj)//尝试将这行代码注释掉，看前后对比
//验证方式1：
delete obj.name
console.log(obj.name);
//验证方式2：
console.log(Object.getOwnPropertyDescriptors(obj));
```

```javascript
//让属性不可以修改(相当于让writablel:false)
var obj = {
    //私有属性(js中没有严格意义上的私有属性，你依旧可以通过obj._age访问到，但是外人是不知道这个隐藏起来的属性，只知道他的替代obj.age)
    _age:20,
    name:"小余"
}

Object.freeze(obj)
obj.name = "小满zs"

console.log(obj);//{ _age: 20, name: '小余' }，name没有被修改为"小满zs"
```

## 创建多个对象的方案

- 如果我们现在希望创建一系列的对象：比如Person对象

  - 包括张三、李四、王五、李雷等等，他们的信息各不相同；
  - 那么采用什么方式来创建比较好呢？

- 目前我们已经学习了两种方式：

  - new Object方式；
  - 字面量创建的方式； 

  ```javascript
  var p1 = {
      name:"小余",
      age:20,
      sex:"男",
      address:"福建",
      eating:function(){
          console.log(this.name+"在吃烧烤");
      },
      running:function(){
          console.log(this.name+"在跑步做运动");
      }
  }
  
  var p2 = {
      name:"小满",
      age:23,
      sex:"男",
      address:"北京",
      learn:function(){
          console.log(this.name+"在学编程");
      },
      running:function(){
          console.log(this.name+"在跑步做运动");
      }
  }
  
  var p3 = {
      name:"洛洛",
      age:20,
      sex:"女",
      address:"福建",
      learn:function(){
          console.log(this.name+"在学Go语言跟rust语言");
      },
      running:function(){
          console.log(this.name+"在内卷");
      }
  }
  //以上的方式过于相似，存在过于重复的代码，我们能不能进行优化呢，用另一种方式创建？
  ```

  

- 上面这种方式有一个很大的弊端：创建同样的对象时，需要编写重复的代码；

### 创建对象的方案 – 工厂模式

- 我们可以想到的一种创建对象的方式：工厂模式
  - 工厂模式其实是一种常见的设计模式；
  - 通常我们会有一个工厂方法，通过该工厂方法我们可以产生想要的对象；

```javascript
function createPerson(){

}
//我们虽然里面属性大多数都是相同的，但是数据是不一样的，比如p1的name是小余，p2的是小满，p3的是洛洛，这个时候我们就可以向调用的里面传入参数来实现我们不同数据的传输
var p1 = createPerson()
var p2 = createPerson()
var p3 = createPerson()
```

```javascript
//上面那个空模板的改善方式，传入参数
function createPerson(name,age,sex,occupation,address){
	var p = new Object()
    p.name = name
    p.age = age
    p.sex = sex
    p.occupation = occupation
    p.address = address
    p.eating = function(){
        console.log(this.name + "在吃满汉全席")
    }
    return p
}

var p1 = createPerson("小余",20,"男","大二学生","福建",)
var p2 = createPerson("小满",24,"男","京东程序员","北京")
var p3 = createPerson("洛洛",20,"萌妹子","Go+Rust+JavaScript+Node.js全栈工程师兼小余的同学",'福建')

console.log(p1,p2,p3);
//打印效果如下：
// {
//     name: '小余',
//     age: 20,
//     sex: '男',
//     occupation: '大二学生',
//     address: '福建',
//     eating: [Function (anonymous)]
//   } {
//     name: '小满',
//     age: 24,
//     sex: '男',
//     occupation: '京东程序员',
//     address: '北京',
//     eating: [Function (anonymous)]
//   } {
//     name: '洛洛',
//     age: 20,
//     sex: '萌妹子',
//     occupation: 'Go+Rust+JavaScript+Node.js全栈工程师兼小余的同学',
//     address: '福建',
//     eating: [Function (anonymous)]
//   }
```

![image-20230109045442038](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230109045442038.png)

#### 工厂函数的缺点

```javascript
1. 通过上述console.log(p1,p2,p3)打印出来的是我们的字面量

2. 缺少对象应该有的类型，只能看到是Prototype那里的类型都是Object，这种形容过于宽广，分类不够具体。因为人是动物，猫咪也是动物，但两者是不能够混为一谈的。获取不到对象最真实的类型

3. 我们想要达到的效果是：当我们拿到p1、p2、p3的时候，我们还能够知道他们对应的类型，知道这是由什么产生的，而这个是工厂函数没办法做到的事情
//如果你对上面的类型还一知半解，也可以参考我的想法，我认为这是分类过于模糊，我们通过工厂函数创建出来了一个"人"，他身上的属性有姓名，身高，职业，性别等等，我们通过工厂函数传入参数也只是对数据进行改变，他本质上的属性是一样的，脱离不了人本身，最多一个叫小余，另一个叫小满，两个不一样的人，但都是人。这个时候我们希望调用工厂函数这个函数的时候，告诉我们类型是人，具体一些，而不是"宇宙中存在的东西"，那太过于宽广，实在是跟没说一样，毕竟你抽象出来的东西哪个不是宇宙中的东西对吧
```

### 认识构造函数

- 工厂方法创建对象有一个比较大的问题：我们在打印对象时，对象的类型都是Object类型
  - 但是从某些角度来说，这些对象应该有一个他们共同的类型；
  - 下面我们来看一下另外一种模式：构造函数的方式；
- 我们先理解什么是构造函数？
  - 构造函数也称之为构造器（constructor），通常是我们在创建对象时会调用的函数；
  - 在其他面向的编程语言里面，构造函数是存在于类中的一个方法，称之为构造方法；
  - 但是JavaScript中的构造函数有点不太一样；
- JavaScript中的构造函数是怎么样的？
  - 构造函数也是一个普通的函数，从表现形式来说，和千千万万个普通的函数没有任何区别；
  - 那么如果这么一个普通的函数被使用new操作符来调用了，那么这个函数就称之为是一个构造函数；
  - 那么被new调用有什么特殊的呢？

#### new操作符调用的作用

- 如果一个函数被使用new操作符调用了，那么它会执行如下操作：

  1. 在内存中创建一个新的对象（空对象）；

  2. 这个对象内部的[[prototype]]属性会被赋值为该构造函数的prototype属性；（后面详细讲）；

  3. 构造函数内部的this，会指向创建出来的新对象；

  4. 执行函数的内部代码（函数体代码）；

     ```javascript
     function foo(){
         var moni = {}
         this.moni
     }
     //new foo相当于会默认执行上面这两个步骤(平时是不显示的，内部自己执行的)，然后进行我们的第五步返回创建出来的新对象，不需要我们手写return返回值
     ```

  5. 如果构造函数没有返回非空对象，则返回创建出来的新对象；

     ```javascript
     function foo(){
         console.log("foo~");
     }
     
     var f1 = new foo//foo~
     console.log(f1);//foo {} 确实创建出来的一个空对象，且类型就是foo
     
     //很明显类型精准了很多，如果上面不够明显，我们可以再来一个案例(输出xiaoyu {}，已经很能证明了吧)
     function xiaoyu(){
         console.log("我是小余");
     }
     
     var f1 = new xiaoyu//我是小余
     console.log(f1);//xiaoyu {}，node打印结果
     ```

     - 浏览器控制台打印结果：

       ![image-20230109055524421](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230109055524421.png)

```javascript
 function foo(){
    console.log("foo~");
}

//foo就是一个普通的函数
foo()
//换一种方式来调用foo函数，使用操作符new
new foo()//一旦我们通过new这样调用，那么这个函数就是一个构造函数了
new foo //我们甚至可以不写这个小括号
//上面3处调用的结果都是foo~，全部成功调用。请注意我说的是3处，也就是上面第四点说的：执行函数的内部代码（函数体代码）

//当我们通过new去调用一个函数时，和普通的调用到底有什么区别？
```

##### 函数什么时候return

> 这里临时补充一个知识点，突然模糊了什么时候在函数中什么时候要写return？前面有说过，但是好像没写上，这里补一下：
>
> - 在JavaScript中，当你想要从一个函数中返回一个值的时候，就可以使用`return`关键字。
>
>   这里的`return`的意义是结束函数的执行，并将指定的值作为结果返回给调用函数的代码。例如，你可以这样定义一个函数：
>
>   ```javascript
>   function add(a, b) {
>     return a + b;
>   }
>   ```
>
> - 这个函数接受两个参数，并返回它们的和。你可以调用这个函数，并将返回值赋值给一个变量，例如：
>
>   ```javascript
>   let sum = add(1, 2); // sum的值为3
>   ```
>
> - 在函数内部，你可以使用多个`return`语句，但是只有第一个会被执行，因为一旦执行了`return`语句，函数就会立即结束。
>
>   如果你希望函数执行完所有的代码后再返回一个值，你可以在函数的最后一行不指定任何返回值，这样函数就会返回一个特殊的值`undefined`。
>
>   举个例子，假设你有一个函数，它接受一个数字并返回它的平方：
>
>   ```javascript
>   function square(x) {
>     console.log(x * x);
>   }
>   
>   let result = square(2); // 输出4，result的值为undefined
>   ```
>
>   在这个函数中，我们没有使用`return`语句来明确地返回一个值，所以函数会返回`undefined`。

### 创建对象的方案 – 构造函数

- 我们来通过构造函数实现一下：

  ```javascript
  function xiaoyu(name,age,sex,address){
      this.name = name
      this.age = age
      this.sex = sex
      this.address = address
  
      this.eating = function(){
          console.log(this.name + "在吃鱿鱼须");
      }
      this.runding = function(){
          console.log(this.name + "在跟坤坤打篮球");
      }
  }
  
  var f1 = new xiaoyu("小余同学",20,"男","福建")
  console.log(f1);
  
  // xiaoyu {
  //     name: '小余同学',
  //     age: 20,
  //     sex: '男',
  //     address: '福建',
  //     eating: [Function (anonymous)],
  //     runding: [Function (anonymous)]
  //   }
  
  //然后跟工厂函数一样的，我们可以进行重复描写
  var f2 = new xiaoyu("小满zs",23,"男","北京")
  var f3 = new xiaoyu("洛洛",20,"萌妹子","福建")
  //很明显，在开头多了一个类型xiaoyu，是不是更加明确清晰了。你想要的任意类型都能够自己更加精准的定位，你写ikun都行
  ```

  ![image-20230109060143434](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230109060143434.png)

- 这个构造函数可以确保我们的对象是有Person的类型的（实际是constructor的属性，这个我们后续再探讨）；

- 但是构造函数就没有缺点了吗？

  - 构造函数也是有缺点的，它在于我们需要为每个对象的函数去**创建一个函数对象实例**；

#### 如何区分是否是构造函数

> 一般来说，构造函数实在跟普通函数没有区别，单纯看一个函数是看不出来的，于是社区对此有了约定俗成的规范，不是必须要遵守，但拥抱规范能够让我们平时更加方便，减少大家的理解跟沟通成本
>
> ```javascript
> function XiaoYu(){
> //对于构造函数，我们函数名首字母会是大写。如果由多个单词组成的话，则是采用大驼峰标识
> }
> ```

#### 构造函数的缺点

> 1. 为什么是f1 === f2是false，那是因为他们创建出来的对象不是同一个对象，虽然打印出来都是[Function: bar]，但是确实是不一样的两个对象
>
> 2. 那为什么不是同一个对象？
>
>    - 第一次执行的时候，我们在foo函数里面定义了bar函数，执行foo函数的时候(函数调用)，会在内存中创建一个bar的函数对象。然后我们用f1接收了这个函数对象之后，第二次执行会重新创建一个bar函数对象，然后放入f2中。此时是同时存在两个函数对象的
>
> 3. 它在于我们需要为每个对象的函数去**创建一个函数对象实例**；(也就是写在开头的那句话
>
>    > 这里创建的函数对象实例为什么会是缺点？
>
>    - 每个对象创建一个函数实例会增加内存的开销，这**可能会导致性能问题**，特别是当你有大量的对象的时候。此外，如果你在构造函数中改变了对象的原型，这**也会导致每个对象都有不同的原型，这可能会增加代码的复杂性**。

```javascript
function foo(){
    function bar(){
        console.log("你猜一不一样");
    }
    return bar
}

var f1 = foo()
var f2 = foo()
console.log(f1 === f2);//false
```

```javascript
//我们还可以拿刚刚的案例来试一下
function xiaoyu(name,age,sex,address){
    this.name = name
    this.age = age
    this.sex = sex
    this.address = address

    //我们这里的创建对象，相当于在对象里再重复的创建对象其实是没有必要的，就是每个对象的函数去创建一个函数对象实例；为什么这个会是缺点看上面第三点
    this.eating = function(){
        console.log(this.name + "在吃鱿鱼须");
    }
    this.runding = function(){
        console.log(this.name + "在跟坤坤打篮球");
    }
}

var f1 = new xiaoyu("小余同学",20,"男","福建")
var f2 = new xiaoyu("小余同学",20,"男","福建")
//name其实都是"小余同学"，他们的值是没有区别的，造成他们不相等的原因是：每个对象都有不同的原型，所以不相等
console.log(f1.eating === f2.eating);//false
console.log(f1.runding === f2.runding);//false
```

## 认识对象的原型

> 当我们对一个东西定义在[[]]里面的时候，证明这个是ECMA标准把它叫做这个名字，prototype翻译过来就是原型的意思

- JavaScript当中每个对象都有一个特殊的内置属性 [[prototype]]，这个特殊的对象可以指向另外一个对象。
- 那么这个对象有什么用呢？
  - 当我们通过引用对象的属性key来获取一个value时，它会触发 [[Get]]的操作；
  - 这个操作会首先检查该属性是否有对应的属性，如果有的话就使用它；
  - 如果对象中没有改属性，那么会访问对象[[prototype]]内置属性指向的对象上的属性；
- 那么如果通过字面量直接创建一个对象，这个对象也会有这样的属性吗？如果有，应该如何获取这个属性呢？
  - 答案是有的，只要是对象都会有这样的一个内置属性；
- 获取的方式有两种：
  - 方式一：通过对象的 `__proto__` 属性可以获取到（但是这个是早期浏览器自己添加的，存在一定的兼容性问题）；
  - 方式二：通过 `Object.getPrototypeOf` 方法可以获取到；

### 对象的原型

```javascript
//我们每个对象中都有一个[[prototype]]，这个属性可以称之为对象的原型(隐式原型)，这里很重要
//称呼为隐式原型的原因：1.平时看不到2.以后也不会去改他3.也不会去使用他。我们只会利用他的底层原理来使用他

//解释原型的概念和看原型
var obj = {name:"小余"}
console.log(obj);//在node打印是看不到隐藏起来的[[prototype]]的，这个需要到浏览器的控制台去看一下(也就是功能是浏览器提供的)
console.log(obj.__proto__);//[Object: null prototype] {}
```

![image-20230109203959859](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230109203959859.png)

> 这个Prototype是浏览器提供的，但有些浏览器可能是没有实现这个东西，所以我们在开发的时候尽量不使用这个。但是理解内部的原理还是很有必要的
>
> 但是他提供了一个属性叫做`__proto__`，通过上面的图片倒数第三个我们也能够看到，在Prototype里面，那proto翻译过来其实也就是原型的意思

```javascript
//通常情况下我们是不能使用__proto__这个原型的，如果你真的想要获取这个对象的话，有提供另一个办法供你获取
var obj = {name:"小余"}
//获取obj的原型，是ES5之后提供的方法，不是浏览器提供的
console.log(Object.getPrototypeOf(obj));//node打印出来的结果：[Object: null prototype] {}
```

### 原型有什么用?

```javascript
//当我们从一个对象中获取某一个属性时，它会触发[[get]]操作
var obj = {name:"小余"}
console.log(obj.age)//像这样取值的时候就会触发get操作，操作分2步
//1.在当前对象中去查找对应的属性，如果找到就直接使用
//2.如果没有找到，会沿着原型去查找，也就是var obj = {name:"小余",__proto__}，先从前面开始找，没找到再从__proto__(原型)找
```

```javascript
//我们在上面是找不到obj的age属性的，因为我们都没有定义age这个属性，age除了obj.age = 18这种方式之外，还可以直接赋值在原型中，也能够找到，这就是顺着原型去查找的
var obj = {name:"小余"}
obj.__proto__.age = 18
console.log(obj.age)//18

//为什么要这么麻烦，要放到原型中，不直接放到对象里面呢？这是为了方便我们后续实现继承
```

### 函数的原型 prototype

- 那么我们知道上面的东西对于我们的构造函数创建对象来说有什么用呢？

  - 它的意义是非常重大的，接下来我们继续来探讨；

- 这里我们又要引入一个新的概念：所有的函数都有一个prototype的属性：

  - 这个`prototype`属性被称为显示原型属性

  ```javascript
  function foo(){
      
  }
  ////函数作为对象来说，它也是有[[prototype]]隐式原型的
  console.log(foo.__proto__)//{}
  //函数因为它是一个函数，所以还会多出来一个显示原型属性，叫做prototype
  console.log(foo.prototype)//{}，这个属性是没有兼容性问题的，ECMA一开始就定义了这个属性
  ```

- 你可能会问题，是不是因为函数是一个对象，所以它有prototype的属性呢？

  - 不是的，因为它是一个函数，才有了这个特殊的属性；
  - 而不是它是一个对象，所以有这个特殊的属性；

  ```javascript
  var obj = {}
  console.log(obj.prototype)//obj就没有这个属性，undefined
  ```

### 再看new操作符

- 我们前面讲过new关键字的步骤如下：

  1. 在内存中创建一个新的对象（空对象）；

  2. 这个对象内部的[[prototype]]属性会被赋值为该构造函数的prototype属性；（后面详细讲）；

     ```javascript
     //代码的内部实现如下
     function foo(){
         var moni = {}
         this = {}
     
         this.__proto__ = foo.prototype//隐式原型指向显示原型
     }
     ```

     ```javascript
     function foo(){
     }
     
     var f1 = new foo()
     var f2 = new foo()
     
     //这就是构造函数的f1跟f2的隐式原型会指向函数的显示原型
     console.log(f1.__proto__ === foo.prototype);//true
     console.log(f2.__proto__ === foo.prototype);//true
     ```

- 那么也就意味着我们通过Person构造函数创建出来的所有对象的[[prototype]]属性都指向Person.prototype：

## Person构造函数原型内存图

![image-20230109225438553](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230109225438553.png)

![image-20230109224443444](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230109224443444.png)

![image-20230109224459797](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230109224459797.png)

```javascript
//上面为内存图表现，p1对象跟p2对象的__proto__隔壁那个空格是指内存地址
function Person(){
    
}
var p1 = new Person()
var p2 = new Person()
//函数的执行体创建p1的对象，还有p2的对象，这个是我们一开始就通过var p1 = new Person()还有var p2 = new Person()达成的，这个对象`new Person`是构造函数，前面我们已经学习过了，然后他们身上是有隐式原型__proto__的，而隐式原型是会指向Person函数的原型对象的显示原型prototype。总结就是p1跟p2指向了构造函数，构造函数的隐式原型指向了函数的显示原型。这也就造就了下面两个true相等的原因
console.log(p1.__proto__ === Person.prototype);//true
console.log(p2.__proto__ === Person.prototype);//true
```

### 赋值为新的对象

![image-20230109233824306](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230109233824306.png)

```javascript
function Person(){

}
var p1 = new Person()
var p2 = new Person()
//想要取到对象中没有的属性的办法：可以在原型中直接加上去，那在p1中找不到就会去原型上找，然后就可以找到了
p1.__proto__.name = "小余"
console.log(p1.name);//小余

//但其实还有更好的办法，我们可以直接放在函数Person的显示原型里面，然后按照顺序，p1里面找不到就去p1自己的隐式原型__proto__找，如果也找不到就会继续往上追溯到Person的显式原型prototype中找，那这回可算找到了
Person.prototype.name = "XiaoYu"
console.log(p1.name);//XiaoYu

//如果我们对p2的隐式原型进行修改，一样会作用到p1身上，这是为什么？
//规范回答：new操作符导致的：这个对象内部的[[prototype]]属性会被赋值为该构造函数的prototype属性；那么也就意味着我们通过Person构造函数创建出来的所有对象的[[prototype]]属性都指向Person.prototype
//理解回答：因为p2的隐式原型会指向Person的显式原型prototype，而p1最终也是会指向到Person的显式原型，所以他们会找到同一个地方，所以就会导致p2隐式原型的改动也会影响到p1隐式原型的改动，因为他们最终追溯到的都是一样的地方
p2.__proto__.name = "大满"
console.log(p1.name);//大满
```

## 函数原型上的属性

### prototype添加属性

![image-20230109235539333](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230109235539333.png)

### constructor属性

- 事实上原型对象上面是有一个属性的：constructor
  - 默认情况下原型上都会添加一个属性叫做constructor，这个constructor指向当前的函数对象；

```javascript
function foo(){

}

console.log(foo.prototype,"纯foo.prototype打印")//这个打印出来是个空对象{}，但是事实上这个并不是空的，只是因为可枚举属性被设置为了false
console.log(Object.getOwnPropertyDescriptors(foo.prototype),"getOwnPropertyDescriptors打印foo.prototype");
```

![image-20230110000506456](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230110000506456.png)

```javascript
//我们知道是事实上这个并不是空的，只是因为可枚举属性被设置为了false，那就可以采取另一种方式，将他的枚举属性设置为true，那就可以看到了
function foo(){

}

Object.defineProperty(foo.prototype,"constructor",{
    enumerable:true,
    configurable:true,
    writable:true,
    value:"小余今天抓住了一只小满"
})

console.log(foo.prototype.constructor);//能够打印出来constructor属性了
//但如果将上面defineProperty给注释掉的话，foo.prototype.constructor就会打印出另一个结果：[Function: foo]

//prototype.constructor = 构造函数本身，也就是foo函数
```

```javascript
//我们来验证一下这句话：prototype.constructor = 构造函数本身，也就是foo函数
function foo(){

}
console.log(foo.prototype.constructor.name);//foo
//我们知道构造函数本身都是有名字的，通过在foo.prototype.constructor，也就是构造函数本身的基础上，打印了name，果然出来了foo这个名字
```

- 所以来个总结：

  - 在原本各种指向的基础上，在我们追溯到显式函数身上，也就是上面原型图中的Person的原型对象prototype之后，由Person原型对象身上的constructor又指回了foo这个构造函数本身。这样就形成了一个完美的循环

  ```javascript
  //我们知道这里形成了循环后，我们甚至能做出来一些骚操作
  function foo(){
  
  }
  console.log(foo.prototype.constructor.prototype.constructor.prototype.constructor.name) //foo
  //我们让他们两者之间不断的互相循环，最终又回到的构造函数本身，然后取出来了构造函数的名字
  ```

## 重写原型对象

> 这个互相引用是会回收的，因为JS的垃圾回收机制是标记清除，是从根节点开始看有没有引用的

![image-20230110233648426](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230110233648426.png)

**当我们使用重写原型方法之后，也就是在内存里又创建了一个对象，内存图如下(代码在下面的代码块中)：**

- foo函数不再指向它的原型对象了，而是指向新的对象了，刚指向的时候，这个新对象连constructor都没有
- 指向新对象之后，foo函数的原型对象就会被销毁掉了，因为我们js的垃圾回收机制是采用标记清除法(详细的内容往回翻)

![image-20230111013756415](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230111013756415.png)

**当我们填入内容之后：**

![image-20230111014318735](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230111014318735.png)



- 如果我们需要在原型上添加过多的属性，通常我们会重新整个原型对象：

```javascript
//没有重写之前
Person.prototype.name = "小余"
Person.prototype.age = 20
Person.prototype.learn = function(){
    console.log(this.name+"在学习");
}
```

```javascript
function Person(){

}
//重写之后：修改之后是不是简洁很多，去掉了很多重复的元素了
Person.prototype = {//这种对象形式的写法意味着直接在内存里创建了一个新的对象
    name:"小余",
    age:20,
    learn:function(){ 
        console.log(this.name+"在学习");
    }
}
```

- 前面我们说过, 每创建一个函数, 就会同时创建它的prototype对象, 这个对象也会自动获取constructor属性；
  - 而我们这里相当于给prototype重新赋值了一个对象, 那么这个新对象的constructor属性, 会指向Object构造函 数, 而不是Person构造函数了

![image-20230111014738997](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230111014738997.png)

```javascript
function Person(){

}
Person.prototype = {
    name:"小余",
    age:18,
	height:1.88
}
//注意多出来的这两个步骤
var f1 = new Person()

console.log(f1.name+"今年"+f1.age);//小余今年18，这里能够打印出来，但是最终指向的地方已经变成新的对象了(原因是因为我们重写了原型对象，通俗的说就是我们在prototype不再一个个等于慢慢写，而是直接使用对象的形式，省去了重复的Person.prototype.xxx = xx，这样的效果就刚刚上面说的一样，相当于在内存中创建了新的对象，构造函数Person的指向也就跟着发生了变化)，不再是Person的显式原型了
```

> 这时候问题就来了，那原来的就这么不要了吗？根据上面的图，我们如果想抛弃原来的那个显式原型的话，是不是还缺少了点什么？
>
> 缺少了constructor啦，就是构造函数的标志，所以我们只要给新对象加上constructor就完工了，原来那个显式原型就可有可无了

![image-20230111021233959](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230111021233959.png)

```javascript
function Person(){

}
Person.prototype = {
    //constructor:Person,//注意这里，这里是我们添加上来的，但是跟原版仍然有点区别，那就是原版的enumerable是为false的，而这样添加的enumerable为true，也就是可枚举的。所以真实开发中我们一般不这么添加，真实开发的添加方法我放在下面了，也就是我们刚刚所学的方式，能够解决我们通过目前这种方式添加时enumerable为true的问题
    name:"小余",
    age:18,
	height:1.88
}
var f1 = new Person()

//真实开发中我们通过Object.defineProperty方式添加constructor
Object.defineProperty(Person.prototype,"constructor",{
    enumerable:false,
    writable:true,
    configurable:true,
    value:Person
})

console.log(f1.name+"今年"+f1.age);
```

## 创建对象 – 构造函数和原型组合

- 我们在上一个构造函数的方式创建对象时，有一个弊端：会创建出重复的函数，比如running、eating这些函数
  - 那么有没有办法让所有的对象去共享这些函数呢?
  - 可以，将这些函数放到Person.prototype的对象上即可；

```javascript
//错误写法
function Person(name,age,sex,address){
    Person.prototype.name = name,
    Person.prototype.age = age,
    Person.prototype.sex = sex,
    Person.prototype.address = address
}
var p1 = new Person("小余",18,"男","福建")
console.log(p1.name);//小余
var p2 = new Person("小满",24,"男","乔家大院")
console.log(p1.name);//小满
//没错，这是错误写法，不能够这么写，因为我们在Person其实会创建一个空对象，然后绑定在this身上调用返回(内部实现，看不到的)，我们往p1，p2传入数据，第一时间肯定是先去p1跟p2各自的身上找，但很显然，我们在Person里面的代码，是直接放到他的显式原型上面了，而Person本身就什么都没有，所有当p1的数据放到显式原型上后，p2的数据紧随其后跟着放上去了，就会直接在显式原型中直接覆盖掉p1的数据。当我们使用p1.name的时候，本身找不到，紧接着去隐式原型中找，没找到，再去显式原型中找，这次找到了，但是找到的是被p2覆盖掉的数据，所有当我们p1.name拿出来的时候就会是p2的name数据
```

```javascript
//正确写法
function Person(name,age,sex,address){
    this.name = name,
    this.age = age,
    this.sex = sex,
    this.address = address
    // this.eating = function(){
    //     console.log(this.name+"今天吃烤地瓜了");
    // }
}
//由于函数如果放在Person里面，那每次都会在构造函数中创建出一个新的，但是里面的内容其实都是一样的，所以最好的方式就是放在原型中，需要的时候顺着原型链找过去
Person.prototype.eating = function(){
    console.log(this.name+"今天吃烤地瓜了");
}

Person.prototype.running = function(){
    console.log(this.name+"今天跑了五公里");
}
var p1 = new Person("小余",18,"男","福建")
var p2 = new Person("小满",24,"男","乔家大院")
console.log(p1.name);//小余	不会发生覆盖的问题了
```



### 内容补充(可枚举)

> 可枚举属性的补充
>
> - 我们设置可枚举为false，在node环境下是打印不出address地址的，但是在浏览器是会显示出来的，这是浏览器为了更加方便我们进行调试，所以把不可枚举的属性也展示出来了，在coderwhy老师的苹果电脑的谷歌浏览器中展示出来的不可枚举属性是会半透明显示的(提示开发者这是一个不可枚举的属性)，但是我在window电脑上的Edge浏览器中显示出来的不可枚举属性是没有半透明效果的，就正常显示

```javascript
var obj = {
    name:"小余",
    age:20
}

Object.defineProperty(obj,"address",{
    enumerable:false,//默认false 
    value:"福建省"
})

console.log(obj);
```

## JavaScript中的类和对象

- 当我们编写如下代码的时候，我们会如何来称呼这个Person呢？
  - 在JS中Person应该被称之为是一个构造函数；
  - 从很多面向对象语言过来的开发者，也习惯称之为类，因为类可以帮助我们创建出来对象p1、p2；
  - 如果从面向对象的编程范式角度来看，Person确实是可以称之为类的；

```javascript
function Person(){
    
}
var p1 = new Person()//通过了new调用，Person变为构造函数。生成新对象，由p1接收
var p2 = new Person()//但也可以称为 类，在ES6之后开始可以使用class去定义，但本质上还是通过原型、原型链 面向对象封装、继承，class它只是一个语法糖而已
```

> 知识点补充：语法糖是什么？
>
> - 语法糖是程序设计语言中一种语法上的简化，它可以使程序员在编写程序时使用更简洁、更易读的语法，同时编译器或解释器会将其转换为更底层的语法。语法糖并不会影响程序的功能或性能，只是提供了一种更方便的编码方式
>
> - 在 JavaScript 中，一个常见的语法糖是箭头函数。箭头函数的语法简洁，可以使代码更简洁易读。下面是一个使用传统函数定义方式和使用箭头函数定义方式实现同样功能的例子。
>
>   ```javascript
>   // 使用传统函数
>   let numbers = [1, 2, 3, 4, 5];
>   let double = numbers.map(function(number) {
>     return number * 2;
>   });
>   console.log(double); // [2, 4, 6, 8, 10]
>   
>   // 使用箭头函数
>   let numbers = [1, 2, 3, 4, 5];
>   let double = numbers.map(number => number * 2);
>   console.log(double); // [2, 4, 6, 8, 10]
>   
>   ```
>
>   - 可以看出在使用箭头函数时，省去了函数的名称、return关键字，并且在参数比较简单的情况下可以省去括号，使代码更简洁易读。

## 面向对象的特性 – 继承

- 面向对象有三大特性：封装、继承、多态
  - 封装：我们前面将属性和方法封装到一个类中，可以称之为封装的过程；
  - 继承：继承是面向对象中非常重要的，不仅仅可以减少重复代码的数量，也是多态前提（纯面向对象中）；
  - 多态：不同的对象在执行时表现出不同的形态；
- **这里核心讲述其中的继承**
- 那么继承是做什么呢？
  - 继承可以帮助我们将重复的代码和逻辑抽取到父类中，子类只需要直接继承过来使用即可。
  - 通俗的来说就是重复利用一些代码(对代码的复用)
  - 继承是多态的前提 
- 那么JavaScript当中如何实现继承呢？
  - 不着急，我们先来看一下JavaScript原型链的机制；
  - 再利用原型链的机制实现一下继承；

### JavaScript原型链

- 在真正实现继承之前，我们先来理解一个非常重要的概念：原型链。
  - 我们知道，从一个对象上获取属性，如果在当前对象中没有获取到就会去它的原型上面获取：

![image-20230111144111168](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230111144111168.png)

```javascript
var obj = {
    name:"小余",
    age:20
}
obj.__proto__ = {

}
obj.__proto__.__proto__ = {

}
obj.__proto__.__proto__.__proto__ = {
    address:"福建省"
}
//[[get]]操作
//1.在当前的对象里面查找属性
//2.如果没有找到，这个时候会去原型对象[[__proto__]]上查找
console.log(obj.address); 
```

### Object的原型

- 那么什么地方是原型链的尽头呢？比如第三个对象是否也是有原型__proto__属性呢？

  ```javascript
  console.log(obj.__proto__.__proto__.__proto__.__proto__);//[Object: null prototype] {}
  
  //到底是找到哪一层对象之后停止继续查找了呢？
  //如果每个原型后面还有原型，那不就无穷无尽吗？但显然是不可能的，原型链的尽头就是Object原型，位于我们__proto__的下一层，你本身自带一个__proto__，在prototype里面，这个__proto__打开是js替我们实现的原型，再下一层就是Object的原型了，也就是最后一层，但是如果你在自身的身上继续叠加__proto__的话，那原型链的尽头就会在这个基础上继续加，加深几层取决于你又套了几层__proto__
  ```

- 我们会发现它打印的是 [Object: null prototype] {}

  - 事实上这个原型就是我们最顶层的原型了
  - 从Object直接创建出来的对象的原型都是 [Object: null prototype] {}

- 那么我们可能会问题： [Object: null prototype] {} 原型有什么特殊吗？

  - 特殊一：该对象有原型属性，但是它的原型属性已经指向的是null，也就是已经是顶层原型了；
  - 特殊二：该对象上有很多默认的属性和方法；

#### Object顶层原型来自哪里

![image-20230111163400343](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230111163400343.png)

![image-20230111163938419](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230111163938419.png)

![image-20230111171832610](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230111171832610.png)

```javascript
//创建了一个对象
var obj = {}
//创建了一个对象，相当于obj对象字面量的语法糖
var obj2 = new Object()//obj2.__proto__ = Object.prototype

function Person(){

}

//new出构造函数这个操作发生的步骤：
//1.在内存中创建一个对象，var moni = {}
//2.this的赋值，this = moni
//3.将Person函数的显示原型prototype赋值给前面创建出来的对象的隐式原型，moni.__proto__=Person.prototype
//4.执行代码体
//5.返回这个对象
var p = new Person()

//但是当我们在使用语法糖new Object()的时候，赋值的情况如下：
//var moni = {}
//this = moni
//moni.__proto__ = Object.prototype
//obj2.__proto__ = Object.prototype
//那这时候就证明了一点，obj.__protot__ === Object.prototype
```

```javascript
var obj = {
    name:"xiaoyu",
    age:18
}
//obj.__proto__等价于Object.prototype	Object是所有类的父类 
console.log(obj.__proto__)//[Object: null prototype] {}
console.log(Object.prototype);//[Object: null prototype] {}
console.log(obj.__proto__ === Object.prototype);//true

//Object.prototype里面的东西是很多的，只是因为都是不可枚举的，所以在node环境下打印是看不见的，但是你可以打印在浏览器的控制台，这里是能看见的，就刚刚说的那样，浏览器为了方便开发者调试，所以会将不可枚举的属性也显示出来

//我们发现Object的原型对象身上也是有构造函数constructor的
console.log(Object.constructor);//[Function: Function]
//打印出Object的原型对象身上的所有属性，记得加上s 别打错啦
console.log(Object.getOwnPropertyDescriptors(Object.prototype))
//打印对象身上显式原型的隐式原型
console.log(Object.prototype.__proto__);//null，因为Object身上的原型已经是最后一层了，属于最顶层的原型，继续往下找只有null
```

**原型对象跟原型属性的关系(自我总结)**

> JavaScript 中所有对象都是通过构造函数创建的，并且对象都有一个隐式的属性，称为原型 (prototype)。每一个构造函数都有一个prototype属性，这个属性指向一个对象，这个对象也叫原型对象，继承了一些属性和方法。
>
> 每当一个新对象被创建，它的内部指针就会指向它的构造函数的原型对象。这意味着如果我们在原型对象上修改了一个属性或方法，那么所有继承了这个原型对象的对象都会受到影响。
>
> ```javascript
> // 定义一个构造函数Person
> function Person(name) {
>  this.name = name;
> }
> 
> // 添加一个原型属性 sayName
> Person.prototype.sayName = function() {
>  console.log(`My name is ${this.name}`);
> }
> 
> // 创建一个对象 person1
> let person1 = new Person("John");
> console.log(person1.name); // "John"
> console.log(person1.sayName); // "My name is John"
> 
> // 修改原型上的属性，所有继承原型的对象会受影响
> Person.prototype.sayName = function() {
>  console.log(`Hello, My name is ${this.name}`);
> }
> console.log(person1.sayName); // "Hello, My name is John"
> ```
>
> 可以看出构造函数的原型对象的属性是被所有继承了它的对象共享的，而对象的属性是被对象本身独享的。
>
> 原型属性可以用来实现继承

#### Person构造函数原型

```javascript
function Person(){

}
console.log(Person.prototype);//{},有时候看着是空的未必真的是空的
console.log(Object.getOwnPropertyDescriptors(Person.prototype))
// {
//     constructor: {
//       value: [Function: Person],
//       writable: true,
//       enumerable: false,
//       configurable: true
//     }
// }
```

#### 创建Object对象的内存图

![image-20230111173150297](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230111173150297.png)

### 原型链关系的内存图

![image-20230111173223272](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230111173223272.png)

### Object是所有类的父类

- 从我们上面的Object原型我们可以得出一个结论：原型链最顶层的原型对象就是Object的原型对象

![image-20230111175049084](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230111175049084.png)

```javascript
//Person原型指向顶层对象
function Person(name,age){
    this.name = name
    this.age = age
}

Person.prototype,running = function(){
    console.log(this.name+"running")
}
var p1 = new Person("why",18)
console.log(p1)
console.log(p1.valueOf())
console.log(p1.toString())
```

#### 为什么需要继承

> 通过这两个类，我们可以看到大量的重复代码，很明显是需要优化的
>
> 这就是为什么我们需要继承的原因

```javascript
//不使用继承封装两个类
// Student
function Student(name, age, sno) {
  this.name = name
  this.age = age
  this.sno = sno
}

Student.prototype.running = function() {
  console.log(this.name + " running~")
}

Student.prototype.eating = function() {
  console.log(this.name + " eating~")
}

Student.prototype.studying = function() {
  console.log(this.name + " studying")
}

// Teacher
function Teacher(name, age, title) {
  this.name = name
  this.age = age
  this.title = title
}

Teacher.prototype.running = function() {
  console.log(this.name + " running~")
}

Teacher.prototype.eating = function() {
  console.log(this.name + " eating~")
}

Teacher.prototype.teaching = function() {
  console.log(this.name + " teaching")
}
```

### 通过原型链实现继承

> 继承就是将公共的代码，公共的逻辑抽取到一个父类里面
>
> 父类是公用的，子类用来处理独有的特殊逻辑
>
> | 父类           | 子类           |
> | -------------- | -------------- |
> | 公共属性和方法 | 特有属性和方法 |

- 如果我们现在需要实现继承，那么就可以利用原型链来实现了：
  - 目前stu的原型是p对象，而p对象的原型是Person默认的原型，里面包含running等函数；
  - 注意：步骤4和步骤5不可以调整顺序，否则会有问题

```javascript
//未实现继承的效果，打印出来效果为undefined
//父类，公共属性和方法
function Person(){
    this.name = "小余"
}

Person.prototype.eating = function(){
    console.log(this.name +"eating~");
}

//子类：特有属性和方法
function Student(){
    this.sno = 111
}

Student.prototype.studying = function(){
    console.log(this.name + "studying");
}

var stu= new Student()
console.log(stu.name);//undefined
console.log(stu.eating);//undefined
//很明显，顺着原型链也是找不到name跟eating这两个属性的，因为stu是接收Student产生的新对象，只会在构造函数Student上面追溯，是没办法追到Person函数身上的
```

**实现继承**

> 在这里最重要的明显是第四步骤，这里我们new了Person函数(父类)，产生新的对象赋值给了p，然后将p赋值给了Student(子类)
>
> 这里首先我们new了Person，使Person变为了构造函数，然后将new Person产生的新对象替换掉了Student的原型，我们知道通常指向顺序是：隐式原型=>显式原型=>constructor(构造函数)=>本身函数。那这里则是将显式原型指向的constructor替换掉了，变成了Student(子类)显式原型=>p对象=>Person原型对象(由P对象的隐式原型指向)

![image-20230112034256733](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230112034256733.png)

```javascript
//实现继承的效果，关键在第四五步骤，顺序不能调整
//定义父类构造函数
function Person(){
    this.name = "小余"
    this.friends = []
}
//往父类原型上添加内容
Person.prototype.eating = function(){
    console.log(this.name +"eating~");
}

//定义子类构造函数
function Student(){
    this.sno = 111
}
//4.创建父类对象，并且作为子类的原型对象(关键)
var p = new Person()
Student.prototype = p//这一步赋值的操作之后，Student原来的原型对象就不再被指向，会在下一轮中被垃圾回收掉。我个人认为这个p更像是链接子类跟父类的中转站，但是它是会替代掉子类原来的原型的

//5.在子类原型上添加内容 这一步不能够跟第四步换是很好理解的，因为第四步要替换掉我们的原型，如果第五步先的话，会绑定到要被替换掉的原型身上，然后跟着一起被替换掉。所以不能够这么做
Student.prototype.studying = function(){
    console.log(this.name + "studying");
}

var stu= new Student()
console.log(stu.name);
//console.log(stu.eating());这里不需要使用console.log()，因为stu.eating()自身已经会调用一次了
stu.eating()
```

#### 原型链继承的弊端

- 但是目前有一个很大的弊端：某些属性其实是保存在p对象上的；
  - 第一，我们通过直接打印对象是看不到这个属性的；
  - 第二，这个属性会被多个对象共享，如果这个对象是一个引用类型，那么就会造成问题；
  - 第三，不能给Person传递参数，因为这个对象是一次性创建的（没办法定制化）；

```javascript
function Person(){
    this.name = "小余"
    this.friends = []
}
Person.prototype.eating = function(){
    console.log(this.name +"eating~");
}
function Student(){
    this.sno = 111
}
var p = new Person()
Student.prototype = p

Student.prototype.studying = function(){
    console.log(this.name + "studying");
}

var stu= new Student()
console.log(stu.name);
stu.eating()

//原型链弊端演示1
console.log(stu);//Person { sno: 111 }，类型怎么变成父类了，这里应该是Student，而不是Person，然后内容也不止一个sno

//原型链弊端演示2
//stu1跟stu2之间应该是相互独立的，因为stu1多了一个名叫小满的朋友，不代表stu2也能够获得同样的朋友
//2.创建出来两个stu对象
var stu1 = new Student()
var stu2 = new Student()
//那问题就来了，我们接下来要对stu1进行操作，但是同时影响到了stu2。因为我们friends是一个引用对象:数组，会造成问题。通常stu1.friends这种操作应该将内容放到自己的对象里面，也就是之前说的那个var moni = {}，是影响不到原型上的(直接修改对象上的属性，是给本对象添加新属性的)，但是当我们使用引用对象的时候，我们知道引用对象其实是获取引用，修改引用里面的值
//直接修改的例子：直接修改对象上的属性，是给本对象添加新属性的
stu.name = "超级满"
console.log(stu2.name)//小余，对stu1的修改影响不到stu2的
//引用的例子
stu1.friend.push("小满")
console.log(stu1.friends);//[ '小满' ]
console.log(stu2.friends);//[ '小满' ]

//原型链弊端演示3
//在前面实现类的过程中都没有传递参数
var stu3 = new Student("超级小 余",112)
```

```javascript
//对friends写法的区别
stu1.friends = ["小余"]//这种写法是直接往friends的数组里面添加内容，数据是在stu1自己的对象里面的
stu1.friends.push("小余")//stu1.friends是[[get]]操作，会顺着原型链一层层往上找，找到原型上面的friends，然后往里面塞入了一个"小余"
```

### 借用构造函数继承

- 为了解决原型链继承中存在的问题，开发人员提供了一种新的技术: constructor stealing(有很多名称: 借用构造函 数或者称之为经典继承或者称之为伪造对象)：
  - steal是偷窃、剽窃的意思，但是这里可以翻译成借用；
- 借用继承的做法非常简单：在子类型构造函数的内部调用父类型构造函数.
  - 因为函数可以在任意的时刻被调用；
  - 因此通过apply()和call()方法也可以在新创建的对象上执行构造函数；

![image-20230112134112365](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230112134112365.png)

```javascript
//解决无法在子类传递参数的问题
function Person(name,age,sex,address){
  this.name = name 
  this.age = age
  this.sex = sex
  this.address = address
  this.friends = []
}

Person.prototype.eating = function(){
  console.log(this.name +"在吃早餐");
}


function Student(name,age,sex,address){
  Person.call(this,name,age,sex,address)//是将name,age,sex,address这四个属性加到this里面
}

var p = new Person()
Student.prototype = p

Student.prototype.learn = function(){
  console.log(this.name+"在学习");
}

var stu = new Student("小余","男",20,"福建")
//成功传递参数，但是Person类型还有问题，后续解决
console.log(stu);//Person { name: '小余', age: '男', sex: 20, address: '福建' }
```

```javascript
//在上面的基础上，两者不会相互影响
var stu = new Student("小余", "男", 20, "福建");
var stu1 = new Student("小满", "男", 24, "北京");

console.log(stu);//Person { name: '小余', age: '男', sex: 20, address: '福建' }
console.log(stu1);//Person { name: '小满', age: '男', sex: 24, address: '北京' }
///////////接下来验证push了
stu.friend.push("园长")
console.log(stu.friends,"这是stu");//[ '园长' ] 这是stu
console.log(stu1.friends,"这是stu1");//[] 这是stu1
```

> 以下是上面的整合

```javascript
//解决原型链继承弊端第三点，不能给函数传递参数的问题
// 父类: 公共属性和方法
function Person(name, age, friends) {//公共的数据传递到父类中，然后子类会内部调用父类
  // this = stu
  this.name = name
  this.age = age
  this.friends = friends
}

Person.prototype.eating = function() {
  console.log(this.name + " eating~")
}

// 子类: 特有属性和方法
function Student(name, age, friends, sno) {
  Person.call(this, name, age, friends)//这里的this是new Student时创建出来的对象，通过call调用这三个参数，就是一个普通的函数调用，就会去父类中调用函数了(子类型构造函数的内部调用父类型构造函数)
  // this.name = name 不能够这么传递，这样就把处理逻辑放到子类里面了，公共的应该抽到父类中去
  // this.age = age
  // this.friends = friends
  this.sno = 111
}

var p = new Person()
Student.prototype = p

Student.prototype.studying = function() {
  console.log(this.name + " studying~")
}


// name/sno
var stu = new Student("why", 18, ["kobe"], 111)

// console.log(stu.name)
// stu.eating()

// stu.studying()


// 原型链实现继承已经解决的弊端
// 1.第一个弊端: 打印stu对象, 继承的属性是看不到的
console.log(stu)

// 2.第二个弊端: 创建出来两个stu的对象
var stu1 = new Student("why", 18, ["lilei"], 111)
var stu2 = new Student("kobe", 30, ["james"], 112)

// // 直接修改对象上的属性, 是给本对象添加了一个新属性
// stu1.name = "kobe"
// console.log(stu2.name)

// // 获取引用, 修改引用中的值, 会相互影响
stu1.friends.push("lucy")

console.log(stu1.friends)
console.log(stu2.friends)

// // 3.第三个弊端: 在前面实现类的过程中都没有传递参数
// var stu3 = new Student("lilei", 112)



// 强调: 借用构造函数也是有弊端:
// 1.第一个弊端: Person函数至少被调用了两次
// 2.第二个弊端: stu的原型对象上会多出一些属性, 但是这些属性是没有存在的必要(多出的属性来自p对象的属性，因为我们原来的原型被p对象替换了，详细的看最上面那张图，p里面的age，name，friends都是要么跟本身的对象重复要么就没有必要的)
```

#### 组合借用继承的问题

- 组合继承是JavaScript最常用的继承模式之一：

  - 如果你理解到这里, 点到为止, 那么组合来实现继承只能说问题不大；
  - 但是它依然不是很完美，但是基本已经没有问题了；(不成问题的问题, 基本一词基本可用, 但基本不用)

- 组合继承存在什么问题呢?

  - 组合继承最大的问题就是无论在什么情况下，都会调用两次父类构造函数。

    一次在创建子类原型的时候；

    另一次在子类构造函数内部(也就是每次创建子类实例的时候)；

  - 另外，如果你仔细按照我的流程走了上面的每一个步骤，你会发现：所有的子类实例事实上会拥有两份父类的 属性

    一份在当前的实例自己里面(也就是person本身的)，另一份在子类对应的原型对象中(也就是 person.__proto__里面)；

    当然，这两份属性我们无需担心访问出现问题，因为默认一定是访问实例本身这一部分的；

### 原型式继承函数

- 原型式继承的渊源
  - 这种模式要从道格拉斯·克罗克福德（Douglas Crockford，著名的前端大师，JSON的创立者）在2006年写的 一篇文章说起: Prototypal Inheritance in JavaScript(在JS中使用原型式继承)
  - 在这篇文章中，它介绍了一种继承方法，而且这种继承方法不是通过构造函数来实现的
  - 为了理解这种方式，我们先再次回顾一下JavaScript想实现继承的目的：重复利用另外一个对象的属性和方法
- 最终的目的：**student对象的原型指向了person对象**；

```javascript
//Student原型对象指向Person的原型对象可不可行 => 回顾一下JavaScript想实现继承的目的：重复利用另外一个对象的属性和方法
//替换前
var p = new Person()
Student.prototype = p
//替换后
Student.prototype = Person.prototype//这样甚至连Person都不需要变成构造函数，内容直接传到Person的原型上面了。这样就影响不到Person自身的对象了，也就不会在Person自身对象多出一些不需要的属性了

//但其实这是不对的(从面向对象的角度来看)，因为此时Person跟Student指向同一个原型(Person原型)了。此时如果再多一个Teacher来指向Person原型的话，我们对Student的修改影响了Person的原型，那Person的原型也会影响到Teacher
```

```javascript
var obj = {
    name:"小余",
    age:20
}

//这一步要实现的是你给我传入的对象是要成为我新创建出来的对象的原型的
function createObject(o){
    var newObj = {}
    //跟获取原型getPrototypeOf对应的当然有设置对象原型的setPrototypeOf了
    Object.setPrototypeOf(newObj,o)
    return newObj//会通过上面创建newObj对象，然后将我们传入的对象作为newObj的原型，最后做一个返回
}

var info = createObject(obj)
console.log(info.__proto__);//实现了info原型指向obj对象
//console.log(info.prototype);我不知道你会不会这么做，但这是不行的，prototype是指向构造函数的原型对象的
```

> 同时我们在这里补充上面setPropertypeOf的详细信息
>
> `Object.setPrototypeOf()` 是 JavaScript 中的一个内置函数，它用于设置一个对象的原型（即内部 [[Prototype]] 属性）。
>
> ```javascript
> Object.setPrototypeOf(object, prototype);
> ```
>
> 参数:
>
> - `object`: 需要设置原型的对象。
> - `prototype`: 新的原型对象。
>
> ```javascript
> let person = {
>   name: 'XiaoYu',
>   age: 20
> };
> 
> let employee = {
>   job: 'Engineer'
> };
> 
> Object.setPrototypeOf(person, employee);
> console.log(person.job); // 'Engineer'
> ```
>
> 在上面的示例中，我们将 `employee` 对象设置为 `person` 对象的原型，所以 `person` 对象现在可以访问 `employee` 对象的属性 `job` 了。
>
> 需要注意的是，`Object.setPrototypeOf()` 只能在 ES6 以上的版本中使用。或者你使用`babel巴别塔`转换成ES5的语法

> 但是在 道格拉斯·克罗克福德 这位大佬那个年代，setPropertypeOf内置函数还没有出来，所以我们来看看他当时是怎么写的吧！

```javascript
var obj = {
    name:"小余",
    age:20
}

//这一步要实现的是你给我传入的对象是要成为我新创建出来的对象的原型的
function createObject(o){
    function Fn(){}
    //然后将Fn的函数原型设置为传进来的函数原型
    Fn.prototype = o
    //然后只有构造函数才有prototype，所以我们需要让Fn变成构造函数
    var newObj = new Fn()//同时这里还有一步深意，那就是o已经变成Fn的构造原型了
    //最后返回结果
    return newObj
}

var info = createObject(obj)
console.log(info.__proto__);//实现了info原型指向obj对象
```

```javascript
//一个错误写法，开发中是最好不要给__proto__原型赋值
function createObject(o){
    var Fn = {}
	Fn.__proto__ = o
    return Fn
}
```

> 回顾知识点：
>
> `__proto__`跟`prototype`的区别
>
> 1. `__proto__` 是 JavaScript 中的一个内部属性，它指向对象的原型。而 `prototype` 是一个对象的属性，用于定义构造函数的实例的原型。
> 2. 简单来说，`__proto__` 指向一个对象的原型对象，而 `prototype` 指向构造函数的原型对象。
>
> ```javascript
> function Person(name) {
>   this.name = name;
> }
> 
> let person = new Person('XiaoYu');
> 
> 
> console.log(person.__proto__ === Person.prototype); // true，这是对象实例的原型
> console.log(Person.__proto__ === Function.prototype); // true 这是函数本身的原型
> //在上面的示例中，person.__proto__ 指向 Person.prototype，而 Person.__proto__ 指向Function.prototype。
> ```
>
> 函数的原型跟对象的原型有什么区别
>
> 函数的原型（`Function.prototype`）用于定义函数的默认属性和方法，比如 `Function.prototype.apply()`、`Function.prototype.bind()` 等。而对象的原型（`__proto__`）用于定义对象的默认属性和方法。
>
> 简单来说，函数的原型是用来定义函数本身的属性和方法，而对象的原型是用来定义对象实例的属性和方法。
>
> 另外需要注意的是，每个函数都是一个对象，所以函数也有自己的原型，但是普通对象没有prototype

### 寄生式继承函数

- 寄生式(Parasitic)继承
  - 寄生式(Parasitic)继承是与原型式继承紧密相关的一种思想, 并且同样由道格拉斯·克罗克福德(Douglas  Crockford)提出和推广的；
  - 寄生式继承的思路是结合原型类继承和工厂模式的一种方式；
  - 即创建一个封装继承过程的函数, 该函数在内部以某种方式来增强对象，最后再将这个对象返回；

```javascript
//寄生式继承是一种创建对象的模式，它通过创建一个函数并在其上面添加属性和方法来实现继承。这种方式的优点是可以在不改变原型链的情况下进行继承，并且可以在创建新对象时不需要使用关键字 new。

//原型函数
var personObj = {
    running:function(){
        console.log("小余正在跑步");
    }
}
//工厂函数
function createStudent(name){
    var abc = Object.create(personObj)
    abc.name = name,
    abc.learn = function(){
        console.log("小余在学习");
    }
}
//寄生式继承 = 工厂函数+原型函数
var stu1 = createStudent("小余")
var stu2 = createStudent("小满")
//一般情况下我们也不会使用，因为是有缺陷的
```

> Object.create()知识点补充(很重要，我们最终方案就依靠这个了)
>
> Object.create() 方法用于创建一个新对象，使用现有的对象来提供新创建的对象的__proto__。
>
> ```javascript
> //语法
> Object.create(proto[, propertiesObject])
> ```
>
> 参数:
>
> - proto: 创建的对象的原型对象。
> - propertiesObject: (可选) 新对象的属性。
>
> ```javascript
> let animal = {
>   eat: function() {
>     console.log("eating");
>   }
> }
> 
> let dog = Object.create(animal);
> dog.bark = function() {
>   console.log("barking");
> }
> 
> dog.eat(); // "eating"
> dog.bark(); // "barking"
> 
> ```
>
> 上面的例子中我们创建了一个 animal 对象，然后使用它来创建一个 dog 对象。 dog 对象继承了 animal 对象的 eat 属性。

## 寄生组合式继承

- 现在我们来回顾一下之前提出的比较理想的组合继承

  - 组合继承是比较理想的继承方式, 但是存在两个问题:

    问题一: 构造函数会被调用两次: 一次在创建子类型原型对象的时候, 一次在创建子类型实例的时候.

    问题二: 父类型中的属性会有两份: 一份在原型对象中, 一份在子类型实例中

- 事实上, 我们现在可以利用寄生式继承将这两个问题给解决掉

  - 你需要先明确一点: 当我们在子类型的构造函数中调用父类型.call(this, 参数)这个函数的时候, 就会将父类型中 的属性和方法复制一份到了子类型中. 所以父类型本身里面的内容, 我们不再需要.
  - 这个时候, 我们还需要获取到一份父类型的原型对象中的属性和方法

- **能不能直接让子类型的原型对象 = 父类型的原型对象呢?**

  - 不要这么做, 因为这么做意味着以后修改了子类型原型对象的某个引用类型的时候, 父类型原生对象的引用类型 也会被修改.
  - 我们使用前面的寄生式思想就可以了

![image-20230114135452413](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230114135452413.png)

```javascript
//最终方案
function Person(name,age,friends){
    this.name = name
    this.age = age
    this.friends = friends
}

Person.prototype.learn = function(){
    console.log("小余在重新学习JavaScript");
}
Person.prototype.runAway = function(){
    console.log("小余跑路了");
}

function Student(name,age,friends,sno,score){
    Person.call(this,name,age,friends)//借用父级的这些属性
    this.sno = sno
    this.score = score
}

Student.prototype.eating = function(){
    console.log("小余今天吃了一只小满果实，Vue经验拉满达到精通");
}

Student.prototype = Object.create(Person.prototype)

var stu = new Student("小余",20,["小满","cool","学姐","菜菜哥"],111,100)
console.log(stu);
// Person {
//     name: '小余',
//     age: 20,
//     friends: [ '小满', 'cool', '学姐', '菜菜哥' ],
//     sno: 111,
//     score: 100
// }
//通过打印结果我们发现这个对象的名称怎么还是Person(父类)，而不是Student(子类)
//这里是名字拼接对象，对象里就是我们的内容，名字是Person，通过找到我们stu.constructor进行拼接了
console.log(stu.constructor.name);//Person，证明了确实如此，是constructor进行拼接的
```

> 解决方案：
>
> 根据上面那张图，我们就知道是因为一路上都没找到constructor，所以顺着原型链找到了Person函数原型里的构造函数constructor，对于这点我们也可以进行验证
>
> 我们的解决方法就是，既然这一路都找不到constructor，那我们就在这路中的Student的原型这里设置一个constructor(因为我们的constructor只存在显式原型中，显式原型继续往下找就是constructor)，让stu顺着原型先找到这个停下来。这就需要使用到我们的`defineproperty`了
>
> ```javascript
> console.log(Person.prototype.constructor.name);//Person
> ```

```javascript
 //只需要添加如下代码，加在Student.prototype = Object.create(Person.prototype)后面
Object.defineProperty(Student.prototype,"constructor",{
    configurable:true,
    writable:true,
    enumerable:false,
    value:Student,

})
```

> 完整代码如下

```javascript
function Person(name,age,friends){
    this.name = name
    this.age = age
    this.friends = friends
}

Person.prototype.learn = function(){
    console.log("小余在重新学习JavaScript");
}
Person.prototype.runAway = function(){
    console.log("小余跑路了");
}

function Student(name,age,friends,sno,score){
    Person.call(this,name,age,friends)//借用父级的这些属性
    this.sno = sno
    this.score = score
}

Student.prototype.eating = function(){
    console.log("小余今天吃了一只小满果实，Vue经验拉满达到精通");
}

Student.prototype = Object.create(Person.prototype)
Object.defineProperty(Student.prototype,"constructor",{
    configurable:true,
    writable:true,
    enumerable:false,
    value:Student,//确定我们类型的名字

})

var stu = new Student("小余",20,["小满","cool","学姐","菜菜哥"],111,100)
console.log(stu);//改变
// Student {
//     name: '小余',
//     age: 20,
//     friends: [ '小满', 'cool', '学姐', '菜菜哥' ],
//     sno: 111,
//     score: 100
// }
console.log(Person.prototype.constructor.name);//Person，因为我们不是改动这个，所以这个肯定还是不变的，我们是在Student的原型加入constructor的
```

> 但是这样依然不是最完善的做法，如果我们换成Teacher继承自Person呢？那不就还得再给Teacher写一遍，然后value写上Teacher。或者说Student不要了，换成Teacher，那不就需要修改很多地方。这就证明还有优化的地方，我们可以写一个工具函数进行补充

```javascript
//工具函数，将这个核心功能封装起来
function inheritPrototype(SubType,SuperType){//SubType：子类型、SuperType：父类型
    SubType.prototype = Object.create(SuperType.prototype)
    Object.defineProperty(SubType.prototype,"constructor",{
        configurable:true,
        writable:true,
        enumerable:false,
        value:SubType,
    })
}
//需要的时候直接使用
inheritPrototype(Student,Person)//Student继承自Person
```

```javascript
//完整代码
function inheritPrototype(SubType,SuperType){//SubType：子类型、SuperType：父类型
    SubType.prototype = Object.create(SuperType.prototype)//使用Object.create的写法有可能太新，社区目前旧的写法放在底下代码块中
    Object.defineProperty(SubType.prototype,"constructor",{
        configurable:true,
        writable:true,
        enumerable:false,
        value:SubType,
    })
}


function Person(name,age,friends){
    this.name = name
    this.age = age
    this.friends = friends
}

Person.prototype.learn = function(){
    console.log("小余在重新学习JavaScript");
}
Person.prototype.runAway = function(){
    console.log("小余跑路了");
}

function Student(name,age,friends,sno,score){
    Person.call(this,name,age,friends)//借用父级的这些属性
    this.sno = sno
    this.score = score
}

inheritPrototype(Student,Person)//Student继承自Person

Student.prototype.eating = function(){
    console.log("小余今天吃了一只小满果实，Vue经验拉满达到精通");
}


var stu = new Student("小余",20,["小满","cool","学姐","菜菜哥"],111,100)
console.log(stu);//正常显示
console.log(Person.prototype.constructor.name);//Person
```

```javascript
//社区的旧写法(目前)，其实就是自己封装Object.create进行使用
//通过这种方式创建的对象是一个浅拷贝，如果o对象里面有引用类型的数据，新对象和o对象指向的是同一个内存地址，对新对象的修改会影响到o对象。这种方式创建的对象也叫原型继承，是一种实现继承的方式
function createObject(o){
    function Fn(){}//创建一个空函数
    Fn.prototype = o//然后将其 prototype 属性设置为传入的对象 o，这样，Fn 函数的所有实例都会继承 o 对象的属性和方法
    return new Fn()//最后，我们使用 new 运算符创建一个新的 Fn 实例并返回这个实例，这样我们就得到了一个继承了 o 对象属性和方法的新对象
}


function inheritPrototype(SubType,SuperType){//SubType：子类型、SuperType：父类型
    SubType.prototype = createObject(SuperType.prototype)
    Object.defineProperty(SubType.prototype,"constructor",{
        configurable:true,
        writable:true,
        enumerable:false,
        value:SubType,
    })
}
```

## 对象的方法补充

### 判断方法

- hasOwnProperty

  - 对象是否有某一个属于自己的属性（不是在原型上的属性）

- in/for in 操作符

  - 判断某个属性是否在某个对象或者对象的原型上
  - in运算符只能检测属性是否存在，如果你需要检测属性的值是否为undefined,可以使用Object.prototype.hasOwnProperty()
  - for-in 循环会依次迭代对象的所有可枚举属性

- instanceof

  - 用于检测构造函数的pototype，是否出现在某个实例对象的原型链上

  - 换个说法就是：`instanceof`方法是用来判断一个对象是否是某个构造函数的实例。

    ```javascript
    object instanceof constructor
    //其中，object是要检测的对象，constructor是构造函数。如果object是constructor的实例，则返回true，否则返回false
    ```

- n isPrototypeOf

  - 用于检测某个对象，是否出现在某个实例对象的原型链上

```javascript
var obj = {
    name:"小余",
    age:20
}

var info = Object.create(obj,{//对象上create属性第二个参数是新增定义对象里面属性的
    address:{
        value:"福建",
        enumerable:true
    }
})
//只有address是真正属于info的，age跟name是在info原型身上的
console.log(info.name);
console.log(info.age);
console.log(info.address);

//判断哪些属性是真正属于info的，hasOwnProperty
console.log(info.hasOwnProperty("address"));//true
console.log(info.hasOwnProperty("name"));//false

//in操作符：不管在当前对象还是对象的原型中，返回的都是true
console.log("address" in info);//true
console.log("name" in info);//true
//所以in操作符的进阶就配合for循环使用
for(var key in info){
    console.log(key);
}
// address
// name
// age

//instanceof：检测构造函数的pototype，是否出现在某个实例对象的原型链上

```

```javascript
//instanceof：用于检测构造函数的pototype，是否出现在某个实例对象的原型链上
function createObject(o) {
  function Fn() {}
  Fn.prototype = o
  return new Fn()
}

function inheritPrototype(SubType, SuperType) {
  SubType.prototype = createObject(SuperType.prototype)
  Object.defineProperty(SubType.prototype, "constructor", {
    enumerable: false,
    configurable: true,
    writable: true,
    value: SubType
  })
}


function Person() {

}

function Student() {

}

inheritPrototype(Student, Person)

console.log(Person.prototype.__proto__)//最顶层的原型：[Object: null prototype] {}

var stu = new Student()
console.log(stu instanceof Student) // true，学生是学生
console.log(stu instanceof Person) // true，学生是人
console.log(stu instanceof Object) // true，Object是最顶层的原型，Person在不额外设置的情况下最终指向Object
```

```javascript
//isPrototypeOf：用于检测某个对象，是否出现在某个实例对象的原型链上
var obj = {
    name:"小余",
    age:18
}

var info = Object.create(obj)

console.log(obj.isPrototypeOf(info));//obj是不是出现在info的原型链上面，结果是true
```

## 原型继承关系

> 这里当我学完一遍后，我后续会回来复习，然后将思路完整总结的

![image-20230114172103104](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230114172103104.png)

![image-20230115003809858](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20230115003809858.png)

```javascript
//最根上面的原型对象
var obj = {
    name:"小余"
}
//很明显通过以下两种方式证明了这点
console.log(obj.__proto__)//[Object: null prototype] {}
console.log(obj.__proto__ === Object.prototype)//true
```

```javascript
//对象里面是有一个__proto__：隐式原型

//Foo是一个函数，那么它会有一个显式原型对象：Foo.prototype
//Foo是一个对象，那么它会有一个隐式原型对象：Foo.__proto__
//显式原型对象必然是跟隐式原型对象不相等的

//prototype来自哪里？
//=>来自你一旦创建一个函数，那函数本身会有js引擎帮你创建出来一个新的对象的。而且Foo.prototype的身上还有constructor，这个又指向回Foo函数了
//__proto__来自哪里?
//new Funtion() 	Foo.__proto__ = Funtion.prototype
//Funtion.prototype = {constructor:Funtion}

//var Foo = new Function()
function Foo(){
    
}
//Foo既是函数，也是一个对象，那是由谁创建出来的呢？由new Funtion
function Funtion(){}//这就像是一个类一样
var Foo = new Funtion()//然后就这样子创建出来了

console.log(Foo.prototype === Foo.__proto__);//false
console.log(Foo.prototype.constructor);//[Function: Foo]
console.log(Foo.__proto__.constructor);//[Function: Funtion]
```

