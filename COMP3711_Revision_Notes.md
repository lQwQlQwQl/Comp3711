# COMP 3711 — Design and Analysis of Algorithms
## Midterm Revision Notes (Condensed Knowledge + Thinking)

> **Sources synthesised:** `01_Prerequisite`, `02_BinarySearchA`, `03_Mergesort` + Merge/Mergesort/Inversion examples, `04_Subarray`, `05_Multiplication`, `06_Random`, `07_Quicksort`, `08_Heap`, `09_Linearsort`, plus the Fall-2025 midterm solution, the Spring-2026 midterm solution, and the Fall-2026 HW2 solution.
>
> **How to use:** Part 0 is the toolkit. Parts 1–7 are the syllabus. Part 8 is the exam-pattern playbook. Part 9 is the one-page fact dump to memorise last.

---

# PART 0 — The Toolkit: How This Course Thinks

## 0.1 The three questions behind every algorithm

Every answer in this course is one of three shapes. Learn to recognise which one is being asked:

| Question type | What the marker wants | Signature phrase |
|---|---|---|
| **Design** | Pseudocode + complexity | "Design an algorithm… analyze its running time" |
| **Analysis** | A recurrence, a tree, a sum, then Θ | "Derive the exact asymptotic solution… show all steps" |
| **Correctness / Lower bound** | An invariant, an exchange, or an information count | "Explain why…" / "Show that it takes Ω(…)" |

**Thinking habit:** never start with code. Start by asking *what structure does the input have that I can exploit?* Sortedness → binary search. Repeated elements → contiguous blocks. Distinctness → ranks z₁…zₙ. Range-limited keys → direct addressing. Uniform random → bucketing.

## 0.2 Asymptotic notation — the exact definitions

- `f(n) = O(g(n))` iff ∃ c, n₀ > 0 with 0 ≤ f(n) ≤ c·g(n) for all n ≥ n₀. *Upper bound — "f is dominated by g".*
- `f(n) = Ω(g(n))` iff ∃ c, n₀ > 0 with f(n) ≥ c·g(n) ≥ 0 for all n ≥ n₀. *Lower bound — "f dominates g".*
- `f(n) = Θ(g(n))` **iff** f(n) = O(g(n)) **and** f(n) = Ω(g(n)). *Tight bound.*
- `f(n) = o(g(n))` iff lim f/g = 0 (strictly smaller); `ω` is the mirror. **Note: `O` is not `o`.**

**Proof template for `max(f,g) = Θ(f+g)`** (a classic exercise):
- `max(f,g) ≤ f + g` for all n → `max(f,g) = O(f+g)`.
- `max(f,g) ≥ f` and `≥ g` → `2·max(f,g) ≥ f + g` → `max(f,g) ≥ ½(f+g)` → `= Ω(f+g)`.

**Bounds you must be able to prove from scratch, not quote:**

| Series | Bound | The trick |
|---|---|---|
| `Σᵢ₌₁ⁿ i` | `Θ(n²)` | Lower: drop terms < n/2, replace rest by n/2 → n²/4 |
| `Σᵢ₌₁ⁿ iᶜ` | `Θ(n^{c+1})` | Same halving argument, general c |
| `Σᵢ₌₁ⁿ 1/i = Hₙ` | `Θ(log n)` | Group into blocks of size 2ᵏ; each block sums to Θ(1); log n blocks |
| `Σᵢ₌₀ⁿ rⁱ` | `Θ(1)` if r<1; `Θ(n)` if r=1; `Θ(rⁿ)` if r>1 | Dominant term is the *top* when increasing, the *bottom* when decreasing |
| `n!` | `Θ((n/e)ⁿ)` (Stirling); useful bounds `n! ≤ nⁿ`, `n! ≥ (n/2)^{n/2}` | Only the easy directions are ever asked |

**Working with `n!` bounds is a favourite:** `n! ≤ nⁿ = O(nⁿ)` and `n! ≥ (n/2)^{n/2} = Ω(n^{n/2})`. The full Stirling form `Θ((n/e)ⁿ)` is stated in lecture but never required to derive.

## 0.3 Growth hierarchy and the trap cases

```
constant ≺ logarithmic ≺ polynomial ≺ exponential ≺ factorial
9999^99999999 ≺ log₁₀n ≺ n^0.1 ≺ n log n ≺ n² ≺ 2ⁿ ≺ n!
```

**Traps that appear in the "which is faster" tables:**

- **Log factors are invisible to polynomials but decisive between same-power terms.** `Θ(n²/(log n)^10)` *beats* `Θ(n^1.5)` because `n^0.5/(log n)^10 → ∞`. Conversely a `(log n)^k` factor can never rescue a smaller polynomial power.
- **Exponential bases are compared, not exponents.** `5ⁿ` beats `7ⁿ`. But `2^{√n}` vs `10^{n^{1/3}}`: compare the exponents — `n^{1/3}` eventually crushes `√n`, so `10^{n^{1/3}}` is astronomically larger.
- **`2^{2n} ≠ Θ(2ⁿ)`.** Set x = 2ⁿ: x² ≠ Θ(x). *(This is the `(c^m)^n = (c^n)^m` trap.)*
- **`2^{n+2} = Θ(2ⁿ)`** — constant shifts in the exponent vanish.
- **A `log` in the exponent means polynomial:** `a^{log_b n} = n^{log_b a}`. Hence `4^{log₂n} = n²`.
- **Base of logarithm is irrelevant:** `log_b a = log_c a / log_c b`, so `log₁₀n = Θ(log₂n) = Θ(log n)`.
- **Unbounded-bounds trap (exam-critical).** If you are told `T_A(n) = O(log n)` and `T_B(n) = Ω(log log n)`, you **cannot** conclude anything — O and Ω are *one-sided promises*, and `log n` and `log log n` are not even the same growth class. **Answer "U" (unsufficient information).** This was Spring-2026 Q1(a) case 3.
- **A recurrence in disguise is still a recurrence.** Spring-2026 Q1(a) case 5 gave `T_A = Θ(n²)` against `T_B(n) = 2T_B(n/2)+n`. Solve the recurrence first: `T_B = Θ(n log n)`, which beats `Θ(n²)`, so **B** is faster. Never compare against an unsolved recurrence.

## 0.4 Solving recurrences — four tools, all examinable

### (a) Expansion / substitution (back-substitution)
Repeatedly substitute and look for the pattern.

**Worked exam example (Spring-2026 Q1b).** `T(n) = 64·T(n/4) + n³`, `T(1)=1`, n a power of 4. **No Master Theorem allowed.**
```
T(n) = 4³·T(n/4) + n³
     = 4⁶·T(n/4²) + 4³·(n/4)³ + n³  = 4⁶·T(n/4²) + 2n³
     = 4⁹·T(n/4³) + 4⁶·(n/4²)³ + 2n³ = 4⁹·T(n/4³) + 3n³
     = 4^{3k}·T(n/4^k) + k·n³
```
Boundary when `k = log₄n`, then `4^{3k} = (4^{log₄n})³ = n³`. So `T(n) = n³ + (log₄n)·n³ = Θ(n³ log n)`, i.e. `α=3, β=1`.

> **Pattern:** at every level the *extra* term is exactly `n³` because `4³·(n/4)³ = n³`. Whenever `a·(1/b)^c = 1` for `f(n)=n^c`, every level costs the same and the answer carries a `log` factor. This is the recurrence-level statement of Master Theorem case 2.

### (b) Recursion tree (the method the exam asks you to *draw*)
For `T(n) = aT(n/b) + f(n)`:

| Level i | # nodes | work per node | work per level |
|---|---|---|---|
| 0 | `1` | `f(n)` | `f(n)` |
| 1 | `a` | `f(n/b)` | `a·f(n/b)` |
| 2 | `a²` | `f(n/b²)` | `a²·f(n/b²)` |
| i | `aⁱ` | `f(n/bⁱ)` | `aⁱ·f(n/bⁱ)` |
| `log_b n` | `a^{log_b n} = n^{log_b a}` | `T(1)` | `n^{log_b a}` |

Total `= n^{log_b a} + Σ_{i=0}^{log_b n −1} aⁱ f(n/bⁱ)`.

**Worked exam example (Fall-2025 Q1a).** `T(n) = 4T(n/4) + n log₄n`, `T(1)=1`. Level 0: 1 node, cost `n log₄n`. Level 1: 4 nodes, each `(n/4)log₄(n/4)`, level total `n log₄(n/4)`. Level 2: 4² nodes, level total `n log₄(n/4²)`. General level i: `4ⁱ` nodes, level total `n log₄(n/4ⁱ)`. Leaves: `4^{log₄ n} = n`, each cost 1, leaf total `n`. Therefore
```
T(n) = n + Σ_{i=0}^{log₄n − 1} n·log₄(n/4ⁱ) = n + n(log₄n)² − n·Σ i
     = n + n(log₄n)² − ½ n log₄n(log₄n − 1) = Θ(n (log n)²).
```
> **Marking checklist for this question type** (it is stated verbatim on the paper): draw level 0 and level 1; show level number + per-node cost + level sum for levels 0,1,2; give #nodes and level sum for general level i; give #leaves and leaf-cost sum as a polynomial in n; only then state `T(n) = Θ(n^α (log n)^β)`.

### (c) Master Theorem
**Simplified form (as taught):** `T(n) = aT(n/b) + n`, a ≥ 1, b > 1 constants.

| Case | Condition | Result | Which level dominates |
|---|---|---|---|
| 1 | `log_b a > 1` (a > b) | `Θ(n^{log_b a})` | **Leaves** (increasing geometric series) |
| 2 | `log_b a = 1` (a = b) | `Θ(n log n)` | **Every level** contributes n |
| 3 | `log_b a < 1` (a < b) | `Θ(n)` | **Root** (decreasing geometric series) |

**Proof idea (know it):** total `= n^{log_b a} + n·Σ_{i=0}^{log_b n−1}(a/b)ⁱ`. The geometric ratio `a/b` decides whether the sum is `Θ((a/b)^{log_b n}) = Θ(n^{log_b a}/n)`, `Θ(log n)`, or `Θ(1)`.

**Full form** with general `f(n)`: compare `f(n)` with `n^{log_b a}`; if `f` is polynomially larger (and regular) → `Θ(f(n))`; if equal → `Θ(n^{log_b a} log n)`; if polynomially smaller → `Θ(n^{log_b a})`.

**Applied in HW2 Q1:** `T(m) = T(m/2) + cm`. Here `a=1, b=2, log₂1 = 0`, so `n^{log_b a} = m⁰ = 1` and `f(m)=cm = Ω(m^{0+1})` → Case 3, regularity `c(m/2) ≤ ½cm` holds → `T(m) = Θ(m)`.

**Master Theorem is banned when the question says so.** Spring-2026 Q1(b) explicitly says *"DO NOT use the Master Theorem for this part"* — you must expand.

### (d) Recursion tree for irregular recurrences
`T(n) = 2T(n−1) + 1` (Towers of Hanoi): level i has `2ⁱ` nodes, `n−1` levels, total `Σ_{i=0}^{n−1}2ⁱ = 2ⁿ − 1`.

> **Note the distinction:** `T(n)=2T(n−1)+1` is `Θ(2ⁿ)` (subproblems shrink by 1, count doubles) while `T(n)=2T(n/2)+n` is `Θ(n log n)`.

## 0.5 Lower bounds: the decision-tree model

**Model.** The algorithm only asks binary (Yes/No) questions; each internal node is a comparison, each leaf an output. **Worst-case running time = height of the tree.** FACT: a binary tree with N leaves has height ≥ log₂N.

**Two lower bounds to reproduce exactly:**

**(i) Searching a sorted array is `Ω(log n)`.** The algorithm must produce n+1 distinct outputs (position 1..n, or "not found") → ≥ n+1 leaves → height ≥ log₂(n+1) = Ω(log n).

**(ii) Comparison sorting is `Ω(n log n)`.** A correct sorting algorithm must distinguish all n! permutations → ≥ n! leaves → height ≥ log₂(n!) = Ω(n log n) using `n! ≥ (n/2)^{n/2}`. Hence **merge sort and heapsort are asymptotically optimal comparison sorts**.

**(iii) Generalised rank selection (Fall-2025 Q1b).** Outputting the k smallest of n distinct integers in increasing order requires `Ω(k log n)` comparisons. Proof: the output is a choice of which k elements `C(n,k)` *and* their order `k!`, giving `C(n,k)·k! = n!/(n−k)! = n(n−1)···(n−k+1) ≥ (n−k+1)^k` possible outputs. With `k ≤ n/2`, `n−k+1 > n/2`, so the tree height is `Ω(log (n/2)^k) = Ω(k log n − k) = Ω(k log n)`.

**(iv) Ternary variant — the coin-weighing exercise.** With a balance scale there are 3 outcomes per weighing. Outcomes: no fake (1) or one of n coins heavy/light (2n) → `1+2n` leaves → ternary tree height ≥ `log₃(1+2n)`. Achievable in `T(n) = T(n/3) + 2 = 2 log₃ n` by splitting into three equal sets and using 2 weighings to isolate which third (and whether heavy or light).

> **Algorithm lower bound vs problem lower bound.** "Insertion sort is `Ω(n²)`" is a statement *about that algorithm*; "sorting is `Ω(n log n)`" is a statement *about the problem* (in a restricted model). Only the second is a barrier — and the way around it is to **leave the comparison model** (counting/radix/bucket sort), use **randomization**, or use **average-case analysis**.

---

# PART 1 — Divide & Conquer I: Binary Search

**Main idea of D&C:** solve a problem of size n by breaking it into one or more smaller subproblems of size < n, solve recursively, then combine.

```
BinarySearch(A, l, r, x):
    if l > r then return nil
    m ← ⌊(l+r)/2⌋
    if A[m] = x return m
    if x < A[m] then BinarySearch(A, l, m-1, x)
    else BinarySearch(A, m+1, r, x)
First call: BinarySearch(A, 1, n, x)
```
**Recurrence:** at most 2 comparisons then discard half → `T(n) ≤ T(n/2) + 2`, `T(1)=T(0)=1`. Recursion tree: `log₂n` levels each costing 2, plus 1 at the leaf → `2log₂n + 1` comparisons.

**Why `Ω(log n)` too:** when x is absent the algorithm must still run down to a leaf, so it does not stop early.

## 1.1 The comparison-free variant (lower_bound)

```
BinarySearch(A, l, r, x):
    if l = r then return l
    m ← ⌊(l+r)/2⌋
    if A[m] < x then BinarySearch(A, m+1, r, x)
    else BinarySearch(A, l, m, x)
First call: BinarySearch(A, 1, n+1, x)
```
- Still finds x if present; **no equality test at all**.
- If x absent it returns the position of the first element `≥ x`, or `n+1` if all elements are smaller.
- Change `<` to `≤` → returns the first element **strictly greater** than x (the successor).
- `Θ(log n)` with a smaller hidden constant. This is `std::lower_bound` / `bisect_left`.

**Tail-recursion → loop** (the practical version):
```
l ← 1; r ← n+1
while l < r
    m ← ⌊(l+r)/2⌋
    if A[m] < x then l ← m+1 else r ← m
return l
```
Same `O(log n)` but `O(1)` space instead of `O(log n)`, no function-call overhead, no stack overflow. `std::lower_bound`, `std::upper_bound`, `bisect_left`, `bisect_right`.

## 1.2 Rotated sorted array

`A[1..n]` sorted then rotated by `n−k` steps, so `A[1..k]` and `A[k+1..n]` are each increasing and `A[n] < A[1]`. Example: `[9,13,16,18,19,23,28,31,37,42,0,1,2,5,7,8]` with `k=10`.

**(1) Find k (position of the maximum) in `O(log n)`:**
```
FindK(A, l, r):
    m ← ⌊(l+r)/2⌋
    if A[m] > A[m+1] then return m     // m is the peak
    if A[m] ≥ A[l] then FindK(A, m+1, r)
    else FindK(A, l, m-1)
```
**Invariant to state:** if `A[m] ≥ A[l]` the peak lies strictly to the right of m; otherwise it lies at or to the left. `T(n) = T(n/2) + c ⟹ O(log n)`.

**(2) Search an arbitrary x in `O(log n)`:** `k ← FindK(A,1,n)`; if `x ≥ A[1]` search `A[1..k]`, else search `A[k+1..n]`. Two `O(log n)` phases.

## 1.3 Finding the last 0 in `0…01…1`

**(1) `O(log n)`:**
```
FindZ(A, l, r):
    m ← ⌊(l+r)/2⌋
    if A[m]=0 and A[m+1]=1 return m
    if A[m]=0 then FindZ(A, m+1, r)
    else FindZ(A, l, m)
```
Loop form with one comparison each (careful with floor/ceil — this is the classic off-by-one question):
```
// one-comparison v1: use ⌈(l+r)/2⌉      // one-comparison v2: use ⌊(l+r)/2⌋
l ← 1, r ← n                             l ← 1, r ← n
while l < r                              while l < r
    m ← ⌈(l+r)/2⌉                            m ← ⌊(l+r)/2⌋
    if A[m]=0 then l ← m                 if A[m]=0 then l ← m+1
    else r ← m−1                         else r ← m
return l                                 return l − 1
```

**(2) `O(log k)` when k ≪ n — exponential (galloping) search:**
```
i ← 1
while A[i] = 0
    i ← min(2i, n)
FindZ(A, i/2, i)
```
Doubling runs `log k` times; the answer lies in `(i/2, i]`, so the binary search costs `log(k/2)`. Total `O(log k)`.

> **Generalisable idea:** when you only know a *bound* on the answer's position, gallop to bracket it in `O(log k)` and then binary search inside the bracket. This is how `std::lower_bound` on a sorted range with a hint works.

---

# PART 2 — Divide & Conquer II: Sorting and Counting

## 2.1 Mergesort

```
Mergesort(A, l, r):
    if l = r then return
    m ← ⌊(l+r)/2⌋
    Mergesort(A, l, m); Mergesort(A, m+1, r); Merge(A, l, m, r)

Merge(A, l, m, r):
    L ← A[l..m]; R ← A[m+1..r]; append ∞ to both
    i ← 1; j ← 1
    for k ← l to r
        if L[i] ≤ R[j] then A[k] ← L[i]; i ← i+1
        else A[k] ← R[j]; j ← j+1
```
**Divide** `Θ(1)`, **conquer** `2T(n/2)`, **combine** `Θ(n)`. The sentinel `∞` removes the need for an explicit "which half is exhausted" test.

**Recurrence:** `T(n) = 2T(n/2) + n`, `T(1)=1` → `T(n) = n log₂n + n = Θ(n log n)`.

**Three simplifications that are legitimate (know why):** replace `≤` by `=` (we want an upper bound), replace `O(n)` by `n` and `O(1)` by `1` (rescaling), assume n is a power of 2 — if n′ is the smallest power of 2 ≥ n then `T(n) ≤ T(n′) ≤ T(2n) = O(T(n))` provided T is increasing and polynomial or slower.

**Why it is also `Ω(n log n)`, i.e. `Θ(n log n)`:** the merge step always costs `Θ(n)` regardless of input, so the running time is the same up to a constant on *every* input. **Every input is a worst-case input.** The whole analysis survives replacing every O by Ω.

**Merge is `Θ(n)` always** — it makes exactly `r−l+1` writes and one comparison per step.

## 2.2 Counting inversions (the classic D&C-with-a-promise problem)

**Definition.** `A[i]`, `A[j]` are inverted if `i < j` but `A[i] > A[j]`. The inversion number is the count of such pairs. It measures *how sorted* an array is, and *similarity between two rankings*. Naive counting is `O(n²)`.

**D&C:** divide in halves; recursively count blue–blue and green–green inversions; count **blue–green** inversions in the combine step. To do the combine in linear time, the recursive calls must make an **extra promise: return the half sorted**. Then a single merge pass counts them.

**The counting rule.** During the merge, when `L[i] > R[j]` we are about to take `R[j]`. Every blue element from position i to the end of L is greater than `R[j]`, and there are exactly `m − l − i + 2` of them (with `L[1..]=A[l..m]`, so `i` is 1-based within L). Hence
```
I[j] = (m − l − i + 2)     the number of blue elements greater than R[j]
```
Summing `I[j]` over all j gives the blue–green inversion count.

**Worked example from the notes:** `A = [1,5,4,8,10,2 | 6,9,12,11,3,7]`. Blue half has 5 internal inversions, green half has 8, blue–green contributes `6+3+2+2+0+0 = 13`. Total `5+8+13 = 22`. (The full 12-element worked example in the slides ends with total **22**.)

**Code shape:**
```
Sort-and-Count(A, l, r):
    if l = r then return 0
    m ← ⌊(l+r)/2⌋
    c1 ← Sort-and-Count(A, l, m)
    c2 ← Sort-and-Count(A, m+1, r)
    c3 ← Merge-and-Count(A, l, m, r)
    return c1 + c2 + c3
```
**Recurrence** `T(n) = 2T(n/2) + n` → `Θ(n log n)`.

**Alternative approach (not in 3711 exam scope):** an augmented BST / Fenwick tree supporting *insert key* and *rank query* (# keys less than query). Mentioned as Ch.17 CLRS, covered in COMP 5711.

> **Transferable design pattern:** *"D&C + an extra promise returned from the recursion"*. Merge sort promises sortedness; inversion counting promises sortedness so the cross-pair count becomes a single linear scan. If a combine step looks super-linear, ask what extra information the recursive call could return to make it linear.

## 2.3 Maximum subarray

**Problem.** `A[1..n]` of positive and negative numbers; maximise `V(i,j) = Σ_{k=i}^{j} A[k]`; the empty subarray is **not** allowed. (Motivation: best span of years for a company's profit.)

**Four algorithms, in increasing cleverness — know all four and why each is what it is:**

| Approach | Idea | Time |
|---|---|---|
| Brute force | Compute every `V(i,j)` from scratch, triple loop | `Θ(n³)` |
| Data reuse | Use `V(i,j) = V(i,j−1) + A[j]` | `Θ(n²)` |
| Divide & conquer | Split; case 3 crosses the midpoint | `Θ(n log n)` |
| Kadane | `V ← max(V + A[i], A[i])` | `Θ(n)` |

**D&C version:**
```
MaxSubarray(A, l, r):
    if l = r then return A[l]
    m ← ⌊(l+r)/2⌋
    M1 ← MaxSubarray(A, l, m)          // entirely left
    M2 ← MaxSubarray(A, m+1, r)        // entirely right
    Lm ← −∞; V ← 0
    for i ← m downto l:  V ← V + A[i]; if V > Lm then Lm ← V    // best suffix ending at m
    Rm ← −∞; V ← 0
    for i ← m+1 to r:    V ← V + A[i]; if V > Rm then Rm ← V    // best prefix starting at m+1
    return max{M1, M2, Lm + Rm}
```
Any subarray either lies entirely left, entirely right, or **crosses the cut**. A crossing subarray `[i,j]` has `i ≤ m < j` and decomposes as `A[i..m]` and `A[m+1..j]` — maximise each independently with two linear scans. `T(n) = 2T(n/2) + n = Θ(n log n)`.

**Kadane:** `V` = best value of a subarray *ending at* i. `V ← max(V + A[i], A[i])` = "extend or restart". Track the global best. See the lecture trace on `[-2,1,-3,4,-1,2,1,-5,4]` → answer 6 (subarray `[4,-1,2,1]`). History lesson: people believed `Θ(n log n)` was optimal until Kadane's insight, later understood as a special case of **dynamic programming**.

## 2.4 Maximum subarray in disguise: stock trading

Prices `p[1..n]`; find `i < j` maximising `p[j] − p[i]`; must buy exactly once and sell later; zero-profit is not allowed.

**D&C in `O(n log n)`:** three cases — both indices left, both right, or `i ≤ m < j`. Case 3 is solved by taking the **minimum of `p[l..m]`** and the **maximum of `p[m+1..r]`**, each found by a linear scan.

**Reduction to maximum subarray:** build the profit array of length n−1 with `Profit[i] = Price[i+1] − Price[i]`. Then
```
Σ_{t=i}^{j} Profit[t] = (p[i+1]−p[i]) + (p[i+2]−p[i+1]) + … + (p[j+1]−p[j]) = p[j+1] − p[i]
```
so maximum profit = maximum subarray of Profit, computable in `O(n)`. **Two lessons: (1) telescoping sums turn range-difference problems into subarray problems; (2) a reduction can buy you a better algorithm than the direct D&C.**

---

# PART 3 — Divide & Conquer III: Multiplication

## 3.1 Integer multiplication

**Cost model matters.** For long integers, define the unit operation explicitly (binary multiplication / binary addition, or word operations). Input size n = number of **bits** per integer.

**Schoolbook (binary long multiplication):** n partial products, each an n-bit addition → `Θ(n²)` multiplications and `Θ(n²)` additions.

**First D&C attempt.** Write `a = 2^{n/2}a₁ + a₀`, `b = 2^{n/2}b₁ + b₀`. Then
```
a·b = 2ⁿ a₁b₁ + 2^{n/2}(a₁b₀ + a₀b₁) + a₀b₀
```
Multiplication by `2^k` is a **left shift by k bits** — free. This needs **4** half-size products.
`T(n) = 4T(n/2) + n`, `T(1)=1` → expansion gives `4^{log₂n} = n²` leaves with `n(2^{log₂n}−1) = n(n−1)` internal work → `Θ(n²)`. **No improvement, and the reason is structural:** the algorithm still multiplies every bit of A by every bit of B. The key contrast with merge sort: *one problem generates **4** subproblems of size n/2*.

**Karatsuba (1960).** We never need `a₁b₀` and `a₀b₁` **individually** — only their **sum**:
```
a₁b₀ + a₀b₁ = (a₁+a₀)(b₁+b₀) − a₁b₁ − a₀b₀
```
Since `a₁b₁` and `a₀b₀` are computed anyway, the middle term costs **one extra multiplication of the sums**, replacing two. Total **3** half-size multiplications:
```
Multiply(A, B):
    n ← |A|;  if n = 1 then return A[1]·B[1]
    mid ← n/2
    U ← Multiply(A[mid+1..n], B[mid+1..n])      // a₁b₁
    Z ← Multiply(A[1..mid],  B[1..mid])         // a₀b₀
    A' ← A[mid+1..n] ⊕ A[1..mid]                // a₁+a₀
    B' ← B[mid+1..n] ⊕ B[1..mid]                // b₁+b₀
    Y ← Multiply(A', B')                        // (a₁+a₀)(b₁+b₀)
    M[1..2n] ← 0
    M[1..n] ← M[1..n] ⊕ Z
    M[mid+1..] ← M[mid+1..] ⊕ (Y ⊖ U ⊖ Z)       // + (a₁b₀+a₀b₁)·2^{n/2}
    M[2mid+1..] ← M[2mid+1..] ⊕ U               // + a₁b₁·2ⁿ
    return M
```
`T(n) = 3T(n/2) + n` → `3^{log₂n} = n^{log₂3} = n^{1.585…}` → **`Θ(n^{log₂3}) ≈ Θ(n^{1.585})`**.

**History / context (worth one line in an answer):** Karatsuba (2 parts, 3 subproblems) → Toom-3 (3 parts, 5 subproblems, `Θ(n^{log₃5}) = Θ(n^{1.465})`) → Toom-4 (4 parts, 7 subproblems, `Θ(n^{log₄7}) = Θ(n^{1.404})`) → FFT-based `Θ(n log n log log n)` (1968) → `Θ(n log n · 2^{Θ(log*n)})` (2007) → **Harvey & van der Hoeven proved the `O(n log n)` conjecture in 2019**. In practice: schoolbook for n < 10, Karatsuba for 10–20, Toom-3 for 100–1000, FFT above 1000, and real libraries operate on 32/64-bit words rather than bits.

## 3.2 Matrix multiplication

`C = AB` with `c_ij = Σ_{k=1}^{n} a_ik b_kj`. Brute force `Θ(n³)`.

**First D&C attempt:** partition A and B into (n/2)×(n/2) blocks; combine via 8 block products and 4 block additions.
`C₁₁ = A₁₁B₁₁ + A₁₂B₂₁`, `C₁₂ = A₁₁B₁₂ + A₁₂B₂₂`, `C₂₁ = A₂₁B₁₁ + A₂₂B₂₁`, `C₂₂ = A₂₁B₁₂ + A₂₂B₂₂`.
`T(n) = 8T(n/2) + Θ(n²)` → `Θ(n^{log₂8}) = Θ(n³)`. **No improvement** — same structural reason as before.

**Strassen (1969):** 7 multiplications instead of 8.
```
P₁ = A₁₁(B₁₂ − B₂₂)          C₁₁ = P₅ + P₄ − P₂ + P₆
P₂ = (A₁₁ + A₁₂)B₂₂          C₁₂ = P₁ + P₂
P₃ = (A₂₁ + A₂₂)B₁₁          C₂₁ = P₃ + P₄
P₄ = A₂₂(B₂₁ − B₁₁)          C₂₂ = P₅ + P₁ − P₃ − P₇
P₅ = (A₁₁ + A₂₂)(B₁₁ + B₂₂)
P₆ = (A₁₂ − A₂₂)(B₂₁ + B₂₂)
P₇ = (A₁₁ − A₂₁)(B₁₁ + B₁₂)
```
7 multiplications of (n/2)×(n/2), `Θ(n²)` additions.
`T(n) = 7T(n/2) + n²` → **`Θ(n^{log₂7}) = Θ(n^{2.807})`**. Used in practice for n > 100. Current record exponent ≈ 2.371 (2024–2026 work).

> **The unifying idea of this whole part (say this in an exam):** *D&C helps exactly when the number of subproblems `a` can be reduced below the naive count, because the exponent is `log_b a`. Reducing n/b splitting or using bignum tricks does nothing by itself — merge sort wins because `a=2, b=2`; the naive multiply loses because `a=4, b=2` gives `log₂4 = 2`, exactly matching the schoolbook exponent. Karatsuba and Strassen win by lowering `a` to 3 and 7.*

---

# PART 4 — Randomization

## 4.1 Probability toolkit

- **Expectation:** `E[X] = Σ_i i·Pr[X = i]`.
- **Indicator variables:** if X ∈ {0,1} then `E[X] = Pr[X=1]`. This is the single most useful trick in the course.
- **Linearity of expectation:** `E[X+Y] = E[X] + E[Y]` — **no independence required**. This is why it is so powerful.
- **Independence:** `E[XY] = E[X]E[Y]` only when independent.
- **Tail form:** for non-negative integer X, `E[X] = Σ_{i≥1} Pr[X ≥ i]`.
- **Union bound:** `Pr[∪Aᵢ] ≤ Σ Pr[Aᵢ]`.

**Warm-ups from the notes.**
- One die: `E = 3.5`. Two dice, expected **max**: `1·(1/36) + 2·(3/36) + 3·(5/36) + 4·(7/36) + 5·(9/36) + 6·(11/36) = 4.47`. Two dice, expected **sum** = 7.
- **Guessing cards without memory:** `Xᵢ` = 1 if the i-th guess is right; `E[Xᵢ] = 1/n`; total `E[X] = n·(1/n) = 1`. *(Surprisingly effortless — a hallmark of linearity.)*
- **Guessing cards with memory:** guess uniformly from the n−i+1 unseen; `E[Xᵢ] = 1/(n−i+1)`; total `Σ_{i=1}^n 1/i = Hₙ = Θ(log n)`.

## 4.2 Hash table with chaining

`h : U → [m]`; collisions resolved by chaining. **Simple Uniform Hashing Assumption (random oracle):** `h(x)` is uniform on [m] and the values `h(0),…,h(u−1)` are mutually independent.

**Theorem.** For any x (in S or not), the expected number of keys colliding with x is ≤ n/m.
**Proof.** For each y ∈ S, let `I_y = 1` if `h(x) = h(y)`. Then
`E[Σ_y I_y] = Σ_y E[I_y] = Σ_y 1/m ≤ n/m`.
**Corollary:** insert / delete / query take `O(1 + α)` expected, where **load factor `α = n/m`**. Choose `m = Θ(n)` so α is constant → `O(1)` expected.

> **Exam angle:** hashing is one of the few places where randomization is suspected to give a *genuine* asymptotic speedup — it is an open problem whether insert/delete/lookup can be done deterministically in `O(1)` time with `O(n)` space.

## 4.3 Birthday paradox

k people, n = 365 days; `X_ij = 1` if i,j share a birthday; `E[X_ij] = 1/n`. Then
`E[X] = C(k,2)·(1/n) = k(k−1)/(2n)`. Setting `≥ 1` gives `k ≈ √(2n) + 1 ≈ 28` for n = 365.

> **Pattern:** "expected number of colliding *pairs*" is always `C(k,2)/n` by linearity — used again for hash collisions and for `Ω(√n)` birthday-attack bounds.

## 4.4 The hiring problem

Randomly permute the n candidates; hire candidate i iff i is the best so far.
`Xᵢ` = 1 if candidate i is hired. Among the first i candidates each is equally likely to be last-best, so `E[Xᵢ] = 1/i`. Hence
`E[X] = Σ_{i=1}^n 1/i = Hₙ = Θ(log n)`.

## 4.5 Random permutation

```
RandomPermute(A):
    n ← A.length
    for i ← 1 to n
        swap A[i] with A[Random(1, i)]
```
`O(n)` time, `O(1)` working space.

**Correctness by induction on i:** after the i-th iteration every permutation of `A[1..i]` is equally likely, i.e. `Pr[A[1..i] = (a₁,…,aᵢ)] = 1/i!`.
- Base `i=1`: trivial.
- Step: let j be such that `a_j` is the original `A[i]`. The event `A[1..i] = (a₁,…,aᵢ)` after step i happens iff `A[1..i−1] = (a₁,…,a_{j−1}, aᵢ, a_{j+1},…,a_{i−1})` after step i−1 **and** `Random(1,i) = j`. Probability `1/(i−1)! · 1/i = 1/i!`. ∎

> Note the direction: this swaps **forward** — `A[i]` gets a uniformly random element from the prefix. (The alternative backward Fisher–Yates swaps `A[i]` with `A[Random(i,n)]`; both are correct, don't mix them up.)

## 4.6 Geometric distribution

X = number of flips until the first head, success probability p.
```
E[X] = Σ_{j≥1} j(1−p)^{j−1} p = 1/p
```
Cleaner derivation via the tail formula: `E[X] = Σ_{i≥1} Pr[X ≥ i] = Σ_{i≥1}(1−p)^{i−1} = 1/(1−(1−p)) = 1/p`.

**Two facts to memorise:** expected waiting time for a success with probability p is `1/p`; the number of *trials* until (and including) the first success is **geometric with mean 1/p**, and the number of *failures before* it is `(1−p)/p`.

## 4.7 Coupon collector

n coupon types, each box uniform. `Xᵢ` = boxes to go from i to i+1 distinct types. Probability of a new type is `(n−i)/n`, so `E[Xᵢ] = n/(n−i)`. Therefore
```
E[X] = Σ_{i=0}^{n−1} n/(n−i) = n Σ_{i=1}^{n} 1/i = n Hₙ = Θ(n log n)
```

**Exam variant (Spring-2026 Q2a):** expected boxes to collect **n/2** distinct coupons:
`E = n(1/n + 1/(n−1) + … + 1/(n/2+1))`. Bound the harmonic block:
- Upper: each term `≤ 1/(n/2)` → sum `≤ (n/2)·(2/n) = 1`.
- Lower: each term `≥ 1/n` → sum `≥ (n/2)·(1/n) = 1/2`.
So the bracket is `Θ(1)` and the answer is **`Θ(n)`** (i.e. `α=1, β=0`). *You must argue both directions — the question says so.*

**Exam variant (Spring-2026 Q2b):** after opening n boxes, how many distinct coupon types in expectation (exact formula)? Let `Xᵢ = 1` if type i is **never** seen. A given box misses type i with probability `1 − 1/n`, so `Pr[Xᵢ=1] = (1 − 1/n)ⁿ`. Expected number **missed** = `n(1−1/n)ⁿ`; expected number **obtained** = `n − n(1−1/n)ⁿ ≈ (1 − 1/e)n ≈ 0.63n`. **Hint-driven:** "how many will we *not* get?" — count the complement, it is the only way to get an exact formula.

## 4.8 The majority problem

Find an element appearing > n/2 times, assuming it exists.
1. **Sort and count:** `O(n log n)` time, `O(1)` space.
2. **Divide & conquer:** `O(n log n)` time, `O(log n)` space, only equality tests.
3. **Hashing:** `O(n)` expected, `O(n)` space.
4. **Try and check:** pick a random element, count its frequency, repeat if not majority → `O(n)` expected, `O(1)` space. **Worst case unbounded**, but the probability of running long is tiny.
5. **Boyer–Moore majority vote:** `O(n)` **worst case**, `O(1)` space, equality tests only.
```
c ← 0
for i ← 1 to n
    if c = 0 then m ← A[i]; c ← 1
    else if m = A[i] then c ← c+1
    else c ← c − 1        (*)
return m
```
**Correctness:** invariant "`A[1..i]` = (c copies of m) + items that have been cancelled". Line (\*) cancels `A[i]` against one copy of m, and `A[i] ≠ m`, so the cancelled pair is non-majority. Any item can have at most n/2 copies cancelled, so a true majority survives as m.

> **When to use which:** if you need the majority *value* only, Boyer–Moore is strictly best (deterministic, O(1) space). If you need the *count*, you must do a verification pass (another O(n)) — and note that Boyer–Moore gives a candidate that is guaranteed correct only if a majority exists.

## 4.9 How randomized algorithms are measured

**Worst-case input, expected randomness:**
```
max_{I : |I| = n}  E_R[ T(I, R) ]
```
where I is the input, R the algorithm's internal randomness, T(I,R) the actual time. **This is different from average-case analysis**, which averages over an input distribution. Randomization moves the randomness from the input (which you don't control) into the algorithm (which you do).

Randomized algorithms are often **simpler and practically faster**. Whether they give a genuine asymptotic speedup over deterministic algorithms is **open** — with hash tables the leading candidate.

---

# PART 5 — Quicksort and Selection

## 5.1 Quicksort = mergesort with the work moved

| | Mergesort | Quicksort |
|---|---|---|
| Split | by position, `Θ(1)`, always balanced | by **pivot value**, `Θ(n)`, data-dependent |
| Recurse | `2T(n/2)` | `T(k) + T(n−1−k)` |
| Combine | `Merge`, `Θ(n)` | **nothing** (`0`) |

```
Quicksort(A, l, r):
    if l ≥ r then return
    m ← Partition(A, l, r)
    Quicksort(A, l, m−1); Quicksort(A, m+1, r)
```

**Partition with the last element as pivot (Lomuto):**
```
Partition(A, l, r):
    x ← A[r]                       // pivot
    i ← l − 1
    for j ← l to r−1
        if A[j] ≤ x then
            i ← i+1; swap A[i] and A[j]
    swap A[i+1] and A[r]
    return i+1                     // pivot's final position
```
**Loop invariant:** `A[l..i] ≤ x`, `A[i+1..j−1] > x`, `A[j..r−1]` unrestricted, `A[r] = x`. Time `Θ(n)`, working space `O(1)` — **in place**.

## 5.2 Pivot selection is everything

- **Best case:** the median → balanced → `Θ(n log n)`. Problem: we do not know the median.
- **Worst case:** smallest or largest → `Θ(n²)`.
- **Fix:** choose a **uniformly random** element, swap it with `A[r]`, then partition. An "average" pivot splits `¼n` / `¾n`, giving `T(n) = T(n/4) + T(3n/4) + n = O(n log n)`.

## 5.3 The binary-tree (pivot-tree) analysis — the exam's expected proof

Assume distinct elements, relabel `z₁ < z₂ < … < zₙ`. Build the tree whose root is the **first pivot**, whose left subtree is the tree of elements left of the pivot, etc. **The tree completely encodes the execution.** `Tᵢ` = subtree rooted at `zᵢ`.

**Key fact.** `zᵢ` is compared with `zⱼ` **iff** `zᵢ` is an ancestor of `zⱼ` or vice versa in this tree — equivalently, **iff `zᵢ` or `zⱼ` is the first pivot chosen among `Z_{ij} = {zᵢ, z_{i+1}, …, zⱼ}`**. If any `z_k ∈ Z_{ij}` with `k ∉ {i,j}` is the first pivot, `zᵢ` and `zⱼ` go to different subarrays and are **never** compared.

Because pivots are uniform, the first pivot chosen inside `Z_{ij}` is uniformly distributed over its `j−i+1` elements. Therefore
```
Pr[zᵢ first pivot in Z_ij] = 1/(j−i+1)
Pr[zᵢ and zⱼ compared] = Pr[either zᵢ or zⱼ is first pivot in Z_ij] = 2/(j−i+1)
```
With `X_ij = 1` if compared and `X = Σ_{i<j} X_ij`:
```
E[X] = Σ_{i<j} E[X_ij] = Σ_{i<j} 2/(j−i+1)
```
Now write the sum as an upper-triangular matrix:
```
i=1 : 2/2 + 2/3 + 2/4 + … + 2/n   = O(log n)
i=2 :       2/2 + 2/3 + … + 2/(n−1) = O(log n)
i=3 :             2/2 + … + 2/(n−2) = O(log n)
…
```
Each row starts at `2/2 = 1` and is a harmonic tail, so each row is `O(log n)`. There are n rows → **`E[X] = O(n log n)`**. (It is also `Ω(n log n)`. The count is `2(Hₙ − 1)` for the first row, and `Hₙ = Θ(log n)`.)

**Quicksort in practice.** `Θ(n log n)` in expectation on any input, and with very high probability. Small hidden constants, cache-efficient (Hoare partition is better than Lomuto). Engineering: start with quicksort, and when recursion depth exceeds ~`2 log n` switch to insertion sort or heapsort. It beats mergesort in practice despite the `Θ(n²)` worst case. *Bentley & McIlroy, "Engineering a Sort Function", 1993.* Implemented in the C++ STL.

## 5.4 Three-way quicksort (ties!) — HW2 Q3

**Failure of plain quicksort with duplicates.** On `A = [c,c,…,c]` with Lomuto partitioning, every element satisfies `A[j] ≤ pivot`, so all elements go left and the subproblems are `(n−1, 0)`. This degenerate split happens with probability **1** regardless of the random pivot, giving `T(n) = T(n−1) + Θ(n) = Θ(n²)` **in expectation**.

**Fix:** partition into three regions — `< pivot`, `= pivot`, `> pivot` — and recurse on the outer two only.
```
Quicksort3Way(A, l, r):
    if l ≥ r then return
    pivot ← random element of A[l..r]
    lt ← l;  i ← l;  gt ← r        // A[l..lt−1] < pivot; A[lt..i−1] = pivot; A[gt+1..r] > pivot
    while i ≤ gt do                // (A)
        if A[i] < pivot then
            swap A[lt] ↔ A[i]; lt ← lt+1; i ← i+1        // (B)
        else if A[i] > pivot then
            swap A[i] ↔ A[gt]; gt ← gt−1                  // (C)  i NOT incremented
        else
            i ← i+1                                       // (D)
    Quicksort3Way(A, l, lt−1)
    Quicksort3Way(A, gt+1, r)
```
**Why `i` is not incremented in (C):** the element swapped in from position `gt` has not been inspected yet.

**Adapted analysis.** For `i < j` set `X_ij = 1` if `zᵢ` and `zⱼ` are ever compared (the algorithm may do both `zᵢ<zⱼ` and `zᵢ>zⱼ`; still count once). Let `Z'_{ij}` be `{zᵢ,…,zⱼ}` **expanded to include every element equal to `zᵢ` or `zⱼ`**. Then
```
Pr[X_ij = 1] = 1/|Z'_{ij}| ≤ 1/(j − i + 1)   and hence   E[X_ij] ≤ 2/(j − i + 1)
```
so the expected running time is still `O(n log n)`.

**The three cases in the argument:**
1. Pivot **outside** `Z'_{ij}` → `zᵢ, zⱼ` stay in the same recursive call; the comparison is *undecided*.
2. Pivot **is `zᵢ` or `zⱼ`** while they are in the same call → they **are** compared.
3. Pivot is **inside** `Z'_{ij}` (and not `zᵢ,zⱼ`) → **no comparison**:
   - if `zᵢ < zⱼ`: the pivot separates them into different recursive calls;
   - if `zᵢ = zⱼ`: all of `Z'_{ij}` are equal, so they land together in the "`=`" region and that region is never recursed into.

## 5.5 Randomized selection (quickselect)

**Goal.** Return the i-th smallest of `A[1..n]` — better than sorting, i.e. linear.
```
Select(A, l, r, i):                    // i-th smallest of A[l..r]
    if l = r then return A[l]
    choose pivot uniformly in A[l..r], swap it with A[r]
    m ← Partition(A, l, r)             // pivot's position
    k ← m − l + 1                      // pivot's relative rank within A[l..r]
    if i = k then return A[m]
    else if i < k then return Select(A, l, m−1, i)
    else return Select(A, m+1, r, i − k)
First call: Select(A, 1, n, i)
```
Only **one** recursive call — this is what makes it linear rather than `n log n`.

**Worked trace** (`Select(A,1,15,10)`): the array is repeatedly partitioned, and `(l, r, i)` evolves `(1,15,10) → (1,12,10) → (6,12,5) → (10,12,1) → (10,10,1)` returning `A[10]`. Note the **rank bookkeeping**: when recursing right, the new rank is `i − k`.

**Analysis.** Call a pivot **good** if it lands between the 25th and 75th percentile.
- `Pr[good] = 1/2`.
- A good pivot discards **at least ¼** of the elements (either everything left of it including L, or everything right including R).
- After i good pivots at most `(3/4)ⁱ n` items remain.
- Let `Yᵢ` = time of stage i (between the i-th and (i+1)-st good pivot) and `Xᵢ` = number of recursive calls in stage i. Then `Yᵢ ≤ Xᵢ(3/4)ⁱn` and `Xᵢ` is geometric with `p = 1/2`, so `E[Xᵢ] = 2`, giving `E[Yᵢ] ≤ 2(3/4)ⁱ n`.
```
E[total] = Σ_i E[Yᵢ] ≤ Σ_i 2(3/4)ⁱ n = 2n · 1/(1 − 3/4) = O(n)
```
A deterministic linear-time selection algorithm exists (median-of-medians) but is complicated.

## 5.6 k-th smallest of two sorted arrays (bridge to the exam question)

`A₁` (size m), `A₂` (size n), find the k-th smallest in the union.
- **Very naive:** merge fully, `Θ(m+n)`.
- **Naive:** merge with an early stop at k, `Θ(k)`.
- **D&C `Θ(log k)`:**
```
Search(A1, A2, start1, end1, start2, end2, k)
  if k = 1 then return min(A1[start1], A2[start2])
  if A1[start1 + k/2 − 1] < A2[start2 + k/2 − 1]
      return Search(A1, A2, start1 + k/2, end1, start2, end2, k/2)   // discard first k/2 of A1
  else
      return Search(A1, A2, start1, end1, start2 + k/2, end2, k/2)   // discard first k/2 of A2
First call: Search(A1, A2, 1, k, 1, k, k)
```
**Why the discard is safe:** if `A1[start1 + k/2 − 1] < A2[start2 + k/2 − 1]`, then at most `k/2 − 1` elements of A1 precede it and at most `k/2 − 1` elements of A2 precede it, so this element's rank is at most `k−1` — none of the first `k/2` elements of A1 can be the answer.

**The full exam version (Fall-2025 Q4) handles unequal lengths `m ≠ n`:** search for the k-th of `A[p..q] ∪ B[r..s]`; if `k=1` return `min(A[p], B[r])`; otherwise compare `A[p + k/2 − 1]` with `B[r + k/2 − 1]`. If `k/2` exceeds a subarray's length (say `q−p+1`), **pretend that out-of-range entry is `∞`** — and note that since `k ≤ (q−p+1)+(s−r+1)`, we always have `k/2 ≤ max{q−p+1, s−r+1}`, so at most one of the two probes is out of range. The searched rank halves each time → `O(log k)` calls with `O(1)` work each.

---

# PART 6 — Heaps and Priority Queues

## 6.1 Priority queue ADT and implementations

Stores n elements each with a priority/key; supports **Insert** and **Extract-Max** (negate priorities for a min-queue).

| Implementation | Insert | Extract-Max |
|---|---|---|
| Unsorted linked list | `O(1)` | `O(n)` |
| Sorted linked list | `O(n)` | `O(1)` |
| Balanced BST | `O(log n)` | `O(log n)` — but large space/time overhead |
| **Binary heap** | `O(log n)` | `O(log n)` — array-based, no pointers |

**Application:** OS process scheduling (`top`, lower PR = higher priority): insert a process when it starts/preempts; extract the lowest-PR process when the CPU frees up.

## 6.2 Binary heap structure

- **Heap-order property:** the priority of a node ≥ the priority of each of its children (max-heap).
- **Shape property:** a **complete binary tree** — all levels full except possibly the lowest, and the lowest is packed left.
- **Array representation, no pointers:** root at `A[1]`; left child of `A[i]` is `A[2i]`; right child is `A[2i+1]`; parent is `A[⌊i/2⌋]`.
- **Height** `h = ⌊log n⌋`, because `2⁰+2¹+…+2^{h−1} = 2^h − 1 < n ≤ 2⁰+…+2^h = 2^{h+1} − 1`.

## 6.3 The three operations

**Max-Heapify (bubble down)** — `O(log n)`, the workhorse:
```
Max-Heapify(A, i):
    l ← 2i; r ← 2i+1
    if l ≤ n and A[l] > A[i] then k ← l else k ← i
    if r ≤ n and A[r] > A[k] then k ← r
    if k ≠ i then swap A[i], A[k]; Max-Heapify(A, k)
```
**Crucial precondition:** if the two subtrees below i are already heaps, then after Heapify the subtree at i is a heap. *This is exactly why Build-Heap must iterate in reverse order.*

**Extract-Max** — `O(log n)`: save `A[1]`; move `A[n]` to `A[1]`; `n ← n−1`; `Max-Heapify(A,1)`.

**Insert** — `O(log n)`: `n ← n+1`; `A[n] ← x`; then **bubble up** while `A[⌊i/2⌋] < A[i]`, swapping and setting `i ← ⌊i/2⌋`.

**Delete(key) / update-priority** — `O(log n)`: find `A[k]` with the key (needs a **position map** from keys to array indices, updated whenever an element moves), swap with `A[n]`, `n ← n−1`, then bubble up *or* down. The position map does not change the asymptotics (hash table) but the overhead is large, so most implementations (C++, Python) do not support it.

**Lazy deletion** when the heap has no delete: keep a hash set/BST `D` of deleted keys. On Extract-Max, pop and if the element's key is in D, remove it from D and pop again. A single Extract-Max may cost more than `O(log n)`, but **any sequence of n operations still costs `O(n log n)`** — this is *amortized analysis* (COMP 5711 / textbook).

## 6.4 Build-Heap in `Θ(n)` — the famous surprising bound

```
Build-Max-Heap(A):
    for i ← ⌊n/2⌋ downto 1
        Max-Heapify(A, i)
```
Naively "n calls × O(log n)" = `O(n log n)`, but the true bound is **`Θ(n)`** because **most nodes are near the bottom and have almost no height**.

Take `n = 2^{h+1} − 1` (all levels full, height h):

| Level i | # nodes | cost of heapify per node | total |
|---|---|---|---|
| 0 | 1 | h | h |
| 1 | 2 | h−1 | 2(h−1) |
| 2 | 4 | h−2 | 4(h−2) |
| i | `2ⁱ` | `h−i` | `2ⁱ(h−i)` |
| h−1 | `2^{h−1}` | 1 | `2^{h−1}` |
| h | `2^h` | 0 | 0 |

Total `= Σ_{i=0}^{h} 2ⁱ(h−i) = Σ_{k=0}^{h} (h−k)2^k`. Using `Σ k/2^k ≤ 2` and `Σ 1/2^k ≤ 2`, this is `≤ n(2 + 2)`, i.e. **`O(n)`**. The slide's clean way of seeing it: `2^{h−1} < 2^h + 2^{h−1} + … + 8 + 4 + 2 ≤ n`.

**Exam version (Fall-2025 Q2b,c) — merging heaps pairwise.** Given `2^{m−k}` heaps each with `2^k − 1` elements, and `2^{m−k−1}` new nodes `v₁,…,v_{2^{m−k−1}}`:
- For each i: make `vᵢ` the **parent of the roots** of `H_{2i−1}` and `H_{2i}`; compare `x = vᵢ` with the smaller of the two roots; if x is larger, swap with that root and then **bubble x down** until it is ≤ both children or is a leaf.
- Cost per i: `O(1)` per level × `O(k)` levels = `O(k)`. Over `2^{m−k−1}` values of i: **`O(k·2^{m−k−1})`**.
- **Build-Heap from scratch:** start with `2^{m−1}` heaps of size 1; repeat the merge step for k = 1,2,…,m. Total `O(Σ_{k=1}^{m} k·2^{m−k−1})`.
- **Bounding the sum:** with `S(x) = Σ_{i≥1} xⁱ = x/(1−x)`, we have `S′(x) = Σ i x^{i−1} = (1−x)^{−2}`, so `Σ_{k=1}^{m} k/2^{k+1} ≤ (1/4)S′(1/2) = 1`. Hence the total `= 2^m Σ k/2^{k+1} ≤ n + 1 = O(n)`. ∎

> **The transferable idea:** *changing the order of summation to "charge each element for its depth" converts an `n log n`-looking bound into a linear one.* The same halving-geometric-series trick appears in Build-Heap, Karatsuba's geometric sum, and the `Σ k/2^k` bound.

## 6.5 Heapsort

**First idea (wrong for space):** build a **min**-heap and do n Extract-Mins → `O(n log n)` time but `O(n)` extra working space (you need somewhere to put the output).

**The real algorithm** uses a **max**-heap and sorts **in place**:
```
Heapsort(A):
    Build-Max-Heap(A)                 // Θ(n)
    for i ← n downto 2
        swap A[1] and A[i]            // move current max to its final place
        n ← n − 1
        Max-Heapify(A, 1)             // restore heap on the shrinking prefix
```
`Θ(n) + (n−1)·Θ(log n) = Θ(n log n)` time, `Θ(1)` extra space. Not stable.

## 6.6 Heap exercises worth knowing

**(a) Merge k sorted arrays of n elements into one array of kn.** Three solutions, all examinable:
1. Sort everything: `O(kn log(kn))`.
2. Divide & conquer, merge pairs: `O(kn log k)`.
3. **Min-heap of size k:** insert the first element of each array; Extract-Min, output it, insert the next element from the same array; repeat. Each of the `kn` elements costs `O(log k)` → `O(kn log k)`.

**(b) The Skyline problem.** Buildings `(x_L, x_R, y)`; output the key points of the upper envelope; assume distinct x-coordinates.
- **D&C:** split the buildings in half, get each skyline recursively, merge in `O(n)`. Base case: one building → key points `(x_L, y), (x_R, 0)`. **Merge:** keep current heights `h₁, h₂` (initially 0); look at the next key point on each skyline, `(x₁,y₁)`, `(x₂,y₂)`; advance the one with the smaller x, updating its h; at `x = min(x₁,x₂)` the merged height is `max(h₁,h₂)`; if that differs from the last emitted height, append `(x, max(h₁,h₂))`. Total `O(n log n)`.
- **Sweep + heap:** sort all vertical boundaries by x; a max-heap keyed by y holds all "live" buildings; on the left boundary insert, on the right boundary delete; `Q.top`'s y is the current skyline height. `O(n log n)`. Implementation trick specific to skyline: store `(priority = y, x_R)` and simply pop while `top.x_R < current x` — **no delete needed at all**.

---

# PART 7 — Linear-Time Sorting and the Sorting Lower Bound

## 7.1 The comparison-sorting barrier

A **comparison-based** sorting algorithm makes decisions only via comparisons between items — its behaviour depends only on the *relative order*, not the values. For such algorithms, sorting n elements requires `Ω(n log n)` (Part 0.5). Therefore **mergesort and heapsort are asymptotically optimal comparison sorts**, and to go faster you must **leave the comparison model**.

## 7.2 Counting sort

**Input:** `A[1..n]`, each an integer in `{0,1,…,k}`. **Output:** `B[1..n]` sorted.
```
Counting-Sort(A, B):
    let C[0..k] be a new array
    for i ← 0 to k: C[i] ← 0
    for j ← 1 to n: C[A[j]] ← C[A[j]] + 1        // C[i] = # of i's
    for i ← 1 to k: C[i] ← C[i] + C[i−1]         // C[i] = # of elements ≤ i
    for j ← n downto 1:
        B[C[A[j]]] ← A[j]
        C[A[j]] ← C[A[j]] − 1
```
**Time `Θ(n+k)`, space `Θ(n+k)`.** It is **stable** — equal elements keep their relative order.

**Two details markers look for:**
- The **prefix-sum** pass converts counts into *final positions*: `C[i]` = the last output slot for value i.
- The final loop runs **backwards** (`j ← n downto 1`) precisely to make it **stable**: placing from the right end of each value's block, the last-seen equal element lands last.

## 7.3 Radix sort

**Input:** n numbers, each with d digits, each digit in `[0,k]`.
```
Radix-Sort(A, d):
    for i ← 1 to d
        use counting sort to sort A on digit i
```
**Correctness (induction on digit position, LSD first).** Assume the numbers are sorted by their low-order `i−1` digits before the pass. Sort on digit i. Then:
- Two numbers that **differ** on digit i are correctly ordered by their low-order i digits.
- Two numbers with the **same** digit i keep their previous relative order **because counting sort is stable**, so they are also correctly ordered by their low-order i digits. ∎

**The correctness proof rests entirely on stability** — always say this.

**Running time.** Counting sort is `Θ(n+k)`, run d times → `Θ(d(n+k))`.

**Choosing d and k for w-bit integers.** Each digit is `log k` bits, so `w = d log k`, i.e. `d = ⌈w/log k⌉`. Runtime `Θ(⌈w/log k⌉(n+k))`, **minimised at `k = n`** giving `Θ(⌈w/log n⌉·n)`. **This is `Θ(n)` in the parameter regime `w = O(log n)`** — i.e. when keys fit in a constant number of machine words. That is the real-world case (see the CPU-architecture table: 64-bit words, `log n ≈ 33–40`).

Radix sort works for strings and floating-point numbers (transform to a fixed-width order-preserving integer form first). Available in C++ Boost and CUDA Thrust.

## 7.4 Bucket sort

**Input:** `A[1..n]` of reals, **uniformly random in [0,1)** (normalise first if not).
Create n buckets; put `A[i]` into bucket `⌊n·A[i]⌋`; sort each bucket; concatenate.

**Analysis.** Creating buckets is `O(n)`. If bucket i has `Xᵢ` elements, sorting costs `O(Σ Xᵢ²)` in expectation:
```
E[Σᵢ Xᵢ²] = Σᵢ E[Xᵢ²] = n·E[X₀²]
```
With `X₀ = Y₁ + … + Yₙ` where `Yᵢ = 1` iff `A[i]` falls into bucket 0:
```
E[X₀²] = E[Y₁² + … + Yₙ² + 2Y₁Y₂ + … + 2Y_{n−1}Yₙ]
       = n·E[Y₁²] + O(n²)·E[Y₁Y₂]
```
Now `E[Y₁²] = Pr[Y₁=1] = 1/n` and, by independence, `E[Y₁Y₂] = E[Y₁]E[Y₂] = 1/n²`. Hence
```
E[X₀²] = n·(1/n) + O(n²)·(1/n²) = O(1)
```
So sorting all buckets is `O(n)` in expectation → **total `O(n)` expected**, but `Θ(n²)` worst case (all elements in one bucket).

> **Contrast counting/radix vs bucket:** counting and radix sort exploit a **bounded integer key range** (deterministic linear time); bucket sort exploits a **known input distribution** (expected linear time, worst case quadratic). Both escape the comparison lower bound by not being comparison-based.

## 7.5 Master comparison table (memorise for the "which sort?" question)

| | Insertion | Merge | Quicksort | Heapsort | Radix | Bucket |
|---|---|---|---|---|---|---|
| **Worst case** | `Θ(n²)` | `Θ(n log n)` | `Θ(n²)` | `Θ(n log n)` | `Θ(n)`* | `Θ(n²)` |
| **Expected** | – | – | `Θ(n log n)` | – | – | – |
| **Average** | `Θ(n²)` | `Θ(n log n)` | `Θ(n log n)` | `Θ(n log n)` | `Θ((w/log n)·n)` | `Θ(n)` |
| **Working space** | `Θ(1)` | `Θ(n)` | `Θ(log n)` | `Θ(1)` | `Θ(n)` | `Θ(n)` |
| **Comparison-based** | Yes | Yes | Yes | Yes | **No** | **No** |
| **Stable** | Yes | Yes | **No** | **No** | Yes | Yes |
| **Cache** | Good | Good | Good | **Bad** | Bad | Bad |
| **Parallelises** | No | Good | Good | No | Good | Good |

\* radix's `Θ(n)` assumes `w = O(log n)`; in general `Θ((w/log n)·n)`.

**The scenario question (Fall-2025 Q1d) — how to answer:**
| Scenario | Answer | Why |
|---|---|---|
| n integers in `[1,n]` | **Counting sort**, `O(n)` | key range k = n is linear in n |
| n floats with `O(√log n)` inversions | **Insertion sort**, `O(n log n)` | runs in `O(n + inversions)`; check `√log n ≤ log n` ⟹ `n + n√log n = O(n log n)`. Note the *worst-case* `O(n²)` bound is not what is asked |
| n floats, nothing known | **Merge sort or heapsort**, `O(n log n)` | only these have a guaranteed `n log n` worst case; quicksort is `Θ(n²)` worst case, and radix/bucket do not apply to arbitrary floats |

> **Critical exam nuance for the "inversions" row:** insertion sort's running time is `Θ(n + I)` where I is the inversion number. With `I = O(√log n)` the total is `n + O(n√log n)`, and since `√log n = O(log n)` this is `O(n log n)` — but it is *also* `O(n)` if you are careful? No: `n√log n` is not `O(n)`, so `n log n` is the safest correct asymptotic statement at that granularity, and it matches the official marking scheme.

---

# PART 8 — The Exam Playbook

## 8.1 Paper structure observed

Both midterms are **closed book**, **4 questions**, 2 hours, with the mark split shown in a table on page 1. Fall-2025: 15/10/10/10 = 45. Spring-2026: 10/10/20/20 = 60. Questions are printed one per page **with extra blank pages** — the instructions explicitly say the blank pages "are not meant to imply that each question requires all of the blank pages. Many can be answered using much less space." Write concisely.

**Recurring topics across the two papers:**

| Topic | Fall-2025 | Spring-2026 |
|---|---|---|
| Recurrence solving (expansion / recursion tree) | Q1a (tree, `Θ(n log²n)`) | Q1b (expansion, `Θ(n³ log n)`, MT banned) |
| Asymptotic comparison table | – | Q1a |
| Lower bound proof | Q1b (`Ω(k log n)`) | – |
| Radix sort trace | Q1c | – |
| Which sorting algorithm? | Q1d | – |
| Heaps (insert trace, merge, build) | Q2a,b,c | Q4a,b |
| Randomized algorithm design + expected cost | Q3 (random-access search) | Q2 (coupon collector) |
| Divide & conquer / selection | Q4 (k-th of two sorted arrays) | Q3 (distinct values + frequencies) |

**So: the guaranteed core is (i) recurrences, (ii) heaps, (iii) randomization with expectation, (iv) one D&C design problem.** Prepare those four to perfection.

## 8.2 Question-by-question walkthroughs

### Spring-2026 Q1(a) — the "A / B / U" table
Method: reduce each entry to a canonical form, then compare growth classes. **Answer key: 1-B, 2-B, 3-U, 4-A, 5-B.**
- Case 1 `Θ(n²/(log n)^10)` vs `Θ(n^{1.5})`: the log factor is negligible against `n^{0.5}` → B faster.
- Case 2 `Θ(2^{√n})` vs `Θ(10^{n^{1/3}})`: compare exponents `√n` vs `n^{1/3}`; `n^{1/3}` wins → B faster.
- Case 3 `O(log n)` vs `Ω(log log n)`: **one-sided bounds, no conclusion → U**.
- Case 4 `Θ(5ⁿ)` vs `Θ(7ⁿ)`: smaller base → A faster.
- Case 5 `Θ(n²)` vs `T_B(n) = 2T_B(n/2)+n = Θ(n log n)`: **solve first** → B faster.

### Spring-2026 Q1(b) — expansion, Master Theorem forbidden
See Part 0.4(a). Deliverable form: `T(n) = Θ(n^α (log n)^β)` with `α=3, β=1`.

### Spring-2026 Q2 — coupon collector variants
See Part 4.7. Two traps: (a) you must argue **both** upper and lower bounds for the harmonic block; (b) part (b) asks for an **exact** formula, "do not use asymptotic notation".

### Spring-2026 Q3 — distinct values + frequencies in a sorted array, `O(log²n)`
**Structure of the input is the whole solution:** a sorted array with m distinct values is m contiguous blocks. Find the block boundaries with a `Search(A,p,r,x)` that returns the **last** index k with `A[k]=x` (given the precondition `A[p]=x`, `p ≤ r`):
```
Search(A, p, r, x):
    if p = r or A[r] = x then return r        // whole range is x → last index is r
    q ← ⌊(p+r)/2⌋
    if A[q] = x then
        if A[q+1] > x then return q           // found the boundary
        else return Search(A, q+1, r, x)      // boundary is to the right
    else return Search(A, p, q−1, x)          // boundary is to the left
```
Each block costs `O(log n)`, and there are `m = Θ(log n)` blocks (given!), so the total is `O(log²n)`. **The `m = Θ(log n)` hint in the question is what makes `O(log²n)` possible** — always look for where the given precondition does the work.

### Spring-2026 Q4(a) — min-heap insert trace
Insert 5, 8, 1, 4, 7, 3, 2, 6 into an initially empty min-heap; **draw the tree after each insertion**. Scoring: −0.5 per mistake, and **drawing only the final tree loses points**.

The array after each insertion (this is the reliable way to get the drawing right):
| Insert | Array `A[1..]` | Resulting root |
|---|---|---|
| 5 | `[5]` | 5 |
| 8 | `[5,8]` | 5 |
| 1 | `[1,5,8]` (1 swaps up to root) | 1 |
| 4 | `[1,4,8,5]` (4 < 5, swaps with A[2]) | 1 |
| 7 | `[1,4,8,5,7]` | 1 |
| 3 | `[1,3,8,5,7,4]` (3 < 8, swaps) | 1 |
| 2 | `[1,2,8,5,7,4,3]` (2 < 8, swaps) | 1 |
| 6 | `[1,2,6,5,7,4,3,8]` (6 < 8, swaps; 6 > 2 stop) | 1 |

**Corresponding trees:**
```
[5]        [5]          [1]            [1]              [1]
           /            /   \          /   \            /   \
         [8]          [5]   [8]      [4]   [8]        [4]   [8]
                                     /                /   \
                                   [5]              [5]   [7]

   [1]              [1]                [1]
  /   \            /   \              /   \
[3]   [8]        [2]   [8]          [2]   [6]
/ \   /          / \   / \         / \   / \
[5][7][4]      [5][7][4][3]      [5][7][4][3]
                                   /
                                 [8]
```
**Method that never fails:** do the *array* update first (append, then repeatedly swap `A[i]` with `A[⌊i/2⌋]` while the parent is larger), then read the tree off the array.

### Spring-2026 Q4(b) — the largest `⌈log₂n⌉` numbers in `O(n log log n)`
The trick is that `log n` output elements is *small*, so you can afford `log log n` per element but you must not sort globally.
1. Split `A` into `n/log²n` blocks of `log²n` elements each. Sort each block in `O(log²n · log log n)` (any `n log n` sort). Total `O(n log log n)`. Store each block as `Bᵢ[j] = (i, A[(i−1)log²n + j])`.
2. Build a **min-heap** containing the **first** pair of every `Bᵢ` (i.e. the smallest element of each block), with the heap ordered on the second component. That is `n/log²n` insertions → `O((n/log n)·log n) = O(n)`.
3. Repeat `⌈log₂n⌉` times: Extract-Min → output `v`; advance the pointer `pᵢ`; insert `Bᵢ[pᵢ]`. Cost `O(log n)` per extract-plus-insert, `log n` times → `O(log²n)`.

**Step 1 dominates: `O(n log log n)`.** Notice the design pattern: *pre-sort small blocks cheaply, then use a heap to merge lazily and extract only as many elements as you need.* This is exactly the "merge k sorted arrays with a heap" pattern of Part 6.6(a) with `k = n/log²n`.

### Fall-2025 Q2(b),(c) — heap merging and the `O(n)` build
See Part 6.4. The marked answer expects the `Σ k/2^{k+1} ≤ (1/4)S′(1/2) = 1` calculation.

### Fall-2025 Q3 — randomized search in a database, expected `O(n)`
**The interface:** `random-access(l,r)` returns a uniformly random record with key in `(l,r)`, or null; its **cost equals the number of keys in that range**. This is the "randomized binary search / quickselect on values" pattern.
```
l ← −∞; r ← ∞
repeat
    R ← random-access(l, r)
    if R = null then return "not found"
    if key(R) = x then return R
    if key(R) < x then r ← key(R) else l ← key(R)
```
**Cost analysis — reproduce this structure exactly:**
- Call a call **good** if the returned key is between the 25th and 75th percentile of the current range. `Pr[good] = 1/2`; a good call eliminates **at least 1/4** of the remaining keys.
- After i good calls at most `(3/4)ⁱ n` keys remain.
- Let `Xᵢ` = number of calls needed to get the (i+1)-st good call after i good calls. `E[Xᵢ] = 2`.
- Total cost `≤ Σ_{i≥0} Xᵢ(3/4)ⁱ n`, so `E[cost] ≤ Σ_i 2(3/4)ⁱ n = 2n · 1/(1−3/4) = 8n = O(n)`.

**This is structurally identical to the randomized-selection analysis of Part 5.5** — good pivot with probability 1/2, geometric waiting time 2, geometric decay `(3/4)ⁱ`. Recognise and reuse it.

### Fall-2025 Q4 — k-th smallest of two sorted arrays, `O(log k)`
See Part 5.6. Remember the `∞` padding rule for unequal lengths, and note the marked answer emphasises that at most one probe is out of range.

## 8.3 Answer templates that score full marks

**Template A — "Design an algorithm"**
1. One sentence on the **idea** and *why* it works structurally.
2. **Documented pseudocode** (the HW instructions define "Documented Pseudocode" as pseudocode *with comments inside it*). Use `←`, `⌊·⌋`, mathematical notation — not `++`, `*`, `%`.
3. **Correctness:** state the invariant or the case analysis (the three cases: left / right / crossing is the recurring shape).
4. **Running time:** write the recurrence, then solve it. Say which level dominates.
5. Only use untaught algorithms if you also analyse them — the paper says so explicitly.

**Template B — "Derive the exact asymptotic solution … show all steps"**
1. Write the recurrence.
2. Draw/describe level 0 and level 1 (and level 2) with node counts, per-node cost, level sums.
3. General level i.
4. Number of leaves and leaf cost.
5. Sum, then simplify to the requested form `Θ(n^α (log n)^β)`.

**Template C — "Show that it takes Ω(…)"**
1. Count the number of **distinct required outputs**.
2. That many leaves in the decision tree.
3. Binary tree with N leaves has height ≥ log N (or log₃ for ternary).
4. Conclude the height (worst-case running time) is Ω(…).

**Template D — "Find the expected number of …"**
1. Define an **indicator** `Xᵢ` per item/step.
2. Compute `E[Xᵢ] = Pr[Xᵢ = 1]` — usually a simple probability.
3. Sum by **linearity** (state that independence is *not* required).
4. Evaluate the sum; recognise `Hₙ = Θ(log n)`, or a geometric/arithmetic series.

**Template E — "Which algorithm is best here?"**
Check in this order: (1) is the key range linear in n? → counting/radix. (2) is the input distribution known/uniform? → bucket. (3) must stability be preserved? (4) is the worst case the criterion or the expected/average case? (5) is working space constrained? Then answer with algorithm **and** running time — the marking scheme gives 0.5 penalty for a correct algorithm with the wrong time and **zero for an incorrect algorithm**.

## 8.4 Common traps checklist

- [ ] `O` vs `Ω` vs `Θ` — never write `Θ` when you have only proved one direction.
- [ ] One-sided bounds cannot be compared. If the table gives `O(·)` for one and `Ω(·)` for the other, the answer is "not enough information".
- [ ] Solve any recurrence given in a comparison table **before** comparing.
- [ ] Check whether the Master Theorem is **banned** (it sometimes is, explicitly).
- [ ] `2^{2n} ≠ Θ(2ⁿ)`; `4^{log₂n} = n²`.
- [ ] When counting levels in a recursion tree, the **leaf level** is level `log_b n`, and there are `log_b n` *internal* levels (0 … log_b n − 1).
- [ ] Radix sort's correctness depends on **counting sort being stable**, and LSD must sort from the **least** significant digit.
- [ ] Counting sort's final loop must go **backwards** for stability.
- [ ] Build-Heap is `O(n)`, not `O(n log n)` — the naive product of "#calls × cost per call" is wrong because it ignores the height distribution.
- [ ] In 3-way quicksort, the `>` branch **does not advance i**.
- [ ] Randomized algorithm performance = worst-case input, **expected** over internal randomness.
- [ ] Quicksort with all-equal elements is `Θ(n²)` **in expectation** for naive partitioning.
- [ ] Insertion sort's running time is `Θ(n + I)` where I is the inversion count — use it when the inversion count is given.
- [ ] Heapsort is in-place (`Θ(1)` space) — do not confuse it with the "build a min-heap and extract n times" idea, which needs `Θ(n)` space.
- [ ] An empty subarray is **not allowed** in the maximum-subarray problem as defined in this course.

---

# PART 9 — One-Page Fact Dump (memorise last)

**Recurrences**
```
T(n)=T(n/2)+c            → Θ(log n)        [binary search]
T(n)=2T(n/2)+n           → Θ(n log n)      [mergesort, max-subarray, inversions]
T(n)=T(n/2)+n            → Θ(n)            [local minimum in a grid, HW2 Q1]
T(n)=4T(n/2)+n           → Θ(n²)           [naive multiply]
T(n)=3T(n/2)+n           → Θ(n^1.585)      [Karatsuba]
T(n)=7T(n/2)+n²          → Θ(n^2.807)      [Strassen]
T(n)=8T(n/2)+n²          → Θ(n³)           [naive matrix multiply]
T(n)=4T(n/4)+n log₄n     → Θ(n log²n)      [Fall-25 Q1a]
T(n)=64T(n/4)+n³         → Θ(n³ log n)     [Spr-26 Q1b]
T(n)=2T(n−1)+1           → Θ(2ⁿ)           [Hanoi]
T(n)=T(n−1)+n            → Θ(n²)           [quicksort worst case]
T(n)=T(n/4)+T(3n/4)+n    → Θ(n log n)      [quicksort average]
T(n)=2T(n/2)+n, k times  → Θ(n^k)
```

**Master Theorem** `T(n)=aT(n/b)+n`: `log_b a > 1` → `Θ(n^{log_b a})` (leaves dominate); `= 1` → `Θ(n log n)` (all levels); `< 1` → `Θ(n)` (root dominates).

**Randomization numbers**
```
Geometric waiting time           1/p
Hiring problem / records         Hₙ = Θ(log n)
Coupon collector (all n)         n·Hₙ = Θ(n log n)
Coupon collector (n/2 types)     Θ(n)
Date with n boxes, distinct      n − n(1−1/n)ⁿ ≈ (1 − 1/e)n ≈ 0.632n
Birthday collision pairs         C(k,2)/n → k ≈ √(2n)
Hashing, load factor α = n/m     O(1+α) expected per op
Quicksort comparisons            Σ_{i<j} 2/(j−i+1) = O(n log n)
Randomized selection             O(n), good pivot w.p. 1/2
Random-access DB search          O(n)   [Fall-25 Q3]
```

**Sorted-block / structure facts**
```
Inversions: A[i]>A[j] for i<j; computed in Θ(n log n) by merge-and-count
  I[j] = m − l − i + 2   (# blue elements greater than R[j])
Max subarray: n³ → n² → n log n → n (Kadane)
Stock profit: reduce to max subarray via Profit[i] = p[i+1] − p[i]
Local min in n×n grid: T(m)=T(m/2)+cm → Θ(n)
Binary search on a sorted array: Ω(log n) and O(log n)
Selection lower bound for k smallest in order: Ω(k log n)
```

**Heap facts**
```
Array: parent ⌊i/2⌋, left 2i, right 2i+1; height ⌊log n⌋
Heapify O(log n); Insert O(log n) (bubble up); Extract-Max O(log n) (bubble down)
Build-Max-Heap: loop i = ⌊n/2⌋ downto 1 → Θ(n) because Σ 2ⁱ(h−i) = O(n)
Heapsort: Θ(n log n), Θ(1) space, NOT stable, bad cache behaviour
k-th smallest in a heap: O(k log k) with an auxiliary heap of size ≤ k
Merge k sorted arrays: O(kn log k) by D&C or by a size-k min-heap
```

**Sorting table** (worst / expected / space / stable / comparison-based)
```
Insertion   Θ(n²)      / –            / Θ(1)     / Yes / Yes
Merge       Θ(n log n) / –            / Θ(n)     / Yes / Yes
Quicksort   Θ(n²)      / Θ(n log n)   / Θ(log n) / No  / Yes
Heapsort    Θ(n log n) / –            / Θ(1)     / No  / Yes
Counting    Θ(n+k)     / –            / Θ(n+k)   / Yes / No
Radix       Θ(d(n+k))  / –            / Θ(n+k)   / Yes / No     [d=⌈w/log n⌉, k=n → Θ(n)]
Bucket      Θ(n²)      / Θ(n)         / Θ(n)     / Yes / No
```

**The three sentences to write when you are stuck**
1. "The recurrence is `T(n) = aT(n/b) + f(n)`, and level i contributes `aⁱ f(n/bⁱ)`."
2. "By linearity of expectation, `E[ΣXᵢ] = ΣE[Xᵢ]`, and each `Xᵢ` is an indicator so `E[Xᵢ] = Pr[Xᵢ=1]`."
3. "Any solution falls into one of three cases: entirely left, entirely right, or crossing the midpoint."

---

*End of revision notes — COMP 3711 midterm.*
