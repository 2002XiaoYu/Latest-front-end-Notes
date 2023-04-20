# 数据结构第二周笔记——线性结构(慕课浙大版本--XiaoYu)

> 笔记出处：GitHub地址：[2002XiaoYu (小余) (github.com)](https://github.com/2002XiaoYu)，作者：小余
>
> 笔记持续更新中，有需要的自行获取，提供markdown跟PDF两种格式，在线浏览如果受到不可控影响无法登录GitHub可以查看我掘金账号的专栏对应的文章(目前在陆续上传中)
>
> 如果对你有所帮助，给作者一个star就是最好的鼓励，作者会持续产出的

### 2.1.1 引子：多项式表示

#### 例子：一元多项式及其运算

<img src=".\数据结构-image\image-20220627134344443.png" alt="image-20220627134344443" style="zoom:50%;" />

主要运算：多项式相加、相减、相乘等

**分析**--**如何表示多项式**

多项式的关键数据：

1. 多项式项数n

2. 各项系数ai以及指数i(这里的ai的i是指a的下标)

   **方法1**：顺序存储结构直接表示

   <img src=".\数据结构-image\image-20220627134953388.png" alt="image-20220627134953388" style="zoom:50%;" />

注解：其实这个的意思就是从头加到尾，不管中间的答案有没有像0这种可以直接省略掉的值都会占一个位置。

所以上方那个问题中如何表示多项式的答案是：需要占2001个位置，首选他是顺序存储结构，然后下标从0开始，所以是2001而不是2000

**方法2**：顺序存储结构表示非零项(按照指数大小有序存储，比如说指数大的排在前面，指数小的排在后面)

<img src=".\数据结构-image\image-20220627140021075.png" alt="image-20220627140021075" style="zoom:50%;" />

多项式相加过程：从头开始，比较两个多项式当前对应项的的指数  =>这样就能够做到指数递降运算

<img src=".\数据结构-image\image-20220627140719716.png" alt="image-20220627140719716" style="zoom:50%;" />

这里的(11，8)是(15，8)与(-4，8)的结合结果，因为指数一样，所以可以结合为一个

这种方法可以有效节省空间，操作效率也不算差

**方法3**：链表结构存储非零项

1. 链表中每个节点存储多项式中的一个非零项，包括系数和指数两个数据域以及一个指针域
2. coef：指数  expon：系数  link：指针域
3. 用指针域将不同的项串起来，同样可以做到指数递降的顺序进行排序

```c
//代码演示
typedef struct PolyNode*Polynomial;
struct PolyNode{
    int coef;
    int expon;
    Polynomial link;
}
```

<img src=".\数据结构-image\image-20220627141534773.png" alt="image-20220627141534773" style="zoom:50%;" />

<img src=".\数据结构-image\image-20220627141555193.png" alt="image-20220627141555193" style="zoom:50%;" />

分别指向多项式的头，然后比较指数大小，大的输出；相等的话，系数相加

### 2.1.2 线性表及顺序存储

**什么是线性表**:由同类型数据元素构成有序列表的线性结构

1. 表中元素个数称为线性表的长度
2. 线性表没有元素的时候，称为空表
3. 表起始位置称表头，表结束位置称表尾

多项式表示问题的启示：

1. 同一个问题可以有不同的表示(存储)方法  =>通常来说的话是使用链表或者数组来进行存储
2. 有一类共性问题：有序线性序列的组织和管理

**线性表的抽象数据类型描述**

类型名称：线性表(List)

数据对象集：线性表是n(>=0)个元素构成的有序列表(a1,a2,...,an)

操作集：线性表L属于List，整数i表示位置，元素X属于ElementType(这个类型可以是整型也可以是实型又或者是个结构，这里统称ElementType)

1. List MakeEmpty()：初始化一个空线性表L
2. ElementType FindKth(int K,ListL)：根据位序K，返回相应元素;
3. int Find(ElementType X,int i, List L)：在线性表L中查找X的第一次出现位置;
4. void Insert(ElementType X,int i,List L)：在位序i前插入一个新元素X;
5. void Delete(int i,List L)：删除指定位序i的元素;
6. int Length(List L)：返回线性表L的长度n

#### 线性表的顺序存储实现

利用数组的连续存储空间顺序存放线性表的各元素

```c
typedef struct LNode*List;
struct LNode{
    ElementType Data [MAXSIZE];//定义了一个数组，数组的分类类型是ElementType
    int List;//代表线性表的最后一个元素，这样的一个结构就可以抽象的实现一个线性表
};
struct LNode L;//定义一个变量L
List PtrL;//还有一个变量PtrL
	//访问下标为i的元素：L.Data[i]或PtrL->Data[i]
	//线性表的长度：L.Last+1或PtrL->Last+1
```

<img src=".\数据结构-image\image-20220627191654197.png" alt="image-20220627191654197" style="zoom:50%;" />

“->”是一个整体，它是用于指向[结构体]子数据的指针，用来取子数据。

换种说法，如果我们在C语言中定义了一个结构体，然后申明一个指针指向这个结构体，那么我们要用指针取出结构体中的数据，就要用到“->”。

**主要操作的实现**

1. 初始化MakeEmpty(建立空的顺序表)

   ```c
   List MakeEmpty()
   {
   	List PtrL;
   	PtrL = (List)malloc(sizeof(struct LNode));//通过malloc申请这样子一个结构
   	PtrL->Last = -1;//Last设置为-1，因为Last是代表最后一个元素。Last为0是代表这个表有一个元素放在第一个位置，没元素就设置为-1，然后把这个结构的指针返还回来
   	return PtrL;
   }
   ```

2. 查找

   ```c
   //科普小知识
   find函数用于查找数组中的某一个指定元素的位置。
   
   比如：有一个数组[0, 0, 5, 4, 4]；
   问：元素5的在什么位置，find函数 返回值 为 2；
   
   find （数组名 + 起始查找元素的位置， 数组名 + 结束查找的元素位置， 想要查找的元素）
   
   int Find(ElementType X, List PtrL)//List PtrL是线性表结构的指针
   {
       int i = 0;
       while(i <= PtrL ->Last && PtrL->Data[i]! = X )
           i++
       if(i > PtrL->Last)  return -1;//如果没找到，返回-1
       else return i;//找到后返回的是存储位置
   }
   查找成功的平均比较次数为(n+1)/2,平均时间性能为O(n)
   ```

   ### 2.1.3顺序存储的插入和删除

   插入(第i(1<=i<=n+1)个位置上插入一个值为X的新元素)   其实就是决定什么时候插入线性表

1. 下标是从0开始的，所以新插入的元素X放到i-1这个位置的话，首先需要把i-1之后的元素往后挪一位给i-1留出位置

2. 也就是先移动再插入，每个元素往后挪使用一个循环就可以解决了

3. <img src=".\数据结构-image\image-20220627200204195.png" alt="image-20220627200204195" style="zoom:50%;" />

4. 从后往前算(挪)，如果从前往后的话算法是不对的

   ```c
   //插入操作实现
   void Insert(ElementType X,int i,List PtrL)
   {
       int j;
       if(PtrL->Last == MAXSIZE-1){//表空间已满，不能插入
           printf("表满");
           return;
       }
       if(i < 1 || i > PtrL -> Last + 2){//检查插入的位置的合法性。超出这个范围就不行噢
           printf("位置不合法");
           return;
       }
       for(j = PtrL->Last; j>= i-1;j--)//List是最后一个元素的位置
           PtrL->Data[j+1] = PtrL->Data[j];//将a1~an倒序向后移动
       PtrL->Data[i-1] = X;//新元素插入
       PtrL->Last++;//Last仍指向最后元素
       return;
   }
   //平均移动次数为n/2，平均时间性能为O(n)
   ```

   ```c
   
   接下来我们来介绍一下另外一个操作：删除
   
   
   如果把后移数组元素的循环
   
      for ( j = PtrL->Last; j >= i-1; j-- )
   
          PtrL->Data[j+1]=PtrL->Data[j];
   
   改为
   
      for ( j = i-1; j <= PtrL->Last; j++ )
   
          PtrL->Data[j+1]=PtrL->Data[j];
   
   那会是什么后果？
       
   分量Data[i-1]到Data[Ptrl->Last+1]都是同一个值，即移之前Data[i-1]的值
   ```

   **删除(删除表中的第i(1 <= i <= n)个位置上的元素)**

   ```c
   //删除掉这个元素后就空出来一个位置了，这种时候就需要从左往右的顺序往前挪
   //删除操作实现
   void Detele(int i,List PtrL)//已知PtrL这个线性表
   {
       int j;
       if( i < 1 || i > PtrL->Last+1){
           printf("不存在第%d个元素",i);
           return;
       }
       for(j = i;j <= PtrL->Last;j++)
           PtrL->Data[j-1] = PtrL->Data[j];//将a的下标(i+1)~an顺序往前移动
       PtrL->Last--;//Last仍指向最后元素
       return;
   }
   //平均移动次数为(n-1)/2,平均时间性能为O(n)
   ```

   

### 2.1.4 链式存储及查找

#### **线性表的链式存储实现**

1. **不要求逻辑上相邻的两个元素物理上也相邻**;通过"链"建立起数据元素之间的逻辑关系
   1. 插入、删除不需要移动数据元素，只需要修改"链"

```c
typedef struct LNode *List;
struct LNode{
    ElementType Data;
    List Next;
};
struct Lnode L;
List PtrL;
```

<img src=".\数据结构-image\image-20220627234839331.png" alt="image-20220627234839331" style="zoom:50%;" />

**主要操作的实现**

1. 求表长

   ```c
   int Length(List PtrL)//链表的头指针，并且是单向链表
   {
       List p = PtrL;//p指向表的第一个结点
       int j = 0;
       while(p){
           p = p->Next;//这步操作相当于让指针往后挪一位
           j++;//当前p指向的是第j个结点
       }
       return j;
   }
   //时间性能为O(n)
   ```

2. 查找

   ```c
   //(1)按序号查找：FindKth;采用类似链表的遍历方法
   List FindKth(int K,List PtrL)
   {
       List p = PtrL;//首先把p这个临时变量设置为链表的表头
       int i = 1;
       while(p != NULL && i < K){
           p = p->Next;//让指针往后挪一位
           i++;
       }
       if( i == K ) return p;//找到第K个，返回指针
       else return NULL;//否则返回空
   }
   //(2)按值查找：Find
   List Find(ElementType X,List PtrL)
   {
       List p = PtrL;
       while(p != NULL && p -> Data != X)
           p = p->Next;
       return p;
   }
   //返回的是两种结果，不是p就是NULL，调用Find函数，发现它返回值等于NULL，就说明没找着
   ```

   ### 2.1.5链式存储的插入和删除

3. 插入(在第i-1(1 <=  i <= n+1)个结点后插入一个值为X的新结点)

   ```c
   //(1)先构造一个新结点，用s指向;  这个时候可以用malloc这个函数来申请一块空间
   //(2)再找到链表的第i-1个结点，用p指向;
   //(3)然后修改指针，插入结点(p之后插入新结点是s)
   
   
   //下图中的操作步骤：让s指向下一个结点，p的Next附给s的Next
   //如果修改指针的两个步骤交换了一下哎，会发生什么？(语句执行顺序为：(1) p->Next=s;  (2) s->Next=p->Next;)
   //答案：s->Next指向s,从而不能正确完成插入
   ```

   <img src=".\数据结构-image\image-20220628002106165.png" alt="image-20220628002106165" style="zoom:50%;" />

   ```c
   //malloc复习区域
   #include<malloc.h>或者#include<alloc.h>//两者的内容是完全一样的
   如果分配成功：则返回指向被分配内存空间的指针
   不然返回指针NULL
   同时，当内存不再使用的时候，应使用free()函数将内存块释放掉。
   关于：void*,表示未确定类型的指针，c,c++规定void*可以强转为任何其他类型的指针，关于void还有一种说法就是其他任何类型都可以直接赋值给它，无需进行强转，但是反过来不可以
   malloc:
   malloc分配的内存大小至少为参数所指定的字节数
   malloc的返回值是一个指针，指向一段可用内存的起始位置，指向一段可用内存的起始地址，多次调用malloc所分配的地址不能有重叠部分，除非某次malloc所分配的地址被释放掉malloc应该尽快完成内存分配并返回（不能使用NP-hard的内存分配算法）实现malloc时应同时实现内存大小调整和内存释放函数（realloc和free）
   malloc和free是配对的，如果申请后不释放就是内存泄露，如果无故释放那就是什么也没做，释放只能释放一次，如果一块空间释放两次或者两次以上会出现错误（但是释放空指针例外，释放空指针也等于什么也没做，所以释放多少次都是可以的。）
   2、malloc和new
   new返回指定类型的指针，并且可以自动计算所需要的大小。
       int *p;
   p = new int;//返回类型为int* ，分配的大小是sizeof(int)
   p = new int[100];//返回类型是int*类型，分配的大小为sizeof(int)*100
   
   而malloc需要我们自己计算字节数，并且返回的时候要强转成指定类型的指针。
       int *p;
   p = (int *)malloc(sizeof(int));
   1）malloc的返回是void*,如果我们写成了：p=malloc(sizeof(int));间接的说明了（将void转化给了int*，这不合理）
   （2）malloc的实参是sizeof(int)，用于指明一个整型数据需要的大小，如果我们写成p=(int*)malloc(1),那么可以看出：只是申请了一个一个字节大小的空间。
   （3）malloc只管分配内存，并不能对其进行初始化，所以得到的一片新内存中，其值将是随机的。一般意义上：我们习惯性的将其初始化为NULL，当然也可以使用memset函数。
   简单的说：
   malloc函数其实就是在内存中找一片指定大小的空间，然后将这个空间的首地址给一个指针变量，这里的指针变量可以是一个单独的指针，也可以是一个数组的首地址，这要看malloc函数中参数size的具体内容。我们这里malloc分配的内存空间在逻辑上是连续的，而在物理上可以不连续。我们作为程序员，关注的是逻辑上的连续，其他的操作系统会帮着我们处理。
   下面就来看看malloc具体是怎么实现的。
   首先要了解操作系统相关的知识：
   虚拟内存地址和物理内存地址
   为了简单，现代操作系统在处理物理内存地址时，普遍采用虚拟内存地址技术。即在汇编程序层面，当涉及内存地址时，都是使用的虚拟内存地址。采用这种技术时，每个进程仿佛自己独享一片2N字节的内存，其中N是机器位数。例如在64位CPU和64位操作系统下每个进程的虚拟地址空间为264Byte。
   这种虚拟地址空间的作用主要是简化程序的编写及方便操作系统对进程间内存的隔离管理，真实中的进程不太可能如此大的空间，实际能用到的空间大小取决于物理内存的大小。
   由于在机器语言层面都是采用虚拟地址，当实际的机器码程序涉及到内存操作时，需要根据当前进程运行的实际上下文将虚拟地址转化为物理内存地址，才能实现对内存数据的操作。这个转换一般由一个叫MMU的硬件完成。
   ```

   插入实现操作

   ```c
   List Insert(ElementType X,int i,List PtrL)
   {
       List p,s;
       if(i == 1){//新结点插入在表头
           s = (List)malloc(sizeof(struct LNode));//申请、填装结点
           s -> Data = X;
           s -> Next = PtrL;
           return s;//返回新表头指针
       }
       p = FindKth(i-1.PtrL);//查找第i-1个结点
       if( p == NULL){//第i-1个不存在，不能插入
       	printf("参数i错");
           return NULL;
       }else{
           s = (List)malloc(sizeof(struct LNode));//申请、填装结点
           s -> Data = X;
           s -> Next = p -> Next;//新结点插入在第i-1个结点的后面
           p -> Next = s;
           return PtrL; //这种情况下链表的头指针是不会变的
       }
   }
   //平均查找次数是n/2
   ```

   删除(删除链表的第i(1 <=  i  <= n)个位置上的结点)

   ```c
   //(1)先找到链表的第i-1个结点，用p指向;
   //(2)再用指针s指向要被删除的结点(p的下一个结点);
   //(3)然后修改指针，删除s所指结点;
   //(4)删除的结点(s)的空间要记得free释放掉(重要)，这样内存空间才不会泄漏
   List Delete(int i,List PtrL)
   {
       List p,s;
       if( i == 1 ){//若要删除的是表的第一个结点
           s = PtrL;//s指向第一个结点
           if(PtrL != NULL) PtrL = PtrL -> Next;
           else return NULL;
           free(s);//释放掉被删除结点
           return PtrL;
       }
       p = FindKth(i-1,PtrL); //查找第i-1个结点，就是要删除结点的前一个结点在哪里
       if(p == NULL){
           printf("第%d个结点不存在",i-1); return NULL;
       }else if(p -> Next == NULL){
           printf("第%d个结点不存在",i); return NULL;
       }else{
           s = p -> Next;//s指向第i个结点
           p -> Next = s -> Next;//从链表中删除
           free(s);//释放被删除结点
           return PtrL;
       }
   }
   //平均时间复杂度也是n/2
   ```

   <img src=".\数据结构-image\image-20220628010648646.png" alt="image-20220628010648646" style="zoom:50%;" />

<img src=".\数据结构-image\image-20220628010948589.png" alt="image-20220628010948589" style="zoom:50%;" />

### 2.1.6 广义表与多重链表

<img src=".\数据结构-image\image-20220628012708073.png" alt="image-20220628012708073" style="zoom:50%;" />

<img src=".\数据结构-image\image-20220628012841252.png" alt="image-20220628012841252" style="zoom:50%;" />

原本a,b,c所在的位置变成了指针，指向另一个一元多项式。这种就是广义表

### 广义表(Generalized List)

1. 广义表是线性表的推广

2. 对于线性表而言，n个元素都是基本的单元素;

3. 广义表中，这些元素不仅可以是单元素也可以是另一个广义表

4. 广义表可能会碰到的问题：一个域有可能不能分解的单元，有可能是一个指针(C语言的解决方法是使用union(联合))

5. union(联合)：可以把不同类型的数据组合在一起，可以把这个空间理解成某种类型，也可以理解为另外一种类型

6. 区分类型的方法：再弄个标记

7. ```c
   typedef struct GNode *GList;
   struct GNode{
       int Tag;//标志域：0表示结点时单元素，1表示结点是广义表   这个Tag就是标志
       union{//子表指针域Sublist与单元素数据域Data复用，即共同存储空间
           ElementType Data;
           GList SubList;
       }URegion;
       GList Next;//指向后续结点
   };
   
   ```

   <img src=".\数据结构-image\image-20220628013451554.png" alt="image-20220628013451554" style="zoom:50%;" />

### 多重链表

**多重链表**：链表中的节点可能同时隶属于多个链

1. 多重链表中结点的指针域会有多个，如前面例子包含了Next和SubList两个指针域;
2. 但包含两个指针域的链表并不一定是多重链表，比如在双向链表不是多重链表。

多重链表有广泛的用途：基本上如树，图这样相对复杂的数据结构都可以采用多重链表的方式实现存储

多重链表指的是它里面的这个链表的结点可能同时隶属于多个链表(意思就是表中的指针会有多个)

<img src=".\数据结构-image\image-20220628014523266.png" alt="image-20220628014523266" style="zoom:50%;" />

稀疏矩阵：矩阵中的0很多，会造成空间浪费

二维数组可以用来表示选课的一种记录

上图中就是用多重链表来表示稀疏矩阵的一种方法

<img src=".\数据结构-image\image-20220628015146123.png" alt="image-20220628015146123" style="zoom:50%;" />

上图中的行与列相互穿插在一起形成十字链表

Head是作为行这个链表的头结点，也作为列这个链表的头结点

Tem：代表稀疏矩阵里面的非零的项

<img src=".\数据结构-image\image-20220628015420650.png" alt="image-20220628015420650" style="zoom:40%;" />

上图：4代表这个稀疏矩阵共有4行，总共有5列，非零项个数总共有7项

通过上图那个指针就可以找到所有列的头节点



在矩阵的多重链表表示中，第i行的head和第i列的head实际上是同一个结点(正确)



1. 用一个标识域Tag来区分头结点和非0元素结点

2. 头节点的标识值为"Head"，矩阵非0元素结点的标识值为"Term"

   <img src=".\数据结构-image\image-20220628015932693.png" alt="image-20220628015932693" style="zoom:50%;" />

经过union的串联在一起，他们共性都是有两个指针：一个Down，一个Right。他们不一样的地方在中间部分。所有我们可以把他们union在一起，形成(a)这个结构

以上就是稀疏矩阵用十字链表解决的一种基本思路

## 2.2.1 什么是堆栈

**计算机如何进行表达式求值？**

<img src=".\数据结构-image\image-20220628024357415.png" alt="image-20220628024357415" style="zoom:50%;" />

由两类对象构成的：

1. 运算数，如2、3、4
2. 运算符号,如+、-、*、/

不同运算符号优先度不一样

**后缀表达式：**运算符号位于两个运算数之后。如abc*+de/-

**中缀表达式：**运算符号位于两个运算数之间。如a+b*c-d/e

两个表达式其实是同一个意思

还有一种表达式叫“**前缀表达式**”，即运算符号位于运算数之前,比如a+b*c的前缀表达式是+a*bc。

你能写出a+b*c-d/e的前缀表达式吗？	正确答案：-+a*bc/de

【例】6 2 / 3 - 4 2 * + = ？   =>   先遇到6然后2接着是/，组成式子得到3，再遇到3然后再遇到-组成式子3-3=0，0接着遇到4跟2然后是*，4 * 2 = 8，然后目前是有0跟8，接着遇到+，相加为8

**后缀表达式求值策略**：从左往右"扫描"，逐个处理运算数和运算符号

1. 遇到运算数怎么办？如何"记住"目前还未参与运算的数？
2. 遇到运算符号怎么办？对应的运算数是什么？
3. 启示：需要有种存储方法，能顺序存储运算数，并在需要时"倒序"输出！
4. 先放进去的后拿出来，后放进去的先拿出来做运算  =>这就是**堆栈**
5. T(N) = O(N)

### 堆栈的抽象数据类型描述

**堆栈(Stack):**具有一定操作约束的线性表

1. 只在一端(栈顶，Top)做插入、删除
2. 插入数据：**入栈(Push)**
3. 删除数据：**出栈(Pop)**
4. **后入先出**：Last In First Out(LIFO) 这是特点

**数据对象集(堆栈)：**一个有0个或者多个元素的有穷线性表

**操作集(堆栈)：**长度为MaxSize的堆栈S 属于Stack，堆栈元素item 属于ElementType

```c
1. Stack CreateStack( int MaxSize)：生成空堆栈，其最大长度为MaxSize;
2. int IsFull(Stack S,int MaxSize)：判断堆栈S是否已满;
3. void Push(Stack S, ElementType item )：将元素item压入堆栈;(重点)相当于插入操作，需要判别堆栈有没有满或空
4. int IsEmpty(Stack S)：判断堆栈S是否为空;
5. ElementType Pop(Stack S)：删除并返回栈顶元素;(重点)相当于删除操作，需要判别堆栈有没有满或空。空了就不能删除了
```

<img src=".\数据结构-image\image-20220628032111469.png" alt="image-20220628032111469" style="zoom:50%;" />

**Push和Pop可以穿插交替进行**;

<img src=".\数据结构-image\image-20220628032222743.png" alt="image-20220628032222743" style="zoom:50%;" />

<img src=".\数据结构-image\image-20220628032242421.png" alt="image-20220628032242421" style="zoom:50%;" />

按ABC顺序入栈，可以产生CAB这样的出栈序列？不可以，是CBA序列

**Push：推(压入)**

**Pop：弹出**

## 2.2.2 堆栈的顺序存储实现

### 栈的顺序存储实现

```
栈的顺序存储结构通常由一个一维数组和一个记录栈顶元素位置的变量组成
```

```c
#define MaxSize <存储数据元素的最大个数>
typedef struct SNode *Stack;
struct SNode{
    ElementType Data[MaxSize];
    int Top;//用来指示栈顶的位置，Top不是地址而是一个整型变量，代表了栈顶位置它的数组下标在哪里
};
//(1)入栈
void Push (Stack PtrS, ElementType item)//包含两个参数，一个是堆栈本身，一个是用指针表示PtrS(Stack类型指针，Stack本身就是堆栈的意思)
{
    if( PtrS -> Top == MaxSize -1 ){//入栈需要判断堆栈满了没有，满了就不能再加了，有极限的。下标从0开始，所以到MaxSize-1就已经满了
        printf("堆栈满") return;
    }else{
        PtrS ->Data[++(PtrS -> Top)] = item;//item放在Top上面的一个位置，这一个语句实际做了两个事情，一是把item放到Top+1这个位置上，同时把Top值加实现了入栈的操作
        return;
    }
}
//(2)出栈
ElementType Pop(Stack PtrS)
{
    if(PtrS -> Top == -1 ){
        printf("堆栈空");//一样的操作，出栈之前要检查以下是不是空了，空了可就没东西往外掏了
        return ERROR;//ERROR是ElementType的特殊值，标志错误
    }else{
        return(PtrS -> Data[(PriS -> Top)--]);
    }
}
```

```c
【例】请用一个数组实现两个堆栈，要求最大地利用数组空间，使数组只要有空间入栈操作就可以成功
//简单将把数组对分的话来做两个堆栈会有一个问题：有一个堆栈满了，但另一个堆栈是空的，还有空余空间
//要求是数组还有空余空间，就允许有入栈操作
//用法就是一个从最左边往里放，另一个从最右边往里放。中间就是空余的大家都可以用

//根据刚才讲的方法，用一个数组来表示双堆栈，如果这两个堆栈的栈顶位置分别是top1和top2，那么可以用top1+top2==MaxSize(数组大小）来判别堆栈是否满？不可以
//因为top1跟top2代表的是数组的下标，所以top1代表的距离是中间空余的位置＋top2，top2也是同理

//正确的分析：使这两个栈分别从数组的两头开始向中间生长；当两个栈的栈顶指针相遇时，表示两个栈都满了
    #define MaxSize<存储数据元素的最大个数>
    struct DStack{
        ElementType Data[MaxSize];
        int Top1;//堆栈1的栈顶指针
        int Top2;//堆栈2的栈顶指针
    }S;
	S.Top1 = -1;//这是堆栈1空的位置
	S.Top2 = MaxSzie;//堆栈2空的位置，数组最后一个位置是MaxSize - 1

//Push(弹出)具体操作
void Push (struct DStack *PtrS,ElementType item,int Tag )
{
    //Tag作为区分两个堆栈的标志，取值为1和2，Tag的值1和2分别代表第一个堆栈跟第二个堆栈
    if( PtrS -> Top2 - PtrS -> Top1 == 1){
        printf("堆栈满"); return;
    }
    if( Tag == 1)//对第一个堆栈操作
        PtrS -> Data[++(PtrS -> Top1)] = item;//Top1后面一个位置
    else//对第二个堆栈进行操作
        PtrS -> Data[--(PtrS -> Top2)] = item;//Top2前面一个位置
}

//Pop(压入)操作
ElementType Pop (struct DStack *PtrS,int Tag )
{
    //Tag作为区分两个堆栈的标志，取值为1和2，Tag的值1和2分别代表第一个堆栈跟第二个堆栈
    if( Tag == 1){//对第一个堆栈进行操作
        if( PtrS -> Top1 == -1){//堆栈1空
         	printf("堆栈1空"); return NULL;
        }else {
            return PtrS -> Data [(PtrS -> Top1)--];
        }
    }else{//对第二个堆栈操作
    if( PtrS -> Top2 == MaxSize){//堆栈2空
    	printf("堆栈2空"); return NULL;    
    }else{
     return PtrS -> Data[(PtrS -> Top2)++];   
	}
}
```

以下部分是百度内容(对堆栈的总结)

```
堆栈严格来说应该叫做栈（stack），先入后出

四种类型：满增栈、满减栈、空增栈、空减栈。

 

满、空栈区别：根据当前指针所在位置是否有东西。

满栈（full stack）：栈指针指向最后压入栈的数据，数据入栈时，sp先减一（或加一）再入栈。

空栈（empty stack）：栈指针指向下一个将要放入数据的位置，数据入栈时，先入栈sp再减一（或加一）。

 

增、减栈区别：根据堆栈的生成方向不同。

递增堆栈(ascending stack)：堆栈由低地址向高地址生长。

递减堆栈(secending stack)：堆栈由高地址向低地址生长。

 

总结：

 

满栈进栈是先移动指针再存；

满栈出栈是先出数据再移动指针；

空栈进栈先存再移动指针；

空栈出栈先移动指针再取数据。

```

## 2.2.3 堆栈的链式存储实现

```c
栈的链式存储结构实际上就是一个单链表，叫做链栈。插入和删除操作只能再链栈的栈顶进行。
栈顶指针Top应该再链表的哪一头？若用单向链表实现一个堆栈，链表的头和尾都可以作为top？
只有链表的头才行。链表的尾空余插入，但是删除就有问题，找不到前面的结点了，因为这是单项链表找不到前面的结点
    
    typedef struct SNode*Stack;
	struct SNode{
        ElementType Data;
        struct SNode*Next;
    };
```

<img src=".\数据结构-image\image-20220628045906099.png" alt="image-20220628045906099" style="zoom:50%;" />

```C
Stack CreateStack()
{//构建一个堆栈的头结点，返回指针
    Stack S;
    S = (Stack)malloc(sizeof(struct SNode));
    s->Next = NULL;
    return S;
}

int IsEmpty(Stack S)
{
    //判断堆栈s是否为空，若为空函数返回整数1，否则返回0
    return (S->Next == NULL);
}
```

<img src=".\数据结构-image\image-20220628050006254.png" alt="image-20220628050006254" style="zoom:50%;" />

```c
void Push(ElementType item,Stack S)
{
	//将元素item压入堆栈S
	struct SNode *TmpCell;
    TmpCell = (struct SNode *)malloc(sizeof(struct SNode));
    TmpCell -> Element = item;
    TmpCell -> Next = S -> Next;
    S -> Next = TmpCell;
}
```

使用链表来进行Push操作的时候不用判别堆栈满不满的问题，因为链表通过不断申请结点空间往里面插

数组实现堆栈的话，数组大小是固定的，存在着满不满的问题



<img src=".\数据结构-image\image-20220628050745839.png" alt="image-20220628050745839" style="zoom:50%;" />

```c
ElementType Pop(Stack S)
{
	//删除并返回堆栈s的栈顶元素
    struct SNode *FirstCell;
    ElementType TopElem;
    if( IsEmpty (S) ){
        printf("堆栈空");return NULL;
    }else{
        FirstCell = S ->Next;
        TopElem = FirstCell -> Element;
        free(FirstCell);
        return TopElem;
    }
}
```

 

## 2.2.4 堆栈应用：表达式求值

### 回忆：应用堆栈实现后缀表达式求值的基本过程：

1. 从左到右读入后缀表达式的各项(运算符或运算数);

```
1.运算数：入栈;
2.运算符：从堆栈中弹出适当数量的运算数，计算并结果入栈;
3.最后，堆栈顶上的元素就是表达式的结果值
```

### 中缀表达式求值

``` = 
基本策略：将中缀表达式转化为后缀表达式，然后求值
如何将中缀表达式转化为后缀？
观察一个简单例子：2+9/3-5  ->  2 9 3 / + 5 -
1.运算数相对顺序不变
2.运算符号顺序发生改变
	1.需要存储"等待中"的运算符号
	2.要将当前运算符号与"等待中"的最后一个运算符号比较(如果前面的一个运算符号的优先级比我来得高，就说明可以拿来计算，如果优先度比我低，那么当前的运算符号还不能说就直接拿来运算，因为后面可能还有优先级比我高的，所以需要保留起来，这个时候我们就需要一种结构来实现我们运算符号的存储，那结构就是堆栈)
		输出：2 9 3
		记下：+ /
		碰到运算数 我们就把它输出
		碰到运算符号 我们等着
		
有括号怎么办？
【例】a*(b+c)/d = ?		a b c + * d /
当括号被丢进堆栈里面的时候，它的优先级降到最低，优先算括号里的内容
算数规则：当遇到同一个优先级的时候，它的顺序是从左到右

T(N) = O(N)
```

<img src=".\数据结构-image\image-20220628210030460.png" alt="image-20220628210030460" style="zoom:50%;" />

请试试应用堆栈将中缀表达式2*(6/3+4)-5转换为后缀表达式。在这个转换过程中，堆栈元素最多时元素个数是多少？3个

```
中缀表达式如何转换为后缀表达式
从头到尾读取中缀表达式的每个对象，对不同对象按不同的情况处理。
1.运算数：直接输出
2.左括号：压入堆栈
3.右括号：将栈顶的运算符弹出并输出，直到遇到左括号(出栈，不输出);
4.运算符：
	1.若优先级大于栈顶运算符时，则把它压栈;
	2.若优先级小于等于栈顶运算符时，将栈顶运算符弹出并输出;再比较新的栈顶运算符，直到该运算符大于栈顶运算符优先级为止，然后将该运算符压栈;
5.若各对象处理完毕，则把堆栈中存留的运算符一并输出
```

<img src=".\数据结构-image\image-20220628215153143.png" alt="image-20220628215153143" style="zoom:50%;" />

```
堆栈的其他应用：
函数的调用及递归实现
深度优先搜索(图)
回溯算法(老鼠走迷宫案例)等等
```

## 2.3 队列及实现

队列跟堆栈一样是一种受限制的线性表

### 什么是队列

**队列(Queue)：**具有一定操作约束的线性表

1. 插入和删除操作：只能一端插入，而在另一端删除(一般的线性表都可以在任何位置进行插入和删除)
2. 跟堆栈相比：堆栈插入和删除都是只能在一端
3. 数据插入：**入队列(AddQ)**
4. 数据删除：**出队列**(**DeleteQ**)
5. 先来先服务(跟堆栈相反)
6. 先进先出：FIFO 

```c
队列的抽象数据类型描述
    类型名称:队列(Queue)
    数据对象集：一个有0个或多个元素的有穷线性表
    操作集：长度为MaxSize的队列Q属于Queue，队列元素item属于ElementType
        
        1.Queue CreateQueue( int MaxSize )：生成长度为MaxSize的空队列；
        2.int IsFullQ(Queue Q,int MaxSize)：判断队列Q是否已满;
		3.void AddQ(Queue Q,int MaxSize)：将数据元素item插入队列Q中
        4.int IsEmptyQ( Queue Q):判断队列Q是否为空;
		5.ElementType DeleteQ( Queue Q):将队头元素从队列中删除并返回
```

### 2.3.1 队列的顺序存储实现

```c
队列的顺序存储结构通常由一个一维数组和一个记录队列头元素位置的变量front以及一个记录队列尾元素位置的变量rear组成的

    #define MaxSize <储存数据元素的最大个数>
    struct QNode{
        ElementType Data[MaxSize];
        int rear;//指针
        int front;//指针
    };
typedef struct QNode *Queue;

如果空队列开始时front和rear值都是-1，当插入4个元素并删除2个元素后，front和rear值分别是多少？1和3
```

#### 顺环队列

```
1.这种方案：堆栈空和满的判别条件是什么？
根据front rear的相对关系(就是他们的距离)来判别的font rear的取值范围是0 -- n-1
2.为什么会出现空，满无法区分？根本原因？
如果大小是n的话，font跟rear的差距的情况就是n种，队列的装载元素的情况有n+1种
解决方案：
(1)使用额外标记：Size或者tag域
当加入一个元素的时候Szie加1，删除一个元素的时候Size减1，通过Size等于0还是等于n就可以知道空的还是满的
使用标记tag 0 1，当我们插入一个元素的时候tag设为1，删除一个元素的时候tag等于0，当front跟rear相等时不清楚空还是满的时候，观察tag，tag就代表了最后一次操作是插入还是删除，就知道是空还是满
(2)仅使用n-1个数组空间(最多放n-1个元素，留一个空位出来就可以避免front跟rear相等的情况)
```

(1)入队列

```c
void AddQ(Queue PtrQ,ElementType item)//item放到队列中 队列使用Queue的结构指针PtrQ来进行表示
{
    if((PtrQ -> rear+1)%MaxSize == PtrQ -> front){//具体代换：5+1对6求余为0
        printf("队列满");
        return;
    }
    PtrQ -> rear = (PtrQ -> rear+1)%MaxSize;
    PtrQ -> Data[PtrQ -> rear] = item;
}
```

(2)出队列

```c
ElementType DeleteQ( Queue PtrQ)
{
	if(PtrQ -> front == PtrQ -> rear){
		printf("队列空");
		return ERROR;
	}else{
		PtrQ -> front = (PtrQ -> front+1)%MaxSize;
		return PtrQ -> Data[PtrQ -> front];
	}
}
```

### 2.3.2 队列的链式存储实现

```c
队列的链式存储结构也可以用一个单链表实现。插入和删除操作分别在链表的两头进行；队列指针front和rear应该分别指向链表的哪一头？
队列的front也可以设在链表的尾？错误，不行的 
只能前面做删除(front)，后面做加入(rear)，如果倒过来的话，删掉了就不知道前面一个在哪里了
    
    struct Node{
        ElementType Data;//结点本身的信息
        struct Node *Next;//把结点串在一起
    };
struct QNode{//链队列结构
    struct Node *rear;//指向队尾结点
    struct Node *front;//指向队头结点
};
typedef struct QNode *Queue;
Queue PtrQ;
```

<img src=".\数据结构-image\image-20220629041503372.png" alt="image-20220629041503372" style="zoom:50%;" />

**不带头结点的链式队列出队操作的示例**

```c
ElementType DeleteQ(Queue PtrQ)
{
	struct Node *FrontCell;
    ElementType FrontElem;
    
    if(PtrQ -> front == NULL){
        printf("队列空"); return ERROR;
    }
    FrontCell = PtrQ -> front;
    if(PtrQ -> front == PtrQ -> rear)//若队列只有一个元素
        PtrQ -> front = PtrQ -> rear = NULL;//删除后队列置为空
    else
        PtrQ -> front = PtrQ -> front -> Next;
    FrontElem = FrontCell -> Data;
    free(FrontCell);//释放被删除结点空间
    return FrontElem;
}
```

## 2.4 多项式的加法运算实现

<img src=".\数据结构-image\image-20220629052314628.png" alt="image-20220629052314628" style="zoom:50%;" />

```
采用不带头结点的单项链表，按照指数递减的顺序排列各项
```

<img src=".\数据结构-image\image-20220629052450477.png" alt="image-20220629052450477" style="zoom:50%;" />

```c
具体实现代码(数据结构)
struct PolyNode{
    int coef;//系数
    int expon;//指数
    struct PolyNode link;//指向下一个节点的指针
};
typedef struct PolyNode*Polynomial;
Polynomial P1,P2;

多项式加法运算(算法思路):
两个指针p1和p2分别指向这两个多项式第一个结点，不断循环：
    1. P1->expon == P2 -> expon(这里比较的其实是指数)：系数相加，若结果不为0，则作为结果多项式对应项的系数。同时，P1和P2都分别指向下一项
    2. P1->expon > P2 -> expon：将P1的当前项存入结果多样式，并使P1指向下一项;
    3. P1->expon < P2 -> expon：将P2的当前项存入结果多样式，并使P2指向下一项;

当某一多项式处理完时，将另一个多项式的所有结点依次复制到结果多项式中去
```

<img src=".\数据结构-image\image-20220629060519985.png" alt="image-20220629060519985" style="zoom:50%;" />

演变过程------------------------分割线

<img src=".\数据结构-image\image-20220629060715728.png" alt="image-20220629060715728" style="zoom:50%;" />

接着是=>

<img src=".\数据结构-image\image-20220629060827486.png" alt="image-20220629060827486" style="zoom:50%;" />

```c
函数实现
Polynomial PolyAdd(Polynomial P1.Polynomial P2)
{
    Polynomial front,rear,temp;//多项式的头是第一个，尾巴第二个，
    int sum;
    rear = (Polynomial) malloc(sizeof(struct PolyNode));//临时申请一个空结点作为结果多项式的表头
    front = rear;//由front记录结果多项式链表头结点，申请的空间front都指向它
    while(P1 && P2)//当两个多项式都有非零待处理时(判空)
        switch(Compare(P1->expon,P2->expon)){//比较P1P2这两个项的所指向的当前这个项的指数，第一个值大返回1，第二个值大返回-1，两个值相等返回0
                case1://P1大
                		Attach(P1->coef,P1->expon,&rear);//两个参数分别代表我要拷贝的这一项的系数和指数
                		P1 = P1 -> link;
                		break;//形成的新的项把它接到rear的后面，P1往后挪
                case-1：//P2大
                    	Attach(P2->coef,P2->expon,&rear);
                		P2 = P2 -> link;
                		break;
                case0://一样大
                        sum = P1->coef + P2->coef;
                		if(sum)Attach(sum.P1->expon,&rear);//判定，如果为0就不用加到结果多项式里面去，不为零就把sum作为系数跟对于的指数凑在一起，把他接到rear的后面去
                		P1 = P1 -> link;
                		P2 = P2 -> link;
                		break;
        }
    //将未处理完的另一个多项式的所有节点依次复制到结果多项式中去
    for(;P1;P1 = P1 -> link) Attach(P1->coef,P1->expon,&rear);//第一个for循环处理P1不空，如果不空就是把P1后面的每一项全部Attach(接到结果多项式的后面的意思)，同时把P1往后挪
    for(;P2;P2 = P2 -> link) Attach(P2->coef,P2->expon,&rear);//如果P1不空的话P2肯定就空了，也就把P2后面的每一项一个一个拷贝到rear的后面去
    rear ->link = NULL;//指向结果多项式的最后一项
    temp = front;
    front = front -> link;//令front指向结果多项式第一个非零项
    free(temp);//释放临时空表头结点  怎么释放？=>将front赋给temp，然后front往后挪，front原来是指向这个临时的表头结点，这个表头结点的下一项就是我们真正的多项式的第一项 
    return front;//返回结果多样式中这个单项链表的第一个结点
}

问题：如果当前p1指向项的（系数，指数）为（2，4），同时P2指向项为(2，6)，那么循环中的switch是执行哪个case?
case -1
```

```c
Attach实现
void Attach(int c,int e,Polynomial*pRear)//传进来的是c跟e的系数跟指数。当前最后一个结点的指针位置传进来的是Polynomial这个类型的指针(Polynomial本身也是指针)，所以pRear实际上是指针的指针
    //C语言是函数常数值传递
{
	Polynomial P;
	
	P = (Polynomial)malloc(sizeof(struct PloyNode));//结点类型是struct PolyNode这个类型
	P -> coef = c;//对新结点赋值
	P -> expon = e;
	P -> link = NULL;
	(*pRear)->link = P;//把新申请的结点P插到rear的后面
	*pRear = P;//修改pRear值
}
```

<img src=".\数据结构-image\image-20220629070700010.png" alt="image-20220629070700010" style="zoom:50%;" />

<img src=".\数据结构-image\image-20220629070712967.png" alt="image-20220629070712967" style="zoom:50%;" />

## 小白专场

<img src=".\数据结构-image\image-20220629071856844.png" alt="image-20220629071856844" style="zoom:50%;" />

#### 多项式的表示

<img src=".\数据结构-image\image-20220629072141892.png" alt="image-20220629072141892" style="zoom:50%;" />

```c
用链表进行表示
    数据结构设计
    typedef struct PolyNode *Polynomial;//将结构指针定义为一个新的类型叫做Polynomial
struct PolyNode{
    int coef;//系数
    int expon;//指数
    Polynomial link;//阈，作为指针指向下一个节点
}
```

#### 程序框架搭建

```c
int main()
{
    读入多项式1
    读入多项式2
    乘法运算并输出
    加法运算并输出
        
        return 0;
}
需要设计的函数：
    1.读一个多项式
    2.两多项式相乘
    3.两多项式相加
    4.多项式输出
   
    int main()
{
    Polynomial P1,P2,PP,PS;
    
    P1 = ReadPoly();
    P2 = ReadPoly();//P1,P2都是链表的结构的指针
    PP = Mult(P1,P2);//Mult是乘法运算，返回的也是一个结构的指针
    PrintPoly(PP);//由PrintPoly来输出多项式
    PS = Add(P1,P2);//加法运算 
    PrintPoly(PS);
    
    return 0;
}

如何读入多项式
    Polynomial ReadPoly()
{
    ....//先读整数再一对一的读入系数跟指数
        scanf("%d",&N);
    ....
        while(N--){//这是读入系数跟指数，放到c跟e里面
        scanf("%d %d",&c,&e);
    	Attach(c,e,&Rear);//读的时候是通过指数递降的顺序来读取的，Rear是可以被改变的，Rear是指向目前为止多项式的最后一项
        }
}
//注意：我们是从左到右读入的，而且先读的是指数高的一项，要插到一个链表里面去，然后再读一对数再插进去，再形成一个节点
//再读一对系数指数，再插到多项式里面去，在链表里面也是指数递降的，应该插到原来结果的后面

Rear初始值是多少？
    两种处理方法：
    1.Rear初值为NULL(说明是刚开始的一个节点，这个时候需要申请节点，然后把Rear用NULL改为指向这个节点)
    在Attach函数中根据Rear是否为NULL做不同处理
    如果值不为NULL，因为从第二项开始Rear值就不为NULL了。这个时候直接把新的节点插到Rear的后面。这样的一种处理方式在Attach函数里面他必须判别Rear是NULL还是说不是NULL，因为这两者处理的程序是不一样的
    2.Rear指向一个空结点(程序时更简单)
    需要注意在最后的时候记得把这个空结点释放掉
    
    处理方法2的具体代码：
    void Attach (int c,int e,Polynomial*pRear)//Polynomial本身也是指针，所以这里的pRear实际是指针的指针(为什么这么做是因为C语言是函数常数直传递)
{
    Polynomial P;
    P = (Polynomial)malloc(sizeof(struct PolyNode));
    P -> coef = c;//对新结点赋值
    P -> expon = e;
    P -> link = NULL;
    (*pRear)->link = P;//指针指过去了
    *pRear = P;//修改pRear值，指到P那里去了
}
```

<img src=".\数据结构-image\image-20220629084605937.png" alt="image-20220629084605937" style="zoom:50%;" />

<img src=".\数据结构-image\image-20220629084915089.png" alt="image-20220629084915089" style="zoom:50%;" />

<img src=".\数据结构-image\image-20220629085051166.png" alt="image-20220629085051166" style="zoom:50%;" />

**处理方法2**

<img src=".\数据结构-image\image-20220629085848083.png" alt="image-20220629085848083" style="zoom:50%;" />

#### 读入多项式的完整程序

```c
Polynomial ReadPoly()
{
    Polynomial P,Rear,t;
    int c,e,N;
    
    scanf("%d",&N);
    P = (Polynomial)malloc(sizeof(struct PolyNode));//链表头空结点
    P -> link = NULL;
    Rear = P;
    while(N--){
        scanf("%d %d",&c,&e);
        Attach(c,e,&Rear)//将当前项插入多项式尾部
    }
    t = P;P = P -> link;free(t);//删除临时生成的头结点  t指向P，P指向P的link  
    return P;
}
```

<img src=".\数据结构-image\image-20220629091612698.png" alt="image-20220629091612698" style="zoom:50%;" />

#### 如何将两个多项式相乘

1. **将当前乘法运算转换为加法运算**

```c
将P1当前项(ci,ei)乘P2多项式，再加到结果多项式里
t1 = P1;t2 = P2;
P = (Polynomial)malloc(sizeof(struct PolyNode));P->link=NULL;//指向空结点的操作
Rear = P;
while(t2){
	Attach(t1->coef*t2->coef,t1->expon+t2->expon,&Rear);
	t2 = t2->link;
}
```

**2.逐项插入**

```c
将P1当前项(c1i,e1i)乘P2当前项(c2i,e2i)，并插入到结果多项式中。关键是要找到插入位置
初始结果多项式可以由P1第一项乘P2获得(如上)
    
    具体代码如下
    Polynomial Mult(Polynomial P1,Polynomial P2)
{
    .......
    t1 = P1；t2 = P2;
    .......
    while(2){
        //先用P1的第一项乘以P2,得到P
        ........
    }
    t1 = t1->link;
    while(t1){
        t2 = P2;Rear = P;
        while(t2){
            e=t1->expon + t2->expon;//指数相加
            c=t1->coef*t2->coef;//系数相乘
            ......
            t2=t2->link;
        }
        t1=t1-<link;
    }
    ......
}
```

以上代码块中有三项省略号的地方需要解释，单独分别列出来在下方进行标记

**第一处省略**

```c
Polynomial P,Rear,t1,t2,t;
int c,e;

if(!P1||!P2) return NULL;

t1 = P1;t2 = P2;
P = (Polynomial)malloc(sizeof(struct PolyNode));P->link = NULL;
Rear = P;
```

**第二处**

```c
while(t1){
	t2 = P2;Rear = P;
    while(t2){
        e = t1->expon + t2->expon;
        c = t1->coef*t2->coef;
    while(Rear->link && Rear->link->expon>e)
        Rear = Rear->link;
    if(Rear->link && Rear->link->expon == e){
       if(Rear->link->coef + c)//判别一下加完是否为0，若不为0则直接加进去，若为0就删掉把内存空间释放
           Rear->link->coef += c;
        else{
            t = Rear -> link;
            Rear->link = t ->link;
            free(t);
        }
    }else{//不相等就是小于的情况，就需要申请一个结点，然后把c跟e赋给这个结点
        t = (Polynomial)malloc(sizeof(struct PolyNode));
        t -> coef = c;t->expon = e;
        t ->link = Rear->link;
        Rear->link=t;Rear = Rear->link;
    }
        t2 = t2->link
    }
    t1 = t1 -> link
}
........
```

![image-20220629214627763](.\数据结构-image\image-20220629214627763.png)

**第三处省略**

```
t2 = P; P = P->link;free(t2);
return P;
```

