# #145 - Bad is_sorted docs [Open]

> Username: BenFrantzDale  
> Created at: 2023-04-21 18:27:22 UTC  
> Updated at: 2023-04-21 18:30:51 UTC  
> Url: https://github.com/boostorg/range/issues/145  

In https://github.com/boostorg/range/blob/develop/doc/reference/algorithm_ext/is_sorted.qbk:  
```  
For the non-predicate version the return value is `true` if and only if for  
each adjacent elements `[x,y]` the expression `x < y` is `true`.  
For the predicate version the return value is `true` is and only if for each  
adjacent elements `[x,y]` the expression `pred(x,y)` is `true`.  
```  
note that's describing a strict ordering, which would treat `0, 0` as not sorted!  
  
I think it should read  
```  
For the non-predicate version the return value is `true` if and only if for  
each adjacent elements `[x, y]` the expression `y < x` is `false` (i.e., `x <= y`), or if the number of elements is zero or one.  
For the predicate version the return value is `true` is and only if for each  
adjacent elements `[x, y]` the expression `pred(y, x)` is `false`, or if the number of elements is zero or one.  
```
