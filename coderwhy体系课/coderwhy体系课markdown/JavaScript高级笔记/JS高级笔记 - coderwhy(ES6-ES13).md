#   JS高级笔记 - coderwhy(ES6-ES13)

> ### 作者：小余同学
>
> 为什么没有01，那是因为01是讲谷歌V8引擎的原理，我看完了，但忘记写笔记了，不想重新看一遍写笔记了hh，大致来说也是很重要的一章节
>
> 需要全系列笔记请到[2002XiaoYu (小余) (github.com)](https://github.com/2002XiaoYu)中自行获取，觉得不错给个star，这是对作者非常大的鼓励

[TOC]

## ES6实现继承

### (掌握)ES5-原型继承关系图解

![image-20230117014937661](.\JavaScript高级_image\image-20230117014937661.png)

![image-20230117095800327](.\JavaScript高级_image\image-20230117095800327.png)



- 下面中p1对象是Person的实例，Person是Funtion的实例(因为p1对象是Person来new出来的，Person函数则是Function来new出来的)
- Function/Object/Person都是Function的实例对象，所以你会发现这些都是指向Function的原型对象
- 原型对象创建的时候，隐式原型都是默认指向Object的显式原型的，Object指向null

```javascript
var obj = {}//由 new Object()创建出来
console.log(obj.__proto__);//指向Object.prototype

function foo(){//由全局的类 new Function()创建

}
console.log(foo.__proto__);//指向Function.prototype

function Person(){

}
//所有函数的隐式原型最终都是指向Function.prototype，也就是
console.log(foo.__proto__ === Function.prototype);//true
console.log(Person.__proto__ === Function.prototype);//true
console.log(foo.__proto__===Person.__proto__);//true

var p1 = new Person()
console.log(Object.prototype);//Object的原型

//Function原型对象的constructor指向
var a = new Function()
console.log(a.prototype.constructor);//[Function: anonymous]匿名函数
```

> 结论：(对象身上隐式原型=>指向 Object 的原型对象，函数身上显式原型+隐式原型=>指向 Function 的原型对象)
>
> - 1. var p1 = new Person()，那p1对象的原型(隐式原型`__proto__`)是指向 Person 的原型对象(prototype) 的
>   2. Person 的原型对象里的constructor(构造函数)再指回Person本身
> - 1. 然后 Person 函数的原型对象是由 new Object() 创建出来的，这里就引出了 Objec t的构造函数
>   2. 既然有Object的构造函数，那就有 Object 构造函数的原型
>   3. 这里 Object 的构造函数的显式原型(prototype)指向 Object 函数的原型对象，然后 Object 的原型对象身上的 constructor 指向 Object 函数本身
> - 1. 此时 Object 函数就像孤岛一样互相指向，但是显然他们不可能独立存在，因为Person函数的原型对象是通过 new Object() 创建出来的，所以 Person 函数的原型对象身上会有隐式原型(因为是 Object 给 new 出来的)，而这个隐式原型就会指向 Object 的显式原型 prototype ，也就是 Object 的原型对象。而Object 的原型对象的`__proto__`就是null了，在往上就没有东西了
> - 1. 除了Object函数之外，还有Function，所有的函数都是通过new Function()创建的，所以所有的函数最终都会指向Function的原型对象(prototype)，如果你是直接创建一个函数的话，那当然就是这个函数的`__proto__`指向Function的prototype了
>   2. 所以我们现在知道了Function，还知道了Function的原型对象。跟Object一样，Function的prototype指向Function的原型对象，然后Function的原型对象身上的constructor又指回Function本身的。
>   3. 那Function本身是从哪里来的？是new Object()创建来的，那显然了，Function的prototype指向Function的原型对象，而Function的原型对象`__proto__`指向的就是Object的原型对象，也就是Object的prototype(因为前面说了Function的原型对象是Object给new的)
>   4. 之前的function Person(){}本身的隐式原型就是指向Function的显式原型。
> - 1. Object我们刚刚说到了显式原型指向Object的原型对象，但是很显然function Object(){}他是一个函数，那就会有隐式原型，这个隐式原型指向的其实就是Function的显式原型。
>   2. 接着就是要说到Function了，我们function Function()指向的除了上面用到的prototype显式原型，还有一个隐式原型，那这个隐式原型指向哪里？还是指向Function的显式原型，没错Function函数本身不管是显式原型还是隐式原型都是指向Function函数的原型对象，这倒是很特殊，在这里进行了约束，不再通过`__proto__`继续指向其他地方的显示原型，停止了扩散

- 加入字面量创建的方式，刚刚是通过new创建的对象，当一开始就是对象的字面量出现，又会是怎么样的呢？

![image-20230117133227224](.\JavaScript高级_image\image-20230117133227224.png)

> - 1. 字面量创建一出来就是对象，那身上的是隐式原型`__proto__`指向的就是我们刚刚所讨论的两大特殊原型对象的其中之一Object的原型对象。因为一开始就是对象，所以跟函数扯不上关系，自然也没有在这个阶段指向Function的原型对象
>
> - 总结：obj是Object的实例对象(因为obj是通过new Object()创建出来的)
>
>   ​			Object是Person/Function的父类

### (理解)ES5-原型继承关系图解-解读

> 所有解答都在图中，看图理解更好，所有的内容都在上面章节总结了

![image-20230117134008957](.\JavaScript高级_image\image-20230117134008957.png)

### (掌握)ES5-构造函数的类方法定义

> - 没有实例对象的情况下，能不能调用函数？不可以
> - 因为 Person 是一个函数，而不是一个实例对象。 learn 方法是在 Person 的 prototype 上的，只能在 Person 的实例上调用。如果要调用 learn 方法，需要先创建一个 Person 的实例，然后在实例上调用 learn 方法

```javascript
function Person(name,age){
    console.log("running");
}
//添加在Person原型上的方法也称为 实例方法
Person.prototype.learn = function(){
    console.log("小余在学习");
}
Person.learn()//是不能这么调用的，因为Person身上没有learn属性，为什么不顺着原型去查找，因为你本身就不是去原型身上找的
//举例子
var obj = {
    friend:{
        play:function() {
            console.log("play~");
        }
    }
}
obj.play()//相当于在obj中去寻找play，但是play在friend里面，很明显找不到的，这些本身就不在原型链上面，所有当然就不会顺着原型链查找
```

```javascript
//那会不会这样调用？
function Person(name,age){
    console.log("running");
}
Person.prototype.learn = function(){
    console.log(this.name+"在学习");
}
Person.prototype.learn()//这样也能调用，但是不会这么做。因为通常函数会绑定一些东西，比如name或者其他属性，你这样调用就没有name了，那肯定是不行了，总不能undefined在学习吧
```

- 进入主题**类方法调用**

  > 常用的方法包括：
  >
  > - Math.abs(x)：返回x的绝对值。
  > - Math.round(x)：对x进行四舍五入。
  > - Math.ceil(x)：返回大于等于x的最小整数。
  > - Math.floor(x)：返回小于等于x的最大整数。
  > - Math.random()：返回0 ~ 1之间的随机数。
  > - Math.max(a, b, c, ...)：返回参数中的最大值。
  > - Math.min(a, b, c, ...)：返回参数中的最小值。
  >
  > 常用的常量包括：
  >
  > - Math.E：自然常数 e。
  > - Math.PI：圆周率 π。
  >
  > 还有其它更多用于进行三角函数运算，指数运算等等
  >
  > 需要注意的是，所有的方法都是静态方法，不需要创建Math对象的实例，直接使用Math即可调用。

```javascript
function Person(name,age){
    this.name = name
    this.age = age
}
//添加Person对象本身的方法成为类方法
var names = ["小余","大余","超大余","小满","中满","大满","超级满"]
Person.randomPerson = function(){
    var randomName = names[Math.floor(Math.random() *names.length)]
    return new Person(randomName,Math.floor(Math.random()*100))
}

var p = Person.randomPerson()//直接调用Person身上的方法，而不是去调用Person原型上的
console.log(p);
```

- 总结：类方法跟实例方法的区别

  - 类方法：

    在类的构造函数上定义的方法。它们被所有类的实例共享，并且可以通过类名来访问。类方法不能访问类实例上的私有属性，因为它们不能被类实例直接访问

    类方法可以在类的构造函数上定义，也可以在类的原型对象上定义。

    > 其实就是直接通过类名调用的，类名就是class xxx的xxx

    ```javascript
    class MyClass {
      static myStaticMethod = () => {
        console.log('This is a static method.');
      }
    }
    
    MyClass.myStaticMethod(); // This is a static method
    ```

  - 实例方法：实例方法是添加到原型上面的方法，也就是在类的原型对象上定义的方法

    实例方法可以访问类中定义的属性和方法，这样就可以在实例方法中操作类的状态

    ```javascript
    var num = 666//num有具体的实例
    num.toFixed()//才能去调用方法
    ```

### (掌握)ES6-ES6名称术语和class定义类

> ES6也叫做ECMAScript2015，或者叫做ES2015
>
> 同理ES7 => ECMAScript2016 => ES2016...依次推出
>
> ES13 => ECMAScript2022 

- 我们会发现，按照前面的构造函数形式创建类 ，不仅仅和编写普通的函数过于相似，而且代码并不容易理解。
  - 在ES6 ECMAScript2015 ）新的标准中使用了 class 关键字来直接定义类；
  - 但是类本质上依然是前面所讲的构造函数、原型链的语法糖而已；
  - 所以学好了前面的构造函数、原型链更有利于我们理解类的概念和继承关系；
- 那么，如何使用class 来定义一个类呢？
  - 可以使用两种方式来声明类：类声明和类表达式；

```javascript
//ES6中定义类
//大括号的3种用法
    //{key:value} -> 对象的用法
    //{表达式} -> 代码块
    //{} -> 类的结构
class Person{//跟另一种方式的不同之处

}

//创建实例对象
var p1 = new Person()
var p2 = new Person()

//另外一种定义方式(不常用)：表达式
var Student = class {//不同之处在这里

}

var foo = function(){

}
//创建实例对象
var stu1 = new Student()
console.log(stu1);
```

### (掌握)ES6-class类中定义构造方法和实例方法

-  注意：类中定义的多个内容不需要使用`，`进行分割；

```javascript
class Person{
    //当我们通过new 关键字调用一个 Person 类时，默认调用 class 中的 constructor 方法
    //如果我们想要向 constructor 传递一些参数，我们一般自己手写一个
    //这里的函数是不需要写 function 的，有两种写法
    //第一种：(类中的构造函数)
    constructor(name,age){//不支持函数的重载
        this.name = name
        this.age = age
    }//方法之间不加逗号
    
    //第二种
    //running = () => {
	//
    //}
    
    //实例方法：running这些本身是放到Person类的prototype里面了
    running(){
        console.log(this.name+"今年"+this.age+"岁了")
    }
}

var p1 = new Person("小余",20)
console.log(p1.name,p1.age);//小余 20
console.log(Person.prototype === p1.__proto__);//true
p1.running()//小余今年20岁了

//研究实例方法到底放在哪，放在类的显式原型中
console.log(Person.running);//undefined
console.log(Person.prototype.running);//[Function: running]
```

### (掌握)ES6-class类和function构造函数

```javascript
//通过function来定义类
function Person1(name,age){
    this.age = age
    this.name = name
}
Person1.prototype.running = function(){

}
Person1.prototype.eating = function(){

}
var p1 = new Person1("大余",25)
console.log(p1.__proto__ === Person1.prototype);//true

//class来定义类，这种写法只是上面的语法糖
class Person2{
    constructor(name,age){
        this.name = name
        this.age = age
    }

    running(){

    }
    eating(){
        
    }
}

var p2 = new Person2("小余",20)
console.log(p2.__proto__ === Person2.prototype);//true
console.log(Person2.prototype.constructor);//指回类本身，跟上面Person1是一样的
console.log(typeof Person1,typeof Person2);//都是function
```

- **class 与 function 的不同点**

```javascript
//function的写法是能够作为普通函数去调用的
function Person1(name,age){
    //这两个需要写，如果不写，name，age打印不出来。如果你name能够打印出来的话，那是你之前有用过，他存到window里面去了，因为Person1函数的外面一层就是window，这个时候就算你把Person1("大满",24)的大满修改掉，控制台也是无法修改掉的，因为函数内部的this.name是去window中读取了
    this.name = name
    this.age = age
    console.log(this.name+"今年已经"+this.age);
}
Person1("大满",24)//大满今年已经24

//class定义的类，不能作为一个普通的函数进行调用
//class的写法则是不行，结果就是报错。不使用 new 的时候是不能够调用的
class Person2{
    constructor(name,age){
        this.name = name
        this.age = age
    }
}

Person2("小余",20)//Class constructor Person2 cannot be invoked without 'new'
```

#### **类和构造函数的异同**

- 我们来研究一下类的一些特性：你会发现它和我们的构造函数的特性其实是一致的

```javascript
class Person{

}

var p = new Person()

console.log(Person);//[class Person]
console.log(Person.prototype);//{}
console.log(Person.prototype.constructor);//[class Person]
console.log(p.__proto__ === Person.prototype);//true

console.log(typeof Person);//function，类型为函数
```

#### 类的构造函数(上面的总结)

- 如果我们希望在创建对象的时候给类传递一些参数，这个时候应该如何做呢？
  - 每个类都可以有一个自己的构造函数（方法），这个方法的名称是固定的constructor；
  - 当我们通过new操作符，操作一个类的时候会调用这个类的构造函数constructor
  -  每个类只能有一个构造函数，如果包含多个构造函数，那么会抛出异常
- 当我们通过new关键字操作类的时候，会调用这个constructor函数，并且执行如下操作：
  1. 在内存中创建一个新的对象（空对象）；
  2. 这个对象内部的[[prototype]]属性会被赋值为该类的prototype属性
  3. 构造函数内部的this，会指向创建出来的新对象
  4. 执行构造函数的内部代码（函数体代码）
  5. 如果构造函数没有返回非空对象，则返回创建出来的新对象

#### 类的实例方法

- 在上面我们定义的属性都是直接放到了this上，也就意味着它是放到了创建出来的新对象中
  - 在前面我们说过对于实例的方法，我们是希望放到原型上的，这样可以被多个实例来共享
  -  这个时候我们可以直接在类中定义

```javascript
class Person{
    constructor(name){
        this.name = name
    }
    learn(){//这个就是实例方法
        console.log(this.name+"爱好学习");
    }
}

var p1 = new Person("小余")
p1.learn()//小余爱好学习
```

### (理解)ES6-类和对象的访问器方法编写

- 语法糖(补充)
  - 语法糖（Syntactic sugar），也译为糖衣语法，是由英国[计算机](https://baike.baidu.com/item/计算机/140338?fromModule=lemma_inlink)科学家彼得·约翰·兰达（Peter J. Landin）发明的一个术语，指计算机语言中添加的某种语法，这种语法对语言的功能并没有影响，但是更方便[程序员](https://baike.baidu.com/item/程序员/62748?fromModule=lemma_inlink)使用。通常来说使用语法糖能够增加程序的可读性，从而减少程序代码出错的机会 => **百度百科**
  - 语法糖是指程序设计语言中的一种语法结构，它是对程序员更友好的语法结构，并不改变语言的语义。语法糖只是一种编程习惯上的约定，它使得程序更容易理解和编写，但是编译器会将其转换为语言的基本语法结构 => **自我总结**

#### 类的访问器(编写)

- 我们之前讲对象的属性描述符时有讲过对象可以添加setter和getter函数的，那么类也是可以的

```javascript
//针对对象
//方式1：描述符
var obj = {

}

Object.defineProperty(obj,"name",{
    configurable:true,
    enumerable:false,
    get:function(){

    },

    set:function(){

    }
})

//方法2：直接在对象定义访问器(少见)	但这种对象上的监听只能实现监听一个的，如果我想要监听多个如何实现
var obj = {
    _name:"小余",
    //setter方法(私有属性)，这就是个名词
    set name(value){
        this._name = value
    },
    //getter方法
    get name(){
        return this._name
    }
}
```

- 静态方法通常用于定义直接使用类来执行的方法，不需要有类的实例，使用static关键字来定义：

```javascript
class Person{
    //规范：通过下划线_命名的属性与方法都是不在外界进行访问的.在ES13中进行了规定，以#开头的也是不能够访问的
    constructor(name,age){
        this._name = name
        this._age = age
    }

    set name(value){
        this._name = value
    }

    get name(){
        return this._name
    }

    set age(value){
        this._age = value
    }
    get age(){
        return this._age
    }
}

//实现了批量监听，而不是针对一个单独监听，p1与p2互不干扰。相对于对象监听，会更加方便
var p1 = new Person("汤姆",20)
p1.name = "小余"
console.log(p1.name);//小余

var p2 = new Person("小余",20)
p2.name = "小满"
p2.age = 24
console.log(p2.name,p2.age);//小满 24
```

#### 访问器的应用场景(案例)

- 封装性更好，但是前端用的少，后端用得比较多。如果想要前端向后端过渡，还是需要多注意一下

```javascript
class Rectangle{
    constructor(x,y,width,height){
        this.x = x,
        this.y = y
        this.width = width
        this.height = height
    }

    get position(){
        return {x:this.x,y:this.y}
    }

    get size(){
        return {width:this.width,height:this.height}
    }

}

var rect1 = new Rectangle(10,20,100,200)
console.log(rect1.position);//{x: 10, y: 20}
console.log(rect1.size);//{width: 100, height: 200}
```

### (掌握)ES6-类中静态方法的定义(类方法)

> 类方法是指放在类中的方法，但通常也叫做类中的静态方法，他们其实是同一个东西

- 静态方法通常用于定义直接使用类来执行的方法，不需要有类的实例，使用static关键字来定义：

```javascript
function Person(){}
//实例方法
Person.prototype.running = function(){
    console.log("小余跑起来了");
}
//类方法
Person.learn = function(){
    console.log("小余在学习了");
}

var P1 = new Person()
//实例方法
P1.running()//小余跑起来了
//类方法，直接调用。这是之前的写法
Person.learn()//小余在学习了
```

- 如果我们通过`class`进行调用的话，class里面的都是实例方法，需要通过new过后才能调用，因为p1的隐式原型指向Person的显式原型

```javascript
class Person{
    running(){
        console.log("小余跑起来了");
    }
}

var p1 = new Person()
//两者其实是一样的，通过上面学习类，你应该已经知道他们为什么一样的
p1.running()//小余跑起来了
Person.prototype.running()//小余跑起来了
```

- 将静态方法(类方法)写在class里面，内聚性会更好，不会过于松散
- 那我们要怎么实现？通过static关键字实现。这样Person.prototype.learn()就能够通过Person.learn()实现了

```javascript
var names = ["小余","小满","班花姐姐","狗洛","超级狗洛"]
class Person{
    constructor(name,age){
        this.name = name
        this.age = age
    }

    running(){
        console.log("小余跑起来了");
    }

    //静态方法
    static learn(){
        //这个静态方法里面也是有this的，this指向谁，得看是谁调用了我们，也就是this的显式绑定
        console.log("小余又在学习了");
    }

    static randomPerson(){
        // console.log(this);
        var randomPerson = names[Math.floor(Math.random()*names.length)]
        //我们new this 其实就相当于new Person，好处是当我们Person改变名字的时候，这里的this会自动指向，不需要再修改
        return new this(randomPerson,Math.floor(Math.random()*100))
    }
}

var p1 = new Person()
p1.running()//小余跑起来了
// Person.prototype.learn()
Person.learn()//小余又在学习了

//使用randomPerson静态方法，可以直接使用Person进行调用，而不需要通过原型链调用或者在class外面定义
var p2 = Person.randomPerson()
console.log(p2);//姓名跟年龄是随机的
```

### (掌握)ES6-类通过extends实现继承特性

- 前面我们花了很大的篇幅讨论了在ES5中实现继承的方案，虽然最终实现了相对满意的继承机制，但是过程却依然是非常繁琐的

- 在ES6中新增了使用extends关键字，可以方便的帮助我们实现继承：
- 继承的类:子类继承父类。 子类又叫做**派生类**,*父类又叫做**基类,超类***

```javascript
//以前的写法，Student、Teacher重复性过高，实现继承过于麻烦
class Student{
    constructor(name,age){
        this.name = name
        this.age = age
    }

    running(){
        console.log("running");
    }

    learn(){
        console.log("学习");
    }
}

class Teacher{
    constructor(name,age,title){
        this.name = name
        this.age = age
        this.title = title
    }

    running(){
        console.log("running");
    }

    learn(){
        console.log("学习");
    }
}
```

```javascript
class Person{
    //父类
    constructor(name,age){
        this.name = name
        this.age = age
    }

    running(){
        console.log("running");
    }

    static learn(){
        console.log("学习");
    }
}

class Student extends Person{
    //子类
    constructor(){//我们这里想要通过父类来实现传递参数，也就是走Person上面的name和age，避免子类的继续重复代码，但是如果这么写会进行报错，我们要如何才能实现这个效果呢？这个问题放到下面的代码块中来解决
        // this.name = name
        // this.age = age
    }

     static age(){
        console.log(this.name+"今年已经"+this.age);
    }
}

class Teacher extends Person{
    //子类
    constructor(title){
//        this.name = name
//        this.age = age
        this.title = title
    }
    title1(){
        console.log(this.title+"这是title");
    }
}
//子类调用父类的静态方法
Teacher.learn()
Student.age("小余",18)//报错咯
```

#### super关键字

> 初步使用super关键字

```javascript
//调用 父对象/父类 的构造函数
super([arguments])

//调用 父对象/父类 上的方法
super.functionOnParent([arguments])
```

```javascript
class Person{
    //父类
    constructor(name,age){
        this.name = name
        this.age = age
    }

    running(){
        console.log("running");
    }

    static learn(){
        console.log("学习");
    }
}

class Student extends Person{
    //子类
    constructor(){//这里就来解决我们上面遗留的问题
        super(name,age)//将name与age来调用父类的constructor，且super需要写在this的前面
        // this.name = name
        // this.age = age
    }

    static age2(){
        console.log("啦啦啦，实现了super");
    }
}
Student.age2()//啦啦啦，实现了
```

### (理解)ES6-super关键字的其他用法

#### super关键字(续)

- Class为我们的方法中还提供了super关键字：
  -  执行 super.method(...) 来调用一个父类方法
  - 执行 super(...) 来调用一个父类 constructor（只能在我们的 constructor 中）
-  注意：在子（派生）类的构造函数中使用this或者返回默认对象之前，必须先通过super调用父类的构造函数！
- super的使用位置有三个：子类的构造函数、实例方法、静态方法；

```javascript
//在上面的情况下，我们子类使用父类的方法，里面的内容都是固定的。如果我们想要根据子类进一步的调整的话，要怎么实现？
class Animal{
    running(){
        console.log("跑步");
    }
    eating(){
        console.log("吃东西");
    }
}

class Dog extends Animal{
    //如果子类对父类的方法实现不满足(继承过来的方法)
    //重新实现称之为重写(对父类方法的重写)
    running(){
        console.log("dog用4条腿狂奔");
    }
}

var a = new Dog()
a.running()//dog用4条腿狂奔
a.eating()//吃东西

```

> 但上面这种是对父类的内容全都不满意的，所以将所有的内容都进行了修改增加，例如上面的从`跑步`变成子类的`dog用4条腿狂奔`
>
> - 但我们大多数情况下，是部分满意，部分不满意，也就是我们不全部修改，我们只修改部分。那这种难道也要大动干戈进行重写吗？那这样不就又增加了子类的工作量了，父类的作用不久又被削弱了吗？
> - 所以我们有更好的方式

```javascript
class Animal{
    running(){
        console.log("跑步");
    }
    eating(){
        console.log("吃东西");
    }
    
    static sleep(){
        console.log("睡觉");
    }
}

class Dog extends Animal{
//    running(){
//        console.log("dog用4条腿狂奔");
//    }
    super.running()//从父类中调用来使用

//重写
    static sleep(){
        console.log("抱着老婆");
        super.sleep()//也可以使用super进行
    }
}

Dog.sleep()//抱着老婆 睡觉	能够调用，说明能够继承静态方法。而且也是可以重写或者使用部分重写的
```

### (理解)ES6-继承自内置类进行扩展

#### 继承内置类

```javascript
class HYArray extends Array{//继承数组，然后进行扩展

}

var arr = new HYArray(10,20,30)

console.log(arr);//HYArray(3) [10, 20, 30]
```

> 通过这个方式，我们就能够实现一些内置的方法，甚至`在这些内`置方法上做一些扩展。例如获取每个数组的第二个之类的，自己实现出来，将实现过程封装起来

```javascript
class HYArray extends Array{
    get lastItem(){
        //获取数组最后一个的数据
        return this[this.length -1]
    }

    	//获取数组的第一个数据
    get firstItem(){
        return this[0]
    }
}

var arr = new HYArray(10,20,30)

console.log(arr.lastItem);//30
console.log(arr.firstItem);//10

//复习我们以前的做法，直接在原型链上面进行扩展
Array.prototype.lastItem = function(){
    return this[this.length -1]
}
```

### (了解)ES6-类的混入mixins写法

- **JavaScript的类只支持单继承：也就是只能有一个父类**
  - 那么在开发中我们我们需要在一个类中添加更多相似的功能时，应该如何来做呢？
  -  这个时候我们可以使用混入（mixin）
  - 我们一般不使用这个，混入是很恶心的操作，已经被hook替代，但是作为了解还是有必要的

```javascript
//JavaScript只支持单继承(不支持多继承)
class Animal {
    running(){}
}

class Flyer{
    flying(){}
}

class Bird extends Animal,Flyer{
    //报错，不能够同时继承多个，在像C++这种语言就可以
}
```

```javascript
function mixinAnimal(BaseClass){
    //返回一个继承自BaseClass的类，在这个BaseClass的类上面进行一个扩展
    return class extends BaseClass{
        running(){}//这个就算扩展的内容
    }
}

function mixinNames(Names){
    return class extends Names{
        names1(){}
    }
}

class Flyer{
    flying(){}
}
//扩展方式1
class Bird extends mixinAnimal(Flyer) {
    //成功混入两个类的方法
}

var p1 = new Bird()

console.log(p1.__proto__);//结果如下
//扩展方式2
var NewBird = mixinNames(mixinAnimal(Flyer))//连续混入3个
var p3 = new NewBird() 
console.log(p3);
//扩展方式3：通过Object.assign的方式将两个对象拷在一起
```

`p1.__proto__`的效果：![image-20230123011952666](.\JavaScript高级_image\image-20230123011952666.png)

`p3.__proto__`的效果：![image-20230123013153852](.\JavaScript高级_image\image-20230123013153852.png)

#### 在react中的高阶组件

![image-20230123013433048](.\JavaScript高级_image\image-20230123013433048.png)

### (理解)ES6-babel工具ES6转ES5代码

> 代号巴别塔，将ES6的代码转化为ES5，用来适应低配置的浏览器，这里主要讲述概念，还有那个工具网站因为在国外一直打不开
>
> ES6转化ES5转化网站：[Babel · The compiler for next generation JavaScript (babeljs.io)](https://babeljs.io/)
>
> ![image-20230123015341166](.\JavaScript高级_image\image-20230123015341166.png)
>
> 成功转化代码的配置：(因为网站会自动根据当前浏览器适配，如果你浏览器支持他可能就不给你适配了，所有需要调整一下，具体的后面的课程会讲)
>
> ![image-20230123015458627](.\JavaScript高级_image\image-20230123015458627.png)

### (了解)ES6-babel工具对class类的转化

```javascript
//转化之前
class Person{
    constructor(name,age){
        this.name = name
        this.age = age
    }

    running(){}

    eating(){}
    
    static randomPerson(){}
}

class Student extends Person{
    constructor(name,age,sno,score){
        super(name,age)
        this.sno = sno
        this.score = score
    }

    studying(){}
}
```

```javascript
"use strict";//严格模式

function _inherits(subClass, superClass) {
    if (typeof superClass !== "function" && superClass !== null) {
        throw new TypeError("Super expression must either be null or a function");
    }
    subClass.prototype = Object.create(superClass && superClass.prototype, {
        constructor: { value: subClass, writable: true, configurable: true }
    });
    Object.defineProperty(subClass, "prototype", { writable: false });
    if (superClass) _setPrototypeOf(subClass, superClass);
}
function _setPrototypeOf(o, p) {
    _setPrototypeOf = Object.setPrototypeOf
        ? Object.setPrototypeOf.bind()
        : function _setPrototypeOf(o, p) {
            o.__proto__ = p;
            return o;
        };
    return _setPrototypeOf(o, p);
}
function _createSuper(Derived) {
    var hasNativeReflectConstruct = _isNativeReflectConstruct();
    return function _createSuperInternal() {
        var Super = _getPrototypeOf(Derived),
            result;
        if (hasNativeReflectConstruct) {
            var NewTarget = _getPrototypeOf(this).constructor;
            result = Reflect.construct(Super, arguments, NewTarget);
        } else {
            result = Super.apply(this, arguments);
        }
        return _possibleConstructorReturn(this, result);
    };
}
function _possibleConstructorReturn(self, call) {
    if (call && (_typeof(call) === "object" || typeof call === "function")) {
        return call;
    } else if (call !== void 0) {
        throw new TypeError(
            "Derived constructors may only return object or undefined"
        );
    }
    return _assertThisInitialized(self);
}
function _assertThisInitialized(self) {
    if (self === void 0) {
        throw new ReferenceError(
            "this hasn't been initialised - super() hasn't been called"
        );
    }
    return self;
}
function _isNativeReflectConstruct() {
    if (typeof Reflect === "undefined" || !Reflect.construct) return false;
    if (Reflect.construct.sham) return false;
    if (typeof Proxy === "function") return true;
    try {
        Boolean.prototype.valueOf.call(
            Reflect.construct(Boolean, [], function () { })
        );
        return true;
    } catch (e) {
        return false;
    }
}
function _getPrototypeOf(o) {
    _getPrototypeOf = Object.setPrototypeOf
        ? Object.getPrototypeOf.bind()
        : function _getPrototypeOf(o) {
            return o.__proto__ || Object.getPrototypeOf(o);
        };
    return _getPrototypeOf(o);
}
function _typeof(obj) {
    "@babel/helpers - typeof";
    return (
        (_typeof =
            "function" == typeof Symbol && "symbol" == typeof Symbol.iterator
                ? function (obj) {
                    return typeof obj;
                }
                : function (obj) {
                    return obj &&
                        "function" == typeof Symbol &&
                        obj.constructor === Symbol &&
                        obj !== Symbol.prototype
                        ? "symbol"
                        : typeof obj;
                }),
        _typeof(obj)
    );
}
function _classCallCheck(instance, Constructor) {
    //如果instance不是constructor的实例的话，就返回错误
    if (!(instance instanceof Constructor)) {
        throw new TypeError("Cannot call a class as a function");
    }
}
function _defineProperties(target, props) {
    for (var i = 0; i < props.length; i++) {
        var descriptor = props[i];//遍历所有的属性为其加上descript
        descriptor.enumerable = descriptor.enumerable || false;
        descriptor.configurable = true;
        if ("value" in descriptor) descriptor.writable = true;
        //通过descriptor.key添加是因为，不希望我这些在默认情况下也会是不可枚举的
        Object.defineProperty(target, _toPropertyKey(descriptor.key), descriptor);
    }
}
function _createClass(Constructor, protoProps, staticProps) {//protoProps,：实例方法，staticProps：静态方法
    if (protoProps) _defineProperties(Constructor.prototype, protoProps);
    if (staticProps) _defineProperties(Constructor, staticProps);//封装起来的方法_defineProperties
    Object.defineProperty(Constructor, "prototype", { writable: false });
    return Constructor;
}
function _toPropertyKey(arg) {
    var key = _toPrimitive(arg, "string");
    return _typeof(key) === "symbol" ? key : String(key);
}
function _toPrimitive(input, hint) {
    if (_typeof(input) !== "object" || input === null) return input;
    var prim = input[Symbol.toPrimitive];
    if (prim !== undefined) {
        var res = prim.call(input, hint || "default");
        if (_typeof(res) !== "object") return res;
        throw new TypeError("@@toPrimitive must return a primitive value.");
    }
    return (hint === "string" ? String : Number)(input);
}
//tree shaking(摇树):如果你定义了但从来不使用，会进行删除。这个过程就叫做这个名词
//纯函数(__PURE__就是意味着提示这是一个纯函数)：相同的输入一定产生相同的输出，并且不会产生副作用
var Person = /*#__PURE__*/ (function () {
    //debugger，这是在后面会进行debugger的演示的
    function Person(name, age) {
        _classCallCheck(this, Person);//这一步只是做了更严谨的判断
        this.name = name;
        this.age = age;
    }
    _createClass(//转化为ES5后，我们的实例方法(原型链上的方法)跟静态方法static(类方法)，_createClass会做出判断
        Person,
        [
            {
                key: "running",
                value: function running() { }
            },
            {
                key: "eating",
                value: function eating() { }
            }
        ],
        [
            {
                key: "randomPerson",
                value: function randomPerson() { }
            }
        ]
    );
    return Person;
})();
var Student = /*#__PURE__*/ (function (_Person) {
    _inherits(Student, _Person);
    var _super = _createSuper(Student);
    function Student(name, age, sno, score) {
        var _this;
        _classCallCheck(this, Student);
        _this = _super.call(this, name, age);
        _this.sno = sno;
        _this.score = score;
        return _this;
    }
    _createClass(Student, [
        {
            key: "studying",
            value: function studying() { }
        }
    ]);
    return Student;
})(Person);

```

> 后面是debugger环节，不好通过语言描述，建议直接观看视频

### (了解)ES6-babel工具对extends的转换

```javascript
class Person{
    constructor(name,age){
        this.name = name
        this.age = age
    }

    running(){}
    eating(){}

    static randomPerson(){}
}

class Student extends Person{
    constructor(name,age,sno,score){
        super(name,age)
        this.sno = sno
        this.score = score
    }

    //实例方法
    studying(){}
    //静态方法
    static randomStudent(){}
}

var stu = new Student()
```

- 经过babel的转化(超级多)

```javascript
"use strict";

function _inherits(subClass, superClass) {
    //判断类型不是函数且不是null的时候，抛出异常
    if (typeof superClass !== "function" && superClass !== null) {
        throw new TypeError("Super expression must either be null or a function");
    }
    //边界的判断：superClass && superClass.prototype，superClass有值的时候才会执行后面原型的部分，防止内容是null会报错
    subClass.prototype = Object.create(superClass && superClass.prototype, {
        constructor: { value: subClass, writable: true, configurable: true },
    });
    Object.defineProperty(subClass, "prototype", { writable: false });//设置subClass这个属性里面的原型里不可写，不能够继续赋值新值，因为改掉了值就无法实现继承了
    if (superClass) _setPrototypeOf(subClass, superClass);
}
function _setPrototypeOf(o, p) {
    _setPrototypeOf = Object.setPrototypeOf
        ? Object.setPrototypeOf.bind()
        : function _setPrototypeOf(o, p) {
            o.__proto__ = p;
            return o;
        };
    return _setPrototypeOf(o, p);
}
function _createSuper(Derived) {
    var hasNativeReflectConstruct = _isNativeReflectConstruct();
    return function _createSuperInternal() {
        var Super = _getPrototypeOf(Derived),
            result;
        if (hasNativeReflectConstruct) {//当if (typeof Proxy === "function") return true的时候，就会执行这里面的内容
            var NewTarget = _getPrototypeOf(this).constructor;
            result = Reflect.construct(Super, arguments, NewTarget);
        } else {//否则就执行这个
            result = Super.apply(this, arguments);//绑定了子类的this，然后将argument传递了进去
        }
        return _possibleConstructorReturn(this, result);
    };
}
function _possibleConstructorReturn(self, call) {
    if (call && (_typeof(call) === "object" || typeof call === "function")) {
        return call;
    } else if (call !== void 0) {
        throw new TypeError(
            "Derived constructors may only return object or undefined"
        );
    }
    return _assertThisInitialized(self);
}
function _assertThisInitialized(self) {
    if (self === void 0) {
        throw new ReferenceError(
            "this hasn't been initialised - super() hasn't been called"
        );
    }
    return self;
}
function _isNativeReflectConstruct() {
    //判断当前的浏览器支不支持Reflect，这Proxy跟Reflect都是ES6的内容
    if (typeof Reflect === "undefined" || !Reflect.construct) return false;
    if (Reflect.construct.sham) return false;
    if (typeof Proxy === "function") return true;
    try {
        Boolean.prototype.valueOf.call(
            Reflect.construct(Boolean, [], function () { })
        );
        return true;
    } catch (e) {
        return false;
    }
}
function _getPrototypeOf(o) {
    _getPrototypeOf = Object.setPrototypeOf
        ? Object.getPrototypeOf.bind()//绑定到一个特定的对象上，这样可以让这个函数只能在这个特定对象上运行
        : function _getPrototypeOf(o) {//判断你的浏览器支持哪种方式，是__proto__还是getPrototypeOf
            return o.__proto__ || Object.getPrototypeOf(o);
        };
    return _getPrototypeOf(o);
}
function _typeof(obj) {
    "@babel/helpers - typeof";
    return (
        (_typeof =
            "function" == typeof Symbol && "symbol" == typeof Symbol.iterator
                ? function (obj) {
                    return typeof obj;
                }
                : function (obj) {
                    return obj &&
                        "function" == typeof Symbol &&
                        obj.constructor === Symbol &&
                        obj !== Symbol.prototype
                        ? "symbol"
                        : typeof obj;
                }),
        _typeof(obj)
    );
}
function _classCallCheck(instance, Constructor) {
    if (!(instance instanceof Constructor)) {//判断传入的内容是不是constructor
        throw new TypeError("Cannot call a class as a function");
    }
}
function _defineProperties(target, props) {
    for (var i = 0; i < props.length; i++) {
        var descriptor = props[i];
        descriptor.enumerable = descriptor.enumerable || false;
        descriptor.configurable = true;
        if ("value" in descriptor) descriptor.writable = true;
        Object.defineProperty(target, _toPropertyKey(descriptor.key), descriptor);
    }
}
function _createClass(Constructor, protoProps, staticProps) {
    if (protoProps) _defineProperties(Constructor.prototype, protoProps);
    if (staticProps) _defineProperties(Constructor, staticProps);
    Object.defineProperty(Constructor, "prototype", { writable: false });
    return Constructor;
}
function _toPropertyKey(arg) {
    var key = _toPrimitive(arg, "string");
    return _typeof(key) === "symbol" ? key : String(key);
}
function _toPrimitive(input, hint) {
    if (_typeof(input) !== "object" || input === null) return input;
    var prim = input[Symbol.toPrimitive];
    if (prim !== undefined) {
        var res = prim.call(input, hint || "default");
        if (_typeof(res) !== "object") return res;
        throw new TypeError("@@toPrimitive must return a primitive value.");
    }
    return (hint === "string" ? String : Number)(input);
}
var Person = /*#__PURE__*/ (function () {
    function Person(name, age) {
        _classCallCheck(this, Person);
        this.name = name;
        this.age = age;
    }
    _createClass(
        Person,
        [//实例方法
            {
                key: "running",
                value: function running() { },
            },
            {
                key: "eating",
                value: function eating() { },
            },
        ],
        [//静态方法
            {
                key: "randomPerson",
                value: function randomPerson() { },
            },
        ]
    );
    return Person;
})();

//继承回顾
function inherit(SubType, SuperType) {
    SubType.prototype = Object.create(SubType.prototype)
    SubType.prototype.constructor = SubType
}

//核心代码
debugger
var Student = /*#__PURE__*/ (function (_Person) {
    _inherits(Student, _Person);//这里的Student可以提前使用，因为下面的Student函数会作用域提升
    var _super = _createSuper(Student);
    function Student(name, age, sno, score) {
        var _this;
        _classCallCheck(this, Student);//检查这个this，让其不当作普通函数进行调用，而Student就是传入constructor形参的部分
        _this = _super.call(this, name, age);//借用构造函数
        _this.sno = sno;
        _this.score = score;
        return _this;
    }

    //实例方法
    _createClass(
        Student,
        [
            {
                key: "studying",
                value: function studying() { },
                //静态方法
            },
        ],
        [
            {
                key: "randomStudent",
                value: function randomStudent() { },
            },
        ]
    );
    return Student;
})(Person);//这种()(Person)传递方式是为了避免函数产生闭包，对函数外的Person产生依赖，继续保持纯函数

var stu = new Student("小余",20,100,110)

```

### (了解)ES6-babel工具源码的获取

> 只是在上面的基础上继续解释了一下，跳过

### (了解)ES6-类方法的继承逻辑

> Student函数对象通过Object.create(Person,Student)创建出来一个对象，将Student作为Person的显式原型，那Person的隐式原型`__proto__`指向Person的显式原型，有了Person的显式原型，就有了Person对应的函数对象(Function)，函数对象指向Person的显式原型

![image-20230125031251927](.\JavaScript高级_image\image-20230125031251927.png)

类方法的继承：   ![image-20230125031535908](.\JavaScript高级_image\image-20230125031535908.png) 

### (理解)内容回顾和作业布置

#### 一. 原型关系图(理解)

* function Person() {}
* function Object() {}
* function Function() {}

![image-20230125032556875](.\JavaScript高级_image\image-20230125032556875.png)

#### 二. ES6类的使用(掌握)

##### 2.1. class定义类

##### 2.2. class类中的内容

* constructor方法
* 实例方法
* 访问器方法
* 静态方法 static

##### 2.3. class的extends

* extends关键字
* super关键字
  * 方式一: 构造方法 super()
    * 一定在使用this之前以及返回对象之前先调用super
    * 构造函数的首行
  * 方式二: 实例方法super.method
  * 方式三: 静态方法super.staticMethod

##### 2.4. 继承自内置类

* 对内置类进行扩展
* Array.prototype.xxx

##### 2.5. 类的混入Mixin

三. babel ES6转ES5源码(不强制要求)

##### 3.1. 只写一个class类的源码

##### 3.2. class的继承extends源码

### (理解)ES6-严格面向对象中的多态表现

#### JavaScript中的多态

- **面向对象的三大特性：封装、继承、多态**
  - 继承是多态的前提
- **JavaScript有多态吗？**
  - 维基百科对多态的定义：多态（英语：polymorphism）指为不同数据类型的实体提供统一的接口，或使用一个单一的符号
    来表示多个不同的类型。
  -  非常的抽象，个人的总结：不同的数据类型进行同一个操作，表现出不同的行为，就是多态的体现。
- 那么从上面的定义来看，JavaScript是一定存在多态的(具有很强的通用性)

```javascript
class Shape{
    getArea(){}
}

//继承
class Rectangle extends Shape{
    constructor(width,height){
        super()//调用父类的构造函数或者方法
        this.width = width
        this.height = height
    }

    //重写
    getArea(){
        return this.width + this.height
    }
}

class Circle extends Shape {
    constructor(radius){
        super()
        this.radius = radius
    }

    //不满意上面的，继续重写
    get Area(){
        return this.radius * this.radius *3.14
    }
}


//创建矩形
var rect1 = new Rectangle(100,200)
var rect2 = new Rectangle(20,30)
var c1 = new Circle(10)
var c2 = new Circle(20)
// console.log(c1.Area);


//多态的表现
/*
    在严格的面向对象语言中，多态是存在如下条件的：
        1.必须有继承(实现接口)
        2.必须有父类的引用指向子类对象
*/
//shape:Shape是父类
function getShapeArea(shape:Shape){//这是TS的写法，相当于子类要继承父类的类型
    //上面TS的操作类似：var shape:Shape = rect1(父类的引用指向子类对象)
    console.log(shape.getArea());
}

//两个子类rect1跟c1传入父类shape中
getShapeArea(rect1)
getShapeArea(c1)

var obj = {
    getArea:function(){
        return 10000
    }
}

//在js中使用时正确与错误的写法
getShapeArea(rect2)//正确
getShapeArea(123)//错误，因为123并没有getArea方法，这个时候如果使用TS就可以加上静态类型检测了
```

### (掌握)ES6-JS面向对象中多态的表现形式

```javascript
//多态的表现，JS到处都是多态 
function sum(a1,a2){
    return a1 +a2
}

//我们对不同的数据类型，提供了统一的接口实现
sum(20,30)
sum("小余","学习")

//多态的表现
var foo = 123
foo = "小余给你拜新年了"
console.log(foo.split());
foo = {
    running:function(){}
}
foo.running()
foo = []
console.log(foo.lenght);
```

### (掌握)ES6-对象字面量的增强写法

- ES6中对 对象字面量 进行了增强，称之为 Enhanced object literals（增强对象字面量）
- 字面量的增强主要包括下面几部分：
  - 属性的简写：Property Shorthand
  - 方法的简写：Method Shorthand
  - 计算属性名：Computed Property Names

#### 属性的增强

```javascript
/*
    1.属性的增强
*/

var name = "小余"
var age = 20
//通常我们是这么写对象字面量的，但是通常在我们写之前，外界可能已经有name跟age这两个内部重复的内容了(比如我们在外面有个name叫小余，obj里面的name也想叫小余，那就直接简写。要是你想要叫小满，那就写上name:"小满")，也就是说如果你不写入内容，他就会追寻你在对象外面的内容
var obj = {
    name:"小余",
    age:20
}
//防止重复性的写法我们可能会这么做,这样引用的就算外面name跟age了
var obj = {
    name:name,
    age:age
}
//但是两个name跟两个age感觉还是有点多余，于是在ES6中出现了语法糖的写法，直接这样简写
var obj = {
    name,
    age
}
console.log(obj);//{ name: '小余', age: 20 }，此处的结果仅对应最后的语法糖简写方式

//导出的写法(后面会讲导出)
module.exports = {
    name,//简写
    age:age//未简写
}

//这种方式在函数中也很常见，例如
function foo(){
    var message = "Hello World"
    var info = "my name is xiaoyu"
    //在JS的函数中一次只能返回一个内容，如果你想要返回多个内容，就放到对象中
    return{
        //未简写
        // message:message,
        // info:info
        //简写
        message,info
    }
}
```

#### 方法的增强

```javascript
/*
    2.方法的增强
*/

var name = "小余"
var age = 20
//通常我们是这么写对象字面量的，但是通常在我们写之前，外界可能已经有name跟age这两个重复的内容了
var obj = {
    name,
    age,
    //1.正常的普通函数写法
    running:function(){},
    //2.箭头函数的写法(但是不会绑定this)
    eating:()=>{},
    //3.类似class的定义方法，省去了function。是属于第一种方法的简写
    swiming(){}
}

obj.running()//{name: '小余', age: 20, running: ƒ, eating: ƒ, swiming: ƒ}
obj.eating()//Window {window: Window, self: Window, document: document, name: '小余', location: Location, …}
obj.swiming()//{name: '小余', age: 20, running: ƒ, eating: ƒ, swiming: ƒ}
```

#### 计算属性名

```javascript
/*
    3.计算属性名
*/

var key = "address"

var name = "小余"
var age = 20
var obj = {
    name,
    age,
    //我们内部的属性名之前都是写死的，要是我想要上面key变量里面的内容address作为我的属性名，那需要怎么办
    //错误的写法
    // key:"广州"
    //正确的写法，使用中括号圈起来，叫做计算属性名是因为会根据我们变量对应的名称来获取真正的值
    [key]:"福建"
}
console.log(obj);//{ name: '小余', age: 20, address: '福建' }
```

```javascript
//计算属性名的能力展现
var key = "address" + " city"
var obj = {
    [key]:"福建"
}
console.log(obj);//{address city: '福建'}
```

### (掌握)ES6-数组和对象的解构以及应用

#### 解构Destructuring

- **ES6中新增了一个从数组或对象中方便获取数据的方法，称之为解构Destructuring**
  - 解构赋值 是一种特殊的语法，它使我们可以将数组或对象“拆包”至一系列变量中
- **我们可以划分为：数组的解构和对象的解构**
- **数组的解构：**
  - 基本解构过程
  - 顺序解构
  - 解构出数组：…语法
  -  默认值
- **对象的解构：**
  - 基本解构过程
  - 任意顺序
  - 重命名
  - 默认值

##### 数组解构

```javascript
var names = ["草莓","汤姆","班花姐姐","小满","超级满","挖机哥"]

//数组的解构(不使用解构方式)
// var name1 = names[0]
// var name2 = names[1]
// var name3 = names[2]
// var name4 = names[3]
// var name5 = names[4]
// var name6 = names[5]
//数组使用解构方式(会自动从头按顺序填入)，[]是固定格式
var [name1,name2,name3,name4,name5,name6] = names
console.log(name1,name2,name3,name4,name5,name6);//草莓 汤姆 班花姐姐 小满 超级满 挖机哥

//顺序问题：严格的顺序(对于我们中间不想要的属性需要留出位置)
var [name1, ,name3, ,name5,name6] = names
console.log(name1,name3,name5,name6);//草莓 班花姐姐 超级满 挖机哥

//解构出数组(前面两个单独放，后面4个我希望放在一个数组里面)，使用剩余
var [name1,name2,...newNames] = names
console.log(name1,name2,newNames);//草莓 汤姆 [ '班花姐姐', '小满', '超级满', '挖机哥' ]

//解构的默认值(如果数组里面有undefined，解构不出来的时候，可以给出默认值)，测试不生效，这里我跟coderwhy的情况发生了不一致的情况，需要明确的在某一个内容上进行赋值默认值，比如现在下面是obj2为undefined，那就需要将默认值写在obj2那里
var obj = ["小满",undefined,"康老师"]
var [obj1,obj2="小余",obj3="default"] = obj
console.log(obj1,obj2,obj3);
```

##### 对象解构

```javascript
var obj = {name:"小余",age:18,height:1.75}
//对象的解构(不使用解构方式)
// var name = obj.name
// var age = obj.age
// var height = obj.height
//对象使用解构方式(基本使用)，使用{}
var {name,age,height} = obj
console.log(name,age,height);//小余 18 1.75

//顺序问题：对象的解构是没有顺序的，根据key解构，也就是说解构的key是跟对象里面的键一一对应的。要是写错了，可就undefined了，不需要按顺序就意味着我们不用像数组解构那样要给中间不想要的属性留出空位了
var {age,height,name} = obj
console.log(age,height,name);//18 1.75 小余

//对变量进行重命名(这里对身高height部分进行重命名)，然后我们在使用的时候，就可以使用新的命名了
var {age,height:xiaoyu_height,name} = obj
console.log(xiaoyu_height);//1.75

//默认值
var {
    name,
    age,
    height,
    address="福建"
} = obj

console.log(name,age,height,address);//小余 18 1.75 福建，我们对address设置了默认值为福建
```

### (掌握)补充-函数对象的原型和Function的关系

```javascript
//new Function()
//foo.__proto__ === Function.prototype
function foo(name,age){
    console.log(this,name,age);
}

//1.对象中的某些属性和方法是来自Function.prototype
//test.__proto__ === Function.prototype
function test(){}

//foo当作一个对象，获取apply方法
foo.call("abc",["小余",20])
console.log(Function.prototype.call);//ƒ call() { [native code] }
console.log(Function.prototype.apply);//ƒ apply() { [native code] }，native意思为原生

//2.在Function.prototype中添加的属性和方法，可以被所有的函数获取
Function.prototype.info = "你好，小余"
console.log(test.info);//你好，小余
console.log(foo.info);//你好，小余
```

### (掌握)补充-手写apply-call实现和抽取封装

#### 实现

```javascript
//call的实现封装
function foo(name,age){
    console.log(this,name,age,"你调用了foo函数");
}

//foo函数可以通过apply/call
// foo.apply("aaa",["小余",20])
// foo.call("bbb","小满",24)

//1.给函数对象添加方法：
Function.prototype.hcapply = function(thisArg,...otherArgs){
    // console.log(this)//就是foo函数的代码，当前调用的函数对象
    // this()//执行foo函数，绑定window

    //获取thisArg，并且确保是一个对象类型。这样不管你是null还是undefined或者是数字都没有问题
    thisArg = (thisArg === null || thisArg === undefined) ? window : Object(thisArg)

    thisArg.fn = this
    Object.defineProperty(thisArg,"fn",{
        value:this,
        configurable:true,
        writable:false//这种写法默认已经不可写了，所有writable可写可不写
    })
    thisArg.fn(...otherArgs)//隐式绑定执行，fn: ƒ会显式出来，如果想要隐藏的话，就可以设置不可写
    delete thisArg.fn//用完删掉
}

foo.hcapply({name:"小余"})//要是对象形式的，不是我们传入对象形式，就是内部要处理成对象形式
foo.hcapply(null)
foo.hcapply(123)
```

```javascript
//apply也差不多，就传入的是数组形式的，所以传入的只有一个数组，不需要解构
function foo(name,age){
    console.log(this,name,age);
}

//foo函数可以通过apply/call
// foo.apply("aaa",["小余",20])
// foo.call("bbb","小满",24)

//1.给函数对象添加方法：
Function.prototype.hcapply = function(thisArg,otherArgs){
    // console.log(this)//就是foo函数的代码，当前调用的函数对象
    // this()//执行foo函数，绑定window

    //获取thisArg，并且确保是一个对象类型。这样不管你是null还是undefined或者是数字都没有问题
    thisArg = (thisArg === null || thisArg === undefined) ? window : Object(thisArg)

    //thisArg.fn = this
    Object.defineProperty(thisArg,"fn",{
        enumerable:false,
        value:this,
        configurable:true,
        writable:false//这种写法默认已经不可写了，所有writable可写可不写
    })
    thisArg.fn(...otherArgs)//隐式绑定执行，fn: ƒ会显式出来，如果想要隐藏的话，就可以设置不可写
    delete thisArg.fn//用完删掉
}

foo.hcapply({name:"小余"},["小满",24])//要是对象形式的，不是我们传入对象形式，就是内部要处理成对象形式
foo.hcapply(123,["洛洛",20])
```

#### 抽取封装

```javascript
//将手写的hccall跟hcapply相同的地方进行了封装
function exefn(thisArg,otherArgs,fn){
    thisArg = (thisArg === null || thisArg === undefined) ? window : Object(thisArg)

    Object.defineProperty(thisArg,"fn",{
        enumerable:false,
        value:fn,
        configurable:true,
        writable:false
    })
    thisArg.fn(...otherArgs)
    delete thisArg.fn
}

function foo(name,age){
    console.log(this,name,age);
}

//封装过的代码就非常的简洁
Function.prototype.hcapply = function(thisArg,otherArgs){
    exefn(thisArg,otherArgs,this)
}
Function.prototype.hccall = function(thisArg,...otherArgs){
    exefn(thisArg,otherArgs,this)
}

foo.hcapply("Person",["小余",20])
foo.hccall("Person","小满",24)
```

> 但是上面还有个小缺点，那就是exefn函数要是不在跟hccall这些同一个文件的话，那岂不是还需要进行引入之类的操作。那这个时候我们就可以将这个函数放入原型链中进行复用

```javascript
//上面是封装到独立的函数中，这里是封装到原型链中
Function.prototype.exefn = function(thisArg,otherArgs){
    thisArg = (thisArg === null || thisArg === undefined) ? window : Object(thisArg)

    Object.defineProperty(thisArg,"fn",{
        enumerable:false,
        value:this,
        configurable:true,
        writable:false
    })
    thisArg.fn(...otherArgs)
    delete thisArg.fn
}

function foo(name,age){
    console.log(this,name,age);
}

//我们通过原型链封装是直接通过this调用，this调用会将exefn隐式绑定到hcapply或者hccall身上。这取决于我们从哪里调用，那就在上面的封装中可以少传入一个fn参数，value可以直接绑定this
Function.prototype.hcapply = function(thisArg,otherArgs){
    this.exefn(thisArg,otherArgs)
}
Function.prototype.hccall = function(thisArg,...otherArgs){
    this.exefn(thisArg,otherArgs)
}

foo.hcapply("Person",["小余",20])
foo.hccall("Person","小满",24)
```

### (掌握)补充-手写bind函数的实现

```javascript
function foo(name,age,sex){
    console.log(this,name,age,sex);
}
Function.prototype.hcbind = function(thisArg,...otherArgs){
    thisArg = (thisArg === undefined || thisArg === null) ? window : Object(thisArg)
    Object.defineProperty(thisArg,"fn",{
        enumerable:false,
        configurable:true,
        writable:false,
        value:this
    })
    return (...newArgs)=>{
        var allArgs = [...otherArgs,...newArgs]
        thisArg.fn(...allArgs)
        //delete this.fn,这里不能这么做，你这样做就不能没调用完的后面继续调用了，因为我们调用完就删掉了，下次就找不到了
    }
}

var newFoo = foo.hcbind({name:"小余"},"小余",20)
newFoo("男")
```

## ES6~ES13新特性（一）

### (理解)新ECMA文档中代码执行术语解析

#### 新的ECMA代码执行描述

-  在执行学习JavaScript代码执行过程中，我们学习了很多ECMA文档的术语：
  -  **执行上下文栈**：Execution Context Stack，用于执行上下文的栈结构；
  -  **执行上下文**：Execution Context，代码在执行之前会先创建对应的执行上下文；
  -  **变量对象**：Variable Object，上下文关联的VO对象，用于记录函数和变量声明；
  -  **全局对象**：Global Object，全局执行上下文关联的VO对象；
  -  **激活对象**：Activation Object，函数执行上下文关联的VO对象；
  -  **作用域链**：scope chain，作用域链，用于关联指向上下文的变量查找；

- **在新的ECMA代码执行描述中（ES5以及之上），对于代码的执行流程描述改成了另外的一些词汇：**
  -  基本思路是相同的，只是**对于一些词汇的描述发生了改变**；
  -  **执行上下文栈和执行上下文**也是相同的；

####  词法环境（Lexical Environments）

- 词法环境是一种规范类型，用于在词法嵌套结构中定义关联的变量、函数等标识符；
  - 一个词法环境是由环境记录（Environment Record）和一个外部词法环境（outer Lexical Environment）组成
  - 一个词法环境经常用于关联一个函数声明、代码块语句、try-catch语句，当它们的代码被执行时，词法环境被创建出来
-  也就是在ES5之后，执行一个代码，通常会关联对应的词法环境；
  -  那么执行上下文会关联哪些词法环境呢？

![image-20230126120728755](.\JavaScript高级_image\image-20230126120728755.png)

#### LexicalEnvironment和VariableEnvironment

- LexicalEnvironment(**词法环境**)用于处理let、const声明的标识符：

![image-20230126120756114](.\JavaScript高级_image\image-20230126120756114.png)

> 翻译：
>
> let和const声明定义了作用域为运行执行上下文的LexicalEnvironment的变量。变量是创建时，它们包含的词法环境被实例化，但可能不能以任何方式访问，直到变量的对LexicalBinding进行计算。由带有初始化器的LexicalBinding定义的变量被赋予其初始化器的值赋值表达式是在LexicalBinding被求值时，而不是在变量被创建时。如果在let声明中使用LexicalBinding没有初始化器，则变量在LexicalBinding计算时被赋值为undefined。

- VariableEnvironment(变量环境)用于处理var和function声明的标识符：

![image-20230126120814150](.\JavaScript高级_image\image-20230126120814150.png)

> 翻译：
>
> var语句声明了运行执行上下文的VariableEnvironment范围内的变量。Var变量被创建当它们所包含的词汇环境被实例化并在创建时初始化为未定义时。在任何范围内一个通用的Bindingldentifier VariableEnvironment可以出现在多个VariableDeclaration中，但这些声明可以一起出现只定义一个变量。由带有初始化器的VariableDeclaration定义的变量被赋给其初始化器的值
> AssignmentExpression是在执行VariableDeclaration时，而不是在创建变量时

#### 环境记录（Environment Record）

- **在这个规范中有两种主要的环境记录值:声明式环境记录和对象环境记录**
  - 声明式环境记录：声明性环境记录用于定义ECMAScript语言语法元素的效果，如函数声明、变量声明和直接将标识符绑定与
    ECMAScript语言值关联起来的Catch子句
  - 对象式环境记录：对象环境记录用于定义ECMAScript元素的效果，例如WithStatement，它将标识符绑定与某些对象的属性
    关联起来

![image-20230126120900723](.\JavaScript高级_image\image-20230126120900723.png)

> 翻译：
>
> 在本规范中使用了两种主要类型的环境记录值:声明性环境记录和对象环境记录。声明性环境记录用于定义ECMAScript语言语法元素的效果，如FunctionDeclarations、VariableDeclarations，以及直接将标识符绑定与ECMAScript语言值关联的Catch子句。对象环境记录包括用于定义ECMAScript元素的效果，例如将标识符绑定与某些对象的属性相关联的WithStatement。全球环境记录和函数环境记录是专门用于脚本全局声明和top-的专门化函数中的级别声明。

#### 新ECMA描述内存图

![image-20230126122639866](.\JavaScript高级_image\image-20230126122639866.png)

- 这个Global Execution Context，在Environment Record中是存在两个空间的，var跟let的是分开放的

### (掌握)let-cosnt的基本使用和注意事项

- **在ES5中我们声明变量都是使用的var关键字，从ES6开始新增了两个关键字可以声明变量：let、const**
  - **let、const在其他编程语言中都是有的**，所以也并不是新鲜的关键字；
  - 但是**let、const确确实实给JavaScript带来一些不一样的东西**；
- **let关键字：**
  - 从直观的角度来说，**let和var是没有太大的区别**的，都是**用于声明一个变量**；
- **const关键字：**
  - const关键字是**constant的单词的缩写，表示常量、衡量**的意思
  - 它表示**保存的数据一旦被赋值，就不能被修改**
  - 但是**如果赋值的是引用类型，那么可以通过引用找到对应的对象，修改对象**的内容
- 注意：
  - 另外let、const不允许重复声明变量；

> JavaScript中的引用类型有以下几种:
>
> 1. Object: 对象是 JavaScript 中最常用的引用类型。对象可以是一个键值对的集合，可以用来存储各种类型的数据。
> 2. Array: 数组是一种特殊的对象，用于存储一组有序的数据。
> 3. Function: 函数是可以被调用的对象，它可以接收参数并返回结果。
> 4. Date: 日期是用于表示日期和时间的对象。
> 5. RegExp: 正则表达式是用于在字符串中执行模式匹配的对象。
> 6. Map: Map 是一种用于存储键值对的数据结构
> 7. Set: Set 是一种用于存储单独的项目的数据结构
>
> 这些都是JavaScript中的引用类型, 是对象类型，是非基本类型。

```javascript
//var变量提升，声明前访问也只会undefined
console.log(foo);const info = {
    name:"小余"
}
info.name = "大满"
console.log(info.name);
//info = {}这样info是会报错的，因为info指向发生了改变，我们info是通过const赋值，不能改变
console.log(info);
var foo = 123
//let声明之前访问报错
console.log(foo);
let foo = 123

//const的内容确定后不可改变，除非赋值的是引用类型，那么可以通过引用找到对应的对象，修改对象
const message = "Hello World"
message = "小余"
console.log(message);//报错

let message = "Hello World"
message = "小余"
console.log(message);//小余

//const 赋值引用类型
const info = {
    name:"小余"
}
info.name = "大满"
console.log(info.name);
//info = {}这样info是会报错的，因为info指向发生了改变，我们info是通过const赋值，不能改变
console.log(info);
```

### (掌握)let-const没有作用域提升和暂时性死区

#### let/const作用域提升

-  **let、const和var的另一个重要区别是作用域提升：**
  -  我们知道**var声明的变量是会进行作用域提升**的；
  - 但是如果我们使用let声明的变量，在声明之前访问会报错

- **那么是不是意味着foo变量只有在代码执行阶段才会创建的呢？**

  - 事实上并不是这样的，我们可以看一下ECMA262对let和const的描述

  -  **这些变量会被创建在包含他们的词法环境被实例化时，但是是不可以访问它们的，直到词法绑定被求值**

    > 有像var那样被提前创建出来，但是在赋值之前不能像var那样被访问还能返回undefined，这是在赋值之前不能够被访问的

![image-20230126132820583](.\JavaScript高级_image\image-20230126132820583.png)

> 翻译：
>
> Let和const声明定义了作用域为运行执行上下文的变量LexicalEnvironment。当包含变量的词法环境为时，将创建变量实例化，但在变量的LexicalBinding被求值之前不能以任何方式访问。由带有初始化器的LexicalBinding定义的变量被赋予其初始化器的值赋值表达式是在LexicalBinding被求值时，而不是在变量被创建时。如果一个let声明中的LexicalBinding没有初始化器，变量被赋值在计算LexicalBinding时未定义。

#### 暂时性死区 (TDZ)

-  **我们知道，在let、const定义的标识符真正执行到声明的代码之前，是不能被访问的**

  - **从块作用域的顶部一直到变量声明完成之前**，这个变量处在**暂时性死区（TDZ，temporal dead zone）**

    > 通俗的说就是这段访问不了的区域叫做暂时性死区

![image-20230126162353690](.\JavaScript高级_image\image-20230126162353690.png)

```javascript
//暂时性死区
function foo(){
    console.log("小余");
    console.log("测试");
    // console.log(bar);

    let bar = "bar"
    let good = "好的"
}
```

- 使用术语 “temporal” 是因为区域取决于执行顺序（时间），而不是编写代码的位置；

```javascript
//暂时性死区和定义的位置没有关系，和代码执行的顺序有关系
function foo(){
    console.log(message);
}
let message = "Hello World"

foo()//Hello World，foo函数定义在message的上面，但是依旧可以打印出来内容不报错，因为我们是在let message的后面进行调用的
console.log(message);//Hello World
```

```javascript
//暂时性死区形成之后，在该区域内这个标识符不能访问
let message = "Hello World"
function foo(){
    console.log(message);//能访问到吗？很显然不行，我们在很早之前作用域的时候也做过类似的操作，在控制台打印message，他会先在自己的当前作用域寻找，message其实在这里面已经形成，词法坏境实例化，只是不能访问，message是在内部存在的，这就阻止了控制台打印的message继续向外层作用域寻找(虽然外层作用域有，但是已经在内层就被拦截住了)

    let message = "小余"
}

foo()//报错啊啦啦
console.log(message);
```

#### let/const有没有作用域提升呢？

- 从上面我们可以看出，在**执行上下文的词法环境创建出来的时候，变量事实上已经被创建**了，只是这个**变量是不能被访问**的
  - 那么变量已经有了，但是不能被访问，是不是一种作用域的提升呢？
- **事实上维基百科并没有对作用域提升有严格的概念解释，那么我们自己从字面量上理解；**
  - 作用域提升：在**声明变量的作用域**中，如果**这个变量可以在声明之前被访问，那么我们可以称之为作用域提升**；
  - 在这里，它虽然被创建出来了，但是不能被访问，我认为不能称之为作用域提升；
- 所以我的观点是**let、const没有进行作用域提升，但是会在解析阶段被创建出来**

### (理解)let-const变量不会添加到window上

#### Window对象添加属性

- **我们知道，在全局通过var来声明一个变量，事实上会在window上添加一个属性：**
  -  但是let、const是不会给window上添加任何属性的。
- 那么我们可能会想这个变量是保存在哪里呢？
  - 放到了环境记录里面，这个环境记录不等于window

> 翻译：
>
> 全局环境记录在逻辑上是一条记录，但它被指定为封装的复合记录对象环境记录和声明性环境记录。对象环境记录具有as
>
> **这里的对象环境记录其实就是指window了**
>
> - 表19:全局环境记录的附加字段(不一定正确，机翻的)
>
> | 字段名                 | 值                                   | 意思                                                         |
> | ---------------------- | ------------------------------------ | ------------------------------------------------------------ |
> | [[ObjectRecord]]       | Object<br>Encironment<br>Record      | 绑定对象是全局对象。它包含全局内置绑定以及FunctionDeclaration(函数声明)<br/>GeneratorDeclaration、AsyncFunctionDeclaration AsyncGeneratorDeclaration, VariableDeclaration<br/>关联领域的全局代码中的定位。 |
> | [[GlobalThisValue]]    | Object                               | this在全局作用域中返回的值。主机可以提供任何ECMAScript Object值。 |
> | [[DeclarativeRecored]] | Declarative<br>Environment<br>Record | 包含关联领域代码的全局代码中所有声明的绑定<br/>FunctionDeclaration、GeneratorDeclaration AsyncFunctionDeclaration AsyncGeneratorDeclaration,和VariableDeclaration绑定。 |
> | [[VarNames]]           | List of String                       | 由FunctionDeclaration、GeneratorDeclaration、AsyncFunctionDeclaration、<br/>的全局代码中的AsyncGeneratorDeclaration和VariableDeclaration声明<br/>领域 |
>
> 

![image-20230126205017470](.\JavaScript高级_image\image-20230126205017470.png)

```javascript
//拿一个很久之前的案例举例
var name = "小余"
//window.name = "" //置为空，因为默认window下就是空内容
console.log(name);//写完运行打印出来的name是小余，这个没有问题。但是当年把上面那行注释掉的时候，name依旧可以在控制台打印出来，因为已经被放入window中了，想要name恢复原来的就需要置为空

//但是这些问题在let跟const中就不会有了。PS:如果你实验过上面的案例，这个时候要么将name置为空，要么重新开当前界面，不然当你let的name注释掉，会打印出你刚刚通过var声明赋值的内容
let name = "大余"
console.log(name);
```

### (掌握)let-const具备块级作用域

#### var的块级作用域

-  **在我们前面的学习中，JavaScript只会形成两个作用域：全局作用域和函数作用域**

![image-20230126225700596](.\JavaScript高级_image\image-20230126225700596.png)

- **ES5中放到一个代码中定义的变量，外面是可以访问的：**

> 通过`var`声明的变量或者非严格模式下(non-strict mode)创建的函数声明没有块级作用域。在语句块里声明的变量的作用域不仅是其所在的函数或者script标签内，所设置变量的影响会在超出语句块本身之外持续存在。换句话说，这种语句块不会引入一个作用域。尽管单独的语句块是合法的语句，但在JavaScript中你不会想使用单独的语句

```javascript
//var没有块级作用域
{
    var foo = "小余"
}

console.log(foo);//foo，正常打印，代码块中并没有阻挡住var声明出来的foo
```

#### let/const的块级作用域

- **在ES6中新增了块级作用域，并且通过let、const、function、class声明的标识符是具备块级作用域的限制的：**

```javascript
{//形成作用域了
    let age = 18
    const name = "小余"
}

console.log(name,age);//报错，无法访问，说明{}形成的作用域块将let跟class限制住了
```

- **但是我们会发现函数拥有块级作用域，但是外面依然是可以访问的：**
  - 这是因为**V8引擎会对函数的声明进行特殊的处理**，允许像var那样在外界直接访问
  - 提升的原因：理论来说不应该允许像var那样在外界直接访问的，因为这个也属于糟粕，不符合规范，但是早期的代码有不少的function是写在{}的里面的，如果不允许在外界访问，那会造成以前的代码库大量的报错，而现在依旧有很多的代码会去引用那些以前的代码库，如果报错就需要去修改别人库的源码，这是一件成本很高的事情，因为很多旧的库都没有人维护了。所以允许像var那样在外界直接访问的原因是为了兼容以前的代码
  - 但是function跟var依旧有区别的，那就是var可以在声明地方的上方进行调用(输出undefined)，但是function不行(会报错)

### (掌握)let-const块级作用域的应用场景

#### 块级作用域的应用

代码块的词法环境执行中：

![image-20230127015726422](.\JavaScript高级_image\image-20230127015726422.png)

代码块的词法环境执行结束：

![image-20230127020021141](.\JavaScript高级_image\image-20230127020021141.png)

> 1. 全局上下文会指向(创建)一个词法环境，这个词法环境里面包含了`环境记录`、`outer:null`
>
> 2. `环境记录`会指向`全局环境记录`，`全局环境记录`里面有`声明式环境记录`和`对象式环境记录window`
>
> 3. 那我们就开始对这个进行了分门别类
>
>    - 声明式环境记录：address
>    - 对象式环境记录：message、age、foo、height( height是var声明，不受{}限制 )
>
> 4. 提示：{}代码块不会形成新的执行上下文的，而是会创建对应的词法环境
>
>    所以代码块的词法环境就创建了出来，他也有属于自己的`环境记录`和`outer`
>
> 5. 代码块的词法环境：`outer：指向全局的词法环境`，`环境记录：代码块的环境记录`
>
>    其中`代码块的环境记录：title、info`
>
> 6. 我们在执行代码块的过程时候看似好像没有人指向它({}代码块的词法环境)，但其实这个时候是由执行上下文进行指向的，所以这个代码块的内容是不会被销毁的
>
> 7. 当我们{}代码块的内容执行完的时候，就不再被全局执行上下文指向了，然后就被销毁掉了，然后{}里面的height属性依旧可以访问，那是因为我们这个变量是使用var声明的，是存储到了全局的对象式环境记录(window)里面了

```javascript
//形成的词法环境
var message = "Hello 小余"
var age = 20
function foo(){}
let address = "福建"

{
    var height = 1.75

    let title = "老师"
    let info = "了解真相"
}
```

#### 按钮的案例

```javascript
//HTML文件
    <button>按钮1</button>
    <button>按钮2</button>
    <button>按钮3</button>
    <button>按钮4</button>
//JS文件
const btnEls = document.querySelectorAll('button')
for(var i = 0;i < btnEls.length;i++){
    var btnEl = btnEls[i]
    btnEl.onclick = function(){
        //console.log("点击了按钮");
        console.log(`点击了按钮${i}`);
    }
}

console.log(i);
//永远是4，我们通过了var声明，那for内部声明的i会直接作用在全局上下文的环境记录里的对象式环境记录window里面，所以每一次for循环i的刷新，都会覆盖掉上一次的结果，最终留下的只是最后循环次数的结果。所以如果我们想用i来测试我们按下去的是哪个按钮，使用var声明是行不通的，因为不管按哪个按钮，最终i的结果都是最好循环的结果，是没办法知道我们具体是按下哪个按钮的
//通过let将i限制在for循环带的{}代码块的环境记录里面，i无法传递到外面，每次刷新得到的都是目前的i
//那此时最外层的console.log(i);就需要去掉了，因为id
```

```javascript
//使用this的取巧方式
const btnEls = document.querySelectorAll('button')
for(var i = 0;i < btnEls.length;i++){
    var btnEl = btnEls[i]
    btnEl.index = i+1
    btnEl.onclick = function(){
        console.log(`点击了${this.index}按钮`);
    }
}

console.log(i);
```

```javascript
//立即执行函数：(XXX)(i)
const btnEls = document.querySelectorAll('button')
for(var i = 0;i < btnEls.length;i++){
    var btnEl = btnEls[i];//这里要加分号，不然会出现语法错误，V8引擎误认为上面跟下面的代码是一起的
    // console.log(btnEl);
    (function(m){//通过函数多加一层作用域跟闭包，这里最后产生的是4个对象，就每次收集到是i都是不会互相干扰的(因为都在不同的对象里，收到了一个i就立马执行，然后创建出下一个新的对象准备接受下一个i)
        btnEl.onclick = function(){
            console.log(`点击了${m}按钮`);
        }
    })(i)
}

console.log(i);
```

![image-20230127033249268](.\JavaScript高级_image\image-20230127033249268.png)

```javascript
//最终简单的let写法
//[[scope]]会将闭包的结果保留下来，造成词法环境不被销毁
const btnEls = document.querySelectorAll('button')
for(let i = 0;i < btnEls.length;i++){//形成新的词法环境了
    //debugger测试使用，能看到block的块级作用域，每次的块内容都不一样的，通过let将i限制在for循环带的{}代码块的环境记录里面，i无法传递到外面，每次刷新得到的都是目前的i
    const btnEl = btnEls[i]
    btnEl.onclick = function(){
        console.log(`点击了${i}按钮`);
    }
}
```

### (掌握)var-let-const开发中的选择

- **那么在开发中，我们到底应该选择使用哪一种方式来定义我们的变量呢？**
- **对于var的使用：**
  -  我们需要明白一个事实，var所表现出来的特殊性：比如**作用域提升、window全局对象、没有块级作用域**等都是**一些历史遗留问题**；
  -  其实是**JavaScript在设计之初的一种语言缺陷**
  - 当然目前市场上也在**利用这种缺陷出一系列的面试题，来考察大家对JavaScript语言本身以及底层的理解**
  - 但是在实际工作中，我们**可以使用最新的规范来编写，也就是不再使用var来定义变量**了
- **对于let、const：**
  - 对于let和const来说，是目前开发中推荐使用的
  - 我们会**优先推荐使用const**，这样可以**保证数据的安全性不会被随意的篡改**
  - 只有当**我们明确知道一个变量后续会需要被重新赋值**时，这个时候**再使用let**
  - 这种在很多**其他语言里面也都是一种约定俗成的规范**，尽量我们也遵守这种规范

## ES6~ES13新特性 ( 二 )

### ES6-模板字符串-展开运算符-ES7-ES11

### (掌握)模板字符串-标签模板字符串的用法和应用

#### 字符串模板基本使用

- **在ES6之前，如果我们想要将字符串和一些动态的变量（标识符）拼接到一起，是非常麻烦和丑陋的（ugly）**
-  **ES6允许我们使用字符串模板来嵌入JS的变量或者表达式来进行拼接：**
  -  首先，我们会使用 `` 符号来编写字符串，称之为模板字符串
  -  其次，在模板字符串中，我们可以通过 ${expression} 来嵌入动态的内容；

```javascript
//ES6之前的拼接
const name = "小余"
const age = 20

const info = "今天来的人是" + name + "他今年" + age + "岁了"
console.log(info)
//ES6之后的拼接(模板字符串)
const info = `今天来的人是${name}他今年${age}岁了`
console.log(info)
```

#### 标签模板字符串使用

- **模板字符串还有另外一种用法：标签模板字符串（Tagged Template Literals）**
- **我们一起来看一个普通的JavaScript的函数：**

```javascript
function foo(...args){//不确定传入多少个参数，使用剩余参数
    console.log(args)
}

foo("Hello World")//[ 'Hello World' ]，是放到数组里面的
```

- **如果我们使用标签模板字符串，并且在调用的时候插入其他的变量：**
  - **模板字符串被拆分**了；
  - 第一个元素是**数组**，是**被模块字符串拆分的字符串组合**
  - **后面的元素是一个个模块字符串传入的内容**

```javascript
function foo(...args){
  console.log(args)
}
foo`小余今天抓到一只奇怪的小满`//可以使用模板字符串进行调用函数
```

![image-20230127170318341](.\JavaScript高级_image\image-20230127170318341.png)

> 标签模板字符串是 JavaScript 中一种新的字符串模板语法，用于将字符串和变量结合起来。它使用反引号（`）来标识字符串，并在字符串中使用 ${expression} 来插入变量。标签模板字符串可以更好地控制字符串插值的语法，并且可以使用函数来自定义字符串的构建过程。

```javascript
const name = "小余"
const age = 20
function foo(...args){
  console.log(args)
}
//标签模板字符串的用法
foo`今天${name}，抓到一只等级为${age}的奇怪小满`
//这样在反引号内的会被进行分割，1.今天	2.${name}	3.抓到一只等级为	4.${age}	5.的奇怪小满
//一共分为了5块，3块是被模板字符串name跟age分开的句子，2块是name跟age模板字符串。根据这个我们就能够自己去进行自由拼接这5块内容，动态插入东西自由度会更高
```

![image-20230127171721708](.\JavaScript高级_image\image-20230127171721708.png)

#### React的styled-components库

> 标签模板字符串会用在这里，以后就会用到
>
> all in js的思想，就是所有东西都写在js里面，包括了html in js，css in js ，js in js
>
> 而这些将东西写在js文件里面就是需要依赖styled-components这个react库

![image-20230127172719714](.\JavaScript高级_image\image-20230127172719714.png)

### (掌握)函数增强-默认参数的用法和注意事项

- **在ES6之前，我们编写的函数参数是没有默认值的，所以我们在编写函数时，如果有下面的需求：**
  - 传入了参数，那么使用传入的参数
  - 没有传入参数，那么使用一个默认值
- **而在ES6中，我们允许给函数一个默认值**

```javascript
function foo(name,age){
  console.log(name,age);//如果我们name跟age没有传值，会默认是undefined，但这个undefined是比较危险的，因为undefined调用的时候会报错
}
foo()//未传值，undefined undefined
```

```javascript
//默认参数写法(最简洁方便)，判断逻辑跟三元运算符差不多，通过转化代码，我们能看到这写法不会对null做出处理的，输入null，返回的还会是null，而不是默认值
function foo(name="小余",age=20){
  console.log(name,age);
}
foo()//未传值，小余 20

//转化为ES5的代码
"use strict";

function foo() {
  var name =
    arguments.length > 0 && arguments[0] !== undefined ? arguments[0] : "小余";
  var age =
    arguments.length > 1 && arguments[1] !== undefined ? arguments[1] : 20;
  console.log(name, age);
}
foo(); //未传值，小余 20
```

```javascript
//以前的写法
function foo(name,age){
    //默认值写法1：
    name = name ? name : "我是默认值"
    //默认值写法2：(不严谨，如果用户传0的话，也会被当作false，从而返回"我是默认值")
    name = name || "我是默认值"
    
    //严谨的写法
    //三元运算符
    name = (name === undefined || name === null) ? "我是默认值" : name
    //ES6之后新增语法：??
    name = name ?? "我是默认值"//name为undefined或者null的时候会返回"我是默认值"，用户传入其他内容就返回内容
}
```

#### 函数默认值的补充

-  **另外参数的默认值我们通常会将其放到最后（在很多语言中，如果不放到最后其实会报错的）：**
  -  但是JavaScript**允许不将其放到最后，但是意味着还是会按照顺序来匹配**
  - **有默认参数的形参，是不会放到lenght之内的**
  - 剩余参数也是放到后面的，那剩余参数和默认参数都有要怎么放？剩余参数放最后
- **另外默认值会改变函数的length的个数，默认值以及后面的参数都不计算在length之内了**

```javascript
function foo(name,age,sex="男"){//有默认参数的要放到最后面，不然不规范
    console.log(name,age,sex)
}
console.log(foo.length);//2

//默认值会改变函数的length的个数，默认值以及后面的参数都不计算在length之内了
//所以这就是为什么默认参数要放到最后面的原因
function bar(sex="女",name,age){//有默认参数的要放到最后面，不然不规范
    console.log(name,age,sex)
}
console.log(bar.length);//0

function about(age,name="小余",...args){//默认参数需要放在剩余参数前面
    console.log(age,name)
}
console.log()
```

### (掌握)函数增强-默认参数和对象解构的结合

- **默认值也可以和解构一起来使用**：

```javascript
//解构的回顾
const obj = {name:"小余",age:20}
const {age} = obj
console.log(age);//20

//函数的默认值是一个对象，但是用户没有传递一个对象进行，怎么做？
function foo(obj = { name:"小余", age:20}){//通过默认参数的方式传进去一个对象{}，还能进行设置默认值
  console.log(obj);
  //麻烦的写法，还需要从obj中进行一次引用，如果数量多了，就会非常重复
  console.log(obj.name,obj.age);
}

foo()
```

```javascript
//上面麻烦使用的优化写法，但是判断条件有点长，而且阅读性不算很好
function foo({name,age} = { name:"小余", age:20}){
  console.log(name,age);
}
foo()

//进一步优化，一样是可以解构出来的，默认值也可以和解构的组合写法，如果传进来的没有name跟age，就默认给值，如果传进来的有name跟age，就进行解构然后使用用户传进来的值
function foo({name = "小余",age = 20} = {}){//{}是默认传入的，然后将需要的解构出来
  console.log(name,age);
}
foo()//小余 20
const obj = {name:"大满",age:24}
foo(obj)//大满 24
```

```javascript
//自己进一步优化
//但是上面这种方式，你能调用的内容就仅限于你解构出来的内容，不能够obj.xxx的进行调用其他没有解构出来的部分。你这个时候就可以使用剩余参数将剩下的没有进行解构的部分给传递进去
function foo({name = "小余",age = 20,...args} = {}){
  console.log(name,age);//大满 24
  console.log(args.sex);//男，你看这样就可以解决不知道用户传递什么内容进来，无法使用obj.xxx进行输入的问题了
}
const obj = {name:"大满",age:24,sex:"男"}
foo(obj)
```

### (掌握)函数增强-箭头函数的额外强调

#### 函数的剩余参数（已经学习）

- **ES6中引用了rest parameter，可以将不定数量的参数放入到一个数组中：**

  - 如果最后一个参数是 ... 为前缀的，那么它会将剩余的参数放到该参数中，并且作为一个数组；

  ```javascript
  function foo(m,n,...args){
      console.log(m,n)
      console.log(args)
  }
  ```

- **那么剩余参数和arguments有什么区别呢？**

  -  剩余参数只包含那些**没有对应形参的实参**，而 **arguments 对象包含了传给函数的所有实参**；
  - **arguments对象不是一个真正的数组**，而**rest参数是一个真正的数组**，可以进行数组的所有操作
  - arguments是**早期的ECMAScript**中为了方便去获取所有的参数提供的一个数据结构，而rest参数是ES6中提供并且希望以此来替代arguments的

- **注意：剩余参数必须放到最后一个位置，否则会报错**

#### 函数箭头函数的补充

- **在前面我们已经学习了箭头函数的用法，这里进行一些补充：**
  - 箭头函数是**没有显式原型prototype**的，所以**不能作为构造函数，使用new来创建对象**
  - 箭头函数也**不绑定this、arguments、super参数**
  - 以后要使用类，就不再使用function了，而是使用class

![image-20230127213029474](.\JavaScript高级_image\image-20230127213029474.png)

```javascript
//function定义的函数是有两个原型的
function foo(){}
console.log(foo.prototype);//浏览器打印：{constructor: ƒ}，node打印：{}
console.log(foo.__proto__ === Function.prototype);//true

// 箭头函数是没有显式原型的
const bar = () => {}
  //这是个函数，所以会有隐式原型
  console.log(bar.__proto__ === Function.prototype);//true
  //但是箭头函数却没有显式原型的
console.log(bar.prototype);//undefined

//所以这个证明了一点，那就是使用箭头函数是不能够new出构造函数的，因为构造函数就是产生一个对象，然后将函数身上的显式原型prototype赋值过去，如果连显式原型都没有，那自然无法赋值
const p1 = new bar()//报错：Uncaught TypeError: bar is not a constructor

//以后如果你想要使用的话，就使用class进行替代了构造函数的使用，class里面的配置就已经包括了constructor了
```

### (掌握)展开语法-展开语法的基本使用

- 展开语法(Spread syntax)：
  -  可以在函数调用/数组构造时，将数组表达式或者string在语法层面展开；
  -  还可以在构造字面量对象时, 将对象表达式按key-value的方式展开；
- 展开语法的场景：
  - 在**函数调用**时使用；
  - 在**数组构造**时使用
  -  在**构建对象字面量**时，也可以使用展开运算符，这个是在ES2018（ES9）中添加的新特性
- 注意：展开运算符其实是一种浅拷贝

```javascript
//1.基础使用
const names = ["小余","小满","洛佬","喜多川"]
function foo(name1,name2,...args){
  console.log(name1,name2,args);
}

foo(...names)
//或者也可以直接展开字符，不过用得很少
const str = "Hello"
foo(...str)
```

![image-20230127232624449](.\JavaScript高级_image\image-20230127232624449.png)

![image-20230127232804140](.\JavaScript高级_image\image-20230127232804140.png)

```javascript
//ES9(ES2018)后新增的字面量方式
function foo(name1,name2,...args){
  console.log(name1,name2,args);
}

const obj = {
  name:"小余",
  age:20
}

//只能在构建字面量的时候进行使用
const obj2 = {
  ...obj,
  sex:"男",
  address:"福建"
}
console.log(obj2);//{name: '小余', age: 20, sex: '男', address: '福建'}

//foo(...obj)，错误使用方式
//在函数的调用时，用展开运算符，将对应的展开数据，进行迭代
//可迭代对象：数据/string/arguments

//有点像我们之前拼接数组的时候做的
const names = ["xiaoyu","小满","大满"]
const names2 = [...names,"园长","cool"]
console.log(names2);// ['xiaoyu', '小满', '大满', '园长', 'cool']
```

![image-20230127233617278](.\JavaScript高级_image\image-20230127233617278.png)

### (掌握)对象的引用赋值-浅拷贝-深拷贝

#### 浅拷贝跟深拷贝的概念

##### 浅拷贝

前置知识：

- 两个对象指向同一地址， 用 `==` 运算符作比较会返回 true。
- 两个对象指向不同地址， 用 `==` 运算符作比较会返回 false。

> 引用赋值

![image-20230128020403710](.\JavaScript高级_image\image-20230128020403710.png)

```javascript
const obj = {
  name:"小余",
  age:20,
  sex:"男"
}

//引用赋值的方式
let info = obj//这是深拷贝，obj不是将内容赋值给info，而是将obj指针指向了info，这样改变了info的同时也会改变obj的内容，因为他们指向的时同一个地方
console.log(info);//{name: '小余', age: 20, sex: '男'}
info.address = "福建"
//注意我此时改变的是info，打印的是obj，但是却把对info添加属性的内容也打印了出来就证明了深拷贝这一点
console.log(obj);//{name: '小余', age: 20, sex: '男', address: '福建'}
```

```javascript
//我写的问题，考验你前面的掌握程度
const obj = {
  name:"小余",
  age:20,
  sex:"男"
}

//引用赋值的方式
let info = obj
info.name = "小满"
console.log(obj.name === info.name)//答案？true，为什么const常量明明不可改变还是相等？
```

浅拷贝只放原始类型的内存图：

![image-20230128020958664](.\JavaScript高级_image\image-20230128020958664.png)

进一步理解：

![image-20230128021216362](.\JavaScript高级_image\image-20230128021216362.png)

```javascript
const obj = {
  name:"小余",
  age:20
}

//浅拷贝
const info2 = {
  ...obj 
}

info2.name = "小满"
//很显然，对info2的改变没有影响到obj
console.log(`obj.name:${obj.name},info.name:${info2.name}`);//obj.name:小余,info.name:小满
```

##### 深拷贝(JSON做法)

- 方式1：第三方库
- 方式2：自己实现(留到后面会手写)
- 方式3：利用原有的JS机制，实现深拷贝(有部分缺点)

![image-20230128022759043](.\JavaScript高级_image\image-20230128022759043.png)

```javascript
//利用JS机制间接实现实现
const info3 = JSON.parse(JSON.stringify(obj))//这样先将obj对象转化为字符串，又转化回了对象。但这个转回的对象已经是一个全新的对象了，跟原来的obj已经没有关系了，尽管内容一样
```

```javascript
const obj = {
  name:"小余",
  age:20
}

const info3 = JSON.parse(JSON.stringify(obj))
info3.name = "黑丝小满"
//info3确实改变成了黑丝小满，但是obj还是小余，证明了实现了深拷贝，对info3的操作没有影响到obj
console.log(`info.name：${info3.name},obj.name：${obj.name}`);
```



> 深拷贝和浅拷贝的区别？
>
> 浅拷贝和深拷贝都是创建一份数据的拷贝。
>
> JS 分为原始类型和引用类型，对于原始类型的拷贝，并没有深浅拷贝的区别，我们讨论的深浅拷贝都只针对引用类型。
>
> - 浅拷贝和深拷贝都复制了值和地址，都是为了解决引用类型赋值后互相影响的问题。
> - 但是浅拷贝**只进行一层复制**，深层次的引用类型还是共享内存地址，原对象和拷贝对象还是会互相影响。
> - 深拷贝就是**无限层级拷贝**，深拷贝后的原对象不会和拷贝对象互相影响。

### (了解)数字表示-其他进制和长数字表示方法

#### 数值的表示

- 在ES6中规范了二进制和八进制的写法：

```javascript
const num1 = 100
//b -> binary(二进制)
const num2 = 0b100
//octonary(八进制)
const num3 = 0o100
//hexadecimal(十六进制)
const num4 = 0x100
console.log(num1,num2,num3,num4);//100 4 64 256
```

- 另外在ES2021新增特性：数字过长时，可以使用_作为连接符

```javascript
const num = 100_000_000//真的友好
console.log(num);
```

### (掌握)Symbol-对象属性的痛点和Symbol的作用

#### Symbol的基本使用

-  Symbol是什么呢？Symbol是ES6中新增的一个基本数据类型，翻译为符号。
- **那么为什么需要Symbol呢？**
  - 在ES6之前，对象的属性名都是字符串形式，那么很容易造成**属性名的冲突**；
  - 如原来有一个对象，我们希望在其中**添加一个新的属性和值**，但是我们在不确定它原来内部有什么内容的情况下，**很容易造成冲突，从而覆盖掉它内部的某个属性**
  - 比如我们前面在讲apply、call、bind实现时，我们有给其中**添加一个fn属性**，那么如果它内部原来已经有了fn属性了呢？
  -  比如开发中我们使用混入，那么混入中出现了同名的属性，必然有一个会被覆盖掉
- Symbol就是为了解决上面的问题，用来**生成一个独一无二的值**
  - Symbol值是通过**Symbol函数**来生成的，生成后可以**作为属性名**
  - 也就是在ES6中，对象的属性名可以使用**字符串**，也可以使用**Symbol值**
- **Symbol即使多次创建值，它们也是不同的**：Symbol函数执行后每次创建出来的值都是独一无二的
- **我们也可以在创建Symbol值的时候传入一个描述description**：这个是ES2019（ES10）新增的特性；

```javascript
const info = {name:"小余"}
const obj = {
  [info]: "老铁666"
}
console.log(obj);//将对象作为key值的话，对象是会先被转化为字符串的.所以最好不这么用
//因为在ES6之后，只有两种形式能作为key值，一个是字符串，一个是Symbol。不符合这两点的在使用过程中会先被转化为字符串
```

![image-20230128025003589](.\JavaScript高级_image\image-20230128025003589.png)

```javascript
const s1 = Symbol()
const obj = {
  [s1]: "老铁666"
}
console.log(obj);

//且Symbol之间是永远不会相等的，生成的永远都是独一无二的
const s2 = Symbol()
console.log(s1 === s2);//false
console.log(s1 == s2);//false
```

![image-20230128025032726](.\JavaScript高级_image\image-20230128025032726.png)

### (了解)Symbol-Symbol额外知识内容的补充

#### Symbol作为属性名

- 我们通常会使用Symbol在对象中表示唯一的属性名：

```javascript
const s1 = Symbol()
const s2 = Symbol()

const obj = {
  name:"xiaoyu",
  age:20,
  [s1]:"aaa",
  [s2]:"bbb"
}

// const obj1 = {}
// obj1[s1] = "aaa"
// obj1[s2] = "bbb"

// const obj2 = {}
// Object.defineProperty(obj,s1,{
//   value:"aaa"
// })


//加入到对象中
//写法1：属性名赋值

//写法2：Object

//写法3：定义字面量的时候使用

//获取Symbol对应的key
console.log(Object.keys(obj));//获取不到['name', 'age']
console.log(Object.getOwnPropertySymbols(obj));//[Symbol(), Symbol()]

const symbolkey = Object.getOwnPropertySymbols(obj)

//获取symbol对应的值
for(const key of symbolkey){
  console.log(obj[key]);///aaa bbb，通过打印出来obj的symbol中的值，在根据这个symbol值for遍历出对象的内容
}
```

#### 相同值的Symbol

-  **前面我们讲Symbol的目的是为了创建一个独一无二的值，那么如果我们现在就是想创建相同的Symbol应该怎么来做呢？**
  -  我们可以使用Symbol.for方法来做到这一点
  - 并且我们可以通过Symbol.keyFor方法来获取对应的key
  - `Symbol`对象有一个可选的字符串参数，称为`description`。它是用于说明`Symbol`的目的，不影响其唯一性。

```javascript
//3.description
const s3 = Symbol("xiaoyu")
console.log(s3.description)
```

> 如果我们想要让两个symbol的值能够一样的话?
>
> `const s3 = Symbol("xiaoyu")`中的`xiaoyu`这个部分我们通常称为description
>
> `s3.description`则称为key

```javascript
//获取Symbol的key
const s3 = Symbol("xiaoyu")
console.log(s3.description)//xiaoyu

//Symbol函数直接生成的值都是独一无二的
const s4 = Symbol(s3.description)
console.log(s3 === s4);//false，就算这样，他们的值也不会一样的，区别在于s3是xiaoyu，s4是Symbol(xiaoyu)

//就算通过symbol.for来生成也是独一无二的，因为我们都是在symbol函数的基础上进行的
const s5 = Symbol.for(s3.description)
console.log(s3 === s5);//false

//但是存在卡bug的方式
const s6 = Symbol.for(s3.description)//两次通过Symbol.for二次生成的值就一样了
console.log(s5 === s6);//true,这个就相等了
```

#### 获取传入的key值

```javascript
//获取Symbol的key
const s3 = Symbol.for("xiaoyu")
console.log(Symbol.keyFor(s3));//xiaoyu，获取symbol的key值，是key值，不是description
```

### (理解)Set-Map数据结构的基本介绍

#### Set的基本使用

- **在ES6之前，我们存储数据的结构主要有两种：数组、对象。**
  -  在ES6中新增了另外两种数据结构：Set、Map，以及它们的另外形式WeakSet、WeakMap

- **Set是一个新增的数据结构，可以用来保存数据，类似于数组，但是和数组的区别是元素不能重复**
  - 创建Set我们需要通过**Set构造函数**（暂时没有字面量创建的方式）
- 我们可以发现Set中存放的元素是**不会重复**的，那么Set有一个非常常用的功能就是**给数组去重**

### (掌握)Set-Map-Set的基本使用和应用场景

```javascript
//创建set
const set = new Set()
console.log(set);
//往set中添加内容
set.add(10)
set.add(20)
set.add(30)
set.add(30)//会自动去重，30只会保留一个
const info = {name:"小余"}
const obj = {name:"小满"}
set.add(info)//可以添加多个对象，但是不能是同一个对象
set.add(obj)
set.add(obj)//添加obj对象只会生效一次
//
```

```javascript
//应用场景：数组的去重
const names = ["小余","小满","小余","Heart","小满"]
//以前的写法
const newNames = []
for(const item of names){
  if (!newNames.includes(item)) {
    newNames.push(item)
  }
}
console.log(newNames);//['小余', '小满', 'Heart']
//现在的写法
//这样去重得到的是Set类型的结果，需要进行转换
const set = new Set(names)//Set(3) { '小余', '小满', 'Heart' }
console.log(set);
//将Set转化为数组类型
const newNames1 = Array.from(set)
console.log(newNames1);//['小余', '小满', 'Heart']
```

#### Set的常见方法

- **Set常见的属性**
  -  size：返回Set中元素的个数
- **Set常用的方法：**
  - **add(value)**：添加某个元素，返回Set对象本身
  - **delete(value)**：从set中删除和这个值相等的元素，返回boolean类型
  -  **has(value)**：判断set中是否存在某个元素，返回boolean类型
  - **clear()**：清空set中所有的元素，没有返回值
  - **forEach(callback, [, thisArg])**：通过forEach遍历set
- **另外Set是支持for of的遍历的**

```javascript
//其他方法

//常见属性：获取数组大小(个数)
console.log(set.size);
//常见方法：增、删、判断存在、清空、遍历这5种常见功能
//增
set.add("喜多川")
console.log(set);//Set(4) {'小余', '小满', 'Heart', '喜多川'}
//删
set.delete("Heart")
console.log(set);//Set(3) {'小余', '小满', '喜多川'}
//判断存在
console.log(set.has("小余"));//true
//forEach遍历
set.forEach(item => console.log(item))//小余 小满 喜多川
//清空
set.clear()
console.log(set);//Set(0) {size: 0}
```

### (理解)Set-Map-WeakSet的使用和应用

#### WeakSet使用

> Weak Reference：弱引用
>
> ![image-20230131032617604](.\JavaScript高级_image\image-20230131032617604.png)
>
> Strong Reference：强引用(就正常的引用就是强引用)

- **和Set类似的另外一个数据结构称之为WeakSet，也是内部元素不能重复的数据结构**
- **那么和Set有什么区别呢？**
  -  区别一：WeakSet中**只能存放对象类型，不能存放基本数据类型**
  - 区别二：WeakSet**对元素的引用是弱引用**，如果没有其他引用对某个对象进行引用，那么GC可以对该对象进行回收

```javascript
//WeakSet的用法
//和set的区别1：只能存放对象类型
const weakSet = new WeakSet()
// weakSet.add(123)//无效会报错，
const obj = {
  name:"小余"
}
weakSet.add(obj)

//和set的区别2：对对象的引用都是弱引用，GC要进行回收的时候，就会当弱引用的指向不存在
```

#### WeakSet常见的方法(增、删、判断)

- add(value)：添加某个元素，返回WeakSet对象本身
- delete(value)：从WeakSet中删除和这个值相等的元素，返回boolean类型
- has(value)：判断WeakSet中是否存在某个元素，返回boolean类型

#### WeakSet的应用

- 注意：WeakSet不能遍历
  - 因为**WeakSet只是对对象的弱引用**，如果我们遍历获取到其中的元素，那么有可能造成对象不能正常的销毁。
  - 所以**存储到WeakSet中的对象是没办法获取**的
- **那么这个东西有什么用呢？**
  -  事实上这个问题并不好回答，我们来使用一个Stack Overflow上的答案

```javascript
class Person{
  running(){
    console.log("要开学了呜呜呜")
  }
}
//第一种传统调用方法
const p1 = new Person()
p1.running()//要开学了呜呜呜

//第二种调用方式，其实就只是将调用的过程变成了赋值，调用。本质没有任何区别
const runFn = p1.running
runFn()//要开学了呜呜呜

//在第二种的基础上继续的骚操作玩法
const obj = {run:runFn}
obj.run()//要开学了呜呜呜

//总结：但是我们通过类来写的话，我们更希望是使用对象来进行调用，也就是第一种传统的方式，而不是二三种的方式，那如何提示用户不要使用第二三种方式呢，这就用到我们所学的这些内容知识了
```

```javascript
//改变添加的部分
const pWeakSet = new WeakSet()//使用WeakSet

class Person{
  constructor(){
    pWeakSet.add(this)//将调用者添加到WeakSet中
  }
  running(){
    if(!pWeakSet.has(this)){//进行判断WeakSet中是否有我们调用的这个结构
      console.log("Type error:你的类型错误");//抛出异常也行
      return
    }
    console.log("要开学了呜呜呜")
  }
}
```

```javascript
//完整的效果展示
const pWeakSet = new WeakSet()

class Person{
  constructor(){
    pWeakSet.add(this)
  }
  running(){
    if(!pWeakSet.has(this)){
      console.log("Type error:你的类型错误");
      return
    }
    console.log("要开学了呜呜呜")
  }
}
//第一种传统调用方法
const p1 = new Person()
p1.running()//要开学了呜呜呜

//第二种调用方式，其实就只是将调用的过程变成了赋值，调用。本质没有任何区别
const runFn = p1.running
runFn()//Type error:你的类型错误

//在第二种的基础上继续的骚操作玩法
const obj = {run:runFn}
obj.run()//Type error:你的类型错误

//我们成功了，这样就可以将使用者不正规的写法进行提示跟引导
```

### (掌握)Set-Map-Map的基本使用过程

- **另外一个新增的数据结构是Map，用于存储映射关系**
- **但是我们可能会想，在之前我们可以使用对象来存储映射关系，他们有什么区别呢？**
  - 事实上我们对象存储映射关系只能用**字符串（ES6新增了Symbol）作为属性名（key）**
  -  某些情况下我们可能希望通过**其他类型作为key，比如对象**，这个时候**会自动将对象转成字符串来作为key**
- **那么我们就可以使用Map：**

```javascript
//不会使用对象来作为key，因为后面一个会将前面的覆盖掉
const info = {name:"小余"}
const info2 = {address:"福建"}

//对象类型的局限性：不可以使用复杂类型作为key。对象最终还是会被转化成字符串的
const obj = {
  [info] : "小余",
  [info2]: "福建"
}
console.log(obj);//{[object Object]: '福建'}，前面的小余内容被覆盖掉了
```

```javascript
//不会使用对象来作为key，因为后面一个会将前面的覆盖掉
const info = {name:"小余"}
const info2 = {address:"福建"}

//Map映射类型
const map = new Map()
map.set(info,"废柴余")
map.set(info2,"牛马大专")
console.log(map);
```

![image-20230131052850259](.\JavaScript高级_image\image-20230131052850259.png)

#### Map的常用方法

- **Map常见的属性：**
  - **size**：返回Map中元素的个数；
- **Map常见的方法：**
  - **set(key, value)**：在Map中添加key、value，并且返回整个Map对象；
  - **get(key)**：根据key获取Map中的value；
  - **has(key)**：判断是否包括某一个key，返回Boolean类型；
  - **delete(key)**：根据key删除一个键值对，返回Boolean类型
  - **clear()**：清空所有的元素
  - **forEach(callback, [, thisArg])**：通过forEach遍历Map
- Map也可以通过**for of**进行遍历

```javascript
const info = {name:"小余"}
const info2 = {address:"福建"}

//Map映射类型
const map = new Map()
////set方法，设置内容
map.set(info,"废柴余")
map.set(info2,"牛马大专")
//Map的常见属性
console.log(map.size);//2
//get方法，获取刚刚设置的内容
console.log(map.get(info));//废柴余
//forEach方法，遍历map中设置的内容
map.forEach(item => {
    console.log(item,"forEach水印");//废柴余 forEach水印  牛马大专 forEach水印
});
//for of遍历迭代，使用这种方式可以拿到里面的具体内容，更加细化
for(item of map){
  console.log(item,"for of水印");
  [key,value] = item
  console.log(key,value);
}
//delete方法，删除内容
map.delete(info2)
console.log(map);//Map(1) {{…} => '废柴余'}，info2内容删除成功
//has判断
console.log(map.has(info2));//false，刚刚删除掉了，所以是false
//clear清空内容
map.clear()
console.log(map);//Map(0) {size: 0}
```

![image-20230131055135487](.\JavaScript高级_image\image-20230131055135487.png)

![image-20230131055609625](.\JavaScript高级_image\image-20230131055609625.png)

### (理解)Set-Map-WeakMap的基本使用过程

#### WeakMap的使用

- **和Map类型的另外一个数据结构称之为WeakMap，也是以键值对的形式存在的。**
- **那么和Map有什么区别呢？**
  - 区别一：**WeakMap的key只能使用对象，不接受其他的类型作为key**；
  -  区别二：WeakMap的**key对对象的引用是弱引用**，如果没有其他引用引用这个对象，那么GC可以回收该对象；

```javascript
const weakMap = new WeakMap()
//无效值用作弱映射键:Invalid value used as weak map key
weakMap.set(1,"abc")
weakMap.set("aaa","cba")
```

- **WeakMap常见的方法有四个：**
  -  set(key, value)：在Map中添加key、value，并且返回整个Map对象；
  - get(key)：根据key获取Map中的value；
  - has(key)：判断是否包括某一个key，返回Boolean类型
  - delete(key)：根据key删除一个键值对，返回Boolean类型

#### WeakMap的应用

- **注意：WeakMap也是不能遍历的**
  - 没有forEach方法，也不支持通过for of的方式进行遍历；
-  那么我们的WeakMap有什么作用呢？（后续专门讲解,Vue3的响应式原理部分）

![image-20230131061550597](.\JavaScript高级_image\image-20230131061550597.png)

### (了解)ES6小总结和ES7-ES13介绍

#### ES6其他知识点说明

- **事实上ES6（ES2015）是一次非常大的版本更新，所以里面重要的特性非常多：**
  - 除了前面讲到的特性外还有很多其他特性
- **Proxy、Reflect，我们会在后续专门进行学习**
  - 并且会利用Proxy、Reflect来讲解Vue3的响应式原理
- **Promise，用于处理异步的解决方案**
  - 后续会详细学习；
  - 并且会学习如何手写Promise；
- **ES Module模块化开发：**
  - 从ES6开发，JavaScript可以进行原生的模块化开发；
  - 这部分内容会在工程化部分学习
  - 包括其他模块化方案：CommonJS、AMD、CMD等方案

## ES7~ES13新特性 ( 三 )

### (掌握)ES8-padStart和ES7 - Array Includes（已学）padEnd和ES9

#### ES7 - Array Includes（已学）

-  在ES7之前，如果我们想判断一个数组中是否包含某个元素，需要通过 indexOf 获取结果，并且判断是否为 -1
- 在ES7中，我们可以通过includes来判断一个数组中是否包含一个指定的元素，根据情况，如果包含则返回 true，否则返回false

```javascript
const names = ["小余","小满","汤姆","草莓"]
//names里面没有麒麟哥这个元素
if(!names.includes("麒麟哥")){
  console.log("麒麟哥不在，群里无内鬼");
}
//names里面有小满这个元素
if (names.includes("小满")) {
  console.log("小满在，被你抓到了");
}
```

#### ES7 –指数exponentiation运算符（已学）

- 在ES7之前，计算数字的乘方需要通过 Math.pow 方法来完成。
- 在ES7中，增加了 ** 运算符，可以对数字来计算乘方

```javascript
//两个都是3的3次方
const result1 = Math.pow(3,3)
const result2 = 3 ** 3
console.log(result1,result2);//27 27
```

#### ES8 Object values

- 之前我们可以通过 **Object.keys** 获取一个对象所有的key
- **在ES8中提供了 Object.values 来获取所有的value值：**

```javascript
const obj = {
  name:"小余",
  age:20
}
//获取对象所有key
console.log(Object.keys(obj));//[ 'name', 'age' ]
//获取对象所有值
console.log(Object.values(obj));//[ '小余', 20 ]

//也可以传入字符串，会进行拆分
console.log(Object.values("你是我的小呀小苹果"));
//['你', '是', '我', '的', '小', '呀', '小', '苹', '果']
```

#### ES8 Object entries

- **通过 Object.entries 可以获取到一个数组，数组中会存放可枚举属性的键值对数组。**
  - 可以针对**对象、数组、字符串**进行操作

```javascript
const obj = {
  name:"小余",
  age:20
}

console.log(Object.entries(obj));//[ [ 'name', '小余' ], [ 'age', 20 ] ]
//应用对象中的entries
for (const emtry of Object.entries(obj)){
  const [key,value] = emtry
  console.log(key,value);//name 小余  age 20
}

//如果是数组
console.log(Object.entries(["a","b","c","d"]));

//如果是一个字符串
console.log(Object.entries("abc"));
```

![image-20230131082637877](.\JavaScript高级_image\image-20230131082637877.png)

#### ES8 - String Padding(前后填充)

- 某些字符串我们需要对其进行前后的填充，来实现某种格式化效果，ES8中增加了 **padStart** 和 **padEnd** 方法，分别是对字符串的首尾进行填充的

```javascript
//padStart:位数不够往前填充，第一个值填所需位数，第二个值填当位数不够时的填充内容
const minute = "2".padStart(2,"0")
//padEnd:位数不够往后填充
const second = "1".padEnd(2,"0")
console.log(`${minute}:${second}`);//02:10
```

- 我们简单具一个应用场景：比如需要对身份证、银行卡的前面位数进行隐藏：

```javascript
const cardNumber = "110102YYYYMMDD888X"//身份证号码
const lastFourNumber = cardNumber.slice(-4)//获取身份证后四位
const finalCardNumber = lastFourNumber.padStart(cardNumber.length,"*")//要求达到身份证位要求的位数，不满足时缺少几位就往开头填入几位*
console.log(finalCardNumber);
```

#### ES8 - Trailing Commas(不推荐)

> Trailing：尾部
>
> Trailing Commas：尾部逗号

- 在ES8中，我们允许在函数定义和调用时多加一个逗号：

```javascript
function foo(a,b,){//函数定义多加逗号
  console.log(a+b);
}
foo(1,5);//6
foo(1,5,)//6	函数调用多加逗号
```

#### ES8 - Object Descriptors

- Object.getOwnPropertyDescriptors ：已经写过了，这里不再重复(获取一个对象其对应的描述符)
- Async Function：async、await：放在后续Promise后进行讲解

#### ES9新增知识点

- **Async iterators：后续迭代器讲解**(异步迭代器)
- **Object spread operators：前面讲过了**
  1. 允许在对象中复制或合并对象属性。它使用三个点 (...) 来表示对象展开，可以把一个对象的所有属性复制到另一个对象中
  2. 如果两个对象有相同的属性名称，则后面的对象中的值会覆盖前面的对象中的值。
- **Promise finally：后续讲Promise讲解**

### (了解)ES10-flat和flatMap的使用

#### ES10 - flat flatMap

- **flat() 方法会按照一个可指定的深度递归遍历数组，并将所有元素与遍历到的子数组中的元素合并为一个新数组返回**

```javascript
//flat的使用：
//将一个数组。按照制定的深度遍历，将遍历到的元素和子数组中的元素组成一个新的数组，进行返回
const nums = [10,20,[30,40,50],[60,[70],[80,[90,100]]]]//嵌套了多层的数组

//flat里面填数字几就平坦化几层
const newNums1 = nums.flat(1)
console.log(newNums1);//(8) [10, 20, 30, 40, 50, 60, Array(1), Array(2)]
const newNums2 = nums.flat(2)
console.log(newNums2);//(9) [10, 20, 30, 40, 50, 60, 70, 80, Array(2)]
const newNums3 = nums.flat(3)
console.log(newNums3);//(10) [10, 20, 30, 40, 50, 60, 70, 80, 90, 100]
```

- **flatMap() 方法首先使用映射函数映射每个元素，然后将结果压缩成一个新数组。**
  - 注意一：flatMap是先进行map(映射)操作，再做flat的操作
  - 注意二：flatMap中的flat相当于深度为1；

```javascript
//flotMap的使用：
//1.对数组中每一个元素应用一次传入的map对应的函数
//实现将"Hello World"拆分成"Hello"跟"World"的效果
const message = [
  "Hello World",
  "Hello 小余",
  "出来了，一只coderwhy"
]


//自己的方式完成需求：for循环完成
const newInfos = []
for(const item of message){
  const infos = item.split(" ")//从空格处做出切割
  for(const item1 of infos){
    newInfos.push(item1)
  }
}
console.log(newInfos);//['Hello', 'World', 'Hello', '小余', '出来了，一只coderwhy']

//方法2：先map后flat操作
const newMessage = message.map(item => item.split(" "))
console.log(newMessage);//会形成二维数组，数组里面嵌套一层
const finalMessage = newMessage.flat(1)//通过flat进行平坦化
console.log(finalMessage);//['Hello', 'World', 'Hello', '小余', '出来了，一只coderwhy']

//方法3：flatMap，一步到位
const newMessage1 = message.flatMap(item => item.split(" "))
console.log(newMessage1);//['Hello', 'World', 'Hello', '小余', '出来了，一只coderwhy']
```

### (了解)ES10-fromEntries方法和trim

#### ES10 - Object fromEntries

- **在前面，我们可以通过 Object.entries 将一个对象转换成 entries**
-  **那么如果我们有一个entries了，如何将其转换成对象呢？**
  -  ES10提供了 Object.formEntries来完成转换：

```javascript
const obj = {
  name:"小余",
  age:20,
  height:1.88
}
//obj对象转化成entries
const entries = Object.entries(obj)
console.log(entries);
//entries转回对象
const info = Object.fromEntries(entries)
console.log(info);
```

- 那么这个方法有什么应用场景呢？

```javascript
const searchSting = "?name=小余&age=20&height=1.75"
const params = new URLSearchParams(searchSting)
console.log(params);//打印不出来内容，但是可以通过get和set获取
console.log(params.get("name"));//小余
console.log(params.get("age"));//20

//上面的操作等价于
for(const item of params.entries()){
  console.log(item);
}
const paramObj = Object.fromEntries(params.entries())
console.log(paramObj);//{name: '小余', age: '20', height: '1.75'}
```

#### ES10 - trimStart trimEnd

- **去除一个字符串首尾的空格，我们可以通过trim方法，如果单独去除前面或者后面呢？**
  - ES10中给我们提供了**trimStart和trimEnd；**

```javascript
const name = "  xiao yu  "
//去除首位空格
console.log(name.trim());//xiao yu
//去除前面空格
console.log(name.trimStart());//xiao yu  
//去除后面空格
console.log(name.trimEnd());//  xiao yu
```

#### ES10 其他知识点

- Symbol description：已经写过了
- Optional catch binding：后面讲解try cach(捕获异常)讲解

### (掌握)ES11-BigInt和空值合并运算符使用

#### ES11 - BigInt

- **在早期的JavaScript中，我们不能正确的表示过大的数字：**
  - 大于**MAX_SAFE_INTEGER**的数值，表示的可能是不正确的

```javascript
console.log(Number.MAX_SAFE_INTEGER)//9007199254740991
const num1 = 9007199254740991 + 1
const num2 = 9007199254740991 + 2
console.log(num1,num2);//9007199254740992 9007199254740992
//可以看到num1跟num2的值是一样的，证明了超过了这个限定最大数值就会发生不稳定错误
```

- **那么ES11中，引入了新的数据类型BigInt，用于表示大的整数**
  - BigInt的表示方法是在数值的后面加上n

```javascript
const bigInt = 9007199254740991n
const num1 = bigInt + 1n
const num2 = bigInt + 2n
//正确显示
console.log(num1,num2);//9007199254740992n 9007199254740993n
```

#### ES11 - Nullish Coalescing Operator

- **ES11，Nullish Coalescing Operator增加了空值合并操作符：**`??`

```javascript
let info = false
info = info || "默认值"
//我们以前判断info内有没有内容通常这样判断，但是这样空字符串，0，false这些无能为力
console.log(info);//默认值

//??空值合并运算符，更加的严谨
let info1 = false
info = info ?? "默认值"
console.log(info1);//false
```

### (理解)ES11-可选链和其他知识补充

#### ES11 - Optional Chaining

- **可选链也是ES11中新增一个特性，主要作用是让我们的代码在进行null和undefined判断时更加清晰和简洁：**

```javascript
const obj = {
  name:"小余",
  friend:{
    name:"小满",
    // running:function(){
    //   console.log("running");
    // }
  }
}
//你在使用的时候，并不知道obj里面有没有friend这个，如果没有，obj.friend会返回undefined，在undefined上调用running就会报错
obj.friend.running();

//更严谨的做法，加入判断
if(obj.running || obj.friend.running){
  obj.friend.running()//判断后再决定调不调用，因为报错如果不try catch会导致后续代码无法执行
}

//可选链的用法(?.)，注意调用下面这个的时候要把前面的两种写法注释掉
obj?.friend?.running?.()//有就执行，没有就不执行了
```

#### ES11 - Global This（已学）

- **在之前我们希望获取JavaScript环境的全局对象，不同的环境获取的方式是不一样的**
  - 比如在浏览器中可以通过this、window来获取
  -  比如在Node中我们需要通过global来获取
- **在ES11中对获取全局对象进行了统一的规范：globalThis**

```javascript
console.log(globalThis)//统一规范
console.log(this)//浏览器
console.log(global)//Node环境
```

#### ES11 - for..in标准化

- **在ES11之前，虽然很多浏览器支持for...in来遍历对象类型，但是并没有被ECMA标准化。**

- 在ES11中，对其进行了标准化，for...in是用于**遍历对象**的key的：

  > `for in` 循环用于枚举对象的属性，`for of` 循环用于迭代可迭代对象（如数组）的元素。
  >
  > 区别：
  >
  > 1. `for in` 返回的是键名，而 `for of` 返回的是键值。
  > 2. `for in` 遍历顺序是不确定的，而 `for of` 遍历顺序是确定的。
  > 3. `for in` 可以迭代所有可枚举属性（包括继承的），而 `for of` 只能迭代定义在对象本身的属性。

```javascript
const obj = {
  name:"小余",
  age:20
}

for(item in obj){//遍历
  console.log(item);
}
```

#### ES11 其他知识点

- Dynamic Import：后续ES Module模块化中详细写
- Promise.allSettled：后续讲Promise的时候详细写
-  import meta：后续ES Module模块化中详细写

### (了解)ES12-FinalizationRegist

- **FinalizationRegistry** 对象可以让你在对象被垃圾回收时请求一个回调
  - FinalizationRegistry 提供了这样的一种方法：当一个**在注册表中注册的对象被回收**时，**请求在某个时间点上调用一个清理回调**。（清理回调有时被称为 finalizer ）
  - 你可以通过**调用register方法，注册任何你想要清理回调的对象，传入该对象和所含的值**

```javascript
let obj = {name:"小余",age:20}

//当被GC(垃圾回收)的时候就会调用这里面的回调函数
const finalRegistry = new FinalizationRegistry(()=>{
  console.log("某一个对象被回收了 ");
})

//调用register(寄存器)进行注册任何我们想要清理回调的对象
finalRegistry.register(obj)//此时不会生效
//置为空，让obj在下轮中被垃圾回收掉
obj = null
//因为DC不是马上就回收的，它会在GPU空闲的时候进行回收，所以在一定间隔之后就会进行回收处理，然后触发我们的回调
```

```javascript
//也可以传入多个的参数来用来当register注册多了之后如何区分
let obj = {name:"小余",age:20}
let info = {name:"coderwhy",age:18}
const finalRegistry = new FinalizationRegistry((value)=>{
  console.log("某一个对象被回收了 ：",value);
})
//第二个参数当在触发时会被加入到finalRegistry的回调函数里面
finalRegistry.register(obj,"小余")//某一个对象被回收了 ： 小余
finalRegistry.register(info,"coderwhy")//某一个对象被回收了 ： coderwhy
obj = null//将引用关联去掉，让obj下一轮被垃圾回收掉
info = null
```

### (了解)ES12-WeakRefs对对象的弱引用

#### ES12 - WeakRefs

- 如果我们默认将一个对象赋值给另外一个引用，那么这个引用是一个强引用：

  - 如果我们希望是一个弱引用的话，可以使用WeakRef；

  > 如果多个`WeakRef`s 有相同的目标，那么他们的 target 对象是一样的。对其中一个调用 deref 的结果将与对另一个调用 deref 的结果匹配（在同一个作业中），您不会从其中一个获取目标对象，而是从另一个获取未定义的对象
  >
  > - `deref`方法返回[`WeakRef`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WeakRef) 实例的目标对象，如果目标对象已被垃圾收集，则返回`undefined` 。
  > - 语法：obj = ref.deref();

```javascript
let info = {name:"xiaoyu",age:20}
let obj1 = new WeakRef(info)
let obj2 = new WeakRef(info)

const finalRegistry = new FinalizationRegistry((value)=>{
  console.log("回收掉咯",value);
})

finalRegistry.register(info,"info")

setTimeout(()=>{
  info = null
},2000)

// setTimeout(()=>{
//   const infoRef = obj1.deref()
//   console.log(infoRef.name,infoRef.age);
// },7000)

console.log(obj1.name);//undefined
console.log(obj1.deref().name);//xiaoyu
```

> 使用命令进行测试：const myref = obj1.deref()

- 回收掉后进行测试，显示undefined

![image-20230201214951751](.\JavaScript高级_image\image-20230201214951751.png)

- 未回收前进行测试，显示对象里的内容。证明了`deref`的作用

![image-20230201215043501](.\JavaScript高级_image\image-20230201215043501.png)

> 如果你想为什么我不直接使用：console**.**log(obj1**.**name);这样来输出呢？
>
> 因为输出不了真正的内容，只会返回undefined，因为弱引用内容不可达，我们接收的obj1里的内容是WeakRef变成的弱引用对象，必须要使用deref来辅助输出
>
> 你最多说console**.**log(obj1**.**deref()**.**name);这样输出

### (了解)ES12-逻辑赋值运算符-replaceAll

#### ES12 - logical assignment operators

- 逻辑赋值运算符的简化
  - 例如`||逻辑赋值运算符`、`??逻辑赋值运算符`、`&&逻辑赋值运算符`

```javascript
//以前学过的赋值运算符,类似下面这种
const foo = "小余" 
// const counter = 100
// counter += 50

//逻辑赋值运算符(ES12新增的)
function bar(message){
  //以前判断传入有没有值，通常这么做
  message = message || "默认值"
  //上面这种方式已经简化了
  message ||= "默认值"
  //这个例子有更好的方式，那就是前面用过的案例
  message = message ?? "默认值"//前面的方式对空字符串，0，false，之类的无能为力。这种方式则可以解决
  //那这种方式也能够简化的啦
  message ??= "默认值" 
  console.log(message);
}

//多种方式自行测试
bar("")
bar(false)
bar(0)
bar("小余666")
```

#### ES12其他知识点

-  **Numeric Separator：写过了；**(数字分割符号，就那个数字太大用_分开)
- **String.replaceAll：字符串替换；**

```javascript
const name = "小满今天穿黑丝,小满对此很满意"
const newName = name.replace("小满","洛洛")//只会修改第一个遇到的
const newName1 = name.replaceAll("小满","康老师")//会将全部都进行修改
console.log(newName);
console.log(newName1);
```

### (理解)ES13-对象属性判断hasOwn方法

#### ES13 - method .at()

- **前面我们有学过字符串、数组的at方法，它们是作为ES13中的新特性加入的：**
- 太新了不建议使用，旧的浏览器可能就不支持，除非进行babel转化

```javascript
//开头下标从0开始，结尾从-1开始往前
//数组
var names = ["abc","cba","nba"]
console.log(names.at(1))//cba
console.log(names.at(-1))//nba

//字符串
var str = "Hello XiaoYu"
console.log(str.at(1))//e
console.log(str.at(-1))//u
```

#### ES13 - Object.hasOwn(obj, propKey)

- Object中新增了一个静态方法（类方法）： hasOwn(obj, propKey)

  - 该方法用于判断一个对象中是否有某个自己的属性；

- 那么和之前学习的Object.prototype.hasOwnProperty有什么区别呢？

  ![image-20230201234740761](.\JavaScript高级_image\image-20230201234740761.png)

  - 区别一：防止对象内部有重写hasOwnProperty
  - 区别二：对于隐式原型指向null的对象， hasOwnProperty无法进行判断

- 也就是说这个特性就是用来替代掉hasOwnProperty方法的

```javascript
const obj = {
  name:"小余",
  age:20,
  //和hasOwnProperty的区别1:防止对象中也有一个自己的hasOwnProperty方法,产生重写
  hasOwnProperty:function(){
    return "挖掘机"
  },
  //这里的隐式原型是测试用的,日常不推荐这样写
  __proto__:{
    address:"福建"
  }
}

console.log(obj.name,obj.age);//小余 20
console.log(obj.address);//福建

//之前的用法
console.log(obj.hasOwnProperty("name"));//true
console.log(obj.hasOwnProperty("address"));//false

//新的用法
console.log(Object.hasOwn(obj,"name"));//true
console.log(Object.hasOwn(obj,"address"));//false

//和hasOwnProperty的区别2:
const info = Object.create(null)//info的原型指向null
info.name = "小余"
////Uncaught TypeError: info.hasOwnProperty is not a function
console.log(info.hasOwnProperty("name"));//因为这个info身上没有hasOwnProperty方法,会去原型上面查找,原型是null肯定就会报错.所以不够严谨
console.log(Object.hasOwn(info,"name"));//true
```

### (理解)ES13-类中增加的新成员和私有属性

#### ES13 - New members of classes

- 在ES13中，新增了定义class类中成员字段（field）的其他方式：

  - Instance public fields
  - Static public fields
  - Instance private fields
  - static private fields
  - static block

- > 函数式编程慢慢又流行起来了，在Vue或者说React中逐渐的替代掉了面向对象的抽象方式，不过也确实，函数式编程会更加的灵活，面向对象会更加笨重一些。写很多东西的话，面向对象就会变得不够方便了
  >
  > 函数作为JS的一等公民，可以传来传去，回调起来也很方便。而且前端主要还是要处理数据，而不是业务的抽象，所有大多数是不需要用到复杂的类的。

```javascript
class Person{
  //1.实例属性
  //height这样写在外面的属性,是当你通过new创建的对象里,一定会被加进去这个的属性
  //这样的属性叫做:对象属性(public):公共 => 因为在所有的地方都可以访问到他.也可以称为Instance public fields(实例公共字段)
  height = 1.75

  //对象属性(private):私有 => 程序员之间的约定
  _intro = "my name is XiaoYu"//能访问到,但最好不要访问.这是以前的私有方式,但是在ES13中得到了强化

  //ES13强化版本的对象属性(private):私有 => 程序员之间的约定
  #intro = "my name is XiaoYu"

  //2.类属性(static)
  //类属性:public,能够直接访问,而不至于通过原型的方式去访问.之前有使用过
  static GithubUser = "2002XiaoYu"
  //类属性:private,私有的
  static #name = "xiaoyu"

  //3.静态代码块:只会在加载类的时候执行一次,后续不执行(前端用的少,后端用的多)
  static {
    console.log("你好,我是小余");
    console.log("这句话只会出现一次");
  }

  constructor(name,age){
    //对象中的属性:在constructor通过this设置
    this.name = name
    this.age = age
    this.address = "福建省"
  }
}

const p = new Person("小余",20)
console.log(p);//Person {height: 1.75, name: '小余', age: 20, address: '福建省'}
console.log(p.name,p.age,p.address,p.height);//小余 20 福建省 1.75

console.log(p._intro);//以前的话,私有属性还是能够取到的:my name is XiaoYu
// console.log(p.#intro);//现在ES13后的私有属性是取不了的,还会报警告:属性 "#intro" 在类 "Person" 外部不可访问，因为它具有专用标识符

console.log(Person.GithubUser)//2002XiaoYu
// console.log(Person.#name);//报错

//提示:打印其他内容的时候记得先把打印#私有属性的给注释掉,不然会一起报错
```

