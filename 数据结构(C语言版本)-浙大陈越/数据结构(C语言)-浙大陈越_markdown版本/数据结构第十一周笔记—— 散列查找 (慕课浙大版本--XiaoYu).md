# 数据结构第十一周笔记—— 散列查找 (慕课浙大版本--XiaoYu)

> 笔记出处：GitHub地址：[2002XiaoYu (小余) (github.com)](https://github.com/2002XiaoYu)，作者：小余
>
> 笔记持续更新中，有需要的自行获取，提供markdown跟PDF两种格式，在线浏览如果受到不可控影响无法登录GitHub可以查看我掘金账号的专栏对应的文章(目前在陆续上传中)
>
> 如果对你有所帮助，给作者一个star就是最好的鼓励，作者会持续产出的

## 11.1 散列表

### 11.1.1 引子：散列的基本思路

C语言变量名必须：

1.  先定义(或者声明)后使用

编译处理时，涉及变量及属性(如：变量类型)的管理：

1. 插入：新变量定义(将变量名及其定义插到我们要管理的这个集合里面去)
2. 查找：变量的引用(在编译的时候，会查找你使用的这个变量是否定义，在根据变量的类型去进行判别看能不能这么使用)

编译处理中对变量管理：

1. 动态查找问题

利用查找树(搜索树)进行变量管理？

1. 两个变量名(字符串)比较效率不高，因为变量名比较比整数的比较要复杂。
2. 整数比较一次比较大小相等就行了，而变量是字符串，需要一个字符一个字符的比过去
3. 是否可以先把字符串转换为数字再处理？这样就是进行比较两个数字的方式了，这就是散列查找

已知的几种查找方法：

1. 顺序查找：要查找的放数组列表，从头到尾慢慢找，效率差
2. 二分查找(静态查找)：从小到大排好然后用二分查找这样，查找效率高
3. 二叉搜索树，平衡二叉树  

<img src=".\数据结构-image\image-20220824223055677.png" alt="image-20220824223055677" style="zoom:50%;" />

问题：如何快速搜索到需要的关键词？如果关键词不方便比较怎么办？

1. 查找的本质：已知对象找位置
   1. 有序安排对象：全序(典型的二分查找，从小到大排好)，半序(某些关键词之间存在一个秩序，例如查找树，任何一个结点都比左边左子树的所有结点要来得大，比右边右子树的所有结点要来得小，但并不像二分查找一样完全有序)
   2. 直接"算出"对象位置：散列 
2. 散列查找法的两项基本工作：
   1. 计算位置：构造散列函数确定关键词存储位置
   2. 解决冲突：应用某种策略解决多个关键词位置相同的问题
3. 时间复杂度几乎是常量：O(1)，即查找时间与问题规模无关！
   1. 散列如果在函数的计算方面还有冲突策略提得好，效率就会非常高

### 11.1.2 什么是散列表

```C
类型名称：符号表(SymbolTable)
数据对象集：符号表是“名字(Name)-属性(Attribute)”对的集合。
操作集：Table∈SymbolTable,Name∈NameType,Attr∈AttributeType
1.SymbolTable InitializeTable(int TableSize )://表的初始化
	创建一个长度为TableSize的符号表；
2.Boolean IsIn(SymbolTable Table,NameType Name)://判别一个对象是不是在这个表里
	查找特定的名字Name是否在符号表Table中；
3.AttributeType Find(SymbolTable Table,NameType Name)://在表里查找属性
	获取Table中指定名字Name对应的属性；
4.SymbolTable Modefy(SymbolTable Table,NameType Name,AttributeType Attr)：//把表中属性改掉
	将Table中指定名字Name的属性修改为Attr;
5.SymbolTable Insert(SymbolTable Table,NameType Name,AttributeType Attr)://在表里插入一个新的对象
	向Table中插入一个新名字Name及其属性Attr;
6、SymbolTable Delete(SymbolTable Table,NameType Name)://从表里删除
	从Table中删除一个名字Name及其属性。
        
        主要操作：上面的3、5、6
```

<img src=".\数据结构-image\image-20220824234520780.png" alt="image-20220824234520780" style="zoom:50%;" />

装填因子：散列表在外面这里是个数组，这个数组被充满的程度



冲突的设计：采用二维数组，同一个地址的就放在同一行，有冲突的在所在那行后一列继续放，如图所示：

<img src=".\数据结构-image\image-20220824235827460.png" alt="image-20220824235827460" style="zoom:33%;" />

哈希函数设计：

```
哈希函数指将哈希表中元素的关键键值映射为元素存储位置的函数。
一般的线性表，树中，记录在结构中的相对位置是随机的，即和记录的关键字之间不存在确定的关系，因此，在结构中查找记录时需进行一系列和关键字的比较。这一类查找方法建立在“比较“的基础上，查找的效率依赖于查找过程中所进行的比较次数。 理想的情况是能直接找到需要的记录，因此必须在记录的存储位置和它的关键字之间建立一个确定的对应关系，使每个关键字和结构中一个唯一的存储位置相对应。
```

只要不冲突的话效率还是很高的

<img src=".\数据结构-image\image-20220825000238029.png" alt="image-20220825000238029" style="zoom:50%;" />

```
▣“散列(Hashing)”的基本思想是：
(1)以关键字key为自变量，通过一个确定的函数h(散列函数)，计算出对应的函数值h(key),作为数据对象的存储地址。
(2)可能不同的关键字会映射到同一个散列地址上，即h(keyi）=h(keyj）(当keyi不等于keyj),称为“冲突(Collision)”。---需要某种冲突解决策略
```

## 11.2 散列函数的构造方法

### 11.2.1 数字关键词的散列函数构造

一个"好"的散列函数一般应考虑下列两个因素：

1. 计算简单，以便提高转换速度
2. 关键词对应的地址空间分布均匀，以尽量减少冲突

数字关键词的散列函数构造

1. 直接定址法：取关键词的某个线性函数值为散列地址，即h(key) = a×key+b (a、b为常数)<img src=".\数据结构-image\image-20220825132959714.png" alt="image-20220825132959714" style="zoom:50%;" />
2. 除留余数法(常用)：//其实就是把一个大的整数把它转换成一个小的整数，这个小的整数就相当于散列表里面的地址
   1. 散列函数为：h(key) = key mod p<img src=".\数据结构-image\image-20220825133843470.png" alt="image-20220825133843470" style="zoom:50%;" />
3. 数字分析法：分析数字关键字在各位上的变化情况，取比较随机的位作为散列地址
   1. 比如：取11位收集号码key的后4位作为地址：散列函数为：h(key)=atoi(key+7)	(char*key)<img src=".\数据结构-image\image-20220825135138135.png" alt="image-20220825135138135" style="zoom:33%;" />
   2. int atoi(char*s)：将类似"5678"的字符串转换为整数5678
   3. 如果关键词key是18位的身份证号码：<img src=".\数据结构-image\image-20220825135313221.png" alt="image-20220825135313221" style="zoom:50%;" />
   4. <img src=".\数据结构-image\image-20220825135441040.png" alt="image-20220825135441040" style="zoom:50%;" />
4. 折叠法：把关键词分割成位数相同的几个部分，然后叠加<img src=".\数据结构-image\image-20220825140542094.png" alt="image-20220825140542094" style="zoom:50%;" />
5. 平方取中法：<img src=".\数据结构-image\image-20220825140630018.png" alt="image-20220825140630018" style="zoom:50%;" />

如果仅改变56793542的最后一位，观察散列值会有什么变化（原散列值为641）。

请计算一下，按照平方取中法,key为56793543的散列值是多少=652

### 11.2.2 字符串关键词的散列函数构造

**字符关键词的散列函数构造**

1. 一个简单的散列函数——ASCII码加和法(ASCII码相加在求个余数)
   1. <img src=".\数据结构-image\image-20220826005023076.png" alt="image-20220826005023076" style="zoom:50%;" />
   2. 假如每个字符的变化范围在0-127，我们把它∑起来，∑后的值在0-1270之间，而变量名实际上的这种变化是非常多的，如果以很窄的计算结果来对付一个很广的这个变量范围就会使得这样的一个哈希函数很容易产生聚集，所以这不是一个很好的办法
2. 简单的改进——前3个字符移位法
   1. h(key)=(key[0] x 27² + key[1] x 27 + key[2])mod TableSize
   2. 这种方法仍然会产生冲突：string、street、strong、structure等等(前三位是一样的)；空间浪费：3000/26³≈30
   3. 空间浪费原因：字符一共有26种变化，所以三个字符的变化一共就26的三次方。而前三位一般出现的情况是3000种，而我们实际上考虑到26³去了，通过上面可以知道浪费的空间足足有30倍
3. 好的散列函数——移位法
   1. <img src=".\数据结构-image\image-20220826101017287.png" alt="image-20220826101017287" style="zoom:50%;" />
   2. 将数值巨大化，采用32进制相乘<img src=".\数据结构-image\image-20220826101121601.png" alt="image-20220826101121601" style="zoom:30%;" />
   3. 优化方法：(a*32+b) =>((a*32+b)*32+c)=>(((a*32+b)*32+c)*32+d)

```
如果直接计算'a'*32^4+'b'*32^3+'c'*32^2+'d'*32+'e'所需要的乘法总次数是4+3+2+1=10次。

采用 ((('a'*32+'b')*32+'c')*32+'d')*32+'e'的计算方法，乘法总次数是多少？

（顺便思考一下两者时间效率的差别）4
```

```c
Index Hash(const char*Key,int TableSize)
{
    unsigned int h = 0;//散列函数值，初始化为0
    while(*Key != ‘\0’)//位移映射，这里就是值不为空的意思 
        h = ( h << 5 ) + *Key++;//h << 5是左移五位的意思
    return h % TableSize;//最后求余得到地址
}
```

## 11.3 冲突处理方法

### 11.3.1 开放定址法

**处理冲突的方法**

> 常用冲突的思路：
>
> 1. 换个位置：开放地址法
> 2. 同一位置的冲突对象组织在一起：链地址法

开放地址法(Open Addressing)

1. 一旦产生了冲突(该地址已有其他元素)，就按某种规则去寻找另一空地址
2. <img src=".\数据结构-image\image-20220826103852201.png" alt="image-20220826103852201" style="zoom:50%;" />
3. di决定了不同的解决冲突方案：线性探测、平方探测、双散列(三种典型的开放地址法)
   1. 线性探测：di = i(di的i是下标)，第一次探测i就是1，第二次探测再上次探测基础上加1，往后持续
   2. 平方探测：跟上方类似，只不过i变成了正负i²(在原来位置基础上偏移量升级层次，加一平方减一平方，加二平方减二平方...) 
   3. 双散列：两个散列函数，第一个散列函数作为他的最早即时找的这个位置，第二个散列函数用来计算偏移量

### 11.3.2 线性探测

> 线性探测法(Linear Probing)：以增量序列1，2，.....，(TableSize-1)循环试探下一个存储地址

<img src=".\数据结构-image\image-20220826122157322.png" alt="image-20220826122157322" style="zoom:50%;" />

<img src=".\数据结构-image\image-20220826122945504.png" alt="image-20220826122945504" style="zoom:50%;" />

会形成聚集现象

如果按照与刚才例子输入相反的顺序插入各个元素，这些元素在散列表中的位置还是一样的？不一样

**散列表查找性能分析**

> 1. 成功平均查找长度(ASLs) => 就是我要找的对象最后被我找到了
> 2. 不成功平均查找长度(ASLu) =>找对象找不着
>
> 什么是成功的 => 在散列表里找到的是成功的，不在散列表的元素就是不成功的

<img src=".\数据结构-image\image-20220826133953107.png" alt="image-20220826133953107" style="zoom:50%;" />

ASL u = 值(值取决于哈希余数)

余数为0要比较3次，为1要比较2次，余数为2要比较1次，余数为3的时候要比较2次，余数为4、5、6都是比较一次就够了，余数为7则要比较9次才能知道，余数为8则是8次

--------------

**余数-哈希函数**

1. 余数的思想
所谓余数，就是程序中取模运算中的“模”。
余数具有一个非常重要的特性：可以将无限的数据归一到有限的范围（余数总是小于除数）

```
你知道，整数是没有边界的，它可能是正无穷，也可能是负无穷。但是余数却可以通过某一种关系，让整数处于一个确定的边界内。我想这也是人类发明星期或者礼拜的初衷吧，任你时光变迁，我都是以 7 天为一个周期，“周”而复始地过着确定的生活。因为从星期的角度看，不管你是哪一天，都会落到星期一到星期日的某一天里。
```

2. 同余定理
在上边的例子中，第一天与第八天都是周一，第二天与第九天都是周二，即

```
1%7=8%7=1
2%7=9%7=2
```

这就引出了余数的另一个重要概念：同余定理

```
口语化表述：
两个整数 a 和 b，如果它们除以正整数 m 得到的余数相等，我们就可以说 a 和 b 对于模 m 同余
```

其实，奇数与偶数的确定就是同余定理的应用。将一个数字模2，得0为偶数，得1为奇数
复杂算法拆解后的原理并不一定复杂，同余定理也可以作为有用的应用，就是哈希函数

3. **哈希函数（散列函数）**

将任意长度的输入，通过哈希算法，压缩为某一固定长度的输出，所得存储位置为散列地址

散列过程

```
（1）存储记录时，通过散列函数记录散列地址，按地址存储记录
（2）查找记录时，通过同样的散列函数计算记录的散列地址，按散列地址访问记录
```

散列技术通过散列函数建立了从记录的关键码集合到散列表的地址集合的一个映射，显然，会出现两个不同记录存放在同一位置的情况，这种现象称为冲突，此时相同位置的记录称为同义词

散列函数中最常采用的方案是除留余数法，其基本思想：

```
选择适当的正整数P，以关键码除以P的余数作为散列地址
通常P为小于或等于表长（最好接近）的最小质数或不包含小于 20 质因子的合数
```

### 11.3.3 线性探测—字符串的例子

> 前面5个都是没有冲突的，第六个冲突1次(查找次数变为2)，第七个冲突4次，最后一个冲突2次
>
> <img src=".\数据结构-image\image-20220826135742628.png" alt="image-20220826135742628" style="zoom:50%;" />
>
> 与例子相似,如果已知散列表的前8个位置有元素(但元素内容与例子不一样）而且后面18个位置也全是空位,那么平均不成功查找次数还是一样的吗？一样的

### 11.3.4 平方探测法(Quadratic Probing) 

> 也可以叫做：二次探测

<img src=".\数据结构-image\image-20220826143648650.png" alt="image-20220826143648650" style="zoom:50%;" />

<img src=".\数据结构-image\image-20220826145722062.png" alt="image-20220826145722062" style="zoom:50%;" />

------

<img src=".\数据结构-image\image-20220826150859973.png" alt="image-20220826150859973" style="zoom:50%;" />

2. 平方探测法(Quadratic Probing)
   1. 是否有空间，平方探测(二次探测)就能找得到？
   2. <img src=".\数据结构-image\image-20220827125834414.png" alt="image-20220827125834414" style="zoom:50%;" />

还有一种平方探测的方式是:![img](.\数据结构-image\1063130987053874834.jpeg)。也就是增量序列为![img](.\数据结构-image\2446580497586752872.jpeg)。

如果采用前面讲的![img](.\数据结构-image\6630549895721824529.jpeg)增量序列找不到空位置，意味着采用![img](.\数据结构-image\1063130987053874834.jpeg)的增量序列也一定找不到空位置。

**线性探测的缺陷**：容易聚集，二次探测虽然也有但不严重

>有定理显示：如果散列表长度TableSize是某个4k+3(k是正整数)形式的素数时，平方探测法就可以探测到整个散列表空间

### 11.3.5 平方探测法的实现

```c
typedef struct HashTbl*HashTable;
struct HashTbl{
    int TableSize;//当前表的实际大小
    Cell*TheCells;//代表自己是一个数组，实际上是一个指针
}H;

HanshTable InitializeTable(int TableSize)
{
    HashTable H;
    int i;
    if( TableSize < MinTableSize ){//判别散列表的大小，太小就没必要做散列，直接放在数组就行了
        Error("散列表太小");
        return NULL;
    }
    //分配散列表
    H = (HashTable)malloc(sizeof(struct HashTbl));//申请空间赋给H
    if( H == NULL )
        FatalError("空间溢出!!!");//判断有没有申请成功
    H->TableSize = NextPrime(TableSize);//申请成功的话希望表的size是素数，NextPrime就是这个目的，产生一个比表大一点的素数
    //分配散列表Cells
    H->TheCells=(Cell*)malloc(sizeof(Cell)*H->TableSize);//为真正的TableSize分配一个空间，就相当于指向一个数组了
    if(H->TheCells == NULL)
        FatalError("空间溢出!!!");
    for(i=0;i<H->TableSize;i++)
        H->TheCells[i].Info = Empty;
    return H;
}
```

> 友情小提示：typedef struct 的typedef是用来取别名的，比如上方 HashTbl 的别名就是H

<img src=".\数据结构-image\image-20220827132244014.png" alt="image-20220827132244014" style="zoom:50%;" />实际删除的元素不能真的从表中拿掉，不然查找的时候会有问题的。如果我们要删除可以先做个记号。这样在后续的查找与插入的好处有：首先在查找的时候碰到被删掉的元素就说这个位置他做了个记号被删掉了，我们就知道这还不是空位还可以继续找，如果真拿掉变成空位的话就会产生误判。然后插入的时候发现这个元素是被删掉了，他不是空位而是原来有元素占着，现在被删掉了，这个时候插入元素就可以来替代原来删掉的元素。这样我们插入删除的操作都可以做并且不影响我们的查找过程

```c
//表的初始化
Position Find(ElementType Key,HashTable H)//平方探测
{
    Position CurrentPos,NewPos;
    int CNum;//记录冲突次数
    CNum = 0;
    NewPos = CurrentPos = Hash(Key,H->TableSize);//要算哈希函数，所以先调用一个哈希函数。CurrentPos是我们值真正要放的位置 
    while(H->TheCells[NewPos].Info != Empty && H->TheCells[NewPos].Element != Key){//Info位置不空且Element值不等于我要找的Key，那就需要继续找，而循环的条件就是我们要找的位置，被别人占了但是不空
        //字符串类型的关键词需要strcmp函数
        if(++CNum % 2){//判断冲突的奇偶次
            NewPos = CurrentPos +(CNum+1)/2*(CNum+1)/2;//探测方法：在原来的位置上(CurrentPos，也就是最早的哈希函数值)加减i²获得新的地址。因为一会加一会减，所以需要在上方用上if来判别是奇数是偶来决定该加还是该减
        	while(NewPos >= H->TableSize)//上方NewPos加上后面的大小可能超出大于TableSize了，所以需要通过不断循环减去TableSize，一直到NewPos不大于他(不大于他就落在0-TableSize之间了)
                NewPos -= H->TableSize;
        }else{//如果是偶数就走这条路啦，减去一个i平方
            NewPos = CurrentPos - CNum/2*CNum/2;
            while(NewPos < 0)//跟上面那个while类似，为了不负到突破地板，需要将值拉回来
                NewPos += H->TableSize;
        }
    }
    return NewPos;
}
```

<img src=".\数据结构-image\image-20220827135442103.png" alt="image-20220827135442103" style="zoom:50%;" />将Cnum映射为i的平方。CNum**加**1除以2就是这个i的值。所以举个例子

>例子：1加1除以2为1，3加1除以2为2，5加1除以2为3
>
>如果是**减**少的话，4除2为2，6除2为3

```c
void Insert(ElementType Key,HashTable H)
{
    //插入操作
    Position Pos;
    Pos = Find(Key,H);//通过Find return出来一个position值
    if(H->TheCells[Pos].Info != Legitimate){//需要判断Pos的状态，如果Pos不属于被占用的状态，那我们这个元素就可以放进去(什么情况不是属于被别人占用：空位或者被删除了)
        //确认在此插入
        H->TheCells[Pos].Info = Legitimate;//将Info设为被我占用的状态，然后下一步将Key放进去
        H->TheCells[Pos].Element = Key;//字符串类型的关键词需要strcpy函数
    }
}
ps：在开放地址散列表中，删除操作要很小心。通常只能"懒惰删除"，即需要增加一个“删除标记(Deleted)”，而并不是真正删除它。以便查找时不会"断链"。其空间可以在下次插入时重用
```

#### 双散列探测法(Double Hashing)

><img src=".\数据结构-image\image-20220827152627815.png" alt="image-20220827152627815" style="zoom:50%;" />

#### 再散列(Rehashing)

1. 当散列表元素太多(即装填因子α太大)时，查找效率会下降(因为冲突在不断增加);  
   1. 怎么解决这个问题？扩大散列表。散列表扩大时，原有元素需要重新计算放置到新表中
   2. 实用最大装填因子一般取0.5 <= α <= 0.85(通常控制在0.5以内)
2. 当装填因子过大时，解决的方法是加倍扩大散列表，这个过程叫做"再散列(Rehashing)"

### 11.3.6 分离链接法(Separate Chaining)

> 将相应位置上有冲突的所有关键词存储在**同一单链表中**

^表示空指针

<img src=".\数据结构-image\image-20220827153508063.png" alt="image-20220827153508063" style="zoom:50%;" />

**链表实现**

```c
typedef struct ListNode*Position,*List;
struct ListNode{
    ElementType Element;
    Position Next;//把Next分量分给P，P是下方代码块的一个指针，指向单项链表的第一个元素
};
typedef struct HashTbl*HashTable;
struct HashTbl{
    int TableSize;
    List TheLists;
};
```

```c
Position Find(ElementType Key,HashTable H)//哈希表来表示
{
    Position P;
    int Pos;
    
    Pos = Hash(Key,H->TableSize);//初始散列位置，第一步算哈希函数值，得到散列函数散列地址，散列地址就代表他在这个数组里的位置
    P = H->TheLists[Pos].Next;//获得链表头，这个P就是上方代码块说的那个指针P，指向单项链表的第一个元素
    while(P != NULL && strcmp(P->Element,Key))//典型的遍历单项链表的循环，只要P不等于NULL，P所指向的这个元素跟我要找的这个元素不相等就一个个往后找，P的Next赋给P。意思就是只要P不空(后面还有元素)，那么循环就一直做，同时循环的另一个条件是当前的这个元素值不等于我要找的元素值，如果列表不空再找下一个，再下一个就P的Next赋给P
        //等循环退出来要么P空了，就return NULL(没找到)。要么就strcmp返回值等于0，等于0就相等了，那这个时候所在的这个节点就是我们找到了，也就是return P
        P = P->Next;
    return P;
}
```

<img src=".\数据结构-image\image-20220827154233888.png" alt="image-20220827154233888" style="zoom:50%;" />

#### 创建开放地址法的散列表

```c
#define MAXTABLESIZE 100000 /* 允许开辟的最大散列表长度 */
typedef int ElementType;    /* 关键词类型用整型 */
typedef int Index;          /* 散列地址类型 */
typedef Index Position;     /* 数据所在位置与散列地址是同一类型 */
/* 散列单元状态类型，分别对应：有合法元素、空单元、有已删除元素 */
typedef enum { Legitimate, Empty, Deleted } EntryType;

typedef struct HashEntry Cell; /* 散列表单元类型 */
struct HashEntry{
    ElementType Data; /* 存放元素 */
    EntryType Info;   /* 单元状态 */
};

typedef struct TblNode *HashTable; /* 散列表类型 */
struct TblNode {   /* 散列表结点定义 */
    int TableSize; /* 表的最大长度 */
    Cell *Cells;   /* 存放散列单元数据的数组 */
};

int NextPrime( int N )
{ /* 返回大于N且不超过MAXTABLESIZE的最小素数 */
    int i, p = (N%2)? N+2 : N+1; /*从大于N的下一个奇数开始 */

    while( p <= MAXTABLESIZE ) {
        for( i=(int)sqrt(p); i>2; i-- )
            if ( !(p%i) ) break; /* p不是素数 */
        if ( i==2 ) break; /* for正常结束，说明p是素数 */
        else  p += 2; /* 否则试探下一个奇数 */
    }
    return p;
}

HashTable CreateTable( int TableSize )
{
    HashTable H;
    int i;

    H = (HashTable)malloc(sizeof(struct TblNode));
    /* 保证散列表最大长度是素数 */
    H->TableSize = NextPrime(TableSize);
    /* 声明单元数组 */
    H->Cells = (Cell *)malloc(H->TableSize*sizeof(Cell));
    /* 初始化单元状态为“空单元” */
    for( i=0; i<H->TableSize; i++ )
        H->Cells[i].Info = Empty;

    return H;
}
```

#### 平方探测法的查找与插入

```c
Position Find( HashTable H, ElementType Key )
{
    Position CurrentPos, NewPos;
    int CNum = 0; /* 记录冲突次数 */

    NewPos = CurrentPos = Hash( Key, H->TableSize ); /* 初始散列位置 */
    /* 当该位置的单元非空，并且不是要找的元素时，发生冲突 */
    while( H->Cells[NewPos].Info!=Empty && H->Cells[NewPos].Data!=Key ) {
                                           /* 字符串类型的关键词需要 strcmp 函数!! */
        /* 统计1次冲突，并判断奇偶次 */
        if( ++CNum%2 ){ /* 奇数次冲突 */
            NewPos = CurrentPos + (CNum+1)*(CNum+1)/4; /* 增量为+[(CNum+1)/2]^2 */
            if ( NewPos >= H->TableSize )
                NewPos = NewPos % H->TableSize; /* 调整为合法地址 */
        }
        else { /* 偶数次冲突 */
            NewPos = CurrentPos - CNum*CNum/4; /* 增量为-(CNum/2)^2 */
            while( NewPos < 0 )
                NewPos += H->TableSize; /* 调整为合法地址 */
        }
    }
    return NewPos; /* 此时NewPos或者是Key的位置，或者是一个空单元的位置（表示找不到）*/
}

bool Insert( HashTable H, ElementType Key )
{
    Position Pos = Find( H, Key ); /* 先检查Key是否已经存在 */

    if( H->Cells[Pos].Info != Legitimate ) { /* 如果这个单元没有被占，说明Key可以插入在此 */
        H->Cells[Pos].Info = Legitimate;
        H->Cells[Pos].Data = Key;
        /*字符串类型的关键词需要 strcpy 函数!! */
        return true;
    }
    else {
        printf("键值已存在");
        return false;
    }
}
```

#### 分离链接法的散列表实现

```c
#define KEYLENGTH 15                   /* 关键词字符串的最大长度 */
typedef char ElementType[KEYLENGTH+1]; /* 关键词类型用字符串 */
typedef int Index;                     /* 散列地址类型 */
/******** 以下是单链表的定义 ********/
typedef struct LNode *PtrToLNode;
struct LNode {
    ElementType Data;
    PtrToLNode Next;
};
typedef PtrToLNode Position;
typedef PtrToLNode List;
/******** 以上是单链表的定义 ********/

typedef struct TblNode *HashTable; /* 散列表类型 */
struct TblNode {   /* 散列表结点定义 */
    int TableSize; /* 表的最大长度 */
    List Heads;    /* 指向链表头结点的数组 */
};

HashTable CreateTable( int TableSize )
{
    HashTable H;
    int i;

    H = (HashTable)malloc(sizeof(struct TblNode));
    /* 保证散列表最大长度是素数，具体见代码5.3 */
    H->TableSize = NextPrime(TableSize);

    /* 以下分配链表头结点数组 */
    H->Heads = (List)malloc(H->TableSize*sizeof(struct LNode));
    /* 初始化表头结点 */
    for( i=0; i<H->TableSize; i++ ) {
         H->Heads[i].Data[0] = '\0';
         H->Heads[i].Next = NULL;
    }

    return H;
}

Position Find( HashTable H, ElementType Key )
{
    Position P;
    Index Pos;
    
    Pos = Hash( Key, H->TableSize ); /* 初始散列位置 */
    P = H->Heads[Pos].Next; /* 从该链表的第1个结点开始 */
    /* 当未到表尾，并且Key未找到时 */ 
    while( P && strcmp(P->Data, Key) )
        P = P->Next;

    return P; /* 此时P或者指向找到的结点，或者为NULL */
}

bool Insert( HashTable H, ElementType Key )
{
    Position P, NewCell;
    Index Pos;
    
    P = Find( H, Key );
    if ( !P ) { /* 关键词未找到，可以插入 */
        NewCell = (Position)malloc(sizeof(struct LNode));
        strcpy(NewCell->Data, Key);
        Pos = Hash( Key, H->TableSize ); /* 初始散列位置 */
        /* 将NewCell插入为H->Heads[Pos]链表的第1个结点 */
        NewCell->Next = H->Heads[Pos].Next;
        H->Heads[Pos].Next = NewCell; 
        return true;
    }
    else { /* 关键词已存在 */
        printf("键值已存在");
        return false;
    }
}

void DestroyTable( HashTable H )
{
    int i;
    Position P, Tmp;
    
    /* 释放每个链表的结点 */
    for( i=0; i<H->TableSize; i++ ) {
        P = H->Heads[i].Next;
        while( P ) {
            Tmp = P->Next;
            free( P );
            P = Tmp;
        }
    }
    free( H->Heads ); /* 释放头结点数组 */
    free( H );        /* 释放散列表结点 */
}
```

### 11.4 散列表的性能分析

> 1. 平均查找长度(ASL)用来度量散列表查找效率：成功、不成功
>    1. 成功：查找的元素再散列表里
>    2. 不成功：查找的元素不在散列表里
> 2. 关键词的比较次数，取决于产生冲突的多少。影响产生充裕多少有以下三个因素：
>    1. 散列函数是否均匀(不均匀的话冲突会多，性能就会差)
>    2. 处理冲突的方法
>    3. 散列表的装填因子α(装了多少元素，装的元素少那么冲突少。装的元素多则冲突多)

分析：不同冲突处理方法、装填因子对效率的影响

1. 线性探测法的查找性能

   可以证明，线性探测法的期望探测次数满足下列公式：<img src=".\数据结构-image\image-20220827214026405.png" alt="image-20220827214026405" style="zoom:50%;" />

<img src=".\数据结构-image\image-20220827214455451.png" alt="image-20220827214455451" style="zoom:50%;" />

对于线性探测，如果当前装填因子值为0.654321, 此时不成功情况下的期望探测次数小于成功情况下的期望探测次数。错误

2. 平方 探测法和双散列探测法的查找性能

   可以证明，平方探测法和双散列探测法探测次数 满足下列公式：<img src=".\数据结构-image\image-20220827214621567.png" alt="image-20220827214621567" style="zoom:50%;" />

   <img src=".\数据结构-image\image-20220828210649498.png" alt="image-20220828210649498" style="zoom:50%;" />

   ASCu:成功	ASLs：失败

**期望探测次数与装填因子α的关系**

> 横坐标：装填因子α
>
> 纵坐标：期望探测次数

当装填因子α＜0.5的时候，各种探测法的期望探测次数都不大，也比较接近

合理的最大装入因子α应该不超过0.85(对线性探测来说)

<img src=".\数据结构-image\image-20220828220419341.png" alt="image-20220828220419341" style="zoom:50%;" />

3. **分离链接法的查找性能**

   所有地址链表的平均长度定义成**装填因子α**，α有可能超过1

   不难证明：其期望探测次数p为：<img src=".\数据结构-image\image-20220828224355504.png" alt="image-20220828224355504" style="zoom:43%;" />

   <img src=".\数据结构-image\image-20220828224458083.png" style="zoom:50%;" />

### 总结

#### 哈希查找(散列查找)特点

选择合适的h(key)，散列法的查找效率期望是常数O(1)，它几乎与关键词的空间的大小n无关！也适合于还建瓷直接比较计算机大的问题

> 优点：
>
> 1. 不像搜索树一样或者平衡二叉树一样，他的查找基本上是跟问题的规模有关系。所以不发生冲突的话基本上是一次成功

>特点：
>
>1. 跟问题规模无关，是一个常量时间
>2. 散列查找在很多情况下，用于字符串的管理(例如web地址名关键词搜索这种字符串管理)，关键词查找
>3. 它是以较小的α为前提。因此，散列方法是一个以空间换时间
>4. 散列方法的存储对关键字是随机的，不便于顺序查找关键字，也不适合于范围查找，或最大值最小值查找

#### 开放地址法

> 优：散列表是一个数组，存储效率高，随机查找
>
> 缺：散列表有"聚集"现象

#### 分离链法

> 优：关键字删除不需要"懒惰删除"法，从而没有存储"垃圾"
>
> 缺：散列表是顺序存储和链式存储的结合，链表部分的存储效率和查找效率都比较低
>
> 太小的α可能导致空间浪费，大的α又将付出更多的时间代价。不均匀的链表长度导致时间效率的严重下降

### 11.5 应用实例：词频统计

```
【例】给定一个英文文本文件，统计文件中所有单词出现的频率，并输出词频最大的前10%的单词及其词频。
		假设单词字符定义为大小写字母、数字和下划线，其它字符均认为是单词分隔符，不予考虑。

【分析】关键：对新读入的单词在已有单词表中查找，如果已经存在，则将该单词的词频加1，如果不存在，则插入该单词并记词频为1。

如何设计该单词表的数据结构才可以进行快速地查找和插入？散列表
```

```c
int main(){
    int TableSize = 10000;//散列表的估计大小
    int wordcount = 0,length;
    HashTable H;
    ElementType word;
    FILE*fp;
    char document[30] = "HarryPotter.txt";//要被统计词频的文件名
    H = Initialize Table( TableSize );//建立散列表(也就是初始化散列表)
    if((fp = fopen(document,"r")) == NULL) FatalError("无法打开文件!\n");
    while(!feof(fp)){//对文件进行处理，读到不是字母跟数字或者下划线而是分隔符的话，那就返回，获得到一个完整的word
        length = GetAWord(fp,word);//从文件中读取一个单词
        if(length > 3){//只考虑适当长度的单词
            wordcount++;//统计文件中单词总数
            InsertAndCount(word,H);//插入哈希表
            //InsertAndCount这个函数作用：到哈希表里去找这个元素单词在不在，不在就插入，如果在就词频加1
        }
    }
    fclose(fp);
    printf("该文档共出现%d个有效单词,"，wordcount);
    Show(H,10.0/100);//显示词频前10%的所有单词
    //Show一共两个参数，一个是我们的散列表，另外一个是我们的要求词频前10%
    //这个函数一共做4件事情：1.统计最大词频;2.用一组数统计从1到最大词频的单词数;3.计算前10%的词频应该是多少;4.输出前10%词频的单词
    DestroyTable(H);//销毁散列表
    return 0;
}
```



## 小白专场[陈越]：电话聊天狂人-C语言实现

### 小白-PM.1 题意理解与解法分析

> 所有电话号码统计一下，打电话或者接电话的总次数是最多的，那这个人就叫做电话聊天狂人

<img src=".\数据结构-image\image-20220831124929950.png" alt="image-20220831124929950" style="zoom:50%;" />

> **解法1：-排序**
>
> 第1步：读入最多2×10五次方个电话号码，每个号码存为长度为11的字符串
> 第2步：按字符串非递减顺序排序
> 第3步：扫描有序数组，累计同号码出现的次数，并且更新最大次数
>
> 优势：编程简单快捷
>
> 缺点：如果这是在现实应用场合，这些号码不是一次性出现给你的，而是每天没分每秒的不断的在进来，每时每刻我们都可能被停下来问这个聊天狂人是谁。那每次都需要做一个NlogN的排序，要做N次这种事情，所以整个算法的复杂度就变成了N2logN
>
> 所以这个算法不好拓展解决动态插入的问题

>**方法2：-直接映射**
>
>为什么是2x10的10次方个单位，因为每个电话有11位，而第一位都是1，把1排除掉
>
><img src=".\数据结构-image\image-20220831130043024.png" alt="image-20220831130043024" style="zoom:50%;" />
>
>优势：编程简单快捷，动态插入快
>
>缺点：下标超过了unsigned long，因为我们平常用到的最长长度的是10位的
>
>直接映射需要存储![img](.\数据结构-image\6619284299584833484.jpeg)个短整型数字，大约是多少字节空间？差不多40GB，<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220831134221137.png" alt="image-20220831134221137" style="zoom:33%;" />
>
>我们需要扫描多少个单位才能找到我们需要的那个电话狂人?
>
>=>为了要找2x10的五次方个号码，必须要扫描2x10的10次方个单元，太浪费啦 

> **解法3-带智商的散列**
>
> 注意：散列表的头节点的个数一共比2N要略大一点
>
> <img src=".\数据结构-image\image-20220831134934368.png" alt="image-20220831134934368" style="zoom:50%;" />
>
> 

### 小白-PM.2 程序框架搭建

```C
int main()
{
    创建散列表;
    读入号码插入表中;
    扫描表输出狂人;
    return 0;	
}
```

```c
int main()
{
    int N,i;
    ElementType Key;//声明这个为了配合我们HashTable里面声明的类型
    HashTable H;
    
    scanf("%d",&N);
    H = CreateTable(N*2);//创建一个散列表
    for(i=0;i<N;i++){
        scanf("%s",Key);Insert(H,Key);//每次读进来一个号码我把它存在key这个字符串里面，然后把它这个字符串插到表里面
        scanf("%s",Key);Insert(H,Key);//每次读入进来的是一对
    }
    ScanAndOutput(H);//ScanAndOutput是自己取的名字
    DestoryTable(H);//HashTable的空间要记得释放掉，写在DestoryTable函数里
    return 0;
}
```

> 创建表需要的流程

<img src=".\数据结构-image\image-20220831144530212.png" alt="image-20220831144530212" style="zoom:50%;" />要调用插入函数的话肯定会用到其他两个函数，一个是散列函数Hash，另一个是要找到合适的位置把它插入,Find

ScanAndOutput要做的事情：<img src=".\数据结构-image\image-20220831144828537.png" alt="image-20220831144828537" style="zoom:43%;" />

### 小白-PM.3 输出狂人

```c
void ScanAndOutput( HashTable H)
{
    int i,MaxCnt = PCnt = 0;
    ElementType MinPhone;
    List Ptr;
    MinPhone[0] = '\0';//最小电话号码初始化为一个空的字符串
    for(i=0;i<H->TableSize;i++){//扫描链表
        Ptr = H->Heads[i].Next;
        while(Ptr){
            if(Ptr->Count >MaxCnt){//更新最大通话次数
                MaxCnt = Ptr->Count;
                strcpy(Minphone,Ptr->Data);//狂人的电话号码copy到我们将要输出的最小的电话号码里
                PCnt = 1;//最狂的只能有一个，初始化次数
            }
            else if(Ptr->Count == MaxCnt ){//狂人不止一个的情况，并列的狂人，最大通话次数一样
                PCnt ++;//狂人计数
                if(strcmp(Minphone,Ptr->Data)>0)//Ptr->Data>0意味着前面号码大后面号码小，那我们就要把最小号码复制到下方这个更新字符串里面去
                    strcpy(Minphone,Ptr->Data);//更新狂人的最小手机号码
            }
            Ptr = Ptr->Next;
        }
    }
    printf("%s %d",MinPhone,MaxCnt);//输出一个是最小的电话号码，另一个是记录这个最大的通话次数
    id(PCnt > 1) printf("%d",PCnt);//狂人如果大于1的话我还得把这个统计数据输出出来
    printf("\n");
}
```

### 小白-PM.4 模块的引用与裁剪

<img src=".\数据结构-image\image-20220831151057159.png" alt="image-20220831151057159" style="zoom:50%;" />

```c
#define KEYLENGTH 11//关键词字符串的最大长度
//关键词类型用字符
typedef char ElementType[KEYLENGTH+1];//长度要加1是因为在C语言中，里面字符串的结尾还占了一个位置
typedef int Index;//散列地址类型

//分离链接法的部分
typedef struct LNode *PtrToLNode;
struct LNode{
    ElementType Data;//电话号码
    PtrToLNode Next;//指向下一个节点的指针
    int Count;//计数器，我们定义为一个整型的Count
};
typedef PtrToLNode Position;
typedef PtrToLNode List;

typedef struct TblNode *HashTable;
struct TblNode{//散列表结点定义
    int TableSize;//表的最大长度
    List Heads;//指向链表头结点的数组
}
```

NextPrime模块

```c
#define MAXTABLESIZE 1000000
int NextPrime(int N)
{/*返回大于N且不超过AXTABLESIZE的最小素数*/
	int i,p = ( N % 2 ) ? N+2 : N+1;/*从大于N的下一个奇数开始*/
	while( p <= MAXTABLESIZE ){
        for( i=(int)sqrt(p);i>2;i--)
            if(!(p%i))break;//p不是素数
        if(i == 2) break;//for正常结束，说明p是素数
        else p += 2;//否则试探下一个奇数
    }
    return p;
}

HashTable CreateTable( int TableSize )
{
    HashTable H;
    int i;
    H = (HashTable)malloc(sizeof(struct TblNode));
    H->TableSize = NextPrime(TableSize);
    H->Heads = (List)malloc(H->TableSize*sizeof(struct LNode));
    for( i = 0;i < H->TableSize; i++){
        H->Heads[i].Data[0] = '\0';H->Heads[i].Next = NULL;
        H->Heads[i].Count = 0;//头节点定义为0，虽然不写也没关系，它本身是一个空节点。但写上是个好习惯，表示这个节点里所有的变量都有一个初始化的值，不要有个节点空着的不知道这是干嘛的
    }
    return H;
}
int Hash(int Key,int P)//Key是整数，当我们把它传给这个Hash函数去处理的时候我们应该已经把那电话号码后五位截取出来了并且把它变成一个整数了，而不是直接把11位电话号码传进来
{
    //除留余数法，散列函数
    return Key%P; 
}
```

> 原始的Find函数

```c
#deine MAXD 5//参与散列映射计算的字符个数，从后往前数5位
Position Find( HashTable H,ElementType Key )
{
    Position P;
    Index Pos;
    //初始散列位置
    Pos = Hash(Key,H->TableSize );//在原始的这个是直接把Key传到Hash函数里面去算的
    //但在我们这个问题里就不能这么做，在Key传进去之前得先把它后五位截出来变成一个整数，变成
    Pos = Hash(atoi(Key+KEYLENGTH-MAXD),H->TableSize)//把数传给atoi转化为整数再把整数传给散列函数去计算
    
    P = H->Heads[Pos].Next;//从该链表的第一个结点开始
    //当未到表尾，并且Key未找到时
    while(P && strcmp(P->Data,Key))
        P = P->Next;
    
    return P;//此时P或者指向找到的结点，或者为NULL
}
```

> 插入函数

```c
bool Insert(HashTable H,ElementType Key)
{
    Position P,NewCell;
    Index Pos;
    
    P = Find(H,Key);
    if(!P){//关键词未找到，可以插入
        NewCell = (Position)malloc(sizeof(struct LNode));
        strcpy(NewCell->Data,Key);
        //初始散列位置
        //Pos = Hash(Key,H->TableSize);这里进行改动
        NewCell->Count = 1;//当我们要插入新的电话号码的时候，这个新的电话号码节点的计数器要初始化为1
        Pos = Hash(atoi(Key+KEYLENGTH-MAXD),H->TableSize);
        //将NewCell插入为H->Heads[Pos]链表的第一个结点
        NewCell->Next = H->Heads[Pos].Next;
        H->Heads[Pos].Next = NewCell;
        return true;
    }
    else{//关键词已存在
        //printf("键值已存在");//表示我找到了这个电话号码，但在实际显示中我们不能显示这一行，需要修改一下
        P->Count++;//找到这个电话号码计数器要加1，表示我们多打了一次电话或多接了一次电话
        return false;
    }
}
```

