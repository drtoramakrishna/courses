# Chapter 3: Longest Increasing Subsequence (LIS)

## 🎯 Learning Objectives
- Understand the LIS problem and its applications
- Master dynamic programming approaches (O(n²) and O(n log n))
- Learn patience sorting and binary search optimization
- Implement and trace both algorithms
- Apply LIS to real-world problems

---

## 3.1 Problem Definition

### 📚 **Longest Increasing Subsequence (LIS)**

**Input:** Sequence A = [a₁, a₂, ..., aₙ] of integers

**Output:** Length of longest subsequence where elements are in strictly increasing order

**Subsequence:** Select elements maintaining relative order (but not necessarily consecutive)

### 🎯 **Example**

```
Input:  A = [10, 9, 2, 5, 3, 7, 101, 18]

Subsequences:
  [10, 101]          - length 2
  [2, 5, 7, 101]     - length 4
  [2, 5, 7, 18]      - length 4
  [2, 3, 7, 101]     - length 4 ✓ LIS
  [2, 3, 7, 18]      - length 4 ✓ LIS

LIS length: 4
```

### 📊 **Visualization**

```mermaid
graph LR
    A[10] -.x.- B[9]
    B -.✓.- C[2]
    C -.✓.- D[5]
    D -.x.- E[3]
    D -.✓.- F[7]
    F -.✓.- G[101]
    F -.✓.- H[18]
    
    style C fill:#ccffcc
    style D fill:#ccffcc
    style F fill:#ccffcc
    style G fill:#ccffcc
```

---

## 3.2 Applications

### 🌍 **Real-World Uses**

1. **Box Stacking Problem**
   - Stack boxes with decreasing base dimensions
   - Maximize total height
   - Reduces to LIS on sorted boxes

2. **Patience Sorting** (Card Game)
   - Variant of solitaire
   - Directly uses LIS algorithm

3. **DNA Sequence Analysis**
   - Find longest common patterns
   - Evolutionary distance measurement

4. **File Versioning**
   - Longest sequence of compatible versions
   - Dependency resolution

5. **Stock Market Analysis**
   - Longest increasing price trend
   - Optimal buy/sell strategy

---

## 3.3 Dynamic Programming Solution: O(n²)

### 🔑 **Key Insight**

**Define:** `dp[i]` = length of LIS ending at position i

**Recurrence:**
```
dp[i] = max(dp[j] + 1) for all j < i where A[j] < A[i]
dp[i] = 1 (base case: single element)

Answer = max(dp[i]) for all i
```

### 📊 **Algorithm Steps**

```
LIS-DP(A, n):
  1. Initialize dp[0..n-1] = 1
  2. For i = 1 to n-1:
       For j = 0 to i-1:
         If A[j] < A[i]:
           dp[i] = max(dp[i], dp[j] + 1)
  3. Return max(dp[])
```

### 💻 **C Implementation**

```c
#include <stdio.h>
#include <stdlib.h>

#define MAX(a, b) ((a) > (b) ? (a) : (b))

// O(n²) Dynamic Programming Solution
int lengthOfLIS_DP(int* nums, int numsSize) {
    if (numsSize == 0) return 0;
    
    // dp[i] = length of LIS ending at index i
    int* dp = (int*)malloc(numsSize * sizeof(int));
    
    // Initialize: each element forms LIS of length 1
    for (int i = 0; i < numsSize; i++) {
        dp[i] = 1;
    }
    
    printf("=== LIS Dynamic Programming Solution ===\n");
    printf("Input: [");
    for (int i = 0; i < numsSize; i++) {
        printf("%d%s", nums[i], i < numsSize - 1 ? ", " : "");
    }
    printf("]\n\n");
    
    // Fill dp table
    for (int i = 1; i < numsSize; i++) {
        printf("Position %d (value=%d):\n", i, nums[i]);
        
        for (int j = 0; j < i; j++) {
            if (nums[j] < nums[i]) {
                int new_length = dp[j] + 1;
                if (new_length > dp[i]) {
                    printf("  Found longer LIS ending at %d: dp[%d] = %d + 1 = %d\n",
                           j, i, dp[j], new_length);
                    dp[i] = new_length;
                }
            }
        }
        
        printf("  Final: dp[%d] = %d\n\n", i, dp[i]);
    }
    
    // Print DP table
    printf("DP Table:\n");
    printf("Index:  ");
    for (int i = 0; i < numsSize; i++) printf("%2d ", i);
    printf("\n");
    
    printf("Value:  ");
    for (int i = 0; i < numsSize; i++) printf("%2d ", nums[i]);
    printf("\n");
    
    printf("dp[i]:  ");
    for (int i = 0; i < numsSize; i++) printf("%2d ", dp[i]);
    printf("\n\n");
    
    // Find maximum
    int maxLen = 1;
    for (int i = 0; i < numsSize; i++) {
        maxLen = MAX(maxLen, dp[i]);
    }
    
    free(dp);
    return maxLen;
}

// Reconstruct the actual LIS
void reconstruct_LIS(int* nums, int numsSize) {
    if (numsSize == 0) return;
    
    int* dp = (int*)malloc(numsSize * sizeof(int));
    int* parent = (int*)malloc(numsSize * sizeof(int));
    
    // Initialize
    for (int i = 0; i < numsSize; i++) {
        dp[i] = 1;
        parent[i] = -1;  // No parent initially
    }
    
    // Fill dp and parent arrays
    for (int i = 1; i < numsSize; i++) {
        for (int j = 0; j < i; j++) {
            if (nums[j] < nums[i] && dp[j] + 1 > dp[i]) {
                dp[i] = dp[j] + 1;
                parent[i] = j;  // Track where we came from
            }
        }
    }
    
    // Find position of maximum length
    int maxLen = 0, maxIdx = 0;
    for (int i = 0; i < numsSize; i++) {
        if (dp[i] > maxLen) {
            maxLen = dp[i];
            maxIdx = i;
        }
    }
    
    // Reconstruct LIS by following parent pointers
    int* lis = (int*)malloc(maxLen * sizeof(int));
    int idx = maxLen - 1;
    int current = maxIdx;
    
    while (current != -1) {
        lis[idx--] = nums[current];
        current = parent[current];
    }
    
    printf("One possible LIS: [");
    for (int i = 0; i < maxLen; i++) {
        printf("%d%s", lis[i], i < maxLen - 1 ? ", " : "");
    }
    printf("]\n");
    
    free(dp);
    free(parent);
    free(lis);
}

int main() {
    int nums[] = {10, 9, 2, 5, 3, 7, 101, 18};
    int n = sizeof(nums) / sizeof(nums[0]);
    
    int result = lengthOfLIS_DP(nums, n);
    printf("LIS Length: %d\n\n", result);
    
    reconstruct_LIS(nums, n);
    
    printf("\n--- Time Complexity ---\n");
    printf("Outer loop: O(n)\n");
    printf("Inner loop: O(n)\n");
    printf("Total: O(n²)\n");
    printf("Space: O(n) for dp array\n");
    
    return 0;
}
```

### 📊 **Trace Example**

**Input:** [10, 9, 2, 5, 3, 7, 101, 18]

| i | nums[i] | Check j | dp[i] | Reasoning |
|---|---------|---------|-------|-----------|
| 0 | 10 | - | 1 | Base case |
| 1 | 9 | 0: 10>9 ✗ | 1 | No smaller element |
| 2 | 2 | 0,1: all larger | 1 | No smaller element |
| 3 | 5 | 2: 2<5 ✓ | 2 | dp[2]+1 = 2 |
| 4 | 3 | 2: 2<3 ✓ | 2 | dp[2]+1 = 2 |
| 5 | 7 | 2,3,4: best is dp[3]=2 | 3 | dp[3]+1 = 3 |
| 6 | 101 | 2,3,4,5: best is dp[5]=3 | 4 | dp[5]+1 = 4 |
| 7 | 18 | 2,3,4,5: best is dp[5]=3 | 4 | dp[5]+1 = 4 |

**Final DP:** [1, 1, 1, 2, 2, 3, 4, 4]
**Answer:** max = 4

---

## 3.4 Optimized Solution: O(n log n)

### 🔑 **Key Insight: Patience Sorting**

**Idea:** Maintain multiple "piles" of increasing subsequences
- For each new element, place it on leftmost pile where it fits
- Use binary search to find the pile
- Number of piles = LIS length

### 📚 **Algorithm Intuition**

```
Patience Sorting:
  1. Maintain array tails[] where:
     tails[i] = smallest tail element of all LIS of length i+1
  
  2. For each element x:
     - Binary search for position in tails[]
     - Replace tails[pos] with x
     - If x is larger than all tails, append it
  
  3. Length of tails[] = LIS length
```

### 🎯 **Why This Works**

**Invariant:** tails[] is always sorted

**Property:** If we can extend an LIS of length k, we can extend any shorter LIS too

**Greedy Choice:** Always use smallest possible tail (leaves more room for future elements)

### 💻 **C Implementation**

```c
#include <stdio.h>
#include <stdlib.h>

// Binary search for leftmost position where nums[i] can be placed
int binarySearch(int* tails, int len, int target) {
    int left = 0, right = len - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (tails[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return left;  // Leftmost position >= target
}

// O(n log n) Binary Search Solution
int lengthOfLIS_Fast(int* nums, int numsSize) {
    if (numsSize == 0) return 0;
    
    // tails[i] = smallest tail of all LIS of length i+1
    int* tails = (int*)malloc(numsSize * sizeof(int));
    int len = 0;  // Current length of tails array
    
    printf("=== LIS Optimized Solution (Binary Search) ===\n");
    printf("Input: [");
    for (int i = 0; i < numsSize; i++) {
        printf("%d%s", nums[i], i < numsSize - 1 ? ", " : "");
    }
    printf("]\n\n");
    
    for (int i = 0; i < numsSize; i++) {
        int num = nums[i];
        
        printf("Processing nums[%d] = %d:\n", i, num);
        printf("  Current tails: [");
        for (int j = 0; j < len; j++) {
            printf("%d%s", tails[j], j < len - 1 ? ", " : "");
        }
        printf("]\n");
        
        // Binary search for position
        int pos = binarySearch(tails, len, num);
        
        printf("  Binary search result: position %d\n", pos);
        
        if (pos == len) {
            // Extend the longest LIS
            tails[len++] = num;
            printf("  Action: Append %d (new longest LIS of length %d)\n", num, len);
        } else {
            // Replace to keep smallest tail
            printf("  Action: Replace tails[%d] = %d with %d\n", pos, tails[pos], num);
            tails[pos] = num;
        }
        
        printf("  Updated tails: [");
        for (int j = 0; j < len; j++) {
            printf("%d%s", tails[j], j < len - 1 ? ", " : "");
        }
        printf("]\n\n");
    }
    
    printf("Final LIS length: %d\n", len);
    
    free(tails);
    return len;
}

// Demonstrate with multiple examples
void test_case(int* nums, int n, const char* description) {
    printf("\n==========================================\n");
    printf("Test Case: %s\n", description);
    printf("==========================================\n");
    
    int result = lengthOfLIS_Fast(nums, n);
    
    printf("\n--- Complexity Analysis ---\n");
    printf("For each element: Binary search in tails[] = O(log n)\n");
    printf("Total: n elements × O(log n) = O(n log n)\n");
    printf("Space: O(n) for tails array\n");
}

int main() {
    // Test case 1
    int nums1[] = {10, 9, 2, 5, 3, 7, 101, 18};
    int n1 = sizeof(nums1) / sizeof(nums1[0]);
    test_case(nums1, n1, "Mixed sequence");
    
    // Test case 2
    int nums2[] = {0, 1, 0, 3, 2, 3};
    int n2 = sizeof(nums2) / sizeof(nums2[0]);
    test_case(nums2, n2, "Multiple peaks");
    
    // Test case 3
    int nums3[] = {7, 7, 7, 7, 7};
    int n3 = sizeof(nums3) / sizeof(nums3[0]);
    test_case(nums3, n3, "All equal");
    
    return 0;
}
```

### 📊 **Detailed Trace**

**Input:** [10, 9, 2, 5, 3, 7, 101, 18]

| Step | Element | tails[] before | Binary Search | Action | tails[] after | Length |
|------|---------|----------------|---------------|--------|---------------|--------|
| 0 | 10 | [] | pos=0 | Append | [10] | 1 |
| 1 | 9 | [10] | pos=0 | Replace | [9] | 1 |
| 2 | 2 | [9] | pos=0 | Replace | [2] | 1 |
| 3 | 5 | [2] | pos=1 | Append | [2,5] | 2 |
| 4 | 3 | [2,5] | pos=1 | Replace | [2,3] | 2 |
| 5 | 7 | [2,3] | pos=2 | Append | [2,3,7] | 3 |
| 6 | 101 | [2,3,7] | pos=3 | Append | [2,3,7,101] | 4 |
| 7 | 18 | [2,3,7,101] | pos=3 | Replace | [2,3,7,18] | 4 |

**Final Answer:** 4

### 🔍 **Why tails[] is Sorted**

**Proof by Induction:**
- **Base:** tails[] starts empty (sorted ✓)
- **Inductive Step:** If tails[] is sorted before processing element x:
  - Binary search finds position pos
  - Either append (maintains sorted ✓)
  - Or replace tails[pos] with x where x < tails[pos] (still sorted ✓)

---

## 3.5 Comparison: O(n²) vs O(n log n)

### 📊 **Algorithm Comparison**

| Aspect | DP Solution | Binary Search Solution |
|--------|-------------|------------------------|
| **Time** | O(n²) | O(n log n) |
| **Space** | O(n) | O(n) |
| **Simplicity** | Easy to understand | Requires insight |
| **Reconstruction** | Easy with parent array | Requires modification |
| **Best for** | Small n, need actual LIS | Large n, only need length |

### 📈 **Performance**

```
Running times for different n:

n = 100:
  O(n²):      10,000 operations
  O(n log n): 664 operations     (15× faster)

n = 1,000:
  O(n²):      1,000,000 operations
  O(n log n): 9,966 operations   (100× faster)

n = 10,000:
  O(n²):      100,000,000 operations
  O(n log n): 132,877 operations (753× faster)
```

---

## 3.6 Variations and Extensions

### 🎯 **Variation 1: Longest Non-Decreasing Subsequence**

**Change:** Allow equal elements

**Modification in Binary Search:**
```c
// Change comparison in binary search
if (tails[mid] <= target) {  // Was: <
    left = mid + 1;
}
```

### 🎯 **Variation 2: Number of LIS**

**Problem:** Count how many LIS exist

**Approach:** Track count array alongside dp array

```c
int findNumberOfLIS(int* nums, int numsSize) {
    int* dp = malloc(numsSize * sizeof(int));
    int* count = malloc(numsSize * sizeof(int));
    
    for (int i = 0; i < numsSize; i++) {
        dp[i] = 1;
        count[i] = 1;  // One way to form LIS of length 1
        
        for (int j = 0; j < i; j++) {
            if (nums[j] < nums[i]) {
                if (dp[j] + 1 > dp[i]) {
                    dp[i] = dp[j] + 1;
                    count[i] = count[j];
                } else if (dp[j] + 1 == dp[i]) {
                    count[i] += count[j];  // Found another way
                }
            }
        }
    }
    
    int maxLen = 0, result = 0;
    for (int i = 0; i < numsSize; i++) {
        if (dp[i] > maxLen) {
            maxLen = dp[i];
            result = count[i];
        } else if (dp[i] == maxLen) {
            result += count[i];
        }
    }
    
    return result;
}
```

### 🎯 **Variation 3: Russian Doll Envelopes (2D LIS)**

**Problem:** Envelopes with (width, height), can nest if both dimensions smaller

**Approach:**
1. Sort by width (ascending), then height (descending)
2. Run LIS on heights

```c
int maxEnvelopes(int** envelopes, int envelopesSize) {
    // Sort: width ascending, height descending
    qsort(envelopes, envelopesSize, sizeof(int*), compare);
    
    // Extract heights
    int* heights = malloc(envelopesSize * sizeof(int));
    for (int i = 0; i < envelopesSize; i++) {
        heights[i] = envelopes[i][1];
    }
    
    // Run LIS on heights
    return lengthOfLIS_Fast(heights, envelopesSize);
}
```

---

## 3.7 Practice Problems

### 🧪 **Problem 1: Longest Bitonic Subsequence**

**Definition:** Bitonic = first increasing, then decreasing

**Approach:**
```
1. Compute lis[i] = LIS ending at i (left to right)
2. Compute lds[i] = LDS starting at i (right to left)
3. Answer = max(lis[i] + lds[i] - 1)
```

### 🧪 **Problem 2: Maximum Length Chain of Pairs**

**Input:** Pairs [(a,b), (c,d), ...] where can chain if b < c

**Approach:** Sort by second element, then LIS on first elements

### 🧪 **Problem 3: Box Stacking**

**Input:** Boxes with (l,w,h), can stack if base area decreasing

**Approach:** Generate rotations, sort, run LIS variant

---

## 📊 Summary

### 🎯 **Key Takeaways**

1. **Problem:** Find longest strictly increasing subsequence
2. **DP Solution:** O(n²) time, easy to implement and understand
3. **Optimized:** O(n log n) using binary search and greedy choice
4. **Patience Sorting:** Maintain smallest tails for each length
5. **Applications:** Box stacking, version control, sequence analysis

### 🔑 **Algorithm Selection Guide**

**Use O(n²) DP when:**
- n is small (< 1000)
- Need to reconstruct actual LIS easily
- Code simplicity is important

**Use O(n log n) Binary Search when:**
- n is large (> 1000)
- Only need LIS length
- Performance is critical

### 📋 **Implementation Checklist**

- [ ] Handle empty input
- [ ] Initialize dp/tails array correctly
- [ ] Binary search returns correct position
- [ ] Update tails array properly
- [ ] Track parent pointers if reconstructing
- [ ] Test edge cases (all equal, strictly decreasing, etc.)

---

## 📚 References

1. **Cormen, T. H., et al. (2009).** *Introduction to Algorithms* (3rd ed.). MIT Press.
   - Chapter 15: Dynamic Programming
   - Section 15.4: Longest Common Subsequence (related problem)

2. **Fredman, M. L. (1975).** "On computing the length of longest increasing subsequences." *Discrete Mathematics*.
   - Original O(n log n) algorithm

3. **Schensted, C. (1961).** "Longest increasing and decreasing subsequences." *Canadian Journal of Mathematics*.
   - Connection to Young tableaux

4. **Sedgewick, R., & Wayne, K. (2011).** *Algorithms* (4th ed.). Addison-Wesley.
   - Chapter 6: Context (sorting and searching applications)

5. **Aldous, D., & Diaconis, P. (1999).** "Longest increasing subsequences: from patience sorting to the Baik-Deift-Johansson theorem." *Bulletin of the American Mathematical Society*.
   - Deep mathematical connections

---

**Next Chapter:** [Network Flow Algorithms →](04_network_flow.md)
