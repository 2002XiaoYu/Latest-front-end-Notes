# 02-内存管理和内存泄露

> ### 作者：小余同学
>
> 为什么没有01，那是因为01是讲谷歌V8引擎的原理，我看完了，但忘记写笔记了，不想重新看一遍写笔记了hh，大致来说也是很重要的一章节
>
> 需要全系列笔记请到[2002XiaoYu (小余) (github.com)](https://github.com/2002XiaoYu)中自行获取，觉得不错给个star，这是对作者非常大的鼓励

[TOC]

## 全局作用域(var)

> #### 基于早期ECMA的版本规范：
>
> GEC(global excution context)全局执行上下文：执行全局代码
>
> FEC(functional excution context)函数执行上下文：执行函数代码
>
> 每一个执行上下文会被关联到一个变量环境(variable object，VO)，在源代码中的变量和函数声明会被作为属性添加到VO中
>
> 对于函数来说，参数也会被添加到VO中
>
> - 解释
>   - 不管执行的是哪个，都会关联到VO对象(variable object)，只是这个VO对象所代表的东西不一样而已
>   - 参数被添加到VO中，是形参的那个地方

### 代码执行过程

- `foo`函数

  ```js
  function foo(){
      //foo函数并没有特殊的含义，是编程约定俗成的一种习惯(定义我们不知道要取什么名字的东西)
  }
  ```

- 函数变量提升

  ```js
  function foo(){
      console.log("小余")
  }
  foo()
  //会在控制台打印出"小余"
  ```

  ```js
  foo()
  function foo(){
      console.log('小余')
  }
  //一样在控制台能够打印出来'小余'
  ```

### 全局函数执行过程

> 编译阶段是js->AST的时候就确立了

**变量对象的定义**：变量对象是一个特殊的对象，并且与执行上下文息息相关，VO（变量对象）里面会存有下列内容：

- variables(var,variableDeclaration);
- function declarations(FD)
- function formal parameters

**AO对象**
函数执行的前一刻，会创建一个称为执行期上下文的内部对象（AO）。一个执行期上下文定义了一个函数执行时的环境，函数每次执行时对应的执行上下文都是独一无二的，所以多次调用一个函数会导致创建多个执行上下文，当函数执行完毕时，它所产生的执行上下文会被销毁。

- 讲人话就是：`AO对象`在函数执行上下文里面

----

- 当我们创建了函数的时候，js引擎会重新开辟一块空间来进行存储(编译阶段)

  > 1. 保存父级作用域(函数的上一层作用域)
  > 2. 保存函数的执行体(就是执行的代码块)

- 开辟的内存空间一般是0x开头
  - 我们在`GlobalObject`(全局对象)中一般是放入了我们函数的名字，例如foo。
  - 然后在全局对象中的foo再引用了对应存储函数的内存空间，也就是保存的是指向该函数的内存地址的指针
  - foo()的()是调用的意思，执行之后就会放入函数的调用栈中(调用栈会再创建出来一个`函数执行上下文(Functional Execution Context)`，在里面会有一个类似GO`global object`的东西，叫做AO`Activation Object`
  - 在执行函数之前会先创建AO对象，会将函数的内容提升到AO对象中，此时函数里面的内容都是undefined，当我们在AO开始执行函数代码的时候，函数内的undefined逐渐被替换掉执行的内容
  - 函数里所有代码执行完了之后，函数的执行上下文就会被弹出栈，执行上下文就会销毁掉。此时AO如果没有人指向它的话，也会跟着一起销毁掉
  - 如果后续在后面又调用了一遍foo()，然后没准还传了一些参数进去，那刚刚的过程又会重复执行了一遍

### 作用域链

- 当我们查找一个变量时，真实的查找路径是沿着作用域链来查找的
- 作用域链组成
  - VO(variable Object 变量对象)
  - Parent(父级作用域)
- 只有函数才会产生作用域，父级作用域在编译阶段就被确认了

### 全局代码执行过程(函数嵌套)

> - 函数里面如果嵌套函数的话，这个时候进行执行的时候，嵌套函数是没有被编译的，而是预编译。等AO对象被创建的时候，它才会被正式编译

```js
var name="why"
foo(123)
function foo(num){
	console.log("1",m);
    var n = 10
    var m = 20
    
    function bar(){
        console.log("2",name)
    }
    bar()
}
//结果如下
1 undefined
2 why
```

- 此时如果我们把`var name="why"`注销掉，会发现控制台的`2 why`并没有消失
  - 因为windows的属性上面本来就有name这个属性，当我们注销掉的时候，值已经保存在windows的name里面了，控制台打印一下windows能找到
  - 所以最好的办法就是我们给name换一个变量名，比如XiaoYu
  - 然后重复上面操作注销掉XiaoYu这个变量，此时就出现我们想要看到的结果：找不到了

### 函数调用函数执行过程

- 函数调用函数的作用域查找

  ```js
  //message打印出来的是哪里的内容
  
  var message = "小余"
  
  function foo(){
      console.log(message)
  
  
  function bar(){
      var message = "大余"
      foo()
  }
  
  bar()
  //打印结果：小余
  ```

- bar()执行的时候会调用一个AO，也就是函数执行上下文

  - AO里面是一个message，此时还是undefined。下一步往里面填入"大余"
  - 再下一步执行foo()，这个时候就又创建出来一个执行上下文的空间用来执行bar里面的foo函数。这个执行上下文就又是一个AO，但此时这个AO里面是空的
  - 此时要取找message，是按照作用域链去查找的，查找的话首先是从自己身上查找，foo自己身上是没有message的，这里的执行上下文是空的，那下一个就是父级作用域，注意这里的父级作用域，不是看你foo()在哪调用的上一级，而是你函数写在哪的上一级，foo函数跟bar函数是并列的，他们的上一级都是全局作用域了，在全局作用域上面的也就只有`var message="小余"`，所以打印的结果为小余
  - 对于函数来说AO其实就是VO

---

以上的都是ES6之前的概念(也就是ES5)

## 新的ECMA标准-变量环境和环境记录

**在最新的ECMA的版本规范中，对一些词汇进行了修改**

> 每一个执行上下文会关联到一个`变量环境`(Variable Environment)中，在执行代码中变量和函数的声明会作为`环境记录`(Environment Record)添加到变量环境中。
>
> 对于函数来说，参数会被作为环境记录添加到变量环境中
>
> - 解释
>   - VO对象(对应AO)被Variable Environment(变量环境)替代
>   - VO对象(对应GO)被VE，也就是Variable Environment，都是变量环境进行了替代
>   - 变量环境里的是环境记录

### 作用域提升面试题

#### 面试题1

> 在foo()里面没有声明n，自己没有就会向父级作用域去找，在父级作用域找到var n = 100，然后进行了覆盖操作，此时我们再打印n出来就是200了
>
> 我们打印的时候，console.log(n)的n其实不是值，而是存放指向值存放的内存空间的指针

```js
var n = 100
function foo(){
    n = 200
}
foo()
console.log(n)
//答案是200
```

#### 面试题2

> 函数内的是foo对象，函数外面的是go(global Object)对象、下面全部统一称呼go对象
>
> 流程步骤：
>
> 1. 最先触发var m = "大余"，go对象：{m：undefined} => {m："大余"}
> 2. 然后执行foo()函数，创造出一个AO对象(执行上下文)，此时还处于编译状态，AO对象：{m：undefined}，注意了，这个时候查找顺序是从自身开始查找，自身没有才去父级查找，而`大余`这个变量信息是在foo()的父级作用域中，所以之后才会去查找。此时执行上下文里面要去打印console.log(m)，先在自己的作用域链中查找，而此时m已经在自身作用域中被赋值了一个undefined，但为什么不往父级找呢？首先这边执行上下文并没有结束，而在下面的时候就找到了var m = "小余"了，所以他就不会在将AO对象的执行上下文都执行结束后再去父级作用域寻找
> 3. 最后执行了执行上下文中var m = "小余"，将AO对象里的m的内容替代成了`小余`，，然后继续往下执行打印，打印出结果

```js
function foo(){
    console.log(m)
    var m = "小余"
    console.log(m);
}
var m = "大余"
foo()
//结果如下
//undefined
//小余
```

#### 面试题3

> 跟前面已经大同小异了
>
> 1. foo1函数的执行结果，如果自身作用域内没有找到n，就会沿着父级作用域寻找，然后foo1是在foo2函数内调用的，父级作用域并不取决于在哪调用，而取决于你函数体处于哪里，foo1的作用域是跟foo2的作用域平级的，他们的父级作用域都是最外层的全局作用域。
> 2. 然后foo2内部首先自己创建出来一个AO对象，再AO对象里创建一个执行上下文，里面先对编译阶段的{n：undefined}进行赋值200，然后通过console.log进行了打印，接着调用了foo1()函数，这foo1()函数答案为一百，在上一步中我们已经进行分析了
> 3. 接着就是调用了foo2()，先打印了foo2中赋值的200，再打印foo1中的100。最后打印了最外层的n，100。这里最外层的打印只能打印100，100如果注销掉就报错，因为显而易见的，全局作用域基本上已经是最大的作用域了，再往上就找不到了，而这个是不会向函数内部去往下找的，且函数执行完后，他的执行上下文就销毁掉了

```js
var n = 100

function foo1(){
    console.log("这是foo1内部",n);
}

function foo2(){
    var n = 200
    console.log("这是foo2内部",n);
    foo1()
}

foo2()

console.log("这是最外层",n);
//执行结果顺序如下
//这是foo2内部 200
//这是foo1内部 100
//这是最外层 100
```

#### 面试题4

> 1. 首先最外层，一个GO对象(Global Object)：{a：undefined,foo:0xa00}，foo的0xa00是内存地址，然后a被赋值为100
> 2. 然后到foo函数部分，生成AO对象，AO对象里面是执行上下文，首先a的内容肯定是先为undefined，接着就return了，后面的var a = 100都还没生效foo函数就结束了，在编辑器中会给出提示：检测到无法访问的代码。但是还是请注意，这个执行上下文中还是出现了a这个变量，虽然完全没有用上，但是他意味着我们的执行上下文中还是出现了a这个变量，阻止了我们向父级作用域继续寻找的道路，所以我们访问不到全局作用域的100
> 3. 最后就只能返回undefined了

```js
var a = 100

function foo(){
    console.log(a)
    return
    var a = 200
}

foo()
//undefined
```

#### 面试题5

> var a = b = 10会转化为两行代码
>
> - var a = 10
> - b = 10(没错，b没有被var声明)，从右向左，先给b赋值
>
> 所以很显然，外面作用域是访问不到a，但是能访问到b的，不然你把console.log(a)注释掉，就可以正常显示控制台信息的b为10了

```js
function foo(){
    var a = b = 10
}
foo()
console.log(a);
console.log(b);
//会报错
```

#### 作用域补充

> 没有声明直接使用，严格来说，语法都错了，应该要报错的，因为我们甚至不知道这个变量是怎么来的，但是JavaScript的语法太灵活了，他允许了这种写法，但是最好不要这样写，就当作了解就行

```js
function foo(){
    m = 200
}

foo()
console.log(m);
//200
```

## 内存管理

### 认识内存管理

> 1. 不管什么样的编程语言，在**代码的执行过程中都是需要给它分配内存**的，不同的是**某些编程语言**需要我们自己**手动的管理内存**，**某些编程语言**会**自动帮助我们管理内存**
> 2. 不管以什么样的方式来管理内存，内存的管理都会有如下的**生命周期**：
>    - 第一步：跟配申请你需要的内存(申请)
>    - 第二步：使用分配的内存(存放一些东西，比如对象等)
>    - 第三步：不需要使用时，对其进行释放
> 3. **不同的编程语言对于第一步和第三步会有不同的实现**
>    - 手动管理内存：比如C、C++，包括早起的OC,都是需要手动来管理内存的申请和释放的(malloc和free函数)；
>    - 自动管理内存：比如Java、JavaScript、python、Swift、Dart等，它们有自动帮助我们管理内存
>    - 通常情况下JavaScript是不需要手动管理的，JavaScript会在**定义变量**的时候为我们分配内存

```js
//创建对象
//Java代码
Person p = new Person()
//JavaScript代码
var obj = {name:"why"}
```

### 内存分配方式

> - JavaScript对于**基本数据类型内存的分配**会在执行时，直接在栈空间进行分配
> - JavaScript对于**复杂数据类型内存的分配**会在堆内存中开辟一块空间，并且将这块空间的指针返回值变量引用。我们一般也称呼这个为引用类型
> - 栈空间存放的是地址，真正的对象实例存放在堆空间中

知识点补充：

1. 简单类型和复杂类型
简单类型
简单类型又叫做基本数据类型或者值类型

- 值类型： 简单数据类型/基本数据类型，在存储变量中存储的是值本身，因此叫做值类型
  String、number、Boolean、undefined、null
- 复杂类型
  复杂类型又叫做引用类型

- 引用类型： 复杂数据类型，在存储变量中存储的仅仅是地址（引用），因此叫做引用数据类型，通过new关键字创建的对象（系统对象、自定义对象），如Object、Array、Data等

## JavaScript的垃圾回收

> - 因为**内存的大小是有限**的，所以当**内存不再需要的时候**，我们需要**对其进行释放**，以便腾出**更多的内存空间**
> - 在手动管理内存的语言中，我们需要通过一些方式自己来释放不再需要的内存，比如free函数
>   - 但是这种管理的方式其实**非常的低效**，影响我们**编写逻辑的代码的效率**；
>   - 并且这种方式**对开发者的要求也很高**，并且**一不小心就会产生内存泄露**；
> - 所以现在大部分**现代的编程语言都是有自己的垃圾回收机制**：
>   - 垃圾回收的英文是**Garbage Collection**，简称`GC`；
>   - 对于那些**不再使用的对象**，我们都称之为**垃圾**，它需要被**回收**，以释放更多的内存空间
>   - 而我们的语言允许环境，比如Java的运行环境JVM，JavaScript的运行环境js引擎都会内存(内置) **垃圾回收器**；
>   - **垃圾回收器**我们也是简称**GC**，所以在很多地方你看到的GC其实是指垃圾回收器
> - 关键问题：**GC怎么知道那些对象不再使用的呢？**
>   - 涉及到GC的算法 =>作为子标题内容进行扩展

### 常见的GC算法 - 引用计数

> 对象里面有一个专门的空间，叫做 retain count，专门记录有多少个指针指向自己的retain count(一个指向加1)，默认为0，但通常最少是1，因为你在栈里面存放的地址已经就指向堆内存了)，这个计数器(retain count)是实时更新的，当这个计数器为0的时候，垃圾回收机制就知道这个对象已经没有人在使用了，就会触发回收机制销毁掉
>
> 引用计数弊端：
>
> - 循环引用
>
>   ```js
>   var obj1 = {friend:obj2}
>   var obj2 = {friend:obj1}
>   //这样互相引用如果不obj1 = null结束的话，会产生内存泄漏的
>   ```
>
> 

```js
var obj = {name:"小余"}

var info = {name:"大余",friend:obj}

var p = {name:"超大余",friend:obj}
```

### 常见的GC算法 - 标记清除

> 1. 这个算法是设置一个根对象(root object)，其实就是GO(Global Object)，垃圾回收器会定期从这个根开始，找所有从根开始有引用到的对象，对于哪些没有引用到的对象，就认为是不可用(不可达)的对象
> 2. 这个算法可以很好的解决循环引用的问题(因为被认为不可用的对象会在下一回中被回收掉)
> 3. JS引擎笔记广泛的采用就是标记清除算法，当然类似V8引擎为了进行更好的优化，它在算法的实现细节上也会结合一些其他的算法

# 03_JS的内存管理和闭包

## 让人迷惑的闭包

- 闭包是JavaScript中一个很难的知识点

### JS中函数是一等公民

> 一等公民：当前的这个东西非常灵活且非常重要的，例如可以作为另外一个函数的参数或者返回值来使用
>
> 比如Java的`对象`就是一等公民

- 在JavaScript中，函数是非常重要的，并且是一等公民：
  - 那么久意味着 函数的使用是非常灵活的
  - 函数可以作为另一个函数的参数，也可以作为另外一个函数的返回值来使用
- 自己编写高阶函数
- 使用内置的高阶函数
  - 把一个函数如果**接受另外一个函数作为参数**，或者**该函数回返回另外一个函数作为返回值的函数**，那么这个函数就称为一个**高阶函数**
  - 底下案例的封装函数小案例就是一个高阶函数
  - 函数作为参数、返回值使用
- Vue3+react
  - vue3 composition api：setup函数->代码(函数hook，定义函数)；
  - react：class->function->hooks


```js
//作为另一个函数的参数，js语法允许函数内部再定义函数
function foo(){
    function bar(){
        console.log("小余的bar");
    }
    return bar
}

var fn = foo()
fn()

//小余的bar
```

```js
//也可以作为另外一个函数的返回值来使用
function foo (aaaa){
    console.log(aaaa);
}

foo(123)

function bar(bbbb){
    return bbbb + "刚吃完午饭"
}


foo(bar("小余"))
// 123
// 小余刚吃完午饭
```

> - add5 = makeAdder(10)的意思是将10传入形参count中，然后你再调用add5(本质上这个时候add5调用的已经是add函数了)，然后在add5中传入的数值将会传入num中。
> - 固定了第一层的数据，并没有完全写死，完全可以在add5中进行定义，不止是add5，我们可以定义var add100 = makeAdder(100)，都是可以的，这样就可以去定制一些函数
> - 为什么我们makeAdder都调用完了，count为什么不会销毁，这就是闭包的使用

```js
function makeAdder(count){
    function add(num){
        return count + num
    }
    return add
}

var add5 = makeAdder(10)

console.log(add5(6));
console.log(add5(66));
//16
//76
```

#### 封装函数小案例

```js
//封装小案例
function calc(num1,num2,calcFn){
    console.log(calcFn(num1,num2));
}

function add(num1,num2){
    return num1 + num2
}

function sub(num1,num2){
    return num1 - num2
}

function mul(num1,num2){
    return num1 * num2
}

calc(10,10,add)
calc(10,10,sub)
calc(10,10,mul)
//20
//0
//100
```

### 数组中的5个常用高阶函数使用

> 挑选偶数的方式

```js
//普通使用
var nums = [2,4,5,8,12,45,23]

var newNums = []
for(var i = 0;i<nums.length;i++){
    var num = nums[i]
    if(num % 2 === 0){
        newNums.push(num)
    }
}
console.log(newNums)
//[ 2, 4, 8, 12 ]
```

#### filter过滤器

```js
//高阶函数filter过滤器的使用

//filter，对数组进行过滤，是数组中的一个方法，传入三个参数(第一个是数组中的值，第二个是数组的下标，第三个是我们当前数组的引用=>就是整个数组传进来),返回值是另外一个新的数组
var nums = [2,4,5,8,12,45,23]
var newNums = nums.filter((item,index,array)=>{
    return item % 2 === 0
})
console.log(newNums);
//[ 2, 4, 8, 12 ]
```

#### map映射

```js
//高阶函数map映射的使用
//map：映射
var newNums2 = nums.map((item)=>{
    return item % 2 === 0 ? '偶数是女生' : '基数是男生'
})
console.log(newNums2);
//[ '偶数是女生', '偶数是女生', '基数是男生', '偶数是女生', '偶数是女生', '基数是男生', '基数是男生' ]
```

#### forEech：迭代

```js
//forEech：迭代，没有返回值，通常就用来打印一些东西
var nums = [2,4,5,8,12,45,23]
nums.forEach((item)=>{
    console.log(item);
})
// 2
// 4
// 5
// 8
// 12
// 45
// 23
```

#### find:查找

```js
//find:查找的意思,有返回值
var nums = [2,4,5,8,"小余",12,45,23]

var item = nums.find((item)=>{
    return  item === "小余"
})
console.log(item);
//小余
------
var item = nums.find((item)=>{
    return  item === "小余不见了"
})
console.log(item);
//undefined
------
var friend = [
    {name:"小余",age:18},
    {name:"大余",age:20},
    {name:"小满",age:23},
    {name:"喜多川",age:22},
    {name:"老鱼皮",age:23}
]

const findFriend = friend.find((item)=>{
    return item.name = "小余"
})
console.log(findFriend);
//{ name: '小余', age: 18 }

//findIndex,找到对象在数组在对象中对应的索引值
const findFriend = friend.findIndex((item)=>{
    return item.name === "小余"
})
console.log(findFriend);
//0
```

#### reduce：累加

```js
//reduce：对我们原来的数组进行一些累加或者统计的操作

//普通实现方式
var nums = [2,4,5,8,12,45,23]
var total = 0
for(var i = 0;i<nums.length;i++){
    total += nums[i]
}
console.log(total);
//99
--------
//高阶函数reduce的使用
//reduce接收参数,第一个参数:上一个函数的返回值(例如我们数组中有7个数字,那就调用7次函数,第一个参数每次都调用上一次的内容)
//那第一次调用的时候没有上一个函数怎么办?我们可以在回调函数后面定义初始化的值,例如0
//prevValue(上一次的值):0 , item:2  prevValue是previousValue的简写
//prevValue(上一次的值):2 , item:4
//不停的将上一次的值跟下一次的值做一个处理,直到全部处理结束带着结果进行返回
var num = nums.reduce((preValue,item)=>{
    return  preValue + item
},0)
console.log(num);
//99
```

#### 函数(Function)与方法(Method)的区别

- 一般来说，其实是指同一个东西。

- 函数(Function)：独立的Funtion，称之为一个函数

- 方法(Method)：当我们的一个函数属于某一个对象时，我们称这个函数是这个对象的方法

- 方法更像是定义在一些特殊地方的函数，函数包含得更大

  ```js
  var obj = {
      
      foo:function(){
          
      }
  }
  //这个foo就是一个属于obj对象的方法
  //调用的时候
  obj.foo()
  ```

## 闭包流程

### 闭包定义：

- 闭包定义分为两个：在计算机科学中(**因为闭包不是JavaScript特有的，在其他语言中也是有的)**和在JavaScript中
- 在计算机科学中队闭包的定义：
  - 闭包(Closure)，又称**词法闭包**(Lexical Closure)或者**函数闭包**(function closures)
  - 是在支持**头等函数**的变成语言中，实现**词法绑定**的一种技术
    - 头等函数是指在程序设计语言中，函数被当作一等公民。这意味着，函数可以作为别的函数的参数、函数的返回值，赋值给变量或存储在数据结构中
    - 解析函数的时候，就会确定它的上层作用域，这是在词法解析的时候进行确定的
  - 闭包在实现上是一个**结构体**，它存储了一个**函数**和一个**关联的环境(关联的自由变量)**(相当于一个符号查找表)
    - 这个结构体在C语言中就是指一个结构
    - 但在JavaScript中，它其实是指一个对象，对象里面存储着一个函数和一个关联环境(想表达是一个整体)
  - 闭包跟函数最大的区别在于，当捕捉闭包的时候，它的**自由变量**会在捕捉时被确定，这样即时脱离了捕捉时的上下文，它也能照常运行(**闭包核心观念**)
    - 自由变量：假如在全局中定义了变量a，在函数中使用了这个a，这个a就是自由变量，可以这样理解，凡是跨了自己的作用域的变量都叫自由变量。
    - 脱离捕捉的上下文：在你函数的上下文之外的地方调用，你脱离了这个作用域范围能够调用，证明了本来该被销毁的自由变量却得以保存
- 闭包的概念最早出现于60年代，最早实现闭包的程序是Scheme的，那么我们就可以理解为什么JavaScript中有闭包：
  - 因为JavaScript中有大量的设计来源于Scheme的。(Scheme是最早实现闭包的语言)
- MDN对JavaScript闭包的解释：
  - 一个函数以及其捆绑的周边环境状态（**lexical environment**，**词法环境**）的引用捆绑在一起(或者说函数被引用包围)，这样的组合就是**闭包**。
  - 换而言之，闭包让开发者可以从内部函数访问外部函数的作用域。
  - 在 JavaScript 中，每当创建一个函数，闭包会随着函数的创建而被同时创建
    - 概括就是有函数就有闭包
    - 之所以会有函数就有闭包是因为，当函数被创建出来的时候，定义在最外层，它的上层作用域就是全局作用域，如果在函数内引用了全局作用域的内容，那也是形成了一个闭包
- **理解总结：**
  - 一个普通的函数function，如果它可以访问外层作用域的自由变量，那么这个函数就是一个闭包
  - 从广义的角度来说，JavaScript的函数都是闭包
  - 从狭义的角度来说，JavaScript中一个函数，如果访问了外层作用域的变量，那么它是一个闭包


### 高阶函数执行过程

```js
function foo(){
    //预解析，前面有讲过
    function bar(){
        console.log("小余");
    }
    return bar
}

var fn = foo()

fn()
//小余
```

#### 流程图

> 一旦我们想要调用函数，它会在执行栈里面创建一个函数的执行上下文
>
> - 这个时候不会马上调用函数执行的上下文，会先创建一个AO对象(函数执行之前创建)
> - 为什么不每个函数都创建AO对象呢？因为如果你如果每个都创建，当数量一多，就会创建很多个AO对象出来，当你都放着不调用，那岂不是就很浪费，所以设置当我们即将调用的前一刻会将AO对象创建出来，这样每个创建出来的AO对象都会被用上

![image-20221216125858055](.\JavaScript高级_image\image-20221216125858055.png)

1. AO对象里面有一个bar，也就是我们刚刚上面代码块中的bar，在foo函数里面进行了return
2. 这个bar存放的其实只是一个地址，原本全局对象GO(global object)里面的fn是underfined，现在变成bar的内存地址(类似`0Xb00`之类的东西)了
3. 执行完之后，执行上下文会销毁掉
4. 然后我们执行了fn()，此时我们应该注意到fn里面的内容其实已经是bar的内存地址了，所以我们执行的时候fn其实是通过bar的内存地址去进行指针指向执行
5. 然后指向的对应ECStack调用栈的全局执行上下文又会创建出来一个函数执行上下文进行执行内容，执行完之后就会把这个函数执行上下文进行一个销毁
6. 然后fn()就会打印出bar中的内容

### 闭包到底是什么

> 1. 从var fn = foo()开始，这个时候在GO对象中，fn还是一个undefined
> 2. 一样的，在执行foo的时候，会先创建出来一个foo的AO执行对象 => 里面有一个name为undefined跟一个预解析的bar函数，bar函数里面存放的是函数指针的一个引用(指向了bar函数创建出来的函数对象0xb00地址，0xb00是一个举例，不一定就是这个)
> 3. 下一刻中将name中的内容填入，取代undefined，然后就是function bar(){xxx}并不执行，而是直接跳到return bar中，这里return返回的bar其实就是0xb00地址，所以在fn = foo()的fn就能拿到你返回的0xb00地址(fn = 0xb00)。这个时候foo函数内的东西就都执行结束了，那这个对应的函数执行上下文就会销毁掉
> 4. 在GO对象中的fn也会对应的替换成bar的指针地址0xb00
> 5. 最后执行fn()，这个又是一个函数的执行，这个时候我们又会创建出来一个函数的执行上下文，但是这次的函数执行上下文，其实就是bar的执行上下文，在第3点中我们已经能感受到替换成bar的过程了。创建bar的AO对象，然后有创建对应的执行上下文，首先里面是VO，VO对应的是AO，接着执行里面的内容，一个控制台打印命令，"小余"是字符串，能够直接被打印出来，但是，这个时候，里面引用了一个name，这个时候name应该要沿着作用域链去查找(VO+parentScope)，VO里面没有找到，在父级foo对象中找到了name，foo对象在定义的时候就已经确定了。我们在bar函数对象0xb00中除了包含了代码执行体之外，还包含了parentScope：foo的AO对象(就是上面**闭包定义中说的词法解析的时候**)，所以能够打印出来name的内容
>
> - 当我们在调用fn函数的时候，就**已经形成闭包**了，因为我们在var fn = foo()执行的时候，foo函数就已经执行完了，然后return返回了bar这个内容，按道理来说，这个时候name就需要随着foo的函数执行上下文销毁掉了，但我们根据结果却依旧能够进行访问到name。这就是js内部帮我们实现的功能
> - 结论：
>   - 闭包是两部分组成的，函数+可以访问的自由变量(bar本身加上它内部引用的自由变量形成闭包)

```js
function foo(){
    var name = "小满不穿裤子"
    function bar(){
        console.log("小余",name);
    }
    return bar
}

var fn = foo()

fn()
//小余 小满不穿裤子
```

```js
//可以访问name：test算闭包
//有访问到：test不算闭包
var name = "放寒假了"
function test(){
    console.log(name);
}
test()
```

> 补充：执行上下文跟作用域的区别：
>
> 当我们要执行函数的时候，就会创建出来一个环境，环境叫做执行上下文，执行上下文有我们的作用域还有作用域链

### 函数的执行过程的内存

<img src=".\JavaScript高级_image\image-20221217113842954.png" style="zoom:67%;" />

---

foo的执行上下文销毁前后对比：

<img src=".\JavaScript高级_image\image-20221217114703886.png" alt="image-20221217114703886" style="zoom:67%;" />

<img src=".\JavaScript高级_image\image-20221217114821548.png" alt="image-20221217114821548" style="zoom:67%;" />

> 1. 我们写了foo函数跟test函数，从foo()开始执行，这个时候会先创建出foo函数的函数对象(0xa00`内存地址`)，然后函数对象里面包括了parentScope`父级作用域`跟函数执行体。
> 2. 然后foo函数这个父级作用域parentScope在下面的代码块中指GO(0x100`内存地址`)，没错，parentScope是指向一个内存地址(根据上图，我们能知道他们其实是一个互相引用的关系)。test函数 同理
> 3. 然后foo执行的时候同理的创建出来对应的函数执行上下文，在执行上下文中，我们知道VO其实就是指AO，存放的AO其实也是内存地址，会对应的去进行引用，接着按顺序将name跟age进行了一次输出，覆盖掉了AO对象中name、age原本默认输出的undefined。输出完了内容之后，一样的会销毁掉执行上下文VO

```js
function foo(){
    var name = "xiaoyu"
    var age = 20
}

function test(){
    console.log("test");
}

foo()
test()
```

## 闭包的执行过程

> 以下是我们已经非常熟悉的闭包过程，这次我们来看下他是怎么进行执行的，这次会解开我们之前还不了解的，为什么闭包会让本该执行完的执行上下文的自由变量不会被销毁掉

**执行之前**一样是非常熟悉的流程，直接上图啦

<img src=".\JavaScript高级_image\image-20221217120650694.png" alt="image-20221217120650694" style="zoom:67%;" />

**当foo开始执行之后**：

<img src=".\JavaScript高级_image\image-20221217120850640.png" alt="image-20221217120850640" style="zoom:67%;" />

> **当foo执行完了之后**：这个时候，bar的内存地址已经存放到fn中了(也就是fn已经指向bar了)，并且在后续被fn()给调用了，所以不管foo的函数执行上下文有没有被销毁，都不会影响到bar的函数对象了(因为GO根对象的fn已经指向了bar函数对象了`上面有介绍JavaScript的垃圾回收，也就是标记清除部分`，让bar函数对象不被销毁)，然后bar函数对象连锁反应又跟foo的AO对象相互进行引用了(最关键的是bar指向foo的AO对象，这是可达的部分)，所以foo的AO对象也不会被销毁。这就是为什么bar引用的父级自由变量会得以保留的原因

<img src=".\JavaScript高级_image\image-20221217121318244.png" alt="image-20221217121318244" style="zoom:67%;" />

> 我们接下来就要继续执行fn的函数执行上下文(bar的)了

<img src=".\JavaScript高级_image\image-20221217123311693.png" alt="image-20221217123311693" style="zoom:67%;" />

<img src=".\JavaScript高级_image\image-20221217123413878.png" alt="image-20221217123413878" style="zoom:67%;" />

> 当bar的执行上下文被销毁掉的时候，也不会影响闭包，因为根对象依旧指向着fn，也就是bar的函数对象，而bar函数对象的父级作用域parentScope指着foo的AO对象，所以脱离了捕捉时的上下文，它也能照常运行。自由变量依旧存在而没有被销毁

<img src=".\JavaScript高级_image\image-20221217123507832.png" alt="image-20221217123507832" style="zoom:67%;" />

```js
function foo(){
    var name = "xiaoyu"
    var age = 20

    function test(){
        console.log("这是我的名字",name);
        console.log("这是我的年龄",age);
    }

    return test
}


var fn = foo()
fn()
//这是我的名字 xiaoyu
//这是我的年龄 20
```

# 04_函数执行作用域链和深入闭包

### 闭包的内存泄漏

> 从上面的代码块中，我们可以知道，当bar函数不被销毁的时候，foo的AO对象就永远不会被销毁，因为我们bar要访问foo的AO对象里面的内容
>
> - 目前因为在全局作用域下fn变量对0xb00的函数对象有引用，而0xb00的作用域中AO(0x200)有引用，所以会造成这些内存都是无法被释放的
>
> 但如果我们的bar函数只执行一次，后面就再也不需要了，那这个AO对象一直保存着就没有意义了，该销毁的却一直保留着，我们就叫这个是内存泄漏

#### 闭包内存泄漏案例

<img src=".\JavaScript高级_image\image-20221218092955343.png" alt="image-20221218092955343" style="zoom:67%;" />

<img src=".\JavaScript高级_image\image-20221218094436686.png" alt="image-20221218094436686" style="zoom:50%;" />

> - 只要arrayFns数组不被销毁，则createFnArray函数也会一直保留着不被销毁

V8引擎源码可以看到对数字的处理：(是在后面回顾的时候进行补充说明的)

![image-20221221221349656](.\JavaScript高级_image\image-20221221221349656.png)

```js
function createFnArray(){
    // 创建一个长度为1024*1024的数组，往里面每个位置填充1.观察占了多少的内存空间(int类型，整数1占4个字节byte)
    //4byte*1024=4kb，再*1024为4mb，占据的空间是4M × 100 + 其他的内存 = 400M+
    //在js里面不管是整数类型还是浮点数类型，看起来都是数字类型，这个时候占据的都是8字节，但是js引擎为了提高空间的利用率，对很多小的数字是用不到8个字节(byte)的，8字节 = 2的64次方，所以8字节是很大的，现在的js引擎大多数都会进行优化，对小的数字类型，在V8中称为Smi，小数字 2的32次方
    var arr = new Array(1024*1024).fill(1)
    
    return function(){
        console.log(arr.length);
    }
}

//var arrayFn = createFnArray()

//arrayFn()
var arrayFns = []
for(var i = 0 ; i<100 ; i++){
    //createFnArray()//我们通过for循环不断调用createFnArray这个函数，我们没有使用任何函数去接收他，所以当他创建进入下一个循环之后就会马上被销毁掉
    arrayFns.push(createFnArray())
}
```

![image-20221218095602579](.\JavaScript高级_image\image-20221218095602579.png)

#### 内存泄漏解决方法

<img src=".\JavaScript高级_image\image-20221217124919230.png" alt="image-20221217124919230" style="zoom:67%;" />

```js
//内存泄漏解决方法
function foo(){
    var name = "xiaoyu"
    var age = 20

    function test(){
        console.log("这是我的名字",name);
        console.log("这是我的年龄",age);
    }

    return test
}


var fn = foo()
fn()

fn = null//将fn指向null，null的内存地址为0x0。此时fn指向bar的指针就会断开了，AO对象跟bar函数对象就形成了一个对于根对象的不可达的对象，将再下次被销毁掉。注意，你把它置为null之后，不会马上回收的，会在发现之后的下一轮进行回收
```

### AO不使用的属性

- 我们来研究一个问题：AO对象不会被销毁时，是否里面的所有属性都不会被释放?
  - 下面代码中的name属于闭包的父作用域里面的变量
  - 我们知道形成闭包之后count一定不会被销毁掉，那么name是否会被销毁掉呢？会，没有被使用到的会销毁掉，`V8引擎做的优化`

```js
function makeAdder(count){
    let name ="why"	
    return function (num){
        debugger
        return count + num
    }
}

const add10 = makeAdder(10)
console.log(add10(5));
console.log(add10(8));
//15
//18
```

#### 闭包的内存泄漏测试

> 内存回收案例测试如下

<img src=".\JavaScript高级_image\image-20221217135007121.png" alt="image-20221217135007121" style="zoom:50%;" />

> 如果我们连foo函数对象都不想要了，我们也来个foo = null，断掉了foo与根对象GO的联系，那下次foo函数也会被销毁，或者说垃圾回收掉
>
> <img src=".\JavaScript高级_image\image-20221218135505633.png" alt="image-20221218135505633" style="zoom:67%;" />回收一半的内存

## JS闭包引用的自由变量销毁

> 当我们除了声明了fn来接收foo()之外，又声明了baz同样子接收foo()，这个时候是又执行了一遍foo函数里面的bar部分，fn跟baz不是同时指向同一个地方，而是又创建了一个新的foo的AO对象跟bar的函数对象，当我们将fn指向null，将内存进行回收时的时候，销毁的也只是fn对应的bar函数对象跟foo()对象，而对baz产生的bar函数对象跟foo的AO对象没有任何的影响，毕竟baz是又重新走了一遍流程，baz跟fn是互相独立的(PS：foo的AO对象是由bar的父级作用域内存地址指向而产生出来的)

<img src=".\JavaScript高级_image\image-20221218195427320.png" alt="image-20221218195427320" style="zoom:67%;" />

> 1. foo的AO对象有bar在指向着，因为bar函数内含父级作用域foo的AO对象的内存地址且正处于引用状态，这个内存地址指向着AO对象，让AO对象不会被销毁掉，但是我们只是引用name这个自由变量，age并没有使用到，按照ECMA规范，正规AO对象都不会被销毁，当然也就包含了我们没有用上的age变量了
> 2. 但是js引擎是非常灵活的，为了提高内存的利用率，这个可能永远使用不上的age属性是会被回收掉的，从而提高空余的内存空间，提高性能

<img src=".\JavaScript高级_image\image-20221218214558472.png" alt="image-20221218214558472" style="zoom:50%;" />

### 闭包引用的AO对象属性销毁

> 通过debugger我们可以看到未使用的父级作用域的变量会被js引擎回收掉，如果引用了则不会

```js
function foo(){
    var name = "why"
    var age = 18

    function bar(){
        debugger
        console.log(name)
    }
    return bar
    
}
var fn = foo()
fn()
```

<img src=".\JavaScript高级_image\image-20221218220313176.png" alt="image-20221218220313176" style="zoom:67%;" />

## JS中函数的this指向

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

结果如下：![image-20221220134952932](.\JavaScript高级_image\image-20221220134952932.png)

# 05_this的绑定规则、优先级和面试

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

![image-20221220212753721](.\JavaScript高级_image\image-20221220212753721.png)

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

![image-20221220224644327](.\JavaScript高级_image\image-20221220224644327.png)

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

![image-20221220230844569](.\JavaScript高级_image\image-20221220230844569.png)

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

![image-20221220232552486](.\JavaScript高级_image\image-20221220232552486.png)

> 那此时这个this是绑定到了谁身上，我们通过结果可以看到是obj2的身上，首先我们bar是调用到了obj1中的foo函数身上，但是最后我们执行的时候，是通过obj2来进行执行的，所以obj2就被绑定到了foo函数里面去了，所以此时foo函数控制台打印this的结果才会是obj2里的内容

![image-20221220233110245](.\JavaScript高级_image\image-20221220233110245.png)

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

  ![image-20221221024716057](.\JavaScript高级_image\image-20221221024716057.png)

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

调用区别：![image-20221221114414556](.\JavaScript高级_image\image-20221221114414556.png)

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

![image-20221222131325900](.\JavaScript高级_image\image-20221222131325900.png)

### 监听点击

> css样式不写，自己写一个宽高加背景颜色出来方便点击
>
> 我们监听点击中的this给到我们的是监听的对象，也就是如下的东西(div的元素对象)：
>
> ![image-20221222183921708](.\JavaScript高级_image\image-20221222183921708.png)
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

![image-20221222204947490](.\JavaScript高级_image\image-20221222204947490.png)

**forEach加第二个参数：**

![image-20221222205023654](.\JavaScript高级_image\image-20221222205023654.png)

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

![image-20221222231000791](.\JavaScript高级_image\image-20221222231000791.png)

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

       ![image-20221223000646446](.\JavaScript高级_image\image-20221223000646446.png)

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

# _06函数的柯里化

## 实现apply、call、bind

- 实现apply、call、bind函数
  - 注意：我们的实现是练习函数、this、调用关系，不会过度考虑一些边界情况 

### call函数的手写实现

> 给函数上面添加一个叫做hycall方法的方式：
>
> 1. 只能给某个函数添加方法
>    - foo.hycall = function(){}
> 2. 给所有的函数添加一个hycall的方法
>    - Funtion.prototype.hycall = function(){}

```JavaScript
Function.prototype.hycall = function(){
    console.log("原型链调用了");
}

function foo(){
    console.log("foo函数调用了");
}

foo.hycall()//原型链调用了
----------------------------------------------------------
//我们在执行foo.hycall的时候会发现只执行了hycall函数，但是却把原本foo函数自带的信息给掩盖掉了，这肯定是不合理的，我们是要在原有的基础上进行调用，而不是另起大厦
```

**改良写法**

```JavaScript
//差的写法，缺乏复用性
Function.prototype.hycall = function(){
    console.log("原型链调用了");
    foo()
}

function foo(){
    console.log("foo函数调用了");
}

foo.hycall()
//好的写法，可以多次复用
//本身我们在进行调用的时候，就相当于已经是隐式绑定了，foo.hycall()的时候，this的绑定就已经绑到foo上面了
Function.prototype.hycall = function(){
    console.log("原型链调用了");
    var fn = this
    fn()
}

function foo(){
    console.log("foo函数调用了");
}

foo.hycall()
```

#### 怎么实现给函数传递参数

> 我们往call函数内传递参数，会在调用的this中打印出来，但是在我们自己手写的函数里面，打印出来的却是windows，如何解决这个问题？

```javascript
Function.prototype.hycall = function(thisArg){
    var fn = this

    //调用需要被执行的函数
    thisArg.fn = fn
    thisArg.fn()
    //等函数执行完之后删掉这个属性
    delete thisArg.fn
}

function foo(){
    console.log(this);
}

foo.hycall({name:"小余",age:"20"})//{ name: '小余', age: '20', fn: [Function: foo] }
```

> 此时手写的函数已经可以传递参数了，但能传递的仅仅是对象，如果传递数字会报错，接下来我们就解决这个问题
>
> ```javascript
> //使用call函数传递数字参数的话
> foo.call(123)//[Number: 123]
> ```

```javascript
//对thisArg转成对象类型(防止传入非对象类型报错)
thisArg = thisArg ? Object(thisArg) : window
```

> call除了第一个参数是用来改变this绑定的，后面还可以绑定一堆参数，这个放到我们自己实现要怎么去做呢？在之前是可以使用argument，现在ES6中有更好的解决方法了

### ES6中的剩余参数

> 对于接收多少个参数我们是不确定的，那这个时候，我们形参的部分就不能够写死了，不然是写不尽写不完的，你也不能够100%猜到用户想输入几个参数

```javascript
function sum(...args){
    //打印出来的args是数组形式 
    console.log(args)
    //展开运算符spread
    console.log(...args)//得到的直接是数值
}
```

> 很显然通过ES6中的...的方式来解决，接着进到最后的一步，返回结果

```javascript
Function.prototype.hycall = function(thisArg,...args){
    // console.log("传递参数进来了噢",this);
    var fn = this

    //对thisArg转成对象类型(防止传入非对象类型报错)
    thisArg = thisArg ? Object(thisArg) : window
    //调用需要被执行的函数
    thisArg.fn = fn
    var result = thisArg.fn(...args)
    //等函数执行完之后删掉这个属性
    delete thisArg.fn
	//返回结果
    return result
}

function foo(){
    console.log(this);
}

foo.hycall(123,6,66,666,6666)
//==========================================>换个函数执行一下
function foo(num1,num2,num3){
    console.log("foo的this指向是",this,"三数相加的结果=",num1+num2+num3);
}

foo.mycall("小余",500,20,1)
//foo的this指向是 String {'小余', fn: ƒ} 三数相加的结果= 521
```

### apply函数的手写实现

```javascript
//自己实现hyapply
Function.prototype.myapply = function(thisArgs,argArray){//区别在于这里不需要ES6的...运算，因为传入的是一整个数组
    var fn = this
    thisArgs = thisArgs ? Object(thisArgs) : window
    thisArgs.fn = fn
    var arr = thisArgs.fn(...argArray)//但是数组需要解构出来
    delete thisArgs.fn
    return arr
}

function sum(num1,num2){
    console.log("sum被调用",this,num1,num2);
}

var result = sum.myapply("小余",[200,30])
console.log(result,'老铁666');
```

> 在call函数的基础上进行小范围修改，貌似已经满足了apply函数的要求了，但是当我们除了第一个用于this指定之外，其他参数我们不传就会出现问题了
>
> - 因为当我们不传递参数的时候，argArray就会是undefined，我们在解构的时候就会变成...undefined了，对underfunded进行扩展是错误的
> - 这个时候我们就可以进行一个判断来解决这个问题
> - 那为什么在call函数的手写的时候没有遇到这个问题呢？因为我们在call函数中的形参是...args，那这东西的格式就是个数组，你什么都不传都默认是空数组[]

```javascript
Function.prototype.myapply = function(thisArgs,argArray){
    var fn = this
    thisArgs = thisArgs ? Object(thisArgs) : window
    thisArgs.fn = fn
	argArray = argArray || []//不止这种写法，也可以使用三元运算符
    var arr = thisArgs.fn(...argArray)
    delete thisArgs.fn
    return arr
}

function sum(num1,num2,num3){
    console.log("sum被调用",this,num1,num2+num3);
}

var result = sum.myapply("小余")
console.log(result,'老铁666');
```

#### 手写call和apply的补充

> 在我们改变this指向的时候，输入0的时候，会指向window，这是由于我们三元运算符那些的写法导致的，也有其他情况可以避免，这样是对边界效应的一种延伸考虑

```javascript
 thisArg = (thisArg !== undefined && thisArg !== null) ? Object(thisArg) : window
```

### bind函数的手写实现

> 首先我们来看看JS中bind传参数的3种方式，第一种传值方式跟call的方式很像，从第二种跟第三种开始，跟其他两个函数(call、apply)发生了不一样的变化，我们来看看他们是怎么实现的吧

```javascript
//方式1：在bind中传值
function foo(num1,num2,num3,num4){
    console.log(this,num1,num2,num3,num4);
}

var bar = foo.bind('小余',10,20,30,40)
bar()
//方式2：在接收bind的bar中传值
function foo(num1,num2,num3,num4){
    console.log(this,num1,num2,num3,num4);
}

var bar = foo.bind('小余')
bar(10,20,30,40)
//方式3：方式1跟方式2的结合，从方式1到方式2中按顺序传递
function foo(num1,num2,num3,num4){
    console.log(this,num1,num2,num3,num4);
}

var bar = foo.bind('小余',10,20)
bar(30,40)

//以上三种方式的答案都是：String {'小余'} 10 20 30 40
```

> 我们是有变量接收值的，就像上方的bar来接收，对于这种情况就需要在手写的函数中最后进行一个return返回

```javascript
Function.prototype.mybind = function(thisArg,...argArray){
    function proxyFn(){

    }

    return proxyFn
}
```

> 首先我们揭秘第二种方式是怎么做到的，也就是得到了值还能继续往里面传值，那是因为我们手写bind函数返回的还是一个函数，而里面这个函数是可以接收值的，所有自然就造成这种情况了，第三种则是手写bind函数跟bind函数内部的函数都可以接收值，然后再做一个拼接就完成了

```javascript
Function.prototype.mybind = function(thisArg,...argArray){
    //1.获取真实要调取的函数
    var fn = this
    //对特殊情况的处理
    thisArg = (thisArg !== undefined && thisArg !== null) ? Object(thisArg) : window
    function proxyFn(...args){
        //将函数放到thisArg中进行调用
        thisArg.fn = fn
        //对传入的两个参数进行合并
        var finalArgs = [...argArray,...args]
        var result = thisArg.fn(finalArgs)
        delete thisArg.fn
        //返回结果
        return result
    }

    return proxyFn
}


function foo(num1,num2,num3,num4){
    console.log(this,num1,num2,num3,num4);
}

var bar = foo.bind('小余',10,20)
bar(30,80)
```

## 认识arguments 

- **argument**是一个对应于 **传递给函数的参数** 的 **类数组(array-like)对象**
  - 类数组对象什么意思？就是长得像数组，但实际上是个对象
  - 我们在实参传递的个数如果超过形参的数量的话，多余的不是丢弃掉，而是跟着前面其他几个参数一起放到argument中了
- array-like意味着它不是一个数组类型，而是一个对象类型：
  - 但是它却拥有数组的一些特性，比如说length，比如说可以通过index索引来访问
  - 但是它却没有数组的一些方法，比如forEach、map等等

```javascript
//argument的基础使用
function foo(num1,num2,num3){
    // console.log(arguments);

    //常见对argument的3个操作
    //1.获取参数长度
    console.log(arguments.length);
    //2。根据索引值获取某一个参数，像数组一样的操作
    console.log(arguments[1]);
    //3.callee属性，获取argument中所在的函数
    console.log(arguments.callee);
}   

foo(10,20,30,40,50)
```

### argument转数组

```javascript
function foo(num1,num2,num3,num4){
    //1.自己遍历
    var newArr = []
    for(var i = 0;i < arguments.length;i++){
        newArr.push(arguments[i] *40)
    }
    console.log(newArr);
    //2.arguments转成array数组类型
    //2.1自己遍历arguments中所有的元素
    //2.2 使用slice
    var newArr2 = Array.prototype.slice.call(arguments)
    console.log(newArr2,"这是newArr2");
    //这里其实跟2.2是一样的，this显示绑定arguments大于隐式绑定
    var newArr3 = [].slice.call(arguments)
    console.log(newArr3)
    //2.3 ES6的语法
    var newArr4 = Array.from(arguments)
    console.log(newArr4)
    //展开运算符
    var newArr5 = [...arguments]
    console.log(newArr5)
}


foo(1,2,3,4)
```

### 数组里的slice实现

```javascript
//补充：在原型链上加函数的方法，我们确实可以在每个函数上面都调用，但是当我们想要调用这个函数的方法本身的时候，就略显麻烦
Function.prototype.aaa = function(){
    //xxxx
}
Function.prototype.aaa()//这样调用
```

```javascript
//自己简单实现的slice
Array.prototype.hyslice = function(start,end){//这里可以进行优化，看用户是否有传递进来这两个参数，没有的话我们就做一个判断处理
    var arr = this
    start = start || 0
    end = end || arr.length
    var newArray = []
    for(var i = start;i < end ;i++){
        newArray.push(arr[i])//就将this里的东西给填入进去，而this已经被我们手动改成我们想要的指向了
    }
    return newArray
}
var newArray = Array.prototype.hyslice.call(["小余","大余","超大余"],1,3)//相当于使用call来调用slice，我们上方函数里的this指向就被改变到我们手写的这个数组内容上了
console.log(newArray);
```

#### 箭头函数-无arguments

> 我们在箭头函数中是没有arguments的，如果你需要的话，js会去上层作用域里面找
>
> 向下方这个，arguments到上层作用域，也就是全局作用域中寻找，这全局作用域是分两种情况的，在node中是有的，在浏览器中则是没有(显示你没有定义)

```javascript
var name = "小余"

var foo = ()=>{
    console.log(name)
    console.log(arguments);
}

foo()
```

**案例**

> 在ES6中用...剩余函数来替代arguments，用来接收所有参数，形成数组

```javascript
function foo(){
    var bar = ()=>{
        console.log(arguments);//打印出来的就是上层foo的arguments
    }
    return bar
}

var fn = foo(123)
fn()
```

# 07对象字面量和对象的封装

## 理解JavaScript纯函数(Pure Function )

- **函数式编程**中有一个非常重要的概念叫**纯函数**，JavaScript符合**函数式编程的范式**，所以**也有纯函数的概念**
  - 在react开发中，纯函数被多次提及
  - 比如**react中组件就被要求像是一个纯函数**(为什么是像，因为还有class组件)，**redux中有一个reducer的概念**，也是要求必须是纯函数
  - 所以**掌握纯函数对理解很多框架的设计**是非常有帮助的
- 纯函数的维基百科定义：
  - 在程序设计中，若一个函数**符合以下条件**，那么这个函数就被称为纯函数
  - 此函数**在相同的输入值时**，需**产生相同的输出**
  - 函数的**输出和输入值以外的其他隐藏信息或状态无关**，也和**由I/O设备产生的外部输出**无关
  - 该函数**不能有语义上可观察的函数副作用**，诸如**"触发事件"，使输出设备输出，或更改输出值以外物件的内容**等
- **总结：**
  - 确定的输入，一定会产生确定的输出
  - 函数在执行过程中，不能产生副作用

### 副作用的理解

- **这里有一个概念，叫做副作用**，什么叫做**副作用**呢？
  - 副作用(side effect)其实本身是医学的一个概念，比如我们经常说吃什么药本来是为了治病，可能会产生一些其他的副作用
  - 在计算机科学中，也引用了副作用的概念，表示在**执行一个函数**时，除了**返回函数值**以外，还对**调用函数产生了附加的影响**，比如**修改了全局变量，修改参数或者改变外部的存储**
- **纯函数在执行的过程中就是不能产生这样的副作用：**
  - 副作用是产生bug的温床

### 纯函数的案例

- 我们来看一个对数组操作的两个函数：
  - slice：slice截取数组时不会对原数组进行任何操作，而是生成一个新的数组
  - splice：splice截取数组，会返回一个新的数组，也会对原数组进行修改
  - slice就是一个纯函数，不会修改传入的参数

### 纯函数-柯里化-组合

> 1. slice函数只要是确定的输入，就会产生确定的输出
> 2. slice在执行的时候，不会产生副作用(没有修改外部的变量，也没有修改传入的参数)

```javascript
var names = ["小余",'小满','骚满','Tom']

//slice只要给它传入一个start/end，那么对于同一个数组来说，它会给我们返回确定的值
//slice函数本身是不会修改原来的数组
//slice -> this
var newNames1 = names.slice(0,2)
console.log("newNames1",newNames1);
console.log("names",names);

//splice是会修改原来的数组对象本身的，所以它不是纯函数
var newNames2 = names.splice(2)
console.log("newNames2",newNames2);
console.log("names",names);
```

- 纯函数练习

```javascript
//非纯函数，传入的值被修改了
function baz(info){
    info.age = 100
}

var obj = {name:"小满",age:23}
baz(obj)
console.log(obj)
//{name: '小满', age: 100}

//test是否是一个纯函数？是
function test(info){
    return{
        ...info,
        age:100
    }
}
test(obj )

//React的函数组件(类组件)
function HelloWorld(props){

}

<HelloWorld info="{}"/>
```

### 纯函数的优势

- **为什么纯函数在函数式编程中非常重要呢？**
  - 因为你可以安心的编写和安心的使用
  - 你在**写的时候**保证函数的纯度，只是**单纯实现自己的业务逻辑**即可，**不需要关心传入的内容**是如何获得的或者**依赖其他的外部变量**是否已经发生了修改
  - 你在**用的时候**，你确定**你的输入内容不会被任意篡改**，并且**自己确定的输入，一定有确定的输出**
- **React中就要求我们无论是函数还是class声明一个组件，这个组件都必须像纯函数一样，保护它们的orops不被修改：**
  - <img src=".\JavaScript高级_image\image-20230103002841805.png" alt="image-20230103002841805" style="zoom:50%;" />

## JavaScript柯里化

- **柯里化**也是属于**函数式编程**里面一个非常重要的概念
- 维基百科解释：
  - 在计算机科学中，**柯里化**(英语：Currying)，又译为卡瑞化或加里化
  - 是把接收**多个参数的函数**，变成**接受一个单一参数**(最初函数的第一个参数)的函数，并且**返回接受余下的参数**，而且**返回结果的新函数**的技术
  - 柯里化声称"**如果你固定某些参数，你将得到接受余下参数的一个函数**"
- **柯里化总结**：
  - 只传递给函数一部分参数来调用它，让它返回另一个函数处理剩下的参数
  - 这个过程称为柯里化

```JavaScript
//假设我们有一个需要填入4个参数的 函数
function foo(m,n,x,y){
    
}
foo(10,20,30,40)
//柯里化的过程
//我们对其进行转化，变得只需要传入一个参数，但这里面需要返回一个函数继续处理剩下的参数
function bar(m){
    return function(n){
        return function(x,y){
            //你也可以将y参数继续return
            m+n+x+y
        }
    }
}
bar(10)(20)(30,40)
```

### 柯里化的结构

```JavaScript
//正常结构
function add(x,y,z){
    return x+y+z
}

var result = add(10,20,30)
console.log(result);

//柯里化
function sum(x){
    return function(y){
        return function(z){
            return x+y+z
        }
    }
}

var result1 = sum(10)(20)(30)
console.log(result1);

//简化柯里化代码
var sum2 = x=>y=>z=>{
    return x+y+z 
}
//还能再次简化var sum2 = x=>y=>z=>x+y+z
var result2 = sum2(20)(30)(40)
console.log(result2,"使用箭头函数简化柯里化的方式")
```

### 柯里化的作用

- **那么为什么需要有柯里化呢？**
  - 在函数式编程中，我们其实往往希望**一个函数处理的问题尽可能的单一**，而**不是将一大堆的处理过程交给一个函数来处理**
  - 那么**我们是否就可以将每次传入的参数在单一的函数中进行处理**，处理完后在**下一个函数中再使用处理后的结果**

```javascript
单一职责原则(SRP)
面向对象 -> 类 -> 尽量只完成一件单一的事情
```

#### 柯里化 - 单一职责的原则

```javascript
//全部挤在一起处理
function add(x,y,z){
    x = x + 2
    y = y * 2
    z = z * z
    return x + y +z
}

console.log(add(10,20,30));
//柯里化处理
function sum(x){
    x = x + 2
    return function(y){
        y = y * 2
        return function(z){
            z = z * z
                return x + y + z
        }
    }
}
console.log(sum(10)(20)(30));
```

#### 柯里化 - 逻辑的复用

```javascript
function foo(m,n){
    return m + n
}
console.log(foo(5,1))
console.log(foo(5,2))
console.log(foo(5,3))
console.log(foo(5,4))
console.log(foo(5,5))//第一个数一直都是不变的，但是我们每次都是需要重复输入，使用柯里化就能实现逻辑上的复用了


function makeAdder(count){
    return function(num){
        return count + num
    }
}
var adder5 = makeAdder(5)
console.log(adder5(1));//重复的逻辑就直接复用了
console.log(adder5(2));
console.log(adder5(3));
console.log(adder5(4));
console.log(adder5(5));
```

> 案例2

```javascript
//打印日志时间
function log(date,type,message){
    console.log(`[${date.getHours()}:${date.getMinutes()}][${type}]:[${message}]`)
}
log(new Date(),'DEBUG','查找到轮播图的bug')//[22:24][DEBUG]:[查找到轮播图的bug]
log(new Date(),'DEBUG','查询菜单的bug')//[22:24][DEBUG]:[查询菜单的bug]
log(new Date(),'DEBUG','查询数据的bug')//[22:24][DEBUG]:[查询数据的bug]
---------------------------------------------------------------------------------------------
//柯里化优化
var log = date => type => message =>{
    console.log(`[${date.getHours()}:${date.getMinutes()}][${type}]:[${message}]`)
}
//如果我打印的都是当前的时间，我们就可以将时间复用
var nowLog = log(new Date());
nowLog("DEBUG")("查找小满去哪了")//[22:32][DEBUG]:[查找小满去哪了]
//或者时间＋类型都全部复用
var nowLog1 = log(new Date())("小满系列查找");
nowLog1("查找小满人去哪了")//[22:34][小满系列查找]:[查找小满人去哪了]
nowLog1("查找小满的黑丝去哪了")//[22:34][小满系列查找]:[查找小满的黑丝去哪了]
nowLog1("查找小满的裤衩子被谁拿走了")//[22:34][小满系列查找]:[查找小满的裤衩子被谁拿走了]
nowLog1("查找小满有没有去按摩店找小姐姐")//[22:34][小满系列查找]:[查找小满有没有去按摩店找小姐姐]
```

#### 柯里化函数的实现

> 实现将正常普通的函数转换成柯里化函数，这样要怎么实现呢？
>
> - 传入一个函数，返回一个function
>
> - 想要获取参数的个数方式
>
>   ```javascript
>   function foo(x,y,z,q){
>       console.log(foo.length)//4
>   }
>   foo()
>   ```

```JavaScript
function add1(x, y, z) {
    return x + y + z
}

function hyCurrying(fn){
    function curried(...args){
        //1.当已经传入的参数 大于等于 需要的参数时，就执行函数
        if(args.length >= fn.length){
            //不使用fn(...args)这种方式，可能会发生this指向问题
            return fn.apply(this,args)//如果使用call的话，args就需要加上...
            //原因是apply第二个参数本身就是数组，所以直接args输出，但call函数第二个参数是一个一个的，需要扩展出来
        }else{
            //当出现hyCurrying(10)(20)(30)这种极端情况的时候，我们就需要再返回新的函数来接收参数
            function curried2(...args2){//由于我们不知道要接收多少参数，这里还是需要...
                //接收到参数后，需要递归调用curried来检查函数的个数是否达到
                //将第一个curried参数跟curried2的参数进行拼接
                return curried.apply(this,args.concat(args2))
            }
            return curried2
        }
    }
    return curried
}
var curryAdd = hyCurrying(add1)
console.log(curryAdd(10,20,30));
console.log(curryAdd(10,20)(30));
console.log(curryAdd(10)(20)(30));
//可能在一个里面将参数全部接收hyCurrying(10,20,30)
//也可能分开接收hyCurrying(10,20)(30)
//也可能全部分开hyCurrying(10)(20)(30)
```

### 理解组合函数

- **组合（Compose）函数**是在JavaScript开发过程中一种**对函数的使用技巧、模式：**
  - 比如我们现在需要对某一个数据进行函数的调用，执行两个函数fn1和fn2，这两个函数是依次执行的；
  - 那么如果每次我们都需要进行两个函数的调用，操作上就会显得重复
  - 那么是否可以将这两个函数组合起来，自动依次调用呢？
  - 这个过程就是对函数的组合，我们称之为 组合函数（Compose Function）；

```javascript
function double(num){
    return num*2
}

function square(num){
    return num ** 2//平方
}

var count = 10
var result = square(double(count))
console.log(result);

//如何将double和square结合起来，实现简单的组合函数
function composeFn(m,n){
    return function(count){
        return n(m(count))
    }
}

var newFn = composeFn(double,square)
console.log(newFn(10));
```

### 通用组合函数的实现

- 刚才我们实现的compose函数比较简单，我们需要考虑更加复杂的情况：比如传入了更多的函数，在调用 compose函数时，传入了更多的参数：

```javascript
function hyCompose(...fns){
    var length = fns.length
    for(var i = 0;i < length;i++){
        if(typeof fns[i] !== 'function'){
            throw new TypeError('要求都是函数类型')//new出一个异常的错误，抛出异常
        }
    }

    function compose(...args){
        var index = 0
        //fns[index].apply(this,args)：取出来fns第一个函数进行apply调用，并将args参数都传递进去。注意，我们是直接使用fns而不是...fns哦
        var result = length ? fns[index].apply(this,args) : args
        while(++index < length){
            result = fns[index].call(this,result)
        }
        return result
    }
    return compose
}
function double(m){
    return m*2
}
function square(n){
    return n ** 2
}
var newFn = hyCompose(double,square)
console.log(newFn(30));
```

# 08_基于对象的封装、原型链

## JavaScript额外知识补充

### with语句

- **with语句** 扩展一个语句的作用域链。
- 不建议使用with语句，因为它可能是混淆错误和兼容性问题的根源。

```javascript
var obj = {
    name:"Hello World",
    age:18
}
with(obj){//会形成自己的作用域
    console.log(name)
    console.log(age)
}
```

### eval函数的

- eval是一个特殊的函数，它可以将传入的字符串当做JavaScript代码来运行。
- 不建议在开发中使用eval：
  - eval代码的可读性非常的差（代码的可读性是高质量代码的重要原则）；
  - eval是一个字符串，那么有可能在执行的过程中被刻意篡改，那么可能会造成被攻击的风险；
  - eval的执行必须经过JS解释器，不能被JS引擎优化；

```javascript
var evalString = `var message = "Hello World;console.log(message)"`
eval(evalString)
console.log(message)
```

### 认识严格模式

- 在ECMAScript5标准中，JavaScript提出了**严格模式的概念（Strict Mode）**：
  - 严格模式很好理解，是一种**具有限制性的JavaScript模式**，从而使**代码隐式的脱离了 ”懒散（sloppy）模式“**； 
  - **支持严格模式的浏览器**在检测到代码中有严格模式时，会**以更加严格的方式对代码进行检测和执行**；
- 严格模式对正常的JavaScript语义进行了一些限制：
  - 严格模式通过 **抛出错误** 来消除一些原有的 **静默（silent）**错误；
  - 严格模式让**JS引擎在执行代码时可以进行更多的优化**（不需要对一些特殊的语法进行处理）；
  - 严格模式禁用了**在ECMAScript未来版本中可能会定义的一些语法**；

### 开启严格模式

- 那么如何开启严格模式呢？严格模式支持粒度话的迁移：
  - 可以支持在js文件中开启严格模式
  - 也支持对某一个函数开启严格模式；
- 严格模式通过在文件或者函数开头使用 "use strict " 来开启

```javascript
"use strict"//开启严格模式

//使用let作为标识符的名称
var name = "abc"
console.log(name)

//定义变量时不使用var
var message = "Hello World"
console.log(message)
```

```javascript
function foo(){//在函数内开启严格模式
    "use strict";
    
    m = "foo"
    console.log(m)
}

foo()
```

## 严格模式限制

- 这里我们来说几个严格模式下的严格语法限制：
  - JavaScript被设计为新手开发者更容易上手，所以有时候本来错误语法，被认为也是可以正常被解析的
  - 但是这种方式可能给带来留下来安全隐患
  - ；
  - 在严格模式下，这种失误就会被当做错误，以便可以快速的发现和修正

1. **无法意外的创建全局变量**
2. **严格模式会使引起静默失败(silently fail,注:不报错也没有任何效果)的赋值操作抛出异常**
3. **严格模式下试图删除不可删除的属性**
4. **严格模式不允许函数参数有相同的名称**
5. **不允许0的八进制语法**
6. **在严格模式下，不允许使用with**
7. **在严格模式下，eval不再为上层引用变量**
8. **严格模式下，this绑定不会默认转成对象**

```javascript
//常见的限制

//1.以外创建全局变量，不会生效而是报错
message = "Hello World"
console.log(message);
//同样的在严格模式下会报错
function foo(){
    age = 18
}
foo()
console.log(age);

//2.不允许函数有相同的参数名称
function foo(x,y,x){//两个x就是相同参数名称，如果不开启严格模式，后面的x会将前面的x覆盖掉
    console.log(x,y,x);
}
foo(10,20,30)//30,20,30(非严格模式)

//3.静默错误
true.name = "xiaoyu"
NaN = 123//非严格模式下不会报错
var obj = {}
Object.defineProperty(obj,'name',{
    configurable:false,//不可配置
    writable:false,//不可写
    value:"why"
})
console.log(obj.name)
obj.name = "xiaoyu"//静默错误，因为我们已经设置不可写入了

//4.不允许使用原先的八进制格式(严格模式)
var num = 0123//八进制
var num2 = 0x123//十六进制
var num3 = 0b100//二进制
console.log(num,num2,num3)//Uncaught SyntaxError: Octal literals are not allowed in strict mode

//5.eval函数不会向上引用变量
var jsString = "var message = 'Hello World';console.log(message)"
eval(jsString)
console.log(message)//这里会报错
```

### 严格模式下的this

```javascript
"use strict"
//之前编写的代码中，自执行函数我们是没有使用过this直接去引用window的
function foo(){
    console.log(this)
    //通常在自执行函数里面我们想要调用window中的name属性的时候，我们不使用this.name
    localStorage.setItem//localStorage也会指向window
} 
foo()//正常情况下this指向window，当开启了严格模式后，自执行函数(默认绑定)会指向undefined

//setTimeout的this
setTimeout(()=>{
    console.log(this)
},2000)//window

setTimeout(function(){
    console.log(this)
},2000)//非严格模式下是window，严格模式下依旧是window，而不是undefined
//this指向window，且是自执行函数，为什么不会是undefined呢？那是因为里面可能执行了一次fn.apply(window)，手动指向了this
//这个是在浏览器中实现的(伪造fake出来的setTimeout)，而不是在v8引擎中实现的
```

## 深入JS面向对象

### 面向对象是现实的抽象方式

> 现实世界的东西大多数都是可以在编程中抽象出来的
>
> 比如你可以抽象出一个女朋友new GridFriend()，或者抽象出coderwhy或者小满什么的来，可惜只能单向的抽象哈哈，不能映射到现实
>
> 编程是对现实世界的抽象，而面向对象是对现实世界抽象的一种方式

- 对象是JavaScript中一个非常重要的概念，这是因为对象可以**将多个相关联的数据封装**到一起，更好的描述一个事物：
  - **比如我们可以描述一辆车**：Car，具有颜色（color）、速度（speed）、品牌（brand）、价格（price），行驶（travel）等 等
  -  **比如我们可以描述一个人**：Person，具有姓名（name）、年龄（age）、身高（height），吃东西（eat）、跑步（run） 等等；
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

![image-20230107083010161](.\JavaScript高级_image\image-20230107083010161.png)

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

# 09_继承的实现方案、ES6面向对象

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

  

-  上面这种方式有一个很大的弊端：创建同样的对象时，需要编写重复的代码；

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

![image-20230109045442038](.\JavaScript高级_image\image-20230109045442038.png)

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

       ![image-20230109055524421](.\JavaScript高级_image\image-20230109055524421.png)

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

  ![image-20230109060143434](.\JavaScript高级_image\image-20230109060143434.png)

- 这个构造函数可以确保我们的对象是有Person的类型的（实际是constructor的属性，这个我们后续再探讨）；

- 但是构造函数就没有缺点了吗？

  - 构造函数也是有缺点的，它在于我们需要为每个对象的函数去**创建一个函数对象实例**；

#### 如何区分是否是构造函数

> 一般来说，构造函数实在跟普通函数没有区别，单纯看一个函数是看不出来的，于是社区对此有了约定俗成的规范，不是必须要遵守，但拥抱规范能够让我们平时更加方便，减少大家的理解跟沟通成本
>
> ```javascript
> function XiaoYu(){
>  //对于构造函数，我们函数名首字母会是大写。如果由多个单词组成的话，则是采用大驼峰标识
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

![image-20230109203959859](.\JavaScript高级_image\image-20230109203959859.png)

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

![image-20230109225438553](.\JavaScript高级_image\image-20230109225438553.png)

![image-20230109224443444](.\JavaScript高级_image\image-20230109224443444.png)

![image-20230109224459797](.\JavaScript高级_image\image-20230109224459797.png)

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

![image-20230109233824306](.\JavaScript高级_image\image-20230109233824306.png)

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

![image-20230109235539333](.\JavaScript高级_image\image-20230109235539333.png)

### constructor属性

- 事实上原型对象上面是有一个属性的：constructor
  - 默认情况下原型上都会添加一个属性叫做constructor，这个constructor指向当前的函数对象；

```javascript
function foo(){

}

console.log(foo.prototype,"纯foo.prototype打印")//这个打印出来是个空对象{}，但是事实上这个并不是空的，只是因为可枚举属性被设置为了false
console.log(Object.getOwnPropertyDescriptors(foo.prototype),"getOwnPropertyDescriptors打印foo.prototype");
```

![image-20230110000506456](.\JavaScript高级_image\image-20230110000506456.png)

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

![image-20230110233648426](.\JavaScript高级_image\image-20230110233648426.png)

**当我们使用重写原型方法之后，也就是在内存里又创建了一个对象，内存图如下(代码在下面的代码块中)：**

- foo函数不再指向它的原型对象了，而是指向新的对象了，刚指向的时候，这个新对象连constructor都没有
- 指向新对象之后，foo函数的原型对象就会被销毁掉了，因为我们js的垃圾回收机制是采用标记清除法(详细的内容往回翻)

![image-20230111013756415](.\JavaScript高级_image\image-20230111013756415.png)

**当我们填入内容之后：**

![image-20230111014318735](.\JavaScript高级_image\image-20230111014318735.png)



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

![image-20230111014738997](.\JavaScript高级_image\image-20230111014738997.png)

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

![image-20230111021233959](.\JavaScript高级_image\image-20230111021233959.png)

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

# 10_ES6-ES12新特性(一)

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

![image-20230111144111168](.\JavaScript高级_image\image-20230111144111168.png)

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

![image-20230111163400343](.\JavaScript高级_image\image-20230111163400343.png)

![image-20230111163938419](.\JavaScript高级_image\image-20230111163938419.png)

![image-20230111171832610](.\JavaScript高级_image\image-20230111171832610.png)

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
>     this.name = name;
> }
> 
> // 添加一个原型属性 sayName
> Person.prototype.sayName = function() {
>     console.log(`My name is ${this.name}`);
> }
> 
> // 创建一个对象 person1
> let person1 = new Person("John");
> console.log(person1.name); // "John"
> console.log(person1.sayName); // "My name is John"
> 
> // 修改原型上的属性，所有继承原型的对象会受影响
> Person.prototype.sayName = function() {
>     console.log(`Hello, My name is ${this.name}`);
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

![image-20230111173150297](.\JavaScript高级_image\image-20230111173150297.png)

### 原型链关系的内存图

![image-20230111173223272](.\JavaScript高级_image\image-20230111173223272.png)

### Object是所有类的父类

- 从我们上面的Object原型我们可以得出一个结论：原型链最顶层的原型对象就是Object的原型对象

![image-20230111175049084](.\JavaScript高级_image\image-20230111175049084.png)

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

![image-20230112034256733](.\JavaScript高级_image\image-20230112034256733.png)

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

# 11_ES6-ES12新特性(二)

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

![image-20230112134112365](.\JavaScript高级_image\image-20230112134112365.png)

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

![image-20230114135452413](.\JavaScript高级_image\image-20230114135452413.png)

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

![image-20230114172103104](.\JavaScript高级_image\image-20230114172103104.png)

![image-20230115003809858](.\JavaScript高级_image\image-20230115003809858.png)

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

