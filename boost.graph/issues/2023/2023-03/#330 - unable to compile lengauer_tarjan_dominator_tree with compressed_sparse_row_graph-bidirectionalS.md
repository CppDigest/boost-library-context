# #330 - unable to compile lengauer_tarjan_dominator_tree with compressed_sparse_row_graph/bidirectionalS [Closed]

> Username: marakew  
> Created at: 2023-03-16 10:27:22 UTC  
> Updated at: 2023-03-24 03:54:42 UTC  
> Closed at: 2023-03-23 12:02:09 UTC  
> Url: https://github.com/boostorg/graph/issues/330  

the issue at concept restriction  
  
https://github.com/boostorg/graph/blob/00028767d804fe3da20996afedf06f9eecfb5975/include/boost/graph/graph_concepts.hpp#L134  
https://github.com/boostorg/graph/blob/00028767d804fe3da20996afedf06f9eecfb5975/include/boost/graph/graph_concepts.hpp#L142  
  
i just remove it, and all compiled ok  
so what wrong ?  
i cant find any implementation "degree" for "compressed_sparse_row_graph"

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2023-03-17 03:32:57 UTC  
> Url: https://github.com/boostorg/graph/issues/330#issuecomment-1473073376  

Ahh... I really don't know. I think you need to provide a lot more information about your build toolchain and environment, etc.

---

## Comment 2

> Username: marakew  
> Created at: 2023-03-18 11:18:17 UTC  
> Url: https://github.com/boostorg/graph/issues/330#issuecomment-1474817271  

example  
https://wandbox.org/permlink/7GQe1hlL1GR7SFgc  
as you can see concept restriction  
but dergee doest use at eny where

---

## Comment 3

> Username: jeremy-murphy  
> Created at: 2023-03-21 23:54:11 UTC  
> Url: https://github.com/boostorg/graph/issues/330#issuecomment-1478738814  

So, there is a bidirectional specialization of CSRG, and it has `in_degree` and `out_degree` member functions, which I believe are all that is required to compute `degree(v, g)`, but maybe the free `degree` function definition is not included automatically?  
  
Could you find where `degree` is defined and include that header file first, see if it fixes the compilation error?

---

## Comment 4

> Username: marakew  
> Created at: 2023-03-22 08:06:18 UTC  
> Url: https://github.com/boostorg/graph/issues/330#issuecomment-1479072826  

function degree not exist for compressed_sparse_row_graph

---

## Comment 5

> Username: jeremy-murphy  
> Created at: 2023-03-23 04:53:42 UTC  
> Url: https://github.com/boostorg/graph/issues/330#issuecomment-1480599958  

Could you dig a little deeper, please? There is a `boost::graph::degree(v, g)` function somewhere, the question is why doesn't it generically support CSRG?

---

## Comment 6

> Username: jeremy-murphy  
> Created at: 2023-03-24 03:54:41 UTC  
> Url: https://github.com/boostorg/graph/issues/330#issuecomment-1482212232  

What was the solution?
