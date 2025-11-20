# Research Exercises: P, NP, and NP-Complete Problems

**Graduate Research Workbook**

---

## Overview

This workbook contains progressive exercises designed to deepen your understanding of computational complexity, NP-Completeness, and algorithmic problem-solving. Exercises range from theoretical proofs to practical implementation.

---

## Part 1: Foundational Understanding

### Exercise 1.1: Complexity Classes

**Objective**: Understand the relationships between complexity classes.

**Tasks**:
1. Prove that $P \subseteq NP$
2. Explain why $NP \subseteq PSPACE$
3. Draw a Venn diagram showing the relationships between P, NP, co-NP, and NP-Complete
4. Research: What is the class co-NP? Give an example of a co-NP problem.

**Deliverable**: 2-page written proof with diagrams

---

### Exercise 1.2: Verifiers vs Solvers

**Objective**: Distinguish between verification and solving.

**Tasks**:
1. For the CLIQUE problem, write:
   - A polynomial-time verifier algorithm (pseudocode)
   - Analyze its time complexity
   - Prove it runs in polynomial time

2. Explain why having a poly-time verifier doesn't immediately give us a poly-time solver

3. Give an example where the verifier is much simpler than any known solver

**Deliverable**: Pseudocode + complexity analysis

---

### Exercise 1.3: Certificate Analysis

**Objective**: Understand the role of certificates in NP.

For each problem, design a certificate and verifier:
1. SUBSET-SUM
2. HAMILTONIAN-PATH
3. 3-SAT
4. VERTEX-COVER
5. GRAPH-ISOMORPHISM (research problem)

**Format for each**:
- Certificate structure (what does it contain?)
- Certificate size (in terms of input size)
- Verifier algorithm (pseudocode)
- Time complexity analysis

**Deliverable**: Technical report with 5 verifier specifications

---

## Part 2: Reduction Techniques

### Exercise 2.1: Classic Reductions

**Objective**: Master the art of polynomial-time reductions.

**Task A**: Prove 3-SAT ≤ₚ CLIQUE

Complete the following steps:
1. Given a 3-SAT instance with $m$ clauses and $n$ variables
2. Construct a graph $G$ (describe vertex and edge creation)
3. Prove: $\phi$ is satisfiable $\iff$ $G$ has an $m$-clique
4. Analyze transformation time complexity
5. Implement in C or Python

**Task B**: Prove CLIQUE ≤ₚ VERTEX-COVER

Use the complement graph technique.

**Deliverable**: 
- Written proofs (5 pages)
- Code implementation of reductions
- Test cases demonstrating correctness

---

### Exercise 2.2: Design Your Own Reduction

**Objective**: Create a reduction from scratch.

**Problem**: Prove that SUBSET-SUM ≤ₚ PARTITION

**Steps**:
1. Understand both problems deeply
2. Design the transformation function $f$
3. Prove correctness: $x \in \text{SUBSET-SUM} \iff f(x) \in \text{PARTITION}$
4. Prove polynomial-time transformation
5. Implement and test

**Challenge**: Find an alternative reduction path (not the standard one)

**Deliverable**: 
- Complete reduction proof
- Implementation with test suite
- Complexity analysis

---

### Exercise 2.3: Reduction Chain

**Objective**: Build a chain of reductions.

Construct a reduction chain showing:

$$\text{SAT} \to \text{3-SAT} \to \text{CLIQUE} \to \text{VERTEX-COVER} \to \text{SET-COVER}$$

For each arrow:
1. Describe the reduction
2. Prove correctness
3. Show polynomial-time transformation

**Deliverable**: 10-page comprehensive document

---

## Part 3: Algorithm Design and Implementation

### Exercise 3.1: SAT Solver Enhancement

**Objective**: Improve the basic DPLL SAT solver.

**Base Code**: Use `sat_solver.c` provided in course materials.

**Enhancements to Implement**:

1. **Conflict-Driven Clause Learning (CDCL)**
   - Learn clauses from conflicts
   - Implement non-chronological backtracking

2. **Variable Ordering Heuristics**
   - Implement VSIDS (Variable State Independent Decaying Sum)
   - Compare with random ordering

3. **Restarts**
   - Implement periodic restarts
   - Experiment with restart strategies

4. **Preprocessing**
   - Subsumption elimination
   - Variable elimination

**Benchmarking**:
- Test on SATLIB benchmark instances
- Compare performance metrics:
  - Time to solution
  - Number of decisions
  - Number of conflicts
  - Learned clause count

**Deliverable**: 
- Enhanced C implementation
- Performance analysis report
- Comparison graphs

---

### Exercise 3.2: Approximation Algorithm Design

**Objective**: Design and analyze approximation algorithms.

**Task A: Vertex Cover 2-Approximation**

1. Implement the maximal matching algorithm
2. Prove the 2-approximation ratio
3. Test on various graph types
4. Find instances where ratio approaches 2

**Task B: Set Cover Greedy Algorithm**

1. Implement greedy set cover
2. Prove $\ln n$-approximation ratio
3. Show this is tight (construct bad instance)
4. Compare with ILP solution

**Task C: TSP Approximations**

1. Implement Christofides algorithm (1.5-approximation for metric TSP)
2. Implement 2-approximation via MST
3. Compare on random Euclidean instances

**Deliverable**:
- Three implementations
- Formal approximation ratio proofs
- Experimental comparison report

---

### Exercise 3.3: Exact Algorithms with Branch-and-Bound

**Objective**: Implement sophisticated exact algorithms.

**Problem**: Traveling Salesman Problem

**Requirements**:
1. Implement branch-and-bound with:
   - MST lower bound
   - 1-tree lower bound
   - Nearest neighbor upper bound

2. Implement pruning strategies:
   - Alpha-beta pruning
   - Best-first search

3. Compare with:
   - Brute force enumeration
   - Held-Karp dynamic programming

**Deliverable**:
- Complete implementation
- Performance analysis on TSP instances (10-20 cities)
- Analysis of pruning effectiveness

---

## Part 4: Parameterized Complexity

### Exercise 4.1: Fixed-Parameter Tractability

**Objective**: Explore FPT algorithms.

**Task**: Implement FPT algorithm for VERTEX-COVER

**Algorithm**: Bounded search tree with kernelization

```
VertexCoverFPT(G, k):
  1. Kernelization: reduce to kernel of size O(k²)
  2. Bounded search tree: O(2^k) branching
  3. Total time: O(2^k + k²n)
```

**Implementation Steps**:
1. Implement kernelization rules:
   - Remove degree-0 vertices
   - Include degree > k vertices in cover
   - Remove dominated vertices

2. Implement bounded search tree:
   - Branch on high-degree vertex
   - Recursive case analysis

3. Compare with brute force for small k

**Deliverable**:
- FPT implementation
- Experimental analysis: vary k from 1 to 20
- Comparison with approximation algorithm

---

### Exercise 4.2: Kernelization

**Objective**: Master kernelization techniques.

**Problems**:
1. VERTEX-COVER: Reduce to $O(k^2)$ kernel
2. FEEDBACK-VERTEX-SET: Research and implement
3. DOMINATING-SET: Attempt kernelization

**For each**:
- Prove kernel size bound
- Implement kernelization algorithm
- Verify correctness

**Deliverable**: Comparative study of kernelization techniques

---

## Part 5: Advanced Topics

### Exercise 5.1: Hardness of Approximation

**Objective**: Understand inapproximability results.

**Research Tasks**:

1. **PCP Theorem**:
   - Read seminal papers
   - Understand connection to hardness of approximation
   - Present implications for MAX-3SAT

2. **Gap Reductions**:
   - Study gap-preserving reductions
   - Prove: MAX-3SAT is hard to approximate within 7/8 + ε

3. **Unique Games Conjecture**:
   - Research current status
   - Understand implications for VERTEX-COVER

**Deliverable**: 
- 15-page research survey
- Presentation (30 minutes)

---

### Exercise 5.2: Special Graph Classes

**Objective**: Study tractable restrictions of NP-Complete problems.

**Tasks**:

1. **Planar Graphs**:
   - Prove 3-COLORING is NP-Complete for planar graphs
   - Show 4-COLORING is in P for planar graphs
   - Implement efficient planar graph algorithms

2. **Trees**:
   - Show VERTEX-COVER is polynomial on trees
   - Implement linear-time algorithm
   - Prove correctness via DP

3. **Bounded Treewidth**:
   - Define treewidth
   - Show many NP-Complete problems become FPT
   - Implement algorithm for one problem

**Deliverable**: Technical report + implementations

---

### Exercise 5.3: Randomized Algorithms

**Objective**: Explore randomization for NP-Complete problems.

**Task A: Randomized SAT Algorithms**

1. Implement PPSZ algorithm
2. Implement random walk for 3-SAT
3. Analyze expected running time

**Task B: Randomized Rounding**

1. Solve LP relaxation of VERTEX-COVER
2. Implement randomized rounding
3. Analyze expected approximation ratio

**Deliverable**: Implementations + probabilistic analysis

---

## Part 6: Real-World Applications

### Exercise 6.1: Constraint Satisfaction Problems

**Objective**: Apply NP-Complete theory to practical CSPs.

**Project**: Sudoku Solver

**Implementation**:
1. Reduce Sudoku to SAT
2. Use your SAT solver to solve Sudoku
3. Optimize for typical Sudoku instances

**Extensions**:
- Implement specialized Sudoku algorithms
- Compare SAT-based vs specialized approaches
- Generate hard Sudoku instances

**Deliverable**: 
- Complete Sudoku solver
- Performance comparison
- Hard instance generator

---

### Exercise 6.2: Scheduling Problem

**Objective**: Solve real-world scheduling as NP-Complete problem.

**Scenario**: University Course Scheduling

**Constraints**:
- No student has time conflicts
- Room capacities
- Professor availability
- Time slot preferences

**Tasks**:
1. Model as graph coloring problem
2. Model as constraint satisfaction problem
3. Model as integer linear program
4. Implement and compare solutions

**Deliverable**: 
- Three different models
- Implementation of best approach
- Analysis on real university data

---

### Exercise 6.3: Network Design

**Objective**: Apply Steiner Tree and related problems.

**Project**: Minimal Network Infrastructure

**Problem**:
- Given locations (terminals) that must be connected
- Given possible intermediate nodes
- Minimize total cable length

**Tasks**:
1. Prove this is Steiner Tree problem
2. Implement 2-approximation via MST
3. Implement better approximation if possible
4. Test on real geographic data

**Deliverable**: Complete implementation + case study

---

## Part 7: Research Project

### Exercise 7.1: Original Research

**Objective**: Contribute to NP-Complete research.

**Choose One Track**:

**Track A: Improved Approximation**
- Choose an NP-Complete problem
- Design improved approximation algorithm
- Prove approximation ratio
- Implement and benchmark

**Track B: Parameterized Complexity**
- Choose a problem
- Find meaningful parameter
- Design FPT algorithm or prove W[1]-hardness
- Implement if FPT

**Track C: Special Cases**
- Identify restricted version of NP-Complete problem
- Prove polynomial-time solvability
- Design efficient algorithm
- Compare with general case

**Track D: Practical Heuristics**
- Choose practical NP-Complete problem
- Design novel heuristic/metaheuristic
- Extensive experimental evaluation
- Compare with state-of-the-art

**Deliverable**: 
- Research paper (10-15 pages)
- Implementation
- Experimental results
- Conference-style presentation

---

## Evaluation Rubric

### Theory Exercises (40%)
- Correctness of proofs
- Clarity of exposition
- Depth of understanding

### Implementation (30%)
- Code quality and documentation
- Correctness of implementation
- Efficiency

### Experimentation (20%)
- Experimental design
- Data collection and analysis
- Visualization

### Research/Creativity (10%)
- Novel insights
- Independent thinking
- Connection to broader context

---

## Resources and Tools

### Recommended Software
- **SAT Solvers**: MiniSat, CryptoMiniSat, Z3
- **Graph Libraries**: NetworkX (Python), Boost Graph (C++)
- **Optimization**: CPLEX, Gurobi, OR-Tools
- **Visualization**: Graphviz, matplotlib

### Datasets
- **SATLIB**: SAT benchmarks
- **DIMACS**: Graph problems
- **TSPLIB**: TSP instances
- **Benchmarks**: https://www.csplib.org/

### Reading List
1. Garey & Johnson - "Computers and Intractability"
2. Arora & Barak - "Computational Complexity"
3. Vazirani - "Approximation Algorithms"
4. Downey & Fellows - "Parameterized Complexity"

---

## Timeline (Suggested 12-Week Schedule)

| Week | Topics | Exercises |
|------|--------|-----------|
| 1-2 | Foundations | 1.1, 1.2, 1.3 |
| 3-4 | Reductions | 2.1, 2.2, 2.3 |
| 5-6 | Algorithms | 3.1, 3.2, 3.3 |
| 7-8 | Parameterized | 4.1, 4.2 |
| 9-10 | Advanced | 5.1, 5.2, 5.3 |
| 11 | Applications | 6.1, 6.2, 6.3 |
| 12 | Research | 7.1 |

---

## Collaboration Policy

- Individual work required for exercises 1.x - 3.x
- Pairs allowed for exercises 4.x - 6.x
- Teams of 2-3 for exercise 7.1

**Academic Integrity**: Cite all sources. Discuss ideas freely, but write independently.

---

**Good luck with your research journey into the fascinating world of computational complexity!**

---

*Document Version: 1.0*  
*Course: Advanced Algorithms - P and NP*  
*Level: Graduate Research*
