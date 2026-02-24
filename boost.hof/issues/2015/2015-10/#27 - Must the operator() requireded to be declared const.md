# #27 - Must the operator() requireded to be declared const? [Closed]

> Username: viboes  
> Created at: 2015-10-04 00:43:57 UTC  
> Updated at: 2015-12-17 20:34:50 UTC  
> Closed at: 2015-12-17 20:34:50 UTC  
> Url: https://github.com/boostorg/hof/issues/27  



---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 04:17:02 UTC  
> Url: https://github.com/boostorg/hof/issues/27#issuecomment-145426635  

Yes, but the mutable adaptor can be used for functions that require mutations.

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-05 22:48:59 UTC  
> Url: https://github.com/boostorg/hof/issues/27#issuecomment-145690860  

But you say that the `mutable` should not be used

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-05 23:23:41 UTC  
> Url: https://github.com/boostorg/hof/issues/27#issuecomment-145696797  

In general, mutable function objects(ie with non-const call operator) are a bad idea, and should be avoided. However, if you really feel you need a mutable function object(perhaps you are given a function object from another library that didn't put `const`) then the mutable adaptor can be used.
