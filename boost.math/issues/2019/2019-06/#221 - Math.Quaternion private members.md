# #221 - Math.Quaternion private members [Closed]

> Username: CaeruleusAqua  
> Created at: 2019-06-26 15:32:39 UTC  
> Updated at: 2019-09-15 18:39:26 UTC  
> Closed at: 2019-09-15 18:39:26 UTC  
> Url: https://github.com/boostorg/math/issues/221  

Is it really necessary that the members of Quaternion are private?  
This will break all libraries that inherit from math.quaternion!  
Just switching from private to protected solves the problem for me (with testet math).  
  
This was changed in commit 68b82322ab2ea972f14122ea7a039011d12f1723.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-09-15 18:39:26 UTC  
> Url: https://github.com/boostorg/math/issues/221#issuecomment-531589035  

I think I'm closing this as "won't fix" as inheritance results in the operators slicing the class.  The public interfaces should really provide what you need in any case?
