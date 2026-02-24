# #9 Remove use of deprecated TR1 library. [Merged]

> Username: jzmaddock  
> Created at: 2014-09-27 12:14:47 UTC  
> Updated at: 2014-09-29 14:47:05 UTC  
> Merged at: 2014-09-29 14:47:05 UTC  
> Closed at: 2014-09-29 14:47:05 UTC  
> Url: https://github.com/boostorg/algorithm/pull/9  

The plan is to deprecate (and remove) Boost.TR1 now that C++11 is a reality, to that end this patch removes usage of Boost.TR1 and falls back to boost::unordered_map directly.

---

## Comment 1

> Username: mclow  
> Created_at: 2014-09-27 15:10:31 UTC  
> Url: https://github.com/boostorg/algorithm/pull/9#issuecomment-57055520  

LGTM.

---
