# #41 - [if_] Clarify the documentation [Closed]

> Username: ldionne  
> Created at: 2015-10-04 01:52:26 UTC  
> Updated at: 2015-10-20 16:48:27 UTC  
> Closed at: 2015-10-20 13:43:14 UTC  
> Url: https://github.com/boostorg/hof/issues/41  

The documentation of `if_` is not clear to me. It says  
  
> The if_ function decorator applies a boolen condition to the function.  
  
However, it seems to me like what it does is make the function callable only when the predicate is satisfied, or something like that. Also, there's a typo in the above quote (`boolen`).

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 04:39:33 UTC  
> Url: https://github.com/boostorg/hof/issues/41#issuecomment-145428629  

Ah yea, that is a much better description. Thanks.

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-20 16:48:27 UTC  
> Url: https://github.com/boostorg/hof/issues/41#issuecomment-149630071  

It would be great to close the issues by referencing the commit that fixed them
