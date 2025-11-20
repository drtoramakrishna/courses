# Internal 2 Exam - Algorithms & Data Structures

## 📚 Complete Teaching Material Index

This directory contains comprehensive teaching material for the Internal 2 module of the Algorithms and Data Structures course. Each chapter includes detailed explanations, algorithm tracing, time complexity analysis, Mermaid diagrams, mathematical derivations, real-life examples, and C code implementations.

---

## 📖 Table of Contents

### Core Complexity Theory
1. **[Polynomial Time Reductions](01_polynomial_time_reductions.md)** ✅
   - Reduction definitions and notation
   - IS ≤_p VC, 3-SAT ≤_p IS examples
   - Complete C implementations with verification
   - Transitivity and hardness implications

2. **[NP, NP-Complete, and NP-Hard Problems](02_np_completeness.md)** ✅
   - Complexity classes P, NP, NP-Complete, NP-Hard
   - Cook-Levin theorem
   - Verifier approach with Hamilton Cycle and SAT examples
   - Proving NP-Completeness (Vertex Cover example)

### Dynamic Programming & Optimization
3. **[Longest Increasing Subsequence](03_longest_increasing_subsequence.md)** ✅
   - O(n²) DP solution with reconstruction
   - O(n log n) binary search (Patience Sorting)
   - Complete trace examples
   - Variations: Number of LIS, Russian Doll Envelopes

### Graph Algorithms - Network Flow
4. **[Network Flow - Ford-Fulkerson Method](04_network_flow.md)** 🔄
   - Flow networks and definitions
   - Ford-Fulkerson algorithm
   - Edmonds-Karp O(VE²) implementation
   - Residual graphs and augmenting paths

5. **[Bipartite Graphs and Matching](05_bipartite_matching.md)** 🔄
   - Bipartite graph properties
   - Maximum matching algorithms
   - Hall's Marriage Theorem
   - Reduction to Max-Flow

6. **[Max-Flow Min-Cut Theorem](06_max_flow_min_cut.md)** 🔄
   - Duality theorem statement and proof
   - Cut definitions and capacity
   - Applications to network reliability

7. **[Perfect Matching in Bipartite Graphs](07_perfect_matching.md)** 🔄
   - Hall's condition for perfect matching
   - Hungarian algorithm for weighted matching
   - Applications to assignment problems

### NP-Hardness Proofs
8. **[TSP NP-Hardness](08_tsp_np_hardness.md)** 🔄
   - Reduction from Hamilton Cycle
   - Proof of NP-Completeness
   - Approximation algorithms (2-approx, Christofides)

### Randomized & Divide-and-Conquer Algorithms
9. **[Randomized Min-Cut (Karger's Algorithm)](09_randomized_mincut.md)** 🔄
   - Contraction algorithm
   - Probability analysis
   - Repeated trials for correctness

10. **[Counting Inversions using Merge Sort](10_counting_inversions.md)** 🔄
    - Inversion definition and significance
    - Modified merge sort O(n log n)
    - Applications to ranking and similarity

11. **[Integer Multiplication - Karatsuba Algorithm](11_karatsuba_algorithm.md)** 🔄
    - Divide-and-conquer for multiplication
    - O(n^1.585) complexity
    - Recursion tree analysis

---

## 🎯 Learning Path

### Recommended Study Order

#### Week 1: Complexity Theory Foundation
1. **Day 1-2:** Polynomial Time Reductions
2. **Day 3-4:** NP, NP-Complete, NP-Hard
3. **Day 5:** Practice reductions and NP-proofs

#### Week 2: Dynamic Programming & Graph Algorithms
1. **Day 1-2:** Longest Increasing Subsequence
2. **Day 3-4:** Network Flow (Ford-Fulkerson)
3. **Day 5:** Bipartite Matching

#### Week 3: Advanced Topics
1. **Day 1-2:** Max-Flow Min-Cut, Perfect Matching
2. **Day 3:** TSP NP-Hardness
3. **Day 4:** Randomized Min-Cut
4. **Day 5:** Counting Inversions, Karatsuba

#### Week 4: Practice & Review
1. **Day 1-3:** Solve practice problems from each chapter
2. **Day 4-5:** Mock exam and weak area review

---

## 📊 Quick Reference

### Algorithm Complexity Cheat Sheet

| Algorithm | Time Complexity | Space | Type |
|-----------|----------------|-------|------|
| **LIS (DP)** | O(n²) | O(n) | Dynamic Programming |
| **LIS (Binary Search)** | O(n log n) | O(n) | DP + Binary Search |
| **Ford-Fulkerson** | O(E × max_flow) | O(V+E) | Graph - Network Flow |
| **Edmonds-Karp** | O(VE²) | O(V+E) | Graph - BFS |
| **Bipartite Matching** | O(VE) | O(V) | Graph - Augmenting Path |
| **Karger's Min-Cut** | O(n²) per trial | O(V+E) | Randomized |
| **Counting Inversions** | O(n log n) | O(n) | Divide & Conquer |
| **Karatsuba Multiply** | O(n^1.585) | O(log n) | Divide & Conquer |

### NP-Complete Problem Reduction Chain

```
SAT → 3-SAT → Independent Set ⇄ Clique ⇄ Vertex Cover
               ↓
        Hamilton Cycle → TSP
               ↓
        Subset Sum → Knapsack
```

---

## 🧪 Practice Problem Index

Each chapter includes:
- ✅ **Worked Examples** with complete traces
- 🔧 **Practice Problems** with hints
- 💡 **Real-World Applications**
- 📝 **Exam-Style Questions**

---

## 💻 Code Repository Structure

```
internal2/
├── 01_polynomial_time_reductions.md
├── 02_np_completeness.md
├── 03_longest_increasing_subsequence.md
├── 04_network_flow.md
├── 05_bipartite_matching.md
├── 06_max_flow_min_cut.md
├── 07_perfect_matching.md
├── 08_tsp_np_hardness.md
├── 09_randomized_mincut.md
├── 10_counting_inversions.md
├── 11_karatsuba_algorithm.md
├── code/
│   ├── reductions/
│   ├── lis/
│   ├── network_flow/
│   ├── matching/
│   └── divide_conquer/
└── practice/
    ├── problems.md
    └── solutions.md
```

---

## 📚 Textbook References

### Primary References
1. **CLRS** - Cormen, Leiserson, Rivest, Stein. *Introduction to Algorithms* (3rd ed.)
2. **KT** - Kleinberg & Tardos. *Algorithm Design*
3. **DPV** - Dasgupta, Papadimitriou, Vazirani. *Algorithms*

### Specific Chapter Mappings
- **Reductions & NP:** CLRS Ch 34, KT Ch 8, DPV Ch 8
- **LIS:** CLRS Ch 15 (DP), KT Ch 6
- **Network Flow:** CLRS Ch 26, KT Ch 7
- **Matching:** KT Ch 7.5, CLRS Ch 26
- **Randomized:** DPV Ch 2, Mitzenmacher & Upfal Ch 1

---

## 🎓 Exam Preparation Tips

### What to Focus On
1. **Algorithm Design:**
   - Understand the intuition behind each algorithm
   - Be able to trace on small examples
   - Know time and space complexity

2. **Proofs:**
   - Correctness proofs (especially for greedy and DP)
   - NP-Completeness reductions
   - Complexity analysis (Big-O, recurrence relations)

3. **Implementations:**
   - Pseudocode for all major algorithms
   - Key data structures used
   - Edge case handling

### Common Exam Question Types
- ✏️ **Trace Algorithm:** Run algorithm on given input
- 📐 **Complexity Analysis:** Derive time/space bounds
- 🔄 **Reduction:** Prove problem X reduces to Y
- 💡 **Application:** Modify algorithm for variant problem
- 📝 **Proof:** Correctness or optimality arguments

---

## 🔧 Interactive Tools

### Visualization Resources
- **VisuAlgo:** [visualgo.net](https://visualgo.net) - LIS, Network Flow, Sorting
- **Algorithm Visualizer:** [algorithm-visualizer.org](https://algorithm-visualizer.org)
- **Graph Editor:** [csacademy.com/app/graph_editor/](https://csacademy.com/app/graph_editor/)

### Online Judges
- **LeetCode:** Dynamic Programming, Graph problems
- **Codeforces:** Competitive programming practice
- **SPOJ:** Classical algorithms

---

## ✅ Progress Tracker

Track your learning progress:

- [ ] Chapter 1: Polynomial Time Reductions
- [ ] Chapter 2: NP-Completeness
- [ ] Chapter 3: Longest Increasing Subsequence
- [ ] Chapter 4: Network Flow
- [ ] Chapter 5: Bipartite Matching
- [ ] Chapter 6: Max-Flow Min-Cut
- [ ] Chapter 7: Perfect Matching
- [ ] Chapter 8: TSP NP-Hardness
- [ ] Chapter 9: Randomized Min-Cut
- [ ] Chapter 10: Counting Inversions
- [ ] Chapter 11: Karatsuba Algorithm

---

## 📞 Getting Help

If you encounter difficulties:
1. Review the worked examples in each chapter
2. Check the references for alternative explanations
3. Practice with the provided code implementations
4. Attempt practice problems progressively

---

**Legend:**
- ✅ Complete with full content
- 🔄 In progress / Coming soon
- 📊 Includes complexity analysis
- 💻 Includes code implementation
- 🧪 Includes practice problems

**Last Updated:** November 2025
