# 1053. Previous Permutation With One Swap

* **Remember to check for (j=0; j<10; i++) such idiot mistake!!!!!**
* -est problem: sort out the priority of constrains
* If leftest, search from right!!





## Brute Force Opt (Still$O(n^2)$ / $O(1000n)$)

I though `0<=arr[i]<=10`

```c++
class Solution {
public:
    vector<int> prevPermOpt1(vector<int>& arr) {
        int i, j;
        // vector<int> lastOccur(10000, -2);
        map<int, int> lastOccur;
        int bestL=-1, bestR=-1;
        for (i=0; i<arr.size(); i++) {
            // cout << "checking " << i << ":" << arr[i] <<endl;
            for (map<int, int>::iterator it = lastOccur.upper_bound(arr[i]); 
                 it != lastOccur.end(); it++) {
                int upb = it->first;
                int occ = it->second;
                // cout << "cmping" << upb << ":" << occ << endl;
                if ((occ > bestL) || 
                    (occ == bestL && arr[i] > arr[bestR])) {
                    bestL = occ;
                    bestR = i;
                    // cout<<"find best swap " << bestL << " " << bestR <<endl;
                }
            }
            lastOccur[arr[i]] = i;
        }
        
        if (bestL < bestR && bestL >= 0) {
            // cout << "swapping" << bestL << " and " << bestR << endl;
            swap(arr[bestL], arr[bestR]);
        }
        return arr;
    }
};
```



## Greedy $O(2n)$

The most important thing is to sort out the condicions’ priority.

Find $A[i], A[j]$ where 

1. $A[i] > A[j]$
2. `i` should be the largest, so that `A[i]` is most least important.
3. `A[j]` should be the largest, so the impact of moving `A[j]` to position `i` is the most umimportant.
4. `j` should be the minimum, so the increase of moving `A[i]` to position `j` is the most benificial.

Accordingly, 

1. Search from right to left, find the rightest possible `A[i]` where exists a `k` such that `A[i]` > `A[k]`, $i<k<len(A)$, so
   1. The formal way is to keep a min record
   2. **A simler way to do this is to find a descending point.** Since the min must be the first we locate. * it does only one comparison per time.
2. Find the `j` from left to right (maximum leftest)



> Runtime: 20 ms, faster than 95.58 %% of C++ online submissions for Previous Permutation With One Swap.
>
> Memory Usage: 24.6 MB, less than 72.12% of C++ online submissions for Previous Permutation With One Swap.
>
> Next challenges:

```c++
class Solution {
public:
    vector<int> prevPermOpt1(vector<int>& arr) {
        int i, j, tmp;
        int bestR=0, maxaj=0, minj=0;
        i = arr.size() - 2;
        while (!(arr[i] > arr[i+1])) {
            i--;
            if (i < 0) return arr; // descending array, no solution.
        }
        /* now arr[i] > arr[i+1], locate the 
         * 1. Largest arr[j] such that j > i & arr[i] > arr[j]
         * 2. Mininum j if there are multiple (1)
         */
        j = i + 1;
        while (j < arr.size()) {
            if ((arr[i] > arr[j]) &&
                (arr[j] > maxaj)) {
                bestR = j;
                maxaj = arr[j];
            }
            j++;
        }
        // swap(arr[i], arr[bestR]);
        tmp = arr[i];
        arr[i] = arr[bestR];
        arr[bestR] = tmp;
        return arr;
    }
};
```



## Greedy Opt $O(n)$



> Runtime: 20 ms, faster than 95.58% of C++ online submissions for Previous Permutation With One Swap.
>
> Memory Usage: 24.8 MB, less than 31.86% of C++ online submissions for Previous Permutation With One Swap.

```c++
class Solution {
public:
    vector<int> prevPermOpt1(vector<int>& arr) {
        int i, j, tmp;
        int maxaj=0, minj=0, bestR=0;
        map<int, int> numPos;
        i = arr.size() - 2;
        
        numPos[arr[i+1]] = i+1;
        while (!(arr[i] > arr[i+1])) {
          // remenber the leftest a[j]
            numPos[arr[i]] = i;
            i--;
            if (i < 0) return arr; // descending array, no solution.
        }
        numPos[arr[i]] = i;
        /* find the largest a[j] that less than a[i]
         */
        auto it = numPos.lower_bound(arr[i]);
        bestR = (--it)->second;
        swap(arr[i], arr[bestR]);
        return arr;
    }
};
```

$O(n)$ or $O(nlog(n))$?

