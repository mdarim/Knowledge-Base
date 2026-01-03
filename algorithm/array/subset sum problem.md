Here are **working Java implementations for the Subset Sum problem** (deciding whether a subset of a given array sums to a target value) with both **recursive** and **dynamic programming** approaches.

---

## ✅ 1) **Recursive (Brute Force) Approach**

This checks **all combinations** of elements to see if any subset sums to the target. It runs in (O(2^n)) time. ([GeeksforGeeks][1])

```java
public class SubsetSumRecursive {

    // Recursive function to check subset sum
    static boolean isSubsetSum(int[] arr, int n, int sum) {
        // Base cases
        if (sum == 0) return true;   // target reached
        if (n == 0) return false;    // no items left

        // If last element is greater than remaining sum,
        // skip it
        if (arr[n - 1] > sum)
            return isSubsetSum(arr, n - 1, sum);

        // Otherwise, check both including and excluding it
        return isSubsetSum(arr, n - 1, sum)
            || isSubsetSum(arr, n - 1, sum - arr[n - 1]);
    }

    public static void main(String[] args) {
        int[] arr = {3, 34, 4, 12, 5, 2};
        int sum = 9;

        if (isSubsetSum(arr, arr.length, sum))
            System.out.println("Subset with given sum exists.");
        else
            System.out.println("No subset with given sum.");
    }
}
```

---

## ✅ 2) **Dynamic Programming (Bottom-Up, Tabulation)**

This is much more efficient for larger `n` and small/moderate sum values: it runs in (O(n \cdot target)) time. ([GeeksforGeeks][1])

```java
public class SubsetSumDP {

    // Returns true if there is a subset with sum equals target
    static boolean isSubsetSum(int[] arr, int target) {
        int n = arr.length;
        boolean[][] dp = new boolean[n + 1][target + 1];

        // If sum is 0, empty subset always exists
        for (int i = 0; i <= n; i++)
            dp[i][0] = true;

        // Fill the table bottom-up
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= target; j++) {
                if (arr[i - 1] > j) {
                    dp[i][j] = dp[i - 1][j];  // exclude
                } else {
                    dp[i][j] = dp[i - 1][j]     // exclude
                               || dp[i - 1][j - arr[i - 1]]; // include
                }
            }
        }

        return dp[n][target];
    }

    public static void main(String[] args) {
        int[] arr = {3, 1, 5, 9, 12};
        int target = 8;

        boolean exists = isSubsetSum(arr, target);
        System.out.println(exists
            ? "There is a subset with sum " + target
            : "No subset with sum " + target);
    }
}
```

---

## 🧠 Notes & Tips

### ✔ When to use which method

* **Recursive approach:** simple and good for learning or small `n`.
* **DP approach:** much faster for larger inputs, especially when many elements and moderate sums. ([GeeksforGeeks][1])

### ✔ Understanding the logic

Both approaches explore whether each element can be **included or excluded**, looking for combinations where the sum equals the target. ([GeeksforGeeks][1])

---

## ⭐ Optional: Space-Optimized DP (1D)

For even better memory usage, you can use a 1D `boolean` array to track reachable sums — just loop backwards when updating the array. (Let me know if you want that version!)

---

If you want also to **print the actual subset elements**, I can show that too!

[1]: https://www.geeksforgeeks.org/subset-sum-problem-dp-25/?utm_source=chatgpt.com "Subset Sum Problem - GeeksforGeeks"

Below is a **Java implementation of the Subset Sum problem that *prints the actual subsets*** whose sum equals a given target. It uses **dynamic programming** to determine which sums are possible and then **backtracks** (reconstructs) the subsets from the DP table. ([GeeksforGeeks][1])

---

## 📌 Java: Subset Sum – Print All Subsets Matching Target

```java
import java.util.ArrayList;
import java.util.List;

public class SubsetSumPrint {

    // Backtracking using dp table to collect subsets
    static void findSubsets(int[] arr, boolean[][] dp, int i, int target,
                            List<Integer> current, List<List<Integer>> result) {

        // Base case: if target is 0, record current subset
        if (target == 0) {
            result.add(new ArrayList<>(current));
            return;
        }

        // If no elements left, just return
        if (i == 0) {
            return;
        }

        // If excluding the element at i-1 still leads to a valid subset
        if (dp[i - 1][target]) {
            findSubsets(arr, dp, i - 1, target, current, result);
        }

        // If including the element at i-1 is possible (and doesn't exceed target)
        if (target >= arr[i - 1] && dp[i - 1][target - arr[i - 1]]) {
            current.add(arr[i - 1]);
            findSubsets(arr, dp, i - 1, target - arr[i - 1], current, result);

            // Backtrack (remove last element to restore state)
            current.remove(current.size() - 1);
        }
    }

    // Builds dp table then calls backtracking
    static List<List<Integer>> subsetSum(int[] arr, int target) {
        int n = arr.length;
        List<List<Integer>> result = new ArrayList<>();

        boolean[][] dp = new boolean[n + 1][target + 1];

        // Initialize: sum of 0 is always possible (empty subset)
        for (int i = 0; i <= n; i++) {
            dp[i][0] = true;
        }

        // Build dp table
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= target; j++) {
                dp[i][j] = dp[i - 1][j];
                if (j >= arr[i - 1] && dp[i - 1][j - arr[i - 1]]) {
                    dp[i][j] = true;
                }
            }
        }

        // If no subset with target sum exists, return empty list
        if (!dp[n][target]) {
            return result;
        }

        // Backtrack to find all subsets
        findSubsets(arr, dp, n, target, new ArrayList<>(), result);
        return result;
    }

    public static void main(String[] args) {
        int[] arr = {5, 2, 3, 10, 6, 8};
        int target = 10;

        List<List<Integer>> subsets = subsetSum(arr, target);

        // Print results
        if (subsets.isEmpty()) {
            System.out.println("No subset with sum " + target + " found.");
        } else {
            System.out.println("Subsets with sum " + target + ":");
            for (List<Integer> subset : subsets) {
                System.out.println(subset);
            }
        }
    }
}
```

---

## 🧠 How It Works

### 🔹 Step 1 — DP Table (Feasibility)

Build a boolean `dp[i][j]` table where:

* `dp[i][j] = true` means a subset of the first `i` elements can sum to `j`.
* Base case: sum 0 is always possible with no elements. ([GeeksforGeeks][1])

### 🔹 Step 2 — Backtracking

Starting from the bottom (`dp[n][target]`):

* If `dp[i - 1][target]` is true → explore the *exclude* branch.
* If including `arr[i - 1]` leads to a valid sum → include it and recurse.
* Use list backtracking to avoid duplicates. ([GeeksforGeeks][1])

---

## 🧾 Example Output

For:

```text
arr = [5, 2, 3, 10, 6, 8]
target = 10
```

Output might be:

```
Subsets with sum 10:
[2, 8]
[10]
[5, 3, 2]
```

(This lists all combinations of elements that sum to 10.) ([GeeksforGeeks][1])

---

## 📝 Notes

✔ If you just want *one* subset, you can stop backtracking early.
✔ If you want *unique* subsets ignoring order, sort the array before processing.
✔ This handles positive integers; for negative numbers, the DP approach needs adjustments because ranges extend below zero.

---

If you’d like a **space-optimized** or **recursive (no DP) version** too, just let me know!

[1]: https://www.geeksforgeeks.org/perfect-sum-problem-print-subsets-given-sum/?utm_source=chatgpt.com "Print all subsets with given sum - GeeksforGeeks"
