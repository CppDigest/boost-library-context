# #1107 - Missing code line? [Open]

> Username: rp-47  
> Created at: 2024-03-01 13:52:04 UTC  
> Updated at: 2024-03-01 14:53:45 UTC  
> Url: https://github.com/boostorg/math/issues/1107  

Looking at the code line  
https://github.com/boostorg/math/blob/6103836593ee40700724465065d74bf92616f3fb/include/boost/math/tools/roots.hpp#L183  
and the block following it, I am thinking: Are you missining `fmax = fmid;` there?  
  
Also, are you sure that the condition  
https://github.com/boostorg/math/blob/6103836593ee40700724465065d74bf92616f3fb/include/boost/math/tools/roots.hpp#L319  
is generally valid and robust? I do get new exceptions because of that. If I ignore them, the root is found as before. (Unfortunately, my solved function can't be easily reproduced for your testing, sorry.)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-03-01 14:53:44 UTC  
> Url: https://github.com/boostorg/math/issues/1107#issuecomment-1973339861  

> and the block following it, I am thinking: Are you missining fmax = fmid; there?  
  
No... I think if anything the `fmin = fmid` in the following block is superfluous: the only thing fmin and fmax are used for within the inner loop of bisection is to check their sign to see which side of the root `mid` is.  
  
> Also, are you sure that the condition  
is generally valid and robust? I do get new exceptions because of that. If I ignore them, the root is found as before. (Unfortunately, my solved function can't be easily reproduced for your testing, sorry.)  
  
Generally, I think error handling here is a good thing - if the test fails then the two end points of the range *do not* bracket the root, so something has clearly gone wrong.  It would certainly be useful to have an example in hand to try and see what has gone wrong, I guess there may be some numerical instability in the function being inverted?
