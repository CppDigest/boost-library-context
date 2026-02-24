# #232 - Todo: Jacobi Polynomials [Closed]

> Username: NAThompson  
> Created at: 2019-08-02 17:38:54 UTC  
> Updated at: 2019-09-01 19:28:01 UTC  
> Closed at: 2019-09-01 19:28:01 UTC  
> Url: https://github.com/boostorg/math/issues/232  

I have a use for the [Jacobi polynomials](https://en.wikipedia.org/wiki/Jacobi_polynomials). It looks like they have a pretty rich set of properties which make them useful in many contexts, the derivatives are "free", in the code sense, and they have a recurrence relation which I suspect has fast and stable evaluation.  
  
(This is on my TODO list . . . hopefully it's not too hard to implement.)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-08-02 20:39:01 UTC  
> Url: https://github.com/boostorg/math/issues/232#issuecomment-517837331  

If you're thinking of implementing via the recurrence relations, then it's almost a 1 line job via the tools here: https://github.com/boostorg/math/blob/hypergeometric_soc_2014/doc/internals/recurrence.qbk  (and yes there are some typos still!!)

---

## Comment 2

> Username: NAThompson  
> Created at: 2019-08-02 21:35:33 UTC  
> Url: https://github.com/boostorg/math/issues/232#issuecomment-517851164  

Yeah, I was wondering if you'd developed something like this for the hypergeometrics. I think these are a special case of the hypergeometrics IIRC.
