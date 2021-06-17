# 477. Total Hamming Distance

[TOC]

 ##### LeetCode Link: [477. Total Hamming Distance](https://leetcode.com/problems/total-hamming-distance/)

#### Describtion:

> The [Hamming distance](https://en.wikipedia.org/wiki/Hamming_distance) between two integers is the number of positions at which the corresponding bits are different.
>
> Given an integer array `nums`, return *the sum of **Hamming distances** between all the pairs of the integers in* `nums`.
>
>  
>
> **Example 1:**
>
> ```
> Input: nums = [4,14,2]
> Output: 6
> Explanation: In binary representation, the 4 is 0100, 14 is 1110, and 2 is 0010 (just
> showing the four bits relevant in this case).
> The answer will be:
> HammingDistance(4, 14) + HammingDistance(4, 2) + HammingDistance(14, 2) = 2 + 2 + 2 = 6.
> ```

Brute Force:

```c++
class Solution {
public:
    int calcOneBits(int x) {
        int sum = 0;
        while (x > 0) {
            if (x % 2 > 0) sum++;
            x /= 2;
        }
        return sum;
    }
    inline int hammingDist(int x, int y) {
        return calcOneBits(x ^ y);
    }
    int totalHammingDistance(vector<int>& nums) {
        int sumHamDist = 0;
        int i, j;
        int x, y;
        for (i = 0; i < nums.size() - 1; ++i) {
            x = nums[i];
            for (j = i + 1; j < nums.size(); ++j) {
                y = nums[j];
                // printf("i, j, vali, valj: %d, %d, %d, %d\n",
                //   i, j, x, y);
                // Hamming Dist
                sumHamDist += hammingDist(x, y);
            }
        }
        return sumHamDist;
    }
};
```

#### **Improved Bitcount**

```c++
while (x > 0) {
  count++;
  x &= x - 1;
}
```

So every time  `x-1` picks one `1` out from `x`, then use `&` to use the new `0` to erase one `1` from the original `x`!

Time complexity $O(n)$, where n is the bits length, which equals the `x /= 2` method but is better for most cases.



#### **Best method**

Suppose `n` `nums` , for the `y`th bit, the hamming distance is $c \times (n-c)$, where `c` is the total count of set bit.

```c++
class Solution {
public:
    int totalHammingDistance(vector<int>& nums) {
        int sumHamDist = 0;
        int i, j;
        int setBitCount = 0;
        for (i=0; i < 32; ++i) {
            setBitCount = 0;
            for (int num : nums) {
                setBitCount += num >> i & 1;
            }
            // cout << setBitCount << endl;
            sumHamDist += setBitCount * (nums.size() - setBitCount);
        }
        return sumHamDist;
    }
};
```

**Time Complexity:** $O(n)$

**Space Complexity:** $O(1)$

### Lessons

1. [Improved Bitcount](#improved-bitcount)
2. Best method for hamming distance

