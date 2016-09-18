<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-e6XsI7qqvAA-forest_sunbeam.jpg" -->
# CMPT231
## Lecture 2: ch4-5
### Divide and Conquer, Recurrences, Randomised Algorithms

---
<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-e6XsI7qqvAA-forest_sunbeam.jpg" -->
## Outline for today
+ **Divide and conquer** *(ch4)*
  + **Merge sort**, recursion tree
  + Proof by **induction**
  + Maximum **subarray**
  + Matrix multiply, **Strassen**'s method
  + **Master method** of solving recurrences
+ **Probabilistic Analysis** *(ch5)*
  + **Hiring** problem and analysis
  + **Randomised** algorithms and PRNGs

---
## Divide and conquer
+ Insertion sort was **incremental**:
  + At each step, *A[1 .. j-1]* has been sorted, so
  + Insert *A[j]* such that *A[1 .. j]* is now sorted
+ Divide and conquer **strategy**:
  + **Split** task up into smaller chunks
  + Small enough chunks can be solved **directly** (base case)
  + **Combine** results and return
+ Implement via **recursion** or **loops**
  + Usually, recursion is **easier** to code but **slower** to run

---
## Merge sort
<div class="imgbox"><div data-markdown>

+ **Split** array in half
  + If only 1 elt, we're done
+ **Recurse** to sort each half
+ **Merge** sorted sub-arrays
  + Need to do this efficiently

</div><div data-markdown>

```
def merge_sort(A, p, r):
  if p >= r: return
  q = floor( (p+r) / 2 )
  merge_sort(A, p, q)
  merge_sort(A, q+1, r)
  merge(A, p, q, r)
```

</div></div>

>>>
TODO: diagram?

---
## Efficient merge in &Theta;(n)
+ Assume subarrays are **sorted**:
  + *A[p .. q]* and *A[q+1 .. r]*, with  *p* &le; *q* &lt; *r*
+ Make temporary **copies** of each sub-array
  + Append an "*&infin;*" **marker** item to end of each copy
+ **Step** through the sub-arrays, using two indices *(i,j)*:
  + Copy **smaller** element into main array
    + and **advance** pointer in that sub-array
+ **Complexity**: *&Theta;(n)*

```
Main : [ A, C, D, E, H, J,  ,  ,  ,  ,  ,   ]
L: [ C, E, H, *K, P, R, inf ]  ||  R: [ A, D, J, *L, N, T, inf ]
```

---
## Merge step: in pseudocode
```
def merge(A, p, q, r):
  ( n1, n2 ) = ( q-p+1, r-q )                   # lengths
  new arrays: L[ 1 .. n1+1 ], R[ 1 .. n2+1 ]

  for i in 1 .. n1: L[ i ] = A[ p+i-1 ]         # copy
  for j in 1 .. n2: R[ j ] = A[ q+j ]
  ( L[ n1+1 ], R[ n2+1 ] ) = ( inf, inf )       # sentinel

  ( i, j ) = ( 1, 1 )
  for k in p .. r:
    if L[ i ] <= R[ j ]:                        # compare
      A[ k ] = L[ i ]                           # copy from left
      i++
    else:
      A[ k ] = R[ j ]                           # copy from right
      j++
```

---
## Complexity of merge sort
+ **Recurrence** relation: **base** case + **inductive** step
  + **Base** case: if *n = 1*, then T(n) = *&Theta;(1)*
  + **Inductive** step: if *n > 1*, then T(n) = *2T(n/2) + &Theta;(n)*
    + **Sort** 2 halves of size *n/2*, then **merge** in *&Theta;(n)*
+ How to **solve** this recurrence?
  + Function **call diagram** looks like binary tree
  + Each **level** `L` has \`2^L\` recursive calls
  + Each call performs \`2^-L\` work in the merge step

---
## Recurrence tree
+ Total work at each level is *&Theta;(n)*
  + Total number of levels is *lg(n)*
  + &rArr; Total complexity: *&Theta;(n lg(n))*
+ This is **not** a formal proof!
  + A **guess** that we can prove by **induction**

>>>
TODO: diagram

---
<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-e6XsI7qqvAA-forest_sunbeam.jpg" -->
## Outline for today
+ Divide and conquer *(ch4)*
  + Merge sort, recursion tree
  + **Proof by induction**
  + Maximum subarray
  + Matrix multiply, Strassen's method
  + Master method of solving recurrences
+ Probabilistic Analysis *(ch5)*
  + Hiring problem and analysis
  + Randomised algorithms and PRNGs

---
## Mathematical induction
+ **Deduction**: general *principles* &rArr; specific *case*
+ **Induction**: representative *case* &rArr; general *rule*
+ Proof by induction needs two **axioms**:
  + **Base case**: starting point, e.g., at *n = 1*
  + **Inductive step**: assuming the rule holds at *n*,
    + **prove** it also holds at *n+1*
    + (equivalently, assume true &forall; *m* &lt; *n*, and prove it for *n*)
+ This proves the rule for **all** (positive) *n*

>>>
BG: dominoes

---
## Example of inductive proof
+ Recall **Gauss**' formula: \`sum_(j=1)^n j = (n(n+1))/2\`
+ We can **prove** it by induction:
+ Prove **base case** (*n = 1*): 1 = *(1)(1+1)/2*
+ Prove **inductive step**:
  + **Assume**: \`sum_(j=1)^n j = (n(n+1))/2\`
  + **Prove**: \`sum_(j=1)^(n+1) j = ((n+1)(n+2))/2\`

---
## Inductive step of Gauss' formula
+ \` sum\_(j=1)^(n+1) j = (sum_(j=1)^n j) + (n+1) \`
+ \` = (n(n+1))/2 + (n+1) \` (by **inductive hypothesis**)
+ \` = (n^2 + n)/2 + (n+1) \`
+ \` = (n^2 + n + 2n + 2)/2 \`
+ \` = (n^2 + 3n + 2)/2 \`
+ \` = ((n+1)(n+2))/2 \`, **proving** the inductive step

---
## Inductive proof for merge sort
+ Apply same method of proof to **recurrences**:
+ **Merge sort**: T(n) = *2T(n/2) + &Theta;(n)*, T(1) &isin; *&Theta;(1)*
+ **Guess** (from recursion tree): T(n) &isin; *&Theta;(n lg n)*
+ Prove **base case**: T(1) &isin; *&Theta;(1 lg 1)* = &Theta;(1)
+ Prove **inductive step**:
  + **Assume**: T(m) &isin; *&Theta;(m lg m)*, for all *m &lt; n*
  + **Prove**: T(n) &isin; *&Theta;(n lg n)*
  + i.e., for big *n*, **find** *c1, c2* so that
  + \` c_1 n text(lg)n <= T(n) <= c_2 n text(lg)n \`

---
## Inductive step for merge sort
+ From the **recurrence**: T(n) = *2T(n/2) + &Theta;(n)*
  + i.e., &exist; *c3*, *c4*:
    \` 2T(n/2) + c_3 n <= T(n) <= 2T(n/2) + c_4 n \`
+ But *n/2* &lt; *n*, so we can apply the **inductive hypothesis**:
  + T(n/2) &isin; &Theta;( *(n/2) lg(n/2)* )
  + i.e., &exist; *c5*, *c6*:
    \` c_5 (n/2) text(lg)(n/2) <= T(n/2) <= c_6 (n/2) text(lg)(n/2) \`
+ \` => (c_5 n/2)(text(lg) n - text(lg) 2) <= T(n/2)
  <=    (c_6 n/2)(text(lg) n - text(lg) 2) \`
+ \` => (c_5/2)(n text(lg) n - n) <= T(n/2)
  <=    (c_6/2)(n text(lg) n - n) \`

---
## Inductive proof, cont.
+ **Substitute** into recurrence for T(n): &exist; *c3, c4, c5, c6*:
+ \` 2T(n/2) + c_3 n <= T(n) <= 2T(n/2) + c_4 n \`
+ \`   => c_5(n text(lg)n - n) + c_3 n <= T(n) \`
  \` <= c_6(n text(lg)n - n) + c_4 n \`
+ \`   => c_5 n text(lg)n + (c_3 - c_5)n <= T(n) \`
  \` <= c_6 n text(lg)n + (c_4 - c_6)n \`
+ We can't **choose** the constants, so we don't know that
  \`(c_3 - c_5)>0\` and \`(c_4-c_6)<0\`,
+ But we know that *n* &isin; *o(n lg n)*, so for big *n*,
  the *n lg n* terms **dominate** and we have T(n) &isin; *&Theta;(n lg n)*.

---
<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-e6XsI7qqvAA-forest_sunbeam.jpg" -->
## Outline for today
+ Divide and conquer *(ch4)*
  + Merge sort, recursion tree
  + Proof by induction
  + **Maximum subarray**
  + Matrix multiply, Strassen's method
  + Master method of solving recurrences
+ Probabilistic Analysis *(ch5)*
  + Hiring problem and analysis
  + Randomised algorithms and PRNGs

---
## Maximum subarray
+ **Input**: array *A[1 .. n]* of numbers (could be negative)
+ **Output**: indices *(i,j)* to maximise *sum( A[i .. j] )*
  + e.g., input daily change in **stock** price:
    + find optimal time to **buy** (*i*) and **sell** (*j*)
+ **Exhaustive** check of all (*i*,*j*): \`Theta(n^2)\`

![Example of max subarray](static/img/Fig-4-1-max_subarray.png)
<!-- .element: style="width: 75%" -->

---
## Max subarray: algorithm
+ **Split** array in half
+ **Recursively** solve each half
  + (what's the **base** case?)
+ Find the max subarray which **spans** the midpoint
  + Do this in *&Theta;(n)*
+ Choose **best** out of 3 options (*left*, *right*, *span*) and return

![Max subarray spanning midpoint](static/img/Fig-4-4-max_subarray.png)

---
## Span midpoint
+ Find the maximum subarray that **spans** the midpoint
+ **Decrement** *i* down from the **midpoint** to the **low** end
  + Maximise *sum( A[i .. mid] )*
+ **Increment** *j* up from `mid+1` to the **high** end
  + Maximise *sum( A[mid+1 .. j] )*
+ Total time is only **linear** in n

![Max subarray spanning midpoint](static/img/Fig-4-4-max_subarray.png)

---
## Max subarray: complexity

```
def max_subarray(A, low, mid, high):
  split_array()                         # O(1)
  max_subarray( left_half )             # T(n/2)
  max_subarray( right_half )            # T(n/2)
  midpt_max_subarray()                  # Theta(n)
  return best_of_3()                    # O(1)
```

+ **Recurrence**: T(n) = *2T(n/2) + &Theta;(n)*
  + **Base** case: T(1) = O(1)
+ Same as merge sort: **solution** is T(n) = *&Theta;(n lg n)*
+ Actually *(#4.1-5)*, max subarray can be done in *&Theta;(n)*!

---
## Programming Joke

+ There's always a way to **shorten** a program by one line.
  + But, there's also always one more **bug**.
  + By **induction**, any program can be shortened to a **single line**, which **doesn't work**.

---
<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-e6XsI7qqvAA-forest_sunbeam.jpg" -->
## Outline for today
+ Divide and conquer *(ch4)*
  + Merge sort, recursion tree
  + Proof by induction
  + Maximum subarray
  + **Matrix multiply, Strassen's method**
  + Master method of solving recurrences
+ Probabilistic Analysis *(ch5)*
  + Hiring problem and analysis
  + Randomised algorithms and PRNGs

---
## Matrix multiply
+ **Input**: two *n* x *n* matrices *A[i,j]* and *B[i,j]*
+ **Output**: *C* = *A* &lowast; *B*, where
  \` C[i,j] = sum_(k=1)^n A[i,k] B[k,j] \`
+ e.g., \` [[C_11, C_12], [C_21, C_22]] =
     [[A_11, A_12], [A_21, A_22]] &lowast; [[B_11, B_12], [B_21, B_22]] \`
+ **Simplest** method:

```
def mult(A, B, n):
  for i in 1 .. n:
    for j in 1 .. n:
      for k in 1 .. n:
        C[i, j] = A[i, k] * B[k, j]
  return C
```

**Complexity**?  Can we do **better**?

---
## Divide-and-conquer mat mul
+ **Split** matrices into **4** parts (assume *n* a power of 2)
+ **Recurse** *8* times to get products of sub-matrices
+ Add and **combine** info final result:
  \` [[C_11, C_12], [C_21, C_22]] =
     [[A_11, A_12], [A_21, A_22]] &lowast; [[B_11, B_12], [B_21, B_22]] \`
  + \` C_11 = A_11 &lowast; B_11 + A_12 &lowast; B_21 \`
  + \` C_12 = A_11 &lowast; B_12 + A_12 &lowast; B_22 \`, ...
+ What's the **base case**?
+ How to **generalise** to *n* not a power of 2?

---
## Complexity of divide-and-conquer
+ **Split**: O(1) by using **indices** rather than copying matrices
+ **Recursion**: *8* calls, each of time *T(n/2)*
+ **Combine**: each entry in *C* needs one add: \`Theta(n^2)\`
+ So the **recurrence** is: \`T(n) = 8T(n/2) + Theta(n^2)\`
  + Unfortunately, this resolves to \`Theta(n^3)\`
  + **No better** than the simple solution
+ What gets us is the *8* **recursive** calls
  + **Strassen**'s idea: save *1* recursive call
  + by spending more on **sums** (which are only \`Theta(n^2)\`)

---
## Strassen's matrix multiply
+ 10 **sums** of submatrices: \`S_1 = B_12 - B_22\`,
  \`S_2 = A_11 + A_12\`, \`S_3 = A_21 + A_22\`, \`S_4 = B_21 - B_11\`,
  \`S_5 = A_11 + A_22\`, \`S_6 = B_11 + B_22\`, \`S_7 = A_12 - A_22\`,
  \`S_8 = B_21 + B_22\`, \`S_9 = A_11 - A_21\`, \`S_10 = B_11 + B_12\`.
+ 7 **recursive** calls: \`P_1 = A_11 &lowast; S_1\`,
  \`P_2 = S_2 &lowast; B_22\`, \`P_3 = S_3 &lowast; B_11\`, \`P_4 = A_22 &lowast; S_4\`,
  \`P_5 = S_5 &lowast; S_6 \`, \`P_6 = S_7 &lowast; S_8 \`, \`P_7 = S_9 &lowast; S_10\`.
+ 4 **results** via addition: \`C_11 = P_5 + P_4 - P_2 + P_6\`,
  \`C_12 = P_1 + P_2\`, \`C_21 = P_3 + P_4\`, \`C_22 = P_5 + P_1 - P_3 - P_7\`.

---
## Complexity of Strassen's method
+ Even though more **sums** are done, still all \`Theta(n^2)\`
  + Doesn't change total **asymptotic** complexity
  + Might not be worth it for **small** *n*, though
+ **Recurrence**: \`T(n) = 7T(n/2) + Theta(n^2)\`
  + Saved us *1* recursive call!
  + **Solution**: \`T(n) = Theta(n^(text(lg) 7))\`
+ This is an example of the **master method**
  + For recurrences of **form** T(n) = *a T( n/b ) + &Theta;( f(n) )*
  + **Compare** *f(n)* with \`n^(log_b a)\`
  + Is more work done in **leaves** of tree or **roots**?

---
<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-e6XsI7qqvAA-forest_sunbeam.jpg" -->
## Outline for today
+ Divide and conquer *(ch4)*
  + Merge sort, recursion tree
  + Proof by induction
  + Maximum subarray
  + Matrix multiply, Strassen's method
  + **Master method of solving recurrences**
+ Probabilistic Analysis *(ch5)*
  + Hiring problem and analysis
  + Randomised algorithms and PRNGs

---
## Master method for recurrences
+ If T(n) has the **form**: *a T(n/b) + f(n)*, with *a, b* &gt; 0
  + **Merge sort**: a = *2*, b = *2*, f(n) = *&Theta;(n)*
+ Case *1*: if \` f(n) in Theta(n^(log_b a)) \`
  + Leaves/roots **balanced**: \` T(n) = Theta(n^(log_b a) log n) \`
+ Case *2*: if \` f(n) in O(n^(log_b a)) \`
  + **Leaves** dominate: \` T(n) = Theta(n^(log_b a)) \`
+ Case *3*: if \` f(n) in Omega(n^(log_b a + epsilon)) \`,
  for some *&epsilon;* > 0,
  **and** if \` a f(n/b) <= c f(n) \` for some *c* &lt; 1 and big *n*
  + **Roots** dominate: \` T(n) = Theta(f(n)) \`
  + Polynomials \` f(n) = n^k \` satisfy the **regularity** condition

---
## Master method: merge sort
+ **Recurrence**: T(n) = *2T(n/2) + &Theta;(n)*:
  + a = *2*, b = *2*, f(n) = *&Theta;(n)*
+ \` f(n) = Theta(n) = Theta(n^(log_2 2)) \`
  + So leaves and roots are **balanced** (case 1)
+ **Solution** is \` T(n) = Theta(n^(log_2 2) log n) = Theta(n log n) \`

---
## Master method: Strassen
+ **Recurrence**: T(n) = *7T(n/2) + &Theta;(n^2)*
  + a = *7*, b = *2*, f(n) = *&Theta;(n^2)*
+ \` f(n) = Theta(n^2) = O(n^(log_2 7 - epsilon)) \`
  + *lg 7* &simeq; 2.8, so, e.g., *&epsilon;* = 0.4 works
  + So **leaves** dominate (case 2)
+ **Solution** is \` T(n) = Theta(n^(log_2 7)) ~~ Theta(n^2.8) \`

---
## Gaps in master method
+ **Doesn't** cover all recurrences of form *a T(n/b) + f(n)*!
  + e.g., T(n) = *2T(n/2) + n log n*
  + **Case 1**: \` n log n notin Theta(n^(log_2 2)) = Theta(n) \`
  + **Case 2**: \` n log n notin O(n^(1-epsilon)) \`,
    for **any** *&epsilon;* > 0
  + **Case 3**: \` n log n notin Omega(n^(1+epsilon)) \`,
    for **any** *&epsilon;* > 0
    + because \` log n notin Omega(n^epsilon) \`
      &forall; *&epsilon;* > 0
+ Need to use **other** methods to solve
  + Some recurrences are just **intractable**

---
## Polylog extension
+ **Generalisation** of master method
+ Applies for \` f(n) in Theta(n^(log_b(a)) log^k(n)) \`
  + (*log* to *k* power, not iterated log)
+ **Solution**: \` T(n) = Theta(n^(log_b(a)) log^(k+1)(n)) \`
  + **Regular** master method is special case, *k = 0*
+ Previous **example**: T(n) = *2T(n/2) + n log n*
  + **Solution**: \` T(n) = Theta(n log^2 n) \`

---
<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-e6XsI7qqvAA-forest_sunbeam.jpg" -->
## Outline for today
+ Divide and conquer *(ch4)*
  + Merge sort, recursion tree
  + Proof by induction
  + Maximum subarray
  + Matrix multiply, Strassen's method
  + Master method of solving recurrences
+ **Probabilistic Analysis** *(ch5)*
  + **Hiring problem and analysis**
  + **Randomised algorithms and PRNGs**

---
## Probabilistic analysis
+ Running time of **insertion sort** depended on input
  + Best-case vs worst-case vs **average**-case
+ **Random variable** *X*: takes values within a domain
  + **Domain** *&Omega;* could be `[0,1]`, \`bbb R\` = (-&infin;, &infin;), \`bbb R^n\`,
    `(A, A-, B+, ...)`, `{blue, red, black}`, etc.
+ **Distribution** *P(X)*: says which values are more likely
  + **Uniform**: all values equally likely
  + **Normal** (Gaussian) "bell curve" *N(&mu;, &sigma;)*
+ **Expected value** *E(X)*: weighted average
  + \` E(X) = int\_(X in Omega) P(X) = sum_(X in Omega) P(X) \`

---
## Example: hiring problem
+ **Input**: list of candidates with *suitability* \`{s\_i}_(i=1)^k\`
  + cost per *interview*: \`c_i\`.  cost per *hire*: \`c_h > c_i\`
+ **Output**: list of hiring *decisions* \`{X_i} in {0,1}^n\`
  + **Constraint**: at any point, *best* candidate so far is hired
  + **Goal**: minimise total *cost* of interviews + hires
+ Total **cost** is: \`c\_i n + c\_h sum\_(i=1)^n X_i\`
  + *Interview* cost is **fixed**, so focus on *hiring* cost
+ **Worst** case: every new candidate is hired: \`X_i = 1 forall i\`
  + (What kind of *suitabilities* \`{s_i}\` would cause this?)
  + **Hiring cost** is \`c_h n\`

---
## Analysis of hiring problem
+ **Assume** order of candidates is *random*
  + each of *n!* possible **permutations** is equally likely
+ For each candidate *i*, find probability of being **hired**:
  + Most **suitable** candidate seen so far
  + \`s\_i\` needs to be **max** of \`{s\_k}_(k=1)^i\`
  + if order is **random**, likelihood is *1/i*
  + So \`P(X_i) = 1/i\`
+ Now we can derive the **expected hiring cost**

---
## Expected hiring cost
+ \` E[ c\_h sum\_(i=1)^n X\_i ] = c\_h sum\_(i=1)^n E[X\_i] \` (by **linearity** of E)
+ \` = c\_h sum\_(i=1)^n P(X\_i)\` (since \`X_i\` is an **indicator**)
+ \` = c\_h sum\_(i=1)^n 1/i\` (random **order**, see prev slide)
+ \` = c_h (ln n + O(1))\` (solution to **harmonic series**)
+ &rArr; much better than **worst-case**: \`c_h n\`

---
## Randomised algorithms
+ Above analysis assumed input order was **random**
  + But we **can't** always assume that!
+ So **inject** randomness into the problem:
  + **Shuffle** input before running algorithm
+ Use a **pseudo-random** number generator *(PRNG)*
  + Typically, returns a **float** in range `[0,1)`
  + Sequence is reproducible by setting **seed**
+ Or **hardware** RNG module (on motherboard, USB, etc.)
  + shot noise, Zener diode noise, beam splitters, etc.

---
## Fisher-Yates shuffle
+ Idea by **Fisher + Yates** (1938)
  + Implementation via swaps by **Durstenfeld** (1964)
+ Randomly **permute** input *A[]* in-place, in *O(n)* time

```
def shuffle(A, n):
  for i in 1 to n:
    swap( A[ i ], A[ random( i+1, n ) ] )
```

+ Use **PRNG** `random(a,b)`: int between *a* and *b*
+ **Correctness** can be proved via *loop invariant*:
  + After *i*-th iteration, each possible permutation of length *i*
    is in the subarray *A[ 1 .. i ]* with probability *(n-i)!/n!*

---
<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-e6XsI7qqvAA-forest_sunbeam.jpg" -->
## Outline for today
+ **Divide and conquer** *(ch4)*
  + **Merge sort**, recursion tree
  + Proof by **induction**
  + Maximum **subarray**
  + Matrix multiply, **Strassen**'s method
  + **Master method** of solving recurrences
+ **Probabilistic Analysis** *(ch5)*
  + **Hiring** problem and analysis
  + **Randomised** algorithms and PRNGs

---
<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-e6XsI7qqvAA-forest_sunbeam.jpg" class="empty" -->