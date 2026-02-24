# #32 - Improve inclusive/exclusive_scan() performance [Open]

> Username: kylelutz  
> Created at: 2013-12-21 19:37:03 UTC  
> Updated at: 2017-03-21 04:14:47 UTC  
> Url: https://github.com/boostorg/compute/issues/32  

Improve the performance of `inclusive_scan()` and `exclusive_scan()`. Scan multiple values per thread. This will also improve the performance of `partial_sum()`.  
##

---

## Comment 1

> Username: banche  
> Created at: 2014-05-19 03:41:57 UTC  
> Url: https://github.com/boostorg/compute/issues/32#issuecomment-43463982  

I will give a look at this week, as far as I remember a quite efficient scan can be done in 3 distinct steps : Up-Sweep, Down-Sweep and then compute the final result ( or something like that).  
  
Hope I can improve the performances!
