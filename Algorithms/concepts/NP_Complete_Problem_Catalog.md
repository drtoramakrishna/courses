# NP-Complete Problem Catalog

**A Quick Reference Guide for Research Students**

---

## How to Use This Catalog

This catalog provides a structured overview of classical NP-Complete problems, organized by category. Each entry includes:
- **Problem Statement**: Formal definition
- **Input/Output**: What the problem takes and returns
- **Reduction From**: Which problem is commonly used to prove NP-Completeness
- **Applications**: Real-world uses
- **Best Known Approximation**: Current state-of-the-art

---

## Category 1: Satisfiability Problems

### 1. SAT (Boolean Satisfiability)

**Definition**: Given a Boolean formula in CNF, is there a truth assignment that satisfies it?

**Input**: Boolean formula $\phi = C_1 \land C_2 \land \cdots \land C_m$ where each $C_i$ is a disjunction of literals

**Output**: YES if satisfying assignment exists, NO otherwise

**Example**:
```
φ = (x₁ ∨ ¬x₂) ∧ (x₂ ∨ x₃) ∧ (¬x₁ ∨ ¬x₃)
Solution: x₁ = T, x₂ = T, x₃ = F
```

**NP-Completeness**: Cook-Levin Theorem (1971)

**Applications**: Circuit verification, AI planning, software verification

**Approximation**: MAX-SAT is APX-hard (inapproximable beyond 7/8 for 3-SAT)

---

### 2. 3-SAT

**Definition**: SAT restricted to exactly 3 literals per clause

**Input**: CNF formula with exactly 3 literals per clause

**Output**: YES/NO

**Reduction From**: SAT ≤ₚ 3-SAT

**Why Important**: Simpler structure makes it canonical for reductions

**Applications**: Automated reasoning, theorem proving

**Special Cases**:
- 2-SAT: **Polynomial time** (linear time via implication graph)
- Horn-SAT: **Polynomial time** (unit propagation)

---

### 3. MAX-SAT

**Definition**: Find assignment satisfying maximum number of clauses

**Input**: CNF formula with $m$ clauses

**Output**: Maximum number of satisfiable clauses

**Type**: Optimization problem (NP-Hard)

**Approximation**: 
- Random assignment: 0.5-approximation
- Derandomized: 0.75-approximation
- MAX-3SAT: 7/8-approximation (tight)

---

## Category 2: Graph Problems

### 4. CLIQUE

**Definition**: Does graph contain a complete subgraph of size $k$?

**Input**: Undirected graph $G = (V, E)$, integer $k$

**Output**: YES if $\exists S \subseteq V, |S| = k$ where all pairs in $S$ are connected

**Reduction From**: 3-SAT ≤ₚ CLIQUE

**Applications**: Social network analysis, bioinformatics (protein interaction)

**Approximation**: Hard to approximate within $n^{1-\epsilon}$ (unless P = NP)

**Related**: Independent Set (complement problem)

---

### 5. INDEPENDENT SET

**Definition**: Does graph contain an independent set of size $k$?

**Input**: Graph $G = (V, E)$, integer $k$

**Output**: YES if $\exists S \subseteq V, |S| = k$ with no edges between vertices in $S$

**Reduction**: CLIQUE ≤ₚ INDEPENDENT-SET (via complement graph)

**Applications**: Radio frequency assignment, coding theory

**Approximation**: Hard to approximate within $n^{1-\epsilon}$

---

### 6. VERTEX COVER

**Definition**: Can we cover all edges with $k$ vertices?

**Input**: Graph $G = (V, E)$, integer $k$

**Output**: YES if $\exists S \subseteq V, |S| \leq k$ where every edge has $\geq 1$ endpoint in $S$

**Reduction From**: CLIQUE ≤ₚ VERTEX-COVER

**Key Relationship**: $S$ is vertex cover $\iff$ $V \setminus S$ is independent set

**Approximation**: 2-approximation (tight under Unique Games Conjecture)

**FPT**: $O(1.2738^k + kn)$ algorithm exists

---

### 7. HAMILTONIAN PATH/CYCLE

**Definition**: Does graph contain path/cycle visiting each vertex exactly once?

**Input**: Graph $G = (V, E)$

**Output**: YES if such path/cycle exists

**Reduction From**: VERTEX-COVER ≤ₚ HAM-CYCLE

**Applications**: 
- DNA sequencing
- Routing in networks
- Robot motion planning

**Special Cases**:
- Trees: Always NO for cycle, trivial for path
- Complete graphs: Always YES
- Grid graphs: Polynomial for some restrictions

**Approximation**: Not approximable (decision problem)

---

### 8. TRAVELING SALESMAN PROBLEM (TSP)

**Definition**: Find shortest tour visiting all cities exactly once

**Decision Version**: Is there tour of length $\leq k$?

**Input**: Complete graph with edge weights, bound $k$

**Output**: YES if tour $\leq k$ exists

**Reduction From**: HAM-CYCLE ≤ₚ TSP

**Approximation**:
- **General TSP**: Inapproximable within any factor (unless P = NP)
- **Metric TSP**: 1.5-approximation (Christofides algorithm)
- **Euclidean TSP**: PTAS exists

**Applications**: Logistics, manufacturing, genome sequencing

---

### 9. GRAPH COLORING

**Definition**: Can graph be colored with $k$ colors (no adjacent vertices same color)?

**Input**: Graph $G = (V, E)$, integer $k$

**Output**: YES if valid $k$-coloring exists

**Reduction From**: 3-SAT ≤ₚ 3-COLORING

**Special Cases**:
- $k = 2$: Polynomial (check if bipartite)
- $k = 3$: NP-Complete
- Planar graphs, $k \geq 5$: Always YES (4-color theorem)
- Planar 3-coloring: NP-Complete

**Approximation**: Hard to approximate within $n^{1-\epsilon}$

**Applications**: Register allocation, scheduling, frequency assignment

---

### 10. DOMINATING SET

**Definition**: Find set $S$ where every vertex is in $S$ or adjacent to vertex in $S$

**Input**: Graph $G = (V, E)$, integer $k$

**Output**: YES if dominating set of size $\leq k$ exists

**Reduction From**: VERTEX-COVER ≤ₚ DOMINATING-SET

**Applications**: Facility location, network monitoring

**Approximation**: $\ln n$-approximation

**FPT**: $O(k^k \cdot n)$ algorithm

---

## Category 3: Subset and Partition Problems

### 11. SUBSET SUM

**Definition**: Does subset sum to target?

**Input**: Set $S = \{a_1, \ldots, a_n\}$, target $t$

**Output**: YES if $\exists T \subseteq S$ where $\sum_{x \in T} x = t$

**Reduction From**: 3-SAT ≤ₚ SUBSET-SUM

**Complexity**: 
- NP-Complete in general
- Pseudo-polynomial: $O(n \cdot t)$ via DP
- FPTAS exists: $(1 + \epsilon)$-approximation

**Applications**: Cryptography, resource allocation

**Variants**:
- Exact: As defined above
- Decision: As above
- Counting: #P-complete

---

### 12. PARTITION

**Definition**: Can set be partitioned into two equal-sum subsets?

**Input**: Set $S = \{a_1, \ldots, a_n\}$

**Output**: YES if $\exists T \subseteq S$ where $\sum_{x \in T} x = \sum_{x \in S \setminus T} x$

**Reduction From**: SUBSET-SUM ≤ₚ PARTITION

**Special Case of**: SUBSET-SUM with $t = \frac{1}{2} \sum_{i} a_i$

**Applications**: Load balancing, fair division

**Pseudo-polynomial**: $O(n \cdot \sum a_i)$

---

### 13. BIN PACKING

**Definition**: Pack items into minimum number of bins

**Decision Version**: Can items fit in $k$ bins?

**Input**: Items with sizes $s_1, \ldots, s_n$, bin capacity $C$, integer $k$

**Output**: YES if items fit in $k$ bins

**Reduction From**: PARTITION ≤ₚ BIN-PACKING

**Approximation**:
- First Fit Decreasing: 11/9 OPT + 6/9
- APTAS exists (asymptotic PTAS)

**Applications**: Memory allocation, container loading

---

### 14. KNAPSACK

**Definition**: Maximize value in knapsack of capacity $W$

**Decision Version**: Can we achieve value $\geq V$ with weight $\leq W$?

**Input**: Items with weights $w_i$, values $v_i$, capacity $W$, target $V$

**Output**: YES if achievable

**Reduction From**: SUBSET-SUM ≤ₚ KNAPSACK

**Complexity**:
- Pseudo-polynomial: $O(nW)$ DP
- FPTAS: $(1 + \epsilon)$-approximation in $O(n^3/\epsilon)$

**Variants**:
- 0/1 Knapsack: NP-Complete
- Fractional Knapsack: Polynomial (greedy)
- Multiple Knapsack: NP-Complete

---

## Category 4: Sequencing and Scheduling

### 15. JOB SCHEDULING

**Definition**: Schedule jobs on machines to minimize makespan

**Input**: Jobs with processing times $p_j$, $m$ machines

**Output**: Schedule minimizing completion time

**Variant**: MULTIPROCESSOR SCHEDULING

**Reduction From**: PARTITION ≤ₚ SCHEDULING

**Approximation**:
- List Scheduling: 2-approximation
- LPT (Longest Processing Time): 4/3-approximation
- PTAS exists for fixed $m$

**Applications**: Manufacturing, cloud computing, parallel processing

---

### 16. SEQUENCING WITH DEADLINES

**Definition**: Maximize number of jobs completed by deadlines

**Input**: Jobs with deadlines $d_j$, processing times $p_j$

**Output**: Maximum jobs completable

**Applications**: Project management, assembly lines

---

## Category 5: Set and Covering Problems

### 17. SET COVER

**Definition**: Cover all elements using minimum sets

**Input**: Universe $U$, collection $\mathcal{S} = \{S_1, \ldots, S_m\}$ where $S_i \subseteq U$, integer $k$

**Output**: YES if $\leq k$ sets cover $U$

**Reduction From**: VERTEX-COVER ≤ₚ SET-COVER

**Approximation**:
- Greedy: $\ln n$-approximation (tight)
- Cannot approximate within $c \ln n$ (unless P = NP)

**Applications**: Facility location, resource allocation, test case selection

**Dual**: SET-PACKING

---

### 18. HITTING SET

**Definition**: Hit all sets with minimum elements

**Input**: Collection $\mathcal{S}$, integer $k$

**Output**: YES if $\exists H, |H| \leq k$ where $H \cap S_i \neq \emptyset$ for all $S_i$

**Relationship**: Dual of SET-COVER

**Applications**: Database queries, test set minimization

---

### 19. EXACT COVER (X3C)

**Definition**: Partition elements using disjoint sets

**Input**: Universe $U$ with $|U| = 3q$, collection $\mathcal{S}$ where $|S_i| = 3$

**Output**: YES if $\exists \mathcal{C} \subseteq \mathcal{S}$ where sets partition $U$

**Reduction From**: 3-SAT ≤ₚ EXACT-COVER

**Applications**: Sudoku solving, tiling problems

**Special**: Used in Dancing Links algorithm (Knuth)

---

## Category 6: Numerical Problems

### 20. INTEGER PROGRAMMING

**Definition**: Find integer solution to linear program

**Input**: Matrix $A$, vectors $b$, $c$

**Output**: Integer vector $x$ maximizing $c^T x$ subject to $Ax \leq b$

**Complexity**: NP-Complete (even with 0/1 variables)

**Approximation**: 
- LP relaxation gives fractional solution
- Rounding techniques

**Applications**: Resource allocation, scheduling, network design

**Special Cases**:
- **Linear Programming**: Polynomial (simplex, interior point)
- **0/1 Integer Programming**: NP-Complete

---

## Category 7: Network and Flow Problems

### 21. STEINER TREE

**Definition**: Connect terminals with minimum-cost tree

**Input**: Graph $G = (V, E)$ with costs, terminal set $T \subseteq V$

**Output**: Minimum-cost tree spanning $T$

**Approximation**: 
- 2-approximation via MST
- 1.39-approximation (Byrka et al.)

**Applications**: VLSI routing, network design, phylogenetic trees

**Special Cases**:
- Euclidean Steiner: PTAS exists
- Rectilinear Steiner: NP-Complete

---

### 22. DIRECTED HAMILTONIAN CYCLE

**Definition**: Hamiltonian cycle in directed graph

**Input**: Directed graph $G = (V, A)$

**Output**: YES if directed cycle visits all vertices once

**Reduction From**: UNDIRECTED-HAM-CYCLE ≤ₚ DIRECTED-HAM-CYCLE

**Harder Than**: Undirected version (cannot simply replace edges)

---

## Category 8: Logic and Games

### 23. CIRCUIT-SAT

**Definition**: Is there input making Boolean circuit output TRUE?

**Input**: Boolean circuit

**Output**: YES if satisfying input exists

**Fundamental**: Used in Cook-Levin proof

**Applications**: Hardware verification, logic synthesis

---

### 24. QUANTIFIED BOOLEAN FORMULA (QBF)

**Definition**: Evaluate fully quantified Boolean formula

**Input**: $\forall x_1 \exists x_2 \forall x_3 \cdots \phi(x_1, x_2, \ldots)$

**Complexity**: **PSPACE-Complete** (harder than NP-Complete!)

**Applications**: AI planning, game theory, verification

---

## Quick Reference: Reduction Chain

```
SAT
 ├→ 3-SAT
 │   ├→ CLIQUE
 │   │   ├→ INDEPENDENT-SET
 │   │   └→ VERTEX-COVER
 │   │       ├→ SET-COVER
 │   │       └→ DOMINATING-SET
 │   ├→ 3-COLORING
 │   └→ SUBSET-SUM
 │       ├→ PARTITION
 │       │   ├→ BIN-PACKING
 │       │   └→ SCHEDULING
 │       └→ KNAPSACK
 └→ HAM-CYCLE
     └→ TSP
```

---

## Research-Worthy Variants

### Open Problems and Active Research Areas

1. **Parameterized Complexity**
   - Which NP-Complete problems have FPT algorithms?
   - Kernelization lower bounds

2. **Approximation Hardness**
   - Tight inapproximability results
   - Unique Games Conjecture implications

3. **Special Graph Classes**
   - Planar graphs
   - Bounded treewidth
   - Chordal graphs

4. **Practical Algorithms**
   - SAT solver improvements
   - Branch-and-bound enhancements
   - Hybrid approaches

5. **Quantum Algorithms**
   - Grover's algorithm applications
   - Quantum annealing for optimization

---

## Problem Selection Guide

**For Learning Reductions**: Start with 3-SAT → CLIQUE → VERTEX-COVER

**For Approximation Study**: VERTEX-COVER, SET-COVER, TSP

**For Parameterized Complexity**: VERTEX-COVER, DOMINATING-SET

**For Practical Implementation**: SAT, SUBSET-SUM, KNAPSACK

**For Theoretical Depth**: CIRCUIT-SAT, Graph Isomorphism (GI)

---

## Further Reading

- **Garey & Johnson** (1979): Complete catalog of 300+ NP-Complete problems
- **Karp's 21 Problems** (1972): Original reductions
- **Complexity Zoo**: https://complexityzoo.net/

---

**Last Updated**: November 2025  
**Maintained by**: Algorithms Research Group
