# 数据结构第四周笔记——树(中)(慕课浙大版本--XiaoYu)

> 笔记出处：GitHub地址：[2002XiaoYu (小余) (github.com)](https://github.com/2002XiaoYu)，作者：小余
>
> 笔记持续更新中，有需要的自行获取，提供markdown跟PDF两种格式，在线浏览如果受到不可控影响无法登录GitHub可以查看我掘金账号的专栏对应的文章(目前在陆续上传中)
>
> 如果对你有所帮助，给作者一个star就是最好的鼓励，作者会持续产出的

## 4.1 二叉搜索树

### 4.1.1 二叉搜索树及查找

```
查找问题：
	1.静态查找与动态查找
	2.针对动态查找，数据如何组织
	
	什么是二叉查找树：直接把元素放在树上，不要放在数组里面。
	好处：树的动态性比较强，要插入删除比在线性里面做要方便
```

#### 二叉搜索树(BST)

```
BST=>Binary Search Tree

也称为二叉排序树或者二叉查找树

二叉搜索树：一颗二叉树，可以为空;如果不为空，满足以下性质：
1.非空左子树的所有键值小于其根结点的键值。
2.非空右子树的所有键值大于其根结点单独键值
3.左、右子树都是二叉搜索树
```

<img src="./数据结构-image\image-20220702012825394.png" alt="image-20220702012825394" style="zoom:50%;" />

<img src="./数据结构-image\image-20220702012726729.png" alt="image-20220702012726729" style="zoom:50%;" />

#### 二叉搜索树操作的特别函数：

<img src="./数据结构-image\image-20220702012904895.png" alt="image-20220702012904895" style="zoom:50%;" />

#### 插入(新结点x)删除(x这个结点)

<img src="./数据结构-image\image-20220702013012616.png" alt="image-20220702013012616" style="zoom:50%;" />

#### 二叉搜索树的查找操作：Find

```
查找从根结点开始，如果树为空，返回NULL

若搜索树非空，则根结点关键字和X进行比较，并进行不同处理：
	1.若X小于根结点赋值，只需在左子树中继续搜索;
	2.如果X大于根结点的键值，在右子树中进行继续搜索;
	3.若两者比较结果是相等，搜索完成，返回指向此结点的指针
```

```c
代码实现
Position Find(ElementType X,BinTree BST)
{
	if(!BST) return NULL;//查找失败
    if( X > BST -> Data)//这是尾递归，下面的两个Find的也是同理
        return Find(X,BST->Right);//在右子树中继续查找
    Else if(X < BST -> Data)
        return Find(X,BST->Left);//在左子树中继续查找
    else//X == BST->Data
        return BST;//查找成功，返回结点的找到结点的地址
}


//由于非递归函数的执行效率高，可将"尾递归"函数改为迭代函数
Position IterFind(ElementType X,BinTree BST)
{
	while(BST){
        if(X > BST->Data)
            BST = BST -> Right;//向右子树中移动，继续查找
        else if(X < BST->Data)
            BST = BST ->Left;//向左子树中移动，继续查找
        else//X == BST ->Data
            return BST;//查找成功，返回结点的找到结点的地址
    }
        return NULL;//查找失败
}

//查找的效率决定于树的高度
```

#### 查找最大和最小元素

```
1.最大元素一定是在树的最右分枝的端结点上
2.最小元素一定是在树的最左分枝的端结点上

对于搜索树的最大元素结点,下面哪个说法是正确的?
A.
一定是叶结点
B.
一定没有左儿子
C.
一定没有右儿子
D.
是后序遍历的最后一个结点
答案是：C
```

##### 查找最小元素的递归函数

```c
Position FindMin( BinTree BST)
{
    if(!BST) return NLULL;//空的二叉树，返回NULL
    else if(!BST->Left)
        return BST;//找到最左结点并返回
    else
        return FindMin(BST->Left);//沿左分支继续查找
}
```

##### 查找最大元素的迭代函数

```c
Position FindMax( BinTree BST)
{
	if( BST )
        while( BST -> Right ) BST = BST->Right;//沿右分支继续查找，直到最右叶结点
    return BST;
}
```

### 4.1.2 二叉搜索树的插入

【分析】关键是要找到元素应该插入的位置，可以采用与Find类似的方法

<img src="./数据结构-image\image-20220702015511291.png" alt="image-20220702015511291" style="zoom:50%;" />

#### 二叉搜索树的插入算法

```C
BinTree Insert(ElementType X,BinTree BST)
{
	if(!BST){
        //若原树为空，生成并返回一个结点的二叉搜索树
        BST = malloc(sizeof(struct TreeNode));
        BST -> Data = X;
        BST -> Left = BST -> Right = NULL;
    }else //开始找要插入元素的位置
        if(X < BST->Data )
            BST -> Left = Insert(X,BST->Left);//递归插入左子树
    else if(X > BST->Data)
        BST->Right = Insert(X,BST->Right);//递归插入右子树
    //else X已经存在，什么都不做
    return BST;
}
```

<img src="./数据结构-image\image-20220702021901357.png" alt="image-20220702021901357" style="zoom:50%;" />

<img src="./数据结构-image\image-20220702021921682.png" alt="image-20220702021921682" style="zoom:50%;" />



### 4.1.3 二叉搜索树的删除

**考虑三种情况**：

1. 要删除的是叶结点：直接删除，并再修改其父节点指针---置为NULL

2. 要删除的结点只有一个孩子结点：

   1. 将其父节点的指针指向要删除结点的孩子结点
   2. <img src="./数据结构-image\image-20220702025452840.png" alt="image-20220702025452840" style="zoom:50%;" />
   3. <img src="./数据结构-image\image-20220702025522322.png" alt="image-20220702025522322" style="zoom:50%;" />

3. 要删除的结点有左、右两颗子树：

   1. 用另一结点代替被删除结点：右子树的最小元素或者左子树的最大元素
   2. <img src="./数据结构-image\image-20220702025716832.png" alt="image-20220702025716832" style="zoom:50%;" /> 取右子树中的最小元素替代
   3. <img src="./数据结构-image\image-20220702025939253.png" alt="image-20220702025939253" style="zoom:50%;" />
   4. <img src="./数据结构-image\image-20220702030039570.png" alt="image-20220702030039570" style="zoom:50%;" />
   5. 代码实现

   ```c
   BinTree Delete (ElementType X,BinTree BST)
   {
   	Position Tmp;   
       if(!BST) printf("要删除的元素未找到");
       else if(X < BST ->Data)
           BST->Left = Delete(X,BST->Left);//左子树递归删除,返回左子树删除了x这个结点之后，新的左子树根结点的地址
       else if(X > BST->Data)
           BST->Right = Delete( X,BST->Right);//右子树递归删除
       else//找到要删除的结点
           if(BST->Left && BST->Right ){//被删除结点有左右两个子节点
               Tmp = FindMin(BST->Right);//在右子树中找最小的元素填充删除结点
               BST->Data = Tmp->Data;
               BST->Right = Delete(BST->Data,BST->Right);//在删除结点的右子树中删除最小元素
           }else{//被删除结点有一个或无子结点
               Tmp = BST;
               if(!BST->Left)//有右孩子或无子结点
                   BST = BST->Right;
               else if(!BST->Left)//有左孩子或无子结点
               	BST = BST->Left;
               free(Tmp);
           }
   }
   return BST;
   ```

   

## 4.2 平衡二叉树

### 4.2.1 什么是平衡二叉树

<img src="./数据结构-image\image-20220702032658211.png" alt="image-20220702032658211" style="zoom:50%;" />

怎么样子算基本上平衡：1.左右结点差不多2.左右高度差不多

#### **平衡因子**：

<img src="./数据结构-image\image-20220702032941579.png" alt="image-20220702032941579" style="zoom:50%;" />

平衡因子是对结点来说的，左右的一个高度差我们就称为平衡因子

```
平衡二叉树(Balanced Binary Tree)(AVL树) AVL是提出这个的科学家名字的第一个字母
空树，或者任一结点左、右子树高度差的绝对值不超过1，即|BF(T)|<=1
```

<img src="./数据结构-image\image-20220702040217888.png" alt="image-20220702040217888" style="zoom:50%;" />

<img src="./数据结构-image\image-20220702040552776.png" alt="image-20220702040552776" style="zoom:50%;" />

画画看，至少需要多少个结点才能构造出一棵4层（h=3)的平衡二叉树？7个

<img src="./数据结构-image\image-20220702041010879.png" alt="image-20220702041010879" style="zoom:50%;" />

<img src="./数据结构-image\image-20220702041413070.png" alt="image-20220702041413070" style="zoom:50%;" />



### 4.2.2 平衡二叉树的调整

#### RR旋转

<img src="./数据结构-image\image-20220702050945421.png" alt="image-20220702050945421" style="zoom:50%;" />

 <img src="./数据结构-image\image-20220702051127034.png" alt="image-20220702051127034" style="zoom:50%;" />

<img src="./数据结构-image\image-20220702051214039.png" alt="image-20220702051214039" style="zoom:50%;" />

#### LL旋转

<img src="./数据结构-image\image-20220702051315347.png" alt="image-20220702051315347" style="zoom:50%;" />

<img src="./数据结构-image\image-20220702051544173.png" alt="image-20220702051544173" style="zoom:50%;" />

#### LR旋转

<img src="./数据结构-image\image-20220702051645639.png" alt="image-20220702051645639" style="zoom:50%;" />

<img src="./数据结构-image\image-20220702051833406.png" alt="image-20220702051833406" style="zoom:50%;" />

<img src="./数据结构-image\image-20220702052115268.png" alt="image-20220702052115268" style="zoom:50%;" />

#### RL旋转

<img src="./数据结构-image\image-20220702052247419.png" alt="image-20220702052247419" style="zoom:50%;" />

<img src="./数据结构-image\image-20220702052510229.png" alt="image-20220702052510229" style="zoom:50%;" />



### 小测试

<img src="./数据结构-image\image-20220702042025929.png" alt="image-20220702042025929" style="zoom:50%;" />

## 小白专场：是否同一棵二叉搜索树 - C实现

### 题意理解及搜索树表示

#### 是否是同一棵二叉搜索树

求解思路

两个序列是否对应相同搜索树的判别

1. **分别建两棵搜索树的判别方法**：根据两个序列分别建树，再判别树是否一样

2. **不建树的判别方式**

   1. <img src="./数据结构-image\image-20220702053319839.png" alt="image-20220702053319839" style="zoom:50%;" />
   2. 先看根结点也就是第一个数一样吗？再把比根结点小的放左边(顺序不动)，比根结点大的放右边(顺序不动)，然后再进行对比左右树，上图就是一样的，下图为反面例子
   3. <img src="./数据结构-image\image-20220702053522047.png" alt="image-20220702053522047" style="zoom:50%;" />

3. 建一颗树，再判别其他序列是否与该树一致

   1. 求解思路

   2. 搜索树表示

   3. 建搜索树T

   4. 判别一序列是否与搜索树T一致

   5. ```c
      搜索树表示
      typedef struct TreeNode*Tree;
      struct TreeNode{
          int v;//v来表示基本信息
          Tree Left,Right;
          int flag;//flag是一个阈(用来判别一个序列是不是跟树一致，实际效果就是如果这个结点没有被访问过flag设为0，被访问过了就设为1)
      };
      ```

      

### 程序框架及建树

#### 程序框架搭建

```c
int main()
{
    对每组数据
        1.读入N和L
        2.根据第一行序列建树T
        3.依据树T分别判别后面的L个序列是否能与T形成同一搜索树并输出结果//分别读入L个序列来跟T做比较，看是不是一致的，如果说一致的，他所对应的搜索树是一样的，一样输出yes，不一样输出no
        
        return 0;
}
根据上方框架，我们需要设计的主要函数：
    1.读数据建搜索树T//读入N个数据来建我们的搜索树，将来所有序列都会跟这个T去比较，所以以T基准
    2.判别一序列是否与T构成一样的搜索树
    int main()
{
    int N,L,i;
    Tree T;
    
    scanf("%d",&N);
    while(N){//N如果为0输出就结束了
        scanf("%d",&L);
        T = Make Tree(N);//读入后面N个数来建我们的树T
        for(i = 0; i < L;i++ ){//比较是不是跟T一致的这样的序列
            if(Judge(T,N))printf("Yes\n");//用函数Judge读入后面的N个数然后跟T去做比较，所以这样Judge有两个参数，一个是树T，一个是序列的个数。通过Judge来判别，如果一致就print yes 反之print no
            else printf("No\n");
            ResetT(T);//清除T中的标记flag
        }
        Free Tree(T);//释放掉树占有的空间，因为已经处理完了准备处理下一组数据了，下一组数据需要的空间可能不需要这么多也可能远远不够
        scanf("%d",&N);
    }
    return 0;
}
```

#### 如何建搜索树

```C
Tree Make Tree(int N)
{
	Tree T;
    int i,V;
    
    scanf("%d",&V);//读入第一个数，然后把数放到V里面去
    T = NewNode(V);//为V构造一个对应的节点，然后赋给T，T只含一个节点
    for(i=1;i<N;i++){//这里是循环N-1次噢
        scanf("%d",&V);
        T = Insert(T,V);//将V一个个插到T里面去
    }
    return T;
}

Tree NewNode(int V)
{
    Tree T = (Tree)malloc(sizeof(struct TreeNode));
    T->v = V;
    T->Left = T->Right = NULL;
    T->flag = 0;
    return T;
}

怎么把树插到T里面去呢？引用insert这个函数
    Tree Insert(Tree T, int V)
{
    if(!T)T = NewNode(V);//如果T空了，那就调用NewNode来产生这样一个节点
    else{
        if(V > T->v)//如果不空就做比较
            T->Right = Insert(T->Right,V);
        else
            T->Left = Insert(T->Left,V);
    }
    return T;
}
```



### 搜索树是否一样的判别

<img src="./数据结构-image\image-20220706083408133.png" alt="image-20220706083408133" style="zoom:50%;" />

```c
方法:在树T中按顺序搜索序列
	1.如果每次搜索所经过的结点在前面均出现过，则一致
	2.否则(某次搜索中遇到前面未出现的结点)，则不一致
    去在T中搜索我们序列中的每一个整数，实际上就是个查找过程
    int check(Tree T,int V)
{
    if(T->flag){
        if(V<T->v)return check(T->Left,V);
        else if(V->T->v)return check(T->Right,V);
        else return 0;
    }
    else{
        if(V == T->v ){
            T->flag = 1;
            return 1;
        }
        else return 0;
    }
}
```

#### 如何辨别

```c
//有bug版本
int Judge(Tree T,int N)
{
    int i,V;
    scanf("%d",&V);
    if(V!=T->v)return 0;
    else T->flag = 1;
    
    for(i=1;i<N;i++){
        scanf("%d",&V);
        if(!check(T,V))return 0;
    }
    return 1;
}
//当发现序列中的某个数与T不一致的时候，必须把序列后面的数都读完。这样才能保证我们下一个要求的序列是我们真正想要的序列 ，这个有bug的程序就是不能把后面的数都读完
以下是修改版本，对整个程序的一个标记而不是对结点的标记
    int Judge(Tree T,int N)
{
    int i,V,flag = 0;//flag：0代表目前还一致，1代表已经不一致
    scanf("%d",&V);
    if(V!=T->v)flag = 1;
    else T->flag = 1;//T->flag是结点上的标记，flag是整个的标记，是不一样的
    for(i = 1;i < N;i++){
        scanf("%d",&V);
        if((!flag)&&(!check(T,V)))flag = 1;
    }
    if(flag)return 0;
    else return 1; 
}

void ResertT(Tree T)//清除T中各结点的flag标记
    {
    if(T->Left) ResetT(T->Left);
    if(T->Right) ResetT(T->Right);
    T->flag = 0;
}

void FreeTree(Tree T)//释放T的空间
{
    if(T->Left) FreeTree(T->Left);
    if(T->Right)FreeTree(T->Right);
    free(T);//递归方法
}
```



## 线性结构之习题讲选[陈越]：Reversing Linked List

### 线性结构习题1：什么是抽象的链表

**链表是一个抽象的数据结构**

1. 有块地方存数据
2. 有块地方存指针——下一个结点的地址(一个抽象的指针指向的就是地址，任何一种形式去存了下一个结点的位置这东西就叫做指针)
3. <img src="./数据结构-image\image-20220706093854900.png" alt="image-20220706093854900" style="zoom:50%;" />
4. <img src="./数据结构-image\image-20220706094019412.png" alt="image-20220706094019412" style="zoom:50%;" />
5. 

### 线性结构习题2：链表逆转算法

#### 单链表的逆转

<img src="./数据结构-image\image-20220706094116841.png" alt="image-20220706094116841" style="zoom:50%;" />

第一个加上头结点虽然会浪费一个空间，但是会使后面的操作变为更加简单

**逆序之后如下：**

<img src="./数据结构-image\image-20220706094241649.png" alt="image-20220706094241649" style="zoom:50%;" />

接下来是逆序的过程：

1. 首先需要对图中定义的词汇进行解释

   1. new：指的是新的已经逆转好的链表，他的头结点的位置
   2. old： 指的是旧的还没有逆转好的老链表，他的头结点位置

2. 我们首先想要把2跟1进行逆转，但是在逆转之前需要先把3这个位置记住，所以设定一个tmp指针去指向3。否则当2的指针一转向，2后面的链表就丢失了

   1. <img src="./数据结构-image\image-20220706095027756.png" alt="image-20220706095027756" style="zoom:50%;" />

   2. <img src="./数据结构-image\image-20220706095101249.png" alt="image-20220706095101249" style="zoom:50%;" />

   3. <img src="./数据结构-image\image-20220706095134083.png" alt="image-20220706095134083" style="zoom:50%;" />

   4. <img src="./数据结构-image\image-20220706095200258.png" alt="image-20220706095200258" style="zoom:50%;" />

   5. ```c
      以下是伪代码实现
      Ptr Reverse(Ptr head,)//head是一个指针
      {
          cnt = 1
          new = head->next;//刚开始指向1的
          old = new->next;//还没有逆转的那个头结点
          while(cnt < K){
              tmp = old->next;//记住下一个的位置
              old->next = new;//指针逆转指向新的结点 
              new = old;old = tmp;//往前位移一段
              cnt++;
          }
          head->next->next = old;//把开头那个空结点指向结尾的(因为逆转了，最开头的现在变为最末尾的了)
          return new;
      }
      //取巧：用顺序表存储，先排序，再直接逆序输出
      ```

      

### 线性结构习题3：测试数据

1. 有尾巴不反转
2. 地址取到上下界
3. 正好全反转
4. K = N是全反转
5. K = 1不用反转
6. 最大(最后剩K-1不反转)、最小N
7. 有多余结点

#### 2-6是属于边界测试

1. 边界测试
   1. 意思就是给你一个数值，你就一定要取到那个范围上界和下界