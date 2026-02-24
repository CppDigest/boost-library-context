# #69 Don't use std::numeric_limits when is_specialized is false [Merged]

> Username: akrzemi1  
> Created at: 2015-05-26 21:04:34 UTC  
> Updated at: 2015-05-28 06:37:22 UTC  
> Merged at: 2015-05-28 06:37:22 UTC  
> Closed at: 2015-05-28 06:37:22 UTC  
> Url: https://github.com/boostorg/test/pull/69  

Prior to this change, in floating-point computations, in the case  
where we work for types where std::numeric_limits are not specialized,  
funcitons min() and max() from std::numeric_limits<FPT> are nonetheless  
instantiated and return FPT(0). While the result is never used in run-time,  
this may still trigger a compile-time failure.  
  
After this change, the paths for the cases where std::numeric_limits are not  
specialized are handled by separate specializations rather than run-time if  
statements.

---

## Comment 1

> Username: rogeeff  
> Created_at: 2015-05-26 23:43:36 UTC  
> Url: https://github.com/boostorg/test/pull/69#issuecomment-105698785  

Why don't we merge fpc_limits instead of having the came code in two  
places. Can fpc_op use version from other file.  
On May 26, 2015 5:04 PM, "Andrzej Krzemieński" notifications@github.com  
wrote:  
  
> Prior to this change, in floating-point computations, in the case  
> where we work for types where std::numeric_limits are not specialized,  
> funcitons min() and max() from std::numeric_limits are nonetheless  
> instantiated and return FPT(0). While the result is never used in run-time,  
> this may still trigger a compile-time failure.  
>   
> After this change, the paths for the cases where std::numeric_limits are  
> not  
> specialized are handled by separate specializations rather than run-time if  
>   
> ## statements.  
>   
> You can view, comment on, or merge this pull request online at:  
>   
>   https://github.com/boostorg/test/pull/69  
> Commit Summary  
> - Don't use std::numeric_limits when is_specialized is false  
>   
> File Changes  
> - _M_ include/boost/test/tools/floating_point_comparison.hpp  
>   https://github.com/boostorg/test/pull/69/files#diff-0 (44)  
> - _M_ include/boost/test/tools/fpc_op.hpp  
>   https://github.com/boostorg/test/pull/69/files#diff-1 (4)  
>   
> Patch Links:  
> - https://github.com/boostorg/test/pull/69.patch  
> - https://github.com/boostorg/test/pull/69.diff  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/69.

---

## Comment 2

> Username: akrzemi1  
> Created_at: 2015-05-27 06:51:10 UTC  
> Url: https://github.com/boostorg/test/pull/69#issuecomment-105779691  

> Why don't we merge fpc_limits instead of having the came code in two places. Can fpc_op use version from other file.  
  
I am confused. What should we merge `fpc_limits` with? I understand that the goal of `fpc_op.hpp` is to select the right comparison algorithm and to report failure in a UTF format. It does not use any information on limits, I cannot see any overlap in code between the two files.

---

## Comment 3

> Username: rogeeff  
> Created_at: 2015-05-28 06:36:47 UTC  
> Url: https://github.com/boostorg/test/pull/69#issuecomment-106196646  

sorry. Misread the diff

---
