# 880. Decoded String at Index 

**Leetcode Link:** https://leetcode.com/problems/decoded-string-at-index/

**Description:**

> An encoded string `s` is given. To find and write the *decoded* string to a tape, the encoded string is read **one character at a time** and the following steps are taken:
>
> - If the character read is a letter, that letter is written onto the tape.
> - If the character read is a digit (say `d`), the entire current tape is repeatedly written `d-1` more times in total.
>
> Now for some encoded string `s`, and an index `k`, find and return the `k`-th letter (1 indexed) in the decoded string.
>
> **Example 1:**
>
> ```
> Input: s = "leet2code3", k = 10
> Output: "o"
> Explanation: 
> The decoded string is "leetleetcodeleetleetcodeleetleetcode".
> The 10th letter in the string is "o".
> ```

### **Algorithm:**

Construct a stack that stores `[low, up, str]` that represents the substring of the decoded string from position `low` to `up`. So that we can use `k` to locate the substring.

**Example**:

s = "leet2code3", k = 10

**constructed stack**:

```
low, up, string
1, 4, "leet" // 1-4 is "leet"
4, 8, "" 			// 4-8 is repeated string
9, 12, "code"	// 9-12 is code
12, 36, ""		// 12-36 is repeated string
```

So everytime we meet a repeated string, we set $k = k % low$, till we find k exactly fit in the range of [low, up] with a valid string.



### **Code:**

```c++
class Solution {
public:
    string decodeAtIndex(string s, int k) {

        //construct query queue
        typedef struct {
            int l;     
            int r; 
            string s;
        } qnode;
        // typedef struct qnode qnode;
        
        
        std::stack<qnode> queryInfo;
        // std::string tmps = "";
        int curLen = 0;
        qnode tmpq;
        string ans;
        
        for (char c : s) {
            // opt
            if (curLen >= k) break;
            if ('a' <= c && c <= 'z') {
                curLen++;
                if (tmpq.s.empty()) {
                    tmpq.l = curLen;
                }
                tmpq.s += c;
                // std::cout << "Decode ascii " << c << std::endl;
            }
            else if ('1' <= c && c <= '9') {
                if (!tmpq.s.empty()) {
                    // save str info
                    tmpq.r = curLen;
                    queryInfo.push(tmpq);
                    // cout << "Push node l, r, str:"
                    //     << tmpq.l << ", " << tmpq.r << ", " << tmpq.s 
                    //     << std::endl;
                    tmpq.s = "";
                }
                tmpq.l = curLen;
                int times = c - '0';
                if (curLen > (INT_MAX / times)) {
                    tmpq.r = curLen = k; // prevent overflow
                }
                else {
                    tmpq.r = curLen = curLen * times;
                }
                queryInfo.push(tmpq);
                // std::cout << "Decode repeat " << c 
                //     << ". Push node l, r, str:"
                //     << tmpq.l << ", " << tmpq.r << ", " << tmpq.s
                //     << std::endl;
            }
            else {
                // std::cout << "panic" << std::endl;
            }
        }
        
        // remaining checking
        if (!tmpq.s.empty()) {
            // save str info
            tmpq.r = curLen;
            queryInfo.push(tmpq);
            // cout << "Push node l, r, str:"
            //     << tmpq.l << ", " << tmpq.r << ", " << tmpq.s 
            //     << std::endl;
        }
            
        // search
        while (!queryInfo.empty()) {
            tmpq = queryInfo.top();
            if (tmpq.l <= k && k <= tmpq.r) {
                if (tmpq.s.empty()) {
                    // std::cout << k;
                    k %= tmpq.l;
                    if (k == 0) k = tmpq.l;
                    // std::cout << " Reduced to " << k << std::endl;
                }
                else {
                    ans = tmpq.s[k - tmpq.l];
                    // std::cout << "Found at " << k << std::endl;
                    break;
                }
            }
            // continue popping
            queryInfo.pop();
        }
        return ans;
    }
};
```

**Notes**:

* **Remenber to check integer overflow!!!! eg. `if (curLen > (INT_MAX / times))`** NONO, can simply use `long`, but make sure everything related in the calculation is `long`.
* **`std::stack` 's `pop` only deletes the top emlement but don't return its value! Use `top` + `pop` instead!**
* **[`lower_bound`, `upper_bound`]**
* **`char.isdigit()`!**



