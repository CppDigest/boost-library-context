# #31 update .travis.yml to correct lcov error messages [Merged]

> Username: sdarwin  
> Created at: 2020-12-31 01:07:14 UTC  
> Updated at: 2021-01-03 09:49:38 UTC  
> Merged at: 2021-01-03 09:49:29 UTC  
> Closed at: 2021-01-03 09:49:29 UTC  
> Url: https://github.com/boostorg/type_index/pull/31  

Travis-ci.org currently says "Please be aware travis-ci.org will be shutting down in several weeks, with all accounts migrating to travis-ci.com. Please stay tuned here for more information."     If migrating to a different CI solution, it will still be useful to base the new CI configuration off the old CI configuration, so fixing any errors can still be a good idea.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2021-01-02 14:03:45 UTC  
> Url: https://github.com/boostorg/type_index/pull/31#issuecomment-753477540  

Looks good. Can we always use the gcov-8 instead of a compiler matxhing version?

---

## Comment 2

> Username: sdarwin  
> Created_at: 2021-01-02 16:20:21 UTC  
> Url: https://github.com/boostorg/type_index/pull/31#issuecomment-753492826  

>  Can we always use the gcov-8   
  
no, it should probably be the matching version. The errors were from a mismatch.

---

## Comment 3

> Username: apolukhin  
> Created_at: 2021-01-03 09:49:38 UTC  
> Url: https://github.com/boostorg/type_index/pull/31#issuecomment-753592258  

Many thanks for the PR!

---
