# #192 - Add more complete combinators [Open]

> Username: pfultz2  
> Created at: 2017-09-17 17:41:54 UTC  
> Updated at: 2017-09-17 17:41:54 UTC  
> Url: https://github.com/boostorg/hof/issues/192  

Add some more adaptors for more complete combinatory logic:  
  
* B = compose  
* C = flip  
* K = always  
* W = ???  
* S = ???  
* I = identity  
  
So the S(ie application combinator) and the W(ie duplicate combinator) combinators are missing, which could be something like:  
  
```  
S(f, g)(xs...) == f(xs...)(g(xs...))  
W(f)(xs...) == f(xs...)(xs...)  
```  
  
Also, even though the C combinator is like flip, it could also be defined as:  
  
```  
C(f)(xs...)(ys...) == f(ys...)(xs...)  
```  
  
There is also the monad bind that can be defined for functions, something like:  
  
```  
mbind(f, g)(xs...) == g(f(xs...))(xs...)  
```
