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





## 练习

### 3. longest substring without repeating character

Given a string `s`, find the length of the **longest substring** without repeating characters. 



```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int[] m = new int[256];
        Arrays.fill(m, -1);
        int result = 0; 
        int left = -1;
        int len = s.length();
        for(int i = 0; i < len; i++) {
            left = Math.max(left, m[s.charAt(i)]);
            m[s.charAt(i)] = i;
            result = Math.max(result, i-left);
        }
        return result;
    }
}
```

Time Complexity: O(n)

Space Complexity: O(n)



### 15. 3Sum

Given an integer array nums, return all the triplets `[nums[i], nums[j], nums[k]]` such that `i != j`, `i != k`, and `j != k`, and `nums[i] + nums[j] + nums[k] == 0`.

Notice that the solution set **must not** contain **duplicate triplets**.



```java
class Solution {
    public List<List<Integer>> threeSum(int[] num) {
    	Arrays.sort(num);
    	List<List<Integer>> res = new LinkedList<>(); 
    	for (int i = 0; i < num.length-2; i++) {
        	if (i == 0 || (i > 0 && num[i] != num[i-1])) { // non duplicate
            	int lo = i+1, hi = num.length-1, sum = 0 - num[i];
            	while (lo < hi) {
                	if (num[lo] + num[hi] == sum) {
                    	res.add(Arrays.asList(num[i], num[lo], num[hi]));
                    	while (lo < hi && num[lo] == num[lo+1]) lo++; // non duplicate
                    	while (lo < hi && num[hi] == num[hi-1]) hi--; //non duplicate
                    	lo++; hi--;
                	} else if (num[lo] + num[hi] < sum) lo++;
                	else hi--;
           		}
        	}
    	}
    	return res;
	}
}
```

Time Complexity: O(n^2)

Space Complexity: O(1)



### 11. Container With Most Water

Given `n` non-negative integers `a1, a2, ..., an` , where each represents a point at coordinate `(i, ai)`. `n` vertical lines are drawn such that the two endpoints of the line `i` is at `(i, ai)` and `(i, 0)`. Find two lines, which, together with the x-axis forms a container, such that the container contains the most water.

**Notice** that you may not slant the container.



```java
class Solution {
    public int maxArea(int[] height) {
        int result = 0;
        int left = 0;
        int right = height.length-1;
        while(left < right) {
            result = Math.max(result, Math.min(height[left], height[right]) * (right - left));
            if(height[left] < height[right]) left++;
            else right--;
        }
        return result;
    }
}
```



### 16. 3Sum Closest

Given an array `nums` of *n* integers and an integer `target`, find three integers in `nums` such that the sum is closest to `target`. Return the sum of the three integers. You may assume that each input would have exactly one solution. 

```java
Input: nums = [-1,2,1,-4], target = 1
Output: 2
Explanation: The sum that is closest to the target is 2. (-1 + 2 + 1 = 2).
```



**Constraints:**

- `3 <= nums.length <= 10^3`
- `-10^3 <= nums[i] <= 10^3`
- `-10^4 <= target <= 10^4`



```java
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        int closest = nums[0] + nums[1] + nums[2];
        Arrays.sort(nums);
        for(int i = 0 ; i < nums.length - 2; i++) {
            int left = i + 1;
            int right = nums.length - 1;
            while(left < right) {
                int diff1 = Math.abs(target - closest);
                int sum = nums[left] + nums[right] + nums[i];
                int diff2 = Math.abs(target - sum);
                if(diff2 < diff1) closest = sum;
                else if(sum > target) {
                    right--;
                } else {
                    left++;
                }
            }
        }
        return closest;
    }
}
```



### 26.  Remove Duplicates from Sorted Array

Given an integer array `nums` sorted in **non-decreasing order**, remove the duplicates [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) such that each unique element appears only **once**. The **relative order** of the elements should be kept the **same**.

Since it is impossible to change the length of the array in some languages, you must instead have the result be placed in the **first part** of the array `nums`. More formally, if there are `k` elements after removing the duplicates, then the first `k` elements of `nums` should hold the final result. It does not matter what you leave beyond the first `k` elements.

Return `k` *after placing the final result in the first* `k` *slots of* `nums`.

Do **not** allocate extra space for another array. You must do this by **modifying the input array in-place**with O(1) extra memory.

**Custom Judge:**

The judge will test your solution with the following code:

```
int[] nums = [...]; // Input array
int[] expectedNums = [...]; // The expected answer with correct length

int k = removeDuplicates(nums); // Calls your implementation

assert k == expectedNums.length;
for (int i = 0; i < k; i++) {
    assert nums[i] == expectedNums[i];
}
```

If all assertions pass, then your solution will be **accepted**.

**Example 1:**

```
Input: nums = [1,1,2]
Output: 2, nums = [1,2,_]
Explanation: Your function should return k = 2, with the first two elements of nums being 1 and 2 respectively.
It does not matter what you leave beyond the returned k (hence they are underscores).
```

**Example 2:**

```
Input: nums = [0,0,1,1,1,2,2,3,3,4]
Output: 5, nums = [0,1,2,3,4,_,_,_,_,_]
Explanation: Your function should return k = 5, with the first five elements of nums being 0, 1, 2, 3, and 4 respectively.
It does not matter what you leave beyond the returned k (hence they are underscores).
```

 

**Constraints:**

- `0 <= nums.length <= 3 * 104`
- `-100 <= nums[i] <= 100`
- `nums` is sorted in **non-decreasing** order.



```java
class Solution {
    public int removeDuplicates(int[] nums) {
        int left = 1;
        int right = 1;
        while(right < nums.length) {
            if(nums[right] == nums[right - 1]) {
                right++;
            } else {
                nums[left++] = nums[right++];
            }
        }
        return left;
    }
}
```



### 86. Partition List

Given the `head` of a linked list and a value `x`, partition it such that all nodes **less than** `x` come before nodes **greater than or equal** to `x`.

You should **preserve** the original relative order of the nodes in each of the two partitions.

**Example 1:**

```
Input: head = [1,4,3,2,5,2], x = 3
Output: [1,2,2,4,3,5]
```

**Example 2:**

```
Input: head = [2,1], x = 2
Output: [1,2]
```

 

**Constraints:**

- The number of nodes in the list is in the range `[0, 200]`.
- `-100 <= Node.val <= 100`
- `-200 <= x <= 200`





```java
class Solution {
    public ListNode partition(ListNode head, int x) {
        ListNode dummy = new ListNode(-1);
        dummy.next = head;
        ListNode pre = dummy;
        ListNode cur;
        while(pre.next!= null && pre.next.val < x) pre = pre.next;
        cur = pre;
        while(cur.next != null) {
            if(cur.next.val < x) {
                ListNode tmp = cur.next;
                cur.next = tmp.next;
                tmp.next = pre.next;
                pre.next = tmp;
                pre = pre.next;
            } else {
                cur = cur.next;
            }
        }
        return dummy.next;
        
    }
}
```





### 88. Merge Sort Array

You are given two integer arrays `nums1` and `nums2`, sorted in **non-decreasing order**, and two integers `m` and `n`, representing the number of elements in `nums1` and `nums2` respectively.

**Merge** `nums1` and `nums2` into a single array sorted in **non-decreasing order**.

The final sorted array should not be returned by the function, but instead be *stored inside the array* `nums1`. To accommodate this, `nums1` has a length of `m + n`, where the first `m` elements denote the elements that should be merged, and the last `n` elements are set to `0` and should be ignored. `nums2`has a length of `n`.

 

**Example 1:**

```
Input: nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
Output: [1,2,2,3,5,6]
Explanation: The arrays we are merging are [1,2,3] and [2,5,6].
The result of the merge is [1,2,2,3,5,6] with the underlined elements coming from nums1.
```

**Example 2:**

```
Input: nums1 = [1], m = 1, nums2 = [], n = 0
Output: [1]
Explanation: The arrays we are merging are [1] and [].
The result of the merge is [1].
```

**Example 3:**

```
Input: nums1 = [0], m = 0, nums2 = [1], n = 1
Output: [1]
Explanation: The arrays we are merging are [] and [1].
The result of the merge is [1].
Note that because m = 0, there are no elements in nums1. The 0 is only there to ensure the merge result can fit in nums1.
```

 

**Constraints:**

- `nums1.length == m + n`
- `nums2.length == n`
- `0 <= m, n <= 200`
- `1 <= m + n <= 200`
- `-109 <= nums1[i], nums2[j] <= 109`



```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int len = m + n;
        int i = len - 1;
        while(n > 0 && m > 0) {
            if(nums1[m-1] > nums2[n-1]) {
                nums1[i] = nums1[m-1]; 
                m--;
            } else {
                nums1[i] = nums2[n-1];
                n--;
            }
            i--;
        }
        while(m > 0) {
            nums1[i] = nums1[m-1];
            m--;
            i--;
        }
        while(n > 0) {
            nums1[i] = nums2[n-1];
            n--;
            i--;
        }
    }
}
```





### 18. 4Sum

Given an array `nums` of `n` integers, return *an array of all the \**unique** quadruplets* `[nums[a], nums[b], nums[c], nums[d]]` such that:

- `0 <= a, b, c, d < n`
- `a`, `b`, `c`, and `d` are **distinct**.
- `nums[a] + nums[b] + nums[c] + nums[d] == target`

You may return the answer in **any order**.

 

**Example 1:**

```
Input: nums = [1,0,-1,0,-2,2], target = 0
Output: [[-2,-1,1,2],[-2,0,0,2],[-1,0,0,1]]
```

**Example 2:**

```
Input: nums = [2,2,2,2,2], target = 8
Output: [[2,2,2,2]]
```

 

**Constraints:**

- `1 <= nums.length <= 200`
- `-109 <= nums[i] <= 109`
- `-109 <= target <= 109`



```java
class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        Arrays.sort(nums);
        List<List<Integer>> result = new LinkedList<>();
        for(int i = 0; i < nums.length - 3; i++) {
            if( i > 0 && nums[i] == nums[i-1]) continue;
            for(int j = i+1; j < nums.length - 2; j++) {
                int left = j + 1;
                int right = nums.length - 1;
                if(j > i + 1 && nums[j] == nums[j-1]) continue;
                while(left < right) {
                    int sum = nums[i] + nums[j] + nums[left] + nums[right];
                    if(sum == target) {
                        result.add(Arrays.asList(nums[i], nums[j], nums[left], nums[right]));
                        while(left < right && nums[left] == nums[left+1]) left++;
                        while(left < right && nums[right] == nums[right - 1]) right--;
                        left++;
                        right--;
                    } else if (sum > target) {
                        right--;
                    }  else {
                        left++;
                    }
                }
            } 
        }
        return result;
    }
}
```



### 167. TwoSum II - Input array is sorted

Given an array of integers `numbers` that is already **sorted in non-decreasing order**, find two numbers such that they add up to a specific `target` number.

Return *the indices of the two numbers (\**1-indexed**) as an integer array* `answer` *of size* `2`*, where* `1 <= answer[0] < answer[1] <= numbers.length`.

The tests are generated such that there is **exactly one solution**. You **may not** use the same element twice.

 

**Example 1:**

```
Input: numbers = [2,7,11,15], target = 9
Output: [1,2]
Explanation: The sum of 2 and 7 is 9. Therefore index1 = 1, index2 = 2.
```

**Example 2:**

```
Input: numbers = [2,3,4], target = 6
Output: [1,3]
```

**Example 3:**

```
Input: numbers = [-1,0], target = -1
Output: [1,2]
```

 

**Constraints:**

- `2 <= numbers.length <= 3 * 104`
- `-1000 <= numbers[i] <= 1000`
- `numbers` is sorted in **non-decreasing order**.
- `-1000 <= target <= 1000`
- The tests are generated such that there is **exactly one solution**.

s

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int left = 0;
        int right = numbers.length - 1;
        int[] result = new int[2];
        while(left < right) {
            int sum = numbers[left] + numbers[right];
            if(sum == target) {
                result[0] = left + 1;
                result[1] = right + 1;
                return result;
            } else if(sum > target) {
                right--;
            } else {
                left++;
            }
        }
        return result;
    }
}
```



### 30. Substring with Concatenation of All words



```java
class Solution {
    public List<Integer> findSubstring(String s, String[] words) {
        List<Integer> result = new LinkedList<Integer>();
        int n = words.length;
        int lenword = words[0].length();
        int len = s.length();
        HashMap<String, Integer> wordCnt = new HashMap<>();
        for(String word : words) {
            if(wordCnt.get(word)!= null)
                wordCnt.replace(word, wordCnt.get(word)+1);
            else
                wordCnt.put(word, 1);
        }
        for(int i = 0; i <= len - n*lenword; i++) {
            HashMap<String, Integer> strCnt = new HashMap<String, Integer>();
            int j;
            for(j = 0; j < n; j++) {
                String t = s.substring(i + j*lenword, i + (j+1)*lenword);
                if(wordCnt.get(t) == null) break;
                if(strCnt.get(t) == null) strCnt.put(t, 1);
                else strCnt.replace(t, strCnt.get(t)+1);
                if(strCnt.get(t) > wordCnt.get(t)) break;
            }
            if(j == n) result.add(i);
        }
        return result;
        
    }
}
```

Time Complexity: O(n^2)

Space Complexity: O(n)



### 19. Remove Nth Node From End of List

Given the `head` of a linked list, remove the `nth` node from the end of the list and return its head. 

**Example 1:**

```
Input: head = [1,2,3,4,5], n = 2
Output: [1,2,3,5]

```

**Example 2:**

```
Input: head = [1], n = 1
Output: []

```

**Example 3:**

```
Input: head = [1,2], n = 1
Output: [1]

```

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
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(-1);
        dummy.next = head;
        int length = 0;
        ListNode first = head;
        while(first != null) {
            length++;
            first = first.next;
        }
        length -= n;
        first = dummy;
        while(length > 0) {
            length--;
            first = first.next;
        }
        first.next = first.next.next;
        return dummy.next;
    }
}
```



### 287 Find the duplicate Number

Given an array of integers `nums` containing `n + 1` integers where each integer is in the range `[1, n]` inclusive.

There is only **one repeated number** in `nums`, return *this repeated number*.

You must solve the problem **without** modifying the array `nums` and uses only constant extra space.

 

**Example 1:**

```
Input: nums = [1,3,4,2,2]
Output: 2

```

**Example 2:**

```
Input: nums = [3,1,3,4,2]
Output: 3

```

**Example 3:**

```
Input: nums = [1,1]
Output: 1

```

**Example 4:**

```
Input: nums = [1,1,2]
Output: 1

```

 

**Constraints:**

- `1 <= n <= 105`
- `nums.length == n + 1`
- `1 <= nums[i] <= n`
- All the integers in `nums` appear only **once** except for **precisely one integer** which appears **two or more** times.



```java
class Solution {
    public int findDuplicate(int[] nums) {
        int slow = nums[0];
        int fast = nums[0];
        do {
            slow = nums[slow];
            fast = nums[nums[fast]];
        } while(slow != fast);
        slow = nums[0];
        while(slow != fast) {
            slow = nums[slow];
            fast = nums[fast];
        }
        return slow;
    }
}
```





### 349. Intersection of Two Arrays

```java
class Solution {
  public int[] intersection(int[] nums1, int[] nums2) {
    HashSet<Integer> set1 = new HashSet<Integer>();
    for (Integer n : nums1) set1.add(n);
    HashSet<Integer> set2 = new HashSet<Integer>();
    for (Integer n : nums2) set2.add(n);

    set1.retainAll(set2);

    int [] output = new int[set1.size()];
    int idx = 0;
    for (int s : set1) output[idx++] = s;
    return output;
  }
}
```

