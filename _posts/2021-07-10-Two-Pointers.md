---
title: "Two Pointers"
layout: post
toc: true
date: 2021-07-10 10:20:00 +0700
categories: [Algorithm]
---

双指针主要用于遍历数组，两个指针指向不同的元素，从而协同完成任务。也可以延伸到多个数组的多个指针。
若两个指针指向同一数组，遍历方向相同且不会相交，则也称为滑动窗口（两个指针包围的区域即为当前的窗口），经常用于区间搜索。
若两个指针指向同一数组，但是遍历方向相反，则可以用来进行搜索，待搜索的数组往往是排好序的。



## a

### 142. Linked List Cycle II (Medium)

Given a linked list, return the node where the cycle begins. If there is no cycle, return `null`.

There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the `next` pointer. Internally, `pos` is used to denote the index of the node that tail's `next` pointer is connected to. **Note that pos is not passed as a parameter**.

**Notice** that you **should not modify** the linked list.



#### Examples

Example 1:

> Input: head = [3, 2, 0, -4], pos =  1
>
> Output: tail connects to node index 1



Example 2:

> Input: head = [1], pos = -1
>
> Output: null



#### Solution

给定两个指针，
分别命名为slow 和fast，起始位置在链表的开头。

1. **判定链表中是否含有环** 

每次fast 前进两步，slow 前进一步。如果fast可以走到尽头，那么说明没有环路；如果fast 可以无限走下去，那么说明一定有环路，且一定存在一个时刻slow 和fast 相遇。

1. **已知链表中含有环，返回这个环的起始位置** 

当slow 和fast 第一次相遇时，我们将fast 重新移动到链表开头，并让slow 和fast 每次都前进一步。当slow 和fast 第二次相遇时，相遇的节点即为环路的开始点。

第一次相遇时，假设慢指针 slow 走了 k 步，那么快指针 fast 一定走了 2k 步，也就是说比 slow 多走了 k 步（也就是环的长度）。 

![twopointers1](H:\2021W\Algorithm\twopointers1.png)

设相遇点距环的起点的距离为 m，那么环的起点距头结点 head 的距离为 k - m，也就是说如果从 head 前进 k - m 步就能到达环起点。

巧的是，如果从相遇点继续前进 k - m 步，也恰好到达环起点。

![twopointers2](H:\2021W\Algorithm\twopointers2.png)





```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode slow = head;
        ListNode fast = head;
        do {
            if(fast == null || fast.next == null) return null; //test fast, not slow
            slow = slow.next;
            fast = fast.next.next;
        } while(slow != fast);
        fast = head;
        while(fast != slow) {
            slow = slow.next;
            fast = fast.next;
        }
    }
}
```

Time Complexity: O(n)

Space Complexity: O(n)



### **寻找链表的中点**

类似上面的思路，我们还可以让快指针一次前进两步，慢指针一次前进一步，当快指针到达链表尽头时，慢指针就处于链表的中间位置。

```java
while (fast != null && fast.next != null) {
    fast = fast.next.next;
    slow = slow.next;
}
// slow 就在中间位置
return slow;
```

当链表的长度是奇数时，slow 恰巧停在中点位置；如果长度是偶数，slow 最终的位置是中间偏右。



### **寻找链表的倒数第 k 个元素**

我们的思路还是使用快慢指针，让快指针先走 k 步，然后快慢指针开始同速前进。这样当快指针走到链表末尾 null 时，慢指针所在的位置就是倒数第 k 个链表节点（为了简化，假设 k 不会超过链表长度）：

```java
ListNode slow, fast;
slow = fast = head;
while (k-- > 0) 
    fast = fast.next;

while (fast != null) {
    slow = slow.next;
    fast = fast.next;
}
return slow;
```





## b. 



### 167. Two Sum II - Input array is sorted (Easy)

Given an array of integers `numbers` that is already **sorted in non-decreasing order**, find two numbers such that they add up to a specific `target` number.

Return *the indices of the two numbers (\**1-indexed**) as an integer array* `answer` *of size* `2`*, where* `1 <= answer[0] < answer[1] <= numbers.length`.

The tests are generated such that there is **exactly one solution**. You **may not** use the same element twice.

在一个增序的整数数组里找到两个数，使它们的和为给定值。已知有且只有一对解。



#### Examples

Example 1:

> Input: numbers = [2, 7, 11, 15], target = 9
>
> Output: [1,2]



#### Solution

因为数组已经排好序，我们可以采用方向相反的双指针来寻找这两个数字，一个初始指向最
小的元素，即数组最左边，向右遍历；一个初始指向最大的元素，即数组最右边，向左遍历。
如果两个指针指向元素的和等于给定值，那么它们就是我们要的结果。如果两个指针指向元
素的和小于给定值，我们把左边的指针右移一位，使得当前的和增加一点。如果两个指针指向元
素的和大于给定值，我们把右边的指针左移一位，使得当前的和减少一点。

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int left = 0;
        int right = numbers.length - 1;
        while(left < right) {
            int sum = numbers[left] + numbers[right];
            if(sum == target) {
                break;
            } else if(sum > target) {
                right--; // 注意加减号
            } else {
                left++; // 注意加减号
            }
        }
        int[] result = {left + 1, right + 1};
        return result;
    }
}
```

Time Complexity: O(n)

Space Complexity: O(1)



### 88. Merge Sorted Array (Easy)

You are given two integer arrays `nums1` and `nums2`, sorted in **non-decreasing order**, and two integers `m` and `n`, representing the number of elements in `nums1` and `nums2`respectively.

**Merge** `nums1` and `nums2` into a single array sorted in **non-decreasing order**.

The final sorted array should not be returned by the function, but instead be *stored inside the array* `nums1`. To accommodate this, `nums1` has a length of `m + n`, where the first `m`elements denote the elements that should be merged, and the last `n` elements are set to `0`and should be ignored. `nums2` has a length of `n`.



给定两个有序数组，把两个数组合并为一个。



#### Examples

Example 1:

> Input: nums1 = [1, 2,3,0,0,0], m = 3, nums2 = [2, 5, 6], n = 3
>
> Output: [1,2,2,3,5,6]



Example 2:

> Input: nums1 = [0], m = 0, nums2 = [1], n = 1
>
> Output: [1]



Example 3:

> Input: nums1 = [1], m = 1, nums2 = [], n = 0
>
> Output: [1]



#### Solution

因为这两个数组已经排好序，我们可以把两个指针分别放在两个数组的末尾，即nums1 的 m-1 位和nums2 的n-1 位。每次将较大的那个数字复制到nums1 的后边，然后向前移动一位。因为我们也要定位nums1 的末尾，所以我们还需要第三个指针，以便复制。

在以下的代码里，我们直接利用m 和n 当作两个数组的指针，再额外创立一个pos 指针，起始位置为m+n-1。每次向前移动m 或n 的时候，也要向前移动pos。这里需要注意，如果nums1的数字已经复制完，不要忘记把nums2 的数字继续复制；如果nums2 的数字已经复制完，剩余nums1 的数字不需要改变，因为它们已经被排好序。

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int indexM = m-1;
        int indexN = n-1;
        int i;
        for(i = nums1.length - 1; i >= 0 && indexM >= 0 && indexN >= 0; i--) {
            if(nums1[indexM] >= nums2[indexN]) {
                nums1[i] = nums1[indexM];
                indexM--;
            } else {
                nums1[i] = nums2[indexN];
                indexN--;
            }
        }
        while(indexN >= 0) { // no need for copy nums1 leftovers, because they are same array,only copy nums2 leftovers
            nums1[i] = nums2[indexN];
            indexN--;
            i--;
        }
    }
}
```

Time Complexity: O(m + n)

Space Complexity: O(1)



### 76. Minimum Window Substring (Hard)

Given two strings `s` and `t` of lengths `m` and `n` respectively, return the **minimum window substring** of `s` such that every character in `t` (**including duplicates**) is included in the window. If there is no such substring, return the empty string `""`*.*

The testcases will be generated such that the answer is **unique**.

A **substring** is a contiguous sequence of characters within the string.

给定两个字符串S 和T，求S 中包含T 所有字符的最短连续子字符串的长度，同时要求时间复杂度不得超过O¹nº。



#### Examples

Example 1:

> Input: s = "ADOBECODEBANC", t = "ABC"
>
> Output: "BANC"



Example 2:

> Input: s = "a", t = "a"
>
> Output: "a"



Example 3:

> Input: s = "a", t = "aa"
>
> Output: ""



#### Solution

滑动窗口算法的思路是这样：

1、我们在字符串 S 中使用双指针中的左右指针技巧，初始化 left = right = 0，把索引闭区间 [left, right] 称为一个「窗口」。

2、我们先不断地增加 right 指针扩大窗口 [left, right]，直到窗口中的字符串符合要求（包含了 T 中的所有字符）。

3、此时，我们停止增加 right，转而不断增加 left 指针缩小窗口 [left, right]，直到窗口中的字符串不再符合要求（不包含 T 中的所有字符了）。同时，每次增加 left，我们都要更新一轮结果。

4、重复第 2 和第 3 步，直到 right 到达字符串 S 的尽头。



```java

```

