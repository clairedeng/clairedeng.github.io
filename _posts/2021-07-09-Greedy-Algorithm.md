---
title: "Greedy Algorithm"
layout: post
toc: true
date: 2021-07-09 10:11:00 +0700
categories: [Leetcode, Greedy Algorithm]
---

贪心算法可以认为是动态规划算法的一个特例，相比动态规划，使用贪心算法需要满足更多的条件（贪心选择性质），但是效率比动态规划要高。 

贪心选择性质：每一步都做出一个局部最优的选择，最终的结果就是全局最优。注意哦，这是一种特殊性质，其实只有一部分问题拥有这个性质。 

比如你面前放着 100 张人民币，你只能拿十张，怎么才能拿最多的面额？显然每次选择剩下钞票中面值最大的一张，最后你的选择一定是最优的。

然而，大部分问题不具有贪心选择性质。比如打斗地主，对手出对儿三，按照贪心策略，你应该出尽可能小的牌刚好压制住对方，但现实情况我们甚至可能会出王炸。这种情况就不能用贪心算法，而得使用动态规划解决，参见前文「动态规划解决博弈问题」。

| 问题     |                                                              |
| -------- | ------------------------------------------------------------ |
| 分配问题 | [455. Assign Cookies](https://leetcode.com/problems/assign-cookies/) |
|          | [135. Candy](https://leetcode.com/problems/candy/)           |
| 区间问题 | [435. Non overlapping intervals]()                           |



## 455. Assign Cookies

### Question Description

Assume you are an awesome parent and want to give your children some cookies. But, you should give each child at most one cookie.

Each child `i` has a greed factor `g[i]`, which is the minimum size of a cookie that the child will be content with; and each cookie `j` has a size `s[j]`. If `s[j] >= g[i]`, we can assign the cookie `j` to the child `i`, and the child `i` will be content. Your goal is to maximize the number of your content children and output the maximum number.

有一群孩子和一堆饼干，每个孩子有一个饥饿度，每个饼干都有一个大小。每个孩子只能吃一个饼干，且只有饼干的大小不小于孩子的饥饿度时，这个孩子才能吃饱。求解最多有多少孩子可以吃饱。

### Constraints

What if size array is empty, what's the output?

return 0, no cookies satisfy requirements.

Can child array be empty?

at least one child, so g.length>= 1.

Will the size element and child greed factor below 0?

$1 \le g[i], s[j] \le 2^{31} - 1$ 

Are the two array sorted?

No.



### Examples

Example 1:

> Input: g = [1,2] (children), s = [1,2,3] (cookies)
>
> Output: 2



Example 2:

> Input: g = [1], s = []
>
> Output: 0



### Solution

贪心策略是，给剩余孩子里最小饥饿度的孩子分配最小的能饱腹的饼干。



```java
class Solution {
    public int findContentChildren(int[] g, int[] s) {
        Arrays.sort(g);
        Arrays.sort(s);
        int indexG = 0;
        int indexS = 0;
        while(indexG < g.length && indexS < s.length) {
            if(g[indexG] <= s[indexS]) indexG++;
            indexS++;
        }
        return indexG;
    }
}
```

Runtime Complexity: $O(n\log n)$

Space Complexity: $O(1)$



## 135. Candy

There are `n` children standing in a line. Each child is assigned a rating value given in the integer array `ratings`.

You are giving candies to these children subjected to the following requirements:

- Each child must have at least one candy.
- Children with a higher rating get more candies than their neighbors.

Return *the minimum number of candies you need to have to distribute the candies to the children*.

一群孩子站成一排，每一个孩子有自己的评分。现在需要给这些孩子发糖果，规则是如果一个孩子的评分比自己身旁的一个孩子要高，那么这个孩子就必须得到比身旁孩子更多的糖果；所有孩子至少要有一个糖果。求解最少需要多少个糖果。



### Constraints

What is the range of the array elements? Can it below 0?

$0 \le \text{ratings}[i] \le 2*10^4$

Can it be empty array?

No, at least one child in the list. 

n == rating.length

$1 \le n \le 2*10^4$



### Examples

Example 1:

> Input: ratings = [1,0,2]
>
> Output: 5



Example 2:

> Input: ratings = [1,2,2]
>
> Output: 4



### Solution

贪心策略：在每次遍历中，只考虑并更新相邻一侧的大小关系。

只需要简单的两次遍历即可：把所有孩子的糖果数初始化为1；先从左往右遍历一遍，如果右边孩子的评分比左边的高，则右边孩子的糖果数更新为左边孩子的糖果数加1；再从右往左遍历一遍，如果左边孩子的评分比右边的高，且左边孩子当前的糖果数不大于右边孩子的糖果数，则左边孩子的糖果数更新为右边孩子的糖果数加1。通过这两次遍历，分配的糖果就可以满足题目要求了。

```java
class Solution {
    public int candy(int[] ratings) {
        int[] result = new int[ratings.length];
        for(int i = 0; i < result.length; i++)
            result[i] = 1;
        for(int i = 1; i < ratings.length; i++) {
            if(ratings[i] > ratings[i-1])
                result[i] = result[i-1]+1;
        }
        for(int i = ratings.length - 2; i >= 0; i--) {
            if(ratings[i] > ratings[i+1])
                result[i] = Math.max(result[i+1] + 1, result[i]); // max of original and new result, important
        }
        int sum = 0;
        for(int i = 0; i < result.length; i++)
            sum += result[i];
        return sum;
    }
}
```

Time Complexity: O(n)

Space Complexity: O(n)



## 435. Non-overlapping Intervals

Given an array of intervals `intervals` where `intervals[i] = [starti, endi]`, return *the minimum number of intervals you need to remove to make the rest of the intervals non-overlapping*. 

给定多个区间，计算让这些区间互不重叠所需要移除区间的最少个数。起止相连不算重叠。



### Constraints

What's going to happen if the array is empty?

the size of the interval is $1 \le size \le 20000$.

is the array of the interval sorted?

no

Can array elements below 0?

$-2*10^4 \le start < end \le 2*10^4$



### Examples

Example 1:

> Input: [[1,3], [4,5], [1,5]]
>
> Output: 1



Example 2:

> Input: [[1,2], [2,3]]
>
> Output: 0



### Solution

贪心策略：优先保留**结束最早的（结尾小）**且不相交的区间。

具体实现方法为，先把区间按照结尾的大小进行增序排序，每次选择结尾最小且和前一个选择的区间不重叠的区间。

```java
class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {
        Arrays.sort(intervals, (a, b) -> a[1] - b[1]); // important, wrong, integer overflow
        int end = intervals[0][1]; // find smallest end point interval
        int count = 0;
        for(int i = 1; i < intervals.length; i++) {
            if(end <= intervals[i][0]) {
                end = intervals[i][1];
            } else {
                count++;
            }
        }
        return count;
    }
}
```

Time complexity: $O(n\log n)$

Space Complexity: $O(1)$



## Practice

| 问题     |                                                          |
| -------- | -------------------------------------------------------- |
| 基础难度 | 605. Can Place Flowers (Easy)                            |
|          | 452. Minimum Number of Arrows to Burst Balloons (Medium) |
|          | 763. Partition Labels (Medium)                           |
|          | 122. Best Time to Buy and Sell Stock II (Easy)           |
| 进阶难度 | 406. Queue Reconstruction by Height (Medium)             |
|          | 665. Non-decreasing Array (Easy)                         |



##605. Can Place Flowers

You have a long flowerbed in which some of the plots are planted, and some are not. However, flowers cannot be planted in **adjacent** plots.

Given an integer array `flowerbed` containing `0`'s and `1`'s, where `0` means empty and `1`means not empty, and an integer `n`, return *if* `n` new flowers can be planted in the `flowerbed`without violating the no-adjacent-flowers rule.



### Constraints

Can flowerbed be empty array?

No. $1 \le flowerbed.length \le 2*10^4$

Can i modify the array?

yes.



### Examples

Example 1:

> Input: flowerbed = [0], n = 1
>
> Output: true



Example 2:

> Input: flowerbed = [1,0,0,0,1], n = 2
>
> Output: false



Example 3: 

> Input: flowerbed = [1,0,0,0,1], n = 1
>
> Output: true



### Solution

贪心策略：只考虑相邻两侧位置的数据和自己位置的数据。



```java
class Solution {
    public boolean canPlaceFlowers(int[] flowerbed, int n) {
        if(flowerbed.length == 1 && flowerbed[0] == 0 && n <= 1) return true;
        int i = 0;
        while(i < flowerbed.length && n > 0) {
            if(i == 0 && i + 1 < flowerbed.length) {
                if(flowerbed[i] == 0 && flowerbed[i+1] == 0) {
                    n--;
                    flowerbed[i] = 1;
                }
            } else if(i > 0 && i < flowerbed.length-1) {
                if(flowerbed[i] == 0 && flowerbed[i-1] == 0 && flowerbed[i+1] == 0){
               		n--;
                    flowerbed[i] = 1;
                }
            } else if (i == flowerbed.length - 1 && i-1 >= 0){
                if(flowerbed[i] == 0 && flowerbed[i-1] == 0) {
                    n--;
                    flowerbed[i] = 1;
                }
            } 
            i+=1;
        }
        return n == 0;
    }
}
```

```java
public class Solution {
    public boolean canPlaceFlowers(int[] flowerbed, int n) {
        int i = 0, count = 0;
        while (i < flowerbed.length) {
            if (flowerbed[i] == 0 && (i == 0 || flowerbed[i - 1] == 0) && (i == flowerbed.length - 1 || flowerbed[i + 1] == 0)) { // good example of how to deal with index range
                flowerbed[i] = 1;
                count++;
            }
            i++;
        }
        return count >= n;
    }
}
```

Time Complexity: O(n)

Space Complexity: O(1)



## 452. Minimum Number of Arrows to Burst Balloons (Medium)

There are some spherical balloons spread in two-dimensional space. For each balloon, provided input is the start and end coordinates of the horizontal diameter. Since it's horizontal, y-coordinates don't matter, and hence the x-coordinates of start and end of the diameter suffice. The start is always smaller than the end.

An arrow can be shot up exactly vertically from different points along the x-axis. A balloon with `xstart` and `xend` bursts by an arrow shot at `x` if `xstart ≤ x ≤ xend`. There is no limit to the number of arrows that can be shot. An arrow once shot keeps traveling up infinitely.

Given an array `points` where `points[i] = [xstart, xend]`, return *the minimum number of arrows that must be shot to burst all balloons*.



### Constraints

can array be empty?

$1 \le points.length \le 10^4$

what is the range of start and end in each interval?

$-2^{31} \le x_{start} < x_{end} \le 2^{31} - 1$



### Examples

Example 1:

> Input: [[0,1]]
>
> Output: 1



Example 2:

> Input:[[1,2], [2,3], [3,4]]
>
> Output: 3



Example 3:

> Input: [[1, 2],[1,2],[1,2]]
>
> Output: 1



### Solution

```java
class Solution {
    public int findMinArrowShots(int[][] points) {
        Arrays.sort(points, (a,b) -> Integer.compare(a[1], b[1])); // right
        int count = 1;
        int prev = points[0][1];
        for(int i = 1; i < points.length; i++) {
            if(points[i][0] > prev) {
                count++;
                prev = points[i][1];
            }
        }
        return count;
    }
}
```



## 763. Partition Labels (Medium)

You are given a string `s`. We want to partition the string into as many parts as possible so that each letter appears in at most one part.

Return *a list of integers representing the size of these parts*.



## 122. Best Time to Buy and Sell Stock II (Easy)

You are given an array `prices` where `prices[i]` is the price of a given stock on the `ith`day.

Find the maximum profit you can achieve. You may complete as many transactions as you like (i.e., buy one and sell one share of the stock multiple times).

**Note:** You may not engage in multiple transactions simultaneously (i.e., you must sell the stock before you buy again).



### Constraints

what is the range for each element?

$0 \le prices[i] \le 10^4$

can array length be 0?

$1 \le prices.length \le 3*10^4$



### Examples

Example 1:

> Input: prices = [1,2,3,4,3,2,1]
>
> Output: 3

Example 2:

> Input: prices = [7,1,5,3,6,4]
>
> Output: 7



### Solution

```java
class Solution {
    public int maxProfit(int[] prices) {
        int sum = 0;
        for(int i = 1; i < prices.length; i++) {
            if(prices[i] > prices[i-1]) 
                sum += prices[i] - prices[i-1];
        }
        return sum;
    }
}
```



## 406. Queue Reconstruction by Height 

You are given an array of people, `people`, which are the attributes of some people in a queue (not necessarily in order). Each `people[i] = [hi, ki]` represents the `ith` person of height `hi` with **exactly** `ki` other people in front who have a height greater than or equal to `hi`.

Reconstruct and return *the queue that is represented by the input array* `people`. The returned queue should be formatted as an array `queue`, where `queue[j] = [hj, kj]` is the attributes of the `jth` person in the queue (`queue[0]` is the person at the front of the queue).



## 参考资料

Leetcode 101 - 最易懂的贪心算法

[CS 341 - Greedy Algorithm](https://cs.uwaterloo.ca/~lapchi/cs341/notes/L08.pdf)

[贪心算法之时间调度问题](https://github.com/labuladong/fucking-algorithm/blob/master/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92%E7%B3%BB%E5%88%97/%E8%B4%AA%E5%BF%83%E7%AE%97%E6%B3%95%E4%B9%8B%E5%8C%BA%E9%97%B4%E8%B0%83%E5%BA%A6%E9%97%AE%E9%A2%98.md)