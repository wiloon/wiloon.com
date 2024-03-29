---
title: B树(B-tree), B+树(B+-tree),B*树(B*-tree)
author: "-"
date: 2011-12-24T12:49:34+00:00
url: btree
categories:
  - Data-Structure
tags:
  - reprint
---
## B树(B-tree), B+树(B+-tree),B*树(B*-tree)
## B树(B-tree), B+树(B+-tree), B\*树(B\*-tree)
### B树(B-tree)

一颗m阶的B树定义如下：

1）每个结点最多有m-1个关键字。

2）根结点最少可以只有1个关键字。

3）非根结点至少有Math.ceil(m/2)-1个关键字。

4）每个结点中的关键字都按照从小到大的顺序排列，每个关键字的左子树中的所有关键字都小于它，而右子树中的所有关键字都大于它。

5）所有叶子结点都位于同一层，或者说根结点到每个叶子结点的长度都相同。


注意: 之前有看到有很多文章把B树和B-tree理解成了两种不同类别的树，其实这两个是同一种树;

概念：

B树和平衡二叉树稍有不同的是B树属于多叉树又名平衡多路查找树 (查找路径不只两个），数据库索引技术里大量使用者B树和B+树的数据结构，让我们来看看他有什么特点;

规则：


 (1）排序方式：所有节点关键字是按递增次序排列，并遵循左小右大原则；

 (2）子节点数：非叶节点的子节点数>1，且<=M ，且M>=2，空树除外 (注：M阶代表一个树节点最多有多少个查找路径，M=M路,当M=2则是2叉树,M=3则是3叉）；

 (3）关键字数：枝节点的关键字数量大于等于ceil(m/2)-1个且小于等于M-1个 (注：ceil()是个朝正无穷方向取整的函数 如ceil(1.1)结果为2);

 (4）所有叶子节点均在同一层、叶子节点除了包含了关键字和关键字记录的指针外也有指向其子节点的指针只不过其指针地址都为null对应下图最后一层节点的空格子;

最后我们用一个图和一个实际的例子来理解B树 (这里为了理解方便我就直接用实际字母的大小来排列C>B>A）

B树的查询流程：


如上图我要从上图中找到E字母，查找流程如下

 (1）获取根节点的关键字进行比较，当前根节点关键字为M，`E<M` (26个字母顺序），所以往找到指向左边的子节点 (二分法规则，左小右大，左边放小于当前节点值的子节点、右边放大于当前节点值的子节点）；

 (2）拿到关键字D和G，`D<E<G` 所以直接找到D和G中间的节点；

 (3）拿到E和F，因为E=E 所以直接返回关键字和指针信息 (如果树结构里面没有包含所要查找的节点则返回null）；



B树的插入节点流程


定义一个5阶树 (平衡5路查找树;），现在我们要把3、8、31、11、23、29、50、28 这些数字构建出一个5阶树出来;

遵循规则：

 (1）节点拆分规则：当前是要组成一个5路查找树，那么此时m=5,关键字数必须<=5-1 (这里关键字数>4就要进行节点拆分）；

 (2）排序规则：满足节点本身比左边节点大，比右边节点小的排序规则;



先插入 3、8、31、11






再插入23、29




再插入50、28






B树节点的删除


规则：



 (1）节点合并规则：当前是要组成一个5路查找树，那么此时m=5,关键字数必须大于等于ceil (5/2） (这里关键字数<2就要进行节点合并）；

 (2）满足节点本身比左边节点大，比右边节点小的排序规则;

 (3）关键字数小于二时先从子节点取，子节点没有符合条件时就向向父节点取，取中间值往父节点放；




特点：

B树相对于平衡二叉树的不同是，每个节点包含的关键字增多了，特别是在B树应用到数据库中的时候，数据库充分利用了磁盘块的原理 (磁盘数据存储是采用块的形式存储的，每个块的大小为4K，每次IO进行数据读取时，同一个磁盘块的数据可以一次性读取出来）把节点大小限制和充分使用在磁盘快大小范围；把树的节点关键字增多后树的层级比原来的二叉树少了，减少数据查找的次数和复杂度;

3、B+树


概念


B+树是B树的一个升级版，相对于B树来说B+树更充分的利用了节点的空间，让查询速度更加稳定，其速度完全接近于二分法查找。为什么说B+树查找的效率要比B树更高、更稳定；我们先看看两者的区别



规则


 (1）B+跟B树不同B+树的非叶子节点不保存关键字记录的指针，只进行数据索引，这样使得B+树每个非叶子节点所能保存的关键字大大增加；

 (2）B+树叶子节点保存了父节点的所有关键字记录的指针，所有数据地址必须要到叶子节点才能获取到。所以每次数据查询的次数都一样；

 (3）B+树叶子节点的关键字从小到大有序排列，左边结尾数据都会保存右边节点开始数据的指针。

 (4）非叶子节点的子节点数=关键字数 (来源百度百科） (根据各种资料 这里有两种算法的实现方式，另一种为非叶节点的关键字数=子节点数-1 (来源维基百科)，虽然他们数据排列结构不一样，但其原理还是一样的Mysql 的B+树是用第一种方式实现）;


 (百度百科算法结构示意图）






 (维基百科算法结构示意图）



特点


1、B+树的层级更少：相较于B树B+每个非叶子节点存储的关键字数更多，树的层级更少所以查询数据更快；

2、B+树查询速度更稳定：B+所有关键字数据地址都存在叶子节点上，所以每次查找的次数都相同所以查询速度要比B树更稳定;

3、B+树天然具备排序功能：B+树所有的叶子节点数据构成了一个有序链表，在查询大小区间的数据时候更方便，数据紧密性很高，缓存的命中率也会比B树高。

4、B+树全节点遍历更快：B+树遍历整棵树只需要遍历所有的叶子节点即可，，而不需要像B树一样需要对每一层进行遍历，这有利于数据库做全表扫描。

B树相对于B+树的优点是，如果经常访问的数据离根节点很近，而B树的非叶子节点本身存有关键字其数据的地址，所以这种数据检索的时候会要比B+树快。

4、B*树

规则


B*树是B+树的变种，相对于B+树他们的不同之处如下：

 (1）首先是关键字个数限制问题，B+树初始化的关键字初始化个数是cei(m/2)，b*树的初始化个数为 (cei(2/3*m)）

 (2）B+树节点满时就会分裂，而B*树节点满时会检查兄弟节点是否满 (因为每个节点都有指向兄弟的指针），如果兄弟节点未满则向兄弟节点转移关键字，如果兄弟节点已满，则从当前节点和兄弟节点各拿出1/3的数据创建一个新的节点出来；



特点


在B+树的基础上因其初始化的容量变大，使得节点空间使用率更高，而又存有兄弟节点的指针，可以向兄弟节点转移关键字的特性使得B*树额分解次数变得更少；

### B+树
关键字个数比孩子结点个数小1

平衡操作不经常发生 

B+ 树是一种树数据结构，通常用于数据库和操作系统的文件系统中。B+ 树的特点是能够保持数据稳定有序，其插入与修改拥有较稳定的对数时间复杂度。B+ 树元素自底向上插入，这与二叉树恰好相反。

1）B+树包含2种类型的结点：内部结点 (也称索引结点）和叶子结点。根结点本身即可以是内部结点，也可以是叶子结点。根结点的关键字个数最少可以只有1个。

2）B+树与B树最大的不同是内部结点不保存数据，只用于索引，所有数据 (或者说记录）都保存在叶子结点中。

3） m阶B+树表示了内部结点最多有m-1个关键字 (或者说内部结点最多有m个子树），阶数m同时限制了叶子结点最多存储m-1个记录。

4）内部结点中的key都按照从小到大的顺序排列，对于内部结点中的一个key，左树中的所有key都小于它，右子树中的key都大于等于它。叶子结点中的记录也按照key的大小排列。

5）每个叶子结点都存有相邻叶子结点的指针，叶子结点本身依关键字的大小自小而大顺序链接。


### B+树的应用

mysql使用B+树作为索引：

B+树相对B树的优点：

①B+树的所有Data域在叶子节点，一般来说都会进行一个优化，就是将所有的叶子节点用指针串联起来，遍历叶子节点就能获取全部数据，这样就能进行区间访问了。

②IO一次读数据是从磁盘上读的，磁盘容量是固定的，取数据量大小是固定的，非叶子节点不存储数据，节点小，磁盘IO次数就少。

作者：听一首老歌
链接：https://www.jianshu.com/p/e3506cee4010
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

>https://zhuanlan.zhihu.com/p/27700617
>https://blog.csdn.net/v_JULY_v/article/details/6530142/
>https://blog.csdn.net/endlu/article/details/51720299
>https://www.cnblogs.com/nullzx/p/8729425.html
>https://csruiliu.github.io/blog/20160605-intro-bptree/
>https://www.cnblogs.com/nullzx/p/8729425.html
>https://www.cnblogs.com/aspirant/p/9214485.html
