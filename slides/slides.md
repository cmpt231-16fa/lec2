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
Left: [ C, E, H, *K, P, R, inf ]   Right: [ A, D, J, *L, N, T, inf ]
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
    if L[i] &le; R[j]:                          // compare
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
  + **Base** case: if *n=1*, then T(n) = *&Theta;(1)*
  + **Inductive** step: if *n>1*, then T(n) = *2T(n/2) + &Theta;(n)*
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
## Maximum subarray
+ **Input**: array *A[1 .. n]* of numbers (could be negative)
+ **Output**: indices *(i,j)* to maximise *sum( A[i .. j] )*
  + e.g., input daily change in **stock** price:
    + find optimal time to **buy** (*i*) and **sell** (*j*)
+ **Exhaustive** check of all (*i*,*j*): \`Theta(n^2)\`

![Example of max subarray](static/img/Fig-4-1-max_subarray.png)
<!-- .element: style="width: 90%" -->

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
+ Actually, *(#4.1-5)*, max subarray can be done in *&Theta;(n)*!

