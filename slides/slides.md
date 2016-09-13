<!-- .slide: data-background-image="http://sermons.seanho.com/img/bg/unsplash-NEgEJmN3JZo-boardwalk_grass.jpg" -->
# CMPT231
## Lecture 2: ch4-5
### Divide and Conquer, Recurrences, Randomised Algorithms

---
## Outline for today
+ **Divide and conquer** *(ch4)*
  + Merge sort, recurrence relations
  + Maximum subarray
  + Matrix multiply, Strassen's method
  + Master method of solving recurrences
+ **Randomised Algorithms** *(ch5)*

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

```
Main : [ A, C, D, E, H, J,  ,  ,  ,  ,  ,   ]
L: [ C, E, H, *K, P, R, inf ]  ||  R: [ A, D, J, *L, N, T, inf ]
```

---
## Merge step: in pseudocode
```
def merge(A, p, q, r):
  (n1, n2) = (q-p+1, r-q)                       // lengths
  new arrays: L[ 1 .. n1+1 ], R[ 1 .. n2+ 1]

  for i in 1 .. n1: L[i] = A[ p+i-1 ]           // copy
  for j in 1 .. n2: R[j] = A[ q+j ]
  (L[ n1+1 ], R[ n2+1 ]) = (inf; inf)           // sentinel

  (i, j) = (1, 1)
  for k in p .. r:
    if L[i] <= R[j]:                          // compare
      A[k] = L[i]                               // copy from left
      i++
    else:
      A[k] = R[j]                               // copy from right
      j++
```

**Complexity**: *&Theta;(n)*

---
## Complexity of merge sort
+ **Recurrence** relation: **base** case + **inductive** step
  + **Base** case: if *n = 1*, then T(n) = *&Theta;(1)*
  + **Inductive** step: if *n > 1*, then T(n) = *2T(n/2) + &Theta;(n)*
    + **Sort** 2 sub-arrays of size *n/2*, then **merge**
+ How to **solve** this recurrence?
  + Function **call diagram** looks like binary tree
  + Each **level** `L` has \`2^L\` calls, each doing \`2^-L\` work
    + Total work at each level is *&Theta;(n)*
  + Total number of levels is *lg(n)*
  + &rArr; Total complexity: *&Theta;(n lg(n))*

>>>
TODO: diagram?

---
## Outline

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
  split_array()                         // O(1)
  max_subarray( left_half )             // T(n/2)
  max_subarray( right_half )            // T(n/2)
  midpt_max_subarray()                  // Theta(n)
  return best_of_3()                    // O(1)
```

+ **Recurrence**: T(n) = *2T(n/2) + &Theta;(n)*
  + **Base** case: T(1) = O(1)
+ Same as merge sort: **solution** is T(n) = *&Theta;(n lg n)*
+ Actually *(#4.1-5)*, max subarray can be done in *&Theta;(n)*!

---
## Outline

---
## Matrix multiply
+ **Input**: two *n* x *n* matrices *A[i,j]* and *B[i,j]*
+ **Output**: *n* x *n* matrix *C* = *A \* B*:
  \` C[i,j] = sum_(k=1)^n A[i,k] B[k,j] \`
  \` [[C_11, C_12], [C_21, C_22]] =
     [[A_11, A_12], [A_21, A_22]] * [[B_11, B_12], [B_21, B_22]] \`
+ **Simplest** method:

```
def mult(A, B, n):
  for i in 1 .. n:
    for j in 1 .. n:
      for k in 1 .. n:
        C[i, j] = A[i, k] * B[k, j]
  return C
```

+ **Complexity**?  Can we do **better**?

---
## Divide-and-conquer mat mul
+ **Split** matrices into **4** parts (assume *n* a power of 2)
+ **Recurse** *8* times to get products of sub-matrices
+ Add and **combine** info final result:
  \` [[C_11, C_12], [C_21, C_22]] =
     [[A_11, A_12], [A_21, A_22]] * [[B_11, B_12], [B_21, B_22]] \`
  \` C_11 = A_11 * B_11 + A_12 * B_21 \`
  \` C_12 = A_11 * B_12 + A_12 * B_22 \`, etc.
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
  + **Strassen**'s idea: spend \`o(n^2)\` work to save *1* call

---
## Strassen's matrix multiply
+ 10 **sums** of submatrices: \`S_1 = B_12 - B_22\`,
  \`S_2 = A_11 + A_12\`, \`S_3 = A_21 + A_22\`, \`S_4 = B_21 - B_11\`,
  \`S_5 = A_11 + A_22\`, \`S_6 = B_11 + B_22\`, \`S_7 = A_12 - A_22\`,
  \`S_8 = B_21 + B_22\`, \`S_9 = A_11 - A_21\`, \`S_10 = B_11 + B_12\`.
+ 7 **recursive** calls: \`P_1 = A_11 * S_1\`,
  \`P_2 = S_2 * B_22\`, \`P_3 = S_3 * B_11\`, \`P_4 = A_22 * S_4\`,
  \`P_5 = S_5 * S_6 \`, \`P_6 = S_7 * S_8 \`, \`P_7 = S_9 * S_10\`.
+ 4 **results** via addition: \`C_11 = P_5 + P_4 - P_2 + P_6\`,
  \`C_12 = P_1 + P_2\`, \`C_21 = P_3 + P_4\`, \`C_22 = P_5 + P_1 - P_3 - P_7\`.

---
## Complexity of Strassen's method
+ Even though more **sums** are done, still all \`Theta(n^2)\`
  + Doesn't change total **asymptotic** complexity
  + Might not be worth it for **small* *n*, though
+ **Recurrence**: \`T(n) = 7T(n/2) + Theta(n^2)\`
  + Saved us *1* recursive call!
  + **Solution**: \`T(n) = Theta(n^(text(lg) 7))\`
+ In **general**: when T(n) = *a T( n/b ) + &Theta;( f(n) )*
  + If *f(n)* is **smaller** than \`O(n^(log_b(a)))\`
    + **Leaves** dominate recursion tree
    + **Solution** is T(n) = \`Theta(n^(log_b(a)))\`
  + One case of the "**master theorem**"

---
## Outline

