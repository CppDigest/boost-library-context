# #30 Expand Travis CI for more coverage [Closed]

> Username: tonyelewis  
> Created at: 2018-10-28 05:12:19 UTC  
> Updated at: 2018-11-01 06:48:19 UTC  
> Closed at: 2018-10-29 20:12:44 UTC  
> Url: https://github.com/boostorg/rational/pull/30  

Some tests have failed in another branch and PR. It'd be good to be  
confirm whether those tests pass without that branch's changes.

---

## Comment 1

> Username: jeking3  
> Created_at: 2018-10-29 18:45:27 UTC  
> Url: https://github.com/boostorg/rational/pull/30#issuecomment-434032857  

The UBSAN issue looks like something in Boost.Test.  I have [reported it](https://github.com/boostorg/test/issues/174).

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-10-29 20:01:34 UTC  
> Url: https://github.com/boostorg/rational/pull/30#issuecomment-434057728  

Not much point in diagnosing the compiler error here; should disable the gcc 4.6 job in the .travis.yml file - that just leaves the UBSAN issue.

---

## Comment 3

> Username: tonyelewis  
> Created_at: 2018-10-29 20:12:44 UTC  
> Url: https://github.com/boostorg/rational/pull/30#issuecomment-434061469  

Closing this pull-request because it has now served its purpose of checking build failures.

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-10-29 20:22:12 UTC  
> Url: https://github.com/boostorg/rational/pull/30#issuecomment-434064622  

I'll finish the update on my end - thanks.

---
