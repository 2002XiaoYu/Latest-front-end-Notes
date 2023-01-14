# JS中函数的this指向

> ### 作者：小余同学
>
> 这是我的 [GitHub](https://github.com/2002XiaoYu) 地址，所有笔记都会进行实时更新，所有不妨给作者这个项目点个star，方便后续再找到继续获取最新的笔记，你的star是作者更新的动力，祝你在学习的路上一帆风顺

[TOC]

### 为什么需要this？

- 在常见的编程语言中，几乎都有this这个关键字(Objective-C中使用的是self)，但是JavaScript中的this和常见的面向对象语言中的this不太一样：
  - 常见面向对象的编程中，比如Java、C++、Swift、Dart等等一系列语言中，t**his通常只会出现在类的方法中**
  - 也就是你需要一个类，类中的方法(特别是实例方法)中，this代表的是当前调用对象
  - 但是JavaScript中的this更加灵活，无论是它出现的位置还是它代表的含义
- 我们来编写一个obj的对象，看有this跟没有this的区别

### this的作用

> 使用this

```js
var obj = {
    name:"小余",
    eacting:function(){
        console.log(this.name + "在吃东西");
    },
    runing:function(){
        console.log(this.name + "在跑步");
    },
    studying:function(){
        console.log(this.name + "在学习");
    }
}

obj.eacting()
obj.runing()
obj.studying()
//小余在吃东西
//小余在跑步
//小余在学习
```

### 不使用this的弊端

> 不使用this，一样是可以打印出来的，从某些角度来说，开发中没有this，很多问题我们也是有解决方案的
>
> 但是如果使用this的话，我们就不需要修改对象内部的代码了

```js
var obj = {
    name:"小余",
    eacting:function(){
        console.log(obj.name + "在吃东西");
    },
    runing:function(){
        console.log(obj.name + "在跑步");
    },
    studying:function(){
        console.log(obj.name + "在学习");
    }
}

obj.eacting()
obj.runing()
obj.studying()
//小余在吃东西
//小余在跑步
//小余在学习
```

> 但是这种解决方案是有弊端的，当我们要使用多个对象的时候，对象里面使用到的名字还得一个个替换掉，例如obj.name就得替换成xiaoman.name
>
> 所以没有this会让我们编写代码非常不方便

```js
var obj = {
    name:"小余",
    eacting:function(){
        console.log(obj.name + "在吃东西");
    },
    runing:function(){
        console.log(obj.name + "在跑步");
    },
    studying:function(){
        console.log(obj.name + "在学习");
    }
}

var xiaoman = {
    name:"小满",
    eacting:function(){
        console.log(xiaoman.name + "在吃东西");
    },
    runing:function(){
        console.log(xiaoman.name + "在跑步");
    },
    studying:function(){
        console.log(xiaoman.name + "在学习");
    }
}

obj.eacting()
obj.runing()
obj.studying()
```

### this在全局作用域指向什么

> 全局中的this是非常特殊的，因为大多数情况下this都是出现在函数中的
>
> - this在全局作用域下
>
>   - 浏览器：window
>
>   - Node环境：{}空对象
>
> - 但是，开发中很少直接在全局作用域下去使用this，通常都是在**函数中使用**
>
>   - 所有的函数在被调用时，都会创建一个执行上下文
>   - 这个上下文中记录着函数的调用栈、AO对象等
>   - this也是其中一条记录
>     - this是动态绑定的。动态绑定就是等到我们函数即将执行的时候才会确定绑定上去，而不是解析的时候确定的
>     - 有着比较多的绑定规则，在不同规则下的绑定情况都不大一样

```js
//打印出来的是两个一模一样的window
console.log(this);
console.log(window);
```

**node环境下为什么是空对象**

> 文件在要被node执行的时候，我们的文件会被node当作一个模块module -> 加载 ->编译 -> 将所有代码放在一个函数里面 -> 执行这个函数，执行了一个apply
>
> - function foo(){xxx}，执行的时候我们不使用foo()，而是foo.apply("小余")，则"小余"会替代掉xxx的内容

### 同一个函数的this的不同

> - this指向什么，跟函数所处的位置是没有关系的
> - 跟函数被调用的方式有关系
>
> ----
>
> 我们先来看一个让人困惑的问题：
>
> - 定义一个函数，我们采用三种不同的方式对他进行调用，它产生了三种不同的结果
>
> 这个的案例可以给我们什么样的启示：
>
> 1. 函数在调用时，JavaScript会默认给this绑定一个值
> 2. this的绑定和定义的位置(编写的位置)没有关系
> 3. this的绑定和调用方式以及调用的位置有关系
> 4. this是在运行时被绑定的

```js
function foo(){
    console.log(this);
}

//1.直接调用这个函数
foo()

//2.创建一个对象，对象中的函数指向foo
var obj = {
    name:"小余",
    foo:foo
}

obj.foo()

//3.apply调用
foo.apply("XiaoYu")
```

结果如下：![image-20221220134952932](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20221220134952932.png)

## this到底是怎么样的绑定规则

> - 绑定一：默认绑定
> - 绑定二：隐式绑定
> - 绑定三：显示绑定
> - 绑定四：new绑定

### 规则1：默认绑定

- 什么情况下使用默认绑定呢？独立函数调用
  - 独立的函数调用我们可以理解成函数没有被绑定到某个对象上进行调用
  - 这种情况下，this指向的就是window 

```js
//案例1
//函数在被调用的时候，没有被绑定在任何的对象上面，也没有使用apply等方式调用
function foo(){
    console.log(this);
}

foo()
```

> 另外的一种情况，这种情况下，函数也是独立调用的。不是XXX.foo()之类的调用也是独立调用

```js
//案例2
function foo1(){
    console.log("foo1",this);
}

function foo2(){
    console.log("foo2",this);
    foo1()
}

function foo3(){
    console.log("foo3",this);
    foo2()
}

foo3()
```

结果如下：

![image-20221220212753721](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20221220212753721.png)

> 要注意，案例3是定义的时候有绑定到对象上面，但是当他在执行的时候，我们执行的是fn，根据之前学的内存里的执行过程，我们知道fn此时执行的就是obj里面的function本身，那fn是独立调用的其实就证明了函数也是独立调用的，那答案就应该指向window
>
> **this指向什么，跟函数所处的位置是没有关系的，跟调用的位置才有关系**

```JS
//案例3
var obj = {
    name:"小余",
    foo:function(){
        console.log(this);
    }
}

var fn = obj.foo

fn()
//答案是指向window，而不是obj
```

> 跟案例3只有一点点的小变化，只是将foo移到了obj外面，然后在obj内部进行引用了，本质上没有变化

```js
//案例四
function foo(){
    console.log(this);
}

var obj = {
    name:"小余",
    foo:foo
}

var bar = obj.foo
bar()
//window
```

> 之前用过的非常熟悉的案例

```js
//案例5
function foo(){
    function bar(){
        console.log(this);
    }

    return bar
}

var fn = foo()
fn()
//这个时候fn函数调用时返回window，非常熟悉的，fn调用的时foo函数里面的bar函数，并没有扯到foo函数上，属于独立调用。然后就是闭包调用必然指向window这种结论是错误的，换种调用方式，就会发生改变了

var obj = {
    name:"大余",
    age:fn
}

obj.age()//隐式绑定
//这种调用方式，js引擎会将obj绑定到我们age的函数内部
//看，此时外面又创建了一个obj对象，里面的age指向就是我们刚刚认定的闭包的fn或者说bar函数，调用顺序是age -> fn -> bar，但此时我们再调用age的时候，返回的结果不再是window了，因为我们输出前的那一刻的调用方式已经发生了变化，此时不再是函数独立调用
```

### 规则2：隐式绑定

- 另外一种比较常见的调用方式是通过某个对象进行调用的
  - 也就是它的调用位置中，是**通过某个对象发起的函数调用** 

```js
//案例1
function foo(){
    console.log(this);
}

var obj = {
    name:"狗洛",
    foo:foo
}

obj.foo()
```

返回结果：

![image-20221220224644327](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20221220224644327.png)

> 我们通过这种在一开始就演示过的代码中可以看到，obj里面的eacting跟running函数的this是可以指向函数的父级作用域的，也就是obj函数
>
> - 因为我们在调用的时候，是通过obj.eating的方式，将obj绑定到eating里面，所以this指向会指到obj上面
> - **object对象会被js引擎绑定到fn函数中的this里面**

```js
//案例2
var obj = {
    name:"小余",
    eating:function(){
        console.log(this.name + "在吃东西");
    },
    running:function(){
        console.log(this.name + "在跑步");
    }
}

obj.eating()
obj.running()
```

> 如果我们将obj跟eating的绑定关系解除掉，再调用eating函数的时候，他的this的指向就会出现问题

```JS
var fn  = obj.eating
fn()
obj.running()
```

吃东西的this指向出现了问题，this.name的结果出不来了，因为我们在调用的时候已经将obj跟eating函数的关系给去除掉了，obj没有绑定到eating里面了，所以就指向不到了obj里面的内容了

![image-20221220230844569](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20221220230844569.png)

> 通过案例3，我们调用obj2中的bar属性，obj2.bar属性调用obj1中的foo函数。

```js
//案例3
var obj1 = {
    name:"obj1",
    foo:function(){
        console.log(this);
    }
}

var obj2 = {
    name:"obj2",
    bar:obj1.foo
}

obj2.bar()
//node环境返回结果：{ name: 'obj2', bar: [Function: foo] }
```

控制台打印结果：

![image-20221220232552486](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20221220232552486.png)

> 那此时这个this是绑定到了谁身上，我们通过结果可以看到是obj2的身上，首先我们bar是调用到了obj1中的foo函数身上，但是最后我们执行的时候，是通过obj2来进行执行的，所以obj2就被绑定到了foo函数里面去了，所以此时foo函数控制台打印this的结果才会是obj2里的内容

![image-20221220233110245](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20221220233110245.png)

### 规则3：显示绑定

- 隐式绑定有一个前提条件：
  - 必须在调用的对象内部有一个对函数的引用(比如一个属性)；
  - 如果没有这样的引用，在进行调用时，会报找不到该函数的错误
  - 正是这个引用，间接的将this绑定到了这个对象上

> 通俗的说就是：以上面obj1、obj2的例子来说，我们obj2如果想要调用obj1里的函数的话，我们就得想办法把obj1里的这个函数放到obj2里的属性里面，然后使用obj2对bar进行一个引用，然后我们才能用obj2.bar进行一个调用

- 如果我们不希望在**对象内部**包含这个函数的引用，同时又希望在这个对象上进行强制调用，该怎么做呢？
  - JavaScript所有的函数都可以使用call和apply方法(这个和Prototype有关)
    - 它们的区别是：第一个参数是相同的，后面的参数apply为数组，call为参数列表
  - 这两个函数的第一个参数都要求是一个对象，这个对象的作用是什么呢？就是给this准备的
  - 在调用这个函数时，会将this绑定到这个传入的对象上

### apply、call、bind的使用

#### call函数

> 函数上面有call方法，所以当我们使用foo.call()的时候，他也会去帮我们调用函数。JavaScript内部已经帮我们实现了一个call函数了

```js
function foo(){
    console.log("函数被调用了");
}

foo()
foo.call()
//调用的结果是一样的
// 函数被调用了
// 函数被调用了
```

#### apply函数

> 跟call函数同理的，函数上同样有apply函数，一样是JavaScript内部替我们实现的

```js
function foo(){
    console.log("函数被调用了");
}

foo()
foo.apply()
// 函数被调用了
// 函数被调用了
```

#### call函数与apply函数的区别

> - 它们两者传参的方式不大一样
>   - call是依次传的
>   - apply是以数组的形式传的
> - call和apply在执行函数时，是可以明确的绑定this，这个绑定规则称之为**显示绑定**

```js
function sum(num1,num2){
    console.log(num1+num2,this)
}

sum.call("call",20,30)//后面的参数是以逗号来做一个分割的，依次挨个传就行
sum.apply("apply",[20,30])//后面的参数是以数组存在的

//50 [String: 'call']
//50 [String: 'apply']
```

#### 直接调用与call、apply的不同

- 如果说`foo()`直接调用跟call、apply的结果是一样的，为什么不全部使用直接调用呢？

  - 首先，它们的this绑定是不一样的
  - foo()直接调用，指向的是全局对象(window)
  - call、apply可以手动指定我们所指向的this是谁，很多时候我们使用这两个的目的也就是这个

  ```js
  function foo(){
      console.log("函数被调用了",this);
  }
  
  var obj = {
      name:"班花姐姐"
  }
  
  foo()
  foo.apply("小余")
  foo.call(obj)
  ```

  返回结果如下：

  ![image-20221221024716057](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20221221024716057.png)

#### 显示绑定 -- bind

> 当我们要重复使用多次绑定的时候，反复调用call或者apply，往里面填写重复的参数的时候，就会显得比较累赘，这个时候我们就可以使用bind函数来替代，bind函数是会返回一个值的，这个时候我们就可以声明一个函数(这个函数也会重新开辟一个堆空间来进行存放的)来接收他，然后直接调用这个声明的函数就可以了
>
> - 然后我们可以看到，我们调用newFoo函数的时候是独立调用的，这个时候应该是指向window才对，但是我们已经用bind将newFoo的指向明确固化到"小余"上面了，这个时候**规则就会冲突，显示绑定bind函数的优先级高于默认绑定**

```js
function foo(){
    console.log(this)
}

// foo.call("小余")
// foo.call("小余")
// foo.call("小余")
// foo.call("小余")
// foo.call("小余")

//默认绑定和现实绑定bind冲突
var newFoo = foo.bind("小余")

newFoo()//是不是比每次都写foo.call("小余")方便一些？
```

> 我们将foo.bind赋值给了newFoo，又声明了bar来接收了foo函数，我们的目的是为了对比它们的this指向问题
>
> - 第一个对比的是bar函数跟foo函数，很明显，是直接赋值的关系，它们是一样的(包括了this也指向一样的地方)，返回true
> - 第二个对比的是newFoo函数和foo函数，这里它们的不同在于newFoo接收的并不是foo函数本身，唯一的变量是被bind修改了this指向的foo函数。经过对比，它们是不相等的，返回的是false，也证明了一点：**bind函数会返回新的内容，但不会修改原本函数的this(他们指向的不是同一块内存空间，而是不相干的两处地方，不然此时foo与newFoo的对比就该返回true了**

```js
function foo(){
    console.log(this)
}

// foo.call("小余")
// foo.call("小余")
// foo.call("小余")
// foo.call("小余")
// foo.call("小余")

//默认绑定和现实绑定bind冲突
var newFoo = foo.bind("小余")

var bar = foo
console.log(bar === foo);//true
console.log(newFoo === foo);//false
```

> `bind`函数和`call`和`apply`函数都可以用来改变函数的调用对象。但是它们之间有一些微妙的差别。
>
> 下面是这三个函数的一些基本区别：
>
> - `bind`函数会创建一个新函数，其中调用对象被固定为指定的值。而`call`和`apply`函数则是立即调用函数，并改变调用对象。
> - `bind`函数可以在调用时指定函数的参数，而`call`和`apply`函数则需要在调用时传入所有的参数。
> - `bind`函数返回的是一个新的函数，而`call`和`apply`函数则是立即执行函数。

### 规则4：new绑定

- JavaScript中的函数可以当作一个类的构造函数来使用，也就是使用new关键字 
- 使用new关键字来调用函数是，会执行如下的操作：
  1. **创建一个全新的对象**
  2. 这个新对象会**被执行prototype连接**
  3. 这个新对象会**绑定到函数调用的this上**(this的绑定在这个步骤完成)
  4. 如果函数没有返回其他对象，表达式会返回这个新对象

> new来调用，会把我们生成的新的对象赋值给这个Person里面内部的的this
>
> 准确来说，js 中的构造函数只是使用new 调用的普通函数，它并不是一个类，最终返回的对象也不是一个实例，只是为了便于理解习惯这么说罢了。
>
> 那么new一个函数究竟发生了什么呢，大致分为三步：
>
> 1. 以构造器的prototype属性为原型，创建新对象；
> 2. 将this(可以理解为上句创建的新对象)和调用参数传给构造器，执行；
> 3. 如果构造器没有手动返回对象，则返回第一步创建的对象

```js
function Person(){
    console.log(this);
}

Person()//正常调用
new Person()//new调用
```

调用区别：![image-20221221114414556](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20221221114414556.png)

> 我们通过一个new关键字调用一个函数时(构造器)，这个时候this是在调用这个构造器创建出来的对象
>
> - this = 创建出来的对象
> - 这个绑定过程就是new 绑定

```js
//案例2
function Person(name,age){
    this.name = name
    this.age = age
}

var p1 = new Person("小余",20)
console.log(p1.name,p1.age);
var p2 = new Person("小满",23)
console.log(p2.name,p2.age);
//小余 20
//小满 23
```

## 一些函数的this分析

### 内置函数的绑定思考

- 有些时候，我们会调用一些JavaScript的内置函数，或者一些第三方库的内置函数
  - 这些内置函数会要求我们传入另外一个函数；
  - 我们自己并不会显示的调用这些函数，而且JavaScript内部或者第三方库内部会帮助我们执行；
  - 这些函数中的this又是如何绑定的呢？
- setTimeout、数组的forEach、div的点击

### setTimeout定时器

```js
setTimeout(function(){
    console.log("正常的this",this);//window
    
    
})

setTimeout(()=>{
    console.log("箭头函数的this",this);//
},2000)
```

node环境下的结果：

![image-20221222131325900](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20221222131325900.png)

### 监听点击

> css样式不写，自己写一个宽高加背景颜色出来方便点击
>
> 我们监听点击中的this给到我们的是监听的对象，也就是如下的东西(div的元素对象)：
>
> ![image-20221222183921708](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20221222183921708.png)
>
> this指向了div的元素对象，这说明了这个boxDiv会拿到内部的函数的，然后进行调用，相当于
>
> ```js
> boxDiv.onclick()
> ```
>
> 也就是隐式绑定了(只不过内部进行了，没有显示出来)，将boxDiv绑定到了onclick上面，所以this会绑定到div元素对象上

```html
    <div class="box"></div>
    <script src="./闭包.js"></script>
```

```js
//1.只能添加一个，如果重复添加，下面那个会把前面的给覆盖掉
const boxDiv = document.querySelector(".box")
boxDiv.onclick = function(){
    console.log(this);
}

//2.能够添加多个，实现原理：将所有的函数收集到数组里面，一旦发生点击的时候，我们就遍历数组，对这些函数进行调用，
//然后内部会进行fn.call(boxDiv)，实现将this绑定到boxDiv身上
boxDiv.addEventListener('onclick',function(){
    console.log(this);
})

boxDiv.addEventListener('onclick',function(){
    console.log(this);
})

boxDiv.addEventListener('onclick',function(){
    console.log(this);
})
```

### 数组中的绑定

> 正常情况下是返回window，但是forEach是接收第二个参数，第二个参数可以帮我们绑定对象，也就包括了this的指向位置

```js
//3.数组forEach map filter find 
var names = ["ABC",'小余','小满']
names.forEach(function(){
    console.log("item",this);
})
//返回连续3个window

//如果forEach加上了第二个参数,则this指向就会发生改变，因为绑定的对象已经被我们手动设置了，同理的map filter find 这些数组的高阶函数都差不多
names.forEach(function(){
    console.log("item",this);
},"小余")
```

**forEach不加第二个参数：**

![image-20221222204947490](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20221222204947490.png)

**forEach加第二个参数：**

![image-20221222205023654](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20221222205023654.png)

**其他函数的效果(不一定就这些)：**

```js
names.forEach(function(){
    console.log("forEach",this);
},"小余")

names.map(function(){
    console.log("map",this);
},"小余")

names.filter(function(){
    console.log("filter",this);
},"小余")

names.find(function(){
    console.log("find",this);
},"小余")
```

![image-20221222231000791](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20221222231000791.png)

## this规则优先级

- 学习了四条规则，接下来开发中我们只需要去查找函数的调用应用了哪条规则即可，但是如果一个函数调用位置应用了很多条规则，优先级谁更高呢？

  1. **默认规则的优先度是最低的**

     - 毫无疑问，默认规则的优先级是最低的，因为存在其他规则时，就会通过其他规则的方式来绑定ths

  2. **显示绑定优先级高于隐式绑定**

     - 代码测试

       ```js
       var obj = {
           name:"小余",
           foo:function(){
               console.log(this);
           }
       }
       obj.foo()//单纯隐式绑定
       //{ name: '小余', foo: [Function: foo] }
       //1.call、apply的显示绑定高于隐式绑定
       obj.foo.call("我是小满")//显示绑定跟隐式绑定的冲突
       //[String: '我是小满']
       
       //2.bind与隐式绑定的优先度比较
       var bar = obj.foo.bind("小余666")
       bar()
       ```

       > bind更明显的比较

       ```js
       //更明显的比较
       function foo(){
           console.log(this)
       }
       
       var obj1 = {
           name:"这是bind更明显的比较",
           foo:foo.bind("喜多川")
       }
       
       obj1.foo()//此时的foo属性才是被绑定到bind上面，前面刚开始的优先度比较更像是直接调用bind传入bar的，不够公平
       //[String: '喜多川']
       //答案返回的是喜多川，所以bind的显示绑定优先度也更高
       ```

  3. new绑定优先级高于隐式绑定

     - 代码测试

       > 如果this打印出来的是obj对象，则证明隐式绑定的优先度更高，如果是foo创建出来的函数对象，则证明new的优先度更高

       ```js
       var obj = {
           name:"小满Vue3视频讲得不错",
           foo:function(){
               console.log(this);
           }
       }
       
       var f = new obj.foo()
       //foo {} 是foo创建出来的函数对象，证明了new的优先度更高
       obj.foo()//这是隐式绑定的写法，可以方便进行对比
       ```

       隐式绑定的结果应该是下面这样的：

       ![image-20221223000646446](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/xiaoyu925/image-20221223000646446.png)

  4. **结论：new关键字是不能够跟call和apply一起来使用的**

     因为call、apply跟new一样都是主动的去调用函数的，是不能够放在一起来使用。所以我们只能够将bind跟new来进行比较，这证明了一件事，那就是bind不是主动去调用函数的(他虽然也改变了this指向，但是会返回新的内容且需要我们去调用，并且不影响之前的内容)，下方的案例也说明了这点

     **但是来了：new关键字内部在去执行的时候，会找到原函数的，将原来的函数当作一个构造器(构造器的概念直接跳到后面面向对象的部分看)，这就是为什么new出来的bar函数最终还是调用foo函数的原因**

     ```js
     function foo(){
         console.log(this);
     }
     
     var bar = foo.bind("测试一下")
     //new出来的bar函数最终还是调用foo函数的
     var obj = new bar()//foo{}
     bar()//[String: '测试一下']
     ```

- ### 优先度总结

  new绑定>显示绑定(apply、call、bind)>隐式绑定>默认绑定(独立函数调用 )

  bind高于call(一般情况下也不会同时用这两个，当作一个了解即可)

## this规则之外

- 我们讲到的规则已经足够应付平时的开发了，但是总有一些语法，超出我们的规则之外。

### 特殊绑定--忽略显示绑定

> apply、call、bind：当传入null/undefined时，自动绑定成全局对象

```javascript
function foo(){
    console.log(this);
}
foo()
foo.apply(null)
foo.apply(undefined)
//打印出来全部都是window，我们可以看到填入 null跟undefined打印出来的也是全局的对象
```

### 特殊绑定--间接函数引用

- 另外一种情况，创建一个函数的间接引用，这种情况使用默认绑定规则
  - 赋值(obj2.foo = obj1.foo)的结果时foo函数
  - foo函数被直接调用，那么是默认绑定 

> 第二种情况是一种独立函数调用，将obj2.foo = obj1.foo作为一个整体来调用。这种情况叫做间接引用，我们并没有直接拿到这个函数，而是通过obj2.foo = obj1.foo这个表达式来返回函数，然后对这个函数做一个调用。这种情况也属于**独立函数的调用**
>
> 

```javascript
//争论：代码规范，到底加不加分号;
var obj1 = {
    name:"这是onj1",
    foo:function(){
        console.log(this);
    }
}

var obj2 = {
    name:"这是obj2",
}

obj2.foo = obj1.foo
obj2.foo()//{ name: '这是obj2', foo: [Function: foo] }

//第二种情况，比较难的情况
(obj2.foo = obj1.foo)()
```

> 第二种情况特殊情况(了解就行，一般没人这么写)
>
> 如果我们不再obj2对象结束那里加上分号的话，编辑器会连带这下面的调用当作一个整体，这是语法分析的一个问题

```javascript
var obj2 = {
    name:"这是obj2",
}
//会将obj2对象连着下面调用当作一个整体
//第二种情况，比较难的情况
(obj2.foo = obj1.foo)()
----------------------------------
//相当于变成如下情况
var obj2 = {
    name:"这是obj2",
}(obj2.foo = obj1.foo)()
//会报错：Uncaught TypeError: Cannot set properties of undefined (setting 'foo')
---------------------------------------
//加上分号后：
var obj2 = {
    name:"这是obj2",
};
(obj2.foo = obj1.foo)()
//正常返回window
```

#### 测试代码(来自你不知道的JavaScript)

```js
function foo(el){
    console.log(el,this);
}

var obj = {
    id:"I am is XiaoYu"
}

[1,2,3].forEach(foo,obj)
//无法运行
//报错：Uncaught TypeError: Cannot read properties of undefined (reading 'forEach')
---------------------------------------
//解决方法1：
function foo(el){
    console.log(el,this);
}

var obj = {
    id:"I am is XiaoYu"
}

var names = [1,2,3]
names.forEach(foo,obj)
------------------------------------------
//解决方法2：
function foo(el){
    console.log(el,this);
}

var obj = { 
    id:"I am is XiaoYu"
};//加上分号，不然会将obj对象和底下的当作一个整体

[1,2,3].forEach(foo,obj)//foo是我们在上面独立定义了,obj是我们传入forEach中this要绑定的对象
```

## 箭头函数arrow function

- 箭头函数是ES6之后增加的一种编写函数的方法，并且它比函数表达式要更加简洁：
  - 箭头函数不会绑定this、argument属性
  - 箭头函数不能作为构造函数来使用(不能和new一起来使用，会抛出错误)

### 箭头函数的使用解析

> 编写箭头函数
>
> ()：参数
>
> =>：箭头
>
> {}：函数执行体(在一些特殊场景大括号可以省略 )

```js
//方式1：
var nums = [10,20,30,40]

nums.forEach((num1,num2,num3)=>{
    console.log(num1,num2,num3)
})
----------------------------------------------------------
//方式2(完整写法)：
 var foo = (num1,num2,num3)=>{
    console.log(num1,num2,num3)
}

var nums = [10,20,30,40]

nums.forEach(foo)
```

#### 箭头函数常见简写方法

```javascript
//简写1：如果参数只有一个，小括号可以省略
//简写前：
nums.forEach((item)=>{
    console.log(item)
})
//简写后：
nums.forEach(item=>{
    console.log(item)
})

//简写2：如果执行体只有一个，大括号可以省略
nums.forEach(item => console.log(item))
//强调：并且它会默认将这行代码的执行结果作为返回值
var newNums = nums.filter(item => item % 2 === 0)//item % 2 === 0的结果会默认返回
console.log(newNums)
//一般情况下我们带大括号的是需要手动return返回的，就像这样
var newNums = nums.filter(item => {
    return item % 2 === 0//这种有大括号的情况下，如果我们不return的话，会返回[]
})
```

> filter/map/reduce结合使用
>
> 用一行完成了对初始值的：过滤出偶数并将其每个偶数扩大100倍，使其相加

```javascript
var nums = [10,20,30,40,51]
// filter/map/reduce结合使用
var result = 
    nums.filter(item => item % 2 === 0)
        .map(item => item *100)
        .reduce((preValue,item)=>preValue+item)
    console.log(result)
```

```javascript
//简写3：如果一个箭头函数，只有一行代码，并且返回一个对象，这个时候如何编写简写
var bar = ()=>{
    return {
        name:"小余",
        age:20
    }
}
//如果你按照上面的简写思路的话，那应该是
var bar = ()=> {name:"小余",age:18}//但这种写法其实是错误的，因为这里会发生混乱，这个大括号到底是判定为执行体还是对象呢？JS引擎会发生错乱
//正确的简写方式
var bar = ()=> ({name:"小余",age:18})
//使用小括号将对象包裹起来，这个是将对象当作一整个整体
```

#### 箭头函数的this获取

> 箭头函数不会绑定this
>
> - 为什么3种不同方式的调用都是window，首先那是因为我们foo的上层作用域是全局的，全局的可不就是window，然后就是我们的call怎么没有改变成功this的指向呢？那是因为箭头函数的原因，箭头函数不会绑定this属性，而我们的foo函数恰巧使用了箭头函数，造成了所有的绑定效果都是指向window，这是很有用的一个特点

```js
var name = "小余"

var  foo = ()=>{
    console.log(this);
}

foo()//window
var obj = {foo:foo}
obj.foo()//window
foo.call("这是call调用的")//window
```

> 有无使用箭头函数的this对比

```javascript
//对比前，没有使用箭头函数
var name = "小余"
function foo(){
    console.log(this);
}

// foo()
var obj = {name:"你已经被小余绑定到obj上啦",foo:foo}
obj.foo()//{ name: '你已经被小余绑定到obj上啦', foo: [Function: foo] }
-----------------------------------------------------------------------------------------------
//对比后，使用箭头函数
var name = "小余"

var  foo = ()=>{
    console.log(this);
}
var obj = {name:"你已经被小余绑定到obj上啦",foo:foo}
obj.foo()//window
```

#### 箭头函数应用场景

> 发送网络请求，将结果放到上面data属性中
>
> 在没有箭头函数的时候，我们通过在getData中创建一个变量将getData内的this的指向进行一个接收来进行使用
>
> 有了箭头函数就不需要这么麻烦，因为箭头函数没有绑定this，所以我们在调用obj.getData的时候，this不会被隐式绑定给强行改变，没有被改变的话，当this在当层找不到想要的就会直接去自己的上层找

```javascript
//无箭头函数时候
var obj = {
    data:[],
    getData:function(){
        //在没有箭头函数的时候，大家通常是这么解决问题的
        var _this = this//这里的this就是obj对象了，getData的上一层可不就是obj
        setTimeout(function(){//没有使用箭头函数，会出现问题，所以要加上var _this = this，然后使用_this
            var result = ["小余",'小满','康老师']
            _this.data = result//_this是外层的变量，这里就形成了一个闭包
            console.log(this)
        },2000)
    }
}

obj.getData()//没有使用箭头函数或者没有声明一个变量来接收getData里面的this的时候，为什么是window，那是因为foo函数绑定到obj上面啦，obj的上层就是全局window了，这是隐式绑定
```

```javascript
//有箭头函数的时候
var obj = {
    data:[],
    getData:function(){
        setTimeout(()=>{
            var result = ["小余",'小满','康老师']
            this.data = result//直接使用this
            console.log(this)//通过直接打印this进行检测
        },2000)
    }
}

obj.getData()
```

### this面试题

####  面试题1

```javascript
//无答案解析版本
var name = "window"
var person = {
    name:"person",
    sayName:function(){
        console.log(this.name);//这里的答案是谁
    }
};
function sayName(){
    var sss = person.sayName
    sss();//调用打印出来的是什么
    person.sayName();//？
    (person.sayName)();//？
    (b = person.sayName)();//？
}

sayName()
```

```javascript
//答案解析版本
var name = "小余window"
var person = {
    name:"person",
    sayName:function(){
        console.log(this.name);
    }
};
function sayName(){
    var sss = person.sayName
    sss();//this.name是小余window ，独立函数调用，所以这里的this指向最外层的window
    person.sayName();//隐式调用，this指向person，控制台打印的this.name是person
    (person.sayName)();//person，隐式调用
    (b = person.sayName)();//间接函数引用，是独立的函数调用，所以是小余window，(b = person.sayName)是一个整体
}

sayName()
```

#### 面试题2

```javascript
var name = 'window'

//person1是字面量对象
var person1 = {//定义对象的时候是不会产生作用域的，所以对象里面的上层在对象外面
  name: 'person1',
  foo1: function () {
    console.log(this.name)
  },//普通函数
  foo2: () => console.log(this.name),//箭头函数
  foo3: function () {
    return function () {
      console.log(this.name)
    }
  },//函数套函数，返回普通函数
  foo4: function () {
    return () => {
      console.log(this.name)
    }
  }//函数套函数，返回箭头函数
}

var person2 = { name: 'person2' }

// person1.foo1(); // person1(隐式绑定)
// person1.foo1.call(person2); // person2(显示绑定优先级大于隐式绑定)

// person1.foo2(); // window(不绑定作用域,上层作用域是全局)
// person1.foo2.call(person2); // window

//这里的person1.foo3()的调用下拿到结果在()继续调用，这种属于独立调用 
// person1.foo3()(); // window(独立函数调用)
// person1.foo3.call(person2)(); // window(独立函数调用)
// person1.foo3().call(person2); // person2(最终调用返回函数式, 使用的是显示绑定)

// person1.foo4()(); // person1(箭头函数不绑定this, 上层作用域this是person1)
// person1.foo4.call(person2)(); // person2(上层作用域被显示的绑定了一个person2)
// person1.foo4().call(person2); // person1(上层找到person1)
```

#### 面试题3

> - 连续new了两次，代表构造函数会被连续调用两次
>
> - 每次new的时候都会创建一个新的对象，这里new了两次表示创建了两个新的对象
>
> ```js
> var person1 = new Person('person1')
> var person2 = new Person('person2')
> //创建出来的2个新对象：
> this = {name:"person1",foo1:function{}}
> this = {name:"person2",foo1:function{}}
> ```

```javascript
var name = 'window'

function Person (name) {//作为构造函数，一般情况下，我们都开头字母大写
  this.name = name
  this.foo1 = function () {
    console.log(this.name)
  },
  this.foo2 = () => console.log(this.name),
  this.foo3 = function () {
    return function () {
      console.log(this.name)
    }
  },
  this.foo4 = function () {
    return () => {
      console.log(this.name)
    }
  }
}

var person1 = new Person('person1')
var person2 = new Person('person2')

person1.foo1() // person1
person1.foo1.call(person2) // person2(显示高于隐式绑定)

person1.foo2() // person1 (上层作用域中的this是person1)
person1.foo2.call(person2) // person1 (上层作用域中的this是person1)

person1.foo3()() // window(独立函数调用)
person1.foo3.call(person2)() // window
person1.foo3().call(person2) // person2

person1.foo4()() // person1
person1.foo4.call(person2)() // person2
person1.foo4().call(person2) // person1


var obj = {
  name: "obj",
  foo: function() {

  }
}
```

#### 面试题4

> 通常我们会对什么时候调用感到疑惑，例如下面这两个
>
> ```javascript
> person1.obj.foo2.call(person2)() // person2
> person1.obj.foo2().call(person2) // obj
> ```
>
> - 他们的区别从foo2开始发生不同，foo2.xxx表示到了foo2还没调用，而是继续深入到里面
> - foo2()则是调用了，然后foo2属性对应的将会生效替代foo2()部分，变为foo2().call(person2)
> - 在下面的表达式中，foo2()属性调用则是return了一个箭头函数，既然return了，那就跳出外面一层function了，且箭头函数是不受call改变this的，this的指向当然就是obj咯(return出来的函数的上一层或者说父级作用域就是obj函数)，所以this.name自然就是obj对象里面的name：obj了

```javascript
var name = 'window'

function Person (name) {
  this.name = name
  this.obj = {//对象里面封装对象
    name: 'obj',
    foo1: function () {
      return function () {//普通返回
        console.log(this.name)
      }
    },
    foo2: function () {
      return () => {//箭头函数返回
        console.log(this.name)
      }
    }
  }
}

var person1 = new Person('person1')
var person2 = new Person('person2')

person1.obj.foo1()() // window
person1.obj.foo1.call(person2)() // window
person1.obj.foo1().call(person2) // person2

person1.obj.foo2()() // obj
person1.obj.foo2.call(person2)() // person2
person1.obj.foo2().call(person2) // obj


// 

// 上层作用域的理解
// var obj = {
//   name: "obj",
//   foo: function() {
//     // 上层作用域是全局
//   }
// }

// function Student() {
//   this.foo = function() {

//   }
// }
```

# 