# 数据结构第三周笔记——树(上)(慕课浙大版本--XiaoYu)

> 笔记出处：GitHub地址：[2002XiaoYu (小余) (github.com)](https://github.com/2002XiaoYu)，作者：小余
>
> 笔记持续更新中，有需要的自行获取，提供markdown跟PDF两种格式，在线浏览如果受到不可控影响无法登录GitHub可以查看我掘金账号的专栏对应的文章(目前在陆续上传中)
>
> 如果对你有所帮助，给作者一个star就是最好的鼓励，作者会持续产出的

## 3.1 树与树的表示

#### 什么是树

1. 人类社会家谱，社会组织结构，图书信息管理都是树的一种体现(层次型的组织结构)
2. 分层次组织在管理上具有更高的效率
   1. 数据管理的基本操作之一：查找

### 3.1.1 引子(顺序查找)

#### 查找(Searching)

```
查找：根据某个给定关键字K，从集合R中找出关键字与K相同的记录
	静态查找：集合中记录是固定的
		没有插入和删除操作，只有查找(例如查字典)
	动态查找：集合中记录是动态变化的
		除查找，还可能发生插入和删除
```

##### 静态查找

方法1：顺序查找

```C
int SequentialSearch (List Tbl,ElementType K)
{
	//	在Element[1]~Element[n]中查找关键字为K的数据元素
    int i;
    Tbl->ElementP[0] = K;//建立哨兵
    for(i = Tbl->Length;Tbl->Element[i] != K;i--);//倒过来做的，从下标大的地方开始往前循环
    return i;//查找成功返回所在单元下标;不成功不返回0
}

typedef struct LNode{
    ElementType Element[MAXSIZE];//决定了放的元素有多少个
    int Length;
};

顺序查找的一种实现(无"哨兵")
    int SequentialSearch (List Tbl ,ElementType K)
{
    //在Element[1]~Element[n]中查找关键字为K的数据元素
    int i;
    
    for(i=Tbl->Length; i>0 && Tbl->Element[i] != K;i--);
    return i;//查找成功返回所在单元下标，不成功返回0
}
在顺序查找中,如果把下列程序中的循环条件"i>0"去掉，会发生什么后果？
    答案：要查找的元素不存在时发生数组越界（i指向小于0的位置）
    
    "哨兵"的作用:可以在数组的最后或者边界上面设一个值，不用每次去判断它的下标是不是达到我的边界。根据for循环，当碰到我们放置的那个值的时候，循环就该退出来了。这样我们在写判断的时候就可以少写一个判断的分支
        我的理解是这个哨兵通常放置在下标0的地方，然后循环从大往小循环，循环到1如果都没有我们需要的值的时候，放在下标0的地方的哨兵是等于我们需要的值来退出循环，代替了for条件中的不能小于0，防止for循环陷入死循环或者说越界跑到下标负数的地方去了。然后返回值如果是0的话我们就心知肚明没有找到我们想要的值了
        
        这种顺序查找算法的时间复杂度为O(n)，平均数是n/2
```

### 3.1.2 引子(二分查找例子)

#### 方法3：二分查找(Binary Search)

**使用前提**：有序存放

<img src="./数据结构-image\image-20220630133518543.png" alt="image-20220630133518543" style="zoom:50%;" />

**例子**：假设有13个数据元素，按关键字由小到大顺序存放，二分查找关键字为444的数据元素过程如下：

<img src="./数据结构-image\image-20220630133710273.png" alt="image-20220630133710273" style="zoom:50%;" />

<img src="./数据结构-image\image-20220630133856929.png" alt="image-20220630133856929" style="zoom:50%;" />

<img src="./数据结构-image\image-20220630134122043.png" alt="image-20220630134122043" style="zoom:50%;" />

 

```
两个边界分别是left跟right
具体过程数据就不展示了，用图片展示出来了，减少我的工作量
在13个元素的二分查找中，找第10个元素比找第8个元素快?对
```

**例子2**：仍然以上面13个数据元素构成的有序线性表为例，二分查找关键字为43的数据元素如下：

<img src="./数据结构-image\image-20220630134238735.png" alt="image-20220630134238735" style="zoom:50%;" />

<img src="./数据结构-image\image-20220630134317471.png" alt="image-20220630134317471" style="zoom:50%;" />

<img src="./数据结构-image\image-20220630134845190.png" alt="image-20220630134845190" style="zoom:50%;" />

### 3.1.3 引子(二分查找实现)

#### 二分查找算法

```c
int BinarySearch(List Tbl,ElementType K)//List Tbl是结构的指针，包含了数组Element和它的大小Length
{
	//在表Tbl中查找关键字为K的数据元素
    int left,right,mid,NoFound = -1;
    
    left = 1;//初始左边界
    right = Tbl->Length//初始右边界
        while(left <= right)
        {
            mid = (left + right)/2 //计算中间元素坐标
            if(K < Tbl->Element[mid])   right = mid - 1;//调整右边界
            else if(K > Tbl->Element[mid]) left = mid + 1;//调整左边界
            else return mid;//查找成功，返回数据元素下标
        }
    return NotFound;//查找不成功，返回-1
}
所以我们就return NotFound

在二分查找的程序实现中，如果left和right的更新不是取mid+1和mid-1而是都取mid，程序也是正确的?当然是错误的啦
    

//List定义: List是stl实现的双向链表,与向量(vectors)相比, 它允许快速的插入和删除,但是随机访问却比较慢。使用时需要添加头文件 #include
```

**二分查找算法具有对数的时间复杂度O(logN)**

#### 11个元素的二分查找判定树

<img src="./数据结构-image\image-20220630141214141.png" alt="image-20220630141214141" style="zoom:50%;" />

**二分查找的启示**：

1. 判定树上每个结点需要的查找次数刚好为该结点所在的层数
2. 查找成功时的查找次数不会超过判定树的深度
3. n个结点的判定树的深度是[log2n]+1 (这里的2是下标)
4. ASL（平均成功查找次数） = (4 * 4 + 4 * 3 + 2 * 2 +1)/11 = 3

### 3.1.4 树的定义和术语

当在树里面插入结点 删除结点的时候比在数组里面方便得多

#### 树的定义

**树(Tree)**：n(n >= 0)个结点构成的有限集合。

当n = 0时，称为空树;

对于任一棵非空树(n > 0)，它具备以下性质：

1. 树中有一个称为"根(Root)"的特殊结点，用r表示;
2. 其余结点可分为m(m>0)个互不相交的有限集T1，T2,....,Tm,其中每个集合本身又是一颗树，称为原来树的"子树(SubTree)"
3. <img src="./数据结构-image\image-20220630212408181.png" alt="image-20220630212408181" style="zoom:50%;" />

#### 树与非树

不是树的例子

1. <img src="./数据结构-image\image-20220630212516643.png" alt="image-20220630212516643" style="zoom:50%;" />
2. 子树是不相交的;
3. 除了根结点外，每个结点有且只有一个父结点;
4. 一颗N个结点的树有N-1条边
5. **树是保证结点联通的最小的一种连接方式(边最少)**
6. 有一个m棵树的集合（也叫森林）共有k条边，问这m颗树共有多少个结点？k+m

#### 树的一些基本术语

1. 结点的度(Degree)：结点的子树个数
2. 树的度：树的所有结点中最大的度数
3. 叶结点(Leaf):度为0的结点
4. 父结点(Parent)：有子树的结点是其子树的根节点的父节点
5. 子结点(Child)：若A结点是B结点的父节点，则称B结点是A结点的子结点;子节点也称孩子结点
6. 兄弟结点(Sibling)：具有同一父节点的各结点彼此是兄弟结点 
7. <img src="./数据结构-image\image-20220630221534749.png" alt="image-20220630221534749" style="zoom:50%;" />
8. 祖先结点(Ancestor)：沿着树根到某一结点路径上的所有结点都是这个结点的祖先结点
9. 子孙结点(Descendant)：某一结点的子树中的所有结点是这个结点的子孙
10. 结点的层次(Lever)：规定根节点在1层，其他任一结点的层数是其父节点的层数加1
11. 树的深度(Depth)：树的所有结点中的最大层次是这颗树的深度

### 3.1.1 树的表示

数组实现：把这些结点按顺序存储在数组里面

<img src="./数据结构-image\image-20220630231222581.png" alt="image-20220630231222581" style="zoom:50%;" />

链表表示：

<img src="./数据结构-image\image-20220630231244287.png" alt="image-20220630231244287" style="zoom:50%;" />

 这样树的结构：每个结点的结构的样子是不一样的。有的结点有3个指针，有的结点有1个指针，有的没有指针。这样整个这个结构的形式都不一样，会给后面的程序实现带来困难(因为访问之前没办法确认会带来多少个儿子)

```c
//另外的构想：能不能为了保持结构的统一，我将每个指针都跟结点中最多的对齐，其他结点不需要的指针就当空指针用。这样的好处就是所有树结点结构是统一的，程序处理起来方便。但同样会带来问题：

//如果这个树有n个结点，那意味着每个结点有3个指针域，整个树会有3n个指针域，而实际上我们的边只有n-1条,这样就会有2n+1个指针域是空的，造成空间上的浪费
```



#### 儿子-兄弟表示法

<img src="./数据结构-image\image-20220630235030353.png" alt="image-20220630235030353" style="zoom:50%;" />

这样可以将整颗树的结点把它串起来

实现效果如下：

<img src="./数据结构-image\image-20220630235200617.png" alt="image-20220630235200617" style="zoom:50%;" />

```
这种方法的优点:
1.树种的每个结点结构都是统一的，都是两个指针域，同时它的空间浪费也不大
	n个结点2n个指针域 其中n-1条边。
	意味着n-1个域是非空的，真正空的域是n+1
	
	问题：在用“儿子-兄弟”法表示的树中，如果从根结点开始访问其“次子”的“次子”，所经过的结点数与下面哪种情况一样？(注意:比较的是结点数,而不是路径)
	答案：从根结点开始访问其“长子”的“长子”的“长子”的“长子”
```

#### 二叉树的图

<img src="./数据结构-image\image-20220630235703723.png" alt="image-20220630235703723" style="zoom:50%;" />

**二叉树特点**：

1. 链表实现方法：旋转45度
2. 每个结点都有两个指针，一个指向左边一个指向右边，每个结点最多是两个儿子
3. 二叉树就是度为2的一种树
4.  <img src="./数据结构-image\image-20220630235936075.png" alt="image-20220630235936075" style="zoom:50%;" />

## 二叉树及存储结构

### 3.2.1 二叉树的定义及性质

#### 二叉树的定义

```
	二叉树T:一个有穷的结点集合

​		这个集合可以为空
		若不为空，则它是由根结点和称为其左子树TL和右子树TR的两个不相交的二叉树组成(L和R是下标)
```

**二叉树具体五种基本形态**

<img src="./数据结构-image\image-20220701000338791.png" alt="image-20220701000338791" style="zoom:50%;" />

**二叉树的子树有左右之分**

<img src="./数据结构-image\image-20220701000422530.png" alt="image-20220701000422530" style="zoom:50%;" />

#### 特殊的二叉树

##### 斜二叉树(Skewed Binary Tree)

1. 只有左边或者只有右边，相当于一个链表
2. <img src="./数据结构-image\image-20220701000612336.png" alt="image-20220701000612336" style="zoom:50%;" />

##### 完美二叉树(Perfect Binary Tree)

**或者叫做满二叉树(Full Binary Tree)**

<img src="./数据结构-image\image-20220701000716448.png" alt="image-20220701000716448" style="zoom:50%;" />

特点：	一个深度为k(>=-1)且有2^(k+1) - 1个结点的二叉树称为完美二叉树。 满二叉树

#### 完全二叉树(Complete Binary Tree)

```
有n个结点的二叉树，对树种结点按从上到下，从左到右的顺序进行编号，编号为i(1 <= i <= n)结点与满二叉树中编号为i结点在二叉树中位置相同
跟上方的区别就是除了最底下一层可以右边缺一点，上面跟满二叉树是一样的。最底下一层左边顺序不能乱
```

#### 二叉树的几个重要性质

<img src="./数据结构-image\image-20220701001640388.png" alt="image-20220701001640388" style="zoom:50%;" />

叶结点的总数等于有两个儿子的结点的总数加1

```
有一颗二叉树，其两个儿子的结点个数为15个，一个儿子的结点个数为32个，问该二叉树的叶结点个数是多少？
16
```

#### 二叉树的抽象数据类型定义

```c
类型名称：二叉树
数据对象集：一个有穷的结点集合。
	若不为空，则由根结点和其左、右二叉子树组成。
	
操作集： BT属于BinTree,Item属于ElementType
重要操作有：
1.Boolean IsEmpty(BinTree BT)：判别BT是否为空;
2.void Traversal(BinTree BT)：遍历，按某顺序访问每个结点;(重要)
3.BinTree CreateBinTree():创建一个二叉树
```

##### 常用的遍历方法

```c
1.void PreOrderTraversal(BinTree BT)://先序--根、左子树、右子树
2.void InOrderTraversal(BinTree BT)://中序--左子树、根、右子树
3.void PostOrderTraversal(BinTree BT)://后序--左子树、右子树、根
4.void LevelOrderTraversal(BinTree BT)://层次遍历(层序遍历,从上到下、从左到右
```

### 3.2.2 二叉树的存储结构

#### 1.顺序存储结构

```c
完全二叉树：按从上至下，从左到右顺序存储n个结点的完全二叉树的结点父子关系;
//这个树最适合数组方式解决
```

<img src="./数据结构-image\image-20220701003345507.png" alt="image-20220701003345507" style="zoom:50%;" />

1. 非根结点(序号 i > 1)的父节点的序号是[i/2]
   1. 这句话的意思就是说假设我们目前知道M结点时6，如果想知道它的父节点就是6/2=3，Q结点就是7/2=3.5，把小数去掉，父节点一样是3
2. 结点(序号为i)的左孩子结点的序号是2i，(若2i <= n,否则没有左孩子)
   1. 举例：意思就是B左孩子是C,O的左孩子是M
3. 结点(序号为i)的右孩子结点的序号是2i+1，(若2i+1 <= n,否则没有右孩子)
   1. 举例：类似上方的意思B右孩子是S...

**一般二叉树**也可以采用这种结构，缺点：但会造成空间浪费....(将缺少的结点补上一个空结点)

<img src="./数据结构-image\image-20220701004348299.png" alt="image-20220701004348299" style="zoom:50%;" />

<img src="./数据结构-image\image-20220701004413217.png" alt="image-20220701004413217" style="zoom:50%;" />

```
问题：

如果参照完全二叉树的表示方法用数组存储下面这棵二叉树，那么结点e所对应的数组下标是多少（树根下标为1）？
答案：6
```

<img src="./数据结构-image\image-20220701004541010.png" alt="image-20220701004541010" style="zoom:50%;" />

2.**链表存储**

<img src="./数据结构-image\image-20220701004912537.png" alt="image-20220701004912537" style="zoom:50%;" />

```c
typedef struct TreeNode *BinTree;
typedef BinTree Position;
struct TreeNode{
    ElementType Data;
    BinTree Left;
    BinTree Right;
}
```

<img src="./数据结构-image\image-20220701005059244.png" alt="image-20220701005059244" style="zoom:50%;" />

## 二叉树的遍历

### 3.3.1 先序中序后序遍历

#### 二叉树的遍历

##### (1)先序遍历

```c
遍历过程为：
1.访问根节点
2.先序遍历其左子树
3.先序遍历其右子树 

对应的递归程序：
void PreOrderTraversal(BinTree BT)//BT是树
{
    if( BT ){//看树是不是空的，空的就退出去，不空就访问根节点
        printf("d",BT->Data);
        PreOrderTraversal(BT->Left);//指向左子树的那个根节点的地址进行递归
        PreOrderTraversal(BT->Right);//指向右子树的那个根节点的地址进行递归
    }
}
```

<img src="./数据结构-image\image-20220701101511679.png" alt="image-20220701101511679" style="zoom:50%;" />

```
先是从根开始  A(B D F E)(C G H I)
先序遍历=>A B D F E C G H I
```

##### (2)中序遍历

```c
遍历过程为：
1.中序遍历其左子树
2.访问根结点;
3.中序遍历其右子树 
对应的递归程序：
void PreOrderTraversal(BinTree BT)//BT是树
{
    if( BT ){//看树是不是空的，空的就退出去，不空就访问根节点
        PreOrderTraversal(BT->Left);//指向左子树的那个根节点的地址进行递归
        printf("d",BT->Data);
        PreOrderTraversal(BT->Right);//指向右子树的那个根节点的地址进行递归
    }
}
```

<img src="./数据结构-image\image-20220701102142532.png" alt="image-20220701102142532" style="zoom:50%;" />

```
中序遍历 => D B E F A G H C I
(D B E F) A ( G H C I)
先是左边再右边，这里我原本的疑问是为什么是先E在F而不是先F再E
原因是E-F是一个树，然后因为这个是左子树，所以从左边开始，E在F的左边
```

<img src="./数据结构-image\image-20220701103417703.png" alt="image-20220701103417703" style="zoom:50%;" />

##### (3)后序遍历

```c
遍历过程为：
1.后序遍历其左子树
2.中序遍历其右子树 
3.访问根结点
对应的递归程序：
void PreOrderTraversal(BinTree BT)//BT是树
{
    if( BT ){//看树是不是空的，空的就退出去，不空就访问根节点
        PreOrderTraversal(BT->Left);//指向左子树的那个根节点的地址进行递归
        PreOrderTraversal(BT->Right);//指向右子树的那个根节点的地址进行递归
        printf("d",BT->Data);
    }
}
```

<img src="./数据结构-image\image-20220701103619016.png" alt="image-20220701103619016" style="zoom:50%;" />

```
注意点：后序遍历，那当然得从下面开始咯，所以会发现B跟E是E优先。H跟C比是H优先
 (D E F B)(H G I C) A
 后序遍历 => D E F B H G I C A
```

##### 总结

```
先序、中序和后序遍历过程：遍历过程中经过结点的路线一样，只是访问各结点的时机不同
图中先从入口到出口的曲线上用三种符号分别标记除先序，中序和后序的访问各结点时刻
```

<img src="./数据结构-image\image-20220701112206952.png" alt="image-20220701112206952" style="zoom:50%;" />



### 3.3.2 中序非递归遍历

#### 二叉树的非递归遍历

##### 中序遍历非递归遍历算法

**非递归算法实现的基本思路：**使用堆栈

这里建议看视频的演示，非常形象

```c
1.遇到一个结点，就把它压栈，并去遍历它的左子树;
2.当左子树遍历结束后，从栈顶弹出这个结点并访问它;
3.然后按其右指针再去中序遍历该结点的右子树

以下是完整代码实现演示
void InOrderTraversal(BinTree BT)
{
    BinTree T = BT;//把BT赋给临时变量T
    Stack S = CreateStack(MaxSize);//创建并初始化堆栈
    while(T || !IsEmpty(S) ){//树不空且堆栈不空
        while(T){//判断堆栈空不空
            //一直向左并将沿途结点压入堆栈
            Push(S,T);//把T压入堆栈S中
            T = T->Left;//然后把T往左挪，就是边挪边把结点压到堆栈里面去，压到底T为NULL就退出来
        }
        if(!IsEmpty(S)){
            T = Pop(S);//结点弹出堆栈
            printf("%5d",T->Data);//(访问)打印结点
            T = T->Right;//转向右子树
        }
    }
}

ppoopoppoo错误
    
PPOPOOPPOO正确
    第二次碰到同一个的时候就print出来
```

<img src="./数据结构-image\image-20220701114302678.png" alt="image-20220701114302678" style="zoom:50%;" />

##### 先序遍历的非递归遍历算法？

把上方那个算法中if中的printf("%5d",T->Data)移到while(T)中Push的后面

##### 后序遍历非递归遍历算法？

### 3.3.3 层序遍历

**二叉树遍历的核心问题**：二维结构的线性化(变成一维的)

```
1.从结点访问其左、右儿子结点
2.访问左儿子后，右儿子结点怎么办？
	1.需要一个存储结构保存暂时不访问的结点
	2.存储结构：堆栈、队列
```

#### 队列实现

```
遍历从根结点开始，首先将根结点入队，然后开始执行循环：结点出队、访问该结点、其左右儿子入队
```

<img src="./数据结构-image\image-20220701122430994.png" alt="image-20220701122430994" style="zoom:50%;" />

<img src="./数据结构-image\image-20220701122450231.png" alt="image-20220701122450231" style="zoom:50%;" />

<img src="./数据结构-image\image-20220701122514520.png" alt="image-20220701122514520" style="zoom:50%;" />

<img src="./数据结构-image\image-20220701122530744.png" alt="image-20220701122530744" style="zoom:50%;" />

<img src="./数据结构-image\image-20220701122549970.png" alt="image-20220701122549970" style="zoom:50%;" />

<img src="./数据结构-image\image-20220701122603151.png" alt="image-20220701122603151" style="zoom:50%;" />

最后结果就是：层序遍历 => A B C D F G I E H

```c
//代码实现
void LevelOrderTraversal(BinTree BT)
{
    Queue Q; BinTree T;
    if(!BT) return;//若是空树直接返回
    Q = CreateQueue(MaxSize);//创建并初始化队列Q
    AddQ(Q,BT);//把BT这个根节点放到Q这个队列里
    while(!IsEmptyQ(Q)){
        T = DeleteQ(Q);//抛出元素赋给T就是一个指针
        printf("%d\n",T->Data);//访问去除队列的结点
        if(T->Left) Add(Q,T->Left);//左右儿子放到队列里去
        if(T->Right) AddQ(Q,T->Right);
    }
}
```



### 3.3.4 遍历应用例子

#### 【**例**】遍历二叉树的应用：输出二叉树中的叶子结点

在二叉树的遍历算法中增加检测结点的"左右子树是否都为空"

```c
void PreOrderPrintLeaves(BinTree BT)
{
    if(BT){
        if(!BT-Left && !BT->Right)
            printf("%d",BT->Data);
        PreOrderPrintLeaves(BT -> Left);
        PreOrderPrintLeaves(BT -> Right);
    }
}
```

#### 【例】求二叉树的高度

 <img src="./数据结构-image\image-20220701124015460.png" alt="image-20220701124015460" style="zoom:50%;" />

```c
void PreOrderGetHeight(BinTree BT)
{
    int HL,HR,MaxH
    if(BT){
        HL = PostOrderGetHeight(BT->Left);//求左子树的深度，这两个都是递归哦
        HR = PostOrderGetHeight(BT->Right);//求右子树的深度
        MaxH = (HL > HR) ? HL :HR;//取左右子树较大的深度
        return (MaxH + 1);//返回树的深度
    }
    else return 0;//空树深度为0
}
```

#### 【例】二元运算表达式树及其遍历

<img src="./数据结构-image\image-20220701124745933.png" alt="image-20220701124745933" style="zoom:50%;" />

**中缀表达式是不准的，会收到运算符优先度的影响**，其它都是准的

解决中缀表达式不准的问题：左子树开始前加个左括号，结束后加个右括号，通过加括号的形式解决优先度问题

#### 【例】由两种遍历序列确定二叉树

已知三种遍历中的任意两种遍历序列，能否唯一确定一颗二叉树呢？

答案：必须要有中序遍历才行

没有中序的困扰:

​	1.先序遍历序列：A B

​	2.后序遍历序列：B A

会发现符合条件的树不止一个



**先序第一个是根**，**后序最后一个是根**

<img src="./数据结构-image\image-20220701125607995.png" alt="image-20220701125607995" style="zoom:50%;" />

问：已知有颗5个结点的二叉树，其前序遍历序列是a????，中序遍历序列是a????，可以断定：

<img src="./数据结构-image\image-20220701125653916.png" alt="image-20220701125653916" style="zoom:50%;" />

答案： 该树根结点是a，且没有左子树

## 小白专场

### 题意理解及二叉树表示

<img src="./数据结构-image\image-20220701221930149.png" alt="image-20220701221930149" style="zoom:50%;" />

 <img src="./数据结构-image\image-20220701224453225.png" alt="image-20220701224453225" style="zoom:50%;" />

不是同构的(反面教材)：

<img src="./数据结构-image\image-20220701224543157.png" alt="image-20220701224543157" style="zoom:50%;" />

<img src="./数据结构-image\image-20220701224727874.png" alt="image-20220701224727874" style="zoom:50%;" />

<img src="./数据结构-image\image-20220701224754107.png" alt="image-20220701224754107" style="zoom:50%;" />

#### 求解思路

1. **二叉树表示**

   1. <img src="./数据结构-image\image-20220701224950324.png" alt="image-20220701224950324" style="zoom:50%;" />(链表的表示方法啊)

   2. <img src="./数据结构-image\image-20220701225028557.png" alt="image-20220701225028557" style="zoom:50%;" />(一般的用数组表示二叉树做法)

   3. 结构数组表示二叉树：静态链表(物理上存储是数组，思想是一种链表的思想 )

      1. <img src="./数据结构-image\image-20220701225755398.png" alt="image-20220701225755398" style="zoom:50%;" />

         A B C D是代表节点本身的信息(用来标识节点的)，并且Left根Right不是指向左儿子右儿子的，而是指向他们的下标的，指向空节点用-1表示

         ```c
         静态链表数组实现代码
             #define MaxTree 10
             #define ElementType char
             #define Tree int
             #define Null -1
             struct TreeNode
             {
                 ElementType Element;
                 Tree Left;//这里Left跟Right不是指针
                 Tree Right;
             }T1[MaxTree],T2[MaxTree];//NULL在C语言这个stdlb.h中定义是0
         //静态链表优势：有链表的灵活性，但他的存储又是在数组上面
         ```

         

2. **建二叉树**

3. **同构判别**

### 程序框架、建树及同构判别

```c
int main()
{
    建二叉树1
    建二叉树2
    判别是否同构并输出
        
        return 0;
}

需要设计的函数：
    1.读数据建二叉树
    2.二叉树同构判别
    
    怎么判别二叉树是不是同构的代码实现
    int main()
{
    Tree R1,R2;
    
    R1 = BuildTree(T1);//建第一棵树R1
    R2 = BuildTree(T2);//建第二课树R2
    if(Isomorphic(R1,R2)) printf("Yes\n");//判断二叉树是不是同构的
    else printf("No\n");
    
    return 0;
}
```

<img src="./数据结构-image\image-20220701234728261.png" alt="image-20220701234728261" style="zoom:50%;" />

%c：转化为整数

Root：树根

根节点：哪个节点不存在其他节点指向他，那就是根节点

Root = ???  =>T[i]中没有任何结点的left(cl)和right(cr)指向它。只有一个

<img src="./数据结构-image\image-20220701235112746.png" alt="image-20220701235112746" style="zoom:50%;" />

```
最后if(!check[i]) break;中返回的值是0的就是根结点，因为有指向的都被转化为1了

T[i].Left = cl - '0';的原因是我们是以字符的形式读取进来的，字符2减去字符0就是整数2
```

#### 如何判别两二叉树同构

<img src="./数据结构-image\image-20220701235555687.png" alt="image-20220701235555687" style="zoom:50%;" />

<img src="./数据结构-image\image-20220701235650631.png" alt="image-20220701235650631" style="zoom:50%;" />

