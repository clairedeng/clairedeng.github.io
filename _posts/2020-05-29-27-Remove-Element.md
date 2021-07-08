---
title: "27 Remove Element"
layout: post
toc: true
date: 2020-05-29 17:10:00 +0700
categories: [Leetcode, Array, Two Pointer]
---

Given an **array** nums and a value val, remove all instances of that value **in-place** and return the new length.

Do not allocate extra space for another array, you must do this by modifying the input array in-place with $O(1)$ extra memory.

The **order** of elements **can be changed**. It doesn't matter what you leave beyond the new length.



Test 1:

> Input: [], 1
>
> Output: 0 ([])



Test 2:

> Input: [3,1,2,3, 3,4,4,5,3,6, 3], 3
>
> Output: 6 ([1,2,4,4,5,6])



Test 3:

> Input: [1,2,3], 4
>
> Output: 3 ([1,2,3])



Approach 1: Two Pointers

```java
public int removeElement(int[] nums, int val) {
    int i = 0;
    for(int j = 0; j < nums.length; j++) {
        if(nums[j] != val) {
            nums[i] = nums[j]; // important
            i++;
        }
    }
    return i;
}
```

Time complexity: $O(n)$

Space complexity: $O(1)$



Approach 2: Two pointers - when elements to remove are rare 

```java
public int removeElement(int[] nums, int val) {
    int i = 0;
    int n = nums.length;
    while(i < n) {
        if(nums[i] == val) {
            nums[i] = nums[n-1]; // important
            n--;
        } else {
            i++;
        }
    }
    return n;
}
```

Time complexity: $O(n)$

Space complexity: $O(1)$

