---
id: 202607261334
title: "Essential Array Algorithmic Patterns: Hashing, Two Pointers, Sliding Window, and Prefix Sums"
aliases:
  - array-algorithmic-patterns
  - array-interview-patterns
  - sliding-window-two-pointers
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/arrays
  - topic/algorithms
  - topic/performance
date_created: 2026-07-26
mastery_level: 1
---

# Essential Array Algorithmic Patterns: Hashing, Two Pointers, Sliding Window, and Prefix Sums

> **TL;DR:** Senior software engineers do not memorize hundreds of array problems; they recognize core **algorithmic patterns**. Master five essential array patterns: **Deduplication (`Set`)**, **Frequency Counter (`Map`/`Object`)**, **Two Pointers**, **Sliding Window**, and **Prefix Sums**.

## The Five Core Array Algorithmic Patterns

Transforming inefficient $O(n^2)$ brute-force solutions into optimal $O(n)$ or $O(\log n)$ algorithms comes from recognizing these five patterns:

| Pattern | Problem Indicator | Recommended Approach | Time Complexity |
| :--- | :--- | :--- | :---: |
| **1. Deduplication** | "Remove duplicate values from list" | `[...new Set(arr)]` | $O(n)$ |
| **2. Frequency Counter** | "Count occurrences / Find anagram / First non-repeating" | Hash Object `{}` or `Map` | $O(n)$ |
| **3. Two Pointers** | "Find pair in sorted array / Palindrome check" | Left ($0$) & Right ($\text{len}-1$) pointers moving inward | $O(n)$ |
| **4. Sliding Window** | "Max sum of $K$ consecutive items / Substring" | Slide window: add right element, subtract left element | $O(n)$ |
| **5. Prefix Sum** | "Multiple range-sum queries $L..R$ on static array" | Cumulative sum array: $\text{prefix}[R] - \text{prefix}[L-1]$ | $O(1)$ query |

---

## 1. Two Pointers Pattern ($O(n)$ vs Brute Force $O(n^2)$)

Given a **sorted array**, find two numbers that sum up to a `target`:
- **Brute Force:** Two nested loops $\rightarrow O(n^2)$.
- **Two Pointers:** Place `left = 0` and `right = length - 1`. 
  - If `sum === target`: Return `[left, right]`.
  - If `sum < target`: Move `left++` (increase sum).
  - If `sum > target`: Move `right--` (decrease sum).

---

## 2. Sliding Window Pattern ($O(n)$ vs Brute Force $O(n \times k)$)

Find maximum sum of $K$ consecutive elements in an array:
- Instead of re-summing all $K$ elements for every window position, **reuse previous window sum**:

$$\text{newWindowSum} = \text{oldWindowSum} + \text{arr}[i] - \text{arr}[i - K]$$

---

## 3. Prefix Sum Pattern ($O(1)$ Query)

For static arrays with thousands of range sum queries between indices $L$ and $R$:
1. Precompute `prefix[i] = prefix[i-1] + arr[i]` in $O(n)$ time once.
2. Answer any range query $L..R$ instantly in $O(1)$ time:

$$\text{rangeSum}(L, R) = \text{prefix}[R] - \text{prefix}[L - 1]$$

---

## Canonical Code Example

This script implements clean, production-ready code for all five fundamental array algorithmic patterns:

```javascript
// --- 1. Deduplication (Set Pattern - O(n)) ---
const rawList = [1, 2, 2, 3, 1, 4];
const uniqueList = [...new Set(rawList)];
console.log("1. Deduplicated List:", uniqueList); // [1, 2, 3, 4]

// --- 2. Frequency Counter (Map Pattern - O(n)) ---
const items = ["apple", "banana", "apple", "orange", "apple"];
const frequencyMap = items.reduce((acc, item) => {
  acc[item] = (acc[item] || 0) + 1;
  return acc;
}, {});
console.log("2. Frequency Counts:", frequencyMap); // { apple: 3, banana: 1, orange: 1 }

// --- 3. Two Pointers (Target Pair Sum - O(n)) ---
function findPairSum(sortedArr, target) {
  let left = 0;
  let right = sortedArr.length - 1;

  while (left < right) {
    const currentSum = sortedArr[left] + sortedArr[right];
    if (currentSum === target) {
      return [sortedArr[left], sortedArr[right]];
    }
    if (currentSum < target) {
      left++;
    } else {
      right--;
    }
  }
  return null;
}
console.log("3. Two Pointers Pair Sum (Target 10):", findPairSum([1, 2, 4, 6, 8, 9], 10)); // [1, 9] or [2, 8]

// --- 4. Sliding Window (Max Sum of K = 3 Consecutive Items - O(n)) ---
function maxSubarraySum(arr, k) {
  if (arr.length < k) return null;

  let windowSum = 0;
  for (let i = 0; i < k; i++) {
    windowSum += arr[i]; // First window sum
  }

  let maxSum = windowSum;

  for (let i = k; i < arr.length; i++) {
    windowSum += arr[i] - arr[i - k]; // Slide window: add new right, subtract old left
    maxSum = Math.max(maxSum, windowSum);
  }
  return maxSum;
}
console.log("4. Sliding Window Max Sum (K=3):", maxSubarraySum([2, 4, 6, 1, 8, 3], 3)); // 15 (6 + 1 + 8)

// --- 5. Prefix Sum Range Query (O(1) Query Time) ---
function buildPrefixSums(arr) {
  const prefix = new Array(arr.length);
  prefix[0] = arr[0];
  for (let i = 1; i < arr.length; i++) {
    prefix[i] = prefix[i - 1] + arr[i];
  }
  return prefix;
}

function queryRangeSum(prefix, L, R) {
  if (L === 0) return prefix[R];
  return prefix[R] - prefix[L - 1];
}

const data = [2, 5, 3, 7, 4];
const prefixArray = buildPrefixSums(data); // [2, 7, 10, 17, 21]
console.log("5. Prefix Sum Range (Index 1 to 3: 5+3+7):", queryRangeSum(prefixArray, 1, 3)); // 15 (17 - 2)
```

---

## Related
* [[js-array-reduce-and-reduceright]] - Aggregation and frequency counting using reduce.
* [[js-array-searching-includes-vs-indexof-find]] - Linear vs binary search techniques.
* [[js-array-flat-and-flatmap]] - Array chunking and flattening algorithms.
* [[MOC - JS Advanced Functions & Performance]] - Time and space complexity analysis ($O(1)$, $O(n)$, $O(n^2)$).
* [[MOC - JS Built-in Objects & Utilities]] - Standard collections (`Set`, `Map`).
