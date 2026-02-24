# #236 Fix range back() function [Merged]

> Username: sdebionne  
> Created at: 2015-03-02 10:07:32 UTC  
> Updated at: 2015-03-06 12:50:22 UTC  
> Merged at: 2015-03-02 13:15:26 UTC  
> Closed at: 2015-03-02 13:15:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/236  

`*(--boost::end(rng));` is not valid for range with raw pointer as iterator (the compiler complains that `'--' needs l-value`. Since the range is supposed to be BidirectionalRange, use `*rbegin()` instead. See [PR#235](https://github.com/boostorg/geometry/pull/235) for the full description and discussion.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-03-02 10:29:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/236#issuecomment-76688938  

I am okay with merging.  
Thanks a lot for the fix.

---
