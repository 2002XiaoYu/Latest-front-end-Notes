# 理解CSS

- **为什么学习CSS**
  1. 网页样式和布局：CSS（**层叠样式表**）是一种用于描述HTML元素在网页上呈现的样式和布局的语言。通过学习CSS，我们可以使网页看起来更加美观、易于使用，为用户提供更好的体验。
  2. 设备和浏览器兼容性：CSS具有跨设备和浏览器的兼容性。学习CSS能帮助我们针对不同的设备（如桌面电脑、平板电脑、手机等）和浏览器（如Chrome、Firefox、Safari等）优化网页样式，确保在各种环境下都能提供一致的用户体验。
  3. 网页性能优化：通过有效地使用CSS，我们可以减少网页的加载时间、优化渲染速度，提高网站的性能。这对于用户体验和搜索引擎优化（SEO）都非常重要。
  4. 动画和交互：CSS3引入了许多新特性，如动画、过渡、变形等，使得我们可以仅使用CSS来实现一些原本需要JavaScript的动画和交互效果。这样可以减少对JavaScript的依赖，提高网站性能。
  5. 响应式设计：CSS的媒体查询功能使得我们可以为不同屏幕尺寸的设备创建自适应的布局，实现响应式设计。这对于提高网站在移动设备上的用户体验和适应性至关重要。
  6. 提高开发效率：掌握CSS可以帮助我们更快速地完成网页开发。通过组织良好的样式表，我们可以在多个页面之间重用样式，减少重复劳动，提高开发效率。
  7. 前端开发的基本技能：CSS是前端开发的三大基石之一（HTML、CSS和JavaScript），掌握CSS是成为一名合格的前端开发人员的基本要求。学习CSS可以为我们打开Web开发领域的大门，为我们的职业发展提供更多机会。

### CSS简要发展历史

> 1. 通过HTML提出：在早期的Web发展中，网页样式主要依赖于HTML元素属性，如`<font>`标签和`bgcolor`属性等，来控制页面的样式。随着Web技术的发展，这种方式逐渐显得不够灵活且难以维护。为了解决这个问题，CSS应运而生。CSS将样式与内容分离，使得网页样式的设计和维护变得更加简单高效。
> 2. CSS1：CSS1于1996年12月发布，是CSS的第一个正式版本。CSS1为Web开发者提供了基本的样式控制功能，包括字体、颜色、背景、边框、列表、定位等。这个版本使得开发者可以更方便地为网页添加样式，并且提高了网页的兼容性和可维护性。
> 3. CSS2：CSS2于1998年5月发布，是CSS的第二个版本。CSS2在CSS1的基础上新增了一些功能，如定位、浮动、层叠顺序（z-index）、生成内容、媒体查询等。这些新特性为Web开发者提供了更多的样式控制功能，使得网页布局和设计变得更加灵活。
> 4. CSS2.1：CSS2.1于2011年6月成为正式标准，是CSS2的一个修订版本。CSS2.1修复了CSS2中的一些错误，同时移除了一些不常用或难以实现的特性，提高了CSS的稳定性和互操作性。
> 5. CSS3：CSS3是CSS的最新版本，从2005年开始逐步推出。CSS3将CSS分为多个模块，每个模块负责一个特定的功能，如选择器、盒模型、背景和边框、文本效果、2D/3D转换、动画、过渡、多列布局等。这种模块化的设计使得CSS可以更快地更新和发展，同时也方便浏览器厂商逐步实现支持。CSS3引入了许多新特性和改进，大大丰富了Web开发者的工具箱，提高了网页的表现力和交互性。

![image-20230424200220652](.\byte-images\image-20230424200220652.png)

### 课程范围

![image-20230424201621385](.\byte-images\image-20230424201621385.png)

## 基础知识

### Casading规则(层叠优先集)

> 你觉得哪个颜色的会生效？

![image-20230424202454329](.\byte-images\image-20230424202454329.png)

#### 层叠三大规则

##### 样式表来源

> 在CSS中，样式表可以按照来源被划分为三种类型：用户代理样式、用户样式表和作者样式表。
>
> 1. 用户代理样式（User Agent Styles）：用户代理（User Agent，通常指浏览器）为HTML元素提供了一组默认的样式。这些样式在没有任何外部样式表的情况下应用于网页，以确保即使在没有CSS的情况下，网页内容依然具有基本的可读性和可访问性。用户代理样式因浏览器而异，通常包括基本的字体、颜色、边距等设置。
> 2. 用户样式表（User Stylesheets）：用户样式表是由用户自定义的样式表，可以用于覆盖用户代理样式和作者样式表中的某些样式。用户可以根据自己的偏好设置用户样式表，例如调整字体大小、颜色等。用户样式表主要用于改善网页的可访问性和可用性，满足个别用户的特殊需求。在现代浏览器中，用户可以通过安装扩展或修改浏览器设置来应用自定义的用户样式表。
> 3. 作者样式表（Author Stylesheets）：作者样式表是由网页开发者创建的样式表，用于定义网页的布局、外观和交互。作者样式表通常是网页设计的主要组成部分，它可以覆盖用户代理样式和用户样式表中的样式。作者样式表可以包含内联样式、嵌入式样式和外部样式表，以及通过`@import`引入的其他样式表。
>
> CSS的层叠规则（Cascading）决定了这三种类型的样式表如何相互作用和叠加。在确定一个元素的最终样式时，CSS会根据特指性（Specificity）、来源和声明的顺序来解析和应用这些样式表中的规则。这种机制允许用户代理、用户和作者之间实现样式的协同和定制

![image-20230424202718790](.\byte-images\image-20230424202718790.png)

##### 选择器优先级

![image-20230424203541852](.\byte-images\image-20230424203541852.png)

> 1. 元素选择器：根据HTML元素类型选择元素。例如，`p`选择所有的`<p>`元素。
> 2. 类选择器：根据元素的类属性选择元素。例如，`.my-class`选择具有类`my-class`的所有元素。
> 3. ID选择器：根据元素的ID属性选择特定元素。例如，`#my-element`选择ID为`my-element`的元素。
> 4. 属性选择器：根据元素的属性和属性值选择元素。例如，`[data-attribute]`选择具有`data-attribute`属性的所有元素，`[data-attribute="value"]`选择具有`data-attribute`属性且其值为`value`的所有元素。
> 5. 伪类选择器：根据元素的状态或文档结构选择元素。例如，`:hover`选择鼠标悬停在其上的元素，`:first-child`选择作为其父元素的第一个子元素的元素。
> 6. 伪元素选择器：选择元素的某个部分，例如内容的前后。例如，`::before`选择元素内容之前的一个虚拟元素，`::after`选择元素内容之后的一个虚拟元素。
> 7. 组合选择器：通过组合多个选择器来选择符合所有条件的元素。
>    - 后代选择器（空格）：例如，`div p`选择所有作为`<div>`元素后代的`<p>`元素。
>    - 子元素选择器（`>`）：例如，`div > p`选择所有作为`<div>`元素直接子元素的`<p>`元素。
>    - 相邻兄弟选择器（`+`）：例如，`div + p`选择紧跟在`<div>`元素后面的`<p>`元素。
>    - 一般兄弟选择器（`~`）：例如，`div ~ p`选择在`<div>`元素后面的所有`<p>`兄弟元素。
> 8. 多个选择器：使用逗号分隔的多个选择器可以同时选择符合任一选择器条件的元素。例如，`div, p`选择所有的`<div>`和`<p>`元素。

![image-20230424203907210](.\byte-images\image-20230424203907210.png)

> 在CSS中，选择器的优先级（也称为特指性，Specificity）决定了当多个规则应用于同一元素时，哪个规则将最终生效。特指性是根据选择器的组成部分计算出来的，主要涉及以下几个层次：
>
> 1. 内联样式（Inline styles）：内联样式具有最高的优先级，它们直接应用于HTML元素的`style`属性上。例如，`<div style="color: red;">`。
> 2. ID选择器：ID选择器具有较高的优先级，它们通过元素的ID属性进行选择。例如，`#my-element`。
> 3. 类选择器、属性选择器和伪类：这一层次的优先级低于ID选择器，包括类选择器（如`.my-class`）、属性选择器（如`[data-attribute]`）和伪类（如`:hover`）。
> 4. 元素选择器和伪元素：这一层次的优先级最低，包括元素选择器（如`div`）和伪元素（如`::before`）。
>
> 特指性是通过给每个层次的选择器分配权重并计算总和来确定的。例如：
>
> - 内联样式的权重为 1000
> - ID选择器的权重为 100
> - 类选择器、属性选择器和伪类的权重为 10
> - 元素选择器和伪元素的权重为 1
>
> 当多个选择器应用于同一元素时，具有较高特指性的选择器将覆盖较低特指性的选择器。如果两个选择器具有相同的特指性，那么它们将按照在CSS代码中出现的顺序进行解析，最后出现的选择器将覆盖之前的选择器。
>
> 需要注意的是，`!important`声明可以改变规则的优先级。在同一特指性级别下，带有`!important`声明的规则将优先于没有`!important`声明的规则。然而，如果有多个`!important`声明冲突，那么仍然需要参考特指性和源代码顺序来确定哪个规则生效。

![image-20230424204620632](.\byte-images\image-20230424204620632.png)

- 字节提供这一张最标准的计算表，分为a，b，c。其实就分别相当于权重值100，10，1。正常情况下，10倍的权重值差距基本上是不会被追赶上的。
- 我们通过这张表可以形象的看出谁的优先度更高，你可以把他的逗号去掉，直接当作整数看，比如(0,1,2)相当于12，(1,0,1)相当于101。谁的数字大谁的权重值就高，数字就相当于权重值了

##### 源码位置

> 以下生效的样式效果是哪个？
>
> - 在这段代码中，CSS 优先级从高到低如下：
>
>   1. `.red` 选择器（在内联样式表中定义）
>   2. `.blue` 选择器（在外部样式表 index.css 中定义）
>   3. `.green` 选择器（在外部样式表 index.css 中定义）
>
>   尽管这些选择器都具有相同的优先级权重，但是它们在样式表中的顺序决定了哪一个生效。根据 CSS 的规则，当有多个相同优先级的选择器存在时，最后出现的选择器将具有更高的优先级(最后声明的生效)。
>
>   在这个例子中，`<h1>` 标签同时具有`green`、`blue` 和 `red` 类。首先，`green` 类的样式被应用，然后被 `blue` 类的样式覆盖(red`类是在link类前面去声明的，所以red类反而是最先被覆盖的)。因此，这段代码的最终效果是 `<h1>` 标签的文字颜色为绿色，字体大小为 48 像素（由 `.green` 类控制）。
>
>   ```css
>   color: green;
>   font-size: 48px;
>   ```

![image-20230424205434311](.\byte-images\image-20230424205434311.png)

```html
//HTML文件
<head>
    <style>
        .red{
            color:red;
        }
    </style>
    <link rel="stylesheet" href="./index.css" type="text/css">
</head>
<body>
<h1 class="green blue red">
    A Tittle
</h1>
</body>
```

```css
/*index.css文件*/
.blue{
    color:blue;
    font-size:48px;
}

.green{
    color:green;
}
```

> 由层叠概念引申出的css代码good practice(好习惯)
>
> 1. 选择器尽量少用id(不好进行复用)
> 2. 尽量不要用！important(后面想扩展的灵活性会被压缩)
> 3. 自己的样式加载在引用库样式的后面(保证我们自己的样式优先生效)

### 继承

> CSS 中的继承是一种机制，通过它，子元素可以从其父元素接收样式属性值。这使得我们可以在一个地方设置通用样式，然后让其他元素自动继承这些样式，从而减少代码的重复和提高可维护性。
>
> 并非所有的 CSS 属性都是可继承的。一些常见的可继承属性包括：`color`、`font`、`text-align`等。这些属性在父元素上设置后，子元素会自动继承这些样式，除非子元素上明确设置了不同的值。

![image-20230424214131397](.\byte-images\image-20230424214131397.png)

### 值和单位

![image-20230424214334767](.\byte-images\image-20230424214334767.png)

#### 单位

1. 长度单位
   - 绝对长度：
     - `px`（像素）：最常见的单位，通常用于屏幕显示。一个像素对应于屏幕上的一个点。
     - `cm`（厘米）、`mm`（毫米）、`in`（英寸）、`pt`（磅，1/72 英寸）、`pc`（点，1/6 英寸）：这些单位主要用于打印媒体，它们基于物理尺寸。
   - 相对长度：
     - `em`：相对于当前元素的字体大小。例如，如果当前字体大小是 16px，那么 2em 等于 32px。
     - `rem`：相对于根元素（通常是 `<html>`）的字体大小。与 `em` 类似，但始终基于根元素的字体大小。
     - `vw`（视窗宽度）、`vh`（视窗高度）：相对于视口（浏览器可见区域）的宽度和高度。1vw 等于视口宽度的 1%，1vh 等于视口高度的 1%。
     - `vmin`、`vmax`：分别等于视口宽度和高度中的较小值和较大值。
2. 角度单位
   - `deg`（度）：角度单位，一圈有 360 度。
   - `rad`（弧度）：角度单位，一圈有 2π 弧度。
   - `grad`（梯度）：角度单位，一圈有 400 梯度。
   - `turn`：角度单位，一圈等于 1 turn。
3. 时间单位
   - `s`（秒）：时间单位，用于表示秒。
   - `ms`（毫秒）：时间单位，用于表示毫秒，1ms 等于 0.001s。
4. 分辨率单位
   - `dpi`（每英寸点数）：表示每英寸内有多少个点（像素）。
   - `dpcm`（每厘米点数）：表示每厘米内有多少个点（像素）。
   - `dppx`（每像素点数）：表示每像素内有多少个点（像素），1dppx 等于 96dpi。

#### 值

1. 文本类：
   - 字符串：用于表示文本值，通常用双引号或单引号括起来。例如：`content: "Hello, World!";`
   - 关键字：用于表示特定属性的预定义值。例如：`display: block;`
2. 数值类：
   - 整数：表示一个没有小数部分的数字值。例如：`z-index: 3;`
   - 小数：表示一个有小数部分的数字值。例如：`opacity: 0.5;`
   - 百分比：表示一个基于另一个值的百分比。例如：`width: 50%;`
   - 长度：表示一个具有单位的长度值。例如：`width: 100px;` 或 `padding: 1em;`
   - 角度：表示一个具有角度单位的值。例如：`rotate(45deg);`
3. 颜色：
   - 预定义颜色：使用 CSS 中预定义的颜色名称。例如：`color: red;`
   - 十六进制颜色：使用 3 或 6 个十六进制数字表示颜色。例如：`color: #ff0000;` 或 `color: #f00;`
   - RGB 颜色：使用三个介于 0 和 255 之间的整数表示颜色。例如：`color: rgb(255, 0, 0);`
   - RGBA 颜色：与 RGB 类似，但还包括一个表示透明度的值（0 到 1 之间的小数）。例如：`color: rgba(255, 0, 0, 0.5);`
   - HSL 颜色：使用色相（0 到 360 之间的整数）、饱和度（百分比）和亮度（百分比）表示颜色。例如：`color: hsl(0, 100%, 50%);`
   - HSLA 颜色：与 HSL 类似，但还包括一个表示透明度的值（0 到 1 之间的小数）。例如：`color: hsla(0, 100%, 50%, 0.5);`
4. 函数生成：
   - URL：使用 `url()` 函数表示一个资源的 URL。例如：`background-image: url("image.jpg");`
   - calc()：使用 `calc()` 函数进行数学计算。例如：`width: calc(100% - 20px);`
   - linear-gradient()：使用 `linear-gradient()` 函数创建线性渐变。例如：`background-image: linear-gradient(to right, red, blue);`
   - radial-gradient()：使用 `radial-gradient()` 函数创建径向渐变。例如：`background-image: radial-gradient(circle, red, blue);`
   - transform 函数：例如：`rotate()`、`scale()`、`translate()` 和 `skew()` 等，用于在不同轴上

### 盒模型

> 视觉格式化模型：[视觉格式化模型 - CSS：层叠样式表 | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Visual_formatting_model)

![image-20230424220126721](.\byte-images\image-20230424220126721.png)

![image-20230424220426144](.\byte-images\image-20230424220426144.png)

#### 实现三角形

```html
/* 创建一个空的 div 元素，将其作为三角形 */
<div class="triangle"></div>

/* 为三角形添加样式 */
<style>
  .triangle {
    /* 将 div 的宽度和高度设置为 0，这样它本身不会占据空间 */
    width: 0;
    height: 0;

    /* 使用 border 来制作三角形 */
    /* 三角形的底边宽度，此处设为 100px */
    border-left: 50px solid transparent;
    border-right: 50px solid transparent;

    /* 三角形的高度，此处设为 100px */
    border-bottom: 100px solid red;

    /* 如果想创建其他方向的三角形，可以尝试更改 border 的设置 */
  }
</style>
```

#### 实现固定比例矩形

```html
<div class="rectangle"></div>
<style>
.rectangle {
  /* 设置矩形的宽度 */
  width: 200px;

  /* 使用 aspect-ratio 属性设置矩形的宽高比为 4:3 */
  aspect-ratio: 4 / 3;

  /* 设置矩形的背景颜色和边框 */
  background-color: lightblue;
  border: 2px solid black;
}

</style>
```

#### 实现水平居中

```html
<div class="container">
  <div class="centered">
      I am XiaoYu.
  </div>
</div>
```

```css
/* 设置 body 和 html 的高度为 100%，这样才能让外层容器占据整个屏幕 */
html, body {
  height: 100%;
  margin: 0;
}

.container {
  /* 将容器的宽度设置为 100%，以使其占据整个屏幕宽度 */
  width: 100%;

  /* 使用 flex 布局将子元素水平居中 */
  display: flex;
  justify-content: center;
}

.centered {
  /* 设置要居中的元素的样式，例如背景色、内边距等 */
  background-color: lightblue;
  padding: 20px;
  border: 2px solid black;
}
```

#### 实现渐变边框

> 和视频中不一样的思路。采用了伪元素
>
> 1. 渐变边框的实现：通过创建一个伪元素并设置其背景为渐变，我们可以实现一个渐变边框的效果。伪元素将覆盖在其父元素（`.gradient-border`）上，但位于父元素的内容之下。
> 2. 边框宽度控制：设置伪元素的 `padding`，我们可以控制渐变边框的宽度。伪元素的 `padding` 就是渐变边框的宽度。
> 3. 与父元素内容分离：伪元素独立于其父元素的内容，因此我们可以在不影响父元素内容的情况下实现渐变边框效果。这使得父元素的内容可以维持其原始样式（例如，背景颜色、边框和圆角），而伪元素专门负责实现渐变边框效果。
>
> 伪元素在这里中主要用于创建一个渐变边框，同时不影响其父元素的内容。这种方法避免了直接修改父元素样式的复杂性，使得实现渐变边框变得更加简单。

```html
<div class="gradient-border">
  <p>这是一个带渐变边框的元素。</p>
</div>

<style>
.gradient-border {
  /* 设置内边距，以便为伪元素留出空间 */
  padding: 10px;
  position: relative;
  display: inline-block;
}

.gradient-border::before {
  /* 创建伪元素，并设置为相对于其父元素定位 */
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: -1;

  /* 使用渐变作为边框的背景 */
  background-image: linear-gradient(to right, red, orange, yellow, green, blue, indigo, violet);

  /* 设置边框的宽度 */
  padding: 3px;

  /* 使伪元素的边框圆角与父元素的边框圆角相匹配 */
  border-radius: 5px;
}

p {
  /* 设置段落的背景颜色、边框和圆角 */
  background-color: white;
  border: 1px solid rgba(0, 0, 0, 0.1);
  border-radius: 5px;
  margin: 0;
  padding: 10px;
}

</style>
```

#### 负外边距

> 负外边距（negative margin）是 CSS 盒模型中的一个概念，它可以在盒子之间创建重叠或者调整盒子的位置。与正外边距（positive margin）增加空间不同，负外边距会减少空间。以下是负外边距在不同情况下的作用：
>
> 1. 水平方向上的负外边距：
>    - 当两个块级元素并排时，它们的负外边距会让它们互相重叠。这可以用于调整元素之间的距离或者实现特定的布局效果。
>    - 当一个元素的左边距为负时，它会向左移动；当一个元素的右边距为负时，它会向右移动。这可以用于调整元素相对于其父容器的位置。
> 2. 垂直方向上的负外边距：
>    - 当两个垂直排列的块级元素具有负外边距时，它们会互相重叠。这可以用于调整元素之间的距离，特别是当需要减少默认的垂直间距时。
>    - 当一个元素的上边距为负时，它会向上移动；当一个元素的下边距为负时，它会拉近下一个元素与它之间的距离。
>
> 需要注意的是，负外边距可能会导致布局上的问题，例如元素重叠、脱离正常文档流等。因此，在使用负外边距时需要谨慎，确保它不会导致不可预期的布局问题。在某些情况下，使用其他布局技术（如 Flexbox、Grid 或定位）可能会更合适。

![image-20230424223248775](.\byte-images\image-20230424223248775.png)

## 布局和定位

![image-20230424224056212](.\byte-images\image-20230424224056212.png)

### 常规流布局

> 常规流（Normal Flow）布局，也被称为文档流（Document Flow）布局，是浏览器默认的 CSS 布局方式。在常规流布局中，元素按照它们在 HTML 代码中的顺序自上而下、自左向右地排列。常规流布局主要包括两类元素：块级元素（block-level elements）和行内元素（inline elements）。
>
> 1. 块级元素：
>    - 块级元素会独占一行，即它们会从上到下排列。
>    - 块级元素默认的宽度为其父容器的宽度，高度取决于其内容。
>    - 常见的块级元素有：`<div>`、`<p>`、`<h1>`-`<h6>`、`<ul>`、`<ol>`、`<li>`等。
> 2. 行内元素：
>    - 行内元素会在同一行内从左到右排列，直到容器宽度不足时才会换行。
>    - 行内元素的宽度和高度取决于其内容，而不是父容器的尺寸。
>    - 常见的行内元素有：`<span>`、`<a>`、`<strong>`、`<em>`、`<img>`等。
>
> 在常规流布局中，元素遵循一定的规则进行排列，这些规则取决于元素的 display 属性。display 属性有几个常用值，例如：`block`（块级元素）、`inline`（行内元素）和 `inline-block`（既具有块级元素的特点，又具有行内元素的特点）。
>
> 虽然常规流布局是浏览器的默认布局方式，但现代前端开发中，很多布局需求无法仅依靠常规流实现。因此，CSS 提供了更加强大和灵活的布局技术，如 Flexbox、Grid、定位（Positioning）等，来满足各种布局需求

<img src=".\byte-images\image-20230424224543550.png" alt="image-20230424224543550" style="zoom:33%;" />

> BFC（Block Formatting Context，块级格式化上下文）和 IFC（Inline Formatting Context，行内格式化上下文）是两种不同的 CSS 布局方式(格式规范)，它们影响着元素如何在页面上排列。在解释 BFC 和 IFC 之前，需要了解一下“格式化上下文（Formatting Context）”。
>
> BFC是什么?
>
> - BFC本身是block formatting context的缩写，是一种格式规范。如果说一个盒子是BFC或者有BFC特性，那么BFC表示的是block formatting.context root。不管它外部显示类型是什么，但是它的内部显示类型是flow-root,.其实就是这个盒子内部形成了一个新的块级格式化上下文。

<img src=".\byte-images\image-20230424224558320.png" alt="image-20230424224558320" style="zoom:50%;" />

#### 块级格式化上下文(block formatting context)

<img src=".\byte-images\image-20230424225721707.png" alt="image-20230424225721707" style="zoom:50%;" />

格式化上下文的布局规范为：

> - “在一个块格式区域中，盒子会从包含块的顶部开始，按序垂直排列。同级盒子间的垂直距离会由“margin“属性决定。相邻两个块级盒子之间的垂直间距会遵循外边距折叠原则被折叠。
> - 在一个块格式区域中，每个盒子的左外边缘会与包含块左边缘重合（如果是从右到左的排版顺序，则盒子的右外边缘与包含块右边缘重合）。

格式化上下文（Formatting Context）是 CSS 盒子模型的一部分，它定义了一组规则来描述如何对元素进行布局。

1. BFC（块级格式化上下文）：

   - BFC 是块级元素的布局环境，这些元素按照垂直方向一个接一个地排列。
   - 在 BFC 中，每个元素的左外边距与上一个元素的右外边距相邻，但不会重叠。
   - BFC 中的元素不会受到外部浮动元素的影响，可以用来清除浮动。
   - BFC 是一个独立的容器，内部元素的布局不会影响到外部元素，反之亦然。

   BFC 可以通过以下方式触发：

   - `display` 属性值为 `block`、`inline-block`、`table`、`table-cell`、`table-caption` 等；
   - `position` 属性值为 `absolute` 或 `fixed`；
   - `float` 属性值不为 `none`；
   - `overflow` 属性值不为 `visible`。

2. IFC（内联级格式化上下文）：

   - IFC 是行内元素的布局环境，这些元素按照水平方向一个接一个地排列。
   - 在 IFC 中，行内元素会根据它们在文档流中的顺序排列，并根据字体的基线对齐。
   - IFC 会自动调整行高以适应最高的行内元素，因此，IFC 中的行高可能不同。
   - IFC 中的元素与块级元素不同，不会独占一行，而是尽可能地填充水平空间，直到容器宽度不足时才会换行。

   IFC 可以通过以下方式触发：

   - `display` 属性值为 `inline`、`inline-block`、`inline-table` 等

#### 外边距塌陷

> 外边距塌陷（Margin Collapsing）是 CSS 布局中一个特殊的现象，它发生在垂直方向上相邻的外边距（Margin）之间。当两个或多个垂直方向上相邻的外边距遇到时，它们会合并为一个外边距，而不是简单地相加。合并后的外边距大小等于相邻外边距中最大的一个。

![image-20230424225804301](.\byte-images\image-20230424225804301.png)

- 外边距塌陷主要出现在以下三种情况：

  1. 相邻兄弟元素的外边距塌陷： 当两个兄弟元素垂直相邻时，它们之间的上下外边距会发生塌陷。合并后的外边距大小等于这两个外边距中的较大值。

  ```html
  <div style="margin-bottom: 20px;"></div>
  <div style="margin-top: 30px;"></div>
  <!--这两个相邻的 div 元素的垂直间距将为 30px（较大的外边距），而不是 20px 和 30px 的总和（50px）-->
  ```

  2. 父元素与第一个或最后一个子元素的外边距塌陷： 当一个块级元素没有上边框、上内边距以及没有内联内容（例如空的 div 或只包含块级子元素的 div）时，该元素的上外边距会与其第一个子元素的上外边距发生塌陷。同样，如果一个元素没有下边框、下内边距以及没有内联内容时，该元素的下外边距会与其最后一个子元素的下外边距发生塌陷。

  ```html
  <div style="margin-top: 20px;">
    <div style="margin-top: 30px;"></div>
  </div>
  <!--上面的父元素和子元素之间的垂直间距将为 30px（较大的外边距），而不是 20px 和 30px 的总和（50px）-->
  ```

  3. 空块级元素的上下外边距塌陷： 当一个块级元素为空（没有内容、内边距和边框），它的上下外边距会发生塌陷，即合并为一个外边距。

  ```html
  <div style="margin-top: 20px; margin-bottom: 30px;"></div>
  <!--上面的空 div 元素的上下外边距将合并为一个 30px 的外边距-->
  ```

- 消除外边距的方法：

  1. 使用内边距（Padding）： 可以使用内边距替代外边距来创建垂直间距。这样可以避免外边距塌陷的问题。

  2. 使用边框（Border）： 如果父元素有边框，那么父元素和子元素的外边距将不会发生塌陷。给父元素添加一个透明的边框即可消除外边距塌陷。

     ```css
     .parent {
       border-top: 1px solid transparent;
     }
     ```

  3. 创建 BFC（Block Formatting Context）： 通过给父元素创建一个 BFC，可以阻止外边距塌陷。创建 BFC 的方法有很多，例如设置 `overflow` 属性为 `auto`、`hidden` 或 `scroll`。

     ```css
     .parent {
       overflow: hidden;
     }
     ```

  4. 使用伪元素（Pseudo-element）： 在父元素内部添加一个 `::before` 伪元素，并设置其 `display` 属性为 `table`。这样可以创建一个新的 BFC，从而避免外边距塌陷

     ```css
     .parent::before {
       content: '';
       display: table;
     }
     ```

  5. 将元素转换为 Flex 或 Grid 容器： 将父元素设置为 Flex 或 Grid 容器时，外边距塌陷不再发生

     ```css
     .parent {
       display: flex;
       flex-direction: column;
     }
     ```

     ```css
     .parent {
       display: grid;
     }
     ```

#### 内联级格式化上下文

> 介绍：内联级格式化上下文（Inline Formatting Context，简称 IFC）是 CSS 渲染模型中的一种布局类型。在内联级格式化上下文中，元素以水平方向进行布局。这些元素称为内联级元素（Inline-level elements），它们不会独占一行，而是与其他内联级元素排在同一行内。常见的内联级元素有 `<span>`、`<em>`、`<strong>`、`<a>` 等。需要注意的是，内联级元素的宽度只受内容本身的宽度影响，而无法通过设置 `width` 和 `height` 属性进行调整
>
> “在内联格式区域中，盒子会从包含块的顶部开始，按序水平排列。只有水平外边距、边框和内边距会被保留。这些盒子可以以不同的方式在垂直方向上对齐：可以底部对齐或顶部对齐，或者按文字底部进行对齐。我们把包含一串盒子的矩形区域称为一个线条框。（The rectangular area that contains the boxes that form a line is called a line box.)"

![image-20230424231853764](.\byte-images\image-20230424231853764.png)

- 水平方向排列： 内联级元素按从左到右（在 LTR（从左到右）文本方向）的顺序排列。如果行内空间不足以容纳一个内联级元素，它会换行到下一行继续排列。
- 垂直对齐： 内联级元素支持 `vertical-align` 属性，可以调整元素在垂直方向上的对齐方式。例如，可以设置元素相对于基线（baseline）向上或向下对齐。
- 行框（Line box）： 在 IFC 中，内联级元素被包含在行框内。行框的高度由内联级元素的高度决定。每一行都有一个行框，行框之间没有垂直外边距塌陷。
- 不支持宽度和高度设置： 内联级元素不支持设置 `width` 和 `height` 属性。它们的宽度由内容决定，而高度则由 `line-height` 属性决定。
- 支持水平外边距、内边距和边框： 内联级元素支持设置水平方向的外边距、内边距和边框，但垂直方向的设置不会影响其他内联级元素的布局。
- 匿名文本： 在 IFC 中，不属于任何内联级元素的文本被视为匿名文本。匿名文本会按照内联级元素的布局规则进行排列。

##### 常用的案例

- 单行文字垂直居中，比如标题
  - 利用了Iine-height的垂直居中特性

```html
<div class="container">
    XiaoYu
</div>

<style>
    .container {
        height: 100px; /* 设置容器高度 */
        background-color: #f0f0f0; /* 设置容器背景色，便于观察垂直居中效果 */
        font-size: 24px; /* 设置字体大小 */
        line-height: 100px; /* 设置行高等于容器高度，实现垂直居中 */
    }
</style>
```

- 文字和icon垂直对齐
  - 利用了line box中计算高度的原理和vertical-align的设置，垂直对齐，但不是完全垂直居中如果设置父元素font-size:0,基线和中线重叠，则居中对齐

![image-20230424235549137](.\byte-images\image-20230424235549137.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>文字和icon垂直对齐</title>
  <style>
  .image{
      width: 24px;
      height: 24px;
      vertical-align: middle;
  }
  .text{
      font-size: 16px;
      line-height: 32px;
      margin-left: 4px;
      vertical-align: middle;
  }
  
  </style>
</head>
<body>
  <div class="wrap">
    <img class="image" src="你的图片地址">
    <span class="text">XiaoYu笔记，你值得拥有</span>
  </div>
</body>
</html>
```

### 弹性盒子

<img src=".\byte-images\image-20230424235617958.png" style="zoom:50%;" />

<img src=".\byte-images\image-20230424235708937.png" alt="image-20230424235708937" style="zoom:50%;" />

#### 使用弹性盒子布局的示例

> 展示灵活的文件长度省略演示[Using Flexbox and text ellipsis together | CSS-Tricks - CSS-Tricks](https://css-tricks.com/using-flexbox-and-text-ellipsis-together/)

```html
<div class="filename">
  <span class="filename__base">this-file-has-a-really-really-really-long-filename.</span>
  <span class="filename__extension">pdf</span>
</div>
```

```css
.filename {
  display: flex;
  min-width: 0;
}

.filename__base {
  text-overflow: ellipsis;
  white-space: nowrap;
  overflow: hidden;
}

.filename__extension {
  flex-shrink: 0;
}

body {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
  max-width: 470px;
  margin: 0 50px;
  font-size: 20px;
  font-family: system-ui,-apple-system,BlinkMacSystemFont,Segoe UI,Roboto,Helvetica Neue,Arial,Noto Sans,sans-serif,Apple Color Emoji,Segoe UI Emoji,Segoe UI Symbol,Noto Color Emoji;
}
```

> 显示筛子的六个不同的面

![image-20230425000432838](.\byte-images\image-20230425000432838.png)

![image-20230425002310184](.\byte-images\image-20230425002310184.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Dice Faces with Flexbox</title>
  <style>
      .container{
          display: flex;
      }
      .dice {
          display: flex;
          flex-wrap: wrap;
          width: 120px;
          height: 120px;
          background-color: #eee;
          border: 1px solid #ccc;
          margin: 10px;
      }

      .dice-row {
          display: flex;
          justify-content: space-between;
          width: 100%;
          padding: 10px;
      }

      .dot {
          width: 20px;
          height: 20px;
          background-color: black;
          border-radius: 50%;
      }

      .empty {
          width: 20px;
          height: 20px;
      }
  </style>
</head>
<body>
<div class="container">
  <div class="dice">
    <div class="dice-row">
      <div class="empty"></div>
      <div class="empty"></div>
      <div class="empty"></div>
    </div>
    <div class="dice-row">
      <div class="empty"></div>
      <div class="dot"></div>
      <div class="empty"></div>
    </div>
    <div class="dice-row">
      <div class="empty"></div>
      <div class="empty"></div>
      <div class="empty"></div>
    </div>
  </div>

  <div class="dice">
    <div class="dice-row">
      <div class="empty"></div>
      <div class="empty"></div>
      <div class="dot"></div>
    </div>
    <div class="dice-row">
      <div class="empty"></div>
      <div class="dot"></div>
      <div class="empty"></div>
    </div>
    <div class="dice-row">
      <div class="dot"></div>
      <div class="empty"></div>
      <div class="empty"></div>
    </div>
  </div>

  <div class="dice">
    <div class="dice-row">
      <div class="dot"></div>
      <div class="empty"></div>
      <div class="dot"></div>
    </div>
    <div class="dice-row">
      <div class="empty"></div>
      <div class="dot"></div>
      <div class="empty"></div>
    </div>
    <div class="dice-row">
      <div class="dot"></div>
      <div class="empty"></div>
      <div class="dot"></div>
    </div>
  </div>
</div>

</body>
</html>
```

### Grid

> 2017年推出的Gd布局可以定义由行和列组成的二维布局，然后将元素放置到网格中。元素可以只占其中一个单元格，也可以占据多行或多列。
>
> CSS Grid Layout（网格布局）是一个用于创建二维网格的 CSS 布局技术。它允许你以行和列的形式设计复杂的布局，而不需要使用浮动、定位和其他传统布局方法。Grid 布局特别适合用于构建响应式和自适应设计，因为它可以轻松地在不同的屏幕尺寸和设备上调整布局。
>
> CSS Grid 的一些基本概念：
>
> 1. 容器（Container）：要使用 Grid 布局，首先需要将一个元素声明为网格容器。这可以通过将 `display` 属性设置为 `grid` 或 `inline-grid` 来实现。
>
>    ```css
>    .container {
>      display: grid;
>    }
>    ```
>
> 2. 网格项（Grid Items）：容器的直接子元素自动成为网格项。
>
> 3. 网格线（Grid Lines）：网格布局由水平和垂直的网格线组成，这些网格线定义了网格的行和列。网格线可以用数字或名称来引用。
>
> 4. 网格轨道（Grid Tracks）：网格轨道是由两条相邻的网格线之间的区域组成的，可以是行或列。
>
> 5. 网格单元（Grid Cells）：由相邻的两条行网格线和两条列网格线组成的一个单元格称为网格单元。
>
> 6. 网格区域（Grid Areas）：网格区域是由任意数量的网格单元组成的矩形区域。网格区域可以用来放置网格项。

<img src=".\byte-images\image-20230425001259687.png" alt="image-20230425001259687" style="zoom:50%;" />

```css
.grid-container{
	display:grid;
	grid-template-columns:100px 1fr;
	grid-template-rows:60px 6fr 1fr;
}
```

![image-20230425001914835](.\byte-images\image-20230425001914835.png)

#### 筛子示例(Grid版本)

![image-20230425002536598](.\byte-images\image-20230425002536598.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Dice Faces with Grid</title>
  <style>
      .dice {
          display: grid;
          grid-template-rows: repeat(3, 1fr);
          grid-template-columns: repeat(3, 1fr);
          width: 100px;
          height: 100px;
          background-color: #eee;
          border: 1px solid #ccc;
          margin-bottom: 10px;
      }

      .dot {
          width: 20px;
          height: 20px;
          background-color: black;
          border-radius: 50%;
          align-self: center;
          justify-self: center;
      }

      .empty {
          width: 20px;
          height: 20px;
      }
  </style>
</head>
<body>
<!-- Dice Face 1 -->
<div class="dice">
  <div class="empty"></div>
  <div class="empty"></div>
  <div class="empty"></div>
  <div class="empty"></div>
  <div class="dot"></div>
  <div class="empty"></div>
  <div class="empty"></div>
  <div class="empty"></div>
  <div class="empty"></div>
</div>

<!-- Dice Face 3 -->
<div class="dice">
  <div class="empty"></div>
  <div class="empty"></div>
  <div class="dot"></div>
  <div class="empty"></div>
  <div class="dot"></div>
  <div class="empty"></div>
  <div class="dot"></div>
  <div class="empty"></div>
  <div class="empty"></div>
</div>

<!-- Dice Face 5 -->
<div class="dice">
  <div class="dot"></div>
  <div class="empty"></div>
  <div class="dot"></div>
  <div class="empty"></div>
  <div class="dot"></div>
  <div class="empty"></div>
  <div class="dot"></div>
  <div class="empty"></div>
  <div class="dot"></div>
</div>
</body>
</html>
```

#### Gird和Flex布局的使用策略

![image-20230425002729645](.\byte-images\image-20230425002729645.png)

### 定位

> 为了我们可以在文档流的基础上，让元素移动，做出更多灵活的改变。当position属性的取值非staticl的时候，可以使用top,right,,bottom,left对其进行定位
>
> 1. `static`： 默认值，元素遵循正常的文档流。在这种情况下，`top`、`right`、`bottom` 和 `left` 属性不会生效。
>
> 2. `relative`： 元素相对于其在正常文档流中的位置进行定位。即使对元素进行了移动，它在文档流中的空间仍然被保留。可以使用 `top`、`right`、`bottom` 和 `left` 属性来调整元素的位置。
>
>    例如，如果一个元素的 `position` 被设置为 `relative`，并且 `top` 设置为 `20px`，那么这个元素将向下移动 20 像素，但它原本在文档流中的位置仍然会被保留。
>
> 3. `absolute`： 元素相对于最近的非 `static` 祖先元素进行定位。如果没有非 `static` 祖先元素，它将相对于文档的初始包含块进行定位。绝对定位的元素从文档流中移除，不占据空间。可以使用 `top`、`right`、`bottom` 和 `left` 属性来调整元素的位置。
>
> 4. `fixed`： 元素相对于浏览器窗口进行定位，不随页面滚动而移动。固定定位的元素从文档流中移除，不占据空间。可以使用 `top`、`right`、`bottom` 和 `left` 属性来调整元素的位置。
>
> 5. `sticky`： 元素在滚动过程中在某种程度上表现为相对定位，又在某种程度上表现为固定定位。当元素在视口内时，它的行为类似于 `relative` 定位，而当元素在视口外时，它的行为类似于 `fixed` 定位。可以使用 `top`、`right`、`bottom` 和 `left` 属性来调整元素的位置。
>
> 注意：`sticky` 定位可能在某些浏览器中不受支持或支持有限。为了确保兼容性，请务必在使用 `sticky` 之前检查 [Can I use](https://caniuse.com/)。

![image-20230425003315201](.\byte-images\image-20230425003315201.png)

## 层叠上下文

> 层叠上下文是对TML元素的三维构想，将元素沿着垂直屏幕的虚构的Z轴排开
>
> 层叠上下文（Stacking Context）它用于决定元素在页面上的层叠顺序。层叠上下文由具有特定属性的元素生成，这些属性会影响该元素及其子元素在 z 轴上的渲染顺序。z 轴是垂直于屏幕的轴线，用于表示页面中元素的前后顺序

![image-20230425003713253](.\byte-images\image-20230425003713253.png)

<img src=".\byte-images\image-20230425003932210.png" alt="image-20230425003932210" style="zoom:50%;" />

> 一些创建层叠上下文的常见属性：
>
> 1. 根元素（`<html>`）自然创建一个层叠上下文。
> 2. 元素的 `position` 属性值为 `absolute` 或 `relative` 且 `z-index` 值不是 `auto`。
> 3. 元素的 `position` 属性值为 `fixed` 或 `sticky`（在兼容的浏览器中）。
> 4. 元素的 `opacity` 属性值小于 1。
> 5. 元素的 `transform`、`filter`、`perspective`、`clip-path`、`mask`、`mask-image`、`mask-border` 属性值不是 `none`。
> 6. 元素的 `isolation` 属性值为 `isolate`。
> 7. 元素的 `mix-blend-mode` 属性值不是 `normal`。
> 8. 元素的 `z-index` 属性值是一个整数，且其 `display` 属性值为 `flex` 或 `grid`。
>
> 在一个层叠上下文中，元素的层叠顺序由以下因素决定：
>
> 1. 层叠上下文的 z-index 值。较高的 z-index 值会使元素在较低的 z-index 值之上显示。
> 2. 当 z-index 相同时，元素在 HTML 代码中的顺序决定层叠顺序。位于后面的元素将覆盖位于前面的元素。

### 层叠上下文(The stacking context) 一 stacking order

- **层叠顺序不仅指不同的层叠上下文的顺序，同一个层叠上下文内，元素间也有顺序：**

> 在同一个层叠上下文中，元素之间的层叠顺序是按照以下规则确定的：
>
> 1. 背景和边框：层叠上下文元素的背景和边框位于最底层。
> 2. 负 z-index：拥有负 z-index 值的子元素按照 z-index 从小到大的顺序绘制。数值小的元素会被数值大的元素覆盖。
> 3. 块级元素：按照 HTML 代码中的顺序绘制非定位的块级元素。位于后面的元素将覆盖位于前面的元素。
> 4. 浮动元素：按照 HTML 代码中的顺序绘制浮动元素。位于后面的元素将覆盖位于前面的元素。
> 5. 内联元素：按照 HTML 代码中的顺序绘制非定位的内联元素。位于后面的元素将覆盖位于前面的元素。
> 6. z-index 为 0 或 auto 的定位元素：按照 HTML 代码中的顺序绘制 z-index 为 0 或 auto 的定位元素。位于后面的元素将覆盖位于前面的元素。
> 7. 正 z-index：拥有正 z-index 值的子元素按照 z-index 从小到大的顺序绘制。数值小的元素会被数值大的元素覆盖。
>
> 在同一个层叠上下文中，元素之间的层叠顺序受多种因素影响，包括元素的类型（如块级、内联、浮动或定位元素）、z-index 值以及在 HTML 代码中的顺序。

![image-20230425010448208](.\byte-images\image-20230425010448208.png)

#### 案例

![image-20230425011910445](.\byte-images\image-20230425011910445.png)

![image-20230425011932013](.\byte-images\image-20230425011932013.png)

### 编写z-index的建议

1. 使用css变量或者预处理语言的变量，管理z-index的值
2. 将预设间隔设置10或100，方便后续的插入

<img src=".\byte-images\image-20230425012100511.png" style="zoom:50%;" />

## 变形、过渡、动画

### transform 变形 3D

> CSS `transform` 属性允许我们对元素进行 2D 或 3D 变换。这里我们主要讨论一下 3D 变换相关的属性。
>
> 1. `transform`：这是一个复合属性，可以包含一个或多个 3D 变换函数。以下是一些常用的 3D 变换函数：
>    - `rotateX(angle)`：绕 X 轴旋转指定的角度。
>    - `rotateY(angle)`：绕 Y 轴旋转指定的角度。
>    - `rotateZ(angle)`：绕 Z 轴旋转指定的角度。
>    - `scale3d(sx, sy, sz)`：分别沿 X、Y 和 Z 轴缩放元素。
>    - `translate3d(tx, ty, tz)`：分别沿 X、Y 和 Z 轴移动元素。
>    - `perspective(p)`：设置透视距离。较小的值会增强 3D 效果。
> 2. `transform-origin`：定义变换的基点，即元素进行变换时以哪个点为中心。默认值为 "50% 50% 0"，即元素的中心点。
> 3. `transform-style`：设置元素的子元素是否位于 3D 空间中。取值为 `flat`（默认）或 `preserve-3d`。当设置为 `preserve-3d` 时，子元素将保留其 3D 位置。
> 4. `perspective`：定义观察者距离元素的距离，对于创建 3D 效果非常有用。较小的值会增强 3D 效果。此属性通常应用于父元素上。
> 5. `perspective-origin`：定义透视投影点的位置。默认值为 "50% 50%"，即元素的中心点。

![image-20230425012348662](.\byte-images\image-20230425012348662.png)

#### 筛子案例

![image-20230425012736413](.\byte-images\image-20230425012736413.png)

> 这里有个彩蛋案例(纯CSS实现)，请将代码复制到编辑器中运行
>
> 字节官方提供的案例：[3D Dice (codepen.io)](https://codepen.io/yao-mo/pen/YzjYLeO)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>3D XiaoYu的彩蛋</title>
  <style>
      /* 设置骰子的基本样式 */
      .dice {
          width: 100px; /* 骰子的宽度 */
          height: 100px; /* 骰子的高度 */
          position: relative; /* 使用相对定位，使子元素可以相对于此元素进行定位 */
          perspective: 800px; /* 设置 3D 视图距离，让骰子看起来有透视效果 */
          margin: 200px; /* 设置外边距，将骰子放置在页面中间 */
      }

      /* 设置骰子容器的样式，用于包含 6 个骰子面 */
      .dice-container {
          width: 100%; /* 容器宽度设置为 100%，使其和父元素（骰子）一样宽 */
          height: 100%; /* 容器高度设置为 100%，使其和父元素（骰子）一样高 */
          position: absolute; /* 使用绝对定位，使容器相对于骰子定位 */
          animation: rotate 20s linear infinite; /* 应用旋转动画，持续时间为 20 秒，线性速度，无限循环 */
          transform-style: preserve-3d; /* 保留 3D 转换，使子元素的 3D 转换相对于容器生效 */
      }

      /* 定义旋转动画关键帧 */
      @keyframes rotate {
          0% { transform: rotateX(0deg) rotateY(0deg); } /* 初始状态：无旋转 */
          100% { transform: rotateX(3600deg) rotateY(7200deg); } /* 结束状态：绕 X 轴旋转 3600 度，绕 Y 轴旋转 7200 度 */
      }

      /* 设置每个骰子面的基本样式 */
      .face {
          width: 100%; /* 面的宽度设置为 100%，使其和父元素（容器）一样宽 */
          height: 100%; /* 面的高度设置为 100%，使其和父元素（容器）一样高 */
          position: absolute; /* 使用绝对定位，使骰子面相对于容器定位 */
          display: flex; /* 使用 flex 布局，使骰子点居中显示 */
          justify-content: center; /* 水平居中对齐 */
          align-items: center; /* 垂直居中对齐 */
          background-color: #eee; /* 骰子面的背景颜色 */
          border: 1px solid #ccc; /* 骰子面的边框 */
          backface-visibility: hidden; /* 隐藏背面，使骰子在旋转时不显示背面 */
      }

      /* 设置骰子点的基本样式 */
      .dot {
          width: 20px; /* 骰子点的宽度 */
          height: 20px;
          /* 骰子点的高度 */
          background-color: black; /* 骰子点的背景颜色 */
          border-radius: 50%; /* 骰子点的圆角，设置为 50% 使其呈现为圆形 */
      }

      .face-1 {
          transform: rotateY(0deg) translateZ(50px);
      }

      .face-2 {
          transform: rotateY(90deg) translateZ(50px);
      }

      .face-3 {
          transform: rotateY(180deg) translateZ(50px);
      }

      .face-4 {
          transform: rotateY(270deg) translateZ(50px);
      }

      .face-5 {
          transform: rotateX(90deg) translateZ(50px);
      }

      .face-6 {
          transform: rotateX(-90deg) translateZ(50px);
      }
  </style>
</head>
<body>
<div class="dice">
  <div class="dice-container">
    <div class="face face-1">
      <div class="dot" style="background-color: red"></div>
    </div>
    <div class="face face-2">
      <div class="dot" style="position:absolute; top: 10px; left: 10px;"></div>
      <div class="dot" style="position:absolute; bottom: 10px; right: 10px;"></div>
    </div>
    <div class="face face-3">
      <div class="dot"></div>
      <div class="dot" style="position:absolute; top: 10px; left: 10px;"></div>
      <div class="dot" style="position:absolute; bottom: 10px; right: 10px;"></div>
    </div>
    <div class="face face-4">
      <div class="dot" style="position:absolute; top: 10px; left: 10px;"></div>
      <div class="dot" style="position:absolute; top: 10px; right: 10px;"></div>
      <div class="dot" style="position:absolute; bottom: 10px; left: 10px;"></div>
      <div class="dot" style="position:absolute; bottom: 10px; right: 10px;"></div>
    </div>
    <div class="face face-5">
      <div class="dot"></div>
      <div class="dot" style="position:absolute; top: 10px; left: 10px;"></div>
      <div class="dot" style="position:absolute; top: 10px; right: 10px;"></div>
      <div class="dot" style="position:absolute; bottom: 10px; left: 10px;"></div>
      <div class="dot" style="position:absolute; bottom: 10px; right: 10px;"></div>
    </div>
    <div class="face face-6">
      <div class="dot" style="position:absolute; top: 10px; left: 10px;"></div>
      <div class="dot" style="position:absolute; top: 10px; right: 10px;"></div>
      <div class="dot" style="position:absolute; bottom: 10px; left: 10px;"></div>
      <div class="dot" style="position:absolute; bottom: 10px; right: 10px;"></div>
      <div class="dot" style="position:absolute; top: 50%; left: 50%; transform: translate(-50%, -50%);"></div>
    </div>
  </div>
</div>
</body>
</html>
```

#### transition过渡

- 通过指定某些元素属性从一种起始状态，在一段时间内以某种变化节奏，过渡到终止状态

```
div {
	transition:<property> <duration> <timing-function> <delay>
}
```

- 其中timing-function一般有三种用法：线性(（linear)、贝塞尔曲线(cubic-bezier()或ease-in等)、阶跃(step)

  - 线性（linear）： 线性 `timing-function` 意味着动画在其整个过程中保持恒定的速度。这意味着在执行动画的每个时间片段中，动画元素的移动速度都相同。要应用线性 `timing-function`，可以使用 `linear` 值：

    ```css
    animation-timing-function: linear;
    ```

  - 贝塞尔曲线（cubic-bezier() 或 ease-in 等）： 贝塞尔曲线 `timing-function` 提供了更为复杂的速度控制。贝塞尔曲线通过一个四阶（cubic）贝塞尔函数定义动画速度的变化。这意味着动画可以在某些时间片段内加速，然后在其他时间片段内减速。CSS 提供了一些预定义的贝塞尔曲线值，如 `ease-in`、`ease-out`、`ease-in-out` 和 `ease`。此外，还可以使用 `cubic-bezier()` 函数自定义贝塞尔曲线：

    ```css
    animation-timing-function: ease-in; /* 预定义值 */
    animation-timing-function: cubic-bezier(0.25, 0.1, 0.25, 1); /* 自定义贝塞尔曲线 */
    ```

  - 阶跃（step）： 阶跃 `timing-function` 用于创建分段的、非平滑动画。这种类型的动画在其执行过程中会跳过一定数量的帧，使动画元素在特定的时间点发生突然的变化。阶跃 `timing-function` 使用 `steps()` 函数定义，其中包含一个整数参数，表示动画的阶段数：

    ```css
    animation-timing-function: steps(4);
    ```

    上述代码将动画分为4个阶段，每个阶段在动画执行过程中都会有一个突然的变化。

    综上所述，`timing-function` 属性通过调整动画速度的变化，为创建复杂、吸引人的动画提供了很大的灵活性。

![image-20230425012948828](.\byte-images\image-20230425012948828.png)

### animation 动画

> 动画库：[Animate.css | A cross-browser library of CSS animations.](https://animate.style/)

![image-20230425020924073](.\byte-images\image-20230425020924073.png)

### transform、transition、animation一性能相关

> [硬件加速详解](https://www.chromium.org/developers/design-documents/gpu-accelerated-compositing-in-chrome/#whither-the-gpu)
>
> [动画性能相关了解深入](https://fed.taobao.org/blog/taofed/do71ct/performance-composite/)

![image-20230425021244134](.\byte-images\image-20230425021244134.png)

## 响应式设计

### 响应式设计推荐遵循的原则

![image-20230425021323005](.\byte-images\image-20230425021323005.png)

### 媒体查询

> 媒体查询（Media Queries）是 CSS3 引入的一种技术，允许某些样式只在页面满足特定条件时才生效。我们可以将媒体类型(如screen、pit)以及媒体特性（如视口宽度、屏幕比例、设备方向：横向或纵向）做为约束条件。
>
> 说白了就是允许你根据设备特征（如屏幕尺寸、分辨率等）对样式进行条件性地应用。这让网页能够根据不同的设备和视口尺寸自适应地调整布局和样式，从而提高用户体验

```css
/*media-type 是指定要应用样式的媒体类型，如 screen、print 等。media-feature-rule 是一个或多个针对特定设备特征的条件表达式，例如 min-width、max-width 等*/
@media media-type and (media-feature-rule) {
  /* 样式规则 */
}
```

#### 案例

> 改变屏幕尺寸时调整字体大小

```css
/* 默认字体大小 */
body {
  font-size: 16px;
}

/* 当屏幕宽度大于等于768像素时，字体大小增加到18px */
@media screen and (min-width: 768px) {
  body {
    font-size: 18px;
  }
}
```

> 针对不同的屏幕尺寸更改布局：

```css
/* 默认布局：单列布局 */
.container {
  width: 100%;
}
.column {
  width: 100%;
  margin-bottom: 20px;
}

/* 当屏幕宽度大于等于600像素时，改为双列布局 */
@media screen and (min-width: 600px) {
  .column {
    width: calc(50% - 20px);
    margin-right: 20px;
    float: left;
  }
}

/* 当屏幕宽度大于等于900像素时，改为三列布局 */
@media screen and (min-width: 900px) {
  .column {
    width: calc(33.333% - 20px);
  }
}

/* 清除浮动 */
.row::after {
  content: "";
  display: table;
  clear: both;
}
```

> 针对高分辨率设备（如 Retina 屏幕）提供高清图像：

```css
/* 默认情况下使用低分辨率图像 */
.logo {
  background-image: url('logo-lowres.png');
}

/* 当设备像素比大于等于2时（例如 Retina 屏幕），使用高分辨率图像 */
@media screen and (min-resolution: 2dppx) {
  .logo {
    background-image: url('logo-highres.png');
  }
}
```

- **使用媒体查询的一些Tips**
  1. 媒体查询同样遵循cascading层叠覆盖原则，min-和max选择一个
  2. 由于设备的多样化逐渐不可枚举，断点的选择尽量根据内容选择
  3. 由于断点的增加会增加样式处理的复杂度，所以尽量减少断点

> #### cascading层叠覆盖原则
>
> Cascading 层叠是 CSS (Cascading Style Sheets) 中的一个核心概念。层叠规则定义了当一个 HTML 元素的样式从多个来源（如内联样式、外部样式表、浏览器默认样式等）发生冲突时，如何确定哪个样式应该优先应用。
>
> 层叠规则主要遵循以下原则：
>
> 1. 重要性（Importance）：`!important` 标记的样式规则具有更高的优先级，会覆盖其他没有标记 `!important` 的样式规则。如果有多个 `!important` 规则冲突，将按照下面的规则进行判断。
> 2. 选择器优先级（Specificity）：选择器优先级是根据选择器的复杂程度计算出的一个权重值。具有更高优先级的选择器定义的样式将覆盖具有较低优先级的选择器定义的样式。优先级的计算规则如下：
>    - 内联样式（style 属性）优先级最高，计为 1000。
>    - 每个 ID 选择器（如 `#example`）计为 100。
>    - 每个类选择器（如 `.example`）、属性选择器（如 `[type="text"]`）或伪类（如 `:hover`）计为 10。
>    - 每个元素选择器（如 `h1`）或伪元素（如 `::before`）计为 1。
>    - 通配符选择器（`*`）、关系选择器（如 `+`, `>`）和否定伪类（如 `:not()`）不增加优先级。
>    - 如果优先级相同，则遵循下面的源顺序规则。
> 3. 源顺序（Source order）：在样式表或文档中后定义的样式规则会覆盖先前定义的相同优先级的样式规则。换句话说，越靠后的规则会覆盖越靠前的规则，前提是它们具有相同的优先级。

<img src=".\byte-images\image-20230425022206607.png" style="zoom:50%;" />

### 设备像素、参考像素和移动设备视口

#### 设备像素

![image-20230425022620908](.\byte-images\image-20230425022620908.png)

#### DPI && PPI

- dpi(dots per inch)：每英寸多少点。
- ppi((pixels per inch)：每英寸多少像素数

> 当用于描述显示器设备时ppi与dpi是同一个概念，说的是每英寸多少物理像素及显示器设备的点距

<img src=".\byte-images\image-20230425022737252.png" alt="image-20230425022737252" style="zoom:50%;" />

- 直观对比：

<img src=".\byte-images\image-20230425022754694.png" alt="image-20230425022754694" style="zoom:50%;" />

#### CSS像素

> CSS 像素（CSS pixel）是一个抽象的单位，用于在网页设计中表示屏幕上的可见元素的尺寸。它不同于物理像素，物理像素是显示器或其他输出设备用来显示内容的实际点。CSS 像素与设备像素比例（device pixel ratio，DPR）有关，DPR 是物理像素与 CSS 像素之间的比例。
>
> 在许多情况下，一个 CSS 像素可能等于一个物理像素。然而，在高分辨率屏幕（例如 Retina 屏幕）上，一个 CSS 像素可能对应多个物理像素。这是因为高分辨率屏幕具有更高的像素密度，即更多的物理像素被压缩到相同的显示区域。为了保持页面元素在不同屏幕上的可读性和布局一致性，浏览器会自动调整 CSS 像素与物理像素之间的映射。
>
> 设备像素比（DPR）是衡量设备物理像素与 CSS 像素之间关系的关键因素。例如，具有 2x 的 DPR 的设备上，一个 CSS 像素实际上占据了 2x2 = 4 个物理像素。在具有 3x 的 DPR 的设备上，一个 CSS 像素占据了 3x3 = 9 个物理像素。
>
> 要确定设备的 DPR，可以使用 JavaScript 的 `window.devicePixelRatio` 属性。为了更好地适应高分辨率屏幕，可以使用媒体查询和响应式图片技术来提供针对不同 DPR 的优化图像。

![image-20230425022908555](.\byte-images\image-20230425022908555.png)

#### DPR设备像素比

> 设备像素比（Device Pixel Ratio，简称 DPR）是一个与屏幕分辨率相关的概念。它表示一个 CSS 像素与设备物理像素之间的比例。简单地说，DPR 描述了一个 CSS 像素占据了多少个设备物理像素。
>
> 在普通屏幕上（例如普通显示器和早期的移动设备屏幕），DPR 通常为 1，这意味着一个 CSS 像素对应一个物理像素。然而，在高分辨率屏幕上（例如 Retina 显示屏或其他高 DPI 屏幕），DPR 的值可能大于 1，这意味着一个 CSS 像素占据了多个物理像素。这样的设计使得在高分辨率屏幕上显示的内容更加清晰和细腻。
>
> 例如，如果设备的 DPR 为 2，那么一个 CSS 像素实际上占据了 2x2 = 4 个物理像素。如果设备的 DPR 为 3，那么一个 CSS 像素占据了 3x3 = 9 个物理像素。
>
> 要确定设备的 DPR，可以使用 JavaScript 的 `window.devicePixelRatio` 属性。为了更好地适应不同 DPR 的设备，可以使用媒体查询和响应式图片技术提供针对不同 DPR 的优化图像。
>
> 当设计网页时，了解 DPR 的概念对于确保在不同分辨率和像素密度的屏幕上实现清晰、一致的用户体验至关重要。通过为高 DPR 设备提供高分辨率的图像和优化的布局，可以确保网页内容在各种设备上都能正常显示。

![image-20230425023102186](.\byte-images\image-20230425023102186.png)

#### 移动端的viewport

> 在移动端，viewport 是一个重要的概念，它用于定义浏览器窗口中可见区域的大小和缩放。
>
> 移动设备的屏幕尺寸和分辨率通常比桌面设备小得多。默认情况下，许多移动浏览器将尝试将整个网页缩放到屏幕的宽度以适应屏幕尺寸，这可能导致文本和元素变得过小，难以阅读和操作。为了解决这个问题，可以使用 viewport 元标签来控制浏览器如何显示页面内容。
>
> 要在网页中设置 viewport，可以在 HTML 文档的 `<head>` 部分添加一个 `<meta>` 标签，如下所示：
>
> ```html
> <meta name="viewport" content="width=device-width, initial-scale=1">
> ```
>
> 这段代码做了两件事：
>
> 1. `width=device-width`：将 viewport 的宽度设置为设备的物理屏幕宽度。这意味着网页的宽度将根据设备的屏幕宽度进行调整，使内容在不同尺寸的设备上自适应。
> 2. `initial-scale=1`：设置页面的初始缩放比例为 1，也就是不缩放。这可以确保在加载时，页面内容会以 100% 的大小显示。
>
> 除了 `width` 和 `initial-scale` 之外，还可以设置其他一些 viewport 属性，例如：
>
> - `minimum-scale`：定义页面的最小缩放比例。
> - `maximum-scale`：定义页面的最大缩放比例。
> - `user-scalable`：指定用户是否可以手动缩放页面（设置为 `yes` 或 `no`）。
>
> 例如，如果要限制用户的缩放能力，可以使用以下代码：
>
> ```html
> <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
> ```

![image-20230425023542196](.\byte-images\image-20230425023542196.png)

##### viewport的meta标签中的属性

1. `width`：设置 viewport 的宽度。通常，将宽度设置为 `device-width`，这意味着 viewport 的宽度等于设备的物理屏幕宽度。这有助于确保页面在不同尺寸的设备上自适应。

```html
<meta name="viewport" content="width=device-width">
```

2. `height`：设置 viewport 的高度。与 `width` 类似，可以使用 `device-height` 作为值。然而，实际使用中，高度属性很少用到，因为大多数情况下，只需关注宽度即可。

```html
<meta name="viewport" content="height=device-height">
```

3. `initial-scale`：设置页面的初始缩放比例。1 表示不缩放，即页面以 100% 的大小显示。设置适当的初始缩放可以确保页面在加载时以合适的大小呈现

```html
<meta name="viewport" content="initial-scale=1">
```

4. `minimum-scale`：定义页面的最小缩放比例。例如，设置为 0.5 表示用户可以将页面缩小到 50% 的大小。

```html
<meta name="viewport" content="minimum-scale=0.5">
```

5. `maximum-scale`：定义页面的最大缩放比例。例如，设置为 2 表示用户可以将页面放大到 200% 的大小

```html
<meta name="viewport" content="maximum-scale=2">
```

6. `user-scalable`：指定用户是否可以手动缩放页面。设置为 `yes` 允许缩放，设置为 `no` 禁止缩放。通常，允许用户缩放页面以适应他们的需求是一个好的做法，但在某些情况下，你可能希望禁止缩放。

```html
<meta name="viewport" content="user-scalable=no">
```

- 通常，将多个属性组合使用以实现最佳效果，例如：

```html
<meta name="viewport" content="width=device-width, initial-scale=1, minimum-scale=1, maximum-scale=2, user-scalable=yes">
```

这个示例设置了 viewport 的宽度、初始缩放比例、最小和最大缩放比例，并允许用户手动缩放页面。

![image-20230425024609859](.\byte-images\image-20230425024609859.png)

### 相对长度的使用

#### em

> `em` 是一种相对长度单位，它的大小是基于其最近的父元素的字体大小。在 CSS 中，`em` 常用于设置字体大小、行高、边距和填充等属性。使用 `em` 可以帮助你实现可伸缩和响应式的设计，因为它们会根据父元素的字体大小自动调整。

![image-20230425024801084](.\byte-images\image-20230425024801084.png)

1. 字体大小

使用 `em` 设置字体大小可以实现可伸缩的文本。当你改变父元素的字体大小时，子元素的字体大小也会相应地调整。

```css
.parent {
    font-size: 16px;
}

.child {
    font-size: 1.5em; /* 1.5 x 16px = 24px */
}
```

2. 行高

使用 `em` 设置行高可以根据文本大小自动调整行间距，从而保持良好的可读性。

```css
p {
    font-size: 18px;
    line-height: 1.5em; /* 1.5 x 18px = 27px */
}
```

3. 边距和填充

使用 `em` 设置边距和填充可以实现与文本大小成比例的间距。这在保持元素间的视觉平衡时非常有用。

```css
/*按钮的内部填充和外部边距与其字体大小成比例*/
.button {
    font-size: 16px;
    padding: 0.5em 1em; /* 0.5 x 16px = 8px (top and bottom), 1 x 16px = 16px (left and right) */
    margin-top: 1em; /* 1 x 16px = 16px */
}
```

4. 媒体查询

在响应式设计中，你可能希望在不同的屏幕尺寸下调整元素的大小。通过在媒体查询中使用 `em`，你可以根据父元素的字体大小来调整元素。

```css
/*当屏幕宽度小于或等于 40em（640px）时，容器的字体大小会减小到 14px，同时，容器的宽度也会相应地减小（60 x 14px = 840px），从而实现响应式设计*/
.container {
    font-size: 16px;
    width: 60em; /* 60 x 16px = 960px */
}

@media (max-width: 40em) {
    .container {
        font-size: 14px;
        /* width will now be 60 x 14px = 840px */
    }
}
```

5. 嵌套元素

当你在嵌套元素中使用 `em` 时，它们的大小将根据其父元素的字体大小进行调整。这使得组件和模块可以在不同的上下文中自动调整大小。

```css
/*在这个例子中，父元素的字体大小为 16px，子元素的字体大小为 20px（1.25 x 16px），孙子元素的字体大小则恢复为 16px（0.8 x 20px）*/
.parent {
    font-size: 16px;
}

.child {
    font-size: 1.25em; /* 1.25 x 16px = 20px */
}

.grandchild {
    font-size: 0.8em; /* 0.8 x 20px = 16px */
}
```

#### rem

> `rem` 是一种相对单位，表示相对于根元素（即 HTML 元素）的字体大小。与 `em` 不同，`rem` 不会受嵌套元素字体大小的影响，因为它直接依赖于根元素的字体大小。这降低了复杂性并使 `rem` 更易于控制和预测。作为相对单位，`rem` 可以适应不同屏幕尺寸，因此在创建响应式设计和组件时尤为有用。

![image-20230425025353793](.\byte-images\image-20230425025353793.png)

1. 全局字体大小

   通常，我们会在根元素（`<html>`）上设置一个基本的字体大小

```css
/*这意味着，1rem 等于 16px。接下来，我们可以使用 rem 单位为其他元素设置字体大小、边距、宽度等属性*/
html {
    font-size: 16px;
}
```

2. 响应式字体大小

   与 `em` 类似，我们可以使用媒体查询在不同屏幕尺寸下调整根元素的字体大小。

```css
/*当屏幕宽度小于或等于 768px 时，根元素的字体大小会减小到 14px。因此，所有使用 rem 单位的元素也会相应地缩小*/
html {
    font-size: 16px;
}

@media (max-width: 768px) {
    html {
        font-size: 14px;
    }
}
```

3. 组件和模块

   使用 `rem` 单位可以帮助我们创建可复用、可伸缩的组件和模块。

```css
/*容器的字体大小、边距和宽度都是基于根元素的字体大小。这意味着，当根元素的字体大小发生变化时，这些值也会自动调整*/
.container {
    font-size: 1.125rem; /* 1.125 x 16px = 18px */
    padding: 1rem; /* 16px */
    width: 60rem; /* 60 x 16px = 960px */
}
```

4. 避免字体大小累积

   与 `em` 不同，`rem` 不受父元素字体大小的影响。这意味着，我们可以避免字体大小累积的问题

```css
/*子元素的字体大小始终为 14px，而不受父元素字体大小的影响*/
.parent {
    font-size: 1.25rem; /* 1.25 x 16px = 20px */
}

.child {
    font-size: 0.875rem; /* 0.875 x 16px = 14px */
}
```

#### vw和vh

> `vw`（viewport width）和 `vh`（viewport height）是两种相对长度单位，用于表示相对于视口宽度和高度的尺寸。这两个单位主要用于响应式设计，以适应不同屏幕尺寸和设备。
>
> 1. `vw`：viewport width（视口宽度）单位。1vw 等于视口宽度的 1%。这意味着，如果浏览器窗口的宽度为 1200px，那么 1vw 就等于 12px（1200 * 0.01）。随着浏览器窗口宽度的变化，`vw` 单位的实际像素值也会相应变化。
> 2. `vh`：viewport height（视口高度）单位。1vh 等于视口高度的 1%。这意味着，如果浏览器窗口的高度为 900px，那么 1vh 就等于 9px（900 * 0.01）。与 `vw` 一样，随着浏览器窗口高度的变化，`vh` 单位的实际像素值也会相应变化。
>
> 这两个单位的主要优点是它们可以根据视口的大小自动调整，从而使设计在不同尺寸的屏幕上保持一致。这在响应式布局中非常有用，尤其是当你需要根据设备或浏览器窗口大小调整元素尺寸时。例如，你可以使用 `vw` 和 `vh` 单位来设置字体大小、边距、宽度和高度等。
>
> 同样，vw也可以作为响应式布局的基准单位。由于vw天然是视口宽度的1%，所以不需要js动态配置。

![image-20230425030003434](.\byte-images\image-20230425030003434.png)

```css
/* 设置字体大小为视口宽度的 4% */
p {
  font-size: 4vw;
}

/* 设置一个元素的高度为视口高度的 50% */
.container {
  height: 50vh;
}

/* 设置一个元素的宽度为视口宽度的 80% */
.box {
  width: 80vw;
}
```

- 需要注意的是，在某些情况下，`vw` 和 `vh` 可能导致内容溢出或不可见。因此，在使用这些单位时，需要确保充分测试并为溢出内容提供适当的处理

## CSS生态相关

- **语言增强：预处理器、后处理器**
- **工程架构：CSS模块化、CSS-ln-Js、Atomic CSS**

### 语言增强 -- CSS预处理器

> 

![image-20230425030257978](.\byte-images\image-20230425030257978.png)

#### 预处理器如何提高研发效率？

![image-20230425030545455](.\byte-images\image-20230425030545455.png)

#### scss、less、stylus简单对比

> Dart Sass 的源代码、文档和示例：[sass/dart-sass: The reference implementation of Sass, written in Dart. (github.com)](https://github.com/sass/dart-sass)
>
> Sass 的源代码、文档和示例：[sass/sass: Sass makes CSS fun! (github.com)](https://github.com/sass/sass)
>
> Less 的源代码：[less/less.js: Less. The dynamic stylesheet language. (github.com)](https://github.com/less/less.js)
>
> stylus 的源代码：[stylus/stylus: Expressive, robust, feature-rich CSS language built for nodejs (github.com)](https://github.com/stylus/stylus)

|                    | scss                        | less                                  | stylus           |
| ------------------ | --------------------------- | ------------------------------------- | ---------------- |
| css语法兼容        | 兼容                        | 兼容                                  | 常规兼容         |
| 可编程能力         | 较强：逻辑处理能力丰富      | 较弱：不支持自定义函数                | 较强             |
| 社区活跃、使用人数 | 最多star 3.5k；burbon库支持 | 较多star 16.9k；twitter bootstrap框架 | 相对少 star 11k; |

##### 代码演示

> 1. Sass (SCSS 语法)：展示了 Sass 的变量、函数（darken）、mixin 和 @include 指令

```scss
@mixin border-radius($radius) {
  -webkit-border-radius: $radius;
  -moz-border-radius: $radius;
  -ms-border-radius: $radius;
  border-radius: $radius;
}

.button {
  background-color: darken($blue, 10%);
  padding: 10px 20px;
  color: white;
  @include border-radius(5px);
}
```

> 2. Less：展示了 Less 的变量、函数（darken）、mixin 和 mixin 调用

```less
.mixin(@border-radius) {
  -webkit-border-radius: @border-radius;
  -moz-border-radius: @border-radius;
  -ms-border-radius: @border-radius;
  border-radius: @border-radius;
}

.button {
  @base-color: blue;
  background-color: darken(@base-color, 10%);
  padding: 10px 20px;
  color: white;
  .mixin(5px);
}
```

> 3. Stylus：展示了 Stylus 的变量、函数（darken）、mixin 和 mixin 调用。注意 Stylus 的语法比其他两者更加简洁

```stylus
border-radius(radius)
  -webkit-border-radius radius
  -moz-border-radius radius
  -ms-border-radius radius
  border-radius radius

.button
  base-color = blue
  background-color darken(base-color, 10%)
  padding 10px 20px
  color white
  border-radius(5px)
```

### 语言增强 -- 广义CSS预处理器

- **现在项目中常见的样式处理流程**

![image-20230425032213644](.\byte-images\image-20230425032213644.png)

### 语言增强 -- CSS后处理器

> Plugins：[PostCSS Plugins](https://postcss.org/docs/postcss-plugins)
>
> CSS 后处理器是一种工具，它在 CSS 预处理器处理生成的 CSS 代码之后运行。其主要目的是优化和改进最终的 CSS 输出，以提高性能、浏览器兼容性和代码可维护性。
>
> CSS 后处理器使用 JavaScript 插件（或其他编程语言编写的工具）来处理 CSS，这些插件可以解决不同的问题或执行特定任务。常见的 CSS 后处理器任务包括：
>
> 1. 自动添加浏览器前缀（autoprefixer）：根据目标浏览器和其版本，自动添加适当的浏览器前缀以确保跨浏览器兼容性。
> 2. CSS 压缩（minification）：删除 CSS 代码中的空格、换行符和注释，以减少文件大小，提高加载速度。
> 3. 优化和合并规则（optimization）：查找和合并重复的 CSS 规则，重写代码以减少不必要的选择器和规则，从而减少文件大小和提高性能。
> 4. 使用 CSS 变量（CSS custom properties）：将 CSS 预处理器中使用的变量转换为原生的 CSS 变量，从而在运行时更改样式。
> 5. 实现未来 CSS 规范（future-proofing）：将尚未得到广泛支持的新 CSS 规范转换为目前可用的兼容语法，以便在不等待浏览器支持的情况下使用新功能。
>
> PostCSS 是目前最流行的 CSS 后处理器。它提供了一个插件系统，使开发人员能够根据需要选择和配置插件。PostCSS 可以与构建工具（如 Webpack、Gulp 和 Grunt）结合使用，将后处理集成到自动化构建和部署流程中。

![image-20230425032520297](.\byte-images\image-20230425032520297.png)

### 语言增强 -- postcss机制浅析

> Astexplorer：[AST explorer](https://astexplorer.net/)是一个在线工具，用于可视化各种编程语言的抽象语法树（AST）。它可以帮助开发者更好地理解代码在解析过程中是如何被表示的。AST 是一种树形数据结构，用于表示源代码中的结构和语法关系。
>
> 在 Astexplorer 中，可以输入源代码，选择相应的解析器，然后查看生成的 AST。这个工具支持多种编程语言和相应的解析器，如 JavaScript、TypeScript、HTML、CSS、JSON 等。除了显示 AST，Astexplorer 还允许我们通过点击树形结构中的节点来高亮源代码中的对应部分，反之亦然。
>
> Astexplorer 对于编译器和解析器的开发、编写代码转换工具（如 Babel 插件）以及深入了解编程语言的语法和结构非常有用。通过 Astexplorer，开发者可以更直观地理解源代码在解析和转换过程中是如何被操作和处理的。
>
> - PostCSS 的工作机制可以分为以下几个步骤：
>
> 1. 解析：PostCSS 使用 CSS 解析器将 CSS 代码解析成一个 Abstract Syntax Tree（AST，抽象语法树）。AST 是一种树形数据结构，它表示了 CSS 代码的结构和层次关系，使得插件可以更容易地遍历和操作 CSS 规则。
> 2. 插件：PostCSS 的插件系统是其核心功能。插件是 JavaScript 函数，它们在 PostCSS 生成 AST 后，对 AST 进行操作和修改。插件可以独立使用，也可以组合成一个管道（pipeline），按照特定的顺序执行多个插件。这使得 PostCSS 非常灵活，开发者可以根据项目需求选择和配置插件。
> 3. 转换：在插件处理 AST 后，PostCSS 将修改后的 AST 转换回 CSS 代码。这个过程中，PostCSS 会保留原始代码中的格式、缩进和注释，以便生成的 CSS 代码具有良好的可读性。
> 4. 集成：PostCSS 可以与各种构建工具（如 Webpack、Gulp 和 Grunt）集成，以便将后处理过程集成到自动化构建和部署流程中。这种集成可以简化开发流程，确保生成的 CSS 代码经过优化且兼容目标浏览器。

![image-20230425032633381](.\byte-images\image-20230425032633381.png)

### 工程架构 -- CSS模块化

> [css-modules/css-modules: Documentation about css-modules (github.com)](https://github.com/css-modules/css-modules)
>
> CSS Module 就是为了解决全局污染问题出现的方案，解决CSS全局污染，本质上是保证样式集合对应的选择器是唯一的，从这个角度看，主流的单纯针对于防止全局污染的方案大概有以下三种：

![image-20230425033159189](.\byte-images\image-20230425033159189.png)

### 工程架构 -- CSS-in-JS

> cij的playground(下面那张图的演示地址)：[CSS in JS Playground](https://www.cssinjsplayground.com/?activeModule=index&library=styled-components)
>
> CSS-in-JS（JavaScript 中的 CSS）是一种现代的前端开发模式，它允许开发者直接在 JavaScript 代码中编写 CSS 样式，利用js动态生成css，这种在React中就是一个大杀器。CSS-in-JS 的目的是解决组件化和模块化的开发过程中遇到的一些 CSS 问题，例如全局作用域、命名冲突、依赖管理和代码重用等。
>
> 在 CSS-in-JS 中，样式被当作 JavaScript 对象来处理，这样可以使用 JavaScript 的所有特性（例如变量、函数、条件语句等）来创建动态样式。为组件创建的样式会在运行时注入到 DOM 中，这样就可以实现局部作用域，避免样式污染和命名冲突。
>
> CSS-in-JS 的优点：
>
> 1. 局部作用域：样式仅应用于特定的组件，避免了全局样式污染和命名冲突。
> 2. 代码组织和重用：将样式与相应的组件绑定，使组件更易于维护和重用。
> 3. 动态样式：可以使用 JavaScript 的能力来根据状态、属性和逻辑创建动态样式。
> 4. 依赖管理：通过 JavaScript 模块系统管理样式依赖，简化了构建和打包过程。
> 5. 主题和样式共享：通过 JavaScript 变量和对象轻松共享主题和样式。
>
> 一些流行的 CSS-in-JS 库包括：
>
> 1. styled-components：使用 ES6 标签模板字符串创建样式化的组件。
> 2. emotion：具有高性能的 CSS-in-JS 库，提供了多种样式定义方式。
> 3. JSS：灵活的、独立的 CSS-in-JS 库，支持插件和样式复用。
>
> 需要注意的是，CSS-in-JS 并非适用于所有场景，也有一些潜在的缺点，如性能开销、额外的学习曲线、以及与传统 CSS 的不兼容。因此，在选择 CSS-in-JS 时，需要根据项目需求和团队技能进行权衡

![image-20230425033352484](.\byte-images\image-20230425033352484.png)

### 工程架构  -- styled.component 机制浅析

> - `styled-components` 的工作机制的解释：
>
> 1. 创建样式化组件：`styled-components` 使用了 ES6 的标签模板字符串功能，它允许你在 JavaScript 中直接编写 CSS 代码。首先，你需要导入 `styled` 对象，然后使用它来创建带有样式的组件。
>
> ```js
> import styled from 'styled-components';
> 
> const StyledButton = styled.button`
>   background-color: blue;
>   color: white;
>   border: none;
>   padding: 10px;
>   cursor: pointer;
>   
>   &:hover {
>     background-color: darkblue;
>   }
> `;
> ```
>
> 1. 生成唯一的类名：`styled-components` 为每个创建的样式化组件生成一个唯一的类名。这可以确保样式不会与其他组件冲突，并实现局部作用域。
> 2. 动态属性和主题：`styled-components` 支持通过插值将动态属性和主题值注入到样式中。这允许你根据组件的属性或应用程序的主题来调整样式
> 3. 样式注入：当你在应用程序中使用样式化组件时，`styled-components` 会将生成的样式注入到 DOM 中。通常情况下，这是通过在页面中添加一个 `style` 标签来实现的，该标签包含所有组件的样式
>
> ```javascript
> const StyledDiv = styled.div`
>   background-color: ${props => props.backgroundColor || 'white'};
>   color: ${props => props.theme.primaryColor};
> `;
> ```
>
> 4. 样式注入：当你在应用程序中使用样式化组件时，`styled-components` 会将生成的样式注入到 DOM 中。通常情况下，这是通过在页面中添加一个 `style` 标签来实现的，该标签包含所有组件的样式
>
> ```javascript
> import React from 'react';
> import { StyledButton } from './StyledButton';
> 
> const App = () => {
>   return (
>     <div>
>       <StyledButton>Click me</StyledButton>
>     </div>
>   );
> };
> 
> export default App;
> ```
>
> 5. 服务器端渲染和样式提取：`styled-components` 支持服务器端渲染，可以在服务器上生成静态 HTML 和 CSS。这有助于提高页面加载速度和搜索引擎优化。此外，`styled-components` 还提供了一个 babel 插件来提取静态样式，从而进一步优化构建结果

![image-20230425033925793](.\byte-images\image-20230425033925793.png)

### 工程架构 -- CSS-in-JS pros & cons

![image-20230425034238007](.\byte-images\image-20230425034238007.png)

### 工程架构 -- 原子化CSS

> 可以去看B站UP主`小满zs`的Vue3+TypeScript中的第51集学习
>
> 传送门：[小满Vue3（第三十七章 unoCss原子化）_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1dS4y1y7vd?p=51&vd_source=ac59d93abc80d658f5a8a76f8cf70159)
>
> CSS 原子化的优缺点
>
> 1. 减少了 css 体积，提高了 css 复用
>
> 2. 减少起名的复杂度
>
> 3. 增加了记忆成本 将 css 拆分为原子之后，你势必要记住一些 class 才能书写，哪怕 tailwindcss 提供了完善的工具链，你写 background，也要记住开头是 bg

![image-20230425034339300](.\byte-images\image-20230425034339300.png)

#### Tailwind 如何做的？

![image-20230425035311877](.\byte-images\image-20230425035311877.png)

#### 原子化的优劣图示

> Facebook重构拥抱atomic css：[Atomic CSS-in-JS (sebastienlorber.com)](https://sebastienlorber.com/atomic-css-in-js)

![image-20230425035411113](.\byte-images\image-20230425035411113.png)

# 课程总结

![image-20230425043340718](.\byte-images\image-20230425043340718.png)