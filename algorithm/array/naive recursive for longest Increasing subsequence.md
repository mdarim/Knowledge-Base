Here’s a **naive recursive Java implementation** for finding the *Longest Increasing Subsequence (LIS)* along with a **detailed explanation** of how the recursion works and why it’s exponential.

---

## 📌 Problem Overview

**Longest Increasing Subsequence (LIS):**
Given an array of integers, find the length of the longest subsequence (not necessarily contiguous) that is *strictly increasing*. For example:

```
Input:  [10, 9, 2, 5, 3, 7, 101, 18]
Output: 4  (subsequence may be [2, 3, 7, 101])
```

This problem can be solved in multiple ways — brute-force recursion, dynamic programming, and optimized methods using binary search. The brute-force recursive approach tries all possible subsequences, giving an exponential time complexity of **O(2ⁿ)**. ([Codecademy][1])

---

## 🧠 Intuition Behind the Naive Recursive Approach

At each index in the array, you have **two choices**:

1. **Include the current number** in the LIS *if* it’s greater than the last picked number.
2. **Skip the current number** and move to the next.

You recursively explore both choices, and the result is the **maximum length among these possibilities**.

To track the increasing property, we keep:

* `prev` — the value of the last selected element in the subsequence, and
* `index` — current position in the array.

If the current element is greater than `prev`, we can choose it and add 1 to the subsequence length.

---

## 📄 Recursive Java Program (Naive)

```java
public class LISRecursive {

    // Public API to start recursion
    public static int lengthOfLIS(int[] nums) {
        // Start with index 0 and previous value set to very small
        // so the first element can always be considered
        return helper(nums, 0, Integer.MIN_VALUE);
    }

    /**
     * Recursive helper to find LIS length
     * @param nums - input array
     * @param index - current index in array
     * @param prev - last included value in subsequence
     * @return maximum LIS length from index onward
     */
    private static int helper(int[] nums, int index, int prev) {
        // Base case: reached the end
        if (index == nums.length)
            return 0;

        // Option 1: skip this element
        int skip = helper(nums, index + 1, prev);

        // Option 2: include this element (if it is greater than prev)
        int take = 0;
        if (nums[index] > prev) {
            take = 1 + helper(nums, index + 1, nums[index]);
        }

        // Return the best of include vs skip
        return Math.max(skip, take);
    }

    // Test the recursive LIS
    public static void main(String[] args) {
        int[] nums = {10, 9, 2, 5, 3, 7, 101, 18};
        System.out.println("Length of LIS: " + lengthOfLIS(nums));
    }
}
```

---

## 🧩 How It Works Step-by-Step

Let’s follow this sample:

```
nums = [10, 9, 2, 5, 3, 7, 101, 18]
```

### Recursion Tree Example

At index 0 (`10`):

* **Skip 10:** Explore LIS starting at index 1
* **Take 10:** LIS length = 1 + LIS from index 1 with prev = 10

Then at index 1 (`9`):

* If `prev = Integer.MIN_VALUE`, both skip and take are possible
* If `prev = 10`, skip only (since 9 ≤ 10)

This branching continues recursively for every index and every possible decision.
The maximum path through all decisions yields the LIS length. ([Medium][2])

---

## 📊 Time & Space Complexity

* **Time Complexity:**
  The recursion explores all combinations of including/skipping elements → `O(2ⁿ)` in the worst case. ([Codecademy][1])

* **Space Complexity:**
  Recursion depth goes up to `n`, so **O(n)** extra space (stack).

👉 This naive recursive method is useful for understanding the problem but **not suitable for large inputs**. The next improvement step is **memoization** (top-down DP) which reduces redundant work significantly. ([GeeksforGeeks][3])

---

## 📌 Summary

| Approach                      | Description                                      | Time Complexity |                   |
|-------------------------------|--------------------------------------------------|-----------------|-------------------|
| **Naive Recursion**           | Try all subsequences with include/skip decisions | **O(2ⁿ)**       |                   |
| **Dynamic Programming**       | Avoids repeated work                             | **O(n²)**       |                   |
| **Optimized (Binary Search)** | Best method for LIS                              | **O(n log n)**  | ([Codecademy][1]) |

---

If you’d like, I can also show the **memoized** version in Java — which is still recursive but much faster!

[1]: https://www.codecademy.com/article/longest-increasing-subsequence-lis-explained?utm_source=chatgpt.com "Longest Increasing Subsequence (LIS) Explained"
[2]: https://medium.com/appkodersolution/longest-increasing-subsequence-leetcode-q300-optimized-java-solution-using-dynamic-programming-a72b7b9f3270?utm_source=chatgpt.com "Longest Increasing Subsequence(LeetCode Q300) ..."
[3]: https://www.geeksforgeeks.org/dsa/longest-increasing-subsequence-dp-3/?utm_source=chatgpt.com "Longest Increasing Subsequence (LIS)"

Here’s a **dynamic programming (DP) solution in Java** for the *Longest Increasing Subsequence (LIS)* problem — with a **detailed explanation** of how and why it works.

---

## 🧠 Dynamic Programming Approach (Bottom-Up)

The idea of **dynamic programming** is to solve the problem by **breaking it into overlapping subproblems** and building up solutions from smaller cases to larger ones.

### 📌 Key Insight

Let’s define a DP array:

```
dp[i] = the length of the longest increasing subsequence that **ends at index i**
```

Since any single element is itself a valid subsequence, the **minimum LIS ending at each index is 1**. That becomes our base case. ([GeeksforGeeks][1])

To fill the DP array:

* For each element `nums[i]`, look at all earlier elements `nums[j]` where `0 ≤ j < i`
* If `nums[j] < nums[i]`, then `nums[i]` can extend the increasing subsequence ending at `j`
* So we update:

```
dp[i] = max(dp[i], dp[j] + 1)
```

This gradually builds the longest subsequence lengths ending at each position. ([GeeksforGeeks][1])

At the end, the **maximum value in dp[]** is the length of the longest increasing subsequence in the whole array. ([Java Guides][2])

---

## 📄 Java Code (Dynamic Programming)

```java
import java.util.Arrays;

public class LISDynamic {

    // Function to return the length of LIS
    public static int lengthOfLIS(int[] nums) {
        // Edge case: empty array
        if (nums == null || nums.length == 0) {
            return 0;
        }

        int n = nums.length;

        // dp[i] will hold the LIS length ending at index i
        int[] dp = new int[n];

        // Initialize dp with 1 (every element is a subsequence of itself)
        Arrays.fill(dp, 1);

        int maxLen = 1; // at least 1 if array is non-empty

        // Build the dp array in bottom-up manner
        for (int i = 1; i < n; i++) {
            for (int j = 0; j < i; j++) {
                // If nums[i] can continue the subsequence at j
                if (nums[i] > nums[j]) {
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
            // Update the global maximum LIS length
            maxLen = Math.max(maxLen, dp[i]);
        }

        return maxLen;
    }

    // Test the function
    public static void main(String[] args) {
        int[] nums = {10, 9, 2, 5, 3, 7, 101, 18};
        System.out.println("Length of LIS: " + lengthOfLIS(nums));
    }
}
```

**Output:**

```
Length of LIS: 4
```

(*Because one LIS is [2, 3, 7, 101].*) ([Java Guides][2])

---

## 🧩 How the DP Works — Example

Consider:

```
nums: [10, 9, 2, 5, 3, 7, 101, 18]
```

Initial dp:

```
dp = [1, 1, 1, 1, 1, 1, 1, 1]
```

Now process:

* At `i = 2` (value **2**):
  Compare with `10` and `9` → both are larger → no update → dp[2] remains 1

* At `i = 3` (value **5**):
  Compare with `2` → 5 > 2 → dp[3] = dp[2] + 1 = 2

… and so on, updating dp step by step.
Eventually dp becomes something like:

```
dp = [1, 1, 1, 2, 2, 3, 4, 4]
```

Maximum is **4**, which is the LIS length. ([GeeksforGeeks][1])

---

## 🚀 Time & Space Complexity

| Approach                  | Time       | Space    |                      |
|---------------------------|------------|----------|----------------------|
| Recursive (naive)         | O(2ⁿ)      | O(n)     |                      |
| **Dynamic Programming**   | **O(n²)**  | **O(n)** |                      |
| Optimized (Binary Search) | O(n log n) | O(n)     | ([GeeksforGeeks][1]) |

* **Time:** Two nested loops over the array → `O(n²)`
* **Space:** The `dp` array of length `n` → `O(n)` ([Java Guides][2])

---

## 📌 Why DP Works Here

Dynamic programming is effective because:

1. **Optimal Substructure:** The LIS ending at `i` depends only on LIS values ending at earlier indices. ([GeeksforGeeks][1])
2. **Overlapping Subproblems:** The dp array **remembers results** so we don’t recompute LIS for the same position repeatedly, unlike in naive recursion. ([codecademy.com][3])

---

If you want, I can also provide the **optimized LIS solution in O(n log n)** — which is even faster!

[1]: https://www.geeksforgeeks.org/dynamic-programming-set-3-longest-increasing-subsequence/?utm_source=chatgpt.com "Longest Increasing Subsequence (LIS) - GeeksforGeeks"
[2]: https://www.javaguides.net/2023/12/longest-increasing-subsequence-java.html?utm_source=chatgpt.com "Longest Increasing Subsequence - Java Solution"
[3]: https://www.codecademy.com/article/longest-increasing-subsequence-lis-explained?utm_source=chatgpt.com "Longest Increasing Subsequence (LIS) Explained | Codecademy"
