---
tags: [dsa, sorting, algorithms, competitive-programming]
aliases: [Sorting Algorithms, Sorts]
---

# Sorting Algorithms

> [!abstract] Overview
> This note covers the 8 sorting algorithms implemented in the project (`selection_sort.h`, `bubble_sort.h`, `insertion_sort.h`, `merge_sort.h`, `quick_sort.h`, `heap_sort.h`, `counting_sort.h`, `radix_sort.h`), driven from a common `main.cpp`. Each section has the core idea, the actual code, and complexity/stability notes. A full comparison table and a running list of algorithms *not yet implemented* (per the project's `README.md`) are at the bottom.

## Quick comparison

| Algorithm | Best | Average | Worst | Space | Stable | In-place | Comparison-based |
|---|---|---|---|---|---|---|---|
| [[#Selection Sort]] | O(n²) | O(n²) | O(n²) | O(1) | ❌ | ✅ | ✅ |
| [[#Bubble Sort]] | O(n) | O(n²) | O(n²) | O(1) | ✅ | ✅ | ✅ |
| [[#Insertion Sort]] | O(n) | O(n²) | O(n²) | O(1) | ✅ | ✅ | ✅ |
| [[#Merge Sort]] | O(n log n) | O(n log n) | O(n log n) | O(n) | ✅ | ❌ | ✅ |
| [[#Quick Sort]] | O(n log n) | O(n log n) | O(n²) | O(log n) | ❌ | ✅ | ✅ |
| [[#Heap Sort]] | O(n log n) | O(n log n) | O(n log n) | O(1) | ❌ | ✅ | ✅ |
| [[#Counting Sort]] | O(n+k) | O(n+k) | O(n+k) | O(n+k) | ✅ | ❌ | ❌ |
| [[#Radix Sort]] | O(d·(n+k)) | O(d·(n+k)) | O(d·(n+k)) | O(n+k) | ✅ | ❌ | ❌ |

*(n = number of elements, k = range of values, d = number of digits)*

---

## Selection Sort

> [!info] Idea
> Repeatedly find the **minimum** of the unsorted suffix and swap it into place at the front. `n-1` passes, each shrinking the unsorted region by one.

```cpp
void selectionSort(vector<int> &nums) {
  int n = nums.size();
  for (int i = 0; i <= n - 2; i++) {
    int mini = i;
    for (int j = i; j <= n - 1; j++) {
      if (nums[j] < nums[mini])
        mini = j;
    }
    swap(nums[i], nums[mini]);
  }
}
```

- Always does exactly `O(n²)` **comparisons**, regardless of input order — no early exit / best-case improvement.
- Only `O(n)` **swaps** total (one per outer iteration) — useful when swap cost >> comparison cost (e.g. sorting large records by key).
- **Not stable** by default (the swap can jump an equal element past others of the same value).

---

## Bubble Sort

> [!info] Idea
> Repeatedly walk the array swapping adjacent out-of-order pairs, "bubbling" the largest remaining element to the end each pass.

```cpp
void bubbleSort(vector<int> &nums) {
  int n = nums.size();
  for (int i = 0; i < n - 1; i++) {
    bool swapped = false;
    for (int j = 0; j < n - i - 1; j++) {
      if (nums[j] > nums[j + 1]) {
        swap(nums[j], nums[j + 1]);
        swapped = true;
      }
    }
    if (!swapped)
      break;
  }
}
```

- The `swapped` flag gives the **optimized/adaptive variant**: if a full pass makes no swaps, the array is already sorted → early exit → `O(n)` best case on already-sorted input.
- Stable (only swaps strictly-greater adjacent pairs).
- Rarely used in practice beyond teaching, due to `O(n²)` average/worst case with a high constant factor (lots of swaps).

---

## Insertion Sort

> [!info] Idea
> Grows a sorted prefix one element at a time — take the next element and shift it backward through the sorted prefix until it lands in the right spot.

```cpp
void insertionSort(vector<int> &nums) {
  int n = nums.size();
  for (int i = 0; i < n; i++) {
    int j = i;
    while (j > 0 && nums[j - 1] > nums[j]) {
      swap(nums[j], nums[j - 1]);
      j--;
    }
  }
}
```

- `O(n)` best case on nearly-sorted data (inner `while` barely runs) — this makes it the go-to choice as the **base case for hybrid sorts** (e.g. introsort/TimSort switch to insertion sort for small subarrays).
- Stable, in-place, adaptive.
- Uses `swap` at every step here — a micro-optimization would shift elements instead of swapping (avoids redundant writes), but asymptotically the same.

---

## Merge Sort

> [!info] Idea
> Classic divide-and-conquer: split the array in half recursively down to single elements, then **merge** sorted halves back together.

```cpp
void merge(vector<int> &arr, int low, int mid, int high) {
  vector<int> temp;
  int left = low, right = mid + 1;

  while (left <= mid && right <= high) {
    if (arr[left] <= arr[right]) temp.push_back(arr[left++]);
    else temp.push_back(arr[right++]);
  }
  while (left <= mid) temp.push_back(arr[left++]);
  while (right <= high) temp.push_back(arr[right++]);

  for (int i = low; i <= high; i++)
    arr[i] = temp[i - low];
}

void mergeSort(vector<int> &arr, int low, int high) {
  if (low >= high) return;
  int mid = (low + high) / 2;
  mergeSort(arr, low, mid);
  mergeSort(arr, mid + 1, high);
  merge(arr, low, mid, high);
}
```

- `arr[left] <= arr[right]` (using `<=`, not `<`) is what makes this **stable** — ties keep left-side order.
- `O(n log n)` guaranteed in all cases — no worst-case degradation, unlike quicksort.
- `O(n)` extra space for the `temp` buffer during merges — the main downside vs in-place sorts.
- Merge sort's recursive splitting is also the backbone of the [[Segment Tree]]'s build (divide range in half, combine children).

---

## Quick Sort

> [!info] Idea
> Divide-and-conquer via **partitioning**: pick a pivot, rearrange so everything `≤ pivot` is left of it and everything `>` is right, then recurse on each side. No merge step needed — partitioning does the work in place.

```cpp
int partition(vector<int> &arr, int low, int high) {
  int pivot = arr[low];
  int i = low, j = high;

  while (i < j) {
    while (i <= high - 1 && arr[i] <= pivot) i++;
    while (j >= low + 1 && arr[j] > pivot) j--;
    if (i < j) swap(arr[i], arr[j]);
  }

  swap(arr[low], arr[j]);
  return j;
}

void quickSort(vector<int> &arr, int low, int high) {
  if (low < high) {
    int p = partition(arr, low, high);
    quickSort(arr, low, p - 1);
    quickSort(arr, p + 1, high);
  }
}
```

- This implementation uses **first element as pivot** (`arr[low]`) — the classic Hoare-style two-pointer partition.
- Worst case `O(n²)` happens on already-sorted or reverse-sorted input with a first-element pivot (each partition is maximally unbalanced). Mitigations not present here but common in production sorts:
  - Random pivot / median-of-three pivot selection
  - Switching to insertion sort for small subarrays
  - Switching to heap sort on bad recursion depth (→ **introsort**, in the "remaining" list below)
- **Not stable** (partitioning swaps can reorder equal elements).
- In-place: `O(log n)` extra space for recursion stack (average); `O(n)` worst case if recursion is unbalanced.

---

## Heap Sort

> [!info] Idea
> Build a **max-heap** from the array, then repeatedly swap the root (max) to the end and shrink the heap, restoring the heap property (`heapify`) each time.

```cpp
void heapify(vector<int> &a, int n, int i) {
  int largest = i;
  int l = 2 * i + 1;
  int r = 2 * i + 2;

  if (l < n && a[l] > a[largest]) largest = l;
  if (r < n && a[r] > a[largest]) largest = r;

  if (largest != i) {
    swap(a[i], a[largest]);
    heapify(a, n, largest);
  }
}

void buildHeap(vector<int> &a, int n) {
  for (int i = n / 2 - 1; i >= 0; i--)
    heapify(a, n, i);
}

void heapHelper(vector<int> &a, int n) {
  for (int i = n - 1; i > 0; i--) {
    swap(a[0], a[i]);
    heapify(a, i, 0);
  }
}

void heapSort(vector<int> &nums) {
  int n = nums.size();
  buildHeap(nums, n);
  heapHelper(nums, n);
}
```

- `buildHeap` starting from `n/2 - 1` down to `0` is the standard **O(n) heap construction** (bottom-up heapify, cheaper than n inserts which would be `O(n log n)`).
- `heapHelper` repeatedly extracts the max to the end of the shrinking array — this is exactly "heap-based priority queue extraction" applied in place.
- Guaranteed `O(n log n)` in all cases, `O(1)` extra space — but **not stable**, and generally has worse real-world cache performance than quicksort/mergesort due to the heap's jumping access pattern.

---

## Counting Sort

> [!info] Idea
> **Non-comparison** sort: count occurrences of each value, convert counts to prefix sums (giving each value's final position range), then place elements directly into their sorted position.

```cpp
void countingSort(vector<int> &nums) {
  int n = nums.size();
  if (n == 0) return;

  int maxi = *max_element(nums.begin(), nums.end());
  vector<int> count(maxi + 1, 0);
  for (int x : nums) count[x]++;

  for (int i = 1; i <= maxi; i++)
    count[i] += count[i - 1];

  vector<int> output(n);
  for (int i = n - 1; i >= 0; i--) {
    output[count[nums[i]] - 1] = nums[i];
    count[nums[i]]--;
  }

  nums = output;
}
```

- `O(n + k)` where `k = maxi` — great when the value range is small relative to `n`, terrible when values are sparse/huge (e.g. sorting 10 numbers ranging up to 10⁹ would allocate a billion-sized array).
- Iterating `i = n-1` down to `0` in the placement loop (rather than forward) is what makes it **stable** — equal elements keep their relative input order.
- This implementation only handles **non-negative integers** (no offset for negatives) — a common extension is shifting by `-min` first to support negative values.
- Building block for [[#Radix Sort]] below (radix sort = counting sort applied per-digit).

---

## Radix Sort

> [!info] Idea
> Sort integers **digit by digit**, from least significant digit (LSD) to most significant, using a stable counting sort as the subroutine at each digit position. After processing all digits, the array is fully sorted.

```cpp
void helper(vector<int> &nums, int exp) {
  int n = nums.size();
  vector<int> count(10, 0);
  vector<int> output(n);

  for (int x : nums)
    count[(x / exp) % 10]++;

  for (int i = 1; i < 10; i++)
    count[i] += count[i - 1];

  for (int i = n - 1; i >= 0; i--) {
    int digit = (nums[i] / exp) % 10;
    output[count[digit] - 1] = nums[i];
    count[digit]--;
  }

  nums = output;
}

void radixSort(vector<int> &nums) {
  if (nums.empty()) return;
  int maxi = *max_element(nums.begin(), nums.end());
  for (int exp = 1; maxi / exp > 0; exp *= 10)
    helper(nums, exp);
}
```

- This is **LSD (Least Significant Digit) radix sort** — processes ones place, then tens, then hundreds, etc. (`exp *= 10` each pass), stopping once `exp` exceeds the largest number.
- Correctness depends entirely on each digit-pass being **stable** — that's why it reuses the exact counting-sort pattern (backward placement loop) rather than any unstable sort.
- `O(d·(n+k))` where `d` = number of digits in the max value, `k` = 10 (digit range) — effectively `O(n)` for fixed-width integers.
- Like counting sort, this version only supports **non-negative integers**; negatives need separate handling (e.g. splitting into two buckets by sign, or an offset).
- MSD (Most Significant Digit) radix sort is the alternative direction — listed as not-yet-implemented below; it's better suited to **string sorting** (variable-length keys) since it can short-circuit per-bucket recursion.

---

## Driver (`main.cpp`)

```cpp
#include "bubble_sort.h"
#include "heap_sort.h"
#include "insertion_sort.h"
#include "merge_sort.h"
#include "quick_sort.h"
#include "selection_sort.h"
#include "counting_sort.h"
#include "radix_sort.h"

int main() {
  vector<int> arr = {64, 25, 12, 22, 11, 90, 5, 18};
  // uncomment one sort at a time to test:
  // selectionSort(arr);
  // bubbleSort(arr);
  // ...
}
```
- Each algorithm lives in its own header (`#pragma once` guarded), included independently — clean separation for benchmarking/testing one algorithm at a time by uncommenting a single line.

---

## Not yet implemented (from `README.md`)

> [!todo] Tracked for later
> The project README lists a large backlog of sorts, grouped by category. Keeping this here as a checklist / map-of-content for future notes — each of these deserves its own note once implemented.

### Comparison sorts
Shell Sort · Comb Sort · Cocktail Shaker Sort · Gnome Sort · Cycle Sort · Tree Sort · Tournament Sort · Smoothsort · Library Sort · Patience Sort · Strand Sort · Block Sort · WikiSort · TimSort · Introsort · Spreadsort · Cartesian Tree Sort · Weak Heap Sort · Splay Sort · Treap Sort · Red-Black Tree Sort · AVL Tree Sort

### Non-comparison sorts
Radix Sort (MSD) · Bucket Sort · Pigeonhole Sort · Bead Sort · Flash Sort · Burstsort · American Flag Sort · Postman Sort · Address Calculation Sort · ProxmapSort

### Parallel / network sorts
Bitonic Sort · Odd-Even Merge Sort · Odd-Even Transposition Sort · Pairwise Sorting Network · Bose-Nelson Sort · Shear Sort · Column Sort · Sample Sort · Parallel Merge Sort · Parallel Quick Sort · AKS Sorting Network

### External sorting
External Merge Sort · Polyphase Merge Sort · Balanced Multiway Merge Sort · Cascade Merge Sort · Replacement Selection Sort

### Specialized / cache-friendly
GrailSort · Block Merge Sort · Cache-Oblivious Funnel Sort · Cache-Aware Merge Sort · CubeSort · Adaptive Shivers Sort

### Linked list sorting
Natural Merge Sort · Bottom-Up Merge Sort · List Quick Sort

### String sorting
3-Way Radix Quicksort · MSD String Sort · LSD String Sort · Multikey Quicksort

### Distributed / big data
Sample Sort · Bucket-Based Distributed Sort · Hadoop TeraSort · External Radix Sort

### Educational / rare
Pancake Sort · Spaghetti Sort · Sleep Sort · Stooge Sort · Slow Sort · Bogo Sort · Bozo Sort · Permutation Sort · Miracle Sort (fictional) · Stalin Sort (joke)

### Hybrid sorts
Introsort · TimSort · Spreadsort · Introselect · Dual-Pivot QuickSort · BlockQuicksort · pdqsort

> [!tip] Most worth learning next
> Bucket Sort · Shell Sort · Tree Sort · TimSort (concept + impl) · Introsort (concept) · pdqsort (concept) · Bitonic Sort (if studying parallel algos) · External Merge Sort (concept)

## See also
- [[Fenwick Tree (BIT)]]
- [[Segment Tree]]
