---
title: "Binary Search"
layout: post
toc: true
date: 2021-07-11 09:02:00 +0700
categories: [Algorithm]
---


二分查找也常被称为二分法或者折半查找，每次查找时通过将待查找区间分成两部分并只取
一部分继续查找，将查找的复杂度大大减少。对于一个长度为$O(n)$ 的数组，二分查找的时间复
杂度为$O(\log n)$。

二分查找也可以看作双指针的一种特殊情况，但我们一般会将二者区分。双指针类型的题，
指针通常是一步一步移动的，而在二分查找里，指针每次移动半个区间长度。

二分查找如何运用到实际的算法问题中呢？当**搜索空间有序**的时候，就可以通过二分搜索「剪枝」，大幅提升效率。 



## 二分查找框架

```java
int binarySearch(int[] nums, int target) {
    int left = 0;
    int right = ...;
    while(...) {
        int mid = left + (right - left) / 2; // prevent integer overflow
        if(nums[mid] == target) {
            ...
        } else if (nums[mid] > target) {
            right = ...
        } else if (nums[mid] < target) {
            left = ...
        }
    }
    return ...;
}
```

**分析二分查找的一个技巧是：不要出现 else，而是把所有情况用 else if 写清楚，这样可以清楚地展现所有细节**。 



### 1. 寻找一个数（基本的二分搜索）

搜索一个数，如果存在，返回其索引，否则返回 -1。 

```java
int binarySearch(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;
    while(left <= right) {
        int mid = left + (right - left)/2;
        if(nums[mid] == target) {
            return mid;
        } else if （nums[mid] > target) {
            right = mid - 1;
        } else if (nums[mid] < target) {
            left = mid + 1;
        }
    }
    return -1;
}
```

**1、为什么 while 循环的条件中是 <=，而不是 <**？

答：因为初始化 `right` 的赋值是 `nums.length - 1`，即最后一个元素的索引，而不是 `nums.length`。

这二者可能出现在不同功能的二分查找中，区别是：前者相当于两端都闭区间 `[left, right]`，后者相当于左闭右开区间 `[left, right)`，因为索引大小为 `nums.length` 是越界的。

我们这个算法中使用的是前者 `[left, right]` 两端都闭的区间。**这个区间其实就是每次进行搜索的区间**。

**什么时候应该停止搜索呢？**

当然，找到了目标值的时候可以终止：

```
    if(nums[mid] == target)
        return mid; 
```

但如果没找到，就需要 while 循环终止，然后返回 -1。

**那 while 循环什么时候应该终止？**

**搜索区间为空的时候应该终止**，意味着你没得找了，就等于没找到嘛。

`while(left <= right)` 的终止条件是 `left == right + 1`，写成区间的形式就是 `[right + 1, right]`，或者带个具体的数字进去 `[3, 2]`，可见**这时候区间为空**，因为没有数字既大于等于 3 又小于等于 2 的吧。所以这时候 while 循环终止是正确的，直接返回 -1 即可。

`while(left < right)` 的终止条件是 `left == right`，写成区间的形式就是 `[right, right]`，或者带个具体的数字进去 `[2, 2]`，**这时候区间非空**，还有一个数 2，但此时 while 循环终止了。也就是说这区间 `[2, 2]` 被漏掉了，索引 2 没有被搜索，如果这时候直接返回 -1 就是错误的。

**2、为什么 left = mid + 1，right = mid - 1？我看有的代码是 right = mid 或者 left = mid，没有这些加加减减，到底怎么回事，怎么判断**？

答：这也是二分查找的一个难点，不过只要你能理解前面的内容，就能够很容易判断。

刚才明确了「搜索区间」这个概念，而且本算法的搜索区间是两端都闭的，即 `[left, right]`。那么当我们发现索引 `mid` 不是要找的 `target`时，下一步应该去搜索哪里呢？

当然是去搜索 `[left, mid-1]` 或者 `[mid+1, right]` 对不对？**因为 mid 已经搜索过，应该从搜索区间中去除**。

**3、此算法有什么缺陷**？

答：至此，你应该已经掌握了该算法的所有细节，以及这样处理的原因。但是，这个算法存在局限性。

比如说给你有序数组 `nums = [1,2,2,2,3]`，`target` 为 2，此算法返回的索引是 2，没错。但是如果我想得到 `target` 的左侧边界，即索引 1，或者我想得到 `target` 的右侧边界，即索引 3，这样的话此算法是无法处理的。

这样的需求很常见，**你也许会说，找到一个 target，然后向左或向右线性搜索不行吗？可以，但是不好，因为这样难以保证二分查找对数级的复杂度了**。

我们后续的算法就来讨论这两种二分查找的算法。



### 2. 寻找左侧边界的二分搜索 

```java
int left_bound(int[] nums, int target) {
    if(num.length == 0) return -1;
    int left = 0;
    int right = nums.length; // important
    while(left < right) {
        int mid = left + (right - left) /2 ;
        if(nums[mid] == target) {
            right = mid;
        } else if (nums[mid] > target) {
            right = mid; // important
        } else if （nums[mid] < target) {
            left = mid + 1;
        }
    }
    return left; // right is the same, since left == right jumps out of the loop
}
```

**1、为什么 while 中是 < 而不是 <=**?

答：用相同的方法分析，因为 `right = nums.length` 而不是 `nums.length - 1`。因此每次循环的「搜索区间」是 `[left, right)` 左闭右开。

`while(left < right)` 终止的条件是 `left == right`，此时搜索区间 `[left, left)` 为空，所以可以正确终止。

**刚才的 right 不是 nums.length - 1 吗，为啥这里非要写成 nums.length 使得「搜索区间」变成左闭右开呢**？

因为对于搜索左右侧边界的二分查找，这种写法比较普遍，我就拿这种写法举例了，保证你以后遇到这类代码可以理解。你非要用两端都闭的写法反而更简单，我会在后面写相关的代码，把三种二分搜索都用一种两端都闭的写法统一起来，你耐心往后看就行了。

**2、为什么没有返回 -1 的操作？如果 nums 中不存在 target 这个值，怎么办**？

答：因为要一步一步来，先理解一下这个「左侧边界」有什么特殊含义：

>target = 2, nums = [1,2,2,2,3]

对于这个数组，算法会返回 1。这个 1 的含义可以这样解读：`nums` 中小于 2 的元素有 1 个。

比如对于有序数组 `nums = [2,3,5,7]`, `target = 1`，算法会返回 0，含义是：`nums` 中小于 1 的元素有 0 个。

再比如说 `nums = [2,3,5,7], target = 8`，算法会返回 4，含义是：`nums` 中小于 8 的元素有 4 个。

综上可以看出，函数的返回值（即 `left` 变量的值）取值区间是闭区间 `[0, nums.length]`，所以我们简单添加两行代码就能在正确的时候 return -1：

```java
while (left < right) {
    //...
}
// target 比所有数都大
if (left == nums.length) return -1;
// 类似之前算法的处理方式
return nums[left] == target ? left : -1;
```

**3、为什么 left = mid + 1，right = mid ？和之前的算法不一样**？

答：这个很好解释，因为我们的「搜索区间」是 `[left, right)` 左闭右开，所以当 `nums[mid]` 被检测之后，下一步的搜索区间应该去掉 `mid`分割成两个区间，即 `[left, mid)` 或 `[mid + 1, right)`。

**4、为什么该算法能够搜索左侧边界**？

答：关键在于对于 `nums[mid] == target` 这种情况的处理：

```
    if (nums[mid] == target)
        right = mid;
```

可见，找到 target 时不要立即返回，而是缩小「搜索区间」的上界 `right`，在区间 `[left, mid)` 中继续搜索，即不断向左收缩，达到锁定左侧边界的目的。

**5、为什么返回 left 而不是 right**？

答：都是一样的，因为 while 终止的条件是 `left == right`。

**6、能不能想办法把 right 变成 nums.length - 1，也就是继续使用两边都闭的「搜索区间」？这样就可以和第一种二分搜索在某种程度上统一起来了**。

答：当然可以，只要你明白了「搜索区间」这个概念，就能有效避免漏掉元素，随便你怎么改都行。下面我们严格根据逻辑来修改：

因为你非要让搜索区间两端都闭，所以 `right` 应该初始化为 `nums.length - 1`，while 的终止条件应该是 `left == right + 1`，也就是其中应该用 `<=`：

```
int left_bound(int[] nums, int target) {
    // 搜索区间为 [left, right]
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        // if else ...
    }
```

因为搜索区间是两端都闭的，且现在是搜索左侧边界，所以 `left` 和 `right` 的更新逻辑如下：

```
if (nums[mid] < target) {
    // 搜索区间变为 [mid+1, right]
    left = mid + 1;
} else if (nums[mid] > target) {
    // 搜索区间变为 [left, mid-1]
    right = mid - 1;
} else if (nums[mid] == target) {
    // 收缩右侧边界
    right = mid - 1;
}
```

由于 while 的退出条件是 `left == right + 1`，所以当 `target` 比 `nums` 中所有元素都大时，会存在以下情况使得索引越界：

> target = 6
>
> nums = [1, 2, 2, 4]

因此，最后返回结果的代码应该检查越界情况：

```java
if (left >= nums.length || nums[left] != target)
    return -1;
return left;
```

至此，整个算法就写完了，完整代码如下：

```java
int left_bound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    // 搜索区间为 [left, right]
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            // 搜索区间变为 [mid+1, right]
            left = mid + 1;
        } else if (nums[mid] > target) {
            // 搜索区间变为 [left, mid-1]
            right = mid - 1;
        } else if (nums[mid] == target) {
            // 收缩右侧边界
            right = mid - 1;
        }
    }
    // 检查出界情况
    if (left >= nums.length || nums[left] != target) // what if left == nums.length
        return -1;
    return left;
}
```

这样就和第一种二分搜索算法统一了，都是两端都闭的「搜索区间」，而且最后返回的也是 `left` 变量的值。只要把住二分搜索的逻辑，两种形式大家看自己喜欢哪种记哪种吧。



### 3. 寻找右侧边界的二分查找

类似寻找左侧边界的算法，这里也会提供两种写法，还是先写常见的左闭右开的写法，只有两处和搜索左侧边界不同，已标注： 

```java
int right_bound(int[] nums, int target) {
    int left = 0;
    int right = nums.length;
    while(left < right) {
        int mid = left + (right - left)/2;
        if(nums[mid] == target) {
            left = mid + 1; // important
        } else if (nums[mid] > target) {
            right = mid;
        } else if (nums[mid] < target) {
            left = mid + 1;
        }
    }
    return right-1; // important
}
```

**1、为什么这个算法能够找到右侧边界**？

答：类似地，关键点还是这里：

```
if (nums[mid] == target) {
    left = mid + 1;
```

当 `nums[mid] == target` 时，不要立即返回，而是增大「搜索区间」的下界 `left`，使得区间不断向右收缩，达到锁定右侧边界的目的。

**2、为什么最后返回 left - 1 而不像左侧边界的函数，返回 left？而且我觉得这里既然是搜索右侧边界，应该返回 right 才对**。

答：首先，while 循环的终止条件是 `left == right`，所以 `left` 和 `right` 是一样的，你非要体现右侧的特点，返回 `right - 1` 好了。

至于为什么要减一，这是搜索右侧边界的一个特殊点，关键在这个条件判断：

```java
if (nums[mid] == target) {
    left = mid + 1;
    // 这样想: mid = left - 1
```

 因为我们对 `left` 的更新必须是 `left = mid + 1`，就是说 while 循环结束时，`nums[left]` 一定不等于 `target` 了，而 `nums[left-1]` 可能是 `target`。 

**3、为什么没有返回 -1 的操作？如果 nums 中不存在 target 这个值，怎么办**？

答：类似之前的左侧边界搜索，因为 while 的终止条件是 `left == right`，就是说 `left` 的取值范围是 `[0, nums.length]`，所以可以添加两行代码，正确地返回 -1：

```java
while (left < right) {
    // ...
}
if (left == 0) return -1;
return nums[left-1] == target ? (left-1) : -1;
```

**4、是否也可以把这个算法的「搜索区间」也统一成两端都闭的形式呢？这样这三个写法就完全统一了，以后就可以闭着眼睛写出来了**。

答：当然可以，类似搜索左侧边界的统一写法，其实只要改两个地方就行了：

```java
int right_bound(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;
    while(left <= right) {
        int mid = left + (right - left) / 2;
        if(nums[mid] == target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1;
        } else if (nums[mid] < target) {
            left = mid + 1;
        }
    }
    if(right < 0 || nums[right] != target)
        return -1;
    return right;
}
```

当 `target` 比所有元素都小时，`right` 会被减到 -1，所以需要在最后防止越界。



### 4. 逻辑统一

来梳理一下这些细节差异的因果逻辑：

**第一个，最基本的二分查找算法**：

```java
因为我们初始化 right = nums.length - 1
所以决定了我们的「搜索区间」是 [left, right]
所以决定了 while (left <= right)
同时也决定了 left = mid+1 和 right = mid-1

因为我们只需找到一个 target 的索引即可
所以当 nums[mid] == target 时可以立即返回
```

**第二个，寻找左侧边界的二分查找**：

```java
因为我们初始化 right = nums.length
所以决定了我们的「搜索区间」是 [left, right)
所以决定了 while (left < right)
同时也决定了 left = mid + 1 和 right = mid

因为我们需找到 target 的最左侧索引
所以当 nums[mid] == target 时不要立即返回
而要收紧右侧边界以锁定左侧边界
```

**第三个，寻找右侧边界的二分查找**：

```java
因为我们初始化 right = nums.length
所以决定了我们的「搜索区间」是 [left, right)
所以决定了 while (left < right)
同时也决定了 left = mid + 1 和 right = mid

因为我们需找到 target 的最右侧索引
所以当 nums[mid] == target 时不要立即返回
而要收紧左侧边界以锁定右侧边界

又因为收紧左侧边界时必须 left = mid + 1
所以最后无论返回 left 还是 right，必须减一
```

对于寻找左右边界的二分搜索，常见的手法是使用左闭右开的「搜索区间」，**我们还根据逻辑将「搜索区间」全都统一成了两端都闭，便于记忆，只要修改两处即可变化出三种写法**：

```java
int binary_search(int[] nums, int target) {
    int left = 0, right = nums.length - 1; 
    while(left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1; 
        } else if(nums[mid] == target) {
            // 直接返回
            return mid;
        }
    }
    // 直接返回
    return -1;
}

int left_bound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1;
        } else if (nums[mid] == target) {
            // 别返回，锁定左侧边界
            right = mid - 1;
        }
    }
    // 最后要检查 left 越界的情况
    if (left >= nums.length || nums[left] != target)
        return -1;
    return left;
}


int right_bound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1;
        } else if (nums[mid] == target) {
            // 别返回，锁定右侧边界
            left = mid + 1;
        }
    }
    // 最后要检查 right 越界的情况
    if (right < 0 || nums[right] != target)
        return -1;
    return right;
}
```



## 例子

### Koko吃香蕉

koko喜欢吃香蕉。这里有N堆香蕉，第i堆中有piles[i]根香蕉。警卫已经离开了，将在H小时后回来。

koko可以决定她吃香蕉的速度K（单位：根/小时）。每个小时，她将会选择一堆香蕉，从中吃掉K根。如果这堆香蕉少于K根，她将吃掉这堆的所有香蕉，然后这一小时内不会再吃更多的香蕉。

koko喜欢慢慢吃，但仍然想在警卫回来前吃掉所有的香蕉。

返回她可以在H小时内吃掉所有香蕉的最小速度K(K为整数)

也就是说，Koko 每小时最多吃一堆香蕉，如果吃不下的话留到下一小时再吃；如果吃完了这一堆还有胃口，也只会等到下一小时才会吃下一堆。在这个条件下，让我们确定 Koko 吃香蕉的**最小速度**（根/小时）。

如果直接给你这个情景，你能想到哪里能用到二分查找算法吗？如果没有见过类似的问题，恐怕是很难把这个问题和二分查找联系起来的。

那么我们先抛开二分查找技巧，想想如何暴力解决这个问题呢？

首先，算法要求的是「`H` 小时内吃完香蕉的最小速度」，我们不妨称为 `speed`，请问 `speed` 最大可能为多少，最少可能为多少呢？

显然最少为 1，最大为 `max(piles)`，因为一小时最多只能吃一堆香蕉。那么暴力解法就很简单了，只要从 1 开始穷举到 `max(piles)`，一旦发现发现某个值可以在 `H` 小时内吃完所有香蕉，这个值就是最小速度：

```java
int minEatingSpeed(int[] piles, int H) {
    int max = getMax(piles);
    for(int speed = 1; speed < max; speed++) {
        if(caFinish(piles, speed, H))
            return speed;
    }
    return max;
}
```

注意这个 for 循环，就是在**连续的空间线性搜索，这就是二分查找可以发挥作用的标志**。由于我们要求的是最小速度，所以可以用一个**搜索左侧边界的二分查找**来代替线性搜索，提升效率： 

```java
int minEatingSpeed(int[] piles, int H) {
    int left = 1;
    int right = getMax(piles) + 1;
    while(left < right) {
        int mid = left + (right - left) / 2;
        if(canFinish(piles, mid, H)) {
            right = mid;
        } else {
            left = mid + 1;
        }
    }
    return left;
}
```

剩下的辅助函数也很简单，可以一步步拆解实现： 

```java
boolean canFinish(int[] piles, int speed, int H) {
    int time = 0;
    for(int n: piles) {
        time += timeOf(n ,speed);
    }
    return time <= H;
}

int timeOf(int n, int speed) {
    return n/speed + ((n%speed > 0)? 1: 0);
}

int getMax(int[] piles) {
    int max = 0;
    for(int n: piles) {
        max = Math.max(max, n);
    }
    return max;
}
```

至此，借助二分查找技巧，算法的时间复杂度为 O(NlogN)。 



### 拓展延伸 - 运输问题

传输带上的第i个包裹的重量为weights[i]。每一天，我们都会按给出重量的顺序往传送带上的所有包裹送达的船的最低运载能力。要在D天内运输完所有的货物，货物不可分割，如何确定运输的最小载重呢？

Example

> 输入：weights = [1，2，3，4，5，6，7，8，9，10]， D = 5
>
> 输出：15
>
> 解释：
>
> 船舶最低载重15 就能够在5天内送达所有包裹，如下表示：
>
> 第一天：1，2，3，4，5
>
> 第二天：6，7
>
> 第三天：8
>
> 第四天：9
>
> 第五天：10
>
> 请注意，货物必须按照给定的顺序装运，因此使用载重能力为14的船舶并将包装分成（2，3，4，5），（1，6，7），（8），（9），（10）是不允许的。

其实本质上和 Koko 吃香蕉的问题一样的，首先确定 `cap` 的最小值和最大值分别为 `max(weights)` 和 `sum(weights)`。 

我们要求**最小载重**，所以可以用搜索左侧边界的二分查找算法优化线性搜索： 

```java
int ShipWithinDays(int[] weights, int D) {
    int left = getMax(weights);
    int right = getSum(weights) + 1;
    while(left < right) {
        int mid = left + (right - left) / 2;
        if(canFinsh(weights, D, mid)) {
            right = mid;
        } else {
            left = mid+1;
        }
    }
    return left;
}

boolean canFinish(int[] w, int D, int cap) {
    int i = 0;
    for(int day = 0; day < D; day++) {
        int maxCap = cap;
        while((maxCap -= w[i]) >= 0) {
            i++;
            if(i == w.length)
                return true;
        }
    }
    return false;
}
```

通过这两个例子，你是否明白了二分查找在实际问题中的应用？

```
for (int i = 0; i < n; i++)
    if (isOK(i))
        return ans;
```



### 二分查找高效判定子序列

https://github.com/labuladong/fucking-algorithm/blob/master/%E9%AB%98%E9%A2%91%E9%9D%A2%E8%AF%95%E7%B3%BB%E5%88%97/%E4%BA%8C%E5%88%86%E6%9F%A5%E6%89%BE%E5%88%A4%E5%AE%9A%E5%AD%90%E5%BA%8F%E5%88%97.md



## 4. Median of Two Sorted Arrays 

https://leetcode.com/problems/median-of-two-sorted-arrays/discuss/2496/Concise-JAVA-solution-based-on-Binary-Search

```java
public double findMedianSortedArrays(int[] A, int[] B) {
	    int m = A.length, n = B.length;
	    int l = (m + n + 1) / 2;
	    int r = (m + n + 2) / 2;
	    return (getkth(A, 0, B, 0, l) + getkth(A, 0, B, 0, r)) / 2.0;
	}

public double getkth(int[] A, int aStart, int[] B, int bStart, int k) {
	if (aStart > A.length - 1) return B[bStart + k - 1];            
	if (bStart > B.length - 1) return A[aStart + k - 1];                
	if (k == 1) return Math.min(A[aStart], B[bStart]);
	
	int aMid = Integer.MAX_VALUE, bMid = Integer.MAX_VALUE;
	if (aStart + k/2 - 1 < A.length) aMid = A[aStart + k/2 - 1]; 
	if (bStart + k/2 - 1 < B.length) bMid = B[bStart + k/2 - 1];        
	
	if (aMid < bMid) 
	    return getkth(A, aStart + k/2, B, bStart,       k - k/2);// Check: aRight + bLeft 
	else 
	    return getkth(A, aStart,       B, bStart + k/2, k - k/2);// Check: bRight + aLeft
}
```



## 167. Two Sum II - Input array is sorted 

```java
public int[] twoSum(int[] numbers, int target) {
        int left = 0;
        int right = numbers.length - 1;
        while(left < right) {
            int sum = numbers[left] + numbers[right];
            if(target == sum) {
                int[] result = {left + 1, right + 1};
                return result;
            } else if(target > sum) {
                left++;
            } else {
                right--;
            }
        }
        return null;
    }
```



## 287. Find the Duplicate Number 

```java
class Solution {
    public int findDuplicate(int[] nums) {
        int slow = nums[0];
        int fast = nums[0];
        do {
            slow = nums[slow];
            fast = nums[nums[fast]];
        } while(slow != fast);
        fast = nums[0];
        while(fast != slow) {
            fast = nums[fast];
            slow = nums[slow];
        }
        return fast;
    }
}
```



## 315. count of smaller numbers after self

merge sort

```
https://leetcode.com/problems/count-of-smaller-numbers-after-self/discuss/445769/merge-sort-CLEAR-simple-EXPLANATION-with-EXAMPLES-O(n-lg-n)
```



## 349. Intersection of Two Arrays

```java
class Solution {
    public int[] set_intersection(HashSet<Integer> set1, HashSet<Integer> set2) {
        int[] output = new int[set1.size()];
        int idx = 0;
        for(Integer s : set1) {
            if(set2.contains(s)) output[idx++] = s;
        }
        return Arrays.copyOf(output,idx);
    }
    
    public int[] intersection(int[] nums1, int[] nums2) {
        HashSet<Integer> set1 = new HashSet<Integer>();
        for(Integer n : nums1) set1.add(n);
        HashSet<Integer> set2 = new HashSet<Integer>();
        for(Integer n : nums2) set2.add(n);
        if(set1.size() < set2.size()) return set_intersection(set1, set2);
        else return set_intersection(set2, set1);
    }
}
```



## 参考资料

[二分查找详解](https://github.com/labuladong/fucking-algorithm/blob/master/%E7%AE%97%E6%B3%95%E6%80%9D%E7%BB%B4%E7%B3%BB%E5%88%97/%E4%BA%8C%E5%88%86%E6%9F%A5%E6%89%BE%E8%AF%A6%E8%A7%A3.md)

[如何运用二分查找算法](https://github.com/labuladong/fucking-algorithm/blob/master/%E9%AB%98%E9%A2%91%E9%9D%A2%E8%AF%95%E7%B3%BB%E5%88%97/koko%E5%81%B7%E9%A6%99%E8%95%89.md)