# #805 Avoid overflow in intermediate bessel_ik computation [Merged]

> Username: mckib2  
> Created at: 2022-07-19 01:46:08 UTC  
> Updated at: 2022-07-20 18:41:25 UTC  
> Merged at: 2022-07-20 16:38:52 UTC  
> Closed at: 2022-07-20 16:38:52 UTC  
> Url: https://github.com/boostorg/math/pull/805  

Closes #804 by adding a check to see if  an overflow is possible in bessel_ik

---

## Comment 1

> Username: mckib2  
> Created_at: 2022-07-19 01:47:46 UTC  
> Updated_at: 2022-07-19 02:01:45 UTC  
> Url: https://github.com/boostorg/math/pull/805#issuecomment-1188505391  

@jzmaddock Thought I'd try my hand at a fix after seeing your fix for #799  
  
__EDIT:__ I squinted at it after pushing and found some issues.  I feel better about it now, please do check that the logic passes your sniff test

---

## Comment 2

> Username: mborland  
> Created_at: 2022-07-20 15:29:31 UTC  
> Url: https://github.com/boostorg/math/pull/805#issuecomment-1190431574  

This looks reasonable to me. Does it fix your issue upstream?

---

## Comment 3

> Username: mckib2  
> Created_at: 2022-07-20 16:30:15 UTC  
> Url: https://github.com/boostorg/math/pull/805#issuecomment-1190499053  

> This looks reasonable to me. Does it fix your issue upstream?  
  
Yes, it does.  Forgot to cross-reference the original SciPy issue where we encountered this overflow in our existing test suite: https://github.com/scipy/scipy/pull/15770

---

## Comment 4

> Username: mborland  
> Created_at: 2022-07-20 16:38:39 UTC  
> Url: https://github.com/boostorg/math/pull/805#issuecomment-1190507349  

> > This looks reasonable to me. Does it fix your issue upstream?  
>   
>   
>   
> Yes, it does.  Forgot to cross-reference the original SciPy issue where we encountered this overflow in our existing test suite: https://github.com/scipy/scipy/pull/15770  
  
Perfect. Merging.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2022-07-20 17:12:47 UTC  
> Url: https://github.com/boostorg/math/pull/805#issuecomment-1190540576  

Thanks everyone!

---
