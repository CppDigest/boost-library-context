# #323 - Adjacency Matrix: doc error for undirected graphs [Open]

> Username: tesfabpel  
> Created at: 2023-01-19 08:48:36 UTC  
> Updated at: 2023-01-30 18:15:40 UTC  
> Url: https://github.com/boostorg/graph/issues/323  

In the doc page for adjacency matrices there is an error for undirected graphs.  
It says:   
> In the case of an undirected graph, the adjacency_matrix. class does not use a full V x V matrix but instead uses a lower triangle (the diagonal and below) since the matrix for an undirected graph is symmetric. This reduces the storage to (V²)/2. [Figure 2](https://www.boost.org/doc/libs/1_81_0/libs/graph/doc/adjacency_matrix.html#fig:undir-adj-matrix-graph) shows an adjacency matrix representation of an undirected graph.  
  
In particular, `This reduces the storage to (V²)/2` is wrong, since if you count the number of items in the Figure 2, you can see that there are 21 items for a graph of 6 vertices (instead of `(6²)/2 = 36 / 2 = 18`).  
  
I believe the correct formula is `(V² + V) / 2`.  
  
For n = 1: `(1² + 1) / 2 = 2 / 2 = 1`  
For n = 2: `(2² + 2) / 2 = 6 / 2 = 3`  
For n = 6: `(6² + 6) / 2 = 42 / 2 = 21`  
  
![boost-graph-doc-matrix-bug](https://user-images.githubusercontent.com/1371976/213394693-68887690-5bbc-4b4a-bc69-e6f0a6785e67.png)

---

## Comment 1

> Username: tesfabpel  
> Created at: 2023-01-19 08:56:45 UTC  
> Url: https://github.com/boostorg/graph/issues/323#issuecomment-1396637950  

I've also created a small python script to test for the idea and it prints OK:  
  
```  
def count_by_formula(n):  
    # if n is odd, `n*n + n` should be   
    # guaranteed to be even since  
    # `n*n` should return again an odd number  
    # and an odd + an odd makes an even  
    # number. So dividing it by 2  
    # produces an integer.  
      
    return int((n*n + n) / 2)  
  
def count_by_summing_rows(n):  
    s = 0  
      
    for i in range(n):  
        s = s + i + 1  
          
    return s  
  
for n in range(100):  
    test = count_by_formula(n)  
    expected = count_by_summing_rows(n)  
      
    print(n, ':', test, expected)  
      
    if test != expected:  
        print('Error')  
        raise n  
  
print('OK')  
```  
  
```  
~$ python boost-graph-doc-matrix-bug.py  
0 : 0 0  
1 : 1 1  
2 : 3 3  
3 : 6 6  
4 : 10 10  
5 : 15 15  
6 : 21 21  
7 : 28 28  
8 : 36 36  
9 : 45 45  
10 : 55 55  
[...]  
OK  
```

---

## Comment 2

> Username: valiko-ua  
> Created at: 2023-01-30 16:06:09 UTC  
> Url: https://github.com/boostorg/graph/issues/323#issuecomment-1408888517  

You are correct if we are talking about exact numbers, but when analyzing an algorithm complexity and memory requirements only the biggest term is of interest. Look for Big O notation for more info.

---

## Comment 3

> Username: tesfabpel  
> Created at: 2023-01-30 16:59:57 UTC  
> Url: https://github.com/boostorg/graph/issues/323#issuecomment-1408984585  

Ah I see, I thought it was an exact number and not Big-O notation and it wasn't explicitly specified (it also says `For a graph with V vertices, a V x V matrix is used` in the paragraph above so I thought there was a mistake).   
  
Also, doesn't `O(n^2/2)` simplify into `O(n^2)` in Big-O (since the `/2` part is a constant)?  
  
Anyway, thank you! If the documentation is written as intended, this issue can be closed according to me.

---

## Comment 4

> Username: valiko-ua  
> Created at: 2023-01-30 18:15:40 UTC  
> Url: https://github.com/boostorg/graph/issues/323#issuecomment-1409095432  

> Also, doesn't `O(n^2/2)` simplify into `O(n^2)` in Big-O (since the `/2` part is a constant)?  
  
You are correct here too. Maybe due to this reason the author didn't use Big-O.  
I'm not the author of those pages, just express my opinion.
