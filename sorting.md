### In-place Algorithm
✅ Definition:

An algorithm is in-place if it uses only a small, constant amount of extra memory (typically O(1)), and the sorting is done by modifying the input array directly.
You're not creating a new array or data structure to hold the result.

Example

```ts
function swapInPlace(arr, i, j) {
    [arr[i], arr[j]] = [arr[j], arr[i]];
}

```

### Stable Sort
✅ Definition:

A sorting algorithm is stable if it preserves the original order of equal elements.

📌 Example:

Imagine sorting a list of people by age:
```ts
[
  { name: "Alice", age: 25 },
  { name: "Bob", age: 25 },
  { name: "Charlie", age: 30 }
]

```
If sorted by age stably, `Alice` will stay before `Bob` (since they had the same age and Alice was first).

🧠 Why it matters?
In multi-level sorting (e.g., first by age, then by name), stable sort ensures that the earlier sort (by name) is not lost.

### Space Complexity
✅ Definition:

The amount of extra memory (RAM) the algorithm needs to sort the data.

- It does NOT include the input array.
- Usually measured in Big-O notation: O(1), O(n), etc.

📌 Categories:

- O(1): Constant space (e.g., Bubble Sort)
- O(n): Linear space (e.g., Merge Sort uses temp arrays)

### Bubble Sort
Repeatedly swaps adjacent elements if they are in the wrong order. Simple but inefficient.
```ts
function bubbleSort(arr) {
    for (let i = 0; i < arr.length - 1; i++) {
        for (let j = 0; j < arr.length - 1 - i; j++) {
            if (arr[j] > arr[j + 1]) {
                [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
            }
        }
    }
    return arr;
}

```
- Time: O(n²)
- Space: O(1)
- Stable ✅
- In-place ✅

### Selection Sort
Finds the smallest element and places it at the beginning, repeating the process for the rest of the array.

✅ Use Case:
Good for small datasets, or when memory writes are expensive (minimizes swaps).

```ts
function selectionSort(arr) {
    for (let i = 0; i < arr.length; i++) {
        let min = i;
        for (let j = i + 1; j < arr.length; j++) {
            if (arr[j] < arr[min]) min = j;
        }
        [arr[i], arr[min]] = [arr[min], arr[i]];
    }
    return arr;
}

```

- Time: O(n²)
- Space: O(1)
- Stable: ❌
- In-place: ✅


### Quick Sort
Divide-and-conquer. Picks a pivot, partitions the array into < pivot and ≥ pivot, and recursively sorts them.
- Divide and conquer approach.
- Good average performance: O(n log n).
- Worst case: O(n²), but rare with good pivot strategies.

```ts
function quickSort(arr) {
    if (arr.length <= 1) return arr;
    const pivot = arr[arr.length - 1];
    const left = [];
    const right = [];
    for (let i = 0; i < arr.length - 1; i++) {
        arr[i] < pivot ? left.push(arr[i]) : right.push(arr[i]);
    }
    return [...quickSort(left), pivot, ...quickSort(right)];
}

```

- Time: Avg O(n log n), Worst O(n²)
- Space: O(log n)
- Stable: ❌
- In-place: ❌

### Merge Sort
Divide the array into halves, recursively sort each half, and merge them back together in order.
- Time: O(n log n)
- Space: O(n)
- Stable: ✅
- In-place: ❌

```ts
function mergeSort(arr) {
    if (arr.length <= 1) return arr;
    const mid = Math.floor(arr.length / 2);
    const left = mergeSort(arr.slice(0, mid));
    const right = mergeSort(arr.slice(mid));
    return merge(left, right);
}

function merge(left, right) {
    let result = [], i = 0, j = 0;
    while (i < left.length && j < right.length) {
        left[i] < right[j] ? result.push(left[i++]) : result.push(right[j++]);
    }
    return result.concat(left.slice(i)).concat(right.slice(j));
}
```

### Insertion Sort
Builds the sorted array one item at a time by inserting each item into its proper place. 

Use Case: Great for small or nearly sorted arrays.

- Simple and fast for small or nearly sorted arrays.
- Best case: O(n) (when array is already sorted).
- Average and worst: O(n²).

```ts
function insertionSort(arr) {
    for (let i = 1; i < arr.length; i++) {
        let key = arr[i], j = i - 1;
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = key;
    }
    return arr;
}
```

- Time: O(n²), Best: O(n)
- Space: O(1)
- Stable: ✅
- In-place: ✅

### Heap Sort
Uses a binary heap data structure to repeatedly remove the largest/smallest element and build the sorted array.

Use Case:
Good for performance-critical applications; not stable.
- Time: O(n log n)
- Space: O(1)
- Stable: ❌
- place: ✅

### Timsort
Timsort is a hybrid sorting algorithm derived from:

- Merge Sort (for stable merging of runs)
- Insertion Sort (for small or nearly sorted sections)

It was designed to perform well on real-world data that often contains sorted sequences (called "runs").

#### Where is Timsort Used?

- Python: sorted() and .sort() use Timsort.

- Java: Arrays.sort() for objects.

- JavaScript (modern V8 engines): .sort() often uses Timsort (or a variation of it).

#### How Timsort Works (Simplified)

- Identify runs: It scans the array to find already sorted subarrays ("runs"). 
E.g., [1, 2, 3] is a run.

- Sort small runs: If a run is too small, it uses insertion sort to extend it.

- Merge runs: Then uses merge sort to combine the sorted runs.

🧠 Why does this work well?

- In real-world data, lists often have partially sorted segments.
- Insertion sort is fast for small arrays.
- Merge sort handles large merges efficiently and stably.
  
