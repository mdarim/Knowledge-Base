Here are two classic Java implementations of **Merge Sort** and **Quick Sort** using divide-and-conquer. Both examples sort an `int[]` array in ascending order.

---

## 📌 1) Merge Sort (Java)

Merge sort recursively splits the array into halves and merges them back in order. It guarantees **O(n log n)** time complexity. ([GeeksforGeeks][1])

```java
public class MergeSort {

    // Main mergeSort method
    public static void mergeSort(int[] arr, int left, int right) {
        if (left < right) {
            int mid = left + (right - left) / 2;

            // Sort first half
            mergeSort(arr, left, mid);

            // Sort second half
            mergeSort(arr, mid + 1, right);

            // Merge sorted halves
            merge(arr, left, mid, right);
        }
    }

    // Merge two sorted subarrays
    public static void merge(int[] arr, int left, int mid, int right) {
        int n1 = mid - left + 1;
        int n2 = right - mid;

        int[] leftArr = new int[n1];
        int[] rightArr = new int[n2];

        System.arraycopy(arr, left, leftArr, 0, n1);
        System.arraycopy(arr, mid + 1, rightArr, 0, n2);

        int i = 0, j = 0, k = left;

        while (i < n1 && j < n2) {
            if (leftArr[i] <= rightArr[j]) {
                arr[k++] = leftArr[i++];
            } else {
                arr[k++] = rightArr[j++];
            }
        }

        while (i < n1) arr[k++] = leftArr[i++];
        while (j < n2) arr[k++] = rightArr[j++];
    }

    public static void main(String[] args) {
        int[] arr = {12, 11, 13, 5, 6, 7};
        mergeSort(arr, 0, arr.length - 1);

        System.out.println("Sorted array:");
        for (int num : arr) {
            System.out.print(num + " ");
        }
    }
}
```

---

## 📌 2) Quick Sort (Java)

Quick sort picks a **pivot**, partitions the array so that smaller elements are on the left and larger ones on the right, and then sorts each partition. Average time complexity is **O(n log n)** but worst case is **O(n²)**. ([Java Development Journal][2])

```java
public class QuickSort {

    public static void quickSort(int[] arr, int low, int high) {
        if (low < high) {
            int pivotIndex = partition(arr, low, high);

            // Recursively sort elements before
            // and after partition
            quickSort(arr, low, pivotIndex - 1);
            quickSort(arr, pivotIndex + 1, high);
        }
    }

    public static int partition(int[] arr, int low, int high) {
        int pivot = arr[high];
        int i = low - 1;

        for (int j = low; j < high; j++) {
            if (arr[j] <= pivot) {
                i++;
                int temp = arr[i];
                arr[i] = arr[j];
                arr[j] = temp;
            }
        }

        int temp = arr[i + 1];
        arr[i + 1] = arr[high];
        arr[high] = temp;

        return i + 1;
    }

    public static void main(String[] args) {
        int[] arr = {10, 7, 8, 9, 1, 5};
        quickSort(arr, 0, arr.length - 1);

        System.out.println("Sorted array:");
        for (int num : arr) {
            System.out.print(num + " ");
        }
    }
}
```

---

## 📊 Summary

| Algorithm  | Time (best/avg/worst)       | Space    | Stable?                        |
|------------|-----------------------------|----------|--------------------------------|
| Merge Sort | O(n log n) all cases        | O(n)     | Yes                            |
| Quick Sort | O(n log n)/avg, O(n²) worst | O(log n) | No (in typical implementation) |

🔹 Use **Merge Sort** when stability and guaranteed performance matter.
🔹 Use **Quick Sort** when average performance and in-place sorting are priorities.

---

Would you like versions that sort **generic types** (e.g., `Comparable<T>`) or that take a **custom comparator**?

[1]: https://www.geeksforgeeks.org/merge-sort/?utm_source=chatgpt.com "Merge Sort - GeeksforGeeks"
[2]: https://javadevjournal.com/algorithms/quick-sort/?utm_source=chatgpt.com "QuickSort Algorithm | Java Development Journal"
