[suanfa.md](https://github.com/gailunking/garen-store/files/14821868/suanfa.md)[Upl# sql

1. distinct去重 放在字段前面

## 什么是算法？

算法就是通过特定的方式，尽可能快速高效的来达成我们对一组数据的处理要求。

好的算法适合某种场景，拥有良好的时间复杂度或者空间复杂度（现在已经基本不做重要考虑）

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=N2IzYjZkYzI2MDgxOGIyNzI5NjY1NDNkMjI1NmU4NmVfYzFhcWxuRFhsTkZVNEFiaklCejJrdm5wVDZBeEdQVnpfVG9rZW46WTMxTGJMM0tvb2w0aG54U1U3M2NnaUgxbkVlXzE3MTE5NjI1Nzg6MTcxMTk2NjE3OF9WNA)

## 如何刷算法：

一定不要为了刷算法而刷算法，要通过算法题，搞清一种，一类题的解题思路

方式：刷完后，24小时后再刷一遍，一周后再复习一遍，等面试前再复盘一遍

#### 时间复杂度：

算法执行的时间随着数据规模增长的关系

空间复杂度 空间占用随问题规模的变大的关系

### 时间复杂度分析：

1. 只关注循环执行次数最多的那一段代码
2. 复杂度为，最高阶的复杂度如n方+n+1 则是O（n²）
3. 嵌套循环 是复杂度相乘 如 则是n*n n²

for（i<n；i--）{

Sout

com（）

}

com{

for（n）

}

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ZWFmYjhmNjAzMDY2NTVlMDQ5M2VjMDFiYjkxNDBiYzNfTU1hSGRneE1DblZtOXVZd1FZUENMbWNCZmltcno3bjBfVG9rZW46TnIwamJJQlVGbzFrVmV4WGp2YmM1cWcwbjFkXzE3MTE5NjI1Nzg6MTcxMTk2NjE3OF9WNA)

## 递归：

1. 一个问题可以分解为多个子问题
2. 除了问题规模不同，解法完全相同
3. 有终止条件

# 排序：

## 冒泡排序：

就是从第一个元素开始，判断相邻两个元素，满足则交换，每次排序将一个最值移动到一端时间复杂度O（n²），空间复杂度O（1），设置一个布尔值，如果某次遍历中没有发生元素交换，说明已经有序，可以停止排序了

动图就是这样

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=ODY2ZTY2ZjFlMTQ0MjljYzJlMzM2NDk2ZmM0NGEwNTBfbnozRGh3YVJISlhkTVU3VU95M0dRVUt4NHBxRmphM1hfVG9rZW46TXhCS2JnRms2b1d5ejR4M1hrY2MzNTVibmNiXzE3MTE5NjI1Nzg6MTcxMTk2NjE3OF9WNA)

https://www.runoob.com/wp-content/uploads/2019/03/bubbleSort.gif

## 快速排序：

快速排序是算法中经常使用到一个排序手段，它的排序效率非常可观，平均时间复杂度O（nlogN）但平时应对大多数使用场景下都要优于这个，最坏的时间复杂度是O（n²），它的主要思想分这个几个步骤

1. 从一组元素中选出一个基准值 可以是最大，最小 或者随机值
2. 进行重排序，将小于基准的放左边，大于基准的放右边，形成两个子序列
3. 对子序列做同样操作，一直递归
4. 将结果合并，得到排序完成的序列

暂时无法在飞书文档外展示此内容

## 堆排序：

# 动态规划：

[70. 爬楼梯 - 力扣（LeetCode）](https://leetcode.cn/problems/climbing-stairs/) leetcode70 爬楼梯问题 

每次一格或者两格，有几种爬法，假如从终点往回迈，大致两条线路 第一次迈1，第一次迈2，所以是f（n-1）+f（n-2）种迈法，然后假如再从f（n-1）开始，依次类推，最后剩下1是1种，剩下2是2种，所以到f（1），f（2）直接返回次数就好了，是一个如图所示的形式。

但是我们发现，好多是重复使用到了，会导致超时，假如是20，则19，18分别那就f（3）要算n-16+3遍，其他的也是，越大的n，需要重算的数的次数越多，那我们可以考虑，在第一次用到它时，就存储起来，然后下次获取时判断有直接拿就好了，省去大量时间，由很高的时间复杂度变成低的。

暂时无法在飞书文档外展示此内容

非递归方法：就是直接从f（1）+f（2）开始往上加，一直加到f（n）；

# 数组

#### [1. 两数之和 - 力扣（LeetCode）](https://leetcode.cn/problems/two-sum/)求两数之和等于目标值的下标

1. 暴力解法，for循环嵌套，把所有的组合列出来，返回可以的那俩 时间复杂度O（n²）
2. 哈希表法（实际就是用hashmap存储已经遍历过的值的思想，和爬楼梯，斐波那契一个思路）

对于每个元素，求一下它的另一个值，需要多少，完事直接拿着目标值，去map中判断是否存在，存在直接返回存储的下标与当前正在遍历的下标值（因为默认只有一个有效解）；

1. #### [88. 合并两个有序数组 - 力扣（LeetCode）](https://leetcode.cn/problems/merge-sorted-array/)e）

   1. 合并两个有序数组，对于两个有序的数组，第一个数组大小为m+n有效元素m个，第二个有效元素数组大小为n，都是升序，要求合并到nums1中，保持升序。
   2. 第一个方法，直接把nums2放到1后面，然后排序
   3. 方法二，双指针法，利用好给的题目所有条件，如图，m+n，有序，两个条件全部用上，大的往index放，然后指针移动。

   暂时无法在飞书文档外展示此内容

1. #### [力扣（LeetCode）官网 - 全球极客挚爱的技术成长平台](https://leetcode.cn/problems/move-zeroes/description/) 283 移动0 借鉴上面那个 用双指针 然后移动的思路

1. #### [力扣（LeetCode）官网 - 全球极客挚爱的技术成长平台](https://leetcode.cn/problems/find-all-numbers-disappeared-in-an-array/description/) 448 找到数组中消失的数字 

数组有n个元素，在1-n之间，找出在1-n之间但不在数组中的元素

1. 我自己的方法 哈希法，把出现的从哈希中剔除，剩下的就是不在的
2. 通过下标去考虑 ，因为这个题目所有的数值都控制在n之内，和下标是一定能产生联系的，比如把数组元素值对n取模然后减一后为下标的元素变成负数 或者让他大于N， 

# 链表：

1. ### [力扣（LeetCode）官网 - 全球极客挚爱的技术成长平台](https://leetcode.cn/problems/merge-two-sorted-lists/) 合并两个有序链表 

   1. 、要搞清楚链表这个结构，搞清楚几个概念 如图每一个链表 代表的只是本身 和她的next节点 具体再下一个 需要去看next节点的属性，所以只要连起来了，首节点就代表是一连串的这个链表
   2. 可以双指针+循环解决
   3. 递归解决

暂时无法在飞书文档外展示此内容

1. [力扣（LeetCode）官网 - 全球极客挚爱的技术成长平台](https://leetcode.cn/problems/merge-two-sorted-lists/description/) leetcode83 删除有序链表的重复元素，怎么说呢，进一步理解一下链表这个数据结构，next，指针相关的 
   1. 除了普通方法，可以使用递归方法，本质是将链表压栈和倒序处理

**感悟：递归的使用，当我们删除链表的一个元素以后，这个链表就变短了，然后后续的处理，和我们对初始链表的处理是一样的，代表可以使用递归处理这个问题，可以联想到递归的思想**

1. 环形链表[1-012-(LeetCode-141) 环形链表_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1eg411w7gn/?p=13&spm_id_from=333.788.top_right_bar_window_history.content.click&vd_source=5b30f2782c6824c5476fe72a2d8cd893)

每一个节点都存入set，判断下一个节点有重复的，说明有环

**环形列表通用解法： 快慢双指针判断是否重叠法（弗洛伊德法）**

1. 环形链表II [力扣（LeetCode）官网 - 全球极客挚爱的技术成长平台](https://leetcode.cn/problems/linked-list-cycle-ii/solutions/2364458/javapython3ckuai-man-zhi-zhen-shu-xue-tu-5ifb/?envType=study-plan-v2&envId=top-100-liked)  与上述类似，不过变成了存在则返回入环节点，而不仅仅是true。

# 树：

1. #### 二叉树中序遍历 [力扣（LeetCode）官网 - 全球极客挚爱的技术成长平台](https://leetcode.cn/problems/binary-tree-inorder-traversal/submissions/499653386/?envType=study-plan-v2&envId=top-100-liked)

递归方式

前序遍历92

后序遍历145

三者思路都是相同的，递归方式就是对于每个元素的判断情况都一样，在递归外面控制一下条件左右顺序即可

非递归的方式，就是利用栈这个数据结构，将元素入栈，然后出现条件的时候弹栈拿到路过的元素，然后判别就可以了。

1. #### 二叉判断是否是轴对称[101. 对称二叉树 - 力扣（LeetCode）](https://leetcode.cn/problems/symmetric-tree/submissions/518188254/)

递归，非递归实现 非递归考虑队列这个数据结构 Queue

1. #### [104. 二叉树的最大深度 - 力扣（LeetCode）](https://leetcode.cn/problems/maximum-depth-of-binary-tree/submissions/518199129/)

## 体会

通过这个题目感受到，题目中给的条件，如果**没有全部利用到**，说**明一定有可以利用，可以优化到的点**，

# 令牌桶算法

一个桶会每隔一段时间生成一批令牌，请求进入之前，要获取到令牌才能继续跟进，否则不可以oading suanfa.md…]()
