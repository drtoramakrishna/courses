# Comprehensive Algorithms Study Guide

## Table of Contents
1. [Probability and Randomization](#probability-and-randomization)
2. [Sorting Algorithms](#sorting-algorithms)
3. [Computational Geometry](#computational-geometry)
4. [Graph Algorithms](#graph-algorithms)
5. [Greedy Algorithms](#greedy-algorithms)
6. [Dynamic Programming](#dynamic-programming)
7. [Advanced Data Structures](#advanced-data-structures)

---

## 1. Probability and Randomization

### 1.1 Contention Resolution

**Concept**: When multiple processes compete for shared resources, randomization helps avoid systematic conflicts.

**Example: Ethernet CSMA/CD Protocol**
- Multiple computers try to transmit on shared network
- If collision occurs, each waits random time before retry
- Probability of successful transmission increases with randomization

**Key Principle**: If n processes choose random time slots from 1 to n², probability of collision is low.

### 1.2 Expectation of Random Variables

**Definition**: For discrete random variable X:
```
E[X] = Σ x · P(X = x)
```

**Linearity of Expectation** (Most Important Property):
```
E[X + Y] = E[X] + E[Y]  (even if X, Y are dependent!)
```

**Example**: Rolling two dice
- E[sum] = E[die1] + E[die2] = 3.5 + 3.5 = 7

---

## 2. Sorting Algorithms

### 2.1 Selection Sort

**Algorithm**:
```
for i = 0 to n-1:
    min_idx = i
    for j = i+1 to n:
        if A[j] < A[min_idx]:
            min_idx = j
    swap(A[i], A[min_idx])
```

**Time Complexity**: O(n²) - always
**Space Complexity**: O(1)
**Stable**: No (can be made stable with modifications)

**Example**:
```
[64, 25, 12, 22, 11]
[11, 25, 12, 22, 64]  // 11 selected
[11, 12, 25, 22, 64]  // 12 selected
[11, 12, 22, 25, 64]  // 22 selected
[11, 12, 22, 25, 64]  // sorted
```

### 2.2 Quick Sort

**Core Idea**: Divide and conquer using partitioning

**Partition Algorithm** (Lomuto Scheme):
```
partition(A, low, high):
    pivot = A[high]
    i = low - 1
    for j = low to high-1:
        if A[j] <= pivot:
            i++
            swap(A[i], A[j])
    swap(A[i+1], A[high])
    return i+1
```

**QuickSort Algorithm**:
```
quicksort(A, low, high):
    if low < high:
        pi = partition(A, low, high)
        quicksort(A, low, pi-1)
        quicksort(A, pi+1, high)
```

**Time Complexity Analysis**:

**Best/Average Case**: O(n log n)
- Recurrence: T(n) = 2T(n/2) + O(n)
- Using Master Theorem: T(n) = O(n log n)

**Worst Case**: O(n²)
- Occurs when pivot is always smallest/largest
- Recurrence: T(n) = T(n-1) + O(n)

**Randomized QuickSort Expected Time Derivation**:

Let X_ij = indicator that elements i and j are compared

```
E[total comparisons] = E[Σ Σ X_ij]
                     = Σ Σ E[X_ij]  (linearity)
                     = Σ Σ P(i and j compared)
```

Two elements i and j are compared iff one is chosen as pivot before any element between them.

```
P(i, j compared) = 2/(j-i+1)
```

**Full Derivation**:
```
E[comparisons] = Σ(i=1 to n-1) Σ(j=i+1 to n) 2/(j-i+1)
               = Σ(i=1 to n-1) Σ(k=2 to n-i+1) 2/k
               ≤ 2n Σ(k=1 to n) 1/k
               = 2n H_n  (H_n is harmonic number)
               = O(n log n)
```

**Example**:
```
[3, 7, 8, 5, 2, 1, 9, 6, 4]
pivot = 4
[3, 2, 1, 4, 7, 8, 9, 6, 5]  // partition
      ↓
Recurse on [3,2,1] and [7,8,9,6,5]
```

### 2.3 Median Finding

**Randomized Select Algorithm** (QuickSelect):

```
randomizedSelect(A, low, high, k):
    if low == high:
        return A[low]
    
    pi = randomizedPartition(A, low, high)
    
    if k == pi:
        return A[pi]
    else if k < pi:
        return randomizedSelect(A, low, pi-1, k)
    else:
        return randomizedSelect(A, pi+1, high, k)
```

**Expected Time Complexity**: O(n)

**Derivation**:
```
T(n) ≤ T(n/2) + O(n)  (expected case with random pivot)
     = O(n) + O(n/2) + O(n/4) + ...
     = O(n(1 + 1/2 + 1/4 + ...))
     = O(2n) = O(n)
```

**Deterministic Linear Time Median (Median of Medians)**:

**Algorithm**:
1. Divide n elements into groups of 5
2. Find median of each group (O(n))
3. Recursively find median of medians (T(n/5))
4. Use this as pivot, partition (O(n))
5. Recurse on appropriate side (T(7n/10) worst case)

**Recurrence**:
```
T(n) = T(n/5) + T(7n/10) + O(n)
```

Since n/5 + 7n/10 = 9n/10 < n, this gives T(n) = O(n).

**Why groups of 5?**
- Groups of 3: T(n) = T(n/3) + T(2n/3) + O(n) = O(n log n)
- Groups of 5: T(n) = T(n/5) + T(7n/10) + O(n) = O(n) ✓
- Groups of 7: Also works but more overhead

### 2.4 Lower Bound of Comparison-Based Sorting

**Theorem**: Any comparison-based sorting algorithm requires Ω(n log n) comparisons in the worst case.

**Proof Using Decision Tree**:

- Decision tree has n! leaves (one for each permutation)
- Each comparison gives binary outcome (left/right)
- Tree height h satisfies: 2^h ≥ n!

Using Stirling's approximation:
```
n! ≈ (n/e)^n √(2πn)
log(n!) ≈ n log n - n log e + O(log n)
        = Ω(n log n)

Therefore: h ≥ log(n!) = Ω(n log n)
```

**Implications**:
- MergeSort, HeapSort are optimal: Θ(n log n)
- QuickSort expected: O(n log n)
- Can't do better with comparisons alone!

### 2.5 Counting Sort

**Non-comparison based sorting for integers in range [0, k]**

**Algorithm**:
```
countingSort(A, n, k):
    C = array of size k+1 initialized to 0
    B = output array of size n
    
    // Count occurrences
    for i = 0 to n-1:
        C[A[i]]++
    
    // Cumulative count
    for i = 1 to k:
        C[i] = C[i] + C[i-1]
    
    // Build output (reverse for stability)
    for i = n-1 down to 0:
        B[C[A[i]]-1] = A[i]
        C[A[i]]--
    
    return B
```

**Time Complexity**: O(n + k)
**Space Complexity**: O(n + k)
**Stable**: Yes

**Example**:
```
Input: [2, 5, 3, 0, 2, 3, 0, 3]
k = 5 (range 0-5)

Count array C after counting:
[2, 0, 2, 3, 0, 1]  // counts of 0,1,2,3,4,5

Cumulative array:
[2, 2, 4, 7, 7, 8]  // positions

Output: [0, 0, 2, 2, 3, 3, 3, 5]
```

**When to use**: When k = O(n), giving O(n) sorting!

### 2.6 Radix Sort

**Idea**: Sort numbers digit by digit using stable sort

**Algorithm (LSD - Least Significant Digit)**:
```
radixSort(A, n, d):  // d = number of digits
    for i = 1 to d:
        stableSort(A by digit i)  // use counting sort
```

**Time Complexity**: O(d(n + k))
- For base-b representation: d = log_b(max_value)
- Using counting sort: O(d(n + b))

**Example** (decimal numbers):
```
Input: [170, 45, 75, 90, 802, 24, 2, 66]

After sorting by ones place:
[170, 90, 802, 2, 24, 45, 75, 66]

After sorting by tens place:
[802, 2, 24, 45, 66, 170, 75, 90]

After sorting by hundreds place:
[2, 24, 45, 66, 75, 90, 170, 802]
```

**Optimization**: Choose base b to minimize d(n + b)
- Optimal when b ≈ n, giving O(n) for numbers ≤ n^c

### 2.7 Bucket Sort

**Idea**: Distribute elements into buckets, sort each bucket

**Algorithm**:
```
bucketSort(A, n):  // assumes A[i] ∈ [0, 1)
    B = array of n empty lists
    
    for i = 0 to n-1:
        insert A[i] into B[⌊n·A[i]⌋]
    
    for i = 0 to n-1:
        sort B[i] using insertion sort
    
    concatenate B[0], B[1], ..., B[n-1]
```

**Time Complexity**: 
- Average case: O(n) when input is uniformly distributed
- Worst case: O(n²) if all elements in one bucket

**Expected Time Analysis**:
```
E[time] = Θ(n) + Σ E[O(n_i²)]  // n_i = size of bucket i

If uniform distribution:
E[n_i²] = 2 - 1/n  (derived from probability)

Total: O(n + n·(2-1/n)) = O(n)
```

---

## 3. Computational Geometry

### 3.1 Convex Hull - Graham's Scan

**Definition**: Convex hull is the smallest convex polygon containing all points.

**Graham's Scan Algorithm**:

```
grahamScan(points):
    1. Find point P0 with lowest y-coordinate (break ties by x)
    2. Sort all points by polar angle with respect to P0
    3. Initialize stack S, push P0, P1, P2
    4. For each point Pi (i ≥ 3):
        While |S| > 1 and (nextToTop(S), top(S), Pi) makes non-left turn:
            pop(S)
        push(S, Pi)
    5. Return S
```

**Turn Detection** (Cross Product):
```
For points P1 = (x1, y1), P2 = (x2, y2), P3 = (x3, y3)

Cross product = (x2-x1)(y3-y1) - (y2-y1)(x3-x1)

> 0: Left turn (counterclockwise)
= 0: Collinear
< 0: Right turn (clockwise)
```

**Time Complexity**: O(n log n)
- Sorting: O(n log n)
- Scan: O(n)

**Example**:
```
Points: (0,0), (1,1), (2,2), (3,1), (4,2), (2,0)

1. P0 = (0,0) (lowest y)
2. Sort by angle: (0,0), (2,0), (1,1), (3,1), (2,2), (4,2)
3. Process:
   Stack: [(0,0), (2,0), (1,1)]
   Add (3,1): Right turn at (2,0), pop (1,1)
   Stack: [(0,0), (2,0), (3,1)]
   Add (2,2): Left turn, push
   Stack: [(0,0), (2,0), (3,1), (2,2)]
   ...
```

---

## 4. Graph Algorithms

### 4.1 Breadth-First Search (BFS)

**Algorithm**:
```
BFS(G, s):
    for each vertex u in G.V:
        u.color = WHITE
        u.d = ∞
        u.π = NIL
    
    s.color = GRAY
    s.d = 0
    s.π = NIL
    Q = empty queue
    ENQUEUE(Q, s)
    
    while Q not empty:
        u = DEQUEUE(Q)
        for each v in G.Adj[u]:
            if v.color == WHITE:
                v.color = GRAY
                v.d = u.d + 1
                v.π = u
                ENQUEUE(Q, v)
        u.color = BLACK
```

**Time Complexity**: O(V + E)
**Space Complexity**: O(V)

**Properties**:
- Finds shortest path in unweighted graphs
- Discovers vertices in order of distance from source
- Creates BFS tree

**Example**:
```
Graph:     1 --- 2
           |     |
           3 --- 4 --- 5

BFS from 1:
Level 0: [1]
Level 1: [2, 3]
Level 2: [4]
Level 3: [5]

Distances: d[1]=0, d[2]=1, d[3]=1, d[4]=2, d[5]=3
```

### 4.2 Dijkstra's Algorithm

**Single-Source Shortest Path for non-negative weights**

**Algorithm**:
```
Dijkstra(G, w, s):
    Initialize-Single-Source(G, s)
    S = ∅  // visited set
    Q = priority queue of all vertices (keyed by d)
    
    while Q not empty:
        u = EXTRACT-MIN(Q)
        S = S ∪ {u}
        for each v in G.Adj[u]:
            RELAX(u, v, w)

RELAX(u, v, w):
    if v.d > u.d + w(u,v):
        v.d = u.d + w(u,v)
        v.π = u
        DECREASE-KEY(Q, v)
```

**Time Complexity**:
- With binary heap: O((V + E) log V)
- With Fibonacci heap: O(E + V log V)
- With array: O(V²)

**Correctness Proof** (by induction):
- **Invariant**: For all u ∈ S, d[u] = δ(s, u) (optimal distance)
- **Base**: Initially S = ∅, trivially true
- **Inductive Step**: When extracting u from Q:
  - u has minimum d[u] among V - S
  - Any shorter path would require passing through V - S
  - But all such vertices have d ≥ d[u]
  - Therefore d[u] is optimal

**Example**:
```
Graph:  A --4-- B
        |       |
        2       1
        |       |
        C --5-- D

Source: A

Initial: d[A]=0, d[B]=∞, d[C]=∞, d[D]=∞

Step 1: Extract A
  Relax A→B: d[B]=4
  Relax A→C: d[C]=2

Step 2: Extract C (min=2)
  Relax C→D: d[D]=7

Step 3: Extract B (min=4)
  Relax B→D: d[D]=5 (improved!)

Step 4: Extract D
Done: d[A]=0, d[B]=4, d[C]=2, d[D]=5
```

**Why non-negative weights?**
Negative edges break the greedy choice:
```
A --5-- B
 \     /
  -10  0
   \  /
    C
    
d[A]=0, extract A, d[B]=5, d[C]=-10
But extract B before C → wrong!
```

### 4.3 Heaps and Priority Queues

**Binary Heap Properties**:
- Complete binary tree
- Max-heap: A[parent(i)] ≥ A[i]
- Min-heap: A[parent(i)] ≤ A[i]

**Operations**:
```
parent(i) = ⌊i/2⌋
left(i) = 2i
right(i) = 2i + 1

MAX-HEAPIFY(A, i):  // O(log n)
    l = left(i)
    r = right(i)
    largest = i
    if l ≤ heap-size and A[l] > A[i]:
        largest = l
    if r ≤ heap-size and A[r] > A[largest]:
        largest = r
    if largest ≠ i:
        swap A[i] and A[largest]
        MAX-HEAPIFY(A, largest)

INSERT(H, key):  // O(log n)
    H.size++
    H[H.size] = -∞
    INCREASE-KEY(H, H.size, key)

EXTRACT-MAX(H):  // O(log n)
    max = H[1]
    H[1] = H[H.size]
    H.size--
    MAX-HEAPIFY(H, 1)
    return max

BUILD-MAX-HEAP(A):  // O(n)
    for i = ⌊n/2⌋ down to 1:
        MAX-HEAPIFY(A, i)
```

**BUILD-HEAP Time Analysis**:
```
Cost = Σ(i=0 to log n) (n/2^(i+1)) · i
     = n Σ(i=0 to log n) i/2^(i+1)
     ≤ n Σ(i=0 to ∞) i/2^i
     = n · 2  (geometric series)
     = O(n)
```

---

## 5. Greedy Algorithms

### 5.1 Interval Scheduling (Unweighted)

**Problem**: Schedule maximum number of non-overlapping intervals.

**Greedy Choice**: Always select interval with earliest finish time.

**Algorithm**:
```
intervalScheduling(intervals):
    sort intervals by finish time
    A = ∅  // selected set
    lastFinish = -∞
    
    for each interval i:
        if i.start ≥ lastFinish:
            A = A ∪ {i}
            lastFinish = i.finish
    
    return A
```

**Time Complexity**: O(n log n)

**Proof of Correctness** (Exchange Argument):
- Let A = greedy solution, O = optimal solution
- If A ≠ O, let i₁ be first interval where they differ
- Let j₁ be the interval O chose instead
- Since greedy chose i₁, finish(i₁) ≤ finish(j₁)
- Replace j₁ with i₁ in O → still feasible
- Repeat → transform O into A without reducing size
- Therefore |A| = |O|

**Example**:
```
Intervals (start, finish):
(1,4), (3,5), (0,6), (5,7), (8,9), (5,9)

Sorted by finish:
(1,4), (3,5), (0,6), (5,7), (8,9), (5,9)

Greedy selection:
Select (1,4), finish=4
Skip (3,5), start=3 < 4
Skip (0,6), start=0 < 4
Select (5,7), finish=7
Select (8,9), finish=9

Result: 3 intervals
```

### 5.2 Weighted Interval Scheduling

**Dynamic Programming Solution** (Not greedy!):

**Problem**: Maximize total weight of non-overlapping intervals.

**Recurrence**:
```
p(j) = latest interval i < j that doesn't overlap with j

OPT(j) = max(weight[j] + OPT(p(j)), OPT(j-1))
         include j          exclude j
```

**Algorithm**:
```
weightedIntervalScheduling(intervals):
    sort intervals by finish time
    compute p(j) for all j
    M[0] = 0
    
    for j = 1 to n:
        M[j] = max(weight[j] + M[p(j)], M[j-1])
    
    return M[n]
```

**Time Complexity**: O(n log n)

**Example**:
```
Intervals (start, finish, weight):
(1,3,5), (2,5,6), (4,6,5), (6,7,4), (5,8,11), (7,9,2)

After sorting by finish time and computing p(j):
j=1: (1,3,5), p(1)=0
j=2: (2,5,6), p(2)=0
j=3: (4,6,5), p(3)=1
j=4: (6,7,4), p(4)=3
j=5: (5,8,11), p(5)=1
j=6: (7,9,2), p(6)=4

M[0]=0
M[1]=max(5+0, 0)=5
M[2]=max(6+0, 5)=6
M[3]=max(5+5, 6)=10
M[4]=max(4+10, 10)=14
M[5]=max(11+5, 14)=16
M[6]=max(2+14, 16)=16
```

### 5.3 Minimum Spanning Tree (MST)

**Kruskal's Algorithm**:

**Greedy Strategy**: Sort edges by weight, add edge if doesn't create cycle.

**Algorithm**:
```
Kruskal(G, w):
    A = ∅
    for each vertex v in G.V:
        MAKE-SET(v)
    
    sort edges by weight w
    
    for each edge (u,v) in sorted order:
        if FIND-SET(u) ≠ FIND-SET(v):
            A = A ∪ {(u,v)}
            UNION(u, v)
    
    return A
```

**Time Complexity**: O(E log E) or O(E log V)
- Sorting: O(E log E)
- Union-Find: O(E α(V)) ≈ O(E) (with path compression)

**Correctness** (Cut Property):
- For any cut (S, V-S), the minimum weight edge crossing the cut is in some MST
- Kruskal's algorithm always adds such edges

**Example**:
```
Graph:     A --1-- B
           | \     |
           6  5    2
           |   \   |
           D --4-- C

Edges sorted: (A,B,1), (B,C,2), (C,D,4), (A,C,5), (A,D,6)

Step 1: Add (A,B,1), components: {A,B}, {C}, {D}
Step 2: Add (B,C,2), components: {A,B,C}, {D}
Step 3: Add (C,D,4), components: {A,B,C,D}
Step 4: Skip (A,C,5), would create cycle
Step 5: Skip (A,D,6), would create cycle

MST edges: (A,B), (B,C), (C,D), total weight=7
```

### 5.4 Union-Find (Disjoint Set Union)

**Operations**:
- MAKE-SET(x): Create singleton set {x}
- UNION(x, y): Merge sets containing x and y
- FIND-SET(x): Return representative of set containing x

**Implementation with Path Compression and Union by Rank**:

```
MAKE-SET(x):
    x.parent = x
    x.rank = 0

FIND-SET(x):
    if x ≠ x.parent:
        x.parent = FIND-SET(x.parent)  // path compression
    return x.parent

UNION(x, y):
    xRoot = FIND-SET(x)
    yRoot = FIND-SET(y)
    
    if xRoot == yRoot:
        return
    
    // Union by rank
    if xRoot.rank < yRoot.rank:
        xRoot.parent = yRoot
    else if xRoot.rank > yRoot.rank:
        yRoot.parent = xRoot
    else:
        yRoot.parent = xRoot
        xRoot.rank++
```

**Time Complexity**: O(α(n)) per operation
- α(n) is inverse Ackermann function
- α(n) ≤ 4 for all practical n (n < 2^65536)
- Essentially constant time!

**Rank Property**: If x.rank = k, then |subtree(x)| ≥ 2^k

**Path Compression Effect**:
```
Before:  A → B → C → D → E (all rank 0)
After FIND-SET(A):  A → E
                    B → E
                    C → E
                    D → E
```

### 5.5 Prim's Algorithm

**Greedy Strategy**: Grow MST from arbitrary root, always add minimum weight edge connecting tree to non-tree vertex.

**Algorithm**:
```
Prim(G, w, r):
    for each u in G.V:
        u.key = ∞
        u.π = NIL
    
    r.key = 0
    Q = priority queue of all vertices
    
    while Q not empty:
        u = EXTRACT-MIN(Q)
        for each v in G.Adj[u]:
            if v in Q and w(u,v) < v.key:
                v.π = u
                v.key = w(u,v)
                DECREASE-KEY(Q, v)
```

**Time Complexity**:
- Binary heap: O((V + E) log V)
- Fibonacci heap: O(E + V log V)

**Comparison: Kruskal vs Prim**:
- **Kruskal**: Better for sparse graphs (E ≈ V)
- **Prim**: Better for dense graphs (E ≈ V²)
- Both correct, both O(E log V) with good implementation

---

## 6. Dynamic Programming

### 6.1 Principles of Dynamic Programming

**Key Characteristics**:
1. **Optimal Substructure**: Optimal solution contains optimal solutions to subproblems
2. **Overlapping Subproblems**: Same subproblems solved multiple times

**Approaches**:
- **Top-Down (Memoization)**: Recursion + caching
- **Bottom-Up (Tabulation)**: Iterative, fill table

**Steps to Design DP**:
1. Characterize structure of optimal solution
2. Define recurrence relation
3. Compute value bottom-up (or top-down with memo)
4. Construct optimal solution from computed info

### 6.2 Classic DP Examples

**Fibonacci**:
```
Recurrence: F(n) = F(n-1) + F(n-2)
Base cases: F(0) = 0, F(1) = 1

Bottom-up O(n):
fib(n):
    if n ≤ 1: return n
    prev2 = 0, prev1 = 1
    for i = 2 to n:
        curr = prev1 + prev2
        prev2 = prev1
        prev1 = curr
    return prev1
```

**0/1 Knapsack**:
```
Items: weight[i], value[i]
Capacity: W

Recurrence:
K(i, w) = max(K(i-1, w),                    // don't take item i
              value[i] + K(i-1, w-weight[i])) // take item i
          if weight[i] ≤ w

K(i, w) = K(i-1, w)  if weight[i] > w

Base: K(0, w) = 0, K(i, 0) = 0

Time: O(nW), Space: O(nW)
```

**Longest Common Subsequence**:
```
X = x₁x₂...xₘ, Y = y₁y₂...yₙ

LCS(i, j) = 0                           if i=0 or j=0
          = LCS(i-1, j-1) + 1           if xᵢ = yⱼ
          = max(LCS(i-1,j), LCS(i,j-1)) if xᵢ ≠ yⱼ

Time: O(mn), Space: O(mn)
```

---

## 7. Advanced Data Structures

### 7.1 Priority Queues

**Operations**:
- INSERT(key): O(log n)
- EXTRACT-MAX/MIN(): O(log n)
- INCREASE/DECREASE-KEY(x, k): O(log n)

**Applications**:
- Dijkstra's algorithm
- Prim's algorithm
- Huffman coding
- Event simulation

### 7.2 Comparison of Data Structures

| Operation | Array | Sorted Array | Binary Heap | BST (balanced) |
|-----------|-------|--------------|-------------|----------------|
| Search | O(n) | O(log n) | O(n) | O(log n) |
| Insert | O(1) | O(n) | O(log n) | O(log n) |
| Delete | O(n) | O(n) | O(log n) | O(log n) |
| Find-Min | O(n) | O(1) | O(1) | O(log n) |
| Extract-Min | O(n) | O(1) | O(log n) | O(log n) |

---

## 8. Key Theorems and Proofs

### 8.1 Master Theorem

**For recurrences of form**: T(n) = aT(n/b) + f(n)

**Case 1**: If f(n) = O(n^(log_b(a) - ε)) for ε > 0
- Then T(n) = Θ(n^(log_b(a)))

**Case 2**: If f(n) = Θ(n^(log_b(a)) · log^k(n)) for k ≥ 0
- Then T(n) = Θ(n^(log_b(a)) · log^(k+1)(n))

**Case 3**: If f(n) = Ω(n^(log_b(a) + ε)) for ε > 0, and af(n/b) ≤ cf(n) for c < 1
- Then T(n) = Θ(f(n))

**Examples**:

1. **Merge Sort**: T(n) = 2T(n/2) + Θ(n)
   - a=2, b=2, f(n)=n
   - log_b(a) = log₂(2) = 1
   - f(n) = Θ(n¹ · log⁰(n)) → Case 2
   - **T(n) = Θ(n log n)**

2. **Binary Search**: T(n) = T(n/2) + Θ(1)
   - a=1, b=2, f(n)=1
   - log_b(a) = log₂(1) = 0
   - f(n) = Θ(n⁰) → Case 2
   - **T(n) = Θ(log n)**

3. **Strassen's Matrix Mult**: T(n) = 7T(n/2) + Θ(n²)
   - a=7, b=2, f(n)=n²
   - log_b(a) = log₂(7) ≈ 2.81
   - f(n) = O(n^2.81-ε) → Case 1
   - **T(n) = Θ(n^2.81)**

### 8.2 Loop Invariant Method

**Structure**:
1. **Initialization**: Invariant is true before first iteration
2. **Maintenance**: If true before iteration, remains true after
3. **Termination**: When loop terminates, invariant gives useful property

**Example: Insertion Sort Correctness**

```
insertionSort(A):
    for j = 2 to n:
        key = A[j]
        i = j - 1
        while i > 0 and A[i] > key:
            A[i+1] = A[i]
            i = i - 1
        A[i+1] = key
```

**Loop Invariant**: At start of each iteration of outer for loop, subarray A[1..j-1] consists of elements originally in A[1..j-1] but in sorted order.

**Proof**:
- **Initialization**: j=2, A[1..1] is trivially sorted
- **Maintenance**: Inner loop inserts A[j] into correct position in A[1..j-1], making A[1..j] sorted
- **Termination**: j=n+1, so A[1..n] is sorted

---

## 9. Important Algorithm Design Paradigms

### 9.1 Divide and Conquer

**Template**:
```
divideAndConquer(problem):
    if problem is small enough:
        solve directly
    
    divide problem into subproblems
    for each subproblem:
        recursively solve
    
    combine solutions
```

**Classic Examples**:
- Merge Sort
- Quick Sort
- Binary Search
- Strassen's Matrix Multiplication
- Closest Pair of Points

**Recurrence Analysis**: Use Master Theorem or recursion tree

### 9.2 Greedy Algorithms

**Greedy Choice Property**: Locally optimal choices lead to globally optimal solution

**Proof Techniques**:
1. **Greedy stays ahead**: Show greedy is always at least as good as optimal
2. **Exchange argument**: Transform optimal into greedy without losing quality

**When Greedy Works**:
- Optimal substructure
- Greedy choice property
- No dependencies between choices (usually)

**Examples**:
- Activity Selection
- Huffman Coding
- Fractional Knapsack
- MST (Kruskal, Prim)
- Dijkstra's SSSP

**When Greedy Fails**:
- 0/1 Knapsack (need DP)
- Weighted Interval Scheduling (need DP)
- Longest Path (NP-hard)

### 9.3 Dynamic Programming vs Greedy

| Aspect | Greedy | Dynamic Programming |
|--------|--------|-------------------|
| Strategy | Make locally optimal choice | Try all possibilities |
| Subproblems | Disjoint | Overlapping |
| Complexity | Usually O(n log n) | Usually O(n²) or higher |
| Space | Usually O(1) or O(n) | Usually O(n) or O(n²) |
| Proof | Exchange argument | Optimal substructure |
| Examples | MST, Activity Selection | LCS, Knapsack, Edit Distance |

---

## 10. Problem-Solving Strategies

### 10.1 Identifying Algorithm Type

**Ask yourself**:

1. **Is there a natural ordering?**
   - → Try sorting first (O(n log n))

2. **Can I solve smaller instances?**
   - → Divide and Conquer or Recursion

3. **Do subproblems overlap?**
   - → Dynamic Programming

4. **Is there a greedy choice?**
   - → Try Greedy (prove correctness!)

5. **Is it a graph problem?**
   - Shortest path → Dijkstra, BFS, Bellman-Ford
   - Connectivity → DFS, BFS, Union-Find
   - MST → Kruskal, Prim

6. **Is there a constraint on comparison?**
   - → Consider non-comparison sorting (counting, radix, bucket)

7. **Do I need all solutions or just one?**
   - All → DP or backtracking
   - One → Greedy or DP

### 10.2 Complexity Analysis Checklist

**Time Complexity**:
- Count basic operations in loops
- Identify recurrence relation
- Apply Master Theorem or solve recurrence
- Consider best, average, worst cases

**Space Complexity**:
- Auxiliary space used
- Recursion stack depth
- Can we do it in-place?

**Common Patterns**:
- O(1): Hash table lookup, array access
- O(log n): Binary search, balanced tree operations
- O(n): Single pass through data
- O(n log n): Efficient sorting, divide and conquer
- O(n²): Nested loops, DP on 2D array
- O(2ⁿ): Subsets, some NP problems
- O(n!): Permutations

---

## 11. Exam Preparation Checklist

### 11.1 Core Concepts to Master

**Sorting**:
- [ ] Understand all O(n²) sorting algorithms
- [ ] Master Quick Sort analysis (expected case)
- [ ] Prove lower bound for comparison sorting
- [ ] Know when to use non-comparison sorts
- [ ] Understand stability and its importance

**Selection/Median Finding**:
- [ ] Randomized selection analysis
- [ ] Median of medians algorithm
- [ ] Why groups of 5?

**Graph Algorithms**:
- [ ] BFS and DFS thoroughly
- [ ] Dijkstra's correctness proof
- [ ] When to use which SSSP algorithm
- [ ] MST algorithms and Union-Find

**Greedy**:
- [ ] Interval scheduling proof
- [ ] MST proofs (cut property)
- [ ] When greedy works vs fails

**Dynamic Programming**:
- [ ] Identify optimal substructure
- [ ] Write recurrence relations
- [ ] Both top-down and bottom-up approaches
- [ ] Space optimization techniques

**Probability**:
- [ ] Expected value calculations
- [ ] Linearity of expectation
- [ ] Indicator random variables
- [ ] Randomized algorithm analysis

### 11.2 Practice Problems by Topic

**Sorting & Selection**:
1. Find kth largest element (Quick Select)
2. Sort array with limited range (Counting Sort)
3. Sort strings of equal length (Radix Sort)
4. Merge k sorted arrays
5. Find median of two sorted arrays

**Greedy**:
1. Interval scheduling (unweighted/weighted)
2. Minimize lateness
3. Huffman coding
4. Fractional knapsack
5. Job sequencing with deadlines

**Graph Algorithms**:
1. Find shortest path in unweighted graph
2. Detect cycle in directed/undirected graph
3. Find MST and its weight
4. Single-source shortest path with non-negative weights
5. Check if graph is bipartite

**Dynamic Programming**:
1. 0/1 Knapsack
2. Longest Common Subsequence
3. Edit Distance
4. Matrix Chain Multiplication
5. Weighted Interval Scheduling
6. Longest Increasing Subsequence

**Computational Geometry**:
1. Find convex hull of points
2. Check if point is inside polygon
3. Find closest pair of points
4. Line segment intersection

### 11.3 Common Mistakes to Avoid

1. **Off-by-one errors** in array indexing
2. **Forgetting base cases** in recursion
3. **Not considering edge cases** (empty input, single element)
4. **Confusing O, Ω, Θ notations**
5. **Assuming greedy works** without proof
6. **Integer overflow** in large computations
7. **Not initializing variables** properly
8. **Modifying loop variable** incorrectly
9. **Incorrect space complexity** (forgetting recursion stack)
10. **Mixing up BFS and DFS** applications

### 11.4 Proof Techniques Summary

**Correctness Proofs**:
1. **Loop Invariant** (Insertion Sort, etc.)
2. **Induction** (most recursive algorithms)
3. **Exchange Argument** (Greedy algorithms)
4. **Contradiction** (Lower bounds)
5. **Optimal Substructure** (DP)

**Complexity Proofs**:
1. **Summations** (nested loops)
2. **Recurrence Relations** (recursive algorithms)
3. **Master Theorem** (divide and conquer)
4. **Amortized Analysis** (dynamic arrays, Union-Find)
5. **Probabilistic Analysis** (randomized algorithms)

---

## 12. Formula Sheet

### Time Complexity Formulas

**Summations**:
```
Σ(i=1 to n) i = n(n+1)/2 = Θ(n²)

Σ(i=1 to n) i² = n(n+1)(2n+1)/6 = Θ(n³)

Σ(i=0 to n) 2ⁱ = 2^(n+1) - 1 = Θ(2ⁿ)

Σ(i=1 to n) 1/i = ln(n) + γ = Θ(log n)  (Harmonic series)
```

**Logarithm Properties**:
```
log(ab) = log(a) + log(b)
log(a/b) = log(a) - log(b)
log(aᵇ) = b·log(a)
log_a(b) = log(b)/log(a)
log₂(n!) = Θ(n log n)  (Stirling)
```

**Binomial Coefficient**:
```
C(n,k) = n!/(k!(n-k)!)

Σ(k=0 to n) C(n,k) = 2ⁿ
```

### Probability Formulas

**Expected Value**:
```
E[X] = Σ x·P(X=x)

E[aX + bY] = aE[X] + bE[Y]  (linearity)

E[X²] = Var(X) + (E[X])²
```

**Indicator Random Variables**:
```
I{A} = 1 if event A occurs, 0 otherwise

E[I{A}] = P(A)
```

### Graph Formulas

**Complete Graph**:
```
|E| = n(n-1)/2  (undirected)
|E| = n(n-1)    (directed)
```

**Tree Properties**:
```
|E| = |V| - 1  (connected tree)

Number of spanning trees (Complete graph) = n^(n-2)  (Cayley)
```

---

## 13. Quick Reference Tables

### Sorting Algorithm Comparison

| Algorithm | Best | Average | Worst | Space | Stable | In-place |
|-----------|------|---------|-------|-------|--------|----------|
| Bubble Sort | O(n) | O(n²) | O(n²) | O(1) | Yes | Yes |
| Selection Sort | O(n²) | O(n²) | O(n²) | O(1) | No | Yes |
| Insertion Sort | O(n) | O(n²) | O(n²) | O(1) | Yes | Yes |
| Merge Sort | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes | No |
| Quick Sort | O(n log n) | O(n log n) | O(n²) | O(log n) | No | Yes |
| Heap Sort | O(n log n) | O(n log n) | O(n log n) | O(1) | No | Yes |
| Counting Sort | O(n+k) | O(n+k) | O(n+k) | O(k) | Yes | No |
| Radix Sort | O(d(n+k)) | O(d(n+k)) | O(d(n+k)) | O(n+k) | Yes | No |
| Bucket Sort | O(n) | O(n) | O(n²) | O(n) | Yes | No |

### Graph Algorithm Comparison

| Algorithm | Problem | Time | Space | Edge Weights |
|-----------|---------|------|-------|--------------|
| BFS | SSSP (unweighted) | O(V+E) | O(V) | Unweighted |
| DFS | Traversal | O(V+E) | O(V) | Any |
| Dijkstra | SSSP | O((V+E)log V) | O(V) | Non-negative |
| Bellman-Ford | SSSP | O(VE) | O(V) | Any (detects -ve cycles) |
| Floyd-Warshall | APSP | O(V³) | O(V²) | Any |
| Kruskal | MST | O(E log E) | O(V) | Any |
| Prim | MST | O((V+E)log V) | O(V) | Any |

### Data Structure Operations

| Structure | Insert | Delete | Search | Find-Min | Extract-Min |
|-----------|--------|--------|--------|----------|-------------|
| Array (unsorted) | O(1) | O(n) | O(n) | O(n) | O(n) |
| Array (sorted) | O(n) | O(n) | O(log n) | O(1) | O(n) |
| Linked List | O(1) | O(1)* | O(n) | O(n) | O(n) |
| Binary Heap | O(log n) | O(log n) | O(n) | O(1) | O(log n) |
| BST (balanced) | O(log n) | O(log n) | O(log n) | O(log n) | O(log n) |
| Hash Table | O(1)† | O(1)† | O(1)† | O(n) | O(n) |

*Given pointer to node
†Average case with good hash function

---

## 14. Sample Exam Questions

### Question 1: Sorting Analysis
**Q**: Prove that any comparison-based sorting algorithm must make Ω(n log n) comparisons in the worst case.

**A**: 
- Use decision tree model
- Each internal node = comparison
- Each leaf = permutation (n! leaves)
- Height h satisfies: 2^h ≥ n!
- By Stirling: log(n!) = Θ(n log n)
- Therefore h = Ω(n log n)

### Question 2: Quick Sort Expected Time
**Q**: Derive the expected running time of randomized Quick Sort.

**A**:
- Let X_ij = indicator that elements i and j are compared
- E[total comparisons] = Σ Σ E[X_ij] = Σ Σ P(i,j compared)
- i and j compared iff one chosen as pivot before any between them
- P(i,j compared) = 2/(j-i+1)
- Summing: E[comparisons] = 2n·H_n = O(n log n)

### Question 3: Greedy Proof
**Q**: Prove that selecting intervals with earliest finish time gives optimal solution for unweighted interval scheduling.

**A**: Exchange argument
- Let A = greedy, O = optimal
- If A ≠ O, let i₁ be first difference
- Replace O's choice with i₁ (still feasible since earlier finish)
- Repeat to transform O → A
- |A| = |O|, so greedy is optimal

### Question 4: Dijkstra's Correctness
**Q**: Why does Dijkstra's algorithm fail with negative edge weights?

**A**:
- Dijkstra assumes once a vertex is processed, its distance is final
- Negative edges violate this: later paths can be shorter
- Example: A→B (weight 5), A→C (weight -10), C→B (weight 0)
- Dijkstra finds d[B]=5, but actual shortest is -10
- Need Bellman-Ford for negative weights

### Question 5: Median of Medians
**Q**: Why does the median of medians algorithm use groups of 5?

**A**:
- Need: n/5 + 7n/10 < n for linear time
- Groups of 3: n/3 + 2n/3 = n (doesn't work)
- Groups of 5: n/5 + 7n/10 = 9n/10 < n ✓
- Groups of 7: n/7 + 11n/14 ≈ 0.93n (works but more overhead)
- 5 is minimum that guarantees linear time with reasonable constants

---

## 15. Final Tips for Exam Success

### Before the Exam:
1. **Practice writing code by hand** (no IDE autocomplete!)
2. **Time yourself** on practice problems
3. **Review proofs**, don't just memorize
4. **Understand why** algorithms work
5. **Make your own cheat sheet** (process helps memory)

### During the Exam:
1. **Read all questions** first
2. **Start with what you know** well
3. **Show your work** for partial credit
4. **Check boundary conditions**
5. **Verify time/space complexity**
6. **Draw diagrams** to clarify thinking
7. **Leave time to review**

### Common Point Earners:
- Correct time complexity analysis
- Clear recurrence relations
- Well-explained proofs
- Proper algorithm pseudocode
- Worked examples
- Edge case handling

### Remember:
- **Clarity > Cleverness**
- **Correct > Fast** (for small examples)
- **Explain your reasoning**
- **Use standard notation**
- **Don't panic** if you get stuck—move on and come back

---

## Good Luck with Your Exam! 🎯

Remember: Understanding > Memorization. Focus on the "why" behind each algorithm, and the "how" will follow naturally.


# Comprehensive Algorithms Textbook
## Research-Level Study Material with Derivations and Practice Problems

---

# CHAPTER 1: Probability and Randomization in Algorithms

## 1.1 Introduction to Randomized Algorithms

### 1.1.1 Why Randomization?

Randomization is a powerful tool in algorithm design that can provide:
- **Simplicity**: Often easier to design and implement
- **Efficiency**: Can achieve better average-case performance
- **Avoiding worst-case**: Randomization helps avoid adversarial inputs
- **Contention resolution**: Useful in distributed systems

### 1.1.2 Types of Randomized Algorithms

**1. Las Vegas Algorithms**
- Always produce correct output
- Running time is a random variable
- Example: Randomized Quick Sort

**2. Monte Carlo Algorithms**
- Running time is deterministic
- Output may be incorrect with small probability
- Example: Miller-Rabin primality test

### 1.1.3 Contention Resolution

**Problem**: n processes compete for a shared resource. If multiple processes try simultaneously, all fail.

**Randomized Strategy**: Each process attempts with probability p.

**Analysis**:
```
P(exactly one succeeds) = n·p·(1-p)^(n-1)

To maximize, take derivative:
d/dp [n·p·(1-p)^(n-1)] = 0
n(1-p)^(n-1) - n·p·(n-1)(1-p)^(n-2) = 0
(1-p) = p(n-1)
p = 1/n

Maximum probability = n·(1/n)·(1-1/n)^(n-1)
                    ≈ (1-1/n)^(n-1)
                    ≈ 1/e ≈ 0.368
```

**Key Insight**: With optimal p = 1/n, constant probability of success per round!

**Practical Example: Ethernet CSMA/CD**
```
When collision detected:
1. Wait for random time in [0, 2^k - 1] slots
2. k increases with each collision (exponential backoff)
3. Randomization prevents repeated collisions
```

## 1.2 Probability Theory Foundations

### 1.2.1 Sample Space and Events

**Definitions**:
- **Sample space Ω**: Set of all possible outcomes
- **Event E**: Subset of Ω
- **Probability P(E)**: Measure satisfying:
  - 0 ≤ P(E) ≤ 1
  - P(Ω) = 1
  - P(E₁ ∪ E₂) = P(E₁) + P(E₂) if E₁ ∩ E₂ = ∅

### 1.2.2 Random Variables

**Definition**: A random variable X is a function X: Ω → ℝ

**Discrete Random Variable**:
```
P(X = x) = probability mass function (PMF)

Σ P(X = x) = 1  (over all possible x)
```

**Continuous Random Variable**:
```
f(x) = probability density function (PDF)

∫ f(x)dx = 1  (over all x)
```

### 1.2.3 Expected Value (Most Important!)

**Definition**:
```
E[X] = Σ x·P(X = x)  (discrete)
E[X] = ∫ x·f(x)dx    (continuous)
```

**Properties**:

**1. Linearity of Expectation** (CRITICAL):
```
E[aX + bY] = aE[X] + bE[Y]

This holds EVEN IF X and Y are dependent!
```

**2. Expected value of sum**:
```
E[Σ Xᵢ] = Σ E[Xᵢ]
```

**3. Product (if independent)**:
```
E[XY] = E[X]·E[Y]  (only if X, Y independent)
```

**Example 1: Dice Roll**
```
X = sum of two dice

Method 1 (direct):
E[X] = Σ k·P(X=k) for k=2 to 12
     = 2·(1/36) + 3·(2/36) + ... + 12·(1/36)
     = 7

Method 2 (linearity):
E[X] = E[die₁] + E[die₂]
     = 3.5 + 3.5 = 7  ✓
```

**Example 2: Coupon Collector Problem**

**Problem**: n types of coupons. Each purchase gives random coupon. Expected purchases to collect all?

**Solution**:
```
Let Xᵢ = purchases needed to get (i+1)th new coupon after having i

P(get new coupon) = (n-i)/n

E[Xᵢ] = n/(n-i)  (geometric distribution)

Total expected purchases:
E[X] = Σ E[Xᵢ] from i=0 to n-1
     = n·(1/n + 1/(n-1) + ... + 1/1)
     = n·Hₙ  (Hₙ = harmonic number)
     = n·ln(n) + O(n)
```

### 1.2.4 Indicator Random Variables

**Definition**:
```
I{A} = 1 if event A occurs
       0 otherwise

E[I{A}] = 1·P(A) + 0·P(Ā) = P(A)
```

**This is the most powerful technique for computing expectations!**

**Example: Expected number of inversions in random permutation**

**Problem**: Array A of n distinct elements. Inversion = pair (i,j) where i < j but A[i] > A[j].

**Solution**:
```
For each pair (i,j) with i < j, define:
Xᵢⱼ = I{A[i] > A[j]}

Total inversions X = Σ Σ Xᵢⱼ (i<j)

E[X] = E[Σ Σ Xᵢⱼ]
     = Σ Σ E[Xᵢⱼ]     (linearity!)
     = Σ Σ P(A[i] > A[j])
     = Σ Σ (1/2)       (random permutation)
     = C(n,2) · (1/2)
     = n(n-1)/4
```

### 1.2.5 Variance and Standard Deviation

**Variance**:
```
Var(X) = E[(X - E[X])²]
       = E[X²] - (E[X])²

σ = √Var(X)  (standard deviation)
```

**Properties**:
```
Var(aX + b) = a²·Var(X)

Var(X + Y) = Var(X) + Var(Y)  (if X, Y independent)
```

## 1.3 Probabilistic Analysis of Algorithms

### 1.3.1 Hiring Problem

**Scenario**: Interview n candidates in random order. Hire anyone better than all previous. Cost: interview = $c_i, hire = $c_h (c_h >> c_i).

**Analysis**:
```
Let X = number of hires
Xᵢ = I{candidate i is hired}

Candidate i hired iff best of first i candidates

E[Xᵢ] = P(i is best of first i) = 1/i

E[X] = Σ E[Xᵢ] from i=1 to n
     = Σ (1/i)
     = Hₙ = ln(n) + O(1)

Total cost = c_i·n + c_h·E[X]
           = c_i·n + c_h·ln(n)
```

### 1.3.2 Birthday Paradox

**Problem**: How many people needed so that probability of shared birthday > 1/2?

**Analysis**:
```
P(all different) = 1 · (364/365) · (363/365) · ... · ((365-n+1)/365)

For n = 23:
P(all different) ≈ 0.493
P(at least one match) ≈ 0.507 > 1/2

General formula:
P(collision) ≈ 1 - e^(-n²/2m)  (m = 365)

Setting = 1/2:
n ≈ √(2m·ln(2)) ≈ 1.177√m
```

**Application**: Hash table collisions, cryptographic attacks

---

## PRACTICE PROBLEMS - CHAPTER 1

### Problem 1.1: Load Balancing
**Q**: n jobs assigned randomly to m machines. What is the expected number of jobs on machine 1?

**Solution**:
```
Let Xᵢ = I{job i assigned to machine 1}
E[Xᵢ] = 1/m

Total X = Σ Xᵢ
E[X] = Σ E[Xᵢ] = n/m
```

### Problem 1.2: Maximum Load
**Q**: In above problem, show maximum load is O(log n / log log n) with high probability.

**Hint**: Use balls-and-bins analysis with Chernoff bounds.

### Problem 1.3: Randomized Min-Cut
**Q**: Karger's algorithm contracts random edges. Prove it finds min-cut with probability ≥ 1/C(n,2).

### Problem 1.4: Quick Select Analysis
**Q**: Prove expected number of comparisons in randomized selection is ≤ 4n.

### Problem 1.5: Hashing with Chaining
**Q**: n keys, m slots. Expected length of longest chain?

---

# CHAPTER 2: Sorting Algorithms - Comparison-Based

## 2.1 Introduction to Sorting

**Problem**: Given array A of n comparable elements, rearrange into non-decreasing order.

**Key Properties**:
- **Time Complexity**: Best, Average, Worst case
- **Space Complexity**: Auxiliary space used
- **Stability**: Preserve relative order of equal elements
- **In-place**: Uses O(1) extra space

## 2.2 Elementary Sorting Algorithms

### 2.2.1 Selection Sort

**Algorithm**:
```python
def selectionSort(A):
    n = len(A)
    for i in range(n):
        # Find minimum in A[i..n-1]
        min_idx = i
        for j in range(i+1, n):
            if A[j] < A[min_idx]:
                min_idx = j
        # Swap minimum to position i
        A[i], A[min_idx] = A[min_idx], A[i]
```

**Detailed Analysis**:
```
Comparisons:
- First pass: (n-1) comparisons
- Second pass: (n-2) comparisons
- ...
- Last pass: 1 comparison

Total = (n-1) + (n-2) + ... + 1
      = n(n-1)/2
      = Θ(n²)

Swaps: exactly (n-1) swaps (minimum possible!)

Space: O(1) auxiliary

Stability: No (can make stable with modification)
```

**When to use**: When writes are expensive (flash memory), small datasets

**Example Trace**:
```
Initial:  [64, 25, 12, 22, 11]
i=0: min=11, swap → [11, 25, 12, 22, 64]
i=1: min=12, swap → [11, 12, 25, 22, 64]
i=2: min=22, swap → [11, 12, 22, 25, 64]
i=3: no swap     → [11, 12, 22, 25, 64]
Done
```

### 2.2.2 Insertion Sort

**Algorithm**:
```python
def insertionSort(A):
    for i in range(1, len(A)):
        key = A[i]
        j = i - 1
        # Insert A[i] into sorted A[0..i-1]
        while j >= 0 and A[j] > key:
            A[j+1] = A[j]
            j -= 1
        A[j+1] = key
```

**Detailed Analysis**:

**Best Case**: Already sorted
```
Comparisons: n-1 (one per element)
Time: Θ(n)
```

**Worst Case**: Reverse sorted
```
Comparisons: 1 + 2 + ... + (n-1) = n(n-1)/2
Shifts: same as comparisons
Time: Θ(n²)
```

**Average Case**: Random permutation
```
Expected comparisons per element: i/2
Total = Σ(i/2) from i=1 to n-1
      = (1/2)·n(n-1)/2
      = Θ(n²)
```

**Loop Invariant Proof**:

**Invariant**: At start of iteration i, subarray A[0..i-1] contains elements originally in A[0..i-1] but in sorted order.

**Initialization**: i=1, A[0..0] is trivially sorted ✓

**Maintenance**: Inner loop inserts A[i] into correct position in A[0..i-1], making A[0..i] sorted ✓

**Termination**: i=n, A[0..n-1] is sorted ✓

**When to use**: 
- Small arrays (n < 10-50)
- Nearly sorted data
- Online algorithms (sorting as data arrives)
- Adaptive to existing order

**Binary Insertion Sort Optimization**:
```python
def binaryInsertionSort(A):
    for i in range(1, len(A)):
        key = A[i]
        # Binary search for insertion position
        pos = bisect_left(A, key, 0, i)
        # Shift elements
        A[pos+1:i+1] = A[pos:i]
        A[pos] = key

# Comparisons: O(n log n)
# Moves: still O(n²)
# Useful when comparisons are expensive
```

### 2.2.3 Bubble Sort

**Algorithm**:
```python
def bubbleSort(A):
    n = len(A)
    for i in range(n):
        swapped = False
        for j in range(n-1-i):
            if A[j] > A[j+1]:
                A[j], A[j+1] = A[j+1], A[j]
                swapped = True
        if not swapped:
            break  # Already sorted
```

**Analysis**:
```
Best Case (sorted): Θ(n) with optimization
Worst Case: Θ(n²)
Average Case: Θ(n²)

Comparisons = n(n-1)/2
Swaps (worst) = n(n-1)/2

Stable: Yes
In-place: Yes
```

**Rarely used in practice due to poor performance**

## 2.3 Quick Sort - The Workhorse

### 2.3.1 Basic Quick Sort

**Algorithm**:
```python
def quickSort(A, low, high):
    if low < high:
        pi = partition(A, low, high)
        quickSort(A, low, pi-1)
        quickSort(A, pi+1, high)

def partition(A, low, high):
    # Lomuto partition scheme
    pivot = A[high]
    i = low - 1  # Boundary of smaller elements
    
    for j in range(low, high):
        if A[j] <= pivot:
            i += 1
            A[i], A[j] = A[j], A[i]
    
    A[i+1], A[high] = A[high], A[i+1]
    return i + 1
```

**Partition Invariant**:
```
At each step:
A[low..i] ≤ pivot
A[i+1..j-1] > pivot
A[j..high-1] = unprocessed
A[high] = pivot
```

**Example of Partition**:
```
Array: [3, 7, 8, 5, 2, 1, 9, 6, 4]
Pivot = 4 (last element)

j=0: A[0]=3 ≤ 4, i++, swap A[0]↔A[0] → [3, 7, 8, 5, 2, 1, 9, 6, 4], i=0
j=1: A[1]=7 > 4, no swap              → [3, 7, 8, 5, 2, 1, 9, 6, 4], i=0
j=2: A[2]=8 > 4, no swap              → [3, 7, 8, 5, 2, 1, 9, 6, 4], i=0
j=3: A[3]=5 > 4, no swap              → [3, 7, 8, 5, 2, 1, 9, 6, 4], i=0
j=4: A[4]=2 ≤ 4, i++, swap A[1]↔A[4] → [3, 2, 8, 5, 7, 1, 9, 6, 4], i=1
j=5: A[5]=1 ≤ 4, i++, swap A[2]↔A[5] → [3, 2, 1, 5, 7, 8, 9, 6, 4], i=2
j=6: A[6]=9 > 4, no swap              → [3, 2, 1, 5, 7, 8, 9, 6, 4], i=2
j=7: A[7]=6 > 4, no swap              → [3, 2, 1, 5, 7, 8, 9, 6, 4], i=2

Final: swap A[3]↔A[8] → [3, 2, 1, 4, 7, 8, 9, 6, 5]
                           ≤4      4      >4

Return pi=3
```

### 2.3.2 Hoare Partition Scheme (Alternative)

**Algorithm**:
```python
def hoarePartition(A, low, high):
    pivot = A[low]
    i = low - 1
    j = high + 1
    
    while True:
        # Find element >= pivot from left
        i += 1
        while A[i] < pivot:
            i += 1
        
        # Find element <= pivot from right
        j -= 1
        while A[j] > pivot:
            j -= 1
        
        if i >= j:
            return j
        
        A[i], A[j] = A[j], A[i]
```

**Advantages of Hoare**:
- Fewer swaps on average (3 times fewer)
- More efficient on average
- Works better with equal elements

### 2.3.3 Time Complexity Analysis

**Worst Case: Θ(n²)**

Occurs when pivot is always smallest or largest element.

**Recurrence**:
```
T(n) = T(n-1) + T(0) + Θ(n)
     = T(n-1) + Θ(n)

Solving:
T(n) = Θ(n) + Θ(n-1) + ... + Θ(1)
     = Θ(n²)
```

**Example worst case**: Already sorted array
```
[1, 2, 3, 4, 5] with pivot = last element
Partition 1: [1,2,3,4] | 5     (n comparisons)
Partition 2: [1,2,3] | 4       (n-1 comparisons)
...
Total = n + (n-1) + ... + 1 = Θ(n²)
```

**Best Case: Θ(n log n)**

Occurs when pivot always divides array into two equal halves.

**Recurrence**:
```
T(n) = 2T(n/2) + Θ(n)

By Master Theorem (Case 2):
T(n) = Θ(n log n)
```

**Recursion Tree**:
```
                    n                    Level 0: n
                /       \
            n/2           n/2            Level 1: n
           /  \          /  \
         n/4  n/4      n/4  n/4          Level 2: n
         ...

Height = log n
Work per level = n
Total = n log n
```

### 2.3.4 Average Case Analysis (DETAILED DERIVATION)

**Assume**: All permutations equally likely.

Let T(n) = expected number of comparisons to sort n elements.

**Key Insight**: Partition uses (n-1) comparisons. After partition with pivot in position k, we have subarrays of size k-1 and n-k.

**Recurrence**:
```
T(n) = (n-1) + (1/n)·Σ[T(k-1) + T(n-k)] for k=1 to n

The (1/n) accounts for equal probability of each position.
```

**Simplification**:
```
T(n) = (n-1) + (1/n)·Σ[T(k-1) + T(n-k)]
     = (n-1) + (2/n)·Σ T(k-1)  from k=1 to n
     = (n-1) + (2/n)·Σ T(i)     from i=0 to n-1

Multiply by n:
n·T(n) = n(n-1) + 2·Σ T(i)   from i=0 to n-1   ...(1)

Similarly for n-1:
(n-1)·T(n-1) = (n-1)(n-2) + 2·Σ T(i)  from i=0 to n-2   ...(2)

Subtract (2) from (1):
n·T(n) - (n-1)·T(n-1) = n(n-1) - (n-1)(n-2) + 2T(n-1)
n·T(n) = (n-1)·T(n-1) + n(n-1) - (n-1)(n-2) + 2T(n-1)
n·T(n) = (n+1)·T(n-1) + 2(n-1)

Divide by n(n+1):
T(n)/(n+1) = T(n-1)/n + 2(n-1)/[n(n+1)]

Let S(n) = T(n)/(n+1):
S(n) = S(n-1) + 2(n-1)/[n(n+1)]

Telescoping sum:
S(n) = S(1) + Σ 2(k-1)/[k(k+1)]  from k=2 to n

S(1) = T(1)/2 = 0

Simplify the sum using partial fractions:
2(k-1)/[k(k+1)] = 2/k - 2/(k+1) + 2/[k(k+1)]
                ≈ 2/k - 2/(k+1)  (dominant terms)

S(n) ≈ 2[1/2 - 1/3 + 1/3 - 1/4 + ... + 1/n - 1/(n+1)]
     = 2[1/2 - 1/(n+1)]
     < 2

Actually, more careful analysis:
S(n) = 2(Hₙ₊₁ - 3/2)  where Hₙ is harmonic number

Therefore:
T(n) = (n+1)·S(n)
     = 2(n+1)(Hₙ₊₁ - 3/2)
     ≈ 2(n+1)ln(n)
     = 1.39n log₂ n  (in base-2 comparisons)
```

**Conclusion**: Average case is Θ(n log n), only ~39% more comparisons than optimal merge sort!

### 2.3.5 Randomized Quick Sort

**Algorithm**:
```python
def randomizedQuickSort(A, low, high):
    if low < high:
        pi = randomizedPartition(A, low, high)
        randomizedQuickSort(A, low, pi-1)
        randomizedQuickSort(A, pi+1, high)

def randomizedPartition(A, low, high):
    # Choose random pivot
    i = random.randint(low, high)
    A[i], A[high] = A[high], A[i]
    return partition(A, low, high)
```

**Expected Time: O(n log n)**

**Detailed Proof using Indicator Random Variables**:

Let X = total number of comparisons.

For elements zᵢ (ith smallest), zⱼ (jth smallest) where i < j:
```
Xᵢⱼ = I{zᵢ compared with zⱼ}

X = Σ Σ Xᵢⱼ  (i=1 to n-1, j=i+1 to n)

E[X] = Σ Σ E[Xᵢⱼ]
     = Σ Σ P(zᵢ compared with zⱼ)
```

**Key Question**: When are zᵢ and zⱼ compared?

**Answer**: They are compared if and only if one of them is chosen as pivot before any element between them (zᵢ₊₁, ..., zⱼ₋₁).

**Probability Calculation**:
```
Consider set S = {zᵢ, zᵢ₊₁, ..., zⱼ}
|S| = j - i + 1

zᵢ and zⱼ are compared iff one is chosen as pivot first from S.

P(zᵢ or zⱼ chosen first) = 2/(j-i+1)

Therefore:
E[Xᵢⱼ] = 2/(j-i+1)
```

**Complete Summation**:
```
E[X] = Σ(i=1 to n-1) Σ(j=i+1 to n) 2/(j-i+1)

Substitute k = j - i:
     = Σ(i=1 to n-1) Σ(k=1 to n-i) 2/(k+1)
     = Σ(i=1 to n-1) 2·[Σ(k=1 to n-i) 1/(k+1)]
     
Upper bound by extending sum:
     < Σ(i=1 to n-1) 2·[Σ(k=2 to n) 1/k]
     = (n-1)·2·(Hₙ - 1)
     ≈ 2n ln(n)
     = O(n log n)

More precise:
E[X] = Σ(i=1 to n-1) Σ(k=1 to n-i) 2/(k+1)

Change order of summation:
     = Σ(k=1 to n-1) Σ(i=1 to n-k) 2/(k+1)
     = Σ(k=1 to n-1) (n-k)·2/(k+1)
     = 2Σ(k=1 to n-1) (n-k)/(k+1)
     = 2Σ(m=2 to n) (n-m+1)/m  (substitute m=k+1)
     = 2Σ(m=2 to n) [n/m - 1 + 1/m]
     = 2n·Hₙ - 2(n-1) + 2(Hₙ-1)
     = 2(n+1)Hₙ - 2n - 2
     ≈ 2(n+1)ln(n) - 2n
     = 1.39n lg n - 1.38n  (in base-2)
```

**This is the same as deterministic average case!** Randomization helps avoid worst case on adversarial input.

### 2.3.6 Three-Way Partitioning (Dutch National Flag)

**Problem**: Many duplicate keys cause poor performance.

**Solution**: Partition into three parts: < pivot, = pivot, > pivot

**Algorithm**:
```python
def threeWayPartition(A, low, high):
    if high <= low:
        return
    
    lt = low          # A[low..lt-1] < pivot
    i = low + 1       # A[lt..i-1] = pivot
    gt = high         # A[gt+1..high] > pivot
    pivot = A[low]
    
    while i <= gt:
        if A[i] < pivot:
            A[lt], A[i] = A[i], A[lt]
            lt += 1
            i += 1
        elif A[i] > pivot:
            A[i], A[gt] = A[gt], A[i]
            gt -= 1
        else:  # A[i] == pivot
            i += 1
    
    return lt, gt

def threeWayQuickSort(A, low, high):
    if high <= low:
        return
    
    lt, gt = threeWayPartition(A, low, high)
    threeWayQuickSort(A, low, lt-1)
    threeWayQuickSort(A, gt+1, high)
```

**Time Complexity**: O(n) when all elements equal!

**Example**:
```
Array: [4, 9, 4, 4, 1, 9, 4, 4, 9, 4, 4, 1, 4]
Pivot: 4

After partition:
[1, 1, 4, 4, 4, 4, 4, 4, 4, 4, 9, 9, 9]
        ↑lt          gt↑

Only need to sort [1,1] and [9,9,9]
```

---

## PRACTICE PROBLEMS - CHAPTER 2A

### Problem 2.1: Insertion Sort Variants
**Q**: Prove that insertion sort makes at most n-1 swaps if there is exactly one inversion.

### Problem 2.2: Quick Sort Pivot Selection
**Q**: Show that selecting median-of-three (first, middle, last) as pivot guarantees O(n log n) on sorted array.

### Problem 2.3: K-way Partition
**Q**: Extend three-way partition to partition array into k groups around k-1 pivots. Analyze complexity.

### Problem 2.4: Quick Sort Depth
**Q**: What is the expected depth of recursion tree in randomized Quick Sort?

**Hint**: Use fact that expected height of random binary search tree is O(log n).

### Problem 2.5: Comparisons Lower Bound
**Q**: Prove that finding both minimum and maximum requires at least ⌈3n/2⌉ - 2 comparisons.

**Solution Sketch**: 
- Process elements in pairs
- Compare within pair (n/2 comparisons)
- Compare larger with max (n/2 - 1 comparisons)
- Compare smaller with min (n/2 - 1 comparisons)
- Total: n/2 + 2(n/2 - 1) = 3n/2 - 2

---

## 2.4 Merge Sort - Divide and Conquer

### 2.4.1 Basic Merge Sort

**Algorithm**:
```python
def mergeSort(A, left, right):
    if left < right:
        mid = (left + right) // 2
        mergeSort(A, left, mid)
        mergeSort(A, mid+1, right)
        merge(A, left, mid, right)

def merge(A, left, mid, right):
    # Create temporary arrays
    L = A[left:mid+1]
    R = A[mid+1:right+1]
    
    i = j = 0
    k = left
    
    # Merge L and R back into A[left..right]
    while i < len(L) and j < len(R):
        if L[i] <= R[j]:
            A[k] = L[i]
            i += 1
        else:
            A[k] = R[j]
            j += 1
        k += 1
    
    # Copy remaining elements
    while i < len(L):
        A[k] = L[i]
        i += 1
        k += 1
    
    while j < len(R):
        A[k] = R[j]
        j += 1
        k += 1
```

**Detailed Example**:
```
Array: [38, 27, 43, 3, 9, 82, 10]

Split Phase:
[38, 27, 43, 3, 9, 82, 10]
    ↓
[38, 27, 43, 3] [9, 82, 10]
    ↓               ↓
[38, 27] [43, 3]   [9, 82] [10]
  ↓        ↓         ↓       ↓
[38][27] [43][3]   [9][82] [10]

Merge Phase:
[27, 38] [3, 43]   [9, 82] [10]
    ↓               ↓
[3, 27, 38, 43]   [9, 10, 82]
         ↓
[3, 9, 10, 27, 38, 43, 82]
```

### 2.4.2 Time Complexity Analysis

**Recurrence Relation**:
```
T(n) = 2T(n/2) + Θ(n)
       ↑         ↑
    Two recursive calls
    on n/2 elements   Merge takes Θ(n)

Base case: T(1) = Θ(1)
```

**Method 1: Recursion Tree**
```
                        cn                     Level 0: cn
                    /        \
                cn/2          cn/2              Level 1: cn
               /    \        /    \
            cn/4   cn/4   cn/4   cn/4           Level 2: cn
            ...
```

```
Height of tree = lg n
Work per level = cn
Total work = cn · lg n = Θ(n log n)
```

**Method 2: Master Theorem**
```
T(n) = 2T(n/2) + Θ(n)

a = 2, b = 2, f(n) = Θ(n)
n^(log_b a) = n^(log_2 2) = n^1 = n

f(n) = Θ(n^1 · log^0 n)

This is Case 2 with k=0:
T(n) = Θ(n^1 · log^(0+1) n) = Θ(n log n)
```

**Method 3: Substitution**
```
Guess: T(n) = cn log n

Inductive hypothesis: T(k) ≤ ck log k for all k < n

T(n) = 2T(n/2) + cn
     ≤ 2 · c(n/2) log(n/2) + cn
     = cn log(n/2) + cn
     = cn(log n - log 2) + cn
     = cn log n - cn + cn
     = cn log n ✓
```

**All Cases**: Best = Average = Worst = Θ(n log n)

### 2.4.3 Space Complexity

**Naive implementation**: O(n) auxiliary space for temporary arrays

**Optimization attempts**:
1. **In-place merge**: Possible but O(n²) time
2. **Using insertions**: Not practical
3. **Linked list**: Natural in-place, but loses cache locality

**Stack space for recursion**: O(log n) due to tree height

### 2.4.4 Merge Sort vs Quick Sort

| Feature | Merge Sort | Quick Sort |
|---------|------------|------------|
| Worst case | Θ(n log n) | Θ(n²) |
| Average case | Θ(n log n) | Θ(n log n) |
| Space | O(n) | O(log n) |
| Stable | Yes | No |
| Cache performance | Poor | Good |
| Practical performance | Good | Excellent |
| Parallelization | Easy | Hard |

**When to use Merge Sort**:
- Stability required
- Worst-case guarantee needed
- External sorting (data doesn't fit in memory)
- Linked lists
- Parallel sorting

**When to use Quick Sort**:
- Average case expected
- In-place sorting needed
- Good cache performance important
- Arrays (random access)

### 2.4.5 Bottom-Up Merge Sort

**Algorithm** (Iterative):
```python
def bottomUpMergeSort(A):
    n = len(A)
    size = 1  # Current subarray size
    
    while size < n:
        # Pick starting index of left subarray
        left = 0
        while left < n:
            mid = min(left + size - 1, n - 1)
            right = min(left + 2*size - 1, n - 1)
            
            if mid < right:
                merge(A, left, mid, right)
            
            left += 2 * size
        
        size *= 2
```

**Advantages**:
- No recursion overhead
- Better for small datasets
- Easier to analyze cache behavior

**Example**:
```
Array: [38, 27, 43, 3, 9, 82, 10]

Size = 1:
Merge [38][27] → [27, 38]
Merge [43][3]  → [3, 43]
Merge [9][82]  → [9, 82]
[10] alone
Result: [27, 38, 3, 43, 9, 82, 10]

Size = 2:
Merge [27,38][3,43] → [3, 27, 38, 43]
Merge [9,82][10]    → [9, 10, 82]
Result: [3, 27, 38, 43, 9, 10, 82]

Size = 4:
Merge [3,27,38,43][9,10,82] → [3,9,10,27,38,43,82]
```

### 2.4.6 Natural Merge Sort

**Idea**: Exploit existing runs (sorted subsequences) in data

**Algorithm**:
```python
def naturalMergeSort(A):
    while True:
        runs = findRuns(A)  # Find sorted subsequences
        if len(runs) == 1:
            break  # Array is sorted
        
        # Merge adjacent runs
        mergeRuns(A, runs)

def findRuns(A):
    runs = []
    start = 0
    
    for i in range(1, len(A)):
        if A[i] < A[i-1]:  # Run ends
            runs.append((start, i-1))
            start = i
    
    runs.append((start, len(A)-1))
    return runs
```

**Performance**: O(n log k) where k = number of runs
- Already sorted: O(n)
- Random: O(n log n)

### 2.4.7 Counting Inversions

**Problem**: Count pairs (i,j) where i < j but A[i] > A[j]

**Application**: Measuring "sortedness", collaborative filtering

**Algorithm** (Modified Merge Sort):
```python
def countInversions(A):
    if len(A) <= 1:
        return A, 0
    
    mid = len(A) // 2
    left, left_inv = countInversions(A[:mid])
    right, right_inv = countInversions(A[mid:])
    merged, split_inv = mergeAndCount(left, right)
    
    return merged, left_inv + right_inv + split_inv

def mergeAndCount(L, R):
    i = j = 0
    inversions = 0
    result = []
    
    while i < len(L) and j < len(R):
        if L[i] <= R[j]:
            result.append(L[i])
            i += 1
        else:
            result.append(R[j])
            inversions += len(L) - i  # All remaining in L are > R[j]
            j += 1
    
    result.extend(L[i:])
    result.extend(R[j:])
    
    return result, inversions
```

**Example**:
```
Array: [2, 4, 1, 3, 5]

Split: [2, 4] and [1, 3, 5]

Left [2, 4]: 0 inversions (sorted)
Right [1, 3, 5]: 0 inversions (sorted)

Merge:
- Compare 2 and 1: 1 > 2, add 1, inversions += 2 (both 2,4 > 1)
- Compare 2 and 3: 2 < 3, add 2
- Compare 4 and 3: 3 < 4, add 3, inversions += 1 (4 > 3)
- Add remaining: 4, 5

Total inversions: 3
Pairs: (2,1), (4,1), (4,3)
```

**Time Complexity**: O(n log n)

---

## 2.5 Lower Bound for Comparison-Based Sorting

### 2.5.1 Decision Tree Model

**Key Idea**: Any comparison-based algorithm can be represented as a binary decision tree.

**Decision Tree Properties**:
- **Internal nodes**: Comparisons (e.g., A[i] ≤ A[j]?)
- **Leaves**: Permutations (sorted order)
- **Path from root to leaf**: Sequence of comparisons leading to sorted array
- **Height h**: Worst-case number of comparisons

**Example** (n=3 elements):
```
                     A[0]:A[1]?
                    /          \
                 ≤               >
                /                  \
           A[1]:A[2]?          A[0]:A[2]?
          /        \           /        \
        ≤           >        ≤           >
       /            \       /            \
   (0,1,2)      A[0]:A[2]? A[1]:A[2]? (2,0,1)
                /      \    /      \
              ≤         > ≤         >
             /          |  |         \
        (0,2,1)    (2,1,0)(1,2,0) (1,0,2)
```

### 2.5.2 Lower Bound Proof

**Theorem**: Any comparison-based sorting algorithm requires Ω(n log n) comparisons in the worst case.

**Proof**:

**Step 1**: Number of leaves must be at least n!
- Each permutation must appear as a leaf
- n! possible permutations of n elements

**Step 2**: Binary tree with L leaves has height h ≥ log₂ L
- Binary tree with height h has at most 2^h leaves
- Therefore: 2^h ≥ L
- Taking log: h ≥ log₂ L

**Step 3**: Apply to decision tree
```
h ≥ log₂(n!)
```

**Step 4**: Use Stirling's approximation
```
n! ≈ √(2πn) · (n/e)^n

log₂(n!) = log₂[√(2πn) · (n/e)^n]
         = log₂√(2πn) + log₂(n/e)^n
         = (1/2)log₂(2πn) + n log₂(n/e)
         = (1/2)log₂(2πn) + n log₂ n - n log₂ e
         = (1/2)log₂(2πn) + n log₂ n - n/ln 2
```

**Step 5**: Simplify
```
log₂(n!) = n log₂ n - n/ln 2 + (1/2)log₂(2πn)
         = n log₂ n - 1.443n + O(log n)
         = Θ(n log n)
```

**Alternative (simpler) proof**:
```
n! = n · (n-1) · (n-2) · ... · 2 · 1
   > n · (n-1) · ... · (n/2)     (drop half the terms)
   > (n/2)^(n/2)                 (each term ≥ n/2)

log(n!) > log[(n/2)^(n/2)]
        = (n/2) log(n/2)
        = (n/2)(log n - 1)
        = Θ(n log n)
```

**Conclusion**: 
- h = Ω(n log n)
- Any comparison-based sorting algorithm must make Ω(n log n) comparisons in worst case
- Merge sort, heap sort achieve this bound (optimal!)

### 2.5.3 Implications

**Optimality**:
- Merge Sort: Θ(n log n) worst case → **Optimal**
- Heap Sort: Θ(n log n) worst case → **Optimal**
- Quick Sort: Θ(n²) worst case → Not optimal, but Θ(n log n) average

**Cannot do better** with comparisons alone!

**To beat O(n log n)**:
- Must use properties beyond comparisons
- Examples: Counting sort, Radix sort, Bucket sort
- These assume additional structure (integers, keys in range, distribution)

---

## 2.6 Heap Sort

### 2.6.1 Binary Heap Review

**Max-Heap Property**: A[parent(i)] ≥ A[i] for all nodes i

**Array Representation**:
```
parent(i) = ⌊i/2⌋
left(i) = 2i
right(i) = 2i + 1
```

**Example Heap**:
```
Array: [16, 14, 10, 8, 7, 9, 3, 2, 4, 1]

Tree representation:
           16
         /    \
       14      10
      /  \    /  \
     8    7  9    3
    / \  /
   2  4 1
```

### 2.6.2 Heap Operations

**MAX-HEAPIFY** - Maintain heap property:
```python
def maxHeapify(A, i, heap_size):
    l = 2 * i + 1  # left child
    r = 2 * i + 2  # right child
    largest = i
    
    if l < heap_size and A[l] > A[largest]:
        largest = l
    if r < heap_size and A[r] > A[largest]:
        largest = r
    
    if largest != i:
        A[i], A[largest] = A[largest], A[i]
        maxHeapify(A, largest, heap_size)
```

**Time**: O(h) = O(log n) where h is height of node

**Example**:
```
Array: [16, 4, 10, 14, 7, 9, 3, 2, 8, 1]
maxHeapify(A, 1):  # Fix node at index 1 (value 4)

           16
         /    \
      → 4      10
       / \    /  \
     14   7  9    3
    / \  /
   2  8 1

Step 1: Compare 4 with children 14 and 7
        Largest = 14 (index 3)
        Swap: [16, 14, 10, 4, 7, 9, 3, 2, 8, 1]

           16
         /    \
       14      10
       / \    /  \
    → 4   7  9    3
     / \  /
    2  8 1

Step 2: Compare 4 with children 2 and 8
        Largest = 8 (index 8)
        Swap: [16, 14, 10, 8, 7, 9, 3, 2, 4, 1]

           16
         /    \
       14      10
       / \    /  \
      8   7  9    3
     / \  /
    2  4 1

Done: Heap property restored
```

**BUILD-MAX-HEAP**:
```python
def buildMaxHeap(A):
    n = len(A)
    # Start from last non-leaf node
    for i in range(n//2 - 1, -1, -1):
        maxHeapify(A, i, n)
```

**Time Complexity Analysis** (Tight bound):

**Intuition**: Not all nodes have height log n

```
Number of nodes at height h: ≤ ⌈n/2^(h+1)⌉

Cost of heapifying node at height h: O(h)

Total cost:
T(n) = Σ (nodes at height h) · O(h)
     = Σ ⌈n/2^(h+1)⌉ · O(h)  for h=0 to ⌊lg n⌋

Upper bound:
T(n) ≤ Σ (n/2^(h+1)) · c·h  for h=0 to ∞
     = cn · Σ h/2^(h+1)
     = cn · Σ h/2^h · (1/2)
     = (cn/2) · Σ h/2^h

We need: Σ h/2^h = ?
```

**Lemma**: Σ(h=0 to ∞) h·x^h = x/(1-x)² for |x| < 1

**Proof**:
```
Start with: Σ x^h = 1/(1-x)  for |x| < 1

Differentiate both sides:
Σ h·x^(h-1) = 1/(1-x)²

Multiply by x:
Σ h·x^h = x/(1-x)²
```

**Apply with x = 1/2**:
```
Σ h/2^h = (1/2)/(1-1/2)² = (1/2)/(1/4) = 2

Therefore:
T(n) ≤ (cn/2) · 2 = cn = O(n)
```

**BUILD-MAX-HEAP is O(n)!** (Not O(n log n) as naive analysis suggests)

### 2.6.3 Heap Sort Algorithm

**Algorithm**:
```python
def heapSort(A):
    # Build max heap: O(n)
    buildMaxHeap(A)
    
    heap_size = len(A)
    # Extract max n-1 times: O(n log n)
    for i in range(len(A)-1, 0, -1):
        # Swap max to end
        A[0], A[i] = A[i], A[0]
        heap_size -= 1
        # Restore heap property
        maxHeapify(A, 0, heap_size)
```

**Detailed Example**:
```
Array: [4, 1, 3, 2, 16, 9, 10, 14, 8, 7]

Step 1: Build max heap
[16, 14, 10, 8, 7, 9, 3, 2, 4, 1]

Step 2: Extract-max iterations
i=9: Swap 16↔1, heapify(0)
     [14, 8, 10, 4, 7, 9, 3, 2, 1, |16]
     
i=8: Swap 14↔1, heapify(0)
     [10, 8, 9, 4, 7, 1, 3, 2, |14, 16]
     
i=7: Swap 10↔2, heapify(0)
     [9, 8, 3, 4, 7, 1, 2, |10, 14, 16]
     
i=6: Swap 9↔2, heapify(0)
     [8, 7, 3, 4, 2, 1, |9, 10, 14, 16]
     
... continuing ...

Final: [1, 2, 3, 4, 7, 8, 9, 10, 14, 16]
```

**Time Complexity**:
- Build heap: O(n)
- n-1 extract-max: each O(log n)
- **Total: O(n log n)** in all cases

**Space Complexity**: O(1) (in-place)

**Properties**:
- **Not stable** (can swap equal elements out of order)
- **In-place**
- **O(n log n) worst case** (optimal!)
- Poor cache performance (random access)

### 2.6.4 Priority Queue Application

Heap Sort demonstrates heap's efficiency for priority queues:

```python
class MaxPriorityQueue:
    def __init__(self):
        self.heap = []
    
    def insert(self, key):  # O(log n)
        self.heap.append(float('-inf'))
        self.increaseKey(len(self.heap)-1, key)
    
    def maximum(self):  # O(1)
        return self.heap[0]
    
    def extractMax(self):  # O(log n)
        if len(self.heap) < 1:
            raise Exception("Heap underflow")
        max_val = self.heap[0]
        self.heap[0] = self.heap[-1]
        self.heap.pop()
        maxHeapify(self.heap, 0, len(self.heap))
        return max_val
    
    def increaseKey(self, i, key):  # O(log n)
        if key < self.heap[i]:
            raise Exception("New key smaller than current")
        self.heap[i] = key
        # Bubble up
        while i > 0 and self.heap[parent(i)] < self.heap[i]:
            self.heap[i], self.heap[parent(i)] = self.heap[parent(i)], self.heap[i]
            i = parent(i)
```

**Applications**:
- Event-driven simulation
- Dijkstra's shortest path
- Prim's MST
- Huffman coding
- Job scheduling

---

## PRACTICE PROBLEMS - CHAPTER 2B

### Problem 2.6: Merge Sort Space Optimization
**Q**: Can merge sort be done in O(1) space while maintaining O(n log n) time?

**Solution**: No for arrays (proven). Yes for linked lists with careful implementation.

### Problem 2.7: K-sorted Array
**Q**: Array where each element is at most k positions from its target position. Design O(n log k) sorting algorithm.

**Solution**: Use min-heap of size k+1. Insert first k+1 elements, then repeatedly extract-min and insert next element.

### Problem 2.8: Heap Property Violation
**Q**: In array representing heap, exactly one element violates heap property. Fix in O(log n).

**Solution**: Find violating element, then either bubble-up or heapify-down depending on comparison with parent.

### Problem 2.9: Median in Heap
**Q**: Can we find median in max-heap in O(1) time?

**Answer**: No. Median can be anywhere in heap. Need O(n) to find it.

### Problem 2.10: k-th Smallest in Heap
**Q**: Find k-th smallest element in min-heap of size n in O(k log k) time.

**Solution**: Use auxiliary min-heap. Start with root, repeatedly extract-min and add its children to auxiliary heap. k-th extraction gives answer.

---

# CHAPTER 3: Selection and Order Statistics

## 3.1 Selection Problem

**Problem**: Given array A of n distinct elements and integer k (1 ≤ k ≤ n), find k-th smallest element.

**Special Cases**:
- k = 1: Minimum
- k = n: Maximum
- k = ⌊n/2⌋: Median

**Applications**:
- Finding percentiles
- Robust statistics
- Outlier detection
- Partitioning data

## 3.2 Simple Solutions

### 3.2.1 Sorting-Based
```python
def select(A, k):
    A.sort()
    return A[k-1]
```
**Time**: O(n log n)

### 3.2.2 Partial Sorting
Keep k smallest elements in sorted order:
```python
def selectPartial(A, k):
    # Use min-heap
    import heapq
    return heapq.nsmallest(k, A)[k-1]
```
**Time**: O(n log k)

### 3.2.3 Two-pass Minimum/Maximum
```python
def findMinMax(A):
    min_val = max_val = A[0]
    for x in A[1:]:
        if x < min_val:
            min_val = x
        elif x > max_val:
            max_val = x
    return min_val, max_val
```
**Comparisons**: At most 2n - 2

**Optimized** (compare pairs first):
```python
def findMinMaxOptimized(A):
    n = len(A)
    if n % 2 == 0:
        if A[0] < A[1]:
            min_val, max_val = A[0], A[1]
        else:
            min_val, max_val = A[1], A[0]
        start = 2
    else:
        min_val = max_val = A[0]
        start = 1
    
    for i in range(start, n-1, 2):
        if A[i] < A[i+1]:
            small, large = A[i], A[i+1]
        else:
            small, large = A[i+1], A[i]
        
        if small < min_val:
            min_val = small
        if large > max_val:
            max_val = large
    
    return min_val, max_val
```
**Comparisons**: ⌈3n/2⌉ - 2 (optimal!)

**Proof of optimality**: See Problem 2.5

## 3.3 Randomized Selection (QuickSelect)

### 3.3.1 Algorithm

```python
def randomizedSelect(A, left, right, k):
    """
    Find k-th smallest element (k is 1-indexed)
    """
    if left == right:
        return A[left]
    
    # Partition around random pivot
    pivot_index = randomizedPartition(A, left, right)
    
    # k-th smallest is at position k-1 (0-indexed)
    position = pivot_index - left + 1
    
    if k == position:
        return A[pivot_index]
    elif k < position:
        return randomizedSelect(A, left, pivot_index-1, k)
    else:
        return randomizedSelect(A, pivot_index+1, right, k-position)
```

**Key Difference from Quick Sort**:
- Quick Sort recurses on both sides
- Quick Select recurses on only one side

### 3.3.2 Expected Time Analysis

**Best Case**: O(n) when pivot always splits 50-50

**Worst Case**: O(n²) when pivot always smallest/largest

**Average Case** (detailed derivation):

Let T(n) = expected time to find k-th element in array of size n.

After partition with pivot at rank i:
- If i = k: Done
- If i > k: Recurse on left subarray of size i-1
- If i < k: Recurse on right subarray of size n-i

```
T(n) ≤ T(max(i-1, n-i)) + O(n)  for random i

Expected time:
E[T(n)] = (1/n) · Σ T(max(i-1, n-i)) + O(n)  for i=1 to n
```

**Key Insight**: For any k, at least half the pivots give "good" split (at most 3n/4 elements on larger side)

**Proof**:
```
"Good" pivot i satisfies:
max(i-1, n-i) ≤ 3n/4

This means:
n/4 ≤ i ≤ 3n/4

Number of good pivots: n/2
Probability of good pivot: 1/2

Expected time until good pivot: 2 (geometric distribution)
```

**Recurrence with good pivots**:
```
T(n) ≤ 2 · n + T(3n/4)
     ≤ 2n + 2(3n/4) + T((3/4)²n)
     ≤ 2n[1 + 3/4 + (3/4)² + ...]
     = 2n · 1/(1-3/4)
     = 2n · 4
     = 8n
     = O(n)
```

**More Precise Analysis**:

Using indicator random variables (similar to Quick Sort):

```
Let X = number of comparisons

Xᵢⱼ = I{elements i and j are compared}

E[X] = Σ Σ E[Xᵢⱼ]
     = Σ Σ P(i and j compared)

For selecting k-th element:
P(i and j compared) depends on whether k is between i and j

Careful analysis gives:
E[X] ≤ 4n
```

### 3.3.3 Practical Implementation

```python
import random

def quickSelect(A, k):
    """Find k-th smallest (1-indexed) in-place"""
    return quickSelectHelper(A, 0, len(A)-1, k-1)

def quickSelectHelper(A, left, right, k):
    if left == right:
        return A[left]
    
    # Partition
    pivot_idx = random.randint(left, right)
    A[pivot_idx], A[right] = A[right], A[pivot_idx]
    
    # Lomuto partition
    pivot = A[right]
    i = left - 1
    for j in range(left, right):
        if A[j] <= pivot:
            i += 1
            A[i], A[j] = A[j], A[i]
    A[i+1], A[right] = A[right], A[i+1]
    pivot_idx = i + 1
    
    if k == pivot_idx:
        return A[k]
    elif k < pivot_idx:
        return quickSelectHelper(A, left, pivot_idx-1, k)
    else:
        return quickSelectHelper(A, pivot_idx+1, right, k)
```

**Example**:
```
Array: [3, 2, 1, 5, 4], k=3 (find 3rd smallest)

Initial: A=[3,2,1,5,4], left=0, right=4
Choose pivot=4 (index 4)
After partition: [3,2,1,4,5], pivot_idx=3
k=2 < pivot_idx=3 → recurse left

A=[3,2,1], left=0, right=2, k=2
Choose pivot=1 (index 2)
After partition: [1,2,3], pivot_idx=0
k=2 > pivot_idx=0 → recurse right

A=[2,3], left=1, right=2, k=2
Choose pivot=3 (index 2)
After partition: [2,3], pivot_idx=2
k=2 == pivot_idx=2 → return A[2]=3 ✓
```

## 3.4 Deterministic Linear-Time Selection

### 3.4.1 The Median-of-Medians Algorithm

**Goal**: Select k-th element in O(n) time worst-case (not just expected).

**Key Idea**: Choose pivot that guarantees good split!

**Algorithm** (SELECT):
```python
def deterministicSelect(A, left, right, k):
    """
    Find k-th smallest element (0-indexed)
    Guaranteed O(n) worst-case time
    """
    n = right - left + 1
    
    # Base case: small array
    if n <= 5:
        A[left:right+1] = sorted(A[left:right+1])
        return A[left + k]
    
    # Step 1: Divide into groups of 5
    groups = []
    for i in range(left, right+1, 5):
        group_end = min(i+4, right)
        groups.append(A[i:group_end+1])
    
    # Step 2: Find median of each group
    medians = []
    for group in groups:
        group.sort()
        medians.append(group[len(group)//2])
    
    # Step 3: Find median of medians recursively
    mom = deterministicSelect(medians, 0, len(medians)-1, len(medians)//2)
    
    # Step 4: Partition around median-of-medians
    pivot_idx = partition_around_pivot(A, left, right, mom)
    
    # Step 5: Recurse on appropriate side
    pivot_pos = pivot_idx - left
    if k == pivot_pos:
        return A[pivot_idx]
    elif k < pivot_pos:
        return deterministicSelect(A, left, pivot_idx-1, k)
    else:
        return deterministicSelect(A, pivot_idx+1, right, k-pivot_pos-1)

def partition_around_pivot(A, left, right, pivot):
    # Find pivot position
    for i in range(left, right+1):
        if A[i] == pivot:
            A[i], A[right] = A[right], A[i]
            break
    
    # Lomuto partition
    i = left - 1
    for j in range(left, right):
        if A[j] <= pivot:
            i += 1
            A[i], A[j] = A[j], A[i]
    A[i+1], A[right] = A[right], A[i+1]
    return i + 1
```

### 3.4.2 Detailed Example

```
Array: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15]
Find k=7 (7th smallest, 0-indexed) = 8

Step 1: Divide into groups of 5
[1,2,3,4,5], [6,7,8,9,10], [11,12,13,14,15]

Step 2: Find median of each group
Medians: [3, 8, 13]

Step 3: Find median of medians
Median of [3,8,13] = 8

Step 4: Partition around 8
[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15]
After partition: [1,2,3,4,5,6,7,8,9,10,11,12,13,14,15]
pivot_idx = 7, A[7] = 8

Step 5: Check position
k=7 == pivot_idx=7 → return 8 ✓
```

### 3.4.3 Correctness Analysis

**Key Question**: Why does median-of-medians guarantee good split?

**Visualization** (n=15 example):
```
Groups sorted by their medians:
Group 1: [1,  2,  3*,  4,  5]
Group 2: [6,  7,  8*,  9, 10]
Group 3: [11, 12, 13*, 14, 15]

* = median of group
Median of medians = 8

Elements ≤ 8:
- All of Group 1: 5 elements
- At least 3 from Group 2: ≥3 elements
- At least 3 from groups with median < 8: ≥3 elements
Total: At least 3 ⌈n/10⌉ elements

Elements ≥ 8: By symmetry, at least 3 ⌈n/10⌉ elements
```

**General Case**:

Number of groups: ⌈n/5⌉

At least half of medians ≤ median-of-medians:
- Number of such groups: ⌈⌈n/5⌉/2⌉ ≥ ⌈n/10⌉

Each group with median ≤ mom contributes:
- At least 3 elements ≤ mom
- (Except possibly last group if incomplete)

**Lower bound on elements ≤ mom**:
```
≥ 3(⌈⌈n/5⌉/2⌉ - 2)   (subtract 2 for mom's group and possibly incomplete group)
≥ 3n/10 - 6
```

**Upper bound on elements > mom**:
```
≤ n - (3n/10 - 6)
= 7n/10 + 6
```

**Similarly for elements ≥ mom and < mom**

**Conclusion**: After partitioning around median-of-medians, larger subarray has at most 7n/10 + 6 elements.

### 3.4.4 Time Complexity Analysis

**Recurrence**:
```
T(n) = T(⌈n/5⌉) + T(7n/10 + 6) + O(n)
       ↑           ↑              ↑
    Find median   Recursive      Partition +
    of medians    call on        create groups
                  larger side
```

**Proof by Substitution**:

Guess: T(n) ≤ cn for some constant c

**Base case**: T(n) ≤ d for n ≤ some constant n₀

**Inductive step**: Assume true for all k < n
```
T(n) = T(⌈n/5⌉) + T(7n/10 + 6) + an
     ≤ c⌈n/5⌉ + c(7n/10 + 6) + an
     ≤ cn/5 + c + 7cn/10 + 6c + an
     = cn(1/5 + 7/10) + 7c + an
     = 9cn/10 + 7c + an

For T(n) ≤ cn, we need:
9cn/10 + 7c + an ≤ cn
7c + an ≤ cn/10
7c ≤ cn/10 - an
7c ≤ n(c/10 - a)

This holds if c ≥ 10a and n > 70

Therefore: T(n) = O(n) with c = 10a
```

**Why groups of 5?**

Let's check other group sizes:

**Groups of 3**:
```
Median of medians eliminates ≥ 2·⌈n/6⌉ elements
Larger subarray ≤ n - 2⌈n/6⌉ ≈ 2n/3

Recurrence: T(n) = T(n/3) + T(2n/3) + O(n)
Solution: T(n) = O(n log n)  (Master Theorem doesn't apply)
```

**Groups of 5**:
```
Larger subarray ≤ 7n/10

Recurrence: T(n) = T(n/5) + T(7n/10) + O(n)
n/5 + 7n/10 = 9n/10 < n ✓
Solution: T(n) = O(n) ✓
```

**Groups of 7**:
```
Larger subarray ≤ 5n/7

Recurrence: T(n) = T(n/7) + T(5n/7) + O(n)
n/7 + 5n/7 = 6n/7 < 9n/10 ✓
Solution: T(n) = O(n) ✓

But more overhead: sorting groups of 7 vs 5
5 is minimum that gives linear time with practical constants
```

### 3.4.5 Practical Considerations

**Randomized vs Deterministic**:

| Aspect | Randomized (QuickSelect) | Deterministic (MoM) |
|--------|-------------------------|-------------------|
| Expected time | O(n) | O(n) |
| Worst case | O(n²) | O(n) |
| Constant factors | Small (~4n) | Large (~10n) |
| Implementation | Simple | Complex |
| Practical use | Preferred | Rare |

**Why is randomized used in practice?**
- Much simpler implementation
- Better constant factors (2-3x faster)
- Worst case extremely unlikely
- Can use median-of-three for better pivot selection

**Improved practical selection**:
```python
def practicalSelect(A, left, right, k):
    # Use insertion sort for small arrays
    if right - left + 1 < 10:
        A[left:right+1] = sorted(A[left:right+1])
        return A[left + k]
    
    # Median-of-three pivot selection
    mid = (left + right) // 2
    # Order left, mid, right
    if A[left] > A[mid]:
        A[left], A[mid] = A[mid], A[left]
    if A[left] > A[right]:
        A[left], A[right] = A[right], A[left]
    if A[mid] > A[right]:
        A[mid], A[right] = A[right], A[mid]
    
    # Use middle as pivot
    A[mid], A[right-1] = A[right-1], A[mid]
    pivot = A[right-1]
    
    # Partition
    i = left
    j = right - 1
    while True:
        i += 1
        while A[i] < pivot:
            i += 1
        j -= 1
        while A[j] > pivot:
            j -= 1
        if i >= j:
            break
        A[i], A[j] = A[j], A[i]
    
    A[i], A[right-1] = A[right-1], A[i]
    
    # Recurse
    if k == i - left:
        return A[i]
    elif k < i - left:
        return practicalSelect(A, left, i-1, k)
    else:
        return practicalSelect(A, i+1, right, k-(i-left+1))
```

---

## PRACTICE PROBLEMS - CHAPTER 3

### Problem 3.1: k-th Largest
**Q**: Modify selection algorithm to find k-th largest element.

**Solution**: Find (n-k+1)-th smallest element.

### Problem 3.2: Median of Two Sorted Arrays
**Q**: Two sorted arrays A[n] and B[m]. Find median in O(log(min(n,m))).

**Solution Sketch**: 
- Binary search on smaller array
- Partition both arrays so left halves have (n+m)/2 elements
- Check if partition is valid: max(leftA) ≤ min(rightB) and max(leftB) ≤ min(rightA)

### Problem 3.3: Closest to Median
**Q**: Find k numbers closest to median in O(n) time.

**Solution**:
1. Find median: O(n)
2. Create array of distances: O(n)
3. Find k-th smallest distance: O(n)
4. Collect all elements with distance ≤ k-th distance: O(n)

### Problem 3.4: Selection in Stream
**Q**: Elements arrive one by one. Maintain k-th smallest efficiently.

**Solution**: Use two heaps:
- Max-heap of k smallest elements
- Min-heap of remaining elements
- When new element arrives: O(log k) to update

### Problem 3.5: Majority Element
**Q**: Element appearing > n/2 times. Find in O(n) time, O(1) space.

**Solution** (Boyer-Moore Voting Algorithm):
```python
def findMajority(A):
    candidate = None
    count = 0
    
    for x in A:
        if count == 0:
            candidate = x
            count = 1
        elif x == candidate:
            count += 1
        else:
            count -= 1
    
    # Verify candidate
    if A.count(candidate) > len(A)//2:
        return candidate
    return None
```

### Problem 3.6: Weighted Median
**Q**: Each element has weight. Weighted median m satisfies: Σ(wᵢ for xᵢ<m) ≤ W/2 and Σ(wᵢ for xᵢ>m) ≤ W/2. Find in O(n) expected time.

**Solution**: Modify QuickSelect to track cumulative weights.

---

# CHAPTER 4: Non-Comparison Based Sorting

## 4.1 Counting Sort

### 4.1.1 The Algorithm

**Assumption**: Input elements are integers in range [0, k]

**Idea**: For each element x, determine how many elements are ≤ x. Place x directly in correct position.

**Algorithm**:
```python
def countingSort(A, k):
    """
    Sort array A of integers in range [0, k]
    Returns sorted array (not in-place)
    """
    n = len(A)
    C = [0] * (k + 1)  # Count array
    B = [0] * n         # Output array
    
    # Count occurrences
    for j in range(n):
        C[A[j]] += 1
    
    # C[i] now contains number of elements equal to i
    
    # Convert to cumulative counts
    for i in range(1, k + 1):
        C[i] = C[i] + C[i-1]
    
    # C[i] now contains number of elements ≤ i
    
    # Build output array (go backwards for stability)
    for j in range(n-1, -1, -1):
        B[C[A[j]] - 1] = A[j]
        C[A[j]] -= 1
    
    return B
```

### 4.1.2 Detailed Example

```
Input: A = [2, 5, 3, 0, 2, 3, 0, 3]
k = 5 (range 0-5)
n = 8

Step 1: Count occurrences
For each element in A, increment C[element]:
C = [2, 0, 2, 3, 0, 1]
     0  1  2  3  4  5
     ↑     ↑  ↑     ↑
   two 0s  two 2s  three 3s  one 5

Step 2: Cumulative counts
C[0] = 2
C[1] = 2 + 0 = 2
C[2] = 2 + 2 = 4
C[3] = 4 + 3 = 7
C[4] = 7 + 0 = 7
C[5] = 7 + 1 = 8

C = [2, 2, 4, 7, 7, 8]

Interpretation: C[i] = number of elements ≤ i
- Elements ≤ 0: 2
- Elements ≤ 2: 4
- Elements ≤ 3: 7
- Elements ≤ 5: 8

Step 3: Build output (process A backwards for stability)
j=7: A[7]=3, C[3]=7 → B[6]=3, C[3]=6
j=6: A[6]=0, C[0]=2 → B[1]=0, C[0]=1
j=5: A[5]=3, C[3]=6 → B[5]=3, C[3]=5
j=4: A[4]=2, C[2]=4 → B[3]=2, C[2]=3
j=3: A[3]=0, C[0]=1 → B[0]=0, C[0]=0
j=2: A[2]=3, C[3]=5 → B[4]=3, C[3]=4
j=1: A[1]=5, C[5]=8 → B[7]=5, C[5]=7
j=0: A[0]=2, C[2]=3 → B[2]=2, C[2]=2

Output: B = [0, 0, 2, 2, 3, 3, 3, 5]
```

### 4.1.3 Why Process Backwards?

**Stability**: Equal elements maintain relative order.

**Example**:
```
Input: [(2,a), (5,b), (2,c)]  (value, tag)

Forward processing:
j=0: A[0]=(2,a), position = C[2]-1 = 1 → B[1]=(2,a)
j=2: A[2]=(2,c), position = C[2]-1 = 0 → B[0]=(2,c)
Result: [(2,c), (2,a), ...] ✗ Not stable!

Backward processing:
j=2: A[2]=(2,c), position = C[2]-1 = 1 → B[1]=(2,c)
j=0: A[0]=(2,a), position = C[2]-1 = 0 → B[0]=(2,a)
Result: [(2,a), (2,c), ...] ✓ Stable!
```

### 4.1.4 Complexity Analysis

**Time Complexity**:
```
Line 3-4 (initialize): O(k)
Lines 7-8 (count): O(n)
Lines 13-14 (cumulative): O(k)
Lines 19-21 (build output): O(n)

Total: O(n + k)
```

**Space Complexity**: O(n + k)
- Output array B: O(n)
- Count array C: O(k)

**When is Counting Sort efficient?**
- When k = O(n), giving O(n) sorting!
- Example: Sorting ages of people (k ≈ 100)
- Example: Sorting exam scores (k ≈ 100)

**When NOT to use**:
- k >> n (e.g., k = 10^9, n = 100)
- Floating point numbers
- Strings (use radix sort)

### 4.1.5 Variants

**In-place Counting Sort** (not stable):
```python
def countingSortInPlace(A, k):
    count = [0] * (k + 1)
    
    for x in A:
        count[x] += 1
    
    index = 0
    for value in range(k + 1):
        for _ in range(count[value]):
            A[index] = value
            index += 1
```

**Counting Sort for Negative Numbers**:
```python
def countingSortNegative(A):
    min_val = min(A)
    max_val = max(A)
    range_size = max_val - min_val + 1
    
    count = [0] * range_size
    output = [0] * len(A)
    
    for num in A:
        count[num - min_val] += 1
    
    for i in range(1, range_size):
        count[i] += count[i-1]
    
    for num in reversed(A):
        output[count[num - min_val] - 1] = num
        count[num - min_val] -= 1
    
    return output
```

## 4.2 Radix Sort

### 4.2.1 The Idea

**Problem**: Sort numbers with d digits using stable sort on each digit.

**Two variants**:
1. **LSD** (Least Significant Digit first): Process right to left
2. **MSD** (Most Significant Digit first): Process left to right

**LSD is more common** - we'll focus on it.

### 4.2.2 LSD Radix Sort Algorithm

```python
def radixSort(A, d):
    """
    Sort array A of d-digit numbers
    Uses counting sort as subroutine
    """
    # Sort on each digit from least to most significant
    for digit_pos in range(d):
        countingSortByDigit(A, digit_pos)

def countingSortByDigit(A, digit_pos):
    """
    Stable sort A based on digit at digit_pos
    Assuming base-10 numbers
    """
    n = len(A)
    output = [0] * n
    count = [0] * 10  # 10 digits: 0-9
    
    # Count occurrences of each digit
    for num in A:
        digit = (num // (10 ** digit_pos)) % 10
        count[digit] += 1
    
    # Cumulative count
    for i in range(1, 10):
        count[i] += count[i-1]
    
    # Build output (backwards for stability)
    for i in range(n-1, -1, -1):
        digit = (A[i] // (10 ** digit_pos)) % 10
        output[count[digit] - 1] = A[i]
        count[digit] -= 1
    
    # Copy back
    for i in range(n):
        A[i] = output[i]
```

### 4.2.3 Detailed Example

```
Input: A = [170, 45, 75, 90, 802, 24, 2, 66]
d = 3 (maximum digits)

Digit 0 (ones place):
Sort by: 170→0, 45→5, 75→5, 90→0, 802→2, 24→4, 2→2, 66→6
Result: [170, 90, 802, 2, 24, 45, 75, 66]

Digit 1 (tens place):
Sort by: 170→7, 90→9, 802→0, 2→0, 24→2, 45→4, 75→7, 66→6
Result: [802, 2, 24, 45, 66, 170, 75, 90]

Digit 2 (hundreds place):
Sort by: 802→8, 2→0, 24→0, 45→0, 66→0, 170→1, 75→0, 90→0
Result: [2, 24, 45, 66, 75, 90, 170, 802]

Final: [2, 24, 45, 66, 75, 90, 170, 802] ✓
```

**Why does LSD work?**

**Intuition**: After sorting on digit i, all numbers are sorted with respect to their i rightmost digits. Sorting on digit i+1 (more significant) preserves this order due to stability.

**Formal Proof by Induction**:

**Base case**: After sorting on digit 0 (ones), numbers are sorted by last digit ✓

**Inductive step**: Assume after sorting on digit i-1, numbers are sorted by their last i digits.

After sorting on digit i:
- Numbers with different digit-i values are in correct order (just sorted)
- Numbers with same digit-i value maintain relative order (stability)
- Their relative order was correct for last i digits (induction hypothesis)
- Therefore, sorted by last i+1 digits ✓

**Termination**: After sorting on digit d-1, sorted by all d digits ✓

### 4.2.4 Complexity Analysis

**Time Complexity**:
```
Outer loop: d iterations
Each iteration: O(n + b) where b = base (usually 10)

Total: O(d(n + b))
```

**Choosing the base b**:

Represent numbers in base b instead of base 10:
- Number of digits: d = ⌈log_b(k)⌉ where k = max value
- Time: O(⌈log_b(k)⌉ · (n + b))

**Optimization**: Choose b to minimize d(n + b)

Taking derivative with respect to b:
```
d/db [log_b(k) · (n + b)] = 0

log_b(k) = (n + b) / (b · ln b)

Approximately: b ≈ n for optimal performance
```

**With optimal base**:
```
d = log_n(k)
Time = O(log_n(k) · n) = O(n · log k / log n)

For k ≤ n^c (polynomial range):
Time = O(cn) = O(n)
```

**Example**: Sort n 32-bit integers
- Base b = n
- d = log_n(2^32) = 32/log n
- Time = O(32n/log n · n) = O(32n)

**Space Complexity**: O(n + b)

### 4.2.5 MSD Radix Sort

**Algorithm** (recursive):
```python
def msdRadixSort(A, left, right, digit_pos, d):
    if left >= right or digit_pos >= d:
        return
    
    # Count sort on current digit
    buckets = [[] for _ in range(10)]
    for i in range(left, right+1):
        digit = (A[i] // (10 ** (d - digit_pos - 1))) % 10
        buckets[digit].append(A[i])
    
    # Copy back and recurse on each bucket
    index = left
    for bucket in buckets:
        for num in bucket:
            A[index] = num
            index += 1
        
        bucket_start = index - len(bucket)
        bucket_end = index - 1
        msdRadixSort(A, bucket_start, bucket_end, digit_pos+1, d)
```

**MSD vs LSD**:

| Feature | LSD | MSD |
|---------|-----|-----|
| Process order | Right to left | Left to right |
| Recursion | No | Yes |
| Early termination | No | Yes (if bucket empty) |
| Variable length | Must pad | Handles naturally |
| Stability | Important | Less important |
| Overhead | Lower | Higher |

**When to use MSD**:
- Variable-length keys
- Want early termination
- Sorting strings

### 4.2.6 Radix Sort for Strings

```python
def radixSortStrings(strings, max_len):
    """
    Sort strings using LSD radix sort
    Assumes all strings padded to max_len
    """
    for pos in range(max_len-1, -1, -1):
        countingSortStringsByChar(strings, pos)

def countingSortStringsByChar(strings, pos):
    """
    Stable sort strings by character at position pos
    """
    n = len(strings)
    output = [''] * n
    count = [0] * 256  # ASCII characters
    
    for s in strings:
        if pos < len(s):
            char_code = ord(s[pos])
        else:
            char_code = 0  # Treat as null
        count[char_code] += 1
    
    for i in range(1, 256):
        count[i] += count[i-1]
    
    for i in range(n-1, -1, -1):
        s = strings[i]
        if pos < len(s):
            char_code = ord(s[pos])
        else:
            char_code = 0
        output[count[char_code] - 1] = s
        count[char_code] -= 1
    
    for i in range(n):
        strings[i] = output[i]
```

**Example**:
```
Input: ["dog", "cat", "bat", "rat", "mat"]
max_len = 3

Position 2 (last char):
Sort by: g, t, t, t, t
Result: ["cat", "bat", "rat", "mat", "dog"]

Position 1 (middle char):
Sort by: a, a, a, a, o
Result: ["cat", "bat", "mat", "rat", "dog"]

Position 0 (first char):
Sort by: c, b, m, r, d
Result: ["bat", "cat", "dog", "mat", "rat"]
```

## 4.3 Bucket Sort

### 4.3.1 The Algorithm

**Assumption**: Input uniformly distributed over [0, 1)

**Idea**: 
1. Divide [0, 1) into n equal buckets
2. Distribute elements into buckets
3. Sort each bucket
4. Concatenate buckets

**Algorithm**:
```python
def bucketSort(A):
    """
    Sort array A of floats in [0, 1)
    """
    n = len(A)
    
    # Create empty buckets
    buckets = [[] for _ in range(n)]
    
    # Distribute elements into buckets
    for num in A:
        index = int(n * num)  # Bucket index
        if index == n:  # Handle edge case where num = 1
            index = n - 1
        buckets[index].append(num)
    
    # Sort individual buckets using insertion sort
    for bucket in buckets:
        insertionSort(bucket)
    
    # Concatenate all buckets
    result = []
    for bucket in buckets:
        result.extend(bucket)
    
    return result
```

### 4.3.2 Detailed Example

```
Input: A = [0.78, 0.17, 0.39, 0.26, 0.72, 0.94, 0.21, 0.12, 0.23, 0.68]
n = 10

Step 1: Create 10 buckets for ranges [0,0.1), [0.1,0.2), ..., [0.9,1.0)

Step 2: Distribute elements
0.78 → bucket 7 [0.7, 0.8)
0.17 → bucket 1 [0.1, 0.2)
0.39 → bucket 3 [0.3, 0.4)
0.26 → bucket 2 [0.2, 0.3)
0.72 → bucket 7 [0.7, 0.8)
0.94 → bucket 9 [0.9, 1.0)
0.21 → bucket 2 [0.2, 0.3)
0.12 → bucket 1 [0.1, 0.2)
0.23 → bucket 2 [0.2, 0.3)
0.68 → bucket 6 [0.6, 0.7)

Buckets:
[0]: []
[1]: [0.17, 0.12]
[2]: [0.26, 0.21, 0.23]
[3]: [0.39]
[4]: []
[5]: []
[6]: [0.68]
[7]: [0.78, 0.72]
[8]: []
[9]: [0.94]

Step 3: Sort each bucket (insertion sort)
[0]: []
[1]: [0.12, 0.17]
[2]: [0.21, 0.23, 0.26]
[3]: [0.39]
[4]: []
[5]: []
[6]: [0.68]
[7]: [0.72, 0.78]
[8]: []
[9]: [0.94]

Step 4: Concatenate
Result: [0.12, 0.17, 0.21, 0.23, 0.26, 0.39, 0.68, 0.72, 0.78, 0.94]
```

### 4.3.3 Time Complexity Analysis

**Average Case** (with uniform distribution):

Let n_i = number of elements in bucket i

**Time breakdown**:
```
Creating buckets: O(n)
Distributing elements: O(n)
Sorting buckets: Σ O(n_i²) for i=0 to n-1
Concatenating: O(n)

Total: O(n) + Σ O(n_i²)
```

**Expected value of Σ n_i²**:

Using indicator random variables:
```
Let X_ij = I{element j falls in bucket i}

n_i = Σ X_ij for j=1 to n

E[n_i] = Σ E[X_ij] = Σ (1/n) = 1

n_i² = (Σ X_ij)²
     = Σ Σ X_ij·X_kj
     = Σ X_ij² + Σ Σ X_ij·X_kj  (j≠k)

E[n_i²] = Σ E[X_ij²] + Σ Σ E[X_ij·X_kj]  (j≠k)
        = Σ E[X_ij] + Σ Σ E[X_ij]·E[X_kj]  (independence)
        = n·(1/n) + n(n-1)·(1/n)²
        = 1 + (n-1)/n
        = 2 - 1/n

Expected total cost of sorting all buckets:
E[Σ n_i²] = Σ E[n_i²]
          = n · (2 - 1/n)
          = 2n - 1

Therefore: E[T(n)] = O(n) + O(2n - 1) = O(n)
```

**Worst Case**: O(n²)
- Occurs when all elements fall into one bucket
- Example: Input already sorted or reverse sorted

**Space Complexity**: O(n)

### 4.3.4 Variants and Extensions

**Bucket Sort for Arbitrary Range [a, b)**:
```python
def bucketSortRange(A, a, b):
    n = len(A)
    buckets = [[] for _ in range(n)]
    
    # Normalize to [0, 1) and distribute
    for num in A:
        normalized = (num - a) / (b - a)
        index = int(n * normalized)
        if index == n:
            index = n - 1
        buckets[index].append(num)
    
    # Sort and concatenate
    result = []
    for bucket in buckets:
        bucket.sort()
        result.extend(bucket)
    
    return result
```

**Bucket Sort for Integers**:
```python
def bucketSortIntegers(A):
    if not A:
        return A
    
    min_val = min(A)
    max_val = max(A)
    n = len(A)
    
    # Create buckets
    bucket_range = (max_val - min_val) / n + 1
    buckets = [[] for _ in range(n)]
    
    # Distribute
    for num in A:
        index = int((num - min_val) / bucket_range)
        buckets[index].append(num)
    
    # Sort and concatenate
    result = []
    for bucket in buckets:
        bucket.sort()
        result.extend(bucket)
    
    return result
```

**Adaptive Bucket Count**:
```python
def adaptiveBucketSort(A):
    """
    Adjust number of buckets based on data distribution
    """
    n = len(A)
    if n <= 1:
        return A
    
    # Use sqrt(n) buckets as heuristic
    num_buckets = int(n ** 0.5) + 1
    
    min_val = min(A)
    max_val = max(A)
    bucket_range = (max_val - min_val) / num_buckets + 1e-9
    
    buckets = [[] for _ in range(num_buckets)]
    
    for num in A:
        index = int((num - min_val) / bucket_range)
        if index >= num_buckets:
            index = num_buckets - 1
        buckets[index].append(num)
    
    result = []
    for bucket in buckets:
        bucket.sort()
        result.extend(bucket)
    
    return result
```

### 4.3.5 When to Use Bucket Sort

**Ideal conditions**:
- Input uniformly distributed
- Know approximate range of values
- Can afford O(n) extra space
- Want O(n) average case

**Real-world applications**:
- External sorting (when data doesn't fit in memory)
- Parallel sorting (buckets can be sorted independently)
- Histogram equalization in image processing
- Hash table implementations

**Comparison with other sorts**:

| Algorithm | Average | Worst | Space | Assumption |
|-----------|---------|-------|-------|------------|
| Counting Sort | O(n+k) | O(n+k) | O(n+k) | Integers in [0,k] |
| Radix Sort | O(d(n+b)) | O(d(n+b)) | O(n+b) | d-digit numbers |
| Bucket Sort | O(n) | O(n²) | O(n) | Uniform distribution |

---

## PRACTICE PROBLEMS - CHAPTER 4

### Problem 4.1: Counting Sort Range Query
**Q**: After running counting sort, answer queries: "How many elements in range [a,b]?" in O(1) time.

**Solution**: Use cumulative count array C. Answer = C[b] - C[a-1].

### Problem 4.2: Sort by Digit Sum
**Q**: Sort n integers by sum of their digits. Design O(n) algorithm if digit sums are in range [1, k].

**Solution**: 
1. Compute digit sum for each number: O(n·d) where d = digits
2. Use counting sort on digit sums: O(n+k)
3. Total: O(n·d + k) = O(n) if k = O(n) and d = O(1)

### Problem 4.3: Two-key Radix Sort
**Q**: Sort pairs (a,b) lexicographically using radix sort.

**Solution**:
1. Sort by second component (b)
2. Sort by first component (a) using stable sort
3. Time: O(n) if both components in constant range

### Problem 4.4: Bucket Sort with Linked Lists
**Q**: Implement bucket sort using linked lists instead of arrays for buckets. Analyze space usage.

**Solution**: Space improves to O(n) total instead of O(n²) worst case with arrays.

### Problem 4.5: k-way Radix Sort
**Q**: Extend radix sort to process k digits at a time. Analyze optimal k.

**Solution**: 
- Time: O((d/k)(n + b^k)) where b = base
- Optimal when d/k ≈ n + b^k
- Usually k = 1 or 2 in practice

### Problem 4.6: Sort Nearly Sorted Array
**Q**: Array where each element is at most k positions from correct location. Design O(n log k) algorithm.

**Solution**: Use min-heap of size k+1:
```python
def sortNearlySorted(A, k):
    import heapq
    result = []
    heap = A[:k+1]
    heapq.heapify(heap)
    
    for i in range(k+1, len(A)):
        result.append(heapq.heappop(heap))
        heapq.heappush(heap, A[i])
    
    while heap:
        result.append(heapq.heappop(heap))
    
    return result
```

---

# CHAPTER 5: Computational Geometry - Convex Hull

## 5.1 Introduction to Convex Hull

### 5.1.1 Definitions

**Convex Set**: A set S is convex if for any two points p, q ∈ S, the line segment pq lies entirely in S.

**Convex Hull**: The convex hull CH(P) of a point set P is the smallest convex set containing P.

**Intuition**: Imagine points as nails on a board. Stretch a rubber band around them—it forms the convex hull.

**Formal Definition**:
```
CH(P) = {Σ λᵢpᵢ : pᵢ ∈ P, λᵢ ≥ 0, Σ λᵢ = 1}
```

### 5.1.2 Properties

1. **CH(P) is a convex polygon** if |P| ≥ 3 and points not collinear
2. **Vertices of CH(P)** are a subset of P
3. **|CH(P)| ≤ |P|** (number of vertices)
4. **CH(CH(P)) = CH(P)** (idempotent)
5. **Unique** for any point set

### 5.1.3 Applications

- **Computer Graphics**: Collision detection, rendering
- **Pattern Recognition**: Shape analysis, image processing
- **Geographic Information Systems**: Finding boundaries
- **Robotics**: Path planning, obstacle avoidance
- **Statistics**: Outlier detection
- **Game Development**: Line of sight, territory control

## 5.2 Geometric Primitives

### 5.2.1 Cross Product (2D)

For vectors u = (u₁, u₂) and v = (v₁, v₂):
```
u × v = u₁v₂ - u₂v₁

Interpretation:
> 0: v is counterclockwise from u (left turn)
= 0: v is collinear with u
< 0: v is clockwise from u (right turn)
```

**Geometric meaning**: Signed area of parallelogram formed by u and v.

### 5.2.2 Orientation Test

Given three points p₁, p₂, p₃, determine orientation:

```python
def orientation(p1, p2, p3):
    """
    Returns:
    > 0 if counterclockwise (left turn)
    = 0 if collinear
    < 0 if clockwise (right turn)
    """
    return (p2[0] - p1[0]) * (p3[1] - p1[1]) - \
           (p2[1] - p1[1]) * (p3[0] - p1[0])
```

**Derivation using vectors**:
```
Vector p1→p2 = (p2.x - p1.x, p2.y - p1.y)
Vector p1→p3 = (p3.x - p1.x, p3.y - p1.y)

Cross product = (p2.x - p1.x)(p3.y - p1.y) - (p2.y - p1.y)(p3.x - p1.x)

If > 0: p3 is left of line p1→p2
If = 0: p3 is on line p1→p2
If < 0: p3 is right of line p1→p2
```

**Example**:
```
p1 = (0, 0), p2 = (4, 4), p3 = (2, 3)

orientation(p1, p2, p3) = (4-0)(3-0) - (4-0)(2-0)
                        = 4·3 - 4·2
                        = 12 - 8
                        = 4 > 0

Therefore: Left turn (counterclockwise)
```

### 5.2.3 Polar Angle

For point p with respect to reference point p₀:
```
θ = atan2(p.y - p₀.y, p.x - p₀.x)
```

**Range**: [-π, π] or [0, 2π]

**Comparison without computing angle**:
```python
def polarAngleComparator(p0):
    """
    Returns comparator function for sorting by polar angle
    """
    def compare(p1, p2):
        # Check which quadrant
        q1 = quadrant(p1, p0)
        q2 = quadrant(p2, p0)
        
        if q1 != q2:
            return q1 - q2
        
        # Same quadrant: use cross product
        cross = orientation(p0, p1, p2)
        if cross == 0:
            # Collinear: closer point first
            dist1 = (p1[0]-p0[0])**2 + (p1[1]-p0[1])**2
            dist2 = (p2[0]-p0[0])**2 + (p2[1]-p0[1])**2
            return dist1 - dist2
        return -cross  # Counterclockwise order
    
    return compare

def quadrant(p, p0):
    """Returns quadrant number 1-4"""
    dx = p[0] - p0[0]
    dy = p[1] - p0[1]
    
    if dx >= 0 and dy >= 0:
        return 1
    elif dx < 0 and dy >= 0:
        return 2
    elif dx < 0 and dy < 0:
        return 3
    else:
        return 4
```

## 5.3 Graham's Scan Algorithm

### 5.3.1 The Algorithm

**High-level idea**:
1. Find lowest point (break ties by x-coordinate)
2. Sort remaining points by polar angle
3. Process points in order, maintaining convex hull
4. Use stack to track hull vertices
5. Remove points that create right turns

**Detailed Algorithm**:
```python
def grahamScan(points):
    """
    Compute convex hull using Graham's scan
    Returns vertices in counterclockwise order
    """
    n = len(points)
    if n < 3:
        return points
    
    # Step 1: Find lowest point (min y, then min x)
    p0 = min(points, key=lambda p: (p[1], p[0]))
    
    # Step 2: Sort by polar angle with respect to p0
    def polarKey(p):
        return (math.atan2(p[1] - p0[1], p[0] - p0[0]), 
                (p[0]-p0[0])**2 + (p[1]-p0[1])**2)
    
    sorted_points = [p0] + sorted([p for p in points if p != p0], 
                                   key=polarKey)
    
    # Step 3: Initialize stack with first 3 points
    stack = [sorted_points[0], sorted_points[1], sorted_points[2]]
    
    # Step 4: Process remaining points
    for i in range(3, n):
        # Remove points that make right turn
        while len(stack) > 1 and \
              orientation(stack[-2], stack[-1], sorted_points[i]) <= 0:
            stack.pop()
        
        stack.append(sorted_points[i])
    
    return stack
```

### 5.3.2 Detailed Example

```
Points: [(0,0), (1,1), (2,2), (3,1), (4,2), (2,0), (3,3), (1,3)]

Step 1: Find lowest point
p0 = (0,0) (lowest y-coordinate)

Step 2: Sort by polar angle from p0
Angles (in degrees):
(1,1): 45°
(2,2): 45° (farther)
(1,3): 71.57°
(3,3): 45° (farthest)
(2,0): 0°
(3,1): 18.43°
(4,2): 26.57°

Sorted: [(0,0), (2,0), (3,1), (4,2), (1,1), (2,2), (3,3), (1,3)]

Step 3: Initialize stack
Stack: [(0,0), (2,0), (3,1)]

Step 4: Process remaining points

Process (4,2):
  Check turn at (2,0), (3,1), (4,2)
  orientation = (3-2)(2-0) - (1-0)(4-2) = 2 - 2 = 0 (collinear)
  Remove (3,1)
  Stack: [(0,0), (2,0)]
  Add (4,2)
  Stack: [(0,0), (2,0), (4,2)]

Process (1,1):
  Check turn at (2,0), (4,2), (1,1)
  orientation = (4-2)(1-0) - (2-0)(1-2) = 2 + 2 = 4 > 0 (left turn)
  Keep (4,2), add (1,1)
  Stack: [(0,0), (2,0), (4,2), (1,1)]

Process (2,2):
  Check turn at (4,2), (1,1), (2,2)
  orientation = (1-4)(2-2) - (1-2)(2-4) = 0 - 2 = -2 < 0 (right turn)
  Remove (1,1)
  Check turn at (2,0), (4,2), (2,2)
  orientation = (4-2)(2-0) - (2-0)(2-2) = 4 > 0 (left turn)
  Add (2,2)
  Stack: [(0,0), (2,0), (4,2), (2,2)]

Process (3,3):
  Check turn at (4,2), (2,2), (3,3)
  orientation = (2-4)(3-2) - (2-2)(3-4) = -2 < 0 (right turn)
  Remove (2,2)
  Check turn at (2,0), (4,2), (3,3)
  orientation = (4-2)(3-0) - (2-0)(3-2) = 6 - 2 = 4 > 0 (left turn)
  Add (3,3)
  Stack: [(0,0), (2,0), (4,2), (3,3)]

Process (1,3):
  Check turn at (4,2), (3,3), (1,3)
  orientation = (3-4)(3-2) - (3-2)(1-3) = -1 + 2 = 1 > 0 (left turn)
  Add (1,3)
  Stack: [(0,0), (2,0), (4,2), (3,3), (1,3)]

Final convex hull: [(0,0), (2,0), (4,2), (3,3), (1,3)]
```

**Visualization**:
```
    (1,3)----(3,3)
     |         |
     |         |
     |        (4,2)
     |       /
     |     /
  (0,0)-(2,0)
```

### 5.3.3 Correctness Proof

**Claim**: Graham's scan correctly computes the convex hull.

**Proof sketch**:

**Lemma 1**: Points are processed in order of increasing polar angle around p0.

*Proof*: By construction (sorting step). ✓

**Lemma 2**: At any point, stack contains vertices of convex hull of points processed so far.

*Proof by induction*:

*Base case*: After adding first 3 points, they form a convex hull (triangle). ✓

*Inductive step*: Assume stack contains CH of first i points. When processing point p_{i+1}:
- If left turn from last two stack points to p_{i+1}: p_{i+1} is on hull, add it. ✓
- If right turn: Second-to-last point is interior (not on final hull), remove it. Continue checking. ✓

**Lemma 3**: No hull vertex is incorrectly removed.

*Proof*: A point is removed only when a later point (larger polar angle) makes it interior. Since we process in polar angle order, this point cannot be on the final hull. ✓

**Conclusion**: Algorithm correctly computes convex hull. ✓

### 5.3.4 Time Complexity Analysis

**Step-by-step**:
```
Step 1: Find lowest point: O(n)
Step 2: Sort by polar angle: O(n log n)
Step 3: Initialize stack: O(1)
Step 4: Process points: O(n) total

Total: O(n log n)
```

**Why is Step 4 O(n)?**

Each point is:
- Pushed onto stack exactly once: O(n) total
- Popped from stack at most once: O(n) total

Total operations: O(n)

**Space Complexity**: O(n) for stack and sorted array

**Optimality**: O(n log n) is optimal for convex hull in comparison model (can be proven using reduction from sorting).

### 5.3.5 Handling Degenerate Cases

**Collinear points**:
```python
def grahamScanWithCollinear(points):
    # ... (same as before until sorting)
    
    # When sorting, if collinear, keep only farthest
    sorted_points = [p0]
    prev_angle = None
    collinear_group = []
    
    for p in sorted([p for p in points if p != p0], key=polarKey):
        angle = math.atan2(p[1] - p0[1], p[0] - p0[0])
        
        if prev_angle is None or abs(angle - prev_angle) > 1e-9:
            # New angle: add farthest from previous group
            if collinear_group:
                sorted_points.append(max(collinear_group, 
                                       key=lambda q: distance(p0, q)))
            collinear_group = [p]
            prev_angle = angle
        else:
            collinear_group.append(p)
    
    # Add last group
    if collinear_group:
        sorted_points.append(max(collinear_group, 
                                key=lambda q: distance(p0, q)))
    
    # Continue with Graham's scan...
```

**Duplicate points**: Remove during preprocessing

**All collinear**: Return line segment endpoints

## 5.4 Other Convex Hull Algorithms

### 5.4.1 Jarvis March (Gift Wrapping)

**Idea**: Start from leftmost point, repeatedly find next hull vertex by choosing point with smallest polar angle.

**Algorithm**:
```python
def jarvisMarch(points):
    n = len(points)
    if n < 3:
        return points
    
    # Find leftmost point
    leftmost = min(points, key=lambda p: (p[0], p[1]))
    
    hull = []
    current = leftmost
    
    while True:
        hull.append(current)
        next_point = points[0]
        
        # Find point with smallest polar angle
        for p in points:
            if p == current:
                continue
            
            cross = orientation(current, next_point, p)
            if next_point == current or cross > 0 or \
               (cross == 0 and distance(current, p) > distance(current, next_point)):
                next_point = p
        
        current = next_point
        
        # Completed the hull
        if current == leftmost:
            break
    
    return hull
```

**Time Complexity**: O(nh) where h = number of hull vertices
- Best case: O(n) when h is constant
- Worst case: O(n²) when h = n

**When to use**: Output-sensitive algorithm, good when h << n

### 5.4.2 QuickHull

**Idea**: Divide and conquer, similar to QuickSort.

**Algorithm**:
1. Find leftmost and rightmost points
2. These divide points into upper and lower hull
3. Recursively find farthest point from line and recurse
4. Base case: No points outside triangle

**Time Complexity**:
- Average: O(n log n)
- Worst case: O(n²)

**Advantage**: Often faster in practice than Graham's scan

### 5.4.3 Chan's Algorithm

**Combines**: Jarvis march + Graham's scan

**Idea**: 
- Partition points into m groups
- Compute convex hull of each group
- Use Jarvis march on these hulls

**Time Complexity**: O(n log h) where h = hull size

**Optimal output-sensitive algorithm**!

**When to use**: When h is unknown and potentially small

---

## PRACTICE PROBLEMS - CHAPTER 5

### Problem 5.1: Area of Convex Hull
**Q**: Given convex hull vertices in order, compute area in O(n).

**Solution** (Shoelace formula):
```python
def polygonArea(vertices):
    n = len(vertices)
    area = 0
    for i in range(n):
        j = (i + 1) % n
        area += vertices[i][0] * vertices[j][1]
        area -= vertices[j][0] * vertices[i][1]
    return abs(area) / 2
```

### Problem 5.2: Point Inside Convex Polygon
**Q**: Check if point p is inside convex polygon in O(log n).

**Solution**: Binary search on polar angle, then check if point is on correct side of edge.

### Problem 5.3: Diameter of Point Set
**Q**: Find maximum distance between any two points. Design O(n log n) algorithm.

**Solution**: 
1. Compute convex hull: O(n log n)
2. Use rotating calipers: O(n)
3. Diameter must be between two hull vertices

### Problem 5.4: Convex Layers
**Q**: Compute all convex hull layers (onion peeling). Analyze complexity.

**Solution**: Repeatedly compute convex hull and remove vertices. O(n²) worst case, O(nh) where h = number of layers.

### Problem 5.5: Minimum Enclosing Circle
**Q**: Find smallest circle enclosing all points. Design O(n) expected time algorithm.

**Hint**: Use Welzl's algorithm (randomized incremental construction).

### Problem 5.6: Line-Hull Intersection
**Q**: Given line and convex hull, find intersection points in O(log n).

**Solution**: Binary search to find edges intersected by line.

---

# CHAPTER 6: Graph Algorithms - BFS and Shortest Paths

## 6.1 Graph Representations

### 6.1.1 Adjacency Matrix

**Structure**: 2D array A where A[i][j] = 1 if edge (i,j) exists

```python
class GraphMatrix:
    def __init__(self, n):
        self.n = n
        self.adj = [[0] * n for _ in range(n)]
    
    def addEdge(self, u, v):
        self.adj[u][v] = 1
        # For undirected: self.adj[v][u] = 1
    
    def hasEdge(self, u, v):
        return self.adj[u][v] == 1
```

**Space**: O(V²)
**Edge check**: O(1)
**List neighbors**: O(V)
**Good for**: Dense graphs, matrix operations

### 6.1.2 Adjacency List

**Structure**: Array of lists, each containing neighbors

```python
class GraphList:
    def __init__(self, n):
        self.n = n
        self.adj = [[] for _ in range(n)]
    
    def addEdge(self, u, v):
        self.adj[u].append(v)
        # For undirected: self.adj[v].append(u)
    
    def neighbors(self, u):
        return self.adj[u]
```

**Space**: O(V + E)
**Edge check**: O(degree(u))
**List neighbors**: O(degree(u))
**Good for**: Sparse graphs, most algorithms

## 6.2 Breadth-First Search (BFS)

### 6.2.1 The Algorithm

## Duplicate Copy

# Comprehensive Algorithms Textbook
## Research-Level Study Material with Derivations and Practice Problems

---

# CHAPTER 1: Probability and Randomization in Algorithms

## 1.1 Introduction to Randomized Algorithms

### 1.1.1 Why Randomization?

Randomization is a powerful tool in algorithm design that can provide:
- **Simplicity**: Often easier to design and implement
- **Efficiency**: Can achieve better average-case performance
- **Avoiding worst-case**: Randomization helps avoid adversarial inputs
- **Contention resolution**: Useful in distributed systems

### 1.1.2 Types of Randomized Algorithms

**1. Las Vegas Algorithms**
- Always produce correct output
- Running time is a random variable
- Example: Randomized Quick Sort

**2. Monte Carlo Algorithms**
- Running time is deterministic
- Output may be incorrect with small probability
- Example: Miller-Rabin primality test

### 1.1.3 Contention Resolution

**Problem**: n processes compete for a shared resource. If multiple processes try simultaneously, all fail.

**Randomized Strategy**: Each process attempts with probability p.

**Analysis**:
```
P(exactly one succeeds) = n·p·(1-p)^(n-1)

To maximize, take derivative:
d/dp [n·p·(1-p)^(n-1)] = 0
n(1-p)^(n-1) - n·p·(n-1)(1-p)^(n-2) = 0
(1-p) = p(n-1)
p = 1/n

Maximum probability = n·(1/n)·(1-1/n)^(n-1)
                    ≈ (1-1/n)^(n-1)
                    ≈ 1/e ≈ 0.368
```

**Key Insight**: With optimal p = 1/n, constant probability of success per round!

**Practical Example: Ethernet CSMA/CD**
```
When collision detected:
1. Wait for random time in [0, 2^k - 1] slots
2. k increases with each collision (exponential backoff)
3. Randomization prevents repeated collisions
```

## 1.2 Probability Theory Foundations

### 1.2.1 Sample Space and Events

**Definitions**:
- **Sample space Ω**: Set of all possible outcomes
- **Event E**: Subset of Ω
- **Probability P(E)**: Measure satisfying:
  - 0 ≤ P(E) ≤ 1
  - P(Ω) = 1
  - P(E₁ ∪ E₂) = P(E₁) + P(E₂) if E₁ ∩ E₂ = ∅

### 1.2.2 Random Variables

**Definition**: A random variable X is a function X: Ω → ℝ

**Discrete Random Variable**:
```
P(X = x) = probability mass function (PMF)

Σ P(X = x) = 1  (over all possible x)
```

**Continuous Random Variable**:
```
f(x) = probability density function (PDF)

∫ f(x)dx = 1  (over all x)
```

### 1.2.3 Expected Value (Most Important!)

**Definition**:
```
E[X] = Σ x·P(X = x)  (discrete)
E[X] = ∫ x·f(x)dx    (continuous)
```

**Properties**:

**1. Linearity of Expectation** (CRITICAL):
```
E[aX + bY] = aE[X] + bE[Y]

This holds EVEN IF X and Y are dependent!
```

**2. Expected value of sum**:
```
E[Σ Xᵢ] = Σ E[Xᵢ]
```

**3. Product (if independent)**:
```
E[XY] = E[X]·E[Y]  (only if X, Y independent)
```

**Example 1: Dice Roll**
```
X = sum of two dice

Method 1 (direct):
E[X] = Σ k·P(X=k) for k=2 to 12
     = 2·(1/36) + 3·(2/36) + ... + 12·(1/36)
     = 7

Method 2 (linearity):
E[X] = E[die₁] + E[die₂]
     = 3.5 + 3.5 = 7  ✓
```

**Example 2: Coupon Collector Problem**

**Problem**: n types of coupons. Each purchase gives random coupon. Expected purchases to collect all?

**Solution**:
```
Let Xᵢ = purchases needed to get (i+1)th new coupon after having i

P(get new coupon) = (n-i)/n

E[Xᵢ] = n/(n-i)  (geometric distribution)

Total expected purchases:
E[X] = Σ E[Xᵢ] from i=0 to n-1
     = n·(1/n + 1/(n-1) + ... + 1/1)
     = n·Hₙ  (Hₙ = harmonic number)
     = n·ln(n) + O(n)
```

### 1.2.4 Indicator Random Variables

**Definition**:
```
I{A} = 1 if event A occurs
       0 otherwise

E[I{A}] = 1·P(A) + 0·P(Ā) = P(A)
```

**This is the most powerful technique for computing expectations!**

**Example: Expected number of inversions in random permutation**

**Problem**: Array A of n distinct elements. Inversion = pair (i,j) where i < j but A[i] > A[j].

**Solution**:
```
For each pair (i,j) with i < j, define:
Xᵢⱼ = I{A[i] > A[j]}

Total inversions X = Σ Σ Xᵢⱼ (i<j)

E[X] = E[Σ Σ Xᵢⱼ]
     = Σ Σ E[Xᵢⱼ]     (linearity!)
     = Σ Σ P(A[i] > A[j])
     = Σ Σ (1/2)       (random permutation)
     = C(n,2) · (1/2)
     = n(n-1)/4
```

### 1.2.5 Variance and Standard Deviation

**Variance**:
```
Var(X) = E[(X - E[X])²]
       = E[X²] - (E[X])²

σ = √Var(X)  (standard deviation)
```

**Properties**:
```
Var(aX + b) = a²·Var(X)

Var(X + Y) = Var(X) + Var(Y)  (if X, Y independent)
```

## 1.3 Probabilistic Analysis of Algorithms

### 1.3.1 Hiring Problem

**Scenario**: Interview n candidates in random order. Hire anyone better than all previous. Cost: interview = $c_i, hire = $c_h (c_h >> c_i).

**Analysis**:
```
Let X = number of hires
Xᵢ = I{candidate i is hired}

Candidate i hired iff best of first i candidates

E[Xᵢ] = P(i is best of first i) = 1/i

E[X] = Σ E[Xᵢ] from i=1 to n
     = Σ (1/i)
     = Hₙ = ln(n) + O(1)

Total cost = c_i·n + c_h·E[X]
           = c_i·n + c_h·ln(n)
```

### 1.3.2 Birthday Paradox

**Problem**: How many people needed so that probability of shared birthday > 1/2?

**Analysis**:
```
P(all different) = 1 · (364/365) · (363/365) · ... · ((365-n+1)/365)

For n = 23:
P(all different) ≈ 0.493
P(at least one match) ≈ 0.507 > 1/2

General formula:
P(collision) ≈ 1 - e^(-n²/2m)  (m = 365)

Setting = 1/2:
n ≈ √(2m·ln(2)) ≈ 1.177√m
```

**Application**: Hash table collisions, cryptographic attacks

---

## PRACTICE PROBLEMS - CHAPTER 1

### Problem 1.1: Load Balancing
**Q**: n jobs assigned randomly to m machines. What is the expected number of jobs on machine 1?

**Solution**:
```
Let Xᵢ = I{job i assigned to machine 1}
E[Xᵢ] = 1/m

Total X = Σ Xᵢ
E[X] = Σ E[Xᵢ] = n/m
```

### Problem 1.2: Maximum Load
**Q**: In above problem, show maximum load is O(log n / log log n) with high probability.

**Hint**: Use balls-and-bins analysis with Chernoff bounds.

### Problem 1.3: Randomized Min-Cut
**Q**: Karger's algorithm contracts random edges. Prove it finds min-cut with probability ≥ 1/C(n,2).

### Problem 1.4: Quick Select Analysis
**Q**: Prove expected number of comparisons in randomized selection is ≤ 4n.

### Problem 1.5: Hashing with Chaining
**Q**: n keys, m slots. Expected length of longest chain?

---

# CHAPTER 2: Sorting Algorithms - Comparison-Based

## 2.1 Introduction to Sorting

**Problem**: Given array A of n comparable elements, rearrange into non-decreasing order.

**Key Properties**:
- **Time Complexity**: Best, Average, Worst case
- **Space Complexity**: Auxiliary space used
- **Stability**: Preserve relative order of equal elements
- **In-place**: Uses O(1) extra space

## 2.2 Elementary Sorting Algorithms

### 2.2.1 Selection Sort

**Algorithm**:
```python
def selectionSort(A):
    n = len(A)
    for i in range(n):
        # Find minimum in A[i..n-1]
        min_idx = i
        for j in range(i+1, n):
            if A[j] < A[min_idx]:
                min_idx = j
        # Swap minimum to position i
        A[i], A[min_idx] = A[min_idx], A[i]
```

**Detailed Analysis**:
```
Comparisons:
- First pass: (n-1) comparisons
- Second pass: (n-2) comparisons
- ...
- Last pass: 1 comparison

Total = (n-1) + (n-2) + ... + 1
      = n(n-1)/2
      = Θ(n²)

Swaps: exactly (n-1) swaps (minimum possible!)

Space: O(1) auxiliary

Stability: No (can make stable with modification)
```

**When to use**: When writes are expensive (flash memory), small datasets

**Example Trace**:
```
Initial:  [64, 25, 12, 22, 11]
i=0: min=11, swap → [11, 25, 12, 22, 64]
i=1: min=12, swap → [11, 12, 25, 22, 64]
i=2: min=22, swap → [11, 12, 22, 25, 64]
i=3: no swap     → [11, 12, 22, 25, 64]
Done
```

### 2.2.2 Insertion Sort

**Algorithm**:
```python
def insertionSort(A):
    for i in range(1, len(A)):
        key = A[i]
        j = i - 1
        # Insert A[i] into sorted A[0..i-1]
        while j >= 0 and A[j] > key:
            A[j+1] = A[j]
            j -= 1
        A[j+1] = key
```

**Detailed Analysis**:

**Best Case**: Already sorted
```
Comparisons: n-1 (one per element)
Time: Θ(n)
```

**Worst Case**: Reverse sorted
```
Comparisons: 1 + 2 + ... + (n-1) = n(n-1)/2
Shifts: same as comparisons
Time: Θ(n²)
```

**Average Case**: Random permutation
```
Expected comparisons per element: i/2
Total = Σ(i/2) from i=1 to n-1
      = (1/2)·n(n-1)/2
      = Θ(n²)
```

**Loop Invariant Proof**:

**Invariant**: At start of iteration i, subarray A[0..i-1] contains elements originally in A[0..i-1] but in sorted order.

**Initialization**: i=1, A[0..0] is trivially sorted ✓

**Maintenance**: Inner loop inserts A[i] into correct position in A[0..i-1], making A[0..i] sorted ✓

**Termination**: i=n, A[0..n-1] is sorted ✓

**When to use**: 
- Small arrays (n < 10-50)
- Nearly sorted data
- Online algorithms (sorting as data arrives)
- Adaptive to existing order

**Binary Insertion Sort Optimization**:
```python
def binaryInsertionSort(A):
    for i in range(1, len(A)):
        key = A[i]
        # Binary search for insertion position
        pos = bisect_left(A, key, 0, i)
        # Shift elements
        A[pos+1:i+1] = A[pos:i]
        A[pos] = key

# Comparisons: O(n log n)
# Moves: still O(n²)
# Useful when comparisons are expensive
```

### 2.2.3 Bubble Sort

**Algorithm**:
```python
def bubbleSort(A):
    n = len(A)
    for i in range(n):
        swapped = False
        for j in range(n-1-i):
            if A[j] > A[j+1]:
                A[j], A[j+1] = A[j+1], A[j]
                swapped = True
        if not swapped:
            break  # Already sorted
```

**Analysis**:
```
Best Case (sorted): Θ(n) with optimization
Worst Case: Θ(n²)
Average Case: Θ(n²)

Comparisons = n(n-1)/2
Swaps (worst) = n(n-1)/2

Stable: Yes
In-place: Yes
```

**Rarely used in practice due to poor performance**

## 2.3 Quick Sort - The Workhorse

### 2.3.1 Basic Quick Sort

**Algorithm**:
```python
def quickSort(A, low, high):
    if low < high:
        pi = partition(A, low, high)
        quickSort(A, low, pi-1)
        quickSort(A, pi+1, high)

def partition(A, low, high):
    # Lomuto partition scheme
    pivot = A[high]
    i = low - 1  # Boundary of smaller elements
    
    for j in range(low, high):
        if A[j] <= pivot:
            i += 1
            A[i], A[j] = A[j], A[i]
    
    A[i+1], A[high] = A[high], A[i+1]
    return i + 1
```

**Partition Invariant**:
```
At each step:
A[low..i] ≤ pivot
A[i+1..j-1] > pivot
A[j..high-1] = unprocessed
A[high] = pivot
```

**Example of Partition**:
```
Array: [3, 7, 8, 5, 2, 1, 9, 6, 4]
Pivot = 4 (last element)

j=0: A[0]=3 ≤ 4, i++, swap A[0]↔A[0] → [3, 7, 8, 5, 2, 1, 9, 6, 4], i=0
j=1: A[1]=7 > 4, no swap              → [3, 7, 8, 5, 2, 1, 9, 6, 4], i=0
j=2: A[2]=8 > 4, no swap              → [3, 7, 8, 5, 2, 1, 9, 6, 4], i=0
j=3: A[3]=5 > 4, no swap              → [3, 7, 8, 5, 2, 1, 9, 6, 4], i=0
j=4: A[4]=2 ≤ 4, i++, swap A[1]↔A[4] → [3, 2, 8, 5, 7, 1, 9, 6, 4], i=1
j=5: A[5]=1 ≤ 4, i++, swap A[2]↔A[5] → [3, 2, 1, 5, 7, 8, 9, 6, 4], i=2
j=6: A[6]=9 > 4, no swap              → [3, 2, 1, 5, 7, 8, 9, 6, 4], i=2
j=7: A[7]=6 > 4, no swap              → [3, 2, 1, 5, 7, 8, 9, 6, 4], i=2

Final: swap A[3]↔A[8] → [3, 2, 1, 4, 7, 8, 9, 6, 5]
                           ≤4      4      >4

Return pi=3
```

### 2.3.2 Hoare Partition Scheme (Alternative)

**Algorithm**:
```python
def hoarePartition(A, low, high):
    pivot = A[low]
    i = low - 1
    j = high + 1
    
    while True:
        # Find element >= pivot from left
        i += 1
        while A[i] < pivot:
            i += 1
        
        # Find element <= pivot from right
        j -= 1
        while A[j] > pivot:
            j -= 1
        
        if i >= j:
            return j
        
        A[i], A[j] = A[j], A[i]
```

**Advantages of Hoare**:
- Fewer swaps on average (3 times fewer)
- More efficient on average
- Works better with equal elements

### 2.3.3 Time Complexity Analysis

**Worst Case: Θ(n²)**

Occurs when pivot is always smallest or largest element.

**Recurrence**:
```
T(n) = T(n-1) + T(0) + Θ(n)
     = T(n-1) + Θ(n)

Solving:
T(n) = Θ(n) + Θ(n-1) + ... + Θ(1)
     = Θ(n²)
```

**Example worst case**: Already sorted array
```
[1, 2, 3, 4, 5] with pivot = last element
Partition 1: [1,2,3,4] | 5     (n comparisons)
Partition 2: [1,2,3] | 4       (n-1 comparisons)
...
Total = n + (n-1) + ... + 1 = Θ(n²)
```

**Best Case: Θ(n log n)**

Occurs when pivot always divides array into two equal halves.

**Recurrence**:
```
T(n) = 2T(n/2) + Θ(n)

By Master Theorem (Case 2):
T(n) = Θ(n log n)
```

**Recursion Tree**:
```
                    n                    Level 0: n
                /       \
            n/2           n/2            Level 1: n
           /  \          /  \
         n/4  n/4      n/4  n/4          Level 2: n
         ...

Height = log n
Work per level = n
Total = n log n
```

### 2.3.4 Average Case Analysis (DETAILED DERIVATION)

**Assume**: All permutations equally likely.

Let T(n) = expected number of comparisons to sort n elements.

**Key Insight**: Partition uses (n-1) comparisons. After partition with pivot in position k, we have subarrays of size k-1 and n-k.

**Recurrence**:
```
T(n) = (n-1) + (1/n)·Σ[T(k-1) + T(n-k)] for k=1 to n

The (1/n) accounts for equal probability of each position.
```

**Simplification**:
```
T(n) = (n-1) + (1/n)·Σ[T(k-1) + T(n-k)]
     = (n-1) + (2/n)·Σ T(k-1)  from k=1 to n
     = (n-1) + (2/n)·Σ T(i)     from i=0 to n-1

Multiply by n:
n·T(n) = n(n-1) + 2·Σ T(i)   from i=0 to n-1   ...(1)

Similarly for n-1:
(n-1)·T(n-1) = (n-1)(n-2) + 2·Σ T(i)  from i=0 to n-2   ...(2)

Subtract (2) from (1):
n·T(n) - (n-1)·T(n-1) = n(n-1) - (n-1)(n-2) + 2T(n-1)
n·T(n) = (n-1)·T(n-1) + n(n-1) - (n-1)(n-2) + 2T(n-1)
n·T(n) = (n+1)·T(n-1) + 2(n-1)

Divide by n(n+1):
T(n)/(n+1) = T(n-1)/n + 2(n-1)/[n(n+1)]

Let S(n) = T(n)/(n+1):
S(n) = S(n-1) + 2(n-1)/[n(n+1)]

Telescoping sum:
S(n) = S(1) + Σ 2(k-1)/[k(k+1)]  from k=2 to n

S(1) = T(1)/2 = 0

Simplify the sum using partial fractions:
2(k-1)/[k(k+1)] = 2/k - 2/(k+1) + 2/[k(k+1)]
                ≈ 2/k - 2/(k+1)  (dominant terms)

S(n) ≈ 2[1/2 - 1/3 + 1/3 - 1/4 + ... + 1/n - 1/(n+1)]
     = 2[1/2 - 1/(n+1)]
     < 2

Actually, more careful analysis:
S(n) = 2(Hₙ₊₁ - 3/2)  where Hₙ is harmonic number

Therefore:
T(n) = (n+1)·S(n)
     = 2(n+1)(Hₙ₊₁ - 3/2)
     ≈ 2(n+1)ln(n)
     = 1.39n log₂ n  (in base-2 comparisons)
```

**Conclusion**: Average case is Θ(n log n), only ~39% more comparisons than optimal merge sort!

### 2.3.5 Randomized Quick Sort

**Algorithm**:
```python
def randomizedQuickSort(A, low, high):
    if low < high:
        pi = randomizedPartition(A, low, high)
        randomizedQuickSort(A, low, pi-1)
        randomizedQuickSort(A, pi+1, high)

def randomizedPartition(A, low, high):
    # Choose random pivot
    i = random.randint(low, high)
    A[i], A[high] = A[high], A[i]
    return partition(A, low, high)
```

**Expected Time: O(n log n)**

**Detailed Proof using Indicator Random Variables**:

Let X = total number of comparisons.

For elements zᵢ (ith smallest), zⱼ (jth smallest) where i < j:
```
Xᵢⱼ = I{zᵢ compared with zⱼ}

X = Σ Σ Xᵢⱼ  (i=1 to n-1, j=i+1 to n)

E[X] = Σ Σ E[Xᵢⱼ]
     = Σ Σ P(zᵢ compared with zⱼ)
```

**Key Question**: When are zᵢ and zⱼ compared?

**Answer**: They are compared if and only if one of them is chosen as pivot before any element between them (zᵢ₊₁, ..., zⱼ₋₁).

**Probability Calculation**:
```
Consider set S = {zᵢ, zᵢ₊₁, ..., zⱼ}
|S| = j - i + 1

zᵢ and zⱼ are compared iff one is chosen as pivot first from S.

P(zᵢ or zⱼ chosen first) = 2/(j-i+1)

Therefore:
E[Xᵢⱼ] = 2/(j-i+1)
```

**Complete Summation**:
```
E[X] = Σ(i=1 to n-1) Σ(j=i+1 to n) 2/(j-i+1)

Substitute k = j - i:
     = Σ(i=1 to n-1) Σ(k=1 to n-i) 2/(k+1)
     = Σ(i=1 to n-1) 2·[Σ(k=1 to n-i) 1/(k+1)]
     
Upper bound by extending sum:
     < Σ(i=1 to n-1) 2·[Σ(k=2 to n) 1/k]
     = (n-1)·2·(Hₙ - 1)
     ≈ 2n ln(n)
     = O(n log n)

More precise:
E[X] = Σ(i=1 to n-1) Σ(k=1 to n-i) 2/(k+1)

Change order of summation:
     = Σ(k=1 to n-1) Σ(i=1 to n-k) 2/(k+1)
     = Σ(k=1 to n-1) (n-k)·2/(k+1)
     = 2Σ(k=1 to n-1) (n-k)/(k+1)
     = 2Σ(m=2 to n) (n-m+1)/m  (substitute m=k+1)
     = 2Σ(m=2 to n) [n/m - 1 + 1/m]
     = 2n·Hₙ - 2(n-1) + 2(Hₙ-1)
     = 2(n+1)Hₙ - 2n - 2
     ≈ 2(n+1)ln(n) - 2n
     = 1.39n lg n - 1.38n  (in base-2)
```

**This is the same as deterministic average case!** Randomization helps avoid worst case on adversarial input.

### 2.3.6 Three-Way Partitioning (Dutch National Flag)

**Problem**: Many duplicate keys cause poor performance.

**Solution**: Partition into three parts: < pivot, = pivot, > pivot

**Algorithm**:
```python
def threeWayPartition(A, low, high):
    if high <= low:
        return
    
    lt = low          # A[low..lt-1] < pivot
    i = low + 1       # A[lt..i-1] = pivot
    gt = high         # A[gt+1..high] > pivot
    pivot = A[low]
    
    while i <= gt:
        if A[i] < pivot:
            A[lt], A[i] = A[i], A[lt]
            lt += 1
            i += 1
        elif A[i] > pivot:
            A[i], A[gt] = A[gt], A[i]
            gt -= 1
        else:  # A[i] == pivot
            i += 1
    
    return lt, gt

def threeWayQuickSort(A, low, high):
    if high <= low:
        return
    
    lt, gt = threeWayPartition(A, low, high)
    threeWayQuickSort(A, low, lt-1)
    threeWayQuickSort(A, gt+1, high)
```

**Time Complexity**: O(n) when all elements equal!

**Example**:
```
Array: [4, 9, 4, 4, 1, 9, 4, 4, 9, 4, 4, 1, 4]
Pivot: 4

After partition:
[1, 1, 4, 4, 4, 4, 4, 4, 4, 4, 9, 9, 9]
        ↑lt          gt↑

Only need to sort [1,1] and [9,9,9]
```

---

## PRACTICE PROBLEMS - CHAPTER 2A

### Problem 2.1: Insertion Sort Variants
**Q**: Prove that insertion sort makes at most n-1 swaps if there is exactly one inversion.

### Problem 2.2: Quick Sort Pivot Selection
**Q**: Show that selecting median-of-three (first, middle, last) as pivot guarantees O(n log n) on sorted array.

### Problem 2.3: K-way Partition
**Q**: Extend three-way partition to partition array into k groups around k-1 pivots. Analyze complexity.

### Problem 2.4: Quick Sort Depth
**Q**: What is the expected depth of recursion tree in randomized Quick Sort?

**Hint**: Use fact that expected height of random binary search tree is O(log n).

### Problem 2.5: Comparisons Lower Bound
**Q**: Prove that finding both minimum and maximum requires at least ⌈3n/2⌉ - 2 comparisons.

**Solution Sketch**: 
- Process elements in pairs
- Compare within pair (n/2 comparisons)
- Compare larger with max (n/2 - 1 comparisons)
- Compare smaller with min (n/2 - 1 comparisons)
- Total: n/2 + 2(n/2 - 1) = 3n/2 - 2

---

## 2.4 Merge Sort - Divide and Conquer

### 2.4.1 Basic Merge Sort

**Algorithm**:
```python
def mergeSort(A, left, right):
    if left < right:
        mid = (left + right) // 2
        mergeSort(A, left, mid)
        mergeSort(A, mid+1, right)
        merge(A, left, mid, right)

def merge(A, left, mid, right):
    # Create temporary arrays
    L = A[left:mid+1]
    R = A[mid+1:right+1]
    
    i = j = 0
    k = left
    
    # Merge L and R back into A[left..right]
    while i < len(L) and j < len(R):
        if L[i] <= R[j]:
            A[k] = L[i]
            i += 1
        else:
            A[k] = R[j]
            j += 1
        k += 1
    
    # Copy remaining elements
    while i < len(L):
        A[k] = L[i]
        i += 1
        k += 1
    
    while j < len(R):
        A[k] = R[j]
        j += 1
        k += 1
```

**Detailed Example**:
```
Array: [38, 27, 43, 3, 9, 82, 10]

Split Phase:
[38, 27, 43, 3, 9, 82, 10]
    ↓
[38, 27, 43, 3] [9, 82, 10]
    ↓               ↓
[38, 27] [43, 3]   [9, 82] [10]
  ↓        ↓         ↓       ↓
[38][27] [43][3]   [9][82] [10]

Merge Phase:
[27, 38] [3, 43]   [9, 82] [10]
    ↓               ↓
[3, 27, 38, 43]   [9, 10, 82]
         ↓
[3, 9, 10, 27, 38, 43, 82]
```

### 2.4.2 Time Complexity Analysis

**Recurrence Relation**:
```
T(n) = 2T(n/2) + Θ(n)
       ↑         ↑
    Two recursive calls
    on n/2 elements   Merge takes Θ(n)

Base case: T(1) = Θ(1)
```

**Method 1: Recursion Tree**
```
                        cn                     Level 0: cn
                    /        \
                cn/2          cn/2              Level 1: cn
               /    \        /    \
            cn/4   cn/4   cn/4   cn/4           Level 2: cn
            ...
```

```
Height of tree = lg n
Work per level = cn
Total work = cn · lg n = Θ(n log n)
```

**Method 2: Master Theorem**
```
T(n) = 2T(n/2) + Θ(n)

a = 2, b = 2, f(n) = Θ(n)
n^(log_b a) = n^(log_2 2) = n^1 = n

f(n) = Θ(n^1 · log^0 n)

This is Case 2 with k=0:
T(n) = Θ(n^1 · log^(0+1) n) = Θ(n log n)
```

**Method 3: Substitution**
```
Guess: T(n) = cn log n

Inductive hypothesis: T(k) ≤ ck log k for all k < n

T(n) = 2T(n/2) + cn
     ≤ 2 · c(n/2) log(n/2) + cn
     = cn log(n/2) + cn
     = cn(log n - log 2) + cn
     = cn log n - cn + cn
     = cn log n ✓
```

**All Cases**: Best = Average = Worst = Θ(n log n)

### 2.4.3 Space Complexity

**Naive implementation**: O(n) auxiliary space for temporary arrays

**Optimization attempts**:
1. **In-place merge**: Possible but O(n²) time
2. **Using insertions**: Not practical
3. **Linked list**: Natural in-place, but loses cache locality

**Stack space for recursion**: O(log n) due to tree height

### 2.4.4 Merge Sort vs Quick Sort

| Feature | Merge Sort | Quick Sort |
|---------|------------|------------|
| Worst case | Θ(n log n) | Θ(n²) |
| Average case | Θ(n log n) | Θ(n log n) |
| Space | O(n) | O(log n) |
| Stable | Yes | No |
| Cache performance | Poor | Good |
| Practical performance | Good | Excellent |
| Parallelization | Easy | Hard |

**When to use Merge Sort**:
- Stability required
- Worst-case guarantee needed
- External sorting (data doesn't fit in memory)
- Linked lists
- Parallel sorting

**When to use Quick Sort**:
- Average case expected
- In-place sorting needed
- Good cache performance important
- Arrays (random access)

### 2.4.5 Bottom-Up Merge Sort

**Algorithm** (Iterative):
```python
def bottomUpMergeSort(A):
    n = len(A)
    size = 1  # Current subarray size
    
    while size < n:
        # Pick starting index of left subarray
        left = 0
        while left < n:
            mid = min(left + size - 1, n - 1)
            right = min(left + 2*size - 1, n - 1)
            
            if mid < right:
                merge(A, left, mid, right)
            
            left += 2 * size
        
        size *= 2
```

**Advantages**:
- No recursion overhead
- Better for small datasets
- Easier to analyze cache behavior

**Example**:
```
Array: [38, 27, 43, 3, 9, 82, 10]

Size = 1:
Merge [38][27] → [27, 38]
Merge [43][3]  → [3, 43]
Merge [9][82]  → [9, 82]
[10] alone
Result: [27, 38, 3, 43, 9, 82, 10]

Size = 2:
Merge [27,38][3,43] → [3, 27, 38, 43]
Merge [9,82][10]    → [9, 10, 82]
Result: [3, 27, 38, 43, 9, 10, 82]

Size = 4:
Merge [3,27,38,43][9,10,82] → [3,9,10,27,38,43,82]
```

### 2.4.6 Natural Merge Sort

**Idea**: Exploit existing runs (sorted subsequences) in data

**Algorithm**:
```python
def naturalMergeSort(A):
    while True:
        runs = findRuns(A)  # Find sorted subsequences
        if len(runs) == 1:
            break  # Array is sorted
        
        # Merge adjacent runs
        mergeRuns(A, runs)

def findRuns(A):
    runs = []
    start = 0
    
    for i in range(1, len(A)):
        if A[i] < A[i-1]:  # Run ends
            runs.append((start, i-1))
            start = i
    
    runs.append((start, len(A)-1))
    return runs
```

**Performance**: O(n log k) where k = number of runs
- Already sorted: O(n)
- Random: O(n log n)

### 2.4.7 Counting Inversions

**Problem**: Count pairs (i,j) where i < j but A[i] > A[j]

**Application**: Measuring "sortedness", collaborative filtering

**Algorithm** (Modified Merge Sort):
```python
def countInversions(A):
    if len(A) <= 1:
        return A, 0
    
    mid = len(A) // 2
    left, left_inv = countInversions(A[:mid])
    right, right_inv = countInversions(A[mid:])
    merged, split_inv = mergeAndCount(left, right)
    
    return merged, left_inv + right_inv + split_inv

def mergeAndCount(L, R):
    i = j = 0
    inversions = 0
    result = []
    
    while i < len(L) and j < len(R):
        if L[i] <= R[j]:
            result.append(L[i])
            i += 1
        else:
            result.append(R[j])
            inversions += len(L) - i  # All remaining in L are > R[j]
            j += 1
    
    result.extend(L[i:])
    result.extend(R[j:])
    
    return result, inversions
```

**Example**:
```
Array: [2, 4, 1, 3, 5]

Split: [2, 4] and [1, 3, 5]

Left [2, 4]: 0 inversions (sorted)
Right [1, 3, 5]: 0 inversions (sorted)

Merge:
- Compare 2 and 1: 1 > 2, add 1, inversions += 2 (both 2,4 > 1)
- Compare 2 and 3: 2 < 3, add 2
- Compare 4 and 3: 3 < 4, add 3, inversions += 1 (4 > 3)
- Add remaining: 4, 5

Total inversions: 3
Pairs: (2,1), (4,1), (4,3)
```

**Time Complexity**: O(n log n)

---

## 2.5 Lower Bound for Comparison-Based Sorting

### 2.5.1 Decision Tree Model

**Key Idea**: Any comparison-based algorithm can be represented as a binary decision tree.

**Decision Tree Properties**:
- **Internal nodes**: Comparisons (e.g., A[i] ≤ A[j]?)
- **Leaves**: Permutations (sorted order)
- **Path from root to leaf**: Sequence of comparisons leading to sorted array
- **Height h**: Worst-case number of comparisons

**Example** (n=3 elements):
```
                     A[0]:A[1]?
                    /          \
                 ≤               >
                /                  \
           A[1]:A[2]?          A[0]:A[2]?
          /        \           /        \
        ≤           >        ≤           >
       /            \       /            \
   (0,1,2)      A[0]:A[2]? A[1]:A[2]? (2,0,1)
                /      \    /      \
              ≤         > ≤         >
             /          |  |         \
        (0,2,1)    (2,1,0)(1,2,0) (1,0,2)
```

### 2.5.2 Lower Bound Proof

**Theorem**: Any comparison-based sorting algorithm requires Ω(n log n) comparisons in the worst case.

**Proof**:

**Step 1**: Number of leaves must be at least n!
- Each permutation must appear as a leaf
- n! possible permutations of n elements

**Step 2**: Binary tree with L leaves has height h ≥ log₂ L
- Binary tree with height h has at most 2^h leaves
- Therefore: 2^h ≥ L
- Taking log: h ≥ log₂ L

**Step 3**: Apply to decision tree
```
h ≥ log₂(n!)
```

**Step 4**: Use Stirling's approximation
```
n! ≈ √(2πn) · (n/e)^n

log₂(n!) = log₂[√(2πn) · (n/e)^n]
         = log₂√(2πn) + log₂(n/e)^n
         = (1/2)log₂(2πn) + n log₂(n/e)
         = (1/2)log₂(2πn) + n log₂ n - n log₂ e
         = (1/2)log₂(2πn) + n log₂ n - n/ln 2
```

**Step 5**: Simplify
```
log₂(n!) = n log₂ n - n/ln 2 + (1/2)log₂(2πn)
         = n log₂ n - 1.443n + O(log n)
         = Θ(n log n)
```

**Alternative (simpler) proof**:
```
n! = n · (n-1) · (n-2) · ... · 2 · 1
   > n · (n-1) · ... · (n/2)     (drop half the terms)
   > (n/2)^(n/2)                 (each term ≥ n/2)

log(n!) > log[(n/2)^(n/2)]
        = (n/2) log(n/2)
        = (n/2)(log n - 1)
        = Θ(n log n)
```

**Conclusion**: 
- h = Ω(n log n)
- Any comparison-based sorting algorithm must make Ω(n log n) comparisons in worst case
- Merge sort, heap sort achieve this bound (optimal!)

### 2.5.3 Implications

**Optimality**:
- Merge Sort: Θ(n log n) worst case → **Optimal**
- Heap Sort: Θ(n log n) worst case → **Optimal**
- Quick Sort: Θ(n²) worst case → Not optimal, but Θ(n log n) average

**Cannot do better** with comparisons alone!

**To beat O(n log n)**:
- Must use properties beyond comparisons
- Examples: Counting sort, Radix sort, Bucket sort
- These assume additional structure (integers, keys in range, distribution)

---

## 2.6 Heap Sort

### 2.6.1 Binary Heap Review

**Max-Heap Property**: A[parent(i)] ≥ A[i] for all nodes i

**Array Representation**:
```
parent(i) = ⌊i/2⌋
left(i) = 2i
right(i) = 2i + 1
```

**Example Heap**:
```
Array: [16, 14, 10, 8, 7, 9, 3, 2, 4, 1]

Tree representation:
           16
         /    \
       14      10
      /  \    /  \
     8    7  9    3
    / \  /
   2  4 1
```

### 2.6.2 Heap Operations

**MAX-HEAPIFY** - Maintain heap property:
```python
def maxHeapify(A, i, heap_size):
    l = 2 * i + 1  # left child
    r = 2 * i + 2  # right child
    largest = i
    
    if l < heap_size and A[l] > A[largest]:
        largest = l
    if r < heap_size and A[r] > A[largest]:
        largest = r
    
    if largest != i:
        A[i], A[largest] = A[largest], A[i]
        maxHeapify(A, largest, heap_size)
```

**Time**: O(h) = O(log n) where h is height of node

**Example**:
```
Array: [16, 4, 10, 14, 7, 9, 3, 2, 8, 1]
maxHeapify(A, 1):  # Fix node at index 1 (value 4)

           16
         /    \
      → 4      10
       / \    /  \
     14   7  9    3
    / \  /
   2  8 1

Step 1: Compare 4 with children 14 and 7
        Largest = 14 (index 3)
        Swap: [16, 14, 10, 4, 7, 9, 3, 2, 8, 1]

           16
         /    \
       14      10
       / \    /  \
    → 4   7  9    3
     / \  /
    2  8 1

Step 2: Compare 4 with children 2 and 8
        Largest = 8 (index 8)
        Swap: [16, 14, 10, 8, 7, 9, 3, 2, 4, 1]

           16
         /    \
       14      10
       / \    /  \
      8   7  9    3
     / \  /
    2  4 1

Done: Heap property restored
```

**BUILD-MAX-HEAP**:
```python
def buildMaxHeap(A):
    n = len(A)
    # Start from last non-leaf node
    for i in range(n//2 - 1, -1, -1):
        maxHeapify(A, i, n)
```

**Time Complexity Analysis** (Tight bound):

**Intuition**: Not all nodes have height log n

```
Number of nodes at height h: ≤ ⌈n/2^(h+1)⌉

Cost of heapifying node at height h: O(h)

Total cost:
T(n) = Σ (nodes at height h) · O(h)
     = Σ ⌈n/2^(h+1)⌉ · O(h)  for h=0 to ⌊lg n⌋

Upper bound:
T(n) ≤ Σ (n/2^(h+1)) · c·h  for h=0 to ∞
     = cn · Σ h/2^(h+1)
     = cn · Σ h/2^h · (1/2)
     = (cn/2) · Σ h/2^h

We need: Σ h/2^h = ?
```

**Lemma**: Σ(h=0 to ∞) h·x^h = x/(1-x)² for |x| < 1

**Proof**:
```
Start with: Σ x^h = 1/(1-x)  for |x| < 1

Differentiate both sides:
Σ h·x^(h-1) = 1/(1-x)²

Multiply by x:
Σ h·x^h = x/(1-x)²
```

**Apply with x = 1/2**:
```
Σ h/2^h = (1/2)/(1-1/2)² = (1/2)/(1/4) = 2

Therefore:
T(n) ≤ (cn/2) · 2 = cn = O(n)
```

**BUILD-MAX-HEAP is O(n)!** (Not O(n log n) as naive analysis suggests)

### 2.6.3 Heap Sort Algorithm

**Algorithm**:
```python
def heapSort(A):
    # Build max heap: O(n)
    buildMaxHeap(A)
    
    heap_size = len(A)
    # Extract max n-1 times: O(n log n)
    for i in range(len(A)-1, 0, -1):
        # Swap max to end
        A[0], A[i] = A[i], A[0]
        heap_size -= 1
        # Restore heap property
        maxHeapify(A, 0, heap_size)
```

**Detailed Example**:
```
Array: [4, 1, 3, 2, 16, 9, 10, 14, 8, 7]

Step 1: Build max heap
[16, 14, 10, 8, 7, 9, 3, 2, 4, 1]

Step 2: Extract-max iterations
i=9: Swap 16↔1, heapify(0)
     [14, 8, 10, 4, 7, 9, 3, 2, 1, |16]
     
i=8: Swap 14↔1, heapify(0)
     [10, 8, 9, 4, 7, 1, 3, 2, |14, 16]
     
i=7: Swap 10↔2, heapify(0)
     [9, 8, 3, 4, 7, 1, 2, |10, 14, 16]
     
i=6: Swap 9↔2, heapify(0)
     [8, 7, 3, 4, 2, 1, |9, 10, 14, 16]
     
... continuing ...

Final: [1, 2, 3, 4, 7, 8, 9, 10, 14, 16]
```

**Time Complexity**:
- Build heap: O(n)
- n-1 extract-max: each O(log n)
- **Total: O(n log n)** in all cases

**Space Complexity**: O(1) (in-place)

**Properties**:
- **Not stable** (can swap equal elements out of order)
- **In-place**
- **O(n log n) worst case** (optimal!)
- Poor cache performance (random access)

### 2.6.4 Priority Queue Application

Heap Sort demonstrates heap's efficiency for priority queues:

```python
class MaxPriorityQueue:
    def __init__(self):
        self.heap = []
    
    def insert(self, key):  # O(log n)
        self.heap.append(float('-inf'))
        self.increaseKey(len(self.heap)-1, key)
    
    def maximum(self):  # O(1)
        return self.heap[0]
    
    def extractMax(self):  # O(log n)
        if len(self.heap) < 1:
            raise Exception("Heap underflow")
        max_val = self.heap[0]
        self.heap[0] = self.heap[-1]
        self.heap.pop()
        maxHeapify(self.heap, 0, len(self.heap))
        return max_val
    
    def increaseKey(self, i, key):  # O(log n)
        if key < self.heap[i]:
            raise Exception("New key smaller than current")
        self.heap[i] = key
        # Bubble up
        while i > 0 and self.heap[parent(i)] < self.heap[i]:
            self.heap[i], self.heap[parent(i)] = self.heap[parent(i)], self.heap[i]
            i = parent(i)
```

**Applications**:
- Event-driven simulation
- Dijkstra's shortest path
- Prim's MST
- Huffman coding
- Job scheduling

---

## PRACTICE PROBLEMS - CHAPTER 2B

### Problem 2.6: Merge Sort Space Optimization
**Q**: Can merge sort be done in O(1) space while maintaining O(n log n) time?

**Solution**: No for arrays (proven). Yes for linked lists with careful implementation.

### Problem 2.7: K-sorted Array
**Q**: Array where each element is at most k positions from its target position. Design O(n log k) sorting algorithm.

**Solution**: Use min-heap of size k+1. Insert first k+1 elements, then repeatedly extract-min and insert next element.

### Problem 2.8: Heap Property Violation
**Q**: In array representing heap, exactly one element violates heap property. Fix in O(log n).

**Solution**: Find violating element, then either bubble-up or heapify-down depending on comparison with parent.

### Problem 2.9: Median in Heap
**Q**: Can we find median in max-heap in O(1) time?

**Answer**: No. Median can be anywhere in heap. Need O(n) to find it.

### Problem 2.10: k-th Smallest in Heap
**Q**: Find k-th smallest element in min-heap of size n in O(k log k) time.

**Solution**: Use auxiliary min-heap. Start with root, repeatedly extract-min and add its children to auxiliary heap. k-th extraction gives answer.

---

# CHAPTER 3: Selection and Order Statistics

## 3.1 Selection Problem

**Problem**: Given array A of n distinct elements and integer k (1 ≤ k ≤ n), find k-th smallest element.

**Special Cases**:
- k = 1: Minimum
- k = n: Maximum
- k = ⌊n/2⌋: Median

**Applications**:
- Finding percentiles
- Robust statistics
- Outlier detection
- Partitioning data

## 3.2 Simple Solutions

### 3.2.1 Sorting-Based
```python
def select(A, k):
    A.sort()
    return A[k-1]
```
**Time**: O(n log n)

### 3.2.2 Partial Sorting
Keep k smallest elements in sorted order:
```python
def selectPartial(A, k):
    # Use min-heap
    import heapq
    return heapq.nsmallest(k, A)[k-1]
```
**Time**: O(n log k)

### 3.2.3 Two-pass Minimum/Maximum
```python
def findMinMax(A):
    min_val = max_val = A[0]
    for x in A[1:]:
        if x < min_val:
            min_val = x
        elif x > max_val:
            max_val = x
    return min_val, max_val
```
**Comparisons**: At most 2n - 2

**Optimized** (compare pairs first):
```python
def findMinMaxOptimized(A):
    n = len(A)
    if n % 2 == 0:
        if A[0] < A[1]:
            min_val, max_val = A[0], A[1]
        else:
            min_val, max_val = A[1], A[0]
        start = 2
    else:
        min_val = max_val = A[0]
        start = 1
    
    for i in range(start, n-1, 2):
        if A[i] < A[i+1]:
            small, large = A[i], A[i+1]
        else:
            small, large = A[i+1], A[i]
        
        if small < min_val:
            min_val = small
        if large > max_val:
            max_val = large
    
    return min_val, max_val
```
**Comparisons**: ⌈3n/2⌉ - 2 (optimal!)

**Proof of optimality**: See Problem 2.5

## 3.3 Randomized Selection (QuickSelect)

### 3.3.1 Algorithm

```python
def randomizedSelect(A, left, right, k):
    """
    Find k-th smallest element (k is 1-indexed)
    """
    if left == right:
        return A[left]
    
    # Partition around random pivot
    pivot_index = randomizedPartition(A, left, right)
    
    # k-th smallest is at position k-1 (0-indexed)
    position = pivot_index - left + 1
    
    if k == position:
        return A[pivot_index]
    elif k < position:
        return randomizedSelect(A, left, pivot_index-1, k)
    else:
        return randomizedSelect(A, pivot_index+1, right, k-position)
```

**Key Difference from Quick Sort**:
- Quick Sort recurses on both sides
- Quick Select recurses on only one side

### 3.3.2 Expected Time Analysis

**Best Case**: O(n) when pivot always splits 50-50

**Worst Case**: O(n²) when pivot always smallest/largest

**Average Case** (detailed derivation):

Let T(n) = expected time to find k-th element in array of size n.

After partition with pivot at rank i:
- If i = k: Done
- If i > k: Recurse on left subarray of size i-1
- If i < k: Recurse on right subarray of size n-i

```
T(n) ≤ T(max(i-1, n-i)) + O(n)  for random i

Expected time:
E[T(n)] = (1/n) · Σ T(max(i-1, n-i)) + O(n)  for i=1 to n
```

**Key Insight**: For any k, at least half the pivots give "good" split (at most 3n/4 elements on larger side)

**Proof**:
```
"Good" pivot i satisfies:
max(i-1, n-i) ≤ 3n/4

This means:
n/4 ≤ i ≤ 3n/4

Number of good pivots: n/2
Probability of good pivot: 1/2

Expected time until good pivot: 2 (geometric distribution)
```

**Recurrence with good pivots**:
```
T(n) ≤ 2 · n + T(3n/4)
     ≤ 2n + 2(3n/4) + T((3/4)²n)
     ≤ 2n[1 + 3/4 + (3/4)² + ...]
     = 2n · 1/(1-3/4)
     = 2n · 4
     = 8n
     = O(n)
```

**More Precise Analysis**:

Using indicator random variables (similar to Quick Sort):

```
Let X = number of comparisons

Xᵢⱼ = I{elements i and j are compared}

E[X] = Σ Σ E[Xᵢⱼ]
     = Σ Σ P(i and j compared)

For selecting k-th element:
P(i and j compared) depends on whether k is between i and j

Careful analysis gives:
E[X] ≤ 4n
```

### 3.3.3 Practical Implementation

```python
import random

def quickSelect(A, k):
    """Find k-th smallest (1-indexed) in-place"""
    return quickSelectHelper(A, 0, len(A)-1, k-1)

def quickSelectHelper(A, left, right, k):
    if left == right:
        return A[left]
    
    # Partition
    pivot_idx = random.randint(left, right)
    A[pivot_idx], A[right] = A[right], A[pivot_idx]
    
    # Lomuto partition
    pivot = A[right]
    i = left - 1
    for j in range(left, right):
        if A[j] <= pivot:
            i += 1
            A[i], A[j] = A[j], A[i]
    A[i+1], A[right] = A[right], A[i+1]
    pivot_idx = i + 1
    
    if k == pivot_idx:
        return A[k]
    elif k < pivot_idx:
        return quickSelectHelper(A, left, pivot_idx-1, k)
    else:
        return quickSelectHelper(A, pivot_idx+1, right, k)
```

**Example**:
```
Array: [3, 2, 1, 5, 4], k=3 (find 3rd smallest)

Initial: A=[3,2,1,5,4], left=0, right=4
Choose pivot=4 (index 4)
After partition: [3,2,1,4,5], pivot_idx=3
k=2 < pivot_idx=3 → recurse left

A=[3,2,1], left=0, right=2, k=2
Choose pivot=1 (index 2)
After partition: [1,2,3], pivot_idx=0
k=2 > pivot_idx=0 → recurse right

A=[2,3], left=1, right=2, k=2
Choose pivot=3 (index 2)
After partition: [2,3], pivot_idx=2
k=2 == pivot_idx=2 → return A[2]=3 ✓
```

## 3.4 Deterministic Linear-Time Selection

### 3.4.1 The Median-of-Medians Algorithm

**Goal**: Select k-th element in O(n) time worst-case (not just expected).

**Key Idea**: Choose pivot that guarantees good split!

**Algorithm** (SELECT):
```python
def deterministicSelect(A, left, right, k):
    """
    Find k-th smallest element (0-indexed)
    Guaranteed O(n) worst-case time
    """
    n = right - left + 1
    
    # Base case: small array
    if n <= 5:
        A[left:right+1] = sorted(A[left:right+1])
        return A[left + k]
    
    # Step 1: Divide into groups of 5
    groups = []
    for i in range(left, right+1, 5):
        group_end = min(i+4, right)
        groups.append(A[i:group_end+1])
    
    # Step 2: Find median of each group
    medians = []
    for group in groups:
        group.sort()
        medians.append(group[len(group)//2])
    
    # Step 3: Find median of medians recursively
    mom = deterministicSelect(medians, 0, len(medians)-1, len(medians)//2)
    
    # Step 4: Partition around median-of-medians
    pivot_idx = partition_around_pivot(A, left, right, mom)
    
    # Step 5: Recurse on appropriate side
    pivot_pos = pivot_idx - left
    if k == pivot_pos:
        return A[pivot_idx]
    elif k < pivot_pos:
        return deterministicSelect(A, left, pivot_idx-1, k)
    else:
        return deterministicSelect(A, pivot_idx+1, right, k-pivot_pos-1)

def partition_around_pivot(A, left, right, pivot):
    # Find pivot position
    for i in range(left, right+1):
        if A[i] == pivot:
            A[i], A[right] = A[right], A[i]
            break
    
    # Lomuto partition
    i = left - 1
    for j in range(left, right):
        if A[j] <= pivot:
            i += 1
            A[i], A[j] = A[j], A[i]
    A[i+1], A[right] = A[right], A[i+1]
    return i + 1
```

### 3.4.2 Detailed Example

```
Array: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15]
Find k=7 (7th smallest, 0-indexed) = 8

Step 1: Divide into groups of 5
[1,2,3,4,5], [6,7,8,9,10], [11,12,13,14,15]

Step 2: Find median of each group
Medians: [3, 8, 13]

Step 3: Find median of medians
Median of [3,8,13] = 8

Step 4: Partition around 8
[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15]
After partition: [1,2,3,4,5,6,7,8,9,10,11,12,13,14,15]
pivot_idx = 7, A[7] = 8

Step 5: Check position
k=7 == pivot_idx=7 → return 8 ✓
```

### 3.4.3 Correctness Analysis

**Key Question**: Why does median-of-medians guarantee good split?

**Visualization** (n=15 example):
```
Groups sorted by their medians:
Group 1: [1,  2,  3*,  4,  5]
Group 2: [6,  7,  8*,  9, 10]
Group 3: [11, 12, 13*, 14, 15]

* = median of group
Median of medians = 8

Elements ≤ 8:
- All of Group 1: 5 elements
- At least 3 from Group 2: ≥3 elements
- At least 3 from groups with median < 8: ≥3 elements
Total: At least 3 ⌈n/10⌉ elements

Elements ≥ 8: By symmetry, at least 3 ⌈n/10⌉ elements
```

**General Case**:

Number of groups: ⌈n/5⌉

At least half of medians ≤ median-of-medians:
- Number of such groups: ⌈⌈n/5⌉/2⌉ ≥ ⌈n/10⌉

Each group with median ≤ mom contributes:
- At least 3 elements ≤ mom
- (Except possibly last group if incomplete)

**Lower bound on elements ≤ mom**:
```
≥ 3(⌈⌈n/5⌉/2⌉ - 2)   (subtract 2 for mom's group and possibly incomplete group)
≥ 3n/10 - 6
```

**Upper bound on elements > mom**:
```
≤ n - (3n/10 - 6)
= 7n/10 + 6
```

**Similarly for elements ≥ mom and < mom**

**Conclusion**: After partitioning around median-of-medians, larger subarray has at most 7n/10 + 6 elements.

### 3.4.4 Time Complexity Analysis

**Recurrence**:
```
T(n) = T(⌈n/5⌉) + T(7n/10 + 6) + O(n)
       ↑           ↑              ↑
    Find median   Recursive      Partition +
    of medians    call on        create groups
                  larger side
```

**Proof by Substitution**:

Guess: T(n) ≤ cn for some constant c

**Base case**: T(n) ≤ d for n ≤ some constant n₀

**Inductive step**: Assume true for all k < n
```
T(n) = T(⌈n/5⌉) + T(7n/10 + 6) + an
     ≤ c⌈n/5⌉ + c(7n/10 + 6) + an
     ≤ cn/5 + c + 7cn/10 + 6c + an
     = cn(1/5 + 7/10) + 7c + an
     = 9cn/10 + 7c + an

For T(n) ≤ cn, we need:
9cn/10 + 7c + an ≤ cn
7c + an ≤ cn/10
7c ≤ cn/10 - an
7c ≤ n(c/10 - a)

This holds if c ≥ 10a and n > 70

Therefore: T(n) = O(n) with c = 10a
```

**Why groups of 5?**

Let's check other group sizes:

**Groups of 3**:
```
Median of medians eliminates ≥ 2·⌈n/6⌉ elements
Larger subarray ≤ n - 2⌈n/6⌉ ≈ 2n/3

Recurrence: T(n) = T(n/3) + T(2n/3) + O(n)
Solution: T(n) = O(n log n)  (Master Theorem doesn't apply)
```

**Groups of 5**:
```
Larger subarray ≤ 7n/10

Recurrence: T(n) = T(n/5) + T(7n/10) + O(n)
n/5 + 7n/10 = 9n/10 < n ✓
Solution: T(n) = O(n) ✓
```

**Groups of 7**:
```
Larger subarray ≤ 5n/7

Recurrence: T(n) = T(n/7) + T(5n/7) + O(n)
n/7 + 5n/7 = 6n/7 < 9n/10 ✓
Solution: T(n) = O(n) ✓

But more overhead: sorting groups of 7 vs 5
5 is minimum that gives linear time with practical constants
```

### 3.4.5 Practical Considerations

**Randomized vs Deterministic**:

| Aspect | Randomized (QuickSelect) | Deterministic (MoM) |
|--------|-------------------------|-------------------|
| Expected time | O(n) | O(n) |
| Worst case | O(n²) | O(n) |
| Constant factors | Small (~4n) | Large (~10n) |
| Implementation | Simple | Complex |
| Practical use | Preferred | Rare |

**Why is randomized used in practice?**
- Much simpler implementation
- Better constant factors (2-3x faster)
- Worst case extremely unlikely
- Can use median-of-three for better pivot selection

**Improved practical selection**:
```python
def practicalSelect(A, left, right, k):
    # Use insertion sort for small arrays
    if right - left + 1 < 10:
        A[left:right+1] = sorted(A[left:right+1])
        return A[left + k]
    
    # Median-of-three pivot selection
    mid = (left + right) // 2
    # Order left, mid, right
    if A[left] > A[mid]:
        A[left], A[mid] = A[mid], A[left]
    if A[left] > A[right]:
        A[left], A[right] = A[right], A[left]
    if A[mid] > A[right]:
        A[mid], A[right] = A[right], A[mid]
    
    # Use middle as pivot
    A[mid], A[right-1] = A[right-1], A[mid]
    pivot = A[right-1]
    
    # Partition
    i = left
    j = right - 1
    while True:
        i += 1
        while A[i] < pivot:
            i += 1
        j -= 1
        while A[j] > pivot:
            j -= 1
        if i >= j:
            break
        A[i], A[j] = A[j], A[i]
    
    A[i], A[right-1] = A[right-1], A[i]
    
    # Recurse
    if k == i - left:
        return A[i]
    elif k < i - left:
        return practicalSelect(A, left, i-1, k)
    else:
        return practicalSelect(A, i+1, right, k-(i-left+1))
```

---

## PRACTICE PROBLEMS - CHAPTER 3

### Problem 3.1: k-th Largest
**Q**: Modify selection algorithm to find k-th largest element.

**Solution**: Find (n-k+1)-th smallest element.

### Problem 3.2: Median of Two Sorted Arrays
**Q**: Two sorted arrays A[n] and B[m]. Find median in O(log(min(n,m))).

**Solution Sketch**: 
- Binary search on smaller array
- Partition both arrays so left halves have (n+m)/2 elements
- Check if partition is valid: max(leftA) ≤ min(rightB) and max(leftB) ≤ min(rightA)

### Problem 3.3: Closest to Median
**Q**: Find k numbers closest to median in O(n) time.

**Solution**:
1. Find median: O(n)
2. Create array of distances: O(n)
3. Find k-th smallest distance: O(n)
4. Collect all elements with distance ≤ k-th distance: O(n)

### Problem 3.4: Selection in Stream
**Q**: Elements arrive one by one. Maintain k-th smallest efficiently.

**Solution**: Use two heaps:
- Max-heap of k smallest elements
- Min-heap of remaining elements
- When new element arrives: O(log k) to update

### Problem 3.5: Majority Element
**Q**: Element appearing > n/2 times. Find in O(n) time, O(1) space.

**Solution** (Boyer-Moore Voting Algorithm):
```python
def findMajority(A):
    candidate = None
    count = 0
    
    for x in A:
        if count == 0:
            candidate = x
            count = 1
        elif x == candidate:
            count += 1
        else:
            count -= 1
    
    # Verify candidate
    if A.count(candidate) > len(A)//2:
        return candidate
    return None
```

### Problem 3.6: Weighted Median
**Q**: Each element has weight. Weighted median m satisfies: Σ(wᵢ for xᵢ<m) ≤ W/2 and Σ(wᵢ for xᵢ>m) ≤ W/2. Find in O(n) expected time.

**Solution**: Modify QuickSelect to track cumulative weights.

---

# CHAPTER 4: Non-Comparison Based Sorting

## 4.1 Counting Sort

### 4.1.1 The Algorithm

**Assumption**: Input elements are integers in range [0, k]

**Idea**: For each element x, determine how many elements are ≤ x. Place x directly in correct position.

**Algorithm**:
```python
def countingSort(A, k):
    """
    Sort array A of integers in range [0, k]
    Returns sorted array (not in-place)
    """
    n = len(A)
    C = [0] * (k + 1)  # Count array
    B = [0] * n         # Output array
    
    # Count occurrences
    for j in range(n):
        C[A[j]] += 1
    
    # C[i] now contains number of elements equal to i
    
    # Convert to cumulative counts
    for i in range(1, k + 1):
        C[i] = C[i] + C[i-1]
    
    # C[i] now contains number of elements ≤ i
    
    # Build output array (go backwards for stability)
    for j in range(n-1, -1, -1):
        B[C[A[j]] - 1] = A[j]
        C[A[j]] -= 1
    
    return B
```

### 4.1.2 Detailed Example

```
Input: A = [2, 5, 3, 0, 2, 3, 0, 3]
k = 5 (range 0-5)
n = 8

Step 1: Count occurrences
For each element in A, increment C[element]:
C = [2, 0, 2, 3, 0, 1]
     0  1  2  3  4  5
     ↑     ↑  ↑     ↑
   two 0s  two 2s  three 3s  one 5

Step 2: Cumulative counts
C[0] = 2
C[1] = 2 + 0 = 2
C[2] = 2 + 2 = 4
C[3] = 4 + 3 = 7
C[4] = 7 + 0 = 7
C[5] = 7 + 1 = 8

C = [2, 2, 4, 7, 7, 8]

Interpretation: C[i] = number of elements ≤ i
- Elements ≤ 0: 2
- Elements ≤ 2: 4
- Elements ≤ 3: 7
- Elements ≤ 5: 8

Step 3: Build output (process A backwards for stability)
j=7: A[7]=3, C[3]=7 → B[6]=3, C[3]=6
j=6: A[6]=0, C[0]=2 → B[1]=0, C[0]=1
j=5: A[5]=3, C[3]=6 → B[5]=3, C[3]=5
j=4: A[4]=2, C[2]=4 → B[3]=2, C[2]=3
j=3: A[3]=0, C[0]=1 → B[0]=0, C[0]=0
j=2: A[2]=3, C[3]=5 → B[4]=3, C[3]=4
j=1: A[1]=5, C[5]=8 → B[7]=5, C[5]=7
j=0: A[0]=2, C[2]=3 → B[2]=2, C[2]=2

Output: B = [0, 0, 2, 2, 3, 3, 3, 5]
```

### 4.1.3 Why Process Backwards?

**Stability**: Equal elements maintain relative order.

**Example**:
```
Input: [(2,a), (5,b), (2,c)]  (value, tag)

Forward processing:
j=0: A[0]=(2,a), position = C[2]-1 = 1 → B[1]=(2,a)
j=2: A[2]=(2,c), position = C[2]-1 = 0 → B[0]=(2,c)
Result: [(2,c), (2,a), ...] ✗ Not stable!

Backward processing:
j=2: A[2]=(2,c), position = C[2]-1 = 1 → B[1]=(2,c)
j=0: A[0]=(2,a), position = C[2]-1 = 0 → B[0]=(2,a)
Result: [(2,a), (2,c), ...] ✓ Stable!
```

### 4.1.4 Complexity Analysis

**Time Complexity**:
```
Line 3-4 (initialize): O(k)
Lines 7-8 (count): O(n)
Lines 13-14 (cumulative): O(k)
Lines 19-21 (build output): O(n)

Total: O(n + k)
```

**Space Complexity**: O(n + k)
- Output array B: O(n)
- Count array C: O(k)

**When is Counting Sort efficient?**
- When k = O(n), giving O(n) sorting!
- Example: Sorting ages of people (k ≈ 100)
- Example: Sorting exam scores (k ≈ 100)

**When NOT to use**:
- k >> n (e.g., k = 10^9, n = 100)
- Floating point numbers
- Strings (use radix sort)

### 4.1.5 Variants

**In-place Counting Sort** (not stable):
```python
def countingSortInPlace(A, k):
    count = [0] * (k + 1)
    
    for x in A:
        count[x] += 1
    
    index = 0
    for value in range(k + 1):
        for _ in range(count[value]):
            A[index] = value
            index += 1
```

**Counting Sort for Negative Numbers**:
```python
def countingSortNegative(A):
    min_val = min(A)
    max_val = max(A)
    range_size = max_val - min_val + 1
    
    count = [0] * range_size
    output = [0] * len(A)
    
    for num in A:
        count[num - min_val] += 1
    
    for i in range(1, range_size):
        count[i] += count[i-1]
    
    for num in reversed(A):
        output[count[num - min_val] - 1] = num
        count[num - min_val] -= 1
    
    return output
```

## 4.2 Radix Sort

### 4.2.1 The Idea

**Problem**: Sort numbers with d digits using stable sort on each digit.

**Two variants**:
1. **LSD** (Least Significant Digit first): Process right to left
2. **MSD** (Most Significant Digit first): Process left to right

**LSD is more common** - we'll focus on it.

### 4.2.2 LSD Radix Sort Algorithm

```python
def radixSort(A, d):
    """
    Sort array A of d-digit numbers
    Uses counting sort as subroutine
    """
    # Sort on each digit from least to most significant
    for digit_pos in range(d):
        countingSortByDigit(A, digit_pos)

def countingSortByDigit(A, digit_pos):
    """
    Stable sort A based on digit at digit_pos
    Assuming base-10 numbers
    """
    n = len(A)
    output = [0] * n
    count = [0] * 10  # 10 digits: 0-9
    
    # Count occurrences of each digit
    for num in A:
        digit = (num // (10 ** digit_pos)) % 10
        count[digit] += 1
    
    # Cumulative count
    for i in range(1, 10):
        count[i] += count[i-1]
    
    # Build output (backwards for stability)
    for i in range(n-1, -1, -1):
        digit = (A[i] // (10 ** digit_pos)) % 10
        output[count[digit] - 1] = A[i]
        count[digit] -= 1
    
    # Copy back
    for i in range(n):
        A[i] = output[i]
```

### 4.2.3 Detailed Example

```
Input: A = [170, 45, 75, 90, 802, 24, 2, 66]
d = 3 (maximum digits)

Digit 0 (ones place):
Sort by: 170→0, 45→5, 75→5, 90→0, 802→2, 24→4, 2→2, 66→6
Result: [170, 90, 802, 2, 24, 45, 75, 66]

Digit 1 (tens place):
Sort by: 170→7, 90→9, 802→0, 2→0, 24→2, 45→4, 75→7, 66→6
Result: [802, 2, 24, 45, 66, 170, 75, 90]

Digit 2 (hundreds place):
Sort by: 802→8, 2→0, 24→0, 45→0, 66→0, 170→1, 75→0, 90→0
Result: [2, 24, 45, 66, 75, 90, 170, 802]

Final: [2, 24, 45, 66, 75, 90, 170, 802] ✓
```

**Why does LSD work?**

**Intuition**: After sorting on digit i, all numbers are sorted with respect to their i rightmost digits. Sorting on digit i+1 (more significant) preserves this order due to stability.

**Formal Proof by Induction**:

**Base case**: After sorting on digit 0 (ones), numbers are sorted by last digit ✓

**Inductive step**: Assume after sorting on digit i-1, numbers are sorted by their last i digits.

After sorting on digit i:
- Numbers with different digit-i values are in correct order (just sorted)
- Numbers with same digit-i value maintain relative order (stability)
- Their relative order was correct for last i digits (induction hypothesis)
- Therefore, sorted by last i+1 digits ✓

**Termination**: After sorting on digit d-1, sorted by all d digits ✓

### 4.2.4 Complexity Analysis

**Time Complexity**:
```
Outer loop: d iterations
Each iteration: O(n + b) where b = base (usually 10)

Total: O(d(n + b))
```

**Choosing the base b**:

Represent numbers in base b instead of base 10:
- Number of digits: d = ⌈log_b(k)⌉ where k = max value
- Time: O(⌈log_b(k)⌉ · (n + b))

**Optimization**: Choose b to minimize d(n + b)

Taking derivative with respect to b:
```
d/db [log_b(k) · (n + b)] = 0

log_b(k) = (n + b) / (b · ln b)

Approximately: b ≈ n for optimal performance
```

**With optimal base**:
```
d = log_n(k)
Time = O(log_n(k) · n) = O(n · log k / log n)

For k ≤ n^c (polynomial range):
Time = O(cn) = O(n)
```

**Example**: Sort n 32-bit integers
- Base b = n
- d = log_n(2^32) = 32/log n
- Time = O(32n/log n · n) = O(32n)

**Space Complexity**: O(n + b)

### 4.2.5 MSD Radix Sort

**Algorithm** (recursive):
```python
def msdRadixSort(A, left, right, digit_pos, d):
    if left >= right or digit_pos >= d:
        return
    
    # Count sort on current digit
    buckets = [[] for _ in range(10)]
    for i in range(left, right+1):
        digit = (A[i] // (10 ** (d - digit_pos - 1))) % 10
        buckets[digit].append(A[i])
    
    # Copy back and recurse on each bucket
    index = left
    for bucket in buckets:
        for num in bucket:
            A[index] = num
            index += 1
        
        bucket_start = index - len(bucket)
        bucket_end = index - 1
        msdRadixSort(A, bucket_start, bucket_end, digit_pos+1, d)
```

**MSD vs LSD**:

| Feature | LSD | MSD |
|---------|-----|-----|
| Process order | Right to left | Left to right |
| Recursion | No | Yes |
| Early termination | No | Yes (if bucket empty) |
| Variable length | Must pad | Handles naturally |
| Stability | Important | Less important |
| Overhead | Lower | Higher |

**When to use MSD**:
- Variable-length keys
- Want early termination
- Sorting strings

### 4.2.6 Radix Sort for Strings

```python
def radixSortStrings(strings, max_len):
    """
    Sort strings using LSD radix sort
    Assumes all strings padded to max_len
    """
    for pos in range(max_len-1, -1, -1):
        countingSortStringsByChar(strings, pos)

def countingSortStringsByChar(strings, pos):
    """
    Stable sort strings by character at position pos
    """
    n = len(strings)
    output = [''] * n
    count = [0] * 256  # ASCII characters
    
    for s in strings:
        if pos < len(s):
            char_code = ord(s[pos])
        else:
            char_code = 0  # Treat as null
        count[char_code] += 1
    
    for i in range(1, 256):
        count[i] += count[i-1]
    
    for i in range(n-1, -1, -1):
        s = strings[i]
        if pos < len(s):
            char_code = ord(s[pos])
        else:
            char_code = 0
        output[count[char_code] - 1] = s
        count[char_code] -= 1
    
    for i in range(n):
        strings[i] = output[i]
```

**Example**:
```
Input: ["dog", "cat", "bat", "rat", "mat"]
max_len = 3

Position 2 (last char):
Sort by: g, t, t, t, t
Result: ["cat", "bat", "rat", "mat", "dog"]

Position 1 (middle char):
Sort by: a, a, a, a, o
Result: ["cat", "bat", "mat", "rat", "dog"]

Position 0 (first char):
Sort by: c, b, m, r, d
Result: ["bat", "cat", "dog", "mat", "rat"]
```

## 4.3 Bucket Sort

### 4.3.1 The Algorithm

**Assumption**: Input uniformly distributed over [0, 1)

**Idea**: 
1. Divide [0, 1) into n equal buckets
2. Distribute elements into buckets
3. Sort each bucket
4. Concatenate buckets

**Algorithm**:
```python
def bucketSort(A):
    """
    Sort array A of floats in [0, 1)
    """
    n = len(A)
    
    # Create empty buckets
    buckets = [[] for _ in range(n)]
    
    # Distribute elements into buckets
    for num in A:
        index = int(n * num)  # Bucket index
        if index == n:  # Handle edge case where num = 1
            index = n - 1
        buckets[index].append(num)
    
    # Sort individual buckets using insertion sort
    for bucket in buckets:
        insertionSort(bucket)
    
    # Concatenate all buckets
    result = []
    for bucket in buckets:
        result.extend(bucket)
    
    return result
```

### 4.3.2 Detailed Example

```
Input: A = [0.78, 0.17, 0.39, 0.26, 0.72, 0.94, 0.21, 0.12, 0.23, 0.68]
n = 10

Step 1: Create 10 buckets for ranges [0,0.1), [0.1,0.2), ..., [0.9,1.0)

Step 2: Distribute elements
0.78 → bucket 7 [0.7, 0.8)
0.17 → bucket 1 [0.1, 0.2)
0.39 → bucket 3 [0.3, 0.4)
0.26 → bucket 2 [0.2, 0.3)
0.72 → bucket 7 [0.7, 0.8)
0.94 → bucket 9 [0.9, 1.0)
0.21 → bucket 2 [0.2, 0.3)
0.12 → bucket 1 [0.1, 0.2)
0.23 → bucket 2 [0.2, 0.3)
0.68 → bucket 6 [0.6, 0.7)

Buckets:
[0]: []
[1]: [0.17, 0.12]
[2]: [0.26, 0.21, 0.23]
[3]: [0.39]
[4]: []
[5]: []
[6]: [0.68]
[7]: [0.78, 0.72]
[8]: []
[9]: [0.94]

Step 3: Sort each bucket (insertion sort)
[0]: []
[1]: [0.12, 0.17]
[2]: [0.21, 0.23, 0.26]
[3]: [0.39]
[4]: []
[5]: []
[6]: [0.68]
[7]: [0.72, 0.78]
[8]: []
[9]: [0.94]

Step 4: Concatenate
Result: [0.12, 0.17, 0.21, 0.23, 0.26, 0.39, 0.68, 0.72, 0.78, 0.94]
```

### 4.3.3 Time Complexity Analysis

**Average Case** (with uniform distribution):

Let n_i = number of elements in bucket i

**Time breakdown**:
```
Creating buckets: O(n)
Distributing elements: O(n)
Sorting buckets: Σ O(n_i²) for i=0 to n-1
Concatenating: O(n)

Total: O(n) + Σ O(n_i²)
```

**Expected value of Σ n_i²**:

Using indicator random variables:
```
Let X_ij = I{element j falls in bucket i}

n_i = Σ X_ij for j=1 to n

E[n_i] = Σ E[X_ij] = Σ (1/n) = 1

n_i² = (Σ X_ij)²
     = Σ Σ X_ij·X_kj
     = Σ X_ij² + Σ Σ X_ij·X_kj  (j≠k)

E[n_i²] = Σ E[X_ij²] + Σ Σ E[X_ij·X_kj]  (j≠k)
        = Σ E[X_ij] + Σ Σ E[X_ij]·E[X_kj]  (independence)
        = n·(1/n) + n(n-1)·(1/n)²
        = 1 + (n-1)/n
        = 2 - 1/n

Expected total cost of sorting all buckets:
E[Σ n_i²] = Σ E[n_i²]
          = n · (2 - 1/n)
          = 2n - 1

Therefore: E[T(n)] = O(n) + O(2n - 1) = O(n)
```

**Worst Case**: O(n²)
- Occurs when all elements fall into one bucket
- Example: Input already sorted or reverse sorted

**Space Complexity**: O(n)

### 4.3.4 Variants and Extensions

**Bucket Sort for Arbitrary Range [a, b)**:
```python
def bucketSortRange(A, a, b):
    n = len(A)
    buckets = [[] for _ in range(n)]
    
    # Normalize to [0, 1) and distribute
    for num in A:
        normalized = (num - a) / (b - a)
        index = int(n * normalized)
        if index == n:
            index = n - 1
        buckets[index].append(num)
    
    # Sort and concatenate
    result = []
    for bucket in buckets:
        bucket.sort()
        result.extend(bucket)
    
    return result
```

**Bucket Sort for Integers**:
```python
def bucketSortIntegers(A):
    if not A:
        return A
    
    min_val = min(A)
    max_val = max(A)
    n = len(A)
    
    # Create buckets
    bucket_range = (max_val - min_val) / n + 1
    buckets = [[] for _ in range(n)]
    
    # Distribute
    for num in A:
        index = int((num - min_val) / bucket_range)
        buckets[index].append(num)
    
    # Sort and concatenate
    result = []
    for bucket in buckets:
        bucket.sort()
        result.extend(bucket)
    
    return result
```

**Adaptive Bucket Count**:
```python
def adaptiveBucketSort(A):
    """
    Adjust number of buckets based on data distribution
    """
    n = len(A)
    if n <= 1:
        return A
    
    # Use sqrt(n) buckets as heuristic
    num_buckets = int(n ** 0.5) + 1
    
    min_val = min(A)
    max_val = max(A)
    bucket_range = (max_val - min_val) / num_buckets + 1e-9
    
    buckets = [[] for _ in range(num_buckets)]
    
    for num in A:
        index = int((num - min_val) / bucket_range)
        if index >= num_buckets:
            index = num_buckets - 1
        buckets[index].append(num)
    
    result = []
    for bucket in buckets:
        bucket.sort()
        result.extend(bucket)
    
    return result
```

### 4.3.5 When to Use Bucket Sort

**Ideal conditions**:
- Input uniformly distributed
- Know approximate range of values
- Can afford O(n) extra space
- Want O(n) average case

**Real-world applications**:
- External sorting (when data doesn't fit in memory)
- Parallel sorting (buckets can be sorted independently)
- Histogram equalization in image processing
- Hash table implementations

**Comparison with other sorts**:

| Algorithm | Average | Worst | Space | Assumption |
|-----------|---------|-------|-------|------------|
| Counting Sort | O(n+k) | O(n+k) | O(n+k) | Integers in [0,k] |
| Radix Sort | O(d(n+b)) | O(d(n+b)) | O(n+b) | d-digit numbers |
| Bucket Sort | O(n) | O(n²) | O(n) | Uniform distribution |

---

## PRACTICE PROBLEMS - CHAPTER 4

### Problem 4.1: Counting Sort Range Query
**Q**: After running counting sort, answer queries: "How many elements in range [a,b]?" in O(1) time.

**Solution**: Use cumulative count array C. Answer = C[b] - C[a-1].

### Problem 4.2: Sort by Digit Sum
**Q**: Sort n integers by sum of their digits. Design O(n) algorithm if digit sums are in range [1, k].

**Solution**: 
1. Compute digit sum for each number: O(n·d) where d = digits
2. Use counting sort on digit sums: O(n+k)
3. Total: O(n·d + k) = O(n) if k = O(n) and d = O(1)

### Problem 4.3: Two-key Radix Sort
**Q**: Sort pairs (a,b) lexicographically using radix sort.

**Solution**:
1. Sort by second component (b)
2. Sort by first component (a) using stable sort
3. Time: O(n) if both components in constant range

### Problem 4.4: Bucket Sort with Linked Lists
**Q**: Implement bucket sort using linked lists instead of arrays for buckets. Analyze space usage.

**Solution**: Space improves to O(n) total instead of O(n²) worst case with arrays.

### Problem 4.5: k-way Radix Sort
**Q**: Extend radix sort to process k digits at a time. Analyze optimal k.

**Solution**: 
- Time: O((d/k)(n + b^k)) where b = base
- Optimal when d/k ≈ n + b^k
- Usually k = 1 or 2 in practice

### Problem 4.6: Sort Nearly Sorted Array
**Q**: Array where each element is at most k positions from correct location. Design O(n log k) algorithm.

**Solution**: Use min-heap of size k+1:
```python
def sortNearlySorted(A, k):
    import heapq
    result = []
    heap = A[:k+1]
    heapq.heapify(heap)
    
    for i in range(k+1, len(A)):
        result.append(heapq.heappop(heap))
        heapq.heappush(heap, A[i])
    
    while heap:
        result.append(heapq.heappop(heap))
    
    return result
```

---

# CHAPTER 5: Computational Geometry - Convex Hull

## 5.1 Introduction to Convex Hull

### 5.1.1 Definitions

**Convex Set**: A set S is convex if for any two points p, q ∈ S, the line segment pq lies entirely in S.

**Convex Hull**: The convex hull CH(P) of a point set P is the smallest convex set containing P.

**Intuition**: Imagine points as nails on a board. Stretch a rubber band around them—it forms the convex hull.

**Formal Definition**:
```
CH(P) = {Σ λᵢpᵢ : pᵢ ∈ P, λᵢ ≥ 0, Σ λᵢ = 1}
```

### 5.1.2 Properties

1. **CH(P) is a convex polygon** if |P| ≥ 3 and points not collinear
2. **Vertices of CH(P)** are a subset of P
3. **|CH(P)| ≤ |P|** (number of vertices)
4. **CH(CH(P)) = CH(P)** (idempotent)
5. **Unique** for any point set

### 5.1.3 Applications

- **Computer Graphics**: Collision detection, rendering
- **Pattern Recognition**: Shape analysis, image processing
- **Geographic Information Systems**: Finding boundaries
- **Robotics**: Path planning, obstacle avoidance
- **Statistics**: Outlier detection
- **Game Development**: Line of sight, territory control

## 5.2 Geometric Primitives

### 5.2.1 Cross Product (2D)

For vectors u = (u₁, u₂) and v = (v₁, v₂):
```
u × v = u₁v₂ - u₂v₁

Interpretation:
> 0: v is counterclockwise from u (left turn)
= 0: v is collinear with u
< 0: v is clockwise from u (right turn)
```

**Geometric meaning**: Signed area of parallelogram formed by u and v.

### 5.2.2 Orientation Test

Given three points p₁, p₂, p₃, determine orientation:

```python
def orientation(p1, p2, p3):
    """
    Returns:
    > 0 if counterclockwise (left turn)
    = 0 if collinear
    < 0 if clockwise (right turn)
    """
    return (p2[0] - p1[0]) * (p3[1] - p1[1]) - \
           (p2[1] - p1[1]) * (p3[0] - p1[0])
```

**Derivation using vectors**:
```
Vector p1→p2 = (p2.x - p1.x, p2.y - p1.y)
Vector p1→p3 = (p3.x - p1.x, p3.y - p1.y)

Cross product = (p2.x - p1.x)(p3.y - p1.y) - (p2.y - p1.y)(p3.x - p1.x)

If > 0: p3 is left of line p1→p2
If = 0: p3 is on line p1→p2
If < 0: p3 is right of line p1→p2
```

**Example**:
```
p1 = (0, 0), p2 = (4, 4), p3 = (2, 3)

orientation(p1, p2, p3) = (4-0)(3-0) - (4-0)(2-0)
                        = 4·3 - 4·2
                        = 12 - 8
                        = 4 > 0

Therefore: Left turn (counterclockwise)
```

### 5.2.3 Polar Angle

For point p with respect to reference point p₀:
```
θ = atan2(p.y - p₀.y, p.x - p₀.x)
```

**Range**: [-π, π] or [0, 2π]

**Comparison without computing angle**:
```python
def polarAngleComparator(p0):
    """
    Returns comparator function for sorting by polar angle
    """
    def compare(p1, p2):
        # Check which quadrant
        q1 = quadrant(p1, p0)
        q2 = quadrant(p2, p0)
        
        if q1 != q2:
            return q1 - q2
        
        # Same quadrant: use cross product
        cross = orientation(p0, p1, p2)
        if cross == 0:
            # Collinear: closer point first
            dist1 = (p1[0]-p0[0])**2 + (p1[1]-p0[1])**2
            dist2 = (p2[0]-p0[0])**2 + (p2[1]-p0[1])**2
            return dist1 - dist2
        return -cross  # Counterclockwise order
    
    return compare

def quadrant(p, p0):
    """Returns quadrant number 1-4"""
    dx = p[0] - p0[0]
    dy = p[1] - p0[1]
    
    if dx >= 0 and dy >= 0:
        return 1
    elif dx < 0 and dy >= 0:
        return 2
    elif dx < 0 and dy < 0:
        return 3
    else:
        return 4
```

## 5.3 Graham's Scan Algorithm

### 5.3.1 The Algorithm

**High-level idea**:
1. Find lowest point (break ties by x-coordinate)
2. Sort remaining points by polar angle
3. Process points in order, maintaining convex hull
4. Use stack to track hull vertices
5. Remove points that create right turns

**Detailed Algorithm**:
```python
def grahamScan(points):
    """
    Compute convex hull using Graham's scan
    Returns vertices in counterclockwise order
    """
    n = len(points)
    if n < 3:
        return points
    
    # Step 1: Find lowest point (min y, then min x)
    p0 = min(points, key=lambda p: (p[1], p[0]))
    
    # Step 2: Sort by polar angle with respect to p0
    def polarKey(p):
        return (math.atan2(p[1] - p0[1], p[0] - p0[0]), 
                (p[0]-p0[0])**2 + (p[1]-p0[1])**2)
    
    sorted_points = [p0] + sorted([p for p in points if p != p0], 
                                   key=polarKey)
    
    # Step 3: Initialize stack with first 3 points
    stack = [sorted_points[0], sorted_points[1], sorted_points[2]]
    
    # Step 4: Process remaining points
    for i in range(3, n):
        # Remove points that make right turn
        while len(stack) > 1 and \
              orientation(stack[-2], stack[-1], sorted_points[i]) <= 0:
            stack.pop()
        
        stack.append(sorted_points[i])
    
    return stack
```

### 5.3.2 Detailed Example

```
Points: [(0,0), (1,1), (2,2), (3,1), (4,2), (2,0), (3,3), (1,3)]

Step 1: Find lowest point
p0 = (0,0) (lowest y-coordinate)

Step 2: Sort by polar angle from p0
Angles (in degrees):
(1,1): 45°
(2,2): 45° (farther)
(1,3): 71.57°
(3,3): 45° (farthest)
(2,0): 0°
(3,1): 18.43°
(4,2): 26.57°

Sorted: [(0,0), (2,0), (3,1), (4,2), (1,1), (2,2), (3,3), (1,3)]

Step 3: Initialize stack
Stack: [(0,0), (2,0), (3,1)]

Step 4: Process remaining points

Process (4,2):
  Check turn at (2,0), (3,1), (4,2)
  orientation = (3-2)(2-0) - (1-0)(4-2) = 2 - 2 = 0 (collinear)
  Remove (3,1)
  Stack: [(0,0), (2,0)]
  Add (4,2)
  Stack: [(0,0), (2,0), (4,2)]

Process (1,1):
  Check turn at (2,0), (4,2), (1,1)
  orientation = (4-2)(1-0) - (2-0)(1-2) = 2 + 2 = 4 > 0 (left turn)
  Keep (4,2), add (1,1)
  Stack: [(0,0), (2,0), (4,2), (1,1)]

Process (2,2):
  Check turn at (4,2), (1,1), (2,2)
  orientation = (1-4)(2-2) - (1-2)(2-4) = 0 - 2 = -2 < 0 (right turn)
  Remove (1,1)
  Check turn at (2,0), (4,2), (2,2)
  orientation = (4-2)(2-0) - (2-0)(2-2) = 4 > 0 (left turn)
  Add (2,2)
  Stack: [(0,0), (2,0), (4,2), (2,2)]

Process (3,3):
  Check turn at (4,2), (2,2), (3,3)
  orientation = (2-4)(3-2) - (2-2)(3-4) = -2 < 0 (right turn)
  Remove (2,2)
  Check turn at (2,0), (4,2), (3,3)
  orientation = (4-2)(3-0) - (2-0)(3-2) = 6 - 2 = 4 > 0 (left turn)
  Add (3,3)
  Stack: [(0,0), (2,0), (4,2), (3,3)]

Process (1,3):
  Check turn at (4,2), (3,3), (1,3)
  orientation = (3-4)(3-2) - (3-2)(1-3) = -1 + 2 = 1 > 0 (left turn)
  Add (1,3)
  Stack: [(0,0), (2,0), (4,2), (3,3), (1,3)]

Final convex hull: [(0,0), (2,0), (4,2), (3,3), (1,3)]
```

**Visualization**:
```
    (1,3)----(3,3)
     |         |
     |         |
     |        (4,2)
     |       /
     |     /
  (0,0)-(2,0)
```

### 5.3.3 Correctness Proof

**Claim**: Graham's scan correctly computes the convex hull.

**Proof sketch**:

**Lemma 1**: Points are processed in order of increasing polar angle around p0.

*Proof*: By construction (sorting step). ✓

**Lemma 2**: At any point, stack contains vertices of convex hull of points processed so far.

*Proof by induction*:

*Base case*: After adding first 3 points, they form a convex hull (triangle). ✓

*Inductive step*: Assume stack contains CH of first i points. When processing point p_{i+1}:
- If left turn from last two stack points to p_{i+1}: p_{i+1} is on hull, add it. ✓
- If right turn: Second-to-last point is interior (not on final hull), remove it. Continue checking. ✓

**Lemma 3**: No hull vertex is incorrectly removed.

*Proof*: A point is removed only when a later point (larger polar angle) makes it interior. Since we process in polar angle order, this point cannot be on the final hull. ✓

**Conclusion**: Algorithm correctly computes convex hull. ✓

### 5.3.4 Time Complexity Analysis

**Step-by-step**:
```
Step 1: Find lowest point: O(n)
Step 2: Sort by polar angle: O(n log n)
Step 3: Initialize stack: O(1)
Step 4: Process points: O(n) total

Total: O(n log n)
```

**Why is Step 4 O(n)?**

Each point is:
- Pushed onto stack exactly once: O(n) total
- Popped from stack at most once: O(n) total

Total operations: O(n)

**Space Complexity**: O(n) for stack and sorted array

**Optimality**: O(n log n) is optimal for convex hull in comparison model (can be proven using reduction from sorting).

### 5.3.5 Handling Degenerate Cases

**Collinear points**:
```python
def grahamScanWithCollinear(points):
    # ... (same as before until sorting)
    
    # When sorting, if collinear, keep only farthest
    sorted_points = [p0]
    prev_angle = None
    collinear_group = []
    
    for p in sorted([p for p in points if p != p0], key=polarKey):
        angle = math.atan2(p[1] - p0[1], p[0] - p0[0])
        
        if prev_angle is None or abs(angle - prev_angle) > 1e-9:
            # New angle: add farthest from previous group
            if collinear_group:
                sorted_points.append(max(collinear_group, 
                                       key=lambda q: distance(p0, q)))
            collinear_group = [p]
            prev_angle = angle
        else:
            collinear_group.append(p)
    
    # Add last group
    if collinear_group:
        sorted_points.append(max(collinear_group, 
                                key=lambda q: distance(p0, q)))
    
    # Continue with Graham's scan...
```

**Duplicate points**: Remove during preprocessing

**All collinear**: Return line segment endpoints

## 5.4 Other Convex Hull Algorithms

### 5.4.1 Jarvis March (Gift Wrapping)

**Idea**: Start from leftmost point, repeatedly find next hull vertex by choosing point with smallest polar angle.

**Algorithm**:
```python
def jarvisMarch(points):
    n = len(points)
    if n < 3:
        return points
    
    # Find leftmost point
    leftmost = min(points, key=lambda p: (p[0], p[1]))
    
    hull = []
    current = leftmost
    
    while True:
        hull.append(current)
        next_point = points[0]
        
        # Find point with smallest polar angle
        for p in points:
            if p == current:
                continue
            
            cross = orientation(current, next_point, p)
            if next_point == current or cross > 0 or \
               (cross == 0 and distance(current, p) > distance(current, next_point)):
                next_point = p
        
        current = next_point
        
        # Completed the hull
        if current == leftmost:
            break
    
    return hull
```

**Time Complexity**: O(nh) where h = number of hull vertices
- Best case: O(n) when h is constant
- Worst case: O(n²) when h = n

**When to use**: Output-sensitive algorithm, good when h << n

### 5.4.2 QuickHull

**Idea**: Divide and conquer, similar to QuickSort.

**Algorithm**:
1. Find leftmost and rightmost points
2. These divide points into upper and lower hull
3. Recursively find farthest point from line and recurse
4. Base case: No points outside triangle

**Time Complexity**:
- Average: O(n log n)
- Worst case: O(n²)

**Advantage**: Often faster in practice than Graham's scan

### 5.4.3 Chan's Algorithm

**Combines**: Jarvis march + Graham's scan

**Idea**: 
- Partition points into m groups
- Compute convex hull of each group
- Use Jarvis march on these hulls

**Time Complexity**: O(n log h) where h = hull size

**Optimal output-sensitive algorithm**!

**When to use**: When h is unknown and potentially small

---

## PRACTICE PROBLEMS - CHAPTER 5

### Problem 5.1: Area of Convex Hull
**Q**: Given convex hull vertices in order, compute area in O(n).

**Solution** (Shoelace formula):
```python
def polygonArea(vertices):
    n = len(vertices)
    area = 0
    for i in range(n):
        j = (i + 1) % n
        area += vertices[i][0] * vertices[j][1]
        area -= vertices[j][0] * vertices[i][1]
    return abs(area) / 2
```

### Problem 5.2: Point Inside Convex Polygon
**Q**: Check if point p is inside convex polygon in O(log n).

**Solution**: Binary search on polar angle, then check if point is on correct side of edge.

### Problem 5.3: Diameter of Point Set
**Q**: Find maximum distance between any two points. Design O(n log n) algorithm.

**Solution**: 
1. Compute convex hull: O(n log n)
2. Use rotating calipers: O(n)
3. Diameter must be between two hull vertices

### Problem 5.4: Convex Layers
**Q**: Compute all convex hull layers (onion peeling). Analyze complexity.

**Solution**: Repeatedly compute convex hull and remove vertices. O(n²) worst case, O(nh) where h = number of layers.

### Problem 5.5: Minimum Enclosing Circle
**Q**: Find smallest circle enclosing all points. Design O(n) expected time algorithm.

**Hint**: Use Welzl's algorithm (randomized incremental construction).

### Problem 5.6: Line-Hull Intersection
**Q**: Given line and convex hull, find intersection points in O(log n).

**Solution**: Binary search to find edges intersected by line.

---

# CHAPTER 6: Graph Algorithms - BFS and Shortest Paths

## 6.1 Graph Representations

### 6.1.1 Adjacency Matrix

**Structure**: 2D array A where A[i][j] = 1 if edge (i,j) exists

```python
class GraphMatrix:
    def __init__(self, n):
        self.n = n
        self.adj = [[0] * n for _ in range(n)]
    
    def addEdge(self, u, v):
        self.adj[u][v] = 1
        # For undirected: self.adj[v][u] = 1
    
    def hasEdge(self, u, v):
        return self.adj[u][v] == 1
```

**Space**: O(V²)
**Edge check**: O(1)
**List neighbors**: O(V)
**Good for**: Dense graphs, matrix operations

### 6.1.2 Adjacency List

**Structure**: Array of lists, each containing neighbors

```python
class GraphList:
    def __init__(self, n):
        self.n = n
        self.adj = [[] for _ in range(n)]
    
    def addEdge(self, u, v):
        self.adj[u].append(v)
        # For undirected: self.adj[v].append(u)
    
    def neighbors(self, u):
        return self.adj[u]
```

**Space**: O(V + E)
**Edge check**: O(degree(u))
**List neighbors**: O(degree(u))
**Good for**: Sparse graphs, most algorithms

## 6.2 Breadth-First Search (BFS)

### 6.2.1 The Algorithm

```python
from collections import deque

def BFS(graph, start):
    """
    Breadth-First Search from start vertex
    Returns: distances and parent pointers
    """
    n = len(graph.adj)
    
    # Initialize
    visited = [False] * n
    distance = [float('inf')] * n
    parent = [None] * n
    
    # Start BFS
    queue = deque([start])
    visited[start] = True
    distance[start] = 0
    
    while queue:
        u = queue.popleft()
        
        # Process all neighbors
        for v in graph.adj[u]:
            if not visited[v]:
                visited[v] = True
                distance[v] = distance[u] + 1
                parent[v] = u
                queue.append(v)
    
    return distance, parent
```

### 6.2.2 Detailed Example

```
Graph (adjacency list):
0: [1, 2]
1: [0, 3, 4]
2: [0, 5]
3: [1]
4: [1, 5]
5: [2, 4]

BFS from vertex 0:

Initial state:
Queue: [0]
Visited: {0}
Distance: [0, ∞, ∞, ∞, ∞, ∞]
Parent: [None, None, None, None, None, None]

Step 1: Process vertex 0
  Dequeue 0
  Neighbors: [1, 2]
  - Visit 1: distance[1] = 1, parent[1] = 0
  - Visit 2: distance[2] = 1, parent[2] = 0
  Queue: [1, 2]
  Visited: {0, 1, 2}
  Distance: [0, 1, 1, ∞, ∞, ∞]
  Parent: [None, 0, 0, None, None, None]

Step 2: Process vertex 1
  Dequeue 1
  Neighbors: [0, 3, 4]
  - Skip 0 (already visited)
  - Visit 3: distance[3] = 2, parent[3] = 1
  - Visit 4: distance[4] = 2, parent[4] = 1
  Queue: [2, 3, 4]
  Visited: {0, 1, 2, 3, 4}
  Distance: [0, 1, 1, 2, 2, ∞]
  Parent: [None, 0, 0, 1, 1, None]

Step 3: Process vertex 2
  Dequeue 2
  Neighbors: [0, 5]
  - Skip 0 (already visited)
  - Visit 5: distance[5] = 2, parent[5] = 2
  Queue: [3, 4, 5]
  Visited: {0, 1, 2, 3, 4, 5}
  Distance: [0, 1, 1, 2, 2, 2]
  Parent: [None, 0, 0, 1, 1, 2]

Step 4: Process vertex 3
  Dequeue 3
  Neighbors: [1]
  - Skip 1 (already visited)
  Queue: [4, 5]

Step 5: Process vertex 4
  Dequeue 4
  Neighbors: [1, 5]
  - Skip 1, 5 (already visited)
  Queue: [5]

Step 6: Process vertex 5
  Dequeue 5
  Neighbors: [2, 4]
  - Skip 2, 4 (already visited)
  Queue: []

Done!

Final result:
Distance: [0, 1, 1, 2, 2, 2]
Parent: [None, 0, 0, 1, 1, 2]

BFS Tree:
       0
      / \
     1   2
    / \   \
   3   4   5
```

### 6.2.3 Properties and Correctness

**Key Properties**:

1. **Level-order traversal**: Visits vertices in order of distance from source
2. **Shortest path**: In unweighted graphs, BFS finds shortest paths
3. **BFS tree**: Parent pointers form a tree rooted at source
4. **Distance property**: For any edge (u,v), |d[v] - d[u]| ≤ 1

**Theorem**: BFS correctly computes shortest path distances in unweighted graphs.

**Proof by induction**:

**Invariant**: When vertex v is enqueued, d[v] = shortest distance from source.

**Base case**: Source s enqueued with d[s] = 0 ✓

**Inductive step**: Assume all vertices in queue have correct distances. When dequeuing u:
- For each unvisited neighbor v of u
- Any path to v must pass through some discovered vertex
- Shortest such path is through u (by BFS properties)
- Therefore d[v] = d[u] + 1 is correct ✓

**Time Complexity Analysis**:

```
Each vertex enqueued/dequeued at most once: O(V)
Each edge examined at most twice (once from each endpoint): O(E)
Total: O(V + E)
```

**Space Complexity**: O(V) for queue and arrays

### 6.2.4 Applications of BFS

**1. Shortest Path in Unweighted Graph**:
```python
def shortestPath(graph, start, end):
    distance, parent = BFS(graph, start)
    
    if distance[end] == float('inf'):
        return None  # No path exists
    
    # Reconstruct path
    path = []
    current = end
    while current is not None:
        path.append(current)
        current = parent[current]
    
    return path[::-1]  # Reverse to get start→end
```

**2. Connected Components**:
```python
def countComponents(graph):
    n = len(graph.adj)
    visited = [False] * n
    count = 0
    
    for v in range(n):
        if not visited[v]:
            # BFS to mark all vertices in this component
            queue = deque([v])
            visited[v] = True
            
            while queue:
                u = queue.popleft()
                for w in graph.adj[u]:
                    if not visited[w]:
                        visited[w] = True
                        queue.append(w)
            
            count += 1
    
    return count
```

**3. Bipartite Testing**:
```python
def isBipartite(graph):
    n = len(graph.adj)
    color = [-1] * n
    
    for start in range(n):
        if color[start] == -1:
            queue = deque([start])
            color[start] = 0
            
            while queue:
                u = queue.popleft()
                
                for v in graph.adj[u]:
                    if color[v] == -1:
                        color[v] = 1 - color[u]
                        queue.append(v)
                    elif color[v] == color[u]:
                        return False  # Odd cycle found
    
    return True
```

**4. Level-Order Traversal**:
```python
def levelOrder(graph, start):
    """Return vertices grouped by distance level"""
    levels = []
    current_level = [start]
    visited = {start}
    
    while current_level:
        levels.append(current_level[:])
        next_level = []
        
        for u in current_level:
            for v in graph.adj[u]:
                if v not in visited:
                    visited.add(v)
                    next_level.append(v)
        
        current_level = next_level
    
    return levels
```

**5. Finding Cycle**:
```python
def hasCycleBFS(graph):
    """Detect cycle using BFS (undirected graph)"""
    n = len(graph.adj)
    visited = [False] * n
    parent = [-1] * n
    
    for start in range(n):
        if visited[start]:
            continue
        
        queue = deque([start])
        visited[start] = True
        
        while queue:
            u = queue.popleft()
            
            for v in graph.adj[u]:
                if not visited[v]:
                    visited[v] = True
                    parent[v] = u
                    queue.append(v)
                elif parent[u] != v:
                    return True  # Back edge found
    
    return False
```

### 6.2.5 BFS Variants

**0-1 BFS** (for graphs with edge weights 0 or 1):
```python
def bfs01(graph, start):
    """
    BFS for graphs with edge weights 0 or 1
    Uses deque: add weight-0 edges to front, weight-1 to back
    """
    n = len(graph.adj)
    distance = [float('inf')] * n
    distance[start] = 0
    dq = deque([start])
    
    while dq:
        u = dq.popleft()
        
        for v, weight in graph.adj[u]:  # (neighbor, weight)
            new_dist = distance[u] + weight
            
            if new_dist < distance[v]:
                distance[v] = new_dist
                
                if weight == 0:
                    dq.appendleft(v)  # Priority: weight-0 first
                else:
                    dq.append(v)
    
    return distance
```

**Multi-source BFS**:
```python
def multiSourceBFS(graph, sources):
    """
    BFS from multiple sources simultaneously
    Used in problems like "nearest exit" or "rotten oranges"
    """
    n = len(graph.adj)
    distance = [float('inf')] * n
    queue = deque()
    
    # Initialize with all sources
    for s in sources:
        distance[s] = 0
        queue.append(s)
    
    while queue:
        u = queue.popleft()
        
        for v in graph.adj[u]:
            if distance[v] == float('inf'):
                distance[v] = distance[u] + 1
                queue.append(v)
    
    return distance
```

**Bidirectional BFS**:
```python
def bidirectionalBFS(graph, start, end):
    """
    Search from both start and end simultaneously
    Faster for finding path between two specific vertices
    """
    if start == end:
        return 0
    
    # Forward BFS from start
    visited_start = {start: 0}
    queue_start = deque([start])
    
    # Backward BFS from end
    visited_end = {end: 0}
    queue_end = deque([end])
    
    distance = 0
    
    while queue_start or queue_end:
        distance += 1
        
        # Expand from start
        if queue_start:
            for _ in range(len(queue_start)):
                u = queue_start.popleft()
                
                for v in graph.adj[u]:
                    if v in visited_end:
                        return visited_start[u] + visited_end[v] + 1
                    
                    if v not in visited_start:
                        visited_start[v] = visited_start[u] + 1
                        queue_start.append(v)
        
        # Expand from end
        if queue_end:
            for _ in range(len(queue_end)):
                u = queue_end.popleft()
                
                for v in graph.adj[u]:
                    if v in visited_start:
                        return visited_start[v] + visited_end[u] + 1
                    
                    if v not in visited_end:
                        visited_end[v] = visited_end[u] + 1
                        queue_end.append(v)
    
    return -1  # No path exists
```

## 6.3 Dijkstra's Algorithm

### 6.3.1 Single-Source Shortest Path

**Problem**: Given weighted graph G=(V,E) with non-negative weights and source s, find shortest paths from s to all vertices.

**Why not BFS?**: BFS works for unweighted graphs (or unit weights). With varying weights, shortest path may have more edges.

**Example where BFS fails**:
```
    s --5--> b
    |        |
    1        1
    |        |
    v        v
    a --1--> c

BFS from s: dist[b]=1, dist[a]=1, dist[c]=2
But shortest path s→a→c has total weight 2, not 5!
Correct: dist[a]=1, dist[c]=2, dist[b]=5
```

### 6.3.2 The Algorithm

**Greedy Strategy**: Always extend shortest known path.

```python
import heapq

def dijkstra(graph, start):
    """
    Dijkstra's algorithm for SSSP
    graph.adj[u] = [(v, weight), ...]
    Returns: distance array
    """
    n = len(graph.adj)
    distance = [float('inf')] * n
    distance[start] = 0
    parent = [None] * n
    visited = [False] * n
    
    # Min-heap: (distance, vertex)
    pq = [(0, start)]
    
    while pq:
        dist_u, u = heapq.heappop(pq)
        
        if visited[u]:
            continue
        
        visited[u] = True
        
        # Relax all edges from u
        for v, weight in graph.adj[u]:
            new_dist = distance[u] + weight
            
            if new_dist < distance[v]:
                distance[v] = new_dist
                parent[v] = u
                heapq.heappush(pq, (new_dist, v))
    
    return distance, parent
```

### 6.3.3 Detailed Example

```
Graph:
    a --4--> b
    |        |
    2        1
    |        |
    v        v
    c --5--> d

Start: a

Initial:
distance: [0, ∞, ∞, ∞]  (a, b, c, d)
visited: [F, F, F, F]
PQ: [(0, a)]

Step 1: Extract (0, a)
  Visit a
  Neighbors: b (weight 4), c (weight 2)
  Relax a→b: distance[b] = 0+4 = 4
  Relax a→c: distance[c] = 0+2 = 2
  distance: [0, 4, 2, ∞]
  visited: [T, F, F, F]
  PQ: [(2, c), (4, b)]

Step 2: Extract (2, c)
  Visit c
  Neighbors: d (weight 5)
  Relax c→d: distance[d] = 2+5 = 7
  distance: [0, 4, 2, 7]
  visited: [T, F, T, F]
  PQ: [(4, b), (7, d)]

Step 3: Extract (4, b)
  Visit b
  Neighbors: d (weight 1)
  Relax b→d: distance[d] = 4+1 = 5 < 7, update!
  distance: [0, 4, 2, 5]
  visited: [T, T, T, F]
  PQ: [(5, d), (7, d)]  // old (7,d) still in heap

Step 4: Extract (5, d)
  Visit d
  No unvisited neighbors
  distance: [0, 4, 2, 5]
  visited: [T, T, T, T]
  PQ: [(7, d)]

Step 5: Extract (7, d)
  Already visited, skip
  PQ: []

Done!

Final distances from a: a=0, b=4, c=2, d=5
Shortest paths:
  a→a: 0
  a→b: 4 (direct)
  a→c: 2 (direct)
  a→d: 5 (a→b→d)
```

### 6.3.4 Correctness Proof

**Theorem**: Dijkstra's algorithm correctly computes shortest paths when all edge weights are non-negative.

**Proof by induction on number of vertices processed**:

**Invariant**: When vertex u is extracted from priority queue, d[u] = δ(s,u) (true shortest distance).

**Base case**: First vertex extracted is source s with d[s] = 0 = δ(s,s) ✓

**Inductive step**: Let u be next vertex extracted from PQ. Assume all previously extracted vertices have correct distances.

*Suppose for contradiction*: d[u] > δ(s,u)

Let P be a true shortest path from s to u:
```
s → ... → x → y → ... → u
     ↑       ↑
  extracted  not yet
  vertices   extracted
```

where x is last extracted vertex on P, and y is first unextracted vertex.

Since x was extracted: d[x] = δ(s,x) (by induction)

When x was extracted, edge x→y was relaxed:
```
d[y] ≤ d[x] + w(x,y) = δ(s,x) + w(x,y) = δ(s,y)
```

Since y is on shortest path P to u:
```
δ(s,y) ≤ δ(s,u)  (y comes before u on P)
```

Since u was extracted before y:
```
d[u] ≤ d[y]  (priority queue property)
```

Combining:
```
d[u] ≤ d[y] ≤ δ(s,y) ≤ δ(s,u)
```

But we assumed d[u] > δ(s,u), contradiction! ✓

Therefore d[u] = δ(s,u). ✓

**Why non-negative weights required?**

With negative weights, a longer path (more edges) might be shorter (less weight). Extracting a vertex doesn't guarantee optimality.

Example:
```
s --1--> a --(-10)--> b

Dijkstra would extract a first with d[a]=1
Then set d[b]=1+(-10)=-9
But if b is already extracted with d[b]=∞, it's wrong!

Actually worse: if there are cycles with negative total weight,
shortest path is undefined (can keep going around cycle).
```

### 6.3.5 Time Complexity Analysis

**Depends on implementation**:

**1. Array (naive)**:
```
Extract-min: O(V)  // scan all vertices
Decrease-key: O(1)  // direct array access
Total: O(V²)
```

**2. Binary heap**:
```
Extract-min: O(log V)
Decrease-key: O(log V)  // actually push new entry
Total: O((V + E) log V)

Breakdown:
- Each vertex extracted once: V extractions
- Each edge relaxed at most once: E relaxations
- Each operation: O(log V)
- Total: O(V log V + E log V) = O((V+E) log V)
```

**3. Fibonacci heap** (theoretical):
```
Extract-min: O(log V) amortized
Decrease-key: O(1) amortized
Total: O(E + V log V)
```

**Comparison**:

| Implementation | Time Complexity | Space | Practical? |
|----------------|----------------|-------|------------|
| Array | O(V²) | O(V) | Dense graphs |
| Binary heap | O((V+E) log V) | O(V) | Most cases ✓ |
| Fibonacci heap | O(E + V log V) | O(V) | Rarely (complex) |

**For sparse graphs** (E ≈ V): Binary heap gives O(V log V)
**For dense graphs** (E ≈ V²): Array gives O(V²) = O(E)

### 6.3.6 Optimization: Lazy Deletion

**Problem**: Multiple entries for same vertex in priority queue

**Solution**: Mark vertices as visited, skip duplicates

```python
def dijkstraOptimized(graph, start):
    n = len(graph.adj)
    distance = [float('inf')] * n
    distance[start] = 0
    visited = set()
    pq = [(0, start)]
    
    while pq:
        dist_u, u = heapq.heappop(pq)
        
        # Skip if already processed
        if u in visited:
            continue
        
        visited.add(u)
        
        # Only relax if this is current best distance
        if dist_u > distance[u]:
            continue
        
        for v, weight in graph.adj[u]:
            new_dist = distance[u] + weight
            
            if new_dist < distance[v]:
                distance[v] = new_dist
                heapq.heappush(pq, (new_dist, v))
    
    return distance
```

**Advantage**: Simple implementation, works well in practice

**Disadvantage**: Priority queue can have O(E) entries in worst case

### 6.3.7 Path Reconstruction

```python
def dijkstraWithPath(graph, start, end):
    n = len(graph.adj)
    distance = [float('inf')] * n
    distance[start] = 0
    parent = [None] * n
    visited = set()
    pq = [(0, start)]
    
    while pq:
        dist_u, u = heapq.heappop(pq)
        
        if u in visited:
            continue
        
        visited.add(u)
        
        if u == end:
            break  # Found shortest path to target
        
        for v, weight in graph.adj[u]:
            new_dist = distance[u] + weight
            
            if new_dist < distance[v]:
                distance[v] = new_dist
                parent[v] = u
                heapq.heappush(pq, (new_dist, v))
    
    # Reconstruct path
    if distance[end] == float('inf'):
        return None, float('inf')
    
    path = []
    current = end
    while current is not None:
        path.append(current)
        current = parent[current]
    
    return path[::-1], distance[end]
```

### 6.3.8 Variants and Applications

**1. Single-pair shortest path** (source to target):
```python
# Same as Dijkstra, but break when target is extracted
# Still Ω(E + V log V) worst case (might need to explore whole graph)
```

**2. All-pairs shortest path**:
```python
def allPairsShortestPath(graph):
    n = len(graph.adj)
    distances = []
    
    for s in range(n):
        dist = dijkstra(graph, s)
        distances.append(dist)
    
    return distances  # distances[u][v] = shortest path u→v

# Time: O(V(E + V log V)) = O(VE + V² log V)
# For dense graphs: O(V³ log V)
# Floyd-Warshall is O(V³) but simpler
```

**3. Shortest path with exactly k edges**:
```python
# Modification: track number of edges used
# State: (distance, vertex, edges_used)
# Increase edges_used on each step
# Extract only when edges_used == k
```

**4. A* Search** (heuristic-guided Dijkstra):
```python
def astar(graph, start, goal, heuristic):
    """
    A* search with heuristic function
    heuristic(v) estimates distance from v to goal
    """
    pq = [(heuristic(start), 0, start)]  # (f, g, vertex)
    g_score = {start: 0}
    
    while pq:
        f, g, u = heapq.heappop(pq)
        
        if u == goal:
            return g
        
        for v, weight in graph.adj[u]:
            tentative_g = g + weight
            
            if v not in g_score or tentative_g < g_score[v]:
                g_score[v] = tentative_g
                f_score = tentative_g + heuristic(v)
                heapq.heappush(pq, (f_score, tentative_g, v))
    
    return float('inf')

# If heuristic is admissible (never overestimates), A* is optimal
# Often much faster than Dijkstra for specific target
```

---

## PRACTICE PROBLEMS - CHAPTER 6

### Problem 6.1: Shortest Path with Exactly K Edges
**Q**: Find shortest path from s to t using exactly k edges. Design O(kE) algorithm.

**Solution**: Dynamic programming:
```python
dp[i][v] = shortest path to v using exactly i edges
dp[i][v] = min(dp[i-1][u] + w(u,v)) for all edges (u,v)
```

### Problem 6.2: Second Shortest Path
**Q**: Find second shortest simple path from s to t in O((V+E) log V).

**Hint**: Modify Dijkstra to track two best distances to each vertex.

### Problem 6.3: Minimum Bottleneck Path
**Q**: Find path from s to t where maximum edge weight is minimized.

**Solution**: Binary search on answer + BFS, or modify Dijkstra to track max edge.

### Problem 6.4: Maze Solving
**Q**: Grid with obstacles. Find shortest path from start to end. Moves: up, down, left, right.

**Solution**: Model as graph, use BFS. O(rows × cols) time.

### Problem 6.5: Word Ladder
**Q**: Transform word A to word B, changing one letter at a time. Each intermediate must be valid word. Find shortest sequence.

**Solution**: Build graph where words are vertices, edge if one letter different. BFS from A to B.

### Problem 6.6: Shortest Path with Toll
**Q**: Each edge has distance and toll. Find shortest path from s to t with total toll ≤ T.

**Solution**: Modified Dijkstra with state (vertex, toll_used). O((V+E)T log(VT)) time.

---

**[The guide continues with more chapters on Greedy Algorithms (MST, Union-Find), Dynamic Programming, and additional practice problems. Would you like me to continue with the remaining chapters?]**