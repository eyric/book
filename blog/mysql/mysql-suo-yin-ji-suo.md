# MySQL索引及锁

## 一、从二叉搜索树到B+树

### **1. 二叉搜索树**&#x20;

二叉查找树（英语：Binary Search Tree），也称为 二叉搜索树、有序二叉树（Ordered Binary Tree）或排序二叉树（Sorted Binary Tree），是指一棵空树或者具有下列性质的二叉树：

* 若任意节点的左子树不空，则左子树上所有节点的值均小于它的根节点的值；
* 若任意节点的右子树不空，则右子树上所有节点的值均大于它的根节点的值；
* 任意节点的左、右子树也分别为二叉查找树；
* 没有键值相等的节点。

二叉查找树相比于其他数据结构的优势在于查找、插入的时间复杂度较低。为$O(\log {n})$。二叉查找树是基础性数据结构，用于构建更为抽象的数据结构，如集合、多重集、关联数组等。

二叉查找树的查找过程和次优二叉树类似，通常采取二叉链表作为二叉查找树的存储结构。中序遍历二叉查找树可得到一个关键字的有序序列，一个无序序列可以通过构造一棵二叉查找树变成一个有序序列，构造树的过程即为对无序序列进行查找的过程。每次插入的新的结点都是二叉查找树上新的叶子结点，在进行插入操作时，不必移动其它结点，只需改动某个结点的指针，由空变为非空即可。搜索、插入、删除的复杂度等于树高，期望 $O(\log {n})$，最坏 $O(n)$（数列有序，树退化成线性表）。

![图1 二叉搜索树（BST）](<../../.gitbook/assets/image (102).png>)



虽然二叉查找树的最坏效率是 $O(n)$，但它支持动态查询，且有很多改进版的二叉查找树可以使树高为 $ O(\log n)$，从而将最坏效率降至 ，如$O(\log {n})$ AVL 树、红黑树等。

### **2. 平衡二叉树(AVL树)**

二叉搜索树一定程度上可以提高搜索效率，但是当原序列有序时，例如序列 A = {1，2，3，4，5，6}，构造二叉搜索树如下图 。依据此序列构造的二叉搜索树为右斜树，同时二叉树退化成单链表，搜索效率降低为 O(n)。

![图2 二叉搜索树退化为链表](<../../.gitbook/assets/image (92).png>)

在此二叉搜索树中查找元素 6 需要查找 6 次。二叉搜索树的查找效率取决于树的高度，因此保持树的高度最小，即可保证树的查找效率。同样的序列 A，将其改为图 1.2 的方式存储，查找元素 6 时只需比较 3 次，查找效率提升一倍。可以看出**当节点数目一定，保持树的左右两端保持平衡，树的查找效率最高**。这种左右子树的高度相差不超过 1 的树为**平衡二叉树**。

![图3 平衡二叉树(AVL)](<../../.gitbook/assets/image (78).png>)

在[计算机科学](https://zh.wikipedia.org/wiki/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%A7%91%E5%AD%A6)中，**AVL树**是最早被发明的[自平衡二叉查找树](https://zh.wikipedia.org/wiki/%E5%B9%B3%E8%A1%A1%E6%A0%91)。在AVL树中，任一节点对应的两棵子树的最大高度差为1，因此它也被称为**高度平衡树**。查找、插入和删除在平均和最坏情况下的[时间复杂度](https://zh.wikipedia.org/wiki/%E6%97%B6%E9%97%B4%E5%A4%8D%E6%9D%82%E5%BA%A6)都是$O(\log {n})$。增加和删除元素的操作则可能需要借由一次或多次[树旋转](https://zh.wikipedia.org/wiki/%E6%A0%91%E6%97%8B%E8%BD%AC)，以实现树的重新平衡。AVL 树得名于它的发明者 [G. M. Adelson-Velsky](https://zh.wikipedia.org/wiki/%E6%A0%BC%E5%A5%A5%E5%B0%94%E5%90%89%C2%B7%E9%98%BF%E6%9D%B0%E5%B0%94%E6%9D%BE-%E9%9F%A6%E5%88%A9%E6%96%AF%E5%9F%BA) 和 Evgenii Landis，他们在1962年的论文《An algorithm for the organization of information》中公开了这一数据结构。

**平衡二叉树**（Balanced BinaryTree）又被称为**AVL树**。它具有以下性质：它是一棵空树或它的左右两个子树的高度差的绝对值不超过1，并且左右两个子树都是一棵平衡二叉树。

平衡二叉树一般是一个有序树，它具有二叉树的所有性质，其遍历操作和二叉树的遍历操作相同。但是由于其对二叉树施加了额外限制，因而其添加、删除操作都必须保证平衡二叉树的因子被保持。

平衡二叉树中引入了一个概念：平衡二叉树节点的平衡因子，它指的是该节点的两个子树，即左子树和右子树的高度差，即用左子树的高度减去右子树的高度，如果该节点的某个子树不存在，则该子树的高度为0,如果高度差的绝对值超过1就要根据情况进行调整。

&#x20;

![图4 AVL树的平衡因子](<../../.gitbook/assets/image (29).png>)

**2.1 AVL树插入时的失衡与调整**

![图5.1 AVL树示例](<../../.gitbook/assets/image (28).png>)

在此平衡二叉树插入节点 99 ，树结构变为：

![图5.2 AVL树失衡示例](<../../.gitbook/assets/image (32).png>)

在上图中，节点 66 的左子树高度为 1，右子树高度为 3，此时平衡因子为 -2，树失去平衡。以节点 66 为父节点的那颗树就称为 **最小失衡子树**。

**最小失衡子树**：在新插入的结点向上查找，以第一个平衡因子的**绝对值**超过 1 的结点为根的子树称为最小不平衡子树。也就是说，一棵失衡的树，是有可能有多棵子树同时失衡的。而这个时候，我们只要调整最小的不平衡子树，就能够将不平衡的树调整为平衡的树。

**平衡二叉树的失衡调整主要是通过旋转最小失衡子树来实现的**。根据旋转的方向有两种处理方式，**左旋** 与 **右旋** 。

旋转的目的就是减少高度，通过降低整棵树的高度来平衡。哪边的树高，就把那边的树向上旋转。

（1） 左旋

以图5.1 为例，加入新节点 99 后， 节点 66 的左子树高度为 1，右子树高度为 3，此时平衡因子为 -2。为保证树的平衡，此时需要对节点 66 做出旋转，因为右子树高度高于左子树，对节点进行左旋操作，流程如下：

1. 节点的右孩子替代此节点位置&#x20;
2. 右孩子的左子树变为该节点的右子树
3. 节点本身变为右孩子的左子树

整个操作流程如动图 5.3 所示。

![图5.3 左旋](<../../.gitbook/assets/image (27).png>)

节点的右孩子替代此节点位置 —— 节点 66 的右孩子是节点 77 ，将节点 77 代替节点 66 的位置

* 右孩子的左子树变为该节点的右子树 —— 节点 77 的左子树为节点 75，将节点 75 挪到节点 66 的右子树位置
* 节点本身变为右孩子的左子树 —— 节点 66 变为了节点 77 的左子树

（2） 右旋

右旋操作与左旋类似，操作流程为：

1. 节点的左孩子代表此节点&#x20;
2. 节点的左孩子的右子树变为节点的左子树
3. 将此节点作为左孩子节点的右子树。

![图5.4 右旋](<../../.gitbook/assets/image (22).png>)

**2.2 AVL树的四种插入节点方式**

假设一颗 AVL 树的某个节点为 A，有四种操作会使 A 的左右子树高度差大于 1，从而破坏了原有 AVL 树的平衡性。平衡二叉树插入节点的情况分为以下四种：

| 插入方式 | 描述                                | 旋转方式   |
| ---- | --------------------------------- | ------ |
| LL   | 在 A 的**左子树**根节点的**左子树**上插入节点而破坏平衡 | 右旋转    |
| RR   | 在 A 的**右子树**根节点的**右子树**上插入节点而破坏平衡 | 左旋转    |
| LR   | 在A的**左子树**根节点的**右子树**上插入节点而破坏平衡   | 先左旋后右旋 |
| RL   | 在 A 的**右子树**根节点的**左子树**上插入节点而破坏平衡 | 先右旋后左旋 |

具体分析如下：

（1） A的左孩子的左子树插入节点(LL)

​ 只需要执行一次右旋即可。

![](<../../.gitbook/assets/image (34).png>)

（2） A的右孩子的右子树插入节点(RR)

​ 只需要执行一次左旋即可。

![](<../../.gitbook/assets/image (31).png>)

（3） A的左孩子的右子树插入节点(LR)

若 A 的左孩子节点 B 的右子树 E 插入节点 F ，导致节点 A 失衡，如图：

![](<../../.gitbook/assets/image (40).png>)

A 的平衡因子为 2 ，若仍按照右旋调整，则变化后的图形为这样：

![](<../../.gitbook/assets/image (36).png>)

经过右旋调整发现，调整后树仍然失衡，说明这种情况单纯的进行右旋操作不能使树重新平衡。那么这种插入方式需要执行两步操作，使得旋转之后为 **原来根结点的左孩子的右孩子作为新的根节点** 。

1. 对失衡节点 A 的左孩子 B 进行左旋操作，即上述 RR 情形操作。
2. 对失衡节点 A 做右旋操作，即上述 LL 情形操作。

调整过程如下：

![](<../../.gitbook/assets/image (23).png>)

也就是说，经过这两步操作，使得 **原来根节点的左孩子的右孩子 E 节点成为了新的根节点**。

（4）A的右孩子的左子树插入节点(RL)

右孩子插入左节点的过程与左孩子插入右节点过程类似，也是需要执行两步操作，使得旋转之后为 **原来根结点的右孩子的左孩子作为新的根节点** 。

1. 对失衡节点 A 的右孩子 C 进行右旋操作，即上述 LL 情形操作。
2. 对失衡节点 A 做左旋操作，即上述 RR 情形操作。

![](<../../.gitbook/assets/image (25).png>)

![](<../../.gitbook/assets/image (41).png>)

也就是说，经过这两步操作，使得 **原来根节点的右孩子的左孩子 D 节点成为了新的根节点**。

（5）小结

1. 在所有的不平衡情况中，都是按照先 **寻找最小不平衡树**，然后 **寻找所属的不平衡类别**，再 **根据 4 种类别进行固定化程序的操作**。
2. LL , LR ，RR ，RL其实已经为我们提供了最后哪个结点作为新的根指明了方向。如 LR 型最后的根结点为原来的根的左孩子的右孩子，RL 型最后的根结点为原来的根的右孩子的左孩子。只要记住这四种情况，可以很快地推导出所有的情况。
3. 维护平衡二叉树，最麻烦的地方在于平衡因子的维护。建议读者们根据小吴提供的图片和动图，自己动手画一遍，这样可以更加感性的理解操作。

**2.3 AVL树的四种删除节点方式**

AVL 树和二叉查找树的删除操作情况一致，都分为四种情况：

（1）删除叶子节点 （2）删除的节点只有左子树 （3）删除的节点只有右子树 （4）删除的节点既有左子树又有右子树

只不过 AVL 树在删除节点后需要重新检查平衡性并修正，同时，删除操作与插入操作后的平衡修正区别在于，插入操作后只需要对插入栈中的弹出的第一个非平衡节点进行修正，而删除操作需要修正栈中的所有非平衡节点。

删除操作的大致步骤如下：

* 以前三种情况为基础尝试删除节点，并将访问节点入栈。
* 如果尝试删除成功，则依次检查栈顶节点的平衡状态，遇到非平衡节点，即进行旋转平衡，直到栈空。
* 如果尝试删除失败，证明是第四种情况。这时先找到被删除节点的右子树最小节点并删除它，将访问节点继续入栈。
* 再依次检查栈顶节点的平衡状态和修正直到栈空。

对于删除操作造成的非平衡状态的修正，可以这样理解：对左或者右子树的删除操作相当于对右或者左子树的插入操作，然后再对应上插入的四种情况选择相应的旋转就好了。

### **3. 红黑树**

红黑树（英语：Red–black tree）是一种自平衡二叉查找树，是每个节点都带有颜色属性的二叉查找树，颜色为红色或黑色。在二叉查找树强制一般要求以外，对于任何有效的红黑树我们增加了如下的额外要求：

1. 节点是红色或黑色。
2. 根是黑色。
3. 所有叶子都是黑色（叶子是NIL节点）。
4. 每个红色节点必须有两个黑色的子节点。（从每个叶子到根的所有路径上不能有两个连续的红色节点。）
5. 从任一节点到其每个叶子的所有简单路径都包含相同数目的黑色节点。

![](<../../.gitbook/assets/image (24).png>)

### **4. B树**

**4.1  B-树**

B树和平衡二叉树稍有不同的是B树属于多叉树又名平衡多路查找树（查找路径不只两个），数据库索引技术里大量使用者B树和B+树的数据结构。

1. 排序方式：所有节点关键字是按递增次序排列，并遵循左小右大原则；
2. 子节点数：非叶节点的子节点数>1，且<=M ，且M>=2，空树除外（注：M阶代表一个树节点最多有多少个查找路径，M=M路,当M=2则是2叉树,M=3则是3叉）；
3. 关键字数：枝节点的关键字数量大于等于ceil(m/2)-1个且小于等于M-1个（注：ceil()是个朝正无穷方向取整的函数 如ceil(1.1)结果为2);
4. 所有叶子节点均在同一层、叶子节点除了包含了关键字和关键字记录的指针外也有指向其子节点的指针只不过其指针地址都为null对应下图最后一层节点的空格子;

用一个图和一个实际的例子来理解B树（这里为了理解方便我就直接用实际字母的大小来排列C>B>A）

![](<../../.gitbook/assets/image (37).png>)

**B树的查询流程：**

如上图我要从上图中找到E字母，查找流程如下

（1）获取根节点的关键字进行比较，当前根节点关键字为M，E\<M（26个字母顺序），所以往找到指向左边的子节点（二分法规则，左小右大，左边放小于当前节点值的子节点、右边放大于当前节点值的子节点）；

（2）拿到关键字D和G，D\<E\<G 所以直接找到D和G中间的节点；

（3）拿到E和F，因为E=E 所以直接返回关键字和指针信息（如果树结构里面没有包含所要查找的节点则返回null）；

**B树的插入节点流程**

定义一个5阶树（平衡5路查找树;），现在我们要把3、8、31、11、23、29、50、28 这些数字构建出一个5阶树出来;

遵循规则：

（1）节点拆分规则：当前是要组成一个5路查找树，那么此时m=5,关键字数必须<=5-1（这里关键字数>4就要进行节点拆分）；

（2）排序规则：满足节点本身比左边节点大，比右边节点小的排序规则;

先插入 3、8、31、11

![](<../../.gitbook/assets/image (42).png>)

再插入23、29

![](<../../.gitbook/assets/image (44).png>)

再插入50、28

![](<../../.gitbook/assets/image (33).png>)

**B树节点的删除**

**规则：**

（1）节点合并规则：当前是要组成一个5路查找树，那么此时m=5,关键字数必须大于等于ceil（5/2）（这里关键字数<2就要进行节点合并）；

（2）满足节点本身比左边节点大，比右边节点小的排序规则;

（3）关键字数小于二时先从子节点取，子节点没有符合条件时就向向父节点取，取中间值往父节点放；

![](<../../.gitbook/assets/image (30).png>)

B树相对于平衡二叉树的不同是，每个节点包含的关键字增多了，特别是在B树应用到数据库中的时候，数据库充分利用了磁盘块的原理（磁盘数据存储是采用块的形式存储的，每个块的大小为4K，每次IO进行数据读取时，同一个磁盘块的数据可以一次性读取出来）把节点大小限制和充分使用在磁盘快大小范围；把树的节点关键字增多后树的层级比原来的二叉树少了，减少数据查找的次数和复杂度;

**4.2  B+树**

1. B+树是B树的一个升级版，相对于B树来说B+树更充分的利用了节点的空间，让查询速度更加稳定，其速度完全接近于二分法查找。为什么说B+树查找的效率要比B树更高、更稳定。
2. B+跟B树不同B+树的**非叶子**节点不保存关键字记录的指针，只进行数据索引，这样使得B+树每个**非叶子**节点所能保存的关键字大大增加；
3. B+树**叶子**节点保存了父节点的所有关键字记录的指针，所有数据地址必须要到叶子节点才能获取到。所以每次数据查询的次数都一样；
4. B+树叶子节点的关键字从小到大有序排列，左边结尾数据都会保存右边节点开始数据的指针。
5. 非叶子节点的子节点数=关键字数（来源百度百科）（根据各种资料 这里有两种算法的实现方式，另一种为非叶节点的关键字数=子节点数-1（来源维基百科)，虽然他们数据排列结构不一样，但其原理还是一样的Mysql 的B+树是用第一种方式实现）;

![百度百科算法结构示意图](<../../.gitbook/assets/image (26).png>)



![维基百科算法结构示意图](<../../.gitbook/assets/image (47).png>)

1、B+**树的层级更少**：相较于B树B+每个**非叶子**节点存储的关键字数更多，树的层级更少所以查询数据更快；

2、B+**树查询速度更稳定**：B+所有关键字数据地址都存在**叶子**节点上，所以每次查找的次数都相同所以查询速度要比B树更稳定;

3、B+**树天然具备排序功能：**B+树所有的**叶子**节点数据构成了一个有序链表，在查询大小区间的数据时候更方便，数据紧密性很高，缓存的命中率也会比B树高。

4、B+**树全节点遍历更快：**B+树遍历整棵树只需要遍历所有的**叶子**节点即可，，而不需要像B树一样需要对每一层进行遍历，这有利于数据库做全表扫描。

**B树**相对于**B+树**的优点是，如果经常访问的数据离根节点很近，而**B树**的**非叶子**节点本身存有关键字其数据的地址，所以这种数据检索的时候会要比**B+树**快。

**4.3 B\*树**

B\*树是B+树的变种，相对于B+树他们的不同之处如下：

（1）首先是关键字个数限制问题，B+树初始化的关键字初始化个数是cei(m/2)，b_树的初始化个数为（cei(2/3_m)）

（2）B+树节点满时就会分裂，而B\*树节点满时会检查兄弟节点是否满（因为每个节点都有指向兄弟的指针），如果兄弟节点未满则向兄弟节点转移关键字，如果兄弟节点已满，则从当前节点和兄弟节点各拿出1/3的数据创建一个新的节点出来；

在B+树的基础上因其初始化的容量变大，使得节点空间使用率更高，而又存有兄弟节点的指针，可以向兄弟节点转移关键字的特性使得B\*树额分解次数变得更少；

![](<../../.gitbook/assets/image (35).png>)

## 二、MySQL中的索引

数据库查询是数据库的最主要功能之一。我们都希望查询数据的速度能尽可能的快，因此数据库系统的设计者会从查询算法的角度进行优化。最基本的查询算法当然是**顺序查找**（linear search），这种复杂度为O(n)的算法在数据量很大时显然是糟糕的，好在计算机科学的发展提供了很多更优秀的查找算法，例如**二分查找**（binary search）、**二叉树查找**（binary tree search）等。如果稍微分析一下会发现，每种查找算法都只能应用于特定的数据之上，例如二分查找要求被检索数据有序，而二叉树查找只能应用于二叉查找树上，但是数据本身的组织结构不可能完全满足各种数据结构（例如，理论上不可能同时将两列都按顺序进行组织），所以，在数据之外，数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用（指向）数据，这样就可以在这些数据结构上实现高级查找算法。这种数据结构，就是**索引**。

​ MySQL官方对索引的定义为：索引（Index）是帮助MySQL高效获取数据的数据结构。提取句子主干，就可以得到索引的本质：索引是数据结构。本文以MySQL数据库为研究对象，讨论与数据库索引相关的一些话题。特别需要说明的是，MySQL支持诸多存储引擎，而各种存储引擎对索引的支持也各不相同，因此MySQL数据库支持多种索引类型，如BTree索引，哈希索引，全文索引等等。为了避免混乱，本文将只关注于BTree索引，因为这是平常使用MySQL时主要打交道的索引，至于哈希索引和全文索引本文暂不讨论。

### **1. MyISAM引擎**

MyISAM引擎使用B+Tree作为索引结构，叶节点的data域存放的是数据记录的地址。下图是MyISAM索引的原理图：

![](<../../.gitbook/assets/image (67).png>)

这里设表一共有三列，假设我们以Col1为主键，则图8是一个MyISAM表的主索引（Primary key）示意。可以看出MyISAM的索引文件仅仅保存数据记录的地址。在MyISAM中，主索引和辅助索引（Secondary key）在结构上没有任何区别，只是主索引要求key是唯一的，而辅助索引的key可以重复。如果我们在Col2上建立一个辅助索引，则此索引的结构如下图所示：

![](<../../.gitbook/assets/image (48).png>)

同样也是一颗B+Tree，data域保存数据记录的地址。因此，MyISAM中索引检索的算法为首先按照B+Tree搜索算法搜索索引，如果指定的Key存在，则取出其data域的值，然后以data域的值为地址，读取相应数据记录。

MyISAM的索引方式也叫做“非聚集”的，之所以这么称呼是为了与InnoDB的聚集索引区分。

### **2. InnoDB引擎**

虽然InnoDB也使用B+Tree作为索引结构，但具体实现方式却与MyISAM截然不同。

第一个重大区别是InnoDB的数据文件本身就是索引文件。从上文知道，MyISAM索引文件和数据文件是分离的，索引文件仅保存数据记录的地址。而在InnoDB中，表数据文件本身就是按B+Tree组织的一个索引结构，这棵树的叶节点data域保存了完整的数据记录。这个索引的key是数据表的主键，因此InnoDB表数据文件本身就是主索引。

![](<../../.gitbook/assets/image (75).png>)

图10是InnoDB主索引（同时也是数据文件）的示意图，可以看到叶节点包含了完整的数据记录。这种索引叫做聚集索引。因为InnoDB的数据文件本身要按主键聚集，所以InnoDB要求表必须有主键（MyISAM可以没有），如果没有显式指定，则MySQL系统会自动选择一个可以唯一标识数据记录的列作为主键，如果不存在这种列，则MySQL自动为InnoDB表生成一个隐含字段作为主键，这个字段长度为6个字节，类型为长整形。

第二个与MyISAM索引的不同是InnoDB的辅助索引data域存储相应记录主键的值而不是地址。换句话说，InnoDB的所有辅助索引都引用主键作为data域。例如，图11为定义在Col3上的一个辅助索引：

![](<../../.gitbook/assets/image (45).png>)

这里以英文字符的ASCII码作为比较准则。聚集索引这种实现方式使得按主键的搜索十分高效，但是辅助索引搜索需要检索两遍索引：首先检索辅助索引获得主键，然后用主键到主索引中检索获得记录。

了解不同存储引擎的索引实现方式对于正确使用和优化索引都非常有帮助，例如知道了InnoDB的索引实现后，就很容易明白为什么不建议使用过长的字段作为主键，因为所有辅助索引都引用主索引，过长的主索引会令辅助索引变得过大。再例如，用非单调的字段作为主键在InnoDB中不是个好主意，因为InnoDB数据文件本身是一颗B+Tree，非单调的主键会造成在插入新记录时数据文件为了维持B+Tree的特性而频繁的分裂调整，十分低效，而使用自增字段作为主键则是一个很好的选择。

## 三、MySQL索引优化实践

MySQL的优化主要分为结构优化（Scheme optimization）和查询优化（Query optimization）。本章讨论的高性能索引策略主要属于结构优化范畴。本章的内容完全基于上文的理论基础，实际上一旦理解了索引背后的机制，那么选择高性能的策略就变成了纯粹的推理，并且可以理解这些策略背后的逻辑。

### **1.示例数据库**

为了讨论索引策略，需要一个数据量不算小的数据库作为示例。本文选用MySQL官方文档中提供的示例数据库之一：employees。这个数据库关系复杂度适中，且数据量较大。下图是这个数据库的E-R关系图（引用自MySQL官方手册）：

![](<../../.gitbook/assets/image (53).png>)

MySQL官方文档中关于此数据库的页面为[http://dev.mysql.com/doc/employee/en/employee.html。里面详细介绍了此数据库，并提供了下载地址和导入方法，如果有兴趣导入此数据库到自己的MySQL可以参考文中内容。](http://dev.mysql.com/doc/employee/en/employee.html%E3%80%82%E9%87%8C%E9%9D%A2%E8%AF%A6%E7%BB%86%E4%BB%8B%E7%BB%8D%E4%BA%86%E6%AD%A4%E6%95%B0%E6%8D%AE%E5%BA%93%EF%BC%8C%E5%B9%B6%E6%8F%90%E4%BE%9B%E4%BA%86%E4%B8%8B%E8%BD%BD%E5%9C%B0%E5%9D%80%E5%92%8C%E5%AF%BC%E5%85%A5%E6%96%B9%E6%B3%95%EF%BC%8C%E5%A6%82%E6%9E%9C%E6%9C%89%E5%85%B4%E8%B6%A3%E5%AF%BC%E5%85%A5%E6%AD%A4%E6%95%B0%E6%8D%AE%E5%BA%93%E5%88%B0%E8%87%AA%E5%B7%B1%E7%9A%84MySQL%E5%8F%AF%E4%BB%A5%E5%8F%82%E8%80%83%E6%96%87%E4%B8%AD%E5%86%85%E5%AE%B9%E3%80%82)

### **2. 最左前缀原理与相关优化**

高效使用索引的首要条件是知道什么样的查询会使用到索引，这个问题和B+Tree中的“最左前缀原理”有关，下面通过例子说明最左前缀原理。

这里先说一下联合索引的概念。在上文中，我们都是假设索引只引用了单个的列，实际上，MySQL中的索引可以以一定顺序引用多个列，这种索引叫做联合索引，一般的，一个联合索引是一个有序元组，其中各个元素均为数据表的一列，实际上要严格定义索引需要用到关系代数，但是这里我不想讨论太多关系代数的话题，因为那样会显得很枯燥，所以这里就不再做严格定义。另外，单列索引可以看成联合索引元素数为1的特例。

![](<../../.gitbook/assets/image (69).png>)

从结果中可以到titles表的主索引为，还有一个辅助索引。为了避免多个索引使事情变复杂（MySQL的SQL优化器在多索引时行为比较复杂），这里我们将辅助索引drop掉：

```sql
ALTER TABLE employees.titles DROP INDEX emp_no;
```

这样就可以专心分析索引PRIMARY的行为了。

**2.1 情况一：全列匹配。**

![](<../../.gitbook/assets/image (60).png>)

很明显，当按照索引中所有列进行精确匹配（这里精确匹配指“=”或“IN”匹配）时，索引可以被用到。这里有一点需要注意，理论上索引对顺序是敏感的，但是由于MySQL的查询优化器会自动调整where子句的条件顺序以使用适合的索引，例如我们将where中的条件顺序颠倒：

![](<../../.gitbook/assets/image (74).png>)

效果是一样的。

**2.2 情况二：最左前缀匹配。**

![](<../../.gitbook/assets/image (49).png>)

当查询条件精确匹配索引的左边连续一个或几个列时，如或，所以可以被用到，但是只能用到一部分，即条件所组成的最左前缀。上面的查询从分析结果看用到了PRIMARY索引，但是key\_len为4，说明只用到了索引的第一列前缀。

**2.3 情况三：查询条件用到了索引中列的精确匹配，但是中间某个条件未提供。**

![](<../../.gitbook/assets/image (66).png>)

此时索引使用情况和情况二相同，因为title未提供，所以查询只用到了索引的第一列，而后面的from\_date虽然也在索引中，但是由于title不存在而无法和左前缀连接，因此需要对结果进行扫描过滤from\_date（这里由于emp\_no唯一，所以不存在扫描）。如果想让from\_date也使用索引而不是where过滤，可以增加一个辅助索引，此时上面的查询会使用这个索引。除此之外，还可以使用一种称之为“隔离列”的优化方法，将emp\_no与from\_date之间的“坑”填上。

首先我们看下title一共有几种不同的值：

![](<../../.gitbook/assets/image (71).png>)

只有7种。在这种成为“坑”的列值比较少的情况下，可以考虑用“IN”来填补这个“坑”从而形成最左前缀：

![](<../../.gitbook/assets/image (54).png>)

这次key\_len为59，说明索引被用全了，但是从type和rows看出IN实际上执行了一个range查询，这里检查了7个key。看下两种查询的性能比较：

![](<../../.gitbook/assets/image (61).png>)

“填坑”后性能提升了一点。如果经过emp\_no筛选后余下很多数据，则后者性能优势会更加明显。当然，如果title的值很多，用填坑就不合适了，必须建立辅助索引。

**2.4 情况四：查询条件没有指定索引第一列。**

![](<../../.gitbook/assets/image (57).png>)

由于不是最左前缀，索引这样的查询显然用不到索引。

**2.5 情况五：匹配某列的前缀字符串。**

![](<../../.gitbook/assets/image (68).png>)

此时可以用到索引，如果通配符%不出现在开头，则可以用到索引，但根据具体情况不同可能只会用其中一个前缀

**2.6 情况六：范围查询。**

![](<../../.gitbook/assets/image (50).png>)

范围列可以用到索引（必须是最左前缀），但是范围列后面的列无法用到索引。同时，索引最多用于一个范围列，因此如果查询条件中有两个范围列则无法全用到索引。

![](<../../.gitbook/assets/image (56).png>)

可以看到索引对第二个范围索引无能为力。这里特别要说明MySQL一个有意思的地方，那就是仅用explain可能无法区分范围索引和多值匹配，因为在type中这两者都显示为range。同时，用了“between”并不意味着就是范围查询，例如下面的查询：

![](<../../.gitbook/assets/image (64).png>)

看起来是用了两个范围查询，但作用于emp\_no上的“BETWEEN”实际上相当于“IN”，也就是说emp\_no实际是多值精确匹配。可以看到这个查询用到了索引全部三个列。因此在MySQL中要谨慎地区分多值匹配和范围匹配，否则会对MySQL的行为产生困惑。

**2.7 情况七：查询条件中含有函数或表达式。**

很不幸，如果查询条件中含有函数或表达式，则MySQL不会为这列使用索引（虽然某些在数学意义上可以使用）。例如：

![](<../../.gitbook/assets/image (70).png>)

虽然这个查询和情况五中功能相同，但是由于使用了函数left，则无法为title列应用索引，而情况五中用LIKE则可以。再如：

![](<../../.gitbook/assets/image (52).png>)

显然这个查询等价于查询emp\_no为10001的函数，但是由于查询条件是一个表达式，MySQL无法为其使用索引。看来MySQL还没有智能到自动优化常量表达式的程度，因此在写查询语句时尽量避免表达式出现在查询中，而是先手工私下代数运算，转换为无表达式的查询语句。

### **3. 索引选择性(区分度)与前缀索引**

既然索引可以加快查询速度，那么是不是只要是查询语句需要，就建上索引？答案是否定的。因为索引虽然加快了查询速度，但索引也是有代价的：索引文件本身要消耗存储空间，同时索引会加重插入、删除和修改记录时的负担，另外，MySQL在运行时也要消耗资源维护索引，因此索引并不是越多越好。一般两种情况下不建议建索引。

第一种情况是表记录比较少，例如一两千条甚至只有几百条记录的表，没必要建索引，让查询做全表扫描就好了。至于多少条记录才算多，这个个人有个人的看法，我个人的经验是以2000作为分界线，记录数不超过 2000可以考虑不建索引，超过2000条可以酌情考虑索引。

另一种不建议建索引的情况是索引的选择性较低。所谓索引的选择性（Selectivity），是指不重复的索引值（也叫基数，Cardinality）与表记录数（#T）的比值：

Index Selectivity = Cardinality / #T

显然选择性的取值范围为(0, 1]，选择性越高的索引价值越大，这是由B+Tree的性质决定的。例如，上文用到的employees.titles表，如果title字段经常被单独查询，是否需要建索引，我们看一下它的选择性：

![](<../../.gitbook/assets/image (46).png>)

title的选择性不足0.0001（精确值为0.00001579），所以实在没有什么必要为其单独建索引。

有一种与索引选择性有关的索引优化策略叫做前缀索引，就是用列的前缀代替整个列作为索引key，当前缀长度合适时，可以做到既使得前缀索引的选择性接近全列索引，同时因为索引key变短而减少了索引文件的大小和维护开销。下面以employees.employees表为例介绍前缀索引的选择和使用。

employees表只有一个索引，那么如果我们想按名字搜索一个人，就只能全表扫描了：

![](<../../.gitbook/assets/image (65).png>)

如果频繁按名字搜索员工，这样显然效率很低，因此我们可以考虑建索引。有两种选择，建或，看下两个索引的选择性：

![](<../../.gitbook/assets/image (73).png>)

显然选择性太低，选择性很好，但是first\_name和last\_name加起来长度为30，有没有兼顾长度和选择性的办法？可以考虑用first\_name和last\_name的前几个字符建立索引，例如，看看其选择性：

![](<../../.gitbook/assets/image (62).png>)

选择性还不错，但离0.9313还是有点距离，那么把last\_name前缀加到4：

![](<../../.gitbook/assets/image (55).png>)

这时选择性已经很理想了，而这个索引的长度只有18，比短了接近一半，我们把这个前缀索引 建上：

![](<../../.gitbook/assets/image (59).png>)

此时再执行一遍按名字查询，比较分析一下与建索引前的结果：

![](<../../.gitbook/assets/image (58).png>)

性能的提升是显著的，查询速度提高了120多倍。

前缀索引兼顾索引大小和查询速度，但是其缺点是不能用于ORDER BY和GROUP BY操作，也不能用于Covering index（即当索引本身包含查询所需全部数据时，不再访问数据文件本身）。

### **4. InnoDB的主键选择与插入优化**

在使用InnoDB存储引擎时，如果没有特别的需要，请永远使用一个与业务无关的自增字段作为主键。

经常看到有帖子或博客讨论主键选择问题，有人建议使用业务无关的自增主键，有人觉得没有必要，完全可以使用如学号或身份证号这种唯一字段作为主键。不论支持哪种论点，大多数论据都是业务层面的。如果从数据库索引优化角度看，使用InnoDB引擎而不使用自增主键绝对是一个糟糕的主意。

上文讨论过InnoDB的索引实现，InnoDB使用聚集索引，数据记录本身被存于主索引（一颗B+Tree）的叶子节点上。这就要求同一个叶子节点内（大小为一个内存页或磁盘页）的各条数据记录按主键顺序存放，因此每当有一条新的记录插入时，MySQL会根据其主键将其插入适当的节点和位置，如果页面达到装载因子（InnoDB默认为15/16），则开辟一个新的页（节点）。

如果表使用自增主键，那么每次插入新的记录，记录就会顺序添加到当前索引节点的后续位置，当一页写满，就会自动开辟一个新的页。如下图所示：

![](<../../.gitbook/assets/image (51).png>)

这样就会形成一个紧凑的索引结构，近似顺序填满。由于每次插入时也不需要移动已有数据，因此效率很高，也不会增加很多开销在维护索引上。

如果使用非自增主键（如果身份证号或学号等），由于每次插入主键的值近似于随机，因此每次新纪录都要被插到现有索引页得中间某个位置：

![](<../../.gitbook/assets/image (63).png>)

此时MySQL不得不为了将新记录插到合适位置而移动数据，甚至目标页面可能已经被回写到磁盘上而从缓存中清掉，此时又要从磁盘上读回来，这增加了很多开销，同时频繁的移动、分页操作造成了大量的碎片，得到了不够紧凑的索引结构，后续不得不通过OPTIMIZE TABLE来重建表并优化填充页面。

因此，只要可以，请尽量在InnoDB上采用自增字段做主键。

## 四、MySQL中的锁

关于 Mysql 的锁，各种概念就会喷涌而出，事实上，锁有好几种维度。

### **1. 类型维度**

* 共享锁（读锁 / S 锁）
* 排它锁（写锁 / X 锁） 类型细分：
*
  * 意向共享锁
  * 意向排他（互斥）锁
* 悲观锁（使用锁，即 for update）
* 乐观锁（使用版本号字段，类似 CAS 机制，即用户自己控制。缺点：并发很高的时候，多了很多无用的重试）

### **2. 锁的粒度（粒度维度）**

* 表锁
* 页锁（Mysql BerkeleyDB 引擎）
* 行锁（InnoDB）

### **3. 锁的算法（算法维度）**

* Record Lock（单行记录）
* Gap Lock（间隙锁，锁定一个范围，但不包含锁定记录）
* Next-Key Lock（Record Lock + Gap Lock，锁定一个范围，并且锁定记录本身， MySql 防止幻读，就是使用此锁实现）

### **4. 默认的读操作，上锁吗？**

* 默认是 MVCC 机制（“一致性非锁定读”）保证 RR 级别的隔离正确性，是不上锁的。

可以选择手动上锁：select xxxx for update (排他锁); select xxxx lock in share mode(共享锁)，称之为“一致性锁定读”。

使用锁之后，就能在 RR 级别下，避免幻读。当然，默认的 MVCC 读，也能避免幻读。

既然 RR 能够防止幻读，那么，SERIALIZABLE 有啥用呢？**答案是**防止丢失更新。

这个时候，我们必须使用 SERIALIZABLE 级别进行串行读取。

最后，行锁的实现原理就是锁住聚集索引，如果你查询的时候，没有正确地击中索引，MySql 优化器将会抛弃行锁，使用表锁。

## 五、MySQL死锁分析

![](<../../.gitbook/assets/image (72).png>)

对于 MySQL 的 InnoDb 存储引擎来说，死锁问题是避免不了的，没有哪种解决方案可以说完全解决死锁问题，但是我们可以通过一些可控的手段，降低出现死锁的概率。

1. 对索引加锁顺序的不一致很可能会导致死锁，所以如果可以，尽量以相同的顺序来访问索引记录和表。在程序以批量方式处理数据的时候，如果事先对数据排序，保证每个线程按固定的顺序来处理记录，也可以大大降低出现死锁的可能；
2. Gap 锁往往是程序中导致死锁的真凶，由于默认情况下 MySQL 的隔离级别是 RR，所以如果能确定幻读和不可重复读对应用的影响不大，可以考虑将隔离级别改成 RC，可以避免 Gap 锁导致的死锁；
3. 为表添加合理的索引，如果不走索引将会为表的每一行记录加锁，死锁的概率就会大大增大；
4. 我们知道 MyISAM 只支持表锁，它采用一次封锁技术来保证事务之间不会发生死锁，所以，我们也可以使用同样的思想，在事务中一次锁定所需要的所有资源，减少死锁概率；
5. 避免大事务，尽量将大事务拆成多个小事务来处理；因为大事务占用资源多，耗时长，与其他事务冲突的概率也会变高；
6. 避免在同一时间点运行多个对同一表进行读写的脚本，特别注意加锁且操作数据量比较大的语句；我们经常会有一些定时脚本，避免它们在同一时间点运行；
7. 设置锁等待超时参数：`innodb_lock_wait_timeout`，这个参数并不是只用来解决死锁问题，在并发访问比较高的情况下，如果大量事务因无法立即获得所需的锁而挂起，会占用大量计算机资源，造成严重性能问题，甚至拖跨数据库。我们通过设置合适的锁等待超时阈值，可以避免这种情况发生。

#### 参考文献

* [MySQL索引背后的数据结构及算法原理](https://blog.codinglabs.org/articles/theory-of-mysql-index.html)
* [平衡二叉树、B树、B+树、B\*树 理解其中一种你就都明白了](https://zhuanlan.zhihu.com/p/27700617)
* [深入理解MySQL索引底层实现原理](https://zhuanlan.zhihu.com/p/77383599)
* [MySQL——索引实现原理](https://juejin.im/post/6844903701480472590)
* [MySql 三大知识点——索引、锁、事务！](https://juejin.im/post/6844903802173128718)
* [Mysql加锁过程详解（8）-理解innodb的锁(record,gap,Next-Key lock)](https://www.cnblogs.com/crazylqy/p/7773492.html)
* [解决死锁之路（终结篇） - 再见死锁](https://www.aneasystone.com/archives/2018/04/solving-dead-locks-four.html)
* [什么是平衡二叉树（AVL）](https://zhuanlan.zhihu.com/p/56066942)
* [红黑树--（高清无码图+代码）演示](https://juejin.im/post/6844903715468492808)
