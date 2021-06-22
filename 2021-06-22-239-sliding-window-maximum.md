# 239. Sliding Window Maximum



### 1. erasable `priority_queue` — `set`

```c++
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        multiset<int> pq;
        vector<int> ans;
        int i;
        // initialize
        for (i=0; i<k; i++) {
            pq.insert(nums[i]);
        }
        ans.push_back(*pq.rbegin());
        // sliding
        for (; i<nums.size(); i++) {
            pq.insert(nums[i]);
            pq.erase(pq.find(nums[i-k]));
            ans.push_back(*pq.rbegin());
        }
        return ans;
    }
};
```

 **Time Cmplx: $O(Nlog(k))$**

**Space Cmplx: $O(k)$**

> Runtime: 608 ms, faster than 10.10% of C++ online submissions for Sliding Window Maximum.
>
> Memory Usage: 206.8 MB, less than 8.46% of C++ online submissions for Sliding Window Maximum.



### 2. Monotonic Queue

归纳法。

设k=2, 我们window就一个已知最大值，那么新进入一个元素时，

1. 如果大于最大值那么应该直接替换最大值，作为新的最大值存在。因为旧的最大值不可能再被用到。
2. 如果小于最大值那么应当保留记录。因为当我们的最大值滑出窗口时，较小的会被当成新的最大值。
3. 如果相等，这种情况暂时不确定。看似不用记录。

设k=3, 当仅有一个最大值时同k=2, 当窗口中有一大一小两值，那么新进入一个元素时，

1. 如果大于最大值，可以把窗口里面的数据都丢掉。因为旧的最大值和较小值都不再会被用到。同理。
2. 如果小于较小值，那么需要记录，同理。
3. 如果大于最大值小于较小值，那么应当记录吗？应该记录，因为最大值滑出的时候，新最大值变为当前值。当前值比较小值大，所以较小值再也不会被用到。所以要丢掉较小值。

综上，**新的值一定会被记录，而窗口中小于最新值的值不会再被用到直接丢掉，所以形成了一个新进入的值一定是一个最小值，即窗口中一定是一个单调递减数列。**那么数列头部就是窗口最大值。

那么如何判断数据滑出窗口呢，就可以用数组下标作为记录，如果小于等于i-k就说明数组头部过时，丢掉。

```c++
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        deque<int> window;
        vector<int> ans;
        int i;
        // init
        for (i=0; i<k; i++) {
            while (!window.empty() && nums[i] >= nums[window.back()]) {
                window.pop_back();
            }
            window.push_back(i);
        }
        ans.push_back(nums[window.front()]);
        // begin
        for (; i<nums.size(); i++) {
            while (!window.empty() && nums[i] >= nums[window.back()]) {
                window.pop_back();
            }
            window.push_back(i);
            while (window.front() <= i-k) window.pop_front();
            ans.push_back(nums[window.front()]);
        }
        return ans;
    }
};
```

**Time Cmplx: $O(N)$**

**Space Cmpx: $O(k)$**

> Runtime: 224 ms, faster than 93.16% of C++ online submissions for Sliding Window Maximum.
>
> Memory Usage: 131.9 MB, less than 53.13% of C++ online submissions for Sliding Window Maximum.

### 3. Partition + Pre-processing

TBD.
