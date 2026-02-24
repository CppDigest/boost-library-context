# #59 - [Pair] Provide a function to modify one component of a pair? [Closed]

> Username: ldionne  
> Created at: 2015-05-04 16:30:11 UTC  
> Updated at: 2015-09-07 20:38:01 UTC  
> Closed at: 2015-09-07 20:38:01 UTC  
> Url: https://github.com/boostorg/hana/issues/59  

It is sometimes useful to modify a single component of a pair by applying a function to it. However, writing   
  
``` cpp  
auto p = make_pair(...);  
auto new_p = make_pair(function(first(p)), second(p));  
```  
  
is redundant and it seems like there should be a generalization. Actually, this could be generalized to arbitrary sequences; mapping `f` over the `n`th element only.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-09-07 20:38:01 UTC  
> Url: https://github.com/boostorg/hana/issues/59#issuecomment-138374569  

Closing because too focused. I think this came up because we needed this in the implementation of some algorithms. However, we now implement algorithms using constexpr expansion most of the time, and this is not required anymore. I don't think this adds enough value to justify adding yet another function.
