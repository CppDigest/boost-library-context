# #59 - oveload sets are functions having hetegeneous domain and co-domain [Closed]

> Username: viboes  
> Created at: 2015-10-04 07:40:34 UTC  
> Updated at: 2015-10-31 00:29:18 UTC  
> Closed at: 2015-10-30 23:07:19 UTC  
> Url: https://github.com/boostorg/hof/issues/59  

It is not clear from the documentation that your library can consider an oveload set as a function  
and that `match` and `conditional` are overload set factories and that their result can be considered as functions arguments for the other functions. I believe that this is very important.   
  
I suggest to use signatures to describe oveloads sets as e.g.   
  
```  
(T1->U1 T2->U2)  
```  
  
could represent an overloaded set that can transform a `T1` on a `U1` and a `T2` on a `U2`.  
  
The signature of `by` could be for the binary case  
  
```  
by :: ( (T1->U1 T2->U2)  (U1 U2 -> V) ) -> ((T1  T2) -> V)   
```  
  
The documentation must state this clearly.

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 06:39:41 UTC  
> Url: https://github.com/boostorg/hof/issues/59#issuecomment-145443557  

I dont understand this at all. Please elaborate.

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-31 00:29:18 UTC  
> Url: https://github.com/boostorg/hof/issues/59#issuecomment-152679627  

Don't worry, we are not on the same wavelength. I was just suggesting that the interface must be more explicit.
