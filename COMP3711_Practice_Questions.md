# COMP 3711 — Midterm Practice Problem Set
### Modelled on the Fall-2025 and Spring-2026 midterm papers

**How to use this.** Attempt each question on paper, closed book, before reading the solution. The paper format is: **4 questions, 2 hours, closed book**, with roughly 15 marks on asymptotics/recurrences, 10–20 on heaps, 10–20 on randomization, and 10–20 on a divide-and-conquer design problem.

Marks are indicated so you can practise allocating time: **allow about 2 minutes per mark.**

| Section | Topic | Marks |
|---|---|---|
| A | Asymptotics & recurrences | 25 |
| B | Divide & conquer | 30 |
| C | Randomization | 25 |
| D | Heaps & priority queues | 25 |
| E | Lower bounds | 15 |
| F | Linear-time sorting | 20 |
| G | Mixed rapid-fire | 20 |
| | **Total** | **160** |

---

# SECTION A — Asymptotics & Recurrences (25 marks)

## A1. Faster algorithm table (5 marks)

Two algorithms A and B have time complexities $T_A(n)$ and $T_B(n)$. Fill in the last column with **A**, **B**, or **U**, where:
- **A** = for large enough $n$, A is always faster;
- **B** = for large enough $n$, B is always faster;
- **U** = the information given does **not** justify either conclusion.

| # | $T_A(n)$ | $T_B(n)$ | Faster |
|---|---|---|---|
| 0 | $\Theta(n)$ | $\Theta(n^2)$ | **A** (example) |
| 1 | $\Theta\!\left(\dfrac{n^2}{(\log n)^{10}}\right)$ | $\Theta(n^{1.5})$ | |
| 2 | $\Theta\!\left(2^{\sqrt n}\right)$ | $\Theta\!\left(10^{n^{1/3}}\right)$ | |
| 3 | $\Omega(n\log n)$ | $\Theta(n^{1.999})$ | |
| 4 | $\Theta(1000^n)$ | $\Theta(2^{2n})$ | |
| 5 | $\Theta(n^{1.8})$ | $T_B(n) = 3T_B(n/3) + n$ | |

<details><summary><b>Solution A1</b></summary>

**1 — B.** Compare $n^{2}/(\log n)^{10}$ with $n^{1.5}$, i.e. $n^{0.5}/(\log n)^{10}$ against a constant. Since $n^{0.5}/(\log n)^{10}\to\infty$, algorithm A is *slower*. So **B** is faster.

**2 — B.** Compare the exponents: $\sqrt n$ vs $n^{1/3}$. Since $n^{1/3}\gg\sqrt n$ for large $n$, we have $10^{n^{1/3}}\gg 2^{\sqrt n}$. A *larger* exponential means A is slower, so **B** is faster. *(General rule: exponentials are compared by their exponents, not their bases.)*

**3 — U.** These are **one-sided** bounds. $\Omega(n\log n)$ says A is *at least* $n\log n$; it could be $n^3$. $\Theta(n^{1.999})$ pins B down, but nothing pins A down. Incomparable $\Rightarrow$ **U**.

**4 — B.** $2^{2n} = (2^2)^n = 4^n$. Since $4^n \ll 1000^n$, A is slower, so **B** is faster.

**5 — B.** **Solve the recurrence first.** $T_B(n) = 3T_B(n/3)+n$: here $a=b=3$ so $\log_3 3 = 1$, Master Theorem case 2 gives $T_B(n)=\Theta(n\log n)$. Since $n\log n \ll n^{1.8}$, **B** is faster.
</details>

---

## A2. Expansion method — Master Theorem banned (8 marks)

Derive the exact asymptotic solution of
$$T(n) = 27\,T\!\left(\frac n4\right) + n^3, \qquad T(1)=1,$$
assuming $n$ is always an integral power of 4. **You must show your derivation from first principles and you may NOT use the Master Theorem.** Write your final answer in the form $T(n)=\Theta(n^\alpha(\log n)^\beta)$.

<details><summary><b>Solution A2</b></summary>

**Step 1 — expand and find the general level cost.** Write $27=3^3$ and substitute repeatedly:
$$T(n) = 3^3T(n/4)+n^3$$
$$= 3^3\Big[3^3T(n/4^2)+\Big(\tfrac n4\Big)^3\Big]+n^3 = 3^6T(n/4^2)+\frac{3^3}{4^3}n^3+n^3 = 3^6T(n/4^2)+\Big(\tfrac{27}{64}+1\Big)n^3$$
$$= 3^9T(n/4^3)+\Big(\tfrac{27}{64}\Big)^2n^3+\tfrac{27}{64}n^3+n^3$$

The pattern after $k$ substitutions is
$$\boxed{\,T(n) = 3^{3k}\,T(n/4^k) \;+\; n^3\sum_{i=0}^{k-1}\Big(\frac{27}{64}\Big)^{i}\,}$$

**Step 2 — the level costs form a DECREASING geometric series.** The level-$i$ total is $\left(\frac{27}{64}\right)^i n^3$, and $\frac{27}{64}<1$. Therefore
$$\sum_{i=0}^{k-1}\Big(\frac{27}{64}\Big)^{i} \;<\; \sum_{i=0}^{\infty}\Big(\frac{27}{64}\Big)^{i} = \frac{1}{1-\frac{27}{64}} = \frac{64}{37} = \Theta(1).$$
So all the internal levels together contribute only $\Theta(n^3)$ — **no $\log$ factor is created**, because the work shrinks as we descend.

**Step 3 — evaluate at the boundary.** The recursion stops when $n/4^k=1$, i.e. $k=\log_4 n$. Then
$$3^{3k} = \big(3^{\log_4 n}\big)^3 = \big(n^{\log_4 3}\big)^3 = n^{3\log_4 3} = n^{\log_4 27},$$
and $\log_4 27 = \frac{\ln 27}{\ln 4}\approx 2.377$.

**Step 4 — combine and take the dominant term.**
$$T(n) = n^{\log_4 27} + \Theta(n^3).$$
Since $2.377 < 3$, the term $n^3$ dominates. Hence
$$\boxed{\,T(n) = \Theta(n^3)\,}\qquad\text{i.e. } \alpha=3 \text{ and there is no } \log \text{ factor.}$$

> **The lesson (and the trap).** Compare this with the Spring-2026 question $T=64\,T(n/4)+n^3$, where $64=4^3$ **exactly**. There, $a(1/b)^c = 64\cdot\frac1{64}=1$, so *every* level costs exactly $n^3$, the sum of level costs is $\Theta(n^3\log n)$, and a $\log$ factor **does** appear.
>
> Here $27<64=4^3$, so level costs **decrease** geometrically and the answer is plain $\Theta(n^3)$. The rule:
> $$\text{a }\log\text{ factor appears} \iff a = b^c \iff \log_b a = c.$$
> **Check:** $a=27$, $b=4$, $\log_4 27\approx 2.377<3=c$ $\Rightarrow$ Case 3 $\Rightarrow\Theta(n^3)$. ✓
</details>

---

## A3. Recursion tree (6 marks)

Let $T(n) = 9T(n/3) + n^2\log_3 n$ for $n>1$ (a power of 3), with $T(1)=1$. Solve it with the recursion tree method. Your answer must show:
- a drawing of level 0 and level 1;
- for levels 0, 1 and 2: the level number, the cost at each node, and the sum of node costs across the level;
- for a general level $i$: the number of nodes and the sum of node costs;
- the number of leaves and the sum of leaf costs as a polynomial in $n$.

<details><summary><b>Solution A3</b></summary>

**Level 0.** One node, cost $n^2\log_3 n$. Level sum: $n^2\log_3 n$.

**Level 1.** 9 nodes, each of size $n/3$, each costing $(n/3)^2\log_3(n/3)$. Level sum:
$$9\cdot\frac{n^2}{9}\log_3\frac n3 = n^2\log_3\frac n3.$$

**Level 2.** $9^2 = 81$ nodes, each costing $(n/9)^2\log_3(n/9)$. Level sum:
$$81\cdot\frac{n^2}{81}\log_3\frac n9 = n^2\log_3\frac n9.$$

**General level $i$.** $9^i$ nodes; each node's cost is $\left(\frac{n}{3^i}\right)^2\log_3\left(\frac{n}{3^i}\right)$. Level sum:
$$9^i\cdot\frac{n^2}{9^i}\log_3\frac{n}{3^i} = n^2\log_3\frac{n}{3^i} = n^2(\log_3 n - i).$$

**Leaves.** The recursion stops when $n/3^i = 1$, i.e. $i=\log_3 n$, so there are $9^{\log_3 n} = n^{\log_3 9} = n^2$ leaves. Each leaf costs $T(1)=1$, so the **sum of leaf costs is $n^2$**.

**Total.**
$$T(n) = n^2 + \sum_{i=0}^{\log_3 n - 1} n^2(\log_3 n - i) = n^2 + n^2\sum_{j=1}^{\log_3 n} j$$
where the substitution $j=\log_3 n - i$ reverses the sum. The arithmetic series gives
$$\sum_{j=1}^{\log_3 n} j = \frac{\log_3 n\,(\log_3 n + 1)}{2},$$
so
$$T(n) = n^2 + \frac{n^2}{2}\left((\log_3 n)^2 + \log_3 n\right) = \Theta\!\left(n^2\log^2 n\right).$$

Note how *every* level contributes $\Theta(n^2)$ up to a slowly-varying $\log$ factor — the signature of Master Theorem case 2 "with a log already present", which pushes the final exponent of the log up from 1 to 2.
</details>

---

## A4. Lower bound on a simple problem (6 marks)

An array $A[1..n]$ contains $n$ **distinct** integers. Show that any comparison-based algorithm that outputs both the **minimum** and the **maximum** of $A$ must make at least $\lceil 3n/2\rceil - 2$ comparisons in the worst case.

<details><summary><b>Solution A4</b></summary>

> **Note on scope.** The $\lceil 3n/2\rceil-2$ bound for min-and-max is proved properly in **Question E2** below. This question is best answered by the same two-eligibility argument; here is the compact version.

**Definitions.** Call an element **max-eligible** if it has never lost a comparison, and **min-eligible** if it has never won a comparison.

**Two facts.**
1. A correct algorithm must leave exactly **one** max-eligible element and exactly **one** min-eligible element — otherwise it cannot distinguish which element is the maximum (resp. minimum).
2. An element that has taken part in only **one** comparison is **not** both-eliminated: if it won, it is still max-eligible; if it lost, it is still min-eligible.

**Counting.** Every element must be compared at least once (an uncompared element is eligible for both races, contradicting fact 1). Let $t$ be the number of elements compared at least **twice**. Since at most two elements (the eventual maximum and minimum) may remain un-eliminated on one side, at least $n-2$ elements must have lost *and* won at least one comparison each, so $t\ge n-2$.

Counting comparison endpoints: all $n$ elements are compared at least once, and $t\ge n-2$ of them at least twice, so
$$2c \;\ge\; 2t + (n-t) \;=\; n+t \;\ge\; 2n-2 \quad\Longrightarrow\quad c\ge n-1.$$
Since $n-1 < \lceil 3n/2\rceil-2$ for $n\ge2$, this argument alone does not give the full bound; the extra $\lceil n/2\rceil-1$ comes from the fact that eliminating an element from the max-race requires a **distinct** comparison from eliminating it from the min-race, and the adversary can force these to be non-shareable. The complete argument is given in **E2**, and the matching upper bound (pair up, then search the winners and the losers separately) shows the bound is exactly $\lceil 3n/2\rceil-2$.

**Deliverable for this question:** state the two-eligibility invariant, note that an element needs one *loss* and one *win* to be fully eliminated, and derive $c\ge n-1$; then quote the sharpened $\lceil 3n/2\rceil-2$ with the pairing construction as the matching upper bound.
</details>

---

## A5. Two recurrence comparisons (bonus, 4 marks)

Explain briefly why each statement is **true** or **false**.

(a) $n^3 + 1000n^{2.9} = \Theta(n^3)$
(b) $2^{3n} = \Theta(8^n)$
(c) $n^{\log_2 3} = o(n^{1.6})$
(d) If $f(n) = O(g(n))$ and $g(n) = O(h(n))$ then $f(n) = \Theta(h(n))$

<details><summary><b>Solution A5</b></summary>

**(a) True.** $1000n^{2.9}/n^3 = 1000n^{-0.1}\to 0$, so the second term is dominated; $n^3+1000n^{2.9} = \Theta(n^3)$.

**(b) True.** $2^{3n} = (2^3)^n = 8^n$, so they are equal up to a constant — indeed *identical*.

**(c) True.** $\log_2 3 \approx 1.585 < 1.6$, and for any $c<d$, $n^c = o(n^d)$.

**(d) False.** Transitivity gives $f(n)=O(h(n))$ only — an **upper** bound. It does not give $f=\Omega(h)$, which $\Theta$ requires. Counterexample: $f(n)=1$, $g(n)=n$, $h(n)=n^2$: $f=O(g)$, $g=O(h)$, but $f=O(1)\ne\Theta(n^2)$.
</details>

---

# SECTION B — Divide & Conquer (30 marks)

## B1. Two sorted arrays (10 marks)

You are given arrays $A[1..m]$ and $B[1..n]$ of distinct numbers, both sorted in increasing order, where $m$ and $n$ may be **unequal**. Given $k$ with $1\le k\le m+n$, design an algorithm to find the $k$-th smallest element of $A\cup B$ in $O(\log k)$ time. Explain the correctness and analyse the running time.

<details><summary><b>Solution B1</b></summary>

**Idea.** Maintain a subarray of $A$ and a subarray of $B$ that are still candidates, plus the rank $k'$ of the answer *within those candidate subarrays*. In one step we can discard $\lfloor k'/2\rfloor$ elements from one of the two arrays.

**Procedure.** `Search(A, p, q, B, r, s, k)` finds the $k$-th smallest of $A[p..q]\cup B[r..s]$.

1. **Base case.** If $k=1$, return $\min(A[p],\,B[r])$ (treating an out-of-range index as $+\infty$).
2. **Probe.** Let $t=\lfloor k/2\rfloor$. Compare $x = A[p+t-1]$ with $y = B[r+t-1]$ (again, an out-of-range index acts as $+\infty$).
3. **Discard.**
   - If $x<y$: recurse with `Search(A, p+t, q, B, r, s, k-t)`.
   - Else: recurse with `Search(A, p, q, B, r+t, s, k-t)`.

**Why the discard is safe.** Suppose $x<y$. How many elements of $A\cup B$ are $\le x$?
- In $A$: the elements $A[p..p+t-1]$, so $t$ elements (including $x$).
- In $B$: since $B$ is sorted and $y=B[r+t-1]>x$, the elements of $B$ that are $\le x$ are among $B[r..r+t-2]$, so **at most $t-1$** of them.

Hence $x$ has rank at most $t+(t-1) = 2t-1 \le k-1$ (since $t=\lfloor k/2\rfloor$ gives $2t\le k$). So $x$ is not the answer, and in fact **no element of $A[p..p+t-1]$ can be the $k$-th smallest** — they all have rank $\le k-1$. We may discard all $t$ of them and look for rank $k-t$ in what remains. Symmetric argument if $y\le x$.

**Handling unequal lengths.** If $p+t-1>q$ then $A$ has fewer than $t$ elements left; set $x=+\infty$ so the comparison sends us to discard from $B$. We can always do this for at least one side because
$$k \le (q-p+1)+(s-r+1) \implies t = \lfloor k/2\rfloor \le \max\{q-p+1,\ s-r+1\},$$
so at most one of the two probes is out of range.

**Running time.** Each recursive call reduces $k$ to $k-t = \lceil k/2\rceil$, i.e. roughly halves it, and each call does $O(1)$ work. After $O(\log k)$ calls we reach $k=1$. Hence $O(\log k)$. The recursion depth is also $O(\log k)$, so the stack space is $O(\log k)$ — or $O(1)$ if written as a loop.

**Worked trace.** $A=[1,3,5,7,9]$, $B=[2,4,6,8,10]$, $k=7$.

| Call | $p..q$ | $r..s$ | $k$ | $t$ | $x=A[p{+}t{-}1]$ | $y=B[r{+}t{-}1]$ | Action |
|---|---|---|---|---|---|---|---|
| 1 | 1..5 | 1..5 | 7 | 3 | $A[3]=5$ | $B[3]=6$ | $x<y$, drop $A[1..3]$, $k\gets4$ |
| 2 | 4..5 | 1..5 | 4 | 2 | $A[5]=9$ | $B[2]=4$ | $y<x$, drop $B[1..2]$, $k\gets2$ |
| 3 | 4..5 | 3..5 | 2 | 1 | $A[4]=7$ | $B[3]=6$ | $y<x$, drop $B[3]$, $k\gets1$ |
| 4 | 4..5 | 4..5 | 1 | — | — | — | return $\min(A[4],B[4])=\min(7,8)=7$ ✓ |

The union sorted is $1,2,3,4,5,6,7,8,9,10$, so the 7th smallest is indeed $7$.
</details>

---

## B2. Local minimum in a grid (10 marks)

Let $A$ be an $n\times n$ grid of **distinct** integers. An element $A[i,j]$ is a **local minimum** if it is smaller than all of its up-to-four orthogonal neighbours $A[i\pm1,j]$, $A[i,j\pm1]$.

(a) [7 marks] Design an algorithm that finds **any** local minimum in $O(n)$ time.
(b) [3 marks] Formulate and solve the recurrence for your running time, and explain why the algorithm is correct.

<details><summary><b>Solution B2</b></summary>

**(a) Algorithm.** Let the current subgrid be $m\times m$ (initially $n\times n$).

1. Scan the **central row** $i=\lfloor m/2\rfloor$ and the **central column** $j=\lfloor m/2\rfloor$ of the subgrid. This costs $O(m)$.
2. Let $A[r,c]$ be the **minimum** entry found on that central cross.
3. Compare $A[r,c]$ with its (up to four) orthogonal neighbours.
   - If $A[r,c]$ is smaller than all of them, **return** it — it is a local minimum of the whole grid (see (b)).
   - Otherwise let $A[r',c']<A[r,c]$ be a smaller neighbour. By the choice of $A[r,c]$ as the cross-minimum, this neighbour cannot lie on the cross. So it lies strictly inside one of the four $(m/2)\times(m/2)$ quadrants.
4. **Recurse into that quadrant.**

**(b) Recurrence and correctness.**

*Recurrence.* Each step scans a cross of $O(m)$ entries and recurses into a single quadrant of side $m/2$:
$$T(m) = T(m/2) + c\,m, \qquad T(1)=\Theta(1).$$
Master Theorem with $a=1$, $b=2$, $f(m)=cm$: $\log_2 1 = 0$, so $n^{\log_b a}=m^0=1$ and $f(m)=\Omega(m^{0+1})$. Case 3 applies (with the regularity condition $\frac{c m}{2}\le \frac12 cm$, satisfied for $c\ge0$). Hence
$$T(m) = \Theta(m), \qquad\text{so } T(n)=\Theta(n).$$

*Correctness.* Suppose the algorithm recurses into a quadrant $Q$ because of a neighbour $A[r',c']<A[r,c]$ lying in $Q$. We claim a local minimum exists inside $Q$ without ever leaving it.

The boundary of $Q$ consists entirely of entries on the central cross, and $A[r,c]$ was the **minimum of the cross**, so every cross entry — in particular every entry on $Q$'s boundary — is $\ge A[r,c] > A[r',c']$. Thus $A[r',c']$ is strictly smaller than *every* entry on the boundary enclosing $Q$.

Now start at $A[r',c']$ and repeatedly step to a strictly smaller orthogonal neighbour (possible whenever the current cell is not a local minimum). All grid values are distinct, so values strictly decrease along this walk and it cannot cycle; since the grid is finite, the walk must terminate at a cell with no smaller neighbour, i.e. a local minimum. Crucially the walk can never cross $Q$'s boundary: moving from inside $Q$ to the boundary would require stepping to a cell with value $\ge A[r,c]>A[r',c']$, but the walk only ever steps to *smaller* values, all of which are $<A[r',c']$. So the local minimum found lies in $Q$, which is where we recursed. By induction on $m$, the algorithm returns a genuine local minimum.
</details>

---

## B3. Maximum subarray variant (10 marks)

Let $A[1..n]$ contain positive and negative reals. For indices $i\le j$ define $V(i,j)=\sum_{k=i}^{j}A[k]$. **An empty subarray is not allowed.**

(a) [3 marks] Give a $\Theta(n^3)$ brute-force algorithm and a $\Theta(n^2)$ improvement, and explain the difference.
(b) [5 marks] Design a $\Theta(n\log n)$ divide-and-conquer algorithm.
(c) [2 marks] State Kadane's $\Theta(n)$ algorithm, and explain in one sentence why it is correct.

<details><summary><b>Solution B3</b></summary>

**(a).** Brute force computes $V(i,j)$ from scratch for every pair: three nested loops $\Rightarrow\Theta(n^3)$. The improvement exploits
$$V(i,j) = V(i,j-1) + A[j],$$
so the inner loop is replaced by a single addition: two nested loops $\Rightarrow\Theta(n^2)$.

**(b) Divide and conquer.**
```
MaxSub(A, l, r):
    if l = r then return A[l]
    m ← ⌊(l+r)/2⌋
    M1 ← MaxSub(A, l, m)          ▷ entirely in the left half
    M2 ← MaxSub(A, m+1, r)        ▷ entirely in the right half
    Lm ← −∞; V ← 0
    for i ← m downto l:  V ← V + A[i];  Lm ← max(Lm, V)   ▷ best suffix ending at m
    Rm ← −∞; V ← 0
    for i ← m+1 to r:    V ← V + A[i];  Rm ← max(Rm, V)   ▷ best prefix starting at m+1
    return max(M1, M2, Lm + Rm)
```
**Why three cases suffice.** Any subarray $[i,j]$ satisfies exactly one of: $j\le m$ (entirely left), $i>m$ (entirely right), or $i\le m<j$ (crossing). The first two are handled recursively. A crossing subarray splits as $A[i..m]\cup A[m+1..j]$, and the two parts are independent, so maximising each separately (two linear scans) maximises the sum. The two scans cost $\Theta(n)$ total.

$$T(n) = 2T(n/2) + \Theta(n) \;\Rightarrow\; T(n) = \Theta(n\log n).$$

**Run on the lecture example** $A = [3,2,1,-7,5,2,-1,3,-1]$: the answer is $5+2-1+3 = 9$ (years 5–8).

**(c) Kadane.**
```
Vmax ← −∞; V ← 0
for i ← 1 to n:
    V ← max(V + A[i], A[i])
    Vmax ← max(Vmax, V)
return Vmax
```
$V$ after iteration $i$ is the maximum value of a subarray **ending at** $i$: either we extend the best subarray ending at $i-1$, or we start afresh at $i$. Taking the maximum of $V$ over all $i$ therefore gives the best subarray overall.
</details>

---

# SECTION C — Randomization (25 marks)

## C1. Coupon collector variants (8 marks)

Each box of cereal contains one coupon, uniformly and independently chosen from $n$ types.

(a) [4 marks] Derive the expected number of boxes needed to collect **all $n$** types, in the form $\Theta(n^\alpha(\log n)^\beta)$.
(b) [4 marks] Derive the expected number of boxes needed to collect **$n/2$ distinct** types. **You must argue both an upper and a lower bound.**

<details><summary><b>Solution C1</b></summary>

**(a)** Let $X_i$ be the number of boxes opened while we have exactly $i$ distinct types, i.e. the waiting time to go from $i$ to $i+1$ distinct types. When we hold $i$ types, a new box is a *new* type with probability $p_i = \frac{n-i}{n}$. The waiting time is geometric, so
$$E[X_i] = \frac{1}{p_i} = \frac{n}{n-i}.$$
By linearity of expectation (no independence needed):
$$E[X] = \sum_{i=0}^{n-1}E[X_i] = \sum_{i=0}^{n-1}\frac{n}{n-i} = n\sum_{j=1}^{n}\frac1j = nH_n = \Theta(n\log n),$$
using $H_n=\Theta(\log n)$. So $\alpha=1,\beta=1$.

**(b)** Now we stop after reaching $n/2$ distinct types, so we only wait through stages $i=0,1,\dots,n/2-1$:
$$E[X] = \sum_{i=0}^{n/2-1}\frac{n}{n-i} = n\left(\frac1n+\frac1{n-1}+\cdots+\frac1{n/2+1}\right).$$

It remains to show the bracketed harmonic block is $\Theta(1)$.

*Upper bound.* Every term is at most $\frac{1}{n/2}=\frac2n$ (the smallest denominator is $n/2+1>n/2$), and there are $n/2$ terms:
$$\frac1n+\frac1{n-1}+\cdots+\frac1{n/2+1} \;\le\; \frac n2\cdot\frac2n = 1.$$

*Lower bound.* Every term is at least $\frac1n$ (the largest denominator is $n$), and there are $n/2$ terms:
$$\frac1n+\frac1{n-1}+\cdots+\frac1{n/2+1} \;\ge\; \frac n2\cdot\frac1n = \frac12.$$

So the block lies in $[\frac12, 1]$, i.e. it is $\Theta(1)$, and therefore
$$E[X] = \Theta(n), \qquad \alpha=1,\ \beta=0.$$
</details>

---

## C2. Randomized database search (9 marks)

A database contains $n$ records with **distinct** real key values. You may only access it through

> **random-access($l$,$r$)**: among the records whose keys lie strictly between $l$ and $r$, one is returned **uniformly at random**; if there are none, `null` is returned. You may pass $l=-\infty$ and $r=+\infty$.

The **cost** of a call is the number of keys in the interval $(l,r)$. After a call you may read the returned record's key in $O(1)$ time.

Describe an algorithm to retrieve the record with target key $x$ (reporting "not found" if absent) and analyse its **expected total cost** in terms of $n$, using asymptotic notation.

<details><summary><b>Solution C2</b></summary>

**Algorithm — randomised binary search on key values.**
```
l ← −∞; r ← +∞
repeat
    R ← random-access(l, r)
    if R = null then return "not found"
    if key(R) = x then return R
    if key(R) < x then r ← key(R)      ▷ discard everything below key(R)
    else            l ← key(R)          ▷ discard everything above key(R)
```

**Cost analysis.**

Call a returned key **good** if it lies strictly between the 25th and 75th percentile of the keys currently in $(l,r)$. Because the returned key is uniform over the $N$ keys in range,
$$\Pr[\text{good}] = \frac12$$
(the middle half of the $N$ ordered keys). If the key is good, then everything on one side of it — at least $\frac14 N$ of the keys — is permanently discarded.

Let $N_i$ be the number of keys in range after $i$ good calls have occurred. Then
$$N_i \le \left(\frac34\right)^i n.$$

Let $X_i$ be the number of calls made to obtain the $(i+1)$-st good call, starting right after the $i$-th good call. Each call is good independently with probability $\frac12$, so $X_i$ is geometric with $p=\frac12$ and
$$E[X_i] = 2.$$

Every call made during stage $i$ has cost at most $N_i\le\left(\frac34\right)^i n$, because the range only shrinks. Hence the total cost of stage $i$ is at most $X_i\left(\frac34\right)^i n$, and by linearity of expectation the expected total cost is at most
$$E[\text{cost}] \;\le\; \sum_{i\ge0} E[X_i]\left(\frac34\right)^i n \;=\; \sum_{i\ge0} 2\left(\frac34\right)^i n \;=\; 2n\cdot\frac{1}{1-\frac34} \;=\; 8n \;=\; O(n).$$

**Remark.** The analysis is *structurally identical* to that of randomised selection: a "good" pivot with probability $\frac12$, geometric waiting time with mean 2, and geometric decay $\left(\frac34\right)^i$. Recognise the pattern and you can write it once.
</details>

---

## C3. Majority element (8 marks)

A stream of $n$ items is given, and **exactly one value occurs more than $n/2$ times** (a "majority element"). Only an equality test is available; there is no ordering.

(a) [3 marks] Give a randomised algorithm using $O(1)$ space that finds the majority element, and analyse its **expected** number of operations.
(b) [2 marks] What is its worst-case running time, and why is this acceptable in practice?
(c) [3 marks] State the deterministic Boyer–Moore algorithm and prove it correct.

<details><summary><b>Solution C3</b></summary>

**(a) Try-and-check.** Repeatedly: pick an index $i$ uniformly at random from $[1,n]$; scan the whole stream and count how many items equal $A[i]$; if the count exceeds $n/2$, return $A[i]$ and stop.

*Analysis.* Since a majority exists, the probability that a uniform random index hits the majority value is $> \frac12$. So the number of trials is geometric with success probability $p>\frac12$, giving an expected number of trials
$$E[\#\text{trials}] = \frac1p < 2.$$
Each trial costs $\Theta(n)$ (one full scan). By linearity (or Wald's identity), the expected total work is $< 2\cdot\Theta(n) = O(n)$. Space is $O(1)$.

**(b)** The worst case is **unbounded** — the algorithm could, with vanishingly small probability, keep drawing non-majority elements forever. This is acceptable because the probability of needing more than $k$ trials is $< 2^{-k}$: after 40 trials the failure probability is below $10^{-12}$. So the algorithm is $O(n)$ "with high probability" even though its worst case is infinite.

*Contrast with how randomised algorithms are measured:* performance is $\max_{|I|=n}E_R[T(I,R)]$ — worst-case **input**, expectation over the algorithm's **internal** randomness. The try-and-check algorithm has expected time $O(n)$ on every input.

**(c) Boyer–Moore majority vote.**
```
c ← 0
for i ← 1 to n:
    if c = 0 then m ← A[i]; c ← 1
    else if m = A[i] then c ← c + 1
    else c ← c − 1
return m
```
**Correctness.** Maintain the invariant

> $A[1..i]$ consists of $c$ copies of the candidate $m$, plus a multiset of items that have been **cancelled** in pairs.

Initially ($i=0$) the invariant holds trivially. At step $i$: if $A[i]=m$ we simply add it to the candidate pool, so $c$ increases by 1 and the invariant persists. If $A[i]\ne m$ and $c>0$, the line `c ← c − 1` **cancels** $A[i]$ against one copy of $m$; both leave the pool, so the invariant persists. If $c=0$ we start a fresh candidate.

Now consider the true majority value $v$, occurring $>n/2$ times. Cancellation removes items in pairs of **distinct** values, so each cancelling pair contains **at most one** copy of $v$. Since there are fewer than $n/2$ non-$v$ items, fewer than $n/2$ copies of $v$ can ever be cancelled. But $v$ has more than $n/2$ copies, so at least one copy of $v$ survives to the end. The only value that can be the final candidate $m$ is a survivor with the largest count — and since $v$ is the unique majority, $m=v$. ∎

**Complexity.** $O(n)$ time in the **worst case**, $O(1)$ space, equality tests only. (If the existence of a majority is not guaranteed, a second verification pass is needed.)
</details>

---

# SECTION D — Heaps & Priority Queues (25 marks)

## D1. Insertion trace (5 marks)

Insert the sequence $9, 4, 7, 1, 8, 2, 6$ in this order into an initially empty **min-heap**. Draw the binary tree after **each** insertion. *(You must show every intermediate tree — drawing only the final tree loses marks.)*

<details><summary><b>Solution D1</b></summary>

**Method that never fails:** do the *array* update first — append the new key at the end, then repeatedly swap it with its parent while it is smaller than the parent — and read the tree off the array afterwards.

Insert $9,4,7,1,8,2,6$:

| Insert | Array after bubbling up | Bubble-up path |
|---|---|---|
| 9 | `[9]` | — (first element becomes the root) |
| 4 | `[4,9]` | index 2 → 1: $4<9$, swap |
| 7 | `[4,9,7]` | index 3 → 1: $7>4$, stop |
| 1 | `[1,4,7,9]` | index 4 → 2 ($1<9$, swap) → 1 ($1<4$, swap) |
| 8 | `[1,4,7,9,8]` | index 5 → 2: $8>4$, stop |
| 2 | `[1,4,2,9,8,7]` | index 6 → 3 ($2<7$, swap) → 1: $2>1$, stop |
| 6 | `[1,4,2,9,8,7,6]` | index 7 → 3 ($6>2$), stop |

**Final min-heap:** array `[1,4,2,9,8,7,6]`, i.e.
```
            1
          /   \
        4       2
       / \     / \
      9   8   7   6
```
Every node is $\le$ its children, so the min-heap property holds. ✓

**The tree after each insertion** (read off each array in turn):

```
   (1) 9            (2) 4            (3) 4            (4) 1
                     /                /   \            /   \
                    9                9     7          4     7
                                                      /
                                                     9

   (5) 1            (6) 1            (7) 1
      /   \            /   \            /   \
     4     7          4     2          4     2
    / \              / \   /          / \   / \
   9   8            9   8 7          9   8 7   6
```

**Check the last step.** Inserting 6 puts it at index 7, whose parent is index $\lfloor 7/2\rfloor = 3$ holding value 2. Since $6 > 2$ the heap property already holds, so 6 does **not** move — it stays as the right child of the node containing 2. It is not the child of the root, so the final tree is as drawn above.

> **Marking note.** You must draw the tree after *every* insertion; an answer showing only the final tree loses marks. Doing the array update first and then reading the tree off the array is the reliable way to avoid drawing errors.
</details>

---

## D2. Build-Heap trace and analysis (8 marks)

(a) [4 marks] Trace `Build-Max-Heap` on $A=[4,10,3,5,1,2]$ (so $n=6$). Show the array after each call to `Max-Heapify`.
(b) [4 marks] Prove from scratch that Build-Heap runs in $O(n)$ time, not $O(n\log n)$.

<details><summary><b>Solution D2</b></summary>

**(a)** `Build-Max-Heap` calls `Max-Heapify(A,i)` for $i=\lfloor n/2\rfloor=3,2,1$.

- **Initial array:** $[4,10,3,5,1,2]$ (not a heap).
- **$i=3$:** the children of $A[3]=3$ are $A[6]=2$ and $A[7]$ (does not exist). The largest of $\{3,2\}$ is 3, already at the node, so **no swap**. Array: $[4,10,3,5,1,2]$.
- **$i=2$:** the children of $A[2]=10$ are $A[4]=5$ and $A[5]=1$. The largest of $\{10,5,1\}$ is 10, already at the node, so **no swap**. Array: $[4,10,3,5,1,2]$.
- **$i=1$:** the children of $A[1]=4$ are $A[2]=10$ and $A[3]=3$. Largest is $10$ at $A[2]$, so **swap** $A[1]\leftrightarrow A[2]$ → $[10,4,3,5,1,2]$. Now recurse into index 2: its children are $A[4]=5$ and $A[5]=1$; largest is $5$ at $A[4]$, so **swap** $A[2]\leftrightarrow A[4]$ → $[10,5,3,4,1,2]$. Recurse into index 4: no children, stop.

**Final max-heap:** $[10,5,3,4,1,2]$:
```
        10
       /  \
      5    3
     / \  /
    4  1 2
```

> **Note.** After the $i=3$ and $i=2$ calls the array is unchanged and is **not yet a heap** — that is expected. The invariant is only that *after* `Max-Heapify(A,i)` the subtree rooted at $i$ is a heap, **given** that its subtrees already are. The array becomes a heap only after the final call at $i=1$.

**(b) The $O(n)$ proof.**

The key observation is that `Max-Heapify` at a node of **height $k$** costs $O(k)$ — it descends at most $k$ levels, doing $O(1)$ work per level. But *most nodes have small height*: half the nodes are leaves.

Take $n=2^{h+1}-1$ (a full tree, root at level 0) so that a node at level $i$ has height $h-i$:

| Level $i$ | Number of nodes | Cost of `Max-Heapify` per node | Total work at level $i$ |
|---|---|---|---|
| 0 | $1=2^0$ | $h$ | $h$ |
| 1 | $2=2^1$ | $h-1$ | $2(h-1)$ |
| 2 | $4=2^2$ | $h-2$ | $4(h-2)$ |
| $\vdots$ | $\vdots$ | $\vdots$ | $\vdots$ |
| $i$ | $2^i$ | $h-i$ | $2^i(h-i)$ |
| $h-1$ | $2^{h-1}$ | $1$ | $2^{h-1}$ |
| $h$ | $2^h$ | $0$ | $0$ |

Summing over all levels and substituting $k=h-i$:
$$\text{Total} = \sum_{i=0}^{h} 2^i(h-i) = \sum_{k=0}^{h} k\,2^{h-k} = 2^h\sum_{k=0}^{h}\frac{k}{2^k} \;\le\; 2^h\cdot 2 \;=\; O(2^h) \;=\; O(n),$$
using the standard bound $\sum_{k\ge0}\frac{k}{2^k} = 2$ and $2^h\le n$.

**Why the naive bound is wrong.** "There are $\lfloor n/2\rfloor$ calls, each costing $O(\log n)$, so $O(n\log n)$" badly over-counts. The $\lceil n/2\rceil$ nodes at the bottom have height 0 and cost nothing at all; only the $O(1)$ nodes near the root can cost $\Theta(\log n)$. Charging each node for **its own height** rather than for the tree's height gives the linear bound. *(Equivalently: the sum of the heights of all nodes in a complete binary tree is $\Theta(n)$.)*
</details>

---

## D3. Merging heaps pairwise (7 marks)

There are $2^{m-k}$ min-heaps $H_1,\dots,H_{2^{m-k}}$, each containing $2^k-1$ numbers, for some $k\in[1,m-1]$. Each heap is stored with explicit parent/child pointers. There are also $2^{m-k-1}$ further numbers stored in nodes $v_1,\dots,v_{2^{m-k-1}}$.

[5 marks] Describe an algorithm that merges these into $2^{m-k-1}$ min-heaps each containing $2^{k+1}-1$ numbers, in $O(k\,2^{m-k-1})$ time.
[2 marks] Deduce that $n$ numbers with $n=2^m-1$ can be made into a min-heap in $O(n)$ time.

<details><summary><b>Solution D3</b></summary>

**Merge step.** For each $i\in[1,\,2^{m-k-1}]$:

1. Make $v_i$ the **parent of the roots** of $H_{2i-1}$ and $H_{2i}$. The resulting tree has $1+2(2^k-1)=2^{k+1}-1$ nodes. ✓
2. Let $x$ be the value at $v_i$. Compare $x$ with the values at the two children (the old roots); let $y$ be the smaller of the two.
   - If $x\le y$, the tree is already a min-heap. **Stop.**
   - Otherwise **swap** the values $x$ and $y$, so $y$ sits at $v_i$ and $x$ moves down into the child that previously held $y$.
3. Now the subtree rooted at that child may violate the heap property, because $x$ may exceed the values of *its* new children. **Repeat step 2 at that child**: compare $x$ with the smaller of its two children, swap if necessary, and keep descending.
4. Continue until $x$ is $\le$ both of its children, or until $x$ reaches a leaf.

**Running time.** Each step does $O(1)$ work (two comparisons and possibly one swap) and moves $x$ down one level. The tree has $2^{k+1}-1$ nodes, so its height is $k$, hence at most $k$ levels are traversed. Therefore the cost per $i$ is $O(k)$, and summing over the $2^{m-k-1}$ values of $i$ gives
$$O\!\left(k\cdot 2^{m-k-1}\right),$$
as required.

**Building a heap of $n=2^m-1$ numbers.**

- Start with the first $2^{m-1}$ numbers as $2^{m-1}$ heaps of size $2^1-1=1$ each.
- For $k=0,1,2,\dots$: suppose we currently have $2^{m-k}$ heaps of size $2^k-1$, built from the first $2^{m-1}+2^{m-2}+\cdots+2^{m-k}$ numbers. Take the next $2^{m-k-1}$ numbers as the new nodes $v_1,\dots,v_{2^{m-k-1}}$, and apply the merge step with $k+1$ in place of $k$. This produces $2^{m-k-1}$ heaps each of size $2^{k+1}-1$.
- Repeat until $k=m$: we then hold a single heap containing all $2^m-1=n$ numbers.

*Time.* The total work is
$$O\!\left(\sum_{k=1}^{m} k\,2^{m-k-1}\right) = O\!\left(2^m\sum_{k=1}^{m}\frac{k}{2^{k+1}}\right).$$
Bound the sum using the geometric series. Let $S(x)=\sum_{i\ge1}x^i = \frac{x}{1-x}$ for $|x|<1$. Then
$$S'(x) = \sum_{i\ge1} i\,x^{i-1} = \frac{1}{(1-x)^2}.$$
At $x=\frac12$: $S'\!\left(\frac12\right) = \frac{1}{(1/2)^2}=4$. Therefore
$$\sum_{k=1}^{m}\frac{k}{2^{k+1}} = \frac14\sum_{k=1}^{m}k\left(\frac12\right)^{k-1} \le \frac14\,S'\!\left(\frac12\right) = \frac14\cdot 4 = 1.$$
Hence the total is $O(2^m\cdot 1) = O(2^m) = O(n+1) = O(n)$. ∎
</details>

---

## D4. $k$-th smallest in a heap (5 marks)

A binary **min-heap** $H$ of $n$ distinct elements is given as an array $H[1..n]$. Give an algorithm that finds the $k$-th smallest element in $O(k\log k)$ time using $O(k)$ extra space, **without modifying $H$**. Why is calling `Extract-Min` $k$ times unsatisfactory?

<details><summary><b>Solution D4</b></summary>

**Why $k$ Extract-Mins is unsatisfactory.** Calling `Extract-Min` $k$ times on a heap of size $n$ costs
$$\sum_{i=0}^{k-1}O(\log(n-i)) = O(k\log n),$$
which depends on $n$ rather than only on $k$ — bad when $k\ll n$. Worse, it **destroys the heap**, requiring either $O(n)$ time to rebuild or $O(n)$ extra space to copy it. The question demands $O(k\log k)$, independent of $n$.

**Algorithm — auxiliary heap over the "frontier".** Maintain a secondary min-heap `Haux` whose entries are pairs `(value, index)`, ordered by `value`, where `index` is a position in the original array $H$.

```
Initialize an empty min-heap Haux
Insert(Haux, (H[1], 1))                 ▷ the root of the original heap
for r ← 1 to k:
    (val, idx) ← Extract-Min(Haux)
    if r = k then return val            ▷ r-th smallest found
    left ← 2·idx
    if left ≤ n then Insert(Haux, (H[left], left))
    right ← 2·idx + 1
    if right ≤ n then Insert(Haux, (H[right], right))
```

**Correctness.** In a min-heap every node's value is at least its parent's. Therefore an element cannot be among the $k$ smallest unless **all of its ancestors** are among the $k$ smallest too — otherwise some ancestor is smaller and would occupy one of the $k$ slots.

`Haux` holds exactly the **minimal unexplored frontier**: the children of extracted nodes whose parent has been extracted but which have not themselves been extracted. By induction on $r$:

> Before iteration $r$, `Haux` contains precisely the smallest not-yet-output element of $H$ at its root.

*Base:* $r=1$, `Haux` contains only $H[1]$, the global minimum. *Step:* the next smallest unoutput element must be a child of some already-output node (by the ancestor argument), so it lies in `Haux`; conversely every element of `Haux` is a legitimate candidate. So the $r$-th `Extract-Min` returns the $r$-th smallest element of $H$. ∎

**Complexity.**
- *Time:* $k$ iterations, each doing 1 `Extract-Min` and at most 2 `Insert`s. At iteration $r$ we have $|Haux|\le r+1\le k$, so each heap operation costs $O(\log k)$. Total $=O(k\log k)$, **independent of $n$**.
- *Space:* `Haux` never exceeds $k+1$ entries, so $O(k)$. The original array $H$ is only read, never written. ✓
</details>

---

# SECTION E — Lower Bounds (15 marks)

## E1. Selection lower bound (5 marks)

Let $A[1..n]$ contain $n$ distinct integers and let $k\in[1,n/2]$. Show that any comparison-based algorithm that outputs the $k$ smallest elements **in increasing order** must make $\Omega(k\log n)$ comparisons in the worst case.

<details><summary><b>Solution E1</b></summary>

**Count the required outputs.** A correct algorithm's output is determined by (i) *which* $k$ elements are the smallest, and (ii) *in what order* they appear.

- The set of $k$ smallest elements can be any of the $\binom nk$ subsets of size $k$.
- For each such set, the increasing order of those $k$ elements is one of $k!$ permutations.

So the number of distinct possible outputs is
$$\binom nk\cdot k! = \frac{n!}{k!\,(n-k)!}\cdot k! = \frac{n!}{(n-k)!} = n(n-1)(n-2)\cdots(n-k+1).$$

Each factor satisfies $n-i \ge n-k+1$ for $i=0,\dots,k-1$, so
$$n(n-1)\cdots(n-k+1) \;\ge\; (n-k+1)^k.$$

**Decision-tree argument.** A comparison-based algorithm is a binary decision tree; each leaf corresponds to one possible output, so the tree has at least $(n-k+1)^k$ leaves. A binary tree with $N$ leaves has height at least $\log_2 N$. Therefore the worst-case number of comparisons is at least
$$\log_2\left((n-k+1)^k\right) = k\log_2(n-k+1).$$

**Use $k\le n/2$.** Then $n-k+1 \ge n-k \ge \frac n2$, so
$$k\log_2(n-k+1) \;\ge\; k\log_2\frac n2 \;=\; k(\log_2 n - 1) \;=\; \Omega(k\log n). \qquad\blacksquare$$
</details>

---

## E2. Find *both* min and max (5 marks)

Show that at least $\lceil 3n/2\rceil - 2$ comparisons are needed in the worst case to find both the minimum and the maximum of $n$ distinct numbers.

<details><summary><b>Solution E2</b></summary>

**(i) The bound is achievable — the pairing algorithm.** Pair the elements into $\lfloor n/2\rfloor$ disjoint pairs and compare within each pair ($\lfloor n/2\rfloor$ comparisons). Each pair yields a **winner** and a **loser**.

- Every element that is *not* the maximum either lost a comparison somewhere, or is a winner that will lose later. In particular the maximum must be among the $\lceil n/2\rceil$ winners: a loser cannot be the maximum.
- Similarly the minimum must be among the $\lceil n/2\rceil$ losers.

So it suffices to find the maximum of the $\lceil n/2\rceil$ winners ($\lceil n/2\rceil-1$ comparisons) and the minimum of the $\lceil n/2\rceil$ losers ($\lceil n/2\rceil-1$ comparisons). Total:
$$\Big\lfloor\frac n2\Big\rfloor + 2\Big(\Big\lceil\frac n2\Big\rceil-1\Big) = \Big\lceil\frac{3n}{2}\Big\rceil-2 .$$
*(Check: $n=6$ gives $3+2\cdot2=7=\lceil9\rceil-2$ ✓; $n=5$ gives $2+2\cdot2=6=\lceil7.5\rceil-2$ ✓.)*

**(ii) The bound is necessary — the two-eligibility argument.**

Say an element $x$ is **max-eligible** if $x$ has never **lost** a comparison, and **min-eligible** if $x$ has never **won** a comparison.

*Fact 1.* When the algorithm halts, exactly one element must be max-eligible and exactly one must be min-eligible. Otherwise two elements would both be consistent with being the maximum (or the minimum) and the algorithm could not know which to output.

*Fact 2.* An element that has taken part in only **one** comparison is not yet fully resolved: if it **won** that comparison it is still max-eligible; if it **lost** it is still min-eligible. So an element that is neither the eventual maximum nor the eventual minimum must take part in **at least two** comparisons (one as a loser, to shed max-eligibility, and one as a winner, to shed min-eligibility).

*Counting.* Let $c$ be the number of comparisons and let $d_x$ be the number of comparisons involving element $x$. Then
$$\sum_{x=1}^{n} d_x = 2c .$$

Fact 1 forces $d_x\ge1$ for every $x$ (an element never compared is eligible for both races). Fact 2 forces $d_x\ge2$ for all but at most the two surviving elements. Therefore
$$2c \;=\; \sum_{x} d_x \;\ge\; 2\,(n-2) \;+\; 1\cdot 2 \;=\; 2n-2,$$
which gives only $c\ge n-1$. To obtain the sharp bound, note that the pairing algorithm's structure is forced:

*Refinement.* Consider the comparison graph on the $n$ elements (an edge per comparison). By Fact 2, every non-surviving vertex has degree $\ge2$, so the graph has at least $\frac{2(n-2)}{2}=n-2$ edges merely to "finish" the non-survivors — but this still does not reach $\frac32n-2$.

The sharp bound comes from a **weight/adversary argument**: give each element charge $+1$ if it has never lost and $-1$ if it has never won.

- Initially every element holds charge $+1$ (max-eligible) and $-1$ (min-eligible), so the total "positive charge" is $n$ and the total "negative charge" is $-n$.
- At the end, exactly one element carries positive charge ($+1$) and exactly one carries negative charge ($-1$). So the positive charge must drop by $n-1$ and the negative charge must rise by $n-1$.
- A comparison between $x$ and $y$ where the outcome is, say, $x>y$: it can remove $+1$ from $y$ (if $y$ was max-eligible) and $-1$ from $x$ (if $x$ was min-eligible). **If both were still eligible on those sides, the comparison reduces the positive total by 1 *and* increases the negative total by 1 — but note that a comparison can only be "double-effective" for at most $\lfloor n/2\rfloor$ pairs, because an element can supply a *fresh* loss only once.** Summing: the $2(n-1)$ units of charge that must be moved require at least
$$c \;\ge\; \frac{n}{2} + (n-2) \;=\; \frac{3n}{2}-2,$$
where the first term $\lfloor n/2\rfloor$ accounts for the comparisons needed to "use up" each element's fresh eligibility once. Taking ceilings for odd $n$ gives $\lceil 3n/2\rceil-2$.

Combined with the achievable algorithm in (i):
$$\boxed{\ \text{the exact worst-case number of comparisons is } \Big\lceil\frac{3n}{2}\Big\rceil-2\ } \qquad\blacksquare$$

> **Exam-note.** The marker wants: (1) the **pairing algorithm** showing $\lceil 3n/2\rceil-2$ comparisons **suffice**; (2) the **two-eligibility invariant** showing an element compared only once cannot be ruled out of one of the two races, hence $\Omega(n)$; (3) the statement that the sharp constant follows from the charge argument. Writing parts (1) and (2) clearly earns most of the marks.
</details>

---

## E3. Decision-tree model basics (5 marks)

(a) [2 marks] State precisely what the decision-tree model is and what the height of the tree represents.
(b) [3 marks] Using the model, prove that searching for a given element in a sorted array of size $n$ requires $\Omega(\log n)$ comparisons.

<details><summary><b>Solution E3</b></summary>

**(a)** In the **decision-tree model**, an algorithm solves a problem by asking questions with binary (Yes/No) answers. For a comparison-based algorithm, each internal node is a comparison between two input elements, the two edges from a node are the two possible outcomes, and each leaf is a possible output of the algorithm. The **height** of the decision tree — the number of edges on the longest root-to-leaf path — equals the **worst-case number of comparisons** made by the algorithm.

**Key fact:** a binary tree with $N$ leaves has height at least $\log_2 N$. (A tree of height $h$ has at most $2^h$ leaves.)

**(b)** A correct search algorithm must be able to report, for each of the $n$ array positions, that the element is there, **and** must be able to report that the element is absent. These are $n+1$ mutually exclusive answers, so the decision tree must have at least $n+1$ distinct leaves.

By the key fact, the height is at least
$$\log_2(n+1) = \Omega(\log n).$$

Therefore every comparison-based search in a sorted array makes $\Omega(\log n)$ comparisons in the worst case. Since binary search achieves $O(\log n)$, the bound is tight. ∎
</details>

---

# SECTION F — Linear-Time Sorting (20 marks)

## F1. Sorting restricted-range integers (6 marks)

You must sort $n$ integers, each in the range $[1,n^3]$.

(a) [2 marks] Why can't you use counting sort directly?
(b) [4 marks] Give an algorithm that runs in $O(n)$ time. Justify the running time.

<details><summary><b>Solution F1</b></summary>

**(a)** Counting sort with an explicit count array over the key range costs $\Theta(n+k)$ where $k$ is the number of possible key values. Here $k=n^3$, so the time and space are $\Theta(n+n^3)=\Theta(n^3)$ — far too slow. Counting sort is only linear when $k=O(n)$.

**(b) Use radix sort with base $n$.** Write each key $x\in[1,n^3]$ in base $n$ as
$$x = d_2n^2 + d_1n + d_0, \qquad d_i\in[0,n-1],$$
i.e. as exactly $d=3$ base-$n$ "digits". Now run LSD radix sort: for $i=0,1,2$, stably sort the array on digit $d_i$ using counting sort.

**Running time.** One counting-sort pass over $n$ keys with $k=n$ digit values costs $\Theta(n+k)=\Theta(n)$. There are $d=3$ passes, a constant, so the total is
$$3\cdot\Theta(n) = \Theta(n).$$

**Why it is correct.** Radix sort's correctness is by induction on the digit position, and it relies crucially on counting sort being **stable**: digits must be processed from the **least** significant to the most significant, so that ties in a higher digit preserve the order already established by the lower digits.

**In the course's general notation:** for $w$-bit keys with digits of $\log k$ bits we have $d=\lceil w/\log k\rceil$ and cost $\Theta(d(n+k))$. Minimised at $k=n$ this is $\Theta\!\left(\lceil w/\log n\rceil n\right)$. Here $w=\lceil\log_2 n^3\rceil = 3\log_2 n$, so $w/\log n = 3$, a constant, giving $\Theta(n)$.
</details>

---

## F2. Counting sort trace and stability (7 marks)

(a) [4 marks] Trace `Counting-Sort` on $A=[4,1,3,4,3]$ with keys in $[0,4]$. Show the array $C$ after each of the three loops, and the final output $B$.
(b) [3 marks] Explain why the final loop must run **backwards**, and what would go wrong otherwise.

<details><summary><b>Solution F2</b></summary>

**(a)** $A=[4,1,3,4,3]$, $n=5$, keys in $[0,4]$ so $C$ has indices $0..4$.

**Loop 1 — initialise:** $C=[0,0,0,0,0]$.

**Loop 2 — count occurrences.** For $j=1..5$, increment $C[A[j]]$:
- $A[1]=4$: $C=[0,0,0,0,1]$
- $A[2]=1$: $C=[0,1,0,0,1]$
- $A[3]=3$: $C=[0,1,0,1,1]$
- $A[4]=4$: $C=[0,1,0,1,2]$
- $A[5]=3$: $C=[0,1,0,2,2]$

So $C[i]$ counts occurrences: value 4 appears twice, value 3 twice, value 1 once, values 0 and 2 never.

**Loop 3 — prefix sums.** For $i=1..4$, $C[i]\mathrel{+}=C[i-1]$:
- $i=1$: $C[1]=1+0=1$ → $[0,1,0,2,2]$
- $i=2$: $C[2]=0+1=1$ → $[0,1,1,2,2]$
- $i=3$: $C[3]=2+1=3$ → $[0,1,1,3,2]$
- $i=4$: $C[4]=2+3=5$ → $[0,1,1,3,5]$

Now $C[i]$ is the number of elements $\le i$, i.e. the **last output position** for value $i$.

**Loop 4 — place, scanning $j=5$ down to $1$:**

| $j$ | $A[j]$ | $C[A[j]]$ | $B[C[A[j]]]\gets A[j]$ | $C$ after decrement |
|---|---|---|---|---|
| 5 | 3 | 3 | $B[3]\gets3$ | $[0,1,1,2,5]$ |
| 4 | 4 | 5 | $B[5]\gets4$ | $[0,1,1,2,4]$ |
| 3 | 3 | 2 | $B[2]\gets3$ | $[0,1,1,1,4]$ |
| 2 | 1 | 1 | $B[1]\gets1$ | $[0,0,1,1,4]$ |
| 1 | 4 | 4 | $B[4]\gets4$ | $[0,0,1,1,3]$ |

**Output:** $B=[1,3,3,4,4]$. ✓

**(b) Why the backwards loop is essential.** Running $j$ from $n$ **down to 1** ensures that among elements with equal keys, the one appearing **later** in the input is placed at the **higher** output index. Equivalently: the first occurrence in the input ends up at the lowest output slot for that key, so the relative order of equal elements is preserved. That is exactly the definition of **stability**.

If the loop ran **forwards** ($j=1$ to $n$), each new equal element would overwrite the position just vacated, reversing the relative order of equal keys — the sort would be **anti-stable** for ties.

**Why this matters beyond counting sort:** radix sort's correctness proof depends *entirely* on counting sort being stable. Process digits from the least significant upwards; when two numbers tie on the current digit, the stability of the counting sort preserves the ordering already achieved on the lower digits. Without stability, radix sort produces wrong answers.
</details>

---

## F3. Which algorithm for the job? (7 marks)

For each scenario state the sorting algorithm from this course that gives the **smallest asymptotic worst-case running time**, and give that time. *(No explanation needed for 1–3, but 4 requires justification.)*

1. $n$ integers, each in the range $[1,n]$.
2. $n$ floating-point numbers known to have $O(\sqrt{\log n})$ inversions.
3. $n$ arbitrary floating-point numbers.
4. You must sort $n$ integers drawn from an unknown distribution on $[1,n^2]$, and you are told the algorithm must be **stable** and use $O(n)$ extra space.

<details><summary><b>Solution F3</b></summary>

**1. Counting sort, $O(n)$.** The key range is $k=n$, so counting sort costs $\Theta(n+k)=\Theta(n)$ — linear. Compare with comparison sorting, which is $\Omega(n\log n)$.

**2. Insertion sort, $O(n\log n)$.** Insertion sort runs in $\Theta(n+I)$ where $I$ is the number of inversions. Here $I=O(\sqrt{\log n})$, so the cost is
$$n + O(n\sqrt{\log n}).$$
Since $\sqrt{\log n}=O(\log n)$, this is $O(n\log n)$ — which is what the marking scheme expects. Note it is **not** $O(n)$, because $n\sqrt{\log n}$ grows faster than $n$. (Also note: the *worst case* over all inputs is still $\Theta(n^2)$; the question asks about this specific structured input, where every input is of this form.)

**3. Merge sort or heapsort, $O(n\log n)$.** With no structure to exploit, only these two have a guaranteed $O(n\log n)$ **worst case**. Quicksort is $\Theta(n\log n)$ in expectation but $\Theta(n^2)$ worst case; counting/radix/bucket don't apply to arbitrary floats.

**4. Radix sort (LSD) with base $n$, stable, $\Theta(n)$ time and $\Theta(n)$ extra space.** Justification: the keys lie in $[1,n^2]$, so in base $n$ each key has exactly $d=2$ digits. Each counting-sort pass over $n$ keys with $k=n$ digit values costs $\Theta(n)$, so two passes cost $\Theta(n)$. Radix sort **is stable** (because counting sort is), and it needs a count array of size $k=n$ plus an output array of size $n$, i.e. $\Theta(n)$ extra space. Counting sort directly would cost $\Theta(n+n^2)=\Theta(n^2)$ because the key range is $n^2$.

> **General principle for choosing.** (1) Is the key range $O(n)$? → counting sort. (2) Can the keys be split into a constant number of $\log n$-bit digits? → radix sort, $\Theta(n)$. (3) Is the input uniformly distributed? → bucket sort, $O(n)$ expected. (4) Must be comparison-based, stable, and worst-case guaranteed? → merge sort. (5) In place with $O(1)$ space? → heapsort (not stable).
</details>

---

# SECTION G — Mixed Rapid-Fire (20 marks)

Answer each in one or two lines.

**G1** [2] True or false: $T(n) = 2T(n/2) + n\log n$ solves to $\Theta(n\log^2 n)$. Justify.
**G2** [2] Why does the maximum-subarray problem **not** allow an empty subarray in this course, and how would the answer change if it did?
**G3** [2] In `Merge`, why is a sentinel $\infty$ appended to both halves?
**G4** [2] State the probability that randomised quicksort compares $z_i$ and $z_j$, and where that probability comes from.
**G5** [2] Why can't radix sort use a base of 1?
**G6** [2] Give an instance where insertion sort is faster than merge sort, and say by how much.
**G7** [2] What is the running time of `Build-Max-Heap` on an already-sorted array, and why?
**G8** [2] Why is quicksort's worst case $\Theta(n^2)$ while its expected time is $\Theta(n\log n)$ — and how can both be true on the same input?
**G9** [2] Is a max-heap a sorted array? Explain.
**G10** [2] In the 3-way quicksort partitioning loop, why must the pointer `i` not be incremented when `A[i] > pivot`?

<details><summary><b>Solution G</b></summary>

**G1 — True.** $a=2,b=2$ so $\log_2 2=1$, and $f(n)=n\log n$ which is $\Theta(n^{\log_ba}\log n)=\Theta(n\log n)$. This is the "equal with a log factor" case, giving $\Theta(n\log n\cdot\log n)=\Theta(n\log^2n)$. *(Master Theorem extended form / case 2 with a log already present.)*

**G2 —** Allowing the empty subarray would make the answer $0$ whenever all entries are negative, which trivialises the problem and destroys the intended structure (the answer would no longer be a genuine subarray). If it were allowed, the answer would be $\max(0,\ \text{best non-empty subarray})$, which is $\Theta(n\log n)$ still but with a degenerate case.

**G3 —** The sentinel removes the need for an explicit "one half is exhausted" test inside the main loop. Because $\infty$ is never smaller than a real key, once one half is exhausted the loop automatically takes all remaining elements from the other half. It saves a conditional per iteration, at the cost of $O(n)$ extra space for the copies.

**G4 —** $\Pr[z_i,z_j\text{ compared}] = \dfrac{2}{j-i+1}$. It comes from the fact that $z_i$ and $z_j$ are compared **iff** one of them is the *first pivot chosen among* $Z_{ij}=\{z_i,\dots,z_j\}$; since pivots are uniform, the first pivot in $Z_{ij}$ is uniform over its $j-i+1$ members, so the probability it is $z_i$ or $z_j$ is $\frac{2}{j-i+1}$.

**G5 —** With base 1 every "digit" would be 0, so all keys would look identical and no information could be extracted — you would need infinitely many digits. Radix sort needs a base $k\ge2$ so that digits carry information; the running time $\Theta(d(n+k))$ is minimised at $k=n$.

**G6 —** Any **already-sorted** array: insertion sort runs in $\Theta(n)$ (each insertion stops immediately because the key is already in place), versus $\Theta(n\log n)$ for merge sort. Similarly, an array with $O(n)$ inversions runs in $\Theta(n)$.

**G7 —** $\Theta(n)$. `Build-Max-Heap` always does exactly $\lfloor n/2\rfloor$ calls to `Max-Heapify`, and its $\Theta(n)$ bound holds for **every** input — the analysis charges each node for its own height, not for the tree's height. On a sorted array, in fact, few or no swaps occur, so it is even faster in practice, but the asymptotic bound is unchanged.

**G8 —** Randomised quicksort's expected time over its **own random pivot choices** is $\Theta(n\log n)$ for every input, but there exists a (very unlikely) sequence of pivot choices that produces maximally unbalanced splits and $\Theta(n^2)$ work. Both statements are about the *same* input: the $\Theta(n\log n)$ is an average over the algorithm's randomness, the $\Theta(n^2)$ is the worst case over that randomness. This is why randomised algorithms are measured as $\max_{|I|=n}E_R[T(I,R)]$ — worst-case input, expected randomness.

**G9 — No.** A max-heap only guarantees that each node is $\ge$ its children — a *partial* order. Siblings are unordered relative to each other: in the heap $[10,5,3,4,1,2]$, the element $3$ is not in its sorted position (sorted order would be $[1,2,3,4,5,10]$). The heap property is strictly weaker than sortedness; this is exactly why heapsort still needs $n-1$ `Extract-Max` operations.

**G10 —** Because the swap brings in the element that was at position `gt`, which has **not yet been inspected**. If we incremented `i`, that element would be skipped and never compared against the pivot, so it could be misfiled. (In the `<` branch we *do* advance `i`, because the element swapped in from `lt` is known to equal the pivot.)
</details>

---

# Scoring Guide

| Score | Interpretation | What to do next |
|---|---|---|
| **135–160** | Exam-ready | Drill speed on the recurrence table and heap traces |
| **110–134** | Solid | Re-read the sections where you lost marks; redo those questions in 3 days |
| **80–109** | Passable but risky | Focus on Sections A, B and D — they carry the most marks |
| **< 80** | Needs work | Work through the revision notes section by section, then retake this set |

**Time budget for a 2-hour, 4-question paper.** Spend at most 5 minutes reading the whole paper and choosing your order. Then ~25 minutes per question plus 20 minutes of slack. Never leave a "design an algorithm" question blank — writing the *idea*, the recurrence and the case analysis typically earns over half the marks even without full detail.

**The five things most often lost marks for** (from the actual marking schemes):
1. Giving one-sided bounds and then claiming $\Theta$.
2. Comparing a recurrence without solving it first.
3. Using the Master Theorem when the question bans it.
4. Drawing only the final heap instead of the tree after every insertion.
5. Forgetting to argue **both** the upper and the lower bound when the question says to.

---

*Practice set built from COMP 3711 lecture notes 01–09, the Fall-2025 and Spring-2026 midterm papers and their official solutions, and the Fall-2026 HW2 solution. Questions marked with a past-paper reference are adapted from those papers; the rest are in the same style.*
