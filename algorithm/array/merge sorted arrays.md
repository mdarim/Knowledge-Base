Below is a simple and common way to **merge two sorted arrays into a single sorted array in Java** using the two-pointer approach (linear time). ([PREP INSTA][1])

### ✅ Java Code to Merge Two Sorted Arrays

```java
public class MergeSortedArrays {

    public static int[] mergeArrays(int[] arr1, int[] arr2) {
        int n = arr1.length;
        int m = arr2.length;
        int[] merged = new int[n + m];

        int i = 0, j = 0, k = 0;

        // Compare and merge
        while (i < n && j < m) {
            if (arr1[i] <= arr2[j]) {
                merged[k++] = arr1[i++];
            } else {
                merged[k++] = arr2[j++];
            }
        }

        // Copy any remaining elements from arr1
        while (i < n) {
            merged[k++] = arr1[i++];
        }

        // Copy any remaining elements from arr2
        while (j < m) {
            merged[k++] = arr2[j++];
        }

        return merged;
    }

    public static void main(String[] args) {
        int[] arr1 = {1, 3, 5, 7};
        int[] arr2 = {2, 4, 6, 8};

        int[] result = mergeArrays(arr1, arr2);
        System.out.print("Merged Array: ");
        for (int num : result) {
            System.out.print(num + " ");
        }
    }
}
```

### 📌 How It Works

* **Two pointers** `i` and `j` traverse `arr1` and `arr2` respectively.
* At each step, the smaller of the two current elements is added to the merged array.
* Once one array is exhausted, the remaining elements of the other are appended. ([PREP INSTA][1])

### ⏱ Efficiency

* **Time Complexity:** `O(n + m)` — linear merge since both are already sorted. ([PREP INSTA][1])
* **Space Complexity:** `O(n + m)` for the new merged array.

---

### 🟢 Alternative: Using Built-in Sort *(simpler but less efficient for sorted inputs)*

If you simply want a one-liner and don’t care about optimal performance for already sorted arrays:

```java
import java.util.Arrays;

int[] merged = IntStream.concat(Arrays.stream(arr1), Arrays.stream(arr2))
                        .sorted()
                        .toArray();
```

This uses Java Streams but runs in `O((n+m) log(n+m))`, which is slower than the two-pointer approach. ([javaconceptoftheday.com][2])

---

If you need to merge into a fixed buffer (like for a LeetCode problem), I can show that version too!

[1]: https://prepinsta.com/java-program/merging-two-sorted-arrays/?utm_source=chatgpt.com "Merging Two Sorted Arrays in Java"
[2]: https://javaconceptoftheday.com/merge-two-arrays-into-single-sorted-array-without-duplicates-in-java/?utm_source=chatgpt.com "Merge Two Arrays Into Single Sorted Array Without ..."
