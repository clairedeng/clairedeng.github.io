---
title: "26 Remove Duplicates from Sorted Array"
date: 2020-05-29 17:25:00 +0700
categories: [Leetcode, Array, Two Pointer]
---

Given a **sorted array** nums, remove the duplicates **in-place** such that each element appear only once and return the new length.

Do not allocate extra space for another array, you must do this by modifying the input array in-place with O(1) extra memory.



Test 1:

> Input: []
>
> Output: 0



Test 2:

> Input: [0,1,2,3,4]
>
> Output: 5 ([0,1,2,3,4])



Test 3:

> Input: [0,0,1,2,2,2,2,3,3,4,4,4,5]
>
> Output: 6([0,1,2,3,4,5])



Approach 1: Two pointers

```java
public int removeDuplicates(int[] nums) {
    if(nums.length == 0) return 0;
    int j = 0;
    for(int i = 1; i < nums.length; i++) {
        if(nums[i] != nums[j]) {
            j++;
            nums[j] = nums[i];
        }
    }
    return j+1;
}
```

Time complexity: $O(n)$

Space complexity: $O(1)$