# #327 - Redundant search for representative in disjoint sets [Closed]

> Username: mglisse  
> Created at: 2023-03-11 22:07:13 UTC  
> Updated at: 2023-09-12 03:30:52 UTC  
> Closed at: 2023-09-12 03:30:52 UTC  
> Url: https://github.com/boostorg/graph/issues/327  

We have a general function to join 2 sets  
https://github.com/boostorg/graph/blob/00028767d804fe3da20996afedf06f9eecfb5975/include/boost/pending/disjoint_sets.hpp#L78-L82  
This function finds the representative for each set, then calls the more specialized  
https://github.com/boostorg/graph/blob/00028767d804fe3da20996afedf06f9eecfb5975/include/boost/pending/disjoint_sets.hpp#L72-L76  
which requires that we pass the representative of each set. It then calls the worker  
https://github.com/boostorg/graph/blob/00028767d804fe3da20996afedf06f9eecfb5975/include/boost/pending/detail/disjoint_sets.hpp#L55-L59  
which starts by looking for the representative of each set... (if anything, this could be `assert(i == comp_rep(p, i));`)  
  
It isn't horrible, the search of the representative only takes one query in parent to check that it is indeed already good, it is probably already in cache and the branch predictor probably learns that this comparison is always true. Still, this defeats the purpose of having 2 different functions.  
  
Note that fixing this has the potential to break code for users who mistakenly used link instead of union_sets, and didn't notice since they are currently equivalent.
