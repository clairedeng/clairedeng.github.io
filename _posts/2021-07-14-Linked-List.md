---
title: "Linked List"
layout: post
toc: true
date: 2021-07-14 09:20:00 +0700
categories: [Algorithm]
---


（单）链表是由节点和指针构成的数据结构，每个节点存有一个值，和一个指向下一个节点的指针，因此很多链表问题可以用递归来处理。不同于数组，链表并不能直接获取任意节点的值，必须要通过指针找到该节点后才能获取其值。同理，在未遍历到链表结尾时，我们也无法知道链表的长度，除非依赖其他数据结构储存长度。LeetCode 默认的链表表示方法如下。

```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int x) {
        val = x;
        next = null;
    }
}
```

由于在进行链表操作时，尤其是删除节点时，经常会因为对当前节点进行操作而导致内存或指针出现问题。有两个小技巧可以解决这个问题：一是尽量处理当前节点的下一个节点而非当前节点本身，二是建立一个虚拟节点(dummy node)，使其指向当前链表的头节点，这样即使原链表所有节点全被删除，也会有一个dummy 存在，返回dummy->next 即可。



## 递归反转链表的一部分

什么叫反转单链表的一部分呢，就是给你一个索引区间，让你把单链表中这部分元素反转，其他部分不变： 

反转从位置m到n的链表。请使用一趟扫描完成反转。

说明：

$1\le m\le n$ 链表长度

Example：

输入：1->2->3->4->5->null, M = 2, N =4

输出：1->4->3->2->5->null

**注意这里的索引是从 1 开始的**。迭代的思路大概是：先用一个 for 循环找到第 `m` 个位置，然后再用一个 for 循环将 `m` 和 `n` 之间的元素反转。但是我们的递归解法不用一个 for 循环，纯递归实现反转。 

迭代实现思路看起来虽然简单，但是细节问题很多的，反而不容易写对。相反，递归实现就很简洁优美，下面就由浅入深，先从反转整个单链表说起。 



### 递归反转整个链表

```java
ListNode reverse(ListNode head) {
    if(head.next == null) return head;
    ListNode last = reverse(head.next);
    head.next.next = head;
    head.next = null;
    return last;
}
```

**对于递归算法，最重要的就是明确递归函数的定义**。具体来说，我们的 `reverse` 函数定义是这样的：

**输入一个节点 head，将「以 head 为起点」的链表反转，并返回反转之后的头结点**。

明白了函数的定义，在来看这个问题。比如说我们想反转这个链表：

head

1	->	2	->	3	->	4	->	5	->	6	->	NULL

那么输入 `reverse(head)` 后，会在这里进行递归：

```
ListNode last = reverse(head.next);
```

不要跳进递归（你的脑袋能压几个栈呀？），而是要根据刚才的函数定义，来弄清楚这段代码会产生什么结果：

 head

1	->	reverse(	2	->	3	->	4	->	5	->	6	->	NULL)

这个 `reverse(head.next)` 执行完成后，整个链表就成了这样： 

head								     last

1 	 $\rightarrow$ 	 2 	<-	3	<-	4	<-	5	<-	6

​	       $\downarrow$

​	    NULL

并且根据函数定义，`reverse` 函数会返回反转之后的头结点，我们用变量 `last`接收了。

现在再来看下面的代码：

```java
head.next.next = head;
```

head								     last

1	<->	2	<-	3	<-	4	<-	5	<-	6

接下来：

```java
head.next = null;
return last;
```

head								     			      last

NULL	<-	1	<-	2	<-	3	<-	4	<-	5	<-	6

神不神奇，这样整个链表就反转过来了！递归代码就是这么简洁优雅，不过其中有两个地方需要注意：

1、递归函数要有 base case，也就是这句：

```
if (head.next == null) return head;
```

意思是如果链表只有一个节点的时候反转也是它自己，直接返回即可。

2、当链表递归反转之后，新的头结点是 `last`，而之前的 `head` 变成了最后一个节点，别忘了链表的末尾要指向 null：

```
head.next = null;
```

理解了这两点后，我们就可以进一步深入了，接下来的问题其实都是在这个算法上的扩展。



### 反转链表前 N 个节点

这次我们实现一个这样的函数：

```
// 将链表的前 n 个节点反转（n <= 链表长度）
ListNode reverseN(ListNode head, int n)
```

比如说对于下图链表，执行 `reverseN(head, 3)`： 

head

1	->	2	->	3	->	4	->	5	->	6	->	NULL

reverse(head, 3);

​			    head

1	<-	2	<-	3		4	->	5	->	6	->	NULL

|_________________________________________________________________________________$\uparrow$

解决思路和反转整个链表差不多，只要稍加修改即可： 

```java
ListNode successor = null;
ListNode reverseN(ListNode head, int n) {
    if(n == 1) {
        successor = head.next;
        return head;
    }
    ListNode last = reverseN(head.next, n-1);
    head.next.next = head;
    head.next = successor;
    return last;
}
```

具体的区别：

1、base case 变为 `n == 1`，反转一个元素，就是它本身，同时**要记录后驱节点**。

2、刚才我们直接把 `head.next` 设置为 null，因为整个链表反转后原来的 `head`变成了整个链表的最后一个节点。但现在 `head` 节点在递归反转之后不一定是最后一个节点了，所以要记录后驱 `successor`（第 n + 1 个节点），反转之后将 `head` 连接上。

head			     last	         successor

1	 $\leftarrow$	    2	    $\leftarrow$	3	 		4	 $\rightarrow$	    5	      $\rightarrow$	  6	   $\rightarrow$	       NULL

|_______________________________________________________________________________________________________________$\uparrow$



### 反转链表的一部分

现在解决我们最开始提出的问题，给一个索引区间 `[m,n]`（索引从 1 开始），仅仅反转区间中的链表元素。

```java
ListNode reverseBetween(ListNode head, int m, int n)
```

首先，如果 `m == 1`，就相当于反转链表开头的 `n` 个元素嘛，也就是我们刚才实现的功能：

```java
ListNode reverseBetween(ListNode head, int m, int n) {
    // base case
    if (m == 1) {
        // 相当于反转前 n 个元素
        return reverseN(head, n);
    }
    // ...
}
```

如果 `m != 1` 怎么办？如果我们把 `head` 的索引视为 1，那么我们是想从第 `m`个元素开始反转对吧；如果把 `head.next` 的索引视为 1 呢？那么相对于 `head.next`，反转的区间应该是从第 `m - 1` 个元素开始的；那么对于 `head.next.next` 呢……

区别于迭代思想，这就是递归思想，所以我们可以完成代码：

```java
ListNode reverseBetween(ListNode head, int m, int n) {
    // base case
    if(m == 1) {
        return reverseN(head, n);
    }
    head.next = reverseBetween(head.next, m-1, n-1);
    return head;
}
```



### 最后总结

递归的思想相对迭代思想，稍微有点难以理解，处理的技巧是：不要跳进递归，而是利用明确的定义来实现算法逻辑。

处理看起来比较困难的问题，可以尝试化整为零，把一些简单的解法进行修改，解决困难的问题。

值得一提的是，递归操作链表并不高效。和迭代解法相比，虽然时间复杂度都是 O(N)，但是迭代解法的空间复杂度是 O(1)，而递归解法需要堆栈，空间复杂度是 O(N)。所以递归操作链表可以作为对递归算法的练习或者拿去和小伙伴装逼，但是考虑效率的话还是使用迭代算法更好。



## k个一组反转链表

给你一个链表，每k个节点一组进行翻转，请你返回翻转后的链表。

k是一个正整数，它的值小于或等于链表的长度。

如果节点总数不是k的整数倍，那么请将最后剩余的节点保持原有顺序。

Example:

给定这个链表：$1\rightarrow2\rightarrow3\rightarrow4\rightarrow5\rightarrow \text{null}$

当k=2时，应返回：$2\rightarrow1\rightarrow4\rightarrow3\rightarrow5\rightarrow \text{null}$

当k=3时，应返回：$3\rightarrow2\rightarrow1\rightarrow4\rightarrow5$



### 分析问题

链表是一种兼具递归和迭代性质的数据结构，认真思考一下可以发现**这个问题具有递归性质**。

什么叫递归性质？直接上图理解，比如说我们对这个链表调用 `reverseKGroup(head, 2)`，即以 2 个节点为一组反转链表：

reverseKGroup(head, 2)

head

1       $\rightarrow$       2       $\rightarrow$        3       $\rightarrow$         4         $\rightarrow$         5         $\rightarrow$        NULL

如果我设法把前 2 个节点反转，那么后面的那些节点怎么处理？后面的这些节点也是一条链表，而且规模（长度）比原来这条链表小，这就叫**子问题**。 

​                 newHead              head

1       $\leftarrow$       2                              3       $\rightarrow$         4         $\rightarrow$         5         $\rightarrow$        NULL

|_______________________________>reverseKGroup(head, 2)

我们可以直接递归调用 `reverseKGroup(cur, 2)`，因为子问题和原问题的结构完全相同，这就是所谓的递归性质。

发现了递归性质，就可以得到大致的算法流程：

**1、先反转以 head 开头的 k 个元素**。

​                       head          newHead

NULL       $\leftarrow$        1       $\leftarrow$       2              3       $\rightarrow$         4         $\rightarrow$         5         $\rightarrow$        NULL

**2、将第 k + 1 个元素作为 head 递归调用 reverseKGroup 函数**。

​                                       newHead       head

NULL       $\leftarrow$        1       $\leftarrow$       2              3       $\rightarrow$         4         $\rightarrow$         5         $\rightarrow$        NULL 

​                                                               reverseKGroup(head, 2)

**3、将上述两个过程的结果连接起来**。 

​           newHead       head

1       $\leftarrow$       2              3       $\rightarrow$         4         $\rightarrow$         5         $\rightarrow$        NULL 

|________________________________________________________> reverseKGroup(head, 2)

整体思路就是这样了，最后一点值得注意的是，递归函数都有个 base case，对于这个问题是什么呢？

题目说了，如果最后的元素不足 `k` 个，就保持不变。这就是 base case，待会会在代码里体现。

### 代码实现

首先，我们要实现一个 `reverse` 函数反转一个区间之内的元素。在此之前我们再简化一下，给定链表头结点，如何反转整个链表？ 

```java
//反转以a为头结点的链表
ListNode reverse(ListNode a) {
    ListNode pre = null;
    ListNode cur = a;
    ListNode nxt = a;
    while(cur != null) {
        nxt = cur.next;
        cur.next = pre;
        pre = cur;
        cur = nxt;
    }
    return pre;
}
```

「反转以 `a` 为头结点的链表」其实就是「反转 `a` 到 null 之间的结点」，那么如果让你「反转 `a` 到 `b` 之间的结点」，你会不会？

只要更改函数签名，并把上面的代码中 `null` 改成 `b` 即可：

```java
ListNode reverse(ListNode a, ListNode b) {
    ListNode pre = null;
    ListNode cur = a;
    ListNode nxt = a;
    while(cur != b) {
        nxt = cur.next;
        cur.next = pre;
        pre = cur;
        cur = nxt;
    }
    return pre;
}
```

现在我们迭代实现了反转部分链表的功能，接下来就按照之前的逻辑编写 `reverseKGroup` 函数即可： 

```java
ListNode reverseKGroup(ListNode head, int k) {
    if(head == null) return null;
    ListNode a = head;
    ListNode b = head;
    for(int i = 0; i < k; i++) {
        if(b == null) return head;
        b = b.next;
    }
    ListNode newHead = reverse(a, b);
    a.next = reverseKGroup(b, k);
    return newHead;
}
```

解释一下 `for` 循环之后的几句代码，注意 `reverse` 函数是反转区间 `[a, b)`，所以情形是这样的： 

a         newHead            b

1       $\leftarrow$       2              reverse( 3       $\rightarrow$         4         $\rightarrow$         5         $\rightarrow$        NULL )

|_________________________________________________________________________$\uparrow$





### 最后说两句

从阅读量上看，基本数据结构相关的算法文章看的人都不多，我想说这是要吃亏的。

大家喜欢看动态规划相关的问题，可能因为面试很常见，但就我个人理解，很多算法思想都是源于数据结构的。我们公众号的成名之作之一，「学习数据结构的框架思维」就提过，什么动规、回溯、分治算法，其实都是树的遍历，树这种结构它不就是个多叉链表吗？你能处理基本数据结构的问题，解决一般的算法问题应该也不会太费事。

那么如何分解问题、发现递归性质呢？这个只能多练习，也许后续可以专门写一篇文章来探讨一下，本文就到此为止吧，希望对大家有帮助！



## 高效判断回文单链表

### 判断回文单链表

输入一个单链表的头结点，判断这个链表中的数字是不是回文： 

```java
/**
 * 单链表节点的定义：
 * public class ListNode {
 *     int val;
 *     ListNode next;
 * }
 */

boolean isPalindrome(ListNode head);

输入: 1->2->null
输出: false

输入: 1->2->2->1->null
输出: true
```

这道题的关键在于，单链表无法倒着遍历，无法使用双指针技巧。那么最简单的办法就是，把原始链表反转存入一条新的链表，然后比较这两条链表是否相同。 



其实，**借助二叉树后序遍历的思路，不需要显式反转原始链表也可以倒序遍历链表**，下面来具体聊聊。

对于二叉树的几种遍历方式，我们再熟悉不过了：

```java
void traverse(TreeNode root) {
    // 前序遍历代码
    traverse(root.left);
    // 中序遍历代码
    traverse(root.right);
    // 后序遍历代码
}
```

链表兼具递归结构，树结构不过是链表的衍生。那么，**链表其实也可以有前序遍历和后序遍历**： 

```java
void traverse(ListNode head) {
    // 前序遍历代码
    traverse(head.next);
    // 后序遍历代码
}
```

这个框架有什么指导意义呢？如果我想正序打印链表中的`val`值，可以在前序遍历位置写代码；反之，如果想倒序遍历链表，就可以在后序遍历位置操作：

```java
/* 倒序打印单链表中的元素值 */
void traverse(ListNode head) {
    if (head == null) return;
    traverse(head.next);
    // 后序遍历代码
    print(head.val);
}
```

说到这了，其实可以稍作修改，模仿双指针实现回文判断的功能： 

```java
ListNode left;
boolean isPalindrome(LitNode head) {
    left = head;
    return traverse(head);
}

boolean traverse(ListNode right) {
    if(right == null) return true;
    boolean res = traverse(right.next);
    res = res && (right.val == left.val);
    left = left.next;
    return res;
}
```

当然，无论造一条反转链表还是利用后序遍历，算法的时间和空间复杂度都是 O(N)。下面我们想想，能不能不用额外的空间，解决这个问题呢？ 



### 优化空间复杂度

更好的思路是这样的：

**1、先通过「双指针技巧」中的快慢指针来找到链表的中点**：

```java
ListNode slow, fast;
slow = fast = head;
while (fast != null && fast.next != null) {
    slow = slow.next;
    fast = fast.next.next;
}
// slow 指针现在指向链表中点
```

**2、如果fast指针没有指向null，说明链表长度为奇数，slow还要再前进一步**：

```java
if (fast != null)
    slow = slow.next;
```

**3、从slow开始反转后面的链表，现在就可以开始比较回文串了**：

```java
ListNode left = head;
ListNode right = reverse(slow);

while (right != null) {
    if (left.val != right.val)
        return false;
    left = left.next;
    right = right.next;
}
return true;
```

至此，把上面 3 段代码合在一起就高效地解决这个问题了，其中`reverse`函数很容易实现：

```java
ListNode reverse(ListNode head) {
    ListNode pre = null, cur = head;
    while (cur != null) {
        ListNode nxt = cur.next;
        cur.next = pre;
        pre = cur;
        cur = nxt;
    }
    return pre;
}
```

算法总体的时间复杂度 O(N)，空间复杂度 O(1)，已经是最优的了。

我知道肯定有读者会问：这种解法虽然高效，但破坏了输入链表的原始结构，能不能避免这个瑕疵呢？

其实这个问题很好解决，关键在于得到`p, q`这两个指针位置。这样，只要在函数 return 之前加一段代码即可恢复原先链表顺序：

```
p.next = reverse(q);
```

### 最后总结

首先，寻找回文串是从中间向两端扩展，判断回文串是从两端向中间收缩。对于单链表，无法直接倒序遍历，可以造一条新的反转链表，可以利用链表的后序遍历，也可以用栈结构倒序处理单链表。

具体到回文链表的判断问题，由于回文的特殊性，可以不完全反转链表，而是仅仅反转部分链表，将空间复杂度降到 O(1)。



## 练习

### 2. Add Two Numbers 

You are given two **non-empty** linked lists representing two non-negative integers. The digits are stored in **reverse order**, and each of their nodes contains a single digit. Add the two numbers and return the sum as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

**Example 1:**

```
Input: l1 = [2,4,3], l2 = [5,6,4]
Output: [7,0,8]
Explanation: 342 + 465 = 807.
```

**Example 2:**

```
Input: l1 = [0], l2 = [0]
Output: [0]
```

**Example 3:**

```
Input: l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
Output: [8,9,9,9,0,0,0,1]
```

 

**Constraints:**

- The number of nodes in each linked list is in the range `[1, 100]`.
- `0 <= Node.val <= 9`
- It is guaranteed that the list represents a number that does not have leading zeros.



```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode p = l1;
        ListNode q = l2;
        ListNode dummy = new ListNode(-1);
        ListNode curr = dummy;
        int carry = 0;
        while(p!= null || q != null) {
            int x = (p != null) ? p.val : 0;
            int y = (q != null) ? q.val : 0;
            int sum = x + y + carry;
            carry = sum /10;
            curr.next = new ListNode(sum%10);
            curr = curr.next;
            if(p != null) p = p.next;
            if(q!= null) q = q.next;
        }
        if(carry == 1){
            curr.next = new ListNode(carry);
        }
        return dummy.next;
    }
}
```



### 21. Merge Two Sorted Lists

Merge two sorted linked lists and return it as a **sorted** list. The list should be made by splicing together the nodes of the first two lists.

 

**Example 1:**

```
Input: l1 = [1,2,4], l2 = [1,3,4]
Output: [1,1,2,3,4,4]
```

**Example 2:**

```
Input: l1 = [], l2 = []
Output: []
```

**Example 3:**

```
Input: l1 = [], l2 = [0]
Output: [0]
```

 

**Constraints:**

- The number of nodes in both lists is in the range `[0, 50]`.
- `-100 <= Node.val <= 100`
- Both `l1` and `l2` are sorted in **non-decreasing** order.



```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(-1);
        ListNode curr = dummy;
        ListNode p = l1;
        ListNode q = l2;
        while(p != null && q != null) {
            if(p.val > q.val) {
                curr.next = q;
                q = q.next;
                curr = curr.next;
            } else {
                curr.next = p;
                p = p.next;
                curr = curr.next;
            }
        }
        if(p != null) {
            curr.next = p;
        }
        if(q != null) {
            curr.next = q;
        }
        return dummy.next;
    }
}
```



### 206. Reverse Linked List 

Given the `head` of a singly linked list, reverse the list, and return *the reversed list*.

 

**Example 1:**

```
Input: head = [1,2,3,4,5]
Output: [5,4,3,2,1]
```

**Example 2:**

```
Input: head = [1,2]
Output: [2,1]
```

**Example 3:**

```
Input: head = []
Output: []
```

 

**Constraints:**

- The number of nodes in the list is the range `[0, 5000]`.
- `-5000 <= Node.val <= 5000`

 

**Follow up:** A linked list can be reversed either iteratively or recursively. Could you implement both?



```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode pre = null;
        ListNode cur = head;
        ListNode nxt;
        while(cur != null) {
            nxt = cur.next;
            cur.next = pre;
            pre = cur;
            cur = nxt;
        }
        return pre;
    }
}
```



````java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode reverseList(ListNode head) {
        if(head == null || head.next == null) return head;
        ListNode pre = reverseList(head.next);
        head.next.next = head;
        head.next = null;
        return pre;
    }
}
````



### 24. Swap Nodes in Pairs 

Given a linked list, swap every two adjacent nodes and return its head. You must solve the problem without modifying the values in the list's nodes (i.e., only nodes themselves may be changed.)

 

**Example 1:**

```
Input: head = [1,2,3,4]
Output: [2,1,4,3]
```

**Example 2:**

```
Input: head = []
Output: []
```

**Example 3:**

```
Input: head = [1]
Output: [1]
```

 

**Constraints:**

- The number of nodes in the list is in the range `[0, 100]`.
- `0 <= Node.val <= 100`



```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode swapPairs(ListNode head) {
        if(head == null || head.next == null) return head;
        ListNode last = head.next;
        head.next = swapPairs(head.next.next);
        last.next = head;
        return last;
    }
};
```







### 25. Reverse Nodes in k-Group 

Given a linked list, reverse the nodes of a linked list *k* at a time and return its modified list.

*k* is a positive integer and is less than or equal to the length of the linked list. If the number of nodes is not a multiple of *k* then left-out nodes, in the end, should remain as it is.

You may not alter the values in the list's nodes, only nodes themselves may be changed.

 

**Example 1:**

```
Input: head = [1,2,3,4,5], k = 2
Output: [2,1,4,3,5]
```

**Example 2:**

```
Input: head = [1,2,3,4,5], k = 3
Output: [3,2,1,4,5]
```

**Example 3:**

```
Input: head = [1,2,3,4,5], k = 1
Output: [1,2,3,4,5]
```

**Example 4:**

```
Input: head = [1], k = 1
Output: [1]
```

 

**Constraints:**

- The number of nodes in the list is in the range `sz`.
- `1 <= sz <= 5000`
- `0 <= Node.val <= 1000`
- `1 <= k <= sz`



```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        if(head == null) return head;
        ListNode a = head;
        ListNode b = head;
        for(int i = 0; i < k; i++) {
            if(b == null) return head;
            b = b.next;
        }
        ListNode last = reverse(a, b);
        a.next = reverseKGroup(b, k);
        return last;
    }
    
    private ListNode reverse(ListNode a, ListNode b) {
        ListNode pre = null;
        ListNode cur = a;
        ListNode next;
        while(cur != b) {
            next = cur.next;
            cur.next = pre;
            pre = cur;
            cur = next;
        }
        return pre;
    }
}
```





### 92. Reverse Linked List II 

Given the `head` of a singly linked list and two integers `left` and `right` where `left <= right`, reverse the nodes of the list from position `left` to position `right`, and return *the reversed list*.

 

**Example 1:**

```
Input: head = [1,2,3,4,5], left = 2, right = 4
Output: [1,4,3,2,5]
```

**Example 2:**

```
Input: head = [5], left = 1, right = 1
Output: [5]
```

 

**Constraints:**

- The number of nodes in the list is `n`.
- `1 <= n <= 500`
- `-500 <= Node.val <= 500`
- `1 <= left <= right <= n`



```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode reverseBetween(ListNode head, int left, int right) {
        if(left == 1) return reverse(head, right);
        head.next = reverseBetween(head.next, left - 1, right - 1);
        return head;
    }
    
    private ListNode reverse(ListNode head, int right) {
        ListNode b = head;
        for(int i = 0; i < right; i++)
            b = b.next;
        
        ListNode pre = b;
        ListNode cur = head;
        ListNode next;
        while(cur != b) {
            next = cur.next;
            cur.next = pre;
            pre = cur;
            cur = next;
        }
        return pre;
    }
}
```

