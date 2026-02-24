# #60 - [Functional] Should `flip` reverse all its arguments? [Closed]

> Username: ldionne  
> Created at: 2015-05-04 16:33:36 UTC  
> Updated at: 2015-12-02 22:01:28 UTC  
> Closed at: 2015-12-02 22:01:28 UTC  
> Url: https://github.com/boostorg/hana/issues/60  

Right now,   
  
``` cpp  
flip(f)(x1, x2, x3, ..., xn) == f(x2, x1, x3, ..., xn)  
```  
  
Instead, `flip` could act like  
  
``` cpp  
flip(f)(x1, x2, x3, ..., xn) == f(xn, ..., x3, x2, x1)  
```  
  
For the 2-argument case, both would be equivalent. However, this would give us a functional version of the  `reverse` algorithm for parameter packs. To know whether this is the way to go, we need more use cases of `flip`.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-12-02 22:01:28 UTC  
> Url: https://github.com/boostorg/hana/issues/60#issuecomment-161447412  

This is out of scope. I want to reduce the prominence of the Functional module, which should be handled by a different library (and is already, see [Fit](https://github.com/pfultz2/fit)).
