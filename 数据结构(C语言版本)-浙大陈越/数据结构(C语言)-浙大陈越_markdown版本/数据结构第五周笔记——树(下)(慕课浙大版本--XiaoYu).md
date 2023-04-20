# 数据结构第五周笔记——树(下)(慕课浙大版本--XiaoYu)

> 笔记出处：GitHub地址：[2002XiaoYu (小余) (github.com)](https://github.com/2002XiaoYu)，作者：小余
>
> 笔记持续更新中，有需要的自行获取，提供markdown跟PDF两种格式，在线浏览如果受到不可控影响无法登录GitHub可以查看我掘金账号的专栏对应的文章(目前在陆续上传中)
>
> 如果对你有所帮助，给作者一个star就是最好的鼓励，作者会持续产出的

## 5.1 堆(heap)

### 5.1.1 什么是堆

1. ```
   优先队列(Priority Queue)：特殊的"队列"，取出元素的顺序是依照元素的优先权(关键字)大小，而不是元素进入队列的先后顺序
   ```

   1. <img src=".\数据结构-image\image-20220707112759644.png" alt="image-20220707112759644" style="zoom:50%;" />

2. 是否可以采用二叉树存储结构？

   1. 二叉搜索树？
   2. 如果采用二叉树结构，应更关注插入还是删除？
      1. 树结点顺序怎么安排？
      2. 树结构怎么样？

3. 优先队列的完全二叉树表示

   1. <img src=".\数据结构-image\image-20220707114110845.png" alt="image-20220707114110845" style="zoom:50%;" />

   2. ```
      堆的两个特性
      	结构性：用数组表示的完全二叉树;
      	有序性：任一结点的关键字是其子树所有结点的最大值(或最小值)
      		"最大堆(MaxHeap)"，也称"大顶堆"：最大值
      		"最小堆(MinHeap)"，也称"小顶堆"：最小值
      	通俗的来说就是最大堆的上面的那个要比连接下面两个都要大。最小堆则相反，最上面那个要比连接下面两个都要小。同时需要是完全二叉树
      	注意：从根结点到任意结点路径上结点系列的有序性！
      ```

   3. <img src=".\数据结构-image\image-20220707114551521.png" alt="image-20220707114551521" style="zoom:50%;" />

#### 堆的抽象数据类型描述

```
类型名称：最大堆(MaxHeap)
数据对象集：完全二叉树，每个结点的元素值不小于其子结点的元素值
操作集：最大堆H属于MAaxHeap,元素item属于ElementType，主要操作有：
1.MaxHeap Create(int MaxSize)：创建一个空的最大堆
2.Boolean IsFull(MaxHeap H)：判断最大堆H是否已满
3.Insert(MaxHeap H,ElementType item)：将元素item插入最大堆H
4.Boolean IsEmpty(MaxHeap H):判断最大堆H是否为空
5.ElementType DeleteMax(MaxHeap H)：返回H中最大元素(高优先级)
```

### 5.1.2 堆的插入

最大堆的创建

```c
typedef struct HeapStruct *MaxHeap;
struct HeapStruct{
    ElementTye *Elements;//存储堆元素的数组
    int Size;//堆的当前元素个数
    int Capacity;//堆的最大容量
};
MaxHeap Create(int MaxSize)
{
    //创建容量为MaxSize的空的最大堆
    MaxHeap H = malloc(sizeof(struct HeapStruct));
    H->Elements = malloc((Maxsize+1)* sizeof(ElementType));//Maxsize+1是希望申请的容量
    H->Size = 0;
    H->Capacity = MaxSize;
    H->Elements[0] = MaxSize;
    H->Elements[0] = MaxData;
    //定义"哨兵"为大于堆中所有可能元素的值，便于以后更快操作
    return H;
}
```

#### 最大堆的插入

1. <img src=".\数据结构-image\image-20220707205756557.png" alt="image-20220707205756557" style="zoom:33%;" />

2. <img src=".\数据结构-image\image-20220707205844776.png" alt="image-20220707205844776" style="zoom:33%;" />

3. <img src=".\数据结构-image\image-20220707205940384.png" alt="image-20220707205940384" style="zoom:33%;" />

4. 如果在底下插入的数值比上面大就沿着线路一路换位置，直到上面没有在比插入的大了为止

5. 算法的实现如下:将新增结点插入到从其父结点到根结点的有序序列中

   1. ```c
      void Insert(Maxheap H,ElementType item)
      {
          //将元素item插入最大堆H,其中H->Elements[0]已经定义为哨兵
          int i;
          if(IsFull(H)){
          printf("最大堆已满");
          return;
      }
      i = ++H->Size;//i指向插入后堆中的最后一个元素的位置
      for(;H->Elements[i/2]) < item;i/=2)
          H->Elements[i] = H->Elements[i/2];//向下过滤结点
      H->Elements[i] = item;//将item插入
      }
      复杂度：T(N) = O(log N)
      ```

      <img src=".\数据结构-image\image-20220707212158564.png" alt="image-20220707212158564" style="zoom:33%;" />

      问题：“哨兵”是在创建堆(Create函数）时设置的：H->Elements[0]=MaxData;

      答案：这是对的

      

### 5.1.3 堆的删除

#### 最大堆的删除

1. 取出根结点(最大值)元素，同时删除堆的一个结点

2. <img src=".\数据结构-image\image-20220707213148580.png" alt="image-20220707213148580" style="zoom:33%;" />

3. <img src=".\数据结构-image\image-20220707213217119.png" alt="image-20220707213217119" style="zoom:50%;" />

   1. 时间复杂度就等于树的高度

4. 删除MaxHeap程序实现：

   1. ```c
      ElementType DeleteMax(MaxHeap H)
      {
          //从最大堆H中取出键值为最大的元素，并删除一个结点
          int Parent,Child;
          ElementType MaxItem,temp;
          if( IsEmpty(H)){
              printf("最大堆已为空");
              return;
          }
          MaxItem = H->Elements[1];//取出根结点最大值，保存起来，最后是要return出去的
          //temp = H->Elements[H->Size--];
          for(Parent = 1;Parent*2 <= H->Size;Parent=Child){//Parent指示我将来要换的位置，退出循环就说明了我们要的位置找到了
              Child = Parent * 2;//Child指向左儿子
              if((Child != H->Size) && (H->Elements[Child] < H->Elements[Child+1]))
                  Child++;//Child指向左右子结点的较大者
              if(temp > H->Elements[Child])break;
              else//移动temp元素到下一层
                  H->Elements[Parent] = H->Elements[Child];
          }
          H->Elements[Parent] = temp;
          return MaxItem;
      }
      
      在完全二叉树里面，i代表一个节点，它的左儿子在2i,右儿子在2i+1
          如果左儿子2i已经超出Size的范围，说明左儿子已经在堆栈外面了，那就说明它没左儿子
          Parent*2 <= H->Size是判别它有没有左儿子，如果没有左儿子那右儿子肯定也没有那就退出循环了
          
          Child != H->Size：Child指向左儿子意味左儿子是最后一个元素了，那就意味着没有右儿子了。实际上这个就相当于判别有没有右儿子
      ```

      问题：有个堆其元素在数组中的序列为：58，25，44，18，10，26，20，12。如果调用DeleteMax函数删除最大值元素，请猜猜看：程序中的for循环刚退出时变量parent的值是多少？

      是6

### 5.1.4 堆的建立

#### 堆的一个应用：堆排序--需要先建堆

最大堆的建立

```
建立最大堆：将已经存在的N个元素按最大堆的要求存放在一个一维数组中

方法1：通过插入操作，将N个元素一个个相继插入到一个初始为空的堆中去，其时间代价最大为O(NlogN)
每次插入 它的时间复杂性是log2N
总共循环N遍，所以整个时间复杂性是Nlog2N
上方这个方法1的效率是不够的，我们可以有更好的方法
方法2：在线性时间复杂度下建立最大堆.
(1)将N个元素按输入顺序存入，先满足完全二叉树的结构特性
(2)调整各结点位置，以满足最大堆的有序特性
```

问：建堆时，最坏情况下需要挪动元素次数是等于树中各结点的高度和。问：对于元素个数为12的堆，其各结点的高度之和是多少？10

<img src=".\数据结构-image\image-20220707221437125.png" alt="image-20220707221437125" style="zoom:67%;" />

### 小测验：堆

![image-20220707222337161](.\数据结构-image\image-20220707222337161.png)

<img src=".\数据结构-image\image-20220707223127298.png" alt="image-20220707223127298" style="zoom:50%;" />

### C语言代码：堆的定义和操作

```c
typedef struct HNode *Heap; /* 堆的类型定义 */
struct HNode {
    ElementType *Data; /* 存储元素的数组 */
    int Size;          /* 堆中当前元素个数 */
    int Capacity;      /* 堆的最大容量 */
};
typedef Heap MaxHeap; /* 最大堆 */
typedef Heap MinHeap; /* 最小堆 */

#define MAXDATA 1000  /* 该值应根据具体情况定义为大于堆中所有可能元素的值 */

MaxHeap CreateHeap( int MaxSize )
{ /* 创建容量为MaxSize的空的最大堆 */

    MaxHeap H = (MaxHeap)malloc(sizeof(struct HNode));
    H->Data = (ElementType *)malloc((MaxSize+1)*sizeof(ElementType));
    H->Size = 0;
    H->Capacity = MaxSize;
    H->Data[0] = MAXDATA; /* 定义"哨兵"为大于堆中所有可能元素的值*/

    return H;
}

bool IsFull( MaxHeap H )
{
    return (H->Size == H->Capacity);
}

bool Insert( MaxHeap H, ElementType X )
{ /* 将元素X插入最大堆H，其中H->Data[0]已经定义为哨兵 */
    int i;
 
    if ( IsFull(H) ) { 
        printf("最大堆已满");
        return false;
    }
    i = ++H->Size; /* i指向插入后堆中的最后一个元素的位置 */
    for ( ; H->Data[i/2] < X; i/=2 )
        H->Data[i] = H->Data[i/2]; /* 上滤X */
    H->Data[i] = X; /* 将X插入 */
    return true;
}

#define ERROR -1 /* 错误标识应根据具体情况定义为堆中不可能出现的元素值 */

bool IsEmpty( MaxHeap H )
{
    return (H->Size == 0);
}

ElementType DeleteMax( MaxHeap H )
{ /* 从最大堆H中取出键值为最大的元素，并删除一个结点 */
    int Parent, Child;
    ElementType MaxItem, X;

    if ( IsEmpty(H) ) {
        printf("最大堆已为空");
        return ERROR;
    }

    MaxItem = H->Data[1]; /* 取出根结点存放的最大值 */
    /* 用最大堆中最后一个元素从根结点开始向上过滤下层结点 */
    X = H->Data[H->Size--]; /* 注意当前堆的规模要减小 */
    for( Parent=1; Parent*2<=H->Size; Parent=Child ) {
        Child = Parent * 2;
        if( (Child!=H->Size) && (H->Data[Child]<H->Data[Child+1]) )
            Child++;  /* Child指向左右子结点的较大者 */
        if( X >= H->Data[Child] ) break; /* 找到了合适位置 */
        else  /* 下滤X */
            H->Data[Parent] = H->Data[Child];
    }
    H->Data[Parent] = X;

    return MaxItem;
} 

/*----------- 建造最大堆 -----------*/
void PercDown( MaxHeap H, int p )
{ /* 下滤：将H中以H->Data[p]为根的子堆调整为最大堆 */
    int Parent, Child;
    ElementType X;

    X = H->Data[p]; /* 取出根结点存放的值 */
    for( Parent=p; Parent*2<=H->Size; Parent=Child ) {
        Child = Parent * 2;
        if( (Child!=H->Size) && (H->Data[Child]<H->Data[Child+1]) )
            Child++;  /* Child指向左右子结点的较大者 */
        if( X >= H->Data[Child] ) break; /* 找到了合适位置 */
        else  /* 下滤X */
            H->Data[Parent] = H->Data[Child];
    }
```



## 5.2 哈夫曼树与哈夫曼编码

### 5.2.1 什么是哈夫曼树(Huffman Tree)

[例]将百分制的考试成绩转换成五分制的成绩

```C
if(score < 60 ) grade = 1;
else if(score < 70) grade = 2;
else if(score < 80) grade = 3;
else if(score < 90) grade = 4;
else grade = 5;
```

上述代码中，其实对应着就有一棵树，如下图：

<img src=".\数据结构-image\image-20220707234237931.png" alt="image-20220707234237931" style="zoom:50%;" />

<img src=".\数据结构-image\image-20220707234356578.png" alt="image-20220707234356578" style="zoom:50%;" />

优化后的效率：

<img src=".\数据结构-image\image-20220707234452711.png" alt="image-20220707234452711" style="zoom:50%;" />

```c
if(score < 80)
{
    if(score < 70 )
        if(score < 60) grade = 1;
    else grade = 2;
}else if(score < 90 )grade = 4;
else grade = 5;
```

**如何根据结点不同的查找频率构造更有效的搜索树？**

#### 哈夫曼树的定义

<img src=".\数据结构-image\image-20220707234921567.png" alt="image-20220707234921567" style="zoom:50%;" />

**最优二叉树或哈夫曼树就是WPL最小的二叉树**

一棵二叉树每一个叶结点的频率或者权重乘以这个叶结点到根结点的这个路径的长度就是带权路径的长度

**权值 = 频率**

[例]有五个叶子结点，它们的权值为{1，2，3，4，5}，用此权值序列可以构造出形状不同的多个二叉树

 <img src=".\数据结构-image\image-20220708001419786.png" alt="image-20220708001419786" style="zoom:50%;" />

<img src=".\数据结构-image\image-20220708001539908.png" alt="image-20220708001539908" style="zoom:50%;" />是50

<img src=".\数据结构-image\image-20220708001741632.png" alt="image-20220708001741632" style="zoom:50%;" />



### 5.2.2 哈夫曼树的构造

1. 每次把权值最小的两颗二叉树合并

2. <img src=".\数据结构-image\image-20220708002041082.png" alt="image-20220708002041082" style="zoom:50%;" />

3. 代码实现(如何选取两个最小的？)

   1. ```c
      typedef struct TreeNode *HuffmanTree;
      struct TreeNode{
          int Weight;
          HuffmanTree Left,Right;
      }
      HuffmanTree Huffman(MinHeap H)
      {
          //假设H->Size个权值已经存在H->Elements[]->Weight里
          int i; HuffmanTree T;
          BuildMinHeap(H);//将H->Elements[]按权值调整为最小堆
          for(i = 1;i < H->Size; i++){//做H->Size-1次合并
              T = malloc(sizeof(struct TreeNode));//建立新结点
              T->Left = DeleteMin(H);//从最小堆中删除一个结点，作为新T的左子结点
              T->Right = DeleteMin(H);//从最小堆中删除一个结点，作为新T的右子结点
              T->Weight = T->Left->Weight+T->Right->Weight;//计算新权值
              Insert(H,T);//将新T插入最小堆
          }
          T = DeleteMin(H);
          return T;
      }
      整体复杂度为O(NlogN)
      ```

      #### 哈夫曼树的特点：

      1. 没有度为1的结点;
      2. n个叶子结点的哈夫曼树共有2n-1个结点
         1. n0：叶结点总数
         2. n1：只有一个儿子的结点总数
         3. n2：有2个儿子的结点总数
         4. n2 = n0 - 1
      3. 哈夫曼树的任意非叶节点的左右子树交换后仍是哈夫曼树;
      4. <img src=".\数据结构-image\image-20220708003623357.png" alt="image-20220708003623357" style="zoom:50%;" />
      5. 

### 5.2.3 哈夫曼编码

给定一段字符串，如何对字符进行编码，可以使得该字符串的编码存储空间最少？

<img src=".\数据结构-image\image-20220708003841443.png" alt="image-20220708003841443" style="zoom:50%;" />

分析：

1. 用等长ASCII编码：58×8 = 464位
2. 用等长3位编码：58×3 = 174位;
3. 不等长编码：出现频率高的字符用的编码短些，出现频率低的字符则可以编码长些？

#### 怎么进行不等长编码？

#### 如何避免二义性(就是你这个编码不止一个意思)

1. 前缀码prefix code：任何字符的编码都不是另一字符编码的前缀
   1. 可以无二义地解码(你的这个编码不能是其他编码的前缀)
2. 二叉树用于编码：
   1. 左右分支：0、1
   2. 字符只在叶结点上
   3. <img src=".\数据结构-image\image-20220708004934296.png" alt="image-20220708004934296" style="zoom:50%;" />
   4. <img src=".\数据结构-image\image-20220708005100967.png" alt="image-20220708005100967" style="zoom:50%;" />
   5. <img src=".\数据结构-image\image-20220708005137719.png" alt="image-20220708005137719" style="zoom:50%;" />
   6. 怎么构造一颗编码代价最小的二叉树？
      1. <img src=".\数据结构-image\image-20220708005631723.png" alt="image-20220708005631723" style="zoom:67%;" />



### 小测验：哈夫曼树

<img src=".\数据结构-image\image-20220708010907498.png" alt="image-20220708010907498" style="zoom:50%;" />



## 5.3 集合及运算

### 5.3.1 集合的表示及查找

1. 集合运算：交，并，补，差，判定一个元素是否属于某一集合

2. 并查集：集合并、查某元素属于什么集合

3. 并查集问题中集合存储如何实现？

   1. 可以用树结构表示集合，树的每个结点代表一个集合元素

   2. <img src=".\数据结构-image\image-20220708125448566.png" alt="image-20220708125448566" style="zoom:50%;" />对这样三棵树的存储方法

      1. 采用数组存储形式
      2. <img src=".\数据结构-image\image-20220708130212563.png" alt="image-20220708130212563" style="zoom:50%;" />
      3. 上图中没有父节点的用负数来表示，Parent是它父节点的下标

   3. ```
      [例]：有10台电脑{1，2，3，.....，9，10}，已知下列电脑之间已经实现了连接：
      1和2、2和4、3和5、4和7、5和8、6和9、6和10
      问：2和7之间，5和9之间是否是连通的？
      2和7是连通的，5和9不连通
      ```

      <img src=".\数据结构-image\image-20220708124931978.png" alt="image-20220708124931978" style="zoom:50%;" />

#### 集合运算

(1)查找某个元素所在的集合(用根结点表示)

```c
int Find(SetType S[],ElementType X)
{
    //在数组S中查找值为X的元素所属的集合
    //MaxSize是全局变量，为数组S的最大长度
    int i;
    for(i = 0;i < MaxSize && S[i].Data != X; i++);
    if(i >= MaxSize) return -1;//未找到X,返回-1
    for(;S[i].Parent >= 0; i = S[i].Parent);//Parent的值为-1的时候就是找到根结点。i = S[i].Parent：原本指向i的位置现在跳到了s[i].Parent
    return i;//找到X所属集合，返回树根结点在数组S中的下标
}
```



### 5.3.2 集合的并运算

(2)集合的并运算

1. 分别找到X1和X2两个元素所在集合树的根结点

2. 如果它们不同根，则将其中一个根结点的父结点指针设置成另一个根结点的数组下标

   1. Union实现代码

   2. ```c
      void Union(SetType S[ ],ElementType X1,ElementType X2 )
      {
          int Root1,Root2;
          Root1 = Find(S,X1);//得到X1与X2对应的树根
          Root2 = Find(S,X2);
          if( Root1 != Root2 ) S[Root2].Parent = Root1;//判断如果不是本身就是同一个集合的，如果是同一个集合的话就不需要做这个并的操作。不同则合并
      }
      ```

      为了改善合并以后的查找性能，可以采用小的集合合并到相对大的集合中。(修改Union函数)。也许树的高度不会增加

### 小测验：集合

已知a、b两个元素均是所在集合的根结点，且分别位于数组分量3和2位置上，其parent值分别为-3,-2。问：将这两个集合按集合大小合并后，a和b的parent值分别是多少？

-5，3

#### 集合的定义与并查

```c
#define MAXN 1000                  /* 集合最大元素个数 */
typedef int ElementType;           /* 默认元素可以用非负整数表示 */
typedef int SetName;               /* 默认用根结点的下标作为集合名称 */
typedef ElementType SetType[MAXN]; /* 假设集合元素下标从0开始 */

void Union( SetType S, SetName Root1, SetName Root2 )
{ /* 这里默认Root1和Root2是不同集合的根结点 */
    /* 保证小集合并入大集合 */
    if ( S[Root2] < S[Root1] ) { /* 如果集合2比较大 */
        S[Root2] += S[Root1];     /* 集合1并入集合2  */
        S[Root1] = Root2;
    }
    else {                         /* 如果集合1比较大 */
        S[Root1] += S[Root2];     /* 集合2并入集合1  */
        S[Root2] = Root1;
    }
}

SetName Find( SetType S, ElementType X )
{ /* 默认集合元素全部初始化为-1 */
    if ( S[X] < 0 ) /* 找到集合的根 */
        return X;
    else
        return S[X] = Find( S, S[X] ); /* 路径压缩 */
```



## 小白专场：堆中的路径-C语言实现

### 堆中的路径

1. <img src=".\数据结构-image\image-20220708133742876.png" alt="image-20220708133742876" style="zoom:50%;" />
2. 5 3的意思是给你5个数据构成一个最小堆进行3次查询。第二行就是要插入的五个数据。5 4 3代表下标

<img src=".\数据结构-image\image-20220708134013080.png" alt="image-20220708134013080" style="zoom:33%;" />

```c
堆的表示及其操作(堆是一种按一定顺序组织的完全二叉树)
#define MAXN 1001
#define MINH -10001

int H[MAXN],size;//由于堆在存储的时候是把根结点放在数组下标为1的地方，也就是说0是空缺的
//这样子按照一层层顺序逐个往数组后面存放，使得堆中的任何一个元素可以很容易的找到他的父节点在哪里，左右儿子在哪里
//整数size表示当前堆大小
void Create()//堆的初始化，就是建立一个空堆(size设置为0)
{
    size = 0;
    H[0] = MINH;//设置岗哨
}
//插入操作
void Insert(int X)
{
    //将X插入H。这里省略检查堆是否已满的代码
    int i;
    for(i = ++size;H[i/2]>X;i/=2)
        H[i] = H[i/2];//i挪到父节点(i/2)的位置
    H[i] = X;
}

主程序
    int main()
{
    1.读入n和m
    2.根据输入序列建堆
    3.对m个要求：打印到根的路径
        
        return 0;
}
//具体实现程序
int main()
{
    int n,m,x,i,j;
    
    scanf("%d%d",&n,&m);
    Create();//堆初始化
    for(i = 0;i < n; i++ ) {//以逐个插入方式建堆
        scanf("%d",&x);
        Insert(x);//利用Insert函数插到堆中
    }
    //m个查询
    for(i=0;i<m;i++){
        scanf("%d",&j);
        printf("d",H[j]);
        while(j < 1){//沿根方向输出各结点(也就是说把他的祖先全部打印出来了)当j>1是代表还没有到根的时候，根的位置是1，这时候j/2就代表了他父节点的位置
            j /= 2;
            printf("%d",H[j]);
        }
        printf("\n");
    }
    return 0;
}
```



## 小白专场[陈越]：File Transfer (是一道经典的并查集的应用题)-C语言实现

### 小白-FT.1 集合的简化表示

集合的表示方法

```c
typedef struct {
    ElementType Data;
    int Parent;
}SetType;
```

查找Find函数

```c
int Find(SetType S[],ElementType X)
{
    int i;
    for(i = 0; i < MaxSize && S[i].Data != X; i++);//找到X在集合中是第几个元素，这个时间复杂度是O(N2)这个数量级，可以有更好的方法来寻找
    if( i >= MaxSize ) return -1;
    for(;S[i].Parent >= 0;i = S[i].Parent);
    return i;
}
```

<img src=".\数据结构-image\image-20220708232958390.png" alt="image-20220708232958390" style="zoom:50%;" />

但其实这个声明这个Data专门用来存储是可以省略的，直接用数组来存储就行了，想知道有几个独立的集合就看数组里面有几个-1就可以了(-1表示处于最上方的了)

<img src=".\数据结构-image\image-20220708233303936.png" alt="image-20220708233303936" style="zoom:50%;" />

#### 集合简化表示

```c
typedef int ElementType;//默认元素可以用非负整数表示
typedef int SetNameL;//默认用根结点的下标作为集合名称
typedef ElementType SetType[MaxSize];

SetName Find(SetType S,ElementType X)//这个X直接就是数组的下标
{
    //默认集合元素全部初始化为-1
    for(;S[X]>=0;X=S[X]);//这和S[X]里面存的直接就是X的父节点
    return X;
}

void Union (SetType S, SetName Root1,SetName Root2)
{
    //这里默认Root1和Root2是不同集合的根结点
    S[Root2] = Root1;
}
```



### 小白-FT.2 题意理解与TSSN的实现

#### 题意理解

C：检测能不能连通，能就Yes不行就No

I：连通

S：输入结束

<img src=".\数据结构-image\image-20220708234749670.png" alt="image-20220708234749670" style="zoom:50%;" />

在这里面每台计算机都是直接或者间接连通的。在上图中1虽然是一个孤立的计算机，但他也被认为是跟自己连通的

所以这个会输出：There are 2 components (这个系统有两个连通机)

<img src=".\数据结构-image\image-20220709000356238.png" alt="image-20220709000356238" style="zoom:50%;" />

在上图的这种情况下，输出的最后一行就应该是The network is connected(这个网络是连通的)

#### 程序框架搭建

```C
int main()
{
	初始化集合;
    do{
        读入一条指令;
        处理指令;
    }while(没结束);
    return 0;
}
```

翻译为具体代码如下

```c
int main()
{
    SetType S;//这是并查集
    int n;//n是集合里面元素的个数
    char in;//第一个字符被命名为in
    
    //初始化集合
    scanf("%d\n",&n);
    Initialization(S,n);
    do{
        scanf("%c",&in);
        switch(in){
            case 'I':Input_connection(S);break;//把两台需要连接的计算机的编号读进来，检查是否连接好了，没连接好就连接一下
                //Union和Find函数都会出现在上方那个函数当中
            case 'C':Check_connection(s);break;//这个函数要做的就是读入待查询的计算机的编号，检查他们在不在一个集合里面，用到Find函数
            case 'S':Check_network(s,n);break;//检查网络是否已经连通了
        }
    }while(in != 'S');
    
    
    
    
    return 0;
}
```

<img src=".\数据结构-image\image-20220709000953246.png" alt="image-20220709000953246" style="zoom:50%;" />

答案是A

#### 程序框架搭建2

```c
//处理输入
void Input_connection(SetType S)
{
    ElementType u,v;
    SetName Root1,Root2;
    scanf("%d %d\n",&u,&v);
    Root1 = Find(S,u-1);
    Root2 = Find(S,v-1);
    if( Root1 != Root2 )
        Union(S,Root1,Root2);
}
-----------------------------------------------------------------
//处理查询
void Check_connection(SetType S)
{
    ElementType u,v;
    SetName Root1,Root2;
    scanf("%d %d\n",&u,&v);
    Root1 = Find(S,u-1);
    Root2 = Find(S,v-1);
    if( Root1 == Root2 )
        printf("Yes\n");
    else printf("no\n")
}
--------------------------------------------------
//检查整个网络是否已经连通
    void Check_network(SetType S,int n)
{
    int i,count = 0;
    for(i = 0; i < n; i++){
        if( S[i] < 0 ) counter++//只要S[i] < 0就意味着这是一个根节点
    }
    if(counter == 1)//只有一个根节点表示整个集合全部连通了
        printf("The network is connected.\n");//输出网络连通了
    else
        printf("There are %d conponents.\n",counter);//输出一共有剁谁个连通集
}

运行效率取决于Find函数与Union函数怎么实现的
```

TSSN(to simple sometime neive)实现

### 小白-FT.3 按秩归并

#### 为什么需要按秩归并？

1. ```
   1. Union(Find(2),Find（1))//注意2在前，1在后。前面输入集合1后面输入集合2。让集合2指向集合1。其实也就是1指向2
   2. Union(Find(3),Find（1))//这里1指向3了，由于上面那个，相当于2也指向3了
   ```

   <img src=".\数据结构-image\image-20220710091606477.png" alt="image-20220710091606477" style="zoom:50%;" />但这样显然会让树越来越高，有几个数，树就有多高，退化成单链表了，而且每次都绑定在1身上就意味着每次都需要从1开始找，直到找到他的根结点

   刚才一系列Union(Find(![img](.\数据结构-image\6608211117981750295.jpeg)), Find(1))（其中![img](.\数据结构-image\6631765955584149961.jpeg))操作的时间复杂度是：T(n) = O(n²)

   #### 为什么树的高度会越来越高

   <img src=".\数据结构-image\image-20220710092019371.png" alt="image-20220710092019371" style="zoom:50%;" />

   1. 只要把矮树贴到高树上就可以避免这种情况。但这样就势必要判断树的高度再决定谁贴到谁身上

   #### 树的高度存哪里？

   ```C
   S[Root] = -1//这是根结点，设置为-1就不会是任何一个元素的下标了，这样就把他和其他的非根结点区别开了。写-1或者-100都是没有区别的都可以
       //由这个想法向后延伸
       S[Root] = -树高//依旧初始化为-1，这棵树的高度一开始就是1，每个计算机都是单独的一个结点
       
       //代码演示(伪代码)
       if( Root2高度 > Root1高度 )
           S[Root1] = Root2;//集合1指向集合2
   	else{
           if(两树一样高) 树高++;//存在新的结点里面
           S[Root2] = Root1;
       }
   
   //实际代码，比高度的做法
       if( S[Root2] < S[Root1] )//因为存的是负数，所以需要反着来
           S[Root1] = Root2;//集合1指向集合2
   	else{
           if( S[Root1] == S[Root2] ) Root1--;//存在新的结点里面，是负数负数负数，需要--而不是++
           S[Root2] = Root1;
       }
   ```

   #### 另一种做法：比规模

   1. 把小树贴在大树上

   2. S[Root] = -元素个数

   3. 最后那个结果树的规模都会改变，变为两个树的规模之和

   4. ```c
      void Union(SetType S,SetName Root1,SetName Root2)
      {
          if(S[Root2] < S[Root1] ){
              S[Root2] += S[Root1];
              S[Root1] = Root2;//把集合小的贴到大的上面去，在这里Root2更大，条件中只是因为是负数表示反过来了而已
          }
          else{
              S[Root1] +=S[Root2];
              S[Root2] = Root1;
          }
      }
      ```

      **上述的两种方法都统称按秩归并**

      按秩归并：最坏情况下树高 = O(logN)

### 小白-FT.4 路径压缩

按秩归并是对Union的一个改进

路径压缩是对Find这个函数的一个改进

```C
SetName Find (SetType S,ElementType X)
{
    if(S[X] < 0 )//找到集合的根，X如果小于0的话那就意味着X本身就已经是根了，直接返回就行了
        return X;
    else
        return S[X] = Find( S,S[X]);//先找到根，把根变为X的父结点，再返回根
}
```

<img src=".\数据结构-image\image-20220710103232692.png" alt="image-20220710103232692" style="zoom:50%;" />

<img src=".\数据结构-image\image-20220710103259091.png" alt="image-20220710103259091" style="zoom:50%;" />

路径压缩好处：

1. 第一次调用find会稍微复杂一点，但只要后续还需要调用就会非常合算
2. Find函数的递归调用是一个伪递归调用(不会像递归一样把堆栈压爆掉，因为是非常容易转换成循环的，编辑器可能直接执行优化过后的循环代码)

<img src=".\数据结构-image\image-20220710104353745.png" alt="image-20220710104353745" style="zoom:50%;" />

做不做路径压缩的本质区别：

1. 在查找次数M前面，到底是要乘一个常数还是要乘一个logN的问题。logN是N的一个递增函数，当N趋向无穷大的时候，logN也会趋向于无穷大。常数是不会变的
2. 当N充分大的时候

