# #339 - Fuzzy testing of index and value of regular axis [Open]

> Username: HDembinski  
> Created at: 2021-09-29 07:47:25 UTC  
> Updated at: 2021-09-29 07:47:25 UTC  
> Url: https://github.com/boostorg/histogram/issues/339  

While we cannot avoid round-off error in the `index` and `value` methods of the `regular` axis, we want these two to be exact inverses of each other. In other words, this should succeed for all values of `nbin`, `start`, `stop`.  
  
```cpp  
auto a = regular(nbin, start, stop);  
for (int i =0; i < nbin; ++i)  
   assert(a.index(a.value(i)) == i);  
```  
  
This needs to verified with fuzzy testing.
